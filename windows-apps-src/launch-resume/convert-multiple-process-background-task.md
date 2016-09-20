---
author: TylerMSFT
title: "Преобразование фоновой задачи с несколькими процессами в фоновую задачу с одним процессом"
description: "Преобразуйте фоновую задачу, которая выполняется в отдельном процессе, в фоновую задачу, которая выполняется внутри процесса вашего приложения переднего плана."
translationtype: Human Translation
ms.sourcegitcommit: 2c34ca40d3c930254500477ab5a2e41e5206d823
ms.openlocfilehash: e342667347cf3b89a5aa193495cbf7195263b276

---

# Преобразование фоновой задачи с несколькими процессами в фоновую задачу с одним процессом

Самый простой способ преобразовать фоновую операцию с несколькими процессами в один процесс— это перенести код метода [IBackgroundTask.Run](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx?f=255&MSPPError=-2147217396) внутрь вашего приложения и инициировать его из метода [OnBackgroundActivated](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.application.onbackgroundactivated.aspx).

Если в вашем приложении используются несколько фоновых задач, [пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BackgroundActivation) показывает, как можно использовать `BackgroundActivatedEventArgs.TaskInstance.Task.Name` для определения, какая задача инициируется.

Если в настоящее время производится обмен данными между фоновым процессом и процессом переднего плана, можно удалить код управления состоянием и код связи.

## Фоновые задачи и типы триггеров, преобразование которых невозможно

* Фоновые задачи с одним процессом не поддерживают активацию фоновой задачи VoIP.
* Фоновые задачи с одним процессом не поддерживают следующие триггеры: [DeviceUseTrigger](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.background.deviceusetrigger.aspx?f=255&MSPPError=-2147217396), [DeviceServicingTrigger](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.background.deviceservicingtrigger.aspx) и **IoTStartupTask**



<!--HONumber=Aug16_HO3-->


