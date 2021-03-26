---
title: 配置 ASP.NET Core 数据保护
author: rick-anderson
description: 了解如何在 ASP.NET Core 中配置数据保护。
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 8c00deb49709bd2ec2a5824c841fa3600f99b204
ms.sourcegitcommit: 4bbc69f51c59bed1a96aa46f9f5dca2f2a2634cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2021
ms.locfileid: "105554663"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="0a518-103">配置 ASP.NET Core 数据保护</span><span class="sxs-lookup"><span data-stu-id="0a518-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="0a518-104">当数据保护系统已初始化时，它将基于操作环境应用 [默认设置](xref:security/data-protection/configuration/default-settings) 。</span><span class="sxs-lookup"><span data-stu-id="0a518-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="0a518-105">这些设置通常适用于在一台计算机上运行的应用程序。</span><span class="sxs-lookup"><span data-stu-id="0a518-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="0a518-106">在某些情况下，开发人员可能想要更改默认设置：</span><span class="sxs-lookup"><span data-stu-id="0a518-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="0a518-107">应用程序分布在多台计算机上。</span><span class="sxs-lookup"><span data-stu-id="0a518-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="0a518-108">出于合规性原因。</span><span class="sxs-lookup"><span data-stu-id="0a518-108">For compliance reasons.</span></span>

<span data-ttu-id="0a518-109">在这些情况下，数据保护系统提供了丰富的配置 API。</span><span class="sxs-lookup"><span data-stu-id="0a518-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="0a518-110">与配置文件类似，应使用适当的权限保护数据保护密钥环。</span><span class="sxs-lookup"><span data-stu-id="0a518-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="0a518-111">你可以选择对静态密钥加密，但这不会阻止攻击者创建新密钥。</span><span class="sxs-lookup"><span data-stu-id="0a518-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="0a518-112">因此，应用的安全将受到影响。</span><span class="sxs-lookup"><span data-stu-id="0a518-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="0a518-113">使用数据保护配置的存储位置应该将其访问权限限制为应用本身，这与保护配置文件的方式类似。</span><span class="sxs-lookup"><span data-stu-id="0a518-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="0a518-114">例如，如果选择将密钥环存储在磁盘上，请使用文件系统权限。</span><span class="sxs-lookup"><span data-stu-id="0a518-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="0a518-115">确保你的 web 应用在其下运行的标识具有对该目录的读取、写入和创建访问权限。</span><span class="sxs-lookup"><span data-stu-id="0a518-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="0a518-116">如果使用 Azure Blob 存储，则只有 web 应用应能够在 Blob 存储中读取、写入或创建新条目等。</span><span class="sxs-lookup"><span data-stu-id="0a518-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="0a518-117">扩展方法 [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) 返回 [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)。</span><span class="sxs-lookup"><span data-stu-id="0a518-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="0a518-118">`IDataProtectionBuilder` 公开扩展方法，你可以将这些方法链接在一起来配置数据保护选项。</span><span class="sxs-lookup"><span data-stu-id="0a518-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0a518-119">本文中使用的数据保护扩展插件需要以下 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="0a518-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="0a518-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span><span class="sxs-lookup"><span data-stu-id="0a518-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [<span data-ttu-id="0a518-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span><span class="sxs-lookup"><span data-stu-id="0a518-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="0a518-122">ProtectKeysWithAzureKeyVault</span><span class="sxs-lookup"><span data-stu-id="0a518-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="0a518-123">使用 CLI 登录到 Azure，例如：</span><span class="sxs-lookup"><span data-stu-id="0a518-123">Log in to Azure using the CLI, for example:</span></span>

```azurecli
az login
``` 

<span data-ttu-id="0a518-124">若要在 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)中存储密钥，请在类中配置 [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) 系统 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="0a518-124">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class.</span></span> <span data-ttu-id="0a518-125">`blobUriWithSasToken` 应存储密钥文件的完整 URI。</span><span class="sxs-lookup"><span data-stu-id="0a518-125">`blobUriWithSasToken` is the full URI where the key file should be stored.</span></span> <span data-ttu-id="0a518-126">此 URI 必须包含 SAS 令牌作为查询字符串参数：</span><span class="sxs-lookup"><span data-stu-id="0a518-126">The URI must contain the SAS token as a query string parameter:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
}
```

<span data-ttu-id="0a518-127">将密钥环存储位置设置 (例如 [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)) 。</span><span class="sxs-lookup"><span data-stu-id="0a518-127">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="0a518-128">必须设置位置，因为调用 `ProtectKeysWithAzureKeyVault` 实现了禁用自动数据保护设置的 [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) ，包括密钥环存储位置。</span><span class="sxs-lookup"><span data-stu-id="0a518-128">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="0a518-129">前面的示例使用 Azure Blob 存储来持久保存密钥环。</span><span class="sxs-lookup"><span data-stu-id="0a518-129">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="0a518-130">有关详细信息，请参阅 [密钥存储提供程序： Azure 存储](xref:security/data-protection/implementation/key-storage-providers#azure-storage)。</span><span class="sxs-lookup"><span data-stu-id="0a518-130">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="0a518-131">还可以通过 [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system)将密钥环保存到本地。</span><span class="sxs-lookup"><span data-stu-id="0a518-131">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="0a518-132">`keyIdentifier`是用于密钥加密的密钥保管库密钥标识符。</span><span class="sxs-lookup"><span data-stu-id="0a518-132">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="0a518-133">例如，在中名为的密钥保管库中创建的密钥 `dataprotection` `contosokeyvault` 具有密钥标识符 `https://contosokeyvault.vault.azure.net/keys/dataprotection/` 。</span><span class="sxs-lookup"><span data-stu-id="0a518-133">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="0a518-134">向应用提供对密钥保管库的 **解包密钥** 和 **包装密钥** 权限。</span><span class="sxs-lookup"><span data-stu-id="0a518-134">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="0a518-135">`ProtectKeysWithAzureKeyVault` 重载</span><span class="sxs-lookup"><span data-stu-id="0a518-135">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="0a518-136">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder、Uri、TokenCredential) ](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) 允许使用 keyIdentifier Uri 和 TokenCredential 来使数据保护系统使用密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="0a518-136">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier Uri and a tokenCredential to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="0a518-137">[ProtectKeysWithAzureKeyVault (IDataProtectionBuilder，string，IKeyEncryptionKeyResolver) ](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) 允许使用 keyIdentifier String 和 IKeyEncryptionKeyResolver 使数据保护系统能够使用密钥保管库。</span><span class="sxs-lookup"><span data-stu-id="0a518-137">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, string, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier string and IKeyEncryptionKeyResolver to enable the data protection system to use the key vault.</span></span>

