---
ms.assetid: F46D5E18-10A3-4F7B-AD67-76437C77E4BC
title: Обзор преобразований
description: Узнайте, как использовать преобразования в API\# среды выполнения Windows путем изменения относительных систем координат элементов в пользовательском интерфейсе.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 28dc4a62bf580da41d424c98c186413dc96a8aae
ms.sourcegitcommit: 3a7f9f05f0127bc8e38139b219e30a8df584cad3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83775831"
---
# <a name="transforms-overview"></a>Обзор преобразований

Узнайте, как использовать преобразования в API среды выполнения Windows путем изменения относительных систем координат элементов в пользовательском интерфейсе. Эти действия могут применяться для корректировки внешнего вида отдельных XAML-элементов (например, при изменении масштаба, повороте или преобразовании положения в пространстве x-y).

## <a name="span-idwhat_is_a_transform_spanspan-idwhat_is_a_transform_spanspan-idwhat_is_a_transform_spanwhat-is-a-transform"></a><span id="What_is_a_transform_"></span><span id="what_is_a_transform_"></span><span id="WHAT_IS_A_TRANSFORM_"></span>Что такое преобразование?

*Преобразование* определяет порядок сопоставления (или преобразования) точек при переходе из одного пространства координат в другое. Когда преобразование применяется к элементу пользовательского интерфейса, это приводит к изменению способа отрисовки элемента на экране как части пользовательского интерфейса.

Преобразования можно поделить на четыре широкие категории: перевод, вращение, масштабирование и наклон (или сдвиг). При использовании API графики для изменения внешнего вида элементов пользовательского интерфейса обычно проще всего создавать преобразования, которые за один раз определяют только одну операцию трансформации. Поэтому в среде выполнения Windows для каждой из этих категорий преобразования определен отдельный класс.

-   [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform): переносит элемент в пространстве x-y путем задания значений [**X**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.translatetransform.x) и [**Y**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.translatetransform.y).
-   [**ScaleTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ScaleTransform): масштабирует преобразование, считая от центральной точки, путем задания значений [**CenterX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.centerx), [**CenterY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.centery), [**ScaleX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.scalex) и [**ScaleY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.scaleyproperty).
-   [**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform): выполняет поворот в плоскости XY путем задания значений [**Angle**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rotatetransform.angle), [**CenterX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rotatetransform.centerx) и [**CenterY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rotatetransform.centery).
-   [**SkewTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SkewTransform): выполняет сдвиг или наклон в плоскости XY путем задания значений [**AngleX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.skewtransform.anglex), [**AngleY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.skewtransform.angley), [**CenterX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.skewtransform.centerx) и [**CenterY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.centeryproperty).

Скорее всего, для сценариев пользовательского интерфейса вы чаще всего будете использовать [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) и [**ScaleTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ScaleTransform).

Преобразования можно комбинировать с помощью двух классов среды выполнения Windows: [**CompositeTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.CompositeTransform) и [**TransformGroup**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TransformGroup). В **CompositeTransform** преобразования применяются в следующем порядке: масштабирование, наклон, поворот и перенос. Если вы хотите применять преобразования в другом порядке, используйте **TransformGroup** вместо **CompositeTransform**. Подробнее: [**CompositeTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.CompositeTransform).

## <a name="span-idtransforms_and_layoutspanspan-idtransforms_and_layoutspanspan-idtransforms_and_layoutspantransforms-and-layout"></a><span id="Transforms_and_layout"></span><span id="transforms_and_layout"></span><span id="TRANSFORMS_AND_LAYOUT"></span>Преобразования и макет

