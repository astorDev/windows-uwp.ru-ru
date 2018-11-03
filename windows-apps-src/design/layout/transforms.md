---
author: Jwmsft
ms.assetid: F46D5E18-10A3-4F7B-AD67-76437C77E4BC
title: Обзор преобразований
description: Узнайте, как использовать преобразования в API среды выполнения Windows путем изменения относительных систем координат элементов в пользовательском интерфейсе.
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 2cfc7c34363bc05e13e618deccc44fada6dec96f
ms.sourcegitcommit: 144f5f127fc4fbd852f2f6780ef26054192d68fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5981728"
---
# <a name="transforms-overview"></a>Обзор преобразований

Узнайте, как использовать преобразования в API среды выполнения Windows путем изменения относительных систем координат элементов в пользовательском интерфейсе. Эти действия могут применяться для корректировки внешнего вида отдельных XAML-элементов (например, при изменении масштаба, повороте или преобразовании положения в пространстве x-y).

## <a name="span-idwhatisatransformspanspan-idwhatisatransformspanspan-idwhatisatransformspanwhat-is-a-transform"></a><span id="What_is_a_transform_"></span><span id="what_is_a_transform_"></span><span id="WHAT_IS_A_TRANSFORM_"></span>Что такое преобразование?

*Преобразование* определяет порядок сопоставления (или преобразования) точек при переходе из одного пространства координат в другое. Когда преобразование применяется к элементу пользовательского интерфейса, это приводит к изменению способа отрисовки элемента на экране как части пользовательского интерфейса.

Преобразования можно поделить на четыре широкие категории: перевод, вращение, масштабирование и наклон (или сдвиг). При использовании API графики для изменения внешнего вида элементов пользовательского интерфейса обычно проще всего создавать преобразования, которые за один раз определяют только одну операцию трансформации. Поэтому в среде выполнения Windows для каждой из этих категорий преобразования определен отдельный класс.

