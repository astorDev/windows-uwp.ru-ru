---
author: eliotcowley
title: Рукоятка управления полетом
description: Используйте API-интерфейс рукоятки управления полетом Windows.Gaming.Input для чтения ввода из рукоятки тестового накопителя.
ms.assetid: DC633F6B-FDC9-4D6E-8401-305861F31192
ms.author: wdg-dev-content
ms.date: 03/06/2017
ms.topic: article
keywords: windows 10, uwp, игры, ввод, рукоятка управления полетом
ms.localizationpriority: medium
ms.openlocfilehash: ebe7695b3f16271f3adedae658c0d62d38d7c078
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "6995407"
---
# <a name="flight-stick"></a>Рукоятка управления полетом

На этой странице приведены основные принципы программирования для рукояток управления полетом, сертифицированных Xbox One, с помощью [Windows.Gaming.Input.FlightStick](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick) и связанных API-интерфейсов для универсальной платформы Windows (UWP).

Изучив информацию на этой странице, вы узнаете:

* как составить список подключенных рукояток управления полетом и их пользователей;
* как определить, что рукоятка управления полетом была добавлена или удалена;
* как считывать входные данные с одной или нескольких рукояток управления полетом;
* как рукоятки управления полетом работают в качестве устройств для навигации по пользовательскому интерфейсу.

## <a name="overview"></a>Обзор

