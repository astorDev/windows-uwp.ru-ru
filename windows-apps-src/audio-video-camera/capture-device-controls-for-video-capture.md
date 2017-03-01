---
author: drewbatgit
ms.assetid: 708170E1-777A-4E4A-9F77-5AB28B88B107
description: "В этой статье рассказывается, как с помощью ручных элементов управления устройства включить расширенные сценарии видеозахвата, в том числе видеосъемку с расширенным динамическим диапазоном (HDR) и приоритетом экспозиции."
title: "Ручные элементы управления для видеозахвата на камере"
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: cd6326ebad94c33fd03bf39f2dfd11f1c27e9b37
ms.lasthandoff: 02/07/2017

---

# <a name="manual-camera-controls-for-video-capture"></a>Ручные элементы управления для видеозахвата на камере

\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


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

## <a name="related-topics"></a>Связанные статьи

* [Камера](camera.md)
* [Основные принципы фото-, аудио- и видеозахвата с помощью MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
 

 





