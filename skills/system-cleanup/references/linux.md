# Linux 常见检查位置

## 1. 家目录中的隐藏项

先查看 `~` 下的隐藏文件和目录, 再按应用检查归属. 这里既有卸载残留, 也有仍在使用的配置, 工具和数据.

### XDG 目录

- `~/.config`: 应用配置, 自启动项. 已卸载应用的专属子目录和配置文件可删除.
- `~/.cache`: 应用缓存, 下载缓存, 缩略图. 按应用处理, 不整体清空.
- `~/.local/share`: 应用数据, 数据库, 插件, 桌面入口. 已卸载应用的专属残留可删除.
- `~/.local/state`: 日志, 历史记录, 会话和恢复状态. 已卸载应用的专属残留可删除.
- `~/.local/bin`: 用户安装的程序, 脚本和链接. 核对归属, 移除已卸载应用留下的入口.
- `~/.local/lib`: 用户安装的库, Python 包等. 属于已安装依赖时由对应工具管理, 不按普通残留清理.

还要检查 `~/.local/share/applications`, `~/.local/share/icons` 和 `~/.config/autostart` 中的应用专属文件. 桌面入口失效只能作为线索, 应核对其指向的程序是否真的已卸载.

### 应用自己的隐藏目录和文件

常见候选包括:

- 浏览器: `~/.mozilla`, `~/.cache/mozilla`, 以及其他浏览器在 `.config` 下的目录. 可能包含多个产品或配置档案, 按实际应用细分.
- 编辑器和 IDE: `~/.vscode`, `~/.vscode-server`, `~/.config/Code`, 以及 JetBrains 的配置, 缓存和数据目录. 本地编辑器与远程服务可能分别使用, 不能只检查桌面程序.
- 开发工具: `~/.npm`, `~/.bun`, `~/.cargo`, `~/.rustup`, `~/.gradle`, `~/.m2`, `~/.nuget`. 既可能有缓存, 也可能有已安装工具和依赖.
- Python 工具: `~/.ipython`, `~/.jupyter`, `~/.conda`. 区分缓存, 配置, 环境和用户内容.
- 容器工具: `~/.docker`, `~/.config/containers`, `~/.local/share/containers`. 可能含凭据和持久化数据, 确认关联工具及服务是否仍在使用.
- 其他应用: `~/.应用名`, `~/.厂商名`, `~/.应用名rc`, `~/.应用名.conf`. 名称用于查找线索, 核对归属后处理.

上述名称只是示例. 实际盘点还应检查未列出的隐藏项, 用目录名, 安装信息和必要的少量元数据识别所属应用.

## 2. Flatpak, Snap 和手动安装

- Flatpak: `~/.var/app/<应用 ID>` 中可能保留配置, 缓存和数据. 确认应用在用户级和系统级都已卸载后, 其专属目录默认可删除.
- Snap: `~/snap/<应用名>` 中可能保留用户数据和多个修订版目录. 核对安装状态后处理, 快照通过 Snap 工具管理.
- 手动或便携安装: 检查用户指定位置, 以及 `~/Applications`, `~/opt`, `~/.local/opt` 等实际存在的目录. 应用可能仍在这些位置, 不要仅凭包管理器没有记录判定卸载.
- 用户服务: 检查 `~/.config/systemd/user` 中已卸载应用的专属单元和启用链接. 服务仍在运行时, 先报告需要停用, 不直接当作普通文件删除.

## 3. 临时文件, 日志和包缓存

- `$TMPDIR`, `/tmp`, `/var/tmp`: 已结束安装或任务的临时残留. 按来源处理, 避开活动文件, 锁和套接字.
- 应用日志目录, `/var/log`: 历史日志和崩溃报告. 保留排障所需记录, 系统日志优先使用管理工具.
- `/var/cache` 下的包管理器目录: 下载包, 仓库缓存. 使用对应包管理器的缓存清理功能, 不默认卸载软件.
- 项目中的构建目录: 构建结果, 测试缓存, 覆盖率输出. 确认可重建, 不把目录名当作唯一依据.

## 4. 开发工具缓存和残留

下列路径是常见默认值. 自定义安装, 环境变量和工具版本可能改变位置, 优先使用工具查询实际路径. 确认工具已卸载后, 其专属配置, 缓存和数据都按残留处理. 仍在使用时按以下分类清理.

### Node.js, 包管理器和前端工具

