---
title: GRPC 性能最佳做法
author: jamesnk
description: 了解生成高性能 gRPC 服务的最佳做法。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 09d959b7fee0b431bb36d5449402a442b2f453ae
ms.sourcegitcommit: 68df2a4d236f9f3299622ed38c75bb51cbdb4856
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107225535"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="99df6-103">GRPC 性能最佳做法</span><span class="sxs-lookup"><span data-stu-id="99df6-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="99df6-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="99df6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="99df6-105">gRPC 专用于高性能服务。</span><span class="sxs-lookup"><span data-stu-id="99df6-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="99df6-106">本文档介绍如何从 gRPC 获得最佳性能。</span><span class="sxs-lookup"><span data-stu-id="99df6-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="99df6-107">重用 gRPC 通道</span><span class="sxs-lookup"><span data-stu-id="99df6-107">Reuse gRPC channels</span></span>

<span data-ttu-id="99df6-108">进行 gRPC 调用时，应重新使用 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="99df6-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="99df6-109">重用通道后通过现有的 HTTP/2 连接对调用进行多路复用。</span><span class="sxs-lookup"><span data-stu-id="99df6-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="99df6-110">如果为每个 gRPC 调用创建一个新通道，则完成此操作所需的时间可能会显著增加。</span><span class="sxs-lookup"><span data-stu-id="99df6-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="99df6-111">每次调用都需要在客户端和服务器之间进行多个网络往返，以创建新的 HTTP/2 连接：</span><span class="sxs-lookup"><span data-stu-id="99df6-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="99df6-112">打开套接字</span><span class="sxs-lookup"><span data-stu-id="99df6-112">Opening a socket</span></span>
2. <span data-ttu-id="99df6-113">建立 TCP 连接</span><span class="sxs-lookup"><span data-stu-id="99df6-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="99df6-114">协商 TLS</span><span class="sxs-lookup"><span data-stu-id="99df6-114">Negotiating TLS</span></span>
4. <span data-ttu-id="99df6-115">启动 HTTP/2 连接</span><span class="sxs-lookup"><span data-stu-id="99df6-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="99df6-116">进行 gRPC 调用</span><span class="sxs-lookup"><span data-stu-id="99df6-116">Making the gRPC call</span></span>

<span data-ttu-id="99df6-117">在 gRPC 调用之间可以安全地共享和重用通道：</span><span class="sxs-lookup"><span data-stu-id="99df6-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="99df6-118">gRPC 客户端是使用通道创建的。</span><span class="sxs-lookup"><span data-stu-id="99df6-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="99df6-119">gRPC 客户端是轻型对象，无需缓存或重用。</span><span class="sxs-lookup"><span data-stu-id="99df6-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="99df6-120">可从一个通道创建多个 gRPC 客户端（包括不同类型的客户端）。</span><span class="sxs-lookup"><span data-stu-id="99df6-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="99df6-121">通道和从该通道创建的客户端可由多个线程安全使用。</span><span class="sxs-lookup"><span data-stu-id="99df6-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="99df6-122">从通道创建的客户端可同时进行多个调用。</span><span class="sxs-lookup"><span data-stu-id="99df6-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="99df6-123">GRPC 客户端工厂提供了一种集中配置通道的方法。</span><span class="sxs-lookup"><span data-stu-id="99df6-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="99df6-124">它会自动重用基础通道。</span><span class="sxs-lookup"><span data-stu-id="99df6-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="99df6-125">有关详细信息，请参阅 <xref:grpc/clientfactory>。</span><span class="sxs-lookup"><span data-stu-id="99df6-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="99df6-126">连接并发</span><span class="sxs-lookup"><span data-stu-id="99df6-126">Connection concurrency</span></span>

