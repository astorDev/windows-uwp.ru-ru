---
author: jwmsft
title: "Анимации времени"
description: "Используйте классы KeyFrameAnimation, чтобы менять пользовательский интерфейс во времени."
ms.author: jimwalk
ms.date: 10/10/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, анимация"
localizationpriority: medium
ms.openlocfilehash: f080e0c4409cb4f0199840e0382e54b988f1bbb1
ms.sourcegitcommit: 44a24b580feea0f188c7eae36e72e4a4f412802b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/31/2017
---
# <a name="time-based-animations"></a>Анимации на основе времени

Когда меняется пользовательский интерфейс или какой-либо его компонент, конечные пользователи могут заметить это по-разному: со временем или мгновенно. На платформе Windows первый вариант является предпочтительным: резкая смена пользовательского интерфейса вводит в заблуждение и удивляет конечных пользователей, потому что они не успевают отследить происходящее. В этом случае конечный пользователь часто ощущает дисгармонию и неестественность процесса.

Вместо этого можно менять пользовательский интерфейс постепенно, чтобы пользователь успевал освоиться, или уведомлять пользователей обо всех вносимых изменениях. На платформе Windows для этого используются анимации на основе времени или KeyFrameAnimations. Анимации KeyFrameAnimations позволяют менять пользовательский интерфейс постепенно и контролировать каждый аспект анимации, включая способ и время начала, а также способ достижения конечного состояния. Например, анимированное перемещение объекта в новое положение (продолжительностью более 300 миллисекунд) выглядит приятнее для человеческого глаза, чем немедленная "телепортация". Если вместо мгновенных изменений используются анимации, изображение и интерфейс в целом получаются более приятными и привлекательными.

## <a name="types-of-time-based-animations"></a>Типы анимаций на основе времени

Существует две категории анимаций на основе времени, которые можно использовать для создания превосходного пользовательского интерфейса в Windows.

**Явные анимации**— как видно из названия, вы в явной форме запускаете анимацию для внесения обновлений.
**Неявные анимации**— эти анимации запускаются системой от вашего имени, если выполняется какое-либо условие.

В этой статье мы рассматриваем создание и использование _явных_ анимаций на основе времени с KeyFrameAnimations.

Существуют разные типы явной и неявной анимации на основе времени, соответствующие разным типа свойство объектов CompositionObjects, которые можно анимировать.

- ColorKeyFrameAnimation
- QuaternionKeyFrameAnimation
- ScalarKeyFrameAnimation
- Vector2KeyFrameAnimation
- Vector3KeyFrameAnimation
- Vector4KeyFrameAnimation

## <a name="create-time-based-animations-with-keyframeanimations"></a>Создание анимаций на основе времени с KeyFrameAnimations

Прежде чем приступить к описанию создания явных анимаций на основе времени с KeyFrameAnimations, рассмотрим несколько понятий.

- KeyFrames— это отдельные "снимки", которые включаются в анимацию.
  - Определяются как пары "ключ-значение". Ключ представляет переход от 0 к 1, то есть указывает, в какой точке жизненного цикла анимации расположен "снимок". Другие параметры представляют значение свойства в этот момент.
- KeyFrameAnimation Properties— параметры пользовательской настройки, которые можно применить, чтобы удовлетворить потребности пользовательского интерфейса.
  - DelayTime— время перед началом анимации после вызова функции StartAnimation.
  - Duration— продолжительность анимации.
  - IterationBehavior— количество повторений (либо бесконечное повторение) анимации.
  - IterationCount— число конечных временных периодов, в течение которых повторяется анимация по ключевым кадрам.
  - KeyFrameCount— число ключевых кадров в определенной анимации по ключевым кадрам.
  - StopBehavior— определяет поведение анимируемого значения при вызове функции StopAnimation.
  - Direction— определяет направление анимации, которую необходимо воспроизвести.
- Группа анимаций — одновременный запуск нескольких анимаций.
  - Часто используется, если нужно одновременно анимировать несколько свойств.

Дополнительные сведения см. в разделе [CompositionAnimationGroup](https://docs.microsoft.com/uwp/api/windows.ui.composition.compositionanimationgroup).

Помня об этом, давай рассмотрим общую формулу создания KeyFrameAnimation:

1. Идентифицируйте CompositionObject и соответствующее свойство, которое необходимо анимировать.
1. Создайте шаблон типа KeyFrameAnimation для компоновщика, соответствующего типу свойства, которое требуется анимировать.
1. Используя шаблон анимации, начните добавлять KeyFrames и определять свойства анимации.
    - Требуется по меньшей мере один объект KeyFrame (ключевой кадр 100% или 1f).
    - Рекомендуется также определить продолжительность.
1. Как только вы будете готовы к запуску этой анимации, вызовите функцию StartAnimation(…) в объекте CompositionObject, указав в качестве целевого объекта свойство, которое требуется анимировать. В частности:
    - `Visual.StartAnimation("targetProperty", CompositionAnimation animation);`
    - `Visual.StartAnimationGroup(AnimationGroup animationGroup);`
1. Если выполняется какая-либо анимация и вы хотите остановить анимацию или группу анимаций, можно воспользоваться следующими API:
    - `Visual.StopAnimation("targetProperty");`
    - `Visual.StopAnimationGroup(AnimationGroup AnimationGroup);`

Давайте рассмотрим пример, чтобы увидеть эту формулу в действии.

## <a name="example"></a>Пример.

В этом примере анимируется смещение визуального элемента с <0,0,0> на <20,20,20> за 1 секунду. Кроме того, между указанными позициями этот визуальный элемент должен выполнить анимацию 10 раз.

![Анимация по ключевым кадрам](images/animation/animated-rectangle.gif)

Для начала вы идентифицируете объект CompositionObject и свойство, которое требуется анимировать. В этом случае красный квадрат представлен составным визуальным элементом с именем `redSquare`. Анимация начинается с этого объекта.

Затем, поскольку нужно анимировать свойство Offset, необходимо создать анимацию Vector3KeyFrameAnimation (Offset— это тип Vector3). Кроме того, вы определяете соответствующие кадры KeyFrames для анимации KeyFrameAnimation.

```csharp
    Vector3KeyFrameAnimation animation = compositor.CreateVector3KeyFrameAnimation();
    animation.InsertKeyFrame(1f, new Vector3(200f, 0f, 0f));
```

Затем мы определим свойства анимации KeyFrameAnimation, чтобы описать ее продолжительность, а также поведение анимирования между двумя позициями (текущей и <200,0,0>) 10 раз.

```csharp
    animation.Duration = TimeSpan.FromSeconds(2);
    animation.Direction = Windows.UI.Composition.AnimationDirection.Alternate;
    // Run animation for 10 times
    animation.IterationCount = 10;
```

Наконец, для выполнения анимации необходимо запустить ее в свойстве CompositionObject.

```csharp
redVisual.StartAnimation("Translation", animation);
```

Ниже приводится полный код для выполнения этого действия.

```csharp
private void AnimateSquare(Compositor compositor, SpriteVisual redSquare)
{ 
    Vector3KeyFrameAnimation animation = compositor.CreateVector3KeyFrameAnimation();
    animation.InsertKeyFrame(1f, new Vector3(200f, 0f, 0f));
    animation.Duration = TimeSpan.FromSeconds(2);
    animation.Direction = Windows.UI.Composition.AnimationDirection.Alternate;
    // Run animation for 10 times
    animation.IterationCount = 10;
    visual.StartAnimation("Translation", animation);
} 
```