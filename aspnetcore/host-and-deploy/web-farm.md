---
title: 在 Web 场中托管 ASP.NET Core
author: rick-anderson
description: 了解如何在 Web 场环境中托管包含共享资源的 ASP.NET Core 应用的多个实例。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/web-farm
ms.openlocfilehash: ee78e80a4eda3089943765700aa6bb62c6c1e07d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057513"
---
# <a name="host-aspnet-core-in-a-web-farm"></a><span data-ttu-id="513ce-103">在 Web 场中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="513ce-103">Host ASP.NET Core in a web farm</span></span>

<span data-ttu-id="513ce-104">作者：[Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="513ce-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="513ce-105">Web 场包含两个或多个 Web 服务器（亦称为“节点”），用于托管应用的多个实例。</span><span class="sxs-lookup"><span data-stu-id="513ce-105">A *web farm* is a group of two or more web servers (or *nodes*) that host multiple instances of an app.</span></span> <span data-ttu-id="513ce-106">若有用户请求到达 Web 场，负载均衡器会将请求分发到 Web 场中的各个节点。</span><span class="sxs-lookup"><span data-stu-id="513ce-106">When requests from users arrive to a web farm, a *load balancer* distributes the requests to the web farm's nodes.</span></span> <span data-ttu-id="513ce-107">Web 场提高了：</span><span class="sxs-lookup"><span data-stu-id="513ce-107">Web farms improve:</span></span>

* <span data-ttu-id="513ce-108">可靠性/可用性：如果一个或多个节点失败，负载均衡器可以将请求路由到其他正常运行的节点，以继续处理请求。</span><span class="sxs-lookup"><span data-stu-id="513ce-108">**Reliability/availability**: When one or more nodes fail, the load balancer can route requests to other functioning nodes to continue processing requests.</span></span>
* <span data-ttu-id="513ce-109">容量/性能：多个节点可以处理的请求数多于一个服务器。</span><span class="sxs-lookup"><span data-stu-id="513ce-109">**Capacity/performance**: Multiple nodes can process more requests than a single server.</span></span> <span data-ttu-id="513ce-110">负载均衡器均衡工作负载的方式是，将请求分发到各个节点。</span><span class="sxs-lookup"><span data-stu-id="513ce-110">The load balancer balances the workload by distributing requests to the nodes.</span></span>
* <span data-ttu-id="513ce-111">**可伸缩性**：如果需要更多或更少容量，可以增加或减少活动节点数，与工作负载保持一致。</span><span class="sxs-lookup"><span data-stu-id="513ce-111">**Scalability**: When more or less capacity is required, the number of active nodes can be increased or decreased to match the workload.</span></span> <span data-ttu-id="513ce-112">[Azure 应用服务](https://azure.microsoft.com/services/app-service/)等 Web 场平台技术可以应系统管理员的请求自动添加或删除节点，也可以自动开始，而无需人为干预。</span><span class="sxs-lookup"><span data-stu-id="513ce-112">Web farm platform technologies, such as [Azure App Service](https://azure.microsoft.com/services/app-service/), can automatically add or remove nodes at the request of the system administrator or automatically without human intervention.</span></span>
* <span data-ttu-id="513ce-113">可维护性：Web 场节点可以依赖一组共享服务，这就简化了系统管理。</span><span class="sxs-lookup"><span data-stu-id="513ce-113">**Maintainability**: Nodes of a web farm can rely on a set of shared services, which results in easier system management.</span></span> <span data-ttu-id="513ce-114">例如，Web 场中的节点可以依赖单一数据库服务器，以及静态资源（如图像和可下载文件）的公用网络位置。</span><span class="sxs-lookup"><span data-stu-id="513ce-114">For example, the nodes of a web farm can rely upon a single database server and a common network location for static resources, such as images and downloadable files.</span></span>

<span data-ttu-id="513ce-115">本主题介绍了在 Web 场中托管且依赖共享资源的 ASP.NET Core 应用的配置和依赖项。</span><span class="sxs-lookup"><span data-stu-id="513ce-115">This topic describes configuration and dependencies for ASP.NET core apps hosted in a web farm that rely upon shared resources.</span></span>

## <a name="general-configuration"></a><span data-ttu-id="513ce-116">常规配置</span><span class="sxs-lookup"><span data-stu-id="513ce-116">General configuration</span></span>

<xref:host-and-deploy/index>  
<span data-ttu-id="513ce-117">了解如何设置托管环境和部署 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="513ce-117">Learn how to set up hosting environments and deploy ASP.NET Core apps.</span></span> <span data-ttu-id="513ce-118">对 Web 场中的每个节点配置进程管理器，以自动启动和重启应用。</span><span class="sxs-lookup"><span data-stu-id="513ce-118">Configure a process manager on each node of the web farm to automate app starts and restarts.</span></span> <span data-ttu-id="513ce-119">每个节点都需要 ASP.NET Core 运行时。</span><span class="sxs-lookup"><span data-stu-id="513ce-119">Each node requires the ASP.NET Core runtime.</span></span> <span data-ttu-id="513ce-120">有关详细信息，请参阅文档的[托管和部署](xref:host-and-deploy/index)区域中的主题。</span><span class="sxs-lookup"><span data-stu-id="513ce-120">For more information, see the topics in the [Host and deploy](xref:host-and-deploy/index) area of the documentation.</span></span>

<xref:host-and-deploy/proxy-load-balancer>  
<span data-ttu-id="513ce-121">了解在代理服务器和负载均衡器后方托管的应用程序的配置，这通常会隐藏重要的请求信息。</span><span class="sxs-lookup"><span data-stu-id="513ce-121">Learn about configuration for apps hosted behind proxy servers and load balancers, which often obscure important request information.</span></span>

<xref:host-and-deploy/azure-apps/index>  
<span data-ttu-id="513ce-122">[Azure 应用服务](https://azure.microsoft.com/services/app-service/)是一个用于托管 Web 应用（包括 ASP.NET Core）的 [Microsoft 云计算平台服务](https://azure.microsoft.com/)。</span><span class="sxs-lookup"><span data-stu-id="513ce-122">[Azure App Service](https://azure.microsoft.com/services/app-service/) is a [Microsoft cloud computing platform service](https://azure.microsoft.com/) for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="513ce-123">应用服务是提供自动缩放、负载均衡、修补和持续部署的完全托管平台。</span><span class="sxs-lookup"><span data-stu-id="513ce-123">App Service is a fully managed platform that provides automatic scaling, load balancing, patching, and continuous deployment.</span></span>

## <a name="app-data"></a><span data-ttu-id="513ce-124">应用数据</span><span class="sxs-lookup"><span data-stu-id="513ce-124">App data</span></span>

<span data-ttu-id="513ce-125">如果应用已缩放为多个实例，可能会有需要跨节点共享的应用状态。</span><span class="sxs-lookup"><span data-stu-id="513ce-125">When an app is scaled to multiple instances, there might be app state that requires sharing across nodes.</span></span> <span data-ttu-id="513ce-126">若为暂时状态，建议共享 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache)。</span><span class="sxs-lookup"><span data-stu-id="513ce-126">If the state is transient, consider sharing an [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache).</span></span> <span data-ttu-id="513ce-127">如果需要暂留共享状态，建议在数据库中存储共享状态。</span><span class="sxs-lookup"><span data-stu-id="513ce-127">If the shared state requires persistence, consider storing the shared state in a database.</span></span>

## <a name="required-configuration"></a><span data-ttu-id="513ce-128">必需配置</span><span class="sxs-lookup"><span data-stu-id="513ce-128">Required configuration</span></span>

<span data-ttu-id="513ce-129">必需为部署到 Web 场的应用配置数据保护和缓存。</span><span class="sxs-lookup"><span data-stu-id="513ce-129">Data Protection and Caching require configuration for apps deployed to a web farm.</span></span>

### <a name="data-protection"></a><span data-ttu-id="513ce-130">数据保护</span><span class="sxs-lookup"><span data-stu-id="513ce-130">Data Protection</span></span>

<span data-ttu-id="513ce-131">应用使用 [ASP.NET Core 数据保护系统](xref:security/data-protection/introduction)来保护数据。</span><span class="sxs-lookup"><span data-stu-id="513ce-131">The [ASP.NET Core Data Protection system](xref:security/data-protection/introduction) is used by apps to protect data.</span></span> <span data-ttu-id="513ce-132">数据保护系统依赖一组在密钥环中存储的加密密钥。</span><span class="sxs-lookup"><span data-stu-id="513ce-132">Data Protection relies upon a set of cryptographic keys stored in a *key ring*.</span></span> <span data-ttu-id="513ce-133">初始化后，数据保护系统会应用在本地存储密钥环的[默认设置](xref:security/data-protection/configuration/default-settings)。</span><span class="sxs-lookup"><span data-stu-id="513ce-133">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) that store the key ring locally.</span></span> <span data-ttu-id="513ce-134">根据默认配置，唯一密钥环存储在 Web 场的各个节点上。</span><span class="sxs-lookup"><span data-stu-id="513ce-134">Under the default configuration, a unique key ring is stored on each node of the web farm.</span></span> <span data-ttu-id="513ce-135">因此，Web 场中的每个节点都无法解密应用在其他任何节点上加密的数据。</span><span class="sxs-lookup"><span data-stu-id="513ce-135">Consequently, each web farm node can't decrypt data that's encrypted by an app on any other node.</span></span> <span data-ttu-id="513ce-136">默认配置通常不适合在 Web 场中托管应用。</span><span class="sxs-lookup"><span data-stu-id="513ce-136">The default configuration isn't generally appropriate for hosting apps in a web farm.</span></span> <span data-ttu-id="513ce-137">若要实现共享密钥环，可以改为始终将用户请求路由到相同的节点。</span><span class="sxs-lookup"><span data-stu-id="513ce-137">An alternative to implementing a shared key ring is to always route user requests to the same node.</span></span> <span data-ttu-id="513ce-138">若要详细了解与 Web 场部署有关的数据保护系统配置，请参阅<xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="513ce-138">For more information on Data Protection system configuration for web farm deployments, see <xref:security/data-protection/configuration/overview>.</span></span>

### <a name="caching"></a><span data-ttu-id="513ce-139">缓存</span><span class="sxs-lookup"><span data-stu-id="513ce-139">Caching</span></span>

<span data-ttu-id="513ce-140">在 Web 场环境中，缓存机制必须跨 Web 场中的节点共享缓存项。</span><span class="sxs-lookup"><span data-stu-id="513ce-140">In a web farm environment, the caching mechanism must share cached items across the web farm's nodes.</span></span> <span data-ttu-id="513ce-141">缓存必须依赖公用 Redis 缓存、共享 SQL Server 数据库，或跨 Web 场共享缓存项的自定义缓存实现。</span><span class="sxs-lookup"><span data-stu-id="513ce-141">Caching must either rely upon a common Redis cache, a shared SQL Server database, or a custom caching implementation that shares cached items across the web farm.</span></span> <span data-ttu-id="513ce-142">有关详细信息，请参阅 <xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="513ce-142">For more information, see <xref:performance/caching/distributed>.</span></span>

## <a name="dependent-components"></a><span data-ttu-id="513ce-143">依赖组件</span><span class="sxs-lookup"><span data-stu-id="513ce-143">Dependent components</span></span>

<span data-ttu-id="513ce-144">下面的方案无需其他配置，但依赖需要配置 Web 场的技术。</span><span class="sxs-lookup"><span data-stu-id="513ce-144">The following scenarios don't require additional configuration, but they depend on technologies that require configuration for web farms.</span></span>

| <span data-ttu-id="513ce-145">方案</span><span class="sxs-lookup"><span data-stu-id="513ce-145">Scenario</span></span> | <span data-ttu-id="513ce-146">依赖&hellip;</span><span class="sxs-lookup"><span data-stu-id="513ce-146">Depends on &hellip;</span></span> |
| -------- | ------------------- |
| <span data-ttu-id="513ce-147">身份验证</span><span class="sxs-lookup"><span data-stu-id="513ce-147">Authentication</span></span> | <span data-ttu-id="513ce-148">数据保护（请参阅<xref:security/data-protection/configuration/overview>）。</span><span class="sxs-lookup"><span data-stu-id="513ce-148">Data Protection (see <xref:security/data-protection/configuration/overview>).</span></span><br><br><span data-ttu-id="513ce-149">有关详细信息，请参阅 <xref:security/authentication/cookie> 和 <xref:security/cookie-sharing>。</span><span class="sxs-lookup"><span data-stu-id="513ce-149">For more information, see <xref:security/authentication/cookie> and <xref:security/cookie-sharing>.</span></span> |
| Identity | <span data-ttu-id="513ce-150">身份验证和数据库配置。</span><span class="sxs-lookup"><span data-stu-id="513ce-150">Authentication and database configuration.</span></span><br><br><span data-ttu-id="513ce-151">有关详细信息，请参阅 <xref:security/authentication/identity>。</span><span class="sxs-lookup"><span data-stu-id="513ce-151">For more information, see <xref:security/authentication/identity>.</span></span> |
| <span data-ttu-id="513ce-152">会话</span><span class="sxs-lookup"><span data-stu-id="513ce-152">Session</span></span> | <span data-ttu-id="513ce-153">数据保护（加密 cookie）（请参阅<xref:security/data-protection/configuration/overview>）和缓存（请参阅 <xref:performance/caching/distributed>）。</span><span class="sxs-lookup"><span data-stu-id="513ce-153">Data Protection (encrypted cookies) (see <xref:security/data-protection/configuration/overview>) and Caching (see <xref:performance/caching/distributed>).</span></span><br><br><span data-ttu-id="513ce-154">有关详细信息，请参阅[会话和状态管理：会话状态](xref:fundamentals/app-state#session-state)。</span><span class="sxs-lookup"><span data-stu-id="513ce-154">For more information, see [Session and state management: Session state](xref:fundamentals/app-state#session-state).</span></span> |
| <span data-ttu-id="513ce-155">TempData</span><span class="sxs-lookup"><span data-stu-id="513ce-155">TempData</span></span> | <span data-ttu-id="513ce-156">数据保护（加密 cookie）（请参阅<xref:security/data-protection/configuration/overview>）或会话（请参阅[会话和状态管理：会话状态](xref:fundamentals/app-state#session-state)）。</span><span class="sxs-lookup"><span data-stu-id="513ce-156">Data Protection (encrypted cookies) (see <xref:security/data-protection/configuration/overview>) or Session (see [Session and state management: Session state](xref:fundamentals/app-state#session-state)).</span></span><br><br><span data-ttu-id="513ce-157">有关详细信息，请参阅[会话和状态管理：TempData](xref:fundamentals/app-state#tempdata)。</span><span class="sxs-lookup"><span data-stu-id="513ce-157">For more information, see [Session and state management: TempData](xref:fundamentals/app-state#tempdata).</span></span> |
| <span data-ttu-id="513ce-158">防伪造</span><span class="sxs-lookup"><span data-stu-id="513ce-158">Anti-forgery</span></span> | <span data-ttu-id="513ce-159">数据保护（请参阅<xref:security/data-protection/configuration/overview>）。</span><span class="sxs-lookup"><span data-stu-id="513ce-159">Data Protection (see <xref:security/data-protection/configuration/overview>).</span></span><br><br><span data-ttu-id="513ce-160">有关详细信息，请参阅 <xref:security/anti-request-forgery>。</span><span class="sxs-lookup"><span data-stu-id="513ce-160">For more information, see <xref:security/anti-request-forgery>.</span></span> |

## <a name="troubleshoot"></a><span data-ttu-id="513ce-161">疑难解答</span><span class="sxs-lookup"><span data-stu-id="513ce-161">Troubleshoot</span></span>

### <a name="data-protection-and-caching"></a><span data-ttu-id="513ce-162">数据保护和缓存</span><span class="sxs-lookup"><span data-stu-id="513ce-162">Data Protection and caching</span></span>

<span data-ttu-id="513ce-163">如果未为 Web 场环境配置数据保护或缓存，就会在处理请求时发生间歇性错误。</span><span class="sxs-lookup"><span data-stu-id="513ce-163">When Data Protection or caching isn't configured for a web farm environment, intermittent errors occur when requests are processed.</span></span> <span data-ttu-id="513ce-164">之所以会发生这种情况是因为，节点不共享相同的资源，并且用户请求并不总是路由回同一节点。</span><span class="sxs-lookup"><span data-stu-id="513ce-164">This occurs because nodes don't share the same resources and user requests aren't always routed back to the same node.</span></span>

<span data-ttu-id="513ce-165">假设用户通过 cookie 身份验证来登录应用。</span><span class="sxs-lookup"><span data-stu-id="513ce-165">Consider a user who signs into the app using cookie authentication.</span></span> <span data-ttu-id="513ce-166">用户在 Web 场中的一个节点上登录应用。</span><span class="sxs-lookup"><span data-stu-id="513ce-166">The user signs into the app on one web farm node.</span></span> <span data-ttu-id="513ce-167">如果用户的下一个请求到达登录应用时所用的同一节点，应用便能解密身份验证 cookie，并允许用户访问应用资源。</span><span class="sxs-lookup"><span data-stu-id="513ce-167">If their next request arrives at the same node where they signed in, the app is able to decrypt the authentication cookie and allows access to the app's resource.</span></span> <span data-ttu-id="513ce-168">如果用户的下一个请求到达其他节点，应用便无法从用户登录时所用的节点解密身份验证 cookie，并且无法授权用户请求获取的资源。</span><span class="sxs-lookup"><span data-stu-id="513ce-168">If their next request arrives at a different node, the app can't decrypt the authentication cookie from the node where the user signed in, and authorization for the requested resource fails.</span></span>

<span data-ttu-id="513ce-169">如果以下任一症状间歇性出现，问题原因通常是为 Web 场环境配置的数据保护或缓存不正确：</span><span class="sxs-lookup"><span data-stu-id="513ce-169">When any of the following symptoms occur **intermittently**, the problem is usually traced to improper Data Protection or caching configuration for a web farm environment:</span></span>

* <span data-ttu-id="513ce-170">身份验证中断：身份验证 cookie 配置不正确或无法解密。</span><span class="sxs-lookup"><span data-stu-id="513ce-170">Authentication breaks: The authentication cookie is misconfigured or can't be decrypted.</span></span> <span data-ttu-id="513ce-171">OAuth（Facebook、Microsoft、Twitter）或 OpenIdConnect 登录失败，出现错误“关联失败”。</span><span class="sxs-lookup"><span data-stu-id="513ce-171">OAuth (Facebook, Microsoft, Twitter) or OpenIdConnect logins fail with the error "Correlation failed."</span></span>
* <span data-ttu-id="513ce-172">授权中断：Identity 丢失。</span><span class="sxs-lookup"><span data-stu-id="513ce-172">Authorization breaks: Identity is lost.</span></span>
* <span data-ttu-id="513ce-173">会话状态丢失数据。</span><span class="sxs-lookup"><span data-stu-id="513ce-173">Session state loses data.</span></span>
* <span data-ttu-id="513ce-174">缓存项消失。</span><span class="sxs-lookup"><span data-stu-id="513ce-174">Cached items disappear.</span></span>
* <span data-ttu-id="513ce-175">TempData 失败。</span><span class="sxs-lookup"><span data-stu-id="513ce-175">TempData fails.</span></span>
* <span data-ttu-id="513ce-176">POST 失败：防伪造检查失败。</span><span class="sxs-lookup"><span data-stu-id="513ce-176">POSTs fail: The anti-forgery check fails.</span></span>

<span data-ttu-id="513ce-177">若要详细了解与 Web 场部署有关的数据保护配置，请参阅<xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="513ce-177">For more information on Data Protection configuration for web farm deployments, see <xref:security/data-protection/configuration/overview>.</span></span> <span data-ttu-id="513ce-178">若要详细了解与 Web 场部署有关的缓存配置，请参阅<xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="513ce-178">For more information on caching configuration for web farm deployments, see <xref:performance/caching/distributed>.</span></span>

## <a name="obtain-data-from-apps"></a><span data-ttu-id="513ce-179">从应用中获取数据</span><span class="sxs-lookup"><span data-stu-id="513ce-179">Obtain data from apps</span></span>

<span data-ttu-id="513ce-180">如果 Web 场应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。</span><span class="sxs-lookup"><span data-stu-id="513ce-180">If the web farm apps are capable of responding to requests, obtain request, connection, and additional data from the apps using terminal inline middleware.</span></span> <span data-ttu-id="513ce-181">有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。</span><span class="sxs-lookup"><span data-stu-id="513ce-181">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="513ce-182">其他资源</span><span class="sxs-lookup"><span data-stu-id="513ce-182">Additional resources</span></span>

* <span data-ttu-id="513ce-183">[适用于 Windows 的自定义脚本扩展](/azure/virtual-machines/extensions/custom-script-windows)：在 Azure 虚拟机上下载和执行脚本，这对于部署后配置和软件安装很有用。</span><span class="sxs-lookup"><span data-stu-id="513ce-183">[Custom Script Extension for Windows](/azure/virtual-machines/extensions/custom-script-windows): Downloads and executes scripts on Azure virtual machines, which is useful for post-deployment configuration and software installation.</span></span>
* <xref:host-and-deploy/proxy-load-balancer>
 