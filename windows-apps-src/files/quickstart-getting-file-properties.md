---
ms.assetid: AC96F645-1BDE-4316-85E0-2FBDE0A0A62A
title: Получение свойств файла
description: Получение свойств &\#8212; верхнего уровня, базовые и расширенные &\#8212; для файла, представленного StorageFile объекта.
ms.date: 12/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 01eda8eefea7e1b3b1102ef154a019e1630e80c2
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66369308"
---
# <a name="get-file-properties"></a>Получение свойств файла

**Важные API**

-   [**StorageFile.GetBasicPropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getbasicpropertiesasync)
-   [**StorageFile.Properties**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.properties)
-   [**StorageItemContentProperties.RetrievePropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.storageitemcontentproperties.retrievepropertiesasync)

Получите свойства — верхнего уровня, базовые и расширенные — для файла, представленного объектом [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile).

> [!NOTE]
> Полный пример см. в разделе [файла доступ к образцу](https://go.microsoft.com/fwlink/p/?linkid=619995).

## <a name="prerequisites"></a>Предварительные требования

-   **Понять асинхронного программирования для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://docs.microsoft.com/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps).

-   **Разрешения на доступ к папке**

    Например, коду в этих примерах требуется возможность **picturesLibrary**. Для вашего расположения может потребоваться другая возможность либо вообще не потребоваться никаких возможностей. Дополнительную информацию см. в разделе [Разрешения на доступ к файлам](file-access-permissions.md).

## <a name="getting-a-files-top-level-properties"></a>Получение свойств файла верхнего уровня

Доступ ко многим свойствам файла верхнего уровня можно получить как к членам класса [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile). К таким свойствам относятся атрибуты файлов, тип содержимого, дата создания, отображаемое имя, тип файла и т. д.

> [!NOTE]
> Обязательно объявите возможность **picturesLibrary**.

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

Многие базовые свойства файла можно получить, вызвав сначала метод [**StorageFile.GetBasicPropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getbasicpropertiesasync). Этот метод возвращает объект [**BasicProperties**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileProperties.BasicProperties), который определяет свойства размера элемента (файл или папка) и дату его последнего изменения.

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

Помимо свойств верхнего уровня и базовых свойств, существует много свойств, связанных с содержимым файла. Доступ к этим расширенным свойствам можно получить, вызвав метод [**BasicProperties.RetrievePropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.basicproperties.retrievepropertiesasync). (Объект [ **BasicProperties** ](https://docs.microsoft.com/uwp/api/Windows.Storage.FileProperties.BasicProperties) будет получен путем вызова [ **StorageFile.Properties** ](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.properties) свойство.) Хотя файл верхнего уровня и основные свойства доступны как свойства класса —[**StorageFile** ](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) и **BasicProperties**, соответственно, расширенные свойства получить, передав [IEnumerable](https://go.microsoft.com/fwlink/p/?LinkID=313091) коллекцию [строка](https://go.microsoft.com/fwlink/p/?LinkID=325032) объектов, представляющих имена свойств, которые должны быть получены для  **BasicProperties.RetrievePropertiesAsync** метод. Затем этот метод возвращает коллекцию [IDictionary](https://go.microsoft.com/fwlink/p/?LinkId=325238). После этого каждое расширенное свойство извлекается из коллекции по имени или индексу.

Этот пример перечисляет все файлы библиотеки изображений, указывает имена нужных свойств (**DataAccessed** и **FileOwner**) в объекте [List](https://go.microsoft.com/fwlink/p/?LinkID=325246), передает объект [List](https://go.microsoft.com/fwlink/p/?LinkID=325246) в [**BasicProperties.RetrievePropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.basicproperties.retrievepropertiesasync) для извлечения свойств и затем получает свойства по имени из возвращенного объекта [IDictionary](https://go.microsoft.com/fwlink/p/?LinkId=325238).

В разделе [Основные свойства Windows](https://docs.microsoft.com/windows/desktop/properties/core-bumper) представлен полный список расширенных свойств файла.

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

 

 
