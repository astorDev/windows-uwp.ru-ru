---
author: laurenhughes
ms.assetid: 3A404CC0-A997-45C8-B2E8-44745539759D
title: Разрешения на доступ к файлам
description: Приложения могут иметь доступ к определенным расположениям в файловой системе по умолчанию. Приложения также могут получить доступ к дополнительным расположениям через средство выбора файлов или с помощью объявления возможностей.
ms.author: lahugh
ms.date: 06/28/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f8699ee06da545e3b34711f496a887fd7aa2c935
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7437812"
---
# <a name="file-access-permissions"></a>Разрешения на доступ к файлам

Универсальные приложения для Windows (далее "Приложения") могут иметь доступ к определенным расположениям в файловой системе по умолчанию. Приложения также могут получить доступ к дополнительным расположениям через средство выбора файлов или с помощью объявления возможностей.

## <a name="the-locations-that-all-apps-can-access"></a>Расположения, доступные всем приложениям

Создавая новое приложение, вы по умолчанию получаете доступ к следующим расположениям в файловой системе:

### <a name="application-install-directory"></a>Каталог установки приложения
Папка, где установлено ваше приложение в системе пользователя.

Существует два основных способа получения доступа к файлам и папкам в вашем приложении папке установки:

1. Так вы можете получить класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), соответствующий папке установки вашего приложения:

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

Затем вы можете получить доступ к файлам и папкам каталога с помощью методов [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230). В примере этот класс **StorageFolder** хранится в переменной `installDirectory`. Вы узнаете подробности о работе с пакетом приложения и папкой установки из статьи [Образец сведений о пакете приложения](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Package) на GitHub.

2. Так вы можете загрузить файл непосредственно из каталога установки приложения с помощью URI приложения:

```cs
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

После завершения метод [**GetFileFromApplicationUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701741) возвращает класс [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), соответствующий файлу `file.txt` в папке установки приложения (`file` в примере).

Префикс «ms-appx:///» в универсальном коде ресурса относится к папке установки приложения. Дополнительные сведения об использовании универсальных кодов ресурсов: [Добавление ссылок на содержимое с помощью URI](https://msdn.microsoft.com/library/windows/apps/hh781215).

Кроме того, доступ к файлам в каталоге установки приложения (в отличие от иных расположений) также можно получить, используя некоторые [модели Win32 и COM для приложений универсальной платформы Windows (UWP)](https://msdn.microsoft.com/library/windows/apps/br205757) и [функции стандартной библиотеки C/C++ из Microsoft Visual Studio](http://msdn.microsoft.com/library/hh875057.aspx).

Папка установки приложения предназначена только для чтения. Не удается получить доступ к папке установки через средство выбора файлов.

### <a name="application-data-locations"></a>Расположения данных приложения
Папки, в которых ваше приложение может хранить данные. Эти папки (локальная, перемещаемая и временная) создаются при установке вашего приложения.

Существует два основных способа получения доступа к файлам и папкам из расположения данных вашего приложения:

1.  Используйте свойства [**ApplicationData**](https://msdn.microsoft.com/library/windows/apps/br241587), чтобы получить папку данных приложения.

Например, вы можете использовать [**ApplicationData**](https://msdn.microsoft.com/library/windows/apps/br241587).[**LocalFolder**](https://msdn.microsoft.com/library/windows/apps/br241621), чтобы получить класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), соответствующий локальной папке вашего приложения, следующим образом:

```cs
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

Если вы хотите получить доступ к перемещаемой или временной папке вашего приложения, вместо этого используйте свойство [**RoamingFolder**](https://msdn.microsoft.com/library/windows/apps/br241623) или [**TemporaryFolder**](https://msdn.microsoft.com/library/windows/apps/br241629).

Загрузив класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), соответствующий расположению данных приложения, вы получите доступ к файлам и папкам этого расположения с помощью методов **StorageFolder**. В примере эти объекты **StorageFolder** хранятся в переменной `localFolder`. Больше об использовании расположений данных приложений можно узнать в рекомендации на странице [Класс ApplicationData](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata), а также скачав [Пример данных приложения](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ApplicationData) с GitHub.

2. Например, так вы можете получить файл непосредственно из локальной папки своего приложения с помощью URI приложения:

```cs
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

После завершения метод [**GetFileFromApplicationUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701741) возвращает класс [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), соответствующий файлу `file.txt` в локальной папке приложения (`file` в примере).

Префикс «ms-appdata:///local/» в универсальном коде ресурса относится к локальной папке приложения. Чтобы получить доступ к файлам в перемещаемой или временной папке приложения, используйте вместо этого «ms-appdata:///roaming/» или «ms-appdata:///temporary/». Дополнительные сведения об использовании универсальных кодов ресурсов приложения см. в разделе [Загрузка файловых ресурсов](https://msdn.microsoft.com/library/windows/apps/hh781229).

Кроме того, доступ к файлам в каталоге установки приложения (в отличие от иных расположений) также можно получить, используя некоторые [модели Win32 и COM для приложений UWP](https://msdn.microsoft.com/library/windows/apps/br205757) и функции стандартной библиотеки C/C++ из Visual Studio.

Не удается получить доступ к локальной, перемещаемой и временной папки через средство выбора файлов.

### <a name="removable-devices"></a>Съемные устройства
Кроме того, ваше приложение по умолчанию имеет доступ к некоторым файлам на подключенных устройствах. Этот вариант подходит, если ваше приложение использует [расширение "Автозапуск"](https://msdn.microsoft.com/library/windows/apps/xaml/hh464906.aspx#autoplay), чтобы запускаться автоматически при подключении к системе устройства, например камеры или USB-устройства флэш-памяти. Доступ вашего приложения к файлам ограничен определенными типами файлов, указанными с помощью объявлений сопоставлений типов файлов в манифесте приложения.

Конечно, вы также можете получить доступ к файлам и папкам на съемном устройстве, вызвав средство выбора файлов (с помощью [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) и [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/br207881)) и позволив пользователю выбирать файлы или папки, предоставляя приложению доступ к ним. Сведения об использовании средства выбора файлов см. в статье [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md).

> [!NOTE]
> Подробнее о доступе к SD-карте или другим съемным устройствам см. в статье [Доступ к SD-карте](access-the-sd-card.md).

## <a name="locations-that-uwp-apps-can-access"></a>Расположений, к которым можно получить доступ к приложениям UWP
### <a name="users-downloads-folder"></a>Папке скачиваемых файлов пользователя

Папка, в которой по умолчанию сохраняются скачиваемые файлы.

По умолчанию ваше приложение имеет доступ к файлам и папкам только в папке скачиваемых файлов пользователя, созданной этим приложением. Однако вы можете получить доступ к файлам и папкам в папке скачиваемых файлов пользователя, вызвав средство выбора файлов ([**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) или [**FolderPicker**](https://msdn.microsoft.com/library/windows/apps/br207881)), чтобы пользователь мог осуществлять навигацию и выбирать файлы или папки, предоставляя приложению доступ к ним.

- Вы можете создать файл в папке скачиваемых файлов пользователя следующим образом:

```cs
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

