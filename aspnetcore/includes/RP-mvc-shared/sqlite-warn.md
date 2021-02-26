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
ms.openlocfilehash: e8c5bd00178aefb91ab0e7066c5490ceba315530
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552302"
---
> [!NOTE]
> 在本教程中，使用 Entity Framework Core 迁移功能（若可行）。 迁移会更新数据库架构，使其与数据模型中的更改相匹配。 但是，迁移仅能执行 EF Core 提供程序所支持的更改类型，且 SQLite 提供程序的功能将受限。 例如，支持添加列，但不支持删除或更改列。 如果已创建迁移以删除或更改列，则 `ef migrations add` 命令将成功，但 `ef database update` 命令会失败。 由于上述限制，本教程不对 SQLite 架构更改使用迁移。 转而在架构更改时，放弃并重新创建数据库。
>
>要绕开 SQLite 限制，可手动写入迁移代码，在表内容更改时重新生成表。 表重新生成涉及：
>
>* 创建新表。
>* 将旧表中的数据复制到新表中。
>* 放弃旧表。
>* 为新表重命名。
>
>有关更多信息，请参见以下资源：
>
> * [SQLite EF Core 数据库提供程序限制](/ef/core/providers/sqlite/limitations)
> * [自定义迁移代码](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [数据种子设定](/ef/core/modeling/data-seeding)
  * [SQLite ALTER TABLE 语句](https://sqlite.org/lang_altertable.html)