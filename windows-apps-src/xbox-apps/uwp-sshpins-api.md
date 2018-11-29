---
title: Портал устройств — справочные материалы по API для PIN-кодов SSH
description: Узнайте, как удалить все доверенные PIN-коды SSH программными средствами.
ms.localizationpriority: medium
ms.openlocfilehash: 1ddf15d3cdb4089a8ef010a4ae46d247a06a10d7
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "7991247"
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

