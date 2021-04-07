---
title: 使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: 了解如何使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 4f3c07f2698dd68fbb80c5f34a3f3df62225e3b3
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105554676"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="ab493-103">使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="ab493-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="ab493-104">本文介绍如何创建使用 [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) 进行身份验证的[独立 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)。</span><span class="sxs-lookup"><span data-stu-id="ab493-104">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="ab493-105">对于在 Visual Studio 中创建且配置为支持 AAD 组织目录中帐户的 Blazor WebAssembly 应用，Visual Studio 当前不会在项目生成时正确配置 Azure 门户应用注册。</span><span class="sxs-lookup"><span data-stu-id="ab493-105">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="ab493-106">以后的 Visual Studio 版本将解决此问题。</span><span class="sxs-lookup"><span data-stu-id="ab493-106">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="ab493-107">本文介绍如何使用 .NET CLI `dotnet new` 命令创建解决方案和 Azure 应用门户注册，以及在 Azure 门户中手动创建应用注册。</span><span class="sxs-lookup"><span data-stu-id="ab493-107">This article shows how to create the solution and Azure app portal registration with the .NET CLI `dotnet new` command and by manually creating the app registration in the Azure portal.</span></span>
>
> <span data-ttu-id="ab493-108">如果希望在更新 IDE 之前使用 Visual Studio 创建解决方案和 Azure 应用注册，请参阅本文的各个部分，并在 Visual Studio 创建解决方案后确认或更新应用的配置和应用的注册。</span><span class="sxs-lookup"><span data-stu-id="ab493-108">If you prefer to create the solution and Azure app registration with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the app's configuration and the app's registration after Visual Studio creates the solution.</span></span>

<span data-ttu-id="ab493-109">注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="ab493-109">Register an AAD app:</span></span>

1. <span data-ttu-id="ab493-110">在 Azure 门户中，导航到“Active Directory”。</span><span class="sxs-lookup"><span data-stu-id="ab493-110">Navigate to **Azure Active Directory** in the Azure portal.</span></span> <span data-ttu-id="ab493-111">在边栏中选择“应用注册”。</span><span class="sxs-lookup"><span data-stu-id="ab493-111">Select **App registrations** in the sidebar.</span></span> <span data-ttu-id="ab493-112">选择“新建注册”按钮。</span><span class="sxs-lookup"><span data-stu-id="ab493-112">Select the **New registration** button.</span></span>
1. <span data-ttu-id="ab493-113">提供应用的名称（例如 Blazor 独立 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="ab493-113">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="ab493-114">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="ab493-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="ab493-115">为此体验选择“仅此组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="ab493-115">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="ab493-116">将“重定向 URI”下拉列表设置为“单页应用程序(SPA)”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback`。</span><span class="sxs-lookup"><span data-stu-id="ab493-116">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="ab493-117">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="ab493-117">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="ab493-118">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="ab493-118">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="ab493-119">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="ab493-119">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="ab493-120">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="ab493-120">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="ab493-121">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="ab493-121">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="ab493-122">如果使用[未经验证的发布者域](/azure/active-directory/develop/howto-configure-publisher-domain)，请清除“权限” > “授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="ab493-122">If you're using an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span> <span data-ttu-id="ab493-123">如果验证了发布者域，则不会出现此复选框。</span><span class="sxs-lookup"><span data-stu-id="ab493-123">If the publisher domain is verified, this check box isn't present.</span></span>
1. <span data-ttu-id="ab493-124">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="ab493-124">Select **Register**.</span></span>

<span data-ttu-id="ab493-125">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="ab493-125">Record the following information:</span></span>

