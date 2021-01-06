---
title: ASP.NET Core 全球化和本地化
author: rick-anderson
description: 了解 ASP.NET Core 如何提供服务和中间件，将内容本地化为不同的语言和区域性。
ms.author: riande
ms.date: 11/30/2019
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
uid: fundamentals/localization
ms.openlocfilehash: 07e2f561b0e9db58780d6e8a271e32b00132b1b5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059515"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="17982-103">ASP.NET Core 全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="17982-103">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="17982-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="17982-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="17982-105">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="17982-105">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="17982-106">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="17982-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="17982-107">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="17982-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="17982-108">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="17982-109">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="17982-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="17982-110">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="17982-111">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="17982-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="17982-112">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="17982-112">App localization involves the following:</span></span>

1. <span data-ttu-id="17982-113">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17982-113">Make the app's content localizable</span></span>
1. <span data-ttu-id="17982-114">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17982-114">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="17982-115">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17982-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="17982-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="17982-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="17982-117">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17982-117">Make the app's content localizable</span></span>

<span data-ttu-id="17982-118">已为 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 设置架构，可以为开发本地化应用提高工作效率。</span><span class="sxs-lookup"><span data-stu-id="17982-118"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="17982-119">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和 <xref:System.Resources.ResourceReader> 在运行时提供特定于区域性的资源。</span><span class="sxs-lookup"><span data-stu-id="17982-119">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="17982-120">接口具有一个索引器和一个用于返回本地化字符串的 `IEnumerable`。</span><span class="sxs-lookup"><span data-stu-id="17982-120">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="17982-121">`IStringLocalizer` 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-121">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="17982-122">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="17982-123">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17982-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="17982-124">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="17982-124">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="17982-125">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17982-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="17982-126">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="17982-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="17982-127">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="17982-128">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="17982-129">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="17982-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="17982-130">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="17982-131">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="17982-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="17982-132">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="17982-133">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="17982-134">通常，仅本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17982-134">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="17982-135">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="17982-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="17982-136">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="17982-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="17982-137">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="17982-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="17982-138">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="17982-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="17982-139">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="17982-140">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="17982-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="17982-141">视图本地化</span><span class="sxs-lookup"><span data-stu-id="17982-141">View localization</span></span>

<span data-ttu-id="17982-142">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="17982-143">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="17982-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="17982-144">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="17982-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="17982-145">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="17982-146">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="17982-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="17982-147">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="17982-148">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="17982-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="17982-149">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="17982-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="17982-150">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="17982-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="17982-151">键</span><span class="sxs-lookup"><span data-stu-id="17982-151">Key</span></span> | <span data-ttu-id="17982-152">“值”</span><span class="sxs-lookup"><span data-stu-id="17982-152">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="17982-153">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="17982-153">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="17982-154">通常，仅本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17982-154">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="17982-155">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="17982-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="17982-156">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="17982-156">DataAnnotations localization</span></span>

<span data-ttu-id="17982-157">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="17982-158">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="17982-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="17982-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="17982-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="17982-161">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="17982-162">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="17982-163">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="17982-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="17982-164">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="17982-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="17982-165">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="17982-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="17982-166">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="17982-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="17982-167">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17982-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="17982-168">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="17982-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="17982-169">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="17982-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="17982-170">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="17982-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="17982-171">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="17982-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="17982-172">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="17982-173">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="17982-173">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="17982-174">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="17982-175">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="17982-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="17982-176">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="17982-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="17982-177">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="17982-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="17982-178">资源文件</span><span class="sxs-lookup"><span data-stu-id="17982-178">Resource files</span></span>

<span data-ttu-id="17982-179">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="17982-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="17982-180">非默认语言的转换字符串在 .resx 资源文件中单独显示。</span><span class="sxs-lookup"><span data-stu-id="17982-180">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="17982-181">例如，你可能想要创建包含转换字符串、名为 Welcome.es.resx 的西班牙语资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="17982-182">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="17982-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="17982-183">要在 Visual Studio 中创建此资源文件，请支持以下操作：</span><span class="sxs-lookup"><span data-stu-id="17982-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="17982-184">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="17982-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/newi.png)

1. <span data-ttu-id="17982-187">在“搜索已安装的模板”框中，输入“资源”并命名该文件。</span><span class="sxs-lookup"><span data-stu-id="17982-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![“添加新项”对话框](localization/_static/res.png)

