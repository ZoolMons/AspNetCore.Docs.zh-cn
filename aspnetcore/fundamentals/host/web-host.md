---
title: ASP.NET Core Web 主机
author: rick-anderson
description: 了解有关 ASP.NET Core 中负责应用启动和生存期管理的 Web 主机。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
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
uid: fundamentals/host/web-host
ms.openlocfilehash: 904b57f95cbc48a8177174dc9be770e8a6abf146
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "96035874"
---
# <a name="aspnet-core-web-host"></a><span data-ttu-id="652f2-103">ASP.NET Core Web 主机</span><span class="sxs-lookup"><span data-stu-id="652f2-103">ASP.NET Core Web Host</span></span>

<span data-ttu-id="652f2-104">ASP.NET Core 应用配置和启动“主机”。</span><span class="sxs-lookup"><span data-stu-id="652f2-104">ASP.NET Core apps configure and launch a *host*.</span></span> <span data-ttu-id="652f2-105">主机负责应用程序启动和生存期管理。</span><span class="sxs-lookup"><span data-stu-id="652f2-105">The host is responsible for app startup and lifetime management.</span></span> <span data-ttu-id="652f2-106">至少，主机配置服务器和请求处理管道。</span><span class="sxs-lookup"><span data-stu-id="652f2-106">At a minimum, the host configures a server and a request processing pipeline.</span></span> <span data-ttu-id="652f2-107">主机还可以设置日志记录、依赖关系注入和配置。</span><span class="sxs-lookup"><span data-stu-id="652f2-107">The host can also set up logging, dependency injection, and configuration.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="652f2-108">本文介绍了只适用于实现后向兼容性的 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="652f2-108">This article covers the Web Host, which remains available only for backward compatibility.</span></span> <span data-ttu-id="652f2-109">建议对所有应用类型使用[通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="652f2-109">The [Generic Host](xref:fundamentals/host/generic-host) is recommended for all app types.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="652f2-110">本文介绍了用于托管 Web 应用的 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="652f2-110">This article covers the Web Host, which is for hosting web apps.</span></span> <span data-ttu-id="652f2-111">对于其他类型的应用，请使用[通用主机](xref:fundamentals/host/generic-host)。</span><span class="sxs-lookup"><span data-stu-id="652f2-111">For other kinds of apps, use the [Generic Host](xref:fundamentals/host/generic-host).</span></span>

::: moniker-end

## <a name="set-up-a-host"></a><span data-ttu-id="652f2-112">设置主机</span><span class="sxs-lookup"><span data-stu-id="652f2-112">Set up a host</span></span>

<span data-ttu-id="652f2-113">创建使用 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 实例的主机。</span><span class="sxs-lookup"><span data-stu-id="652f2-113">Create a host using an instance of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="652f2-114">通常在应用的入口点来执行 `Main` 方法。</span><span class="sxs-lookup"><span data-stu-id="652f2-114">This is typically performed in the app's entry point, the `Main` method.</span></span>

<span data-ttu-id="652f2-115">在项目模板中，`Main` 位于 Program.cs。</span><span class="sxs-lookup"><span data-stu-id="652f2-115">In the project templates, `Main` is located in *Program.cs*.</span></span> <span data-ttu-id="652f2-116">典型应用调用 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 来开始创建主机：</span><span class="sxs-lookup"><span data-stu-id="652f2-116">A typical app calls [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) to start setting up a host:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseStartup<Startup>();
}
```

<span data-ttu-id="652f2-117">调用 `CreateDefaultBuilder` 的代码位于名为 `CreateWebHostBuilder` 的方法中，这让它区分于 `Main` 中对生成器对象调用 `Run` 的代码。</span><span class="sxs-lookup"><span data-stu-id="652f2-117">The code that calls `CreateDefaultBuilder` is in a method named `CreateWebHostBuilder`, which separates it from the code in `Main` that calls `Run` on the builder object.</span></span> <span data-ttu-id="652f2-118">如果使用 [Entity Framework Core 工具](/ef/core/miscellaneous/cli/)，必须有这种区分。</span><span class="sxs-lookup"><span data-stu-id="652f2-118">This separation is required if you use [Entity Framework Core tools](/ef/core/miscellaneous/cli/).</span></span> <span data-ttu-id="652f2-119">这些工具希望找到 `CreateWebHostBuilder` 方法，可以在设计时调用此方法来配置主机，而无需运行应用。</span><span class="sxs-lookup"><span data-stu-id="652f2-119">The tools expect to find a `CreateWebHostBuilder` method that they can call at design time to configure the host without running the app.</span></span> <span data-ttu-id="652f2-120">一种替代方法是实现 `IDesignTimeDbContextFactory`。</span><span class="sxs-lookup"><span data-stu-id="652f2-120">An alternative is to implement `IDesignTimeDbContextFactory`.</span></span> <span data-ttu-id="652f2-121">有关详细信息，请参阅[设计时 DbContext 创建](/ef/core/miscellaneous/cli/dbcontext-creation)。</span><span class="sxs-lookup"><span data-stu-id="652f2-121">For more information, see [Design-time DbContext Creation](/ef/core/miscellaneous/cli/dbcontext-creation).</span></span>

<span data-ttu-id="652f2-122">`CreateDefaultBuilder` 执行下列任务：</span><span class="sxs-lookup"><span data-stu-id="652f2-122">`CreateDefaultBuilder` performs the following tasks:</span></span>

* <span data-ttu-id="652f2-123">使用应用的托管配置提供程序将 [Kestrel](xref:fundamentals/servers/kestrel) 服务器配置为 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="652f2-123">Configures [Kestrel](xref:fundamentals/servers/kestrel) server as the web server using the app's hosting configuration providers.</span></span> <span data-ttu-id="652f2-124">有关 Kestrel 服务器默认选项，请参阅 <xref:fundamentals/servers/kestrel#kestrel-options>。</span><span class="sxs-lookup"><span data-stu-id="652f2-124">For the Kestrel server's default options, see <xref:fundamentals/servers/kestrel#kestrel-options>.</span></span>
* <span data-ttu-id="652f2-125">将[内容根目录](xref:fundamentals/index#content-root)设置为由 [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory) 返回的路径。</span><span class="sxs-lookup"><span data-stu-id="652f2-125">Sets the [content root](xref:fundamentals/index#content-root) to the path returned by [Directory.GetCurrentDirectory](/dotnet/api/system.io.directory.getcurrentdirectory).</span></span>
* <span data-ttu-id="652f2-126">通过以下对象加载[主机配置](#host-configuration-values)：</span><span class="sxs-lookup"><span data-stu-id="652f2-126">Loads [host configuration](#host-configuration-values) from:</span></span>
  * <span data-ttu-id="652f2-127">前缀为 `ASPNETCORE_` 的环境变量（例如，`ASPNETCORE_ENVIRONMENT`）。</span><span class="sxs-lookup"><span data-stu-id="652f2-127">Environment variables prefixed with `ASPNETCORE_` (for example, `ASPNETCORE_ENVIRONMENT`).</span></span>
  * <span data-ttu-id="652f2-128">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="652f2-128">Command-line arguments.</span></span>
* <span data-ttu-id="652f2-129">按以下顺序加载应用配置：</span><span class="sxs-lookup"><span data-stu-id="652f2-129">Loads app configuration in the following order from:</span></span>
  * <span data-ttu-id="652f2-130">*appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="652f2-130">*appsettings.json*.</span></span>
  * <span data-ttu-id="652f2-131">appsettings.{Environment}.json。</span><span class="sxs-lookup"><span data-stu-id="652f2-131">*appsettings.{Environment}.json*.</span></span>
  * <span data-ttu-id="652f2-132">应用在使用入口程序集的 `Development` 环境中运行时的[用户机密](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="652f2-132">[User secrets](xref:security/app-secrets) when the app runs in the `Development` environment using the entry assembly.</span></span>
  * <span data-ttu-id="652f2-133">环境变量。</span><span class="sxs-lookup"><span data-stu-id="652f2-133">Environment variables.</span></span>
  * <span data-ttu-id="652f2-134">命令行参数。</span><span class="sxs-lookup"><span data-stu-id="652f2-134">Command-line arguments.</span></span>
* <span data-ttu-id="652f2-135">配置控制台和调试输出的[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="652f2-135">Configures [logging](xref:fundamentals/logging/index) for console and debug output.</span></span> <span data-ttu-id="652f2-136">日志记录包含 appsettings.json 或 appsettings.{Environment}.json 文件的日志记录配置部分中指定的[日志筛选](xref:fundamentals/logging/index#log-filtering)规则。</span><span class="sxs-lookup"><span data-stu-id="652f2-136">Logging includes [log filtering](xref:fundamentals/logging/index#log-filtering) rules specified in a Logging configuration section of an *appsettings.json* or *appsettings.{Environment}.json* file.</span></span>
* <span data-ttu-id="652f2-137">使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)在 IIS 后面运行时，`CreateDefaultBuilder` 会启用 [IIS 集成](xref:host-and-deploy/iis/index)，这会配置应用的基址和端口。</span><span class="sxs-lookup"><span data-stu-id="652f2-137">When running behind IIS with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), `CreateDefaultBuilder` enables [IIS Integration](xref:host-and-deploy/iis/index), which configures the app's base address and port.</span></span> <span data-ttu-id="652f2-138">IIS 集成还配置应用以[捕获启动错误](#capture-startup-errors)。</span><span class="sxs-lookup"><span data-stu-id="652f2-138">IIS Integration also configures the app to [capture startup errors](#capture-startup-errors).</span></span> <span data-ttu-id="652f2-139">有关 IIS 默认选项，请参阅 <xref:host-and-deploy/iis/index#iis-options>。</span><span class="sxs-lookup"><span data-stu-id="652f2-139">For the IIS default options, see <xref:host-and-deploy/iis/index#iis-options>.</span></span>
* <span data-ttu-id="652f2-140">如果应用环境为“开发”，请将 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) 设为 `true`。</span><span class="sxs-lookup"><span data-stu-id="652f2-140">Sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span> <span data-ttu-id="652f2-141">有关详细信息，请参阅[作用域验证](#scope-validation)。</span><span class="sxs-lookup"><span data-stu-id="652f2-141">For more information, see [Scope validation](#scope-validation).</span></span>

<span data-ttu-id="652f2-142">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration)、[ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) 以及 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 的其他方法和扩展方法可重写和增强 `CreateDefaultBuilder` 定义的配置。</span><span class="sxs-lookup"><span data-stu-id="652f2-142">The configuration defined by `CreateDefaultBuilder` can be overridden and augmented by [ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration), [ConfigureLogging](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging), and other methods and extension methods of [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder).</span></span> <span data-ttu-id="652f2-143">下面是一些示例：</span><span class="sxs-lookup"><span data-stu-id="652f2-143">A few examples follow:</span></span>

* <span data-ttu-id="652f2-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) 用于指定应用的其他 `IConfiguration`。</span><span class="sxs-lookup"><span data-stu-id="652f2-144">[ConfigureAppConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configureappconfiguration) is used to specify additional `IConfiguration` for the app.</span></span> <span data-ttu-id="652f2-145">下面的 `ConfigureAppConfiguration` 调用添加委托，以在 appsettings.xml 文件中添加应用配置。</span><span class="sxs-lookup"><span data-stu-id="652f2-145">The following `ConfigureAppConfiguration` call adds a delegate to include app configuration in the *appsettings.xml* file.</span></span> <span data-ttu-id="652f2-146">可多次调用 `ConfigureAppConfiguration`。</span><span class="sxs-lookup"><span data-stu-id="652f2-146">`ConfigureAppConfiguration` may be called multiple times.</span></span> <span data-ttu-id="652f2-147">请注意，此配置不适用于主机（例如，服务器 URL 或环境）。</span><span class="sxs-lookup"><span data-stu-id="652f2-147">Note that this configuration doesn't apply to the host (for example, server URLs or environment).</span></span> <span data-ttu-id="652f2-148">请参阅[主机配置值](#host-configuration-values)部分。</span><span class="sxs-lookup"><span data-stu-id="652f2-148">See the [Host configuration values](#host-configuration-values) section.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            config.AddXmlFile("appsettings.xml", optional: true, reloadOnChange: true);
        })
        ...
    ```

