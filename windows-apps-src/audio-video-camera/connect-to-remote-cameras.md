---
ms.assetid: ''
description: В этой статье показано, как подключиться к удаленной камеры и получить MediaFrameSourceGroup для получения кадров из каждой камеры.
title: Подключение к удаленной камеры
ms.date: 04/19/2019
ms.topic: article
ms.custom: 19H1
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: bc719b8dad2adef0542edf284d257846052eac21
ms.sourcegitcommit: fca0132794ec187e90b2ebdad862f22d9f6c0db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63789589"
---
# <a name="connect-to-remote-cameras"></a>Подключение к удаленной камеры

В этой статье показано, как подключиться к одной или нескольких удаленных камеры и получить [ **MediaFrameSourceGroup** ](https://docs.microsoft.com/uwp/api/Windows.Media.Capture.Frames.MediaFrameSourceGroup) объект, который позволяет считывать кадры из каждой камеры. Дополнительные сведения о чтении кадры из источника мультимедиа см. в разделе [обработки мультимедиа кадров с MediaFrameReader](process-media-frames-with-mediaframereader.md). Дополнительные сведения о связывании с устройствами, см. в разделе [пары устройств](https://docs.microsoft.com/windows/uwp/devices-sensors/pair-devices).

> [!NOTE] 
> Компоненты, обсуждаемые в этой статье доступны только начиная с Windows 10, версия 1903 года.

## <a name="create-a-devicewatcher-class-to-watch-for-available-remote-cameras"></a>Создайте класс DeviceWatcher для наблюдения за доступные удаленного камеры

[ **DeviceWatcher** ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher) класс выполняет мониторинг устройств, доступной для приложения и уведомляет приложение, когда устройства добавляются или удаляются. Получить экземпляр **DeviceWatcher** путем вызова [ **DeviceInformation.CreateWatcher**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.createwatcher#Windows_Devices_Enumeration_DeviceInformation_CreateWatcher_System_String_), передав строку расширенный синтаксис запроса (AQS), определяющий тип устройства, которые вы хотите отслеживать. Ниже приводится AQS строка, задающая сетевые камеры устройства:

```
@"System.Devices.InterfaceClassGuid:=""{B8238652-B500-41EB-B4F3-4234F7F5AE99}"" AND System.Devices.InterfaceEnabled:=System.StructuredQueryType.Boolean#True"
```

> [!NOTE] 
> Вспомогательный метод [ **MediaFrameSourceGroup.GetDeviceSelector** ](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.getdeviceselector) возвращает строку AQS, которое будет отслеживать локально подключенных и удаленный сетевой камеры. Чтобы отслеживать только сетевые камеры, следует использовать строку AQS, показанный выше.


При запуске возвращенного **DeviceWatcher** путем вызова [ **запустить** ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.start) метод, он будет вызывать [ **Added** ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.added) событий для каждой сети камеры, который в данный момент доступна. Пока вы не остановите наблюдатель путем вызова [ **остановить**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.stop), **Added** событие будет вызываться, когда станут доступны новые сетевые камеры устройства и [ **Удалено** ](https://docs.microsoft.com/en-us/uwp/api/windows.devices.enumeration.devicewatcher.removed) событие будет вызываться при недоступности камеры устройства.

Переданные аргументы события **Added** и **удалено** обработчики событий являются [ **DeviceInformation** ](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) или [  **DeviceInformationUpdate** ](https://docs.microsoft.com/en-us/uwp/api/windows.devices.enumeration.deviceinformationupdate) соответственно. Каждый из этих объектов имеет **идентификатор** свойство, являющееся идентификатором для камеры сети, для которой произошло событие. Передайте этот идентификатор в [ **MediaFrameSourceGroup.FromIdAsync** ](https://docs.microsoft.com/uwp/api/windows.media.capture.frames.mediaframesourcegroup.fromidasync) метод для получения [ **MediaFrameSourceGroup** ](https://docs.microsoft.com/en-us/uwp/api/windows.media.capture.frames.mediaframesourcegroup.fromidasync) объект, который можно использовать для получить кадры из камеры.

## <a name="remote-camera-pairing-helper-class"></a>Вспомогательный класс удаленного камеры для связывания

В примере показан вспомогательный класс, который использует **DeviceWatcher** для создания и обновления **ObservableCollection** из **MediaFrameSourceGroup** объектов для поддержки Привязка данных к списку камеры. Типичные приложения будет переносить **MediaFrameSourceGroup** в класс пользовательской модели. Обратите внимание, что вспомогательный класс хранит ссылку на приложение [ **CoreDispatcher** ](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) и обновляет коллекцию камеры в вызовы [ **RunAsync** ](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync) чтобы гарантировать обновление пользовательского интерфейса, привязанным к коллекции в потоке пользовательского интерфейса.

Кроме того, в этом примере обрабатывается [ **DeviceWatcher.Updated** ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.updated) событий в дополнение к **Added** и **удалено** события. В **Updated** удаляется из обработчика, связанный удаленный камеры устройства и затем добавляются к коллекции.

[!code-cs[SnippetRemoteCameraPairingHelper](./code/Frames_Win10/Frames_Win10/RemoteCameraPairingHelper.cs#SnippetRemoteCameraPairingHelper)]


## <a name="related-topics"></a>См. также

* [Камера](camera.md)
* [Основные фото, видео и аудио захвата с MediaCapture](basic-photo-video-and-audio-capture-with-MediaCapture.md)
* [Пример кадров камеры](https://go.microsoft.com/fwlink/?LinkId=823230)
* [Обработка мультимедиа кадров с MediaFrameReader](process-media-frames-with-mediaframereader.md)
 

 




