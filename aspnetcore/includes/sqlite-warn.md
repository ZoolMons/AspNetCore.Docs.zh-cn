---
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
ms.openlocfilehash: 0cb1b72cf0e2d76b7f5752ce1314c70ff7789418
ms.sourcegitcommit: fafcf015d64aa2388bacee16ba38799daf06a4f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105957492"
---
> [!NOTE]
> 
> <span data-ttu-id="6b6b0-101">**SQLite 限制**</span><span class="sxs-lookup"><span data-stu-id="6b6b0-101">**SQLite limitations**</span></span>
>
> <span data-ttu-id="6b6b0-102">本教程使用 Entity Framework Core 迁移功能（若可行）[](/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli)。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-102">This tutorial uses the Entity Framework Core [migrations](/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli) feature where possible.</span></span> <span data-ttu-id="6b6b0-103">迁移会更新数据库架构，使其与数据模型中的更改相匹配。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-103">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="6b6b0-104">但迁移只执行数据库引擎支持的更改类型，而 SQLite 的架构更改功能受限。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-104">However, migrations only does the kinds of changes that the database engine supports, and SQLite's schema change capabilities are limited.</span></span> <span data-ttu-id="6b6b0-105">例如，支持添加列，但不支持删除列。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-105">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="6b6b0-106">如果已创建迁移以删除列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-106">If a migration is created to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> 
>
> <span data-ttu-id="6b6b0-107">要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-107">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="6b6b0-108">代码将在 `Up` 和 `Down` 方法中用于迁移，并且将涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="6b6b0-108">The code goes in the `Up` and `Down` methods for a migration and involves:</span></span>
>
> * <span data-ttu-id="6b6b0-109">创建新表。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-109">Creating a new table.</span></span>
> * <span data-ttu-id="6b6b0-110">将旧表中的数据复制到新表中。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-110">Copying data from the old table to the new table.</span></span>
> * <span data-ttu-id="6b6b0-111">放弃旧表。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-111">Dropping the old table.</span></span>
> * <span data-ttu-id="6b6b0-112">为新表重命名。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-112">Renaming the new table.</span></span>
>
> <span data-ttu-id="6b6b0-113">本教程不涉及编写此类型的特定于数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-113">Writing database-specific code of this type is outside the scope of this tutorial.</span></span> <span data-ttu-id="6b6b0-114">相反，每当尝试应用迁移失败时，本教程将删除并重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="6b6b0-114">Instead, this tutorial drops and re-creates the database whenever an attempt to apply a migration would fail.</span></span> <span data-ttu-id="6b6b0-115">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="6b6b0-115">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="6b6b0-116">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="6b6b0-116">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="6b6b0-117">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="6b6b0-117">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="6b6b0-118">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="6b6b0-118">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="6b6b0-119">SQLite ALTER TABLE 语句</span><span class="sxs-lookup"><span data-stu-id="6b6b0-119">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)