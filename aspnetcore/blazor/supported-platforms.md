---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 支持的平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: fe0734dbf6eb2647fa6c9b6f336063b9ec091139
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054952"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="13d39-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="13d39-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="13d39-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13d39-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="13d39-105">下表中所示的浏览器支持 Blazor WebAssembly 和 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="13d39-105">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="13d39-106">浏览者</span><span class="sxs-lookup"><span data-stu-id="13d39-106">Browser</span></span>                          | <span data-ttu-id="13d39-107">Version</span><span class="sxs-lookup"><span data-stu-id="13d39-107">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="13d39-108">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="13d39-108">Apple Safari, including iOS</span></span>      | <span data-ttu-id="13d39-109">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-109">Current&dagger;</span></span> |
| <span data-ttu-id="13d39-110">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="13d39-110">Google Chrome, including Android</span></span> | <span data-ttu-id="13d39-111">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-111">Current&dagger;</span></span> |
| <span data-ttu-id="13d39-112">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="13d39-112">Microsoft Edge</span></span>                   | <span data-ttu-id="13d39-113">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-113">Current&dagger;</span></span> |
| <span data-ttu-id="13d39-114">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="13d39-114">Mozilla Firefox</span></span>                  | <span data-ttu-id="13d39-115">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-115">Current&dagger;</span></span> |  

<span data-ttu-id="13d39-116">&dagger;最新指的是浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="13d39-116">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="13d39-117">浏览者</span><span class="sxs-lookup"><span data-stu-id="13d39-117">Browser</span></span>                          | <span data-ttu-id="13d39-118">Version</span><span class="sxs-lookup"><span data-stu-id="13d39-118">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="13d39-119">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="13d39-119">Apple Safari, including iOS</span></span>      | <span data-ttu-id="13d39-120">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-120">Current&dagger;</span></span>       |
| <span data-ttu-id="13d39-121">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="13d39-121">Google Chrome, including Android</span></span> | <span data-ttu-id="13d39-122">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-122">Current&dagger;</span></span>       |
| <span data-ttu-id="13d39-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="13d39-123">Microsoft Edge</span></span>                   | <span data-ttu-id="13d39-124">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-124">Current&dagger;</span></span>       |
| <span data-ttu-id="13d39-125">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="13d39-125">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="13d39-126">不支持&Dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-126">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="13d39-127">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="13d39-127">Mozilla Firefox</span></span>                  | <span data-ttu-id="13d39-128">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-128">Current&dagger;</span></span>       |  

<span data-ttu-id="13d39-129">&dagger;最新指的是浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="13d39-129">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="13d39-130">&Dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="13d39-130">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="13d39-131">浏览者</span><span class="sxs-lookup"><span data-stu-id="13d39-131">Browser</span></span>                          | <span data-ttu-id="13d39-132">Version</span><span class="sxs-lookup"><span data-stu-id="13d39-132">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="13d39-133">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="13d39-133">Apple Safari, including iOS</span></span>      | <span data-ttu-id="13d39-134">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-134">Current&dagger;</span></span> |
| <span data-ttu-id="13d39-135">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="13d39-135">Google Chrome, including Android</span></span> | <span data-ttu-id="13d39-136">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-136">Current&dagger;</span></span> |
| <span data-ttu-id="13d39-137">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="13d39-137">Microsoft Edge</span></span>                   | <span data-ttu-id="13d39-138">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-138">Current&dagger;</span></span> |
| <span data-ttu-id="13d39-139">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="13d39-139">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="13d39-140">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-140">11&Dagger;</span></span>      |
| <span data-ttu-id="13d39-141">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="13d39-141">Mozilla Firefox</span></span>                  | <span data-ttu-id="13d39-142">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="13d39-142">Current&dagger;</span></span> |

<span data-ttu-id="13d39-143">&dagger;最新指的是浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="13d39-143">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="13d39-144">&Dagger;需要其他填充代码。</span><span class="sxs-lookup"><span data-stu-id="13d39-144">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="13d39-145">例如，可通过 [`Polyfill.io`](https://polyfill.io/v3/) 捆绑包添加承诺。</span><span class="sxs-lookup"><span data-stu-id="13d39-145">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="13d39-146">其他资源</span><span class="sxs-lookup"><span data-stu-id="13d39-146">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
