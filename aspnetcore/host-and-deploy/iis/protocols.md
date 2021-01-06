---
title: 在 IIS 中将 ASP.NET Core 和 HTTP/2 结合使用
author: rick-anderson
description: 了解如何将 HTTP/2 功能和 IIS 结合使用。
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057409"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>在 IIS 中将 ASP.NET Core 和 HTTP/2 结合使用

作者：[Justin Kotalik](https://github.com/jkotalik)

以下 IIS 部署方案中的 ASP.NET Core 支持 [HTTP/2](https://httpwg.org/specs/rfc7540.html)：

* Windows Server 2016 或更高版本/Windows 10 或更高版本
* IIS 10 或更高版本
* TLS 1.2 或更高版本的连接
* [进程外托管](xref:host-and-deploy/iis/index#out-of-process-hosting-model)时：面向公众的边缘服务器连接使用 HTTP/2，但与 [Kestrel 服务器](xref:fundamentals/servers/kestrel)的反向代理连接使用 HTTP/1.1。

对于建立了 HTTP/2 连接时的进程内部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/2`。 对于建立了 HTTP/2 连接时的进程外部署，[`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) 报告 `HTTP/1.1`。

若要详细了解进程内和进程外托管模型，请参阅 <xref:host-and-deploy/aspnet-core-module>。

默认情况下已为 HTTPS/TLS 连接启用 HTTP/2。 如果未建立 HTTP/2 连接，连接会回退到 HTTP/1.1。 有关使用 IIS 部署的 HTTP/2 配置的详细信息，请参阅 [IIS 上的 HTTP/2](/iis/get-started/whats-new-in-iis-10/http2-on-iis)。

## <a name="advanced-http2-features-to-support-grpc"></a>用于支持 gRPC 的高级 HTTP/2 功能

IIS 中的其他 HTTP/2 功能支持 gRPC，包括对响应尾部和发送重置帧的支持。

在 IIS 上运行 gRPC 的要求：

* 进程内托管。
* Windows 10，OS 内部版本 20300.1000 或更高版本。 可能需要使用 Windows 预览体验计划内部版本。
* TLS 1.2 或更高版本的连接

### <a name="trailers"></a>预告片

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>重置

[!INCLUDE[](~/includes/reset.md)]
