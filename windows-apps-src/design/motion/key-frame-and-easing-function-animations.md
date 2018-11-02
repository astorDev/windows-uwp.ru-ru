---
author: Jwmsft
title: Анимации по ключевым кадрам и на основе функций для реалистичной анимации
ms.assetid: D8AF24CD-F4C2-4562-AFD7-25010955D677
description: Линейные анимации по ключевым кадрам, анимации по ключевым кадрам со значением KeySpline и функции для реалистичной анимации —это три различные методики реализации приблизительно одного и того же сценария.
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2076866cc31f50bd7ccfc6730bca5be96ffdc7c3
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5938537"
---
# <a name="key-frame-animations-and-easing-function-animations"></a>Анимации по ключевым кадрам и на основе функций для реалистичной анимации



Линейные анимации по ключевым кадрам, анимации по ключевым кадрам со значением **KeySpline** или функции для реалистичной анимации–это три различных методики реализации приблизительно одного и того же сценария: создания анимации раскадровки, которая является немного более сложной и использует поведение нелинейной анимации из начального состояния в конечное состояние.

## <a name="prerequisites"></a>Необходимые условия

Обязательно ознакомьтесь с разделом [Анимации раскадровки](storyboarded-animations.md). Данный раздел основан на понятиях анимации, которые объясняются в разделе [Анимации раскадровки](storyboarded-animations.md) и не будут описаны здесь. Например, в разделе [Анимации раскадровки](storyboarded-animations.md) приводится описание нацеливания на анимации, раскадровок как ресурсов, значений свойства [**Timeline**](https://msdn.microsoft.com/library/windows/apps/BR210517), например [**Duration**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.duration), [**FillBehavior**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.fillbehavior) и т.д.

## <a name="animating-using-key-frame-animations"></a>Анимация по ключевым кадрам

Анимации по ключевым кадрам допускают несколько целевых значений, которые достигаются в определенный момент по временной шкале анимации. Другими словами, каждый ключевой кадр может указывать различное промежуточное значение, последний достигнутый ключевой кадр является конечным значением анимации. Указав несколько значений для анимирования, вы можете создавать более сложные анимации. Кроме того, анимации по ключевым кадрам включают различную логику интерполяции, каждая из которых реализуется как отдельный подкласс **KeyFrame** в каждом типе анимации. В частности, каждый тип анимации по ключевым кадрам имеет вариант **Discrete**, **Linear**, **Spline** и **Easing** класса **KeyFrame** для указания ключевых кадров. Например, чтобы указать анимацию, которая нацеливается на [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx) и использует ключевые кадры, можно объявить ключевые кадры с помощью [**DiscreteDoubleKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR243130), [**LinearDoubleKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR210316), [**SplineDoubleKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR210446) и [**EasingDoubleKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR210269). Вы можете использовать один или все эти типы в одной коллекции **KeyFrames**, чтобы изменять интерполяцию при каждом достижении нового ключевого кадра.

В поведении интерполяции каждый ключевой кадр управляет интерполяцией, пока не достигается время **KeyTime**. Также в это время достигается значение **Value**. Если существуют дополнительные ключевые кадры, то это значение становится начальным значением следующего ключевого кадра в последовательности.

В момент начала анимации, если не существует ключевого кадра со значением **KeyTime**, равным "0:0:0", начальным значением является неанимированное значение свойства. Это аналогично поведению анимации **From**/**To**/**By** при отсутствии **From**.

Продолжительность анимации по ключевым кадрам неявно равна самому высокому значению **KeyTime**, заданному в любом из ее ключевых кадров. Если требуется, вы можете задать явное значение [**Duration**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.duration), но следите за тем, чтобы оно не было меньше значения **KeyTime** в собственных ключевых кадрах; в противном случае вы обрежете часть анимации.

Помимо [**Duration**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.duration), в анимации по ключевым кадрам можно задать все свойства, основанные на [**Timeline**](https://msdn.microsoft.com/library/windows/apps/BR210517), как в случае анимации **From**/**To**/**By**, поскольку классы анимации по ключевым кадрам также наследуются от **Timeline**. К ним относятся следующие:

-   [**AutoReverse**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.autoreverse): по достижении последнего ключевого кадра воспроизведение кадров повторяется в обратном порядке, начиная с конца. Это удваивает кажущуюся продолжительность анимации.
-   [**BeginTime**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.begintime): задерживает запуск анимации. Отсчет временной шкалы для значений **KeyTime** в кадрах не начинается, пока не будет достигнуто значение **BeginTime**, что исключает риск обрезания кадров.
-   [**FillBehavior**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.fillbehavior): определение дальнейших действий по достижении последнего ключевого кадра. **FillBehavior** не влияет на какие-либо промежуточные ключевые кадры.
-   [**RepeatBehavior**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.repeatbehaviorproperty):
    -   Если задано значение **Forever**, ключевые кадры и их временная шкала повторяются бесконечно.
    -   Если задан счетчик итерации, временная шкала повторяется указанное количество раз.
    -   Если задано значение [**Duration**](https://msdn.microsoft.com/library/windows/apps/BR242377), временная шкала повторяется, пока не будет достигнуто это значение времени. Это может привести к усечению анимации во время последовательности ключевых кадров, если значение не является целым множителем значения неявной продолжительности временной шкалы.
-   [**SpeedRatio**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.speedratioproperty) (не является широко используемым)

### <a name="linear-key-frames"></a>Линейные ключевые кадры

Линейные ключевые кадры приводят к простой линейной интерполяции значения, пока не будет достигнуто значение **KeyTime** кадра. Это поведение интерполяции очень похоже на более простые анимации **From**/**To**/**By**, описанные в разделе [Анимации раскадровки](storyboarded-animations.md).

Ниже приводится описание использования анимации по ключевым кадрам для масштабирования отображаемой высоты прямоугольника с помощью линейных ключевых кадров. В этом примере выполняется анимация, в которой высота прямоугольника немного увеличивается линейно в течение первых 4 секунд, а затем быстро масштабируется в течение последней секунды, пока высота прямоугольника не увеличится в два раза по сравнению с начальной высотой.

```xml
<StackPanel>
    <StackPanel.Resources>
        <Storyboard x:Name="myStoryboard">
            <DoubleAnimationUsingKeyFrames
              Storyboard.TargetName="myRectangle"
              Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)">
                <LinearDoubleKeyFrame Value="1" KeyTime="0:0:0"/>
                <LinearDoubleKeyFrame Value="1.2" KeyTime="0:0:4"/>
                <LinearDoubleKeyFrame Value="2" KeyTime="0:0:5"/>
            </DoubleAnimationUsingKeyFrames>
        </Storyboard>
    </StackPanel.Resources>
</StackPanel>
```

### <a name="discrete-key-frames"></a>Дискретные ключевые кадры

Дискретные ключевые кадры не используют интерполяцию вовсе. По достижении значения **KeyTime** просто применяется новое значение **Value**. В зависимости от того, какое свойство пользовательского интерфейса анимируется, это часто приводит к тому, что кажется, будто анимация "прыгает". Убедитесь, что это именно то поведение, которое вам нужно. Вы можете свести к минимуму видимые прыжки, увеличив количество объявляемых ключевых кадров, но если вам требуется плавная анимация, рекомендуется использовать линейные или сплайновые ключевые кадры.

> [!NOTE]
> Дискретные ключевые кадры— это единственный способ анимировать значение, которое не относится к типам [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx), [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870) и [**Color**](https://msdn.microsoft.com/library/windows/apps/Hh673723), с помощью [**DiscreteObjectKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR243132). Более подробное описание процесса приводится далее в этом разделе.

### <a name="spline-key-frames"></a>Сплайновые ключевые кадры

Сплайновый ключевой кадр создает изменяемый переход между значениями в соответствии со значением свойства **KeySpline**. Это свойство указывает первую и вторую контрольные точки кривой Безье, которая описывает ускорение анимации. По сути, [**KeySpline**](https://msdn.microsoft.com/library/windows/apps/BR210307) определяет отношение функции и времени, где график зависимости функции от времени является формой этой кривой Безье. Обычно указывается значение **KeySpline** в строке собирательного атрибута XAML, содержащей четыре значения [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx), разделенные пробелами или запятыми. Эти значения являются парами "X,Y" двух контрольных точек кривой Безье. "X"–это время, а "Y"–модификатор функции в зависимости от значения. Каждое значение всегда должно находиться в диапазоне от 0 до 1 включительно. Без модификации контрольной точки в соответствии со значением **KeySpline** прямая линия в диапазоне от (0;0) до (1;1) является представлением функции во времени для линейной интерполяции. Контрольные точки изменяют форму этой кривой и, следовательно, поведение функции во времени для сплайновой анимации. Возможно, визуально это лучше рассматривать как график. Вы можете выполнить [образец визуализатора ключ-сплайн Silverlight](http://samples.msdn.microsoft.com/Silverlight/SampleBrowser/index.htm#/?sref=KeySplineExample) в браузере, чтобы просмотреть, как контрольные точки изменяют кривую и как выполняется анимация примера при использовании его в качестве значения **KeySpline**.

В следующем примере показано три различных ключевых кадра, применяемых к анимации, которая является ключевой сплайновой анимацией для значения [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx) ([**SplineDoubleKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR210446)). Обратите внимание на строку "0.6,0.0 0.9,0.00", применяемую к **KeySpline**. В результате создается кривая, в которой анимация сначала выполняется медленно, а затем быстро достигает этого значения незадолго до достижения **KeyTime**.

```xml
<Storyboard x:Name="myStoryboard">
    <!-- Animate the TranslateTransform's X property
        from 0 to 350, then 50,
        then 200 over 10 seconds. -->
    <DoubleAnimationUsingKeyFrames
        Storyboard.TargetName="MyAnimatedTranslateTransform"
        Storyboard.TargetProperty="X"
        Duration="0:0:10" EnableDependentAnimation="True">

        <!-- Using a LinearDoubleKeyFrame, the rectangle moves 
            steadily from its starting position to 500 over 
            the first 3 seconds.  -->
        <LinearDoubleKeyFrame Value="500" KeyTime="0:0:3"/>

        <!-- Using a DiscreteDoubleKeyFrame, the rectangle suddenly 
            appears at 400 after the fourth second of the animation. -->
        <DiscreteDoubleKeyFrame Value="400" KeyTime="0:0:4"/>

        <!-- Using a SplineDoubleKeyFrame, the rectangle moves 
            back to its starting point. The
            animation starts out slowly at first and then speeds up. 
            This KeyFrame ends after the 6th second. -->
        <SplineDoubleKeyFrame KeySpline="0.6,0.0 0.9,0.00" Value="0" KeyTime="0:0:6"/>
    </DoubleAnimationUsingKeyFrames>
</Storyboard>
```

### <a name="easing-key-frames"></a>Ключевые кадры для реалистичной анимации

Ключевой кадр для реалистичной анимации — это ключевой кадр, в котором применяется интерполяция, а управление зависимостью функции от времени интерполяции осуществляется с помощью нескольких предопределенных математических формул. Фактически, используя типы функции для реалистичной анимации, вы можете получить почти такой же результат, что и при использовании сплайнового ключевого кадра, однако существуют некоторые функции для реалистичной анимации, например [**BackEase**](https://msdn.microsoft.com/library/windows/apps/BR243049), которые невозможно воспроизвести с помощью сплайна.

Чтобы применить функцию для реалистичной анимации к ключевому кадру для реалистичной анимации, установите свойство **EasingFunction** в качестве элемента свойства в XAML для данного ключевого кадра. В качестве значения укажите элемент объекта для одного из типов функции для реалистичной анимации.

В этом примере [**CubicEase**](https://msdn.microsoft.com/library/windows/apps/BR243126), а затем [**BounceEase**](https://msdn.microsoft.com/library/windows/apps/BR243057) применяются в качестве последовательных ключевых кадров к [**DoubleAnimation**](https://msdn.microsoft.com/library/windows/apps/BR243136) для создания эффекта отскакивания.

```xml
<Storyboard x:Name="myStoryboard">
    <DoubleAnimationUsingKeyFrames Duration="0:0:10"
        Storyboard.TargetProperty="Height"
        Storyboard.TargetName="myEllipse">

        <!-- This keyframe animates the ellipse up to the crest 
            where it slows down and stops. -->
        <EasingDoubleKeyFrame Value="-300" KeyTime="00:00:02">
            <EasingDoubleKeyFrame.EasingFunction>
                <CubicEase/>
            </EasingDoubleKeyFrame.EasingFunction>
        </EasingDoubleKeyFrame>

        <!-- This keyframe animates the ellipse back down and makes
            it bounce. -->
        <EasingDoubleKeyFrame Value="0" KeyTime="00:00:06">
            <EasingDoubleKeyFrame.EasingFunction>
                <BounceEase Bounces="5"/>
            </EasingDoubleKeyFrame.EasingFunction>
        </EasingDoubleKeyFrame>
    </DoubleAnimationUsingKeyFrames>
</Storyboard>
```

Это всего лишь один из примеров функции для реалистичной анимации. Подробнее о них вы узнаете в следующем разделе.

## <a name="easing-functions"></a>Функции для реалистичной анимации

Функции для реалистичной анимации позволяют применять настраиваемые математические формулы к анимациям. Математические операции часто полезны для создания анимаций, которые имитируют реальную физику в двухмерной системе координат. Например, вы хотите, чтобы объект реалистично отскакивал или вел себя так, будто закреплен на пружине. Вы могли бы использовать анимацию по ключевым кадрам или даже анимацию **From**/**To**/**By**, чтобы приблизиться к таким эффектам, но это потребовало бы значительного объема работы, а результат все равно был бы менее точным, чем в случае применения математической формулы.

Функции для реалистичной анимации можно применять к анимациям тремя способами.

-   Используя ключевой кадр для реалистичной анимации в анимации по ключевым кадрам, как описано в предыдущем разделе. Используйте [**EasingColorKeyFrame.EasingFunction**](https://msdn.microsoft.com/library/windows/apps/BR210267), [**EasingDoubleKeyFrame.EasingFunction**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.easingdoublekeyframe.easingfunction.aspx) или [**EasingPointKeyFrame.EasingFunction**](https://msdn.microsoft.com/library/windows/apps/BR210279).
-   Задав свойство **EasingFunction** в одном из типов анимации **From**/**To**/**By**. Используйте [**ColorAnimation.EasingFunction**](https://msdn.microsoft.com/library/windows/apps/BR243075), [**DoubleAnimation.EasingFunction**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.doubleanimation.easingfunction.aspx) или [**PointAnimation.EasingFunction**](https://msdn.microsoft.com/library/windows/apps/BR210354).
-   Задав [**GeneratedEasingFunction**](https://msdn.microsoft.com/library/windows/apps/BR209037) как часть [**VisualTransition**](https://msdn.microsoft.com/library/windows/apps/BR209034). Это характерно для определения визуальных состояний элементов управления. Дополнительные сведения см. в разделе [**GeneratedEasingFunction**](https://msdn.microsoft.com/library/windows/apps/BR209037) или [Раскадровки для визуальных состояний](https://msdn.microsoft.com/library/windows/apps/xaml/JJ819808).

Ниже представлен список функций для реалистичной анимации.

-   [**BackEase**](https://msdn.microsoft.com/library/windows/apps/BR243049): объект немного отводится в противоположную сторону, прежде чем начинает двигаться по заданному пути.
-   [**BounceEase**](https://msdn.microsoft.com/library/windows/apps/BR243057): создается эффект отскакивания.
-   [**CircleEase**](https://msdn.microsoft.com/library/windows/apps/BR243063): создается анимация, которая ускоряется или замедляется на основании круговой функции.
-   [**CubicEase**](https://msdn.microsoft.com/library/windows/apps/BR243126): создается анимация, которая ускоряется или замедляется на основании формулы f(t)=t3.
-   [**ElasticEase**](https://msdn.microsoft.com/library/windows/apps/BR210282): создается анимация, напоминающая колебания пружины взад и вперед до полной остановки.
-   [**ExponentialEase**](https://msdn.microsoft.com/library/windows/apps/BR210294): создается анимация, которая ускоряется или замедляется на основании экспоненциальной формулы.
-   [**PowerEase**](https://msdn.microsoft.com/library/windows/apps/BR210399): создается анимация, которая ускоряется или замедляется на основании формулы f(t) = tp, где p равно свойству [**Power**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.powerease.power).
-   [**QuadraticEase**](https://msdn.microsoft.com/library/windows/apps/BR210403): создается анимация, которая ускоряется или замедляется на основании формулы f(t)=t2.
-   [**QuarticEase**](https://msdn.microsoft.com/library/windows/apps/BR210405): создается анимация, которая ускоряется или замедляется на основании формулы f(t)=t4.
-   [**QuinticEase**](https://msdn.microsoft.com/library/windows/apps/BR210407): создается анимация, которая ускоряется или замедляется на основании формулы f(t)=t5.
-   [**SineEase**](https://msdn.microsoft.com/library/windows/apps/BR210439): создается анимация, которая ускоряется или замедляется на основании синусоидальной функции.

Некоторые из функций для реалистичной анимации имеют собственные свойства. Например, [**BounceEase**](https://msdn.microsoft.com/library/windows/apps/BR243057) имеет два свойства [**Bounces**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.bounceease.bounces.aspx) и [**Bounciness**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.bounceease.bounciness.aspx), которые изменяют поведение функции во времени данной конкретной функции **BounceEase**. Другие функции для реалистичной анимации, такие как [**CubicEase**](https://msdn.microsoft.com/library/windows/apps/BR243126), не имеют иных свойств, кроме свойства [**EasingMode**](https://msdn.microsoft.com/library/windows/apps/BR210275), общего для всех функций для реалистичной анимации, и всегда создают одинаковое поведение функции во времени.

Некоторые из этих функций для реалистичной анимации немного перекрываются в зависимости от настроек свойств функций для реалистичной анимации, имеющих их. Например, [**QuadraticEase**](https://msdn.microsoft.com/library/windows/apps/BR210403) абсолютно совпадает с [**PowerEase**](https://msdn.microsoft.com/library/windows/apps/BR210399) со значением [**Power**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.powerease.power), равным 2. Кроме того, [**CircleEase**](https://msdn.microsoft.com/library/windows/apps/BR243063) обычно является значением по умолчанию [**ExponentialEase**](https://msdn.microsoft.com/library/windows/apps/BR210294).

Функция для реалистичной анимации [**BackEase**](https://msdn.microsoft.com/library/windows/apps/BR243049) уникальна, поскольку она может изменять значение за пределами обычного диапазона, заданного с помощью **From**/**To** или значений ключевых кадров. Она запускает анимацию, изменяя значение в направлении, обратном тому, которое ожидалось бы при обычном поведении **From**/**To**, снова возвращается к **From** или начальному значению, а затем выполняет анимацию в обычном режиме.

В предыдущем примере мы показали, как объявить функцию для реалистичной анимации в случае анимации по ключевым кадрам. В следующем примере функция для реалистичной анимации применяется к анимации **From**/**To**/**By**.

```xml
<StackPanel x:Name="LayoutRoot" Background="White">
    <StackPanel.Resources>
        <Storyboard x:Name="myStoryboard">
            <DoubleAnimation From="30" To="200" Duration="00:00:3" 
                Storyboard.TargetName="myRectangle" 
                Storyboard.TargetProperty="(UIElement.RenderTransform).(ScaleTransform.ScaleY)">
                <DoubleAnimation.EasingFunction>
                    <BounceEase Bounces="2" EasingMode="EaseOut" 
                                Bounciness="2"/>
                </DoubleAnimation.EasingFunction>
            </DoubleAnimation>
        </Storyboard>
    </StackPanel.Resources>
    <Rectangle x:Name="myRectangle" Fill="Blue" Width="200" Height="30"/>
</StackPanel>
```

Когда функция для реалистичной анимации применяется к анимации **From**/**To**/**By**, она изменяет характеристики функции во времени, связанные с тем, как значение интерполируется между значениями **From** и **To** во время ([**Duration**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.timeline.duration)) анимации. Без функции для реалистичной анимации это была бы линейная интерполяция.

## <a name="span-iddiscreteobjectvalueanimationsspanspan-iddiscreteobjectvalueanimationsspanspan-iddiscreteobjectvalueanimationsspandiscrete-object-value-animations"></a><span id="Discrete_object_value_animations"></span><span id="discrete_object_value_animations"></span><span id="DISCRETE_OBJECT_VALUE_ANIMATIONS"></span>Дискретные анимации значений объектов

Один тип анимации заслуживает особого внимания, поскольку это единственный способ применения анимированного значения к свойствам, не принадлежащим к типу [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx), [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870) или [**Color**](https://msdn.microsoft.com/library/windows/apps/Hh673723). Это анимация по ключевым кадрам [**ObjectAnimationUsingKeyFrames**](https://msdn.microsoft.com/library/windows/apps/BR210320). Анимирование с помощью значений [**Object**](https://msdn.microsoft.com/library/windows/apps/xaml/system.object.aspx) отличается, поскольку возможность интерполяции значений между кадрами отсутствует. По достижении значения [**KeyTime**](https://msdn.microsoft.com/library/windows/apps/BR210342) кадра анимированное значение незамедлительно становится равным значению, указанному в **Value** ключевого кадра. Поскольку интерполяции нет, существует только один ключевой кадр, который можно использовать в коллекции ключевых кадров **ObjectAnimationUsingKeyFrames**: [**DiscreteObjectKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR243132).

Значение [**Value**](https://msdn.microsoft.com/library/windows/apps/BR210344) [**DiscreteObjectKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR243132) часто задается с помощью синтаксиса элемента свойства, поскольку значение объекта, которое вы пытаетесь задать, часто не может быть выражено в качестве строки для заполнения **Value** в синтаксисе атрибута. Вы по-прежнему можете использовать синтаксис атрибута, если используется образец, например [StaticResource](https://msdn.microsoft.com/library/windows/apps/Mt185588).

Одним из случаев, когда можно увидеть [**ObjectAnimationUsingKeyFrames**](https://msdn.microsoft.com/library/windows/apps/BR210320), используемый в шаблонах по умолчанию, является ситуация, когда свойство шаблона ссылается на ресурс [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush). Эти ресурсы являются объектами [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962), а не только значением [**Color**](https://msdn.microsoft.com/library/windows/apps/Hh673723), и используют ресурсы, определенные как системные темы ([**ThemeDictionaries**](https://msdn.microsoft.com/library/windows/apps/BR208807)). Их можно назначить непосредственно значению типа **Brush**, такому как [**TextBlock.Foreground**](https://msdn.microsoft.com/library/windows/apps/BR209665); непрямое нацеливание использовать не требуется. Но поскольку **SolidColorBrush** не является [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx), [**Point**](https://msdn.microsoft.com/library/windows/apps/BR225870) или **Color**, необходимо применить **ObjectAnimationUsingKeyFrames** для использования ресурса.

```xml
<Style x:Key="TextButtonStyle" TargetType="Button">
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="Button">
                <Grid Background="Transparent">
                    <TextBlock x:Name="Text"
                        Text="{TemplateBinding Content}"/>
                    <VisualStateManager.VisualStateGroups>
                        <VisualStateGroup x:Name="CommonStates">
                            <VisualState x:Name="Normal"/>
                            <VisualState x:Name="PointerOver">
                                <Storyboard>
                                    <ObjectAnimationUsingKeyFrames Storyboard.TargetName="Text" Storyboard.TargetProperty="Foreground">
                                        <DiscreteObjectKeyFrame KeyTime="0" Value="{StaticResource ApplicationPointerOverForegroundThemeBrush}"/>
                                    </ObjectAnimationUsingKeyFrames>
                                </Storyboard>
                            </VisualState>
                            <VisualState x:Name="Pressed">
                                <Storyboard>
                                    <ObjectAnimationUsingKeyFrames Storyboard.TargetName="Text" Storyboard.TargetProperty="Foreground">
                                        <DiscreteObjectKeyFrame KeyTime="0" Value="{StaticResource ApplicationPressedForegroundThemeBrush}"/>
                                    </ObjectAnimationUsingKeyFrames>
                                </Storyboard>
                            </VisualState>
...
                       </VisualStateGroup>
                    </VisualStateManager.VisualStateGroups>
                </Grid>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

Также возможно использование [**ObjectAnimationUsingKeyFrames**](https://msdn.microsoft.com/library/windows/apps/BR210320) для анимирования свойств, использующих значение перечисления. Вот другой пример из именованного стиля, построенного по шаблонам среды выполнения Windows по умолчанию. Обратите внимание на то, как задается свойство [**Visibility**](https://msdn.microsoft.com/library/windows/apps/BR208992), которое принимает константу перечисления [**Visibility**](https://msdn.microsoft.com/library/windows/apps/BR209006). В этом случае можно задать значение, используя синтаксис атрибута. Вам потребуется только неполное имя константы из перечисления для задания свойства со значением перечисления, например "Collapsed".

```xml
<Style x:Key="BackButtonStyle" TargetType="Button">
    <Setter Property="Template">
      <Setter.Value>
        <ControlTemplate TargetType="Button">
          <Grid x:Name="RootGrid">
            <VisualStateManager.VisualStateGroups>
              <VisualStateGroup x:Name="CommonStates">
              <VisualState x:Name="Normal"/>
...           <VisualState x:Name="Disabled">
                <Storyboard>
                  <ObjectAnimationUsingKeyFrames Storyboard.TargetName="RootGrid" Storyboard.TargetProperty="Visibility">
                    <DiscreteObjectKeyFrame Value="Collapsed" KeyTime="0"/>
                  </ObjectAnimationUsingKeyFrames>
                </Storyboard>
              </VisualState>
            </VisualStateGroup>
...
          </VisualStateManager.VisualStateGroups>
        </Grid>
      </ControlTemplate>
    </Setter.Value>
  </Setter>
</Style>
```

Можно использовать несколько [**DiscreteObjectKeyFrame**](https://msdn.microsoft.com/library/windows/apps/BR243132) для набора кадров [**ObjectAnimationUsingKeyFrames**](https://msdn.microsoft.com/library/windows/apps/BR210320). Анимирование значения [**Image.Source**](https://msdn.microsoft.com/library/windows/apps/BR242760) может быть интересным способом создания анимации слайд-шоу, поскольку пример сценария с несколькими значениями объекта может оказаться полезен.

 ## <a name="related-topics"></a>Связанные разделы

* [Синтаксис PropertyPath](https://msdn.microsoft.com/library/windows/apps/Mt185586)
* [Общие сведения о свойствах зависимостей](https://msdn.microsoft.com/library/windows/apps/Mt185583)
* [**Раскадровка**](https://msdn.microsoft.com/library/windows/apps/BR210490)
* [**Storyboard.TargetProperty**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.storyboard.targetpropertyproperty)