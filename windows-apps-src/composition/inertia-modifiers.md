---
author: jwmsft
title: Использование модификаторов инерции для создания точек прикрепления
description: Узнайте, как использовать функцию InertiaModifier модуля InteractionTracker для создания элементов движения, выполняющих прикрепление к определенной точке.
ms.author: jimwalk
ms.date: 10/10/2017
ms.topic: article
keywords: windows 10, uwp, анимация
ms.localizationpriority: medium
ms.openlocfilehash: 20c10b1cc621da834a8a7c411e75eb92b1944b5a
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6040489"
---
# <a name="create-snap-points-with-inertia-modifiers"></a>Создание точек прикрепления с модификаторами инерции

В этой статье более подробно рассматривается, как использовать функцию InertiaModifier модуля InteractionTracker для создания элементов движения, выполняющих прикрепление к определенной точке.

## <a name="prerequisites"></a>Предварительные требования

Подразумевается, что вы знакомы с понятиями, которые рассматриваются в этих статьях:

- [Анимации на основе ввода](input-driven-animations.md)
- [Пользовательские взаимодействия с манипуляциями, выполняемые с помощью InteractionTracker](interaction-tracker-manipulations.md)
- [Анимации на основе связей](relation-animations.md)

## <a name="what-are-snap-points-and-why-are-they-useful"></a>Что такое точки прикрепления и почему они полезны?

При создании пользовательских взаимодействий с манипуляциями иногда полезно создать специализированные _точки положения_ на холсте с возможностью прокрутки/масштабирования, в которых InteractionTracker всегда будет располагаться в периоды неактивности. Они часто называются _точки прикрепления_.

В следующем примере обратите внимание на то, как в результате прокрутки элементы пользовательского интерфейса оказываются в странном положении между разными изображениями:

![Прокрутка без точек прикрепления](images/animation/snap-points-none.gif)

Если добавлены точки прикрепления, то когда вы прекращаете прокрутку изображений, они "прикрепляются" в определенном положении. При наличии точек прикрепления прокрутка изображений становится гораздо более четкой и лучше реагирует на действия пользователя.

![Прокрутка с одной точкой прикрепления](images/animation/snap-points-single.gif)

## <a name="interactiontracker-and-inertiamodifiers"></a>InteractionTracker и модификаторы InertiaModifier

При создании пользовательских взаимодействий с манипуляциями с помощью InteractionTracker можно создавать схемы движения по точкам прикрепления, используя модификаторы InertiaModifier. Модификаторы InertiaModifier позволяют определить, где и как InteractionTracker достигает места назначения при переходе в состояние инерции. Модификаторы InertiaModifier можно использовать для изменения положения на оси X или Y или свойств масштабирования InteractionTracker.

Существует три типа модификаторов InertiaModifier:

- InteractionTrackerInertiaRestingValue— это способ изменить конечное положение покоя после взаимодействия или программной скорости. Предопределенное движение переместит InteractionTracker в это положение.
- InteractionTrackerInertiaMotion— это способ определить конкретное движение, которое InteractionTracker будет выполнять после взаимодействия или программной скорости. Конечное расположение определяется по этому движению.
- InteractionTrackerInertiaNaturalMotion— это способ определить конечное положение покоя после взаимодействия или программной скорости, но с физической анимацией (NaturalMotionAnimation).

При переходе в состояние инерции InteractionTracker оценивает каждый назначенный ему модификатор InertiaModifier и определяет, применяется ли какой-либо из этих модификаторов. Это означает, что можно создать и присвоить InteractionTracker несколько модификаторов InertiaModifier, однако при определении каждого из них необходимо сделать следующее:

1. Определите состояние — выражение, которое определяет условный оператор, при котором должен происходить конкретный модификатор InertiaModifier. Для этого часто необходимо обратиться к свойству NaturalRestingPosition модуля InteractionTracker (место назначения с учетом инерции по умолчанию).
1. Определите параметры RestingValue/Motion/NaturalMotion— определите фактическое выражение значения покоя, выражение движения или NaturalMotionAnimation, которая происходит, если условие выполнено.

> [!NOTE]
> Условный аспект модификаторов InertiaModifier оцениваются только один раз, когда InteractionTracker переходит в состояние инерции. Однако только для InertiaMotion выражение движения оценивается на каждом кадре для модификатора, условие которого выполнено.

