---
title: 第 3 部分，已搭建基架的 Razor 页面
author: rick-anderson
description: Razor 页面教程系列的第 3 部分。
ms.author: riande
ms.date: 09/25/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/page
ms.openlocfilehash: 049e7764766a4d5d535f7d7959a3554b040607c5
ms.sourcegitcommit: 54fe1ae5e7d068e27376d562183ef9ddc7afc432
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102588498"
---
# <a name="part-3-scaffolded-razor-pages-in-aspnet-core"></a><span data-ttu-id="c0b66-103">第 3 部分，ASP.NET Core 中已搭建基架的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="c0b66-103">Part 3, scaffolded Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="c0b66-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c0b66-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c0b66-105">本教程介绍[上一教程](xref:tutorials/razor-pages/model)中通过搭建基架创建的 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="c0b66-105">This tutorial examines the Razor Pages created by scaffolding in the [previous tutorial](xref:tutorials/razor-pages/model).</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="c0b66-106">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="c0b66-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="c0b66-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="c0b66-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="c0b66-108">“创建”、“删除”、“详细信息”和“编辑”页面</span><span class="sxs-lookup"><span data-stu-id="c0b66-108">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="c0b66-109">检查 Pages/Movies/Index.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="c0b66-109">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="c0b66-110">Razor 页面派生自 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="c0b66-110">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="c0b66-111">按照约定，`PageModel` 派生的类称为 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="c0b66-111">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="c0b66-112">此构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将 `RazorPagesMovieContext` 添加到页面：</span><span class="sxs-lookup"><span data-stu-id="c0b66-112">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml.cs?name=snippet1&highlight=5)]

