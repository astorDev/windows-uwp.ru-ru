---
title: Перенос фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса
description: Порт ожидания процесса фоновой задачи в процессе фоновой задачи, которая выполняется внутри процесса приложения переднего плана.
ms.date: 09/19/2018
ms.topic: article
keywords: Windows 10, uwp, фоновая задача, службы приложений
ms.assetid: 5327e966-b78d-4859-9b97-5a61c362573e
ms.localizationpriority: medium
ms.openlocfilehash: 42aaa5600b30924acc84aa61c2a15dbb2a320c10
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66366263"
---
# <a name="port-an-out-of-process-background-task-to-an-in-process-background-task"></a>Перенос фоновой задачи, выполняемой вне процесса, в фоновую задачу внутри процесса

Перенос вне процесса (ООП) фоновые действия для действия в процессе проще всего открыть вашей [IBackgroundTask.Run](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run?f=255&MSPPError=-2147217396) метод кода внутри приложения и запустить его из [OnBackgroundActivated ](/uwp/api/windows.ui.xaml.application.onbackgroundactivated). Технология, описываемого здесь не является создание оболочки по ООП фоновой задачи в фоновом режиме в процессе задачу; его о перезаписи (или перенос) ООП версии к версии в процессе.

Если в вашем приложении используются несколько фоновых задач, [пример фоновой активации](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/BackgroundActivation) показывает, как можно использовать `BackgroundActivatedEventArgs.TaskInstance.Task.Name` для определения, какая задача инициируется.

Если в настоящее время производится обмен данными между фоновым процессом и процессом переднего плана, можно удалить код управления состоянием и код связи.

## <a name="background-tasks-and-trigger-types-that-cannot-be-converted"></a>Фоновые задачи и типы триггеров, преобразование которых невозможно

* Фоновые задачи внутри процесса не поддерживают активацию фоновой задачи VoIP.
* В процессе фоновые задачи не поддерживают следующие триггеры:  [DeviceUseTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.deviceusetrigger?f=255&MSPPError=-2147217396), [DeviceServicingTrigger](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.deviceservicingtrigger) и **IoTStartupTask**
