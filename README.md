# CangjieCorpus

本语料库聚焦仓颉编程语言，整合官方开发指南、API 文档、工具说明和补充示例，以 Markdown 格式保存，供学习、智能编码助手和 RAG（检索增强生成）应用使用。

当前按 **仓颉 1.2.0** 更新语言文档。各目录采用的文档来源与版本如下；鸿蒙文档按 `docs_cangjie` 的指定提交同步，其 SDK 适用范围以各篇文档说明为准。

## 语料库结构与来源

| 目录 | 内容 | 本次同步来源 | 状态 |
| --- | --- | --- | --- |
| [manual/source_zh_cn](manual/source_zh_cn) | 语言开发指南 | 本地 `cangjie_docs` 的 `docs/dev-guide/source_zh_cn`，提交 `25eaa89`（提交说明对应 `release/1.2`） | 已同步 |
| [tools/source_zh_cn](tools/source_zh_cn) | 包管理、调试、格式化、检查等工具指南 | 同一 `cangjie_docs` 提交的 `docs/tools/source_zh_cn` | 已同步 |
| [libs/std](libs/std) | 标准库 API 与示例 | 本地 `cangjie_runtime` 的 `stdlib/doc/libs/std`，提交 `a1db6277`（提交说明对应 `release/1.2`） | 已按源码 Markdown 完整同步 |
| [libs/stdx](libs/stdx) | 扩展库 API 与示例 | 本地 `cangjie_stdx` 的 `doc/libs_stdx`，提交 `243a4b6`（提交说明对应 `release/1.2`） | 已按源码 Markdown 完整同步 |
| [ohos/zh-cn](ohos/zh-cn) | 鸿蒙仓颉开发指南、API 参考与配套资源 | 本地 `docs_cangjie` 的 `zh-cn`，提交 `641feb3b` | 已按源码 Markdown 完整同步 |
| [extra](extra) | 项目自编的语言与集合操作速查说明 | 对照本次同步的开发指南和标准库文档核对 | 已修订，具体示例已通过 1.2.0 分类验证，见 [验证报告](EXTRA_VALIDATION.md) |

官方项目入口：[仓颉文档](https://gitcode.com/Cangjie/cangjie_docs)、[标准库与运行时](https://gitcode.com/Cangjie/cangjie_runtime)、[扩展库](https://gitcode.com/Cangjie/cangjie_stdx)、[OpenHarmony 仓颉文档](https://gitcode.com/openharmony/docs_cangjie)。上表记录实际导入来源及提交，各目录的适用版本以对应来源文档为准。

## 补充说明

`extra` 包含 Array、ArrayList、集合操作、函数、HashMap、HashSet、数字、Option、Rune、排序、String 和 Tuple 的使用说明。这些内容由项目维护，仅供学习研究参考，不代表官方标准实现；各篇附有本地参考文档链接。代码块既有完整程序，也有需要补充导入或上下文的片段，以及明确标注的错误示例。

std、stdx 和鸿蒙文档均直接从上表所列源码目录同步原始 Markdown 与配套资源，保留上游目录结构和链接，替换此前的 HTML 转换版本。manual 和 tools 同样直接同步源码 Markdown。由于本仓库的目录映射与上游不同，跨目录链接需按本仓库路径检查和适配。

后续同步应比较新增、修改和删除的文件，检查跨目录链接与资源引用，并记录来源版本。若用于 RAG，更新语料后还需重建或增量更新切分、向量与检索索引，清除已删除文档对应的旧条目。
