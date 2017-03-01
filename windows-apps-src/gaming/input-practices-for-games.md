---
author: mithom
title: "Рекомендации по использованию устройств ввода для игр"
description: "Узнайте о шаблонах и методиках эффективного использования устройств ввода."
ms.assetid: CBAD3345-3333-4924-B6D8-705279F52676
ms.author: wdg-dev-content
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, игры, ввод"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 15d56a27ad914b258bb19b80b3498510d01105cd
ms.lasthandoff: 02/07/2017

---

# <a name="input-practices-for-games"></a>Способы ввода данных в играх

На этой странице приведены шаблоны и методики эффективного использования устройств ввода в играх универсальной платформы Windows (UWP).

Прочитав эту страницу, вы узнаете:
* как отслеживать игроков, а также используемые ими устройства ввода и навигации;
* как определять положения кнопок (из нажатого в отпущенное, из отпущенного в нажатое);
* как определять сложные схемы положений кнопок с помощью одной проверки.

## <a name="tracking-users-and-their-devices"></a>Отслеживание пользователей и их устройств

Все устройства ввода связаны с классом [User][Windows.System.User], чтобы удостоверение пользователя можно было сопоставить с игровыми моментами, достижениями, изменениями параметров и другими действиями. Пользователи могут входить в устройство ввода или выходить из него по желанию и часто случается, что в устройство ввода, подключенное к системе, входит другой пользователь после выхода предыдущего. При выполнении пользователем входа или выхода возникает событие [IGameController.UserChanged][]. Вы можете зарегистрировать обработчик событий для этого события, чтобы отслеживать игроков и устройства, которые они используют.

Удостоверение пользователя также используется для сопоставления устройства ввода с соответствующим контроллером навигации в пользовательском интерфейсе.

В связи с этим, вводимые пользователем данные, необходимо отслеживать и сопоставлять с помощью свойства [User][igamecontroller.user] класса Device (наследуется из интерфейса [IGameController][]).

В примере кода [UserGamepadPairingUWP _(на GitHub)_](
https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/Samples/System/UserGamepadPairingUWP) показано, как можно отслеживать пользователей и используемые ими устройства.

## <a name="detecting-button-transitions"></a>Определение положений кнопки

Иногда бывает необходимо узнать, когда пользователь сначала нажимает или отпускает кнопку, а именно, в какой момент состояние кнопки переходит из отпущенного состояния в нажатое или наоборот. Чтобы определить это, необходимо знать данные ввода, считанные с устройства ранее, и сравнить с ними текущие показания, чтобы понять, какие изменения произошли.

В следующем примере кода представлен базовый подход к запоминанию предыдущих считанных показаний. Пример основан на геймпадах, однако основной принцип определения состояния кнопок для аркадного джойстика, гоночного руля и кнопок навигации в пользовательском интерфейсе один и тот же.

```cpp
GamepadReading newReading();
GamepadReading oldReading();

// Game::Loop represents one iteration of a typical game loop
void Game::Loop()
{
    // move previous newReading into oldReading before getting next newReading
    oldReading = newReading, newReading = gamepad.CurrentReading();

    // process device readings using buttonJustPressed/buttonJustReleased
}
```

Сначала `Game::Loop` перемещает существующее значение `newReading` (данные ввода, считанные с геймпада во время предыдущей итерации цикла) в `oldReading`, а затем заполняет `newReading` новым считанным с геймпада значением для текущей итерации. На основании полученной информации вы можете определять положения кнопок.

В следующем примере кода представлен базовый подход к обнаружению изменения состояния кнопки.

```cpp
bool buttonJustPressed(const gamepadButtons selection)
{
    bool newSelectionPressed = (selection == (newReading.Buttons & selection));
    bool oldSelectionPressed = (selection == (oldReading.Buttons & selection));

    return newSelectionPressed && !oldSelectionPressed;
}

bool buttonJustReleased(gamepadButtons selection)
{
    bool newSelectionReleased = (gamepadButtons.None == (newReading.Buttons & selection));
    bool oldSelectionReleased = (gamepadButtons.None == (oldReading.Buttons & selection));

    return newSelectionReleased && !oldSelectionReleased;
}
```

