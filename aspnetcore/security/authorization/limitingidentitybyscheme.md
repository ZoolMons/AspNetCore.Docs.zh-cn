---
title: 使用 ASP.NET Core 中的特定方案授权
author: rick-anderson
description: 本文介绍如何在使用多个身份验证方法时将标识限制为特定方案。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: c4cbec1b829fb8fd47f7b6924b6870bd5dd7097d
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057299"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="9bac5-103">使用 ASP.NET Core 中的特定方案授权</span><span class="sxs-lookup"><span data-stu-id="9bac5-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="9bac5-104">有关 ASP.NET Core 中的身份验证方案的简介，请参阅 [身份验证方案](xref:security/authentication/index#authentication-scheme)。</span><span class="sxs-lookup"><span data-stu-id="9bac5-104">For an introduction to authentication schemes in ASP.NET Core, see [Authentication scheme](xref:security/authentication/index#authentication-scheme).</span></span>

<span data-ttu-id="9bac5-105">在某些情况下，例如 (Spa) 的单页面应用程序，通常使用多种身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="9bac5-105">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="9bac5-106">例如，应用可能会使用 cookie 基于的身份验证登录，并使用 JWT 持有者身份验证来处理 JavaScript 请求。</span><span class="sxs-lookup"><span data-stu-id="9bac5-106">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="9bac5-107">在某些情况下，应用程序可能有多个身份验证处理程序实例。</span><span class="sxs-lookup"><span data-stu-id="9bac5-107">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="9bac5-108">例如，两个 cookie 处理程序，其中一个包含基本标识，一个在已触发多重身份验证 (MFA) 时创建。</span><span class="sxs-lookup"><span data-stu-id="9bac5-108">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="9bac5-109">可能会触发 MFA，因为用户请求了需要额外安全的操作。</span><span class="sxs-lookup"><span data-stu-id="9bac5-109">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="9bac5-110">有关在用户请求需要 MFA 的资源时强制执行 MFA 的详细信息，请参阅 GitHub 颁发 [保护部分与 mfa](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195)。</span><span class="sxs-lookup"><span data-stu-id="9bac5-110">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="9bac5-111">身份验证方案是在身份验证过程中配置身份验证服务时命名的。</span><span class="sxs-lookup"><span data-stu-id="9bac5-111">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="9bac5-112">例如：</span><span class="sxs-lookup"><span data-stu-id="9bac5-112">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="9bac5-113">在前面的代码中，添加了两个身份验证处理程序：一个是 cookie ，一个用于持有者。</span><span class="sxs-lookup"><span data-stu-id="9bac5-113">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="9bac5-114">指定默认方案 `HttpContext.User` 会导致将属性设置为该标识。</span><span class="sxs-lookup"><span data-stu-id="9bac5-114">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="9bac5-115">如果不需要该行为，请通过调用的无参数形式来禁用它 `AddAuthentication` 。</span><span class="sxs-lookup"><span data-stu-id="9bac5-115">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="9bac5-116">选择具有授权属性的方案</span><span class="sxs-lookup"><span data-stu-id="9bac5-116">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="9bac5-117">在授权时，应用指示要使用的处理程序。</span><span class="sxs-lookup"><span data-stu-id="9bac5-117">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="9bac5-118">选择应用程序将通过以逗号分隔的身份验证方案列表传递到来授权的处理程序 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="9bac5-118">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="9bac5-119">`[Authorize]`属性指定要使用的身份验证方案或方案，不管是否配置了默认设置。</span><span class="sxs-lookup"><span data-stu-id="9bac5-119">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="9bac5-120">例如：</span><span class="sxs-lookup"><span data-stu-id="9bac5-120">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="9bac5-121">在前面的示例中， cookie 和持有者处理程序都运行，并且有机会为当前用户创建并追加标识。</span><span class="sxs-lookup"><span data-stu-id="9bac5-121">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="9bac5-122">通过仅指定一个方案，将运行相应的处理程序。</span><span class="sxs-lookup"><span data-stu-id="9bac5-122">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="9bac5-123">在前面的代码中，只有具有 "持有者" 方案的处理程序才会运行。</span><span class="sxs-lookup"><span data-stu-id="9bac5-123">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="9bac5-124">cookie将忽略任何基于的标识。</span><span class="sxs-lookup"><span data-stu-id="9bac5-124">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="9bac5-125">选择具有策略的方案</span><span class="sxs-lookup"><span data-stu-id="9bac5-125">Selecting the scheme with policies</span></span>

<span data-ttu-id="9bac5-126">如果希望在 [策略](xref:security/authorization/policies)中指定所需的方案，则可以在 `AuthenticationSchemes` 添加策略时设置集合：</span><span class="sxs-lookup"><span data-stu-id="9bac5-126">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

<span data-ttu-id="9bac5-127">在前面的示例中，"Over18" 策略仅针对 "持有者" 处理程序创建的标识运行。</span><span class="sxs-lookup"><span data-stu-id="9bac5-127">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="9bac5-128">通过设置特性的属性来使用该策略 `[Authorize]` `Policy` ：</span><span class="sxs-lookup"><span data-stu-id="9bac5-128">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="9bac5-129">使用多种身份验证方案</span><span class="sxs-lookup"><span data-stu-id="9bac5-129">Use multiple authentication schemes</span></span>

<span data-ttu-id="9bac5-130">某些应用可能需要支持多种身份验证类型。</span><span class="sxs-lookup"><span data-stu-id="9bac5-130">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="9bac5-131">例如，你的应用程序可以从 Azure Active Directory 和用户数据库对用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="9bac5-131">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="9bac5-132">另一个示例是从 Active Directory 联合身份验证服务和 Azure Active Directory B2C 对用户进行身份验证的应用程序。</span><span class="sxs-lookup"><span data-stu-id="9bac5-132">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="9bac5-133">在这种情况下，应用程序应接受来自多个颁发者的 JWT 持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="9bac5-133">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="9bac5-134">添加想要接受的所有身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="9bac5-134">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="9bac5-135">例如，以下代码 `Startup.ConfigureServices` 将添加两个具有不同颁发者的 JWT 持有者身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="9bac5-135">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> <span data-ttu-id="9bac5-136">只向默认的身份验证方案注册一个 JWT 持有者身份验证 `JwtBearerDefaults.AuthenticationScheme` 。</span><span class="sxs-lookup"><span data-stu-id="9bac5-136">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="9bac5-137">必须使用唯一的身份验证方案注册附加身份验证。</span><span class="sxs-lookup"><span data-stu-id="9bac5-137">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="9bac5-138">下一步是更新默认授权策略，以接受这两种身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="9bac5-138">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="9bac5-139">例如：</span><span class="sxs-lookup"><span data-stu-id="9bac5-139">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

<span data-ttu-id="9bac5-140">当重写默认授权策略时，可以使用 `[Authorize]` 控制器中的属性。</span><span class="sxs-lookup"><span data-stu-id="9bac5-140">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="9bac5-141">然后，控制器接受由第一个或第二个颁发者颁发的 JWT 的请求。</span><span class="sxs-lookup"><span data-stu-id="9bac5-141">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

<span data-ttu-id="9bac5-142">有关使用多个身份验证方案，请参阅 [此 GitHub 问题](https://github.com/dotnet/aspnetcore/issues/26002) 。</span><span class="sxs-lookup"><span data-stu-id="9bac5-142">See [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/26002) on using multiple authentication schemes.</span></span>
