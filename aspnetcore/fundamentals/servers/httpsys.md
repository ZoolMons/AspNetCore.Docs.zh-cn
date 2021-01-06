---
title: ASP.NET Core 中的 HTTP.sys Web 服务器实现
author: rick-anderson
description: 了解 Windows 上适用于 ASP.NET Core 的 Web 服务器 HTTP.sys。 HTTP.sys 构建于 HTTP.sys 内核模式驱动程序之上，是 Kestrel 的一种替代选择，可用来直接连接到 Internet，而无需使用 IIS。
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 9c65abd5a055bb677a14921296316e7e03760bc2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "96855360"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="10ac1-104">ASP.NET Core 中的 HTTP.sys Web 服务器实现</span><span class="sxs-lookup"><span data-stu-id="10ac1-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="10ac1-105">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="10ac1-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="10ac1-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="10ac1-107">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="10ac1-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10ac1-108">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="10ac1-109">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="10ac1-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="10ac1-110">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="10ac1-111">端口共享</span><span class="sxs-lookup"><span data-stu-id="10ac1-111">Port sharing</span></span>
* <span data-ttu-id="10ac1-112">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="10ac1-112">HTTPS with SNI</span></span>
* <span data-ttu-id="10ac1-113">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10ac1-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="10ac1-114">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="10ac1-114">Direct file transmission</span></span>
* <span data-ttu-id="10ac1-115">响应缓存</span><span class="sxs-lookup"><span data-stu-id="10ac1-115">Response caching</span></span>
* <span data-ttu-id="10ac1-116">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10ac1-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="10ac1-117">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="10ac1-117">Supported Windows versions:</span></span>

* <span data-ttu-id="10ac1-118">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-118">Windows 7 or later</span></span>
* <span data-ttu-id="10ac1-119">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="10ac1-120">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10ac1-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="10ac1-121">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-121">When to use HTTP.sys</span></span>

<span data-ttu-id="10ac1-122">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="10ac1-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="10ac1-123">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="10ac1-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="10ac1-125">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="10ac1-127">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="10ac1-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="10ac1-128">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="10ac1-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="10ac1-129">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="10ac1-129">HTTP/2 support</span></span>

