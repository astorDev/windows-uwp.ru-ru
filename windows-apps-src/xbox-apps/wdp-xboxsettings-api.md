---
author: m-stahl
title: Портал устройства Xbox - справочник по параметрам API для разработчиков
description: Узнайте, как получить доступ к параметрам разработчика Xbox.
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 6ab12b99-2944-49c9-92d9-f995efc4f6ce
ms.openlocfilehash: dfde4c45a4aa5a520e0aa98cd7f31f7d84854e08
ms.sourcegitcommit: 0e44f197e7e649d542ec3f67cd790a61dbe1226f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/07/2017
ms.locfileid: "662504"
---
# <a name="developer-settings-api-reference"></a>Справочник по параметрам API для разработчика   
Вы можете получить доступ к параметрам Xbox One, которые будут полезны при разработке с помощью этого API.

## <a name="get-all-developer-settings-at-once"></a>Получение всех параметров разработчика одновременно

**Запрос**

Вы можете использовать следующий запрос, чтобы получить все параметры разработчика в одном запросе.

Метод      | URI запроса
:------     | :-----
GET | /ext/settings
<br />
**Параметры URI**

- Нет

**Заголовки запроса**

- Нет

**Тело запроса**

- Нет

**Ответ**   
Ответ — это JSON-массив, содержащей все параметры. Каждый объект параметров содержит следующие поля.

Name (string) — имя параметра.
Value (String) — значение параметра.
RequiresReboot ("Yes" | "No") — в этом поле указывается, требуется ли перезагрузка для применения параметра.
Disabled ("Yes" | "Нет")— в этом поле указано, отключен ли параметр и может ли он быть изменен.
Category (String)— категория параметра.
Type ("Text" | "Number" | "Bool" | "Select")— в этом поле указан тип параметра: текстовый ввод, логическое значение ("истина" или "ложь"), число с минимумом и максимумом либо особый список значений на выбор.

Если параметр — это число: Min (Number)— в этом поле указывается минимальное числовое значение параметра.
Max (Number)— в этом поле указано максимальное числовое значение параметра.

Если параметр select: OptionsVariable ("Yes" | "No")— в этом поле указано, являются ли варианты параметра переменными, если действительные варианты можно изменять без перезагрузки.
Options— JSON-массив, содержащий допустимые варианты для выбора в виде строк.

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
200 | Запрос выполнен успешно
4XX | Коды ошибок
5XX | Коды ошибок

## <a name="get-settings-one-at-a-time"></a>Получение параметров по одному
Параметры можно также получить по отдельности.

**Запрос**

Вы можете использовать следующий запрос, чтобы получить информацию об отдельном параметре.

Метод      | URI запроса
:------     | :-----
GET | /ext/settings/\<имя параметра\>
<br />
**Параметры URI**

- Нет

**Заголовки запроса**

- Нет

**Тело запроса**

- Нет

**Ответ**   
Ответ — это объект JSON со следующими полями.

Name (string) — имя параметра.
Value (String) — значение параметра.
RequiresReboot ("Yes" | "No") — в этом поле указывается, требуется ли перезагрузка для применения параметра.
Disabled ("Yes" | "Нет")— в этом поле указано, отключен ли параметр и может ли он быть изменен.
Category (String)— категория параметра.
Type ("Text" | "Number" | "Bool" | "Select")— в этом поле указан тип параметра: текстовый ввод, логическое значение ("истина" или "ложь"), число с минимумом и максимумом либо особый список значений на выбор.

Если параметр — это число: Min (Number)— в этом поле указывается минимальное числовое значение параметра.
Max (Number)— в этом поле указано максимальное числовое значение параметра.

Если параметр select: OptionsVariable ("Yes" | "No")— в этом поле указано, являются ли варианты параметра переменными, если действительные варианты можно изменять без перезагрузки.
Options— JSON-массив, содержащий допустимые варианты для выбора в виде строк.

**Код состояния**

Этот API имеет следующие предполагаемые коды состояния.

Код состояния HTTP      | Описание
:------     | :-----
200 | Запрос выполнен успешно
4XX | Коды ошибок
5XX | Коды ошибок

## <a name="set-the-value-of-a-setting"></a>Установка значения параметра
Вы можете установить значение параметра.

**Запрос**

Вы можете использовать следующий запрос, чтобы установить значение параметра.

Метод      | URI запроса
:------     | :-----
PUT | /ext/settings/\<имя параметра\>
<br />
**Параметры URI**

- Нет

**Заголовки запроса**

- Нет

**Тело запроса**   
Текст запроса — это объект JSON, содержащий следующие поля.   
Value (String) — новое значение параметра.

**Ответ**   

- Нет

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