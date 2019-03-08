---
title: Каталог сеансов многопользовательской игры Xbox One
description: Дополнительные сведения о создании многопользовательские сеансов с помощью службы Xbox Live Mutliplayer сеанса каталога (MPSD).
ms.assetid: 70da1be3-5f39-4eed-b62d-9cdd47e413d2
ms.date: 04/04/2017
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 52b363492d1cd17ae54ae5d23d04371c5adf73ed
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57606819"
---
# <a name="xbox-one-multiplayer-session-directory"></a>Каталог сеансов многопользовательской игры Xbox One

Здесь представлен обзор создания многопользовательской, с помощью новой службы Xbox один многопользовательские сеанса каталога (MPSD). Бумаги предназначенные для работы в основном на Xbox One разработчики title, отправить их сеанса шаблоны непосредственно к Xbox разработки портал (XDP). Служба MPSD можно также можно настроить с помощью центра партнеров, но не рассматривается в этой статье. Он предназначен для получения представления о них с помощью термины и основные понятия, связанные с конфигурацией MPSD, использование и устранение неполадок многопользовательской сеансов.

## <a name="revision-summary"></a>Сводка версия

Клиентские библиотеки XSAPI (API службы Xbox) в настоящее время использовать версию контракта 104 при связи с MPSD служб. Эта версия документа обновляет схему шаблона сеанса для контракта версии 107, который является последней версии контракта, которые поддерживаются службами MPSD. В разделе приведены изменения между версиями 104 и 107 [обновление схемы контракта](#_Contract_schema_update).

Обратите внимание, что шаблоны, которые были написаны для версии контракта 104 будет необходимо обновить, если они публикуются как 107. Тем не менее, службы обладают обратной совместимостью, поэтому можно продолжать использовать текущий библиотеки, которые будут обновлены в будущем выпуске XDK.

Предыдущие версии этого документа обновленные сведения о сеансах сервера и добавлен новый раздел о Xbox Live API-интерфейсы службы и вызовы службы RESTful. Кроме того была обновлена в таблице в запрос для раздела сведений о состоянии сеанса и внесены изменения в разделе Шаблоны качества обслуживания (QoS).

## <a name="introduction"></a>Введение

В службе Xbox One многопользовательской — это безопасный документ, который находится в облаке на многопользовательской каталог сеанса (MPSD), и этот документ представляет собой группу людей, игры. В частности многопользовательские сеансы имеют следующие характеристики:

-   Каждый сеанс имеет уникальный URI.

-   Документ сеанса содержит текущие элементы настройки игры, начальная загрузка данных и сведения о сервере игр.

-   Заголовки, создание и управление их собственных сеансов.

-   Сеанс обеспечивает подключение между его члены.

Каталог сеансов многопользовательскую централизует метаданных сеанса игр для всех клиентов. Он предоставляет базовые сведения, необходимые о сеансе, помогут настроить устройство обеспечения безопасности связи между клиентами. Он также предоставляет базовые метаданные первой загрузки для подключения к игре, перед началом передачи более конкретные игровых данных клиента. С управления временем жизни процесса (PLM) и характер переключения задач приложений в службе Xbox One MPSD гарантирует, что клиенты получат правильные сведения для обращения в одноранговые узлы и серверы, которые идентифицированы как часть активного сеанса игр и координаты оболочка и консоли операционной системой для резервирования активации и управления проигрывателя время существования сеанса игры.

## <a name="terminology-used-in-this-document"></a>Терминология, используемая в этом документе

| Термин                 | Определение                                                                                                                                                                                                                                                                                  |
|----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Многопользовательской  | Безопасный документ, который размещается в облаке Xbox Live и представляет группу пользователей (или будет) соединены друг с другом при воспроизведении заголовок на Xbox One. Все аспекты многопользовательскую — например подбор игроков, сторонами, соединения в ход выполнения и т. д. — использовать многопользовательские сеанса. |
| Игры сеанса         | Это фактический игр сеанс, представлены в MPSD, в котором пользователи воспроизводится друг с другом. Все сценарии многопользовательские конечном счете оказываются в сеансе игр.                                                                                                                               |
| Совпадение билет сеанса | Это сеанс, используемый для отслеживания соответствия доступной для отправки запросов во время подбор игроков.                                                                                                                                                                                                                 |
| Неактивные проигрывателя      | Игрок, который задан в неактивном состоянии в рамках сеанса. Пользователь задает заголовок неактивный состояние, при ограниченном игры, приостановлен, или в противном случае неактивные, как определено для наименования.                                                                                     |

## <a name="the-multiplayer-session-directory"></a>В многопользовательской каталог

MPSD облегчает и помогает координировать информации сеанса между online игроков заголовки. Может существовать различные типы сеансов, создаваемых для выполнения различных задач, многопользовательские игры. Следующая таблица располагает различия в том, как такие задачи выполнены на Xbox 360 и как они выполняются на Xbox One.

| Функции или задачи                     | Xbox 360                                                                                                        | Xbox One                                                                                                   |
|--------------------------------------|-----------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|
| **Получение сведений о сеансе игр**     | **XSessionGetDetails**, **XSessionSearchByID**, или самостоятельно отслеживать.                                              | Запросите сведения о сеансе в службе.                                                          |
| **Миграция узла**                     | При необходимости вызывает заголовок **XSessionMigrateHost.**                                                           | Нет необходимости создавать новый сеанс, просто назначьте новый узел для SessionID.                                  |
| **Управление несколькими сеансами проигрывателя**  | Непростой задачей для обработки более чем один сеанс одновременно. Например **XNetReplaceKey** и **XNetUnregisterKey**. | Сеансов на основе службы упрощает управление одного сеанса очистки и позволяет легко обрабатывать несколько сеансов.    |
| **Обработка входа для распределения по уровням и отключает** | Обрабатывать отключается и выход по-разному, с помощью **XCloseHandle** или **XSessionDelete**, соответственно. | Централизованная служба упрощает входа для распределения по уровням и отключения обработки, и время ожидания задаются в файле конфигурации игры. |

### <a name="session-patterns"></a>Шаблоны сеанса

-   Игровые сеансы

    -   Сеанс с XUIDs игроков, данные адреса устройство обеспечения безопасности и состояния. Это будет рассматривать как сеанс сценария игры.

    -   Может быть peer-to-peer, ведущего узла одноранговой сети, одноранговых сервер или гибридной.

-   Совпадение билет сеансов

    -   Сеанс, который передан в подбор игроков, чтобы найти другие игроки можно поиграть. Обратите внимание, что сеанс игры можно также билет сеанса, если игра ищет больше игроков.

-   Сеансы сервера

    -   Игровые сеансы создаются и используются посредством обработки вычислений Xbox Live.

Рис. 1 иллюстрирует использование MPSD сеансов, где синие прямоугольники представляют MPSD сеансы и красные поля — службы, которые используют их.

Рисунок 1. Используйте MPSD сеанса.

## <a name="mpsd-sessions"></a>Сеансы MPSD

Сеансы поддерживают два списка связанных сущностей:

1.  Члены (пользователи), добавленных или были приглашены в сеанс.

2.  Серверы (облачных серверов или выделенные серверы), входящих в сеанс.

Каждая сущность имеет:

1.  Постоянное значение во время создания.

2.  Изменяемые свойства.

3.  Метаданные только для чтения.

### <a name="xbox-live-service-apis-and-restful-service-calls"></a>Xbox Live API-интерфейсы службы и службы RESTful вызовы

Существует два способа взаимодействия со службами подбор игроков и один сеансы Xbox. Первый способ — использовать стандартные вызовы HTTPS к RESTful URI службы Xbox Live. Это позволяет гибко заголовки вызова и взаимодействии с этими службами, в зависимости от их сервера и игр конфигурации. Список этих URI можно найти в [документации Xbox одного пакета средств разработки (XDK)](https://developer.xboxlive.com/en-us/platform/development/documentation/software/Pages/home.aspx) в разделе «Xbox Live Справочник по службам RESTful.» [1]

Второй способ — использовать Xbox Live службы интерфейсы API, который выступать в качестве оболочки для службы RESTful идентификаторы URI. Позволяют более традиционного подхода с помощью API-интерфейсов в коде без необходимости обрабатывать трафик HTTPS для каждого вызова. Исходный код для API-интерфейсы Xbox Live службы поставляется с помощью пакета средств разработки Xbox (XDK) и его можно изменить и включены в заголовок вашей, при необходимости. Примеры написаны с помощью API службы Xbox Live. Дополнительные сведения об API-интерфейсы Xbox Live Services можно найти в Xbox One [XDK документации](https://developer.xboxlive.com/en-us/platform/development/documentation/software/Pages/home.aspx) в разделе «Xbox Live Services ссылка». [2]

### <a name="mpsd-sessions-and-templates"></a>Сеансы MPSD и шаблоны

MPSD сеансы создаются с помощью шаблонов, полученные через портал разработки Xbox (XDP). Шаблоны, документы JSON, которые определяют платформу для создания сеанса. Шаблон предоставляет константы для нового сеанса.

В приведенном ниже фрагменте [пример кода рандеву проигрывателя](https://developer.xboxlive.com/en-us/platform/development/education/Pages/Samples.aspx) приведен пример конфигурации шаблона.

```json
// Used for creating the session that you then pass into your match ticket request. This *should* not have any QoS requirements.
MatchTicketSession (Contract Version: 107)
{
    "constants": {
        "system": {
            "version": 1,
            "maxMembersCount": 10,
            "visibility": "private",
            "capabilities": {},
            "memberInitialization": {
                "joinTimeout": 20000,
                "externalEvaluation": false,
                "membersNeededToStart": 1
            }
        },
        "custom": {}
    }
}

// This is the new game session that is returned after you’ve been matched.
// Note: This is used for in-game QoS. For out-of-game QoS, you will need P2P/HTP requirements.
GameSession (Contract Version:107)
{
    "constants": {
        "system": {
            "maxMembersCount": 12,
            "capabilities": {"connectivity": true }
        },
        "memberInitialization": {
         "joinTimeout": 20000,
         "measurementTimeout": 15000,
         "externalEvaluation": false,
         "membersNeededToStart": 4
         },

   "custom": {}
  }
}
```

Следует использовать билет сеанса совпадения с шаблоном игры сеанса настройки со значениями времени ожидания службы QoS в его **memberInitialization** объекта.

Рисунок 2. Hopper образец.

![](../../images/whitepapers/mpsd_image1.png)

В фрагменте кода ниже приведен пример шаблона сеанса игр peer-to-peer (управляемая title QoS).

```
{
    "constants": {
        "system": {
            "version": 1,
            "maxMembersCount": 10,
            "visibility": "private",
            "capabilities": {
                "connectivity": true
            },
            "memberInitialization": {
                "joinTimeout": 20000,
                "externalEvaluation": false,
                "membersNeededToStart": 2
            }
        },
        "custom": {}
    }
}

```

Ниже приведен пример сеанса однорангового узла для сервера и НЕОБРАБОТАННОГО шаблона.

```
{
    "constants": {
        "system": {
            "version": 1,
            "maxMembersCount": 10,
            "visibility": "private",
            "capabilities": {}
        },
        "custom": {}
    }
}
```

Следующий код является примером шаблона соответствия билет сеанса, который используется для смарт-совпадать.

```
{
    "constants": {
        "system": {
            "version": 1,
            "maxMembersCount": 10,
            "visibility": "private",
            "capabilities": {},
            "memberInitialization": {
                "joinTimeout": 20000,
                "externalEvaluation": false,
                "membersNeededToStart": 1
            }
        },
        "custom": {}
    }
}

```

### <a name="checking-which-templates-are-configured-for-your-scid"></a>Проверка того, какие шаблоны, настроенные для вашего SCID

#### <a name="session-templates"></a>Шаблоны сеансов

Список шаблонов сеансов, которые существуют в SCID, а также сведения из шаблона конкретного сеанса, можно получить из MPSD:

```
GET /serviceconfigs/{scid}/sessiontemplates

GET /serviceconfigs/{scid}/sessiontemplates/{session-template-name}
```

#### <a name="query-for-session-state-information"></a>Запрос информации о состоянии сеанса

Сеансы, можно запросить на уровнях шаблона конфигурации и сеанса обслуживания:

```
GET /serviceconfigs/{scid}/sessions

GET /serviceconfigs/{scid}/sessiontemplates/{session-template-name}/sessions
```

По умолчанию возвращается до 100 сеансов отличным от private. Запрос можно изменить с помощью этих параметров строки запроса:

| Строка запроса             | Результат                                                                                                         | Описание                                                                                         |
|--------------------------|----------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Ключевое слово = foo              | Включать только сеансы с ключевого слова «foo».                                                             |                                                                                                     |
| XUID = 123                 | Включать только сеансы, в которых пользователь «123».                                                               | По умолчанию пользователь должен быть активной в сеансе для включения.                                  |
| *Резервирование*=**true** | Включить сеансы, для которых пользователь задается как зарезервированные проигрывателя, но не присоединен к становятся проигрыватель с active. | Только в том случае, при запросе собственный сеанс, или при запросе определенного пользователя сеансов от сервера к серверу. |
| *Неактивные*=**true**      | Включить сеансы, которые пользователь принял, но не воспроизводится активно.                                     | Сеансы, в которых пользователь является «Готово», но не «активен» учитываются как неактивные.                           |
| *закрытый*=**true**       | Включает закрытый сеансы.                                                                                      | Только в том случае, при запросе собственный сеанс, или при запросе сервера на сервер.                            |
| *видимость*= открыто        | Включать только сеансы, которые являются «открыть».                                                                         | Если задать значение «"частные",» «закрытый = true» директивы также должно быть установлено.                                 |
| *воспользоваться*= 5                 | Возвращать до пяти сеансов.                                                                                    | Должен быть в диапазоне от 0 до 100.                                                                          |

Результат — это массив JSON ссылок сеанса. Некоторые данные сеанса включена.

**Примечание** каждый запрос должен включать фильтр ключевых слов, фильтр XUID или оба.

При настройке любого *частного* (возвращает частных сеансов) или *резервирования* (который возвращает сеансы пользователя еще не присоединен) для **true** вызывающая программа должна иметь доступ на уровне сервера к сеансу, или для утверждения XUID вызывающей стороны на соответствие фильтру XUID в URI. В противном случае — «403 запрещено» возвращается (ли все такие сеансы фактически существуют).

В следующем фрагменте кода показан пример ответ на запрос.

```
{
"results": [ {
"xuid": "9876",  // If the session was found from a xuid, that xuid.
"startTime": "2009-06-15T13:45:30.0900000Z",
"sessionRef": {
  "scid": "foo",
  "templateName": "bar",
  "name": "session-seven"
},
"accepted": 4,        // Approximate number of non-reserved members.
"status": "active",   // or "reserved" or "inactive". This is the state of the user in the session, not the session itself. Only present if the session was found using a xuid.
"visibility": "open", // or "private", "visible", or "full"
"myTurn": true,       // Not present is the same as 'false'. Only present if the session was found using a xuid.
"keywords": [ "one", "two" ]
} ]
}

```

## <a name="session-template-attributes"></a>Атрибуты шаблона сеанса

<div id="_Contract_schema_update"/>

## <a name="contract-schema-update"></a>Обновление схемы контракта

Как уже было сказано в начале этого документа, последней версией контракта шаблона сеанса — 107, который вводит некоторые изменения в схему из предыдущей версии 104. Шаблоны, которые были написаны для версии контракта 104 необходимо обновить, если они публикуются как 107. Ниже приведен краткий перечень изменений.

-   **/Constants/System/managedInitialization** переименовывается в **/constants/system/memberInitialization**.

    -   **AutoEvaluate** поле переименовывается в **externalEvaluation** и полярности изменения, кроме тех, которые **false** остается по умолчанию.

    -   Значение по умолчанию **membersNeededToStart** изменяется с 2 на 1.

    -   Значение по умолчанию **joinTimeout** изменяется с 5 секунд на 10 секунд.

    -   **MeasurementTimeout** по умолчанию изменяется с 5 секунд до 30 секунд.

-   **/Constants/System/timeouts** удаляется, и временем ожидания по были переименованы и перемещены в разделе **/константы/system**.

    -   **Зарезервированные** время ожидания становится **reservedRemovalTimeout**.

    -   **Неактивного** время ожидания становится **inactiveRemovalTimeout** и его новое значение по умолчанию равно 0, а не 2 часа.

    -   **Готовы** время ожидания становится **readyRemovalTimeout**.

    -   **SessionEmpty** время ожидания становится **sessionEmptyTimeout**.

-   **/Constants/System/Capabilities/gameplay** должен быть указан как **true** о сеансах, которые представляют фактические игровой процесс (в отличие от модуля поддержки сеансов, например сеансов соответствия и основной) для включения последних игроков и reporting репутации.

### <a name="system-objects"></a>Системные объекты

Каждый из системных объектов в документе сеанса имеет фиксированную схему, принудительно интерпретируется MPSD.

В теле запросов PUT системные объекты проверяются и слияние так же, как настраиваемые объекты. Но в отличие от пользовательских объектов, после они объединяются система дальнейшей проверяются и обрабатываемое объекты на основе этих схем.

**/ константы/системы**

```json
{
"version": 1,  //Document Version (FAL release version 1, service contract 107)
"maxMembersCount": 100,  // Defaults to 100 if not set on creation. Must be between 1 and 100.
"visibility": "private",  // Or "visible" or "open", defaults to "open" if not set on creation.

"initiators": [ "1234" ],  // If specified on a new session, the creators xuid must be in the list (or the creator must be a server).
"inviteProtocol": "party",  // Optional URI scheme of the launch URI for invite toasts.

"reservedRemovalTimeout": 10000, // Default is 30 seconds. Member is removed from the session.
"inactiveRemovalTimeout": 0, // Default is zero. Member is removed from the session.
"readyRemovalTimeout": 60000, // Default is three minutes. Member is removed from the session.
"sessionEmptyTimeout": 0, // Default is zero. Session is deleted.

// Capabilities are boolean values that are optionally set in the session template. If no capabilities are needed, an empty "capabilities" object should be in the template in order to prevent capabilities from being specified on session creation, unless the title desires dynamic session capabilities.
"capabilities": {
"clientMatchmaking": true,
"connectivity": true, // Cannot be set if ‘large’ is specified.
     "suppressPresenceActivityCheck": false,
     "gameplay": false,
     "large": false
},
/* If a "memberInitialization" object is set, the session expects the client system or title to perform initialization following session creation and/or as new members join the session. The timeouts and initialization stages are automatically tracked by the session, including QoS measurements if any metrics are set. These timeouts override the session's reservation and ready timeouts for members that have 'initializationEpisode' set. */
"memberInitialization": {
"joinTimeout": 20000,  // Milliseconds. Unspecified counts as 10 seconds.
"externalEvaluation": false,
"membersNeededToStart": 2  // Unspecified counts as 1. Must be between 0 and maxMembersCount. Only applies to episode 1. If 00 and the session is created with no members to initialize, episode 1 is skipped..
},
```

**/ Свойства/системы**

```
{
// Optional array of case-insensitive strings. Cannot be set if the session's visibility is "private".
"keywords": [ "hello" ],

// Array of integer indices of members whose turn it is. Defaults to empty. Can't be set (and doesn't appear) on large sessions.
"turn": [ 0 ],

/* Restricts who can join "open" sessions. (Has no effect on reservations, which means it has no impact on "private" and "visible" sessions.) Defaults to "none". On large sessions, "none" is the only valid value.
If "local", only users whose token's DeviceId matches someone else already in the session and "active": true.
If "followed", only local users (as defined above) and users who are followed by an existing (not reserved) member of the session can join without a reservation. */
"joinRestriction": "none",

// Device token of the host. Must match the "deviceToken" of at least one member, otherwise this field is deleted.
// If "peerToHostRequirements" is set and "host" is set, the measurement stage assumes the given host is the correct host and only measures metrics to that host.
// Can't be set on large sessions.
"host": "99e4c701",

// Can only be set while "initializing/stage" is "evaluating". True indicates success, and false indicates failure. Once set, "initializing/stage" is immediately updated, and this field is removed.
"initializationSucceeded": true,

/* The ordered list of case-insensitive connection strings that the session could use to connect to a game server. Generally titles should use the first on the list, but sophisticated titles could use a custom mechanism (e.g. Thunderhead) for choosing one of the others (e.g. based on load). */
"serverConnectionStringCandidates": [ "datacenter b", "serverfarm a" ],

"matchmaking": {
    "targetSessionConstants": { },
    // Force a specific connection string to be used (useful in preserveSession=always cases).
    "serverConnectionString": "datacenter b",
},

// True if the match that was found didn't work out and needs to be resubmitted. Set to false to signal that the match did work, and the matchmaking service can release the session.
"matchmakingResubmit": true
}

```

### <a name="timeouts"></a>Время ожидания

Сеансы можно изменить, таймеров и других внешних событий. **Тайм-ауты** в MPSD содержит базовый механизм управления жизненным циклом сеанса и члены.

**NextTimer** поле сеанса дает время таймера. Клиенты могут использовать эти сведения для выбора более хорошим интервалы следующий опрос. Это значение также возвращается в **Expires** заголовок HTTP.

Время ожидания указанный в миллисекундах. Ноль разрешена, означает, что время ожидания должно быть немедленно. Если заданный тайм-аут не указан, он считается бесконечной. Так как время ожидания по имеют значения по умолчанию, шаблон сеанса следует явно указать значение «null» для бесконечное время ожидания.

#### <a name="sessionemptytimeout"></a>sessionEmptyTimeout

**/Constants/system/sessionEmptyTimeout** значение настраивает время в миллисекундах после сеанса становится пустой, он будет удален. Значение по умолчанию равно нулю, это означает, что сеанс будут немедленно удалены. Если значение не указано, пустые сеансы будут неограниченное время жизни.

#### <a name="member-timeouts"></a>Время ожидания члена

Три других время ожидания в пределах **/константы/system** контролировать количество времени, участник может оставаться в определенном состоянии:

-   **reservedRemovalTimeout**

    -   Резервирование удалено истечения времени ожидания. Значение по умолчанию — 30 секунд.

-   **inactiveRemovalTimeout**

    -   Неактивный элемент удаляется из сеанса через два часа по умолчанию.

-   **readyRemovalTimeout**

    -   Члены, являющиеся «Готово» вернуться к неактивное состояние через три минуты, по умолчанию.

<div id="_Member_initialization_in"/>

## <a name="member-initialization-in-session-documents"></a>Инициализация члена в документах сеанса

### <a name="member-initialization"></a>Инициализация члена


**MemberInitialization** этапы инициализации после создания сеанса и/или как члены новых элементов управления веб-трансляции. Время ожидания и инициализации этапы, автоматически отслеживаются контекстом сеанса, включая измерения качества обслуживания, если заданы все метрики.

Это время ожидания переопределить резервирования и готовы время ожидания сеанса для членов, имеющих **initializationEpisode** набор свойств.

Пример.

```
"memberInitialization": {
        "joinTimeout": 5000,
        "measurementTimeout": 5000,
        "evaluationTimeout": 5000,  // only specify for external evaluation
        "externalEvaluation": true,
       "membersNeededToStart": 2
    },
```


![](../../images/whitepapers/mpsd_image2.png)

**Рис. 3. Последовательность инициализации элемента.**

Каждый из трех этапов инициализации членов может истечь время ожидания:

1.  **joiningTimeout**

    -   Количество времени, в миллисекундах, что пользователям нужно подключиться к сеансу. Резервирования членов, которые не удается присоединиться к удаляются.

2.  **measuringTimeout**

    -   Количество времени, как у членов для передачи их измерения. Члены, которые не удалось отправить измерения будут отмечены *failureReason* из «timeout».

3.  **evaluationTimeout**

    -   Количество времени для элемента и отправить решение оценки. Если решение не получен, он подсчитывает как сбой.

**externalEvaluation**

-   MPSD выполняется автоматическое вычисление QoS на основе требований, предоставленный в шаблоне сеанса. Когда externalEvaluation имеет значение false, выполняется вычисление. **externalEvaluation** должен быть **true** при **evaluationTimeout** имеет значение. Если имеются два требования peer-to-peer или ведущего узла одноранговой сети, по-прежнему необходимо задать **externalEvaluation** для **false** чтобы сеанс автоматически завершить инициализацию.

**membersNeededToStart**

-   Это минимальное количество резервирований члена, которые должны присутствовать с «инициализировать»: «true» и передать QoS для автоматического вычисления для успешного выполнения.

### <a name="initialization-episodes"></a>Эпизоды инициализации


Когда **memberInitialization** объект имеет значение, MPSD развивается этапов инициализации, эпизод. Первый эпизод запускается в том случае, когда сеанс создается и будет включать всех этапов, определенных в шаблоне.

Всем новым участникам, пригласили или объединить во время этого эпизода уже будут помечены для следующего сеанса. Состояние этого эпизода или **memberInitialization** в целом может извлекаться из **инициализация** объект сеанса.

**Примечание** следует помнить, что этот объект удаляется после завершения установки.

Пример.

```
"initializing": {
    "stage": "measuring",
    "stageStartTime": "2009-06-15T13:45:30.0900000Z",
    "episode": 1
},

```

Выходит из рабочей области присоединяться к измерению к оценке. Если эпизод завершается ошибкой, то этап имеет значение *сбой* и сеанс не может быть инициализирован. В противном случае — после завершения инициализации эпизод **инициализация** удаляется объект.

Также можно отслеживать сбои инициализации для каждого члена. Они устанавливаются при переходе из рабочей области соединения или измерения, если этот элемент не проходит.

Пример.
```
"initializationFailure": "latency",
```
В порядке приоритета, для этого атрибута может быть присвоено *тайм-аут, задержки, bandwidthdown, bandwidthup, сеть, группа*, или *эпизод*. Значение сети означает, что конфигурация сети и/или условий (таких как конфликтующие преобразование сетевых адресов \[NAT\]) запрещено измеряемого метрик качества обслуживания. Единственное возможное значение в конце присоединение — *группы*. (По истечении времени ожидания присоединяться к резервирование удаляется.)

Если **memberInitialization** имеет значение и добавлен элемент с «инициализировать»: true, это имеет значение эпизод инициализации, который элемент будет участвовать в. Значение 1 используется для элементы, добавленные к новому сеансу во время ее создания, и он удаляется при завершении инициализации эпизод.
```
"initializationEpisode": 1,
```

## <a name="match-ticket-session"></a>Совпадение билет сеанса

При совпадение билет сеанса используется сеанс MPSD, используются некоторые специальные сеанса свойства и константы.

**/Members/ {index} / константы/системы**

```json
{

  {
  "xuid": "12345678",

  "initialize": "false", // Run initialization for this user (if “memberInitialization” is set). Defaults to false.

```

Когда служба Координирующему добавляет пользователей к сеансу, он предоставляет некоторый контекст вокруг как и почему они были сопоставлены в сеанс, в **matchmakingResult** поля.

```
"matchmakingResult": {
```

Это копия пользователя **serverMeasurements** из сеанса подбор игроков.

```json
"serverMeasurements": {
    "east.thunderhead.azure.com": {
        "latency": 233  // Milliseconds
      }
    }
  }
}

```

## <a name="quality-of-service-qos-templates"></a>Качество обслуживания (QoS) шаблонов

Для шаблонов игр сеанса значения можно добавить, информирующие MPSD для координации с сетевого уровня и платформе социальной сети в консоли. Эту координацию предназначена для проверки качества состояния подключения, до начала создания сеанса и пользователь уведомляется о том, что игра готов к присоединению к.

В следующем фрагменте кода приведен пример шаблона сеанса игр ведущего узла одноранговой сети с политиками качества обслуживания.

```json
{
    "constants": {
        "system": {
            "version": 1,
            "maxMembersCount": 20,
            "visibility": "private",
            "capabilities": {
                "connectivity": true
            },
            "memberInitialization": {
                "joinTimeout": 20000,
                "externalEvaluation": false,
                "membersNeededToStart": 1
            },
            "peerToHostRequirements": {
                "latencyMaximum": 350,
                "bandwidthDownMinimum": 1000,
                "bandwidthUpMinimum": 100,
                "hostSelectionMetric": "latency"
            }
        },
        "custom": {}
    }
}
```

Этот фрагмент кода приведен пример шаблона peer-to-peer игр сеанса с QoS.

```json
{
    "constants": {
        "system": {
            "version": 1,
            "maxMembersCount": 20,
            "visibility": "private",
            "capabilities": {
                "connectivity": true
            },
            "memberInitialization": {
                "joinTimeout": 20000,
                "externalEvaluation": false,
                "membersNeededToStart": 1
            },
            "peerToPeerRequirements": {
                "latencyMaximum": 250,
                "bandwidthMinimum": 10000
            }
        },
        "custom": {}
    }
}

```

### <a name="qos-session-template-attributes"></a>Атрибуты шаблона сеанса QoS

Если **memberInitialization** объект имеет значение, то сеанс ожидает, что системе клиента или заголовок, чтобы выполнить инициализацию после создания сеанса и/или как новые члены присоединиться к сеансу.

Время ожидания и инициализации этапы, автоматически отслеживаются контекстом сеанса, включая измерения качества обслуживания, если заданы все метрики.

Это время ожидания переопределить резервирования и готовы время ожидания сеанса для членов, имеющих **initializationEpisode** набор свойств.

```json
"memberInitialization": {
"joinTimeout": 5000,  // Milliseconds. Unspecified counts as 10 seconds.
"measurementTimeout": 5000,  // Milliseconds. Unspecified counts as 30 seconds.
"evaluationTimeout": 5000,  // Milliseconds. Can only be set if 'externalEvaluation' is true. Unspecified counts as 5 seconds.
"externalEvaluation": true,
"membersNeededToStart": 2  // Unspecified counts as 1. Must be between 0 and maxMembersCount. Only applies to episode 1. If 0 and the session is created with no members to initialize, episode 1 is skipped.
},

```

Сеанс игры с QoS требуются возможности подключения. Если метрики не указаны, они по умолчанию на то, что бы требовалось, чтобы удовлетворить требования к качеству обслуживания. Если они указаны, они должны быть достаточно для удовлетворения требования к качеству обслуживания.

```json
"metrics": {
    "latency": true,
    "bandwidthDown": true,
    "bandwidthUp": true,
    "custom": true

```

Следующих пороговых значений применяются на каждое парный подключение для всех элементов в сеансе.

```json
"peerToPeerRequirements": {
    "latencyMaximum": 250,  // Milliseconds
    "bandwidthMinimum": 10000  // Kilobits per second
},

```

Применение следующих пороговых значений каждого подключения с кандидатом на узле:

```json
"peerToHostRequirements": {
    "latencyMaximum": 250,  // Milliseconds
    "bandwidthDownMinimum": 100000,  // Kilobits per second
    "bandwidthUpMinimum": 1000,  // Kilobits per second
    "hostSelectionMetric": "bandwidthup"  // Or "bandwidthdown" or "latency". Not specified is the same as "latency".
},

```

Следующие потенциальные соединение с сервером, строки должны быть вычисления (Обратите внимание, что строки подключения должны быть строчными):

```json
"measurementServerAddresses": {
        "east.thunderhead.azure.com": {
            "secureDeviceAddress": "r5Y="  // Base-64 encoded secure-device-address
        },
        "west.thunderhead.azure.com": {
            "secureDeviceAddress": "rwY="
        }
    }
}

```

**члены / system/свойства / {индекс}**

Состояние элемента управления эти флаги и **activeTitle**, и они являются взаимоисключающими (является ошибкой, позволяющий настраивать **true**). Для каждого из них **false** совпадает со значением «нет». По умолчанию находится в состоянии «неактивные», то есть таких сведений нет.

```json
"ready": true,
"active": false,

// Base-64 blob, or not present. Empty-string is the same as not present.
// 'capabilities/connectivity' must be enabled in order for this to be set.
"secureDeviceAddress": "ryY=",

// List of members in my group, by index. Each element must be an integer 0 <= n < 'membersInfo/next'.  
// During member initialization, if any members in the list fail, this member will also fail.
"group": [ 5 ],

// QoS measurements by lower-case device token.
// Like all fields, "measurements" must be updated as a whole. It should be set once when measurement is complete, not incrementally.
// Metrics can me omitted if they weren't successfully measured, i.e. the peer is unreachable.
// If a "measurements" object is set, it can't contain an entry for the member's own address.
"measurements": {
"e69c43a8": {
"latency": 5953,  // Milliseconds
"bandwidthDown": 19342,  // Kilobits per second
"bandwidthUp": 944,  // Kilobits per second
"custom": { }
     }
},

// QoS measurements by game-server connection string. Like all fields, "serverMeasurements" must be updated as a whole, so it should be set once when measurement is complete.
// If empty, it means that none of the measurements completed within the "serverMeasurementTimeout".
    "serverMeasurements": {
        "east.thunderhead.azure.com": {
            "latency": 233  // Milliseconds
        }
    },

// Subscriptions for shoulder taps on session changes. The 'profile' indicates which session changes to tap as well as other properties of the registration like the min time between taps.
// The subscription is named with a client-generated GUID that is also sent back with the tap as a context ID.
// Subscriptions can be added and removed individually, without affecting other subscriptions in the "subscriptions" object.
// To remove a subscription, set its context ID to null.
// (Like the "ready" and "active" flags, the "subscriptions" data is copied out and maintained internally, so the normal replace-all rule on system fields doesn't apply to "subscriptions".)
"subscriptions": {
"961dc162-3a8c-4982-b58b-0347ed086bc9": {
"profile": "party",  // Or "matchmaking", "initialization", "roster", "queueHost", or "queue"
"onBehalfOfTitleId": "3948320593",  // Optional decimal title ID of the registered channel.  If not set the title ID is taken from the token.
},
"709fef70-4638-4b94-905b-24cb02706eb5": null
}
}

```

## <a name="qos-phase-and-session-initialization-details"></a>Данные инициализации QoS этапа и сеансов

MPSD отслеживает и отчета о результатах качества обслуживания для создания игр после завершения инициализации члена шаблона. Ход выполнения этой операции будет представлен *инициализация* объекта в документе сеанса, как описано в разделе [инициализация члена](#_Member_initialization_in) предыдущем разделе. *Инициализация* объект имеет *этап* атрибут, который представляет текущий этап инициализации. Рабочей области продвижения *присоединение* для *измерение* для *оценки*.

-   Если инициализация эпизод \#1 не удается, то этап присваивается *сбой* и не удается инициализировать сеанс. В противном случае по завершении этого эпизода инициализации «инициализация» объект удаляется. Если **externalEvaluation** присваивается **false**, оценке этап пропускается. Если ни один из **метрики** , ни **measurementServerAddresses** не установлен, пропускается измерения рабочей области.

```json
"initializing": {
    "stage": "measuring",
    "stageStartTime": "2009-06-15T13:45:30.0900000Z",
    "episode": 1
},

```

-   Узел кандидатами являются маркеры устройств, перечислены в порядке предпочтения. Они задаются после *измерение* этапа инициализации эпизод \#1, если **peerToHostRequirements** имеет значение и **/properties/system/host** не задано. Они также будут удалены после **/properties/system/host** задан объект.

```json
"hostCandidates": [ "ab90a362", "99582e67" ],

"constants": { /* Property Bag */ },
"properties": { /* Property Bag */ },
"members": {
    "1": {
        "constants": { /* Property Bag */ },
        "properties": { /* Property Bag */ },

```

-   *: Тег игрока* атрибут будет задан, только если элемент принят, и утверждения: тег игрока, найденный для этого элемента.

```json
"gamertag": "stacy",
```

-   *DeviceToken* атрибут имеет значение, когда элемент отправляет по адресу безопасные устройства. Он является строкой, без учета регистра, который может использоваться для сравнения на равенство.

```json
"deviceToken": "9f4032ba7",
```

-   *Зарезервированные* значение удаляется после выполнения пользователем свой первый запрос PUT, документ сеанса. Когда игроки зарезервированы, это означает, что они были приглашены в сеанс игр, но не приняли и не имели их подключений вычисляется.

```json
"reserved": true,
```

-   Если элемент активен, *activeTitleId* — это заголовок, в котором элемент активен, в десятичном формате.

```json
"activeTitleId": "8397267",
```

-   Этот атрибут — это время, что пользователь, присоединенных к сеансу. Если *зарезервированные* — **true**, затем *joinTime* указывает время, по которому резервирования.

```json
"joinTime": "2009-06-15T13:45:30.0900000Z",
```

-   Представления, если этот элемент в массиве свойств/system/turn не отображается.

```json
"turn": true,
```

-   *InitializationFailure* атрибут имеет значение члена объекта при переходит за пределы присоединении или измерение рабочей области, если элемент не истек успешно рабочей области. В порядке приоритета, ему может быть присвоено *время ожидания*, *задержки*, *bandwidthdown*, *bandwidthup*, *сети* , *группы*, или *эпизод*.
    *Сети* значение означает, что конфигурация сети и/или условий (таких как конфликтующие преобразования сетевых адресов \[NAT\]) запрещено измеряемого метрик качества обслуживания. Единственное возможное значение в конце присоединение — *группы*. (По истечении времени ожидания присоединяться к резервирование удаляется.) *Эпизод* значение устанавливается после этапа неудачных «оценки» во всех элементах эпизод инициализации, в том, что не сбой во время присоединения или измерение.

```json
"initializationFailure": "latency",
```

-   Если **memberInitialization** имеет значение и добавлен элемент с «инициализировать»: true, это имеет значение эпизод инициализации, который элемент будет участвовать в. Используется значение 1 для элементы, добавленные к новому сеансу во время создания. Удаляются при завершении инициализации эпизод.

```json
"initializationEpisode": 1,
```

-   *Далее* атрибут представляет значение индекса следующего элемента в сеансе. Она совпадает со значением *Далее* атрибут в **membersInfo** объекта ниже, если нет дополнительных участников для добавления.

```json
            "next": 4
        },
        "4": { "next": 5 /* etc */ }
    },
    "membersInfo": {
        "first": 1,  // The first member's index.
        "next": 5,  // The index that the next member added will get.
        "count": 2,  // The number of members.
        "accepted": 1  // The number of members that are no longer 'pending'.
    },
    "servers": {
        "name": {
            "constants": { /* Property Bag */ },
            "properties": { /* Property Bag */ }
        }
    }
}

```

## <a name="xbox-cloud-compute-session"></a>Сеанс Xbox облачных вычислений

Сеанс облачных вычислений Xbox содержит упорядоченный список строки подключения, без учета регистра, сеанс может использовать для подключения к серверу игр. Как правило заголовки следует использовать первую строку в списке, но сложных заголовки может использовать пользовательский механизм (например, Xbox Live вычислений) для выбора одной из них (например, на основе нагрузки).

```json
    "serverConnectionStringCandidates": [ "west.thunderhead.azure.com", "east.thunderhead.azure.com" ],

    "matchmaking": {
        "clientResult": {  // Requires the clientMatchmaking property.
            "status": "searching",  // Or "expired", "found", "failed", or "canceled".
            "statusDetails": "Description",  // Default is empty string.
            "typicalWait": 30,  // The expected number of seconds waiting as a non-negative integer.
            "targetSessionRef": {
                "scid": "1ECFDB89-36EB-4E59-8901-11F7393689AE",
                "templateName": "capture-the-flag",
                "name": "2D58F65F-0E3C-4F1F-8277-2BC9873FDB23"
            }
        },

        "targetSessionConstants": { },

        // Force a specific connection string to be used (useful in preserveSession=always cases).
        "serverConnectionString": "west.thunderhead.azure.com",

        // True if the match that was found didn't work out and needs to be resubmitted. Set to false
        // to signal that the match did work, and the matchmaking service can release the session.
        "resubmit": true
    }
}

```

**/servers/{server-name}/properties/system **

```json
{
    "lockId": "opaque56789",  // If set, a matchmaking service is servicing this session.
    "status": "searching",  // Or "expired", "found", "failed", or "canceled". Optional.
    "statusDetails": "Description",  // Optional free-form text. Default is empty string.
    "typicalWait": 30,  // Optional. The expected number of seconds waiting as a non-negative integer.
    "targetSessionRef": {  // Optional.
        "scid": "1ECFDB89-36EB-4E59-8901-11F7393689AE",
        "templateName": "capture-the-flag",
        "name": "2D58F65F-0E3C-4F1F-8277-2BC9873FDB23"
    }
}

```

## <a name="raw-session-and-custom-title-properties"></a>Необработанные сеанса и свойства пользовательского заголовка

Сеанс можно использовать для хранения сведений о пользовательских по обслуживанию (метаданные) вокруг игры. Игры или сохраненные данные должны храниться в TMS ++.

### <a name="property-bags"></a>Контейнеры свойств

Каждый из объектов выше, помеченная как контейнер свойств состоит из двух необязательно внутренних объектов, системы и пользовательские.

Настраиваемые объекты могут содержать любой JSON.

```json
"custom": {
    "myField1": true,
    "myField2": "string",
    "myField3": 5.5,
    "myField4": { "myObject": null },
    "myField5": [ "my", "array" ]
}

```

## <a name="active-member-decay"></a>Decay активным участником

Активные члены автоматически помечаются неактивные, когда MPSD обнаруживает, что пользователь больше не участвует в заголовке. Это может произойти, например, если наличие времени ожидания записи пользователя. Этот механизм является просто поддержку; то есть заголовки по-прежнему необходимы заранее помечайте члены как неактивные (или удалить их из сеанса) когда члены оставьте или из него название, становятся выключенное sign out или иным образом.

## <a name="faq-and-troubleshooting"></a>Часто задаваемые вопросы и устранение неполадок

### <a name="how-do-i-call-mpsd-"></a>Как вызвать MPSD?

С помощью проверки подлинности сертификата: sessiondirectory.xboxlive.com клиента

Пример.

```
PUT https://client-sessiondirectory-stress.xboxlive.com/serviceconfigs/8cvda84-2606-4bab-8eda-d12313e65143/sessiontemplates/teamDeathmatch/sessions/3baafc35-816d-49cd-9656-5772506c988a
```

С помощью проверки подлинности XToken: sessiondirectory.xboxlive.com

Пример.

```
PUT https://sessiondirectory-stress.xboxlive.com/serviceconfigs/8cvda84-2606-4bab-8eda-d12313e65143/sessiontemplates/teamDeathmatch/sessions/3baafc35-816d-49cd-9656-5772506c988a
```

### <a name="how-do-i-find-out-what-scid-session-template-and-sandbox-to-use"></a>Как узнать какие SCID шаблона сеанса и "песочницы" для использования?

Эти сведения можно найти на сайте XDP названия. Если у вас еще нет доступа к XDP обратитесь к менеджеру разработчика, который может помочь в получении информации для вас.

### <a name="is-there-an-example-of-a-request-body-that-i-can-compare-my-own-to"></a>Есть ли пример тела запроса, который может сравнить, я вручную для?


### <a name="i-am-getting-a-404-error-when-calling-mpsd"></a>При вызове MPSD я получаю сообщение об ошибке 404.

Соберите трассировки Fiddler для получения дополнительных сведений и выполните одно из следующих:

1.  Проверка, возвращаемое сообщение, как часть текста HttpResponse для распространенных сообщений 404:

    -   *Файл конфигурации запрошенной службы имеет неверное или не настроено для сеансов*. Проверьте правильность SCID, который используется.

    -   *Запрошенный сеанс не был найден*. Убедитесь, что сеанс будет создан, и сеанс правильности выбора шаблона перед его возвращением. Можно создать сеанс с вызов PUT.

2.  Проверьте URI, который вы используете.

3.  Перезагрузить консоль и/или повторите попытку, используя нового пользователя.

4.  Поиск [форумы разработчиков развлечения](https://developer.xboxlive.com/en-us/platform/community/forums/Pages/home.aspx) код ошибки или других возможных решений.

### <a name="i-am-getting-a-403-error-when-calling-mpsd"></a>Я получаю ошибку 403 при вызове MPSD.

Обычно это разрешения или область проблемы. Соберите трассировки Fiddler для получения дополнительных сведений и выполните одно из следующих:

1.  Проверка сообщения, возвращаемые как часть текста HttpResponse для распространенных сообщений 403:

-   * Конфигурации запрошенной службы невозможен. *

    -   Убедитесь, что вы используете учетную запись, имеет доступ к «песочнице».

    -   Убедитесь, что вы находитесь в правильный "песочницы".

    -   Если вы используете проверку подлинности сертификата и получении этой ошибки обратитесь к вашей DAM.

-   *Запрошенный сеанс будет недоступно. Закрытые сеансы можно прочитать только участниками сеанса.*

    -   Вы пытаетесь получить доступ к сеанс с видимостью «private». Только члены в рамках сеанса может читать документ сеанса.

-   *Текст запроса не может содержать ссылок на существующие элементы, если основной проверки подлинности с сервером.*

    -   Другой пользователь не удается подключиться к сеансу от имени пользователя. Вы можете только пригласить. Значение индекса «зарезервировать\_&lt;номер&gt;"пригласить проигрыватель.

### <a name="i-am-getting-a-412-precondition-failed-error"></a>Я получаю ошибку 412 Необходимое условие не выполнено.

Эта команда возвращает 412 Необходимое условие не выполнено, если сеанс уже существует:

> PUT serviceconfigs/00000000-0000-0000-0000-000000000000/sessiontemplates/быстрого/сеансы/foo HTTP/1.1 Content-Type: application/json If-None-Match: \*

Эта команда возвращает 412 Необходимое условие не выполнено, если сеанс etag не совпадает с заголовком If-Match:

> PUT serviceconfigs/00000000-0000-0000-0000-000000000000/sessiontemplates/быстрого/сеансы/foo HTTP/1.1 Content-Type: application/json If - Match: 9555A7DE-8B91-40E4-8CFB-0629312C9C7D

### <a name="i-am-getting-errors-such-as-405-409-503-and-400when-calling-mpsd"></a>Я получаю ошибки, такие как 405, 409, 503 и вызывающий MPSD 400when.

Сбор трассировки Fiddler для получения дополнительных сведений и затем проверить сообщения, возвращаемые как часть тексте HttpResponse. Это должны получить достаточно информации для выявления и исправления ошибки или для поиска [форумы разработчиков развлечения](https://developer.xboxlive.com/en-us/platform/community/forums/Pages/home.aspx) возможные решения.

Также можно получить текст ответа, если вы используете API службы Xbox Live, задав **DiagnosticsTraceLevel** свойство ошибки, которые выводят информацию в отладки выходного столбца или можно использовать  **XboxLiveContextSettings.ServiceCallRouted** событий, как показано в несколько образцов для вывода заголовка пользовательского интерфейса.

### <a name="what-can-or-should-i-change-in-the-templates-for-my-title"></a>Что можно или необходимо изменять в шаблонах для заголовок моего?

Шаблоны сеанса не используются по умолчанию, но являются сочетанием под определение. Тем не менее нельзя переопределить константы, уже задано в шаблонах, несмотря на то, что можно добавить к ним.

### <a name="im-getting-an-error-that-is-saying-my-session-isnt-initialized"></a>Я получаю сообщение об ошибке, которое говорит, что мой сеанс не инициализирован.

При инициализации членов присутствует в шаблоне (игры, сторону и сценарии билет совпадение, обычно), необходимо убедиться, что «инициализировать = true» установлено достаточное количество резервирований член (члены, необходимые для запуска) для передачи качества обслуживания, чтобы устранить эту проблему.

### <a name="my-session-is-not-being-created-and-im-getting-an-http-204-error"></a>Мой сеанс не создается, и я получаю сообщение об ошибке HTTP 204.

Это означает, что отсутствуют, пользователи не добавляются в сеанс, при его создании. Если нет пользователей для сеанса во время создания, сеанс не создается. Убедитесь, что добавить по крайней мере один проигрыватель при создании сеанса. Для сценариев на выделенном сервере получите проигрывателя, кто пытается создать сопоставления, или, необходимо создать сопоставления и предоставить этой начальной проигрывателя, соответствующие. Кроме того, можно изменить или удалить **sessionEmptyTimeout**.

### <a name="when-should-i-poll-the-mpsd"></a>Когда следует опросить MPSD?

Следует избегать опроса MPSD сеансов. На высоком уровне это можно сделать, создав код способом, который использует сеанс MPSD только для первоначальной установки подключения к сети для каждого клиента, а также для восстановления состояния сети клиента или клиентов, потеряна связь. Также важно, чтобы воспользоваться преимуществами примитивы синхронизации на основе etag в MPSD во избежание необходимости обновлять состояние сеанса, чтобы устранить конфликты.

Типичное применение этих принципов — при наличии набора N клиентов, которым необходимо соединять друг с другом и играть в peer-to-peer сетки. Вместо того, чтобы регулярно запросе сеанса новых элементов, каждый член можно присоединиться к сеансу, подключиться к элементы уже в сеансе и предполагается, что любой более поздней версии соединители различных типов будет делать то же самое. Просмотрите примеры чат и проигрывателя рандеву примеры того, как реализовать это.

Некоторых редких случаях, где опроса может оказаться необходимым для короткого периода; Если вы считаете, что необходимо сделать это, обратитесь к своему менеджеру разработчика.
