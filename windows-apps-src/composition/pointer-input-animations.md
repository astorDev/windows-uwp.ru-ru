---
title: Анимации на основе указателей
description: Узнайте, как использовать положение указателя для создания динамического взаимодействия "прикрепление к курсору".
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, анимация
ms.localizationpriority: medium
ms.openlocfilehash: 3512d47c8b3e689b0baadec26c1d8f0f510e03ef
ms.sourcegitcommit: d2517e522cacc5240f7dffd5bc1eaa278e3f7768
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8335146"
---
# <a name="pointer-based-animations"></a>Анимации на основе указателей

В этой статье показано, как использовать положение указателя для создания динамического взаимодействия "прикрепление к курсору".

## <a name="prerequisites"></a>Предварительные требования

Подразумевается, что вы знакомы с понятиями, которые рассматриваются в этих статьях:

- [Анимации на основе ввода](input-driven-animations.md)
- [Анимации на основе связей](relation-animations.md)

## <a name="why-create-pointer-position-driven-experiences"></a>Зачем создавать взаимодействия на основе положения указателя?

На языке проектирования Fluent сенсорный ввод не является единственным способом взаимодействия с пользовательским интерфейсом. Поскольку UWP поддерживает разнообразные форм-факторы устройств, конечные пользователи взаимодействуют с приложениями, используя другие методы ввода, такие как мышь и перо. Благодаря поступлению от таких методов ввода данных о расположении конечные пользователи чувствуют более тесную связь с приложением.

Взаимодействия на основе положения указателя позволяют создавать дополнительные движения и взаимодействия с пользовательским интерфейсом для вашего приложения, используя метод ввода расположения указателя на экране. Эти взаимодействия часто предоставляют дополнительный контекст и обратную связь для конечных пользователей о поведении и структуре пользовательского интерфейса. Взаимодействие более не является односторонним. Скорее, это двухсторонний поток, в котором конечный пользователь осуществляет ввод с помощью определенного метода ввода, а пользовательский интерфейс приложения может реагировать на этот ввод.

Вот несколько примеров:

- Анимация положения важных сведений так, чтобы они следовали за курсором

    ![Пример прикрепления важных сведений к указателю](images/animation/spotlight-reveal.gif)

- Поворот изображения в зависимости от положения указателя

    ![Пример поворота указателя](images/animation/pointer-rotate.gif)

## <a name="using-pointerpositionpropertyset"></a>Использование PointerPositionPropertySet

Эти взаимодействия можно создать с помощью PointerPositionPropertySet. Этот набор свойств PropertySet создается для элемента UIElement, чтобы сохранить положение указателя во время положительного тестирования нажатия UIElement. Значение положения является относительным для координатного пространства (положение <0,0>— это верхний левый угол UIElement). Затем этот набор свойств можно использовать в анимации для создания движения другого свойства.

Для каждого из разных способов ввода указателя существует несколько доступных состояний, в которых может находиться ввод, при изменении положения: наведение, нажатие, нажатие и перемещение. PointerPositionPropertySet сохраняет положение указателя мыши и пера только в состояниях наведения, нажатия, нажатия и перемещения.

Общие шаги для начала работы.

1. Определите элемент UIElement, в котором необходимо отслеживать положение указателя.
1. Осуществите доступ к набору свойств PointerPositionPropertySet через ElementCompositionPreview.
    - Передайте элемент UIElement методу ElementCompositionPreview.GetPointerPositionPropertySet.
1. Создайте анимацию ExpressionAnimation, которая ссылается на свойство Position в наборе PropertySet.
    - Не забудьте задать параметр ссылки!
1. Назначьте свойство объекта CompositionObject целевым объектом анимации ExpressionAnimation.

> [!NOTE]
> Рекомендуется назначить набор PropertySet, возвращаемый методом GetPointerPositionPropertySet, переменной класса. В этом случае набор свойств не будет удален при сборке мусора и, следовательно, никак не повлияет на анимацию ExpressionAnimation, которая содержит на него ссылку. Анимации ExpressionAnimations не поддерживают строгую ссылку ни на какой из объектов, используемых в уравнении.

## <a name="example"></a>Пример.

Рассмотрим пример, где мы используем положение наведения средства ввода "мышь" или "перо" для динамического поворота изображения.

![Пример поворота указателя](images/animation/pointer-rotate.gif)

На изображении показан элемент UIElement, поэтому давайте сначала получим ссылку на PointerPositionPropertySet

```csharp
_pointerPositionPropSet = ElementCompositionPreview.GetPointerPositionPropertySet(UIElement element);
```

В этом примере два выражения.

- Выражение, где изображение вращается с учетом того, на каком расстоянии указатель находится от центра изображения. Чем больше это расстояние, тем больше вращение.
- Выражение, где ось вращения меняется с учетом положения указателя. Ось вращения должна быть перпендикулярна вектору положения.

Определим два выражения, целевым объектом одного из которых является свойство RotationAngle, а другого— свойство RotationAxis. Ссылка на набор PointerPositionPropertySet оформляется так же, как на любой другой набор PropertySet.

В этом примере выражения создаются с использованием классов ExpressionBuilder.

```csharp
// || DEFINE THE EXPRESSION FOR THE ROTATION ANGLE ||
var hoverPosition = _pointerPositionPropSet.GetSpecializedReference
<PointerPositionPropertySetReferenceNode>().Position;
var angleExpressionNode =
EF.Conditional(
 hoverPosition == new Vector3(0, 0, 0),
 ExpressionValues.CurrentValue.CreateScalarCurrentValue(),
 35 * ((EF.Clamp(EF.Distance(center, hoverPosition), 0, distanceToCenter) % distanceToCenter) / distanceToCenter));
_tiltVisual.StartAnimation("RotationAngleInDegrees", angleExpressionNode);

// || DEFINE THE EXPRESSION FOR THE ROTATION AXIS ||
var axisAngleExpressionNode = EF.Vector3(
-(hoverPosition.Y - center.Y) * EF.Conditional(hoverPosition.Y == center.Y, 0, 1),
 (hoverPosition.X - center.X) * EF.Conditional(hoverPosition.X == center.X, 0, 1),
 0);
_tiltVisual.StartAnimation("RotationAxis", axisAngleExpressionNode);
```