---
author: TylerMSFT
ms.assetid: 8BDDE64A-77D2-4F9D-A1A0-E4C634BCD890
title: "Сохранение файла с помощью средства выбора"
description: "Чтобы дать пользователям возможность задать имя и расположение, в которое приложение должно сохранить файл, воспользуйтесь средством FileSavePicker."
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 68c7fcedbea40541632aa0a7ae65aa77db206280

---

# Сохранение файла с помощью средства выбора


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871)
-   [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171)

Чтобы дать пользователям возможность задать имя и расположение, в которое они хотели бы, чтобы приложение сохранило файл, воспользуйтесь средством [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871).

> **Примечание.** См. также [пример средства выбора файлов](http://go.microsoft.com/fwlink/p/?linkid=619994).

 

## Необходимые условия


-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Права на доступ к расположению**

    См. раздел [Разрешения на доступ к файлам](file-access-permissions.md).

## Пошаговое руководство по FileSavePicker


Чтобы позволить пользователям задавать имя, тип файла и расположение для сохранения файла, используйте класс [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871). Создайте, настройте и отобразите объект средства выбора файлов, а затем сохраните данные с помощью возвращенного объекта [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), представляющего выбранный файл.

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

Задайте свойства объекта средства выбора файлов, соответствующие вашим пользователям и приложению. Указания по настройке средства выбора файлов см. в разделе [Руководство и контрольный список для средств выбора файлов](https://msdn.microsoft.com/library/windows/apps/hh465182).

В этом примере устанавливаются три свойства: [**SuggestedStartLocation**](https://msdn.microsoft.com/library/windows/apps/br207880), [**FileTypeChoices**](https://msdn.microsoft.com/library/windows/apps/br207875) и [**SuggestedFileName**](https://msdn.microsoft.com/library/windows/apps/br207878).

> **Примечание**.
            [
              **Объекты FileSavePicker**
            ](https://msdn.microsoft.com/library/windows/apps/br207871) отображают средство выбора файлов с помощью [**PickerViewMode.List**](https://msdn.microsoft.com/library/windows/apps/br207891).

     
- Поскольку пользователь сохраняет документ или текстовый файл, пример присваивает [**SuggestedStartLocation**](https://msdn.microsoft.com/library/windows/apps/br207880) локальной папке приложения с помощью [**LocalFolder**](https://msdn.microsoft.com/library/windows/apps/br241621). Присвойте свойство [**SuggestedStartLocation**](https://msdn.microsoft.com/library/windows/apps/br207854) расположению, подходящему для типа сохраняемых файлов, например «Музыка», «Изображения», «Видео» или «Документы». Из начального расположения пользователь может перейти в другие расположения.
 
- Чтобы убедиться, что приложение сможет открыть файл после его сохранения, мы используем [**FileTypeChoices**](https://msdn.microsoft.com/library/windows/apps/br207875) для указания типов файлов, поддерживаемых примером (документы Microsoft Word и текстовые файлы). Убедитесь, что ваше приложение поддерживает все заданные типы файлов. Пользователи смогут сохранить свой файл как один из указанных типов файлов. Кроме того, они могут изменить тип файла, выбрав другой из указанных типов файлов. Первый тип файла в списке будет выбран по умолчанию. Для управления установите свойство [**DefaultFileExtension**](https://msdn.microsoft.com/library/windows/apps/br207873).

> **Примечание.** Средство выбора файлов также использует текущий выбранный тип файла как фильтр отображения. Таким образом отображаются только типы файлов, соответствующие выбранным.

- Чтобы избавить пользователя от лишней работы с клавиатурой, в примере задается [**SuggestedFileName**](https://msdn.microsoft.com/library/windows/apps/br207878). Сделайте предлагаемое имя файла подходящим для сохраняемого файла. Например, можно предложить имя существующего файла, если оно есть, или первую строку документа, если сохраняемому файлу еще не присвоено имя, как это делается в Word.

2.  **Отобразите FileSavePicker и сохраните выбранный файл.**

    Чтобы отобразить средство выбора файлов, вызовите [**PickSaveFileAsync**](https://msdn.microsoft.com/library/windows/apps/br207876). После того как пользователь укажет имя, тип файла и расположение и подтвердит сохранение файла, **PickSaveFileAsync** возвратит объект [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), представляющий сохраненный файл. Теперь когда вы получили право на чтение и запись в файл, вы можете захватить и обработать его.

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

**Подсказка.** Следует всегда проверять сохраненный файл на допустимость, прежде чем выполнять дальнейшую обработку. Затем можно сохранить содержимое в файл так, как это нужно для вашего приложения, и принять соответствующие меры, если выбранный файл окажется недопустимым.

     

 

 







<!--HONumber=Jun16_HO4-->


