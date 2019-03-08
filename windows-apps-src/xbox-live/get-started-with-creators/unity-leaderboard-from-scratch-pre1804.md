---
title: Создать список лидеров в Unity
description: Руководство по созданию собственных списков лидеров в Unity
ms.date: 04/24/2018
ms.topic: get-started-article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, unity, списки лидеров
ms.openlocfilehash: a62cb2b3bd4afebcc7aa9060db60ac167f052977
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57657829"
---
# <a name="leaderboards-data-in-unity"></a>Данные списки лидеров в Unity

Для тех, кому нужны возможности пользовательского списка лидеров в этой статье предоставит инструменты для реализации собственного списка лидеров, перейдя по API, доступных для разработчиков Unity. Когда вы знаете, как получить данные списка лидеров можно применить его к пользовательскому интерфейсу по своему выбору.

[!IMPORTANT]
> Эта статья относится к версии подключаемого модуля до обновления, внесенного в мае 2018 года (выпуска 1804). Если вы установили Xbox Live подключаемый модуль после указанного периода, или еще не загружен возможно более новой версии, который использует различные вызовы для сбора данных "список лидеров". Помимо этого вы обнаружите, что снимки экрана в этот подключаемый модуль не совпадают с самой последней версии. Вместо этого обратитесь к [новые списки лидеров unity из временных файлов статьи](unity-leaderboard-from-scratch.md).


## <a name="call-for-leaderboard-data"></a>Вызов для данных "список лидеров"

Чтобы запросить данные списка лидеров в службе Xbox Live, необходимо вызвать метод  `void GetLeaderboard(XboxLiveUser user,  LeaderboardQuery query)`

Успешно вызывать этот метод, вам потребуется получить `XboxLiveUser` из [вход](unity-prefabs-and-sign-in.md), имеют [настроен stat](add-stats-and-leaderboards-in-unity.md) со значением для по крайней мере один проигрыватель и формы `LeaderboardQuery`. Статьях по ссылкам можно получить, если вы не уже знаете, как для входа в систему пользователь или нужно инициализировать статистику для вашего списка лидеров. При наличии инициализированный Статистика самый простой способ связать ее с помощью скрипта "список лидеров" не включать один из prefabs статистики: `IntegerStat`, `DoubleStat`, или `StringStat` как общедоступную переменную. Ваш stat потребуется его идентификатор свойства, настроенного по крайней мере как это мы будем использовать для **statName** параметра при вызове для наших данных "список лидеров". Наконец, вам потребуется форму `LeaderboardQuery` объекта.
Объект `LeaderboardQuery` имеет несколько атрибутов, которые могут устанавливаться которых повлияет на данные, возвращенные:

- **StatName(Required):** это идентификатор stat, ваш список лидеров будут извлекаться данные, если это значение не задано, не присвоено значение идентификатора stats, данные не возвращаются.
- **SocialGroup:** данных возвращаемого списка лидеров в зависимости от значения этой строки будут фильтроваться для друзей, избранные друзьями и если он не установлен, извлечет нефильтрованные глобального списка лидеров. Значение «» будет возвращает нефильтрованный списка, значение «all» возвратит список друзьям только в нем «избранные» приведет к получению списка друзьям только избранные присутствует.
- **SkipResultToRank:** Если задано, эта переменная целое число без знака определит ранжирующие данные списка лидеров запускают с при возврате. Ранжирование начинаются с ранг 1.
- **SkipResultToMe:** Если задать значение true, это логическое значение приведет к "список лидеров" данные, возвращаемые начинаются с `XboxLiveUser` используется в `GetLeaderboard()` вызова.
- **Порядок:** Перечисления типа `Microsoft.Xbox.Services.Leaderboard.SortOrder` имеют два возможных значения: по возрастанию или по убыванию. Переменной запроса определит порядок сортировки вашего списка лидеров. По умолчанию списки лидеров возвращают данные в порядке убывания.
- **MaxItems:** Это целое число без знака определяет максимальное число строк, возвращаемых для одного вызова `GetLeaderboard()`.

Формирование вашей LeaderboardQuery может выглядеть следующим образом:

```csharp
using Microsoft.Xbox.Services.Leaderboard;

LeaderboardQuery query = new LeaderboardQuery
        {
            StatName = stat.ID,
            SkipResultToRank = 100,
            MaxItems = 5
        };
```

