---
author: mcleanbyron
ms.assetid: c5246681-82c7-44df-87e1-a84a926e6496
description: "Используйте этот метод в API рекламных акций Магазина Windows для управления рекламой в рекламных кампаниях."
title: "Управление рекламными материалами для рекламных кампаний"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, API рекламных акций Магазина Windows, uwp, рекламные кампании"
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 1e0755134a47b6acfb48f735ea56c4aa3c46be14
ms.lasthandoff: 02/08/2017

---

# <a name="manage-creatives-for-ad-campaigns"></a>Управление рекламными материалами для рекламных кампаний

Используйте эти методы в API рекламных акций Магазина Windows для загрузки собственной рекламы, чтобы использовать ее в рекламных кампаниях, или для получения существующих реклам. Реклама может быть связана с одной или несколькими линиями поставки даже в разных рекламных кампаниях при условии, что она всегда представляет одно и то же приложение.

Дополнительные сведения о связи между рекламными материалами, кампаниями, линиями поставки и профилями таргетинга см. в разделе [Проведение рекламных кампаний с помощью служб Магазина Windows](run-ad-campaigns-using-windows-store-services.md#call-the-windows-store-promotions-api).

## <a name="prerequisites"></a>Предварительные условия

Для использования этих методов сначала необходимо сделать следующее.

* Если вы еще не сделали этого, выполните все [необходимые условия](run-ad-campaigns-using-windows-store-services.md#prerequisites) для API рекламных акций Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этих методов. После получения маркера доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия маркера можно получить новый маркер.

## <a name="request"></a>Запрос

Эти методы имеют следующие URI.

| Тип метода | Универсальный код ресурса (URI) запроса     |  Описание  |
|--------|-----------------------------|---------------|
| POST   | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/creative``` |  Создает новую рекламу.  |
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/promotion/creative/{creativeId}``` |  Получает рекламу, заданную *creativeId*.  |

>**Примечание**.&nbsp;&nbsp;Этот API в настоящее время не поддерживает метод PUT.

<span/> 
### <a name="header"></a>Заголовок

| Заголовок        | Тип   | Описание         |
|---------------|--------|---------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |
| Tracking ID   | Код GUID   | Необязательный параметр. Идентификатор, который отслеживает поток вызовов.                                  |


<span/>
### <a name="request-body"></a>Текст запроса

Метод POST требует тело запроса JSON с обязательными полями объекта [Creative](#creative).

<span/>
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

<span/>
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

| Поле        | Тип   |  Описание      |  Только чтение  | По умолчанию  |  Обязательный для POST |  
|--------------|--------|---------------|------|-------------|------------|
|  id   |  целое число   |  Идентификатор рекламного материала.     |   Да    |      |    Нет   |       
|  name   |  строка   |   Имя рекламного материала.    |    Нет   |      |  Да     |       
|  content   |  строка   |  Содержимое рекламного изображения в формате Base64.     |  Нет     |      |   Да    |       
|  height   |  целое число   |   Высота рекламного материала.    |    Нет    |      |   Да    |       
|  width   |  целое число   |  Ширина рекламного материала.     |  Нет    |     |    Да   |       
|  landingUrl   |  строка   |  URL-адрес, куда направляет рекламный материал (это значение должно быть действительным URI).     |  Нет    |     |   Да    |       
|  format   |  строка   |   Формат рекламы. На данный момент единственным поддерживаемым значением является **Banner**.    |   Нет    |  Banner   |  Нет     |       
|  imageAttributes   | [ImageAttributes](#image-attributes)    |   Предоставляет атрибуты для рекламного материала.     |   Нет    |      |   Да    |       
|  storeProductId   |  строка   |   [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для приложения, с которым связана эта рекламная кампания. Пример кода продукта в Магазине — 9nblggh42cfd.    |   Нет    |    |  Нет     |   |  

<span id="image-attributes"/>
## <a name="imageattributes-object"></a>Объект ImageAttributes

| Поле        | Тип   |  Описание      |  Только чтение  | Значение по умолчанию  | Обязательный для POST |  
|--------------|--------|---------------|------|-------------|------------|
|  imageExtension   |   строка  |   Расширение изображения, например, PNG или JPG.    |    Нет   |      |   Да    |       |


## <a name="related-topics"></a>Статьи по теме

* [Проведение рекламных кампаний с помощью служб Магазина Windows](run-ad-campaigns-using-windows-store-services.md)
* [Управление рекламными кампаниями](manage-ad-campaigns.md)
* [Управление линиями поставки для рекламных кампаний](manage-delivery-lines-for-ad-campaigns.md)
* [Управление профилями таргетинга рекламных кампаний](manage-targeting-profiles-for-ad-campaigns.md)
* [Получение данных об эффективности рекламной кампании](get-ad-campaign-performance-data.md)

