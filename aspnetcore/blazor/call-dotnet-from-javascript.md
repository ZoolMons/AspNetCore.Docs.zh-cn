---
title: 从 ASP.NET Core Blazor 中的 JavaScript 函数调用 .NET 方法
author: guardrex
description: 了解如何在 Blazor 应用中通过 JavaScript 函数调用 .NET 方法。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 08/12/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/call-dotnet-from-javascript
ms.openlocfilehash: c1a97919cb41f42a93f28d9b5f1ecf6bd3e64da0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97592851"
---
# <a name="call-net-methods-from-javascript-functions-in-aspnet-core-no-locblazor"></a><span data-ttu-id="12974-103">从 ASP.NET Core Blazor 中的 JavaScript 函数调用 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="12974-103">Call .NET methods from JavaScript functions in ASP.NET Core Blazor</span></span>

<span data-ttu-id="12974-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)、[Daniel Roth](https://github.com/danroth27)、[Shashikant Rudrawadi](http://wisne.co) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="12974-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Daniel Roth](https://github.com/danroth27), [Shashikant Rudrawadi](http://wisne.co), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="12974-105">Blazor 应用可从 .NET 方法调用 JavaScript 函数，也可从 JavaScript 函数调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="12974-105">A Blazor app can invoke JavaScript functions from .NET methods and .NET methods from JavaScript functions.</span></span> <span data-ttu-id="12974-106">这被称为 JavaScript 互操作（JS 互操作） 。</span><span class="sxs-lookup"><span data-stu-id="12974-106">These scenarios are called *JavaScript interoperability* (*JS interop*).</span></span>

<span data-ttu-id="12974-107">本文介绍如何从 JavaScript 调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="12974-107">This article covers invoking .NET methods from JavaScript.</span></span> <span data-ttu-id="12974-108">要详细了解如何通过 .NET 调用 JavaScript 函数，请参阅 <xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="12974-108">For information on how to call JavaScript functions from .NET, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

<span data-ttu-id="12974-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="12974-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="static-net-method-call"></a><span data-ttu-id="12974-110">静态 .NET 方法调用</span><span class="sxs-lookup"><span data-stu-id="12974-110">Static .NET method call</span></span>

<span data-ttu-id="12974-111">要从 JavaScript 调用静态 .NET 方法，请使用 `DotNet.invokeMethod` 或 `DotNet.invokeMethodAsync` 函数。</span><span class="sxs-lookup"><span data-stu-id="12974-111">To invoke a static .NET method from JavaScript, use the `DotNet.invokeMethod` or `DotNet.invokeMethodAsync` functions.</span></span> <span data-ttu-id="12974-112">传入要调用的静态方法的标识符、包含该函数的程序集的名称以及任意自变量。</span><span class="sxs-lookup"><span data-stu-id="12974-112">Pass in the identifier of the static method you wish to call, the name of the assembly containing the function, and any arguments.</span></span> <span data-ttu-id="12974-113">异步版本是支持 Blazor Server 方案的首选。</span><span class="sxs-lookup"><span data-stu-id="12974-113">The asynchronous version is preferred to support Blazor Server scenarios.</span></span> <span data-ttu-id="12974-114">.NET 方法必须是公共的静态方法，并且包含 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="12974-114">The .NET method must be public, static, and have the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute.</span></span> <span data-ttu-id="12974-115">当前不支持调用开放式泛型方法。</span><span class="sxs-lookup"><span data-stu-id="12974-115">Calling open generic methods isn't currently supported.</span></span>

<span data-ttu-id="12974-116">该示例应用包含一个 C# 方法，用于返回 `int` 数组。</span><span class="sxs-lookup"><span data-stu-id="12974-116">The sample app includes a C# method to return an `int` array.</span></span> <span data-ttu-id="12974-117">[`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 特性应用于方法。</span><span class="sxs-lookup"><span data-stu-id="12974-117">The [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute is applied to the method.</span></span>

<span data-ttu-id="12974-118">`Pages/JsInterop.razor`：</span><span class="sxs-lookup"><span data-stu-id="12974-118">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary"
        onclick="exampleJsFunctions.returnArrayAsyncJs()">
    Trigger .NET static method ReturnArrayAsync
</button>

@code {
    [JSInvokable]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="12974-119">为客户端提供的 JavaScript 会调用 C# .net 方法。</span><span class="sxs-lookup"><span data-stu-id="12974-119">JavaScript served to the client invokes the C# .NET method.</span></span>

<span data-ttu-id="12974-120">`wwwroot/exampleJsInterop.js`：</span><span class="sxs-lookup"><span data-stu-id="12974-120">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=8-14)]

