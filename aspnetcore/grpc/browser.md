---
title: 在浏览器应用中使用 gRPC
author: jamesnk
description: 了解如何配置 ASP.NET Core 上的 gRPC 服务，以使其可以从使用 gRPC-Web 的浏览器应用中进行调用。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 0967a70b498156d9c4ea8818ee1c80b37d9f2d87
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217474"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="8791d-103">在浏览器应用中使用 gRPC</span><span class="sxs-lookup"><span data-stu-id="8791d-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="8791d-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="8791d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="8791d-105">了解如何配置现有 ASP.NET Core gRPC 服务，以使其可以从使用 [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) 协议的浏览器应用中进行调用。</span><span class="sxs-lookup"><span data-stu-id="8791d-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="8791d-106">gRPC-Web 允许浏览器 JavaScript 和 Blazor 应用调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="8791d-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="8791d-107">无法从基于浏览器的应用中调用 HTTP/2 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="8791d-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="8791d-108">可将托管于 ASP.NET Core 中的 gRPC 服务配置为随 HTTP/2 gRPC 一起支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="8791d-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="8791d-109">有关将 gRPC 服务添加到现有 ASP.NET Core 应用的说明，请参阅[将 gRPC 服务添加到 ASP.NET Core 应用](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app)。</span><span class="sxs-lookup"><span data-stu-id="8791d-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="8791d-110">有关创建 gRPC 项目的说明，请参阅 <xref:tutorials/grpc/grpc-start>。</span><span class="sxs-lookup"><span data-stu-id="8791d-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="8791d-111">.NET Core 中的 gRPC-Web 与Envoy</span><span class="sxs-lookup"><span data-stu-id="8791d-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="8791d-112">有两种方式可将 gRPC-Web 添加到 ASP.NET Core 应用中：</span><span class="sxs-lookup"><span data-stu-id="8791d-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="8791d-113">在 ASP.NET Core 中同时支持 gRPC-Web 和 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="8791d-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="8791d-114">此选项会使用 `Grpc.AspNetCore.Web` 包提供的中间件。</span><span class="sxs-lookup"><span data-stu-id="8791d-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="8791d-115">使用 [Envoy 代理](https://www.envoyproxy.io/)的 gRPC-Web 支持将 gRPC-Web 转换为 gRPC HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="8791d-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="8791d-116">转换后的调用随后会转发给 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="8791d-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="8791d-117">每种方法既有优点，也有缺点。</span><span class="sxs-lookup"><span data-stu-id="8791d-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="8791d-118">如果应用的环境已将 Envoy 用作代理，则也可以使用 Envoy 提供 gRPC-Web 支持。</span><span class="sxs-lookup"><span data-stu-id="8791d-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="8791d-119">对于仅需要 ASP.NET Core 的 gRPC-Web 的基本解决方案，`Grpc.AspNetCore.Web` 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="8791d-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="8791d-120">配置 ASP.NET Core 中的 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="8791d-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="8791d-121">可将托管于 ASP.NET Core 中的 gRPC 服务配置为随 HTTP/2 gRPC 一起支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="8791d-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="8791d-122">gRPC-Web 不需要对服务进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="8791d-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="8791d-123">唯一的修改是启动配置。</span><span class="sxs-lookup"><span data-stu-id="8791d-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="8791d-124">若要使用 ASP.NET Core gRPC 服务启用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="8791d-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="8791d-125">添加对 [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) 包的引用。</span><span class="sxs-lookup"><span data-stu-id="8791d-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="8791d-126">配置应用以使用 gRPC-Web，方法是将 `UseGrpcWeb` 和 `EnableGrpcWeb` 添加到 Startup.cs：</span><span class="sxs-lookup"><span data-stu-id="8791d-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="8791d-127">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8791d-127">The preceding code:</span></span>

* <span data-ttu-id="8791d-128">在路由之后、终结点之前添加 gRPC-Web 中间件 `UseGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="8791d-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="8791d-129">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `EnableGrpcWeb` 的 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="8791d-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="8791d-130">或者，可以配置 gRPC-Web 中间件，使所有服务在默认情况下都支持 gRPC-Web，而不需要 `EnableGrpcWeb`。</span><span class="sxs-lookup"><span data-stu-id="8791d-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="8791d-131">在添加中间件时指定 `new GrpcWebOptions { DefaultEnabled = true }`。</span><span class="sxs-lookup"><span data-stu-id="8791d-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="8791d-132">存在一个已知问题，它导致在 .NET Core 3.x 中[由 HTTP.sys 托管](xref:fundamentals/servers/httpsys)时 gRPC-Web 会失败。</span><span class="sxs-lookup"><span data-stu-id="8791d-132">There is a known issue that causes gRPC-Web to fail when [hosted by HTTP.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="8791d-133">[此处](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)提供了一种变通方法，可用来获取在 HTTP.sys 上运行的 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="8791d-133">A workaround to get gRPC-Web working on HTTP.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="8791d-134">gRPC-Web 和 CORS</span><span class="sxs-lookup"><span data-stu-id="8791d-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="8791d-135">浏览器安全性可防止网页向不处理网页的域发送请求。</span><span class="sxs-lookup"><span data-stu-id="8791d-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="8791d-136">此限制适用于使用浏览器应用发出 gRPC-Web 调用。</span><span class="sxs-lookup"><span data-stu-id="8791d-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="8791d-137">例如，由 `https://www.contoso.com` 提供服务的浏览器应用对托管于 `https://services.contoso.com` 上的 gRPC-Web 服务的调用会被阻止。</span><span class="sxs-lookup"><span data-stu-id="8791d-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="8791d-138">跨域资源共享 (CORS) 可用于放宽此限制。</span><span class="sxs-lookup"><span data-stu-id="8791d-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="8791d-139">若要允许浏览器应用进行跨域 gRPC-Web 调用，请[在 ASP.NET Core 中设置 CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="8791d-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="8791d-140">使用内置 CORS 支持，并使用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> 公开特定于 gRPC 的标头。</span><span class="sxs-lookup"><span data-stu-id="8791d-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="8791d-141">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8791d-141">The preceding code:</span></span>

* <span data-ttu-id="8791d-142">调用 `AddCors` 以添加 CORS 服务，并配置公开特定于 gRPC 的标头的 CORS 策略。</span><span class="sxs-lookup"><span data-stu-id="8791d-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="8791d-143">调用 `UseCors` 以在路由之后、终结点之前添加 CORS 中间件。</span><span class="sxs-lookup"><span data-stu-id="8791d-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="8791d-144">指定 `endpoints.MapGrpcService<GreeterService>()` 方法支持带有 `RequiresCors`的 CORS。</span><span class="sxs-lookup"><span data-stu-id="8791d-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="8791d-145">gRPC-Web 和流式处理</span><span class="sxs-lookup"><span data-stu-id="8791d-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="8791d-146">HTTP/2 上的传统 gRPC 支持所有方向的流式处理。</span><span class="sxs-lookup"><span data-stu-id="8791d-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="8791d-147">gRPC-Web 对流式处理提供有限的支持：</span><span class="sxs-lookup"><span data-stu-id="8791d-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="8791d-148">gRPC-Web 浏览器客户端不支持调用客户端流式处理和双向流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="8791d-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="8791d-149">托管在 Azure 应用服务和 IIS 上的 ASP.NET Core gRPC 服务不支持双向流式处理。</span><span class="sxs-lookup"><span data-stu-id="8791d-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="8791d-150">使用 gRPC 时，仅建议使用一元方法和服务器流式处理方法。</span><span class="sxs-lookup"><span data-stu-id="8791d-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="8791d-151">从浏览器调用 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="8791d-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="8791d-152">浏览器应用可以使用 gRPC-Web 来调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="8791d-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="8791d-153">使用 gRPC-Web 从浏览器调用 gRPC 服务时，存在一些要求和限制：</span><span class="sxs-lookup"><span data-stu-id="8791d-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="8791d-154">服务器必须已配置为支持 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="8791d-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="8791d-155">不支持客户端流式处理和双向流式处理调用。</span><span class="sxs-lookup"><span data-stu-id="8791d-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="8791d-156">支持服务器流式处理。</span><span class="sxs-lookup"><span data-stu-id="8791d-156">Server streaming is supported.</span></span>
* <span data-ttu-id="8791d-157">在其他域上调用 gRPC 服务需要在服务器上配置 [CORS](xref:security/cors)。</span><span class="sxs-lookup"><span data-stu-id="8791d-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="8791d-158">JavaScript gRPC-Web 客户端</span><span class="sxs-lookup"><span data-stu-id="8791d-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="8791d-159">存在 JavaScript gRPC-Web 客户端。</span><span class="sxs-lookup"><span data-stu-id="8791d-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="8791d-160">有关如何从 JavaScript 使用 gRPC-Web 的说明，请参阅[使用 gRPC-Web 编写 JavaScript 客户端代码](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)。</span><span class="sxs-lookup"><span data-stu-id="8791d-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="8791d-161">使用 .NET gRPC 客户端配置 gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="8791d-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="8791d-162">可以将 .NET gRPC 客户端配置为发出 gRPC-Web 调用。</span><span class="sxs-lookup"><span data-stu-id="8791d-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="8791d-163">这对于托管在浏览器中且具有相同 JavaScript 代码 HTTP 限制的 [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) 应用来说非常有用。</span><span class="sxs-lookup"><span data-stu-id="8791d-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="8791d-164">使用 .NET 客户端调用 gRPC-Web [与 HTTP/2 gRPC 相同](xref:grpc/client)。</span><span class="sxs-lookup"><span data-stu-id="8791d-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="8791d-165">唯一的修改是创建通道的方式。</span><span class="sxs-lookup"><span data-stu-id="8791d-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="8791d-166">若要使用 gRPC-Web：</span><span class="sxs-lookup"><span data-stu-id="8791d-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="8791d-167">添加对 [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) 包的引用。</span><span class="sxs-lookup"><span data-stu-id="8791d-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="8791d-168">确保对 [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 包的引用为 2.29.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8791d-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="8791d-169">配置通道以使用 `GrpcWebHandler`：</span><span class="sxs-lookup"><span data-stu-id="8791d-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="8791d-170">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8791d-170">The preceding code:</span></span>

* <span data-ttu-id="8791d-171">配置通道以使用 gRPC-Web。</span><span class="sxs-lookup"><span data-stu-id="8791d-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="8791d-172">创建一个客户端并使用通道发出调用。</span><span class="sxs-lookup"><span data-stu-id="8791d-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="8791d-173">`GrpcWebHandler` 具有以下配置选项：</span><span class="sxs-lookup"><span data-stu-id="8791d-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="8791d-174">**InnerHandler**：发出 gRPC HTTP 请求的基础 <xref:System.Net.Http.HttpMessageHandler>，例如 `HttpClientHandler`。</span><span class="sxs-lookup"><span data-stu-id="8791d-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="8791d-175">GrpcWebMode：枚举类型，指定 gRPC HTTP 请求 `Content-Type` 是 `application/grpc-web` 还是 `application/grpc-web-text`。</span><span class="sxs-lookup"><span data-stu-id="8791d-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="8791d-176">`GrpcWebMode.GrpcWeb` 配置不进行编码即发送的内容。</span><span class="sxs-lookup"><span data-stu-id="8791d-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="8791d-177">默认值。</span><span class="sxs-lookup"><span data-stu-id="8791d-177">Default value.</span></span>
    * <span data-ttu-id="8791d-178">`GrpcWebMode.GrpcWebText` 配置需进行 base64 编码的内容。</span><span class="sxs-lookup"><span data-stu-id="8791d-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="8791d-179">对于浏览器中的服务器流式处理调用是必需的。</span><span class="sxs-lookup"><span data-stu-id="8791d-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="8791d-180">**HttpVersion**：HTTP 协议 `Version` 用于在基础 gRPC HTTP 请求上设置 [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version)。</span><span class="sxs-lookup"><span data-stu-id="8791d-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="8791d-181">gRPC-Web 不需要特定版本，且除非指定，否则不会替代默认版本。</span><span class="sxs-lookup"><span data-stu-id="8791d-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8791d-182">生成的 gRPC 客户端具有用于调用一元方法的同步和异步方法。</span><span class="sxs-lookup"><span data-stu-id="8791d-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="8791d-183">例如，`SayHello` 是同步的，而 `SayHelloAsync` 是异步的。</span><span class="sxs-lookup"><span data-stu-id="8791d-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="8791d-184">在 Blazor WebAssembly 应用中调用同步方法将导致应用无响应。</span><span class="sxs-lookup"><span data-stu-id="8791d-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="8791d-185">必须始终在 Blazor WebAssembly 中使用异步方法。</span><span class="sxs-lookup"><span data-stu-id="8791d-185">Async methods must always be used in Blazor WebAssembly.</span></span>

### <a name="use-grpc-client-factory-with-grpc-web"></a><span data-ttu-id="8791d-186">将 gRPC-Web 与 gRPC 客户端工厂一起使用</span><span class="sxs-lookup"><span data-stu-id="8791d-186">Use gRPC client factory with gRPC-Web</span></span>

<span data-ttu-id="8791d-187">可以使用 gRPC 与 [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory) 的集成来创建与 gRPC-Web 兼容的 .NET 客户端。</span><span class="sxs-lookup"><span data-stu-id="8791d-187">A gRPC-Web compatible .NET client can be created using gRPC's integration with [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span></span>

<span data-ttu-id="8791d-188">若要将 gRPC-Web 与客户端工厂一起使用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="8791d-188">To use gRPC-Web with client factory:</span></span>

* <span data-ttu-id="8791d-189">将包引用添加到以下包的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="8791d-189">Add package references to the project file for the following packages:</span></span>
  * [<span data-ttu-id="8791d-190">Grpc.Net.Client.Web</span><span class="sxs-lookup"><span data-stu-id="8791d-190">Grpc.Net.Client.Web</span></span>](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [<span data-ttu-id="8791d-191">Grpc.Net.ClientFactory</span><span class="sxs-lookup"><span data-stu-id="8791d-191">Grpc.Net.ClientFactory</span></span>](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* <span data-ttu-id="8791d-192">使用泛型 `AddGrpcClient` 扩展方法，通过依赖项注入 (DI) 注册 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="8791d-192">Register a gRPC client with dependency injection (DI) using the generic `AddGrpcClient` extension method.</span></span> <span data-ttu-id="8791d-193">在 Blazor WebAssembly 应用中，服务在 `Program.cs` 中通过 DI 注册。</span><span class="sxs-lookup"><span data-stu-id="8791d-193">In a Blazor WebAssembly app, services are registered with DI in `Program.cs`.</span></span>
* <span data-ttu-id="8791d-194">使用 <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> 扩展方法配置 `GrpcWebHandler`。</span><span class="sxs-lookup"><span data-stu-id="8791d-194">Configure `GrpcWebHandler` using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

<span data-ttu-id="8791d-195">有关详细信息，请参阅 <xref:grpc/clientfactory>。</span><span class="sxs-lookup"><span data-stu-id="8791d-195">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8791d-196">其他资源</span><span class="sxs-lookup"><span data-stu-id="8791d-196">Additional resources</span></span>

* [<span data-ttu-id="8791d-197">适用于 Web 客户端 GitHub 项目的 gRPC</span><span class="sxs-lookup"><span data-stu-id="8791d-197">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
