---
ms.assetid: 0CBCEEA0-2B0E-44A1-A09A-F7A939632F3A
title: Раскадрованные анимации
description: Раскадрованные анимации — это не просто анимации в визуальном смысле.
ms.date: 07/13/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 62f603a6ff5aadc1c3e5342db6a7d771f8c37a7b
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67320841"
---
# <a name="storyboarded-animations"></a>Раскадрованные анимации

Раскадрованные анимации — это не просто анимации в визуальном смысле. Раскадрованная анимация — это способ изменить значение свойства зависимостей как функцию времени. Раскадрованная анимация, не входящая в состав библиотеки анимации, может потребоваться вам, например, чтобы определить состояние для элемента управления как части шаблона элемента управления или определения страницы.

## <a name="differences-with-silverlight-and-wpf"></a>Отличия от Silverlight и WPF

Если вы знакомы с Microsoft Silverlight или WPF (Windows Presentation Foundation), прочтите этот раздел. Если нет, то можете пропустить его.

В целом создание раскадрованных анимаций для приложений среды выполнения Windows похоже на работу в Silverlight и WPF. Но есть несколько важных отличий.

-   Раскадрованные анимации — не единственный и не самый легкий для разработчиков способ визуально анимировать пользовательский интерфейс. Вместо применения раскадрованных анимаций часто рекомендуется использовать анимации темы и анимационные эффекты перехода. Таким образом можно быстро создать рекомендованные анимации пользовательского интерфейса, не вникая в детали свойств анимации. Дополнительные сведения см. в статье [Обзор анимаций](xaml-animation.md).
-   В среде выполнения Windows многие элементы управления XAML содержат анимации тем и анимационные эффекты перехода как часть встроенного поведения. В большинстве случаев элементы управления WPF и Silverlight не содержали поведение анимации по умолчанию.
-   Не все созданные вами пользовательские анимации могут по умолчанию запускаться в приложении среды выполнения Windows, если система анимации определяет, что анимации могут ухудшить производительность пользовательского интерфейса. Анимации, которые система определила как причину ухудшения производительности, называются *зависимыми анимациями*. Они называются зависимыми, поскольку синхронизация анимации напрямую работает против потока пользовательского интерфейса, в котором выполняется попытка применить изменения среды выполнения к пользовательскому интерфейсу в результате ввода данных активным пользователем и других обновлений. Зависимая анимация, потребляющая слишком много системных ресурсов в потоке пользовательского интерфейса, может привести к тому, что приложение перестанет отвечать в определенных ситуациях. Если анимация вызывает изменение макета или может по-другому влиять на производительность в потоке пользовательского интерфейса, вам часто придется явным образом включать ее, чтобы увидеть ее выполнение. Для этого используется свойство **EnableDependentAnimation** в определенных классах анимаций. Подробнее: [Зависимые и независимые анимации](./storyboarded-animations.md#dependent-and-independent-animations).
-   Пользовательские функции для реалистичной анимации в данный момент не поддерживаются в среде выполнения Windows.

## <a name="defining-storyboarded-animations"></a>Определение раскадрованных анимаций

Раскадрованная анимация — это способ изменить значение свойства зависимостей как функцию времени. Свойство, которое вы анимируете, не всегда является свойством, напрямую влияющим на пользовательский интерфейс вашего приложения. Но так как XAML предназначен для определения пользовательского интерфейса приложения, вы обычно анимируете свойство, связанное с пользовательским интерфейсом. Например, вы можете анимировать угол [**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform) или значение цвета для фона кнопки.

Одна из главных причин, по которой вы можете определять раскадрованную анимацию, состоит в том, что вы являетесь автором элемента управления или изменяете шаблон элемента управления и определяете визуальные состояния. Подробнее об этом см. в разделе [Раскадрованные анимации для визуальных состояний](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10)).

Если вы занимаетесь определением визуальных состояний или пользовательской анимации для приложения, описанные в этой теме понятия и API можно использовать в обоих случаях.

