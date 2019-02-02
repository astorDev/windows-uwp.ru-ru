---
Description: Learn about several ways you can programmatically enable customers to rate and review your app.
title: Запрос оценок и отзывов для вашего приложения
ms.date: 01/22/2019
ms.topic: article
keywords: Windows 10, uwp, оценки, отзывы
ms.localizationpriority: medium
ms.openlocfilehash: b167f4cc40ee72e6405436bacee28f2f20b4623c
ms.sourcegitcommit: 7a1899358cd5ce9d2f9fa1bd174a123740f98e7a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/01/2019
ms.locfileid: "9042640"
---
# <a name="request-ratings-and-reviews-for-your-app"></a>Запрос оценок и отзывов для вашего приложения

Можно добавить код в приложение универсальной платформы Windows (UWP), чтобы программным образом запрашивать у пользователей оценку или отзыв на приложение. Это можно сделать несколькими способами:
* Можно отобразить диалоговое окно оценки и отзыва непосредственно в контексте приложения.
* Можно программным способом открыть страницу оценки и отзывов для вашего приложения в Microsoft Store.

Когда вы будете готовы проанализировать оценки и отзывы данных, можно просмотреть данные в центре партнеров или использовать API аналитики для Microsoft Store для программного получения этих данных.

> [!IMPORTANT]
> При добавлении функции оценки в вашем приложении, все отзывы необходимо отправить пользователю механизмов оценку магазина, независимо от выбранного по звездам. Если от пользователей, собирать отзывы или комментарии, она должна быть очистки, что он не связан с приложения оценки или отзывы в магазине, но отправляется непосредственно в разработчик приложения. См. в разделе, разработчика поведения Дополнительные сведения, связанные с [Fraudulent или хакеры действия](https://docs.microsoft.com/legal/windows/agreements/store-developer-code-of-conduct#3-fraudulent-or-dishonest-activities).

## <a name="show-a-rating-and-review-dialog-in-your-app"></a>Показ диалогового окна оценки и отзыва в приложении

Чтобы программным образом показать пользователю диалоговое окно вашего приложения, которое просит клиентов оценить ваше приложение и отзыв, вызовите метод [RequestRateAndReviewAppAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestrateandreviewappasync) в пространстве имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) . 

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

Метод **RequestRateAndReviewAppAsync** впервые появился в Windows 10, версия 1809, и может использоваться только в проектах, предназначенных для **Windows 10 октября 2018 г. Update (10.0; Сборка 17763)** или более поздней версии в Visual Studio.

### <a name="response-data-for-the-rating-and-review-request"></a>Данные ответов для запросов на оценку и отзыв

После отправки этого запроса для отображения оценки и отзыва диалоговое окно, свойство [ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storerateandreviewresult.extendedjsondata) класса [StoreRateAndReviewResult](https://docs.microsoft.com/uwp/api/windows.services.store.storerateandreviewresult) содержит строку формата JSON, указывающий, является ли запрос выполнен успешно.

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
| *status*       | Строка, указывающая, оставил ли пользователь оценку или отзыв. Поддерживаются значения **success** и **aborted**. |
| *data*         | Объект, содержащий одно логическое значение с именем *updated*. Это значение указывает, обновил ли пользователь существующую оценку или отзыв. Объект *data* включается только в ответы при успешном завершении. |
| *errorDetails* | Строка, содержащая информацию об ошибках для запроса.                                                                                     |

## <a name="launch-the-rating-and-review-page-for-your-app-in-the-store"></a>Запуск страницы оценки и отзыва страницы для вашего приложения в Store

Если вы хотите программным образом открыть страницу оценки и отзыва для вашего приложения в Store, можно использовать метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync) со схемой URI ```ms-windows-store://review```, как показано в этом примере кода.

```csharp
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-windows-store://review/?ProductId=9WZDNCRFHVJL"));
```

Дополнительные сведения см. в разделе [Запуск приложения Microsoft Store](../launch-resume/launch-store-app.md).

## <a name="analyze-your-ratings-and-reviews-data"></a>Анализ данных оценок и отзывов

Для анализа данных оценок и отзывов пользователей есть несколько вариантов:
* Отчет [отзывы](../publish/reviews-report.md) в центре партнеров можно использовать для просмотра оценок и отзывов пользователей. Также можно скачать этот отчет для просмотра в автономном режиме.
* Можно использовать методы [Получения оценок приложения](get-app-ratings.md) и [Получения отзывов на приложение](get-app-reviews.md) в API аналитики Microsoft Store, чтобы программным образом получить оценки и отзывы пользователей в формате JSON.

## <a name="related-topics"></a>Статьи по теме

* [Отправка запросов в Store](send-requests-to-the-store.md)
* [Запуск приложения Microsoft Store](../launch-resume/launch-store-app.md)
* [Отчет "Отзывы"](../publish/reviews-report.md)
