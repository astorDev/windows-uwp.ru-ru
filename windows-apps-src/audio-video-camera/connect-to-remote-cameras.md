---
ms.assetid: ''
description: В этой статье показано, как подключиться к удаленным камерам и получить Медиафрамесаурцеграуп для получения кадров с каждой камеры.
title: Подключение к удаленным камерам
ms.date: 04/19/2019
ms.topic: article
ms.custom: 19H1
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c7b876cff994f775b770d22c103d27271047b269
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75683637"
---
# <a name="connect-to-remote-cameras"></a>Подключение к удаленным камерам

В этой статье показано, как подключиться к одной или нескольким удаленным камерам и получить объект [**медиафрамесаурцеграуп**](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameSourceGroup) , позволяющий считывать кадры с каждой камеры. Дополнительные сведения о чтении кадров из источника мультимедиа см. в разделе [Обработка кадров мультимедиа с помощью медиафрамереадер](process-media-frames-with-mediaframereader.md). Дополнительные сведения о связывании с устройствами см. в разделе [связывание устройств](https://docs.microsoft.com/windows/uwp/devices-sensors/pair-devices).

> [!NOTE] 
> Функции, описанные в этой статье, доступны только начиная с Windows 10 версии 1903.

## <a name="create-a-devicewatcher-class-to-watch-for-available-remote-cameras"></a>Создание класса Девицеватчер для отслеживания доступных удаленных камер

Класс [**девицеватчер**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher) отслеживает устройства, доступные для приложения, и уведомляет ваше приложение о добавлении или удалении устройств. Получите экземпляр **девицеватчер** , вызвав [**девицеинформатион. креатеватчер**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.createwatcher#Windows_Devices_Enumeration_DeviceInformation_CreateWatcher_System_String_), передав строку расширенного синтаксиса запросов (АКС), определяющую тип устройств, которые требуется отслеживать. Ниже приведены строки АКС, указывающие устройства сетевой камеры.

```
@"System.Devices.InterfaceClassGuid:=""{B8238652-B500-41EB-B4F3-4234F7F5AE99}"" AND System.Devices.InterfaceEnabled:=System.StructuredQueryType.Boolean#True"
```

> [!NOTE] 
> Вспомогательный метод [**медиафрамесаурцеграуп. жетдевицеселектор**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.getdeviceselector) ВОЗВРАЩАЕТ строку АКС, которая будет отслеживать локально подключенные и удаленные сетевые камеры. Чтобы отслеживать только сетевые камеры, следует использовать строку АКС, показанную выше.


При запуске возвращенного **девицеватчер** путем вызова метода [**Start**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.start) будет вызвано [**Добавление**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.added) события для каждой доступной в данный момент сетевой камеры. Пока наблюдатель не будет приостановлен с помощью вызова функции " [**Завершение**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.stop)", **добавленное** событие будет вызвано, когда становятся доступными новые устройства сетевой камеры, а [**Удаленное**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.removed) событие будет вызвано при недоступности устройства камеры.

Аргументы события, передаваемые в обработчики событий **добавления** и **удаления** , — это объект [**девицеинформатион**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) или [**девицеинформатионупдате**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformationupdate) , соответственно. У каждого из этих объектов есть свойство **ID** , которое является идентификатором сетевой камеры, для которой было запущено событие. Передайте этот идентификатор в метод [**медиафрамесаурцеграуп. фромидасинк**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.fromidasync) , чтобы получить объект [**медиафрамесаурцеграуп**](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.fromidasync) , который можно использовать для получения кадров от камеры.

## <a name="remote-camera-pairing-helper-class"></a>Вспомогательный класс для связывания камер удаленной камеры

В следующем примере показан вспомогательный класс, который использует **девицеватчер** для создания и обновления **ObservableCollection** объектов **медиафрамесаурцеграуп** для поддержки привязки данных к списку камер. Типичные приложения заключают **медиафрамесаурцеграуп** в пользовательский класс модели. Обратите внимание, что вспомогательный класс поддерживает ссылку на [**CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) приложения и обновляет коллекцию камер в рамках вызовов [**RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync) , чтобы обеспечить обновление пользовательского интерфейса, привязанного к коллекции, в потоке пользовательского интерфейса.

Кроме того, в этом примере обрабатывается событие [**девицеватчер. Updates**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.updated) в дополнение к **добавленным** и **удаляемым** событиям. В **обновленном** обработчике связанное устройство удаленной камеры удаляется из, а затем добавляется обратно в коллекцию.

[!code-cs[SnippetRemoteCameraPairingHelper](./code/Frames_Win10/Frames_Win10/RemoteCameraPairingHelper.cs#SnippetRemoteCameraPairingHelper)]


## <a name="related-topics"></a>Связанные темы

* [Камера](camera.md)
* [Базовая фотография, видео и запись звука с помощью Медиакаптуре](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Пример кадров камеры](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CameraFrames)
* [Обработка кадров мультимедиа с помощью Медиафрамереадер](process-media-frames-with-mediaframereader.md)
 

 