<span data-ttu-id="12974-121">如果选择了“`Trigger .NET static method ReturnArrayAsync`”按钮，请在浏览器的 Web 开发人员工具中检查控制台输出。</span><span class="sxs-lookup"><span data-stu-id="12974-121">When the **`Trigger .NET static method ReturnArrayAsync`** button is selected, examine the console output in the browser's web developer tools.</span></span>

<span data-ttu-id="12974-122">控制台输出为：</span><span class="sxs-lookup"><span data-stu-id="12974-122">The console output is:</span></span>

```console
Array(4) [ 1, 2, 3, 4 ]
```

<span data-ttu-id="12974-123">第四个数组值推送到 `ReturnArrayAsync` 返回的数组 (`data.push(4);`)。</span><span class="sxs-lookup"><span data-stu-id="12974-123">The fourth array value is pushed to the array (`data.push(4);`) returned by `ReturnArrayAsync`.</span></span>

<span data-ttu-id="12974-124">默认情况下，方法标识符是方法名称，但你也可以使用 [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) 特性构造函数来指定其他标识符：</span><span class="sxs-lookup"><span data-stu-id="12974-124">By default, the method identifier is the method name, but you can specify a different identifier using the [`[JSInvokable]`](xref:Microsoft.JSInterop.JSInvokableAttribute) attribute constructor:</span></span>

```csharp
@code {
    [JSInvokable("DifferentMethodName")]
    public static Task<int[]> ReturnArrayAsync()
    {
        return Task.FromResult(new int[] { 1, 2, 3 });
    }
}
```

<span data-ttu-id="12974-125">在客户端 JavaScript 文件中：</span><span class="sxs-lookup"><span data-stu-id="12974-125">In the client-side JavaScript file:</span></span>

```javascript
returnArrayAsyncJs: function () {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'DifferentMethodName')
    .then(data => {
      data.push(4);
      console.log(data);
    });
}
```

<span data-ttu-id="12974-126">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="12974-126">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

## <a name="instance-method-call"></a><span data-ttu-id="12974-127">实例方法调用</span><span class="sxs-lookup"><span data-stu-id="12974-127">Instance method call</span></span>

<span data-ttu-id="12974-128">还可以从 JavaScript 调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="12974-128">You can also call .NET instance methods from JavaScript.</span></span> <span data-ttu-id="12974-129">从 JavaScript 调用 .NET 实例方法：</span><span class="sxs-lookup"><span data-stu-id="12974-129">To invoke a .NET instance method from JavaScript:</span></span>

* <span data-ttu-id="12974-130">按引用向 JavaScript 传递 .NET 实例：</span><span class="sxs-lookup"><span data-stu-id="12974-130">Pass the .NET instance by reference to JavaScript:</span></span>
  * <span data-ttu-id="12974-131">对 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType> 进行静态调用。</span><span class="sxs-lookup"><span data-stu-id="12974-131">Make a static call to <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="12974-132">在 <xref:Microsoft.JSInterop.DotNetObjectReference> 实例中包装实例，并在 <xref:Microsoft.JSInterop.DotNetObjectReference> 实例上调用 <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A>。</span><span class="sxs-lookup"><span data-stu-id="12974-132">Wrap the instance in a <xref:Microsoft.JSInterop.DotNetObjectReference> instance and call <xref:Microsoft.JSInterop.DotNetObjectReference.Create%2A> on the <xref:Microsoft.JSInterop.DotNetObjectReference> instance.</span></span> <span data-ttu-id="12974-133">处置 <xref:Microsoft.JSInterop.DotNetObjectReference> 对象（本部分稍后会展示一个示例）。</span><span class="sxs-lookup"><span data-stu-id="12974-133">Dispose of <xref:Microsoft.JSInterop.DotNetObjectReference> objects (an example appears later in this section).</span></span>
