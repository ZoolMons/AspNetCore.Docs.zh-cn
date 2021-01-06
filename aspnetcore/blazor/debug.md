---
title: 调试 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何调试 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 990882c03ddc14c664aa8da0518fb36087199aca
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "94550512"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="84b57-103">调试 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="84b57-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="84b57-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="84b57-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="84b57-105">可以使用基于 Chromium 的浏览器 (Edge/Chrome) 中的浏览器开发工具调试 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="84b57-106">还可以使用以下集成开发环境 (IDE) 调试应用：</span><span class="sxs-lookup"><span data-stu-id="84b57-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="84b57-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84b57-107">Visual Studio</span></span>
* <span data-ttu-id="84b57-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84b57-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="84b57-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84b57-109">Visual Studio Code</span></span>

<span data-ttu-id="84b57-110">可用方案包括：</span><span class="sxs-lookup"><span data-stu-id="84b57-110">Available scenarios include:</span></span>

* <span data-ttu-id="84b57-111">设置和删除断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="84b57-112">在 IDE 中运行具有调试支持的应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="84b57-113">单步执行代码。</span><span class="sxs-lookup"><span data-stu-id="84b57-113">Single-step through the code.</span></span>
* <span data-ttu-id="84b57-114">在 IDE 中使用键盘快捷方式恢复代码执行。</span><span class="sxs-lookup"><span data-stu-id="84b57-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="84b57-115">在“局部变量”窗口中，观察局部变量的值。</span><span class="sxs-lookup"><span data-stu-id="84b57-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="84b57-116">请参阅调用堆栈，包括 JavaScript 和 .NET 之间的调用链。</span><span class="sxs-lookup"><span data-stu-id="84b57-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="84b57-117">目前，无法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="84b57-117">For now, you *can't*:</span></span>

* <span data-ttu-id="84b57-118">出现未经处理的异常时中断。</span><span class="sxs-lookup"><span data-stu-id="84b57-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="84b57-119">于应用启动期间在调试代理运行之前命中断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="84b57-120">这包括 `Program.Main` (`Program.cs`) 中的断点和组件的 [`OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods) 中的断点，其中这些组件由请求自应用的第一页加载。</span><span class="sxs-lookup"><span data-stu-id="84b57-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="84b57-121">在非本地方案中调试（例如，[适用于 Linux 的 Windows 子系统 (WSL)](/windows/wsl/) 或 [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)）。</span><span class="sxs-lookup"><span data-stu-id="84b57-121">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="84b57-122">先决条件</span><span class="sxs-lookup"><span data-stu-id="84b57-122">Prerequisites</span></span>

