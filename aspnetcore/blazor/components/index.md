---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
uid: blazor/components/index
ms.openlocfilehash: fe30c3b3be92c30dea6d0cb97e642eec1da6328b
ms.sourcegitcommit: 72c8ada9b9644ee4751ed6869e892a8558dd8e66
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98723507"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="42263-103">创建和使用 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="42263-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="42263-104">作者：[Luke Latham](https://github.com/guardrex)、[Daniel Roth](https://github.com/danroth27)、[Scott Addie](https://github.com/scottaddie) 和 [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="42263-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Scott Addie](https://github.com/scottaddie), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="42263-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="42263-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="42263-106">Blazor 应用是使用组件构建的。</span><span class="sxs-lookup"><span data-stu-id="42263-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="42263-107">组件是自包含的用户界面 (UI) 块，例如页、对话框或窗体。</span><span class="sxs-lookup"><span data-stu-id="42263-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="42263-108">组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。</span><span class="sxs-lookup"><span data-stu-id="42263-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="42263-109">组件非常灵活且轻量。</span><span class="sxs-lookup"><span data-stu-id="42263-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="42263-110">可在项目之间嵌套、重复使用和共享。</span><span class="sxs-lookup"><span data-stu-id="42263-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="42263-111">组件类</span><span class="sxs-lookup"><span data-stu-id="42263-111">Component classes</span></span>

<span data-ttu-id="42263-112">组件是使用 C# 和 HTML 标记的组合在 [Razor](xref:mvc/views/razor) 组件文件 (`.razor`) 中实现的。</span><span class="sxs-lookup"><span data-stu-id="42263-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="42263-113">Blazor 中的组件正式称为 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="42263-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="42263-114">Razor 语法</span><span class="sxs-lookup"><span data-stu-id="42263-114">Razor syntax</span></span>

<span data-ttu-id="42263-115">Blazor 应用中的 Razor 组件广泛使用 Razor 语法。</span><span class="sxs-lookup"><span data-stu-id="42263-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="42263-116">如果你不熟悉 Razor 标记语言，建议先阅读 [ASP.NET Core 的 Razor 语法参考](xref:mvc/views/razor)，然后再继续。</span><span class="sxs-lookup"><span data-stu-id="42263-116">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="42263-117">访问 Razor 语法上的内容时，请特别注意以下各节：</span><span class="sxs-lookup"><span data-stu-id="42263-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="42263-118">[指令](xref:mvc/views/razor#directives)：前缀为 `@` 的保留关键字，通常会更改组件标记的分析或运行方式。</span><span class="sxs-lookup"><span data-stu-id="42263-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="42263-119">[指令特性](xref:mvc/views/razor#directive-attributes)：前缀为 `@` 的保留关键字，通常会更改组件元素的分析或运行方式。</span><span class="sxs-lookup"><span data-stu-id="42263-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="42263-120">名称</span><span class="sxs-lookup"><span data-stu-id="42263-120">Names</span></span>

<span data-ttu-id="42263-121">组件的名称必须以大写字符开头。</span><span class="sxs-lookup"><span data-stu-id="42263-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="42263-122">例如，`MyCoolComponent.razor` 有效，而 `myCoolComponent.razor` 无效。</span><span class="sxs-lookup"><span data-stu-id="42263-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="42263-123">路由</span><span class="sxs-lookup"><span data-stu-id="42263-123">Routing</span></span>

<span data-ttu-id="42263-124">可以通过为应用中的每个可访问组件提供路由模板来实现 Blazor 中的路由。</span><span class="sxs-lookup"><span data-stu-id="42263-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="42263-125">编译具有 [`@page`][9] 指令的 Razor 文件时，将为生成的类提供指定路由模板的 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute>。</span><span class="sxs-lookup"><span data-stu-id="42263-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="42263-126">在运行时，路由器将使用 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 查找组件类，并呈现具有与请求的 URL 匹配的路由模板的任何组件。</span><span class="sxs-lookup"><span data-stu-id="42263-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="42263-127">有关详细信息，请参阅 <xref:blazor/fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="42263-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="42263-128">标记</span><span class="sxs-lookup"><span data-stu-id="42263-128">Markup</span></span>

<span data-ttu-id="42263-129">使用 HTML 定义组件的 UI。</span><span class="sxs-lookup"><span data-stu-id="42263-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="42263-130">动态呈现逻辑（例如，循环、条件、表达式）是使用名为 *Razor* 的嵌入式 C# 语法添加的。</span><span class="sxs-lookup"><span data-stu-id="42263-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="42263-131">在编译应用时，HTML 标记和 C# 呈现逻辑转换为组件类。</span><span class="sxs-lookup"><span data-stu-id="42263-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="42263-132">生成的类的名称与文件名匹配。</span><span class="sxs-lookup"><span data-stu-id="42263-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="42263-133">组件类的成员在 [`@code`][1] 块中定义。</span><span class="sxs-lookup"><span data-stu-id="42263-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="42263-134">在 [`@code`][1] 块中，通过用于处理事件或定义其他组件逻辑的方法指定组件状态（属性、字段）。</span><span class="sxs-lookup"><span data-stu-id="42263-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="42263-135">允许多个 [`@code`][1] 块。</span><span class="sxs-lookup"><span data-stu-id="42263-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="42263-136">可以使用以 `@` 开头的 C# 表达式将组件成员作为组件的呈现逻辑的一部分。</span><span class="sxs-lookup"><span data-stu-id="42263-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="42263-137">例如，通过为字段名称添加 `@` 前缀来呈现 C# 字段。</span><span class="sxs-lookup"><span data-stu-id="42263-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="42263-138">下面的示例计算并呈现：</span><span class="sxs-lookup"><span data-stu-id="42263-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="42263-139">`font-style` 的 CSS 属性值的 `headingFontStyle`。</span><span class="sxs-lookup"><span data-stu-id="42263-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="42263-140">`<h1>` 元素内容的 `headingText`。</span><span class="sxs-lookup"><span data-stu-id="42263-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="42263-141">最初呈现组件后，组件会为响应事件而重新生成其呈现树。</span><span class="sxs-lookup"><span data-stu-id="42263-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="42263-142">然后 Blazor 将新呈现树与前一个呈现树进行比较，并对浏览器文档对象模型 (DOM) 应用任何修改。</span><span class="sxs-lookup"><span data-stu-id="42263-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="42263-143"><xref:blazor/components/rendering> 中提供了更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="42263-143">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="42263-144">组件是普通 C# 类，可以放置在项目中的任何位置。</span><span class="sxs-lookup"><span data-stu-id="42263-144">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="42263-145">生成网页的组件通常位于 `Pages` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="42263-145">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="42263-146">非页面组件通常放置在 `Shared` 文件夹或添加到项目的自定义文件夹中。</span><span class="sxs-lookup"><span data-stu-id="42263-146">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="42263-147">命名空间</span><span class="sxs-lookup"><span data-stu-id="42263-147">Namespaces</span></span>

<span data-ttu-id="42263-148">通常，组件的命名空间是从应用的根命名空间和该组件在应用内的位置（文件夹）派生而来的。</span><span class="sxs-lookup"><span data-stu-id="42263-148">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="42263-149">如果应用的根命名空间是 `BlazorSample`，并且 `Counter` 组件位于 `Pages` 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="42263-149">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="42263-150">`Counter` 组件的命名空间为 `BlazorSample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="42263-150">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="42263-151">组件的完全限定类型名称为 `BlazorSample.Pages.Counter`。</span><span class="sxs-lookup"><span data-stu-id="42263-151">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="42263-152">对于保存组件的自定义文件夹，将 [`@using`][2] 指令添加到父组件或应用的 `_Imports.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="42263-152">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="42263-153">下面的示例提供 `Components` 文件夹中的组件：</span><span class="sxs-lookup"><span data-stu-id="42263-153">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="42263-154">还可以使用其完全限定的名称来引用组件，而不需要 [`@using`][2] 指令：</span><span class="sxs-lookup"><span data-stu-id="42263-154">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="42263-155">使用 Razor 创建的组件的命名空间基于（按优先级顺序）：</span><span class="sxs-lookup"><span data-stu-id="42263-155">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="42263-156">Razor 文件 (`.razor`) 标记 (`@namespace BlazorSample.MyNamespace`) 中的 [`@namespace`][8] 指定内容。</span><span class="sxs-lookup"><span data-stu-id="42263-156">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="42263-157">项目文件 (`<RootNamespace>BlazorSample</RootNamespace>`) 中项目的 `RootNamespace`。</span><span class="sxs-lookup"><span data-stu-id="42263-157">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="42263-158">项目名称，取自项目文件的文件名 (`.csproj`)，以及从项目根到组件的路径。</span><span class="sxs-lookup"><span data-stu-id="42263-158">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="42263-159">例如，框架将 `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) 解析为命名空间 `BlazorSample.Pages`。</span><span class="sxs-lookup"><span data-stu-id="42263-159">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="42263-160">组件遵循 C# 名称绑定规则。</span><span class="sxs-lookup"><span data-stu-id="42263-160">Components follow C# name binding rules.</span></span> <span data-ttu-id="42263-161">对于本示例中的 `Index` 组件，范围内的组件是所有组件：</span><span class="sxs-lookup"><span data-stu-id="42263-161">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="42263-162">在同一文件夹 `Pages` 中。</span><span class="sxs-lookup"><span data-stu-id="42263-162">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="42263-163">未显式指定其他命名空间的项目根中的组件。</span><span class="sxs-lookup"><span data-stu-id="42263-163">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="42263-164">不支持 `global::` 限定。</span><span class="sxs-lookup"><span data-stu-id="42263-164">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="42263-165">不支持导入具有别名 [`using`](/dotnet/csharp/language-reference/keywords/using-statement) 语句的组件（例如，`@using Foo = Bar`）。</span><span class="sxs-lookup"><span data-stu-id="42263-165">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="42263-166">不支持部分限定名称。</span><span class="sxs-lookup"><span data-stu-id="42263-166">Partially qualified names aren't supported.</span></span> <span data-ttu-id="42263-167">例如，不支持使用 `<Shared.NavMenu></Shared.NavMenu>` 添加 `@using BlazorSample` 和引用 `NavMenu` 组件 (`NavMenu.razor`)。</span><span class="sxs-lookup"><span data-stu-id="42263-167">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="42263-168">分部类支持</span><span class="sxs-lookup"><span data-stu-id="42263-168">Partial class support</span></span>

<span data-ttu-id="42263-169">Razor 组件作为分部类生成。</span><span class="sxs-lookup"><span data-stu-id="42263-169">Razor components are generated as partial classes.</span></span> <span data-ttu-id="42263-170">使用以下方法之一创建 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="42263-170">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="42263-171">在 [`@code`][1] 块中使用单个文件中的 HTML 标记和 Razor 代码定义 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="42263-171">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="42263-172">Blazor 模板使用此方法来定义其 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="42263-172">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="42263-173">C# 代码位于定义为分部类的代码隐藏文件中。</span><span class="sxs-lookup"><span data-stu-id="42263-173">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="42263-174">下面的示例显示了从 Blazor 模板生成的应用中具有 [`@code`][1] 块的默认 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="42263-174">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="42263-175">HTML 标记、Razor 代码和 C# 代码位于同一个文件中：</span><span class="sxs-lookup"><span data-stu-id="42263-175">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="42263-176">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-176">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="42263-177">还可以使用具有分部类的代码隐藏文件创建 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="42263-177">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="42263-178">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-178">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="42263-179">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="42263-179">`Counter.razor.cs`:</span></span>

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

<span data-ttu-id="42263-180">根据需要将任何所需的命名空间添加到分部类文件中。</span><span class="sxs-lookup"><span data-stu-id="42263-180">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="42263-181">Razor 组件使用的典型命名空间包括：</span><span class="sxs-lookup"><span data-stu-id="42263-181">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="42263-182">`_Imports.razor` 文件中的 [`@using`][2] 指令仅适用于 Razor 文件 (`.razor`)，而不适用于 C# 文件 (`.cs`)。</span><span class="sxs-lookup"><span data-stu-id="42263-182">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="42263-183">指定基类</span><span class="sxs-lookup"><span data-stu-id="42263-183">Specify a base class</span></span>

<span data-ttu-id="42263-184">[`@inherits`][6] 指令可用于指定组件的基类。</span><span class="sxs-lookup"><span data-stu-id="42263-184">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="42263-185">下面的示例演示组件如何继承基类 `BlazorRocksBase` 以提供组件的属性和方法。</span><span class="sxs-lookup"><span data-stu-id="42263-185">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="42263-186">基类应派生自 <xref:Microsoft.AspNetCore.Components.ComponentBase>。</span><span class="sxs-lookup"><span data-stu-id="42263-186">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="42263-187">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-187">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="42263-188">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="42263-188">`BlazorRocksBase.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a><span data-ttu-id="42263-189">使用组件</span><span class="sxs-lookup"><span data-stu-id="42263-189">Use components</span></span>

<span data-ttu-id="42263-190">通过使用 HTML 元素语法声明组件，组件可以包含其他组件。</span><span class="sxs-lookup"><span data-stu-id="42263-190">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="42263-191">使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。</span><span class="sxs-lookup"><span data-stu-id="42263-191">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="42263-192">`Pages/Index.razor` 中的以下标记呈现了 `HeadingComponent` 实例：</span><span class="sxs-lookup"><span data-stu-id="42263-192">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="42263-193">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-193">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="42263-194">如果某个组件包含一个 HTML 元素，该元素的大写首字母与组件名称不匹配，则会发出警告，指示该元素名称异常。</span><span class="sxs-lookup"><span data-stu-id="42263-194">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="42263-195">为组件的命名空间添加 [`@using`][2] 指令使组件可用，从而解决此警告。</span><span class="sxs-lookup"><span data-stu-id="42263-195">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="42263-196">参数</span><span class="sxs-lookup"><span data-stu-id="42263-196">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="42263-197">路由参数</span><span class="sxs-lookup"><span data-stu-id="42263-197">Route parameters</span></span>

<span data-ttu-id="42263-198">组件可以接收来自 [`@page`][9] 指令所提供的路由模板的路由参数。</span><span class="sxs-lookup"><span data-stu-id="42263-198">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="42263-199">路由器使用路由参数来填充相应的组件参数。</span><span class="sxs-lookup"><span data-stu-id="42263-199">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="42263-200">支持可选参数。</span><span class="sxs-lookup"><span data-stu-id="42263-200">Optional parameters are supported.</span></span> <span data-ttu-id="42263-201">在下面的示例中，`text` 可选参数将 route 段的值赋给组件的 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="42263-201">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="42263-202">如果该段不存在，则将 `Text` 的值设置为 `fantastic`。</span><span class="sxs-lookup"><span data-stu-id="42263-202">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="42263-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="42263-204">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-204">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="42263-205">不支持可选参数，因此在前面的示例中应用了两个 [`@page`][9] 指令。</span><span class="sxs-lookup"><span data-stu-id="42263-205">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="42263-206">第一个指令允许导航到没有参数的组件。</span><span class="sxs-lookup"><span data-stu-id="42263-206">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="42263-207">第二个 [`@page`][9] 指令会接收 `{text}` 路由参数，并将值赋予 `Text` 属性。</span><span class="sxs-lookup"><span data-stu-id="42263-207">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="42263-208">要了解 catch-all 路由参数 `{*pageRoute}`（它可跨多个文件夹边界捕获路径），请参阅 <xref:blazor/fundamentals/routing#catch-all-route-parameters>。</span><span class="sxs-lookup"><span data-stu-id="42263-208">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="42263-209">组件参数</span><span class="sxs-lookup"><span data-stu-id="42263-209">Component parameters</span></span>

<span data-ttu-id="42263-210">组件可以有组件参数，这些参数是使用组件类中包含 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性的简单或复杂公共属性定义的。</span><span class="sxs-lookup"><span data-stu-id="42263-210">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="42263-211">使用这些属性在标记中为组件指定参数。</span><span class="sxs-lookup"><span data-stu-id="42263-211">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="42263-212">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-212">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="42263-213">可为组件参数分配默认值：</span><span class="sxs-lookup"><span data-stu-id="42263-213">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="42263-214">在示例应用的以下示例中，`ParentComponent` 设置 `ChildComponent` 的 `Title` 属性的值。</span><span class="sxs-lookup"><span data-stu-id="42263-214">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="42263-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-215">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="42263-216">按照约定，使用 [Razor 的保留 `@` 符号](xref:mvc/views/razor#razor-syntax)将构成 C# 代码的特性值赋给参数：</span><span class="sxs-lookup"><span data-stu-id="42263-216">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="42263-217">父字段或属性：`Title="@{FIELD OR PROPERTY}`，其中占位符 `{FIELD OR PROPERTY}` 是父组件的 C# 字段或属性。</span><span class="sxs-lookup"><span data-stu-id="42263-217">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="42263-218">方法的结果：`Title="@{METHOD}"`，其中占位符 `{METHOD}` 是父组件的 C# 方法。</span><span class="sxs-lookup"><span data-stu-id="42263-218">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="42263-219">[隐式或显式表达式](xref:mvc/views/razor#implicit-razor-expressions)：`Title="@({EXPRESSION})"`，其中占位符 `{EXPRESSION}` 是 C# 表达式。</span><span class="sxs-lookup"><span data-stu-id="42263-219">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="42263-220">有关详细信息，请参阅 [ASP.NET Core 的 Razor 语法参考](xref:mvc/views/razor)。</span><span class="sxs-lookup"><span data-stu-id="42263-220">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="42263-221">请勿创建会写入其自己的组件参数的组件，而是使用私有字段。</span><span class="sxs-lookup"><span data-stu-id="42263-221">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="42263-222">有关详细信息，请参阅[重写参数](#overwritten-parameters)部分。</span><span class="sxs-lookup"><span data-stu-id="42263-222">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="42263-223">子内容</span><span class="sxs-lookup"><span data-stu-id="42263-223">Child content</span></span>

<span data-ttu-id="42263-224">组件可以设置另一个组件的内容。</span><span class="sxs-lookup"><span data-stu-id="42263-224">Components can set the content of another component.</span></span> <span data-ttu-id="42263-225">分配组件提供指定接收组件的标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="42263-225">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="42263-226">在下面的示例中，`ChildComponent` 具有一个表示 <xref:Microsoft.AspNetCore.Components.RenderFragment>（表示要呈现的 UI 段）的 `ChildContent` 属性。</span><span class="sxs-lookup"><span data-stu-id="42263-226">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="42263-227">`ChildContent` 的值放置在应呈现内容的组件标记中。</span><span class="sxs-lookup"><span data-stu-id="42263-227">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="42263-228">`ChildContent` 的值是从父组件接收的，并呈现在启动面板的 `panel-body` 中。</span><span class="sxs-lookup"><span data-stu-id="42263-228">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="42263-229">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-229">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="42263-230">必须按约定将接收 <xref:Microsoft.AspNetCore.Components.RenderFragment> 内容的属性命名为 `ChildContent`。</span><span class="sxs-lookup"><span data-stu-id="42263-230">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="42263-231">示例应用中的 `ParentComponent` 可以通过将内容置于 `<ChildComponent>` 标记中，提供用于呈现 `ChildComponent` 的内容。</span><span class="sxs-lookup"><span data-stu-id="42263-231">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="42263-232">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-232">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="42263-233">由于 Blazor 呈现子内容的方式，如果在子组件的内容中使用递增循环变量，则在 `for` 循环内呈现组件需要本地索引变量：</span><span class="sxs-lookup"><span data-stu-id="42263-233">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="42263-234">或者，将 `foreach` 循环用于 <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>：</span><span class="sxs-lookup"><span data-stu-id="42263-234">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="42263-235">属性展开和任意参数</span><span class="sxs-lookup"><span data-stu-id="42263-235">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="42263-236">除了组件的声明参数外，组件还可以捕获和呈现其他属性。</span><span class="sxs-lookup"><span data-stu-id="42263-236">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="42263-237">其他属性可以在字典中捕获，然后在使用 [`@attributes`][3] Razor 指令呈现组件时，将其展开到元素上。</span><span class="sxs-lookup"><span data-stu-id="42263-237">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="42263-238">此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。</span><span class="sxs-lookup"><span data-stu-id="42263-238">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="42263-239">例如，为支持多个参数的 `<input>` 单独定义属性可能比较繁琐。</span><span class="sxs-lookup"><span data-stu-id="42263-239">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="42263-240">在下面的示例中，第一个 `<input>` 元素 (`id="useIndividualParams"`) 使用单个组件参数，第二个 `<input>` 元素 (`id="useAttributesDict"`) 使用属性展开：</span><span class="sxs-lookup"><span data-stu-id="42263-240">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

<span data-ttu-id="42263-241">参数的类型必须使用字符串键实现 `IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>`。</span><span class="sxs-lookup"><span data-stu-id="42263-241">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="42263-242">使用这两种方法呈现的 `<input>` 元素相同：</span><span class="sxs-lookup"><span data-stu-id="42263-242">The rendered `<input>` elements using both approaches is identical:</span></span>

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

<span data-ttu-id="42263-243">若要接受任意特性，请使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性定义组件参数，并将 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="42263-243">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="42263-244">借助 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 上的 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 属性，参数可以匹配所有不匹配其他任何参数的特性。</span><span class="sxs-lookup"><span data-stu-id="42263-244">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="42263-245">组件只能使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 定义单个参数。</span><span class="sxs-lookup"><span data-stu-id="42263-245">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="42263-246">与 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 一起使用的属性类型必须可以使用字符串键从 `Dictionary<string, object>` 中分配。</span><span class="sxs-lookup"><span data-stu-id="42263-246">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="42263-247">`IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>` 也是此方案中的选项。</span><span class="sxs-lookup"><span data-stu-id="42263-247">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="42263-248">相对于元素特性位置的 [`@attributes`][3] 位置很重要。</span><span class="sxs-lookup"><span data-stu-id="42263-248">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="42263-249">在元素上展开 [`@attributes`][3] 时，将从右到左（从最后一个到第一个）处理特性。</span><span class="sxs-lookup"><span data-stu-id="42263-249">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="42263-250">请考虑以下使用 `Child` 组件的组件示例：</span><span class="sxs-lookup"><span data-stu-id="42263-250">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="42263-251">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-251">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="42263-252">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-252">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="42263-253">`Child` 组件的 `extra` 属性设置为 [`@attributes`][3] 右侧。</span><span class="sxs-lookup"><span data-stu-id="42263-253">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="42263-254">通过附加特性传递时，`Parent` 组件的呈现的 `<div>` 包含 `extra="5"`，因为特性是从右到左（从最后一个到第一个）处理的：</span><span class="sxs-lookup"><span data-stu-id="42263-254">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="42263-255">在下面的示例中，`extra` 和 [`@attributes`][3] 的顺序在 `Child` 组件的 `<div>` 中反转：</span><span class="sxs-lookup"><span data-stu-id="42263-255">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="42263-256">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-256">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="42263-257">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="42263-257">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="42263-258">通过附加属性传递时，`Parent` 组件中呈现的 `<div>` 包含 `extra="10"`：</span><span class="sxs-lookup"><span data-stu-id="42263-258">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="42263-259">捕获对组件的引用</span><span class="sxs-lookup"><span data-stu-id="42263-259">Capture references to components</span></span>

<span data-ttu-id="42263-260">组件引用提供了一种引用组件实例的方法，以便可以向该实例发出命令，例如 `Show` 或 `Reset`。</span><span class="sxs-lookup"><span data-stu-id="42263-260">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="42263-261">若要捕获组件引用，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="42263-261">To capture a component reference:</span></span>

* <span data-ttu-id="42263-262">向子组件添加 [`@ref`][4] 特性。</span><span class="sxs-lookup"><span data-stu-id="42263-262">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="42263-263">定义与子组件类型相同的字段。</span><span class="sxs-lookup"><span data-stu-id="42263-263">Define a field with the same type as the child component.</span></span>

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

<span data-ttu-id="42263-264">呈现组件时，将用 `CustomLoginDialog` 子组件实例填充 `loginDialog` 字段。</span><span class="sxs-lookup"><span data-stu-id="42263-264">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="42263-265">然后，可以在组件实例上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="42263-265">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="42263-266">仅在呈现组件后填充 `loginDialog` 变量，其输出包含 `MyLoginDialog` 元素。</span><span class="sxs-lookup"><span data-stu-id="42263-266">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="42263-267">在呈现组件之前，没有任何可引用的内容。</span><span class="sxs-lookup"><span data-stu-id="42263-267">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="42263-268">若要在组件完成呈现后操作组件引用，请使用 [`OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)。</span><span class="sxs-lookup"><span data-stu-id="42263-268">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="42263-269">若要结合使用事件处理程序和引用变量，请使用 Lambda 表达式，或在 [`OnAfterRenderAsync` 或 `OnAfterRender` 方法](xref:blazor/components/lifecycle#after-component-render)中分配事件处理程序委托。</span><span class="sxs-lookup"><span data-stu-id="42263-269">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="42263-270">这可确保在分配事件处理程序之前先分配引用变量。</span><span class="sxs-lookup"><span data-stu-id="42263-270">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

<span data-ttu-id="42263-271">要引用循环中的组件，请参阅[捕获对多个相似子组件的引用 (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358)。</span><span class="sxs-lookup"><span data-stu-id="42263-271">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="42263-272">尽管捕获组件引用使用与[捕获元素引用](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements)类似的语法，但它不是 JavaScript 互操作功能。</span><span class="sxs-lookup"><span data-stu-id="42263-272">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="42263-273">组件引用不会传递给 JavaScript 代码。</span><span class="sxs-lookup"><span data-stu-id="42263-273">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="42263-274">组件引用只在 .NET 代码中使用。</span><span class="sxs-lookup"><span data-stu-id="42263-274">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="42263-275">不要使用组件引用来改变子组件的状态。</span><span class="sxs-lookup"><span data-stu-id="42263-275">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="42263-276">请改用常规声明性参数将数据传递给子组件。</span><span class="sxs-lookup"><span data-stu-id="42263-276">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="42263-277">使用常规声明性参数使子组件在正确的时间自动重新呈现。</span><span class="sxs-lookup"><span data-stu-id="42263-277">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="42263-278">同步上下文</span><span class="sxs-lookup"><span data-stu-id="42263-278">Synchronization context</span></span>

<span data-ttu-id="42263-279">Blazor 使用同步上下文 (<xref:System.Threading.SynchronizationContext>) 来强制执行单个逻辑线程。</span><span class="sxs-lookup"><span data-stu-id="42263-279">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="42263-280">组件的[生命周期方法](xref:blazor/components/lifecycle)和 Blazor 引发的任何事件回调都在此同步上下文上执行。</span><span class="sxs-lookup"><span data-stu-id="42263-280">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="42263-281">Blazor Server的同步上下文尝试模拟单线程环境，使其与浏览器中的单线程 WebAssembly 模型紧密匹配。</span><span class="sxs-lookup"><span data-stu-id="42263-281">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="42263-282">在任意给定的时间点，工作只在一个线程上执行，从而造成单个逻辑线程的印象。</span><span class="sxs-lookup"><span data-stu-id="42263-282">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="42263-283">不会同时执行两个操作。</span><span class="sxs-lookup"><span data-stu-id="42263-283">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="42263-284">避免阻止线程的调用</span><span class="sxs-lookup"><span data-stu-id="42263-284">Avoid thread-blocking calls</span></span>

<span data-ttu-id="42263-285">通常，不要调用以下方法。</span><span class="sxs-lookup"><span data-stu-id="42263-285">Generally, don't call the following methods.</span></span> <span data-ttu-id="42263-286">以下方法阻止线程，进而阻止应用继续工作，直到基础 <xref:System.Threading.Tasks.Task> 完成：</span><span class="sxs-lookup"><span data-stu-id="42263-286">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="42263-287">在外部调用组件方法以更新状态</span><span class="sxs-lookup"><span data-stu-id="42263-287">Invoke component methods externally to update state</span></span>

<span data-ttu-id="42263-288">如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用 `InvokeAsync` 方法来调度到 Blazor 的同步上下文。</span><span class="sxs-lookup"><span data-stu-id="42263-288">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="42263-289">例如，假设有一个可通知任何侦听组件更新状态的通告程序服务：</span><span class="sxs-lookup"><span data-stu-id="42263-289">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

<span data-ttu-id="42263-290">注册 `NotifierService`：</span><span class="sxs-lookup"><span data-stu-id="42263-290">Register the `NotifierService`:</span></span>

* <span data-ttu-id="42263-291">在 Blazor WebAssembly 中，在 `Program.Main` 中将服务注册为单一实例：</span><span class="sxs-lookup"><span data-stu-id="42263-291">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="42263-292">在 Blazor Server 中，在 `Startup.ConfigureServices` 中将服务注册为有作用域：</span><span class="sxs-lookup"><span data-stu-id="42263-292">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="42263-293">使用 `NotifierService` 更新组件：</span><span class="sxs-lookup"><span data-stu-id="42263-293">Use the `NotifierService` to update a component:</span></span>

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

<span data-ttu-id="42263-294">在前面的示例中，`NotifierService` 在 Blazor 的同步上下文之外调用组件的 `OnNotify` 方法。</span><span class="sxs-lookup"><span data-stu-id="42263-294">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="42263-295">`InvokeAsync` 用于切换到正确的上下文，并将呈现排入队列。</span><span class="sxs-lookup"><span data-stu-id="42263-295">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="42263-296">有关详细信息，请参阅 <xref:blazor/components/rendering>。</span><span class="sxs-lookup"><span data-stu-id="42263-296">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="42263-297">使用 \@ 键控制是否保留元素和组件</span><span class="sxs-lookup"><span data-stu-id="42263-297">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="42263-298">在呈现元素或组件的列表并且元素或组件随后发生更改时，Blazor 的比较算法必须决定之前的元素或组件中有哪些可以保留，以及模型对象应如何映射到这些元素或组件。</span><span class="sxs-lookup"><span data-stu-id="42263-298">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="42263-299">通常，此过程是自动的，可以忽略，但在某些情况下，可能需要控制该过程。</span><span class="sxs-lookup"><span data-stu-id="42263-299">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="42263-300">请看下面的示例：</span><span class="sxs-lookup"><span data-stu-id="42263-300">Consider the following example:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="42263-301">`People` 集合的内容可能会随插入、删除或重新排序的条目而更改。</span><span class="sxs-lookup"><span data-stu-id="42263-301">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="42263-302">当组件重新呈现时，`<DetailsEditor>` 组件可能会更改以接收不同的 `Details` 参数值。</span><span class="sxs-lookup"><span data-stu-id="42263-302">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="42263-303">这可能导致重新呈现比预期更复杂。</span><span class="sxs-lookup"><span data-stu-id="42263-303">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="42263-304">在某些情况下，重新呈现可能会导致可见行为差异，如失去元素焦点。</span><span class="sxs-lookup"><span data-stu-id="42263-304">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="42263-305">可通过 [`@key`][5] 指令属性来控制映射过程。</span><span class="sxs-lookup"><span data-stu-id="42263-305">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="42263-306">[`@key`][5] 使比较算法保证基于键的值保留元素或组件：</span><span class="sxs-lookup"><span data-stu-id="42263-306">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

<span data-ttu-id="42263-307">当 `People` 集合发生更改时，比较算法会保留 `<DetailsEditor>` 实例与 `person` 实例之间的关联：</span><span class="sxs-lookup"><span data-stu-id="42263-307">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="42263-308">如果从 `People` 列表中删除 `Person`，则仅从 UI 中删除相应的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="42263-308">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="42263-309">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="42263-309">Other instances are left unchanged.</span></span>
* <span data-ttu-id="42263-310">如果在列表中的某个位置插入 `Person`，则会在相应位置插入一个新的 `<DetailsEditor>` 实例。</span><span class="sxs-lookup"><span data-stu-id="42263-310">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="42263-311">其他实例保持不变。</span><span class="sxs-lookup"><span data-stu-id="42263-311">Other instances are left unchanged.</span></span>
* <span data-ttu-id="42263-312">如果对 `Person` 项进行重新排序，则保留相应的 `<DetailsEditor>` 实例，并在 UI 中重新排序。</span><span class="sxs-lookup"><span data-stu-id="42263-312">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="42263-313">在某些场景中，使用 [`@key`][5] 可最大程度降低重新呈现的复杂性，并避免在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。</span><span class="sxs-lookup"><span data-stu-id="42263-313">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="42263-314">对于每个容器元素或组件而言，键是本地的。</span><span class="sxs-lookup"><span data-stu-id="42263-314">Keys are local to each container element or component.</span></span> <span data-ttu-id="42263-315">不会在整个文档中全局地比较键。</span><span class="sxs-lookup"><span data-stu-id="42263-315">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="42263-316">何时使用 \@key</span><span class="sxs-lookup"><span data-stu-id="42263-316">When to use \@key</span></span>

<span data-ttu-id="42263-317">通常，每当列表呈现（例如，在 [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) 块中）且存在合适的值来定义 [`@key`][5] 时，最好使用 [`@key`][5]。</span><span class="sxs-lookup"><span data-stu-id="42263-317">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="42263-318">还可以使用 [`@key`][5] 来防止 Blazor 在对象发生更改时保留元素或组件子树：</span><span class="sxs-lookup"><span data-stu-id="42263-318">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="42263-319">如果 `@currentPerson` 更改，则 [`@key`][5] 属性指令强制 Blazor 放弃整个 `<div>` 及其子代，并利用新的元素和组件在 UI 中重新生成子树。</span><span class="sxs-lookup"><span data-stu-id="42263-319">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="42263-320">如果需要确保在 `@currentPerson` 更改时不保留 UI 状态，这会很有用。</span><span class="sxs-lookup"><span data-stu-id="42263-320">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="42263-321">何时不使用 \@key</span><span class="sxs-lookup"><span data-stu-id="42263-321">When not to use \@key</span></span>

<span data-ttu-id="42263-322">与 [`@key`][5] 进行比较时，会产生性能成本。</span><span class="sxs-lookup"><span data-stu-id="42263-322">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="42263-323">性能成本不是很大，但仅在控制元素或组件保留规则对应用有利的情况下才指定 [`@key`][5]。</span><span class="sxs-lookup"><span data-stu-id="42263-323">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="42263-324">即使未使用 [`@key`][5]，Blazor 也会尽可能地保留子元素和组件实例。</span><span class="sxs-lookup"><span data-stu-id="42263-324">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="42263-325">使用 [`@key`][5] 的唯一优点是控制如何将模型实例映射到保留的组件实例，而不是选择映射的比较算法。</span><span class="sxs-lookup"><span data-stu-id="42263-325">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="42263-326">\@key 使用哪些值</span><span class="sxs-lookup"><span data-stu-id="42263-326">What values to use for \@key</span></span>

<span data-ttu-id="42263-327">通常，为 [`@key`][5] 提供以下类型之一的值：</span><span class="sxs-lookup"><span data-stu-id="42263-327">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="42263-328">模型对象实例（例如，先前示例中的 `Person` 实例）。</span><span class="sxs-lookup"><span data-stu-id="42263-328">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="42263-329">这可确保基于对象引用相等性的保留。</span><span class="sxs-lookup"><span data-stu-id="42263-329">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="42263-330">唯一标识符（例如，`int`、`string` 或 `Guid` 类型的主键值）。</span><span class="sxs-lookup"><span data-stu-id="42263-330">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="42263-331">确保用于 [`@key`][5] 的值不冲突。</span><span class="sxs-lookup"><span data-stu-id="42263-331">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="42263-332">如果在同一父元素内检测到冲突值，则 Blazor 引发异常，因为它无法明确地将旧元素或组件映射到新元素或组件。</span><span class="sxs-lookup"><span data-stu-id="42263-332">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="42263-333">仅使用非重复值，例如对象实例或主键值。</span><span class="sxs-lookup"><span data-stu-id="42263-333">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="42263-334">重写参数</span><span class="sxs-lookup"><span data-stu-id="42263-334">Overwritten parameters</span></span>

<span data-ttu-id="42263-335">Blazor 框架通常会施加安全的父级到子级参数的赋值：</span><span class="sxs-lookup"><span data-stu-id="42263-335">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="42263-336">不会意外覆盖参数。</span><span class="sxs-lookup"><span data-stu-id="42263-336">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="42263-337">最大程度地减少副作用。</span><span class="sxs-lookup"><span data-stu-id="42263-337">Side-effects are minimized.</span></span> <span data-ttu-id="42263-338">例如，可避免附加的呈现，因为它们可能会创建无限的呈现循环。</span><span class="sxs-lookup"><span data-stu-id="42263-338">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="42263-339">当父组件重新呈现时，子组件会接收可能覆盖现有值的新参数值。</span><span class="sxs-lookup"><span data-stu-id="42263-339">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="42263-340">当使用一个或多个数据绑定参数开发组件并且开发人员直接写入子组件中的参数时，通常会发生意外覆盖子组件中的参数值：</span><span class="sxs-lookup"><span data-stu-id="42263-340">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="42263-341">子组件通过父组件中的一个或多个参数值呈现。</span><span class="sxs-lookup"><span data-stu-id="42263-341">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="42263-342">子级直接写入参数的值。</span><span class="sxs-lookup"><span data-stu-id="42263-342">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="42263-343">父组件重新呈现并覆盖子参数的值。</span><span class="sxs-lookup"><span data-stu-id="42263-343">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="42263-344">覆盖参数值的可能性也会扩展到子组件的属性资源库中。</span><span class="sxs-lookup"><span data-stu-id="42263-344">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="42263-345">我们的通用指南不是创建直接写入其自身参数的组件。</span><span class="sxs-lookup"><span data-stu-id="42263-345">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="42263-346">请考虑使用以下有故障的 `Expander` 组件，它们会：</span><span class="sxs-lookup"><span data-stu-id="42263-346">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="42263-347">呈现子内容。</span><span class="sxs-lookup"><span data-stu-id="42263-347">Renders child content.</span></span>
* <span data-ttu-id="42263-348">切换以使用组件参数 (`Expanded`) 显示子内容。</span><span class="sxs-lookup"><span data-stu-id="42263-348">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="42263-349">组件直接写入 `Expanded` 参数，该参数演示了覆盖的参数存在的问题，应避免这样做。</span><span class="sxs-lookup"><span data-stu-id="42263-349">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

<span data-ttu-id="42263-350">`Expander` 组件会添加到可调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>的父组件中：</span><span class="sxs-lookup"><span data-stu-id="42263-350">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

<span data-ttu-id="42263-351">最初，在切换 `Expanded` 属性时，`Expander` 组件独立地作出行为。</span><span class="sxs-lookup"><span data-stu-id="42263-351">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="42263-352">子组件会按预期方式维护其状态。</span><span class="sxs-lookup"><span data-stu-id="42263-352">The child components maintain their states as expected.</span></span> <span data-ttu-id="42263-353">在父组件中调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 时，第一个子组件的 `Expanded` 会重新重置为它的初始值 (`true`)。</span><span class="sxs-lookup"><span data-stu-id="42263-353">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="42263-354">第二个 `Expander` 组件的 `Expanded` 值不会重置，因为第二个组件中没有呈现任何子内容。</span><span class="sxs-lookup"><span data-stu-id="42263-354">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="42263-355">要维持在前述情况中的状态，请在 `Expander` 组件中使用私有字段来保留它的切换状态。</span><span class="sxs-lookup"><span data-stu-id="42263-355">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="42263-356">以下经修定的 `Expander` 组件：</span><span class="sxs-lookup"><span data-stu-id="42263-356">The following revised `Expander` component:</span></span>

* <span data-ttu-id="42263-357">接受父项中的 `Expanded` 组件参数值。</span><span class="sxs-lookup"><span data-stu-id="42263-357">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="42263-358">将组件参数值分配给 [OnInitialized 事件](xref:blazor/components/lifecycle#component-initialization-methods)中的私有字段 (`expanded`)。</span><span class="sxs-lookup"><span data-stu-id="42263-358">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="42263-359">使用私有字段来维护其内部切换状态，该状态演示如何避免直接写入参数。</span><span class="sxs-lookup"><span data-stu-id="42263-359">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

<span data-ttu-id="42263-360">有关其他信息，请参阅 [Blazor 双向绑定错误 (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599)。</span><span class="sxs-lookup"><span data-stu-id="42263-360">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="42263-361">应用属性</span><span class="sxs-lookup"><span data-stu-id="42263-361">Apply an attribute</span></span>

<span data-ttu-id="42263-362">可以通过 [`@attribute`][7] 指令在 Razor 组件中应用属性。</span><span class="sxs-lookup"><span data-stu-id="42263-362">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="42263-363">下面的示例将 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 特性应用于组件类：</span><span class="sxs-lookup"><span data-stu-id="42263-363">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="42263-364">条件 HTML 元素属性</span><span class="sxs-lookup"><span data-stu-id="42263-364">Conditional HTML element attributes</span></span>

<span data-ttu-id="42263-365">HTML 元素属性基于 .NET 值有条件地呈现。</span><span class="sxs-lookup"><span data-stu-id="42263-365">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="42263-366">如果值为 `false` 或 `null`，则不会呈现属性。</span><span class="sxs-lookup"><span data-stu-id="42263-366">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="42263-367">如果值为 `true`，则以最小化呈现属性。</span><span class="sxs-lookup"><span data-stu-id="42263-367">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="42263-368">在下面的示例中，`IsCompleted` 确定是否在元素的标记中呈现 `checked`：</span><span class="sxs-lookup"><span data-stu-id="42263-368">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="42263-369">如果 `IsCompleted` 为 `true`，则复选框呈现为：</span><span class="sxs-lookup"><span data-stu-id="42263-369">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="42263-370">如果 `IsCompleted` 为 `false`，则复选框呈现为：</span><span class="sxs-lookup"><span data-stu-id="42263-370">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="42263-371">有关详细信息，请参阅 [ASP.NET Core 的 Razor 语法参考](xref:mvc/views/razor)。</span><span class="sxs-lookup"><span data-stu-id="42263-371">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="42263-372">.NET 类型为 `bool` 时，某些 HTML 属性（如 [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）无法正常运行。</span><span class="sxs-lookup"><span data-stu-id="42263-372">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="42263-373">在这些情况下，请使用 `string` 类型，而不是 `bool`。</span><span class="sxs-lookup"><span data-stu-id="42263-373">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="42263-374">原始 HTML</span><span class="sxs-lookup"><span data-stu-id="42263-374">Raw HTML</span></span>

<span data-ttu-id="42263-375">通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文字文本。</span><span class="sxs-lookup"><span data-stu-id="42263-375">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="42263-376">若要呈现原始 HTML，请将 HTML 内容包装在 `MarkupString` 值中。</span><span class="sxs-lookup"><span data-stu-id="42263-376">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="42263-377">将该值分析为 HTML 或 SVG，并插入到 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="42263-377">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="42263-378">呈现从任何不受信任的源构造的原始 HTML 存在安全风险，应避免！</span><span class="sxs-lookup"><span data-stu-id="42263-378">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="42263-379">下面的示例演示如何使用 `MarkupString` 类型向组件的呈现输出添加静态 HTML 内容块：</span><span class="sxs-lookup"><span data-stu-id="42263-379">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="42263-380">Razor 模板</span><span class="sxs-lookup"><span data-stu-id="42263-380">Razor templates</span></span>

<span data-ttu-id="42263-381">可以使用 Razor 模板语法来定义呈现片段。</span><span class="sxs-lookup"><span data-stu-id="42263-381">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="42263-382">Razor 模板是一种定义 UI 代码片段的方法，请使用以下格式：</span><span class="sxs-lookup"><span data-stu-id="42263-382">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="42263-383">下面的示例演示如何在组件中指定 <xref:Microsoft.AspNetCore.Components.RenderFragment> 和 <xref:Microsoft.AspNetCore.Components.RenderFragment%601> 值并直接呈现模板。</span><span class="sxs-lookup"><span data-stu-id="42263-383">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="42263-384">还可以将呈现片段作为参数传递给[模板化组件](xref:blazor/components/templated-components)。</span><span class="sxs-lookup"><span data-stu-id="42263-384">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

<span data-ttu-id="42263-385">以上代码的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="42263-385">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="42263-386">静态资产</span><span class="sxs-lookup"><span data-stu-id="42263-386">Static assets</span></span>

<span data-ttu-id="42263-387">Blazor 遵循 ASP.NET Core 应用的约定，将静态资产放在项目的 [`web root (wwwroot)` 文件夹](xref:fundamentals/index#web-root)下。</span><span class="sxs-lookup"><span data-stu-id="42263-387">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="42263-388">使用基相对路径 (`/`) 来引用静态资产的 Web 根。</span><span class="sxs-lookup"><span data-stu-id="42263-388">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="42263-389">在下面的示例中，`logo.png` 实际位于 `{PROJECT ROOT}/wwwroot/images` 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="42263-389">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="42263-390">Razor 组件不支持波浪符斜杠表示法 (`~/`)。</span><span class="sxs-lookup"><span data-stu-id="42263-390">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="42263-391">有关设置应用基路径的信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="42263-391">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="42263-392">组件中不支持标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="42263-392">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="42263-393">Razor 组件（`.razor` 文件）不支持 [`Tag Helpers`](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="42263-393">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="42263-394">若要在 Blazor 中提供类似标记帮助程序的功能，请创建一个具有与标记帮助程序相同功能的组件，并改为使用该组件。</span><span class="sxs-lookup"><span data-stu-id="42263-394">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="42263-395">可缩放的向量图形 (SVG) 图像</span><span class="sxs-lookup"><span data-stu-id="42263-395">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="42263-396">由于 Blazor 呈现 HTML，因此通过 `<img>` 标记支持浏览器支持的图像，包括可缩放的矢量图形 (SVG) 图像(`.svg`)：</span><span class="sxs-lookup"><span data-stu-id="42263-396">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="42263-397">同样，样式表文件 (`.css`) 的 CSS 规则支持 SVG 图像：</span><span class="sxs-lookup"><span data-stu-id="42263-397">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="42263-398">但是，并非在所有情况下都支持内联的 SVG 标记。</span><span class="sxs-lookup"><span data-stu-id="42263-398">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="42263-399">如果将 `<svg>` 标记直接放入组件文件 (`.razor`)，则支持基本图像呈现，但很多高级场景尚不受支持。</span><span class="sxs-lookup"><span data-stu-id="42263-399">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="42263-400">例如，当前未遵循 `<use>` 标记，并且 [`@bind`][10] 不能与某些 SVG 标记一起使用。</span><span class="sxs-lookup"><span data-stu-id="42263-400">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="42263-401">有关详细信息，请参阅 [Blazor (dotnet/aspnetcore #18271) 中的 SVG 支持](https://github.com/dotnet/aspnetcore/issues/18271)。</span><span class="sxs-lookup"><span data-stu-id="42263-401">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="42263-402">空白的呈现行为</span><span class="sxs-lookup"><span data-stu-id="42263-402">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="42263-403">除非将 [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) 指令与值 `true` 一起使用，否则在以下情况下默认删除额外的空白：</span><span class="sxs-lookup"><span data-stu-id="42263-403">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="42263-404">元素中的前导或尾随空白。</span><span class="sxs-lookup"><span data-stu-id="42263-404">Leading or trailing within an element.</span></span>
* <span data-ttu-id="42263-405">`RenderFragment` 参数中的前导或尾随空白。</span><span class="sxs-lookup"><span data-stu-id="42263-405">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="42263-406">例如，传递到另一个组件的子内容。</span><span class="sxs-lookup"><span data-stu-id="42263-406">For example, child content passed to another component.</span></span>
* <span data-ttu-id="42263-407">在 C# 代码块（例如 `@if` 和 `@foreach`）之前或之后。</span><span class="sxs-lookup"><span data-stu-id="42263-407">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="42263-408">但是，在使用 CSS 规则（例如 `white-space: pre`）时，删除空白可能会影响呈现输出。</span><span class="sxs-lookup"><span data-stu-id="42263-408">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="42263-409">若要禁用此性能优化并保留空白，请执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="42263-409">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="42263-410">将 `@preservewhitespace true` 指令添加到 `.razor` 文件的顶部，从而将首选项应用于特定组件。</span><span class="sxs-lookup"><span data-stu-id="42263-410">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="42263-411">将 `@preservewhitespace true` 指令添加到 `_Imports.razor` 文件中，从而将首选项应用于整个子目录或整个项目。</span><span class="sxs-lookup"><span data-stu-id="42263-411">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="42263-412">在大多数情况下，不需要执行任何操作，因为应用程序通常会继续正常运行（但速度会更快）。</span><span class="sxs-lookup"><span data-stu-id="42263-412">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="42263-413">如果去除空白会导致特定组件出现任何问题，请在该组件中使用 `@preservewhitespace true` 来禁用此优化。</span><span class="sxs-lookup"><span data-stu-id="42263-413">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="42263-414">空白将保留在组件的源代码中。</span><span class="sxs-lookup"><span data-stu-id="42263-414">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="42263-415">即使在没有视觉效果的情况下，只有空白的文本也会呈现在浏览器的文档对象模型 (DOM) 中。</span><span class="sxs-lookup"><span data-stu-id="42263-415">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="42263-416">请考虑使用以下 Razor 组件代码：</span><span class="sxs-lookup"><span data-stu-id="42263-416">Consider the following Razor component code:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="42263-417">前面的示例呈现以下不必要的空白：</span><span class="sxs-lookup"><span data-stu-id="42263-417">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="42263-418">在 `@foreach` 代码块外。</span><span class="sxs-lookup"><span data-stu-id="42263-418">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="42263-419">围绕 `<li>` 元素。</span><span class="sxs-lookup"><span data-stu-id="42263-419">Around the `<li>` element.</span></span>
* <span data-ttu-id="42263-420">围绕 `@item.Text` 输出。</span><span class="sxs-lookup"><span data-stu-id="42263-420">Around the `@item.Text` output.</span></span>

<span data-ttu-id="42263-421">包含 100 项的列表将导致 402 个空白区域，并且任何额外的空白都不会在视觉上影响呈现的输出。</span><span class="sxs-lookup"><span data-stu-id="42263-421">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="42263-422">呈现组件的静态 HTML 时，不会保留标记中的空白。</span><span class="sxs-lookup"><span data-stu-id="42263-422">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="42263-423">例如，在呈现的输出中查看以下组件的源：</span><span class="sxs-lookup"><span data-stu-id="42263-423">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="42263-424">不会保留前面 Razor 标记中的空白：</span><span class="sxs-lookup"><span data-stu-id="42263-424">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="42263-425">其他资源</span><span class="sxs-lookup"><span data-stu-id="42263-425">Additional resources</span></span>

* <span data-ttu-id="42263-426"><xref:blazor/security/server/threat-mitigation>：包括有关如何生成必须应对资源耗尽的 Blazor Server应用的指南。</span><span class="sxs-lookup"><span data-stu-id="42263-426"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "ASP.NET Core 的 Razor 语法参考"
[2]: <xref:mvc/views/razor#using> "ASP.NET Core 的 Razor 语法参考"
[3]: <xref:mvc/views/razor#attributes> "ASP.NET Core 的 Razor 语法参考"
[4]: <xref:mvc/views/razor#ref> "ASP.NET Core 的 Razor 语法参考"
[5]: <xref:mvc/views/razor#key> "ASP.NET Core 的 Razor 语法参考"
[6]: <xref:mvc/views/razor#inherits> "ASP.NET Core 的 Razor 语法参考"
[7]: <xref:mvc/views/razor#attribute> "ASP.NET Core 的 Razor 语法参考"
[8]: <xref:mvc/views/razor#namespace> "ASP.NET Core 的 Razor 语法参考"
[9]: <xref:mvc/views/razor#page> "ASP.NET Core 的 Razor 语法参考"
[10]: <xref:mvc/views/razor#bind> "ASP.NET Core 的 Razor 语法参考"
