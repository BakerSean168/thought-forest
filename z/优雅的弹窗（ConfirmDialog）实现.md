---
tags:
  - tech/dev/frame
  - type/snippet
  - status/evergreen
description: 优雅的弹窗（ConfirmDialog）实现
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:36
---

> [!info] **上级索引**
> [[Vue3 MOC]] | [[前端基础 MOC]]

---


# 优雅地确认框实现 

## 基础概念

前端的确认弹窗主要为用于 **删除** 或者 **重要操作的确认**，主要为 **取消** 和 **确认** 操作选项。
所以可以直接提取出主要内容：
- 提醒内容
- 确认按钮、取消按钮

然后封装一个 ComfirmDialog 组件

## 实战经验


```vue
<template>
  <div>
    <!-- Snackbar 消息提示 -->
    <v-snackbar
      v-model="snackbar.visible"
      :color="snackbar.color"
      :timeout="snackbar.timeout"
      location="top"
      multi-line
    >
      {{ snackbar.message }}
      <template #actions>
        <v-btn variant="text" @click="closeSnackbar"> 关闭 </v-btn>
      </template>
    </v-snackbar>

    <!-- 确认对话框 -->
    <v-dialog v-model="dialog.visible" max-width="400" persistent>
      <v-card>
        <v-card-title class="d-flex align-center">
          <v-icon
            :icon="getDialogIcon(dialog.type)"
            :color="getDialogColor(dialog.type)"
            class="mr-2"
          />
          {{ dialog.title }}
        </v-card-title>

        <v-card-text>
          {{ dialog.message }}
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn variant="text" @click="handleDialogCancel"> 取消 </v-btn>
          <v-btn :color="getDialogColor(dialog.type)" variant="flat" @click="handleDialogConfirm">
            确定
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script setup lang="ts">
import { toRefs } from 'vue';
import { getGlobalMessage, type MessageType } from '../../composables/useMessage';

// 🔥 使用全局单例，确保所有地方调用的是同一个实例
const { snackbar, dialog, closeSnackbar, handleDialogConfirm, handleDialogCancel } = getGlobalMessage();

/**
 * 获取对话框图标
 */
const getDialogIcon = (type: MessageType): string => {
  const icons = {
    success: 'mdi-check-circle',
    error: 'mdi-alert-circle',
    warning: 'mdi-alert',
    info: 'mdi-information',
  };
  return icons[type];
};

/**
 * 获取对话框颜色
 */
const getDialogColor = (type: MessageType): string => {
  const colors = {
    success: 'success',
    error: 'error',
    warning: 'warning',
    info: 'info',
  };
  return colors[type];
};
</script>

<style scoped>
.v-card-title {
  font-size: 1.25rem;
  font-weight: 500;
}
</style>

```

