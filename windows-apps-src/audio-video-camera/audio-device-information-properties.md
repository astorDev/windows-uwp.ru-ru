---
ms.assetid: 3b75d881-bdcf-402b-a330-23cd29d68e53
description: В этой статье перечислены свойства DeviceInformation, связанные со звуковыми устройствами
title: Свойства сведений о звуковых устройствах
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 443bcc3c0280aca85de31d8c9f3704302432cb76
ms.sourcegitcommit: b11f305dbf7649c4b68550b666487c77ea30d98f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "7840007"
---
# <a name="audio-device-information-properties"></a>Свойства сведений о звуковых устройствах

В этой статье перечислены свойства сведений об устройствах, связанные со звуковыми устройствами. В Windows с каждым аппаратным устройством связаны свойства [**DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/BR225393), содержащие подробные сведения об устройстве. Вы можете использовать их для получения определенной информации об устройстве или при создании средства выбора устройств. Общие сведения о перечислении устройств в Windows можно найти в разделах [Перечисление устройств](../devices-sensors/enumerate-devices.md) и [Свойства сведений об устройствах](../devices-sensors/device-information-properties.md).


|Имя|Тип|Описание|
|------------------------------------------------------------|------------|------------------------------------------------------|
|**System.Devices.AudioDevice.Microphone.SensitivityInDbfs**|Double|Определяет чувствительность микрофона в децибелах относительно полной шкалы (dBFS).|
|**System.Devices.AudioDevice.Microphone.SignalToNoiseRatioInDb**|Double|Определяет отношения сигнал-шум (SNR) микрофона, измеренное в децибелах (дБ).|
|**System.Devices.AudioDevice.SpeechProcessingSupported**|Логическое|Указывает, поддерживает ли звуковое устройство обработку речи.|
|**System.Devices.AudioDevice.RawProcessingSupported**|Логическое|Указывает, поддерживает ли звуковое устройство обработку необработанных данных.|
|**System.Devices.MicrophoneArray.Geometry**|неподписанный символ[]|Геометрические данные для набора микрофонов.|

## <a name="related-topics"></a>Еще по теме

* [Перечисление устройств](../devices-sensors/enumerate-devices.md)
* [Свойства сведений об устройствах](../devices-sensors/device-information-properties.md)
* [Создание средства выбора устройств](../devices-sensors/build-a-device-selector.md)
* [Воспроизведение мультимедиа](media-playback.md)