- npm: 缓存常见于 `~/.npm`, 用 `npm config get cache` 查询. 缓存清理使用 npm 自带功能. 用户级全局安装位置用 `npm config get prefix` 查询, 不把全局包当作缓存.
- pnpm: 存储常见于 `~/.local/share/pnpm/store`, 用 `pnpm store path` 查询. 使用 pnpm 的存储清理功能处理未引用内容, 不直接清空仍在使用的存储.
- Bun: 检查 `~/.bun/install/cache`. `~/.bun/bin` 是工具或全局安装入口, 不属于缓存.
- 项目: 检查 `node_modules/.cache`, `.next/cache`, `.nuxt`, `.turbo` 等. `node_modules` 是依赖安装结果, 清理前确认能够重新安装, 不混同于下载缓存.

### Python, 环境和包管理器

- pip: 缓存常见于 `~/.cache/pip`, 用 `pip cache dir` 查询, 使用 pip 的缓存管理功能清理.
- uv: 缓存常见于 `~/.cache/uv`, 用 `uv cache dir` 查询, 使用 uv 的缓存清理功能处理.
- Poetry, Pipenv: 检查工具查询出的缓存和虚拟环境目录. Poetry 常见缓存位于 `~/.cache/pypoetry`. 虚拟环境是已安装依赖, 不只是缓存.
- Conda: 通过 `conda info` 查询包缓存和环境位置. 区分下载包缓存与环境, 不默认执行会影响所有环境的清理.
- 项目: 检查 `__pycache__`, `.pytest_cache`, `.mypy_cache`, `.ruff_cache`, `.tox`, `.nox`, `build` 和测试输出. `.venv` 仅在项目已废弃或用户同意重建时清理.

### Rust 和 Go

- Rust: 检查 `${CARGO_HOME:-~/.cargo}` 下的 `registry`, `git` 缓存及项目 `target`. `bin` 是已安装命令, 配置和凭据不是缓存. `${RUSTUP_HOME:-~/.rustup}` 保存工具链, 使用 rustup 管理仍在使用的工具链.
- Go: 用 `go env GOCACHE GOMODCACHE GOPATH` 查询构建缓存, 模块缓存和工作目录, 使用 Go 自带清理功能. `GOPATH` 下的源码和 `bin` 不并入缓存清理.

### Java, Gradle, Maven 和 .NET

- Gradle: 检查 `~/.gradle/caches` 和 `~/.gradle/wrapper/dists`. 先停止相关构建或守护进程, 保留仍需离线使用的分发包和依赖.
- Maven: 检查 `~/.m2/repository`. 它可能包含未发布的本地安装产物, 不能统一视为可重新下载. 配置和凭据通常另存于 `~/.m2` 中.
- NuGet: 用 `dotnet nuget locals all --list` 查询各类缓存, 按类别使用 NuGet 或 dotnet 清理. 默认全局包位置常见于 `~/.nuget/packages`.
- .NET: `~/.dotnet` 可能是 SDK, 工具和安装目录, 不是普通缓存. 项目 `bin`, `obj` 是构建产物, 但要保留仍需部署或调试的输出.

### 编辑器和 IDE

- VS Code: 检查 `~/.config/Code`, `~/.cache` 中相关目录, `~/.vscode/extensions` 和 `~/.vscode-server`. 衍生版本使用不同目录. 配置, 扩展和远程服务分别判断是否仍在使用.
- JetBrains: 常见位置为 `~/.config/JetBrains`, `~/.cache/JetBrains`, `~/.local/share/JetBrains`. 按产品和版本查找旧残留, 缓存与设置, 插件, 本地历史分开处理.
- 其他 IDE: 按实际配置查找索引, 插件, 日志和工作区数据. 工作区数据可能包含未保存内容, 不按索引缓存一并清空.

### 容器, SDK 和其他构建工具

- Docker, Podman: 通过对应工具盘点构建缓存, 镜像, 容器和卷. 使用有明确范围的清理功能, 不直接删除引擎存储目录. 容器卷不是缓存.
- Android: 检查 `~/.android`, 实际 Android SDK 目录及 `~/.gradle`. 模拟器设备可能含用户数据, SDK 和系统镜像通过所属管理工具处理.
- CMake, C/C++: 检查明确属于构建输出的目录及 ccache, sccache 缓存. 缓存位置通过工具配置查询, 不把源码目录中的同名文件作为删除目标.

## 5. 用户决定的内容

- 回收站: `$XDG_DATA_HOME/Trash`, 默认 `~/.local/share/Trash`. 其他文件系统可能有独立回收站, 通过回收站工具处理.
- 下载目录: 使用实际配置的下载目录, 不假定名称一定是 `Downloads`.
- 备份, 旧安装包, 虚拟机磁盘和容器卷: 单独列出, 不并入常规缓存清理.

## 卸载残留示例

确认 `foo` 已卸载后, 可以将 `~/.config/foo`, `~/.cache/foo`, `~/.local/share/foo`, `~/.local/state/foo`, `~/.foo` 及其专属启动入口一起列入删除清单. 实际目录可能使用厂商名, 应用 ID 或旧名称, 应一并查找.
