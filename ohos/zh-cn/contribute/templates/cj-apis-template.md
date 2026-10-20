# API接口说明模板（仓颉版）

## 总体写作说明

> **说明：**
>
> 所有的写作说明，在完成写作后，都要删除。

| 序号 | 说明项 | 细则 |
| --- | ----- | ---- |
|1|客户化写作基本要求| **写作中，请变身开发者，对于开发者使用该API时所需的使用场景、参数选取原则、开发建议/经验、示例等信息进行清晰描述，达到指导开发者顺利使用本API进行开发的目标。** |
|2|上传路径|文档路径：<br>docs_cangjie/zh-cn/application-dev/reference/xxx（可能为XxxKit、arkui-cj、ArkData等）<br>图片路径：<br>API参考的公共章节：docs_cangjie/zh-cn/application-dev/reference/figures<br>API各模块：docs_cangjie/zh-cn/application-dev/reference/xxx/figures<br>并在markdown文件中通过路径!\[](figures/xxx.png)引用。|
|3|文件命名|一个package对应一篇API文档，文件名称为`cj-apis-包名ohos后面的部分.md`，全小写，包名中的`.`使用`-`代替。<br>例如，**ohos.multimedia.audio**包对应的文档命名为**cj-apis-multimedia-audio.md** |
|4|目录修改|增删文件时，需要修改对应的`README`和`website`，即`docs_cangjie/zh-cn/application-dev/reference/XxxKit/README_zh.md`和`docs_cangjie/zh-cn/application-dev/website.md`。 |
|5|文档结构|- 导入模块<br>- 权限列表（如不涉及请删除）<br>- 使用说明<br>- API（const、let/var、func、interface、class、struct、enum、type...）<br> API按上述类型顺序展示，同类型的按API字母序，请注意排列。 |
|6|示例代码规范|参考[示例规范](#示例规范) |
|7|链接写法|格式：\[链接文字](链接内容)<br>跨文件夹链接：\[指南](../../xxx/xxx.md)，一个`../`表示上移一层文件夹。<br>页面内链接：\[class A](#class-a)，链接文字和需要链接到的标题尽量保持一致，全小写无特殊符号无标签。**注意**：如果文中提到仓颉自定义类型，均需添加链接。 |
|8|废弃API |如果源码中API对应的`APILevel`注解中有`deprecated`字段，在文档对应标题结尾加上`<sup>(deprecated)</sup>`，并在功能描述结尾加上注意事项：<br>> \**注意：**<br>><br>> 从xx版本开始废弃不再使用 / 未来版本即将废弃, 可使用xxx替代。 |
|9|说明/告警格式|\> \*\*说明：**<br>><br>> - xxx<br>> - xxx（如果只有一条内容，去掉无序列表-）|
|10|泛型类型加`\`转义符|API类型若为泛型，形如`A<B>`、`A<B<C>>`，<>中的内容在markdown文本中不会显示，请在<前加转义符`\`，正确写法为`A\<B>`、`A\<B\<C>>`。|

## cj.d标签与文档字段的对应关系

在cj.d源码中，公开的API均添加了自定义注解APILevel，其中涉及的字段与文档描述字段的对应关系详见下表。

> 以下字段均需下沉到每一个API中。

| APILevel的字段 | 含义 | 文档字段 |
| ---------- | ---- | ------- |
| since | 起始版本 | \*\*起始版本：** xx |
| syscap | 系统能力 | \*\*系统能力：** SystemCapability.xxx.xxx |
| permission | 需要权限 |  1. 如果仅涉及一个权限，格式：<br>    **需要权限**：ohos.permission.xxxx   <br>2. 如果该接口涉及多个权限，则采用“和、或”进行分割，格式：<br>    **需要权限**：ohos.permission.A 和 ohos.permission.B<br>    **需要权限**：ohos.permission.A 或 ohos.permission.B <br>3. 涉及版本变更时，“需要权限”后跟当前版本的权限要求，历史版本的权限要求换行按列表描述，样例：<br>**需要权限**：ohos.permission.A <br>- 在API (y-1)时，需要申请权限ohos.permission.A和B。<br>- 从API y开始，仅需申请ohos.permission.A。<br>4. 仅在某些固定场景下，需要申请权限。“需要权限”后跟cj.d中的permission保持一致，再补充情况说明，分为两类情况，当情况较为简单时，可采用括号补充描述；当情况较为复杂时，换行描述。<br>样例1：<br> **需要权限**：ohos.permission.A（仅当创建窗口类型为AA时需要申请）<br>样例2：<br> **需要权限**：ohos.permission.A<br>- 当应用处于xx情况时，需要同步申请ohos.permission.B。<br>- 当应用处于yy情况时，无需申请任何权限。|

## 示例规范

所有的示例代码采用代码块的样式，并标记开发语言。

仓颉语言标注`cangjie`、ArkTS标注`ts`、C语言标注`c`、C++语言标注`cpp`。

```cangjie
// 1. 所有的示例代码需要进行自检，确保运行结果符合预期。
// 2. 不能出现缺符号、变量前后不一致等低错。
// 3. 所有使用到的变量要进行声明。
// 4. 接口传参异常时，需验证代码能否捕获错误，抛出对应错误码。即：如果API中包含错误码描述，示例中应使用try-catch捕获错误信息；如果不涉及错误码，则正常添加示例代码即可。

// 5. 不允许直接写参数名，必须是可使用、易替代的实际用例。如果非用户自定义填写，需通过注释进行说明。
// 例如：let result = xxx.createExample(parameterOne); // parameterOne由扫描自动获取

// 6. 示例中不允许出现硬编码。如涉及，需将硬编码换成显示占位符：
// let test_passwd = YOUR_PASSWD // 请输入密钥

// 7. 示例中不允许出现真实的三方网址。如涉及，需使用"exmple.com"代替。

// 8. 注释要精简、突出要点。在中文文档中，注释需使用中文（常见术语或参数名除外）。需提供注释的典型场景还有：
//   - 当代码不能说明变量命名的具体含义，或不能说明代码逻辑时，必须提供注释。
//   - 涉及到复杂算法或特殊语法时，必须提供注释。

// 9. 示例上方需添加示例自动看护标签。
//   - <!-- compile -->：仅编译检查，期望编译成功。
//   - <!-- compile.error -->：仅编译检查，期望编译失败（反例）。
//   - <!-- run -->：编译且运行检查，期望编译且运行成功。
//   - <!-- verify -->：验证结果检查，结果使用`text`呈现，期望示例结果和实际结果一致。
//   - <!-- code_check_manual -->：示例无法被自动编译运行，需手动验证。
//   - <!-- code_no_check -->：伪代码，无需编译运行。

<!--compile-->

try {
    ...
} catch (e: BusinessException) {
    AppLog.error("ErrorCode: ${e.code}, ErrorMessage: ${e.message}")
}
```

# 文档标题

> *写作说明*
>
> 1. **文档标题**：`包名（功能）`，要求使用中文短语概括本模块功能；但如果部分概念术语使用英文更便于开发者理解，可以直接使用。如Ability、SIM卡管理等。
> 2. **标题层级**：
>     - 二级标题：package中的全局API。如：const、var/let、function、interface、class、struct、enum、type等；
>     - 三级标题：全局API的子成员。如：class下的const、var、let、init、function等。
> 3. 每篇API文档以包为单元进行撰写，子包和父包应当分为两个文档。

<!--Del-->
> **说明：**
>
> 当前为Beta阶段。
<!--DelEnd-->

模块描述。此处对该模块的定义、功能、使用场景、使用建议进行描述，采用如下固定句式。

**（模块介绍，可选）** xxx是xxx。

**（功能描述，必选）** 提供xxx能力，包括xxx、xxx等，用于xxx。（当模块名不够语义化时，推荐此句式）/xxx模块，用于xxx、xxx。（当模块名已经表达了清晰的语义时，推荐此句式）

**（使用场景，可选）** 当需要xxx时，使用本模块API xxx。

**（使用建议或注意事项，可选）** 本模块API可与xxx联合使用，以提升开发效率……。

**举例1**：“后台任务管理模块”的模块描述示例

```text
本模块提供后台任务管理能力。

当应用或业务模块处于后台（无可见界面）时，如果有需要继续执行或者后续执行的业务，可基于业务类型，申请短时任务延迟挂起（Suspend），或长时任务避免进入挂起状态。
```

**举例2**：“拨打电话模块”的模块描述示例

```text
本模块提供呼叫管理能力，包括拨打电话、跳转到拨号界面、获取通话状态、格式化电话号码等。

如需订阅通话状态，请使用observer.on('callStateChange')。
```

**举例3**：“分布式数据管理模块”的模块描述示例

```text
分布式数据管理模块为应用程序提供不同设备间数据库的分布式协同能力。

通过调用分布式数据各个接口，应用程序可将数据保存到分布式数据库中，并可对分布式数据库中的数据进行增加、删除、修改、查询、同步等操作。
```

## 导入模块

> *写作说明*
>
> 必选，从开发者角度出发，写kit粒度的导入。

```cangjie
import kit.xxx.*
```

## 权限列表

> *写作说明*
>
> 可选，如果没有需要的权限可删除此二级标题。

ohos.permission.xxx

ohos.permission.xxx

## 使用说明

> *写作说明*
>
> 给出使用上的特殊要求、限制或情况。
> 可选，如果不涉及可删除此二级标题。
> 如果本文档涉及API示例，则需提供以下内容：

API示例代码使用说明：

- 若示例代码首行有“// index.cj”注释，表示该示例可在仓颉模板工程的“index.cj”文件中编译运行。
- 若示例需获取[Context](../../application-dev/reference/AbilityKit/cj-apis-app-ability-ui_ability.md#class-context)应用上下文，需在仓颉模板工程中的“main_ability.cj”文件中进行配置。

上述示例工程及配置模板详见[仓颉示例代码说明](../../application-dev/reference/cj-development-intro.md#仓颉示例代码说明)。

## const XXX

> *写作说明*
>
> 1. 可选，如果没有`全局常量`可删除。如果有多个常量，请分多个二级标题内容描述，并按字母序展示。
> 2. 需给出的内容为：签名、功能、类型、需要权限（可选）、系统能力、起始版本。

```cangjie
public const XXX: Type = DEFAULT
```

**功能：** xxx。

**类型：** [Type]()。

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx

## let xxx

> *写作说明*
>
> 1. 可选，如果没有`全局变量`可删除。如果有多个变量，请分多个二级标题内容描述，并按字母序展示。
> 2. 需给出的内容为：签名、功能、类型、需要权限（可选）、读写能力、系统能力、起始版本。

```cangjie
public let XXX: Type
```

**功能：** xxx。

**类型：** [Type]()。

**读写能力：** 只读/可读写（var是可读写，let是只读）

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx

## var xxx

参考[let](#let-xxx)

## static func xxx(Type1, Type2, ...)

参考[func](#func-xxxtype1-type2-)

## func xxx(Type1, Type2, ...)

> *写作说明*
>
> 1. 可选，如果没有`全局函数`可删除。如果有多个函数，请分多个二级标题内容描述，并按字母序展示。
>
> 2. 二级标题为`func + 函数名称 + (各入参类型，与签名中顺序一致)`。如无参也需加`()`。
>
> 3. 需给出的内容为：签名、功能、需要权限（可选）、系统能力、起始版本、参数（若涉及）、返回值（若涉及）、异常（若涉及）、示例。
>
> 4. **方法描述**：对方法实现的功能进行描述，包括其使用的前提条件（*如：在xx方法调用后才能调用、需要确保网络已连接……*）、使用之后的影响（*如：调用该接口后再进行xx将不起效*）等。
>
> 5. **表格内换行**：在表格中，换行采用特殊标记\<br>；在普通正文中描述中，增加空行即代表换行。
>
> 6. **参数表格**：参数顺序、参数名、参数类型应严格与签名中保持一致；根据签名中参数是否有形如`= DEFAULT`的默认值来判断是否必填（无=为必填，默认值列填`-`；反之非必填，默认值列填=后的默认值）；根据签名中参数名后是否有`!`来判断是否为命名参数，有则在“说明”列开头加上**命名参数。**
>
> 7. 示例为必选项，需严格遵守[示例规范](#示例规范)。

```cangjie
public func funcName(param1: Type1, param2!: Type2 = DEFAULT, param3: ?Type3): ReturnType
```

**功能：** xxx。

**需要权限：** ohos.permission.xxx

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx

**参数：**

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
| ----- | --- | ---- | --- | --- |
| param1 | Type1 | 是 | - | 参数描述（包括参数的含义与用途、使用场景、选取建议、参数间关联关系等）。另需包含以下内容：<br>1. 参数取值说明（包括取值范围、单位、默认值、取值原则或建议值；<br>2. 边界值涉及限制/异常时，需讲明具体场景；<br>3. 如果有固定格式，需要给出格式样例，尤其是URI）；<br>4. 非必填参数，或默认值为None时，需说明不填写该参数或为None的含义或后果。<br>5. 自定义类型需要进行建链说明。 |
| param2 | Type2 | 否 | DEFAULT | **命名参数。** 参数描述。 |
| param3 | ?Type3 | 是 | - | 参数描述。 |

**返回值：**

| 类型 | 说明 |
| --- | --- |
| ReturnType | 返回值描述。需包含以下内容：<br>1. 取到返回值之后，可以用来做什么。<br>2. 返回值如果可枚举，需枚举说明返回值意义。<br>3. 返回值如果为某个具体值/格式，需和实际实现保持一致。  |

**异常：**

- XxxException：对应错误码如下表，详见[xxx错误码]()。*（链接到对应模块的错误码参考文档）*

  | 错误码ID | 错误信息 |
  | ------- | ------- |
  | xxxx  | The parameter is invalid. |
  | xxxx  | ... |

**示例：**

```cangjie

try {
    ...
} catch (e: BusinessException) {
    AppLog.error("ErrorCode: ${e.code}, ErrorMessage: ${e.message}")
}
```

## interface XXX

> *写作说明*
>
> 1. 可选，如果没有可删除。如果有多个interface，请分多个二级标题内容描述，并按字母序展示。
>
> 2. 此二级标题需给出的内容为：签名、功能、需要权限（可选）、系统能力、起始版本、父类型（若涉及）、示例。
>
> 3. **签名**：interface的签名需给出所有`prop`和成员`func`（interface特殊性：代码中interface的成员默认都是public，所以就算子成员源码中没有public关键字，也是默认对外的，需要提供文档）。
>
> 4. interface中的每个prop、function单独新建三级标题，顺序为prop、func，同类型内部按字母序展示。
>
> 5. **注意可见性**：若该interface继承的包可见性为非public，则该interface不应当体现在文档中。

```cangjie
public interface XXX {
    prop property1: Type
    func xx(param: Type1): Type2
}
```

**功能：** interface描述。如果有使用限制，需要在此说明。例如，是否有前提条件，是否需要通过什么方法先构造一个实例等。

**需要权限：** ohos.permission.xxx

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx

### prop property1

> *写作说明*
>
> 1. 可选，如果没有属性可删除此二级标题。
>
> 2. 类型如果为自定义类型，需要建立链接到对应的interface或enum中。
>
> 3. 需给出的内容为：签名、功能、需要权限（若涉及）、系统能力、起始版本。
>
> 4. **读写能力**：根据签名中是否有mut关键字来判断，有mut为可读写，反之为只读。

```cangjie
public mut prop xxx: Type
```

**功能：** ……。

**类型：** [Type]()

**读写能力：** 只读/可读写

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx

### func xx(Type1)

参考[func](#func-xxxtype1-type2-)

## class XXX

> *写作说明*
>
> 1. 可选，如果没有可删除。如果有多个class，请分多个二级标题内容描述，并按字母序展示。
>
> 2. 此二级标题需给出的内容为：签名、功能、需要权限（可选）、系统能力、起始版本、父类型（若涉及）、示例。
>
> 3. **签名**：class的签名需给出所有公开常量`const`、变量`let/var`和构造函数`init`。
>
> 4. class中的每个const、let/var、function单独新建三级标题，顺序为const、let、var、init、func等，同类型按字母序排序展示。
>
> 5. **注意可见性**：若该class继承的包可见性为非public，则该class不在文档中体现。

```cangjie
public class XXX <: ParentClass {
    public const CONST_XXX = 1
    public let/var variable: Type
    public init(params: Types)
}
```

**功能：** ……。

**需要权限：** ohos.permission.xxx

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx

**父类型：**

- [ParentClass]()

**示例：**

参考[示例规范](#示例规范)

### const CONST_XXX

参考[const](#const-xxx)

### let/var variable

参考[let/var](#let-xxx)

### init(Types)

标题名为`init(Types)`，其余参考[func](#func-xxxtype1-type2-)

### func xxx(Types)

参考[func](#func-xxxtype1-type2-)

## struct XXX

参考[class](#class-xxx)

## enum XXX

> *写作说明*
>
> 1. 可选，如果没有可删除。如果有多个枚举，请分多个二级标题内容描述，并按字母序展示。
>
> 2. 此二级标题需给出的内容为：签名、功能、需要权限（可选）、系统能力、起始版本、父类型（若涉及）、示例。
>
> 3. **签名**：enum的签名需给出所有`公开构造器`。
>
> 4. enum中的每个公开构造器、function单独新建三级标题，顺序为构造器、成员函数，同类型按字母序排序展示。

```cangjie
public enum XXX <: Equatable & ToString {
    | NAME1
    | NAME2(Int)
}
```

**功能：** 在此处给出该枚举类型的简要描述。如：表示连接的充电器类型的枚举。

**需要权限：** ohos.permission.xxx

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx

**父类型：**

- [Equatable]()
- [ToString]()

### NAME1

```cangjie
NAME1
```

**功能：**……。

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx（与代码注解中的APILEVEL保持一致）

### NAME2(Int)

```cangjie
NAME2(...)
```

**功能：** ……。（带参数的枚举构造器）

**系统能力：** SystemCapability.xxx.xxx

**起始版本：** xx

### func !=(XXX)

参考[func](#func-xxxtype1-type2-)

### func ==(XXX)

参考[func](#func-xxxtype1-type2-)

### func toString()

参考[func](#func-xxxtype1-type2-)

## type XXX

> *写作说明*
>
> 1. 可选，如果没有可删除此二级标题，对应cj.d中的type定义。
>
> 2. 如果取值为固定值，如字符串、已定义的枚举值等，需说明其数据类型和指定取值；如果取值为指定类型，需说明是否可取该类型任意值，还是有取值范围。
>
> 3. 类型如果为自定义类型，需要建立对应超链接。

```cangjie
public type XXX = xx
```

**功能：** ……。

**类型：** [xx]()

（如果类型为lambda表达式`(Type1, Type2) -> ReturnType`，按需提供如下**类型参数**和**类型返回值**表格）

|类型参数|说明|
| --- | --- |
|[Type1]()|（类型参数具体含义）|
|[Type2]()|...|

|类型返回值|说明|
| --- | --- |
|[ReturnType]()|（类型返回值具体含义）|

## extend写法说明

```text
## A（基础类型）
...
### extend A <: B
...
#### func xxx(...)
...
```