<span data-ttu-id="99df6-127">HTTP/2 连接通常会限制一个连接上同时存在的[最大并发流（活动 HTTP 请求）数](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2)。</span><span class="sxs-lookup"><span data-stu-id="99df6-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="99df6-128">默认情况下，大多数服务器将此限制设置为 100 个并发流。</span><span class="sxs-lookup"><span data-stu-id="99df6-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="99df6-129">gRPC 通道使用单个 HTTP/2 连接，并且并发调用在该连接上多路复用。</span><span class="sxs-lookup"><span data-stu-id="99df6-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="99df6-130">当活动调用数达到连接流限制时，其他调用会在客户端中排队。</span><span class="sxs-lookup"><span data-stu-id="99df6-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="99df6-131">排队调用等待活动调用完成后再发送。</span><span class="sxs-lookup"><span data-stu-id="99df6-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="99df6-132">由于此限制，具有高负载或长时间运行的流式处理 gRPC 调用的应用程序可能会因调用排队而出现性能问题。</span><span class="sxs-lookup"><span data-stu-id="99df6-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="99df6-133">.NET 5 引入 `SocketsHttpHandler.EnableMultipleHttp2Connections` 属性。</span><span class="sxs-lookup"><span data-stu-id="99df6-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="99df6-134">如果设置为 `true`，则当达到并发流限制时，通道会创建额外的 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="99df6-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="99df6-135">创建 `GrpcChannel` 时，会自动将其内部 `SocketsHttpHandler` 配置为创建额外的 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="99df6-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="99df6-136">如果应用配置其自己的处理程序，请考虑将 `EnableMultipleHttp2Connections` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="99df6-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

<span data-ttu-id="99df6-137">.NET Core 3.1 应用有几种解决方法：</span><span class="sxs-lookup"><span data-stu-id="99df6-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="99df6-138">为具有高负载的应用的区域创建单独的 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="99df6-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="99df6-139">例如，`Logger` gRPC 服务可能具有高负载。</span><span class="sxs-lookup"><span data-stu-id="99df6-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="99df6-140">使用单独的通道在应用中创建 `LoggerClient`。</span><span class="sxs-lookup"><span data-stu-id="99df6-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="99df6-141">使用 gRPC 通道池，例如创建 gRPC 通道列表。</span><span class="sxs-lookup"><span data-stu-id="99df6-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="99df6-142">每次需要 gRPC 通道时，使用 `Random` 从列表中选取一个通道。</span><span class="sxs-lookup"><span data-stu-id="99df6-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="99df6-143">使用 `Random` 在多个连接上随机分配调用。</span><span class="sxs-lookup"><span data-stu-id="99df6-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="99df6-144">提升服务器上的最大并发流限制是解决此问题的另一种方法。</span><span class="sxs-lookup"><span data-stu-id="99df6-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="99df6-145">在 Kestrel 中，这是用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> 配置的。</span><span class="sxs-lookup"><span data-stu-id="99df6-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="99df6-146">不建议提升最大并发流限制。</span><span class="sxs-lookup"><span data-stu-id="99df6-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="99df6-147">单个 HTTP/2 连接上的流过多会带来新的性能问题：</span><span class="sxs-lookup"><span data-stu-id="99df6-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="99df6-148">尝试写入连接的流之间发生线程争用。</span><span class="sxs-lookup"><span data-stu-id="99df6-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="99df6-149">连接数据包丢失导致在 TCP 层阻止所有调用。</span><span class="sxs-lookup"><span data-stu-id="99df6-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="99df6-150">负载均衡</span><span class="sxs-lookup"><span data-stu-id="99df6-150">Load balancing</span></span>

<span data-ttu-id="99df6-151">一些负载均衡器不能与 gRPC 一起高效工作。</span><span class="sxs-lookup"><span data-stu-id="99df6-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="99df6-152">通过在终结点之间分布 TCP 连接，L4（传输）负载均衡器在连接级别上运行。</span><span class="sxs-lookup"><span data-stu-id="99df6-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="99df6-153">这种方法非常适合使用 HTTP / 1.1 进行的负载均衡 API 调用。</span><span class="sxs-lookup"><span data-stu-id="99df6-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="99df6-154">使用 HTTP/1.1 进行的并发调用在不同的连接上发送，实现调用在终结点之间的负载均衡。</span><span class="sxs-lookup"><span data-stu-id="99df6-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="99df6-155">由于 L4 负载均衡器是在连接级别运行的，它们不太适用于 gRPC。</span><span class="sxs-lookup"><span data-stu-id="99df6-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="99df6-156">GRPC 使用 HTTP/2，在单个 TCP 连接上多路复用多个调用。</span><span class="sxs-lookup"><span data-stu-id="99df6-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="99df6-157">通过该连接的所有 gRPC 调用都将前往一个终结点。</span><span class="sxs-lookup"><span data-stu-id="99df6-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="99df6-158">有两种方法可以高效地对 gRPC 进行负载均衡：</span><span class="sxs-lookup"><span data-stu-id="99df6-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="99df6-159">客户端负载均衡</span><span class="sxs-lookup"><span data-stu-id="99df6-159">Client-side load balancing</span></span>
* <span data-ttu-id="99df6-160">L7（应用程序）代理负载均衡</span><span class="sxs-lookup"><span data-stu-id="99df6-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="99df6-161">只有 gRPC 调用可以在终结点之间进行负载均衡。</span><span class="sxs-lookup"><span data-stu-id="99df6-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="99df6-162">一旦建立了流式 gRPC 调用，通过流发送的所有消息都将前往一个终结点。</span><span class="sxs-lookup"><span data-stu-id="99df6-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="99df6-163">客户端负载均衡</span><span class="sxs-lookup"><span data-stu-id="99df6-163">Client-side load balancing</span></span>

