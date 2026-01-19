---
tags: [type/howto, status/evergreen]
description: 如何xxx
created: 2026-01-02T21:10:26
updated: 2026-01-02T21:10:54
---

# mqtt源码编译 

既然官方二进制包（Binary Package）在某些 MinGW 版本下缺失，**从官方源码仓库拉取并手动编译安装**就是最彻底、最专业的解决办法。这不仅能解决版本不匹配的问题，还能让你深度掌握 Qt 模块的构成。

针对你的 **Qt 6.8.0 + MinGW 13.1.0** 环境，以下是详细的实操流程：

---

## 1. 准备工作

在开始之前，请确保你的开发环境已具备以下工具：

* **Git**: 用于克隆仓库。
* **CMake**: Qt 6 模块的首选构建系统。
* **Ninja** (推荐): 比 `mingw32-make` 更快的构建工具（可通过 `aqt` 或 `scoop` 安装）。

---

## 2. 第一步：从官方仓库拉取源码

Qt MQTT 是一个独立的附加模块，托管在 Qt 的官方 GitHub 镜像或官方 Gerrit 服务器上。

```powershell
# 进入你的工作目录（例如 D:\home\projects）
cd D:\home\projects

# 克隆仓库
git clone https://github.com/qt/qtmqtt.git

# 进入目录并切换到与你 Qt 库一致的分支（非常重要！）
cd qtmqtt
git checkout 6.8.0

```

---

## 3. 第二步：配置编译环境变量

为了让编译过程顺利找到你的 Qt 核心库和编译器，我们需要在当前终端临时注入路径：

```powershell
# 1. 加入编译器路径
$env:PATH = "D:\opt\Qt\Tools\mingw1310_64\bin;" + $env:PATH

# 2. 加入 Qt 核心库路径（包含 qmake 和 cmake 配置文件）
$env:PATH = "D:\opt\Qt\6.8.0\mingw_64\bin;" + $env:PATH

# 3. 验证是否生效
gcc --version   # 应显示 13.1.0
cmake --version # 应能正常运行

```

---

## 4. 第三步：使用 CMake 进行配置与编译

在 Qt 6 中，模块编译统一推荐使用 **CMake**。

### 1. 创建构建目录

```powershell
mkdir build
cd build

```

### 2. 执行配置 (Configure)

我们需要通过 `CMAKE_INSTALL_PREFIX` 指定安装路径，使其直接安装到你现有的 Qt 文件夹中。

```powershell
cmake -G "Ninja" `
  -DCMAKE_MESSAGE_LOG_LEVEL=STATUS `
  -DCMAKE_PREFIX_PATH="D:/opt/Qt/6.8.0/mingw_64" `
  -DCMAKE_INSTALL_PREFIX="D:/opt/Qt/6.8.0/mingw_64" `
  ..

```

> **提示**：如果你没有安装 Ninja，可以将 `-G "Ninja"` 换成 `-G "MinGW Makefiles"`。

### 3. 执行构建 (Build)

```powershell
cmake --build .

```

### 4. 执行安装 (Install)

这一步会将生成的 `.a` 静态库、`.dll` 动态库和头文件自动拷贝到 `D:\opt\Qt\6.8.0\mingw_64` 的对应子文件夹中。

```powershell
cmake --install .

```

---

## 5. 第四步：验证安装结果

安装完成后，你可以通过以下路径检查文件是否就位：

* **头文件**: `D:\opt\Qt\6.8.0\mingw_64\include\QtMqtt`
* **静态库**: `D:\opt\Qt\6.8.0\mingw_64\lib\libQt6Mqtt.a`
* **动态库**: `D:\opt\Qt\6.8.0\mingw_64\bin\Qt6Mqtt.dll`
* **CMake 配置**: `D:\opt\Qt\6.8.0\mingw_64\lib\cmake\Qt6Mqtt`

---

## 6. 第五步：在项目中使用

由于你已经将模块安装到了 Qt 的标准路径下，现在你的项目可以像使用原生模块一样调用它了。

### 如果使用 `.pro` (qmake):

```pro
QT += mqtt

```

### 如果使用 `CMakeLists.txt` (CMake):

```cmake
find_package(Qt6 REQUIRED COMPONENTS Mqtt)
target_link_libraries(mytarget PRIVATE Qt6::Mqtt)

```

---

## 7. 常见坑点排查

* **版本不匹配**: 如果你拉取的 `qtmqtt` 源码是 `6.5` 而 Qt 库是 `6.8`，编译时会报大量的宏定义错误。务必确保 `git checkout` 的版本号一致。
* **权限问题**: 如果你将 Qt 安装在 `C:\Program Files` 等系统目录，执行 `cmake --install` 时需要管理员权限运行终端。由于你安装在 `D:\opt`，通常不会有此问题。
* **残留缓存**: 如果编译失败后修改了配置，记得删除 `build` 文件夹下的所有文件再重新开始。

---

