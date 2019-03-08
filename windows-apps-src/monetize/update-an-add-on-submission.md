---
ms.assetid: 8C63D33B-557D-436E-9DDA-11F7A5BFA2D7
description: Используйте этот метод в API отправки Microsoft Store для обновления существующей отправки надстройки.
title: Обновление отправки надстройки
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, отправка надстройки, обновление, продукт внутри приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: fd0bb8df9b9fc36216da72e4ad01ebd2e650ad1a
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57620619"
---
# <a name="update-an-add-on-submission"></a>Обновление отправки надстройки


Используйте этот метод в API отправки Microsoft Store, чтобы обновить данные для существующей отправки надстройки (также известной как продукт внутри приложения или IAP). После успешного обновления отправки с помощью этого метода необходимо [зафиксировать отправку](commit-an-add-on-submission.md) для проверки и публикации.

Дополнительные сведения об использовании этого метода в процессе создания отправки надстройки с помощью API отправки Microsoft Store см. в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

## <a name="prerequisites"></a>Предварительные условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.
* Создайте надстройку адресация, одно из приложений. Это можно сделать в центре партнеров, или это можно сделать с помощью [создать надстройку отправки](create-an-add-on-submission.md) метод.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| PUT    | ```https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}/submissions/{submissionId} ``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| inAppProductId | Строка | Обязательный. Код продукта в Магазине для надстройки, отправку которой необходимо обновить. Идентификатор Store доступен в центре партнеров, и она включена в данные ответа для запросов к [создать надстройку](create-an-add-on.md) или [получение сведений о надстройка](get-all-add-ons.md).  |
| submissionId | Строка | Обязательный. Идентификатор отправки для обновления. Этот идентификатор добавляется в данные ответов для запросов на [создание отправки надстройки](create-an-add-on-submission.md). Для отправки, который был создан в центре партнеров этот идентификатор также доступна в URL-АДРЕСЕ для отправки страницы в центре партнеров.  |


### <a name="request-body"></a>Тело запроса

Текст запроса содержит следующие параметры.

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| contentType           | Строка  |  [Тип содержимого](../publish/enter-add-on-properties.md#content-type), которое предоставляется в надстройке. Может принимать одно из следующих значений. <ul><li>NotSet (Не задано)</li><li>BookDownload (Загрузка книги)</li><li>EMagazine (Электронный журнал)</li><li>ENewspaper (Электронная газета)</li><li>MusicDownload (Загрузка музыки)</li><li>MusicStream (Потоковая передача музыки)</li><li>OnlineDataStorage (Сетевое хранилище данных)</li><li>VideoDownload (Загрузка видео)</li><li>VideoStream (Потоковая передача видео)</li><li>Asp</li><li>OnlineDownload (Загрузка по Интернету)</li></ul> |  
| keywords           | Массив  | Массив строк, содержащих до 10 [ключевых слов](../publish/enter-add-on-properties.md#keywords) для надстройки. Приложение может запрашивать надстройки с помощью этих ключевых слов.   |
| lifetime           | Строка  |  Время существования надстройки. Может принимать одно из следующих значений. <ul><li>Forever (Навсегда)</li><li>OneDay (Один день)</li><li>ThreeDays (3 дня)</li><li>FiveDays (5 дней)</li><li>OneWeek (Одна неделя)</li><li>TwoWeeks (Две недели)</li><li>OneMonth (Один месяц)</li><li>TwoMonths (Два месяца)</li><li>ThreeMonths (Три месяца)</li><li>SixMonths (Шесть месяцев)</li><li>OneYear (Один год)</li></ul> |
| listings           | Объект  | Объект, содержащий сведения об описании надстройки. Дополнительные сведения см. в разделе [Ресурс описания](manage-add-on-submissions.md#listing-object).  |
| pricing           | Объект  | Объект, содержащий сведения о цене надстройки. Дополнительные сведения см. в разделе [Ресурс цены](manage-add-on-submissions.md#pricing-object).  |
| targetPublishMode           | Строка  | Режим публикации для отправки. Может принимать одно из следующих значений. <ul><li>Immediate (Незамедлительно)</li><li>Manual (Вручную)</li><li>SpecificDate (Указанная дата)</li></ul> |
| targetPublishDate           | Строка  | Дата публикации отправки в формате ISO 8601, если для *targetPublishMode* задано значение SpecificDate.  |
| tag           | Строка  |  [Настраиваемые данные разработчика](../publish/enter-add-on-properties.md#custom-developer-data) для надстройки (ранее эта информация называлась *tag*).   |
| visibility  | Строка  |  Видимость надстройки. Может принимать одно из следующих значений. <ul><li>Hidden (Скрыто)</li><li>Public (Общее)</li><li>Private (Частное)</li><li>NotSet (Не задано)</li></ul>  |


### <a name="request-example"></a>Пример запроса

В следующем примере показано обновление отправки надстройки.

```json
PUT https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/9NBLGGH4TNMP/submissions/1152921504621230023 HTTP/1.1
Authorization: Bearer <your access token>
Content-Type: application/json
{
  "contentType": "EMagazine",
  "keywords": [
    "books"
  ],
  "lifetime": "FiveDays",
  "listings": {
    "en": {
      "description": "English add-on description",
      "icon": {
        "fileName": "add-on-en-us-listing2.png",
        "fileStatus": "Uploaded"
      },
      "title": "Add-on Title (English)"
    },
    "ru": {
      "description": "Russian add-on description",
      "icon": {
        "fileName": "add-on-ru-listing.png",
        "fileStatus": "Uploaded"
      },
      "title": "Add-on Title (Russian)"
    }
  },
  "pricing": {
    "marketSpecificPricings": {
      "RU": "Tier3",
      "US": "Tier4",
    },
    "sales": [],
    "priceId": "Free"
  },
  "targetPublishDate": "2016-03-15T05:10:58.047Z",
  "targetPublishMode": "Immediate",
  "tag": "SampleTag",
  "visibility": "Public",
}
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. В теле ответа содержатся сведения об обновленной отправке. Дополнительные сведения о значениях в тексте ответа см. в разделе [Ресурс отправки надстройки](manage-add-on-submissions.md#add-on-submission-object).

```json
{
  "id": "1152921504621243680",
  "contentType": "EMagazine",
  "keywords": [
    "books"
  ],
  "lifetime": "FiveDays",
  "listings": {
    "en": {
      "description": "English add-on description",
      "icon": {
        "fileName": "add-on-en-us-listing2.png",
        "fileStatus": "Uploaded"
      },
      "title": "Add-on Title (English)"
    },
    "ru": {
      "description": "Russian add-on description",
      "icon": {
        "fileName": "add-on-ru-listing.png",
        "fileStatus": "Uploaded"
      },
      "title": "Add-on Title (Russian)"
    }
  },
  "pricing": {
    "marketSpecificPricings": {
      "RU": "Tier3",
      "US": "Tier4",
    },
    "sales": [],
    "priceId": "Free"
  },
  "targetPublishDate": "2016-03-15T05:10:58.047Z",
  "targetPublishMode": "Immediate",
  "tag": "SampleTag",
  "visibility": "Public",
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [
      {
        "code": "None",
        "details": "string"
      }
    ],
    "warnings": [
      {
        "code": "ListingOptOutWarning",
        "details": "You have removed listing language(s): []"
      }
    ],
    "certificationReports": [
      {
      }
    ]
  },
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/26920f66-b592-4439-9a9d-fb0f014902ec?sv=2014-02-14&sr=b&sig=usAN0kNFNnYE2tGQBI%2BARQWejX1Guiz7hdFtRhyK%2Bog%3D&se=2016-06-17T20:45:51Z&sp=rwl",
  "friendlyName": "Submission 2"
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 400  | Не удалось обновить отправку. Недопустимый запрос. |
| 409  | Не удалось обновить отправки из-за текущее состояние надстройки или надстройки использует возможности центра партнеров, [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   


## <a name="related-topics"></a>Статьи по теме

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправкой надстройки](manage-add-on-submissions.md)
* [Получить отправки надстройки](get-an-add-on-submission.md)
* [Создайте надстройку отправки](create-an-add-on-submission.md)
* [Зафиксировать отправки надстройки](commit-an-add-on-submission.md)
* [Удалить надстройку отправки](delete-an-add-on-submission.md)
* [Получение сведений о состоянии отправки надстройки](get-status-for-an-add-on-submission.md)
