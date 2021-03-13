---
title: ASP.NET Core 中的响应缓存中间件
author: rick-anderson
description: 了解如何在 ASP.NET Core 中配置和使用响应缓存中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/middleware
ms.openlocfilehash: 11473bbf8b4e2d67d15798a5d87ee01761682f9a
ms.sourcegitcommit: 07e7ee573fe4e12be93249a385db745d714ff6ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103413465"
---
# <a name="response-caching-middleware-in-aspnet-core"></a><span data-ttu-id="607f3-103">ASP.NET Core 中的响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="607f3-103">Response Caching Middleware in ASP.NET Core</span></span>

<span data-ttu-id="607f3-104">作者：[John Luo](https://github.com/JunTaoLuo)</span><span class="sxs-lookup"><span data-stu-id="607f3-104">By [John Luo](https://github.com/JunTaoLuo)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="607f3-105">本文介绍如何在 ASP.NET Core 应用程序中配置响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="607f3-105">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="607f3-106">中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-106">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="607f3-107">有关 HTTP 缓存和属性的介绍 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，请参阅 [响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="607f3-107">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="607f3-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="607f3-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="607f3-109">配置</span><span class="sxs-lookup"><span data-stu-id="607f3-109">Configuration</span></span>

<span data-ttu-id="607f3-110">响应缓存中间件可通过共享框架隐式地用于 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="607f3-110">Response Caching Middleware is implicitly available for ASP.NET Core apps via the shared framework.</span></span>

<span data-ttu-id="607f3-111">在中 `Startup.ConfigureServices` ，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="607f3-111">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="607f3-112">将应用程序配置为将中间件与 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 扩展方法一起使用，该扩展方法将中间件添加到中的请求处理管道 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="607f3-112">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <span data-ttu-id="607f3-113"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A><xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>使用[CORS 中间件](xref:security/cors)之前，必须先调用。</span><span class="sxs-lookup"><span data-stu-id="607f3-113"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using [CORS middleware](xref:security/cors).</span></span>

<span data-ttu-id="607f3-114">示例应用添加标头以在后续请求时控制缓存：</span><span class="sxs-lookup"><span data-stu-id="607f3-114">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="607f3-115">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)：将可缓存的响应缓存多达10秒。</span><span class="sxs-lookup"><span data-stu-id="607f3-115">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="607f3-116">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：仅当后续请求的接受编码标头与原始请求的 [接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4) 标头匹配时，才将中间件配置为提供缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-116">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

<span data-ttu-id="607f3-117">前面的标头不会写入响应，并在控制器、操作或页上被重写 Razor ：</span><span class="sxs-lookup"><span data-stu-id="607f3-117">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="607f3-118">具有 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="607f3-118">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="607f3-119">即使未设置属性，这也适用。</span><span class="sxs-lookup"><span data-stu-id="607f3-119">This applies even if a property isn't set.</span></span> <span data-ttu-id="607f3-120">例如，省略 [VaryByHeader](./response.md#vary) 属性将导致从响应中删除相应的标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-120">For example, omitting the [VaryByHeader](./response.md#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="607f3-121">响应缓存中间件仅缓存服务器响应，导致 200 (正常) 状态代码。</span><span class="sxs-lookup"><span data-stu-id="607f3-121">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="607f3-122">中间件将忽略任何其他响应，包括 [错误页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="607f3-122">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="607f3-123">包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-123">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="607f3-124">有关中间件如何确定响应是否可缓存的详细信息，请参阅 [缓存的条件](#conditions-for-caching) 。</span><span class="sxs-lookup"><span data-stu-id="607f3-124">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="607f3-125">选项</span><span class="sxs-lookup"><span data-stu-id="607f3-125">Options</span></span>

<span data-ttu-id="607f3-126">响应缓存选项如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="607f3-126">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="607f3-127">选项</span><span class="sxs-lookup"><span data-stu-id="607f3-127">Option</span></span> | <span data-ttu-id="607f3-128">描述</span><span class="sxs-lookup"><span data-stu-id="607f3-128">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="607f3-129">响应正文的最大可缓存大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="607f3-129">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="607f3-130">默认值为 `64 * 1024 * 1024` (64 MB) 。</span><span class="sxs-lookup"><span data-stu-id="607f3-130">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="607f3-131">响应缓存中间件的大小限制（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="607f3-131">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="607f3-132">默认值为 `100 * 1024 * 1024` (100 MB) 。</span><span class="sxs-lookup"><span data-stu-id="607f3-132">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="607f3-133">确定是否将响应缓存在区分大小写的路径上。</span><span class="sxs-lookup"><span data-stu-id="607f3-133">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="607f3-134">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="607f3-134">The default value is `false`.</span></span> |

<span data-ttu-id="607f3-135">下面的示例将中间件配置为：</span><span class="sxs-lookup"><span data-stu-id="607f3-135">The following example configures the middleware to:</span></span>

* <span data-ttu-id="607f3-136">大小小于或等于1024字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-136">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="607f3-137">将响应存储为区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="607f3-137">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="607f3-138">例如， `/page1` 和 `/Page1` 分别存储。</span><span class="sxs-lookup"><span data-stu-id="607f3-138">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="607f3-139">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="607f3-139">VaryByQueryKeys</span></span>

