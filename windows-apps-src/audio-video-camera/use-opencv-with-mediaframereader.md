---
ms.assetid: ''
description: В этой статье рассказывается, как использовать библиотеку компьютерного зрения с открытым исходным кодом (OpenCV) с классом MediaFrameReader.
title: Используйте OpenCV с MediaFrameReader
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, openCV
ms.localizationpriority: medium
ms.openlocfilehash: e5a1993ea4808cabf9f82640f03f0187d431f3d2
ms.sourcegitcommit: d38e2f31c47434cd6dbbf8fe8d01c20b98fabf02
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393510"
---
# <a name="use-the-open-source-computer-vision-library-opencv-with-mediaframereader"></a>Использование библиотеки компьютерного зрения с открытым исходным кодом (OpenCV) с MediaFrameReader

В этой статье рассказывается, как использовать библиотеку компьютерного зрения с открытым исходным кодом (OpenCV), библиотеку собственного кода, которая предоставляет широкий набор алгоритмов обработки изображений с классом [**MediaFrameReader**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader), который может считывать мультимедиа-кадры из нескольких источников одновременно. В примере кода в этой статье рассматривается создание простого приложения, которое получает кадры с цветной камеры, размывает каждый кадр, используя библиотеку OpenCV, и затем отображает обработанное изображение в элементе управления XAML **Image**. 

>[!NOTE]
>OpenCV.Win.Core и OpenCV.Win. ImgProc не обновляются регулярно, но по-прежнему рекомендуются для создания OpenCVHelper, как описано на этой странице.

Эта статья основана на содержимом двух других статей:

* [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md) — в этой статье приводятся подробные сведения по использованию **MediaFrameReader** для получения кадров из одного или нескольких источников мультимедиа-кадров и подробно рассматривается большая часть кода примера в данной статье. В частности, в разделе **Обработка мультимедиа-кадров с помощью MediaFrameReader** содержится код для вспомогательного класса **FrameRenderer**, который обрабатывает представление мультимедиа-кадров в элементе XAML **Image**. В примере кода в этой статье также используется этот вспомогательный класс.

* [Обработка точечных рисунков программного обеспечения с помощью opencv](process-software-bitmaps-with-opencv.md) . в этой статье рассматривается создание машинного кода среда выполнения Windows компоненте, **опенквбридже**, который помогает выполнять преобразование между объектом **софтваребитмап** , используемым **медиафрамереадер** и тип **материалов** , используемый библиотекой opencv. В примере кода в этой статье предполагается, что уже выполнены действия по добавлению компонента **OpenCVBridge** в решение приложения UWP.