<span data-ttu-id="0a518-138">如果应用使用以前的 Azure 包 ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) 和 [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) 并结合了 Azure Key Vault 和 Azure 存储来存储和保护密钥，则 <xref:System.UriFormatException?displayProperty=nameWithType> 会在密钥存储的 blob 不存在时引发。</span><span class="sxs-lookup"><span data-stu-id="0a518-138">If the app uses the prior Azure packages ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) and [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) and a combination of Azure Key Vault and Azure Storage to store and protect keys, <xref:System.UriFormatException?displayProperty=nameWithType> is thrown if the blob for key storage doesn't exist.</span></span> <span data-ttu-id="0a518-139">可以在 Azure 门户中运行应用之前手动创建 blob，也可以使用以下过程：</span><span class="sxs-lookup"><span data-stu-id="0a518-139">The blob can be manually created ahead of running the app in the Azure portal, or use the following procedure:</span></span>

1. <span data-ttu-id="0a518-140">删除对的调用，以便 `ProtectKeysWithAzureKeyVault` 在首次运行时创建 blob。</span><span class="sxs-lookup"><span data-stu-id="0a518-140">Remove the call to `ProtectKeysWithAzureKeyVault` for the first run to create the blob in place.</span></span>
1. <span data-ttu-id="0a518-141">将对的调用添加到 `ProtectKeysWithAzureKeyVault` 后面的运行。</span><span class="sxs-lookup"><span data-stu-id="0a518-141">Add the call to `ProtectKeysWithAzureKeyVault` for subsequent runs.</span></span>

<span data-ttu-id="0a518-142">`ProtectKeysWithAzureKeyVault`建议删除首次运行，因为它可确保创建的文件具有正确的架构和值。</span><span class="sxs-lookup"><span data-stu-id="0a518-142">Removing `ProtectKeysWithAzureKeyVault` for the first run is advised, as it ensures that the file is created with the proper schema and values in place.</span></span> 