* <span data-ttu-id="652f2-149">下面的 `ConfigureLogging` 调用添加委托，以将最小日志记录级别 ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) 配置为 [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel)。</span><span class="sxs-lookup"><span data-stu-id="652f2-149">The following `ConfigureLogging` call adds a delegate to configure the minimum logging level ([SetMinimumLevel](/dotnet/api/microsoft.extensions.logging.loggingbuilderextensions.setminimumlevel)) to [LogLevel.Warning](/dotnet/api/microsoft.extensions.logging.loglevel).</span></span> <span data-ttu-id="652f2-150">此设置重写 `CreateDefaultBuilder` 在 appsettings.Development.json 和 appsettings.Production.json 中配置的设置，分别为 `LogLevel.Debug` 和 `LogLevel.Error`。</span><span class="sxs-lookup"><span data-stu-id="652f2-150">This setting overrides the settings in *appsettings.Development.json* (`LogLevel.Debug`) and *appsettings.Production.json* (`LogLevel.Error`) configured by `CreateDefaultBuilder`.</span></span> <span data-ttu-id="652f2-151">可多次调用 `ConfigureLogging`。</span><span class="sxs-lookup"><span data-stu-id="652f2-151">`ConfigureLogging` may be called multiple times.</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureLogging(logging => 
        {
            logging.SetMinimumLevel(LogLevel.Warning);
        })
        ...
    ```

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="652f2-152">下面调用 `ConfigureKestrel` 来重写 `CreateDefaultBuilder` 在配置 Kestrel 时建立的 30,000,000 字节默认 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)：</span><span class="sxs-lookup"><span data-stu-id="652f2-152">The following call to `ConfigureKestrel` overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="652f2-153">下面调用 [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) 来重写 `CreateDefaultBuilder` 在配置 Kestrel 时建立的 30,000,000 字节默认 [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize)：</span><span class="sxs-lookup"><span data-stu-id="652f2-153">The following call to [UseKestrel](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderkestrelextensions.usekestrel) overrides the default [Limits.MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.kestrel.core.kestrelserverlimits.maxrequestbodysize) of 30,000,000 bytes established when Kestrel was configured by `CreateDefaultBuilder`:</span></span>

    ```csharp
    WebHost.CreateDefaultBuilder(args)
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 20000000;
        });
    ```

::: moniker-end

<span data-ttu-id="652f2-154">[内容根](xref:fundamentals/index#content-root) 确定主机搜索内容文件（如 MVC 视图文件）的位置。</span><span class="sxs-lookup"><span data-stu-id="652f2-154">The [content root](xref:fundamentals/index#content-root) determines where the host searches for content files, such as MVC view files.</span></span> <span data-ttu-id="652f2-155">应用从项目的根文件夹启动时，会将项目的根文件夹用作内容根。</span><span class="sxs-lookup"><span data-stu-id="652f2-155">When the app is started from the project's root folder, the project's root folder is used as the content root.</span></span> <span data-ttu-id="652f2-156">这是 [Visual Studio](https://visualstudio.microsoft.com) 和 [dotnet new 模板](/dotnet/core/tools/dotnet-new)中使用的默认值。</span><span class="sxs-lookup"><span data-stu-id="652f2-156">This is the default used in [Visual Studio](https://visualstudio.microsoft.com) and the [dotnet new templates](/dotnet/core/tools/dotnet-new).</span></span>

<span data-ttu-id="652f2-157">有关应用配置的详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="652f2-157">For more information on app configuration, see <xref:fundamentals/configuration/index>.</span></span>

> [!NOTE]
> <span data-ttu-id="652f2-158">作为使用静态 `CreateDefaultBuilder` 方法的替代方法，从 [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 创建主机是一种受 ASP.NET Core 2.x 支持的方法。</span><span class="sxs-lookup"><span data-stu-id="652f2-158">As an alternative to using the static `CreateDefaultBuilder` method, creating a host from [WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) is a supported approach with ASP.NET Core 2.x.</span></span>

<span data-ttu-id="652f2-159">设置主机时，可以提供[配置](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure)和 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) 方法。</span><span class="sxs-lookup"><span data-stu-id="652f2-159">When setting up a host, [Configure](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.configureservices) methods can be provided.</span></span> <span data-ttu-id="652f2-160">如果指定 `Startup` 类，必须定义 `Configure` 方法。</span><span class="sxs-lookup"><span data-stu-id="652f2-160">If a `Startup` class is specified, it must define a `Configure` method.</span></span> <span data-ttu-id="652f2-161">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="652f2-161">For more information, see <xref:fundamentals/startup>.</span></span> <span data-ttu-id="652f2-162">多次调用 `ConfigureServices` 将追加到另一个。</span><span class="sxs-lookup"><span data-stu-id="652f2-162">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="652f2-163">多次调用 `WebHostBuilder` 上的 `Configure` 或 `UseStartup` 将替换以前的设置。</span><span class="sxs-lookup"><span data-stu-id="652f2-163">Multiple calls to `Configure` or `UseStartup` on the `WebHostBuilder` replace previous settings.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="652f2-164">主机配置值</span><span class="sxs-lookup"><span data-stu-id="652f2-164">Host configuration values</span></span>

<span data-ttu-id="652f2-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) 依赖于以下的方法设置主机配置值：</span><span class="sxs-lookup"><span data-stu-id="652f2-165">[WebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder) relies on the following approaches to set the host configuration values:</span></span>

* <span data-ttu-id="652f2-166">主机生成器配置，其中包括格式 `ASPNETCORE_{configurationKey}` 的环境变量。</span><span class="sxs-lookup"><span data-stu-id="652f2-166">Host builder configuration, which includes environment variables with the format `ASPNETCORE_{configurationKey}`.</span></span> <span data-ttu-id="652f2-167">例如 `ASPNETCORE_ENVIRONMENT`。</span><span class="sxs-lookup"><span data-stu-id="652f2-167">For example, `ASPNETCORE_ENVIRONMENT`.</span></span>
* <span data-ttu-id="652f2-168">[UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) 和 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 等扩展（请参阅[重写配置](#override-configuration)部分）。</span><span class="sxs-lookup"><span data-stu-id="652f2-168">Extensions such as [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot) and [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) (see the [Override configuration](#override-configuration) section).</span></span>
* <span data-ttu-id="652f2-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) 和关联键。</span><span class="sxs-lookup"><span data-stu-id="652f2-169">[UseSetting](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilder.usesetting) and the associated key.</span></span> <span data-ttu-id="652f2-170">使用 `UseSetting` 设置值时，该值设置为无论何种类型的字符串。</span><span class="sxs-lookup"><span data-stu-id="652f2-170">When setting a value with `UseSetting`, the value is set as a string regardless of the type.</span></span>

<span data-ttu-id="652f2-171">主机使用任何一个选项设置上一个值。</span><span class="sxs-lookup"><span data-stu-id="652f2-171">The host uses whichever option sets a value last.</span></span> <span data-ttu-id="652f2-172">有关详细信息，请参阅下一部分中的[重写配置](#override-configuration)。</span><span class="sxs-lookup"><span data-stu-id="652f2-172">For more information, see [Override configuration](#override-configuration) in the next section.</span></span>

### <a name="application-key-name"></a><span data-ttu-id="652f2-173">应用程序键（名称）</span><span class="sxs-lookup"><span data-stu-id="652f2-173">Application Key (Name)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="652f2-174">在主机构造期间调用 [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) 或 [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) 时，会自动设置 `IWebHostEnvironment.ApplicationName` 属性。</span><span class="sxs-lookup"><span data-stu-id="652f2-174">The `IWebHostEnvironment.ApplicationName` property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="652f2-175">该值设置为包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="652f2-175">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="652f2-176">要显式设置值，请使用 [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)：</span><span class="sxs-lookup"><span data-stu-id="652f2-176">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="652f2-177">在主机构造期间调用 [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) 或 [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) 时，会自动设置 [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) 属性。</span><span class="sxs-lookup"><span data-stu-id="652f2-177">The [IHostingEnvironment.ApplicationName](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.applicationname) property is automatically set when [UseStartup](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usestartup) or [Configure](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configure) is called during host construction.</span></span> <span data-ttu-id="652f2-178">该值设置为包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="652f2-178">The value is set to the name of the assembly containing the app's entry point.</span></span> <span data-ttu-id="652f2-179">要显式设置值，请使用 [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey)：</span><span class="sxs-lookup"><span data-stu-id="652f2-179">To set the value explicitly, use the [WebHostDefaults.ApplicationKey](/dotnet/api/microsoft.aspnetcore.hosting.webhostdefaults.applicationkey):</span></span>

::: moniker-end

<span data-ttu-id="652f2-180">**密钥**：applicationName</span><span class="sxs-lookup"><span data-stu-id="652f2-180">**Key**: applicationName</span></span>  
<span data-ttu-id="652f2-181">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="652f2-181">**Type**: *string*</span></span>  
<span data-ttu-id="652f2-182">**默认值**：包含应用入口点的程序集的名称。</span><span class="sxs-lookup"><span data-stu-id="652f2-182">**Default**: The name of the assembly containing the app's entry point.</span></span>  
<span data-ttu-id="652f2-183">**设置使用**：`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="652f2-183">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="652f2-184">**环境变量**：`ASPNETCORE_APPLICATIONNAME`</span><span class="sxs-lookup"><span data-stu-id="652f2-184">**Environment variable**: `ASPNETCORE_APPLICATIONNAME`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.ApplicationKey, "CustomApplicationName")
```

### <a name="capture-startup-errors"></a><span data-ttu-id="652f2-185">捕获启动错误</span><span class="sxs-lookup"><span data-stu-id="652f2-185">Capture Startup Errors</span></span>

<span data-ttu-id="652f2-186">此设置控制启动错误的捕获。</span><span class="sxs-lookup"><span data-stu-id="652f2-186">This setting controls the capture of startup errors.</span></span>

<span data-ttu-id="652f2-187">**键**：captureStartupErrors</span><span class="sxs-lookup"><span data-stu-id="652f2-187">**Key**: captureStartupErrors</span></span>  
<span data-ttu-id="652f2-188">**类型**：布尔型（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="652f2-188">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="652f2-189">**默认**：默认为 `false`，除非应用使用 Kestrel 在 IIS 后方运行，其中默认值是 `true`。</span><span class="sxs-lookup"><span data-stu-id="652f2-189">**Default**: Defaults to `false` unless the app runs with Kestrel behind IIS, where the default is `true`.</span></span>  
<span data-ttu-id="652f2-190">**设置使用**：`CaptureStartupErrors`</span><span class="sxs-lookup"><span data-stu-id="652f2-190">**Set using**: `CaptureStartupErrors`</span></span>  
<span data-ttu-id="652f2-191">**环境变量**：`ASPNETCORE_CAPTURESTARTUPERRORS`</span><span class="sxs-lookup"><span data-stu-id="652f2-191">**Environment variable**: `ASPNETCORE_CAPTURESTARTUPERRORS`</span></span>

<span data-ttu-id="652f2-192">当 `false` 时，启动期间出错导致主机退出。</span><span class="sxs-lookup"><span data-stu-id="652f2-192">When `false`, errors during startup result in the host exiting.</span></span> <span data-ttu-id="652f2-193">当 `true` 时，主机在启动期间捕获异常并尝试启动服务器。</span><span class="sxs-lookup"><span data-stu-id="652f2-193">When `true`, the host captures exceptions during startup and attempts to start the server.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .CaptureStartupErrors(true)
```

