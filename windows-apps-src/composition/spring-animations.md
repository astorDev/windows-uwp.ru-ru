---
author: jwmsft
title: Пружинная анимация
description: Узнайте, как использовать пружинные анимации естественных движений.
ms.author: jimwalk
ms.date: 10/10/2017
ms.topic: article
keywords: windows 10, uwp, анимация
ms.localizationpriority: medium
ms.openlocfilehash: 2b28653fc7746075c57f862b0c885beac6d4934f
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/25/2018
ms.locfileid: "5542801"
---
# <a name="spring-animations"></a>Пружинная анимация

В этой статье показано, как использовать пружинные анимации NaturalMotionAnimations.

## <a name="prerequisites"></a>Предварительные требования

Подразумевается, что вы знакомы с понятиями, которые рассматриваются в этих статьях:

- [Анимации с естественным движением](natural-animations.md)

## <a name="why-springs"></a>Почему пружины?

Пружины обеспечивают достаточно распространенный, знакомый всем тип движения (от игрушек до опытов в кабинете физики с прикрепленным к пружине блоком). Колеблющееся движение пружины часто ассоциируется у наблюдающего с игривостью и легкостью. Следовательно, движение пружины будет хорошо смотреться в пользовательском интерфейсе всех приложений, в которых нужно создать для пользователя более игривую траекторию движения, нежели движение по кривой Безье третьего порядка. В таких случаях движение пружины не только обеспечивает более игривое взаимодействие, но и позволяет привлечь внимание к новому содержимому или содержимому, которое анимируется в настоящее время.In these cases, spring motion not only creates a livelier motion experience, but also can help draw attention to new or currently animating content. В зависимости от фирменной символики приложения или языка движения колебания могут быть более выраженными и видимыми или более незаметными.

![Движение с использованием пружинной анимации](images/animation/offset-spring.gif)
![Движение с использованием анимации по кривой Безье третьего порядка](images/animation/offset-cubic-bezier.gif)

## <a name="using-springs-in-your-ui"></a>Использование пружин в пользовательском интерфейсе

Как упоминалось ранее, с помощью пружин можно реализовать движение в пользовательском интерфейсе приложения знакомым и игривым способом. Стандартное использование пружин в пользовательском интерфейсе:

| Описание использования пружин | Наглядный пример |
| ------------------------ | -------------- |
| Живое "выглядывающее" движение. (Анимация масштабирования) | ![Движение масштабирования с использованием пружинной анимации](images/animation/scale-spring.gif) |
| Создание немного более энергичного движения (смещение анимации) | ![Движение смещения с использованием пружинной анимации](images/animation/offset-spring.gif) |

В каждом из этих случаев движение пружины инициируется пружинным переходом к новому значению, колебанием вокруг нового значения или колебанием вокруг существующего значения с определенной начальной скоростью.

![Колебание пружинной анимации](images/animation/spring-animation-diagram.png)

## <a name="defining-your-spring-motion"></a>Определение пружинного движения

Для создания пружинного взаимодействия используются API NaturalMotionAnimation. В частности, создается SpringNaturalMotionAnimation с использованием методов создания* вне компоновщика. Затем можно определить следующие свойства движения:

- DampingRatio— выражает уровень затухания пружинного движения, которое используется в анимации.

| Коэффициент затухания | Описание |
| ------------------- | ----------- |
| DampingRatio = 0 | Без затухания: пружина долго колеблется |
| 0 < DampingRatio < 1 | Слабозатухающая: пружина колеблется от небольшого до большого колебания. |
| DampingRatio = 1 | Критическое затухание: пружина не колеблется. |
| DampingRation > 1 | Сильнозатухающая: пружина быстро достигает места назначения, резко замедляется и перестает колебаться |

- Период — время, необходимое пружине для выполнения одного колебания.
- Конечное/начальное значение: определенные начальные и конечные точки пружинного движения( если не определено, начальное и(или) конечное значение будет равно текущему).
- Начальная скорость — начальная программная скорость движения.

Можно также определить набор свойств движения, которые совпадают с KeyFrameAnimations:

- Время задержки / поведение задержки
- StopBehavior

В стандартных случаях анимирования смещения и масштабирования/размера специалисты по созданию дизайна Windows рекомендуют использовать следующие значения DampingRation и Period для разных типов пружин:

| Свойство | Нормальная пружина | Пружина с затуханием | Пружина со слабым затуханием |
| -------- | ------------- | --------------- | -------------------- |
| Смещение | Коэффициент затухания = 0,8 <br/> Период = 50 мс | Коэффициент затухания = 0,85 <br/> Период = 50 мс | Коэффициент затухания = 0,65 <br/> Период = 60 мс |
| Масштаб и размер | Коэффициент затухания = 0,7 <br/> Период = 50 мс | Коэффициент затухания = 0,8 <br/> Период = 50 мс | Коэффициент затухания = 0,6 <br/> Период = 60 мс |