**下一步建议：**
既然 MQTT 模块已经可以从源码级掌控了，**你需要我帮你写一段基于 `QMqttClient` 的最小化连接代码，用来测试你的智能管家能否成功连接到公共 MQTT 服务器（如 HiveMQ）吗？**
既然官方二进制包（Binary Package）在某些 MinGW 版本下缺失，**从官方源码仓库拉取并手动编译安装**就是最彻底、最专业的解决办法。这不仅能解决版本不匹配的问题，还能让你深度掌握 Qt 模块的构成。

针对你的 **Qt 6.8.0 + MinGW 13.1.0** 环境，以下是详细的实操流程：

---

## 1. 准备工作

在开始之前，请确保你的开发环境已具备以下工具：

- **Git**: 用于克隆仓库。
- **CMake**: Qt 6 模块的首选构建系统。
- **Ninja** (推荐): 比 `mingw32-make` 更快的构建工具（可通过 `aqt` 或 `scoop` 安装）。

---

## 2. 第一步：从官方仓库拉取源码

Qt MQTT 是一个独立的附加模块，托管在 Qt 的官方 GitHub 镜像或官方 Gerrit 服务器上。

PowerShell

```
# 进入你的工作目录（例如 D:\home\projects）
cd D:\home\projects

# 克隆仓库
git clone https://github.com/qt/qtmqtt.git

# 进入目录并切换到与你 Qt 库一致的分支（非常重要！）
cd qtmqtt
git checkout 6.8.0
```

---

## 3. 第二步：配置编译环境变量

为了让编译过程顺利找到你的 Qt 核心库和编译器，我们需要在当前终端临时注入路径：

PowerShell

```
# 1. 加入编译器路径
$env:PATH = "D:\opt\Qt\Tools\mingw1310_64\bin;" + $env:PATH

# 2. 加入 Qt 核心库路径（包含 qmake 和 cmake 配置文件）
$env:PATH = "D:\opt\Qt\6.8.0\mingw_64\bin;" + $env:PATH

# 3. 验证是否生效
gcc --version   # 应显示 13.1.0
cmake --version # 应能正常运行
```

---

## 4. 第三步：使用 CMake 进行配置与编译

在 Qt 6 中，模块编译统一推荐使用 **CMake**。

### 1. 创建构建目录

PowerShell

```
mkdir build
cd build
```

### 2. 执行配置 (Configure)

我们需要通过 `CMAKE_INSTALL_PREFIX` 指定安装路径，使其直接安装到你现有的 Qt 文件夹中。

PowerShell

```
cmake -G "Ninja" `
  -DCMAKE_MESSAGE_LOG_LEVEL=STATUS `
  -DCMAKE_PREFIX_PATH="D:/opt/Qt/6.8.0/mingw_64" `
  -DCMAKE_INSTALL_PREFIX="D:/opt/Qt/6.8.0/mingw_64" `
  ..
```

> **提示**：如果你没有安装 Ninja，可以将 `-G "Ninja"` 换成 `-G "MinGW Makefiles"`。

### 3. 执行构建 (Build)

PowerShell

```
cmake --build .
```

### 4. 执行安装 (Install)

这一步会将生成的 `.a` 静态库、`.dll` 动态库和头文件自动拷贝到 `D:\opt\Qt\6.8.0\mingw_64` 的对应子文件夹中。

PowerShell

```
cmake --install .
```

---

## 5. 第四步：验证安装结果

安装完成后，你可以通过以下路径检查文件是否就位：

- **头文件**: `D:\opt\Qt\6.8.0\mingw_64\include\QtMqtt`
- **静态库**: `D:\opt\Qt\6.8.0\mingw_64\lib\libQt6Mqtt.a`
- **动态库**: `D:\opt\Qt\6.8.0\mingw_64\bin\Qt6Mqtt.dll`
- **CMake 配置**: `D:\opt\Qt\6.8.0\mingw_64\lib\cmake\Qt6Mqtt`

---

## 6. 第五步：在项目中使用

由于你已经将模块安装到了 Qt 的标准路径下，现在你的项目可以像使用原生模块一样调用它了。

### 如果使用 `.pro` (qmake):

代码段

```
QT += mqtt
```

### 如果使用 `CMakeLists.txt` (CMake):

CMake

```
find_package(Qt6 REQUIRED COMPONENTS Mqtt)
target_link_libraries(mytarget PRIVATE Qt6::Mqtt)
```

---

## 7. 常见坑点排查

- **版本不匹配**: 如果你拉取的 `qtmqtt` 源码是 `6.5` 而 Qt 库是 `6.8`，编译时会报大量的宏定义错误。务必确保 `git checkout` 的版本号一致。
- **权限问题**: 如果你将 Qt 安装在 `C:\Program Files` 等系统目录，执行 `cmake --install` 时需要管理员权限运行终端。由于你安装在 `D:\opt`，通常不会有此问题。
- **残留缓存**: 如果编译失败后修改了配置，记得删除 `build` 文件夹下的所有文件再重新开始。

---

下一步建议：

既然 MQTT 模块已经可以从源码级掌控了，你需要我帮你写一段基于 QMqttClient 的最小化连接代码，用来测试你的智能管家能否成功连接到公共 MQTT 服务器（如 HiveMQ）吗？