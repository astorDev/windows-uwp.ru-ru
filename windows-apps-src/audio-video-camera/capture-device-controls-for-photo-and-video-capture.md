---
ms.assetid: 831123A7-1F40-4B74-AE9F-69AC9883B4AD
description: В этой статье рассказывается, как использовать ручные элементы управления устройства для включения расширенных сценариев фото- и видеозахвата, в том числе оптической стабилизации изображений и плавного масштабирования.
title: Ручные элементы управления фото- и видеозахватом на камере
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7344e5004e6ac398673734cb03ddbdde93b3bd0d
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74254309"
---
# <a name="manual-camera-controls-for-photo-and-video-capture"></a>Ручные элементы управления фото- и видеозахватом на камере



В этой статье рассказывается, как использовать ручные элементы управления устройства для включения расширенных сценариев фото- и видеозахвата, в том числе оптической стабилизации изображений и плавного масштабирования.

Описанные в ней элементы управления добавляются в приложение с помощью одного и того же шаблона. Для начала проверьте, поддерживается ли элемент управления на устройстве, на котором выполняется приложение. Если это так, установите для элемента управления нужный режим. Как правило, если элемент управления не поддерживается на текущем устройстве, необходимо отключить или скрыть элемент пользовательского интерфейса, который позволяет пользователю включать соответствующую функцию.

В этой статье используется код, адаптированный из [примера Camera Manual Controls SDK](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CameraManualControls). Вы можете скачать этот пример, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки собственного приложения.

> [!NOTE]
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

Все API элементов управления устройства, описанные в этой статье, входят в пространство имен [**Windows.Media.Devices**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices).

[!code-cs[VideoControllersUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetVideoControllersUsing)]

## <a name="exposure"></a>Экспозиция

[  **ExposureControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ExposureControl) позволяет задать скорость затвора для фото- или видеосъемки.

В этом примере используется элемент управления [**Slider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider) для настройки текущего значения экспозиции и флажок для включения и выключения автоматической настройки экспозиции.

[!code-xml[ExposureXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetExposureXAML)]

Проверьте, поддерживает ли используемое устройство захвата **ExposureControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecontrol.supported). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции. Задайте для флажка состояние "Установлен", чтобы указать, включена ли в данный момент автоматическая настройка экспозиции (свойство [**Auto**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecontrol.auto)).

Величина экспозиции должна находиться в поддерживаемом диапазоне устройства и изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecontrol.min), [**Max**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecontrol.max) и [**Step**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecontrol.step), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **ExposureControl** после отмены регистрации обработчика событий [**ValueChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.rangebase.valuechanged), чтобы событие не активировалось при установке значения.

[!code-cs[ExposureControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetExposureControl)]

В обработчике событий **ValueChanged** получите текущее значение элемента управления и установите значение экспозиции путем вызова метода [**SetValueAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecontrol.setvalueasync).

[!code-cs[ExposureSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetExposureSlider)]

В обработчике событий **CheckedChanged** флажка автоматической экспозиции включите или выключите автоматическую настройку экспозиции путем вызова метода [**SetAutoAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecontrol.setautoasync) и передачи значения логического типа.

[!code-cs[ExposureCheckBox](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetExposureCheckBox)]

> [!IMPORTANT]
> Режим автоматической выдержки поддерживается, только пока выполняется поток предварительного просмотра. Убедитесь, что поток предварительного просмотра выполняется, перед тем как включить автоматическую экспозицию.

## <a name="exposure-compensation"></a>Коррекция экспозиции

[  **ExposureCompensationControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ExposureCompensationControl) позволяет задавать коррекцию экспозиции для фото- или видеосъемки.

В этом примере используется элемент управления [**Slider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider) для настройки текущего значения коррекции экспозиции.

[!code-xml[EvXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetEvXAML)]

Проверьте, поддерживает ли используемое устройство захвата **ExposureCompensationControl**, проверив свойство [Supported](supported-codecs.md). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции.

Величина коррекции экспозиции должна находиться в поддерживаемом диапазоне устройства и должна изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecompensationcontrol.min), [**Max**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecompensationcontrol.max) и [**Step**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecompensationcontrol.step), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **ExposureCompensationControl** после отмены регистрации обработчика событий [**ValueChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.rangebase.valuechanged), чтобы событие не активировалось при установке значения.