## <a name="example"></a>Пример.

Посмотрим, как можно использовать модификаторы InertiaModifier для создания точек прикрепления с целью воссоздания холста прокрутки изображений. В этом примере каждая манипуляция предназначена для потенциального перемещения по одному изображению, это часто называется "единственная обязательная точка прикрепления".

Для начала настроим классы InteractionTracker, VisualInteractionSource и Expression, в которых будет использоваться положение InteractionTracker.

```csharp
private void SetupInput()
{
    _tracker = InteractionTracker.Create(_compositor);
    _tracker.MinPosition = new Vector3(0f);
    _tracker.MaxPosition = new Vector3(3000f);

    _source = VisualInteractionSource.Create(_root);
    _source.ManipulationRedirectionMode =
        VisualInteractionSourceRedirectionMode.CapableTouchpadOnly;
    _source.PositionYSourceMode = InteractionSourceMode.EnabledWithInertia;
    _tracker.InteractionSources.Add(_source);

    var scrollExp = _compositor.CreateExpressionAnimation("-tracker.Position.Y");
    scrollExp.SetReferenceParameter("tracker", _tracker);
    ElementCompositionPreview.GetElementVisual(scrollPanel).StartAnimation("Offset.Y", scrollExp);
}
```

Поскольку поведение единственной обязательной точки прикрепления переместит содержимое либо вверх, либо вниз, затем вам потребуется два разных модификатора инерции: один, перемещающий прокручиваемое содержимое вверх, а другой— вниз.

```csharp
// Snap-Point to move the content up
var snapUpModifier = InteractionTrackerInertiaRestingValue.Create(_compositor);
// Snap-Point to move the content down
var snapDownModifier = InteractionTrackerInertiaRestingValue.Create(_compositor);
```

Направление прикрепления (вверх или вниз) определяется в зависимости от того, где оказался бы InteractionTracker относительно расстояния прикрепления (расстояния между расположениями прикрепления) при перемещении естественным образом. Если после средней точки, то выполняется прикрепление вниз, в противном случае— вверх. (В этом примере расстояние прикрепления сохраняется в PropertySet)

```csharp
// Is NaturalRestingPosition less than the halfway point between Snap Points?
snapUpModifier.Condition = _compositor.CreateExpressionAnimation(
"this.Target.NaturalRestingPosition.y < (this.StartingValue – ” + “mod(this.StartingValue, prop.snapDistance) + prop.snapDistance / 2)");
snapUpModifier.Condition.SetReferenceParameter("prop", _propSet);
// Is NaturalRestingPosition greater than the halfway point between Snap Points?
snapDownModifier.Condition = _compositor.CreateExpressionAnimation(
"this.Target.NaturalRestingPosition.y >= (this.StartingValue – ” + “mod(this.StartingValue, prop.snapDistance) + prop.snapDistance / 2)");
snapDownModifier.Condition.SetReferenceParameter("prop", _propSet);
```

На этой схеме изображена логика происходящего:

![Схема модификатора инерции](images/animation/inertia-modifier-diagram.png)

Теперь достаточно определить значения покоя для каждого модификатора InertiaModifier: указать, следует ли переместить положение InteractionTracker в предыдущее положение прикрепления или в следующее.

```csharp
snapUpModifier.RestingValue = _compositor.CreateExpressionAnimation(
"this.StartingValue - mod(this.StartingValue, prop.snapDistance)");
snapUpModifier.RestingValue.SetReferenceParameter("prop", _propSet);
snapForwardModifier.RestingValue = _compositor.CreateExpressionAnimation(
"this.StartingValue + prop.snapDistance - mod(this.StartingValue, ” + “prop.snapDistance)");
snapForwardModifier.RestingValue.SetReferenceParameter("prop", _propSet);
```

Наконец, нужно добавить модификаторы InertiaModifier в InteractionTracker. Теперь когда InteractionTracker переходит в состояние инерции InertiaState, он проверит условия ваших модификаторов InertiaModifier, чтобы определить, нужно ли изменить расположение.

```csharp
var modifiers = new InteractionTrackerInertiaRestingValue[] { 
snapUpModifier, snapDownModifier };
_tracker.ConfigurePositionYInertiaModifiers(modifiers);
```