---
ms.assetid: 3604524F-112A-474F-B0CA-0726DC8DB885
title: Определение доступности файлов Microsoft OneDrive
description: Определите доступность файла Microsoft OneDrive с помощью свойства StorageFile.IsAvailable.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 36835a198d03a8ad5f5e811a74e120c9bbd25c08
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258590"
---
# <a name="determining-availability-of-microsoft-onedrive-files"></a>Определение доступности файлов Microsoft OneDrive


**Важные API**

-   [**Класс FileIO**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileIO)
-   [**Класс StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile)
-   [**Свойство StorageFile.IsAvailable**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.isavailable)

Определите доступность файла Microsoft OneDrive с помощью свойства [**StorageFile.IsAvailable**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.isavailable).

## <a name="prerequisites"></a>Предварительные условия

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)** .

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://docs.microsoft.com/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps).

-   **Объявление возможностей приложения**

    См. раздел [Разрешения на доступ к файлам](file-access-permissions.md).

## <a name="using-the-storagefileisavailable-property"></a>Использование свойства StorageFile.IsAvailable

Пользователи могут отмечать файлы OneDrive как "доступные автономно" (по умолчанию) и как "только в сети". Благодаря этому пользователи могут перемещать большие файлы (например, изображения или видео) в свое хранилище OneDrive, отмечать их как «только в сети» и тем самым экономить место на диске. (На локальном диске будет храниться только файл, содержащий метаданные.)

[**StorageFile.IsAvailable**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.isavailable) позволяет определить, доступен ли файл в настоящий момент. Значения свойства **StorageFile.IsAvailable** в различных сценариях перечислены в следующей таблице.

| Тип файла                              | Online | Сеть с лимитным тарифным планом        | Вне сети |
|-------------------------------------------|--------|------------------------|---------|
| Локальный файл                                | True   | True                   | True    |
| Файл OneDrive с отметкой "доступен автономно" | True   | True                   | True    |
| Файл OneDrive с отметкой "только в сети"       | True   | На основании настройки пользователя | False   |
| Сетевой файл                              | True   | На основании настройки пользователя | False   |

 

Далее описан порядок действий при определении того, является ли файл доступным в настоящий момент.

1.  Объявите возможность, соответствующую библиотеке, к которой требуется получить доступ.
2.  Включите пространство имен [**Windows.Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage). Это пространство имен содержит типы для управления файлами, папками и параметрами приложений. Кроме того, оно включает необходимый тип [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile).
3.  Получите объект [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) для нужных файлов. Если выполняется перечисление библиотеки, для выполнения этого шага обычно необходимо вызвать метод [**StorageFolder.CreateFileQuery**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.createfilequery), а затем — метод [**GetFilesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfilesasync) результирующего объекта [**StorageFileQueryResult**](https://docs.microsoft.com/uwp/api/Windows.Storage.Search.StorageFileQueryResult). Метод **GetFilesAsync** возвращает коллекцию [IReadOnlyList](https://msdn.microsoft.com/library/hh192385.aspx) объектов **StorageFile**.
4.  После получения доступа к объекту [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), представляющему нужные файлы, значение свойства [**StorageFile.IsAvailable**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.isavailable) показывает, доступен ли файл.

Следующий общий метод иллюстрирует способ перечисления любой папки и возвращения коллекции объектов [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) для этой папки. Затем метод вызова повторяется для возвращенной коллекции, ссылаясь на свойство [**StorageFile.IsAvailable**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.isavailable) каждого файла.

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
