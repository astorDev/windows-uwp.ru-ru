---
ms.assetid: 4F9657E5-1AF8-45E0-9617-45AF64E144FC
description: Эти методы можно используете в интерфейсе API отправки Microsoft Store, управление надстройками для приложений, зарегистрированных для учетной записи центра партнеров.
title: Управление надстройками
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, API отправки в Microsoft Store, надстройки, продукт внутри приложения, IAP
ms.localizationpriority: medium
ms.openlocfilehash: 51c940fffde3c770f397999e566570410528a1e8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57617829"
---
# <a name="manage-add-ons"></a>Управление надстройками

Используйте указанные ниже методы API отправки в Microsoft Store для управления надстройками для ваших приложений. Введение в API отправки в Microsoft Store, включая необходимые условия для использования этого API, см. в разделе [Создание отправок и управление ими с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md).

Эти методы можно использовать только для получения, создания или удаления надстроек. Руководство по созданию отправок для надстроек см. в описании методов в разделе [Управление отправками надстроек](manage-add-on-submissions.md).

<table>
<colgroup>
<col width="10%" />
<col width="30%" />
<col width="60%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Метод</th>
<th align="left">URI</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">GET</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/inappproducts</td>
<td align="left"><a href="get-all-add-ons.md">Получить все надстройки для приложений</a></td>
</tr>
<tr>
<td align="left">GET</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}</td>
<td align="left"><a href="get-an-add-on.md">Возвращение конкретной надстройки</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/inappproducts</td>
<td align="left"><a href="create-an-add-on.md">Создать надстройку</a></td>
</tr>
<tr>
<td align="left">DELETE</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}</td>
<td align="left"><a href="delete-an-add-on.md">Удалить надстройку</a></td>
</tr>
</tbody>
</table>

## <a name="prerequisites"></a>Предварительные условия

Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки в Microsoft Store, прежде чем использовать любой из этих методов.

## <a name="data-resources"></a>Ресурсы данных

Для управления надстройками методы API отправки в Microsoft Store используют следующие ресурсы данных JSON.

<span id="add-on-object" />

### <a name="add-on-resource"></a>Ресурс надстройки

Этот ресурс описывает надстройку.

```json
{
  "applications": {
    "value": [
      {
        "id": "9NBLGGH4R315",
        "resourceLocation": "applications/9NBLGGH4R315"
      }
    ],
    "totalCount": 1
  },
  "id": "9NBLGGH4TNMP",
  "productId": "TestAddOn",
  "productType": "Durable",
  "pendingInAppProductSubmission": {
    "id": "1152921504621243619",
    "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243619"
  },
  "lastPublishedInAppProductSubmission": {
    "id": "1152921504621243705",
    "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243705"
  }
}
```

У этого ресурса есть следующие значения.

| Значение      | Тип   | Описание        |
|------------|--------|--------------|
| Приложения      | Массив  | Массив, содержащий один [ресурс приложения](#application-object), который представляет приложение, с которым связана эта надстройка. Этот массив поддерживает только один элемент.  |
| id | Строка  | Код продукта в Магазине для этой надстройки. Это значение предоставляется Магазином. Пример кода продукта в Магазине: 9NBLGGH4TNMP.  |
| productId | Строка  | Код продукта этой надстройки. Это идентификатор, предоставленный разработчиком при создании надстройки. Дополнительные сведения см. в разделе [Установка типа и кода продукта](https://msdn.microsoft.com/windows/uwp/publish/set-your-iap-product-id). |
| productType | Строка  | Тип продукта этой надстройки. Поддерживаются следующие значения: **Устойчивые** и **готовых к использованию**.  |
| lastPublishedInAppProductSubmission       | Объект | [Ресурс отправки](#submission-object), который предоставляет сведения о последней опубликованной отправке для надстройки.         |
| pendingInAppProductSubmission        | Объект  |  [Ресурс отправки](#submission-object), который предоставляет сведения о текущей ожидающей отправке для надстройки.  |   |

<span id="application-object" />

### <a name="application-resource"></a>Ресурс приложения

Этот ресурс описывает приложение, с которым связана надстройка. В следующем примере показан формат этого ресурса.

```json
{
  "applications": {
    "value": [
      {
        "id": "9NBLGGH4R315",
        "resourceLocation": "applications/9NBLGGH4R315"
      }
    ],
    "totalCount": 1
  },
}
```

У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание        |
|-----------------|---------|-----------|
| value            | Объект  |  Объект, содержащий следующие значения: <br/><br/> <ul><li>*id*. Код продукта в Магазине для приложения. Подробнее о коде продукта в Магазине см. в статье [Просмотр сведений об идентификации приложений](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).</li><li>*resourceLocation*. Относительный путь, который можно добавить к базовому URI запроса ```https://manage.devcenter.microsoft.com/v1.0/my/```, чтобы получить полные данные для приложения.</li></ul>   |
| totalCount   | int  | Количество объектов приложения в массиве *applications* тела ответа.                                                                                                                                                 |

<span id="submission-object" />

### <a name="submission-resource"></a>Ресурс отправки

Этот ресурс содержит сведения об отправке для надстройки. В следующем примере показан формат этого ресурса.

```json
{
  "pendingInAppProductSubmission": {
    "id": "1152921504621243619",
    "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243619"
  },
}
```

У этого ресурса есть следующие значения.

| Значение           | Тип    | Описание     |
|-----------------|---------|------------------|
| id            | Строка  | Идентификатор отправки.    |
| resourceLocation   | Строка  | Относительный путь, который можно добавить к базовому URI запроса ```https://manage.devcenter.microsoft.com/v1.0/my/```, чтобы получить полные данные для отправки.     |
 
<span/>

## <a name="related-topics"></a>Статьи по теме

* [Создание и управление отправкой, с помощью служб Microsoft Store](create-and-manage-submissions-using-windows-store-services.md)
* [Управление отправкой надстройки с помощью API отправки Microsoft Store](manage-add-on-submissions.md)
* [Получение всех надстроек](get-all-add-ons.md)
* [Получения надстройки](get-an-add-on.md)
* [Создать надстройку](create-an-add-on.md)
* [Удалить надстройку](delete-an-add-on.md)
