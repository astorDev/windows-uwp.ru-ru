---
title: Сведения об устройстве Xbox портал Справочник по API
description: Узнайте, как получить доступ к информации устройства Xbox.
ms.date: 11/7/2017
ms.topic: article
keywords: Windows 10, uwp, xbox, портала устройств
ms.localizationpriority: medium
ms.openlocfilehash: d7901890e1cc8fab24742e8785562d13d2fe182a
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "7694599"
---
# <a name="xbox-info-api-reference"></a>Справочник по API сведения Xbox   
Можно получить доступ к Xbox One сведения об устройстве, с помощью этого API.

## <a name="get-xbox-one-device-information"></a>Получить Xbox One сведения об устройстве

**Запрос**

Вы можете получить сведения об устройстве о консоли Xbox One.

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

* OsVersion - (String) версию операционной системы.
* OsEdition - (String) в версии операционной системы, такие как «Март 2017 г.» или «март 2017 г. QFE 1".
* ConsoleId - код (String) консоли приложения.
* DeviceId - (String) консоли Xbox Live устройства идентификатор.
* SerialNumber - (String) консоли серийный номер.
* DevMode - режима текущего разработчика (String) консоли приложения, например «None» или «Retail».
* ConsoleType - типа (String) консоли приложения, например «Xbox One» или «Xbox One S».
* DevkitCertificateExpirationTime - (Number) — время UTC в секундах, когда истечет срок действия сертификата комплект средств для разработчика консоли.

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