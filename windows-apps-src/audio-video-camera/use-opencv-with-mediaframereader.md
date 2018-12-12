---
ms.assetid: ''
description: В этой статье рассказывается, как использовать библиотеку компьютерного зрения с открытым исходным кодом (OpenCV) с классом MediaFrameReader.
title: Используйте OpenCV с MediaFrameReader
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, openCV
ms.localizationpriority: medium
ms.openlocfilehash: a603899776879cb7c8dc2439c3c22906db0b8038
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8938665"
---
# <a name="use-the-open-source-computer-vision-library-opencv-with-mediaframereader"></a>Использование библиотеки компьютерного зрения с открытым исходным кодом (OpenCV) с MediaFrameReader

В этой статье рассказывается, как использовать библиотеку компьютерного зрения с открытым исходным кодом (OpenCV), библиотеку собственного кода, которая предоставляет широкий набор алгоритмов обработки изображений с классом [**MediaFrameReader**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader), который может считывать мультимедиа-кадры из нескольких источников одновременно. В примере кода в этой статье рассматривается создание простого приложения, которое получает кадры с цветной камеры, размывает каждый кадр, используя библиотеку OpenCV, и затем отображает обработанное изображение в элементе управления XAML **Image**. 

>[!NOTE]
>OpenCV.Win.Core и OpenCV.Win. ImgProc не обновляются регулярно, но по-прежнему рекомендуются для создания OpenCVHelper, как описано на этой странице.

Эта статья основана на содержимом двух других статей:

* [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md) — в этой статье приводятся подробные сведения по использованию **MediaFrameReader** для получения кадров из одного или нескольких источников мультимедиа-кадров и подробно рассматривается большая часть кода примера в данной статье. В частности, в разделе **Обработка мультимедиа-кадров с помощью MediaFrameReader** содержится код для вспомогательного класса **FrameRenderer**, который обрабатывает представление мультимедиа-кадров в элементе XAML **Image**. В примере кода в этой статье также используется этот вспомогательный класс.

* [Обработка программных точечных рисунков с помощью OpenCV](process-software-bitmaps-with-opencv.md) — в этой статье подробно рассматривается создание компонента среды выполнения Windows на основе собственного кода **OpenCVBridge**, который позволяет выполнять преобразование объектов **SoftwareBitmap**, используемых **MediaFrameReader**, в тип **Mat**, используемый библиотекой OpenCV. В примере кода в этой статье предполагается, что уже выполнены действия по добавлению компонента **OpenCVBridge** в решение приложения UWP.

