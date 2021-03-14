---
title: 在 ASP.NET Core 中路由到控制器操作
author: rick-anderson
description: 了解 ASP.NET Core MVC 如何使用路由中间件来匹配传入请求的 URL 并将它们映射到操作。
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: eeae0ef44fc9b8a92da40481f5dbc7422ed8d43c
ms.sourcegitcommit: d5fa39765959738eed4bcf5ee0b207cefddb4873
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2021
ms.locfileid: "103460438"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a><span data-ttu-id="fcc46-103">在 ASP.NET Core 中路由到控制器操作</span><span class="sxs-lookup"><span data-stu-id="fcc46-103">Routing to controller actions in ASP.NET Core</span></span>

<span data-ttu-id="fcc46-104">作者：[Ryan Nowak](https://github.com/rynowak)、[Kirk Larkinn](https://twitter.com/serpent5) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fcc46-104">By [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fcc46-105">ASP.NET Core 控制器使用路由 [中间件](xref:fundamentals/middleware/index) 来匹配传入请求的 url，并将其映射到 [操作](#action)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-105">ASP.NET Core controllers use the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to [actions](#action).</span></span>  <span data-ttu-id="fcc46-106">路由模板：</span><span class="sxs-lookup"><span data-stu-id="fcc46-106">Route templates:</span></span>

* <span data-ttu-id="fcc46-107">在启动代码或属性中定义。</span><span class="sxs-lookup"><span data-stu-id="fcc46-107">Are defined in startup code or attributes.</span></span>
* <span data-ttu-id="fcc46-108">描述 URL 路径如何与 [操作](#action)匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-108">Describe how URL paths are matched to [actions](#action).</span></span>
* <span data-ttu-id="fcc46-109">用于生成链接的 Url。</span><span class="sxs-lookup"><span data-stu-id="fcc46-109">Are used to generate URLs for links.</span></span> <span data-ttu-id="fcc46-110">生成的链接通常在响应中返回。</span><span class="sxs-lookup"><span data-stu-id="fcc46-110">The generated links are typically returned in responses.</span></span>

<span data-ttu-id="fcc46-111">操作是按 [逆路由](#cr) 或按 [属性路由](#ar)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-111">Actions are either [conventionally-routed](#cr) or [attribute-routed](#ar).</span></span> <span data-ttu-id="fcc46-112">在控制器或 [操作](#action) 上放置路由，使其属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-112">Placing a route on the controller or [action](#action) makes it attribute-routed.</span></span> <span data-ttu-id="fcc46-113">有关详细信息，请参阅[混合路由](#routing-mixed-ref-label)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-113">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="fcc46-114">本文档：</span><span class="sxs-lookup"><span data-stu-id="fcc46-114">This document:</span></span>

* <span data-ttu-id="fcc46-115">说明 MVC 与路由之间的交互：</span><span class="sxs-lookup"><span data-stu-id="fcc46-115">Explains the interactions between MVC and routing:</span></span>
  * <span data-ttu-id="fcc46-116">典型的 MVC 应用使用路由功能的方式。</span><span class="sxs-lookup"><span data-stu-id="fcc46-116">How typical MVC apps make use of routing features.</span></span>
  * <span data-ttu-id="fcc46-117">涵盖两种：</span><span class="sxs-lookup"><span data-stu-id="fcc46-117">Covers both:</span></span>
    * <span data-ttu-id="fcc46-118">通常用于控制器和视图的[传统路由](#cr)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-118">[Conventional routing](#cr) typically used with controllers and views.</span></span>
    * <span data-ttu-id="fcc46-119">用于 REST Api 的 *属性路由*。</span><span class="sxs-lookup"><span data-stu-id="fcc46-119">*Attribute routing* used with REST APIs.</span></span> <span data-ttu-id="fcc46-120">如果主要对 REST Api 的路由感兴趣，请跳转到 [Rest api 的属性路由](#ar) 部分。</span><span class="sxs-lookup"><span data-stu-id="fcc46-120">If you're primarily interested in routing for REST APIs, jump to the [Attribute routing for REST APIs](#ar) section.</span></span>
  * <span data-ttu-id="fcc46-121">有关高级路由的详细信息，请参阅 [路由](xref:fundamentals/routing) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-121">See [Routing](xref:fundamentals/routing) for advanced routing details.</span></span>
* <span data-ttu-id="fcc46-122">指 ASP.NET Core 3.0 中添加的默认路由系统，称为 "终结点路由"。</span><span class="sxs-lookup"><span data-stu-id="fcc46-122">Refers to the default routing system added in ASP.NET Core 3.0, called endpoint routing.</span></span> <span data-ttu-id="fcc46-123">出于兼容性目的，可以将控制器用于以前版本的路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-123">It's possible to use controllers with the previous version of routing for compatibility purposes.</span></span> <span data-ttu-id="fcc46-124">有关说明，请参阅 [2.2-3.0 迁移指南](xref:migration/22-to-30) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-124">See the [2.2-3.0 migration guide](xref:migration/22-to-30) for instructions.</span></span> <span data-ttu-id="fcc46-125">有关旧路由系统上的参考材料，请参阅 [本文档的2.2 版本](xref:mvc/controllers/routing?view=aspnetcore-2.2) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-125">Refer to the [2.2 version of this document](xref:mvc/controllers/routing?view=aspnetcore-2.2) for reference material on the legacy routing system.</span></span>

<a name="cr"></a>

## <a name="set-up-conventional-route"></a><span data-ttu-id="fcc46-126">设置传统路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-126">Set up conventional route</span></span>

<span data-ttu-id="fcc46-127">`Startup.Configure` 使用 [传统路由](#crd)时，通常具有如下所示的代码：</span><span class="sxs-lookup"><span data-stu-id="fcc46-127">`Startup.Configure` typically has code similar to the following when using [conventional routing](#crd):</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

<span data-ttu-id="fcc46-128">在对的调用中 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> ， <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> 用于创建单个路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-128">Inside the call to <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> is used to create a single route.</span></span> <span data-ttu-id="fcc46-129">单路由命名为 `default` route。</span><span class="sxs-lookup"><span data-stu-id="fcc46-129">The single route is named `default` route.</span></span> <span data-ttu-id="fcc46-130">大多数具有控制器和视图的应用都使用类似于路由的路由模板 `default` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-130">Most apps with controllers and views use a route template similar to the `default` route.</span></span> <span data-ttu-id="fcc46-131">REST Api 应使用 [属性路由](#ar)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-131">REST APIs should use [attribute routing](#ar).</span></span>

<span data-ttu-id="fcc46-132">路由模板 `"{controller=Home}/{action=Index}/{id?}"` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-132">The route template `"{controller=Home}/{action=Index}/{id?}"`:</span></span>

* <span data-ttu-id="fcc46-133">匹配 URL 路径，例如 `/Products/Details/5`</span><span class="sxs-lookup"><span data-stu-id="fcc46-133">Matches a URL path like `/Products/Details/5`</span></span>
* <span data-ttu-id="fcc46-134">`{ controller = Products, action = Details, id = 5 }`通过词汇切分路径来提取路由值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-134">Extracts the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="fcc46-135">如果应用有一个名为的控制器 `ProductsController` 和一个操作，则提取路由值将导致匹配 `Details` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-135">The extraction of route values results in a match if the app has a controller named `ProductsController` and a `Details` action:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* <span data-ttu-id="fcc46-136">`/Products/Details/5` 模型将的值绑定 `id = 5` ，以将 `id` 参数设置为 `5` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-136">`/Products/Details/5` model binds the value of `id = 5` to set the `id` parameter to `5`.</span></span> <span data-ttu-id="fcc46-137">有关更多详细信息，请参阅 [模型绑定](xref:mvc/models/model-binding) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-137">See [Model Binding](xref:mvc/models/model-binding) for more details.</span></span>
* <span data-ttu-id="fcc46-138">`{controller=Home}``Home`将定义为默认值 `controller` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-138">`{controller=Home}` defines `Home` as the default `controller`.</span></span>
* <span data-ttu-id="fcc46-139">`{action=Index}``Index`将定义为默认值 `action` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-139">`{action=Index}` defines `Index` as the default `action`.</span></span>
*  <span data-ttu-id="fcc46-140">`?`中的字符 `{id?}` 定义 `id` 为可选。</span><span class="sxs-lookup"><span data-stu-id="fcc46-140">The `?` character in `{id?}` defines `id` as optional.</span></span>
  * <span data-ttu-id="fcc46-141">默认路由参数和可选路由参数不必包含在 URL 路径中进行匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-141">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="fcc46-142">有关路由模板语法的详细说明，请参阅[路由模板参考](xref:fundamentals/routing#route-template-reference)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-142">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>
* <span data-ttu-id="fcc46-143">匹配 URL 路径 `/` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-143">Matches the URL path `/`.</span></span>
* <span data-ttu-id="fcc46-144">生成路由值 `{ controller = Home, action = Index }` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-144">Produces the route values `{ controller = Home, action = Index }`.</span></span>

<span data-ttu-id="fcc46-145">和的值 `controller` 将 `action` 使用默认值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-145">The values for `controller` and `action` make use of the default values.</span></span> <span data-ttu-id="fcc46-146">`id` 不会生成值，因为 URL 路径中没有相应的段。</span><span class="sxs-lookup"><span data-stu-id="fcc46-146">`id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="fcc46-147">`/` 仅当存在和操作时才匹配 `HomeController` `Index` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-147">`/` only matches if there exists a `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="fcc46-148">使用上述控制器定义和路由模板，为 `HomeController.Index` 以下 URL 路径运行操作：</span><span class="sxs-lookup"><span data-stu-id="fcc46-148">Using the preceding controller definition and route template, the `HomeController.Index` action is run for the following URL paths:</span></span>

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

<span data-ttu-id="fcc46-149">URL 路径 `/` 使用路由模板默认 `Home` 控制器和 `Index` 操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-149">The URL path `/` uses the route template default `Home` controllers and `Index` action.</span></span> <span data-ttu-id="fcc46-150">URL 路径 `/Home` 使用路由模板默认 `Index` 操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-150">The URL path `/Home` uses the route template default `Index` action.</span></span>

<span data-ttu-id="fcc46-151">简便方法 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>：</span><span class="sxs-lookup"><span data-stu-id="fcc46-151">The convenience method <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:</span></span>

```csharp
endpoints.MapDefaultControllerRoute();
```

<span data-ttu-id="fcc46-152">代替</span><span class="sxs-lookup"><span data-stu-id="fcc46-152">Replaces:</span></span>

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> <span data-ttu-id="fcc46-153">使用 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> 和中间件配置路由 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-153">Routing is configured using the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> middleware.</span></span> <span data-ttu-id="fcc46-154">使用控制器：</span><span class="sxs-lookup"><span data-stu-id="fcc46-154">To use controllers:</span></span>
>
> * <span data-ttu-id="fcc46-155"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A>在内部调用 `UseEndpoints` ，以映射[属性路由](#ar)控制器。</span><span class="sxs-lookup"><span data-stu-id="fcc46-155">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> inside `UseEndpoints` to map [attribute routed](#ar) controllers.</span></span>
> * <span data-ttu-id="fcc46-156">调用 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> 或 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 以映射 [逆路由](#cr) 控制器和 [属性路由](#ar) 控制器。</span><span class="sxs-lookup"><span data-stu-id="fcc46-156">Call <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> or <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>, to map both [conventionally routed](#cr) controllers and [attribute routed](#ar) controllers.</span></span>

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a><span data-ttu-id="fcc46-157">传统路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-157">Conventional routing</span></span>

<span data-ttu-id="fcc46-158">传统路由用于控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="fcc46-158">Conventional routing is used with controllers and views.</span></span> <span data-ttu-id="fcc46-159">`default` 路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-159">The `default` route:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

<span data-ttu-id="fcc46-160">前面是 *传统路由* 的示例。</span><span class="sxs-lookup"><span data-stu-id="fcc46-160">The preceding is an example of a *conventional route*.</span></span> <span data-ttu-id="fcc46-161">它被称为 *传统路由* ，因为它建立了一个 URL 路径 *约定* ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-161">It's called *conventional routing* because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="fcc46-162">第一个路径段 `{controller=Home}` 映射到控制器名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-162">The first path segment, `{controller=Home}`, maps to the controller name.</span></span>
* <span data-ttu-id="fcc46-163">第二段 `{action=Index}` 映射到 [操作](#action) 名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-163">The second segment, `{action=Index}`, maps to the [action](#action) name.</span></span>
* <span data-ttu-id="fcc46-164">第三段 `{id?}` 用于可选 `id` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-164">The third segment, `{id?}` is used for an optional `id`.</span></span> <span data-ttu-id="fcc46-165">`?`中的 `{id?}` 使其成为可选的。</span><span class="sxs-lookup"><span data-stu-id="fcc46-165">The `?` in `{id?}` makes it optional.</span></span> <span data-ttu-id="fcc46-166">`id` 用于映射到模型实体。</span><span class="sxs-lookup"><span data-stu-id="fcc46-166">`id` is used to map to a model entity.</span></span>

<span data-ttu-id="fcc46-167">使用此 `default` 路由，URL 路径：</span><span class="sxs-lookup"><span data-stu-id="fcc46-167">Using this `default` route, the URL path:</span></span>

* <span data-ttu-id="fcc46-168">`/Products/List` 映射到 `ProductsController.List` 操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-168">`/Products/List` maps to the `ProductsController.List` action.</span></span>
* <span data-ttu-id="fcc46-169">`/Blog/Article/17` 映射到 `BlogController.Article` 和通常将参数绑定 `id` 到17。</span><span class="sxs-lookup"><span data-stu-id="fcc46-169">`/Blog/Article/17` maps to `BlogController.Article` and typically model binds the `id` parameter to 17.</span></span>

<span data-ttu-id="fcc46-170">此映射：</span><span class="sxs-lookup"><span data-stu-id="fcc46-170">This mapping:</span></span>

* <span data-ttu-id="fcc46-171">**仅** 基于控制器和 [操作](#action)名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-171">Is based on the controller and [action](#action) names **only**.</span></span>
* <span data-ttu-id="fcc46-172">不基于命名空间、源文件位置或方法参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-172">Isn't based on namespaces, source file locations, or method parameters.</span></span>

<span data-ttu-id="fcc46-173">通过使用传统路由和默认路由，可以创建应用，而无需为每个操作都提供新的 URL 模式。</span><span class="sxs-lookup"><span data-stu-id="fcc46-173">Using conventional routing with the default route allows creating the app without having to come up with a new URL pattern for each action.</span></span> <span data-ttu-id="fcc46-174">对于具有 [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) 样式操作的应用，跨控制器的 url 保持一致：</span><span class="sxs-lookup"><span data-stu-id="fcc46-174">For an app with [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) style actions, having consistency for the URLs across controllers:</span></span>

* <span data-ttu-id="fcc46-175">有助于简化代码。</span><span class="sxs-lookup"><span data-stu-id="fcc46-175">Helps simplify the code.</span></span>
* <span data-ttu-id="fcc46-176">使 UI 更具可预测性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-176">Makes the UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="fcc46-177">`id`前面的代码中的将由路由模板定义为可选的。</span><span class="sxs-lookup"><span data-stu-id="fcc46-177">The `id` in the preceding code is defined as optional by the route template.</span></span> <span data-ttu-id="fcc46-178">无需作为 URL 的一部分提供的可选 ID 即可执行操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-178">Actions can execute without the optional ID provided as part of the URL.</span></span> <span data-ttu-id="fcc46-179">通常， `id` 从 URL 中省略时：</span><span class="sxs-lookup"><span data-stu-id="fcc46-179">Generally, when`id` is omitted from the URL:</span></span>
>
> * <span data-ttu-id="fcc46-180">`id``0`由模型绑定设置为。</span><span class="sxs-lookup"><span data-stu-id="fcc46-180">`id` is set to `0` by model binding.</span></span>
> * <span data-ttu-id="fcc46-181">在数据库匹配中找不到实体 `id == 0` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-181">No entity is found in the database matching `id == 0`.</span></span>
>
> <span data-ttu-id="fcc46-182">[特性路由](#ar) 可提供精细的控制，以使某些操作（而不是其他操作）需要 ID。</span><span class="sxs-lookup"><span data-stu-id="fcc46-182">[Attribute routing](#ar) provides fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="fcc46-183">按照约定，文档包含可选参数，如 `id` 它们可能出现在正确用法中的情况。</span><span class="sxs-lookup"><span data-stu-id="fcc46-183">By convention, the documentation includes optional parameters like `id` when they're likely to appear in correct usage.</span></span>

<span data-ttu-id="fcc46-184">大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。</span><span class="sxs-lookup"><span data-stu-id="fcc46-184">Most apps should choose a basic and descriptive routing scheme so that URLs are readable and meaningful.</span></span> <span data-ttu-id="fcc46-185">默认传统路由 `{controller=Home}/{action=Index}/{id?}`：</span><span class="sxs-lookup"><span data-stu-id="fcc46-185">The default conventional route `{controller=Home}/{action=Index}/{id?}`:</span></span>

* <span data-ttu-id="fcc46-186">支持基本的描述性路由方案。</span><span class="sxs-lookup"><span data-stu-id="fcc46-186">Supports a basic and descriptive routing scheme.</span></span>
* <span data-ttu-id="fcc46-187">是基于 UI 的应用的有用起点。</span><span class="sxs-lookup"><span data-stu-id="fcc46-187">Is a useful starting point for UI-based apps.</span></span>
* <span data-ttu-id="fcc46-188">是许多 web UI 应用程序所需的唯一路由模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-188">Is the only route template needed for many web UI apps.</span></span> <span data-ttu-id="fcc46-189">对于较大的 web UI 应用，使用 [区域](#areas) 的其他路由常常是必需的。</span><span class="sxs-lookup"><span data-stu-id="fcc46-189">For larger web UI apps, another route using [Areas](#areas) is frequently all that's needed.</span></span>

<span data-ttu-id="fcc46-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> 和 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-190"><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :</span></span>

* <span data-ttu-id="fcc46-191">根据调用的顺序，自动将 **订单** 值分配给其终结点。</span><span class="sxs-lookup"><span data-stu-id="fcc46-191">Automatically assign an **order** value to their endpoints based on the order they are invoked.</span></span>

<span data-ttu-id="fcc46-192">ASP.NET Core 3.0 及更高版本中的终结点路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-192">Endpoint routing in ASP.NET Core 3.0 and later:</span></span>

* <span data-ttu-id="fcc46-193">没有路由的概念。</span><span class="sxs-lookup"><span data-stu-id="fcc46-193">Doesn't have a concept of routes.</span></span>
* <span data-ttu-id="fcc46-194">不为执行扩展性提供排序保证，同时处理所有终结点。</span><span class="sxs-lookup"><span data-stu-id="fcc46-194">Doesn't provide ordering guarantees for the execution of extensibility,  all endpoints are processed at once.</span></span>

<span data-ttu-id="fcc46-195">启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。</span><span class="sxs-lookup"><span data-stu-id="fcc46-195">Enable [Logging](xref:fundamentals/logging/index) to see how the built-in routing implementations, such as <xref:Microsoft.AspNetCore.Routing.Route>, match requests.</span></span>

<span data-ttu-id="fcc46-196">[属性路由](#ar) 将在本文档的后面部分进行说明。</span><span class="sxs-lookup"><span data-stu-id="fcc46-196">[Attribute routing](#ar) is explained later in this document.</span></span>

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a><span data-ttu-id="fcc46-197">多个传统路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-197">Multiple conventional routes</span></span>

<span data-ttu-id="fcc46-198">[](#cr) `UseEndpoints` 通过添加更多对和的调用，可以在内添加多个传统路由 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-198">Multiple [conventional routes](#cr) can be added inside `UseEndpoints` by adding more calls to <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A>.</span></span> <span data-ttu-id="fcc46-199">这样做允许定义多个约定，或者添加专用于特定 [操作](#action)的传统路由，例如：</span><span class="sxs-lookup"><span data-stu-id="fcc46-199">Doing so allows defining multiple conventions, or to adding conventional routes that are dedicated to a specific [action](#action), such as:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

<span data-ttu-id="fcc46-200">`blog`上述代码中的路由是专用的 **传统路由**。</span><span class="sxs-lookup"><span data-stu-id="fcc46-200">The `blog` route in the preceding code is a **dedicated conventional route**.</span></span> <span data-ttu-id="fcc46-201">这称为专用的传统路由，因为：</span><span class="sxs-lookup"><span data-stu-id="fcc46-201">It's called a dedicated conventional route because:</span></span>

* <span data-ttu-id="fcc46-202">它使用 [传统路由](#cr)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-202">It uses [conventional routing](#cr).</span></span>
* <span data-ttu-id="fcc46-203">它专用于特定的 [操作](#action)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-203">It's dedicated to a specific [action](#action).</span></span>

<span data-ttu-id="fcc46-204">因为 `controller` 和 `action` 不会以参数形式出现在路由模板中 `"blog/{*article}"` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-204">Because `controller` and `action` don't appear in the route template `"blog/{*article}"` as parameters:</span></span>

* <span data-ttu-id="fcc46-205">它们只能具有默认值 `{ controller = "Blog", action = "Article" }` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-205">They can only have the default values `{ controller = "Blog", action = "Article" }`.</span></span>
* <span data-ttu-id="fcc46-206">此路由始终映射到操作 `BlogController.Article` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-206">This route always maps to the action `BlogController.Article`.</span></span>

<span data-ttu-id="fcc46-207">`/Blog`、 `/Blog/Article` 和 `/Blog/{any-string}` 是唯一与博客路由匹配的 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="fcc46-207">`/Blog`, `/Blog/Article`, and `/Blog/{any-string}` are the only URL paths that match the blog route.</span></span>

<span data-ttu-id="fcc46-208">上面的示例：</span><span class="sxs-lookup"><span data-stu-id="fcc46-208">The preceding example:</span></span>

* <span data-ttu-id="fcc46-209">`blog` 路由具有比路由更高的优先级， `default` 因为它是首先添加的。</span><span class="sxs-lookup"><span data-stu-id="fcc46-209">`blog` route has a higher priority for matches than the `default` route because it is added first.</span></span>
* <span data-ttu-id="fcc46-210">是一个 [缩略](https://developer.mozilla.org/docs/Glossary/Slug) 名称样式路由的示例，在此示例中，通常将项目名称作为 URL 的一部分。</span><span class="sxs-lookup"><span data-stu-id="fcc46-210">Is an example of [Slug](https://developer.mozilla.org/docs/Glossary/Slug) style routing where it's typical to have an article name as part of the URL.</span></span>

> [!WARNING]
> <span data-ttu-id="fcc46-211">在 ASP.NET Core 3.0 及更高版本中，路由不会：</span><span class="sxs-lookup"><span data-stu-id="fcc46-211">In ASP.NET Core 3.0 and later, routing doesn't:</span></span>
> * <span data-ttu-id="fcc46-212">定义名为 *route* 的概念。</span><span class="sxs-lookup"><span data-stu-id="fcc46-212">Define a concept called a *route*.</span></span> <span data-ttu-id="fcc46-213">`UseRouting` 向中间件管道添加路由匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-213">`UseRouting` adds route matching to the middleware pipeline.</span></span> <span data-ttu-id="fcc46-214">`UseRouting`中间件会查看应用中定义的终结点集，并根据请求选择最佳的终结点匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-214">The `UseRouting` middleware looks at the set of endpoints defined in the app, and selects the best endpoint match based on the request.</span></span>
> * <span data-ttu-id="fcc46-215">提供可扩展性（如或）的执行 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 顺序 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-215">Provide guarantees about the execution order of extensibility like <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> or <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>.</span></span>
>
><span data-ttu-id="fcc46-216">有关路由的参考材料，请参阅 [路由](xref:fundamentals/routing) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-216">See [Routing](xref:fundamentals/routing) for reference material on routing.</span></span>

<a name="cro"></a>

### <a name="conventional-routing-order"></a><span data-ttu-id="fcc46-217">传统路由顺序</span><span class="sxs-lookup"><span data-stu-id="fcc46-217">Conventional routing order</span></span>

<span data-ttu-id="fcc46-218">传统路由仅匹配应用定义的操作和控制器的组合。</span><span class="sxs-lookup"><span data-stu-id="fcc46-218">Conventional routing only matches a combination of action and controller that are defined by the app.</span></span> <span data-ttu-id="fcc46-219">这旨在简化传统路由重叠的情况。</span><span class="sxs-lookup"><span data-stu-id="fcc46-219">This is intended to simplify cases where conventional routes overlap.</span></span>
<span data-ttu-id="fcc46-220">使用、和添加路由时， <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> 会根据 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 调用顺序，自动将其分配给终结点。</span><span class="sxs-lookup"><span data-stu-id="fcc46-220">Adding routes using <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>, and <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> automatically assign an order value to their endpoints based on the order they are invoked.</span></span> <span data-ttu-id="fcc46-221">与前面显示的路由的匹配具有更高的优先级。</span><span class="sxs-lookup"><span data-stu-id="fcc46-221">Matches from a route that appears earlier have a higher priority.</span></span> <span data-ttu-id="fcc46-222">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="fcc46-222">Conventional routing is order-dependent.</span></span> <span data-ttu-id="fcc46-223">通常情况下，应将具有区域的路由置于更早的位置，因为它们比没有区域的路由更具体。</span><span class="sxs-lookup"><span data-stu-id="fcc46-223">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span> <span data-ttu-id="fcc46-224">使用全部捕获路由参数的[专用传统路由](#dcr) `{*article}` 可以使路由过于[贪婪](xref:fundamentals/routing#greedy)，这意味着它与你打算与其他路由匹配的 url 相匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-224">[Dedicated conventional routes](#dcr) with catch-all route parameters like `{*article}` can make a route too [greedy](xref:fundamentals/routing#greedy), meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="fcc46-225">将贪婪路由置于路由表中，以防止贪婪匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-225">Put the greedy routes later in the route table to prevent greedy matches.</span></span>

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a><span data-ttu-id="fcc46-226">解决不明确的操作</span><span class="sxs-lookup"><span data-stu-id="fcc46-226">Resolving ambiguous actions</span></span>

<span data-ttu-id="fcc46-227">如果两个终结点通过路由匹配，则路由必须执行下列操作之一：</span><span class="sxs-lookup"><span data-stu-id="fcc46-227">When two endpoints match through routing, routing must do one of the following:</span></span>

* <span data-ttu-id="fcc46-228">选择最佳的候选项。</span><span class="sxs-lookup"><span data-stu-id="fcc46-228">Choose the best candidate.</span></span>
* <span data-ttu-id="fcc46-229">引发异常。</span><span class="sxs-lookup"><span data-stu-id="fcc46-229">Throw an exception.</span></span>

<span data-ttu-id="fcc46-230">例如：</span><span class="sxs-lookup"><span data-stu-id="fcc46-230">For example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

<span data-ttu-id="fcc46-231">前面的控制器定义了两个匹配的操作：</span><span class="sxs-lookup"><span data-stu-id="fcc46-231">The preceding controller defines two actions that match:</span></span>

* <span data-ttu-id="fcc46-232">URL 路径 `/Products33/Edit/17`</span><span class="sxs-lookup"><span data-stu-id="fcc46-232">The URL path `/Products33/Edit/17`</span></span>
* <span data-ttu-id="fcc46-233">路由数据 `{ controller = Products33, action = Edit, id = 17 }` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-233">Route data `{ controller = Products33, action = Edit, id = 17 }`.</span></span>

<span data-ttu-id="fcc46-234">这是 MVC 控制器的典型模式：</span><span class="sxs-lookup"><span data-stu-id="fcc46-234">This is a typical pattern for MVC controllers:</span></span>

* <span data-ttu-id="fcc46-235">`Edit(int)` 显示用于编辑产品的窗体。</span><span class="sxs-lookup"><span data-stu-id="fcc46-235">`Edit(int)` displays a form to edit a product.</span></span>
* <span data-ttu-id="fcc46-236">`Edit(int, Product)` 处理已发布的窗体。</span><span class="sxs-lookup"><span data-stu-id="fcc46-236">`Edit(int, Product)` processes  the posted form.</span></span>

<span data-ttu-id="fcc46-237">解析正确的路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-237">To resolve the correct route:</span></span>

* <span data-ttu-id="fcc46-238">`Edit(int, Product)` 当请求为 HTTP 时选择 `POST` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-238">`Edit(int, Product)` is selected when the request is an HTTP `POST`.</span></span>
* <span data-ttu-id="fcc46-239">`Edit(int)` 当 [HTTP 谓词](#verb) 为其他任何内容时，将选择。</span><span class="sxs-lookup"><span data-stu-id="fcc46-239">`Edit(int)` is selected when the [HTTP verb](#verb) is anything else.</span></span> <span data-ttu-id="fcc46-240">`Edit(int)` 通常通过调用 `GET` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-240">`Edit(int)` is generally called via `GET`.</span></span>

<span data-ttu-id="fcc46-241"><xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>提供了 `[HttpPost]` 用于路由的，以便它可以根据请求的 HTTP 方法进行选择。</span><span class="sxs-lookup"><span data-stu-id="fcc46-241">The <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, `[HttpPost]`, is provided to routing so that it can choose based on the HTTP method of the request.</span></span> <span data-ttu-id="fcc46-242">`HttpPostAttribute` `Edit(int, Product)` 比更好匹配 `Edit(int)` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-242">The `HttpPostAttribute` makes `Edit(int, Product)` a better match than `Edit(int)`.</span></span>

<span data-ttu-id="fcc46-243">了解属性的角色非常重要 `HttpPostAttribute` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-243">It's important to understand the role of attributes like `HttpPostAttribute`.</span></span> <span data-ttu-id="fcc46-244">为其他 [HTTP 谓词](#verb)定义了类似的属性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-244">Similar attributes are defined for other [HTTP verbs](#verb).</span></span> <span data-ttu-id="fcc46-245">在 [传统路由](#cr)中，当操作是显示形式的一部分时，操作通常使用相同的操作名称，即提交窗体工作流。</span><span class="sxs-lookup"><span data-stu-id="fcc46-245">In [conventional routing](#cr), it's common for actions to use the same action name when they're part of a show form, submit form workflow.</span></span> <span data-ttu-id="fcc46-246">例如，请参阅 [检查两个编辑操作方法](xref:tutorials/first-mvc-app/controller-methods-views#get-post)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-246">For example, see [Examine the two Edit action methods](xref:tutorials/first-mvc-app/controller-methods-views#get-post).</span></span>

<span data-ttu-id="fcc46-247">如果路由无法选择最佳候选项，则 <xref:System.Reflection.AmbiguousMatchException> 会引发，并列出多个匹配的终结点。</span><span class="sxs-lookup"><span data-stu-id="fcc46-247">If routing can't choose a best candidate, an <xref:System.Reflection.AmbiguousMatchException> is thrown, listing the multiple matched endpoints.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a><span data-ttu-id="fcc46-248">传统路由名称</span><span class="sxs-lookup"><span data-stu-id="fcc46-248">Conventional route names</span></span>

<span data-ttu-id="fcc46-249">`"blog"` `"default"` 以下示例中的字符串和是传统的路由名称：</span><span class="sxs-lookup"><span data-stu-id="fcc46-249">The strings  `"blog"` and `"default"` in the following examples are conventional route names:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="fcc46-250">路由名称为路由指定逻辑名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-250">The route names give the route a logical name.</span></span> <span data-ttu-id="fcc46-251">命名路由可用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-251">The named route can be used for URL generation.</span></span> <span data-ttu-id="fcc46-252">当路由顺序使 URL 生成复杂化时，使用命名路由可简化 URL 创建。</span><span class="sxs-lookup"><span data-stu-id="fcc46-252">Using a named route simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="fcc46-253">路由名称必须是唯一的应用程序范围。</span><span class="sxs-lookup"><span data-stu-id="fcc46-253">Route names must be unique application wide.</span></span>

<span data-ttu-id="fcc46-254">路由名称：</span><span class="sxs-lookup"><span data-stu-id="fcc46-254">Route names:</span></span>

* <span data-ttu-id="fcc46-255">不会影响 URL 匹配或处理请求。</span><span class="sxs-lookup"><span data-stu-id="fcc46-255">Have no impact on URL matching or handling of requests.</span></span>
* <span data-ttu-id="fcc46-256">仅用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-256">Are used only for URL generation.</span></span>

<span data-ttu-id="fcc46-257">路由名称概念在路由中表示为 [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-257">The route name concept is represented in routing as [IEndpointNameMetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata).</span></span> <span data-ttu-id="fcc46-258">术语 **路由名称** 和 **终结点名称**：</span><span class="sxs-lookup"><span data-stu-id="fcc46-258">The terms **route name** and **endpoint name**:</span></span>

* <span data-ttu-id="fcc46-259">是可互换的。</span><span class="sxs-lookup"><span data-stu-id="fcc46-259">Are interchangeable.</span></span>
* <span data-ttu-id="fcc46-260">文档和代码中使用哪一个取决于所述的 API。</span><span class="sxs-lookup"><span data-stu-id="fcc46-260">Which one is used in documentation and code depends on the API being described.</span></span>

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a><span data-ttu-id="fcc46-261">REST Api 的属性路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-261">Attribute routing for REST APIs</span></span>

<span data-ttu-id="fcc46-262">REST Api 应使用属性路由将应用功能建模为一组资源，其中的操作由 [HTTP 谓词](#verb)表示。</span><span class="sxs-lookup"><span data-stu-id="fcc46-262">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by [HTTP verbs](#verb).</span></span>

<span data-ttu-id="fcc46-263">属性路由使用一组属性将操作直接映射到路由模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-263">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="fcc46-264">下面 `StartUp.Configure` 是 REST API 的典型代码，并在下一个示例中使用：</span><span class="sxs-lookup"><span data-stu-id="fcc46-264">The following `StartUp.Configure` code is typical for a REST API and is used in the next sample:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

<span data-ttu-id="fcc46-265">在前面的代码中，在 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> 中调用， `UseEndpoints` 以映射属性路由控制器。</span><span class="sxs-lookup"><span data-stu-id="fcc46-265">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> is called inside `UseEndpoints` to map attribute routed controllers.</span></span>

<span data-ttu-id="fcc46-266">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="fcc46-266">In the following example:</span></span>

* <span data-ttu-id="fcc46-267">使用前面的 `Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="fcc46-267">The preceding `Configure` method is used.</span></span>
* <span data-ttu-id="fcc46-268">`HomeController` 匹配一组与默认传统路由匹配的 Url `{controller=Home}/{action=Index}/{id?}` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-268">`HomeController` matches a set of URLs similar to what the default conventional route `{controller=Home}/{action=Index}/{id?}` matches.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

<span data-ttu-id="fcc46-269">对 `HomeController.Index` 任何 URL 路径（、或）运行操作 `/` `/Home` `/Home/Index` `/Home/Index/3` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-269">The `HomeController.Index` action is run for any of the URL paths `/`, `/Home`, `/Home/Index`, or `/Home/Index/3`.</span></span>

<span data-ttu-id="fcc46-270">此示例突出显示了属性路由与 [传统路由](#cr)之间的主要编程区别。</span><span class="sxs-lookup"><span data-stu-id="fcc46-270">This example highlights a key programming difference between attribute routing and [conventional routing](#cr).</span></span> <span data-ttu-id="fcc46-271">属性路由需要更多输入才能指定路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-271">Attribute routing requires more input to specify a route.</span></span> <span data-ttu-id="fcc46-272">传统的默认路由会更简洁地处理路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-272">The conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="fcc46-273">但是，特性路由允许和要求精确控制哪些路由模板适用于每个 [操作](#action)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-273">However, attribute routing allows and requires precise control of which route templates apply to each [action](#action).</span></span>

<span data-ttu-id="fcc46-274">使用属性路由时，控制器和操作名称不会播放任何操作，除非使用 [令牌替换](#routing-token-replacement-templates-ref-label) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-274">With attribute routing, the controller and action names play no part in which action is matched, unless [token replacement](#routing-token-replacement-templates-ref-label) is used.</span></span> <span data-ttu-id="fcc46-275">下面的示例匹配与上一示例相同的 Url：</span><span class="sxs-lookup"><span data-stu-id="fcc46-275">The following example matches the same URLs as the previous example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="fcc46-276">下面的代码使用和的标记 `action` 替换 `controller` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-276">The following code uses token replacement for `action` and `controller`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

<span data-ttu-id="fcc46-277">以下代码适用于 `[Route("[controller]/[action]")]` 控制器：</span><span class="sxs-lookup"><span data-stu-id="fcc46-277">The following code applies `[Route("[controller]/[action]")]` to the controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

<span data-ttu-id="fcc46-278">在前面的代码中， `Index` 方法模板必须在 `/` 路由模板之前预置或 `~/` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-278">In the preceding code, the `Index` method templates must prepend `/` or `~/` to the route templates.</span></span> <span data-ttu-id="fcc46-279">应用于操作的以 `/` 或 `~/` 开头的路由模板不与应用于控制器的路由模板合并。</span><span class="sxs-lookup"><span data-stu-id="fcc46-279">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span>

<span data-ttu-id="fcc46-280">有关路由模板选择的信息，请参阅 [路由模板优先级](xref:fundamentals/routing#rtp) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-280">See [Route template precedence](xref:fundamentals/routing#rtp) for information on route template selection.</span></span>

## <a name="reserved-routing-names"></a><span data-ttu-id="fcc46-281">保留的路由名称</span><span class="sxs-lookup"><span data-stu-id="fcc46-281">Reserved routing names</span></span>

<span data-ttu-id="fcc46-282">使用控制器或页面时，保留的路由参数名称如下 Razor ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-282">The following keywords are reserved route parameter names when using Controllers or Razor Pages:</span></span>

* `action`
* `area`
* `controller`
* `handler`
* `page`

<span data-ttu-id="fcc46-283">使用 `page` 作为带有属性路由的路由参数是一个常见错误。</span><span class="sxs-lookup"><span data-stu-id="fcc46-283">Using `page` as a route parameter with attribute routing is a common error.</span></span> <span data-ttu-id="fcc46-284">这样做会导致在 URL 生成时出现不一致的行为。</span><span class="sxs-lookup"><span data-stu-id="fcc46-284">Doing that results in inconsistent and confusing behavior with URL generation.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

<span data-ttu-id="fcc46-285">URL 生成使用特殊参数名称来确定 URL 生成操作是指引用 Razor 页面还是引用控制器。</span><span class="sxs-lookup"><span data-stu-id="fcc46-285">The special parameter names are used by the URL generation to determine if a URL generation operation refers to a Razor Page or to a Controller.</span></span>

<a name="verb"></a>

## <a name="http-verb-templates"></a><span data-ttu-id="fcc46-286">HTTP 谓词模板</span><span class="sxs-lookup"><span data-stu-id="fcc46-286">HTTP verb templates</span></span>

<span data-ttu-id="fcc46-287">ASP.NET Core 具有以下 HTTP 谓词模板：</span><span class="sxs-lookup"><span data-stu-id="fcc46-287">ASP.NET Core has the following HTTP verb templates:</span></span>

* <span data-ttu-id="fcc46-288">[HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span><span class="sxs-lookup"><span data-stu-id="fcc46-288">[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)</span></span>
* <span data-ttu-id="fcc46-289">[HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span><span class="sxs-lookup"><span data-stu-id="fcc46-289">[[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)</span></span>
* <span data-ttu-id="fcc46-290">[HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span><span class="sxs-lookup"><span data-stu-id="fcc46-290">[[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)</span></span>
* <span data-ttu-id="fcc46-291">[HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span><span class="sxs-lookup"><span data-stu-id="fcc46-291">[[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)</span></span>
* <span data-ttu-id="fcc46-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span><span class="sxs-lookup"><span data-stu-id="fcc46-292">[[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)</span></span>
* <span data-ttu-id="fcc46-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span><span class="sxs-lookup"><span data-stu-id="fcc46-293">[[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)</span></span>

<a name="rt"></a>

### <a name="route-templates"></a><span data-ttu-id="fcc46-294">路由模板</span><span class="sxs-lookup"><span data-stu-id="fcc46-294">Route templates</span></span>

<span data-ttu-id="fcc46-295">ASP.NET Core 具有以下路由模板：</span><span class="sxs-lookup"><span data-stu-id="fcc46-295">ASP.NET Core has the following route templates:</span></span>

* <span data-ttu-id="fcc46-296">所有 [HTTP 谓词模板](#verb) 都是路由模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-296">All the [HTTP verb templates](#verb) are route templates.</span></span>
* <span data-ttu-id="fcc46-297">[路由](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span><span class="sxs-lookup"><span data-stu-id="fcc46-297">[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)</span></span>

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a><span data-ttu-id="fcc46-298">具有 Http 谓词特性的属性路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-298">Attribute routing with Http verb attributes</span></span>

<span data-ttu-id="fcc46-299">请考虑以下控制器：</span><span class="sxs-lookup"><span data-stu-id="fcc46-299">Consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="fcc46-300">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="fcc46-300">In the preceding code:</span></span>

* <span data-ttu-id="fcc46-301">每个操作都包含 `[HttpGet]` 特性，该特性仅将匹配限制为 HTTP GET 请求。</span><span class="sxs-lookup"><span data-stu-id="fcc46-301">Each action contains the `[HttpGet]` attribute, which constrains matching to HTTP GET requests only.</span></span>
* <span data-ttu-id="fcc46-302">`GetProduct`操作包括 `"{id}"` 模板，因此 `id` 附加到 `"api/[controller]"` 控制器上的模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-302">The `GetProduct` action includes the `"{id}"` template, therefore `id` is appended to the `"api/[controller]"` template on the controller.</span></span> <span data-ttu-id="fcc46-303">方法模板为 `"api/[controller]/"{id}""` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-303">The methods template is `"api/[controller]/"{id}""`.</span></span> <span data-ttu-id="fcc46-304">因此，此操作仅匹配对窗体、、等的 GET 请求 `/api/test2/xyz` `/api/test2/123` `/api/test2/{any string}` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-304">Therefore this action only matches GET requests for the form `/api/test2/xyz`,`/api/test2/123`,`/api/test2/{any string}`, etc.</span></span>
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* <span data-ttu-id="fcc46-305">`GetIntProduct`操作包含 `"int/{id:int}")` 模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-305">The `GetIntProduct` action contains the `"int/{id:int}")` template.</span></span> <span data-ttu-id="fcc46-306">`:int`模板的部分将 `id` 路由值限制为可以转换为整数的字符串。</span><span class="sxs-lookup"><span data-stu-id="fcc46-306">The `:int` portion of the template constrains the `id` route values to strings that can be converted to an integer.</span></span> <span data-ttu-id="fcc46-307">针对以下内容的 GET 请求 `/api/test2/int/abc` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-307">A GET request to `/api/test2/int/abc`:</span></span>
  * <span data-ttu-id="fcc46-308">与此操作不匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-308">Doesn't match this action.</span></span>
  * <span data-ttu-id="fcc46-309">返回 " [找不到 404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) " 错误。</span><span class="sxs-lookup"><span data-stu-id="fcc46-309">Returns a [404 Not Found](https://developer.mozilla.org/docs/Web/HTTP/Status/404) error.</span></span>
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* <span data-ttu-id="fcc46-310">`GetInt2Product`操作包含 `{id}` 在模板中，但不会限制为 `id` 可转换为整数的值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-310">The `GetInt2Product` action contains `{id}` in the template, but doesn't constrain `id` to values that can be converted to an integer.</span></span> <span data-ttu-id="fcc46-311">针对以下内容的 GET 请求 `/api/test2/int2/abc` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-311">A GET request to `/api/test2/int2/abc`:</span></span>
  * <span data-ttu-id="fcc46-312">与此路由匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-312">Matches this route.</span></span>
  * <span data-ttu-id="fcc46-313">模型绑定无法转换 `abc` 为整数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-313">Model binding fails to convert `abc` to an integer.</span></span> <span data-ttu-id="fcc46-314">该 `id` 方法的参数是整数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-314">The `id` parameter of the method is integer.</span></span>
  * <span data-ttu-id="fcc46-315">返回 [400 错误请求](https://developer.mozilla.org/docs/Web/HTTP/Status/400) ，因为模型绑定无法转换 `abc` 为整数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-315">Returns a [400 Bad Request](https://developer.mozilla.org/docs/Web/HTTP/Status/400) because model binding failed to convert`abc` to an integer.</span></span>
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

<span data-ttu-id="fcc46-316">特性路由可以使用 <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> 、和等属性 <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-316">Attribute routing can use <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> attributes such as <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>, <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>, and <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>.</span></span> <span data-ttu-id="fcc46-317">所有 [HTTP 谓词](#verb) 特性都接受路由模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-317">All of the [HTTP verb](#verb) attributes accept a route template.</span></span> <span data-ttu-id="fcc46-318">下面的示例演示两个匹配同一路由模板的操作：</span><span class="sxs-lookup"><span data-stu-id="fcc46-318">The following example shows two actions that match the same route template:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

<span data-ttu-id="fcc46-319">使用 URL 路径 `/products3` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-319">Using the URL path `/products3`:</span></span>

* <span data-ttu-id="fcc46-320">`MyProductsController.ListProducts`当[HTTP 谓词](#verb)为时，该操作将运行 `GET` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-320">The `MyProductsController.ListProducts` action runs when the [HTTP verb](#verb) is `GET`.</span></span>
* <span data-ttu-id="fcc46-321">`MyProductsController.CreateProduct`当[HTTP 谓词](#verb)为时，该操作将运行 `POST` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-321">The `MyProductsController.CreateProduct` action runs when the [HTTP verb](#verb) is `POST`.</span></span>

<span data-ttu-id="fcc46-322">构建 REST API 时，很少需要 `[Route(...)]` 在操作方法上使用，因为该操作接受所有 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="fcc46-322">When building a REST API, it's rare that you'll need to use `[Route(...)]` on an action method because the action accepts all HTTP methods.</span></span> <span data-ttu-id="fcc46-323">更好的方法是使用更具体的 [HTTP 谓词特性](#verb) 来精确了解 API 支持的内容。</span><span class="sxs-lookup"><span data-stu-id="fcc46-323">It's better to use the more specific [HTTP verb attribute](#verb) to be precise about what your API supports.</span></span> <span data-ttu-id="fcc46-324">REST API 的客户端需要知道映射到特定逻辑操作的路径和 Http 谓词。</span><span class="sxs-lookup"><span data-stu-id="fcc46-324">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="fcc46-325">REST Api 应使用属性路由将应用功能建模为一组资源，其中的操作由 HTTP 谓词表示。</span><span class="sxs-lookup"><span data-stu-id="fcc46-325">REST APIs should use attribute routing to model the app's functionality as a set of resources where operations are represented by HTTP verbs.</span></span> <span data-ttu-id="fcc46-326">这意味着，多个操作（例如，同一逻辑资源的 GET 和 POST）使用相同的 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-326">This means that many operations, for example, GET and POST on the same logical resource use the same URL.</span></span> <span data-ttu-id="fcc46-327">属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。</span><span class="sxs-lookup"><span data-stu-id="fcc46-327">Attribute routing provides a level of control that's needed to carefully design an API's public endpoint layout.</span></span>

<span data-ttu-id="fcc46-328">由于属性路由适用于特定操作，因此，使参数变成路由模板定义中的必需参数很简单。</span><span class="sxs-lookup"><span data-stu-id="fcc46-328">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="fcc46-329">在下面的示例中， `id` 需要作为 URL 路径的一部分：</span><span class="sxs-lookup"><span data-stu-id="fcc46-329">In the following example, `id` is required as part of the URL path:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="fcc46-330">`Products2ApiController.GetProduct(int)`操作：</span><span class="sxs-lookup"><span data-stu-id="fcc46-330">The `Products2ApiController.GetProduct(int)` action:</span></span>

* <span data-ttu-id="fcc46-331">是用 URL 路径（如）运行的 `/products2/3`</span><span class="sxs-lookup"><span data-stu-id="fcc46-331">Is run with URL path like `/products2/3`</span></span>
* <span data-ttu-id="fcc46-332">不以 URL 路径运行 `/products2` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-332">Isn't run with the URL path `/products2`.</span></span>

<span data-ttu-id="fcc46-333">使用 [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性，操作可以限制支持的请求内容类型。</span><span class="sxs-lookup"><span data-stu-id="fcc46-333">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="fcc46-334">有关详细信息，请参阅 [使用 "使用" 属性定义支持的请求内容类型](xref:web-api/index#consumes)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-334">For more information, see [Define supported request content types with the Consumes attribute](xref:web-api/index#consumes).</span></span>

 <span data-ttu-id="fcc46-335">请参阅[路由](xref:fundamentals/routing)了解路由模板和相关选项的完整说明。</span><span class="sxs-lookup"><span data-stu-id="fcc46-335">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

<span data-ttu-id="fcc46-336">有关的详细信息 `[ApiController]` ，请参阅 [ApiController 属性](xref:web-api/index##apicontroller-attribute)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-336">For more information on `[ApiController]`, see [ApiController attribute](xref:web-api/index##apicontroller-attribute).</span></span>

## <a name="route-name"></a><span data-ttu-id="fcc46-337">路由名称</span><span class="sxs-lookup"><span data-stu-id="fcc46-337">Route name</span></span>

<span data-ttu-id="fcc46-338">以下代码定义  的`Products_List`路由名称：</span><span class="sxs-lookup"><span data-stu-id="fcc46-338">The following code  defines a route name of `Products_List`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

<span data-ttu-id="fcc46-339">可以使用路由名称基于特定路由生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-339">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="fcc46-340">路由名称：</span><span class="sxs-lookup"><span data-stu-id="fcc46-340">Route names:</span></span>

* <span data-ttu-id="fcc46-341">不会影响路由的 URL 匹配行为。</span><span class="sxs-lookup"><span data-stu-id="fcc46-341">Have no impact on the URL matching behavior of routing.</span></span>
* <span data-ttu-id="fcc46-342">仅用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-342">Are only used for URL generation.</span></span>

<span data-ttu-id="fcc46-343">路由名称必须在应用程序范围内唯一。</span><span class="sxs-lookup"><span data-stu-id="fcc46-343">Route names must be unique application-wide.</span></span>

<span data-ttu-id="fcc46-344">对比前面的代码和常规默认路由，该路由将 `id` 参数定义为可选的 (`{id?}`) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-344">Contrast the preceding code with the conventional default route, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="fcc46-345">精确指定 Api 的功能具有多种优点，例如允许 `/products` 和 `/products/5` 调度到不同的操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-345">The ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a><span data-ttu-id="fcc46-346">组合特性路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-346">Combining attribute routes</span></span>

<span data-ttu-id="fcc46-347">若要使属性路由减少重复，可将控制器上的路由属性与各个操作上的路由属性合并。</span><span class="sxs-lookup"><span data-stu-id="fcc46-347">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="fcc46-348">控制器上定义的所有路由模板均作为操作上路由模板的前缀。</span><span class="sxs-lookup"><span data-stu-id="fcc46-348">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="fcc46-349">在控制器上放置路由属性会使控制器中的 **所有** 操作都使用属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-349">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

<span data-ttu-id="fcc46-350">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="fcc46-350">In the preceding example:</span></span>

* <span data-ttu-id="fcc46-351">URL 路径 `/products` 可以匹配 `ProductsApi.ListProducts`</span><span class="sxs-lookup"><span data-stu-id="fcc46-351">The URL path `/products` can match `ProductsApi.ListProducts`</span></span>
* <span data-ttu-id="fcc46-352">URL 路径 `/products/5` 可以匹配 `ProductsApi.GetProduct(int)` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-352">The URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span>

<span data-ttu-id="fcc46-353">这两个操作仅匹配 HTTP， `GET` 因为它们用特性标记 `[HttpGet]` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-353">Both of these actions only match HTTP `GET` because they're marked with the `[HttpGet]` attribute.</span></span>

<span data-ttu-id="fcc46-354">应用于操作的以 `/` 或 `~/` 开头的路由模板不与应用于控制器的路由模板合并。</span><span class="sxs-lookup"><span data-stu-id="fcc46-354">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="fcc46-355">下面的示例匹配一组类似于默认路由的 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="fcc46-355">The following example matches a set of URL paths similar to the default route.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="fcc46-356">下表说明了 `[Route]` 上述代码中的属性：</span><span class="sxs-lookup"><span data-stu-id="fcc46-356">The following table explains the `[Route]` attributes in the preceding code:</span></span>

| <span data-ttu-id="fcc46-357">属性</span><span class="sxs-lookup"><span data-stu-id="fcc46-357">Attribute</span></span>               | <span data-ttu-id="fcc46-358">结合 `[Route("Home")]`</span><span class="sxs-lookup"><span data-stu-id="fcc46-358">Combines with `[Route("Home")]`</span></span> | <span data-ttu-id="fcc46-359">定义路由模板</span><span class="sxs-lookup"><span data-stu-id="fcc46-359">Defines route template</span></span> |
| ----------------- | ------------ | --------- |
| `[Route("")]` | <span data-ttu-id="fcc46-360">是</span><span class="sxs-lookup"><span data-stu-id="fcc46-360">Yes</span></span> | `"Home"` |
| `[Route("Index")]` | <span data-ttu-id="fcc46-361">是</span><span class="sxs-lookup"><span data-stu-id="fcc46-361">Yes</span></span> | `"Home/Index"` |
| `[Route("/")]` | <span data-ttu-id="fcc46-362">**否**</span><span class="sxs-lookup"><span data-stu-id="fcc46-362">**No**</span></span> | `""` |
| `[Route("About")]` | <span data-ttu-id="fcc46-363">是</span><span class="sxs-lookup"><span data-stu-id="fcc46-363">Yes</span></span> | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a><span data-ttu-id="fcc46-364">属性路由顺序</span><span class="sxs-lookup"><span data-stu-id="fcc46-364">Attribute route order</span></span>

<span data-ttu-id="fcc46-365">路由构建树并同时匹配所有终结点：</span><span class="sxs-lookup"><span data-stu-id="fcc46-365">Routing builds a tree and matches all endpoints simultaneously:</span></span>

* <span data-ttu-id="fcc46-366">路由条目的行为方式与置于理想排序中的行为相同。</span><span class="sxs-lookup"><span data-stu-id="fcc46-366">The route entries behave as if placed in an ideal ordering.</span></span>
* <span data-ttu-id="fcc46-367">最特定的路由在更通用的路由之前有机会执行。</span><span class="sxs-lookup"><span data-stu-id="fcc46-367">The most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="fcc46-368">例如，类似于属性路由的属性路由 `blog/search/{topic}` 更为具体 `blog/{*article}` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-368">For example, an attribute route like `blog/search/{topic}` is more specific than an attribute route like `blog/{*article}`.</span></span> <span data-ttu-id="fcc46-369">`blog/search/{topic}`默认情况下，路由具有更高的优先级，因为它更为具体。</span><span class="sxs-lookup"><span data-stu-id="fcc46-369">The `blog/search/{topic}` route has higher priority, by default, because it's more specific.</span></span> <span data-ttu-id="fcc46-370">使用 [传统路由](#cr)，开发人员负责按所需的顺序放置路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-370">Using [conventional routing](#cr), the developer is responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="fcc46-371">属性路由可以使用属性配置订单 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-371">Attribute routes can configure an order using the <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> property.</span></span> <span data-ttu-id="fcc46-372">提供的所有 [路由属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) 都包括 `Order` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-372">All of the framework provided [route attributes](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) include `Order` .</span></span> <span data-ttu-id="fcc46-373">路由按 `Order` 属性的升序进行处理。</span><span class="sxs-lookup"><span data-stu-id="fcc46-373">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="fcc46-374">默认顺序为 `0`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-374">The default order is `0`.</span></span> <span data-ttu-id="fcc46-375">使用 `Order = -1` 不设置顺序的路由之前的运行设置路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-375">Setting a route using `Order = -1` runs before routes that don't set an order.</span></span> <span data-ttu-id="fcc46-376">使用 `Order = 1` 默认路由排序后的运行设置路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-376">Setting a route using `Order = 1` runs after default route ordering.</span></span>

<span data-ttu-id="fcc46-377">**避免** 依赖于 `Order` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-377">**Avoid** depending on `Order`.</span></span> <span data-ttu-id="fcc46-378">如果应用的 URL 空间需要显式顺序值才能正确路由，则很可能会使客户端混淆。</span><span class="sxs-lookup"><span data-stu-id="fcc46-378">If an app's URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="fcc46-379">通常，属性路由选择 URL 匹配的正确路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-379">In general, attribute routing selects the correct route with URL matching.</span></span> <span data-ttu-id="fcc46-380">如果用于 URL 生成的默认顺序不起作用，则使用路由名称作为替代通常比应用属性更简单 `Order` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-380">If the default order used for URL generation isn't working, using a route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="fcc46-381">请考虑以下两个控制器，它们都定义了路由匹配 `/home` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-381">Consider the following two controllers which both define the route matching `/home`:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

<span data-ttu-id="fcc46-382">`/home`使用前面的代码请求将引发异常，如下所示：</span><span class="sxs-lookup"><span data-stu-id="fcc46-382">Requesting `/home` with the preceding code throws an exception similar to the following:</span></span>

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

<span data-ttu-id="fcc46-383">添加 `Order` 到其中一个路由属性可解决歧义：</span><span class="sxs-lookup"><span data-stu-id="fcc46-383">Adding `Order` to one of the route attributes resolves the ambiguity:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

<span data-ttu-id="fcc46-384">在前面的代码中， `/home` 运行 `HomeController.Index` 终结点。</span><span class="sxs-lookup"><span data-stu-id="fcc46-384">With the preceding code, `/home` runs the `HomeController.Index` endpoint.</span></span> <span data-ttu-id="fcc46-385">若要转到 `MyDemoController.MyIndex` ，请请求 `/home/MyIndex` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-385">To get to the `MyDemoController.MyIndex`, request `/home/MyIndex`.</span></span> <span data-ttu-id="fcc46-386">**注意**：</span><span class="sxs-lookup"><span data-stu-id="fcc46-386">**Note**:</span></span>

* <span data-ttu-id="fcc46-387">上面的代码是一个示例或不良路由设计。</span><span class="sxs-lookup"><span data-stu-id="fcc46-387">The preceding code is an example or poor routing design.</span></span> <span data-ttu-id="fcc46-388">它用于说明 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-388">It was used to illustrate the `Order` property.</span></span>
* <span data-ttu-id="fcc46-389">`Order`属性只解析多义性，该模板无法匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-389">The `Order` property only resolves the ambiguity, that template cannot be matched.</span></span> <span data-ttu-id="fcc46-390">最好删除该 `[Route("Home")]` 模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-390">It would be better to remove the `[Route("Home")]` template.</span></span>

<span data-ttu-id="fcc46-391">请参阅[ Razor 页面路由和应用约定：路由](xref:razor-pages/razor-pages-conventions#route-order)顺序获取有关页面的路由顺序的信息 Razor 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-391">See [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order) for information on route order with Razor Pages.</span></span>

<span data-ttu-id="fcc46-392">在某些情况下，将返回具有不明确路由的 HTTP 500 错误。</span><span class="sxs-lookup"><span data-stu-id="fcc46-392">In some cases, an HTTP 500 error is returned with ambiguous routes.</span></span> <span data-ttu-id="fcc46-393">使用 [日志记录](xref:fundamentals/logging/index) 查看导致的终结点 `AmbiguousMatchException` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-393">Use [logging](xref:fundamentals/logging/index) to see which endpoints caused the `AmbiguousMatchException`.</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="fcc46-394">路由模板中的标记替换 [控制器]，[操作]，[区域]</span><span class="sxs-lookup"><span data-stu-id="fcc46-394">Token replacement in route templates [controller], [action], [area]</span></span>

<span data-ttu-id="fcc46-395">为方便起见，特性路由支持 *标记替换* ，方法是在方括号中包含一个标记 (`[` ， `]`) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-395">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="fcc46-396">令牌 `[action]` 、 `[area]` 和 `[controller]` 将替换为自定义路由的操作的 "操作名称"、"区域名称" 和 "控制器名称" 的值：</span><span class="sxs-lookup"><span data-stu-id="fcc46-396">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="fcc46-397">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="fcc46-397">In the preceding code:</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * <span data-ttu-id="fcc46-398">与 `/Products0/List`</span><span class="sxs-lookup"><span data-stu-id="fcc46-398">Matches `/Products0/List`</span></span>

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * <span data-ttu-id="fcc46-399">与 `/Products0/Edit/{id}`</span><span class="sxs-lookup"><span data-stu-id="fcc46-399">Matches `/Products0/Edit/{id}`</span></span>

<span data-ttu-id="fcc46-400">标记替换发生在属性路由生成的最后一步。</span><span class="sxs-lookup"><span data-stu-id="fcc46-400">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="fcc46-401">前面的示例与下面的代码具有相同的行为：</span><span class="sxs-lookup"><span data-stu-id="fcc46-401">The preceding example behaves the same as the following code:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

<span data-ttu-id="fcc46-402">属性路由还可以与继承结合使用。</span><span class="sxs-lookup"><span data-stu-id="fcc46-402">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="fcc46-403">这与标记替换功能功能强大。</span><span class="sxs-lookup"><span data-stu-id="fcc46-403">This is powerful combined with token replacement.</span></span> <span data-ttu-id="fcc46-404">标记替换也适用于属性路由定义的路由名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-404">Token replacement also applies to route names defined by attribute routes.</span></span>
<span data-ttu-id="fcc46-405">`[Route("[controller]/[action]", Name="[controller]_[action]")]`为每个操作生成唯一的路由名称：</span><span class="sxs-lookup"><span data-stu-id="fcc46-405">`[Route("[controller]/[action]", Name="[controller]_[action]")]`generates a unique route name for each action:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

<span data-ttu-id="fcc46-406">若要匹配文本标记替换分隔符 `[` 或 `]`，可通过重复该字符（`[[` 或 `]]`）对其进行转义。</span><span class="sxs-lookup"><span data-stu-id="fcc46-406">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="fcc46-407">使用参数转换程序自定义标记替换</span><span class="sxs-lookup"><span data-stu-id="fcc46-407">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="fcc46-408">使用参数转换程序可以自定义标记替换。</span><span class="sxs-lookup"><span data-stu-id="fcc46-408">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="fcc46-409">参数转换程序实现 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-409">A parameter transformer implements <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> and transforms the value of parameters.</span></span> <span data-ttu-id="fcc46-410">例如，自定义 `SlugifyParameterTransformer` 参数转换器将 `SubscriptionManagement` 路由值更改为 `subscription-management` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-410">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`:</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<span data-ttu-id="fcc46-411"><xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> 是应用程序模型约定，可以：</span><span class="sxs-lookup"><span data-stu-id="fcc46-411">The <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> is an application model convention that:</span></span>

* <span data-ttu-id="fcc46-412">将参数转换程序应用到应用程序中的所有属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-412">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="fcc46-413">在替换属性路由标记值时对其进行自定义。</span><span class="sxs-lookup"><span data-stu-id="fcc46-413">Customizes the attribute route token values as they are replaced.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

<span data-ttu-id="fcc46-414">前面的 `ListAll` 方法匹配 `/subscription-management/list-all` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-414">The preceding `ListAll` method matches `/subscription-management/list-all`.</span></span>

<span data-ttu-id="fcc46-415">`RouteTokenTransformerConvention` 在 `ConfigureServices` 中注册为选项。</span><span class="sxs-lookup"><span data-stu-id="fcc46-415">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

<span data-ttu-id="fcc46-416">有关信息区的定义，请参阅 [网站上的 MDN web 文档](https://developer.mozilla.org/docs/Glossary/Slug) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-416">See [MDN web docs on Slug](https://developer.mozilla.org/docs/Glossary/Slug) for the definition of Slug.</span></span>

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a><span data-ttu-id="fcc46-417">多个属性路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-417">Multiple attribute routes</span></span>

<span data-ttu-id="fcc46-418">属性路由支持定义多个访问同一操作的路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-418">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="fcc46-419">此操作最常用于模拟默认传统路由的行为，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="fcc46-419">The most common usage of this is to mimic the behavior of the default conventional route as shown in the following example:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

<span data-ttu-id="fcc46-420">在控制器上放置多个路由属性意味着每个属性都与操作方法上的每个路由属性结合：</span><span class="sxs-lookup"><span data-stu-id="fcc46-420">Putting multiple route attributes on the controller means that each one combines with each of the route attributes on the action methods:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

<span data-ttu-id="fcc46-421">所有 [HTTP 谓词](#verb) 路由约束都实现 `IActionConstraint` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-421">All the [HTTP verb](#verb) route constraints implement `IActionConstraint`.</span></span>

<span data-ttu-id="fcc46-422">当实现的多个路由属性 <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> 放置在一个操作上时：</span><span class="sxs-lookup"><span data-stu-id="fcc46-422">When multiple route attributes that implement <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> are placed on an action:</span></span>

* <span data-ttu-id="fcc46-423">每个操作约束都与应用于控制器的路由模板结合。</span><span class="sxs-lookup"><span data-stu-id="fcc46-423">Each action constraint combines with the route template applied to the controller.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

<span data-ttu-id="fcc46-424">在操作中使用多个路由可能看起来非常有用，更好的做法是让应用程序的 URL 空间基本且定义完善。</span><span class="sxs-lookup"><span data-stu-id="fcc46-424">Using multiple routes on actions might seem useful and powerful, it's better to keep your app's URL space basic and well defined.</span></span> <span data-ttu-id="fcc46-425">**仅** 在需要时对操作使用多个路由，例如支持现有客户端。</span><span class="sxs-lookup"><span data-stu-id="fcc46-425">Use multiple routes on actions **only** where needed, for example, to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="fcc46-426">指定属性路由的可选参数、默认值和约束</span><span class="sxs-lookup"><span data-stu-id="fcc46-426">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="fcc46-427">属性路由支持使用与传统路由相同的内联语法，来指定可选参数、默认值和约束。</span><span class="sxs-lookup"><span data-stu-id="fcc46-427">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

<span data-ttu-id="fcc46-428">在上面的代码中， `[HttpPost("product14/{id:int}")]` 应用路由约束。</span><span class="sxs-lookup"><span data-stu-id="fcc46-428">In the preceding code, `[HttpPost("product14/{id:int}")]` applies a route constraint.</span></span> <span data-ttu-id="fcc46-429">此 `Products14Controller.ShowProduct` 操作仅由类似的 URL 路径进行匹配 `/product14/3` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-429">The `Products14Controller.ShowProduct` action is matched only by URL paths like `/product14/3`.</span></span> <span data-ttu-id="fcc46-430">路由模板部分 `{id:int}` 仅限制整数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-430">The route template portion `{id:int}` constrains that segment to only integers.</span></span>

<span data-ttu-id="fcc46-431">有关路由模板语法的详细说明，请参阅[路由模板参考](xref:fundamentals/routing#route-template-reference)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-431">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="fcc46-432">使用 IRouteTemplateProvider 的自定义路由属性</span><span class="sxs-lookup"><span data-stu-id="fcc46-432">Custom route attributes using IRouteTemplateProvider</span></span>

<span data-ttu-id="fcc46-433">所有 [路由特性](#rt) 都实现 <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-433">All of the [route attributes](#rt) implement <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>.</span></span> <span data-ttu-id="fcc46-434">ASP.NET Core 运行时：</span><span class="sxs-lookup"><span data-stu-id="fcc46-434">The ASP.NET Core runtime:</span></span>

* <span data-ttu-id="fcc46-435">应用启动时，在控制器类和操作方法上查找属性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-435">Looks for attributes on controller classes and action methods when the app starts.</span></span>
* <span data-ttu-id="fcc46-436">使用实现 `IRouteTemplateProvider` 以生成初始路由集的特性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-436">Uses the attributes that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="fcc46-437">实现 `IRouteTemplateProvider` 以定义自定义路由属性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-437">Implement `IRouteTemplateProvider` to define custom route attributes.</span></span> <span data-ttu-id="fcc46-438">每个 `IRouteTemplateProvider` 都允许定义一个包含自定义路由模板、顺序和名称的路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-438">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

<span data-ttu-id="fcc46-439">上述 `Get` 方法返回 `Order = 2, Template = api/MyTestApi` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-439">The preceding `Get` method returns `Order = 2, Template = api/MyTestApi`.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a><span data-ttu-id="fcc46-440">使用应用程序模型自定义属性路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-440">Use application model to customize attribute routes</span></span>

<span data-ttu-id="fcc46-441">应用程序模型：</span><span class="sxs-lookup"><span data-stu-id="fcc46-441">The application model:</span></span>

* <span data-ttu-id="fcc46-442">是在启动时创建的对象模型。</span><span class="sxs-lookup"><span data-stu-id="fcc46-442">Is an object model created at startup.</span></span>
* <span data-ttu-id="fcc46-443">包含 ASP.NET Core 用来在应用程序中路由和执行操作的所有元数据。</span><span class="sxs-lookup"><span data-stu-id="fcc46-443">Contains all of the metadata used by ASP.NET Core to route and execute the actions in an app.</span></span>

<span data-ttu-id="fcc46-444">应用程序模型包括从路由属性收集的所有数据。</span><span class="sxs-lookup"><span data-stu-id="fcc46-444">The application model includes all of the data gathered from route attributes.</span></span> <span data-ttu-id="fcc46-445">路由属性中的数据由 `IRouteTemplateProvider` 实现提供。</span><span class="sxs-lookup"><span data-stu-id="fcc46-445">The data from route attributes is provided by the `IRouteTemplateProvider` implementation.</span></span> <span data-ttu-id="fcc46-446">规范</span><span class="sxs-lookup"><span data-stu-id="fcc46-446">Conventions:</span></span>

* <span data-ttu-id="fcc46-447">可以编写来修改应用程序模型，以自定义路由的行为方式。</span><span class="sxs-lookup"><span data-stu-id="fcc46-447">Can be written to modify the application model to customize how routing behaves.</span></span>
* <span data-ttu-id="fcc46-448">在应用程序启动时读取。</span><span class="sxs-lookup"><span data-stu-id="fcc46-448">Are read at app startup.</span></span>

<span data-ttu-id="fcc46-449">本部分显示了使用应用程序模型自定义路由的基本示例。</span><span class="sxs-lookup"><span data-stu-id="fcc46-449">This section shows a basic example of customizing routing using application model.</span></span> <span data-ttu-id="fcc46-450">下面的代码使路由大致与项目的文件夹结构对齐。</span><span class="sxs-lookup"><span data-stu-id="fcc46-450">The following code makes routes roughly line up with the folder structure of the project.</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

<span data-ttu-id="fcc46-451">下面的代码阻止将 `namespace` 约定应用到已路由属性的控制器：</span><span class="sxs-lookup"><span data-stu-id="fcc46-451">The following code prevents the `namespace` convention from being applied to controllers that are attribute routed:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

<span data-ttu-id="fcc46-452">例如，以下控制器不使用 `NamespaceRoutingConvention` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-452">For example, the following controller doesn't use `NamespaceRoutingConvention`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

<span data-ttu-id="fcc46-453">`NamespaceRoutingConvention.Apply` 方法：</span><span class="sxs-lookup"><span data-stu-id="fcc46-453">The `NamespaceRoutingConvention.Apply` method:</span></span>

* <span data-ttu-id="fcc46-454">如果控制器为属性路由，则不执行任何操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-454">Does nothing if the controller is attribute routed.</span></span>
* <span data-ttu-id="fcc46-455">基于删除基的控制器模板 `namespace` `namespace` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-455">Sets the controllers template based on the `namespace`, with the base `namespace` removed.</span></span>

<span data-ttu-id="fcc46-456">`NamespaceRoutingConvention`可应用于 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-456">The `NamespaceRoutingConvention` can be applied in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

<span data-ttu-id="fcc46-457">例如，请考虑以下控制器：</span><span class="sxs-lookup"><span data-stu-id="fcc46-457">For example, consider the following controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

<span data-ttu-id="fcc46-458">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="fcc46-458">In the preceding code:</span></span>

* <span data-ttu-id="fcc46-459">基数 `namespace` 是 `My.Application` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-459">The base `namespace` is `My.Application`.</span></span>
* <span data-ttu-id="fcc46-460">前面的控制器的全名为 `My.Application.Admin.Controllers.UsersController` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-460">The full name of the preceding controller is `My.Application.Admin.Controllers.UsersController`.</span></span>
* <span data-ttu-id="fcc46-461">将 `NamespaceRoutingConvention` 控制器模板设置为 `Admin/Controllers/Users/[action]/{id?` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-461">The `NamespaceRoutingConvention` sets the controllers template to `Admin/Controllers/Users/[action]/{id?`.</span></span>

<span data-ttu-id="fcc46-462">`NamespaceRoutingConvention`还可以应用为控制器上的属性：</span><span class="sxs-lookup"><span data-stu-id="fcc46-462">The `NamespaceRoutingConvention` can also be applied as an attribute on a controller:</span></span>

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="fcc46-463">混合路由：属性路由与传统路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-463">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="fcc46-464">ASP.NET Core 应用可以混合使用传统路由和属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-464">ASP.NET Core apps can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="fcc46-465">通常将传统路由用于为浏览器处理 HTML 页面的控制器，将属性路由用于处理 REST API 的控制器。</span><span class="sxs-lookup"><span data-stu-id="fcc46-465">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="fcc46-466">操作既支持传统路由，也支持属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-466">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="fcc46-467">通过在控制器或操作上放置路由可实现属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-467">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="fcc46-468">不能通过传统路由访问定义属性路由的操作，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="fcc46-468">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="fcc46-469">控制器上的 **任何** 路由属性都使控制器属性中的 **所有操作都** 已路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-469">**Any** route attribute on the controller makes **all** actions in the controller attribute routed.</span></span>

<span data-ttu-id="fcc46-470">属性路由和传统路由使用相同的路由引擎。</span><span class="sxs-lookup"><span data-stu-id="fcc46-470">Attribute routing and conventional routing use the same routing engine.</span></span>

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a><span data-ttu-id="fcc46-471">URL 生成和环境值</span><span class="sxs-lookup"><span data-stu-id="fcc46-471">URL Generation and ambient values</span></span>

<span data-ttu-id="fcc46-472">应用可以使用路由 URL 生成功能来生成指向操作的 URL 链接。</span><span class="sxs-lookup"><span data-stu-id="fcc46-472">Apps can use routing URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="fcc46-473">生成 Url 可消除硬编码 Url，使代码更可靠和更易于维护。</span><span class="sxs-lookup"><span data-stu-id="fcc46-473">Generating URLs eliminates hardcoding URLs, making code more robust and maintainable.</span></span> <span data-ttu-id="fcc46-474">本部分重点介绍 MVC 提供的 URL 生成功能，仅介绍 URL 生成的工作原理的基础知识。</span><span class="sxs-lookup"><span data-stu-id="fcc46-474">This section focuses on the URL generation features provided by MVC and only cover basics of how URL generation works.</span></span> <span data-ttu-id="fcc46-475">有关 URL 生成的详细说明，请参阅[路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-475">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="fcc46-476"><xref:Microsoft.AspNetCore.Mvc.IUrlHelper>接口是 MVC 之间的基础结构和用于生成 URL 的路由的基础元素。</span><span class="sxs-lookup"><span data-stu-id="fcc46-476">The <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> interface is the underlying element of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="fcc46-477">的实例 `IUrlHelper` 可通过 " `Url` 控制器"、"视图" 和 "视图" 组件中的属性使用。</span><span class="sxs-lookup"><span data-stu-id="fcc46-477">An instance of `IUrlHelper` is available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="fcc46-478">在下面的示例中， `IUrlHelper` 通过属性使用接口 `Controller.Url` 生成其他操作的 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-478">In the following example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="fcc46-479">如果应用使用默认传统路由，则变量的值 `url` 为 URL 路径字符串 `/UrlGeneration/Destination` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-479">If the app is using the default conventional route, the value of the `url` variable is the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="fcc46-480">此 URL 路径由路由通过组合创建：</span><span class="sxs-lookup"><span data-stu-id="fcc46-480">This URL path is created by routing by combining:</span></span>

* <span data-ttu-id="fcc46-481">当前请求中的路由值，称为 " **环境值**"。</span><span class="sxs-lookup"><span data-stu-id="fcc46-481">The route values from the current request, which are called **ambient values**.</span></span>
* <span data-ttu-id="fcc46-482">传递到的值 `Url.Action` ，并将这些值替换为路由模板：</span><span class="sxs-lookup"><span data-stu-id="fcc46-482">The values passed to `Url.Action` and substituting those values into the route template:</span></span>

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="fcc46-483">路由模板中的每个路由参数都会通过将名称与这些值和环境值匹配，来替换掉原来的值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-483">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="fcc46-484">不具有值的路由参数可以：</span><span class="sxs-lookup"><span data-stu-id="fcc46-484">A route parameter that doesn't have a value can:</span></span>

* <span data-ttu-id="fcc46-485">如果有一个默认值，则使用默认值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-485">Use a default value if it has one.</span></span>
* <span data-ttu-id="fcc46-486">如果是可选的，则跳过它。</span><span class="sxs-lookup"><span data-stu-id="fcc46-486">Be skipped if it's optional.</span></span> <span data-ttu-id="fcc46-487">例如， `id` 路由模板中的 `{controller}/{action}/{id?}` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-487">For example, the `id` from the  route template `{controller}/{action}/{id?}`.</span></span>

<span data-ttu-id="fcc46-488">如果任何所需的路由参数没有对应的值，URL 生成将失败。</span><span class="sxs-lookup"><span data-stu-id="fcc46-488">URL generation fails if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="fcc46-489">如果某个路由的 URL 生成失败，则尝试下一个路由，直到尝试所有路由或找到匹配项为止。</span><span class="sxs-lookup"><span data-stu-id="fcc46-489">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="fcc46-490">前面的示例 `Url.Action` 假定 [传统路由](#cr)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-490">The preceding example of `Url.Action` assumes [conventional routing](#cr).</span></span> <span data-ttu-id="fcc46-491">URL 生成的工作方式类似于 [属性路由](#ar)，但概念不同。</span><span class="sxs-lookup"><span data-stu-id="fcc46-491">URL generation works similarly with [attribute routing](#ar), though the concepts are different.</span></span> <span data-ttu-id="fcc46-492">对于传统路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-492">With conventional routing:</span></span>

* <span data-ttu-id="fcc46-493">路由值用于扩展模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-493">The route values are used to expand a template.</span></span>
* <span data-ttu-id="fcc46-494">和的路由值 `controller` `action` 通常出现在该模板中。</span><span class="sxs-lookup"><span data-stu-id="fcc46-494">The route values for `controller` and `action` usually appear in that template.</span></span> <span data-ttu-id="fcc46-495">这是因为由路由匹配的 Url 遵循约定。</span><span class="sxs-lookup"><span data-stu-id="fcc46-495">This works because the URLs matched by routing adhere to a convention.</span></span>

<span data-ttu-id="fcc46-496">下面的示例使用属性路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-496">The following example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

<span data-ttu-id="fcc46-497">`Source`上述代码中的操作生成 `custom/url/to/destination` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-497">The `Source` action in the preceding code generates `custom/url/to/destination`.</span></span>

<span data-ttu-id="fcc46-498"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> 已添加到 ASP.NET Core 3.0 中作为的替代项 `IUrlHelper` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-498"><xref:Microsoft.AspNetCore.Routing.LinkGenerator> was added in ASP.NET Core 3.0 as an alternative to `IUrlHelper`.</span></span> <span data-ttu-id="fcc46-499">`LinkGenerator` 提供类似但更灵活的功能。</span><span class="sxs-lookup"><span data-stu-id="fcc46-499">`LinkGenerator` offers similar but more flexible functionality.</span></span> <span data-ttu-id="fcc46-500">上的每个方法 `IUrlHelper` 也具有相应的一系列方法 `LinkGenerator` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-500">Each method on `IUrlHelper` has a corresponding family of methods on `LinkGenerator` as well.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="fcc46-501">根据操作名称生成 URL</span><span class="sxs-lookup"><span data-stu-id="fcc46-501">Generating URLs by action name</span></span>

<span data-ttu-id="fcc46-502">[LinkGenerator、GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)和所有相关重载都旨在通过指定控制器名称和操作名称来生成目标终结[点。](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)</span><span class="sxs-lookup"><span data-stu-id="fcc46-502">[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), [LinkGenerator.GetPathByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*), and all related overloads all are designed to generate the target endpoint by specifying a controller name and action name.</span></span>

<span data-ttu-id="fcc46-503">使用时 `Url.Action` ，和的当前路由值 `controller` `action` 由运行时提供：</span><span class="sxs-lookup"><span data-stu-id="fcc46-503">When using `Url.Action`, the current route values for `controller` and `action` are provided by the runtime:</span></span>

* <span data-ttu-id="fcc46-504">和的值 `controller` `action` 都属于 [环境值](#ambient) 和值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-504">The value of `controller` and `action` are part of both [ambient values](#ambient) and values.</span></span> <span data-ttu-id="fcc46-505">方法 `Url.Action` 始终使用和的当前值， `action` `controller` 并生成路由到当前操作的 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="fcc46-505">The method `Url.Action` always uses the current values of `action` and `controller` and generates a URL path that routes to the current action.</span></span>

<span data-ttu-id="fcc46-506">路由尝试使用环境值中的值来填充生成 URL 时未提供的信息。</span><span class="sxs-lookup"><span data-stu-id="fcc46-506">Routing attempts to use the values in ambient values to fill in information that wasn't provided when generating a URL.</span></span> <span data-ttu-id="fcc46-507">请考虑类似于 `{a}/{b}/{c}/{d}` 环境值的路由 `{ a = Alice, b = Bob, c = Carol, d = David }` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-507">Consider a route like `{a}/{b}/{c}/{d}` with ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`:</span></span>

* <span data-ttu-id="fcc46-508">路由具有足够的信息来生成 URL，无需任何其他值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-508">Routing has enough information to generate a URL without any additional values.</span></span>
* <span data-ttu-id="fcc46-509">路由具有足够的信息，因为所有路由参数都具有值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-509">Routing has enough information because all route parameters have a value.</span></span>

<span data-ttu-id="fcc46-510">如果添加了值 `{ d = Donovan }` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-510">If the value `{ d = Donovan }` is added:</span></span>

* <span data-ttu-id="fcc46-511">值 `{ d = David }` 被忽略。</span><span class="sxs-lookup"><span data-stu-id="fcc46-511">The value `{ d = David }` is ignored.</span></span>
* <span data-ttu-id="fcc46-512">生成的 URL 路径为 `Alice/Bob/Carol/Donovan` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-512">The generated URL path is `Alice/Bob/Carol/Donovan`.</span></span>

<span data-ttu-id="fcc46-513">**警告**： URL 路径是分层的。</span><span class="sxs-lookup"><span data-stu-id="fcc46-513">**Warning**: URL paths are hierarchical.</span></span> <span data-ttu-id="fcc46-514">在前面的示例中，如果添加了值 `{ c = Cheryl }` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-514">In the preceding example, if the value `{ c = Cheryl }` is added:</span></span>

* <span data-ttu-id="fcc46-515">这两个值 `{ c = Carol, d = David }` 都将被忽略。</span><span class="sxs-lookup"><span data-stu-id="fcc46-515">Both of the values `{ c = Carol, d = David }` are ignored.</span></span>
* <span data-ttu-id="fcc46-516">不再存在的值 `d` ，URL 生成将失败。</span><span class="sxs-lookup"><span data-stu-id="fcc46-516">There is no longer a value for `d` and URL generation fails.</span></span>
* <span data-ttu-id="fcc46-517">`c` `d` 若要生成 URL，必须指定和的所需值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-517">The desired values of `c` and `d` must be specified to generate a URL.</span></span>  

<span data-ttu-id="fcc46-518">你可能希望在默认路由中遇到此问题 `{controller}/{action}/{id?}` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-518">You might expect to hit this problem with the default route `{controller}/{action}/{id?}`.</span></span> <span data-ttu-id="fcc46-519">此问题在实践中很罕见，因为 `Url.Action` 始终显式指定 `controller` 和 `action` 值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-519">This problem is rare in practice because `Url.Action` always explicitly specifies a `controller` and `action` value.</span></span>

<span data-ttu-id="fcc46-520">多个 [Url 重载。操作](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 采用路由值对象为除和以外的路由参数提供值 `controller` `action` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-520">Several overloads of [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) take a route values object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="fcc46-521">路由值对象经常与一起使用 `id` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-521">The route values object is frequently used with `id`.</span></span> <span data-ttu-id="fcc46-522">例如 `Url.Action("Buy", "Products", new { id = 17 })`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-522">For example, `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="fcc46-523">路由值对象：</span><span class="sxs-lookup"><span data-stu-id="fcc46-523">The route values object:</span></span>

* <span data-ttu-id="fcc46-524">按约定通常是匿名类型的对象。</span><span class="sxs-lookup"><span data-stu-id="fcc46-524">By convention is usually an object of anonymous type.</span></span>
* <span data-ttu-id="fcc46-525">可以是 `IDictionary<>` 或 [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-525">Can be an `IDictionary<>` or a [POCO](https://wikipedia.org/wiki/Plain_old_CLR_object)).</span></span>

<span data-ttu-id="fcc46-526">任何与路由参数不匹配的附加路由值都放在查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="fcc46-526">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="fcc46-527">前面的代码生成 `/Products/Buy/17?color=red` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-527">The preceding code generates `/Products/Buy/17?color=red`.</span></span>

<span data-ttu-id="fcc46-528">下面的代码生成一个绝对 URL：</span><span class="sxs-lookup"><span data-stu-id="fcc46-528">The following code generates an absolute URL:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

<span data-ttu-id="fcc46-529">若要创建绝对 URL，请使用以下项之一：</span><span class="sxs-lookup"><span data-stu-id="fcc46-529">To create an absolute URL, use one of the following:</span></span>

* <span data-ttu-id="fcc46-530">接受的重载 `protocol` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-530">An overload that accepts a `protocol`.</span></span> <span data-ttu-id="fcc46-531">例如，前面的代码。</span><span class="sxs-lookup"><span data-stu-id="fcc46-531">For example, the preceding code.</span></span>
* <span data-ttu-id="fcc46-532">默认情况下， [LinkGenerator](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)生成绝对 uri。</span><span class="sxs-lookup"><span data-stu-id="fcc46-532">[LinkGenerator.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), which generates absolute URIs by default.</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a><span data-ttu-id="fcc46-533">按路由生成 Url</span><span class="sxs-lookup"><span data-stu-id="fcc46-533">Generate URLs by route</span></span>

<span data-ttu-id="fcc46-534">前面的代码演示了如何通过传入控制器和操作名称来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-534">The preceding code demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="fcc46-535">`IUrlHelper` 还提供了 [RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) 系列方法。</span><span class="sxs-lookup"><span data-stu-id="fcc46-535">`IUrlHelper` also provides the [Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) family of methods.</span></span> <span data-ttu-id="fcc46-536">这些方法类似于 [Url. 操作](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)，但它们不会将和的当前值 `action` 复制 `controller` 到路由值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-536">These methods are similar to [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="fcc46-537">最常见的用法是 `Url.RouteUrl` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-537">The most common usage of `Url.RouteUrl`:</span></span>

* <span data-ttu-id="fcc46-538">指定用于生成 URL 的路由名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-538">Specifies a route name to generate the URL.</span></span>
* <span data-ttu-id="fcc46-539">通常不指定控制器或操作名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-539">Generally doesn't specify a controller or action name.</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

<span data-ttu-id="fcc46-540">下面的 Razor 文件生成一个到的 HTML 链接 `Destination_Route` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-540">The following Razor file generates an HTML link to the `Destination_Route`:</span></span>

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a><span data-ttu-id="fcc46-541">在 HTML 和中生成 Url Razor</span><span class="sxs-lookup"><span data-stu-id="fcc46-541">Generate URLs in HTML and Razor</span></span>

<span data-ttu-id="fcc46-542"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> 提供 <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> 方法 [Html.beginform](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) 和 [html.actionlink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) 分别生成 `<form>` 和元素的方法 `<a>` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-542"><xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> provides the <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> methods [Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) and [Html.ActionLink](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="fcc46-543">这些方法使用 [Url 操作](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) 方法来生成 url，并接受类似参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-543">These methods use the [Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="fcc46-544">`HtmlHelper` 的配套 `Url.RouteUrl` 为 `Html.BeginRouteForm` 和 `Html.RouteLink`，两者具有相似的功能。</span><span class="sxs-lookup"><span data-stu-id="fcc46-544">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="fcc46-545">TagHelper 通过 `form` TagHelper 和 `<a>` TagHelper 生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-545">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="fcc46-546">两者均通过 `IUrlHelper` 来实现。</span><span class="sxs-lookup"><span data-stu-id="fcc46-546">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="fcc46-547">有关详细信息，请参阅 [窗体中的标记帮助](xref:mvc/views/working-with-forms) 程序。</span><span class="sxs-lookup"><span data-stu-id="fcc46-547">See [Tag Helpers in forms](xref:mvc/views/working-with-forms) for more information.</span></span>

<span data-ttu-id="fcc46-548">在视图内，可通过 `Url` 属性将 `IUrlHelper` 用于前文未涵盖的任何临时 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="fcc46-548">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a><span data-ttu-id="fcc46-549">操作结果中的 URL 生成</span><span class="sxs-lookup"><span data-stu-id="fcc46-549">URL generation in Action Results</span></span>

<span data-ttu-id="fcc46-550">前面的示例演示了如何 `IUrlHelper` 在控制器中使用。</span><span class="sxs-lookup"><span data-stu-id="fcc46-550">The preceding examples showed using `IUrlHelper` in a controller.</span></span> <span data-ttu-id="fcc46-551">控制器中最常见的用法是将 URL 生成为操作结果的一部分。</span><span class="sxs-lookup"><span data-stu-id="fcc46-551">The most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="fcc46-552"><xref:Microsoft.AspNetCore.Mvc.ControllerBase> 和 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类为操作结果提供简便的方法来引用另一项操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-552">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase> and <xref:Microsoft.AspNetCore.Mvc.Controller> base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="fcc46-553">一种典型用法是在接受用户输入后重定向：</span><span class="sxs-lookup"><span data-stu-id="fcc46-553">One typical usage is to redirect after accepting user input:</span></span>

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

<span data-ttu-id="fcc46-554">操作将生成工厂方法（如 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> 和），并 <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> 遵循中的方法 `IUrlHelper` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-554">The action results factory methods such as <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="fcc46-555">专用传统路由的特殊情况</span><span class="sxs-lookup"><span data-stu-id="fcc46-555">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="fcc46-556">[传统路由](#cr) 可以使用一种特殊的路由定义，称为 [专用的传统路由](#dcr)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-556">[Conventional routing](#cr) can use a special kind of route definition called a [dedicated conventional route](#dcr).</span></span> <span data-ttu-id="fcc46-557">在以下示例中，名为的路由 `blog` 是一个专用的传统路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-557">In the following example, the route named `blog` is a dedicated conventional route:</span></span>

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<span data-ttu-id="fcc46-558">使用前面的路由定义， `Url.Action("Index", "Home")` 使用路由生成 URL 路径 `/` `default` ，但为什么要这样做呢？</span><span class="sxs-lookup"><span data-stu-id="fcc46-558">Using the preceding route definitions, `Url.Action("Index", "Home")` generates the URL path `/` using the `default` route, but why?</span></span> <span data-ttu-id="fcc46-559">用户可能认为使用 `blog`，路由值 `{ controller = Home, action = Index }` 就足以生成 URL，且结果为 `/blog?action=Index&controller=Home`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-559">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="fcc46-560">[专用传统路由](#dcr) 依赖于默认值的特殊行为，这些默认值没有相应的路由参数可防止 [路由过于被](xref:fundamentals/routing#greedy) URL 生成。</span><span class="sxs-lookup"><span data-stu-id="fcc46-560">[Dedicated conventional routes](#dcr) rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being too [greedy](xref:fundamentals/routing#greedy) with URL generation.</span></span> <span data-ttu-id="fcc46-561">在此例中，默认值是为 `{ controller = Blog, action = Article }`，`controller` 和 `action` 均未显示为路由参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-561">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="fcc46-562">当路由执行 URL 生成时，提供的值必须与默认值匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-562">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="fcc46-563">使用的 URL 生成 `blog` 失败，因为这些值 `{ controller = Home, action = Index }` 不匹配 `{ controller = Blog, action = Article }` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-563">URL generation using `blog` fails because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="fcc46-564">然后，路由回退，尝试使用 `default`，并最终成功。</span><span class="sxs-lookup"><span data-stu-id="fcc46-564">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="fcc46-565">Areas</span><span class="sxs-lookup"><span data-stu-id="fcc46-565">Areas</span></span>

<span data-ttu-id="fcc46-566">[区域](xref:mvc/controllers/areas) 是一项 MVC 功能，用于将相关功能作为一个单独的组组织到一个组中：</span><span class="sxs-lookup"><span data-stu-id="fcc46-566">[Areas](xref:mvc/controllers/areas) are an MVC feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="fcc46-567">控制器操作的路由命名空间。</span><span class="sxs-lookup"><span data-stu-id="fcc46-567">Routing namespace for controller actions.</span></span>
* <span data-ttu-id="fcc46-568">视图的文件夹结构。</span><span class="sxs-lookup"><span data-stu-id="fcc46-568">Folder structure for views.</span></span>

<span data-ttu-id="fcc46-569">通过使用区域，应用可以有多个具有相同名称的控制器，只要它们具有不同的区域即可。</span><span class="sxs-lookup"><span data-stu-id="fcc46-569">Using areas allows an app to have multiple controllers with the same name, as long as they have different areas.</span></span> <span data-ttu-id="fcc46-570">通过向 `controller` 和 `action` 添加另一个路由参数 `area`，可使用区域为路由创建层次结构。</span><span class="sxs-lookup"><span data-stu-id="fcc46-570">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="fcc46-571">本部分讨论路由如何与区域交互。</span><span class="sxs-lookup"><span data-stu-id="fcc46-571">This section discusses how routing interacts with areas.</span></span> <span data-ttu-id="fcc46-572">有关如何将区域与视图结合使用的详细信息，请参阅 [区域](xref:mvc/controllers/areas) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-572">See [Areas](xref:mvc/controllers/areas) for details about how areas are used with views.</span></span>

<span data-ttu-id="fcc46-573">下面的示例将 MVC 配置为使用默认传统路由，并为 `area` 命名的 `area` 指定路由 `Blog` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-573">The following example configures MVC to use the default conventional route and an `area` route for an `area` named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="fcc46-574">在前面的代码中， <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> 将调用来创建 `"blog_route"` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-574">In the preceding code, <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> is called to create the `"blog_route"`.</span></span> <span data-ttu-id="fcc46-575">第二个参数 `"Blog"` 为区域名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-575">The second parameter, `"Blog"`, is the area name.</span></span>

<span data-ttu-id="fcc46-576">当匹配 URL 路径（如 `/Manage/Users/AddUser` ）时， `"blog_route"` 路由将生成路由值 `{ area = Blog, controller = Users, action = AddUser }` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-576">When matching a URL path like `/Manage/Users/AddUser`, the `"blog_route"` route generates the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="fcc46-577">`area`路由值由的默认值生成 `area` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-577">The `area` route value is produced by a default value for `area`.</span></span> <span data-ttu-id="fcc46-578">创建的路由 `MapAreaControllerRoute` 等效于以下内容：</span><span class="sxs-lookup"><span data-stu-id="fcc46-578">The route created by `MapAreaControllerRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

<span data-ttu-id="fcc46-579">`MapAreaControllerRoute` 通过为使用所提供的区域名称（本例中为 `Blog`）的 `area` 提供默认值和约束，来创建路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-579">`MapAreaControllerRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="fcc46-580">默认值确保路由始终生成 `{ area = Blog, ... }`，约束要求在生成 URL 时使用值 `{ area = Blog, ... }`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-580">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

<span data-ttu-id="fcc46-581">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="fcc46-581">Conventional routing is order-dependent.</span></span> <span data-ttu-id="fcc46-582">通常情况下，应将具有区域的路由置于更早的位置，因为它们比没有区域的路由更具体。</span><span class="sxs-lookup"><span data-stu-id="fcc46-582">In general, routes with areas should be placed earlier as they're more specific than routes without an area.</span></span>

<span data-ttu-id="fcc46-583">使用前面的示例，路由值 `{ area = Blog, controller = Users, action = AddUser }` 与以下操作匹配：</span><span class="sxs-lookup"><span data-stu-id="fcc46-583">Using the preceding example, the route values `{ area = Blog, controller = Users, action = AddUser }` match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="fcc46-584">[[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)特性用于将控制器表示为区域的一部分。</span><span class="sxs-lookup"><span data-stu-id="fcc46-584">The [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute is what denotes a controller as part of an area.</span></span> <span data-ttu-id="fcc46-585">此控制器在区域中 `Blog` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-585">This controller is in the `Blog` area.</span></span> <span data-ttu-id="fcc46-586">没有属性的控制器 `[Area]` 不是任何区域的成员，并且在 `area` 路由值由路由提供时不匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-586">Controllers without an `[Area]` attribute are not members of any area, and do **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="fcc46-587">在下面的示例中，只有所列出的第一个控制器才能与路由值 `{ area = Blog, controller = Users, action = AddUser }` 匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-587">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

<span data-ttu-id="fcc46-588">此处显示每个控制器的命名空间，以获取完整性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-588">The namespace of each controller is shown here for completeness.</span></span> <span data-ttu-id="fcc46-589">如果前面的控制器使用相同的命名空间，则会生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="fcc46-589">If the preceding controllers used the same namespace, a compiler error would be generated.</span></span> <span data-ttu-id="fcc46-590">类命名空间对 MVC 的路由没有影响。</span><span class="sxs-lookup"><span data-stu-id="fcc46-590">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="fcc46-591">前两个控制器是区域成员，仅在 `area` 路由值提供其各自的区域名称时匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-591">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="fcc46-592">第三个控制器不是任何区域的成员，只能在路由没有为 `area` 提供任何值时匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-592">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

<a name="aa"></a>

<span data-ttu-id="fcc46-593">就 *不匹配任何值* 而言，缺少 `area` 值相当于 `area` 的值为 NULL 或空字符串。</span><span class="sxs-lookup"><span data-stu-id="fcc46-593">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="fcc46-594">在区域内执行操作时，的路由值 `area` 可用作路由用于生成 URL 的 [环境值](#ambient) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-594">When executing an action inside an area, the route value for `area` is available as an [ambient value](#ambient) for routing to use for URL generation.</span></span> <span data-ttu-id="fcc46-595">这意味着默认情况下，区域在 URL 生成中具有 *粘性*，如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="fcc46-595">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<span data-ttu-id="fcc46-596">下面的代码生成一个 URL，用于 `/Zebra/Users/AddUser` ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-596">The following code generates a URL to `/Zebra/Users/AddUser`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a><span data-ttu-id="fcc46-597">操作定义</span><span class="sxs-lookup"><span data-stu-id="fcc46-597">Action definition</span></span>

<span data-ttu-id="fcc46-598">控制器上的公共方法（具有 [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) 特性的方法除外）是操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-598">Public methods on a controller, except those with the [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) attribute, are actions.</span></span>

## <a name="sample-code"></a><span data-ttu-id="fcc46-599">示例代码</span><span class="sxs-lookup"><span data-stu-id="fcc46-599">Sample code</span></span>

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* <span data-ttu-id="fcc46-600">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/routing/samples/3.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="fcc46-600">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/mvc/controllers/routing/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fcc46-601">ASP.NET Core MVC 使用路由[中间件](xref:fundamentals/middleware/index)来匹配传入请求的 URL 并将它们映射到操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-601">ASP.NET Core MVC uses the Routing [middleware](xref:fundamentals/middleware/index) to match the URLs of incoming requests and map them to actions.</span></span> <span data-ttu-id="fcc46-602">路由在启动代码或属性中定义。</span><span class="sxs-lookup"><span data-stu-id="fcc46-602">Routes are defined in startup code or attributes.</span></span> <span data-ttu-id="fcc46-603">路由描述应如何将 URL 路径与操作相匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-603">Routes describe how URL paths should be matched to actions.</span></span> <span data-ttu-id="fcc46-604">它还用于在响应中生成送出的 URL（用于链接）。</span><span class="sxs-lookup"><span data-stu-id="fcc46-604">Routes are also used to generate URLs (for links) sent out in responses.</span></span>

<span data-ttu-id="fcc46-605">操作既支持传统路由，也支持属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-605">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="fcc46-606">通过在控制器或操作上放置路由可实现属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-606">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="fcc46-607">有关详细信息，请参阅[混合路由](#routing-mixed-ref-label)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-607">See [Mixed routing](#routing-mixed-ref-label) for more information.</span></span>

<span data-ttu-id="fcc46-608">本文档将介绍 MVC 与路由之间的交互，以及典型的 MVC 应用如何使用各种路由功能。</span><span class="sxs-lookup"><span data-stu-id="fcc46-608">This document will explain the interactions between MVC and routing, and how typical MVC apps make use of routing features.</span></span> <span data-ttu-id="fcc46-609">有关高级路由的详细信息，请参阅[路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-609">See [Routing](xref:fundamentals/routing) for details on advanced routing.</span></span>

## <a name="setting-up-routing-middleware"></a><span data-ttu-id="fcc46-610">设置路由中间件</span><span class="sxs-lookup"><span data-stu-id="fcc46-610">Setting up Routing Middleware</span></span>

<span data-ttu-id="fcc46-611">在 *Configure* 方法中，可能会看到与下面类似的代码：</span><span class="sxs-lookup"><span data-stu-id="fcc46-611">In your *Configure* method you may see code similar to:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="fcc46-612">在对 `UseMvc` 的调用中，`MapRoute` 用于创建单个路由，亦称 `default` 路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-612">Inside the call to `UseMvc`, `MapRoute` is used to create a single route, which we'll refer to as the `default` route.</span></span> <span data-ttu-id="fcc46-613">大多数 MVC 应用使用带有模板的路由，与 `default` 路由类似。</span><span class="sxs-lookup"><span data-stu-id="fcc46-613">Most MVC apps will use a route with a template similar to the `default` route.</span></span>

<span data-ttu-id="fcc46-614">路由模板 `"{controller=Home}/{action=Index}/{id?}"` 可以匹配诸如 `/Products/Details/5` 之类的 URL 路径，并通过对路径进行标记来提取路由值 `{ controller = Products, action = Details, id = 5 }`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-614">The route template `"{controller=Home}/{action=Index}/{id?}"` can match a URL path like `/Products/Details/5` and will extract the route values `{ controller = Products, action = Details, id = 5 }` by tokenizing the path.</span></span> <span data-ttu-id="fcc46-615">MVC 将尝试查找名为 `ProductsController` 的控制器并运行 `Details` 操作：</span><span class="sxs-lookup"><span data-stu-id="fcc46-615">MVC will attempt to locate a controller named `ProductsController` and run the action `Details`:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

<span data-ttu-id="fcc46-616">请注意，在此示例中，当调用此操作时，模型绑定会使用值 `id = 5` 将 `id` 参数设置为 `5`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-616">Note that in this example, model binding would use the value of `id = 5` to set the `id` parameter to `5` when invoking this action.</span></span> <span data-ttu-id="fcc46-617">有关更多详细信息，请参阅[模型绑定](../models/model-binding.md)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-617">See the [Model Binding](../models/model-binding.md) for more details.</span></span>

<span data-ttu-id="fcc46-618">使用 `default` 路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-618">Using the `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="fcc46-619">路由模板：</span><span class="sxs-lookup"><span data-stu-id="fcc46-619">The route template:</span></span>

* <span data-ttu-id="fcc46-620">`{controller=Home}` 将 `Home` 定义为默认 `controller`</span><span class="sxs-lookup"><span data-stu-id="fcc46-620">`{controller=Home}` defines `Home` as the default `controller`</span></span>

* <span data-ttu-id="fcc46-621">`{action=Index}` 将 `Index` 定义为默认 `action`</span><span class="sxs-lookup"><span data-stu-id="fcc46-621">`{action=Index}` defines `Index` as the default `action`</span></span>

* <span data-ttu-id="fcc46-622">`{id?}` 将 `id` 定义为可选参数</span><span class="sxs-lookup"><span data-stu-id="fcc46-622">`{id?}` defines `id` as optional</span></span>

<span data-ttu-id="fcc46-623">默认路由参数和可选路由参数不必包含在 URL 路径中进行匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-623">Default and optional route parameters don't need to be present in the URL path for a match.</span></span> <span data-ttu-id="fcc46-624">有关路由模板语法的详细说明，请参阅[路由模板参考](xref:fundamentals/routing#route-template-reference)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-624">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<span data-ttu-id="fcc46-625">`"{controller=Home}/{action=Index}/{id?}"` 可以匹配 URL 路径 `/` 并生成路由值 `{ controller = Home, action = Index }`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-625">`"{controller=Home}/{action=Index}/{id?}"` can match the URL path `/` and will produce the route values `{ controller = Home, action = Index }`.</span></span> <span data-ttu-id="fcc46-626">`controller` 和 `action` 的值使用默认值，`id` 不生成值，因为 URL 路径中没有相应的段。</span><span class="sxs-lookup"><span data-stu-id="fcc46-626">The values for `controller` and `action` make use of the default values, `id` doesn't produce a value since there's no corresponding segment in the URL path.</span></span> <span data-ttu-id="fcc46-627">MVC 使用这些路由值选择 `HomeController` 和 `Index` 操作：</span><span class="sxs-lookup"><span data-stu-id="fcc46-627">MVC would use these route values to select the `HomeController` and `Index` action:</span></span>

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

<span data-ttu-id="fcc46-628">通过使用此控制器定义和路由模板，将对以下任意 URL 路径执行 `HomeController.Index` 操作：</span><span class="sxs-lookup"><span data-stu-id="fcc46-628">Using this controller definition and route template, the `HomeController.Index` action would be executed for any of the following URL paths:</span></span>

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

<span data-ttu-id="fcc46-629">简便方法 `UseMvcWithDefaultRoute`：</span><span class="sxs-lookup"><span data-stu-id="fcc46-629">The convenience method `UseMvcWithDefaultRoute`:</span></span>

```csharp
app.UseMvcWithDefaultRoute();
```

<span data-ttu-id="fcc46-630">可用于替换：</span><span class="sxs-lookup"><span data-stu-id="fcc46-630">Can be used to replace:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="fcc46-631">`UseMvc` 和 `UseMvcWithDefaultRoute` 可向中间件管道添加 `RouterMiddleware` 的实例。</span><span class="sxs-lookup"><span data-stu-id="fcc46-631">`UseMvc` and `UseMvcWithDefaultRoute` add an instance of `RouterMiddleware` to the middleware pipeline.</span></span> <span data-ttu-id="fcc46-632">MVC 不直接与中间件交互，而是使用路由来处理请求。</span><span class="sxs-lookup"><span data-stu-id="fcc46-632">MVC doesn't interact directly with middleware, and uses routing to handle requests.</span></span> <span data-ttu-id="fcc46-633">MVC 通过 `MvcRouteHandler` 实例连接到路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-633">MVC is connected to the routes through an instance of `MvcRouteHandler`.</span></span> <span data-ttu-id="fcc46-634">`UseMvc` 内的代码与下面类似：</span><span class="sxs-lookup"><span data-stu-id="fcc46-634">The code inside of `UseMvc` is similar to the following:</span></span>

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

<span data-ttu-id="fcc46-635">`UseMvc` 不直接定义任何路由，它向 `attribute` 路由的路由集合添加占位符。</span><span class="sxs-lookup"><span data-stu-id="fcc46-635">`UseMvc` doesn't directly define any routes, it adds a placeholder to the route collection for the `attribute` route.</span></span> <span data-ttu-id="fcc46-636">重载 `UseMvc(Action<IRouteBuilder>)` 则允许用户添加自己的路由，并且还支持属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-636">The overload `UseMvc(Action<IRouteBuilder>)` lets you add your own routes and also supports attribute routing.</span></span>  <span data-ttu-id="fcc46-637">`UseMvc` 及其所有变体都会为属性路由添加占位符：无论如何配置 `UseMvc`，属性路由始终可用。</span><span class="sxs-lookup"><span data-stu-id="fcc46-637">`UseMvc` and all of its variations add a placeholder for the attribute route - attribute routing is always available regardless of how you configure `UseMvc`.</span></span> <span data-ttu-id="fcc46-638">`UseMvcWithDefaultRoute` 定义默认路由并支持属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-638">`UseMvcWithDefaultRoute` defines a default route and supports attribute routing.</span></span> <span data-ttu-id="fcc46-639">[属性路由](#attribute-routing-ref-label)部分提供了有关属性路由的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="fcc46-639">The [Attribute Routing](#attribute-routing-ref-label) section includes more details on attribute routing.</span></span>

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a><span data-ttu-id="fcc46-640">传统路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-640">Conventional routing</span></span>

<span data-ttu-id="fcc46-641">`default` 路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-641">The `default` route:</span></span>

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="fcc46-642">前面的代码是传统路由的示例。</span><span class="sxs-lookup"><span data-stu-id="fcc46-642">The preceding code is an example of a conventional routing.</span></span> <span data-ttu-id="fcc46-643">此样式称为传统路由，因为它建立了一个 URL 路径 *约定* ：</span><span class="sxs-lookup"><span data-stu-id="fcc46-643">This style is called conventional routing because it establishes a *convention* for URL paths:</span></span>

* <span data-ttu-id="fcc46-644">第一个路径段映射到控制器名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-644">The first path segment maps to the controller name.</span></span>
* <span data-ttu-id="fcc46-645">第二个映射到操作名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-645">The second maps to the action name.</span></span>
* <span data-ttu-id="fcc46-646">第三段用于可选 `id` 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-646">The third segment is used for an optional `id`.</span></span> <span data-ttu-id="fcc46-647">`id` 映射到模型实体。</span><span class="sxs-lookup"><span data-stu-id="fcc46-647">`id` maps to a model entity.</span></span>

<span data-ttu-id="fcc46-648">使用此 `default` 路由时，URL 路径 `/Products/List` 映射到 `ProductsController.List` 操作，`/Blog/Article/17` 映射到 `BlogController.Article`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-648">Using this `default` route, the URL path `/Products/List` maps to the `ProductsController.List` action, and `/Blog/Article/17` maps to `BlogController.Article`.</span></span> <span data-ttu-id="fcc46-649">此映射 **仅** 基于控制器和操作名称，而不基于命名空间、源文件位置或方法参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-649">This mapping is based on the controller and action names **only** and isn't based on namespaces, source file locations, or method parameters.</span></span>

> [!TIP]
> <span data-ttu-id="fcc46-650">使用默认路由进行传统路由时，可快速生成应用程序，无需为所定义的每项操作提供一个新的 URL 模式。</span><span class="sxs-lookup"><span data-stu-id="fcc46-650">Using conventional routing with the default route allows you to build the application quickly without having to come up with a new URL pattern for each action you define.</span></span> <span data-ttu-id="fcc46-651">对于包含 CRUD 样式操作的应用程序，通过保持各控制器间 URL 的一致性，可帮助简化代码，使 UI 更易预测。</span><span class="sxs-lookup"><span data-stu-id="fcc46-651">For an application with CRUD style actions, having consistency for the URLs across your controllers can help simplify your code and make your UI more predictable.</span></span>

> [!WARNING]
> <span data-ttu-id="fcc46-652">路由模板将 `id` 定义为可选参数，意味着无需在 URL 中提供 ID 也可执行操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-652">The `id` is defined as optional by the route template, meaning that your actions can execute without the ID provided as part of the URL.</span></span> <span data-ttu-id="fcc46-653">从 URL 中省略 `id` 通常会导致模型绑定将它设置为 `0`，进而导致在数据库中找不到与 `id == 0` 匹配的实体。</span><span class="sxs-lookup"><span data-stu-id="fcc46-653">Usually what will happen if `id` is omitted from the URL is that it will be set to `0` by model binding, and as a result no entity will be found in the database matching `id == 0`.</span></span> <span data-ttu-id="fcc46-654">属性路由可以提供细化控制，使某些操作需要 ID，某些操作不需要 ID。</span><span class="sxs-lookup"><span data-stu-id="fcc46-654">Attribute routing can give you fine-grained control to make the ID required for some actions and not for others.</span></span> <span data-ttu-id="fcc46-655">按照惯例，当可选参数（比如 `id`）有可能在正确的用法中出现时，本文档将涵盖这些参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-655">By convention the documentation will include optional parameters like `id` when they're likely to appear in correct usage.</span></span>

## <a name="multiple-routes"></a><span data-ttu-id="fcc46-656">多个路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-656">Multiple routes</span></span>

<span data-ttu-id="fcc46-657">通过添加对 `MapRoute` 的多次调用，可以在 `UseMvc` 内添加多个路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-657">You can add multiple routes inside `UseMvc` by adding more calls to `MapRoute`.</span></span> <span data-ttu-id="fcc46-658">这样做可以定义多个约定，或添加专用于特定操作的传统路由，比如：</span><span class="sxs-lookup"><span data-stu-id="fcc46-658">Doing so allows you to define multiple conventions, or to add conventional routes that are dedicated to a specific action, such as:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="fcc46-659">此处的 `blog` 路由是一个 *专用的传统路由*，这表示它使用传统路由系统，但专用于特定的操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-659">The `blog` route here is a *dedicated conventional route*, meaning that it uses the conventional routing system, but is dedicated to a specific action.</span></span> <span data-ttu-id="fcc46-660">由于 `controller` 和 `action` 不会在路由模板中作为参数显示，它们只能有默认值，因此，此路由将始终映射到 `BlogController.Article` 操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-660">Since `controller` and `action` don't appear in the route template as parameters, they can only have the default values, and thus this route will always map to the action `BlogController.Article`.</span></span>

<span data-ttu-id="fcc46-661">路由集合中的路由会进行排序，并按添加顺序进行处理。</span><span class="sxs-lookup"><span data-stu-id="fcc46-661">Routes in the route collection are ordered, and will be processed in the order they're added.</span></span> <span data-ttu-id="fcc46-662">因此，在此示例中，将先尝试 `blog` 路由，再尝试 `default` 路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-662">So in this example, the `blog` route will be tried before the `default` route.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc46-663">*专用传统路由* 通常使用全部 **捕获** 路由参数 `{*article}` 来捕获 URL 路径的其余部分。</span><span class="sxs-lookup"><span data-stu-id="fcc46-663">*Dedicated conventional routes* often use **catch-all** route parameters like `{*article}` to capture the remaining portion of the URL path.</span></span> <span data-ttu-id="fcc46-664">这会使某个路由变得“太贪婪”，也就是说，它会匹配用户想要使用其他路由来匹配的 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-664">This can make a route 'too greedy' meaning that it matches URLs that you intended to be matched by other routes.</span></span> <span data-ttu-id="fcc46-665">将“贪婪的”路由放在路由表中靠后的位置可解决此问题。</span><span class="sxs-lookup"><span data-stu-id="fcc46-665">Put the 'greedy' routes later in the route table to solve this.</span></span>

### <a name="fallback"></a><span data-ttu-id="fcc46-666">回退</span><span class="sxs-lookup"><span data-stu-id="fcc46-666">Fallback</span></span>

<span data-ttu-id="fcc46-667">在处理请求时，MVC 将验证路由值能否用于在应用程序中查找控制器和操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-667">As part of request processing, MVC will verify that the route values can be used to find a controller and action in your application.</span></span> <span data-ttu-id="fcc46-668">如果路由值与任何操作都不匹配，则将该路由视为不匹配，并尝试下一个路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-668">If the route values don't match an action then the route isn't considered a match, and the next route will be tried.</span></span> <span data-ttu-id="fcc46-669">这称为 *回退*，其目的是简化传统路由重叠的情况。</span><span class="sxs-lookup"><span data-stu-id="fcc46-669">This is called *fallback*, and it's intended to simplify cases where conventional routes overlap.</span></span>

### <a name="disambiguating-actions"></a><span data-ttu-id="fcc46-670">区分操作</span><span class="sxs-lookup"><span data-stu-id="fcc46-670">Disambiguating actions</span></span>

<span data-ttu-id="fcc46-671">当通过路由匹配到两项操作时，MVC 必须进行区分，以选择“最佳”候选项，否则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="fcc46-671">When two actions match through routing, MVC must disambiguate to choose the 'best' candidate or else throw an exception.</span></span> <span data-ttu-id="fcc46-672">例如：</span><span class="sxs-lookup"><span data-stu-id="fcc46-672">For example:</span></span>

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

<span data-ttu-id="fcc46-673">此控制器定义了两项操作，这两项操作均与 URL 路径 `/Products/Edit/17` 和路由数据 `{ controller = Products, action = Edit, id = 17 }` 匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-673">This controller defines two actions that would match the URL path `/Products/Edit/17` and route data `{ controller = Products, action = Edit, id = 17 }`.</span></span> <span data-ttu-id="fcc46-674">这是 MVC 控制器的典型模式，其中 `Edit(int)` 显示用于编辑产品的表单，`Edit(int, Product)` 处理已发布的表单。</span><span class="sxs-lookup"><span data-stu-id="fcc46-674">This is a typical pattern for MVC controllers where `Edit(int)` shows a form to edit a product, and `Edit(int, Product)` processes  the posted form.</span></span> <span data-ttu-id="fcc46-675">为此，MVC 需要在请求为 HTTP `POST` 时选择 `Edit(int, Product)`，在 Http 谓词为任何其他内容时选择 `Edit(int)`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-675">To make this possible MVC would need to choose `Edit(int, Product)` when the request is an HTTP `POST` and `Edit(int)` when the HTTP verb is anything else.</span></span>

<span data-ttu-id="fcc46-676">`HttpPostAttribute` ( `[HttpPost]` ) 是 `IActionConstraint` 的实现，它仅允许执行当 Http 谓词为 `POST` 时选择的操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-676">The `HttpPostAttribute` ( `[HttpPost]` ) is an implementation of `IActionConstraint` that will only allow the action to be selected when the HTTP verb is `POST`.</span></span> <span data-ttu-id="fcc46-677">`IActionConstraint` 的存在使 `Edit(int, Product)` 成为比 `Edit(int)`“更好”的匹配项，因此会先尝试 `Edit(int, Product)`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-677">The presence of an `IActionConstraint` makes the `Edit(int, Product)` a 'better' match than `Edit(int)`, so `Edit(int, Product)` will be tried first.</span></span>

<span data-ttu-id="fcc46-678">只需在特殊化方案中编写自定义 `IActionConstraint` 实现，但务必了解 `HttpPostAttribute` 等属性的角色 — 为其他 Http 谓词定义了类似的属性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-678">You will only need to write custom `IActionConstraint` implementations in specialized scenarios, but it's important to understand the role of attributes like `HttpPostAttribute`  - similar attributes are defined for other HTTP verbs.</span></span> <span data-ttu-id="fcc46-679">在传统路由中，当操作属于 `show form -> submit form` 工作流时通常使用相同的操作名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-679">In conventional routing it's common for actions to use the same action name when they're part of a `show form -> submit form` workflow.</span></span> <span data-ttu-id="fcc46-680">在阅读[了解 IActionConstraint](#understanding-iactionconstraint) 部分后，此模式的便利性将变得更加明显。</span><span class="sxs-lookup"><span data-stu-id="fcc46-680">The convenience of this pattern will become more apparent after reviewing the [Understanding IActionConstraint](#understanding-iactionconstraint) section.</span></span>

<span data-ttu-id="fcc46-681">如果匹配多个路由，但 MVC 找不到“最佳”路由，则会引发 `AmbiguousActionException`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-681">If multiple routes match, and MVC can't find a 'best' route, it will throw an `AmbiguousActionException`.</span></span>

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a><span data-ttu-id="fcc46-682">路由名称</span><span class="sxs-lookup"><span data-stu-id="fcc46-682">Route names</span></span>

<span data-ttu-id="fcc46-683">以下示例中的字符串 `"blog"` 和 `"default"` 都是路由名称：</span><span class="sxs-lookup"><span data-stu-id="fcc46-683">The strings  `"blog"` and `"default"` in the following examples are route names:</span></span>

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="fcc46-684">路由名称为路由提供一个逻辑名称，以便使用命名路由来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-684">The route names give the route a logical name so that the named route can be used for URL generation.</span></span> <span data-ttu-id="fcc46-685">路由排序会使 URL 生成复杂化，而这极大地简化了 URL 创建。</span><span class="sxs-lookup"><span data-stu-id="fcc46-685">This greatly simplifies URL creation when the ordering of routes could make URL generation complicated.</span></span> <span data-ttu-id="fcc46-686">路由名称必须在应用程序范围内唯一。</span><span class="sxs-lookup"><span data-stu-id="fcc46-686">Route names must be unique application-wide.</span></span>

<span data-ttu-id="fcc46-687">路由名称不影响请求的 URL 匹配或处理；它们仅用于 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="fcc46-687">Route names have no impact on URL matching or handling of requests; they're used only for URL generation.</span></span> <span data-ttu-id="fcc46-688">[路由](xref:fundamentals/routing)提供了有关 URL 生成（包括 MVC 特定帮助程序中的 URL 生成）的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="fcc46-688">[Routing](xref:fundamentals/routing) has more detailed information on URL generation including URL generation in MVC-specific helpers.</span></span>

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a><span data-ttu-id="fcc46-689">属性路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-689">Attribute routing</span></span>

<span data-ttu-id="fcc46-690">属性路由使用一组属性将操作直接映射到路由模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-690">Attribute routing uses a set of attributes to map actions directly to route templates.</span></span> <span data-ttu-id="fcc46-691">在下面的示例中，`Configure` 方法使用 `app.UseMvc();`，不传递任何路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-691">In the following example, `app.UseMvc();` is used in the `Configure` method and no route is passed.</span></span> <span data-ttu-id="fcc46-692">`HomeController` 将匹配一组 URL，这组 URL 与默认路由 `{controller=Home}/{action=Index}/{id?}` 匹配的 URL 类似：</span><span class="sxs-lookup"><span data-stu-id="fcc46-692">The `HomeController` will match a set of URLs similar to what the default route `{controller=Home}/{action=Index}/{id?}` would match:</span></span>

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

<span data-ttu-id="fcc46-693">将针对任意 URL 路径 `/`、`/Home` 或 `/Home/Index` 执行 `HomeController.Index()` 操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-693">The `HomeController.Index()` action will be executed for any of the URL paths `/`, `/Home`, or `/Home/Index`.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc46-694">此示例重点介绍属性路由与传统路由之间的主要编程差异。</span><span class="sxs-lookup"><span data-stu-id="fcc46-694">This example highlights a key programming difference between attribute routing and conventional routing.</span></span> <span data-ttu-id="fcc46-695">属性路由需要更多输入来指定路由；传统的默认路由处理路由的方式则更简洁。</span><span class="sxs-lookup"><span data-stu-id="fcc46-695">Attribute routing requires more input to specify a route; the conventional default route handles routes more succinctly.</span></span> <span data-ttu-id="fcc46-696">但是，属性路由允许（并需要）精确控制应用于每项操作的路由模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-696">However, attribute routing allows (and requires) precise control of which route templates apply to each action.</span></span>

<span data-ttu-id="fcc46-697">使用属性路由时，控制器名称和操作名称对于操作的选择 **没有** 影响。</span><span class="sxs-lookup"><span data-stu-id="fcc46-697">With attribute routing the controller name and action names play **no** role in which action is selected.</span></span> <span data-ttu-id="fcc46-698">此示例匹配的 URL 与上一示例相同。</span><span class="sxs-lookup"><span data-stu-id="fcc46-698">This example will match the same URLs as the previous example.</span></span>

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> <span data-ttu-id="fcc46-699">上述路由模板未定义 `action`、`area` 和 `controller` 的路由参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-699">The route templates above don't define route parameters for `action`, `area`, and `controller`.</span></span> <span data-ttu-id="fcc46-700">事实上，属性路由中不允许使用这些路由参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-700">In fact, these route parameters are not allowed in attribute routes.</span></span> <span data-ttu-id="fcc46-701">由于路由模板已与某项操作关联，因此，分析 URL 中的操作名称毫无意义。</span><span class="sxs-lookup"><span data-stu-id="fcc46-701">Since the route template is already associated with an action, it wouldn't make sense to parse the action name from the URL.</span></span>

## <a name="attribute-routing-with-httpverb-attributes"></a><span data-ttu-id="fcc46-702">使用 Http[Verb] 属性的属性路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-702">Attribute routing with Http[Verb] attributes</span></span>

<span data-ttu-id="fcc46-703">属性路由还可以使用 `Http[Verb]` 属性，比如 `HttpPostAttribute`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-703">Attribute routing can also make use of the `Http[Verb]` attributes such as `HttpPostAttribute`.</span></span> <span data-ttu-id="fcc46-704">所有这些属性都可采用路由模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-704">All of these attributes can accept a route template.</span></span> <span data-ttu-id="fcc46-705">此示例展示与同一路由模板匹配的两项操作：</span><span class="sxs-lookup"><span data-stu-id="fcc46-705">This example shows two actions that match the same route template:</span></span>

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

<span data-ttu-id="fcc46-706">对于诸如 `/products` 之类的 URL 路径，当 Http 谓词为 `GET` 时将执行 `ProductsApi.ListProducts` 操作，当 Http 谓词为 `POST` 时将执行 `ProductsApi.CreateProduct`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-706">For a URL path like `/products` the `ProductsApi.ListProducts` action will be executed when the HTTP verb is `GET` and `ProductsApi.CreateProduct` will be executed when the HTTP verb is `POST`.</span></span> <span data-ttu-id="fcc46-707">属性路由首先将 URL 与路由属性定义的路由模板集进行匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-707">Attribute routing first matches the URL against the set of route templates defined by route attributes.</span></span> <span data-ttu-id="fcc46-708">一旦某个路由模板匹配，就会应用 `IActionConstraint` 约束来确定可以执行的操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-708">Once a route template matches, `IActionConstraint` constraints are applied to determine which actions can be executed.</span></span>

> [!TIP]
> <span data-ttu-id="fcc46-709">生成 REST API 时，很少会在操作方法上使用 `[Route(...)]`这是因为该操作将接受所有 HTTP 方法。</span><span class="sxs-lookup"><span data-stu-id="fcc46-709">When building a REST API, it's rare that you will want to use `[Route(...)]` on an action method as the action will accept all HTTP methods.</span></span> <span data-ttu-id="fcc46-710">建议使用更特定的 `Http*Verb*Attributes` 来明确 API 所支持的操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-710">It's better to use the more specific `Http*Verb*Attributes` to be precise about what your API supports.</span></span> <span data-ttu-id="fcc46-711">REST API 的客户端需要知道映射到特定逻辑操作的路径和 Http 谓词。</span><span class="sxs-lookup"><span data-stu-id="fcc46-711">Clients of REST APIs are expected to know what paths and HTTP verbs map to specific logical operations.</span></span>

<span data-ttu-id="fcc46-712">由于属性路由适用于特定操作，因此，使参数变成路由模板定义中的必需参数很简单。</span><span class="sxs-lookup"><span data-stu-id="fcc46-712">Since an attribute route applies to a specific action, it's easy to make parameters required as part of the route template definition.</span></span> <span data-ttu-id="fcc46-713">在此示例中，`id` 是 URL 路径中的必需参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-713">In this example, `id` is required as part of the URL path.</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="fcc46-714">将针对诸如 `/products/3`（而非 `/products`）之类的 URL 路径执行 `ProductsApi.GetProduct(int)` 操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-714">The `ProductsApi.GetProduct(int)` action will be executed for a URL path like `/products/3` but not for a URL path like `/products`.</span></span> <span data-ttu-id="fcc46-715">请参阅[路由](xref:fundamentals/routing)了解路由模板和相关选项的完整说明。</span><span class="sxs-lookup"><span data-stu-id="fcc46-715">See [Routing](xref:fundamentals/routing) for a full description of route templates and related options.</span></span>

## <a name="route-name"></a><span data-ttu-id="fcc46-716">路由名称</span><span class="sxs-lookup"><span data-stu-id="fcc46-716">Route Name</span></span>

<span data-ttu-id="fcc46-717">以下代码定义 `Products_List` 的 *路由名称*：</span><span class="sxs-lookup"><span data-stu-id="fcc46-717">The following code  defines a *route name* of `Products_List`:</span></span>

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="fcc46-718">可以使用路由名称基于特定路由生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-718">Route names can be used to generate a URL based on a specific route.</span></span> <span data-ttu-id="fcc46-719">路由名称不影响路由的 URL 匹配行为，仅用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-719">Route names have no impact on the URL matching behavior of routing and are only used for URL generation.</span></span> <span data-ttu-id="fcc46-720">路由名称必须在应用程序范围内唯一。</span><span class="sxs-lookup"><span data-stu-id="fcc46-720">Route names must be unique application-wide.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc46-721">这一点与传统的 *默认路由* 相反，后者将 `id` 参数定义为可选参数 (`{id?}`)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-721">Contrast this with the conventional *default route*, which defines the `id` parameter as optional (`{id?}`).</span></span> <span data-ttu-id="fcc46-722">这种精确指定 API 的功能可带来一些好处，比如允许将 `/products` 和 `/products/5` 分派到不同的操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-722">This ability to precisely specify APIs has advantages, such as  allowing `/products` and `/products/5` to be dispatched to different actions.</span></span>

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a><span data-ttu-id="fcc46-723">合并路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-723">Combining routes</span></span>

<span data-ttu-id="fcc46-724">若要使属性路由减少重复，可将控制器上的路由属性与各个操作上的路由属性合并。</span><span class="sxs-lookup"><span data-stu-id="fcc46-724">To make attribute routing less repetitive, route attributes on the controller are combined with route attributes on the individual actions.</span></span> <span data-ttu-id="fcc46-725">控制器上定义的所有路由模板均作为操作上路由模板的前缀。</span><span class="sxs-lookup"><span data-stu-id="fcc46-725">Any route templates defined on the controller are prepended to route templates on the actions.</span></span> <span data-ttu-id="fcc46-726">在控制器上放置路由属性会使控制器中的 **所有** 操作都使用属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-726">Placing a route attribute on the controller makes **all** actions in the controller use attribute routing.</span></span>

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

<span data-ttu-id="fcc46-727">在此示例中，URL 路径 `/products` 可以匹配 `ProductsApi.ListProducts`，URL 路径 `/products/5` 可以匹配 `ProductsApi.GetProduct(int)`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-727">In this example the URL path `/products` can match `ProductsApi.ListProducts`, and the URL path `/products/5` can match `ProductsApi.GetProduct(int)`.</span></span> <span data-ttu-id="fcc46-728">这两项操作仅匹配 HTTP `GET`，因为它们用 `HttpGetAttribute` 标记。</span><span class="sxs-lookup"><span data-stu-id="fcc46-728">Both of these actions only match HTTP `GET` because they're marked with the `HttpGetAttribute`.</span></span>

<span data-ttu-id="fcc46-729">应用于操作的以 `/` 或 `~/` 开头的路由模板不与应用于控制器的路由模板合并。</span><span class="sxs-lookup"><span data-stu-id="fcc46-729">Route templates applied to an action that begin with `/` or `~/` don't get combined with route templates applied to the controller.</span></span> <span data-ttu-id="fcc46-730">此示例匹配一组与 *默认路由* 类似的 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="fcc46-730">This example matches a set of URL paths similar to the *default route*.</span></span>

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a><span data-ttu-id="fcc46-731">对属性路由排序</span><span class="sxs-lookup"><span data-stu-id="fcc46-731">Ordering attribute routes</span></span>

<span data-ttu-id="fcc46-732">与按定义的顺序执行的传统路由不同，属性路由会生成一个树并同时匹配所有路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-732">In contrast to conventional routes, which execute in a defined order, attribute routing builds a tree and matches all routes simultaneously.</span></span> <span data-ttu-id="fcc46-733">其行为就像路由条目是以理想排序方式放置的一样；最特定的路由有机会比较一般的路由先执行。</span><span class="sxs-lookup"><span data-stu-id="fcc46-733">This behaves as-if the route entries were placed in an ideal ordering; the most specific routes have a chance to execute before the more general routes.</span></span>

<span data-ttu-id="fcc46-734">例如，像 `blog/search/{topic}` 这样的路由比像 `blog/{*article}` 这样的路由更特定。</span><span class="sxs-lookup"><span data-stu-id="fcc46-734">For example, a route like `blog/search/{topic}` is more specific than a route like `blog/{*article}`.</span></span> <span data-ttu-id="fcc46-735">从逻辑上讲，`blog/search/{topic}` 路由默认情况下先“运行”，因为这是唯一合理的排序。</span><span class="sxs-lookup"><span data-stu-id="fcc46-735">Logically speaking the `blog/search/{topic}` route 'runs' first, by default, because that's the only sensible ordering.</span></span> <span data-ttu-id="fcc46-736">使用传统路由时，开发人员负责按所需顺序放置路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-736">Using conventional routing, the developer is  responsible for placing routes in the desired order.</span></span>

<span data-ttu-id="fcc46-737">属性路由可以使用框架提供的所有路由属性的 `Order` 属性来配置顺序。</span><span class="sxs-lookup"><span data-stu-id="fcc46-737">Attribute routes can configure an order, using the `Order` property of all of the framework provided route attributes.</span></span> <span data-ttu-id="fcc46-738">路由按 `Order` 属性的升序进行处理。</span><span class="sxs-lookup"><span data-stu-id="fcc46-738">Routes are processed according to an ascending sort of the `Order` property.</span></span> <span data-ttu-id="fcc46-739">默认顺序为 `0`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-739">The default order is `0`.</span></span> <span data-ttu-id="fcc46-740">使用 `Order = -1` 设置的路由比未设置顺序的路由先运行。</span><span class="sxs-lookup"><span data-stu-id="fcc46-740">Setting a route using `Order = -1` will run before routes that don't set an order.</span></span> <span data-ttu-id="fcc46-741">使用 `Order = 1` 设置的路由在默认路由排序后运行。</span><span class="sxs-lookup"><span data-stu-id="fcc46-741">Setting a route using `Order = 1` will run after default route ordering.</span></span>

> [!TIP]
> <span data-ttu-id="fcc46-742">避免依赖 `Order`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-742">Avoid depending on `Order`.</span></span> <span data-ttu-id="fcc46-743">如果 URL 空间需要有显式顺序值才能正确进行路由，则同样可能使客户端混淆不清。</span><span class="sxs-lookup"><span data-stu-id="fcc46-743">If your URL-space requires explicit order values to route correctly, then it's likely confusing to clients as well.</span></span> <span data-ttu-id="fcc46-744">属性路由通常选择与 URL 匹配的正确路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-744">In general attribute routing will select the correct route with URL matching.</span></span> <span data-ttu-id="fcc46-745">如果用于 URL 生成的默认顺序不起作用，使用路由名称作为替代项通常比应用 `Order` 属性更简单。</span><span class="sxs-lookup"><span data-stu-id="fcc46-745">If the default order used for URL generation isn't working, using route name as an override is usually simpler than applying the `Order` property.</span></span>

<span data-ttu-id="fcc46-746">Razor Pages 路由和 MVC 控制器路由共享一个实现。</span><span class="sxs-lookup"><span data-stu-id="fcc46-746">Razor Pages routing and MVC controller routing share an implementation.</span></span> <span data-ttu-id="fcc46-747">页面上的路由顺序信息 Razor 主题中提供了[ Razor 页面路由和应用约定：路由顺序](xref:razor-pages/razor-pages-conventions#route-order)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-747">Information on route order in the Razor Pages topics is available at [Razor Pages route and app conventions: Route order](xref:razor-pages/razor-pages-conventions#route-order).</span></span>

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a><span data-ttu-id="fcc46-748">路由模板中的标记替换（[controller]、[action]、[area]）</span><span class="sxs-lookup"><span data-stu-id="fcc46-748">Token replacement in route templates ([controller], [action], [area])</span></span>

<span data-ttu-id="fcc46-749">为方便起见，特性路由支持 *标记替换* ，方法是在方括号中包含一个标记 (`[` ， `]`) 。</span><span class="sxs-lookup"><span data-stu-id="fcc46-749">For convenience, attribute routes support *token replacement* by enclosing a token in square-brackets (`[`, `]`).</span></span> <span data-ttu-id="fcc46-750">标记 `[action]`、`[area]` 和 `[controller]` 替换为定义了路由的操作中的操作名称值、区域名称值和控制器名称值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-750">The tokens `[action]`, `[area]`, and `[controller]` are replaced with the values of the action name, area name, and controller name from the action where the route is defined.</span></span> <span data-ttu-id="fcc46-751">在接下来的示例中，操作与注释中所述的 URL 路径匹配：</span><span class="sxs-lookup"><span data-stu-id="fcc46-751">In the following example, the actions match URL paths as described in the comments:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="fcc46-752">标记替换发生在属性路由生成的最后一步。</span><span class="sxs-lookup"><span data-stu-id="fcc46-752">Token replacement occurs as the last step of building the attribute routes.</span></span> <span data-ttu-id="fcc46-753">上述示例的行为方式将与以下代码相同：</span><span class="sxs-lookup"><span data-stu-id="fcc46-753">The above example will behave the same as the following code:</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

<span data-ttu-id="fcc46-754">属性路由还可以与继承结合使用。</span><span class="sxs-lookup"><span data-stu-id="fcc46-754">Attribute routes can also be combined with inheritance.</span></span> <span data-ttu-id="fcc46-755">与标记替换结合使用时尤为强大。</span><span class="sxs-lookup"><span data-stu-id="fcc46-755">This is particularly powerful combined with token replacement.</span></span>

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

<span data-ttu-id="fcc46-756">标记替换也适用于属性路由定义的路由名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-756">Token replacement also applies to route names defined by attribute routes.</span></span> <span data-ttu-id="fcc46-757">`[Route("[controller]/[action]", Name="[controller]_[action]")]` 为每项操作生成一个唯一的路由名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-757">`[Route("[controller]/[action]", Name="[controller]_[action]")]` generates a unique route name for each action.</span></span>

<span data-ttu-id="fcc46-758">若要匹配文本标记替换分隔符 `[` 或 `]`，可通过重复该字符（`[[` 或 `]]`）对其进行转义。</span><span class="sxs-lookup"><span data-stu-id="fcc46-758">To match the literal token replacement delimiter `[` or  `]`, escape it by repeating the character (`[[` or `]]`).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a><span data-ttu-id="fcc46-759">使用参数转换程序自定义标记替换</span><span class="sxs-lookup"><span data-stu-id="fcc46-759">Use a parameter transformer to customize token replacement</span></span>

<span data-ttu-id="fcc46-760">使用参数转换程序可以自定义标记替换。</span><span class="sxs-lookup"><span data-stu-id="fcc46-760">Token replacement can be customized using a parameter transformer.</span></span> <span data-ttu-id="fcc46-761">参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-761">A parameter transformer implements `IOutboundParameterTransformer` and transforms the value of parameters.</span></span> <span data-ttu-id="fcc46-762">例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-762">For example, a custom `SlugifyParameterTransformer` parameter transformer changes the `SubscriptionManagement` route value to `subscription-management`.</span></span>

<span data-ttu-id="fcc46-763">`RouteTokenTransformerConvention` 是应用程序模型约定，可以：</span><span class="sxs-lookup"><span data-stu-id="fcc46-763">The `RouteTokenTransformerConvention` is an application model convention that:</span></span>

* <span data-ttu-id="fcc46-764">将参数转换程序应用到应用程序中的所有属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-764">Applies a parameter transformer to all attribute routes in an application.</span></span>
* <span data-ttu-id="fcc46-765">在替换属性路由标记值时对其进行自定义。</span><span class="sxs-lookup"><span data-stu-id="fcc46-765">Customizes the attribute route token values as they are replaced.</span></span>

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

<span data-ttu-id="fcc46-766">`RouteTokenTransformerConvention` 在 `ConfigureServices` 中注册为选项。</span><span class="sxs-lookup"><span data-stu-id="fcc46-766">The `RouteTokenTransformerConvention` is registered as an option in `ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a><span data-ttu-id="fcc46-767">多个路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-767">Multiple Routes</span></span>

<span data-ttu-id="fcc46-768">属性路由支持定义多个访问同一操作的路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-768">Attribute routing supports defining multiple routes that reach the same action.</span></span> <span data-ttu-id="fcc46-769">此操作最常用于模拟 *默认传统路由* 的行为，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="fcc46-769">The most common usage of this is to mimic the behavior of the *default conventional route* as shown in the following example:</span></span>

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

<span data-ttu-id="fcc46-770">在控制器上放置多个路由属性意味着，每个路由属性将与操作方法上的每个路由属性合并。</span><span class="sxs-lookup"><span data-stu-id="fcc46-770">Putting multiple route attributes on the controller means that each one will combine with each of the route attributes on the action methods.</span></span>

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

<span data-ttu-id="fcc46-771">当在某个操作上放置多个路由属性（可实现 `IActionConstraint`）时，每个操作约束将与定义它的属性中的路由模板合并。</span><span class="sxs-lookup"><span data-stu-id="fcc46-771">When multiple route attributes (that implement `IActionConstraint`) are placed on an action, then each action constraint combines with the route template from the attribute that defined it.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> <span data-ttu-id="fcc46-772">在操作上使用多个路由可能看起来很强大，但更建议使应用程序的 URL 空间保持简洁且定义完善。</span><span class="sxs-lookup"><span data-stu-id="fcc46-772">While using multiple routes on actions can seem powerful, it's better to keep your application's URL space simple and well-defined.</span></span> <span data-ttu-id="fcc46-773">仅在需要时，例如为了支持现有客户端，才在操作上使用多个路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-773">Use multiple routes on actions only where needed, for example to support existing clients.</span></span>

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a><span data-ttu-id="fcc46-774">指定属性路由的可选参数、默认值和约束</span><span class="sxs-lookup"><span data-stu-id="fcc46-774">Specifying attribute route optional parameters, default values, and constraints</span></span>

<span data-ttu-id="fcc46-775">属性路由支持使用与传统路由相同的内联语法，来指定可选参数、默认值和约束。</span><span class="sxs-lookup"><span data-stu-id="fcc46-775">Attribute routes support the same inline syntax as conventional routes to specify optional parameters, default values, and constraints.</span></span>

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

<span data-ttu-id="fcc46-776">有关路由模板语法的详细说明，请参阅[路由模板参考](xref:fundamentals/routing#route-template-reference)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-776">See [Route Template Reference](xref:fundamentals/routing#route-template-reference) for a detailed description of route template syntax.</span></span>

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a><span data-ttu-id="fcc46-777">使用 `IRouteTemplateProvider` 的自定义路由属性</span><span class="sxs-lookup"><span data-stu-id="fcc46-777">Custom route attributes using `IRouteTemplateProvider`</span></span>

<span data-ttu-id="fcc46-778">该框架中提供的所有路由属性（`[Route(...)]`、`[HttpGet(...)]` 等）都可实现 `IRouteTemplateProvider` 接口。</span><span class="sxs-lookup"><span data-stu-id="fcc46-778">All of the route attributes provided in the framework ( `[Route(...)]`, `[HttpGet(...)]` , etc.) implement the `IRouteTemplateProvider` interface.</span></span> <span data-ttu-id="fcc46-779">当应用启动时，MVC 会查找控制器类和操作方法上的属性，并使用可实现 `IRouteTemplateProvider` 的属性生成一组初始路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-779">MVC looks for attributes on controller classes and action methods when the app starts and uses the ones that implement `IRouteTemplateProvider` to build the initial set of routes.</span></span>

<span data-ttu-id="fcc46-780">用户可以实现 `IRouteTemplateProvider` 来定义自己的路由属性。</span><span class="sxs-lookup"><span data-stu-id="fcc46-780">You can implement `IRouteTemplateProvider` to define your own route attributes.</span></span> <span data-ttu-id="fcc46-781">每个 `IRouteTemplateProvider` 都允许定义一个包含自定义路由模板、顺序和名称的路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-781">Each `IRouteTemplateProvider` allows you to define a single route with a custom route template, order, and name:</span></span>

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

<span data-ttu-id="fcc46-782">应用 `[MyApiController]` 时，上述示例中的属性会自动将 `Template` 设置为 `"api/[controller]"`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-782">The attribute from the above example automatically sets the `Template` to `"api/[controller]"` when `[MyApiController]` is applied.</span></span>

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a><span data-ttu-id="fcc46-783">使用应用程序模型自定义属性路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-783">Using Application Model to customize attribute routes</span></span>

<span data-ttu-id="fcc46-784">*应用程序模型* 是一个在启动时创建的对象模型，MVC 可使用其中的所有元数据来路由和执行操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-784">The *application model* is an object model created at startup with all of the metadata used by MVC to route and execute your actions.</span></span> <span data-ttu-id="fcc46-785">*应用程序模型* 包含从路由属性收集（通过 `IRouteTemplateProvider`）的所有数据。</span><span class="sxs-lookup"><span data-stu-id="fcc46-785">The *application model* includes all of the data gathered from route attributes (through `IRouteTemplateProvider`).</span></span> <span data-ttu-id="fcc46-786">可通过编写 *约定* 在启动时修改应用程序模型，以便自定义路由的行为方式。</span><span class="sxs-lookup"><span data-stu-id="fcc46-786">You can write *conventions* to modify the application model at startup time to customize how routing behaves.</span></span> <span data-ttu-id="fcc46-787">此部分通过一个简单的示例说明了如何使用应用程序模型自定义路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-787">This section shows a simple example of customizing routing using application model.</span></span>

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a><span data-ttu-id="fcc46-788">混合路由：属性路由与传统路由</span><span class="sxs-lookup"><span data-stu-id="fcc46-788">Mixed routing: Attribute routing vs conventional routing</span></span>

<span data-ttu-id="fcc46-789">MVC 应用程序可以混合使用传统路由与属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-789">MVC applications can mix the use of conventional routing and attribute routing.</span></span> <span data-ttu-id="fcc46-790">通常将传统路由用于为浏览器处理 HTML 页面的控制器，将属性路由用于处理 REST API 的控制器。</span><span class="sxs-lookup"><span data-stu-id="fcc46-790">It's typical to use conventional routes for controllers serving HTML pages for browsers, and attribute routing for controllers serving REST APIs.</span></span>

<span data-ttu-id="fcc46-791">操作既支持传统路由，也支持属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-791">Actions are either conventionally routed or attribute routed.</span></span> <span data-ttu-id="fcc46-792">通过在控制器或操作上放置路由可实现属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-792">Placing a route on the controller or the action makes it attribute routed.</span></span> <span data-ttu-id="fcc46-793">不能通过传统路由访问定义属性路由的操作，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="fcc46-793">Actions that define attribute routes cannot be reached through the conventional routes and vice-versa.</span></span> <span data-ttu-id="fcc46-794">控制器上的 **任何** 路由属性都会使控制器中的所有操作使用属性路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-794">**Any** route attribute on the controller makes all actions in the controller attribute routed.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc46-795">这两种路由系统的区别在于 URL 与路由模板匹配后所应用的过程。</span><span class="sxs-lookup"><span data-stu-id="fcc46-795">What distinguishes the two types of routing systems is the process applied after a URL matches a route template.</span></span> <span data-ttu-id="fcc46-796">在传统路由中，将使用匹配项中的路由值，从包含所有传统路由操作的查找表中选择操作和控制器。</span><span class="sxs-lookup"><span data-stu-id="fcc46-796">In conventional routing, the route values from the match are used to choose the action and controller from a lookup table of all conventional routed actions.</span></span> <span data-ttu-id="fcc46-797">在属性路由中，每个模板都与某项操作关联，无需进行进一步的查找。</span><span class="sxs-lookup"><span data-stu-id="fcc46-797">In attribute routing, each template is already associated with an action, and no further lookup is needed.</span></span>

## <a name="complex-segments"></a><span data-ttu-id="fcc46-798">复杂段</span><span class="sxs-lookup"><span data-stu-id="fcc46-798">Complex segments</span></span>

<span data-ttu-id="fcc46-799">复杂段（例如，`[Route("/dog{token}cat")]`）通过非贪婪的方式从右到左匹配文字进行处理。</span><span class="sxs-lookup"><span data-stu-id="fcc46-799">Complex segments (for example, `[Route("/dog{token}cat")]`), are processed by matching up literals from right to left in a non-greedy way.</span></span> <span data-ttu-id="fcc46-800">有关说明，请参阅[源代码](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-800">See [the source code](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296) for a description.</span></span> <span data-ttu-id="fcc46-801">有关详细信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8197)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-801">For more information, see [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8197).</span></span>

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a><span data-ttu-id="fcc46-802">URL 生成</span><span class="sxs-lookup"><span data-stu-id="fcc46-802">URL Generation</span></span>

<span data-ttu-id="fcc46-803">MVC 应用程序可以使用路由的 URL 生成功能，生成指向操作的 URL 链接。</span><span class="sxs-lookup"><span data-stu-id="fcc46-803">MVC applications can use routing's URL generation features to generate URL links to actions.</span></span> <span data-ttu-id="fcc46-804">生成 URL 可消除硬编码 URL，使代码更稳定、更易维护。</span><span class="sxs-lookup"><span data-stu-id="fcc46-804">Generating URLs eliminates hardcoding URLs, making your code more robust and maintainable.</span></span> <span data-ttu-id="fcc46-805">此部分重点介绍 MVC 提供的 URL 生成功能，并且仅涵盖 URL 生成工作原理的基础知识。</span><span class="sxs-lookup"><span data-stu-id="fcc46-805">This section focuses on the URL generation features provided by MVC and will only cover basics of how URL generation works.</span></span> <span data-ttu-id="fcc46-806">有关 URL 生成的详细说明，请参阅[路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-806">See [Routing](xref:fundamentals/routing) for a detailed description of URL generation.</span></span>

<span data-ttu-id="fcc46-807">`IUrlHelper` 接口用于生成 URL，是 MVC 与路由之间的基础结构的基础部分。</span><span class="sxs-lookup"><span data-stu-id="fcc46-807">The `IUrlHelper` interface is the underlying piece of infrastructure between MVC and routing for URL generation.</span></span> <span data-ttu-id="fcc46-808">在控制器、视图和视图组件中，可通过 `Url` 属性找到 `IUrlHelper` 的实例。</span><span class="sxs-lookup"><span data-stu-id="fcc46-808">You'll find an instance of `IUrlHelper` available through the `Url` property in controllers, views, and view components.</span></span>

<span data-ttu-id="fcc46-809">在此示例中，将通过 `Controller.Url` 属性使用 `IUrlHelper` 接口来生成指向另一项操作的 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-809">In this example, the `IUrlHelper` interface is used through the `Controller.Url` property to generate a URL to another action.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

<span data-ttu-id="fcc46-810">如果应用程序使用的是传统默认路由，则 `url` 变量的值将为 URL 路径字符串 `/UrlGeneration/Destination`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-810">If the application is using the default conventional route, the value of the `url` variable will be the URL path string `/UrlGeneration/Destination`.</span></span> <span data-ttu-id="fcc46-811">此 URL 路径由路由创建，方法是将当前请求中的路由值（环境值）与传递到 `Url.Action` 的值合并，并将这些值替换到路由模板中：</span><span class="sxs-lookup"><span data-stu-id="fcc46-811">This URL path is created by routing by combining the route values from the current request (ambient values), with the values passed to `Url.Action` and substituting those values into the route template:</span></span>

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

<span data-ttu-id="fcc46-812">路由模板中的每个路由参数都会通过将名称与这些值和环境值匹配，来替换掉原来的值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-812">Each route parameter in the route template has its value substituted by matching names with the values and ambient values.</span></span> <span data-ttu-id="fcc46-813">没有值的路由参数如果有默认值，则可使用默认值；如果本身是可选参数（比如此示例中的 `id`），则可直接跳过。</span><span class="sxs-lookup"><span data-stu-id="fcc46-813">A route parameter that doesn't have a value can use a default value if it has one, or be skipped if it's optional (as in the case of `id` in this example).</span></span> <span data-ttu-id="fcc46-814">如果任何所需路由参数没有对应的值，URL 生成将失败。</span><span class="sxs-lookup"><span data-stu-id="fcc46-814">URL generation will fail if any required route parameter doesn't have a corresponding value.</span></span> <span data-ttu-id="fcc46-815">如果某个路由的 URL 生成失败，则尝试下一个路由，直到尝试所有路由或找到匹配项为止。</span><span class="sxs-lookup"><span data-stu-id="fcc46-815">If URL generation fails for a route, the next route is tried until all routes have been tried or a match is found.</span></span>

<span data-ttu-id="fcc46-816">上面的 `Url.Action` 示例假定使用传统路由，但 URL 生成功能的工作方式与属性路由相似，只不过概念不同。</span><span class="sxs-lookup"><span data-stu-id="fcc46-816">The example of `Url.Action` above assumes conventional routing, but URL generation works similarly with attribute routing, though the concepts are different.</span></span> <span data-ttu-id="fcc46-817">在传统路由中，路由值用于扩展模板，`controller` 和 `action` 的路由值通常出现在该模板中 — 这种做法可行是因为通过路由匹配的 URL 遵守某项 *约定*。</span><span class="sxs-lookup"><span data-stu-id="fcc46-817">With conventional routing, the route values are used to expand a template, and the route values for `controller` and `action` usually appear in that template - this works because the URLs matched by routing adhere to a *convention*.</span></span> <span data-ttu-id="fcc46-818">在属性路由中，`controller` 和 `action` 的路由值不能出现在模板中，它们用于查找要使用的模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-818">In attribute routing, the route values for `controller` and `action` are not allowed to appear in the template - they're instead used to look up which template to use.</span></span>

<span data-ttu-id="fcc46-819">此示例使用属性路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-819">This example uses attribute routing:</span></span>

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

<span data-ttu-id="fcc46-820">MVC 生成一个包含所有属性路由操作的查找表，并匹配 `controller` 和 `action` 的值，以选择要用于生成 URL 的路由模板。</span><span class="sxs-lookup"><span data-stu-id="fcc46-820">MVC builds a lookup table of all attribute routed actions and will match the `controller` and `action` values to select the route template to use for URL generation.</span></span> <span data-ttu-id="fcc46-821">在上述示例中，生成了 `custom/url/to/destination`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-821">In the sample above,   `custom/url/to/destination` is generated.</span></span>

### <a name="generating-urls-by-action-name"></a><span data-ttu-id="fcc46-822">根据操作名称生成 URL</span><span class="sxs-lookup"><span data-stu-id="fcc46-822">Generating URLs by action name</span></span>

<span data-ttu-id="fcc46-823">`Url.Action` (`IUrlHelper` .</span><span class="sxs-lookup"><span data-stu-id="fcc46-823">`Url.Action` (`IUrlHelper` .</span></span> <span data-ttu-id="fcc46-824">`Action`) 以及所有相关重载都基于这样一种想法：用户想通过指定控制器名称和操作名称来指定要链接的内容。</span><span class="sxs-lookup"><span data-stu-id="fcc46-824">`Action`) and all related overloads all are based on that idea that you want to specify what you're linking to by specifying a controller name and action name.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc46-825">使用 `Url.Action` 时，将为用户指定 `controller` 和 `action` 的当前路由值，`controller` 和 `action` 的值是环境值 *和* **值** 的一部分。</span><span class="sxs-lookup"><span data-stu-id="fcc46-825">When using `Url.Action`, the current route values for `controller` and `action` are specified for you - the value of `controller` and `action` are part of both *ambient values* **and** *values*.</span></span> <span data-ttu-id="fcc46-826">`Url.Action` 方法始终使用 `action` 和 `controller` 的当前值，并将生成将路由到当前操作的 URL 路径。</span><span class="sxs-lookup"><span data-stu-id="fcc46-826">The method `Url.Action`, always uses the current values of `action` and `controller` and will generate a URL path that routes to the current action.</span></span>

<span data-ttu-id="fcc46-827">路由尝试使用环境值中的值来填充生成 URL 时未提供的信息。</span><span class="sxs-lookup"><span data-stu-id="fcc46-827">Routing attempts to use the values in ambient values to fill in information that you didn't provide when generating a URL.</span></span> <span data-ttu-id="fcc46-828">通过使用路由（比如 `{a}/{b}/{c}/{d}`）和环境值 `{ a = Alice, b = Bob, c = Carol, d = David }`，路由就具有足够的信息来生成 URL，而无需任何附加值，因为所有路由参数都有值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-828">Using a route like `{a}/{b}/{c}/{d}` and ambient values `{ a = Alice, b = Bob, c = Carol, d = David }`, routing has enough information to generate a URL without any additional values - since all route parameters have a value.</span></span> <span data-ttu-id="fcc46-829">如果添加了值 `{ d = Donovan }`，则会忽略值 `{ d = David }`，生成的 URL 路径将为 `Alice/Bob/Carol/Donovan`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-829">If you added the value `{ d = Donovan }`, the value `{ d = David }` would be ignored, and the generated URL path would be `Alice/Bob/Carol/Donovan`.</span></span>

> [!WARNING]
> <span data-ttu-id="fcc46-830">URL 路径是分层的。</span><span class="sxs-lookup"><span data-stu-id="fcc46-830">URL paths are hierarchical.</span></span> <span data-ttu-id="fcc46-831">在上述示例中，如果添加了值 `{ c = Cheryl }`，则会忽略 `{ c = Carol, d = David }` 这两个值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-831">In the example above, if you added the value `{ c = Cheryl }`, both of the values `{ c = Carol, d = David }` would be ignored.</span></span> <span data-ttu-id="fcc46-832">在这种情况下，`d` 不再具有任何值，URL 生成将失败。</span><span class="sxs-lookup"><span data-stu-id="fcc46-832">In this case we no longer have a value for `d` and URL generation will fail.</span></span> <span data-ttu-id="fcc46-833">用户需要指定 `c` 和 `d` 所需的值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-833">You would need to specify the desired value of `c` and `d`.</span></span>  <span data-ttu-id="fcc46-834">使用默认路由 (`{controller}/{action}/{id?}`) 时可能会遇到此问题，但在实际操作中很少遇到此行为，因为 `Url.Action` 始终显式指定 `controller` 和 `action` 值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-834">You might expect to hit this problem with the default route (`{controller}/{action}/{id?}`) - but you will rarely encounter this behavior in practice as `Url.Action` will always explicitly specify a `controller` and `action` value.</span></span>

<span data-ttu-id="fcc46-835">较长的 `Url.Action` 重载还采用附加 *路由值* 对象，为 `controller` 和 `action` 以外的路由参数提供值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-835">Longer overloads of `Url.Action` also take an additional *route values* object to provide values for route parameters other than `controller` and `action`.</span></span> <span data-ttu-id="fcc46-836">此重载最常与 `id` 结合使用，比如 `Url.Action("Buy", "Products", new { id = 17 })`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-836">You will most commonly see this used with `id` like `Url.Action("Buy", "Products", new { id = 17 })`.</span></span> <span data-ttu-id="fcc46-837">按照惯例，*路由值* 对象通常是匿名类型的对象，但它也可以是 `IDictionary<>` 或 *普通旧 .NET 对象*。</span><span class="sxs-lookup"><span data-stu-id="fcc46-837">By convention the *route values* object is usually an object of anonymous type, but it can also be an `IDictionary<>` or a *plain old .NET object*.</span></span> <span data-ttu-id="fcc46-838">任何与路由参数不匹配的附加路由值都放在查询字符串中。</span><span class="sxs-lookup"><span data-stu-id="fcc46-838">Any additional route values that don't match route parameters are put in the query string.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> <span data-ttu-id="fcc46-839">若要创建绝对 URL，请使用采用 `protocol` 的重载：`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span><span class="sxs-lookup"><span data-stu-id="fcc46-839">To create an absolute URL, use an overload that accepts a `protocol`: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`</span></span>

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a><span data-ttu-id="fcc46-840">根据路由生成 URL</span><span class="sxs-lookup"><span data-stu-id="fcc46-840">Generating URLs by route</span></span>

<span data-ttu-id="fcc46-841">上面的代码演示了如何通过传入控制器和操作名称来生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-841">The code above demonstrated generating a URL by passing in the controller and action name.</span></span> <span data-ttu-id="fcc46-842">`IUrlHelper` 还提供 `Url.RouteUrl` 系列的方法。</span><span class="sxs-lookup"><span data-stu-id="fcc46-842">`IUrlHelper` also provides the `Url.RouteUrl` family of methods.</span></span> <span data-ttu-id="fcc46-843">这些方法类似于 `Url.Action`，但它们不会将 `action` 和 `controller` 的当前值复制到路由值。</span><span class="sxs-lookup"><span data-stu-id="fcc46-843">These methods are similar to `Url.Action`, but they don't copy the current values of `action` and `controller` to the route values.</span></span> <span data-ttu-id="fcc46-844">最常见的用法是指定一个路由名称，以使用特定路由来生成 URL，通常 *不* 指定控制器或操作名称。</span><span class="sxs-lookup"><span data-stu-id="fcc46-844">The most common usage is to specify a route name to use a specific route to generate the URL, generally *without* specifying a controller or action name.</span></span>

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a><span data-ttu-id="fcc46-845">在 HTML 中生成 URL</span><span class="sxs-lookup"><span data-stu-id="fcc46-845">Generating URLs in HTML</span></span>

<span data-ttu-id="fcc46-846">`IHtmlHelper` 提供 `HtmlHelper` 方法 `Html.BeginForm` 和 `Html.ActionLink`，可分别生成 `<form>` 和 `<a>` 元素。</span><span class="sxs-lookup"><span data-stu-id="fcc46-846">`IHtmlHelper` provides the `HtmlHelper` methods `Html.BeginForm` and `Html.ActionLink` to generate `<form>` and `<a>` elements respectively.</span></span> <span data-ttu-id="fcc46-847">这些方法使用 `Url.Action` 方法来生成 URL，并且采用相似的参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-847">These methods use the `Url.Action` method to generate a URL and they accept similar arguments.</span></span> <span data-ttu-id="fcc46-848">`HtmlHelper` 的配套 `Url.RouteUrl` 为 `Html.BeginRouteForm` 和 `Html.RouteLink`，两者具有相似的功能。</span><span class="sxs-lookup"><span data-stu-id="fcc46-848">The `Url.RouteUrl` companions for `HtmlHelper` are `Html.BeginRouteForm` and `Html.RouteLink` which have similar functionality.</span></span>

<span data-ttu-id="fcc46-849">TagHelper 通过 `form` TagHelper 和 `<a>` TagHelper 生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-849">TagHelpers generate URLs through the `form` TagHelper and the `<a>` TagHelper.</span></span> <span data-ttu-id="fcc46-850">两者均通过 `IUrlHelper` 来实现。</span><span class="sxs-lookup"><span data-stu-id="fcc46-850">Both of these use `IUrlHelper` for their implementation.</span></span> <span data-ttu-id="fcc46-851">有关详细信息，请参阅[使用表单](../views/working-with-forms.md)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-851">See [Working with Forms](../views/working-with-forms.md) for more information.</span></span>

<span data-ttu-id="fcc46-852">在视图内，可通过 `Url` 属性将 `IUrlHelper` 用于前文未涵盖的任何临时 URL 生成。</span><span class="sxs-lookup"><span data-stu-id="fcc46-852">Inside views, the `IUrlHelper` is available through the `Url` property for any ad-hoc URL generation not covered by the above.</span></span>

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a><span data-ttu-id="fcc46-853">在操作结果中生成 URL</span><span class="sxs-lookup"><span data-stu-id="fcc46-853">Generating URLS in Action Results</span></span>

<span data-ttu-id="fcc46-854">以上示例展示了如何在控制器中使用 `IUrlHelper`，不过，控制器中最常见的用法是将 URL 生成为操作结果的一部分。</span><span class="sxs-lookup"><span data-stu-id="fcc46-854">The examples above have shown using `IUrlHelper` in a controller, while the most common usage in a controller is to generate a URL as part of an action result.</span></span>

<span data-ttu-id="fcc46-855">`ControllerBase` 和 `Controller` 基类为操作结果提供简便的方法来引用另一项操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-855">The `ControllerBase` and `Controller` base classes provide convenience methods for action results that reference another action.</span></span> <span data-ttu-id="fcc46-856">一种典型用法是在接受用户输入后进行重定向。</span><span class="sxs-lookup"><span data-stu-id="fcc46-856">One typical usage is to redirect after accepting user input.</span></span>

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

<span data-ttu-id="fcc46-857">操作结果工厂方法遵循与 `IUrlHelper` 上的方法类似的模式。</span><span class="sxs-lookup"><span data-stu-id="fcc46-857">The action results factory methods follow a similar pattern to the methods on `IUrlHelper`.</span></span>

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a><span data-ttu-id="fcc46-858">专用传统路由的特殊情况</span><span class="sxs-lookup"><span data-stu-id="fcc46-858">Special case for dedicated conventional routes</span></span>

<span data-ttu-id="fcc46-859">传统路由可以使用一种特殊的路由定义，称为 *专用传统路由*。</span><span class="sxs-lookup"><span data-stu-id="fcc46-859">Conventional routing can use a special kind of route definition called a *dedicated conventional route*.</span></span> <span data-ttu-id="fcc46-860">在下面的示例中，名为 `blog` 的路由是一种专用传统路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-860">In the example below, the route named `blog` is a dedicated conventional route.</span></span>

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

<span data-ttu-id="fcc46-861">使用这些路由定义，`Url.Action("Index", "Home")` 将通过 `default` 路由生成 URL 路径 `/`，但是，为什么会这样？</span><span class="sxs-lookup"><span data-stu-id="fcc46-861">Using these route definitions, `Url.Action("Index", "Home")` will generate the URL path `/` with the `default` route, but why?</span></span> <span data-ttu-id="fcc46-862">用户可能认为使用 `blog`，路由值 `{ controller = Home, action = Index }` 就足以生成 URL，且结果为 `/blog?action=Index&controller=Home`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-862">You might guess the route values `{ controller = Home, action = Index }` would be enough to generate a URL using `blog`, and the result would be `/blog?action=Index&controller=Home`.</span></span>

<span data-ttu-id="fcc46-863">专用传统路由依赖于不具有相应路由参数的默认值的特殊行为，以防止路由在 URL 生成过程中“太贪婪”。</span><span class="sxs-lookup"><span data-stu-id="fcc46-863">Dedicated conventional routes rely on a special behavior of default values that don't have a corresponding route parameter that prevents the route from being "too greedy" with URL generation.</span></span> <span data-ttu-id="fcc46-864">在此例中，默认值是为 `{ controller = Blog, action = Article }`，`controller` 和 `action` 均未显示为路由参数。</span><span class="sxs-lookup"><span data-stu-id="fcc46-864">In this case the default values are `{ controller = Blog, action = Article }`, and neither `controller` nor `action` appears as a route parameter.</span></span> <span data-ttu-id="fcc46-865">当路由执行 URL 生成时，提供的值必须与默认值匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-865">When routing performs URL generation, the values provided must match the default values.</span></span> <span data-ttu-id="fcc46-866">使用 `blog` 的 URL 生成将失败，因为值 `{ controller = Home, action = Index }` 与 `{ controller = Blog, action = Article }` 不匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-866">URL generation using `blog` will fail because the values `{ controller = Home, action = Index }` don't match `{ controller = Blog, action = Article }`.</span></span> <span data-ttu-id="fcc46-867">然后，路由回退，尝试使用 `default`，并最终成功。</span><span class="sxs-lookup"><span data-stu-id="fcc46-867">Routing then falls back to try `default`, which succeeds.</span></span>

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a><span data-ttu-id="fcc46-868">Areas</span><span class="sxs-lookup"><span data-stu-id="fcc46-868">Areas</span></span>

<span data-ttu-id="fcc46-869">[区域](areas.md)是一种 MVC 功能，用于将相关功能整理到一个组中，作为单独的路由命名空间（用于控制器操作）和文件夹结构（用于视图）。</span><span class="sxs-lookup"><span data-stu-id="fcc46-869">[Areas](areas.md) are an MVC feature used to organize related functionality into a group as a separate routing-namespace (for controller actions) and folder structure (for views).</span></span> <span data-ttu-id="fcc46-870">通过使用区域，应用程序可以有多个名称相同的控制器，只要它们具有不同的 *区域*。</span><span class="sxs-lookup"><span data-stu-id="fcc46-870">Using areas allows an application to have multiple controllers with the same name - as long as they have different *areas*.</span></span> <span data-ttu-id="fcc46-871">通过向 `controller` 和 `action` 添加另一个路由参数 `area`，可使用区域为路由创建层次结构。</span><span class="sxs-lookup"><span data-stu-id="fcc46-871">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area` to `controller` and `action`.</span></span> <span data-ttu-id="fcc46-872">此部分将讨论路由如何与区域交互；有关如何将区域与视图结合使用的详细信息，请参阅[区域](areas.md)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-872">This section will discuss how routing interacts with areas - see [Areas](areas.md) for details about how areas are used with views.</span></span>

<span data-ttu-id="fcc46-873">下面的示例将 MVC 配置为使用默认传统路由和 *区域路由*（用于名为 `Blog` 的区域）：</span><span class="sxs-lookup"><span data-stu-id="fcc46-873">The following example configures MVC to use the default conventional route and an *area route* for an area named `Blog`:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

<span data-ttu-id="fcc46-874">与 URL 路径（比如 `/Manage/Users/AddUser`）匹配时，第一个路由将生成路由值 `{ area = Blog, controller = Users, action = AddUser }`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-874">When matching a URL path like `/Manage/Users/AddUser`, the first route will produce the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span> <span data-ttu-id="fcc46-875">`area` 路由值由 `area` 的默认值生成，事实上，通过 `MapAreaRoute` 创建的路由等效于以下路由：</span><span class="sxs-lookup"><span data-stu-id="fcc46-875">The `area` route value is produced by a default value for `area`, in fact the route created by `MapAreaRoute` is equivalent to the following:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

<span data-ttu-id="fcc46-876">`MapAreaRoute` 通过为使用所提供的区域名称（本例中为 `Blog`）的 `area` 提供默认值和约束，来创建路由。</span><span class="sxs-lookup"><span data-stu-id="fcc46-876">`MapAreaRoute` creates a route using both a default value and constraint for `area` using the provided area name, in this case `Blog`.</span></span> <span data-ttu-id="fcc46-877">默认值确保路由始终生成 `{ area = Blog, ... }`，约束要求在生成 URL 时使用值 `{ area = Blog, ... }`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-877">The default value ensures that the route always produces `{ area = Blog, ... }`, the constraint requires the value `{ area = Blog, ... }` for URL generation.</span></span>

> [!TIP]
> <span data-ttu-id="fcc46-878">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="fcc46-878">Conventional routing is order-dependent.</span></span> <span data-ttu-id="fcc46-879">一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。</span><span class="sxs-lookup"><span data-stu-id="fcc46-879">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="fcc46-880">在上面的示例中，路由值将与以下操作匹配：</span><span class="sxs-lookup"><span data-stu-id="fcc46-880">Using the above example, the route values would match the following action:</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

<span data-ttu-id="fcc46-881">`AreaAttribute` 用于将控制器表示为某个区域的一部分，比方说，此控制器位于 `Blog` 区域中。</span><span class="sxs-lookup"><span data-stu-id="fcc46-881">The `AreaAttribute` is what denotes a controller as part of an area, we say that this controller is in the `Blog` area.</span></span> <span data-ttu-id="fcc46-882">没有 `[Area]` 属性的控制器不是任何区域的成员，在路由提供 `area` 路由值时 **不** 匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-882">Controllers without an `[Area]` attribute are not members of any area, and will **not** match when the `area` route value is provided by routing.</span></span> <span data-ttu-id="fcc46-883">在下面的示例中，只有所列出的第一个控制器才能与路由值 `{ area = Blog, controller = Users, action = AddUser }` 匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-883">In the following example, only the first controller listed can match the route values `{ area = Blog, controller = Users, action = AddUser }`.</span></span>

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> <span data-ttu-id="fcc46-884">出于完整性考虑，此处显示了每个控制器的命名空间，否则，控制器会发生命名冲突并生成编译器错误。</span><span class="sxs-lookup"><span data-stu-id="fcc46-884">The namespace of each controller is shown here for completeness - otherwise the controllers would have a naming conflict and generate a compiler error.</span></span> <span data-ttu-id="fcc46-885">类命名空间对 MVC 的路由没有影响。</span><span class="sxs-lookup"><span data-stu-id="fcc46-885">Class namespaces have no effect on MVC's routing.</span></span>

<span data-ttu-id="fcc46-886">前两个控制器是区域成员，仅在 `area` 路由值提供其各自的区域名称时匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-886">The first two controllers are members of areas, and only match when their respective area name is provided by the `area` route value.</span></span> <span data-ttu-id="fcc46-887">第三个控制器不是任何区域的成员，只能在路由没有为 `area` 提供任何值时匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-887">The third controller isn't a member of any area, and can only match when no value for `area` is provided by routing.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc46-888">就 *不匹配任何值* 而言，缺少 `area` 值相当于 `area` 的值为 NULL 或空字符串。</span><span class="sxs-lookup"><span data-stu-id="fcc46-888">In terms of matching *no value*, the absence of the `area` value is the same as if the value for `area` were null or the empty string.</span></span>

<span data-ttu-id="fcc46-889">在某个区域内执行某项操作时，`area` 的路由值将以 *环境值* 的形式提供，以便路由用于生成 URL。</span><span class="sxs-lookup"><span data-stu-id="fcc46-889">When executing an action inside an area, the route value for `area` will be available as an *ambient value* for routing to use for URL generation.</span></span> <span data-ttu-id="fcc46-890">这意味着默认情况下，区域在 URL 生成中具有 *粘性*，如以下示例所示。</span><span class="sxs-lookup"><span data-stu-id="fcc46-890">This means that by default areas act *sticky* for URL generation as demonstrated by the following sample.</span></span>
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a><span data-ttu-id="fcc46-891">了解 IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="fcc46-891">Understanding IActionConstraint</span></span>

> [!NOTE]
> <span data-ttu-id="fcc46-892">此部分深入介绍框架内部结构以及 MVC 如何选择要执行的操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-892">This section is a deep-dive on framework internals and how MVC chooses an action to execute.</span></span> <span data-ttu-id="fcc46-893">典型的应用程序不需要自定义 `IActionConstraint`</span><span class="sxs-lookup"><span data-stu-id="fcc46-893">A typical application won't need a custom `IActionConstraint`</span></span>

<span data-ttu-id="fcc46-894">即使不熟悉 `IActionConstraint`，也可能已经用过该接口。</span><span class="sxs-lookup"><span data-stu-id="fcc46-894">You have likely already used `IActionConstraint` even if you're not familiar with the interface.</span></span> <span data-ttu-id="fcc46-895">`[HttpGet]` 属性和类似的 `[Http-VERB]` 属性可实现 `IActionConstraint` 来限制操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="fcc46-895">The `[HttpGet]` Attribute and similar `[Http-VERB]` attributes implement `IActionConstraint` in order to limit the execution of an action method.</span></span>

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

<span data-ttu-id="fcc46-896">假定使用默认传统路由，URL 路径 `/Products/Edit` 将生成值 `{ controller = Products, action = Edit }`，这将匹配此处所示的 **两项** 操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-896">Assuming the default conventional route, the URL path `/Products/Edit` would produce the values `{ controller = Products, action = Edit }`, which would match **both** of the actions shown here.</span></span> <span data-ttu-id="fcc46-897">在 `IActionConstraint` 术语中，我们会说，这两项操作都被视为候选项，因为它们都与该路由数据匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-897">In `IActionConstraint` terminology we would say that both of these actions are considered candidates - as they both match the route data.</span></span>

<span data-ttu-id="fcc46-898">当 `HttpGetAttribute` 执行时，它认为 *Edit()* 是 *GET* 的匹配项，而不是任何其他 Http 谓词的匹配项。</span><span class="sxs-lookup"><span data-stu-id="fcc46-898">When the `HttpGetAttribute` executes, it will say that *Edit()* is a match for *GET* and isn't a match for any other HTTP verb.</span></span> <span data-ttu-id="fcc46-899">`Edit(...)` 操作未定义任何约束，因此将匹配任何 Http 谓词。</span><span class="sxs-lookup"><span data-stu-id="fcc46-899">The `Edit(...)` action doesn't have any constraints defined, and so will match any HTTP verb.</span></span> <span data-ttu-id="fcc46-900">因此，假定 Http 谓词为 `POST`，则仅 `Edit(...)` 匹配。</span><span class="sxs-lookup"><span data-stu-id="fcc46-900">So assuming a `POST` - only `Edit(...)` matches.</span></span> <span data-ttu-id="fcc46-901">不过，对于 `GET`，这两项操作仍然都能匹配，只是具有 `IActionConstraint` 的操作始终被认为比没有该接口的操作 *更匹配*。</span><span class="sxs-lookup"><span data-stu-id="fcc46-901">But, for a `GET` both actions can still match - however, an action with an `IActionConstraint` is always considered *better* than an action without.</span></span> <span data-ttu-id="fcc46-902">因此，由于 `Edit()` 具有 `[HttpGet]`，则认为它更特定，在两项操作都能匹配的情况将选择它。</span><span class="sxs-lookup"><span data-stu-id="fcc46-902">So because `Edit()` has `[HttpGet]` it's considered more specific, and will be selected if both actions can match.</span></span>

<span data-ttu-id="fcc46-903">从概念上讲，`IActionConstraint` 是一种 *重载* 形式，但它并不重载具有相同名称的方法，而在匹配相同 URL 的操作之间重载。</span><span class="sxs-lookup"><span data-stu-id="fcc46-903">Conceptually, `IActionConstraint` is a form of *overloading*, but instead of overloading methods with the same name, it's overloading between actions that match the same URL.</span></span> <span data-ttu-id="fcc46-904">属性路由也使用 `IActionConstraint`，这可能会导致将不同控制器中的操作都视为候选项。</span><span class="sxs-lookup"><span data-stu-id="fcc46-904">Attribute routing also uses `IActionConstraint` and can result in actions from different controllers both being considered candidates.</span></span>

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a><span data-ttu-id="fcc46-905">实现 IActionConstraint</span><span class="sxs-lookup"><span data-stu-id="fcc46-905">Implementing IActionConstraint</span></span>

<span data-ttu-id="fcc46-906">实现 `IActionConstraint` 最简单的方法是创建派生自 `System.Attribute` 的类，并将其置于操作和控制器上。</span><span class="sxs-lookup"><span data-stu-id="fcc46-906">The simplest way to implement an `IActionConstraint` is to create a class derived from `System.Attribute` and place it on your actions and controllers.</span></span> <span data-ttu-id="fcc46-907">MVC 将自动发现任何应用为属性的 `IActionConstraint`。</span><span class="sxs-lookup"><span data-stu-id="fcc46-907">MVC will automatically discover any `IActionConstraint` that are applied as attributes.</span></span> <span data-ttu-id="fcc46-908">可使用应用程序模型应用约束，这可能是最灵活的一种方法，因为它允许对其应用方式进行元编程。</span><span class="sxs-lookup"><span data-stu-id="fcc46-908">You can use the application model to apply constraints, and this is probably the most flexible approach as it allows you to metaprogram how they're applied.</span></span>

<span data-ttu-id="fcc46-909">在下面的示例中，约束基于路由数据中的 *国家/地区代码* 选择操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-909">In the following example, a constraint chooses an action based on a *country code* from the route data.</span></span> <span data-ttu-id="fcc46-910">[GitHub 上的完整示例](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)。</span><span class="sxs-lookup"><span data-stu-id="fcc46-910">The [full sample on GitHub](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs).</span></span>

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

<span data-ttu-id="fcc46-911">用户负责实现 `Accept` 方法，并为要执行的约束选择“顺序”。</span><span class="sxs-lookup"><span data-stu-id="fcc46-911">You are responsible for implementing the `Accept` method and choosing an 'Order' for the constraint to execute.</span></span> <span data-ttu-id="fcc46-912">在此例中，当 `country` 路由值匹配时，`Accept` 方法返回 `true` 以表示该操作是匹配项。</span><span class="sxs-lookup"><span data-stu-id="fcc46-912">In this case, the `Accept` method returns `true` to denote the action is a match when the `country` route value matches.</span></span> <span data-ttu-id="fcc46-913">它与 `RouteValueAttribute` 的不同之处在于，它允许回退到非属性化操作。</span><span class="sxs-lookup"><span data-stu-id="fcc46-913">This is different from a `RouteValueAttribute` in that it allows fallback to a non-attributed action.</span></span> <span data-ttu-id="fcc46-914">通过该示例可以了解到，如果定义 `en-US` 操作，则像 `fr-FR` 这样的国家/地区代码将回退到一个未应用 `[CountrySpecific(...)]` 的较通用的控制器。</span><span class="sxs-lookup"><span data-stu-id="fcc46-914">The sample shows that if you define an `en-US` action then a country code like `fr-FR` will fall back to a more generic controller that doesn't have `[CountrySpecific(...)]` applied.</span></span>

<span data-ttu-id="fcc46-915">`Order` 属性决定约束所属的 *阶段*。</span><span class="sxs-lookup"><span data-stu-id="fcc46-915">The `Order` property decides which *stage* the constraint is part of.</span></span> <span data-ttu-id="fcc46-916">操作约束基于 `Order` 分组运行。</span><span class="sxs-lookup"><span data-stu-id="fcc46-916">Action constraints run in groups based on the `Order`.</span></span> <span data-ttu-id="fcc46-917">例如，该框架提供的所有 HTTP 方法属性均使用相同的 `Order` 值，以便在相同的阶段运行。</span><span class="sxs-lookup"><span data-stu-id="fcc46-917">For example, all of the framework provided HTTP method attributes use the same `Order` value so that they run in the same stage.</span></span> <span data-ttu-id="fcc46-918">用户可以按需设置阶段数来实现所需的策略。</span><span class="sxs-lookup"><span data-stu-id="fcc46-918">You can have as many stages as you need to implement your desired policies.</span></span>

> [!TIP]
> <span data-ttu-id="fcc46-919">若要确定 `Order` 的值，请考虑是否应在 HTTP 方法前应用约束。</span><span class="sxs-lookup"><span data-stu-id="fcc46-919">To decide on a value for `Order` think about whether or not your constraint should be applied before HTTP methods.</span></span> <span data-ttu-id="fcc46-920">数值较低的先运行。</span><span class="sxs-lookup"><span data-stu-id="fcc46-920">Lower numbers run first.</span></span>

::: moniker-end