1. <span data-ttu-id="17982-189">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="17982-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列](localization/_static/hola.png)

   <span data-ttu-id="17982-191">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17982-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![显示 Welcome Spanish (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="17982-193">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="17982-193">Resource file naming</span></span>

<span data-ttu-id="17982-194">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="17982-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="17982-195">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="17982-196">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-197">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="17982-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="17982-198">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="17982-199">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-200">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="17982-201">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-202">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="17982-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="17982-203">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-204">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="17982-205">资源名称</span><span class="sxs-lookup"><span data-stu-id="17982-205">Resource name</span></span> | <span data-ttu-id="17982-206">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="17982-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="17982-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="17982-208">圆点</span><span class="sxs-lookup"><span data-stu-id="17982-208">Dot</span></span>  |
| <span data-ttu-id="17982-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="17982-210">路径</span><span class="sxs-lookup"><span data-stu-id="17982-210">Path</span></span> |

<span data-ttu-id="17982-211">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="17982-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="17982-212">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="17982-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="17982-213">Razor 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="17982-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="17982-214">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="17982-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="17982-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="17982-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="17982-217">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="17982-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="17982-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="17982-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="17982-219">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="17982-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="17982-220">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="17982-220">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="17982-221">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="17982-221">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="17982-222">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="17982-222">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="17982-223">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-223">Localization does not work by default.</span></span>
* <span data-ttu-id="17982-224">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="17982-224">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="17982-225">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="17982-225">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="17982-226">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="17982-226">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="17982-227">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17982-227">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="17982-228">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="17982-228">Culture fallback behavior</span></span>

<span data-ttu-id="17982-229">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="17982-229">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="17982-230">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-230">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="17982-231">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-231">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="17982-232">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="17982-232">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="17982-233">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="17982-233">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="17982-234">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="17982-234">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="17982-235">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="17982-235">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="17982-236">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="17982-236">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="17982-237">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-237">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="17982-238">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-238">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="17982-239">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="17982-239">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="17982-240">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-240">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="17982-241">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="17982-241">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="17982-242">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-242">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="17982-243">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="17982-243">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="17982-244">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="17982-244">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="17982-245">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="17982-245">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="17982-246">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="17982-246">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="17982-247">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="17982-247">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="17982-248">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="17982-248">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="17982-249">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="17982-249">Add other cultures</span></span>

<span data-ttu-id="17982-250">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-250">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="17982-251">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="17982-251">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="17982-252">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="17982-252">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="17982-253">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17982-253">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="17982-254">配置本地化</span><span class="sxs-lookup"><span data-stu-id="17982-254">Configure localization</span></span>

<span data-ttu-id="17982-255">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="17982-255">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="17982-256">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="17982-256">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="17982-257">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="17982-257">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="17982-258">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="17982-258">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="17982-259">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="17982-259">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="17982-260">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="17982-260">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="17982-261">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="17982-261">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="17982-262">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="17982-262">Localization middleware</span></span>

<span data-ttu-id="17982-263">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-263">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="17982-264">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="17982-264">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="17982-265">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="17982-265">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="17982-266">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="17982-266">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="17982-267">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-267">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="17982-268">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="17982-268">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="17982-269">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="17982-269">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="17982-270">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-270">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="17982-271">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="17982-271">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="17982-272">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17982-272">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="17982-273">某些应用将使用查询字符串来设置<https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>。</span><span class="sxs-lookup"><span data-stu-id="17982-273">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="17982-274">对于使用 cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="17982-274">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="17982-275">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-275">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="17982-276">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="17982-276">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="17982-277">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="17982-277">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="17982-278">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="17982-278">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="17982-279">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="17982-279">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="17982-280">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17982-280">CookieRequestCultureProvider</span></span>

<span data-ttu-id="17982-281">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-281">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="17982-282">若要创建 cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="17982-282">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="17982-283">`CookieRequestCultureProvider` `DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="17982-283">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="17982-284">默认的 cookie 名称为 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="17982-284">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="17982-285">cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中 `c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="17982-285">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="17982-286">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-286">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="17982-287">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17982-287">The Accept-Language HTTP header</span></span>

<span data-ttu-id="17982-288">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="17982-288">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="17982-289">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="17982-289">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="17982-290">浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。</span><span class="sxs-lookup"><span data-stu-id="17982-290">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="17982-291">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="17982-291">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="17982-292">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17982-292">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="17982-293">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="17982-293">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="17982-294">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="17982-294">Tap **Languages**.</span></span>

   ![Internet 选项](localization/_static/lang.png)

1. <span data-ttu-id="17982-296">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="17982-296">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="17982-297">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="17982-297">Tap **Add a language**.</span></span>

1. <span data-ttu-id="17982-298">添加语言。</span><span class="sxs-lookup"><span data-stu-id="17982-298">Add the language.</span></span>

1. <span data-ttu-id="17982-299">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="17982-299">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="17982-300">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="17982-300">Use a custom provider</span></span>

<span data-ttu-id="17982-301">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-301">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="17982-302">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="17982-302">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="17982-303">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="17982-303">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="17982-304">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-304">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="17982-305">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="17982-305">Set the culture programmatically</span></span>

<span data-ttu-id="17982-306">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="17982-306">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="17982-307">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="17982-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="17982-308">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="17982-308">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="17982-309">`SetLanguage` 方法可设置区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="17982-309">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="17982-310">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="17982-310">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="17982-311">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="17982-311">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="17982-312">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="17982-312">Model binding route data and query strings</span></span>

<span data-ttu-id="17982-313">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="17982-313">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="17982-314">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="17982-314">Globalization and localization terms</span></span>

<span data-ttu-id="17982-315">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="17982-315">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="17982-316">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="17982-316">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="17982-317">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="17982-317">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="17982-318">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-318">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="17982-319">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="17982-319">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="17982-320">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="17982-320">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="17982-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="17982-321">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="17982-322">请参阅 <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>。</span><span class="sxs-lookup"><span data-stu-id="17982-322">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="17982-323">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="17982-323">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="17982-324">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="17982-324">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="17982-325">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="17982-325">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="17982-326">术语：</span><span class="sxs-lookup"><span data-stu-id="17982-326">Terms:</span></span>

* <span data-ttu-id="17982-327">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-327">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="17982-328">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-328">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="17982-329">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-329">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="17982-330">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="17982-330">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="17982-331">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-331">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="17982-332">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="17982-332">(for example "en", "es")</span></span>
* <span data-ttu-id="17982-333">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-333">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="17982-334">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="17982-334">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="17982-335">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-335">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="17982-336">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="17982-336">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="17982-337">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="17982-337">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="17982-338">其他资源</span><span class="sxs-lookup"><span data-stu-id="17982-338">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="17982-339">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="17982-339">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="17982-340">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="17982-340">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="17982-341">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="17982-341">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="17982-342">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="17982-342">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="17982-343">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="17982-343">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17982-344">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="17982-344">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="17982-345">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="17982-345">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="17982-346">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="17982-346">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="17982-347">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="17982-347">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="17982-348">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-348">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="17982-349">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="17982-349">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="17982-350">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-350">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="17982-351">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="17982-351">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="17982-352">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="17982-352">App localization involves the following:</span></span>

1. <span data-ttu-id="17982-353">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17982-353">Make the app's content localizable</span></span>
1. <span data-ttu-id="17982-354">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17982-354">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="17982-355">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17982-355">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="17982-356">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="17982-356">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="17982-357">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17982-357">Make the app's content localizable</span></span>

<span data-ttu-id="17982-358">已为 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 设置架构，可以为开发本地化应用提高工作效率。</span><span class="sxs-lookup"><span data-stu-id="17982-358"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="17982-359">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和 <xref:System.Resources.ResourceReader> 在运行时提供特定于区域性的资源。</span><span class="sxs-lookup"><span data-stu-id="17982-359">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="17982-360">接口具有一个索引器和一个用于返回本地化字符串的 `IEnumerable`。</span><span class="sxs-lookup"><span data-stu-id="17982-360">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="17982-361">`IStringLocalizer` 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-361">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="17982-362">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-362">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="17982-363">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17982-363">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="17982-364">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="17982-364">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="17982-365">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17982-365">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="17982-366">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="17982-366">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="17982-367">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-367">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="17982-368">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-368">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="17982-369">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="17982-369">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="17982-370">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-370">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="17982-371">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="17982-371">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="17982-372">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-372">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="17982-373">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-373">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="17982-374">通常，仅本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17982-374">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="17982-375">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="17982-375">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="17982-376">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="17982-376">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="17982-377">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="17982-377">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="17982-378">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="17982-378">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="17982-379">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-379">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="17982-380">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="17982-380">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="17982-381">视图本地化</span><span class="sxs-lookup"><span data-stu-id="17982-381">View localization</span></span>

<span data-ttu-id="17982-382">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-382">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="17982-383">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="17982-383">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="17982-384">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="17982-384">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="17982-385">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-385">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="17982-386">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="17982-386">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="17982-387">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-387">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="17982-388">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="17982-388">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="17982-389">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="17982-389">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="17982-390">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="17982-390">A French resource file could contain the following:</span></span>

| <span data-ttu-id="17982-391">键</span><span class="sxs-lookup"><span data-stu-id="17982-391">Key</span></span> | <span data-ttu-id="17982-392">“值”</span><span class="sxs-lookup"><span data-stu-id="17982-392">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="17982-393">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="17982-393">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="17982-394">通常，仅本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17982-394">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="17982-395">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="17982-395">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="17982-396">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="17982-396">DataAnnotations localization</span></span>

<span data-ttu-id="17982-397">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-397">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="17982-398">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="17982-398">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="17982-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-399">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="17982-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-400">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="17982-401">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-401">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="17982-402">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-402">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="17982-403">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="17982-403">Using one resource string for multiple classes</span></span>

<span data-ttu-id="17982-404">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="17982-404">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="17982-405">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="17982-405">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="17982-406">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="17982-406">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="17982-407">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17982-407">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="17982-408">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="17982-408">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="17982-409">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="17982-409">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="17982-410">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="17982-410">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="17982-411">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="17982-411">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="17982-412">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-412">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="17982-413">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="17982-413">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="17982-414">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-414">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="17982-415">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="17982-415">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="17982-416">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="17982-416">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="17982-417">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="17982-417">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="17982-418">资源文件</span><span class="sxs-lookup"><span data-stu-id="17982-418">Resource files</span></span>

<span data-ttu-id="17982-419">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="17982-419">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="17982-420">非默认语言的转换字符串在 .resx 资源文件中单独显示。</span><span class="sxs-lookup"><span data-stu-id="17982-420">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="17982-421">例如，你可能想要创建包含转换字符串、名为 Welcome.es.resx 的西班牙语资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-421">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="17982-422">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="17982-422">"es" is the language code for Spanish.</span></span> <span data-ttu-id="17982-423">要在 Visual Studio 中创建此资源文件，请支持以下操作：</span><span class="sxs-lookup"><span data-stu-id="17982-423">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="17982-424">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="17982-424">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/newi.png)

1. <span data-ttu-id="17982-427">在“搜索已安装的模板”框中，输入“资源”并命名该文件。</span><span class="sxs-lookup"><span data-stu-id="17982-427">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![“添加新项”对话框](localization/_static/res.png)

1. <span data-ttu-id="17982-429">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="17982-429">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列](localization/_static/hola.png)

   <span data-ttu-id="17982-431">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17982-431">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![显示 Welcome Spanish (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="17982-433">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="17982-433">Resource file naming</span></span>

<span data-ttu-id="17982-434">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="17982-434">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="17982-435">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-435">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="17982-436">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-436">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-437">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="17982-437">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="17982-438">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-438">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="17982-439">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-439">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-440">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-440">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="17982-441">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-441">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-442">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="17982-442">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="17982-443">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-443">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-444">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-444">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="17982-445">资源名称</span><span class="sxs-lookup"><span data-stu-id="17982-445">Resource name</span></span> | <span data-ttu-id="17982-446">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="17982-446">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="17982-447">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-447">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="17982-448">圆点</span><span class="sxs-lookup"><span data-stu-id="17982-448">Dot</span></span>  |
| <span data-ttu-id="17982-449">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-449">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="17982-450">路径</span><span class="sxs-lookup"><span data-stu-id="17982-450">Path</span></span> |

<span data-ttu-id="17982-451">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="17982-451">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="17982-452">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="17982-452">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="17982-453">Razor 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="17982-453">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="17982-454">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="17982-454">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="17982-455">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-455">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="17982-456">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-456">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="17982-457">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="17982-457">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="17982-458">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="17982-458">RootNamespaceAttribute</span></span> 

<span data-ttu-id="17982-459">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="17982-459">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="17982-460">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="17982-460">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="17982-461">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="17982-461">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="17982-462">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="17982-462">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="17982-463">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-463">Localization does not work by default.</span></span>
* <span data-ttu-id="17982-464">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="17982-464">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="17982-465">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="17982-465">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="17982-466">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="17982-466">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="17982-467">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17982-467">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="17982-468">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="17982-468">Culture fallback behavior</span></span>

<span data-ttu-id="17982-469">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="17982-469">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="17982-470">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-470">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="17982-471">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-471">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="17982-472">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="17982-472">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="17982-473">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="17982-473">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="17982-474">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="17982-474">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="17982-475">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="17982-475">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="17982-476">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="17982-476">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="17982-477">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-477">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="17982-478">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-478">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="17982-479">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="17982-479">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="17982-480">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-480">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="17982-481">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="17982-481">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="17982-482">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-482">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="17982-483">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="17982-483">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="17982-484">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="17982-484">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="17982-485">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="17982-485">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="17982-486">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="17982-486">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="17982-487">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="17982-487">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="17982-488">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="17982-488">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="17982-489">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="17982-489">Add other cultures</span></span>

<span data-ttu-id="17982-490">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-490">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="17982-491">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="17982-491">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="17982-492">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="17982-492">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="17982-493">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17982-493">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="17982-494">配置本地化</span><span class="sxs-lookup"><span data-stu-id="17982-494">Configure localization</span></span>

<span data-ttu-id="17982-495">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="17982-495">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="17982-496">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="17982-496">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="17982-497">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="17982-497">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="17982-498">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="17982-498">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="17982-499">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="17982-499">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="17982-500">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="17982-500">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="17982-501">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="17982-501">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="17982-502">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="17982-502">Localization middleware</span></span>

<span data-ttu-id="17982-503">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-503">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="17982-504">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="17982-504">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="17982-505">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="17982-505">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="17982-506">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="17982-506">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="17982-507">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-507">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="17982-508">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="17982-508">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="17982-509">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="17982-509">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="17982-510">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-510">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="17982-511">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="17982-511">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="17982-512">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17982-512">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="17982-513">某些应用将使用查询字符串来设置<https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>。</span><span class="sxs-lookup"><span data-stu-id="17982-513">Some apps will use a query string to set the <https://docs.microsoft.com/dotnet/api/system.globalization.cultureinfo?view=netcore-3.1>.</span></span> <span data-ttu-id="17982-514">对于使用 cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="17982-514">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="17982-515">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-515">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="17982-516">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="17982-516">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="17982-517">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="17982-517">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="17982-518">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="17982-518">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="17982-519">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="17982-519">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="17982-520">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17982-520">CookieRequestCultureProvider</span></span>

<span data-ttu-id="17982-521">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-521">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="17982-522">若要创建 cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="17982-522">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="17982-523">`CookieRequestCultureProvider` `DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="17982-523">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="17982-524">默认的 cookie 名称为 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="17982-524">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="17982-525">cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中 `c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="17982-525">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="17982-526">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-526">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="17982-527">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17982-527">The Accept-Language HTTP header</span></span>

<span data-ttu-id="17982-528">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="17982-528">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="17982-529">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="17982-529">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="17982-530">浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。</span><span class="sxs-lookup"><span data-stu-id="17982-530">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="17982-531">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="17982-531">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="17982-532">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17982-532">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="17982-533">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="17982-533">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="17982-534">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="17982-534">Tap **Languages**.</span></span>

   ![Internet 选项](localization/_static/lang.png)

1. <span data-ttu-id="17982-536">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="17982-536">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="17982-537">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="17982-537">Tap **Add a language**.</span></span>

1. <span data-ttu-id="17982-538">添加语言。</span><span class="sxs-lookup"><span data-stu-id="17982-538">Add the language.</span></span>

1. <span data-ttu-id="17982-539">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="17982-539">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="17982-540">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="17982-540">Use a custom provider</span></span>

<span data-ttu-id="17982-541">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-541">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="17982-542">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="17982-542">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="17982-543">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="17982-543">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="17982-544">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-544">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="17982-545">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="17982-545">Set the culture programmatically</span></span>

<span data-ttu-id="17982-546">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="17982-546">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="17982-547">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="17982-547">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="17982-548">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="17982-548">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="17982-549">`SetLanguage` 方法可设置区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="17982-549">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="17982-550">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="17982-550">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="17982-551">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="17982-551">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="17982-552">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="17982-552">Model binding route data and query strings</span></span>

<span data-ttu-id="17982-553">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="17982-553">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="17982-554">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="17982-554">Globalization and localization terms</span></span>

<span data-ttu-id="17982-555">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="17982-555">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="17982-556">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="17982-556">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="17982-557">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="17982-557">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="17982-558">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-558">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="17982-559">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="17982-559">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="17982-560">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="17982-560">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="17982-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="17982-561">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="17982-562">请参阅 <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>。</span><span class="sxs-lookup"><span data-stu-id="17982-562">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="17982-563">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="17982-563">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="17982-564">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="17982-564">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="17982-565">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="17982-565">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="17982-566">术语：</span><span class="sxs-lookup"><span data-stu-id="17982-566">Terms:</span></span>

* <span data-ttu-id="17982-567">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-567">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="17982-568">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-568">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="17982-569">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-569">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="17982-570">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="17982-570">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="17982-571">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-571">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="17982-572">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="17982-572">(for example "en", "es")</span></span>
* <span data-ttu-id="17982-573">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-573">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="17982-574">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="17982-574">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="17982-575">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-575">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="17982-576">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="17982-576">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="17982-577">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="17982-577">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="17982-578">其他资源</span><span class="sxs-lookup"><span data-stu-id="17982-578">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="17982-579">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="17982-579">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="17982-580">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="17982-580">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="17982-581">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="17982-581">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="17982-582">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="17982-582">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="17982-583">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="17982-583">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="17982-584">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="17982-584">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="17982-585">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="17982-585">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="17982-586">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="17982-586">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="17982-587">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="17982-587">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="17982-588">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-588">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="17982-589">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="17982-589">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="17982-590">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-590">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="17982-591">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="17982-591">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="17982-592">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="17982-592">App localization involves the following:</span></span>

1. <span data-ttu-id="17982-593">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17982-593">Make the app's content localizable</span></span>
1. <span data-ttu-id="17982-594">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17982-594">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="17982-595">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17982-595">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="17982-596">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="17982-596">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="17982-597">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17982-597">Make the app's content localizable</span></span>

<span data-ttu-id="17982-598">已为 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 设置架构，可以为开发本地化应用提高工作效率。</span><span class="sxs-lookup"><span data-stu-id="17982-598"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="17982-599">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和 <xref:System.Resources.ResourceReader> 在运行时提供特定于区域性的资源。</span><span class="sxs-lookup"><span data-stu-id="17982-599">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="17982-600">接口具有一个索引器和一个用于返回本地化字符串的 `IEnumerable`。</span><span class="sxs-lookup"><span data-stu-id="17982-600">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="17982-601">`IStringLocalizer` 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-601">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="17982-602">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-602">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="17982-603">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17982-603">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="17982-604">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="17982-604">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="17982-605">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17982-605">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="17982-606">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="17982-606">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="17982-607">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-607">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="17982-608">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-608">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="17982-609">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="17982-609">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="17982-610">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-610">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="17982-611">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="17982-611">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="17982-612">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-612">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="17982-613">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-613">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

> [!NOTE]
> <span data-ttu-id="17982-614">通常，仅本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17982-614">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="17982-615">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="17982-615">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="17982-616">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="17982-616">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="17982-617">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="17982-617">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="17982-618">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="17982-618">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/SharedResource.cs)]

<span data-ttu-id="17982-619">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-619">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="17982-620">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="17982-620">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="17982-621">视图本地化</span><span class="sxs-lookup"><span data-stu-id="17982-621">View localization</span></span>

<span data-ttu-id="17982-622">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-622">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="17982-623">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="17982-623">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="17982-624">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="17982-624">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="17982-625">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-625">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="17982-626">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="17982-626">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="17982-627">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17982-627">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="17982-628">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="17982-628">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="17982-629">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="17982-629">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="17982-630">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="17982-630">A French resource file could contain the following:</span></span>

| <span data-ttu-id="17982-631">键</span><span class="sxs-lookup"><span data-stu-id="17982-631">Key</span></span> | <span data-ttu-id="17982-632">“值”</span><span class="sxs-lookup"><span data-stu-id="17982-632">Value</span></span> |
| --- | ----- |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="17982-633">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="17982-633">The rendered view would contain the HTML markup from the resource file.</span></span>

> [!NOTE]
> <span data-ttu-id="17982-634">通常，仅本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17982-634">Generally, only localize text, not HTML.</span></span>

<span data-ttu-id="17982-635">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="17982-635">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="17982-636">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="17982-636">DataAnnotations localization</span></span>

<span data-ttu-id="17982-637">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-637">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="17982-638">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="17982-638">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="17982-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-639">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="17982-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-640">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="17982-641">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-641">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="17982-642">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-642">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="17982-643">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="17982-643">Using one resource string for multiple classes</span></span>

<span data-ttu-id="17982-644">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="17982-644">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="17982-645">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="17982-645">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="17982-646">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="17982-646">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="17982-647">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17982-647">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="17982-648">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="17982-648">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="17982-649">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="17982-649">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="17982-650">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="17982-650">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="17982-651">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="17982-651">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="17982-652">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-652">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="17982-653">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="17982-653">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="17982-654">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-654">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="17982-655">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="17982-655">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="17982-656">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="17982-656">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="17982-657">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="17982-657">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="17982-658">资源文件</span><span class="sxs-lookup"><span data-stu-id="17982-658">Resource files</span></span>

<span data-ttu-id="17982-659">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="17982-659">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="17982-660">非默认语言的转换字符串在 .resx 资源文件中单独显示。</span><span class="sxs-lookup"><span data-stu-id="17982-660">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="17982-661">例如，你可能想要创建包含转换字符串、名为 Welcome.es.resx 的西班牙语资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-661">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="17982-662">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="17982-662">"es" is the language code for Spanish.</span></span> <span data-ttu-id="17982-663">要在 Visual Studio 中创建此资源文件，请支持以下操作：</span><span class="sxs-lookup"><span data-stu-id="17982-663">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="17982-664">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="17982-664">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

   ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/newi.png)

