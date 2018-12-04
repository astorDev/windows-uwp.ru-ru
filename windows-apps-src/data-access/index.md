---
ms.assetid: 76776b0f-3163-48c9-835b-3f4213968079
title: Доступ к данным
description: В этом разделе описывается хранение данных на устройстве в частной базе данных и использование объектно-реляционного отображения в приложениях универсальной платформы Windows (UWP).
ms.date: 11/13/2017
ms.topic: article
keywords: windows 10, uwp, данные, база данных, реляционная, таблицы, sqlite
ms.localizationpriority: medium
ms.openlocfilehash: eb5adbdd3ae12d039d934e8d0cbe468ae5c1187c
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8466783"
---
# <a name="data-access"></a>Доступ к данным

Вы можете сохранить данные на устройстве пользователя с помощью базы данных SQLite. Ваше приложение можно также подключаться напрямую к базе данных SQL Server без использования каких-либо уровень служб.

| Статья | Описание|
|-------|------------|
| [Использование базы данных SQLite в приложении UWP](sqlite-databases.md) | Показано, как использовать SQLite для хранения и извлечения данных из облегченной базы данных на устройстве пользователя. SQLite–это бессерверное встраиваемое ядро СУБД. |
| [Использование базы данных SQL server в приложении UWP](sql-server-databases.md) | Показано, как подключаться напрямую к базе данных SQL Server и затем хранить и извлекать данные с помощью классов в пространстве имен [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient.aspx) . Уровень служб не требуется. |

## <a name="related-topics"></a>Статьи по теме

* [Пример базы данных заказов клиентов](https://github.com/Microsoft/Windows-appsample-customers-orders-database)
