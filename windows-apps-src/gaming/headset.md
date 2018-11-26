---
title: Гарнитура
description: Используйте API Windows.Gaming.Input гарнитуры для обнаружения гарнитур, записи голоса игрока и воспроизведения звука.
ms.assetid: 021CCA26-D339-4C8B-B084-0D499BD83ABE
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, гарнитура
ms.localizationpriority: medium
ms.openlocfilehash: b3de68cc59c9928a52eba5caeb840e9e825eecf0
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "7691918"
---
# <a name="headset"></a>Гарнитура

На этой странице приведены основные принципы программирования для гарнитур с помощью API-интерфейсов [Windows.Gaming.Input.Headset][гарнитуры] и связанных API для универсальной платформы Windows (UWP).

Прочитав эту страницу, вы узнаете:
* Как получить доступ к гарнитуре, подключенной к устройству навигации или ввода.
* Как определить, что гарнитура была подключена или отключена.


## <a name="headset-overview"></a>Обзор гарнитуры

Гарнитуры— это устройства захвата и воспроизведения звука, которые чаще всего используются для связи с другими игроками в онлайн-играх, однако некоторые модели могут также использоваться в игровом процессе или другим оригинальным образом. Поддержка гарнитур в приложениях UWP для Windows 10 и Xbox реализована с помощью пространства имен [Windows.Gaming.Input][].


## <a name="detect-and-track-headsets"></a>Обнаружение и отслеживание гарнитур

Гарнитурами управляет система, поэтому их не требуется создавать или инициализировать. Система предоставляет доступ к гарнитуре через устройство ввода, к которому она подключена, а также к событиям, которые будут уведомлять вас, когда гарнитура подключена или отключена.

### <a name="igamecontrollerheadset"></a>IGameController.Headset

Все устройства в ввода в пространстве имен [Windows.Gaming.Input][] реализуют интерфейс [IGameController][], который определяет свойство [Headset][igamecontroller.headset], представляющее собой гарнитуру, которая в данный момент подключена к устройству.

### <a name="connecting-and-disconnecting-headsets"></a>Подключение и отключение гарнитур

При подключении или отключении гарнитуры вызываются события [HeadsetConnected][igamecontroller.headsetconnected] и [HeadsetDisconnected][igamecontroller.headsetdisconnected]. Вы можете зарегистрировать обработчики для этих событий, чтобы отслеживать, подключена ли в настоящий момент к устройству ввода гарнитура или нет.

В следующем примере кода показано, как зарегистрировать обработчик для события `HeadsetConnected`.

```cpp
auto inputDevice = myGamepads[0]; // or arcade stick, racing wheel

inputDevice.HeadsetConnected += ref new TypedEventHandler<IGameController^, Headset^>(IGameController^ device, Headset^ headset)
{
    // enable headset capture and playback on this device
}
```

В следующем примере кода показано, как зарегистрировать обработчик для события `HeadsetDisconnected`.

```cpp
auto inputDevice = myGamepads[0]; // or arcade stick, racing wheel

inputDevice.HeadsetDisconnected += ref new TypedEventHandler<IGameController^, Headset^>(IGameController^ device, Headset^ headset)
{
    // disable headset capture and playback on this device
}
```

## <a name="using-the-headset"></a>Использование гарнитуры

Класс [Headset][] состоит из двух строк, представляющих идентификаторы конечной точки XAudio— один для записи звука (запись с микрофона гарнитуры) и другой для обработки звука (воспроизведения через наушник гарнитуры).

Инструкции по работе с XAudio в этом разделе не приводятся. Дополнительные сведения см. в разделе [Руководство по программированию для XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415737.aspx) и [Справочник по API XAudio2](https://msdn.microsoft.com/library/windows/desktop/ee415899.aspx).


[Windows.Gaming.Input]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.aspx
[igamecontroller]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.aspx
[igamecontroller.headset]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.headset.aspx
[igamecontroller.headsetconnected]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.headsetconnected.aspx
[igamecontroller.headsetdisconnected]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.headsetdisconnected.aspx
[headset]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.headset.aspx