С помощью этого запроса возвращает отсортированные пять строк, начиная с разбиении списка лидеров отдельных для заданного stat.

> [!WARNING]
> Значение выше, чем число исполнителей, содержащихся в таблице лидеров SkipResultToRank приведет к созданию списков лидеров в данные, возвращаемые с нулем строк.

Теперь, когда у нас есть все элементы друг с другом, мы можем вызвать `GetLeaderboard(XboxLiveUser user,  LeaderboardQuery query)` функции, где мы будем использовать наши со знаком в `XboxLiveUser` (скорее всего, `XboxLiveUserInfo.User`) получен из входа в систему как наш пользователь и `LeaderboardQuery` мы только что созданную нашего запроса.

Ваш список лидеров, вызов функции может выглядеть следующим образом:

```csharp
using Microsoft.Xbox.Services.Leaderboard;

public void LoadLeaderboard()
    {
        // check to make sure you have a valid stat
        if (this.stat == null)
        {
            // TO DO: Display "Stat not specified" error message!
            return;
        }

        // check to make sure you have an XboxLiveUser
        if (this.xboxLiveUser == null)
        {
            if (XboxLiveUserManager.Instance.UserForSingleUserMode != null
                && XboxLiveUserManager.Instance.UserForSingleUserMode.User != null)
            {
                // set the XboxLiveUser if one is available
                this.xboxLiveUser = XboxLiveUserManager.Instance.UserForSingleUserMode.User;
            }
            else // if you don't have a user signed in display an error message and exit. 
            {
                // TO DO: Display "User Not logged In" error message
                return;
            }
        }

        LeaderboardQuery query;

        query = new LeaderboardQuery
        {
            StatName = this.stat.ID,
            MaxItems = this.maxItems,

        };

        XboxLive.Instance.StatsManager.GetLeaderboard(this.xboxLiveUser, query);
    }
```

Теперь можно заметить, наш список лидеров, извлечение функция `GetLeaderboard()` возвращает значение void и таким образом, не возвращающего данные списка лидеров, мы ищем. Мы фактически будет получать данные списка лидеров в функцию события, описанные в следующем разделе.

## <a name="receive-the-leaderboard-data"></a>Получение данных "список лидеров"

Для получения данных списка лидеров, будет необходимо добавить функцию прослушивания `StatsManagerComponent` экземпляр названия. Следует добавить следующую строку кода, чтобы `Awake()` функция кода: `StatsManagerComponent.Instance.GetLeaderboardCompleted += this.MyGetLeaderboardCompletedFunction`.

```csharp
private void Awake()
    {
        this.EnsureEventSystem();
        XboxLiveServicesSettings.EnsureXboxLiveServicesSettings();

        StatsManagerComponent.Instance.GetLeaderboardCompleted += this.GetLeaderboardCompleted;
    }
```

`StatsManagerComponent` Прослушивает события завершения "список лидеров". Запустив эту строку кода, вы добавите функции в список функций, которые должны вызываться при возникновении события завершения "список лидеров". В этом примере, эта функция вызывается `MyGetLeaderBoardCompletedFunction()`, можно присвоить имя функции в собственный сценарий. Функция «MyGetLeaderboardCompletedFunction» нужно будет принимать два параметра, объект, представляющий отправителя и `Microsoft.Xbox.Services.Client.StatEventArgs` параметра. Оболочка этой функции может выглядеть следующим образом:

```csharp
using Microsoft.Xbox.Services.Statistics.Manager;

private void MyGetLeaderBoardCompletedFunction(object sender, StatEventArgs statArgs)
    {
        //Do Something;
    }
```

Первое, что следует сделать эта функция является проверка на наличие ошибок, которые можно найти в `StatEventArgs` statArgs параметра. Содержит StatArgs `StatisticEvent` вызывается EventData, которая содержит `System.Exception` вызывается ErrorInfo. Если произошла ошибка при получении списка лидеров данных его можно найти в statArgs.EventData.ErrorInfo. Вы можете добавить простой if инструкции в приведенный выше код на наличие ошибок.

```csharp
using Microsoft.Xbox.Services.Statistics.Manager;

private void MyGetLeaderBoardCompletedFunction(object sender, StatEventArgs statArgs)
    {
        if (e.EventData.ErrorInfo != null && e.EventData.ErrorInfo.Message == "")
        {
            // TO DO: Display error message
            return;
        }
    }
```

