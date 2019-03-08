---
title: Аркадный джойстик
description: Для обнаружения аркадных джойстиков и чтения их данных используйте API аркадных джойстиков Windows.Gaming.Input.
ms.assetid: 2E52232F-3014-4C8C-B39D-FAC478BA3E01
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, аркадный джойстик, ввод
ms.localizationpriority: medium
ms.openlocfilehash: 6f9e3ff29dfb17b6e2a07df52153013b5266206e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57593149"
---
# <a name="arcade-stick"></a>Аркадный джойстик

На этой странице приведены основные принципы программирования для аркадных джойстиков Xbox One с помощью [Windows.Gaming.Input.ArcadeStick][arcadestick] и связанных API-интерфейсов для универсальной платформы Windows (UWP).

Изучив информацию на этой странице, вы узнаете:

* как составить список подключенных аркадных джойстиков и их пользователей;
* как определить, что аркадный джойстик был добавлен или удален;
* как считывать входные данные с одного или нескольких аркадных джойстиков;
* как вести arcade фиксируется как устройства навигации пользовательского интерфейса

## <a name="arcade-stick-overview"></a>Обзор аркадного джойстика

Аркадные джойстики — это устройства ввода, позволяющие передать ощущение игры на стационарном игровом автомате и обладающие высокоточными цифровыми элементами управления. Аркадные джойстики являются идеальным устройством ввода для файтингов и других аркадных игр, а также подходят для любой игры, в которой используются полностью цифровые элементы управления. Поддержка аркадных джойстиков в приложениях UWP для Windows 10 и Xbox One реализована с помощью пространства имен [Windows.Gaming.Input][].

Устройства Xbox One arcade оснащены 8-процессорный цифровой джойстик, шесть **действие** кнопок (в виде A1 A6 на рисунке ниже), а также два **специальные** кнопок (в виде S1 и S2); они ALL-digital устройств ввода, которые не поддерживают аналогового элементы управления или вибрацией. Устройства Xbox One arcade также оснащены **представление** и **меню** кнопки, используемый для поддержки навигации пользовательского интерфейса, но они не предназначены для поддержки команд игровой процесс и не может использоваться в легко джойстика кнопки .

![Аркадные палочка с джойстика концами 4, 6 кнопки действий (A1 A6) и 2 специальные кнопки (S1 и S2)](images/arcade-stick-1.png)

### <a name="ui-navigation"></a>Навигация в пользовательском интерфейсе

Чтобы облегчить задачу обеспечения поддержки множества разных устройств ввода для навигации по пользовательскому интерфейсу и сохранить единообразие используемых для этого элементов управления в разных играх и на разных устройствах, большинство _физических_ устройств ввода параллельно выполняют функцию отдельного _логического_ устройства ввода под названием [контроллер навигации по пользовательскому интерфейсу](ui-navigation-controller.md). Контроллер навигации в пользовательском интерфейсе предоставляет стандартный набор элементов управления для команд навигации в пользовательском интерфейсе на разных устройствах ввода.