* <span data-ttu-id="ab493-126">应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="ab493-126">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="ab493-127">目录（租户）ID（例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`）</span><span class="sxs-lookup"><span data-stu-id="ab493-127">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="ab493-128">在“身份验证”>“平台配置”>“单页应用程序(SPA)”中：</span><span class="sxs-lookup"><span data-stu-id="ab493-128">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="ab493-129">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="ab493-129">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="ab493-130">对于“隐式授权”，请确保没有选中“访问令牌”和“ID 令牌”的复选框。</span><span class="sxs-lookup"><span data-stu-id="ab493-130">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="ab493-131">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="ab493-131">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="ab493-132">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="ab493-132">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="ab493-133">注册 AAD 应用：</span><span class="sxs-lookup"><span data-stu-id="ab493-133">Register an AAD app:</span></span>

1. <span data-ttu-id="ab493-134">在 Azure 门户中，导航到“Active Directory”。</span><span class="sxs-lookup"><span data-stu-id="ab493-134">Navigate to **Azure Active Directory** in the Azure portal.</span></span> <span data-ttu-id="ab493-135">在边栏中选择“应用注册”。</span><span class="sxs-lookup"><span data-stu-id="ab493-135">Select **App registrations** in the sidebar.</span></span> <span data-ttu-id="ab493-136">选择“新建注册”按钮。</span><span class="sxs-lookup"><span data-stu-id="ab493-136">Select the **New registration** button.</span></span>
1. <span data-ttu-id="ab493-137">提供应用的名称（例如 Blazor 独立 AAD） 。</span><span class="sxs-lookup"><span data-stu-id="ab493-137">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="ab493-138">选择支持的帐户类型。</span><span class="sxs-lookup"><span data-stu-id="ab493-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="ab493-139">为此体验选择“仅此组织目录中的帐户”。</span><span class="sxs-lookup"><span data-stu-id="ab493-139">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="ab493-140">将“重定向 URI”下拉列表设置为“Web”，并提供以下重定向 URI：`https://localhost:{PORT}/authentication/login-callback` 。</span><span class="sxs-lookup"><span data-stu-id="ab493-140">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="ab493-141">在 Kestrel 上运行的应用的默认端口为 5001。</span><span class="sxs-lookup"><span data-stu-id="ab493-141">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="ab493-142">如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。</span><span class="sxs-lookup"><span data-stu-id="ab493-142">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="ab493-143">对于 IIS Express，可以在“调试”面板的应用属性中找到该应用随机生成的端口。</span><span class="sxs-lookup"><span data-stu-id="ab493-143">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="ab493-144">由于此时应用不存在，并且 IIS Express 端口未知，因此请在创建应用后返回到此步骤，然后更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="ab493-144">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="ab493-145">本主题后面部分会显示一个注解，以提醒 IIS Express 用户更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="ab493-145">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="ab493-146">如果使用[未经验证的发布者域](/azure/active-directory/develop/howto-configure-publisher-domain)，请清除“权限” > “授予对 openid 和 offline_access 权限的管理员同意”复选框。</span><span class="sxs-lookup"><span data-stu-id="ab493-146">If you're using an [unverified publisher domain](/azure/active-directory/develop/howto-configure-publisher-domain), clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span> <span data-ttu-id="ab493-147">如果验证了发布者域，则不会出现此复选框。</span><span class="sxs-lookup"><span data-stu-id="ab493-147">If the publisher domain is verified, this check box isn't present.</span></span>
1. <span data-ttu-id="ab493-148">选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="ab493-148">Select **Register**.</span></span>

<span data-ttu-id="ab493-149">记录以下信息：</span><span class="sxs-lookup"><span data-stu-id="ab493-149">Record the following information:</span></span>

