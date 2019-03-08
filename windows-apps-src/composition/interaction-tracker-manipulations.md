---
title: Пользовательские манипуляции, выполняемые с помощью InteractionTracker
description: Используйте API InteractionTracker для создания пользовательских взаимодействий с манипуляциями.
ms.date: 10/10/2017
ms.topic: article
keywords: windows 10, uwp, анимация
ms.localizationpriority: medium
ms.openlocfilehash: 9d2c965bcfbf81efe73ce8aff93cdb8b31163fbd
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57653849"
---
# <a name="custom-manipulation-experiences-with-interactiontracker"></a>Пользовательские взаимодействия с манипуляциями, выполняемые с помощью InteractionTracker

В этой статье рассматривается, как использовать API InteractionTracker для создания пользовательских взаимодействий с манипуляциями.

## <a name="prerequisites"></a>Предварительные условия

Подразумевается, что вы знакомы с понятиями, которые рассматриваются в этих статьях:

- [Анимации на основе входных данных](input-driven-animations.md)
- [Связь на основе анимации](relation-animations.md)

## <a name="why-create-custom-manipulation-experiences"></a>Зачем создавать пользовательские взаимодействия с манипуляциями?

В большинстве случаев для создания взаимодействий в пользовательском интерфейсе достаточно воспользоваться встроенными элементами управления манипуляциями. Но что если стандартных элементов управления недостаточно? Что если требуется создать определенное взаимодействие на основе ввода, а движения стандартной манипуляции пользовательского интерфейса недостаточно? В этом случае помогут пользовательские взаимодействия. Они позволяют разработчикам приложений и дизайнерам действовать более креативно и создавать подвижные взаимодействия, которые лучше соответствуют специфике бренда и конкретному языку разработки. Вам с нуля предоставляется доступ к подходящему набору строительных компонентов для полной пользовательской настройки взаимодействия с манипуляциями: от того, как движение должно реагировать на касание экрана пальцем и удаление пальца с экрана, до точек прикрепления и связывания ввода.

Ниже приводится несколько распространенных примеров создания пользовательского взаимодействия с манипуляциями:

- Добавление пользовательского поведения прокрутки, удаления, закрытия
- Эффекты на основе ввода (при сдвиге содержимое размывается)
- Пользовательские элементы управления со специальными движениями манипуляции (пользовательские типы ListView, ScrollViewer и т. д.)

![Пример прокрутки пальцем](images/animation/swipe-scroller.gif)

![Пример анимации путем оттягивания](images/animation/pull-to-animate.gif)

## <a name="why-use-interactiontracker"></a>Зачем использовать InteractionTracker?

InteractionTracker был впервые представлен в пространстве имен Windows.UI.Composition.Interactions в версии SDK 10586. InteractionTracker позволяет:

- **Complete Flexibility**: мы хотим, чтобы у вас была возможность настроить все аспекты взаимодействия с манипуляцией; в частности, точные движения, которые происходят во время ввода или в ответ на него. При создании пользовательского взаимодействия с манипуляцией с помощью InteractionTracker все необходимые кнопки в вашем распоряжении.
- **Smooth Performance**: одна из сложностей в работе с взаимодействиями с манипуляцией заключается в том, что их производительность зависит от потока пользовательского интерфейса. Это может отрицательно сказаться на взаимодействии с манипуляциями, если пользовательский интерфейс занят. InteractionTracker был создан для использования новой подсистемы анимации, которая функционирует в независимом потоке со скоростью 60 кадр/с, что обеспечивает плавное движение.

## <a name="overview-interactiontracker"></a>Обзор: InteractionTracker

При создании пользовательских взаимодействий с манипуляцией вы взаимодействуете с двумя основными компонентами. Сначала рассмотрим эти компоненты:

- [InteractionTracker](https://docs.microsoft.com/uwp/api/windows.ui.composition.interactions.interactiontracker) — базовый объект, поддерживающий конечный автомат, свойства которого определяются вводом активного пользователя, непосредственными обновлениями и анимациями. Подразумевается, что объект затем будет связан с CompositionAnimation для создания пользовательского движения с манипуляцией.
- [VisualInteractionSource](https://docs.microsoft.com/uwp/api/windows.ui.composition.interactions.visualinteractionsource) — дополнительный объект, определяющий, когда и при каких условиях ввод отправляется в InteractionTracker. Он определяет и тип CompositionVisual, используемый для проверки нажатия, и другие свойства конфигурации ввода.

Свойства InteractionTracker в качестве конечного автомата могут определяться любым из перечисленных:

- Direct User Interaction: конечный пользователь выполняет манипуляции непосредственно в области проверки нажатия VisualInteractionSource
- Inertia: определяется программной скоростью или жестом пользователя, свойства InteractionTracker анимируются по кривой инерции.
- CustomAnimation: пользовательская анимация, непосредственно нацеленная на свойство InteractionTracker

### <a name="interactiontracker-state-machine"></a>Конечный автомат InteractionTracker

Как упоминалось ранее, InteractionTracker является конечного автомата, с четырех состояниях — каждый из которых можно перейти к любой из четырех состояний. (Дополнительные сведения о смене состояний InteractionTracker см. в документации по классу [InteractionTracker](https://docs.microsoft.com/uwp/api/windows.ui.composition.interactions.interactiontracker).)

| Состояние | Описание |
|-------|-------------|
| Idle | Нет активности, вводы или анимации не определяются |
| Состояние Interacting (взаимодействие) | Обнаружен активный пользовательский ввод |
| Инерция | Активное движение в результате активного ввода или программной скорости |
| CustomAnimation | Активное движение в результате пользовательской анимации |

В каждом из этих случаев при смене состояния InteractionTracker создается событие (или обратный вызов), которое можно прослушать. Чтобы вы могли прослушивать эти события, они должны реализовывать интерфейс [IInteractionTrackerOwner](https://docs.microsoft.com/uwp/api/windows.ui.composition.interactions.iinteractiontrackerowner) и создать объект InteractionTracker с помощью метода CreateWithOwner. На следующей схеме также показано, когда инициируются разные события.

![Конечный автомат InteractionTracker](images/animation/interaction-tracker-diagram.png)

## <a name="using-the-visualinteractionsource"></a>Использование VisualInteractionSource

Чтобы InteractionTracker определялся вводом, необходимо подключить к нему VisualInteractionSource (VIS). VIS создается в качестве дополнительного объекта с использованием CompositionVisual, чтобы определить:

1. область проверки нажатия, в которой будет отслеживаться ввод, и координационное пространство, в котором обнаруживаются жесты
1. Конфигурации ввода, который будут обнаруживаться и маршрутизироваться, могут включать следующее:
    - Выявляемые жестов: Позиция X и Y (горизонтальная и вертикальная панорамирования), масштаб (сжатие)
    - Инерция
    - Ограничители и цепочки
    - Режимы перенаправления: какие входные данные перенаправляются автоматически на InteractionTracker

> [!NOTE]
> Поскольку VisualInteractionSource создается на основе положения проверки нажатия и координационного пространства визуального элемента, не рекомендуется использовать визуальный элемент, который будет двигаться или менять положение.

> [!NOTE]
> Можно использовать несколько экземпляров VisualInteractionSource с одним и тем же InteractionTracker, если областей проверки нажатия несколько. Однако, как правило, используется только один VIS.

VisualInteractionSource также отвечает за управление ситуацией, когда входные данные с разных типов ввода (касание, PTP, перо) перенаправляются в InteractionTracker. Это поведение определяется свойством ManipulationRedirectionMode. По умолчанию, любой ввод с указателя отправляется в поток пользовательского интерфейса, а ввод с высокоточной сенсорной панели — в VisualInteractionSource и InteractionTracker.

Следовательно, если сенсорный ввод и ввод с пера (обновление Creators Update) должны определять манипуляцию через VisualInteractionSource и InteractionTracker, необходимо вызвать метод VisualInteractionSource.TryRedirectForManipulation. В небольшом фрагменте кода из приложения XAML ниже метод вызывается, когда в самой верхней сетке UIElement происходит событие нажатия на сенсорную панель:

```csharp
private void root_PointerPressed(object sender, PointerRoutedEventArgs e)
{
    if (e.Pointer.PointerDeviceType == Windows.Devices.Input.PointerDeviceType.Touch)
    {
        _source.TryRedirectForManipulation(e.GetCurrentPoint(root));
    }
}
```

## <a name="tie-in-with-expressionanimations"></a>Связывание с ExpressionAnimations

При использовании InteractionTracker для запуска взаимодействия с манипуляцией вы, в основном, взаимодействуете со свойствами Scale и Position. Как и другие свойства CompositionObject, они могут либо представлять собой целевой объект, либо на них может ссылаться CompositionAnimation (как правило, ExpressionAnimations).

Чтобы использовать InteractionTracker в Expression, создайте ссылку на свойство Position (или Scale) модуля отслеживания, как в примере ниже. По мере того как свойство InteractionTracker меняется из-за вышеперечисленных условий, вывод Expression также меняется.

```csharp
// With Strings
var opacityExp = _compositor.CreateExpressionAnimation("-tracker.Position");
opacityExp.SetReferenceParameter("tracker", _tracker);

// With ExpressionBuilder
var opacityExp = -_tracker.GetReference().Position;
```

> [!NOTE]
> При создании в типе Expression ссылки на положение InteractionTracker, необходимо указать отрицательное значение для результирующего типа Expression, чтобы движение выполнялось в правильном направлении. Это вызвано перемещением InteractionTracker из начальной точке на графе; такой подход позволяет воспринимать перемещение InteractionTracker в координатах "реального мира", например расстояние от начальной точки.

## <a name="get-started"></a>Начало работы

Чтобы начать работать с InteractionTracker для создания пользовательских взаимодействий с манипуляциями, выполните следующие действия:

1. Создайте объект InteractionTracker с помощью InteractionTracker.Create или InteractionTracker.CreateWithOwner.
    - (Если используется CreateWithOwner, убедитесь, что реализован интерфейс IInteractionTrackerOwner.)
1. Задайте минимальное и максимальное положение только что созданного объекта InteractionTracker.
1. Создайте VisualInteractionSource с CompositionVisual.
    - Убедитесь, что передаваемый визуальный элемент имеет ненулевой размер. В противном случае не удастся правильно выполнить проверку нажатия.
1. Задайте свойства VisualInteractionSource.
    - VisualInteractionSourceRedirectionMode
    - PositionXSourceMode, PositionYSourceMode, ScaleSourceMode
    - Ограничители и цепочки
1. Добавьте VisualInteractionSource в InteractionTracker с помощью InteractionTracker.InteractionSources.Add.
1. Настройте TryRedirectForManipulation для моментов обнаружения ввода касанием и пером.
    - Как правило, в XAML для этого используется событие PointerPressed элемента UIElement.
1. Создайте анимацию ExpressionAnimation, которая ссылается на положение InteractionTracker, и назначьте ее целевым объектом свойство объекта CompositionObject.

Здесь приводится небольшой фрагмент кода с пунктами 1–5 в действии:

```csharp
private void InteractionTrackerSetup(Compositor compositor, Visual hitTestRoot)
{ 
    // #1 Create InteractionTracker object
    var tracker = InteractionTracker.Create(compositor);

    // #2 Set Min and Max positions
    tracker.MinPosition = new Vector3(-1000f);
    tracker.MaxPosition = new Vector3(1000f);

    // #3 Setup the VisualInteractionSourc
    var source = VisualInteractionSource.Create(hitTestRoot);

    // #4 Set the properties for the VisualInteractionSource
    source.ManipulationRedirectionMode =
        VisualInteractionSourceRedirectionMode.CapableTouchpadOnly;
    source.PositionXSourceMode = InteractionSourceMode.EnabledWithInertia;
    source.PositionYSourceMode = InteractionSourceMode.EnabledWithInertia;

    // #5 Add the VisualInteractionSource to InteractionTracker
    tracker.InteractionSources.Add(source);
}
```

Более сложные сценарии использования InteractionTracker описаны в следующих статьях:

- [Создание точек прикрепления с InertiaModifiers](inertia-modifiers.md)
- [По запросу для обновления с SourceModifiers](source-modifiers.md)
