---
AIGC:
  Label: '1'
  ContentProducer: '001191110108MA01KP2T5U00000'
  ProduceID: '7c8e5530-6be9-40bd-95d6-61b42100de39'
  ReservedCode1: ''
  ContentPropagator: '001191110108MA01KP2T5U00000'
  PropagateID: '7c8e5530-6be9-40bd-95d6-61b42100de39'
  ReservedCode2: ''
---

# MT-DOS 命令帮助
> 记号：`<...>` 必填参数，`[...]` 可选参数，`a/b` 表示同义别名（如 `md/mkdir`）。

MT-DOS 是一个用 tkinter 编写的虚拟 DOS / 终端操作系统模拟器：虚拟磁盘与分区、注册表、虚拟进程、光盘、局域网文件互传、Wikidot 站点浏览、音频工具、科学计算器等，全部通过终端命令操作。

## 版本总览

| # | 版本 | 源文件 | 修改时间 | 大小 | help 列出命令数 | 主要变化 |
|---|------|--------|----------|------|----------------|----------|
| 1 | 1.0 | `source1.0.py` | 10-12 | 297 KB | 67 | 初版：文件系统、虚拟磁盘、挖矿、Wikidot 浏览、音频、Python IDE、MTServer 多终端 |
| 2 | 1.1 | `source1.1.py` | 10-17 | 321 KB | 65 | 新增 MTU 软件包；移除 MTServer 多终端 |
| 3 | 1.2 | `source1.2.py` | 10-17 | 395 KB | 85 | 新增注册表（MTR）；webjs / cookies 增强 |
| 4 | 1.2c1 | `source1.2c1.py` | 10-18 | 335 KB | 75 | 1.2 精简版：移除 pyide/pyrun、内存与磁盘 I/O、LAN 广播、run/mtrun |
| 5 | 1.3f | `source1.3f.py` | 10-19 | 402 KB | 91 | 新增虚拟化（c2）、MTRAW、mtc 编译器、rmdir/move、注册表复制移动 |
| 6 | 1.3a1 | `source1.3a1.py` | 10-21 | 486 KB | 121 | 新增计算器 comp、mtz 归档、LAN 文件互传（23 条） |
| 7 | 1.3a2 | `source1.3a2.py` | 10-24 | 577 KB | 141 | 新增光盘 ISO/MTDF、type/cat、mkfile/touch、计算器 2.0 |
| 8 | 1.3a4 | `source1.3a4.py` | 11-01 | 596 KB | 142 | 新增 install 真机安装（技术预览）；余同 1.3a2 |

> 除 help 列出的命令外，每个版本都还有 3 个实际可用但未列入 help 的命令（`savemtc` / `delmtc` / `listmtc`），见下方「通用补充」。

## 通用补充（适用于所有版本）

以下命令在 `execute_command()` 中实际存在，但没有写进任何版本的 help 清单：