<span data-ttu-id="0a518-143">建议升级到 [AspNetCore DataProtection](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) 和 [AspNetCore](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) 包，因为如果 blob 不存在，则该 API 会自动创建该 blob 的包。</span><span class="sxs-lookup"><span data-stu-id="0a518-143">We recommended upgrading to the [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) and [Azure.Extensions.AspNetCore.DataProtection.Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) packages because the API provided automatically creates the blob if it doesn't exist.</span></span>

```csharp
services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage("<storage account connection string", "<key store container name>", "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
```

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="0a518-144">PersistKeysToFileSystem</span><span class="sxs-lookup"><span data-stu-id="0a518-144">PersistKeysToFileSystem</span></span>

<span data-ttu-id="0a518-145">若要将密钥存储在 UNC 共享上，而不是存储在 *% LOCALAPPDATA%* 默认位置，请使用 [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)配置系统：</span><span class="sxs-lookup"><span data-stu-id="0a518-145">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="0a518-146">如果更改密钥持久性位置，系统将不再自动加密静态密钥，因为它不知道 DPAPI 是否为适当的加密机制。</span><span class="sxs-lookup"><span data-stu-id="0a518-146">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="0a518-147">ProtectKeysWith\*</span><span class="sxs-lookup"><span data-stu-id="0a518-147">ProtectKeysWith\*</span></span>

