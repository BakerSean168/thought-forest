---
tags:
  - tech/lang/python
  - type/howto
  - status/growing
description: virtualenv
created: 2025-01-01T00:00:00
updated: 2025-12-07T21:16:37
---

> [!info] **上级索引**
> [[Python MOC]] | [[后端开发 MOC]]

---


# virtualenv

## 命令

### virtualenv 常用命令

[详细命令查看文档](https://virtualenv.pypa.io/en/latest/cli_interface.html#cli-flags)

| 命令                                       | 作用                   |
| ---------------------------------------- | -------------------- |
| `pip install virtualenv`                 | 安装 `virtualenv`      |
| `virtualenv myenv`                       | 创建一个名为 `myenv` 的虚拟环境 |
| `virtualenv -p /usr/bin/python3.8 myenv` | 指定 python 环境为 3.8    |
| `source myenv/bin/activate`              | 激活虚拟环境（Unix 或 MacOS） |
| `myenv\Scripts\activate`                 | 激活虚拟环境（Windows）      |
| `pip install package_name`               | 在虚拟环境中安装包            |
| `pip list`                               | 列出虚拟环境中已安装的包         |
| `deactivate`                             | 退出虚拟环境               |
| `rm -rf myenv`                           | 删除虚拟环境               |

## 版本信息

- **工具版本：**
- **最后更新：** 2025-09-29
