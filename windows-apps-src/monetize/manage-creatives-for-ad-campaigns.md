---
ms.assetid: c5246681-82c7-44df-87e1-a84a926e6496
description: Используйте этот метод в API рекламных акций Microsoft Store для управления рекламными материалами в кампаниях.
title: Управление рекламными материалами
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API рекламных акций Microsoft Store, рекламные кампании
ms.localizationpriority: medium
ms.openlocfilehash: 3411ee4c947d809009c2185389f5513a49afce98
ms.sourcegitcommit: d1c3e13de3da3f7dce878b3735ee53765d0df240
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/24/2019
ms.locfileid: "66215035"
---
# <a name="manage-creatives"></a>Управление рекламными материалами

Используйте эти методы в API рекламных акций Microsoft Store, чтобы выкладывать собственную рекламу и использовать ее в кампаниях, либо получать существующие рекламные материалы. Рекламный элемент может быть связан с одним или несколькими каналами доставки (даже в разных рекламных кампаниях) при условии, что элемент всегда представляет одно и тоже приложение.

Дополнительные сведения о связи между рекламными материалами, кампаниями, строками поставки и целевыми профилями см. в разделе [Проведение рекламных кампаний с помощью служб Microsoft Store](run-ad-campaigns-using-windows-store-services.md#call-the-windows-store-promotions-api).

> [!NOTE]
> Если вы используете этот API для добавления собственных рекламных материалов, их размер не должен превышать 40 КБ. При отправке файла большего размера API не станет возвращать ошибку, однако кампании создана не будет.

## <a name="prerequisites"></a>Предварительные требования

Для использования этих методов сначала необходимо сделать следующее.

* Если вы еще не сделали этого, выполните все [необходимые условия](run-ad-campaigns-using-windows-store-services.md#prerequisites) для API рекламных акций Microsoft Store.
* [Получите маркер доступа Azure AD](run-ad-campaigns-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этих методов. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.


## <a name="request"></a>Запрос

Эти методы имеют следующие URI.

| Тип метода | Универсальный код ресурса (URI) запроса     |  Описание  |
|--------|-----------------------------|---------------|
| ПОМЕСТИТЬ   | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/creative``` |  Создает новую рекламу.  |
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/creative/{creativeId}``` |  Получает рекламу, заданную *creativeId*.  |

> [!NOTE]
> Этот API в настоящее время не поддерживает метод PUT.


### <a name="header"></a>Header

| Header        | Тип   | Описание         |
|---------------|--------|---------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |
| Tracking ID   | Код GUID   | Необязательно. Идентификатор, который отслеживает поток вызовов.                                  |


### <a name="request-body"></a>Тело запроса

Метод POST требует тело запроса JSON с обязательными полями объекта [Creative](#creative).


### <a name="request-examples"></a>Примеры запросов

Следующий пример демонстрирует вызов метода POST для создания рекламного материала. В этом примере значение *content* сокращено для краткости.

```json
POST https://manage.devcenter.microsoft.com/v1.0/my/promotion/creative HTTP/1.1
Authorization: Bearer <your access token>

{
  "name": "Contoso App Campaign - Creative 1",
  "content": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAAQABAAD/2wBDAAgGB...other base64 data shortened for brevity...",
  "height": 80,
  "width": 480,
  "imageAttributes":
  {
    "imageExtension": "PNG"
  }
}
```

Следующий пример демонстрирует вызов метода GET для получения рекламного материала.

```json
GET https://manage.devcenter.microsoft.com/v1.0/my/promotion/creative/106851  HTTP/1.1
Authorization: Bearer <your access token>
```


## <a name="response"></a>Ответ

Эти методы возвращают тело ответа JSON с объектом [Creative](#creative), содержащее сведения о созданном или полученном рекламном материале. В следующем примере показано тело ответа для этих методов. В этом примере значение *content* сокращено для краткости.

```json
{
    "Data": {
        "id": 106126,
        "name": "Contoso App Campaign - Creative 2",
        "content": "data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEAAQABAAD/2wBDAAgGB...other base64 data shortened for brevity...",
        "height": 50,
        "width": 300,
        "format": "Banner",
        "imageAttributes":
        {
          "imageExtension": "PNG"
        },
        "storeProductId": "9nblggh42cfd"
    }
}
```


<span id="creative"/>

## <a name="creative-object"></a>Объект Creative

Для этих методов тела запроса и ответа содержат следующие поля. В этой таблице показаны поля, которые доступны только для чтения (это означает, что они не могут изменяться в методе PUT), и поля, которые необходимы в теле запроса для метода POST.

| Поле        | Тип   |  Описание      |  Только чтение  | Значение по умолчанию  |  Обязательный для POST |  
|--------------|--------|---------------|------|-------------|------------|
|  id   |  целое число   |  Идентификатор рекламного материала.     |   Да    |      |    Нет   |       
|  name   |  строка   |   Имя рекламного материала.    |    Нет   |      |  Да     |       
|  content   |  строка   |  Содержимое рекламного изображения в формате Base64.<br/><br/>**Примечание.** &nbsp;&nbsp;Максимально допустимый размер рекламных материалов — 40 КБ. При отправке файла большего размера API не станет возвращать ошибку, однако кампании создана не будет.     |  Нет     |      |   Да    |       
|  height   |  целое число   |   Высота рекламного материала.    |    Нет    |      |   Да    |       
|  width   |  целое число   |  Ширина рекламного материала.     |  Нет    |     |    Да   |       
|  landingUrl   |  строка   |  Если вы используете службы, например AppsFlyer, Kochava, настройки или Vungle отслеживания кампании для измерения установки аналитики для вашего приложения, назначить URL-адрес отслеживания в это поле при вызове метода POST (Если указано, это значение должно быть допустимым URI). Если вы не используете службы отслеживания кампании, опустите это значение при вызове метода POST (в данном случае этот URL-адрес будет создан автоматически).   |  Нет    |     |   Да    |
|  format   |  строка   |   Формат рекламы. На данный момент единственным поддерживаемым значением является **Banner**.    |   Нет    |  Баннеры   |  Нет     |       
|  imageAttributes   | [ImageAttributes](#image-attributes)    |   Предоставляет атрибуты для рекламного материала.     |   Нет    |      |   Да    |       
|  storeProductId   |  строка   |   [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для приложения, с которым связана эта рекламная кампания. Пример кода продукта в Магазине — 9nblggh42cfd.    |   Нет    |    |  Нет     |   |  


<span id="image-attributes"/>

## <a name="imageattributes-object"></a>Объект ImageAttributes

| Поле        | Тип   |  Описание      |  Только чтение  | Значение по умолчанию  | Обязательный для POST |  
|--------------|--------|---------------|------|-------------|------------|
|  imageExtension   |   строка  |   Принимает одно из следующих значений: **PNG** или **JPG**.    |    Нет   |      |   Да    |       |


## <a name="related-topics"></a>См. также

* [Запустите рекламные кампании, с помощью служб Microsoft Store](run-ad-campaigns-using-windows-store-services.md)
* [Управление рекламных кампаний](manage-ad-campaigns.md)
* [Управление доставки строк для рекламных кампаний](manage-delivery-lines-for-ad-campaigns.md)
* [Управление профилями выбора целевой платформы для рекламных кампаний](manage-targeting-profiles-for-ad-campaigns.md)
* [Получить данные о производительности ad кампании](get-ad-campaign-performance-data.md)