```ts
/**
 * Vuetify 消息提示封装
 * @module useMessage
 * @description 提供统一的消息提示、确认框、对话框等 UI 交互功能
 */

import { ref, type Ref } from 'vue';

/**
 * 消息类型
 */
export type MessageType = 'success' | 'info' | 'warning' | 'error';

/**
 * 消息选项
 */
export interface MessageOptions {
  title?: string;
  message: string;
  type?: MessageType;
  duration?: number;
  showClose?: boolean;
}

/**
 * 确认框选项
 */
export interface ConfirmOptions {
  title?: string;
  message: string;
  type?: MessageType;
  confirmText?: string;
  cancelText?: string;
  persistent?: boolean;
  width?: string | number;
}

/**
 * Snackbar 状态
 */
interface SnackbarState {
  visible: boolean;
  message: string;
  color: string;
  timeout: number;
}

/**
 * Dialog 状态
 */
interface DialogState {
  visible: boolean;
  title: string;
  message: string;
  type: MessageType;
  resolve: ((value: boolean) => void) | null;
}

/**
 * Vuetify 消息提示 Composable
 * @description 提供优雅的消息提示功能
 *
 * @example
 * ```typescript
 * const message = useMessage()
 *
 * // 基础用法
 * message.success('操作成功')
 * message.error('操作失败')
 * message.warning('请注意')
 * message.info('提示信息')
 *
 * // 确认框
 * const confirmed = await message.confirm({
 *   title: '确认删除',
 *   message: '确定要删除这条记录吗？',
 *   type: 'warning'
 * })
 *
 * if (confirmed) {
 *   await deleteRecord()
 * }
 *
 * // 删除确认（快捷方式）
 * await message.delConfirm('确定要删除吗？')
 * ```
 */
export function useMessage() {
  // Snackbar 状态
  const snackbar = ref<SnackbarState>({
    visible: false,
    message: '',
    color: 'success',
    timeout: 3000,
  });

  // Dialog 状态
  const dialog = ref<DialogState>({
    visible: false,
    title: '',
    message: '',
    type: 'info',
    resolve: null,
  });

  /**
   * 显示 Snackbar
   */
  const showSnackbar = (message: string, color: string, timeout = 3000) => {
    snackbar.value = {
      visible: true,
      message,
      color,
      timeout,
    };
  };

  /**
   * 成功提示
   * @param message - 提示内容
   * @param duration - 显示时长（毫秒）
   */
  const success = (message: string, duration = 3000) => {
    showSnackbar(message, 'success', duration);
  };

  /**
   * 错误提示
   * @param message - 提示内容
   * @param duration - 显示时长（毫秒）
   */
  const error = (message: string, duration = 4000) => {
    showSnackbar(message, 'error', duration);
  };

  /**
   * 警告提示
   * @param message - 提示内容
   * @param duration - 显示时长（毫秒）
   */
  const warning = (message: string, duration = 3500) => {
    showSnackbar(message, 'warning', duration);
  };

  /**
   * 信息提示
   * @param message - 提示内容
   * @param duration - 显示时长（毫秒）
   */
  const info = (message: string, duration = 3000) => {
    showSnackbar(message, 'info', duration);
  };

  /**
   * 通用确认框
   * @param options - 确认框选项
   * @returns Promise<boolean> - 用户选择结果
   *
   * @example
   * ```typescript
   * const confirmed = await message.confirm({
   *   title: '确认操作',
   *   message: '确定要执行此操作吗？',
   *   type: 'warning',
   *   confirmText: '确定',
   *   cancelText: '取消'
   * })
   * ```
   */
  const confirm = (options: ConfirmOptions): Promise<boolean> => {
    return new Promise((resolve) => {
      dialog.value = {
        visible: true,
        title: options.title || '提示',
        message: options.message,
        type: options.type || 'info',
        resolve,
      };
    });
  };

  /**
   * 删除确认框（快捷方式）
   * @param message - 确认内容
   * @param title - 标题
   * @returns Promise<boolean>
   *
   * @example
   * ```typescript
   * try {
   *   await message.delConfirm('确定要删除这条记录吗？')
   *   // 用户点击确认
   *   await deleteApi(id)
   *   message.success('删除成功')
   * } catch {
   *   // 用户点击取消或关闭
   *   console.log('取消删除')
   * }
   * ```
   */
  const delConfirm = (message?: string, title?: string): Promise<boolean> => {
    return confirm({
      title: title || '确认删除',
      message: message || '确定要删除这条记录吗？删除后无法恢复。',
      type: 'warning',
      confirmText: '确定删除',
      cancelText: '取消',
    });
  };

  /**
   * 保存确认框
   * @param message - 确认内容
   * @param title - 标题
   * @returns Promise<boolean>
   */
  const saveConfirm = (message?: string, title?: string): Promise<boolean> => {
    return confirm({
      title: title || '确认保存',
      message: message || '确定要保存当前修改吗？',
      type: 'info',
      confirmText: '保存',
      cancelText: '取消',
    });
  };

  /**
   * 离开确认框（用于未保存提示）
   * @param message - 确认内容
   * @returns Promise<boolean>
   */
  const leaveConfirm = (message?: string): Promise<boolean> => {
    return confirm({
      title: '离开页面',
      message: message || '你有未保存的修改，确定要离开吗？',
      type: 'warning',
      confirmText: '离开',
      cancelText: '继续编辑',
    });
  };

  /**
   * 关闭 Snackbar
   */
  const closeSnackbar = () => {
    snackbar.value.visible = false;
  };

  /**
   * 处理 Dialog 确认
   */
  const handleDialogConfirm = () => {
    if (dialog.value.resolve) {
      dialog.value.resolve(true);
    }
    dialog.value.visible = false;
    dialog.value.resolve = null;
  };

  /**
   * 处理 Dialog 取消
   */
  const handleDialogCancel = () => {
    if (dialog.value.resolve) {
      dialog.value.resolve(false);
    }
    dialog.value.visible = false;
    dialog.value.resolve = null;
  };

  return {
    // 状态
    snackbar,
    dialog,

    // 提示方法
    success,
    error,
    warning,
    info,

    // 确认框方法
    confirm,
    delConfirm,
    saveConfirm,
    leaveConfirm,

    // 控制方法
    closeSnackbar,
    handleDialogConfirm,
    handleDialogCancel,
  };
}

/**
 * 全局消息实例（单例模式）
 */
let globalMessageInstance: ReturnType<typeof useMessage> | null = null;

/**
 * 获取全局消息实例
 * @description 提供全局单例，可在任何地方使用
 *
 * @example
 * ```typescript
 * import { getGlobalMessage } from '@dailyuse/ui'
 *
 * const message = getGlobalMessage()
 * message.success('操作成功')
 * ```
 */
export function getGlobalMessage(): ReturnType<typeof useMessage> {
  if (!globalMessageInstance) {
    globalMessageInstance = useMessage();
  }
  return globalMessageInstance;
}

/**
 * 消息提示类型定义（用于组件）
 */
export type MessageInstance = ReturnType<typeof useMessage>;

```

## 使用指南

把 组件 在 app.vue 中导入，然后就可以在需要用的地方导入 composables 文件就可以直接使用了

```typescript
// ✅ 导入 useMessage
// @ts-ignore - @dailyuse/ui type declarations not generated yet
import { useMessage } from '@dailyuse/ui';

const message = useMessage();

// ✅ 删除 KeyResult - 使用优雅的确认对话框
const handleDeleteKeyResult = async () => {
  try {
    // 使用 useMessage 的 delConfirm 获取用户确认
    const confirmed = await message.delConfirm(
      '此操作将同时删除所有关联的进度记录，无法撤销。',
      '删除关键结果'
    );

    if (!confirmed) {
      return;
    }

    // 用户确认删除
    await deleteKeyResultForGoal(props.keyResult.goalUuid, props.keyResult.uuid);
  } catch (error) {
    console.error('删除关键结果失败:', error);
    message.error('删除关键结果失败');
  }
};
```

## 经验总结

[[vue全局对话框没反应问题]]

## 信息参考

