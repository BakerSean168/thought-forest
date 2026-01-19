---
tags: [type/howto, tech/tool/vim, status/growing]
description: Vim 编辑器完整教程与命令速查表
created: 2025-12-09T10:00:00
updated: 2025-12-23T11:25:57
---

# Vim 编辑器

> 高效的文本编辑器，掌握 Vim 能大幅提升编码效率

---

## 核心概念

### 三大模式

- **普通模式 (Normal Mode)**：导航与命令执行，`Esc` 进入
- **插入模式 (Insert Mode)**：文本编辑，按 `i`、`a`、`o` 等进入
- **命令模式 (Command Mode)**：执行高级命令，按 `:` 进入

### 模式切换流图

```
插入模式 --[Esc]--> 普通模式 --[:]--> 命令模式
                        |
                    [i/a/o 等]
                        |
                      插入模式
```

---

## 命令速查表

> 按用途分类，支持 Obsidian 片段链接：`[[Vim#^basic-move]]`

### 删除全部

ggdG

### 光标移动 ^basic-move

| 命令 | 说明 |
|------|------|
| `h` | 左移一字符 |
| `j` | 下移一行 |
| `k` | 上移一行 |
| `l` | 右移一字符 |
| `w` | 前进一个单词 |
| `W` | 前进一个单词（忽略标点） |
| `b` | 后退一个单词 |
| `B` | 后退一个单词（忽略标点） |
| `0` | 行首 |
| `$` | 行尾 |
| `^` | 行首非空字符 |
| `gg` | 文件开头 |
| `G` | 文件末尾 |
| `:{N}` | 跳转到第 N 行 |
| `Ctrl+U` | 上翻半页 |
| `Ctrl+D` | 下翻半页 |
| `Ctrl+F` | 前翻一页 |
| `Ctrl+B` | 后翻一页 |
| `f{char}` | 查找行内下一个指定字符 |
| `F{char}` | 查找行内上一个指定字符 |
| `t{char}` | 光标移至字符前 |
| `T{char}` | 光标移至字符后 |
| `;` | 重复最后一次 f/t 命令 |
| `,` | 反向重复最后一次 f/t 命令 |

### 查找与替换 ^search-replace

| 命令 | 说明 |
|------|------|
| `/{pattern}` | 向下查找 |
| `?{pattern}` | 向上查找 |
| `n` | 下一个匹配 |
| `N` | 上一个匹配 |
| `:s/old/new/` | 当前行替换（仅首次） |
| `:s/old/new/g` | 当前行全部替换 |
| `:%s/old/new/g` | 全文替换 |
| `:%s/old/new/gc` | 全文替换（确认每次） |
| `:set ic` | 不区分大小写查找 |
| `:set noic` | 区分大小写 |

### 编辑操作 ^edit-ops

| 命令          | 说明                      |
| ----------- | ----------------------- |
| `i`         | 光标前插入                   |
| `I`         | 行首插入                    |
| `a`         | 光标后插入                   |
| `A`         | 行尾插入                    |
| `o`         | 下一行插入                   |
| `O`         | 上一行插入                   |
| `x`         | 删除光标处字符                 |
| `X`         | 删除光标前字符                 |
| `d{motion}` | 删除（如 `dw` 删词，`dd` 删行）   |
| `dd`        | 删除整行                    |
| `D`         | 删除至行尾                   |
| `c{motion}` | 删除并进入插入模式               |
| `cc`        | 替换整行                    |
| `C`         | 替换至行尾                   |
| `y{motion}` | 复制（如 `yw` 复制词，`yy` 复制行） |
| `yy`        | 复制整行                    |
| `p`         | 光标后粘贴                   |
| `P`         | 光标前粘贴                   |
| `u`         | 撤销                      |
| `Ctrl+R`    | 重做                      |
| `~`         | 切换大小写                   |
| `>>`        | 右缩进                     |
| `<<`        | 左缩进                     |
| `==`        | 自动缩进                    |

### 选择 ^selection

| 命令 | 说明 |
|------|------|
| `v` | 字符选择 |
| `V` | 行选择 |
| `Ctrl+V` | 块选择 |
| `aw` | 选择一个单词（含空格） |
| `iw` | 选择单词（不含空格） |
| `ap` | 选择段落 |
| `ip` | 选择段落内容 |
| `a"` | 选择双引号内容及引号 |
| `i"` | 选择双引号内容 |

