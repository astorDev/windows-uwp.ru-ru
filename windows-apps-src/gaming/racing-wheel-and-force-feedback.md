---
author: eliotcowley
title: Гоночный руль и обратная связь по усилию
description: Используйте API-интерфейсы Windows.Gaming.Input гоночного руля для обнаружения гоночных рулей и определения их возможностей, а также считывания с гоночных рулей данных и отправки на них команд обратной связи по усилию.
ms.assetid: 6287D87F-6F2E-4B67-9E82-3D6E51CBAFF9
ms.author: wdg-dev-content
ms.date: 05/09/2018
ms.topic: article
keywords: Windows 10, uwp, игры, гоночный руль, обратная связь по усилию
ms.localizationpriority: medium
ms.openlocfilehash: 20b4b35bb729ee49dbfd3f2b2b2a029a4319521c
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6029940"
---
# <a name="racing-wheel-and-force-feedback"></a>Гоночный руль и обратная связь по усилию

На этой странице приведены основные принципы программирования для гоночных рулей Xbox One с помощью API-интерфейса [Windows.Gaming.Input.RacingWheel][] и других API для универсальной платформы Windows (UWP).

Прочитав эту страницу, вы узнаете:

* как составить список подключенных гоночных рулей и их пользователей;
* как определить, что гоночный руль был добавлен или удален;
* как считывать входные данные с одного или нескольких гоночных рулей;
* как отправлять команды обратной связи по усилию;
* принципы работы гоночных рулей в качестве устройств для навигации по пользовательскому интерфейсу.

## <a name="racing-wheel-overview"></a>Обзор гоночного руля

Гоночные рули— это устройства ввода, которые позволяют ощутить эффект присутствия в кабине настоящего гоночного автомобиля. Гоночные рули— это идеальные устройства ввода как для аркадных гоночных игр, так и для гоночных симуляторов легковых или грузовых автомобилей. Поддержка гоночных рулей в приложениях UWP для Windows 10 и Xbox One реализована с помощью пространства имен [Windows.Gaming.Input][].

Гоночные рули для Xbox One доступны в разных ценовых категориях, а дорогие модели, как правило, обладают большим количеством улучшенных возможностей ввода и обратной связи по усилию. Все гоночные рули оснащаются аналоговым рулевым колесом, аналоговыми элементами управления газом и тормозом, а также кнопками, расположенными на руле. Некоторые гоночные рули, также оснащаются аналоговыми элементами управления сцеплением и ручным тормозом, рычагом переключения передач, а также поддерживают обратную связь по усилию. Не все гоночные рули оснащаются одинаковым набором функций, а также отличаются друг от друга наличием или отсутствием поддержки определенных возможностей. Например, рулевые колеса могут поддерживать разные диапазоны вращения, а рычаги переключения передач— разное количество передач.

### <a name="device-capabilities"></a>Возможности устройства

Разные гоночные рули для Xbox One предлагают разные дополнительные возможности, а также разные уровни поддержки этих возможностей. Эта степень разнообразия для одного типа устройства ввода является уникальной для устройств с поддержкой API-интерфейсов [Windows.Gaming.Input][]. Кроме того, большинство устройств, с которыми вам придется работать, будут поддерживать по крайней мере некоторые дополнительные возможности или другой функционал. По этой причине важно определить возможности каждого подключенного гоночного руля и обеспечить поддержку всего разнообразия возможностей, которые могут быть реализованы в вашей игре.

