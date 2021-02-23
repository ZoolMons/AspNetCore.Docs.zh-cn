---
title: ASP.NET Core 中 .NET 的开放 Web 接口 (OWIN)
author: ardalis
description: 了解 ASP.NET Core 如何支持 .NET 的开放 Web 接口 (OWIN)，将 Web 应用与 Web 服务器分离。
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
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
uid: fundamentals/owin
ms.openlocfilehash: c44803c6f67d8e03759a01f7aa71d82088be9a11
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975269"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a><span data-ttu-id="b5b73-103">ASP.NET Core 中 .NET 的开放 Web 接口 (OWIN)</span><span class="sxs-lookup"><span data-stu-id="b5b73-103">Open Web Interface for .NET (OWIN) with ASP.NET Core</span></span>

<span data-ttu-id="b5b73-104">作者：[Steve Smith](https://ardalis.com/) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b5b73-104">By [Steve Smith](https://ardalis.com/) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b5b73-105">ASP.NET Core：</span><span class="sxs-lookup"><span data-stu-id="b5b73-105">ASP.NET Core:</span></span>

* <span data-ttu-id="b5b73-106">支持 .NET 的开放 Web 接口 (OWIN)。</span><span class="sxs-lookup"><span data-stu-id="b5b73-106">Supports the Open Web Interface for .NET (OWIN).</span></span>
* <span data-ttu-id="b5b73-107">具有与 .NET Core 兼容的 `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) 库的替换项。</span><span class="sxs-lookup"><span data-stu-id="b5b73-107">Has .NET Core compatible replacements for the `Microsoft.Owin.*` ([Katana](/aspnet/aspnet/overview/owin-and-katana/)) libraries.</span></span>

<span data-ttu-id="b5b73-108">OWIN 允许 Web 应用从 Web 服务器分离。</span><span class="sxs-lookup"><span data-stu-id="b5b73-108">OWIN allows web apps to be decoupled from web servers.</span></span> <span data-ttu-id="b5b73-109">它定义了在管道中使用中间件来处理请求和相关响应的标准方法。</span><span class="sxs-lookup"><span data-stu-id="b5b73-109">It defines a standard way for middleware to be used in a pipeline to handle requests and associated responses.</span></span> <span data-ttu-id="b5b73-110">ASP.NET Core 应用程序和中间件可以与基于 OWIN 的应用程序、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="b5b73-110">ASP.NET Core applications and middleware can interoperate with OWIN-based applications, servers, and middleware.</span></span>

<span data-ttu-id="b5b73-111">OWIN 提供了一个分离层，可一起使用具有不同对象模型的两个框架。</span><span class="sxs-lookup"><span data-stu-id="b5b73-111">OWIN provides a decoupling layer that allows two frameworks with disparate object models to be used together.</span></span> <span data-ttu-id="b5b73-112">`Microsoft.AspNetCore.Owin` 包提供了两个适配器实现：</span><span class="sxs-lookup"><span data-stu-id="b5b73-112">The `Microsoft.AspNetCore.Owin` package provides two adapter implementations:</span></span>

* <span data-ttu-id="b5b73-113">ASP.NET Core 到 OWIN</span><span class="sxs-lookup"><span data-stu-id="b5b73-113">ASP.NET Core to OWIN</span></span> 
* <span data-ttu-id="b5b73-114">OWIN 到 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b5b73-114">OWIN to ASP.NET Core</span></span>

<span data-ttu-id="b5b73-115">此方法可将 ASP.NET Core 托管在兼容 OWIN 的服务器/主机上，或在 ASP.NET Core 上运行其他兼容 OWIN 的组件。</span><span class="sxs-lookup"><span data-stu-id="b5b73-115">This allows ASP.NET Core to be hosted on top of an OWIN compatible server/host or for other OWIN compatible components to be run on top of ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="b5b73-116">使用这些适配器会带来性能成本。</span><span class="sxs-lookup"><span data-stu-id="b5b73-116">Using these adapters comes with a performance cost.</span></span> <span data-ttu-id="b5b73-117">仅使用 ASP.NET Core 组件的应用不应使用 `Microsoft.AspNetCore.Owin` 包或适配器。</span><span class="sxs-lookup"><span data-stu-id="b5b73-117">Apps using only ASP.NET Core components shouldn't use the `Microsoft.AspNetCore.Owin` package or adapters.</span></span>

<span data-ttu-id="b5b73-118">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b5b73-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a><span data-ttu-id="b5b73-119">在 ASP.NET Core 管道中运行 OWIN 中间件</span><span class="sxs-lookup"><span data-stu-id="b5b73-119">Running OWIN middleware in the ASP.NET Core pipeline</span></span>

<span data-ttu-id="b5b73-120">ASP.NET Core 的 OWIN 支持作为 `Microsoft.AspNetCore.Owin` 包的一部分进行部署。</span><span class="sxs-lookup"><span data-stu-id="b5b73-120">ASP.NET Core's OWIN support is deployed as part of the `Microsoft.AspNetCore.Owin` package.</span></span> <span data-ttu-id="b5b73-121">可通过安装此包将 OWIN 支持导入到项目中。</span><span class="sxs-lookup"><span data-stu-id="b5b73-121">You can import OWIN support into your project by installing this package.</span></span>

<span data-ttu-id="b5b73-122">OWIN 中间件符合 [OWIN 规范](https://owin.org/spec/spec/owin-1.0.0.html)，该规范要求使用 `Func<IDictionary<string, object>, Task>` 接口，并设置特定的键（如 `owin.ResponseBody`）。</span><span class="sxs-lookup"><span data-stu-id="b5b73-122">OWIN middleware conforms to the [OWIN specification](https://owin.org/spec/spec/owin-1.0.0.html), which requires a `Func<IDictionary<string, object>, Task>` interface, and specific keys be set (such as `owin.ResponseBody`).</span></span> <span data-ttu-id="b5b73-123">以下简单的 OWIN 中间件显示“Hello World”：</span><span class="sxs-lookup"><span data-stu-id="b5b73-123">The following simple OWIN middleware displays "Hello World":</span></span>

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

<span data-ttu-id="b5b73-124">示例签名返回 `Task`，并接受 OWIN 所要求的 `IDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="b5b73-124">The sample signature returns a `Task` and accepts an `IDictionary<string, object>` as required by OWIN.</span></span>

<span data-ttu-id="b5b73-125">以下代码显示了如何使用 `UseOwin` 扩展方法将 `OwinHello` 中间件（如上所示）添加到 ASP.NET Core 管道。</span><span class="sxs-lookup"><span data-stu-id="b5b73-125">The following code shows how to add the `OwinHello` middleware (shown above) to the ASP.NET Core pipeline with the `UseOwin` extension method.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

<span data-ttu-id="b5b73-126">可配置在 OWIN 管道中要进行的其他操作。</span><span class="sxs-lookup"><span data-stu-id="b5b73-126">You can configure other actions to take place within the OWIN pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="b5b73-127">响应标头只能在首次写入响应流之前进行修改。</span><span class="sxs-lookup"><span data-stu-id="b5b73-127">Response headers should only be modified prior to the first write to the response stream.</span></span>

> [!NOTE]
> <span data-ttu-id="b5b73-128">由于性能原因，不建议多次调用 `UseOwin`。</span><span class="sxs-lookup"><span data-stu-id="b5b73-128">Multiple calls to `UseOwin` is discouraged for performance reasons.</span></span> <span data-ttu-id="b5b73-129">组合在一起时 OWIN 组件的性能最佳。</span><span class="sxs-lookup"><span data-stu-id="b5b73-129">OWIN components will operate best if grouped together.</span></span>

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a><span data-ttu-id="b5b73-130">在基于 OWIN 的服务器上运行 ASP.NET Core 并使用其 WebSocket 支持</span><span class="sxs-lookup"><span data-stu-id="b5b73-130">Run ASP.NET Core on an OWIN-based server and use its WebSockets support</span></span>

<span data-ttu-id="b5b73-131">ASP.NET Core 如何利用基于 OWIN 的服务器功能的另一个示例是访问 WebSocket 等功能。</span><span class="sxs-lookup"><span data-stu-id="b5b73-131">Another example of how OWIN-based servers' features can be leveraged by ASP.NET Core is access to features like WebSockets.</span></span> <span data-ttu-id="b5b73-132">前面示例中使用的 .NET OWIN Web 服务器支持内置的 Web 套接字，可由 ASP.NET Core 应用程序利用。</span><span class="sxs-lookup"><span data-stu-id="b5b73-132">The .NET OWIN web server used in the previous example has support for Web Sockets built in, which can be leveraged by an ASP.NET Core application.</span></span> <span data-ttu-id="b5b73-133">下面的示例显示了简单的 Web 应用，它支持 Web 套接字并回显通过 WebSocket 发送到服务器的所有内容。</span><span class="sxs-lookup"><span data-stu-id="b5b73-133">The example below shows a simple web app that supports Web Sockets and echoes back everything sent to the server through WebSockets.</span></span>

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a><span data-ttu-id="b5b73-134">OWIN 环境</span><span class="sxs-lookup"><span data-stu-id="b5b73-134">OWIN environment</span></span>

<span data-ttu-id="b5b73-135">可使用 `HttpContext` 来构造 OWIN 环境。</span><span class="sxs-lookup"><span data-stu-id="b5b73-135">You can construct an OWIN environment using the `HttpContext`.</span></span>

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a><span data-ttu-id="b5b73-136">OWIN 键</span><span class="sxs-lookup"><span data-stu-id="b5b73-136">OWIN keys</span></span>

<span data-ttu-id="b5b73-137">OWIN 依赖于 `IDictionary<string,object>` 对象，以在整个 HTTP请求/响应交换中传达信息。</span><span class="sxs-lookup"><span data-stu-id="b5b73-137">OWIN depends on an `IDictionary<string,object>` object to communicate information throughout an HTTP Request/Response exchange.</span></span> <span data-ttu-id="b5b73-138">ASP.NET Core 实现以下所列的键。</span><span class="sxs-lookup"><span data-stu-id="b5b73-138">ASP.NET Core implements the keys listed below.</span></span> <span data-ttu-id="b5b73-139">请参阅[主规范、扩展](https://owin.org/#spec)和 [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html)（OWIN 键指南和常用键）。</span><span class="sxs-lookup"><span data-stu-id="b5b73-139">See the [primary specification, extensions](https://owin.org/#spec), and [OWIN Key Guidelines and Common Keys](https://owin.org/spec/spec/CommonKeys.html).</span></span>

### <a name="request-data-owin-v100"></a><span data-ttu-id="b5b73-140">请求数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="b5b73-140">Request data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="b5b73-141">键</span><span class="sxs-lookup"><span data-stu-id="b5b73-141">Key</span></span>               | <span data-ttu-id="b5b73-142">值（类型）</span><span class="sxs-lookup"><span data-stu-id="b5b73-142">Value (type)</span></span> | <span data-ttu-id="b5b73-143">描述</span><span class="sxs-lookup"><span data-stu-id="b5b73-143">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b5b73-144">owin.RequestScheme</span><span class="sxs-lookup"><span data-stu-id="b5b73-144">owin.RequestScheme</span></span> | `String` |  |
| <span data-ttu-id="b5b73-145">owin.RequestMethod</span><span class="sxs-lookup"><span data-stu-id="b5b73-145">owin.RequestMethod</span></span>  | `String` | |    
| <span data-ttu-id="b5b73-146">owin.RequestPathBase</span><span class="sxs-lookup"><span data-stu-id="b5b73-146">owin.RequestPathBase</span></span>  | `String` | |    
| <span data-ttu-id="b5b73-147">owin.RequestPath</span><span class="sxs-lookup"><span data-stu-id="b5b73-147">owin.RequestPath</span></span> | `String` | |     
| <span data-ttu-id="b5b73-148">owin.RequestQueryString</span><span class="sxs-lookup"><span data-stu-id="b5b73-148">owin.RequestQueryString</span></span>  | `String` | |    
| <span data-ttu-id="b5b73-149">owin.RequestProtocol</span><span class="sxs-lookup"><span data-stu-id="b5b73-149">owin.RequestProtocol</span></span>  | `String` | |    
| <span data-ttu-id="b5b73-150">owin.RequestHeaders</span><span class="sxs-lookup"><span data-stu-id="b5b73-150">owin.RequestHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="b5b73-151">owin.RequestBody</span><span class="sxs-lookup"><span data-stu-id="b5b73-151">owin.RequestBody</span></span> | `Stream`  | |

### <a name="request-data-owin-v110"></a><span data-ttu-id="b5b73-152">请求数据 (OWIN v1.1.0)</span><span class="sxs-lookup"><span data-stu-id="b5b73-152">Request data (OWIN v1.1.0)</span></span>

| <span data-ttu-id="b5b73-153">键</span><span class="sxs-lookup"><span data-stu-id="b5b73-153">Key</span></span>               | <span data-ttu-id="b5b73-154">值（类型）</span><span class="sxs-lookup"><span data-stu-id="b5b73-154">Value (type)</span></span> | <span data-ttu-id="b5b73-155">描述</span><span class="sxs-lookup"><span data-stu-id="b5b73-155">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b5b73-156">owin.RequestId</span><span class="sxs-lookup"><span data-stu-id="b5b73-156">owin.RequestId</span></span> | `String` | <span data-ttu-id="b5b73-157">可选</span><span class="sxs-lookup"><span data-stu-id="b5b73-157">Optional</span></span> |

### <a name="response-data-owin-v100"></a><span data-ttu-id="b5b73-158">响应数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="b5b73-158">Response data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="b5b73-159">键</span><span class="sxs-lookup"><span data-stu-id="b5b73-159">Key</span></span>               | <span data-ttu-id="b5b73-160">值（类型）</span><span class="sxs-lookup"><span data-stu-id="b5b73-160">Value (type)</span></span> | <span data-ttu-id="b5b73-161">描述</span><span class="sxs-lookup"><span data-stu-id="b5b73-161">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b5b73-162">owin.ResponseStatusCode</span><span class="sxs-lookup"><span data-stu-id="b5b73-162">owin.ResponseStatusCode</span></span> | `int` | <span data-ttu-id="b5b73-163">可选</span><span class="sxs-lookup"><span data-stu-id="b5b73-163">Optional</span></span> |
| <span data-ttu-id="b5b73-164">owin.ResponseReasonPhrase</span><span class="sxs-lookup"><span data-stu-id="b5b73-164">owin.ResponseReasonPhrase</span></span> | `String` | <span data-ttu-id="b5b73-165">可选</span><span class="sxs-lookup"><span data-stu-id="b5b73-165">Optional</span></span> |
| <span data-ttu-id="b5b73-166">owin.ResponseHeaders</span><span class="sxs-lookup"><span data-stu-id="b5b73-166">owin.ResponseHeaders</span></span> | `IDictionary<string,string[]>`  | |
| <span data-ttu-id="b5b73-167">owin.ResponseBody</span><span class="sxs-lookup"><span data-stu-id="b5b73-167">owin.ResponseBody</span></span> | `Stream`  | |

### <a name="other-data-owin-v100"></a><span data-ttu-id="b5b73-168">其他数据 (OWIN v1.0.0)</span><span class="sxs-lookup"><span data-stu-id="b5b73-168">Other data (OWIN v1.0.0)</span></span>

| <span data-ttu-id="b5b73-169">键</span><span class="sxs-lookup"><span data-stu-id="b5b73-169">Key</span></span>               | <span data-ttu-id="b5b73-170">值（类型）</span><span class="sxs-lookup"><span data-stu-id="b5b73-170">Value (type)</span></span> | <span data-ttu-id="b5b73-171">描述</span><span class="sxs-lookup"><span data-stu-id="b5b73-171">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b5b73-172">owin.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="b5b73-172">owin.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="b5b73-173">owin.Version</span><span class="sxs-lookup"><span data-stu-id="b5b73-173">owin.Version</span></span>  | `String` | |   

### <a name="common-keys"></a><span data-ttu-id="b5b73-174">常用键</span><span class="sxs-lookup"><span data-stu-id="b5b73-174">Common keys</span></span>

| <span data-ttu-id="b5b73-175">键</span><span class="sxs-lookup"><span data-stu-id="b5b73-175">Key</span></span>               | <span data-ttu-id="b5b73-176">值（类型）</span><span class="sxs-lookup"><span data-stu-id="b5b73-176">Value (type)</span></span> | <span data-ttu-id="b5b73-177">描述</span><span class="sxs-lookup"><span data-stu-id="b5b73-177">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b5b73-178">ssl.ClientCertificate</span><span class="sxs-lookup"><span data-stu-id="b5b73-178">ssl.ClientCertificate</span></span> | `X509Certificate` |  |
| <span data-ttu-id="b5b73-179">ssl.LoadClientCertAsync</span><span class="sxs-lookup"><span data-stu-id="b5b73-179">ssl.LoadClientCertAsync</span></span>  | `Func<Task>` | |    
| <span data-ttu-id="b5b73-180">server.RemoteIpAddress</span><span class="sxs-lookup"><span data-stu-id="b5b73-180">server.RemoteIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="b5b73-181">server.RemotePort</span><span class="sxs-lookup"><span data-stu-id="b5b73-181">server.RemotePort</span></span> | `String` | |     
| <span data-ttu-id="b5b73-182">server.LocalIpAddress</span><span class="sxs-lookup"><span data-stu-id="b5b73-182">server.LocalIpAddress</span></span>  | `String` | |    
| <span data-ttu-id="b5b73-183">server.LocalPort</span><span class="sxs-lookup"><span data-stu-id="b5b73-183">server.LocalPort</span></span>  | `String` | |    
| <span data-ttu-id="b5b73-184">server.IsLocal</span><span class="sxs-lookup"><span data-stu-id="b5b73-184">server.IsLocal</span></span>  | `bool` | |    
| <span data-ttu-id="b5b73-185">server.OnSendingHeaders</span><span class="sxs-lookup"><span data-stu-id="b5b73-185">server.OnSendingHeaders</span></span>  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a><span data-ttu-id="b5b73-186">SendFiles v0.3.0</span><span class="sxs-lookup"><span data-stu-id="b5b73-186">SendFiles v0.3.0</span></span>

| <span data-ttu-id="b5b73-187">键</span><span class="sxs-lookup"><span data-stu-id="b5b73-187">Key</span></span>               | <span data-ttu-id="b5b73-188">值（类型）</span><span class="sxs-lookup"><span data-stu-id="b5b73-188">Value (type)</span></span> | <span data-ttu-id="b5b73-189">描述</span><span class="sxs-lookup"><span data-stu-id="b5b73-189">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b5b73-190">sendfile.SendAsync</span><span class="sxs-lookup"><span data-stu-id="b5b73-190">sendfile.SendAsync</span></span> | <span data-ttu-id="b5b73-191">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b5b73-191">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> | <span data-ttu-id="b5b73-192">每请求</span><span class="sxs-lookup"><span data-stu-id="b5b73-192">Per Request</span></span> |

### <a name="opaque-v030"></a><span data-ttu-id="b5b73-193">Opaque v0.3.0</span><span class="sxs-lookup"><span data-stu-id="b5b73-193">Opaque v0.3.0</span></span>

| <span data-ttu-id="b5b73-194">键</span><span class="sxs-lookup"><span data-stu-id="b5b73-194">Key</span></span>               | <span data-ttu-id="b5b73-195">值（类型）</span><span class="sxs-lookup"><span data-stu-id="b5b73-195">Value (type)</span></span> | <span data-ttu-id="b5b73-196">描述</span><span class="sxs-lookup"><span data-stu-id="b5b73-196">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b5b73-197">opaque.Version</span><span class="sxs-lookup"><span data-stu-id="b5b73-197">opaque.Version</span></span> | `String` |  |
| <span data-ttu-id="b5b73-198">opaque.Upgrade</span><span class="sxs-lookup"><span data-stu-id="b5b73-198">opaque.Upgrade</span></span> | `OpaqueUpgrade` | <span data-ttu-id="b5b73-199">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b5b73-199">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="b5b73-200">opaque.Stream</span><span class="sxs-lookup"><span data-stu-id="b5b73-200">opaque.Stream</span></span> | `Stream` |  |
| <span data-ttu-id="b5b73-201">opaque.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="b5b73-201">opaque.CallCancelled</span></span> | `CancellationToken` |  |

### <a name="websocket-v030"></a><span data-ttu-id="b5b73-202">WebSocket v0.3.0</span><span class="sxs-lookup"><span data-stu-id="b5b73-202">WebSocket v0.3.0</span></span>

| <span data-ttu-id="b5b73-203">键</span><span class="sxs-lookup"><span data-stu-id="b5b73-203">Key</span></span>               | <span data-ttu-id="b5b73-204">值（类型）</span><span class="sxs-lookup"><span data-stu-id="b5b73-204">Value (type)</span></span> | <span data-ttu-id="b5b73-205">描述</span><span class="sxs-lookup"><span data-stu-id="b5b73-205">Description</span></span> |
| ----------------- | ------------ | ----------- |
| <span data-ttu-id="b5b73-206">websocket.Version</span><span class="sxs-lookup"><span data-stu-id="b5b73-206">websocket.Version</span></span> | `String` |  |
| <span data-ttu-id="b5b73-207">websocket.Accept</span><span class="sxs-lookup"><span data-stu-id="b5b73-207">websocket.Accept</span></span> | `WebSocketAccept` | <span data-ttu-id="b5b73-208">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b5b73-208">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span> |
| <span data-ttu-id="b5b73-209">websocket.AcceptAlt</span><span class="sxs-lookup"><span data-stu-id="b5b73-209">websocket.AcceptAlt</span></span> |  | <span data-ttu-id="b5b73-210">非规范</span><span class="sxs-lookup"><span data-stu-id="b5b73-210">Non-spec</span></span> |
| <span data-ttu-id="b5b73-211">websocket.SubProtocol</span><span class="sxs-lookup"><span data-stu-id="b5b73-211">websocket.SubProtocol</span></span> | `String` | <span data-ttu-id="b5b73-212">请参阅 [RFC6455 4.2.2 节](https://tools.ietf.org/html/rfc6455#section-4.2.2)步骤 5.5</span><span class="sxs-lookup"><span data-stu-id="b5b73-212">See [RFC6455 Section 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2) Step 5.5</span></span> |
| <span data-ttu-id="b5b73-213">websocket.SendAsync</span><span class="sxs-lookup"><span data-stu-id="b5b73-213">websocket.SendAsync</span></span> | `WebSocketSendAsync` | <span data-ttu-id="b5b73-214">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b5b73-214">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="b5b73-215">websocket.ReceiveAsync</span><span class="sxs-lookup"><span data-stu-id="b5b73-215">websocket.ReceiveAsync</span></span> | `WebSocketReceiveAsync` | <span data-ttu-id="b5b73-216">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b5b73-216">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="b5b73-217">websocket.CloseAsync</span><span class="sxs-lookup"><span data-stu-id="b5b73-217">websocket.CloseAsync</span></span> | `WebSocketCloseAsync` | <span data-ttu-id="b5b73-218">请参阅[委托签名](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span><span class="sxs-lookup"><span data-stu-id="b5b73-218">See [delegate signature](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)</span></span>  |
| <span data-ttu-id="b5b73-219">websocket.CallCancelled</span><span class="sxs-lookup"><span data-stu-id="b5b73-219">websocket.CallCancelled</span></span> | `CancellationToken` |  |
| <span data-ttu-id="b5b73-220">websocket.ClientCloseStatus</span><span class="sxs-lookup"><span data-stu-id="b5b73-220">websocket.ClientCloseStatus</span></span> | `int` | <span data-ttu-id="b5b73-221">可选</span><span class="sxs-lookup"><span data-stu-id="b5b73-221">Optional</span></span> |
| <span data-ttu-id="b5b73-222">websocket.ClientCloseDescription</span><span class="sxs-lookup"><span data-stu-id="b5b73-222">websocket.ClientCloseDescription</span></span> | `String` | <span data-ttu-id="b5b73-223">可选</span><span class="sxs-lookup"><span data-stu-id="b5b73-223">Optional</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="b5b73-224">其他资源</span><span class="sxs-lookup"><span data-stu-id="b5b73-224">Additional resources</span></span>

* [<span data-ttu-id="b5b73-225">中间件</span><span class="sxs-lookup"><span data-stu-id="b5b73-225">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="b5b73-226">服务器</span><span class="sxs-lookup"><span data-stu-id="b5b73-226">Servers</span></span>](xref:fundamentals/servers/index)
