---
author: TylerMSFT
title: Перенос фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса
description: Перенос вне процесса фоновую задачу внутри процесса фоновую задачу, которая выполняется внутри процесса вашего приложения переднего плана.
ms.author: twhitney
ms.date: 09/19/2018
ms.topic: article
keywords: Windows 10, uwp, фоновая задача, службы приложений
ms.assetid: 5327e966-b78d-4859-9b97-5a61c362573e
ms.localizationpriority: medium
ms.openlocfilehash: 47008fd7ba0b7724aa8fbdc2dd6cbd55288faea0
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "5923373"
---
# <a name="port-an-out-of-process-background-task-to-an-in-process-background-task"></a>Перенос фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса

Самый простой способ перенести вне процесса (OOP) фоновую активность для обработки действий — это перенести код метода [IBackgroundTask.Run](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx?f=255&MSPPError=-2147217396) внутрь вашего приложения и инициировать его из [OnBackgroundActivated](/uwp/api/windows.ui.xaml.application.onbackgroundactivated). Технология описываемого здесь не является о создании оболочка из фоновой задачи, выполняемой OOP задачи, выполняемой внутри процесса; его сведения о перезаписи (или перенос) для версии OOP в версии внутри процесса.

Если в вашем приложении используются несколько фоновых задач, [пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BackgroundActivation) показывает, как можно использовать `BackgroundActivatedEventArgs.TaskInstance.Task.Name` для определения, какая задача инициируется.

Если в настоящее время производится обмен данными между фоновым процессом и процессом переднего плана, можно удалить код управления состоянием и код связи.

## <a name="background-tasks-and-trigger-types-that-cannot-be-converted"></a>Фоновые задачи и типы триггеров, преобразование которых невозможно

* Фоновые задачи внутри процесса не поддерживают активацию фоновой задачи VoIP.
* Фоновые задачи внутри процесса не поддерживают следующие триггеры: [DeviceUseTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceusetrigger.aspx?f=255&MSPPError=-2147217396), [DeviceServicingTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceservicingtrigger.aspx) и **IoTStartupTask**
