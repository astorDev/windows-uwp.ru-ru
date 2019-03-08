---
title: Скрипт список лидеров в Unity
description: Руководство по созданию собственных списков лидеров в Unity
ms.date: 04/24/2018
ms.topic: get-started-article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, unity, списки лидеров
ms.openlocfilehash: 6e73ffd9b55f3638eb3cf4245c6f7943fe92dc48
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57608759"
---
# <a name="script-a-leaderbaord-gameobject"></a>Сценарий leaderbaord GameObject

Для тех, кому нужны возможности пользовательского списка лидеров в этой статье предоставит инструменты для реализации собственного списка лидеров, перейдя по API, доступных для разработчиков Unity. Когда вы знаете, как получить данные списка лидеров можно применить его к пользовательскому интерфейсу по своему выбору.

## <a name="call-for-leaderboard-data"></a>Вызов для данных "список лидеров"

Существуют два вызова API для извлечения данных списка лидеров.

- `void GetLeaderboard(XboxLiveUser user, string statName, LeaderboardQuery query)`
- `void GetSocialLeaderboard(XboxLiveUSer user, string statName, string socialGroup, LeaderboardQuery query)`

Для успешного осуществления любой из этих вызовов возвращают данные, вам потребуется получить `XboxLiveUser` по [вход](unity-prefabs-and-sign-in.md), имеют [настроен stat](add-stats-and-leaderboards-in-unity.md) со значением для по крайней мере один проигрыватель и форму `LeaderboardQuery`. Статьях по ссылкам можно получить, если вы не уже знаете, как для входа в систему пользователь или нужно инициализировать статистику для вашего списка лидеров. После инициализации статистики самый простой способ связать его с "список лидеров" сценарий является включение одного из prefabs статистики: `IntegerStat`, `DoubleStat`, или `StringStat` как общедоступную переменную. Ваш stat потребуется его идентификатор свойства, настроенного по крайней мере как это мы будем использовать для **statName** параметра при вызове для наших данных "список лидеров". Наконец, вам потребуется форму `LeaderboardQuery` объекта.
Объект `LeaderboardQuery` имеет несколько атрибутов, которые могут устанавливаться которых повлияет на данные, возвращенные:

- **SkipResultToRank**: Если задано, эта переменная целое число без знака определит ранжирующие данные списка лидеров запускают с при возврате. Ранжирование начинаются с ранг 1.
- **SkipResultToMe**: Если задать значение true, это логическое значение приведет к "список лидеров" данные, возвращаемые начинаются с `XboxLiveUser` используется в `GetLeaderboard()` вызова.
- **Порядок**: Перечисления типа `Microsoft.Xbox.Services.Leaderboard.SortOrder` имеют два возможных значения: по возрастанию или по убыванию. Переменной запроса определит порядок сортировки вашего списка лидеров.
- **MaxItems**: Это целое число без знака определяет максимальное число строк, возвращаемых за каждый вызов `GetLeaderboard()` или `GetSocialLeaderboard()`.

Формирование вашей leaderboardQuery может выглядеть следующим образом:

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

Теперь, когда у нас есть все элементы друг с другом, мы можем вызвать `GetLeaderboard(XboxLiveUser user, string statName, LeaderboardQuery query)` функции.

`GetSocialLeaderboard(XboxLiveUSer user, string statName, string socialGroup, LeaderboardQuery query)` Функция имеет один дополнительный параметр с именем socialGroup. Эта строка выступает в качестве фильтра отношение к полученным данным. Допустимые значения для socialGroup: следующим образом:

- «все»: Эта команда возвращает список лидеров, отфильтрованные XboxLiveUser друзьям
- «Избранное»: Эта команда возвращает список лидеров, отфильтрованные XboxLiveUser избранные друзьям

Можно использовать `LeaderboardTypes` перечисление в `Microsoft.Xbox.Services.Client` пространство имен меток вашей socialGroup списки лидеров, а затем использовать `LeaderboardHelper` класса функция `GetSocialGroupFromLeaderboardType(LeaderboardTypes leaderboardType)` извлечения соответствующую строку.

> [!NOTE]
> Передача пустой строки для параметра socialGroup будет возвращать те же результаты, что и вызов метода `GetLeaderboard()` функции. Вы получите нефильтрованные *глобального* списка лидеров, показывающий все в ранжирующей в таблице лидеров, играют в игры.