Дополнительные сведения см. в разделе [Определение возможностей гоночного руля](#determining-racing-wheel-capabilities).

### <a name="force-feedback"></a>Обратная связь по усилию

Некоторые гоночные рули Xbox One поддерживают полноценную обратную связь по усилию, то есть эти устройства могут применять реальную силу к оси элемента управления (например, к рулевому колесу), а не обычную вибрацию. В играх эта возможность используется для более глубокого погружения в игровой процесс (_имитация повреждения при столкновении_, реалистичное ощущение езды), за счет чего сложность управления автомобилем также повышается.

Дополнительные сведения см. в разделе [Обзор обратной связи по усилию](#force-feedback-overview).

### <a name="ui-navigation"></a>Навигация в пользовательском интерфейсе

Чтобы облегчить задачу обеспечения поддержки разных устройств ввода для навигации в пользовательском интерфейсе и сохранить единообразие используемых для этого элементов управления в разных играх и на разных устройствах, большинство _физических_ устройств ввода параллельно выполняют функцию отдельного _логического_ устройства ввода под названием [контроллер навигации в пользовательском интерфейсе](ui-navigation-controller.md). Контроллер навигации в пользовательском интерфейсе предоставляет стандартный набор элементов управления для команд навигации в пользовательском интерфейсе на разных устройствах ввода.

В силу того, что способ управления в них продиктован использованием исключительно аналоговых средств управления, и в силу некоторой степени различий между разными гоночными рулями, они, как правило, оснащаются цифровой крестовиной, кнопками **View (Вид)**, **Menu (Меню)**, **A**, **B**, **X** и **Y**, напоминающими кнопки на [геймпаде](gamepad-and-vibration.md). Эти кнопки изначально не предназначены для выполнения игровых команд и возможности их использования в качестве кнопок гоночного руля ограничены.

Если рассматривать гоночный руль в качестве контроллера навигации в пользовательском интерфейсе, то в нем реализован [необходимый набор](ui-navigation-controller.md#required-set) навигационных команд, заданных левому джойстику, крестовине, кнопкам **View**, **Menu**, **A** и **B**.

| Команды навигации | Кнопки на гоночном руле |
| ------------------:| ------------------ |
|                 Вверх | Крестовина вверх           |
|               Вниз | Крестовина вниз         |
|               Влево | Крестовина влево         |
|              Вправо | Крестовина вправо        |
|               Просмотр | Кнопка просмотра        |
|               Меню | Кнопка меню        |
|             Принять | Кнопка A           |
|             Отмена | Кнопка "B"           |

Кроме того, в некоторых гоночных рулях может быть реализовано сопоставление некоторых [дополнительных наборов](ui-navigation-controller.md#optional-set) навигационных команд с другими поддерживаемыми ими типами ввода, однако соответствие этих команд друг другу может быть разным на различных устройствах. Рассмотрите возможность реализации поддержки в том числе и этих команд, сначала убедившись в том, что они не будут использоваться в качестве важных команд для навигации в интерфейсе вашей игры.

| Команды навигации | Кнопки на гоночном руле    |
| ------------------:| --------------------- |
|            PAGE UP | _Могут быть разными_              |
|          PAGE DOWN | _Могут быть разными_              |
|          PAGE LEFT | _Могут быть разными_              |
|         PAGE RIGHT | _Могут быть разными_              |
|          Прокрутка вверх | _Могут быть разными_              |
|        Прокрутка вниз | _Могут быть разными_              |
|        Прокрутка влево | _Могут быть разными_              |
|       Прокрутить вправо | _Могут быть разными_              |
|          Контекстный вызов 1 | Кнопка "X" (_как правило_) |
|          Контекстный вызов 2 | Кнопка "Y" (_как правило_) |
|          Контекстный вызов 3 | _Могут быть разными_              |
|          Контекстный вызов 4 | _Могут быть разными_              |

## <a name="detect-and-track-racing-wheels"></a>Обнаружение и отслеживание гоночных рулей

Обнаружение и отслеживание гоночных рулей происходит точно так же, как в случае геймпадов, за тем исключением, что вместо класса [Gamepad](https://docs.microsoft.com/uwp/api/Windows.Gaming.Input.Gamepad) используется класс [RacingWheel][]. Подробнее см. в статье [Геймпад и вибрация](gamepad-and-vibration.md).

<!-- Racing wheels are managed by the system, therefore you don't have to create or initialize them. The system provides a list of connected racing wheels and events to notify you when a racing wheel is added or removed.

### The racing wheels list

The [RacingWheel][] class provides a static property, [RacingWheels][], which is a read-only list of racing wheels that are currently connected. Because you might only be interested in some of the connected racing wheels, it's recommended that you maintain your own collection instead of accessing them through the `RacingWheels` property.

The following example copies all connected racing wheels into a new collection.
```cpp
auto myRacingWheels = ref new Vector<RacingWheel^>();

for (auto racingwheel : RacingWheel::RacingWheels)
{
    // This code assumes that you're interested in all racing wheels.
    myRacingWheels->Append(racingwheel);
}
```

### Adding and removing racing wheels

When a racing wheel is added or removed the [RacingWheelAdded][] and [RacingWheelRemoved][] events are raised. You can register handlers for these events to keep track of the racing wheels that are currently connected.

The following example starts tracking an racing wheels that's been added.
```cpp
RacingWheel::RacingWheelAdded += ref new EventHandler<RacingWheel^>(Platform::Object^, RacingWheel^ args)
{
    // This code assumes that you're interested in all new racing wheels.
    myRacingWheels->Append(args);
}
```

The following example stops tracking a racing wheel that's been removed.
```cpp
RacingWheel::RacingWheelRemoved += ref new EventHandler<RacingWheel^>(Platform::Object^, RacingWheel^ args)
{
    unsigned int indexRemoved;

    if(myRacingWheels->IndexOf(args, &indexRemoved))
    {
        myRacingWheels->RemoveAt(indexRemoved);
    }
}
```

### Users and headsets

Each racing wheel can be associated with a user account to link their identity to their gameplay, and can have a headset attached to facilitate voice chat or in-game features. To learn more about working with users and headsets, see [Tracking users and their devices](input-practices-for-games.md#tracking-users-and-their-devices) and [Headset](headset.md). -->

## <a name="reading-the-racing-wheel"></a>Считывание данных с гоночного руля

После выбора гоночных рулей, с которыми вы хотите работать, можно приступить к сбору данных, вводимых с помощью рулей. Тем не менее, в отличие от некоторых других типов вводимых данных, к которым вы уже могли привыкнуть, гоночные рули не передают данные об изменении состояния путем создания событий. Вместо этого для получения стандартной информации об их текущем состоянии вы проводите _опрос_.

### <a name="polling-the-racing-wheel"></a>Опрос гоночного руля

В ходе опроса сохраняется моментальный снимок гоночного руля в конкретный момент времени. Такой подход к сбору входных данных отлично подходит для большинства игр, потому что их логика, как правило, выполняется в детерминированном цикле и не зависит от событий; кроме того, проще интерпретировать игровые команды из входных данных, собранных в один момент, чем из многочисленных единичных входных данных, собранных за определенный период.

Опрашивание гоночного руля выполняется путем вызова функции [GetCurrentReading][], которая возвращает структуру [RacingWheelReading][], содержащую состояние гоночного руля.

В следующем примере кода выполняется опрос гоночного руля на предмет его текущего состояния.

```cpp
auto racingwheel = myRacingWheels[0];

RacingWheelReading reading = racingwheel->GetCurrentReading();
```

Помимо состояния гоночного руля считанные данные содержат метку времени, указывающую точное время извлечения сведений о состоянии. Метку времени удобно использовать для обращения ко времени предыдущего события считывания данных или ко времени моделирования конкретного игрового момента.

### <a name="determining-racing-wheel-capabilities"></a>Определение возможностей гоночного руля

Многие элементы управления гоночного руля являются дополнительными (необязательными) или поддерживают различные варианты использования даже применительно к основным (обязательным) элементам управления, поэтому перед тем как можно будет обработать данные ввода, собранные в ходе каждого события считывания данных с гоночного руля, вам необходимо определить возможности каждого гоночного руля по отдельности.

К дополнительным элементам управления относятся ручной тормоз, сцепление и рычаг переключения передач. Вы можете определить, поддерживает ли подключенный руль эти элементы управления, выполнив считывание свойств [HasHandbrake][], [HasClutch][] и [HasPatternShifter][] гоночного руля. Элемент управления поддерживается, если значением свойства является **true**; в противном случае поддержка отсутствует.

```cpp
if (racingwheel->HasHandbrake)
{
    // the handbrake is supported
}

if (racingwheel->HasClutch)
{
    // the clutch is supported
}

if (racingwheel->HasPatternShifter)
{
    // the pattern shifter is supported
}
```

Кроме того, к элементам управления, характеристики которых могут различаться, относятся рулевое колесо и рычаг переключения передач. У рулевых колес могут быть разные углы физического поворота, поддерживаемые фактическим рулевым колесом, а у рычагов переключения передач может быть разное количество передач переднего хода. Вы можете определить наибольший угол поворота, поддерживаемый фактическим рулевым колесом, выполнив считывание свойства `MaxWheelAngle` гоночного руля. Это значение является максимальным поддерживаемым физическим углом поворота в градусах по часовой стрелке (положительная степень в градусах), которое аналогичным образом поддерживается в направлении против часовой стрелки (отрицательная степень в градусах). Вы можете определить максимальную переднюю передачу, поддерживаемую рычагом переключения передач, выполнив считывание свойства `MaxPatternShifterGear` гоночного руля. Это значение является самой высокой поддерживаемой передачей. То есть, если значение равно 4, это значит, что рычаг переключения передач поддерживает задний ход, нейтральную передачу, первую, вторую, третью и четвертую передачи.

```cpp
auto maxWheelDegrees = racingwheel->MaxWheelAngle;
auto maxShifterGears = racingwheel->MaxPatternShifterGear;
```

Наконец, некоторые гоночные рули поддерживают обратную связь по усилию на рулевом колесе. Вы можете определить, поддерживает ли подключенный гоночный руль обратную связь по усилию, выполнив считывание свойства [WheelMotor][] гоночного руля. Обратная связь по усилию поддерживается, если значение `WheelMotor` не равно **нулю**; в противном случае поддержка отсутствует.

```cpp
if (racingwheel->WheelMotor != nullptr)
{
    // force feedback is supported
}
```

Сведения о том, как использовать возможность обратной связи по усилию гоночных рулей, поддерживающих ее, см. в разделе [Обзор обратной связи по усилию](#force-feedback-overview).

### <a name="reading-the-buttons"></a>Чтение кнопок

Все кнопки гоночного руля— четыре направления крестовины, **Previous Gear** (предыдущая передача) и **Next Gear** (следующая передача), а также 16дополнительных кнопок— предоставляют цифровые данные, указывающие на то, нажата ли кнопка (вниз) или отпущена (вверх). В целях обеспечения эффективности эти показания кнопок не указываются в виде отдельных логических значений. Вместо этого все они упаковываются в одно битовое поле, представляемое перечислением [RacingWheelButtons][].

> [!NOTE]
> Гоночные рули оснащаются дополнительными кнопками, используемыми для навигации в пользовательском интерфейсе, такими как кнопки **View** и **Menu**. Эти кнопки не являются частью перечисления `RacingWheelButtons` и данные с них можно считать, только если войти в гоночный руль как в устройство навигации в пользовательском интерфейсе. Дополнительные сведения см. в разделе [Устройство навигации в пользовательском интерфейсе](ui-navigation-controller.md).

Значения кнопок считываются из свойства `Buttons` структуры [RacingWheelReading][]. Так как это свойство представляет собой битовое поле, для изоляции значения кнопки, сведения о которой вам интересны, используется побитовая маскировка. Кнопка нажата (вниз), когда установлен соответствующий бит; в противном случае кнопка отпущена (вверх).

Следующий пример кода определяет, нажата ли кнопка **Next Gear**.

```cpp
if (RacingWheelButtons::NextGear == (reading.Buttons & RacingWheelButtons::NextGear))
{
    // Next Gear is pressed
}
```

Следующий пример кода определяет, отпущена ли кнопка "Next Gear".

```cpp
if (RacingWheelButtons::None == (reading.Buttons & RacingWheelButtons::NextGear))
{
    // Next Gear is released (not pressed)
}
```

Иногда нужно определить, когда кнопка переходит из нажатого состояния в отпущенное или наоборот, узнать состояние сразу нескольких кнопок (нажаты или отпущены) либо определить особую схему расположения кнопок в наборе — некоторые кнопки могут быть нажаты, а другие отпущены. Сведения о том, как определить эти условия, см. в разделах [Определение положений кнопки](input-practices-for-games.md#detecting-button-transitions) и [Определение сложных схем положений кнопок](input-practices-for-games.md#detecting-complex-button-arrangements).

### <a name="reading-the-wheel"></a>Считывание данных с рулевого колеса

Рулевое колесо является основным элементом управления и позволяет считывать с него аналоговые данные в диапазоне от -1,0 до +1,0. Значение, равное -1,0, соответствует крайнему левому положению рулевого колеса; значение +1,0 соответствует крайнему правому положению. Значение положения рулевого колеса считывается из свойства `Wheel` структуры [RacingWheelReading][].

```cpp
float wheel = reading.Wheel;  // returns a value between -1.0 and +1.0.
```

Несмотря на то, что показания рулевого колеса соответствуют разным углам физического поворота фактического рулевого колеса в зависимости от диапазона вращения, поддерживаемого физическим гоночным рулем, масштабировать показания рулевого колеса, как правило, не требуется. Рулевые колеса, поддерживающие большие углы вращения, просто обеспечивают более высокую точность показаний.

### <a name="reading-the-throttle-and-brake"></a>Считывание данных с элементов управления газом и тормозом

Газ и тормоз являются основными элементами управления и позволяют считывать с них аналоговые данные в диапазоне от 0,0 (полностью отпущен) до 1,0 (полностью нажат), представляемые значениями с плавающей запятой. Значение элемента управления газом считывается из свойства `Throttle` структуры [RacingWheelReading][], а значение элемента управления тормозом считывается из свойства `Brake`.

```cpp
float throttle = reading.Throttle;  // returns a value between 0.0 and 1.0
float brake    = reading.Brake;     // returns a value between 0.0 and 1.0
```

### <a name="reading-the-handbrake-and-clutch"></a>Считывание данных с элементов управления ручным тормозом и сцеплением

Ручной тормоз и сцепление являются дополнительными элементами управления и позволяют считывать с них аналоговые данные в диапазоне от 0,0 (полностью отпущен) до 1,0 (полностью нажат), представляемые значениями с плавающей запятой. Значение элемента управления ручным тормозом считывается из свойства `Handbrake` структуры [RacingWheelReading][], а значение элемента управления сцеплением считывается из свойства `Clutch`.

```cpp
float handbrake = 0.0;
float clutch = 0.0;

if(racingwheel->HasHandbrake)
{
    handbrake = reading.Handbrake;  // returns a value between 0.0 and 1.0
}

if(racingwheel->HasClutch)
{
    clutch = reading.Clutch;        // returns a value between 0.0 and 1.0
}
```

### <a name="reading-the-pattern-shifter"></a>Считывание данных с рычага переключения передач

Рычаг переключения передач является дополнительным элементом управления и позволяет считывать с него цифровые данные в диапазоне от -1 до [MaxPatternShifterGear][], представляемые целым значением со знаком. Значение, равное -1 или 0, соответствует _заднему ходу_ и _нейтральной_ передаче соответственно; увеличивающиеся положительные значения соответствуют более высоким передним передачам до значения [MaxPatternShifterGear][] включительно. Значение рычага переключения передач считывается из свойства `PatternShifterGear` структуры [RacingWheelReading][].

```cpp
if (racingwheel->HasPatternShifter)
{
    gear = reading.PatternShifterGear;
}
```

> [!NOTE]
> Рычаг переключения передач (если поддерживается) размещается рядом с основными кнопками **Предыдущей передачи** и **Следующей передачи**, что также влияет на текущую включенную передачу автомобиля игрока. Простой стратегией унификации использования этих устройств ввода, когда оба из них имеются в наличии, будет игнорирование рычага переключения передач (и сцепления) при выборе игроком автоматической коробки переключения передач для своего автомобиля, а также игнорирование кнопки **Предыдущей** и **Следующей передачи** при выборе игроком ручной коробки переключения передач для своего автомобиля, только если гоночный руль игрока оснащен элементом управления рычагом переключения передач. Если такая стратегия не подходит для вашей игры, можно реализовать другую стратегию унификации.

## <a name="run-the-inputinterfacing-sample"></a>Запуск примера кода InputInterfacing

В примере кода [InputInterfacingUWP _(в центре GitHub)_](https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/Samples/System/InputInterfacingUWP) показано, как использовать гоночные рули и разные типы устройств ввода вместе, а также представлено, как эти устройства ввода работают в качестве контроллеров навигации в пользовательском интерфейсе.

## <a name="force-feedback-overview"></a>Обзор обратной связи по усилию

Многие гоночные рули поддерживают обратную связь по усилию, обеспечивающую более глубокое погружение в игру, и усложняя процесс вождения, делая его более реалистичным. Гоночные рули, поддерживающие обратную связь по усилию, как правило, оснащены одним моторчиком, который применяет усилие к рулевому колесу вдоль одной оси, а именно оси вращения колеса. Поддержка обратной связи по усилию в приложениях UWP для Windows 10 и Xbox One реализована с помощью пространства имен [Windows.Gaming.Input.ForceFeedback][].

> [!NOTE]
> API-интерфейсы обратной связи по усилию могут поддерживать работу с несколькими осями усилия, однако в настоящий момент все гоночные рули для Xbox One поддерживают обратную связь по усилию применительно только к оси вращения рулевого колеса.

## <a name="using-force-feedback"></a>Использование обратной связи по усилию

В этих разделах приведены основы программирования эффектов обратной связи по усилию для гоночных рулей Xbox One. Обратная связь обеспечивается с помощью эффектов, которые сначала загружаются в устройство обратной связи по усилию. После этого их работу можно запускать, приостанавливать, возобновлять и останавливать подобно звуковым эффектам. Однако сначала необходимо определить возможности обратной связи гоночного руля.

### <a name="determining-force-feedback-capabilities"></a>Определение возможностей обратной связи по усилию

Вы можете определить, поддерживает ли подключенный гоночный руль обратную связь по усилию, выполнив считывание свойства [WheelMotor][] гоночного руля. Обратная связь по усилию не поддерживается, если свойство `WheelMotor` имеет значение **ноль**. В противном случае обратная связь по усилию поддерживается и вы можете определить конкретные возможности обратной связи моторчика (например, оси, на которые он воздействует).

```cpp
if (racingwheel->WheelMotor != nullptr)
{
    auto axes = racingwheel->WheelMotor->SupportedAxes;

    if(ForceFeedbackEffectAxes::X == (axes & ForceFeedbackEffectAxes::X))
    {
        // Force can be applied through the X axis
    }

    if(ForceFeedbackEffectAxes::Y == (axes & ForceFeedbackEffectAxes::Y))
    {
        // Force can be applied through the Y axis
    }

    if(ForceFeedbackEffectAxes::Z == (axes & ForceFeedbackEffectAxes::Z))
    {
        // Force can be applied through the Z axis
    }
}
```

### <a name="loading-force-feedback-effects"></a>Загрузка эффектов обратной связи по усилию

Эффекты обратной связи по усилию загружаются в устройство обратной связи, в котором они в "автономном режиме" воспроизводятся по команде игры. Доступен ряд основных эффектов. Пользовательские эффекты можно создать с помощью класса, реализующего интерфейс [IForceFeedbackEffect][].

| Класс эффекта         | Описание эффекта                                                                     |
| -------------------- | -------------------------------------------------------------------------------------- |
| ConditionForceEffect | Эффект, который применяет переменное усилие в ответ на текущие показания датчика, встроенного в устройство. |
| ConstantForceEffect  | Эффект, который применяет постоянное усилие вдоль вектора.                                  |
| PeriodicForceEffect  | Эффект, который применяет вдоль вектора переменное усилие, определяемое формой волны.           |
| RampForceEffect      | Эффект, который применяет линейно возрастающее/уменьшающееся усилие вдоль вектора.          |

```cpp
using FFLoadEffectResult = ForceFeedback::ForceFeedbackLoadEffectResult;

auto effect = ref new Windows.Gaming::Input::ForceFeedback::ConstantForceEffect();
auto time = TimeSpan(10000);

effect->SetParameters(Windows::Foundation::Numerics::float3(1.0f, 0.0f, 0.0f), time);

// Here, we assume 'racingwheel' is valid and supports force feedback

IAsyncOperation<FFLoadEffectResult>^ request
    = racingwheel->WheelMotor->LoadEffectAsync(effect);

auto loadEffectTask = Concurrency::create_task(request);

loadEffectTask.then([this](FFLoadEffectResult result)
{
    if (FFLoadEffectResult::Succeeded == result)
    {
        // effect successfully loaded
    }
    else
    {
        // effect failed to load
    }
}).wait();
```

### <a name="using-force-feedback-effects"></a>Использование эффектов обратной связи по усилию

После загрузки работу эффектов можно синхронно запускать, приостанавливать, возобновлять и останавливать путем вызова функций в свойстве `WheelMotor` гоночного руля либо по отдельности путем вызова функций непосредственно в самом эффекте обратной связи. Как правило, все эффекты, которые требуется использовать на устройстве обратной связи, необходимо загрузить до начала игры, а затем использовать соответствующие им функции `SetParameters` для обновления эффектов в ходе игры.

```cpp
if (ForceFeedbackEffectState::Running == effect->State)
{
    effect->Stop();
}
else
{
    effect->Start();
}
```

Наконец, когда это необходимо, можно асинхронно включать, отключать или перезагружать всю систему обратной связи по усилию на конкретном гоночном руле.

## <a name="see-also"></a>См. также

* [Windows.Gaming.Input.UINavigationController][]
* [Windows.Gaming.Input.IGameController][]
* [Рекомендации по использованию устройств ввода для игр](input-practices-for-games.md)

[Windows.Gaming.Input]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.aspx
[Windows.Gaming.Input.UINavigationController]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.uinavigationcontroller.aspx
[Windows.Gaming.Input.IGameController]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.aspx
[racingwheel]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.aspx
[racingwheels]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.racingwheels.aspx
[racingwheeladded]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.racingwheeladded.aspx
[racingwheelremoved]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.racingwheelremoved.aspx
[haspatternshifter]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.haspatternshifter.aspx
[hashandbrake]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.hashandbrake.aspx
[hasclutch]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.hasclutch.aspx
[maxpatternshiftergear]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.maxpatternshiftergear.aspx
[maxwheelangle]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.maxwheelangle.aspx
[getcurrentreading]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.getcurrentreading.aspx
[wheelmotor]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheel.wheelmotor.aspx
[racingwheelreading]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheelreading.aspx
[racingwheelbuttons]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.racingwheelbuttons.aspx
