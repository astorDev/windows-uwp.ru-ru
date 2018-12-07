---
ms.assetid: 831123A7-1F40-4B74-AE9F-69AC9883B4AD
description: В этой статье рассказывается, как использовать ручные элементы управления устройства для включения расширенных сценариев фото- и видеозахвата, в том числе оптической стабилизации изображений и плавного масштабирования.
title: Ручные элементы управления фото- и видеозахватом на камере
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 303cbd5e87db773324cd98447df6d99dc6de5a0c
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8800072"
---
# <a name="manual-camera-controls-for-photo-and-video-capture"></a>Ручные элементы управления фото- и видеозахватом на камере



В этой статье рассказывается, как использовать ручные элементы управления устройства для включения расширенных сценариев фото- и видеозахвата, в том числе оптической стабилизации изображений и плавного масштабирования.

Описанные в ней элементы управления добавляются в приложение с помощью одного и того же шаблона. Для начала проверьте, поддерживается ли элемент управления на устройстве, на котором выполняется приложение. Если это так, установите для элемента управления нужный режим. Как правило, если элемент управления не поддерживается на текущем устройстве, необходимо отключить или скрыть элемент пользовательского интерфейса, который позволяет пользователю включать соответствующую функцию.

В этой статье используется код, адаптированный из [примера Camera Manual Controls SDK](https://go.microsoft.com/fwlink/?linkid=845228). Вы можете скачать этот пример, чтобы просмотреть код в контексте или использовать пример как отправную точку для настройки собственного приложения.

> [!NOTE]
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

Все API элементов управления устройства, описанные в этой статье, входят в пространство имен [**Windows.Media.Devices**](https://msdn.microsoft.com/library/windows/apps/br206902).

[!code-cs[VideoControllersUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetVideoControllersUsing)]

## <a name="exposure"></a>Экспозиция

[**ExposureControl**](https://msdn.microsoft.com/library/windows/apps/dn278910) позволяет задать скорость затвора для фото- или видеосъемки.

В этом примере используется элемент управления [**Slider**](https://msdn.microsoft.com/library/windows/apps/br209614) для настройки текущего значения экспозиции и флажок для включения и выключения автоматической настройки экспозиции.

[!code-xml[ExposureXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetExposureXAML)]

Проверьте, поддерживает ли используемое устройство захвата **ExposureControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn297710). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции. Задайте для флажка состояние "Установлен", чтобы указать, включена ли в данный момент автоматическая настройка экспозиции (свойство [**Auto**](https://msdn.microsoft.com/library/windows/apps/dn278911)).

Величина экспозиции должна находиться в поддерживаемом диапазоне устройства и изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://msdn.microsoft.com/library/windows/apps/dn278919), [**Max**](https://msdn.microsoft.com/library/windows/apps/dn278914) и [**Step**](https://msdn.microsoft.com/library/windows/apps/dn278930), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **ExposureControl** после отмены регистрации обработчика событий [**ValueChanged**](https://msdn.microsoft.com/library/windows/apps/br209737), чтобы событие не активировалось при установке значения.

[!code-cs[ExposureControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetExposureControl)]

В обработчике событий **ValueChanged** получите текущее значение элемента управления и установите значение экспозиции путем вызова метода [**SetValueAsync**](https://msdn.microsoft.com/library/windows/apps/dn278927).

[!code-cs[ExposureSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetExposureSlider)]

В обработчике событий **CheckedChanged** флажка автоматической экспозиции включите или выключите автоматическую настройку экспозиции путем вызова метода [**SetAutoAsync**](https://msdn.microsoft.com/library/windows/apps/dn278920) и передачи значения логического типа.

[!code-cs[ExposureCheckBox](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetExposureCheckBox)]

> [!IMPORTANT]
> Режим автоматической выдержки поддерживается, только пока выполняется поток предварительного просмотра. Убедитесь, что поток предварительного просмотра выполняется, перед тем как включить автоматическую экспозицию.

## <a name="exposure-compensation"></a>Коррекция экспозиции

[**ExposureCompensationControl**](https://msdn.microsoft.com/library/windows/apps/dn278897) позволяет задавать коррекцию экспозиции для фото- или видеосъемки.

В этом примере используется элемент управления [**Slider**](https://msdn.microsoft.com/library/windows/apps/br209614) для настройки текущего значения коррекции экспозиции.

[!code-xml[EvXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetEvXAML)]

Проверьте, поддерживает ли используемое устройство захвата **ExposureCompensationControl**, проверив свойство [Supported](supported-codecs.md). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции.

Величина коррекции экспозиции должна находиться в поддерживаемом диапазоне устройства и должна изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://msdn.microsoft.com/library/windows/apps/dn278901), [**Max**](https://msdn.microsoft.com/library/windows/apps/dn278899) и [**Step**](https://msdn.microsoft.com/library/windows/apps/dn278904), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **ExposureCompensationControl** после отмены регистрации обработчика событий [**ValueChanged**](https://msdn.microsoft.com/library/windows/apps/br209737), чтобы событие не активировалось при установке значения.

[!code-cs[EvControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetEvControl)]

В обработчике событий **ValueChanged** получите текущее значение элемента управления и установите значение экспозиции путем вызова метода [**SetValueAsync**](https://msdn.microsoft.com/library/windows/apps/dn278903).

[!code-cs[EvValueChanged](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetEvValueChanged)]

## <a name="flash"></a>Вспышка

[**FlashControl**](https://msdn.microsoft.com/library/windows/apps/dn297725) позволяет включить или отключить вспышку, а также включить автоматическую вспышку, когда система динамически определяет, следует ли ее использовать. Этот элемент управления позволяет включить автоматическое устранение эффекта красных глаз на устройствах, которые поддерживают эту возможность. Эти параметры будут применяться к фотографиям. [**TorchControl**](https://msdn.microsoft.com/library/windows/apps/dn279077) — это отдельный элемент управления для включения и выключения фонарика для видеозаписи.

В этом примере используется набор переключателей для переключения между режимами вспышки «Включено», «Выключено» и «Авто». Также имеется флажок для включения функции устранения эффекта красных глаз и видеофонарика.

[!code-xml[FlashXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetFlashXAML)]

Проверьте, поддерживает ли используемое устройство записи **FlashControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn297837). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции. Если **FlashControl** поддерживается, автоматическое устранение эффекта красных глаз может поддерживаться или не поддерживаться, поэтому перед включением пользовательского интерфейса следует проверить свойство [**RedEyeReductionSupported**](https://msdn.microsoft.com/library/windows/apps/dn297766). Так как **TorchControl** и flash— два отдельных элемента управления, также следует проверить его свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn279081) перед использованием.

В обработчике событий [**Checked**](https://msdn.microsoft.com/library/windows/apps/br209796) для каждого переключателя flash следует включить или отключить соответствующий параметр вспышки. Обратите внимание: чтобы вспышка использовалась всегда, необходимо установить для свойства [**Enabled**](https://msdn.microsoft.com/library/windows/apps/dn297733) значение true, а для свойства [**Auto**](https://msdn.microsoft.com/library/windows/apps/dn297728) — значение false.

[!code-cs[FlashControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFlashControl)]

[!code-cs[FlashRadioButtons](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFlashRadioButtons)]

В обработчике для флажка устранения эффекта красных глаз установите свойству [**RedEyeReduction**](https://msdn.microsoft.com/library/windows/apps/dn297758) соответствующее значение.

[!code-cs[RedEye](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetRedEye)]

Наконец, в обработчике для флажка видеофонарика установите для свойства [**Enabled**](https://msdn.microsoft.com/library/windows/apps/dn279078) соответствующее значение.

[!code-cs[Torch](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTorch)]

> [!NOTE] 
>  На некоторых устройствах фонарик не будет светить, даже если для [**TorchControl.Enabled**](https://msdn.microsoft.com/library/windows/apps/dn279078) установлено значение true, если на устройстве не запущен поток предварительного просмотра и в данный момент не выполняется запись видео. Рекомендуемый порядок действий: включить предварительный просмотр видео, затем включить фонарик, установив для **Enabled** значение true, а затем начать видеозапись. На некоторых устройствах фонарик включается после запуска предварительного просмотра. На других устройствах фонарик может не включаться, пока не начнется видеозапись.

## <a name="focus"></a>Фокус

Для объекта [**FocusControl**](https://msdn.microsoft.com/library/windows/apps/dn297788) поддерживаются три распространенных метода настройки фокусировки камеры: непрерывная автофокусировка, фокусировка по нажатию и фокусировка вручную. Приложение камеры поддерживает все три этих метода, но для удобства чтения в этой статье каждый метод обсуждается по отдельности. В этом разделе также рассматривается включение вспомогательного света для фокусировки.

### <a name="continuous-autofocus"></a>Непрерывная автофокусировка

При включенной непрерывной автофокусировке камера фокусируется динамически, пытаясь сохранить объект съемки в фокусе. В этом примере используется переключатель для включения и выключения непрерывной автофокусировки.

[!code-xml[CAFXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetCAFXAML)]

Проверьте, поддерживает ли используемое устройство записи **FocusControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn297785). Затем проверьте, поддерживается ли непрерывная автофокусировка, проверив список [**SupportedFocusModes**](https://msdn.microsoft.com/library/windows/apps/dn608079) на наличие значения [**FocusMode.Continuous**](https://msdn.microsoft.com/library/windows/apps/dn608084). Если оно присутствует, можно отобразить переключатель непрерывной автофокусировки.

[!code-cs[CAF](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetCAF)]

В обработчике событий [**Checked**](https://msdn.microsoft.com/library/windows/apps/br209796) для переключателя непрерывной автофокусировки используйте свойство [**VideoDeviceController.FocusControl**](https://msdn.microsoft.com/library/windows/apps/dn279091) для получения экземпляра элемента управления. Вызовите [**UnlockAsync**](https://msdn.microsoft.com/library/windows/apps/dn608081), чтобы разблокировать элемент управления, если приложение ранее вызвало [**LockAsync**](https://msdn.microsoft.com/library/windows/apps/dn608075) для включения другого режима фокусировки.

Создайте новый объект [**FocusSettings**](https://msdn.microsoft.com/library/windows/apps/dn608085) и установите для свойства [**Mode**](https://msdn.microsoft.com/library/windows/apps/dn608090) значение **Continuous**. Установите для свойства [**AutoFocusRange**](https://msdn.microsoft.com/library/windows/apps/dn608086) значение, подходящее для вашего сценария приложения или выбранное пользователем из пользовательского интерфейса. Передайте объект **FocusSettings** в метод [**Configure**](https://msdn.microsoft.com/library/windows/apps/dn608067), а затем вызовите [**FocusAsync**](https://msdn.microsoft.com/library/windows/apps/dn297794) для запуска непрерывной автофокусировки.

[!code-cs[CafFocusRadioButton](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetCafFocusRadioButton)]

> [!IMPORTANT]
> Режим автофокусировки поддерживается только во время выполнения потока предварительного просмотра. Убедитесь, что поток предварительного просмотра выполняется, перед тем как включить непрерывную автофокусировку.

### <a name="tap-to-focus"></a>Фокусировка по нажатию

Метод фокусировки по нажатию использует [**FocusControl**](https://msdn.microsoft.com/library/windows/apps/dn297788) и [**RegionsOfInterestControl**](https://msdn.microsoft.com/library/windows/apps/dn279064) для определения подобласти кадра, на которой должно сфокусироваться устройство записи. Область фокуса задается пользователем путем нажатия на экран, после чего отображается поток предварительного просмотра.

В этом примере используется переключатель для включения и выключения режима фокусировки по нажатию.

[!code-xml[TapFocusXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetTapFocusXAML)]

Проверьте, поддерживает ли используемое устройство записи **FocusControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn297785). Чтобы воспользоваться этим методом, необходима поддержка **RegionsOfInterestControl** и хотя бы одной области. Проверьте свойства [**AutoFocusSupported**](https://msdn.microsoft.com/library/windows/apps/dn279066) и [**MaxRegions**](https://msdn.microsoft.com/library/windows/apps/dn279069), чтобы определить, следует ли показывать переключатель для фокусировки по нажатию.

[!code-cs[TapFocus](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapFocus)]

В обработчике событий [**Checked**](https://msdn.microsoft.com/library/windows/apps/br209796) для переключателя фокусировки по нажатию используйте свойство [**VideoDeviceController.FocusControl**](https://msdn.microsoft.com/library/windows/apps/dn279091) для получения экземпляра элемента управления. Вызовите метод [**LockAsync**](https://msdn.microsoft.com/library/windows/apps/dn608075), чтобы заблокировать элемент управления, если приложение ранее вызвало [**UnlockAsync**](https://msdn.microsoft.com/library/windows/apps/dn608081) для включения непрерывной автофокусировки, а затем дождитесь, пока пользователя коснется экрана, чтобы изменить фокус.

[!code-cs[TapFocusRadioButton](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapFocusRadioButton)]

В этом примере происходит фокусировка на области, когда пользователь касается экрана, а затем фокус с этой области убирается, когда пользователь касается ее еще раз (как в случае переключателя). Используйте логическую переменную для отслеживания текущего состояния переключения.

[!code-cs[IsFocused](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetIsFocused)]

Следующий шаг — ожидание передачи данных для события, когда пользователь касается экрана, путем обработки события [**Tapped**](https://msdn.microsoft.com/library/windows/apps/br208985) для [**CaptureElement**](https://msdn.microsoft.com/library/windows/apps/br209278), для которого в данный момент отображается поток предварительного просмотра. Если камера в настоящее время не отображает поток предварительного просмотра или если режим фокусировки по нажатию отключен, выполните возврат из обработчика, не выполняя никаких действий.

Если переменная отслеживания *\_isFocused* переключена на значение false и если камера в данный момент не в процессе фокусировки (определяется свойством [**FocusState**](https://msdn.microsoft.com/library/windows/apps/dn608074) для **FocusControl**), следует начинать процесс фокусировки по нажатию. Получите положение касания пользователя из аргументов события, переданных в обработчик. В данном примере эта возможность также используется для получения размера области, на которой будет выполняться фокусировка. В этом случае размер составляет 1/4 от наименьшего размера элемента захвата. Передайте положение касания и размер области во вспомогательный метод **TapToFocus**, который определяется в следующем разделе.

Если для переключателя *\_isFocused* установлено значение true, касание пользователя должно убрать фокус из предыдущей области. Это действие выполняется во вспомогательном методе **TapUnfocus**, показанном ниже.

[!code-cs[TapFocusPreviewControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapFocusPreviewControl)]

Во вспомогательном методе **TapToFocus** сначала установите для *\_isFocused* значение true, чтобы каждое следующее касание экрана убирало фокус с предыдущей области.

Следующая задача в этом вспомогательном методе— определить прямоугольник в потоке предварительного просмотра, которому будет назначаться элемент управления фокусом. Для этого требуется два действия. Сначала нужно определить прямоугольник, в котором разместится поток предварительного просмотра, в элементе управления [**CaptureElement**](https://msdn.microsoft.com/library/windows/apps/br209278). Он зависит от размеров потока предварительного просмотра и ориентации устройства. Вспомогательный метод **GetPreviewStreamRectInControl**, показанный в конце данного раздела, выполняет эту задачу и возвращает прямоугольник, содержащий поток предварительного просмотра.

Следующая задача в **TapToFocus** — преобразовать место касания и нужный размер прямоугольника фокусировки, который был определен в обработчике событий **CaptureElement.Tapped**, в координаты в потоке захвата. Вспомогательный метод **ConvertUiTapToPreviewRect**, показанный ниже в этом разделе, выполняет это преобразование и возвращает прямоугольник в координатах потока захвата, в котором будет находиться фокус.

Теперь, когда получен целевой прямоугольник, создайте новый объект [**RegionOfInterest**](https://msdn.microsoft.com/library/windows/apps/dn279058), задав для свойства [**Bounds**](https://msdn.microsoft.com/library/windows/apps/dn279062) целевой прямоугольник, полученный в предыдущих шагах.

Получите [**FocusControl**](https://msdn.microsoft.com/library/windows/apps/dn297788) устройства захвата. Создайте новый объект [**FocusSettings**](https://msdn.microsoft.com/library/windows/apps/dn608085) и установите для [**Mode**](https://msdn.microsoft.com/library/windows/apps/dn608076) и [**AutoFocusRange**](https://msdn.microsoft.com/library/windows/apps/dn608086) желаемые значения, предварительно проверив, поддерживаются ли они в **FocusControl**. Вызовите [**Configure**](https://msdn.microsoft.com/library/windows/apps/dn608067) в **FocusControl**, чтобы активировать параметры и сообщить устройству, что следует начать фокусировку на выбранной области.

Затем получите [**RegionsOfInterestControl**](https://msdn.microsoft.com/library/windows/apps/dn279064) устройства записи и вызовите [**SetRegionsAsync**](https://msdn.microsoft.com/library/windows/apps/dn279070), чтобы установить активную область. Несколько областей можно установить на устройствах, которые поддерживают такую возможность, но в этом примере задается только одна область.

Наконец, вызовите [**FocusAsync**](https://msdn.microsoft.com/library/windows/apps/dn297794) в **FocusControl** для фокусировки.

> [!IMPORTANT]
> При реализации сценария «коснитесь, чтобы навести фокус» важен порядок операций. Вам следует вызывать эти API в следующем порядке:
>
> 1. [**FocusControl.Configure**](https://msdn.microsoft.com/library/windows/apps/dn608067)
> 2. [**RegionsOfInterestControl.SetRegionsAsync**](https://msdn.microsoft.com/library/windows/apps/dn279070)
> 3. [**FocusControl.FocusAsync**](https://msdn.microsoft.com/library/windows/apps/dn297794)

[!code-cs[TapToFocus](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapToFocus)]

Во вспомогательном методе **TapUnfocus** получите **RegionsOfInterestControl** и вызовите [**ClearRegionsAsync**](https://msdn.microsoft.com/library/windows/apps/dn279068), чтобы очистить область, зарегистрированную с помощью элемента управления во вспомогательном методе **TapToFocus**. Затем получите **FocusControl** и вызовите [**FocusAsync**](https://msdn.microsoft.com/library/windows/apps/dn297794), чтобы заставить устройство перефокусироваться без области интереса.

[!code-cs[TapUnfocus](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetTapUnfocus)]

Вспомогательный метод **GetPreviewStreamRectInControl** использует разрешение потока предварительного просмотра и ориентацию устройства для определения прямоугольника внутри элемента управления предварительного просмотра, содержащего поток предварительного просмотра; при этом обрезаются все поля для киноформата "Letterbox", которые может добавлять этот элемент управления для сохранения соотношения сторон потока. Этот метод также использует переменные — члены класса, определенные в примере базового кода захвата мультимедиа в разделе [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md).

[!code-cs[GetPreviewStreamRectInControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetGetPreviewStreamRectInControl)]

Вспомогательный метод **ConvertUiTapToPreviewRect** принимает в качестве аргументов место события касания, требуемый размер области фокуса и прямоугольник, содержащий поток предварительного просмотра, полученный из вспомогательного метода **GetPreviewStreamRectInControl**. Этот метод использует данные значения и текущую ориентацию устройства для расчета прямоугольника потока предварительного просмотра, содержащего нужную область. Этот метод также использует переменные — члены класса, определенные в примере базового кода захвата мультимедиа в разделе [Захват фотографий и видео с помощью MediaCapture](capture-photos-and-video-with-mediacapture.md).

[!code-cs[ConvertUiTapToPreviewRect](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetConvertUiTapToPreviewRect)]

### <a name="manual-focus"></a>Фокусировка вручную

При ручной фокусировке используется элемент управления **Slider**, который позволяет задать текущую глубину фокуса для устройства захвата. Для включения и выключения ручной фокусировки используется переключатель.

[!code-xml[ManualFocusXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetManualFocusXAML)]

Проверьте, поддерживает ли используемое устройство записи **FocusControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn297837). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции.

Величина фокуса должна находиться в поддерживаемом диапазоне устройства и должна изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://msdn.microsoft.com/library/windows/apps/dn297808), [**Max**](https://msdn.microsoft.com/library/windows/apps/dn297802) и [**Step**](https://msdn.microsoft.com/library/windows/apps/dn297833), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **FocusControl** после отмены регистрации обработчика событий [**ValueChanged**](https://msdn.microsoft.com/library/windows/apps/br209737), чтобы событие не активировалось при установке значения.

[!code-cs[Focus](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFocus)]

В обработчике событий **Checked** для переключателя фокусировки вручную получите объект **FocusControl** и вызовите [**LockAsync**](https://msdn.microsoft.com/library/windows/apps/dn608075), если приложение ранее разблокировало фокус путем вызова [**UnlockAsync**](https://msdn.microsoft.com/library/windows/apps/dn608081).

[!code-cs[ManualFocusChecked](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetManualFocusChecked)]

В обработчике событий **ValueChanged** для ползунка фокусировки вручную получите текущее значение элемента управления и установите значение фокуса, вызвав [**SetValueAsync**](https://msdn.microsoft.com/library/windows/apps/dn297828).

[!code-cs[FocusSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFocusSlider)]

### <a name="enable-the-focus-light"></a>Включение подсветки для фокусировки

На устройствах, поддерживающих эту функцию, вы можете включить подсветку для фокусировки, которая помогает устройству сфокусироваться. В этом примере используется флажок для включения и отключения подсветки для фокусировки.

[!code-xml[FocusLightXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetFocusLightXAML)]

Проверьте, поддерживает ли используемое устройство записи **FlashControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn297785). Также проверьте [**AssistantLightSupported**](https://msdn.microsoft.com/library/windows/apps/dn608066), чтобы узнать, поддерживается ли подсветка для фокусировки. Если обе возможности поддерживаются, вы можете включить и отобразить пользовательский интерфейс для этой функции.

[!code-cs[FocusLight](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFocusLight)]

В обработчике событий **CheckedChanged** получите объект [**FlashControl**](https://msdn.microsoft.com/library/windows/apps/dn297725) устройства захвата. Задайте свойство [**AssistantLightEnabled**](https://msdn.microsoft.com/library/windows/apps/dn608065), чтобы включить или отключить подсветку.

[!code-cs[FocusLightCheckBox](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetFocusLightCheckBox)]

## <a name="iso-speed"></a>Скорость ISO

[**IsoSpeedControl**](https://msdn.microsoft.com/library/windows/apps/dn297850) позволяет задать скорость ISO для фото- или видеосъемки.

В этом примере используется элемент управления [**Slider**](https://msdn.microsoft.com/library/windows/apps/br209614) для настройки текущего значения коррекции экспозиции и флажок для включения и выключения автоматической настройки скорости ISO.

[!code-xml[IsoXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetIsoXAML)]

Проверьте, поддерживает ли используемое устройство записи **IsoSpeedControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn297869). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции. Задайте для флажка состояние "Установлен", чтобы указать, включена ли в данный момент автоматическая настройка скорости ISO (свойство [**Auto**](https://msdn.microsoft.com/library/windows/apps/dn608093)).

Величина скорости ISO должна находиться в поддерживаемом диапазоне устройства и должна изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://msdn.microsoft.com/library/windows/apps/dn608095), [**Max**](https://msdn.microsoft.com/library/windows/apps/dn608094) и [**Step**](https://msdn.microsoft.com/library/windows/apps/dn608129), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **IsoSpeedControl** после отмены регистрации обработчика событий [**ValueChanged**](https://msdn.microsoft.com/library/windows/apps/br209737), чтобы событие не активировалось при установке значения.

[!code-cs[IsoControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetIsoControl)]

В обработчике событий **ValueChanged** получите текущее значение элемента управления и установите значение скорости ISO путем вызова метода [**SetValueAsync**](https://msdn.microsoft.com/library/windows/apps/dn608128).

[!code-cs[IsoSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetIsoSlider)]

В обработчике событий **CheckedChanged** флажка автоматической настройки скорости ISO включите параметр автоматической настройки скорости ISO, вызвав [**SetAutoAsync**](https://msdn.microsoft.com/library/windows/apps/dn608127). Включите автоматическую настройку скорости ISO, вызвав [**SetValueAsync**](https://msdn.microsoft.com/library/windows/apps/dn608128) и передав текущее значение ползунка.

[!code-cs[IsoCheckBox](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetIsoCheckBox)]

## <a name="optical-image-stabilization"></a>Оптическая стабилизация изображений

Оптическая стабилизация изображений (OIS) стабилизирует захваченный видеопоток с помощью механического управления аппаратным устройством захвата, что обеспечивает более высокое качество по сравнению с цифровой стабилизацией. На устройствах, которые не поддерживают оптическую стабилизацию изображений, можно использовать функцию VideoStabilizationEffect для выполнения цифровой стабилизации захваченного видео. Дополнительные сведения см. в статье [Эффекты для видеозахвата](effects-for-video-capture.md).

Чтобы узнать, поддерживается ли на устройстве оптическая стабилизация изображений, проверьте свойство [**OpticalImageStabilizationControl.Supported**](https://msdn.microsoft.com/library/windows/apps/dn926689).

Элемент управления OIS работает в трех режимах: "Включен", "Отключен" и "Автоматический режим". Устройство в динамическом режиме определяет, улучшит ли оптическая стабилизация захват мультимедиа, и, если это так, включает эту функцию. Если определенный режим содержится в коллекции [**OpticalImageStabilizationControl.SupportedModes**](https://msdn.microsoft.com/library/windows/apps/dn926690), то он поддерживается на устройстве.

Чтобы включить или отключить оптическую стабилизацию изображений, переведите [**OpticalImageStabilizationControl.Mode**](https://msdn.microsoft.com/library/windows/apps/dn926691) в нужный режим.

[!code-cs[SetOpticalImageStabilizationMode](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSetOpticalImageStabilizationMode)]

## <a name="powerline-frequency"></a>Частота линии питания
Некоторые камеры поддерживают обработку для защиты от мерцания, которая зависит от частоты переменного тока в текущей среде. Некоторые устройства поддерживают автоматическое определение частоты питания, а на других ее необходимо задать вручную. В следующем примере кода показано, как определить частоты питания устройстве и, если требуется, задать частоту вручную. 

Сначала вызовите метод [**TryGetPowerlineFrequency**](https://msdn.microsoft.com/library/windows/apps/br206898) класса **VideoDeviceController**, передав выходной параметр типа [**PowerlineFrequency**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Capture.PowerlineFrequency). Если вызов завершается ошибкой, управление частотой питания на текущем устройстве не поддерживается. Если функция поддерживается, вы можете определить, доступен ли на устройстве автоматический режим, попытавшись установить автоматический режим. Для этого, вызвав [**TrySetPowerlineFrequency**](https://msdn.microsoft.com/library/windows/apps/br206899) и передайте ему значение **Auto**. Если вызов выполнен успешно, это означает, что автоматическая установка частоты питания поддерживается. Если контроллер частоты питания поддерживается на устройстве, а автоматическое обнаружение частоты — нет, вы можете вручную задать частоту с помощью метода **TrySetPowerlineFrequency**. В этом примере **MyCustomFrequencyLookup** — это пользовательский метод, который реализуется, чтобы определить правильную частоту для текущего расположения устройства. 

[!code-cs[PowerlineFrequency](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetPowerlineFrequency)]

## <a name="white-balance"></a>Баланс белого

[**WhiteBalanceControl**](https://msdn.microsoft.com/library/windows/apps/dn279104) позволяет задать баланс белого для фото- или видеосъемки.

В этом примере используется элемент управления [**ComboBox**](https://msdn.microsoft.com/library/windows/apps/br209348) для выбора встроенных стандартных значений цветовой температуры и элемент управления [**Slider**](https://msdn.microsoft.com/library/windows/apps/br209614) для ручной настройки баланса белого.

[!code-xml[WhiteBalanceXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetWhiteBalanceXAML)]

Проверьте, поддерживает ли используемое устройство записи **WhiteBalanceControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn279120). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции. Установите для элементов поля со списком значения перечисления [**ColorTemperaturePreset**](https://msdn.microsoft.com/library/windows/apps/dn278894). Для выбранного элемента установите текущее значение свойства [**Preset**](https://msdn.microsoft.com/library/windows/apps/dn279110).

Для ручного элемента управления значение баланса белого должно находиться в поддерживаемом диапазоне устройства и изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://msdn.microsoft.com/library/windows/apps/dn279109), [**Max**](https://msdn.microsoft.com/library/windows/apps/dn279107) и [**Step**](https://msdn.microsoft.com/library/windows/apps/dn279119), которые используются для установки соответствующих свойств элемента управления ползунком. Перед включением ручного элемента управления убедитесь, что диапазон между минимальным и максимальным значениями больше, чем размер шага. В противном случае этот элемент управления не будет поддерживаться на текущем устройстве.

Задайте в качестве значения элемента управления ползунком текущее значение **WhiteBalanceControl** после отмены регистрации обработчика событий [**ValueChanged**](https://msdn.microsoft.com/library/windows/apps/br209737), чтобы событие не активировалось при установке значения.

[!code-cs[WhiteBalance](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetWhiteBalance)]

В обработчике событий [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/br209776) поля со списком для цветовой температуры получите выбранное в данный момент стандартное значение и задайте значение для элемента управления путем вызова [**SetPresetAsync**](https://msdn.microsoft.com/library/windows/apps/dn279113). Если выбрано значение, отличное от **Manual**, отключите ползунок ручной настройки баланса белого.

[!code-cs[WhiteBalanceComboBox](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetWhiteBalanceComboBox)]

В обработчике событий **ValueChanged** получите текущее значение элемента управления и установите значение баланса белого путем вызова метода [**SetValueAsync**](https://msdn.microsoft.com/library/windows/apps/dn278927).

[!code-cs[WhiteBalanceSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetWhiteBalanceSlider)]

> [!IMPORTANT]
> Регулировка баланса белого поддерживается только во время выполнения потока предварительного просмотра. Убедитесь, что поток предварительного просмотра выполняется, перед тем как настраивать баланс белого.

> [!IMPORTANT]
> Стандартное значение **ColorTemperaturePreset.Auto** сообщает системе, что необходимо автоматически настроить баланс белого. В некоторых случаях, например при записи последовательности фотографий, где уровни баланса белого должны совпадать для всех кадров, вы можете заблокировать элемент управления, зафиксировав текущее значение автоматической настройки. Для этого вызовите [**SetPresetAsync**](https://msdn.microsoft.com/library/windows/apps/dn279113) и укажите стандартное значение **Manual**, не задавая значение для элемента управления с помощью [**SetValueAsync**](https://msdn.microsoft.com/library/windows/apps/dn279114). Это приведет к блокировке текущего значения устройством. Не пытайтесь прочитать текущее значение элемента управления, а затем передать возвращенное значение в **SetValueAsync**, так как оно может оказаться неправильным.

## <a name="zoom"></a>Масштаб

[**ZoomControl**](https://msdn.microsoft.com/library/windows/apps/dn608149) позволяет задать масштаб для фото- или видеосъемки.

В этом примере используется элемент управления [**Slider**](https://msdn.microsoft.com/library/windows/apps/br209614) для настройки текущего значения масштаба. В следующем разделе показано, как выполняется масштабирование на основе жестов сжатия на экране.

[!code-xml[ZoomXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetZoomXAML)]

Проверьте, поддерживает ли используемое устройство записи **ZoomControl**, проверив свойство [**Supported**](https://msdn.microsoft.com/library/windows/apps/dn633819). Если этот элемент управления поддерживается, вы можете включить и отобразить пользовательский интерфейс для этой функции.

Значение масштаба должно находиться в поддерживаемом диапазоне устройства и должно изменяться шагами поддерживаемой величины. Получите поддерживаемые значения для текущего устройства путем проверки свойств [**Min**](https://msdn.microsoft.com/library/windows/apps/dn633817), [**Max**](https://msdn.microsoft.com/library/windows/apps/dn608150) и [**Step**](https://msdn.microsoft.com/library/windows/apps/dn633818), которые используются для установки соответствующих свойств элемента управления ползунком.

Задайте в качестве значения элемента управления ползунком текущее значение **ZoomControl** после отмены регистрации обработчика событий [**ValueChanged**](https://msdn.microsoft.com/library/windows/apps/br209737), чтобы событие не активировалось при установке значения.

[!code-cs[ZoomControl](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetZoomControl)]

В обработчике событий **ValueChanged** создайте новый экземпляр класса [**ZoomSettings**](https://msdn.microsoft.com/library/windows/apps/dn926722), задав для свойства [**Value**](https://msdn.microsoft.com/library/windows/apps/dn926724) текущее значение ползунка масштабирования. Если свойство [**SupportedModes**](https://msdn.microsoft.com/library/windows/apps/dn926721) элемента управления **ZoomControl** содержит [**ZoomTransitionMode.Smooth**](https://msdn.microsoft.com/library/windows/apps/dn926726), это означает, что устройство поддерживает плавные переходы между уровнями масштабирования. Поскольку этот режим улучшает взаимодействие с пользователем, это значение обычно используется для свойства [**Mode**](https://msdn.microsoft.com/library/windows/apps/dn926723) объекта **ZoomSettings**.

Наконец, измените текущие параметры масштабирования, передав объект **ZoomSettings** в метод [**Configure**](https://msdn.microsoft.com/library/windows/apps/dn926719) объекта **ZoomControl**.

[!code-cs[ZoomSlider](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetZoomSlider)]

### <a name="smooth-zoom-using-pinch-gesture"></a>Плавное масштабирование жестом сжатия

Как отмечалось в предыдущем разделе, на устройствах, поддерживающих соответствующую функцию, с помощью режима плавного масштабирования устройство захвата может плавно переходить от одного уровня цифрового масштабирования к другому. Это позволяет пользователю избежать резких переходов при динамическом изменении масштаба в процессе записи. В этом разделе описывается, как изменить масштаб в ответ на жест сжатия.

Для начала определите, поддерживается ли на текущем устройстве элемент управления цифровым масштабированием, проверив свойство [**ZoomControl.Supported**](https://msdn.microsoft.com/library/windows/apps/dn633819). Далее определите, доступен ли режим плавного масштабирования. Для этого проверьте, содержит ли свойство [**ZoomControl.SupportedModes**](https://msdn.microsoft.com/library/windows/apps/dn926721) значение [**ZoomTransitionMode.Smooth**](https://msdn.microsoft.com/library/windows/apps/dn926726).

[!code-cs[IsSmoothZoomSupported](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetIsSmoothZoomSupported)]

На устройствах, поддерживающих мультисенсорную технологию, обычный сценарий — это настройка коэффициента масштабирования на основе двухпальцевого жеста сжатия. Чтобы включить жест сжатия, установите для свойства [**ManipulationMode**](https://msdn.microsoft.com/library/windows/apps/br208948) элемента управления [**CaptureElement**](https://msdn.microsoft.com/library/windows/apps/br209278) значение [**ManipulationModes.Scale**](https://msdn.microsoft.com/library/windows/apps/br227934). Затем зарегистрируйте обработчик события [**ManipulationDelta**](https://msdn.microsoft.com/library/windows/apps/br208946), которое возникает при изменении масштаба с помощью жеста сжатия.

[!code-cs[RegisterPinchGestureHandler](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetRegisterPinchGestureHandler)]

В обработчике события **ManipulationDelta** обновите коэффициент масштабирования в зависимости от изменения жеста сжатия пользователя. Значение [**ManipulationDelta.Scale**](https://msdn.microsoft.com/library/windows/apps/br242016) указывает на изменение масштаба с помощью жеста сжатия. Например, небольшое увеличение масштаба обозначается значением, которое немного превышает 1, а небольшое уменьшение — значением, которое немного меньше 1. В этом примере текущее значение элемента управления масштабированием умножается на значение разницы масштаба.

Прежде чем задавать коэффициент масштабирования, убедитесь, что его значение не меньше минимального значения, которое поддерживается устройством и указано свойством [**ZoomControl.Min**](https://msdn.microsoft.com/library/windows/apps/dn633817). Кроме того, убедитесь, что значение коэффициента меньше или равно значению [**ZoomControl.Max**](https://msdn.microsoft.com/library/windows/apps/dn608150). Наконец необходимо убедиться, что коэффициент масштабирования является кратен размеру этапа масштабирования, поддерживаемых устройством, как указано в свойстве [**Шаг**](https://msdn.microsoft.com/library/windows/apps/dn633818) . Если коэффициент масштабирования не соответствует этим требованиям, при попытке задать масштаб на устройстве захвата будет отображено исключение.

Чтобы установить масштаб на устройстве захвата, создайте новый объект [**ZoomSettings**](https://msdn.microsoft.com/library/windows/apps/dn926722). Установите для свойства [**Mode**](https://msdn.microsoft.com/library/windows/apps/dn926723) значение [**ZoomTransitionMode.Smooth**](https://msdn.microsoft.com/library/windows/apps/dn926726), а затем укажите требуемый коэффициент масштабирования в свойстве [**Value**](https://msdn.microsoft.com/library/windows/apps/dn926724). После этого вызовите метод [**ZoomControl.Configure**](https://msdn.microsoft.com/library/windows/apps/dn926719), чтобы задать новое значение масштаба на устройстве. В результате устройство плавно перейдет на новый масштаб.

[!code-cs[ManipulationDelta](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetManipulationDelta)]

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
