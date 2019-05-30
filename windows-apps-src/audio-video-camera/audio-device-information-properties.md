---
ms.assetid: 3b75d881-bdcf-402b-a330-23cd29d68e53
description: В этой статье перечислены свойства DeviceInformation, связанные со звуковыми устройствами
title: Свойства сведений о звуковых устройствах
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 97b6bd0c3567c00902a9528d54e6417f41ac66ed
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66359098"
---
# <a name="audio-device-information-properties"></a>Свойства сведений о звуковых устройствах

В этой статье перечислены свойства сведений об устройствах, связанные со звуковыми устройствами. В Windows с каждым аппаратным устройством связаны свойства [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation), содержащие подробные сведения об устройстве. Вы можете использовать их для получения определенной информации об устройстве или при создании средства выбора устройств. Общие сведения о перечислении устройств в Windows можно найти в разделах [Перечисление устройств](../devices-sensors/enumerate-devices.md) и [Свойства сведений об устройствах](../devices-sensors/device-information-properties.md).


|Имя|Тип|Описание|
|------------------------------------------------------------|------------|------------------------------------------------------|
|**System.Devices.AudioDevice.Microphone.SensitivityInDbfs**|Double|Определяет чувствительность микрофона в децибелах относительно полной шкалы (dBFS).|
|**System.Devices.AudioDevice.Microphone.SignalToNoiseRatioInDb**|Double|Определяет отношения сигнал-шум (SNR) микрофона, измеренное в децибелах (дБ).|
|**System.Devices.AudioDevice.SpeechProcessingSupported**|Boolean (Логическое)|Указывает, поддерживает ли звуковое устройство обработку речи.|
|**System.Devices.AudioDevice.RawProcessingSupported**|Boolean (Логическое)|Указывает, поддерживает ли звуковое устройство обработку необработанных данных.|
|**System.Devices.MicrophoneArray.Geometry**|неподписанный символ[]|Геометрические данные для набора микрофонов.|

## <a name="related-topics"></a>См. также

* [Перечисление устройств](../devices-sensors/enumerate-devices.md)
* [Свойства сведений об устройстве](../devices-sensors/device-information-properties.md)
* [Создание селектора устройства](../devices-sensors/build-a-device-selector.md)
* [Воспроизведение мультимедиа](media-playback.md)




