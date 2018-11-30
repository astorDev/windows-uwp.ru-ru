---
title: Рекомендации по использованию устройств ввода для игр
description: Узнайте о шаблонах и методиках эффективного использования устройств ввода.
ms.assetid: CBAD3345-3333-4924-B6D8-705279F52676
ms.date: 11/20/2017
ms.topic: article
keywords: windows 10, uwp, игры, ввод
ms.localizationpriority: medium
ms.openlocfilehash: 73e0ba3e563b57c2e392809097567b7e6739c90d
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8210713"
---
# <a name="input-practices-for-games"></a>Способы ввода данных в играх

На этой странице приведены шаблоны и методики эффективного использования устройств ввода в играх универсальной платформы Windows (UWP).

Прочитав эту страницу, вы узнаете:

* как отслеживать игроков, а также используемые ими устройства ввода и навигации;
* как определять положения кнопок (из нажатого в отпущенное, из отпущенного в нажатое);
* как определять сложные схемы положений кнопок с помощью одной проверки.

## <a name="choosing-an-input-device-class"></a>Выбор класса устройств ввода

Существует множество различных API-интерфейсов ввода, таких как [ArcadeStick](https://docs.microsoft.com/uwp/api/windows.gaming.input.arcadestick), [FlightStick](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick) и [Gamepad](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepad). Как выбрать оптимальный API-интерфейс для своей игры?

Выбирайте любой API-интерфейс, который максимально подходит для ввода данных в вашей игре. Например, если вы разрабатываете игру на двухмерной платформе, можно просто использовать класс **Gamepad** и не задействовать дополнительные функциональные возможности, предоставляемые другими классами. В этом случае игра будет поддерживать только геймпады. Единообразный интерфейс обеспечит совместимость со множеством различных геймпадов, поэтому вам не придется писать дополнительный код.

С другой стороны, для сложных игр-симуляторов, где пользователи управляют самолетами или гоночными машинами, потребуется перечислить все объекты [RawGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller). Этот базовый план необходим для поддержки нишевых устройств, распространенных среди геймеров-энтузиастов. Такие устройства могут быть оснащены отдельными педалями или рукоятками управления двигателем, рассчитанными на одного игрока. 

С помощью методов класса ввода **FromGameController**, таких как [Gamepad.FromGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepad.fromgamecontroller), можно проверить, насколько тщательно контролируется каждое устройство. Например, если устройство также относится к классу **Gamepad**, возможно, потребуется изменить пользовательский интерфейс сопоставления кнопок и предоставить игроку выбор из нескольких практичных стандартных сопоставлений. (Обратите внимание на различие: если используется только **RawGameController**, игроку придется вручную настраивать элементы управления геймпада.) 

Кроме того, можно посмотреть код поставщика (VID) и код продукта (PID) в классе **RawGameController** (с помощью методов [HardwareVendorId](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller.HardwareVendorId) и [HardwareProductId](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller.HardwareProductId) соответственно) и создать рекомендуемые сопоставления кнопок для популярных устройств. При этом игра будет по-прежнему совместима с еще неизвестными будущими устройствами, так как игрок сможет настроить сопоставления вручную.

## <a name="keeping-track-of-connected-controllers"></a>Отслеживание подключенных контроллеров

Хотя каждый тип контроллера включает список подключенных контроллеров (например, [Gamepad.Gamepads](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepad.Gamepads)), желательно вести свой собственный список контроллеров. Подробнее см. в разделе [Список геймпадов](gamepad-and-vibration.md#the-gamepads-list) (в статье о каждом типе контроллеров есть раздел с аналогичным названием).

Однако что произойдет, если пользователь отключит свой контроллер или подключит новый? Эти события необходимо обрабатывать и соответствующим образом обновлять список. Подробнее см. в разделе [Добавление и удаление геймпадов](gamepad-and-vibration.md#adding-and-removing-gamepads) (снова-таки, в статье о каждом типе контроллеров есть раздел с аналогичным названием).

Поскольку события добавления и удаления вызываются асинхронно, при работе со списком контроллеров есть возможность получить неверные результаты. Поэтому всякий раз, когда вы обращаетесь к своему списку контроллеров, вы должны установить для него блокировку, чтобы к нему одновременно мог обращаться только один поток. Это можно сделать с помощью [параллельной среды выполнения](https://docs.microsoft.com/cpp/parallel/concrt/concurrency-runtime), а именно [класса critical_section](https://docs.microsoft.com/cpp/parallel/concrt/reference/critical-section-class), который находится в **&lt;ppl.h&gt;**.

Еще один момент, который нужно учитывать,— это то, что список подключенных контроллеров изначально будет пустым, и его заполнение займет одну-две секунды. Поэтому, если вы только назначаете текущий геймпад в методе запуска, он будет иметь значение **null**!

Чтобы это исправить, нужно предусмотреть метод, который "обновляет" главный геймпад (в игре с одним игроком; многопользовательские игры требуют более сложных решений). Этот метод затем следует вызывать в обработчиках событий добавления и удаления контроллеров или же в методе обновления.

Следующий метод просто возвращает первый геймпад в списке (или **nullptr**, если список пустой). Затем просто нужно не забывать делать проверку на предмет **nullptr** всякий раз, когда вы делаете что-либо с этим контроллером. Блокировать ли игровой процесс при отсутствии подключенного контроллера (например, приостанавливать игру) или оставлять его продолжаться без ввода— решать вам.

```cpp
#include <ppl.h>

using namespace Platform::Collections;
using namespace Windows::Gaming::Input;
using namespace concurrency;

Vector<Gamepad^>^ m_myGamepads = ref new Vector<Gamepad^>();

Gamepad^ GetFirstGamepad()
{
    Gamepad^ gamepad = nullptr;
    critical_section::scoped_lock{ m_lock };

    if (m_myGamepads->Size > 0)
    {
        gamepad = m_myGamepads->GetAt(0);
    }

    return gamepad;
}
```

Чтобы продемонстрировать все это вместе, вот пример обработки ввода с помощью геймпада:

```cpp
#include <algorithm>
#include <ppl.h>

using namespace Platform::Collections;
using namespace Windows::Foundation;
using namespace Windows::Gaming::Input;
using namespace concurrency;

static Vector<Gamepad^>^ m_myGamepads = ref new Vector<Gamepad^>();
static Gamepad^          m_gamepad = nullptr;
static critical_section  m_lock{};

void Start()
{
    // Register for gamepad added and removed events.
    Gamepad::GamepadAdded += ref new EventHandler<Gamepad^>(&OnGamepadAdded);
    Gamepad::GamepadRemoved += ref new EventHandler<Gamepad^>(&OnGamepadRemoved);

    // Add connected gamepads to m_myGamepads.
    for (auto gamepad : Gamepad::Gamepads)
    {
        OnGamepadAdded(nullptr, gamepad);
    }
}

void Update()
{
    // Update the current gamepad if necessary.
    if (m_gamepad == nullptr)
    {
        auto gamepad = GetFirstGamepad();

        if (m_gamepad != gamepad)
        {
            m_gamepad = gamepad;
        }
    }

    if (m_gamepad != nullptr)
    {
        // Gather gamepad reading.
    }
}

// Get the first gamepad in the list.
Gamepad^ GetFirstGamepad()
{
    Gamepad^ gamepad = nullptr;
    critical_section::scoped_lock{ m_lock };

    if (m_myGamepads->Size > 0)
    {
        gamepad = m_myGamepads->GetAt(0);
    }

    return gamepad;
}

void OnGamepadAdded(Platform::Object^ sender, Gamepad^ args)
{
    // Check if the just-added gamepad is already in m_myGamepads; if it isn't, 
    // add it.
    critical_section::scoped_lock lock{ m_lock };
    auto it = std::find(begin(m_myGamepads), end(m_myGamepads), args);

    if (it == end(m_myGamepads))
    {
        m_myGamepads->Append(args);
    }
}

void OnGamepadRemoved(Platform::Object^ sender, Gamepad^ args)
{
    // Remove the gamepad that was just disconnected from m_myGamepads.
    unsigned int indexRemoved;
    critical_section::scoped_lock lock{ m_lock };

    if (m_myGamepads->IndexOf(args, &indexRemoved))
    {
        if (m_gamepad == m_myGamepads->GetAt(indexRemoved))
        {
            m_gamepad = nullptr;
        }

        m_myGamepads->RemoveAt(indexRemoved);
    }
}
```

## <a name="tracking-users-and-their-devices"></a>Отслеживание пользователей и их устройств

Все устройства ввода связаны с классом [User](https://docs.microsoft.com/uwp/api/windows.system.user), чтобы удостоверение пользователя можно было сопоставить с игровыми моментами, достижениями, изменениями параметров и другими действиями. Пользователи могут входить в устройство ввода и выходить из него в любое время. Часто случается, что после выхода первого пользователя в устройство ввода, подключенное к системе, входит другой пользователь. При входе или выходе пользователя возникает событие [IGameController.UserChanged](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontroller.UserChanged). Вы можете зарегистрировать обработчик событий для этого события, чтобы отслеживать игроков и используемые устройства.

Удостоверение пользователя— еще один элемент, который связывает устройство ввода с соответствующим [контроллером навигации в пользовательском интерфейсе](ui-navigation-controller.md).

В связи с этим необходимо отслеживать вводимые пользователем данные и сопоставлять их с помощью свойства [User](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontroller.User) в классе устройств (это свойство наследуется у интерфейса [IGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontroller)).

В примере кода [UserGamepadPairingUWP (на GitHub)](
https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/Samples/System/UserGamepadPairingUWP) показано, как можно отслеживать пользователей и используемые ими устройства.

## <a name="detecting-button-transitions"></a>Определение положений кнопки

Иногда бывает необходимо узнать, когда пользователь сначала нажимает или отпускает кнопку, а именно, в какой момент состояние кнопки переходит из отпущенного состояния в нажатое или наоборот. Чтобы определить это, необходимо знать данные ввода, считанные с устройства ранее, и сравнить с ними текущие показания, чтобы понять, какие изменения произошли.

В следующем примере кода представлен базовый подход к запоминанию предыдущих считанных показаний. Пример основан на геймпадах, однако для аркадного джойстика, гоночного руля и других устройств ввода действуют такие же принципы.

```cpp
Gamepad gamepad;
GamepadReading newReading();
GamepadReading oldReading();

// Called at the start of the game.
void Game::Start()
{
    gamepad = Gamepad::Gamepads[0];
}

// Game::Loop represents one iteration of a typical game loop
void Game::Loop()
{
    // move previous newReading into oldReading before getting next newReading
    oldReading = newReading, newReading = gamepad.GetCurrentReading();

    // process device readings using buttonJustPressed/buttonJustReleased (see below)
}
```

Сначала `Game::Loop` перемещает существующее значение `newReading` (данные ввода, считанные с геймпада во время предыдущей итерации цикла) в `oldReading`, а затем заполняет `newReading` новым считанным с геймпада значением для текущей итерации. На основании полученной информации вы можете определять положения кнопок.

В следующем примере кода представлен базовый подход к обнаружению изменений состояния кнопки:

```cpp
bool ButtonJustPressed(const GamepadButtons selection)
{
    bool newSelectionPressed = (selection == (newReading.Buttons & selection));
    bool oldSelectionPressed = (selection == (oldReading.Buttons & selection));

    return newSelectionPressed && !oldSelectionPressed;
}

bool ButtonJustReleased(GamepadButtons selection)
{
    bool newSelectionReleased =
        (GamepadButtons.None == (newReading.Buttons & selection));

    bool oldSelectionReleased =
        (GamepadButtons.None == (oldReading.Buttons & selection));

    return newSelectionReleased && !oldSelectionReleased;
}
```

Эти две функции сначала наследуют логическое состояние выбранной кнопки из `newReading` и `oldReading`, а затем выполняют бинарную логику, чтобы определить, произошло ли целевое изменение состояния кнопки. Эти функции возвращают значение **true**, только если новые считанные данные содержат целевое состояние (кнопка нажата или отпущена соответственно) *и* ранее считанные данные не содержат целевое состояние; в противном случае они возвращают значение **false**.

## <a name="detecting-complex-button-arrangements"></a>Определение сложных схем положений кнопок

Каждая кнопка на устройстве ввода предоставляет цифровые данные, указывающие на ее состояние: нажата (down) или отпущена (up). В целях обеспечения эффективности эти показания кнопок не указываются в виде отдельных логических значений. Вместо этого все они упаковываются в битовые поля, представляемые соответствующими перечислениями, например [GamepadButtons](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepadbuttons) (в зависимости от устройства). Для считывания данных с конкретных кнопок используется побитовая маскировка, позволяющая изолировать нужные значения. Кнопка нажата, когда установлен соответствующий бит (состояние down); в противном случае кнопка отпущена (состояние up).

Вспомним, как определяется положение отдельных кнопок (нажата или отпущена). Пример основан на геймпадах, однако для аркадного джойстика, гоночного руля и других устройств ввода действуют такие же принципы.

```cpp
GamepadReading reading = gamepad.GetCurrentReading();

// Determines whether gamepad button A is pressed.
if (GamepadButtons::A == (reading.Buttons & GamepadButtons::A))
{
    // The A button is pressed.
}

// Determines whether gamepad button A is released.
if (GamepadButtons::None == (reading.Buttons & GamepadButtons::A))
{
    // The A button is released (not pressed).
}
```

Как видно, определить состояние одной кнопки просто, но иногда нужно определить состояние нескольких кнопок или положение кнопок в наборе&mdash;некоторые кнопки могут быть нажаты, а другие отпущены. Проверку состояния нескольких кнопок выполнить сложнее, чем проверку состояния одной кнопки&mdash;особенно когда существует вероятность, что состояние кнопок разное&mdash;однако для этих проверок существует простое правило, которое одинаково успешно применяется и к одной кнопке, и к нескольким.

В следующем примере показано, как определить, нажаты ли обе кнопки геймпада "A" и "B":

```cpp
if ((GamepadButtons::A | GamepadButtons::B) == (reading.Buttons & (GamepadButtons::A | GamepadButtons::B))
{
    // The A and B buttons are both pressed.
}
```

В следующем примере показано, как определить, отпущены ли обе кнопки геймпада "A" и "B":

```cpp
if ((GamepadButtons::None == (reading.Buttons & GamepadButtons::A | GamepadButtons::B))
{
    // The A and B buttons are both released (not pressed).
}
```

В следующем примере показано, как определить, нажата ли кнопка геймпада "A" одновременно с отпущенной кнопкой "B":

```cpp
if (GamepadButtons::A == (reading.Buttons & (GamepadButtons::A | GamepadButtons::B))
{
    // The A button is pressed and the B button is released (B is not pressed).
}
```

Правило, объединяющее все эти пять примеров кода, заключается в том, что расположение проверяемых кнопок, задается выражением в левой части оператора равенства, тогда как кнопки, состояние которых следует учитывать, выбираются с помощью выражения маскировки в правой части.

В следующем примере были внесены изменения по сравнению с предыдущим, поэтому правило показано более наглядно:

```cpp
auto buttonArrangement = GamepadButtons::A;
auto buttonSelection = (reading.Buttons & (GamepadButtons::A | GamepadButtons::B));

if (buttonArrangement == buttonSelection)
{
    // The A button is pressed and the B button is released (B is not pressed).
}
```

Это правило можно применять для проверки любого количества кнопок с любой схемой состояний.

## <a name="get-the-state-of-the-battery"></a>Получение состояния аккумулятора

Для любого игрового устройства управления, реализующего интерфейс [IGameControllerBatteryInfo](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontrollerbatteryinfo), можно вызвать метод [TryGetBatteryReport](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontrollerbatteryinfo.TryGetBatteryReport) в экземпляре устройства управления, чтобы получить объект [BatteryReport](https://docs.microsoft.com/uwp/api/windows.devices.power.batteryreport), который предоставляет информацию об аккумуляторе в устройстве. Можно получить такие свойства, как скорость зарядки ([ChargeRateInMilliwatts](https://docs.microsoft.com/uwp/api/windows.devices.power.batteryreport.ChargeRateInMilliwatts)), предполагаемую энергоемкость нового аккумулятора ([DesignCapacityInMilliwattHours](https://docs.microsoft.com/en-us/uwp/api/windows.devices.power.batteryreport.DesignCapacityInMilliwattHours)) и энергоемкость текущего аккумулятора при полной зарядке ([FullChargeCapacityInMilliwattHours](https://docs.microsoft.com/en-us/uwp/api/windows.devices.power.batteryreport.FullChargeCapacityInMilliwattHours)).

Для игровых устройств управления, поддерживающих подробную отчетность по аккумуляторам, можно получить эту и другую информацию об аккумуляторе, как описано в разделе [Получение сведений об аккумуляторе](../devices-sensors/get-battery-info.md). Тем не менее большинство игровых устройств управления не поддерживают такой уровень отчетности по аккумуляторам, т.к. в них используются недорогие аппаратные элементы. Для таких контроллеров необходимо помнить следующее:

* **ChargeRateInMilliwatts** и **DesignCapacityInMilliwattHours** всегда будут иметь значение **NULL**.

* Вы можете получить процент заряда аккумулятора путем следующего расчета: [RemainingCapacityInMilliwattHours](https://docs.microsoft.com/en-us/uwp/api/windows.devices.power.batteryreport.RemainingCapacityInMilliwattHours) / **FullChargeCapacityInMilliwattHours**. Значения этих свойств следует игнорировать и использовать только вычисленный процент.

* Процент в предыдущем пункте всегда будет иметь одно из следующих значений:

    * 100% (полностью заряжено)
    * 70% (средний уровень)
    * 40% (низкий уровень)
    * 10% (критический уровень)

Если ваш код выполняет какое-либо действие (например, рисует пользовательский интерфейс) на основании оставшегося уровня заряда аккумулятора, следите за тем, чтобы он соответствовал значениям выше. Например, если вы хотите предупреждать игрока о низком заряде аккумулятора в контроллере, делайте это тогда, когда заряд опустится до 10%.

## <a name="see-also"></a>См. также

* [Класс Windows.System.User](https://docs.microsoft.com/uwp/api/windows.system.user)
* [Интерфейс Windows.Gaming.Input.IGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontroller)
* [Перечисление Windows.Gaming.Input.GamepadButtons](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamepadbuttons)