| 命令 | 说明 |
|------|------|
| `savemtc <name> <type> <body>` | 创建 MTC 扩展指令（保存到 `A:\`） |
| `savemtc <name> <type> --help "帮助文本" <body>` | 创建时附带帮助文本 |
| `savemtc <name> --from <file>` | 从现有文件创建扩展（按扩展名自动判断类型） |
| `delmtc <name>` | 删除一个 MTC 扩展指令 |
| `listmtc` | 列出所有已安装的 MTC 扩展指令 |

MTC 扩展类型：`python`（执行 Python 代码，完整系统权限）、`script`（逐行执行 MT-DOS 命令）、`js`（经 Node.js 执行 JavaScript）、`com`（执行 DOS COM 机器码十六进制）。

其他通用行为：

- **盘符切换**：直接输入 `C:`、`D:`、`M:` 等即可切换当前盘。
- **A:\ 中的 `.mtc` 文件**会注册为自定义命令，支持热插拔（改动自动生效），`reload` 可手动重载。
- `help` 显示命令分类总览，`help <分类>` 显示该分类的详细命令。

---

## 1. source1.0.py（版本 1.0）

修改时间 10-12，大小 297 KB。初始版本。具备文件系统、虚拟磁盘/分区、挖矿与钱包、Wikidot（MT-Backrooms）站点浏览、音频工具、Python IDE，以及独有的 MTServer 多终端服务器功能。

help 共列出 **67 条**命令（另有通用补充中的 3 条隐藏命令）：

### File System（8 条）

| 命令 | 说明 |
|------|------|
| `dir` | List directory contents |
| `cd <path>` | Change directory |
| `md/mkdir <name>` | Create directory |
| `ren/rename <old> <new>` | Rename file or directory |
| `copy <src> <dst>` | Copy file (supports cross-drive and wildcards) |
| `del <file>` | Delete file |
| `edit <file>` | Open text editor |
| `mkfile <name>` | Create empty file |

### Disk Management（12 条）

| 命令 | 说明 |
|------|------|
| `complist` | List all hardware (disks, RAM, GPU) |
| `mkdisk <size_gb>` | Create new virtual disk |
| `partdisk <disk> <size> <letter>` | Partition a disk |
| `deldisk <disk>` | Delete entire disk |
| `delpart <letter>` | Delete a partition |
| `plug disk <size> [on <disk>]` | Plug in / extend disk |
| `plug ram <size>` | Plug in virtual RAM |
| `unplug <disk>` | Unplug disk (offline) |
| `unplug ram <id>` | Unplug RAM module |
| `rescan [disk]` | Rescan hardware / bring disk online |
| `format <drive:> /fs:MTFS\|MAT64` | Format partition |
| `wipe <file>` | Securely erase file (unrecoverable) |

### Hardware（5 条）

| 命令 | 说明 |
|------|------|
| `hardware` | Show current hardware info |
| `mine` | Start mining |
| `stopmine` | Stop mining |
| `wallet` | Show wallet balance |
| `systeminfo` | Show system information |

### Network & Web（14 条）

| 命令 | 说明 |
|------|------|
| `crawl <url>` | Crawl web page content |
| `mt <page>` | Browse MT-Backrooms wikidot page |
| `login <user> <pass>` | Login to Wikidot |
| `logout` | Logout from Wikidot |
| `checklogin` | Check login status |
| `status` | Show session status |
| `cookies` | Show session cookies |
| `headers` | Show HTTP headers |
| `cdweb <url>` | Navigate to web page |
| `webpwd` | Show current web page info |
| `webdir` | List web page elements |
| `webedit <id> [value]` | Interact with web element |
| `webedit js <code>` | Execute JS on current page |
| `webjs <code>` | Execute JS on current page (alias) |

### Audio（6 条）

| 命令 | 说明 |
|------|------|
| `play <file>` | Play audio file |
| `stopaudio` | Stop audio playback |
| `audioinfo <file>` | Show audio file info |
| `waveform <file>` | Generate waveform image |
| `spectrogram <file>` | Generate spectrogram image |
| `player [file]` | Open audio player |

### Development（3 条）

| 命令 | 说明 |
|------|------|
| `pyide [file]` | Open Python IDE |
| `pyrun <file.py>` | Run Python file |
| `img <file>` | Open image viewer |

### System（6 条）

| 命令 | 说明 |
|------|------|
| `reboot` | Reboot system |
| `help [category]` | Show this help or category-specific help |
| `reload` | Reload mtc extensions from A:\ |
| `startserver` | Start MTServer on port 25565 |
| `spawnclient` | Spawn a new client terminal |
| `workspace <cid> ...` | Manage client workspace rules |

### Memory & Disk I/O（4 条）

| 命令 | 说明 |
|------|------|
| `memwrite <addr> <hex>` | Write binary data to memory address |
| `memread <addr> <length>` | Read binary data from memory address |
| `diskwrite <drive> <sector> <hex>` | Write binary data to disk sector |
| `diskread <drive> <sector> [count]` | Read disk sectors (hex dump) |

### Network（6 条）

| 命令 | 说明 |
|------|------|
| `lanbroadcast <port> <msg>` | Broadcast UDP message to LAN |
| `lanscan [port] [timeout]` | Listen for LAN broadcast messages |
| `jsweb <url>` | Open web page with JS support |
| `jsexec <code>` | Execute JS on current page |
| `jseval <expr>` | Evaluate JS expression |
| `jsclose` | Close JS page context |

### Executable（3 条）

| 命令 | 说明 |
|------|------|
| `run <file.com>` | Execute DOS COM file (Unicorn emulator) |
| `mtrun <file.mtef>` | Execute mtEF executable |
| `exec <file.exe> [args]` | Run real executable file |

### 本版本补充说明

- 本版本独有的 **MTServer 多终端**：`startserver` 启动服务器（端口 25565），`spawnclient [role] [name]` 生成客户端终端窗口。
- 客户端终端（spawnclient 打开的窗口）内使用另一套命令：`help`、`who`、`chat <消息>`、`chat @id <消息>`（私聊）、`broadcast <消息>`、`mkfile <文件名>`、`edit <文件名>`、`setrole <id> <角色>`、`ban <id>`、`exit`。
- `workspace` 子命令：`workspace <cid> true|false`（开关工作区限制）、`workspace <cid> allow <路径>`、`workspace <cid> block <路径>`。
- 注意：`mkfile` 只在客户端终端里实现，主终端输入无效（但出现在 help 清单中）。
- `run` 使用 Unicorn 引擎模拟执行 16 位 DOS COM 程序；`mtrun` 执行 mtEF 可执行文件。

---

## 2. source1.1.py（版本 1.1）

修改时间 10-17，大小 321 KB。新增 MTU 软件包系统；移除了 1.0 的 MTServer 多终端功能（startserver / spawnclient / workspace）。

help 共列出 **65 条**命令（另有通用补充中的 3 条隐藏命令）：

### File System（8 条）

| 命令 | 说明 |
|------|------|
| `dir` | List directory contents |
| `cd <path>` | Change directory |
| `md/mkdir <name>` | Create directory |
| `ren/rename <old> <new>` | Rename file or directory |
| `copy <src> <dst>` | Copy file (supports cross-drive and wildcards) |
| `del <file>` | Delete file |
| `edit <file>` | Open text editor |
| `mkfile <name>` | Create empty file |

### Disk Management（12 条）

| 命令 | 说明 |
|------|------|
| `complist` | List all hardware (disks, RAM, GPU) |
| `mkdisk <size_gb>` | Create new virtual disk |
| `partdisk <disk> <size> <letter>` | Partition a disk |
| `deldisk <disk>` | Delete entire disk |
| `delpart <letter>` | Delete a partition |
| `plug disk <size> [on <disk>]` | Plug in / extend disk |
| `plug ram <size>` | Plug in virtual RAM |
| `unplug <disk>` | Unplug disk (offline) |
| `unplug ram <id>` | Unplug RAM module |
| `rescan [disk]` | Rescan hardware / bring disk online |
| `format <drive:> /fs:MTFS\|MAT64` | Format partition |
| `wipe <file>` | Securely erase file (unrecoverable) |

### Hardware（5 条）

| 命令 | 说明 |
|------|------|
| `hardware` | Show current hardware info |
| `mine` | Start mining |
| `stopmine` | Stop mining |
| `wallet` | Show wallet balance |
| `systeminfo` | Show system information |

### Network & Web（14 条）

| 命令 | 说明 |
|------|------|
| `crawl <url>` | Crawl web page content |
| `mt <page>` | Browse MT-Backrooms wikidot page |
| `login <user> <pass>` | Login to Wikidot |
| `logout` | Logout from Wikidot |
| `checklogin` | Check login status |
| `status` | Show session status |
| `cookies` | Show session cookies |
| `headers` | Show HTTP headers |
| `cdweb <url>` | Navigate to web page |
| `webpwd` | Show current web page info |
| `webdir` | List web page elements |
| `webedit <id> [value]` | Interact with web element |
| `webedit js <code>` | Execute JS on current page |
| `webjs <code>` | Execute JS on current page (alias) |

### Audio（6 条）

| 命令 | 说明 |
|------|------|
| `play <file>` | Play audio file |
| `stopaudio` | Stop audio playback |
| `audioinfo <file>` | Show audio file info |
| `waveform <file>` | Generate waveform image |
| `spectrogram <file>` | Generate spectrogram image |
| `player [file]` | Open audio player |

### Development（3 条）

| 命令 | 说明 |
|------|------|
| `pyide [file]` | Open Python IDE |
| `pyrun <file.py>` | Run Python file |
| `img <file>` | Open image viewer |

### System（4 条）

| 命令 | 说明 |
|------|------|
| `reboot` | Reboot system |
| `help [category]` | Show this help or category-specific help |
| `reload` | Reload mtc extensions from A:\ |
| `mtu list\|info\|install\|uninstall\|make` | Manage MTU packages installed from the floppy (A:) |

### Memory & Disk I/O（4 条）

| 命令 | 说明 |
|------|------|
| `memwrite <addr> <hex>` | Write binary data to memory address |
| `memread <addr> <length>` | Read binary data from memory address |
| `diskwrite <drive> <sector> <hex>` | Write binary data to disk sector |
| `diskread <drive> <sector> [count]` | Read disk sectors (hex dump) |

### Network（6 条）

| 命令 | 说明 |
|------|------|
| `lanbroadcast <port> <msg>` | Broadcast UDP message to LAN |
| `lanscan [port] [timeout]` | Listen for LAN broadcast messages |
| `jsweb <url>` | Open web page with JS support |
| `jsexec <code>` | Execute JS on current page |
| `jseval <expr>` | Evaluate JS expression |
| `jsclose` | Close JS page context |

### Executable（3 条）

| 命令 | 说明 |
|------|------|
| `run <file.com>` | Execute DOS COM file (Unicorn emulator) |
| `mtrun <file.mtef>` | Execute mtEF executable |
| `exec <file.exe> [args]` | Run real executable file |

### 本版本补充说明

- 新增 **MTU 软件包**：把 `.mtu` 文件放入软盘目录 `A:\`，下次启动自动装入虚拟磁盘；`mtu make` 可从虚拟文件打包。
- 实际可用的 `mtu` 子命令比 help 文本多一个：`mtu reinstall <name>|all`。
- `mkfile` 列于 help 但主终端未实现（1.0 的遗留文本）。

---

## 3. source1.2.py（版本 1.2）

修改时间 10-17，大小 395 KB。新增注册表（MTR，15 条命令）；webjs 增强（可直接执行 JS 或按 webdir 编号操作元素）；cookies 支持子命令管理。

help 共列出 **85 条**命令（另有通用补充中的 3 条隐藏命令）：

### File System（8 条）

| 命令 | 说明 |
|------|------|
| `dir` | List directory contents |
| `cd <path>` | Change directory |
| `md/mkdir <name>` | Create directory |
| `ren/rename <old> <new>` | Rename file or directory |
| `copy <src> <dst>` | Copy file (supports cross-drive and wildcards) |
| `del <file>` | Delete file |
| `edit <file>` | Open text editor |
| `mkfile <name>` | Create empty file |

### Disk Management（12 条）

| 命令 | 说明 |
|------|------|
| `complist` | List all hardware (disks, RAM, GPU) |
| `mkdisk <size_gb>` | Create new virtual disk |
| `partdisk <disk> <size> <letter>` | Partition a disk |
| `deldisk <disk>` | Delete entire disk |
| `delpart <letter>` | Delete a partition |
| `plug disk <size> [on <disk>]` | Plug in / extend disk |
| `plug ram <size>` | Plug in virtual RAM |
| `unplug <disk>` | Unplug disk (offline) |
| `unplug ram <id>` | Unplug RAM module |
| `rescan [disk]` | Rescan hardware / bring disk online |
| `format <drive:> /fs:MTFS\|MAT64` | Format partition |
| `wipe <file>` | Securely erase file (unrecoverable) |

### Hardware（5 条）

| 命令 | 说明 |
|------|------|
| `hardware` | Show current hardware info |
| `mine` | Start mining |
| `stopmine` | Stop mining |
| `wallet` | Show wallet balance |
| `systeminfo` | Show system information |

### Network & Web（14 条）

| 命令 | 说明 |
|------|------|
| `crawl <url>` | Crawl web page content |
| `mt <page>` | Browse MT-Backrooms wikidot page |
| `login <user> <pass>` | Login to Wikidot |
| `logout` | Logout from Wikidot |
| `checklogin` | Check login status |
| `status` | Show session status |
| `cookies` | Show session cookies |
| `headers` | Show HTTP headers |
| `cdweb <url>` | Navigate to web page |
| `webpwd` | Show current web page info |
| `webdir` | List web page elements |
| `webedit <id> [value]` | Interact with web element |
| `webedit js <code>` | Execute JS on current page |
| `webjs <code>` | Execute JS on current page (alias) |

### Audio（6 条）

| 命令 | 说明 |
|------|------|
| `play <file>` | Play audio file |
| `stopaudio` | Stop audio playback |
| `audioinfo <file>` | Show audio file info |
| `waveform <file>` | Generate waveform image |
| `spectrogram <file>` | Generate spectrogram image |
| `player [file]` | Open audio player |

### Development（3 条）

| 命令 | 说明 |
|------|------|
| `pyide [file]` | Open Python IDE |
| `pyrun <file.py>` | Run Python file |
| `img <file>` | Open image viewer |

### System（4 条）

| 命令 | 说明 |
|------|------|
| `reboot` | Reboot system |
| `help [category]` | Show this help or category-specific help |
| `reload` | Reload mtc extensions from A:\ |
| `mtu list\|info\|install\|uninstall\|make` | Manage MTU packages installed from the floppy (A:) |

### Memory & Disk I/O（4 条）

| 命令 | 说明 |
|------|------|
| `memwrite <addr> <hex>` | Write binary data to memory address |
| `memread <addr> <length>` | Read binary data from memory address |
| `diskwrite <drive> <sector> <hex>` | Write binary data to disk sector |
| `diskread <drive> <sector> [count]` | Read disk sectors (hex dump) |

### Network（6 条）

| 命令 | 说明 |
|------|------|
| `lanbroadcast <port> <msg>` | Broadcast UDP message to LAN |
| `lanscan [port] [timeout]` | Listen for LAN broadcast messages |
| `jsweb <url>` | Open web page with JS support |
| `jsexec <code>` | Execute JS on current page |
| `jseval <expr>` | Evaluate JS expression |
| `jsclose` | Close JS page context |

### Web（5 条）

| 命令 | 说明 |
|------|------|
| `webjs` | JS exec OR ID-based element op (mirrors webedit) |
| `webjs <id>` | Operate element by webdir ID (= webedit <id>) |
| `webjs js <code>` | Run JS code (explicit) |
| `webjs <code>` | Run JS code (page.title, querySelector, etc.) |
| `cookies [list\|clear\|save\|load\|set]` | Cookie management (auto-saved by session) |

### Executable（3 条）

| 命令 | 说明 |
|------|------|
| `run <file.com>` | Execute DOS COM file (Unicorn emulator) |
| `mtrun <file.mtef>` | Execute mtEF executable |
| `exec <file.exe> [args]` | Run real executable file |

### Registry（15 条）

| 命令 | 说明 |
|------|------|
| `mtr` | List current registry key (= mtrdir) |
| `mtrdir [path]` | List subkeys and values under a key |
| `cdmtr <path>` | Change current registry key (supports .././absolute) |
| `mtrpwd` | Show current registry path |
| `mkitem <name>` | Create a key (with intermediates) |
| `delitem <name>` | Delete a key and all its children |
| `renitem <old> <new>` | Rename a key |
| `setval <name> <type> <value> [--create]` | Set a value under current key (types: file/dir/switch/int/string/binary) |
| `getval <name>` | Get a value under current key |
| `delval <name>` | Delete a value under current key |
| `mtrtree [path]` | Show registry tree (depth=4) |
| `mtrexport <file>` | Export the whole registry to a file |
| `mtrimport <file>` | Import a registry file (replaces all) |
| `mtrpath` | Show the .mtr file location |
| `mtrhelp` | Show MTR command family help |

### 本版本补充说明

- 新增 **注册表**：数据保存为 `.mtr` 文件，`mtr` 系列命令管理键值（类型：file/dir/switch/int/string/binary）。
- `webjs` 双模式：既可执行 JS 代码，也可按 `webdir` 输出的编号操作页面元素（等价 `webedit <id>`）。
- `cookies` 子命令：`list / clear / save / load / set`（会话自动保存）。

---

## 4. source1.2c1.py（版本 1.2c1）

修改时间 10-18，大小 335 KB。1.2 的精简版（c1）。移除：pyide / pyrun（Python IDE）、memwrite / memread / diskwrite / diskread（内存与磁盘 I/O）、lanbroadcast / lanscan（UDP 广播）、run / mtrun（COM 模拟与 mtEF 运行）。其余与 1.2 一致（含注册表）。

help 共列出 **75 条**命令（另有通用补充中的 3 条隐藏命令）：

### File System（8 条）

| 命令 | 说明 |
|------|------|
| `dir` | List directory contents |
| `cd <path>` | Change directory |
| `md/mkdir <name>` | Create directory |
| `ren/rename <old> <new>` | Rename file or directory |
| `copy <src> <dst>` | Copy file (supports cross-drive and wildcards) |
| `del <file>` | Delete file |
| `edit <file>` | Open text editor |
| `mkfile <name>` | Create empty file |

### Disk Management（12 条）

| 命令 | 说明 |
|------|------|
| `complist` | List all hardware (disks, RAM, GPU) |
| `mkdisk <size_gb>` | Create new virtual disk |
| `partdisk <disk> <size> <letter>` | Partition a disk |
| `deldisk <disk>` | Delete entire disk |
| `delpart <letter>` | Delete a partition |
| `plug disk <size> [on <disk>]` | Plug in / extend disk |
| `plug ram <size>` | Plug in virtual RAM |
| `unplug <disk>` | Unplug disk (offline) |
| `unplug ram <id>` | Unplug RAM module |
| `rescan [disk]` | Rescan hardware / bring disk online |
| `format <drive:> /fs:MTFS\|MAT64` | Format partition |
| `wipe <file>` | Securely erase file (unrecoverable) |

### Hardware（5 条）

| 命令 | 说明 |
|------|------|
| `hardware` | Show current hardware info |
| `mine` | Start mining |
| `stopmine` | Stop mining |
| `wallet` | Show wallet balance |
| `systeminfo` | Show system information |

### Network & Web（14 条）

| 命令 | 说明 |
|------|------|
| `crawl <url>` | Crawl web page content |
| `mt <page>` | Browse MT-Backrooms wikidot page |
| `login <user> <pass>` | Login to Wikidot |
| `logout` | Logout from Wikidot |
| `checklogin` | Check login status |
| `status` | Show session status |
| `cookies` | Show session cookies |
| `headers` | Show HTTP headers |
| `cdweb <url>` | Navigate to web page |
| `webpwd` | Show current web page info |
| `webdir` | List web page elements |
| `webedit <id> [value]` | Interact with web element |
| `webedit js <code>` | Execute JS on current page |
| `webjs <code>` | Execute JS on current page (alias) |

### Audio（6 条）

| 命令 | 说明 |
|------|------|
| `play <file>` | Play audio file |
| `stopaudio` | Stop audio playback |
| `audioinfo <file>` | Show audio file info |
| `waveform <file>` | Generate waveform image |
| `spectrogram <file>` | Generate spectrogram image |
| `player [file]` | Open audio player |

### Development（1 条）

| 命令 | 说明 |
|------|------|
| `img <file>` | Open image viewer |

### System（4 条）

| 命令 | 说明 |
|------|------|
| `reboot` | Reboot system |
| `help [category]` | Show this help or category-specific help |
| `reload` | Reload mtc extensions from A:\ |
| `mtu list\|info\|install\|uninstall\|make` | Manage MTU packages installed from the floppy (A:) |

### Network（4 条）

| 命令 | 说明 |
|------|------|
| `jsweb <url>` | Open web page with JS support |
| `jsexec <code>` | Execute JS on current page |
| `jseval <expr>` | Evaluate JS expression |
| `jsclose` | Close JS page context |

### Web（5 条）

| 命令 | 说明 |
|------|------|
| `webjs` | JS exec OR ID-based element op (mirrors webedit) |
| `webjs <id>` | Operate element by webdir ID (= webedit <id>) |
| `webjs js <code>` | Run JS code (explicit) |
| `webjs <code>` | Run JS code (page.title, querySelector, etc.) |
| `cookies [list\|clear\|save\|load\|set]` | Cookie management (auto-saved by session) |

### Executable（1 条）

| 命令 | 说明 |
|------|------|
| `exec <file.exe> [args]` | Run real executable file |

### Registry（15 条）

| 命令 | 说明 |
|------|------|
| `mtr` | List current registry key (= mtrdir) |
| `mtrdir [path]` | List subkeys and values under a key |
| `cdmtr <path>` | Change current registry key (supports .././absolute) |
| `mtrpwd` | Show current registry path |
| `mkitem <name>` | Create a key (with intermediates) |
| `delitem <name>` | Delete a key and all its children |
| `renitem <old> <new>` | Rename a key |
| `setval <name> <type> <value> [--create]` | Set a value under current key (types: file/dir/switch/int/string/binary) |
| `getval <name>` | Get a value under current key |
| `delval <name>` | Delete a value under current key |
| `mtrtree [path]` | Show registry tree (depth=4) |
| `mtrexport <file>` | Export the whole registry to a file |
| `mtrimport <file>` | Import a registry file (replaces all) |
| `mtrpath` | Show the .mtr file location |
| `mtrhelp` | Show MTR command family help |

---

## 5. source1.3f.py（版本 1.3f）

修改时间 10-19，大小 402 KB。新增虚拟化层（c2：虚拟进程/任务管理器/CPU 亲和性/内存沙箱）、MTRAW 裸盘文件系统、mtc 编译器、rmdir 与 move、注册表复制/移动。按文件时间此版本早于 1.3a1（f 与 a 为不同分支）。

help 共列出 **91 条**命令（另有通用补充中的 3 条隐藏命令）：

### File System（10 条）

| 命令 | 说明 |
|------|------|
| `dir` | List directory contents |
| `cd <path>` | Change directory |
| `md/mkdir <name>` | Create directory |
| `rmdir/rd <dir> [/s]` | Remove directory (/s = recursive) |
| `ren/rename <old> <new>` | Rename file or directory |
| `copy <src> <dst>` | Copy file (supports cross-drive and wildcards) |
| `move/mv/cut <src> <dst>` | Move file or directory (cross-drive supported) |
| `del <file>` | Delete file |
| `edit <file>` | Open text editor |
| `mkfile <name>` | Create empty file |

### Disk Management（13 条）

| 命令 | 说明 |
|------|------|
| `complist` | List all hardware (disks, RAM, GPU) |
| `mkdisk <size_gb>` | Create new virtual disk |
| `partdisk <disk> <size> <letter>` | Partition a disk |
| `deldisk <disk>` | Delete entire disk |
| `delpart <letter>` | Delete a partition |
| `plug disk <size> [on <disk>]` | Plug in / extend disk |
| `plug ram <size>` | Plug in virtual RAM |
| `unplug <disk>` | Unplug disk (offline) |
| `unplug ram <id>` | Unplug RAM module |
| `rescan [disk]` | Rescan hardware / bring disk online |
| `format <drive:> /fs:MTFS\|MAT64\|MTRAW` | Format partition (3 fs types) |
| `wipe <file>` | Securely erase file (unrecoverable) |
| `mtraw info\|scan\|format` | MTRAW-specific operations |

### Virtualization (c2)（8 条）

| 命令 | 说明 |
|------|------|
| `tdir/tlist/ps` | List virtual processes and handles |
| `cdtask <pid\|handle>` | Switch process tree context |
| `tdel/tkill <pid\|handle>` | Terminate a process (recursive) |
| `tspawn <name> [mem] [cores]` | Spawn a virtual process |
| `taskmon/vmon` | Virtual hardware monitor (CPU/mem/disk) |
| `cpuaff set <cores>\|list\|reset\|cores <n>` | CPU affinity control |
| `memstat` | Memory sandbox status (2GB cap) |
| `memgrow <gb>` | Expand memory sandbox cap |

### Hardware (legacy)（5 条）

| 命令 | 说明 |
|------|------|
| `hardware` | Show REAL hardware info (legacy) |
| `mine` | Start mining |
| `stopmine` | Stop mining |
| `wallet` | Show wallet balance |
| `systeminfo` | Show system information |

### Network & Web（14 条）

| 命令 | 说明 |
|------|------|
| `crawl <url>` | Crawl web page content |
| `mt <page>` | Browse MT-Backrooms wikidot page |
| `login <user> <pass>` | Login to Wikidot |
| `logout` | Logout from Wikidot |
| `checklogin` | Check login status |
| `status` | Show session status |
| `cookies` | Show session cookies |
| `headers` | Show HTTP headers |
| `cdweb <url>` | Navigate to web page |
| `webpwd` | Show current web page info |
| `webdir` | List web page elements |
| `webedit <id> [value]` | Interact with web element |
| `webedit js <code>` | Execute JS on current page |
| `webjs <code>` | Execute JS on current page (alias) |

### Audio（6 条）

| 命令 | 说明 |
|------|------|
| `play <file>` | Play audio file |
| `stopaudio` | Stop audio playback |
| `audioinfo <file>` | Show audio file info |
| `waveform <file>` | Generate waveform image |
| `spectrogram <file>` | Generate spectrogram image |
| `player [file]` | Open audio player |

### Development（1 条）

| 命令 | 说明 |
|------|------|
| `img <file>` | Open image viewer |

### System（4 条）

| 命令 | 说明 |
|------|------|
| `reboot` | Reboot system |
| `help [category]` | Show this help or category-specific help |
| `reload` | Reload mtc extensions from A:\ |
| `mtu list\|info\|install\|uninstall\|make` | Manage MTU packages installed from the floppy (A:) |

### Network（4 条）

| 命令 | 说明 |
|------|------|
| `jsweb <url>` | Open web page with JS support |
| `jsexec <code>` | Execute JS on current page |
| `jseval <expr>` | Evaluate JS expression |
| `jsclose` | Close JS page context |

### Web（5 条）

| 命令 | 说明 |
|------|------|
| `webjs` | JS exec OR ID-based element op (mirrors webedit) |
| `webjs <id>` | Operate element by webdir ID (= webedit <id>) |
| `webjs js <code>` | Run JS code (explicit) |
| `webjs <code>` | Run JS code (page.title, querySelector, etc.) |
| `cookies [list\|clear\|save\|load\|set]` | Cookie management (auto-saved by session) |

### Executable（1 条）

| 命令 | 说明 |
|------|------|
| `exec <file.exe> [args]` | Run real executable file |

### Registry（17 条）

| 命令 | 说明 |
|------|------|
| `mtr` | List current registry key (= mtrdir) |
| `mtrdir [path]` | List subkeys and values under a key |
| `cdmtr <path>` | Change current registry key (supports .././absolute) |
| `mtrpwd` | Show current registry path |
| `mkitem <name>` | Create a key (with intermediates) |
| `delitem <name>` | Delete a key and all its children |
| `renitem <old> <new>` | Rename a key |
| `mtrcopy/mtrcp <src> <dst>` | Copy a key (with children) or a value |
| `mtrcut/mtrmv <src> <dst>` | Move (cut) a key or a value |
| `setval <name> <type> <value> [--create]` | Set a value under current key (types: file/dir/switch/int/string/binary) |
| `getval <name>` | Get a value under current key |
| `delval <name>` | Delete a value under current key |
| `mtrtree [path]` | Show registry tree (depth=4) |
| `mtrexport <file>` | Export the whole registry to a file |
| `mtrimport <file>` | Import a registry file (replaces all) |
| `mtrpath` | Show the .mtr file location |
| `mtrhelp` | Show MTR command family help |

### Executable (mtEF)（3 条）

| 命令 | 说明 |
|------|------|
| `mtc <src.py> <dst.mtef>` | Compile Python source to mtEF binary |
| `mtc --batch-only <dst> <cmds>` | Compile batch-only mtEF |
| `tspawn <file.mtef> [mem] [cores] [args]` | Execute mtEF, spawns virtual process |

### 本版本补充说明

- 新增 **虚拟化（c2）**：`tspawn` 生成虚拟进程（可指定内存 MB 与 CPU 核心），`tdir/ps` 列出进程，`taskmon` 打开虚拟硬件监视器，`memstat/memgrow` 管理内存沙箱（默认上限 2GB）。
- 别名：`kill`（=tdel/tkill）、`hwmon`（=taskmon/vmon）、`affinity`（=cpuaff）、`meminfo`（=memstat）。
- `format` 支持 3 种文件系统：MTFS / MAT64 / MTRAW。

---

## 6. source1.3a1.py（版本 1.3a1）

修改时间 10-21，大小 486 KB。新增科学计算器 comp、mtz 压缩包、LAN 局域网文件互传（23 条命令）；mine/wallet 语义改为 MTY AFK-boost / 使用积分。

help 共列出 **121 条**命令（另有通用补充中的 3 条隐藏命令）：

### File System（10 条）

| 命令 | 说明 |
|------|------|
| `dir` | List directory contents |
| `cd <path>` | Change directory |
| `md/mkdir <name>` | Create directory |
| `rmdir/rd <dir> [/s]` | Remove directory (/s = recursive) |
| `ren/rename <old> <new>` | Rename file or directory |
| `copy <src> <dst>` | Copy file (supports cross-drive and wildcards) |
| `move/mv/cut <src> <dst>` | Move file or directory (cross-drive supported) |
| `del <file>` | Delete file |
| `edit <file>` | Open text editor |
| `mkfile <name>` | Create empty file |

### Disk Management（13 条）

| 命令 | 说明 |
|------|------|
| `complist` | List all hardware (disks, RAM, GPU) |
| `mkdisk <size_gb>` | Create new virtual disk |
| `partdisk <disk> <size> <letter>` | Partition a disk |
| `deldisk <disk>` | Delete entire disk |
| `delpart <letter>` | Delete a partition |
| `plug disk <size> [on <disk>]` | Plug in / extend disk |
| `plug ram <size>` | Plug in virtual RAM |
| `unplug <disk>` | Unplug disk (offline) |
| `unplug ram <id>` | Unplug RAM module |
| `rescan [disk]` | Rescan hardware / bring disk online |
| `format <drive:> /fs:MTFS\|MAT64\|MTRAW` | Format partition (3 fs types) |
| `wipe <file>` | Securely erase file (unrecoverable) |
| `mtraw info\|scan\|format` | MTRAW-specific operations |

### Virtualization (c2)（8 条）

| 命令 | 说明 |
|------|------|
| `tdir/tlist/ps` | List virtual processes and handles |
| `cdtask <pid\|handle>` | Switch process tree context |
| `tdel/tkill <pid\|handle>` | Terminate a process (recursive) |
| `tspawn <name> [mem] [cores]` | Spawn a virtual process |
| `taskmon/vmon` | Virtual hardware monitor (CPU/mem/disk) |
| `cpuaff set <cores>\|list\|reset\|cores <n>` | CPU affinity control |
| `memstat` | Memory sandbox status (2GB cap) |
| `memgrow <gb>` | Expand memory sandbox cap |

### Hardware (legacy)（5 条）

| 命令 | 说明 |
|------|------|
| `hardware` | Show REAL hardware info (legacy) |
| `mine` | Start MTY AFK-boost (idle score accrual) |
| `stopmine` | Stop MTY AFK-boost |
| `wallet` | Show MTY usage score |
| `systeminfo` | Show system information |

### Calculator (1.3a1)（2 条）

| 命令 | 说明 |
|------|------|
| `comp <expr>` | Evaluate math expression (sin/cos/sqrt/pi/...) |
| `comp solve <eq> [for <v>]` | Solve an equation (linear/polynomial/general) |

### Archive (1.3a1)（5 条）

| 命令 | 说明 |
|------|------|
| `mtz create <a.mtz> <f\|dir> [...]` | Create a compressed .mtz archive |
| `mtz list <a.mtz>` | List contents of a .mtz archive |
| `mtz extract <a.mtz> [dest]` | Extract a .mtz archive |
| `mtz add <a.mtz> <f\|dir> [...]` | Add entries to a .mtz archive |
| `mtz remove <a.mtz> <name>` | Remove an entry from a .mtz archive |

### LAN (1.3a1)（23 条）

| 命令 | 说明 |
|------|------|
| `lan start [port]` | Start the LAN file server (default 53210) |
| `lan stop` | Stop the LAN file server |
| `lan key <passphrase>` | Set pairing key (must match on both sides) |
| `lan share <path>` | Set the shared root folder |
| `lan connect <host> <port> <password>` | Connect to a peer (verify key) |
| `lan disconnect` | Forget the current peer |
| `lan status` | Show server / key / share / peer status |
| `lan peers` | List known peers |
| `cdlan [path]` | Change remote working dir (\ / .. .) |
| `lanpwd` | Show remote working dir |
| `landir [path]` | List remote dir |
| `lancopy <rsrc> <ldst>` | Copy file from peer to local |
| `lanput <lsrc> <rdst>` | Copy file from local to peer |
| `lancut <rsrc> <ldst>` | Copy from peer then delete remote source |
| `landel <rname>` | Delete file/dir on peer |
| `lanmkdir <rname>` | Make dir on peer |
| `lanmove <rsrc> <rdst>` | Rename/move on peer |
| `lanstat <rname>` | Show remote file info |
| `lanedit <rname>` | Fetch, edit locally, push back |
| `lan pair <host> [port]` | Verify key with a peer (low-level) |
| `lan dir <host> [port]` | List a peer's shared folder (low-level) |
| `lan get <host> <rfile> [lfile]` | Download (low-level) |
| `lan put <host> <lfile> [rfile]` | Upload (low-level) |

### Network & Web（14 条）

| 命令 | 说明 |
|------|------|
| `crawl <url>` | Crawl web page content |
| `mt <page>` | Browse MT-Backrooms wikidot page |
| `login <user> <pass>` | Login to Wikidot |
| `logout` | Logout from Wikidot |
| `checklogin` | Check login status |
| `status` | Show session status |
| `cookies` | Show session cookies |
| `headers` | Show HTTP headers |
| `cdweb <url>` | Navigate to web page |
| `webpwd` | Show current web page info |
| `webdir` | List web page elements |
| `webedit <id> [value]` | Interact with web element |
| `webedit js <code>` | Execute JS on current page |
| `webjs <code>` | Execute JS on current page (alias) |

### Audio（6 条）

| 命令 | 说明 |
|------|------|
| `play <file>` | Play audio file |
| `stopaudio` | Stop audio playback |
| `audioinfo <file>` | Show audio file info |
| `waveform <file>` | Generate waveform image |
| `spectrogram <file>` | Generate spectrogram image |
| `player [file]` | Open audio player |

### Development（1 条）

| 命令 | 说明 |
|------|------|
| `img <file>` | Open image viewer |

### System（4 条）

| 命令 | 说明 |
|------|------|
| `reboot` | Reboot system |
| `help [category]` | Show this help or category-specific help |
| `reload` | Reload mtc extensions from A:\ |
| `mtu list\|info\|install\|uninstall\|make` | Manage MTU packages installed from the floppy (A:) |

### Network（4 条）

| 命令 | 说明 |
|------|------|
| `jsweb <url>` | Open web page with JS support |
| `jsexec <code>` | Execute JS on current page |
| `jseval <expr>` | Evaluate JS expression |
| `jsclose` | Close JS page context |

### Web（5 条）

| 命令 | 说明 |
|------|------|
| `webjs` | JS exec OR ID-based element op (mirrors webedit) |
| `webjs <id>` | Operate element by webdir ID (= webedit <id>) |
| `webjs js <code>` | Run JS code (explicit) |
| `webjs <code>` | Run JS code (page.title, querySelector, etc.) |
| `cookies [list\|clear\|save\|load\|set]` | Cookie management (auto-saved by session) |

### Executable（1 条）

| 命令 | 说明 |
|------|------|
| `exec <file.exe> [args]` | Run real executable file |

### Registry（17 条）

| 命令 | 说明 |
|------|------|
| `mtr` | List current registry key (= mtrdir) |
| `mtrdir [path]` | List subkeys and values under a key |
| `cdmtr <path>` | Change current registry key (supports .././absolute) |
| `mtrpwd` | Show current registry path |
| `mkitem <name>` | Create a key (with intermediates) |
| `delitem <name>` | Delete a key and all its children |
| `renitem <old> <new>` | Rename a key |
| `mtrcopy/mtrcp <src> <dst>` | Copy a key (with children) or a value |
| `mtrcut/mtrmv <src> <dst>` | Move (cut) a key or a value |
| `setval <name> <type> <value> [--create]` | Set a value under current key (types: file/dir/switch/int/string/binary) |
| `getval <name>` | Get a value under current key |
| `delval <name>` | Delete a value under current key |
| `mtrtree [path]` | Show registry tree (depth=4) |
| `mtrexport <file>` | Export the whole registry to a file |
| `mtrimport <file>` | Import a registry file (replaces all) |
| `mtrpath` | Show the .mtr file location |
| `mtrhelp` | Show MTR command family help |

### Executable (mtEF)（3 条）

| 命令 | 说明 |
|------|------|
| `mtc <src.py> <dst.mtef>` | Compile Python source to mtEF binary |
| `mtc --batch-only <dst> <cmds>` | Compile batch-only mtEF |
| `tspawn <file.mtef> [mem] [cores] [args]` | Execute mtEF, spawns virtual process |

### 本版本补充说明

- 新增 **LAN 文件互传**：带配对密钥的局域网文件服务器 + 远程文件管理；`lan connect` 连接对端后可用 `cdlan/landir/lancopy/lanput` 等操作远程目录，另有 `lan pair/dir/get/put` 低层命令。
- 别名：`kill`（=tdel/tkill）、`hwmon`（=taskmon/vmon）、`affinity`（=cpuaff）、`meminfo`（=memstat）。
- `format` 支持 3 种文件系统：MTFS / MAT64 / MTRAW。

---

## 7. source1.3a2.py（版本 1.3a2）

修改时间 10-24，大小 577 KB。新增光盘系统（disc 系列 + MTDF 文件系统）、type/cat 查看文件、mkfile/touch 建空文件（本版起 mkfile 真正可用）、计算器升级 2.0。

help 共列出 **141 条**命令（另有通用补充中的 3 条隐藏命令）：

### File System（11 条）

| 命令 | 说明 |
|------|------|
| `dir [path]` | List directory (dir M:\ , dir C:\docs , ...) |
| `cd <path>` | Change directory |
| `md/mkdir <name>` | Create directory |
| `rmdir/rd <dir> [/s]` | Remove directory (/s = recursive) |
| `ren/rename <old> <new>` | Rename file or directory |
| `copy <src> <dst>` | Copy file (supports cross-drive and wildcards) |
| `move/mv/cut <src> <dst>` | Move file or directory (cross-drive supported) |
| `del <file>` | Delete file (supports wildcards, e.g. del M:\*.tmp) |
| `type <file>` | Display text file contents (type M:\readme.txt) |
| `edit <file>` | Open text editor |
| `mkfile <name>` | Create empty file |

### Disk Management（13 条）

| 命令 | 说明 |
|------|------|
| `complist` | List all hardware (disks, optical/floppy drives, RAM, GPU) |
| `mkdisk <size_gb>` | Create new virtual disk |
| `partdisk <disk> <size> <letter>` | Partition a disk |
| `deldisk <disk>` | Delete entire disk |
| `delpart <letter>` | Delete a partition |
| `plug disk <size> [on <disk>]` | Plug in / extend disk |
| `plug ram <size>` | Plug in virtual RAM |
| `unplug <disk>` | Unplug disk (offline) |
| `unplug ram <id>` | Unplug RAM module |
| `rescan [disk]` | Rescan hardware / bring disk online |
| `format <drive:> /fs:MTFS\|MAT64\|MTRAW\|MTDF` | Format partition (4 fs types) |
| `wipe <file>` | Securely erase file (unrecoverable) |
| `mtraw info\|scan\|format` | MTRAW-specific operations |

### Optical Disc (ISO)（10 条）

启动时自动把 `disc\mtdisc0m.iso` 挂载为 `M:`（默认盘），文件命令可直接使用 `M:\` 路径。

| 命令 | 说明 |
|------|------|
| `disc list` | List all ISO files in disc\ |
| `disc info <name>` | Show disc info (MTDF or unknown) |
| `disc create <name> [label]` | Create new empty MTDF disc |
| `disc import <host_file> [as <name>]` | Copy external ISO into disc\ |
| `disc delete <name>` | Delete an ISO file |
| `disc format <name> [label]` | Re-format disc as MTDF (erases data) |
| `disc label <name> <new label>` | Set MTDF volume label |
| `disc insert <name> [letter:]` | Insert (mount) disc to drive M-Z |
| `disc eject <letter:>` | Eject (unmount) disc |
| `disc mounts` | List currently mounted discs |

### MTDF Filesystem（4 条）

| 命令 | 说明 |
|------|------|
| `mtdf info <drive>:` | Show MTDF volume info |
| `mtdf list <drive>:` | List files at MTDF disc root |
| `mtdf check <drive>:` | Verify MTDF magic and structure |
| `mtdf label <drive>: <label>` | Set MTDF volume label |

### Virtualization (c2)（8 条）

| 命令 | 说明 |
|------|------|
| `tdir/tlist/ps` | List virtual processes and handles |
| `cdtask <pid\|handle>` | Switch process tree context |
| `tdel/tkill <pid\|handle>` | Terminate a process (recursive) |
| `tspawn <name> [mem] [cores]` | Spawn a virtual process |
| `taskmon/vmon` | Virtual hardware monitor (CPU/mem/disk) |
| `cpuaff set <cores>\|list\|reset\|cores <n>` | CPU affinity control |
| `memstat` | Memory sandbox status (2GB cap) |
| `memgrow <gb>` | Expand memory sandbox cap |

### Hardware (legacy)（5 条）

| 命令 | 说明 |
|------|------|
| `hardware` | Show REAL hardware info (legacy) |
| `mine` | Start MTY AFK-boost (idle score accrual) |
| `stopmine` | Stop MTY AFK-boost |
| `wallet` | Show MTY usage score |
| `systeminfo` | Show system information |

### Calculator (2.0)（6 条）

| 命令 | 说明 |
|------|------|
| `comp <expr>` | Evaluate expression (complex i, variables, user functions) |
| `comp /le` | Open expression-lab window for long multi-line input |
| `comp solve <eq[,eq..]> [for <v>]` | Equation / system / inequality solver (exact + numeric) |
| `comp sum(..) / diff / integrate / limit` | Infinite sums, products, calculus (needs sympy) |
| `comp x = 5 / f(x) = x^2` | Define variables / functions (comp list, comp clear <n>) |
| `comp plot <expr> [x=a..b]` | Render function graph in a plot window |

### Archive (1.3a2)（5 条）

| 命令 | 说明 |
|------|------|
| `mtz create <a.mtz> <f\|dir> [...]` | Create a compressed .mtz archive |
| `mtz list <a.mtz>` | List contents of a .mtz archive |
| `mtz extract <a.mtz> [dest]` | Extract a .mtz archive |
| `mtz add <a.mtz> <f\|dir> [...]` | Add entries to a .mtz archive |
| `mtz remove <a.mtz> <name>` | Remove an entry from a .mtz archive |

### LAN (1.3a2)（23 条）

| 命令 | 说明 |
|------|------|
| `lan start [port]` | Start the LAN file server (default 53210) |
| `lan stop` | Stop the LAN file server |
| `lan key <passphrase>` | Set pairing key (must match on both sides) |
| `lan share <path>` | Set the shared root folder |
| `lan connect <host> <port> <password>` | Connect to a peer (verify key) |
| `lan disconnect` | Forget the current peer |
| `lan status` | Show server / key / share / peer status |
| `lan peers` | List known peers |
| `cdlan [path]` | Change remote working dir (\ / .. .) |
| `lanpwd` | Show remote working dir |
| `landir [path]` | List remote dir |
| `lancopy <rsrc> <ldst>` | Copy file from peer to local |
| `lanput <lsrc> <rdst>` | Copy file from local to peer |
| `lancut <rsrc> <ldst>` | Copy from peer then delete remote source |
| `landel <rname>` | Delete file/dir on peer |
| `lanmkdir <rname>` | Make dir on peer |
| `lanmove <rsrc> <rdst>` | Rename/move on peer |
| `lanstat <rname>` | Show remote file info |
| `lanedit <rname>` | Fetch, edit locally, push back |
| `lan pair <host> [port]` | Verify key with a peer (low-level) |
| `lan dir <host> [port]` | List a peer's shared folder (low-level) |
| `lan get <host> <rfile> [lfile]` | Download (low-level) |
| `lan put <host> <lfile> [rfile]` | Upload (low-level) |

### Network & Web（14 条）

| 命令 | 说明 |
|------|------|
| `crawl <url>` | Crawl web page content |
| `mt <page>` | Browse MT-Backrooms wikidot page |
| `login <user> <pass>` | Login to Wikidot |
| `logout` | Logout from Wikidot |
| `checklogin` | Check login status |
| `status` | Show session status |
| `cookies` | Show session cookies |
| `headers` | Show HTTP headers |
| `cdweb <url>` | Navigate to web page |
| `webpwd` | Show current web page info |
| `webdir` | List web page elements |
| `webedit <id> [value]` | Interact with web element |
| `webedit js <code>` | Execute JS on current page |
| `webjs <code>` | Execute JS on current page (alias) |

### Audio（6 条）

| 命令 | 说明 |
|------|------|
| `play <file>` | Play audio file |
| `stopaudio` | Stop audio playback |
| `audioinfo <file>` | Show audio file info |
| `waveform <file>` | Generate waveform image |
| `spectrogram <file>` | Generate spectrogram image |
| `player [file]` | Open audio player |

### Development（1 条）

| 命令 | 说明 |
|------|------|
| `img <file>` | Open image viewer |

### System（4 条）

| 命令 | 说明 |
|------|------|
| `reboot` | Reboot system |
| `help [category]` | Show this help or category-specific help |
| `reload` | Reload mtc extensions from A:\ |
| `mtu list\|info\|install\|uninstall\|make` | Manage MTU packages installed from the floppy (A:) |

### Network（4 条）

| 命令 | 说明 |
|------|------|
| `jsweb <url>` | Open web page with JS support |
| `jsexec <code>` | Execute JS on current page |
| `jseval <expr>` | Evaluate JS expression |
| `jsclose` | Close JS page context |

### Web（5 条）

| 命令 | 说明 |
|------|------|
| `webjs` | JS exec OR ID-based element op (mirrors webedit) |
| `webjs <id>` | Operate element by webdir ID (= webedit <id>) |
| `webjs js <code>` | Run JS code (explicit) |
| `webjs <code>` | Run JS code (page.title, querySelector, etc.) |
| `cookies [list\|clear\|save\|load\|set]` | Cookie management (auto-saved by session) |

### Executable（1 条）

| 命令 | 说明 |
|------|------|
| `exec <file.exe> [args]` | Run real executable file |

### Registry（17 条）

| 命令 | 说明 |
|------|------|
| `mtr` | List current registry key (= mtrdir) |
| `mtrdir [path]` | List subkeys and values under a key |
| `cdmtr <path>` | Change current registry key (supports .././absolute) |
| `mtrpwd` | Show current registry path |
| `mkitem <name>` | Create a key (with intermediates) |
| `delitem <name>` | Delete a key and all its children |
| `renitem <old> <new>` | Rename a key |
| `mtrcopy/mtrcp <src> <dst>` | Copy a key (with children) or a value |
| `mtrcut/mtrmv <src> <dst>` | Move (cut) a key or a value |
| `setval <name> <type> <value> [--create]` | Set a value under current key (types: file/dir/switch/int/string/binary) |
| `getval <name>` | Get a value under current key |
| `delval <name>` | Delete a value under current key |
| `mtrtree [path]` | Show registry tree (depth=4) |
| `mtrexport <file>` | Export the whole registry to a file |
| `mtrimport <file>` | Import a registry file (replaces all) |
| `mtrpath` | Show the .mtr file location |
| `mtrhelp` | Show MTR command family help |

### Executable (mtEF)（3 条）

| 命令 | 说明 |
|------|------|
| `mtc <src.py> <dst.mtef>` | Compile Python source to mtEF binary |
| `mtc --batch-only <dst> <cmds>` | Compile batch-only mtEF |
| `tspawn <file.mtef> [mem] [cores] [args]` | Execute mtEF, spawns virtual process |

### 本版本补充说明

- 新增 **光盘（ISO）**：`disc\` 目录中的 ISO 由 `disc` 系列命令管理并可挂载到 M-Z 盘；启动时自动把 `mtdisc0m.iso` 挂载为 `M:`（默认盘），文件命令可直接使用 `M:\` 路径。
- `format` 支持 4 种文件系统：MTFS / MAT64 / MTRAW / MTDF。
- `del` 支持通配符（如 `del M:\*.tmp`）；`type`（别名 `cat`）显示文本文件；`mkfile`（别名 `touch`）创建空文件。
- 计算器 2.0：复数 `i`、变量与自定义函数、方程组/不等式求解、级数/微积分（需 sympy）、`comp plot` 绘图、`comp /le` 打开长表达式输入窗口。
- 别名：`kill`（=tdel/tkill）、`hwmon`（=taskmon/vmon）、`affinity`（=cpuaff）、`meminfo`（=memstat）。

---

## 8. source1.3a4.py（版本 1.3a4）

修改时间 11-01，大小 596 KB。在 1.3a2 基础上新增 install 命令：把 MT-DOS 系统包（WinPE + python38 + mt-dos）安装到真实硬盘并写入引导（技术预览，仅 BIOS/Legacy）。其余与 1.3a2 完全一致。

help 共列出 **142 条**命令（另有通用补充中的 3 条隐藏命令）：

### File System（11 条）

| 命令 | 说明 |
|------|------|
| `dir [path]` | List directory (dir M:\ , dir C:\docs , ...) |
| `cd <path>` | Change directory |
| `md/mkdir <name>` | Create directory |
| `rmdir/rd <dir> [/s]` | Remove directory (/s = recursive) |
| `ren/rename <old> <new>` | Rename file or directory |
| `copy <src> <dst>` | Copy file (supports cross-drive and wildcards) |
| `move/mv/cut <src> <dst>` | Move file or directory (cross-drive supported) |
| `del <file>` | Delete file (supports wildcards, e.g. del M:\*.tmp) |
| `type <file>` | Display text file contents (type M:\readme.txt) |
| `edit <file>` | Open text editor |
| `mkfile <name>` | Create empty file |

### Disk Management（13 条）

| 命令 | 说明 |
|------|------|
| `complist` | List all hardware (disks, optical/floppy drives, RAM, GPU) |
| `mkdisk <size_gb>` | Create new virtual disk |
| `partdisk <disk> <size> <letter>` | Partition a disk |
| `deldisk <disk>` | Delete entire disk |
| `delpart <letter>` | Delete a partition |
| `plug disk <size> [on <disk>]` | Plug in / extend disk |
| `plug ram <size>` | Plug in virtual RAM |
| `unplug <disk>` | Unplug disk (offline) |
| `unplug ram <id>` | Unplug RAM module |
| `rescan [disk]` | Rescan hardware / bring disk online |
| `format <drive:> /fs:MTFS\|MAT64\|MTRAW\|MTDF` | Format partition (4 fs types) |
| `wipe <file>` | Securely erase file (unrecoverable) |
| `mtraw info\|scan\|format` | MTRAW-specific operations |

### Optical Disc (ISO)（10 条）

启动时自动把 `disc\mtdisc0m.iso` 挂载为 `M:`（默认盘），文件命令可直接使用 `M:\` 路径。

| 命令 | 说明 |
|------|------|
| `disc list` | List all ISO files in disc\ |
| `disc info <name>` | Show disc info (MTDF or unknown) |
| `disc create <name> [label]` | Create new empty MTDF disc |
| `disc import <host_file> [as <name>]` | Copy external ISO into disc\ |
| `disc delete <name>` | Delete an ISO file |
| `disc format <name> [label]` | Re-format disc as MTDF (erases data) |
| `disc label <name> <new label>` | Set MTDF volume label |
| `disc insert <name> [letter:]` | Insert (mount) disc to drive M-Z |
| `disc eject <letter:>` | Eject (unmount) disc |
| `disc mounts` | List currently mounted discs |

### MTDF Filesystem（4 条）

| 命令 | 说明 |
|------|------|
| `mtdf info <drive>:` | Show MTDF volume info |
| `mtdf list <drive>:` | List files at MTDF disc root |
| `mtdf check <drive>:` | Verify MTDF magic and structure |
| `mtdf label <drive>: <label>` | Set MTDF volume label |

### Virtualization (c2)（8 条）

| 命令 | 说明 |
|------|------|
| `tdir/tlist/ps` | List virtual processes and handles |
| `cdtask <pid\|handle>` | Switch process tree context |
| `tdel/tkill <pid\|handle>` | Terminate a process (recursive) |
| `tspawn <name> [mem] [cores]` | Spawn a virtual process |
| `taskmon/vmon` | Virtual hardware monitor (CPU/mem/disk) |
| `cpuaff set <cores>\|list\|reset\|cores <n>` | CPU affinity control |
| `memstat` | Memory sandbox status (2GB cap) |
| `memgrow <gb>` | Expand memory sandbox cap |

### Hardware (legacy)（5 条）

| 命令 | 说明 |
|------|------|
| `hardware` | Show REAL hardware info (legacy) |
| `mine` | Start MTY AFK-boost (idle score accrual) |
| `stopmine` | Stop MTY AFK-boost |
| `wallet` | Show MTY usage score |
| `systeminfo` | Show system information |

### Calculator (2.0)（6 条）

| 命令 | 说明 |
|------|------|
| `comp <expr>` | Evaluate expression (complex i, variables, user functions) |
| `comp /le` | Open expression-lab window for long multi-line input |
| `comp solve <eq[,eq..]> [for <v>]` | Equation / system / inequality solver (exact + numeric) |
| `comp sum(..) / diff / integrate / limit` | Infinite sums, products, calculus (needs sympy) |
| `comp x = 5 / f(x) = x^2` | Define variables / functions (comp list, comp clear <n>) |
| `comp plot <expr> [x=a..b]` | Render function graph in a plot window |

### Archive (1.3a2)（5 条）

| 命令 | 说明 |
|------|------|
| `mtz create <a.mtz> <f\|dir> [...]` | Create a compressed .mtz archive |
| `mtz list <a.mtz>` | List contents of a .mtz archive |
| `mtz extract <a.mtz> [dest]` | Extract a .mtz archive |
| `mtz add <a.mtz> <f\|dir> [...]` | Add entries to a .mtz archive |
| `mtz remove <a.mtz> <name>` | Remove an entry from a .mtz archive |

### LAN (1.3a2)（23 条）

| 命令 | 说明 |
|------|------|
| `lan start [port]` | Start the LAN file server (default 53210) |
| `lan stop` | Stop the LAN file server |
| `lan key <passphrase>` | Set pairing key (must match on both sides) |
| `lan share <path>` | Set the shared root folder |
| `lan connect <host> <port> <password>` | Connect to a peer (verify key) |
| `lan disconnect` | Forget the current peer |
| `lan status` | Show server / key / share / peer status |
| `lan peers` | List known peers |
| `cdlan [path]` | Change remote working dir (\ / .. .) |
| `lanpwd` | Show remote working dir |
| `landir [path]` | List remote dir |
| `lancopy <rsrc> <ldst>` | Copy file from peer to local |
| `lanput <lsrc> <rdst>` | Copy file from local to peer |
| `lancut <rsrc> <ldst>` | Copy from peer then delete remote source |
| `landel <rname>` | Delete file/dir on peer |
| `lanmkdir <rname>` | Make dir on peer |
| `lanmove <rsrc> <rdst>` | Rename/move on peer |
| `lanstat <rname>` | Show remote file info |
| `lanedit <rname>` | Fetch, edit locally, push back |
| `lan pair <host> [port]` | Verify key with a peer (low-level) |
| `lan dir <host> [port]` | List a peer's shared folder (low-level) |
| `lan get <host> <rfile> [lfile]` | Download (low-level) |
| `lan put <host> <lfile> [rfile]` | Upload (low-level) |

### Network & Web（14 条）

| 命令 | 说明 |
|------|------|
| `crawl <url>` | Crawl web page content |
| `mt <page>` | Browse MT-Backrooms wikidot page |
| `login <user> <pass>` | Login to Wikidot |
| `logout` | Logout from Wikidot |
| `checklogin` | Check login status |
| `status` | Show session status |
| `cookies` | Show session cookies |
| `headers` | Show HTTP headers |
| `cdweb <url>` | Navigate to web page |
| `webpwd` | Show current web page info |
| `webdir` | List web page elements |
| `webedit <id> [value]` | Interact with web element |
| `webedit js <code>` | Execute JS on current page |
| `webjs <code>` | Execute JS on current page (alias) |

### Audio（6 条）

| 命令 | 说明 |
|------|------|
| `play <file>` | Play audio file |
| `stopaudio` | Stop audio playback |
| `audioinfo <file>` | Show audio file info |
| `waveform <file>` | Generate waveform image |
| `spectrogram <file>` | Generate spectrogram image |
| `player [file]` | Open audio player |

### Development（1 条）

| 命令 | 说明 |
|------|------|
| `img <file>` | Open image viewer |

### System（4 条）

| 命令 | 说明 |
|------|------|
| `reboot` | Reboot system |
| `help [category]` | Show this help or category-specific help |
| `reload` | Reload mtc extensions from A:\ |
| `mtu list\|info\|install\|uninstall\|make` | Manage MTU packages installed from the floppy (A:) |

### Network（4 条）

| 命令 | 说明 |
|------|------|
| `jsweb <url>` | Open web page with JS support |
| `jsexec <code>` | Execute JS on current page |
| `jseval <expr>` | Evaluate JS expression |
| `jsclose` | Close JS page context |

### Web（5 条）

| 命令 | 说明 |
|------|------|
| `webjs` | JS exec OR ID-based element op (mirrors webedit) |
| `webjs <id>` | Operate element by webdir ID (= webedit <id>) |
| `webjs js <code>` | Run JS code (explicit) |
| `webjs <code>` | Run JS code (page.title, querySelector, etc.) |
| `cookies [list\|clear\|save\|load\|set]` | Cookie management (auto-saved by session) |

### Executable（1 条）

| 命令 | 说明 |
|------|------|
| `exec <file.exe> [args]` | Run real executable file |

### Setup (1.3a3)（1 条）

| 命令 | 说明 |
|------|------|
| `install <宿主机包路径> [check\|force]` | Install MT-DOS system package (WinPE+python38+mt-dos) to its drive & write bootloader (tech preview, BIOS/Legacy only) |

### Registry（17 条）

| 命令 | 说明 |
|------|------|
| `mtr` | List current registry key (= mtrdir) |
| `mtrdir [path]` | List subkeys and values under a key |
| `cdmtr <path>` | Change current registry key (supports .././absolute) |
| `mtrpwd` | Show current registry path |
| `mkitem <name>` | Create a key (with intermediates) |
| `delitem <name>` | Delete a key and all its children |
| `renitem <old> <new>` | Rename a key |
| `mtrcopy/mtrcp <src> <dst>` | Copy a key (with children) or a value |
| `mtrcut/mtrmv <src> <dst>` | Move (cut) a key or a value |
| `setval <name> <type> <value> [--create]` | Set a value under current key (types: file/dir/switch/int/string/binary) |
| `getval <name>` | Get a value under current key |
| `delval <name>` | Delete a value under current key |
| `mtrtree [path]` | Show registry tree (depth=4) |
| `mtrexport <file>` | Export the whole registry to a file |
| `mtrimport <file>` | Import a registry file (replaces all) |
| `mtrpath` | Show the .mtr file location |
| `mtrhelp` | Show MTR command family help |

### Executable (mtEF)（3 条）

| 命令 | 说明 |
|------|------|
| `mtc <src.py> <dst.mtef>` | Compile Python source to mtEF binary |
| `mtc --batch-only <dst> <cmds>` | Compile batch-only mtEF |
| `tspawn <file.mtef> [mem] [cores] [args]` | Execute mtEF, spawns virtual process |

### 本版本补充说明

- `install` 源自 1.3a3 的 Setup 功能（程序内 systeminfo 显示 TerminalOS v1.3a3）。
- 别名：`kill`（=tdel/tkill）、`hwmon`（=taskmon/vmon）、`affinity`（=cpuaff）、`meminfo`（=memstat）。
- `format` 支持 4 种文件系统：MTFS / MAT64 / MTRAW / MTDF；`type/cat`、`mkfile/touch`、`del` 通配符与 1.3a2 相同。
