---
ms.assetid: 3604524F-112A-474F-B0CA-0726DC8DB885
title: Определение доступности файлов Microsoft OneDrive
description: Определите доступность файла Microsoft OneDrive с помощью свойства StorageFile.IsAvailable.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 26eb371e767f0c1e7f3d5855cf68728958c0cda3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608939"
---
# <a name="determining-availability-of-microsoft-onedrive-files"></a>Определение доступности файлов Microsoft OneDrive


**Важные API**

-   [**Класс файловый ввод-вывод**](https://msdn.microsoft.com/library/windows/apps/Hh701440)
-   [**Класс StorageFile**](https://msdn.microsoft.com/library/windows/apps/BR227171)
-   [**Свойство StorageFile.IsAvailable**](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.isavailable.aspx)

Определите доступность файла Microsoft OneDrive с помощью свойства [**StorageFile.IsAvailable**](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.isavailable.aspx).

## <a name="prerequisites"></a>Предварительные условия

-   **Понять асинхронного программирования для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/Mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/Mt187334).

-   **Объявления capabilty приложения**

    См. раздел [Разрешения на доступ к файлам](file-access-permissions.md).

## <a name="using-the-storagefileisavailable-property"></a>Использование свойства StorageFile.IsAvailable

Пользователи могут отмечать файлы OneDrive как "доступные автономно" (по умолчанию) и как "только в сети". Благодаря этому пользователи могут перемещать большие файлы (например, изображения или видео) в свое хранилище OneDrive, отмечать их как «только в сети» и тем самым экономить место на диске. (На локальном диске будет храниться только файл, содержащий метаданные.)

[**StorageFile.IsAvailable**](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.isavailable.aspx), используется для определения, является ли файл в настоящее время недоступно. Значения свойства **StorageFile.IsAvailable** в различных сценариях перечислены в следующей таблице.

| Тип файла                              | Online | Сеть с лимитным тарифным планом        | Вне сети |
|-------------------------------------------|--------|------------------------|---------|
| Локальный файл                                | True   | True                   | True    |
| Файл OneDrive с отметкой "доступен автономно" | True   | True                   | True    |
| Файл OneDrive с отметкой "только в сети"       | True   | На основании настройки пользователя | False   |
| Сетевой файл                              | True   | На основании настройки пользователя | False   |

 

Далее описан порядок действий при определении того, является ли файл доступным в настоящий момент.

1.  Объявите возможность, соответствующую библиотеке, к которой требуется получить доступ.
2.  Включите пространство имен [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/BR227346). Это пространство имен содержит типы для управления файлами, папками и параметрами приложений. Кроме того, оно включает необходимый тип [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/BR227171).
3.  Получите объект [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/BR227171) для нужных файлов. Если выполняется перечисление библиотеки, для выполнения этого шага обычно необходимо вызвать метод [**StorageFolder.CreateFileQuery**](https://msdn.microsoft.com/library/windows/apps/BR227252), а затем — метод [**GetFilesAsync**](https://msdn.microsoft.com/library/windows/apps/br227276.aspx) результирующего объекта [**StorageFileQueryResult**](https://msdn.microsoft.com/library/windows/apps/BR208046). Метод **GetFilesAsync** возвращает коллекцию [IReadOnlyList](https://go.microsoft.com/fwlink/p/?LinkId=324970) объектов **StorageFile**.
4.  После получения доступа к объекту [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/BR227171), представляющему нужные файлы, значение свойства [**StorageFile.IsAvailable**](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.isavailable.aspx) показывает, доступен ли файл.

Следующий общий метод иллюстрирует способ перечисления любой папки и возвращения коллекции объектов [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/BR227171) для этой папки. Затем метод вызова повторяется для возвращенной коллекции, ссылаясь на свойство [**StorageFile.IsAvailable**](https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefile.isavailable.aspx) каждого файла.

```cs
/// <summary>
/// Generic function that retrieves all files from the specified folder.
/// </summary>
/// <param name="folder">The folder to be searched.</param>
/// <returns>An IReadOnlyList collection containing the file objects.</returns>
async Task<System.Collections.Generic.IReadOnlyList<StorageFile>> GetLibraryFilesAsync(StorageFolder folder)
{
    var query = folder.CreateFileQuery();
    return await query.GetFilesAsync();
}

private async void CheckAvailabilityOfFilesInPicturesLibrary()
{
    // Determine availability of all files within Pictures library.
    var files = await GetLibraryFilesAsync(KnownFolders.PicturesLibrary);
    for (int i = 0; i < files.Count; i++)
    {
        StorageFile file = files[i];

        StringBuilder fileInfo = new StringBuilder();
        fileInfo.AppendFormat("{0} (on {1}) is {2}",
                    file.Name,
                    file.Provider.DisplayName,
                    file.IsAvailable ? "available" : "not available");
    }
}
```
