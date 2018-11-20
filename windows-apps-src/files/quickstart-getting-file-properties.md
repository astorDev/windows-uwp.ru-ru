---
author: laurenhughes
ms.assetid: AC96F645-1BDE-4316-85E0-2FBDE0A0A62A
title: Получение свойств файла
description: Получите свойства&\#8212;верхнего уровня, базовые и расширенные&\#8212;для файла, представленного объектом StorageFile.
ms.author: lahugh
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8fc44300376efb5b56f390457e516f35a3ec4202
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7304806"
---
# <a name="get-file-properties"></a>Получение свойств файла



**Важные API**

-   [**StorageFile.GetBasicPropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/hh701737)
-   [**StorageFile.Properties**](https://msdn.microsoft.com/library/windows/apps/br227225)
-   [**StorageItemContentProperties.RetrievePropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/hh770652)

Получите свойства — верхнего уровня, базовые и расширенные — для файла, представленного объектом [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171).

> [!NOTE]
> [См. также [Пример доступа к файлам](http://go.microsoft.com/fwlink/p/?linkid=619995).]

 


## <a name="prerequisites"></a>Необходимые условия

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Права на доступ к расположению**

    Например, коду в этих примерах требуется возможность **picturesLibrary**. Для вашего расположения может потребоваться другая возможность либо вообще не потребоваться никаких возможностей. Дополнительную информацию см. в разделе [Разрешения на доступ к файлам](file-access-permissions.md).

## <a name="getting-a-files-top-level-properties"></a>Получение свойств файла верхнего уровня

Доступ ко многим свойствам файла верхнего уровня можно получить как к членам класса [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171). К таким свойствам относятся атрибуты файлов, тип содержимого, дата создания, отображаемое имя, тип файла и т. д.

**Примечание**не забудьте объявить возможность **picturesLibrary** .

 

Этот пример перечисляет все файлы библиотеки «Изображения», предоставляя доступ к некоторым свойствам верхнего уровня каждого из файлов.

```csharp
// Enumerate all files in the Pictures library.
var folder = Windows.Storage.KnownFolders.PicturesLibrary;
var query = folder.CreateFileQuery();
var files = await query.GetFilesAsync();

foreach (Windows.Storage.StorageFile file in files)
{
    StringBuilder fileProperties = new StringBuilder();

    // Get top-level file properties.
    fileProperties.AppendLine("File name: " + file.Name);
    fileProperties.AppendLine("File type: " + file.FileType);
}
```

## <a name="getting-a-files-basic-properties"></a>Получение базовых свойств файла

Многие базовые свойства файла можно получить, вызвав сначала метод [**StorageFile.GetBasicPropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/hh701737). Этот метод возвращает объект [**BasicProperties**](https://msdn.microsoft.com/library/windows/apps/br212113), который определяет свойства размера элемента (файл или папка) и дату его последнего изменения.

Этот пример перечисляет все файлы библиотеки "Изображения", предоставляя доступ к некоторым базовым свойствам каждого из файлов.

```csharp
// Enumerate all files in the Pictures library.
var folder = Windows.Storage.KnownFolders.PicturesLibrary;
var query = folder.CreateFileQuery();
var files = await query.GetFilesAsync();

foreach (Windows.Storage.StorageFile file in files)
{
    StringBuilder fileProperties = new StringBuilder();

    // Get file's basic properties.
    Windows.Storage.FileProperties.BasicProperties basicProperties =
        await file.GetBasicPropertiesAsync();
    string fileSize = string.Format("{0:n0}", basicProperties.Size);
    fileProperties.AppendLine("File size: " + fileSize + " bytes");
    fileProperties.AppendLine("Date modified: " + basicProperties.DateModified);
}
 ```

## <a name="getting-a-files-extended-properties"></a>Получение расширенных свойств файла

Помимо свойств верхнего уровня и базовых свойств, существует много свойств, связанных с содержимым файла. Доступ к этим расширенным свойствам можно получить, вызвав метод [**BasicProperties.RetrievePropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br212124). (Объект [**BasicProperties**](https://msdn.microsoft.com/library/windows/apps/br212113) получают, вызывая свойство [**StorageFile.Properties**](https://msdn.microsoft.com/library/windows/apps/br227225).) Доступ к свойствам верхнего уровня и базовым свойствам файла можно получить, обратившись к ним как к свойствам класса ([**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) и **BasicProperties** соответственно). Однако расширенные свойства получают, передавая коллекцию [IEnumerable](http://go.microsoft.com/fwlink/p/?LinkID=313091) объектов [String](http://go.microsoft.com/fwlink/p/?LinkID=325032), представляющих имена свойств, которые должны быть получены методом **BasicProperties.RetrievePropertiesAsync**. Затем этот метод возвращает коллекцию [IDictionary](http://go.microsoft.com/fwlink/p/?LinkId=325238). После этого каждое расширенное свойство извлекается из коллекции по имени или индексу.

Этот пример перечисляет все файлы библиотеки изображений, указывает имена нужных свойств (**DataAccessed** и **FileOwner**) в объекте [List](http://go.microsoft.com/fwlink/p/?LinkID=325246), передает объект [List](http://go.microsoft.com/fwlink/p/?LinkID=325246) в [**BasicProperties.RetrievePropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br212124) для извлечения свойств и затем получает свойства по имени из возвращенного объекта [IDictionary](http://go.microsoft.com/fwlink/p/?LinkId=325238).

В разделе [Основные свойства Windows](https://msdn.microsoft.com/library/windows/desktop/mt805470) представлен полный список расширенных свойств файла.

```csharp
const string dateAccessedProperty = "System.DateAccessed";
const string fileOwnerProperty = "System.FileOwner";

// Enumerate all files in the Pictures library.
var folder = KnownFolders.PicturesLibrary;
var query = folder.CreateFileQuery();
var files = await query.GetFilesAsync();

foreach (Windows.Storage.StorageFile file in files)
{
    StringBuilder fileProperties = new StringBuilder();

    // Define property names to be retrieved.
    var propertyNames = new List<string>();
    propertyNames.Add(dateAccessedProperty);
    propertyNames.Add(fileOwnerProperty);

    // Get extended properties.
    IDictionary<string, object> extraProperties =
        await file.Properties.RetrievePropertiesAsync(propertyNames);

    // Get date-accessed property.
    var propValue = extraProperties[dateAccessedProperty];
    if (propValue != null)
    {
        fileProperties.AppendLine("Date accessed: " + propValue);
    }

    // Get file-owner property.
    propValue = extraProperties[fileOwnerProperty];
    if (propValue != null)
    {
        fileProperties.AppendLine("File owner: " + propValue);
    }
}
```

 

 
