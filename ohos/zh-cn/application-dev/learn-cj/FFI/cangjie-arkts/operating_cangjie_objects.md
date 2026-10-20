# ArkTS 访问仓颉数据

此章节介绍如何在 ArkTS 里操作仓颉的对象，有以下三种方式：

1. 自定义函数：使用 ArkTS 运行时的内存管理机制来控制仓颉对象的生命周期，并通过相关的互操作接口来访问该对象。
2. JSExternal：将仓颉对象存入 JSExternal 中，并绑定在 JSObject 上，把 JSExternal 的数据隐藏起来，以此来提高接口的安全性。
3. JSClass：对于追求性能的场景，可以定义一个 JSClass 来加速对象创建和减小内存占用。

## 自定义函数

这里用例展示的是把仓颉对象分享到 ArkTS 运行时，使用 ArkTS 运行时的内存管理机制来控制仓颉对象的生命周期，并通过相关的互操作接口来访问该对象。示例如下：

1. 定义仓颉函数：

    <!--compile-->
    ```cangjie
    // 导入互操作库
    import ohos.ark_interop.*
    import ohos.ark_interop_macro.*
    // 定义共享类，SharedObject为互操作库的类
    class Data <: SharedObject {
        Data(
            // 定义2个属性
            var id: Int64,
            let name: String
        ) {}

        static init() {
            // 注册导出到ark的函数
            JSModule.registerFunc("createData", createData)
            JSModule.registerFunc("setDataId", setDataId)
            JSModule.registerFunc("getDataId", getDataId)
        }

        // 创建共享对象
        static func createData(context: JSContext, _: JSCallInfo): JSValue {
            // 创建仓颉对象
            let data = Data(1, "abc")
            // 创建js对仓颉对象的引用
            let jsExternal = context.external(data)
            // 返回js对仓颉对象的引用
            return jsExternal.toJSValue()
        }

        // 设置对象的id
        static func setDataId(context: JSContext, callInfo: JSCallInfo): JSValue {
            // 读取参数
            let arg0 = callInfo[0]
            let arg1 = callInfo[1]

            // 把参数0转换为js对仓颉对象的引用
            let jsExternal = arg0.asExternal()
            // 获取仓颉对象
            let data: Data = jsExternal.cast<Data>().getOrThrow()
            // 把参数1转换为Float64
            let value = arg1.toNumber()

            // 仓颉对象修改属性
            data.id = Int64(value)

            // 返回undefined
            let result = context.undefined().toJSValue()
            return result
        }

        // 获取对象的id
        static func getDataId(context: JSContext, callInfo: JSCallInfo): JSValue {
            let arg0 = callInfo[0]

            let jsExternal = arg0.asExternal()

            let data: Data = jsExternal.cast<Data>().getOrThrow()

            let result = context.number(Float64(data.id)).toJSValue()
            return result
        }
    }
    ```

2. 在 Index.d.ts 文件中，提供互操作的接口声明：

    ```typescript
    // libohos_app_cangjie_entry.so 对应的 Index.d.ts
    export declare function createData(): undefined;
    export declare function setDataId(data: undefined, value: number): void;
    export declare function getDataId(data: undefined): number;
    ```

3. ArkTS 调用仓颉函数：

    ```typescript
    // 导入仓颉动态库，该动态库名称需与仓颉包名保持一致，且需和互操作接口所属包名一致
    import cjLib from "libohos_app_cangjie_entry.so";

    // 创建共享对象
    let data = cjLib.createData();
    // 操作对象属性
    cjLib.setDataId(data, 3);
    let id = cjLib.getDataId(data);

    console.log("id is " + id);
    ```

JSExternal对象在ArkTS里的类型会被识别为undefined。若直接将undefined作为参数传入，极易出现参数传递错误，导致运行时报错，示例如下：

```typescript
// ...
// 创建共享对象
let data = cjLib.createData();
// 操作对象属性
cjLib.setDataId(undefined, 3); // 错误的参数，应该传递的是仓颉引用，但是编译器能通过编译
let id = cjLib.getDataId(data);
// ...
```

## JSExternal

在实际开发接口时，可以把JSExternal对象绑定到一个JSObject对象上，把JSExternal的数据隐藏起来，以此来提高接口的安全性。

以下示例展示了这一用法：

### 定义仓颉函数