* <span data-ttu-id="12974-134">使用 `invokeMethod` 或 `invokeMethodAsync` 函数在实例上调用 .NET 实例方法。</span><span class="sxs-lookup"><span data-stu-id="12974-134">Invoke .NET instance methods on the instance using the `invokeMethod` or `invokeMethodAsync` functions.</span></span> <span data-ttu-id="12974-135">在从 JavaScript 调用其他 .NET 方法时，也可以将 .NET 实例作为自变量传递。</span><span class="sxs-lookup"><span data-stu-id="12974-135">The .NET instance can also be passed as an argument when invoking other .NET methods from JavaScript.</span></span>

> [!NOTE]
> <span data-ttu-id="12974-136">示例应用会将消息记录到客户端控制台。</span><span class="sxs-lookup"><span data-stu-id="12974-136">The sample app logs messages to the client-side console.</span></span> <span data-ttu-id="12974-137">对于示例应用展示的以下示例，请在浏览器的开发人员工具中检查浏览器的控制台输出。</span><span class="sxs-lookup"><span data-stu-id="12974-137">For the following examples demonstrated by the sample app, examine the browser's console output in the browser's developer tools.</span></span>

<span data-ttu-id="12974-138">如果选择了“`Trigger .NET instance method HelloHelper.SayHello`”按钮，则 `ExampleJsInterop.CallHelloHelperSayHello` 会被调用并将名称 `Blazor` 传递给方法。</span><span class="sxs-lookup"><span data-stu-id="12974-138">When the **`Trigger .NET instance method HelloHelper.SayHello`** button is selected, `ExampleJsInterop.CallHelloHelperSayHello` is called and passes a name, `Blazor`, to the method.</span></span>

<span data-ttu-id="12974-139">`Pages/JsInterop.razor`：</span><span class="sxs-lookup"><span data-stu-id="12974-139">`Pages/JsInterop.razor`:</span></span>

```razor
<button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
    Trigger .NET instance method HelloHelper.SayHello
</button>

@code {
    public async Task TriggerNetInstanceMethod()
    {
        var exampleJsInterop = new ExampleJsInterop(JS);
        await exampleJsInterop.CallHelloHelperSayHello("Blazor");
    }
}
```

<span data-ttu-id="12974-140">`CallHelloHelperSayHello` 使用 `HelloHelper` 的新实例调用 JavaScript 函数 `sayHello`。</span><span class="sxs-lookup"><span data-stu-id="12974-140">`CallHelloHelperSayHello` invokes the JavaScript function `sayHello` with a new instance of `HelloHelper`.</span></span>

<span data-ttu-id="12974-141">`JsInteropClasses/ExampleJsInterop.cs`：</span><span class="sxs-lookup"><span data-stu-id="12974-141">`JsInteropClasses/ExampleJsInterop.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/ExampleJsInterop.cs?name=snippet1&highlight=11-18)]

<span data-ttu-id="12974-142">`wwwroot/exampleJsInterop.js`：</span><span class="sxs-lookup"><span data-stu-id="12974-142">`wwwroot/exampleJsInterop.js`:</span></span>

[!code-javascript[](./common/samples/5.x/BlazorWebAssemblySample/wwwroot/exampleJsInterop.js?highlight=15-18)]

<span data-ttu-id="12974-143">该名称将传递给 `HelloHelper` 的构造函数，该构造函数设置 `HelloHelper.Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="12974-143">The name is passed to `HelloHelper`'s constructor, which sets the `HelloHelper.Name` property.</span></span> <span data-ttu-id="12974-144">执行 JavaScript 函数 `sayHello` 时，`HelloHelper.SayHello` 返回 `Hello, {Name}!` 消息，JavaScript 函数将该消息写入控制台。</span><span class="sxs-lookup"><span data-stu-id="12974-144">When the JavaScript function `sayHello` is executed, `HelloHelper.SayHello` returns the `Hello, {Name}!` message, which is written to the console by the JavaScript function.</span></span>