[!code-cs[EvControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetEvControl)]

В обработчике событий **ValueChanged** получите текущее значение элемента управления и установите значение экспозиции путем вызова метода [**SetValueAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecompensationcontrol.setvalueasync).

[!code-cs[EvValueChanged](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetEvValueChanged)]

## <a name="flash"></a>Flash

[  **FlashControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.FlashControl) позволяет включить или отключить вспышку, а также включить автоматическую вспышку, когда система динамически определяет, следует ли ее использовать. Этот элемент управления позволяет включить автоматическое устранение эффекта красных глаз на устройствах, которые поддерживают эту возможность. Эти параметры будут применяться к фотографиям. [  **TorchControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.TorchControl) — это отдельный элемент управления для включения и выключения фонарика для видеозаписи.

В этом примере используется набор переключателей для переключения между режимами вспышки «Включено», «Выключено» и «Авто». Также имеется флажок для включения функции устранения эффекта красных глаз и видеофонарика.

[!code-xml[FlashXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetFlashXAML)]

Проверьте, поддерживает ли используемое устройство записи **FlashControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.supported). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции. Если **FlashControl** поддерживается, автоматическое устранение эффекта красных глаз может поддерживаться или не поддерживаться, поэтому перед включением пользовательского интерфейса следует проверить свойство [**RedEyeReductionSupported**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.redeyereductionsupported). Так как **TorchControl** и flash — два отдельных элемента управления, также следует проверить его свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.torchcontrol.supported) перед использованием.

В обработчике событий [**Checked**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.togglebutton.checked) для каждого переключателя flash следует включить или отключить соответствующий параметр вспышки. Обратите внимание: чтобы вспышка использовалась всегда, необходимо установить для свойства [**Enabled**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.enabled) значение true, а для свойства [**Auto**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.auto) — значение false.

[!code-cs[FlashControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFlashControl)]

[!code-cs[FlashRadioButtons](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFlashRadioButtons)]

В обработчике для флажка устранения эффекта красных глаз установите свойству [**RedEyeReduction**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.redeyereduction) соответствующее значение.

[!code-cs[RedEye](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetRedEye)]

