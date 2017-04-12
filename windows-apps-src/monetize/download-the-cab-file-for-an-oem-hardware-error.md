---
author: mcleanbyron
ms.assetid: E64030CA-EC00-4113-9939-26D5688C61BC
description: "Используйте этот метод в API аналитики для Магазина Windows, чтобы скачать CAB-файл для ошибки оборудования. Этот метод предназначен только для изготовителей оборудования."
title: "Скачивание CAB-файла для ошибки оборудования OEM"
ms.author: mcleans
ms.date: 03/17/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, UWP, API аналитики для Магазина Windows, скачать CAB-файл"
ms.openlocfilehash: fca846b2a7ec117a99aff1764eb0acffb1a9f11f
ms.sourcegitcommit: 64cfb79fd27b09d49df99e8c9c46792c884593a7
translationtype: HT
---
# <a name="download-the-cab-file-for-an-oem-hardware-error"></a>Скачивание CAB-файла для ошибки оборудования OEM

Используйте этот метод в API аналитики для Магазина Windows, чтобы скачать CAB-файл, связанный с ошибкой оборудования конкретного изготовителя. Перед применением этого метода необходимо сначала воспользоваться методом [получения сведений об ошибке оборудования OEM](get-details-for-an-oem-hardware-error.md), чтобы извлечь идентификатор нужного CAB-файла.

Чтобы получить другие сведения об ошибках оборудования OEM, воспользуйтесь методами [получения данных системы отчетов об ошибках оборудования OEM](get-oem-hardware-error-reporting-data.md) и [получения сведений об ошибке оборудования OEM](get-details-for-an-oem-hardware-error.md) в API аналитики для Магазина Windows.

> [!NOTE]
> Этот метод может использоваться только в учетных записях разработчиков, связанных с [программой Центра разработки оборудования для Windows](https://msdn.microsoft.com/windows/hardware/drivers/dashboard/get-started-with-the-hardware-dashboard).

## <a name="prerequisites"></a>Необходимые условия

Для использования этого метода сначала необходимо сделать следующее:

* Если вы еще не сделали этого, выполните все [необходимые условия](access-analytics-data-using-windows-store-services.md#prerequisites) для API аналитики для Магазина Windows.
* [Получите маркер доступа Azure AD](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token), который будет использоваться в заголовке запроса этого метода. После получения маркера доступа у вас будет 60минут, чтобы использовать его до окончания срока действия маркера. После истечения срока действия маркера можно получить новый маркер.
* Получите идентификатор CAB-файла, который необходимо скачать. Для получения этого идентификатора используйте метод [получения сведений об ошибке оборудования OEM](get-details-for-an-oem-hardware-error.md), чтобы извлечь подробные сведения о конкретной ошибке оборудования, и значение **cabIdHash** в тексте ответа этого метода.

## <a name="request"></a>Запрос


### <a name="request-syntax"></a>Синтаксис запроса

| Метод | URI запроса                                                          |
|--------|----------------------------------------------------------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/hardware/cabdownload``` |

<span/> 

### <a name="request-header"></a>Заголовок запроса

| Заголовок        | Тип   | Описание                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | Строка | Обязательное. Маркер доступа Azure AD в формате **Bearer** &lt;*token*&gt;. |

<span/> 

### <a name="request-parameters"></a>Параметры запроса

| Параметр        | Тип   |  Описание      |  Обязательный  |
|---------------|--------|---------------|------|
| cabIdHash | string | Уникальный идентификатор CAB-файла, который необходимо скачать. Для получения этого идентификатора используйте метод [получения сведений об ошибке оборудования OEM](get-details-for-an-oem-hardware-error.md), чтобы извлечь подробные сведения об определенной ошибке в приложении, и значение **cabIdHash** в тексте ответа этого метода. |  Да  |

<span/>
 
### <a name="request-example"></a>Пример запроса

В следующем примере показано, как с помощью этого метода скачать CAB-файл.

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/hardware/cabdownload?cabIdHash=c1a51104-d682-4230-be14-7278b18e3555 HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>Ответ

Этот метод возвращает код ответа 302 (перенаправление), а заголовку **Location** в ответе присваивается универсальный код ресурса (URI) для подписанного URL-адреса (SAS) CAB-файла. Вызывающий объект перенаправляется на этот URI, чтобы автоматически скачать CAB-файл.

## <a name="related-topics"></a>Связанные разделы

* [Получение данных системы отчетов об ошибках оборудования OEM](get-oem-hardware-error-reporting-data.md)
* [Получение сведений об ошибке оборудования OEM](get-details-for-an-oem-hardware-error.md)
