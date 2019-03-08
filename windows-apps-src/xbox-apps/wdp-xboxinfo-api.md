---
title: Справка по API для сведений об Xbox на портале устройств
description: Узнайте, как получить доступ к сведениям об устройстве Xbox.
ms.date: 11/072017
ms.topic: article
keywords: Windows 10, универсальной платформы Windows, xbox, портал устройств
ms.localizationpriority: medium
ms.openlocfilehash: 85c2c139aa8064e1f0769064b95eeb531086b8c1
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57617499"
---
# <a name="xbox-info-api-reference"></a>Справка по API для сведений об Xbox   
Доступ к сведениям об устройстве Xbox One можно получить с помощью API.

## <a name="get-xbox-one-device-information"></a>Получение сведений об устройстве Xbox One

**Запрос**

Вы можете получить сведения об устройстве Xbox One.

Метод      | Универсальный код ресурса (URI) запроса
:------     | :-----
GET | /ext/xbox/info
<br />
**Параметры URI**

- Нет

**Заголовки запроса**

- Нет

**Текст запроса**

- Нет

**Ответ**   
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

<br />
**Семейств доступных устройств**

* Windows Xbox