---
ms.assetid: 3A404CC0-A997-45C8-B2E8-44745539759D
title: Разрешения на доступ к файлам
description: Приложения могут иметь доступ к определенным расположениям в файловой системе по умолчанию. Приложения также могут получить доступ к дополнительным расположениям через средство выбора файлов или с помощью объявления возможностей.
ms.date: 12/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
- javascript
ms.openlocfilehash: 1473d93bc10f50bf361f92f753adb786e502fc3a
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66369436"
---
# <a name="file-access-permissions"></a>Разрешения на доступ к файлам

Универсальных приложений для платформы Windows (UWP) можно получить доступ к определенным файловой системы по умолчанию. Приложения также могут получить доступ к дополнительным расположениям через средство выбора файлов или с помощью объявления возможностей.

## <a name="the-locations-that-all-apps-can-access"></a>Расположения, доступные всем приложениям

Создавая новое приложение, вы по умолчанию получаете доступ к следующим расположениям в файловой системе:

### <a name="application-install-directory"></a>Папку установки приложения
Папку для установки приложения в системе пользователя.

Существует два основных способа для доступа к файлам и папкам в вашем приложении каталог установки:

1. Так вы можете получить класс [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder), соответствующий папке установки вашего приложения:

    ```csharp
    Windows.Storage.StorageFolder installedLocation = Windows.ApplicationModel.Package.Current.InstalledLocation;
    ```
    
    ```javascript
    var installDirectory = Windows.ApplicationModel.Package.current.installedLocation;
    ```
    
    ```cppwinrt
    #include <winrt/Windows.Storage.h>
    ...
    Windows::Storage::StorageFolder installedLocation{ Windows::ApplicationModel::Package::Current().InstalledLocation() };
    ```
    
    ```cpp
    Windows::Storage::StorageFolder^ installedLocation = Windows::ApplicationModel::Package::Current->InstalledLocation;
    ```

    Затем вы можете получить доступ к файлам и папкам каталога с помощью методов [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder). В примере этот класс **StorageFolder** хранится в переменной `installDirectory`. Вы узнаете подробности о работе с пакетом приложения и папкой установки из статьи [Образец сведений о пакете приложения](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Package) на GitHub.

2. Так вы можете загрузить файл непосредственно из каталога установки приложения с помощью URI приложения:

    ```csharp
    using Windows.Storage;            
    StorageFile file = await StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appx:///file.txt"));
    ```
    
    ```javascript
    Windows.Storage.StorageFile.getFileFromApplicationUriAsync("ms-appx:///file.txt").done(
        function(file) {
            // Process file
        }
    );
    ```
    
    ```cppwinrt
    Windows::Foundation::IAsyncAction ExampleCoroutineAsync()
    {
        Windows::Storage::StorageFile file{
            co_await Windows::Storage::StorageFile::GetFileFromApplicationUriAsync(Windows::Foundation::Uri{L"ms-appx:///file.txt"})
        };
        // Process file
    }
    ```
    
    ```cpp
    auto getFileTask = create_task(StorageFile::GetFileFromApplicationUriAsync(ref new Uri("ms-appx:///file.txt")));
    getFileTask.then([](StorageFile^ file) 
    {
        // Process file
    });
    ```

    После завершения метод [**GetFileFromApplicationUriAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getfilefromapplicationuriasync) возвращает класс [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), соответствующий файлу `file.txt` в папке установки приложения (`file` в примере).
    
    Префикс «ms-appx:///» в универсальном коде ресурса относится к папке установки приложения. Дополнительные сведения об использовании универсальных кодов ресурсов: [Добавление ссылок на содержимое с помощью URI](https://docs.microsoft.com/previous-versions/windows/apps/hh781215(v=win.10)).