Помимо этих статей, чтобы просмотреть и скачать полный рабочий пример сценария, описанного в этой статье, см. раздел [Пример кадров камеры + OpenCV](https://go.microsoft.com/fwlink/?linkid=854003) в репозитории универсальных примеров Windows на GitHub.

Чтобы быстро приступить к разработке, можно включить библиотеку OpenCV в проект приложения UWP с помощью пакетов NuGet, но эти пакеты могут не передать процесс цертфиЦикатион приложения при отправке приложения в магазин, поэтому рекомендуется скачать OpenCV исходный код библиотеки и самостоятельное создание двоичных файлов перед отправкой приложения. Сведения по разработке с помощью OpenCV можно найти на сайте [https://opencv.org](https://opencv.org)


## <a name="implement-the-opencvhelper-native-windows-runtime-component"></a>Реализация компонента Опенквхелпер Native среда выполнения Windows
Выполните действия, описанные в разделе [Обработка точечных рисунков программного обеспечения с помощью opencv](process-software-bitmaps-with-opencv.md) , чтобы создать вспомогательный компонент opencv среда выполнения Windows и добавить ссылку на проект компонента в решение приложения UWP.

## <a name="find-available-frame-source-groups"></a>Поиск доступных групп источников кадров
Во-первых, вам потребуется найти группу источников мультимедиа-кадров, из которой будут поступать мультимедиа-кадры. Получите список доступных групп источников на текущем устройстве, вызвав метод **[MediaFrameSourceGroup.FindAllAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.FindAllAsync)** . Затем выберите группы источников, которые предоставляют типы датчиков, необходимые для сценария работы вашего приложения. В этом примере нам нужна просто группа источников, предоставляющая кадры с RGB-камеры.

[!code-cs[OpenCVFrameSourceGroups](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameSourceGroups)]

## <a name="initialize-the-mediacapture-object"></a>Инициализация объекта MediaCapture
Далее нужно инициализировать объект **MediaCapture** для использования выбранной на предыдущем этапе группы источников кадров, задав свойство **[SourceGroup](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings.SourceGroup)** для **MediaCaptureInitializationSettings**.

> [!NOTE] 
> Прием, используемый компонентом OpenCVHelper, подробно описан в разделе [Обработка программных точечных рисунков с помощью OpenCV](process-software-bitmaps-with-opencv.md), и он требует, чтобы данные изображения находились в памяти ЦП, а не в памяти графического процессора. Поэтому следует задать **MemoryPreference.CPU** в поле **[MemoryPreference](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacaptureinitializationsettings.MemoryPreference)** объекта **MediaCaptureInitializationSettings**.

После инициализации объекта **MediaCapture** получите ссылку на источник RGB-кадров, обратившись к свойству **[MediaCapture.FrameSources](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.FrameSources)** .

[!code-cs[OpenCVInitMediaCapture](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVInitMediaCapture)]

## <a name="initialize-the-mediaframereader"></a>Инициализация MediaFrameReader
Затем создайте [**MediaFrameReader**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameReader) для источника RGB-кадров, полученного на предыдущем шаге. Для сохранения хорошей частоты кадров, возможно, потребуется обрабатывать кадры с более низким разрешением по сравнению с разрешением камеры. В этом примере приведен необязательный аргумент **[BitmapSize](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapsize)** метода **[MediaCapture.CreateFrameReaderAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.createframereaderasync)** для создания запроса на изменение размера кадров, предоставляемых средством чтения кадров, до значения 640 x 480 пикселей.

После создания средства чтения кадров зарегистрируйте обработчик для события **[FrameArrived](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.FrameArrived)** . Затем создайте новый объект **[SoftwareBitmapSource](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.imaging.softwarebitmapsource)** , который вспомогательный класс **FrameRenderer** будет использовать для предоставления обработанных изображений. Затем вызовите конструктор для **FrameRenderer**. Инициализируйте экземпляр класса **опенквхелпер** , определенного в компоненте Среда выполнения Windows опенквбридже. Этот вспомогательный класс используется в обработчике **FrameArrived** для обработки каждого кадра. Наконец, запустите средство чтения кадров вызовом метода **[StartAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.StartAsync)** .

[!code-cs[OpenCVFrameReader](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameReader)]


## <a name="handle-the-framearrived-event"></a>Обработка события FrameArrived
Событие **FrameArrived** создается при поступлении каждого нового кадра из средства чтения кадров. Вызовите метод **[TryAcquireLatestFrame](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereader.TryAcquireLatestFrame)** для получения кадра, если он существует. Получите **SoftwareBitmap** из **[MediaFrameReference](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframereference)** . Обратите внимание, что класс **CVHelper**, используемый в этом примере, требует, чтобы изображения использовали формат пикселей BRGA8 с предварительным умножением альфа-канала. Если кадр, переданный данному событию, имеет другой формат, преобразуйте **SoftwareBitmap** в правильный формат. Затем создайте **SoftwareBitmap** для использования в качестве целевого объекта операции размытия. Свойства источника изображения используются в качестве аргументов для конструктора в целях создания точечного рисунка соответствующего формата. Вызовите метод **Blur** вспомогательного класса для обработки кадра. Наконец, передайте полученное из операции размытия изображение в **PresentSoftwareBitmap**, метод вспомогательного класса **FrameRenderer**, который отображает изображение в элементе управления XAML **Image**, с которым он инициализирован.

[!code-cs[OpenCVFrameArrived](./code/Frames_Win10/Frames_Win10/MainPage.OpenCV.xaml.cs#SnippetOpenCVFrameArrived)]

## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Базовая фотография, видео и запись звука с помощью Медиакаптуре](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Обработка кадров мультимедиа с помощью Медиафрамереадер](process-media-frames-with-mediaframereader.md)
* [Обработка точечных рисунков программного обеспечения с помощью OpenCV](process-software-bitmaps-with-opencv.md)
* [Пример кадров камеры](https://go.microsoft.com/fwlink/?LinkId=823230)
* [Кадры камеры + пример OpenCV](https://go.microsoft.com/fwlink/?linkid=854003)
 

 




