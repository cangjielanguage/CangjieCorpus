# extra 示例验证报告

验证日期：2026-10-19。编译器：`Cangjie Compiler: 1.2.0 (cjnative)`，目标：`aarch64-apple-darwin`。SDK 来源为用户提供的本地 `cangjie` 目录。

## 范围与方法

检查 `extra` 的 12 篇文档，共 106 个代码块。84 个具体代码块进入编译验证：

- 76 个正常代码块补齐必要上下文后编译并运行成功。
- 6 个明确标注的编译错误示例按预期失败，并核对对应诊断。
- 2 个运行时错误示例编译成功，分别触发 `IndexOutOfBoundsException` 和 `NoneValueException`。
- 22 个代码块为 API 签名、单独的 import、带省略号或占位变量的语法模板，未作为独立程序编译。

片段验证时补入集合、排序、数学和转换导入，按示例上下文补充 `arr`、`list`、`s` 等变量。无入口的语句放入 `main` 并以 `()` 结束，避免最后一个表达式被当作入口返回值。函数定义片段检查可编译性，另添加空入口运行；这不等同于对这些函数的所有输入进行测试。未对全库官方示例执行编译验证。

另运行一组带结果断言的程序，验证 Array.map、切片共享存储、UTF-8 字节数与 Rune 数、二进制解析、HashMap 初始化索引、Option 默认值延迟求值及元组赋值；全部通过。

## 逐块结果

代码块编号按各文档中 `cangjie` 围栏出现顺序计数。

