---
description: Этот метод можно используйте в API анализа Microsoft Store для загрузки CAB-файл для ошибки в игре для Xbox One.
title: Скачать CAB-файл для ошибки в игре Xbox One
ms.date: 11/06/2018
ms.topic: article
keywords: Windows 10, UWP, API аналитики для Microsoft Store, скачать CAB-файл
ms.localizationpriority: medium
ms.openlocfilehash: 736219533a254e6380c10600e97f707f15e37de6
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57604329"
---
# <a name="download-the-cab-file-for-an-error-in-your-xbox-one-game"></a>Скачать CAB-файл для ошибки в игре Xbox One

Этот метод можно используйте в API анализа Microsoft Store для загрузки CAB-файл, связанный с конкретной ошибки в игре для Xbox One, был принимаются через портал разработчиков Xbox (XDP) и доступной на панели мониторинга центра партнеров XDP Analytics. Этот метод можно только загрузить CAB-файл для ошибки, возникшей в последние 30 дней.

Прежде чем использовать этот метод, необходимо сначала воспользоваться [получения сведений о ошибку в Xbox One игр](get-details-for-an-error-in-your-xbox-one-game.md) метод для получения идентификатора файла CAB нужно загрузить.

## <a name="prerequisites"></a>Предварительные условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.
* Получите идентификатор CAB-файла, который необходимо скачать. Чтобы получить этот идентификатор, используйте [получения сведений о ошибку в Xbox One игр](get-details-for-an-error-in-your-xbox-one-game.md) метод для получения сведений о конкретной ошибке в приложении и используйте **cabId** значения в тексте ответа этого метода.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/cabdownload``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательно  |
|---------------|--------|---------------|------|
| applicationId | Строка | Идентификатор продукта игры Xbox One, для которого требуется загрузить CAB-файл. Чтобы получить код продукта для игры, перейдите к игре на портале разработчиков для Xbox (XDP) и получите код продукта из URL-адреса. Кроме того при загрузке данных о работоспособности из отчет по аналитике центра партнеров Windows, код продукта входит в TSV-файл. |  Да  |
| cabId | Строка | Уникальный идентификатор CAB-файла, который необходимо скачать. Чтобы получить этот идентификатор, используйте [получения сведений о ошибку в Xbox One игр](get-details-for-an-error-in-your-xbox-one-game.md) метод для получения сведений о конкретной ошибке в приложении и используйте **cabId** значения в тексте ответа этого метода. |  Да  |

 
### <a name="request-example"></a>Пример запроса

В следующем примере показано, как с помощью этого метода скачать CAB-файл. Замените параметры *applicationId* и *cabId* соответствующими значениями для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/cabdownload?applicationId=BRRT4NJ9B3D1&cabId=1336373323853 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

Этот метод возвращает код ответа 302 (перенаправление), а заголовку **Location** в ответе присваивается универсальный код ресурса (URI) для подписанного URL-адреса (SAS) CAB-файла. Вызывающий объект перенаправляется на этот URI, чтобы автоматически скачать CAB-файл.

## <a name="related-topics"></a>Статьи по теме

* [Доступ к данным аналитики с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получить данные отчетов об ошибках для Xbox One игр](get-error-reporting-data-for-your-xbox-one-game.md)
* [Подробные сведения для ошибки в Xbox One игр](get-details-for-an-error-in-your-xbox-one-game.md)
* [Получить трассировку стека для ошибки в Xbox One игр](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)
