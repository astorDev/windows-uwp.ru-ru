---
author: drewbatgit
ms.assetid: B5D915E4-4280-422C-BA0E-D574C534410B
description: В данной статье рассказывается о том, как с помощью классов SceneAnalysisEffect и FaceDetectionEffect анализировать содержимое потока предварительного просмотра при захвате мультимедиа.
title: Эффекты для анализа кадров камеры
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: d948dee234ad6c49da847324422737b1bae27e30
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "5547662"
---
# <a name="effects-for-analyzing-camera-frames"></a>Эффекты для анализа кадров камеры



В данной статье рассказывается о том, как с помощью классов [**SceneAnalysisEffect**](https://msdn.microsoft.com/library/windows/apps/dn948902) и [**FaceDetectionEffect**](https://msdn.microsoft.com/library/windows/apps/dn948776) анализировать содержимое потока предварительного просмотра при захвате мультимедиа.

## <a name="scene-analysis-effect"></a>Эффект анализа сцены

Класс [**SceneAnalysisEffect**](https://msdn.microsoft.com/library/windows/apps/dn948902) анализирует видеокадры в потоке предварительного просмотра при захвате мультимедиа и рекомендует параметры обработки для улучшения результатов захвата. Сейчас этот эффект позволяет определять, можно ли улучшить захват с помощью технологии High Dynamic Range (HDR).

Если эффект рекомендует использовать технологию HDR, то ее можно применить указанными ниже способами.

-   Класс [**AdvancedPhotoCapture**](https://msdn.microsoft.com/library/windows/apps/mt181386) позволяет захватывать фотографии с помощью встроенного в ОСWindows алгоритма обработки изображений по технологии HDR. Дополнительные сведения см. в статье [Захват фотографий с использованием технологии High Dynamic Range (HDR)](high-dynamic-range-hdr-photo-capture.md).

-   Используйте [**HdrVideoControl**](https://msdn.microsoft.com/library/windows/apps/dn926680) для захвата видео с помощью алгоритма обработки HDR, встроенного в систему Windows. Дополнительные сведения см. в статье [Доступ к элементам управления видеозахватом на устройстве](capture-device-controls-for-video-capture.md).

-   Класс [**VariablePhotoSequenceControl**](https://msdn.microsoft.com/library/windows/apps/dn640573) позволяет захватывать последовательность кадров, которые затем можно объединить с помощью пользовательской реализации алгоритма HDR. Дополнительные сведения см. в статье [Переменная последовательность фотографий](variable-photo-sequence.md).

### <a name="scene-analysis-namespaces"></a>Пространства имен для анализа сцены

Чтобы использовать функцию анализа сцены, помимо пространств имен, необходимых для основного захвата мультимедиа, приложение должно включать указанные ниже пространства имен.

[!code-cs[SceneAnalysisUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSceneAnalysisUsing)]

### <a name="initialize-the-scene-analysis-effect-and-add-it-to-the-preview-stream"></a>Инициализация эффекта анализа сцены и добавление его в поток предварительного просмотра

Видеоэффекты реализованы с помощью двух API: определения эффекта, которое предоставляет параметры, необходимые устройству захвата для инициализации эффекта, и экземпляра эффекта, который можно использовать для управления эффектом. Так как вам может потребоваться доступ к экземпляру эффекта из нескольких мест в вашем коде, в общем случае следует объявить переменную-член для хранения объекта.

[!code-cs[DeclareSceneAnalysisEffect](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareSceneAnalysisEffect)]

В своем приложении после инициализации объекта **MediaCapture** создайте новый экземпляр класса [**SceneAnalysisEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn948903).

Зарегистрируйте эффект для устройства захвата, вызвав метод [**AddVideoEffectAsync**](https://msdn.microsoft.com/library/windows/apps/dn878035) в объекте **MediaCapture**, предоставив **SceneAnalysisEffectDefinition** и указав тип [**MediaStreamType.VideoPreview**](https://msdn.microsoft.com/library/windows/apps/br226640), чтобы показать, что необходимо применить эффект к потоку предварительного просмотра видео, а не к потоку захвата. **AddVideoEffectAsync** возвращает экземпляр добавленного эффекта. Так как этот метод можно использовать для эффектов различных типов, необходимо привести возвращенный экземпляр к объекту [**SceneAnalysisEffect**](https://msdn.microsoft.com/library/windows/apps/dn948902).

Чтобы получить результаты анализа сцены, необходимо зарегистрировать обработчик события [**SceneAnalyzed**](https://msdn.microsoft.com/library/windows/apps/dn948920).

Сейчас эффект анализа сцены включает только анализатор High Dynamic Range. Включите функцию анализа HDR, задав для свойства [**HighDynamicRangeControl.Enabled**](https://msdn.microsoft.com/library/windows/apps/dn948827) эффекта значение true.

[!code-cs[CreateSceneAnalysisEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateSceneAnalysisEffectAsync)]

### <a name="implement-the-sceneanalyzed-event-handler"></a>Реализация обработчика события SceneAnalyzed

Результаты анализа сцены возвращаются в обработчике события **SceneAnalyzed**. У объекта [**SceneAnalyzedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn948922), переданного в обработчик, имеется объект [**SceneAnalysisEffectFrame**](https://msdn.microsoft.com/library/windows/apps/dn948907), у которого, в свою очередь, имеется объект [**HighDynamicRangeOutput**](https://msdn.microsoft.com/library/windows/apps/dn948830). Свойство [**Certainty**](https://msdn.microsoft.com/library/windows/apps/dn948833) выходных данных High Dynamic Range имеет значение от0 до1,0. Значение 0указывает на то, что с помощью обработки HDR не удастся улучшить результат захвата, а значение 1,0указывает на то, что удастся. Вы можете выбрать пороговую точку, в которой необходимо использовать алгоритм HDR, или отобразить результаты для пользователя и предоставить ему возможность принять такое решение.

[!code-cs[SceneAnalyzed](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSceneAnalyzed)]

Кроме того, у объекта [**HighDynamicRangeOutput**](https://msdn.microsoft.com/library/windows/apps/dn948830), переданного в обработчик, имеется свойство [**FrameControllers**](https://msdn.microsoft.com/library/windows/apps/dn948834), содержащее предложенные контроллеры кадров для захвата переменной последовательности фотографий и ее обработки с использованием алгоритма HDR. Дополнительные сведения см. в статье [Переменная последовательность фотографий](variable-photo-sequence.md).

### <a name="clean-up-the-scene-analysis-effect"></a>Очистка эффекта анализа сцены

После того как приложение выполнило захват, перед ликвидацией объекта **MediaCapture** необходимо выключить эффект анализа сцены, задав для свойства [**HighDynamicRangeAnalyzer.Enabled**](https://msdn.microsoft.com/library/windows/apps/dn948827) эффекта значение false и отменив регистрацию обработчика событий [**SceneAnalyzed**](https://msdn.microsoft.com/library/windows/apps/dn948920). Вызовите метод [**MediaCapture.ClearEffectsAsync**](https://msdn.microsoft.com/library/windows/apps/br226592), указав поток предварительного просмотра видео, так как это именно тот поток, в который был добавлен эффект. В заключение присвойте переменной-члену значение null.

[!code-cs[CleanUpSceneAnalysisEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpSceneAnalysisEffectAsync)]

## <a name="face-detection-effect"></a>Эффект обнаружения лиц

Эффект [**FaceDetectionEffect**](https://msdn.microsoft.com/library/windows/apps/dn948776) позволяет определять положения лиц в потоке предварительного просмотра захвата мультимедиа. Благодаря этому эффекту можно получать уведомления при обнаружении лица в потоке предварительного просмотра. Кроме того, он заключает каждое обнаруженное в кадре предварительного просмотра лицо в прямоугольник. На поддерживаемых устройствах эффект обнаружения лиц также позволяет улучшить экспозицию и фокусировку для самого важного лица в сцене.

### <a name="face-detection-namespaces"></a>Пространства имен для функции обнаружения лиц

Чтобы использовать функцию обнаружения лиц помимо пространств имен, необходимых для основного захвата мультимедиа, приложение должно включать указанные ниже пространства имен.

[!code-cs[FaceDetectionUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFaceDetectionUsing)]

### <a name="initialize-the-face-detection-effect-and-add-it-to-the-preview-stream"></a>Инициализация эффекта обнаружения лиц и добавление его в поток предварительного просмотра

Видеоэффекты реализованы с помощью двух API: определения эффекта, которое предоставляет параметры, необходимые устройству захвата для инициализации эффекта, и экземпляра эффекта, который можно использовать для управления эффектом. Так как вам может потребоваться доступ к экземпляру эффекта из нескольких мест в вашем коде, в общем случае следует объявить переменную-член для хранения объекта.

[!code-cs[DeclareFaceDetectionEffect](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareFaceDetectionEffect)]

В своем приложении после инициализации объекта **MediaCapture** создайте новый экземпляр класса [**FaceDetectionEffectDefinition**](https://msdn.microsoft.com/library/windows/apps/dn948778). Настройте свойство [**DetectionMode**](https://msdn.microsoft.com/library/windows/apps/dn948781), чтобы указать, что для вас имеет больший приоритет, скорость обработки или точность распознавания лиц. Настройте свойство [**SynchronousDetectionEnabled**](https://msdn.microsoft.com/library/windows/apps/dn948786), чтобы указать, что не следует задерживать входящие кадры в ожидании завершения процесса обнаружения лиц, так как это может привести к рывкам изображения при предварительном просмотре.

Зарегистрируйте эффект для устройства захвата, вызвав метод [**AddVideoEffectAsync**](https://msdn.microsoft.com/library/windows/apps/dn878035) в объекте **MediaCapture**, предоставив **FaceDetectionEffectDefinition** и указав тип [**MediaStreamType.VideoPreview**](https://msdn.microsoft.com/library/windows/apps/br226640), чтобы показать, что необходимо применить эффект к потоку предварительного просмотра видео, а не к потоку захвата. **AddVideoEffectAsync** возвращает экземпляр добавленного эффекта. Так как этот метод можно использовать для эффектов различных типов, необходимо привести возвращенный экземпляр к объекту [**FaceDetectionEffect**](https://msdn.microsoft.com/library/windows/apps/dn948776).

Чтобы включить или выключить эффект, настройте свойство [**FaceDetectionEffect.Enabled**](https://msdn.microsoft.com/library/windows/apps/dn948818). Укажите, как часто эффект должен анализировать кадры, настроив свойство [**FaceDetectionEffect.DesiredDetectionInterval**](https://msdn.microsoft.com/library/windows/apps/dn948814). Оба этих свойства можно настроить в процессе захвата мультимедиа.

[!code-cs[CreateFaceDetectionEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateFaceDetectionEffectAsync)]

### <a name="receive-notifications-when-faces-are-detected"></a>Получение уведомлений при обнаружении лиц

Если при обнаружении лиц необходимо выполнять определенное действие, например рисовать рамки вокруг обнаруженных лиц в предварительном просмотре видео, то можно зарегистрировать событие [**FaceDetected**](https://msdn.microsoft.com/library/windows/apps/dn948820).

[!code-cs[RegisterFaceDetectionHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterFaceDetectionHandler)]

В обработчике события можно получить список всех обнаруженных в кадре лиц с помощью свойства [**FaceDetectionEffectFrame.DetectedFaces**](https://msdn.microsoft.com/library/windows/apps/dn948792) объекта [**FaceDetectedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn948774). Свойство [**FaceBox**](https://msdn.microsoft.com/library/windows/apps/dn974126) представляет собой структуру [**BitmapBounds**](https://msdn.microsoft.com/library/windows/apps/br226169), описывающую прямоугольник, содержащий обнаруженное лицо, в единицах, относительных к размерам изображений в потоке предварительного просмотра. Пример кода, преобразующего координаты в потоке предварительного просмотра в координаты на экране, см. в [примере обнаружения лиц для UWP](http://go.microsoft.com/fwlink/?LinkId=619486).

[!code-cs[FaceDetected](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetFaceDetected)]

### <a name="clean-up-the-face-detection-effect"></a>Очистка эффекта обнаружения лиц

После того как приложение выполнило захват, перед ликвидацией объекта **MediaCapture** необходимо выключить эффект обнаружения лиц с помощью свойства [**FaceDetectionEffect.Enabled**](https://msdn.microsoft.com/library/windows/apps/dn948818) и отменить регистрацию обработчика событий [**FaceDetected**](https://msdn.microsoft.com/library/windows/apps/dn948820) (если вы ранее регистрировали его). Вызовите метод [**MediaCapture.ClearEffectsAsync**](https://msdn.microsoft.com/library/windows/apps/br226592), указав поток предварительного просмотра видео, так как это именно тот поток, в который был добавлен эффект. В заключение присвойте переменной-члену значение null.

[!code-cs[CleanUpFaceDetectionEffectAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpFaceDetectionEffectAsync)]

### <a name="check-for-focus-and-exposure-support-for-detected-faces"></a>Проверка поддержки корректировки фокусировки и экспозиции на основании данных, полученных от функции обнаружения лиц

Не все устройства имеют устройство захвата, которое может корректировать фокусировку и экспозицию на основании данных, полученных от функции обнаружения лиц. Так как функция обнаружения лиц потребляет ресурсы устройства, то, возможно, вам потребуется включать функцию распознавания лиц только на устройствах, которые могут использовать ее для улучшения захвата. Чтобы проверить, доступна ли функция оптимизации захвата на основе функции обнаружения лиц, получите класс [**VideoDeviceController**](https://msdn.microsoft.com/library/windows/apps/br226825) для инициализированного объекта [MediaCapture](capture-photos-and-video-with-mediacapture.md), а затем— класс контроллера видеоустройства [**RegionsOfInterestControl**](https://msdn.microsoft.com/library/windows/apps/dn279064). Убедитесь, что свойство [**MaxRegions**](https://msdn.microsoft.com/library/windows/apps/dn279069) поддерживает не менее одной области. Затем проверьте, присвоено ли свойству [**AutoExposureSupported**](https://msdn.microsoft.com/library/windows/apps/dn279065) или свойству [**AutoFocusSupported**](https://msdn.microsoft.com/library/windows/apps/dn279066) значение true. Если эти условия выполняются, это означает, что устройство может использовать функцию обнаружения лиц для корректировки параметров захвата.

[!code-cs[AreFaceFocusAndExposureSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetAreFaceFocusAndExposureSupported)]

## <a name="related-topics"></a>Еще по теме

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 




