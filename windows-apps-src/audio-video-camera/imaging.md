---
ms.assetid: 3FD2AA71-EF67-47B2-9332-3FFA5D3703EA
description: В этой статье объясняется, как загружать и сохранять файлы изображений с помощью BitmapDecoder и BitmapEncoder, а также как использовать объект SoftwareBitmap для представления точечных рисунков.
title: Создание, редактирование и сохранение точечных рисунков
ms.date: 03/22/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c25fc09d606c0f143f357dd7f89026fa94b80922
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67318355"
---
# <a name="create-edit-and-save-bitmap-images"></a>Создание, редактирование и сохранение точечных рисунков



В этой статье объясняется, как загрузить и сохранить файлы изображений с помощью [**BitmapDecoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) и [**BitmapEncoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapEncoder), а также как использовать объект [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) для представления точечных рисунков.

Класс **SoftwareBitmap** — это универсальный API, который можно создать из нескольких источников, включая файлы изображений, объекты [**WriteableBitmap**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.WriteableBitmap), поверхности Direct3D и код. **SoftwareBitmap** позволяет легко преобразовывать различные форматы пикселей в режимы альфа-канала и наоборот, а также получить доступ на низком уровне к пиксельным данным. Кроме того, **SoftwareBitmap** — это распространенный интерфейс, который используется различными функциями Windows, включая следующие.

-   [**CapturedFrame** ](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CapturedFrame) позволит вам получить кадры, захваченные камеры как **SoftwareBitmap**.

