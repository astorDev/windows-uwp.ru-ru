---
title: Справочник по API для сетевых учетных данных портала устройств
description: Узнайте, как добавлять, удалять или обновлять сетевые учетные данные программными средствами.
ms.localizationpriority: medium
ms.topic: article
ms.date: 02/08/2017
ms.openlocfilehash: ac30d8db830c51ee40653feb49b443ed44502617
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57659219"
---
# <a name="network-credentials-api-reference"></a>Справочник по API для сетевых учетных данных
Вы можете добавлять, удалять или обновлять сохраненные сетевые учетные данные в комплекте средств разработчика с помощью этого API REST.

## <a name="get-existing-credentials"></a>Получение имеющихся учетных данных

**Запрос**

Вы можете получить список сохраненных общих ресурсов вместе с именем пользователя, имеющего учетные данные для этого сетевого общего ресурса.

Метод      | Универсальный код ресурса (URI) запроса
:------     | :-----
GET | /ext/networkcredential
<br />
**Параметры URI**

- Нет

**Заголовки запроса**

- Нет

**Текст запроса**   

- Нет

**Ответ**   

- JSON-массив в следующем формате:
* Учетные данные
  * NetworkPath — путь к сетевому общему ресурсу.
  * Username — имя пользователя, который имеет сохраненные учетные данные.

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
200 | Успешно
4XX | Коды ошибок
5XX | Коды ошибок

## <a name="add-or-update-stored-credentials-for-a-user"></a>Добавление или обновление сохраненных учетных данных пользователя

**Запрос**

Метод      | Универсальный код ресурса (URI) запроса
:------     | :-----
POST | /ext/networkcredential
<br />
**Параметры URI**

В запросе URI можно указать следующие дополнительные параметры.

| Параметр универсального кода ресурса (URI)      | Описание     | 
| ------------------ |-----------------|
| NetworkPath        | Сетевой путь к общему ресурсу, для которого вы добавляете учетные данные с целью доступа. |
<br>

**Заголовки запроса**

- Нет

**Текст запроса**

- Следующие элементы JSON:
* NetworkPath — путь к сетевому общему ресурсу.
* Username — имя пользователя для хранения учетных данных.
* Password — новый или обновленный пароль для этого пользователя.

**Ответ**   

- Нет  

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
204 | Успешно
4XX | Коды ошибок
5XX | Коды ошибок

## <a name="remove-stored-credentials-for-a-share"></a>Удаление сохраненных учетных данных для общего ресурса.

**Запрос**

Метод      | Универсальный код ресурса (URI) запроса
:------     | :-----
DELETE | /ext/networkcredential
<br />
**Параметры URI**

В запросе URI можно указать следующие дополнительные параметры.

| Параметр универсального кода ресурса (URI)      | Описание     | 
| ------------------ |-----------------|
| NetworkPath        | Сетевой путь к общему ресурсу, из которого нужно удалить сохраненные учетные данные. |
<br>

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
204 | Запрос учетных данных выполнен успешно.
4XX | Коды ошибок
5XX | Коды ошибок

<br />
**Семейств доступных устройств**

* Windows Xbox