<span data-ttu-id="c0b66-113">请参阅[异步代码](xref:data/ef-rp/intro#asynchronous-code)，了解有关使用实体框架的异步编程的详细信息。</span><span class="sxs-lookup"><span data-stu-id="c0b66-113">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="c0b66-114">对页面发出请求时，`OnGetAsync` 方法向 Razor 页面返回影片列表。</span><span class="sxs-lookup"><span data-stu-id="c0b66-114">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="c0b66-115">`OnGetAsync` 或 `OnGet` 在 Razor 页面上调用，以初始化该页面的状态。</span><span class="sxs-lookup"><span data-stu-id="c0b66-115">On a Razor Page, `OnGetAsync` or `OnGet` is called to initialize the state of the page.</span></span> <span data-ttu-id="c0b66-116">在这种情况下，`OnGetAsync` 将获得影片列表并显示出来。</span><span class="sxs-lookup"><span data-stu-id="c0b66-116">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="c0b66-117">当 `OnGet` 返回 `void` 或 `OnGetAsync` 返回 `Task` 时，不使用任何返回语句。</span><span class="sxs-lookup"><span data-stu-id="c0b66-117">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return statement is used.</span></span> <span data-ttu-id="c0b66-118">例如“隐私”页：</span><span class="sxs-lookup"><span data-stu-id="c0b66-118">For example the Privacy Page:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="c0b66-119">当返回类型是 `IActionResult` 或 `Task<IActionResult>` 时，必须提供返回语句。</span><span class="sxs-lookup"><span data-stu-id="c0b66-119">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="c0b66-120">例如，Pages/Movies/Create.cshtml.cs `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="c0b66-120">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="c0b66-121">检查 Pages/Movies/Index.cshtml Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="c0b66-121">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml)]

<span data-ttu-id="c0b66-122">Razor 可以从 HTML 转换为 C# 或 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="c0b66-122">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="c0b66-123">当 `@` 符号后跟 [Razor 保留关键字](xref:mvc/views/razor#razor-reserved-keywords)时，它会转换为 Razor 特定标记，否则会转换为 C#。</span><span class="sxs-lookup"><span data-stu-id="c0b66-123">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

### <a name="the-page-directive"></a><span data-ttu-id="c0b66-124">@page 指令</span><span class="sxs-lookup"><span data-stu-id="c0b66-124">The @page directive</span></span>

<span data-ttu-id="c0b66-125">`@page` Razor 指令将文件转换为一个 MVC 操作，这意味着它可以处理请求。</span><span class="sxs-lookup"><span data-stu-id="c0b66-125">The `@page` Razor directive makes the file an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="c0b66-126">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="c0b66-126">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="c0b66-127">`@page` 和 `@model` 是转换为 Razor 特定标记的示例。</span><span class="sxs-lookup"><span data-stu-id="c0b66-127">`@page` and `@model` are examples of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="c0b66-128">有关详细信息，请参阅 [Razor 语法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-128">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="c0b66-129">@model 指令</span><span class="sxs-lookup"><span data-stu-id="c0b66-129">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="c0b66-130">`@model` 指令指定传递到 Razor 页面的模型类型。</span><span class="sxs-lookup"><span data-stu-id="c0b66-130">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="c0b66-131">在前面的示例中，`@model` 行使 `PageModel` 派生的类可用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="c0b66-131">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="c0b66-132">在页面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 帮助程序](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)中使用该模型。</span><span class="sxs-lookup"><span data-stu-id="c0b66-132">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>


<span data-ttu-id="c0b66-133">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="c0b66-133">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="c0b66-134"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="c0b66-134">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="c0b66-135">检查 Lambda 表达式（而非求值）。</span><span class="sxs-lookup"><span data-stu-id="c0b66-135">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="c0b66-136">这意味着当 `model`、`model.Movie` 或 `model.Movie[0]` 为 `null` 或为空时，不会存在任何访问冲突。</span><span class="sxs-lookup"><span data-stu-id="c0b66-136">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` is `null` or empty.</span></span> <span data-ttu-id="c0b66-137">对 Lambda 表达式求值时（例如，使用 `@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="c0b66-137">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>

### <a name="the-layout-page"></a><span data-ttu-id="c0b66-138">布局页</span><span class="sxs-lookup"><span data-stu-id="c0b66-138">The layout page</span></span>

<span data-ttu-id="c0b66-139">选择菜单链接（“Razor PagesMovie”、“主页”和“隐私”）  。</span><span class="sxs-lookup"><span data-stu-id="c0b66-139">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="c0b66-140">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="c0b66-140">Each page shows the same menu layout.</span></span> <span data-ttu-id="c0b66-141">菜单布局是在 Pages/Shared/_Layout.cshtml 文件中实现。</span><span class="sxs-lookup"><span data-stu-id="c0b66-141">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="c0b66-142">打开并检查 Pages/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="c0b66-142">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="c0b66-143">[布局](xref:mvc/views/layout)模板允许 HTML 容器具有如下布局：</span><span class="sxs-lookup"><span data-stu-id="c0b66-143">[Layout](xref:mvc/views/layout) templates allow the HTML container layout to be:</span></span>

* <span data-ttu-id="c0b66-144">在一个位置指定。</span><span class="sxs-lookup"><span data-stu-id="c0b66-144">Specified in one place.</span></span>
* <span data-ttu-id="c0b66-145">应用于站点中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="c0b66-145">Applied in multiple pages in the site.</span></span>

<span data-ttu-id="c0b66-146">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="c0b66-146">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="c0b66-147">`RenderBody` 是显示全部页面专用视图的占位符，已包装在布局页中。</span><span class="sxs-lookup"><span data-stu-id="c0b66-147">`RenderBody` is a placeholder where all the page-specific views show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="c0b66-148">例如，选择“隐私”链接后，Pages/Privacy.cshtml 视图在 `RenderBody` 方法中呈现。</span><span class="sxs-lookup"><span data-stu-id="c0b66-148">For example, select the **Privacy** link and the *Pages/Privacy.cshtml* view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="c0b66-149">ViewData 和布局</span><span class="sxs-lookup"><span data-stu-id="c0b66-149">ViewData and layout</span></span>

<span data-ttu-id="c0b66-150">考虑来自 Pages/Movies/Index.cshtml 文件的以下标记：</span><span class="sxs-lookup"><span data-stu-id="c0b66-150">Consider the following markup from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="c0b66-151">前面突出显示的标记是 Razor 转换为 C# 的一个示例。</span><span class="sxs-lookup"><span data-stu-id="c0b66-151">The preceding highlighted markup is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="c0b66-152">`{` 和 `}` 字符括住 C# 代码块。</span><span class="sxs-lookup"><span data-stu-id="c0b66-152">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="c0b66-153">`PageModel` 基类包含 `ViewData` 字典属性，可用于将数据传递到某个视图。</span><span class="sxs-lookup"><span data-stu-id="c0b66-153">The `PageModel` base class contains a `ViewData` dictionary property that can be used to pass data to a View.</span></span> <span data-ttu-id="c0b66-154">可以使用 ***键值*** 模式将对象添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="c0b66-154">Objects are added to the `ViewData` dictionary using a ***key value*** pattern.</span></span> <span data-ttu-id="c0b66-155">在前面的示例中，`Title` 属性被添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="c0b66-155">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="c0b66-156">`Title` 属性用于 Pages/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="c0b66-156">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="c0b66-157">以下标记显示 _Layout.cshtml 文件的前几行。</span><span class="sxs-lookup"><span data-stu-id="c0b66-157">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6)]

<span data-ttu-id="c0b66-158">行 `@*Markup removed for brevity.*@` 是 Razor 注释。</span><span class="sxs-lookup"><span data-stu-id="c0b66-158">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="c0b66-159">与 HTML 注释 `<!-- -->` 不同，Razor 注释不会发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="c0b66-159">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="c0b66-160">有关详细信息，请参阅 [MDN Web 文档：HTML 入门](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-160">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="c0b66-161">更新布局</span><span class="sxs-lookup"><span data-stu-id="c0b66-161">Update the layout</span></span>

1. <span data-ttu-id="c0b66-162">更改 Pages/Shared/_Layout.cshtml 文件中的 `<title>` 元素以显示 Movie 而不是 RazorPagesMovie 。</span><span class="sxs-lookup"><span data-stu-id="c0b66-162">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

1. <span data-ttu-id="c0b66-163">在 Pages/Shared/_Layout.cshtml 文件中，查找以下定位点元素。</span><span class="sxs-lookup"><span data-stu-id="c0b66-163">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

   ```cshtml
   <a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
   ```

1. <span data-ttu-id="c0b66-164">将前面的元素替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="c0b66-164">Replace the preceding element with the following markup:</span></span>

   ```cshtml
   <a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
   ```

   <span data-ttu-id="c0b66-165">前面的定位点元素是一个[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-165">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="c0b66-166">此处它是[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-166">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="c0b66-167">`asp-page="/Movies/Index"` 标记帮助程序属性和值可以创建指向 `/Movies/Index` Razor 页面的链接。</span><span class="sxs-lookup"><span data-stu-id="c0b66-167">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="c0b66-168">`asp-area` 属性值为空，因此在链接中未使用区域。</span><span class="sxs-lookup"><span data-stu-id="c0b66-168">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="c0b66-169">有关详细信息，请参阅[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-169">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

1. <span data-ttu-id="c0b66-170">保存所做的更改，并通过选择“RpMovie”链接测试应用。</span><span class="sxs-lookup"><span data-stu-id="c0b66-170">Save the changes and test the app by selecting the **RpMovie** link.</span></span> <span data-ttu-id="c0b66-171">如果遇到任何问题，请参阅 GitHub 中的 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) 文件。</span><span class="sxs-lookup"><span data-stu-id="c0b66-171">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

1. <span data-ttu-id="c0b66-172">测试“主页”、“RpMovie”、“创建”、“编辑”和“删除”链接    。</span><span class="sxs-lookup"><span data-stu-id="c0b66-172">Test the **Home**, **RpMovie**, **Create**, **Edit**, and **Delete** links.</span></span> <span data-ttu-id="c0b66-173">每个页面都设置有标题，可以在浏览器选项卡中看到标题。将某个页面加入书签时，标题用于该书签。</span><span class="sxs-lookup"><span data-stu-id="c0b66-173">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="c0b66-174">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="c0b66-174">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="c0b66-175">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的的非英语区域设置，以及支持非美国英语日期格式，必须执行使应用全球化的步骤。</span><span class="sxs-lookup"><span data-stu-id="c0b66-175">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="c0b66-176">有关添加十进制逗号的说明，请参阅 [GitHub 问题 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-176">See this [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="c0b66-177">在 Pages/_ViewStart.cshtml 文件中设置 `Layout` 属性：</span><span class="sxs-lookup"><span data-stu-id="c0b66-177">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/Pages/_ViewStart.cshtml)]

<span data-ttu-id="c0b66-178">前面的标记针对 Pages 文件夹下的所有 Razor 文件将布局文件设置为 Pages/Shared/_Layout.cshtml。 </span><span class="sxs-lookup"><span data-stu-id="c0b66-178">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="c0b66-179">请参阅[布局](xref:razor-pages/index#layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="c0b66-179">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="c0b66-180">“创建”页面模型</span><span class="sxs-lookup"><span data-stu-id="c0b66-180">The Create page model</span></span>

<span data-ttu-id="c0b66-181">检查 *Pages/Movies/Create.cshtml.cs* 页面模型：</span><span class="sxs-lookup"><span data-stu-id="c0b66-181">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="c0b66-182">`OnGet` 方法初始化页面所需的任何状态。</span><span class="sxs-lookup"><span data-stu-id="c0b66-182">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="c0b66-183">“创建”页没有任何要初始化的状态，因此返回 `Page`。</span><span class="sxs-lookup"><span data-stu-id="c0b66-183">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="c0b66-184">在本教程的后面部分中，将介绍 `OnGet` 初始化状态的示例。</span><span class="sxs-lookup"><span data-stu-id="c0b66-184">Later in the tutorial, an example of `OnGet` initializing state is shown.</span></span> <span data-ttu-id="c0b66-185">`Page` 方法创建用于呈现 Create.cshtml 页的 `PageResult` 对象。</span><span class="sxs-lookup"><span data-stu-id="c0b66-185">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="c0b66-186">`Movie` 属性使用 [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) 特性来选择加入[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-186">The `Movie` property uses the [[BindProperty]](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute) attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="c0b66-187">当“创建”表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="c0b66-187">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="c0b66-188">当页面发布表单数据时，运行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="c0b66-188">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="c0b66-189">如果不存在任何模型错误，将重新显示表单，以及发布的任何表单数据。</span><span class="sxs-lookup"><span data-stu-id="c0b66-189">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="c0b66-190">在发布表单前，可以在客户端捕获到大部分模型错误。</span><span class="sxs-lookup"><span data-stu-id="c0b66-190">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="c0b66-191">模型错误的一个示例是，发布的日期字段值无法转换为日期。</span><span class="sxs-lookup"><span data-stu-id="c0b66-191">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="c0b66-192">本教程后面讨论了客户端验证和模型验证。</span><span class="sxs-lookup"><span data-stu-id="c0b66-192">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="c0b66-193">如果没有模型错误：</span><span class="sxs-lookup"><span data-stu-id="c0b66-193">If there are no model errors:</span></span>

* <span data-ttu-id="c0b66-194">将保存数据。</span><span class="sxs-lookup"><span data-stu-id="c0b66-194">The data is saved.</span></span>
* <span data-ttu-id="c0b66-195">浏览器将重定向到 Index 页。</span><span class="sxs-lookup"><span data-stu-id="c0b66-195">The browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="c0b66-196">“创建 Razor”页面</span><span class="sxs-lookup"><span data-stu-id="c0b66-196">The Create Razor Page</span></span>

<span data-ttu-id="c0b66-197">检查 Pages/Movies/Create.cshtml Razor 页面文件：</span><span class="sxs-lookup"><span data-stu-id="c0b66-197">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="c0b66-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0b66-198">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c0b66-199">Visual Studio 以用于标记帮助程序的特殊加粗字体显示以下标记：</span><span class="sxs-lookup"><span data-stu-id="c0b66-199">Visual Studio displays the following tags in a distinctive bold font used for Tag Helpers:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

![Create.cshtml 页的 VS17 视图](page/_static/th3.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="c0b66-201">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c0b66-201">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="c0b66-202">以下标记帮助程序显示在前面的标记中：</span><span class="sxs-lookup"><span data-stu-id="c0b66-202">The following Tag Helpers are shown in the preceding markup:</span></span>

* `<form method="post">`
* `<div asp-validation-summary="ModelOnly" class="text-danger"></div>`
* `<label asp-for="Movie.Title" class="control-label"></label>`
* `<input asp-for="Movie.Title" class="form-control" />`
* `<span asp-validation-for="Movie.Title" class="text-danger"></span>`

---

<span data-ttu-id="c0b66-203">`<form method="post">` 元素是一个[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-203">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="c0b66-204">表单标记帮助程序会自动包含[防伪令牌](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-204">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="c0b66-205">基架引擎在模型中为每个字段（ID 除外）创建 Razor 标记，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c0b66-205">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample3/RazorPagesMovie30/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="c0b66-206">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)（`<div asp-validation-summary` 和 `<span asp-validation-for`）显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="c0b66-206">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="c0b66-207">本系列后面的部分将更详细地讨论有关验证的信息。</span><span class="sxs-lookup"><span data-stu-id="c0b66-207">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="c0b66-208">[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) 生成标签描述和 `Title` 属性的 `[for]` 特性。</span><span class="sxs-lookup"><span data-stu-id="c0b66-208">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="c0b66-209">[输入标记帮助程序](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) 使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性并在客户端生成 jQuery 验证所需的 HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="c0b66-209">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

<span data-ttu-id="c0b66-210">有关标记帮助程序（如 `<form method="post">`）的详细信息，请参阅 [ASP.NET Core 中的标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-210">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0b66-211">其他资源</span><span class="sxs-lookup"><span data-stu-id="c0b66-211">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="c0b66-212">[上一篇：添加模型](xref:tutorials/razor-pages/model)
> [下一步：使用数据库](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="c0b66-212">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-create-delete-details-and-edit-pages"></a><span data-ttu-id="c0b66-213">“创建”、“删除”、“详细信息”和“编辑”页面</span><span class="sxs-lookup"><span data-stu-id="c0b66-213">The Create, Delete, Details, and Edit pages</span></span>

<span data-ttu-id="c0b66-214">检查 Pages/Movies/Index.cshtml.cs 页面模型：</span><span class="sxs-lookup"><span data-stu-id="c0b66-214">Examine the *Pages/Movies/Index.cshtml.cs* Page Model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml.cs)]

<span data-ttu-id="c0b66-215">Razor 页面派生自 `PageModel`。</span><span class="sxs-lookup"><span data-stu-id="c0b66-215">Razor Pages are derived from `PageModel`.</span></span> <span data-ttu-id="c0b66-216">按照约定，`PageModel` 派生的类称为 `<PageName>Model`。</span><span class="sxs-lookup"><span data-stu-id="c0b66-216">By convention, the `PageModel`-derived class is named `<PageName>Model`.</span></span> <span data-ttu-id="c0b66-217">此构造函数使用[依赖关系注入](xref:fundamentals/dependency-injection)将 `RazorPagesMovieContext` 添加到页。</span><span class="sxs-lookup"><span data-stu-id="c0b66-217">The constructor uses [dependency injection](xref:fundamentals/dependency-injection) to add the `RazorPagesMovieContext` to the page.</span></span> <span data-ttu-id="c0b66-218">已搭建基架的页面遵循此模式。</span><span class="sxs-lookup"><span data-stu-id="c0b66-218">The scaffolded pages follow this pattern.</span></span> <span data-ttu-id="c0b66-219">请参阅[异步代码](xref:data/ef-rp/intro#asynchronous-code)，了解有关使用实体框架的异步编程的详细信息。</span><span class="sxs-lookup"><span data-stu-id="c0b66-219">See [Asynchronous code](xref:data/ef-rp/intro#asynchronous-code) for more information on asynchronous programming with Entity Framework.</span></span>

<span data-ttu-id="c0b66-220">对页面发出请求时，`OnGetAsync` 方法向 Razor 页面返回影片列表。</span><span class="sxs-lookup"><span data-stu-id="c0b66-220">When a request is made for the page, the `OnGetAsync` method returns a list of movies to the Razor Page.</span></span> <span data-ttu-id="c0b66-221">`OnGetAsync` 或 `OnGet` 在 Razor 页面上调用，以初始化该页面的状态。</span><span class="sxs-lookup"><span data-stu-id="c0b66-221">`OnGetAsync` or `OnGet` is called on a Razor Page to initialize the state for the page.</span></span> <span data-ttu-id="c0b66-222">在这种情况下，`OnGetAsync` 将获得影片列表并显示出来。</span><span class="sxs-lookup"><span data-stu-id="c0b66-222">In this case, `OnGetAsync` gets a list of movies and displays them.</span></span>

<span data-ttu-id="c0b66-223">当 `OnGet` 返回 `void` 或 `OnGetAsync` 返回 `Task` 时，不使用任何返回方法。</span><span class="sxs-lookup"><span data-stu-id="c0b66-223">When `OnGet` returns `void` or `OnGetAsync` returns `Task`, no return method is used.</span></span> <span data-ttu-id="c0b66-224">当返回类型是 `IActionResult` 或 `Task<IActionResult>` 时，必须提供返回语句。</span><span class="sxs-lookup"><span data-stu-id="c0b66-224">When the return type is `IActionResult` or `Task<IActionResult>`, a return statement must be provided.</span></span> <span data-ttu-id="c0b66-225">例如，Pages/Movies/Create.cshtml.cs `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="c0b66-225">For example, the *Pages/Movies/Create.cshtml.cs* `OnPostAsync` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml.cs?name=snippet)]

<a name="index"></a> <span data-ttu-id="c0b66-226">检查 Pages/Movies/Index.cshtml Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="c0b66-226">Examine the *Pages/Movies/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml)]

<span data-ttu-id="c0b66-227">Razor 可以从 HTML 转换为 C# 或 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="c0b66-227">Razor can transition from HTML into C# or into Razor-specific markup.</span></span> <span data-ttu-id="c0b66-228">当 `@` 符号后跟 [Razor 保留关键字](xref:mvc/views/razor#razor-reserved-keywords)时，它会转换为 Razor 特定标记，否则会转换为 C#。</span><span class="sxs-lookup"><span data-stu-id="c0b66-228">When an `@` symbol is followed by a [Razor reserved keyword](xref:mvc/views/razor#razor-reserved-keywords), it transitions into Razor-specific markup, otherwise it transitions into C#.</span></span>

<span data-ttu-id="c0b66-229">`@page` Razor 指令将文件转换为一个 MVC 操作，这意味着它可以处理请求。</span><span class="sxs-lookup"><span data-stu-id="c0b66-229">The `@page` Razor directive makes the file into an MVC action, which means that it can handle requests.</span></span> <span data-ttu-id="c0b66-230">`@page` 必须是页面上的第一个 Razor 指令。</span><span class="sxs-lookup"><span data-stu-id="c0b66-230">`@page` must be the first Razor directive on a page.</span></span> <span data-ttu-id="c0b66-231">`@page` 是转换为 Razor 特定标记的示例。</span><span class="sxs-lookup"><span data-stu-id="c0b66-231">`@page` is an example of transitioning into Razor-specific markup.</span></span> <span data-ttu-id="c0b66-232">有关详细信息，请参阅 [Razor 语法](xref:mvc/views/razor#razor-syntax)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-232">See [Razor syntax](xref:mvc/views/razor#razor-syntax) for more information.</span></span>

<a name="md"></a>

### <a name="the-model-directive"></a><span data-ttu-id="c0b66-233">@model 指令</span><span class="sxs-lookup"><span data-stu-id="c0b66-233">The @model directive</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-2&highlight=2)]

<span data-ttu-id="c0b66-234">`@model` 指令指定传递到 Razor 页面的模型类型。</span><span class="sxs-lookup"><span data-stu-id="c0b66-234">The `@model` directive specifies the type of the model passed to the Razor Page.</span></span> <span data-ttu-id="c0b66-235">在前面的示例中，`@model` 行使 `PageModel` 派生的类可用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="c0b66-235">In the preceding example, the `@model` line makes the `PageModel`-derived class available to the Razor Page.</span></span> <span data-ttu-id="c0b66-236">在页面上的 `@Html.DisplayNameFor` 和 `@Html.DisplayFor` [HTML 帮助程序](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers)中使用该模型。</span><span class="sxs-lookup"><span data-stu-id="c0b66-236">The model is used in the `@Html.DisplayNameFor` and `@Html.DisplayFor` [HTML Helpers](/aspnet/mvc/overview/older-versions-1/views/creating-custom-html-helpers-cs#understanding-html-helpers) on the page.</span></span>

### <a name="html-helpers"></a><span data-ttu-id="c0b66-237">HTML 帮助程序</span><span class="sxs-lookup"><span data-stu-id="c0b66-237">HTML Helpers</span></span>

<span data-ttu-id="c0b66-238">检查以下 HTML 帮助程序中使用的 Lambda 表达式：</span><span class="sxs-lookup"><span data-stu-id="c0b66-238">Examine the lambda expression used in the following HTML Helper:</span></span>

```cshtml
@Html.DisplayNameFor(model => model.Movie[0].Title)
```

<span data-ttu-id="c0b66-239"><xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML 帮助程序检查 Lambda 表达式中引用的 `Title` 属性来确定显示名称。</span><span class="sxs-lookup"><span data-stu-id="c0b66-239">The <xref:System.Web.Mvc.Html.DisplayNameExtensions.DisplayNameFor%2A?displayProperty=nameWithType> HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="c0b66-240">检查 Lambda 表达式（而非求值）。</span><span class="sxs-lookup"><span data-stu-id="c0b66-240">The lambda expression is inspected rather than evaluated.</span></span> <span data-ttu-id="c0b66-241">这意味着当 `model`、`model.Movie` 或 `model.Movie[0]` 为 `null` 或为空时，不会存在任何访问冲突。</span><span class="sxs-lookup"><span data-stu-id="c0b66-241">That means there is no access violation when `model`, `model.Movie`, or `model.Movie[0]` are `null` or empty.</span></span> <span data-ttu-id="c0b66-242">对 Lambda 表达式求值时（例如，使用 `@Html.DisplayFor(modelItem => item.Title)`），将求得该模型的属性值。</span><span class="sxs-lookup"><span data-stu-id="c0b66-242">When the lambda expression is evaluated, for example, with `@Html.DisplayFor(modelItem => item.Title)`, the model's property values are evaluated.</span></span>
### <a name="the-layout-page"></a><span data-ttu-id="c0b66-243">布局页</span><span class="sxs-lookup"><span data-stu-id="c0b66-243">The layout page</span></span>

<span data-ttu-id="c0b66-244">选择菜单链接（“Razor PagesMovie”、“主页”和“隐私”）  。</span><span class="sxs-lookup"><span data-stu-id="c0b66-244">Select the menu links **RazorPagesMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="c0b66-245">每页显示相同的菜单布局。</span><span class="sxs-lookup"><span data-stu-id="c0b66-245">Each page shows the same menu layout.</span></span> <span data-ttu-id="c0b66-246">菜单布局是在 Pages/Shared/_Layout.cshtml 文件中实现。</span><span class="sxs-lookup"><span data-stu-id="c0b66-246">The menu layout is implemented in the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="c0b66-247">打开并检查 Pages/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="c0b66-247">Open and examine the *Pages/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="c0b66-248">[布局](xref:mvc/views/layout)模板使你能够在一个位置指定网站的 HTML 容器布局，然后将它应用到网站中的多个页面。</span><span class="sxs-lookup"><span data-stu-id="c0b66-248">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of a site in one place and then apply it across multiple pages in the site.</span></span> <span data-ttu-id="c0b66-249">查找 `@RenderBody()` 行。</span><span class="sxs-lookup"><span data-stu-id="c0b66-249">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="c0b66-250">`RenderBody` 是显示所创建的全部页面专用视图的占位符，已包装在布局页中。</span><span class="sxs-lookup"><span data-stu-id="c0b66-250">`RenderBody` is a placeholder where all the page-specific views you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="c0b66-251">例如，如果选择“隐私”链接，Pages/Privacy.cshtml 视图在 `RenderBody` 方法中呈现。</span><span class="sxs-lookup"><span data-stu-id="c0b66-251">For example, if you select the **Privacy** link, the **Pages/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

<a name="vd"></a>

### <a name="viewdata-and-layout"></a><span data-ttu-id="c0b66-252">ViewData 和布局</span><span class="sxs-lookup"><span data-stu-id="c0b66-252">ViewData and layout</span></span>

<span data-ttu-id="c0b66-253">考虑来自 Pages/Movies/Index.cshtml 文件中的以下代码：</span><span class="sxs-lookup"><span data-stu-id="c0b66-253">Consider the following code from the *Pages/Movies/Index.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Index.cshtml?range=1-6&highlight=4-999)]

<span data-ttu-id="c0b66-254">前面突出显示的代码是 Razor 转换为 C# 的一个示例。</span><span class="sxs-lookup"><span data-stu-id="c0b66-254">The preceding highlighted code is an example of Razor transitioning into C#.</span></span> <span data-ttu-id="c0b66-255">`{` 和 `}` 字符括住 C# 代码块。</span><span class="sxs-lookup"><span data-stu-id="c0b66-255">The `{` and `}` characters enclose a block of C# code.</span></span>

<span data-ttu-id="c0b66-256">`PageModel` 基类具有 `ViewData` 字典属性，可用于添加要传递到某个视图的数据。</span><span class="sxs-lookup"><span data-stu-id="c0b66-256">The `PageModel` base class has a `ViewData` dictionary property that can be used to add data that you want to pass to a View.</span></span> <span data-ttu-id="c0b66-257">可以使用键/值模式将对象添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="c0b66-257">You add objects into the `ViewData` dictionary using a key/value pattern.</span></span> <span data-ttu-id="c0b66-258">在前面的示例中，`Title` 属性被添加到 `ViewData` 字典。</span><span class="sxs-lookup"><span data-stu-id="c0b66-258">In the preceding sample, the `Title` property is added to the `ViewData` dictionary.</span></span>

<span data-ttu-id="c0b66-259">`Title` 属性用于 Pages/Shared/_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="c0b66-259">The `Title` property is used in the *Pages/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="c0b66-260">以下标记显示 _Layout.cshtml 文件的前几行。</span><span class="sxs-lookup"><span data-stu-id="c0b66-260">The following markup shows the first few lines of the *_Layout.cshtml* file.</span></span>

<!-- We need a snapshot copy of layout because we are changing in the next step. -->

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/NU/_Layout.cshtml?highlight=6-99)]

<span data-ttu-id="c0b66-261">行 `@*Markup removed for brevity.*@` 是 Razor 注释。</span><span class="sxs-lookup"><span data-stu-id="c0b66-261">The line `@*Markup removed for brevity.*@` is a Razor comment.</span></span> <span data-ttu-id="c0b66-262">与 HTML 注释 `<!-- -->` 不同，Razor 注释不会发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="c0b66-262">Unlike HTML comments `<!-- -->`, Razor comments are not sent to the client.</span></span> <span data-ttu-id="c0b66-263">有关详细信息，请参阅 [MDN Web 文档：HTML 入门](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-263">See [MDN web docs: Getting started with HTML](https://developer.mozilla.org/docs/Learn/HTML/Introduction_to_HTML/Getting_started#HTML_comments) for more information.</span></span>

### <a name="update-the-layout"></a><span data-ttu-id="c0b66-264">更新布局</span><span class="sxs-lookup"><span data-stu-id="c0b66-264">Update the layout</span></span>

<span data-ttu-id="c0b66-265">更改 Pages/Shared/_Layout.cshtml 文件中的 `<title>` 元素以显示 Movie 而不是 RazorPagesMovie 。</span><span class="sxs-lookup"><span data-stu-id="c0b66-265">Change the `<title>` element in the *Pages/Shared/_Layout.cshtml* file to display **Movie** rather than **RazorPagesMovie**.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml?range=1-6&highlight=6)]

<span data-ttu-id="c0b66-266">在 Pages/Shared/_Layout.cshtml 文件中，查找以下定位点元素。</span><span class="sxs-lookup"><span data-stu-id="c0b66-266">Find the following anchor element in the *Pages/Shared/_Layout.cshtml* file.</span></span>

```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Index">RazorPagesMovie</a>
```

<span data-ttu-id="c0b66-267">将前面的元素替换为以下标记。</span><span class="sxs-lookup"><span data-stu-id="c0b66-267">Replace the preceding element with the following markup.</span></span>

```cshtml
<a class="navbar-brand" asp-page="/Movies/Index">RpMovie</a>
```

<span data-ttu-id="c0b66-268">前面的定位点元素是一个[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-268">The preceding anchor element is a [Tag Helper](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="c0b66-269">此处它是[定位点标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-269">In this case, it's the [Anchor Tag Helper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper).</span></span> <span data-ttu-id="c0b66-270">`asp-page="/Movies/Index"` 标记帮助程序属性和值可以创建指向 `/Movies/Index` Razor 页面的链接。</span><span class="sxs-lookup"><span data-stu-id="c0b66-270">The `asp-page="/Movies/Index"` Tag Helper attribute and value creates a link to the `/Movies/Index` Razor Page.</span></span> <span data-ttu-id="c0b66-271">`asp-area` 属性值为空，因此在链接中未使用区域。</span><span class="sxs-lookup"><span data-stu-id="c0b66-271">The `asp-area` attribute value is empty, so the area isn't used in the link.</span></span> <span data-ttu-id="c0b66-272">有关详细信息，请参阅[区域](xref:mvc/controllers/areas)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-272">See [Areas](xref:mvc/controllers/areas) for more information.</span></span>

<span data-ttu-id="c0b66-273">保存所做的更改，并通过单击“RpMovie”链接测试应用。</span><span class="sxs-lookup"><span data-stu-id="c0b66-273">Save your changes, and test the app by clicking on the **RpMovie** link.</span></span> <span data-ttu-id="c0b66-274">如果遇到任何问题，请参阅 GitHub 中的 [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) 文件。</span><span class="sxs-lookup"><span data-stu-id="c0b66-274">See the [_Layout.cshtml](https://github.com/dotnet/AspNetCore.Docs/blob/main/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Shared/_Layout.cshtml) file in GitHub if you have any problems.</span></span>

<span data-ttu-id="c0b66-275">测试其他链接（“主页”、“RpMovie”、“创建”、“编辑”和“删除”）。</span><span class="sxs-lookup"><span data-stu-id="c0b66-275">Test the other links (**Home**, **RpMovie**, **Create**, **Edit**, and **Delete**).</span></span> <span data-ttu-id="c0b66-276">每个页面都设置有标题，可以在浏览器选项卡中看到标题。将某个页面加入书签时，标题用于该书签。</span><span class="sxs-lookup"><span data-stu-id="c0b66-276">Each page sets the title, which you can see in the browser tab. When you bookmark a page, the title is used for the bookmark.</span></span>

> [!NOTE]
> <span data-ttu-id="c0b66-277">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="c0b66-277">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="c0b66-278">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的的非英语区域设置，以及支持非美国英语日期格式，必须执行使应用全球化的步骤。</span><span class="sxs-lookup"><span data-stu-id="c0b66-278">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point, and non US-English date formats, you must take steps to globalize the app.</span></span> <span data-ttu-id="c0b66-279">有关添加十进制逗号的说明，请参阅 [GitHub 问题 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-279">This [GitHub issue 4076](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420) for instructions on adding decimal comma.</span></span>

<span data-ttu-id="c0b66-280">在 Pages/_ViewStart.cshtml 文件中设置 `Layout` 属性：</span><span class="sxs-lookup"><span data-stu-id="c0b66-280">The `Layout` property is set in the *Pages/_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/_ViewStart.cshtml)]

<span data-ttu-id="c0b66-281">前面的标记针对 Pages 文件夹下的所有 Razor 文件将布局文件设置为 Pages/Shared/_Layout.cshtml。 </span><span class="sxs-lookup"><span data-stu-id="c0b66-281">The preceding markup sets the layout file to *Pages/Shared/_Layout.cshtml* for all Razor files under the *Pages* folder.</span></span> <span data-ttu-id="c0b66-282">请参阅[布局](xref:razor-pages/index#layout)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="c0b66-282">See [Layout](xref:razor-pages/index#layout) for more information.</span></span>

### <a name="the-create-page-model"></a><span data-ttu-id="c0b66-283">“创建”页面模型</span><span class="sxs-lookup"><span data-stu-id="c0b66-283">The Create page model</span></span>

<span data-ttu-id="c0b66-284">检查 *Pages/Movies/Create.cshtml.cs* 页面模型：</span><span class="sxs-lookup"><span data-stu-id="c0b66-284">Examine the *Pages/Movies/Create.cshtml.cs* page model:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetALL)]

<span data-ttu-id="c0b66-285">`OnGet` 方法初始化页面所需的任何状态。</span><span class="sxs-lookup"><span data-stu-id="c0b66-285">The `OnGet` method initializes any state needed for the page.</span></span> <span data-ttu-id="c0b66-286">“创建”页没有任何要初始化的状态，因此返回 `Page`。</span><span class="sxs-lookup"><span data-stu-id="c0b66-286">The Create page doesn't have any state to initialize, so `Page` is returned.</span></span> <span data-ttu-id="c0b66-287">教程的后面部分将介绍 `OnGet` 方法初始化状态。</span><span class="sxs-lookup"><span data-stu-id="c0b66-287">Later in the tutorial you see `OnGet` method initialize state.</span></span> <span data-ttu-id="c0b66-288">`Page` 方法创建用于呈现 Create.cshtml 页的 `PageResult` 对象。</span><span class="sxs-lookup"><span data-stu-id="c0b66-288">The `Page` method creates a `PageResult` object that renders the *Create.cshtml* page.</span></span>

<span data-ttu-id="c0b66-289">`Movie` 属性使用 [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> 特性来选择加入[模型绑定](xref:mvc/models/model-binding)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-289">The `Movie` property uses the [[BindProperty]]<xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute> attribute to opt-in to [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="c0b66-290">当“创建”表单发布表单值时，ASP.NET Core 运行时将发布的值绑定到 `Movie` 模型。</span><span class="sxs-lookup"><span data-stu-id="c0b66-290">When the Create form posts the form values, the ASP.NET Core runtime binds the posted values to the `Movie` model.</span></span>

<span data-ttu-id="c0b66-291">当页面发布表单数据时，运行 `OnPostAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="c0b66-291">The `OnPostAsync` method is run when the page posts form data:</span></span>

[!code-csharp[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml.cs?name=snippetPost)]

<span data-ttu-id="c0b66-292">如果不存在任何模型错误，将重新显示表单，以及发布的任何表单数据。</span><span class="sxs-lookup"><span data-stu-id="c0b66-292">If there are any model errors, the form is redisplayed, along with any form data posted.</span></span> <span data-ttu-id="c0b66-293">在发布表单前，可以在客户端捕获到大部分模型错误。</span><span class="sxs-lookup"><span data-stu-id="c0b66-293">Most model errors can be caught on the client-side before the form is posted.</span></span> <span data-ttu-id="c0b66-294">模型错误的一个示例是，发布的日期字段值无法转换为日期。</span><span class="sxs-lookup"><span data-stu-id="c0b66-294">An example of a model error is posting a value for the date field that cannot be converted to a date.</span></span> <span data-ttu-id="c0b66-295">本教程后面讨论了客户端验证和模型验证。</span><span class="sxs-lookup"><span data-stu-id="c0b66-295">Client-side validation and model validation are discussed later in the tutorial.</span></span>

<span data-ttu-id="c0b66-296">如果不存在模型错误，将保存数据，并且浏览器会重定向到Index页。</span><span class="sxs-lookup"><span data-stu-id="c0b66-296">If there are no model errors, the data is saved, and the browser is redirected to the Index page.</span></span>

### <a name="the-create-razor-page"></a><span data-ttu-id="c0b66-297">“创建 Razor”页面</span><span class="sxs-lookup"><span data-stu-id="c0b66-297">The Create Razor Page</span></span>

<span data-ttu-id="c0b66-298">检查 Pages/Movies/Create.cshtml Razor 页面文件：</span><span class="sxs-lookup"><span data-stu-id="c0b66-298">Examine the *Pages/Movies/Create.cshtml* Razor Page file:</span></span>

[!code-cshtml[](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml)]

# <a name="visual-studio"></a>[<span data-ttu-id="c0b66-299">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c0b66-299">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c0b66-300">Visual Studio 以用于标记帮助程序的特殊加粗字体显示 `<form method="post">` 标记：</span><span class="sxs-lookup"><span data-stu-id="c0b66-300">Visual Studio displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers:</span></span>

![Create.cshtml 页的 VS17 视图](page/_static/th.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="c0b66-302">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c0b66-302">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c0b66-303">有关标记帮助程序（如 `<form method="post">`）的详细信息，请参阅 [ASP.NET Core 中的标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-303">For more information on Tag Helpers such as `<form method="post">`, see [Tag Helpers in ASP.NET Core](xref:mvc/views/tag-helpers/intro).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="c0b66-304">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="c0b66-304">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="c0b66-305">Visual Studio for Mac 以用于标记帮助程序的特殊加粗字体显示 `<form method="post">` 标记。</span><span class="sxs-lookup"><span data-stu-id="c0b66-305">Visual Studio for Mac displays the `<form method="post">` tag in a distinctive bold font used for Tag Helpers.</span></span>

---

<span data-ttu-id="c0b66-306">`<form method="post">` 元素是一个[表单标记帮助程序](xref:mvc/views/working-with-forms#the-form-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-306">The `<form method="post">` element is a [Form Tag Helper](xref:mvc/views/working-with-forms#the-form-tag-helper).</span></span> <span data-ttu-id="c0b66-307">表单标记帮助程序会自动包含[防伪令牌](xref:security/anti-request-forgery)。</span><span class="sxs-lookup"><span data-stu-id="c0b66-307">The Form Tag Helper automatically includes an [antiforgery token](xref:security/anti-request-forgery).</span></span>

<span data-ttu-id="c0b66-308">基架引擎在模型中为每个字段（ID 除外）创建 Razor 标记，如下所示：</span><span class="sxs-lookup"><span data-stu-id="c0b66-308">The scaffolding engine creates Razor markup for each field in the model, except the ID, similar to the following:</span></span>

[!code-cshtml[](~/tutorials/razor-pages/razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=15-20)]

<span data-ttu-id="c0b66-309">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)（`<div asp-validation-summary` 和 `<span asp-validation-for`）显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="c0b66-309">The [Validation Tag Helpers](xref:mvc/views/working-with-forms#the-validation-tag-helpers) (`<div asp-validation-summary` and `<span asp-validation-for`) display validation errors.</span></span> <span data-ttu-id="c0b66-310">本系列后面的部分将更详细地讨论有关验证的信息。</span><span class="sxs-lookup"><span data-stu-id="c0b66-310">Validation is covered in more detail later in this series.</span></span>

<span data-ttu-id="c0b66-311">[标签标记帮助程序](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) 生成标签描述和 `Title` 属性的 `[for]` 特性。</span><span class="sxs-lookup"><span data-stu-id="c0b66-311">The [Label Tag Helper](xref:mvc/views/working-with-forms#the-label-tag-helper) (`<label asp-for="Movie.Title" class="control-label"></label>`) generates the label caption and `[for]` attribute for the `Title` property.</span></span>

<span data-ttu-id="c0b66-312">[输入标记帮助程序](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) 使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 属性并在客户端生成 jQuery 验证所需的 HTML 属性。</span><span class="sxs-lookup"><span data-stu-id="c0b66-312">The [Input Tag Helper](xref:mvc/views/working-with-forms) (`<input asp-for="Movie.Title" class="form-control">`) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c0b66-313">其他资源</span><span class="sxs-lookup"><span data-stu-id="c0b66-313">Additional resources</span></span>

* [<span data-ttu-id="c0b66-314">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="c0b66-314">YouTube version of this tutorial</span></span>](https://youtu.be/zxgKjPYnOMM)

> [!div class="step-by-step"]
> <span data-ttu-id="c0b66-315">[上一篇：添加模型](xref:tutorials/razor-pages/model)
> [下一步：使用数据库](xref:tutorials/razor-pages/sql)</span><span class="sxs-lookup"><span data-stu-id="c0b66-315">[Previous: Add a model](xref:tutorials/razor-pages/model)
[Next: Work with a database](xref:tutorials/razor-pages/sql)</span></span>

::: moniker-end