Наконец, в обработчике для флажка видеофонарика установите для свойства [**Enabled**](https://docs.microsoft.com/uwp/api/windows.media.devices.torchcontrol.enabled) соответствующее значение.

[!code-cs[Torch](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTorch)]

> [!NOTE] 
>  На некоторых устройствах фонарик не будет светить, даже если для [**TorchControl.Enabled**](https://docs.microsoft.com/uwp/api/windows.media.devices.torchcontrol.enabled) установлено значение true, если на устройстве не запущен поток предварительного просмотра и в данный момент не выполняется запись видео. Рекомендуемый порядок действий: включить предварительный просмотр видео, затем включить фонарик, установив для **Enabled** значение true, а затем начать видеозапись. На некоторых устройствах фонарик включается после запуска предварительного просмотра. На других устройствах фонарик может не включаться, пока не начнется видеозапись.

## <a name="focus"></a>Фокусировка

Для объекта [**FocusControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.FocusControl) поддерживаются три распространенных метода настройки фокусировки камеры: непрерывная автофокусировка, фокусировка по нажатию и фокусировка вручную. Приложение камеры поддерживает все три этих метода, но для удобства чтения в этой статье каждый метод обсуждается по отдельности. В этом разделе также рассматривается включение вспомогательного света для фокусировки.

### <a name="continuous-autofocus"></a>Непрерывная автофокусировка

При включенной непрерывной автофокусировке камера фокусируется динамически, пытаясь сохранить объект съемки в фокусе. В этом примере используется переключатель для включения и выключения непрерывной автофокусировки.

[!code-xml[CAFXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetCAFXAML)]

Проверьте, поддерживает ли используемое устройство записи **FocusControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.supported). Затем проверьте, поддерживается ли непрерывная автофокусировка, проверив список [**SupportedFocusModes**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.supportedfocusmodes) на наличие значения [**FocusMode.Continuous**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.FocusMode). Если оно присутствует, можно отобразить переключатель непрерывной автофокусировки.

[!code-cs[CAF](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetCAF)]

В обработчике событий [**Checked**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.togglebutton.checked) для переключателя непрерывной автофокусировки используйте свойство [**VideoDeviceController.FocusControl**](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.focuscontrol) для получения экземпляра элемента управления. Вызовите [**UnlockAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.unlockasync), чтобы разблокировать элемент управления, если приложение ранее вызвало [**LockAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.lockasync) для включения другого режима фокусировки.

Создайте новый объект [**FocusSettings**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.FocusSettings) и установите для свойства [**Mode**](https://docs.microsoft.com/uwp/api/windows.media.devices.focussettings.mode) значение **Continuous**. Установите для свойства [**AutoFocusRange**](https://docs.microsoft.com/uwp/api/windows.media.devices.focussettings.autofocusrange) значение, подходящее для вашего сценария приложения или выбранное пользователем из пользовательского интерфейса. Передайте объект **FocusSettings** в метод [**Configure**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.configure), а затем вызовите [**FocusAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.focusasync) для запуска непрерывной автофокусировки.

[!code-cs[CafFocusRadioButton](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetCafFocusRadioButton)]

> [!IMPORTANT]
> Режим автофокусировки поддерживается только во время выполнения потока предварительного просмотра. Убедитесь, что поток предварительного просмотра выполняется, перед тем как включить непрерывную автофокусировку.

### <a name="tap-to-focus"></a>Фокусировка по нажатию

Метод фокусировки по нажатию использует [**FocusControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.FocusControl) и [**RegionsOfInterestControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.RegionsOfInterestControl) для определения подобласти кадра, на которой должно сфокусироваться устройство записи. Область фокуса задается пользователем путем нажатия на экран, после чего отображается поток предварительного просмотра.

В этом примере используется переключатель для включения и выключения режима фокусировки по нажатию.

[!code-xml[TapFocusXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetTapFocusXAML)]

Проверьте, поддерживает ли используемое устройство записи **FocusControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.supported). Чтобы воспользоваться этим методом, необходима поддержка **RegionsOfInterestControl** и хотя бы одной области. Проверьте свойства [**AutoFocusSupported**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.autofocussupported) и [**MaxRegions**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.maxregions), чтобы определить, следует ли показывать переключатель для фокусировки по нажатию.

[!code-cs[TapFocus](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapFocus)]

В обработчике событий [**Checked**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.togglebutton.checked) для переключателя фокусировки по нажатию используйте свойство [**VideoDeviceController.FocusControl**](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.focuscontrol) для получения экземпляра элемента управления. Вызовите метод [**LockAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.lockasync), чтобы заблокировать элемент управления, если приложение ранее вызвало [**UnlockAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.unlockasync) для включения непрерывной автофокусировки, а затем дождитесь, пока пользователя коснется экрана, чтобы изменить фокус.

[!code-cs[TapFocusRadioButton](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapFocusRadioButton)]

В этом примере происходит фокусировка на области, когда пользователь касается экрана, а затем фокус с этой области убирается, когда пользователь касается ее еще раз (как в случае переключателя). Используйте логическую переменную для отслеживания текущего состояния переключения.

[!code-cs[IsFocused](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetIsFocused)]

Следующий шаг — ожидание передачи данных для события, когда пользователь касается экрана, путем обработки события [**Tapped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.tapped) для [**CaptureElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CaptureElement), для которого в данный момент отображается поток предварительного просмотра. Если камера в настоящее время не отображает поток предварительного просмотра или если режим фокусировки по нажатию отключен, выполните возврат из обработчика, не выполняя никаких действий.

Если переменная отслеживания *\_фокус* переключена на значение false, а также если камера не находится в процессе фокусировки (определяется свойством [**фокусстате**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.focusstate) **фокусконтрол**), начните процесс TAP в фокусировку. Получите положение касания пользователя из аргументов события, переданных в обработчик. В данном примере эта возможность также используется для получения размера области, на которой будет выполняться фокусировка. В этом случае размер составляет 1/4 от наименьшего размера элемента захвата. Передайте положение касания и размер области во вспомогательный метод **TapToFocus**, который определяется в следующем разделе.

Если переключатель *\_с фокусом* установлен в значение true, пользователь касания должен удалить фокус из предыдущего региона. Это действие выполняется во вспомогательном методе **TapUnfocus**, показанном ниже.

[!code-cs[TapFocusPreviewControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapFocusPreviewControl)]

В вспомогательном методе **таптофокус** установите переключатель *\_с фокусом* на true, чтобы следующий экран отправит фокус из области касания.

Следующая задача в этом вспомогательном методе — определить прямоугольник в потоке предварительного просмотра, которому будет назначаться элемент управления фокусом. Для этого требуется два действия. Сначала нужно определить прямоугольник, в котором разместится поток предварительного просмотра, в элементе управления [**CaptureElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CaptureElement). Он зависит от размеров потока предварительного просмотра и ориентации устройства. Вспомогательный метод **GetPreviewStreamRectInControl**, показанный в конце данного раздела, выполняет эту задачу и возвращает прямоугольник, содержащий поток предварительного просмотра.

Следующая задача в **TapToFocus** — преобразовать место касания и нужный размер прямоугольника фокусировки, который был определен в обработчике событий **CaptureElement.Tapped**, в координаты в потоке захвата. Вспомогательный метод **ConvertUiTapToPreviewRect**, показанный ниже в этом разделе, выполняет это преобразование и возвращает прямоугольник в координатах потока захвата, в котором будет находиться фокус.

Теперь, когда получен целевой прямоугольник, создайте новый объект [**RegionOfInterest**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.RegionOfInterest), задав для свойства [**Bounds**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionofinterest.bounds) целевой прямоугольник, полученный в предыдущих шагах.

Получите [**FocusControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.FocusControl) устройства захвата. Создайте новый объект [**FocusSettings**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.FocusSettings) и установите для [**Mode**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.mode) и [**AutoFocusRange**](https://docs.microsoft.com/uwp/api/windows.media.devices.focussettings.autofocusrange) желаемые значения, предварительно проверив, поддерживаются ли они в **FocusControl**. Вызовите [**Configure**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.configure) в **FocusControl**, чтобы активировать параметры и сообщить устройству, что следует начать фокусировку на выбранной области.

Затем получите [**RegionsOfInterestControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.RegionsOfInterestControl) устройства записи и вызовите [**SetRegionsAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.setregionsasync), чтобы установить активную область. Несколько областей можно установить на устройствах, которые поддерживают такую возможность, но в этом примере задается только одна область.

Наконец, вызовите [**FocusAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.focusasync) в **FocusControl** для фокусировки.

> [!IMPORTANT]
> При реализации сценария «коснитесь, чтобы навести фокус» важен порядок операций. Вам следует вызывать эти API в следующем порядке:
>
> 1. [**Фокусконтрол. Настройка**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.configure)
> 2. [**Регионсофинтерестконтрол. Сетрегионсасинк**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.setregionsasync)
> 3. [**Фокусконтрол. Фокусасинк**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.focusasync)

[!code-cs[TapToFocus](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapToFocus)]

Во вспомогательном методе **TapUnfocus** получите **RegionsOfInterestControl** и вызовите [**ClearRegionsAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.regionsofinterestcontrol.clearregionsasync), чтобы очистить область, зарегистрированную с помощью элемента управления во вспомогательном методе **TapToFocus**. Затем получите **FocusControl** и вызовите [**FocusAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.focusasync), чтобы заставить устройство перефокусироваться без области интереса.

[!code-cs[TapUnfocus](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapUnfocus)]

Вспомогательный метод **GetPreviewStreamRectInControl** использует разрешение потока предварительного просмотра и ориентацию устройства для определения прямоугольника внутри элемента управления предварительного просмотра, содержащего поток предварительного просмотра; при этом обрезаются все поля для киноформата "Letterbox", которые может добавлять этот элемент управления для сохранения соотношения сторон потока. Этот метод также использует переменные — члены класса, определенные в примере базового кода захвата мультимедиа в разделе [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md).

[!code-cs[GetPreviewStreamRectInControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetGetPreviewStreamRectInControl)]

Вспомогательный метод **ConvertUiTapToPreviewRect** принимает в качестве аргументов место события касания, требуемый размер области фокуса и прямоугольник, содержащий поток предварительного просмотра, полученный из вспомогательного метода **GetPreviewStreamRectInControl**. Этот метод использует данные значения и текущую ориентацию устройства для расчета прямоугольника потока предварительного просмотра, содержащего нужную область. Этот метод также использует переменные — члены класса, определенные в примере базового кода захвата мультимедиа в разделе [Захват фотографий и видео с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md).

[!code-cs[ConvertUiTapToPreviewRect](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetConvertUiTapToPreviewRect)]

### <a name="manual-focus"></a>Фокусировка вручную

При ручной фокусировке используется элемент управления **Slider**, который позволяет задать текущую глубину фокуса для устройства захвата. Для включения и выключения ручной фокусировки используется переключатель.

[!code-xml[ManualFocusXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetManualFocusXAML)]

Проверьте, поддерживает ли используемое устройство записи **FocusControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.supported). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции.

Величина фокуса должна находиться в поддерживаемом диапазоне устройства и должна изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.min), [**Max**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.max) и [**Step**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.step), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **FocusControl** после отмены регистрации обработчика событий [**ValueChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.rangebase.valuechanged), чтобы событие не активировалось при установке значения.

