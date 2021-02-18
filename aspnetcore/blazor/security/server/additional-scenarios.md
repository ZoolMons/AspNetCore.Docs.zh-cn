---
title: ASP.NET Core Blazor Server 其他安全方案
author: guardrex
description: 了解如何为其他安全方案配置 Blazor Server。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: d47cfba75b640f57cc713049594d4e8acd1fcd0e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280328"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="d054e-103">ASP.NET Core Blazor Server 其他安全方案</span><span class="sxs-lookup"><span data-stu-id="d054e-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="d054e-104">将令牌传递到 Blazor Server 应用</span><span class="sxs-lookup"><span data-stu-id="d054e-104">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="d054e-105">可以使用本节中介绍的方法将 Blazor Server 应用中 Razor 组件外部可用的令牌传递给组件。</span><span class="sxs-lookup"><span data-stu-id="d054e-105">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="d054e-106">与对常规 Razor Pages 或 MVC 应用进行身份验证一样，对 Blazor Server 应用进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="d054e-106">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="d054e-107">预配令牌并将其保存到身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="d054e-107">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="d054e-108">例如：</span><span class="sxs-lookup"><span data-stu-id="d054e-108">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="d054e-109">可选择使用 `options.Scope.Add("{SCOPE}");` 添加其他作用域，其中占位符 `{SCOPE}` 是要添加的其他作用域。</span><span class="sxs-lookup"><span data-stu-id="d054e-109">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="d054e-110">定义可在 Blazor 应用中使用的作用域令牌提供程序服务，以解析[依赖项注入 (DI)](xref:blazor/fundamentals/dependency-injection) 中的令牌：</span><span class="sxs-lookup"><span data-stu-id="d054e-110">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d054e-111">在 `Startup.ConfigureServices` 中，为以下对象添加服务：</span><span class="sxs-lookup"><span data-stu-id="d054e-111">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="d054e-112">定义一个类，以在初始应用状态下使用访问令牌和刷新令牌传递它：</span><span class="sxs-lookup"><span data-stu-id="d054e-112">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d054e-113">在 `_Host.cshtml` 文件中，创建 `InitialApplicationState` 实例，并将其作为参数传递给应用：</span><span class="sxs-lookup"><span data-stu-id="d054e-113">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
```

<span data-ttu-id="d054e-114">在 `App` 组件 (`App.razor`) 中，解析服务并使用参数中的数据对其进行初始化：</span><span class="sxs-lookup"><span data-stu-id="d054e-114">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="d054e-115">为 [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet 包添加对应用的包引用。</span><span class="sxs-lookup"><span data-stu-id="d054e-115">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="d054e-116">在发出安全 API 请求的服务中，注入令牌提供程序并检索 API 请求的令牌：</span><span class="sxs-lookup"><span data-stu-id="d054e-116">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="d054e-117">设置身份验证方案</span><span class="sxs-lookup"><span data-stu-id="d054e-117">Set the authentication scheme</span></span></h2>

<span data-ttu-id="d054e-118">对于使用多个身份验证中间件并因此具有多个身份验证方案的应用，可以在 `Startup.Configure` 的终结点配置中显式设置 Blazor 使用的方案。</span><span class="sxs-lookup"><span data-stu-id="d054e-118">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="d054e-119">下面的示例设置 Azure Active Directory 方案：</span><span class="sxs-lookup"><span data-stu-id="d054e-119">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="d054e-120">将令牌传递到 Blazor Server 应用</span><span class="sxs-lookup"><span data-stu-id="d054e-120">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="d054e-121">可以使用本节中介绍的方法将 Blazor Server 应用中 Razor 组件外部可用的令牌传递给组件。</span><span class="sxs-lookup"><span data-stu-id="d054e-121">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="d054e-122">与对常规 Razor Pages 或 MVC 应用进行身份验证一样，对 Blazor Server 应用进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="d054e-122">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="d054e-123">预配令牌并将其保存到身份验证 cookie。</span><span class="sxs-lookup"><span data-stu-id="d054e-123">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="d054e-124">例如：</span><span class="sxs-lookup"><span data-stu-id="d054e-124">For example:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

<span data-ttu-id="d054e-125">可选择使用 `options.Scope.Add("{SCOPE}");` 添加其他作用域，其中占位符 `{SCOPE}` 是要添加的其他作用域。</span><span class="sxs-lookup"><span data-stu-id="d054e-125">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="d054e-126">可选择使用 `options.Resource = "{RESOURCE}";` 指定资源，其中占位符 `{RESOURCE}` 即为资源。</span><span class="sxs-lookup"><span data-stu-id="d054e-126">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="d054e-127">例如：</span><span class="sxs-lookup"><span data-stu-id="d054e-127">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="d054e-128">定义一个类，以在初始应用状态下使用访问令牌和刷新令牌传递它：</span><span class="sxs-lookup"><span data-stu-id="d054e-128">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d054e-129">定义可在 Blazor 应用中使用的作用域令牌提供程序服务，以解析[依赖项注入 (DI)](xref:blazor/fundamentals/dependency-injection) 中的令牌：</span><span class="sxs-lookup"><span data-stu-id="d054e-129">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d054e-130">在 `Startup.ConfigureServices` 中，为以下对象添加服务：</span><span class="sxs-lookup"><span data-stu-id="d054e-130">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="d054e-131">在 `_Host.cshtml` 文件中，创建 `InitialApplicationState` 实例，并将其作为参数传递给应用：</span><span class="sxs-lookup"><span data-stu-id="d054e-131">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

<span data-ttu-id="d054e-132">在 `App` 组件 (`App.razor`) 中，解析服务并使用参数中的数据对其进行初始化：</span><span class="sxs-lookup"><span data-stu-id="d054e-132">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

<span data-ttu-id="d054e-133">为 [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet 包添加对应用的包引用。</span><span class="sxs-lookup"><span data-stu-id="d054e-133">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="d054e-134">在发出安全 API 请求的服务中，注入令牌提供程序并检索 API 请求的令牌：</span><span class="sxs-lookup"><span data-stu-id="d054e-134">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme"><span data-ttu-id="d054e-135">设置身份验证方案</span><span class="sxs-lookup"><span data-stu-id="d054e-135">Set the authentication scheme</span></span></h2>

<span data-ttu-id="d054e-136">对于使用多个身份验证中间件并因此具有多个身份验证方案的应用，可以在 `Startup.Configure` 的终结点配置中显式设置 Blazor 使用的方案。</span><span class="sxs-lookup"><span data-stu-id="d054e-136">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="d054e-137">下面的示例设置 Azure Active Directory 方案：</span><span class="sxs-lookup"><span data-stu-id="d054e-137">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="d054e-138">使用 OpenID Connect (OIDC) v2.0 终结点</span><span class="sxs-lookup"><span data-stu-id="d054e-138">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="d054e-139">在 5.0 之前的 ASP.NET Core 版本中，身份验证库和 Blazor 模板使用 OpenID Connect (OIDC) v1.0 终结点。</span><span class="sxs-lookup"><span data-stu-id="d054e-139">In versions of ASP.NET Core prior to 5.0, the authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="d054e-140">若要在 5.0 以前的版本中使用 v2.0 终结点，请在 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 中配置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> 选项：</span><span class="sxs-lookup"><span data-stu-id="d054e-140">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="d054e-141">也可以在应用设置 (`appsettings.json`) 文件中进行设置：</span><span class="sxs-lookup"><span data-stu-id="d054e-141">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="d054e-142">如果将段添加到授权不适合应用的 OIDC 提供程序（例如，使用非 AAD 提供程序），则直接设置 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 属性。</span><span class="sxs-lookup"><span data-stu-id="d054e-142">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="d054e-143">使用 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> 键在 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 或应用设置文件中设置属性。</span><span class="sxs-lookup"><span data-stu-id="d054e-143">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="d054e-144">代码更改</span><span class="sxs-lookup"><span data-stu-id="d054e-144">Code changes</span></span>

* <span data-ttu-id="d054e-145">ID 令牌中的声明列表针对 v2.0 终结点会发生更改。</span><span class="sxs-lookup"><span data-stu-id="d054e-145">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="d054e-146">有关详细信息，请参阅[为什么要更新到 Microsoft 标识平台 (v2.0)？](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="d054e-146">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="d054e-147">（位于 Azure 文档中）。</span><span class="sxs-lookup"><span data-stu-id="d054e-147">in the Azure documentation.</span></span>
* <span data-ttu-id="d054e-148">由于在 v2.0 终结点的范围 URI 中指定了资源，请删除 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> 中的 <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> 属性设置：</span><span class="sxs-lookup"><span data-stu-id="d054e-148">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="d054e-149">有关详细信息，请参阅 Azure 文档中的[作用域，非资源](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources)部分。</span><span class="sxs-lookup"><span data-stu-id="d054e-149">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="d054e-150">应用 ID URI</span><span class="sxs-lookup"><span data-stu-id="d054e-150">App ID URI</span></span>

* <span data-ttu-id="d054e-151">使用 v2.0 终结点时，API 会定义一个 `App ID URI`，来表示 API 的唯一标识符。</span><span class="sxs-lookup"><span data-stu-id="d054e-151">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="d054e-152">所有作用域都将应用 ID URI 用作前缀，v2.0 终结点以应用 ID URI 为受众发出访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d054e-152">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="d054e-153">使用 V2.0 终结点时，服务器 API 中配置的客户端 ID 会从 API 应用程序 ID（客户端 ID）更改为应用 ID URI。</span><span class="sxs-lookup"><span data-stu-id="d054e-153">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="d054e-154">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="d054e-154">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="d054e-155">可以在 OIDC 提供程序应用注册说明中找到要使用的应用 ID URI。</span><span class="sxs-lookup"><span data-stu-id="d054e-155">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
