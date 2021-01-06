---
title: ASP.NET Core 1.1 的新增功能
author: rick-anderson
description: 了解 ASP.NET Core 1.1 的新增功能。
ms.author: riande
ms.date: 12/18/2018
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
uid: aspnetcore-1.1
ms.openlocfilehash: cc891280a6314dbc4c0838d5b4a8d2a20698eab6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059736"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="64617-103">ASP.NET Core 1.1 的新增功能</span><span class="sxs-lookup"><span data-stu-id="64617-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="64617-104">ASP.NET Core 1.1 新增了以下功能：</span><span class="sxs-lookup"><span data-stu-id="64617-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="64617-105">URL 重写中间件</span><span class="sxs-lookup"><span data-stu-id="64617-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="64617-106">响应缓存中间件</span><span class="sxs-lookup"><span data-stu-id="64617-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="64617-107">查看组件即标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="64617-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="64617-108">MVC 型中间件筛选器</span><span class="sxs-lookup"><span data-stu-id="64617-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="64617-109">基于 Cookie 的 TempData 提供程序</span><span class="sxs-lookup"><span data-stu-id="64617-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="64617-110">Azure App Service 日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="64617-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="64617-111">Azure Key Vault 配置提供程序</span><span class="sxs-lookup"><span data-stu-id="64617-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="64617-112">Azure 和 Redis 存储数据保护密钥存储库</span><span class="sxs-lookup"><span data-stu-id="64617-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="64617-113">适用于 Windows 的 WebListener 服务器</span><span class="sxs-lookup"><span data-stu-id="64617-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="64617-114">WebSockets 支持</span><span class="sxs-lookup"><span data-stu-id="64617-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="64617-115">在 ASP.NET Core 的 1.0 和 1.1 版本之间进行选择</span><span class="sxs-lookup"><span data-stu-id="64617-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="64617-116">ASP.NET Core 1.1 比 ASP.NET Core 1.0 功能更多。</span><span class="sxs-lookup"><span data-stu-id="64617-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="64617-117">通常情况下，建议使用最新版本。</span><span class="sxs-lookup"><span data-stu-id="64617-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="64617-118">其他信息</span><span class="sxs-lookup"><span data-stu-id="64617-118">Additional Information</span></span>

- [<span data-ttu-id="64617-119">ASP.NET Core 1.1.0 发行说明</span><span class="sxs-lookup"><span data-stu-id="64617-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="64617-120">若要实时了解 ASP.NET Core 开发团队的进度和计划，请收看 [ASP.NET Community Standup](https://live.asp.net/)。</span><span class="sxs-lookup"><span data-stu-id="64617-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
