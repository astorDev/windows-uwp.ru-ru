---
author: mcleanbyron
description: Используйте этот метод в API аналитики для Microsoft Store, чтобы скачать CAB-файл для ошибки в вашем классическом приложении.
title: Скачивание CAB-файла для ошибки в классическом приложении
ms.author: mcleans
ms.date: 03/06/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, API аналитики для Microsoft Store, скачать CAB-файл, классическое приложение
ms.localizationpriority: medium
ms.openlocfilehash: f1aa6c770451676cb1326f95b96bb0d808039880
ms.sourcegitcommit: 1773bec0f46906d7b4d71451ba03f47017a87fec
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/17/2018
ms.locfileid: "1662724"
---
# <a name="download-the-cab-file-for-an-error-in-your-desktop-application"></a>Скачивание CAB-файла для ошибки в классическом приложении

Используйте этот метод в API аналитики для Microsoft Store для загрузки CAB-файла, связанного с конкретной ошибкой для классического приложения, которое вы добавили в [программу для разработчиков классических приложений для Windows](https://msdn.microsoft.com/library/windows/desktop/mt826504). Этот метод позволяет скачать CAB-файл только для ошибок приложения, возникших за последние 30 дней. Скачать CAB-файл также можно в разделе [Отчетов о работоспособности](https://msdn.microsoft.com/library/windows/desktop/mt826504) для классических приложений на информационной панели Центра разработки для Windows.

Перед применением этого метода необходимо сначала воспользоваться методом [получения сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md), чтобы извлечь хэш идентификатора нужного CAB-файла.

## <a name="prerequisites"></a>Что вам понадобится


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите токен доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия токена можно получить новый токен.
* Получите хэш идентификатора CAB-файла, который необходимо скачать. Для получения этого значения используйте метод [получения подробных сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md), чтобы получить подробные сведения об определенной ошибке в приложении, и значение **cabIdHash** в тексте ответа этого метода.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/cabdownload``` |


### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Авторизация | Строка | Обязательный. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  |
|---------------|--------|---------------|------|
| applicationId | строка | Код продукта для классического приложения, по которому требуется скачать CAB-файл. Чтобы получить код продукта для классического приложения, откройте любой [отчет аналитики центра разработки для классических приложений](https://msdn.microsoft.com/library/windows/desktop/mt826504) (такой как **отчет о работоспособности**) и получите код продукта из URL-адреса. |  Да  |
| cabIdHash | строка | Уникальный хэш идентификатора CAB-файла, который необходимо скачать. Для получения этого значения используйте метод [получения подробных сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md), чтобы получить подробные сведения об определенной ошибке в приложении, и значение **cabIdHash** в тексте ответа этого метода. |  Да  |


### <a name="request-example"></a>Пример запроса

В следующем примере показано, как с помощью этого метода скачать CAB-файл. Замените параметры *applicationId* и *cabIdHash* соответствующими значениями для вашего классического приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/cabdownload?applicationId=10238467886765136388&cabIdHash=54ffb83a-e159-41d2-8158-f36f306cc01e HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

Этот метод возвращает код ответа 302 (перенаправление), а заголовку **Location** в ответе присваивается универсальный код ресурса (URI) для подписанного URL-адреса (SAS) CAB-файла. Вызывающий объект перенаправляется на этот URI, чтобы автоматически скачать CAB-файл.

## <a name="related-topics"></a>Еще по теме:

* [Отчет о работоспособности](../publish/health-report.md)
* [Доступ к аналитическим данным с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получение данных отчетов об ошибках для классического приложения](get-desktop-application-error-reporting-data.md)
* [Получение сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md)
* [Получение трассировки стека при возникновении ошибки в классическом приложении](get-the-stack-trace-for-an-error-in-your-desktop-application.md)
