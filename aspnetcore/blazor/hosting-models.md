---
title: ASP.NET Core Blazor 托管模型
author: guardrex
description: 了解 Blazor WebAssembly 和 Blazor Server 托管模型。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/hosting-models
ms.openlocfilehash: ea39235e51fc430dc2c67b4f2cf281c416705c0b
ms.sourcegitcommit: 1f35de0ca9ba13ea63186c4dc387db4fb8e541e0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "104711069"
---
# <a name="aspnet-core-blazor-hosting-models"></a>ASP.NET Core Blazor 托管模型

Blazor 是一种 Web 框架，专用于在基于 [WebAssembly](https://webassembly.org/) 的 .NET 运行时 (Blazor WebAssembly) 上的浏览器中运行客户端，或在 ASP.NET Core (Blazor Server) 中运行服务器端 。 对于任意托管模型，应用和组件模型都相同。

## Blazor WebAssembly

主要的 Blazor 托管模型在 WebAssembly 上的浏览器中运行客户端。 将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。 应用将在浏览器线程中直接执行。 UI 更新和事件处理在同一进程中进行。 应用资产作为静态文件部署到可为客户端提供静态内容的 Web 服务器或服务中。

![Blazor WebAssembly:Blazor 应用在浏览器内部的 UI 线程上运行。](hosting-models/_static/blazor-webassembly.png)

如果创建了 Blazor WebAssembly 应用进行部署，但没有后端 ASP.NET Core 应用来为其文件提供服务，那么该应用被称为独立 Blazor WebAssembly 应用。 如果创建了应用进行部署，但没有后端应用来为其文件提供服务，那么该应用被称为托管的 Blazor WebAssembly 应用。 托管的 Blazor WebAssembly **`Client`** 应用通常使用 Web API 调用或 [SignalR](xref:signalr/introduction)（<xref:tutorials/signalr-blazor>）通过网络与后端 **`Server`** 应用交互。

`blazor.webassembly.js` 脚本由框架和句柄提供：

* 下载 .NET 运行时、应用和应用依赖项。
* 初始化运行应用的运行时。

Blazor WebAssembly 托管模型具有以下优点：

* 没有 .NET 服务器端依赖项。 应用下载到客户端后即可正常运行。
* 可充分利用客户端资源和功能。
* 工作可从服务器转移到客户端。
* 无需 ASP.NET Core Web 服务器即可托管应用。 无服务器部署方案可行，例如通过内容分发网络 (CDN) 为应用提供服务的方案。

Blazor WebAssembly 托管模型具有以下局限性：

* 应用仅可使用浏览器功能。
* 需要可用的客户端硬件和软件（例如 WebAssembly 支持）。
* 下载项大小较大，应用加载耗时较长。
* .NET 运行时和工具支持不够完善。 例如，[.NET Standard](/dotnet/standard/net-standard) 支持和调试方面存在限制。

若要创建 Blazor WebAssembly 应用，请参阅 <xref:blazor/tooling>。

Blazor 托管应用模型支持 [Docker 容器](/dotnet/standard/microservices-architecture/container-docker-introduction/index)。 对于 Visual Studio 中的 Docker 支持，请右键单击托管的 Blazor WebAssembly 解决方案的 **`Server`** 项目，然后选择“添加” > “Docker 支持”。

## Blazor Server

使用 Blazor Server 托管模型可从 ASP.NET Core 应用中在服务器上执行应用。 UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。

![浏览器通过 SignalR 连接与服务器上的应用（该应用托管在 ASP.NET Core 应用内部）进行交互。](hosting-models/_static/blazor-server.png)

ASP.NET Core 应用会引用应用的 `Startup` 类以添加以下内容：

* 服务器端服务。
* 用于请求处理管道的应用。

在客户端上，`blazor.server.js` 脚本与服务器建立 SignalR 连接。 脚本由 ASP.NET Core 共享框架中的嵌入资源提供给客户端应用。 客户端应用负责根据需要保持和还原应用状态。 

Blazor Server 托管模型具有以下优点：

* 下载项大小明显小于 Blazor WebAssembly 应用，且应用加载速度快得多。
* 应用可充分利用服务器功能，包括使用任何与 .NET Core 兼容的 API。
* 服务器上的 .NET Core 用于运行应用，因此调试等现有 .NET 工具可按预期正常工作。
* 支持瘦客户端。 例如，Blazor Server 应用适用于不支持 WebAssembly 的浏览器以及资源受限的设备。
* 应用的 .NET/C# 代码库（其中包括应用的组件代码）不适用于客户端。

> [!IMPORTANT]
> Blazor Server 应用预呈现以响应第一个客户端请求，这会在服务器上创建 UI 状态。 客户端尝试创建 SignalR 连接时，“必须重新连接到同一服务器”。 使用多个后端服务器的 Blazor Server 应用应实现粘滞会话，从而建立 SignalR 连接。 有关更多信息，请参见[连接到服务器](#connection-to-the-server)一节。

Blazor Server 托管模型具有以下局限性：

* 通常延迟较高。 每次用户交互都涉及到网络跃点。
* 不支持脱机工作。 如果客户端连接失败，应用会停止工作。
* 如果具有多名用户，则应用扩缩性存在挑战。 服务器必须管理多个客户端连接并处理客户端状态。
* 需要 ASP.NET Core 服务器为应用提供服务。 无服务器部署方案不可行，例如通过内容分发网络 (CDN) 为应用提供服务的方案。

若要创建 Blazor Server 应用，请参阅 <xref:blazor/tooling>。

Blazor Server 应用模型支持 [Docker 容器](/dotnet/standard/microservices-architecture/container-docker-introduction/index)。 对于 Visual Studio 中的 Docker 支持，请右键单击 Visual Studio 中的项目，然后选择“添加” > “Docker 支持” 。

### <a name="comparison-to-server-rendered-ui"></a>与服务器呈现的 UI 进行比较

理解 Blazor Server 应用的一种方法是，了解其与使用 Razor 视图或 Razor Pages 在 ASP.NET Core 应用中呈现 UI 的惯用模型之间的差异。 这两种模型都使用 [Razor 语言](xref:mvc/views/razor)描述 HTML 内容，但两者在标记的呈现方式上差别显著。

呈现 Razor 页面或视图时，每行 Razor 代码都以文本形式发出 HTML。 呈现后，服务器会丢弃页面或视图实例，包括生成的任何状态。 出现另一个对该页面的请求时，例如，服务器验证失败并显示验证摘要时：

* 整个页面将再次重新呈现为 HTML 文本。
* 页面会发送到客户端。

Blazor 应用由 UI 的可重用元素组成，这些元素称为组件。 组件包含 C# 代码、标记和其他成分。 呈现组件时，Blazor 会生成所含组件的图，类似于 HTML 或 XML 文档对象模型 (DOM)。 此图包含属性和字段中保存的组件状态。 Blazor 会评估组件图，生成二进制形式的标记。 二进制格式可以：

* 转换为 HTML 文本（预呈现 &dagger; 期间）。
* 用于在定期呈现期间高效更新标记。

&dagger;预呈现：请求获取的 Razor 组件在服务器上编译为静态 HTML，并发送到客户端，然后呈现给用户。 客户端与服务器之间建立连接后，组件的静态预呈现元素会替换为交互式元素。 预呈现会使应用对用户的响应更加迅速。

Blazor 中的 UI 更新由以下内容触发：

* 用户交互，例如选中按钮。
* 应用触发器，例如计时器。

组件组已预呈现，且已计算 UI diff（差异）。 此差异是更新客户端上的 UI 所需的最小一组 DOM 编辑。 差异以二进制格式发送到客户端，并由浏览器应用。

用户在客户端上退出组件之后，组件会被丢弃。 用户与组件交互时，组件的状态（服务、资源）必须保存在服务器的内存中。 由于服务器可能同时保存多个组件的状态，因此必须解决内存不足的问题。 要了解如何创作 Blazor Server 应用以确保充分使用服务器内存，请参阅 <xref:blazor/security/server/threat-mitigation>。

### <a name="circuits"></a>线路

Blazor Server 应用基于 [ASP.NET CoreSignalR](xref:signalr/introduction) 构建。 每个客户端通过一个或多个称为“线路”的 SignalR 连接与服务器通信。 线路是 Blazor 对可容忍短暂网络中断的 SignalR 连接的抽象。 Blazor 客户端发现 SignalR 连接已断开时，它会尝试使用新的 SignalR 连接来重新连接到服务器。

连接到 Blazor Server 应用的每个浏览器屏幕（浏览器标签页或 iframe）均使用 SignalR 连接。 与典型服务器呈现应用相比，这是另一个关键差异。 在服务器呈现应用的多个浏览器屏幕中打开同一应用通常不需要服务器上的其他资源。 在 Blazor Server 应用中，若服务器要管理浏览器屏幕，则每个浏览器屏幕均需要独立线路和组件状态的独立实例。

Blazor 将关闭浏览器标签页或访问外部 URL 视为正常终止。 如果正常终止，则会立即释放线路和关联的资源。 例如，由于网络中断，客户端也可能异常地断开连接。 Blazor Server 会将断开连接的路线存储一段时间（可配置），以便客户端重新连接。

Blazor Server 允许代码定义线路处理程序，后者允许在用户线路的状态发生更改时运行代码。 有关详细信息，请参阅 <xref:blazor/fundamentals/signalr?pivots=server#blazor-server-circuit-handler>。

### <a name="ui-latency"></a>UI 延迟

UI 延迟是指从启动操作到 UI 更新所需的时间。 要使应用对用户进行响应，需要 UI 延迟值较小。 在 Blazor Server 应用中，每个操作都会发送到服务器并进行处理，然后发回 UI 差异。 因此，UI 延迟是网络延迟和处理操作时的服务器延迟的总和。

如果是仅用于专用公司网络的商业应用程序，用户通常不易感受到因网络延迟而导致的延迟。 如果是通过 Internet 部署的应用，用户可能会更容易感受到延迟，用户分布广泛时感受尤为明显。

内存使用也会导致应用延迟。 内存使用率增大会导致垃圾收集频繁或内存分页到磁盘，两者均会降低应用性能，进而增大 UI 延迟。

Blazor Server 应用应降低网络延迟和内存使用率，从而优化以最大限度地降低 UI 延迟。 有关测量网络延迟的方法，请参阅 <xref:blazor/host-and-deploy/server#measure-network-latency>。 有关 SignalR 和 Blazor 的详细信息，请参阅以下内容：

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>连接到服务器

Blazor Server 应用需要与服务器建立有效的 SignalR 连接。 如果连接丢失，应用会尝试重新连接到服务器。 只要客户端的状态仍在服务器的内存中，客户端会话即可恢复，且不会失去状态。

Blazor Server 应用预呈现以响应第一个客户端请求，这会在服务器上创建 UI 状态。 客户端尝试创建 SignalR 连接时，必须重新连接到同一服务器。 使用多个后端服务器的 Blazor Server 应用应实现粘滞会话，从而建立 SignalR 连接。

我们建议将 [Azure SignalR 服务](/azure/azure-signalr)用于 Blazor Server 应用。 该服务允许将 Blazor Server 应用扩展到大量并发 SignalR 连接。 可将服务的 `ServerStickyMode` 选项或配置值设置为 `Required`，从而为 Azure SignalR 服务启用粘滞会话。 有关详细信息，请参阅 <xref:blazor/host-and-deploy/server#signalr-configuration>。

使用 IIS 时，粘滞会话通过应用程序请求路由启用。 有关详细信息，请参阅[使用应用程序请求路由实现 HTTP 负载均衡](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)。

## <a name="additional-resources"></a>其他资源

* <xref:blazor/tooling>
* <xref:blazor/project-structure>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/signalr>
* <xref:tutorials/signalr-blazor>