<span data-ttu-id="607f3-140">使用 MVC/web API 控制器或 Razor 页面页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性指定为响应缓存设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="607f3-140">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="607f3-141">`[ResponseCache]`严格要求中间件的属性的唯一参数是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，它不对应于实际 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-141">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="607f3-142">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="607f3-142">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="607f3-143">如果不使用 `[ResponseCache]` 属性，响应缓存可能会随而变化 `VaryByQueryKeys` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-143">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="607f3-144"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：</span><span class="sxs-lookup"><span data-stu-id="607f3-144">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="607f3-145">使用等于中的单个值 `*` 会 `VaryByQueryKeys` 根据所有请求查询参数改变缓存。</span><span class="sxs-lookup"><span data-stu-id="607f3-145">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="607f3-146">响应缓存中间件使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="607f3-146">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="607f3-147">下表提供了有关影响响应缓存的 HTTP 标头的信息。</span><span class="sxs-lookup"><span data-stu-id="607f3-147">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="607f3-148">标头</span><span class="sxs-lookup"><span data-stu-id="607f3-148">Header</span></span> | <span data-ttu-id="607f3-149">详细信息</span><span class="sxs-lookup"><span data-stu-id="607f3-149">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="607f3-150">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-150">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="607f3-151">中间件仅考虑用缓存指令标记的缓存响应 `public` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-151">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="607f3-152">具有以下参数的控件缓存：</span><span class="sxs-lookup"><span data-stu-id="607f3-152">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="607f3-153">最大期限</span><span class="sxs-lookup"><span data-stu-id="607f3-153">max-age</span></span></li><li><span data-ttu-id="607f3-154">最大过期&#8224;</span><span class="sxs-lookup"><span data-stu-id="607f3-154">max-stale&#8224;</span></span></li><li><span data-ttu-id="607f3-155">最小-新</span><span class="sxs-lookup"><span data-stu-id="607f3-155">min-fresh</span></span></li><li><span data-ttu-id="607f3-156">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="607f3-156">must-revalidate</span></span></li><li><span data-ttu-id="607f3-157">no-cache</span><span class="sxs-lookup"><span data-stu-id="607f3-157">no-cache</span></span></li><li><span data-ttu-id="607f3-158">无-商店</span><span class="sxs-lookup"><span data-stu-id="607f3-158">no-store</span></span></li><li><span data-ttu-id="607f3-159">仅限-缓存</span><span class="sxs-lookup"><span data-stu-id="607f3-159">only-if-cached</span></span></li><li><span data-ttu-id="607f3-160">private</span><span class="sxs-lookup"><span data-stu-id="607f3-160">private</span></span></li><li><span data-ttu-id="607f3-161">public</span><span class="sxs-lookup"><span data-stu-id="607f3-161">public</span></span></li><li><span data-ttu-id="607f3-162">s-maxage</span><span class="sxs-lookup"><span data-stu-id="607f3-162">s-maxage</span></span></li><li><span data-ttu-id="607f3-163">代理重新验证&#8225;</span><span class="sxs-lookup"><span data-stu-id="607f3-163">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="607f3-164">&#8224;如果未指定任何限制 `max-stale` ，则中间件不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="607f3-164">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="607f3-165">&#8225;`proxy-revalidate` 的效果与相同 `must-revalidate` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-165">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="607f3-166">有关详细信息，请参阅 [RFC 7231： Request Cache-Control 指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="607f3-166">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="607f3-167">`Pragma: no-cache`请求中的标头将产生与相同的效果 `Cache-Control: no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-167">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="607f3-168">标头中的相关指令 `Cache-Control` （如果存在）将重写此标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-168">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="607f3-169">考虑向后兼容 HTTP/1.0。</span><span class="sxs-lookup"><span data-stu-id="607f3-169">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="607f3-170">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-170">The response isn't cached if the header exists.</span></span> <span data-ttu-id="607f3-171">请求处理管道中设置一个或多个的任何中间件 cookie 会阻止响应缓存中间件缓存响应 (例如， [ cookie 基于的 TempData 提供程序](xref:fundamentals/app-state#tempdata)) 。</span><span class="sxs-lookup"><span data-stu-id="607f3-171">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="607f3-172">`Vary`标头用于根据另一个标头改变缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-172">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="607f3-173">例如，通过包含标头来缓存响应， `Vary: Accept-Encoding` 此标头将使用标头和单独的请求来缓存响应 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-173">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="607f3-174">永远不会存储标头值 `*` 为的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-174">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="607f3-175">除非由其他标头重写，否则不会存储或检索此标头过时的响应 `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-175">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="607f3-176">如果值不为 `*` ，并且响应的与提供的任何值都不匹配，则将从缓存中提供完整响应 `ETag` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-176">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="607f3-177">否则，将为 304 (修改) 响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-177">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="607f3-178">如果 `If-None-Match` 标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-178">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="607f3-179">否则，将提供 *304-未修改* 响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-179">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="607f3-180">从缓存提供时， `Date` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-180">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="607f3-181">从缓存提供时， `Content-Length` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-181">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="607f3-182">`Age`忽略原始响应中发送的标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-182">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="607f3-183">中间件在为缓存的响应提供服务时计算一个新值。</span><span class="sxs-lookup"><span data-stu-id="607f3-183">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="607f3-184">缓存遵从请求 Cache-Control 指令</span><span class="sxs-lookup"><span data-stu-id="607f3-184">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="607f3-185">中间件遵循 [HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。</span><span class="sxs-lookup"><span data-stu-id="607f3-185">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="607f3-186">规则要求使用缓存来服从 `Cache-Control` 客户端发送的有效标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-186">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="607f3-187">在规范下，客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-187">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="607f3-188">目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="607f3-188">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="607f3-189">为了更好地控制缓存行为，浏览 ASP.NET Core 的其他缓存功能。</span><span class="sxs-lookup"><span data-stu-id="607f3-189">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="607f3-190">请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="607f3-190">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="607f3-191">疑难解答</span><span class="sxs-lookup"><span data-stu-id="607f3-191">Troubleshooting</span></span>

