---
Description: Узнайте, как в свободном движении используются функции времени и плавности.
title: Согласование по времени и реалистичная анимация
label: Timing and easing
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 098a75da573a977aa393197a61a62b0337f0dc06
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970509"
---
# <a name="timing-and-easing"></a>Согласование по времени и реалистичная анимация

В то время как движение основано на законах физики реального мира, мы работаем с цифровой средой, которую пользователи хотят видеть быстрой и производительной.

## <a name="examples"></a>Примеры

<table>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> установлено, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/EasingFunction">открыть приложение, и см. раздел ускорение функций в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="how-fluent-motion-uses-time"></a>Как в плавном движении Fluent используется время

Согласование по времени — важный элемент, позволяющий сделать движение естественным при попадании объектов в пользовательских интерфейс, выходе из него и перемещении в его пределах.

1. Объекты или сцены, попадающие в видимую область, — быстрые, но заметные. Продолжительность этих анимаций, как правило, превышает продолжительность анимаций выхода из видимой области. Это связано с тем, что сцене требуется время для иерархического формирования.
1. Объекты или сцены, покидающие видимую область, — очень быстрые. Пользователь должен понимать, куда пропал элемент пользовательского интерфейса. Однако после закрытия пользовательского интерфейса он не должен мешаться.
1. Объекты, преобразующиеся во время сцены, должны обладать продолжительностью, сопоставимой с проходимым ими расстоянием.

## <a name="timing-in-fluent-motion"></a>Согласование по времени в плавном движении Fluent

Согласование по времени для плавного движения Fluent составляет 500 мс (или пол секунды) в качестве контрольной точки, так это максимальное количество времени, которое пользователь может мгновенно воспринять.

![Главное изображение](images/time.gif)

### <a name="150ms-exit"></a>**150 мс** (выход)

:::row:::
    :::column:::
Используется для объектов или страниц, которые завершают сцену или закрываются.
Позволяет получить очень быструю направленную обратную связь о выходе из пользовательского интерфейса, когда согласование по времени не сказывается на частоте кадров, в результате чего достигается плавная анимация.
    :::column-end:::
    :::column:::
        ![движение 150ms](images/150msAlt.gif)
    :::column-end:::
:::row-end:::

### <a name="300ms-enter"></a>**300 мс** (вход)

:::row:::
    :::column:::
Используется для объектов или страниц, которые вводятся в сцену или открываются.
Предоставляет достаточное количество времени для ознакомления с содержимым, входящим в сцену.
    :::column-end:::
    :::column:::
        ![Движение 300 мс](images/300ms.gif)
    :::column-end:::
:::row-end:::

### <a name="500ms-move"></a>**≤500 мс** (перемещение)

:::row:::
    :::column:::
Используется для объектов, переводимых по одной сцене или в нескольких сценах. 
    :::column-end:::
    :::column:::
        ![Движение 500 мс](images/500ms.gif)
    :::column-end:::
:::row-end:::

## <a name="easing-in-fluent-motion"></a>Реалистичная анимация в плавном движении Fluent

Реалистичная анимация — это способ управления скоростью объекта в ходе его перемещения. Это связывающее звено, объединяющее все взаимодействия плавного движения Fluent. Реалистичная анимация используется для унификации физического вида объектов, перемещающихся по системе. Это один из способов имитации реального мира, позволяющий сделать так, чтобы движущиеся объекты выглядели естественно в своей среде.

![Главное изображение](images/easing.gif)

## <a name="apply-easing-to-motion"></a>Применение реалистичной анимации к движению

Эти примеры реалистичных анимаций помогут сделать так, чтобы движение ваших объектов выглядело более естественным. Они являются основой при работе с плавным движением Fluent.

В примерах кода показано, как применить рекомендуемые значения реалистичной анимации к анимациям раскадровки (XAML) или анимациям композиции (C#).

### <a name="accelerate-exit"></a>**Ускорение** (выход)

:::row:::
    :::column:::
Используется для пользовательского интерфейса или объектов, которые завершают сцену.

Объекты становятся недоступными и получают импульс до достижения скорости экранирования.
Полученный результат заключается в том, что объект пытается избавиться от пользовательского способа и освободить место для нового содержимого.
    :::column-end:::
    :::column:::
        ![ускорение замедления](images/accelEase.gif)
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

:::row:::
    :::column:::
Используется для объектов или пользовательского интерфейса, входящих в сцену, либо для перемещения, либо для порождения.

После выполнения на сцене объект достигается с помощью экстремального трения, что снижает скорость работы объекта.
Полученный результат заключается в том, что объект уходит с длинного расстояния и записывается на экстремальную скорость или быстро возвращается в состояние RESTful.

Даже если он предшествует моменту невозможности реагирования, скорость входящего объекта влияет на быстроту и скорость реагирования.
    :::column-end:::
    :::column:::
        ![замедление замедления](images/decelEase.gif)
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

:::row:::
    :::column:::
Это базовый уровень замедления для любого изменения анимированных параметров в системе.
Используйте стандартную реалистичную анимацию применительно к объектам, которые изменяются от состояния к состоянию на экране (например, простое изменение положения). Также используйте ее для объектов, изменяющих форму в сцене (например, увеличение размеров объекта).

Полученный результат заключается в том, что объекты, изменяющие состояние с A на B, переходят и записываются естественными принудительными.
    :::column-end:::
    :::column:::
        ![Стандартная замедление](images/standardEase.gif)
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

## <a name="related-articles"></a>Похожие статьи

- [Обзор движения](index.md)
- [Направление и сила притяжения](directionality-and-gravity.md)
