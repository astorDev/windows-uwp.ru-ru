---
author: eliotcowley
title: Геймпад и вибрация
description: Используйте API Windows.Gaming.Input геймпада для определения, считывания и отправки команд вибрации и импульсов на геймпады.
ms.assetid: BB03BB8E-255F-4AE8-AC43-1E519CA860FE
ms.author: wdg-dev-content
ms.date: 09/06/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, игры, геймпад, вибрация
ms.localizationpriority: medium
ms.openlocfilehash: 2bf78b43bb09f97c196858d7cc4fcdb1e71462fc
ms.sourcegitcommit: 68fcac3288d5698a13dbcbd57f51b30592f24860
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "4061102"
---
# <a name="gamepad-and-vibration"></a>Геймпад и вибрация

На этой странице приведены основные принципы программирования для геймпадов Xbox One с помощью [Windows.Gaming.Input.Gamepad][геймпад] и связанных API-интерфейсов для универсальной платформы Windows (UWP).

Изучив информацию на этой странице, вы узнаете:

* как составить список подключенных геймпадов и их пользователей;
* как определить, что геймпад был добавлен или удален;
* как считывать входные данные с одного или нескольких геймпадов;
* как отправлять команды вибрации и импульсов;
* как геймпады работают в качестве устройств навигации по Пользовательскому интерфейсу

## <a name="gamepad-overview"></a>Обзор геймпадов

Такие геймпады, как беспроводной геймпад Xbox и беспроводной геймпад Xbox серии S являются игровыми устройствами ввода общего назначения. Это стандартные устройства ввода для Xbox One, которое игроки часто используют в среде Windows, если им неудобно играть с помощью клавиатуры и мыши. Поддержка геймпадов в приложениях UWP для Windows 10 и Xbox реализована с помощью пространства имен [Windows.Gaming.Input][].

Геймпады Xbox One оснащаются направления крестовины (или крестовиной); **A**, **B**, **X**, **Y**, **представления**и кнопки **меню** ; левый и правый аналоговые стики, бамперы и триггеров; и общее количество четырьмя вибромоторами. Оба аналоговых стика обеспечивают двойное считывание аналоговых данных по осям X и Y, а также выступают как кнопки при нажатии вниз. Каждый триггер предоставляет показания аналоговый, представляющее, насколько далеко он будет отображен обратно.

<!-- > [!NOTE]
> The Xbox Elite Wireless Controller is equipped with four additional **Paddle** buttons on its underside. These can be used to provide redundant access to game commands that are difficult to use together (such as the right thumbstick together with any of the **A**, **B**, **X**, or **Y** buttons) or to provide dedicated access to additional commands. -->

> [!NOTE]
> `Windows.Gaming.Input.Gamepad` также поддерживает геймпады Xbox 360, которые имеют один и тот же макет элементов управления, как и стандартные геймпады Xbox One.

### <a name="vibration-and-impulse-triggers"></a>Триггеры вибрации и импульсов

Геймпады Xbox One оснащены двумя отдельными моторами, обеспечивающими сильную и слабую вибрацию геймпада, а также двумя специальными моторами для резкой вибрации для каждого триггера (благодаря этой уникальной функции триггеры геймпадов Xbox One называются _импульсными триггерами_).

> [!NOTE]
> Геймпады Xbox 360 не оснащаются _импульсных триггеров_.

