---
ms.assetid: BF929A68-9C82-4866-BC13-A32B3A550005
title: Отслеживание недавно использовавшихся файлов и папок
description: Отслеживайте файлы, к которым часто обращается пользователь, добавляя их в список недавно использованных файлов (MRU).
ms.date: 12/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 318c58b393a33916df7bab51a4ef2690494d14fb
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259609"
---
# <a name="track-recently-used-files-and-folders"></a>Отслеживание недавно использовавшихся файлов и папок

**Важные API**

- [**MostRecentlyUsedList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.mostrecentlyusedlist)
- [**FileOpenPicker**](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-fileopenpicker)

Отслеживайте файлы, к которым часто обращается пользователь, добавляя их в список недавно использованных файлов (MRU). Платформа управляет списком MRU, сортируя элементы по времени последнего доступа к ним и удаляя самые старые элементы списка, имеющего ограничение в 25 элементов. Каждое приложение имеет собственный список MRU.

Список MRU вашего приложения представлен классом [**StorageItemMostRecentlyUsedList**](https://docs.microsoft.com/uwp/api/Windows.Storage.AccessCache.StorageItemMostRecentlyUsedList), полученным из статического свойства [**StorageApplicationPermissions.MostRecentlyUsedList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.mostrecentlyusedlist). Элементы MRU хранятся в виде объектов [**IStorageItem**](https://docs.microsoft.com/uwp/api/Windows.Storage.IStorageItem). Поэтому в список можно добавлять и объекты [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) (то есть файлы), и объекты [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder) (то есть папки).

> [!NOTE]
> Полные примеры: [пример средства выбора файлов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FilePicker) и [пример доступа к файлам](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FileAccess).

## <a name="prerequisites"></a>Предварительные условия

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)** .

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://docs.microsoft.com/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps).

-   **Права доступа к расположению**

    См. раздел [Разрешения на доступ к файлам](file-access-permissions.md).

-   [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md)

    К выбранным файлам часто принадлежат файлы, к которым пользователь возвращается снова и снова.

 ## <a name="add-a-picked-file-to-the-mru"></a>Добавление выбранного файла в список MRU

-   К файлам, которые выбирает пользователь, часто принадлежат файлы, к которым он постоянно возвращается. Поэтому рекомендуется добавить выбранные файлы в список MRU вашего приложения сразу после их выбора. Вот как это сделать.

    ```cs
    Windows.Storage.StorageFile file = await picker.PickSingleFileAsync();

    var mru = Windows.Storage.AccessCache.StorageApplicationPermissions.MostRecentlyUsedList;
    string mruToken = mru.Add(file, "profile pic");
    ```

    [**StorageItemMostRecentlyUsedList.Add**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageitemmostrecentlyusedlist.add) перегружается. В примере используется [**Add(IStorageItem, String)** ](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageitemmostrecentlyusedlist.add), поэтому можно сопоставить метаданные с файлом. Указание метаданных позволяет записать назначение элемента, например "изображение профиля". Кроме того, вы можете добавить файл в список MRU и без метаданных, вызвав метод [**Add(IStorageItem)** ](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageitemmostrecentlyusedlist.add). При добавлении элемента в список MRU метод возвращает уникальную идентификационную строку, так называемый маркер, который используется для извлечения элемента.

> [!TIP]
> Чтобы извлечь элемент из списка MRU, вам потребуется маркер, поэтому сохраните его. Подробные сведения о данных приложения см. в статье [Управление данными приложения](https://docs.microsoft.com/previous-versions/windows/apps/hh465109(v=win.10)).

## <a name="use-a-token-to-retrieve-an-item-from-the-mru"></a>Использование маркера для извлечения элемента из списка MRU

Для извлечения элемента используйте наиболее подходящий метод.

-   Файл извлекается как [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) с помощью метода [**GetFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageitemmostrecentlyusedlist.getfileasync).
-   Папка извлекается как [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder) с помощью метода [**GetFolderAsync**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageitemmostrecentlyusedlist.getfolderasync).
-   Общий интерфейс [**IStorageItem**](https://docs.microsoft.com/uwp/api/Windows.Storage.IStorageItem), который может представлять как файл, так и папку, извлекается с помощью метода [**GetItemAsync**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageitemmostrecentlyusedlist.getitemasync).

Вот как вернуть только что добавленный файл:

```cs
StorageFile retrievedFile = await mru.GetFileAsync(mruToken);
```

Вот как выполнить итерацию всех записей, чтобы получить маркеры, а затем — элементы:

```cs
foreach (Windows.Storage.AccessCache.AccessListEntry entry in mru.Entries)
{
    string mruToken = entry.Token;
    string mruMetadata = entry.Metadata;
    Windows.Storage.IStorageItem item = await mru.GetItemAsync(mruToken);
    // The type of item will tell you whether it's a file or a folder.
}
```

Класс [**AccessListEntryView**](https://docs.microsoft.com/uwp/api/Windows.Storage.AccessCache.AccessListEntryView) позволяет выполнять итерацию записей в MRU. Эти записи — структуры [**AccessListEntry**](https://docs.microsoft.com/uwp/api/Windows.Storage.AccessCache.AccessListEntry), которые содержат маркер и метаданные элемента.

## <a name="removing-items-from-the-mru-when-its-full"></a>Удаление элементов из списка MRU при его заполнении

Если достигнуто ограничение списка в 25 элементов, и вы пытаетесь добавить новый элемент, то элемент, к которому не обращались самое продолжительное время, автоматически удаляется. Поэтому нет необходимости удалять элемент перед добавлением нового.

## <a name="future-access-list"></a>Список дальнейшего доступа

Помимо списка MRU ваше приложение также имеет список дальнейшего доступа. Выбирая файлы и папки, пользователь предоставляет вашему приложению разрешение на доступ к элементам, к которым невозможно получить доступ иным способом. Когда вы добавляете эти элементы в список дальнейшего доступа, то сохраняете разрешение на случай, когда приложению понадобится получить доступ к элементам позже. Список дальнейшего доступа приложения представлен классом [**StorageItemAccessList**](https://docs.microsoft.com/uwp/api/Windows.Storage.AccessCache.StorageItemAccessList), полученным из статического свойства [**StorageApplicationPermissions.FutureAccessList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.futureaccesslist).

Когда пользователь выбирает элемент, вы можете добавить его в список дальнейшего доступа, а также в список MRU.

-   В списке [**FutureAccessList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.futureaccesslist) может содержаться до 1000 элементов. Помните: он может содержать как папки, так и файлы, т. е. множество папок.
-   Платформа не удаляет элементы из списка [**FutureAccessList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.futureaccesslist) самостоятельно. Если достигнут предел в 1000 элементов, вы не можете добавить следующий элемент, пока не освободите место с помощью метода [**Remove**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageitemmostrecentlyusedlist.remove).
