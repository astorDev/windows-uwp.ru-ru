---
ms.assetid: 12ECEA89-59D2-4BCE-B24C-5A4DD525E0C7
title: Доступ к содержимому домашней группы
description: Получайте доступ к содержимому в папке домашней группы пользователя, включая изображения, музыку и видео.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 422d02b8797bef8ea4f793ba652e9e4e34421a9b
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8745725"
---
# <a name="accessing-homegroup-content"></a>Доступ к содержимому домашней группы



**Важные API**

-   [**Класс Windows.Storage.KnownFolders**](https://msdn.microsoft.com/library/windows/apps/br227151)

Получайте доступ к содержимому в папке домашней группы пользователя, включая изображения, музыку и видео.

## <a name="prerequisites"></a>Необходимые условия

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Объявление характеристик приложений**

    Для получения доступа к содержимому домашней группы на компьютере пользователя должна быть настроена домашняя группа, а ваше приложение должно поддерживать по крайней мере одну из следующих возможностей: **picturesLibrary**, **musicLibrary** или **videosLibrary**. Получив доступ к папке домашней группы, ваше приложение будет видеть только те библиотеки, которые соответствуют характеристикам, объявленным в манифесте приложения. Дополнительную информацию см. в разделе [Разрешения на доступ к файлам](file-access-permissions.md).

    > [!NOTE]
    >  Содержимое библиотеки документов домашней группы невидимо для вашего приложения независимо от характеристик, объявленных в манифесте, и настроек общего доступа пользователя.     

-   **Использование средств выбора файлов**

    Обычно средство выбора файлов используется для доступа к файлам и папкам в домашней группе. Сведения об использовании средства выбора файлов см. в статье [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md).

-   **Запросы файлов и папок**

    Вы можете использовать запросы для перечисления файлов и папок в домашней группе. Подробные сведения о запросах файлов и папок см. в разделе [Перечисление и запрос файлов и папок](quickstart-listing-files-and-folders.md).

## <a name="open-the-file-picker-at-the-homegroup"></a>Откройте средство выбора файлов в домашней группе.

Выполните следующие действия, чтобы открыть экземпляр средства выбора файлов, который позволяет пользователю выбирать файлы и папки из домашней группы.

1.  **Создание и настройка средства выбора файлов**

    Используйте [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847), чтобы создать средство выбора файлов, и задайте для его свойства [**SuggestedStartLocation**](https://msdn.microsoft.com/library/windows/apps/br207854) значение [**PickerLocationId.HomeGroup**](https://msdn.microsoft.com/library/windows/apps/br207890). Также вы можете задать другие свойства, актуальные для ваших пользователей и приложения. Указания по настройке средства выбора файлов см. в разделе [Руководство и контрольный список для средств выбора файлов](https://msdn.microsoft.com/library/windows/apps/hh465182).

    Этот пример создает средство выбора файлов, которое открывает файлы любого типа в домашней папке и отображает их в виде эскизов:
    ```cs
    Windows.Storage.Pickers.FileOpenPicker picker = new Windows.Storage.Pickers.FileOpenPicker();
    picker.ViewMode = Windows.Storage.Pickers.PickerViewMode.Thumbnail;
    picker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.HomeGroup;
    picker.FileTypeFilter.Clear();
    picker.FileTypeFilter.Add("*");
    ```

2.  **Отобразите средство выбора файлов и обработайте выбранный файл.**

    После создания и настройки средства выбора файлов пользователь может выбрать один файл, вызвав [**FileOpenPicker.PickSingleFileAsync**](https://msdn.microsoft.com/library/windows/apps/jj635275), или несколько файлов, вызвав [**FileOpenPicker.PickMultipleFilesAsync**](https://msdn.microsoft.com/library/windows/apps/br207851).

    Этот пример отображает средство выбора файлов, чтобы позволить пользователю выбрать один файл.
    ```cs
    Windows.Storage.StorageFile file = await picker.PickSingleFileAsync();

    if (file != null)
    {
        // Do something with the file.
    }
    else
    {
        // No file returned. Handle the error.
    }   
    ```

## <a name="search-the-homegroup-for-files"></a>Поиск файлов в домашней группе

В этом разделе описано, как найти элементы в домашней группе, соответствующие термину из запроса пользователя.

1.  **Получите термин запроса от пользователя.**

    Вот термин запроса, который пользователь ввел в элемент управления [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683), называемый `searchQueryTextBox`:
    ```cs
    string queryTerm = this.searchQueryTextBox.Text;    
    ```

2.  **Задайте параметры запроса и фильтр поиска.**

    Параметры запроса определяют порядок сортировки результатов поиска, а фильтр поиска определяет, какие элементы включать в результаты поиска.

    Этот пример задает параметры запроса, которые сортируют результаты поиска по релевантности, а затем по дате изменения. Фильтр поиска — это термин запроса, который пользователь ввел на предыдущем этапе:
    ```cs
    Windows.Storage.Search.QueryOptions queryOptions =
            new Windows.Storage.Search.QueryOptions
                (Windows.Storage.Search.CommonFileQuery.OrderBySearchRank, null);
    queryOptions.UserSearchFilter = queryTerm.Text;
    Windows.Storage.Search.StorageFileQueryResult queryResults =
            Windows.Storage.KnownFolders.HomeGroup.CreateFileQueryWithOptions(queryOptions);    
    ```

3.  **Выполните запрос и обработайте результаты.**

    Следующий пример запускает запрос поиска в домашней группе и сохраняет имена всех соответствующих файлов в виде списка строк.
    ```cs
    System.Collections.Generic.IReadOnlyList<Windows.Storage.StorageFile> files =
        await queryResults.GetFilesAsync();

    if (files.Count > 0)
    {
        outputString += (files.Count == 1) ? "One file found\n" : files.Count.ToString() + " files found\n";
        foreach (Windows.Storage.StorageFile file in files)
        {
            outputString += file.Name + "\n";
        }
    }    
    ```


## <a name="search-the-homegroup-for-a-particular-users-shared-files"></a>Поиск общих файлов определенного пользователя в домашней группе

В этом разделе показано, как найти файлы домашней группы, предоставленные конкретным пользователем.

1.  **Получите коллекцию пользователей домашней группы.**

    Каждая из папок первого уровня в домашней группе представляет отдельного пользователя домашней группы. Поэтому, чтобы получить коллекцию пользователей домашней группы, вызовите [**GetFoldersAsync**](https://msdn.microsoft.com/library/windows/apps/br227279) и получите папки домашней группы верхнего уровня.
    ```cs
    System.Collections.Generic.IReadOnlyList<Windows.Storage.StorageFolder> hgFolders =
        await Windows.Storage.KnownFolders.HomeGroup.GetFoldersAsync();    
    ```

2.  **Найдите папку нужного пользователя и создайте область запроса файла для папки этого пользователя.**

    Следующий пример просматривает полученные папки, чтобы найти папку нужного пользователя. Затем он устанавливает параметры запроса, чтобы найти в папке все файлы, отсортированные сначала по релевантности, а затем по дате изменения. Пример создает строку, которая сообщает о числе найденных файлов и перечисляет их имена.
    ```cs
    bool userFound = false;
    foreach (Windows.Storage.StorageFolder folder in hgFolders)
    {
        if (folder.DisplayName == targetUserName)
        {
            // Found the target user's folder, now find all files in the folder.
            userFound = true;
            Windows.Storage.Search.QueryOptions queryOptions =
                new Windows.Storage.Search.QueryOptions
                    (Windows.Storage.Search.CommonFileQuery.OrderBySearchRank, null);
            queryOptions.UserSearchFilter = "*";
            Windows.Storage.Search.StorageFileQueryResult queryResults =
                folder.CreateFileQueryWithOptions(queryOptions);
            System.Collections.Generic.IReadOnlyList<Windows.Storage.StorageFile> files =
                await queryResults.GetFilesAsync();

            if (files.Count > 0)
            {
                string outputString = "Searched for files belonging to " + targetUserName + "'\n";
                outputString += (files.Count == 1) ? "One file found\n" : files.Count.ToString() + " files found\n";
                foreach (Windows.Storage.StorageFile file in files)
                {
                    outputString += file.Name + "\n";
                }
            }
        }
    }    
    ```

## <a name="stream-video-from-the-homegroup"></a>Потоковое видео из домашней группы

Выполните следующие шаги, чтобы воспроизводить потоковое видеосодержимое из домашней группы.

1.  **Включите MediaElement в свое приложение.**

    [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926) позволяет воспроизводить звук и видео в приложении. Дополнительные сведения о воспроизведении звука и видео см. в разделах [Создание пользовательских элементов управления транспортировкой](https://msdn.microsoft.com/library/windows/apps/mt187271) и [Звук, видео и камера](https://msdn.microsoft.com/library/windows/apps/mt203788).
    ```HTML
    <Grid x:Name="Output" HorizontalAlignment="Left" VerticalAlignment="Top" Grid.Row="1">
        <MediaElement x:Name="VideoBox" HorizontalAlignment="Left" VerticalAlignment="Top" Margin="0" Width="400" Height="300"/>
    </Grid>    
    ```

2.  **Откройте средство выбора файлов в домашней группе и примените фильтр, который включает видеофайлы в форматах, поддерживаемых вашим приложением.**

    Этот пример включает файлы MP4 и WMV в средство выбора файлов в окне открытия.
    ```cs
    Windows.Storage.Pickers.FileOpenPicker picker = new Windows.Storage.Pickers.FileOpenPicker();
    picker.ViewMode = Windows.Storage.Pickers.PickerViewMode.Thumbnail;
    picker.SuggestedStartLocation = Windows.Storage.Pickers.PickerLocationId.HomeGroup;
    picker.FileTypeFilter.Clear();
    picker.FileTypeFilter.Add(".mp4");
    picker.FileTypeFilter.Add(".wmv");
    Windows.Storage.StorageFile file = await picker.PickSingleFileAsync();   
    ```

3.  **Открыть файл Выбор пользователя для доступа на чтение и задайте поток файла в качестве источника для** [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), а затем воспроизведение файла.
    ```cs
    if (file != null)
    {
        var stream = await file.OpenAsync(Windows.Storage.FileAccessMode.Read);
        VideoBox.SetSource(stream, file.ContentType);
        VideoBox.Stop();
        VideoBox.Play();
    }
    else
    {
        // No file selected. Handle the error here.
    }    
    ```
