# macOS 常见检查位置

## 1. 用户级应用残留

- `~/Library/Caches`: 应用缓存, 按应用目录处理.
- `~/Library/Preferences`: 偏好设置, 如应用专属 `.plist`. 已卸载应用的专属文件可删除.
- `~/Library/Application Support`: 配置, 数据库, 插件, 离线资源. 已卸载应用的专属目录默认可删除, 包括其中的数据.
- `~/Library/Containers`: 沙箱应用的配置和数据, 根据应用标识核对归属.
- `~/Library/Group Containers`: 同一组应用共享的数据. 只有关联应用都不再使用时才清理对应容器.
- `~/Library/Saved Application State`: 应用窗口和会话恢复信息. 已卸载应用的专属状态可删除.
- `~/Library/HTTPStorages`, `~/Library/WebKit`: 部分应用的网络和网页数据, 核对应用标识后处理.
- `~/Library/Logs`: 应用日志, 清理卸载残留或不再需要的历史日志.
- `~/Library/Logs/DiagnosticReports`: 崩溃和诊断报告, 不再用于排障时可清理.

先从应用名称, 厂商名和 bundle ID 查找候选. 名称相似不等于属于同一个应用, 尤其要检查同厂商其他产品是否还在使用.

## 2. 家目录隐藏项和命令行工具

macOS 上也可能存在 `~/.config`, `~/.cache`, `~/.local` 和 `~/.应用名`. 按应用查找配置, 数据, 缓存和用户级可执行入口.

开发工具常用 `~/.npm`, `~/.cargo`, `~/.gradle`, `~/.m2` 等目录. 先区分缓存和已安装工具, 不整体删除仍在使用的工具目录.

通过 Homebrew 或其他包管理器安装的内容优先由对应工具管理. Homebrew 缓存常见于 `~/Library/Caches/Homebrew`, 实际位置以工具查询结果为准.

## 3. 安装位置和启动残留

- 安装位置: 检查 `/Applications`, `~/Applications` 及实际使用的便携版, 命令行工具位置.
- 用户启动项: 检查 `~/Library/LaunchAgents` 中目标应用的专属项目. 仍在运行的后台组件需先处理运行状态.
- 共享或系统级残留: 按需检查 `/Library/Application Support`, `/Library/Preferences`, `/Library/LaunchAgents`, `/Library/LaunchDaemons`. 这些位置可能需要额外权限, 不能因桌面应用消失就判断后台组件已卸载.

## 4. 临时文件

- `$TMPDIR`: 检查已结束任务的临时残留. 它常位于 `/private/var/folders` 下, 只处理具体对象, 不整体清空上级目录.

## 5. 开发工具缓存和残留

下列路径是常见默认值. 自定义安装, 环境变量和工具版本可能改变位置, 优先使用工具查询实际路径. 确认工具已卸载后, 其专属配置, 缓存和数据都按残留处理. 仍在使用时按以下分类清理.

### Node.js, 包管理器和前端工具

- npm: 缓存常见于 `~/.npm`, 用 `npm config get cache` 查询. 缓存清理使用 npm 自带功能. 用户级全局安装位置用 `npm config get prefix` 查询, 不把全局包当作缓存.
- pnpm: 存储常见于 `~/Library/pnpm/store`, 用 `pnpm store path` 查询. 使用 pnpm 的存储清理功能处理未引用内容, 不直接清空仍在使用的存储.
- Yarn: 位置随版本变化, 可能在用户缓存目录或项目的 `.yarn/cache` 中. 使用该版本的查询和清理功能. 项目缓存可能用于离线或零安装工作流.
- Bun: 检查 `~/.bun/install/cache`. `~/.bun/bin` 是工具或全局安装入口, 不属于缓存.
- 项目: 检查 `node_modules/.cache`, `.next/cache`, `.nuxt`, `.turbo` 等. `node_modules` 是依赖安装结果, 清理前确认能够重新安装, 不混同于下载缓存.

### Python, 环境和包管理器