Дополнительные сведения см. в разделе [Обзор вибрации и импульсных триггеров](#vibration-and-impulse-triggers-overview).

### <a name="thumbstick-deadzones"></a>Мертвые зоны аналоговых стиков

Аналоговый стик в состоянии покоя в центральном положении в идеале каждый раз должен давать одинаковые нейтральные значения по осям X и Y. Однако из-за механических воздействий и чувствительности стика фактические значения в центральном положении только приблизительно соответствуют идеальным нейтральным значениям и могут различаться между разными считываниями. По этой причине необходимо всегда использовать небольшой _мертвой зоны_&mdash;диапазон значений рядом с идеальным центральным положением, которые игнорируются&mdash;чтобы компенсировать различия производства, механическим износом и другими проблемами с геймпадом.

Чем больше мертвая зона, тем проще отличить преднамеренный ввод от непреднамеренного ввода.

Дополнительные сведения см. в разделе [Считывание значений аналогового стика](#reading-the-thumbsticks).

### <a name="ui-navigation"></a>Навигация по пользовательскому интерфейсу

Чтобы облегчить задачу обеспечения поддержки разных устройств ввода для навигации в пользовательском интерфейсе и сохранить единообразие используемых для этого элементов управления в разных играх и на разных устройствах, большинство _физических_ устройств ввода параллельно выполняют функцию отдельного _логического_ устройства ввода под названием [контроллер навигации в пользовательском интерфейсе](ui-navigation-controller.md). Контроллер навигации в пользовательском интерфейсе предоставляет стандартный набор элементов управления для команд навигации в пользовательском интерфейсе на разных устройствах ввода.

Контроллер навигации в пользовательском Интерфейсе геймпадов сопоставлять [необходимый набор](ui-navigation-controller.md#required-set) команд навигации для левого джойстика, Крестовина, **представления**, **меню**, **A**и **B** кнопки.

| Команда навигации | Действие геймпада                       |
| ------------------:| ----------------------------------- |
|                 Вверх | Левый аналоговый стик вверх / крестовина вверх       |
|               Вниз | Левый аналоговый стик вниз / крестовина вниз   |
|               Влево | Левый аналоговый стик влево / крестовина влево   |
|              Вправо | Левый аналоговый стик вправо / крестовина вправо |
|               Просмотр | Кнопка просмотра                         |
|               Меню | Кнопка меню                         |
|             Принять | Кнопка A                            |
|             Отмена | Кнопка B                            |

Кроме того, геймпады поддерживают все [дополнительные наборы](ui-navigation-controller.md#optional-set) команд навигации для остальных входных данных.

| Команда навигации | Действие геймпада          |
| ------------------:| ---------------------- |
|            Предыдущая страница | Левый триггер           |
|          Следующая страница | Правый триггер          |
|          Страница влево | Левый бампер            |
|         Страница вправо | Правый бампер           |
|          Прокрутка вверх | Правый аналоговый стик вверх    |
|        Прокрутка вниз | Правый аналоговый стик вниз  |
|        Прокрутка влево | Правый аналоговый стик влево  |
|       Прокрутка вправо | Правый аналоговый стик вправо |
|          Контекстный вызов 1 | Кнопка X               |
|          Контекстный вызов 2 | Кнопка Y               |
|          Контекстный вызов 3 | Нажатие левого аналогового стика  |
|          Контекстный вызов 4 | Нажатие правого аналогового стика |

## <a name="detect-and-track-gamepads"></a>Обнаружение и отслеживание геймпадов

Геймпадами управляет система, поэтому их не требуется создавать или инициализировать. Система предоставляет список подключенных геймпадов и событий и уведомляет о добавлении или удалении геймпада.

### <a name="the-gamepads-list"></a>Список геймпадов

Класс [Gamepad][] имеет статическое свойство [Gamepads][] — доступный только для чтения список подключенных геймпадов. Поскольку вам могут быть нужны лишь некоторые подключенные геймпады, рекомендуется создать собственную коллекцию, а не осуществлять доступ к ним через `Gamepads` свойства.

В следующем примере кода выполняется копирование всех подключенных геймпадов в новую коллекцию. Обратите внимание, поскольку других потоков в фоновом режиме будут иметь доступ к этой коллекции (в событиях [GamepadAdded][] и [GamepadRemoved][] ), необходимо заблокировать любой код, который считывает или обновляет коллекцию.

```cpp
auto myGamepads = ref new Vector<Gamepad^>();
critical_section myLock{};

for (auto gamepad : Gamepad::Gamepads)
{
    // Check if the gamepad is already in myGamepads; if it isn't, add it.
    critical_section::scoped_lock lock{ myLock };
    auto it = std::find(begin(myGamepads), end(myGamepads), gamepad);

    if (it == end(myGamepads))
    {
        // This code assumes that you're interested in all gamepads.
        myGamepads->Append(gamepad);
    }
}
```

```cs
private readonly object myLock = new object();
private List<Gamepad> myGamepads = new List<Gamepad>();
private Gamepad mainGamepad;

private void GetGamepads()
{
    lock (myLock)
    {
        foreach (var gamepad in Gamepad.Gamepads)
        {
            // Check if the gamepad is already in myGamepads; if it isn't, add it.
            bool gamepadInList = myGamepads.Contains(gamepad);

            if (!gamepadInList)
            {
                // This code assumes that you're interested in all gamepads.
                myGamepads.Add(gamepad);
            }
        }
    }   
}
```

### <a name="adding-and-removing-gamepads"></a>Добавление и удаление геймпадов

При добавлении или удалении геймпада, [GamepadAdded][] и [GamepadRemoved][] событий. Можно зарегистрировать обработчики для этих событий, чтобы отслеживать подключенные в данный момент геймпады.

Следующий пример кода запускает отслеживание добавленного геймпада.

```cpp
Gamepad::GamepadAdded += ref new EventHandler<Gamepad^>(Platform::Object^, Gamepad^ args)
{
    // Check if the just-added gamepad is already in myGamepads; if it isn't, add
    // it.
    critical_section::scoped_lock lock{ myLock };
    auto it = std::find(begin(myGamepads), end(myGamepads), args);

    if (it == end(myGamepads))
    {
        // This code assumes that you're interested in all new gamepads.
        myGamepads->Append(args);
    }
}
```

```cs
Gamepad.GamepadAdded += (object sender, Gamepad e) =>
{
    // Check if the just-added gamepad is already in myGamepads; if it isn't, add
    // it.
    lock (myLock)
    {
        bool gamepadInList = myGamepads.Contains(e);

        if (!gamepadInList)
        {
            myGamepads.Add(e);
        }
    }
};
```

Следующий пример кода останавливает отслеживание удаленного геймпада. Также необходимо обработать, что происходит с геймпадов, который вы отслеживаете после их удаления; Например, этот код только отслеживает входные данные с одного геймпада, а просто задает для него `nullptr` при удалении. Вам потребуется проверить каждый кадр, если активно вашего игрового контроллера и обновления, какие геймпада ввод от сбор, при контроллеров подключения и отключения.

```cpp
Gamepad::GamepadRemoved += ref new EventHandler<Gamepad^>(Platform::Object^, Gamepad^ args)
{
    unsigned int indexRemoved;
    critical_section::scoped_lock lock{ myLock };

    if(myGamepads->IndexOf(args, &indexRemoved))
    {
        if (m_gamepad == myGamepads->GetAt(indexRemoved))
        {
            m_gamepad = nullptr;
        }

        myGamepads->RemoveAt(indexRemoved);
    }
}
```

```cs
Gamepad.GamepadRemoved += (object sender, Gamepad e) =>
{
    lock (myLock)
    {
        int indexRemoved = myGamepads.IndexOf(e);

        if (indexRemoved > -1)
        {
            if (mainGamepad == myGamepads[indexRemoved])
            {
                mainGamepad = null;
            }

            myGamepads.RemoveAt(indexRemoved);
        }
    }
};
```

Дополнительные сведения см. [способы ввода в играх](input-practices-for-games.md) .

### <a name="users-and-headsets"></a>Пользователи и гарнитуры

Каждый геймпад можно связать с учетной записью пользователя, чтобы соотнести его личность с игровым процессом. Также к геймпаду можно подключить гарнитуру для использования голосового чата или других внутриигровых возможностей. Дополнительные сведения о работе с пользователями и гарнитурами см. в разделах [Отслеживание пользователей и их устройств](input-practices-for-games.md#tracking-users-and-their-devices) и [Гарнитура](headset.md).

## <a name="reading-the-gamepad"></a>Считывание данных с геймпада

После выбора необходимого геймпада можно приступить к сбору данных, вводимых с его помощью. В отличие от некоторых других типов вводимых данных, с которыми вы, возможно, уже знакомы, геймпады не передают сведения об изменении состояния путем создания событий. Вместо этого необходимо регулярно считывать их текущее состояние, _опрашивая_ соответствующие устройства.

### <a name="polling-the-gamepad"></a>Опрос геймпада

В ходе опроса сохраняется моментальный снимок устройства навигации в конкретный момент времени. Такой подход к сбору входных данных отлично подходит для большинства игр, потому что их логика, как правило, выполняется в детерминированном цикле и не зависит от событий; кроме того, проще интерпретировать игровые команды из входных данных, собранных в один момент, чем из многочисленных единичных входных данных, собранных за определенный период.

Опрос геймпада выполняется путем вызова функции [GetCurrentReading][], которая возвращает структуру [GamepadReading][], содержащую состояние геймпада.

В следующем примере кода выполняется опрос геймпада на предмет его текущего состояния.

```cpp
auto gamepad = myGamepads[0];

GamepadReading reading = gamepad->GetCurrentReading();
```

```cs
Gamepad gamepad = myGamepads[0];

GamepadReading reading = gamepad.GetCurrentReading();
```

Помимо состояния геймпада, считанные данные содержат метку времени, указывающую точное время получения сведений о состоянии. Метку времени удобно использовать для обращения ко времени предыдущего события считывания данных или ко времени моделирования конкретного игрового момента.

### <a name="reading-the-thumbsticks"></a>Считывание данных с аналоговых стиков

Каждый стик позволяет считывать с него аналоговые данные в диапазоне от -1,0 до +1,0 по осям X и Y. На оси X значение -1,0 соответствует крайнему левому положению стика, значение +1,0 — крайнему правому. На оси Y значение -1,0 соответствует крайнему нижнему положению стика, а значение +1,0 — крайнему верхнему. В обеих осей составляет приблизительно 0,0, когда манипулятор находится в центральном положении, но это точное значение может меняться даже между последующими показаниями; стратегии для снижения этого изменения рассматриваются далее в этом разделе.

Значение для оси X левого аналогового стика считывается из свойства `LeftThumbstickX` структуры [GamepadReading][], а значение для оси Y — из свойства `LeftThumbstickY`. Значение для оси X правого аналогового стика считывается из свойства `RightThumbstickX` структуры, а значение для оси Y — из свойства `RightThumbstickY`.

```cpp
float leftStickX = reading.LeftThumbstickX;   // returns a value between -1.0 and +1.0
float leftStickY = reading.LeftThumbstickY;   // returns a value between -1.0 and +1.0
float rightStickX = reading.RightThumbstickX; // returns a value between -1.0 and +1.0
float rightStickY = reading.RightThumbstickY; // returns a value between -1.0 and +1.0
```

```cs
double leftStickX = reading.LeftThumbstickX;   // returns a value between -1.0 and +1.0
double leftStickY = reading.LeftThumbstickY;   // returns a value between -1.0 and +1.0
double rightStickX = reading.RightThumbstickX; // returns a value between -1.0 and +1.0
double rightStickY = reading.RightThumbstickY; // returns a value between -1.0 and +1.0
```

При считывании значений аналогового стика можно заметить, что когда манипулятор находится в состоянии покоя в центральном положении, значения могут быть не равны 0,0. Вместо этого вы будете получать различные значения, близкие к 0,0, каждый раз, когда манипулятор перемещается и возвращается в центральное положение. Для устранения данной проблемы можно установить небольшую _мертвую зону_ — диапазон значений рядом с идеальным центральным положением, которые будут игнорироваться. Один из способов создания мертвой зоны — определить, насколько далеко от центра сдвинулся стик, и игнорировать смещения на расстояние, которое окажется меньше выбранного вами. Расстояние можно вычислить&mdash;они не являются точно, поскольку показания джойстика по сути полярные, а не плоскостные&mdash;с помощью теоремы Пифагора. В результате вы получите радиальную мертвую зону.

В следующем примере кода показан расчет базовой радиальной мертвой зоны с помощью теоремы Пифагора.

```cpp
float leftStickX = reading.LeftThumbstickX;   // returns a value between -1.0 and +1.0
float leftStickY = reading.LeftThumbstickY;   // returns a value between -1.0 and +1.0

// choose a deadzone -- readings inside this radius are ignored.
const float deadzoneRadius = 0.1;
const float deadzoneSquared = deadzoneRadius * deadzoneRadius;

// Pythagorean theorem -- for a right triangle, hypotenuse^2 = (opposite side)^2 + (adjacent side)^2
auto oppositeSquared = leftStickY * leftStickY;
auto adjacentSquared = leftStickX * leftStickX;

// accept and process input if true; otherwise, reject and ignore it.
if ((oppositeSquared + adjacentSquared) > deadzoneSquared)
{
    // input accepted, process it
}
```

```cs
double leftStickX = reading.LeftThumbstickX;   // returns a value between -1.0 and +1.0
double leftStickY = reading.LeftThumbstickY;   // returns a value between -1.0 and +1.0

// choose a deadzone -- readings inside this radius are ignored.
const double deadzoneRadius = 0.1;
const double deadzoneSquared = deadzoneRadius * deadzoneRadius;

// Pythagorean theorem -- for a right triangle, hypotenuse^2 = (opposite side)^2 + (adjacent side)^2
double oppositeSquared = leftStickY * leftStickY;
double adjacentSquared = leftStickX * leftStickX;

// accept and process input if true; otherwise, reject and ignore it.
if ((oppositeSquared + adjacentSquared) > deadzoneSquared)
{
    // input accepted, process it
}
```

Каждый аналоговый стик также действует как кнопка при нажатии вниз. Дополнительные сведения о чтении этих входных данных см. в разделе [Считывание данных с кнопок](#reading-the-buttons).

### <a name="reading-the-triggers"></a>Считывание данных с триггеров

Триггеры представлены в виде значений с плавающей запятой в диапазоне от 0,0 (полностью отпущены) до 1,0 (полностью нажаты). Значение левого триггера считывается из свойства `LeftTrigger` структуры [GamepadReading][], а значение правого триггера — из свойства `RightTrigger`.

```cpp
float leftTrigger  = reading.LeftTrigger;  // returns a value between 0.0 and 1.0
float rightTrigger = reading.RightTrigger; // returns a value between 0.0 and 1.0
```

```cs
double leftTrigger = reading.LeftTrigger;  // returns a value between 0.0 and 1.0
double rightTrigger = reading.RightTrigger; // returns a value between 0.0 and 1.0
```

### <a name="reading-the-buttons"></a>Считывание данных с кнопок

Все кнопки геймпада&mdash;— четыре направления крестовины, левый и правый бамперы, нажатие левого и правого аналогового стика, **A**, **B**, **X**, **Y**, **представления**и **меню**&mdash;предоставляет цифровыми чтения, Указывает, имеет ли он нажата (вниз) или отпущена (вверх). Для повышения эффективности показания кнопок не указываются в виде отдельных логических значений; Вместо этого все они упаковываются в одно битовое поле, представляемое перечислением [GamepadButtons][] .

<!-- > [!NOTE]
> The Xbox Elite Wireless Controller is equipped with four additional **paddle** buttons on its underside. These buttons are also represented in the `GamepadButtons` enumeration and their values are read in the same way as the standard gamepad buttons. -->

Значения кнопок считываются из свойства `Buttons` структуры [GamepadReading][]. Так как это свойство представляет собой битовое поле, для изоляции значения кнопки, сведения о которой вам интересны, используется побитовая маскировка. Кнопка нажата (вниз), когда установлен соответствующий бит; в противном случае кнопка отпущена (вверх).

Следующий пример кода определяет, нажата ли кнопка A.

```cpp
if (GamepadButtons::A == (reading.Buttons & GamepadButtons::A))
{
    // button A is pressed
}
```

```cs
if (GamepadButtons.A == (reading.Buttons & GamepadButtons.A))
{
    // button A is pressed
}
```

Следующий пример кода определяет, отпущена ли кнопка A.

```cpp
if (GamepadButtons::None == (reading.Buttons & GamepadButtons::A))
{
    // button A is released
}
```

```cs
if (GamepadButtons.None == (reading.Buttons & GamepadButtons.A))
{
    // button A is released
}
```

Иногда может потребоваться определить, когда кнопка переходит из нажатого состояния в отпущенное или отпущена узнать состояние сразу, ли несколько кнопок нажата или отпущена, упорядочен ли набор кнопок является определенным образом&mdash;некоторые нажаты, другие — нет. Сведения о том, как определить каждое из этих условий, см. в разделах [Определение положений кнопки](input-practices-for-games.md#detecting-button-transitions) и [Определение сложных схем положений кнопок](input-practices-for-games.md#detecting-complex-button-arrangements).

## <a name="run-the-gamepad-input-sample"></a>Пример чтения данных, вводимых с геймпада

Пример [GamepadUWP sample _(github)_](https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/UWPSamples/System/GamepadUWP) показывает, как подключиться к геймпаду и считать его состояние.

## <a name="vibration-and-impulse-triggers-overview"></a>Обзор вибрации и импульсных триггеров

Вибромоторы внутри геймпада предназначены для предоставления пользователю тактильной обратной связи. В играх эта возможность используется для усиления эффекта погружения, для передачи информации о состоянии (например, получение повреждений), для предупреждения о приближении к важным объектам и в иных целях.

Геймпады Xbox One оснащены четырьмя независимыми вибромоторами. Два являются больших моторов, расположенных в теле геймпада; левый мотор предоставляет грубый, амплитуда высокой вибрации, а правый предоставляет позволяет получить более плавную, более блеклыми вибрацию. Два других маленьких мотора располагаются в каждом триггере. Они обеспечивают резкую вибрацию, воспринимаемую пальцами пользователя на триггере. Благодаря этой уникальной функции триггеры геймпадов Xbox One называются _импульсными триггерами_. Управляя этими моторами, можно создавать широкий спектр тактильных ощущений.

## <a name="using-vibration-and-impulse"></a>Использование вибрации и импульсов

Вибрация геймпада управляется с помощью свойства [Vibration][] класса [Gamepad][]. `Vibration` — это экземпляр структуры [GamepadVibration][], которая состоит из четырех значений с плавающей запятой. Каждое значение представляет интенсивность работы одного их моторов.

Несмотря на то что члены `Gamepad.Vibration` свойство можно изменить напрямую, рекомендуется инициализации отдельного `GamepadVibration` экземпляр значения, вы хотите, а затем скопировать его в `Gamepad.Vibration` свойство одновременно изменить фактическое моторов.

В следующем примере показано, как одновременно изменить интенсивность работы всех моторов.

```cpp
// get the first gamepad
Gamepad^ gamepad = Gamepad::Gamepads->GetAt(0);

// create an instance of GamepadVibration
GamepadVibration vibration;

// ... set vibration levels on vibration struct here

// copy the GamepadVibration struct to the gamepad
gamepad.Vibration = vibration;
```

```cs
// get the first gamepad
Gamepad gamepad = Gamepad.Gamepads[0];

// create an instance of GamepadVibration
GamepadVibration vibration = new GamepadVibration();

// ... set vibration levels on vibration struct here

// copy the GamepadVibration struct to the gamepad
gamepad.Vibration = vibration;
```

### <a name="using-the-vibration-motors"></a>Использование вибромоторов

Левый и правый вибромоторы получают значения с плавающей запятой от 0,0 (без вибрации) до 1,0 (наиболее сильная вибрация). Интенсивность работы левого мотора задается с помощью свойства `LeftMotor` структуры [GamepadVibration][], а интенсивность работы правого мотора — с помощью свойства `RightMotor`.

В следующем примере задается интенсивность работы обоих вибромоторов и активируется вибрация геймпада.

```cpp
GamepadVibration vibration;
vibration.LeftMotor = 0.80;  // sets the intensity of the left motor to 80%
vibration.RightMotor = 0.25; // sets the intensity of the right motor to 25%
gamepad.Vibration = vibration;
```

```cs
GamepadVibration vibration = new GamepadVibration();
vibration.LeftMotor = 0.80;  // sets the intensity of the left motor to 80%
vibration.RightMotor = 0.25; // sets the intensity of the right motor to 25%
mainGamepad.Vibration = vibration;
```

Помните, что эти два мотора не идентичны, и установка одинаковых значений для этих свойств не приведет к возникновению одинакового уровня вибрации в этих моторах. При любых значениях левый мотор создает сильную вибрацию более низкой частотой, чем мотор вправо, который&mdash;же значении&mdash;создает позволяет получить более плавную вибрацию с более высокой частотой. Даже при максимальных значениях левый мотор не сможет обеспечить такую же высокую частоту вибрации, как правый, а правый — не сможет обеспечить такую же сильную вибрацию, как левый. При этом, так как моторы жестко прикреплены к корпусу геймпада, игроки не воспринимают вибрацию как идущую от двух разных источников, несмотря на то, что моторы имеют различные характеристики и вибрируют с разной интенсивностью. Это позволяет получить более широкий диапазон ощущений, чем в случае использования одинаковых моторов.

### <a name="using-the-impulse-triggers"></a>Использование импульсных триггеров

Мотор каждого импульсного триггера получает значение с плавающей запятой от 0,0 (без вибрации) до 1,0 (наиболее сильная вибрация). Интенсивность мотора левого триггера задается с помощью свойства `LeftTrigger` структуры [GamepadVibration][], интенсивность мотора правого триггера — с помощью свойства `RightTrigger`.

В следующем примере задается интенсивность обоих импульсных триггеров и выполняется их активация.

```cpp
GamepadVibration vibration;
vibration.LeftTrigger = 0.75;  // sets the intensity of the left trigger to 75%
vibration.RightTrigger = 0.50; // sets the intensity of the right trigger to 50%
gamepad.Vibration = vibration;
```

```cs
GamepadVibration vibration = new GamepadVibration();
vibration.LeftTrigger = 0.75;  // sets the intensity of the left trigger to 75%
vibration.RightTrigger = 0.50; // sets the intensity of the right trigger to 50%
mainGamepad.Vibration = vibration;
```

В отличие от первых двух моторов, эти два вибромотора внутри триггеров идентичны друг другу, и установка одинаковых значений приведет к одинаковой вибрации этих моторов. Однако, так как эти моторы не имеют жесткого сцепления друг с другом, игроки чувствуют их вибрацию независимо. Это позволяет одновременно создавать с помощью обоих триггеров полностью независимые ощущения и помогает им передавать более подробную информацию, чем та информация, которую передают моторы в корпусе геймпада.

## <a name="run-the-gamepad-vibration-sample"></a>Пример вибрации геймпада

Пример [GamepadVibrationUWP sample _(github)_](https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/UWPSamples/System/GamepadVibrationUWP) показывает, как вибромоторы и импульсные триггеры геймпада создают различные эффекты.

## <a name="see-also"></a>См. также

* [Windows.Gaming.Input.UINavigationController][]
* [Windows.Gaming.Input.IGameController][]
* [Рекомендации по использованию устройств ввода для игр](input-practices-for-games.md)

[Windows.Gaming.Input]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.aspx
[Windows.Gaming.Input.UINavigationController]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.uinavigationcontroller.aspx
[Windows.Gaming.Input.IGameController]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.aspx
[gamepad]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepad.aspx
[gamepads]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepad.gamepads.aspx
[gamepadadded]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepad.gamepadadded.aspx
[gamepadremoved]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepad.gamepadremoved.aspx
[getcurrentreading]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepad.getcurrentreading.aspx
[vibration]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepad.vibration.aspx
[gamepadreading]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepadreading.aspx
[gamepadbuttons]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepadbuttons.aspx
[gamepadvibration]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.gamepadvibration.aspx
