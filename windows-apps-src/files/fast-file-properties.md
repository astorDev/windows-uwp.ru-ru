---
author: laurenhughes
title: Быстрый доступ к свойствам файлов в UWP
description: Теперь можно получить список файлов и их свойств из библиотеки для использования в приложении UWP.
ms.author: lahugh
ms.date: 03/22/2018
ms.topic: article
keywords: windows 10, uwp, файл, свойства
ms.localizationpriority: medium
ms.openlocfilehash: e2f63e848820361a64a2a96348a8e1cc2419f233
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/10/2018
ms.locfileid: "6264544"
---
# <a name="fast-access-to-file-properties-in-uwp"></a>Быстрый доступ к свойствам файлов в UWP 

Узнайте, как быстро получить список файлов и их свойств из библиотеки для использования этих свойств в приложении.  

Предварительные требования 
- **Асинхронное программирование для приложений универсальной платформы Windows (UWP)**  вы узнаете, как написания асинхронных приложений на C# или Visual Basic, см. в разделе [вызов асинхронных API в C# или Visual Basic](https://docs.microsoft.com/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic).     Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps). 
- **Разрешения на доступ к библиотекам**  Коду в этих примерах требуется возможность **picturesLibrary** , но вашего расположения файла может потребоваться другая возможность либо никаких возможностей вообще. Дополнительную информацию см. в разделе [Разрешения на доступ к файлам](https://docs.microsoft.com/windows/uwp/files/file-access-permissions). 
- **Простое перечисление файлов**  В этом примере используется [QueryOptions](https://docs.microsoft.com/uwp/api/Windows.Storage.Search.QueryOptions) для настройки нескольких расширенных свойств перечисления. Подробные инструкции о том, как получить простой список файлов для небольшого каталога, см. в разделе [Перечисление файлов и папок и адресация им запросов](https://docs.microsoft.com/windows/uwp/files/quickstart-listing-files-and-folders). 

## <a name="usage"></a>Использование  
Многим приложениям требуется отобразить список свойств группы файлов, но не всегда требуется напрямую взаимодействовать с файлами. Например, музыкальное приложение воспроизводит (открывает) по одному файлу за раз, но ему требуются свойства всех файлов в папке, чтобы можно было отобразить очередь композиций или чтобы пользователь мог выбрать действительный файл для воспроизведения. 

Примеры на этой странице не следует использовать в приложениях, которые изменят метаданные каждого файла, или в приложениях, взаимодействующих со всеми полученными файлами StorageFIle помимо считывания их свойств. См. дополнительные сведения в разделе [Перечисление файлов и папок и адресация им запросов](https://docs.microsoft.com/windows/uwp/files/quickstart-listing-files-and-folders). 

## <a name="enumerate-all-the-pictures-in-a-location"></a>Перечисление всех изображений в расположении 
В этом примере мы сделаем следующее:
-  создадим объект [QueryOptions](https://docs.microsoft.com/uwp/api/Windows.Storage.Search.QueryOptions), чтобы указать, что приложению требуется как можно скорее перечислить файлы;
-  найдем свойства файлов, выполнив постраничную подкачку объектов StorageFile в приложение. Постраничная подкачка файлов позволяет уменьшить объем используемой приложением памяти и повышает скорость его реагирования.

### <a name="creating-the-query"></a>Создание запроса 
Чтобы создать запрос, мы используем объект QueryOptions, который позволяет указать, что приложению нужно перечислить только определенные типы файлов изображений или отфильтровать файлы, защищенные системой Windows Information Protection (System.Security.EncryptionOwners). 

Важно задать свойства, доступ к которым приложение будет осуществлять с помощью метода [QueryOptions.SetPropertyPrefetch](https://docs.microsoft.com/uwp/api/windows.storage.search.queryoptions.setpropertyprefetch). Если приложение осуществляет доступ к свойству, которое не было вызвано предварительно, производительность будет существенно снижаться.

Настройка свойства [IndexerOption.OnlyUseIndexerAndOptimzeForIndexedProperties](https://docs.microsoft.com/uwp/api/Windows.Storage.Search.IndexerOption) указывает системе на то, что вернуть результаты нужно как можно скорее, однако включить в них нужно только свойства, заданные в методе [SetPropertyPrefetch](https://docs.microsoft.com/uwp/api/windows.storage.search.queryoptions.setpropertyprefetch). 

### <a name="paging-in-the-results"></a>Постраничная подкачка результатов 
В библиотеке изображений пользователей могут храниться тысячи и миллионы файлов, поэтому при вызове метода [GetFilesAsync](https://docs.microsoft.com/uwp/api/windows.storage.search.storagefilequeryresult.getfilesasync) компьютер пользователя будет перегружен, потому что для каждого изображения создается файл StorageFile. Чтобы решить эту проблему, можно однократно создать фиксированное количество файлов StorageFiles, обработать их в пользовательском интерфейсе, а затем освободить память. 

В нашем примере для этого используется метод [StorageFileQueryResult.GetFilesAsync(UInt32 StartIndex, UInt32 maxNumberOfItems)](https://docs.microsoft.com/uwp/api/windows.storage.search.storagefilequeryresult.getfilesasync), позволяющий извлекать не более 100 файлов за раз. Затем приложение обрабатывает файлы и позволяет операционной системе освободить память. Благодаря этой технике ограничивается максимальный объем памяти приложения и обеспечивается сохранение быстрой реакции системы. Потребуется скорректировать количество файлов, возвращаемых в вашем сценарии, но чтобы система быстро отвечала на запросы всех пользователей, не рекомендуется извлекать более 500 файлов за раз.


**Пример.**  
```csharp
StorageFolder folderToEnumerate = KnownFolders.PicturesLibrary; 
// Check if the folder is indexed before doing anything. 
IndexedState folderIndexedState = await folderToEnumerate.GetIndexedStateAsync(); 
if (folderIndexedState == IndexedState.NotIndexed || folderIndexedState == IndexedState.Unknown) 
{ 
    // Only possible in indexed directories.  
    return; 
} 
 
QueryOptions picturesQuery = new QueryOptions() 
{ 
    FolderDepth = FolderDepth.Deep, 
    // Filter out all files that have WIP enabled
    ApplicationSearchFilter = "System.Security.EncryptionOwners:[]", 
    IndexerOption = IndexerOption.OnlyUseIndexerAndOptimizeForIndexedProperties 
}; 

picturesQuery.FileTypeFilter.Add(".jpg"); 
string[] otherProperties = new string[] 
{ 
    SystemProperties.GPS.LatitudeDecimal, 
    SystemProperties.GPS.LongitudeDecimal 
}; 
 
picturesQuery.SetPropertyPrefetch(PropertyPrefetchOptions.BasicProperties | PropertyPrefetchOptions.ImageProperties, 
                                    otherProperties); 
SortEntry sortOrder = new SortEntry() 
{ 
    AscendingOrder = true, 
    PropertyName = "System.FileName" // FileName property is used as an example. Any property can be used here.  
}; 
picturesQuery.SortOrder.Add(sortOrder); 
 
// Create the query and get the results 
uint index = 0; 
const uint stepSize = 100; 
if (!folderToEnumerate.AreQueryOptionsSupported(picturesQuery)) 
{ 
    log("Querying for a sort order is not supported in this location"); 
    picturesQuery.SortOrder.Clear(); 
} 
StorageFileQueryResult queryResult = folderToEnumerate.CreateFileQueryWithOptions(picturesQuery); 
IReadOnlyList<StorageFile> images = await queryResult.GetFilesAsync(index, stepSize); 
while (images.Count != 0 || index < 10000) 
{ 
    foreach (StorageFile file in images) 
    { 
        // With the OnlyUseIndexerAndOptimizeForIndexedProperties set, this won't  
        // be async. It will run synchronously. 
        var imageProps = await file.Properties.GetImagePropertiesAsync(); 
 
        // Build the UI 
        log(String.Format("{0} at {1}, {2}", 
                    file.Path, 
                    imageProps.Latitude, 
                    imageProps.Longitude)); 
    } 
    index += stepSize; 
    images = await queryResult.GetFilesAsync(index, stepSize); 
} 
```

### <a name="results"></a>Результаты 
Полученные файлы StorageFile содержат только запрошенные свойства, но возвращаются в 10 раз быстрее по сравнению с другими вариантами IndexerOptions.Приложение в любом случае может запросить доступ к свойствам, которые не включены в запрос, однако при открытии файла и извлечении этих свойств снижается производительность.  

## <a name="adding-folders-to-libraries"></a>Добавление папок в библиотеки 
Приложения могут потребовать от пользователя добавления расположения в индекс, используя метод [StorageLibrary.RequestAddFolderAsync](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageLibrary.RequestAddFolderAsync). Как только расположение будет включено в индекс, оно автоматически проиндексируется, и приложения смогут использовать эту технику для перечисления файлов.
 
## <a name="see-also"></a>См. также:
[Справочные материалы по API QueryOptions](https://docs.microsoft.com/uwp/api/windows.storage.search.queryoptions)  
[Перечисление файлов и папок и адресация им запросов](https://docs.microsoft.com/windows/uwp/files/quickstart-listing-files-and-folders)  
[Разрешения на доступ к файлам](https://docs.microsoft.com/windows/uwp/files/file-access-permissions)  
[Пошаговое руководство по быстрому доступу к свойствам](https://blogs.msdn.microsoft.com/adamdwilson/2017/12/20/fast-file-enumeration-with-partially-initialized-storagefiles/)
 
 
