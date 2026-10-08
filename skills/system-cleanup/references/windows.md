# Windows 常见检查位置

路径通过环境变量或系统已知文件夹接口解析, 不假定用户目录和系统盘固定.

## 1. 用户级应用残留

- `%APPDATA%` 下的应用或厂商目录: 配置, 用户资料和数据库. 已卸载应用的专属残留默认可删除.
- `%LOCALAPPDATA%` 下的应用或厂商目录: 缓存, 日志, 配置和本地数据. 按应用处理, 不整体清空 `AppData`.
- 用户目录中的 `AppData\LocalLow`: 部分应用配置和数据, 游戏存档. 使用实际已知文件夹位置, 确认归属后处理.
- `%LOCALAPPDATA%\Packages`: 打包应用的配置, 缓存和数据. 核对包身份及安装状态, 优先使用系统机制处理残留.
- 用户主目录中的 `.应用名`, `.config`, `.cache`: 跨平台和命令行工具的配置, 数据, 缓存. 按应用查找, 不忽略隐藏项.

应用也可能在“文档”“保存的游戏”等已知文件夹中保存专属数据. 确认卸载后, 这些专属残留也可纳入清理, 清单中说明存档等数据会一并删除.

Windows 已知文件夹可能被重定向或同步, 应以实际位置为准, 避免无意扩大到共享或云端数据.

## 2. 安装位置和启动残留

- 安装位置: 检查系统和用户级安装记录, `Program Files`, `Program Files (x86)`, `%LOCALAPPDATA%\Programs` 及实际便携版位置.
- `%PROGRAMDATA%`: 可能保留共享配置, 服务数据和更新文件. 只处理已卸载应用的专属部分.
- 开始菜单和启动文件夹: 使用已知文件夹接口定位, 检查目标应用留下的快捷方式.
- 服务, 计划任务和启动项: 若目标组件仍在运行或启用, 先列出所需处理, 不只删除其文件.

注册表只检查与目标应用明确关联的卸载或启动残留. 不进行通用“注册表清理”, 应用卸载后也不能据此批量删除所有名称相似的键.

## 3. 临时文件, 日志和缓存

- `%TEMP%`, `%TMP%`: 已结束安装或任务的临时文件. 使用实际路径, 跳过占用项.
- `%LOCALAPPDATA%\CrashDumps`: 应用崩溃转储, 不再用于排障时清理.
- Windows Error Reporting 的用户或系统目录: 排队或归档的错误报告. 按实际位置和权限检查, 优先使用系统清理功能.
- 浏览器, IDE 和开发工具的缓存目录: 下载缓存, 索引, 构建缓存. 使用工具查询位置, 区分缓存与用户资料.
- Windows 临时文件和更新缓存: 系统维护残留, 使用“临时文件”“存储感知”等系统机制.

不手工删除 `WinSxS`, `Windows\Installer` 或驱动存储目录.

## 4. 开发工具缓存和残留

下列路径是常见默认值. 自定义安装, 环境变量和工具版本可能改变位置, 优先使用工具查询实际路径. 确认工具已卸载后, 其专属配置, 缓存和数据都按残留处理. 仍在使用时按以下分类清理.

### Node.js, 包管理器和前端工具

- npm: 缓存常见于 `%LOCALAPPDATA%\npm-cache`, 用 `npm config get cache` 查询. 缓存清理使用 npm 自带功能. 全局安装位置用 `npm config get prefix` 查询, 常见的 `%APPDATA%\npm` 是安装目录, 不是缓存.
- pnpm: 存储常见于 `%LOCALAPPDATA%\pnpm\store`, 用 `pnpm store path` 查询. 使用 pnpm 的存储清理功能处理未引用内容, 不直接清空仍在使用的存储.
- Yarn: 位置随版本变化, 可能在用户缓存目录或项目的 `.yarn\cache` 中. 使用该版本的查询和清理功能. 项目缓存可能用于离线或零安装工作流.
- Bun: 检查 `%USERPROFILE%\.bun\install\cache`. `.bun\bin` 是工具或全局安装入口, 不属于缓存.
- 项目: 检查 `node_modules\.cache`, `.next\cache`, `.nuxt`, `.turbo` 等. `node_modules` 是依赖安装结果, 清理前确认能够重新安装, 不混同于下载缓存.

### Python, 环境和包管理器

- pip: 缓存常见于 `%LOCALAPPDATA%\pip\Cache`, 用 `pip cache dir` 查询, 使用 pip 的缓存管理功能清理.
- uv: 缓存常见于 `%LOCALAPPDATA%\uv\cache`, 用 `uv cache dir` 查询, 使用 uv 的缓存清理功能处理.
- Poetry, Pipenv: 检查工具查询出的缓存和虚拟环境目录. Poetry 常见缓存位于 `%LOCALAPPDATA%\pypoetry\Cache`. 虚拟环境是已安装依赖, 不只是缓存.
- Conda: 通过 `conda info` 查询包缓存和环境位置. 区分下载包缓存与环境, 不默认执行会影响所有环境的清理.
- 项目: 检查 `__pycache__`, `.pytest_cache`, `.mypy_cache`, `.ruff_cache`, `.tox`, `.nox`, `build` 和测试输出. `.venv` 仅在项目已废弃或用户同意重建时清理.

