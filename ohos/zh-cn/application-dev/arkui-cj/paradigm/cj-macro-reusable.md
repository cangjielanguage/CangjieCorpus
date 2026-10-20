# @Reusable宏：组件复用

<!--Del-->
> **说明：**
>
> 当前为Beta阶段。
<!--DelEnd-->

@Reusable装饰的自定义组件支持组件复用。当自定义组件从组件树上移除时，会被存入缓存池，后续在创建相同类型的组件节点时，将优先复用缓存池中的组件对象，从而避免重复创建和销毁，提升性能。

## 概述

@Reusable用于装饰自定义组件，表示该自定义组件具有被复用的能力。

在开发复杂界面时，UI渲染效率是一个需要考虑的问题。例如在长列表快速滑动时，大量列表项的创建和销毁可能导致界面卡顿。组件复用是一种优化UI性能的重要方法。通过复用先前创建并且已经下树的组件对象，降低组件创建和销毁的频率，从而减小计算开销，提升UI渲染效率。

> **注意：**
>
> - @Reusable装饰的自定义组件在从组件树中移除时，自定义组件（包含视图节点、组件实例和状态上下文）将被放入其父自定义组件的缓存池中。后续创建新自定义组件节点时，将优先复用缓存池中的节点，从而节约组件重新创建的时间。
> - @Reusable提供了[aboutToRecycle](../../reference/arkui-cj/cj-custom-component-lifecycle.md#func-abouttorecycle)和[aboutToReuse](../../reference/arkui-cj/cj-custom-component-lifecycle.md#func-abouttoreusereuseparams)两个生命周期，在组件被回收时调用aboutToRecycle，在组件被复用时调用aboutToReuse。开发者可以在这两个生命周期中实现组件回收、复用相关的业务逻辑。
> - @Reusable装饰的自定义组件下有子组件时，会在回收和复用时递归调用子组件的aboutToRecycle和aboutToReuse（与子组件是否被@Reusable标记无关），直到遍历完所有子组件。
> - 组件复用前后应保持组件结构不变。

## 限制条件

### 仅用于自定义组件

@Reusable仅用于自定义组件[@Component](./cj-create-custom-components.md#component)，不可与[@Builder](./cj-macro-builder.md#builder宏自定义构建函数)搭配使用。

<!-- run -->

```cangjie
package ohos_app_cangjie_entry
import kit.ArkUI.*
import ohos.arkui.state_management.*
import ohos.arkui.state_macro_manage.*

// @Builder不能与@Reuable搭配使用
// @Reusable
@Builder
func MyBuilder() {
    Crash()
}

@Component
public class Crash {
    public func build() {
        Column() {
            Text("Crash")
                .fontSize(12)
                .lineHeight(8)
                .fontColor(Color.Blue)
        }
        .width(100.percent)
        .height(100.percent)
        .justifyContent(FlexAlign.Center)
    }
}

@Entry
@Component
public class EntryView {
    public func build(): Unit {
        Column() {
            MyBuilder()
        }
    }
}
```

### 组件结构需一致

被@Reusable装饰的自定义组件在复用前后，应保持组件的结构不变。否则，会在复用过程中创建或销毁子组件，降低复用效率和性能，甚至造成应用行为异常。

对于复用过程中创建的子组件，框架会在其创建后依次调用aboutToReuse方法和aboutToAppear方法。在调用aboutToReuse方法时，由于其aboutToAppear方法还未执行，且内部子组件还未创建，因此aboutToReuse方法中依赖aboutToAppear方法执行结果，或依赖内部子组件状态的相关操作会引起预期外的行为。在调用aboutToReuse方法后，框架会再调用aboutToAppear方法并初始化组件。

### 不建议嵌套使用

@Reusable宏不建议嵌套使用，会增加内存，降低复用效率，加大维护难度。嵌套使用会导致额外缓存池的生成，各缓存池拥有相同树状结构，复用效率低下。此外，嵌套使用会使生命周期管理复杂，资源和变量共享困难。

## 使用场景

### 动态布局更新

重复创建与移除视图可能引起频繁的布局计算，从而影响帧率。采用组件复用可以避免不必要的视图创建与布局计算，提升性能。

以下示例中，将Child自定义组件标记为复用组件，通过Button点击更新Child，触发复用。

 <!-- run -->

```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*

public class Message {
    public var value: String
    public init(val: String) {
        value = val
    }
}

@Entry
@Component
public class EntryView {
    @State
    var switch: Bool = true
    public func build() {
        Column() {
            Button("Hello")
                .fontSize(30)
                .fontWeight(FontWeight.Bold)
                .onClick({
                    evt => switch = !switch
                })
            if (switch) {
                Child(message: Message("Child"))
            }
        }
            .height(100.percent)
            .width(100.percent)
    }
}

@Reusable
@Component
class Child {
    @State
    var message: Message = Message("about to reuse")
    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<Message>("message")) {
            message = value
        }
    }
    func build() {
        Column() {
            Text(this
                .message
                .value)
        }
            .borderWidth(1)
            .height(100)
    }
}
```

### 列表滚动配合LazyForEach使用

当应用展示大量数据的列表并进行滚动操作时，频繁创建和销毁列表项视图可能导致卡顿和性能问题。使用列表组件的组件复用机制可以重用已创建的列表项视图，提高滚动流畅度。

以下示例代码将CardView自定义组件标记为复用组件，List上下滑动，触发CardView复用。

 <!-- run -->

```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import std.collection.ArrayList

class MyDataSource <: IDataSource<Int64> {
    public MyDataSource(let data_: ArrayList<Int64>) {}
    public var listenerOp: Option<DataChangeListener> = None
    public func totalCount(): Int64 {
        return data_.size
    }
    public func getData(index: Int64): Int64 {
        return data_[index]
    }

    public func pushData(val: Int64): Unit {
        data_.add(val)
    }

    public func registerDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = listener
    }

    public func unregisterDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = None
    }
}

@Entry
@Component
public class EntryView {
    let data: MyDataSource = MyDataSource(ArrayList<Int64>([]))
    protected override func aboutToAppear() {
        for (i in 0..1000) {
            data.pushData(i)
        }
    }

    public func build(): Unit {
        Column() {
            List() {
                LazyForEach(
                    data,
                    itemGenerator: {
                        item: Int64, idx: Int64 => ListItem() {
                            CardView(item: "${item}")
                        }
                    }
                )
            }
        }
    }
}

// 复用组件
@Reusable
@Component
class CardView {
    // 被@State修饰的变量item才能更新，未被@State修饰的变量不会更新。
    @State
    var item: String = ""

    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<String>("item")) {
            item = value
        }
    }

    func build() {
        Column() {
            Text(item).fontSize(30)
        }.borderWidth(1).height(100)
    }
}
```

### if使用场景

示例代码将OneMoment自定义组件标记为复用组件，List上下滑动，触发OneMoment复用;

 <!-- run -->

```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import std.collection.ArrayList
import kit.PerformanceAnalysisKit.Hilog
import kit.LocalizationKit.*

class MyDataSource <: IDataSource<FriendMoment> {
    public MyDataSource(let data_: ArrayList<FriendMoment>) {}
    public var listenerOp: Option<DataChangeListener> = None
    public func totalCount(): Int64 {
        return data_.size
    }
    public func getData(index: Int64): FriendMoment {
        return data_[index]
    }

    public func pushData(val: FriendMoment): Unit {
        data_.add(val)
    }

    public func registerDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = listener
    }

    public func unregisterDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = None
    }
}

public class FriendMoment {
    public var text: String = ""
    public var title: String = ""
    public var image: ?AppResource = None
    public init(text: String, title: String, image: ?AppResource) {
        this.text = text
        this.title = title
        this.image = image
    }
}

@Reusable
@Component
public class OneMoment {
    @State
    var moment: FriendMoment = FriendMoment("", "", @r(app.media.startIcon))
    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<FriendMoment>("moment")) {
            this.moment = value
            Hilog.info(0, "cangjie", "====aboutToReuse====OneMoment==复用了==== ${value.text}")
        }
    }
    public func build() {
        Column() {
            Text(moment.text)
            if (moment
                .image
                .isSome()) {
                Flex(wrap: FlexWrap.Wrap) {
                    Image((moment.image) ?? @r(app.media.background))
                        .height(50)
                        .width(50)
                    Image((moment.image) ?? @r(app.media.background))
                        .height(50)
                        .width(50)
                    Image((moment.image) ?? @r(app.media.background))
                        .height(50)
                        .width(50)
                    Image((moment.image) ?? @r(app.media.background))
                        .height(50)
                        .width(50)
                }
            }
        }
    }
}

@Entry
@Component
public class EntryView {
    let data: MyDataSource = MyDataSource(ArrayList<FriendMoment>([]))
    protected override func aboutToAppear() {
        for (i in 0..20) {
            let title = "${i + 1}test+if"
            data.pushData(FriendMoment("${i}", title, @r(app.media.startIcon)))
        }
        for (i in 0..50) {
            let title = "${i + 1}test+if"
            data.pushData(FriendMoment("${i}", title, Option<AppResource>.None))
        }
    }

    public func build(): Unit {
        Column() {
            List() {
                LazyForEach(
                    data,
                    itemGenerator: {
                        item: FriendMoment, idx: Int64 => ListItem() {
                            OneMoment(moment: item)
                        }
                    }
                )
            }
        }
    }
}
```

### 列表滚动-Foreach使用场景

使用Foreach创建可复用的自定义组件，由于Foreach渲染控制语法的全展开属性，导致复用组件无法复用。示例中点击update，数据刷新成功，但滑动列表时，ListItemView无法复用。点击clear，再次点击update，ListItemView复用成功，因为一帧内重复创建多个已被销毁的自定义组件。

<!-- run -->
```cangjie
package ohos_app_cangjie_entry
import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import kit.PerformanceAnalysisKit.Hilog

@Entry
@Component
public class EntryView {
   @State var arrlist: ObservedArrayList<Int64> = ObservedArrayList<Int64>([])
   @State var switch: Bool = true
   func build() {
       Column() {
           Button("clear").onClick({e=>
               if (arrlist.size < 50) { return }
               for (i in 0..50) {
                   this.arrlist.remove(0)
               }
           })
           Button("update").onClick({e=>
               for (i in 0..50) {
                   this.arrlist.append(i)
               }
           })
           List(space: 10) {
               ForEach(this.arrlist, itemGenerator: {item: Int64, idx: Int64 =>
                   ListItem() {
                       ListItemView(num: item)
                   }
               }, keyGenerator: {item: Int64, idx: Int64 => "${item}"})
           }.cachedCount(0)
       }.height(100.percent).width(100.percent)
   }
}

@Reusable
@Component
class ListItemView {
   @State
   var num: Int64 = 0

   protected override func aboutToAppear() {
       // 点击 update，首次进入，上下滑动，由于Foreach折叠展开属性，无法复用。
       Hilog.info(0, "TEST", "=== Child 创建 ===")
   }

   protected override func aboutToReuse(params: ReuseParams) {
       // 点击clear，再次update，复用成功。
       // 符合一帧内重复创建多个已销毁的自定义组件。
       if (let Some(value) <- params.get<Int64>("num")) {
           num = value
       }
   }

   func build() {
       Column() {
           Text("Child ${num}")
       }
   }
}
```

### Grid使用场景

 示例中使用@Reusable宏修饰GridItem中的自定义组件ReusableChildComponent，即表示其具备组件复用的能力。

使用aboutToReuse可以在Grid滑动时，从复用缓存中加入到组件树之前触发，从而更新组件状态变量，展示正确内容。

需要注意的是无需在aboutToReuse中对[\@Link](../state_management/cj-macro-link.md)、[\@StorageLink](../state_management/cj-appstorage.md#storagelink)、[\@Consume](../state_management/cj-macro-provide-and-consume.md)等自动更新值的状态变量进行更新，可能触发不必要的组件刷新。

 <!-- run -->

 ```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import std.collection.ArrayList

class MyDataSource <: IDataSource<Int64> {
    public MyDataSource(let data_: ArrayList<Int64>) {}
    public var listenerOp: Option<DataChangeListener> = None
    public func totalCount(): Int64 {
        return data_.size
    }
    public func getData(index: Int64): Int64 {
        return data_[index]
    }

    public func pushData(val: Int64): Unit {
        data_.add(val)
    }

    public func registerDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = listener
    }

    public func unregisterDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = None
    }
}

@Entry
@Component
public class EntryView {
    let data: MyDataSource = MyDataSource(ArrayList<Int64>([]))
    protected override func aboutToAppear() {
        for (i in 0..100) {
            data.pushData(i)
        }
    }

    public func build(): Unit {
        Column() {
            Grid() {
                LazyForEach(data, itemGenerator: {item: Int64, idx: Int64 =>
                    GridItem() {
                        ReusableChildComponent(item: item)
                    }
                })
            }
            .cachedCount(2)
            .columnsTemplate("1fr 1fr 1fr")
            .columnsGap(10)
            .rowsGap(10)
            .margin(10)
            .height(500)
            .backgroundColor(0xFAEEE0)
        }
    }
}


@Reusable
@Component
class ReusableChildComponent {
    @State
    var item: Int64 = 0

    // aboutToReuse从复用缓存中加入到组件树之前调用，可在此处更新组件的状态变量以展示正确的内容。
    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<Int64>("item")) {
            item = value
        }
    }
    public func build() {
        Column() {
           Text("Grid child ${item}")
        }.padding(20)
    }
}
 ```

 ### Swiper使用场景

 在Swiper滑动场景中，条目中的子组件频繁创建和销毁。可以将这些子组件封装成自定义组件，并使用@Reusable宏修饰，以实现组件复用。
 
```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import std.collection.ArrayList

class MyDataSource <: IDataSource<Int64> {
    public MyDataSource(let data_: ArrayList<Int64>) {}
    public var listenerOp: Option<DataChangeListener> = None
    public func totalCount(): Int64 {
        return data_.size
    }
    public func getData(index: Int64): Int64 {
        return data_[index]
    }

    public func pushData(val: Int64): Unit {
        data_.add(val)
    }

    public func registerDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = listener
    }

    public func unregisterDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = None
    }
}

@Entry
@Component
public class EntryView {
    let data: MyDataSource = MyDataSource(ArrayList<Int64>([]))
    protected override func aboutToAppear() {
        for (i in 0..100) {
            data.pushData(i)
        }
    }

    public func build(): Unit {
        Column() {
            Swiper() {
                LazyForEach(data, itemGenerator: {item: Int64, idx: Int64 =>
                    SwiperItem(item: item)
                }, keyGenerator: {item: Int64, idx: Int64 => "${item}"})
            }
        }
    }
}


@Reusable
@Component
class SwiperItem {
    @State
    var item: Int64 = 0

    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<Int64>("item")) {
            item = value
        }
    }
    public func build() {
        Column() {
           Text("Swiper Content ${item}")
        }.padding(20).height(100).width(100.percent)
    }
}
```

 ### 列表滚动-ListItemGroup使用场景

可以视作特殊List滑动场景，将ListItem需要移除重建的子组件封装成自定义组件，并使用@Reusable装饰器修饰，使其具备组件复用能力。

```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import std.collection.ArrayList

class DataSrc1 <: IDataSource<String> {
    public DataSrc1() {}
    public var data: ArrayList<String> = ArrayList<String>()
    public var listenerOp: Option<DataChangeListener> = None

    public func totalCount(): Int64 {
        return data.size
    }

    public func getData(index: Int64): String {
        return data[index]
    }

    public func registerDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = listener
    }

    public func unregisterDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = None
    }

    public func pushData(val: String): Unit {
        data.add(val)
    }
}

class DataSrc2 <: IDataSource<DataSrc1> {
    public DataSrc2() {}
    public var data: ArrayList<DataSrc1> = ArrayList<DataSrc1>()
    public var listenerOp: Option<DataChangeListener> = None

    public func totalCount(): Int64 {
        return data.size
    }

    public func getData(index: Int64): DataSrc1 {
        return data[index]
    }

    public func registerDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = listener
    }

    public func unregisterDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = None
    }

    public func pushData(val: DataSrc1): Unit {
        data.add(val)
    }
}

@Entry
@Component
class EntryView {
    let data: DataSrc2 = DataSrc2()

    protected override func aboutToAppear() {
        for (i in 0..10000) {
            let data1 = DataSrc1()
            for (j in 0..12) {
                data1.pushData("测试条目数据: ${i} - ${j}")
            }
            data.pushData(data1)
        }
    }
      @Builder
      func itemHead(text: String) {
        Text(text)
          .fontSize(20)
          .backgroundColor(0xAABBCC)
          .width(100.percent)
          .padding(10)
      }
    public func build() {
        Stack() {
            List() {
                LazyForEach(
                    data,
                    itemGenerator: { item: DataSrc1, index: Int64 =>
                        ListItemGroup(header: {=> bind(itemHead, this)("${index}")}) {
                            LazyForEach(
                                item,
                                itemGenerator: { item2: String, index2: Int64 =>
                                    ListItem() {
                                        Inner(str: item2)
                                    }
                                }
                            )
                        }
                        .width(100.percent)
                        .height(60)
                    }
                )
            }
        }
        .width(100.percent)
        .height(100.percent)
    }
}

@Reusable
@Component
class Inner {
    @State var str: String = ""

    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<String>("str")) {
            str = value
        }
    }

    func build() {
        Text(str)
    }
}
```

### 多条目类型使用场景

**标准型**

复用组件的布局相同，示例参见本文列表滚动部分的描述。

**组合型**

复用组件间存在多种差异，但通常具备共同的子组件。将三种复用组件以组合型方式转换为Builder函数后，内部的共享子组件将统一置于父组件EntryView之下。复用这些子组件时，缓存池在父组件层面实现共享，减少组件创建过程中的资源消耗。

```cangjie
package ohos_app_cangjie_entry

import kit.ArkUI.*
import ohos.arkui.state_macro_manage.*
import std.collection.ArrayList
import kit.PerformanceAnalysisKit.Hilog
import kit.LocalizationKit.*

class MyDataSource <: IDataSource<String> {
    public MyDataSource() {}
    private var dataArray: ArrayList<String> = ArrayList<String>()
    private var listenerOp: Option<DataChangeListener> = None

    public func totalCount(): Int64 {
        return dataArray.size
    }

    public func getData(index: Int64): String {
        return dataArray[index]
    }

    public func pushData(data: String): Unit {
        dataArray.add(data)
    }

    public func registerDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = listener
    }

    public func unregisterDataChangeListener(listener: DataChangeListener): Unit {
        listenerOp = None
    }
}

@Entry
@Component
class EntryView {
    let data: MyDataSource = MyDataSource()

    protected override func aboutToAppear() {
        for (i in 0..1000) {
            data.pushData("${i}")
        }
    }

    @Builder
    func itemBuilderOne(item: String) {
        Column() {
            ChildComponentA(item: item)
            ChildComponentB(item: item)
            ChildComponentC(item: item)
        }
    }

    @Builder
    func itemBuilderTwo(item: String) {
        Column() {
            ChildComponentA(item: item)
            ChildComponentC(item: item)
            ChildComponentD(item: item)
        }
    }

    @Builder
    func itemBuilderThree(item: String) {
        Column() {
            ChildComponentA(item: item)
            ChildComponentB(item: item)
            ChildComponentD(item: item)
        }
    }

    public func build() {
        List(space: 40) {
            LazyForEach(
                data,
                itemGenerator: { item: String, index: Int64 =>
                    ListItem() {
                        if (index % 3 == 0) {
                            itemBuilderOne(item)
                        } else if (index % 5 == 0) {
                            itemBuilderTwo(item)
                        } else {
                            itemBuilderThree(item)
                        }
                    }
                    .backgroundColor(0xFFCCCCCC)
                    .width(100.percent)
                    .onAppear({ =>
                        Hilog.info(0, "Sample_ReusableComponent", "ListItem ${index} onAppear")
                    })
                }
            )
        }
        .width(100.percent)
        .height(100.percent)
        .cachedCount(0)
    }
}

@Reusable
@Component
class ChildComponentA {
    @State var item: String = ""

    protected override func aboutToReuse(params: ReuseParams) {
        Hilog.info(0, "Sample_ReusableComponent", "ChildComponentA Reuse")
        if (let Some(value) <- params.get<String>("item")) {
            item = value
        }
    }

    protected override func aboutToRecycle() {
        Hilog.info(0, "Sample_ReusableComponent", "ChildComponentA Recycle")
    }

    func build() {
        Column() {
            Text("Item ${item} Child Component A")
                .fontSize(20)
                .margin(left: 10)
                .fontColor(Color.Blue)
            Grid() {
                ForEach([1,2,3,4,5,6,7,8,9], itemGenerator: { item: Int64, index: Int64 =>
                    GridItem() {
                         // 请开发者自行在src/main/resources/base/media路径下添加app.media.startIcon图片，否则运行时会因资源缺失而报错。
                        Image(@r(app.media.startIcon))
                            .height(20)
                    }
                })
            }
            .columnsTemplate("1fr 1fr 1fr 1fr 1fr")
            .rowsTemplate("1fr 1fr 1fr 1fr")
            .columnsGap(10)
            .width(90.percent)
            .height(160)
        }
        .margin(left: 10, right: 10)
        .backgroundColor(0xFAEEE0)
    }
}

@Reusable
@Component
class ChildComponentB {
    @State var item: String = ""

    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<String>("item")) {
            item = value
        }
    }

    func build() {
        Row() {
            Text("Item ${item} Child Component B")
                .fontSize(20)
                .margin(left: 10)
                .fontColor(Color.Red)
        }
        .margin(left: 10, right: 10)
    }
}

@Reusable
@Component
class ChildComponentC {
    @State var item: String = ""

    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<String>("item")) {
            item = value
        }
    }

    func build() {
        Row() {
            Text("Item ${item} Child Component C")
                .fontSize(20)
                .margin(left: 10)
                .fontColor(Color.Green)
        }
        .margin(left: 10, right: 10)
    }
}

@Reusable
@Component
class ChildComponentD {
    @State var item: String = ""

    protected override func aboutToReuse(params: ReuseParams) {
        if (let Some(value) <- params.get<String>("item")) {
            item = value
        }
    }

    func build() {
        Row() {
            Text("Item ${item} Child Component D")
                .fontSize(20)
                .margin(left: 10)
                .fontColor(Color.Green)
        }
        .margin(left: 10, right: 10)
    }
}
```