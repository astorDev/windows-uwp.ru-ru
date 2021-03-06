---
title: Справочные материалы по API для контроллеров портала устройств
description: Узнайте, как получить число подключенных физических контроллеров и отключить их программными средствами.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 8b5061f9193d78d4ff23f5fa707b0bea67a10f98
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57657009"
---
# <a name="controller-api-reference"></a>Справочные материалы по API для контроллеров   
Вы можете получить количество подключенных физических контроллеров и отключить их, используя этот API REST.

## <a name="determine-the-number-of-attached-physical-controllers"></a>Определение количества подключенных физических контроллеров

**Запрос**

Количество подключенных физических контроллеров на устройстве можно проверить, используя следующий запрос.

Метод      | Универсальный код ресурса (URI) запроса
:------     | :-----
GET | /ext/remoteinput/controllers
<br />
**Параметры URI**

- Нет

**Заголовки запроса**

- Нет

**Текст запроса**   

- Нет

**Ответ**   

- Свойство номера JSON ConnectedControllerCount, определяющее число подключенных физических контроллеров.

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
200 | Успешно
4XX | Коды ошибок
5XX | Коды ошибок

## <a name="disconnect-all-physical-controllers-on-the-devkit"></a>Отключение всех физических контроллеров в комплекте средств разработки

**Запрос**

Отключить все физические контроллеры на устройстве можно с помощью следующего запроса.

Метод      | Универсальный код ресурса (URI) запроса
:------     | :-----
DELETE | /ext/remoteinput/controllers
<br />
**Параметры URI**

- Нет

**Заголовки запроса**

- Нет

**Текст запроса**   

- Нет

**Ответ**   

- Нет 

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
204 | Запрос на отключение контроллеров выполнен успешно.
4XX | Коды ошибок
5XX | Коды ошибок

<br />
**Семейств доступных устройств**

* Windows Xbox