### <a name="content-root"></a><span data-ttu-id="652f2-194">内容根</span><span class="sxs-lookup"><span data-stu-id="652f2-194">Content root</span></span>

<span data-ttu-id="652f2-195">此设置确定 ASP.NET Core 开始搜索内容文件。</span><span class="sxs-lookup"><span data-stu-id="652f2-195">This setting determines where ASP.NET Core begins searching for content files.</span></span>

<span data-ttu-id="652f2-196">**键**：contentRoot</span><span class="sxs-lookup"><span data-stu-id="652f2-196">**Key**: contentRoot</span></span>  
<span data-ttu-id="652f2-197">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="652f2-197">**Type**: *string*</span></span>  
<span data-ttu-id="652f2-198">**默认**：默认为应用程序集所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="652f2-198">**Default**: Defaults to the folder where the app assembly resides.</span></span>  
<span data-ttu-id="652f2-199">**设置使用**：`UseContentRoot`</span><span class="sxs-lookup"><span data-stu-id="652f2-199">**Set using**: `UseContentRoot`</span></span>  
<span data-ttu-id="652f2-200">**环境变量**：`ASPNETCORE_CONTENTROOT`</span><span class="sxs-lookup"><span data-stu-id="652f2-200">**Environment variable**: `ASPNETCORE_CONTENTROOT`</span></span>

<span data-ttu-id="652f2-201">内容根目录也用作 [Web 根目录](xref:fundamentals/index#web-root)的基路径。</span><span class="sxs-lookup"><span data-stu-id="652f2-201">The content root is also used as the base path for the [web root](xref:fundamentals/index#web-root).</span></span> <span data-ttu-id="652f2-202">如果内容根路径不存在，主机将无法启动。</span><span class="sxs-lookup"><span data-stu-id="652f2-202">If the content root path doesn't exist, the host fails to start.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseContentRoot("c:\\<content-root>")
```

<span data-ttu-id="652f2-203">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="652f2-203">For more information, see:</span></span>

* [<span data-ttu-id="652f2-204">基础知识：内容根目录</span><span class="sxs-lookup"><span data-stu-id="652f2-204">Fundamentals: Content root</span></span>](xref:fundamentals/index#content-root)
* [<span data-ttu-id="652f2-205">Web 根目录</span><span class="sxs-lookup"><span data-stu-id="652f2-205">Web root</span></span>](#web-root)

### <a name="detailed-errors"></a><span data-ttu-id="652f2-206">详细错误</span><span class="sxs-lookup"><span data-stu-id="652f2-206">Detailed Errors</span></span>

<span data-ttu-id="652f2-207">确定是否应捕获详细错误。</span><span class="sxs-lookup"><span data-stu-id="652f2-207">Determines if detailed errors should be captured.</span></span>

<span data-ttu-id="652f2-208">**键**：detailedErrors</span><span class="sxs-lookup"><span data-stu-id="652f2-208">**Key**: detailedErrors</span></span>  
<span data-ttu-id="652f2-209">**类型**：布尔型（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="652f2-209">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="652f2-210">**默认值**：false</span><span class="sxs-lookup"><span data-stu-id="652f2-210">**Default**: false</span></span>  
<span data-ttu-id="652f2-211">**设置使用**：`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="652f2-211">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="652f2-212">**环境变量**：`ASPNETCORE_DETAILEDERRORS`</span><span class="sxs-lookup"><span data-stu-id="652f2-212">**Environment variable**: `ASPNETCORE_DETAILEDERRORS`</span></span>

<span data-ttu-id="652f2-213">启用（或当<a href="#environment">环境</a>设置为 `Development` ）时，应用捕获详细的异常。</span><span class="sxs-lookup"><span data-stu-id="652f2-213">When enabled (or when the <a href="#environment">Environment</a> is set to `Development`), the app captures detailed exceptions.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.DetailedErrorsKey, "true")
```

### <a name="environment"></a><span data-ttu-id="652f2-214">环境</span><span class="sxs-lookup"><span data-stu-id="652f2-214">Environment</span></span>

<span data-ttu-id="652f2-215">设置应用的环境。</span><span class="sxs-lookup"><span data-stu-id="652f2-215">Sets the app's environment.</span></span>

<span data-ttu-id="652f2-216">**键**：环境</span><span class="sxs-lookup"><span data-stu-id="652f2-216">**Key**: environment</span></span>  
<span data-ttu-id="652f2-217">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="652f2-217">**Type**: *string*</span></span>  
<span data-ttu-id="652f2-218">**默认**：生产</span><span class="sxs-lookup"><span data-stu-id="652f2-218">**Default**: Production</span></span>  
<span data-ttu-id="652f2-219">**设置使用**：`UseEnvironment`</span><span class="sxs-lookup"><span data-stu-id="652f2-219">**Set using**: `UseEnvironment`</span></span>  
<span data-ttu-id="652f2-220">**环境变量**：`ASPNETCORE_ENVIRONMENT`</span><span class="sxs-lookup"><span data-stu-id="652f2-220">**Environment variable**: `ASPNETCORE_ENVIRONMENT`</span></span>

<span data-ttu-id="652f2-221">可将环境设置为任何值。</span><span class="sxs-lookup"><span data-stu-id="652f2-221">The environment can be set to any value.</span></span> <span data-ttu-id="652f2-222">框架定义的值包括 `Development``Staging` 和 `Production`。</span><span class="sxs-lookup"><span data-stu-id="652f2-222">Framework-defined values include `Development`, `Staging`, and `Production`.</span></span> <span data-ttu-id="652f2-223">值不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="652f2-223">Values aren't case sensitive.</span></span> <span data-ttu-id="652f2-224">默认情况下，从 `ASPNETCORE_ENVIRONMENT` 环境变量读取环境。</span><span class="sxs-lookup"><span data-stu-id="652f2-224">By default, the *Environment* is read from the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="652f2-225">使用 [Visual Studio](https://visualstudio.microsoft.com) 时，可在 launchSettings.json 文件中设置环境变量。</span><span class="sxs-lookup"><span data-stu-id="652f2-225">When using [Visual Studio](https://visualstudio.microsoft.com), environment variables may be set in the *launchSettings.json* file.</span></span> <span data-ttu-id="652f2-226">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="652f2-226">For more information, see <xref:fundamentals/environments>.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseEnvironment(EnvironmentName.Development)
```

