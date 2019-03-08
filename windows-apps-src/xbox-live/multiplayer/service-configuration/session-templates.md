---
title: Шаблоны сеанса многопользовательской игры
description: Сведения о шаблонах многопользовательской Xbox Live.
ms.assetid: 178c9863-0fce-4e6a-9147-a928110b53a2
ms.date: 04/04/2017
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox one, многопользовательские, шаблон сеанса
ms.localizationpriority: medium
ms.openlocfilehash: 0bbe4f6a3afe2d39fb18b4d4bad13e2aa91d246e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57599439"
---
# <a name="multiplayer-session-templates"></a>Шаблоны сеанса многопользовательской игры

В этом разделе дается краткий обзор многопользовательской шаблонов и приводится несколько примеров шаблонов, которые можно скопировать и изменить для многопользовательской сеансов.

Шаблон многопользовательской — план, который используется для создания многопользовательской. Все сеансы должны создаваться на основе предопределенных шаблона. Шаблон определяет константы, которые будут совпадать для любого сеанса, который создается из шаблона. Когда игра создает сеанс на основе шаблона, игры можно добавить и изменить дополнительные данные в сеанс, но не могут изменять константы, которые были определены в шаблоне.

 Дополнительные сведения см. в разделе [Общие сведения о сеансе](../multiplayer-appendix/mpsd-session-details.md).

Список шаблонов сеансов, которые применяются к идентификатора конфигурации службы (SCID), а также содержимое конкретного сеанса шаблоны, можно получить из каталога сеанса многопользовательскую (MPSD).


## <a name="about-session-templates"></a>Сведения о шаблонах сеанса

Шаблон сеанс использует тот же формат, как запрос HTTP PUT для создания или изменения сеанса. Разница в шаблоне ограничена константы (нет элементов, серверы или свойства). Константы могут быть любые константы сеанса, а также пользовательский раздел и широкого спектра константы системы.

### <a name="session-template-versions"></a>Шаблонные версии сеанса

Шаблоны сеанса, определенные в этом разделе создаются с помощью версии контракта шаблона 107. При использовании их для создания нового шаблона, убедитесь, что вводится в виде версии контракта 107.

Если вы используете XSAPI и просмотрите полученный запросов в отладчике, можно заметить, что запросы используют версию контракта шаблона 105. MPSD эффективно «обновляет» эти запросы до версии 107 во время выполнения.

> **Примечание.** Является допустимым для использования версии отдельное соглашение в запросе от используемой в шаблоне сеанса.

При необходимости можно изменить шаблон сеанса из версии 104/105 до версии 107. Инструкции см. Миграция инструкции в [распространенных проблем при адаптации Your названий многопользовательскую 2015](../multiplayer-appendix/common-issues-when-adapting-multiplayer.md).


## <a name="session-template-default-values"></a>Значения по умолчанию шаблона сеанса

Каждый сеанс, созданные на основе шаблона сеанс запускается как копию шаблона. Значения, которые не включают шаблон можно предоставить во время создания сеанса. Значения по умолчанию приведены в некоторых случаях, если другое значение не задано. Например заданное по умолчанию время ожидания которых истекло версия контракта 107 является:

```json
    {
      "constants": {
        "system": {
          "reservedRemovalTimeout": 30000,
          "inactiveRemovalTimeout": 0,
          "readyRemovalTimeout": 180000,
          "sessionEmptyTimeout": 0
        }
      }
    }
```
Вы можете принудительно значение оставаться отменить задание, указав значение null. Это переопределяет все параметры по умолчанию и не дает значение задаваемое во время создания сеанса. Например для удаления пустой время ожидания сеанса, позволяя сеансы для продолжения бесконечно, даже без членов, добавьте следующий шаблон сеанса:
```json
    {
      "constants": {
        "system": {
          "sessionEmptyTimeout": null
        }
      }
    }
```
> **Важно!** Константы, которые задаются с помощью шаблона нельзя изменить при помощи операции записи для MPSD. Чтобы изменить значения, необходимо создать и отправить новый шаблон с необходимые изменения.


