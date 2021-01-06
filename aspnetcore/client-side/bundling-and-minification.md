---
title: ASP.NET Core 中的捆绑和缩小静态资产
author: scottaddie
description: 了解如何通过应用捆绑和缩小技术优化 ASP.NET Core Web 应用程序中的静态资源。
ms.author: scaddie
ms.custom: mvc
ms.date: 09/02/2020
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
uid: client-side/bundling-and-minification
ms.openlocfilehash: 7dd11ceb7a7c01ce1042f50595013b7fe7f1cd5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054835"
---
# <a name="bundle-and-minify-static-assets-in-aspnet-core"></a>ASP.NET Core 中的捆绑和缩小静态资产

作者：[Scott Addie](https://twitter.com/Scott_Addie) 和 [David Pine](https://twitter.com/davidpine7)

本文介绍应用捆绑和缩小的好处，包括如何在 ASP.NET Core Web 应用中使用这些功能。

## <a name="what-is-bundling-and-minification"></a>什么是捆绑和缩小

捆绑和缩小是可以在 Web 应用中应用的两个不同的性能优化。 捆绑和缩小一起使用，可减少服务器的请求数并减小请求的静态资产的大小，从而提高性能。

捆绑和缩小主要缩短第一个页面请求加载时间。 请求网页后，浏览器会缓存静态资产（JavaScript、CSS 和图像）。 因此，在请求相同资产的同一站点上请求相同的一个或多个页面时，捆绑和缩小不会提高性能。 如果未在资产上正确设置 expires 标头，且未使用捆绑和缩小，则浏览器的新鲜度启发会在几天后将资产标记为过期。 此外，浏览器还需要对每个资产进行验证请求。 在这种情况下，即使在第一个页面请求后，捆绑和缩小仍能提高性能。

### <a name="bundling"></a>捆绑

捆绑将多个文件合并到单个文件中。 捆绑可减少呈现 Web 资产（如网页）所需的服务器请求数。 可以专门为 CSS、JavaScript 等创建任意数量的单个捆绑。文件越少，从浏览器到服务器或从提供应用程序的服务的 HTTP 请求就越少。 这会提高第一页加载性能。

### <a name="minification"></a>缩小

缩小在不更改功能的情况下从代码中删除不必要的字符。 因此，请求的资产（如 CSS、图像和 JavaScript 文件）的大小大幅减小。 缩小的常见副作用包括将变量名称缩短为一个字符、删除注释和不必要的空格。

考虑以下 JavaScript 函数：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.js)]

缩小将函数缩减为以下内容：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/js/site.min.js)]

除了删除注释和不必要的空格外，还进行了以下参数和变量名称重命名：

原始 | 重命名
--- | :---:
`imageTagAndImageID` | `t`
`imageContext` | `a`
`imageElement` | `r`

## <a name="impact-of-bundling-and-minification"></a>捆绑和缩小的影响

下表概述了单独加载资产与使用捆绑和缩小之间的差异：

操作 | 使用捆绑/缩小 | 不使用捆绑/缩小 | 更改
--- | :---: | :---: | :---:
文件请求  | 7   | 18     | 157%
传输的 KB | 156 | 264.68 | 70%
加载时间（毫秒） | 885 | 2360   | 167%

对于 HTTP 请求标头，浏览器非常详细。 捆绑时，已发送的总字节数指标明显减少。 加载时间显示了显著改进，但本示例在本地运行。 将捆绑和缩小与通过网络传输的资产结合使用时，可实现更高的性能提升。

## <a name="choose-a-bundling-and-minification-strategy"></a>选择捆绑和缩小策略

MVC 和 Razor Pages 项目模板提供了一种用于捆绑和缩小的解决方案，它们构成 JSON 配置文件。 第三方工具（如 [Grunt](xref:client-side/using-grunt) 任务运行程序）以更复杂的方式完成相同的任务。 开发工作流需要捆绑和缩小之外的其他处理（如 linting 和图像优化）时，第三方工具非常适用。 通过使用设计时捆绑和缩小，在应用部署之前创建缩小文件。 在部署之前进行捆绑和缩小具有减少服务器负载的优点。 但是，必须认识到，设计时捆绑和缩小会增加生成的复杂性，并且仅适用于静态文件。

## <a name="configure-bundling-and-minification"></a>配置捆绑和缩小

> [!NOTE]
> 需要将 [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet 包添加到项目中使其正常工作。

::: moniker range="<= aspnetcore-2.0"

在 ASP.NET Core 2.0 或更早版本中，MVC 和 Razor Pages 项目模板提供了一个 bundleconfig.json 配置文件，该文件定义每个捆绑的选项：

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

在 ASP.NET Core 2.1 或更高版本中，将名为 bundleconfig.json 的新 JSON 文件添加到 MVC 或 Razor Pages 项目根目录。 在该文件中包含以下 JSON 作为起点：

::: moniker-end

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig.json)]

bundleconfig.json 文件定义每个捆绑的选项。 在前面的示例中，为自定义 JavaScript (wwwroot/js/site.js) 和样式表 (wwwroot/css/site.css) 文件定义了单一捆绑配置 。

配置选项包括：

