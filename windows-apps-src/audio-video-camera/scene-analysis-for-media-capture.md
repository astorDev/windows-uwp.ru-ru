---
ms.assetid: B5D915E4-4280-422C-BA0E-D574C534410B
description: В данной статье рассказывается о том, как с помощью классов SceneAnalysisEffect и FaceDetectionEffect анализировать содержимое потока предварительного просмотра при захвате мультимедиа.
title: Эффекты для анализа кадров камеры
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 406af54cfaae8710cea2d989278a16f28c8dd619
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74256208"
---
# <a name="effects-for-analyzing-camera-frames"></a>Эффекты для анализа кадров камеры



В данной статье рассказывается о том, как с помощью классов [**SceneAnalysisEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffect) и [**FaceDetectionEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffect) анализировать содержимое потока предварительного просмотра при захвате мультимедиа.

## <a name="scene-analysis-effect"></a>Эффект анализа сцены

Класс [**SceneAnalysisEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffect) анализирует видеокадры в потоке предварительного просмотра при захвате мультимедиа и рекомендует параметры обработки для улучшения результатов захвата. Сейчас этот эффект позволяет определять, можно ли улучшить захват с помощью технологии High Dynamic Range (HDR).

Если эффект рекомендует использовать технологию HDR, то ее можно применить указанными ниже способами.

-   Класс [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture) позволяет захватывать фотографии с помощью встроенного в ОС Windows алгоритма обработки изображений по технологии HDR. Дополнительные сведения см. в статье [Захват фотографий с использованием технологии High Dynamic Range (HDR)](high-dynamic-range-hdr-photo-capture.md).

-   Используйте [**HdrVideoControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.HdrVideoControl) для захвата видео с помощью алгоритма обработки HDR, встроенного в систему Windows. Дополнительные сведения см. в статье [Доступ к элементам управления видеозахватом на устройстве](capture-device-controls-for-video-capture.md).

-   Класс [**VariablePhotoSequenceControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.Core.VariablePhotoSequenceController) позволяет захватывать последовательность кадров, которые затем можно объединить с помощью пользовательской реализации алгоритма HDR. Дополнительные сведения см. в статье [Переменная последовательность фотографий](variable-photo-sequence.md).

### <a name="scene-analysis-namespaces"></a>Пространства имен для анализа сцены

Чтобы использовать функцию анализа сцены, помимо пространств имен, необходимых для основного захвата мультимедиа, приложение должно включать указанные ниже пространства имен.

[!code-cs[SceneAnalysisUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSceneAnalysisUsing)]

### <a name="initialize-the-scene-analysis-effect-and-add-it-to-the-preview-stream"></a>Инициализация эффекта анализа сцены и добавление его в поток предварительного просмотра

Видеоэффекты реализованы с помощью двух API: определения эффекта, которое предоставляет параметры, необходимые устройству захвата для инициализации эффекта, и экземпляра эффекта, который можно использовать для управления эффектом. Так как вам может потребоваться доступ к экземпляру эффекта из нескольких мест в вашем коде, в общем случае следует объявить переменную-член для хранения объекта.

[!code-cs[DeclareSceneAnalysisEffect](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareSceneAnalysisEffect)]

В своем приложении после инициализации объекта **MediaCapture** создайте новый экземпляр класса [**SceneAnalysisEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffectDefinition).

Зарегистрируйте эффект для устройства захвата, вызвав метод [**AddVideoEffectAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.addvideoeffectasync) в объекте **MediaCapture**, предоставив **SceneAnalysisEffectDefinition** и указав тип [**MediaStreamType.VideoPreview**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType), чтобы показать, что необходимо применить эффект к потоку предварительного просмотра видео, а не к потоку захвата. Метод **AddVideoEffectAsync** возвращает экземпляр добавленного эффекта. Так как этот метод можно использовать для эффектов различных типов, необходимо привести возвращенный экземпляр к объекту [**SceneAnalysisEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffect).

