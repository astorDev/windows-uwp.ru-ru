---
ms.assetid: BF929A68-9C82-4866-BC13-A32B3A550005
title: Отслеживание недавно использовавшихся файлов и папок
description: Отслеживайте файлы, к которым часто обращается пользователь, добавляя их в список недавно использованных файлов (MRU).
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4a810c097b4f162395106e74b68d5e9cdb2f8538
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8886246"
---
# <a name="track-recently-used-files-and-folders"></a>Отслеживание недавно использовавшихся файлов и папок

**Важные API**

- [**MostRecentlyUsedList**](https://msdn.microsoft.com/library/windows/apps/br207458)
- [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/hh738369)

Отслеживайте файлы, к которым часто обращается пользователь, добавляя их в список недавно использованных файлов (MRU). Платформа управляет списком MRU, сортируя элементы по времени последнего доступа к ним и удаляя самые старые элементы списка, имеющего ограничение в 25 элементов. Каждое приложение имеет собственный список MRU.

Список MRU вашего приложения представлен классом [**StorageItemMostRecentlyUsedList**](https://msdn.microsoft.com/library/windows/apps/br207475), полученным из статического свойства [**StorageApplicationPermissions.MostRecentlyUsedList**](https://msdn.microsoft.com/library/windows/apps/br207458). Элементы MRU хранятся в виде объектов [**IStorageItem**](https://msdn.microsoft.com/library/windows/apps/br227129). Поэтому в список можно добавлять и объекты [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) (то есть файлы), и объекты [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230) (то есть папки).

> [!NOTE]
> См. также [пример средства выбора файлов](http://go.microsoft.com/fwlink/p/?linkid=619994) и [пример доступа к файлам](http://go.microsoft.com/fwlink/p/?linkid=619995).

 

## <a name="prerequisites"></a>Необходимые условия

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Права на доступ к расположению**

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

    [**StorageItemMostRecentlyUsedList.Add**](https://msdn.microsoft.com/library/windows/apps/br207476) перегружается. В примере используется [**Add(IStorageItem, String)**](https://msdn.microsoft.com/library/windows/apps/br207481), поэтому можно сопоставить метаданные с файлом. Указание метаданных позволяет записать назначение элемента, например "изображение профиля". Кроме того, вы можете добавить файл в список MRU и без метаданных, вызвав метод [**Add(IStorageItem)**](https://msdn.microsoft.com/library/windows/apps/br207480). При добавлении элемента в список MRU метод возвращает уникальную идентификационную строку, так называемый маркер, который используется для извлечения элемента.

> [!TIP]
> Чтобы извлечь элемент из списка MRU, вам потребуется маркер, потому сохраните его. Подробные сведения о данных приложения см. в статье [Управление данными приложения](https://msdn.microsoft.com/library/windows/apps/hh465109).

## <a name="use-a-token-to-retrieve-an-item-from-the-mru"></a>Использование маркера для извлечения элемента из списка MRU

Для извлечения элемента используйте наиболее подходящий метод.

-   Файл извлекается как [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) с помощью метода [**GetFileAsync**](https://msdn.microsoft.com/library/windows/apps/br207486).
-   Папка извлекается как [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230) с помощью метода [**GetFolderAsync**](https://msdn.microsoft.com/library/windows/apps/br207489).
-   Общий интерфейс [**IStorageItem**](https://msdn.microsoft.com/library/windows/apps/br227129), который может представлять как файл, так и папку, извлекается с помощью метода [**GetItemAsync**](https://msdn.microsoft.com/library/windows/apps/br207492).

Вот как вернуть только что добавленный файл:

```cs
StorageFile retrievedFile = await mru.GetFileAsync(mruToken);
```

Вот как выполнить итерацию всех записей, чтобы получить маркеры, а затем— элементы:

```cs
foreach (Windows.Storage.AccessCache.AccessListEntry entry in mru.Entries)
{
    string mruToken = entry.Token;
    string mruMetadata = entry.Metadata;
    Windows.Storage.IStorageItem item = await mru.GetItemAsync(mruToken);
    // The type of item will tell you whether it's a file or a folder.
}
```

Класс [**AccessListEntryView**](https://msdn.microsoft.com/library/windows/apps/br227349) позволяет выполнять итерацию записей в MRU. Эти записи— структуры [**AccessListEntry**](https://msdn.microsoft.com/library/windows/apps/br227348), которые содержат маркер и метаданные элемента.

## <a name="removing-items-from-the-mru-when-its-full"></a>Удаление элементов из списка MRU при его заполнении

Если достигнуто ограничение списка в 25 элементов, и вы пытаетесь добавить новый элемент, то элемент, к которому не обращались самое продолжительное время, автоматически удаляется. Поэтому нет необходимости удалять элемент перед добавлением нового.

## <a name="future-access-list"></a>Список дальнейшего доступа

Помимо списка MRU ваше приложение также имеет список дальнейшего доступа. Выбирая файлы и папки, пользователь предоставляет вашему приложению разрешение на доступ к элементам, к которым невозможно получить доступ иным способом. Когда вы добавляете эти элементы в список дальнейшего доступа, то сохраняете разрешение на случай, когда приложению понадобится получить доступ к элементам позже. Список дальнейшего доступа приложения представлен классом [**StorageItemAccessList**](https://msdn.microsoft.com/library/windows/apps/br207459), полученным из статического свойства [**StorageApplicationPermissions.FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457).

Когда пользователь выбирает элемент, вы можете добавить его в список дальнейшего доступа, а также в список MRU.

-   В списке [**FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457) может содержаться до 1000 элементов. Помните: он может содержать как папки, так и файлы, т. е. множество папок.
-   Платформа не удаляет элементы из списка [**FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457) самостоятельно. Если достигнут предел в 1000 элементов, вы не можете добавить следующий элемент, пока не освободите место с помощью метода [**Remove**](https://msdn.microsoft.com/library/windows/apps/br207497).
