---
tags:
  - type/howto
  - status/evergreen
description: 本项目来讲讲 **DailyUse** 的 desktop 端是如何实现的。
created: 2026-01-11T19:55:38
updated: 2026-01-11T21:45:26
---

# Dailyuse-desktop项目实现 

首先，dailyuse 项目是 monorepo + nx 项目，采用 package 拼装成 项目的思想， desktop 只是一个容器。这是本人的一个实践尝试。
为了探索 “拼装” 时的具体细节。

项目的基础架构，以创建目标为例：
1. 有一个创建目标的按钮，点击后弹出表单，并初始化默认信息。（客户端 presentation）
2. 用户输入后，对表单信息前端校验。（客户端 presentation）
3. 用户点击保存后，通过 hook 调用 application （客户端 application）
4. application 调用 ipc （客户端 infrastructure）
5. ipc 发送数据到 主进程 ipc （服务端 infrastructure）
6. 主进程 ipc 调用 服务端 application （服务端 application）
7. 服务端 application 调用 goal 实体的方法进行校验并创建，可能要查数据看数据是否冲突。 （服务端 domain、服务端 infrastructure）
8. 然后持久化保存 goal （服务端 infrastructure）

## 数据库

### desktop 中的具体实现

`better-sqlite3` + SQLite 本地数据库
采用按模块拆分的方式组织 数据库代码

```
apps/desktop/src/main/database/
├── index.ts                      # 核心：连接管理、性能优化
├── schema/
│   ├── index.ts                 # 统一导出所有 schema
│   ├── account.schema.ts        # Account + Auth 表
│   ├── goal.schema.ts           # Goal 模块表
│   ├── task.schema.ts           # Task 模块表  
│   ├── schedule.schema.ts       # Schedule 模块表
│   ├── reminder.schema.ts       # Reminder 模块表
│   ├── ai.schema.ts             # AI 模块表
│   ├── notification.schema.ts   # Notification 模块表
│   ├── dashboard.schema.ts      # Dashboard 模块表
│   ├── repository.schema.ts     # Repository 模块表
│   ├── setting.schema.ts        # Setting 模块表
│   └── sync.schema.ts           # Sync 基础设施表
```

## application-server 包

里面主要是各个模块的服务端应用层的代码，接下来以 create-goal.ts 作为实例来记录。

1. 服务端应用层是以用例的形式组织代码
2. 每个用例应该是需要 “domain 包中的领域服务还有实体对象”、“infrastructure 包中的 container”作为依赖

```ts
/**
 * Create Goal Service
 *
 * 创建新目标的应用服务
 */

import type { IGoalRepository } from '@dailyuse/domain-server/goal';
import { GoalDomainService, Goal } from '@dailyuse/domain-server/goal';
import type { CreateGoalRequest, GoalResponse } from '@dailyuse/contracts/goal';
import { eventBus } from '@dailyuse/utils';
import { GoalContainer } from '@dailyuse/infrastructure-server';

/**
 * Create Goal Service
 */
export class CreateGoal {
  private static instance: CreateGoal;
  private readonly domainService: GoalDomainService;

  private constructor(private readonly goalRepository: IGoalRepository) {
    this.domainService = new GoalDomainService();
  }

  /**
   * 创建服务实例（支持依赖注入）
   */
  static createInstance(goalRepository?: IGoalRepository): CreateGoal {
    const container = GoalContainer.getInstance();
    const repo = goalRepository || container.getGoalRepository();
    CreateGoal.instance = new CreateGoal(repo);
    return CreateGoal.instance;
  }

  /**
   * 获取服务单例
   */
  static getInstance(): CreateGoal {
    if (!CreateGoal.instance) {
      CreateGoal.instance = CreateGoal.createInstance();
    }
    return CreateGoal.instance;
  }

  /**
   * 重置实例（用于测试）
   */
  static resetInstance(): void {
    CreateGoal.instance = undefined as unknown as CreateGoal;
  }

  async execute(accountUuid: string, input: CreateGoalRequest): Promise<GoalResponse> {
    // 1. 验证输入
    this.validateInput(accountUuid, input);

    // 2. 如果有父目标，先查询
    let parentGoal: Goal | undefined;
    if (input.parentGoalUuid) {
      const found = await this.goalRepository.findById(input.parentGoalUuid);
      if (!found) {
        throw new Error(`Parent goal not found: ${input.parentGoalUuid}`);
      }
      parentGoal = found;
    }

    // 3. 委托领域服务创建聚合根
    const goal = this.domainService.createGoal({ accountUuid, ...input }, parentGoal);

    // 4. 如果有 keyResults，添加到目标中
    // if (input.keyResults && input.keyResults.length > 0) {
    //   for (const krParams of input.keyResults) {
    //     this.domainService.addKeyResultToGoal(goal, {
    //       title: krParams.title,
    //       description: krParams.description,
    //       valueType: krParams.valueType || 'INCREMENTAL',
    //       targetValue: krParams.targetValue ?? 100,
    //       unit: krParams.unit,
    //       weight: krParams.weight ?? 5,
    //     });
    //   }
    // }

    // 5. 持久化
    await this.goalRepository.save(goal);

    // 6. 发布领域事件
    await this.publishEvents(goal);

    // 7. 返回结果
    return {
      goal: goal.toClientDTO(true),
    };
  }

  private validateInput(accountUuid: string, input: CreateGoalRequest): void {
    if (!input.title?.trim()) {
      throw new Error('Title is required');
    }
    if (!accountUuid?.trim()) {
      throw new Error('Account UUID is required');
    }
  }

  private async publishEvents(goal: Goal): Promise<void> {
    const events = goal.getUncommittedDomainEvents();
    for (const event of events) {
      await eventBus.emit(event.eventType, event);
    }
  }
}

/**
 * 便捷函数：创建目标
 */
export const createGoal = (accountUuid: string, input: CreateGoalRequest): Promise<GoalResponse> =>
  CreateGoal.getInstance().execute(accountUuid, input);
```

