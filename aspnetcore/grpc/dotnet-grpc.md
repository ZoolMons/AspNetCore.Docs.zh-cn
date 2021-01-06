---
title: 通过 dotnet-grpc 管理 Protobuf 参考
author: juntaoluo
description: 了解如何通过 dotnet-grpc 全局工具添加、更新、删除和列出 Protobuf 引用。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
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
uid: grpc/dotnet-grpc
ms.openlocfilehash: f34e1543d9695e138a85db3b79e013cf5fb6d138
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059905"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a><span data-ttu-id="efc84-103">通过 dotnet-grpc 管理 Protobuf 参考</span><span class="sxs-lookup"><span data-stu-id="efc84-103">Manage Protobuf references with dotnet-grpc</span></span>

<span data-ttu-id="efc84-104">作者：[John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="efc84-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="efc84-105">`dotnet-grpc` 是一种 .NET Core 全局工具，用于在 .NET gRPC 项目中管理 [Protobuf (.proto)](xref:grpc/basics#proto-file) 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-105">`dotnet-grpc` is a .NET Core Global Tool for managing [Protobuf (*.proto*)](xref:grpc/basics#proto-file) references within a .NET gRPC project.</span></span> <span data-ttu-id="efc84-106">该工具可以用于添加、刷新、删除和列出 Protobuf 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-106">The tool can be used to add, refresh, remove, and list Protobuf references.</span></span>

## <a name="installation"></a><span data-ttu-id="efc84-107">安装</span><span class="sxs-lookup"><span data-stu-id="efc84-107">Installation</span></span>

<span data-ttu-id="efc84-108">若要安装 `dotnet-grpc` [.NET Core 全局工具](/dotnet/core/tools/global-tools)，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="efc84-108">To install the `dotnet-grpc` [.NET Core Global Tool](/dotnet/core/tools/global-tools), run the following command:</span></span>

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a><span data-ttu-id="efc84-109">添加引用</span><span class="sxs-lookup"><span data-stu-id="efc84-109">Add references</span></span>

<span data-ttu-id="efc84-110">`dotnet-grpc` 可以用于将 Protobuf 引用作为 `<Protobuf />` 项添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="efc84-110">`dotnet-grpc` can be used to add Protobuf references as `<Protobuf />` items to the *.csproj* file:</span></span>

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

<span data-ttu-id="efc84-111">Protobuf 引用用于生成 C# 客户端和/或服务器资产。</span><span class="sxs-lookup"><span data-stu-id="efc84-111">The Protobuf references are used to generate the C# client and/or server assets.</span></span> <span data-ttu-id="efc84-112">`dotnet-grpc` 工具可以：</span><span class="sxs-lookup"><span data-stu-id="efc84-112">The `dotnet-grpc` tool can:</span></span>

* <span data-ttu-id="efc84-113">从磁盘上的本地文件创建 Protobuf 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-113">Create a Protobuf reference from local files on disk.</span></span>
* <span data-ttu-id="efc84-114">从 URL 指定的远程文件创建 Protobuf 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-114">Create a Protobuf reference from a remote file specified by a URL.</span></span>
* <span data-ttu-id="efc84-115">确保将正确的 gRPC 包依赖项添加到项目。</span><span class="sxs-lookup"><span data-stu-id="efc84-115">Ensure the correct gRPC package dependencies are added to the project.</span></span>

<span data-ttu-id="efc84-116">例如，将 `Grpc.AspNetCore` 包添加到 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="efc84-116">For example, the `Grpc.AspNetCore` package is added to a web app.</span></span> <span data-ttu-id="efc84-117">`Grpc.AspNetCore` 包含 gRPC 服务器和客户端库以及工具支持。</span><span class="sxs-lookup"><span data-stu-id="efc84-117">`Grpc.AspNetCore` contains gRPC server and client libraries and tooling support.</span></span> <span data-ttu-id="efc84-118">或者，将 `Grpc.Net.Client`、`Grpc.Tools` 和 `Google.Protobuf` 包（其中仅包含 gRPC 客户端库和工具支持）添加到控制台应用。</span><span class="sxs-lookup"><span data-stu-id="efc84-118">Alternatively, the `Grpc.Net.Client`, `Grpc.Tools` and `Google.Protobuf` packages, which contain only the gRPC client libraries and tooling support, are added to a Console app.</span></span>

### <a name="add-file"></a><span data-ttu-id="efc84-119">添加文件</span><span class="sxs-lookup"><span data-stu-id="efc84-119">Add file</span></span>

<span data-ttu-id="efc84-120">`add-file` 命令用于将磁盘上的本地文件添加为 Protobuf 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-120">The `add-file` command is used to add local files on disk as Protobuf references.</span></span> <span data-ttu-id="efc84-121">提供的文件路径：</span><span class="sxs-lookup"><span data-stu-id="efc84-121">The file paths provided:</span></span>

* <span data-ttu-id="efc84-122">可以是当前目录的相对路径，也可以是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-122">Can be relative to the current directory or absolute paths.</span></span>
* <span data-ttu-id="efc84-123">可以包含用于基于模式的文件[通配](https://wikipedia.org/wiki/Glob_(programming))的通配符。</span><span class="sxs-lookup"><span data-stu-id="efc84-123">May contain wild cards for pattern-based file [globbing](https://wikipedia.org/wiki/Glob_(programming)).</span></span>

<span data-ttu-id="efc84-124">如果任何文件处于项目目录之外，则会添加一个 `Link` 元素，以在 Visual Studio 中的文件夹 `Protos` 下显示该文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-124">If any files are outside the project directory, a `Link` element is added to display the file under the folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="efc84-125">用法</span><span class="sxs-lookup"><span data-stu-id="efc84-125">Usage</span></span>

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a><span data-ttu-id="efc84-126">自变量</span><span class="sxs-lookup"><span data-stu-id="efc84-126">Arguments</span></span>

| <span data-ttu-id="efc84-127">参数</span><span class="sxs-lookup"><span data-stu-id="efc84-127">Argument</span></span> | <span data-ttu-id="efc84-128">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-128">Description</span></span> |
|-|-|
| <span data-ttu-id="efc84-129">文件</span><span class="sxs-lookup"><span data-stu-id="efc84-129">files</span></span> | <span data-ttu-id="efc84-130">Protobuf 文件引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-130">The protobuf file references.</span></span> <span data-ttu-id="efc84-131">这些可以是本地 protobuf 文件的 glob 的路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-131">These can be a path to glob for local protobuf files.</span></span> |

#### <a name="options"></a><span data-ttu-id="efc84-132">选项</span><span class="sxs-lookup"><span data-stu-id="efc84-132">Options</span></span>

| <span data-ttu-id="efc84-133">短选项</span><span class="sxs-lookup"><span data-stu-id="efc84-133">Short option</span></span> | <span data-ttu-id="efc84-134">长选项</span><span class="sxs-lookup"><span data-stu-id="efc84-134">Long option</span></span> | <span data-ttu-id="efc84-135">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-135">Description</span></span> |
|-|-|-|
| <span data-ttu-id="efc84-136">-p</span><span class="sxs-lookup"><span data-stu-id="efc84-136">-p</span></span> | <span data-ttu-id="efc84-137">--project</span><span class="sxs-lookup"><span data-stu-id="efc84-137">--project</span></span> | <span data-ttu-id="efc84-138">要操作的项目文件的路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-138">The path to the project file to operate on.</span></span> <span data-ttu-id="efc84-139">如果未指定文件，则该命令会在当前目录中搜索一个文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-139">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="efc84-140">-S</span><span class="sxs-lookup"><span data-stu-id="efc84-140">-s</span></span> | <span data-ttu-id="efc84-141">--services</span><span class="sxs-lookup"><span data-stu-id="efc84-141">--services</span></span> | <span data-ttu-id="efc84-142">应生成的 gRPC 服务的类型。</span><span class="sxs-lookup"><span data-stu-id="efc84-142">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="efc84-143">如果指定 `Default`，则 `Both` 用于 Web 项目，而 `Client` 用于非 Web 项目。</span><span class="sxs-lookup"><span data-stu-id="efc84-143">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="efc84-144">接受的值包括 `Both`、`Client`、`Default`、`None` 和 `Server`。</span><span class="sxs-lookup"><span data-stu-id="efc84-144">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="efc84-145">-o</span><span class="sxs-lookup"><span data-stu-id="efc84-145">-i</span></span> | <span data-ttu-id="efc84-146">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="efc84-146">--additional-import-dirs</span></span> | <span data-ttu-id="efc84-147">解析 protobuf 文件的导入时要使用的其他目录。</span><span class="sxs-lookup"><span data-stu-id="efc84-147">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="efc84-148">这是以分号分隔的路径列表。</span><span class="sxs-lookup"><span data-stu-id="efc84-148">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="efc84-149">--access</span><span class="sxs-lookup"><span data-stu-id="efc84-149">--access</span></span> | <span data-ttu-id="efc84-150">要用于生成的 C# 类的访问修饰符。</span><span class="sxs-lookup"><span data-stu-id="efc84-150">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="efc84-151">默认值为 `Public`。</span><span class="sxs-lookup"><span data-stu-id="efc84-151">The default value is `Public`.</span></span> <span data-ttu-id="efc84-152">接受的值为 `Internal` 和 `Public`。</span><span class="sxs-lookup"><span data-stu-id="efc84-152">Accepted values are `Internal` and `Public`.</span></span>

### <a name="add-url"></a><span data-ttu-id="efc84-153">添加 URL</span><span class="sxs-lookup"><span data-stu-id="efc84-153">Add URL</span></span>

<span data-ttu-id="efc84-154">`add-url` 命令用于将源 URL 指定的远程文件添加为 Protobuf 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-154">The `add-url` command is used to add a remote file specified by an source URL as Protobuf reference.</span></span> <span data-ttu-id="efc84-155">必须提供文件路径才能指定下载远程文件的位置。</span><span class="sxs-lookup"><span data-stu-id="efc84-155">A file path must be provided to specify where to download the remote file.</span></span> <span data-ttu-id="efc84-156">文件路径可以是当前目录的相对路径，也可以是绝对路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-156">The file path can be relative to the current directory or an absolute path.</span></span> <span data-ttu-id="efc84-157">如果文件路径处于项目目录之外，则会添加一个 `Link` 元素，以在 Visual Studio 中的虚拟文件夹 `Protos` 下显示该文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-157">If the file path is outside the project directory, a `Link` element is added to display the file under the virtual folder `Protos` in Visual Studio.</span></span>

### <a name="usage"></a><span data-ttu-id="efc84-158">用法</span><span class="sxs-lookup"><span data-stu-id="efc84-158">Usage</span></span>

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a><span data-ttu-id="efc84-159">自变量</span><span class="sxs-lookup"><span data-stu-id="efc84-159">Arguments</span></span>

| <span data-ttu-id="efc84-160">参数</span><span class="sxs-lookup"><span data-stu-id="efc84-160">Argument</span></span> | <span data-ttu-id="efc84-161">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-161">Description</span></span> |
|-|-|
| <span data-ttu-id="efc84-162">URL</span><span class="sxs-lookup"><span data-stu-id="efc84-162">url</span></span> | <span data-ttu-id="efc84-163">远程 protobuf 文件的 URL。</span><span class="sxs-lookup"><span data-stu-id="efc84-163">The URL to a remote protobuf file.</span></span> |

#### <a name="options"></a><span data-ttu-id="efc84-164">选项</span><span class="sxs-lookup"><span data-stu-id="efc84-164">Options</span></span>

| <span data-ttu-id="efc84-165">短选项</span><span class="sxs-lookup"><span data-stu-id="efc84-165">Short option</span></span> | <span data-ttu-id="efc84-166">长选项</span><span class="sxs-lookup"><span data-stu-id="efc84-166">Long option</span></span> | <span data-ttu-id="efc84-167">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-167">Description</span></span> |
|-|-|-|
| <span data-ttu-id="efc84-168">-o</span><span class="sxs-lookup"><span data-stu-id="efc84-168">-o</span></span> | <span data-ttu-id="efc84-169">--output</span><span class="sxs-lookup"><span data-stu-id="efc84-169">--output</span></span> | <span data-ttu-id="efc84-170">指定远程 protobuf 文件的下载路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-170">Specifies the download path for the remote protobuf file.</span></span> <span data-ttu-id="efc84-171">这是必需选项。</span><span class="sxs-lookup"><span data-stu-id="efc84-171">This is a required option.</span></span>
| <span data-ttu-id="efc84-172">-p</span><span class="sxs-lookup"><span data-stu-id="efc84-172">-p</span></span> | <span data-ttu-id="efc84-173">--project</span><span class="sxs-lookup"><span data-stu-id="efc84-173">--project</span></span> | <span data-ttu-id="efc84-174">要操作的项目文件的路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-174">The path to the project file to operate on.</span></span> <span data-ttu-id="efc84-175">如果未指定文件，则该命令会在当前目录中搜索一个文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-175">If a file is not specified, the command searches the current directory for one.</span></span>
| <span data-ttu-id="efc84-176">-S</span><span class="sxs-lookup"><span data-stu-id="efc84-176">-s</span></span> | <span data-ttu-id="efc84-177">--services</span><span class="sxs-lookup"><span data-stu-id="efc84-177">--services</span></span> | <span data-ttu-id="efc84-178">应生成的 gRPC 服务的类型。</span><span class="sxs-lookup"><span data-stu-id="efc84-178">The type of gRPC services that should be generated.</span></span> <span data-ttu-id="efc84-179">如果指定 `Default`，则 `Both` 用于 Web 项目，而 `Client` 用于非 Web 项目。</span><span class="sxs-lookup"><span data-stu-id="efc84-179">If `Default` is specified, `Both` is used for Web projects and `Client` is used for non-Web projects.</span></span> <span data-ttu-id="efc84-180">接受的值包括 `Both`、`Client`、`Default`、`None` 和 `Server`。</span><span class="sxs-lookup"><span data-stu-id="efc84-180">Accepted values are `Both`, `Client`, `Default`, `None`, `Server`.</span></span>
| <span data-ttu-id="efc84-181">-o</span><span class="sxs-lookup"><span data-stu-id="efc84-181">-i</span></span> | <span data-ttu-id="efc84-182">--additional-import-dirs</span><span class="sxs-lookup"><span data-stu-id="efc84-182">--additional-import-dirs</span></span> | <span data-ttu-id="efc84-183">解析 protobuf 文件的导入时要使用的其他目录。</span><span class="sxs-lookup"><span data-stu-id="efc84-183">Additional directories to be used when resolving imports for the protobuf files.</span></span> <span data-ttu-id="efc84-184">这是以分号分隔的路径列表。</span><span class="sxs-lookup"><span data-stu-id="efc84-184">This is a semicolon separated list of paths.</span></span>
| | <span data-ttu-id="efc84-185">--access</span><span class="sxs-lookup"><span data-stu-id="efc84-185">--access</span></span> | <span data-ttu-id="efc84-186">要用于生成的 C# 类的访问修饰符。</span><span class="sxs-lookup"><span data-stu-id="efc84-186">The access modifier to use for the generated C# classes.</span></span> <span data-ttu-id="efc84-187">默认值是 `Public`。</span><span class="sxs-lookup"><span data-stu-id="efc84-187">Default value is `Public`.</span></span> <span data-ttu-id="efc84-188">接受的值为 `Internal` 和 `Public`。</span><span class="sxs-lookup"><span data-stu-id="efc84-188">Accepted values are `Internal` and `Public`.</span></span>

## <a name="remove"></a><span data-ttu-id="efc84-189">删除</span><span class="sxs-lookup"><span data-stu-id="efc84-189">Remove</span></span>

<span data-ttu-id="efc84-190">`remove` 命令用于从 .csproj 文件中删除 Protobuf 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-190">The `remove` command is used to remove Protobuf references from the *.csproj* file.</span></span> <span data-ttu-id="efc84-191">该命令接受路径参数和源 URL 作为参数。</span><span class="sxs-lookup"><span data-stu-id="efc84-191">The command accepts path arguments and source URLs as arguments.</span></span> <span data-ttu-id="efc84-192">工具：</span><span class="sxs-lookup"><span data-stu-id="efc84-192">The tool:</span></span>

* <span data-ttu-id="efc84-193">仅删除 Protobuf 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-193">Only removes the Protobuf reference.</span></span>
* <span data-ttu-id="efc84-194">不会删除 .proto 文件，即使它最初是从远程 URL 下载也是如此。</span><span class="sxs-lookup"><span data-stu-id="efc84-194">Does not delete the *.proto* file, even if it was originally downloaded from a remote URL.</span></span>

### <a name="usage"></a><span data-ttu-id="efc84-195">用法</span><span class="sxs-lookup"><span data-stu-id="efc84-195">Usage</span></span>

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a><span data-ttu-id="efc84-196">自变量</span><span class="sxs-lookup"><span data-stu-id="efc84-196">Arguments</span></span>

| <span data-ttu-id="efc84-197">参数</span><span class="sxs-lookup"><span data-stu-id="efc84-197">Argument</span></span> | <span data-ttu-id="efc84-198">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-198">Description</span></span> |
|-|-|
| <span data-ttu-id="efc84-199">引用</span><span class="sxs-lookup"><span data-stu-id="efc84-199">references</span></span> | <span data-ttu-id="efc84-200">要删除的 protobuf 引用的 URL 或文件路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-200">The URLs or file paths of the protobuf references to remove.</span></span> |

### <a name="options"></a><span data-ttu-id="efc84-201">选项</span><span class="sxs-lookup"><span data-stu-id="efc84-201">Options</span></span>

| <span data-ttu-id="efc84-202">短选项</span><span class="sxs-lookup"><span data-stu-id="efc84-202">Short option</span></span> | <span data-ttu-id="efc84-203">长选项</span><span class="sxs-lookup"><span data-stu-id="efc84-203">Long option</span></span> | <span data-ttu-id="efc84-204">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-204">Description</span></span> |
|-|-|-|
| <span data-ttu-id="efc84-205">-p</span><span class="sxs-lookup"><span data-stu-id="efc84-205">-p</span></span> | <span data-ttu-id="efc84-206">--project</span><span class="sxs-lookup"><span data-stu-id="efc84-206">--project</span></span> | <span data-ttu-id="efc84-207">要操作的项目文件的路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-207">The path to the project file to operate on.</span></span> <span data-ttu-id="efc84-208">如果未指定文件，则该命令会在当前目录中搜索一个文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-208">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="refresh"></a><span data-ttu-id="efc84-209">刷新</span><span class="sxs-lookup"><span data-stu-id="efc84-209">Refresh</span></span>

<span data-ttu-id="efc84-210">`refresh` 命令用于使用来自源 URL 的最新内容更新远程引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-210">The `refresh` command is used to update a remote reference with the latest content from the source URL.</span></span> <span data-ttu-id="efc84-211">下载文件路径和源 URL 都可以用于指定要更新的引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-211">Both the download file path and the source URL can be used to specify the reference to be updated.</span></span> <span data-ttu-id="efc84-212">注意：</span><span class="sxs-lookup"><span data-stu-id="efc84-212">Note:</span></span>

* <span data-ttu-id="efc84-213">会比较文件内容的哈希，以确定是否应更新本地文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-213">The hashes of the file contents are compared to determine whether the local file should be updated.</span></span>
* <span data-ttu-id="efc84-214">不会比较时间戳信息。</span><span class="sxs-lookup"><span data-stu-id="efc84-214">No timestamp information is compared.</span></span>

<span data-ttu-id="efc84-215">如果需要更新，则该工具始终将本地文件替换为远程文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-215">The tool always replaces the local file with the remote file if an update is needed.</span></span>

### <a name="usage"></a><span data-ttu-id="efc84-216">用法</span><span class="sxs-lookup"><span data-stu-id="efc84-216">Usage</span></span>

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a><span data-ttu-id="efc84-217">自变量</span><span class="sxs-lookup"><span data-stu-id="efc84-217">Arguments</span></span>

| <span data-ttu-id="efc84-218">参数</span><span class="sxs-lookup"><span data-stu-id="efc84-218">Argument</span></span> | <span data-ttu-id="efc84-219">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-219">Description</span></span> |
|-|-|
| <span data-ttu-id="efc84-220">引用</span><span class="sxs-lookup"><span data-stu-id="efc84-220">references</span></span> | <span data-ttu-id="efc84-221">应更新的远程 protobuf 引用的 URL 或文件路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-221">The URLs or file paths to remote protobuf references that should be updated.</span></span> <span data-ttu-id="efc84-222">将此参数保留为空，以刷新所有远程引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-222">Leave this argument empty to refresh all remote references.</span></span> |

### <a name="options"></a><span data-ttu-id="efc84-223">选项</span><span class="sxs-lookup"><span data-stu-id="efc84-223">Options</span></span>

| <span data-ttu-id="efc84-224">短选项</span><span class="sxs-lookup"><span data-stu-id="efc84-224">Short option</span></span> | <span data-ttu-id="efc84-225">长选项</span><span class="sxs-lookup"><span data-stu-id="efc84-225">Long option</span></span> | <span data-ttu-id="efc84-226">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-226">Description</span></span> |
|-|-|-|
| <span data-ttu-id="efc84-227">-p</span><span class="sxs-lookup"><span data-stu-id="efc84-227">-p</span></span> | <span data-ttu-id="efc84-228">--project</span><span class="sxs-lookup"><span data-stu-id="efc84-228">--project</span></span> | <span data-ttu-id="efc84-229">要操作的项目文件的路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-229">The path to the project file to operate on.</span></span> <span data-ttu-id="efc84-230">如果未指定文件，则该命令会在当前目录中搜索一个文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-230">If a file is not specified, the command searches the current directory for one.</span></span>
| | <span data-ttu-id="efc84-231">--dry-run</span><span class="sxs-lookup"><span data-stu-id="efc84-231">--dry-run</span></span> | <span data-ttu-id="efc84-232">输出将更新的文件的列表，而不下载任何新内容。</span><span class="sxs-lookup"><span data-stu-id="efc84-232">Outputs a list of files that would be updated without downloading any new content.</span></span>

## <a name="list"></a><span data-ttu-id="efc84-233">列表</span><span class="sxs-lookup"><span data-stu-id="efc84-233">List</span></span>

<span data-ttu-id="efc84-234">`list` 命令用于显示项目文件中的所有 Protobuf 引用。</span><span class="sxs-lookup"><span data-stu-id="efc84-234">The `list` command is used to display all the Protobuf references in the project file.</span></span> <span data-ttu-id="efc84-235">如果某列的所有值都是默认值，则可以省略该列。</span><span class="sxs-lookup"><span data-stu-id="efc84-235">If all values of a column are default values, the column may be omitted.</span></span>

### <a name="usage"></a><span data-ttu-id="efc84-236">用法</span><span class="sxs-lookup"><span data-stu-id="efc84-236">Usage</span></span>

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a><span data-ttu-id="efc84-237">选项</span><span class="sxs-lookup"><span data-stu-id="efc84-237">Options</span></span>

| <span data-ttu-id="efc84-238">短选项</span><span class="sxs-lookup"><span data-stu-id="efc84-238">Short option</span></span> | <span data-ttu-id="efc84-239">长选项</span><span class="sxs-lookup"><span data-stu-id="efc84-239">Long option</span></span> | <span data-ttu-id="efc84-240">描述</span><span class="sxs-lookup"><span data-stu-id="efc84-240">Description</span></span> |
|-|-|-|
| <span data-ttu-id="efc84-241">-p</span><span class="sxs-lookup"><span data-stu-id="efc84-241">-p</span></span> | <span data-ttu-id="efc84-242">--project</span><span class="sxs-lookup"><span data-stu-id="efc84-242">--project</span></span> | <span data-ttu-id="efc84-243">要操作的项目文件的路径。</span><span class="sxs-lookup"><span data-stu-id="efc84-243">The path to the project file to operate on.</span></span> <span data-ttu-id="efc84-244">如果未指定文件，则该命令会在当前目录中搜索一个文件。</span><span class="sxs-lookup"><span data-stu-id="efc84-244">If a file is not specified, the command searches the current directory for one.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="efc84-245">其他资源</span><span class="sxs-lookup"><span data-stu-id="efc84-245">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
