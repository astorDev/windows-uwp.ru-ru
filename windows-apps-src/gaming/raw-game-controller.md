---
title: Необработанное игровое устройство управления
description: API-интерфейсы необработанного игрового устройства управления Windows.Gaming.Input позволяют считывать входные данные практически с любых типов игровых устройств управления.
ms.assetid: 2A466C16-1F51-4D8D-AD13-704B6D3C7BEC
ms.date: 03/08/2017
ms.topic: article
keywords: Windows 10, uwp, игры, ввод, необработанное игровое устройство управления
ms.localizationpriority: medium
ms.openlocfilehash: 7b5f4d49ad49cf9f9065fe17788456e9dd2a4a4e
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8879232"
---
# <a name="raw-game-controller"></a>Необработанное игровое устройство управления

На этой странице описываются основные принципы программирования практически любых игровых устройств управления с использованием контроллера [Windows.Gaming.Input.RawGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller) и связанных API-интерфейсов для универсальной платформы Windows (UWP).

Изучив информацию на этой странице, вы узнаете:

* как получить список подключенных необработанных игровых устройств управления и их пользователей
* как определить, что необработанное игровое устройство управления было добавлено или удалено
* как узнать возможности необработанного игрового устройства управления
* как считывать входные данные с необработанного игрового устройства управления

## <a name="overview"></a>Обзор

Необработанное игровое устройство управления— это общее представление игрового устройства управления, элементы которого используются в различных распространенных игровых устройствах управления. Эти элементы управления представлены простыми массивами безымянных кнопок, переключателей и осей. С помощью необработанного игрового устройства управления можно разрешить пользователям создавать собственные сопоставления ввода независимо от типа используемого контроллера.

