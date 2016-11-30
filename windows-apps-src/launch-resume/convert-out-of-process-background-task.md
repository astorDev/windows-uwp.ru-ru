---
author: TylerMSFT
title: "Преобразование фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса"
description: "Преобразуйте фоновую задачу, которая выполняется вне процесса, в фоновую задачу, которая выполняется внутри процесса вашего приложения переднего плана."
translationtype: Human Translation
ms.sourcegitcommit: 7d1c160f8b725cd848bf8357325c6ca284b632ae
ms.openlocfilehash: b361a558ecef2030370590eedbef69bd04cf68bd

---

# Преобразование фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса

Самый простой способ преобразовать фоновую задачу, выполняемую вне процесса, в активность внутри процесса — это перенести код метода [IBackgroundTask.Run](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx?f=255&MSPPError=-2147217396) внутрь вашего приложения и инициировать его из метода [OnBackgroundActivated](https://msdn.microsoft.com/en-us/library/windows/apps/windows.ui.xaml.application.onbackgroundactivated.aspx).

Если в вашем приложении используются несколько фоновых задач, [пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BackgroundActivation) показывает, как можно использовать `BackgroundActivatedEventArgs.TaskInstance.Task.Name` для определения, какая задача инициируется.

Если в настоящее время производится обмен данными между фоновым процессом и процессом переднего плана, можно удалить код управления состоянием и код связи.

## Фоновые задачи и типы триггеров, преобразование которых невозможно

* Фоновые задачи внутри процесса не поддерживают активацию фоновой задачи VoIP.
* Фоновые задачи внутри процесса не поддерживают следующие триггеры: [DeviceUseTrigger](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.background.deviceusetrigger.aspx?f=255&MSPPError=-2147217396), [DeviceServicingTrigger](https://msdn.microsoft.com/en-us/library/windows/apps/windows.applicationmodel.background.deviceservicingtrigger.aspx) и **IoTStartupTask**



<!--HONumber=Nov16_HO1-->