<span data-ttu-id="12974-145">`JsInteropClasses/HelloHelper.cs`：</span><span class="sxs-lookup"><span data-stu-id="12974-145">`JsInteropClasses/HelloHelper.cs`:</span></span>

[!code-csharp[](./common/samples/5.x/BlazorWebAssemblySample/JsInteropClasses/HelloHelper.cs?name=snippet1&highlight=5,10-11)]

<span data-ttu-id="12974-146">浏览器 Web 开发人员工具中的控制台输出：</span><span class="sxs-lookup"><span data-stu-id="12974-146">Console output in the browser's web developer tools:</span></span>

```console
Hello, Blazor!
```

<span data-ttu-id="12974-147">为避免内存泄露并允许对创建 <xref:Microsoft.JSInterop.DotNetObjectReference> 的组件进行垃圾回收，请采用以下任一方法：</span><span class="sxs-lookup"><span data-stu-id="12974-147">To avoid a memory leak and allow garbage collection on a component that creates a <xref:Microsoft.JSInterop.DotNetObjectReference>, adopt one of the following approaches:</span></span>

* <span data-ttu-id="12974-148">处置类中创建了 <xref:Microsoft.JSInterop.DotNetObjectReference> 实例的对象：</span><span class="sxs-lookup"><span data-stu-id="12974-148">Dispose of the object in the class that created the <xref:Microsoft.JSInterop.DotNetObjectReference> instance:</span></span>

  ```csharp
  public class ExampleJsInterop : IDisposable
  {
      private readonly IJSRuntime js;
      private DotNetObjectReference<HelloHelper> objRef;

      public ExampleJsInterop(IJSRuntime js)
      {
          this.js = js;
      }

      public ValueTask<string> CallHelloHelperSayHello(string name)
      {
          objRef = DotNetObjectReference.Create(new HelloHelper(name));

          return js.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```

  <span data-ttu-id="12974-149">还可以在组件中实现上述 `ExampleJsInterop` 类中所示的模式：</span><span class="sxs-lookup"><span data-stu-id="12974-149">The preceding pattern shown in the `ExampleJsInterop` class can also be implemented in a component:</span></span>

  ```razor
  @page "/JSInteropComponent"
  @using {APP ASSEMBLY}.JsInteropClasses
  @implements IDisposable
  @inject IJSRuntime JS

  <h1>JavaScript Interop</h1>

  <button type="button" class="btn btn-primary" @onclick="TriggerNetInstanceMethod">
      Trigger .NET instance method HelloHelper.SayHello
  </button>

  @code {
      private DotNetObjectReference<HelloHelper> objRef;

      public async Task TriggerNetInstanceMethod()
      {
          objRef = DotNetObjectReference.Create(new HelloHelper("Blazor"));

          await JS.InvokeAsync<string>(
              "exampleJsFunctions.sayHello",
              objRef);
      }

      public void Dispose()
      {
          objRef?.Dispose();
      }
  }
  ```
  
  <span data-ttu-id="12974-150">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="12974-150">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="12974-151">如果组件或类不处置 <xref:Microsoft.JSInterop.DotNetObjectReference>，请通过调用 `.dispose()` 在客户端上处置该对象：</span><span class="sxs-lookup"><span data-stu-id="12974-151">When the component or class doesn't dispose of the <xref:Microsoft.JSInterop.DotNetObjectReference>, dispose of the object on the client by calling `.dispose()`:</span></span>

  ```javascript
  window.myFunction = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'MyMethod');
    dotnetHelper.dispose();
  }
  ```

## <a name="component-instance-method-call"></a><span data-ttu-id="12974-152">组件实例方法调用</span><span class="sxs-lookup"><span data-stu-id="12974-152">Component instance method call</span></span>

<span data-ttu-id="12974-153">要调用组件的 .NET 方法，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="12974-153">To invoke a component's .NET methods:</span></span>

