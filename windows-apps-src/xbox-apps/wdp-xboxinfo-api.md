---
title: Сведения об устройстве Xbox портала Справочник по API
description: Узнайте, как получить доступ к данным устройства Xbox.
ms.date: 11/7/2017
ms.topic: article
keywords: Windows 10, uwp, xbox, портала устройств
ms.localizationpriority: medium
ms.openlocfilehash: d7901890e1cc8fab24742e8785562d13d2fe182a
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8206716"
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

* OsVersion - (строка) версию операционной системы.
* OsEdition - (String) в выпуске операционной системы, такие как «Март 2017 г.» или «март 2017 г. QFE 1".
* ConsoleId - код (строка) консоли приложения.
* DeviceId - (строка) консоли Xbox Live устройства идентификатор.
* SerialNumber - (строка) консоли серийный номер.
* DevMode - режима текущего разработчика (строка) консоли элемента, например «None» или «Коммерческие».
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