---
title: ASP.NET Core Blazor WebAssembly 性能最佳做法
author: pranavkm
description: 用于提高 ASP.NET Core Blazor WebAssembly 应用性能并避免常见性能问题的提示。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, devx-track-js
ms.date: 10/09/2020
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
uid: blazor/webassembly-performance-best-practices
ms.openlocfilehash: 58a87bc5413523fdf052a9e1c41196bb8b0ab457
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529964"
---
# <a name="aspnet-core-blazor-webassembly-performance-best-practices"></a><span data-ttu-id="26b9f-103">ASP.NET Core Blazor WebAssembly 性能最佳做法</span><span class="sxs-lookup"><span data-stu-id="26b9f-103">ASP.NET Core Blazor WebAssembly performance best practices</span></span>

<span data-ttu-id="26b9f-104">作者：[Pranav Krishnamoorthy](https://github.com/pranavkm) 和 [Steve Sanderson](https://github.com/SteveSandersonMS)</span><span class="sxs-lookup"><span data-stu-id="26b9f-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Steve Sanderson](https://github.com/SteveSandersonMS)</span></span>

<span data-ttu-id="26b9f-105">Blazor WebAssembly 经过精心设计和优化，可在最真实的应用程序 UI 方案中提高性能。</span><span class="sxs-lookup"><span data-stu-id="26b9f-105">Blazor WebAssembly is carefully designed and optimized to enable high performance in most realistic application UI scenarios.</span></span> <span data-ttu-id="26b9f-106">但是，仅当开发人员使用正确的模式和功能时才会产生最佳结果。</span><span class="sxs-lookup"><span data-stu-id="26b9f-106">However, producing the best results depends on developers using the right patterns and features.</span></span> <span data-ttu-id="26b9f-107">请考虑以下方面：</span><span class="sxs-lookup"><span data-stu-id="26b9f-107">Consider the following aspects:</span></span>

* <span data-ttu-id="26b9f-108">运行时吞吐量：.NET 代码在 WebAssembly 运行时内在解释器上运行，因此 CPU 吞吐量会受到限制。</span><span class="sxs-lookup"><span data-stu-id="26b9f-108">**Runtime throughput**: The .NET code runs on an interpreter within the WebAssembly runtime, so CPU throughput is limited.</span></span> <span data-ttu-id="26b9f-109">在要求苛刻的方案中，应用受益于[优化呈现速度](#optimize-rendering-speed)。</span><span class="sxs-lookup"><span data-stu-id="26b9f-109">In demanding scenarios, the app benefits from [optimizing rendering speed](#optimize-rendering-speed).</span></span>
* <span data-ttu-id="26b9f-110">启动时间：应用会将 .NET 运行时传输到浏览器，因此，使用[最小化应用程序下载大小](#minimize-app-download-size)的功能非常重要。</span><span class="sxs-lookup"><span data-stu-id="26b9f-110">**Startup time**: The app transfers a .NET runtime to the browser, so it's important to use features that [minimize the application download size](#minimize-app-download-size).</span></span>

## <a name="optimize-rendering-speed"></a><span data-ttu-id="26b9f-111">优化呈现速度</span><span class="sxs-lookup"><span data-stu-id="26b9f-111">Optimize rendering speed</span></span>

<span data-ttu-id="26b9f-112">以下部分提供最小化呈现工作负载和提升 UI 响应能力的建议。</span><span class="sxs-lookup"><span data-stu-id="26b9f-112">The following sections provide recommendations to minimize rendering workload and improve UI responsiveness.</span></span> <span data-ttu-id="26b9f-113">遵循此建议可以轻松地以 UI 呈现速度提升 10 倍或更高。</span><span class="sxs-lookup"><span data-stu-id="26b9f-113">Following this advice could easily make a *ten-fold or higher improvement* in UI rendering speeds.</span></span>

### <a name="avoid-unnecessary-rendering-of-component-subtrees"></a><span data-ttu-id="26b9f-114">避免不必要地呈现组件子树</span><span class="sxs-lookup"><span data-stu-id="26b9f-114">Avoid unnecessary rendering of component subtrees</span></span>

<span data-ttu-id="26b9f-115">在运行时，组件作为层次结构存在。</span><span class="sxs-lookup"><span data-stu-id="26b9f-115">At runtime, components exist as a hierarchy.</span></span> <span data-ttu-id="26b9f-116">根组件具有子组件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-116">A root component has child components.</span></span> <span data-ttu-id="26b9f-117">反过来，根的子项也有其自己的子组件，依此类推。</span><span class="sxs-lookup"><span data-stu-id="26b9f-117">In turn, the root's children have their own child components, and so on.</span></span> <span data-ttu-id="26b9f-118">发生事件（例如用户选择某个按钮）时，这就是 Blazor 如何决定要重新呈现哪些组件：</span><span class="sxs-lookup"><span data-stu-id="26b9f-118">When an event occurs, such as a user selecting a button, this is how Blazor decides which components to rerender:</span></span>

1. <span data-ttu-id="26b9f-119">事件本身将被调度到呈现事件处理程序的任何组件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-119">The event itself is dispatched to whichever component rendered the event's handler.</span></span> <span data-ttu-id="26b9f-120">执行事件处理程序后，将重新呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-120">After executing the event handler, that component is rerendered.</span></span>
1. <span data-ttu-id="26b9f-121">每当重新呈现任何组件时，都会向其每个子组件提供参数值的新副本。</span><span class="sxs-lookup"><span data-stu-id="26b9f-121">Whenever any component is rerendered, it supplies a new copy of the parameter values to each of its child components.</span></span>
1. <span data-ttu-id="26b9f-122">接收一组新的参数值时，每个组件都会选择是否要重新呈现。</span><span class="sxs-lookup"><span data-stu-id="26b9f-122">When receiving a new set of parameter values, each component chooses whether to rerender.</span></span> <span data-ttu-id="26b9f-123">默认情况下，如果参数值可能已更改（例如，如果它们是可变对象），则组件重新呈现。</span><span class="sxs-lookup"><span data-stu-id="26b9f-123">By default, components rerender if the parameter values may have changed (for example, if they are mutable objects).</span></span>

<span data-ttu-id="26b9f-124">此序列的最后两个步骤以递归方式沿着组件层次结构继续向下。</span><span class="sxs-lookup"><span data-stu-id="26b9f-124">The last two steps of this sequence continue recursively down the component hierarchy.</span></span> <span data-ttu-id="26b9f-125">在许多情况下，将重新呈现整个子树。</span><span class="sxs-lookup"><span data-stu-id="26b9f-125">In many cases, the entire subtree is rerendered.</span></span> <span data-ttu-id="26b9f-126">这意味着，针对高级组件的事件可能会导致成本高昂的重新呈现过程，因为必须重新呈现该点之下的所有内容。</span><span class="sxs-lookup"><span data-stu-id="26b9f-126">This means that events targeting high-level components can cause expensive rerendering processes because everything below that point must be rerendered.</span></span>

<span data-ttu-id="26b9f-127">如果要中断此过程并防止将递归呈现到特定子树中，则可以执行以下任一操作：</span><span class="sxs-lookup"><span data-stu-id="26b9f-127">If you want to interrupt this process and prevent rendering recursion into a particular subtree, then you can either:</span></span>

* <span data-ttu-id="26b9f-128">确保某个组件的所有参数均属于基元不可变类型（例如，`string`、`int`、`bool`、`DateTime` 等其他类型）。</span><span class="sxs-lookup"><span data-stu-id="26b9f-128">Ensure that all parameters to a certain component are of primitive immutable types (for example, `string`, `int`, `bool`, `DateTime`, and others).</span></span> <span data-ttu-id="26b9f-129">如果这些参数值均未更改，则用于检测更改的内置逻辑会自动跳过重新呈现。</span><span class="sxs-lookup"><span data-stu-id="26b9f-129">The built-in logic for detecting changes automatically skips rerendering if none of these parameter values have changed.</span></span> <span data-ttu-id="26b9f-130">如果使用 `<Customer CustomerId="@item.CustomerId" />` 呈现子组件（其中 `CustomerId` 为 `int` 值），则除非 `item.CustomerId` 更改，否则不会重新呈现。</span><span class="sxs-lookup"><span data-stu-id="26b9f-130">If you render a child component with `<Customer CustomerId="@item.CustomerId" />`, where `CustomerId` is an `int` value, then it isn't rerendered except when `item.CustomerId` changes.</span></span>
* <span data-ttu-id="26b9f-131">如果需要接受非基元参数值（如自定义模型类型、事件回调或 <xref:Microsoft.AspNetCore.Components.RenderFragment> 值），则可以重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 以控制有关是否呈现的决策，[使用 `ShouldRender`](#use-of-shouldrender) 部分中对此进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="26b9f-131">If you need to accept nonprimitive parameter values, such as custom model types, event callbacks, or <xref:Microsoft.AspNetCore.Components.RenderFragment> values, then you can override <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to control the decision about whether to render, which is described in the [Use of `ShouldRender`](#use-of-shouldrender) section.</span></span>

<span data-ttu-id="26b9f-132">通过跳过重新呈现整个子树，当发生事件时，可以删除绝大多数的呈现开销。</span><span class="sxs-lookup"><span data-stu-id="26b9f-132">By skipping rerendering of whole subtrees, you may be able to remove the vast majority of the rendering cost when an event occurs.</span></span>

<span data-ttu-id="26b9f-133">你可能想要专门分解出子组件，以便可以跳过重新呈现 UI 的此部分。</span><span class="sxs-lookup"><span data-stu-id="26b9f-133">You may wish to factor out child components specifically so that you can skip rerendering that part of the UI.</span></span> <span data-ttu-id="26b9f-134">这是降低父组件的呈现开销的有效方法。</span><span class="sxs-lookup"><span data-stu-id="26b9f-134">This is a valid way to reduce the rendering cost of a parent component.</span></span>

#### <a name="use-of-shouldrender"></a><span data-ttu-id="26b9f-135">使用 `ShouldRender`</span><span class="sxs-lookup"><span data-stu-id="26b9f-135">Use of `ShouldRender`</span></span>

<span data-ttu-id="26b9f-136">如果创作了一个仅限 UI 的组件，且该组件在最初呈现后从未更改（无论使用哪个参数值），则请将 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 配置为返回 `false`：</span><span class="sxs-lookup"><span data-stu-id="26b9f-136">If authoring a UI-only component that never changes after the initial render (regardless of any parameter values), configure <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> to return `false`:</span></span>

```razor
@code {
    protected override bool ShouldRender() => false;
}
```

<span data-ttu-id="26b9f-137">如果组件只需在其参数值以特定方式改变时重新呈现，则可以使用私有字段跟踪必要的信息来检测更改。</span><span class="sxs-lookup"><span data-stu-id="26b9f-137">If the component only requires rerendering when its parameter values mutate in particular ways, then you can use private fields to track the necessary information to detect changes.</span></span> <span data-ttu-id="26b9f-138">在下面的示例中，`shouldRender` 基于检查结果，检查是否有任何类型的应提示重新呈现的更改或转变。</span><span class="sxs-lookup"><span data-stu-id="26b9f-138">In the following example, `shouldRender` is based on checking for any kind of change or mutation that should prompt a rerender.</span></span> <span data-ttu-id="26b9f-139">`prevOutboundFlightId` 和 `prevInboundFlightId` 跟踪下一个可能更新的信息：</span><span class="sxs-lookup"><span data-stu-id="26b9f-139">`prevOutboundFlightId` and `prevInboundFlightId` track information for the next potential update:</span></span>

```razor
@code {
    [Parameter]
    public FlightInfo OutboundFlight { get; set; }
    
    [Parameter]
    public FlightInfo InboundFlight { get; set; }

    private int prevOutboundFlightId;
    private int prevInboundFlightId;
    private bool shouldRender;

    protected override void OnParametersSet()
    {
        shouldRender = OutboundFlight.FlightId != prevOutboundFlightId
            || InboundFlight.FlightId != prevInboundFlightId;

        prevOutboundFlightId = OutboundFlight.FlightId;
        prevInboundFlightId = InboundFlight.FlightId;
    }

    protected override void ShouldRender() => shouldRender;

    // Note that 
}
```

<span data-ttu-id="26b9f-140">在前面的代码中，事件处理程序还可以将 `shouldRender` 设置为 `true`，以便在事件之后重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-140">In the preceding code, an event handler may also set `shouldRender` to `true` so that the component is rerendered after the event.</span></span>

<span data-ttu-id="26b9f-141">大多数组件不需要此级别的手动控制。</span><span class="sxs-lookup"><span data-stu-id="26b9f-141">For most components, this level of manual control isn't necessary.</span></span> <span data-ttu-id="26b9f-142">只应考虑跳过呈现子树（如果这些子树的呈现费用非常昂贵并导致 UI 延迟）。</span><span class="sxs-lookup"><span data-stu-id="26b9f-142">You should only be concerned about skipping rendering subtrees if those subtrees are particularly expensive to render and are causing UI lag.</span></span>

<span data-ttu-id="26b9f-143">有关详细信息，请参阅 <xref:blazor/components/lifecycle>。</span><span class="sxs-lookup"><span data-stu-id="26b9f-143">For more information, see <xref:blazor/components/lifecycle>.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="virtualization"></a><span data-ttu-id="26b9f-144">虚拟化</span><span class="sxs-lookup"><span data-stu-id="26b9f-144">Virtualization</span></span>

<span data-ttu-id="26b9f-145">在循环中呈现大量 UI（例如，具有数千个条目的列表或网格）时，呈现操作的极大数量可能会导致 UI 呈现延迟，从而导致用户体验不佳。</span><span class="sxs-lookup"><span data-stu-id="26b9f-145">When rendering large amounts of UI within a loop, for example a list or grid with thousands of entries, the sheer quantity of rendering operations can lead to a lag in UI rendering and thus a poor user experience.</span></span> <span data-ttu-id="26b9f-146">假设用户只能在不滚动的情况下同时查看少量元素，则花费太多时间呈现当前不可见的元素似乎太浪费了。</span><span class="sxs-lookup"><span data-stu-id="26b9f-146">Given that the user can only see a small number of elements at once without scrolling, it seems wasteful to spend so much time rendering elements that aren't currently visible.</span></span>

<span data-ttu-id="26b9f-147">为了解决此问题，Blazor 提供了 `Virtualize` 组件，它可创建任意规模的列表的外观和滚动行为，但只会呈现当前滚动视区内的列表项。</span><span class="sxs-lookup"><span data-stu-id="26b9f-147">To address this, Blazor provides the `Virtualize` component that creates the appearance and scroll behaviors of an arbitrarily-large list but only renders the list items that are within the current scroll viewport.</span></span> <span data-ttu-id="26b9f-148">例如，这意味着应用可以有一个包含 100,000 个条目的列表，但每次只需支付 20 个可见项的费用。</span><span class="sxs-lookup"><span data-stu-id="26b9f-148">For example, this means that the app can have a list with 100,000 entries but only pay the rendering cost of 20 items that are visible at any one time.</span></span> <span data-ttu-id="26b9f-149">使用 `Virtualize` 组件可以按数量级提高 UI 性能。</span><span class="sxs-lookup"><span data-stu-id="26b9f-149">Use of the `Virtualize` component can scale up UI performance by orders of magnitude.</span></span>

<span data-ttu-id="26b9f-150">有关详细信息，请参阅 <xref:blazor/components/virtualization>。</span><span class="sxs-lookup"><span data-stu-id="26b9f-150">For more information, see <xref:blazor/components/virtualization>.</span></span>

::: moniker-end

### <a name="create-lightweight-optimized-components"></a><span data-ttu-id="26b9f-151">创建轻型且优化的组件</span><span class="sxs-lookup"><span data-stu-id="26b9f-151">Create lightweight, optimized components</span></span>

<span data-ttu-id="26b9f-152">大多数 Blazor 组件不需要强硬的优化工作。</span><span class="sxs-lookup"><span data-stu-id="26b9f-152">Most Blazor components don't require aggressive optimization efforts.</span></span> <span data-ttu-id="26b9f-153">这是因为大多数组件并不经常在 UI 中重复，也不会高频率重新呈现。</span><span class="sxs-lookup"><span data-stu-id="26b9f-153">This is because most components don't often repeat in the UI and don't rerender at high frequency.</span></span> <span data-ttu-id="26b9f-154">例如，`@page` 组件和表示高级 UI 块（如对话框或窗体）的组件在大多数情况下一次仅显示一个，并且仅为了响应用户手势而重新呈现。</span><span class="sxs-lookup"><span data-stu-id="26b9f-154">For example, `@page` components and components representing high-level UI pieces such as dialogs or forms, most likely appear only one at a time and only rerender in response to a user gesture.</span></span> <span data-ttu-id="26b9f-155">这些组件不会创建较高的呈现工作负载，因此你可以自由地使用所需的任何框架功能组合，而无需担心呈现性能。</span><span class="sxs-lookup"><span data-stu-id="26b9f-155">These components don't create a high rendering workload, so you can freely use any combination of framework features you want without worrying much about rendering performance.</span></span>

<span data-ttu-id="26b9f-156">但是，还有生成需要大规模重复的组件的常见情况。</span><span class="sxs-lookup"><span data-stu-id="26b9f-156">However, there are also common scenarios where you build components that need to be repeated at scale.</span></span> <span data-ttu-id="26b9f-157">例如：</span><span class="sxs-lookup"><span data-stu-id="26b9f-157">For example:</span></span>

* <span data-ttu-id="26b9f-158">大型嵌套窗体可能包含数百个单个输入、标签和其他元素。</span><span class="sxs-lookup"><span data-stu-id="26b9f-158">Large nested forms may have hundreds of individual inputs, labels, and other elements.</span></span>
* <span data-ttu-id="26b9f-159">网格可能包含数千个单元格。</span><span class="sxs-lookup"><span data-stu-id="26b9f-159">Grids may have thousands of cells.</span></span>
* <span data-ttu-id="26b9f-160">散点图可能包含数百万个数据点。</span><span class="sxs-lookup"><span data-stu-id="26b9f-160">Scatter plots may have millions of data points.</span></span>

<span data-ttu-id="26b9f-161">如果将每个单位建模为单独的组件实例，则会有很多呈现性能变得至关重要的实例。</span><span class="sxs-lookup"><span data-stu-id="26b9f-161">If modelling each unit as separate component instances, there will be so many of them that their rendering performance does become critical.</span></span> <span data-ttu-id="26b9f-162">本部分提供了有关使此类组件变得轻量，以便 UI 保持快速且响应迅速的建议。</span><span class="sxs-lookup"><span data-stu-id="26b9f-162">This section provides advice on making such components lightweight so that the UI remains fast and responsive.</span></span>

#### <a name="avoid-thousands-of-component-instances"></a><span data-ttu-id="26b9f-163">避免上千个组件实例</span><span class="sxs-lookup"><span data-stu-id="26b9f-163">Avoid thousands of component instances</span></span>

<span data-ttu-id="26b9f-164">每个组件都是单独的，可以独立于其父组件和子组件进行呈现。</span><span class="sxs-lookup"><span data-stu-id="26b9f-164">Each component is a separate island that can render independently of its parents and children.</span></span> <span data-ttu-id="26b9f-165">通过选择如何将 UI 拆分为组件的层次结构，你将控制 UI 呈现的粒度。</span><span class="sxs-lookup"><span data-stu-id="26b9f-165">By choosing how to split up the UI into a hierarchy of components, you are taking control over the granularity of UI rendering.</span></span> <span data-ttu-id="26b9f-166">这可能导致性能良好或不佳。</span><span class="sxs-lookup"><span data-stu-id="26b9f-166">This can be either good or bad for performance.</span></span>

* <span data-ttu-id="26b9f-167">将 UI 拆分为多个组件后，当发生事件时，可以有较小部分的 UI 重新呈现。</span><span class="sxs-lookup"><span data-stu-id="26b9f-167">By splitting the UI into more components, you can have smaller portions of the UI rerender when events occur.</span></span> <span data-ttu-id="26b9f-168">例如，当用户单击表行中的某个按钮时，你可能能够仅重新呈现单行，而不是整页或整个表。</span><span class="sxs-lookup"><span data-stu-id="26b9f-168">For example when a user clicks a button in a table row, you may be able to have only that single row rerender instead of the whole page or table.</span></span>
* <span data-ttu-id="26b9f-169">但是，每个额外组件都包含一些额外内存和 CPU 开销，用于处理其独立状态和呈现生命周期。</span><span class="sxs-lookup"><span data-stu-id="26b9f-169">However, each extra component involves some extra memory and CPU overhead to deal with its independent state and rendering lifecycle.</span></span>

<span data-ttu-id="26b9f-170">优化 .NET 5 上的 Blazor WebAssembly 性能时，我们计算了每个组件实例大约 0.06 毫秒的呈现开销。</span><span class="sxs-lookup"><span data-stu-id="26b9f-170">When tuning the performance of Blazor WebAssembly on .NET 5, we measured a rendering overhead of around 0.06 ms per component instance.</span></span> <span data-ttu-id="26b9f-171">这基于接受在典型便携式计算机上运行的三个参数的简单组件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-171">This is based on a simple component that accepts three parameters running on a typical laptop.</span></span> <span data-ttu-id="26b9f-172">在内部，开销很大程度上取决于从字典中检索每个组件的状态以及传递和接收参数。</span><span class="sxs-lookup"><span data-stu-id="26b9f-172">Internally, the overhead is largely due to retrieving per-component state from dictionaries and passing and receiving parameters.</span></span> <span data-ttu-id="26b9f-173">通过成倍增加，你可以看到添加 2,000 个额外的组件实例会使呈现时间增加 0.12 秒，并且用户会觉得 UI 开始变得缓慢。</span><span class="sxs-lookup"><span data-stu-id="26b9f-173">By multiplication, you can see that adding 2,000 extra component instances would add 0.12 seconds to the rendering time and the UI would begin feeling slow to users.</span></span>

<span data-ttu-id="26b9f-174">可以使组件变得更轻量，以便你可以拥有更多组件，但功能更强大的技术通常不会有如此多的组件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-174">It's possible to make components more lightweight so that you can have more of them, but often the more powerful technique is not to have so many components.</span></span> <span data-ttu-id="26b9f-175">以下部分将介绍两种方法。</span><span class="sxs-lookup"><span data-stu-id="26b9f-175">The following sections describe two approaches.</span></span>

##### <a name="inline-child-components-into-their-parents"></a><span data-ttu-id="26b9f-176">将子组件嵌入到其父组件中</span><span class="sxs-lookup"><span data-stu-id="26b9f-176">Inline child components into their parents</span></span>

<span data-ttu-id="26b9f-177">请考虑呈现一系列子组件的以下组件：</span><span class="sxs-lookup"><span data-stu-id="26b9f-177">Consider the following component that renders a sequence of child components:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <ChatMessageDisplay Message="@message" />
    }
</div>
```

<span data-ttu-id="26b9f-178">对于前面的示例代码，`<ChatMessageDisplay>` 组件在文件 `ChatMessageDisplay.razor` 中定义，其中包含：</span><span class="sxs-lookup"><span data-stu-id="26b9f-178">For the preceding example code, the `<ChatMessageDisplay>` component is defined in a file `ChatMessageDisplay.razor` containing:</span></span>

```razor
<div class="chat-message">
    <span class="author">@Message.Author</span>
    <span class="text">@Message.Text</span>
</div>

@code {
    [Parameter]
    public ChatMessage Message { get; set; }
}
```

<span data-ttu-id="26b9f-179">只要一次不显示数千条消息，前面的示例就可以正常工作且性能良好。</span><span class="sxs-lookup"><span data-stu-id="26b9f-179">The preceding example works fine and performs well as long as thousands of messages aren't shown at once.</span></span> <span data-ttu-id="26b9f-180">若要一次显示数千条消息，请考虑不分解出单独的 `ChatMessageDisplay` 组件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-180">To show thousands of messages at once, consider *not* factoring out the separate `ChatMessageDisplay` component.</span></span> <span data-ttu-id="26b9f-181">而改为将呈现直接内联到父组件：</span><span class="sxs-lookup"><span data-stu-id="26b9f-181">Instead, inline the rendering directly into the parent:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    }
</div>
```

<span data-ttu-id="26b9f-182">这样可以避免因呈现如此多的子组件，但无法单独重新呈现每个子组件而导致的每组件开销。</span><span class="sxs-lookup"><span data-stu-id="26b9f-182">This avoids the per-component overhead of rendering so many child components at the cost of not being able to rerender each of them independently.</span></span>

##### <a name="define-reusable-renderfragments-in-code"></a><span data-ttu-id="26b9f-183">在代码中定义可重用的 `RenderFragments`</span><span class="sxs-lookup"><span data-stu-id="26b9f-183">Define reusable `RenderFragments` in code</span></span>

<span data-ttu-id="26b9f-184">你可能会分解出子组件，纯粹作为重复使用呈现逻辑的方法。</span><span class="sxs-lookup"><span data-stu-id="26b9f-184">You may be factoring out child components purely as a way of reusing rendering logic.</span></span> <span data-ttu-id="26b9f-185">如果是这种情况，则仍可以重复使用呈现逻辑，而无需声明实际组件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-185">If that's the case, it's still possible to reuse rendering logic without declaring actual components.</span></span> <span data-ttu-id="26b9f-186">在任何组件的 `@code` 块中，你都可以定义发出 UI 并可从任何位置调用的 <xref:Microsoft.AspNetCore.Components.RenderFragment>：</span><span class="sxs-lookup"><span data-stu-id="26b9f-186">In any component's `@code` block, you can define a <xref:Microsoft.AspNetCore.Components.RenderFragment> that emits UI and can be called from anywhere:</span></span>

```razor
<h1>Hello, world!</h1>

@RenderWelcomeInfo

@code {
    private RenderFragment RenderWelcomeInfo = __builder =>
    {
        <div>
            <p>Welcome to your new app!</p>

            <SurveyPrompt Title="How is Blazor working for you?" />
        </div>
    };
}
```

<span data-ttu-id="26b9f-187">如前面的示例中所述，组件可通过其 `@code` 块内外的代码发出标记。</span><span class="sxs-lookup"><span data-stu-id="26b9f-187">As demonstated in the preceding example, components can emit markup from code within their `@code` block and outside it.</span></span> <span data-ttu-id="26b9f-188">此方法可在多处定义可在组件的常规呈现输出内呈现的 <xref:Microsoft.AspNetCore.Components.RenderFragment> 委托。</span><span class="sxs-lookup"><span data-stu-id="26b9f-188">This approach defines a <xref:Microsoft.AspNetCore.Components.RenderFragment> delegate that you can render inside the component's normal render output, optionally in multiple places.</span></span> <span data-ttu-id="26b9f-189">委托必须接受名为 `__builder`、类型为 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 的参数，以便 Razor 编译器可以为其生成呈现说明。</span><span class="sxs-lookup"><span data-stu-id="26b9f-189">It's necessary for the delegate to accept a parameter called `__builder` of type <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> so that the Razor compiler can produce rendering instructions for it.</span></span>

<span data-ttu-id="26b9f-190">如果要使其可在多个组件之间重复使用，请考虑将其声明为 `public static` 成员：</span><span class="sxs-lookup"><span data-stu-id="26b9f-190">If you want to make this reusable across multiple components, consider declaring it as a `public static` member:</span></span>

```razor
public static RenderFragment SayHello = __builder =>
{
    <h1>Hello!</h1>
};
```

<span data-ttu-id="26b9f-191">现在可以从不相关的组件调用该委托。</span><span class="sxs-lookup"><span data-stu-id="26b9f-191">This could now be invoked from an unrelated component.</span></span> <span data-ttu-id="26b9f-192">此方法可用于生成无需任何每组件开销即可呈现的可重用标记段库。</span><span class="sxs-lookup"><span data-stu-id="26b9f-192">This technique is useful for building libraries of reusable markup snippets that render without any per-component overhead.</span></span>

<span data-ttu-id="26b9f-193"><xref:Microsoft.AspNetCore.Components.RenderFragment> 委托也可以接受参数。</span><span class="sxs-lookup"><span data-stu-id="26b9f-193"><xref:Microsoft.AspNetCore.Components.RenderFragment> delegates can also accept parameters.</span></span> <span data-ttu-id="26b9f-194">若要创建上述示例中的 `ChatMessageDisplay` 组件的等效项，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="26b9f-194">To create the equivalent of the `ChatMessageDisplay` component from the earlier example:</span></span>

```razor
<div class="chat">
    @foreach (var message in messages)
    {
        @ChatMessageDisplay(message)
    }
</div>

@code {
    private RenderFragment<ChatMessage> ChatMessageDisplay = message => __builder =>
    {
        <div class="chat-message">
            <span class="author">@message.Author</span>
            <span class="text">@message.Text</span>
        </div>
    };
}
```

<span data-ttu-id="26b9f-195">此方法提供了无需每组件开销即可重复使用呈现逻辑的好处。</span><span class="sxs-lookup"><span data-stu-id="26b9f-195">This approach provides the benefit of reusing rendering logic without per-component overhead.</span></span> <span data-ttu-id="26b9f-196">但是，既无法单独刷新 UI 的子树，也无法在呈现 UI 的父树时跳过呈现其子树，因为没有组件边界。</span><span class="sxs-lookup"><span data-stu-id="26b9f-196">However, it doesn't have the benefit of being able to refresh its subtree of the UI independently, nor does it have the ability to skip rendering that subtree of the UI when its parent renders, since there's no component boundary.</span></span>

<span data-ttu-id="26b9f-197">对于无法由字段初始化表达式引用的非静态字段、方法或属性（如以下示例中的 `TitleTemplate`），请使用属性，而不是 <xref:Microsoft.AspNetCore.Components.RenderFragment> 的字段：</span><span class="sxs-lookup"><span data-stu-id="26b9f-197">For a non-static field, method, or property that can't be referenced by a field initializer, such as `TitleTemplate` in the following example, use a property instead of a field for the <xref:Microsoft.AspNetCore.Components.RenderFragment>:</span></span>

```csharp
protected RenderFragment DisplayTitle => __builder =>
{
    <div>
        @TitleTemplate
    </div>   
};
```

#### <a name="dont-receive-too-many-parameters"></a><span data-ttu-id="26b9f-198">不接收太多参数</span><span class="sxs-lookup"><span data-stu-id="26b9f-198">Don't receive too many parameters</span></span>

<span data-ttu-id="26b9f-199">如果某个组件极频繁地重复（例如，数百或数千次），请记住，会产生传递和接收每个参数的开销。</span><span class="sxs-lookup"><span data-stu-id="26b9f-199">If a component repeats extremely often, for example hundreds or thousands of times, then bear in mind that the overhead of passing and receiving each parameter builds up.</span></span>

<span data-ttu-id="26b9f-200">很少有过多参数严格地限制性能，但这可能是一个因素。</span><span class="sxs-lookup"><span data-stu-id="26b9f-200">It's rare that too many parameters severely restricts performance, but it can be a factor.</span></span> <span data-ttu-id="26b9f-201">对于在网格内呈现 1,000 次的 `<TableCell>` 组件，传递给它的每个额外参数可能会使总呈现开销增加大约 15 毫秒。</span><span class="sxs-lookup"><span data-stu-id="26b9f-201">For a `<TableCell>` component that renders 1,000 times within a grid, each extra parameter passed to it could add around 15 ms to the total rendering cost.</span></span> <span data-ttu-id="26b9f-202">如果每个单元格接受 10 个参数，每次组件呈现的参数传递大约需要 150 毫秒，则总计可能需要 150,000 毫秒（150 秒），因此可能会导致 UI 滞后。</span><span class="sxs-lookup"><span data-stu-id="26b9f-202">If each cell accepted 10 parameters, parameter passing takes around 150 ms per component render and  thus perhaps 150,000 ms (150 seconds) and on its own cause a laggy UI.</span></span>

<span data-ttu-id="26b9f-203">若要减少此负载，可以通过自定义类将多个参数捆绑在一起。</span><span class="sxs-lookup"><span data-stu-id="26b9f-203">To reduce this load, you could bundle together multiple parameters via custom classes.</span></span> <span data-ttu-id="26b9f-204">例如，`<TableCell>` 组件可能接受：</span><span class="sxs-lookup"><span data-stu-id="26b9f-204">For example, a `<TableCell>` component might accept:</span></span>

```razor
@typeparam TItem

...

@code {
    [Parameter]
    public TItem Data { get; set; }
    
    [Parameter]
    public GridOptions Options { get; set; }
}
```

<span data-ttu-id="26b9f-205">在前面的示例中，每个单元格的 `Data` 都是不同的，但 `Options` 在所有单元格中都是通用的。</span><span class="sxs-lookup"><span data-stu-id="26b9f-205">In the preceding example, `Data` is different for every cell, but `Options` is common across all of them.</span></span> <span data-ttu-id="26b9f-206">当然，可以改为不需要 `<TableCell>` 组件，并且将其逻辑内联到父组件中。</span><span class="sxs-lookup"><span data-stu-id="26b9f-206">Of course, it might be an improvement not to have a `<TableCell>` component and instead inline its logic into the parent component.</span></span>

#### <a name="ensure-cascading-parameters-are-fixed"></a><span data-ttu-id="26b9f-207">确保级联参数是固定的</span><span class="sxs-lookup"><span data-stu-id="26b9f-207">Ensure cascading parameters are fixed</span></span>

<span data-ttu-id="26b9f-208">`<CascadingValue>` 组件具有名为 `IsFixed` 的可选参数。</span><span class="sxs-lookup"><span data-stu-id="26b9f-208">The `<CascadingValue>` component has an optional parameter called `IsFixed`.</span></span>

* <span data-ttu-id="26b9f-209">如果 `IsFixed` 值为 `false`（默认值），则级联值的每个接收方都会将订阅设置为接收更改通知。</span><span class="sxs-lookup"><span data-stu-id="26b9f-209">If the `IsFixed` value is `false` (the default), then every recipient of the cascaded value sets up a subscription to receive change notifications.</span></span> <span data-ttu-id="26b9f-210">在这种情况下，由于订阅跟踪，每个 `[CascadingParameter]` 的开销大体上都要比常规 `[Parameter]` 昂贵。</span><span class="sxs-lookup"><span data-stu-id="26b9f-210">In this case, each `[CascadingParameter]` is **substantially more expensive** than a regular `[Parameter]` due to the subscription tracking.</span></span>
* <span data-ttu-id="26b9f-211">如果 `IsFixed` 值为 `true`（例如，`<CascadingValue Value="@someValue" IsFixed="true">`），则接收方会接收初始值，但不会将任何订阅设置为接收更新。</span><span class="sxs-lookup"><span data-stu-id="26b9f-211">If the `IsFixed` value is `true` (for example, `<CascadingValue Value="@someValue" IsFixed="true">`), then receipients receive the initial value but do *not* set up any subscription to receive updates.</span></span> <span data-ttu-id="26b9f-212">在这种情况下，每个 `[CascadingParameter]` 都是轻型的，并不比常规 `[Parameter]` 昂贵。</span><span class="sxs-lookup"><span data-stu-id="26b9f-212">In this case, each `[CascadingParameter]` is lightweight and **no more expensive** than a regular `[Parameter]`.</span></span>

<span data-ttu-id="26b9f-213">因此，只要有可能，就应对级联值使用 `IsFixed="true"`。</span><span class="sxs-lookup"><span data-stu-id="26b9f-213">So wherever possible, you should use `IsFixed="true"` on cascaded values.</span></span> <span data-ttu-id="26b9f-214">只要提供的值不随时间变化，就可以执行此操作。</span><span class="sxs-lookup"><span data-stu-id="26b9f-214">You can do this whenever the value being supplied doesn't change over time.</span></span> <span data-ttu-id="26b9f-215">在组件将 `this` 作为级联值传递的常见模式下，应使用 `IsFixed="true"`：</span><span class="sxs-lookup"><span data-stu-id="26b9f-215">In the common pattern where a component passes `this` as a cascaded value, you should use `IsFixed="true"`:</span></span>

```razor
<CascadingValue Value="this" IsFixed="true">
    <SomeOtherComponents>
</CascadingValue>
```

<span data-ttu-id="26b9f-216">如果有大量其他组件接收级联值，则这会带来巨大的差异。</span><span class="sxs-lookup"><span data-stu-id="26b9f-216">This makes a huge difference if there are a large number of other components that receive the cascaded value.</span></span> <span data-ttu-id="26b9f-217">有关详细信息，请参阅 <xref:blazor/components/cascading-values-and-parameters>。</span><span class="sxs-lookup"><span data-stu-id="26b9f-217">For more information, see <xref:blazor/components/cascading-values-and-parameters>.</span></span>

#### <a name="avoid-attribute-splatting-with-captureunmatchedvalues"></a><span data-ttu-id="26b9f-218">使用 `CaptureUnmatchedValues` 避免特性展开</span><span class="sxs-lookup"><span data-stu-id="26b9f-218">Avoid attribute splatting with `CaptureUnmatchedValues`</span></span>

<span data-ttu-id="26b9f-219">组件可以选择使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> 标志接收“不匹配”参数值：</span><span class="sxs-lookup"><span data-stu-id="26b9f-219">Components can elect to receive "unmatched" parameter values using the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> flag:</span></span>

```razor
<div @attributes="OtherAttributes">...</div>

@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public IDictionary<string, object> OtherAttributes { get; set; }
}
```

<span data-ttu-id="26b9f-220">此方法允许将任意附加特性传递到元素。</span><span class="sxs-lookup"><span data-stu-id="26b9f-220">This approach allows passing through arbitrary additional attributes to the element.</span></span> <span data-ttu-id="26b9f-221">但是，这也相当昂贵，因为呈现器必须：</span><span class="sxs-lookup"><span data-stu-id="26b9f-221">However, it is also quite expensive because the renderer must:</span></span>

* <span data-ttu-id="26b9f-222">将所有提供的参数与用于生成字典的已知参数集匹配。</span><span class="sxs-lookup"><span data-stu-id="26b9f-222">Match all of the supplied parameters against the set of known parameters to build a dictionary.</span></span>
* <span data-ttu-id="26b9f-223">跟踪同一特性的多个副本彼此覆盖的方式。</span><span class="sxs-lookup"><span data-stu-id="26b9f-223">Keep track of how multiple copies of the same attribute overwrite each other.</span></span>

<span data-ttu-id="26b9f-224">可随意在非性能关键组件（如不经常重复的组件）上使用 <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>。</span><span class="sxs-lookup"><span data-stu-id="26b9f-224">Feel free to use <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> on non-performance-critical components, such as ones that are not repeated frequently.</span></span> <span data-ttu-id="26b9f-225">但对于大规模呈现的组件（如大型列表中的每个项或网格中的单元格），请尝试避免特性展开。</span><span class="sxs-lookup"><span data-stu-id="26b9f-225">However for components that render at scale, such as each items in a large list or cells in a grid, try to avoid attribute splatting.</span></span>

<span data-ttu-id="26b9f-226">有关详细信息，请参阅 <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>。</span><span class="sxs-lookup"><span data-stu-id="26b9f-226">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

#### <a name="implement-setparametersasync-manually"></a><span data-ttu-id="26b9f-227">手动实现 `SetParametersAsync`</span><span class="sxs-lookup"><span data-stu-id="26b9f-227">Implement `SetParametersAsync` manually</span></span>

<span data-ttu-id="26b9f-228">每组件呈现开销的主要方面之一是将传入参数值写入 `[Parameter]` 属性。</span><span class="sxs-lookup"><span data-stu-id="26b9f-228">One of the main aspects of the per-component rendering overhead is writing incoming parameter values to the `[Parameter]` properties.</span></span> <span data-ttu-id="26b9f-229">呈现器必须使用反射来实现此目的。</span><span class="sxs-lookup"><span data-stu-id="26b9f-229">The renderer has to use reflection to do this.</span></span> <span data-ttu-id="26b9f-230">即使此情况已经过优化，WebAssembly 运行时上缺少 JIT 支持也会施加限制。</span><span class="sxs-lookup"><span data-stu-id="26b9f-230">Even though this is somewhat optimized, the absence of JIT support on the WebAssembly runtime imposes limits.</span></span>

<span data-ttu-id="26b9f-231">在某些极端情况下，你可能希望避免反射并手动实现自己的参数设置逻辑。</span><span class="sxs-lookup"><span data-stu-id="26b9f-231">In some extreme cases, you may wish to avoid the reflection and implement your own parameter setting logic manually.</span></span> <span data-ttu-id="26b9f-232">这可能适用于以下情况：</span><span class="sxs-lookup"><span data-stu-id="26b9f-232">This may be applicable when:</span></span>

* <span data-ttu-id="26b9f-233">你有极频繁地呈现的组件（例如，UI 中有数百个或数千个副本）。</span><span class="sxs-lookup"><span data-stu-id="26b9f-233">You have a component that renders extremely often (for example, there are hundreds or thousands of copies of it in the UI).</span></span>
* <span data-ttu-id="26b9f-234">它接受多个参数。</span><span class="sxs-lookup"><span data-stu-id="26b9f-234">It accepts many parameters.</span></span>
* <span data-ttu-id="26b9f-235">你会发现接收参数的开销对 UI 响应能力有明显的影响。</span><span class="sxs-lookup"><span data-stu-id="26b9f-235">You find that the overhead of receiving parameters has an observable impact on UI responsiveness.</span></span>

<span data-ttu-id="26b9f-236">在这些情况下，可以重写组件的虚拟 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 方法，并实现自己的特定于组件的逻辑。</span><span class="sxs-lookup"><span data-stu-id="26b9f-236">In these cases, you can override the component's virtual <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> method and implement your own component-specific logic.</span></span> <span data-ttu-id="26b9f-237">下面的示例特意避免了任何字典查找：</span><span class="sxs-lookup"><span data-stu-id="26b9f-237">The following example deliberately avoids any dictionary lookups:</span></span>

```razor
@code {
    [Parameter]
    public int MessageId { get; set; }

    [Parameter]
    public string Text { get; set; }

    [Parameter]
    public EventCallback<string> TextChanged { get; set; }

    [Parameter]
    public Theme CurrentTheme { get; set; }

    public override Task SetParametersAsync(ParameterView parameters)
    {
        foreach (var parameter in parameters)
        {
            switch (parameter.Name)
            {
                case nameof(MessageId):
                    MessageId = (int)parameter.Value;
                    break;
                case nameof(Text):
                    Text = (string)parameter.Value;
                    break;
                case nameof(TextChanged):
                    TextChanged = (EventCallback<string>)parameter.Value;
                    break;
                case nameof(CurrentTheme):
                    CurrentTheme = (Theme)parameter.Value;
                    break;
                default:
                    throw new ArgumentException($"Unknown parameter: {parameter.Name}");
            }
        }

        return base.SetParametersAsync(ParameterView.Empty);
    }
}
```

<span data-ttu-id="26b9f-238">在上面的代码中，返回基类 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 会运行正常的生命周期方法，而不会再次分配参数。</span><span class="sxs-lookup"><span data-stu-id="26b9f-238">In the preceding code, returning the base class <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> runs the normal lifecycle methods without assigning parameters again.</span></span>

<span data-ttu-id="26b9f-239">正如你在前面的代码中所看到的，重写 <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> 和提供自定义逻辑比较复杂且繁琐，因此，我们通常不建议使用此方法。</span><span class="sxs-lookup"><span data-stu-id="26b9f-239">As you can see in the preceding code, overriding <xref:Microsoft.AspNetCore.Components.ComponentBase.SetParametersAsync%2A> and supplying custom logic is complicated and laborious, so we don't recommend this approach in general.</span></span> <span data-ttu-id="26b9f-240">在极端情况下，它可以提高 20-25% 的呈现性能，但只应在前面列出的方案中考虑使用此方法。</span><span class="sxs-lookup"><span data-stu-id="26b9f-240">In extreme cases, it can improve rendering performance by 20-25%, but you should only consider this approach in the scenarios listed earlier.</span></span>

### <a name="dont-trigger-events-too-rapidly"></a><span data-ttu-id="26b9f-241">不要过快触发事件</span><span class="sxs-lookup"><span data-stu-id="26b9f-241">Don't trigger events too rapidly</span></span>

<span data-ttu-id="26b9f-242">某些浏览器事件极频繁地触发 `onmousemove` 和 `onscroll`，每秒可以触发数十或数百次。</span><span class="sxs-lookup"><span data-stu-id="26b9f-242">Some browser events fire extremely frequently, for example `onmousemove` and `onscroll`, which can fire tens or hundreds of times per second.</span></span> <span data-ttu-id="26b9f-243">在大多数情况下，不需要经常执行 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="26b9f-243">In most cases, you don't need to perform UI updates this frequently.</span></span> <span data-ttu-id="26b9f-244">如果尝试这样做，可能会损害 UI 响应能力或消耗过多的 CPU 时间。</span><span class="sxs-lookup"><span data-stu-id="26b9f-244">If you try to do so, you may harm UI responsiveness or consume excessive CPU time.</span></span>

<span data-ttu-id="26b9f-245">你可能更想使用 JS 互操作来注册不太频繁触发的回调，而不是使用本机 `@onmousemove` 或 `@onscroll` 事件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-245">Rather than using native `@onmousemove` or `@onscroll` events, you may prefer to use JS interop to register a callback that fires less frequently.</span></span> <span data-ttu-id="26b9f-246">例如，以下组件 (`MyComponent.razor`) 显示鼠标的位置，但每 500 毫秒最多只能更新一次：</span><span class="sxs-lookup"><span data-stu-id="26b9f-246">For example, the following component (`MyComponent.razor`) displays the position of the mouse but only updates at most once every 500 ms:</span></span>

```razor
@inject IJSRuntime JS
@implements IDisposable

<h1>@message</h1>

<div @ref="myMouseMoveElement" style="border:1px dashed red;height:200px;">
    Move mouse here
</div>

@code {
    ElementReference myMouseMoveElement;
    DotNetObjectReference<MyComponent> selfReference;
    private string message = "Move the mouse in the box";

    [JSInvokable]
    public void HandleMouseMove(int x, int y)
    {
        message = $"Mouse move at {x}, {y}";
        StateHasChanged();
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            selfReference = DotNetObjectReference.Create(this);
            var minInterval = 500; // Only notify every 500 ms
            await JS.InvokeVoidAsync("onThrottledMouseMove", 
                myMouseMoveElement, selfReference, minInterval);
        }
    }

    public void Dispose() => selfReference?.Dispose();
}
```

<span data-ttu-id="26b9f-247">相应的 JavaScript 代码（可放置在 `index.html` 页中或作为 ES6 模块加载）注册实际 DOM 事件侦听器。</span><span class="sxs-lookup"><span data-stu-id="26b9f-247">The corresponding JavaScript code, which can be placed in the `index.html` page or loaded as an ES6 module, registers the actual DOM event listener.</span></span> <span data-ttu-id="26b9f-248">在此示例中，事件侦听器使用 [Lodash 的 `throttle` 函数](https://lodash.com/docs/4.17.15#throttle)来限制调用速率：</span><span class="sxs-lookup"><span data-stu-id="26b9f-248">In this example, the event listener uses [Lodash's `throttle` function](https://lodash.com/docs/4.17.15#throttle) to limit the rate of invocations:</span></span>

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.20/lodash.min.js"></script>
<script>
  function onThrottledMouseMove(elem, component, interval) {
    elem.addEventListener('mousemove', _.throttle(e => {
      component.invokeMethodAsync('HandleMouseMove', e.offsetX, e.offsetY);
    }, interval));
  }
</script>
```

<span data-ttu-id="26b9f-249">此方法对于 Blazor Server 更为重要，因为每个事件调用都涉及通过网络传递消息。</span><span class="sxs-lookup"><span data-stu-id="26b9f-249">This technique can be even more important for Blazor Server, since each event invocation involves delivering a message over the network.</span></span> <span data-ttu-id="26b9f-250">它对于 Blazor WebAssembly 非常有用，因为它可以极大地减少呈现工作量。</span><span class="sxs-lookup"><span data-stu-id="26b9f-250">It's valuable for Blazor WebAssembly because it can greatly reduce the amount of rendering work.</span></span>

## <a name="optimize-javascript-interop-speed"></a><span data-ttu-id="26b9f-251">优化 JavaScript 互操作速度</span><span class="sxs-lookup"><span data-stu-id="26b9f-251">Optimize JavaScript interop speed</span></span>

<span data-ttu-id="26b9f-252">.NET 和 JavaScript 之间的调用涉及一些额外的开销，因为：</span><span class="sxs-lookup"><span data-stu-id="26b9f-252">Calls between .NET and JavaScript involve some additional overhead because:</span></span>

* <span data-ttu-id="26b9f-253">默认情况下，调用是异步的。</span><span class="sxs-lookup"><span data-stu-id="26b9f-253">By default, calls are asynchronous.</span></span>
* <span data-ttu-id="26b9f-254">默认情况下，参数和返回值已进行 JSON 序列化。</span><span class="sxs-lookup"><span data-stu-id="26b9f-254">By default, parameters and return values are JSON-serialized.</span></span> <span data-ttu-id="26b9f-255">这是为了在 .NET 和 JavaScript 类型之间提供一种易于理解的转换机制。</span><span class="sxs-lookup"><span data-stu-id="26b9f-255">This is to provide an easy-to-understand conversion mechanism between .NET and JavaScript types.</span></span>

<span data-ttu-id="26b9f-256">此外，在 Blazor Server 上，这些调用通过网络传递。</span><span class="sxs-lookup"><span data-stu-id="26b9f-256">Additionally on Blazor Server, these calls are passed across the network.</span></span>

### <a name="avoid-excessively-fine-grained-calls"></a><span data-ttu-id="26b9f-257">避免过度细化的调用</span><span class="sxs-lookup"><span data-stu-id="26b9f-257">Avoid excessively fine-grained calls</span></span>

<span data-ttu-id="26b9f-258">由于每个调用都涉及一些开销，因此减少调用次数可能会有用。</span><span class="sxs-lookup"><span data-stu-id="26b9f-258">Since each call involves some overhead, it can be valuable to reduce the number of calls.</span></span> <span data-ttu-id="26b9f-259">请考虑以下代码，该代码在浏览器的 `localStorage` 存储中存储项的集合：</span><span class="sxs-lookup"><span data-stu-id="26b9f-259">Consider the following code, which stores a collection of items in the browser's `localStorage` store:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    foreach (var item in items)
    {
        await JS.InvokeVoidAsync("localStorage.setItem", item.Id, 
            JsonSerializer.Serialize(item));
    }
}
```

<span data-ttu-id="26b9f-260">前面的示例对每个项进行单独的 JS 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="26b9f-260">The preceding example makes a separate JS interop call for each item.</span></span> <span data-ttu-id="26b9f-261">而以下方法则会将 JS 互操作减少为单个调用：</span><span class="sxs-lookup"><span data-stu-id="26b9f-261">Instead, the following approach reduces the JS interop to a single call:</span></span>

```csharp
private async Task StoreAllInLocalStorage(IEnumerable<TodoItem> items)
{
    await JS.InvokeVoidAsync("storeAllInLocalStorage", items);
}
```

<span data-ttu-id="26b9f-262">相应的 JavaScript 函数定义如下：</span><span class="sxs-lookup"><span data-stu-id="26b9f-262">The corresponding JavaScript function defined as follows:</span></span>

```javascript
function storeAllInLocalStorage(items) {
  items.forEach(item => {
    localStorage.setItem(item.id, JSON.stringify(item));
  });
}
```

<span data-ttu-id="26b9f-263">对于 Blazor WebAssembly，这仅在进行大量 JS 互操作调用时才有影响。</span><span class="sxs-lookup"><span data-stu-id="26b9f-263">For Blazor WebAssembly, this usually only matters if you're making a large number of JS interop calls.</span></span>

### <a name="consider-making-synchronous-calls"></a><span data-ttu-id="26b9f-264">考虑进行同步调用</span><span class="sxs-lookup"><span data-stu-id="26b9f-264">Consider making synchronous calls</span></span>

<span data-ttu-id="26b9f-265">默认情况下，JavaScript 互操作调用是异步的，无论调用的代码是同步还是异步。</span><span class="sxs-lookup"><span data-stu-id="26b9f-265">JavaScript interop calls are asynchronous by default, regardless of whether the code being called is synchronous or asynchronous.</span></span> <span data-ttu-id="26b9f-266">这是为了确保组件与 Blazor WebAssembly 和 Blazor Server 兼容。</span><span class="sxs-lookup"><span data-stu-id="26b9f-266">This is to ensure components are compatible with both Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="26b9f-267">在 Blazor Server 上，所有 JavaScript 互操作调用都必须是异步的，因为它们是通过网络连接发送的。</span><span class="sxs-lookup"><span data-stu-id="26b9f-267">On Blazor Server, all JavaScript interop calls must be asynchronous because they are sent over a network connection.</span></span>

<span data-ttu-id="26b9f-268">如果你确定你的应用只在 Blazor WebAssembly 上运行，则可以选择执行同步 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="26b9f-268">If you know for certain that your app only ever runs on Blazor WebAssembly, you can choose to make synchronous JavaScript interop calls.</span></span> <span data-ttu-id="26b9f-269">与进行异步调用相比，它的开销略少，并且可能会导致呈现周期更少，因为在等待结果时没有中间状态。</span><span class="sxs-lookup"><span data-stu-id="26b9f-269">This has slightly less overhead than making asynchronous calls and can result in fewer render cycles because there is no intermediate state while awaiting results.</span></span>

<span data-ttu-id="26b9f-270">若要进行从 .NET 到 JavaScript 的同步调用，请将 <xref:Microsoft.JSInterop.IJSRuntime> 转换为 <xref:Microsoft.JSInterop.IJSInProcessRuntime>：</span><span class="sxs-lookup"><span data-stu-id="26b9f-270">To make a synchronous call from .NET to JavaScript, cast <xref:Microsoft.JSInterop.IJSRuntime> to <xref:Microsoft.JSInterop.IJSInProcessRuntime>:</span></span>

```razor
@inject IJSRuntime JS

...

@code {
    protected override void HandleSomeEvent()
    {
        var jsInProcess = (IJSInProcessRuntime)JS;
        var value = jsInProcess.Invoke<string>("javascriptFunctionIdentifier");
    }
}
```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="26b9f-271">使用 `IJSObjectReference` 时，可以通过转换为 `IJSInProcessObjectReference` 进行同步调用。</span><span class="sxs-lookup"><span data-stu-id="26b9f-271">When working with `IJSObjectReference`, you can make a synchronous call by casting to `IJSInProcessObjectReference`.</span></span>

::: moniker-end

<span data-ttu-id="26b9f-272">若要进行从 JavaScript 到 .NET 的同步调用，请使用 `DotNet.invokeMethod` 而不是 `DotNet.invokeMethodAsync`。</span><span class="sxs-lookup"><span data-stu-id="26b9f-272">To make a synchronous call from JavaScript to .NET, use `DotNet.invokeMethod` instead of `DotNet.invokeMethodAsync`.</span></span>

<span data-ttu-id="26b9f-273">同步调用在以下情况下起作用：</span><span class="sxs-lookup"><span data-stu-id="26b9f-273">Synchronous calls work if:</span></span>

* <span data-ttu-id="26b9f-274">应用在 Blazor WebAssembly（而不是 Blazor Server）上运行。</span><span class="sxs-lookup"><span data-stu-id="26b9f-274">The app is running on Blazor WebAssembly, not Blazor Server.</span></span>
* <span data-ttu-id="26b9f-275">调用的函数以同步方式返回值（它不是 `async` 方法，不会返回 .NET <xref:System.Threading.Tasks.Task> 或 JavaScript `Promise`）。</span><span class="sxs-lookup"><span data-stu-id="26b9f-275">The called function returns a value synchronously (it isn't an `async` method and doesn't return a .NET <xref:System.Threading.Tasks.Task> or JavaScript `Promise`).</span></span>

<span data-ttu-id="26b9f-276">有关详细信息，请参阅 <xref:blazor/call-javascript-from-dotnet>。</span><span class="sxs-lookup"><span data-stu-id="26b9f-276">For more information, see <xref:blazor/call-javascript-from-dotnet>.</span></span>

::: moniker range=">= aspnetcore-5.0"
 
### <a name="consider-making-unmarshalled-calls"></a><span data-ttu-id="26b9f-277">考虑进行已打乱的调用</span><span class="sxs-lookup"><span data-stu-id="26b9f-277">Consider making unmarshalled calls</span></span>

<span data-ttu-id="26b9f-278">在 Blazor WebAssembly 上运行时，可以进行从 .NET 到 JavaScript 的已打乱调用。</span><span class="sxs-lookup"><span data-stu-id="26b9f-278">When running on Blazor WebAssembly, it's possible to make unmarshalled calls from .NET to JavaScript.</span></span> <span data-ttu-id="26b9f-279">这些是不执行参数或返回值的 JSON 序列化的同步调用。</span><span class="sxs-lookup"><span data-stu-id="26b9f-279">These are synchronous calls that don't perform JSON serialization of arguments or return values.</span></span> <span data-ttu-id="26b9f-280">内存管理以及 .NET 和 JavaScript 表示形式之间的转换的所有方面均留给开发人员处理。</span><span class="sxs-lookup"><span data-stu-id="26b9f-280">All aspects of memory management and translations between .NET and JavaScript representations are left up to the developer.</span></span>

> [!WARNING]
> <span data-ttu-id="26b9f-281">虽然使用 `IJSUnmarshalledRuntime` 这种 JS 互操作方法的开销最小，但与这些 API 交互所需的 JavaScript API 目前没有文档记录，而且可能会在将来的版本中出现中断性变更。</span><span class="sxs-lookup"><span data-stu-id="26b9f-281">While using `IJSUnmarshalledRuntime` has the least overhead of the JS interop approaches, the JavaScript APIs required to interact with these APIs are currently undocumented and subject to breaking changes in future releases.</span></span>

```javascript
function jsInteropCall() {
    return BINDING.js_to_mono_obj("Hello world");
}
```

```razor
@inject IJSRuntime JS

@code {
    protected override void OnInitialized()
    {
        var unmarshalledJs = (IJSUnmarshalledRuntime)JS;
        var value = unmarshalledJs.InvokeUnmarshalled<string>("jsInteropCall");
    }
}
```

::: moniker-end

## <a name="minimize-app-download-size"></a><span data-ttu-id="26b9f-282">最小化应用下载大小</span><span class="sxs-lookup"><span data-stu-id="26b9f-282">Minimize app download size</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="intermediate-language-il-trimming"></a><span data-ttu-id="26b9f-283">中间语言 (IL) 剪裁</span><span class="sxs-lookup"><span data-stu-id="26b9f-283">Intermediate Language (IL) trimming</span></span>

<span data-ttu-id="26b9f-284">[从 Blazor WebAssembly 应用修剪未使用的程序集](xref:blazor/host-and-deploy/configure-trimmer)会通过删除应用的二进制文件中的未使用代码来减小应用的大小。</span><span class="sxs-lookup"><span data-stu-id="26b9f-284">[Trimming unused assemblies from a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-trimmer) reduces the app's size by removing unused code in the app's binaries.</span></span> <span data-ttu-id="26b9f-285">默认情况下，裁边器在发布应用程序时执行。</span><span class="sxs-lookup"><span data-stu-id="26b9f-285">By default, the Trimmer is executed when publishing an application.</span></span> <span data-ttu-id="26b9f-286">要从剪裁中受益，请使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令发布应用用于部署，并将 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 选项设置为 `Release`：</span><span class="sxs-lookup"><span data-stu-id="26b9f-286">To benefit from trimming, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="intermediate-language-il-linking"></a><span data-ttu-id="26b9f-287">中间语言 (IL) 链接</span><span class="sxs-lookup"><span data-stu-id="26b9f-287">Intermediate Language (IL) linking</span></span>

<span data-ttu-id="26b9f-288">通过[链接 Blazor WebAssembly 应用](xref:blazor/host-and-deploy/configure-linker)，可剪裁应用二进制文件中未使用的代码来减小应用的大小。</span><span class="sxs-lookup"><span data-stu-id="26b9f-288">[Linking a Blazor WebAssembly app](xref:blazor/host-and-deploy/configure-linker) reduces the app's size by trimming unused code in the app's binaries.</span></span> <span data-ttu-id="26b9f-289">默认情况下，仅在 `Release` 配置中生成时才启用中间语言 (IL) 链接器。</span><span class="sxs-lookup"><span data-stu-id="26b9f-289">By default, the Intermediate Language (IL) Linker is only enabled when building in `Release` configuration.</span></span> <span data-ttu-id="26b9f-290">要从此中受益，请使用 [`dotnet publish`](/dotnet/core/tools/dotnet-publish) 命令发布应用用于部署，并将 [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) 选项设置为 `Release`：</span><span class="sxs-lookup"><span data-stu-id="26b9f-290">To benefit from this, publish the app for deployment using the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the [-c|--configuration](/dotnet/core/tools/dotnet-publish#options) option set to `Release`:</span></span>

::: moniker-end

```dotnetcli
dotnet publish -c Release
```

### <a name="use-systemtextjson"></a><span data-ttu-id="26b9f-291">使用 System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="26b9f-291">Use System.Text.Json</span></span>

<span data-ttu-id="26b9f-292">Blazor 的 JS 互操作实现依赖于 <xref:System.Text.Json> - 这是一个性能高但内存分配较低的 JSON 序列化库。</span><span class="sxs-lookup"><span data-stu-id="26b9f-292">Blazor's JS interop implementation relies on <xref:System.Text.Json>, which is a high-performance JSON serialization library with low memory allocation.</span></span> <span data-ttu-id="26b9f-293">与添加一个或多个备用 JSON 库相比，使用 <xref:System.Text.Json> 不会增加应用有效负载的大小。</span><span class="sxs-lookup"><span data-stu-id="26b9f-293">Using <xref:System.Text.Json> doesn't result in additional app payload size over adding one or more alternate JSON libraries.</span></span>

<span data-ttu-id="26b9f-294">有关迁移指南，请参阅[如何从 `Newtonsoft.Json` 迁移到 `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to)。</span><span class="sxs-lookup"><span data-stu-id="26b9f-294">For migration guidance, see [How to migrate from `Newtonsoft.Json` to `System.Text.Json`](/dotnet/standard/serialization/system-text-json-migrate-from-newtonsoft-how-to).</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="26b9f-295">延迟加载程序集</span><span class="sxs-lookup"><span data-stu-id="26b9f-295">Lazy load assemblies</span></span>

<span data-ttu-id="26b9f-296">当路由需要程序集时，在运行时加载程序集。</span><span class="sxs-lookup"><span data-stu-id="26b9f-296">Load assemblies at runtime when the assemblies are required by a route.</span></span> <span data-ttu-id="26b9f-297">有关详细信息，请参阅 <xref:blazor/webassembly-lazy-load-assemblies>。</span><span class="sxs-lookup"><span data-stu-id="26b9f-297">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="compression"></a><span data-ttu-id="26b9f-298">压缩</span><span class="sxs-lookup"><span data-stu-id="26b9f-298">Compression</span></span>

<span data-ttu-id="26b9f-299">发布 Blazor WebAssembly 应用时，将在发布过程中对输出内容进行静态压缩，从而减小应用的大小，并免去运行时压缩的开销。</span><span class="sxs-lookup"><span data-stu-id="26b9f-299">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="26b9f-300">Blazor 依赖服务器来执行内容协商和提供静态压缩的文件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-300">Blazor relies on the server to perform content negotation and serve statically-compressed files.</span></span>

<span data-ttu-id="26b9f-301">部署应用后，请验证该应用是否提供压缩的文件。</span><span class="sxs-lookup"><span data-stu-id="26b9f-301">After an app is deployed, verify that the app serves compressed files.</span></span> <span data-ttu-id="26b9f-302">检查浏览器开发人员工具中的“网络”选项卡，并验证文件是否具有 `Content-Encoding: br` 或 `Content-Encoding: gz`。</span><span class="sxs-lookup"><span data-stu-id="26b9f-302">Inspect the Network tab in a browser's Developer Tools and verify that the files are served with `Content-Encoding: br` or `Content-Encoding: gz`.</span></span> <span data-ttu-id="26b9f-303">如果主机未提供压缩的文件，请按照 <xref:blazor/host-and-deploy/webassembly#compression> 中的说明操作。</span><span class="sxs-lookup"><span data-stu-id="26b9f-303">If the host isn't serving compressed files, follow the instructions in <xref:blazor/host-and-deploy/webassembly#compression>.</span></span>

### <a name="disable-unused-features"></a><span data-ttu-id="26b9f-304">禁用未使用的功能</span><span class="sxs-lookup"><span data-stu-id="26b9f-304">Disable unused features</span></span>

<span data-ttu-id="26b9f-305">Blazor WebAssembly 的运行时包含以下 .NET 功能；如果应用不需要这些功能就能减少有效负载的大小，可将它们禁用：</span><span class="sxs-lookup"><span data-stu-id="26b9f-305">Blazor WebAssembly's runtime includes the following .NET features that can be disabled if the app doesn't require them for a smaller payload size:</span></span>

* <span data-ttu-id="26b9f-306">包含数据文件来确保时区信息正确。</span><span class="sxs-lookup"><span data-stu-id="26b9f-306">A data file is included to make timezone information correct.</span></span> <span data-ttu-id="26b9f-307">如果应用不需要此功能，请考虑通过将应用项目文件中的 `BlazorEnableTimeZoneSupport` MSBuild 属性设置为 `false` 来禁用它：</span><span class="sxs-lookup"><span data-stu-id="26b9f-307">If the app doesn't require this feature, consider disabling it by setting the `BlazorEnableTimeZoneSupport` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorEnableTimeZoneSupport>false</BlazorEnableTimeZoneSupport>
  </PropertyGroup>
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="26b9f-308">默认情况下，Blazor WebAssembly 携带在用户区域性中显示值（如日期和货币）所需的全球化资源。</span><span class="sxs-lookup"><span data-stu-id="26b9f-308">By default, Blazor WebAssembly carries globalization resources required to display values, such as dates and currency, in the user's culture.</span></span> <span data-ttu-id="26b9f-309">如果应用不需要本地化，你可以[将应用配置为支持不变区域性](xref:blazor/globalization-localization)，这基于 `en-US` 区域性：</span><span class="sxs-lookup"><span data-stu-id="26b9f-309">If the app doesn't require localization, you may [configure the app to support the invariant culture](xref:blazor/globalization-localization), which is based on the `en-US` culture:</span></span>

  ```xml
  <PropertyGroup>
    <InvariantGlobalization>true</InvariantGlobalization>
  </PropertyGroup>
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="26b9f-310">包括排序规则信息来确保 <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> 之类的 API 正常工作。</span><span class="sxs-lookup"><span data-stu-id="26b9f-310">Collation information is included to make APIs such as <xref:System.StringComparison.InvariantCultureIgnoreCase?displayProperty=nameWithType> work correctly.</span></span> <span data-ttu-id="26b9f-311">如果确定应用不需要排序规则数据，请考虑通过将应用项目文件中的 `BlazorWebAssemblyPreserveCollationData` MSBuild 属性设置为 `false` 来禁用它：</span><span class="sxs-lookup"><span data-stu-id="26b9f-311">If you're certain that the app doesn't require the collation data, consider disabling it by setting the `BlazorWebAssemblyPreserveCollationData` MSBuild property in the app's project file to `false`:</span></span>

  ```xml
  <PropertyGroup>
    <BlazorWebAssemblyPreserveCollationData>false</BlazorWebAssemblyPreserveCollationData>
  </PropertyGroup>
  ```

::: moniker-end
