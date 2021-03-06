---
ms.assetid: 87708690-079A-443D-807E-D2BF9F614DDF
description: Используйте этот метод в интерфейсе API отправки Microsoft Store для получения данных для рейса пакета для приложения, зарегистрированный для вашей учетной записи центра партнеров.
title: Получение тестового пакета
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, тестируемая возможность, тестовый пакет
ms.localizationpriority: medium
ms.openlocfilehash: 3a02a299682610cd516067acefc795df9512a268
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371765"
---
# <a name="get-a-package-flight"></a>Получение тестового пакета

Используйте этот метод в интерфейсе API отправки Microsoft Store для получения данных для рейса пакета для приложения, зарегистрированный для вашей учетной записи центра партнеров.

## <a name="prerequisites"></a>Предварительные требования

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание текста заголовка и запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| GET    | `https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}` |


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | строка | Обязательный. Код продукта для приложения, содержащего тестовый пакет, который требуется получить. Идентификатор Store для приложения, доступна в центре партнеров.  |
| flightId | строка | Обязательный. Идентификатор получаемого тестового пакета. Этот идентификатор добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md). Для рейсов, который был создан в центре партнеров этот идентификатор также доступен в URL-адрес для страницы рейсов в центре партнеров.  |


### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

В следующем примере показано, как получить информацию о тестовом пакете с идентификатором 43e448df-97c9-4a43-a0bc-2a445e736bcd для приложения с кодом продукта в Магазине 9WZDNCRD91MD.

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. Дополнительные сведения о значениях, которые могут содержаться в теле ответа, см. в следующих разделах.

```json
{
  "flightId": "43e448df-97c9-4a43-a0bc-2a445e736bcd",
  "friendlyName": "myflight",
  "lastPublishedFlightSubmission": {
    "id": "1152921504621086517",
    "resourceLocation": "flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621086517"
  },
  "pendingFlightSubmission": {
    "id": "115292150462124364",
    "resourceLocation": "flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243647"
  },
  "groupIds": [
    "0"
  ],
  "rankHigherThan": "671c2857-725e-4faf-9e9e-ea1191ef879c"
}
```

### <a name="response-body"></a>Тело ответа

| Значение      | Тип   | Описание                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| flightId            | строка  | Идентификатор для тестового пакета. Это значение предоставляется путем центра партнеров.  |
| friendlyName           | строка  | Имя тестового пакета, указанное разработчиком.   |  
| lastPublishedFlightSubmission       | Объект | Объект, который предоставляет сведения о последней опубликованной отправке для тестового пакета. Дополнительные сведения см. ниже в разделе [Объект отправки](#submission_object).  |
| pendingFlightSubmission        | Объект  |  Объект, который предоставляет сведения о текущей ожидающей отправке для тестового пакета. Дополнительные сведения см. ниже в разделе [Объект отправки](#submission_object).  |   
| groupIds           | Массив  | Массив строк, содержащий идентификаторы тестовых групп, которые связаны с тестовым пакетом. Дополнительные сведения о тестовых группах см. в разделе [Тестовые пакеты](https://docs.microsoft.com/windows/uwp/publish/package-flights).   |
| rankHigherThan           | строка  | Понятное имя тестового пакета, приоритет которого на единицу ниже приоритета текущего тестового пакета. Дополнительные сведения о задании приоритетов тестовых групп см. в разделе [Тестовые пакеты](https://docs.microsoft.com/windows/uwp/publish/package-flights).  |


<span id="submission_object" />

### <a name="submission-object"></a>Объект отправки

Значения *lastPublishedFlightSubmission* и *pendingFlightSubmission* в теле ответа содержат объекты, предоставляющие сведения о ресурсе для отправки тестового пакета. Эти объекты имеют следующие значения.

| Значение           | Тип    | Описание                                                                                                                                                                                                                          |
|-----------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| id            | строка  | Идентификатор отправки.    |
| resourceLocation   | строка  | Относительный путь, который можно добавить к базовому URI запроса `https://manage.devcenter.microsoft.com/v1.0/my/`, чтобы получить полные данные для отправки.               |


## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание     |
|--------|---------------------  |
| 400  | Недопустимый запрос. |
| 404  | Не удалось найти указанный тестовый пакет.   |   
| 409  | Приложение использует функцию центра партнеров, которая [в настоящее время не поддерживается API отправки Microsoft Store](create-and-manage-submissions-using-windows-store-services.md#not_supported). |                                                                                                 


## <a name="related-topics"></a>См. также

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Создание пакета рейса](create-a-flight.md)
* [Удаление пакета рейса](delete-a-flight.md)