В макете XAML преобразования применяются после завершения этапа разметки. Поэтому вычисление доступного пространства и принятие других решений относительно макета выполнялось еще до применения преобразований. Так как сначала создается макет, иногда вы будете получать неожиданные результаты при преобразовании элементов, находящихся в ячейке [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) или похожем контейнере макета, которому в процессе разметки выделяется пространство. Преобразованный элемент может оказаться усеченным или загороженным, поскольку его пытаются переместить в область, для которой при делении пространства родительского контейнера не учитывались размеры после преобразования. Возможно, вам стоит поэкспериментировать с результатами преобразования и скорректировать некоторые параметры. Например, вместо использования адаптивного макета и изменения размера с помощью Star вам, возможно, придется изменить свойства **Center** или объявить фиксированные размеры в пикселях для пространств макета, чтобы гарантировать, что родительский элемент выделяет достаточно места.

**Примечание по переносу**  Windows Presentation Foundation (WPF) содержит свойство **LayoutTransform**, применяющее преобразования перед этапом разметки. Однако XAML среды выполнения Windows не поддерживает свойство **LayoutTransform**. (В Microsoft Silverlight нет этого свойства.)

В качестве альтернативы в наборе средств сообщества Windows предоставляется инструмент [LayoutTransformControl](/windows/communitytoolkit/controls/LayoutTransformControl), который применяет преобразования матрицы к любому элементу FrameworkElement приложения.

## <a name="span-idapplying_a_transform_to_a_ui_elementspanspan-idapplying_a_transform_to_a_ui_elementspanspan-idapplying_a_transform_to_a_ui_elementspanapplying-a-transform-to-a-ui-element"></a><span id="Applying_a_transform_to_a_UI_element"></span><span id="applying_a_transform_to_a_ui_element"></span><span id="APPLYING_A_TRANSFORM_TO_A_UI_ELEMENT"></span>Применение преобразования к элементу пользовательского интерфейса

Применение преобразования к объекту обычно выполняется для назначения свойства [**UIElement.RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform). Назначение этого свойства не приводит к буквальному попиксельному изменению объекта. На самом деле назначение свойства состоит в применении преобразования в локальном пространстве координат, в котором существует объект. Затем с помощью логики и операции отрисовки (после создания макета) отрисовываются комбинированные пространства координат, создавая впечатление, что изменился внешний вид объекта, а также, возможно, его позиция в макете (если применялся класс [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform)).

По умолчанию центр каждого преобразования отрисовки совпадает с началом локальной системы координат целевого объекта (0,0). Единственным исключением является [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform), в котором нет задаваемых свойств выравнивания по центру, так как эффект переноса остается неизменным независимо от местонахождения центра. Но у каждого из остальных преобразований есть свойства, задающие значения **CenterX** и **CenterY**.

Каждый раз, когда вы используете преобразования по отношению к [**UIElement.RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform), помните, что в [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) есть другое свойство, влияющее на поведение преобразования, — [**RenderTransformOrigin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransformorigin). **RenderTransformOrigin** объявляет, нужно ли применять полное преобразование к точке по умолчанию (0,0) элемента или же к другой точке начала координат в относительном координатном пространстве данного элемента. В случае типичных элементов (0,0) означает, что преобразование размещается в верхнем левом углу. В зависимости от желаемого эффекта вы можете изменять **RenderTransformOrigin** вместо корректировки значений **CenterX** и **CenterY** при преобразованиях. Учтите, что если применяется и значение **RenderTransformOrigin**, и значение **CenterX** / **CenterY**, результаты могут оказаться неожиданными, особенно если вы анимируете какие-либо из этих значений.

Для проверки столкновения фигур неправильной формы объект, к которому применяется преобразование, продолжает отвечать на входные данные ожидаемым образом, который согласуется с внешним видом объекта в пространстве x-y. Например, если вы использовали [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) для перемещения [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) на 400 пикселей в сторону в пользовательском интерфейсе, **Rectangle** может реагировать на события [**PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed), когда пользователь нажимает точку, где визуально появляется **Rectangle**. Вы не получите ложных событий, если пользователь нажмет область, в которой **Rectangle** находился перед переносом. Применение преобразования не влияет на аспекты индекса z, влияющие на тестирование нажатия. Индекс z, определяющий, какой именно элемент обрабатывает события ввода для точки пространства x-y, по-прежнему оценивается с помощью дочернего порядка, объявленного в контейнере. Обычно применяется тот же порядок, что и при объявлении элементов в XAML, хотя для дочерних элементов объекта [**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) его можно скорректировать путем применения присоединенного свойства [**Canvas.ZIndex**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc190397(v=vs.95)) к дочерним элементам.

