---
author: laurenhughes
ms.assetid: 3FD2AA71-EF67-47B2-9332-3FFA5D3703EA
description: В этой статье объясняется, как загружать и сохранять файлы изображений с помощью BitmapDecoder и BitmapEncoder, а также как использовать объект SoftwareBitmap для представления точечных рисунков.
title: Создание, редактирование и сохранение точечных рисунков
ms.author: lahugh
ms.date: 03/22/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: dc7d3d70291d29102af614f29fd4531523a961e1
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6029572"
---
# <a name="create-edit-and-save-bitmap-images"></a>Создание, редактирование и сохранение точечных рисунков



В этой статье объясняется, как загрузить и сохранить файлы изображений с помощью [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/br226176) и [**BitmapEncoder**](https://msdn.microsoft.com/library/windows/apps/br226206), а также как использовать объект [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358) для представления точечных рисунков.

Класс **SoftwareBitmap**— это универсальный API, который можно создать из нескольких источников, включая файлы изображений, объекты [**WriteableBitmap**](https://msdn.microsoft.com/library/windows/apps/br243259), поверхности Direct3D и код. **SoftwareBitmap** позволяет легко преобразовывать различные форматы пикселей в режимы альфа-канала и наоборот, а также получить доступ на низком уровне к пиксельным данным. Кроме того, **SoftwareBitmap**— это распространенный интерфейс, который используется различными функциями Windows, включая следующие.

-   [**CapturedFrame**](https://msdn.microsoft.com/library/windows/apps/dn278725) позволяет получить кадры, захваченные с камеры в виде **SoftwareBitmap**.

-   [**VideoFrame**](https://msdn.microsoft.com/library/windows/apps/dn930917) позволяет получить представление **SoftwareBitmap** элемента **VideoFrame**.

-   [**FaceDetector**](https://msdn.microsoft.com/library/windows/apps/dn974129) позволяет обнаруживать лица в **SoftwareBitmap**.

В примере кода в этой статье используется API из следующих пространств имен.

[!code-cs[Namespaces](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetNamespaces)]

## <a name="create-a-softwarebitmap-from-an-image-file-with-bitmapdecoder"></a>Создание SoftwareBitmap из файла изображения с помощью BitmapDecoder

Чтобы создать [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358) из файла, получите экземпляр [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), содержащий данные изображения. В этом примере используется [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847), что позволяет пользователю выбирать файл изображения.

[!code-cs[PickInputFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetPickInputFile)]

Вызовите метод [**OpenAsync**](https://msdn.microsoft.com/library/windows/apps/br227116) объекта **StorageFile**, чтобы получить поток произвольного доступа, содержащий данные изображения. Вызовите статический метод [**BitmapDecoder.CreateAsync**](https://msdn.microsoft.com/library/windows/apps/br226182), чтобы получить экземпляр класса [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/br226176) для указанного потока. Вызовите [**GetSoftwareBitmapAsync**](https://msdn.microsoft.com/library/windows/apps/dn887332), чтобы получить объект [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358), содержащий изображение.

[!code-cs[CreateSoftwareBitmapFromFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateSoftwareBitmapFromFile)]

## <a name="save-a-softwarebitmap-to-a-file-with-bitmapencoder"></a>Сохранение SoftwareBitmap в файл с помощью BitmapEncoder

Чтобы сохранить **SoftwareBitmap** в файл, получите экземпляр **StorageFile**, в котором будет сохранено изображение. В этом примере используется [**FileSavePicker**](https://msdn.microsoft.com/library/windows/apps/br207871), что позволяет пользователю выбирать выходной файл.

[!code-cs[PickOuputFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetPickOuputFile)]

Вызовите метод [**OpenAsync**](https://msdn.microsoft.com/library/windows/apps/br227116) объекта **StorageFile**, чтобы получить поток произвольного доступа, в который будет записано изображение. Вызовите статический метод [**BitmapEncoder.CreateAsync**](https://msdn.microsoft.com/library/windows/apps/br226211), чтобы получить экземпляр класса [**BitmapEncoder**](https://msdn.microsoft.com/library/windows/apps/br226206) для указанного потока. Первый параметр **CreateAsync**— это идентификатор GUID, представляющий кодек, который необходимо использовать для кодирования изображения. **BitmapEncoder** — класс, который предоставляет свойство, содержащее идентификатор для каждого кодека, поддерживаемого кодировщиком, например [**JpegEncoderId**](https://msdn.microsoft.com/library/windows/apps/br226226).

Используйте метод [**SetSoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887337), чтобы задать изображение для кодирования. Вы можете задать значения свойства [**BitmapTransform**](https://msdn.microsoft.com/library/windows/apps/br226254) для применения основных преобразований изображения во время кодирования. Свойство [**IsThumbnailGenerated**](https://msdn.microsoft.com/library/windows/apps/br226225) определяет, создал ли кодировщик эскиз. Обратите внимание, что не все форматы файлов поддерживают эскизы, поэтому при использовании этой функции может возникнуть ошибка о недопустимой операции, если эскизы не поддерживаются.

Вызовите метод [**FlushAsync**](https://msdn.microsoft.com/library/windows/apps/br226216), чтобы кодировщик записал данные изображения в указанный файл.

[!code-cs[SaveSoftwareBitmapToFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSaveSoftwareBitmapToFile)]

Вы можете задать дополнительные параметры кодирования при создании **BitmapEncoder**. Для этого создайте новый объект [**BitmapPropertySet**](https://msdn.microsoft.com/library/windows/apps/hh974338) и заполните его одним или несколькими объектами [**BitmapTypedValue**](https://msdn.microsoft.com/library/windows/apps/hh700687), представляющими параметры кодировщика. Список поддерживаемых параметров кодировщика см. в разделе [Справочник по параметрам BitmapEncoder](bitmapencoder-options-reference.md).

[!code-cs[UseEncodingOptions](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetUseEncodingOptions)]

## <a name="use-softwarebitmap-with-a-xaml-image-control"></a>Использование SoftwareBitmap с элементом управления изображением XAML

Чтобы отобразить изображение на странице XAML с помощью элемента управления [**Image**](https://msdn.microsoft.com/library/windows/apps/br242752), сначала найдите элемент управления **Image** на своей странице XAML.

[!code-xml[ImageControl](./code/ImagingWin10/cs/MainPage.xaml#SnippetImageControl)]

В настоящее время элемент управления **Image** поддерживает только изображения с кодированием BGRA8 и альфа-каналом с предварительным умножением (или без альфа-канала). Перед попыткой отображения изображения убедитесь, что оно имеет правильный формат; если формат неправильный, используйте статический метод [**Convert**](https://msdn.microsoft.com/library/windows/apps/dn887362) объекта **SoftwareBitmap**, чтобы преобразовать изображение в поддерживаемый формат.

Создайте новый объект [**SoftwareBitmapSource**](https://msdn.microsoft.com/library/windows/apps/dn997854). Задайте содержимое исходного объекта, вызвав [**SetBitmapAsync**](https://msdn.microsoft.com/library/windows/apps/dn997856), передающий **SoftwareBitmap**. Затем вы можете задать свойство [**Source**](https://msdn.microsoft.com/library/windows/apps/br242760) элемента управления **Image** для созданного **SoftwareBitmapSource**.

[!code-cs[SoftwareBitmapToWriteableBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSoftwareBitmapToWriteableBitmap)]

Также можно использовать **SoftwareBitmapSource**, чтобы задать **SoftwareBitmap** в качестве [**ImageSource**](https://msdn.microsoft.com/library/windows/apps/br210105) для [**ImageBrush**](https://msdn.microsoft.com/library/windows/apps/br210101).

## <a name="create-a-softwarebitmap-from-a-writeablebitmap"></a>Создание SoftwareBitmap из WriteableBitmap

Можно создать **SoftwareBitmap** из существующего **WriteableBitmap**. Для этого вызовите метод [**SoftwareBitmap.CreateCopyFromBuffer**](https://msdn.microsoft.com/library/windows/apps/dn887370) и укажите свойство **PixelBuffer** объекта **WriteableBitmap**, чтобы задать пиксельные данные. Второй аргумент позволяет запрашивать формат пикселей для созданного **WriteableBitmap**. Можно использовать свойства [**PixelWidth**](https://msdn.microsoft.com/library/windows/apps/br243253) и [**PixelHeight**](https://msdn.microsoft.com/library/windows/apps/br243251) элемента **WriteableBitmap**, чтобы указать размеры нового изображения.

[!code-cs[WriteableBitmapToSoftwareBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetWriteableBitmapToSoftwareBitmap)]

## <a name="create-or-edit-a-softwarebitmap-programmatically"></a>Программное создание или изменение SoftwareBitmap

До сих пор в этом разделе рассматривалась работа с файлами изображений. Однако вы также можете создать **SoftwareBitmap** с помощью программного кода и использовать этот же способ для получения доступа к пиксельным данным **SoftwareBitmap** и для их изменения.

**SoftwareBitmap** использует COM-взаимодействие для отображения необработанного буфера, который содержит пиксельные данные.

Для использования COM-взаимодействия необходимо включить ссылку в пространство имен **System.Runtime.InteropServices** вашего проекта.

[!code-cs[InteropNamespace](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetInteropNamespace)]

Инициализируйте COM-интерфейс [**IMemoryBufferByteAccess**](https://msdn.microsoft.com/library/windows/desktop/mt297505), добавив следующий код в ваше пространство имен.

[!code-cs[COMImport](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCOMImport)]

Создайте **SoftwareBitmap** с требуемым форматом пикселей и размером. Можно также использовать существующий **SoftwareBitmap**, для которого нужно изменить пиксельные данные. Вызовите метод [**SoftwareBitmap.LockBuffer**](https://msdn.microsoft.com/library/windows/apps/dn887380), чтобы получить экземпляр класса [**BitmapBuffer**](https://msdn.microsoft.com/library/windows/apps/dn887325), представляющий буфер пиксельных данных. Приведите объект **BitmapBuffer** в COM-интерфейс объекта **IMemoryBufferByteAccess**, а затем вызовите метод [**IMemoryBufferByteAccess.GetBuffer**](https://msdn.microsoft.com/library/windows/desktop/mt297506), чтобы заполнить массив байтов данными. Используя метод [**BitmapBuffer.GetPlaneDescription**](https://msdn.microsoft.com/library/windows/apps/dn887330), получите объект [**BitmapPlaneDescription**](https://msdn.microsoft.com/library/windows/apps/dn887342), который поможет вычислить смещение в буфер для каждого пикселя.

[!code-cs[CreateNewSoftwareBitmap](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateNewSoftwareBitmap)]

Поскольку этот метод получает доступ к необработанному буферу, который лежит в основе типов среды выполнения Windows, его необходимо объявить с помощью ключевого слова **unsafe**. Необходимо также настроить проект в Microsoft Visual Studio, чтобы разрешить компиляцию небезопасного кода. Для этого откройте страницу проекта **Свойства**, щелкните страницу свойств **Сборка** и установите флажок **Разрешить небезопасный код**.

## <a name="create-a-softwarebitmap-from-a-direct3d-surface"></a>Создание SoftwareBitmap с поверхности Direct3D

Для создания объекта **SoftwareBitmap** с поверхности Direct3D необходимо включить в проект пространство имен [**Windows.Graphics.DirectX.Direct3D11**](https://msdn.microsoft.com/library/windows/apps/dn895104).

[!code-cs[Direct3DNamespace](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetDirect3DNamespace)]

Вызовите [**CreateCopyFromSurfaceAsync**](https://msdn.microsoft.com/library/windows/apps/dn887373), чтобы создать новый объект **SoftwareBitmap** с поверхности. Как указано в имени, новый **SoftwareBitmap** имеет отдельную копию данных изображения. Изменения в **SoftwareBitmap** не будут влиять на поверхность Direct3D.

[!code-cs[CreateSoftwareBitmapFromSurface](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetCreateSoftwareBitmapFromSurface)]

## <a name="convert-a-softwarebitmap-to-a-different-pixel-format"></a>Преобразование SoftwareBitmap в другой формат пикселей

Класс **SoftwareBitmap** предоставляет статический метод [**Convert**](https://msdn.microsoft.com/library/windows/apps/dn887362), который позволяет легко создать **SoftwareBitmap**, использующий формат пикселей и режим альфа-канала, указанные вами в существующем **SoftwareBitmap**. Обратите внимание, что созданный точечный рисунок содержит отдельную копию данных изображения. Изменения нового точечного рисунка не будут влиять на исходный файл.

[!code-cs[Convert](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetConvert)]

## <a name="transcode-an-image-file"></a>Перекодирование файла изображения

Вы можете перекодировать файл изображения непосредственно из [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/br226176) в [**BitmapEncoder**](https://msdn.microsoft.com/library/windows/apps/br226206). Создайте [**IRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/br241731) из файла для перекодирования. Создайте **BitmapDecoder** из входного потока. Создайте [**InMemoryRandomAccessStream**](https://msdn.microsoft.com/library/windows/apps/br241720) для записи кодировщиком и вызовите метод [**BitmapEncoder.CreateForTranscodingAsync**](https://msdn.microsoft.com/library/windows/apps/br226214), передав поток в памяти и объект декодера. Возможности кодирования не поддерживаются при перекодировании; вместо этого необходимо использовать команду [**CreateAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.createasync). Все свойства в файле изображения, специально не заданные в кодировщике, будут записаны в выходной файл без изменений. Вызовите метод [**FlushAsync**](https://msdn.microsoft.com/library/windows/apps/br226216), чтобы кодировщик выполнил кодирование в поток в памяти. Наконец, обратите поток файла и поток в памяти в начало и вызовите метод [**CopyAsync**](https://msdn.microsoft.com/library/windows/apps/hh701827), чтобы записать поток в памяти за пределами потока файла.

[!code-cs[TranscodeImageFile](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetTranscodeImageFile)]

## <a name="related-topics"></a>Статьи по теме

* [Справочник по параметрам BitmapEncoder](bitmapencoder-options-reference.md)
* [Метаданные изображения](image-metadata.md)
 

 