После подтверждения, что отсутствуют ошибки, сохранение результатов запроса списка лидеров, которые можно найти в `statArgs.EventData.EventArgs.Result`. `Result` является `LeaderBoardResult` объект, содержащий данные, необходимые для заполнения вашего списка лидеров. В нашем примере кода мы будет извлекать эти данные и отправить его в другую функцию, вызывается LoadResult.

```csharp
using Microsoft.Xbox.Services.Statistics.Manager;

private void MyGetLeaderBoardCompletedFunction(object sender, StatEventArgs statArgs)
    {
        if (e.EventData.ErrorInfo != null && e.EventData.ErrorInfo.Message == "")
        {
            // TO DO: Display error message
            return;
        }

        LeaderboardResultEventArgs leaderboardArgs = (LeaderboardResultEventArgs)statArgs.EventData.EventArgs;
        this.LoadResult(leaderboardArgs.Result);
    }
```

`LeaderboardResult` Результат, мы отправляем LoadResult функция будет иметь все данные, нам нужно считывать данные списка лидеров, который был возвращен как выполнять дополнительные вызовы для получения ранги, еще не полученное в результате исходного вызова. Требуется хранить результаты в переменной класса для списка лидеров сценарий следующим образом:

```csharp
using Microsoft.Xbox.Services.Leaderboard;

void LoadResult(LeaderboardResult result)
    {
        this.leaderboardData = result;
    }
```

Это важно, так как `LeaderboardResult` содержит HasNext свойство, которое определяет, есть ли более поздней версии раздел списка лидеров, могут быть получены. `LeaderboardResult` Также хранит переменную для общее количество строк, составляющих "список лидеров". Эти свойства будут важны для перехода вашего списка лидеров. Для извлечения данных из вашего `LeaderBoardResult` просто реализовать для цикл с помощью `LeaderboardResults` список `LeaderboardRow` вызывается `Rows`. В нашем примере кода мы просто хотим объединить значения в каждом `LeaderboardRow` в строку для отображения.

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

В нашем примере мы использовали ранга игрока и значения свойства `LeaderBoardResult` для заполнения наших строк, а также параметр DisplayName stat, связанный с "список лидеров".

Я уверен, что вы сможете сделать нечто более серьезных изменений с помощью всех этих данных "список лидеров".

## <a name="navigating-the-leaderboard-data"></a>Перемещение по данным "список лидеров"

В наиболее распространенных экземпляров не будет загружать каждый один ранг в ваш список лидеров за один раз и необходимо иметь возможность перемещения ранги для отображения разные разделы списка лидеров для пользователя. Допустим, что имеется список лидеров - ста ранг игроков. В первом вызове `GetLeaderboard()` извлечении десять `LeaderboardRows` и отобразить их для проигрывателя. Проигрыватель может потребоваться узнать больше, чем ведущих десяти игроков. Для получения следующего набора десять пользователей проще всего определить ли `LeaderboardResult` хранится от последнего запроса содержит больше строк для извлечения и затем вызвав `GetLeaderboard()` с этой LeaderboardResult `NextQuery` свойство. Следующий код будет получения следующего набора данных "список лидеров".

```csharp
using Microsoft.Xbox.Services;
using Microsoft.Xbox.Services.Leaderboard;

void GetNextLeaders()
    {
        if(this.leaderboardData.HasNext)
        {
            LeaderboardQuery query = this.leaderboardData.NextQuery;
            XboxLive.Instance.StatsManager.GetLeaderboard(this.xboxLiveUser, query);
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
        StatName = stat.ID,
        SkipResultToRank = targetRank,
        MaxItems = this.maxItems
    };

    XboxLive.Instance.StatsManager.GetLeaderboard(this.xboxLiveUser, query); // call the GetLeaderboard() function with the new query
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
            StatName = stat.ID,
            SkipResultToMe = true,
            MaxItems = this.maxItems
        };

        XboxLive.Instance.StatsManager.GetLeaderboard(this.xboxLiveUser, query); // call the GetLeaderboard() function with the new query
    }
```

Если вы хотите подробно более подробный пример списка лидеров всегда смогут прочитать Leaderboard.cs сценария в папке XboxLive подключаемый модуль средств >> XboxLive >> Сценарии >> Leaderboard.cs.