## application-client 包

- 客户端应用层用于调用 接口，所以支持 接口 的依赖注入
- 负责把获取到的数据进行处理，转为实体对象。需要依赖 domain-client

## infrastructure-server

container， 每个模块的仓储容器

```ts
/**
 * Goal Container (Server)
 *
 * 依赖注入容器，管理 Goal 模块的 repository 实例
 */

import type { IGoalRepository, IGoalStatisticsRepository, IGoalFolderRepository } from '@dailyuse/domain-server/goal';

/**
 * Goal 模块依赖注入容器
 */
export class GoalContainer {
  private static instance: GoalContainer;
  private goalRepository: IGoalRepository | null = null;
  private statisticsRepository: IGoalStatisticsRepository | null = null;
  private goalFolderRepository: IGoalFolderRepository | null = null;

  private constructor() {}

  /**
   * 获取容器单例
   */
  static getInstance(): GoalContainer {
    if (!GoalContainer.instance) {
      GoalContainer.instance = new GoalContainer();
    }
    return GoalContainer.instance;
  }

  /**
   * 重置容器（用于测试）
   */
  static resetInstance(): void {
    GoalContainer.instance = new GoalContainer();
  }

  /**
   * 注册 GoalRepository
   */
  registerGoalRepository(repository: IGoalRepository): this {
    this.goalRepository = repository;
    return this;
  }

  /**
   * 注册 GoalStatisticsRepository
   */
  registerStatisticsRepository(repository: IGoalStatisticsRepository): this {
    this.statisticsRepository = repository;
    return this;
  }

  /**
   * 注册 GoalFolderRepository
   */
  registerGoalFolderRepository(repository: IGoalFolderRepository): this {
    this.goalFolderRepository = repository;
    return this;
  }

  /**
   * 获取 GoalRepository
   */
  getGoalRepository(): IGoalRepository {
    if (!this.goalRepository) {
      throw new Error('GoalRepository not registered. Call registerGoalRepository first.');
    }
    return this.goalRepository;
  }

  /**
   * 获取 GoalStatisticsRepository
   */
  getStatisticsRepository(): IGoalStatisticsRepository {
    if (!this.statisticsRepository) {
      throw new Error('GoalStatisticsRepository not registered. Call registerStatisticsRepository first.');
    }
    return this.statisticsRepository;
  }

  /**
   * 获取 GoalFolderRepository
   */
  getGoalFolderRepository(): IGoalFolderRepository {
    if (!this.goalFolderRepository) {
      throw new Error('GoalFolderRepository not registered. Call registerGoalFolderRepository first.');
    }
    return this.goalFolderRepository;
  }

  /**
   * 检查是否已配置
   */
  isConfigured(): boolean {
    return this.goalRepository !== null;
  }

  /**
   * 清空所有注册的依赖
   */
  clear(): void {
    this.goalRepository = null;
    this.statisticsRepository = null;
    this.goalFolderRepository = null;
  }
}

```

## infrastructure-client

接口（注册管理） 和 仓储