## <a name="span-idother_transform_propertiesspanspan-idother_transform_propertiesspanspan-idother_transform_propertiesspanother-transform-properties"></a><span id="Other_transform_properties"></span><span id="other_transform_properties"></span><span id="OTHER_TRANSFORM_PROPERTIES"></span>Другие свойства преобразования

-   [**Brush.Transform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.brush.transform), [**Brush.RelativeTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.brush.relativetransform). Эти свойства влияют на то, как [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) использует пространство координат в области, к которой применили **Brush** для задания таких визуальных свойств, как передние планы и фоны. Эти преобразования обычно не применяются для большинства распространенных кистей (для которых обычно задается сплошной цвет с помощью свойства [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush)), но иногда могут оказаться полезными при рисовании областей при помощи [**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) или [**LinearGradientBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush).
-   [**Geometry.Transform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.geometry.transform). Это свойство можно использовать для применения преобразования к геометрии перед использованием этой геометрии для значения свойства [**Path.Data**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.path.data).

## <a name="span-idanimating_a_transformspanspan-idanimating_a_transformspanspan-idanimating_a_transformspananimating-a-transform"></a><span id="Animating_a_transform"></span><span id="animating_a_transform"></span><span id="ANIMATING_A_TRANSFORM"></span>Анимация преобразования