-   [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027): переносит элемент в пространстве x-y путем задания значений [**X**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.translatetransform.x.aspx) и [**Y**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.translatetransform.y).
-   [**ScaleTransform**](https://msdn.microsoft.com/library/windows/apps/BR242940): масштабирует преобразование, считая от центральной точки, путем задания значений [**CenterX**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.scaletransform.centerx.aspx), [**CenterY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.scaletransform.centery.aspx), [**ScaleX**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.scaletransform.scalex.aspx) и [**ScaleY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.scaletransform.scaleyproperty).
-   [**RotateTransform**](https://msdn.microsoft.com/library/windows/apps/BR242932): выполняет поворот в пространстве x-y путем задания значений [**Angle**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.rotatetransform.angle.aspx), [**CenterX**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.rotatetransform.centerx.aspx) и [**CenterY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.rotatetransform.centery).
-   [**SkewTransform**](https://msdn.microsoft.com/library/windows/apps/BR242950): выполняет сдвиг или наклон в пространстве x-y путем задания значений [**AngleX**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.skewtransform.anglex.aspx), [**AngleY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.skewtransform.angley.aspx), [**CenterX**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.skewtransform.centerx.aspx) и [**CenterY**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.scaletransform.centeryproperty).

Скорее всего, для сценариев пользовательского интерфейса вы обычно будете использовать [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027) и [**ScaleTransform**](https://msdn.microsoft.com/library/windows/apps/BR242940).

Преобразования можно комбинировать с помощью двух классов среды выполнения Windows: [**CompositeTransform**](https://msdn.microsoft.com/library/windows/apps/BR228105) и [**TransformGroup**](https://msdn.microsoft.com/library/windows/apps/BR243022). В **CompositeTransform** преобразования применяются в следующем порядке: масштабирование, наклон, поворот и перенос. Если вы хотите применять преобразования в другом порядке, используйте **TransformGroup** вместо **CompositeTransform**. Подробнее: [**CompositeTransform**](https://msdn.microsoft.com/library/windows/apps/BR228105).

## <a name="span-idtransformsandlayoutspanspan-idtransformsandlayoutspanspan-idtransformsandlayoutspantransforms-and-layout"></a><span id="Transforms_and_layout"></span><span id="transforms_and_layout"></span><span id="TRANSFORMS_AND_LAYOUT"></span>Преобразования и макет

В макете XAML преобразования применяются после завершения этапа разметки. Поэтому вычисление доступного пространства и принятие других решений относительно макета выполнялось еще до применения преобразований. Так как сначала создается макет, иногда вы будете получать неожиданные результаты при преобразовании элементов, находящихся в ячейке [**Grid**](https://msdn.microsoft.com/library/windows/apps/BR242704) или похожем контейнере макета, которому в процессе разметки выделяется пространство. Преобразованный элемент может оказаться усеченным или загороженным, поскольку его пытаются переместить в область, для которой при делении пространства родительского контейнера не учитывались размеры после преобразования. Возможно, вам стоит поэкспериментировать с результатами преобразования и скорректировать некоторые параметры. Например, вместо использования адаптивного макета и изменения размера с помощью Star вам, возможно, придется изменить свойства **Center** или объявить фиксированные размеры в пикселях для пространств макета, чтобы гарантировать, что родительский элемент выделяет достаточно места.

**Примечание о миграции.** Windows Presentation Foundation (WPF) имеет свойство **LayoutTransform**, применяющее преобразования перед этапом разметки. Однако XAML среды выполнения Windows не поддерживает свойство **LayoutTransform**. (В Microsoft Silverlight нет этого свойства.)

## <a name="span-idapplyingatransformtoauielementspanspan-idapplyingatransformtoauielementspanspan-idapplyingatransformtoauielementspanapplying-a-transform-to-a-ui-element"></a><span id="Applying_a_transform_to_a_UI_element"></span><span id="applying_a_transform_to_a_ui_element"></span><span id="APPLYING_A_TRANSFORM_TO_A_UI_ELEMENT"></span>Применение преобразования к элементу пользовательского интерфейса

Применение преобразования к объекту обычно выполняется для назначения свойства [**UIElement.RenderTransform**](https://msdn.microsoft.com/library/windows/apps/BR208980). Назначение этого свойства не приводит к буквальному попиксельному изменению объекта. На самом деле назначение свойства состоит в применении преобразования в локальном пространстве координат, в котором существует объект. Затем с помощью логики и операции отрисовки (после создания макета) отрисовываются комбинированные пространства координат, создавая впечатление, что изменился внешний вид объекта, а также, возможно, его позиция в макете (если применялся класс [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027)).

По умолчанию центр каждого преобразования отрисовки совпадает с началом локальной системы координат целевого объекта(0,0). Единственным исключением является [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027), в котором нет задаваемых свойств выравнивания по центру, так как эффект переноса остается неизменным независимо от местонахождения центра. Но у каждого из остальных преобразований есть свойства, задающие значения **CenterX** и **CenterY**.

Каждый раз, когда вы используете преобразования по отношению к [**UIElement.RenderTransform**](https://msdn.microsoft.com/library/windows/apps/BR208980), помните, что в [**UIElement**](https://msdn.microsoft.com/library/windows/apps/BR208911) есть другое свойство, влияющее на поведение преобразования,— [**RenderTransformOrigin**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.rendertransformorigin.aspx). **RenderTransformOrigin** объявляет, нужно ли применять полное преобразование к точке по умолчанию (0,0) элемента или же к другой точке начала координат в относительном координатном пространстве данного элемента. В случае типичных элементов (0,0) означает, что преобразование размещается в верхнем левом углу. В зависимости от желаемого эффекта вы можете изменять **RenderTransformOrigin** вместо корректировки значений **CenterX** и **CenterY** при преобразованиях. Учтите, что если применяется и значение **RenderTransformOrigin**, и значение **CenterX** / **CenterY**, результаты могут оказаться неожиданными, особенно если вы анимируете какие-либо из этих значений.

Для проверки столкновения фигур неправильной формы объект, к которому применяется преобразование, продолжает отвечать на входные данные ожидаемым образом, который согласуется с внешним видом объекта в пространстве x-y. Например, если вы использовали [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027) для перемещения [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) на 400пикселей в сторону в пользовательском интерфейсе, **Rectangle** может реагировать на события [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.pointerpressed.aspx), когда пользователь нажимает точку, где визуально появляется **Rectangle**. Вы не получите ложных событий, если пользователь нажмет область, в которой **Rectangle** находился перед переносом. Применение преобразования не влияет на аспекты индекса z, влияющие на тестирование нажатия. Индекс z, определяющий, какой именно элемент обрабатывает события ввода для точки пространства x-y, по-прежнему оценивается с помощью дочернего порядка, объявленного в контейнере. Обычно применяется тот же порядок, что и при объявлении элементов в XAML, хотя для дочерних элементов объекта [**Canvas**](https://msdn.microsoft.com/library/windows/apps/BR209267) его можно скорректировать путем применения присоединенного свойства [**Canvas.ZIndex**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.zindex.aspx) к дочерним элементам.

## <a name="span-idothertransformpropertiesspanspan-idothertransformpropertiesspanspan-idothertransformpropertiesspanother-transform-properties"></a><span id="Other_transform_properties"></span><span id="other_transform_properties"></span><span id="OTHER_TRANSFORM_PROPERTIES"></span>Другие свойства преобразования

-   
              Свойства [**Brush.Transform**](https://msdn.microsoft.com/library/windows/apps/BR228082), [**Brush.RelativeTransform**](https://msdn.microsoft.com/library/windows/apps/BR228080) влияют на то, как[**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) использует пространство координат в области, к которой применили **Brush** для задания таких визуальных свойств, как передние планы и фоны. Эти преобразования обычно не применяются для большинства распространенных кистей (для которых обычно задается сплошной цвет с помощью свойства [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/BR242962)), но иногда могут оказаться полезными при рисовании областей при помощи [**ImageBrush**](https://msdn.microsoft.com/library/windows/apps/BR210101) или [**LinearGradientBrush**](https://msdn.microsoft.com/library/windows/apps/BR210108).
-   
              Свойство [**Geometry.Transform**](https://msdn.microsoft.com/library/windows/apps/BR210066) можно использовать для применения преобразования к геометрии перед использованием этой геометрии для значения свойства [**Path.Data**](https://msdn.microsoft.com/library/windows/apps/BR243356).

## <a name="span-idanimatingatransformspanspan-idanimatingatransformspanspan-idanimatingatransformspananimating-a-transform"></a><span id="Animating_a_transform"></span><span id="animating_a_transform"></span><span id="ANIMATING_A_TRANSFORM"></span>Анимация преобразования


              Объекты [**Transform**](https://msdn.microsoft.com/library/windows/apps/BR243006) можно анимировать. Чтобы анимировать **Transform**, примените анимацию совместимого типа к свойству, которое требуется анимировать. Как правило, это значит, что вы используете объекты [**DoubleAnimation**](https://msdn.microsoft.com/library/windows/apps/BR243136) или [**DoubleAnimationUsingKeyFrames**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.animation.doubleanimationusingkeyframes) для определения анимации, так как все свойства преобразования относятся к типу [**Double**](https://msdn.microsoft.com/library/windows/apps/xaml/system.double.aspx). Анимации, которые влияют на преобразование, используемое для значения [**UIElement.RenderTransform**](https://msdn.microsoft.com/library/windows/apps/BR208980), не считаются зависимыми даже тогда, когда их продолжительность не является нулевой. Подробнее о зависимых анимациях: [Раскадрованные анимации](/windows/uwp/design/motion/storyboarded-animations).

Если вы настраиваете анимацию свойств для создания эффекта, аналогичного преобразованию по визуальному представлению, например анимируете свойства [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) и [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) элемента [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/BR208706), а не применяете к нему [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027), такие анимации почти всегда считаются зависимыми. Вам придется включить анимации и, возможно, столкнуться с серьезным снижением производительности из-за этой анимации, особенно если вы пытаетесь поддерживать взаимодействие с пользователем во время анимации данного объекта. По этой причине предпочтительней использовать преобразование и анимировать его, а не любое другое свойство, где анимация воспринималась бы в качестве зависимой.

Чтобы выбрать цель преобразования, необходимо наличие класса [**Transform**](https://msdn.microsoft.com/library/windows/apps/BR243006), выступающего в качестве значения [**RenderTransform**](https://msdn.microsoft.com/library/windows/apps/BR208980). Обычно вы помещаете элемент для соответствующего типа преобразования в исходный код XAML, не задавая свойства для этого преобразования.

Как правило, для применения анимаций к свойствам преобразования используется метод косвенного выбора. Подробнее о синтаксисе косвенного выбора можно узнать в разделах [Раскадрованные анимации](/windows/uwp/design/motion/storyboarded-animations) и [Синтаксис пути к свойству](https://msdn.microsoft.com/library/windows/apps/Mt185586).

Иногда стандартные стили элементов управления определяют анимации преобразований как часть своего поведения визуального состояния. Например, визуальные состояния для [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/BR227538) используют анимированные значения [**RotateTransform**](https://msdn.microsoft.com/library/windows/apps/BR242932) для "кружения" точек в кольце.

Вот простой пример анимации преобразования. В данном случае анимируется свойство [**Angle**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.rotatetransform.angle.aspx) класса [**RotateTransform**](https://msdn.microsoft.com/library/windows/apps/BR242932) для вращения [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) на месте вокруг его визуального центра. В этом примере **RotateTransform** присваивается имя, и поэтому косвенный выбор анимации не требуется. Но вместо этого преобразование можно оставить безымянным, присвоить имя элементу, к которому применяется преобразование, и использовать косвенный выбор (например, `(UIElement.RenderTransform).(RotateTransform.Angle)`.

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

## <a name="span-idaccountingforcoordinateframesofreferenceatruntimespanspan-idaccountingforcoordinateframesofreferenceatruntimespanspan-idaccountingforcoordinateframesofreferenceatruntimespanaccounting-for-coordinate-frames-of-reference-at-run-time"></a><span id="Accounting_for_coordinate_frames_of_reference_at_run_time"></span><span id="accounting_for_coordinate_frames_of_reference_at_run_time"></span><span id="ACCOUNTING_FOR_COORDINATE_FRAMES_OF_REFERENCE_AT_RUN_TIME"></span>Учет координатных систем отсчета во время выполнения


              В [**UIElement**](https://msdn.microsoft.com/library/windows/apps/BR208911) есть метод с именем [**TransformToVisual**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.transformtovisual.aspx), способный создавать класс [**Transform**](https://msdn.microsoft.com/library/windows/apps/BR243006), соотносящий координатные системы отсчета для двух элементов пользовательского интерфейса. Его можно использовать для сравнения элемента со стандартной для приложения координатной системой отсчета, если вы передаете в качестве первого параметра корневой визуальный элемент. Это может быть удобно в случае, если вы перенаправили событие ввода из другого элемента или пытаетесь предсказать поведение макета без фактического запроса этапа разметки.

Данные о событии, полученные из событий указателя, обеспечивают доступ к методу [**GetCurrentPoint**](https://msdn.microsoft.com/library/windows/apps/BR212141), где вы можете указать параметр *relativeTo*, чтобы заменить координатную систему отсчета определенным элементом, а не стандартным значением приложения. Этот подход просто вызывает внутреннее применение преобразования переноса и преобразует данные координат x-y при создании возвращенного объекта [**PointerPoint**](https://msdn.microsoft.com/library/windows/apps/BR242038).

## <a name="span-iddescribingatransformmathematicallyspanspan-iddescribingatransformmathematicallyspanspan-iddescribingatransformmathematicallyspandescribing-a-transform-mathematically"></a><span id="Describing_a_transform_mathematically"></span><span id="describing_a_transform_mathematically"></span><span id="DESCRIBING_A_TRANSFORM_MATHEMATICALLY"></span>Математическое описание преобразования

Преобразование можно описать с помощью матрицы преобразования. Для описания преобразований на двухмерной плоскости x-y применяется матрица 3×3. Матрицы аффинных преобразований можно умножать в целях формирования любого количества таких линейных преобразований, как поворот и наклон (сдвиг), вслед за которыми выполняется перенос. Последний столбец матрицы аффинных преобразований равен (0, 0, 1), поэтому в математическом описании вам нужно определить только члены первых двух столбцов.

Математическое описание преобразования может быть полезно, если у вас есть математический опыт или вы знакомы с методами программирования графики, которые также используют матрицы для описания преобразований пространства координат. Существует класс, производный от класса [**Transform**](https://msdn.microsoft.com/library/windows/apps/BR243006) и позволяющий выразить преобразование непосредственно в виде матрицы 3×3 — [**MatrixTransform**](https://msdn.microsoft.com/library/windows/apps/BR210137). 
              Класс **MatrixTransform** имеет свойство [**Matrix**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.matrixtransform.matrix.aspx), которое содержит структуру из шести свойств: [**M11**](https://msdn.microsoft.com/library/windows/apps/Hh673847), [**M12**](https://msdn.microsoft.com/library/windows/apps/Hh673853), [**M21**](https://msdn.microsoft.com/library/windows/apps/Hh673851), [**M22**](https://msdn.microsoft.com/library/windows/apps/Hh673849), [**OffsetX**](https://msdn.microsoft.com/library/windows/apps/Hh673810) и [**OffsetY**](https://msdn.microsoft.com/library/windows/apps/Hh673816). В каждом свойстве [**Matrix**](https://msdn.microsoft.com/library/windows/apps/BR210127) используется значение **Double**, которое соотносится с шестью релевантными значениями (в столбцах 1 и 2) матрицы аффинного преобразования.

|                                             |                                             |     |
|---------------------------------------------|---------------------------------------------|-----|
| [**M11**](https://msdn.microsoft.com/library/windows/apps/Hh673847)         | [**M21**](https://msdn.microsoft.com/library/windows/apps/Hh673851)         | 0   |
| [**M12**](https://msdn.microsoft.com/library/windows/apps/Hh673853)         | [**M22**](https://msdn.microsoft.com/library/windows/apps/Hh673849)         | 0   |
| [**OffsetX**](https://msdn.microsoft.com/library/windows/apps/Hh673810) | [**OffsetY**](https://msdn.microsoft.com/library/windows/apps/Hh673816) | 1   |

 

Любое преобразование, которое можно описать с помощью [**TranslateTransform**](https://msdn.microsoft.com/library/windows/apps/BR243027), [**ScaleTransform**](https://msdn.microsoft.com/library/windows/apps/BR242940), [**RotateTransform**](https://msdn.microsoft.com/library/windows/apps/BR242932) или [**SkewTransform**](https://msdn.microsoft.com/library/windows/apps/BR242950), можно точно так же описать при помощи [**MatrixTransform**](https://msdn.microsoft.com/library/windows/apps/BR210137) со значением [**Matrix**](https://msdn.microsoft.com/library/windows/apps/BR210127). Но обычно вы просто используете **TranslateTransform** и другие классы, потому что проще представить себе свойства этих классов преобразования, чем задавать компоненты вектора в **Matrix**. Также проще анимировать отдельные свойства преобразований, тогда как **Matrix** — фактически структура, а не [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/BR242356), и поэтому не может поддерживать анимированные отдельные значения.

Ряд средств проектирования на XAML, позволяющих применять операции преобразования, будет упорядочивать результаты в виде [**MatrixTransform**](https://msdn.microsoft.com/library/windows/apps/BR210137). При этом, возможно, лучше снова использовать то же средство проектирования, чтобы изменить эффект преобразования и переупорядочить XAML, вместо того чтобы пытаться манипулировать значениями [**Matrix**](https://msdn.microsoft.com/library/windows/apps/BR210127) непосредственно в XAML.

## <a name="span-id3-dtransformsspanspan-id3-dtransformsspanspan-id3-dtransformsspan3-d-transforms"></a><span id="3-D_transforms"></span><span id="3-d_transforms"></span><span id="3-D_TRANSFORMS"></span>Трехмерные преобразования

В Windows 10 XAML появилось новое свойство, [**UIElement.Transform3D**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.transform3d.aspx), которое можно использовать для создания трехмерных эффектов пользовательского интерфейса. Для этого используйте [**PerspectiveTransform3D**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.media3d.perspectivetransform3d.aspx), чтобы добавить общую трехмерную перспективу ("камеру") к сцене, а затем с помощью [**CompositeTransform3D**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.media.media3d.compositetransform3d.aspx) преобразуйте элемент в трехмерном пространстве так же, как при использовании [**CompositeTransform**](https://msdn.microsoft.com/library/windows/apps/BR228105). Описание реализации трехмерных преобразований см. в разделе [**UIElement.Transform3D**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.transform3d.aspx).

 Для простых трехмерных эффектов, которые применяются только к одному объекту, можно использовать свойство [**UIElement.Projection**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.projection). Использование значения[**PlaneProjection**](https://msdn.microsoft.com/library/windows/apps/br210192) для этого свойства эквивалентно применению фиксированного преобразования перспективы и одного или нескольких трехмерных преобразований к элементу. Этот тип преобразования подробно описан в разделе [Объемные эффекты перспективы для пользовательского интерфейса на XAML](3-d-perspective-effects.md).

## <a name="span-idrelatedtopicsspanrelated-topics"></a><span id="related_topics"></span>Связанные разделы

* [**UIElement.Transform3D**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.transform3d.aspx)
* [Трехмерные эффекты для пользовательского интерфейса XAML](3-d-perspective-effects.md)
* [**Преобразование**](https://msdn.microsoft.com/library/windows/apps/BR243006)
 

 





