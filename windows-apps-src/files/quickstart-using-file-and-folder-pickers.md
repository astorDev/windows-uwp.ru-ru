---
author: laurenhughes
ms.assetid: F87DBE2F-77DB-4573-8172-29E11ABEFD34
title: Открытие файлов и папок с помощью средства выбора
description: Получите доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. Можно использовать классы FileOpenPicker и FileSavePicker для получения доступа к файлам, а также FolderPicker — для получения доступа к папкам.
ms.author: lahugh
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b79bfa9ecdf76d2d59e3c0991240d88599dbe6dd
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6970909"
---
# <a name="open-files-and-folders-with-a-picker"></a>Открытие файлов и папок с помощью средства выбора

**Важные API**

-   [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847)
-   [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/br207881)
-   [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171)

Получите доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. Для получения доступа к файлам можно использовать классы [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) и [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871), а для получения доступа к папкам— класс [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/br207881).

> [!NOTE]
> Полный пример см. в статье [Пример средства выбора файлов](http://go.microsoft.com/fwlink/p/?linkid=619994).

## <a name="prerequisites"></a>Необходимые условия


-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Права на доступ к расположению**

    См. раздел [Разрешения на доступ к файлам](file-access-permissions.md).

## <a name="file-picker-ui"></a>Пользовательский интерфейс средства выбора файлов


В средстве выбора файлов отображаются сведения, позволяющие сориентировать пользователей и обеспечить им привычное взаимодействие с системой при открытии и сохранении файлов.

Эти сведения включают следующее:

-   текущее расположение;
-   выбранный пользователем элемент или несколько элементов;
-   дерево расположений, к которым может перейти пользователь. Они включают расположения в файловой системе, например папку «Музыка» или «Загрузки», а также приложения, которые реализуют контракт средства выбора файлов (такие как «Камера», «Фотографии» и Microsoft OneDrive).

Почтовое приложение может отображать средство выбора файлов, чтобы предоставить пользователю возможность выбора вложений.

![Средство выбора файлов, в котором выбрано два файла для открытия.](images/picker-multifile-600px.png)

## <a name="how-pickers-work"></a>Как работают средства выбора


Используя средство выбора, ваше приложение может получать доступ к файлам и папкам в системе пользователя, просматривать и сохранять их. Ваше приложение получает эти выбранные элементы в виде объектов [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) и [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), с которыми потом можно работать.

Средство выбора использует единый унифицированный интерфейс для обеспечения пользователю возможности выбора файлов и папок из файловой системы либо из других приложений. Файлы, выбранные из других приложений, воспринимаются как файлы из файловой системы: они возвращаются как объекты [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171). В целом ваше приложение может работать с ними так же, как с другими объектами. Другие приложения обеспечивают доступ к файлам за счет участия в контрактах средства выбора файлов. Сведения о настройке вашего приложения для предоставления другим приложениям файлов, места сохранения или обновлений файлов см. в разделе [Взаимодействие с контрактами "Средство выбора файлов"](https://msdn.microsoft.com/library/windows/apps/hh465192).

Например, вы можете вызывать средство выбора файлов в своем приложении, чтобы пользователь имел возможность открыть необходимый файл. За счет этого ваше приложение становится вызывающим приложением. Средство выбора файлов взаимодействует с системой и/или другими приложениями, обеспечивая пользователю возможность поиска и выбора определенного файла. Когда пользователь выбирает файл, средство выбора файлов возвращает этот файл в ваше приложение. Ниже показан процесс, который имеет место при выборе пользователем файла из другого приложения, например OneDrive.

![Схема, на которой одно приложение получает файл для открытия из другого приложения с помощью средства выбора файлов, играющего роль интерфейса между двумя приложениями.](images/app-to-app-diagram-600px.png)

## <a name="pick-a-single-file-complete-code-listing"></a>Выбор одного файла: полный код


```cs
var picker = new Windows.Storage.Pickers.FileOpenPicker();
picker.ViewMode = Windows.Storage.Pickers.PickerViewMode.Thumbnail;
picker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.PicturesLibrary;
picker.FileTypeFilter.Add(".jpg");
picker.FileTypeFilter.Add(".jpeg");
picker.FileTypeFilter.Add(".png");

Windows.Storage.StorageFile file = await picker.PickSingleFileAsync();
if (file != null)
{
    // Application now has read/write access to the picked file
    this.textBlock.Text = "Picked photo: " + file.Name;
}
else
{
    this.textBlock.Text = "Operation cancelled.";
}
```

## <a name="pick-a-single-file-step-by-step"></a>Выбор одного файла: пошаговые инструкции


Использование средства выбора файлов включает создание и настройку объекта «средство выбора файлов» и отображение средства выбора файлов, чтобы дать пользователю возможность выбрать один или несколько элементов.

1.  **Создание и настройка FileOpenPicker**

    ```cs
    var picker = new Windows.Storage.Pickers.FileOpenPicker();
    picker.ViewMode = Windows.Storage.Pickers.PickerViewMode.Thumbnail;
    picker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.PicturesLibrary;
    picker.FileTypeFilter.Add(".jpg");
    picker.FileTypeFilter.Add(".jpeg");
    picker.FileTypeFilter.Add(".png");
    ```
    Задайте свойства объекта «средство выбора файлов», соответствующие вашим пользователям и приложению.

    В этом примере создается сложное визуальное представление в виде картинок в удобном расположении, из которых может выбирать пользователь, путем задания трех свойств: [**ViewMode**](https://msdn.microsoft.com/library/windows/apps/br207855), [**SuggestedStartLocation**](https://msdn.microsoft.com/library/windows/apps/br207854) и [**FileTypeFilter**](https://msdn.microsoft.com/library/windows/apps/br207850).

    -   Задание свойства [**ViewMode**](https://msdn.microsoft.com/library/windows/apps/br207855) равным значению **Thumbnail** перечисления [**PickerViewMode**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.storage.pickers.pickerviewmode.aspx#thumbnail) создает сложное визуальное представление за счет эскизов картинок, представляющих файлы в средстве выбора файлов. Выполните это, чтобы выбрать визуальные файлы, например фотографии или видео. В других случаях воспользуйтесь [**PickerViewMode.List**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.storage.pickers.pickerviewmode.aspx#list). Гипотетическое почтовое приложение с функциями **Прикрепить изображение или видео** и **Прикрепить документ** установит свойство **ViewMode**, соответствующее функции, прежде чем отобразить средство выбора файлов.

    -   Выбор библиотеки «Изображения» в качестве значения для параметра [**SuggestedStartLocation**](https://msdn.microsoft.com/library/windows/apps/br207854) с помощью [**PickerLocationId.PicturesLibrary**](https://msdn.microsoft.com/library/windows/apps/br207890) позволяет пользователю начать работу в расположении вероятного нахождения изображений. Присвойте свойство **SuggestedStartLocation** расположению, подходящему для типа выбранных файлов, например «Музыка», «Изображения», «Видео» или «Документы». Из начального расположения пользователь может перейти в другие расположения.

    -   Использование [**FileTypeFilter**](https://msdn.microsoft.com/library/windows/apps/br207850) для указания типов файлов сохраняет внимание пользователя на выборе соответствующих файлов. Чтобы заменить предыдущие типы файлов в **FileTypeFilter** новыми записями, используйте вместо метода [**Add**](https://msdn.microsoft.com/library/windows/apps/br207834) метод [**ReplaceAll**](https://msdn.microsoft.com/library/windows/apps/br207844).

2.  **Отображение FileOpenPicker**

    - **Выбор одного файла**

    ```cs
    Windows.Storage.StorageFile file = await picker.PickSingleFileAsync();
    if (file != null)
    {
        // Application now has read/write access to the picked file
        this.textBlock.Text = "Picked photo: " + file.Name;
    }
    else
    {
        this.textBlock.Text = "Operation cancelled.";
    }
    ```

    - **Выбор нескольких файлов**  

    ```cs
    var files = await picker.PickMultipleFilesAsync();
    if (files.Count > 0)
    {
        StringBuilder output = new StringBuilder("Picked files:\n");

        // Application now has read/write access to the picked file(s)
        foreach (Windows.Storage.StorageFile file in files)
        {
            output.Append(file.Name + "\n");
        }
        this.textBlock.Text = output.ToString();
    }
    else
    {
        this.textBlock.Text = "Operation cancelled.";
    }
    ```

## <a name="pick-a-folder-complete-code-listing"></a>Выбор папки: приводится полный код


```cs
var folderPicker = new Windows.Storage.Pickers.FolderPicker();
folderPicker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.Desktop;
folderPicker.FileTypeFilter.Add("*");

Windows.Storage.StorageFolder folder = await folderPicker.PickSingleFolderAsync();
if (folder != null)
{
    // Application now has read/write access to all contents in the picked folder
    // (including other sub-folder contents)
    Windows.Storage.AccessCache.StorageApplicationPermissions.
    FutureAccessList.AddOrReplace("PickedFolderToken", folder);
    this.textBlock.Text = "Picked folder: " + folder.Name;
}
else
{
    this.textBlock.Text = "Operation cancelled.";
}
```

> [!TIP]
> Каждый раз, когда ваше приложение получает доступ к файлу или папке через средство выбора, добавляйте этот файл или папку в свойство [**FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457) или [**MostRecentlyUsedList**](https://msdn.microsoft.com/library/windows/apps/br207458) приложения для отслеживания. Подробнее об использовании этих списков см. в разделе [Отслеживание последних использованных файлов и папок](how-to-track-recently-used-files-and-folders.md).