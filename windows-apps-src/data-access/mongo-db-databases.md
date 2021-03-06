---
title: Использование базы данных MongoDB в приложении UWP
description: Используйте базу данных MongoDB в приложении UWP.
ms.date: 03/28/2019
ms.topic: article
keywords: windows 10, uwp, MongoDB, database
ms.localizationpriority: medium
ms.openlocfilehash: ea3e630a3bb0b8fc5f7f4168b0b946f115b97446
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67713998"
---
# <a name="use-a-mongodb-database"></a>Использование базы данных MongoDB
В этой статье описано, как настроить работу базы данных MongoDB в приложении UWP. Здесь также приведены небольшие фрагменты кода, в которых показано, как с взаимодействовать с базами данных программным способом.

## <a name="set-up-your-solution"></a>Настройка решения

Чтобы подключить приложение напрямую к базе данных MongoDB, убедитесь, что минимальная версия вашего проекта поддерживает Fall Creators Update (сборка 16299).  Эти сведения можно найти на странице свойств проекта UWP.

![Изображение панели свойств "Нацеливание" в Visual Studio с целью и минимальным набором версий для Fall Creators Update](images/min-version-fall-creators.png)

Откройте **Консоль диспетчера пакетов** (Представление -> Другие окна -> Консоль диспетчера пакетов). Чтобы установить драйвер для MongoDB используйте команду **Install-Package MongoDB.Driver**. Это позволит получать доступ к базам данных MongoDB программным способом.

## <a name="test-your-connection-using-sample-code"></a>Проверка подключения с помощью примера кода
Следующий пример кода возвращает коллекцию из удаленного клиента MongoDB, а затем добавляет новый документ в эту коллекцию. Затем он использует API MongoDB для получения и отображения нового размера коллекции и вставленного документа. Обратите внимание, что IP-адрес и имена баз данных нужно настроить надлежащим образом.

```csharp
var client = new MongoClient("mongodb://10.xxx.xx.xxx:xxx");
IMongoDatabase database = client.GetDatabase("foo");
IMongoCollection<BsonDocument> collection = database.GetCollection<BsonDocument>("bar");
BsonDocument document = new BsonDocument
{
     { "name","MongoDB"},
     { "type","Database"},
     { "count",1},
     { "info",new BsonDocument { { "x", 203 }, { "y", 102 } }}
};
collection.InsertOne(document);
long count = collection.CountDocuments(document);
Debug.WriteLine(count);
IFindFluent<BsonDocument, BsonDocument> document1 = collection.Find(document);
Debug.WriteLine(document1.ToString());
```
