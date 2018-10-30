---
author: laurenhughes
ms.assetid: 1AE29512-7A7D-4179-ADAC-F02819AC2C39
title: Файлы и папки в библиотеках музыки, изображений и видео
description: Добавьте существующие папки музыки, изображений или видео в соответствующие библиотеки. Можно также удалить папки из библиотек, получить список папок в библиотеке и найти сохраненные фотографии, музыку и видео.
ms.author: lahugh
ms.date: 06/18/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1859d758806b4e92758decb40b8a30d02acb254d
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "5766696"
---
# <a name="files-and-folders-in-the-music-pictures-and-videos-libraries"></a>Файлы и папки в библиотеках музыки, изображений и видео

Добавьте существующие папки музыки, изображений или видео в соответствующие библиотеки. Можно также удалить папки из библиотек, получить список папок в библиотеке и найти сохраненные фотографии, музыку и видео.

Библиотека — это виртуальная коллекция папок, которая содержит известную папку по умолчанию и все другие папки, добавленные пользователем в библиотеку с помощью вашего приложения или одного из встроенных приложений. Например, библиотека изображений содержит известную папку изображений по умолчанию. Пользователь может добавлять папки в библиотеку изображений или удалить их из нее с помощью вашего приложения или встроенного приложения "Фотографии".

## <a name="prerequisites"></a>Необходимые условия


-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Права на доступ к расположению**

    В Visual Studio откройте файл манифеста приложения в Конструкторе манифестов. На странице **Возможности** выберите библиотеки, которыми управляет приложение.

    -   **Библиотека музыки**
    -   **Библиотека изображений**
    -   **Библиотека видео**

    Дополнительную информацию см. в разделе [Разрешения на доступ к файлам](file-access-permissions.md).

## <a name="get-a-reference-to-a-library"></a>Получение ссылок на библиотеку

> [!NOTE]
> Обязательно объявите соответствующую возможность. Дополнительные сведения см. в статье [Объявление возможностей приложений](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).
 

