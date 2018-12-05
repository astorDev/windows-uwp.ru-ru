---
title: Справочные материалы по API удаленного ввода на портале устройств
description: Сведения об удаленной отправке данных ввода с контроллера, клавиатуры и мыши на Xbox.
ms.localizationpriority: medium
ms.openlocfilehash: e0db86ad50bfb1cb27f516243542a554e710c3ea
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8708962"
---
# <a name="remote-input-api-reference"></a>Справочник по API удаленного ввода   
С помощью этого API-интерфейса можно удаленно отправлять данные ввода с контроллера, клавиатуры и мыши в режиме реального времени.

**Запрос**

Метод      | URI запроса
:------     | :-----
WebSocket | /ext/remoteinput
<br />
**Параметры универсального кода ресурса (URI)**

- Нет

**Заголовки запроса**

- Нет

**Запрос**

Веб-сокет, через который осуществляется отправка набора сообщений с массивами байтов. Каждое сообщение имеет следующий формат.

Первый байт указывает на тип входных данных. Поддерживаются следующие типы входных данных.

| Тип ввода        | Значение байта |
|------------|-------------|
Keyboard KeyCodes (клавиатура) | 0x01
Keyboard ScanCodes (клавиатура) | 0x02
Mouse (мышь) | 0x03
Clear All (очистить все) | 0x04

Для KeyboardKeyCodes и KeyboardScanCodes второй байт является значением кода клавиши или кода опроса, а третий байт имеет значение 0x01 для нажатия вниз и значение 0x00 — для отпускания клавиши.

Для сообщения Mouse (Мышь) следующим значением является UINT16 в порядке сети (2 байта), которое указывает на тип события мыши.

| Тип действия        | Значение UINT16 |
|------------|-------------|
Move (переместить) | 0x0001
LeftDown (влево вниз) | 0x0002
LeftUp (влево вверх) | 0x0004
RightDown (вправо вниз) | 0x0008
RightUp (вправо вверх) | 0x0010
MiddleDown (в середину вниз) | 0x0020
MiddleUp (в середину вверх) | 0x0040
X1Down (вниз) | 0x0080
X1Up (вверх) | 0x0100
X2Down (вниз) | 0x0200
X2Up (вверх) | 0x0400
VerticalWheelMoved (движение колесика по вертикали) | 0x0800
HorizontalWheelMoved (движение колесика по горизонтали) | 0x1000

За этим байтом следуют два значения UINT32 в порядке сети и дополнительное третье значение UINT32 для действий колесика мыши. Первые два значения — координаты по оси X и Y, а третье — величина поворота колесика мыши. Координаты по оси X и Y должны иметь значение от 0 до 65,535 и обозначать относительное положение указателя мыши в горизонтальной и вертикальной плоскостях.

ClearAll указывает на то, что любые удерживаемые в настоящий момент клавиши должны быть отпущены. Предполагается, что других байтов нет.

Для отправки данных, вводимых с геймпада, вместе с KeyboardKeyCodes можно использовать значения кодов клавиш, представляющие собой нажатия кнопок на геймпаде. Значения:

| Тип геймпада        | Значение байта |
|------------|-------------|
VK_GAMEPAD_A                       |  0xC3
VK_GAMEPAD_B                       |  0xC4
VK_GAMEPAD_X                       |  0xC5
VK_GAMEPAD_Y                       |  0xC6
VK_GAMEPAD_RIGHT_SHOULDER          |  0xC7
VK_GAMEPAD_LEFT_SHOULDER           |  0xC8
VK_GAMEPAD_LEFT_TRIGGER            |  0xC9
VK_GAMEPAD_RIGHT_TRIGGER           |  0xCA
VK_GAMEPAD_DPAD_UP                 |  0xCB
VK_GAMEPAD_DPAD_DOWN               |  0xCC
VK_GAMEPAD_DPAD_LEFT               |  0xCD
VK_GAMEPAD_DPAD_RIGHT              |  0xCE
VK_GAMEPAD_MENU                    |  0xCF
VK_GAMEPAD_VIEW                    |  0xD0
VK_GAMEPAD_LEFT_THUMBSTICK_BUTTON  |  0xD1
VK_GAMEPAD_RIGHT_THUMBSTICK_BUTTON |  0xD2
VK_GAMEPAD_LEFT_THUMBSTICK_UP      |  0xD3
VK_GAMEPAD_LEFT_THUMBSTICK_DOWN    |  0xD4
VK_GAMEPAD_LEFT_THUMBSTICK_RIGHT   |  0xD5
VK_GAMEPAD_LEFT_THUMBSTICK_LEFT    |  0xD6
VK_GAMEPAD_RIGHT_THUMBSTICK_UP     |  0xD7
VK_GAMEPAD_RIGHT_THUMBSTICK_DOWN   |  0xD8
VK_GAMEPAD_RIGHT_THUMBSTICK_RIGHT  |  0xD9
VK_GAMEPAD_RIGHT_THUMBSTICK_LEFT   |  0xDA


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