## <a name="example-session-templates"></a>Примеры шаблонов сеанса
В этом разделе показано несколько примеров шаблонов сеансов для разных целей и топологии сети. Проверьте каждый шаблон, прежде чем выбрать один, наилучшим образом подходящий для вашего клиента. Можно скопировать и вставить шаблон в конфигурацию службы, потенциально, после внесения необходимых изменений.

### <a name="standard-lobby-session"></a>Стандартный основной сеанса
Следующий шаблон можно использовать как начальный шаблон для создания сеанса основной вашей игры:

* Изменение `maxMembersCount` значение максимальное число проигрыватели, которые требуется поддерживать в текущем сеансе основной.  
* Если название не поддерживает игроков, используя разные платформы (например Xbox One и ПК) воспроизведение друг с другом, можно удалить `crossPlay` элемент.  
* Другие значения можно изменить, но следующие значения являются оптимальных значений для начала, если вы не знаете, что нужно.


```json
{
   "constants": {
        "system": {
            "version": 1,
            "maxMembersCount": 8,
            "visibility": "open",
            "capabilities": {
                "connectivity": true,
                "connectionRequiredForActiveMembers": true,
                "crossPlay": true,
                "userAuthorizationStyle": true
            },
        },
        "custom": {}
    }
}
```

### <a name="standard-game-session-without-matchmaking"></a>Стандартный сеанс игры без подбор игроков
Следующий шаблон можно использовать как начальный шаблон для создания игр сеанса для игры, в том случае, если игры не поддерживает анонимные подбор игроков и не требует более чем 100 членов.

Обратите внимание, что только новые значения, указанные в шаблоне сеанса стандартный основной следующее:
* `constants.system.inviteProtocol : "game"`
* `constants.system.capabilities.gameplay : true`

```json
{
   "constants": {
        "system": {
            "version": 1,
            "maxMembersCount": 8,
            "visibility": "open",
            "inviteProtocol": "game",
            "capabilities": {
                "connectivity": true,
                "connectionRequiredForActiveMembers": true,
                "gameplay" : true,
                "crossPlay": true,
                "userAuthorizationStyle": true
            }
        },
        "custom": {}
    }
}
```

### <a name="add-matchmaking-to-a-game-session-template-while-letting-the-multiplayer-service-handle-quality-of-service-checks"></a>Добавление партнеров в шаблон игр сеанса, при этом разрешив многопользовательские службы обработки качества проверки службы.

Можно добавить следующие `memberInitialization` элемент json в шаблоне игровой процесс для добавления поддержки для партнеров.

При создании вашей SmartMatch hopper, этот шаблон используется как целевой шаблон сеанса для вашей hopper.

```json
{
   "constants": {
        "system": {
            "memberInitialization": {
               "joinTimeout": 20000,
               "measurementTimeout": 15000,
               "membersNeededToStart": 2
            }
        }
    }
}
```

### <a name="add-matchmaking-to-a-game-session-template-where-quality-of-service-checks-are-handled-by-a-title-managed-data-center"></a>Добавление партнеров в шаблон игр сеанса, где качество служба проверяет обрабатываются title управляемого центра обработки данных.



```json
{
   "constants": {
        "system": {
            "peerToHostRequirements": {  
                "latencyMaximum": 250,
                "bandwidthDownMinimum": 256,
                "bandwidthUpMinimum": 256,
                "hostSelectionMetric": "latency"
            },
            "memberInitialization": {
               "joinTimeout": 15000,
               "measurementTimeout": 15000,
               "membersNeededToStart": 2
            }
        },
        "custom": {}
    }
}
```

### <a name="basic-session-template-for-client-server-game-session"></a>Базовые функции сеанса и шаблон для игр сеанса клиент сервер

Этот шаблон можно использовать для заголовок, который не выполняет peer-to-peer связи и не использует вычислений Xbox Live, но имеет клиентам подключаться к размещенному серверу независимых производителей.
```json
    {
      "constants": {
        "system": {
          "version": 1,
          "maxMembersCount": 12,
          "visibility": "open",
          "inviteProtocol": "game",
          "capabilities": {
            "connectionRequiredForActiveMembers": true,
            "gameplay" : true,
          },
        },
        "custom": {}
      }
    }
```