<span data-ttu-id="99df6-164">对于客户端负载均衡，客户端了解终结点。</span><span class="sxs-lookup"><span data-stu-id="99df6-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="99df6-165">对于每个 gRPC 调用，客户端会选择一个不同的终结点作为将该调用发送到的目的地。</span><span class="sxs-lookup"><span data-stu-id="99df6-165">For each gRPC call, it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="99df6-166">如果延迟很重要，那么客户端负载均衡是一个很好的选择。</span><span class="sxs-lookup"><span data-stu-id="99df6-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="99df6-167">客户端和服务之间没有代理，因此调用直接发送到服务。</span><span class="sxs-lookup"><span data-stu-id="99df6-167">There's no proxy between the client and the service, so the call is sent to the service directly.</span></span> <span data-ttu-id="99df6-168">客户端负载均衡的缺点是每个客户端必须跟踪它应该使用的可用终结点。</span><span class="sxs-lookup"><span data-stu-id="99df6-168">The downside to client-side load balancing is that each client must keep track of the available endpoints that it should use.</span></span>

<span data-ttu-id="99df6-169">Lookaside 客户端负载均衡是一种将负载均衡状态存储在中心位置的技术。</span><span class="sxs-lookup"><span data-stu-id="99df6-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="99df6-170">客户端定期查询中心位置以获取在作出负载均衡决策时要使用的信息。</span><span class="sxs-lookup"><span data-stu-id="99df6-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="99df6-171">`Grpc.Net.Client` 当前不支持客户端负载均衡。</span><span class="sxs-lookup"><span data-stu-id="99df6-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="99df6-172">如果 .NET 中需要客户端负载均衡，则 [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="99df6-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="99df6-173">代理负载均衡</span><span class="sxs-lookup"><span data-stu-id="99df6-173">Proxy load balancing</span></span>

<span data-ttu-id="99df6-174">L7（应用程序）代理的工作级别高于 L4（传输）代理。</span><span class="sxs-lookup"><span data-stu-id="99df6-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="99df6-175">L7 代理了解 HTTP/2，并且能够在多个终结点之间的一个 HTTP/2 连接上将多路复用的 gRPC 调用分发给代理。</span><span class="sxs-lookup"><span data-stu-id="99df6-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="99df6-176">使用代理比客户端负载均衡更简单，但会增加 gRPC 调用的额外延迟。</span><span class="sxs-lookup"><span data-stu-id="99df6-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="99df6-177">有很多 L7 代理可用。</span><span class="sxs-lookup"><span data-stu-id="99df6-177">There are many L7 proxies available.</span></span> <span data-ttu-id="99df6-178">一些选项包括：</span><span class="sxs-lookup"><span data-stu-id="99df6-178">Some options are:</span></span>

