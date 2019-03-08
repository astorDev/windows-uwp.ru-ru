---
title: Списки лидеров
description: Узнайте, как использовать списки лидеров, Xbox Live для сравнения игроков.
ms.assetid: 132604f9-6107-4479-9246-f8f497978db7
ms.date: 09/28/2018
ms.topic: article
keywords: xbox live, xbox, игры, uwp, windows 10, xbox one
ms.localizationpriority: medium
ms.openlocfilehash: 8fd7e30b99418fda614a888d9269548cdc57a88a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57662029"
---
# <a name="leaderboards"></a>Списки лидеров

## <a name="introduction"></a>Введение

Как описано в разделе [обзор платформы данных](../data-platform/data-platform.md), списки лидеров — отличный способ способствуют конкуренции между игроков и удержание игроков в ознакомлении с растущим потоком их предыдущих лучший результат, а также со своими друзьями.

Списки лидеров для [рекомендуемые Stats](stats2017.md#configured-stats-and-featured-leaderboards) всегда отображаются в центре заголовок игры и иногда отображаются как часть пользовательского интерфейса для заголовка, когда она закреплена на домашнюю страницу. Вашего настроенного Stats Избранное также можно создать списки лидеров внутри заголовка.

## <a name="choosing-good-leaderboards"></a>Выбор хороший списки лидеров

Как уже говорилось в [Stats проигрывателя](player-stats.md), список лидеров соответствует stat, которую вы определили.  Необходимо выбрать списки лидеров, соответствующие достижение, которая проигрыватель может работать как к повышению.

К примеру лучшее время круга в гоночную игру обусловлено тем, хороший "список лидеров" игроков может требоваться работать к повышению их лучшее время круга.  Другие примеры: соотношение Kill/смерти стрелялки или максимальный размер поля со списком в порядке игре.

## <a name="when-to-display-leaderboards"></a>Время отображения списки лидеров

У вас есть возможность использовать списки лидеров в любое время в вашей должности.  Выберите время, когда список лидеров не будет мешать игровой процесс или поток название.  Между Округляет число и после совпадения оба хорошие времена.

## <a name="how-to-display-leaderboards"></a>Как отобразить списки лидеров

Существует множество вариантов для отображения списки лидеров в Xbox Live SDK.  Если вы используете Unity с помощью Xbox Live Creators Program, можно приступить к работе с помощью списка лидеров Prefab для отображения данных списка лидеров.  См. в разделе [Настройка Xbox Live в Unity](../get-started-with-creators/configure-xbox-live-in-unity.md) статье конкретные сведения.

Если вы пишете непосредственно Xbox Live SDK, а затем продолжайте чтение, чтобы узнать об API-интерфейсах, можно использовать.

## <a name="programming-guide"></a>Руководство по программированию

Существует несколько интерфейсов API для списка лидеров, можно использовать для получения текущего состояния список лидеров.  Все API-интерфейсы являются асинхронными и не блокируют.  Можно производить в запрос на получение данных "список лидеров" и по-прежнему на обычную обработку игр.  При "список лидеров" результаты возвращаются из службы, можно отобразить результаты в соответствующее время.

Данные списка лидеров в службе, следует запросить немного опережают, когда необходимо отобразить ее, игроки не блокируются ожидание списка лидеров для отображения.

## <a name="leaderboards-2013-apis"></a>API-интерфейсы 2013 списки лидеров

Вы увидите `leaderboard_service` пространство имен для всех API списка лидеров 2013 Stats.

<table>

<tr>
<td>C++ API</td><td>Описание</td>
</tr>

<tr>
<td markdown="block">

```cpp

pplx::task<xbox_live_result<leaderboard_result>> get_leaderboard(
        const string_t& scid,
        const string_t& name
        );
```

</td>

<td>Наиболее простая версия API.  Это возвращает значения списка лидеров для заданного списка лидеров, начиная с проигрывателя в верхней части списка лидеров.</td>

</tr>

<tr>

<td markdown="block">

```csharp
Windows::Foundation::IAsyncOperation< LeaderboardResult^> ^  GetLeaderboardAsync (
        _In_ Platform::String^ serviceConfigurationId,
         _In_ Platform::String^ leaderboardName
        ) 
```

</td>

<td>WinRT C# кода — получить список лидеров для одного списка лидеров, учитывая кода конфигурации службы и имя списка лидеров.</td>

</tr>

<tr>
<td markdown="block">

```cpp

pplx::task<xbox_live_result<leaderboard_result>> get_leaderboard(
    _In_ const string_t& scid,
    _In_ const string_t& name,
    _In_ uint32_t skipToRank,
    _In_ uint32_t maxItems = 0
    );

```

</td>

<td>Этот API предоставляет некоторые большую гибкость, можно указать ранг (позиция), будет отображаться, а также максимальное значение параметра возвращаемых элементов.  Например этот API можно использовать, если для отображения списка лидеров, начиная с позиции 1000.</td>

</tr>

<tr>

<td markdown="block">

```csharp
Windows::Foundation::IAsyncOperation< LeaderboardResult^> ^  GetLeaderboardAsync (
         _In_ Platform::String^ serviceConfigurationId,
         _In_ Platform::String^ leaderboardName,
         _In_ uint32 skipToRank,
         _In_ uint32 maxItems
        ) 
```

</td>

<td>WinRT C# кода — откроется страница "список лидеров" результатов для одного "список лидеров" предоставить кода конфигурации службы и имя списка лидеров, результаты будут начинаются с «skipToRank» ранг "список лидеров".</td>

</tr>

<tr>

<td markdown="block">

```cpp

pplx::task<xbox_live_result<leaderboard_result>> get_leaderboard_skip_to_xuid(
    _In_ const string_t& scid,
    _In_ const string_t& name,
    _In_ const string_t& skipToXuid = string_t(),
    _In_ uint32_t maxItems = 0
    );

```

</td>

<td>

Используйте его, если вы хотите пропустить списка лидеров для конкретного пользователя.  Объект `XUID` — это уникальный идентификатор для каждого пользователя Xbox.  Можно получить для пользователя, выполнившего или любого из своих друзей и передать его в эту функцию.

</td>

</tr>

<tr>

<td markdown="block">

```csharp
Windows::Foundation::IAsyncOperation< LeaderboardResult^> ^  GetLeaderboardWithSkipToUserAsync (
         _In_ Platform::String^ serviceConfigurationId,
         _In_ Platform::String^ leaderboardName,
         _In_opt_ Platform::String^ skipToXboxUserId,
         _In_ uint32 maxItems
        )
```

</td>

<td>WinRT C# кода — получить список лидеров, начиная с указанного проигрывателя, независимо от того, ранг или оценки, упорядоченные по игрока процентильный ранг игрока</td>

</tr>

</table>

## <a name="2013-c-example"></a>Пример 2013 C++

При использовании C++ API слой, который затем можно задать обратный вызов должен быть вызван после списка лидеров результаты возвращаются из службы.  Мы покажем пример этого ниже.

Если вы не знакомы с `pplx::task` , возвращаемой из этих интерфейсов API, это объект асинхронную задачу из Microsoft параллельных программирования Library (PPL).  Дополнительные сведения об этом в [ https://github.com/Microsoft/cpprestsdk/wiki/Programming-with-Tasks ](https://github.com/Microsoft/cpprestsdk/wiki/Programming-with-Tasks).

В разделе ниже показано, как может получать результаты списка лидеров и использовать их.

### <a name="1-create-an-async-task-to-retrieve-leaderboard-results"></a>1. Создание асинхронной задачи для получения результатов "список лидеров"

Первым делом для вызова службы списки лидеров для получения результатов для определенного списка лидеров.

```cpp
pplx::task<xbox_live_result<leaderboard_result>> asyncTask;
auto& leaderboardService = xboxLiveContext->leaderboard_service();

asyncTask = leaderboardService.get_leaderboard(m_liveResources->GetServiceConfigId(), LeaderboardIdEnemyDefeats);
```

### <a name="2-setup-a-callback"></a>2. Настройка обратного вызова

Вы можете настроить [задача продолжения](https://msdn.microsoft.com/en-us/library/dd492427(v=vs.110).aspx#continuations) нужно вызвать после списка лидеров результаты возвращаются.  Это сделать следующим образом ниже.

```cpp
asyncTask.then([this](xbox::services::xbox_live_result<xbox::services::leaderboard::leaderboard_result> result)
{
    // Handle result here
});
```

Эта задача продолжения вызывается в контексте объекта, который изначально его вызвала и получает ```leaderboard_result``` отображаемые способом, который подходит для заголовка.


### <a name="3-display-leaderboard"></a>3. Отображение списка лидеров

Данные списка лидеров, содержатся в ```leaderboard_result``` и поля говорят сами за себя.  См. ниже приведен пример.

```cpp
auto leaderboard = result.payload();

for (const xbox::services::leaderboard::leaderboard_row& row : leaderboard.rows())
{
    string_t colValues;
    for (auto columnValue : row.column_values())
    {
        colValues = colValues + L" ";
        colValues = colValues + columnValue;
    }
    m_console->Format(L"%18s %8d %14f %10s\n", row.gamertag().c_str(), row.rank(), row.percentile(), colValues.c_str());
}

```

## <a name="2013-winrt-c-example"></a>2013 WinRT C# пример

При использовании WinRT C# слоя, не нужно будет сделать обратный вызов отдельных задач и будет просто потребность в использовании `await` ключевое слово при вызове службы списка лидеров.

### <a name="1-access-the-leaderboardservice"></a>1. Доступ к LeaderboardService

`LeaderboardService` Могут быть получены из `XboxLiveContext` создан при входе пользователя в игру, он понадобится для вызова для списка лидеров в данных.

```csharp
XboxLiveContext xboxLiveContext = idManager.xboxLiveContext;
LeaderboardService boardService = xboxLiveContext.LeaderboardService;
```

### <a name="2-call-the-leaderboardservice"></a>2. Вызовите LeaderboardService

```csharp
LeaderboardResult boardResult = await boardService.GetLeaderboardAsync(
     xboxLiveConfig.ServiceConfigurationId,
     leaderboardName
     );
```

### <a name="3-retrieve-leaderboard-data"></a>3. Получить данные списка лидеров

`GetLeaderboardAsync()` Возвращает `LeaderboardResult` оно будет содержать заполнение именованного списка лидеров статистики.

`LeaderboardResult` содержит несколько функций и свойств для упрощения чтения данных "список лидеров".

|Свойство  |Описание  |
|---------|---------|
|public IAsyncOperation<LeaderboardResult> GetNextAsync(uint maxItems);     |Извлекает следующий набор ранги не превышающим maxItems параметра. Это по сути еще один вызов `GetLeaderboard()`         |
|public LeaderboardQuery GetNextQuery();     |Извлекает LeaderboardQuery, который может быть использован для вызова списка лидеров для получения следующего набора данных.         |
|public bool HasNext { get; }    |обозначает ли имеются другие строки списка лидеров для извлечения         |
|открытый IReadOnlyList<LeaderboardRow> строк {get;}     | Строки, содержащие данные списка лидеров по рангу        |
|public IReadOnlyList<LeaderboardColumn> Columns { get; }     | Список столбцов, составляющих "список лидеров"        |
|открытый uint TotalRowCount {get;}     | Общее количество строк в "список лидеров"        |
|public string DisplayName { get; }     | Имя, отображаемое для списка лидеров       |

Данные списка лидеров будет предоставляться одной странице за раз. Может организовать цикл по `LeaderboardResult` строки и столбцы для извлечения данных.  
Используйте `HasNext` логическое и `GetNextAsync()` функции для получения последующие страницы данных "список лидеров".

```csharp
if (boardResult != null)
{
    foreach (LeaderboardRow row in boardResult.Rows)
    {
        Debug.Write(string.Format("Rank: {0} | Gamertag: {1} | {2}\n", row.Rank, row.Gamertag, row.Values.ToString()));
    }
}
```

## <a name="leaderboard-2017"></a>Список лидеров 2017

Для выполнения вызовов к службе списка лидеров статистики 2017 г., будет использоваться `StatisticManager` API списка лидеров, а не `LeaderboardService` "список лидеров" API-интерфейсы.  

`xbox::services::stats:manager::stats_manager::get_leaderboard`  

```cpp
xbox_live_result< void >  get_leaderboard (
     _In_ const xbox_live_user_t &user,
     _In_ const string_t &statName,
     _In_ leaderboard::leaderboard_query query
     ) 
```  

`xbox::services::stats:manager::stats_manager::get_leaderboard`  

```cpp
xbox_live_result< void >  get_social_leaderboard (_In_ const xbox_live_user_t &user,
     _In_ const string_t &statName,
     _In_ const string_t &socialGroup,
     _In_ leaderboard::leaderboard_query query
)
```  

`Microsoft.Xbox.Services.Statistics.Manager.StatisticManager.GetLeaderboard`  

```csharp
public void GetLeaderboard(
    XboxLiveUser user,
    string statName,
    LeaderboardQuery query
    )
```  

`Microsoft.Xbox.Services.Statistics.Manager.StatisticManager.GetSocialLeaderboard`  

```csharp
public void GetSocialLeaderboard(
    XboxLiveUser user,
    string statName,
    string socialGroup,
    LeaderboardQuery query
    )
```  

## <a name="2017-c-example"></a>Пример 2017 C++

### <a name="1-get-a-singleton-instance-of-the-statsmanager"></a>1. Получите одноэлементный экземпляр stats_manager

Перед вызовом `stats_manager` функции, вам потребуется установить переменную на экземпляр Singleton.

```csharp
m_statsManager = stats_manager::get_singleton_instance();
```

### <a name="2-create-a-leaderboardquery"></a>2. Создание LeaderboardQuery

`leaderboard_query` Определяет сумму заказа, и начальная точка данных, возвращаемого из вызова "список лидеров".

Объект `leaderboard_query` имеет несколько атрибутов, которые могут устанавливаться которых повлияет на данные, возвращенные:

|Свойство |Описание  |
|---------|---------|
|m_skipResultToRank     |Эта переменная целое число без знака определит ранжирующие данные списка лидеров запускают с при возврате. Ранжирование начинаются с ранг 1.         |
|m_skipResultToMe     |Если задать значение true, это логическое значение приведет к "список лидеров" данные, возвращаемые начинаются с `XboxLiveUser` используется в `get_leaderboard()` вызова.  |
|m_order     |Перечисления типа `xbox::services::leaderboard::sort_order` имеют два возможных значения: по возрастанию или по убыванию. Переменной запроса определит порядок сортировки вашего списка лидеров.        |
|m_maxItems     |Это целое число без знака определяет максимальное число строк, возвращаемых за каждый вызов `get_leaderboard` или `get_social_leaderboard()`.         |

`leaderboard_query` имеет несколько функция set, которые можно использовать для присвоения значения этих свойств. Ниже показано, как можно настроить на `leaderboard_query`

```cpp
leaderboard::leaderboard_query leaderboardQuery;
leaderboardQuery.set_skip_result_to_rank(10);
leaderboardQuery.set_max_items(10);
leaderboardQuery.set_order(sort_order::descending);
```

Этот запрос возвращает отсортированные десять строк, начиная с разбиении списка лидеров отдельных.

> [!WARNING]
> Значение выше, чем число исполнителей, содержащихся в таблице лидеров SkipResultToRank приведет к созданию списков лидеров в данные, возвращаемые с нулем строк.

### <a name="3-call-getleaderboard"></a>3. Вызов get_leaderboard

```cpp
leaderboard::leaderboard_query leaderboardQuery;
m_statsManager->get_leaderboard(user, statName, leaderboardQuery);
```

> [!IMPORTANT]
> `statName` Используется в `GetLeaderboard()` вызов должен быть совпадает с именем stat, настроенный для заголовка в [центра партнеров](https://partner.microsoft.com/dashboard), который учитывается регистр.

### <a name="4-read-the-leaderboard-data"></a>4. Чтение данных "список лидеров"

Чтобы считать данные списка лидеров, вам потребуется вызвать `stats_manager::do_work()` функцию, которая будет возвращать список `stat_event` значения. Список лидеров данных будут содержаться в `stat_event` типа `stat_event_type::get_leaderboard_complete`. Если вы столкнетесь события этого типа в списке `stat_event`s, может просмотреть `leaderboard_result` содержащихся в `stat_event` доступ к данным.

Пример `do_work()` обработчика

```cpp
void Sample::UpdateStatsManager()
{
    // Process events from the stats manager
    // This should be called each frame update

    auto statsEvents = m_statsManager->do_work();
    std::wstring text;

    for (const auto& evt : statsEvents)
    {
        switch (evt.event_type())
        {
            case stat_event_type::local_user_added: 
                text = L"local_user_added"; 
                break;

            case stat_event_type::local_user_removed: 
                text = L"local_user_removed"; 
                break;

            case stat_event_type::stat_update_complete: 
                text = L"stat_update_complete"; 
                break;

            case stat_event_type::get_leaderboard_complete: //leaderboard data is read here
                text = L"get_leaderboard_complete";
                auto getLeaderboardCompleteArgs = std::dynamic_pointer_cast<leaderboard_result_event_args>(evt.event_args());
                ProcessLeaderboards(evt.local_user(), getLeaderboardCompleteArgs->result());
                break;
        }

        stringstream_t source;
        source << _T("StatsManager event: ");
        source << text;
        source << _T(".");
        m_console->WriteLine(source.str().c_str());
    }
}
```

Чтение данных "список лидеров" из результата "список лидеров"  

```cpp
void Sample::PrintLeaderboard(const xbox::services::leaderboard::leaderboard_result& leaderboard)
{
    if (leaderboard.rows().size() > 0)
    {
        m_console->Format(L"%16s %6s %12s %12s\n", L"Gamertag", L"Rank", L"Percentile", L"Values");
    }

    for (const xbox::services::leaderboard::leaderboard_row& row : leaderboard.rows())
    {
        string_t colValues;
        for (auto columnValue : row.column_values())
        {
            colValues = colValues + L" ";
            colValues = colValues + columnValue;
        }
        m_console->Format(L"%16s %6d %12f %12s\n", row.gamertag().c_str(), row.rank(), row.percentile(), colValues.c_str());
    }
}
```  

Загрузить другие страницы данных из списка лидеров.  

```cpp
void Sample::ProcessLeaderboards(
    _In_ std::shared_ptr<xbox::services::system::xbox_live_user> user,
    _In_ xbox::services::xbox_live_result<xbox::services::leaderboard::leaderboard_result> result
    )
{
    if (!result.err())
    {
        auto leaderboardResult = result.payload();
        PrintLeaderboard(leaderboardResult);

        // Keep processing if there is more data in the leaderboard
        if (leaderboardResult.has_next())
        {
            if (!leaderboardResult.get_next_query().err())
            {               
                auto lbQuery = leaderboardResult.get_next_query().payload();
                if (lbQuery.social_group().empty())
                {
                    m_statsManager->get_leaderboard(user, lbQuery.stat_name(), lbQuery);
                }
                else
                {
                    m_statsManager->get_social_leaderboard(user, lbQuery.stat_name(), lbQuery.social_group(), lbQuery);
                }
            }
        }
    }
}
```  

## <a name="2017-winrt-c-example"></a>2017 WinRT C# пример

### <a name="1-get-a-singleton-instance-of-the-statisticmanager"></a>1. Получите одноэлементный экземпляр StatisticManager

Перед вызовом `StatisticManager` функции, вам потребуется установить переменную на экземпляр Singleton.

```csharp
statManager = StatisticManager.SingletonInstance;
```

### <a name="2-create-a-leaderboardquery"></a>2. Создание LeaderboardQuery

`LeaderboardQuery` Определяет сумму заказа, и начальная точка данных, возвращаемого из вызова "список лидеров".  

```csharp
public sealed class LeaderboardQuery : __ILeaderboardQueryPublicNonVirtuals
    {
        [Overload("CreateInstance1")]
        public LeaderboardQuery();

        public bool HasNext { get; }
        public string SocialGroup { get; }
        public string StatName { get; }
        public SortOrder Order { get; set; }
        public uint MaxItems { get; set; }
        public uint SkipResultToRank { get; set; }
        public bool SkipResultToMe { get; set; }
    }
```

Объект `LeaderboardQuery` имеет несколько атрибутов, которые могут устанавливаться которых повлияет на данные, возвращенные:

|Свойство |Описание  |
|---------|---------|
|SkipResultToRank     |Эта переменная целое число без знака определит ранжирующие данные списка лидеров запускают с при возврате. Ранжирование начинаются с ранг 1.         |
|SkipResultToMe     |Если задать значение true, это логическое значение приведет к "список лидеров" данные, возвращаемые начинаются с `XboxLiveUser` используется в `GetLeaderboard()` вызова.  |
|Order (Порядок)     |Перечисления типа `Microsoft.Xbox.Services.Leaderboard.SortOrder` имеют два возможных значения: по возрастанию или по убыванию. Переменной запроса определит порядок сортировки вашего списка лидеров.        |
|maxItems     |Это целое число без знака определяет максимальное число строк, возвращаемых за каждый вызов `GetLeaderboard()` или `GetSocialLeaderboard()`.         |

Формирование вашей `LeaderboardQuery` может выглядеть следующим образом:

```csharp
using Microsoft.Xbox.Services.Leaderboard;

LeaderboardQuery query = new LeaderboardQuery
        {
            SkipResultToRank = 100,
            MaxItems = 5
        };
```

Этот запрос возвращает отсортированные пять строк, начиная с разбиении списка лидеров отдельных.

> [!WARNING]
> Значение выше, чем число исполнителей, содержащихся в таблице лидеров SkipResultToRank приведет к созданию списков лидеров в данные, возвращаемые с нулем строк.

### <a name="3-call-getleaderboard"></a>3. Вызов GetLeaderboard()

Теперь вы можете вызвать `GetLeaderboard()` с вашей `XboxLiveUser`, имя вашего статистики и `LeaderboardQuery`.

```csharp
statManager.GetLeaderboard(xboxLiveUser, statName, leaderboardQuery);
```

> [!IMPORTANT]
> `statName` Используется в `GetLeaderboard()` вызов должен быть совпадает с именем stat, настроенный для заголовка в [центра партнеров](https://partner.microsoft.com/dashboard), который учитывается регистр.

### <a name="4-read-leaderboard-data"></a>4. Список лидеров чтения данных

Чтобы считать данные списка лидеров, вам потребуется вызвать `StatisticManager.DoWork()` функцию, которая будет возвращать список `StatisticEvent` значения. Список лидеров данных будут содержаться в `StatisticEvent` типа `GetLeaderboardComplete`. Если вы столкнетесь события этого типа в списке `StatisticEvent`s, может просмотреть `LeaderboardResult` содержащихся в `StatisticEvent` доступ к данным.

```csharp
IReadOnlyList<StatisticEvent> statEvents = statManager.DoWork(); //In practice this should be called every update frame

foreach(StatisticEvent statEvent in statEvents)
{
    if(statEvent.EventType == StatisticEventType.GetLeaderboardComplete
        && statEvent.ErrorCode == 0)
    {
        LeaderboardResultEventArgs leaderArgs = (LeaderboardResultEventArgs)statEvent.EventArgs;
        LeaderboardResult leaderboardResult = leaderArgs.Result;
        foreach(LeaderboardRow leaderRow in leaderboardResult.Rows)
        {
            Debug.WriteLine(string.Format("Rank: {0} | Gamertag: {1} | {2}:{3}\n\n", leaderRow.Rank, leaderRow.Gamertag, "test", leaderRow.Values[0]));
        }
    }
}
```

В коде title `StatisticManager.DoWork()` следует использовать для обработки всех входящих событий диспетчера статистики, а не только для списки лидеров. 

> [!NOTE]
> Чтобы получить `LeaderboardResultEventArgs` необходимо выполнить приведение `StatisticEvent.EventArgs` как `LeaderboardResultEventArgs` переменной.

### <a name="5-retrieve-more-leaderboard-data"></a>5. Получить дополнительные данные списка лидеров

Чтобы получить последующие страницы данных списка лидеров, необходимо будет использовать `LeaderboardResult.HasNext` свойство и `LeaderboardResult.GetNextQuery()` функции для получения `LeaderboardQuery` , вы попадете на следующую страницу данных.

```csharp
while (leaderboardResult.HasNext)
{
    leaderboardQuery = leaderboardResult.GetNextQuery();
    statManager.GetLeaderboard(xboxLiveUser, leaderboardQuery.StatName, leaderboardQuery)
    // StatisticManager.DoWork() is called
    // Leaderboard results are read
}
```