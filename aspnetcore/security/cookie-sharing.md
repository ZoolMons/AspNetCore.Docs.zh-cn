---
title: cookie在 ASP.NET 应用之间共享身份验证
author: rick-anderson
description: 了解如何 cookie 在 ASP.NET 4.x 和 ASP.NET Core 应用之间共享身份验证。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252989"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="9f0fa-103">cookie在 ASP.NET 应用之间共享身份验证</span><span class="sxs-lookup"><span data-stu-id="9f0fa-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="9f0fa-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9f0fa-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9f0fa-105">网站通常由单独的 web 应用组成。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="9f0fa-106">若要 (SSO) 体验提供单一登录，站点内的 web 应用必须共享身份验证 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="9f0fa-107">为了支持此方案，数据保护堆栈允许共享 Katana cookie authentication 和 ASP.NET Core cookie 身份验证票证。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="9f0fa-108">在下面的示例中：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-108">In the examples that follow:</span></span>

* <span data-ttu-id="9f0fa-109">身份验证 cookie 名称设置为的公共值 `.AspNet.SharedCookie` 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="9f0fa-110">`AuthenticationType`设置为 `Identity.Application` 显式或默认设置为。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="9f0fa-111">常见的应用名称用于使数据保护系统 () 共享数据保护密钥 `SharedCookieApp` 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="9f0fa-112">`Identity.Application` 用作身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="9f0fa-113">无论使用哪种方案，都必须在共享应用 *内和* 共享应用中以一致的方式使用它， cookie 或者通过显式设置。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="9f0fa-114">加密和解密时将使用该方案 cookie ，因此必须在应用间使用一致的方案。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="9f0fa-115">使用通用的 [数据保护密钥](xref:security/data-protection/implementation/key-management) 存储位置。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="9f0fa-116">在 ASP.NET Core 应用中， <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 用于设置密钥存储位置。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="9f0fa-117">在 .NET Framework 应用中， Cookie 身份验证中间件使用的实现 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="9f0fa-118">`DataProtectionProvider` 为身份验证负载数据的加密和解密提供数据保护服务 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="9f0fa-119">该 `DataProtectionProvider` 实例与应用程序的其他部分所使用的数据保护系统隔离。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="9f0fa-120">[DataProtectionProvider (DirectoryInfo，Action \<IDataProtectionBuilder>) ](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) 接受 <xref:System.IO.DirectoryInfo> 来指定数据保护密钥存储的位置。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="9f0fa-121">`DataProtectionProvider` 需要 DataProtection NuGet 包 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="9f0fa-122">在 ASP.NET Core 1.x 应用程序中，请参阅 [AspNetCore 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="9f0fa-123">在 .NET Framework 应用中，将包引用添加到 [AspNetCore. DataProtection](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="9f0fa-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> 设置常见的应用名称。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="9f0fa-125">与共享身份验证 cookieASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9f0fa-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="9f0fa-126">使用 ASP.NET Core Identity 时：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="9f0fa-127">数据保护密钥和应用程序名称必须在应用之间共享。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="9f0fa-128">在以下示例中，将向方法提供一个公共密钥存储位置 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="9f0fa-129"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>在以下示例中，使用配置公共共享应用名称 (`SharedCookieApp`) 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="9f0fa-130">有关详细信息，请参阅 <xref:security/data-protection/configuration/overview>。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="9f0fa-131">使用 <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> 扩展方法为设置数据保护服务 cookie 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="9f0fa-132">默认的身份验证类型为 `Identity.Application` 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="9f0fa-133">在 `Startup.ConfigureServices`中：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="9f0fa-134">**注意：** 上述说明不适用于 `ITicketStore` (`CookieAuthenticationOptions.SessionStore`) 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="9f0fa-135">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/21163)。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="9f0fa-136">共享身份 cookie 验证 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="9f0fa-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="9f0fa-137">如果 cookie 直接使用，则 ASP.NET Core Identity 在中配置数据保护和身份验证 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="9f0fa-138">在下面的示例中，"身份验证类型" 设置为 `Identity.Application` ：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="9f0fa-139">cookie跨不同的基路径共享</span><span class="sxs-lookup"><span data-stu-id="9f0fa-139">Share cookies across different base paths</span></span>

<span data-ttu-id="9f0fa-140">身份验证 cookie 使用[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)作为其默认值[ Cookie 。路径](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path)。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="9f0fa-141">如果应用的 cookie 必须在不同的基路径间共享，则 `Path` 必须重写：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="9f0fa-142">cookie跨子域共享</span><span class="sxs-lookup"><span data-stu-id="9f0fa-142">Share cookies across subdomains</span></span>

<span data-ttu-id="9f0fa-143">承载跨子域共享的应用时 cookie ，请在中指定一个公共域[ Cookie 。域](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain)属性。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="9f0fa-144">若要 cookie 在上跨应用共享 `contoso.com` ，例如 `first_subdomain.contoso.com` 和 `second_subdomain.contoso.com` ，请将 `Cookie.Domain` 指定 `.contoso.com` 为：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="9f0fa-145">加密静态数据保护密钥</span><span class="sxs-lookup"><span data-stu-id="9f0fa-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="9f0fa-146">对于生产部署，请将配置 `DataProtectionProvider` 为使用 DPAPI 或 X509Certificate 加密静态密钥。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="9f0fa-147">有关详细信息，请参阅 <xref:security/data-protection/implementation/key-encryption-at-rest>。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="9f0fa-148">在下面的示例中，提供了一个证书指纹来 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> 执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="9f0fa-149">cookie在 ASP.NET 4.x 和 ASP.NET Core 应用之间共享身份验证</span><span class="sxs-lookup"><span data-stu-id="9f0fa-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="9f0fa-150">使用 Katana Authentication 中间件的 ASP.NET 4.x 应用程序 Cookie 可以配置为生成 cookie 与 ASP.NET Core Cookie authentication 中间件兼容的身份验证。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="9f0fa-151">这允许在多个步骤中升级大型站点的单个应用，同时跨站点提供平稳的 SSO 体验。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="9f0fa-152">当应用使用 Katana Cookie Authentication 中间件时，它会调用 `UseCookieAuthentication` 项目的 *Startup.Auth.cs* 文件。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="9f0fa-153">使用 Visual Studio 2013 和更高版本创建的 ASP.NET 4.x web 应用项目在 Cookie 默认情况下使用 Katana Authentication 中间件。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="9f0fa-154">尽管 `UseCookieAuthentication` ASP.NET Core 应用已过时且不受支持，但 `UseCookieAuthentication` 在使用 Katana Authentication 中间件的 ASP.NET 4.x 应用中调用 Cookie 是有效的。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="9f0fa-155">ASP.NET 4.x 应用必须面向 .NET Framework 4.5.1 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="9f0fa-156">否则，无法安装所需的 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="9f0fa-157">若要 cookie 在 ASP.NET 4.x 应用和 ASP.NET Core 应用之间共享身份验证，请按在 [ cookie ASP.NET Core 应用间共享身份验证](#share-authentication-cookies-with-aspnet-core-identity) 中的说明配置 ASP.NET Core 应用，并按如下所示配置 ASP.NET 4.x 应用。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="9f0fa-158">确认应用的包已更新到最新版本。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="9f0fa-159">将 [Owin](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) 包安装到每个 ASP.NET 1.x 应用程序中。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="9f0fa-160">查找并修改对的调用 `UseCookieAuthentication` ：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="9f0fa-161">更改该 cookie 名称以匹配该 Cookie 示例) 中 ASP.NET Core Authentication 中间件 (使用的名称 `.AspNet.SharedCookie` 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="9f0fa-162">在下面的示例中，"身份验证类型" 设置为 `Identity.Application` 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="9f0fa-163">向 `DataProtectionProvider` 通用数据保护密钥存储位置提供已初始化的实例。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="9f0fa-164">确认应用名称设置为在 cookie 示例) 中共享身份验证 (的所有应用使用的通用应用名称 `SharedCookieApp` 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="9f0fa-165">如果未设置 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` 和 `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` ，则将设置 <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> 为区分唯一用户的声明。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="9f0fa-166">*App_Start/startup.auth.cs*：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-166">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="9f0fa-167">生成用户标识时， () 的身份验证类型 `Identity.Application` 必须与 `AuthenticationType` `UseCookieAuthentication` *App_Start/startup.auth.cs* 中的 "设置" 中定义的类型匹配。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="9f0fa-168">*模型/ IdentityModels.cs*：</span><span class="sxs-lookup"><span data-stu-id="9f0fa-168">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="9f0fa-169">使用公共用户数据库</span><span class="sxs-lookup"><span data-stu-id="9f0fa-169">Use a common user database</span></span>

<span data-ttu-id="9f0fa-170">如果应用使用相同 Identity 的架构 (相同的 Identity) 版本，请确认 Identity 每个应用的系统指向同一用户数据库。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="9f0fa-171">否则，当标识系统尝试将身份验证中的信息与 cookie 数据库中的信息进行匹配时，它将在运行时生成故障。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="9f0fa-172">如果 Identity 架构在应用中不同，通常是因为应用使用的是不同 Identity 的版本，则在 Identity 不重新映射和添加其他应用的架构中的列的情况下，不能使用来共享基于最新版本的公共数据库 Identity 。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="9f0fa-173">将其他应用程序升级到使用最新版本通常更为有效， Identity 以便应用可以共享公共数据库。</span><span class="sxs-lookup"><span data-stu-id="9f0fa-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9f0fa-174">其他资源</span><span class="sxs-lookup"><span data-stu-id="9f0fa-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
