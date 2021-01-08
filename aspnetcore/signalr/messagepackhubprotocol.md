---
title: 使用中的 MessagePack Hub 协议 SignalR 进行 ASP.NET Core
author: bradygaster
description: 将 MessagePack Hub 协议添加到 ASP.NET Core SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7c3640e9cd2c5d392400a115813584861f789554
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024686"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="61da0-103">使用中的 MessagePack Hub 协议 SignalR 进行 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="61da0-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="61da0-104">本文假定读者 [熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="61da0-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="61da0-105">什么是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="61da0-105">What is MessagePack?</span></span>

<span data-ttu-id="61da0-106">[MessagePack](https://msgpack.org/index.html) 是一种快速、精简的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="61da0-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="61da0-107">当性能和带宽需要考虑时，它很有用，因为它会创建比 [JSON](https://www.json.org/)更小的消息。</span><span class="sxs-lookup"><span data-stu-id="61da0-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="61da0-108">在查看网络跟踪和日志时，不能读取二进制消息，除非这些字节是通过 MessagePack 分析器传递的。</span><span class="sxs-lookup"><span data-stu-id="61da0-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="61da0-109">SignalR 提供对 MessagePack 格式的内置支持，并为客户端和服务器提供要使用的 Api。</span><span class="sxs-lookup"><span data-stu-id="61da0-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="61da0-110">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="61da0-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="61da0-111">若要在服务器上启用 MessagePack 集线器协议，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="61da0-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="61da0-112">在 `Startup.ConfigureServices` 方法中，将添加 `AddMessagePackProtocol` 到在 `AddSignalR` 服务器上启用 MessagePack 支持的调用。</span><span class="sxs-lookup"><span data-stu-id="61da0-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-113">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="61da0-113">JSON is enabled by default.</span></span> <span data-ttu-id="61da0-114">添加 MessagePack 可支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="61da0-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="61da0-115">若要自定义 MessagePack 如何设置数据的格式，请 `AddMessagePackProtocol` 使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="61da0-116">在该委托中， `SerializerOptions` 属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="61da0-117">有关解析程序工作方式的详细信息，请访问 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)上的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="61da0-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="61da0-118">属性可用于要序列化的对象，以定义应如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="61da0-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="61da0-119">强烈建议查看 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 和应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="61da0-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="61da0-120">例如， `.WithSecurity(MessagePackSecurity.UntrustedData)` 在替换时调用 `SerializerOptions` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="61da0-121">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="61da0-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-122">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="61da0-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="61da0-123">客户端只能支持一个协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="61da0-124">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="61da0-125">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-125">.NET client</span></span>

<span data-ttu-id="61da0-126">若要在 .NET 客户端中启用 MessagePack，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在上安装包并调用 `AddMessagePackProtocol` `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="61da0-127">此 `AddMessagePackProtocol` 调用采用一个委托来配置与服务器类似的选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="61da0-128">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-128">JavaScript client</span></span>

<span data-ttu-id="61da0-129">MessagePack 支持 JavaScript 客户端由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="61da0-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="61da0-130">通过在命令行界面中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="61da0-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="61da0-131">安装 npm 包后，可以通过 JavaScript 模块加载程序直接使用该模块，或通过引用以下文件将该模块导入到浏览器中：</span><span class="sxs-lookup"><span data-stu-id="61da0-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="61da0-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="61da0-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="61da0-133">在浏览器中， `msgpack5` 还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="61da0-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="61da0-134">使用 `<script>` 标记创建引用。</span><span class="sxs-lookup"><span data-stu-id="61da0-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="61da0-135">可在 *node_modules\msgpack5\dist\msgpack5.js* 中找到库。</span><span class="sxs-lookup"><span data-stu-id="61da0-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-136">使用元素时 `<script>` ，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="61da0-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="61da0-137">如果在 *msgpack5.js* 之前引用 *signalr-protocol-msgpack.js* ，则在尝试使用 MessagePack 进行连接时将出现错误。</span><span class="sxs-lookup"><span data-stu-id="61da0-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="61da0-138">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="61da0-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="61da0-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`如果添加到，则 `HubConnectionBuilder` 会将客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="61da0-140">目前，JavaScript 客户端上没有用于 MessagePack 协议的配置选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

### <a name="java-client"></a><span data-ttu-id="61da0-141">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-141">Java client</span></span>

<span data-ttu-id="61da0-142">若要使用 Java 启用 MessagePack，请安装 `com.microsoft.signalr.messagepack` 包。</span><span class="sxs-lookup"><span data-stu-id="61da0-142">To enable MessagePack with Java, install the `com.microsoft.signalr.messagepack` package.</span></span> <span data-ttu-id="61da0-143">当使用 Gradle 时，请将以下行添加到 `dependencies` *Gradle* 文件的部分：</span><span class="sxs-lookup"><span data-stu-id="61da0-143">When using Gradle, add the following line to the `dependencies` section of the *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr.messagepack:signalr-messagepack:5.0.0'
```

<span data-ttu-id="61da0-144">当使用 Maven 时，请在pom.xml文件的元素中添加以下行 `<dependencies>` ： </span><span class="sxs-lookup"><span data-stu-id="61da0-144">When using Maven, add the following lines inside the `<dependencies>` element of the *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element messagePack](java-client/sample/pom.xml?name=snippet_dependencyElement_messagePack)]

<span data-ttu-id="61da0-145">调用 `withHubProtocol(new MessagePackHubProtocol())` `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-145">Call `withHubProtocol(new MessagePackHubProtocol())` on `HubConnectionBuilder`.</span></span>

```java
HubConnection messagePackConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withHubProtocol(new MessagePackHubProtocol())
    .build();
```

## <a name="messagepack-quirks"></a><span data-ttu-id="61da0-146">MessagePack 兼容</span><span class="sxs-lookup"><span data-stu-id="61da0-146">MessagePack quirks</span></span>

<span data-ttu-id="61da0-147">使用 MessagePack 集线器协议时，需要注意几个问题。</span><span class="sxs-lookup"><span data-stu-id="61da0-147">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="61da0-148">MessagePack 区分大小写</span><span class="sxs-lookup"><span data-stu-id="61da0-148">MessagePack is case-sensitive</span></span>

<span data-ttu-id="61da0-149">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="61da0-149">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="61da0-150">例如，请考虑以下 c # 类：</span><span class="sxs-lookup"><span data-stu-id="61da0-150">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="61da0-151">从 JavaScript 客户端发送时，必须使用 `PascalCased` 属性名称，因为大小写必须与 c # 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="61da0-151">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="61da0-152">例如：</span><span class="sxs-lookup"><span data-stu-id="61da0-152">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="61da0-153">使用 `camelCased` 名称无法正确绑定到 c # 类。</span><span class="sxs-lookup"><span data-stu-id="61da0-153">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="61da0-154">可以通过使用 `Key` 属性为 MessagePack 属性指定不同的名称来解决此情况。</span><span class="sxs-lookup"><span data-stu-id="61da0-154">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="61da0-155">有关详细信息，请参阅 [MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="61da0-155">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="61da0-156">序列化/反序列化时不保留 DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="61da0-156">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="61da0-157">MessagePack 协议不提供对的值进行编码的方法 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-157">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="61da0-158">因此，在对日期进行反序列化时，如果为，则 MessagePack 集线器协议会转换为 UTC 格式， `DateTime.Kind` `DateTimeKind.Local` 否则它将不会触及时间并按原样传递它。</span><span class="sxs-lookup"><span data-stu-id="61da0-158">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="61da0-159">如果你使用的是 `DateTime` 值，则建议在发送这些值前将其转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="61da0-159">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="61da0-160">接收到本地时间时将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="61da0-160">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="61da0-161">JavaScript 中的 MessagePack 不支持 MinValue</span><span class="sxs-lookup"><span data-stu-id="61da0-161">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="61da0-162">JavaScript 客户端使用的 [msgpack5](https://github.com/mcollina/msgpack5) 库 SignalR 不支持 `timestamp96` MessagePack 中的类型。</span><span class="sxs-lookup"><span data-stu-id="61da0-162">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="61da0-163">此类型用于对非常大的日期值进行编码， (在将来或在未来) 中非常早的时间。</span><span class="sxs-lookup"><span data-stu-id="61da0-163">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="61da0-164">的值 `DateTime.MinValue` 为 `January 1, 0001` ，必须在值中对其进行编码 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-164">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="61da0-165">因此， `DateTime.MinValue` 不支持向 JavaScript 客户端发送发送。</span><span class="sxs-lookup"><span data-stu-id="61da0-165">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="61da0-166">当 `DateTime.MinValue` JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="61da0-166">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="61da0-167">通常， `DateTime.MinValue` 用于对 "缺失" 或值进行编码 `null` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-167">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="61da0-168">如果需要在 MessagePack 中对该值进行编码，请使用 () 的可以为 null 的 `DateTime` 值， `DateTime?` 或对 `bool` 指示日期是否存在的单独值进行编码。</span><span class="sxs-lookup"><span data-stu-id="61da0-168">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="61da0-169">有关此限制的详细信息，请参阅 GitHub 颁发 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="61da0-169">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="61da0-170">"提前" 编译环境中的 MessagePack 支持</span><span class="sxs-lookup"><span data-stu-id="61da0-170">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="61da0-171">.NET 客户端和服务器使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) 库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="61da0-171">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="61da0-172">因此，默认情况下，在使用 "预先" 编译 (（如 Xamarin iOS 或 Unity) ）的环境中不支持默认值。</span><span class="sxs-lookup"><span data-stu-id="61da0-172">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="61da0-173">可以通过 "预生成" 序列化程序/反序列化程序代码，在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="61da0-173">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="61da0-174">有关详细信息，请参阅 [MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin)。</span><span class="sxs-lookup"><span data-stu-id="61da0-174">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="61da0-175">预生成序列化程序后，可以使用传递给的配置委托注册它们 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="61da0-175">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="61da0-176">类型检查在 MessagePack 中更加严格</span><span class="sxs-lookup"><span data-stu-id="61da0-176">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="61da0-177">JSON 集线器协议将在反序列化过程中执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="61da0-177">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="61da0-178">例如，如果传入的对象的属性值是 (的数字 `{ foo: 42 }`) 但 .net 类的属性的类型为 `string` ，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="61da0-178">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="61da0-179">但是，MessagePack 不会执行此转换，并将引发异常，该异常可在服务器端日志 (和控制台) 中出现：</span><span class="sxs-lookup"><span data-stu-id="61da0-179">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="61da0-180">有关此限制的详细信息，请参阅 GitHub 颁发 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="61da0-180">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

### <a name="chars-and-strings-in-java"></a><span data-ttu-id="61da0-181">Java 中的字符和字符串</span><span class="sxs-lookup"><span data-stu-id="61da0-181">Chars and Strings in Java</span></span>

<span data-ttu-id="61da0-182">在 java 客户端中， `char` 对象将被序列化为单字符 `String` 对象。</span><span class="sxs-lookup"><span data-stu-id="61da0-182">In the java client, `char` objects will be serialized as one-character `String` objects.</span></span> <span data-ttu-id="61da0-183">这与 c # 和 JavaScript 客户端相反，后者将它们序列化为 `short` 对象。</span><span class="sxs-lookup"><span data-stu-id="61da0-183">This is in contrast with the C# and JavaScript client, which serialize them as `short` objects.</span></span> <span data-ttu-id="61da0-184">MessagePack 规范本身不会定义对象的行为 `char` ，因此，由库作者决定如何序列化它们。</span><span class="sxs-lookup"><span data-stu-id="61da0-184">The MessagePack spec itself does not define behavior for `char` objects, so it is up to the library author to determine how to serialize them.</span></span> <span data-ttu-id="61da0-185">我们的客户端之间的行为差异是我们用于实现的库的结果。</span><span class="sxs-lookup"><span data-stu-id="61da0-185">The difference in behavior between our clients is a result of the libraries we used for our implementations.</span></span>

## <a name="related-resources"></a><span data-ttu-id="61da0-186">相关资源</span><span class="sxs-lookup"><span data-stu-id="61da0-186">Related resources</span></span>

* [<span data-ttu-id="61da0-187">入门</span><span class="sxs-lookup"><span data-stu-id="61da0-187">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="61da0-188">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-188">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="61da0-189">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-189">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="61da0-190">本文假定读者 [熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="61da0-190">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="61da0-191">什么是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="61da0-191">What is MessagePack?</span></span>

<span data-ttu-id="61da0-192">[MessagePack](https://msgpack.org/index.html) 是一种快速、精简的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="61da0-192">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="61da0-193">当性能和带宽需要考虑时，它很有用，因为它会创建比 [JSON](https://www.json.org/)更小的消息。</span><span class="sxs-lookup"><span data-stu-id="61da0-193">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="61da0-194">在查看网络跟踪和日志时，不能读取二进制消息，除非这些字节是通过 MessagePack 分析器传递的。</span><span class="sxs-lookup"><span data-stu-id="61da0-194">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="61da0-195">SignalR 提供对 MessagePack 格式的内置支持，并为客户端和服务器提供要使用的 Api。</span><span class="sxs-lookup"><span data-stu-id="61da0-195">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="61da0-196">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="61da0-196">Configure MessagePack on the server</span></span>

<span data-ttu-id="61da0-197">若要在服务器上启用 MessagePack 集线器协议，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="61da0-197">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="61da0-198">在 `Startup.ConfigureServices` 方法中，将添加 `AddMessagePackProtocol` 到在 `AddSignalR` 服务器上启用 MessagePack 支持的调用。</span><span class="sxs-lookup"><span data-stu-id="61da0-198">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-199">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="61da0-199">JSON is enabled by default.</span></span> <span data-ttu-id="61da0-200">添加 MessagePack 可支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="61da0-200">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="61da0-201">若要自定义 MessagePack 如何设置数据的格式，请 `AddMessagePackProtocol` 使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-201">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="61da0-202">在该委托中， `FormatterResolvers` 属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-202">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="61da0-203">有关解析程序工作方式的详细信息，请访问 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)上的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="61da0-203">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="61da0-204">属性可用于要序列化的对象，以定义应如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="61da0-204">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="61da0-205">强烈建议查看 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 和应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="61da0-205">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="61da0-206">例如，将 `MessagePackSecurity.Active` 静态属性设置为 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-206">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="61da0-207">设置 `MessagePackSecurity.Active` 需要手动安装[MessagePack 的1.9 版。](https://www.nuget.org/packages/MessagePack/1.9.3)</span><span class="sxs-lookup"><span data-stu-id="61da0-207">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="61da0-208">正在安装 `MessagePack` 版本为的 1.9. x 的升级 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="61da0-208">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="61da0-209">`MessagePack` 版本2.x 引入了重大更改，与 SignalR 版本3.1 及更早版本不兼容。</span><span class="sxs-lookup"><span data-stu-id="61da0-209">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="61da0-210">如果 `MessagePackSecurity.Active` 未设置为 `MessagePackSecurity.UntrustedData` ，则恶意客户端可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="61da0-210">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="61da0-211">`MessagePackSecurity.Active`在中设置 `Program.Main` ，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="61da0-211">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="61da0-212">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="61da0-212">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-213">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="61da0-213">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="61da0-214">客户端只能支持一个协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-214">Clients can only support a single protocol.</span></span> <span data-ttu-id="61da0-215">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-215">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="61da0-216">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-216">.NET client</span></span>

<span data-ttu-id="61da0-217">若要在 .NET 客户端中启用 MessagePack，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在上安装包并调用 `AddMessagePackProtocol` `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-217">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="61da0-218">此 `AddMessagePackProtocol` 调用采用一个委托来配置与服务器类似的选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-218">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="61da0-219">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-219">JavaScript client</span></span>

<span data-ttu-id="61da0-220">MessagePack 支持 JavaScript 客户端由 [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="61da0-220">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="61da0-221">通过在命令行界面中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="61da0-221">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="61da0-222">安装 npm 包后，可以通过 JavaScript 模块加载程序直接使用该模块，或通过引用以下文件将该模块导入到浏览器中：</span><span class="sxs-lookup"><span data-stu-id="61da0-222">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="61da0-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="61da0-223">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="61da0-224">在浏览器中， `msgpack5` 还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="61da0-224">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="61da0-225">使用 `<script>` 标记创建引用。</span><span class="sxs-lookup"><span data-stu-id="61da0-225">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="61da0-226">可在 *node_modules\msgpack5\dist\msgpack5.js* 中找到库。</span><span class="sxs-lookup"><span data-stu-id="61da0-226">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-227">使用元素时 `<script>` ，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="61da0-227">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="61da0-228">如果在 *msgpack5.js* 之前引用 *signalr-protocol-msgpack.js* ，则在尝试使用 MessagePack 进行连接时将出现错误。</span><span class="sxs-lookup"><span data-stu-id="61da0-228">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="61da0-229">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="61da0-229">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="61da0-230">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`如果添加到，则 `HubConnectionBuilder` 会将客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-230">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="61da0-231">目前，JavaScript 客户端上没有用于 MessagePack 协议的配置选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-231">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="61da0-232">MessagePack 兼容</span><span class="sxs-lookup"><span data-stu-id="61da0-232">MessagePack quirks</span></span>

<span data-ttu-id="61da0-233">使用 MessagePack 集线器协议时，需要注意几个问题。</span><span class="sxs-lookup"><span data-stu-id="61da0-233">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="61da0-234">MessagePack 区分大小写</span><span class="sxs-lookup"><span data-stu-id="61da0-234">MessagePack is case-sensitive</span></span>

<span data-ttu-id="61da0-235">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="61da0-235">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="61da0-236">例如，请考虑以下 c # 类：</span><span class="sxs-lookup"><span data-stu-id="61da0-236">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="61da0-237">从 JavaScript 客户端发送时，必须使用 `PascalCased` 属性名称，因为大小写必须与 c # 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="61da0-237">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="61da0-238">例如：</span><span class="sxs-lookup"><span data-stu-id="61da0-238">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="61da0-239">使用 `camelCased` 名称无法正确绑定到 c # 类。</span><span class="sxs-lookup"><span data-stu-id="61da0-239">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="61da0-240">可以通过使用 `Key` 属性为 MessagePack 属性指定不同的名称来解决此情况。</span><span class="sxs-lookup"><span data-stu-id="61da0-240">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="61da0-241">有关详细信息，请参阅 [MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="61da0-241">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="61da0-242">序列化/反序列化时不保留 DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="61da0-242">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="61da0-243">MessagePack 协议不提供对的值进行编码的方法 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-243">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="61da0-244">因此，在对日期进行反序列化时，MessagePack Hub 协议假设传入日期为 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="61da0-244">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="61da0-245">如果使用的是 `DateTime` 本地时间的值，建议在发送之前将值转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="61da0-245">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="61da0-246">接收到本地时间时将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="61da0-246">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="61da0-247">有关此限制的详细信息，请参阅 GitHub 颁发 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="61da0-247">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="61da0-248">JavaScript 中的 MessagePack 不支持 MinValue</span><span class="sxs-lookup"><span data-stu-id="61da0-248">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="61da0-249">JavaScript 客户端使用的 [msgpack5](https://github.com/mcollina/msgpack5) 库 SignalR 不支持 `timestamp96` MessagePack 中的类型。</span><span class="sxs-lookup"><span data-stu-id="61da0-249">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="61da0-250">此类型用于对非常大的日期值进行编码， (在将来或在未来) 中非常早的时间。</span><span class="sxs-lookup"><span data-stu-id="61da0-250">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="61da0-251">的值 `DateTime.MinValue` 为 `January 1, 0001` ，必须在值中对其进行编码 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-251">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="61da0-252">因此， `DateTime.MinValue` 不支持向 JavaScript 客户端发送发送。</span><span class="sxs-lookup"><span data-stu-id="61da0-252">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="61da0-253">当 `DateTime.MinValue` JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="61da0-253">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="61da0-254">通常， `DateTime.MinValue` 用于对 "缺失" 或值进行编码 `null` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-254">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="61da0-255">如果需要在 MessagePack 中对该值进行编码，请使用 () 的可以为 null 的 `DateTime` 值， `DateTime?` 或对 `bool` 指示日期是否存在的单独值进行编码。</span><span class="sxs-lookup"><span data-stu-id="61da0-255">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="61da0-256">有关此限制的详细信息，请参阅 GitHub 颁发 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="61da0-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="61da0-257">"提前" 编译环境中的 MessagePack 支持</span><span class="sxs-lookup"><span data-stu-id="61da0-257">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="61da0-258">.NET 客户端和服务器使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="61da0-258">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="61da0-259">因此，默认情况下，在使用 "预先" 编译 (（如 Xamarin iOS 或 Unity) ）的环境中不支持默认值。</span><span class="sxs-lookup"><span data-stu-id="61da0-259">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="61da0-260">可以通过 "预生成" 序列化程序/反序列化程序代码，在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="61da0-260">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="61da0-261">有关详细信息，请参阅 [MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="61da0-261">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="61da0-262">预生成序列化程序后，可以使用传递给的配置委托注册它们 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="61da0-262">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="61da0-263">类型检查在 MessagePack 中更加严格</span><span class="sxs-lookup"><span data-stu-id="61da0-263">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="61da0-264">JSON 集线器协议将在反序列化过程中执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="61da0-264">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="61da0-265">例如，如果传入的对象的属性值是 (的数字 `{ foo: 42 }`) 但 .net 类的属性的类型为 `string` ，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="61da0-265">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="61da0-266">但是，MessagePack 不会执行此转换，并将引发异常，该异常可在服务器端日志 (和控制台) 中出现：</span><span class="sxs-lookup"><span data-stu-id="61da0-266">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="61da0-267">有关此限制的详细信息，请参阅 GitHub 颁发 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="61da0-267">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="61da0-268">相关资源</span><span class="sxs-lookup"><span data-stu-id="61da0-268">Related resources</span></span>

* [<span data-ttu-id="61da0-269">入门</span><span class="sxs-lookup"><span data-stu-id="61da0-269">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="61da0-270">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-270">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="61da0-271">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-271">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="61da0-272">本文假定读者 [熟悉入门中](xref:tutorials/signalr)介绍的主题。</span><span class="sxs-lookup"><span data-stu-id="61da0-272">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="61da0-273">什么是 MessagePack？</span><span class="sxs-lookup"><span data-stu-id="61da0-273">What is MessagePack?</span></span>

<span data-ttu-id="61da0-274">[MessagePack](https://msgpack.org/index.html) 是一种快速、精简的二进制序列化格式。</span><span class="sxs-lookup"><span data-stu-id="61da0-274">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="61da0-275">当性能和带宽需要考虑时，它很有用，因为它会创建比 [JSON](https://www.json.org/)更小的消息。</span><span class="sxs-lookup"><span data-stu-id="61da0-275">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="61da0-276">在查看网络跟踪和日志时，不能读取二进制消息，除非这些字节是通过 MessagePack 分析器传递的。</span><span class="sxs-lookup"><span data-stu-id="61da0-276">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="61da0-277">SignalR 提供对 MessagePack 格式的内置支持，并为客户端和服务器提供要使用的 Api。</span><span class="sxs-lookup"><span data-stu-id="61da0-277">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="61da0-278">在服务器上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="61da0-278">Configure MessagePack on the server</span></span>

<span data-ttu-id="61da0-279">若要在服务器上启用 MessagePack 集线器协议，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在应用中安装包。</span><span class="sxs-lookup"><span data-stu-id="61da0-279">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="61da0-280">在 `Startup.ConfigureServices` 方法中，将添加 `AddMessagePackProtocol` 到在 `AddSignalR` 服务器上启用 MessagePack 支持的调用。</span><span class="sxs-lookup"><span data-stu-id="61da0-280">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-281">默认情况下启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="61da0-281">JSON is enabled by default.</span></span> <span data-ttu-id="61da0-282">添加 MessagePack 可支持 JSON 和 MessagePack 客户端。</span><span class="sxs-lookup"><span data-stu-id="61da0-282">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="61da0-283">若要自定义 MessagePack 如何设置数据的格式，请 `AddMessagePackProtocol` 使用委托来配置选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-283">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="61da0-284">在该委托中， `FormatterResolvers` 属性可用于配置 MessagePack 序列化选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-284">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="61da0-285">有关解析程序工作方式的详细信息，请访问 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp)上的 MessagePack 库。</span><span class="sxs-lookup"><span data-stu-id="61da0-285">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="61da0-286">属性可用于要序列化的对象，以定义应如何处理它们。</span><span class="sxs-lookup"><span data-stu-id="61da0-286">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="61da0-287">强烈建议查看 [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) 和应用建议的修补程序。</span><span class="sxs-lookup"><span data-stu-id="61da0-287">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="61da0-288">例如，将 `MessagePackSecurity.Active` 静态属性设置为 `MessagePackSecurity.UntrustedData` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-288">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="61da0-289">设置 `MessagePackSecurity.Active` 需要手动安装[MessagePack 的1.9 版。](https://www.nuget.org/packages/MessagePack/1.9.3)</span><span class="sxs-lookup"><span data-stu-id="61da0-289">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="61da0-290">正在安装 `MessagePack` 版本为的 1.9. x 的升级 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="61da0-290">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="61da0-291">当未 `MessagePackSecurity.Active` 设置为时 `MessagePackSecurity.UntrustedData` ，恶意客户端可能会导致拒绝服务。</span><span class="sxs-lookup"><span data-stu-id="61da0-291">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="61da0-292">`MessagePackSecurity.Active`在中设置 `Program.Main` ，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="61da0-292">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="61da0-293">在客户端上配置 MessagePack</span><span class="sxs-lookup"><span data-stu-id="61da0-293">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-294">默认情况下，为支持的客户端启用 JSON。</span><span class="sxs-lookup"><span data-stu-id="61da0-294">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="61da0-295">客户端只能支持一个协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-295">Clients can only support a single protocol.</span></span> <span data-ttu-id="61da0-296">添加 MessagePack 支持将替换任何以前配置的协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-296">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="61da0-297">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-297">.NET client</span></span>

<span data-ttu-id="61da0-298">若要在 .NET 客户端中启用 MessagePack，请 `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` 在上安装包并调用 `AddMessagePackProtocol` `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-298">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="61da0-299">此 `AddMessagePackProtocol` 调用采用一个委托来配置与服务器类似的选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-299">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="61da0-300">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-300">JavaScript client</span></span>

<span data-ttu-id="61da0-301">MessagePack 支持 JavaScript 客户端由 [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm 包提供。</span><span class="sxs-lookup"><span data-stu-id="61da0-301">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="61da0-302">通过在命令行界面中执行以下命令来安装包：</span><span class="sxs-lookup"><span data-stu-id="61da0-302">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="61da0-303">安装 npm 包后，可以通过 JavaScript 模块加载程序直接使用该模块，或通过引用以下文件将该模块导入到浏览器中：</span><span class="sxs-lookup"><span data-stu-id="61da0-303">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="61da0-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="61da0-304">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="61da0-305">在浏览器中， `msgpack5` 还必须引用库。</span><span class="sxs-lookup"><span data-stu-id="61da0-305">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="61da0-306">使用 `<script>` 标记创建引用。</span><span class="sxs-lookup"><span data-stu-id="61da0-306">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="61da0-307">可在 *node_modules\msgpack5\dist\msgpack5.js* 中找到库。</span><span class="sxs-lookup"><span data-stu-id="61da0-307">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="61da0-308">使用元素时 `<script>` ，顺序很重要。</span><span class="sxs-lookup"><span data-stu-id="61da0-308">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="61da0-309">如果在 *msgpack5.js* 之前引用 *signalr-protocol-msgpack.js* ，则在尝试使用 MessagePack 进行连接时将出现错误。</span><span class="sxs-lookup"><span data-stu-id="61da0-309">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="61da0-310">*signalr-protocol-msgpack.js* 之前也需要 *signalr.js* 。</span><span class="sxs-lookup"><span data-stu-id="61da0-310">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="61da0-311">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`如果添加到，则 `HubConnectionBuilder` 会将客户端配置为在连接到服务器时使用 MessagePack 协议。</span><span class="sxs-lookup"><span data-stu-id="61da0-311">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="61da0-312">目前，JavaScript 客户端上没有用于 MessagePack 协议的配置选项。</span><span class="sxs-lookup"><span data-stu-id="61da0-312">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="61da0-313">MessagePack 兼容</span><span class="sxs-lookup"><span data-stu-id="61da0-313">MessagePack quirks</span></span>

<span data-ttu-id="61da0-314">使用 MessagePack 集线器协议时，需要注意几个问题。</span><span class="sxs-lookup"><span data-stu-id="61da0-314">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="61da0-315">MessagePack 区分大小写</span><span class="sxs-lookup"><span data-stu-id="61da0-315">MessagePack is case-sensitive</span></span>

<span data-ttu-id="61da0-316">MessagePack 协议区分大小写。</span><span class="sxs-lookup"><span data-stu-id="61da0-316">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="61da0-317">例如，请考虑以下 c # 类：</span><span class="sxs-lookup"><span data-stu-id="61da0-317">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="61da0-318">从 JavaScript 客户端发送时，必须使用 `PascalCased` 属性名称，因为大小写必须与 c # 类完全匹配。</span><span class="sxs-lookup"><span data-stu-id="61da0-318">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="61da0-319">例如：</span><span class="sxs-lookup"><span data-stu-id="61da0-319">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="61da0-320">使用 `camelCased` 名称无法正确绑定到 c # 类。</span><span class="sxs-lookup"><span data-stu-id="61da0-320">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="61da0-321">可以通过使用 `Key` 属性为 MessagePack 属性指定不同的名称来解决此情况。</span><span class="sxs-lookup"><span data-stu-id="61da0-321">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="61da0-322">有关详细信息，请参阅 [MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp#object-serialization)。</span><span class="sxs-lookup"><span data-stu-id="61da0-322">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="61da0-323">序列化/反序列化时不保留 DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="61da0-323">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="61da0-324">MessagePack 协议不提供对的值进行编码的方法 `Kind` `DateTime` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-324">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="61da0-325">因此，在对日期进行反序列化时，MessagePack Hub 协议假设传入日期为 UTC 格式。</span><span class="sxs-lookup"><span data-stu-id="61da0-325">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="61da0-326">如果使用的是 `DateTime` 本地时间的值，建议在发送之前将值转换为 UTC。</span><span class="sxs-lookup"><span data-stu-id="61da0-326">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="61da0-327">接收到本地时间时将它们从 UTC 转换为本地时间。</span><span class="sxs-lookup"><span data-stu-id="61da0-327">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="61da0-328">有关此限制的详细信息，请参阅 GitHub 颁发 [aspnet/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632)。</span><span class="sxs-lookup"><span data-stu-id="61da0-328">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="61da0-329">JavaScript 中的 MessagePack 不支持 MinValue</span><span class="sxs-lookup"><span data-stu-id="61da0-329">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="61da0-330">JavaScript 客户端使用的 [msgpack5](https://github.com/mcollina/msgpack5) 库 SignalR 不支持 `timestamp96` MessagePack 中的类型。</span><span class="sxs-lookup"><span data-stu-id="61da0-330">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="61da0-331">此类型用于对非常大的日期值进行编码， (在将来或在未来) 中非常早的时间。</span><span class="sxs-lookup"><span data-stu-id="61da0-331">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="61da0-332">的值 `DateTime.MinValue` `January 1, 0001` 必须在值中进行编码 `timestamp96` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-332">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="61da0-333">因此， `DateTime.MinValue` 不支持向 JavaScript 客户端发送发送。</span><span class="sxs-lookup"><span data-stu-id="61da0-333">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="61da0-334">当 `DateTime.MinValue` JavaScript 客户端收到时，将引发以下错误：</span><span class="sxs-lookup"><span data-stu-id="61da0-334">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="61da0-335">通常， `DateTime.MinValue` 用于对 "缺失" 或值进行编码 `null` 。</span><span class="sxs-lookup"><span data-stu-id="61da0-335">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="61da0-336">如果需要在 MessagePack 中对该值进行编码，请使用 () 的可以为 null 的 `DateTime` 值， `DateTime?` 或对 `bool` 指示日期是否存在的单独值进行编码。</span><span class="sxs-lookup"><span data-stu-id="61da0-336">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="61da0-337">有关此限制的详细信息，请参阅 GitHub 颁发 [aspnet/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228)。</span><span class="sxs-lookup"><span data-stu-id="61da0-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="61da0-338">"提前" 编译环境中的 MessagePack 支持</span><span class="sxs-lookup"><span data-stu-id="61da0-338">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="61da0-339">.NET 客户端和服务器使用的 [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) 库使用代码生成来优化序列化。</span><span class="sxs-lookup"><span data-stu-id="61da0-339">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="61da0-340">因此，默认情况下，在使用 "预先" 编译 (（如 Xamarin iOS 或 Unity) ）的环境中不支持默认值。</span><span class="sxs-lookup"><span data-stu-id="61da0-340">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="61da0-341">可以通过 "预生成" 序列化程序/反序列化程序代码，在这些环境中使用 MessagePack。</span><span class="sxs-lookup"><span data-stu-id="61da0-341">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="61da0-342">有关详细信息，请参阅 [MessagePack-CSharp 文档](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports)。</span><span class="sxs-lookup"><span data-stu-id="61da0-342">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="61da0-343">预生成序列化程序后，可以使用传递给的配置委托注册它们 `AddMessagePackProtocol` ：</span><span class="sxs-lookup"><span data-stu-id="61da0-343">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="61da0-344">类型检查在 MessagePack 中更加严格</span><span class="sxs-lookup"><span data-stu-id="61da0-344">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="61da0-345">JSON 集线器协议将在反序列化过程中执行类型转换。</span><span class="sxs-lookup"><span data-stu-id="61da0-345">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="61da0-346">例如，如果传入的对象的属性值是 (的数字 `{ foo: 42 }`) 但 .net 类的属性的类型为 `string` ，则将转换该值。</span><span class="sxs-lookup"><span data-stu-id="61da0-346">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="61da0-347">但是，MessagePack 不会执行此转换，并将引发异常，该异常可在服务器端日志 (和控制台) 中出现：</span><span class="sxs-lookup"><span data-stu-id="61da0-347">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="61da0-348">有关此限制的详细信息，请参阅 GitHub 颁发 [aspnet/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937)。</span><span class="sxs-lookup"><span data-stu-id="61da0-348">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="61da0-349">相关资源</span><span class="sxs-lookup"><span data-stu-id="61da0-349">Related resources</span></span>

* [<span data-ttu-id="61da0-350">入门</span><span class="sxs-lookup"><span data-stu-id="61da0-350">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="61da0-351">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-351">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="61da0-352">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="61da0-352">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