### <a name="hosting-startup-assemblies"></a><span data-ttu-id="652f2-227">承载启动程序集</span><span class="sxs-lookup"><span data-stu-id="652f2-227">Hosting Startup Assemblies</span></span>

<span data-ttu-id="652f2-228">设置应用的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="652f2-228">Sets the app's hosting startup assemblies.</span></span>

<span data-ttu-id="652f2-229">**键**：hostingStartupAssemblies</span><span class="sxs-lookup"><span data-stu-id="652f2-229">**Key**: hostingStartupAssemblies</span></span>  
<span data-ttu-id="652f2-230">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="652f2-230">**Type**: *string*</span></span>  
<span data-ttu-id="652f2-231">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="652f2-231">**Default**: Empty string</span></span>  
<span data-ttu-id="652f2-232">**设置使用**：`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="652f2-232">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="652f2-233">**环境变量**：`ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="652f2-233">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES`</span></span>

<span data-ttu-id="652f2-234">承载启动程序集的以分号分隔的字符串在启动时加载。</span><span class="sxs-lookup"><span data-stu-id="652f2-234">A semicolon-delimited string of hosting startup assemblies to load on startup.</span></span>

<span data-ttu-id="652f2-235">虽然配置值默认为空字符串，但是承载启动程序集会始终包含应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="652f2-235">Although the configuration value defaults to an empty string, the hosting startup assemblies always include the app's assembly.</span></span> <span data-ttu-id="652f2-236">提供承载启动程序集时，当应用在启动过程中生成其公用服务时将它们添加到应用的程序集加载。</span><span class="sxs-lookup"><span data-stu-id="652f2-236">When hosting startup assemblies are provided, they're added to the app's assembly for loading when the app builds its common services during startup.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupAssembliesKey, "assembly1;assembly2")
```

### <a name="https-port"></a><span data-ttu-id="652f2-237">HTTPS 端口</span><span class="sxs-lookup"><span data-stu-id="652f2-237">HTTPS Port</span></span>

<span data-ttu-id="652f2-238">设置 HTTPS 重定向端口。</span><span class="sxs-lookup"><span data-stu-id="652f2-238">Set the HTTPS redirect port.</span></span> <span data-ttu-id="652f2-239">用于[强制实施 HTTPS](xref:security/enforcing-ssl)。</span><span class="sxs-lookup"><span data-stu-id="652f2-239">Used in [enforcing HTTPS](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="652f2-240">键：https_port；类型：字符串；
默认值 ：未设置默认值。</span><span class="sxs-lookup"><span data-stu-id="652f2-240">**Key**: https_port **Type**: *string*
**Default**: A default value isn't set.</span></span>
<span data-ttu-id="652f2-241">设置使用：`UseSetting`
环境变量：`ASPNETCORE_HTTPS_PORT`</span><span class="sxs-lookup"><span data-stu-id="652f2-241">**Set using**: `UseSetting`
**Environment variable**: `ASPNETCORE_HTTPS_PORT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting("https_port", "8080")
```

### <a name="hosting-startup-exclude-assemblies"></a><span data-ttu-id="652f2-242">承载启动排除程序集</span><span class="sxs-lookup"><span data-stu-id="652f2-242">Hosting Startup Exclude Assemblies</span></span>

<span data-ttu-id="652f2-243">承载启动程序集的以分号分隔的字符串在启动时排除。</span><span class="sxs-lookup"><span data-stu-id="652f2-243">A semicolon-delimited string of hosting startup assemblies to exclude on startup.</span></span>

<span data-ttu-id="652f2-244">键：hostingStartupExcludeAssemblies</span><span class="sxs-lookup"><span data-stu-id="652f2-244">**Key**: hostingStartupExcludeAssemblies</span></span>  
<span data-ttu-id="652f2-245">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="652f2-245">**Type**: *string*</span></span>  
<span data-ttu-id="652f2-246">**默认**：空字符串</span><span class="sxs-lookup"><span data-stu-id="652f2-246">**Default**: Empty string</span></span>  
<span data-ttu-id="652f2-247">**设置使用**：`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="652f2-247">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="652f2-248">**环境变量**：`ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span><span class="sxs-lookup"><span data-stu-id="652f2-248">**Environment variable**: `ASPNETCORE_HOSTINGSTARTUPEXCLUDEASSEMBLIES`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.HostingStartupExcludeAssembliesKey, "assembly1;assembly2")
```

### <a name="prefer-hosting-urls"></a><span data-ttu-id="652f2-249">首选承载 URL</span><span class="sxs-lookup"><span data-stu-id="652f2-249">Prefer Hosting URLs</span></span>

<span data-ttu-id="652f2-250">指示主机是否应该侦听使用 `WebHostBuilder` 配置的 URL，而不是使用 `IServer` 实现配置的 URL。</span><span class="sxs-lookup"><span data-stu-id="652f2-250">Indicates whether the host should listen on the URLs configured with the `WebHostBuilder` instead of those configured with the `IServer` implementation.</span></span>

<span data-ttu-id="652f2-251">**键**：preferHostingUrls</span><span class="sxs-lookup"><span data-stu-id="652f2-251">**Key**: preferHostingUrls</span></span>  
<span data-ttu-id="652f2-252">**类型**：布尔型（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="652f2-252">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="652f2-253">**默认值**：true</span><span class="sxs-lookup"><span data-stu-id="652f2-253">**Default**: true</span></span>  
<span data-ttu-id="652f2-254">**设置使用**：`PreferHostingUrls`</span><span class="sxs-lookup"><span data-stu-id="652f2-254">**Set using**: `PreferHostingUrls`</span></span>  
<span data-ttu-id="652f2-255">**环境变量**：`ASPNETCORE_PREFERHOSTINGURLS`</span><span class="sxs-lookup"><span data-stu-id="652f2-255">**Environment variable**: `ASPNETCORE_PREFERHOSTINGURLS`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .PreferHostingUrls(false)
```

### <a name="prevent-hosting-startup"></a><span data-ttu-id="652f2-256">阻止承载启动</span><span class="sxs-lookup"><span data-stu-id="652f2-256">Prevent Hosting Startup</span></span>

<span data-ttu-id="652f2-257">阻止承载启动程序集自动加载，包括应用的程序集所配置的承载启动程序集。</span><span class="sxs-lookup"><span data-stu-id="652f2-257">Prevents the automatic loading of hosting startup assemblies, including hosting startup assemblies configured by the app's assembly.</span></span> <span data-ttu-id="652f2-258">有关详细信息，请参阅 <xref:fundamentals/configuration/platform-specific-configuration>。</span><span class="sxs-lookup"><span data-stu-id="652f2-258">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

<span data-ttu-id="652f2-259">**键**：preventHostingStartup</span><span class="sxs-lookup"><span data-stu-id="652f2-259">**Key**: preventHostingStartup</span></span>  
<span data-ttu-id="652f2-260">**类型**：布尔型（`true` 或 `1`）</span><span class="sxs-lookup"><span data-stu-id="652f2-260">**Type**: *bool* (`true` or `1`)</span></span>  
<span data-ttu-id="652f2-261">**默认值**：false</span><span class="sxs-lookup"><span data-stu-id="652f2-261">**Default**: false</span></span>  
<span data-ttu-id="652f2-262">**设置使用**：`UseSetting`</span><span class="sxs-lookup"><span data-stu-id="652f2-262">**Set using**: `UseSetting`</span></span>  
<span data-ttu-id="652f2-263">**环境变量**：`ASPNETCORE_PREVENTHOSTINGSTARTUP`</span><span class="sxs-lookup"><span data-stu-id="652f2-263">**Environment variable**: `ASPNETCORE_PREVENTHOSTINGSTARTUP`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
```

### <a name="server-urls"></a><span data-ttu-id="652f2-264">服务器 URL</span><span class="sxs-lookup"><span data-stu-id="652f2-264">Server URLs</span></span>

<span data-ttu-id="652f2-265">指示 IP 地址或主机地址，其中包含服务器应针对请求侦听的端口和协议。</span><span class="sxs-lookup"><span data-stu-id="652f2-265">Indicates the IP addresses or host addresses with ports and protocols that the server should listen on for requests.</span></span>

<span data-ttu-id="652f2-266">**键**：urls</span><span class="sxs-lookup"><span data-stu-id="652f2-266">**Key**: urls</span></span>  
<span data-ttu-id="652f2-267">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="652f2-267">**Type**: *string*</span></span>  
<span data-ttu-id="652f2-268">**默认**： http://localhost:5000</span><span class="sxs-lookup"><span data-stu-id="652f2-268">**Default**: http://localhost:5000</span></span>  
<span data-ttu-id="652f2-269">**设置使用**：`UseUrls`</span><span class="sxs-lookup"><span data-stu-id="652f2-269">**Set using**: `UseUrls`</span></span>  
<span data-ttu-id="652f2-270">**环境变量**：`ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="652f2-270">**Environment variable**: `ASPNETCORE_URLS`</span></span>

