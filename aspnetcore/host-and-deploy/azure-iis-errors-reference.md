---
title: Azure 应用服务和 IIS 上 ASP.NET Core 的常见错误参考
author: rick-anderson
description: 获取在 Azure 应用服务和 IIS 上托管 ASP.NET Core 应用的常见错误的故障排除建议。
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
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: b009cc61a94e618a48d96ecbd770ef6371308f6a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059840"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="98204-103">Azure 应用服务和 IIS 上 ASP.NET Core 的常见错误参考</span><span class="sxs-lookup"><span data-stu-id="98204-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="98204-104">本主题描述在 Azure 应用服务和 IIS 上托管 ASP.NET Core 应用时的常见错误，并提供特定错误的故障排除建议。</span><span class="sxs-lookup"><span data-stu-id="98204-104">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="98204-105">有关一般故障排除指南，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-105">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="98204-106">收集以下信息：</span><span class="sxs-lookup"><span data-stu-id="98204-106">Collect the following information:</span></span>

* <span data-ttu-id="98204-107">浏览器行为（状态代码和错误消息）</span><span class="sxs-lookup"><span data-stu-id="98204-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="98204-108">应用程序事件日志条目</span><span class="sxs-lookup"><span data-stu-id="98204-108">Application Event Log entries</span></span>
  * <span data-ttu-id="98204-109">Azure 应用服务：请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-109">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="98204-110">IIS</span><span class="sxs-lookup"><span data-stu-id="98204-110">IIS</span></span>
    1. <span data-ttu-id="98204-111">选择 Windows 菜单中的“开始”，键入“事件查看器”，然后按 Enter 。</span><span class="sxs-lookup"><span data-stu-id="98204-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="98204-112">打开事件查看器后，展开侧栏中的“Windows 日志”>“应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="98204-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="98204-113">ASP.NET Core 模块 stdout 和 debug日志条目</span><span class="sxs-lookup"><span data-stu-id="98204-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="98204-114">Azure 应用服务：请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-114">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="98204-115">IIS：按照 ASP.NET Core 模块主题的[日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)和[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)部分中的说明进行操作。</span><span class="sxs-lookup"><span data-stu-id="98204-115">IIS: Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="98204-116">将错误信息与以下常见错误进行比较。</span><span class="sxs-lookup"><span data-stu-id="98204-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="98204-117">如果找到匹配项，请按照故障排除建议进行操作。</span><span class="sxs-lookup"><span data-stu-id="98204-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="98204-118">本主题中的错误列表并未包含所有错误。</span><span class="sxs-lookup"><span data-stu-id="98204-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="98204-119">如果遇到此处未列出的错误，请使用本主题底部的“内容反馈”按钮打开一个新问题，并提供有关如何重现错误的详细说明。</span><span class="sxs-lookup"><span data-stu-id="98204-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="98204-120">OS 升级删除了 32 位 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="98204-120">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="98204-121">**应用程序日志：** 未能加载模块 DLL C:\WINDOWS\system32\inetsrv\aspnetcore.dll。</span><span class="sxs-lookup"><span data-stu-id="98204-121">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="98204-122">该数据是个错误。</span><span class="sxs-lookup"><span data-stu-id="98204-122">The data is the error.</span></span>

<span data-ttu-id="98204-123">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-123">Troubleshooting:</span></span>

<span data-ttu-id="98204-124">OS 升级期间不会保留 C:\Windows\SysWOW64\inetsrv 目录中的非 OS 文件。</span><span class="sxs-lookup"><span data-stu-id="98204-124">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="98204-125">如果在 OS 升级前已安装 ASP.NET Core 模块，且随后在 OS 升级后在 32 位模式下运行任何应用池，则会遇到此问题。</span><span class="sxs-lookup"><span data-stu-id="98204-125">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="98204-126">在 OS 升级后修复 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="98204-126">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="98204-127">请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="98204-127">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="98204-128">运行安装程序时，选择“修复”。</span><span class="sxs-lookup"><span data-stu-id="98204-128">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="98204-129">缺少站点扩展、安装了 32 位 (x86) 和 64 位 (x64) 站点扩展，或设置了错误的进程位数</span><span class="sxs-lookup"><span data-stu-id="98204-129">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="98204-130">适用于 Azure 应用服务托管的应用。</span><span class="sxs-lookup"><span data-stu-id="98204-130">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="98204-131">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="98204-131">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="98204-132">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="98204-132">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="98204-133">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="98204-133">Could not find inprocess request handler.</span></span> <span data-ttu-id="98204-134">调用 hostfxr 捕获的输出：无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="98204-134">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="98204-135">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="98204-135">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="98204-136">未能启动应用程序“/LM/W3SVC/1416782824/ROOT”，ErrorCode“0x8000ffff”。</span><span class="sxs-lookup"><span data-stu-id="98204-136">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="98204-137">**ASP.NET Core 模块 stdout 日志：** 无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="98204-137">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="98204-138">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="98204-138">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

* <span data-ttu-id="98204-139">**ASP.NET Core 模块调试日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="98204-139">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="98204-140">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="98204-140">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="98204-141">返回失败的 HRESULT：0x8000ffff。</span><span class="sxs-lookup"><span data-stu-id="98204-141">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="98204-142">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="98204-142">Could not find inprocess request handler.</span></span> <span data-ttu-id="98204-143">无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="98204-143">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="98204-144">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="98204-144">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="98204-145">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-145">Troubleshooting:</span></span>

