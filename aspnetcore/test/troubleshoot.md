---
title: ASP.NET Core 项目故障排除和调试
author: Rick-Anderson
description: 理解 ASP.NET Core 项目的警告和错误，并对其进行故障排除。
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
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
uid: test/troubleshoot
ms.openlocfilehash: 8e6c640cd775e5d4cbe6e34c1cecc391baf57344
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059567"
---
# <a name="troubleshoot-and-debug-aspnet-core-projects"></a><span data-ttu-id="adb54-103">ASP.NET Core 项目故障排除和调试</span><span class="sxs-lookup"><span data-stu-id="adb54-103">Troubleshoot and debug ASP.NET Core projects</span></span>

<span data-ttu-id="adb54-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="adb54-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="adb54-105">以下链接提供了故障排除指南：</span><span class="sxs-lookup"><span data-stu-id="adb54-105">The following links provide troubleshooting guidance:</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="adb54-106">NDC 会议（伦敦，2018 年）：诊断 ASP.NET Core 应用程序中的问题</span><span class="sxs-lookup"><span data-stu-id="adb54-106">NDC Conference (London, 2018): Diagnosing issues in ASP.NET Core Applications</span></span>](https://www.youtube.com/watch?v=RYI0DHoIVaA)
* [<span data-ttu-id="adb54-107">ASP.NET 博客：解决 ASP.NET Core 性能问题</span><span class="sxs-lookup"><span data-stu-id="adb54-107">ASP.NET Blog: Troubleshooting ASP.NET Core Performance Problems</span></span>](https://blogs.msdn.microsoft.com/webdev/2018/05/23/asp-net-core-performance-improvements/)

## <a name="net-core-sdk-warnings"></a><span data-ttu-id="adb54-108">.NET Core SDK 警告</span><span class="sxs-lookup"><span data-stu-id="adb54-108">.NET Core SDK warnings</span></span>

### <a name="both-the-32-bit-and-64-bit-versions-of-the-net-core-sdk-are-installed"></a><span data-ttu-id="adb54-109">同时安装了 .NET Core SDK 的 32 位和 64 位版本</span><span class="sxs-lookup"><span data-stu-id="adb54-109">Both the 32-bit and 64-bit versions of the .NET Core SDK are installed</span></span>

<span data-ttu-id="adb54-110">在 ASP.NET Core 的“新建项目”对话框中，可能会显示以下警告：</span><span class="sxs-lookup"><span data-stu-id="adb54-110">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="adb54-111">同时安装了 .NET Core SDK 的 32 位和 64 位版本。</span><span class="sxs-lookup"><span data-stu-id="adb54-111">Both 32-bit and 64-bit versions of the .NET Core SDK are installed.</span></span> <span data-ttu-id="adb54-112">仅显示安装在 C:\\Program Files\\dotnet\\sdk\\ 的 64 位版本的模板。</span><span class="sxs-lookup"><span data-stu-id="adb54-112">Only templates from the 64-bit versions installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="adb54-113">如果同时安装了 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) 的 32 位 (x86) 和 64 位 (x64) 版本，则会出现此警告。</span><span class="sxs-lookup"><span data-stu-id="adb54-113">This warning appears when both 32-bit (x86) and 64-bit (x64) versions of the [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) are installed.</span></span> <span data-ttu-id="adb54-114">可能同时安装两个版本的常见原因包括：</span><span class="sxs-lookup"><span data-stu-id="adb54-114">Common reasons both versions may be installed include:</span></span>

* <span data-ttu-id="adb54-115">最初使用 32 位计算机下载了 .NET Core SDK 安装程序，然后将其复制并安装到 64 位计算机上。</span><span class="sxs-lookup"><span data-stu-id="adb54-115">You originally downloaded the .NET Core SDK installer using a 32-bit machine but then copied it across and installed it on a 64-bit machine.</span></span>
* <span data-ttu-id="adb54-116">32 位 .NET Core SDK 是由另一个应用程序安装的。</span><span class="sxs-lookup"><span data-stu-id="adb54-116">The 32-bit .NET Core SDK was installed by another application.</span></span>
* <span data-ttu-id="adb54-117">下载并安装了错误的版本。</span><span class="sxs-lookup"><span data-stu-id="adb54-117">The wrong version was downloaded and installed.</span></span>

<span data-ttu-id="adb54-118">卸载 32 位 .NET Core SDK 以避免出现此警告。</span><span class="sxs-lookup"><span data-stu-id="adb54-118">Uninstall the 32-bit .NET Core SDK to prevent this warning.</span></span> <span data-ttu-id="adb54-119">从“控制面板” > “程序和功能” > “卸载或更改程序”卸载  。</span><span class="sxs-lookup"><span data-stu-id="adb54-119">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="adb54-120">如果了解警告出现的原因及其含义，可以忽略此警告。</span><span class="sxs-lookup"><span data-stu-id="adb54-120">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="the-net-core-sdk-is-installed-in-multiple-locations"></a><span data-ttu-id="adb54-121">.NET Core SDK 安装在多个位置</span><span class="sxs-lookup"><span data-stu-id="adb54-121">The .NET Core SDK is installed in multiple locations</span></span>

<span data-ttu-id="adb54-122">在 ASP.NET Core 的“新建项目”对话框中，可能会显示以下警告：</span><span class="sxs-lookup"><span data-stu-id="adb54-122">In the **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

> <span data-ttu-id="adb54-123">.NET Core SDK 安装在多个位置。</span><span class="sxs-lookup"><span data-stu-id="adb54-123">The .NET Core SDK is installed in multiple locations.</span></span> <span data-ttu-id="adb54-124">仅显示安装在 C:\\Program Files\\dotnet\\sdk\\ 的 SDK 的模板。</span><span class="sxs-lookup"><span data-stu-id="adb54-124">Only templates from the SDKs installed at 'C:\\Program Files\\dotnet\\sdk\\' are displayed.</span></span>

<span data-ttu-id="adb54-125">如果在 C:\\Program Files\\dotnet\\sdk\\ 之外的目录中至少安装了一个 .NET Core SDK，则会看到此消息。</span><span class="sxs-lookup"><span data-stu-id="adb54-125">You see this message when you have at least one installation of the .NET Core SDK in a directory outside of *C:\\Program Files\\dotnet\\sdk\\*.</span></span> <span data-ttu-id="adb54-126">发生这种情况，通常是由于使用复制/粘贴而不是 MSI 安装程序在计算机上部署了 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="adb54-126">Usually this happens when the .NET Core SDK has been deployed on a machine using copy/paste instead of the MSI installer.</span></span>

<span data-ttu-id="adb54-127">卸载所有 32 位 .NET Core SDK 和运行时，以防出现此警告。</span><span class="sxs-lookup"><span data-stu-id="adb54-127">Uninstall all 32-bit .NET Core SDKs and runtimes to prevent this warning.</span></span> <span data-ttu-id="adb54-128">从“控制面板” > “程序和功能” > “卸载或更改程序”卸载  。</span><span class="sxs-lookup"><span data-stu-id="adb54-128">Uninstall from **Control Panel** > **Programs and Features** > **Uninstall or change a program**.</span></span> <span data-ttu-id="adb54-129">如果了解警告出现的原因及其含义，可以忽略此警告。</span><span class="sxs-lookup"><span data-stu-id="adb54-129">If you understand why the warning occurs and its implications, you can ignore the warning.</span></span>

### <a name="no-net-core-sdks-were-detected"></a><span data-ttu-id="adb54-130">未检测到 .NET Core SDK</span><span class="sxs-lookup"><span data-stu-id="adb54-130">No .NET Core SDKs were detected</span></span>

* <span data-ttu-id="adb54-131">在 Visual Studio 中，ASP.NET Core 的“新建项目”对话框中可能会显示以下警告：</span><span class="sxs-lookup"><span data-stu-id="adb54-131">In the Visual Studio **New Project** dialog for ASP.NET Core, you may see the following warning:</span></span>

  > <span data-ttu-id="adb54-132">未检测到任何 .NET Core SDK，请确保它们包含在环境变量 `PATH` 中。</span><span class="sxs-lookup"><span data-stu-id="adb54-132">No .NET Core SDKs were detected, ensure they are included in the environment variable `PATH`.</span></span>

* <span data-ttu-id="adb54-133">执行 `dotnet` 命令时，警告显示为：</span><span class="sxs-lookup"><span data-stu-id="adb54-133">When executing a `dotnet` command, the warning appears as:</span></span>

  > <span data-ttu-id="adb54-134">找不到任何已安装的 dotnet SDK。</span><span class="sxs-lookup"><span data-stu-id="adb54-134">It was not possible to find any installed dotnet SDKs.</span></span>

<span data-ttu-id="adb54-135">如果环境变量 `PATH` 不指向计算机上的任何 .NET Core SDK，则会出现这些警告。</span><span class="sxs-lookup"><span data-stu-id="adb54-135">These warnings appear when the environment variable `PATH` doesn't point to any .NET Core SDKs on the machine.</span></span> <span data-ttu-id="adb54-136">若要解决此问题，请执行以下操作:</span><span class="sxs-lookup"><span data-stu-id="adb54-136">To resolve this problem:</span></span>

* <span data-ttu-id="adb54-137">安装 .NET Core SDK。</span><span class="sxs-lookup"><span data-stu-id="adb54-137">Install the .NET Core SDK.</span></span> <span data-ttu-id="adb54-138">从 [.NET 下载](https://dotnet.microsoft.com/download)获取最新的安装程序。</span><span class="sxs-lookup"><span data-stu-id="adb54-138">Obtain the latest installer from [.NET Downloads](https://dotnet.microsoft.com/download).</span></span>
* <span data-ttu-id="adb54-139">验证 `PATH` 环境变量是否指向安装 SDK 的位置（对于 64 位/x64，为 `C:\Program Files\dotnet\`，对于 32 位/x86，为 `C:\Program Files (x86)\dotnet\`）。</span><span class="sxs-lookup"><span data-stu-id="adb54-139">Verify that the `PATH` environment variable points to the location where the SDK is installed (`C:\Program Files\dotnet\` for 64-bit/x64 or `C:\Program Files (x86)\dotnet\` for 32-bit/x86).</span></span> <span data-ttu-id="adb54-140">SDK 安装程序通常会设置 `PATH`。</span><span class="sxs-lookup"><span data-stu-id="adb54-140">The SDK installer normally sets the `PATH`.</span></span> <span data-ttu-id="adb54-141">始终在同一台计算机上安装相同位数的 SDK 和运行时。</span><span class="sxs-lookup"><span data-stu-id="adb54-141">Always install the same bitness SDKs and runtimes on the same machine.</span></span>

### <a name="missing-sdk-after-installing-the-net-core-hosting-bundle"></a><span data-ttu-id="adb54-142">安装 .NET Core 托管捆绑包后缺少 SDK</span><span class="sxs-lookup"><span data-stu-id="adb54-142">Missing SDK after installing the .NET Core Hosting Bundle</span></span>

<span data-ttu-id="adb54-143">如果安装了 .NET Core 运行时以指向 32 位 (x86) 版本的 .NET Core (`C:\Program Files (x86)\dotnet\`)，那么安装 [.NET Core 托管捆绑包](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)会修改 `PATH`。</span><span class="sxs-lookup"><span data-stu-id="adb54-143">Installing the [.NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) modifies the `PATH` when it installs the .NET Core runtime to point to the 32-bit (x86) version of .NET Core (`C:\Program Files (x86)\dotnet\`).</span></span> <span data-ttu-id="adb54-144">当使用 32 位 (x86) .NET Core `dotnet` 命令时，这可能会导致缺少 SDK（[未检测到 .NET Core SDK](#no-net-core-sdks-were-detected)）。</span><span class="sxs-lookup"><span data-stu-id="adb54-144">This can result in missing SDKs when the 32-bit (x86) .NET Core `dotnet` command is used ([No .NET Core SDKs were detected](#no-net-core-sdks-were-detected)).</span></span> <span data-ttu-id="adb54-145">若要解决此问题，请将 `C:\Program Files\dotnet\` 移到 `PATH` 上 `C:\Program Files (x86)\dotnet\` 之前的位置。</span><span class="sxs-lookup"><span data-stu-id="adb54-145">To resolve this problem, move `C:\Program Files\dotnet\` to a position before `C:\Program Files (x86)\dotnet\` on the `PATH`.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="adb54-146">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="adb54-146">Obtain data from an app</span></span>

<span data-ttu-id="adb54-147">如果某个应用能够响应请求，则可以使用中间件从应用获取以下数据：</span><span class="sxs-lookup"><span data-stu-id="adb54-147">If an app is capable of responding to requests, you can obtain the following data from the app using middleware:</span></span>

* <span data-ttu-id="adb54-148">请求：方法、方案、主机、基路径、路径、查询字符串、标头</span><span class="sxs-lookup"><span data-stu-id="adb54-148">Request: Method, scheme, host, pathbase, path, query string, headers</span></span>
* <span data-ttu-id="adb54-149">连接: 远程 IP 地址、远程端口、本地 IP 地址、本地端口、客户端证书</span><span class="sxs-lookup"><span data-stu-id="adb54-149">Connection: Remote IP address, remote port, local IP address, local port, client certificate</span></span>
* <span data-ttu-id="adb54-150">Identity：名称、显示名称</span><span class="sxs-lookup"><span data-stu-id="adb54-150">Identity: Name, display name</span></span>
* <span data-ttu-id="adb54-151">配置设置</span><span class="sxs-lookup"><span data-stu-id="adb54-151">Configuration settings</span></span>
* <span data-ttu-id="adb54-152">环境变量</span><span class="sxs-lookup"><span data-stu-id="adb54-152">Environment variables</span></span>

<span data-ttu-id="adb54-153">将以下[中间件](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)代码置于 `Startup.Configure` 方法的请求处理管道的开头。</span><span class="sxs-lookup"><span data-stu-id="adb54-153">Place the following [middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) code at the beginning of the `Startup.Configure` method's request processing pipeline.</span></span> <span data-ttu-id="adb54-154">在运行中间件之前会检查环境，以确保仅在开发环境中执行代码。</span><span class="sxs-lookup"><span data-stu-id="adb54-154">The environment is checked before the middleware is run to ensure that the code is only executed in the Development environment.</span></span>

<span data-ttu-id="adb54-155">若要获取环境，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="adb54-155">To obtain the environment, use either of the following approaches:</span></span>

* <span data-ttu-id="adb54-156">将 `IHostingEnvironment` 插入到 `Startup.Configure` 方法中，并检查带有本地变量的环境。</span><span class="sxs-lookup"><span data-stu-id="adb54-156">Inject the `IHostingEnvironment` into the `Startup.Configure` method and check the environment with the local variable.</span></span> <span data-ttu-id="adb54-157">下面的示例代码对此方法进行了演示。</span><span class="sxs-lookup"><span data-stu-id="adb54-157">The following sample code demonstrates this approach.</span></span>

* <span data-ttu-id="adb54-158">将环境分配到 `Startup` 类中的属性。</span><span class="sxs-lookup"><span data-stu-id="adb54-158">Assign the environment to a property in the `Startup` class.</span></span> <span data-ttu-id="adb54-159">使用属性（例如 `if (Environment.IsDevelopment())`）检查环境。</span><span class="sxs-lookup"><span data-stu-id="adb54-159">Check the environment using the property (for example, `if (Environment.IsDevelopment())`).</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, 
    IConfiguration config)
{
    if (env.IsDevelopment())
    {
        app.Run(async (context) =>
        {
            var sb = new StringBuilder();
            var nl = System.Environment.NewLine;
            var rule = string.Concat(nl, new string('-', 40), nl);
            var authSchemeProvider = app.ApplicationServices
                .GetRequiredService<IAuthenticationSchemeProvider>();

            sb.Append($"Request{rule}");
            sb.Append($"{DateTimeOffset.Now}{nl}");
            sb.Append($"{context.Request.Method} {context.Request.Path}{nl}");
            sb.Append($"Scheme: {context.Request.Scheme}{nl}");
            sb.Append($"Host: {context.Request.Headers["Host"]}{nl}");
            sb.Append($"PathBase: {context.Request.PathBase.Value}{nl}");
            sb.Append($"Path: {context.Request.Path.Value}{nl}");
            sb.Append($"Query: {context.Request.QueryString.Value}{nl}{nl}");

            sb.Append($"Connection{rule}");
            sb.Append($"RemoteIp: {context.Connection.RemoteIpAddress}{nl}");
            sb.Append($"RemotePort: {context.Connection.RemotePort}{nl}");
            sb.Append($"LocalIp: {context.Connection.LocalIpAddress}{nl}");
            sb.Append($"LocalPort: {context.Connection.LocalPort}{nl}");
            sb.Append($"ClientCert: {context.Connection.ClientCertificate}{nl}{nl}");

            sb.Append($"Identity{rule}");
            sb.Append($"User: {context.User.Identity.Name}{nl}");
            var scheme = await authSchemeProvider
                .GetSchemeAsync(IISDefaults.AuthenticationScheme);
            sb.Append($"DisplayName: {scheme?.DisplayName}{nl}{nl}");

            sb.Append($"Headers{rule}");
            foreach (var header in context.Request.Headers)
            {
                sb.Append($"{header.Key}: {header.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Websockets{rule}");
            if (context.Features.Get<IHttpUpgradeFeature>() != null)
            {
                sb.Append($"Status: Enabled{nl}{nl}");
            }
            else
            {
                sb.Append($"Status: Disabled{nl}{nl}");
            }

            sb.Append($"Configuration{rule}");
            foreach (var pair in config.AsEnumerable())
            {
                sb.Append($"{pair.Path}: {pair.Value}{nl}");
            }
            sb.Append(nl);

            sb.Append($"Environment Variables{rule}");
            var vars = System.Environment.GetEnvironmentVariables();
            foreach (var key in vars.Keys.Cast<string>().OrderBy(key => key, 
                StringComparer.OrdinalIgnoreCase))
            {
                var value = vars[key];
                sb.Append($"{key}: {value}{nl}");
            }

            context.Response.ContentType = "text/plain";
            await context.Response.WriteAsync(sb.ToString());
        });
    }
}
```

## <a name="debug-aspnet-core-apps"></a><span data-ttu-id="adb54-160">调试 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="adb54-160">Debug ASP.NET Core apps</span></span>

<span data-ttu-id="adb54-161">以下链接提供有关调试 ASP.NET Core 应用的信息。</span><span class="sxs-lookup"><span data-stu-id="adb54-161">The following links provide information on debugging ASP.NET Core apps.</span></span>

* [<span data-ttu-id="adb54-162">在 Linux 上调试 ASP Core</span><span class="sxs-lookup"><span data-stu-id="adb54-162">Debugging ASP Core on Linux</span></span>](https://devblogs.microsoft.com/premier-developer/debugging-asp-core-on-linux-with-visual-studio-2017/)
* [<span data-ttu-id="adb54-163">通过 SSH 在 Unix 上调试 .NET Core</span><span class="sxs-lookup"><span data-stu-id="adb54-163">Debugging .NET Core on Unix over SSH</span></span>](https://devblogs.microsoft.com/devops/debugging-net-core-on-unix-over-ssh/)
* [<span data-ttu-id="adb54-164">快速入门：使用 Visual Studio 调试程序调试 ASP.NET</span><span class="sxs-lookup"><span data-stu-id="adb54-164">Quickstart: Debug ASP.NET with the Visual Studio debugger</span></span>](/visualstudio/debugger/quickstart-debug-aspnet)
* <span data-ttu-id="adb54-165">有关更多调试信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/2960)。</span><span class="sxs-lookup"><span data-stu-id="adb54-165">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/2960) for more debugging information.</span></span>
