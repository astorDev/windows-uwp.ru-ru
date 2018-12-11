---
title: Анимации на основе отношений
description: Создайте движение на основе свойства другого объекта.
ms.date: 10/10/2017
ms.topic: article
keywords: windows 10, uwp, анимация
ms.localizationpriority: medium
ms.openlocfilehash: b6fdc59e8a7203a3bb8c6ad79adabd446b884639
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8926300"
---
# <a name="relation-based-animations"></a>Анимации на основе отношений

В этой статье приводится краткий обзор создания анимаций на основе отношений с использованием составных анимаций ExpressionAnimations.

## <a name="dynamic-relation-based-experiences"></a>Динамические взаимодействия на основе отношений

При создании в приложении взаимодействий с движением это движение не всегда основано на времени— иногда движение зависит от свойства другого объекта. KeyFrameAnimations не позволяют легко и удобно представить такие типы взаимодействия с движением. Движение в таких сценариях не должно быть дискретным и определенным заранее. Напротив, движение может динамически адаптироваться с учетом отношений с другими свойствами объекта. Например, можно анимировать прозрачность объекта в зависимости от его горизонтального положения. Другие примеры включают такие взаимодействия с движением, как закрепленные заголовки или параллакс.

Эти типы движения позволяют создать пользовательский интерфейс, который выглядит более связно, а не разрозненно. У пользователя складывается впечатление, что в пользовательском интерфейсе происходит динамическое взаимодействие.

![Перемещающийся по орбите шар](images/animation/orbit.gif)

![Представление списка с параллаксом](images/animation/parallax.gif)

## <a name="using-expressionanimations"></a>Использование ExpressionAnimations

Чтобы создать взаимодействия типа "движение" на основе отношений, следует использовать тип ExpressionAnimation. ExpressionAnimations (или кратко Expressions)— это новый тип анимации, который позволяет выражать математическую связь— связь, которую система использует для вычисления значения свойства анимации в каждом кадре. Другими словами, Expressions— это просто математическое выражение, которое определяет желаемое значение анимируемого свойства на кадр. Expressions— это универсальный компонент, который можно использовать в разнообразных сценариях, включая следующие:

- Относительный размер, анимации смещения.
- Закрепленные заголовки, параллакс с ScrollViewer. (См. [Расширение возможностей существующих взаимодействий ScrollViewer](scroll-input-animations.md).)
- Точки прикрепления с модификаторами InertiaModifiers и модулем InteractionTracker. (См. [Создание точек прикрепления с модификаторами инерции](inertia-modifiers.md).)

При работе с ExpressionAnimations существует несколько вещей, о которых требуется упомянуть заранее:

- Бесконечность: в отличие от родственного типа KeyFrameAnimation, у Expressions нет определенного срока действия. Поскольку Expressions— это математические отношения, это анимации, которые выполняются бесконечно. При желании эти анимации можно остановить.
- Выполняется, но не всегда оценивает: производительность— это всегда важный момент в работе с непрерывно выполняющимися анимациями. Однако не следует беспокоиться, систем достаточна умна, чтобы Expression выполняло переоценку, только если какой-либо ввод или параметр изменится.
- Разрешение в правильный тип объекта: поскольку Expressions— это математические отношения, важно убедиться, что выражение, определяющее Expression, разрешается в тот же тип свойства, на которое нацелена анимация. Например, при анимировании смещения Expression должно разрешаться в тип Vector3.

### <a name="components-of-an-expression"></a>Компоненты Expression

При создании математического отношения Expression используется несколько базовых компонентов:

- Параметры: значения, представляющие значения-константы или ссылки на другие составные объекты.
- Математические операторы: стандартные математические операторы, такие как плюс (+), минус (-), умножить (*), разделить(/), которые объединяют параметры в выражение. Также включены условные операторы: больше (>), равно (==), тернарный оператор (условие ? ifTrue : ifFalse) и т. д.
- Математические функции: математические функции/ярлыки на основе System.Numerics. Полный список поддерживаемых функций см. в разделе [ExpressionAnimation](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.ExpressionAnimation).