* <span data-ttu-id="ab493-150">应用程序（客户端）ID（例如 `41451fa7-82d9-4673-8fa5-69eff5a761fd`）</span><span class="sxs-lookup"><span data-stu-id="ab493-150">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="ab493-151">目录（租户）ID（例如 `e86c78e2-8bb4-4c41-aefd-918e0565a45e`）</span><span class="sxs-lookup"><span data-stu-id="ab493-151">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="ab493-152">在“身份验证”>“平台配置”>“Web”中：</span><span class="sxs-lookup"><span data-stu-id="ab493-152">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="ab493-153">确认存在 `https://localhost:{PORT}/authentication/login-callback` 的重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="ab493-153">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="ab493-154">对于“隐式授权”，选中“访问令牌”和“ID 令牌”的复选框  。</span><span class="sxs-lookup"><span data-stu-id="ab493-154">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="ab493-155">此体验可接受应用的其余默认值。</span><span class="sxs-lookup"><span data-stu-id="ab493-155">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="ab493-156">选择“保存”按钮。</span><span class="sxs-lookup"><span data-stu-id="ab493-156">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="ab493-157">在空文件夹中创建应用。</span><span class="sxs-lookup"><span data-stu-id="ab493-157">Create the app in an empty folder.</span></span> <span data-ttu-id="ab493-158">将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行该命令：</span><span class="sxs-lookup"><span data-stu-id="ab493-158">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="ab493-159">占位符</span><span class="sxs-lookup"><span data-stu-id="ab493-159">Placeholder</span></span>   | <span data-ttu-id="ab493-160">Azure 门户中的名称</span><span class="sxs-lookup"><span data-stu-id="ab493-160">Azure portal name</span></span>       | <span data-ttu-id="ab493-161">示例</span><span class="sxs-lookup"><span data-stu-id="ab493-161">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="ab493-162">应用程序（客户端）ID</span><span class="sxs-lookup"><span data-stu-id="ab493-162">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="ab493-163">目录（租户）ID</span><span class="sxs-lookup"><span data-stu-id="ab493-163">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="ab493-164">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="ab493-164">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="ab493-165">在 Azure 门户中，使用默认设置为在 Kestrel 服务器上运行的应用的端口 5001 配置应用的平台配置“重定向 URI”。</span><span class="sxs-lookup"><span data-stu-id="ab493-165">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="ab493-166">如果应用是在随机 IIS Express 端口上运行的，则可以在“调试”面板的应用属性中找到该应用的端口。</span><span class="sxs-lookup"><span data-stu-id="ab493-166">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="ab493-167">如果端口之前未使用应用的已知端口进行配置，请返回到 Azure 门户中应用的注册，并使用正确的端口更新重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="ab493-167">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="ab493-168">创建应用后，应该能够：</span><span class="sxs-lookup"><span data-stu-id="ab493-168">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="ab493-169">使用 AAD 用户帐户登录到应用。</span><span class="sxs-lookup"><span data-stu-id="ab493-169">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="ab493-170">请求 Microsoft API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="ab493-170">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="ab493-171">有关详情，请参阅：</span><span class="sxs-lookup"><span data-stu-id="ab493-171">For more information, see:</span></span>
  * [<span data-ttu-id="ab493-172">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="ab493-172">Access token scopes</span></span>](#access-token-scopes)
  * [<span data-ttu-id="ab493-173">快速入门：配置应用程序以公开 Web API</span><span class="sxs-lookup"><span data-stu-id="ab493-173">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
  * [<span data-ttu-id="ab493-174">使用 AAD 托管：访问令牌范围（包含有关 AAD `App ID URI` 范围格式的指导）</span><span class="sxs-lookup"><span data-stu-id="ab493-174">Hosted with AAD: Access token scopes (includes guidance on AAD `App ID URI` scope formats)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory#access-token-scopes)
  * [<span data-ttu-id="ab493-175">Microsoft Graph API 的访问令牌范围</span><span class="sxs-lookup"><span data-stu-id="ab493-175">Access token scopes for Microsoft Graph API</span></span>](xref:blazor/security/webassembly/graph-api)

## <a name="authentication-package"></a><span data-ttu-id="ab493-176">身份验证包</span><span class="sxs-lookup"><span data-stu-id="ab493-176">Authentication package</span></span>

<span data-ttu-id="ab493-177">创建应用以使用工作或学校帐户 (`SingleOrg`) 时，应用会自动接收 [Microsoft 身份验证库](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="ab493-177">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="ab493-178">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="ab493-178">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="ab493-179">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="ab493-179">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="ab493-180">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="ab493-180">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="ab493-181">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包会间接将 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包传递到应用中。</span><span class="sxs-lookup"><span data-stu-id="ab493-181">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="ab493-182">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="ab493-182">Authentication service support</span></span>

<span data-ttu-id="ab493-183">使用由 [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="ab493-183">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="ab493-184">此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="ab493-184">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="ab493-185">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="ab493-185">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="ab493-186"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> 方法接受回叫，以配置验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="ab493-186">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="ab493-187">注册应用时，可以从 AAD 配置中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="ab493-187">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="ab493-188">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="ab493-188">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="ab493-189">示例：</span><span class="sxs-lookup"><span data-stu-id="ab493-189">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="ab493-190">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="ab493-190">Access token scopes</span></span>

<span data-ttu-id="ab493-191">Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="ab493-191">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="ab493-192">要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> 的默认访问令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="ab493-192">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="ab493-193">使用 `AdditionalScopesToConsent` 指定其他作用域：</span><span class="sxs-lookup"><span data-stu-id="ab493-193">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="ab493-194">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="ab493-194">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="ab493-195">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="ab493-195">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="ab493-196">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="ab493-196">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="ab493-197">登录模式</span><span class="sxs-lookup"><span data-stu-id="ab493-197">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="ab493-198">导入文件</span><span class="sxs-lookup"><span data-stu-id="ab493-198">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="ab493-199">索引页</span><span class="sxs-lookup"><span data-stu-id="ab493-199">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="ab493-200">应用组件</span><span class="sxs-lookup"><span data-stu-id="ab493-200">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="ab493-201">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="ab493-201">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="ab493-202">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="ab493-202">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="ab493-203">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="ab493-203">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="ab493-204">其他资源</span><span class="sxs-lookup"><span data-stu-id="ab493-204">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="ab493-205">生成 Authentication.MSAL JavaScript 库的自定义版本</span><span class="sxs-lookup"><span data-stu-id="ab493-205">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="ab493-206">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="ab493-206">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="ab493-207">Microsoft 标识平台文档</span><span class="sxs-lookup"><span data-stu-id="ab493-207">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