<span data-ttu-id="84b57-123">调试需要使用以下浏览器之一：</span><span class="sxs-lookup"><span data-stu-id="84b57-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="84b57-124">Google Chrome（版本 70 或更高版本）（默认）</span><span class="sxs-lookup"><span data-stu-id="84b57-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="84b57-125">Microsoft Edge（版本 80 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="84b57-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="84b57-126">确保防火墙或代理不会阻止与调试代理（`NodeJS` 进程）之间的通信。</span><span class="sxs-lookup"><span data-stu-id="84b57-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="84b57-127">有关详细信息，请参阅[防火墙配置](#firewall-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="84b57-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="84b57-128">Visual Studio for Mac 需要版本 8.8（内部版本 1532）或更高版本：</span><span class="sxs-lookup"><span data-stu-id="84b57-128">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="84b57-129">选择 [Microsoft：Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) 页面上的“下载 Visual Studio for Mac”按钮，安装最新版本的 Visual Studio for Mac。</span><span class="sxs-lookup"><span data-stu-id="84b57-129">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="84b57-130">从 Visual Studio 中选择预览通道。</span><span class="sxs-lookup"><span data-stu-id="84b57-130">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="84b57-131">有关详细信息，请参阅[安装 Visual Studio for Mac 的预览版本](/visualstudio/mac/install-preview)。</span><span class="sxs-lookup"><span data-stu-id="84b57-131">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="84b57-132">当前不支持 macOS 上的 Apple Safari。</span><span class="sxs-lookup"><span data-stu-id="84b57-132">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="84b57-133">启用调试</span><span class="sxs-lookup"><span data-stu-id="84b57-133">Enable debugging</span></span>

<span data-ttu-id="84b57-134">若要为现有 Blazor WebAssembly 应用启用调试，请更新启动项目中的 `launchSettings.json` 文件，使每个启动配置文件包含以下 `inspectUri` 属性：</span><span class="sxs-lookup"><span data-stu-id="84b57-134">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="84b57-135">更新后，`launchSettings.json` 文件应类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="84b57-135">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="84b57-136">`inspectUri` 属性具有以下作用：</span><span class="sxs-lookup"><span data-stu-id="84b57-136">The `inspectUri` property:</span></span>

* <span data-ttu-id="84b57-137">使 IDE 能够检测到该应用为 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-137">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="84b57-138">指示脚本调试基础结构通过 Blazor 的调试代理连接到浏览器。</span><span class="sxs-lookup"><span data-stu-id="84b57-138">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="84b57-139">已启动的浏览器 (`browserInspectUri`) 上 WebSocket 协议 (`wsProtocol`)、主机 (`url.hostname`)、端口 (`url.port`) 和检查器 URI 的占位符值由框架提供。</span><span class="sxs-lookup"><span data-stu-id="84b57-139">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="84b57-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="84b57-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="84b57-141">要在 Visual Studio 中调试 Blazor WebAssembly 应用，请按以下步骤执行：</span><span class="sxs-lookup"><span data-stu-id="84b57-141">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="84b57-142">创建新的 ASP.NET Core 托管 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-142">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="84b57-143">按 <kbd>F5</kbd> 在调试器中运行应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-143">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="84b57-144">不支持“启动时不调试”(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="84b57-144">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="84b57-145">当应用以调试配置运行时，调试开销始终会导致性能的小幅下降。</span><span class="sxs-lookup"><span data-stu-id="84b57-145">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="84b57-146">在 `*Client*` 应用中，在 `Pages/Counter.razor` 中的 `currentCount++;` 行上设置断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-146">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="84b57-147">在浏览器中，导航到 `Counter` 页，然后选择“单击此处”按钮以命中断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-147">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="84b57-148">在 Visual Studio 中，检查“局部变量”窗口中 `currentCount` 字段的值。</span><span class="sxs-lookup"><span data-stu-id="84b57-148">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="84b57-149">按 <kbd>F5</kbd> 继续执行。</span><span class="sxs-lookup"><span data-stu-id="84b57-149">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="84b57-150">调试 Blazor WebAssembly 应用时，还可以调试服务器代码：</span><span class="sxs-lookup"><span data-stu-id="84b57-150">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="84b57-151">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 的“`Pages/FetchData.razor`”页中设置断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-151">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="84b57-152">在 `Get` 操作方法的 `WeatherForecastController` 中设置一个断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-152">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="84b57-153">浏览到 `Fetch Data` 页，在 `FetchData` 组件中的首个断点向服务器发出 HTTP 请求前命中该断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-153">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="84b57-154">按 <kbd>F5</kbd> 以继续执行，然后在服务器上命中 `WeatherForecastController` 中的断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-154">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="84b57-155">再次按 <kbd>F5</kbd> 以继续执行，并查看浏览器中呈现的天气预报表。</span><span class="sxs-lookup"><span data-stu-id="84b57-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="84b57-156">在运行调试代理之前，在应用启动期间不会命中断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-156">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="84b57-157">这包括 `Program.Main` (`Program.cs`) 中的断点和组件的 [`OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods) 中的断点，其中这些组件由请求自应用的第一页加载。</span><span class="sxs-lookup"><span data-stu-id="84b57-157">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="84b57-158">如果应用托管在不同于 `/` 的[应用程序基路径](xref:blazor/host-and-deploy/index#app-base-path)上，请更新 `Properties/launchSettings.json` 中的以下属性，以反映应用程序的基路径：</span><span class="sxs-lookup"><span data-stu-id="84b57-158">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="84b57-159">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="84b57-159">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="84b57-160">每个配置文件的 `inspectUri`：</span><span class="sxs-lookup"><span data-stu-id="84b57-160">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="84b57-161">前面设置中的占位符：</span><span class="sxs-lookup"><span data-stu-id="84b57-161">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="84b57-162">`{INSECURE PORT}`：不安全的端口。</span><span class="sxs-lookup"><span data-stu-id="84b57-162">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="84b57-163">默认情况下提供随机值，但允许使用自定义端口。</span><span class="sxs-lookup"><span data-stu-id="84b57-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="84b57-164">`{APP BASE PATH}`：应用程序的基路径。</span><span class="sxs-lookup"><span data-stu-id="84b57-164">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="84b57-165">`{SECURE PORT}`：安全的端口。</span><span class="sxs-lookup"><span data-stu-id="84b57-165">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="84b57-166">默认情况下提供随机值，但允许使用自定义端口。</span><span class="sxs-lookup"><span data-stu-id="84b57-166">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="84b57-167">`{PROFILE 1, 2, ... N}`：启动设置配置文件。</span><span class="sxs-lookup"><span data-stu-id="84b57-167">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="84b57-168">通常，应用会默认指定多个配置文件（例如，IIS Express 的配置文件和 Kestrel 服务器使用的项目配置文件）。</span><span class="sxs-lookup"><span data-stu-id="84b57-168">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="84b57-169">在下面的示例中，应用托管在 `/OAT` 上，并在 `wwwroot/index.html` 中将应用程序基路径配置为 `<base href="/OAT/">`：</span><span class="sxs-lookup"><span data-stu-id="84b57-169">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="84b57-170">有关将自定义应用基路径用于 Blazor WebAssembly 应用的信息，请参阅 <xref:blazor/host-and-deploy/index#app-base-path>。</span><span class="sxs-lookup"><span data-stu-id="84b57-170">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="84b57-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="84b57-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="84b57-172">调试独立 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="84b57-172">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="84b57-173">在 VS Code 中打开独立 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-173">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="84b57-174">可能会收到通知，告诉你需要进行其他设置才能启用调试：</span><span class="sxs-lookup"><span data-stu-id="84b57-174">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="84b57-175">需要进行其他设置才能调试 Blazor WebAssembly 应用程序。</span><span class="sxs-lookup"><span data-stu-id="84b57-175">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="84b57-176">如果收到通知，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="84b57-176">If you receive the notification:</span></span>

   * <span data-ttu-id="84b57-177">确认是否已安装最新的[适用于 Visual Studio Code 的 C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="84b57-177">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="84b57-178">若要检查是否已安装此扩展，请在菜单栏中依次打开“视图” > “扩展”，或选择“活动”边栏中的“扩展”图标。</span><span class="sxs-lookup"><span data-stu-id="84b57-178">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="84b57-179">确认已启用 JavaScript 预览调试。</span><span class="sxs-lookup"><span data-stu-id="84b57-179">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="84b57-180">在菜单栏中打开设置（依次选择“文件” > “首选项” > “设置”）。</span><span class="sxs-lookup"><span data-stu-id="84b57-180">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="84b57-181">使用关键字 `debug preview` 进行搜索。</span><span class="sxs-lookup"><span data-stu-id="84b57-181">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="84b57-182">在搜索结果中，确认是否已选中“调试”>“JavaScript:使用预览”复选框。</span><span class="sxs-lookup"><span data-stu-id="84b57-182">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="84b57-183">如果启用预览调试的选项不存在，请升级到最新版本的 VS Code 或安装 [JavaScript 调试器扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)（VS Code 1.46 或更早版本）。</span><span class="sxs-lookup"><span data-stu-id="84b57-183">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="84b57-184">重载窗口。</span><span class="sxs-lookup"><span data-stu-id="84b57-184">Reload the window.</span></span>

1. <span data-ttu-id="84b57-185">使用 <kbd>F5</kbd> 键盘快捷方式或菜单项启动调试。</span><span class="sxs-lookup"><span data-stu-id="84b57-185">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="84b57-186">不支持“启动时不调试”(<kbd>Ctrl</kbd>+<kbd>F5</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="84b57-186">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="84b57-187">当应用以调试配置运行时，调试开销始终会导致性能的小幅下降。</span><span class="sxs-lookup"><span data-stu-id="84b57-187">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="84b57-188">出现提示时，选择“Blazor WebAssembly 调试”选项以启动调试。</span><span class="sxs-lookup"><span data-stu-id="84b57-188">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="84b57-189">此时会启动独立应用，并打开调试浏览器。</span><span class="sxs-lookup"><span data-stu-id="84b57-189">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="84b57-190">在 `*Client*` 应用中，在 `Pages/Counter.razor` 中的 `currentCount++;` 行上设置断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-190">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="84b57-191">在浏览器中，导航到 `Counter` 页，然后选择“单击此处”按钮以命中断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-191">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="84b57-192">在运行调试代理之前，在应用启动期间不会命中断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-192">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="84b57-193">这包括 `Program.Main` (`Program.cs`) 中的断点和组件的 [`OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods) 中的断点，其中这些组件由请求自应用的第一页加载。</span><span class="sxs-lookup"><span data-stu-id="84b57-193">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="84b57-194">调试托管 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="84b57-194">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="84b57-195">在 VS Code 中打开托管 Blazor WebAssembly 应用的“解决方案”文件夹。</span><span class="sxs-lookup"><span data-stu-id="84b57-195">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="84b57-196">如果没有为项目设置启动配置，将显示以下通知。</span><span class="sxs-lookup"><span data-stu-id="84b57-196">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="84b57-197">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="84b57-197">Select **Yes**.</span></span>

   > <span data-ttu-id="84b57-198">“{APPLICATION NAME}”中缺少进行生产和调试所需的资产。</span><span class="sxs-lookup"><span data-stu-id="84b57-198">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="84b57-199">警告的 VS Code</span><span class="sxs-lookup"><span data-stu-id="84b57-199">Add them?</span></span>

1. <span data-ttu-id="84b57-200">在窗口顶部的命令面板中，选择托管解决方案内的“服务器”项目。</span><span class="sxs-lookup"><span data-stu-id="84b57-200">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="84b57-201">这会生成一个 `launch.json` 文件，其中包含用于启动调试器的启动配置。</span><span class="sxs-lookup"><span data-stu-id="84b57-201">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="84b57-202">附加到现有的调试会话</span><span class="sxs-lookup"><span data-stu-id="84b57-202">Attach to an existing debugging session</span></span>

<span data-ttu-id="84b57-203">若要附加到正在运行的 Blazor 应用，请创建一个具有以下配置的 `launch.json` 文件：</span><span class="sxs-lookup"><span data-stu-id="84b57-203">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="84b57-204">只有独立应用才支持附加到调试会话。</span><span class="sxs-lookup"><span data-stu-id="84b57-204">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="84b57-205">若要使用完整堆栈调试，必须从 VS Code 启动应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-205">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="84b57-206">启动配置选项</span><span class="sxs-lookup"><span data-stu-id="84b57-206">Launch configuration options</span></span>

<span data-ttu-id="84b57-207">`blazorwasm` 调试类型 (`.vscode/launch.json`) 支持以下启动配置选项。</span><span class="sxs-lookup"><span data-stu-id="84b57-207">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="84b57-208">选项</span><span class="sxs-lookup"><span data-stu-id="84b57-208">Option</span></span>    | <span data-ttu-id="84b57-209">描述</span><span class="sxs-lookup"><span data-stu-id="84b57-209">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="84b57-210">使用 `launch` 启动调试会话并将其附加到 Blazor WebAssembly 应用，或使用 `attach` 将调试会话附加到已运行的应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-210">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="84b57-211">调试时要在浏览器中打开的 URL。</span><span class="sxs-lookup"><span data-stu-id="84b57-211">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="84b57-212">默认为 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="84b57-212">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="84b57-213">要为调试会话启动的浏览器。</span><span class="sxs-lookup"><span data-stu-id="84b57-213">The browser to launch for the debugging session.</span></span> <span data-ttu-id="84b57-214">设置为 `edge` 或 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="84b57-214">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="84b57-215">默认为 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="84b57-215">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="84b57-216">用于从 JS 调试程序生成日志。</span><span class="sxs-lookup"><span data-stu-id="84b57-216">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="84b57-217">设置为 `true` 即可生成日志。</span><span class="sxs-lookup"><span data-stu-id="84b57-217">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="84b57-218">如果要启动和调试托管的 Blazor WebAssembly 应用，则必须设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="84b57-218">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="84b57-219">指定 Web 服务器的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="84b57-219">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="84b57-220">如果应用是从子路由中提供的，则应设置它。</span><span class="sxs-lookup"><span data-stu-id="84b57-220">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="84b57-221">等待调试会话附加完成的毫秒数。</span><span class="sxs-lookup"><span data-stu-id="84b57-221">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="84b57-222">默认值为 30,000 毫秒（30 秒）。</span><span class="sxs-lookup"><span data-stu-id="84b57-222">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="84b57-223">为运行托管应用的服务器而对可执行文件进行的引用。</span><span class="sxs-lookup"><span data-stu-id="84b57-223">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="84b57-224">如果 `hosted` 是 `true`，则必须设置它。</span><span class="sxs-lookup"><span data-stu-id="84b57-224">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="84b57-225">要在其中启动应用的工作目录。</span><span class="sxs-lookup"><span data-stu-id="84b57-225">The working directory to launch the app under.</span></span> <span data-ttu-id="84b57-226">如果 `hosted` 是 `true`，则必须设置它。</span><span class="sxs-lookup"><span data-stu-id="84b57-226">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="84b57-227">要提供给已启动进程的环境变量。</span><span class="sxs-lookup"><span data-stu-id="84b57-227">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="84b57-228">仅当 `hosted` 设置为 `true` 时才适用。</span><span class="sxs-lookup"><span data-stu-id="84b57-228">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="84b57-229">启动配置示例</span><span class="sxs-lookup"><span data-stu-id="84b57-229">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="84b57-230">启动并调试独立 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="84b57-230">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="84b57-231">在指定的 URL 附加到正在运行的应用</span><span class="sxs-lookup"><span data-stu-id="84b57-231">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="84b57-232">使用 Microsoft Edge 启动并调试托管 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="84b57-232">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="84b57-233">浏览器默认配置为 Google Chrome。</span><span class="sxs-lookup"><span data-stu-id="84b57-233">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="84b57-234">使用 Microsoft Edge 进行调试时，将 `browser` 设置为 `edge`。</span><span class="sxs-lookup"><span data-stu-id="84b57-234">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="84b57-235">若要使用 Google Chrome，要么不要设置 `browser` 选项，要么将选项值设置为 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="84b57-235">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="84b57-236">在前面的示例中，`MyHostedApp.Server.dll` 是“服务器”应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="84b57-236">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="84b57-237">在“解决方案”文件夹中，“`.vscode`”文件夹位于“`Client`”、“`Server`”和“`Shared`”文件夹旁边。</span><span class="sxs-lookup"><span data-stu-id="84b57-237">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="84b57-238">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="84b57-238">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="84b57-239">要在 Visual Studio for Mac 中调试 Blazor WebAssembly 应用，请按以下步骤执行：</span><span class="sxs-lookup"><span data-stu-id="84b57-239">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="84b57-240">创建新的 ASP.NET Core 托管 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-240">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="84b57-241">按 <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> 在调试器中运行应用。</span><span class="sxs-lookup"><span data-stu-id="84b57-241">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="84b57-242">不支持“启动时不调试”(<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="84b57-242">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="84b57-243">当应用以调试配置运行时，调试开销始终会导致性能的小幅下降。</span><span class="sxs-lookup"><span data-stu-id="84b57-243">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="84b57-244">Google Chrome 或 Microsoft Edge 必须是调试会话的选定浏览器。</span><span class="sxs-lookup"><span data-stu-id="84b57-244">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="84b57-245">在 `*Client*` 应用中，在 `Pages/Counter.razor` 中的 `currentCount++;` 行上设置断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-245">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="84b57-246">在浏览器中，导航到 `Counter` 页，然后选择“单击此处”按钮以命中断点：</span><span class="sxs-lookup"><span data-stu-id="84b57-246">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="84b57-247">在 Visual Studio 中，检查“局部变量”窗口中 `currentCount` 字段的值。</span><span class="sxs-lookup"><span data-stu-id="84b57-247">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="84b57-248">按 <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> 继续执行。</span><span class="sxs-lookup"><span data-stu-id="84b57-248">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="84b57-249">调试 Blazor WebAssembly 应用时，还可以调试服务器代码：</span><span class="sxs-lookup"><span data-stu-id="84b57-249">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="84b57-250">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 的“`Pages/FetchData.razor`”页中设置断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-250">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="84b57-251">在 `Get` 操作方法的 `WeatherForecastController` 中设置一个断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-251">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="84b57-252">浏览到 `Fetch Data` 页，在 `FetchData` 组件中的首个断点向服务器发出 HTTP 请求前命中该断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-252">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="84b57-253">按 <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> 继续执行，然后在服务器上命中 `WeatherForecastController` 中的断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-253">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="84b57-254">再次按 <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> 以继续执行，并查看浏览器中呈现的天气预报表。</span><span class="sxs-lookup"><span data-stu-id="84b57-254">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="84b57-255">在运行调试代理之前，在应用启动期间不会命中断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-255">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="84b57-256">这包括 `Program.Main` (`Program.cs`) 中的断点和组件的 [`OnInitialized{Async}` 方法](xref:blazor/components/lifecycle#component-initialization-methods) 中的断点，其中这些组件由请求自应用的第一页加载。</span><span class="sxs-lookup"><span data-stu-id="84b57-256">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="84b57-257">有关详细信息，请参阅[使用 Visual Studio for Mac 进行调试](/visualstudio/mac/debugging)。</span><span class="sxs-lookup"><span data-stu-id="84b57-257">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="84b57-258">在浏览器中调试</span><span class="sxs-lookup"><span data-stu-id="84b57-258">Debug in the browser</span></span>

<span data-ttu-id="84b57-259">本部分中的指南适用于在 Windows 上运行的 Google Chrome 和 Microsoft Edge。</span><span class="sxs-lookup"><span data-stu-id="84b57-259">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="84b57-260">在开发环境中运行该应用的调试版本。</span><span class="sxs-lookup"><span data-stu-id="84b57-260">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="84b57-261">启动浏览器并导航到应用程序的 URL（例如 `https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="84b57-261">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="84b57-262">在浏览器中，尝试通过按 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> 启动远程调试。</span><span class="sxs-lookup"><span data-stu-id="84b57-262">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="84b57-263">浏览器必须在已启用远程调试的情况下运行，而这并不是默认设置。</span><span class="sxs-lookup"><span data-stu-id="84b57-263">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="84b57-264">如果远程调试处于禁用状态，将呈现“无法找到可调试的浏览器选项卡”错误页，并且其中包含关于在调试端口打开的情况下启动浏览器的说明。</span><span class="sxs-lookup"><span data-stu-id="84b57-264">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="84b57-265">按照适用于你的浏览器的说明操作，接下来将打开一个新的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="84b57-265">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="84b57-266">关闭上一个浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="84b57-266">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="84b57-267">在启用远程调试的情况下运行浏览器后，按上一步中的调试键盘快捷方式会打开新的调试程序选项卡。</span><span class="sxs-lookup"><span data-stu-id="84b57-267">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="84b57-268">片刻后，“源”选项卡显示 `file://` 节点中应用的 .NET 程序集的列表。</span><span class="sxs-lookup"><span data-stu-id="84b57-268">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="84b57-269">在组件代码（`.razor` 文件）和 C# 代码文件 (`.cs`) 中，当代码执行时将命中你设置的断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-269">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="84b57-270">命中断点后，正常单步执行代码 (<kbd>F10</kbd>) 或恢复代码执行 (<kbd>F8</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="84b57-270">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="84b57-271">Blazor 提供调试代理，该代理实现 [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)，并使用特定于 .NET 的信息扩展该协议。</span><span class="sxs-lookup"><span data-stu-id="84b57-271">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="84b57-272">按下调试键盘快捷方式后，Blazor 会将 Chrome 开发者工具指向代理。</span><span class="sxs-lookup"><span data-stu-id="84b57-272">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="84b57-273">代理连接到要调试的浏览器窗口（因此需要启用远程调试）。</span><span class="sxs-lookup"><span data-stu-id="84b57-273">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="84b57-274">浏览器源映射</span><span class="sxs-lookup"><span data-stu-id="84b57-274">Browser source maps</span></span>

<span data-ttu-id="84b57-275">浏览器源映射允许浏览器将编译后的文件映射回其原始源文件，并且通常用于客户端调试。</span><span class="sxs-lookup"><span data-stu-id="84b57-275">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="84b57-276">但是，Blazor 当前不直接将 C# 映射到 JavaScript/WASM。</span><span class="sxs-lookup"><span data-stu-id="84b57-276">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="84b57-277">相反，Blazor 在浏览器中进行 IL 解释，因此源映射不相关。</span><span class="sxs-lookup"><span data-stu-id="84b57-277">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="84b57-278">防火墙配置</span><span class="sxs-lookup"><span data-stu-id="84b57-278">Firewall configuration</span></span>

<span data-ttu-id="84b57-279">如果防火墙阻止与调试代理之间的通信，则创建允许浏览器与 `NodeJS` 进程之间进行通信的防火墙例外规则。</span><span class="sxs-lookup"><span data-stu-id="84b57-279">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="84b57-280">必须谨慎修改防火墙配置，以避免造成安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="84b57-280">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="84b57-281">仔细应用安全指南、遵循最佳安全实践，并遵守防火墙制造商发出的警告。</span><span class="sxs-lookup"><span data-stu-id="84b57-281">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="84b57-282">允许与 `NodeJS` 进程之间的开放式通信：</span><span class="sxs-lookup"><span data-stu-id="84b57-282">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="84b57-283">根据防火墙的功能和配置，打开节点服务器的任何连接。</span><span class="sxs-lookup"><span data-stu-id="84b57-283">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="84b57-284">可能会因网络而有风险。</span><span class="sxs-lookup"><span data-stu-id="84b57-284">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="84b57-285">仅建议在开发人员计算机上使用。</span><span class="sxs-lookup"><span data-stu-id="84b57-285">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="84b57-286">如果可能，只允许与受信任的网络或专用网络上的 `NodeJS` 进程之间的开放式通信。</span><span class="sxs-lookup"><span data-stu-id="84b57-286">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="84b57-287">有关 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security)配置指南，请参阅[创建入站程序或服务规则](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule)。</span><span class="sxs-lookup"><span data-stu-id="84b57-287">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="84b57-288">有关详细信息，请参阅 Windows 防火墙文档集中的[具有高级安全性的 Windows Defender 防火墙](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security)和相关文章。</span><span class="sxs-lookup"><span data-stu-id="84b57-288">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="84b57-289">疑难解答</span><span class="sxs-lookup"><span data-stu-id="84b57-289">Troubleshoot</span></span>

<span data-ttu-id="84b57-290">如果遇到错误，以下提示可能有所帮助：</span><span class="sxs-lookup"><span data-stu-id="84b57-290">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="84b57-291">在“调试程序”选项卡中，在浏览器中打开开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="84b57-291">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="84b57-292">在控制台中，执行 `localStorage.clear()` 以删除所有断点。</span><span class="sxs-lookup"><span data-stu-id="84b57-292">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="84b57-293">确认你已安装并信任 ASP.NET Core HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="84b57-293">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="84b57-294">有关详细信息，请参阅 <xref:security/enforcing-ssl#troubleshoot-certificate-problems>。</span><span class="sxs-lookup"><span data-stu-id="84b57-294">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="84b57-295">Visual Studio 要求在“工具” > “选项” > “调试” > “常规”中选择“对 ASP.NET 启用 JavaScript 调试(Chrome、Edge 和 IE)”选项。</span><span class="sxs-lookup"><span data-stu-id="84b57-295">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="84b57-296">这是 Visual Studio 的默认设置。</span><span class="sxs-lookup"><span data-stu-id="84b57-296">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="84b57-297">如果调试不起作用，请确认已选中该选项。</span><span class="sxs-lookup"><span data-stu-id="84b57-297">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="84b57-298">如果你的环境使用 HTTP 代理，请确保在代理绕过设置中包含 `localhost`。</span><span class="sxs-lookup"><span data-stu-id="84b57-298">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="84b57-299">这可以通过在以下二者之一中设置 `NO_PROXY` 环境变量来实现：</span><span class="sxs-lookup"><span data-stu-id="84b57-299">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="84b57-300">项目的 `launchSettings.json` 文件。</span><span class="sxs-lookup"><span data-stu-id="84b57-300">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="84b57-301">在将其应用于所有应用时所在的用户或系统环境变量级别。</span><span class="sxs-lookup"><span data-stu-id="84b57-301">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="84b57-302">使用环境变量时，请重新启动 Visual Studio 以使更改生效。</span><span class="sxs-lookup"><span data-stu-id="84b57-302">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="84b57-303">确保防火墙或代理不会阻止与调试代理（`NodeJS` 进程）之间的通信。</span><span class="sxs-lookup"><span data-stu-id="84b57-303">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="84b57-304">有关详细信息，请参阅[防火墙配置](#firewall-configuration)部分。</span><span class="sxs-lookup"><span data-stu-id="84b57-304">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="84b57-305">`OnInitialized{Async}` 中的未命中断点</span><span class="sxs-lookup"><span data-stu-id="84b57-305">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="84b57-306">Blazor 框架的调试代理需要一小段时间才能启动，因此 [`OnInitialized{Async}` 生命周期方法](xref:blazor/components/lifecycle#component-initialization-methods)中的断点可能不会命中。</span><span class="sxs-lookup"><span data-stu-id="84b57-306">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="84b57-307">建议在方法主体开头添加延迟，以便在命中断点之前，为调试代理指定一段时间来启动。</span><span class="sxs-lookup"><span data-stu-id="84b57-307">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="84b57-308">你可以根据 [`if` 编译器指令](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)包括延迟，以确保应用的发布版本不存在延迟。</span><span class="sxs-lookup"><span data-stu-id="84b57-308">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="84b57-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="84b57-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="84b57-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="84b57-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="84b57-311">Visual Studio (Windows) 超时</span><span class="sxs-lookup"><span data-stu-id="84b57-311">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="84b57-312">如果 Visual Studio 引发了调试适配器启动因已达到超时而失败的异常，可使用注册表设置调整超时：</span><span class="sxs-lookup"><span data-stu-id="84b57-312">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="84b57-313">前述命令中的 `{TIMEOUT}` 占位符以毫秒为单位。</span><span class="sxs-lookup"><span data-stu-id="84b57-313">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="84b57-314">例如， 1 分钟指定为 `60000`。</span><span class="sxs-lookup"><span data-stu-id="84b57-314">For example, one minute is assigned as `60000`.</span></span>
