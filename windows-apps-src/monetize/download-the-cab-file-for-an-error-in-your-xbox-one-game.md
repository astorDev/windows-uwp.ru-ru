---
author: Xansky
description: Используйте этот метод в API аналитики для Microsoft Store, чтобы скачать CAB-файл для ошибки в игры Xbox One.
title: Скачать CAB-файл для ошибки в игры Xbox One
ms.author: mhopkins
ms.date: 11/06/2018
ms.topic: article
keywords: Windows 10, UWP, API аналитики для Microsoft Store, скачать CAB-файл
ms.localizationpriority: medium
ms.openlocfilehash: ad1e3339fe50087c3a3d8cdcf3a99f27c1c868df
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7302783"
---
# <a name="download-the-cab-file-for-an-error-in-your-xbox-one-game"></a>Скачать CAB-файл для ошибки в игры Xbox One

Используйте этот метод в API аналитики для Microsoft Store, чтобы скачать CAB-файл, связанный с определенной ошибкой в игры Xbox One, которое было добавлено через портал разработчика Xbox (XDP) и доступны на информационной панели центра партнеров аналитики XDP. Этот метод позволяет скачать только CAB-файла для ошибок, возникших за последние 30 дней.

Прежде чем использовать этот метод, необходимо сначала использовать метод [получения сведений об ошибке в игры Xbox One](get-details-for-an-error-in-your-xbox-one-game.md) для получения идентификатора CAB-файла, который необходимо скачать.

## <a name="prerequisites"></a>Необходимые условия


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Получите идентификатор CAB-файла, который необходимо скачать. Для получения этого идентификатора используйте метод [получения сведений об ошибке в Xbox One, игры](get-details-for-an-error-in-your-xbox-one-game.md) , чтобы получить подробные сведения об определенной ошибке в вашем приложении и используйте значение **cabId** в тексте ответа этого метода.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/cabdownload``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  |
|---------------|--------|---------------|------|
| applicationId | string | КОД продукта для игры Xbox One, для которого вы загружаете CAB-файла. Чтобы получить код продукта для игры, перейдите к игре на портале разработчиков для Xbox (XDP) и получите код продукта из URL-адреса. Кроме того Если загрузить данные работоспособности из аналитического отчета центра партнеров Windows, код продукта включается в TSV-файл. |  Да  |
| cabId | string | Уникальный идентификатор CAB-файла, который необходимо скачать. Для получения этого идентификатора используйте метод [получения сведений об ошибке в Xbox One, игры](get-details-for-an-error-in-your-xbox-one-game.md) , чтобы получить подробные сведения об определенной ошибке в вашем приложении и используйте значение **cabId** в тексте ответа этого метода. |  Да  |

 
### <a name="request-example"></a>Пример запроса

В следующем примере показано, как с помощью этого метода скачать CAB-файл. Замените параметры *applicationId* и *cabId* соответствующими значениями для вашего приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/xbox/cabdownload?applicationId=BRRT4NJ9B3D1&cabId=1336373323853 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

Этот метод возвращает код ответа 302 (перенаправление), а заголовку **Location** в ответе присваивается универсальный код ресурса (URI) для подписанного URL-адреса (SAS) CAB-файла. Вызывающий объект перенаправляется на этот URI, чтобы автоматически скачать CAB-файл.

## <a name="related-topics"></a>Статьи по теме

* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение данных системы отчетов для Xbox One игры](get-error-reporting-data-for-your-xbox-one-game.md)
* [Получение сведений об ошибке в Xbox One игры](get-details-for-an-error-in-your-xbox-one-game.md)
* [Получение трассировки стека при возникновении ошибки в Xbox One игры](get-the-stack-trace-for-an-error-in-your-xbox-one-game.md)
