---
title: ASP.NET Core Blazor 组件呈现
author: guardrex
description: 了解 ASP.NET Core Blazor 应用中的 Razor 组件呈现，包括何时调用 StateHasChanged。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2021
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
uid: blazor/components/rendering
ms.openlocfilehash: 1d244434cd3aa7e1a49839cc0c6cecb61abbcdff
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711329"
---
# <a name="aspnet-core-blazor-component-rendering"></a><span data-ttu-id="b9212-103">ASP.NET Core Blazor 组件呈现</span><span class="sxs-lookup"><span data-stu-id="b9212-103">ASP.NET Core Blazor component rendering</span></span>

<span data-ttu-id="b9212-104">当组件第一次通过父组件添加到组件层次结构时，它们必须呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-104">Components *must* render when they're first added to the component hierarchy by a parent component.</span></span> <span data-ttu-id="b9212-105">只有在这种情况下，组件才必须呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-105">This is the only time that a component must render.</span></span> <span data-ttu-id="b9212-106">在其他情况下，组件可以按照各自的逻辑和约定呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-106">Components *may* render at other times according to their own logic and conventions.</span></span>

## <a name="rendering-conventions-for-componentbase"></a><span data-ttu-id="b9212-107">`ComponentBase` 的呈现约定</span><span class="sxs-lookup"><span data-stu-id="b9212-107">Rendering conventions for `ComponentBase`</span></span>

<span data-ttu-id="b9212-108">默认情况下，Razor 组件继承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 基类，该基类包含在以下时间触发重新呈现的逻辑：</span><span class="sxs-lookup"><span data-stu-id="b9212-108">By default, Razor components inherit from the <xref:Microsoft.AspNetCore.Components.ComponentBase> base class, which contains logic to trigger rerendering at the following times:</span></span>

