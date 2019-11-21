---
ms.assetid: 8BDDE64A-77D2-4F9D-A1A0-E4C634BCD890
title: Сохранение файла с помощью средства выбора
description: Чтобы дать пользователям возможность задать имя и расположение, в которое приложение должно сохранить файл, воспользуйтесь средством FileSavePicker.
ms.date: 12/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2293907b3088890ac01d9037609054961aa95992
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259579"
---
# <a name="save-a-file-with-a-picker"></a>Сохранение файла с помощью средства выбора

**Важные API**

-   [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker)
-   [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile)

Чтобы дать пользователям возможность задать имя и расположение, в которое они хотели бы, чтобы приложение сохранило файл, воспользуйтесь средством [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker).

> [!NOTE]
> Полный пример: [пример средства выбора файлов](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FilePicker).

 

## <a name="prerequisites"></a>Предварительные условия


-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)** .

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://docs.microsoft.com/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://docs.microsoft.com/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps).

-   **Права доступа к расположению**

    См. раздел [Разрешения на доступ к файлам](file-access-permissions.md).

## <a name="filesavepicker-step-by-step"></a>Пошаговое руководство по FileSavePicker

Чтобы позволить пользователям задавать имя, тип файла и расположение для сохранения файла, используйте класс [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker). Создайте, настройте и отобразите объект средства выбора файлов, а затем сохраните данные с помощью возвращенного объекта [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), представляющего выбранный файл.

1.  **Создание и настройка FileSavePicker**

    ```cs
    var savePicker = new Windows.Storage.Pickers.FileSavePicker();
    savePicker.SuggestedStartLocation =
        Windows.Storage.Pickers.PickerLocationId.DocumentsLibrary;
    // Dropdown of file types the user can save the file as
    savePicker.FileTypeChoices.Add("Plain Text", new List<string>() { ".txt" });
    // Default file name if the user does not type one in or select a file to replace
    savePicker.SuggestedFileName = "New Document";
    ```

Задайте свойства объекта средства выбора файлов, соответствующие вашим пользователям и приложению. В этом примере задаются три свойства: [**SuggestedStartLocation**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker.suggestedstartlocation), [**FileTypeChoices**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker.filetypechoices) и [**SuggestedFileName**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker.suggestedfilename).
     
- Поскольку пользователь сохраняет документ или текстовый файл, пример присваивает [**SuggestedStartLocation**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker.suggestedstartlocation) локальной папке приложения с помощью [**LocalFolder**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder). Присвойте свойство [**SuggestedStartLocation**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.suggestedstartlocation) расположению, подходящему для типа сохраняемых файлов, например «Музыка», «Изображения», «Видео» или «Документы». Из начального расположения пользователь может перейти в другие расположения.

- Чтобы убедиться, что приложение сможет открыть файл после его сохранения, мы используем [**FileTypeChoices**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker.filetypechoices) для указания типов файлов, поддерживаемых примером (документы Microsoft Word и текстовые файлы). Убедитесь, что ваше приложение поддерживает все заданные типы файлов. Пользователи смогут сохранить свой файл как один из указанных типов файлов. Кроме того, они могут изменить тип файла, выбрав другой из указанных типов файлов. Первый тип файла в списке будет выбран по умолчанию. Для управления установите свойство [**DefaultFileExtension**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker.defaultfileextension).

    > [!NOTE]
    > Средство выбора файлов также использует текущий выбранный тип файла как фильтр отображения. Таким образом отображаются только типы файлов, соответствующие выбранным.

- Чтобы избавить пользователя от лишней работы с клавиатурой, в примере задается [**SuggestedFileName**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker.suggestedfilename). Сделайте предлагаемое имя файла подходящим для сохраняемого файла. Например, можно предложить имя существующего файла, если оно есть, или первую строку документа, если сохраняемому файлу еще не присвоено имя, как это делается в Word.

> [!NOTE]
>Объекты  [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker) отображают средство выбора файлов с помощью режима просмотра [**PickerViewMode.List**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.PickerViewMode).

2.  **Отобразите FileSavePicker и сохраните выбранный файл.**

    Чтобы отобразить средство выбора файлов, вызовите [**PickSaveFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker.picksavefileasync). После того как пользователь укажет имя, тип файла и расположение и подтвердит сохранение файла, **PickSaveFileAsync** возвратит объект [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), представляющий сохраненный файл. Теперь когда вы получили право на чтение и запись в файл, вы можете захватить и обработать его.

    ```cs
    Windows.Storage.StorageFile file = await savePicker.PickSaveFileAsync();
        if (file != null)
        {
            // Prevent updates to the remote version of the file until
            // we finish making changes and call CompleteUpdatesAsync.
            Windows.Storage.CachedFileManager.DeferUpdates(file);
            // write to file
            await Windows.Storage.FileIO.WriteTextAsync(file, file.Name);
            // Let Windows know that we're finished changing the file so
            // the other app can update the remote version of the file.
            // Completing updates may require Windows to ask for user input.
            Windows.Storage.Provider.FileUpdateStatus status =
                await Windows.Storage.CachedFileManager.CompleteUpdatesAsync(file);
            if (status == Windows.Storage.Provider.FileUpdateStatus.Complete)
            {
                this.textBlock.Text = "File " + file.Name + " was saved.";
            }
            else
            {
                this.textBlock.Text = "File " + file.Name + " couldn't be saved.";
            }
        }
        else
        {
            this.textBlock.Text = "Operation cancelled.";
        }
    ```

В примере проверяется допустимость файла и записывается его собственное имя. См. также [Создание, запись и чтение файла](quickstart-reading-and-writing-files.md).

> [!TIP]
> Следует всегда проверять сохраненный файл на допустимость, прежде чем выполнять дальнейшую обработку. Затем можно сохранить содержимое в файл так, как это нужно для вашего приложения, и принять соответствующие меры, если выбранный файл окажется недопустимым.
