---
title: 在 ASP.NET 4.x 和 ASP.NET Core 之间进行选择
author: rick-anderson
description: 介绍 ASP.NET Core 和ASP.NET 4.x 以及如何在它们之间进行选择。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 02/12/2020
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
uid: fundamentals/choose-between-aspnet-and-aspnetcore
ms.openlocfilehash: 192784b4f2cb3b80511814de4f777c4a49fc594b
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710732"
---
# <a name="choose-between-aspnet-4x-and-aspnet-core"></a><span data-ttu-id="4907a-103">在 ASP.NET 4.x 和 ASP.NET Core 之间进行选择</span><span class="sxs-lookup"><span data-stu-id="4907a-103">Choose between ASP.NET 4.x and ASP.NET Core</span></span>

<span data-ttu-id="4907a-104">ASP.NET Core 是 ASP.NET 4.x 的重新设计。</span><span class="sxs-lookup"><span data-stu-id="4907a-104">ASP.NET Core is a redesign of ASP.NET 4.x.</span></span> <span data-ttu-id="4907a-105">本文列出了两者之间的区别。</span><span class="sxs-lookup"><span data-stu-id="4907a-105">This article lists the differences between them.</span></span>

## <a name="aspnet-core"></a><span data-ttu-id="4907a-106">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4907a-106">ASP.NET Core</span></span>

<span data-ttu-id="4907a-107">ASP.NET Core 是一个跨平台的开源框架，用于在 Windows、macOS 或 Linux 上生成基于云的新式 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="4907a-107">ASP.NET Core is an open-source, cross-platform framework for building modern, cloud-based web apps on Windows, macOS, or Linux.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="aspnet-4x"></a><span data-ttu-id="4907a-108">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="4907a-108">ASP.NET 4.x</span></span>

<span data-ttu-id="4907a-109">ASP.NET 4.x 是一个成熟的框架，提供在 Windows 上生成基于服务器的企业级 Web 应用所需的服务。</span><span class="sxs-lookup"><span data-stu-id="4907a-109">ASP.NET 4.x is a mature framework that provides the services needed to build enterprise-grade, server-based web apps on Windows.</span></span>

## <a name="framework-selection"></a><span data-ttu-id="4907a-110">框架选择</span><span class="sxs-lookup"><span data-stu-id="4907a-110">Framework selection</span></span>

<span data-ttu-id="4907a-111">下表将 ASP.NET Core 与 ASP.NET 4.x 进行比较。</span><span class="sxs-lookup"><span data-stu-id="4907a-111">The following table compares ASP.NET Core to ASP.NET 4.x.</span></span>

| <span data-ttu-id="4907a-112">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4907a-112">ASP.NET Core</span></span> | <span data-ttu-id="4907a-113">ASP.NET 4.x</span><span class="sxs-lookup"><span data-stu-id="4907a-113">ASP.NET 4.x</span></span> |
|---|---|
|<span data-ttu-id="4907a-114">针对 Windows、macOS 或 Linux 进行生成</span><span class="sxs-lookup"><span data-stu-id="4907a-114">Build for Windows, macOS, or Linux</span></span>|<span data-ttu-id="4907a-115">针对 Windows 进行生成</span><span class="sxs-lookup"><span data-stu-id="4907a-115">Build for Windows</span></span>|
|<span data-ttu-id="4907a-116">[Razor Pages](xref:razor-pages/index) 是在 ASP.NET Core 2.x 及更高版本中创建 Web UI 时建议使用的方法。</span><span class="sxs-lookup"><span data-stu-id="4907a-116">[Razor Pages](xref:razor-pages/index) is the recommended approach to create a Web UI as of ASP.NET Core 2.x.</span></span> <span data-ttu-id="4907a-117">另请参阅 [MVC](xref:mvc/overview)、[Web API](xref:tutorials/first-web-api) 和 [SignalR](xref:signalr/introduction)。</span><span class="sxs-lookup"><span data-stu-id="4907a-117">See also [MVC](xref:mvc/overview), [Web API](xref:tutorials/first-web-api), and [SignalR](xref:signalr/introduction).</span></span>|<span data-ttu-id="4907a-118">使用 [Web Forms](/aspnet/web-forms)、[SignalR](/aspnet/signalr)、[MVC](/aspnet/mvc)、[Web API](/aspnet/web-api/)、[WebHook](/aspnet/webhooks/) 或[网页](/aspnet/web-pages)</span><span class="sxs-lookup"><span data-stu-id="4907a-118">Use [Web Forms](/aspnet/web-forms), [SignalR](/aspnet/signalr), [MVC](/aspnet/mvc), [Web API](/aspnet/web-api/), [WebHooks](/aspnet/webhooks/), or [Web Pages](/aspnet/web-pages)</span></span>|
|<span data-ttu-id="4907a-119">每个计算机多个版本</span><span class="sxs-lookup"><span data-stu-id="4907a-119">Multiple versions per machine</span></span>|<span data-ttu-id="4907a-120">每个计算机一个版本</span><span class="sxs-lookup"><span data-stu-id="4907a-120">One version per machine</span></span>|
|<span data-ttu-id="4907a-121">使用 C# 或 F# 通过 [Visual Studio](https://visualstudio.microsoft.com/vs/)、[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) 或 [Visual Studio Code](https://code.visualstudio.com/) 进行开发</span><span class="sxs-lookup"><span data-stu-id="4907a-121">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/), [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/), or [Visual Studio Code](https://code.visualstudio.com/) using C# or F#</span></span>|<span data-ttu-id="4907a-122">使用 C#、VB 或 F# 通过 [Visual Studio](https://visualstudio.microsoft.com/vs/) 进行开发</span><span class="sxs-lookup"><span data-stu-id="4907a-122">Develop with [Visual Studio](https://visualstudio.microsoft.com/vs/) using C#, VB, or F#</span></span>|
|<span data-ttu-id="4907a-123">比 ASP.NET 4.x 性能更高</span><span class="sxs-lookup"><span data-stu-id="4907a-123">Higher performance than ASP.NET 4.x</span></span>|<span data-ttu-id="4907a-124">良好的性能</span><span class="sxs-lookup"><span data-stu-id="4907a-124">Good performance</span></span>|
|[<span data-ttu-id="4907a-125">使用 .NET Core 运行时</span><span class="sxs-lookup"><span data-stu-id="4907a-125">Use .NET Core runtime</span></span>](/dotnet/standard/choosing-core-framework-server)|<span data-ttu-id="4907a-126">使用 .NET Framework 运行时</span><span class="sxs-lookup"><span data-stu-id="4907a-126">Use .NET Framework runtime</span></span>|

