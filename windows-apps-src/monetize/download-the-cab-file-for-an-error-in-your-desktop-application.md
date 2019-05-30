---
description: Используйте этот метод в API аналитики для Microsoft Store, чтобы скачать CAB-файл для ошибки в вашем классическом приложении.
title: Скачивание CAB-файла для ошибки в классическом приложении
ms.date: 03/06/2018
ms.topic: article
keywords: Windows 10, UWP, API аналитики для Microsoft Store, скачать CAB-файл, классическое приложение
ms.localizationpriority: medium
ms.openlocfilehash: 7a0c6203b3a55ecf8ca5e9473a41a7e6fb233000
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371297"
---
# <a name="download-the-cab-file-for-an-error-in-your-desktop-application"></a>Скачивание CAB-файла для ошибки в классическом приложении

Используйте этот метод в API аналитики для Microsoft Store для загрузки CAB-файла, связанного с конкретной ошибкой для классического приложения, которое вы добавили в [программу для разработчиков классических приложений для Windows](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program). Этот метод позволяет скачать CAB-файл только для ошибок приложения, возникших за последние 30 дней. Загрузка файлов CAB, также доступны в [отчет о работоспособности](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program) для классических приложений в центре партнеров.

Перед применением этого метода необходимо сначала воспользоваться методом [получения сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md), чтобы извлечь хэш идентификатора нужного CAB-файла.

## <a name="prerequisites"></a>предварительные требования


Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Microsoft Store.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения токена доступа у вас будет 60 минут, чтобы использовать его до окончания его срока действия. После истечения срока действия токена можно получить новый токен.
* Получите хэш идентификатора CAB-файла, который необходимо скачать. Для получения этого значения используйте метод [получения подробных сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md), чтобы получить подробные сведения об определенной ошибке в приложении, и значение **cabIdHash** в тексте ответа этого метода.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | Универсальный код ресурса (URI) запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/cabdownload``` |


### <a name="request-header"></a>Заголовок запроса

| Header        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | строка | Обязательный. Маркер доступа Azure AD в форме **носителя** &lt; *маркера*&gt;. |


### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательно  |
|---------------|--------|---------------|------|
| applicationId | строка | Код продукта для классического приложения, по которому требуется скачать CAB-файл. Чтобы получить идентификатор продукта для настольного приложения, открыть любой [отчета аналитики центра партнеров для своего настольного приложения](https://docs.microsoft.com/windows/desktop/appxpkg/windows-desktop-application-program) (такие как **отчет о работоспособности**) и получить идентификатор продукта из URL-адрес. |  Да  |
| cabIdHash | строка | Уникальный хэш идентификатора CAB-файла, который необходимо скачать. Для получения этого значения используйте метод [получения подробных сведений об ошибке в классическом приложении](get-details-for-an-error-in-your-desktop-application.md), чтобы получить подробные сведения об определенной ошибке в приложении, и значение **cabIdHash** в тексте ответа этого метода. |  Да  |


### <a name="request-example"></a>Пример запроса

В следующем примере показано, как с помощью этого метода скачать CAB-файл. Замените параметры *applicationId* и *cabIdHash* соответствующими значениями для вашего классического приложения.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/desktop/cabdownload?applicationId=10238467886765136388&cabIdHash=54ffb83a-e159-41d2-8158-f36f306cc01e HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

Этот метод возвращает код ответа 302 (перенаправление), а заголовку **Location** в ответе присваивается универсальный код ресурса (URI) для подписанного URL-адреса (SAS) CAB-файла. Вызывающий объект перенаправляется на этот URI, чтобы автоматически скачать CAB-файл.

## <a name="related-topics"></a>См. также

* [Отчет о работоспособности](../publish/health-report.md)
* [Доступ к данным аналитики с помощью служб Microsoft Store](access-analytics-data-using-windows-store-services.md)
* [Получить данные для своего настольного приложения отчетов об ошибках](get-desktop-application-error-reporting-data.md)
* [Подробные сведения для ошибки в приложения для настольных компьютеров](get-details-for-an-error-in-your-desktop-application.md)
* [Получить трассировку стека для ошибки в приложения для настольных компьютеров](get-the-stack-trace-for-an-error-in-your-desktop-application.md)
