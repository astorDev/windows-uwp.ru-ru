---
author: mcleanbyron
description: "Используйте этот метод в API отправки Магазина Windows для получения сведений о выпуске пакета в составе отправки тестового пакета."
title: "Получение сведений о выпуске пакета в составе отправки тестового пакета с использованием API отправки магазина Windows"
translationtype: Human Translation
ms.sourcegitcommit: 9b76a11adfab838b21713cb384cdf31eada3286e
ms.openlocfilehash: 72dcad2c6f278e81c8f4fbbc7353c1aada123862

---

# Получение сведений о выпуске пакета в составе отправки тестового пакета с использованием API отправки магазина Windows


Используйте этот метод в API отправки Магазина Windows для получения сведений о [выпуске пакета](../publish/gradual-package-rollout.md) в составе отправки тестового пакета. Дополнительные сведения о процессе создания отправки тестового пакета с помощью API отправки Магазина Windows см. в разделе [Управление отправкой тестового пакета](manage-flight-submissions.md).

## Необходимые условия

Для использования этого метода необходимо выполнить следующие действия:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку тестового пакета для приложения в учетной записи центра разработки. Это можно сделать на информационной панели центра разработки или с помощью метода [создания отправки тестового пакета](create-a-flight-submission.md).

>**Примечание.**&nbsp;&nbsp;Этот метод можно использовать только для учетных записей Центра разработки для Windows, у которых есть разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей.

## Запрос

У этого метода следующий синтаксис. Примеры использования и описание заголовка и параметров запроса приведены в следующих разделах.

| Метод | Универсальный код ресурса (URI) запроса                                                      |
|--------|------------------------------------------------------------------|
| GET   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights/{flightId}/submissions/{submissionId}/packagerollout   ``` |

<span/>
 

### Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | Обязательный. Код продукта в Магазине для приложения, содержащего отправку тестового пакета, с информацией о выпуске пакета, которую требуется получить. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| flightId | string | Обязательный. Код продукта в Магазине для тестового пакета, содержащего отправку тестового пакета, с информацией о выпуске пакета, которую требуется получить. Этот идентификатор отображается на информационной панели Центра разработки, а также добавляется в данные ответов для запросов на [создание тестового пакета](create-a-flight.md) и [получение тестовых пакетов для приложения](get-flights-for-an-app.md).  |
| submissionId | строка | Обязательный. Идентификатор отправки с информацией о выпуске пакета, которую требуется получить. Этот идентификатор отображается на информационной панели Центра разработки, а также добавляется в данные ответов для запросов на [создание отправки тестового пакета](create-a-flight-submission.md).  |

<span/>

### Тело запроса

Предоставлять тело запроса для этого метода не требуется.

### Пример запроса

Следующий пример демонстрирует получение сведений о выпуске пакета для отправки тестового пакета.

```
GET https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights/43e448df-97c9-4a43-a0bc-2a445e736bcd/submissions/1152921504621243649/packagerollout HTTP/1.1
Authorization: Bearer <your access token>
```

## Ответ

В следующем примере показано тело ответа JSON, позволяющего успешно выполнить вызов этого метода для отправки тестового пакета с поддержкой постепенного выпуска пакета. Подробнее о значениях в тексте ответа см. в описании [ресурса "Выпуск пакета"](manage-flight-submissions.md#package-rollout-object).

```json
{
    "isPackageRollout": true,
    "packageRolloutPercentage": 25,
    "packageRolloutStatus": "PackageRolloutInProgress",
    "fallbackSubmissionId": "1212922684621243058"
}
```

Если отправка тестового пакета не поддерживает постепенный выпуск пакета, возвращается следующее тело ответа.

```json
{
    "isPackageRollout": false,
    "packageRolloutPercentage": 0,
    "packageRolloutStatus": "PackageRolloutNotStarted",
    "fallbackSubmissionId": "0"
}
```

## Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти отправку тестового пакета. |
| 409  | Отправка тестового пакета не принадлежит указанному тестовому пакету, или приложение использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported). |   

<span/>


## Связанные статьи

* [Постепенный выпуск пакета](../publish/gradual-package-rollout.md)
* [Управление отправками тестовых пакетов с помощью API отправки Магазина Windows](manage-flight-submissions.md)
* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)



<!--HONumber=Nov16_HO1-->

