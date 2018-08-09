---
author: mcleanbyron
Description: Learn about several ways you can programmatically enable customers to rate and review your app.
title: Запрос оценок и отзывов для вашего приложения
ms.author: mcleans
ms.date: 03/22/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, оценки, отзывы
ms.localizationpriority: medium
ms.openlocfilehash: 8f897eeaee835c1c1bcd96e9bd843ed8f6a85612
ms.sourcegitcommit: 6618517dc0a4e4100af06e6d27fac133d317e545
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2018
ms.locfileid: "1690380"
---
# <a name="request-ratings-and-reviews-for-your-app"></a>Запрос оценок и отзывов для вашего приложения

Можно добавить код в приложение универсальной платформы Windows (UWP), чтобы программным образом запрашивать у пользователей оценку или отзыв на приложение. Это можно сделать несколькими способами:
* Можно отобразить диалоговое окно оценки и отзыва непосредственно в контексте приложения.
* Можно программным способом открыть страницу оценки и отзывов для вашего приложения в Microsoft Store.

Когда вы будете готовы проанализировать данные оценок и отзывов, можете просмотреть эти данные на информационной панели центра разработки для Windows или использовать API аналитики Microsoft Store для программного получения этих данных.

## <a name="show-a-rating-and-review-dialog-in-your-app"></a>Показ диалогового окна оценки и отзыва в приложении

Чтобы программным образом показать пользователю диалоговое окно, где он может оставить оценку и отзыв, вызовите метод [SendRequestAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storerequesthelper.sendrequestasync) в пространстве имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store). Передайте целое число 16 в параметр *requestKind* и пустую строку в параметр *parametersAsJson*, как показано в следующем примере кода. В этом примере требуется библиотека [Json.NET](http://www.newtonsoft.com/json) от Newtonsoft и использование инструкций для пространств имен **Windows.Services.Store**, **System.Threading.Tasks** и **Newtonsoft.Json.Linq**.

> [!IMPORTANT]
> Запрос на показ диалогового окна оценку и отзыва должен вызываться в потоке пользовательского интерфейса в приложении.

```csharp
public async Task<bool> ShowRatingReviewDialog()
{
    StoreSendRequestResult result = await StoreRequestHelper.SendRequestAsync(
        StoreContext.GetDefault(), 16, String.Empty);

    if (result.ExtendedError == null)
    {
        JObject jsonObject = JObject.Parse(result.Response);
        if (jsonObject.SelectToken("status").ToString() == "success")
        {
            // The customer rated or reviewed the app.
            return true;
        }
    }

    // There was an error with the request, or the customer chose not to
    // rate or review the app.
    return false;
}
```

Метод **SendRequestAsync** использует систему простых запросов на основе целых чисел и данные параметров на основе JSON для доступа к различным операциям Store для приложений. Когда вы передаете целое число 16 параметру *requestKind*, вы создаете запрос на показ диалогового окна оценки и отзыва и отправляете соответствующие данные в Store. Этот метод впервые появился в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio. Общие сведения об этом методе см. в разделе [Отправка запросов в Store](send-requests-to-the-store.md).

### <a name="response-data-for-the-rating-and-review-request"></a>Данные ответов для запросов на оценку и отзыв

После отправки этого запроса для отображения диалогового окна оценки и отзыва свойство [Response](https://docs.microsoft.com/uwp/api/windows.services.store.storesendrequestresult.Response) возвращаемого значения [StoreSendRequestResult](https://docs.microsoft.com/uwp/api/windows.services.store.storesendrequestresult) содержит строку формата JSON, которая указывает, успешно ли выполнен запрос.

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

|  Поле  |  Описание  |
|----------------------|---------------|
|  *status*                   |  Строка, указывающая, оставил ли пользователь оценку или отзыв. Поддерживаются значения **success** и **aborted**.   |
|  *data*                   |  Объект, содержащий одно логическое значение с именем *updated*. Это значение указывает, обновил ли пользователь существующую оценку или отзыв. Объект *data* включается только в ответы при успешном завершении.   |
|  *errorDetails*                   |  Строка, содержащая информацию об ошибках для запроса. |

## <a name="launch-the-rating-and-review-page-for-your-app-in-the-store"></a>Запуск страницы оценки и отзыва страницы для вашего приложения в Store

Если вы хотите программным образом открыть страницу оценки и отзыва для вашего приложения в Store, можно использовать метод [LaunchUriAsync](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync) со схемой URI ```ms-windows-store://review```, как показано в этом примере кода.

```csharp
bool result = await Windows.System.Launcher.LaunchUriAsync(new Uri("ms-windows-store://review/?ProductId=9WZDNCRFHVJL"));
```

Дополнительные сведения см. в разделе [Запуск приложения Microsoft Store](../launch-resume/launch-store-app.md).

## <a name="analyze-your-ratings-and-reviews-data"></a>Анализ данных оценок и отзывов

Для анализа данных оценок и отзывов пользователей есть несколько вариантов:
* Можно использовать отчет [Отзывы](../publish/reviews-report.md) на информационной панели центра разработки для Windows для просмотра оценок и отзывов пользователей. Также можно скачать этот отчет для просмотра в автономном режиме.
* Можно использовать методы [Получения оценок приложения](get-app-ratings.md) и [Получения отзывов на приложение](get-app-reviews.md) в API аналитики Microsoft Store, чтобы программным образом получить оценки и отзывы пользователей в формате JSON.

## <a name="related-topics"></a>Статьи по теме

* [Отправка запросов в Store](send-requests-to-the-store.md)
* [Запуск приложения Microsoft Store](../launch-resume/launch-store-app.md)
* [Отчет "Отзывы"](../publish/reviews-report.md)