### Rust 和 Go

- Rust: 检查实际 `CARGO_HOME`, 未设置时通常为 `%USERPROFILE%\.cargo`, 其中 `registry`, `git` 是下载相关缓存, `bin` 是已安装命令. `RUSTUP_HOME` 默认通常为 `%USERPROFILE%\.rustup`, 保存工具链, 使用 rustup 管理仍在使用的工具链. 项目 `target` 是构建输出.
- Go: 用 `go env GOCACHE GOMODCACHE GOPATH` 查询实际位置. 构建缓存常见于 `%LOCALAPPDATA%\go-build`, 模块缓存常见于 `%USERPROFILE%\go\pkg\mod`. 使用 Go 自带清理功能, `GOPATH` 下的源码和 `bin` 不并入缓存清理.

### Java, Gradle, Maven 和 .NET

- Gradle: 检查 `%USERPROFILE%\.gradle\caches` 和 `.gradle\wrapper\dists`. 先停止相关构建或守护进程, 保留仍需离线使用的分发包和依赖.
- Maven: 检查 `%USERPROFILE%\.m2\repository`. 它可能包含未发布的本地安装产物, 不能统一视为可重新下载. 配置和凭据通常另存于 `.m2` 中.
- NuGet: 用 `dotnet nuget locals all --list` 查询各类缓存, 按类别使用 NuGet 或 dotnet 清理. 默认全局包位置常见于 `%USERPROFILE%\.nuget\packages`, HTTP 缓存常见于 `%LOCALAPPDATA%\NuGet` 下.
- .NET: `%USERPROFILE%\.dotnet` 可能保存工具, 配置或用户级安装, 系统 SDK 通过安装管理工具处理. 项目 `bin`, `obj` 是构建产物, 但要保留仍需部署或调试的输出.

### 编辑器和 IDE

- VS Code: 检查 `%APPDATA%\Code`, `%USERPROFILE%\.vscode\extensions` 及相关缓存目录. 衍生版本使用不同目录. 配置, 扩展, 远程服务和 WSL 内的服务分别判断是否仍在使用.
- JetBrains: 常见位置为 `%APPDATA%\JetBrains`, `%LOCALAPPDATA%\JetBrains`, 旧版也可能使用家目录下的独立产品目录. 按产品和版本查找旧残留, 缓存与设置, 插件, 本地历史分开处理.
- 其他 IDE: 按实际配置查找索引, 插件, 日志和工作区数据. 工作区数据可能包含未保存内容, 不按索引缓存一并清空.

### 容器, SDK 和其他构建工具

- Docker, Podman: 通过对应工具盘点构建缓存, 镜像, 容器和卷. 桌面版或 WSL 中的持久化磁盘不是缓存, 不直接删除整块磁盘或引擎存储目录.
- Android: 检查 `%USERPROFILE%\.android`, 实际 Android SDK 目录及 `.gradle`. SDK 常见于 `%LOCALAPPDATA%\Android\Sdk`. 模拟器设备可能含用户数据, SDK 和系统镜像通过所属管理工具处理.
- CMake, C/C++: 检查明确属于构建输出的目录及 ccache, sccache 缓存. 缓存位置通过工具配置查询, 不把源码目录中的同名文件作为删除目标.

### Visual Studio 和 Windows 平台开发

- `%LOCALAPPDATA%\Microsoft\VisualStudio`, `%APPDATA%\Microsoft\VisualStudio`: 按实例和版本检查缓存, 配置及旧版本残留.
- 项目 `.vs`: 可能包含索引, 用户设置和工作区状态, 不能全部当作可再生成索引. 项目 `Debug`, `Release` 等输出目录按实际构建配置识别.
- Visual Studio 安装缓存, 工作负载和 Windows SDK: 由 Visual Studio Installer 或系统安装工具管理, 不手工删除共享 SDK 和安装器目录.
- WSL 内的开发工具: 按 Linux 参考文件检查缓存和卸载残留, 不以删除整个发行版虚拟磁盘代替内部清理.

## 5. 用户决定的内容

回收站, 下载文件, 备份, 虚拟机磁盘和容器持久化数据单独列出. 回收站使用系统机制处理, 不直接删除 `$Recycle.Bin`.

WSL 内部的垃圾按 Linux 规则检查. 不要把整个 WSL 发行版虚拟磁盘当作缓存删除, 删除内部文件也不一定立即缩小虚拟磁盘文件.