<span data-ttu-id="652f2-271">设置为服务器应响应的以分号分隔 (;) 的 URL 前缀列表。</span><span class="sxs-lookup"><span data-stu-id="652f2-271">Set to a semicolon-separated (;) list of URL prefixes to which the server should respond.</span></span> <span data-ttu-id="652f2-272">例如 `http://localhost:123`。</span><span class="sxs-lookup"><span data-stu-id="652f2-272">For example, `http://localhost:123`.</span></span> <span data-ttu-id="652f2-273">使用“\*”指示服务器应针对请求侦听的使用特定端口和协议（例如 `http://*:5000`）的 IP 地址或主机名。</span><span class="sxs-lookup"><span data-stu-id="652f2-273">Use "\*" to indicate that the server should listen for requests on any IP address or hostname using the specified port and protocol (for example, `http://*:5000`).</span></span> <span data-ttu-id="652f2-274">协议（`http://` 或 `https://`）必须包含每个 URL。</span><span class="sxs-lookup"><span data-stu-id="652f2-274">The protocol (`http://` or `https://`) must be included with each URL.</span></span> <span data-ttu-id="652f2-275">不同的服务器支持的格式有所不同。</span><span class="sxs-lookup"><span data-stu-id="652f2-275">Supported formats vary among servers.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseUrls("http://*:5000;http://localhost:5001;https://hostname:5002")
```

<span data-ttu-id="652f2-276">Kestrel 具有自己的终结点配置 API。</span><span class="sxs-lookup"><span data-stu-id="652f2-276">Kestrel has its own endpoint configuration API.</span></span> <span data-ttu-id="652f2-277">有关详细信息，请参阅 <xref:fundamentals/servers/kestrel#endpoint-configuration>。</span><span class="sxs-lookup"><span data-stu-id="652f2-277">For more information, see <xref:fundamentals/servers/kestrel#endpoint-configuration>.</span></span>

### <a name="shutdown-timeout"></a><span data-ttu-id="652f2-278">关闭超时</span><span class="sxs-lookup"><span data-stu-id="652f2-278">Shutdown Timeout</span></span>

<span data-ttu-id="652f2-279">指定等待 Web 主机关闭的时长。</span><span class="sxs-lookup"><span data-stu-id="652f2-279">Specifies the amount of time to wait for Web Host to shut down.</span></span>

<span data-ttu-id="652f2-280">**键**：shutdownTimeoutSeconds</span><span class="sxs-lookup"><span data-stu-id="652f2-280">**Key**: shutdownTimeoutSeconds</span></span>  
<span data-ttu-id="652f2-281">**类型**：int</span><span class="sxs-lookup"><span data-stu-id="652f2-281">**Type**: *int*</span></span>  
<span data-ttu-id="652f2-282">**默认**：5</span><span class="sxs-lookup"><span data-stu-id="652f2-282">**Default**: 5</span></span>  
<span data-ttu-id="652f2-283">**设置使用**：`UseShutdownTimeout`</span><span class="sxs-lookup"><span data-stu-id="652f2-283">**Set using**: `UseShutdownTimeout`</span></span>  
<span data-ttu-id="652f2-284">**环境变量**：`ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span><span class="sxs-lookup"><span data-stu-id="652f2-284">**Environment variable**: `ASPNETCORE_SHUTDOWNTIMEOUTSECONDS`</span></span>

<span data-ttu-id="652f2-285">虽然键使用 `UseSetting` 接受 int（例如 `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`），但是 [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) 扩展方法采用 [TimeSpan](/dotnet/api/system.timespan)。</span><span class="sxs-lookup"><span data-stu-id="652f2-285">Although the key accepts an *int* with `UseSetting` (for example, `.UseSetting(WebHostDefaults.ShutdownTimeoutKey, "10")`), the [UseShutdownTimeout](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useshutdowntimeout) extension method takes a [TimeSpan](/dotnet/api/system.timespan).</span></span>

<span data-ttu-id="652f2-286">在超时时间段中，托管：</span><span class="sxs-lookup"><span data-stu-id="652f2-286">During the timeout period, hosting:</span></span>

* <span data-ttu-id="652f2-287">触发器 [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping)。</span><span class="sxs-lookup"><span data-stu-id="652f2-287">Triggers [IApplicationLifetime.ApplicationStopping](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.applicationstopping).</span></span>
* <span data-ttu-id="652f2-288">尝试停止托管服务，对服务停止失败的任何错误进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="652f2-288">Attempts to stop hosted services, logging any errors for services that fail to stop.</span></span>

<span data-ttu-id="652f2-289">如果在所有托管服务停止之前就达到了超时时间，则会在应用关闭时会终止剩余的所有活动的服务。</span><span class="sxs-lookup"><span data-stu-id="652f2-289">If the timeout period expires before all of the hosted services stop, any remaining active services are stopped when the app shuts down.</span></span> <span data-ttu-id="652f2-290">即使没有完成处理工作，服务也会停止。</span><span class="sxs-lookup"><span data-stu-id="652f2-290">The services stop even if they haven't finished processing.</span></span> <span data-ttu-id="652f2-291">如果停止服务需要额外的时间，请增加超时时间。</span><span class="sxs-lookup"><span data-stu-id="652f2-291">If services require additional time to stop, increase the timeout.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseShutdownTimeout(TimeSpan.FromSeconds(10))
```

### <a name="startup-assembly"></a><span data-ttu-id="652f2-292">启动程序集</span><span class="sxs-lookup"><span data-stu-id="652f2-292">Startup Assembly</span></span>

<span data-ttu-id="652f2-293">确定要在其中搜索 `Startup` 类的程序集。</span><span class="sxs-lookup"><span data-stu-id="652f2-293">Determines the assembly to search for the `Startup` class.</span></span>

<span data-ttu-id="652f2-294">**键**：startupAssembly</span><span class="sxs-lookup"><span data-stu-id="652f2-294">**Key**: startupAssembly</span></span>  
<span data-ttu-id="652f2-295">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="652f2-295">**Type**: *string*</span></span>  
<span data-ttu-id="652f2-296">**默认**：应用的程序集</span><span class="sxs-lookup"><span data-stu-id="652f2-296">**Default**: The app's assembly</span></span>  
<span data-ttu-id="652f2-297">**设置使用**：`UseStartup`</span><span class="sxs-lookup"><span data-stu-id="652f2-297">**Set using**: `UseStartup`</span></span>  
<span data-ttu-id="652f2-298">**环境变量**：`ASPNETCORE_STARTUPASSEMBLY`</span><span class="sxs-lookup"><span data-stu-id="652f2-298">**Environment variable**: `ASPNETCORE_STARTUPASSEMBLY`</span></span>

<span data-ttu-id="652f2-299">按名称（`string`）或类型（`TStartup`）的程序集可以引用。</span><span class="sxs-lookup"><span data-stu-id="652f2-299">The assembly by name (`string`) or type (`TStartup`) can be referenced.</span></span> <span data-ttu-id="652f2-300">如果调用多个 `UseStartup` 方法，优先选择最后一个方法。</span><span class="sxs-lookup"><span data-stu-id="652f2-300">If multiple `UseStartup` methods are called, the last one takes precedence.</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup("StartupAssemblyName")
```

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseStartup<TStartup>()
```

### <a name="web-root"></a><span data-ttu-id="652f2-301">Web 根</span><span class="sxs-lookup"><span data-stu-id="652f2-301">Web root</span></span>

<span data-ttu-id="652f2-302">设置应用的静态资产的相对路径。</span><span class="sxs-lookup"><span data-stu-id="652f2-302">Sets the relative path to the app's static assets.</span></span>

<span data-ttu-id="652f2-303">**键**：webroot</span><span class="sxs-lookup"><span data-stu-id="652f2-303">**Key**: webroot</span></span>  
<span data-ttu-id="652f2-304">**类型**：string</span><span class="sxs-lookup"><span data-stu-id="652f2-304">**Type**: *string*</span></span>  
<span data-ttu-id="652f2-305">**默认**：默认值为 `wwwroot`。</span><span class="sxs-lookup"><span data-stu-id="652f2-305">**Default**: The default is `wwwroot`.</span></span> <span data-ttu-id="652f2-306">{content root}/wwwroot 的路径必须存在。</span><span class="sxs-lookup"><span data-stu-id="652f2-306">The path to *{content root}/wwwroot* must exist.</span></span> <span data-ttu-id="652f2-307">如果该路径不存在，则使用无操作文件提供程序。</span><span class="sxs-lookup"><span data-stu-id="652f2-307">If the path doesn't exist, a no-op file provider is used.</span></span>  
<span data-ttu-id="652f2-308">**设置使用**：`UseWebRoot`</span><span class="sxs-lookup"><span data-stu-id="652f2-308">**Set using**: `UseWebRoot`</span></span>  
<span data-ttu-id="652f2-309">**环境变量**：`ASPNETCORE_WEBROOT`</span><span class="sxs-lookup"><span data-stu-id="652f2-309">**Environment variable**: `ASPNETCORE_WEBROOT`</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseWebRoot("public")
```

<span data-ttu-id="652f2-310">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="652f2-310">For more information, see:</span></span>

