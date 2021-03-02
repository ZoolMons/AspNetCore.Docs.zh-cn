---
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
ms.openlocfilehash: 5c7412028a81233a0aedacfbe451014eeca4adc1
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552000"
---
<span data-ttu-id="2cc0a-101">ASP.NET Core 具有如下优点：</span><span class="sxs-lookup"><span data-stu-id="2cc0a-101">ASP.NET Core provides the following benefits:</span></span>

* <span data-ttu-id="2cc0a-102">生成 Web UI 和 Web API 的统一场景。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-102">A unified story for building web UI and web APIs.</span></span>
* <span data-ttu-id="2cc0a-103">针对可测试性进行构建。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-103">Architected for testability.</span></span>
* <span data-ttu-id="2cc0a-104">[Razor Pages](xref:razor-pages/index) 可以使基于页面的编码方式更简单高效。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-104">[Razor Pages](xref:razor-pages/index) makes coding page-focused scenarios easier and more productive.</span></span>
* <span data-ttu-id="2cc0a-105">[Blazor](xref:blazor/index) 允许在浏览器中使用 C# 和 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-105">[Blazor](xref:blazor/index) lets you use C# in the browser alongside JavaScript.</span></span> <span data-ttu-id="2cc0a-106">共享全部使用 .NET 编写的服务器端和客户端应用逻辑。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-106">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="2cc0a-107">能够在 Windows、macOS 和 Linux 上进行开发和运行。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-107">Ability to develop and run on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="2cc0a-108">开放源代码和[以社区为中心](https://live.asp.net/)。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-108">Open-source and [community-focused](https://live.asp.net/).</span></span>
* <span data-ttu-id="2cc0a-109">集成[新式客户端框架](xref:blazor/index)和开发工作流。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-109">Integration of [modern, client-side frameworks](xref:blazor/index) and development workflows.</span></span>
* <span data-ttu-id="2cc0a-110">支持使用 [gRPC](xref:grpc/index) 托管远程过程调用 (RPC)。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-110">Support for hosting Remote Procedure Call (RPC) services using [gRPC](xref:grpc/index).</span></span>
* <span data-ttu-id="2cc0a-111">基于环境的云就绪[配置系统](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-111">A cloud-ready, environment-based [configuration system](xref:fundamentals/configuration/index).</span></span>
* <span data-ttu-id="2cc0a-112">内置[依赖项注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-112">Built-in [dependency injection](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="2cc0a-113">轻型的[高性能](https://github.com/aspnet/benchmarks)模块化 HTTP 请求管道。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-113">A lightweight, [high-performance](https://github.com/aspnet/benchmarks), and modular HTTP request pipeline.</span></span>
* <span data-ttu-id="2cc0a-114">能够托管于以下各项：</span><span class="sxs-lookup"><span data-stu-id="2cc0a-114">Ability to host on the following:</span></span>
  * [<span data-ttu-id="2cc0a-115">Kestrel</span><span class="sxs-lookup"><span data-stu-id="2cc0a-115">Kestrel</span></span>](xref:fundamentals/servers/kestrel)
  * [<span data-ttu-id="2cc0a-116">IIS</span><span class="sxs-lookup"><span data-stu-id="2cc0a-116">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="2cc0a-117">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="2cc0a-117">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys)
  * [<span data-ttu-id="2cc0a-118">Nginx</span><span class="sxs-lookup"><span data-stu-id="2cc0a-118">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="2cc0a-119">Apache</span><span class="sxs-lookup"><span data-stu-id="2cc0a-119">Apache</span></span>](xref:host-and-deploy/linux-apache)
  * [<span data-ttu-id="2cc0a-120">Docker</span><span class="sxs-lookup"><span data-stu-id="2cc0a-120">Docker</span></span>](xref:host-and-deploy/docker/index)
* <span data-ttu-id="2cc0a-121">[并行版本控制](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-121">[Side-by-side versioning](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level).</span></span>
* <span data-ttu-id="2cc0a-122">简化新式 Web 开发的工具。</span><span class="sxs-lookup"><span data-stu-id="2cc0a-122">Tooling that simplifies modern web development.</span></span>