* <span data-ttu-id="b9212-109">从父组件应用一组已更新的[参数](xref:blazor/components/data-binding#binding-with-component-parameters)之后。</span><span class="sxs-lookup"><span data-stu-id="b9212-109">After applying an updated set of [parameters](xref:blazor/components/data-binding#binding-with-component-parameters) from a parent component.</span></span>
* <span data-ttu-id="b9212-110">为[级联参数](xref:blazor/components/cascading-values-and-parameters)应用已更新的值之后。</span><span class="sxs-lookup"><span data-stu-id="b9212-110">After applying an updated value for a [cascading parameter](xref:blazor/components/cascading-values-and-parameters).</span></span>
* <span data-ttu-id="b9212-111">通知事件并调用其自己的某个[事件处理程序](xref:blazor/components/event-handling)之后。</span><span class="sxs-lookup"><span data-stu-id="b9212-111">After notification of an event and invoking one of its own [event handlers](xref:blazor/components/event-handling).</span></span>
* <span data-ttu-id="b9212-112">调用自己的 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 方法之后（请参阅 [Blazor 生命周期：状态更改](xref:blazor/components/lifecycle#state-changes)）。</span><span class="sxs-lookup"><span data-stu-id="b9212-112">After a call to its own <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> method (see [Blazor lifecycle: State changes](xref:blazor/components/lifecycle#state-changes)).</span></span>

<span data-ttu-id="b9212-113">如果满足以下任一条件，则继承自 <xref:Microsoft.AspNetCore.Components.ComponentBase> 的组件会跳过因参数更新而触发的重新呈现：</span><span class="sxs-lookup"><span data-stu-id="b9212-113">Components inherited from <xref:Microsoft.AspNetCore.Components.ComponentBase> skip rerenders due to parameter updates if either of the following are true:</span></span>

* <span data-ttu-id="b9212-114">所有参数值都是已知的不可变基元类型（例如，`int`、`string`、`DateTime`），并且自上一组参数设置后就没有改变过。</span><span class="sxs-lookup"><span data-stu-id="b9212-114">All of the parameter values are of known immutable primitive types, such as `int`, `string`, `DateTime`, and haven't changed since the previous set of parameters were set.</span></span>
* <span data-ttu-id="b9212-115">组件的 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 方法返回 `false`。</span><span class="sxs-lookup"><span data-stu-id="b9212-115">The component's <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> method returns `false`.</span></span>

<span data-ttu-id="b9212-116">有关 <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A> 的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>。</span><span class="sxs-lookup"><span data-stu-id="b9212-116">For more information on <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>, see <xref:blazor/webassembly-performance-best-practices#use-of-shouldrender>.</span></span>

## <a name="control-the-rendering-flow"></a><span data-ttu-id="b9212-117">控制呈现流</span><span class="sxs-lookup"><span data-stu-id="b9212-117">Control the rendering flow</span></span>

<span data-ttu-id="b9212-118">在大多数情况下，<xref:Microsoft.AspNetCore.Components.ComponentBase> 约定会在某个事件发生后导致重新呈现正确的组件子集。</span><span class="sxs-lookup"><span data-stu-id="b9212-118">In most cases, <xref:Microsoft.AspNetCore.Components.ComponentBase> conventions result in the correct subset of component rerenders after an event occurs.</span></span> <span data-ttu-id="b9212-119">开发人员通常不需要提供手动逻辑来告诉框架，要重新呈现哪些组件以及何时重新呈现它们。</span><span class="sxs-lookup"><span data-stu-id="b9212-119">Developers aren't usually required to provide manual logic to tell the framework which components to rerender and when to rerender them.</span></span> <span data-ttu-id="b9212-120">框架约定的总体效果如下：接收事件的组件重新呈现自身，从而递归触发参数值可能已更改的后代组件的重新呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-120">The overall effect of the framework's conventions is that the component receiving an event rerenders itself, which recursively triggers rerendering of descendant components whose parameter values may have changed.</span></span>

<span data-ttu-id="b9212-121">有关框架约定对性能的影响以及如何针对呈现优化应用的组件层次结构的详细信息，请参阅 <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>。</span><span class="sxs-lookup"><span data-stu-id="b9212-121">For more information on the performance implications of the framework's conventions and how to optimize an app's component hierarchy for rendering, see <xref:blazor/webassembly-performance-best-practices#optimize-rendering-speed>.</span></span>

## <a name="when-to-call-statehaschanged"></a><span data-ttu-id="b9212-122">何时调用 `StateHasChanged`</span><span class="sxs-lookup"><span data-stu-id="b9212-122">When to call `StateHasChanged`</span></span>

<span data-ttu-id="b9212-123">通过调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，可以随时触发呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-123">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> allows you to trigger a render at any time.</span></span> <span data-ttu-id="b9212-124">但请注意，不必要时，不要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，这是一个常见错误，会产生不必要的呈现成本。</span><span class="sxs-lookup"><span data-stu-id="b9212-124">However, be careful not to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> unnecessarily, which is a common mistake that imposes unnecessary rendering costs.</span></span>

<span data-ttu-id="b9212-125">对于以下情况，代码不需要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>：</span><span class="sxs-lookup"><span data-stu-id="b9212-125">Code shouldn't need to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when:</span></span>

* <span data-ttu-id="b9212-126">定期处理事件（无论是同步还是异步），因为 <xref:Microsoft.AspNetCore.Components.ComponentBase> 会触发大多数常规事件处理程序的呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-126">Routinely handling events, whether synchronously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for most routine event handlers.</span></span>
* <span data-ttu-id="b9212-127">实现 [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) 或 [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set) 等典型生命周期逻辑（无论是同步还是异步），因为 <xref:Microsoft.AspNetCore.Components.ComponentBase> 会触发典型生命周期事件的呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-127">Implementing typical lifecycle logic, such as [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) or [`OnParametersSetAsync`](xref:blazor/components/lifecycle#after-parameters-are-set), whether synchonrously or asynchronously, since <xref:Microsoft.AspNetCore.Components.ComponentBase> triggers a render for typical lifecycle events.</span></span>

<span data-ttu-id="b9212-128">但是，在本文以下部分所述的情况下，可能适合调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>：</span><span class="sxs-lookup"><span data-stu-id="b9212-128">However, it might make sense to call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in the cases described in the following sections of this article:</span></span>

* [<span data-ttu-id="b9212-129">异步处理程序涉及多个异步阶段</span><span class="sxs-lookup"><span data-stu-id="b9212-129">An asynchronous handler involves multiple asynchronous phases</span></span>](#an-asynchronous-handler-involves-multiple-asynchronous-phases)
* [<span data-ttu-id="b9212-130">从 Blazor 呈现和事件处理系统外部接收调用</span><span class="sxs-lookup"><span data-stu-id="b9212-130">Receiving a call from something external to the Blazor rendering and event handling system</span></span>](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)
* [<span data-ttu-id="b9212-131">在由特定事件重新呈现的子树之外呈现组件</span><span class="sxs-lookup"><span data-stu-id="b9212-131">To render component outside the subtree that is rerendered by a particular event</span></span>](#to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event)

### <a name="an-asynchronous-handler-involves-multiple-asynchronous-phases"></a><span data-ttu-id="b9212-132">异步处理程序涉及多个异步阶段</span><span class="sxs-lookup"><span data-stu-id="b9212-132">An asynchronous handler involves multiple asynchronous phases</span></span>

<span data-ttu-id="b9212-133">由于在 .NET 中定义任务的方式，<xref:System.Threading.Tasks.Task> 的接收方只能观察到其最终完成状态，而观察不到中间异步状态。</span><span class="sxs-lookup"><span data-stu-id="b9212-133">Due to the way that tasks are defined in .NET, a receiver of a <xref:System.Threading.Tasks.Task> can only observe its final completion, not intermediate asynchronous states.</span></span> <span data-ttu-id="b9212-134">因此，仅在第一次返回 <xref:System.Threading.Tasks.Task> 且 <xref:System.Threading.Tasks.Task> 最终完成时，<xref:Microsoft.AspNetCore.Components.ComponentBase> 才能触发重新呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-134">Therefore, <xref:Microsoft.AspNetCore.Components.ComponentBase> can only trigger rerendering when the <xref:System.Threading.Tasks.Task> is first returned and when the <xref:System.Threading.Tasks.Task> finally completes.</span></span> <span data-ttu-id="b9212-135">框架无法知道要在其他中间点重新呈现组件。</span><span class="sxs-lookup"><span data-stu-id="b9212-135">The framework can't know to rerender a component at other intermediate points.</span></span> <span data-ttu-id="b9212-136">如果要在中间点重新呈现，请在这些点调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="b9212-136">If you want to rerender at intermediate points, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> at those points.</span></span>

<span data-ttu-id="b9212-137">以下面的 `CounterState1` 组件为例，该组件在每次单击时都会更新计数四次：</span><span class="sxs-lookup"><span data-stu-id="b9212-137">Consider the following `CounterState1` component, which updates the count four times on each click:</span></span>

* <span data-ttu-id="b9212-138">自动呈现在 `currentCount` 第一次和最后一次递增之后发生。</span><span class="sxs-lookup"><span data-stu-id="b9212-138">Automatic renders occur after the first and last increments of `currentCount`.</span></span>
* <span data-ttu-id="b9212-139">当框架未在 `currentCount` 递增的中间处理点自动触发重新呈现时，通过调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 触发手动呈现。</span><span class="sxs-lookup"><span data-stu-id="b9212-139">Manual renders are triggered by calls to <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> when the framework doesn't automatically trigger rerenders at intermediate processing points where `currentCount` is incremented.</span></span>

<span data-ttu-id="b9212-140">`Pages/CounterState1.razor`:</span><span class="sxs-lookup"><span data-stu-id="b9212-140">`Pages/CounterState1.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/rendering/CounterState1.razor?highlight=17,21,25,29)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/rendering/CounterState1.razor?highlight=17,21,25,29)]

::: moniker-end

### <a name="receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system"></a><span data-ttu-id="b9212-141">从 Blazor 呈现和事件处理系统外部接收调用</span><span class="sxs-lookup"><span data-stu-id="b9212-141">Receiving a call from something external to the Blazor rendering and event handling system</span></span>

<span data-ttu-id="b9212-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> 只知道其自身的生命周期方法和 Blazor 触发的事件。</span><span class="sxs-lookup"><span data-stu-id="b9212-142"><xref:Microsoft.AspNetCore.Components.ComponentBase> only knows about its own lifecycle methods and Blazor-triggered events.</span></span> <span data-ttu-id="b9212-143"><xref:Microsoft.AspNetCore.Components.ComponentBase> 不知道代码中可能发生的其他事件。</span><span class="sxs-lookup"><span data-stu-id="b9212-143"><xref:Microsoft.AspNetCore.Components.ComponentBase> doesn't know about other events that may occur in code.</span></span> <span data-ttu-id="b9212-144">例如，Blazor 不知道自定义数据存储引发的任何 C# 事件。</span><span class="sxs-lookup"><span data-stu-id="b9212-144">For example, any C# events raised by a custom data store are unknown to Blazor.</span></span> <span data-ttu-id="b9212-145">为了使此类事件触发重新呈现，从而在 UI 中显示已更新的值，请调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>。</span><span class="sxs-lookup"><span data-stu-id="b9212-145">In order for such events to trigger rerendering to display updated values in the UI, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>.</span></span>

<span data-ttu-id="b9212-146">以下面的 `CounterState2` 组件为例，该组件使用 <xref:System.Timers.Timer?displayProperty=fullName> 定期更新计数并调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 来更新 UI：</span><span class="sxs-lookup"><span data-stu-id="b9212-146">Consider the following `CounterState2` component that uses <xref:System.Timers.Timer?displayProperty=fullName> to update a count at a regular interval and calls <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> to update the UI:</span></span>

* <span data-ttu-id="b9212-147">`OnTimerCallback` 在 Blazor 管理的任何呈现流或事件通知之外运行。</span><span class="sxs-lookup"><span data-stu-id="b9212-147">`OnTimerCallback` runs outside of any Blazor-managed rendering flow or event notification.</span></span> <span data-ttu-id="b9212-148">因此，`OnTimerCallback` 必须调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，因为 Blazor 不知道回调中的 `currentCount` 更改。</span><span class="sxs-lookup"><span data-stu-id="b9212-148">Therefore, `OnTimerCallback` must call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> because Blazor isn't aware of the changes to `currentCount` in the callback.</span></span>
* <span data-ttu-id="b9212-149">组件实现 <xref:System.IDisposable>，当框架调用 `Dispose` 方法时，其中的 <xref:System.Timers.Timer> 将释放。</span><span class="sxs-lookup"><span data-stu-id="b9212-149">The component implements <xref:System.IDisposable>, where the <xref:System.Timers.Timer> is disposed when the framework calls the `Dispose` method.</span></span> <span data-ttu-id="b9212-150">有关详细信息，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。</span><span class="sxs-lookup"><span data-stu-id="b9212-150">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

<span data-ttu-id="b9212-151">由于回调是在 Blazor 的同步上下文之外调用的，因此组件必须将 `OnTimerCallback` 的逻辑包装在 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> 中，以将其移到呈现器的同步上下文中。</span><span class="sxs-lookup"><span data-stu-id="b9212-151">Because the callback is invoked outside of Blazor's synchronization context, the component must wrap the logic of `OnTimerCallback` in <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A?displayProperty=nameWithType> to move it onto the renderer's synchronization context.</span></span> <span data-ttu-id="b9212-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> 只能从呈现器的同步上下文调用，否则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="b9212-152"><xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> can only be called from the renderer's synchronization context and throws an exception otherwise.</span></span> <span data-ttu-id="b9212-153">这等效于封送到其他 UI 框架中的 UI 线程。</span><span class="sxs-lookup"><span data-stu-id="b9212-153">This is equivalent to marshalling to the UI thread in other UI frameworks.</span></span>

<span data-ttu-id="b9212-154">`Pages/CounterState2.razor`:</span><span class="sxs-lookup"><span data-stu-id="b9212-154">`Pages/CounterState2.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/rendering/CounterState2.razor?highlight=26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/rendering/CounterState2.razor?highlight=26)]

::: moniker-end

### <a name="to-render-a-component-outside-the-subtree-thats-rerendered-by-a-particular-event"></a><span data-ttu-id="b9212-155">在由特定事件重新呈现的子树之外呈现组件</span><span class="sxs-lookup"><span data-stu-id="b9212-155">To render a component outside the subtree that's rerendered by a particular event</span></span>

<span data-ttu-id="b9212-156">UI 可能涉及：</span><span class="sxs-lookup"><span data-stu-id="b9212-156">The UI might involve:</span></span>

1. <span data-ttu-id="b9212-157">向一个组件调度事件。</span><span class="sxs-lookup"><span data-stu-id="b9212-157">Dispatching an event to one component.</span></span>
1. <span data-ttu-id="b9212-158">更改某种状态。</span><span class="sxs-lookup"><span data-stu-id="b9212-158">Changing some state.</span></span>
1. <span data-ttu-id="b9212-159">重新呈现完全不同的组件（该组件不是接收事件的组件的后代）。</span><span class="sxs-lookup"><span data-stu-id="b9212-159">Rerendering a completely different component that isn't a descendant of the component receiving the event.</span></span>

<span data-ttu-id="b9212-160">处理此情况的一种方法是，提供状态管理类（通常作为依赖关系注入 (DI) 服务）注入到多个组件。</span><span class="sxs-lookup"><span data-stu-id="b9212-160">One way to deal with this scenario is to provide a *state management* class, often as a dependency injection (DI) service, injected into multiple components.</span></span> <span data-ttu-id="b9212-161">当一个组件在状态管理器上调用方法时，状态管理器引发 C# 事件，然后由独立组件接收该事件。</span><span class="sxs-lookup"><span data-stu-id="b9212-161">When one component calls a method on the state manager, the state manager raises a C# event that's then received by an independent component.</span></span>

<span data-ttu-id="b9212-162">由于这些 C# 事件位于 Blazor 呈现管道之外，因此，请对要呈现的其他组件调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>，以响应状态管理器的事件。</span><span class="sxs-lookup"><span data-stu-id="b9212-162">Since these C# events are outside the Blazor rendering pipeline, call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> on other components you wish to render in response to the state manager's events.</span></span>

<span data-ttu-id="b9212-163">这与前面的 <xref:System.Timers.Timer?displayProperty=fullName> 案例（[上一部分](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system)中）类似。</span><span class="sxs-lookup"><span data-stu-id="b9212-163">This is similar to the earlier case with <xref:System.Timers.Timer?displayProperty=fullName> in the [previous section](#receiving-a-call-from-something-external-to-the-blazor-rendering-and-event-handling-system).</span></span> <span data-ttu-id="b9212-164">由于执行调用堆栈通常保留在呈现器的同步上下文中，因此通常不需要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>。</span><span class="sxs-lookup"><span data-stu-id="b9212-164">Since the execution call stack typically remains on the renderer's synchronization context, calling <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> isn't normally required.</span></span> <span data-ttu-id="b9212-165">仅当逻辑转义同步上下文时才需要调用 <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A>，例如，对 <xref:System.Threading.Tasks.Task> 调用 <xref:System.Threading.Tasks.Task.ContinueWith%2A> 或使用 [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A) 等待 <xref:System.Threading.Tasks.Task>。</span><span class="sxs-lookup"><span data-stu-id="b9212-165">Calling <xref:Microsoft.AspNetCore.Components.ComponentBase.InvokeAsync%2A> is only required if the logic escapes the synchronization context, such as calling <xref:System.Threading.Tasks.Task.ContinueWith%2A> on a <xref:System.Threading.Tasks.Task> or awaiting a <xref:System.Threading.Tasks.Task> with [`ConfigureAwait(false)`](xref:System.Threading.Tasks.Task.ConfigureAwait%2A).</span></span>