### 配置与设置 ^config

| 命令 | 说明 |
|------|------|
| `:set number` | 显示行号 |
| `:set relativenumber` | 相对行号 |
| `:set tabstop=4` | Tab 宽度 |
| `:set shiftwidth=4` | 缩进宽度 |
| `:set expandtab` | 用空格替代 Tab |
| `:set ignorecase` | 查找不分大小写 |
| `:set hlsearch` | 高亮查找结果 |
| `:set autoindent` | 自动缩进 |
| `:set hidden` | 缓冲区隐藏 |
| `:set mouse=a` | 启用鼠标 |
| `:set colorscheme {name}` | 设置配色 |
| `:edit ~/.vimrc` | 编辑配置文件 |
| `:source ~/.vimrc` | 重新加载配置 |

### 文件操作 ^file-ops

| 命令 | 说明 |
|------|------|
| `:e {file}` | 打开文件 |
| `:w` | 保存 |
| `:w {file}` | 另存为 |
| `:wq` | 保存并退出 |
| `:q!` | 不保存退出 |
| `:qa` | 全部关闭 |
| `:qa!` | 全部不保存关闭 |
| `:saveas {file}` | 另存为 |
| `:read {file}` | 插入另一文件内容 |
| `:!{cmd}` | 执行外部命令 |

### 分屏 ^split

| 命令 | 说明 |
|------|------|
| `:split` | 水平分屏 |
| `:vsplit` | 竖直分屏 |
| `:sp {file}` | 水平分屏打开文件 |
| `:vsp {file}` | 竖直分屏打开文件 |
| `Ctrl+W h/j/k/l` | 在分屏间移动 |
| `Ctrl+W =` | 等宽分屏 |
| `Ctrl+W _` | 最大化水平 |
| `Ctrl+W \|` | 最大化竖直 |

### 宏与重复 ^macro

| 命令 | 说明 |
|------|------|
| `q{a-z}` | 开始记录宏到寄存器 a-z |
| `q` | 停止记录 |
| `@{a-z}` | 执行宏 |
| `@@` | 重复上次宏 |
| `.` | 重复上次修改 |
| `:range{n}` | 重复 n 次 |

---

## 实操技巧

### 高效编辑模式

**组合操作示例**

```vim
" 删除单词并保留缩进
cw

" 在括号间跳转
%

" 重复上次命令
.

" 重复 5 次删除行
5dd

" 删除括号及其内容
di(
```

### 常用编程快捷方式

```vim
" 注释行（需配置 plugin）
gcc

" 增减缩进
>ip  (增加段落缩进)
<i{  (减少大括号内缩进)

" 格式化代码
gg=G  (全文自动缩进)

" 选中括号内所有内容
vi{
```

---

## 必备配置 (~/.vimrc)

```vim
" 基础设置
set number              " 显示行号
set relativenumber      " 相对行号
set tabstop=4          " Tab 宽度
set shiftwidth=4       " 缩进宽度
set expandtab          " 空格替代 Tab
set autoindent         " 自动缩进
set smartindent        " 智能缩进

" 查找与替换
set ignorecase         " 不分大小写
set smartcase          " 智能大小写
set hlsearch           " 高亮搜索
set incsearch          " 实时搜索

" UI 与交互
set mouse=a            " 启用鼠标
set hidden             " 缓冲区隐藏
set clipboard=unnamedplus  " 系统剪贴板
set background=dark    " 深色背景
colorscheme desert     " 配色方案

" 性能优化
set lazyredraw         " 延迟绘制
set ttyfast            " 快速终端
set history=1000       " 命令历史

" 文件编码
set encoding=utf-8
set fileencoding=utf-8
```

---

## 进阶学习路径

1. **基础阶段** → 掌握上述快速查表的常用命令
2. **提升阶段** → 学习 Vim Script、自定义快捷键
3. **高效阶段** → 配置 Plugin（vim-plug、NeoVim）
4. **专精阶段** → LSP、DAP、自定义扩展

---

## 参考资源

- 内置教程：`:help {topic}`
- `:help motion` 查看移动命令
- `:help options` 查看所有配置选项
- Vim cheatsheet 查询：`:help index`

---

*最后更新: 2025-12-09*