<span data-ttu-id="0a518-148">可以通过调用任何[ProtectKeysWith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions)配置 api 将系统配置为保护静态密钥。</span><span class="sxs-lookup"><span data-stu-id="0a518-148">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="0a518-149">请考虑以下示例，该示例将密钥存储在 UNC 共享上，并使用特定的 x.509 证书对静态密钥进行加密：</span><span class="sxs-lookup"><span data-stu-id="0a518-149">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(Configuration["Thumbprint"]);
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0a518-150">在 ASP.NET Core 2.1 或更高版本中，你可以提供[ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)的[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) ，例如从文件中加载的证书：</span><span class="sxs-lookup"><span data-stu-id="0a518-150">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", Configuration["Thumbprint"]));
}
```

::: moniker-end

<span data-ttu-id="0a518-151">有关内置密钥加密机制的更多示例和讨论，请参阅 [静态密钥加密](xref:security/data-protection/implementation/key-encryption-at-rest) 。</span><span class="sxs-lookup"><span data-stu-id="0a518-151">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="0a518-152">UnprotectKeysWithAnyCertificate</span><span class="sxs-lookup"><span data-stu-id="0a518-152">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="0a518-153">在 ASP.NET Core 2.1 或更高版本中，你可以使用[UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)的[X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)</span><span class="sxs-lookup"><span data-stu-id="0a518-153">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", Configuration["MyPasswordKey"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", Configuration["MyPasswordKey_1"),
            new X509Certificate2("certificate_old_2.pfx", Configuration["MyPasswordKey_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="0a518-154">SetDefaultKeyLifetime</span><span class="sxs-lookup"><span data-stu-id="0a518-154">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="0a518-155">若要将系统配置为使用14天的密钥生存期而不是默认的90天，请使用 [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime)：</span><span class="sxs-lookup"><span data-stu-id="0a518-155">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="0a518-156">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="0a518-156">SetApplicationName</span></span>

<span data-ttu-id="0a518-157">默认情况下，数据保护系统基于其 [内容根](xref:fundamentals/index#content-root) 路径将应用彼此隔离，即使它们共享相同的物理密钥存储库。</span><span class="sxs-lookup"><span data-stu-id="0a518-157">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="0a518-158">这可防止应用了解彼此的受保护负载。</span><span class="sxs-lookup"><span data-stu-id="0a518-158">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="0a518-159">在应用之间共享受保护的负载：</span><span class="sxs-lookup"><span data-stu-id="0a518-159">To share protected payloads among apps:</span></span>

* <span data-ttu-id="0a518-160"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>在每个应用中配置相同的值。</span><span class="sxs-lookup"><span data-stu-id="0a518-160">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="0a518-161">在应用程序中使用相同版本的数据保护 API 堆栈。</span><span class="sxs-lookup"><span data-stu-id="0a518-161">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="0a518-162">在应用的项目文件中执行以下 **任一** 操作：</span><span class="sxs-lookup"><span data-stu-id="0a518-162">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="0a518-163">通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)引用相同的共享框架版本。</span><span class="sxs-lookup"><span data-stu-id="0a518-163">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="0a518-164">引用相同的 [数据保护包](xref:security/data-protection/introduction#package-layout) 版本。</span><span class="sxs-lookup"><span data-stu-id="0a518-164">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="0a518-165">DisableAutomaticKeyGeneration</span><span class="sxs-lookup"><span data-stu-id="0a518-165">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="0a518-166">你可能会遇到这样的情况：你不希望应用程序自动滚动更新密钥 (创建新密钥) ，因为它们接近过期。</span><span class="sxs-lookup"><span data-stu-id="0a518-166">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="0a518-167">这种情况的一个示例可能是在主/辅助关系中设置的应用，其中只有主应用负责密钥管理问题，辅助应用只是具有密钥环的只读视图。</span><span class="sxs-lookup"><span data-stu-id="0a518-167">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="0a518-168">可以将辅助应用配置为使用以下配置系统将密钥环视为只读 <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> ：</span><span class="sxs-lookup"><span data-stu-id="0a518-168">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="0a518-169">每应用程序隔离</span><span class="sxs-lookup"><span data-stu-id="0a518-169">Per-application isolation</span></span>

<span data-ttu-id="0a518-170">当数据保护系统由 ASP.NET Core 主机提供时，它会自动将应用彼此隔离，即使这些应用在相同的工作进程帐户下运行并且使用相同的主密钥材料也是如此。</span><span class="sxs-lookup"><span data-stu-id="0a518-170">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="0a518-171">这有点类似于 System.web 元素中的 IsolateApps 修饰符 `<machineKey>` 。</span><span class="sxs-lookup"><span data-stu-id="0a518-171">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="0a518-172">隔离机制的工作原理是将本地计算机上的每个应用视为唯一的租户，因此， <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> 任何给定应用的根都将自动包括应用 ID 作为鉴别器。</span><span class="sxs-lookup"><span data-stu-id="0a518-172">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="0a518-173">应用的唯一 ID 是应用的物理路径：</span><span class="sxs-lookup"><span data-stu-id="0a518-173">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="0a518-174">对于在 IIS 中托管的应用，唯一 ID 是应用的 IIS 物理路径。</span><span class="sxs-lookup"><span data-stu-id="0a518-174">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="0a518-175">如果在 web 场环境中部署了应用，则此值是稳定的，假定在 web 场中的所有计算机上配置了类似的 IIS 环境。</span><span class="sxs-lookup"><span data-stu-id="0a518-175">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="0a518-176">对于在 [Kestrel 服务器](xref:fundamentals/servers/index#kestrel)上运行的自承载应用程序，唯一 ID 是指向磁盘上的应用程序的物理路径。</span><span class="sxs-lookup"><span data-stu-id="0a518-176">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="0a518-177">唯一标识符旨在重置 &mdash; 单独的应用程序和计算机本身。</span><span class="sxs-lookup"><span data-stu-id="0a518-177">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="0a518-178">此隔离机制假定应用不是恶意的。</span><span class="sxs-lookup"><span data-stu-id="0a518-178">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="0a518-179">恶意应用始终会影响在同一工作进程帐户下运行的任何其他应用。</span><span class="sxs-lookup"><span data-stu-id="0a518-179">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="0a518-180">在应用不受信任的共享主机环境中，托管提供商应采取措施来确保应用之间的操作系统级隔离，包括分离应用程序的底层密钥存储库。</span><span class="sxs-lookup"><span data-stu-id="0a518-180">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="0a518-181">如果 ASP.NET Core 主机未提供数据保护系统 (例如，如果通过具体类型对其进行实例化 `DataProtectionProvider`) 则默认情况下禁用应用程序隔离。</span><span class="sxs-lookup"><span data-stu-id="0a518-181">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="0a518-182">禁用应用隔离后，只要提供相应的 [用途](xref:security/data-protection/consumer-apis/purpose-strings)，同一密钥材料支持的所有应用就可以共享有效负载。</span><span class="sxs-lookup"><span data-stu-id="0a518-182">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="0a518-183">若要在此环境中提供应用隔离，请对配置对象调用 [SetApplicationName](#setapplicationname) 方法，并为每个应用提供唯一的名称。</span><span class="sxs-lookup"><span data-stu-id="0a518-183">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="0a518-184">用 UseCryptographicAlgorithms 更改算法</span><span class="sxs-lookup"><span data-stu-id="0a518-184">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="0a518-185">数据保护堆栈允许您更改新生成的密钥使用的默认算法。</span><span class="sxs-lookup"><span data-stu-id="0a518-185">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="0a518-186">执行此操作的最简单方法是从配置回调调用 [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) ：</span><span class="sxs-lookup"><span data-stu-id="0a518-186">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="0a518-187">默认 EncryptionAlgorithm 为 AES-256-CBC，默认 ValidationAlgorithm 为 HMACSHA256。</span><span class="sxs-lookup"><span data-stu-id="0a518-187">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="0a518-188">系统管理员可以通过 [计算机范围内的策略](xref:security/data-protection/configuration/machine-wide-policy)来设置默认策略，但会对 `UseCryptographicAlgorithms` 替代默认策略进行显式调用。</span><span class="sxs-lookup"><span data-stu-id="0a518-188">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="0a518-189">通过调用， `UseCryptographicAlgorithms` 可以从预定义的内置列表中指定所需的算法。</span><span class="sxs-lookup"><span data-stu-id="0a518-189">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="0a518-190">您无需担心算法的实现。</span><span class="sxs-lookup"><span data-stu-id="0a518-190">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="0a518-191">在上述方案中，如果在 Windows 上运行，数据保护系统将尝试使用 AES 的 CNG 实现。</span><span class="sxs-lookup"><span data-stu-id="0a518-191">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="0a518-192">否则，它会回退到托管的[系统。](/dotnet/api/system.security.cryptography.aes)</span><span class="sxs-lookup"><span data-stu-id="0a518-192">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="0a518-193">可以通过调用 [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)手动指定实现。</span><span class="sxs-lookup"><span data-stu-id="0a518-193">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="0a518-194">更改算法不会影响密钥环中的现有密钥。</span><span class="sxs-lookup"><span data-stu-id="0a518-194">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="0a518-195">它仅影响新生成的键。</span><span class="sxs-lookup"><span data-stu-id="0a518-195">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="0a518-196">指定自定义托管算法</span><span class="sxs-lookup"><span data-stu-id="0a518-196">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0a518-197">若要指定自定义托管算法，请创建一个指向实现类型的 [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) 实例：</span><span class="sxs-lookup"><span data-stu-id="0a518-197">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="0a518-198">若要指定自定义托管算法，请创建一个指向实现类型的 [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) 实例：</span><span class="sxs-lookup"><span data-stu-id="0a518-198">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="0a518-199">通常， \* 类型属性必须指向具体的可实例化 (通过 [System.security.cryptography.symmetricalgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) 和 [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)的公共无参数的) ctor 实现，尽管系统特别适用于一些值， `typeof(Aes)` 以便于方便。</span><span class="sxs-lookup"><span data-stu-id="0a518-199">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="0a518-200">System.security.cryptography.symmetricalgorithm 必须具有≥128位的密钥长度和≥64位的块大小，并且必须支持 PKCS #7 填充的 CBC 模式加密。</span><span class="sxs-lookup"><span data-stu-id="0a518-200">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="0a518-201">KeyedHashAlgorithm 的摘要大小必须为 >= 128 位，并且它必须支持长度等于哈希算法摘要长度的键。</span><span class="sxs-lookup"><span data-stu-id="0a518-201">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="0a518-202">KeyedHashAlgorithm 不一定是 HMAC。</span><span class="sxs-lookup"><span data-stu-id="0a518-202">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="0a518-203">指定自定义 Windows CNG 算法</span><span class="sxs-lookup"><span data-stu-id="0a518-203">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0a518-204">若要通过 HMAC 验证使用 CBC 模式加密指定自定义 Windows CNG 算法，请创建包含算法信息的 [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) 实例：</span><span class="sxs-lookup"><span data-stu-id="0a518-204">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="0a518-205">若要通过 HMAC 验证使用 CBC 模式加密指定自定义 Windows CNG 算法，请创建包含算法信息的 [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) 实例：</span><span class="sxs-lookup"><span data-stu-id="0a518-205">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="0a518-206">对称块加密算法的密钥长度必须为 >= 128 位，块大小为 >= 64 位，并且它必须支持 PKCS #7 填充的 CBC 模式加密。</span><span class="sxs-lookup"><span data-stu-id="0a518-206">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="0a518-207">哈希算法的摘要大小必须为 >= 128 位，并且必须支持使用 BCRYPT \_ ALG \_ 句柄 \_ HMAC \_ 标志标志打开。</span><span class="sxs-lookup"><span data-stu-id="0a518-207">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="0a518-208">\*提供程序属性可以设置为 null，以将默认提供程序用于指定的算法。</span><span class="sxs-lookup"><span data-stu-id="0a518-208">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="0a518-209">有关详细信息，请参阅 [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) 文档。</span><span class="sxs-lookup"><span data-stu-id="0a518-209">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="0a518-210">若要使用 Galois/Counter 模式加密和验证来指定自定义 Windows CNG 算法，请创建包含算法信息的 [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) 实例：</span><span class="sxs-lookup"><span data-stu-id="0a518-210">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="0a518-211">若要使用 Galois/Counter 模式加密和验证来指定自定义 Windows CNG 算法，请创建包含算法信息的 [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) 实例：</span><span class="sxs-lookup"><span data-stu-id="0a518-211">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="0a518-212">对称块密码算法的密钥长度必须为 >= 128 位，块大小正好为128位，并且必须支持 GCM 加密。</span><span class="sxs-lookup"><span data-stu-id="0a518-212">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="0a518-213">可以将 [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) 属性设置为 null，以将默认提供程序用于指定的算法。</span><span class="sxs-lookup"><span data-stu-id="0a518-213">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="0a518-214">有关详细信息，请参阅 [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) 文档。</span><span class="sxs-lookup"><span data-stu-id="0a518-214">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="0a518-215">指定其他自定义算法</span><span class="sxs-lookup"><span data-stu-id="0a518-215">Specifying other custom algorithms</span></span>

<span data-ttu-id="0a518-216">尽管不是作为第一类 API 公开的，但数据保护系统的扩展能力足以允许指定几乎任何类型的算法。</span><span class="sxs-lookup"><span data-stu-id="0a518-216">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="0a518-217">例如，可以将硬件安全模块中包含的所有密钥保留 (HSM) ，并提供核心加密和解密例程的自定义实现。</span><span class="sxs-lookup"><span data-stu-id="0a518-217">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="0a518-218">有关详细信息，请参阅[核心加密扩展性](xref:security/data-protection/extensibility/core-crypto)中的[IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) 。</span><span class="sxs-lookup"><span data-stu-id="0a518-218">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="0a518-219">在 Docker 容器中托管时保持密钥</span><span class="sxs-lookup"><span data-stu-id="0a518-219">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="0a518-220">在 [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) 容器中托管时，应在以下任一项中维护密钥：</span><span class="sxs-lookup"><span data-stu-id="0a518-220">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="0a518-221">一个文件夹，它是在容器的生存期之外保留的 Docker 卷，如共享卷或主机装入的卷。</span><span class="sxs-lookup"><span data-stu-id="0a518-221">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="0a518-222">外部提供程序，如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 或 [Redis](https://redis.io/)。</span><span class="sxs-lookup"><span data-stu-id="0a518-222">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="0a518-223">通过 Redis 保持密钥</span><span class="sxs-lookup"><span data-stu-id="0a518-223">Persisting keys with Redis</span></span>

<span data-ttu-id="0a518-224">只应使用支持 [Redis 数据暂留](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) 的 Redis 版本来存储密钥。</span><span class="sxs-lookup"><span data-stu-id="0a518-224">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="0a518-225">[Azure Blob 存储](/azure/storage/blobs/storage-blobs-introduction) 是持久性的，可用于存储密钥。</span><span class="sxs-lookup"><span data-stu-id="0a518-225">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="0a518-226">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/13476)。</span><span class="sxs-lookup"><span data-stu-id="0a518-226">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0a518-227">其他资源</span><span class="sxs-lookup"><span data-stu-id="0a518-227">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
