---
title: Использование базы данных MySQL в приложении UWP
description: Использование базы данных MySQL в приложении UWP.
ms.date: 03/28/2019
ms.topic: article
keywords: windows 10, uwp, MySQL, database
ms.localizationpriority: medium
ms.openlocfilehash: bfed9c0a0c4198095b9be48fe71832bdfca67718
ms.sourcegitcommit: 139717a79af648a9231821bdfcaf69d8a1e6e894
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67713783"
---
# <a name="use-a-mysql-database"></a>Использование базы данных MySQL
В этой статье описываются шаги, необходимые для настройки работы базы данных MySQL с приложением UWP. Здесь также приведены небольшие фрагменты кода, в которых показано, как с взаимодействовать с базами данных программным способом.

## <a name="set-up-your-solution"></a>Настройка решения

Чтобы подключить приложение напрямую к базе данных MySQL, убедитесь, что минимальная версия вашего проекта поддерживает Fall Creators Update (сборка 16299).  Эти сведения можно найти на странице свойств проекта UWP.

![Изображение панели свойств "Нацеливание" в Visual Studio с целью и минимальным набором версий для Fall Creators Update](images/min-version-fall-creators.png)

Откройте **Консоль диспетчера пакетов** (Представление -> Другие окна -> Консоль диспетчера пакетов). Чтобы установить драйвер для MySQL используйте команду **Install-Package MySql.Data**. Это позволит получать доступ к базам данных MySQL программным способом.

## <a name="test-your-connection-using-sample-code"></a>Проверка подключения с помощью примера кода
Далее приведен пример подключения к удаленной базе данных MySQL и чтения из нее. Обратите внимание, что IP-адрес, учетные данные и имя базы данных нужно настроить надлежащим образом.

```csharp
string M_str_sqlcon = "server=10.xxx.xx.xxx;user id=foo;password=bar;database=baz";
MySqlConnection mysqlcon = new MySqlConnection(M_str_sqlcon);
MySqlCommand mysqlcom = new MySqlCommand("select * from table1", mysqlcon);
mysqlcon.Open();
MySqlDataReader mysqlread = mysqlcom.ExecuteReader(CommandBehavior.CloseConnection);
while (mysqlread.Read())
{
    Debug.WriteLine(mysqlread.GetString(0)+":"+mysqlread.GetString(1));
}
mysqlcon.Close();
```
