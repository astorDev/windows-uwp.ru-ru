---
Description: Troubleshoot Microsoft Take a Test events and errors with the event viewer.
title: Устранение неполадок тестирования Microsoft с помощью просмотра событий.
ms.assetid: 9218e542-f520-4616-98fc-b113d5a08e0f
ms.date: 10/06/2017
ms.topic: article
keywords: Windows 10, uwp, для образовательных учреждений
ms.localizationpriority: medium
ms.openlocfilehash: 2f4bdcf45c7dd37dd540a666d99b5fa2fd2d49f8
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "7979680"
---
# <a name="troubleshoot-microsoft-take-a-test-with-the-event-viewer"></a>Устранение неполадок тестирования Microsoft с помощью просмотра событий

Можно использовать средство просмотра событий для просмотра событий и ошибок тестирования. Тестирование добавляет в журнал события, когда поступает запрос блокировки, успешно проходит регистрация устройства, успешно применяются политики блокировки и в других случаях.

Включение просмотра событий в средстве просмотра событий.
1. Откройте `Event Viewer`
2. Перейдите к `Applications and Services Logs > Microsoft > Windows > Management-SecureAssessment`
3. Щелкните правой кнопкой мыши `Operational` и выберите `Enable Log`

Сохранение журналов событий.
1. Щелкните правой кнопкой мыши `Operational`
2. Нажмите `Save All Events As…`