* <span data-ttu-id="12974-154">使用 `invokeMethod` 或 `invokeMethodAsync` 函数对组件执行静态方法调用。</span><span class="sxs-lookup"><span data-stu-id="12974-154">Use the `invokeMethod` or `invokeMethodAsync` function to make a static method call to the component.</span></span>
* <span data-ttu-id="12974-155">组件的静态方法将其实例方法调用包装为已调用的 <xref:System.Action>。</span><span class="sxs-lookup"><span data-stu-id="12974-155">The component's static method wraps the call to its instance method as an invoked <xref:System.Action>.</span></span>

> [!NOTE]
> <span data-ttu-id="12974-156">对于多名用户可能同时使用同一组件的 Blazor Server 应用，请使用帮助程序类来调用实例方法。</span><span class="sxs-lookup"><span data-stu-id="12974-156">For Blazor Server apps, where several users might be concurrently using the same component, use a helper class to invoke instance methods.</span></span>
>
> <span data-ttu-id="12974-157">有关详细信息，请参阅[组件实例方法帮助程序类](#component-instance-method-helper-class)部分。</span><span class="sxs-lookup"><span data-stu-id="12974-157">For more information, see the [Component instance method helper class](#component-instance-method-helper-class) section.</span></span>

<span data-ttu-id="12974-158">在客户端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="12974-158">In the client-side JavaScript:</span></span>

```javascript
function updateMessageCallerJS() {
  DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
}
```

<span data-ttu-id="12974-159">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="12974-159">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="12974-160">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12974-160">`Pages/JSInteropComponent.razor`:</span></span>

```razor
@page "/JSInteropComponent"

<p>
    Message: @message
</p>

<p>
    <button onclick="updateMessageCallerJS()">Call JS Method</button>
</p>

@code {
    private static Action action;
    private string message = "Select the button.";

    protected override void OnInitialized()
    {
        action = UpdateMessage;
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        StateHasChanged();
    }

    [JSInvokable]
    public static void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="12974-161">若要向实例方法传递参数：</span><span class="sxs-lookup"><span data-stu-id="12974-161">To pass arguments to the instance method:</span></span>

* <span data-ttu-id="12974-162">向 JS 方法调用添加参数。</span><span class="sxs-lookup"><span data-stu-id="12974-162">Add parameters to the JS method invocation.</span></span> <span data-ttu-id="12974-163">在下面的示例中，一个名称被传递给方法。</span><span class="sxs-lookup"><span data-stu-id="12974-163">In the following example, a name is passed to the method.</span></span> <span data-ttu-id="12974-164">可根据需要将其他参数添加到列表。</span><span class="sxs-lookup"><span data-stu-id="12974-164">Additional parameters can be added to the list as needed.</span></span>

  ```javascript
  function updateMessageCallerJS(name) {
    DotNet.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller', name);
  }
  ```
  
  <span data-ttu-id="12974-165">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="12974-165">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

* <span data-ttu-id="12974-166">向参数的 <xref:System.Action> 提供正确的类型。</span><span class="sxs-lookup"><span data-stu-id="12974-166">Provide the correct types to the <xref:System.Action> for the parameters.</span></span> <span data-ttu-id="12974-167">向 C# 方法提供参数列表。</span><span class="sxs-lookup"><span data-stu-id="12974-167">Provide the parameter list to the C# methods.</span></span> <span data-ttu-id="12974-168">使用参数 (`action.Invoke(name)`) 调用 <xref:System.Action> (`UpdateMessage`)。</span><span class="sxs-lookup"><span data-stu-id="12974-168">Invoke the <xref:System.Action> (`UpdateMessage`) with the parameters (`action.Invoke(name)`).</span></span>

  <span data-ttu-id="12974-169">`Pages/JSInteropComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="12974-169">`Pages/JSInteropComponent.razor`:</span></span>

  ```razor
  @page "/JSInteropComponent"

  <p>
      Message: @message
  </p>

  <p>
      <button onclick="updateMessageCallerJS('Sarah Jane')">
          Call JS Method
      </button>
  </p>

  @code {
      private static Action<string> action;
      private string message = "Select the button.";

      protected override void OnInitialized()
      {
          action = UpdateMessage;
      }

      private void UpdateMessage(string name)
      {
          message = $"{name}, UpdateMessage Called!";
          StateHasChanged();
      }

      [JSInvokable]
      public static void UpdateMessageCaller(string name)
      {
          action.Invoke(name);
      }
  }
  ```

  <span data-ttu-id="12974-170">选择“调用 JS 方法”按钮时输出 `message`：</span><span class="sxs-lookup"><span data-stu-id="12974-170">Output `message` when the **Call JS Method** button is selected:</span></span>

  ```
  Sarah Jane, UpdateMessage Called!
  ```

## <a name="component-instance-method-helper-class"></a><span data-ttu-id="12974-171">组件实例方法帮助程序类</span><span class="sxs-lookup"><span data-stu-id="12974-171">Component instance method helper class</span></span>

<span data-ttu-id="12974-172">帮助程序类用于将实例方法作为 <xref:System.Action> 进行调用。</span><span class="sxs-lookup"><span data-stu-id="12974-172">The helper class is used to invoke an instance method as an <xref:System.Action>.</span></span> <span data-ttu-id="12974-173">帮助程序类在以下情况中非常有用：</span><span class="sxs-lookup"><span data-stu-id="12974-173">Helper classes are useful when:</span></span>

* <span data-ttu-id="12974-174">同一类型的多个组件呈现在同一页上。</span><span class="sxs-lookup"><span data-stu-id="12974-174">Several components of the same type are rendered on the same page.</span></span>
* <span data-ttu-id="12974-175">使用 Blazor Server 应用，其中多名用户可能同时使用某个组件。</span><span class="sxs-lookup"><span data-stu-id="12974-175">A Blazor Server app is used, where multiple users might be using a component concurrently.</span></span>

<span data-ttu-id="12974-176">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="12974-176">In the following example:</span></span>

* <span data-ttu-id="12974-177">`JSInteropExample` 组件包含若干 `ListItem` 组件。</span><span class="sxs-lookup"><span data-stu-id="12974-177">The `JSInteropExample` component contains several `ListItem` components.</span></span>
* <span data-ttu-id="12974-178">每个 `ListItem` 组件都由一个消息和一个按钮组成。</span><span class="sxs-lookup"><span data-stu-id="12974-178">Each `ListItem` component is composed of a message and a button.</span></span>
* <span data-ttu-id="12974-179">选择 `ListItem` 组件按钮后，`ListItem` 的 `UpdateMessage` 方法会更改列表项文本并隐藏该按钮。</span><span class="sxs-lookup"><span data-stu-id="12974-179">When a `ListItem` component button is selected, that `ListItem`'s `UpdateMessage` method changes the list item text and hides the button.</span></span>

<span data-ttu-id="12974-180">`MessageUpdateInvokeHelper.cs`:</span><span class="sxs-lookup"><span data-stu-id="12974-180">`MessageUpdateInvokeHelper.cs`:</span></span>

```csharp
using System;
using Microsoft.JSInterop;

public class MessageUpdateInvokeHelper
{
    private Action action;

    public MessageUpdateInvokeHelper(Action action)
    {
        this.action = action;
    }

    [JSInvokable("{APP ASSEMBLY}")]
    public void UpdateMessageCaller()
    {
        action.Invoke();
    }
}
```

<span data-ttu-id="12974-181">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="12974-181">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="12974-182">在客户端 JavaScript 中：</span><span class="sxs-lookup"><span data-stu-id="12974-182">In the client-side JavaScript:</span></span>

```javascript
window.updateMessageCallerJS = (dotnetHelper) => {
    dotnetHelper.invokeMethodAsync('{APP ASSEMBLY}', 'UpdateMessageCaller');
    dotnetHelper.dispose();
}
```

<span data-ttu-id="12974-183">占位符 `{APP ASSEMBLY}` 是应用的应用程序集名称（例如 `BlazorSample`）。</span><span class="sxs-lookup"><span data-stu-id="12974-183">The placeholder `{APP ASSEMBLY}` is the app's app assembly name (for example, `BlazorSample`).</span></span>

<span data-ttu-id="12974-184">`Shared/ListItem.razor`:</span><span class="sxs-lookup"><span data-stu-id="12974-184">`Shared/ListItem.razor`:</span></span>

```razor
@inject IJSRuntime JS

<li>
    @message
    <button @onclick="InteropCall" style="display:@display">InteropCall</button>
</li>

@code {
    private string message = "Select one of these list item buttons.";
    private string display = "inline-block";
    private MessageUpdateInvokeHelper messageUpdateInvokeHelper;

    protected override void OnInitialized()
    {
        messageUpdateInvokeHelper = new MessageUpdateInvokeHelper(UpdateMessage);
    }

    protected async Task InteropCall()
    {
        await JS.InvokeVoidAsync("updateMessageCallerJS",
            DotNetObjectReference.Create(messageUpdateInvokeHelper));
    }

    private void UpdateMessage()
    {
        message = "UpdateMessage Called!";
        display = "none";
        StateHasChanged();
    }
}
```

<span data-ttu-id="12974-185">`Pages/JSInteropExample.razor`:</span><span class="sxs-lookup"><span data-stu-id="12974-185">`Pages/JSInteropExample.razor`:</span></span>

```razor
@page "/JSInteropExample"

<h1>List of components</h1>

<ul>
    <ListItem />
    <ListItem />
    <ListItem />
    <ListItem />
</ul>
```

[!INCLUDE[](~/blazor/includes/share-interop-code.md)]

## <a name="avoid-circular-object-references"></a><span data-ttu-id="12974-186">避免循环引用对象</span><span class="sxs-lookup"><span data-stu-id="12974-186">Avoid circular object references</span></span>

<span data-ttu-id="12974-187">不能在客户端上针对以下调用就包含循环引用的对象进行序列化：</span><span class="sxs-lookup"><span data-stu-id="12974-187">Objects that contain circular references can't be serialized on the client for either:</span></span>

* <span data-ttu-id="12974-188">.NET 方法调用。</span><span class="sxs-lookup"><span data-stu-id="12974-188">.NET method calls.</span></span>
* <span data-ttu-id="12974-189">返回类型具有循环引用时，从 C# 发出的 JavaScript 方法调用。</span><span class="sxs-lookup"><span data-stu-id="12974-189">JavaScript method calls from C# when the return type has circular references.</span></span>

<span data-ttu-id="12974-190">有关详细信息，请查看以下问题：</span><span class="sxs-lookup"><span data-stu-id="12974-190">For more information, see the following issues:</span></span>

* [<span data-ttu-id="12974-191">循环引用不受支持，使用两个按钮 (dotnet/aspnetcore #20525)</span><span class="sxs-lookup"><span data-stu-id="12974-191">Circular references are not supported, take two (dotnet/aspnetcore #20525)</span></span>](https://github.com/dotnet/aspnetcore/issues/20525)
* [<span data-ttu-id="12974-192">建议：在序列化时添加机制来处理循环引用 (dotnet/runtime #30820)</span><span class="sxs-lookup"><span data-stu-id="12974-192">Proposal: Add mechanism to handle circular references when serializing (dotnet/runtime #30820)</span></span>](https://github.com/dotnet/runtime/issues/30820)

## <a name="js-modules"></a><span data-ttu-id="12974-193">JS 模块</span><span class="sxs-lookup"><span data-stu-id="12974-193">JS modules</span></span>

<span data-ttu-id="12974-194">对于 JS 隔离，JS 互操作适用于浏览器针对 [EcmaScript 模块 (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)) 的默认支持。</span><span class="sxs-lookup"><span data-stu-id="12974-194">For JS isolation, JS interop works with the browser's default support for [EcmaScript modules (ESM)](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules) ([ECMAScript specification](https://tc39.es/ecma262/#sec-modules)).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="12974-195">其他资源</span><span class="sxs-lookup"><span data-stu-id="12974-195">Additional resources</span></span>

* <xref:blazor/call-javascript-from-dotnet>
* [<span data-ttu-id="12974-196">`InteropComponent.razor` 示例（dotnet/AspNetCore GitHub 存储库，3.1 版本分支）</span><span class="sxs-lookup"><span data-stu-id="12974-196">`InteropComponent.razor` example (dotnet/AspNetCore GitHub repository, 3.1 release branch)</span></span>](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Components/test/testassets/BasicTestApp/InteropComponent.razor)
