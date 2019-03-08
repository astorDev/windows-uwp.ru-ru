---
Description: Дополнительные сведения о нескольких способах можно программно включить клиентам оценить и проверить приложение.
title: Запрос оценок и отзывов для вашего приложения
ms.date: 01/22/2019
ms.topic: article
keywords: Windows 10, uwp, оценки, отзывы
ms.localizationpriority: medium
ms.openlocfilehash: b167f4cc40ee72e6405436bacee28f2f20b4623c
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57601309"
---
# <a name="request-ratings-and-reviews-for-your-app"></a>Запрос оценок и отзывов для вашего приложения

Можно добавить код в приложение универсальной платформы Windows (UWP), чтобы программным образом запрашивать у пользователей оценку или отзыв на приложение. Это можно сделать несколькими способами:
* Можно отобразить диалоговое окно оценки и отзыва непосредственно в контексте приложения.
* Можно программным способом открыть страницу оценки и отзывов для вашего приложения в Microsoft Store.

Когда будете готовы для анализа, оценки и проверки данных, можно просмотреть данные в центре партнеров или использовать API анализа Microsoft Store для получения этих данных программными средствами.

> [!IMPORTANT]
> При добавлении функции оценки в приложении, все проверки должно отправить пользователя механизмы оценки Store, независимо от оценки выбранного типа "звезда". Если собирать отзывы и комментарии от пользователей, должно быть понятно, что оно относится к оценка приложения или проверки в Store, но отправляется непосредственно для разработчика приложений. См. правила поведения разработчиков, Дополнительные сведения, относящиеся к [Fraudulent или действий недобросовестных](https://docs.microsoft.com/legal/windows/agreements/store-developer-code-of-conduct#3-fraudulent-or-dishonest-activities).

## <a name="show-a-rating-and-review-dialog-in-your-app"></a>Показ диалогового окна оценки и отзыва в приложении

Чтобы программным способом отображать диалоговое окно из приложения, которое запрашивает у клиента оценить ваше приложение и отправить отзыв, вызовите [RequestRateAndReviewAppAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestrateandreviewappasync) метод в [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) пространства имен. 

> [!IMPORTANT]
> Запрос на показ диалогового окна оценку и отзыва должен вызываться в потоке пользовательского интерфейса в приложении.

```csharp
using Windows.ApplicationModel.Store;

private StoreContext _storeContext;

public async Task Initialize()
{
    if (App.IsMultiUserApp) // pseudo-code
    {
        IReadOnlyList<User> users = await User.FindAllAsync();
        User firstUser = users[0];
        _storeContext = StoreContext.GetForUser(firstUser);
    }
    else
    {
        _storeContext = StoreContext.GetDefault();
    }
}

private async Task PromptUserToRateApp()
{
    // Check if we’ve recently prompted user to review, we don’t want to bother user too often and only between version changes
    if (HaveWePromptedUserInPastThreeMonths())  // pseudo-code
    {
        return;
    }

    StoreRateAndReviewResult result = await 
        _storeContext.RequestRateAndReviewAppAsync();

    // Check status
    switch (result.Status)
    { 
        case StoreRateAndReviewStatus.Succeeded:
            // Was this an updated review or a new review, if Updated is false it means it was a users first time reviewing
            if (result.UpdatedExistingRatingOrReview)
            {
                // This was an updated review thank user
                ThankUserForReview(); // pseudo-code
            }
            else
            {
                // This was a new review, thank user for reviewing and give some free in app tokens
                ThankUserForReviewAndGrantTokens(); // pseudo-code
            }
            // Keep track that we prompted user and don’t do it again for a while
            SetUserHasBeenPrompted(); // pseudo-code
            break;

        case StoreRateAndReviewStatus.CanceledByUser:
            // Keep track that we prompted user and don’t prompt again for a while
            SetUserHasBeenPrompted(); // pseudo-code

            break;

        case StoreRateAndReviewStatus.NetworkError:
            // User is probably not connected, so we’ll try again, but keep track so we don’t try too often
            SetUserHasBeenPromptedButHadNetworkError(); // pseudo-code

            break;

        // Something else went wrong
        case StoreRateAndReviewStatus.OtherError:
        default:
            // Log error, passing in ExtendedJsonData however it will be empty for now
            LogError(result.ExtendedError, result.ExtendedJsonData); // pseudo-code
            break;
    }
}
```

**RequestRateAndReviewAppAsync** метод появился в Windows 10, версия 1809, и может использоваться только в проектах, предназначенных **Windows 10 октября 2018 Update (10.0; Сборка 17763)** или более поздней версии, в Visual Studio.

### <a name="response-data-for-the-rating-and-review-request"></a>Данные ответов для запросов на оценку и отзыв

После отправки запроса, чтобы отобразить оценку и просмотрите диалоговое окно, [ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storerateandreviewresult.extendedjsondata) свойство [StoreRateAndReviewResult](https://docs.microsoft.com/uwp/api/windows.services.store.storerateandreviewresult) класс содержит строку в формате JSON, указывающее, является ли запрос выполнен успешно.

В следующем примере показано возвращаемое значение для данного запроса после успешной отправки оценки или отзыва пользователем.

```json
{ 
  "status": "success", 
  "data": {
    "updated": false
  },
  "errorDetails": "Success"
}
```

В следующем примере показано возвращаемое значение для данного запроса, если пользователь не оставляет оценку или отзыв.

```json
{ 
  "status": "aborted", 
  "errorDetails": "Navigation was unsuccessful"
}
```

В таблице ниже указаны поля, которые включаются в строку формата JSON.

| Поле          | Описание                                                                                                                                   |
|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|
| *состояние*       | Строка, указывающая, оставил ли пользователь оценку или отзыв. Поддерживаются значения **success** и **aborted**. |
| *Данные*         | Объект, содержащий одно логическое значение с именем *updated*. Это значение указывает, обновил ли пользователь существующую оценку или отзыв. Объект *data* включается только в ответы при успешном завершении. |
| *ErrorDetails* | Строка, содержащая информацию об ошибках для запроса.                                                                                     |

## <a name="launch-the-rating-and-review-page-for-your-app-in-the-store"></a>Запуск страницы оценки и отзыва страницы для вашего приложения в Store

Если вы хотите программным образом открыть страницу оценки и отзыва для вашего приложения в Store, можно использовать метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync) со схемой URI ```ms-windows-store://review```, как показано в этом примере кода.

```csharp
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-windows-store://review/?ProductId=9WZDNCRFHVJL"));
```

Дополнительные сведения см. в разделе [Запуск приложения Microsoft Store](../launch-resume/launch-store-app.md).

## <a name="analyze-your-ratings-and-reviews-data"></a>Анализ данных оценок и отзывов

Для анализа данных оценок и отзывов пользователей есть несколько вариантов:
* Можно использовать [просматривает](../publish/reviews-report.md) отчетов в центре партнеров, чтобы увидеть рейтинги и отзывы от клиентов. Также можно скачать этот отчет для просмотра в автономном режиме.
* Можно использовать методы [Получения оценок приложения](get-app-ratings.md) и [Получения отзывов на приложение](get-app-reviews.md) в API аналитики Microsoft Store, чтобы программным образом получить оценки и отзывы пользователей в формате JSON.

## <a name="related-topics"></a>Статьи по теме

* [Отправлять запросы к Store](send-requests-to-the-store.md)
* [Запустите приложение Microsoft Store](../launch-resume/launch-store-app.md)
* [Отчет "Рецензии"](../publish/reviews-report.md)
