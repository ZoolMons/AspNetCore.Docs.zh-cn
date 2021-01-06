---
title: 版本控制 gRPC 服务
author: jamesnk
description: 了解如何对 gRPC 服务进行版本控制。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
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
uid: grpc/versioning
ms.openlocfilehash: 38204b16d041f21221862c566b90a6a9571d26a1
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058696"
---
# <a name="versioning-grpc-services"></a>版本控制 gRPC 服务

作者：[James Newton-King](https://twitter.com/jamesnk)

添加到应用的新功能可能要求提供给客户端的 gRPC 服务进行更改（有时要求该服务以意想不到、打破常规的方式进行更改）。 gRPC 服务更改时：

* 应考虑更改会如何影响客户端。
* 应实现支持更改的版本控制策略。

## <a name="backwards-compatibility"></a>向后兼容性

gRPC 协议旨在支持随时间变化的服务。 通常，gRPC 服务和方法会不中断地新增内容。 非中断性变更允许现有客户端继续工作而不做任何变更。 更改或删除 gRPC 服务是中断性变更。 gRPC 服务发生中断性变更时，必须更新和重新部署使用该服务的客户端。

对服务进行非中断性变更有许多好处：

* 现有客户端可继续运行。
* 避免向客户端通知中断性变更并进行更新。
* 只需要记录和维护服务的一个版本。

### <a name="non-breaking-changes"></a>非重大变化

在 gRPC 协议级别和 .NET 二进制级别，这些变更不会中断。

* 添加新服务
* 向服务中添加新方法
* 将字段添加到请求消息 - 添加到请求消息的字段将在服务器上通过[默认值](https://developers.google.com/protocol-buffers/docs/proto3#default)（若未设置）进行反序列化。 若要实现非中断性变更，当新字段不是由旧客户端设置时，服务必须成功。
* 将字段添加到响应消息 - 添加到响应消息的字段将反序列化到客户端上消息的[未知字段](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)集合中。
* 向枚举添加值 - 枚举被序列化为数值。 新的枚举值在客户端反序列化为没有枚举名的枚举值。 若要实现非中断性变更，旧客户端在接收新枚举值时必须正确运行。

### <a name="binary-breaking-changes"></a>二进制中断性变更

以下变更在 gRPC 协议级别是非中断性变更，但如果客户端升级到最新的 .proto 协定或客户端 .NET 程序集，则需要对其进行更新。 如果你计划将 gRPC 库发布到 NuGet，二进制兼容性很重要。

* 删除字段 - 已删除字段中的值被反序列化为消息的[未知字段](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)。 这并不是 gRPC 协议中断性变更，但如果客户端升级到最新的协定，则需要对其进行更新。 删除的字段编号不会在将来被意外重用，这点很重要。 若要确保不会发生这种情况，请使用 Protobuf 的[保留](https://developers.google.com/protocol-buffers/docs/proto3#reserved)关键字指定邮件上已删除的字段编号和名称。
* 重命名消息 - 消息名称通常不会在网络上发送，因此这不是 gRPC 协议中断性变更。 如果客户端升级到最新的协定，则需要对其进行更新。 当消息名称用于标识消息类型时，[任何](https://developers.google.com/protocol-buffers/docs/proto3#any)字段都会出现消息名称在网络上发送的情况。
* 更改 csharp_namespace - 更改 `csharp_namespace` 将更改所生成的 .NET 类型的命名空间。 这并不是 gRPC 协议中断性变更，但如果客户端升级到最新的协定，则需要对其进行更新。

### <a name="protocol-breaking-changes"></a>协议中断性变更

以下各项是协议和二进制的中断性变更：

* 重命名字段 - 对于 Protobuf 内容，字段名只在生成的代码中使用。 字段编号用于标识网络上的字段。 对 Protobuf 来说，重命名字段不是协议中断性变更。 但是，如果服务器正在使用 JSON 内容，则重命名字段是一个中断性变更。
* 更改字段数据类型 - 将字段的数据类型更改为[不兼容类型](https://developers.google.com/protocol-buffers/docs/proto3#updating)将在反序列化消息时导致错误。 即使新的数据类型是兼容的，但如果客户端升级到最新的协定，它也可能需要更新以支持新的类型。
* 更改字段编号 - 对于 Protobuf 有效负载，字段编号用于标识网络上的字段。
* 重命名包、服务或方法 - gRPC 使用包名、服务名和方法名来生成 URL。 客户端从服务器获取 UNIMPLEMENTED 状态。
* 删除服务或方法 - 客户端在调用已删除的方法时从服务器获取 UNIMPLEMENTED 状态。

### <a name="behavior-breaking-changes"></a>行为中断性变更

在进行非中断性更改时，还必须考虑旧客户端是否可以继续使用新的服务行为。 例如，向请求消息添加新字段：

* 它不是协议中断性变更。
* 如果未设置新字段，则在服务器上返回错误状态对于旧客户端来说是一个中断性变更。

行为兼容性由应用特定的代码决定。

## <a name="version-number-services"></a>版本号服务

服务应尽量保持与旧客户端的后向兼容。 最终对应用的更改可能需要进行中断性变更。 中断旧客户端并强制其随服务一起更新不是一种好的用户体验。 若要在进行中断性变更的同时保持后向兼容性，一种方法是发布服务的多个版本。

gRPC 支持可选的[包](https://developers.google.com/protocol-buffers/docs/proto3#packages)说明符，它的功能非常类似于 .NET 命名空间。 实际上，如果 .proto 文件中未设置 `option csharp_namespace`，则 `package` 将用作生成的 .NET 类型的 .NET 命名空间。 该包可用于指定服务的版本号及其消息：

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

包名称与服务名称相结合以标识服务地址。 服务地址允许并行托管服务的多个版本：

* `greet.v1.Greeter`
* `greet.v2.Greeter`

已进行版本控制的服务的实现在 Startup.cs 中注册：

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

通过在包名称中包含版本号，你可发布具有中断性变更的服务 v2 版本，同时继续支持调用 v1 版本的旧客户端 。 更新客户端以使用 v2 服务后，你可选择删除旧版本。 计划发布服务的多个版本时：

* 如果合理，请避免中断性变更。
* 除非进行中断性更改，否则请勿更新版本号。
* 进行中断性变更时，请务必更新版本号。

发布多个服务版本会使其重复。 若要减少重复，请考虑将业务逻辑从服务实现移动到可由新旧实现重用的集中位置：

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

使用不同包名称生成的服务和消息属于不同的 .NET 类型。 将业务逻辑移动到集中位置需要将消息映射到常见类型。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/protobuf>