Для того чтобы свойство, которое вы выбираете для раскадрованной анимации, было анимировано, оно должно быть *свойством зависимостей*. Свойство зависимостей — основной компонент реализации XAML для среды выполнения Windows. Записываемые свойства наиболее распространенных элементов пользовательского интерфейса обычно реализуются как свойства зависимостей, поэтому вы можете анимировать их, применять значения, связанные с данными, или применять [**Style**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) и выбирать свойство с помощью [**Setter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Setter). Подробнее о работе свойств зависимостей: [Общие сведения о свойствах зависимостей](https://docs.microsoft.com/windows/uwp/xaml-platform/dependency-properties-overview).

Чаще всего раскадрованная анимация определяется с помощью записи XAML. Если вы используете такой инструмент, как Microsoft Visual Studio, он создаст XAML-код автоматически. Можно также определить раскадрованную анимацию при помощи кода, но такой способ менее распространен.

Рассмотрим простой пример. В этом примере XAML свойство [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) анимируется для отдельного объекта [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle).

```xaml
<Page ...>
  <Page.Resources>
    <!-- Storyboard resource: Animates a rectangle's opacity. -->
    <Storyboard x:Name="myStoryboard">
      <DoubleAnimation
        Storyboard.TargetName="MyAnimatedRectangle"
        Storyboard.TargetProperty="Opacity"
        From="1.0" To="0.0" Duration="0:0:1"/>
    </Storyboard>
  </Page.Resources>

  <!--Page root element, UI definition-->
  <Grid>
    <Rectangle x:Name="MyAnimatedRectangle"
      Width="300" Height="200" Fill="Blue"/>
  </Grid>
</Page>
```

### <a name="identifying-the-object-to-animate"></a>Идентификация объекта для анимации

В предыдущем примере раскадровка анимировала свойство [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) объекта [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle). Вы не объявляете анимации для самого объекта. Это делается в определении анимации для раскадровки. Раскадровки, как правило, определяются в XAML, который не находится в непосредственной близости от определения пользовательского интерфейса XAML для объекта, который нужно анимировать. Вместо этого они обычно задаются как ресурс XAML.

Чтобы соединить анимацию с целевым объектом, вы ссылаетесь на него, используя его идентифицирующее программное имя. Следует всегда применять атрибут [x:Name](https://docs.microsoft.com/windows/uwp/xaml-platform/x-name-attribute) в определении пользовательского интерфейса XAML, чтобы указать объект, который нужно анимировать. Затем вы выбираете объект, который нужно анимировать, задавая свойство [**Storyboard.TargetName**](https://docs.microsoft.com/dotnet/api/system.windows.media.animation.storyboard.targetname?view=netframework-4.8) в определении анимации. Для значения свойства **Storyboard.TargetName** вы используете строку имени целевого объекта, которую вы задали ранее в другом месте в атрибуте x:Name.

### <a name="targeting-the-dependency-property-to-animate"></a>Выбор свойства зависимостей для анимации

Вы задаете значение для [**Storyboard.TargetProperty**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/ms616983(v=vs.95)) в анимации. Таким образом определяется, какое именно свойство целевого объекта анимируется.

Иногда необходимо выбрать свойство, которое не имеет прямого отношения к целевому объекту, но находится в связи между объектом и свойством на более глубоком уровне. Часто это требуется для того, чтобы детализировать набор дополнительных значений объекта и свойства, пока вы не сможете сослаться на тип свойства, который можно анимировать ([**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN), [**Point**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point), [**Color**](https://docs.microsoft.com/uwp/api/Windows.UI.Color)). Это понятие называется *непрямой выбор*, а синтаксис для выбора свойства таким способом называется *путь к свойству*.

Рассмотрим пример. Распространенный сценарий для раскадрованной анимации — это изменение цвета области пользовательского интерфейса или элемента управления в приложении, чтобы показать, что элемент управления находится в конкретном состоянии. Например, вам нужно анимировать свойство [**Foreground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.foreground) для [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock), чтобы оно изменило свой цвет с красного на зеленый. Вы верно предполагаете, что этот процесс связан с [**ColorAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.ColorAnimation). Однако ни одно из свойств элементов пользовательского интерфейса, которые влияют на цвет объекта, на самом деле не относится к типу [**Color**](https://docs.microsoft.com/uwp/api/Windows.UI.Color). Вместо этого они относятся к типу [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush). Фактически вам нужно выбрать для анимации свойство [**Color**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush.Color) класса [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush), являющегося производным от **Brush** типом, который обычно используется для этих свойств пользовательского интерфейса, относящихся к цвету. Вот как это выглядит в контексте формирования пути к свойству для вашего выбора свойства анимации:

```xaml
<Storyboard x:Name="myStoryboard">
  <ColorAnimation
    Storyboard.TargetName="tb1"
    Storyboard.TargetProperty="(TextBlock.Foreground).(SolidColorBrush.Color)"
    From="Red" To="Green"/>
</Storyboard>
```

Ниже показан этот синтаксис для его частей:

- В каждом наборе круглых скобок () заключено имя свойства.
- В имени свойства есть точка, разделяющая имя типа и имя свойства, чтобы свойство, которое вы идентифицируете, было точно выражено.
- Точка посередине, которая не находится внутри круглых скобок, является шагом. Это интерпретируется с помощью синтаксиса и указывает на необходимость взять значение первого свойства (которое является объектом), войти в объектную модель и выбрать отдельное подсвойство значения первого свойства.

Вот список сценариев выбора анимации, где вы, вероятно, будете использовать непрямой выбор свойств и строки путей к свойствам, что примерно соответствует синтаксису, который вы будете применять:

- Анимирование значения [**X**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.translatetransform.x) класса [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) применительно к [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform): `(UIElement.RenderTransform).(TranslateTransform.X)`
- Анимирование [**Color**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush.Color) в [**GradientStop**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.GradientStop) класса [**LinearGradientBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush) применительно к [**Fill**](/uwp/api/Windows.UI.Xaml.Shapes.Shape.Fill): `(Shape.Fill).(GradientBrush.GradientStops)[0].(GradientStop.Color)`
- Анимирование значения [**X**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.translatetransform.x) класса [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform), который является одним из четырех преобразований в [**TransformGroup**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TransformGroup), применительно к [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform):`(UIElement.RenderTransform).(TransformGroup.Children)[3].(TranslateTransform.X)`

Обратите внимание, что в некоторых примерах числа заключены в квадратные скобки. Это индексатор. Индексатор означает, что имя свойства перед ним имеет коллекцию в качестве значения, и что вам нужен один элемент (на что указывает индекс с отчетом от нуля) из этой коллекции.

Вы также можете анимировать присоединенные свойства XAML. Всегда заключайте полное имя присоединенного свойства в круглые скобки, например `(Canvas.Left)`. Подробнее об этом: [Анимирование присоединенных свойств XAML](./storyboarded-animations.md#animating-xaml-attached-properties).

Дополнительную информацию об использовании пути к свойству для анимации см. в разделах [Синтаксис Property-path](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax) или [**Присоединенное свойство Storyboard.TargetProperty**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/ms616983(v=vs.95)).

### <a name="animation-types"></a>Типы анимации

Система анимации среды выполнения Windows имеет три отдельных типа, к которым может применяться раскадрованная анимация.

-   [**Двойные**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN), могут быть анимированы с любым [ **DoubleAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DoubleAnimation)
-   [**Точка**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point), могут быть анимированы с любым [ **PointAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.PointAnimation)
-   [**Цвет**](https://docs.microsoft.com/uwp/api/Windows.UI.Color), могут быть анимированы с любым [ **ColorAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.ColorAnimation)

Также существует универсальный тип анимации [**Object**](https://docs.microsoft.com/dotnet/api/system.object?redirectedfrom=MSDN) для значений ссылок на объекты, который мы рассмотрим позднее.

### <a name="specifying-the-animated-values"></a>Указание анимированных значений

Мы уже показали вам, как выбрать объект и свойство для анимации, но еще не описали, что анимация делает со значением свойства при выполнении.

Типы анимаций, которые мы описали, иногда называют анимациями **From**/**To**/**By**. Это значит, что анимация со временем изменяет значение свойства, используя некоторые из этих входных данных, которые поступают из определения анимации:

-   Значение начинается со значения **From**. Если вы не укажете значение **From**, начальным значением будет любое значение, содержащееся в анимированном свойстве перед выполнением анимации. Это может быть значение по умолчанию, значение из стиля или шаблона или значение, специально примененное определением пользовательского интерфейса XAML или кодом приложения.
-   В конце анимации значением будет значение **To**.
-   Или же задайте свойство **By**, чтобы указать конечное значение относительно начального значения. Это свойство задается вместо свойства **To**.
-   Если вы не укажете значение **To** или **By**, конечным значением будет любое значение, содержащееся в анимированном свойстве перед выполнением анимации. В этом случае рекомендуется иметь значение **From**, иначе анимация совсем не изменит значение, так как начальное и конечное значения одинаковые.
-   Обычно анимация содержит как минимум одно из значений **From**, **By** или **To**, но никогда не имеет всех трех.

Давайте вернемся к предыдущему примеру XAML и еще раз посмотрим на значения **From** и **To** и на свойство **Duration**. В этом примере анимируется свойство [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity), а типом свойства **Opacity** является [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN). Итак, здесь используется анимация [**DoubleAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DoubleAnimation).

`From="1.0" To="0.0"` указывает, что при выполнении анимации свойство [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity) начинается со значения 1 и анимируется до 0. Другими словами (в контексте того, что эти значения [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN) означают для свойства **Opacity**), в результате анимации непрозрачный объект постепенно становится прозрачным.

```xaml
...
<Storyboard x:Name="myStoryboard">
  <DoubleAnimation
    Storyboard.TargetName="MyAnimatedRectangle"
    Storyboard.TargetProperty="Opacity"
    From="1.0" To="0.0" Duration="0:0:1"/>
</Storyboard>
...
```

`Duration="0:0:1"` указывает, как долго длится анимация, т. е. за какое время прямоугольник исчезает. Свойство [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration) указывается в виде *часы*:*минуты*:*секунды*. Период времени в этом примере составляет одну секунду.

Подробнее о значениях [**Duration**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Duration) и синтаксисе XAML: [**Duration**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Duration).

> [!NOTE]
> Примечание. Если вы уверены, что начальное состояние анимируемого объекта содержит свойство [**Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity), всегда равное 1 (заданное по умолчанию или явно), то для нашего примера можно пропустить значение **From** — анимация будет использовать неявное начальное значение, а результат будет тот же.

### <a name="fromtoby-are-nullable"></a>From/To/By определять необязательно

Ранее мы говорили, что можно пропустить **From**, **To** или **By** и использовать текущие неанимированные значения в качестве замены отсутствующего значения. Угадать тип свойств **From**, **To** и **By** анимации непросто. Например, свойство [**DoubleAnimation.To**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.doubleanimation.easingfunction) не относится к типу [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN). Зато [**Nullable**](https://docs.microsoft.com/dotnet/api/system.nullable-1?redirectedfrom=MSDN) относится к **Double**. А его значение по умолчанию равно **null**, а не 0. С помощью этого значения **null** система анимации определяет, что вы не задали значение для свойства **From**, **To** или **By**. Visual C++ расширения компонентов (C++/CX) не имеет **Nullable** тип, поэтому она использует [ **IReference** ](https://docs.microsoft.com/uwp/api/Windows.Foundation.IReference_T_) вместо этого.

### <a name="other-properties-of-an-animation"></a>Другие свойства анимации

Все следующие свойства, описанные в этом разделе, являются необязательными, так как они содержат значения по умолчанию, подходящие для большинства анимаций.

### <a name="autoreverse"></a>**AutoReverse**

Если вы не зададите для анимации свойство [**AutoReverse**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.autoreverse) или [**RepeatBehavior**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.repeatbehavior), эта анимация будет выполнена один раз в течение времени, указанного в [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration).

Свойство [**AutoReverse**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.autoreverse) указывает, будет ли временная шкала воспроизводиться в обратном направлении после того, как дойдет до конца [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration). Если вы установите для него значение **true**, анимация будет выполнена в обратном порядке после того, как дойдет до конца объявленного [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration), изменяя значение от конечного (**To**) к начальному (**From**). Это значит, что анимация эффективно выполняется в два раза дольше времени, заданного в [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration).

### <a name="repeatbehavior"></a>**RepeatBehavior**

Свойство [**RepeatBehavior**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.repeatbehavior) указывает число воспроизведений временной шкалы или больший период, в течение которого должна повторяться временная шкала. По умолчанию временная шкала имеет число итераций, равное 1x, что означает, что она воспроизводится один раз в течение времени, указанного в [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration), и не повторяется.

Можно заставить анимацию выполняться несколько итераций. Например, при значении "3x" анимация будет выполнена три раза. Или же вы можете указать другое значение [**Duration**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Duration) для [**RepeatBehavior**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.repeatbehavior). Для эффективной работы это значение **Duration** должно быть больше, чем значение **Duration** самой анимации. Например, если вы укажете в **RepeatBehavior** значение "0:0:10" для анимации, имеющей свойство [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration) со значением "0:0:2", то эта анимация повторится пять раз. Если эти значения не делятся без остатка, анимация прекратится в тот момент, когда будет достигнуто время **RepeatBehavior**, что может случиться после выполнения части процесса. Кроме того, вы можете указать специальное значение Forever, при котором анимация будет выполняться бесконечно, пока не будет остановлена намеренно.

Подробнее о значениях [**RepeatBehavior**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.RepeatBehavior) и синтаксисе XAML: [**RepeatBehavior**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.RepeatBehavior).

### <a name="fillbehaviorstop"></a>**FillBehavior="Stop"**

По умолчанию, когда анимация заканчивается, она оставляет значение свойства как окончательное значение **To** или измененное значение **By**, даже после того как ее длительность превышена. Однако если вы задаете значение свойства [**FillBehavior**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.fillbehavior) как [**FillBehavior.Stop**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.FillBehavior), значение анимированного значения возвращается в любое значение, которое присутствовало до применения анимации, или, точнее, в текущее действительное значение, что определено системой свойств зависимостей (подробнее об этом отличии: [Общие сведения о свойствах зависимостей](https://docs.microsoft.com/windows/uwp/xaml-platform/dependency-properties-overview)).

### <a name="begintime"></a>**BeginTime**

По умолчанию для свойства [**BeginTime**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.begintime) анимации установлено значение «0:0:0», поэтому она начинается, как только запускается содержащийся в ней класс [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard). Это можно изменить, если **Storyboard** содержит несколько анимаций и вы хотите назначить время запуска других анимаций относительно начальной анимации или специально создать кратковременную задержку.

### <a name="speedratio"></a>**SpeedRatio**

Если в [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) содержится несколько анимаций, вы можете изменить скорость одной или нескольких анимаций относительно **Storyboard**. Наконец, родительский класс **Storyboard** контролирует, как истекает время [**Duration**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Duration) во время выполнения анимации. Это свойство используется не очень часто. Подробнее об этом: [**SpeedRatio**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.speedratio).

## <a name="defining-more-than-one-animation-in-a-storyboard"></a>Определение нескольких анимаций в **Storyboard**

[  **Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) может содержать несколько определений анимаций. У вас может быть несколько анимаций, если вы применяете их к двум свойствам одного целевого объекта. Вы можете изменить свойства [**TranslateX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.compositetransform.translatex) и [**TranslateY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.compositetransform.translatey) класса [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform), используемого как [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform) элемента пользовательского интерфейса; в результате этот элемент будет перемещен по диагонали. Для этого требуется две разные анимации, но они должны находиться в одном и том же **Storyboard**, так как вам всегда нужно, чтобы эти две анимации выполнялись вместе.

Анимации не обязательно должны быть одного типа или предназначаться для одного и того же объекта. Они могут иметь разную длительность, и им не нужно совместно использовать значения свойств.

Когда выполняется родительский [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard), все анимации, содержащиеся в нем, также будут выполняться.

Класс [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) фактически имеет множество тех же свойств анимации, что и типы анимации, так как они оба совместно используют базовый класс [**Timeline**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Timeline). Таким образом, **Storyboard** может иметь [**RepeatBehavior**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.repeatbehavior) или [**BeginTime**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.begintime). Однако обычно вы не задаете их для **Storyboard**, если только не хотите, чтобы все содержащиеся анимации имели такое поведение. Как правило, любое свойство **Timeline**, заданное для **Storyboard**, применяется ко всем его дочерним анимациям. Если свойства не заданы, **Storyboard** имеет неявную длительность, которая вычисляется из самого большого значения [**Duration**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Duration) содержащихся анимаций. Явно заданное значение [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration) для **Storyboard**, которое меньше, чем значение одной из его дочерних анимаций, приведет к тому, что эта анимация будет прервана, что не всегда желательно.

Раскадровка не может содержать двух анимаций, пытающихся выбрать и анимировать одно и то же свойство одного и того же объекта. Если вы попробуете сделать это, то получите ошибку выполнения при запуске раскадровки. Это ограничение применяется, даже если анимации не перекрываются во времени из-за намеренно разных значений [**BeginTime**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.begintime) и длительностей. Если вы действительно хотите применить более сложную временную шкалу анимации к одному и тому же свойству в отдельной раскадровке, необходимо использовать анимацию по ключевым кадрам. См. раздел [Анимации по ключевым кадрам и на основе функций для реалистичной анимации](key-frame-and-easing-function-animations.md).

Система анимации может применять несколько анимаций к значению свойства, если эти входные данные поступают из нескольких раскадровок. Способ использования этого поведения специально для одновременного выполнения раскадровок не распространен. Однако возможно, что определенная приложением анимация, которую вы применяете к свойству элемента управления, будет изменять значение **HoldEnd** анимации, которая ранее выполнялась как часть модели визуального состояния элемента управления.

## <a name="defining-a-storyboard-as-a-resource"></a>Определение раскадровки как ресурса

[  **Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) – это контейнер, в который вы помещаете объекты анимации. Вы обычно определяете **Storyboard** как ресурс, доступный объекту, который вы хотите анимировать, в [**Resources**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.resources) или [**Application.Resources**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.resources) уровня страницы.

В этом примере показано, как [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) из предыдущего примера содержался бы в определении [**Resources**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.resources) уровня страницы, где **Storyboard** — это различаемый по ключу ресурс корневого [**Page**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page). Обратите внимание на атрибут [x:Name](https://docs.microsoft.com/windows/uwp/xaml-platform/x-name-attribute). С помощью этого атрибута вы определяете имя переменной для **Storyboard**, чтобы другие элементы в XAML, а также в коде, могли ссылаться на **Storyboard** позднее.

```xaml
<Page ...>
  <Page.Resources>
    <!-- Storyboard resource: Animates a rectangle's opacity. -->
    <Storyboard x:Name="myStoryboard">
      <DoubleAnimation
        Storyboard.TargetName="MyAnimatedRectangle"
        Storyboard.TargetProperty="Opacity"
        From="1.0" To="0.0" Duration="0:0:1"/>
    </Storyboard>
  </Page.Resources>
  <!--Page root element, UI definition-->
  <Grid>
    <Rectangle x:Name="MyAnimatedRectangle"
      Width="300" Height="200" Fill="Blue"/>
  </Grid>
</Page>
```

Для упорядочения ресурсов с ключом в XAML обычно определяются ресурсы в корневом элементе файла XAML, например page.xaml или app.xaml. Вы также можете разложить ресурсы на отдельные файлы, а затем выполнить их слияние в приложениях или на страницах. Подробнее: [ResourceDictionary и ссылки на ресурсы XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/resourcedictionary-and-xaml-resource-references).

> [!NOTE]
> XAML среды выполнения Windows поддерживает идентификацию ресурсов при помощи атрибута [x:Key](https://docs.microsoft.com/windows/uwp/xaml-platform/x-key-attribute) или [x:Name](https://docs.microsoft.com/windows/uwp/xaml-platform/x-name-attribute). Атрибут x:Name чаще используется для класса [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard), так как в конечном счете вам нужно будет ссылаться на него при помощи имени переменной, чтобы вы могли вызвать его метод [**Begin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.begin) и выполнить анимацию. Если вы все-таки будете применять атрибут [x:Key](https://docs.microsoft.com/windows/uwp/xaml-platform/x-key-attribute), вам потребуется использовать методы [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary), например индексатор [**Item**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.item), чтобы получить его как различаемый по ключу ресурс, а затем привести полученный объект к **Storyboard**, чтобы использовать методы **Storyboard**.

### <a name="storyboards-for-visual-states"></a>Раскадровки для визуальных состояний

Вы также помещаете анимации в [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) при объявлении анимаций визуального состояния для визуального отображения элемента управления. В этом случае определяемые вами элементы **Storyboard** помещаются в контейнер [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState), расположенный на более глубоком уровне в [**Style**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Style) (ресурсом с ключом является **Style**). В данном случае вам не требуется ключ или имя для **Storyboard**, поскольку **VisualState** имеет конечное имя, которое может вызвать класс [**VisualStateManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.visualstatemanager). Стили элементов управления часто раскладываются на отдельные файлы [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary) XAML, а не помещаются в коллекцию **Resources** приложения или страницы. Подробнее об этом см. в разделе [Раскадрованные анимации для визуальных состояний](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10)).

## <a name="dependent-and-independent-animations"></a>Зависимые и независимые анимации

На данном этапе нам нужно познакомиться с несколькими важными понятиями о работе системы анимации. В частности, анимация по существу взаимодействует с тем, как приложение среды выполнения Windows преобразовывает объекты для просмотра на экране и как эта отрисовка использует потоки обработки. Приложение среды выполнения Windows всегда имеет основной поток пользовательского интерфейса, и этот поток отвечает за обновление текущей информации на экране. Кроме того, приложение среды выполнения Windows имеет поток композиции, который используется для предварительного вычисления макетов непосредственно перед их отображением. Когда вы анимируете пользовательский интерфейс, вы можете создать большое количество работы для потока пользовательского интерфейса. Система должна перерисовать большие области экрана, используя довольно короткие интервалы времени между обновлениями. Это необходимо для захвата самого последнего значения анимированного свойства. Если вы не будете внимательны, существует опасность того, что анимация замедлит скорость ответа пользовательского интерфейса или повлияет на производительность других компонентов приложения, которые находятся в том же потоке пользовательского интерфейса.

*Зависимая анимация* — это анимация, которая может замедлить работу потока пользовательского интерфейса. Анимация, не представляющая такой опасности, называется *независимой анимацией*. Различие между зависимыми и независимыми анимациями определяется не просто типами анимаций ([**DoubleAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DoubleAnimation) и т. д.), как было описано выше. Оно определяется тем, какие отдельные свойства вы анимируете, а также другими факторами, например наследованием и композицией элементов управления. Существуют условия, когда анимация, даже если она изменяет пользовательский интерфейс, оказывает минимальное влияние на поток пользовательского интерфейса и может быть обработана потоком композиции как независимая анимация.

Анимация называется независимой, если имеет любую из следующих характеристик.

-   Значение свойства [**Duration**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.duration) анимации равно 0 секунд (см. предупреждение).
-   Объектом анимации выбрано свойство [**UIElement.Opacity**](/uwp/api/Windows.UI.Xaml.UIElement.Opacity).
-   Значение вложенного свойства из этих целевым объектом для анимации [ **UIElement** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) свойства: [**Объект Transform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.transform3d), [ **RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform), [ **проекции**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.projection), [ **Клипов**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.clip)
-   Объектом анимации выбрано свойство [**Canvas.Left**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.left?view=netframework-4.8) или [**Canvas.Top**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.top?view=netframework-4.8)
-   Анимация предназначена для значения [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) и использует [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush), анимируя его [**Color**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush.Color).
-   Анимация представляет собой [**ObjectAnimationUsingKeyFrames**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.ObjectAnimationUsingKeyFrames).

> [!WARNING]
> Чтобы ваша анимация обрабатывалась как независимая, необходимо явно задать `Duration="0"`. Например, если вы удалите `Duration="0"` из этого XAML, анимация будет обрабатываться как зависимая, даже если [**KeyTime**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.doublekeyframe.keytime) кадра равно "0:0:0".

```xaml
<Storyboard>
  <DoubleAnimationUsingKeyFrames
    Duration="0"
    Storyboard.TargetName="Button2"
    Storyboard.TargetProperty="Width">
    <DiscreteDoubleKeyFrame KeyTime="0:0:0" Value="200"/>
  </DoubleAnimationUsingKeyFrames>
</Storyboard>
```

Анимация, которая не соответствует этим критериям, вероятно, является зависимой. По умолчанию система анимации не выполняет зависимую анимацию. Поэтому во время разработки и тестирования приложения вы можете даже не увидеть выполнение анимации. Вы все равно можете использовать ее, но необходимо специально включить каждую такую зависимую анимацию. Чтобы включить анимацию, установите для свойства **EnableDependentAnimation** объекта анимации значение **true**. (Каждый подкласс [**Timeline**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Timeline), представляющий анимацию, имеет свою реализацию этого свойства, но все они называются `EnableDependentAnimation`.)

Требование к разработчикам включать зависимые анимации — это часть осознанного подхода к проектированию системы анимации и процесса разработки. Мы хотим, чтобы разработчики понимали, что анимации действительно оказывают отрицательное влияние на производительность и быстродействие пользовательского интерфейса. Анимации с низкой производительностью трудно изолировать и отладить в полномасштабном приложении. Поэтому лучше включать только те зависимые анимации, которые действительно необходимы для работы с пользовательским интерфейсом приложения. Мы не хотим, чтобы производительность приложения страдала из-за декоративных анимаций, использующих большое количество ресурсов процессора. Дополнительные советы по производительности для анимации см. в разделе [об оптимизации анимаций и ресурсов мультимедиа](https://docs.microsoft.com/windows/uwp/debug-test-perf/optimize-animations-and-media).

Кроме того, как разработчик вы можете применить параметр приложения, который всегда отключает зависимые анимации, даже те, где **EnableDependentAnimation** имеет значение **true**. См.: [**Timeline.AllowDependentAnimations**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.allowdependentanimations).

> [!TIP]
> При использовании области анимации в Blend для Visual Studio 2017, каждый раз, когда вы попытаетесь применить зависимые анимации к свойству визуальное состояние, предупреждения будут отображаться в конструкторе. Предупреждения не будут отображаться в выходных данных сборки или списка ошибок. Если требуется вручную изменить XAML, конструктор не выведет предупреждение. Во время выполнения, при отладке, выходные данные отладки на панели вывода будут отображаться предупреждение, анимации не является независимым и будут пропущены.


## <a name="starting-and-controlling-an-animation"></a>Анимация: запуск и управление

Все, что мы показали вам до сих пор, на самом деле не позволит выполнить или применить анимацию! Пока анимация не запущена и не выполняется, изменения значений, объявляемые анимацией в XAML, находятся в скрытом состоянии и не происходят. Вы должны явно запустить анимацию каким-либо способом, связанным со временем существования приложения или со взаимодействием с пользователем. Самый простой способ – запустить анимацию при помощи вызова метода [**Begin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.begin) для класса [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard), который является родительским для этой анимации. Нельзя вызывать методы непосредственно из XAML, поэтому любое действие для включения анимации вы будете выполнять из кода. Это будет либо файл с выделенным кодом для страниц или компонентов вашего приложения, либо возможно логика вашего элемента управления, если вы определяете класс пользовательского элемента управления.

Как правило, вы вызовете [**Begin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.begin) и просто позволите анимации выполняться до ее завершения. Кроме того, вы можете использовать методы [**Pause**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.pause), [**Resume**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.resume) и [**Stop**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.stop), чтобы управлять [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) во время выполнения, а также другие API, применяемые для более сложных сценариев управления анимацией.

При вызове [**Begin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.begin) для раскадровки, содержащей анимацию, которая повторяется бесконечно (`RepeatBehavior="Forever"`), эта анимация будет выполняться до тех пор, пока содержащая ее страница не будет выгружена или пока вы специально не вызовете метод [**Pause**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.pause) или [**Stop**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.stop).

### <a name="starting-an-animation-from-app-code"></a>Запуск анимации из кода приложения

Вы можете запускать анимации автоматически или в ответ на действия пользователей. Для автоматического запуска вы, как правило, используете событие времени жизни объекта, например [**Loaded**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.loaded), которое будет выступать в качестве переключателя анимации. Событие **Loaded** хорошо подходит для этого случая, потому что на данном этапе пользовательский интерфейс готов к взаимодействию и анимация не будет прервана в начале из-за того, что другая часть интерфейса еще загружается.

В следующем примере к прямоугольнику присоединяется событие [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed). Таким образом, анимация начинается, когда пользователь щелкает прямоугольник.

```xaml
<Rectangle PointerPressed="Rectangle_Tapped"
  x:Name="MyAnimatedRectangle"
  Width="300" Height="200" Fill="Blue"/>
```

Обработчик событий запускает [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard) (анимацию), используя метод [**Begin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.storyboard.begin) класса **Storyboard**.

```csharp
myStoryboard.Begin();
```

```cppwinrt
myStoryboard().Begin();
```

```cpp
myStoryboard->Begin();
```

```vb
myStoryBoard.Begin()
```

Вы можете обработать событие [**Completed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.completed), чтобы запустить другую логику после завершения применения значений в анимации. Кроме того, метод [**GetAnimationBaseValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.getanimationbasevalue) может быть полезен в диагностике взаимодействия системы свойств и анимации.

> [!TIP]
> Всякий раз, когда вы пишете код для сценария приложения, в котором анимация запускается из кода приложения, рекомендуется дополнительно проверить, нет ли в библиотеке анимаций для вашего сценария пользовательского интерфейса готовой анимации или перехода. Анимации из библиотеки проще в использовании и обеспечивают более согласованное взаимодействие с пользовательским интерфейсом во всех приложениях среды выполнения Windows.

 

### <a name="animations-for-visual-states"></a>Анимации для визуальных состояний

Поведение при выполнении для класса [**Storyboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard), который используется для определения визуального состояния элемента управления, отличается от того, как приложение может выполнить раскадровку напрямую. Применительно к определению визуального состояния в XAML, **Storyboard** – это элемент содержащего класса [**VisualState**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.VisualState), а состояние в целом управляется при помощи API [**VisualStateManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.visualstatemanager). Все анимации внутри будут выполнены в соответствии с их значениями анимации и свойствами [**Timeline**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Timeline), когда содержащий класс **VisualState** будет использован элементом управления. Подробнее об этом см. в разделе [Раскадровки для визуальных состояний](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10)). Видимое свойство [**FillBehavior**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.timeline.fillbehavior) для визуальных состояний другое. При переходе в другое визуальное состояние все изменения свойств, примененные предыдущим визуальным состоянием, и его анимации отменяются, даже если новое визуальное состояние прямо не применяет новую анимацию к свойству.

### <a name="storyboard-and-eventtrigger"></a>**Storyboard** и **EventTrigger**

Существует способ запустить анимацию, которая может быть полностью объявлена в XAML. Однако он больше не применяется широко. Это традиционный синтаксис из WPF и ранних версий Silverlight до поддержки [**VisualStateManager**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.visualstatemanager). Этот синтаксис [**EventTrigger**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.EventTrigger) до сих пор работает в XAML среды выполнения Windows для обеспечения импорта или совместимости, но только для поведения триггера, основанного на событии [**FrameworkElement.Loaded**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.loaded); попытка активировать другие события приведет к вызову исключений или к ошибке при компиляции. Подробнее об этом см. в разделе [**EventTrigger**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.EventTrigger) или [**BeginStoryboard**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.BeginStoryboard).

## <a name="animating-xaml-attached-properties"></a>Анимирование присоединенных свойств XAML

Этот сценарий не распространен, но вы можете применить анимированное значение к присоединенному свойству XAML. Дополнительную информацию о присоединенных свойствах и об их работе см. в разделе [Общие сведения о присоединенных свойствах](https://docs.microsoft.com/windows/uwp/xaml-platform/attached-properties-overview). Для выбора присоединенного свойства необходим [синтаксис property-path](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax), который содержит имя свойства в круглых скобках. Вы можете анимировать встроенные присоединенные свойства, например [**Canvas.ZIndex**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc190397(v=vs.95)), с помощью [**ObjectAnimationUsingKeyFrames**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.ObjectAnimationUsingKeyFrames), который применяет дискретные целочисленные значения. Однако существующее ограничение реализации XAML среды выполнения Windows состоит в том, что вы не можете анимировать пользовательское присоединенное свойство.

## <a name="more-animation-types-and-next-steps-for-learning-about-animating-your-ui"></a>Дополнительные типы анимации и следующие этапы для изучения анимации пользовательского интерфейса

До настоящего момента мы показали пользовательские анимации, которые выполняются для двух значений, а затем линейно интерполируют значения по мере необходимости во время выполнения анимации. Они называются анимациями **From**/**To**/**By**. Но есть еще один тип анимации, позволяющий вам объявить промежуточные значения, которые находятся между началом и концом. Это называется *анимацией по ключевым кадрам*. Также существует способ изменить логику интерполяции либо для анимации **From**/**To**/**By**, либо для анимации по ключевым кадрам. Этот способ связан с применением функции для реалистичной анимации. Узнать больше об этих понятиях можно в разделе [Анимации по ключевым кадрам и на основе функций для реалистичной анимации](key-frame-and-easing-function-animations.md).

## <a name="related-topics"></a>См. также

* [Синтаксис пути к свойству](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax)
* [Общие сведения о свойствах зависимостей](https://docs.microsoft.com/windows/uwp/xaml-platform/dependency-properties-overview)
* [Ключевым кадрам и анимация функция для реалистичной анимации](key-frame-and-easing-function-animations.md)
* [Выполнена его раскадровка анимации для визуальных состояний](https://docs.microsoft.com/previous-versions/windows/apps/jj819808(v=win.10))
* [Шаблоны элементов управления](https://docs.microsoft.com/windows/uwp/controls-and-patterns/control-templates)
* [**раскадровки**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.Storyboard)
* [**Storyboard.TargetProperty**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/ms616983(v=vs.95))
 

 




