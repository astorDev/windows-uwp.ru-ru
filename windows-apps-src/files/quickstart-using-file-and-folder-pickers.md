---
author: TylerMSFT
ms.assetid: F87DBE2F-77DB-4573-8172-29E11ABEFD34
title: "Открытие файлов и папок с помощью средства выбора"
description: "Получите доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. Можно использовать классы FileOpenPicker и FileSavePicker для получения доступа к файлам, а также FolderPicker — для получения доступа к папкам."
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: efb0b106c779820b2dee48eff6f09b54ae9ef2c4

---

# Открытие файлов и папок с помощью средства выбора


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847)
-   [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/br207881)
-   [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171)

Получите доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. Можно использовать классы [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) и [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871) для получения доступа к файлам, а также [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/br207881) — для получения доступа к папкам.

**Примечание.** См. также [пример средства выбора файлов](http://go.microsoft.com/fwlink/p/?linkid=619994).

 

## Необходимые условия


-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Права на доступ к расположению**

    См. раздел [Разрешения на доступ к файлам](file-access-permissions.md).

## Пользовательский интерфейс средства выбора файлов


Средство выбора файлов отображает сведения, позволяющие сориентировать пользователей и обеспечить им привычное взаимодействие с системой при доступе к файлам и их сохранении.

Эти сведения включают следующее:

-   текущее расположение;
-   выбранный пользователем элемент или несколько элементов;
-   дерево расположений, к которым может перейти пользователь. Они включают расположения в файловой системе, например папку «Музыка» или «Загрузки», а также приложения, которые реализуют контракт средства выбора файлов (такие как «Камера», «Фотографии» и Microsoft OneDrive).

Почтовое приложение может отображать средство выбора файлов, чтобы предоставить пользователю возможность выбора вложений.

![a file picker with two files picked to be opened.](images/picker-multifile-600px.png)

## Работа средств выбора


Используя средство выбора, ваше приложение может получить доступ к файлам и папкам в системе пользователя. С помощью средства выбора пользователь ищет в системе файлы (или папки) для открытия или сохранения. Ваше приложение получает эти выбранные элементы в виде объектов [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) и [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), с которыми потом можно работать.

Средство выбора использует единый унифицированный интерфейс для обеспечения пользователю возможности выбора файлов и папок из файловой системы либо из других приложений. Файлы, выбранные из других приложений, воспринимаются как файлы из файловой системы: они возвращаются как объекты [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171). В целом ваше приложение может работать с ними так же, как с другими объектами подобного типа. Другие приложения обеспечивают доступ к файлам за счет участия в контрактах "Средство выбора файлов". Сведения о настройке вашего приложения для предоставления другим приложениям файлов, места сохранения или обновлений файлов см. в разделе [Взаимодействие с контрактами "Средство выбора файлов"](https://msdn.microsoft.com/library/windows/apps/hh465192).

Например, вы можете вызывать средство выбора файлов в своем приложении, чтобы пользователь имел возможность открыть необходимый файл. За счет этого ваше приложение становится вызывающим приложением. Средство выбора файлов взаимодействует с системой и/или другими приложениями, обеспечивая пользователю возможность поиска и выбора определенного файла. Когда пользователь выбирает файл, средство выбора файлов возвращает этот файл в ваше приложение. Ниже показан процесс, происходящий, когда пользователь выбирает файл из другого приложения, например OneDrive. В данном случае OneDrive является поставляющим приложением.

![На схеме одно приложение получает файл, который нужно открыть из другого, с использованием средства выбора файлов, играющего роль интерфейса между двумя приложениями.](images/app-to-app-diagram-600px.png)

## Выбор одного файла для открытия: приводится полный код


```CSharp
var picker = new Windows.Storage.Pickers.FileOpenPicker();
picker.ViewMode = Windows.Storage.Pickers.PickerViewMode.Thumbnail;
picker.SuggestedStartLocation = 
    Windows.Storage.Pickers.PickerLocationId.PicturesLibrary;
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

Код выбора нескольких файлов см. в разделе [Пример средства выбора файлов](http://go.microsoft.com/fwlink/p/?linkid=619994).

## Выбор одного файла для открытия: пошаговые инструкции


Использование средства выбора файлов включает создание и настройку объекта средства выбора файлов и отображение средства выбора файлов, чтобы пользователь мог выбрать элементы.

1.  **Создание и настройка FileOpenPicker**

```CSharp
var picker = new Windows.Storage.Pickers.FileOpenPicker();
    picker.ViewMode = Windows.Storage.Pickers.PickerViewMode.Thumbnail;
    picker.SuggestedStartLocation = 
        Windows.Storage.Pickers.PickerLocationId.PicturesLibrary;
    picker.FileTypeFilter.Add(".jpg");
    picker.FileTypeFilter.Add(".jpeg");
    picker.FileTypeFilter.Add(".png");
```

Задайте свойства объекта средства выбора файлов, соответствующие вашим пользователям и приложению. Указания по настройке средства выбора файлов см. в разделе [Руководство и контрольный список для средств выбора файлов](https://msdn.microsoft.com/library/windows/apps/hh465182).

Этот пример создает сложное визуальное отображение в виде картинок в подходящем расположении, которое пользователь может выбрать, установив три свойства: [**ViewMode**](https://msdn.microsoft.com/library/windows/apps/br207855), [**SuggestedStartLocation**](https://msdn.microsoft.com/library/windows/apps/br207854) и [**FileTypeFilter**](https://msdn.microsoft.com/library/windows/apps/br207850).

-   Установка [**ViewMode**](https://msdn.microsoft.com/library/windows/apps/br207855) на перечислимые значения **Thumbnail**[**PickerViewMode**](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.storage.pickers.pickerviewmode.aspx#thumbnail) создает сложное визуальное отображение за счет эскизов картинок, представляющих файлы в средстве выбора файлов. Выполните это, чтобы выбрать визуальные файлы, например фотографии или видео. В других случаях воспользуйтесь [**PickerViewMode.List**](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/windows.storage.pickers.pickerviewmode.aspx#list). Гипотетическое почтовое приложение с функциями **Прикрепить изображение или видео** и **Прикрепить документ** установит свойство **ViewMode**, соответствующее функции, прежде чем отобразить средство выбора файлов.

-   Выбор библиотеки «Изображения» в качестве значения для параметра [**SuggestedStartLocation**](https://msdn.microsoft.com/library/windows/apps/br207854) с помощью [**PickerLocationId.PicturesLibrary**](https://msdn.microsoft.com/library/windows/apps/br207890) позволяет пользователю начать работу в расположении вероятного нахождения изображений. Присвойте свойство **SuggestedStartLocation** расположению, подходящему для типа выбранных файлов, например «Музыка», «Изображения», «Видео» или «Документы». Из начального расположения пользователь может перейти в другие расположения.

-   Использование [**FileTypeFilter**](https://msdn.microsoft.com/library/windows/apps/br207850) для указания типов файлов сохраняет внимание пользователя на выборе соответствующих файлов. Чтобы заменить предыдущие типы файлов в **FileTypeFilter** новыми записями, используйте [**ReplaceAll**](https://msdn.microsoft.com/library/windows/apps/br207844) вместо [**Add**](https://msdn.microsoft.com/library/windows/apps/br207834).

2.  **Показ FileOpenPicker**

    -   **Выбор одного файла**

```CSharp
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

    -   **To pick multiple files**

```CSharp
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

## Выбор папки: приводится полный код


```CSharp
var folderPicker = new Windows.Storage.Pickers.FolderPicker();
folderPicker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.Desktop;

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

**Подсказка.**  Каждый раз, когда ваше приложение получает доступ к файлу или папке через средство выбора, добавляйте этот элемент в свойство приложения [**FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457) или [**MostRecentlyUsedList**](https://msdn.microsoft.com/library/windows/apps/br207458) для отслеживания. Подробнее об использовании этих списков см. в разделе [Отслеживание последних использованных файлов и папок](how-to-track-recently-used-files-and-folders.md).

 

 

 







<!--HONumber=Jun16_HO4-->