<!--compile-->
```cangjie
// 导入互操作库
import ohos.ark_interop.*
import ohos.ark_interop_macro.*
// 定义共享类
class Data <: SharedObject {
    Data(
        // 定义2个属性
        var id: Int64,
        let name: String
    ) {}

    static init() {
        // 注册导出到ark的函数
        JSModule.registerFunc("createData", createData)
    }

    // 创建共享对象
    static func createData(context: JSContext, _: JSCallInfo): JSValue {
        let data = Data(1, "abc")
        let jsExternal = context.external(data)

        // 创建空JSObject
        let object = context.object()
        // 把js对仓颉对象的引用挂在JSObject的隐藏属性上
        object.attachCJObject(jsExternal)

        // 为js对象增加2个方法
        object["setId"] = context.function(setDataId).toJSValue()
        object["getId"] = context.function(getDataId).toJSValue()

        return object.toJSValue()
    }

    // 设置对象的id
    static func setDataId(context: JSContext, callInfo: JSCallInfo): JSValue {
        // 获取this指针
        let thisArg = callInfo.thisArg
        let arg0 = callInfo[0]

        // 把this指针转换为JSObject
        let thisObject = thisArg.asObject()
        // 从JSObject上获取隐藏属性
        let jsExternal = thisObject.getAttachInfo().getOrThrow()
        // 从js对仓颉对象的引用上获取仓颉对象
        let data = jsExternal.cast<Data>().getOrThrow()
        // 把参数0转换为Float64
        let value = arg0.toNumber()

        // 修改仓颉对象的属性
        data.id = Int64(value)

        let result = context.undefined()
        return result.toJSValue()
    }

    // 获取对象的id
    static func getDataId(context: JSContext, callInfo: JSCallInfo): JSValue {
        let thisArg = callInfo.thisArg
        let thisObject = thisArg.asObject()
        let jsExternal = thisObject.getAttachInfo().getOrThrow()
        let data = jsExternal.cast<Data>().getOrThrow()

        let result = context.number(Float64(data.id)).toJSValue()
        return result
    }
}
```

### 提供互操作的接口声明

在 Index.d.ts 文件中，提供互操作的接口声明：

```typescript
// libohos_app_cangjie_entry.so对应的Index.d.ts
interface Data {
    setId(value: number): void;
    getId(): number;
}

export declare function createData(): Data;
```

### ArkTS 调用仓颉函数

```typescript
// 导入仓颉动态库，该动态库名称为仓颉包名的名称，该名称需要和互操作接口所在的包名一致
import cjLib from "libohos_app_cangjie_entry.so";

// 创建共享对象
let data = cjLib.createData();
// 操作对象属性
data.setId(3);
let id = data.getId();

console.log("id is " + id);
```

## JSClass

将所有对象操作方法直接挂载到对象上，不仅内存占用较高，也会增加对象创建开销。对性能要求较高的场景，可通过定义JSClass，提升对象创建效率并降低内存占用，示例如下：

1. 定义仓颉函数：

    <!--compile-->
    ```cangjie
    // 导入互操作库
    import ohos.ark_interop.*
    import ohos.ark_interop_macro.*
    // 定义共享类
    class Data <: SharedObject {
        Data(
            // 定义2个属性
            var id: Int64,
            let name: String
        ) {}

        static init() {
            // 注册导出到ark的类
            JSModule.registerClass("Data") {
                context =>
                    // 创建JSClass
                    let clazz = context.clazz(jsConstructor)
                    // 增加方法
                    clazz.addMethod(context.string("setId"), context.function(setDataId))
                    clazz.addMethod(context.string("getId"), context.function(getDataId))

                    return clazz
            }
        }

        // js构造函数
        static func jsConstructor(context: JSContext, callInfo: JSCallInfo): JSValue {
            // 获取this指针
            let thisArg = callInfo.thisArg
            // 转换为JSObject
            let thisObject = thisArg.asObject()
            // 创建对象
            let data = Data(1, "abc")
            // 创建js对仓颉对象的引用
            let jsExternal = context.external(data)
            // 设置JSObject属性
            thisObject.attachCJObject(jsExternal)
            return thisObject.toJSValue()
        }

        // 设置对象的id
        static func setDataId(context: JSContext, callInfo: JSCallInfo): JSValue {
            // 获取this指针
            let thisArg = callInfo.thisArg
            // 把this指针转换为JSObject
            let thisObject = thisArg.asObject()
            // 从JSObject上获取隐藏属性
            let jsExternal = thisObject.getAttachInfo().getOrThrow()
            // 从js对仓颉对象的引用上获取仓颉对象
            let data = jsExternal.cast<Data>().getOrThrow()

            let arg0 = callInfo[0]
            // 把参数0转换为Float64
            let value = arg0.toNumber()

            // 修改仓颉对象的属性
            data.id = Int64(value)

            let result = context.undefined()
            return result.toJSValue()
        }

        // 获取对象的id
        static func getDataId(context: JSContext, callInfo: JSCallInfo): JSValue {
            let thisArg = callInfo.thisArg
            let thisObject = thisArg.asObject()
            let jsExternal = thisObject.getAttachInfo().getOrThrow()
            let data = jsExternal.cast<Data>().getOrThrow()

            let result = context.number(Float64(data.id)).toJSValue()
            return result
        }
    }
    ```

2. 在 Index.d.ts 文件中，提供互操作的接口声明：

    ```typescript
    // libohos_app_cangjie_entry.so对应的Index.d.ts
    export declare class Data {
        setId(value: number): void;
        getId(): number;
        constructor();
    }
    ```

3. ArkTS 调用仓颉函数：

    ```typescript
    // 导入仓颉动态库，该动态库名称为仓颉包名的名称，该名称需要和互操作接口所在的包名一致
    import cjLib from "libohos_app_cangjie_entry.so";

    // 创建共享对象
    let data = new cjLib.Data();
    // 操作对象属性
    data.setId(3);
    let id = data.getId();

    console.log("id is " + id);
    ```