* <span data-ttu-id="98204-146">如果在预览运行时运行该应用，请安装 32 位 (x86) 或  64位 (x64) 站点扩展，以匹配应用的位数和应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="98204-146">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="98204-147">请勿同时安装扩展或扩展的多个运行时版本。</span><span class="sxs-lookup"><span data-stu-id="98204-147">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="98204-148">ASP.NET Core {RUNTIME VERSION} (x86) 运行时</span><span class="sxs-lookup"><span data-stu-id="98204-148">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="98204-149">ASP.NET Core {RUNTIME VERSION} (x64) 运行时</span><span class="sxs-lookup"><span data-stu-id="98204-149">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="98204-150">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="98204-150">Restart the app.</span></span> <span data-ttu-id="98204-151">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="98204-151">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="98204-152">如果在预览运行时运行应用，且同时安装了 32 位 (x86) 和 64 位 (x64) [站点扩展](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)，请卸载与应用的位数不匹配的站点扩展。</span><span class="sxs-lookup"><span data-stu-id="98204-152">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="98204-153">删除站点扩展之后，重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="98204-153">After removing the site extension, restart the app.</span></span> <span data-ttu-id="98204-154">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="98204-154">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="98204-155">如果在预览运行时运行该应用，并且站点扩展的位数匹配应用的位数，请确认预览站点扩展的运行时版本匹配应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="98204-155">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="98204-156">确认“应用程序设置”中的应用“平台”与应用的位数匹配。</span><span class="sxs-lookup"><span data-stu-id="98204-156">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="98204-157">有关详细信息，请参阅 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>。</span><span class="sxs-lookup"><span data-stu-id="98204-157">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="98204-158">已部署 x86 应用，但 32 位应用未启用应用池</span><span class="sxs-lookup"><span data-stu-id="98204-158">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="98204-159">**浏览器：** HTTP 错误 500.30 - ANCM 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="98204-159">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="98204-160">**应用程序日志：** 物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 遇到意外的托管异常，异常代码= '0xe0434352'。</span><span class="sxs-lookup"><span data-stu-id="98204-160">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="98204-161">请检查 stderr 日志以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="98204-161">Please check the stderr logs for more information.</span></span> <span data-ttu-id="98204-162">物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 未能加载 clr 和托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="98204-162">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="98204-163">CLR 工作线程提前退出</span><span class="sxs-lookup"><span data-stu-id="98204-163">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="98204-164">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="98204-164">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

* <span data-ttu-id="98204-165">**ASP.NET Core 模块调试日志：** 返回失败的 HRESULT：0x8007023e</span><span class="sxs-lookup"><span data-stu-id="98204-165">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="98204-166">发布自包含应用时，SDK 会捕获此方案。</span><span class="sxs-lookup"><span data-stu-id="98204-166">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="98204-167">如果 RID 与平台目标（例如，`win10-x64` RID 与项目文件中的 `<PlatformTarget>x86</PlatformTarget>`）不匹配，则 SDK 会产生错误。</span><span class="sxs-lookup"><span data-stu-id="98204-167">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="98204-168">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-168">Troubleshooting:</span></span>

<span data-ttu-id="98204-169">对于依赖 x86 框架的部署 (`<PlatformTarget>x86</PlatformTarget>`)，请为 32 位应用启用 IIS 应用池。</span><span class="sxs-lookup"><span data-stu-id="98204-169">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="98204-170">在 IIS 管理器中，打开应用池的“高级设置”并将“启用 32 位应用程序”设置为 True  。</span><span class="sxs-lookup"><span data-stu-id="98204-170">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="98204-171">平台与 RID 冲突</span><span class="sxs-lookup"><span data-stu-id="98204-171">Platform conflicts with RID</span></span>

* <span data-ttu-id="98204-172">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="98204-172">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="98204-173">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 命令行启动进程，ErrorCode = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="98204-173">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="98204-174">**ASP.NET Core 模块 stdout 日志：** 未经处理的异常：System.BadImageFormatException：无法加载文件或程序集 '{ASSEMBLY}.dll'。</span><span class="sxs-lookup"><span data-stu-id="98204-174">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="98204-175">试图加载的程序的格式不正确。</span><span class="sxs-lookup"><span data-stu-id="98204-175">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="98204-176">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-176">Troubleshooting:</span></span>

* <span data-ttu-id="98204-177">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="98204-177">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="98204-178">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="98204-178">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="98204-179">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-179">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="98204-180">如果在升级应用和部署更新的程序集时，Azure 应用部署出现此异常，请从先前的部署中手动删除所有文件。</span><span class="sxs-lookup"><span data-stu-id="98204-180">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="98204-181">部署升级的应用时，延迟的不兼容程序集可能造成 `System.BadImageFormatException` 异常。</span><span class="sxs-lookup"><span data-stu-id="98204-181">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="98204-182">URI 终结点错误或网站已停止</span><span class="sxs-lookup"><span data-stu-id="98204-182">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="98204-183">**浏览器：** ERR_CONNECTION_REFUSED --或者-- 无法连接</span><span class="sxs-lookup"><span data-stu-id="98204-183">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="98204-184">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-184">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-185">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-185">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="98204-186">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-186">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-187">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-187">Troubleshooting:</span></span>

* <span data-ttu-id="98204-188">确认正在使用的应用的 URI 端点是否正确。</span><span class="sxs-lookup"><span data-stu-id="98204-188">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="98204-189">检查绑定。</span><span class="sxs-lookup"><span data-stu-id="98204-189">Check the bindings.</span></span>

* <span data-ttu-id="98204-190">确认 IIS 网站未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="98204-190">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="98204-191">已禁用 CoreWebEngine 或 W3SVC 服务器功能</span><span class="sxs-lookup"><span data-stu-id="98204-191">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="98204-192">**OS 异常：** 必须安装 IIS 7.0 CoreWebEngine 和 W3SVC 功能才能使用 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="98204-192">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="98204-193">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-193">Troubleshooting:</span></span>

<span data-ttu-id="98204-194">确认启用了适当的角色和功能。</span><span class="sxs-lookup"><span data-stu-id="98204-194">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="98204-195">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="98204-195">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="98204-196">网站物理路径不正确或缺少应用</span><span class="sxs-lookup"><span data-stu-id="98204-196">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="98204-197">**浏览器：** 403 禁止访问 - 访问被拒绝 --或者-- 403.14 禁止访问 - Web 服务器被配置为不列出此目录的内容。</span><span class="sxs-lookup"><span data-stu-id="98204-197">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="98204-198">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-198">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-199">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-199">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="98204-200">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-200">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-201">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-201">Troubleshooting:</span></span>

<span data-ttu-id="98204-202">检查 IIS 网站的“基本设置”和物理应用文件夹。</span><span class="sxs-lookup"><span data-stu-id="98204-202">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="98204-203">确认应用所在的文件夹位于 IIS 网站的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="98204-203">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="98204-204">角色不正确、未安装 ASP.NET Core 模块或权限不正确</span><span class="sxs-lookup"><span data-stu-id="98204-204">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="98204-205">**浏览器：** 500.19 内部服务器错误 - 无法访问请求的页面，因为该页面的相关配置数据无效。</span><span class="sxs-lookup"><span data-stu-id="98204-205">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="98204-206">--或者-- 无法显示此页面</span><span class="sxs-lookup"><span data-stu-id="98204-206">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="98204-207">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-207">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-208">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-208">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="98204-209">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-209">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-210">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-210">Troubleshooting:</span></span>

