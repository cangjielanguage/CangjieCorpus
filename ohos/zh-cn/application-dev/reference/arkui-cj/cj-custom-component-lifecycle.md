# 自定义组件的生命周期

<!--Del-->
> **说明：**
>
> 当前为Beta阶段。
<!--DelEnd-->

自定义组件的生命周期回调函数用于通知用户该自定义组件的生命周期，这些回调函数在运行时由开发框架在特定的时间进行调用，不能从应用程序中手动调用这些回调函数。不要在多个窗口复用同一个自定义组件节点，其生命周期可能会紊乱。

>**说明：**
>
>- 允许在生命周期函数中使用异步函数，比如网络资源获取，定时器设置等。

## func build()

```cangjie
public func build(): Unit
```

**功能：** build()函数用于定义自定义组件的声明式UI描述，自定义组件必须定义build()函数。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

## func aboutToAppear()

```cangjie
protected open func aboutToAppear(): Unit
```

**功能：** aboutToAppear函数在创建自定义组件的新实例后，在其build()函数执行前调用。允许在aboutToAppear函数中改变状态变量，更改将在后续执行build()函数中生效。

> **说明：**
>
> * 在该回调函数内，建议仅执行当前节点组件的初始化逻辑，避免高耗时操作阻塞主线程。对于高耗时操作，推荐采用缓存或异步方案替代。
> * 在需要频繁创建和销毁组件的场景中，将会频繁调用该回调函数。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

## func onDidBuild()

```cangjie
protected open func onDidBuild(): Unit
```

**功能：** onDidBuild函数在自定义组件的build()函数执行后调用，开发者可以在这个阶段进行埋点数据上报等不影响实际UI的功能。不建议在onDidBuild函数中更改状态变量、使用animateTo等功能，这可能会导致不稳定的UI表现。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

## func aboutToDisappear()

```cangjie
protected open func aboutToDisappear(): Unit
```

**功能：** aboutToDisappear函数在自定义组件析构销毁时执行。不允许在aboutToDisappear函数中改变状态变量，特别是\@Link变量的修改可能会导致应用程序行为不稳定。

> **说明：**
>
> 在需要频繁创建和销毁组件的场景中，将会频繁调用该回调函数。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

## func onPageShow()

```cangjie
protected open func onPageShow(): Unit
```

**功能：** router路由页面（仅[\@Entry](../../arkui-cj/paradigm/cj-create-custom-components.md#entry)装饰的自定义组件）每次显示时触发一次，包括路由跳转、应用进入前台等场景。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

## func onPageHide()

```cangjie
protected open func onPageHide(): Unit
```

**功能：** router路由页面每次隐藏时触发一次，包括路由跳转、应用进入后台等场景。

> **说明：**
>
> 在该回调函数内，建议避免执行高耗时操作阻塞主线程造成卡顿。对于高耗时操作例如相机资源释放，推荐使用异步方案替代。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

## func onBackPress()

```cangjie
protected open func onBackPress(): Bool
```

**功能：** 当用户点击返回按钮时触发（仅router路由页面生效）。返回true表示页面自己处理返回逻辑，不进行页面路由；返回false表示使用默认的路由返回逻辑，不设置返回值按照false处理。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

**返回值：**

|类型|说明|
|:----|:----|
|Bool|返回true表示页面自己处理返回逻辑，不进行页面路由；返回false表示使用默认的路由返回逻辑，不设置返回值按照false处理。|

## func aboutToReuse(ReuseParams)

```cangjie
protected open func aboutToReuse(_: ReuseParams): Unit
```

**功能：** 当一个可复用的自定义组件从复用缓存中重新加入到节点树时，触发aboutToReuse生命周期回调，并将组件的构造参数传递给aboutToReuse。

> **说明：**
>
> - 避免对[\@Link](../../arkui-cj/state_management/cj-macro-link.md)/[\@Prop](../../arkui-cj/state_management/cj-macro-prop.md)等自动更新的状态变量，在aboutToReuse中重复赋值。
> - 在滑动场景中，使用组件复用通常需要用该回调函数去更新组件的状态变量，因此在该回调函数中应避免耗时操作，否则会导致丢帧卡顿。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

**参数：**

|参数名|类型|必填|默认值|说明|
|:---|:---|:---|:---|:---|
|_|[ReuseParams](./cj-common-types.md#class-reuseparams)|是|-|自定义组件的构造参数。|

```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import kit.PerformanceAnalysisKit.Hilog

@Entry
@Component
public class EntryView {
    @State
    var switch: Bool = true
    public func build(): Unit {
        Column() {
            Button("Switch")
            .fontSize(50)
            .onClick({e=>
                this.switch = !this.switch
            })
            if (this.switch) {
                Child(message: "Child")
            }
        }
    }
}

@Reusable
@Component
class Child {
    @State
    var message: String = ""

    protected override func aboutToReuse(params: ReuseParams) {
        Hilog.info(0, "TEST", "Child 复用")
        if (let Some(value) <- params.get<String>("message")) {
            message = value
        }
    }

    func build() {
        Column(space: 20) {
            Text(this.message).fontSize(20)
        }
    }
}
```

## func aboutToRecycle()

```cangjie
protected open func aboutToRecycle(): Unit
```

**功能：** 组件的生命周期回调，在可复用组件从组件树上被加入到复用缓存之前调用。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22

```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import kit.PerformanceAnalysisKit.Hilog

@Entry
@Component
public class EntryView {
    @State
    var switch: Bool = true
    public func build(): Unit {
        Column() {
            Button("Switch")
            .fontSize(50)
            .onClick({e=>
                this.switch = !this.switch
            })
            if (this.switch) {
                Child(message: "Child")
            }
        }
    }
}

@Reusable
@Component
class Child {
    @State
    var message: String = ""

    protected override func aboutToRecycle(): Unit {
        // 这里可以释放比较占内存的内容或其他非必要资源引用，避免一直占用内存，引发内存泄漏
        Hilog.info(0, "TEST", "Child 进入复用池")
    }
    
    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<String>("message")) {
            message = value
        }
    }

    func build() {
        Column(space: 20) {
            Text(this.message).fontSize(20)
        }
    }
}
```

## func pageTransition()

```cangjie
protected open func pageTransition(): Unit
```

**功能：** 进入此页面或移动到其他页面时实现动画。

**系统能力：** SystemCapability.ArkUI.ArkUI.Full

**起始版本：** 22
