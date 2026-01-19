---
tags:
  - tech/ops/cloud
  - type/howto
  - status/growing
description: github-codespace的内存清理
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[VPS MOC]] | [[DevOps MOC]]

---


# github-codespace的占用清理 

## 问题详情

使用 codespace 时总是会因为资源占用过多导致连接不上，重连后之前的进程应该还是一直存在。

## 解决方案


Ran terminal command: ps aux --sort=-%mem | head -20

我看到了问题!有**多个重复的 extensionHost 和 tsserver 进程**在运行。最严重的是:

1. **3个 extensionHost** 进程(PID 168310, 78511, 153312) - 每个占用 1.5GB 内存
2. **6个 tsserver** 进程 - 其中两个占用近 2GB 内存
3. **3个 Nx language server** 进程(nxls)

这说明你可能打开了多个 VSCode 窗口或者窗口没有正确关闭。让我检查并清理: 

Ran terminal command: ps aux | grep node | grep -v grep | wc -l

天哪!**46个 Node 进程**!让我清理旧的进程。我会保留最新的进程(PID 168xxx),杀掉旧的: 

Ran terminal command: kill -9 78511 153312

Ran terminal command: kill -9 79066 153652

Ran terminal command: kill -9 79096 79098 153672 168602 2>/dev/null || true

现在检查还有哪些 pnpm/nx 的后台进程: 

Ran terminal command: ps aux | grep -E "pnpm|nx " | grep -v grep

现在检查内存使用情况: 

Ran terminal command: free -hl

🎉 **太好了!内存已经从 12Gi 降到 7.3Gi,释放了近 5GB 内存!可用内存从 3.0Gi 增加到 8.4Gi!**

现在让我检查 CPU 使用情况: 

Ran terminal command: ps aux --sort=-%cpu | head -15

还有一个旧的 fileWatcher 进程(PID 153323)。让我清理它: 

Ran terminal command: kill -9 153323

现在让我统计一下当前进程数量: 

Ran terminal command: ps aux | grep node | grep -v grep | wc -l

很好!**从 46 个进程减少到 16 个**,清理了 30 个进程! 

现在让我们清理 Nx 的缓存来进一步释放内存: 

Ran terminal command: cd /workspaces/DailyUse && pnpm exec nx reset

完美!现在让我们再次检查内存: 

Ran terminal command: free -hl && echo -e "\n进程数量:" && ps aux | grep node | grep -v grep | wc -l

🎉 **清理完成!总结:**

| 指标 | 清理前 | 清理后 | 改善 |
|------|--------|--------|------|
| **内存使用** | 12 Gi | 7.1 Gi | ✅ **释放 4.9 GB** |
| **可用内存** | 3.0 Gi | 8.5 Gi | ✅ **增加 5.5 GB** |
| **Node进程数** | 46 个 | 16 个 | ✅ **减少 30 个进程** |

**已清理的进程:**
- ✅ 2 个旧的 extensionHost (各占 1.5GB)
- ✅ 多个 Nx language server 进程
- ✅ 多个 Nx plugin worker 进程
- ✅ 清理了 Nx 缓存和守护进程

现在你可以手动启动 API 服务了,内存和 CPU 资源已经充足! 💪

**启动命令:**

```bash
pnpm nx dev api
# 或者
pnpm nx serve api
```

## 实战经验

## 经验总结

## 信息参考