| 文档 | 代码块 | 结果 |
| --- | --- | --- |
| [ extra/Array.md ](extra/Array.md) | 1 | 编译及运行通过 |
| [ extra/Array.md ](extra/Array.md) | 4 | 编译及运行通过 |
| [ extra/Array.md ](extra/Array.md) | 5 | 编译及运行通过 |
| [ extra/Array.md ](extra/Array.md) | 6 | 编译及运行通过 |
| [ extra/ArrayList.md ](extra/ArrayList.md) | 2 | 编译及运行通过 |
| [ extra/ArrayList.md ](extra/ArrayList.md) | 3 | 编译及运行通过 |
| [ extra/ArrayList.md ](extra/ArrayList.md) | 4 | 预期运行时异常 |
| [ extra/ArrayList.md ](extra/ArrayList.md) | 7 | 编译及运行通过 |
| [ extra/ArrayList.md ](extra/ArrayList.md) | 11 | 编译及运行通过 |
| [ extra/ArrayList.md ](extra/ArrayList.md) | 12 | 编译及运行通过 |
| [ extra/ArrayList.md ](extra/ArrayList.md) | 13 | 编译及运行通过 |
| [ extra/ArrayList.md ](extra/ArrayList.md) | 15 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 2 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 3 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 4 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 5 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 6 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 8 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 9 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 12 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 15 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 16 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 17 | 编译及运行通过 |
| [ extra/Collection.md ](extra/Collection.md) | 18 | 编译及运行通过 |
| [ extra/Function.md ](extra/Function.md) | 1 | 预期编译失败 |
| [ extra/Function.md ](extra/Function.md) | 2 | 编译及运行通过 |
| [ extra/HashMap.md ](extra/HashMap.md) | 2 | 编译及运行通过 |
| [ extra/HashMap.md ](extra/HashMap.md) | 3 | 预期运行时异常 |
| [ extra/HashMap.md ](extra/HashMap.md) | 4 | 编译及运行通过 |
| [ extra/HashMap.md ](extra/HashMap.md) | 5 | 编译及运行通过 |
| [ extra/HashMap.md ](extra/HashMap.md) | 8 | 编译及运行通过 |
| [ extra/HashMap.md ](extra/HashMap.md) | 9 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 2 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 3 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 4 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 5 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 6 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 7 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 9 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 10 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 11 | 编译及运行通过 |
| [ extra/HashSet.md ](extra/HashSet.md) | 12 | 编译及运行通过 |
| [ extra/Numbers.md ](extra/Numbers.md) | 1 | 编译及运行通过 |
| [ extra/Numbers.md ](extra/Numbers.md) | 2 | 编译及运行通过 |
| [ extra/Numbers.md ](extra/Numbers.md) | 3 | 编译及运行通过 |
| [ extra/Numbers.md ](extra/Numbers.md) | 4 | 编译及运行通过 |
| [ extra/Numbers.md ](extra/Numbers.md) | 5 | 编译及运行通过 |
| [ extra/Numbers.md ](extra/Numbers.md) | 6 | 编译及运行通过 |
| [ extra/Option.md ](extra/Option.md) | 1 | 编译及运行通过 |
| [ extra/Option.md ](extra/Option.md) | 2 | 编译及运行通过 |
| [ extra/Option.md ](extra/Option.md) | 3 | 编译及运行通过 |
| [ extra/Option.md ](extra/Option.md) | 4 | 编译及运行通过 |
| [ extra/Rune.md ](extra/Rune.md) | 1 | 编译及运行通过 |
| [ extra/Rune.md ](extra/Rune.md) | 2 | 编译及运行通过 |
| [ extra/Rune.md ](extra/Rune.md) | 3 | 编译及运行通过 |
| [ extra/Rune.md ](extra/Rune.md) | 4 | 编译及运行通过 |
| [ extra/Sorting.md ](extra/Sorting.md) | 2 | 编译及运行通过 |
| [ extra/Sorting.md ](extra/Sorting.md) | 3 | 编译及运行通过 |
| [ extra/Sorting.md ](extra/Sorting.md) | 4 | 预期编译失败 |
| [ extra/Sorting.md ](extra/Sorting.md) | 5 | 编译及运行通过 |
| [ extra/Sorting.md ](extra/Sorting.md) | 6 | 编译及运行通过 |
| [ extra/Sorting.md ](extra/Sorting.md) | 8 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 1 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 2 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 3 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 4 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 5 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 6 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 7 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 8 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 9 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 10 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 11 | 编译及运行通过 |
| [ extra/String.md ](extra/String.md) | 12 | 编译及运行通过 |
| [ extra/Tuple.md ](extra/Tuple.md) | 1 | 编译及运行通过 |
| [ extra/Tuple.md ](extra/Tuple.md) | 2 | 预期编译失败 |
| [ extra/Tuple.md ](extra/Tuple.md) | 3 | 预期编译失败 |
| [ extra/Tuple.md ](extra/Tuple.md) | 4 | 编译及运行通过 |
| [ extra/Tuple.md ](extra/Tuple.md) | 5 | 预期编译失败 |
| [ extra/Tuple.md ](extra/Tuple.md) | 6 | 编译及运行通过 |
| [ extra/Tuple.md ](extra/Tuple.md) | 7 | 编译及运行通过 |
| [ extra/Tuple.md ](extra/Tuple.md) | 8 | 编译及运行通过 |
| [ extra/Tuple.md ](extra/Tuple.md) | 9 | 预期编译失败 |
| [ extra/Tuple.md ](extra/Tuple.md) | 10 | 编译及运行通过 |

## 未独立编译的签名与模板

- `Array_2`（文档原代码块编号）。
- `Array_3`（文档原代码块编号）。
- `ArrayList_1`（文档原代码块编号）。
- `ArrayList_5`（文档原代码块编号）。
- `ArrayList_6`（文档原代码块编号）。
- `ArrayList_8`（文档原代码块编号）。
- `ArrayList_9`（文档原代码块编号）。
- `ArrayList_10`（文档原代码块编号）。
- `ArrayList_14`（文档原代码块编号）。
- `Collection_1`（文档原代码块编号）。
- `Collection_7`（文档原代码块编号）。
- `Collection_10`（文档原代码块编号）。
- `Collection_11`（文档原代码块编号）。
- `Collection_13`（文档原代码块编号）。
- `Collection_14`（文档原代码块编号）。
- `HashMap_1`（文档原代码块编号）。
- `HashMap_6`（文档原代码块编号）。
- `HashMap_7`（文档原代码块编号）。
- `HashSet_1`（文档原代码块编号）。
- `HashSet_8`（文档原代码块编号）。
- `Sorting_1`（文档原代码块编号）。
- `Sorting_7`（文档原代码块编号）。

## 文档修订

- 修正 Option 输出注释中的 `None` 大小写。
- 数字位运算示例同时标明实际十进制输出与对应二进制值。
- 更新各篇验证状态及 README；保留反例及其错误说明。
- 清理 Sorting 文档的行尾空格。
