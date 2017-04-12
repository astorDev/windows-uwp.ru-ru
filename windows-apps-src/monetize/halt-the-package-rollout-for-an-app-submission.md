---
author: mcleanbyron
description: "Используйте этот метод в API отправки Магазина Windows для остановки выпуска пакета в составе отправки приложения."
title: "Остановка выпуска для отправки приложения"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API отправки Магазина Windows, выпуск пакета, отправка приложения, остановка"
ms.assetid: 4ce79fe3-deda-4d31-b938-d672c3869051
ms.openlocfilehash: 7bc86db250f27f0785bf505a15975bcf65cb5eff
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="halt-the-rollout-for-an-app-submission"></a>Остановка выпуска для отправки приложения


Используйте этот метод в API отправки Магазина Windows для [остановки выпуска пакета](../publish/gradual-package-rollout.md#completing-the-rollout) в составе отправки приложения. Дополнительные сведения о процессе создания отправки приложения с помощью API отправки Магазина Windows см. в разделе [Управление отправками приложений](manage-app-submissions.md).

>**Примечание.**&nbsp;&nbsp;Если вы остановили выпуск в составе отправки приложения, а затем [создали новую отправку приложения](create-an-app-submission.md), новая отправка будет являться клоном остановленной отправки.


## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](create-and-manage-submissions-using-windows-store-services.md#prerequisites) для API отправки Магазина Windows.
* [Получите маркер доступа Azure AD](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Создайте отправку для приложения в учетной записи Центра разработки. Это можно сделать на информационной панели Центра разработки или с помощью метода [Создание отправки приложения](create-an-app-submission.md).
* Включите постепенный выпуск пакета для отправки. Это можно сделать на [информационной панели Центра разработки](../publish/gradual-package-rollout.md) или [с помощью API отправки Магазина Windows](manage-app-submissions.md#manage-gradual-package-rollout).

>**Примечание.**&nbsp;&nbsp;Этот метод можно использовать только для учетных записей Центра разработки для Windows, у которых есть разрешение на использование API отправки Магазина Windows. Такое разрешение имеется не у всех учетных записей.

## <a name="request"></a>Запрос

У этого метода следующий синтаксис. Примеры использования и описание заголовка и параметров запроса приведены в следующих разделах.

| Метод | URI запроса                                                      |
|--------|------------------------------------------------------------------|
| POST   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}/haltpackagerollout``` |

<span/>
 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/>

### <a name="request-parameters"></a>Параметры запроса

| Имя        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | Строка | Обязательный. Код продукта в Магазине для приложения, содержащего отправку с выпуском пакета, который требуется остановить. Дополнительные сведения о коде продукта в Магазине см. в разделе [Просмотр сведений об идентификации приложения](https://msdn.microsoft.com/windows/uwp/publish/view-app-identity-details).  |
| submissionId | Строка | Обязательный. Идентификатор отправки с выпуском пакета, который необходимо остановить. Этот идентификатор отображается на информационной панели Центра разработки, а также добавляется в данные ответов для запросов на [Создание отправки приложения](create-an-app-submission.md).  |

<span/>

### <a name="request-body"></a>Тело запроса

Предоставлять текст запроса для этого метода не требуется.

### <a name="request-example"></a>Пример запроса

Следующий пример демонстрирует остановку выпуска пакета для отправки приложения.

```
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/submissions/1152921504621243680/haltpackagerollout HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

В следующем примере представлен текст ответа JSON в случае успешного вызова этого метода. Подробнее о значениях в тексте ответа см. в описании [ресурса «Выпуск пакета»](manage-app-submissions.md#package-rollout-object).

```json
{
    "isPackageRollout": true,
    "packageRolloutPercentage": 0.0,
    "packageRolloutStatus": "PackageRolloutStopped",
    "fallbackSubmissionId": "1212922684621243058"
}
```

## <a name="error-codes"></a>Коды ошибок

Если запрос не удается выполнить, ответ будет содержать один из следующих кодов ошибок HTTP.

| Код ошибки |  Описание   |
|--------|------------------|
| 404  | Не удалось найти отправку. |
| 409  | Этот код указывает на одну из следующих ошибок.<br/><br/><ul><li>Отправка не находится в допустимом состоянии для операции постепенного выпуска (перед вызовом этого метода отправка должна быть опубликована, и значение [packageRolloutStatus](manage-app-submissions.md#package-rollout-object) должно быть равно **PackageRolloutInProgress**).</li><li>Отправка не относится к указанному приложению.</li><li>Приложение использует функцию информационной панели Центра разработки, которая [в настоящее время не поддерживается API отправки Магазина Windows](create-and-manage-submissions-using-windows-store-services.md#not_supported).</li></ul> |   

<span/>


## <a name="related-topics"></a>Статьи по теме

* [Постепенный выпуск пакета](../publish/gradual-package-rollout.md)
* [Управление отправками приложений с помощью API отправки Магазина Windows](manage-app-submissions.md)
* [Создание отправок и управление ими с помощью служб Магазина Windows](create-and-manage-submissions-using-windows-store-services.md)
