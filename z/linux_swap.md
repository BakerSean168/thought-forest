---
tags:
  - tech/ops/linux
  - type/howto
  - status/growing
description: linux_swap
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Linux MOC]] | [[后端开发 MOC]]

---


# linux_swap 

在欧拉（EulerOS）系统中配置虚拟内存（Swap）时，需根据物理内存大小和业务需求合理规划。对于 **4vCPU + 8GB 物理内存** 的配置，以下是详细的虚拟内存设置方案：

### 一、虚拟内存大小建议

根据行业经验和欧拉系统最佳实践，虚拟内存（Swap）推荐设置为 **8-16GB**，具体规则如下：

- **8GB 物理内存**：Swap 大小建议为 **8-16GB**（物理内存的 1-2 倍）。
- **特殊场景**：
	- 若运行内存敏感型应用（如数据库），建议设置为 **16GB**。
	- 若系统需支持休眠（Hibernate）功能，Swap 大小需等于物理内存（即 8GB）。

### 二、配置方法（推荐使用 Swap 文件）

#### 1. 检查当前 Swap 状态


```bash
# 查看Swap使用情况
free -h
swapon -s  # 显示Swap设备/文件
```

#### 2. 创建 Swap 文件（以 8GB 为例）


```bash
# 创建8GB的Swap文件（使用fallocate命令更高效）
sudo fallocate -l 8G /swapfile

# 若fallocate不可用，使用dd命令（较慢）
sudo dd if=/dev/zero of=/swapfile bs=1G count=8
```

#### 3. 配置权限并格式化


```bash
sudo chmod 600 /swapfile  # 确保只有root可读写
sudo mkswap /swapfile     # 格式化为Swap文件
```

#### 4. 启用 Swap 文件


```bash
sudo swapon /swapfile     # 临时启用
```

#### 5. 永久生效（修改 fstab）


```bash
echo '/swapfile none swap defaults 0 0' | sudo tee -a /etc/fstab
```

### 三、优化配置（可选）

#### 1. 调整 Swap 使用优先级（可选）

```bash
# 设置Swap文件优先级为10（数值越高优先级越高）
sudo swapon -p 10 /swapfile
```

#### 2. 降低 Swap 使用频率（推荐）

通过修改 `swappiness` 参数减少内存数据交换到磁盘的频率：


```bash
# 临时调整（立即生效）
echo 10 | sudo tee /proc/sys/vm/swappiness

# 永久生效（编辑配置文件）
sudo sed -i '$a vm.swappiness=10' /etc/sysctl.conf
sudo sysctl -p  # 使配置生效
```

#### 3. 启用内存压缩（Zswap，提升性能）


```bash
# 临时启用Zswap
echo 1 | sudo tee /sys/module/zswap/parameters/enabled

# 永久生效（编辑配置文件）
sudo sed -i '$a options zswap.enabled=1' /etc/modprobe.d/zswap.conf
sudo depmod -a
```

### 四、验证配置


```bash
# 检查Swap文件是否生效
swapon -s
free -h

# 查看Swap优先级和使用情况
cat /proc/swaps
```

### 五、注意事项

1. **磁盘空间**：确保 `/` 分区或目标分区有足够空间创建 Swap 文件。
2. **文件系统兼容性**：
	- 若使用 **btrfs** 文件系统，需额外配置子卷并调整内核参数。
	- 避免在 **ext4** 分区的 `/` 目录下创建过大的 Swap 文件，可能影响系统性能。
3. **生产环境**：
	- 建议通过 **分区** 而非文件方式配置 Swap（更稳定），操作步骤参考。
	- 调整 Swap 前备份重要数据，尤其是涉及分区操作时。

### 六、配置示例（完整脚本）


```bash
# 创建并启用8GB Swap文件
sudo fallocate -l 8G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
echo '/swapfile none swap defaults 0 0' | sudo tee -a /etc/fstab

# 优化Swap使用策略
echo 10 | sudo tee /proc/sys/vm/swappiness
sudo sed -i '$a vm.swappiness=10' /etc/sysctl.conf
sudo sysctl -p
```

执行以上步骤后，欧拉系统将拥有 8GB 虚拟内存，结合优化参数可提升内存管理效率。
