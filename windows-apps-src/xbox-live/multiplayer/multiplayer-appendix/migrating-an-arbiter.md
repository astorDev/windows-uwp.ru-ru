---
title: Миграция арбитр
description: Сведения о переносе арбитр в 2015-многопользовательскую Xbox Live
ms.assetid: 9fb5d2c0-d548-4a22-b64e-6b215f78d22e
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, арбитр, многопользовательские 2015
ms.localizationpriority: medium
ms.openlocfilehash: 7e250841fb0731a903c0e9c5bdeeecd42f07ac84
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57629709"
---
# <a name="migrating-an-arbiter"></a>Миграция арбитр

В определенный момент во время завершения сеанса может потребоваться выбрать нового класса arbiter, используя миграцию arbiter. Существует два типа миграции:

-   **Нормальные арбитр миграции**
-   **Миграцию arbiter отработки отказа**

Ниже блок-схеме показано, как перенести арбитра.

![](../../images/multiplayer/Multiplayer_2015_HostMigration.png)

## <a name="graceful-arbiter-migration"></a>Нормальные арбитр миграции

Нормальные арбитр миграции, в исходящие арбитр может облегчения задачи миграции и определить новые арбитр. Этот тип миграции использует параметр арбитр, как описано в разделе [как: Задайте арбитра для сеанса MPSD](multiplayer-how-tos.md).


## <a name="failover-arbiter-migration"></a>Миграцию Arbiter отработки отказа

В миграцию arbiter отработки отказа подключение к предыдущей арбитр теряется, и остальные одноранговые узлы необходимо определить новый арбитр для сеанса. Миграцию arbiter отработки отказа также задает маркер устройства для узла и обрабатывает коды состояний HTTP/412, что делает так же, как нормальные миграцию arbiter. Тем не менее существует несколько подходов для выбора новой арбитр во время миграции арбитр отработки отказа.
## <a name="select-arbiter-using-the-host-candidate-list"></a>Выбор класса Arbiter, с помощью узла список кандидатов

Вы можете настроить MPSD, чтобы предоставить упорядоченный узла список кандидатов основании координирующему показатели качества обслуживания, которые оцениваются во время определенных операций. Клиент может использовать этот список, чтобы определить новый арбитр. Чтобы воспользоваться преимуществами этого списка во время миграции класса arbiter, каждый узел можно:

1.  Определите список положение предыдущего класса arbiter.
2.  Оцените консоль далее в списке.
3.  Если консоль локальной консоли, используйте его как новое арбитр.
4.  Если консоль больше нет в многопользовательской или отключен от соседних, переходим к следующем кандидата в списке и поработать с ним как в предыдущих шагах.
5.  Если к концу списка с нет нового класса arbiter выбран, использует каскадный подход к выбору класса arbiter, что может нарушить связи. См. в разделе «Выделение "жадные" арбитр.»

| Примечание.                                                                                                                                                                                                                                                                                    |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Не рекомендуется для создания списка кандидатов узла в игре после координирующему через явные пробы QoS в заголовок. Если этот механизм является абсолютно необходимым, у клиента вместо сведения о пользователе, например, идентификатор пользователя Xbox, маркер устройства узла для определения класса arbiter кандидатов. |


### <a name="select-arbiter-using-peer-voting"></a>Выбор класса Arbiter, с помощью однорангового узла голосования

Если полное подключение существует среди всех членов, они могут использовать сообщения однорангового голосовать и выберите новый арбитр. Новый арбитр затем обновляет маркер устройства узла для сеанса с помощью синхронизированного обновления. См. в разделе [как: Обновление многопользовательской](multiplayer-how-tos.md).


### <a name="use-greedy-arbiter-selection"></a>Используйте вариант "жадные" Arbiter

Иногда не узла список кандидатов недоступна или подключение QoS не требуется, например, для чисто арбитр обязанности. В этих случаях клиент можно использовать подход "жадные" арбитр выбора. В этом случае однорангового узла следует задать нового класса arbiter, как только он обнаружит, что исходного класса arbiter покинул игр сеанса, предоставленное **MultiplayerSessionChanged** событий. Всем другим узлам см. код состояния HTTP/412 при попытке установить узлов маркер устройства, при условии, что другие изменения в сеанс не вносятся на этом этапе. Только одному узлу удалось выбрать нового класса arbiter.


## <a name="see-also"></a>См. также

[Подробные сведения о сеансе MPSD](mpsd-session-details.md)