Как контроллер навигации пользовательского интерфейса arcade придерживается этого же карты [требуется набор](ui-navigation-controller.md#required-set) команд навигации джойстик и **представление**, **меню**, **действием 1**, и **действиями 2** кнопки.

| Команда навигации | Ввод с аркадного джойстика  |
| ------------------:| ------------------- |
|                 Вверх | Стик вверх            |
|               Вниз | Стик вниз          |
|               Влево | Стик влево          |
|              Вправо | Стик вправо         |
|               Просмотр | Кнопка просмотра         |
|               Меню | Кнопка меню         |
|             Принять | Кнопка действия 1     |
|             Отмена | Кнопка действия 2     |

Аркадные джойстики не поддерживают какие-либо [дополнительные наборы](ui-navigation-controller.md#optional-set) команд навигации.

## <a name="detect-and-track-arcade-sticks"></a>Обнаружение и отслеживание аркадных джойстиков

Выявления и отслеживания arcade фиксируется работает в точно так же, как и для игровых панелей, за исключением с [ArcadeStick][] вместо класса [Gamepad](https://docs.microsoft.com/uwp/api/Windows.Gaming.Input.Gamepad) класса. Подробнее см. в статье [Геймпад и вибрация](gamepad-and-vibration.md).

<!-- Arcade sticks are managed by the system, therefore you don't have to create or initialize them. The system provides a list of connected arcades sticks and events to notify you when an arcade stick is added or removed.

### The arcade sticks list

The [ArcadeStick][] class provides a static property, [ArcadeSticks][], which is a read-only list of arcade sticks that are currently connected. Because you might only be interested in some of the connected arcade sticks, it's recommended that you maintain your own collection instead of accessing them through the `ArcadeSticks` property.

The following example copies all connected arcade sticks into a new collection. Note that because other threads in the background will be accessing this collection (in the [ArcadeStickAdded][] and [ArcadeStickRemoved][] events), you need to place a lock around any code that reads or updates the collection.

```cpp
auto myArcadeSticks = ref new Vector<ArcadeStick^>();
critical_section myLock{};

for (auto arcadeStick : ArcadeStick::ArcadeSticks)
{
    // Check if the arcade stick is already in myArcadeSticks; if it isn't, add
    // it.
    critical_section::scoped_lock lock{ myLock };
    auto it = std::find(begin(myArcadeSticks), end(myArcadeSticks), arcadeStick);

    if (it == end(myArcadeSticks))
    {
        // This code assumes that you're interested in all arcade sticks.
        myArcadeSticks->Append(arcadeStick);
    }
}
```

### Adding and removing arcade sticks

When an arcade stick is added or removed the [ArcadeStickAdded][] and [ArcadeStickRemoved][] events are raised. You can register handlers for these events to keep track of the arcade sticks that are currently connected.

The following example starts tracking an arcade stick that's been added.

```cpp
ArcadeStick::ArcadeStickAdded += ref new EventHandler<ArcadeStick^>(Platform::Object^, ArcadeStick^ args)
{
    // Check if the just-added arcade stick is already in myArcadeSticks; if it
    // isn't, add it.
    critical_section::scoped_lock lock{ myLock };
    auto it = std::find(begin(myGamepads), end(myGamepads), args);

    // This code assumes that you're interested in all new arcade sticks.
    myArcadeSticks->Append(args);
}
```

The following example stops tracking an arcade stick that's been removed.

```cpp
ArcadeStick::ArcadeStickRemoved += ref new EventHandler<ArcadeStick^>(Platform::Object^, ArcadeStick^ args)
{
    unsigned int indexRemoved;

    if(myArcadeSticks->IndexOf(args, &indexRemoved))
    {
        myArcadeSticks->RemoveAt(indexRemoved);
    }
}
```

### Users and headsets

Each arcade stick can be associated with a user account to link their identity to their gameplay, and can have a headset attached to facilitate voice chat or in-game features. To learn more about working with users and headsets, see [Tracking users and their devices](input-practices-for-games.md#tracking-users-and-their-devices) and [Headset](headset.md). -->

## <a name="reading-the-arcade-stick"></a>Чтение аркадных джойстиков

После выбора аркадного джойстика, с которым вы хотите работать, можно приступить к сбору данных, вводимых с его помощью. Тем не менее, в отличие от некоторых других типов вводимых данных, с которыми вы, возможно, уже знакомы, аркадные джойстики не передают данные об изменении состояния путем создания событий. Вместо этого для получения стандартной информации об их текущем состоянии вы проводите _опрос_.

### <a name="polling-the-arcade-stick"></a>Опрос аркадного джойстика

В ходе опроса сохраняется моментальный снимок аркадного джойстика в конкретный момент времени. Такой подход к сбору входных данных отлично подходит для большинства игр, потому что их логика, как правило, выполняется в детерминированном цикле и не зависит от событий; кроме того, проще интерпретировать игровые команды из входных данных, собранных в один момент, чем из многочисленных единичных входных данных, собранных за определенный период.

Опрос аркадного джойстика выполняется путем вызова функции [GetCurrentReading][], которая возвращает структуру [ArcadeStickReading][], содержащую состояние аркадного джойстика.

В следующем примере кода выполняется опрос аркадного джойстика на предмет его текущего состояния.

```cpp
auto arcadestick = myArcadeSticks[0];

ArcadeStickReading reading = arcadestick->GetCurrentReading();
```

Помимо состояния аркадного джойстика считанные данные содержат метку времени, указывающую точное время извлечения сведений о состоянии. Метку времени удобно использовать для обращения ко времени предыдущего события считывания данных или ко времени моделирования конкретного игрового момента.

### <a name="reading-the-buttons"></a>Чтение кнопок

Каждой из кнопок палочка arcade&mdash;четырех направлений джойстик, шесть **действие** кнопки, а также два **специальные** кнопки&mdash;обеспечивает чтение цифровой информации, указывающее ли он нажатии (вниз) или отпускании (вверх). Для повышения эффективности показания кнопки не представлены в виде отдельных логических значений. Вместо этого они лучше всех защищены в единый битового поля, представленного [ArcadeStickButtons][] перечисления.

> [!NOTE]
> Накопители Arcade оснащены дополнительные кнопки, используемый для навигации пользовательского интерфейса, такие как **представление** и **меню** кнопки. Эти кнопки не являются частью перечисления `ArcadeStickButtons`, и данные с них можно считать только при доступе к аркадному джойстику как к устройству навигации по пользовательскому интерфейсу. Дополнительные сведения см. в разделе [Устройство навигации в пользовательском интерфейсе](ui-navigation-controller.md).

Значения кнопок считываются из свойства `Buttons` структуры [ArcadeStickReading][]. Так как это свойство представляет собой битовое поле, для изоляции значения кнопки, сведения о которой вам интересны, используется побитовая маскировка. Кнопка нажата (вниз), когда установлен соответствующий бит; в противном случае кнопка отпущена (вверх).

В следующем примере определяется ли **действием 1** нажатии кнопки.

```cpp
if (ArcadeStickButtons::Action1 == (reading.Buttons & ArcadeStickButtons::Action1))
{
    // Action 1 is pressed
}
```

В следующем примере определяется ли **действием 1** кнопки мыши.

```cpp
if (ArcadeStickButtons::None == (reading.Buttons & ArcadeStickButtons::Action1))
{
    // Action 1 is released (not pressed)
}
```

Иногда нужно определить, когда кнопка переходит из нажатого состояния в отпущенное или наоборот, узнать состояние сразу нескольких кнопок (нажаты или отпущены) либо определить особую схему расположения кнопок в наборе — некоторые кнопки могут быть нажаты, а другие отпущены. Сведения о том, как обнаруживать эти условия, см. в разделах [Обнаружение переходов состояний кнопок](input-practices-for-games.md#detecting-button-transitions) и [Обнаружение сложных расположений кнопок](input-practices-for-games.md#detecting-complex-button-arrangements).

## <a name="run-the-inputinterfacing-sample"></a>Запуск примера кода InputInterfacing

В примере кода [InputInterfacingUWP _(в центре GitHub)_](https://github.com/Microsoft/Xbox-ATG-Samples/tree/master/Samples/System/InputInterfacingUWP) показано, как использовать аркадные джойстики и различные типы устройств ввода вместе, а также представлено, как эти устройства ввода работают в качестве контроллеров навигации по пользовательскому интерфейсу.

## <a name="see-also"></a>См. также

* [Windows.Gaming.Input.UINavigationController][]
* [Windows.Gaming.Input.IGameController][]
* [Входной и рекомендации для игр](input-practices-for-games.md)

[Windows.Gaming.Input]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.aspx
[Windows.Gaming.Input.IGameController]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.igamecontroller.aspx
[Windows.Gaming.Input.UINavigationController]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.uinavigationcontroller.aspx
[arcadestick]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.arcadestick.aspx
[arcadesticks]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.arcadestick.arcadesticks.aspx
[arcadestickadded]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.arcadestick.arcadestickadded.aspx
[arcadestickremoved]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.arcadestick.arcadestickremoved.aspx
[getcurrentreading]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.arcadestick.getcurrentreading.aspx
[arcadestickreading]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.arcadestickreading.aspx
[arcadestickbuttons]: https://msdn.microsoft.com/library/windows/apps/windows.gaming.input.arcadestickbuttons.aspx
