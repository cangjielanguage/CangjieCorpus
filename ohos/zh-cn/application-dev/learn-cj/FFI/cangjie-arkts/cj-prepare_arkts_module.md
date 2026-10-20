# ArkTS 侧获取互操作模块名

仓颉应用中支持增加ArkTS模块，不支持增加ArkTS页面，以下主要介绍增加ArkTS模块。

ArkTS模块主要分为NAPI（Node.js API） 模块以及三方库，仓颉仅支持对NAPI模块的调用。

在介绍具体的加载前，首先介绍仓颉的导入名称及ArkTS模块名称的映射规范，如下：

| ArkTS 模块名                 | 仓颉导入名称                             | 说明                                                         |
| :--------------------------- | :--------------------------------------- | :----------------------------------------------------------- |
| @ohos.file.photoAccessHelper | ("file.photoAccessHelper")               | ArkTS 模块名以 @ohos 开头，则仓颉导入名称只需要去掉前缀 "@ohos."。 |
| @hms.core.push.pushService   | ("core.push.pushService", prefix: "hms") | ArkTS 模块名以 @xxx （xxx 不为 ohos） 开头，则有两个导入名称，第一个名称为模块名去掉 "@xxx." 的剩余部分，第二个参数为 `prefix: "xxx"`。该例的仓颉导入名称为 `"core.push.pushService", prefix: "hms"`。 |

仓颉应用使用 ArkTS 模块的工程配置与一般的仓颉工程没有区别，可参考<!--RP1-->[创建一个新的工程](../../../cj-start/start/quick-start/cj-quick-start-first-cangjie-app.md)<!--RP1End-->，仅额外需要在代码中加载 NAPI 模块。以加载 OpenHarmony <!--RP2-->[相册管理](https://gitcode.com/openharmony/docs/blob/master/zh-cn/application-dev/reference/apis-media-library-kit/arkts-apis-photoAccessHelper.md)<!--RP2End-->模块为例：查看 ArkTS 文档后，其模块名为"@ohos.file.photoAccessHelper"。 这样就获取到了 ArkTS 侧的模块名，后续可以在仓颉侧调用。