[**DownloadsFolder**](https://msdn.microsoft.com/library/windows/apps/br241632).[**CreateFileAsync**](https://msdn.microsoft.com/library/windows/apps/hh996761) перегружен таким образом, чтобы вы могли указать системе алгоритм действий, если в папке загружаемых файлов уже существует файл с таким же именем. После завершения эти методы возвращают класс [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), соответствующий созданному файлу. В примере этот файл назван `newFile`.

- Вы можете создать вложенную папку в папке скачиваемых файлов пользователя следующим образом:

```cs
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

[**DownloadsFolder**](https://msdn.microsoft.com/library/windows/apps/br241632).[**CreateFolderAsync**](https://msdn.microsoft.com/library/windows/apps/hh996763) перегружен таким образом, чтобы вы могли указать системе алгоритм действий, если в папке загружаемых файлов уже существует вложенная папка с таким же именем. После завершения эти методы возвращают класс [**StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230), соответствующий созданной вложенной папке. В примере этот файл назван `newFolder`.

Если вы создаете файл или папку в папке скачиваемых файлов, мы рекомендуем добавить этот элемент в [**FutureAccessList**](https://msdn.microsoft.com/library/windows/apps/br207457) вашего приложения, чтобы в дальнейшем оно могло быстро получать доступ к этому элементу.

## <a name="accessing-additional-locations"></a>Доступ к дополнительным расположениям

Кроме расположений по умолчанию, приложение может получить доступ к дополнительным файлам и папкам с помощью объявления возможностей в манифесте приложения (см. статью [Объявления возможностей приложения](https://msdn.microsoft.com/library/windows/apps/mt270968)) или с помощью вызова средства выбора файлов, что позволяет пользователю выбирать файлы и папки, предоставляя приложению доступ к ним (см. статью [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md)).

Приложения, объявляющие расширение [AppExecutionAlias](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap5-appexecutionalias), имеют разрешения файловой системы начиная с каталога, откуда они запускаются в консольном окне, и далее вниз.

В таблице ниже перечислены дополнительные расположения, к которым можно получить доступ, объявив возможности и используя связанные API [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/br227346).

| Местоположение | Возможность | API Windows.Storage |
|----------|------------|---------------------|
| Все файлы, к которым у пользователя имеется доступ. Например: документы, изображения, фотографии, файлы для загрузки, рабочий стол, OneDrive и т. д. | broadFileSystemAccess<br><br>Это ограниченная возможность. При первом использовании система попросит пользователя предоставить доступ. Доступ настраивается в разделе "Параметры" > "Конфиденциальность" > "Файловая система". Если приложение отправляется в магазин Store, объявляющий эту возможность, потребуется дополнительно обосновать, зачем приложению нужна эта возможность и как планируется ее использовать.<br>Эта возможность работает для API-интерфейсов в пространстве [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/BR227346) | Неприменимо |
| Документы | DocumentsLibrary <br><br>Примечание. Необходимо добавить сопоставления типов файлов в манифест приложения, в котором указаны конкретные типы файлов, доступные приложению в этом расположении. <br><br>Используйте данную возможность, если ваше приложение:<br>- поддерживает кросс-платформенный автономный доступ к конкретному содержимому OneDrive, используя допустимые URL-адреса OneDrive или идентификаторы ресурсов;<br>-Сохраняет открытые файлы в OneDrive пользователя автоматически при автономном режиме | [KnownFolders.DocumentsLibrary](https://msdn.microsoft.com/library/windows/apps/br227152) |
| Музыка     | MusicLibrary <br>См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.MusicLibrary](https://msdn.microsoft.com/library/windows/apps/br227155) |    
| Изображения  | PicturesLibrary<br> См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.PicturesLibrary](https://msdn.microsoft.com/library/windows/apps/br227156) |  
| Видео    | VideosLibrary<br>См. также статью [Файлы и папки в библиотеках музыки, изображений и видео](quickstart-managing-folders-in-the-music-pictures-and-videos-libraries.md) | [KnownFolders.VideosLibrary](https://msdn.microsoft.com/library/windows/apps/br227159) |   
| Съемные устройства  | RemovableDevices <br><br>Примечание. Необходимо добавить сопоставления типов файлов в манифест приложения, в котором указаны конкретные типы файлов, доступные приложению в этом расположении. <br><br>См. также статью [Получение доступа к SD-карте](access-the-sd-card.md) | [KnownFolders.RemovableDevices](https://msdn.microsoft.com/library/windows/apps/br227158) |  
| Библиотеки домашней группы  | Требуется по меньшей мере одна из следующих возможностей: <br>- MusicLibrary, <br>- PicturesLibrary, <br>- VideosLibrary | [KnownFolders.HomeGroup](https://msdn.microsoft.com/library/windows/apps/br227153) |      
| Устройства сервера мультимедиа (DLNA) | Требуется по меньшей мере одна из следующих возможностей: <br>- MusicLibrary, <br>- PicturesLibrary, <br>- VideosLibrary | [KnownFolders.MediaServerDevices](https://msdn.microsoft.com/library/windows/apps/br227154) |
| Папки UNC | Требуется комбинация следующих возможностей: <br><br>Возможность домашней и рабочей сетей: <br>- PrivateNetworkClientServer. <br><br>Добавьте по меньшей мере одну возможность общедоступных сетей и Интернета: <br>- InternetClient, <br>- InternetClientServer. <br><br>Если применимо, добавьте возможность учетных данных домена:<br>- EnterpriseAuthentication. <br><br>Примечание. Необходимо добавить сопоставления типов файлов в манифест приложения, в котором указаны конкретные типы файлов, доступные приложению в этом расположении | Получение папки с помощью: <br>[StorageFolder.GetFolderFromPathAsync](https://msdn.microsoft.com/library/windows/apps/br227278) <br><br>Получение файла с помощью: <br>[StorageFile.GetFileFromPathAsync](https://msdn.microsoft.com/library/windows/apps/br227206) |

**Пример.**

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