* <span data-ttu-id="99df6-179">[Envoy](https://www.envoyproxy.io/) - 一种常用的开源代理。</span><span class="sxs-lookup"><span data-stu-id="99df6-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="99df6-180">[Linkerd](https://linkerd.io/) - Kubernetes 服务网格。</span><span class="sxs-lookup"><span data-stu-id="99df6-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="99df6-181">[YARP:反向代理](https://microsoft.github.io/reverse-proxy/) - 用 .NET 编写的预览开源代理。</span><span class="sxs-lookup"><span data-stu-id="99df6-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="99df6-182">进程内通信</span><span class="sxs-lookup"><span data-stu-id="99df6-182">Inter-process communication</span></span>

<span data-ttu-id="99df6-183">客户端和服务之间的 gRPC 调用通常通过 TCP 套接字发送。</span><span class="sxs-lookup"><span data-stu-id="99df6-183">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="99df6-184">TCP 非常适用于网络中的通信，但当客户端和服务在同一台计算机上时，[进程间通信 (IPC)](https://wikipedia.org/wiki/Inter-process_communication) 的效率更高。</span><span class="sxs-lookup"><span data-stu-id="99df6-184">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="99df6-185">考虑在同一台计算机上的进程之间使用 Unix 域套接字或命名管道之类的传输进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="99df6-185">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="99df6-186">有关详细信息，请参阅 <xref:grpc/interprocess>。</span><span class="sxs-lookup"><span data-stu-id="99df6-186">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="99df6-187">保持活动 ping</span><span class="sxs-lookup"><span data-stu-id="99df6-187">Keep alive pings</span></span>

<span data-ttu-id="99df6-188">保持活动 ping 可用于在非活动期间使 HTTP/2 连接保持为活动状态。</span><span class="sxs-lookup"><span data-stu-id="99df6-188">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="99df6-189">如果在应用恢复活动时已准备好现有 HTTP/2 连接，则可以快速进行初始 gRPC 调用，而不会因重新建立连接而导致延迟。</span><span class="sxs-lookup"><span data-stu-id="99df6-189">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="99df6-190">在 <xref:System.Net.Http.SocketsHttpHandler> 上配置保持活动 ping：</span><span class="sxs-lookup"><span data-stu-id="99df6-190">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

<span data-ttu-id="99df6-191">前面的代码配置了一个通道，该通道在非活动期间每 60 秒向服务器发送一次保持活动 ping。</span><span class="sxs-lookup"><span data-stu-id="99df6-191">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="99df6-192">ping 确保服务器和使用中的任何代理不会由于不活动而关闭连接。</span><span class="sxs-lookup"><span data-stu-id="99df6-192">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="99df6-193">流式处理</span><span class="sxs-lookup"><span data-stu-id="99df6-193">Streaming</span></span>

<span data-ttu-id="99df6-194">在高性能方案中，可使用 gRPC 双向流式处理取代一元 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="99df6-194">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="99df6-195">双向流启动后，来回流式处理消息比使用多个一元 gRPC 调用发送消息更快。</span><span class="sxs-lookup"><span data-stu-id="99df6-195">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="99df6-196">流式处理消息作为现有 HTTP/2 请求上的数据发送，节省了为每个一元调用创建新的 HTTP/2 请求的开销。</span><span class="sxs-lookup"><span data-stu-id="99df6-196">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="99df6-197">示例服务：</span><span class="sxs-lookup"><span data-stu-id="99df6-197">Example service:</span></span>

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

<span data-ttu-id="99df6-198">示例客户端：</span><span class="sxs-lookup"><span data-stu-id="99df6-198">Example client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

<span data-ttu-id="99df6-199">将一元调用替换为双向流式处理是一种高级技术，由于性能原因，这在许多情况下并不适用。</span><span class="sxs-lookup"><span data-stu-id="99df6-199">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="99df6-200">有以下情况时，使用流式处理调用是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="99df6-200">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="99df6-201">需要高吞吐量或低延迟。</span><span class="sxs-lookup"><span data-stu-id="99df6-201">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="99df6-202">gRPC 和 HTTP/2 被标识为性能瓶颈。</span><span class="sxs-lookup"><span data-stu-id="99df6-202">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="99df6-203">客户端的辅助程序使用 gRPC 服务发送或接收常规消息。</span><span class="sxs-lookup"><span data-stu-id="99df6-203">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="99df6-204">请注意使用流式处理调用而不是一元调用的其他复杂性和限制：</span><span class="sxs-lookup"><span data-stu-id="99df6-204">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="99df6-205">流可能会因服务或连接错误而中断。</span><span class="sxs-lookup"><span data-stu-id="99df6-205">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="99df6-206">需要在出现错误时重启流的逻辑。</span><span class="sxs-lookup"><span data-stu-id="99df6-206">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="99df6-207">对于多线程处理，`RequestStream.WriteAsync` 并不安全。</span><span class="sxs-lookup"><span data-stu-id="99df6-207">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="99df6-208">一次只能将一条消息写入流中。</span><span class="sxs-lookup"><span data-stu-id="99df6-208">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="99df6-209">通过单个流从多个线程发送消息需要制造者/使用者队列（如 <xref:System.Threading.Channels.Channel%601>）来整理消息。</span><span class="sxs-lookup"><span data-stu-id="99df6-209">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="99df6-210">gRPC 流式处理方法仅限于接收一种类型的消息并发送一种类型的消息。</span><span class="sxs-lookup"><span data-stu-id="99df6-210">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="99df6-211">例如，`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` 接收 `RequestMessage` 并发送 `ResponseMessage`。</span><span class="sxs-lookup"><span data-stu-id="99df6-211">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="99df6-212">Protobuf 对使用 `Any` 和 `oneof` 支持未知消息或条件消息，可以解决此限制。</span><span class="sxs-lookup"><span data-stu-id="99df6-212">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>

## <a name="binary-payloads"></a><span data-ttu-id="99df6-213">二进制有效负载</span><span class="sxs-lookup"><span data-stu-id="99df6-213">Binary payloads</span></span>

<span data-ttu-id="99df6-214">Protobuf 支持标量值类型为 `bytes` 的二进制有效负载。</span><span class="sxs-lookup"><span data-stu-id="99df6-214">Binary payloads are supported in Protobuf with the `bytes` scalar value type.</span></span> <span data-ttu-id="99df6-215">C# 中生成的属性使用 `ByteString` 作为属性类型。</span><span class="sxs-lookup"><span data-stu-id="99df6-215">A generated property in C# uses `ByteString` as the property type.</span></span>

```protobuf
syntax = "proto3";

message PayloadResponse {
    bytes data = 1;
}  
```

<span data-ttu-id="99df6-216">Protobuf 是一种二进制格式，它以最小开销有效地序列化大型二进制有效负载。</span><span class="sxs-lookup"><span data-stu-id="99df6-216">Protobuf is a binary format that efficiently serializes large binary payloads with minimal overhead.</span></span>  <span data-ttu-id="99df6-217">基于文本的格式（如 JSON）需要[将字节编码为 base64](https://en.wikipedia.org/wiki/Base64)，并将 33% 添加到消息大小。</span><span class="sxs-lookup"><span data-stu-id="99df6-217">Text based formats like JSON require [encoding bytes to base64](https://en.wikipedia.org/wiki/Base64) and add 33% to the message size.</span></span>

<span data-ttu-id="99df6-218">使用大型 `ByteString` 有效负载时，有一些最佳做法可以避免下面所讨论的不必要副本和分配。</span><span class="sxs-lookup"><span data-stu-id="99df6-218">When working with large `ByteString` payloads there are some best practices to avoid unnecessary copies and allocations that are discussed below.</span></span>

### <a name="send-binary-payloads"></a><span data-ttu-id="99df6-219">发送二进制有效负载</span><span class="sxs-lookup"><span data-stu-id="99df6-219">Send binary payloads</span></span>

<span data-ttu-id="99df6-220">`ByteString` 实例通常使用 `ByteString.CopyFrom(byte[] data)` 创建。</span><span class="sxs-lookup"><span data-stu-id="99df6-220">`ByteString` instances are normally created using `ByteString.CopyFrom(byte[] data)`.</span></span> <span data-ttu-id="99df6-221">此方法会分配新的 `ByteString` 和新的 `byte[]`。</span><span class="sxs-lookup"><span data-stu-id="99df6-221">This method allocates a new `ByteString` and a new `byte[]`.</span></span> <span data-ttu-id="99df6-222">数据会复制到新的字节数组中。</span><span class="sxs-lookup"><span data-stu-id="99df6-222">Data is copied into the new byte array.</span></span>

<span data-ttu-id="99df6-223">通过使用 `UnsafeByteOperations.UnsafeWrap(ReadOnlyMemory<byte> bytes)` 创建 `ByteString` 实例，可以避免其他分配和复制操作。</span><span class="sxs-lookup"><span data-stu-id="99df6-223">Additional allocations and copies can be avoided by using `UnsafeByteOperations.UnsafeWrap(ReadOnlyMemory<byte> bytes)` to create `ByteString` instances.</span></span>

```csharp
var data = await File.ReadAllBytesAsync(path);

var payload = new PayloadResponse();
payload.Data = UnsafeByteOperations.UnsafeWrap(data);
```

<span data-ttu-id="99df6-224">字节不会通过 `UnsafeByteOperations.UnsafeWrap` 进行复制，因此在使用 `ByteString` 时，不得修改字节。</span><span class="sxs-lookup"><span data-stu-id="99df6-224">Bytes are not copied with `UnsafeByteOperations.UnsafeWrap` so they must not be modified while the `ByteString` is in use.</span></span>

<span data-ttu-id="99df6-225">`UnsafeByteOperations.UnsafeWrap` 要求使用 [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) 版本 3.15.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="99df6-225">`UnsafeByteOperations.UnsafeWrap` requires [Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) version 3.15.0 or later.</span></span>

### <a name="read-binary-payloads"></a><span data-ttu-id="99df6-226">读取二进制有效负载</span><span class="sxs-lookup"><span data-stu-id="99df6-226">Read binary payloads</span></span>

<span data-ttu-id="99df6-227">通过使用 `ByteString.Memory` 和 `ByteString.Span` 属性，可以有效地从 `ByteString` 实例读取数据。</span><span class="sxs-lookup"><span data-stu-id="99df6-227">Data can be efficiently read from `ByteString` instances by using `ByteString.Memory` and `ByteString.Span` properties.</span></span>

```csharp
var byteString = UnsafeByteOperations.UnsafeWrap(new byte[] { 0, 1, 2 });
var data = byteString.Span;

for (var i = 0; i < data.Length; i++)
{
    Console.WriteLine(data[i]);
}
```

<span data-ttu-id="99df6-228">这些属性允许代码直接从 `ByteString` 读取数据，而无需分配或副本。</span><span class="sxs-lookup"><span data-stu-id="99df6-228">These properties allow code to read data directly from a `ByteString` without allocations or copies.</span></span>

<span data-ttu-id="99df6-229">大多数 .NET API 具有 `ReadOnlyMemory<byte>` 和 `byte[]` 重载，因此建议使用 `ByteString.Memory` 来使用基础数据。</span><span class="sxs-lookup"><span data-stu-id="99df6-229">Most .NET APIs have `ReadOnlyMemory<byte>` and `byte[]` overloads, so `ByteString.Memory` is the recommended way to use the underlying data.</span></span> <span data-ttu-id="99df6-230">但是，在某些情况下，应用可能需要将数据作为字节数组获取。</span><span class="sxs-lookup"><span data-stu-id="99df6-230">However, there are circumstances where an app might need to get the data as a byte array.</span></span> <span data-ttu-id="99df6-231">如果需要字节数组，则 <xref:System.Runtime.InteropServices.MemoryMarshal.TryGetArray%2A?displayProperty=nameWithType> 方法可用于从 `ByteString` 获取数组，而无需分配数据的新副本。</span><span class="sxs-lookup"><span data-stu-id="99df6-231">If a byte array is required then the <xref:System.Runtime.InteropServices.MemoryMarshal.TryGetArray%2A?displayProperty=nameWithType> method can be used to get an array from a `ByteString` without allocating a new copy of the data.</span></span>

```csharp
var byteString = GetByteString();

ByteArrayContent content;
if (MemoryMarshal.TryGetArray(byteString.Memory, out var segment))
{
    // Success. Use the ByteString's underlying array.
    content = new ByteArrayContent(segment.Array, segment.Offset, segment.Count);
}
else
{
    // TryGetArray didn't succeed. Fall back to creating a copy of the data with ToByteArray.
    content = new ByteArrayContent(byteString.ToByteArray());
}

var httpRequest = new HttpRequestMessage();
httpRequest.Content = content;
```

<span data-ttu-id="99df6-232">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="99df6-232">The preceding code:</span></span>

* <span data-ttu-id="99df6-233">尝试使用 <xref:System.Runtime.InteropServices.MemoryMarshal.TryGetArray%2A?displayProperty=nameWithType> 从 `ByteString.Memory` 获取数组。</span><span class="sxs-lookup"><span data-stu-id="99df6-233">Attempts to get an array from `ByteString.Memory` with <xref:System.Runtime.InteropServices.MemoryMarshal.TryGetArray%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="99df6-234">如果成功检索，则使用 `ArraySegment<byte>`。</span><span class="sxs-lookup"><span data-stu-id="99df6-234">Uses the `ArraySegment<byte>` if it was successfully retrieved.</span></span> <span data-ttu-id="99df6-235">段具有对数组、偏移和计数的引用。</span><span class="sxs-lookup"><span data-stu-id="99df6-235">The segment has a reference to the array, offset and count.</span></span>
* <span data-ttu-id="99df6-236">否则，将回退到使用 `ByteString.ToByteArray()` 分配新数组。</span><span class="sxs-lookup"><span data-stu-id="99df6-236">Otherwise, falls back to allocating a new array with `ByteString.ToByteArray()`.</span></span>