<span data-ttu-id="607f3-192">如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。</span><span class="sxs-lookup"><span data-stu-id="607f3-192">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="607f3-193">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-193">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="607f3-194">启用 [日志记录](xref:fundamentals/logging/index) 以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="607f3-194">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="607f3-195">在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-195">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="607f3-196">例如，浏览器可以在 `Cache-Control` `no-cache` 刷新页面时将标题设置为或 `max-age=0` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-196">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="607f3-197">以下工具可以显式设置请求标头，并优先于测试缓存：</span><span class="sxs-lookup"><span data-stu-id="607f3-197">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="607f3-198">Fiddler</span><span class="sxs-lookup"><span data-stu-id="607f3-198">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="607f3-199">Postman</span><span class="sxs-lookup"><span data-stu-id="607f3-199">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="607f3-200">缓存条件</span><span class="sxs-lookup"><span data-stu-id="607f3-200">Conditions for caching</span></span>

* <span data-ttu-id="607f3-201">请求必须使用 200 (良好) 状态代码来生成服务器响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-201">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="607f3-202">请求方法必须为 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="607f3-202">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="607f3-203">在中 `Startup.Configure` ，必须将响应缓存中间件置于需要缓存的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="607f3-203">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="607f3-204">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="607f3-204">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="607f3-205">`Authorization`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="607f3-205">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="607f3-206">`Cache-Control` 标头参数必须是有效的，并且响应必须标记 `public` 且未标记 `private` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-206">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="607f3-207">`Pragma: no-cache`如果标头不存在，则标头不得出现 `Cache-Control` ，因为标头会 `Cache-Control` `Pragma` 在存在时覆盖标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-207">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="607f3-208">`Set-Cookie`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="607f3-208">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="607f3-209">`Vary` 标头参数必须有效并且不等于 `*` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-209">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="607f3-210">`Content-Length`如果设置) 必须与响应正文的大小匹配，则标头值 (。</span><span class="sxs-lookup"><span data-stu-id="607f3-210">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="607f3-211"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>不使用。</span><span class="sxs-lookup"><span data-stu-id="607f3-211">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="607f3-212">`Expires`标头和 `max-age` 和缓存指令指定的响应不能过时 `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-212">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="607f3-213">响应缓冲必须成功。</span><span class="sxs-lookup"><span data-stu-id="607f3-213">Response buffering must be successful.</span></span> <span data-ttu-id="607f3-214">响应的大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。</span><span class="sxs-lookup"><span data-stu-id="607f3-214">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="607f3-215">响应的正文大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。</span><span class="sxs-lookup"><span data-stu-id="607f3-215">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="607f3-216">必须根据 [RFC 7234](https://tools.ietf.org/html/rfc7234) 规范来缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-216">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="607f3-217">例如， `no-store` 指令在请求或响应标头字段中不得存在。</span><span class="sxs-lookup"><span data-stu-id="607f3-217">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="607f3-218">有关详细信息，请参阅 *第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234) 的缓存中。</span><span class="sxs-lookup"><span data-stu-id="607f3-218">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="607f3-219">用于生成安全令牌的防伪系统，以防止跨站点请求伪造 (CSRF) 攻击将 `Cache-Control` 和 `Pragma` 标头设置为， `no-cache` 以便不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-219">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="607f3-220">有关如何为 HTML 窗体元素禁用防伪标记的信息，请参阅 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="607f3-220">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="607f3-221">其他资源</span><span class="sxs-lookup"><span data-stu-id="607f3-221">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="607f3-222">本文介绍如何在 ASP.NET Core 应用程序中配置响应缓存中间件。</span><span class="sxs-lookup"><span data-stu-id="607f3-222">This article explains how to configure Response Caching Middleware in an ASP.NET Core app.</span></span> <span data-ttu-id="607f3-223">中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-223">The middleware determines when responses are cacheable, stores responses, and serves responses from cache.</span></span> <span data-ttu-id="607f3-224">有关 HTTP 缓存和属性的介绍 [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) ，请参阅 [响应缓存](xref:performance/caching/response)。</span><span class="sxs-lookup"><span data-stu-id="607f3-224">For an introduction to HTTP caching and the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute, see [Response Caching](xref:performance/caching/response).</span></span>

<span data-ttu-id="607f3-225">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="607f3-225">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/middleware/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration"></a><span data-ttu-id="607f3-226">配置</span><span class="sxs-lookup"><span data-stu-id="607f3-226">Configuration</span></span>

<span data-ttu-id="607f3-227">使用 [AspNetCore 元包](xref:fundamentals/metapackage-app) 或添加对 [AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="607f3-227">Use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) package.</span></span>

<span data-ttu-id="607f3-228">在中 `Startup.ConfigureServices` ，将响应缓存中间件添加到服务集合：</span><span class="sxs-lookup"><span data-stu-id="607f3-228">In `Startup.ConfigureServices`, add the Response Caching Middleware to the service collection:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

<span data-ttu-id="607f3-229">将应用程序配置为将中间件与 <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> 扩展方法一起使用，该扩展方法将中间件添加到中的请求处理管道 `Startup.Configure` ：</span><span class="sxs-lookup"><span data-stu-id="607f3-229">Configure the app to use the middleware with the <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> extension method, which adds the middleware to the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

<span data-ttu-id="607f3-230">示例应用添加标头以在后续请求时控制缓存：</span><span class="sxs-lookup"><span data-stu-id="607f3-230">The sample app adds headers to control caching on subsequent requests:</span></span>

* <span data-ttu-id="607f3-231">[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)：将可缓存的响应缓存多达10秒。</span><span class="sxs-lookup"><span data-stu-id="607f3-231">[Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): Caches cacheable responses for up to 10 seconds.</span></span>
* <span data-ttu-id="607f3-232">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4)：仅当后续请求的接受编码标头与原始请求的 [接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4) 标头匹配时，才将中间件配置为提供缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-232">[Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): Configures the middleware to serve a cached response only if the [Accept-Encoding](https://tools.ietf.org/html/rfc7231#section-5.3.4) header of subsequent requests matches that of the original request.</span></span>

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

<span data-ttu-id="607f3-233">前面的标头不会写入响应，并在控制器、操作或页上被重写 Razor ：</span><span class="sxs-lookup"><span data-stu-id="607f3-233">The preceding headers are not written to the response and are overridden when a controller, action, or Razor Page:</span></span>

* <span data-ttu-id="607f3-234">具有 [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="607f3-234">Has a [[ResponseCache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute.</span></span> <span data-ttu-id="607f3-235">即使未设置属性，这也适用。</span><span class="sxs-lookup"><span data-stu-id="607f3-235">This applies even if a property isn't set.</span></span> <span data-ttu-id="607f3-236">例如，省略 [VaryByHeader](./response.md#vary) 属性将导致从响应中删除相应的标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-236">For example, omitting the [VaryByHeader](./response.md#vary) property will cause the corresponding header to be removed from the response.</span></span>

<span data-ttu-id="607f3-237">响应缓存中间件仅缓存服务器响应，导致 200 (正常) 状态代码。</span><span class="sxs-lookup"><span data-stu-id="607f3-237">Response Caching Middleware only caches server responses that result in a 200 (OK) status code.</span></span> <span data-ttu-id="607f3-238">中间件将忽略任何其他响应，包括 [错误页](xref:fundamentals/error-handling)。</span><span class="sxs-lookup"><span data-stu-id="607f3-238">Any other responses, including [error pages](xref:fundamentals/error-handling), are ignored by the middleware.</span></span>

> [!WARNING]
> <span data-ttu-id="607f3-239">包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-239">Responses containing content for authenticated clients must be marked as not cacheable to prevent the middleware from storing and serving those responses.</span></span> <span data-ttu-id="607f3-240">有关中间件如何确定响应是否可缓存的详细信息，请参阅 [缓存的条件](#conditions-for-caching) 。</span><span class="sxs-lookup"><span data-stu-id="607f3-240">See [Conditions for caching](#conditions-for-caching) for details on how the middleware determines if a response is cacheable.</span></span>

## <a name="options"></a><span data-ttu-id="607f3-241">选项</span><span class="sxs-lookup"><span data-stu-id="607f3-241">Options</span></span>

<span data-ttu-id="607f3-242">响应缓存选项如下表中所示。</span><span class="sxs-lookup"><span data-stu-id="607f3-242">Response caching options are shown in the following table.</span></span>

| <span data-ttu-id="607f3-243">选项</span><span class="sxs-lookup"><span data-stu-id="607f3-243">Option</span></span> | <span data-ttu-id="607f3-244">描述</span><span class="sxs-lookup"><span data-stu-id="607f3-244">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | <span data-ttu-id="607f3-245">响应正文的最大可缓存大小（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="607f3-245">The largest cacheable size for the response body in bytes.</span></span> <span data-ttu-id="607f3-246">默认值为 `64 * 1024 * 1024` (64 MB) 。</span><span class="sxs-lookup"><span data-stu-id="607f3-246">The default value is `64 * 1024 * 1024` (64 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | <span data-ttu-id="607f3-247">响应缓存中间件的大小限制（以字节为单位）。</span><span class="sxs-lookup"><span data-stu-id="607f3-247">The size limit for the response cache middleware in bytes.</span></span> <span data-ttu-id="607f3-248">默认值为 `100 * 1024 * 1024` (100 MB) 。</span><span class="sxs-lookup"><span data-stu-id="607f3-248">The default value is `100 * 1024 * 1024` (100 MB).</span></span> |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | <span data-ttu-id="607f3-249">确定是否将响应缓存在区分大小写的路径上。</span><span class="sxs-lookup"><span data-stu-id="607f3-249">Determines if responses are cached on case-sensitive paths.</span></span> <span data-ttu-id="607f3-250">默认值为 `false`。</span><span class="sxs-lookup"><span data-stu-id="607f3-250">The default value is `false`.</span></span> |

<span data-ttu-id="607f3-251">下面的示例将中间件配置为：</span><span class="sxs-lookup"><span data-stu-id="607f3-251">The following example configures the middleware to:</span></span>

* <span data-ttu-id="607f3-252">大小小于或等于1024字节的缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-252">Cache responses with a body size smaller than or equal to 1,024 bytes.</span></span>
* <span data-ttu-id="607f3-253">将响应存储为区分大小写的路径。</span><span class="sxs-lookup"><span data-stu-id="607f3-253">Store the responses by case-sensitive paths.</span></span> <span data-ttu-id="607f3-254">例如， `/page1` 和 `/Page1` 分别存储。</span><span class="sxs-lookup"><span data-stu-id="607f3-254">For example, `/page1` and `/Page1` are stored separately.</span></span>

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a><span data-ttu-id="607f3-255">VaryByQueryKeys</span><span class="sxs-lookup"><span data-stu-id="607f3-255">VaryByQueryKeys</span></span>

<span data-ttu-id="607f3-256">使用 MVC/web API 控制器或 Razor 页面页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) 属性指定为响应缓存设置适当的标头所需的参数。</span><span class="sxs-lookup"><span data-stu-id="607f3-256">When using MVC / web API controllers or Razor Pages page models, the [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) attribute specifies the parameters necessary for setting the appropriate headers for response caching.</span></span> <span data-ttu-id="607f3-257">`[ResponseCache]`严格要求中间件的属性的唯一参数是 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> ，它不对应于实际 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-257">The only parameter of the `[ResponseCache]` attribute that strictly requires the middleware is <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>, which doesn't correspond to an actual HTTP header.</span></span> <span data-ttu-id="607f3-258">有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。</span><span class="sxs-lookup"><span data-stu-id="607f3-258">For more information, see <xref:performance/caching/response#responsecache-attribute>.</span></span>

<span data-ttu-id="607f3-259">如果不使用 `[ResponseCache]` 属性，响应缓存可能会随而变化 `VaryByQueryKeys` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-259">When not using the `[ResponseCache]` attribute, response caching can be varied with `VaryByQueryKeys`.</span></span> <span data-ttu-id="607f3-260"><xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：</span><span class="sxs-lookup"><span data-stu-id="607f3-260">Use the <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> directly from the [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):</span></span>

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

<span data-ttu-id="607f3-261">使用等于中的单个值 `*` 会 `VaryByQueryKeys` 根据所有请求查询参数改变缓存。</span><span class="sxs-lookup"><span data-stu-id="607f3-261">Using a single value equal to `*` in `VaryByQueryKeys` varies the cache by all request query parameters.</span></span>

## <a name="http-headers-used-by-response-caching-middleware"></a><span data-ttu-id="607f3-262">响应缓存中间件使用的 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="607f3-262">HTTP headers used by Response Caching Middleware</span></span>

<span data-ttu-id="607f3-263">下表提供了有关影响响应缓存的 HTTP 标头的信息。</span><span class="sxs-lookup"><span data-stu-id="607f3-263">The following table provides information on HTTP headers that affect response caching.</span></span>

| <span data-ttu-id="607f3-264">标头</span><span class="sxs-lookup"><span data-stu-id="607f3-264">Header</span></span> | <span data-ttu-id="607f3-265">详细信息</span><span class="sxs-lookup"><span data-stu-id="607f3-265">Details</span></span> |
| ------ | ------- |
| `Authorization` | <span data-ttu-id="607f3-266">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-266">The response isn't cached if the header exists.</span></span> |
| `Cache-Control` | <span data-ttu-id="607f3-267">中间件仅考虑用缓存指令标记的缓存响应 `public` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-267">The middleware only considers caching responses marked with the `public` cache directive.</span></span> <span data-ttu-id="607f3-268">具有以下参数的控件缓存：</span><span class="sxs-lookup"><span data-stu-id="607f3-268">Control caching with the following parameters:</span></span><ul><li><span data-ttu-id="607f3-269">最大期限</span><span class="sxs-lookup"><span data-stu-id="607f3-269">max-age</span></span></li><li><span data-ttu-id="607f3-270">最大过期&#8224;</span><span class="sxs-lookup"><span data-stu-id="607f3-270">max-stale&#8224;</span></span></li><li><span data-ttu-id="607f3-271">最小-新</span><span class="sxs-lookup"><span data-stu-id="607f3-271">min-fresh</span></span></li><li><span data-ttu-id="607f3-272">must-revalidate</span><span class="sxs-lookup"><span data-stu-id="607f3-272">must-revalidate</span></span></li><li><span data-ttu-id="607f3-273">no-cache</span><span class="sxs-lookup"><span data-stu-id="607f3-273">no-cache</span></span></li><li><span data-ttu-id="607f3-274">无-商店</span><span class="sxs-lookup"><span data-stu-id="607f3-274">no-store</span></span></li><li><span data-ttu-id="607f3-275">仅限-缓存</span><span class="sxs-lookup"><span data-stu-id="607f3-275">only-if-cached</span></span></li><li><span data-ttu-id="607f3-276">private</span><span class="sxs-lookup"><span data-stu-id="607f3-276">private</span></span></li><li><span data-ttu-id="607f3-277">public</span><span class="sxs-lookup"><span data-stu-id="607f3-277">public</span></span></li><li><span data-ttu-id="607f3-278">s-maxage</span><span class="sxs-lookup"><span data-stu-id="607f3-278">s-maxage</span></span></li><li><span data-ttu-id="607f3-279">代理重新验证&#8225;</span><span class="sxs-lookup"><span data-stu-id="607f3-279">proxy-revalidate&#8225;</span></span></li></ul><span data-ttu-id="607f3-280">&#8224;如果未指定任何限制 `max-stale` ，则中间件不会执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="607f3-280">&#8224;If no limit is specified to `max-stale`, the middleware takes no action.</span></span><br><span data-ttu-id="607f3-281">&#8225;`proxy-revalidate` 的效果与相同 `must-revalidate` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-281">&#8225;`proxy-revalidate` has the same effect as `must-revalidate`.</span></span><br><br><span data-ttu-id="607f3-282">有关详细信息，请参阅 [RFC 7231： Request Cache-Control 指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。</span><span class="sxs-lookup"><span data-stu-id="607f3-282">For more information, see [RFC 7231: Request Cache-Control Directives](https://tools.ietf.org/html/rfc7234#section-5.2.1).</span></span> |
| `Pragma` | <span data-ttu-id="607f3-283">`Pragma: no-cache`请求中的标头将产生与相同的效果 `Cache-Control: no-cache` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-283">A `Pragma: no-cache` header in the request produces the same effect as `Cache-Control: no-cache`.</span></span> <span data-ttu-id="607f3-284">标头中的相关指令 `Cache-Control` （如果存在）将重写此标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-284">This header is overridden by the relevant directives in the `Cache-Control` header, if present.</span></span> <span data-ttu-id="607f3-285">考虑向后兼容 HTTP/1.0。</span><span class="sxs-lookup"><span data-stu-id="607f3-285">Considered for backward compatibility with HTTP/1.0.</span></span> |
| `Set-Cookie` | <span data-ttu-id="607f3-286">如果标头存在，则不会缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-286">The response isn't cached if the header exists.</span></span> <span data-ttu-id="607f3-287">请求处理管道中设置一个或多个的任何中间件 cookie 会阻止响应缓存中间件缓存响应 (例如， [ cookie 基于的 TempData 提供程序](xref:fundamentals/app-state#tempdata)) 。</span><span class="sxs-lookup"><span data-stu-id="607f3-287">Any middleware in the request processing pipeline that sets one or more cookies prevents the Response Caching Middleware from caching the response (for example, the [cookie-based TempData provider](xref:fundamentals/app-state#tempdata)).</span></span>  |
| `Vary` | <span data-ttu-id="607f3-288">`Vary`标头用于根据另一个标头改变缓存的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-288">The `Vary` header is used to vary the cached response by another header.</span></span> <span data-ttu-id="607f3-289">例如，通过包含标头来缓存响应， `Vary: Accept-Encoding` 此标头将使用标头和单独的请求来缓存响应 `Accept-Encoding: gzip` `Accept-Encoding: text/plain` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-289">For example, cache responses by encoding by including the `Vary: Accept-Encoding` header, which caches responses for requests with headers `Accept-Encoding: gzip` and `Accept-Encoding: text/plain` separately.</span></span> <span data-ttu-id="607f3-290">永远不会存储标头值 `*` 为的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-290">A response with a header value of `*` is never stored.</span></span> |
| `Expires` | <span data-ttu-id="607f3-291">除非由其他标头重写，否则不会存储或检索此标头过时的响应 `Cache-Control` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-291">A response deemed stale by this header isn't stored or retrieved unless overridden by other `Cache-Control` headers.</span></span> |
| `If-None-Match` | <span data-ttu-id="607f3-292">如果值不为 `*` ，并且响应的与提供的任何值都不匹配，则将从缓存中提供完整响应 `ETag` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-292">The full response is served from cache if the value isn't `*` and the `ETag` of the response doesn't match any of the values provided.</span></span> <span data-ttu-id="607f3-293">否则，将为 304 (修改) 响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-293">Otherwise, a 304 (Not Modified) response is served.</span></span> |
| `If-Modified-Since` | <span data-ttu-id="607f3-294">如果 `If-None-Match` 标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-294">If the `If-None-Match` header isn't present, a full response is served from cache if the cached response date is newer than the value provided.</span></span> <span data-ttu-id="607f3-295">否则，将提供 *304-未修改* 响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-295">Otherwise, a *304 - Not Modified* response is served.</span></span> |
| `Date` | <span data-ttu-id="607f3-296">从缓存提供时， `Date` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-296">When serving from cache, the `Date` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Content-Length` | <span data-ttu-id="607f3-297">从缓存提供时， `Content-Length` 如果未在原始响应中提供标头，中间件将设置标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-297">When serving from cache, the `Content-Length` header is set by the middleware if it wasn't provided on the original response.</span></span> |
| `Age` | <span data-ttu-id="607f3-298">`Age`忽略原始响应中发送的标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-298">The `Age` header sent in the original response is ignored.</span></span> <span data-ttu-id="607f3-299">中间件在为缓存的响应提供服务时计算一个新值。</span><span class="sxs-lookup"><span data-stu-id="607f3-299">The middleware computes a new value when serving a cached response.</span></span> |

## <a name="caching-respects-request-cache-control-directives"></a><span data-ttu-id="607f3-300">缓存遵从请求 Cache-Control 指令</span><span class="sxs-lookup"><span data-stu-id="607f3-300">Caching respects request Cache-Control directives</span></span>

<span data-ttu-id="607f3-301">中间件遵循 [HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。</span><span class="sxs-lookup"><span data-stu-id="607f3-301">The middleware respects the rules of the [HTTP 1.1 Caching specification](https://tools.ietf.org/html/rfc7234#section-5.2).</span></span> <span data-ttu-id="607f3-302">规则要求使用缓存来服从 `Cache-Control` 客户端发送的有效标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-302">The rules require a cache to honor a valid `Cache-Control` header sent by the client.</span></span> <span data-ttu-id="607f3-303">在规范下，客户端可以使用 `no-cache` 标头值发出请求，并强制服务器为每个请求生成新的响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-303">Under the specification, a client can make requests with a `no-cache` header value and force the server to generate a new response for every request.</span></span> <span data-ttu-id="607f3-304">目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。</span><span class="sxs-lookup"><span data-stu-id="607f3-304">Currently, there's no developer control over this caching behavior when using the middleware because the middleware adheres to the official caching specification.</span></span>

<span data-ttu-id="607f3-305">为了更好地控制缓存行为，浏览 ASP.NET Core 的其他缓存功能。</span><span class="sxs-lookup"><span data-stu-id="607f3-305">For more control over caching behavior, explore other caching features of ASP.NET Core.</span></span> <span data-ttu-id="607f3-306">请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="607f3-306">See the following topics:</span></span>

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a><span data-ttu-id="607f3-307">疑难解答</span><span class="sxs-lookup"><span data-stu-id="607f3-307">Troubleshooting</span></span>

<span data-ttu-id="607f3-308">如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。</span><span class="sxs-lookup"><span data-stu-id="607f3-308">If caching behavior isn't as expected, confirm that responses are cacheable and capable of being served from the cache.</span></span> <span data-ttu-id="607f3-309">检查请求的传入标头和响应的传出标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-309">Examine the request's incoming headers and the response's outgoing headers.</span></span> <span data-ttu-id="607f3-310">启用 [日志记录](xref:fundamentals/logging/index) 以帮助进行调试。</span><span class="sxs-lookup"><span data-stu-id="607f3-310">Enable [logging](xref:fundamentals/logging/index) to help with debugging.</span></span>

<span data-ttu-id="607f3-311">在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-311">When testing and troubleshooting caching behavior, a browser may set request headers that affect caching in undesirable ways.</span></span> <span data-ttu-id="607f3-312">例如，浏览器可以在 `Cache-Control` `no-cache` 刷新页面时将标题设置为或 `max-age=0` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-312">For example, a browser may set the `Cache-Control` header to `no-cache` or `max-age=0` when refreshing a page.</span></span> <span data-ttu-id="607f3-313">以下工具可以显式设置请求标头，并优先于测试缓存：</span><span class="sxs-lookup"><span data-stu-id="607f3-313">The following tools can explicitly set request headers and are preferred for testing caching:</span></span>

* [<span data-ttu-id="607f3-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="607f3-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
* [<span data-ttu-id="607f3-315">Postman</span><span class="sxs-lookup"><span data-stu-id="607f3-315">Postman</span></span>](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a><span data-ttu-id="607f3-316">缓存条件</span><span class="sxs-lookup"><span data-stu-id="607f3-316">Conditions for caching</span></span>

* <span data-ttu-id="607f3-317">请求必须使用 200 (良好) 状态代码来生成服务器响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-317">The request must result in a server response with a 200 (OK) status code.</span></span>
* <span data-ttu-id="607f3-318">请求方法必须为 GET 或 HEAD。</span><span class="sxs-lookup"><span data-stu-id="607f3-318">The request method must be GET or HEAD.</span></span>
* <span data-ttu-id="607f3-319">在中 `Startup.Configure` ，必须将响应缓存中间件置于需要缓存的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="607f3-319">In `Startup.Configure`, Response Caching Middleware must be placed before middleware that require caching.</span></span> <span data-ttu-id="607f3-320">有关详细信息，请参阅 <xref:fundamentals/middleware/index>。</span><span class="sxs-lookup"><span data-stu-id="607f3-320">For more information, see <xref:fundamentals/middleware/index>.</span></span>
* <span data-ttu-id="607f3-321">`Authorization`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="607f3-321">The `Authorization` header must not be present.</span></span>
* <span data-ttu-id="607f3-322">`Cache-Control` 标头参数必须是有效的，并且响应必须标记 `public` 且未标记 `private` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-322">`Cache-Control` header parameters must be valid, and the response must be marked `public` and not marked `private`.</span></span>
* <span data-ttu-id="607f3-323">`Pragma: no-cache`如果标头不存在，则标头不得出现 `Cache-Control` ，因为标头会 `Cache-Control` `Pragma` 在存在时覆盖标头。</span><span class="sxs-lookup"><span data-stu-id="607f3-323">The `Pragma: no-cache` header must not be present if the `Cache-Control` header isn't present, as the `Cache-Control` header overrides the `Pragma` header when present.</span></span>
* <span data-ttu-id="607f3-324">`Set-Cookie`标题不得存在。</span><span class="sxs-lookup"><span data-stu-id="607f3-324">The `Set-Cookie` header must not be present.</span></span>
* <span data-ttu-id="607f3-325">`Vary` 标头参数必须有效并且不等于 `*` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-325">`Vary` header parameters must be valid and not equal to `*`.</span></span>
* <span data-ttu-id="607f3-326">`Content-Length`如果设置) 必须与响应正文的大小匹配，则标头值 (。</span><span class="sxs-lookup"><span data-stu-id="607f3-326">The `Content-Length` header value (if set) must match the size of the response body.</span></span>
* <span data-ttu-id="607f3-327"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>不使用。</span><span class="sxs-lookup"><span data-stu-id="607f3-327">The <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature> isn't used.</span></span>
* <span data-ttu-id="607f3-328">`Expires`标头和 `max-age` 和缓存指令指定的响应不能过时 `s-maxage` 。</span><span class="sxs-lookup"><span data-stu-id="607f3-328">The response must not be stale as specified by the `Expires` header and the `max-age` and `s-maxage` cache directives.</span></span>
* <span data-ttu-id="607f3-329">响应缓冲必须成功。</span><span class="sxs-lookup"><span data-stu-id="607f3-329">Response buffering must be successful.</span></span> <span data-ttu-id="607f3-330">响应的大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> 。</span><span class="sxs-lookup"><span data-stu-id="607f3-330">The size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>.</span></span> <span data-ttu-id="607f3-331">响应的正文大小必须小于配置的或默认值 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> 。</span><span class="sxs-lookup"><span data-stu-id="607f3-331">The body size of the response must be smaller than the configured or default <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>.</span></span>
* <span data-ttu-id="607f3-332">必须根据 [RFC 7234](https://tools.ietf.org/html/rfc7234) 规范来缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-332">The response must be cacheable according to the [RFC 7234](https://tools.ietf.org/html/rfc7234) specifications.</span></span> <span data-ttu-id="607f3-333">例如， `no-store` 指令在请求或响应标头字段中不得存在。</span><span class="sxs-lookup"><span data-stu-id="607f3-333">For example, the `no-store` directive must not exist in request or response header fields.</span></span> <span data-ttu-id="607f3-334">有关详细信息，请参阅 *第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234) 的缓存中。</span><span class="sxs-lookup"><span data-stu-id="607f3-334">See *Section 3: Storing Responses in Caches* of [RFC 7234](https://tools.ietf.org/html/rfc7234) for details.</span></span>

> [!NOTE]
> <span data-ttu-id="607f3-335">用于生成安全令牌的防伪系统，以防止跨站点请求伪造 (CSRF) 攻击将 `Cache-Control` 和 `Pragma` 标头设置为， `no-cache` 以便不缓存响应。</span><span class="sxs-lookup"><span data-stu-id="607f3-335">The Antiforgery system for generating secure tokens to prevent Cross-Site Request Forgery (CSRF) attacks sets the `Cache-Control` and `Pragma` headers to `no-cache` so that responses aren't cached.</span></span> <span data-ttu-id="607f3-336">有关如何为 HTML 窗体元素禁用防伪标记的信息，请参阅 <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> 。</span><span class="sxs-lookup"><span data-stu-id="607f3-336">For information on how to disable antiforgery tokens for HTML form elements, see <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="607f3-337">其他资源</span><span class="sxs-lookup"><span data-stu-id="607f3-337">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end