---
title: ASP.NET Core 中的缓存内存
author: rick-anderson
description: 了解如何在 ASP.NET Core 内存中缓存数据。
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
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
uid: performance/caching/memory
ms.openlocfilehash: 71aeb246a22e236ea134e0427d07837b09988bf9
ms.sourcegitcommit: b81327f1a62e9857d9e51fb34775f752261a88ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105051005"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="7fb54-103">ASP.NET Core 中的缓存内存</span><span class="sxs-lookup"><span data-stu-id="7fb54-103">Cache in-memory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7fb54-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7fb54-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7fb54-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/memory/3.0sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7fb54-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/memory/3.0sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="7fb54-106">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="7fb54-106">Caching basics</span></span>

<span data-ttu-id="7fb54-107">缓存可以减少生成内容所需的工作，从而显著提高应用程序的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="7fb54-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="7fb54-108">缓存最适用于不经常更改的 **数据，生成** 成本很高。</span><span class="sxs-lookup"><span data-stu-id="7fb54-108">Caching works best with data that changes infrequently **and** is expensive to generate.</span></span> <span data-ttu-id="7fb54-109">通过缓存，可以比从数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="7fb54-109">Caching makes a copy of data that can be returned much faster than from the source.</span></span> <span data-ttu-id="7fb54-110">应该对应用进行编写和测试，使其 **永不** 依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="7fb54-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="7fb54-111">ASP.NET Core 支持多个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="7fb54-112">最简单的缓存基于 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)。</span><span class="sxs-lookup"><span data-stu-id="7fb54-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="7fb54-113">`IMemoryCache` 表示存储在 web 服务器的内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-113">`IMemoryCache` represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="7fb54-114">在服务器场上运行的应用 (多台服务器) 应确保会话在使用内存中缓存时处于粘滞状态。</span><span class="sxs-lookup"><span data-stu-id="7fb54-114">Apps running on a server farm (multiple servers) should ensure sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="7fb54-115">粘滞会话确保来自客户端的后续请求都将发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="7fb54-115">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="7fb54-116">例如，Azure Web 应用使用 [应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) 将所有后续请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="7fb54-116">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="7fb54-117">Web 场中的非粘滞会话需要 [分布式缓存](distributed.md) ，以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="7fb54-117">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="7fb54-118">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="7fb54-118">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="7fb54-119">使用分布式缓存会将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="7fb54-119">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="7fb54-120">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="7fb54-120">The in-memory cache can store any object.</span></span> <span data-ttu-id="7fb54-121">分布式缓存接口仅限 `byte[]` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-121">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="7fb54-122">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="7fb54-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="7fb54-123">MemoryCache/缓存</span><span class="sxs-lookup"><span data-stu-id="7fb54-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="7fb54-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)) 可用于：</span><span class="sxs-lookup"><span data-stu-id="7fb54-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="7fb54-125">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="7fb54-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="7fb54-126">面向 .NET Standard 2.0 或更高版本的任何 [.net 实现](/dotnet/standard/net-standard#net-implementation-support) 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="7fb54-127">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="7fb54-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="7fb54-128">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="7fb54-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="7fb54-129">[](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` 建议使用 (本文中所述) ， `System.Runtime.Caching` / `MemoryCache` 因为它更好地集成到 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="7fb54-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="7fb54-130">例如， `IMemoryCache` 使用 ASP.NET Core [依赖关系注入](xref:fundamentals/dependency-injection)本身工作。</span><span class="sxs-lookup"><span data-stu-id="7fb54-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7fb54-131">将 `System.Runtime.Caching` / `MemoryCache` ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，请使用作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="7fb54-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="7fb54-132">缓存指南</span><span class="sxs-lookup"><span data-stu-id="7fb54-132">Cache guidelines</span></span>

* <span data-ttu-id="7fb54-133">代码应始终具有回退选项，以获取数据，而 **不** 是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="7fb54-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="7fb54-134">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="7fb54-135">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="7fb54-135">Limit cache growth:</span></span>
  * <span data-ttu-id="7fb54-136">不要 **使用外部** 输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="7fb54-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="7fb54-137">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="7fb54-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="7fb54-138">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="7fb54-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="7fb54-139">ASP.NET Core 运行时不会根据内存 **压力限制缓存** 大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-139">The ASP.NET Core runtime does **not** limit cache size based on memory pressure.</span></span> <span data-ttu-id="7fb54-140">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-140">It's up to the developer to limit cache size.</span></span>

## <a name="use-imemorycache"></a><span data-ttu-id="7fb54-141">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="7fb54-141">Use IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="7fb54-142">使用 [依赖关系注入](xref:fundamentals/dependency-injection)中的 *共享* 内存缓存，并调用 `SetSize` 、 `Size` 或 `SizeLimit` 以限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="7fb54-142">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="7fb54-143">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-143">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="7fb54-144">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="7fb54-144">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="7fb54-145">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-145">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="7fb54-146">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发 `InvalidOperationException` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-146">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="7fb54-147">当使用 `SetSize` 、 `Size` 或 `SizeLimit` 来限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="7fb54-147">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="7fb54-148">有关详细信息和示例，请参阅 [使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="7fb54-148">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>
> <span data-ttu-id="7fb54-149">共享缓存由其他框架或库共享。</span><span class="sxs-lookup"><span data-stu-id="7fb54-149">A shared cache is one shared by other frameworks or libraries.</span></span> <span data-ttu-id="7fb54-150">例如，EF Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-150">For example, EF Core uses the shared cache and does not specify a size.</span></span> 

<span data-ttu-id="7fb54-151">内存中缓存是从应用程序中使用 [依赖关系注入](xref:fundamentals/dependency-injection)引用的一种 *服务*。</span><span class="sxs-lookup"><span data-stu-id="7fb54-151">In-memory caching is a *service* that's referenced from an app using [Dependency Injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7fb54-152">`IMemoryCache`在构造函数中请求实例：</span><span class="sxs-lookup"><span data-stu-id="7fb54-152">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="7fb54-153">以下代码使用 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) 来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="7fb54-153">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="7fb54-154">如果未缓存时间，则将创建一个新条目，并 [将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="7fb54-154">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span> <span data-ttu-id="7fb54-155">`CacheKeys`类是下载示例的一部分。</span><span class="sxs-lookup"><span data-stu-id="7fb54-155">The `CacheKeys` class is part of the download sample.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="7fb54-156">显示当前时间和缓存时间：</span><span class="sxs-lookup"><span data-stu-id="7fb54-156">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

<span data-ttu-id="7fb54-157">下面的代码使用 [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_System_TimeSpan_) extension 方法来缓存相对时间的数据而不创建 `MemoryCacheEntryOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="7fb54-157">The following code uses the [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_System_TimeSpan_) extension method to cache data for a relative time without creating the `MemoryCacheEntryOptions` object.</span></span>
[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_set)]

<span data-ttu-id="7fb54-158">如果在 `DateTime` 超时期限内存在请求，则缓存值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="7fb54-158">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span>

<span data-ttu-id="7fb54-159">以下代码使用 [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 和 [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="7fb54-159">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="7fb54-160">以下代码调用 [Get 获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 缓存时间：</span><span class="sxs-lookup"><span data-stu-id="7fb54-160">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="7fb54-161">下面的代码获取或创建具有绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="7fb54-161">The following code gets or creates a cached item with absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

<span data-ttu-id="7fb54-162">只有具有可调过期的缓存项集存在过时的风险。</span><span class="sxs-lookup"><span data-stu-id="7fb54-162">A cached item set with a sliding expiration only is at risk of becoming stale.</span></span> <span data-ttu-id="7fb54-163">如果访问的时间比滑动过期时间间隔更频繁，则该项将永不过期。</span><span class="sxs-lookup"><span data-stu-id="7fb54-163">If it's accessed more frequently than the sliding expiration interval, the item will never expire.</span></span> <span data-ttu-id="7fb54-164">将弹性过期与绝对过期组合在一起，以保证项目在其绝对过期时间通过后过期。</span><span class="sxs-lookup"><span data-stu-id="7fb54-164">Combine a sliding expiration with an absolute expiration to guarantee that the item expires once its absolute expiration time passes.</span></span> <span data-ttu-id="7fb54-165">绝对过期会将项的上限设置为可缓存项的时间，同时仍允许项在可调整过期时间间隔内未请求时提前过期。</span><span class="sxs-lookup"><span data-stu-id="7fb54-165">The absolute expiration sets an upper bound to how long the item can be cached while still allowing the item to expire earlier if it isn't requested within the sliding expiration interval.</span></span> <span data-ttu-id="7fb54-166">如果同时指定了绝对过期和可调过期时间，则过期时间以逻辑方式运算。</span><span class="sxs-lookup"><span data-stu-id="7fb54-166">When both absolute and sliding expiration are specified, the expirations are logically ORed.</span></span> <span data-ttu-id="7fb54-167">如果滑动过期时间间隔 *或* 绝对过期时间通过，则从缓存中逐出该项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-167">If either the sliding expiration interval *or* the absolute expiration time pass, the item is evicted from the cache.</span></span>

<span data-ttu-id="7fb54-168">下面的代码获取或创建具有可调 *和* 绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="7fb54-168">The following code gets or creates a cached item with both sliding *and* absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

<span data-ttu-id="7fb54-169">前面的代码保证数据的缓存时间不超过绝对时间。</span><span class="sxs-lookup"><span data-stu-id="7fb54-169">The preceding code guarantees the data will not be cached longer than the absolute time.</span></span>

<span data-ttu-id="7fb54-170"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> 是类中的扩展方法 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-170"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> are extension methods in the <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> class.</span></span> <span data-ttu-id="7fb54-171">这些方法扩展了的功能 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-171">These methods extend the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span>

## `MemoryCacheEntryOptions`

<span data-ttu-id="7fb54-172">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="7fb54-172">The following sample:</span></span>

* <span data-ttu-id="7fb54-173">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="7fb54-173">Sets a sliding expiration time.</span></span> <span data-ttu-id="7fb54-174">访问此缓存项的请求将重置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="7fb54-174">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="7fb54-175">将缓存优先级设置为 [CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove)。</span><span class="sxs-lookup"><span data-stu-id="7fb54-175">Sets the cache priority to [CacheItemPriority.NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).</span></span>
* <span data-ttu-id="7fb54-176">设置在从缓存中逐出项后将调用的 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-176">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="7fb54-177">回调在从缓存中删除项的代码的不同线程上运行。</span><span class="sxs-lookup"><span data-stu-id="7fb54-177">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="7fb54-178">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="7fb54-178">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="7fb54-179">`MemoryCache`实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="7fb54-179">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="7fb54-180">缓存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="7fb54-180">The cache size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="7fb54-181">如果设置了缓存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="7fb54-181">If the cache size limit is set, all entries must specify size.</span></span> <span data-ttu-id="7fb54-182">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-182">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="7fb54-183">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-183">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="7fb54-184">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="7fb54-184">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="7fb54-185">例如：</span><span class="sxs-lookup"><span data-stu-id="7fb54-185">For example:</span></span>

* <span data-ttu-id="7fb54-186">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="7fb54-186">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="7fb54-187">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="7fb54-187">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="7fb54-188">如果 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> 未设置，则缓存的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="7fb54-188">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> isn't set, the cache grows without bound.</span></span> <span data-ttu-id="7fb54-189">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-189">The ASP.NET Core runtime doesn't trim the cache when system memory is low.</span></span> <span data-ttu-id="7fb54-190">应用必须构建为：</span><span class="sxs-lookup"><span data-stu-id="7fb54-190">Apps must be architected to:</span></span>

* <span data-ttu-id="7fb54-191">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="7fb54-191">Limit cache growth.</span></span>
* <span data-ttu-id="7fb54-192"><xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> 当可用内存有限时调用或：</span><span class="sxs-lookup"><span data-stu-id="7fb54-192">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="7fb54-193">以下代码创建了 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> [依赖关系注入](xref:fundamentals/dependency-injection)可访问的无大小固定大小：</span><span class="sxs-lookup"><span data-stu-id="7fb54-193">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="7fb54-194">`SizeLimit` 没有单位。</span><span class="sxs-lookup"><span data-stu-id="7fb54-194">`SizeLimit` does not have units.</span></span> <span data-ttu-id="7fb54-195">如果已设置缓存大小限制，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-195">Cached entries must specify size in whatever units they deem most appropriate if the cache size limit has been set.</span></span> <span data-ttu-id="7fb54-196">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="7fb54-196">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="7fb54-197">如果缓存条目大小之和超过指定的值，则不会缓存条目 `SizeLimit` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-197">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="7fb54-198">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-198">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="7fb54-199">以下代码将注册 `MyMemoryCache` 到 [依赖关系注入](xref:fundamentals/dependency-injection) 容器。</span><span class="sxs-lookup"><span data-stu-id="7fb54-199">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

<span data-ttu-id="7fb54-200">`MyMemoryCache` 对于识别此大小限制缓存并知道如何适当地设置缓存条目大小的组件，将创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-200">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="7fb54-201">以下代码使用 `MyMemoryCache` ：</span><span class="sxs-lookup"><span data-stu-id="7fb54-201">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

<span data-ttu-id="7fb54-202">缓存项的大小可以通过 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> 扩展方法来设置：</span><span class="sxs-lookup"><span data-stu-id="7fb54-202">The size of the cache entry can be set by <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> or the <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> extension methods:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="7fb54-203">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="7fb54-203">MemoryCache.Compact</span></span>

<span data-ttu-id="7fb54-204">`MemoryCache.Compact` 尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="7fb54-204">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="7fb54-205">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-205">All expired items.</span></span>
* <span data-ttu-id="7fb54-206">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="7fb54-206">Items by priority.</span></span> <span data-ttu-id="7fb54-207">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-207">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="7fb54-208">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="7fb54-208">Least recently used objects.</span></span>
* <span data-ttu-id="7fb54-209">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-209">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="7fb54-210">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-210">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="7fb54-211">永远不会删除具有优先级的固定项 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-211">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span> <span data-ttu-id="7fb54-212">以下代码将删除缓存项并调用 `Compact` ：</span><span class="sxs-lookup"><span data-stu-id="7fb54-212">The following code removes a cache item and calls `Compact`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="7fb54-213">有关详细信息，请参阅 [GitHub 上的 Compact 源](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-213">See [Compact source on GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="7fb54-214">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="7fb54-214">Cache dependencies</span></span>

<span data-ttu-id="7fb54-215">下面的示例演示如何在依赖条目过期时使缓存条目过期。</span><span class="sxs-lookup"><span data-stu-id="7fb54-215">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="7fb54-216"><xref:Microsoft.Extensions.Primitives.CancellationChangeToken>添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-216">A <xref:Microsoft.Extensions.Primitives.CancellationChangeToken> is added to the cached item.</span></span> <span data-ttu-id="7fb54-217">当 `Cancel` 在上调用时 `CancellationTokenSource` ，将逐出两个缓存项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-217">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="7fb54-218">使用 <xref:System.Threading.CancellationTokenSource> 允许将多个缓存条目作为一个组逐出。</span><span class="sxs-lookup"><span data-stu-id="7fb54-218">Using a <xref:System.Threading.CancellationTokenSource> allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="7fb54-219">`using`在上面的代码中，在块中创建的缓存条目 `using` 将继承触发器和过期设置。</span><span class="sxs-lookup"><span data-stu-id="7fb54-219">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="7fb54-220">附加说明</span><span class="sxs-lookup"><span data-stu-id="7fb54-220">Additional notes</span></span>

* <span data-ttu-id="7fb54-221">不会在后台进行过期。</span><span class="sxs-lookup"><span data-stu-id="7fb54-221">Expiration doesn't happen in the background.</span></span> <span data-ttu-id="7fb54-222">没有计时器可主动扫描过期项目的缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-222">There is no timer that actively scans the cache for expired items.</span></span> <span data-ttu-id="7fb54-223">缓存上的任何活动 (`Get` ， `Set` `Remove`) 可以触发过期项的后台扫描。</span><span class="sxs-lookup"><span data-stu-id="7fb54-223">Any activity on the cache (`Get`, `Set`, `Remove`) can trigger a background scan for expired items.</span></span> <span data-ttu-id="7fb54-224"> (上的计时器 `CancellationTokenSource` <xref:System.Threading.CancellationTokenSource.CancelAfter*>) 还会删除项，并触发扫描过期项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-224">A timer on the `CancellationTokenSource` (<xref:System.Threading.CancellationTokenSource.CancelAfter*>) also removes the entry and triggers a scan for expired items.</span></span> <span data-ttu-id="7fb54-225">下面的示例将 [CancellationTokenSource (TimeSpan) ](/dotnet/api/system.threading.cancellationtokensource.-ctor) 用于注册的令牌。</span><span class="sxs-lookup"><span data-stu-id="7fb54-225">The following example uses [CancellationTokenSource(TimeSpan)](/dotnet/api/system.threading.cancellationtokensource.-ctor) for the registered token.</span></span> <span data-ttu-id="7fb54-226">此令牌激发后，会立即删除该条目，并激发逐出回调：</span><span class="sxs-lookup"><span data-stu-id="7fb54-226">When this token fires it removes the entry immediately and fires the eviction callbacks:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* <span data-ttu-id="7fb54-227">使用回调来重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="7fb54-227">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="7fb54-228">多个请求可以查找缓存的密钥值为空，因为回调未完成。</span><span class="sxs-lookup"><span data-stu-id="7fb54-228">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="7fb54-229">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-229">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="7fb54-230">当使用一个缓存条目创建另一个缓存条目时，子对象会复制父条目的过期令牌和基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="7fb54-230">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="7fb54-231">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="7fb54-231">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="7fb54-232">用于 <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> 设置在从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="7fb54-232">Use <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>
* <span data-ttu-id="7fb54-233">对于大多数应用， `IMemoryCache` 启用。</span><span class="sxs-lookup"><span data-stu-id="7fb54-233">For most apps, `IMemoryCache` is enabled.</span></span> <span data-ttu-id="7fb54-234">例如， `AddMvc` 在中调用、、、 `AddControllersWithViews` `AddRazorPages` `AddMvcCore().AddRazorViewEngine` 和许多其他 `Add{Service}` 方法将 `ConfigureServices` 启用 `IMemoryCache` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-234">For example, calling `AddMvc`, `AddControllersWithViews`, `AddRazorPages`, `AddMvcCore().AddRazorViewEngine`, and many other `Add{Service}` methods in `ConfigureServices`, enables `IMemoryCache`.</span></span> <span data-ttu-id="7fb54-235">对于未调用上述方法之一的应用程序 `Add{Service}` ，可能需要 <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> 在中调用 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-235">For apps that are not calling one of the preceding `Add{Service}` methods, it may be necessary to call <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> in `ConfigureServices`.</span></span>

## <a name="background-cache-update"></a><span data-ttu-id="7fb54-236">后台缓存更新</span><span class="sxs-lookup"><span data-stu-id="7fb54-236">Background cache update</span></span>

<span data-ttu-id="7fb54-237">使用 [后台服务](xref:fundamentals/host/hosted-services) （如） <xref:Microsoft.Extensions.Hosting.IHostedService> 来更新缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-237">Use a [background service](xref:fundamentals/host/hosted-services) such as <xref:Microsoft.Extensions.Hosting.IHostedService> to update the cache.</span></span> <span data-ttu-id="7fb54-238">后台服务可重新计算条目，然后仅在准备就绪时将其分配给缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-238">The background service can recompute the entries and then assign them to the cache only when they’re ready.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7fb54-239">其他资源</span><span class="sxs-lookup"><span data-stu-id="7fb54-239">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
<span data-ttu-id="7fb54-240">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7fb54-240">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7fb54-241">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/memory/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7fb54-241">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="7fb54-242">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="7fb54-242">Caching basics</span></span>

<span data-ttu-id="7fb54-243">缓存可以减少生成内容所需的工作，从而显著提高应用程序的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="7fb54-243">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="7fb54-244">缓存最适用于不经常更改的数据。</span><span class="sxs-lookup"><span data-stu-id="7fb54-244">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="7fb54-245">通过缓存，可以比从原始数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="7fb54-245">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="7fb54-246">应编写并测试代码，使其 **绝不会** 依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="7fb54-246">Code should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="7fb54-247">ASP.NET Core 支持多个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-247">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="7fb54-248">最简单的缓存基于 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)，这表示存储在 web 服务器内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-248">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="7fb54-249">在服务器场上运行的应用 (多台服务器) 应确保会话在使用内存中缓存时处于粘滞。</span><span class="sxs-lookup"><span data-stu-id="7fb54-249">Apps that run on a server farm (multiple servers) should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="7fb54-250">粘滞会话确保以后来自客户端的请求都发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="7fb54-250">Sticky sessions ensure that later requests from a client all go to the same server.</span></span> <span data-ttu-id="7fb54-251">例如，Azure Web 应用使用 [应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) 将来自用户代理的所有请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="7fb54-251">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all requests from a user agent to the same server.</span></span>

<span data-ttu-id="7fb54-252">Web 场中的非粘滞会话需要 [分布式缓存](distributed.md) ，以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="7fb54-252">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="7fb54-253">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="7fb54-253">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="7fb54-254">使用分布式缓存会将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="7fb54-254">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="7fb54-255">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="7fb54-255">The in-memory cache can store any object.</span></span> <span data-ttu-id="7fb54-256">分布式缓存接口仅限 `byte[]` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-256">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="7fb54-257">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="7fb54-257">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="7fb54-258">MemoryCache/缓存</span><span class="sxs-lookup"><span data-stu-id="7fb54-258">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="7fb54-259"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)) 可用于：</span><span class="sxs-lookup"><span data-stu-id="7fb54-259"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="7fb54-260">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="7fb54-260">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="7fb54-261">面向 .NET Standard 2.0 或更高版本的任何 [.net 实现](/dotnet/standard/net-standard#net-implementation-support) 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-261">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="7fb54-262">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="7fb54-262">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="7fb54-263">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="7fb54-263">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="7fb54-264">[](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` 建议使用 (本文中所述) ， `System.Runtime.Caching` / `MemoryCache` 因为它更好地集成到 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="7fb54-264">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="7fb54-265">例如， `IMemoryCache` 使用 ASP.NET Core [依赖关系注入](xref:fundamentals/dependency-injection)本身工作。</span><span class="sxs-lookup"><span data-stu-id="7fb54-265">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="7fb54-266">将 `System.Runtime.Caching` / `MemoryCache` ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，请使用作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="7fb54-266">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="7fb54-267">缓存指南</span><span class="sxs-lookup"><span data-stu-id="7fb54-267">Cache guidelines</span></span>

* <span data-ttu-id="7fb54-268">代码应始终具有回退选项，以获取数据，而 **不** 是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="7fb54-268">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="7fb54-269">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-269">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="7fb54-270">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="7fb54-270">Limit cache growth:</span></span>
  * <span data-ttu-id="7fb54-271">不要 **使用外部** 输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="7fb54-271">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="7fb54-272">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="7fb54-272">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="7fb54-273">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="7fb54-273">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="7fb54-274">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-274">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="7fb54-275">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-275">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="7fb54-276">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="7fb54-276">Using IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="7fb54-277">使用 [依赖关系注入](xref:fundamentals/dependency-injection)中的 *共享* 内存缓存，并调用 `SetSize` 、 `Size` 或 `SizeLimit` 以限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="7fb54-277">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="7fb54-278">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-278">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="7fb54-279">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="7fb54-279">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="7fb54-280">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-280">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="7fb54-281">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发 `InvalidOperationException` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-281">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="7fb54-282">当使用 `SetSize` 、 `Size` 或 `SizeLimit` 来限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="7fb54-282">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="7fb54-283">有关详细信息和示例，请参阅 [使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="7fb54-283">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="7fb54-284">内存中缓存是从应用程序中使用 [依赖关系注入](../../fundamentals/dependency-injection.md)引用的一种 *服务*。</span><span class="sxs-lookup"><span data-stu-id="7fb54-284">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="7fb54-285">调用 `AddMemoryCache` 于 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="7fb54-285">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="7fb54-286">`IMemoryCache`在构造函数中请求实例：</span><span class="sxs-lookup"><span data-stu-id="7fb54-286">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="7fb54-287">`IMemoryCache`需要 NuGet 包 Microsoft.AspNetCore.App，可在[元包](xref:fundamentals/metapackage-app)中[找到。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)</span><span class="sxs-lookup"><span data-stu-id="7fb54-287">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="7fb54-288">以下代码使用 [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) 来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="7fb54-288">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="7fb54-289">如果未缓存时间，则将创建一个新条目，并 [将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="7fb54-289">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="7fb54-290">显示当前时间和缓存时间：</span><span class="sxs-lookup"><span data-stu-id="7fb54-290">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="7fb54-291">如果在 `DateTime` 超时期限内存在请求，则缓存值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="7fb54-291">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="7fb54-292">下图显示了从缓存中检索到的当前时间和旧时间：</span><span class="sxs-lookup"><span data-stu-id="7fb54-292">The following image shows the current time and an older time retrieved from the cache:</span></span>

![显示两个不同时间的索引视图](memory/_static/time.png)

<span data-ttu-id="7fb54-294">以下代码使用 [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) 和 [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) 来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="7fb54-294">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="7fb54-295">以下代码调用 [Get 获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 缓存时间：</span><span class="sxs-lookup"><span data-stu-id="7fb54-295">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="7fb54-296"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> 、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和 [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) 是 [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 类的扩展方法的一部分，它扩展了的功能 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-296"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="7fb54-297">有关其他缓存方法的说明，请参阅 [IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) 和 [CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-297">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="7fb54-298">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="7fb54-298">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="7fb54-299">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="7fb54-299">The following sample:</span></span>

* <span data-ttu-id="7fb54-300">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="7fb54-300">Sets a sliding expiration time.</span></span> <span data-ttu-id="7fb54-301">访问此缓存项的请求将重置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="7fb54-301">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="7fb54-302">将缓存优先级设置为 `CacheItemPriority.NeverRemove` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-302">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="7fb54-303">设置在从缓存中逐出项后将调用的 [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-303">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="7fb54-304">回调在从缓存中删除项的代码的不同线程上运行。</span><span class="sxs-lookup"><span data-stu-id="7fb54-304">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="7fb54-305">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="7fb54-305">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="7fb54-306">`MemoryCache`实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="7fb54-306">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="7fb54-307">缓存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="7fb54-307">The cache size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="7fb54-308">如果设置了缓存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="7fb54-308">If the cache size limit is set, all entries must specify size.</span></span> <span data-ttu-id="7fb54-309">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-309">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="7fb54-310">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-310">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="7fb54-311">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="7fb54-311">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="7fb54-312">例如：</span><span class="sxs-lookup"><span data-stu-id="7fb54-312">For example:</span></span>

* <span data-ttu-id="7fb54-313">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="7fb54-313">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="7fb54-314">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="7fb54-314">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="7fb54-315">如果 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> 未设置，则缓存的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="7fb54-315">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> is not set, the cache grows without bound.</span></span> <span data-ttu-id="7fb54-316">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-316">The ASP.NET Core runtime does not trim the cache when system memory is low.</span></span> <span data-ttu-id="7fb54-317">应用程序的体系结构非常多：</span><span class="sxs-lookup"><span data-stu-id="7fb54-317">Apps much be architected to:</span></span>

* <span data-ttu-id="7fb54-318">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="7fb54-318">Limit cache growth.</span></span>
* <span data-ttu-id="7fb54-319"><xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> 当可用内存有限时调用或：</span><span class="sxs-lookup"><span data-stu-id="7fb54-319">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="7fb54-320">以下代码创建了 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> [依赖关系注入](xref:fundamentals/dependency-injection)可访问的无大小固定大小：</span><span class="sxs-lookup"><span data-stu-id="7fb54-320">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="7fb54-321">`SizeLimit` 没有单位。</span><span class="sxs-lookup"><span data-stu-id="7fb54-321">`SizeLimit` does not have units.</span></span> <span data-ttu-id="7fb54-322">如果已设置缓存大小限制，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-322">Cached entries must specify size in whatever units they deem most appropriate if the cache size limit has been set.</span></span> <span data-ttu-id="7fb54-323">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="7fb54-323">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="7fb54-324">如果缓存条目大小之和超过指定的值，则不会缓存条目 `SizeLimit` 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-324">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="7fb54-325">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="7fb54-325">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="7fb54-326">以下代码将注册 `MyMemoryCache` 到 [依赖关系注入](xref:fundamentals/dependency-injection) 容器。</span><span class="sxs-lookup"><span data-stu-id="7fb54-326">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="7fb54-327">`MyMemoryCache` 对于识别此大小限制缓存并知道如何适当地设置缓存条目大小的组件，将创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-327">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="7fb54-328">以下代码使用 `MyMemoryCache` ：</span><span class="sxs-lookup"><span data-stu-id="7fb54-328">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="7fb54-329">缓存项的大小可以通过 [大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) 或 [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) 扩展方法来设置：</span><span class="sxs-lookup"><span data-stu-id="7fb54-329">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="7fb54-330">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="7fb54-330">MemoryCache.Compact</span></span>

<span data-ttu-id="7fb54-331">`MemoryCache.Compact` 尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="7fb54-331">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="7fb54-332">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-332">All expired items.</span></span>
* <span data-ttu-id="7fb54-333">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="7fb54-333">Items by priority.</span></span> <span data-ttu-id="7fb54-334">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-334">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="7fb54-335">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="7fb54-335">Least recently used objects.</span></span>
* <span data-ttu-id="7fb54-336">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-336">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="7fb54-337">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-337">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="7fb54-338">永远不会删除具有优先级的固定项 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-338">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="7fb54-339">有关详细信息，请参阅 [GitHub 上的 Compact 源](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) 。</span><span class="sxs-lookup"><span data-stu-id="7fb54-339">See [Compact source on GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="7fb54-340">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="7fb54-340">Cache dependencies</span></span>

<span data-ttu-id="7fb54-341">下面的示例演示如何在依赖条目过期时使缓存条目过期。</span><span class="sxs-lookup"><span data-stu-id="7fb54-341">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="7fb54-342"><xref:Microsoft.Extensions.Primitives.CancellationChangeToken>添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-342">A <xref:Microsoft.Extensions.Primitives.CancellationChangeToken> is added to the cached item.</span></span> <span data-ttu-id="7fb54-343">当 `Cancel` 在上调用时 `CancellationTokenSource` ，将逐出两个缓存项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-343">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="7fb54-344">使用 `CancellationTokenSource` 允许将多个缓存条目作为一个组逐出。</span><span class="sxs-lookup"><span data-stu-id="7fb54-344">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="7fb54-345">`using`在上面的代码中，在块中创建的缓存条目 `using` 将继承触发器和过期设置。</span><span class="sxs-lookup"><span data-stu-id="7fb54-345">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="7fb54-346">附加说明</span><span class="sxs-lookup"><span data-stu-id="7fb54-346">Additional notes</span></span>

* <span data-ttu-id="7fb54-347">使用回调来重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="7fb54-347">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="7fb54-348">多个请求可以查找缓存的密钥值为空，因为回调未完成。</span><span class="sxs-lookup"><span data-stu-id="7fb54-348">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="7fb54-349">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="7fb54-349">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="7fb54-350">当使用一个缓存条目创建另一个缓存条目时，子对象会复制父条目的过期令牌和基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="7fb54-350">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="7fb54-351">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="7fb54-351">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="7fb54-352">使用 [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) 设置从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="7fb54-352">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="background-cache-update"></a><span data-ttu-id="7fb54-353">后台缓存更新</span><span class="sxs-lookup"><span data-stu-id="7fb54-353">Background cache update</span></span>

<span data-ttu-id="7fb54-354">使用 [后台服务](xref:fundamentals/host/hosted-services) （如） <xref:Microsoft.Extensions.Hosting.IHostedService> 来更新缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-354">Use a [background service](xref:fundamentals/host/hosted-services) such as <xref:Microsoft.Extensions.Hosting.IHostedService> to update the cache.</span></span> <span data-ttu-id="7fb54-355">后台服务可重新计算条目，然后仅在准备就绪时将其分配给缓存。</span><span class="sxs-lookup"><span data-stu-id="7fb54-355">The background service can recompute the entries and then assign them to the cache only when they’re ready.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7fb54-356">其他资源</span><span class="sxs-lookup"><span data-stu-id="7fb54-356">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