### <a name="lobbysmartmatch-ticket-session-template-for-peer-based-networking"></a>Шаблон основной/SmartMatch билет сеанса, организации сетей на основе однорангового узла

Этот шаблон служит для создания сеанса основной или SmartMatch билет сеанса, только чтобы использоваться для отправки нескольких игроков в подбор игроков. Шаблон — не должна использоваться для настройки сеанса игры. Он предназначен для использования клиентами с помощью топологии сети peer-to-peer или ведущего узла одноранговой сети.
```json
    {
      "constants": {
        "system": {
          "version": 1,
          "maxMembersCount": 10,
          "visibility": "open",
          "capabilities": {
            "connectionRequiredForActiveMembers": true,
          },
          "memberInitialization": {
            "membersNeededToStart": 1
          },
        },
        "custom": {}
      }
    }
```

### <a name="quality-of-service-qos-templates"></a>Качество обслуживания (QoS) шаблонов

Если ваш клиент использует подбор игроков и оценке качества обслуживания, необходимо добавить некоторые константы шаблона сеанса для информирования MPSD для координации с клиентом для управления пользователями, подключиться к сеансу. Эту координацию проверяет качество состояния подключения перед уведомлением пользователей, которые игры готов к запуску. В случае клиент сервер игры координация проверяет качество соединения перед группы игроков вводит подбор игроков.

#### <a name="peer-to-host-game-session-template-with-qos"></a>Шаблон игр сеанса ведущего узла одноранговой сети с политиками качества обслуживания

В следующем примере представлена шаблона сеанса игр ведущего узла одноранговой сети с политиками качества обслуживания.
```json
    {
      "constants": {
        "system": {
          "version": 1,
          "maxMembersCount": 12,
          "visibility": "open",
          "inviteProtocol": "game",
          "capabilities": {
            "connectivity": true,
            "connectionRequiredForActiveMembers": true,
            "gameplay" : true
          },
          "memberInitialization": {
            "membersNeededToStart": 2
          },
          "peerToHostRequirements": {
            "latencyMaximum": 350,
            "bandwidthDownMinimum": 1000,
            "bandwidthUpMinimum": 1000,
            "hostSelectionMetric": "latency"
          }
        },
        "custom": { }
      }
    }
```

#### <a name="peer-to-peer-game-session-template-with-qos"></a>Шаблон Peer-to-peer игр сеанса с контролем качества обслуживания

Ниже приведен пример шаблона peer-to-peer игр сеанса с QoS.
```json
    {
    "constants": {
      "system": {
        "version": 1,
        "maxMembersCount": 12,
        "visibility": "open",
        "inviteProtocol": "game",
        "capabilities": {
          "connectivity": true,
          "connectionRequiredForActiveMembers": true,
          "gameplay" : true
        },
        "memberInitialization": {
          "membersNeededToStart": 2
        },
        "peerToPeerRequirements": {
          "latencyMaximum": 250,
          "bandwidthMinimum": 10000
        }
      },
      "custom": { }
     }
    }
```

#### <a name="client-server-xbox-live-compute-lobbymatchmaking-session-template-with-qos"></a>Шаблон сеанса клиент сервер (Xbox Live вычислений) основной/подбор игроков с контролем качества обслуживания

Этот шаблон служит для создания сеанса основной или сеанс подбор игроков, с помощью QoS. Этот шаблон не предназначен для использования для настройки сеанса игры.
```json
    {
      "constants": {
        "system": {
          "version": 1,
          "maxMembersCount": 12,
          "visibility": "open",
          "memberInitialization": {
            "membersNeededToStart": 1
          }
        },
        "custom": {}
      }
    }
```

#### <a name="session-template-for-crossplay-between-xbox-one-and-windows-10"></a>Шаблон сеанса для crossplay между Xbox One и Windows 10

Этот шаблон служит для включения crossplay многопользовательские между Xbox One и Windows 10. Возможность userAuthorizationStyle обеспечивает доступ к Windows 10. Возможность crossPlay необязательно означает, что название поддерживает взаимодействия, например приглашения и объединения выполняется между платформами.
```json
    {
      "constants": {
        "system": {
          "capabilities": {
            "crossPlay": true,
            "userAuthorizationStyle": true
          },
        },
        "custom": {}
      }
    }
```
