---
ms.assetid: F87DBE2F-77DB-4573-8172-29E11ABEFD34
title: Открытие файлов и папок с помощью средства выбора
description: Получите доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. Можно использовать классы FileOpenPicker и FileSavePicker для получения доступа к файлам, а также FolderPicker — для получения доступа к папкам.
ms.date: 12/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b3143247eab0bd8ed4266b78e34ec54dafa4dd7e
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "74259569"
---
# <a name="open-files-and-folders-with-a-picker"></a>Открытие файлов и папок с помощью средства выбора

**Важные API**

-   [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker)
-   [**FolderPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FolderPicker)
-   [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile)

Получите доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. Для получения доступа к файлам можно использовать классы [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) и [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker), а для получения доступа к папкам — класс [**FolderPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FolderPicker).

> [!NOTE]
> Полный пример: [пример средства выбора файлов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FilePicker).

## <a name="prerequisites"></a>Предварительные условия


-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)** .

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://docs.microsoft.com/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps).

-   **Права доступа к расположению**

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


Используя средство выбора, ваше приложение может получать доступ к файлам и папкам в системе пользователя, просматривать и сохранять их. Ваше приложение получает эти выбранные элементы в виде объектов [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) и [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder), с которыми потом можно работать.

Средство выбора использует единый унифицированный интерфейс для обеспечения пользователю возможности выбора файлов и папок из файловой системы либо из других приложений. Файлы, выбранные из других приложений, воспринимаются как файлы из файловой системы: они возвращаются как объекты [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile). В целом ваше приложение может работать с ними так же, как с другими объектами. Другие приложения обеспечивают доступ к файлам за счет участия в контрактах средства выбора файлов. Сведения о настройке вашего приложения для предоставления другим приложениям файлов, места сохранения или обновлений файлов см. в разделе [Взаимодействие с контрактами "Средство выбора файлов"](https://docs.microsoft.com/previous-versions/windows/apps/hh465192(v=win.10)).

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

    Этот пример создает сложное визуальное отображение в виде картинок в подходящем расположении, которое пользователь может выбрать, установив три свойства: [**ViewMode**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.viewmode), [**SuggestedStartLocation**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.suggestedstartlocation) и [**FileTypeFilter**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.filetypefilter).

    -   Задание для свойства [**ViewMode**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.viewmode) значения **Thumbnail** перечисления [**PickerViewMode**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.PickerViewMode) создает сложное визуальное представление за счет эскизов изображений, представляющих файлы в средстве выбора файлов. Выполните это, чтобы выбрать визуальные файлы, например фотографии или видео. В других случаях воспользуйтесь [**PickerViewMode.List**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.PickerViewMode). Гипотетическое почтовое приложение с функциями **Прикрепить изображение или видео** и **Прикрепить документ** установит свойство **ViewMode**, соответствующее функции, прежде чем отобразить средство выбора файлов.

    -   Выбор библиотеки «Изображения» в качестве значения для параметра [**SuggestedStartLocation**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.suggestedstartlocation) с помощью [**PickerLocationId.PicturesLibrary**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.PickerLocationId) позволяет пользователю начать работу в расположении вероятного нахождения изображений. Присвойте свойство **SuggestedStartLocation** расположению, подходящему для типа выбранных файлов, например «Музыка», «Изображения», «Видео» или «Документы». Из начального расположения пользователь может перейти в другие расположения.

    -   Использование [**FileTypeFilter**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.filetypefilter) для указания типов файлов сохраняет внимание пользователя на выборе соответствующих файлов. Чтобы заменить предыдущие типы файлов в **FileTypeFilter** новыми записями, используйте вместо метода [**Add**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileextensionvector.append) метод [**ReplaceAll**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileextensionvector.replaceall).

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

## <a name="pick-a-folder-complete-code-listing"></a>Выбор папки: полный код


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
> Каждый раз, когда ваше приложение получает доступ к файлу или папке через средство выбора, добавляйте этот файл или папку в свойство [**FutureAccessList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.futureaccesslist) или [**MostRecentlyUsedList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.mostrecentlyusedlist) приложения для отслеживания. Подробнее об использовании этих списков см. в разделе [Отслеживание последних использованных файлов и папок](how-to-track-recently-used-files-and-folders.md).