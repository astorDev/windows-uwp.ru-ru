---
Description: Troubleshoot Microsoft Take a Test events and errors with the event viewer.
title: Устранение неполадок тестирования Microsoft с помощью просмотра событий.
author: PatrickFarley
ms.author: pafarley
ms.assetid: 9218e542-f520-4616-98fc-b113d5a08e0f
ms.date: 10/06/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, образовательных заведений
ms.localizationpriority: medium
ms.openlocfilehash: 3193525316d085e56244d6f03da99e3e07c6539f
ms.sourcegitcommit: f2f4820dd2026f1b47a2b1bf2bc89d7220a79c1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "2792422"
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