<span data-ttu-id="10ac1-130">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="10ac1-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="10ac1-131">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="10ac1-132">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="10ac1-133">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="10ac1-134">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="10ac1-135">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10ac1-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="10ac1-136">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="10ac1-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="10ac1-137">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="10ac1-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="10ac1-138">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="10ac1-139">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10ac1-140">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10ac1-141">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10ac1-142">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="10ac1-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="10ac1-143">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="10ac1-144">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="10ac1-145">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="10ac1-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="10ac1-146">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="10ac1-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="10ac1-147">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="10ac1-148">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="10ac1-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="10ac1-149">Property</span><span class="sxs-lookup"><span data-stu-id="10ac1-149">Property</span></span> | <span data-ttu-id="10ac1-150">描述</span><span class="sxs-lookup"><span data-stu-id="10ac1-150">Description</span></span> | <span data-ttu-id="10ac1-151">默认</span><span class="sxs-lookup"><span data-stu-id="10ac1-151">Default</span></span> |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | <span data-ttu-id="10ac1-152">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="10ac1-152">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="10ac1-153">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="10ac1-153">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="10ac1-154">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="10ac1-154">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="10ac1-155">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="10ac1-155">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="10ac1-156">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="10ac1-156">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="10ac1-157">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="10ac1-157">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="10ac1-158">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="10ac1-158">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | <span data-ttu-id="10ac1-159">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-159">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="10ac1-160">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-160">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="10ac1-161">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-161">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | <span data-ttu-id="10ac1-162">由于限制条件而拒绝请求时的 HTTP.sys 行为。</span><span class="sxs-lookup"><span data-stu-id="10ac1-162">The HTTP.sys behavior when rejecting requests due to throttling conditions.</span></span> | [<span data-ttu-id="10ac1-163">Http503VerbosityLevel.<br>Basic</span><span class="sxs-lookup"><span data-stu-id="10ac1-163">Http503VerbosityLevel.<br>Basic</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="10ac1-164">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="10ac1-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="10ac1-165">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="10ac1-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="10ac1-166">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="10ac1-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="10ac1-167">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="10ac1-167">Use `-1` for infinite.</span></span> <span data-ttu-id="10ac1-168">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="10ac1-169">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="10ac1-169">(machine-wide</span></span><br><span data-ttu-id="10ac1-170">设置）</span><span class="sxs-lookup"><span data-stu-id="10ac1-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="10ac1-171">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="10ac1-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="10ac1-172">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="10ac1-172">30000000 bytes</span></span><br><span data-ttu-id="10ac1-173">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="10ac1-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="10ac1-174">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="10ac1-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="10ac1-175">1000</span><span class="sxs-lookup"><span data-stu-id="10ac1-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="10ac1-176">这指示服务器是否负责创建和配置请求队列，或是否应附加到现有队列。</span><span class="sxs-lookup"><span data-stu-id="10ac1-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="10ac1-177">附加到现有队列时，大多数现有配置选项不适用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="10ac1-178">HTTP.sys 请求队列的名称。</span><span class="sxs-lookup"><span data-stu-id="10ac1-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="10ac1-179">`null`（匿名队列）</span><span class="sxs-lookup"><span data-stu-id="10ac1-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="10ac1-180">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="10ac1-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="10ac1-181">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="10ac1-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="10ac1-182">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="10ac1-183">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="10ac1-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="10ac1-184"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-184"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="10ac1-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="10ac1-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="10ac1-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="10ac1-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="10ac1-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: The minimum send rate for the response.</span></span></li><li><span data-ttu-id="10ac1-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="10ac1-190">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="10ac1-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="10ac1-191">最有用的是 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="10ac1-191">The most useful is <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, which is used to add a prefix to the collection.</span></span> <span data-ttu-id="10ac1-192">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="10ac1-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="10ac1-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="10ac1-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="10ac1-194">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="10ac1-195">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="10ac1-196">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="10ac1-197">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="10ac1-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="10ac1-198">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="10ac1-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="10ac1-199">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="10ac1-200">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="10ac1-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="10ac1-201">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="10ac1-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="10ac1-202">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="10ac1-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="10ac1-203">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="10ac1-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="10ac1-205">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="10ac1-205">Configure Windows Server</span></span>

1. <span data-ttu-id="10ac1-206">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="10ac1-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="10ac1-207">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10ac1-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10ac1-208">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="10ac1-209">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10ac1-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10ac1-210">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="10ac1-211">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="10ac1-212">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="10ac1-213">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="10ac1-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="10ac1-214">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="10ac1-215">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="10ac1-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="10ac1-216">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="10ac1-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="10ac1-217">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="10ac1-218">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="10ac1-218">Install the required .NET Framework.</span></span> <span data-ttu-id="10ac1-219">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="10ac1-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="10ac1-220">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="10ac1-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="10ac1-221">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="10ac1-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="10ac1-222">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="10ac1-223">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10ac1-224">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="10ac1-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="10ac1-225">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="10ac1-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="10ac1-226">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="10ac1-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="10ac1-227">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="10ac1-228">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="10ac1-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="10ac1-229">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="10ac1-230">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="10ac1-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="10ac1-231">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="10ac1-232">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="10ac1-233">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="10ac1-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="10ac1-234">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="10ac1-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="10ac1-235">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="10ac1-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="10ac1-236">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="10ac1-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="10ac1-237">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="10ac1-238">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="10ac1-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="10ac1-239">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="10ac1-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="10ac1-240">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="10ac1-241">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="10ac1-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="10ac1-242">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="10ac1-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="10ac1-243">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="10ac1-244">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10ac1-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="10ac1-245">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="10ac1-246">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="10ac1-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="10ac1-247">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="10ac1-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="10ac1-248">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="10ac1-249">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="10ac1-250">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="10ac1-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="10ac1-251">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="10ac1-252">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="10ac1-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="10ac1-253">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="10ac1-254">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10ac1-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="10ac1-255">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="10ac1-256">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="10ac1-257">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="10ac1-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="10ac1-258">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="10ac1-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="10ac1-259">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="10ac1-260">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-260">In Visual Studio:</span></span>
     * <span data-ttu-id="10ac1-261">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="10ac1-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="10ac1-262">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="10ac1-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="10ac1-263">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="10ac1-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="10ac1-264">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="10ac1-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="10ac1-265">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="10ac1-265">Open the app's project file.</span></span>
     * <span data-ttu-id="10ac1-266">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="10ac1-267">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-267">In the following example:</span></span>

   * <span data-ttu-id="10ac1-268">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="10ac1-269">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="10ac1-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="10ac1-270">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="10ac1-271">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="10ac1-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="10ac1-272">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="10ac1-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="10ac1-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="10ac1-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="10ac1-274">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="10ac1-274">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="10ac1-275">运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-275">Run the app.</span></span>

   <span data-ttu-id="10ac1-276">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="10ac1-277">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="10ac1-278">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="10ac1-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="10ac1-279">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="10ac1-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="10ac1-280">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-280">A development certificate is used in this example.</span></span> <span data-ttu-id="10ac1-281">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="10ac1-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10ac1-283">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="10ac1-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10ac1-284">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="10ac1-285">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="10ac1-286">用于支持 gRPC 的高级 HTTP/2 功能</span><span class="sxs-lookup"><span data-stu-id="10ac1-286">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="10ac1-287">HTTP.sys 中的其他 HTTP/2 功能支持 gRPC，包括对响应尾部和发送重置帧的支持。</span><span class="sxs-lookup"><span data-stu-id="10ac1-287">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="10ac1-288">使用 HTTP.SYS 运行 gRPC 的要求：</span><span class="sxs-lookup"><span data-stu-id="10ac1-288">Requirements to run gRPC with HTTP.SYS:</span></span>

* <span data-ttu-id="10ac1-289">Windows 10，OS 内部版本 19041.508 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-289">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="10ac1-290">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-290">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="10ac1-291">预告片</span><span class="sxs-lookup"><span data-stu-id="10ac1-291">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="10ac1-292">重置</span><span class="sxs-lookup"><span data-stu-id="10ac1-292">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="10ac1-293">其他资源</span><span class="sxs-lookup"><span data-stu-id="10ac1-293">Additional resources</span></span>

* [<span data-ttu-id="10ac1-294">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-294">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="10ac1-295">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="10ac1-295">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="10ac1-296">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="10ac1-296">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="10ac1-297">主机</span><span class="sxs-lookup"><span data-stu-id="10ac1-297">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="10ac1-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="10ac1-299">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="10ac1-299">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10ac1-300">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-300">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="10ac1-301">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="10ac1-301">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="10ac1-302">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-302">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="10ac1-303">端口共享</span><span class="sxs-lookup"><span data-stu-id="10ac1-303">Port sharing</span></span>
* <span data-ttu-id="10ac1-304">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="10ac1-304">HTTPS with SNI</span></span>
* <span data-ttu-id="10ac1-305">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10ac1-305">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="10ac1-306">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="10ac1-306">Direct file transmission</span></span>
* <span data-ttu-id="10ac1-307">响应缓存</span><span class="sxs-lookup"><span data-stu-id="10ac1-307">Response caching</span></span>
* <span data-ttu-id="10ac1-308">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10ac1-308">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="10ac1-309">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="10ac1-309">Supported Windows versions:</span></span>

* <span data-ttu-id="10ac1-310">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-310">Windows 7 or later</span></span>
* <span data-ttu-id="10ac1-311">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-311">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="10ac1-312">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10ac1-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="10ac1-313">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-313">When to use HTTP.sys</span></span>

<span data-ttu-id="10ac1-314">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="10ac1-314">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="10ac1-315">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="10ac1-315">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="10ac1-317">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-317">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="10ac1-319">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="10ac1-319">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="10ac1-320">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="10ac1-320">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="10ac1-321">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="10ac1-321">HTTP/2 support</span></span>

<span data-ttu-id="10ac1-322">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="10ac1-322">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="10ac1-323">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-323">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="10ac1-324">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-324">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="10ac1-325">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-325">TLS 1.2 or later connection</span></span>

<span data-ttu-id="10ac1-326">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-326">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="10ac1-327">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10ac1-327">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="10ac1-328">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="10ac1-328">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="10ac1-329">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="10ac1-329">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="10ac1-330">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-330">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="10ac1-331">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-331">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10ac1-332">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-332">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10ac1-333">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-333">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10ac1-334">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="10ac1-334">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="10ac1-335">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-335">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="10ac1-336">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-336">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="10ac1-337">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="10ac1-337">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="10ac1-338">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="10ac1-338">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="10ac1-339">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-339">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="10ac1-340">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="10ac1-340">**HTTP.sys options**</span></span>

| <span data-ttu-id="10ac1-341">Property</span><span class="sxs-lookup"><span data-stu-id="10ac1-341">Property</span></span> | <span data-ttu-id="10ac1-342">描述</span><span class="sxs-lookup"><span data-stu-id="10ac1-342">Description</span></span> | <span data-ttu-id="10ac1-343">默认</span><span class="sxs-lookup"><span data-stu-id="10ac1-343">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="10ac1-344">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="10ac1-344">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="10ac1-345">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="10ac1-345">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="10ac1-346">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="10ac1-346">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="10ac1-347">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="10ac1-347">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="10ac1-348">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="10ac1-348">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="10ac1-349">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="10ac1-349">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="10ac1-350">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="10ac1-350">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="10ac1-351">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="10ac1-351">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="10ac1-352">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="10ac1-352">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="10ac1-353">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-353">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="10ac1-354">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-354">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="10ac1-355">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-355">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="10ac1-356">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="10ac1-356">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="10ac1-357">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="10ac1-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="10ac1-358">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="10ac1-358">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="10ac1-359">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="10ac1-359">Use `-1` for infinite.</span></span> <span data-ttu-id="10ac1-360">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-360">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="10ac1-361">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="10ac1-361">(machine-wide</span></span><br><span data-ttu-id="10ac1-362">设置）</span><span class="sxs-lookup"><span data-stu-id="10ac1-362">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="10ac1-363">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="10ac1-363">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="10ac1-364">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="10ac1-364">30000000 bytes</span></span><br><span data-ttu-id="10ac1-365">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="10ac1-365">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="10ac1-366">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="10ac1-366">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="10ac1-367">1000</span><span class="sxs-lookup"><span data-stu-id="10ac1-367">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="10ac1-368">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="10ac1-368">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="10ac1-369">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="10ac1-369">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="10ac1-370">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-370">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="10ac1-371">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="10ac1-371">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="10ac1-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="10ac1-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="10ac1-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="10ac1-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="10ac1-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="10ac1-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="10ac1-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="10ac1-378">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="10ac1-378">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="10ac1-379">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="10ac1-379">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="10ac1-380">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="10ac1-380">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="10ac1-381">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="10ac1-381">**MaxRequestBodySize**</span></span>

<span data-ttu-id="10ac1-382">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-382">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="10ac1-383">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-383">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="10ac1-384">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-384">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="10ac1-385">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="10ac1-385">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="10ac1-386">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="10ac1-386">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="10ac1-387">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-387">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="10ac1-388">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="10ac1-388">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="10ac1-389">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="10ac1-389">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="10ac1-390">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="10ac1-390">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="10ac1-391">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="10ac1-391">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="10ac1-393">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="10ac1-393">Configure Windows Server</span></span>

1. <span data-ttu-id="10ac1-394">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="10ac1-394">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="10ac1-395">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10ac1-395">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10ac1-396">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-396">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="10ac1-397">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10ac1-397">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10ac1-398">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-398">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="10ac1-399">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-399">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="10ac1-400">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-400">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="10ac1-401">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="10ac1-401">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="10ac1-402">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-402">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="10ac1-403">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="10ac1-403">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="10ac1-404">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="10ac1-404">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="10ac1-405">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-405">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="10ac1-406">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="10ac1-406">Install the required .NET Framework.</span></span> <span data-ttu-id="10ac1-407">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="10ac1-407">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="10ac1-408">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="10ac1-408">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="10ac1-409">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="10ac1-409">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="10ac1-410">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-410">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="10ac1-411">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-411">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10ac1-412">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="10ac1-412">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="10ac1-413">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="10ac1-413">`urls` command-line argument</span></span>
   * <span data-ttu-id="10ac1-414">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="10ac1-414">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="10ac1-415">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-415">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="10ac1-416">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="10ac1-416">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="10ac1-417">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-417">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="10ac1-418">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="10ac1-418">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="10ac1-419">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-419">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="10ac1-420">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-420">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="10ac1-421">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="10ac1-421">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="10ac1-422">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="10ac1-422">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="10ac1-423">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="10ac1-423">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="10ac1-424">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="10ac1-424">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="10ac1-425">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-425">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="10ac1-426">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="10ac1-426">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="10ac1-427">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="10ac1-427">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="10ac1-428">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-428">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="10ac1-429">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="10ac1-429">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="10ac1-430">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="10ac1-430">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="10ac1-431">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-431">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="10ac1-432">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10ac1-432">Don't use a wildcard binding.</span></span> <span data-ttu-id="10ac1-433">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-433">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="10ac1-434">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="10ac1-434">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="10ac1-435">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="10ac1-435">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="10ac1-436">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-436">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="10ac1-437">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-437">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="10ac1-438">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="10ac1-438">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="10ac1-439">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-439">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="10ac1-440">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="10ac1-440">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="10ac1-441">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-441">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="10ac1-442">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10ac1-442">Don't use a wildcard binding.</span></span> <span data-ttu-id="10ac1-443">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-443">Use a valid IP address.</span></span>
   * <span data-ttu-id="10ac1-444">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-444">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="10ac1-445">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="10ac1-445">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="10ac1-446">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="10ac1-446">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="10ac1-447">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-447">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="10ac1-448">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-448">In Visual Studio:</span></span>
     * <span data-ttu-id="10ac1-449">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="10ac1-449">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="10ac1-450">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="10ac1-450">Select the **Package** tab.</span></span>
     * <span data-ttu-id="10ac1-451">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="10ac1-451">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="10ac1-452">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="10ac1-452">When not using Visual Studio:</span></span>
     * <span data-ttu-id="10ac1-453">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="10ac1-453">Open the app's project file.</span></span>
     * <span data-ttu-id="10ac1-454">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-454">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="10ac1-455">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-455">In the following example:</span></span>

   * <span data-ttu-id="10ac1-456">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-456">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="10ac1-457">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="10ac1-457">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="10ac1-458">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-458">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="10ac1-459">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="10ac1-459">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="10ac1-460">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="10ac1-460">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="10ac1-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="10ac1-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="10ac1-462">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="10ac1-462">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="10ac1-463">运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-463">Run the app.</span></span>

   <span data-ttu-id="10ac1-464">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-464">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="10ac1-465">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-465">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="10ac1-466">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="10ac1-466">The app responds at the server's public IP address.</span></span> <span data-ttu-id="10ac1-467">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="10ac1-467">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="10ac1-468">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-468">A development certificate is used in this example.</span></span> <span data-ttu-id="10ac1-469">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="10ac1-469">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10ac1-471">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="10ac1-471">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10ac1-472">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-472">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="10ac1-473">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-473">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10ac1-474">其他资源</span><span class="sxs-lookup"><span data-stu-id="10ac1-474">Additional resources</span></span>

* [<span data-ttu-id="10ac1-475">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-475">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="10ac1-476">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="10ac1-476">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="10ac1-477">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="10ac1-477">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="10ac1-478">主机</span><span class="sxs-lookup"><span data-stu-id="10ac1-478">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="10ac1-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="10ac1-480">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="10ac1-480">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10ac1-481">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-481">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="10ac1-482">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="10ac1-482">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="10ac1-483">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-483">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="10ac1-484">端口共享</span><span class="sxs-lookup"><span data-stu-id="10ac1-484">Port sharing</span></span>
* <span data-ttu-id="10ac1-485">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="10ac1-485">HTTPS with SNI</span></span>
* <span data-ttu-id="10ac1-486">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10ac1-486">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="10ac1-487">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="10ac1-487">Direct file transmission</span></span>
* <span data-ttu-id="10ac1-488">响应缓存</span><span class="sxs-lookup"><span data-stu-id="10ac1-488">Response caching</span></span>
* <span data-ttu-id="10ac1-489">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10ac1-489">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="10ac1-490">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="10ac1-490">Supported Windows versions:</span></span>

* <span data-ttu-id="10ac1-491">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-491">Windows 7 or later</span></span>
* <span data-ttu-id="10ac1-492">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-492">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="10ac1-493">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10ac1-493">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="10ac1-494">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-494">When to use HTTP.sys</span></span>

<span data-ttu-id="10ac1-495">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="10ac1-495">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="10ac1-496">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="10ac1-496">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="10ac1-498">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-498">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="10ac1-500">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="10ac1-500">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="10ac1-501">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="10ac1-501">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="10ac1-502">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="10ac1-502">HTTP/2 support</span></span>

<span data-ttu-id="10ac1-503">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="10ac1-503">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="10ac1-504">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-504">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="10ac1-505">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-505">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="10ac1-506">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-506">TLS 1.2 or later connection</span></span>

<span data-ttu-id="10ac1-507">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-507">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="10ac1-508">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10ac1-508">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="10ac1-509">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="10ac1-509">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="10ac1-510">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="10ac1-510">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="10ac1-511">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-511">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="10ac1-512">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-512">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10ac1-513">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-513">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10ac1-514">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-514">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10ac1-515">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="10ac1-515">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="10ac1-516">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-516">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="10ac1-517">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-517">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="10ac1-518">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-518">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="10ac1-519">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-519">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="10ac1-520">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="10ac1-520">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="10ac1-521">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="10ac1-521">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="10ac1-522">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-522">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="10ac1-523">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="10ac1-523">**HTTP.sys options**</span></span>

| <span data-ttu-id="10ac1-524">Property</span><span class="sxs-lookup"><span data-stu-id="10ac1-524">Property</span></span> | <span data-ttu-id="10ac1-525">描述</span><span class="sxs-lookup"><span data-stu-id="10ac1-525">Description</span></span> | <span data-ttu-id="10ac1-526">默认</span><span class="sxs-lookup"><span data-stu-id="10ac1-526">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="10ac1-527">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="10ac1-527">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="10ac1-528">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="10ac1-528">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="10ac1-529">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="10ac1-529">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="10ac1-530">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="10ac1-530">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="10ac1-531">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="10ac1-531">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="10ac1-532">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="10ac1-532">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="10ac1-533">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="10ac1-533">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="10ac1-534">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="10ac1-534">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="10ac1-535">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="10ac1-535">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="10ac1-536">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-536">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="10ac1-537">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-537">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="10ac1-538">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-538">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="10ac1-539">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="10ac1-539">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="10ac1-540">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="10ac1-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="10ac1-541">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="10ac1-541">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="10ac1-542">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="10ac1-542">Use `-1` for infinite.</span></span> <span data-ttu-id="10ac1-543">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-543">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="10ac1-544">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="10ac1-544">(machine-wide</span></span><br><span data-ttu-id="10ac1-545">设置）</span><span class="sxs-lookup"><span data-stu-id="10ac1-545">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="10ac1-546">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="10ac1-546">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="10ac1-547">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="10ac1-547">30000000 bytes</span></span><br><span data-ttu-id="10ac1-548">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="10ac1-548">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="10ac1-549">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="10ac1-549">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="10ac1-550">1000</span><span class="sxs-lookup"><span data-stu-id="10ac1-550">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="10ac1-551">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="10ac1-551">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="10ac1-552">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="10ac1-552">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="10ac1-553">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-553">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="10ac1-554">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="10ac1-554">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="10ac1-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="10ac1-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="10ac1-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="10ac1-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="10ac1-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="10ac1-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="10ac1-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="10ac1-561">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="10ac1-561">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="10ac1-562">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="10ac1-562">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="10ac1-563">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="10ac1-563">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="10ac1-564">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="10ac1-564">**MaxRequestBodySize**</span></span>

<span data-ttu-id="10ac1-565">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-565">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="10ac1-566">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-566">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="10ac1-567">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-567">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="10ac1-568">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="10ac1-568">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="10ac1-569">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="10ac1-569">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="10ac1-570">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-570">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="10ac1-571">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="10ac1-571">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="10ac1-572">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="10ac1-572">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="10ac1-573">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="10ac1-573">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="10ac1-574">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="10ac1-574">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="10ac1-576">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="10ac1-576">Configure Windows Server</span></span>

1. <span data-ttu-id="10ac1-577">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="10ac1-577">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="10ac1-578">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10ac1-578">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10ac1-579">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-579">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="10ac1-580">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10ac1-580">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10ac1-581">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-581">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="10ac1-582">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-582">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="10ac1-583">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-583">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="10ac1-584">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="10ac1-584">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="10ac1-585">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-585">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="10ac1-586">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="10ac1-586">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="10ac1-587">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="10ac1-587">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="10ac1-588">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-588">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="10ac1-589">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="10ac1-589">Install the required .NET Framework.</span></span> <span data-ttu-id="10ac1-590">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="10ac1-590">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="10ac1-591">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="10ac1-591">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="10ac1-592">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="10ac1-592">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="10ac1-593">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-593">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="10ac1-594">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-594">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10ac1-595">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="10ac1-595">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="10ac1-596">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="10ac1-596">`urls` command-line argument</span></span>
   * <span data-ttu-id="10ac1-597">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="10ac1-597">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="10ac1-598">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-598">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="10ac1-599">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="10ac1-599">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="10ac1-600">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-600">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="10ac1-601">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="10ac1-601">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="10ac1-602">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-602">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="10ac1-603">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-603">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="10ac1-604">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="10ac1-604">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="10ac1-605">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="10ac1-605">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="10ac1-606">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="10ac1-606">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="10ac1-607">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="10ac1-607">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="10ac1-608">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-608">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="10ac1-609">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="10ac1-609">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="10ac1-610">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="10ac1-610">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="10ac1-611">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-611">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="10ac1-612">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="10ac1-612">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="10ac1-613">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="10ac1-613">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="10ac1-614">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-614">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="10ac1-615">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10ac1-615">Don't use a wildcard binding.</span></span> <span data-ttu-id="10ac1-616">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-616">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="10ac1-617">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="10ac1-617">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="10ac1-618">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="10ac1-618">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="10ac1-619">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-619">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="10ac1-620">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-620">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="10ac1-621">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="10ac1-621">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="10ac1-622">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-622">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="10ac1-623">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="10ac1-623">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="10ac1-624">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-624">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="10ac1-625">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10ac1-625">Don't use a wildcard binding.</span></span> <span data-ttu-id="10ac1-626">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-626">Use a valid IP address.</span></span>
   * <span data-ttu-id="10ac1-627">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-627">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="10ac1-628">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="10ac1-628">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="10ac1-629">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="10ac1-629">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="10ac1-630">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-630">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="10ac1-631">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-631">In Visual Studio:</span></span>
     * <span data-ttu-id="10ac1-632">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="10ac1-632">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="10ac1-633">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="10ac1-633">Select the **Package** tab.</span></span>
     * <span data-ttu-id="10ac1-634">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="10ac1-634">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="10ac1-635">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="10ac1-635">When not using Visual Studio:</span></span>
     * <span data-ttu-id="10ac1-636">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="10ac1-636">Open the app's project file.</span></span>
     * <span data-ttu-id="10ac1-637">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-637">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="10ac1-638">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-638">In the following example:</span></span>

   * <span data-ttu-id="10ac1-639">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-639">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="10ac1-640">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="10ac1-640">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="10ac1-641">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-641">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="10ac1-642">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="10ac1-642">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="10ac1-643">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="10ac1-643">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="10ac1-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="10ac1-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="10ac1-645">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="10ac1-645">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="10ac1-646">运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-646">Run the app.</span></span>

   <span data-ttu-id="10ac1-647">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-647">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="10ac1-648">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-648">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="10ac1-649">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="10ac1-649">The app responds at the server's public IP address.</span></span> <span data-ttu-id="10ac1-650">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="10ac1-650">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="10ac1-651">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-651">A development certificate is used in this example.</span></span> <span data-ttu-id="10ac1-652">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="10ac1-652">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10ac1-654">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="10ac1-654">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10ac1-655">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-655">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="10ac1-656">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-656">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10ac1-657">其他资源</span><span class="sxs-lookup"><span data-stu-id="10ac1-657">Additional resources</span></span>

* [<span data-ttu-id="10ac1-658">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-658">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="10ac1-659">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="10ac1-659">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="10ac1-660">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="10ac1-660">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="10ac1-661">主机</span><span class="sxs-lookup"><span data-stu-id="10ac1-661">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="10ac1-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) 是仅在 Windows 上运行的[适用于 ASP.NET Core 的 Web 服务器](xref:fundamentals/servers/index)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="10ac1-663">HTTP.sys 是 [Kestrel](xref:fundamentals/servers/kestrel) 服务器的替代选择，提供了一些 Kestrel 不提供的功能。</span><span class="sxs-lookup"><span data-stu-id="10ac1-663">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="10ac1-664">HTTP.sys 与 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)不兼容，无法与 IIS 或 IIS Express 结合使用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-664">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="10ac1-665">HTTP.sys 支持以下功能：</span><span class="sxs-lookup"><span data-stu-id="10ac1-665">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="10ac1-666">Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-666">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="10ac1-667">端口共享</span><span class="sxs-lookup"><span data-stu-id="10ac1-667">Port sharing</span></span>
* <span data-ttu-id="10ac1-668">具有 SNI 的 HTTPS</span><span class="sxs-lookup"><span data-stu-id="10ac1-668">HTTPS with SNI</span></span>
* <span data-ttu-id="10ac1-669">基于 TLS 的 HTTP/2（Windows 10 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10ac1-669">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="10ac1-670">直接文件传输</span><span class="sxs-lookup"><span data-stu-id="10ac1-670">Direct file transmission</span></span>
* <span data-ttu-id="10ac1-671">响应缓存</span><span class="sxs-lookup"><span data-stu-id="10ac1-671">Response caching</span></span>
* <span data-ttu-id="10ac1-672">WebSocket（Windows 8 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="10ac1-672">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="10ac1-673">受支持的 Windows 版本：</span><span class="sxs-lookup"><span data-stu-id="10ac1-673">Supported Windows versions:</span></span>

* <span data-ttu-id="10ac1-674">Windows 7 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-674">Windows 7 or later</span></span>
* <span data-ttu-id="10ac1-675">Windows Server 2008 R2 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-675">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="10ac1-676">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="10ac1-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="10ac1-677">何时使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-677">When to use HTTP.sys</span></span>

<span data-ttu-id="10ac1-678">HTTP.sys 对于以下情形的部署来说很有用：</span><span class="sxs-lookup"><span data-stu-id="10ac1-678">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="10ac1-679">需要将服务器直接公开到 Internet 而不使用 IIS 的部署。</span><span class="sxs-lookup"><span data-stu-id="10ac1-679">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys 直接与 Internet 进行通信](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="10ac1-681">内部部署需要 Kestrel 中没有的功能，如 [Windows 身份验证](xref:security/authentication/windowsauth)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-681">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys 直接与内部网络进行通信](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="10ac1-683">HTTP.sys 是一项成熟的技术，可以抵御多种攻击，并提供可靠、安全、可伸缩的全功能 Web 服务器。</span><span class="sxs-lookup"><span data-stu-id="10ac1-683">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="10ac1-684">IIS 本身作为 HTTP.sys 之上的 HTTP 侦听器运行。</span><span class="sxs-lookup"><span data-stu-id="10ac1-684">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="10ac1-685">HTTP/2 支持</span><span class="sxs-lookup"><span data-stu-id="10ac1-685">HTTP/2 support</span></span>

<span data-ttu-id="10ac1-686">如果满足以下基本要求，将为 ASP.NET Core 应用启用 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：</span><span class="sxs-lookup"><span data-stu-id="10ac1-686">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="10ac1-687">Windows Server 2016/Windows 10 或更高版本</span><span class="sxs-lookup"><span data-stu-id="10ac1-687">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="10ac1-688">[应用程序层协议协商 (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) 连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-688">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="10ac1-689">TLS 1.2 或更高版本的连接</span><span class="sxs-lookup"><span data-stu-id="10ac1-689">TLS 1.2 or later connection</span></span>

<span data-ttu-id="10ac1-690">如果已建立 HTTP/2 连接，[HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 会报告 `HTTP/1.1`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-690">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="10ac1-691">默认情况下将启用 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="10ac1-691">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="10ac1-692">如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。</span><span class="sxs-lookup"><span data-stu-id="10ac1-692">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="10ac1-693">在 Windows 的未来版本中，将提供 HTTP/2 配置标志，包括使用 HTTP.sys 禁用 HTTP/2 的功能。</span><span class="sxs-lookup"><span data-stu-id="10ac1-693">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="10ac1-694">对 Kerberos 进行内核模式身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-694">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="10ac1-695">HTTP.sys 通过 Kerberos 身份验证协议委托给内核模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-695">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="10ac1-696">Kerberos 和 HTTP.sys 不支持用户模式身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-696">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="10ac1-697">必须使用计算机帐户来解密从 Active Directory 获取的并由客户端转发到服务器的 Kerberos 令牌/票证，以便对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="10ac1-697">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="10ac1-698">注册主机的服务主体名称 (SPN)，而不是应用的用户。</span><span class="sxs-lookup"><span data-stu-id="10ac1-698">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="10ac1-699">如何使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-699">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="10ac1-700">配置 ASP.NET Core 应用以使用 HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="10ac1-700">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="10ac1-701">使用 [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) 时，不需要项目文件中的包引用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-701">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="10ac1-702">未使用 `Microsoft.AspNetCore.App` 元包时，向 [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) 添加包引用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-702">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="10ac1-703">生成主机时调用 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> 扩展方法，指定所有必需的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>。</span><span class="sxs-lookup"><span data-stu-id="10ac1-703">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="10ac1-704">以下示例将选项设置为其默认值：</span><span class="sxs-lookup"><span data-stu-id="10ac1-704">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="10ac1-705">通过[注册表设置](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)处理其他 HTTP.sys 配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-705">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="10ac1-706">**HTTP.sys 选项**</span><span class="sxs-lookup"><span data-stu-id="10ac1-706">**HTTP.sys options**</span></span>

| <span data-ttu-id="10ac1-707">Property</span><span class="sxs-lookup"><span data-stu-id="10ac1-707">Property</span></span> | <span data-ttu-id="10ac1-708">描述</span><span class="sxs-lookup"><span data-stu-id="10ac1-708">Description</span></span> | <span data-ttu-id="10ac1-709">默认</span><span class="sxs-lookup"><span data-stu-id="10ac1-709">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="10ac1-710">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="10ac1-710">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="10ac1-711">控制是否允许 `HttpContext.Request.Body` 和 `HttpContext.Response.Body` 的同步输入/输出。</span><span class="sxs-lookup"><span data-stu-id="10ac1-711">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="10ac1-712">Authentication.AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="10ac1-712">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="10ac1-713">允许匿名请求。</span><span class="sxs-lookup"><span data-stu-id="10ac1-713">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="10ac1-714">Authentication.Schemes</span><span class="sxs-lookup"><span data-stu-id="10ac1-714">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="10ac1-715">指定允许的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="10ac1-715">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="10ac1-716">可能在处理侦听器之前随时修改。</span><span class="sxs-lookup"><span data-stu-id="10ac1-716">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="10ac1-717">通过 [AuthenticationSchemes 枚举](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)`Basic`、`Kerberos`、`Negotiate`、`None` 和 `NTLM` 提供值。</span><span class="sxs-lookup"><span data-stu-id="10ac1-717">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="10ac1-718">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="10ac1-718">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="10ac1-719">尝试[内核模式](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode)缓存，响应合格的标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-719">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="10ac1-720">该响应可能不包括 `Set-Cookie`、`Vary` 或 `Pragma` 标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-720">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="10ac1-721">它必须包括属性为 `public` 的 `Cache-Control` 标头和 `shared-max-age` 或 `max-age` 值，或 `Expires` 标头。</span><span class="sxs-lookup"><span data-stu-id="10ac1-721">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="10ac1-722">最大并发接受数量。</span><span class="sxs-lookup"><span data-stu-id="10ac1-722">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="10ac1-723">5 &times; [环境。<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="10ac1-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="10ac1-724">要接受的最大并发连接数。</span><span class="sxs-lookup"><span data-stu-id="10ac1-724">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="10ac1-725">使用 `-1` 实现无限。</span><span class="sxs-lookup"><span data-stu-id="10ac1-725">Use `-1` for infinite.</span></span> <span data-ttu-id="10ac1-726">通过 `null` 使用注册表的计算机范围内的设置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-726">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="10ac1-727">（计算机范围内的</span><span class="sxs-lookup"><span data-stu-id="10ac1-727">(machine-wide</span></span><br><span data-ttu-id="10ac1-728">设置）</span><span class="sxs-lookup"><span data-stu-id="10ac1-728">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="10ac1-729">请参阅 <a href="#maxrequestbodysize">MaxRequestBodySize</a> 部分。</span><span class="sxs-lookup"><span data-stu-id="10ac1-729">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="10ac1-730">30000000 个字节</span><span class="sxs-lookup"><span data-stu-id="10ac1-730">30000000 bytes</span></span><br><span data-ttu-id="10ac1-731">(~28.6 MB)</span><span class="sxs-lookup"><span data-stu-id="10ac1-731">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="10ac1-732">队列中允许的最大请求数。</span><span class="sxs-lookup"><span data-stu-id="10ac1-732">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="10ac1-733">1000</span><span class="sxs-lookup"><span data-stu-id="10ac1-733">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="10ac1-734">指示由于客户端断开连接而失败的响应主体写入应引发异常还是正常完成。</span><span class="sxs-lookup"><span data-stu-id="10ac1-734">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="10ac1-735">（正常完成）</span><span class="sxs-lookup"><span data-stu-id="10ac1-735">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="10ac1-736">公开 HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> 配置，也可以在注册表中进行配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-736">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="10ac1-737">请访问 API 链接详细了解每个设置，包括默认值：</span><span class="sxs-lookup"><span data-stu-id="10ac1-737">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="10ac1-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody)：允许 HTTP 服务器 API 在保持活动的连接上排出实体正文的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="10ac1-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody)：允许请求实体正文到达的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="10ac1-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait)：允许 HTTP 服务器 API 分析请求头的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="10ac1-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection)：允许空闲连接的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="10ac1-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond)：响应的最小发送速率。</span><span class="sxs-lookup"><span data-stu-id="10ac1-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="10ac1-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue)：在应用选取请求前，允许请求在请求队列中停留的时间。</span><span class="sxs-lookup"><span data-stu-id="10ac1-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="10ac1-744">指定要向 HTTP.sys 注册的 <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>。</span><span class="sxs-lookup"><span data-stu-id="10ac1-744">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="10ac1-745">最有用的是 [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*)，它用于将前缀添加到集合中。</span><span class="sxs-lookup"><span data-stu-id="10ac1-745">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="10ac1-746">可能在处理侦听器之前随时对这些设置进行修改。</span><span class="sxs-lookup"><span data-stu-id="10ac1-746">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="10ac1-747">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="10ac1-747">**MaxRequestBodySize**</span></span>

<span data-ttu-id="10ac1-748">允许的请求正文的最大大小（以字节计）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-748">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="10ac1-749">当设置为 `null` 时，最大请求正文大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-749">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="10ac1-750">此限制不会影响升级后的连接，这始终不受限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-750">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="10ac1-751">在 ASP.NET Core MVC 应用中为单个 `IActionResult` 替代限制的推荐方法是在操作方法上使用 <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> 属性：</span><span class="sxs-lookup"><span data-stu-id="10ac1-751">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="10ac1-752">如果在应用开始读取请求后尝试配置请求限制，则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="10ac1-752">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="10ac1-753">`IsReadOnly` 属性可用于指示 `MaxRequestBodySize` 属性是否处于只读状态。只读状态意味着已经太迟了，无法配置限制。</span><span class="sxs-lookup"><span data-stu-id="10ac1-753">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="10ac1-754">如果应用应替代每个请求的 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize>，请使用 <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>：</span><span class="sxs-lookup"><span data-stu-id="10ac1-754">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="10ac1-755">如果使用的是 Visual Studio，请确保应用未经配置以运行 IIS 或 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="10ac1-755">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="10ac1-756">在 Visual Studio 中，默认启动配置文件是针对 IIS Express 的。</span><span class="sxs-lookup"><span data-stu-id="10ac1-756">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="10ac1-757">若要作为控制台应用运行该项目，请手动更改所选配置文件，如以下屏幕截图中所示：</span><span class="sxs-lookup"><span data-stu-id="10ac1-757">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![选择控制台应用配置文件](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="10ac1-759">配置 Windows Server</span><span class="sxs-lookup"><span data-stu-id="10ac1-759">Configure Windows Server</span></span>

1. <span data-ttu-id="10ac1-760">确定要为应用打开的端口，并使用 [Windows 防火墙](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule)或 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 打开防火墙端口，以允许流量到达 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="10ac1-760">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="10ac1-761">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10ac1-761">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10ac1-762">在部署到 Azure VM 时，在[网络安全组](/azure/virtual-machines/windows/nsg-quickstart-portal)中打开端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-762">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="10ac1-763">在以下命令和应用配置中，使用的是端口 443。</span><span class="sxs-lookup"><span data-stu-id="10ac1-763">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="10ac1-764">如果需要，获取并安装 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-764">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="10ac1-765">在 Windows 上，可使用 [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate) 创建自签名证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-765">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="10ac1-766">有关不支持的示例，请参阅 [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-766">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="10ac1-767">在服务器的“本地计算机”>“个人”存储中，安装自签名证书或 CA 签名证书 。</span><span class="sxs-lookup"><span data-stu-id="10ac1-767">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="10ac1-768">如果应用为[框架相关部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)，则安装 .NET Core、.NET Framework 或两者（如果应用是面向 .NET Framework 的 .NET Core 应用）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-768">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="10ac1-769">**.NET Core**：如果应用需要 .NET Core，请从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取并运行 .NET Core 运行时安装程序。</span><span class="sxs-lookup"><span data-stu-id="10ac1-769">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="10ac1-770">请勿在服务器上安装完整 SDK。</span><span class="sxs-lookup"><span data-stu-id="10ac1-770">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="10ac1-771">**.NET Framework**：如果应用需要 .NET Framework，请参阅 [.NET Framework 安装指南](/dotnet/framework/install/)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-771">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="10ac1-772">安装所需的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="10ac1-772">Install the required .NET Framework.</span></span> <span data-ttu-id="10ac1-773">可以从 [.NET Core 下载](https://dotnet.microsoft.com/download)页获取最新 .NET Framework 的安装程序。</span><span class="sxs-lookup"><span data-stu-id="10ac1-773">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="10ac1-774">如果应用是[独立式部署](/dotnet/core/deploying/#self-contained-deployments-scd)，应用在部署中包含运行时。</span><span class="sxs-lookup"><span data-stu-id="10ac1-774">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="10ac1-775">无需在服务器上安装任何框架。</span><span class="sxs-lookup"><span data-stu-id="10ac1-775">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="10ac1-776">在应用中配置 URL 和端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-776">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="10ac1-777">默认情况下，ASP.NET Core 绑定到 `http://localhost:5000`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-777">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="10ac1-778">若要配置 URL 前缀和端口，可采用以下方法：</span><span class="sxs-lookup"><span data-stu-id="10ac1-778">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="10ac1-779">`urls` 命令行参数</span><span class="sxs-lookup"><span data-stu-id="10ac1-779">`urls` command-line argument</span></span>
   * <span data-ttu-id="10ac1-780">`ASPNETCORE_URLS` 环境变量</span><span class="sxs-lookup"><span data-stu-id="10ac1-780">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="10ac1-781">下面的代码示例展示了如何对端口 443 结合使用 <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> 和服务器的本地 IP 地址 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-781">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="10ac1-782">`UrlPrefixes` 的一个优点是会为格式不正确的前缀立即生成一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="10ac1-782">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="10ac1-783">`UrlPrefixes` 中的设置替代 `UseUrls`/`urls`/`ASPNETCORE_URLS` 设置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-783">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="10ac1-784">因此，`UseUrls`、`urls` 和 `ASPNETCORE_URLS` 环境变量的一个优点是在 Kestrel 和 HTTP.sys 之间切换变得更加容易。</span><span class="sxs-lookup"><span data-stu-id="10ac1-784">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="10ac1-785">HTTP.sys 使用 [HTTP 服务器 API UrlPrefix 字符串格式](/windows/win32/http/urlprefix-strings)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-785">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="10ac1-786">不应使用顶级通配符绑定（`http://*:80/` 和 `http://+:80`）。</span><span class="sxs-lookup"><span data-stu-id="10ac1-786">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="10ac1-787">顶级通配符绑定会带来应用安全漏洞。</span><span class="sxs-lookup"><span data-stu-id="10ac1-787">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="10ac1-788">此行为同时适用于强通配符和弱通配符。</span><span class="sxs-lookup"><span data-stu-id="10ac1-788">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="10ac1-789">请使用显式主机名或 IP 地址，而不是通配符。</span><span class="sxs-lookup"><span data-stu-id="10ac1-789">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="10ac1-790">如果可控制整个父域（相对于易受攻击的 `*.com`），子域通配符绑定（例如，`*.mysub.com`）不会构成安全风险。</span><span class="sxs-lookup"><span data-stu-id="10ac1-790">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="10ac1-791">有关详细信息，请参阅 [RFC 7230：第 5.4 节：主机](https://tools.ietf.org/html/rfc7230#section-5.4)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-791">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="10ac1-792">在服务器上预注册 URL 前缀。</span><span class="sxs-lookup"><span data-stu-id="10ac1-792">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="10ac1-793">用于配置 HTTP.sys 的内置工具为 *netsh.exe*。</span><span class="sxs-lookup"><span data-stu-id="10ac1-793">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="10ac1-794">*netsh.exe* 用于保留 URL 前缀并分配 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-794">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="10ac1-795">此工具需要管理员特权。</span><span class="sxs-lookup"><span data-stu-id="10ac1-795">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="10ac1-796">使用 netsh.exe 工具为应用注册 URL：</span><span class="sxs-lookup"><span data-stu-id="10ac1-796">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="10ac1-797">`<URL>`：完全限定的统一资源定位器 (URL)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-797">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="10ac1-798">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10ac1-798">Don't use a wildcard binding.</span></span> <span data-ttu-id="10ac1-799">请使用有效主机名或本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-799">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="10ac1-800">URL 必须包含尾部反斜杠。</span><span class="sxs-lookup"><span data-stu-id="10ac1-800">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="10ac1-801">`<USER>`：指定用户名或用户组名称。</span><span class="sxs-lookup"><span data-stu-id="10ac1-801">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="10ac1-802">在以下示例中，服务器的本地 IP 地址是 `10.0.0.4`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-802">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="10ac1-803">在 URL 注册后，工具响应返回 `URL reservation successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-803">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="10ac1-804">若要删除已注册的 URL，请使用 `delete urlacl` 命令：</span><span class="sxs-lookup"><span data-stu-id="10ac1-804">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="10ac1-805">在服务器上注册 X.509 证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-805">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="10ac1-806">使用 netsh.exe 工具为应用注册证书：</span><span class="sxs-lookup"><span data-stu-id="10ac1-806">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="10ac1-807">`<IP>`：指定绑定的本地 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-807">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="10ac1-808">不要使用通配符绑定。</span><span class="sxs-lookup"><span data-stu-id="10ac1-808">Don't use a wildcard binding.</span></span> <span data-ttu-id="10ac1-809">请使用有效 IP 地址。</span><span class="sxs-lookup"><span data-stu-id="10ac1-809">Use a valid IP address.</span></span>
   * <span data-ttu-id="10ac1-810">`<PORT>`：指定绑定的端口。</span><span class="sxs-lookup"><span data-stu-id="10ac1-810">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="10ac1-811">`<THUMBPRINT>`：X.509 证书指纹。</span><span class="sxs-lookup"><span data-stu-id="10ac1-811">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="10ac1-812">`<GUID>`：开发人员生成的表示应用的 GUID，以供参考。</span><span class="sxs-lookup"><span data-stu-id="10ac1-812">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="10ac1-813">为了便于参考，将 GUID 作为包标记存储在应用中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-813">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="10ac1-814">在 Visual Studio 中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-814">In Visual Studio:</span></span>
     * <span data-ttu-id="10ac1-815">在“解决方案资源管理器”中，右键单击应用，并选择“属性”，以打开应用的项目属性。</span><span class="sxs-lookup"><span data-stu-id="10ac1-815">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="10ac1-816">选择“包”选项卡。</span><span class="sxs-lookup"><span data-stu-id="10ac1-816">Select the **Package** tab.</span></span>
     * <span data-ttu-id="10ac1-817">在“标记”字段中输入已创建的 GUID。</span><span class="sxs-lookup"><span data-stu-id="10ac1-817">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="10ac1-818">如果使用的不是 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="10ac1-818">When not using Visual Studio:</span></span>
     * <span data-ttu-id="10ac1-819">打开应用的项目文件。</span><span class="sxs-lookup"><span data-stu-id="10ac1-819">Open the app's project file.</span></span>
     * <span data-ttu-id="10ac1-820">使用已创建的 GUID，将 `<PackageTags>` 属性添加到新的或现有的 `<PropertyGroup>`：</span><span class="sxs-lookup"><span data-stu-id="10ac1-820">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="10ac1-821">如下示例中：</span><span class="sxs-lookup"><span data-stu-id="10ac1-821">In the following example:</span></span>

   * <span data-ttu-id="10ac1-822">服务器的本地 IP 地址是 `10.0.0.4`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-822">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="10ac1-823">联机随机 GUID 生成器提供 `appid` 值。</span><span class="sxs-lookup"><span data-stu-id="10ac1-823">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="10ac1-824">在证书注册后，工具响应返回 `SSL Certificate successfully added`。</span><span class="sxs-lookup"><span data-stu-id="10ac1-824">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="10ac1-825">若要删除证书注册，请使用 `delete sslcert` 命令：</span><span class="sxs-lookup"><span data-stu-id="10ac1-825">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="10ac1-826">*netsh.exe* 的参考文档：</span><span class="sxs-lookup"><span data-stu-id="10ac1-826">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="10ac1-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))（超文本传输协议 (HTTP) 的 Netsh 命令）</span><span class="sxs-lookup"><span data-stu-id="10ac1-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * <span data-ttu-id="10ac1-828">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)（UrlPrefix 字符串）</span><span class="sxs-lookup"><span data-stu-id="10ac1-828">[UrlPrefix Strings](/windows/win32/http/urlprefix-strings)</span></span>

1. <span data-ttu-id="10ac1-829">运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-829">Run the app.</span></span>

   <span data-ttu-id="10ac1-830">结合使用 HTTP（而不是 HTTPS）和大于 1024 的端口号绑定到 localhost，无需管理员权限，即可运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-830">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="10ac1-831">对于其他配置（例如，使用本地 IP 地址或绑定到端口 443），必须有管理员权限才能运行应用。</span><span class="sxs-lookup"><span data-stu-id="10ac1-831">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="10ac1-832">应用在服务器的公共 IP 地址处响应。</span><span class="sxs-lookup"><span data-stu-id="10ac1-832">The app responds at the server's public IP address.</span></span> <span data-ttu-id="10ac1-833">此示例在 Internet 上的公共 IP 地址 `104.214.79.47` 处访问服务器。</span><span class="sxs-lookup"><span data-stu-id="10ac1-833">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="10ac1-834">此示例使用的是开发证书。</span><span class="sxs-lookup"><span data-stu-id="10ac1-834">A development certificate is used in this example.</span></span> <span data-ttu-id="10ac1-835">在绕过浏览器的不受信任证书警告后，页面安全加载。</span><span class="sxs-lookup"><span data-stu-id="10ac1-835">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![显示应用索引页已加载的浏览器窗口](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="10ac1-837">代理服务器和负载均衡器方案</span><span class="sxs-lookup"><span data-stu-id="10ac1-837">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="10ac1-838">如果应用由 HTTP.sys 托管并且与来自 Internet 或公司网络的请求进行交互，当在代理服务器和负载均衡器后托管时，可能需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="10ac1-838">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="10ac1-839">有关详细信息，请参阅[配置 ASP.NET Core 以使用代理服务器和负载均衡器](xref:host-and-deploy/proxy-load-balancer)。</span><span class="sxs-lookup"><span data-stu-id="10ac1-839">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="10ac1-840">其他资源</span><span class="sxs-lookup"><span data-stu-id="10ac1-840">Additional resources</span></span>

* [<span data-ttu-id="10ac1-841">使用 HTTP.sys 启用 Windows 身份验证</span><span class="sxs-lookup"><span data-stu-id="10ac1-841">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="10ac1-842">HTTP 服务器 API</span><span class="sxs-lookup"><span data-stu-id="10ac1-842">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="10ac1-843">aspnet/HttpSysServer GitHub 存储库（源代码）</span><span class="sxs-lookup"><span data-stu-id="10ac1-843">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="10ac1-844">主机</span><span class="sxs-lookup"><span data-stu-id="10ac1-844">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
