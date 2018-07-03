---
author: jwmsft
Description: Learn how Fluent motion uses timing and easing functions.
title: Согласование по времени и реалистичная анимация — анимация в приложениях UWP
label: Timing and easing
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows10, UWP
pm-contact: stmoy
design-contact: jeffarn
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 412ba7e36c2bb36562ceee13bb1e204ff402a882
ms.sourcegitcommit: 517c83baffd344d4c705bc644d7c6d2b1a4c7e1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "1843921"
---
# <a name="timing-and-easing"></a>Согласование по времени и реалистичная анимация

В то время как движение основано на законах физики реального мира, мы работаем с цифровой средой, которую пользователи хотят видеть быстрой и производительной. 

## <a name="how-fluent-motion-uses-time"></a>Как в плавном движении Fluent используется время

Согласование по времени — важный элемент, позволяющий сделать движение естественным при попадании объектов в пользовательских интерфейс, выходе из него и перемещении в его пределах.

1. Объекты или сцены, попадающие в видимую область, — быстрые, но заметные. Продолжительность этих анимаций, как правило, превышает продолжительность анимаций выхода из видимой области. Это связано с тем, что сцене требуется время для иерархического формирования.
1. Объекты или сцены, покидающие видимую область, — очень быстрые. Пользователь должен понимать, куда пропал элемент пользовательского интерфейса. Однако после закрытия пользовательского интерфейса он не должен мешаться.
1. Объекты, преобразующиеся во время сцены, должны обладать продолжительностью, сопоставимой с проходимым ими расстоянием.

## <a name="timing-in-fluent-motion"></a>Согласование по времени в плавном движении Fluent

Согласование по времени для плавного движения Fluent составляет 500мс (или пол секунды) в качестве контрольной точки, так это максимальное количество времени, которое пользователь может мгновенно воспринять.

![главное изображение](images/time.gif)

### <a name="150ms-exit"></a>**150мс** (выход)

:::row::: :::column::: Используется для выходящих из сцены объектов или закрывающихся страниц.
Позволяет получить очень быструю направленную обратную связь о выходе из пользовательского интерфейса, когда согласование по времени не сказывается на частоте кадров, в результате чего достигается плавная анимация.
:::column-end::: :::column::: ![150мс, движение](images/150msAlt.gif) :::column-end::: :::row-end:::

### <a name="300ms-enter"></a>**300мс** (вход)

:::row::: :::column::: Используется для входящих в сцену объектов или открывающихся страниц.
Предоставляет достаточное количество времени для ознакомления с содержимым, входящим в сцену.
:::column-end::: :::column::: ![300мс, движение](images/300ms.gif) :::column-end::: :::row-end:::

### <a name="500ms-move"></a>**≤500мс** (перемещение)

:::row::: :::column::: Используется для объектов, преобразующихся в одной сцене или нескольких сценах. :::column-end::: :::column::: ![500мс, движение](images/500ms.gif) :::column-end::: :::row-end:::

## <a name="easing-in-fluent-motion"></a>Реалистичная анимация в плавном движении Fluent

Реалистичная анимация — это способ управления скоростью объекта в ходе его перемещения. Это связывающее звено, объединяющее все взаимодействия плавного движения Fluent. Реалистичная анимация используется для унификации физического вида объектов, перемещающихся по системе. Это один из способов имитации реального мира, позволяющий сделать так, чтобы движущиеся объекты выглядели естественно в своей среде.

![главное изображение](images/easing.gif)

## <a name="apply-easing-to-motion"></a>Применение реалистичной анимации к движению

Эти примеры реалистичных анимаций помогут сделать так, чтобы движение ваших объектов выглядело более естественным. Они являются основой при работе с плавным движением Fluent.

