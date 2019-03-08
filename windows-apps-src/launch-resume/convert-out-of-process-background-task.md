---
title: Перенос фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса
description: Порт ожидания процесса фоновой задачи в процессе фоновой задачи, которая выполняется внутри процесса приложения переднего плана.
ms.date: 09/19/2018
ms.topic: article
keywords: Windows 10, uwp, фоновая задача, службы приложений
ms.assetid: 5327e966-b78d-4859-9b97-5a61c362573e
ms.localizationpriority: medium
ms.openlocfilehash: 97dd249165877591743892a136d51e0969dd902a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57601209"
---
# <a name="port-an-out-of-process-background-task-to-an-in-process-background-task"></a>Перенос фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса

Перенос вне процесса (ООП) фоновые действия для действия в процессе проще всего открыть вашей [IBackgroundTask.Run](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx?f=255&MSPPError=-2147217396) метод кода внутри приложения и запустить его из [OnBackgroundActivated ](/uwp/api/windows.ui.xaml.application.onbackgroundactivated). Технология, описываемого здесь не является создание оболочки по ООП фоновой задачи в фоновом режиме в процессе задачу; его о перезаписи (или перенос) ООП версии к версии в процессе.

Если в вашем приложении используются несколько фоновых задач, [пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BackgroundActivation) показывает, как можно использовать `BackgroundActivatedEventArgs.TaskInstance.Task.Name` для определения, какая задача инициируется.

Если в настоящее время производится обмен данными между фоновым процессом и процессом переднего плана, можно удалить код управления состоянием и код связи.

## <a name="background-tasks-and-trigger-types-that-cannot-be-converted"></a>Фоновые задачи и типы триггеров, преобразование которых невозможно

* Фоновые задачи внутри процесса не поддерживают активацию фоновой задачи VoIP.
* В процессе фоновые задачи не поддерживают следующие триггеры:  [DeviceUseTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceusetrigger.aspx?f=255&MSPPError=-2147217396), [DeviceServicingTrigger](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.deviceservicingtrigger.aspx) и **IoTStartupTask**