* [<span data-ttu-id="652f2-311">基础知识：Web 根目录</span><span class="sxs-lookup"><span data-stu-id="652f2-311">Fundamentals: Web root</span></span>](xref:fundamentals/index#web-root)
* [<span data-ttu-id="652f2-312">内容根目录</span><span class="sxs-lookup"><span data-stu-id="652f2-312">Content root</span></span>](#content-root)

## <a name="override-configuration"></a><span data-ttu-id="652f2-313">重写配置</span><span class="sxs-lookup"><span data-stu-id="652f2-313">Override configuration</span></span>

<span data-ttu-id="652f2-314">使用[配置](xref:fundamentals/configuration/index)可以配置 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="652f2-314">Use [Configuration](xref:fundamentals/configuration/index) to configure Web Host.</span></span> <span data-ttu-id="652f2-315">在下面的示例中，主机配置是根据需要在 hostsettings.json 文件中指定。</span><span class="sxs-lookup"><span data-stu-id="652f2-315">In the following example, host configuration is optionally specified in a *hostsettings.json* file.</span></span> <span data-ttu-id="652f2-316">命令行参数可能会重写从 hostsettings.json 文件加载的任何配置。</span><span class="sxs-lookup"><span data-stu-id="652f2-316">Any configuration loaded from the *hostsettings.json* file may be overridden by command-line arguments.</span></span> <span data-ttu-id="652f2-317">生成的配置（在 `config` 中）用于通过 [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 配置主机。</span><span class="sxs-lookup"><span data-stu-id="652f2-317">The built configuration (in `config`) is used to configure the host with [UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration).</span></span> <span data-ttu-id="652f2-318">`IWebHostBuilder` 配置会添加到应用配置中，但反之不亦然&mdash;`ConfigureAppConfiguration` 不影响 `IWebHostBuilder` 配置。</span><span class="sxs-lookup"><span data-stu-id="652f2-318">`IWebHostBuilder` configuration is added to the app's configuration, but the converse isn't true&mdash;`ConfigureAppConfiguration` doesn't affect the `IWebHostBuilder` configuration.</span></span>

<span data-ttu-id="652f2-319">先用 hostsettings.json config 重写 `UseUrls` 提供的配置，再用命令行参数 config：</span><span class="sxs-lookup"><span data-stu-id="652f2-319">Overriding the configuration provided by `UseUrls` with *hostsettings.json* config first, command-line argument config second:</span></span>

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var config = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("hostsettings.json", optional: true)
            .AddCommandLine(args)
            .Build();

        return WebHost.CreateDefaultBuilder(args)
            .UseUrls("http://*:5000")
            .UseConfiguration(config)
            .Configure(app =>
            {
                app.Run(context => 
                    context.Response.WriteAsync("Hello, World!"));
            });
    }
}
```

<span data-ttu-id="652f2-320">hostsettings.json：</span><span class="sxs-lookup"><span data-stu-id="652f2-320">*hostsettings.json*:</span></span>

```json
{
    urls: "http://*:5005"
}
```

> [!NOTE]
> <span data-ttu-id="652f2-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) 只将所提供的 `IConfiguration` 中的密钥复制到主机生成器配置中。</span><span class="sxs-lookup"><span data-stu-id="652f2-321">[UseConfiguration](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useconfiguration) only copies keys from the provided `IConfiguration` to the host builder configuration.</span></span> <span data-ttu-id="652f2-322">因此，JSON、INI 和 XML 设置文件的设置 `reloadOnChange: true` 没有任何影响。</span><span class="sxs-lookup"><span data-stu-id="652f2-322">Therefore, setting `reloadOnChange: true` for JSON, INI, and XML settings files has no effect.</span></span>

<span data-ttu-id="652f2-323">若要指定在特定的 URL 上运行的主机，所需的值可以在执行 [dotnet 运行](/dotnet/core/tools/dotnet-run)时从命令提示符传入。</span><span class="sxs-lookup"><span data-stu-id="652f2-323">To specify the host run on a particular URL, the desired value can be passed in from a command prompt when executing [dotnet run](/dotnet/core/tools/dotnet-run).</span></span> <span data-ttu-id="652f2-324">命令行参数重写 hostsettings.json 文件中的 `urls` 值，且服务器侦听端口 8080：</span><span class="sxs-lookup"><span data-stu-id="652f2-324">The command-line argument overrides the `urls` value from the *hostsettings.json* file, and the server listens on port 8080:</span></span>

```dotnetcli
dotnet run --urls "http://*:8080"
```

## <a name="manage-the-host"></a><span data-ttu-id="652f2-325">管理主机</span><span class="sxs-lookup"><span data-stu-id="652f2-325">Manage the host</span></span>

<span data-ttu-id="652f2-326">**运行**</span><span class="sxs-lookup"><span data-stu-id="652f2-326">**Run**</span></span>

<span data-ttu-id="652f2-327">`Run` 方法启动 Web 应用并阻止调用线程，直到关闭主机：</span><span class="sxs-lookup"><span data-stu-id="652f2-327">The `Run` method starts the web app and blocks the calling thread until the host is shut down:</span></span>

```csharp
host.Run();
```

<span data-ttu-id="652f2-328">**Start**</span><span class="sxs-lookup"><span data-stu-id="652f2-328">**Start**</span></span>

<span data-ttu-id="652f2-329">通过调用 `Start` 方法以非阻止方式运行主机：</span><span class="sxs-lookup"><span data-stu-id="652f2-329">Run the host in a non-blocking manner by calling its `Start` method:</span></span>

```csharp
using (host)
{
    host.Start();
    Console.ReadLine();
}
```

<span data-ttu-id="652f2-330">如果 URL 列表传递给 `Start` 方法，该列表侦听指定的 URL：</span><span class="sxs-lookup"><span data-stu-id="652f2-330">If a list of URLs is passed to the `Start` method, it listens on the URLs specified:</span></span>

```csharp
var urls = new List<string>()
{
    "http://*:5000",
    "http://localhost:5001"
};

var host = new WebHostBuilder()
    .UseKestrel()
    .UseStartup<Startup>()
    .Start(urls.ToArray());

using (host)
{
    Console.ReadLine();
}
```

<span data-ttu-id="652f2-331">应用可以使用通过静态便捷方法预配置的 `CreateDefaultBuilder` 默认值初始化并启动新的主机。</span><span class="sxs-lookup"><span data-stu-id="652f2-331">The app can initialize and start a new host using the pre-configured defaults of `CreateDefaultBuilder` using a static convenience method.</span></span> <span data-ttu-id="652f2-332">这些方法在没有控制台输出的情况下启动服务器，并使用 [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) 等待中断（Ctrl-C/SIGINT 或 SIGTERM）：</span><span class="sxs-lookup"><span data-stu-id="652f2-332">These methods start the server without console output and with [WaitForShutdown](/dotnet/api/microsoft.aspnetcore.hosting.webhostextensions.waitforshutdown) wait for a break (Ctrl-C/SIGINT or SIGTERM):</span></span>

<span data-ttu-id="652f2-333">**Start(RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="652f2-333">**Start(RequestDelegate app)**</span></span>

<span data-ttu-id="652f2-334">从 `RequestDelegate` 开始：</span><span class="sxs-lookup"><span data-stu-id="652f2-334">Start with a `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start(app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="652f2-335">在浏览器中向 `http://localhost:5000` 发出请求，接收响应“Hello World!”</span><span class="sxs-lookup"><span data-stu-id="652f2-335">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="652f2-336">`WaitForShutdown` 受到阻止，直到发出中断（Ctrl-C/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="652f2-336">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="652f2-337">应用显示 `Console.WriteLine` 消息并等待 keypress 退出。</span><span class="sxs-lookup"><span data-stu-id="652f2-337">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="652f2-338">**Start(string url, RequestDelegate app)**</span><span class="sxs-lookup"><span data-stu-id="652f2-338">**Start(string url, RequestDelegate app)**</span></span>

<span data-ttu-id="652f2-339">从 URL 和 `RequestDelegate` 开始：</span><span class="sxs-lookup"><span data-stu-id="652f2-339">Start with a URL and `RequestDelegate`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", app => app.Response.WriteAsync("Hello, World!")))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="652f2-340">生成与 Start(RequestDelegate app) 相同的结果，除非应用在 `http://localhost:8080` 上响应。</span><span class="sxs-lookup"><span data-stu-id="652f2-340">Produces the same result as **Start(RequestDelegate app)**, except the app responds on `http://localhost:8080`.</span></span>

<span data-ttu-id="652f2-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="652f2-341">**Start(Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="652f2-342">使用 `IRouteBuilder` 的实例 ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) 用于路由中间件：</span><span class="sxs-lookup"><span data-stu-id="652f2-342">Use an instance of `IRouteBuilder` ([Microsoft.AspNetCore.Routing](https://www.nuget.org/packages/Microsoft.AspNetCore.Routing/)) to use routing middleware:</span></span>

```csharp
using (var host = WebHost.Start(router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shutdown the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="652f2-343">该示例中使用以下浏览器请求：</span><span class="sxs-lookup"><span data-stu-id="652f2-343">Use the following browser requests with the example:</span></span>

| <span data-ttu-id="652f2-344">请求</span><span class="sxs-lookup"><span data-stu-id="652f2-344">Request</span></span>                                    | <span data-ttu-id="652f2-345">响应</span><span class="sxs-lookup"><span data-stu-id="652f2-345">Response</span></span>                                 |
| ------------------------------------------ | ---------------------------------------- |
| `http://localhost:5000/hello/Martin`       | <span data-ttu-id="652f2-346">Hello, Martin!</span><span class="sxs-lookup"><span data-stu-id="652f2-346">Hello, Martin!</span></span>                           |
| `http://localhost:5000/buenosdias/Catrina` | <span data-ttu-id="652f2-347">Buenos dias, Catrina!</span><span class="sxs-lookup"><span data-stu-id="652f2-347">Buenos dias, Catrina!</span></span>                    |
| `http://localhost:5000/throw/ooops!`       | <span data-ttu-id="652f2-348">使用“ooops!”字符串引发异常</span><span class="sxs-lookup"><span data-stu-id="652f2-348">Throws an exception with string "ooops!"</span></span> |
| `http://localhost:5000/throw`              | <span data-ttu-id="652f2-349">使用“Uh oh!”字符串引发异常</span><span class="sxs-lookup"><span data-stu-id="652f2-349">Throws an exception with string "Uh oh!"</span></span> |
| `http://localhost:5000/Sante/Kevin`        | <span data-ttu-id="652f2-350">Sante, Kevin!</span><span class="sxs-lookup"><span data-stu-id="652f2-350">Sante, Kevin!</span></span>                            |
| `http://localhost:5000`                    | <span data-ttu-id="652f2-351">Hello World!</span><span class="sxs-lookup"><span data-stu-id="652f2-351">Hello World!</span></span>                             |

<span data-ttu-id="652f2-352">`WaitForShutdown` 受到阻止，直到发出中断（Ctrl-C/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="652f2-352">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="652f2-353">应用显示 `Console.WriteLine` 消息并等待 keypress 退出。</span><span class="sxs-lookup"><span data-stu-id="652f2-353">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="652f2-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span><span class="sxs-lookup"><span data-stu-id="652f2-354">**Start(string url, Action\<IRouteBuilder> routeBuilder)**</span></span>

<span data-ttu-id="652f2-355">使用 URL 和 `IRouteBuilder` 实例：</span><span class="sxs-lookup"><span data-stu-id="652f2-355">Use a URL and an instance of `IRouteBuilder`:</span></span>

```csharp
using (var host = WebHost.Start("http://localhost:8080", router => router
    .MapGet("hello/{name}", (req, res, data) => 
        res.WriteAsync($"Hello, {data.Values["name"]}!"))
    .MapGet("buenosdias/{name}", (req, res, data) => 
        res.WriteAsync($"Buenos dias, {data.Values["name"]}!"))
    .MapGet("throw/{message?}", (req, res, data) => 
        throw new Exception((string)data.Values["message"] ?? "Uh oh!"))
    .MapGet("{greeting}/{name}", (req, res, data) => 
        res.WriteAsync($"{data.Values["greeting"]}, {data.Values["name"]}!"))
    .MapGet("", (req, res, data) => res.WriteAsync("Hello, World!"))))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="652f2-356">生成与 Start(Action\<IRouteBuilder> routeBuilder) 相同的结果，除非应用在 `http://localhost:8080` 上响应。</span><span class="sxs-lookup"><span data-stu-id="652f2-356">Produces the same result as **Start(Action\<IRouteBuilder> routeBuilder)**, except the app responds at `http://localhost:8080`.</span></span>

<span data-ttu-id="652f2-357">**StartWith(Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="652f2-357">**StartWith(Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="652f2-358">提供委托以配置 `IApplicationBuilder`：</span><span class="sxs-lookup"><span data-stu-id="652f2-358">Provide a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith(app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="652f2-359">在浏览器中向 `http://localhost:5000` 发出请求，接收响应“Hello World!”</span><span class="sxs-lookup"><span data-stu-id="652f2-359">Make a request in the browser to `http://localhost:5000` to receive the response "Hello World!"</span></span> <span data-ttu-id="652f2-360">`WaitForShutdown` 受到阻止，直到发出中断（Ctrl-C/SIGINT 或 SIGTERM）。</span><span class="sxs-lookup"><span data-stu-id="652f2-360">`WaitForShutdown` blocks until a break (Ctrl-C/SIGINT or SIGTERM) is issued.</span></span> <span data-ttu-id="652f2-361">应用显示 `Console.WriteLine` 消息并等待 keypress 退出。</span><span class="sxs-lookup"><span data-stu-id="652f2-361">The app displays the `Console.WriteLine` message and waits for a keypress to exit.</span></span>

<span data-ttu-id="652f2-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span><span class="sxs-lookup"><span data-stu-id="652f2-362">**StartWith(string url, Action\<IApplicationBuilder> app)**</span></span>

<span data-ttu-id="652f2-363">提供 URL 和委托以配置 `IApplicationBuilder`：</span><span class="sxs-lookup"><span data-stu-id="652f2-363">Provide a URL and a delegate to configure an `IApplicationBuilder`:</span></span>

```csharp
using (var host = WebHost.StartWith("http://localhost:8080", app => 
    app.Use(next => 
    {
        return async context => 
        {
            await context.Response.WriteAsync("Hello World!");
        };
    })))
{
    Console.WriteLine("Use Ctrl-C to shut down the host...");
    host.WaitForShutdown();
}
```

<span data-ttu-id="652f2-364">生成与 StartWith(Action\<IApplicationBuilder> app) 相同的结果，除非应用在 `http://localhost:8080` 上响应。</span><span class="sxs-lookup"><span data-stu-id="652f2-364">Produces the same result as **StartWith(Action\<IApplicationBuilder> app)**, except the app responds on `http://localhost:8080`.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="iwebhostenvironment-interface"></a><span data-ttu-id="652f2-365">IWebHostEnvironment 接口</span><span class="sxs-lookup"><span data-stu-id="652f2-365">IWebHostEnvironment interface</span></span>

<span data-ttu-id="652f2-366">`IWebHostEnvironment` 接口提供有关应用的 Web 托管环境的信息。</span><span class="sxs-lookup"><span data-stu-id="652f2-366">The `IWebHostEnvironment` interface provides information about the app's web hosting environment.</span></span> <span data-ttu-id="652f2-367">使用[构造函数注入](xref:fundamentals/dependency-injection)获取 `IWebHostEnvironment` 以使用其属性和扩展方法：</span><span class="sxs-lookup"><span data-stu-id="652f2-367">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IWebHostEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IWebHostEnvironment _env;

    public CustomFileReader(IWebHostEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="652f2-368">[基于约定的方法](xref:fundamentals/environments#environment-based-startup-class-and-methods)可以用于在启动时基于环境配置应用。</span><span class="sxs-lookup"><span data-stu-id="652f2-368">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="652f2-369">或者，将 `IWebHostEnvironment` 注入到 `Startup` 构造函数用于 `ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="652f2-369">Alternatively, inject the `IWebHostEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IWebHostEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IWebHostEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="652f2-370">除了 `IsDevelopment` 扩展方法，`IWebHostEnvironment` 提供 `IsStaging`、`IsProduction` 和 `IsEnvironment(string environmentName)` 方法。</span><span class="sxs-lookup"><span data-stu-id="652f2-370">In addition to the `IsDevelopment` extension method, `IWebHostEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="652f2-371">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="652f2-371">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="652f2-372">`IWebHostEnvironment` 服务还可以直接注入到 `Configure` 方法以设置处理管道：</span><span class="sxs-lookup"><span data-stu-id="652f2-372">The `IWebHostEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="652f2-373">创建自定义[中间件](xref:fundamentals/middleware/write)时可以将 `IWebHostEnvironment` 注入 `Invoke` 方法：</span><span class="sxs-lookup"><span data-stu-id="652f2-373">`IWebHostEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="ihostingenvironment-interface"></a><span data-ttu-id="652f2-374">IHostingEnvironment 接口</span><span class="sxs-lookup"><span data-stu-id="652f2-374">IHostingEnvironment interface</span></span>

<span data-ttu-id="652f2-375">[IHostingEnvironment 接口](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment)提供有关应用的 Web 承载环境的信息。</span><span class="sxs-lookup"><span data-stu-id="652f2-375">The [IHostingEnvironment interface](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) provides information about the app's web hosting environment.</span></span> <span data-ttu-id="652f2-376">使用[构造函数注入](xref:fundamentals/dependency-injection)获取 `IHostingEnvironment` 以使用其属性和扩展方法：</span><span class="sxs-lookup"><span data-stu-id="652f2-376">Use [constructor injection](xref:fundamentals/dependency-injection) to obtain the `IHostingEnvironment` in order to use its properties and extension methods:</span></span>

```csharp
public class CustomFileReader
{
    private readonly IHostingEnvironment _env;

    public CustomFileReader(IHostingEnvironment env)
    {
        _env = env;
    }

    public string ReadFile(string filePath)
    {
        var fileProvider = _env.WebRootFileProvider;
        // Process the file here
    }
}
```

<span data-ttu-id="652f2-377">[基于约定的方法](xref:fundamentals/environments#environment-based-startup-class-and-methods)可以用于在启动时基于环境配置应用。</span><span class="sxs-lookup"><span data-stu-id="652f2-377">A [convention-based approach](xref:fundamentals/environments#environment-based-startup-class-and-methods) can be used to configure the app at startup based on the environment.</span></span> <span data-ttu-id="652f2-378">或者，将 `IHostingEnvironment` 注入到 `Startup` 构造函数用于 `ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="652f2-378">Alternatively, inject the `IHostingEnvironment` into the `Startup` constructor for use in `ConfigureServices`:</span></span>

```csharp
public class Startup
{
    public Startup(IHostingEnvironment env)
    {
        HostingEnvironment = env;
    }

    public IHostingEnvironment HostingEnvironment { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        if (HostingEnvironment.IsDevelopment())
        {
            // Development configuration
        }
        else
        {
            // Staging/Production configuration
        }

        var contentRootPath = HostingEnvironment.ContentRootPath;
    }
}
```

> [!NOTE]
> <span data-ttu-id="652f2-379">除了 `IsDevelopment` 扩展方法，`IHostingEnvironment` 提供 `IsStaging`、`IsProduction` 和 `IsEnvironment(string environmentName)` 方法。</span><span class="sxs-lookup"><span data-stu-id="652f2-379">In addition to the `IsDevelopment` extension method, `IHostingEnvironment` offers `IsStaging`, `IsProduction`, and `IsEnvironment(string environmentName)` methods.</span></span> <span data-ttu-id="652f2-380">有关详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="652f2-380">For more information, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="652f2-381">`IHostingEnvironment` 服务还可以直接注入到 `Configure` 方法以设置处理管道：</span><span class="sxs-lookup"><span data-stu-id="652f2-381">The `IHostingEnvironment` service can also be injected directly into the `Configure` method for setting up the processing pipeline:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // In Development, use the Developer Exception Page
        app.UseDeveloperExceptionPage();
    }
    else
    {
        // In Staging/Production, route exceptions to /error
        app.UseExceptionHandler("/error");
    }

    var contentRootPath = env.ContentRootPath;
}
```

<span data-ttu-id="652f2-382">创建自定义[中间件](xref:fundamentals/middleware/write)时可以将 `IHostingEnvironment` 注入 `Invoke` 方法：</span><span class="sxs-lookup"><span data-stu-id="652f2-382">`IHostingEnvironment` can be injected into the `Invoke` method when creating custom [middleware](xref:fundamentals/middleware/write):</span></span>

```csharp
public async Task Invoke(HttpContext context, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Configure middleware for Development
    }
    else
    {
        // Configure middleware for Staging/Production
    }

    var contentRootPath = env.ContentRootPath;
}
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="ihostapplicationlifetime-interface"></a><span data-ttu-id="652f2-383">IHostApplicationLifetime 接口</span><span class="sxs-lookup"><span data-stu-id="652f2-383">IHostApplicationLifetime interface</span></span>

<span data-ttu-id="652f2-384">`IHostApplicationLifetime` 允许后启动和关闭活动。</span><span class="sxs-lookup"><span data-stu-id="652f2-384">`IHostApplicationLifetime` allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="652f2-385">接口上的三个属性是用于注册 `Action` 方法（用于定义启动和关闭事件）的取消标记。</span><span class="sxs-lookup"><span data-stu-id="652f2-385">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="652f2-386">取消标记</span><span class="sxs-lookup"><span data-stu-id="652f2-386">Cancellation Token</span></span>    | <span data-ttu-id="652f2-387">触发条件</span><span class="sxs-lookup"><span data-stu-id="652f2-387">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| `ApplicationStarted`  | <span data-ttu-id="652f2-388">主机已完全启动。</span><span class="sxs-lookup"><span data-stu-id="652f2-388">The host has fully started.</span></span> |
| `ApplicationStopped`  | <span data-ttu-id="652f2-389">主机正在完成正常关闭。</span><span class="sxs-lookup"><span data-stu-id="652f2-389">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="652f2-390">应处理所有请求。</span><span class="sxs-lookup"><span data-stu-id="652f2-390">All requests should be processed.</span></span> <span data-ttu-id="652f2-391">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="652f2-391">Shutdown blocks until this event completes.</span></span> |
| `ApplicationStopping` | <span data-ttu-id="652f2-392">主机正在执行正常关闭。</span><span class="sxs-lookup"><span data-stu-id="652f2-392">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="652f2-393">仍在处理请求。</span><span class="sxs-lookup"><span data-stu-id="652f2-393">Requests may still be processing.</span></span> <span data-ttu-id="652f2-394">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="652f2-394">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IHostApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="652f2-395">`StopApplication` 请求终止应用。</span><span class="sxs-lookup"><span data-stu-id="652f2-395">`StopApplication` requests termination of the app.</span></span> <span data-ttu-id="652f2-396">以下类在调用类的 `Shutdown` 方法时使用 `StopApplication` 正常关闭应用：</span><span class="sxs-lookup"><span data-stu-id="652f2-396">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IHostApplicationLifetime _appLifetime;

    public MyClass(IHostApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="iapplicationlifetime-interface"></a><span data-ttu-id="652f2-397">IApplicationLifetime 接口</span><span class="sxs-lookup"><span data-stu-id="652f2-397">IApplicationLifetime interface</span></span>

<span data-ttu-id="652f2-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) 允许后启动和关闭活动。</span><span class="sxs-lookup"><span data-stu-id="652f2-398">[IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) allows for post-startup and shutdown activities.</span></span> <span data-ttu-id="652f2-399">接口上的三个属性是用于注册 `Action` 方法（用于定义启动和关闭事件）的取消标记。</span><span class="sxs-lookup"><span data-stu-id="652f2-399">Three properties on the interface are cancellation tokens used to register `Action` methods that define startup and shutdown events.</span></span>

| <span data-ttu-id="652f2-400">取消标记</span><span class="sxs-lookup"><span data-stu-id="652f2-400">Cancellation Token</span></span>    | <span data-ttu-id="652f2-401">触发条件</span><span class="sxs-lookup"><span data-stu-id="652f2-401">Triggered when&#8230;</span></span> |
| --------------------- | --------------------- |
| [<span data-ttu-id="652f2-402">ApplicationStarted</span><span class="sxs-lookup"><span data-stu-id="652f2-402">ApplicationStarted</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstarted) | <span data-ttu-id="652f2-403">主机已完全启动。</span><span class="sxs-lookup"><span data-stu-id="652f2-403">The host has fully started.</span></span> |
| [<span data-ttu-id="652f2-404">ApplicationStopped</span><span class="sxs-lookup"><span data-stu-id="652f2-404">ApplicationStopped</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopped) | <span data-ttu-id="652f2-405">主机正在完成正常关闭。</span><span class="sxs-lookup"><span data-stu-id="652f2-405">The host is completing a graceful shutdown.</span></span> <span data-ttu-id="652f2-406">应处理所有请求。</span><span class="sxs-lookup"><span data-stu-id="652f2-406">All requests should be processed.</span></span> <span data-ttu-id="652f2-407">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="652f2-407">Shutdown blocks until this event completes.</span></span> |
| [<span data-ttu-id="652f2-408">ApplicationStopping</span><span class="sxs-lookup"><span data-stu-id="652f2-408">ApplicationStopping</span></span>](/dotnet/api/microsoft.extensions.hosting.iapplicationlifetime.applicationstopping) | <span data-ttu-id="652f2-409">主机正在执行正常关闭。</span><span class="sxs-lookup"><span data-stu-id="652f2-409">The host is performing a graceful shutdown.</span></span> <span data-ttu-id="652f2-410">仍在处理请求。</span><span class="sxs-lookup"><span data-stu-id="652f2-410">Requests may still be processing.</span></span> <span data-ttu-id="652f2-411">关闭受到阻止，直到完成此事件。</span><span class="sxs-lookup"><span data-stu-id="652f2-411">Shutdown blocks until this event completes.</span></span> |

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app, IApplicationLifetime appLifetime)
    {
        appLifetime.ApplicationStarted.Register(OnStarted);
        appLifetime.ApplicationStopping.Register(OnStopping);
        appLifetime.ApplicationStopped.Register(OnStopped);

        Console.CancelKeyPress += (sender, eventArgs) =>
        {
            appLifetime.StopApplication();
            // Don't terminate the process immediately, wait for the Main thread to exit gracefully.
            eventArgs.Cancel = true;
        };
    }

    private void OnStarted()
    {
        // Perform post-startup activities here
    }

    private void OnStopping()
    {
        // Perform on-stopping activities here
    }

    private void OnStopped()
    {
        // Perform post-stopped activities here
    }
}
```

<span data-ttu-id="652f2-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) 请求应用终止。</span><span class="sxs-lookup"><span data-stu-id="652f2-412">[StopApplication](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime.stopapplication) requests termination of the app.</span></span> <span data-ttu-id="652f2-413">以下类在调用类的 `Shutdown` 方法时使用 `StopApplication` 正常关闭应用：</span><span class="sxs-lookup"><span data-stu-id="652f2-413">The following class uses `StopApplication` to gracefully shut down an app when the class's `Shutdown` method is called:</span></span>

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

::: moniker-end

## <a name="scope-validation"></a><span data-ttu-id="652f2-414">作用域验证</span><span class="sxs-lookup"><span data-stu-id="652f2-414">Scope validation</span></span>

<span data-ttu-id="652f2-415">如果应用环境为“开发”，则 [CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) 将 [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) 设为 `true`。</span><span class="sxs-lookup"><span data-stu-id="652f2-415">[CreateDefaultBuilder](/dotnet/api/microsoft.aspnetcore.webhost.createdefaultbuilder) sets [ServiceProviderOptions.ValidateScopes](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions.validatescopes) to `true` if the app's environment is Development.</span></span>

<span data-ttu-id="652f2-416">若将 `ValidateScopes` 设为 `true`，默认服务提供程序会执行检查来验证以下内容：</span><span class="sxs-lookup"><span data-stu-id="652f2-416">When `ValidateScopes` is set to `true`, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="652f2-417">没有从根服务提供程序直接或间接解析到有作用域的服务。</span><span class="sxs-lookup"><span data-stu-id="652f2-417">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="652f2-418">未将有作用域的服务直接或间接注入到单一实例。</span><span class="sxs-lookup"><span data-stu-id="652f2-418">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="652f2-419">调用 [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) 时，会创建根服务提供程序。</span><span class="sxs-lookup"><span data-stu-id="652f2-419">The root service provider is created when [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) is called.</span></span> <span data-ttu-id="652f2-420">在启动提供程序和应用时，根服务提供程序的生存期对应于应用/服务的生存期，并在关闭应用时释放。</span><span class="sxs-lookup"><span data-stu-id="652f2-420">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="652f2-421">有作用域的服务由创建它们的容器释放。</span><span class="sxs-lookup"><span data-stu-id="652f2-421">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="652f2-422">如果作用域创建于根容器，则该服务的生存会有效地提升至单一实例，因为根容器只会在应用/服务关闭时将其释放。</span><span class="sxs-lookup"><span data-stu-id="652f2-422">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="652f2-423">验证服务作用域，将在调用 `BuildServiceProvider` 时收集这类情况。</span><span class="sxs-lookup"><span data-stu-id="652f2-423">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="652f2-424">若要始终验证作用域（包括在生存环境中验证），请使用主机生成器上的 [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) 配置 [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions)：</span><span class="sxs-lookup"><span data-stu-id="652f2-424">To always validate scopes, including in the Production environment, configure the [ServiceProviderOptions](/dotnet/api/microsoft.extensions.dependencyinjection.serviceprovideroptions) with [UseDefaultServiceProvider](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.usedefaultserviceprovider) on the host builder:</span></span>

```csharp
WebHost.CreateDefaultBuilder(args)
    .UseDefaultServiceProvider((context, options) => {
        options.ValidateScopes = true;
    })
```

## <a name="additional-resources"></a><span data-ttu-id="652f2-425">其他资源</span><span class="sxs-lookup"><span data-stu-id="652f2-425">Additional resources</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:host-and-deploy/windows-service>
