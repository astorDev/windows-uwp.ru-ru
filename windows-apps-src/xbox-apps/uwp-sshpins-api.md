---
title: Портал устройств — справочные материалы по API для PIN-кодов SSH
description: Узнайте, как удалить все доверенные PIN-коды SSH программными средствами.
ms.localizationpriority: medium
ms.openlocfilehash: 1ddf15d3cdb4089a8ef010a4ae46d247a06a10d7
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8705238"
---
# <a name="ssh-pins-api-reference"></a>Справочные материалы по API для PIN-кодов SSH
Вы можете удалить все доверенные PIN-коды SSH в комплекте средств разработки, используя этот API REST.

## <a name="remove-trusted-ssh-pins"></a>Удаление доверенных PIN-кодов SSH

**Запрос**

Метод      | URI запроса
:------     | :-----
DELETE | /ext/app/sshpins
<br />
**Параметры универсального кода ресурса (URI)**

- Нет

**Заголовки запроса**

- Нет

**Тело запроса**   

- Нет

**Ответ**   

- Нет 

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
204 | Запрос на удаление PIN-кодов выполнен успешно.
4XX | Коды ошибок
5XX | Коды ошибок

<br />
**Доступные семейства устройств**

* Windows Xbox

