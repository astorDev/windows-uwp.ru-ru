---
author: M-Stahl
title: Сведения об устройстве Xbox портала Справочник по API
description: Узнайте, как получить доступ к данным устройства Xbox.
ms.author: mstahl
ms.date: 11/7/2017
ms.topic: article
keywords: Windows 10, uwp, xbox, портала устройств
ms.localizationpriority: medium
ms.openlocfilehash: 4b0e2bab0ce7d5525e8032809954ff656a74a61c
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5744273"
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