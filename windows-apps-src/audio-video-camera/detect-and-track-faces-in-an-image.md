---
ms.assetid: 84729E44-10E9-4D7D-8575-6A9D97467ECD
description: В этом разделе объясняется, как использовать FaceDetector для обнаружения лиц на изображении. Инструмент FaceTracker оптимизирован для отслеживания лиц с течением времени в последовательности видеокадров.
title: Обнаружение лиц на изображениях или в видео
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f1be694be412bbf0a4e076e8ac5753eefda74c55
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360912"
---
# <a name="detect-faces-in-images-or-videos"></a>Обнаружение лиц на изображениях или в видео



\[Некоторая информация имеет отношение к предварительному выпуску продукта, который может быть значительно изменен перед коммерческим выпуском. Майкрософт не предоставляет никаких гарантий, явных или подразумеваемых, относительно предоставленной здесь информации.\]

В этом разделе объясняется, как использовать [**FaceDetector**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.FaceDetector) для обнаружения лиц на изображении. Инструмент [**FaceTracker**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.FaceTracker) оптимизирован для отслеживания лиц с течением времени в последовательности видеокадров.

Сведения об альтернативном методе отслеживания лиц с помощью [**FaceDetectionEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffect) см. в разделе [Анализ сцен для захвата мультимедиа](scene-analysis-for-media-capture.md).

