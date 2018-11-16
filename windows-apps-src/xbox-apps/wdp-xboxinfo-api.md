---
author: M-Stahl
title: Сведения об устройстве Xbox портал Справочник по API
description: Узнайте, как получить доступ к информации устройства Xbox.
ms.author: mstahl
ms.date: 11/7/2017
ms.topic: article
keywords: Windows 10, uwp, xbox, портала устройств
ms.localizationpriority: medium
ms.openlocfilehash: 4b0e2bab0ce7d5525e8032809954ff656a74a61c
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "6992139"
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