```csharp
using Microsoft.Xbox.Services.Leaderboard;
using Microsoft.Xbox.Services.Statistics.Manager;
using Microsoft.Xbox.Services;

public void LoadLeaderboard()
{

    if (this.stat == null)
    {
        // TO DO: Display "Stat not specified" error message!
        return;
    }

    if (this.xboxLiveUser == null)
    {
        if (SignInManager.Instance.GetCurrentNumberOfPlayers() > 0)
        {
            this.xboxLiveUser = SignInManager.Instance.GetPlayer(1);
            this.isLocalUserAdded = true;
        }
        else
        {
            // TO DO: Display "No user signed-in" error message!
            return;
        }
    }

    LeaderboardQuery query = new LeaderboardQuery
    {
        MaxItems = 5,
    };

    XboxLive.Instance.StatsManager.GetLeaderboard(this.xboxLiveUser, this.stat.ID, otherQuery);
}

```

Теперь вы могли заметить, что наши два списка лидеров извлечение функции возвращают значение void и таким образом, не возвращают данные списка лидеров, которые мы ищем. Мы фактически будет получать данные списка лидеров в функцию события, описанные в следующем разделе.

## <a name="receive-the-leaderboard-data"></a>Получение данных "список лидеров"

Для получения данных списка лидеров, будет необходимо добавить функцию прослушивания `StatsManagerComponent` экземпляр названия. Следует добавить следующую строку кода, чтобы `Awake()` функция кода: `StatsManagerComponent.Instance.GetLeaderboardCompleted += this.MyGetLeaderboardCompletedFunction`. `StatsManagerComponent` В `Microsoft.Xbox.Services.Client` пространства имен прослушивает события завершения "список лидеров". Запустив эту строку кода, вы добавите функции в список функций, которые должны вызываться при возникновении события завершения "список лидеров". В этом примере, эта функция вызывается MyGetLeaderBoardCompletedFunction можно назвать функцию в собственный сценарий. Функция «MyGetLeaderboardCompletedFunction» нужно будет принимать два параметра, объект, представляющий отправителя и `Microsoft.Xbox.Services.Client.StatEventArgs` параметра. Оболочка этой функции может выглядеть следующим образом:

```csharp
using Microsoft.Xbox.Services.Client;
using Microsoft.Xbox.Services.Statistics.Manager;

private void GetLeaderboardCompleted(object sender, StatEventArgs statArgs)
    {
        //Do Something;
    }
```

Первое, что следует сделать эта функция является проверка на наличие ошибок, которые можно найти в `StatEventArgs` statArgs параметра. Содержит StatArgs `StatisticEvent` EventData, которая содержит данные об ошибках. Если произошла ошибка при получении данных списка лидеров можно найти его в `statArgs.EventData.ErrorCode` или `statArgs.EventData.ErrorMessage`. Если ошибок не было ErrorCode будет равно 0, и сообщение об ошибке будет отображаться пустая строка «». Вы можете добавить простой if инструкции в приведенный выше код на наличие ошибок.

```csharp
using Microsoft.Xbox.Services.Client;
using Microsoft.Xbox.Services.Statistics.Manager;

private void GetLeaderboardCompleted(object sender, StatEventArgs statArgs)
    {
        if (statArgs.EventData.ErrorCode != 0) //if there is an error
        {
            // TO DO: Display error message
            return;
        }
    }
```

После подтверждения, что отсутствуют ошибки, сохранение результатов запроса списка лидеров, которые можно найти в `statArgs.EventData.EventArgs.Result`. `Result` является `LeaderBoardResult` объект, содержащий данные, необходимые для заполнения вашего списка лидеров. В нашем примере мы будет извлекать эти данные и отправлять их в другой функция, вызываемая `LoadResult()`.

```csharp
using Microsoft.Xbox.Services.Client;
using Microsoft.Xbox.Services.Statistics.Manager;

private void GetLeaderboardCompleted(object sender, StatEventArgs statArgs)
    {
        if (statArgs.EventData.ErrorCode != 0)
        {
            // TO DO: Display error message
            return;
        }

        LeaderboardResultEventArgs leaderboardArgs = (LeaderboardResultEventArgs)statArgs.EventData.EventArgs;
        this.LoadResult(leaderboardArgs.Result);
    }
```

`LeaderboardResult` Результат, мы отправляем `LoadResult()` функция будет иметь все данные, нам нужно считывать данные списка лидеров, который был возвращен как выполнять дополнительные вызовы для получения ранги, еще не полученное в результате исходного вызова. Требуется хранить результаты в переменной класса для списка лидеров сценарий следующим образом:

```csharp
using Microsoft.Xbox.Services.Leaderboard;

void LoadResult(LeaderboardResult result)
    {
        this.leaderboardData = result;
    }
```

Это важно, так как `LeaderboardResult` содержит `HasNext` свойство, которое определяет, есть ли более поздней версии раздел списка лидеров, можно получить, результат также содержит общее число строк, составляющих "список лидеров". Эти свойства будут важны для перехода вашего списка лидеров. Для извлечения данных из вашего `LeaderBoardResult` просто реализовать для цикл с помощью `LeaderboardResults` список `LeaderboardRow` вызывается `Rows`. В нашем примере кода мы просто хотим объединить значения в каждом `LeaderboardRow` в строку для отображения.