* <span data-ttu-id="98204-211">确认启用了适当的角色。</span><span class="sxs-lookup"><span data-stu-id="98204-211">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="98204-212">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="98204-212">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="98204-213">打开“程序和功能”或“应用和功能”，然后确认是否安装了 Windows Server Hosting  。</span><span class="sxs-lookup"><span data-stu-id="98204-213">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="98204-214">如果已安装的程序列表中没有 Windows Server Hosting，请下载并安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="98204-214">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="98204-215">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="98204-215">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="98204-216">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="98204-216">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="98204-217">请确保将“应用程序池”>“进程模型”>“Identity”设置为“ApplicationPoolIdentity”，或确保自定义标识具有访问应用部署文件夹的相应权限   。</span><span class="sxs-lookup"><span data-stu-id="98204-217">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="98204-218">如果卸载了 ASP.NET Core 托管捆绑包并已安装了一个早期版本的托管捆绑包，则 applicationHost.config 文件将不包含 ASP.NET Core 模块分区。</span><span class="sxs-lookup"><span data-stu-id="98204-218">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="98204-219">可打开 %windir%/System32/inetsrv/config 处的 applicationHost.config 文件并找到 `<configuration><configSections><sectionGroup name="system.webServer">` 分区组 。</span><span class="sxs-lookup"><span data-stu-id="98204-219">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="98204-220">如果分区组中缺少 ASP.NET Core 模块分区，请添加以下分区元素：</span><span class="sxs-lookup"><span data-stu-id="98204-220">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="98204-221">或者，安装最新版本的 ASP.NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="98204-221">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="98204-222">最新版本与受支持的 ASP.NET Core 应用向后兼容。</span><span class="sxs-lookup"><span data-stu-id="98204-222">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="98204-223">processPath 不正确、缺少 PATH 变量、未安装托管捆绑包、未重启系统/IIS、未安装 VC++ Redistributable 或 dotnet.exe 访问冲突</span><span class="sxs-lookup"><span data-stu-id="98204-223">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="98204-224">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="98204-224">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="98204-225">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"{...}" ' 命令行启动进程</span><span class="sxs-lookup"><span data-stu-id="98204-225">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="98204-226">，ErrorCode = '0x80070002 :0。</span><span class="sxs-lookup"><span data-stu-id="98204-226">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="98204-227">应用程序 '{PATH}' 无法启动。</span><span class="sxs-lookup"><span data-stu-id="98204-227">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="98204-228">'{PATH}' 中未找到可执行文件。</span><span class="sxs-lookup"><span data-stu-id="98204-228">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="98204-229">未能启动应用程序 '/LM/W3SVC/2/ROOT'，ErrorCode '0x8007023e'。</span><span class="sxs-lookup"><span data-stu-id="98204-229">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="98204-230">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-230">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="98204-231">**ASP.NET Core 模块调试日志：** 事件日志：应用程序 '{PATH}' 无法启动。</span><span class="sxs-lookup"><span data-stu-id="98204-231">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="98204-232">'{PATH}' 中未找到可执行文件。</span><span class="sxs-lookup"><span data-stu-id="98204-232">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="98204-233">返回失败的 HRESULT：0x8007023e</span><span class="sxs-lookup"><span data-stu-id="98204-233">Failed HRESULT returned: 0x8007023e</span></span>

<span data-ttu-id="98204-234">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-234">Troubleshooting:</span></span>