* `outputFileName`：要输出的捆绑文件的名称。 可包含 bundleconfig.json 文件中的相对路径。 （必需）
* `inputFiles`：要捆绑在一起的文件数组。 这些是配置文件的相对路径。 可以选择使用空值，*这将导致输出文件为空。 支持 [glob](https://www.tldp.org/LDP/abs/html/globbingref.html) 模式。
* `minify`：输出类型的缩小选项。 可选，默认值 - `minify: { enabled: true }`
  * 每个输出文件类型都有配置选项。
    * [CSS 缩小程序](https://github.com/madskristensen/BundlerMinifier/wiki/cssminifier)
    * [JavaScript 缩减程序](https://github.com/madskristensen/BundlerMinifier/wiki/JavaScript-Minifier-settings)
    * [HTML 缩小程序](https://github.com/madskristensen/BundlerMinifier/wiki)
* `includeInProject`：指示是否将生成的文件添加到项目文件的标记。 可选，默认值 - false
* `sourceMap`：指示是否为捆绑的文件生成源映射的标记。 可选，默认值 - false
* `sourceMapRootPath`：用于存储所生成的源映射文件的根路径。

## <a name="add-files-to-workflow"></a>向工作流添加文件

假设添加了额外的 custom.css 文件，类似于以下内容：

[!code-css[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/wwwroot/css/custom.css)]

若要缩小 custom.css 并将其与 site.css 捆绑到 site.min.css 文件中，请将相对路径添加到 bundleconfig.json   ：

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/bundleconfig2.json?highlight=6)]

> [!NOTE]
> 或者，可以使用以下通配模式：
>
> ```json
> "inputFiles": ["wwwroot/**/!(*.min).css" ]
> ```
>
> 此通配模式匹配所有 CSS 文件，并排除缩小的文件模式。

生成应用程序。 打开 site.min.css 并注意 custom.css 的内容将追加到文件末尾 。

## <a name="environment-based-bundling-and-minification"></a>基于环境的捆绑和缩小

最佳做法是，应在生产环境中使用应用的捆绑文件和缩小文件。 在开发过程中，原始文件可简化应用的调试。

使用视图中的[环境标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper)指定要包含在页面中的文件。 环境标记帮助程序仅在特定[环境](xref:fundamentals/environments)中运行时呈现其内容。

以下 `environment` 标记将在 `Development` 环境中运行时呈现未处理的 CSS 文件：

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=21-24)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=9-12)]

::: moniker-end

以下 `environment` 标记将在非 `Development` 环境中运行时呈现捆绑的和缩小的 CSS 文件。 例如，在 `Production` 或 `Staging` 中运行将触发这些样式表的呈现：

::: moniker range=">= aspnetcore-2.0"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=5&range=25-30)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-cshtml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/Pages/_Layout.cshtml?highlight=3&range=13-18)]

::: moniker-end

## <a name="consume-bundleconfigjson-from-gulp"></a>从 Gulp 使用 bundleconfig.json

在某些情况下，应用的捆绑和缩小工作流需要额外处理。 示例包括图像优化、缓存清除和 CDN 资产处理。 为了满足这些要求，可以将捆绑和缩小工作流转换为使用 Gulp。

### <a name="manually-convert-the-bundling-and-minification-workflow-to-use-gulp"></a>手动转换捆绑和缩小工作流以使用 Gulp

将 package.json 文件（包含以下 `devDependencies`）添加到项目根：

> [!WARNING]
> `gulp-uglify` 模块不支持 ECMAScript (ES) 2015/ES6 和更高版本。 安装 [gulp-terser](https://www.npmjs.com/package/gulp-terser) 而不是 `gulp-uglify` 来使用 ES2015/ES6 或更高版本。

[!code-json[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/package.json?range=5-13)]

通过在与 package.json 相同的级别运行以下命令来安装依赖项：

```bash
npm i
```

安装 Gulp CLI 作为全局依赖项：

```bash
npm i -g gulp-cli
```

将以下 gulpfile.js 文件复制到项目根：

[!code-javascript[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/gulpfile.js?range=1-11,14-)]

### <a name="run-gulp-tasks"></a>运行 Gulp 任务

若要在 Visual Studio 中生成项目之前触发 Gulp 缩小任务：

1. 安装 [BuildBundlerMinifier](https://www.nuget.org/packages/BuildBundlerMinifier) NuGet 包。
1. 将以下 [MSBuild 目标](/visualstudio/msbuild/msbuild-targets)添加到项目文件：

    [!code-xml[](../client-side/bundling-and-minification/samples/BuildBundlerMinifierApp/BuildBundlerMinifierApp.csproj?range=14-16)]

在此示例中，`MyPreCompileTarget` 目标内定义的所有任务在预定义的 `Build` 目标之前运行。 Visual Studio 的输出窗口中显示类似于以下内容的输出：

```console
1>------ Build started: Project: BuildBundlerMinifierApp, Configuration: Debug Any CPU ------
1>BuildBundlerMinifierApp -> C:\BuildBundlerMinifierApp\bin\Debug\netcoreapp2.0\BuildBundlerMinifierApp.dll
1>[14:17:49] Using gulpfile C:\BuildBundlerMinifierApp\gulpfile.js
1>[14:17:49] Starting 'min:js'...
1>[14:17:49] Starting 'min:css'...
1>[14:17:49] Starting 'min:html'...
1>[14:17:49] Finished 'min:js' after 83 ms
1>[14:17:49] Finished 'min:css' after 88 ms
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
```

## <a name="additional-resources"></a>其他资源

* [使用 Grunt](xref:client-side/using-grunt)
* [使用多个环境](xref:fundamentals/environments)
* [标记帮助程序](xref:mvc/views/tag-helpers/intro)