Помимо этих статей, чтобы просмотреть и скачать полный рабочий пример сценария, описанного в этой статье, см. раздел [Пример кадров камеры + OpenCV](https://go.microsoft.com/fwlink/?linkid=854003) в репозитории универсальных примеров Windows на GitHub.

Для начала разработки быстро, вы можете включить библиотеку OpenCV в проект приложения UWP с помощью пакетов NuGet, но эти пакеты не может пройти процесс certficication приложения, при отправке приложения в Store, поэтому рекомендуется загрузить OpenCV Библиотека исходный код и сборки двоичных файлов самостоятельно перед отправкой своего приложения. Сведения по разработке с помощью OpenCV можно найти на сайте [http://opencv.org](http://opencv.org)


## <a name="implement-the-opencvhelper-native-windows-runtime-component"></a>Реализация компонента среды выполнения Windows с собственным кодом OpenCVHelper
Выполните действия, описанные в разделе [Обработка программных точечных рисунков с помощью OpenCV](process-software-bitmaps-with-opencv.md), для создания вспомогательного компонента среды выполнения Windows OpenCV и добавьте ссылку на проект компонента в ваше решение для приложения UWP.

## <a name="find-available-frame-source-groups"></a>Поиск доступных групп источников кадров
Во-первых, вам потребуется найти группу источников мультимедиа-кадров, из которой будут поступать мультимедиа-кадры. Получите список доступных групп источников на текущем устройстве, вызвав метод **[MediaFrameSourceGroup.FindAllAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.FindAllAsync)**. Затем выберите группы источников, которые предоставляют типы датчиков, необходимые для сценария работы вашего приложения. В этом примере нам нужна просто группа источников, предоставляющая кадры с RGB-камеры.

[!code-cs[OpenCVFrameSourceGroups](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameSourceGroups)]

## <a name="initialize-the-mediacapture-object"></a>Инициализация объекта MediaCapture
Далее нужно инициализировать объект **MediaCapture** для использования выбранной на предыдущем этапе группы источников кадров, задав свойство **[SourceGroup](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings.SourceGroup)** для **MediaCaptureInitializationSettings**.

> [!NOTE] 
> Прием, используемый компонентом OpenCVHelper, подробно описан в разделе [Обработка программных точечных рисунков с помощью OpenCV](process-software-bitmaps-with-opencv.md), и он требует, чтобы данные изображения находились в памяти ЦП, а не в памяти графического процессора. Поэтому следует задать **MemoryPreference.CPU** в поле **[MemoryPreference](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings.MemoryPreference)** объекта **MediaCaptureInitializationSettings**.

После инициализации объекта **MediaCapture** получите ссылку на источник RGB-кадров, обратившись к свойству **[MediaCapture.FrameSources](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.FrameSources)**.

[!code-cs[OpenCVInitMediaCapture](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVInitMediaCapture)]

## <a name="initialize-the-mediaframereader"></a>Инициализация MediaFrameReader
Затем создайте [**MediaFrameReader**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.Frames.MediaFrameReader) для источника RGB-кадров, полученного на предыдущем шаге. Для сохранения хорошей частоты кадров, возможно, потребуется обрабатывать кадры с более низким разрешением по сравнению с разрешением камеры. В этом примере приведен необязательный аргумент **[BitmapSize](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapsize)** метода **[MediaCapture.CreateFrameReaderAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.createframereaderasync)** для создания запроса на изменение размера кадров, предоставляемых средством чтения кадров, до значения 640x480 пикселей.

После создания средства чтения кадров зарегистрируйте обработчик для события **[FrameArrived](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.FrameArrived)**. Затем создайте новый объект **[SoftwareBitmapSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource)**, который вспомогательный класс **FrameRenderer** будет использовать для предоставления обработанных изображений. Затем вызовите конструктор для **FrameRenderer**. Инициализируйте экземпляр класса **OpenCVHelper**, определенный в компоненте среды выполнения Windows OpenCVBridge. Этот вспомогательный класс используется в обработчике **FrameArrived** для обработки каждого кадра. Наконец, запустите средство чтения кадров вызовом метода **[StartAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.StartAsync)**.

[!code-cs[OpenCVFrameReader](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameReader)]


## <a name="handle-the-framearrived-event"></a>Обработка события FrameArrived
Событие **FrameArrived** создается при поступлении каждого нового кадра из средства чтения кадров. Вызовите метод **[TryAcquireLatestFrame](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.TryAcquireLatestFrame)** для получения кадра, если он существует. Получите **SoftwareBitmap** из **[MediaFrameReference](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereference)**. Обратите внимание, что класс **CVHelper**, используемый в этом примере, требует, чтобы изображения использовали формат пикселей BRGA8 с предварительным умножением альфа-канала. Если кадр, переданный данному событию, имеет другой формат, преобразуйте **SoftwareBitmap** в правильный формат. Затем создайте **SoftwareBitmap** для использования в качестве целевого объекта операции размытия. Свойства источника изображения используются в качестве аргументов для конструктора в целях создания точечного рисунка соответствующего формата. Вызовите метод **Blur** вспомогательного класса для обработки кадра. Наконец, передайте полученное из операции размытия изображение в **PresentSoftwareBitmap**, метод вспомогательного класса **FrameRenderer**, который отображает изображение в элементе управления XAML **Image**, с которым он инициализирован.

[!code-cs[OpenCVFrameArrived](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameArrived)]

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md)
* [Обработка программных точечных рисунков с помощью OpenCV](process-software-bitmaps-with-opencv.md)
* [Пример кадров камеры](http://go.microsoft.com/fwlink/?LinkId=823230)
* [Пример кадров камеры и OpenCV](https://go.microsoft.com/fwlink/?linkid=854003)
 

 




