---
author: TylerMSFT
ms.assetid: 1AE29512-7A7D-4179-ADAC-F02819AC2C39
title: "Файлы и папки в библиотеках музыки, изображений и видео"
description: "Добавьте существующие папки музыки, изображений или видео в соответствующие библиотеки. Можно также удалить папки из библиотек, получить список папок в библиотеке и найти сохраненные фотографии, музыку и видео."
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 332f89f53a55d5783f7497ca5c6cd601dcee5217

---

# Файлы и папки в библиотеках музыки, изображений и видео


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Добавьте существующие папки музыки, изображений или видео в соответствующие библиотеки. Можно также удалить папки из библиотек, получить список папок в библиотеке и найти сохраненные фотографии, музыку и видео.

Библиотека — это виртуальная коллекция папок, которая содержит известную папку по умолчанию и все другие папки, добавленные пользователем в библиотеку с помощью вашего приложения или одного из встроенных приложений. Например, библиотека изображений содержит известную папку изображений по умолчанию. Пользователь может добавлять папки в библиотеку изображений или удалить их из нее с помощью вашего приложения или встроенного приложения "Фотографии".

## Необходимые условия


-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Права на доступ к расположению**

    В Visual Studio откройте файл манифеста приложения в Конструкторе манифестов. На странице **Возможности** выберите библиотеки, которыми управляет приложение.

    -   **Библиотека музыки**
    -   **Библиотека изображений**
    -   **Библиотека видео**

    Дополнительную информацию см. в разделе [Разрешения на доступ к файлам](file-access-permissions.md).

## Получение ссылок на библиотеку


**Примечание.** Не забудьте объявить соответствующую возможность.
 