<span data-ttu-id="4907a-127">有关 .NET Framework 上的 ASP.NET Core 2.x 支持的信息，请参阅[面向 .NET Framework 的 ASP.NET Core](xref:index#target-framework)。</span><span class="sxs-lookup"><span data-stu-id="4907a-127">See [ASP.NET Core targeting .NET Framework](xref:index#target-framework) for information on ASP.NET Core 2.x support on .NET Framework.</span></span>

## <a name="aspnet-core-scenarios"></a><span data-ttu-id="4907a-128">ASP.NET Core 方案</span><span class="sxs-lookup"><span data-stu-id="4907a-128">ASP.NET Core scenarios</span></span>

* [<span data-ttu-id="4907a-129">网站</span><span class="sxs-lookup"><span data-stu-id="4907a-129">Websites</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="4907a-130">API</span><span class="sxs-lookup"><span data-stu-id="4907a-130">APIs</span></span>](xref:tutorials/first-web-api)
* [<span data-ttu-id="4907a-131">实时</span><span class="sxs-lookup"><span data-stu-id="4907a-131">Real-time</span></span>](xref:signalr/introduction)
* [<span data-ttu-id="4907a-132">将 ASP.NET Core 应用部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="4907a-132">Deploy an ASP.NET Core app to Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet)

## <a name="aspnet-4x-scenarios"></a><span data-ttu-id="4907a-133">ASP.NET 4.x 方案</span><span class="sxs-lookup"><span data-stu-id="4907a-133">ASP.NET 4.x scenarios</span></span>

* [<span data-ttu-id="4907a-134">网站</span><span class="sxs-lookup"><span data-stu-id="4907a-134">Websites</span></span>](/aspnet/mvc)
* [<span data-ttu-id="4907a-135">API</span><span class="sxs-lookup"><span data-stu-id="4907a-135">APIs</span></span>](/aspnet/web-api)
* [<span data-ttu-id="4907a-136">实时</span><span class="sxs-lookup"><span data-stu-id="4907a-136">Real-time</span></span>](/aspnet/signalr)
* [<span data-ttu-id="4907a-137">在 Azure 中创建 ASP.NET 4.x Web 应用</span><span class="sxs-lookup"><span data-stu-id="4907a-137">Create an ASP.NET 4.x web app in Azure</span></span>](/azure/app-service/app-service-web-get-started-dotnet-framework)

## <a name="additional-resources"></a><span data-ttu-id="4907a-138">其他资源</span><span class="sxs-lookup"><span data-stu-id="4907a-138">Additional resources</span></span>

* [<span data-ttu-id="4907a-139">ASP.NET 简介</span><span class="sxs-lookup"><span data-stu-id="4907a-139">Introduction to ASP.NET</span></span>](/aspnet/overview)
* [<span data-ttu-id="4907a-140">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="4907a-140">Introduction to ASP.NET Core</span></span>](xref:index)
* <xref:host-and-deploy/azure-apps/index>