-   [**VideoFrame** ](https://docs.microsoft.com/uwp/api/Windows.Media.VideoFrame) позволит вам получить **SoftwareBitmap** представление **VideoFrame**.

-   [**FaceDetector** ](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.FaceDetector) позволяет обнаружить лица в **SoftwareBitmap**.

В примере кода в этой статье используется API из следующих пространств имен.

[!code-cs[Namespaces](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetNamespaces)]

## <a name="create-a-softwarebitmap-from-an-image-file-with-bitmapdecoder"></a>Создание SoftwareBitmap из файла изображения с помощью BitmapDecoder

Чтобы создать [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) из файла, получите экземпляр [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile), содержащий данные изображения. В этом примере используется [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker), что позволяет пользователю выбирать файл изображения.

[!code-cs[PickInputFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetPickInputFile)]

Вызовите метод [**OpenAsync**](https://docs.microsoft.com/uwp/api/windows.storage.istoragefile.openasync) объекта **StorageFile**, чтобы получить поток произвольного доступа, содержащий данные изображения. Вызовите статический метод [**BitmapDecoder.CreateAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.createasync), чтобы получить экземпляр класса [**BitmapDecoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) для указанного потока. Вызовите [**GetSoftwareBitmapAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.getsoftwarebitmapasync), чтобы получить объект [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap), содержащий изображение.

[!code-cs[CreateSoftwareBitmapFromFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateSoftwareBitmapFromFile)]

## <a name="save-a-softwarebitmap-to-a-file-with-bitmapencoder"></a>Сохранение SoftwareBitmap в файл с помощью BitmapEncoder

Чтобы сохранить **SoftwareBitmap** в файл, получите экземпляр **StorageFile**, в котором будет сохранено изображение. В этом примере используется [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker), что позволяет пользователю выбирать выходной файл.

[!code-cs[PickOutputFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetPickOutputFile)]

Вызовите метод [**OpenAsync**](https://docs.microsoft.com/uwp/api/windows.storage.istoragefile.openasync) объекта **StorageFile**, чтобы получить поток произвольного доступа, в который будет записано изображение. Вызовите статический метод [**BitmapEncoder.CreateAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.createasync), чтобы получить экземпляр класса [**BitmapEncoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapEncoder) для указанного потока. Первый параметр **CreateAsync** — это идентификатор GUID, представляющий кодек, который необходимо использовать для кодирования изображения. **BitmapEncoder** — класс, который предоставляет свойство, содержащее идентификатор для каждого кодека, поддерживаемого кодировщиком, например [**JpegEncoderId**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.jpegencoderid).

Используйте метод [**SetSoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.setsoftwarebitmap), чтобы задать изображение для кодирования. Вы можете задать значения свойства [**BitmapTransform**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapTransform) для применения основных преобразований изображения во время кодирования. Свойство [**IsThumbnailGenerated**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.isthumbnailgenerated) определяет, создал ли кодировщик эскиз. Обратите внимание, что не все форматы файлов поддерживают эскизы, поэтому при использовании этой функции может возникнуть ошибка о недопустимой операции, если эскизы не поддерживаются.

Вызовите метод [**FlushAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.flushasync), чтобы кодировщик записал данные изображения в указанный файл.

[!code-cs[SaveSoftwareBitmapToFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSaveSoftwareBitmapToFile)]

Вы можете задать дополнительные параметры кодирования при создании **BitmapEncoder**. Для этого создайте новый объект [**BitmapPropertySet**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapPropertySet) и заполните его одним или несколькими объектами [**BitmapTypedValue**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapTypedValue), представляющими параметры кодировщика. Список поддерживаемых параметров кодировщика см. в разделе [Справочник по параметрам BitmapEncoder](bitmapencoder-options-reference.md).

[!code-cs[UseEncodingOptions](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetUseEncodingOptions)]

## <a name="use-softwarebitmap-with-a-xaml-image-control"></a>Использование SoftwareBitmap с элементом управления изображением XAML

Чтобы отобразить изображение на странице XAML с помощью элемента управления [**Image**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image), сначала найдите элемент управления **Image** на своей странице XAML.

[!code-xml[ImageControl](./code/ImagingWin10/cs/MainPage.xaml#SnippetImageControl)]

В настоящее время элемент управления **Image** поддерживает только изображения с кодированием BGRA8 и альфа-каналом с предварительным умножением (или без альфа-канала). Перед попыткой отображения изображения убедитесь, что оно имеет правильный формат; если формат неправильный, используйте статический метод [**Convert**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.softwarebitmap.convert) объекта **SoftwareBitmap**, чтобы преобразовать изображение в поддерживаемый формат.

Создайте новый объект [**SoftwareBitmapSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource). Задайте содержимое исходного объекта, вызвав [**SetBitmapAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource.setbitmapasync), передающий **SoftwareBitmap**. Затем вы можете задать свойство [**Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image.source) элемента управления **Image** для созданного **SoftwareBitmapSource**.

[!code-cs[SoftwareBitmapToWriteableBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSoftwareBitmapToWriteableBitmap)]

Также можно использовать **SoftwareBitmapSource**, чтобы задать **SoftwareBitmap** в качестве [**ImageSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imagebrush.imagesource) для [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush).

## <a name="create-a-softwarebitmap-from-a-writeablebitmap"></a>Создание SoftwareBitmap из WriteableBitmap

Можно создать **SoftwareBitmap** из существующего **WriteableBitmap**. Для этого вызовите метод [**SoftwareBitmap.CreateCopyFromBuffer**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.softwarebitmap.createcopyfrombuffer) и укажите свойство **PixelBuffer** объекта **WriteableBitmap**, чтобы задать пиксельные данные. Второй аргумент позволяет запрашивать формат пикселей для созданного **WriteableBitmap**. Можно использовать свойства [**PixelWidth**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.bitmapsource.pixelwidth) и [**PixelHeight**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.bitmapsource.pixelheight) элемента **WriteableBitmap**, чтобы указать размеры нового изображения.

[!code-cs[WriteableBitmapToSoftwareBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetWriteableBitmapToSoftwareBitmap)]

## <a name="create-or-edit-a-softwarebitmap-programmatically"></a>Программное создание или изменение SoftwareBitmap

До сих пор в этом разделе рассматривалась работа с файлами изображений. Однако вы также можете создать **SoftwareBitmap** с помощью программного кода и использовать этот же способ для получения доступа к пиксельным данным **SoftwareBitmap** и для их изменения.

**SoftwareBitmap** использует COM-взаимодействие для отображения необработанного буфера, который содержит пиксельные данные.

Для использования COM-взаимодействия необходимо включить ссылку в пространство имен **System.Runtime.InteropServices** вашего проекта.

[!code-cs[InteropNamespace](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetInteropNamespace)]

Инициализируйте COM-интерфейс [**IMemoryBufferByteAccess**](https://docs.microsoft.com/previous-versions/mt297505(v=vs.85)), добавив следующий код в ваше пространство имен.

[!code-cs[COMImport](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCOMImport)]

Создайте **SoftwareBitmap** с требуемым форматом пикселей и размером. Можно также использовать существующий **SoftwareBitmap**, для которого нужно изменить пиксельные данные. Вызовите метод [**SoftwareBitmap.LockBuffer**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.softwarebitmap.lockbuffer), чтобы получить экземпляр класса [**BitmapBuffer**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapBuffer), представляющий буфер пиксельных данных. Приведите объект **BitmapBuffer** в COM-интерфейс объекта **IMemoryBufferByteAccess**, а затем вызовите метод [**IMemoryBufferByteAccess.GetBuffer**](https://docs.microsoft.com/windows/desktop/WinRT/imemorybufferbyteaccess-getbuffer), чтобы заполнить массив байтов данными. Используя метод [**BitmapBuffer.GetPlaneDescription**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapbuffer.getplanedescription), получите объект [**BitmapPlaneDescription**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapPlaneDescription), который поможет вычислить смещение в буфер для каждого пикселя.

[!code-cs[CreateNewSoftwareBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateNewSoftwareBitmap)]

Поскольку этот метод получает доступ к необработанному буферу, который лежит в основе типов среды выполнения Windows, его необходимо объявить с помощью ключевого слова **unsafe**. Необходимо также настроить проект в Microsoft Visual Studio, чтобы разрешить компиляцию небезопасного кода. Для этого откройте страницу проекта **Свойства**, щелкните страницу свойств **Сборка** и установите флажок **Разрешить небезопасный код**.

## <a name="create-a-softwarebitmap-from-a-direct3d-surface"></a>Создание SoftwareBitmap с поверхности Direct3D

Для создания объекта **SoftwareBitmap** с поверхности Direct3D необходимо включить в проект пространство имен [**Windows.Graphics.DirectX.Direct3D11**](https://docs.microsoft.com/uwp/api/Windows.Graphics.DirectX.Direct3D11).

[!code-cs[Direct3DNamespace](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetDirect3DNamespace)]

Вызовите [**CreateCopyFromSurfaceAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.softwarebitmap.createcopyfromsurfaceasync), чтобы создать новый объект **SoftwareBitmap** с поверхности. Как указано в имени, новый **SoftwareBitmap** имеет отдельную копию данных изображения. Изменения в **SoftwareBitmap** не будут влиять на поверхность Direct3D.

[!code-cs[CreateSoftwareBitmapFromSurface](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateSoftwareBitmapFromSurface)]

## <a name="convert-a-softwarebitmap-to-a-different-pixel-format"></a>Преобразование SoftwareBitmap в другой формат пикселей

Класс **SoftwareBitmap** предоставляет статический метод [**Convert**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.softwarebitmap.convert), который позволяет легко создать **SoftwareBitmap**, использующий формат пикселей и режим альфа-канала, указанные вами в существующем **SoftwareBitmap**. Обратите внимание, что созданный точечный рисунок содержит отдельную копию данных изображения. Изменения нового точечного рисунка не будут влиять на исходный файл.

[!code-cs[Convert](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetConvert)]

## <a name="transcode-an-image-file"></a>Перекодирование файла изображения

Вы можете перекодировать файл изображения непосредственно из [**BitmapDecoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) в [**BitmapEncoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapEncoder). Создайте [**IRandomAccessStream**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.IRandomAccessStream) из файла для перекодирования. Создайте **BitmapDecoder** из входного потока. Создайте [**InMemoryRandomAccessStream**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.InMemoryRandomAccessStream) для записи кодировщиком и вызовите метод [**BitmapEncoder.CreateForTranscodingAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.createfortranscodingasync), передав поток в памяти и объект декодера. Возможности кодирования не поддерживаются при перекодировании; вместо этого необходимо использовать команду [**CreateAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.createasync). Все свойства в файле изображения, специально не заданные в кодировщике, будут записаны в выходной файл без изменений. Вызовите метод [**FlushAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.flushasync), чтобы кодировщик выполнил кодирование в поток в памяти. Наконец, обратите поток файла и поток в памяти в начало и вызовите метод [**CopyAsync**](https://docs.microsoft.com/uwp/api/windows.storage.streams.randomaccessstream.copyasync), чтобы записать поток в памяти за пределами потока файла.

[!code-cs[TranscodeImageFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetTranscodeImageFile)]

## <a name="related-topics"></a>См. также

* [Справочник по параметрам BitmapEncoder](bitmapencoder-options-reference.md)
* [Метаданные изображений](image-metadata.md)
 

 




