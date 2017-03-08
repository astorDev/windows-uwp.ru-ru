---
author: TylerMSFT
title: "Преобразование фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса"
description: "Преобразуйте фоновую задачу, которая выполняется вне процесса, в фоновую задачу, которая выполняется внутри процесса вашего приложения переднего плана."
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 5327e966-b78d-4859-9b97-5a61c362573e
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: f67d3ea2293e50a04bdbb4277fa4ad9e46834473
ms.lasthandoff: 02/08/2017

---

# <a name="convert-an-out-of-process-background-task-to-an-in-process-background-task"></a>Преобразование фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса

Самый простой способ преобразовать фоновую задачу, выполняемую вне процесса, в активность внутри процесса — это перенести код метода [IBackgroundTask.Run](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx?f=255&MSPPError=-2147217396) внутрь вашего приложения и инициировать его из метода [OnBackgroundActivated](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onbackgroundactivated.aspx).

Если в вашем приложении используются несколько фоновых задач, [пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BackgroundActivation) показывает, как можно использовать `BackgroundActivatedEventArgs.TaskInstance.Task.Name` для определения, какая задача инициируется.

Если в настоящее время производится обмен данными между фоновым процессом и процессом переднего плана, можно удалить код управления состоянием и код связи.

## <a name="background-tasks-and-trigger-types-that-cannot-be-converted"></a>Фоновые задачи и типы триггеров, преобразование которых невозможно

* Фоновые задачи внутри процесса не поддерживают активацию фоновой задачи VoIP.
* Фоновые задачи внутри процесса не поддерживают следующие триггеры: [DeviceUseTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceusetrigger.aspx?f=255&MSPPError=-2147217396), [DeviceServicingTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceservicingtrigger.aspx) и **IoTStartupTask**