Эти две функции сначала наследуют логическое состояние кнопки из `newReading` и `oldReading`, а затем выполняют бинарную логику, чтобы определить, имело ли место целевое изменение состояния кнопки. Эти функции возвращают значение _true_, только если новые считанные данные содержат целевое состояние (кнопка нажата или отпущена соответственно) *и* ранее считанные данные не содержат целевое состояние; в противном случае они возвращают значение _false_.


## <a name="detecting-complex-button-arrangements"></a>Определение сложных схем положений кнопок

Каждая кнопка устройства ввода предоставляет цифровые данные, указывающие на то, нажата ли она (вниз) или отпущена (вверх). В целях обеспечения эффективности эти показания кнопок не указываются в виде отдельных логических значений. Вместо этого все они упаковываются в битовые поля, представляемые соответствующими перечислениями, например [GamepadButtons][] (в зависимости от устройства). Чтобы можно было считать данные с конкретных кнопок, для изоляции значения кнопки, сведения о которой вам интересны, используется побитовая маскировка. Кнопки нажаты (вниз), когда установлен соответствующий бит; в противном случае кнопка отпущена (вверх).

Вспомним, каким образом определяется положение отдельных кнопок (нажата или отпущена). Пример основан на геймпадах, однако основной принцип определения состояния кнопок для аркадного джойстика, гоночного руля и кнопок навигации в пользовательском интерфейсе один и тот же.

```cpp
// determines whether gamepad button A is pressed
if (GamepadButtons::A == (reading.Buttons & GamepadButtons::A))
{
    // button A is pressed
}

// determines whether gamepad button A is released
if (GamepadButtons::None == (reading.Buttons & GamepadButtons::A))
{
    // button A is released (not pressed)
}
```

Как видно, определить состояние одной кнопки просто, однако иногда может потребоваться определить, нажато ли или отпущено несколько кнопок либо узнать особое расположение ряда кнопок — некоторые кнопки нажаты, а некоторые отпущены. Проверка состояния нескольких кнопок является более сложной задачей, чем проверка состояния одной кнопки — особенно в тех случаях, когда существует вероятность, что состояние кнопок разное — однако существует простое правило выполнения этих проверок, равным образом применимое к проверке состояния как одной кнопки, так и нескольких.

В следующем примере кода определяется, нажаты ли обе кнопки геймпада "A" и "B".

```cpp
if ((GamepadButtons::A | GamepadButtons::B) == (reading.Buttons & (GamepadButtons::A | GamepadButtons::B))
{
    // buttons A and B are pressed
}
```

В следующем примере кода определяется, отпущены ли обе кнопки геймпада "A" и "B".

```cpp
if ((GamepadButtons::None == (reading.Buttons & GamepadButtons::A | GamepadButtons::B))
{
    // buttons A and B are released (not pressed)
}
```

В следующем примере определяется, нажата ли кнопка геймпада "A", в то время как кнопка "B" отпущена.

```cpp
if (GamepadButtons::A == (reading.Buttons & (GamepadButtons::A | GamepadButtons::B))
{
    // button A is pressed and button B is released (button B is not pressed)
}
```

Правило, объединяющее все эти пять примеров кода, заключается в том, что расположение проверяемых кнопок, задается выражением в левой части оператора равенства, тогда как кнопки, состояние которых следует учитывать, выбираются с помощью выражения маскировки в правой части.

В следующем примере кода это правило представлено более четко за счет переписывания предыдущего примера.

```cpp
auto buttonArrangement = GamepadButtons::A;
auto buttonSelection = (reading.Buttons & (GamepadButtons::A | GamepadButtons::B));

if (buttonArrangement == buttonSelection)
{
    // button A is pressed and button B is released (button B is not pressed)
}
```

Это правило можно применять для проверки любого количества кнопок с любой схемой их состояний.



[Windows.System.User]: https://msdn.microsoft.com/library/windows/apps/windows.system.user.aspx
[igamecontroller]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.aspx
[igamecontroller.user]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.user.aspx
[igamecontroller.userchanged]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.userchanged.aspx
[gamepadbuttons]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepadbuttons.aspx