Expressions также поддерживают набор ключевых слов— специальных фраз, имеющих определенное значение только в масштабах системы ExpressionAnimation. Они перечислены (вместе с полным списком математических функций) в документации [ExpressionAnimation](https://docs.microsoft.com/uwp/api/Windows.UI.Composition.ExpressionAnimation).

### <a name="creating-expressions-with-expressionbuilder"></a>Создание выражений Expressions с помощью ExpressionBuilder

Существует два варианта создания Expressions в приложении UWP:

1. Создание выражения в виде строки через официальный, общедоступный API.
1. Создание выражения в типобезопасной объектной модели через инструмент ExpressionBuilder с открытым исходным кодом. См. раздел [Исходный код и документация Github](https://github.com/Microsoft/WindowsUIDevLabs/tree/master/ExpressionBuilder).

В контексте этого документа выражения Expressions определяются с использованием ExpressionBuilder.

### <a name="parameters"></a>Параметры

Параметры— это основа выражения Expression. Существует два типа параметров:

- Константы: эти параметры представляют типизированные переменные System.Numeric. Эти параметры получают значения, назначенные однократно при запуске анимации.
- Ссылки: эти параметры представляют ссылки на объекты CompositionObjects, их значения непрерывно обновляются после начала анимации.

Как правило, ссылки— это основной аспект, определяющий способ динамического изменения вывода Expression. Вывод Expression меняется в результате изменения этих ссылок. Если создать Expression со строками Strings или использовать их в шаблонном сценарии (используя Expression для работы с несколькими объектами CompositionObjects), потребуется назвать используемые параметры и задать их значения. См. дополнительные сведения в разделе "Пример".

### <a name="working-with-keyframeanimations"></a>Работа с KeyFrameAnimations

Expressions также могут использоваться с KeyFrameAnimations. В этих примерах Expression следует использовать для определения значения KeyFrame в определенной временной точке. Такие типы KeyFrames называются ExpressionKeyFrames.

```csharp
KeyFrameAnimation.InsertExpressionKeyFrame(Single, String)
KeyFrameAnimation.InsertExpressionKeyFrame(Single, ExpressionNode)
```

Однако в отличие от ExpressionAnimations, ExpressionKeyFrames оцениваются только после запуска KeyFrameAnimation. Помните, что вы передаете ExpressionAnimation не в качестве значения KeyFrame, а как строку (или ExpressionNode, если используется ExpressionBuilder).

## <a name="example"></a>Пример

А сейчас рассмотрим пример использования Expressions, в частности пример PropertySet из коллекции примеров пользовательского интерфейса Windows. Рассмотрим Expression, управляющее поведением перемещения по орбите синего шара.

![Перемещающийся по орбите шар](images/animation/orbit.gif)

Все взаимодействие обеспечивается тремя компонентами:

1. KeyFrameAnimation, анимирующая смещение красного шара по оси Y.
1. PropertySet со свойством **Rotation**, которое обеспечивает движение орбиты, анимируемое другой анимацией KeyFrameAnimation.
1. ExpressionAnimation, определяющая смещение синего шара в зависимости от смещения красного шара и свойство Rotation, сохраняющее идеальную орбиту.

Рассмотрим анимацию ExpressionAnimation, определенную в пункте 3. Кроме того, для создания этого выражения Expression будут использоваться классы ExpressionBuilder. Копия кода, используемого для создания этого взаимодействия через строки Strings, приводится в конце.

В этом выражении существует два свойства, на которые необходимо сослаться из PropertySet; одно— это смещение относительно центральной точки; другое— вращение.

```
var propSetCenterPoint =
_propertySet.GetReference().GetVector3Property("CenterPointOffset");

// This rotation value will animate via KFA from 0 -> 360 degrees
var propSetRotation = _propertySet.GetReference().GetScalarProperty("Rotation");
```

Затем необходимо определить компонент Vector3, обеспечивающий фактическое перемещение по орбите.

```
var orbitRotation = EF.Vector3(
    EF.Cos(EF.ToRadians(propSetRotation)) * 150,
    EF.Sin(EF.ToRadians(propSetRotation)) * 75, 0);
```

> [!NOTE]
> `EF` это сокращенное обозначение, "использующее" нотацию для определения функций ExpressionBuilder.ExpressionFunctions.

Наконец, необходимо объединить эти компоненты и сослаться на положение красного шара, чтобы определить математическое отношение.

```
var orbitExpression = redSprite.GetReference().Offset + propSetCenterPoint + orbitRotation;
blueSprite.StartAnimation("Offset", orbitExpression);
```

Гипотетическая ситуация: что если бы нужно было использовать то же выражение Expression, но с двумя другими визуальными элементами Visuals, то есть если бы перемещающихся по орбите шаров было 2. Благодаря CompositionAnimations можно повторно использовать анимацию и делать целевыми несколько объектов CompositionObjects. Единственное, что нужно изменить, при использовании этого выражения Expression для работы с дополнительной орбитой, это ссылка на визуальный элемент Visual. Это называется "шаблоном".

В этом случае меняется выражение Expression, созданное ранее. Вместо того чтобы "получать" ссылку на CompositionObject, вы создаете ссылку с именем, а затем назначаете ей разные значения:

```
var orbitExpression = ExpressionValues.Reference.CreateVisualReference("orbitRoundVisual");
orbitExpression.SetReferenceParameter("orbitRoundVisual", redSprite);
blueSprite.StartAnimation("Offset", orbitExpression);
// Later on … use same Expression to assign to another orbiting Visual
orbitExpression.SetReferenceParameter("orbitRoundVisual", yellowSprite);
greenSprite.StartAnimation("Offset", orbitExpression);
```

вот код для сценария, когда Expression определяется с использованием строк Strings через общедоступный API.

```
ExpressionAnimation expressionAnimation =
compositor.CreateExpressionAnimation("visual.Offset + " +
"propertySet.CenterPointOffset + " +
"Vector3(cos(ToRadians(propertySet.Rotation)) * 150," + "sin(ToRadians(propertySet.Rotation)) * 75, 0)");
 var propSetCenterPoint = _propertySet.GetReference().GetVector3Property("CenterPointOffset");
 var propSetRotation = _propertySet.GetReference().GetScalarProperty("Rotation");
expressionAnimation.SetReferenceParameter("propertySet", _propertySet);
expressionAnimation.SetReferenceParameter("visual", redSprite);
```
