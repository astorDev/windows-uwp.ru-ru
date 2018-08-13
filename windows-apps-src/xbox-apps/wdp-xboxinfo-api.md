---
author: M-Stahl
title: Сведения об устройстве Xbox портала Справочник по API
description: Узнайте, как получить доступ к данным устройства Xbox.
ms.author: mstahl
ms.date: 11/7/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, xbox, портал устройства
ms.localizationpriority: medium
ms.openlocfilehash: db1df2418a2bb60de4a72f51ad01a0bfd547ec20
ms.sourcegitcommit: 897a111e8fc5d38d483800288ad01c523e924ef4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2018
ms.locfileid: "406255"
---
# <a name="xbox-info-api-reference"></a>Справочник по Xbox Info API   
Можно получить доступ к одной Xbox сведений об устройстве, с помощью этот интерфейс API.

## <a name="get-xbox-one-device-information"></a>Получение сведений о Xbox одно устройство

**Запрос**

О вашей Xbox один можно получить сведения об устройстве.

Метод      | URI запроса
:------     | :-----
GET | /ext/xbox/info
<br />
**Параметры универсального кода ресурса (URI)**

- Нет

**Заголовки запроса**

- Нет

**Тело запроса**

- Нет

**Ответ**   
Объект JSON с перечисленными ниже полями.

* OsVersion - (String) версии операционной системы.
* OsEdition - (String) версии операционной системы, такие как «Март 2017» или «март 2017 QFE 1".
* ConsoleId - идентификатор (String) консоли абонента.
* DeviceId - (строки) консоли Xbox Live устройства код.
* SerialNumber - серийный номер (String) консоли абонента.
* DevMode - режиме текущего разработчика (String) консоли пользователя, например, «None» или «Конфигурация».
* ConsoleType - (String) консоль типа, например «Xbox один» или «Один S Xbox».
* DevkitCertificateExpirationTime - (число) время в формате UTC в секундах, истечения срока действия сертификата комплект для разработчиков консоли.

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
200 | Запрос выполнен успешно
4XX | Коды ошибок
5XX | Коды ошибок

<br />
**Доступные семейства устройств**

* Windows Xbox