В примерах кода показано, как применить рекомендуемые значения реалистичной анимации к анимациям раскадровки (XAML) или анимациям композиции (C#).

### <a name="accelerate-exit"></a>**Ускорение** (выход)

:::row::: :::column::: Используется для выходящего из сцены пользовательского интерфейса или объектов.

        Objects become powered and gain momentum until they reach escape velocity.
        The resulting feel is that the object is trying its hardest to get out of the user's way and make room for new content to come in.
    :::column-end:::
    :::column:::
        ![accelerate easing](images/accelEase.gif)
    :::column-end:::
:::row-end:::

```
cubic-bezier(0.7 , 0 , 1 , 0.5)
```

```xaml
<!-- Use for XAML Storyboard animations. -->
<Storyboard x:Name="Storyboard">
    <DoubleAnimation Storyboard.TargetName="Translation" Storyboard.TargetProperty="X" From="0" To="200" Duration="0:0:0.15">
        <DoubleAnimation.EasingFunction>
            <ExponentialEase Exponent="4.5" EasingMode="EaseIn" />
        </DoubleAnimation.EasingFunction>
    </DoubleAnimation>
</Storyboard>
```

```csharp
// Use for Composition animations.
CubicBezierEasingFunction accelerate =
    _compositor.CreateCubicBezierEasingFunction(new Vector2(0.7f, 0.0f), new Vector2(1.0f, 0.5f));
_exitAnimation = _compositor.CreateScalarKeyFrameAnimation();
_exitAnimation.InsertKeyFrame(0.0f, _startValue);
_exitAnimation.InsertKeyFrame(1.0f, _endValue, accelerate);
_exitAnimation.Duration = TimeSpan.FromMilliseconds(150);
```

### <a name="decelerate-enter"></a>**Замедление** (выход)

:::row::: :::column::: Используется для входящего в сцену пользовательского интерфейса, перемещения по интерфейсу или создания подпроцессов.

        Once on-scene, the object is met with extreme friction, which slows the object to rest.
        The resulting feel is that the object traveled from a long distance away and entered at an extreme velocity, or is quickly returning to a rest state.

        Even if it's preceded by a moment of unresponsiveness, the velocity of the incoming object has the effect of feeling fast and responsive.
    :::column-end:::
    :::column:::
        ![decelerate easing](images/decelEase.gif)
    :::column-end:::
:::row-end:::

```
cubic-bezier(0.1 , 0.9 , 0.2 , 1)
```

```xaml
<!-- Use for XAML Storyboard animations. -->
<Storyboard x:Name="Storyboard">
    <DoubleAnimation Storyboard.TargetName="Translation" Storyboard.TargetProperty="X" From="0" To="200" Duration="0:0:0.3">
        <DoubleAnimation.EasingFunction>
            <ExponentialEase Exponent="7" EasingMode="EaseOut" />
        </DoubleAnimation.EasingFunction>
    </DoubleAnimation>
</Storyboard>
```

```csharp
// Use for Composition animations.
CubicBezierEasingFunction decelerate =
    _compositor.CreateCubicBezierEasingFunction(new Vector2(0.1f, 0.9f), new Vector2(0.2f, 1.0f));
_enterAnimation = _compositor.CreateScalarKeyFrameAnimation();
_enterAnimation.InsertKeyFrame(0.0f, _startValue);
_enterAnimation.InsertKeyFrame(1.0f, _endValue, decelerate);
_enterAnimation.Duration = TimeSpan.FromMilliseconds(300);
```

### <a name="standard-easing-move"></a>**Стандартная реалистичная анимация** (перемещение)

:::row::: :::column::: Это базовая реалистичная анимация для изменения любого анимируемого параметра внутри системы.
Используйте стандартную реалистичную анимацию применительно к объектам, которые изменяются от состояния к состоянию на экране (например, простое изменение положения). Также используйте ее для объектов, изменяющих форму в сцене (например, увеличение размеров объекта).

        The resulting feel is that objects changing state from A to B are overcoming, and taken over by, natural forces.
    :::column-end:::
    :::column:::
        ![standard easing](images/standardEase.gif)
    :::column-end:::
:::row-end:::

```
cubic-bezier(0.8 , 0 , 0.2 , 1)
```

```xaml
<!-- Use for XAML Storyboard animations. -->
<Storyboard x:Name="Storyboard">
    <DoubleAnimation Storyboard.TargetName="Translation" Storyboard.TargetProperty="X" From="0" To="200" Duration="0:0:0.5">
        <DoubleAnimation.EasingFunction>
            <CircleEase EasingMode="EaseInOut" />
        </DoubleAnimation.EasingFunction>
    </DoubleAnimation>
</Storyboard>
```

```csharp
// Use for Composition animations.
CubicBezierEasingFunction standard =
    _compositor.CreateCubicBezierEasingFunction(new Vector2(0.8f, 0.0f), new Vector2(0.2f, 1.0f));
 _moveAnimation = _compositor.CreateScalarKeyFrameAnimation();
 _moveAnimation.InsertKeyFrame(0.0f, _startValue);
 _moveAnimation.InsertKeyFrame(1.0f, _endValue, standard);
 _moveAnimation.Duration = TimeSpan.FromMilliseconds(500);
```

## <a name="related-articles"></a>Смежные разделы

- [Обзор движения](index.md)
- [Направление и сила притяжения](directionality-and-gravity.md)