В этой статье используется код, адаптированный из примеров [Базовое обнаружение лиц](https://go.microsoft.com/fwlink/p/?LinkId=620512&clcid=0x409) и [Базовое отслеживание лиц](https://go.microsoft.com/fwlink/p/?LinkId=620513&clcid=0x409). Вы можете скачать эти примеры, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки вашего приложения.

## <a name="detect-faces-in-a-single-image"></a>Обнаружение лиц на одном изображении

Класс [**FaceDetector**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.FaceDetector) позволяет обнаружить одно или несколько лиц на неподвижном изображении.

В этом примере используется API из следующих пространств имен.

[!code-cs[FaceDetectionUsing](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetFaceDetectionUsing)]

Объявите переменную-член класса для объекта [**FaceDetector**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.FaceDetector) и для списка объектов [**DetectedFace**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.DetectedFace), которые будут обнаружены на изображении.

[!code-cs[ClassVariables1](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetClassVariables1)]

Обнаружение лиц выполняется в объекте [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap), который можно создать различными способами. В этом примере [**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) используется, чтобы пользователь мог выбрать файл изображения, в котором будут обнаруживаться лица. Дополнительные сведения о работе с точечными рисунками программного обеспечения см. в разделе [Обработка изображений](imaging.md).

[!code-cs[Picker](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetPicker)]

Используйте класс [**BitmapDecoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) для декодирования файла изображения в **SoftwareBitmap**. Процесс обнаружения лиц проходит быстрее на меньшем изображении, поэтому вы, возможно, захотите масштабировать исходное изображение до меньшего размера. Это можно выполнить во время декодирования, создав объект [**BitmapTransform**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapTransform), задав свойства [**ScaledWidth**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmaptransform.scaledwidth) и [**ScaledHeight**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmaptransform.scaledheight) и передав его вызову [**GetSoftwareBitmapAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.getsoftwarebitmapasync), который возвращает декодированный и масштабированный **SoftwareBitmap**.

[!code-cs[Decode](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetDecode)]

В текущей версии класс **FaceDetector** поддерживает только изображения в формате Gray8 или Nv12. Класс **SoftwareBitmap** предоставляет метод [**Convert**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.softwarebitmap.windows), который преобразует точечный рисунок из одного формата в другой. В этом примере исходное изображение преобразовывается в формат пикселей Gray8, если оно еще не в этом формате. При желании для того, чтобы определить во время выполнения, поддерживается ли формат пикселей, можно использовать методы [**GetSupportedBitmapPixelFormats**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.facedetector.getsupportedbitmappixelformats) и [**IsBitmapPixelFormatSupported**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.facedetector.isbitmappixelformatsupported) на случай, если набор поддерживаемых форматов будет расширяться в будущих версиях.

[!code-cs[Format](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetFormat)]

Создайте экземпляр объекта **FaceDetector**, вызвав [**CreateAsync**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.facedetector.createasync), а затем вызовите [**DetectFacesAsync**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.facedetector.detectfacesasync), передав точечный рисунок, который был масштабирован до оптимального размера и преобразован в поддерживаемый формат пикселей. Этот метод возвращает список объектов [**DetectedFace**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.DetectedFace). **ShowDetectedFaces** — это вспомогательный метод, показанный ниже, который рисует квадраты вокруг лиц на изображении.

[!code-cs[Detect](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetDetect)]

Не забудьте удалить объекты, созданные во время процесса обнаружения лиц.

[!code-cs[Dispose](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetDispose)]

Чтобы отобразить изображение и нарисовать рамки вокруг обнаруженных лиц, добавьте на страницу XAML элемент [**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas).

[!code-xml[Canvas](./code/FaceDetection_Win10/cs/MainPage.xaml#SnippetCanvas)]

Определите несколько переменных-членов, чтобы оформить квадраты, которые будут нарисованы.

[!code-cs[ClassVariables2](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetClassVariables2)]

Во вспомогательном методе **ShowDetectedFaces** создается новый элемент [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush), а источник задается как [**SoftwareBitmapSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Imaging.SoftwareBitmapSource), созданный из **SoftwareBitmap**, который представляет исходное изображение. Фон элемента управления XAML **Canvas** задается как кисть изображения.

Если список лиц, переданный во вспомогательный метод, не пуст, пройдите по всем лицам в списке и используйте свойство [**FaceBox**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.detectedface.facebox) класса [**DetectedFace**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.DetectedFace), чтобы определить положение и размер прямоугольника в изображении, которое содержит лицо. Так как размер элемента управления **Canvas**, вероятно, будет отличаться от размера исходного изображения, необходимо умножить координаты X и Y, а также ширину и высоту **FaceBox** на значение масштабирования, которое представляет собой соотношение размера исходного изображения к фактическому размеру элемента управления **Canvas**.

[!code-cs[ShowDetectedFaces](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetShowDetectedFaces)]

## <a name="track-faces-in-a-sequence-of-frames"></a>Отслеживание лиц в последовательности кадров

Если вы хотите обнаружить лица в видео, эффективней использовать класс [**FaceTracker**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.FaceTracker) вместо класса [**FaceDetector**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.FaceDetector), хотя действия по реализации очень похожи. **FaceTracker** использует сведения о ранее обработанных кадрах для оптимизации процесса обнаружения.

[!code-cs[FaceTrackingUsing](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetFaceTrackingUsing)]

Объявите переменную класса для объекта **FaceTracker**. В этом примере для запуска отслеживания лиц на определенный интервал используется [**ThreadPoolTimer**](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPoolTimer). [SemaphoreSlim](https://docs.microsoft.com/dotnet/api/system.threading.semaphoreslim?redirectedfrom=MSDN) используется для обеспечения того, что в определенный момент выполняется только одна операция отслеживания лиц.

[!code-cs[ClassVariables3](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetClassVariables3)]

Чтобы инициализировать операцию отслеживания лиц, создайте новый объект **FaceTracker**, вызвав [**CreateAsync**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.facetracker.createasync). Инициализируйте нужный интервал таймера, а затем создайте таймер. Вспомогательный метод **ProcessCurrentVideoFrame** будет вызываться каждый раз по истечении заданного интервала.

[!code-cs[TrackingInit](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetTrackingInit)]

Вспомогательный метод **ProcessCurrentVideoFrame** вызывается таймером асинхронно, поэтому метод сначала вызывает метод **Wait** семафора, чтобы убедиться, что операция отслеживания выполняется, и если это так, метод возвращает значение, не пытаясь обнаружить лица. В конце этого метода вызывается метод **Release** семафора, который делает возможным последующий вызов **ProcessCurrentVideoFrame** для продолжения.

Класс [**FaceTracker**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.FaceTracker) выполняется в объектах [**VideoFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.VideoFrame). Существует несколько способов получить **VideoFrame**, в том числе захватив кадр предварительного просмотра из запущенного объекта [MediaCapture](capture-photos-and-video-with-mediacapture.md) или реализовав метод [**ProcessFrame**](https://docs.microsoft.com/uwp/api/windows.media.effects.ibasicaudioeffect.processframe) элемента [**IBasicVideoEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Effects.IBasicVideoEffect). В этом примере используется неопределенный вспомогательный метод, который возвращает видеокадр, **GetLatestFrame**, в качестве заполнителя для этой операции. Подробнее о получении видеокадров из потока предварительного просмотра на запущенном устройстве захвата мультимедиа см. в разделе [Получение кадра предварительного просмотра](get-a-preview-frame.md).

Как и **FaceDetector**, **FaceTracker** поддерживает ограниченный набор форматов пикселей. В этом примере обнаружение лиц отменяется, если формат предоставленного кадра — не Nv12.

Вызовите [**ProcessNextFrameAsync**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.facetracker.processnextframeasync), чтобы извлечь список объектов [**DetectedFace**](https://docs.microsoft.com/uwp/api/Windows.Media.FaceAnalysis.DetectedFace), представляющих лица в кадре. Когда у вас будет список лиц, их можно отображать тем же образом, который описан выше для обнаружения лиц. Обратите внимание, что так как вспомогательный метод отслеживания лиц не вызывается в потоке пользовательского интерфейса, все обновления пользовательского интерфейса необходимо выполнять внутри вызова [**CoredDispatcher.RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.windows).

[!code-cs[ProcessCurrentVideoFrame](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetProcessCurrentVideoFrame)]

## <a name="related-topics"></a>См. также

* [Анализ сцены для записи мультимедиа](scene-analysis-for-media-capture.md)
* [Пример простого обнаружение лиц](https://go.microsoft.com/fwlink/p/?LinkId=620512&clcid=0x409)
* [Пример простого отслеживания лиц](https://go.microsoft.com/fwlink/p/?LinkId=620513&clcid=0x409)
* [Камера](camera.md)
* [Основные фото, видео и аудио захвата с MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Воспроизведение мультимедиа](media-playback.md)
