---
ms.assetid: 708170E1-777A-4E4A-9F77-5AB28B88B107
description: В этой статье рассказывается, как с помощью ручных элементов управления устройства включить расширенные сценарии видеозахвата, в том числе видеосъемку с расширенным динамическим диапазоном (HDR) и приоритетом экспозиции.
title: Ручные элементы управления для видеозахвата на камере
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f144ef398fc55e79d2f0190c61214cdf1aa93b68
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8758604"
---
# <a name="manual-camera-controls-for-video-capture"></a>Ручные элементы управления для видеозахвата на камере



В этой статье рассказывается, как с помощью ручных элементов управления устройства включить расширенные сценарии видеозахвата, в том числе видеосъемку с расширенным динамическим диапазоном (HDR) и приоритетом экспозиции.

Описанные в ней элементы управления видеоустройством добавляются в приложение с помощью одного и того же шаблона. Для начала проверьте, поддерживается ли элемент управления на устройстве, на котором выполняется приложение. Если это так, установите для элемента управления нужный режим. Как правило, если элемент управления не поддерживается на текущем устройстве, необходимо отключить или скрыть элемент пользовательского интерфейса, который позволяет пользователю включать соответствующую функцию.

Все API элементов управления устройства, описанные в этой статье, входят в пространство имен [**Windows.Media.Devices**](https://msdn.microsoft.com/library/windows/apps/br206902).

[!code-cs[VideoControllersUsing](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetVideoControllersUsing)]

> [!NOTE] 
> В этой статье используются понятия и код из статьи [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md), в которой описаны этапы реализации основных принципов фото- и видеозахвата. Мы рекомендуем ознакомиться с базовым шаблоном захвата мультимедиа в этой статье, прежде чем перейти к более сложным сценариям захвата. Код в этой статье подразумевает, что ваше приложение уже содержит экземпляр MediaCapture, инициализированный надлежащим образом.

## <a name="hdr-video"></a>Видео HDR

Функция расширенного динамического диапазона (HDR) для видео позволяет применять к видеопотоку устройства захвата обработку HDR. Чтобы определить, поддерживается ли видео HDR, проверьте свойство [**HdrVideoControl.Supported**](https://msdn.microsoft.com/library/windows/apps/dn926682).

Элемент управления видео HDR работает в трех режимах "Включен", "Отключен" и "Автоматический режим". Устройство в динамическом режиме определяет, улучшит ли обработка видео HDR захват мультимедиа, и, если это так, включает функцию HDR. Чтобы определить, поддерживается ли на текущем устройстве определенный режим, проверьте, доступен ли он в коллекции [**HdrVideoControl.SupportedModes**](https://msdn.microsoft.com/library/windows/apps/dn926683).

Чтобы включить или отключить обработку видео HDR, переведите свойство [**HdrVideoControl.Mode**](https://msdn.microsoft.com/library/windows/apps/dn926681) в нужный режим.

[!code-cs[SetHdrVideoMode](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetSetHdrVideoMode)]

## <a name="exposure-priority"></a>Приоритет выдержки

Если включен элемент [**ExposurePriorityVideoControl**](https://msdn.microsoft.com/library/windows/apps/dn926644), он оценивает кадры видео с устройства захвата, чтобы определить, записывается ли видео при низкой освещенности. Если это так, элемент управления снижает частоту кадров захватываемого видео, чтобы увеличить выдержку каждого кадра и улучшить визуальное качество видео.

Чтобы определить, поддерживается ли на текущем устройстве элемент управления приоритетом выдержки, проверьте свойство [**ExposurePriorityVideoControl.Supported**](https://msdn.microsoft.com/library/windows/apps/dn926647).

Чтобы включить или отключить этот элемент управления, переведите свойство [**ExposurePriorityVideoControl.Enabled**](https://msdn.microsoft.com/library/windows/apps/dn926646) в нужный режим.

[!code-cs[EnableExposurePriority](./code/BasicMediaCaptureWin10/cs/MainPage.xaml.cs#SnippetEnableExposurePriority)]

## <a name="temporal-denoising"></a>Временное шумоподавление
Начиная с Windows 10 версии 1803 вы можете включить временное шумоподавление для видео на устройствах, которые его поддерживают. Эта функция объединяет данные из нескольких смежных кадров в режиме реального времени для получения видеокадров без шумов.

[**VideoTemporalDenoisingControl**](https://docs.microsoft.com/uwp/api/windows.media.devices.videotemporaldenoisingcontrol) позволяет приложению определить, поддерживается ли временное шумоподавление на текущем устройстве, и, если да, какие режимы шумоподавления поддерживаются. Доступны следующие режимы шумоподавления: [**Off**](https://docs.microsoft.com/uwp/api/windows.media.devices.videotemporaldenoisingmode), [**On**](https://docs.microsoft.com/uwp/api/windows.media.devices.videotemporaldenoisingmode) и [**Auto**](https://docs.microsoft.com/uwp/api/windows.media.devices.videotemporaldenoisingmode). Устройство может не поддерживать все режимы, но каждое устройство должно поддерживать режим **Auto** или **On** и **Off**.

В следующем примере представлен простой пользовательский интерфейс для предоставления переключателей, которые позволяют пользователю переключаться между режимами шумоподавления.

[!code-cs[SnippetDenoiseXAML](./code/BasicMediaCaptureWin10/cs/MainPage.xaml#SnippetDenoiseXAML)]

В следующем методе проверяется свойство [**VideoTemporalDenoisingControl.Supported**](https://docs.microsoft.com/uwp/api/windows.media.devices.videotemporaldenoisingcontrol.supported), чтобы определить, поддерживается ли временное шумоподавление на текущем устройстве. Если это так, мы проверяем, поддерживаются ли режимы **Off** и **Auto** или **On**. Если это так, мы отображаем наши переключатели. Затем кнопки **Auto** и **On** становятся видимыми, если эти методы поддерживаются.

[!code-cs[SnippetUpdateDenoiseCapabilities](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetUpdateDenoiseCapabilities)]

В обработчике события **Checked** для переключателей проверяется имя кнопки и устанавливается соответствующий режим с помощью свойства [**VideoTemporalDenoisingControl.Mode**](https://docs.microsoft.com/uwp/api/windows.media.devices.videotemporaldenoisingcontrol.mode).

[!code-cs[SnippetDenoiseButtonChecked](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetDenoiseButtonChecked)]

### <a name="disabling-temporal-denoising-while-processing-frames"></a>Отключение временного шумоподавления при обработке кадров
Видео, которое было обработано с помощью временного шумоподавления, может быть более приятным для просмотра. Однако поскольку временное шумоподавление может повлиять на согласованность изображения и уменьшить объем деталей в кадре, приложения, которые используют обработку изображений на кадрах, например регистрацию или распознавание символов, могут программно отключать шумоподавление, если обработка изображений включена.

Следующий пример определяет, какие режимы шумоподавления поддерживаются, и сохраняет эту информацию в переменных класса.

[!code-cs[SnippetDenoiseFrameReaderVars](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetDenoiseFrameReaderVars)]

[!code-cs[SnippetDenoiseCapabilitiesForFrameProcessing](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetDenoiseCapabilitiesForFrameProcessing)]

Если приложение включает обработку кадров, режиму шумоподавления присваивается значение **Off**, если этот режим поддерживается, чтобы при обработке кадров можно было использовать необработанные кадры.

[!code-cs[SnippetEnableFrameProcessing](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetEnableFrameProcessing)]

Если приложение отключает обработку кадра, для режима шумоподавления устанавливается значение **On** или **Auto** в зависимости от того, какой режим поддерживается.

[!code-cs[SnippetDisableFrameProcessing](./code/BasicMediaCaptureWin10/cs/MainPage.ManualControls.xaml.cs#SnippetDisableFrameProcessing)]

Дополнительные сведения о получении видеокадров для обработки изображений см. в разделе [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md).

## <a name="related-topics"></a>Статьи по теме

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Обработка кадров мультимедиа с помощью MediaFrameReader](process-media-frames-with-mediaframereader.md)
*  [**VideoTemporalDenoisingControl**](https://docs.microsoft.com/uwp/api/windows.media.devices.videotemporaldenoisingcontrol)
 