Определив эти свойства, можно передать NaturalMotionAnimation пружины методу StartAnimation объекта CompositionObject или свойству Motion модификатора InertiaModifier модуля InteractionTracker.

## <a name="example"></a>Пример.

В этом примере в пользовательском интерфейсе создается взаимодействие "навигация" и "холст". Когда пользователь нажимает кнопку "Развернуть", область навигации совершает пружинное, колеблющееся анимированное движение.

![Пружинная анимация по щелчку](images/animation/spring-animation-on-click.gif)

Для начала определите пружинную анимацию в событии по щелчку для момента, когда отображается область навигации. Затем определите свойства анимации, используя функцию InitialValueExpression, которая позволяет использовать Expression для определения FinalValue. Кроме того, вы отслеживаете, открыта ли область, и запускаете анимацию, когда область готова.

```csharp
private void Button_Clicked(object sender, RoutedEventArgs e)
{
 _springAnimation = _compositor.CreateSpringScalarAnimation();
 _springAnimation.DampingRatio = 0.75f;
 _springAnimation.Period = TimeSpan.FromSeconds(0.5);

 if (!_expanded)
 {
 _expanded = true;
 _propSet.InsertBoolean("expanded", true);
 _springAnimation.InitialValueExpression[“FinalValue”] = “this.StartingValue + 250”;
 } else
 {
 _expanded = false;
 _propSet.InsertBoolean("expanded", false);
_springAnimation.InitialValueExpression[“FinalValue”] = “this.StartingValue - 250”;
 }
 _naviPane.StartAnimation("Offset.X", _springAnimation);
}
```

Что если это движение необходимо связать с вводом? что если конечный пользователь проводит пальцем за пределы экрана и области исчезают с пружинным движением? Еще важнее, чтобы движение адаптировалось в зависимости от скорости движения пальца конечного пользователя, если тот проводит по экрану быстро или сильно.

![Пружинная анимация по прокрутке](images/animation/spring-animation-on-swipe.gif)

Для этого можно взять ту же пружинную анимацию и передать ее модификатору IntertiaModifier с InteractionTracker. Дополнительные сведения об InputAnimations и InteractionTracker см. в разделе [Пользовательские взаимодействия с манипуляциями, выполняемые с помощью InteractionTracker](interaction-tracker-manipulations.md). Для рассмотрения этого примера кода допустим, что вы уже настроили InteractionTracker и VisualInteractionSource. Создадим модификаторы InertiaModifiers, которые будут принимать анимацию NaturalMotionAnimation (в данном случае пружинную).

```csharp
// InteractionTracker and the VisualInteractionSource previously setup
// The open and close ScalarSpringAnimations defined earlier
private void SetupInput()
{
 // Define the InertiaModifier to manage the open motion
 var openMotionModifer = InteractionTrackerInertiaNaturalMotion.Create(compositor);

 // Condition defines to use open animation if panes in non-expanded view
 // Property set value to track if open or closed is managed in other part of code
 openMotionModifer.Condition = _compositor.CreateExpressionAnimation(
"propset.expanded == false");
 openMotionModifer.Condition.SetReferenceParameter("propSet", _propSet);
 openMotionModifer.NaturalMotion = _openSpringAnimation;

 // Define the InertiaModifer to manage the close motion
 var closeMotionModifier = InteractionTrackerInertiaNaturalMotion.Create(_compositor);

 // Condition defines to use close animation if panes in expanded view
 // Property set value to track if open or closed is managed in other part of code
 closeMotionModifier.Condition = 
_compositor.CreateExpressionAnimation("propSet.expanded == true");
 closeMotionModifier.Condition.SetReferenceParameter("propSet", _propSet);
 closeMotionModifier.NaturalMotion = _closeSpringAnimation;

 _tracker.ConfigurePositionXInertiaModifiers(new 
InteractionTrackerInertiaNaturalMotion[] { openMotionModifer, closeMotionModifier});

 // Take output of InteractionTracker and assign to the pane
 var exp = _compositor.CreateExpressionAnimation("-tracker.Position.X");
 exp.SetReferenceParameter("tracker", _tracker);
 ElementCompositionPreview.GetElementVisual(pageNavigation).
StartAnimation("Translation.X", exp);
}
```

Теперь в пользовательском интерфейсе есть и программная пружинная анимация, и пружинная анимация на основе ввода.

Обобщим: что необходимо сделать, чтобы использовать пружинную анимацию в приложении:

1. Создайте SpringAnimation вне компоновщика.
1. Определите свойства SpringAnimation, если не хотите использовать значения по умолчанию:
    - Коэффициент затухания DampingRatio
    - Период
    - Конечное значение
    - Начальное значение
    - Начальная скорость
1. Назначьте соответствующие значения целевому объекту.
    - Если выполняется анимирование свойства объекта CompositionObject, передайте SpringAnimation в качестве параметра объекту StartAnimation.
    - Если нужно использовать это свойство с вводом, задайте значение SpringAnimation для свойства NaturalMotion модификатора InertiaModifier.