Класс [RawGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller) фактически предназначен для сценариев, когда другие классы ввода ([ArcadeStick](https://docs.microsoft.com/uwp/api/windows.gaming.input.arcadestick), [FlightStick](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick), и т.д) не соответствуют требованиям &mdash; если вам нужно что-то более универсальное и вы предполагаете, что ваши клиенты будут использовать различные типы игровых устройств управления, выбирайте этот класс.

## <a name="detect-and-track-raw-game-controllers"></a>Обнаружение и отслеживание необработанных игровых устройств управления

Обнаружение и отслеживание необработанных игровых устройств управления происходит точно так же, как в случае геймпадов, за тем исключением, что вместо класса [Gamepad](https://docs.microsoft.com/uwp/api/Windows.Gaming.Input.Gamepad) используется класс [RawGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller). Подробнее см. в статье [Геймпад и вибрация](gamepad-and-vibration.md).

<!-- Raw game controllers are managed by the system, therefore you don't have to create or initialize them. The system provides a list of connected raw game controllers and events to notify you when a raw game controller is added or removed.

### The raw game controller list

The [RawGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller) class provides a static property, [RawGameControllers](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller#Windows_Gaming_Input_RawGameController_RawGameControllers), which is a read-only list of raw game controllers that are currently connected. Because you might only be interested in some of the connected raw game controllers, we recommend that you maintain your own collection instead of accessing them through the **RawGameControllers** property.

The following example copies all connected raw game controllers into a new collection:

```cpp
auto myRawGameControllers = ref new Vector<RawGameController^>();

for (auto rawGameController : RawGameController::RawGameControllers)
{
    // This code assumes that you're interested in all raw game controllers.
    myRawGameControllers->Append(rawGameController);
}
```

### Adding and removing raw game controllers

When a raw game controller is added or removed, the [RawGameControllerAdded](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller#Windows_Gaming_Input_RawGameController_RawGameControllerAdded) and [RawGameControllerRemoved](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller#Windows_Gaming_Input_RawGameController_RawGameControllerRemoved) events are raised. You can register handlers for these events to keep track of the raw game controllers that are currently connected.

The following example starts tracking a raw game controller that's been added:

```cpp
RawGameController::RawGameControllerAdded += 
    ref new EventHandler<RawGameController^>(
        [] (Platform::Object^, RawGameController^ args)
{
    // This code assumes that you're interested in all new raw game controllers.
    myRawGameControllers->Append(args);
});
```

The following example stops tracking a raw game controller that's been removed:

```cpp
RawGameController::RawGameControllerRemoved +=
    ref new EventHandler<RawGameController^>(
        [] (Platform::Object^, RawGameController^ args)
{
    unsigned int indexRemoved;

    if (myRawGameControllers->IndexOf(args, &indexRemoved))
    {
        myRawGameControllers->RemoveAt(indexRemoved);
    }
});
```

### Users and headsets

Each raw game controller can be associated with a user account to link their identity to their gameplay, and can have a headset attached to facilitate voice chat or in-game features. To learn more about working with users and headsets, see [Tracking users and their devices](input-practices-for-games.md#tracking-users-and-their-devices) and [Headset](headset.md). -->

## <a name="get-the-capabilities-of-a-raw-game-controller"></a>Получение информации о возможностях необработанного игрового устройства управления

Выбрав нужное необработанное игровое устройство управления, можно собрать сведения о возможностях этого устройства. Можно получить количество кнопок на необработанном игровом устройстве управления с помощью [RawGameController.ButtonCount](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller.ButtonCount), количество аналоговых осей— с помощью [RawGameController.AxisCount](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller.AxisCount), а количество переключателей— с помощью  [RawGameController.SwitchCount](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller.SwitchCount). Кроме того, можно получить тип переключателя с помощью [RawGameController.GetSwitchKind](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller#Windows_Gaming_Input_RawGameController_GetSwitchKind_System_Int32_).

В следующем примере показано, как получить количество точек ввода для необработанного игрового устройства управления:

```cpp
auto rawGameController = myRawGameControllers->GetAt(0);
int buttonCount = rawGameController->ButtonCount;
int axisCount = rawGameController->AxisCount;
int switchCount = rawGameController->SwitchCount;
```

В следующем примере показано, как определить тип каждого переключателя:

```cpp
for (uint32_t i = 0; i < switchCount; i++)
{
    GameControllerSwitchKind mySwitch = rawGameController->GetSwitchKind(i);
}
```

## <a name="reading-the-raw-game-controller"></a>Считывание данных с необработанного игрового устройства управления

Определив количество точек ввода на необработанном игровом устройстве управления, можно приступать к сбору входных данных. Однако в отличие от некоторых других типов входных данных, с которыми вы, возможно, уже знакомы, необработанные игровые устройства управления не передают сведения об изменении состояния путем создания событий. Вместо этого для получения стандартной информации об их текущем состоянии проводится _опрос_.

### <a name="polling-the-raw-game-controller"></a>Опрос необработанного игрового устройства управления

В ходе опроса сохраняется моментальный снимок необработанного игрового устройства управления в конкретный момент времени. Такой подход к сбору входных данных применим к большинству игр, поскольку их логика обычно выполняется в рамках детерминированного цикла и не определяется событиями. Кроме того, проще интерпретировать игровые команды на основе всех одновременно собранных входных данных, чем постепенно собирать входные данные из множества отдельных источников.

Опрашивать необработанные игровые устройства управления можно с помощью вызова [RawGameController.GetCurrentReading](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller#Windows_Gaming_Input_RawGameController_GetCurrentReading_System_Boolean___Windows_Gaming_Input_GameControllerSwitchPosition___System_Double___). Эта функция заполняет массивы для кнопок, переключателей и осей, которые содержат данные о состоянии необработанного игрового устройства управления.

В следующем примере показано, как опросить необработанное игровое устройство управления и получить сведения о его текущем состоянии:

```cpp
Platform::Array<bool>^ currentButtonReading =
    ref new Platform::Array<bool>(buttonCount);

Platform::Array<GameControllerSwitchPosition>^ currentSwitchReading =
    ref new Platform::Array<GameControllerSwitchPosition>(switchCount);

Platform::Array<double>^ currentAxisReading = ref new Platform::Array<double>(axisCount);

rawGameController->GetCurrentReading(
    currentButtonReading,
    currentSwitchReading,
    currentAxisReading);
```

Невозможно гарантированно определить, какую позицию в каком массиве займет то или иное значение на различных типах контроллеров, поэтому нужно идентифицировать входные данные с помощью методов [RawGameController.GetButtonLabel](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller#Windows_Gaming_Input_RawGameController_GetButtonLabel_System_Int32_) и [RawGameController.GetSwitchKind](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller#Windows_Gaming_Input_RawGameController_GetSwitchKind_System_Int32_).

**GetButtonLabel** сообщает текст или символ, нанесенный на физическую кнопку, но не предоставляет сведений о ее функции&mdash;таким образом, этот метод лучше использовать в качестве вспомогательного средства для пользовательского интерфейса в сценариях, где игрок получает подсказки о функциях кнопок. **GetSwitchKind** помогает определить тип переключателя (то есть количество позиций), но не сообщает его имя.

Стандартного способа получения метки для оси или переключателя не существует, поэтому такие точки ввода необходимо проверить самостоятельно.

Если вы хотите поддерживать определенный контроллер, можно получить [RawGameController.HardwareProductId](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller.HardwareProductId) и [RawGameController.HardwareVendorId](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller.HardwareVendorId) и проверить, соответствуют ли эти значения контроллеру. Положение каждого элемента ввода в каждом массиве всегда одинаково для всех контроллеров с одинаковыми значением свойств **HardwareProductId** и **HardwareVendorId**, поэтому разные контроллеры одного типа не могут использовать разную логику.

Помимо состояния необработанного игрового устройства управления в каждом сеансе считывания данных возвращается метка времени, которая точно указывает время получения сведений о состоянии. Метку времени удобно использовать для обращения ко времени предыдущего события считывания данных или ко времени моделирования конкретного игрового момента.

### <a name="reading-the-buttons-and-switches"></a>Считывание кнопок и переключателей

Для каждой из кнопок необработанного игрового устройства управления можно получить цифровые данные, указывающие положение кнопки: нажата (состояние down) или отпущена (состояние up). Показания кнопки отображаются в виде отдельных логических значений в одном массиве. Метку для каждой кнопки можно найти с помощью [RawGameController.GetButtonLabel](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller#Windows_Gaming_Input_RawGameController_GetButtonLabel_System_Int32_) с индексом логического значения в массиве. Каждое значение представлено как [GameControllerButtonLabel](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamecontrollerbuttonlabel).

В следующем примере показано, как определить, нажата ли кнопка A на **Xbox**:

```cpp
for (uint32_t i = 0; i < buttonCount; i++)
{
    if (currentButtonReading[i])
    {
        GameControllerButtonLabel buttonLabel = rawGameController->GetButtonLabel(i);

        if (buttonLabel == GameControllerButtonLabel::XboxA)
        {
            // XboxA is pressed.
        }
    }
}
```

Иногда нужно определить, когда кнопка переходит из нажатого состояния в отпущенное или наоборот, узнать состояние сразу нескольких кнопок (нажаты или отпущены) либо определить особую схему расположения кнопок в наборе&mdash;некоторые кнопки могут быть нажаты, а другие отпущены. Сведения о том, как определить каждое из этих условий, см. в разделах [Определение положений кнопки](input-practices-for-games.md#detecting-button-transitions) и [Определение сложных схем положений кнопок](input-practices-for-games.md#detecting-complex-button-arrangements).

Значения переключателей предоставляются в виде массива [GameControllerSwitchPosition](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamecontrollerswitchposition). Поскольку это свойство является битовым полем, для изоляции направление переключения используется побитовая маскировка.

В следующем примере показано, как определить, находится ли каждый переключатель в верхнем положении:

```cpp
for (uint32_t i = 0; i < switchCount; i++)
{
    if (GameControllerSwitchPosition::Up ==
        (currentSwitchReading[i] & GameControllerSwitchPosition::Up))
    {
        // The switch is in the up position.
    }
}
```

### <a name="reading-the-analog-inputs-sticks-triggers-throttles-and-so-on"></a>Считывание данных с аналоговых устройств ввода (рукояток, триггеров, регуляторов и т.д.)

Аналоговая ось предоставляет показания от 0,0 до 1,0. В эти показания входят все измерения джойстика, например оси X и Y для стандартных джойстиков, или оси X, Y и Z (разворот, уклон и поворот соответственно) для рукояток управления полетами.

Эти значения могут представлять аналоговые триггеры, регуляторы и любые другие аналоговые устройства ввода. Эти значения не снабжаются метками, поэтому рекомендуется проверить код на различных устройствах ввода и убедиться, что значения совпадают с вашими ожиданиями.

Когда джойстик находится в центральном положении, значение на всех осях составляет приблизительно 0,5. Однако это значение может меняться даже в промежутке между двумя последовательными считываниями. Способы устранения таких отклонений рассматриваются далее в этом разделе.

В приведенном ниже примере показано, как считывать аналоговые значения с контроллера Xbox:

```cpp
// Xbox controllers have 6 axes: 2 for each stick and one for each trigger.
float leftStickX = currentAxisReading[0];
float leftStickY = currentAxisReading[1];
float rightStickX = currentAxisReading[2];
float rightStickY = currentAxisReading[3];
float leftTrigger = currentAxisReading[4];
float rightTrigger = currentAxisReading[5];
```

При считывании значений джойстика можно заметить, что значения могут быть не равны 0,5, даже если джойстик находится без движения в центральном положении. Вместо этого отображаются различные значения, близкие к 0,5, когда джойстик перемещается и возвращается в центральное положение. Для устранения данной проблемы можно установить небольшую _мертвую зону_ — диапазон значений рядом с идеальным центральным положением, которые будут игнорироваться.

Один из способов создания мертвой зоны— определить, насколько далеко от центра сдвинулся джойстик, и игнорировать смещения на расстояние, которое окажется меньше выбранного. Расстояние можно вычислить приблизительно&mdash;не точно, поскольку показания джойстика по сути полярные, а не плоскостные&mdash;с помощью теоремы Пифагора. В результате вы получите радиальную мертвую зону.

В следующем примере показан расчет базовой радиальной мертвой зоны с помощью теоремы Пифагора:

```cpp
// Choose a deadzone. Readings inside this radius are ignored.
const float deadzoneRadius = 0.1f;
const float deadzoneSquared = deadzoneRadius * deadzoneRadius;

// Pythagorean theorem: For a right triangle, hypotenuse^2 = (opposite side)^2 + (adjacent side)^2
float oppositeSquared = leftStickY * leftStickY;
float adjacentSquared = leftStickX * leftStickX;

// Accept and process input if true; otherwise, reject and ignore it.
if ((oppositeSquared + adjacentSquared) < deadzoneSquared)
{
    // Input accepted, process it.
}
```

<!--## Run the RawGameControllerUWP sample

The [RawGameControllerUWP sample (GitHub)](TODO: Link) demonstrates how to use raw game controllers. TODO: More information-->

## <a name="see-also"></a>См. также

* [Устройства ввода для игр](input-for-games.md)
* [Рекомендации по использованию устройств ввода для игр](input-practices-for-games.md)
* [Пространство имен Windows.Gaming.Input](https://docs.microsoft.com/uwp/api/windows.gaming.input)
* [Класс Windows.Gaming.Input.IGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.rawgamecontroller)
* [Интерфейс Windows.Gaming.Input.IGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontroller)
* [Интерфейс Windows.Gaming.Input.IGameControllerBatteryInfo](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontrollerbatteryinfo)