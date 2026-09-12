# 执行环境

本技能不附带库, 渲染器或辅助脚本.

## 默认工具

| 任务 | 依赖 |
| --- | --- |
| Word 正文提取 | Pandoc |
| Word 对象定位, 新建和编辑 | Python 包 `python-docx` |
| Excel 读取, 新建和编辑 | Python 包 `openpyxl` |
| 大批量表格数据清洗 | Python 包 `pandas` |
| PowerPoint 文字提取 | Python 包 `markitdown[pptx]`, 提供 `markitdown` 命令 |
| PowerPoint 对象定位和编辑 | Python 包 `python-pptx` |
| PowerPoint 新建 | npm 包 `pptxgenjs` |
| OOXML 局部修改 | Python 标准库 `zipfile` 和包 `lxml` |
| 页面渲染和 PDF 导出 | LibreOffice 的 `soffice` 命令及所需字体 |

## 依赖管理

- 仅补齐本次所缺依赖, 保留已有可用环境. 不安装整套工具集或使用 Codex 私有包及缓存目录.
- 复用现有隔离环境. 无可用环境时, Python 包使用任务虚拟环境或 `uv run --with`, Node 包安装到任务目录. 不修改全局依赖.
- 脚本放在能解析依赖的任务目录, 不写进包安装目录.
- 缺少系统程序, 字体或安装权限时说明缺项及影响. 系统级安装先取得用户授权.
- 默认用 LibreOffice, 用户指定其他引擎时核对接口和导出行为, 不反复探测其他引擎.
- 已知用法直接使用. 参数或 API 有疑问时查对应版本的子命令 `--help` 或对象文档, 不先读整套手册或反复换库.

按需运行任务脚本:

```bash
uv run --with python-docx edit_document.py
```

缺少 `uv` 时使用任务虚拟环境, 无需为运行脚本安装新的环境管理器.
