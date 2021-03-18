---
title: 预呈现和集成 ASP.NET Core Razor 组件
author: guardrex
description: 了解 Blazor 应用的 Razor 组件集成方案，包括在服务器上预呈现 Razor 组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: a0c5cc0bdc78f2ea70b8c128616ad09328ccf87d
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102587380"
---
# <a name="prerender-and-integrate-aspnet-core-razor-components"></a><span data-ttu-id="1983d-103">预呈现和集成 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="1983d-103">Prerender and integrate ASP.NET Core Razor components</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1983d-104">Razor 组件可以通过托管的 Blazor WebAssembly 解决方案集成到 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="1983d-104">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="1983d-105">呈现页面或视图时，可以同时预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-105">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="1983d-106">Configuration</span><span class="sxs-lookup"><span data-stu-id="1983d-106">Configuration</span></span>

<span data-ttu-id="1983d-107">设置 Blazor WebAssembly 应用的预呈现：</span><span class="sxs-lookup"><span data-stu-id="1983d-107">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="1983d-108">将 Blazor WebAssembly 应用托管在 ASP.NET Core 应用中。</span><span class="sxs-lookup"><span data-stu-id="1983d-108">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="1983d-109">可以将独立的 Blazor WebAssembly 应用添加到 ASP.NET Core 解决方案中，也可以使用根据 [Blazor WebAssembly 项目模板](xref:blazor/project-structure)创建的托管 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="1983d-109">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the [Blazor WebAssembly project template](xref:blazor/project-structure).</span></span>

