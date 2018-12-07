---
title: Портал устройств — справочные материалы по API для PIN-кодов SSH
description: Узнайте, как удалить все доверенные PIN-коды SSH программными средствами.
ms.localizationpriority: medium
ms.topic: article
ms.date: 02/08/2017
ms.openlocfilehash: 2c7dc6fab021c11c98276ee53af161bea25601a9
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8779124"
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