Чтобы получить результаты анализа сцены, необходимо зарегистрировать обработчик события [**SceneAnalyzed**](https://docs.microsoft.com/uwp/api/windows.media.core.sceneanalysiseffect.sceneanalyzed).

Сейчас эффект анализа сцены включает только анализатор High Dynamic Range. Включите функцию анализа HDR, задав для свойства [**HighDynamicRangeControl.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.core.highdynamicrangecontrol.enabled) эффекта значение true.

[!code-cs[CreateSceneAnalysisEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateSceneAnalysisEffectAsync)]

### <a name="implement-the-sceneanalyzed-event-handler"></a>Реализация обработчика события SceneAnalyzed

Результаты анализа сцены возвращаются в обработчике события **SceneAnalyzed**. У объекта [**SceneAnalyzedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalyzedEventArgs), переданного в обработчик, имеется объект [**SceneAnalysisEffectFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffectFrame), у которого, в свою очередь, имеется объект [**HighDynamicRangeOutput**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.HighDynamicRangeOutput). Свойство [**Certainty**](https://docs.microsoft.com/uwp/api/windows.media.core.highdynamicrangeoutput.certainty) выходных данных High Dynamic Range имеет значение от 0 до 1,0. Значение 0 указывает на то, что с помощью обработки HDR не удастся улучшить результат захвата, а значение 1,0 указывает на то, что удастся. Вы можете выбрать пороговую точку, в которой необходимо использовать алгоритм HDR, или отобразить результаты для пользователя и предоставить ему возможность принять такое решение.

[!code-cs[SceneAnalyzed](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSceneAnalyzed)]

Кроме того, у объекта [**HighDynamicRangeOutput**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.HighDynamicRangeOutput), переданного в обработчик, имеется свойство [**FrameControllers**](https://docs.microsoft.com/uwp/api/windows.media.core.highdynamicrangeoutput.framecontrollers), содержащее предложенные контроллеры кадров для захвата переменной последовательности фотографий и ее обработки с использованием алгоритма HDR. Дополнительные сведения см. в статье [Переменная последовательность фотографий](variable-photo-sequence.md).

### <a name="clean-up-the-scene-analysis-effect"></a>Очистка эффекта анализа сцены

После того как приложение выполнило захват, перед ликвидацией объекта **MediaCapture** необходимо выключить эффект анализа сцены, задав для свойства [**HighDynamicRangeAnalyzer.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.core.highdynamicrangecontrol.enabled) эффекта значение false и отменив регистрацию обработчика событий [**SceneAnalyzed**](https://docs.microsoft.com/uwp/api/windows.media.core.sceneanalysiseffect.sceneanalyzed). Вызовите метод [**MediaCapture.ClearEffectsAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.cleareffectsasync), указав поток предварительного просмотра видео, так как это именно тот поток, в который был добавлен эффект. В заключение присвойте переменной-члену значение null.

[!code-cs[CleanUpSceneAnalysisEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpSceneAnalysisEffectAsync)]

## <a name="face-detection-effect"></a>Эффект обнаружения лиц

Эффект [**FaceDetectionEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffect) позволяет определять положения лиц в потоке предварительного просмотра захвата мультимедиа. Благодаря этому эффекту можно получать уведомления при обнаружении лица в потоке предварительного просмотра. Кроме того, он заключает каждое обнаруженное в кадре предварительного просмотра лицо в прямоугольник. На поддерживаемых устройствах эффект обнаружения лиц также позволяет улучшить экспозицию и фокусировку для самого важного лица в сцене.

### <a name="face-detection-namespaces"></a>Пространства имен для функции обнаружения лиц

Чтобы использовать функцию обнаружения лиц помимо пространств имен, необходимых для основного захвата мультимедиа, приложение должно включать указанные ниже пространства имен.

[!code-cs[FaceDetectionUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFaceDetectionUsing)]

### <a name="initialize-the-face-detection-effect-and-add-it-to-the-preview-stream"></a>Инициализация эффекта обнаружения лиц и добавление его в поток предварительного просмотра

Видеоэффекты реализованы с помощью двух API: определения эффекта, которое предоставляет параметры, необходимые устройству захвата для инициализации эффекта, и экземпляра эффекта, который можно использовать для управления эффектом. Так как вам может потребоваться доступ к экземпляру эффекта из нескольких мест в вашем коде, в общем случае следует объявить переменную-член для хранения объекта.

[!code-cs[DeclareFaceDetectionEffect](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareFaceDetectionEffect)]

В своем приложении после инициализации объекта **MediaCapture** создайте новый экземпляр класса [**FaceDetectionEffectDefinition**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffectDefinition). Настройте свойство [**DetectionMode**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffectdefinition.detectionmode), чтобы указать, что для вас имеет больший приоритет, скорость обработки или точность распознавания лиц. Настройте свойство [**SynchronousDetectionEnabled**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffectdefinition.synchronousdetectionenabled), чтобы указать, что не следует задерживать входящие кадры в ожидании завершения процесса обнаружения лиц, так как это может привести к рывкам изображения при предварительном просмотре.

Зарегистрируйте эффект для устройства захвата, вызвав метод [**AddVideoEffectAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.addvideoeffectasync) в объекте **MediaCapture**, предоставив **FaceDetectionEffectDefinition** и указав тип [**MediaStreamType.VideoPreview**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.MediaStreamType), чтобы показать, что необходимо применить эффект к потоку предварительного просмотра видео, а не к потоку захвата. Метод **AddVideoEffectAsync** возвращает экземпляр добавленного эффекта. Так как этот метод можно использовать для эффектов различных типов, необходимо привести возвращенный экземпляр к объекту [**FaceDetectionEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectionEffect).

Чтобы включить или выключить эффект, настройте свойство [**FaceDetectionEffect.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.enabled). Укажите, как часто эффект должен анализировать кадры, настроив свойство [**FaceDetectionEffect.DesiredDetectionInterval**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.desireddetectioninterval). Оба этих свойства можно настроить в процессе захвата мультимедиа.

[!code-cs[CreateFaceDetectionEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateFaceDetectionEffectAsync)]

### <a name="receive-notifications-when-faces-are-detected"></a>Получение уведомлений при обнаружении лиц

Если при обнаружении лиц необходимо выполнять определенное действие, например рисовать рамки вокруг обнаруженных лиц в предварительном просмотре видео, то можно зарегистрировать событие [**FaceDetected**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.facedetected).

[!code-cs[RegisterFaceDetectionHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterFaceDetectionHandler)]

В обработчике события можно получить список всех обнаруженных в кадре лиц с помощью свойства [**FaceDetectionEffectFrame.DetectedFaces**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffectframe.detectedfaces) объекта [**FaceDetectedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.FaceDetectedEventArgs). Свойство [**FaceBox**](https://docs.microsoft.com/uwp/api/windows.media.faceanalysis.detectedface.facebox) представляет собой структуру [**BitmapBounds**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapBounds), описывающую прямоугольник, содержащий обнаруженное лицо, в единицах, относительных к размерам изображений в потоке предварительного просмотра. Пример кода, преобразующего координаты в потоке предварительного просмотра в координаты на экране, см. в [примере обнаружения лиц для UWP](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CameraFaceDetection).

[!code-cs[FaceDetected](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFaceDetected)]

### <a name="clean-up-the-face-detection-effect"></a>Очистка эффекта обнаружения лиц

После того как приложение выполнило захват, перед ликвидацией объекта **MediaCapture** необходимо выключить эффект обнаружения лиц с помощью свойства [**FaceDetectionEffect.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.enabled) и отменить регистрацию обработчика событий [**FaceDetected**](https://docs.microsoft.com/uwp/api/windows.media.core.facedetectioneffect.facedetected) (если вы ранее регистрировали его). Вызовите метод [**MediaCapture.ClearEffectsAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.cleareffectsasync), указав поток предварительного просмотра видео, так как это именно тот поток, в который был добавлен эффект. В заключение присвойте переменной-члену значение null.

[!code-cs[CleanUpFaceDetectionEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpFaceDetectionEffectAsync)]

### <a name="check-for-focus-and-exposure-support-for-detected-faces"></a>Проверка поддержки корректировки фокусировки и экспозиции на основании данных, полученных от функции обнаружения лиц

Не все устройства имеют устройство захвата, которое может корректировать фокусировку и экспозицию на основании данных, полученных от функции обнаружения лиц. Так как функция обнаружения лиц потребляет ресурсы устройства, то, возможно, вам потребуется включать функцию распознавания лиц только на устройствах, которые могут использовать ее для улучшения захвата. Чтобы проверить, доступна ли функция оптимизации захвата на основе функции обнаружения лиц, получите класс [**VideoDeviceController**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.VideoDeviceController) для инициализированного объекта [MediaCapture](capture-photos-and-video-with-mediacapture.md), а затем — класс контроллера видеоустройства [**RegionsOfInterestControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.RegionsOfInterestControl). Убедитесь, что свойство [**MaxRegions**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.maxregions) поддерживает не менее одной области. Затем проверьте, присвоено ли свойству [**AutoExposureSupported**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.autoexposuresupported) или свойству [**AutoFocusSupported**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.autofocussupported) значение true. Если эти условия выполняются, это означает, что устройство может использовать функцию обнаружения лиц для корректировки параметров захвата.

[!code-cs[AreFaceFocusAndExposureSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetAreFaceFocusAndExposureSupported)]

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
* [Базовая фотография, видео и запись звука с помощью Медиакаптуре](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 




