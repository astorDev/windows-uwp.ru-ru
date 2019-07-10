---
title: Справка по API для сведений об Xbox на портале устройств
description: Узнайте, как получить доступ к сведениям об устройстве Xbox.
ms.date: 04/18/2019
ms.topic: article
keywords: Windows 10, универсальной платформы Windows, xbox, портал устройств
ms.localizationpriority: medium
ms.openlocfilehash: c6a8e595be9a0846df2af81ea0b7fc1605f62e5f
ms.sourcegitcommit: 139717a79af648a9231821bdfcaf69d8a1e6e894
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67714074"
---
# <a name="xbox-info-api-reference"></a>Справка по API для сведений об Xbox   
Доступ к сведениям об устройстве Xbox One можно получить с помощью API.

## <a name="get-xbox-one-device-information"></a>Получение сведений об устройстве Xbox One

## <a name="request"></a>Запрос

Вы можете получить сведения об устройстве Xbox One.

Метод      | Универсальный код ресурса (URI) запроса
:------     | :-----
GET | /ext/xbox/info

**Параметры URI**

- Нет

**Заголовки запроса**

- Нет

**Текст запроса**

- Нет

## <a name="response"></a>Ответ
Объект JSON с перечисленными ниже полями.

* OsVersion (String) — версия ОС.
* OsEdition (String) — выпуск ОС, например "March 2017" или "March 2017 QFE 1".
* ConsoleId (String) — ИД консоли.
* DeviceId (String) — ИД устройства Xbox Live консоли.
* SerialNumber (String) — серийный номер консоли.
* DevMode (String): текущий режим разработчика консоли, например "None" или "Retail".
* ConsoleType (String): тип консоли, например "Xbox One" или "Xbox One S".
* DevkitCertificateExpirationTime (Number): время UTC в секундах, по истечении которого истекает срок действия сертификата на пакет средств разработчика для консоли.

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
200 | Запрос выполнен успешно
4XX | Коды ошибок
5XX | Коды ошибок

**Семейств доступных устройств**

* Windows Xbox