Объекты [**Transform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform) можно анимировать. Чтобы анимировать **Transform**, примените анимацию совместимого типа к свойству, которое требуется анимировать. Как правило, это значит, что вы используете объекты [**DoubleAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DoubleAnimation) или [**DoubleAnimationUsingKeyFrames**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.doubleanimationusingkeyframes) для определения анимации, так как все свойства преобразования относятся к типу [**Double**](https://docs.microsoft.com/dotnet/api/system.double). Анимации, которые влияют на преобразование, используемое для значения [**UIElement.RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform), не считаются зависимыми даже тогда, когда их продолжительность не является нулевой. Подробнее о зависимых анимациях: [Раскадрованные анимации](/windows/uwp/design/motion/storyboarded-animations).

Если вы настраиваете анимацию свойств для создания эффекта, аналогичного преобразованию по визуальному представлению, например анимируете свойства [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) и [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) элемента [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement), а не применяете к нему [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform), такие анимации почти всегда считаются зависимыми. Вам придется включить анимации и, возможно, столкнуться с серьезным снижением производительности из-за этой анимации, особенно если вы пытаетесь поддерживать взаимодействие с пользователем во время анимации данного объекта. По этой причине предпочтительней использовать преобразование и анимировать его, а не любое другое свойство, где анимация воспринималась бы в качестве зависимой.

Чтобы выбрать цель преобразования, необходимо наличие класса [**Transform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform), выступающего в качестве значения [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform). Обычно вы помещаете элемент для соответствующего типа преобразования в исходный код XAML, не задавая свойства для этого преобразования.

Как правило, для применения анимаций к свойствам преобразования используется метод косвенного выбора. Подробнее о синтаксисе косвенного выбора можно узнать в разделах [Раскадрованные анимации](/windows/uwp/design/motion/storyboarded-animations) и [Синтаксис пути к свойству](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax).

Иногда стандартные стили элементов управления определяют анимации преобразований как часть своего поведения визуального состояния. Например, визуальные состояния для [**ProgressRing**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) используют анимированные значения [**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform) для "кружения" точек в кольце.

Вот простой пример анимации преобразования. В данном случае анимируется свойство [**Angle**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rotatetransform.angle) класса [**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform) для вращения [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) на месте вокруг его визуального центра. В этом примере **RotateTransform** присваивается имя, и поэтому косвенный выбор анимации не требуется. Но вместо этого преобразование можно оставить безымянным, присвоить имя элементу, к которому применяется преобразование, и использовать косвенный выбор (например, `(UIElement.RenderTransform).(RotateTransform.Angle)`.

```xml
<StackPanel Margin="15">
  <StackPanel.Resources>
    <Storyboard x:Name="myStoryboard">
      <DoubleAnimation
       Storyboard.TargetName="myTransform"
       Storyboard.TargetProperty="Angle"
       From="0" To="360" Duration="0:0:5" 
       RepeatBehavior="Forever" />
    </Storyboard>
  </StackPanel.Resources>
  <Rectangle Width="50" Height="50" Fill="RoyalBlue"
   PointerPressed="StartAnimation">
    <Rectangle.RenderTransform>
      <RotateTransform x:Name="myTransform" Angle="45" CenterX="25" CenterY="25" />
    </Rectangle.RenderTransform>
  </Rectangle>
</StackPanel>
```

```xml
void StartAnimation (object sender, RoutedEventArgs e) {
    myStoryboard.Begin();
}
```

## <a name="span-idaccounting_for_coordinate_frames_of_reference_at_run_timespanspan-idaccounting_for_coordinate_frames_of_reference_at_run_timespanspan-idaccounting_for_coordinate_frames_of_reference_at_run_timespanaccounting-for-coordinate-frames-of-reference-at-run-time"></a><span id="Accounting_for_coordinate_frames_of_reference_at_run_time"></span><span id="accounting_for_coordinate_frames_of_reference_at_run_time"></span><span id="ACCOUNTING_FOR_COORDINATE_FRAMES_OF_REFERENCE_AT_RUN_TIME"></span>Учет координатных систем отсчета во время выполнения

В [**UIElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement) есть метод [**TransformToVisual**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.transformtovisual), способный создавать класс [**Transform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform), соотносящий координатные системы отсчета для двух элементов пользовательского интерфейса. Его можно использовать для сравнения элемента со стандартной для приложения координатной системой отсчета, если вы передаете в качестве первого параметра корневой визуальный элемент. Это может быть удобно в случае, если вы перенаправили событие ввода из другого элемента или пытаетесь предсказать поведение макета без фактического запроса этапа разметки.

Данные о событии, полученные из событий указателя, обеспечивают доступ к методу [**GetCurrentPoint**](https://docs.microsoft.com/uwp/api/windows.ui.input.pointerpoint.getcurrentpoint), где вы можете указать параметр *relativeTo*, чтобы заменить координатную систему отсчета определенным элементом, а не стандартным значением приложения. Этот подход просто вызывает внутреннее применение преобразования переноса и преобразует данные координат x-y при создании возвращенного объекта [**PointerPoint**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.PointerPoint).

## <a name="span-iddescribing_a_transform_mathematicallyspanspan-iddescribing_a_transform_mathematicallyspanspan-iddescribing_a_transform_mathematicallyspandescribing-a-transform-mathematically"></a><span id="Describing_a_transform_mathematically"></span><span id="describing_a_transform_mathematically"></span><span id="DESCRIBING_A_TRANSFORM_MATHEMATICALLY"></span>Математическое описание преобразования

Преобразование можно описать с помощью матрицы преобразования. Для описания преобразований на двухмерной плоскости x-y применяется матрица 3×3. Матрицы аффинных преобразований можно умножать в целях формирования любого количества таких линейных преобразований, как поворот и наклон (сдвиг), вслед за которыми выполняется перенос. Последний столбец матрицы аффинных преобразований равен (0, 0, 1), поэтому в математическом описании вам нужно определить только члены первых двух столбцов.

Математическое описание преобразования может быть полезно, если у вас есть математический опыт или вы знакомы с методами программирования графики, которые также используют матрицы для описания преобразований пространства координат. Существует класс, производный от класса [**Transform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform) и позволяющий выразить преобразование непосредственно в виде матрицы 3×3, — [**MatrixTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.MatrixTransform). Класс **MatrixTransform** имеет свойство [**Matrix**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrixtransform.matrix), которое содержит структуру из шести свойств: [**M11**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m11), [**M12**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m12), [**M21**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m21), [**M22**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m22), [**OffsetX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.offsetx) и [**OffsetY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.offsety). В каждом свойстве [**Matrix**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Matrix) используется значение **Double**, которое соотносится с шестью релевантными значениями (в столбцах 1 и 2) матрицы аффинного преобразования.

|                                             |                                             |     |
|---------------------------------------------|---------------------------------------------|-----|
| [**M11**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m11)         | [**M21**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m21)         | 0   |
| [**M12**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m12)         | [**M22**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m22)         | 0   |
| [**OffsetX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.offsetx) | [**OffsetY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.offsety) | 1   |

 

Любое преобразование, которое можно описать с помощью [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform), [**ScaleTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ScaleTransform), [**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform) или [**SkewTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SkewTransform), можно точно так же описать при помощи [**MatrixTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.MatrixTransform) со значением [**Matrix**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Matrix). Но обычно вы просто используете **TranslateTransform** и другие классы, потому что проще представить себе свойства этих классов преобразования, чем задавать компоненты вектора в **Matrix**. Также проще анимировать отдельные свойства преобразований, тогда как **Matrix** — фактически структура, а не [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject), и поэтому не может поддерживать анимированные отдельные значения.

Ряд средств проектирования на XAML, позволяющих применять операции преобразования, будет упорядочивать результаты в виде [**MatrixTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.MatrixTransform). При этом, возможно, лучше снова использовать то же средство проектирования, чтобы изменить эффект преобразования и переупорядочить XAML, вместо того чтобы пытаться манипулировать значениями [**Matrix**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Matrix) непосредственно в XAML.

## <a name="span-id3-d_transformsspanspan-id3-d_transformsspanspan-id3-d_transformsspan3-d-transforms"></a><span id="3-D_transforms"></span><span id="3-d_transforms"></span><span id="3-D_TRANSFORMS"></span>Трехмерные преобразования

В Windows 10 XAML появилось новое свойство, [**UIElement.Transform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.transform3d), которое можно использовать для создания трехмерных эффектов пользовательского интерфейса. Для этого используйте [**PerspectiveTransform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.media3d.perspectivetransform3d), чтобы добавить общую трехмерную перспективу ("камеру") к сцене, а затем с помощью [**CompositeTransform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.media3d.compositetransform3d) преобразуйте элемент в трехмерном пространстве так же, как при использовании [**CompositeTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.CompositeTransform). Описание реализации трехмерных преобразований см. в разделе [**UIElement.Transform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.transform3d).

 Для простых трехмерных эффектов, которые применяются только к одному объекту, можно использовать свойство [**UIElement.Projection**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.projection). Использование значения [**PlaneProjection**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PlaneProjection) для этого свойства эквивалентно применению фиксированного преобразования перспективы и одного или нескольких трехмерных преобразований к элементу. Этот тип преобразования подробно описан в разделе [Объемные эффекты перспективы для пользовательского интерфейса на XAML](3-d-perspective-effects.md).

## <a name="span-idrelated_topicsspanrelated-topics"></a><span id="related_topics"></span>Связанные темы

* [**UIElement.Transform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.transform3d)
* [Трехмерные эффекты для пользовательского интерфейса XAML](3-d-perspective-effects.md)
* [**Преобразование**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform)
 

 