* <span data-ttu-id="98204-235">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="98204-235">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="98204-236">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="98204-236">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="98204-237">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="98204-238">检查 web.config 中 `<aspNetCore>` 元素的 processPath 属性，对于依赖框架的部署 (FDD)，确保它为 `dotnet`，对于[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)，确保它为 `.\{ASSEMBLY}.exe` 。</span><span class="sxs-lookup"><span data-stu-id="98204-238">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="98204-239">对于 FDD，可能无法通过路径设置访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="98204-239">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="98204-240">确认“系统路径”设置中存在“C:\Program Files\dotnet”\\。</span><span class="sxs-lookup"><span data-stu-id="98204-240">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="98204-241">对于 FDD，应用池的用户标识可能无法访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="98204-241">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="98204-242">确认应用池用户标识具有访问 C:\Program Files\dotnet 目录的权限。</span><span class="sxs-lookup"><span data-stu-id="98204-242">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="98204-243">确认没有为应用池用户标识配置针对 C:\Program Files\dotnet 和应用目录的拒绝规则。</span><span class="sxs-lookup"><span data-stu-id="98204-243">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="98204-244">可能已部署 FDD 并安装了 .NET Core，但未重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="98204-244">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="98204-245">重启服务器，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS 。</span><span class="sxs-lookup"><span data-stu-id="98204-245">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="98204-246">可能已部署 FDD，但未在托管系统上安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="98204-246">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="98204-247">如果尚未安装 .NET Core 运行时，则运行系统上的 **.NET Core 托管捆绑包安装程序**。</span><span class="sxs-lookup"><span data-stu-id="98204-247">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="98204-248">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="98204-248">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="98204-249">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="98204-249">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="98204-250">如果需要特定的运行时，请从 [.NET 下载存档](https://dotnet.microsoft.com/download/archives)下载运行时并将其安装在系统上。</span><span class="sxs-lookup"><span data-stu-id="98204-250">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="98204-251">重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS，完成安装 。</span><span class="sxs-lookup"><span data-stu-id="98204-251">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="98204-252">\<aspNetCore> 元素的参数不正确</span><span class="sxs-lookup"><span data-stu-id="98204-252">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="98204-253">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="98204-253">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="98204-254">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="98204-254">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="98204-255">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="98204-255">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="98204-256">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="98204-256">Could not find inprocess request handler.</span></span> <span data-ttu-id="98204-257">调用 hostfxr 捕获的输出：是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="98204-257">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="98204-258">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 未能启动应用程序 '/LM/W3SVC/3/ROOT'，ErrorCode '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="98204-258">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="98204-259">**ASP.NET Core 模块 stdout 日志：** 是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="98204-259">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="98204-260">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="98204-260">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="98204-261">**ASP.NET Core 模块调试日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="98204-261">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="98204-262">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="98204-262">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="98204-263">返回失败的 HRESULT：0x8000ffff 找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="98204-263">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="98204-264">调用 hostfxr 捕获的输出：是否希望运行 dotnet SDK 命令？</span><span class="sxs-lookup"><span data-stu-id="98204-264">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="98204-265">请从以下位置安装 dotnet SDK： https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 返回失败的 HRESULT：0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="98204-265">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="98204-266">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-266">Troubleshooting:</span></span>

* <span data-ttu-id="98204-267">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="98204-267">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="98204-268">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="98204-268">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="98204-269">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-269">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="98204-270">检查 web.config 中 `<aspNetCore>` 元素的 arguments 属性，确认该属性：(a) 对于依赖框架的部署 (FDD) 为 `.\{ASSEMBLY}.dll`；或 (b) 对于独立部署 (SCD)，则为不存在、为空字符串 (`arguments=""`)，或为应用参数列表 (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 。</span><span class="sxs-lookup"><span data-stu-id="98204-270">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="98204-271">缺少 .NET Core 共享框架</span><span class="sxs-lookup"><span data-stu-id="98204-271">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="98204-272">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="98204-272">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="98204-273">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="98204-273">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="98204-274">这很可能意味着应用配置错误，请检查 Microsoft.NetCore.App 和 Microsoft.AspNetCore.App 版本是否针对该应用程序并安装在计算机上。</span><span class="sxs-lookup"><span data-stu-id="98204-274">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="98204-275">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="98204-275">Could not find inprocess request handler.</span></span> <span data-ttu-id="98204-276">调用 hostfxr 捕获的输出：无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="98204-276">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="98204-277">找不到指定的框架 'Microsoft.AspNetCore.App'、版本 '{VERSION}'。</span><span class="sxs-lookup"><span data-stu-id="98204-277">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="98204-278">未能启动应用程序 '/LM/W3SVC/5/ROOT'，ErrorCode '0x8000ffff'。</span><span class="sxs-lookup"><span data-stu-id="98204-278">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="98204-279">**ASP.NET Core 模块 stdout 日志：** 无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="98204-279">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="98204-280">找不到指定的框架 'Microsoft.AspNetCore.App'、版本 '{VERSION}'。</span><span class="sxs-lookup"><span data-stu-id="98204-280">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="98204-281">**ASP.NET Core 模块调试日志：** 返回失败的 HRESULT：0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="98204-281">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

<span data-ttu-id="98204-282">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-282">Troubleshooting:</span></span>

<span data-ttu-id="98204-283">对于依赖框架的部署 (FDD)，确认已在系统上安装正确的运行时。</span><span class="sxs-lookup"><span data-stu-id="98204-283">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="98204-284">应用程序池已停止</span><span class="sxs-lookup"><span data-stu-id="98204-284">Stopped Application Pool</span></span>

* <span data-ttu-id="98204-285">**浏览器：** 503 服务不可用</span><span class="sxs-lookup"><span data-stu-id="98204-285">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="98204-286">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-286">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-287">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-287">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="98204-288">**ASP.NET Core 模块调试日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-288">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-289">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-289">Troubleshooting:</span></span>

<span data-ttu-id="98204-290">确认应用程序池未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="98204-290">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="98204-291">子应用程序包括 \<handlers> 部分</span><span class="sxs-lookup"><span data-stu-id="98204-291">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="98204-292">**浏览器：** HTTP 错误 500.19 - 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="98204-292">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="98204-293">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-293">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-294">**ASP.NET Core 模块 stdout 日志：** 创建根应用的日志文件并显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="98204-294">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="98204-295">未创建子应用的日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-295">The sub-app's log file isn't created.</span></span>

* <span data-ttu-id="98204-296">**ASP.NET Core 模块调试日志：** 创建根应用的日志文件并显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="98204-296">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="98204-297">未创建子应用的日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-297">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="98204-298">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-298">Troubleshooting:</span></span>

<span data-ttu-id="98204-299">确认子应用的 web.config 文件不包含 `<handlers>` 部分，或者子应用未继承父级应用的处理程序。</span><span class="sxs-lookup"><span data-stu-id="98204-299">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="98204-300">父级应用的 web.config 的`<system.webServer>` 放在 `<location>` 元素内。</span><span class="sxs-lookup"><span data-stu-id="98204-300">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="98204-301">将 <xref:System.Configuration.SectionInformation.InheritInChildApplications*> 属性设置为 `false`，表示 [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) 元素中指定的设置不会由驻留在父级应用子目录中的应用继承。</span><span class="sxs-lookup"><span data-stu-id="98204-301">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="98204-302">有关详细信息，请参阅 <xref:host-and-deploy/aspnet-core-module>。</span><span class="sxs-lookup"><span data-stu-id="98204-302">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="98204-303">stdout 日志路径不正确</span><span class="sxs-lookup"><span data-stu-id="98204-303">stdout log path incorrect</span></span>

* <span data-ttu-id="98204-304">**浏览器：** 应用正常响应。</span><span class="sxs-lookup"><span data-stu-id="98204-304">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="98204-305">**应用程序日志：** 无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="98204-305">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="98204-306">异常消息：在 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 返回了 HRESULT 0x80070005。</span><span class="sxs-lookup"><span data-stu-id="98204-306">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="98204-307">无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中停止 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="98204-307">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="98204-308">异常消息：在 {PATH} 返回了 HRESULT 0x80070002。</span><span class="sxs-lookup"><span data-stu-id="98204-308">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="98204-309">无法在 {PATH}\aspnetcorev2_inprocess.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="98204-309">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="98204-310">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-310">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="98204-311">**ASP.NET Core 模块调试日志：** 无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="98204-311">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="98204-312">异常消息：在 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 返回了 HRESULT 0x80070005。</span><span class="sxs-lookup"><span data-stu-id="98204-312">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="98204-313">无法在 C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll 中停止 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="98204-313">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="98204-314">异常消息：在 {PATH} 返回了 HRESULT 0x80070002。</span><span class="sxs-lookup"><span data-stu-id="98204-314">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="98204-315">无法在 {PATH}\aspnetcorev2_inprocess.dll 中启动 stdout 重定向。</span><span class="sxs-lookup"><span data-stu-id="98204-315">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

<span data-ttu-id="98204-316">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-316">Troubleshooting:</span></span>

* <span data-ttu-id="98204-317">web.config 的 `<aspNetCore>` 元素中指定的 `stdoutLogFile` 路径不存在。</span><span class="sxs-lookup"><span data-stu-id="98204-317">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="98204-318">有关详细信息，请参阅 [ASP.NET Core 模块：日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)。</span><span class="sxs-lookup"><span data-stu-id="98204-318">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="98204-319">应用池用户没有 stdout 日志路径的写入权限。</span><span class="sxs-lookup"><span data-stu-id="98204-319">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="98204-320">应用程序配置常见问题</span><span class="sxs-lookup"><span data-stu-id="98204-320">Application configuration general issue</span></span>

* <span data-ttu-id="98204-321">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败 --或者-- HTTP 错误 500.30 - ANCM 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="98204-321">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="98204-322">**应用程序日志：** 变量</span><span class="sxs-lookup"><span data-stu-id="98204-322">**Application Log:** Variable</span></span>

* <span data-ttu-id="98204-323">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空或使用常规条目创建，直到应用失败。</span><span class="sxs-lookup"><span data-stu-id="98204-323">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="98204-324">**ASP.NET Core 模块调试日志：** 变量</span><span class="sxs-lookup"><span data-stu-id="98204-324">**ASP.NET Core Module Debug Log:** Variable</span></span>

<span data-ttu-id="98204-325">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-325">Troubleshooting:</span></span>

<span data-ttu-id="98204-326">此进程无法启动，这很可能是由应用配置或编程问题引起的。</span><span class="sxs-lookup"><span data-stu-id="98204-326">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="98204-327">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="98204-327">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="98204-328">本主题描述在 Azure 应用服务和 IIS 上托管 ASP.NET Core 应用时的常见错误，并提供特定错误的故障排除建议。</span><span class="sxs-lookup"><span data-stu-id="98204-328">This topic describes common errors and provides troubleshooting advice for specific errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="98204-329">有关一般故障排除指南，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-329">For general troubleshooting guidance, see <xref:test/troubleshoot-azure-iis>.</span></span>

<span data-ttu-id="98204-330">收集以下信息：</span><span class="sxs-lookup"><span data-stu-id="98204-330">Collect the following information:</span></span>

* <span data-ttu-id="98204-331">浏览器行为（状态代码和错误消息）</span><span class="sxs-lookup"><span data-stu-id="98204-331">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="98204-332">应用程序事件日志条目</span><span class="sxs-lookup"><span data-stu-id="98204-332">Application Event Log entries</span></span>
  * <span data-ttu-id="98204-333">Azure 应用服务：请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-333">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="98204-334">IIS</span><span class="sxs-lookup"><span data-stu-id="98204-334">IIS</span></span>
    1. <span data-ttu-id="98204-335">选择 Windows 菜单中的“开始”，键入“事件查看器”，然后按 Enter 。</span><span class="sxs-lookup"><span data-stu-id="98204-335">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="98204-336">打开事件查看器后，展开侧栏中的“Windows 日志”>“应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="98204-336">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="98204-337">ASP.NET Core 模块 stdout 和 debug日志条目</span><span class="sxs-lookup"><span data-stu-id="98204-337">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="98204-338">Azure 应用服务：请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-338">Azure App Service: See <xref:test/troubleshoot-azure-iis>.</span></span>
  * <span data-ttu-id="98204-339">IIS：按照 ASP.NET Core 模块主题的[日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)和[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)部分中的说明进行操作。</span><span class="sxs-lookup"><span data-stu-id="98204-339">IIS: Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="98204-340">将错误信息与以下常见错误进行比较。</span><span class="sxs-lookup"><span data-stu-id="98204-340">Compare error information to the following common errors.</span></span> <span data-ttu-id="98204-341">如果找到匹配项，请按照故障排除建议进行操作。</span><span class="sxs-lookup"><span data-stu-id="98204-341">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="98204-342">本主题中的错误列表并未包含所有错误。</span><span class="sxs-lookup"><span data-stu-id="98204-342">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="98204-343">如果遇到此处未列出的错误，请使用本主题底部的“内容反馈”按钮打开一个新问题，并提供有关如何重现错误的详细说明。</span><span class="sxs-lookup"><span data-stu-id="98204-343">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="98204-344">OS 升级删除了 32 位 ASP.NET Core 模块</span><span class="sxs-lookup"><span data-stu-id="98204-344">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="98204-345">**应用程序日志：** 未能加载模块 DLL C:\WINDOWS\system32\inetsrv\aspnetcore.dll。</span><span class="sxs-lookup"><span data-stu-id="98204-345">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="98204-346">该数据是个错误。</span><span class="sxs-lookup"><span data-stu-id="98204-346">The data is the error.</span></span>

<span data-ttu-id="98204-347">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-347">Troubleshooting:</span></span>

<span data-ttu-id="98204-348">OS 升级期间不会保留 C:\Windows\SysWOW64\inetsrv 目录中的非 OS 文件。</span><span class="sxs-lookup"><span data-stu-id="98204-348">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="98204-349">如果在 OS 升级前已安装 ASP.NET Core 模块，且随后在 OS 升级后在 32 位模式下运行任何应用池，则会遇到此问题。</span><span class="sxs-lookup"><span data-stu-id="98204-349">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="98204-350">在 OS 升级后修复 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="98204-350">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="98204-351">请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="98204-351">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="98204-352">运行安装程序时，选择“修复”。</span><span class="sxs-lookup"><span data-stu-id="98204-352">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="98204-353">缺少站点扩展、安装了 32 位 (x86) 和 64 位 (x64) 站点扩展，或设置了错误的进程位数</span><span class="sxs-lookup"><span data-stu-id="98204-353">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="98204-354">适用于 Azure 应用服务托管的应用。</span><span class="sxs-lookup"><span data-stu-id="98204-354">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="98204-355">**浏览器：** HTTP 错误 500.0 - ANCM 进程内处理程序加载失败</span><span class="sxs-lookup"><span data-stu-id="98204-355">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="98204-356">**应用程序日志：** 调用 hostfxr 以查找进程内请求处理程序失败，未找到任何本机依赖项。</span><span class="sxs-lookup"><span data-stu-id="98204-356">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="98204-357">找不到进程内请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="98204-357">Could not find inprocess request handler.</span></span> <span data-ttu-id="98204-358">调用 hostfxr 捕获的输出：无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="98204-358">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="98204-359">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="98204-359">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="98204-360">未能启动应用程序“/LM/W3SVC/1416782824/ROOT”，ErrorCode“0x8000ffff”。</span><span class="sxs-lookup"><span data-stu-id="98204-360">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="98204-361">**ASP.NET Core 模块 stdout 日志：** 无法找到任何兼容的框架版本。</span><span class="sxs-lookup"><span data-stu-id="98204-361">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="98204-362">找不到指定的框架“Microsoft.AspNetCore.App”、版本“{VERSION}-preview-\*”。</span><span class="sxs-lookup"><span data-stu-id="98204-362">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

<span data-ttu-id="98204-363">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-363">Troubleshooting:</span></span>

* <span data-ttu-id="98204-364">如果在预览运行时运行该应用，请安装 32 位 (x86) 或  64位 (x64) 站点扩展，以匹配应用的位数和应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="98204-364">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="98204-365">请勿同时安装扩展或扩展的多个运行时版本。</span><span class="sxs-lookup"><span data-stu-id="98204-365">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="98204-366">ASP.NET Core {RUNTIME VERSION} (x86) 运行时</span><span class="sxs-lookup"><span data-stu-id="98204-366">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="98204-367">ASP.NET Core {RUNTIME VERSION} (x64) 运行时</span><span class="sxs-lookup"><span data-stu-id="98204-367">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="98204-368">重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="98204-368">Restart the app.</span></span> <span data-ttu-id="98204-369">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="98204-369">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="98204-370">如果在预览运行时运行应用，且同时安装了 32 位 (x86) 和 64 位 (x64) [站点扩展](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension)，请卸载与应用的位数不匹配的站点扩展。</span><span class="sxs-lookup"><span data-stu-id="98204-370">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="98204-371">删除站点扩展之后，重新启动应用。</span><span class="sxs-lookup"><span data-stu-id="98204-371">After removing the site extension, restart the app.</span></span> <span data-ttu-id="98204-372">等待几秒钟，以便应用重新启动。</span><span class="sxs-lookup"><span data-stu-id="98204-372">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="98204-373">如果在预览运行时运行该应用，并且站点扩展的位数匹配应用的位数，请确认预览站点扩展的运行时版本匹配应用的运行时版本。</span><span class="sxs-lookup"><span data-stu-id="98204-373">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="98204-374">确认“应用程序设置”中的应用“平台”与应用的位数匹配。</span><span class="sxs-lookup"><span data-stu-id="98204-374">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="98204-375">有关详细信息，请参阅 <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>。</span><span class="sxs-lookup"><span data-stu-id="98204-375">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="98204-376">已部署 x86 应用，但 32 位应用未启用应用池</span><span class="sxs-lookup"><span data-stu-id="98204-376">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="98204-377">**浏览器：** HTTP 错误 500.30 - ANCM 进程内启动失败</span><span class="sxs-lookup"><span data-stu-id="98204-377">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="98204-378">**应用程序日志：** 物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 遇到意外的托管异常，异常代码= '0xe0434352'。</span><span class="sxs-lookup"><span data-stu-id="98204-378">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="98204-379">请检查 stderr 日志以获取详细信息。</span><span class="sxs-lookup"><span data-stu-id="98204-379">Please check the stderr logs for more information.</span></span> <span data-ttu-id="98204-380">物理根路径为 '{PATH}' 的应用程序 '/LM/W3SVC/5/ROOT' 未能加载 clr 和托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="98204-380">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="98204-381">CLR 工作线程提前退出</span><span class="sxs-lookup"><span data-stu-id="98204-381">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="98204-382">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="98204-382">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="98204-383">发布自包含应用时，SDK 会捕获此方案。</span><span class="sxs-lookup"><span data-stu-id="98204-383">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="98204-384">如果 RID 与平台目标（例如，`win10-x64` RID 与项目文件中的 `<PlatformTarget>x86</PlatformTarget>`）不匹配，则 SDK 会产生错误。</span><span class="sxs-lookup"><span data-stu-id="98204-384">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="98204-385">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-385">Troubleshooting:</span></span>

<span data-ttu-id="98204-386">对于依赖 x86 框架的部署 (`<PlatformTarget>x86</PlatformTarget>`)，请为 32 位应用启用 IIS 应用池。</span><span class="sxs-lookup"><span data-stu-id="98204-386">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="98204-387">在 IIS 管理器中，打开应用池的“高级设置”并将“启用 32 位应用程序”设置为 True  。</span><span class="sxs-lookup"><span data-stu-id="98204-387">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="98204-388">平台与 RID 冲突</span><span class="sxs-lookup"><span data-stu-id="98204-388">Platform conflicts with RID</span></span>

* <span data-ttu-id="98204-389">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="98204-389">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="98204-390">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ' 命令行启动进程，ErrorCode = '0x80004005 : ff。</span><span class="sxs-lookup"><span data-stu-id="98204-390">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="98204-391">**ASP.NET Core 模块 stdout 日志：** 未经处理的异常：System.BadImageFormatException：无法加载文件或程序集 '{ASSEMBLY}.dll'。</span><span class="sxs-lookup"><span data-stu-id="98204-391">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="98204-392">试图加载的程序的格式不正确。</span><span class="sxs-lookup"><span data-stu-id="98204-392">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="98204-393">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-393">Troubleshooting:</span></span>

* <span data-ttu-id="98204-394">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="98204-394">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="98204-395">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="98204-395">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="98204-396">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-396">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="98204-397">如果在升级应用和部署更新的程序集时，Azure 应用部署出现此异常，请从先前的部署中手动删除所有文件。</span><span class="sxs-lookup"><span data-stu-id="98204-397">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="98204-398">部署升级的应用时，延迟的不兼容程序集可能造成 `System.BadImageFormatException` 异常。</span><span class="sxs-lookup"><span data-stu-id="98204-398">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="98204-399">URI 终结点错误或网站已停止</span><span class="sxs-lookup"><span data-stu-id="98204-399">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="98204-400">**浏览器：** ERR_CONNECTION_REFUSED --或者-- 无法连接</span><span class="sxs-lookup"><span data-stu-id="98204-400">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="98204-401">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-401">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-402">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-402">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-403">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-403">Troubleshooting:</span></span>

* <span data-ttu-id="98204-404">确认正在使用的应用的 URI 端点是否正确。</span><span class="sxs-lookup"><span data-stu-id="98204-404">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="98204-405">检查绑定。</span><span class="sxs-lookup"><span data-stu-id="98204-405">Check the bindings.</span></span>

* <span data-ttu-id="98204-406">确认 IIS 网站未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="98204-406">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="98204-407">已禁用 CoreWebEngine 或 W3SVC 服务器功能</span><span class="sxs-lookup"><span data-stu-id="98204-407">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="98204-408">**OS 异常：** 必须安装 IIS 7.0 CoreWebEngine 和 W3SVC 功能才能使用 ASP.NET Core 模块。</span><span class="sxs-lookup"><span data-stu-id="98204-408">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="98204-409">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-409">Troubleshooting:</span></span>

<span data-ttu-id="98204-410">确认启用了适当的角色和功能。</span><span class="sxs-lookup"><span data-stu-id="98204-410">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="98204-411">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="98204-411">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="98204-412">网站物理路径不正确或缺少应用</span><span class="sxs-lookup"><span data-stu-id="98204-412">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="98204-413">**浏览器：** 403 禁止访问 - 访问被拒绝 --或者-- 403.14 禁止访问 - Web 服务器被配置为不列出此目录的内容。</span><span class="sxs-lookup"><span data-stu-id="98204-413">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="98204-414">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-414">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-415">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-415">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-416">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-416">Troubleshooting:</span></span>

<span data-ttu-id="98204-417">检查 IIS 网站的“基本设置”和物理应用文件夹。</span><span class="sxs-lookup"><span data-stu-id="98204-417">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="98204-418">确认应用所在的文件夹位于 IIS 网站的物理路径中。</span><span class="sxs-lookup"><span data-stu-id="98204-418">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="98204-419">角色不正确、未安装 ASP.NET Core 模块或权限不正确</span><span class="sxs-lookup"><span data-stu-id="98204-419">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="98204-420">**浏览器：** 500.19 内部服务器错误 - 无法访问请求的页面，因为该页面的相关配置数据无效。</span><span class="sxs-lookup"><span data-stu-id="98204-420">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="98204-421">--或者-- 无法显示此页面</span><span class="sxs-lookup"><span data-stu-id="98204-421">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="98204-422">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-422">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-423">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-423">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-424">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-424">Troubleshooting:</span></span>

* <span data-ttu-id="98204-425">确认启用了适当的角色。</span><span class="sxs-lookup"><span data-stu-id="98204-425">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="98204-426">请参阅 [IIS 配置](xref:host-and-deploy/iis/index#iis-configuration)。</span><span class="sxs-lookup"><span data-stu-id="98204-426">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="98204-427">打开“程序和功能”或“应用和功能”，然后确认是否安装了 Windows Server Hosting  。</span><span class="sxs-lookup"><span data-stu-id="98204-427">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="98204-428">如果已安装的程序列表中没有 Windows Server Hosting，请下载并安装 .NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="98204-428">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="98204-429">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="98204-429">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="98204-430">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="98204-430">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="98204-431">请确保将“应用程序池”>“进程模型”>“Identity”设置为“ApplicationPoolIdentity”，或确保自定义标识具有访问应用部署文件夹的相应权限   。</span><span class="sxs-lookup"><span data-stu-id="98204-431">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="98204-432">如果卸载了 ASP.NET Core 托管捆绑包并已安装了一个早期版本的托管捆绑包，则 applicationHost.config 文件将不包含 ASP.NET Core 模块分区。</span><span class="sxs-lookup"><span data-stu-id="98204-432">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="98204-433">可打开 %windir%/System32/inetsrv/config 处的 applicationHost.config 文件并找到 `<configuration><configSections><sectionGroup name="system.webServer">` 分区组 。</span><span class="sxs-lookup"><span data-stu-id="98204-433">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="98204-434">如果分区组中缺少 ASP.NET Core 模块分区，请添加以下分区元素：</span><span class="sxs-lookup"><span data-stu-id="98204-434">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="98204-435">或者，安装最新版本的 ASP.NET Core 托管捆绑包。</span><span class="sxs-lookup"><span data-stu-id="98204-435">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="98204-436">最新版本与受支持的 ASP.NET Core 应用向后兼容。</span><span class="sxs-lookup"><span data-stu-id="98204-436">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="98204-437">processPath 不正确、缺少 PATH 变量、未安装托管捆绑包、未重启系统/IIS、未安装 VC++ Redistributable 或 dotnet.exe 访问冲突</span><span class="sxs-lookup"><span data-stu-id="98204-437">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

* <span data-ttu-id="98204-438">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="98204-438">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="98204-439">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"{...}" ' 命令行启动进程</span><span class="sxs-lookup"><span data-stu-id="98204-439">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="98204-440">，ErrorCode = '0x80070002 :0.</span><span class="sxs-lookup"><span data-stu-id="98204-440">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="98204-441">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="98204-441">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="98204-442">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-442">Troubleshooting:</span></span>

* <span data-ttu-id="98204-443">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="98204-443">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="98204-444">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="98204-444">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="98204-445">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-445">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="98204-446">检查 web.config 中 `<aspNetCore>` 元素的 processPath 属性，对于依赖框架的部署 (FDD)，确保它为 `dotnet`，对于[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)，确保它为 `.\{ASSEMBLY}.exe` 。</span><span class="sxs-lookup"><span data-stu-id="98204-446">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="98204-447">对于 FDD，可能无法通过路径设置访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="98204-447">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="98204-448">确认“系统路径”设置中存在“C:\Program Files\dotnet”\\。</span><span class="sxs-lookup"><span data-stu-id="98204-448">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="98204-449">对于 FDD，应用池的用户标识可能无法访问 dotnet.exe。</span><span class="sxs-lookup"><span data-stu-id="98204-449">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="98204-450">确认应用池用户标识具有访问 C:\Program Files\dotnet 目录的权限。</span><span class="sxs-lookup"><span data-stu-id="98204-450">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="98204-451">确认没有为应用池用户标识配置针对 C:\Program Files\dotnet 和应用目录的拒绝规则。</span><span class="sxs-lookup"><span data-stu-id="98204-451">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="98204-452">可能已部署 FDD 并安装了 .NET Core，但未重启 IIS。</span><span class="sxs-lookup"><span data-stu-id="98204-452">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="98204-453">重启服务器，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS 。</span><span class="sxs-lookup"><span data-stu-id="98204-453">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="98204-454">可能已部署 FDD，但未在托管系统上安装 .NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="98204-454">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="98204-455">如果尚未安装 .NET Core 运行时，则运行系统上的 **.NET Core 托管捆绑包安装程序**。</span><span class="sxs-lookup"><span data-stu-id="98204-455">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="98204-456">当前 .NET Core 托管捆绑包安装程序（直接下载）</span><span class="sxs-lookup"><span data-stu-id="98204-456">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="98204-457">有关详细信息，请参阅[安装 .NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)。</span><span class="sxs-lookup"><span data-stu-id="98204-457">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="98204-458">如果需要特定的运行时，请从 [.NET 下载存档](https://dotnet.microsoft.com/download/archives)下载运行时并将其安装在系统上。</span><span class="sxs-lookup"><span data-stu-id="98204-458">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="98204-459">重启系统，或通过从命令提示符依次执行 net stop was /y 和 net start w3svc 来重启 IIS，完成安装 。</span><span class="sxs-lookup"><span data-stu-id="98204-459">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="98204-460">\<aspNetCore> 元素的参数不正确</span><span class="sxs-lookup"><span data-stu-id="98204-460">Incorrect arguments of \<aspNetCore> element</span></span>

* <span data-ttu-id="98204-461">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="98204-461">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="98204-462">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 未能通过 '"dotnet" .\{ASSEMBLY}.dll' 命令行启动进程，ErrorCode = '0x80004005 :80008081。</span><span class="sxs-lookup"><span data-stu-id="98204-462">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="98204-463">**ASP.NET Core 模块 stdout 日志：** 要执行的应用程序不存在：'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="98204-463">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

<span data-ttu-id="98204-464">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-464">Troubleshooting:</span></span>

* <span data-ttu-id="98204-465">确认应用在 Kestrel 上本地运行。</span><span class="sxs-lookup"><span data-stu-id="98204-465">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="98204-466">进程失败可能是由应用的内部问题导致的。</span><span class="sxs-lookup"><span data-stu-id="98204-466">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="98204-467">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="98204-467">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

* <span data-ttu-id="98204-468">检查 web.config 中 `<aspNetCore>` 元素的 arguments 属性，确认该属性：(a) 对于依赖框架的部署 (FDD) 为 `.\{ASSEMBLY}.dll`；或 (b) 对于独立部署 (SCD)，则为不存在、为空字符串 (`arguments=""`)，或为应用参数列表 (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) 。</span><span class="sxs-lookup"><span data-stu-id="98204-468">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

<span data-ttu-id="98204-469">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-469">Troubleshooting:</span></span>

<span data-ttu-id="98204-470">对于依赖框架的部署 (FDD)，确认已在系统上安装正确的运行时。</span><span class="sxs-lookup"><span data-stu-id="98204-470">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="98204-471">应用程序池已停止</span><span class="sxs-lookup"><span data-stu-id="98204-471">Stopped Application Pool</span></span>

* <span data-ttu-id="98204-472">**浏览器：** 503 服务不可用</span><span class="sxs-lookup"><span data-stu-id="98204-472">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="98204-473">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-473">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-474">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-474">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-475">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-475">Troubleshooting:</span></span>

<span data-ttu-id="98204-476">确认应用程序池未处于“已停止”状态。</span><span class="sxs-lookup"><span data-stu-id="98204-476">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="98204-477">子应用程序包括 \<handlers> 部分</span><span class="sxs-lookup"><span data-stu-id="98204-477">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="98204-478">**浏览器：** HTTP 错误 500.19 - 内部服务器错误</span><span class="sxs-lookup"><span data-stu-id="98204-478">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="98204-479">**应用程序日志：** 没有条目</span><span class="sxs-lookup"><span data-stu-id="98204-479">**Application Log:** No entry</span></span>

* <span data-ttu-id="98204-480">**ASP.NET Core 模块 stdout 日志：** 创建根应用的日志文件并显示正常操作。</span><span class="sxs-lookup"><span data-stu-id="98204-480">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="98204-481">未创建子应用的日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-481">The sub-app's log file isn't created.</span></span>

<span data-ttu-id="98204-482">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-482">Troubleshooting:</span></span>

<span data-ttu-id="98204-483">确认子应用的 web.config 文件不包括 `<handlers>` 部分。</span><span class="sxs-lookup"><span data-stu-id="98204-483">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="98204-484">stdout 日志路径不正确</span><span class="sxs-lookup"><span data-stu-id="98204-484">stdout log path incorrect</span></span>

* <span data-ttu-id="98204-485">**浏览器：** 应用正常响应。</span><span class="sxs-lookup"><span data-stu-id="98204-485">**Browser:** The app responds normally.</span></span>

* <span data-ttu-id="98204-486">**应用程序日志：** 警告：无法创建 stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log，ErrorCode = -2147024893。</span><span class="sxs-lookup"><span data-stu-id="98204-486">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="98204-487">**ASP.NET Core 模块 stdout 日志：** 未创建日志文件。</span><span class="sxs-lookup"><span data-stu-id="98204-487">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

<span data-ttu-id="98204-488">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-488">Troubleshooting:</span></span>

* <span data-ttu-id="98204-489">web.config 的 `<aspNetCore>` 元素中指定的 `stdoutLogFile` 路径不存在。</span><span class="sxs-lookup"><span data-stu-id="98204-489">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="98204-490">有关详细信息，请参阅 [ASP.NET Core 模块：日志创建和重定向](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)。</span><span class="sxs-lookup"><span data-stu-id="98204-490">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="98204-491">应用池用户没有 stdout 日志路径的写入权限。</span><span class="sxs-lookup"><span data-stu-id="98204-491">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="98204-492">应用程序配置常见问题</span><span class="sxs-lookup"><span data-stu-id="98204-492">Application configuration general issue</span></span>

* <span data-ttu-id="98204-493">**浏览器：** HTTP 错误 502.5 - 进程失败</span><span class="sxs-lookup"><span data-stu-id="98204-493">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="98204-494">**应用程序日志：** 物理根路径为 'C:\{PATH}\' 的应用程序 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' 通过 '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' 命令行创建了进程，但该进程出现故障、未响应或未在给定的端口 '{PORT}' 上侦听，ErrorCode = '{ERROR CODE}'</span><span class="sxs-lookup"><span data-stu-id="98204-494">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="98204-495">**ASP.NET Core 模块 stdout 日志：** 日志文件已创建，但为空。</span><span class="sxs-lookup"><span data-stu-id="98204-495">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

<span data-ttu-id="98204-496">疑难解答：</span><span class="sxs-lookup"><span data-stu-id="98204-496">Troubleshooting:</span></span>

<span data-ttu-id="98204-497">此进程无法启动，这很可能是由应用配置或编程问题引起的。</span><span class="sxs-lookup"><span data-stu-id="98204-497">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="98204-498">有关详细信息，请参阅下列主题：</span><span class="sxs-lookup"><span data-stu-id="98204-498">For more information, see the following topics:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