Кроме того, доступ к файлам в каталоге установки приложения (в отличие от иных расположений) также можно получить, используя некоторые [модели Win32 и COM для приложений универсальной платформы Windows (UWP)](https://docs.microsoft.com/uwp/win32-and-com/win32-and-com-for-uwp-apps) и [функции стандартной библиотеки C/C++ из Microsoft Visual Studio](https://docs.microsoft.com/cpp/cpp/c-cpp-language-and-standard-libraries).

Папка установки приложения предназначена только для чтения. Не удается получить доступ к каталогу установки через средство выбора файлов.

### <a name="application-data-locations"></a>Расположения данных приложения
Папки, в которых ваше приложение может хранить данные. Эти папки (локальная, перемещаемая и временная) создаются при установке вашего приложения.

Существует два основных способа для доступа к файлам и папкам из расположений данных вашего приложения:

1.  Используйте свойства [**ApplicationData**](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationData), чтобы получить папку данных приложения.

    Например, вы можете использовать [**ApplicationData**](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationData).[**LocalFolder**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.localfolder), чтобы получить класс [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder), соответствующий локальной папке вашего приложения, следующим образом:
    
    ```csharp
    using Windows.Storage;
    StorageFolder localFolder = ApplicationData.Current.LocalFolder;
    ```
    
    ```javascript
    var localFolder = Windows.Storage.ApplicationData.current.localFolder;
    ```
    
    ```cppwinrt
    Windows::Storage::StorageFolder storageFolder{
        Windows::Storage::ApplicationData::Current().LocalFolder()
    };
    ```
    
    ```cpp
    using namespace Windows::Storage;
    StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
    ```
    
    Если вы хотите получить доступ к перемещаемой или временной папке вашего приложения, вместо этого используйте свойство [**RoamingFolder**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingfolder) или [**TemporaryFolder**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.temporaryfolder).
    
    Загрузив класс [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder), соответствующий расположению данных приложения, вы получите доступ к файлам и папкам этого расположения с помощью методов **StorageFolder**. В примере эти объекты **StorageFolder** хранятся в переменной `localFolder`. Больше об использовании расположений данных приложений можно узнать в рекомендации на странице [Класс ApplicationData](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata), а также скачав [Пример данных приложения](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ApplicationData) с GitHub.

2. Файл можно получить непосредственно из локальной папке приложения с помощью URI, приложения следующим образом:
    
    ```csharp
    using Windows.Storage;
    StorageFile file = await StorageFile.GetFileFromApplicationUriAsync(new Uri("ms-appdata:///local/file.txt"));
    ```
    
    ```javascript
    Windows.Storage.StorageFile.getFileFromApplicationUriAsync("ms-appdata:///local/file.txt").done(
        function(file) {
            // Process file
        }
    );
    ```
    
    ```cppwinrt
    Windows::Storage::StorageFile file{
        co_await Windows::Storage::StorageFile::GetFileFromApplicationUriAsync(Windows::Foundation::Uri{ L"ms-appdata:///local/file.txt" })
    };
    // Process file
    ```
    
    ```cpp
    using Windows::Storage;
    auto getFileTask = create_task(StorageFile::GetFileFromApplicationUriAsync(ref new Uri("ms-appdata:///local/file.txt")));
    getFileTask.then([](StorageFile^ file) 
    {
        // Process file
    });
    ```
    
    После завершения метод [**GetFileFromApplicationUriAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getfilefromapplicationuriasync) возвращает класс [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), соответствующий файлу `file.txt` в локальной папке приложения (`file` в примере).
    
    Префикс «ms-appdata:///local/» в универсальном коде ресурса относится к локальной папке приложения. Чтобы получить доступ к файлам в перемещаемой или временной папке приложения, используйте вместо этого «ms-appdata:///roaming/» или «ms-appdata:///temporary/». Дополнительные сведения об использовании универсальных кодов ресурсов приложения см. в разделе [Загрузка файловых ресурсов](https://docs.microsoft.com/previous-versions/windows/apps/hh781229(v=win.10)).

Кроме того, доступ к файлам в каталоге установки приложения (в отличие от иных расположений) также можно получить, используя некоторые [модели Win32 и COM для приложений UWP](https://docs.microsoft.com/uwp/win32-and-com/win32-and-com-for-uwp-apps) и функции стандартной библиотеки C/C++ из Visual Studio.

Local, перемещаемый или временной папки недоступны через средство выбора файлов.

### <a name="removable-devices"></a>Съемные устройства
Кроме того, ваше приложение по умолчанию имеет доступ к некоторым файлам на подключенных устройствах. Этот вариант подходит, если ваше приложение использует [расширение "Автозапуск"](https://docs.microsoft.com/previous-versions/windows/apps/hh464906(v=win.10)), чтобы запускаться автоматически при подключении к системе устройства, например камеры или USB-устройства флэш-памяти. Доступ вашего приложения к файлам ограничен определенными типами файлов, указанными с помощью объявлений сопоставлений типов файлов в манифесте приложения.

Конечно, вы также можете получить доступ к файлам и папкам на съемном устройстве, вызвав средство выбора файлов (с помощью [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) и [**FolderPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FolderPicker)) и позволив пользователю выбирать файлы или папки, предоставляя приложению доступ к ним. Сведения об использовании средства выбора файлов см. в статье [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md).

> [!NOTE]
> Подробнее о доступе к SD-карте или другим съемным устройствам см. в статье [Доступ к SD-карте](access-the-sd-card.md).

## <a name="locations-that-uwp-apps-can-access"></a>Расположения, которые можно получить доступ к приложений универсальной платформы Windows
### <a name="users-downloads-folder"></a>Файлы для загрузки папки

Папка, в которой по умолчанию сохраняются скачиваемые файлы.

По умолчанию ваше приложение имеет доступ к файлам и папкам только в папке скачиваемых файлов пользователя, созданной этим приложением. Однако вы можете получить доступ к файлам и папкам в папке скачиваемых файлов пользователя, вызвав средство выбора файлов ([**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) или [**FolderPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FolderPicker)), чтобы пользователь мог осуществлять навигацию и выбирать файлы или папки, предоставляя приложению доступ к ним.

- Вы можете создать файл в папке скачиваемых файлов пользователя следующим образом:

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
    
    ```cppwinrt
    Windows::Storage::StorageFile newFile{
        co_await Windows::Storage::DownloadsFolder::CreateFileAsync(L"file.txt")
    };
    // Process file
    ```
    
    ```cpp
    using Windows::Storage;
    auto createFileTask = create_task(DownloadsFolder::CreateFileAsync(L"file.txt"));
    createFileTask.then([](StorageFile^ newFile)
    {
        // Process file
    });
    ```

    [**DownloadsFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.DownloadsFolder).[ **CreateFileAsync** ](https://docs.microsoft.com/uwp/api/windows.storage.downloadsfolder.createfileasync) перегружен, можно указать, что должна делать система Если уже существующий файл в папке «загрузки», который имеет то же имя. После завершения эти методы возвращают класс [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), соответствующий созданному файлу. В примере этот файл назван `newFile`.

- Вы можете создать вложенную папку в папке скачиваемых файлов пользователя следующим образом:

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
    
    ```cppwinrt
    Windows::Storage::StorageFolder newFolder{
        co_await Windows::Storage::DownloadsFolder::CreateFolderAsync(L"New Folder")
    };
    // Process folder
    ```
    
    ```cpp
    using Windows::Storage;
    auto createFolderTask = create_task(DownloadsFolder::CreateFolderAsync(L"New Folder"));
    createFolderTask.then([](StorageFolder^ newFolder)
    {
        // Process folder
    });
    ```

    [**DownloadsFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.DownloadsFolder).[ **CreateFolderAsync** ](https://docs.microsoft.com/uwp/api/windows.storage.downloadsfolder.createfolderasync) перегружен, можно указать, что должна делать система Если уже существующий во вложенную папку в папке «загрузки», который имеет то же имя. После завершения эти методы возвращают класс [**StorageFolder**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFolder), соответствующий созданной вложенной папке. В примере этот файл назван `newFolder`.

Если вы создаете файл или папку в папке скачиваемых файлов, мы рекомендуем добавить этот элемент в [**FutureAccessList**](https://docs.microsoft.com/uwp/api/windows.storage.accesscache.storageapplicationpermissions.futureaccesslist) вашего приложения, чтобы в дальнейшем оно могло быстро получать доступ к этому элементу.

## <a name="accessing-additional-locations"></a>Доступ к дополнительным расположениям

Кроме расположений по умолчанию, приложение может получить доступ к дополнительным файлам и папкам с помощью объявления возможностей в манифесте приложения (см. статью [Объявления возможностей приложения](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations)) или с помощью вызова средства выбора файлов, что позволяет пользователю выбирать файлы и папки, предоставляя приложению доступ к ним (см. статью [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md)).

Приложения, объявляющие расширение [AppExecutionAlias](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap5-appexecutionalias), имеют разрешения файловой системы начиная с каталога, откуда они запускаются в консольном окне, и далее вниз.

В таблице ниже перечислены дополнительные расположения, к которым можно получить доступ, объявив возможности и используя связанные API [**Windows.Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage).

| Location | Возможности | API Windows.Storage |
|----------|------------|---------------------|
| Все файлы, к которым у пользователя имеется доступ. Например: документы, изображения, фотографии, файлы для загрузки, рабочий стол, OneDrive и т. д. | broadFileSystemAccess<br><br>Это ограниченная возможность. Доступ может настраиваться в **параметры** > **конфиденциальности** > **файловая система**. Так как пользователи могут предоставить или запретить разрешение любое время в **параметры**, следует убедиться, что приложение устойчива к этим изменениям. Если обнаружится, что приложение не имеет доступа, вы можете предложить пользователю изменять этот параметр, ссылку на [доступ к файловой системе Windows 10 и конфиденциальности](https://privacy.microsoft.com/en-US/windows-10-file-system-access-and-privacy) статьи. Обратите внимание на то, что пользователь должен закрыть приложение, переключите параметр и перезапустить приложение. Если они переключите параметр во время работы приложения, платформа будет приостановить работу приложения, таким образом, можно сохранить состояние, а затем принудительно завершить приложение, чтобы применить новые параметры. В обновление за апрель 2018 года по умолчанию для разрешения включено. В обновлении октября 2018 по умолчанию — Off.<br /><br />Если приложение отправляется в магазин Store, объявляющий эту возможность, потребуется дополнительно обосновать, зачем приложению нужна эта возможность и как планируется ее использовать.<br>Эта возможность работает для API-интерфейсов в [ **Windows.Storage** ](https://docs.microsoft.com/uwp/api/Windows.Storage) пространства имен. См. в разделе **пример** в конце этой статьи, пример того, как включить эту возможность в приложении. | Неприменимо |
| Документы | DocumentsLibrary <br><br>Примечание. Сопоставления типов файлов необходимо добавить в манифест приложения, объявите определенные типы файлов, которые приложение может обращаться к в этом расположении. <br><br>Используйте данную возможность, если ваше приложение:<br>- поддерживает кросс-платформенный автономный доступ к конкретному содержимому OneDrive, используя допустимые URL-адреса OneDrive или идентификаторы ресурсов;<br>-Сохранение открытых файлов в OneDrive пользователя автоматически во время вне сети | [KnownFolders.DocumentsLibrary](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.documentslibrary) |
| Музыка     | MusicLibrary <br>См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.MusicLibrary](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.musiclibrary) |    
| Изображения  | PicturesLibrary<br> См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.PicturesLibrary](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.pictureslibrary) |  
| Видео    | VideosLibrary<br>См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.VideosLibrary](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.videoslibrary) |   
| Съемные устройства  | RemovableDevices <br><br>Примечание. Необходимо добавить сопоставления типов файлов в манифест приложения, в котором указаны конкретные типы файлов, доступные приложению в этом расположении. <br><br>См. также статью [Получение доступа к SD-карте](access-the-sd-card.md) | [KnownFolders.RemovableDevices](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.removabledevices) |  
| Библиотеки домашней группы  | Требуется по меньшей мере одна из следующих возможностей: <br>- MusicLibrary, <br>- PicturesLibrary, <br>- VideosLibrary | [KnownFolders.HomeGroup](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.homegroup) |      
| Устройства сервера мультимедиа (DLNA) | Требуется по меньшей мере одна из следующих возможностей: <br>- MusicLibrary, <br>- PicturesLibrary, <br>- VideosLibrary | [KnownFolders.MediaServerDevices](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders.mediaserverdevices) |
| Папки UNC | Требуется комбинация следующих возможностей: <br><br>Возможность домашней и рабочей сетей: <br>- PrivateNetworkClientServer. <br><br>Добавьте по меньшей мере одну возможность общедоступных сетей и Интернета: <br>- InternetClient, <br>- InternetClientServer. <br><br>Если применимо, добавьте возможность учетных данных домена:<br>- EnterpriseAuthentication. <br><br>Примечание. Сопоставления типов файлов необходимо добавить в манифест приложения, объявите определенные типы файлов, которые приложение может обращаться к в этом расположении. | Получение папки с помощью: <br>[StorageFolder.GetFolderFromPathAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfolderfrompathasync) <br><br>Получение файла с помощью: <br>[StorageFile.GetFileFromPathAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getfilefrompathasync) |

**Пример**

Этот пример добавляет ограниченную возможность `broadFileSystemAccess`. Нужно не только указать возможность, но и добавить пространство имен `rescap`, а также добавить его к `IgnorableNamespaces`:

```xaml
<Package
  ...
  xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
  IgnorableNamespaces="uap mp uap5 rescap">
...
<Capabilities>
    <rescap:Capability Name="broadFileSystemAccess" />
</Capabilities>
```

> [!NOTE]
> Полный список возможностей приложения см. в разделе [Объявления о возможностях приложения](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).