```csharp
using Microsoft.Xbox.Services.Leaderboard

void LoadResult(LeaderboardResult result)
{

    this.leaderboardData = result;

    this.leaderBoardText.text = "" // leaderBoardText is a UnityEngine.UI text box.

    foreach (LeaderboardRow row in this.leaderboardData.Rows)
    {
        leaderBoardText.text += string.Format("Rank: {0} | Gamertag: {1} | {2}:{3}\n\n", row.Rank, row.Gamertag, this.stat.DisplayName, row.Values[0]);
    }
}
```

В нашем примере мы использовали ранга игрока и значения свойства LeaderBoardResult для заполнения наших строк, а также параметр DisplayName stat, связанный с "список лидеров".

Я уверен, что вы сможете сделать нечто более серьезных изменений с помощью всех этих данных "список лидеров".

## <a name="navigating-the-leaderboard-data"></a>Перемещение по данным "список лидеров"

В наиболее распространенных экземпляров не будет загружать каждый один ранг в ваш список лидеров за один раз и необходимо иметь возможность перемещения ранги для отображения разные разделы списка лидеров для пользователя. Давайте предположим, что список лидеров со 100 первых ранжированных игроков. В первом вызове `GetLeaderboard()` или `GetSocialLeaderboard` получения 10 `LeaderboardRows` и отобразить их для проигрывателя. Проигрыватель может потребоваться узнать больше, чем ведущих десяти игроков. Самый простой способ получения следующего набора десять пользователей, — для определения того, является ли `LeaderboardResult` хранится от последнего запрос содержит несколько строк для извлечения и затем вызвав `GetLeaderboard()` с помощью следующего запроса, LeaderboardResult. Чтобы использовать LeaderBoardResult *nextQuery* необходимо использовать функцию `LeaderBoardResult.GetNextQuery()`. Код для получения следующего набора ранги будет выглядеть следующим образом.

```csharp
using Microsoft.Xbox.Services;
using Microsoft.Xbox.Services.Leaderboard;

void GetNextLeaders()
    {
        if(this.leaderboardData.HasNext)
        {
            LeaderboardQuery query = this.leaderboardData.GetNextQuery();
            XboxLive.Instance.StatsManager.GetLeaderboard(this.xboxLiveUser, this.stat.ID, query);
        }
        else
        {
            //TO DO: Display an error message or go back to the beginning of the leaderboard as the situation demands.
        }
    }
```

Возврат к предыдущим вашего списка лидеров немного сложнее, так как нет функции для извлечения предыдущих X число ранги из вашего списка лидеров. Чтобы получить предыдущие рейтинги, будет необходимо написать свою собственную логику. Один метод было бы хранить ваши `MaxItems` на `LeaderboardQuery` и вычисления ранга, какие необходимо пропустить с помощью `SkipToRank` атрибут вашей `LeaderboardQuery`. Этот код может выглядеть примерно следующим образом:

```csharp
using Microsoft.Xbox.Services;
using Microsoft.Xbox.Services.Leaderboard;

void GetPreviousLeader()
{
    if(leaderboardData == null || leaderboardData.Rows.Count < 1)
    {
        return;
    }

    uint topRank = leaderboardData.Rows[0].Rank; //get the first rank of the leaderboard.
    uint targetRank = topRank - this.maxItems > 0 ? topRank - this.maxItems : 0; //set your targetRank equal to the current top rank of your leaderboard minus the configured number of rows to display at a time.

    LeaderboardQuery query = new LeaderboardQuery // make a new query with the calculated targetRank
    {
        SkipResultToRank = targetRank,
        MaxItems = this.maxItems
    };

    XboxLive.Instance.StatsManager.GetLeaderboard(this.xboxLiveUser, this.stat.ID, query); // call the GetLeaderboard() function with the new query
}
```

Окончательный наиболее распространенным сценарием является проигрыватель может просто для просмотра их место в таблице лидеров. Это легко достигается путем вызова `GetLeaderboard()` функции с помощью запроса где `SkipResultToMe` атрибута задано значение true.

```csharp
using Microsoft.Xbox.Services;
using Microsoft.Xbox.Services.Leaderboard;

    void GetRankForTag()
    {

        LeaderboardQuery query = new LeaderboardQuery // make a new query with the calculated targetRank
        {
            SkipResultToMe = true,
            MaxItems = this.maxItems
        };

        XboxLive.Instance.StatsManager.GetLeaderboard(this.xboxLiveUser, this.stat.ID, query); // call the GetLeaderboard() function with the new query
    }
```

Если вы хотите подробно более подробный пример списка лидеров всегда смогут прочитать Leaderboard.cs сценария в папке XboxLive подключаемый модуль средств >> XboxLive >> Сценарии >> Leaderboard.cs.