- pip: 缓存常见于 `~/Library/Caches/pip`, 用 `pip cache dir` 查询, 使用 pip 的缓存管理功能清理.
- uv: 缓存常见于 `~/.cache/uv`, 用 `uv cache dir` 查询, 使用 uv 的缓存清理功能处理.
- Poetry, Pipenv: 检查工具查询出的缓存和虚拟环境目录. Poetry 常见缓存位于 `~/Library/Caches/pypoetry`. 虚拟环境是已安装依赖, 不只是缓存.
- Conda: 通过 `conda info` 查询包缓存和环境位置. 区分下载包缓存与环境, 不默认执行会影响所有环境的清理.
- 项目: 检查 `__pycache__`, `.pytest_cache`, `.mypy_cache`, `.ruff_cache`, `.tox`, `.nox`, `build` 和测试输出. `.venv` 仅在项目已废弃或用户同意重建时清理.

### Rust 和 Go

- Rust: 检查 `${CARGO_HOME:-~/.cargo}` 下的 `registry`, `git` 缓存及项目 `target`. `bin` 是已安装命令, 配置和凭据不是缓存. `${RUSTUP_HOME:-~/.rustup}` 保存工具链, 使用 rustup 管理仍在使用的工具链.
- Go: 用 `go env GOCACHE GOMODCACHE GOPATH` 查询实际位置. 构建缓存常见于 `~/Library/Caches/go-build`, 模块缓存常见于 `~/go/pkg/mod`. 使用 Go 自带清理功能, `GOPATH` 下的源码和 `bin` 不并入缓存清理.

### Java, Gradle, Maven 和 .NET

- Gradle: 检查 `~/.gradle/caches` 和 `~/.gradle/wrapper/dists`. 先停止相关构建或守护进程, 保留仍需离线使用的分发包和依赖.
- Maven: 检查 `~/.m2/repository`. 它可能包含未发布的本地安装产物, 不能统一视为可重新下载. 配置和凭据通常另存于 `~/.m2` 中.
- NuGet: 用 `dotnet nuget locals all --list` 查询各类缓存, 按类别使用 NuGet 或 dotnet 清理. 默认全局包位置常见于 `~/.nuget/packages`.
- .NET: `~/.dotnet` 可能保存工具, 配置或用户级安装, SDK 也可能位于系统安装目录. 项目 `bin`, `obj` 是构建产物, 但要保留仍需部署或调试的输出.

### 编辑器和 IDE

- VS Code: 检查 `~/Library/Application Support/Code`, `~/.vscode/extensions`, `~/.vscode-server` 及相关缓存目录. 衍生版本使用不同目录. 配置, 扩展和远程服务分别判断是否仍在使用.
- JetBrains: 常见位置为 `~/Library/Application Support/JetBrains`, `~/Library/Caches/JetBrains`, `~/Library/Logs/JetBrains`, 旧版也可能使用独立产品目录. 按产品和版本查找旧残留, 缓存与设置, 插件, 本地历史分开处理.
- 其他 IDE: 按实际配置查找索引, 插件, 日志和工作区数据. 工作区数据可能包含未保存内容, 不按索引缓存一并清空.

### 容器, SDK 和其他构建工具

- Docker, Podman: 通过对应工具盘点构建缓存, 镜像, 容器和卷. 桌面版或虚拟机里的持久化磁盘不是缓存, 不直接删除整块磁盘或引擎存储目录.
- Android: 检查 `~/.android`, 实际 Android SDK 目录及 `~/.gradle`. SDK 常见于 `~/Library/Android/sdk`. 模拟器设备可能含用户数据, SDK 和系统镜像通过所属管理工具处理.
- CMake, C/C++: 检查明确属于构建输出的目录及 ccache, sccache 缓存. 缓存位置通过工具配置查询, 不把源码目录中的同名文件作为删除目标.

### Xcode 和 Apple 平台开发

- `~/Library/Developer/Xcode/DerivedData`: 构建和索引数据, 可重建, 说明重建成本.
- `~/Library/Developer/Xcode/Archives`: 发布归档和相关符号, 不作为普通缓存.
- `~/Library/Developer/Xcode` 下的设备支持和其他版本资源: 核对开发, 调试需要后清理, 不整体清空.
- `~/Library/Developer/CoreSimulator`: 通过 Xcode 或模拟器工具管理设备及数据. 旧设备可能保留测试数据, 模拟器运行时也可能由多个项目使用.
- Swift 包缓存: 区分项目 `.build`, Xcode 管理的依赖与工具查询出的全局缓存, 按所属工具处理.

## 6. 用户决定的内容

`~/.Trash`, 其他卷的回收站, 下载文件, 设备备份和 Time Machine 备份单独列出. 清空回收站使用系统机制. 未授权的 Time Machine 快照不纳入普通清理.
