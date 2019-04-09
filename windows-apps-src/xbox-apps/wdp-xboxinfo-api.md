---
title: Справка по API для сведений об Xbox на портале устройств
description: Узнайте, как получить доступ к сведениям об устройстве Xbox.
ms.date: 11/072017
ms.topic: article
keywords: Windows 10, универсальной платформы Windows, xbox, портал устройств
ms.localizationpriority: medium
ms.openlocfilehash: 7aa8b11bc439266d36fbb27a7eaa7b07e924a17c
ms.sourcegitcommit: bad7ed6def79acbb4569de5a92c0717364e771d9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59244040"
---
# <a name="xbox-info-api-reference"></a>Справка по API для сведений об Xbox   
Доступ к сведениям об устройстве Xbox One можно получить с помощью API.

## <a name="get-xbox-one-device-information"></a>Получение сведений об устройстве Xbox One

## <a name="request"></a>Запрос

Вы можете получить сведения об устройстве Xbox One.

Метод      | Универсальный код ресурса (URI) запроса
:------     | :-----
GET | /ext/xbox/info

**Параметры универсального кода ресурса (URI)**

- Нет

**Заголовки запроса**

- Нет

**Тело запроса**

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

**Доступные семейства устройств**

* Windows Xbox