[!code-cs[Focus](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFocus)]

В обработчике событий **Checked** для переключателя фокусировки вручную получите объект **FocusControl** и вызовите [**LockAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.lockasync), если приложение ранее разблокировало фокус путем вызова [**UnlockAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.unlockasync).

[!code-cs[ManualFocusChecked](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetManualFocusChecked)]

В обработчике событий **ValueChanged** для ползунка фокусировки вручную получите текущее значение элемента управления и установите значение фокуса, вызвав [**SetValueAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.focuscontrol.setvalueasync).

[!code-cs[FocusSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFocusSlider)]

### <a name="enable-the-focus-light"></a>Включение подсветки для фокусировки

На устройствах, поддерживающих эту функцию, вы можете включить подсветку для фокусировки, которая помогает устройству сфокусироваться. В этом примере используется флажок для включения и отключения подсветки для фокусировки.

[!code-xml[FocusLightXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetFocusLightXAML)]

Проверьте, поддерживает ли используемое устройство записи **FlashControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.supported). Также проверьте [**AssistantLightSupported**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.assistantlightsupported), чтобы узнать, поддерживается ли подсветка для фокусировки. Если обе возможности поддерживаются, вы можете включить и отобразить пользовательский интерфейс для этой функции.

[!code-cs[FocusLight](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFocusLight)]

