---
author: TylerMSFT
ms.assetid: 3A404CC0-A997-45C8-B2E8-44745539759D
title: "Разрешения на доступ к файлам"
description: "Приложения могут иметь доступ к определенным расположениям в файловой системе по умолчанию. Приложения также могут получить доступ к дополнительным расположениям через средство выбора файлов или с помощью объявления возможностей."
translationtype: Human Translation
ms.sourcegitcommit: 3de603aec1dd4d4e716acbbb3daa52a306dfa403
ms.openlocfilehash: abcd6c1747566c7f8464016fadcb5a0441652afb

---
# Разрешения на доступ к файлам

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Приложения могут иметь доступ к определенным расположениям в файловой системе по умолчанию. Приложения также могут получить доступ к дополнительным расположениям через средство выбора файлов или с помощью объявления возможностей.

## Расположения, доступные всем приложениям

Создавая новое приложение, вы по умолчанию получаете доступ к следующим расположениям в файловой системе:

-   
              **Папка установки приложения**. Папка, в которую устанавливается ваше приложение в системе пользователя.

    Существуют два основных способа получения доступа к файлам и папкам в папке установки вашего приложения.

    1.  Так вы можете получить класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), соответствующий папке установки вашего приложения:
        > [!div class="tabbedCodeSnippets"]
        ```csharp
        Windows.Storage.StorageFolder installedLocation = Windows.ApplicationModel.Package.Current.InstalledLocation;
        ```
        ```javascript
        var installDirectory = Windows.ApplicationModel.Package.current.installedLocation;
        ```

       Затем вы можете получить доступ к файлам и папкам каталога с помощью методов [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230). В примере этот класс **StorageFolder** хранится в переменной `installDirectory`. Чтобы узнать больше о работе с пакетом приложения и каталогом установки, скачайте [Пример сведений о пакете приложения](http://go.microsoft.com/fwlink/p/?linkid=231526) для Windows8.1, а затем используйте исходный код из него в приложении для Windows10.

    2.  Так вы можете загрузить файл непосредственно из каталога установки приложения с помощью URI приложения:
        > [!div class="tabbedCodeSnippets"]
        ```csharp
        using Windows.Storage;            
        StorageFile file = await StorageFile.GetFileFromApplicationUriAsync("ms-appx:///file.txt");
        ```
        ```javascript
        Windows.Storage.StorageFile.getFileFromApplicationUriAsync("ms-appx:///file.txt").done(
            function(file) {
                // Process file
            }
        );
        ```
        
        После завершения метод [**GetFileFromApplicationUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701741) возвращает класс [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), соответствующий файлу *file.txt* в папке установки приложения (`file` в примере).

        Префикс «ms-appx:///» в универсальном коде ресурса относится к папке установки приложения. Дополнительные сведения об использовании универсальных кодов ресурсов: [Добавление ссылок на содержимое с помощью URI](https://msdn.microsoft.com/library/windows/apps/hh781215).

    Кроме того, доступ к файлам в каталоге установки приложения (в отличие от иных расположений) также можно получить, используя некоторые [модели Win32 и COM для приложений универсальной платформы Windows (UWP)](https://msdn.microsoft.com/library/windows/apps/br205757) и [функции стандартной библиотеки C/C++ из Microsoft Visual Studio](http://msdn.microsoft.com/library/hh875057.aspx).

    Папка установки приложения предназначена только для чтения. Нельзя получить доступ к папке установки с помощью средства выбора файлов.

-   **Расположения данных приложения.** Папки, в которых ваше приложение может хранить данные. Эти папки (локальная, перемещаемая и временная) создаются при установке вашего приложения.

    Существуют два основных способа получения доступа к файлам и папкам из расположения данных вашего приложения.

    1.  Используйте свойства [**ApplicationData**](https://msdn.microsoft.com/library/windows/apps/br241587), чтобы получить папку данных приложения.

        Например, вы можете использовать [**ApplicationData**](https://msdn.microsoft.com/library/windows/apps/br241587).[**LocalFolder**](https://msdn.microsoft.com/library/windows/apps/br241621), чтобы получить класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), соответствующий локальной папке вашего приложения, следующим образом:
        > [!div class="tabbedCodeSnippets"]
        ```csharp
        using Windows.Storage;
        StorageFolder localFolder = ApplicationData.Current.LocalFolder;
        ```
        ```javascript
        var localFolder = Windows.Storage.ApplicationData.current.localFolder;
        ```
 
        Если вы хотите получить доступ к перемещаемой или временной папке вашего приложения, вместо этого используйте свойство [**RoamingFolder**](https://msdn.microsoft.com/library/windows/apps/br241623) или [**TemporaryFolder**](https://msdn.microsoft.com/library/windows/apps/br241629).

        Загрузив класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), соответствующий расположению данных приложения, вы получите доступ к файлам и папкам этого расположения с помощью методов **StorageFolder**. В примере эти объекты **StorageFolder** хранятся в переменной `localFolder`. Больше об использовании расположений данных приложения можно узнать из статьи [Управление данными приложения](https://msdn.microsoft.com/library/windows/apps/hh465109), а также скачав [Пример данных приложения](http://go.microsoft.com/fwlink/p/?linkid=231478) для Windows8.1 и использовав исходный код из него в приложении для Windows10.

    2.  Например, так вы можете получить файл непосредственно из локальной папки своего приложения с помощью URI приложения:
        > [!div class="tabbedCodeSnippets"]
        ```csharp
        using Windows.Storage;
        StorageFile file = await StorageFile.GetFileFromApplicationUriAsync("ms-appdata:///local/file.txt");
        ```
        ```javascript
        Windows.Storage.StorageFile.getFileFromApplicationUriAsync("ms-appdata:///local/file.txt").done(
            function(file) {
                // Process file
            }
        );
        ```

        После завершения метод [**GetFileFromApplicationUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701741) возвращает класс [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), соответствующий файлу *file.txt* в локальной папке приложения (`file` в примере).

        Префикс «ms-appdata:///local/» в универсальном коде ресурса относится к локальной папке приложения. Чтобы получить доступ к файлам в перемещаемой или временной папке приложения, используйте вместо этого «ms-appdata:///roaming/» или «ms-appdata:///temporary/». Дополнительные сведения об использовании универсальных кодов ресурсов приложения см. в разделе [Загрузка файловых ресурсов](https://msdn.microsoft.com/library/windows/apps/hh781229).

    Кроме того, доступ к файлам в каталоге установки приложения (в отличие от иных расположений) также можно получить, используя некоторые [модели Win32 и COM для приложений UWP](https://msdn.microsoft.com/library/windows/apps/br205757) и функции стандартной библиотеки C/C++ из Visual Studio.

    Нельзя получить доступ к локальной, перемещаемой и временной папкам с помощью средства выбора файлов.

-   **Съемные устройства.** Кроме того, ваше приложение по умолчанию имеет доступ к некоторым файлам на подключенных устройствах. Этот вариант подходит, если ваше приложение использует [расширение "Автозапуск"](https://msdn.microsoft.com/library/windows/apps/xaml/hh464906.aspx#autoplay), чтобы запускаться автоматически при подключении к системе устройства, например камеры или USB-устройства флэш-памяти. Доступ вашего приложения к файлам ограничен определенными типами файлов, указанными с помощью объявлений сопоставлений типов файлов в манифесте приложения.

    Конечно, вы также можете получить доступ к файлам и папкам на съемном устройстве, вызвав средство выбора файлов (с помощью [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) и [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/br207881)) и позволив пользователю выбирать файлы или папки, предоставляя приложению доступ к ним. Сведения об использовании средства выбора файлов см. в статье [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md).

    
              **Примечание.**  Подробнее о доступе к SD-карте из мобильного приложения см. в статье [Доступ к SD-карте](access-the-sd-card.md).

     

## Расположения, к которым приложения Магазина Windows могут получить доступ

-   **Папка скачиваемых файлов пользователя.** Папка, в которой по умолчанию сохраняются скачиваемые файлы.

    По умолчанию ваше приложение имеет доступ к файлам и папкам только в папке скачиваемых файлов пользователя, созданной этим приложением. Однако вы можете получить доступ к файлам и папкам в папке скачиваемых файлов пользователя, вызвав средство выбора файлов ([**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) или [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/br207881)), чтобы пользователь мог осуществлять навигацию и выбирать файлы или папки, предоставляя приложению доступ к ним.

    -   Вы можете создать файл в папке скачиваемых файлов пользователя следующим образом:
        > [!div class="tabbedCodeSnippets"]
        ```csharp
        using Windows.Storage;
        StorageFile newFile = await DownloadsFolder.CreateFileAsync("file.txt");
        ```
        ```javascript
        Windows.Storage.DownloadsFolder.createFileAsync("file.txt").done(
            function(newFile) {
                // Process file
            }
        );
        ```
 
        
              [
              **DownloadsFolder**](https://msdn.microsoft.com/library/windows/apps/br241632).[**CreateFileAsync**](https://msdn.microsoft.com/library/windows/apps/hh996761) перегружен таким образом, чтобы вы могли указать системе алгоритм действий, если в папке загружаемых файлов уже существует файл с таким же именем. После завершения эти методы возвращают класс [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), соответствующий созданному файлу. В примере этот файл назван `newFile`.

    -   Вы можете создать вложенную папку в папке скачиваемых файлов пользователя следующим образом:
        > [!div class="tabbedCodeSnippets"]
        ```csharp
        using Windows.Storage;
        StorageFolder newFolder = await DownloadsFolder.CreateFolderAsync("New Folder");
        ```
        ```javascript
        Windows.Storage.DownloadsFolder.createFolderAsync("New Folder").done(
            function(newFolder) {
                // Process folder
            }
        );
        ```
 
        
              [
              **DownloadsFolder**](https://msdn.microsoft.com/library/windows/apps/br241632).[**CreateFolderAsync**](https://msdn.microsoft.com/library/windows/apps/hh996763) перегружен таким образом, чтобы вы могли указать системе алгоритм действий, если в папке загружаемых файлов уже существует вложенная папка с таким же именем. После завершения эти методы возвращают класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), соответствующий созданной вложенной папке. В примере этот файл назван `newFolder`.

    Если вы создаете файл или папку в папке скачиваемых файлов, мы рекомендуем добавить этот элемент в [**FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457) вашего приложения, чтобы в дальнейшем оно могло быстро получать доступ к этому элементу.

## Доступ к дополнительным расположениям

Кроме расположений по умолчанию, приложение может получить доступ к дополнительным файлам и папкам с помощью объявления возможностей в манифесте приложения (см. статью [Объявления возможностей приложения](https://msdn.microsoft.com/library/windows/apps/mt270968)) или с помощью вызова средства выбора файлов, что позволяет пользователю выбирать файлы и папки, предоставляя приложению доступ к ним (см. статью [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md)).

В таблице ниже перечислены дополнительные расположения, к которым можно получить доступ, объявив возможности и используя связанные API [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/br227346).

| Местоположение | Возможность | API Windows.Storage |
|----------|------------|---------------------|
| Документы | DocumentsLibrary <br><br>Примечание. Необходимо добавить сопоставления типов файлов в манифест приложения, в котором указаны конкретные типы файлов, доступные приложению в этом расположении. <br><br>Используйте данную возможность, если ваше приложение:<br>- поддерживает кросс-платформенный автономный доступ к конкретному содержимому OneDrive, используя допустимые URL-адреса OneDrive или идентификаторы ресурсов;<br>- автоматически сохраняет открытые файлы в OneDrive пользователя при работе в автономном режиме | [KnownFolders.DocumentsLibrary](https://msdn.microsoft.com/library/windows/apps/br227152) |
| Музыка     | MusicLibrary <br>См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.MusicLibrary](https://msdn.microsoft.com/library/windows/apps/br227155) |    
| Изображения  | PicturesLibrary<br> См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.PicturesLibrary](https://msdn.microsoft.com/library/windows/apps/br227156) |  
| Видео    | VideosLibrary<br>См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.VideosLibrary](https://msdn.microsoft.com/library/windows/apps/br227159) |   
| Съемные устройства  | RemovableDevices <br><br>Примечание. Необходимо добавить сопоставления типов файлов в манифест приложения, в котором указаны конкретные типы файлов, доступные приложению в этом расположении. <br><br>См. также статью [Получение доступа к SD-карте](access-the-sd-card.md) | [KnownFolders.RemovableDevices](https://msdn.microsoft.com/library/windows/apps/br227158) |  
| Библиотеки домашней группы  | Требуется по меньшей мере одна из следующих возможностей: <br>- MusicLibrary, <br>- PicturesLibrary, <br>- VideosLibrary | [KnownFolders.HomeGroup](https://msdn.microsoft.com/library/windows/apps/br227153) |      
| Устройства сервера мультимедиа (DLNA) | Требуется по меньшей мере одна из следующих возможностей: <br>- MusicLibrary, <br>- PicturesLibrary, <br>- VideosLibrary | [KnownFolders.MediaServerDevices](https://msdn.microsoft.com/library/windows/apps/br227154) | 
| Папки UNC | Требуется комбинация следующих возможностей: <br><br>Возможность домашней и рабочей сетей: <br>- PrivateNetworkClientServer. <br><br>Добавьте по меньшей мере одну возможность общедоступных сетей и Интернета: <br>- InternetClient, <br>- InternetClientServer. <br><br>Если применимо, добавьте возможность учетных данных домена:<br>- EnterpriseAuthentication. <br><br>Примечание. Необходимо добавить сопоставления типов файлов в манифест приложения, в котором указаны конкретные типы файлов, доступные приложению в этом расположении | Получение папки с помощью: <br>[StorageFolder.GetFolderFromPathAsync](https://msdn.microsoft.com/library/windows/apps/br227278) <br><br>Получение файла с помощью: <br>[StorageFile.GetFileFromPathAsync](https://msdn.microsoft.com/library/windows/apps/br227206) |




<!--HONumber=Jul16_HO2-->