Чтобы получить ссылку на библиотеку музыки, изображений или видео, вызовите метод [**StorageLibrary.GetLibraryAsync**](https://msdn.microsoft.com/library/windows/apps/dn251725). Предоставьте соответствующее значение из перечисления [**KnownLibraryId**](https://msdn.microsoft.com/library/windows/apps/dn298399).

-   [**KnownLibraryId.Music**](https://msdn.microsoft.com/library/windows/apps/br227155)
-   [**KnownLibraryId.Pictures**](https://msdn.microsoft.com/library/windows/apps/br227156)
-   [**KnownLibraryId.Videos**](https://msdn.microsoft.com/library/windows/apps/br227159)

```cs
var myPictures = await Windows.Storage.StorageLibrary.GetLibraryAsync(Windows.Storage.KnownLibraryId.Pictures);
```

## <a name="get-the-list-of-folders-in-a-library"></a>Получение списка папок в библиотеке


Чтобы получить список папок, получите значение свойства [**StorageLibrary.Folders**](https://msdn.microsoft.com/library/windows/apps/dn251724).

```cs
using Windows.Foundation.Collections;
IObservableVector<Windows.Storage.StorageFolder> myPictureFolders = myPictures.Folders;
```

## <a name="get-the-folder-in-a-library-where-new-files-are-saved-by-default"></a>Получение папки в библиотеке, в котором новые файлы сохраняются по умолчанию


Чтобы получить папку в библиотеке, в которую по умолчанию сохраняются новые файлы, получите значение свойства [**StorageLibrary.SaveFolder**](https://msdn.microsoft.com/library/windows/apps/dn251728).

```cs
Windows.Storage.StorageFolder savePicturesFolder = myPictures.SaveFolder;
```

## <a name="add-an-existing-folder-to-a-library"></a>Добавление существующей папки в библиотеку

Чтобы добавить папку в библиотеку, вызовите [**StorageLibrary.RequestAddFolderAsync**](https://msdn.microsoft.com/library/windows/apps/dn251726). Рассмотрим в качестве примера библиотеку изображений. Вызов этого метода отображает для пользователя средство выбора папок с кнопкой **Добавить эту папку в библиотеку изображений**. Если пользователь выбирает папку, то она остается в исходном расположении на диске и становится элементом в свойстве [**StorageLibrary.Folders**](https://msdn.microsoft.com/library/windows/apps/dn251724) (и во встроенном приложении «Фотографии»), но папка не отображается как дочерний элемент папки «Изображения» в проводнике.


```cs
Windows.Storage.StorageFolder newFolder = await myPictures.RequestAddFolderAsync();
```

## <a name="remove-a-folder-from-a-library"></a>Удаление папки из библиотеки

Чтобы удалить папку из библиотеки, вызовите метод [**StorageLibrary.RequestRemoveFolderAsync**](https://msdn.microsoft.com/library/windows/apps/dn251727) и укажите папку, которую нужно удалить. Можно использовать элемент управления [**StorageLibrary.Folders**](https://msdn.microsoft.com/library/windows/apps/dn251724) и [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) (или подобный ему), чтобы пользователь мог выбрать папку для удаления.

При вызове [**StorageLibrary.RequestRemoveFolderAsync**](https://msdn.microsoft.com/library/windows/apps/dn251727) пользователь увидит диалоговое окно подтверждения, указывающее, что эта папка «больше не будет отображаться в библиотеке изображений, но не будет удалена». Это означает, что папка остается в исходном расположении на диске, удаляется из свойства [**StorageLibrary.Folders**](https://msdn.microsoft.com/library/windows/apps/dn251724) и больше не будет включена во встроенное приложение «Фотографии».

В следующем примере предполагается, что пользователь выбрал папку для удаления из элемента управления [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) с именем **lvPictureFolders**.


```cs
bool result = await myPictures.RequestRemoveFolderAsync(folder);
```

## <a name="get-notified-of-changes-to-the-list-of-folders-in-a-library"></a>Получение уведомлений об изменениях списка папок в библиотеке


Для получения уведомлений об изменениях в списке папок в библиотеке зарегистрируйте обработчик для события [**StorageLibrary.DefinitionChanged**](https://msdn.microsoft.com/library/windows/apps/dn251723) библиотеки.


```cs
myPictures.DefinitionChanged += MyPictures_DefinitionChanged;

void HandleDefinitionChanged(Windows.Storage.StorageLibrary sender, object args)
{
    // ...
}
```

## <a name="media-library-folders"></a>Папки библиотеки мультимедиа


В устройстве предусмотрено пять стандартных расположений для хранения файлов мультимедиа пользователей и приложений. Встроенные приложения хранят в этих расположениях как созданные пользователями, так и загруженные файлы мультимедиа.

Используются следующие расположения.

-   Папка **Изображения**. Содержит изображения.

    -   Папка **Пленка**. Содержит фотографии и видео со встроенной камеры.

    -   Папка **Сохраненные изображения**. Содержит изображения, сохраненные пользователем из других приложений.

-   Папка **Музыка**. Содержит композиции, подкасты и аудиокниги.

-   Папка **Видео**. Содержит видео.

Пользователи или приложения также могут хранить файлы мультимедиа за пределами папок библиотек на SD-карте. Чтобы наверняка найти файл мультимедиа на SD-карте, просканируйте содержимое SD-карты или попросите пользователя выбрать файл с помощью средства выбора файлов. Дополнительные сведения см. в разделе [Доступ к SD-карте](access-the-sd-card.md).

## <a name="querying-the-media-libraries"></a>Запрос в библиотеки мультимедиа

Чтобы получить коллекцию файлов, укажите библиотеку и нужный тип файлов.

```cs
using Windows.Storage;
using Windows.Storage.Search;

private async void getSongs()
{
    QueryOptions queryOption = new QueryOptions
        (CommonFileQuery.OrderByTitle, new string[] { ".mp3", ".mp4", ".wma" });

    queryOption.FolderDepth = FolderDepth.Deep;

    Queue<IStorageFolder> folders = new Queue<IStorageFolder>();

    var files = await KnownFolders.MusicLibrary.CreateFileQueryWithOptions
      (queryOption).GetFilesAsync();

    foreach (var file in files)
    {
        // do something with the music files
    }
}
```

### <a name="query-results-include-both-internal-and-removable-storage"></a>Результаты запроса включают внутренние хранилища и съемные носители

Пользователи могут выбрать хранение файлов на дополнительной SD-карте по умолчанию. Приложения же могут запретить хранение файлов на SD-карте. В результате библиотеки мультимедиа могут храниться и во внутреннем хранилище устройства, и на SD-карте.

Для обработки этой возможности не нужно писать дополнительный код. Методы в пространстве имен [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/br227346), запрашивающие известные папки, прозрачно сочетают результаты запросов из обоих расположений. Чтобы получить эти комбинированные результаты, нет необходимости указывать возможность **removableStorage** в файле манифеста приложения.

Рассмотрите состояние хранилища устройства, показанное на следующем изображении.

![Изображения телефона и SD-карты](images/phone-media-locations.png)

Если вы запросите содержимое библиотеки изображений, вызвав `await KnownFolders.PicturesLibrary.GetFilesAsync()`, результаты будут включать как internalPic.jpg, так и SDPic.jpg.


## <a name="working-with-photos"></a>Работа с фотографиями

На устройствах, на которых камера сохраняет каждое изображение и в низком, и в высоком разрешении, глубокий запрос вернет только изображения с низким разрешением.

Папки «Пленка» и «Сохраненные изображения» не поддерживают глубокие запросы.

**Открытие фотографии в приложении, с помощью которого она снята**

Если вы хотите предоставить пользователю возможность открыть фотографию позже в том приложении, с помощью которого она снята, вы можете сохранить **CreatorAppId** с метаданными фотографии с помощью кода, похожего на приведенный в следующем примере. В этом примере **testPhoto** представляет объект [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171).

```cs
IDictionary<string, object> propertiesToSave = new Dictionary<string, object>();

propertiesToSave.Add("System.CreatorOpenWithUIOptions", 1);
propertiesToSave.Add("System.CreatorAppId", appId);

testPhoto.Properties.SavePropertiesAsync(propertiesToSave).AsyncWait();   
```

## <a name="using-stream-methods-to-add-a-file-to-a-media-library"></a>Использование потоковых методов для добавления файла в библиотеку мультимедиа

Когда вы получаете доступ к библиотеке мультимедиа с помощью известной папки, такой как **KnownFolders.PictureLibrary**, и используете потоковые методы для добавления в библиотеку файла, вам обязательно следует закрыть все потоки, которые открыл ваш код. В противном случае эти методы не смогут добавить файл в библиотеку мультимедиа ожидаемым образом, так как минимум один поток будет по-прежнему обрабатывать файл.

Например, при выполнении следующего кода файл не добавляется в библиотеку мультимедиа. В строке кода `using (var destinationStream = (await destinationFile.OpenAsync(FileAccessMode.ReadWrite)).GetOutputStreamAt(0))` методы **OpenAsync** и **GetOutputStreamAt** открывают поток. Однако только поток, открытый методом **GetOutputStreamAt**, ликвидируется в результате выполнения оператора **using**. Другой поток остается открытым и не позволяет сохранить файл.

```cs
StorageFolder testFolder = await StorageFolder.GetFolderFromPathAsync(@"C:\test");
StorageFile sourceFile = await testFolder.GetFileAsync("TestImage.jpg");
StorageFile destinationFile = await KnownFolders.CameraRoll.CreateFileAsync("MyTestImage.jpg");
using (var sourceStream = (await sourceFile.OpenReadAsync()).GetInputStreamAt(0))
{
    using (var destinationStream = (await destinationFile.OpenAsync(FileAccessMode.ReadWrite)).GetOutputStreamAt(0))
    {
        await RandomAccessStream.CopyAndCloseAsync(sourceStream, destinationStream);
    }
}
```

Чтобы успешно использовать потоковые методы для добавления файлов в библиотеку мультимедиа, обязательно закрывайте все потоки, открытые кодом, как в следующем примере.

```cs
StorageFolder testFolder = await StorageFolder.GetFolderFromPathAsync(@"C:\test");
StorageFile sourceFile = await testFolder.GetFileAsync("TestImage.jpg");
StorageFile destinationFile = await KnownFolders.CameraRoll.CreateFileAsync("MyTestImage.jpg");

using (var sourceStream = await sourceFile.OpenReadAsync())
{
    using (var sourceInputStream = sourceStream.GetInputStreamAt(0))
    {
        using (var destinationStream = await destinationFile.OpenAsync(FileAccessMode.ReadWrite))
        {
            using (var destinationOutputStream = destinationStream.GetOutputStreamAt(0))
            {
                await RandomAccessStream.CopyAndCloseAsync(sourceInputStream, destinationStream);
            }
        }
    }
}
```