В обработчике событий **CheckedChanged** получите объект [**FlashControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.FlashControl) устройства захвата. Задайте свойство [**AssistantLightEnabled**](https://docs.microsoft.com/uwp/api/windows.media.devices.flashcontrol.assistantlightenabled), чтобы включить или отключить подсветку.

[!code-cs[FocusLightCheckBox](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFocusLightCheckBox)]

## <a name="iso-speed"></a>Скорость ISO

[  **IsoSpeedControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.IsoSpeedControl) позволяет задать скорость ISO для фото- или видеосъемки.

В этом примере используется элемент управления [**Slider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider) для настройки текущего значения коррекции экспозиции и флажок для включения и выключения автоматической настройки скорости ISO.

[!code-xml[IsoXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetIsoXAML)]

Проверьте, поддерживает ли используемое устройство записи **IsoSpeedControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.isospeedcontrol.supported). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции. Задайте для флажка состояние "Установлен", чтобы указать, включена ли в данный момент автоматическая настройка скорости ISO (свойство [**Auto**](https://docs.microsoft.com/uwp/api/windows.media.devices.isospeedcontrol.auto)).

Величина скорости ISO должна находиться в поддерживаемом диапазоне устройства и должна изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://docs.microsoft.com/uwp/api/windows.media.devices.isospeedcontrol.min), [**Max**](https://docs.microsoft.com/uwp/api/windows.media.devices.isospeedcontrol.max) и [**Step**](https://docs.microsoft.com/uwp/api/windows.media.devices.isospeedcontrol.step), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **IsoSpeedControl** после отмены регистрации обработчика событий [**ValueChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.rangebase.valuechanged), чтобы событие не активировалось при установке значения.

[!code-cs[IsoControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetIsoControl)]

В обработчике событий **ValueChanged** получите текущее значение элемента управления и установите значение скорости ISO путем вызова метода [**SetValueAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.isospeedcontrol.setvalueasync).

[!code-cs[IsoSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetIsoSlider)]

В обработчике событий **CheckedChanged** флажка автоматической настройки скорости ISO включите параметр автоматической настройки скорости ISO, вызвав [**SetAutoAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.isospeedcontrol.setautoasync). Включите автоматическую настройку скорости ISO, вызвав [**SetValueAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.isospeedcontrol.setvalueasync) и передав текущее значение ползунка.

[!code-cs[IsoCheckBox](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetIsoCheckBox)]

## <a name="optical-image-stabilization"></a>Оптическая стабилизация изображений

Оптическая стабилизация изображений (OIS) стабилизирует захваченный видеопоток с помощью механического управления аппаратным устройством захвата, что обеспечивает более высокое качество по сравнению с цифровой стабилизацией. На устройствах, которые не поддерживают оптическую стабилизацию изображений, можно использовать функцию VideoStabilizationEffect для выполнения цифровой стабилизации захваченного видео. Дополнительные сведения см. в статье [Эффекты для видеозахвата](effects-for-video-capture.md).

Чтобы узнать, поддерживается ли на устройстве оптическая стабилизация изображений, проверьте свойство [**OpticalImageStabilizationControl.Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.opticalimagestabilizationcontrol.supported).

Элемент управления OIS работает в трех режимах: "Включен", "Отключен" и "Автоматический режим". Устройство в динамическом режиме определяет, улучшит ли оптическая стабилизация захват мультимедиа, и, если это так, включает эту функцию. Если определенный режим содержится в коллекции [**OpticalImageStabilizationControl.SupportedModes**](https://docs.microsoft.com/uwp/api/windows.media.devices.opticalimagestabilizationcontrol.supportedmodes), то он поддерживается на устройстве.

Чтобы включить или отключить оптическую стабилизацию изображений, переведите [**OpticalImageStabilizationControl.Mode**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.OpticalImageStabilizationMode) в нужный режим.

[!code-cs[SetOpticalImageStabilizationMode](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSetOpticalImageStabilizationMode)]

## <a name="powerline-frequency"></a>Частота линии питания
Некоторые камеры поддерживают обработку для защиты от мерцания, которая зависит от частоты переменного тока в текущей среде. Некоторые устройства поддерживают автоматическое определение частоты питания, а на других ее необходимо задать вручную. В следующем примере кода показано, как определить частоты питания устройстве и, если требуется, задать частоту вручную. 

Сначала вызовите методTryGetPowerlineFrequency[**класса**VideoDeviceController](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.trygetpowerlinefrequency), передав выходной параметр типа [**PowerlineFrequency**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.PowerlineFrequency). Если вызов завершается ошибкой, управление частотой питания на текущем устройстве не поддерживается. Если функция поддерживается, вы можете определить, доступен ли на устройстве автоматический режим, попытавшись установить автоматический режим. Для этого вызовите [**трисетповерлинефрекуенци**](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.trysetpowerlinefrequency) и передавайте значение **Auto**. Если вызов будет выполнен, это означает, что частота автоматической Powerline поддерживается. Если контроллер частоты питания поддерживается на устройстве, а автоматическое обнаружение частоты — нет, вы можете вручную задать частоту с помощью метода **TrySetPowerlineFrequency**. В этом примере **MyCustomFrequencyLookup** — это пользовательский метод, который реализуется, чтобы определить правильную частоту для текущего расположения устройства. 

[!code-cs[PowerlineFrequency](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetPowerlineFrequency)]

## <a name="white-balance"></a>Баланс белого

[  **WhiteBalanceControl**](https://docs.microsoft.com/uwp/api/windows.media.devices.videodevicecontroller.whitebalancecontrol) позволяет задать баланс белого для фото- или видеосъемки.

В этом примере используется элемент управления [**ComboBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ComboBox) для выбора встроенных стандартных значений цветовой температуры и элемент управления [**Slider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider) для ручной настройки баланса белого.

[!code-xml[WhiteBalanceXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetWhiteBalanceXAML)]

Проверьте, поддерживает ли используемое устройство записи **WhiteBalanceControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.whitebalancecontrol.supported). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции. Установите для элементов поля со списком значения перечисления [**ColorTemperaturePreset**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ColorTemperaturePreset). Для выбранного элемента установите текущее значение свойства [**Preset**](https://docs.microsoft.com/uwp/api/windows.media.devices.whitebalancecontrol.preset).

Для ручного элемента управления значение баланса белого должно находиться в поддерживаемом диапазоне устройства и изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://docs.microsoft.com/uwp/api/windows.media.devices.whitebalancecontrol.min), [**Max**](https://docs.microsoft.com/uwp/api/windows.media.devices.whitebalancecontrol.max) и [**Step**](https://docs.microsoft.com/uwp/api/windows.media.devices.whitebalancecontrol.step), которые используются для установки соответствующих свойств элемента управления ползунком. Перед включением ручного элемента управления убедитесь, что диапазон между минимальным и максимальным значениями больше, чем размер шага. В противном случае этот элемент управления не будет поддерживаться на текущем устройстве.

Задайте в качестве значения элемента управления ползунком текущее значение **WhiteBalanceControl** после отмены регистрации обработчика событий [**ValueChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.rangebase.valuechanged), чтобы событие не активировалось при установке значения.

[!code-cs[WhiteBalance](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetWhiteBalance)]

В обработчике событий [**SelectionChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) поля со списком для цветовой температуры получите выбранное в данный момент стандартное значение и задайте значение для элемента управления путем вызова [**SetPresetAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.whitebalancecontrol.setpresetasync). Если выбрано значение, отличное от **Manual**, отключите ползунок ручной настройки баланса белого.

[!code-cs[WhiteBalanceComboBox](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetWhiteBalanceComboBox)]

В обработчике событий **ValueChanged** получите текущее значение элемента управления и установите значение баланса белого путем вызова метода [**SetValueAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.exposurecontrol.setvalueasync).

[!code-cs[WhiteBalanceSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetWhiteBalanceSlider)]

> [!IMPORTANT]
> Регулировка баланса белого поддерживается только во время выполнения потока предварительного просмотра. Убедитесь, что поток предварительного просмотра выполняется, перед тем как настраивать баланс белого.

> [!IMPORTANT]
> Стандартное значение **ColorTemperaturePreset.Auto** сообщает системе, что необходимо автоматически настроить баланс белого. В некоторых случаях, например при записи последовательности фотографий, где уровни баланса белого должны совпадать для всех кадров, вы можете заблокировать элемент управления, зафиксировав текущее значение автоматической настройки. Для этого вызовите [**SetPresetAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.whitebalancecontrol.setpresetasync) и укажите стандартное значение **Manual**, не задавая значение для элемента управления с помощью [**SetValueAsync**](https://docs.microsoft.com/uwp/api/windows.media.devices.whitebalancecontrol.setvalueasync). Это приведет к блокировке текущего значения устройством. Не пытайтесь прочитать текущее значение элемента управления, а затем передать возвращенное значение в **SetValueAsync**, так как оно может оказаться неправильным.

## <a name="zoom"></a>Zoom

[  **ZoomControl**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ZoomControl) позволяет задать масштаб для фото- или видеосъемки.

В этом примере используется элемент управления [**Slider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider) для настройки текущего значения масштаба. В следующем разделе показано, как выполняется масштабирование на основе жестов сжатия на экране.

[!code-xml[ZoomXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetZoomXAML)]

Проверьте, поддерживает ли используемое устройство записи **ZoomControl**, проверив свойство [**Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.supported). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции.

Значение масштаба должно находиться в поддерживаемом диапазоне устройства и должно изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.min), [**Max**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.max) и [**Step**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.step), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **ZoomControl** после отмены регистрации обработчика событий [**ValueChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.rangebase.valuechanged), чтобы событие не активировалось при установке значения.

[!code-cs[ZoomControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetZoomControl)]

В обработчике событий **ValueChanged** создайте новый экземпляр класса [**ZoomSettings**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ZoomSettings), задав для свойства [**Value**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomsettings.value) текущее значение ползунка масштабирования. Если свойство [**SupportedModes**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.supportedmodes) элемента управления **ZoomControl** содержит [**ZoomTransitionMode.Smooth**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ZoomTransitionMode), это означает, что устройство поддерживает плавные переходы между уровнями масштабирования. Поскольку этот режим улучшает взаимодействие с пользователем, это значение обычно используется для свойства [**Mode**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomsettings.mode) объекта **ZoomSettings**.

Наконец, измените текущие параметры масштабирования, передав объект **ZoomSettings** в метод [**Configure**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.configure) объекта **ZoomControl**.

[!code-cs[ZoomSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetZoomSlider)]

### <a name="smooth-zoom-using-pinch-gesture"></a>Плавное масштабирование жестом сжатия

Как отмечалось в предыдущем разделе, на устройствах, поддерживающих соответствующую функцию, с помощью режима плавного масштабирования устройство захвата может плавно переходить от одного уровня цифрового масштабирования к другому. Это позволяет пользователю избежать резких переходов при динамическом изменении масштаба в процессе записи. В этом разделе описывается, как изменить масштаб в ответ на жест сжатия.

Для начала определите, поддерживается ли на текущем устройстве элемент управления цифровым масштабированием, проверив свойство [**ZoomControl.Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.supported). Далее определите, доступен ли режим плавного масштабирования. Для этого проверьте, содержит ли свойство [**ZoomControl.SupportedModes**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.supportedmodes) значение [**ZoomTransitionMode.Smooth**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ZoomTransitionMode).

[!code-cs[IsSmoothZoomSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetIsSmoothZoomSupported)]

На устройствах, поддерживающих мультисенсорную технологию, обычный сценарий — это настройка коэффициента масштабирования на основе двухпальцевого жеста сжатия. Чтобы включить жест сжатия, установите для свойства [**ManipulationMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationmode) элемента управления [**CaptureElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CaptureElement) значение [**ManipulationModes.Scale**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.ManipulationModes). Затем зарегистрируйте обработчик события [**ManipulationDelta**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.manipulationdelta), которое возникает при изменении масштаба с помощью жеста сжатия.

[!code-cs[RegisterPinchGestureHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterPinchGestureHandler)]

В обработчике события **ManipulationDelta** обновите коэффициент масштабирования в зависимости от изменения жеста сжатия пользователя. Значение [**ManipulationDelta.Scale**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.ManipulationDelta) указывает на изменение масштаба с помощью жеста сжатия. Например, небольшое увеличение масштаба обозначается значением, которое немного превышает 1, а небольшое уменьшение — значением, которое немного меньше 1. В этом примере текущее значение элемента управления масштабированием умножается на значение разницы масштаба.

Прежде чем задавать коэффициент масштабирования, убедитесь, что его значение не меньше минимального значения, которое поддерживается устройством и указано свойством [**ZoomControl.Min**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.min). Кроме того, убедитесь, что значение коэффициента меньше или равно значению [**ZoomControl.Max**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.max). Наконец, необходимо убедиться, что коэффициент масштабирования является кратным размеру шага масштабирования, поддерживаемого устройством, как указано в свойстве [**Step**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.step) . Если коэффициент масштабирования не соответствует этим требованиям, при попытке задать масштаб на устройстве захвата будет отображено исключение.

Чтобы установить масштаб на устройстве захвата, создайте новый объект [**ZoomSettings**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ZoomSettings). Установите для свойства [**Mode**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomsettings.mode) значение [**ZoomTransitionMode.Smooth**](https://docs.microsoft.com/uwp/api/Windows.Media.Devices.ZoomTransitionMode), а затем укажите требуемый коэффициент масштабирования в свойстве [**Value**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomsettings.value). После этого вызовите метод [**ZoomControl.Configure**](https://docs.microsoft.com/uwp/api/windows.media.devices.zoomcontrol.configure), чтобы задать новое значение масштаба на устройстве. В результате устройство плавно перейдет на новый масштаб.

[!code-cs[ManipulationDelta](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetManipulationDelta)]

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
* [Базовая фотография, видео и запись звука с помощью Медиакаптуре](basic-photo-video-and-audio-capture-with-MediaCapture.md)