1. <span data-ttu-id="1983d-110">从 Blazor WebAssembly 客户端项目中删除默认的静态 `wwwroot/index.html` 文件。</span><span class="sxs-lookup"><span data-stu-id="1983d-110">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="1983d-111">删除客户端项目的 `Program.Main` 中的以下行：</span><span class="sxs-lookup"><span data-stu-id="1983d-111">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="1983d-112">将 `Pages/_Host.cshtml` 文件添加到服务器项目。</span><span class="sxs-lookup"><span data-stu-id="1983d-112">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="1983d-113">可以使用命令行界面中的 `dotnet new blazorserver -o BlazorServer` 命令获得 Blazor Server 模板创建的应用的 `_Host.cshtml` 文件。</span><span class="sxs-lookup"><span data-stu-id="1983d-113">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="1983d-114">将 `Pages/_Host.cshtml` 文件放入托管 Blazor WebAssembly 解决方案的服务器应用后，对此文件进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="1983d-114">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="1983d-115">将命名空间设置为服务器应用的 `Pages` 文件夹（例如，`@namespace BlazorHosted.Server.Pages`）。</span><span class="sxs-lookup"><span data-stu-id="1983d-115">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="1983d-116">为客户端项目设置 [`@using`](xref:mvc/views/razor#using) 指令（例如 `@using BlazorHosted.Client`）。</span><span class="sxs-lookup"><span data-stu-id="1983d-116">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="1983d-117">更新样式表链接，以指向 WebAssembly 应用的样式表。</span><span class="sxs-lookup"><span data-stu-id="1983d-117">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="1983d-118">在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：</span><span class="sxs-lookup"><span data-stu-id="1983d-118">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="1983d-119">更新[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)的 `render-mode`，以预呈现根 `App` 组件：</span><span class="sxs-lookup"><span data-stu-id="1983d-119">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="1983d-120">更新 Blazor 脚本源，以使用客户端 Blazor WebAssembly 脚本：</span><span class="sxs-lookup"><span data-stu-id="1983d-120">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="1983d-121">在服务器项目的 `Startup.Configure` (`Startup.cs`) 中：</span><span class="sxs-lookup"><span data-stu-id="1983d-121">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="1983d-122">在开发环境中从应用程序生成器上调用 `UseDeveloperExceptionPage`。</span><span class="sxs-lookup"><span data-stu-id="1983d-122">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="1983d-123">在应用程序生成器上调用 `UseBlazorFrameworkFiles`。</span><span class="sxs-lookup"><span data-stu-id="1983d-123">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="1983d-124">将回退从 `index.html` 文件 (`endpoints.MapFallbackToFile("index.html");`) 更改为 `_Host.cshtml` 页面：`endpoints.MapFallbackToPage("/_Host");`。</span><span class="sxs-lookup"><span data-stu-id="1983d-124">Change the fallback from the `index.html` file (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page: `endpoints.MapFallbackToPage("/_Host");`.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="1983d-125">使用组件标记帮助程序，通过页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="1983d-125">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="1983d-126">组件标记帮助程序支持两种呈现模式来通过页面或视图呈现 Blazor WebAssembly 应用中的组件：</span><span class="sxs-lookup"><span data-stu-id="1983d-126">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="1983d-127">`WebAssembly`：呈现 Blazor WebAssembly 应用程序的标记，以便在浏览器中进行加载时包含交互式组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-127">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="1983d-128">不预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-128">The component isn't prerendered.</span></span> <span data-ttu-id="1983d-129">通过此选项，可以在不同的页面轻松呈现各种 Blazor WebAssembly 组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-129">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="1983d-130">`WebAssemblyPrerendered`：在静态 HTML 中预呈现组件，并包含 Blazor WebAssembly 应用的标记，以便稍后在浏览器中进行加载时使组件成为交互式组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-130">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="1983d-131">在下面的 Razor Pages 示例中，页面中呈现了 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-131">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="1983d-132">页面的[呈现部分](xref:mvc/views/layout#sections)包含了 Blazor WebAssembly 脚本，以使组件成为交互式组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-132">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="1983d-133">为客户端应用的 `Pages` 命名空间添加 [`@using`](xref:mvc/views/razor#using) 指令，以避免将 `Counter` 组件的整个命名空间用于组件标记帮助程序 (`{APP ASSEMBLY}.Pages.Counter`)。</span><span class="sxs-lookup"><span data-stu-id="1983d-133">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="1983d-134">在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：</span><span class="sxs-lookup"><span data-stu-id="1983d-134">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="1983d-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="1983d-135"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="1983d-136">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="1983d-136">Is prerendered into the page.</span></span>
* <span data-ttu-id="1983d-137">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="1983d-137">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="1983d-138">有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="1983d-138">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="1983d-139">前面的示例要求服务器应用的布局 (`_Layout.cshtml`) 在 `</body>` 结束标记内包含脚本的[呈现部分](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>)：</span><span class="sxs-lookup"><span data-stu-id="1983d-139">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="1983d-140">`_Layout.cshtml` 文件位于 Razor Pages 应用的 `Pages/Shared` 文件夹中，或位于 MVC 应用的 `Views/Shared` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1983d-140">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="1983d-141">如果应用程序还应使用 Blazor WebAssembly 应用中的样式设置组件样式，则将此应用的样式包含在 `_Layout.cshtml` 文件中。</span><span class="sxs-lookup"><span data-stu-id="1983d-141">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="1983d-142">在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：</span><span class="sxs-lookup"><span data-stu-id="1983d-142">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="1983d-143">使用 CSS 选择器，通过页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="1983d-143">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="1983d-144">将根组件添加到 `Program.Main` (`Program.cs`) 中的“客户端”项目中。</span><span class="sxs-lookup"><span data-stu-id="1983d-144">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="1983d-145">下面的示例使用 CSS 选择器将 `Counter` 组件声明为根组件，该选择器会选择 `id` 与 `my-counter` 匹配的元素。</span><span class="sxs-lookup"><span data-stu-id="1983d-145">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="1983d-146">在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：</span><span class="sxs-lookup"><span data-stu-id="1983d-146">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="1983d-147">在下面的 Razor Pages 示例中，页面中呈现了 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-147">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="1983d-148">页面的[呈现部分](xref:mvc/views/layout#sections)包含了 Blazor WebAssembly 脚本，以使组件成为交互式组件：</span><span class="sxs-lookup"><span data-stu-id="1983d-148">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="1983d-149">前面的示例要求服务器应用的布局 (`_Layout.cshtml`) 在 `</body>` 结束标记内包含脚本的[呈现部分](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>)：</span><span class="sxs-lookup"><span data-stu-id="1983d-149">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="1983d-150">`_Layout.cshtml` 文件位于 Razor Pages 应用的 `Pages/Shared` 文件夹中，或位于 MVC 应用的 `Views/Shared` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1983d-150">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="1983d-151">如果应用程序还应使用 Blazor WebAssembly 应用中的样式设置组件样式，则将此应用的样式包含在 `_Layout.cshtml` 文件中。</span><span class="sxs-lookup"><span data-stu-id="1983d-151">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="1983d-152">在下面的示例中，客户端应用的命名空间为 `BlazorHosted.Client`：</span><span class="sxs-lookup"><span data-stu-id="1983d-152">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="additional-resources"></a><span data-ttu-id="1983d-153">其他资源</span><span class="sxs-lookup"><span data-stu-id="1983d-153">Additional resources</span></span>

* [<span data-ttu-id="1983d-154">支持预呈现身份验证</span><span class="sxs-lookup"><span data-stu-id="1983d-154">Support prerendering with authentication</span></span>](xref:blazor/security/webassembly/additional-scenarios#support-prerendering-with-authentication)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="1983d-155">.NET 5 或更高版本中的 ASP.NET Core 支持通过托管的 Blazor WebAssembly 解决方案将 Razor 组件集成到 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="1983d-155">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="1983d-156">请选择本文的 .NET 5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="1983d-156">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="1983d-157">Razor 组件可以通过 Blazor Server 应用集成到 Razor Pages 和 MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="1983d-157">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="1983d-158">呈现页面或视图时，可以同时预呈现组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-158">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="1983d-159">[配置此应用](#configuration)后，根据应用的要求按照下列部分中的指南操作：</span><span class="sxs-lookup"><span data-stu-id="1983d-159">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="1983d-160">可路由组件：针对可直接通过用户请求进行路由的组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-160">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="1983d-161">如果访问者应该能够使用 [`@page`](xref:mvc/views/razor#page) 指令在浏览器中为组件发出 HTTP 请求，则按照此指南操作。</span><span class="sxs-lookup"><span data-stu-id="1983d-161">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="1983d-162">在 Razor Pages 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="1983d-162">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="1983d-163">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="1983d-163">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="1983d-164">[从页面或视图呈现组件](#render-components-from-a-page-or-view)：针对无法直接通过用户请求进行路由的组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-164">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="1983d-165">如果应用使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)将组件嵌入到现有页面和视图，则按照此指南操作。</span><span class="sxs-lookup"><span data-stu-id="1983d-165">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="1983d-166">Configuration</span><span class="sxs-lookup"><span data-stu-id="1983d-166">Configuration</span></span>

<span data-ttu-id="1983d-167">现有 Razor Pages 或 MVC 应用可以将 Razor 组件集成到页面和视图中：</span><span class="sxs-lookup"><span data-stu-id="1983d-167">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="1983d-168">在应用的布局文件 (`_Layout.cshtml`) 中：</span><span class="sxs-lookup"><span data-stu-id="1983d-168">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="1983d-169">将以下 `<base>` 标记添加到 `<head>` 元素：</span><span class="sxs-lookup"><span data-stu-id="1983d-169">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="1983d-170">前面示例中的 `href` 值（应用基路径）假设应用驻留在根 URL 路径 (`/`) 处。</span><span class="sxs-lookup"><span data-stu-id="1983d-170">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="1983d-171">如果应用是子应用程序，请按照 <xref:blazor/host-and-deploy/index#app-base-path> 一文的“应用基路径”部分中的指导进行操作。</span><span class="sxs-lookup"><span data-stu-id="1983d-171">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="1983d-172">`_Layout.cshtml` 文件位于 Razor Pages 应用的 `Pages/Shared` 文件夹中，或位于 MVC 应用的 `Views/Shared` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1983d-172">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="1983d-173">在紧接着 `Scripts` 呈现部分的前方为 `blazor.server.js` 脚本添加 `<script>` 标记：</span><span class="sxs-lookup"><span data-stu-id="1983d-173">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="1983d-174">框架会将 `blazor.server.js` 脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="1983d-174">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="1983d-175">无需手动将脚本添加到应用。</span><span class="sxs-lookup"><span data-stu-id="1983d-175">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="1983d-176">将 `_Imports.razor` 文件添加到项目的根文件夹，其中包含以下内容（将最后一个命名空间 `MyAppNamespace` 更改为应用的命名空间）：</span><span class="sxs-lookup"><span data-stu-id="1983d-176">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="1983d-177">在 `Startup.ConfigureServices`中，注册 Blazor Server 服务：</span><span class="sxs-lookup"><span data-stu-id="1983d-177">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="1983d-178">在 `Startup.Configure` 中，将 Blazor 中心终结点添加到 `app.UseEndpoints`：</span><span class="sxs-lookup"><span data-stu-id="1983d-178">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="1983d-179">将组件集成到任何页面或视图。</span><span class="sxs-lookup"><span data-stu-id="1983d-179">Integrate components into any page or view.</span></span> <span data-ttu-id="1983d-180">有关详细信息，请参阅[从页面或视图呈现组件](#render-components-from-a-page-or-view)部分。</span><span class="sxs-lookup"><span data-stu-id="1983d-180">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-razor-pages-app"></a><span data-ttu-id="1983d-181">在 Razor Pages 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="1983d-181">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="1983d-182">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-182">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="1983d-183">在 Razor Pages 应用中支持可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="1983d-183">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="1983d-184">按照[配置](#configuration)部分中的指南操作。</span><span class="sxs-lookup"><span data-stu-id="1983d-184">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="1983d-185">将具有以下内容的 `App.razor` 文件添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="1983d-185">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="1983d-186">将具有以下内容的 `_Host.cshtml` 文件添加到 `Pages` 文件夹：</span><span class="sxs-lookup"><span data-stu-id="1983d-186">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="1983d-187">组件将共享 `_Layout.cshtml` 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="1983d-187">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="1983d-188"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：</span><span class="sxs-lookup"><span data-stu-id="1983d-188"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="1983d-189">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="1983d-189">Is prerendered into the page.</span></span>
   * <span data-ttu-id="1983d-190">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="1983d-190">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="1983d-191">有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="1983d-191">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="1983d-192">在 `Startup.Configure` 中，将 `_Host.cshtml` 页的低优先级路由添加到终结点配置：</span><span class="sxs-lookup"><span data-stu-id="1983d-192">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="1983d-193">将可路由组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="1983d-193">Add routable components to the app.</span></span> <span data-ttu-id="1983d-194">例如：</span><span class="sxs-lookup"><span data-stu-id="1983d-194">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="1983d-195">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="1983d-195">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="1983d-196">在 MVC 应用中使用可路由组件</span><span class="sxs-lookup"><span data-stu-id="1983d-196">Use routable components in an MVC app</span></span>

<span data-ttu-id="1983d-197">本部分介绍如何添加可直接从用户请求路由的组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-197">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="1983d-198">在 MVC 应用中支持可路由 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="1983d-198">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="1983d-199">按照[配置](#configuration)部分中的指南操作。</span><span class="sxs-lookup"><span data-stu-id="1983d-199">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="1983d-200">将具有以下内容的 `App.razor` 文件添加到项目根：</span><span class="sxs-lookup"><span data-stu-id="1983d-200">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="1983d-201">将具有以下内容的 `_Host.cshtml` 文件添加到 `Views/Home` 文件夹：</span><span class="sxs-lookup"><span data-stu-id="1983d-201">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="1983d-202">组件将共享 `_Layout.cshtml` 文件用于布局。</span><span class="sxs-lookup"><span data-stu-id="1983d-202">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="1983d-203"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 会配置 `App` 组件是否：</span><span class="sxs-lookup"><span data-stu-id="1983d-203"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="1983d-204">在页面中预呈现。</span><span class="sxs-lookup"><span data-stu-id="1983d-204">Is prerendered into the page.</span></span>
   * <span data-ttu-id="1983d-205">在页面上呈现为静态 HTML，或者包含从用户代理启动 Blazor 应用所需的信息。</span><span class="sxs-lookup"><span data-stu-id="1983d-205">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="1983d-206">有关组件标记帮助程序的详细信息（包括传递参数和 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> 配置），请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="1983d-206">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="1983d-207">向主控制器添加操作：</span><span class="sxs-lookup"><span data-stu-id="1983d-207">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="1983d-208">在 `Startup.Configure` 中，将返回 `_Host.cshtml` 视图的控制器操作的低优先级路由添加到终结点配置：</span><span class="sxs-lookup"><span data-stu-id="1983d-208">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="1983d-209">创建 `Pages` 文件夹并将可路由组件添加到应用。</span><span class="sxs-lookup"><span data-stu-id="1983d-209">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="1983d-210">例如：</span><span class="sxs-lookup"><span data-stu-id="1983d-210">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="1983d-211">有关命名空间的详细信息，请参阅[组件命名空间](#component-namespaces)部分。</span><span class="sxs-lookup"><span data-stu-id="1983d-211">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="1983d-212">从页面或视图呈现组件</span><span class="sxs-lookup"><span data-stu-id="1983d-212">Render components from a page or view</span></span>

<span data-ttu-id="1983d-213">本部分介绍如何在无法从用户请求直接路由组件的情况下，将组件添加到页面或视图。</span><span class="sxs-lookup"><span data-stu-id="1983d-213">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="1983d-214">若要从页面或视图呈现组件，请使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="1983d-214">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="1983d-215">呈现有状态交互式组件</span><span class="sxs-lookup"><span data-stu-id="1983d-215">Render stateful interactive components</span></span>

<span data-ttu-id="1983d-216">可以将有状态的交互式组件添加到 Razor 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="1983d-216">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="1983d-217">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="1983d-217">When the page or view renders:</span></span>

* <span data-ttu-id="1983d-218">该组件通过页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="1983d-218">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="1983d-219">用于预呈现的初始组件状态丢失。</span><span class="sxs-lookup"><span data-stu-id="1983d-219">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="1983d-220">建立 SignalR 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="1983d-220">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="1983d-221">以下 Razor 页面将呈现 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="1983d-221">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="1983d-222">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="1983d-222">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="1983d-223">呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="1983d-223">Render noninteractive components</span></span>

<span data-ttu-id="1983d-224">在以下 Razor 页面中，使用以下格式通过指定的初始值静态呈现 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="1983d-224">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="1983d-225">由于该组件是以静态方式呈现的，因此它不是交互式组件：</span><span class="sxs-lookup"><span data-stu-id="1983d-225">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="1983d-226">有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>。</span><span class="sxs-lookup"><span data-stu-id="1983d-226">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="1983d-227">组件命名空间</span><span class="sxs-lookup"><span data-stu-id="1983d-227">Component namespaces</span></span>

<span data-ttu-id="1983d-228">使用自定义文件夹保存应用的组件时，将表示文件夹的命名空间添加到页面/视图或 `_ViewImports.cshtml` 文件。</span><span class="sxs-lookup"><span data-stu-id="1983d-228">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="1983d-229">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="1983d-229">In the following example:</span></span>

* <span data-ttu-id="1983d-230">将 `MyAppNamespace` 更改为应用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="1983d-230">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="1983d-231">如果不使用名为 `Components` 的文件夹来保存组件，请将 `Components` 更改为组件所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="1983d-231">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="1983d-232">`_ViewImports.cshtml` 文件位于 Razor Pages 应用的 `Pages` 文件夹中，或是 MVC 应用的 `Views` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1983d-232">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="1983d-233">有关详细信息，请参阅 <xref:blazor/components/index#namespaces>。</span><span class="sxs-lookup"><span data-stu-id="1983d-233">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