1. <span data-ttu-id="17982-667">在“搜索已安装的模板”框中，输入“资源”并命名该文件。</span><span class="sxs-lookup"><span data-stu-id="17982-667">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

   ![“添加新项”对话框](localization/_static/res.png)

1. <span data-ttu-id="17982-669">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="17982-669">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

   ![Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列](localization/_static/hola.png)

   <span data-ttu-id="17982-671">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17982-671">Visual Studio shows the *Welcome.es.resx* file.</span></span>

   ![显示 Welcome Spanish (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="17982-673">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="17982-673">Resource file naming</span></span>

<span data-ttu-id="17982-674">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="17982-674">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="17982-675">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-675">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="17982-676">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-676">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-677">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="17982-677">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="17982-678">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-678">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="17982-679">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-679">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-680">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-680">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="17982-681">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-681">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-682">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="17982-682">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="17982-683">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17982-683">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17982-684">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-684">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="17982-685">资源名称</span><span class="sxs-lookup"><span data-stu-id="17982-685">Resource name</span></span> | <span data-ttu-id="17982-686">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="17982-686">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="17982-687">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-687">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="17982-688">圆点</span><span class="sxs-lookup"><span data-stu-id="17982-688">Dot</span></span>  |
| <span data-ttu-id="17982-689">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-689">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="17982-690">路径</span><span class="sxs-lookup"><span data-stu-id="17982-690">Path</span></span> |

<span data-ttu-id="17982-691">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="17982-691">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="17982-692">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="17982-692">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="17982-693">Razor 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="17982-693">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="17982-694">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="17982-694">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="17982-695">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-695">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="17982-696">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17982-696">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="17982-697">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="17982-697">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="17982-698">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="17982-698">RootNamespaceAttribute</span></span> 

<span data-ttu-id="17982-699">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="17982-699">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="17982-700">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="17982-700">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="17982-701">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="17982-701">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="17982-702">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="17982-702">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="17982-703">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-703">Localization does not work by default.</span></span>
* <span data-ttu-id="17982-704">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="17982-704">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="17982-705">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="17982-705">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="17982-706">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="17982-706">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="17982-707">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17982-707">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="17982-708">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="17982-708">Culture fallback behavior</span></span>

<span data-ttu-id="17982-709">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="17982-709">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="17982-710">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-710">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="17982-711">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-711">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="17982-712">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="17982-712">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="17982-713">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="17982-713">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="17982-714">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="17982-714">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="17982-715">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="17982-715">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="17982-716">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="17982-716">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="17982-717">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-717">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="17982-718">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17982-718">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="17982-719">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="17982-719">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="17982-720">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17982-720">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="17982-721">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="17982-721">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="17982-722">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-722">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="17982-723">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="17982-723">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="17982-724">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="17982-724">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="17982-725">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="17982-725">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="17982-726">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="17982-726">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="17982-727">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="17982-727">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="17982-728">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="17982-728">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="17982-729">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="17982-729">Add other cultures</span></span>

<span data-ttu-id="17982-730">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="17982-730">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="17982-731">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="17982-731">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="17982-732">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="17982-732">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="17982-733">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17982-733">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="17982-734">配置本地化</span><span class="sxs-lookup"><span data-stu-id="17982-734">Configure localization</span></span>

<span data-ttu-id="17982-735">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="17982-735">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="17982-736">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="17982-736">`AddLocalization` adds the localization services to the services container.</span></span> <span data-ttu-id="17982-737">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="17982-737">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="17982-738">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="17982-738">`AddViewLocalization` adds support for localized view files.</span></span> <span data-ttu-id="17982-739">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="17982-739">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="17982-740">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="17982-740">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="17982-741">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="17982-741">`AddDataAnnotationsLocalization` adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="17982-742">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="17982-742">Localization middleware</span></span>

<span data-ttu-id="17982-743">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-743">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="17982-744">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="17982-744">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="17982-745">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="17982-745">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="17982-746">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="17982-746">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="17982-747">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-747">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="17982-748">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="17982-748">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
1. `CookieRequestCultureProvider`
1. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="17982-749">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="17982-749">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="17982-750">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-750">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="17982-751">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="17982-751">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="17982-752">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17982-752">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="17982-753">某些应用将使用查询字符串来设置<xref:System.Globalization.CultureInfo>。</span><span class="sxs-lookup"><span data-stu-id="17982-753">Some apps will use a query string to set the <xref:System.Globalization.CultureInfo>.</span></span> <span data-ttu-id="17982-754">对于使用 cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="17982-754">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="17982-755">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-755">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="17982-756">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="17982-756">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="17982-757">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="17982-757">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

```
http://localhost:5000/?culture=es-MX&ui-culture=es-MX
```

<span data-ttu-id="17982-758">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="17982-758">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="17982-759">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="17982-759">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

```
http://localhost:5000/?culture=es-MX
```

### <a name="no-loccookierequestcultureprovider"></a><span data-ttu-id="17982-760">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17982-760">CookieRequestCultureProvider</span></span>

<span data-ttu-id="17982-761">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-761">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="17982-762">若要创建 cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="17982-762">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="17982-763">`CookieRequestCultureProvider` `DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="17982-763">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="17982-764">默认的 cookie 名称为 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="17982-764">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="17982-765">cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中 `c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="17982-765">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

```
c=en-UK|uic=en-US
```

<span data-ttu-id="17982-766">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-766">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="17982-767">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17982-767">The Accept-Language HTTP header</span></span>

<span data-ttu-id="17982-768">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="17982-768">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="17982-769">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="17982-769">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="17982-770">浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。</span><span class="sxs-lookup"><span data-stu-id="17982-770">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="17982-771">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="17982-771">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="17982-772">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17982-772">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="17982-773">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="17982-773">From the gear icon, tap **Internet Options**.</span></span>

1. <span data-ttu-id="17982-774">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="17982-774">Tap **Languages**.</span></span>

   ![Internet 选项](localization/_static/lang.png)

1. <span data-ttu-id="17982-776">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="17982-776">Tap **Set Language Preferences**.</span></span>

1. <span data-ttu-id="17982-777">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="17982-777">Tap **Add a language**.</span></span>

1. <span data-ttu-id="17982-778">添加语言。</span><span class="sxs-lookup"><span data-stu-id="17982-778">Add the language.</span></span>

1. <span data-ttu-id="17982-779">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="17982-779">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="17982-780">Content-Language HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="17982-780">The Content-Language HTTP header</span></span>

<span data-ttu-id="17982-781">[Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) 实体标头：</span><span class="sxs-lookup"><span data-stu-id="17982-781">The [Content-Language](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

* <span data-ttu-id="17982-782">用于描述面向受众的语言。</span><span class="sxs-lookup"><span data-stu-id="17982-782">Is used to describe the language(s) intended for the audience.</span></span>
* <span data-ttu-id="17982-783">允许用户根据用户的首选语言来区分。</span><span class="sxs-lookup"><span data-stu-id="17982-783">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="17982-784">实体标头用于 HTTP 请求和响应。</span><span class="sxs-lookup"><span data-stu-id="17982-784">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="17982-785">可通过设置属性 `ApplyCurrentCultureToResponseHeaders` 来添加 `Content-Language` 标头。</span><span class="sxs-lookup"><span data-stu-id="17982-785">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="17982-786">添加 `Content-Language` 标头：</span><span class="sxs-lookup"><span data-stu-id="17982-786">Adding the `Content-Language` header:</span></span>

* <span data-ttu-id="17982-787">允许 RequestLocalizationMiddleware 使用 `CurrentUICulture` 设置 `Content-Language` 标头。</span><span class="sxs-lookup"><span data-stu-id="17982-787">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
* <span data-ttu-id="17982-788">无需显式设置响应标头 `Content-Language`。</span><span class="sxs-lookup"><span data-stu-id="17982-788">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="17982-789">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="17982-789">Use a custom provider</span></span>

<span data-ttu-id="17982-790">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-790">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="17982-791">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="17982-791">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="17982-792">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="17982-792">The following code shows how to add a custom provider:</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="17982-793">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17982-793">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="17982-794">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="17982-794">Set the culture programmatically</span></span>

<span data-ttu-id="17982-795">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="17982-795">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="17982-796">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="17982-796">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="17982-797">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="17982-797">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="17982-798">`SetLanguage` 方法可设置区域性 cookie。</span><span class="sxs-lookup"><span data-stu-id="17982-798">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="17982-799">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="17982-799">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="17982-800">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="17982-800">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="17982-801">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="17982-801">Model binding route data and query strings</span></span>

<span data-ttu-id="17982-802">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="17982-802">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="17982-803">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="17982-803">Globalization and localization terms</span></span>

<span data-ttu-id="17982-804">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="17982-804">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="17982-805">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="17982-805">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="17982-806">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="17982-806">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="17982-807">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-807">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="17982-808">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="17982-808">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="17982-809">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="17982-809">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="17982-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="17982-810">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="17982-811">请参阅 <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>。</span><span class="sxs-lookup"><span data-stu-id="17982-811">See <https://docs.microsoft.com/previous-versions/commerce-server/ee825488(v=cs.20)>.</span></span>

<span data-ttu-id="17982-812">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="17982-812">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="17982-813">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="17982-813">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="17982-814">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="17982-814">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="17982-815">术语：</span><span class="sxs-lookup"><span data-stu-id="17982-815">Terms:</span></span>

* <span data-ttu-id="17982-816">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-816">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="17982-817">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="17982-817">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="17982-818">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="17982-818">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="17982-819">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="17982-819">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="17982-820">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-820">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="17982-821">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="17982-821">(for example "en", "es")</span></span>
* <span data-ttu-id="17982-822">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-822">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="17982-823">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="17982-823">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="17982-824">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="17982-824">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="17982-825">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="17982-825">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="17982-826">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="17982-826">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="17982-827">其他资源</span><span class="sxs-lookup"><span data-stu-id="17982-827">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="17982-828">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="17982-828">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="17982-829">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="17982-829">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="17982-830">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="17982-830">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="17982-831">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="17982-831">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="17982-832">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="17982-832">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
