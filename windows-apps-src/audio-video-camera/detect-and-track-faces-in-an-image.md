---
ms.assetid: 84729E44-10E9-4D7D-8575-6A9D97467ECD
description: В этом разделе объясняется, как использовать FaceDetector для обнаружения лиц на изображении. Инструмент FaceTracker оптимизирован для отслеживания лиц с течением времени в последовательности видеокадров.
title: Обнаружение лиц на изображениях или в видео
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 970fbcef984f28e779ea7c133de95f7f7f99be8d
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8469281"
---
# <a name="detect-faces-in-images-or-videos"></a>Обнаружение лиц на изображениях или в видео



\[Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.\]

В этом разделе объясняется, как использовать [**FaceDetector**](https://msdn.microsoft.com/library/windows/apps/dn974129) для обнаружения лиц на изображении. Инструмент [**FaceTracker**](https://msdn.microsoft.com/library/windows/apps/dn974150) оптимизирован для отслеживания лиц с течением времени в последовательности видеокадров.

Сведения об альтернативном методе отслеживания лиц с помощью [**FaceDetectionEffect**](https://msdn.microsoft.com/library/windows/apps/dn948776) см. в разделе [Анализ сцен для захвата мультимедиа](scene-analysis-for-media-capture.md).

В этой статье используется код, адаптированный из примеров [Базовое обнаружение лиц](http://go.microsoft.com/fwlink/p/?LinkId=620512&clcid=0x409) и [Базовое отслеживание лиц](http://go.microsoft.com/fwlink/p/?LinkId=620513&clcid=0x409). Вы можете скачать эти примеры, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки вашего приложения.

## <a name="detect-faces-in-a-single-image"></a>Обнаружение лиц на одном изображении

Класс [**FaceDetector**](https://msdn.microsoft.com/library/windows/apps/dn974129) позволяет обнаружить одно или несколько лиц на неподвижном изображении.

В этом примере используются API из следующих пространств имен.

[!code-cs[FaceDetectionUsing](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetFaceDetectionUsing)]

Объявите переменную-член класса для объекта [**FaceDetector**](https://msdn.microsoft.com/library/windows/apps/dn974129) и для списка объектов [**DetectedFace**](https://msdn.microsoft.com/library/windows/apps/dn974123), которые будут обнаружены на изображении.

[!code-cs[ClassVariables1](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetClassVariables1)]

Обнаружение лиц выполняется в объекте [**SoftwareBitmap**](https://msdn.microsoft.com/library/windows/apps/dn887358), который можно создать различными способами. В этом примере [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847) используется, чтобы пользователь мог выбрать файл изображения, в котором будут обнаруживаться лица. Дополнительные сведения о работе с точечными рисунками программного обеспечения см. в разделе [Обработка изображений](imaging.md).

[!code-cs[Picker](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetPicker)]

Используйте класс [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/br226176) для декодирования файла изображения в **SoftwareBitmap**. Процесс обнаружения лиц проходит быстрее на меньшем изображении, поэтому вы, возможно, захотите масштабировать исходное изображение до меньшего размера. Это можно выполнить во время декодирования, создав объект [**BitmapTransform**](https://msdn.microsoft.com/library/windows/apps/br226254), задав свойства [**ScaledWidth**](https://msdn.microsoft.com/library/windows/apps/br226261) и [**ScaledHeight**](https://msdn.microsoft.com/library/windows/apps/br226260) и передав его вызову [**GetSoftwareBitmapAsync**](https://msdn.microsoft.com/library/windows/apps/dn887332), который возвращает декодированный и масштабированный **SoftwareBitmap**.

[!code-cs[Decode](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetDecode)]

В текущей версии класс **FaceDetector** поддерживает только изображения в формате Gray8 или Nv12. Класс **SoftwareBitmap** предоставляет метод [**Convert**](https://msdn.microsoft.com/library/windows/apps/dn887362), который преобразует точечный рисунок из одного формата в другой. В этом примере исходное изображение преобразовывается в формат пикселей Gray8, если оно еще не в этом формате. При желании для того, чтобы определить во время выполнения, поддерживается ли формат пикселей, можно использовать методы [**GetSupportedBitmapPixelFormats**](https://msdn.microsoft.com/library/windows/apps/dn974140) и [**IsBitmapPixelFormatSupported**](https://msdn.microsoft.com/library/windows/apps/dn974142) на случай, если набор поддерживаемых форматов будет расширяться в будущих версиях.

[!code-cs[Format](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetFormat)]

Создайте экземпляр объекта **FaceDetector**, вызвав [**CreateAsync**](https://msdn.microsoft.com/library/windows/apps/dn974132), а затем вызовите [**DetectFacesAsync**](https://msdn.microsoft.com/library/windows/apps/dn974134), передав точечный рисунок, который был масштабирован до оптимального размера и преобразован в поддерживаемый формат пикселей. Этот метод возвращает список объектов [**DetectedFace**](https://msdn.microsoft.com/library/windows/apps/dn974123). **ShowDetectedFaces**— это вспомогательный метод, показанный ниже, который рисует квадраты вокруг лиц на изображении.

[!code-cs[Detect](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetDetect)]

Не забудьте удалить объекты, созданные во время процесса обнаружения лиц.

[!code-cs[Dispose](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetDispose)]

Чтобы отобразить изображение и нарисовать рамки вокруг обнаруженных лиц, добавьте на страницу XAML элемент [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267).

[!code-xml[Canvas](./code/FaceDetection_Win10/cs/MainPage.xaml#SnippetCanvas)]

Определите несколько переменных-членов, чтобы оформить квадраты, которые будут нарисованы.

[!code-cs[ClassVariables2](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetClassVariables2)]

Во вспомогательном методе **ShowDetectedFaces** создается новый элемент [**ImageBrush**](https://msdn.microsoft.com/library/windows/apps/br210101), а источник задается как [**SoftwareBitmapSource**](https://msdn.microsoft.com/library/windows/apps/dn997854), созданный из **SoftwareBitmap**, который представляет исходное изображение. Фон элемента управления XAML **Canvas** задается как кисть изображения.

Если список лиц, переданный во вспомогательный метод, не пуст, пройдите по всем лицам в списке и используйте свойство [**FaceBox**](https://msdn.microsoft.com/library/windows/apps/dn974126) класса [**DetectedFace**](https://msdn.microsoft.com/library/windows/apps/dn974123), чтобы определить положение и размер прямоугольника в изображении, которое содержит лицо. Так как размер элемента управления **Canvas**, вероятно, будет отличаться от размера исходного изображения, необходимо умножить координаты X и Y, а также ширину и высоту **FaceBox** на значение масштабирования, которое представляет собой соотношение размера исходного изображения к фактическому размеру элемента управления **Canvas**.

[!code-cs[ShowDetectedFaces](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetShowDetectedFaces)]

## <a name="track-faces-in-a-sequence-of-frames"></a>Отслеживание лиц в последовательности кадров

Если вы хотите обнаружить лица в видео, эффективней использовать класс [**FaceTracker**](https://msdn.microsoft.com/library/windows/apps/dn974150) вместо класса [**FaceDetector**](https://msdn.microsoft.com/library/windows/apps/dn974129), хотя действия по реализации очень похожи. **FaceTracker** использует сведения о ранее обработанных кадрах для оптимизации процесса обнаружения.

[!code-cs[FaceTrackingUsing](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetFaceTrackingUsing)]

Объявите переменную класса для объекта **FaceTracker**. В этом примере для запуска отслеживания лиц на определенный интервал используется [**ThreadPoolTimer**](https://msdn.microsoft.com/library/windows/apps/br230587). [SemaphoreSlim](https://msdn.microsoft.com/library/system.threading.semaphoreslim.aspx) используется для обеспечения того, что в определенный момент выполняется только одна операция отслеживания лиц.

[!code-cs[ClassVariables3](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetClassVariables3)]

Чтобы инициализировать операцию отслеживания лиц, создайте новый объект **FaceTracker**, вызвав [**CreateAsync**](https://msdn.microsoft.com/library/windows/apps/dn974151). Инициализируйте нужный интервал таймера, а затем создайте таймер. Вспомогательный метод **ProcessCurrentVideoFrame** будет вызываться каждый раз по истечении заданного интервала.

[!code-cs[TrackingInit](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetTrackingInit)]

Вспомогательный метод **ProcessCurrentVideoFrame** вызывается таймером асинхронно, поэтому метод сначала вызывает метод **Wait** семафора, чтобы убедиться, что операция отслеживания выполняется, и если это так, метод возвращает значение, не пытаясь обнаружить лица. В конце этого метода вызывается метод **Release** семафора, который делает возможным последующий вызов **ProcessCurrentVideoFrame** для продолжения.

Класс [**FaceTracker**](https://msdn.microsoft.com/library/windows/apps/dn974150) выполняется в объектах [**VideoFrame**](https://msdn.microsoft.com/library/windows/apps/dn930917). Существует несколько способов получить **VideoFrame**, в том числе захватив кадр предварительного просмотра из запущенного объекта [MediaCapture](capture-photos-and-video-with-mediacapture.md) или реализовав метод [**ProcessFrame**](https://msdn.microsoft.com/library/windows/apps/dn764784) элемента [**IBasicVideoEffect**](https://msdn.microsoft.com/library/windows/apps/dn764788). В этом примере используется неопределенный вспомогательный метод, который возвращает видеокадр, **GetLatestFrame**, в качестве заполнителя для этой операции. Подробнее о получении видеокадров из потока предварительного просмотра на запущенном устройстве захвата мультимедиа см. в разделе [Получение кадра предварительного просмотра](get-a-preview-frame.md).

Как и **FaceDetector**, **FaceTracker** поддерживает ограниченный набор форматов пикселей. В этом примере обнаружение лиц отменяется, если формат предоставленного кадра — не Nv12.

Вызовите [**ProcessNextFrameAsync**](https://msdn.microsoft.com/library/windows/apps/dn974157), чтобы извлечь список объектов [**DetectedFace**](https://msdn.microsoft.com/library/windows/apps/dn974123), представляющих лица в кадре. Когда у вас будет список лиц, их можно отображать тем же образом, который описан выше для обнаружения лиц. Обратите внимание, что так как вспомогательный метод отслеживания лиц не вызывается в потоке пользовательского интерфейса, все обновления пользовательского интерфейса необходимо выполнять внутри вызова [**CoredDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317).

[!code-cs[ProcessCurrentVideoFrame](./code/FaceDetection_Win10/cs/MainPage.xaml.cs#SnippetProcessCurrentVideoFrame)]

## <a name="related-topics"></a>Смежные разделы

* [Анализ сцен для захвата мультимедиа](scene-analysis-for-media-capture.md)
* [Пример базового обнаружения лиц](http://go.microsoft.com/fwlink/p/?LinkId=620512&clcid=0x409)
* [Пример базового отслеживания лиц](http://go.microsoft.com/fwlink/p/?LinkId=620513&clcid=0x409)
* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Воспроизведение мультимедиа](media-playback.md)
