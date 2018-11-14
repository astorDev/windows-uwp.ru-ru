---
author: TylerMSFT
title: Работа с файлами
description: Узнайте, как работать с файлами на универсальной платформе Windows.
ms.author: twhitney
ms.date: 05/01/2018
ms.topic: article
keywords: начало работы, uwp, windows 10, обучающий курс, файлы, ввод-вывод файлов, чтение файла, запись файла, создание файла, запись текста, чтение текста
ms.localizationpriority: medium
ms.openlocfilehash: 02ac4d3f157343182097e67dc8825dde940302c3
ms.sourcegitcommit: 71e8eae5c077a7740e5606298951bb78fc42b22c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6666300"
---
# <a name="work-with-files"></a>Работа с файлами

В этом разделе описывается чтение файлов и запись данные в файлы приложении универсальной платформы Windows (UWP). Здесь представлены основные API-интерфейсы и типы, а также ссылки на дополнительные ресурсы.

Это не учебное пособие. Если вам необходимо полное руководство, см. разделе [Создание, запись и чтение файла](https://docs.microsoft.com/windows/uwp/files/quickstart-reading-and-writing-files), в котором показано, как создавать, читать и записывать файлы, использовать буферы и потоки. Вас также может заинтересовать [пример доступа к файлу](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FileAccess), в котором показано, как создавать, читать, записывать, копировать и удалять файл, а также как получать свойства файла и запоминать файлу или папку, чтобы ваше приложение легко могло получить доступ к ним снова.

Мы рассмотрим код для записи текста в файл и чтения текста из файла, а также для получения доступа к папкам локальным, перемещаемым и временным папкам приложения.

## <a name="what-do-you-need-to-know"></a>Что необходимо знать?

Ниже приведены основные типы, которые вам нужно знать для чтения текста и записи текста в файл.

- [Windows.Storage.StorageFile](https://docs.microsoft.com/uwp/api/windows.storage.storagefile) представляет файл. Этот класс содержит свойства, которые предоставляют сведения о файле, и методы для создания, открытия, копирования, удаления и переименования файлов.
Возможно, вы привыкли работать со строковыми путями. Существует ряд API-интерфейсов UWP, принимающих строковый путь, однако чаще всего вы будете использовать объект **StorageFile** для представления файла, так как некоторые файлы на платформе UWP могут быть без пути или их путь может быть слишком громоздким. Используйте метод [StorageFile.GetFileFromPathAsync()](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.getfilefrompathasync) для преобразования строкового пути в объект **StorageFile**. 

- Класс [FileIO](https://docs.microsoft.com/uwp/api/windows.storage.fileio) предоставляет простой способ для чтения и записи текста. Этот класс также может читать и записывать массив байтов или содержимое буфера. Этот класс очень похож на класс [PathIO](https://docs.microsoft.com/uwp/api/windows.storage.pathio). Основное различие заключается в том, что вместо использования строкового пути, как в **PathIO**, они принимает **StorageFile**.
- [Windows.Storage.StorageFolder](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder) представляет папку (каталог). Этот класс содержит методы для создания файлов, запроса содержимого папки, создания, переименования и удаление папок, а также свойства, которые предоставляют сведения о папке. 

Вот распространенные способы получения **StorageFolder**.

- [Windows.Storage.Pickers.FolderPicker](https://docs.microsoft.com/uwp/api/windows.storage.pickers.folderpicker) позволяет пользователю перейти к требуемой папке.
- [Windows.Storage.ApplicationData.Current](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.current) предоставляет объект **StorageFolder** для одной из локальных для приложения папок, например локальной, перемещаемой и временной папки.
- [Windows.Storage.KnownFolders](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders) предоставляет **StorageFolder** для известных библиотеки, таких как "Музыка" и "Изображения".

## <a name="write-text-to-a-file"></a>Запись текста в файл

 В рамках этого обзор мы рассмотрим простой сценарий: чтение и запись текста. Начнем с кода, который использует класс **FileIO**, чтобы записать текст в файл.

```csharp
Windows.Storage.StorageFolder storageFolder = Windows.Storage.ApplicationData.Current.LocalFolder;
Windows.Storage.StorageFile file = await storageFolder.CreateFileAsync("test.txt",
        Windows.Storage.CreationCollisionOption.OpenIfExists);

await Windows.Storage.FileIO.WriteTextAsync(file, "Example of writing a string\r\n");

// Append a list of strings, one per line, to the file
var listOfStrings = new List<string> { "line1", "line2", "line3" };
await Windows.Storage.FileIO.AppendLinesAsync(file, listOfStrings); // each entry in the list is written to the file on its own line.
```

Сначала мы определим, где файл должен быть размещен. `Windows.Storage.ApplicationData.Current.LocalFolder` предоставляет доступ к локальной папке данных, которая создается для приложения во время установки. Дополнительные сведения о папках, доступных вашему приложению, см. в разделе [Доступ к файловой системе](#access-the-file-system).

Затем мы используем **StorageFolder** для создания файла (или его открытия, если он уже существует).

Класс **FileIO** предоставляет удобный способ записи текста в файл. `FileIO.WriteTextAsync()` заменяет все содержимое файла предоставленным текстом. `FileIO.AppendLinesAsync()` добавляет в файл коллекцию строк, записывая по одному строковому значению в каждую строку.

## <a name="read-text-from-a-file"></a>Чтение текста из файла

Как и при записи в файл, чтение файла начинается с указания его расположения. Мы будем использовать то же расположение, как и в примере выше. Затем мы используем класс **FileIO** для чтения его содержимого.

```csharp
Windows.Storage.StorageFolder storageFolder = Windows.Storage.ApplicationData.Current.LocalFolder;
Windows.Storage.StorageFile file = await storageFolder.GetFileAsync("test.txt");

string text = await Windows.Storage.FileIO.ReadTextAsync(file);
```

Вы также можете считывать каждую строку файла в отдельные строки в коллекции с помощью `IList<string> contents = await Windows.Storage.FileIO.ReadLinesAsync(sampleFile);`

## <a name="access-the-file-system"></a>Доступ к файловой системе

На платформе UWP доступ к папке ограничивается, чтобы обеспечить целостность и конфиденциальность данных пользователя. 

### <a name="app-folders"></a>Папки приложения

При установке приложение UWP в каталоге c:\users\<имя пользователя>\AppData\Local\Packages\<идентификатор пакета приложения>\ создается несколько папок для хранения, помимо прочего, локальных, перемещаемых и временных файлов приложения. Приложению не нужно объявлять какие-либо возможности, для доступа к этим папкам, и они недоступны другим приложениям. Эти папки также удаляются при удалении приложения.

Вот некоторые из папок приложения, которые часто используются.

- **LocalState**: для локальных на текущем устройстве данных. При резервном копировании устройства данные в этом каталоге сохраняются в образе резервной копии в службе OneDrive. Если пользователь сбрасывает или заменяет устройства, эти данные будут восстановлены. Для доступа к этой папке используйте `Windows.Storage.ApplicationData.Current.LocalFolder.`. Сохраните локальные данные, которые не требуется архивировать в OneDrive, в объекте **LocalCacheFolder**, к которому можно обратиться с помощью `Windows.Storage.ApplicationData.Current.LocalCacheFolder`.

- **RoamingState**: для данных, которые должны реплицироваться на всех устройствах, где установлено приложение. Windows ограничивает объем данных, которые будут перемещаться, поэтому сохраняйте здесь только параметры пользователя и небольшие файлы. Для доступа к перемещаемой папке используйте `Windows.Storage.ApplicationData.Current.RoamingFolder`.

- **TempState**: для данных, которые могут быть удалены, когда приложение не выполняется. Для доступа к этой папке используйте `Windows.Storage.ApplicationData.Current.TemporaryFolder`.

### <a name="access-the-rest-of-the-file-system"></a>Доступ к остальной части файловой системы

Приложение UWP должно объявить свои намерения для доступа к определенной библиотеке пользователя, добавив соответствующую возможность в свой манифест. Затем при установке приложения пользователям предлагается подтвердить, что они разрешили доступ к указанной библиотеке. Если это не так, приложение не устанавливается. Существуют возможности для доступа к библиотекам изображений, видео и музыки. Полный список см. в разделе [Объявление возможностей приложения](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations). Чтобы получить **StorageFolder** для этих библиотек, используйте класс [Windows.Storage.KnownFolders](https://docs.microsoft.com/uwp/api/windows.storage.knownfolders).

#### <a name="documents-library"></a>Библиотека документов

Хотя и существует возможность доступа к библиотеке документов пользователя, она ограничена, то есть приложение, которое ее объявит, будет отклонено в Microsoft Store, если не воспользуетесь процессом, чтобы получить специальное разрешение. Оно не предназначено для общего использования. Вместо этого используйте средства выбора файла или папки (см. разделы [Открытие файлов и папок с помощью средства выбора](https://docs.microsoft.com/windows/uwp/files/quickstart-using-file-and-folder-pickers) и [Сохранение файла с помощью средства выбора](https://docs.microsoft.com/windows/uwp/files/quickstart-save-a-file-with-a-picker)), позволяющие пользователям перейти к папке или файлу. При переходе пользователей к файлу или папке они неявно разрешают приложению доступа к ним, и система разрешает доступ.

#### <a name="general-access"></a>Общий доступ

Кроме того, ваше приложение может объявить ограниченную возможность [broadFileSystem](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations) в манифесте, которая также должна быть утверждена в Microsoft Store. Затем приложение сможет получить доступ к любому файлу, к которому обращается пользователь, без вмешательства средства выбора файла или папки.

Полный список расположений, которые доступны приложениям, см. в разделе [Разрешения на доступ к файлам](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).

## <a name="useful-apis-and-docs"></a>Полезные API и документы

Вот краткое описание API-интерфейсов и другая полезная документация, которая поможет вам начать работу с файлами и папками.

### <a name="useful-apis"></a>Полезные интерфейсы API

| API | Описание |
|------|---------------|
|  [Windows.Storage.StorageFile](https://docs.microsoft.com/uwp/api/windows.storage.storagefile) | Предоставляет сведения о файле, и методы для создания, открытия, копирования, удаления и переименования файлов. |
| [Windows.Storage.StorageFolder](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder) | Предоставляет сведения о папке, методах для создания файлов и методах для создания, удаления и переименования папок. |
| [FileIO](https://docs.microsoft.com/uwp/api/windows.storage.fileio) |  Предоставляет простой способ для чтения и записи текста. Этот класс также может читать и записывать массив байтов или содержимое буфера. |
| [PathIO](https://docs.microsoft.com/uwp/api/windows.storage.pathio) | Предоставляет простой способ чтения и записи текста из файла по строковому пути к файлу. Этот класс также может читать и записывать массив байтов или содержимое буфера. |
| [DataReader](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader) & [DataWriter](https://docs.microsoft.com/uwp/api/windows.storage.streams.datawriter) |  Чтение и запись буферов, байт, целых чисел, идентификаторов GUID, объектов TimeSpan и других данных из потока и в поток. |
| [Windows.Storage.ApplicationData.Current](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.current) | Предоставляет доступ к папкам, созданным для приложения, таким как папки локальных, перемещаемых и временных файлов. |
| [Windows.Storage.Pickers.FolderPicker](https://docs.microsoft.com/uwp/api/windows.storage.pickers.folderpicker) |  Позволяет пользователю выбрать папку и возвращает ее **StorageFolder**. Этот класс используется, чтобы получить доступ к расположениям, которые по умолчанию приложению недоступны. |
| [Windows.Storage.Pickers.FileOpenPicker](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker) | Позволяет пользователю выбрать файл и возвращает его **StorageFile**. Этот класс используется, чтобы получить доступ к файлу, который по умолчанию приложению недоступен. |
| [Windows.Storage.Pickers.FileSavePicker](https://docs.microsoft.com/uwp/api/windows.storage.pickers.filesavepicker) | Позволяет пользователю выбрать имя, расширение и расположение файла. Возвращает **StorageFile**. Этот класс используется, чтобы сохранить файл в расположении, которое недоступно приложению по умолчанию. |
|  [Пространство имен Windows.Storage.Streams](https://docs.microsoft.com/uwp/api/windows.storage.streams) | Охватывает потоки чтения и записи. В частности, изучите классы [DataReader](https://docs.microsoft.com/uwp/api/windows.storage.streams.datareader) и [DataWriter](https://docs.microsoft.com/uwp/api/windows.storage.streams.datawriter), которые считывают и записывают буферы, байты, целые числа, GUID, TimeSpan и др. |

### <a name="useful-docs"></a>Полезные документы

| Статья | Описание |
|-------|----------------|
| [Пространство имен Windows.Storage](https://docs.microsoft.com/uwp/api/windows.storage) | Справочные документы по API. |
| [Файлы, папки и библиотеки](https://docs.microsoft.com/windows/uwp/files/) | Концептуальные документы. |
| [Создание, запись и чтение файла](https://docs.microsoft.com/windows/uwp/files/quickstart-reading-and-writing-files) | Описание создания, чтения и записи текста, двоичных данных и потоков. |
| [Начало работы с локальным хранением данных приложения](https://blogs.windows.com/buildingapps/2016/05/10/getting-started-storing-app-data-locally/#pCbJKGjcShh5DTV5.97) | Помимо рекомендаций для хранения локальных данных здесь также описывается назначение папок LocalSettings и LocalCache. |
| [Начало работы с перемещающимися данными приложения](https://blogs.windows.com/buildingapps/2016/05/03/getting-started-with-roaming-app-data/#RgjgLt5OkU9DbVV8.97) | Серия из двух частей, посвященная использованию перемещаемых данных приложения. |
| [Руководство по перемещаемым данным приложения](http://msdn.microsoft.com/library/windows/apps/hh465094) | Следуйте этим рекомендациям для перемещаемых данных при проектировании приложения. |
| [Хранение и извлечение параметров и прочих данных приложения](https://docs.microsoft.com/windows/uwp/design/app-settings/store-and-retrieve-app-data) | Общие сведения о различных хранилищах+ данных приложений, таких как локальные, перемещаемые и временные папки. В разделе [Перемещаемые данные](https://docs.microsoft.com/windows/uwp/design/app-settings/store-and-retrieve-app-data#roaming-data) представлены инструкции и дополнительные сведения о записи данных, которые перемещаются между устройствами. |
| [Разрешения на доступ к файлам](https://docs.microsoft.com/windows/uwp/files/file-access-permissions) | Сведения о расположениях в файловой системе, доступных вашему приложению. |
| [Открытие файлов и папок с помощью средства выбора](https://docs.microsoft.com/windows/uwp/files/quickstart-using-file-and-folder-pickers) | Здесь описывается, как получить доступ к файлам и папкам, разрешив пользователю взаимодействовать со средством выбора. |
| [Windows.Storage.Streams](https://docs.microsoft.com/uwp/api/windows.storage.streams) | Типы, используемые для чтения и записи потоков. |
| [Файлы и папки в библиотеках музыки, изображений и видео](https://docs.microsoft.com/windows/uwp/files/quickstart-managing-folders-in-the-music-pictures-and-videos-libraries) | Здесь описывается, как удалить папки из библиотек, получить список папок в библиотеке и найти сохраненные фотографии, музыку и видео. |

## <a name="useful-code-samples"></a>Полезные примеры кода

| Пример кода | Описание |
|-----------------|---------------|
| [Образец данных приложения](https://code.msdn.microsoft.com/windowsapps/ApplicationData-sample-fb043eb2) | Здесь показано, как хранить и извлекать данные, связанные с отдельным пользователем, с помощью API-интерфейсов данных приложения. |
| [Пример доступа к файлу](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/FileAccess) | Здесь показано, как создавать, читать, копировать и удалять файл. |
| [Пример средства выбора файлов](http://code.msdn.microsoft.com/windowsapps/File-picker-sample-9f294cba) | Здесь показано, как получить доступ к файлам и папкам, позволяя пользователю выбирать их с помощью пользовательского интерфейса, и как сохранить файл, чтобы пользователь мог указывать имя, тип и расположение файла. |
| [Пример JSON](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Json) | В этой статье показано, как кодировать и декодировать объекты, массивы, строки, числа и логические значения JSON с помощью [пространства имен Windows.Data.Json](https://docs.microsoft.com/uwp/api/Windows.Data.Json). |
| [Дополнительные примеры кода](https://developer.microsoft.com//windows/samples) | Выберите **Файлы, папки и библиотеки** в раскрывающемся списке категорий. |