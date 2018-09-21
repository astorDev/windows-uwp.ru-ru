---
author: TylerMSFT
title: Перенос вне процесса фоновую задачу в фоновую задачу в процессе
description: Перенос вне процесса фоновые задачи, выполняемой внутри процесса фоновую задачу, которая выполняется внутри процесса вашего приложения переднего плана.
ms.author: twhitney
ms.date: 09/19/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, фоновая задача, службы приложений
ms.assetid: 5327e966-b78d-4859-9b97-5a61c362573e
ms.localizationpriority: medium
ms.openlocfilehash: b9010f82b0460bd46757bc1e0d58c01dec459104
ms.sourcegitcommit: 5dda01da4702cbc49c799c750efe0e430b699502
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/21/2018
ms.locfileid: "4114420"
---
# <a name="port-an-out-of-process-background-task-to-an-in-process-background-task"></a>Перенос вне процесса фоновую задачу в фоновую задачу в процессе

Самый простой способ перенести вне процесса (OOP) фоновую активность внутри процесса действие — это перенести код метода [IBackgroundTask.Run](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx?f=255&MSPPError=-2147217396) внутрь вашего приложения и инициировать его из [OnBackgroundActivated](/uwp/api/windows.ui.xaml.application.onbackgroundactivated). Технология описываемого здесь не является о создании оболочка из фоновой задачи, выполняемой OOP в процесс фоновой задачи; его о перезаписи (или перенос) для версии OOP в версии внутри процесса.

Если в вашем приложении используются несколько фоновых задач, [пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BackgroundActivation) показывает, как можно использовать `BackgroundActivatedEventArgs.TaskInstance.Task.Name` для определения, какая задача инициируется.

Если в настоящее время производится обмен данными между фоновым процессом и процессом переднего плана, можно удалить код управления состоянием и код связи.

## <a name="background-tasks-and-trigger-types-that-cannot-be-converted"></a>Фоновые задачи и типы триггеров, преобразование которых невозможно

* Фоновые задачи внутри процесса не поддерживают активацию фоновой задачи VoIP.
* Фоновые задачи внутри процесса не поддерживают следующие триггеры: [DeviceUseTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceusetrigger.aspx?f=255&MSPPError=-2147217396), [DeviceServicingTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceservicingtrigger.aspx) и **IoTStartupTask**
