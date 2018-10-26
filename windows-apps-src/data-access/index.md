---
author: normesta
ms.assetid: 76776b0f-3163-48c9-835b-3f4213968079
title: Доступ к данным
description: В этом разделе описывается хранение данных на устройстве в частной базе данных и использование объектно-реляционного отображения в приложениях универсальной платформы Windows (UWP).
ms.author: normesta
ms.date: 11/13/2017
ms.topic: article
keywords: windows 10, uwp, данные, база данных, реляционная, таблицы, sqlite
ms.localizationpriority: medium
ms.openlocfilehash: beca20d358430ecd82cd1bc57459a6f6af36be77
ms.sourcegitcommit: b7e3d222e229cdbf04e837fcb94fb7d84a93de09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5621680"
---
# <a name="data-access"></a>Доступ к данным

Вы можете сохранить данные на устройстве пользователя с помощью базы данных SQLite. Ваше приложение можно также подключаться напрямую к базе данных SQL Server без использования каких-либо уровень служб.

| Статья | Описание|
|-------|------------|
| [Использование базы данных SQLite в приложении UWP](sqlite-databases.md) | Показано, как использовать SQLite для хранения и извлечения данных из облегченной базы данных на устройстве пользователя. SQLite–это бессерверное встраиваемое ядро СУБД. |
| [Использование базы данных SQL server в приложении UWP](sql-server-databases.md) | Показано, как подключаться напрямую к базе данных SQL Server и затем хранить и извлекать данные с помощью классов в пространстве имен [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient.aspx) . Уровень служб не требуется. |

## <a name="related-topics"></a>Еще по теме

* [Пример базы данных заказов клиентов](https://github.com/Microsoft/Windows-appsample-customers-orders-database)