Рукоятки управления полетом— это игровые устройства ввода, которые используются для воспроизведения функций рукояток управления в кабине самолета или космического корабля. Эти устройства ввода идеально подходят для быстрого и точного управления летательным аппаратом. Поддержка рукояток управления полетом в приложениях UWP для Windows 10 и Xbox One реализована с помощью пространства имен [Windows.Gaming.Input](https://docs.microsoft.com/uwp/api/windows.gaming.input).

Рукоятки управления полетом Xbox One оснащаются следующими элементами управления:

* Гибкий аналоговый джойстик, который можно свернуть, повернуть и наклонить
* Аналоговый регулятор
* Две кнопки выполнения
* Цифровой переключатель, способный двигаться в 8 направлениях
* Кнопки **Представление** и **Меню**

> [!NOTE]
> Кнопки **Представление** и **Меню** используются для поддержки навигации в пользовательском интерфейсе, а не для игровых команд, поэтому они не могут использоваться как кнопки джойстика.

### <a name="ui-navigation"></a>Навигация в пользовательском интерфейсе

Чтобы облегчить задачу обеспечения поддержки разных устройств ввода для навигации в пользовательском интерфейсе и сохранить единообразие используемых для этого элементов управления в разных играх и на разных устройствах, большинство _физических_ устройств ввода параллельно выполняют функцию отдельных _логических_ устройств ввода, которые называются [контроллерами навигации в пользовательском интерфейсе](ui-navigation-controller.md). Контроллер навигации в пользовательском интерфейсе предоставляет стандартный набор элементов управления для команд навигации в пользовательском интерфейсе на разных устройствах ввода.

Как контроллер навигации в пользовательском интерфейсе, рукоятка управления полетом сопоставляет [требуемый набор](ui-navigation-controller.md#required-set) команд навигации с джойстиком и кнопками **Представление**, **Меню**, **FirePrimary** и **FireSecondary**.

| Команды навигации | Ввод рукоятки управления полетом                  |
| ------------------:| ----------------------------------- |
|                 Вверх | Джойстик вверх                         |
|               Вниз | Джойстик вниз                       |
|               Влево | Джойстик влево                       |
|              Вправо | Джойстик вправо                      |
|               Просмотр | Кнопка **Представление**                     |
|               Меню | Кнопка **Меню**                     |
|             Принять | Кнопка **FirePrimary**              |
|             Отмена | Кнопка **FireSecondary**            |

Рукоятки управления полетомне поддерживают какие-либо [дополнительные наборы](ui-navigation-controller.md#optional-set) команд навигации.

## <a name="detect-and-track-flight-sticks"></a>Обнаружение и отслеживание рукояток управления полетом

Обнаружение и отслеживание рукояток управления полетом происходит точно так же, как в случае геймпадов, за тем исключением, что вместо класса [Gamepad](https://docs.microsoft.com/uwp/api/Windows.Gaming.Input.Gamepad) используется класс [FlightStick](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick). Дополнительные сведения см. в статье [Геймпад и вибрация](gamepad-and-vibration.md).

<!-- Flight sticks are managed by the system, therefore you don't have to create or initialize them. The system provides a list of connected flight sticks and events to notify you when a flight stick is added or removed.

### The flight stick list

The [FlightStick](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick) class provides a static property, [FlightSticks](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick#Windows_Gaming_Input_FlightStick_FlightSticks), which is a read-only list of flight sticks that are currently connected. Because you might only be interested in some of the connected flight sticks, we recommend that you maintain your own collection instead of accessing them through the `FlightSticks` property.

The following example copies all connected flight sticks into a new collection:

```cpp
auto myFlightSticks = ref new Vector<FlightStick^>();

for (auto flightStick : FlightStick::FlightSticks)
{
    // This code assumes that you're interested in all flight sticks.
    myFlightSticks->Append(flightStick);
}
```

### Adding and removing flight sticks

When a flight stick is added or removed, the [FlightStickAdded](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick#Windows_Gaming_Input_FlightStick_FlightStickAdded) and [FlightStickRemoved](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick#Windows_Gaming_Input_FlightStick_FlightStickRemoved) events are raised. You can register handlers for these events to keep track of the flight sticks that are currently connected.

The following example starts tracking a flight stick that's been added:

```cpp
FlightStick::FlightStickAdded += 
    ref new EventHandler<FlightStick^>([] (Platform::Object^, FlightStick^ args)
{
    // This code assumes that you're interested in all new flight sticks.
    myFlightSticks->Append(args);
});
```

The following example stops tracking a flight stick that's been removed:

```cpp
FlightStick::FlightStickRemoved += 
    ref new EventHandler<FlightStick^>([] (Platform::Object^, FlightStick^ args)
{
    unsigned int indexRemoved;

    if (myFlightSticks->IndexOf(args, &indexRemoved))
    {
        myFlightSticks->RemoveAt(indexRemoved);
    }
});
```

### Users and headsets

Each flight stick can be associated with a user account to link their identity to their gameplay, and can have a headset attached to facilitate voice chat or in-game features. To learn more about working with users and headsets, see [Tracking users and their devices](input-practices-for-games.md#tracking-users-and-their-devices) and [Headset](headset.md). -->

## <a name="reading-the-flight-stick"></a>Чтение рукояток управления полетом

После выбора рукоятки управления полетом, с которой вы хотите работать, можно приступить к сбору данных, вводимых с ее помощью. Тем не менее, в отличие от некоторых других типов вводимых данных, с которыми вы, возможно, уже знакомы, рукоятки управления полетом не передают данные об изменении состояния путем создания событий. Вместо этого для получения стандартной информации об их текущем состоянии вы проводите _опрос_.

### <a name="polling-the-flight-stick"></a>Опрос рукояток управления полетом

В ходе опроса сохраняется моментальный снимок рукоятки управления полетом в конкретный момент времени. Такой подход к сбору входных данных применим к большинству игр, поскольку их логика обычно выполняется в рамках детерминированного цикла и не определяется событиями. Кроме того, проще интерпретировать игровые команды на основе всех одновременно собранных входных данных, чем постепенно собирать входные данные из множества отдельных источников.

Опрос рукоятки управления полетом выполняется путем вызова функции [FlightStick.GetCurrentReading](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstick.GetCurrentReading). Эта функция возвращает структуру [FlightStickReading](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstickreading), которая содержит состояние рукоятки управления полетом.

В следующем примере кода выполняется опрос рукоятки управления полетом на предмет ее текущего состояния.

```cpp
auto flightStick = myFlightSticks->GetAt(0);
FlightStickReading reading = flightStick->GetCurrentReading();
```

Помимо состояния рукоятки управления полетом считанные данные содержат метку времени, указывающую точное время извлечения сведений о состоянии. Метку времени удобно использовать для обращения ко времени предыдущего события считывания данных или ко времени моделирования конкретного игрового момента.

### <a name="reading-the-joystick-and-throttle-input"></a>Данные джойстика и ввод регулятора

Джойстик передает аналоговые данные от -1,0 до 1,0 по осям X, Y и Z (вращение, уклон и поворот соответственно). Для вращения значение -1,0 соответствует крайнему левому положению джойстика, а значение +1,0 — крайнему правому. Для уклона значение -1,0 соответствует крайнему нижнему положению джойстика, а значение +1,0 — крайнему верхнему. Для поворота значение -1,0 соответствует крайнему положению против часовой стрелки, а значение +1,0 — крайнему положению по часовой стрелке.

Когда джойстик находится в центральном положении, по всем осям значение будет приблизительно равно 0,0, но для точного значения нормально изменяться, даже между последующими показаниями. Стратегии для снижения этого изменения рассматриваются далее в этом разделе.

Значение вращения джойстика считывается из свойства [FlightStickReading.Roll](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstickreading.Roll), значение уклона считывается из свойства [FlightStickReading.Pitch](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstickreading.Pitch), а значение поворота считывается из свойства [FlightStickReading.Yaw](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstickreading.Yaw).

```cpp
// Each variable will contain a value between -1.0 and 1.0.
float roll = reading.Roll;
float pitch = reading.Pitch;
float yaw = reading.Yaw;
```

При считывании значений джойстика можно заметить, что когда джойстик находится в состоянии покоя в центральном положении, значения могут быть не равны 0,0. Вместо этого вы будете получать различные значения, близкие к 0,0, каждый раз, когда джойстик перемещается и возвращается в центральное положение. Для устранения данной проблемы можно установить небольшую _мертвую зону_ — диапазон значений рядом с идеальным центральным положением, которые будут игнорироваться.

Один из способов создания мертвой зоны— определить, насколько далеко от центра сдвинулся джойстик, и игнорировать смещения на расстояние, которое окажется меньше выбранного. Расстояние можно вычислить приблизительно &mdash; не точно, поскольку показания джойстика по сути полярные, а не плоскостные &mdash; с помощью теоремы Пифагора. В результате вы получите радиальную мертвую зону.

В следующем примере показан расчет базовой радиальной мертвой зоны с помощью теоремы Пифагора:

```cpp
// Choose a deadzone. Readings inside this radius are ignored.
const float deadzoneRadius = 0.1f;
const float deadzoneSquared = deadzoneRadius * deadzoneRadius;

// Pythagorean theorem: For a right triangle, hypotenuse^2 = (opposite side)^2 + (adjacent side)^2
float oppositeSquared = pitch * pitch;
float adjacentSquared = roll * roll;

// Accept and process input if true; otherwise, reject and ignore it.
if ((oppositeSquared + adjacentSquared) < deadzoneSquared)
{
    // Input accepted, process it.
}
```

### <a name="reading-the-buttons-and-hat-switch"></a>Данные кнопок и переключателя

Каждая из кнопок выполнения рукоятки управления полетом предоставляет цифровые данные, которые указывают, нажата ли кнопка (вниз) или отпущена (вверх). В целях обеспечения эффективности эти показания кнопок не указываются в виде отдельных логических значений. Вместо этого все они упаковываются в одно битовое поле, представляемое перечислением [FlightStickButtons](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstickbuttons). Кроме того, переключатель, способный двигаться в 8 направлениях, обеспечивает направление, упакованное в одно битовое поле, представленное перечислением [GameControllerSwitchPosition](https://docs.microsoft.com/uwp/api/windows.gaming.input.gamecontrollerswitchposition).

> [!NOTE]
> Рукоятки управления полетами оснащаются дополнительными кнопками, используемыми для навигации по пользовательскому интерфейсу, такими как кнопки **Представление** и **Меню**. Эти кнопки не являются частью перечисления `FlightStickButtons`, и данные с них можно считать только при доступе к рукоятке управления полетом как к устройству навигации по пользовательскому интерфейсу. Дополнительные сведения см. в разделе [Контроллер навигации по пользовательскому интерфейсу](ui-navigation-controller.md).

Значения кнопок считываются из свойства [FlightStickReading.Buttons](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstickreading.Buttons). Так как это свойство представляет собой битовое поле, для изоляции значения кнопки, сведения о которой вам интересны, используется побитовая маскировка. Кнопка нажата (вниз), когда установлен соответствующий бит; в противном случае кнопка отпущена (вверх).

Следующий пример кода определяет, нажата ли кнопка **FirePrimary**.

```cpp
if (FlightStickButtons::FirePrimary == (reading.Buttons & FlightStickButtons::FirePrimary))
{
    // FirePrimary is pressed.
}
```

Следующий пример кода определяет, отпущена ли кнопка **FirePrimary**.

```cpp
if (FlightStickButtons::None == (reading.Buttons & FlightStickButtons::FirePrimary))
{
    // FirePrimary is released (not pressed).
}
```

Иногда нужно определить, когда кнопка переходит из нажатого состояния в отпущенное или наоборот, узнать состояние сразу нескольких кнопок (нажаты или отпущены) либо определить особую схему расположения кнопок в наборе&mdash;некоторые кнопки могут быть нажаты, а другие отпущены. Сведения о том, как определить каждое из этих условий, см. в разделах [Определение положений кнопки](input-practices-for-games.md#detecting-button-transitions) и [Определение сложных схем положений кнопок](input-practices-for-games.md#detecting-complex-button-arrangements).

Значение переключателя считывается из свойства [FlightStickReading.HatSwitch](https://docs.microsoft.com/uwp/api/windows.gaming.input.flightstickreading.HatSwitch). Поскольку это свойство также является битовым полем, побитовая маскировка снова используется для изоляции положения переключателя.

В следующем примере показано, как определить, находится ли переключатель в верхнем положении:

```cpp
if (GameControllerSwitchPosition::Up == (reading.HatSwitch & GameControllerSwitchPosition::Up))
{
    // The hat switch is in the up position.
}
```

В следующем примере показано, как определить, находится ли переключатель в центральном положении:

```cpp
if (GameControllerSwitchPosition::Center == (reading.HatSwitch & GameControllerSwitchPosition::Center))
{
    // The hat switch is in the center position.
}
```

<!--## Run the InputInterfacingUWP sample

The [InputInterfacingUWP sample _(github)_](https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/Samples/System/InputInterfacingUWP) demonstrates how to use flight sticks and different kinds of input devices in tandem, as well as how these input devices behave as UI navigation controllers.-->

## <a name="see-also"></a>См. также:

* [Класс Windows.Gaming.Input.UINavigationController](https://docs.microsoft.com/uwp/api/windows.gaming.input.uinavigationcontroller)
* [Интерфейс Windows.Gaming.Input.IGameController](https://docs.microsoft.com/uwp/api/windows.gaming.input.igamecontroller)
* [Рекомендации по использованию устройств ввода для игр](input-practices-for-games.md)