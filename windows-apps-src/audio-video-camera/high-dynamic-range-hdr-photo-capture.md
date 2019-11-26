---
ms.assetid: 0186EA01-8446-45BA-A109-C5EB4B80F368
description: В этой статье рассказывается, как использовать класс AdvancedPhotoCapture для фотозахвата с расширенным динамическим диапазоном (HDR) и в условиях низкой освещенности.
title: Фотозахват с расширенным динамическим диапазоном (HDR) и в условиях низкой освещенности
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 04133cc4a58ddbdbd45ba68b4c0635cd47ca2ca8
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257006"
---
# <a name="high-dynamic-range-hdr-and-low-light-photo-capture"></a>Фотозахват с расширенным динамическим диапазоном (HDR) и в условиях низкой освещенности



В этой статье показано, как использовать класс [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture) для фотозахвата с расширенным динамическим диапазоном (HDR). Кроме того, этот API дает возможность получить опорный кадр из захвата HDR, прежде чем завершить обработку окончательного изображения.

Другие статьи, связанные с захватом HDR:

-   Вы можете использовать [**SceneAnalysisEffect**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.SceneAnalysisEffect), чтобы разрешить системе оценить содержимое потока предварительного просмотра захвата мультимедиа и определить, улучшит ли обработка HDR результат захвата. Дополнительные сведения см. в разделе [Анализ сцен для захвата мультимедиа](scene-analysis-for-media-capture.md).

-   Используйте [**HdrVideoControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.HdrVideoControl) для захвата видео с помощью алгоритма обработки HDR, встроенного в систему Windows. Дополнительные сведения см. в статье [Доступ к элементам управления видеозахватом на устройстве](capture-device-controls-for-video-capture.md).

-   Вы можете использовать [**VariablePhotoSequenceCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Core.VariablePhotoSequenceCapture) для захвата последовательности фотографий (при чем для каждой из них можно указать особые параметры захвата) и внедрения собственного алгоритма HDR или другого алгоритма обработки. Дополнительные сведения см. в статье [Переменная последовательность фотографий](variable-photo-sequence.md).



> [!NOTE] 
> Начиная с Windows 10 версии 1709 поддерживается одновременная запись видео и использование **AdvancedPhotoCapture**.  Такая функциональность не поддерживается в предыдущих версиях. Это изменение означает, что вы можете одновременно подготовить **[LowLagMediaRecording](https://docs.microsoft.com/uwp/api/windows.media.capture.lowlagmediarecording)** и **[AdvancedPhotoCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture)** . Можно запустить или остановить запись видео между вызовами методов **[MediaCapture.PrepareAdvancedPhotoCaptureAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.prepareadvancedphotocaptureasync)** и **[AdvancedPhotoCapture.FinishAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.FinishAsync)** . Вы также можете вызвать **[AdvancedPhotoCapture.CaptureAsync](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.CaptureAsync)** во время записи видео. Тем не менее при некоторых сценариях использования **AdvancedPhotoCapture**, например, при записи HDR-фотографий во время ведения видеозаписи, некоторые видеокадры могут быть изменены из-за записи в режиме HDR, а может быть отрицательно воспринято пользователями. По этой причине во время записи видео **[AdvancedPhotoControl.SupportedModes](https://docs.microsoft.com/uwp/api/windows.media.devices.advancedphotocontrol.SupportedModes)** возвращает другой список режимов. Это значение необходимо проверять сразу же после запуска или остановки видеозаписи, чтобы убедиться, что нужный режим поддерживается на текущем этапе видеозаписи.


> [!NOTE] 
> Начиная с Windows 10 версии 1709, при включении HDR-режима для **AdvancedPhotoCapture** значение свойства [**FlashControl.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.enabled) игнорируется и вспышка никогда не активируется. В других режимах съемки установленное свойство **FlashControl.Enabled** переопределяет параметры **AdvancedPhotoCapture**, поэтому обычные фотографии снимаются со вспышкой. Если в параметре [**Auto**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.auto) задано значение true, то **AdvancedPhotoCapture** может использовать или не использовать вспышку, в зависимости от стандартного поведения камеры в условиях текущей сцены фотосъемки. В предыдущих выпусках параметр вспышки **AdvancedPhotoCapture** всегда переопределял параметр **FlashControl.Enabled**.

> [!NOTE] 
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

Существует универсальной пример Windows, демонстрирующий функциональность класса **AdvancedPhotoCapture**, с помощью которого можно увидеть API, используемые в контексте. Кроме того, этот класс может служить начальной точкой создания собственного приложения. Дополнительные сведения см. в разделе [Пример расширенной съемки на камеру](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CameraAdvancedCapture).

## <a name="advanced-photo-capture-namespaces"></a>Пространства имен расширенного фотозахвата

В примерах кода в этой статье помимо пространств имен, необходимых для основного захвата мультимедиа, используются API в следующих пространствах имен.

[!code-cs[HDRPhotoUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetHDRPhotoUsing)]

## <a name="hdr-photo-capture"></a>Фотозахват HDR

### <a name="determine-if-hdr-photo-capture-is-supported-on-the-current-device"></a>Определение возможности фотозахвата HDR на текущем устройстве

Описанный в этой статье способ захвата HDR выполняется с помощью объекта [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture). Не все устройства поддерживают фотозахват HDR с помощью **AdvancedPhotoCapture**. Определите, поддерживает ли этот метод устройство, на котором в настоящее время запущено приложение. Для этого необходимо получить свойствоVideoDeviceController[**объекта**MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.VideoDeviceController), а затем получить свойство [**AdvancedPhotoControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoControl). Проверьте коллекцию [**SupportedModes**](https://docs.microsoft.com/uwp/api/windows.media.devices.advancedphotocontrol.supportedmodes) контроллера видеоустройства , чтобы узнать, входит ли в нее режим [**AdvancedPhotoMode.Hdr**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoMode). Если входит, то в **AdvancedPhotoCapture** поддерживается режим записи HDR.

[!code-cs[HdrSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetHdrSupported)]

### <a name="configure-and-prepare-the-advancedphotocapture-object"></a>Настройка и подготовка объекта AdvancedPhotoCapture

Поскольку необходим доступ к экземпляру [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture) из нескольких расположений в пределах вашего кода, нужно объявить переменную-член для удержания объекта.

[!code-cs[DeclareAdvancedCapture](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareAdvancedCapture)]

В вашем приложении после инициализации объекта **MediaCapture** создайте объект [**AdvancedPhotoCaptureSettings**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoCaptureSettings) и установите режим [**AdvancedPhotoMode.Hdr**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoMode). Вызовите метод [**Configure**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoControl) объекта [**AdvancedPhotoControl**](https://docs.microsoft.com/uwp/api/windows.media.devices.advancedphotocontrol.configure), передав в него созданный объект **AdvancedPhotoCaptureSettings**.

Вызовите методPrepareAdvancedPhotoCaptureAsync[**объекта**MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.prepareadvancedphotocaptureasync), передав объект [**ImageEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.ImageEncodingProperties) с указанием типа кодирования, которое должно использоваться в захвате. Класс **ImageEncodingProperties** предоставляет статические методы для создания кодирования изображений, которые поддерживаются **MediaCapture**.

**PrepareAdvancedPhotoCaptureAsync** возвращает объект [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture), который будет использоваться для инициализации фотозахвата. Вы можете использовать этот объект, чтобы зарегистрировать обработчики событий для [**OptionalReferencePhotoCaptured**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.optionalreferencephotocaptured) и [**AllPhotosCaptured**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.allphotoscaptured), которые описываются далее в этой статье.

[!code-cs[CreateAdvancedCaptureAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateAdvancedCaptureAsync)]

### <a name="capture-an-hdr-photo"></a>Захват фотографий с технологией HDR

Чтобы захватить фотографию с расширенным динамическим диапазоном (HDR), вызовите метод [**CaptureAsync**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture) объекта [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.captureasync). Этот метод возвращает объект [**AdvancedCapturedPhoto**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedCapturedPhoto), предоставляющий созданную фотографию в свойстве [**Frame**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedcapturedphoto.frame).

[!code-cs[CaptureHdrPhotoAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCaptureHdrPhotoAsync)]

Большинство приложений фотографии кодируют поворот сделанного фото в файле изображения, чтобы он правильно отображался в других приложениях и на других устройствах. В этом примере показано использование вспомогательного класса **CameraRotationHelper** для расчета правильной ориентации файла. Этот класс более подробно описан в статье [**Обработка ориентации устройства с помощью MediaCapture**](handle-device-orientation-with-mediacapture.md).

Вспомогательный метод **SaveCapturedFrameAsync**, который сохраняет изображение на диск, рассматривается далее в этой статье.

### <a name="get-optional-reference-frame"></a>Получение дополнительного опорного кадра

При использовании технологии HDR захватывается несколько кадров. После захвата всех кадров из них составляется одно изображение. Вы можете получить доступ к кадру после его захвата, но до завершения обработки HDR, обработав событие [**OptionalReferencePhotoCaptured**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.optionalreferencephotocaptured). Если вас интересует только конечный результат фотографии с расширенным динамическим диапазоном (HDR), этого делать не нужно.

> [!IMPORTANT]
> [**Оптионалреференцефотокаптуред**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.optionalreferencephotocaptured) не вызывается на устройствах, поддерживающих HDR-устройство, и поэтому не создает опорные кадры. В приложении должны предусматриваться случаи, при которых это событие не возникает.

Поскольку опорный кадр поступает из контекста вызова **CaptureAsync**, предусмотрен механизм для передачи контекстной информации обработчику **OptionalReferencePhotoCaptured**. Сначала нужно вызвать объект, который будет содержать контекстные данные. При этом имя и содержимое этого объекта зависит только от вас. В этом примере определяется объект, который содержит члены для отслеживания имени файла и ориентации камеры при захвате.

[!code-cs[AdvancedCaptureContext](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetAdvancedCaptureContext)]

Создайте экземпляр объекта контекста, добавьте его члены, а затем передайте его в перегрузку [**CaptureAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.captureasync), где объект принимается в качестве параметра.

[!code-cs[CaptureWithContext](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCaptureWithContext)]

В обработчике событий [**OptionalReferencePhotoCaptured**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.optionalreferencephotocaptured) приведите свойство [**Context**](https://docs.microsoft.com/uwp/api/windows.media.capture.optionalreferencephotocapturedeventargs.context) объекта [**OptionalReferencePhotoCapturedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.OptionalReferencePhotoCapturedEventArgs) к классу объектов контекста. В этом примере изменяется имя файла для разделения изображения опорного кадра и окончательного изображения с расширенным динамическим диапазоном (HDR), а затем вызывается вспомогательный метод **SaveCapturedFrameAsync**, чтобы сохранить изображение.

[!code-cs[OptionalReferencePhotoCaptured](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetOptionalReferencePhotoCaptured)]

### <a name="receive-a-notification-when-all-frames-have-been-captured"></a>Получение уведомления после захвата всех кадров

Фотозахват с технологией HDR предполагает два шага. Сначала захватывается несколько кадров, а затем кадры обрабатываются и создается окончательное HDR-изображение. Инициировать другой захват во время захвата исходных кадров HDR нельзя. Однако это можно сделать после захвата всех кадров, но до завершающей обработки HDR. Событие [**AllPhotosCaptured**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.allphotoscaptured) возникает после завершения захвата HDR, уведомляя вас о том, что вы можете инициировать другой захват. Обычный сценарий — отключить кнопку захвата пользовательского интерфейса в начале захвата с HDR, а затем снова включить ее при вызове **AllPhotosCaptured**.

[!code-cs[AllPhotosCaptured](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetAllPhotosCaptured)]

### <a name="clean-up-the-advancedphotocapture-object"></a>Удаление объекта AdvancedPhotoCapture

Когда приложение завершит захват, прежде чем ликвидировать объект **MediaCapture**, необходимо завершить работу объекта [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture). Для этого вызовите [**FinishAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.finishasync) и установите для переменной-члена значение Null.

[!code-cs[CleanUpAdvancedPhotoCapture](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpAdvancedPhotoCapture)]


## <a name="low-light-photo-capture"></a>Фотозахват при слабом освещении
Начиная c Windows 10 (версия 1607) класс **AdvancedPhotoCapture** можно использовать для съемки фото с применением встроенного алгоритма, который повышает качество фото, сделанных в условиях низкой освещенности. При использовании функции съемки при низком освещении, предоставляемой классом [**AdvancedPhotoCapture**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedPhotoCapture), система оценивает текущую сцену и при необходимости применяет алгоритм, компенсирующий условия низкой освещенности. Если система определяет, что этот алгоритм не нужен, выполняется обычный захват.

Прежде чем использовать фотозахват в условиях низкой освещенности, определите, поддерживает ли этот метод устройство, на котором в настоящее время запущено приложение. Для этого необходимо получить свойствоVideoDeviceController[**объекта**MediaCapture](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.VideoDeviceController), а затем получить свойство [**AdvancedPhotoControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoControl). Просмотрите коллекцию контроллера видеоустройств [**SupportedModes**](https://docs.microsoft.com/uwp/api/windows.media.devices.advancedphotocontrol.supportedmodes), чтобы узнать, включает ли она [**AdvancedPhotoMode.LowLight**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoMode). Если да, то захват в условиях низкого освещения с использованием **AdvancedPhotoCapture** поддерживается. 
[!code-cs[LowLightSupported1](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetLowLightSupported1)]

[!code-cs[LowLightSupported2](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetLowLightSupported2)]

Затем объявите переменную-член для хранения объекта **AdvancedPhotoCapture**. 

[!code-cs[DeclareAdvancedCapture](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetDeclareAdvancedCapture)]

После того как вы инициализировали объект **MediaCapture**, создайте в своем приложении объект [**AdvancedPhotoCaptureSettings**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoCaptureSettings) и установите режим [**AdvancedPhotoMode.LowLight**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoMode). Вызовите метод [**Configure**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.AdvancedPhotoControl) объекта [**AdvancedPhotoControl**](https://docs.microsoft.com/uwp/api/windows.media.devices.advancedphotocontrol.configure), передав созданный объект **AdvancedPhotoCaptureSettings**.

Вызовите методPrepareAdvancedPhotoCaptureAsync[**объекта**MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.prepareadvancedphotocaptureasync), передав объект [**ImageEncodingProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.ImageEncodingProperties) с указанием типа кодирования, которое должно использоваться в захвате. 

[!code-cs[CreateAdvancedCaptureLowLightAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCreateAdvancedCaptureLowLightAsync)]

Чтобы сделать фото, вызовите метод [**CaptureAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.captureasync).

[!code-cs[CaptureLowLight](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCaptureLowLight)]

Как и в примере HDR выше, здесь используется вспомогательный класс **CameraRotationHelper**, чтобы определить значение поворота, которое необходимо добавить в код изображения, чтобы оно правильно отображалось в других приложениях и на других устройствах. Этот класс более подробно описан в статье [**Обработка ориентации устройства с помощью MediaCapture**](handle-device-orientation-with-mediacapture.md).

Вспомогательный метод **SaveCapturedFrameAsync**, который сохраняет изображение на диск, рассматривается далее в этой статье.

Можно сделать несколько фото в условиях низкого освещения, не изменяя настройки объекта **AdvancedPhotoCapture**, однако по завершении фотозахвата необходимо вызвать метод [**FinishAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.finishasync), чтобы удалить объект и связанные ресурсы.

[!code-cs[CleanUpAdvancedPhotoCapture](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetCleanUpAdvancedPhotoCapture)]

## <a name="working-with-advancedcapturedphoto-objects"></a>Работа с объектами AdvancedCapturedPhoto
[**Адванцедфотокаптуре. каптуреасинк**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.captureasync) возвращает объект [**адванцедкаптуредфото**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.AdvancedCapturedPhoto) , представляющий захваченную фотографию. Этот объект предоставляет свойство [**Frame**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedcapturedphoto.frame), которое возвращает объект [**CapturedFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CapturedFrame), представляющий изображение. Событие [**OptionalReferencePhotoCaptured**](https://docs.microsoft.com/uwp/api/windows.media.capture.advancedphotocapture.optionalreferencephotocaptured) также предоставляет объект **CapturedFrame** в аргументах события. Получив объект этого типа, с ним можно выполнить несколько действий, включая создание [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.SoftwareBitmap) или сохранение изображения в файл. 

## <a name="get-a-softwarebitmap-from-a-capturedframe"></a>Получение объекта SoftwareBitmap из CapturedFrame
Чтобы получить **SoftwareBitmap** из объекта **CapturedFrame**, нужно всего лишь получить доступ к свойству [**SoftwareBitmap**](https://docs.microsoft.com/uwp/api/windows.media.capture.capturedframe.softwarebitmap) объекта. Однако большинство форматов кодирования не поддерживают свойство **SoftwareBitmap** с **AdvancedPhotoCapture**, поэтому прежде необходимо проверить и убедиться, что это свойство не равно null.

[!code-cs[SoftwareBitmapFromCapturedFrame](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSoftwareBitmapFromCapturedFrame)]

Единственный формат кодирования, поддерживающий свойство **SoftwareBitmap** для **AdvancedPhotoCapture**, в этом выпуске — это несжатый NV12. Поэтому если требуется использовать эту функцию, необходимо задать эту кодировку при вызове метода [**PrepareAdvancedPhotoCaptureAsync**](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture.prepareadvancedphotocaptureasync). 

[!code-cs[UncompressedNv12](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetUncompressedNv12)]

Конечно, всегда можно сохранить изображение в файл, а затем загрузить файл в **SoftwareBitmap** отдельным действием. Дополнительные сведения о работе с **SoftwareBitmap** см. в разделе [**Создание, редактирование и сохранение растровых изображений**](imaging.md).

## <a name="save-a-capturedframe-to-a-file"></a>Сохранение CapturedFrame в файл
Класс [**CapturedFrame**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.CapturedFrame) реализует интерфейс IInputStream, чтобы его можно было использовать в качестве ввода в [**BitmapDecoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder), после чего [**BitmapEncoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapEncoder) можно использовать для записи данных изображения на диск.

В следующем примере создается новая папка в библиотеке изображений пользователя и в этой папке создается файл. Обратите внимание, что для получения доступа к этому каталогу файл манифеста вашего приложения должен включать функцию **Библиотека изображений**. Затем поток файлов открывается в заданном файле. После этого вызывается метод [**BitmapDecoder.CreateAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapdecoder.createasync) для создания декодера из **CapturedFrame**. Затем [**CreateForTranscodingAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.createfortranscodingasync) создает кодировщик из потока файлов и декодера.

Дальнейшие шаги включают кодирование ориентации фото и включение этого кода в файл изображения с помощью [**BitmapProperties**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.bitmapproperties) кодировщика. Дополнительные сведения об ориентации при создании изображений см. в разделе [**Обработка ориентации устройства с использованием MediaCapture**](handle-device-orientation-with-mediacapture.md).

Наконец, изображение записывается в файл вызовом метода [**FlushAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapencoder.flushasync).

[!code-cs[SaveCapturedFrameAsync](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSaveCapturedFrameAsync)]

## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Базовая фотография, видео и запись звука с помощью Медиакаптуре](basic-photo-video-and-audio-capture-with-MediaCapture.md)