Чтобы получить ссылку на библиотеку музыки, изображений или видео, вызовите метод [**StorageLibrary.GetLibraryAsync**](https://msdn.microsoft.com/library/windows/apps/dn251725). Предоставьте соответствующее значение из перечисления [**KnownLibraryId**](https://msdn.microsoft.com/library/windows/apps/dn298399).

-   [**KnownLibraryId.Music**](https://msdn.microsoft.com/library/windows/apps/br227155)
-   [**KnownLibraryId.Pictures**](https://msdn.microsoft.com/library/windows/apps/br227156)
-   [**KnownLibraryId.Videos**](https://msdn.microsoft.com/library/windows/apps/br227159)

```CSharp
    var myPictures = await Windows.Storage.StorageLibrary.GetLibraryAsync
        (Windows.Storage.KnownLibraryId.Pictures);
```

## Получение списка папок в библиотеке


Чтобы получить список папок, получите значение свойства [**StorageLibrary.Folders**](https://msdn.microsoft.com/library/windows/apps/dn251724).

```CSharp
    using Windows.Foundation.Collections;

    // ...
            
    IObservableVector<Windows.Storage.StorageFolder> myPictureFolders = myPictures.Folders;
```

## Получение папки в библиотеке, в котором новые файлы сохраняются по умолчанию


Чтобы получить папку в библиотеке, в которую по умолчанию сохраняются новые файлы, получите значение свойства [**StorageLibrary.SaveFolder**](https://msdn.microsoft.com/library/windows/apps/dn251728).

```CSharp
    Windows.Storage.StorageFolder savePicturesFolder = myPictures.SaveFolder;
```

## Добавление существующей папки в библиотеку


Чтобы добавить папку в библиотеку, вызовите [**StorageLibrary.RequestAddFolderAsync**](https://msdn.microsoft.com/library/windows/apps/dn251726). Рассмотрим в качестве примера библиотеку изображений. Вызов этого метода отображает для пользователя средство выбора папок с кнопкой **Добавить эту папку в библиотеку изображений**. Если пользователь выбирает папку, то она остается в исходном расположении на диске и становится элементом в свойстве [**StorageLibrary.Folders**](https://msdn.microsoft.com/library/windows/apps/dn251724) (и во встроенном приложении «Фотографии»), но папка не отображается как дочерний элемент папки «Изображения» в проводнике.


```CSharp
    Windows.Storage.StorageFolder newFolder = await myPictures.RequestAddFolderAsync();
```

## Удаление папки из библиотеки


Чтобы удалить папку из библиотеки, вызовите метод [**StorageLibrary.RequestRemoveFolderAsync**](https://msdn.microsoft.com/library/windows/apps/dn251727) и укажите папку, которую нужно удалить. Можно использовать элемент управления [**StorageLibrary.Folders**](https://msdn.microsoft.com/library/windows/apps/dn251724) и [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) (или подобный ему), чтобы пользователь мог выбрать папку для удаления.

При вызове [**StorageLibrary.RequestRemoveFolderAsync**](https://msdn.microsoft.com/library/windows/apps/dn251727) пользователь увидит диалоговое окно подтверждения, указывающее, что эта папка «больше не будет отображаться в библиотеке изображений, но не будет удалена». Это означает, что папка остается в исходном расположении на диске, удаляется из свойства [**StorageLibrary.Folders**](https://msdn.microsoft.com/library/windows/apps/dn251724) и больше не будет включена во встроенное приложение «Фотографии».

В следующем примере предполагается, что пользователь выбрал папку для удаления из элемента управления [**ListView**](https://msdn.microsoft.com/library/windows/apps/br242878) с именем **lvPictureFolders**.


```CSharp
    bool result = await myPictures.RequestRemoveFolderAsync(folder);
```

## Получение уведомлений об изменениях списка папок в библиотеке


Для получения уведомлений об изменениях в списке папок в библиотеке зарегистрируйте обработчик для события [**StorageLibrary.DefinitionChanged**](https://msdn.microsoft.com/library/windows/apps/dn251723) библиотеки.


```CSharp
    myPictures.DefinitionChanged += MyPictures_DefinitionChanged;
    // ...

void HandleDefinitionChanged(Windows.Storage.StorageLibrary sender, object args)
{
    // ...
}
```

## Папки библиотеки мультимедиа


В устройстве предусмотрено пять стандартных расположений для хранения файлов мультимедиа пользователей и приложений. Встроенные приложения хранят в этих расположениях как созданные пользователями, так и загруженные файлы мультимедиа.

Используются следующие расположения.

-   Папка **Изображения**. Содержит изображения.

    -   Папка **Пленка**. Содержит фотографии и видео со встроенной камеры.

    -   Папка **Сохраненные изображения**. Содержит изображения, сохраненные пользователем из других приложений.

-   Папка **Музыка**. Содержит композиции, подкасты и аудиокниги.

-   Папка **Видео**. Содержит видео.

Пользователи или приложения также могут хранить файлы мультимедиа за пределами папок библиотек на SD-карте. Чтобы наверняка найти файл мультимедиа на SD-карте, просканируйте содержимое SD-карты или попросите пользователя выбрать файл с помощью средства выбора файлов. Дополнительные сведения см. в разделе [Доступ к SD-карте](access-the-sd-card.md).

## Запрос в библиотеки мультимедиа


### Результаты запроса включают внутренние хранилища и съемные носители

Пользователи могут выбрать хранение файлов на дополнительной SD-карте по умолчанию. Приложения же могут запретить хранение файлов на SD-карте. В результате библиотеки мультимедиа могут храниться и во внутреннем хранилище устройства, и на SD-карте.

Для обработки этой возможности не нужно писать дополнительный код. Методы в пространстве имен [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/br227346), запрашивающие известные папки, прозрачно сочетают результаты запросов из обоих расположений. Чтобы получить эти комбинированные результаты, нет необходимости указывать возможность **removableStorage** в файле манифеста приложения.

Рассмотрите состояние хранилища устройства, показанное на следующем изображении.

![Изображения телефона и SD-карты](images/phone-media-locations.png)

Если вы запросите содержимое библиотеки изображений, вызвав `await KnownFolders.PicturesLibrary.GetFilesAsync()`, результаты будут включать как internalPic.jpg, так и SDPic.jpg.

### Глубокие запросы

Глубокие запросы позволяют быстро перечислить все содержимое библиотеки мультимедиа.

Глубокие запросы возвращают только файлы мультимедиа указанного типа. Например, если вы обратились с помощью такого запроса к библиотеке музыки, в результаты не будут включены файлы изображений, найденные в папке «Музыка».

На устройствах, на которых камера сохраняет каждое изображение и в низком, и в высоком разрешении, глубокий запрос вернет только изображения с низким разрешением.

Папки «Пленка» и «Сохраненные изображения» не поддерживают глубокие запросы.

Доступны следующие глубокие запросы.

**Библиотека изображений**

-   `GetFilesAsync(CommonFileQuery.OrderByDate)`

**Библиотека музыки**

-   `GetFilesAsync(CommonFileQuery.OrderByName)`
-   `GetFoldersAsync(CommonFolderQuery.GroupByArtist)`
-   `GetFoldersAysnc(CommonFolderQuery.GroupByAlbum)`
-   `GetFoldersAysnc(CommonFolderQuery.GroupByAlbumArtist)`
-   `GetFoldersAsync(CommonFolderQuery.GroupByGenre)`

**Видеотека**

-   `GetFilesAsync(CommonFileQuery.OrderByDate)`

### Плоские запросы

Чтобы получить полный список всех файлов и папок в библиотеке, вызовите `GetFilesAsync(CommonFileQuery.DefaultQuery)`. Этот метод возвращает все файлы библиотеки вне зависимости от их типа. Это плоский запрос, поэтому, если пользователь создал такие папки в библиотеке, вам придется перечислить содержимое вложенных папок рекурсивно.

Плоские запросы позволяют получить файлы мультимедиа тех типов, которые не распознаются встроенными запросами, или все файлы библиотеки, включая файлы других типов, кроме указанного. Например, если вы обратились с помощью такого запроса к библиотеке музыки, в результаты будут включены также и все файлы изображений, найденные в папке «Музыка».

### Примеры запросов

Предположим, что на устройстве и дополнительной SD-карте хранятся папки и файлы, представленные на следующем изображении.

![Файлы на ](images/phone-media-queries.png)

Вот несколько примеров запросов и результаты, которые вернут такие запросы.

| Запрос | Результаты |
|--------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| KnownFolders.PicturesLibrary.GetItemsAsync();  | - Папка «Пленка» во внутреннем хранилище <br>- Папка «Пленка» на SD-карте <br>- Папка «Сохраненные изображения» во внутреннем хранилище <br>- Папка «Сохраненные изображения» на SD-карте <br><br>Это плоский запрос, поэтому возвращаются только непосредственные дочерние объекты папки «Изображения». |
| KnownFolders.PicturesLibrary.GetFilesAsync();  | Нет результатов. <br><br>Это плоский запрос, а папка «Изображения» не содержит файлов, представляющих собой ее непосредственные дочерние объекты. |
| KnownFolders.PicturesLibrary.GetFilesAsync(CommonFileQuery.OrderByDate); | - Файл 4-3-2012.jpg с SD-карты <br>- Файл 1-1-2014.jpg из внутреннего хранилища <br>- Файл 1-2-2014.jpg из внутреннего хранилища <br>- Файл 1-6-2014.jpg с SD-карты <br><br>Это глубокий запрос, поэтому возвращается содержимое папки «Изображения» и ее дочерних папок. |
| KnownFolders.CameraRoll.GetFilesAsync(); | - Файл 1-1-2014.jpg из внутреннего хранилища <br>- Файл 4-3-2012.jpg с SD-карты <br><br>Это плоский запрос. Упорядочение результатов не гарантируется. |

 
## Возможности библиотеки мультимедиа и типы файлов


Ниже перечислены возможности, которые вы можете указать в файле манифеста приложения для доступа к файлам мультимедиа в приложении.

-   **Музыка**. Укажите возможность **Библиотека музыки** в файле манифеста приложения, чтобы приложение могло видеть файлы следующих типов и получать доступ к ним.

    -   QCP
    -   WAV
    -   MP3
    -   M4R
    -   M4A
    -   AAC
    -   AMR
    -   WMA
    -   3G2
    -   3GP
    -   MP4
    -   WM
    -   ASF
    -   3GPP
    -   3GP2
    -   MPA
    -   ADT
    -   ADTS
    -   PYA
-   **Фотографии**. Укажите возможность **Библиотека изображений** в файле манифеста приложения, чтобы приложение могло видеть файлы следующих типов и получать доступ к ним.

    -   JPEG
    -   JPE
    -   JPG
    -   GIF
    -   TIFF
    -   TIF
    -   PNG
    -   BMP
    -   WDP
    -   JXR
    -   HDP
-   **Видео**. Укажите возможность **Video Library** в файле манифеста приложения, чтобы приложение могло видеть файлы следующих типов и получать доступ к ним.

    -   WM
    -   M4V
    -   WMV
    -   ASF
    -   MOV
    -   MP4
    -   3G2
    -   3GP
    -   MP4V
    -   AVI
    -   PYV
    -   3GPP
    -   3GP2

## Работа с фотографиями


На устройствах, на которых камера сохраняет каждое изображение и в низком, и в высоком разрешении, глубокий запрос вернет только изображения с низким разрешением.

Папки «Пленка» и «Сохраненные изображения» не поддерживают глубокие запросы.

**Открытие фотографии в приложении, с помощью которого она снята**

Если вы хотите предоставить пользователю возможность открыть фотографию позже в том приложении, с помощью которого она снята, вы можете сохранить **CreatorAppId** с метаданными фотографии с помощью кода, похожего на приведенный в следующем примере. В этом примере **testPhoto** представляет объект [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171).

```CSharp
  IDictionary<string, object> propertiesToSave = new Dictionary<string, object>();

  propertiesToSave.Add("System.CreatorOpenWithUIOptions", 1);
  propertiesToSave.Add("System.CreatorAppId", appId);
 
  testPhoto.Properties.SavePropertiesAsync(propertiesToSave).AsyncWait();   
```

## Использование потоковых методов для добавления файла в библиотеку мультимедиа


Когда вы получаете доступ к библиотеке мультимедиа с помощью известной папки, такой как **KnownFolders.PictureLibrary**, и используете потоковые методы для добавления в библиотеку файла, вам обязательно следует закрыть все потоки, которые открыл ваш код. В противном случае эти методы не смогут добавить файл в библиотеку мультимедиа ожидаемым образом, так как минимум один поток будет по-прежнему обрабатывать файл.

Например, при выполнении следующего кода файл не добавляется в библиотеку мультимедиа. В строке кода `using (var destinationStream = (await destinationFile.OpenAsync(FileAccessMode.ReadWrite)).GetOutputStreamAt(0))` методы **OpenAsync** и **GetOutputStreamAt** открывают поток. Однако только поток, открытый методом **GetOutputStreamAt**, ликвидируется в результате выполнения оператора **using**. Другой поток остается открытым и не позволяет сохранить файл.

```CSharp
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

```CSharp
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

 

 







<!--HONumber=Jun16_HO4-->


