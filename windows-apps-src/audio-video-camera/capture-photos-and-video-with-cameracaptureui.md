---
author: drewbatgit
ms.assetid: CC0D6E9B-128D-488B-912F-318F5EE2B8D3
description: "В этой статье описано, как использовать класс CameraCaptureUI, чтобы фотографировать и снимать видео с помощью пользовательского интерфейса камеры, встроенного в Windows."
title: "Фото- и видеосъемка с использованием встроенного пользовательского интерфейса камеры в Windows"
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 7be9a38bdb4d9489c08cd53c5b24348e16d7a74f
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="capture-photos-and-video-with-windows-built-in-camera-ui"></a>Фото- и видеосъемка с использованием встроенного пользовательского интерфейса камеры в Windows

\[ Обновлено для приложений UWP в Windows10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этой статье описано, как использовать класс CameraCaptureUI, чтобы фотографировать и снимать видео с помощью пользовательского интерфейса камеры, встроенного в Windows. Эта функция проста в использовании и позволяет приложению получать захваченные пользователем фотографии и видео с помощью всего нескольких строк кода.

Если вы хотите предоставить собственный пользовательский интерфейс камеры или вам требуется более надежное низкоуровневое управление операцией захвата, необходимо использовать объект [**MediaCapture**](https://msdn.microsoft.com/library/windows/apps/br241124) и внедрить собственный способ захвата. Подробнее см. в статье [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md).

> [!NOTE]
> Не следует указывать в файле манифеста вашего приложения возможность использования **веб-камеры** или **микрофона**, если используется CameraCaptureUI. Если вы это сделаете, ваше приложение будет отображаться в параметрах конфиденциальности камеры устройства, но даже если пользователь запретит приложению доступ к камере, это не помешает CameraCaptureUI захватывать мультимедиа. Это обусловлено тем, что встроенное приложение камеры Windows является доверенным основным приложением, которое требует, чтобы пользователь инициировал захват фото, звука и видео нажатием кнопки. Ваше приложение может не пройти сертификацию WACK (Комплекта сертификации Windows-приложений) при отправке в Магазин, если указать возможность использования веб-камеры или микрофона при использовании CameraCaptureUI.
> Вы должны указать в файле манифеста вашего приложения возможность использования веб-камеры или микрофона, если для программного захвата звука, фото или видео используется MediaCapture.

## <a name="capture-a-photo-with-cameracaptureui"></a>Захват фотографий с помощью CameraCaptureUI

Чтобы применять пользовательский интерфейс захвата с камеры, добавьте в свой проект пространство имен [**Windows.Media.Capture**](https://msdn.microsoft.com/library/windows/apps/br226738). Чтобы выполнять файловые операции с возвращенными файлами изображений, включите в проект пространство имен [**Windows.Storage**](https://msdn.microsoft.com/library/windows/apps/br227346).

[!code-cs[UsingCaptureUI](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingCaptureUI)]

Чтобы захватить фотографию, создайте новый объект [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/br241030). С помощью свойства [**PhotoSettings**](https://msdn.microsoft.com/library/windows/apps/br241058) объекта можно указать параметры возвращенной фотографии, например ее формат. По умолчанию пользовательский интерфейс захвата с камеры позволяет обрезать фотографии перед их возвращением. Эту функцию можно отключить с помощью свойства [**AllowCropping**](https://msdn.microsoft.com/library/windows/apps/br241042). В этом примере свойство [**CroppedSizeInPixels**](https://msdn.microsoft.com/library/windows/apps/br241044) используется для возвращения изображений с разрешением 200x200 пикселей.

> [!NOTE]
> Обрезка изображения в **CameraCaptureUI** не поддерживается для устройств из семейства мобильных устройств. Значение свойства [**AllowCropping**](https://msdn.microsoft.com/library/windows/apps/br241042) игнорируется, если приложение выполняется на этих устройствах.

Вызовите метод [**CaptureFileAsync**](https://msdn.microsoft.com/library/windows/apps/br241057) и задайте [**CameraCaptureUIMode.Photo**](https://msdn.microsoft.com/library/windows/apps/br241040), чтобы захватить фотографию. Если захват выполнен успешно, этот метод возвращает экземпляр [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), содержащий изображение. Если пользователь отменил захват, возвращается объект null.

[!code-cs[CapturePhoto](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCapturePhoto)]

Файл **StorageFile**, содержащий фотографию, получает динамически генерируемое имя и сохраняется в локальной папке приложения. Чтобы упорядочить снятые фотографии, их можно переместить файл в другой папке.

[!code-cs[CopyAndDeletePhoto](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCopyAndDeletePhoto)]

Чтобы использовать фотографию в приложении, можно создать объект [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358), который можно использовать с различными функциями универсального приложения для Windows.

Сначала необходимо добавить в проект пространство имен [**Windows.Graphics.Imaging**](https://msdn.microsoft.com/library/windows/apps/br226400).

[!code-cs[UsingSoftwareBitmap](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingSoftwareBitmap)]

Вызовите метод [**OpenAsync**](https://msdn.microsoft.com/library/windows/apps/br227116) для доступа к потоку файла изображения. Вызовите [**BitmapDecoder.CreateAsync**](https://msdn.microsoft.com/library/windows/apps/br226182), чтобы получить доступ к декодеру точечных рисунков для потока. Затем вызовите метод [**GetSoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887332), чтобы получить доступ к изображению в виде **SoftwareBitmap**.

[!code-cs[SoftwareBitmap](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetSoftwareBitmap)]

Чтобы показать изображение в пользовательском интерфейсе, объявите элемент управления [**Image**](https://msdn.microsoft.com/library/windows/apps/br242752) на странице XAML.

[!code-xml[ImageControl](./code/CameraCaptureUIWin10/cs/MainPage.xaml#SnippetImageControl)]

Чтобы использовать точечный рисунок программного обеспечения на странице XAML, добавьте в свой проект пространство имен [**Windows.UI.Xaml.Media.Imaging**](https://msdn.microsoft.com/library/windows/apps/br243258) using.

[!code-cs[UsingSoftwareBitmapSource](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingSoftwareBitmapSource)]

Элементу управления **Image** требуется, чтобы источник изображения был в формате BGRA8 с предварительным умножением альфа-канала или без него, поэтому вызовите статистический метод [**SoftwareBitmap.Convert**](https://msdn.microsoft.com/library/windows/apps/dn887362) для создания нового точечного рисунка программного обеспечения в требуемом формате. Затем создайте новый объект [**SoftwareBitmapSource**](https://msdn.microsoft.com/library/windows/apps/dn997854) и вызовите метод [**SetBitmapAsync**](https://msdn.microsoft.com/library/windows/apps/dn997856), чтобы присвоить источнику точечный рисунок программного обеспечения. После этого задайте свойство [**Source**](https://msdn.microsoft.com/library/windows/apps/br242760) элемента управления **Image**, чтобы отобразить захваченную фотографию в пользовательском интерфейсе.

[!code-cs[SetImageSource](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetSetImageSource)]

## <a name="capture-a-video-with-cameracaptureui"></a>Видеозахват с помощью CameraCaptureUI

Чтобы захватить видео, создайте новый объект [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/br241030). С помощью свойства [**VideoSettings**](https://msdn.microsoft.com/library/windows/apps/br241059) объекта можно указать параметры возвращенного видео, например его формат.

Вызовите метод [**CaptureFileAsync**](https://msdn.microsoft.com/library/windows/apps/br241057) и задайте [**Video**](https://msdn.microsoft.com/library/windows/apps/br241059), чтобы осуществить видеозахват. Если захват выполнен успешно, этот метод возвращает экземпляр [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), содержащий видео. Если пользователь отменил захват, возвращается объект null.

[!code-cs[CaptureVideo](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetCaptureVideo)]

Доступные действия с захваченным видеофайлом зависят от сценария вашего приложения. Далее в этой статье объясняется, как быстро создать композицию мультимедиа из одного или нескольких захваченных видео и отобразить ее в пользовательском интерфейсе.

Сначала добавьте элемент управления [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), в котором композиция видеороликов будет отображаться на вашей странице XAML.

[!code-xml[MediaElement](./code/CameraCaptureUIWin10/cs/MainPage.xaml#SnippetMediaElement)]

Добавьте в проект пространства имен [**Windows.Media.Editing**](https://msdn.microsoft.com/library/windows/apps/dn640565) и [**Windows.Media.Core**](https://msdn.microsoft.com/library/windows/apps/dn278962).


[!code-cs[UsingMediaComposition](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetUsingMediaComposition)]

Объявите переменные-члены для объекта [**MediaComposition**](https://msdn.microsoft.com/library/windows/apps/dn652646) и [**MediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn282716), которые нужно оставить в области на время существования страницы.

[!code-cs[DeclareMediaComposition](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetDeclareMediaComposition)]

Прежде чем осуществлять видеозахват, создайте новый экземпляр класса **MediaComposition**.

[!code-cs[InitComposition](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetInitComposition)]

Создайте объект [**MediaClip**](https://msdn.microsoft.com/library/windows/apps/dn652596) с помощью видеофайла, возвращенного пользовательским интерфейсом захвата с камеры, вызвав метод [**MediaClip.CreateFromFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn652607). Добавьте мультимедийный клип в коллекцию [**Clips**](https://msdn.microsoft.com/library/windows/apps/dn652648) композиции.

Вызовите метод [**GeneratePreviewMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn652674), чтобы создать из композиции объект **MediaStreamSource**.

[!code-cs[AddToComposition](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetAddToComposition)]

Затем задайте для источника потока использование метода [**SetMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn299029) элемента мультимедиа, чтобы отобразить композицию в пользовательском интерфейсе.

[!code-cs[SetMediaElementSource](./code/CameraCaptureUIWin10/cs/MainPage.xaml.cs#SnippetSetMediaElementSource)]

Вы можете продолжать записывать видеоклипы и добавлять их в композицию. Дополнительные сведения о композициях мультимедиа см. в статье [Создание и редактирование мультимедиа](media-compositions-and-editing.md).

> [!NOTE] 
> Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

## <a name="related-topics"></a>Еще по теме

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [**CameraCaptureUI**](https://msdn.microsoft.com/library/windows/apps/br241030) 
 

 




