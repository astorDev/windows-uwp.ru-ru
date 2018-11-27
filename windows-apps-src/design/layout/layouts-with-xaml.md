---
Description: XAML gives you a flexible layout system to create a responsive UI.
title: Гибкие макеты в XAML
ms.date: 05/19/2017
ms.topic: article
keywords: Windows10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: 82a528b3ec98f56e1079e11ec1123d86de15d50f
ms.sourcegitcommit: 681c70f964210ab49ac5d06357ae96505bb78741
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2018
ms.locfileid: "7701649"
---
# <a name="responsive-layouts-with-xaml"></a>Гибкие макеты в XAML

Система макетов XAML позволяет использовать функцию автоматического выбора размера, панели макетов, визуальные состояния и даже отдельные определения пользовательского интерфейса для создания интерфейса с быстрым откликом. Благодаря гибкому макету ваше приложение будет прекрасно смотреться на экранах с различными размерами окна, разными разрешениями, плотностью пикселей и ориентацией. XAML также можно использовать для перемещения, изменения размера, показа/скрытия или изменения архитектуры пользовательского интерфейса приложения, как описано в статье [Методы создания отзывчивого дизайна](responsive-design.md). В этой статье описано, как реализовывать гибкие макеты с помощью XAML.

## <a name="fluid-layouts-with-properties-and-panels"></a>Гибкие макеты со свойствами и панелями

Основа гибкого макета — это правильное применение свойств и панелей макета XAML для перемещения, изменения размеров и адаптации содержимого. 

Система макетов XAML поддерживает как статические, так и гибкие макеты. В статическом макете элементам управления прямо назначаются размеры пикселей и положения. Если пользователь меняет разрешение или ориентацию устройства, пользовательский интерфейс не меняется. Статические макеты могут оказаться обрезанными при различных конструктивных параметрах и размерах изображения. Гибкие макеты — напротив, сжимаются, растягиваются и видоизменяются, адаптируясь к доступному на устройстве визуальному пространству экрана. 

На практике используется сочетание статических и гибких элементов для создания пользовательского интерфейса. В некоторых местах по-прежнему используются статические элементы и значения, однако следует обеспечивать, чтобы пользовательский интерфейс в целом адаптировался к различным разрешениям, размерам экрана и представлениям.

В этой статье описано, как создать гибки макет с помощью свойств и панелей макета XAML.

### <a name="layout-properties"></a>Свойства макета
Свойства макета управляют размером и положением элемента. Для создания гибкого макета используйте автоматический или пропорциональный размер элементов, чтобы дочерние элементы панелей макета могли разместиться необходимым способом. 

Ниже приведены некоторые распространенные свойства макета и способы их использования для создания гибких макетов.

**Height и Width**

Свойства [**Height**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.height.aspx) и [**Width**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.width.aspx) определяют размер элемента. Можно использовать статические значения, измеренные в эффективных пикселях, функцию автоматического выбора размера либо пропорциональный размер. 

Функция автоматического выбора размера изменяет размер элементов пользовательского интерфейса в соответствии с их содержимым или размером родительского контейнера. Можно также использовать функцию автоматического выбора размера со строками и столбцами сетки. Для использования функции автоматического выбора размеров установите для параметров Height и/или Width элементов пользовательского интерфейса значение **Auto**.

> [!NOTE]
> Будет ли элемент изменять размер согласно своему содержимому или контейнеру, зависит от того, как родительский контейнер обработает изменение размеров его дочерних элементов. Подробнее см. в разделе [Панели макета](#layout-panels) далее в этой статье.

Пропорциональное изменение размеров, также именуемое *задание размера с помощью звезды*, распределяет доступное пространство среди строк и столбцов сетки с помощью взвешенных пропорций. В языке XAML значения звезд выражены как \* (или *n*\* для пропорционального задания размера с помощью звезды). Например, чтобы указать, что один столбец двухстолбцового макета в пять раз шире, чем другой, используйте значения «5\*» и «\*» для свойств [**Width**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.columndefinition.width.aspx) в элементах [**ColumnDefinition**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.columndefinition.aspx).

В этом примере сочетаются фиксированный, автоматический и пропорциональный размер в элементе [**Grid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx) с четырьмя столбцами.

&nbsp;|&nbsp;|&nbsp;
------|------|------
Column_1 | **Auto (Автоматически)** | Размер столбца автоматически подстраивается под содержимое.
Column_2 | * | После вычисления значений ширины для автоматических столбцов этот столбец получает часть оставшейся ширины. Ширина столбца Column_2 будет занимать половину ширины столбца Column_4.
Column_3 | **44** | Столбец будет иметь ширину 44пикселя.
Column_4 | **2**\* | После вычисления значений ширины для столбцов с автоматическим подбором размера этот столбец получает часть оставшейся ширины. Ширина столбца Column_4 будет в два раза больше ширины столбца Column_2.

Ширина столбцов по умолчанию составляет «*», поэтому не нужно явным образом задавать это значение для второго столбца.

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="Auto"/>
        <ColumnDefinition/>
        <ColumnDefinition Width="44"/>
        <ColumnDefinition Width="2*"/>
    </Grid.ColumnDefinitions>
    <TextBlock Text="Column 1 sizes to its conent." FontSize="24"/>
</Grid>
```

В конструкторе XAML в Visual Studio результат выглядит следующим образом.

![Сетка 4 столбцов в конструкторе Visual Studio](images/xaml-layout-grid-in-designer.png)

Чтобы получить размер элемента во время выполнения, используйте свойства только для чтения [**ActualHeight**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.actualheight.aspx) и [**ActualWidth**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.actualwidth.aspx) вместо Height и Width.

**Ограничения размера**

При автоматическом выборе размера в вашем пользовательском интерфейсе все же может потребоваться установить ограничения для размера элемента. Вы можете задать свойства [**MinWidth**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.minwidth.aspx)/[**MaxWidth**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.maxwidth.aspx) и [**MinHeight**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.minheight.aspx)/[**MaxHeight**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.maxheight.aspx), чтобы определить значения, которые ограничивают размер элемента, но в то же время обеспечивают его гибкое изменение.

В элементах Grid свойство MinWidth/MaxWidth можно также использовать с определениями столбца. Свойство MinHeight/MaxHeight можно использовать с определениями строки.

**Выравнивание**

Используйте свойства [**HorizontalAlignment**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.horizontalalignment.aspx) и [**VerticalAlignment**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.verticalalignment.aspx), чтобы указать, как элемент должен быть расположен в выделенном родительском контейнере.
- Значения для **HorizontalAlignment**: **Left**, **Center**, **Right** и **Stretch**.
- Значения для **VerticalAlignment**: **Top**, **Center**, **Bottom** и **Stretch**.

При выравнивании **Stretch** элементы занимают все место в соответствии с родительским контейнером. Stretch является значением по умолчанию для обоих свойств выравнивания. Однако некоторые элементы управления, например [**Button**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.button.aspx), перезаписывают это значение в своем стиле по умолчанию.
Любой элемент, который может содержать дочерние элементы, может интерпретировать значение Stretch только для свойств HorizontalAlignment и VerticalAlignment. Например, элемент, использующий значения Stretch по умолчанию в Grid, растягивается, заполняя ячейку, которая его содержит. Тот же элемент, помещенный в Canvas, принимает размеры его содержимого. Подробнее о том, как каждая панель обрабатывает значение Stretch, см. в статье [Панели макета](layout-panels.md).

Подробнее см. в статье [Выравнивание, поле и заполнение](alignment-margin-padding.md) и на справочных страницах [**HorizontalAlignment**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.horizontalalignment.aspx) и [**VerticalAlignment**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.verticalalignment.aspx).

**Видимость**

Можно показать или скрыть элемент, задав свойству [**Visibility**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.visibility.aspx) одно из значений перечисления [**Visibility**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.visibility.aspx): **Visible** или **Collapsed**. Когда элемент свернут (Collapsed), он не занимает место в макете пользовательского интерфейса.

Можно изменить свойство Visibility элемента в коде или в визуальном состоянии. Когда свойство Visibility элемента изменено, все его дочерние элементы также изменяются. Вы можете заменить разделы своего пользовательского интерфейса путем отображения одной панели и сворачивания другой.

> [!Tip]
> Если у вас есть элементы в пользовательском Интерфейсе, которые являются **Collapsed** по умолчанию, объекты продолжают создаваться при запуске, даже если они невидимы. Вы можете отложить загрузку этих элементов до их отображения, установив для атрибута **x:DeferLoadStrategy** значение «Lazy». Это может улучшить производительность при запуске. Подробнее см. в разделе [Атрибут x:DeferLoadStrategy](../../xaml-platform/x-deferloadstrategy-attribute.md).

### <a name="style-resources"></a>Ресурсы стиля

Вам не придется задавать значение свойства по отдельности для элемента управления. Обычно такой способ более эффективен для группировки значений свойств в ресурс [**Style**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.style.aspx) и применения Style к элементу управления. Это особенно актуально, когда необходимо применить одинаковые значения свойства к многочисленным элементам управления. Подробнее об использовании стилей см. в разделе [Настройка стиля элементов управления](../controls-and-patterns/xaml-styles.md).

### <a name="layout-panels"></a>Панели макета

Чтобы разместить видимые объекты, необходимо поместить их в элемент «Панель» или в другой объект-контейнер. Платформа XAML предоставляет различные классы panel, такие как [**Canvas**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.aspx), [**Grid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx), [**RelativePanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.aspx) и [**StackPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.stackpanel.aspx), которые служат контейнерами для размещения элементов пользовательского интерфейса.

Выбирая панель, главное — определить, как панель размещает свои дочерние элементы и задает их размеры. Также может потребоваться учесть характер перекрытия дочерних элементов, наложенных друг на друга.

Ниже показано сравнение основных функций элементов управления панели, предоставленных в платформе XAML.

Элемент управления панели | Описание
--------------|------------
[**Холст**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.canvas.aspx) | **Canvas** не поддерживает гибкий пользовательский интерфейс. Вы управляете всеми аспектами размещения дочерних элементов и определения их размеров. Он обычно применяется в особых случаях, например, при создании графики или определении небольших статических областей большего адаптивного пользовательского интерфейса. Вы можете использовать код или визуальные состояния для перемещения элементов во время выполнения.<li>Абсолютное положение элементов задается присоединенными свойствами Canvas.Top и Canvas.Left.</li><li>Многоуровневое расположение можно явным образом указать с помощью присоединенного свойства Canvas.ZIndex.</li><li>Значения Stretch для параметра HorizontalAlignment/VerticalAlignment игнорируются. Если размер элемента явным образом не задан, его размер подбирается по содержимому.</li><li>Содержимое дочерних элементов визуально не сокращается, если его размер больше панели. </li><li>Содержимое дочерних элементов может выходить за границы панели.</li>
[**Сетка**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.grid.aspx) | **Grid** поддерживает гибкое изменение размера дочерних элементов. Вы можете использовать код или визуальные состояния для перемещения элементов и адаптации.<li>Элементы упорядочиваются в строки и столбцы с помощью прикрепленных свойств Grid.Row и Grid.Column.</li><li>Присоединенные свойства Grid.RowSpan и Grid.ColumnSpan позволяют распространить элементы на несколько строк или столбцов.</li><li>Значения Stretch для параметра HorizontalAlignment/VerticalAlignment учитываются. Если размер элемента явным образом не задан, он растягивается на все доступное пространство ячейки сетки.</li><li>Содержимое дочерних элементов визуально обрезается, если его размер больше панели.</li><li>Размер содержимого ограничен границами панели, поэтому в прокручиваемом содержимом отображаются полосы прокрутки, если необходимо.</li>
[**RelativePanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.aspx) | <li>Элементы упорядочены относительно края или центра панели и относительно друг друга.</li><li>Элементы размещаются с помощью различных присоединенных свойств, которые управляют выравниванием панели, выравниванием элементов одного уровня и расположением элементов одного уровня. </li><li>Значения Stretch для параметров HorizontalAlignment/VerticalAlignment игнорируются, если присоединенные свойства RelativePanel для выравнивания не будут вызывать растягивание (например, элемент выравнивается по правому и левому краям панели). Если размер элемента не задан явно и он не растянут, то размер соответствует его содержимому.</li><li>Содержимое дочерних элементов визуально обрезается, если его размер больше панели.</li><li>Размер содержимого ограничен границами панели, поэтому в прокручиваемом содержимом отображаются полосы прокрутки, если необходимо.</li>
[**StackPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.stackpanel.aspx) |<li>Элементы размещаются стопкой на одной линии по вертикали или горизонтали.</li><li>Значения Stretch для параметров HorizontalAlignment/VerticalAlignment соблюдаются в направлении, противоположном свойству Orientation. Если размер элемента явным образом не задан, он растягивается на всю доступную ширину (или высоту, если Orientation имеет значение Horizontal). В направлении, определенном свойством Orientation, размер элемента соответствует его содержимому.</li><li>Содержимое дочерних элементов визуально обрезается, если его размер больше панели.</li><li>Размер содержимого может выходить за границы панели в направлении, определенном свойством Orientation. Поэтому содержимое с возможностью прокрутки растягивается за границы панели, и в нем не отображаются полосы прокрутки. Необходимо явным образом ограничить высоту (или ширину) содержимого дочерних элементов, чтобы сделать полосы прокрутки видимыми.</li>
[**VariableSizedWrapGrid**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.variablesizedwrapgrid.aspx) |<li>Элементы упорядочены по строкам и столбцам с автоматическим переносом на новую строку или столбец при достижении значения MaximumRowsOrColumns.</li><li>Упорядочиваются ли элементы по строкам и столбцам, определяется свойством Orientation.</li><li>Присоединенные свойства VariableSizedWrapGrid.RowSpan и VariableSizedWrapGrid.ColumnSpan позволяют распространить элементы на несколько строк или столбцов.</li><li>Значения Stretch для параметра HorizontalAlignment/VerticalAlignment игнорируются. Размеры элементов задаются свойствами ItemHeight и ItemWidth. Если эти свойства не заданы, размер элемента в первой ячейке соответствует его содержимому, и все остальные ячейки наследуют этот размер.</li><li>Содержимое дочерних элементов визуально обрезается, если его размер больше панели.</li><li>Размер содержимого ограничен границами панели, поэтому в прокручиваемом содержимом отображаются полосы прокрутки, если необходимо.</li>

Подробнее об этих панелях и примеры см. в разделе [Панели макета](layout-panels.md). См. также раздел [Пример адаптивных методик](http://go.microsoft.com/fwlink/p/?LinkId=620024).

Панели макета позволяют упорядочить пользовательский интерфейс в логические группы элементов управления. При их использовании с надлежащими параметрами свойства можно получить дополнительную поддержку для функции автоматического выбора размера, передать и переформатировать элементы пользовательского интерфейса. Однако большинство макетов пользовательского интерфейса следует изменить при изменении размера окна. Для этого можно использовать визуальные состояния.

## <a name="adaptive-layouts-with-visual-states-and-state-triggers"></a>Адаптивные макеты с визуальными состояниями и триггерами состояния
Используйте визуальные состояния, чтобы существенно изменить пользовательский интерфейс на основе размера окна или внести другие правки.

Когда окно вашего приложения увеличится или уменьшится до размеров, выходящих за указанный диапазон, возможно, вы захотите изменить свойства макета, чтобы перемещать окна и изменять их размеры, адаптировать, отображать или заменять разделы вашего пользовательского интерфейса. Вы можете определять различные визуальные состояния пользовательского интерфейса и применять их, когда ширина или высота окна превысит заданное пороговое значение. 

[**AdaptiveTrigger**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.adaptivetrigger.aspx) предоставляет простой способ установки порогового значения (или «точки останова»), при котором применяется состояние. [**VisualState**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.visualstate.aspx) определяет значения свойства, применяемые к элементу, когда он находится в определенном состоянии. Сгруппируйте визуальные состояния в [**VisualStateManager**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.visualstatemanager.aspx), который применяет соответствующее VisualState при определенных условиях.

### <a name="set-visual-states-in-code"></a>Задание визуальных состояний в коде

Для применения визуального состояния из кода вызовите метод [**VisualStateManager.GoToState**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.visualstatemanager.gotostate.aspx). Например, чтобы применить состояние, когда окно приложения имеет определенный размер, обработайте событие [**SizeChanged**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.window.sizechanged.aspx) и вызовите **GoToState** для применения соответствующего состояния.

Здесь [**VisualStateGroup**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.visualstategroup.aspx) содержит два определения VisualState. Первое, `DefaultState`, пустое. Если оно применяется, применяются значения, определенные на странице XAML. Второе, `WideState`, изменяет свойство [**DisplayMode**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.splitview.displaymode.aspx) параметра [**SplitView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.splitview.aspx) на **Inline** и открывает панель. Это состояние применяется в обработчике событий SizeChanged, если ширина окна превышает 640 эффективных пикселей.

> [!NOTE]
> Windows не предоставляет средств, благодаря которым приложение могло бы определить конкретное устройство, на котором оно запущено. Система способна определить тип устройства (мобильное, настольное и т. д.), на котором запущено приложение, эффективное разрешение и размер экранного пространства, доступного для приложения (размер окна приложения). Мы рекомендуем задавать визуальные состояния для [размеров экрана и точек прерывания](screen-sizes-and-breakpoints-for-responsive-design.md).


```xaml
<Page ...>
    <Grid>
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup>
                <VisualState x:Name="DefaultState">
                        <Storyboard>
                        </Storyboard>
                    </VisualState>

                <VisualState x:Name="WideState">
                    <Storyboard>
                        <ObjectAnimationUsingKeyFrames
                            Storyboard.TargetProperty="SplitView.DisplayMode"
                            Storyboard.TargetName="mySplitView">
                            <DiscreteObjectKeyFrame KeyTime="0">
                                <DiscreteObjectKeyFrame.Value>
                                    <SplitViewDisplayMode>Inline</SplitViewDisplayMode>
                                </DiscreteObjectKeyFrame.Value>
                            </DiscreteObjectKeyFrame>
                        </ObjectAnimationUsingKeyFrames>
                        <ObjectAnimationUsingKeyFrames
                            Storyboard.TargetProperty="SplitView.IsPaneOpen"
                            Storyboard.TargetName="mySplitView">
                            <DiscreteObjectKeyFrame KeyTime="0" Value="True"/>
                        </ObjectAnimationUsingKeyFrames>
                    </Storyboard>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <SplitView x:Name="mySplitView" DisplayMode="CompactInline"
                   IsPaneOpen="False" CompactPaneLength="20">
            <!-- SplitView content -->

            <SplitView.Pane>
                <!-- Pane content -->
            </SplitView.Pane>
        </SplitView>
    </Grid>
</Page>
```

```csharp
private void CurrentWindow_SizeChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
{
    if (e.Size.Width > 640)
        VisualStateManager.GoToState(this, "WideState", false);
    else
        VisualStateManager.GoToState(this, "DefaultState", false);
}
```

### <a name="set-visual-states-in-xaml-markup"></a>Задание визуальных состояний в разметке XAML

До Windows10 определения VisualState требовали объектов [**Storyboard**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.animation.storyboard.aspx) для изменения свойств, и необходимо было вызвать **GoToState** в коде для применения состояния. Это демонстрируется в предыдущем примере. Вы увидите много примеров, которые также используют этот синтаксис. Возможно, у вас даже есть код, в котором он используется.

Начиная c Windows10, можно использовать упрощенный синтаксис [**Setter**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.setter.aspx), показанный здесь, и использовать объекты [**StateTrigger**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.statetrigger.aspx) в разметке XAML для применения состояния. Триггеры состояния используется для создания своих простых правил, которые автоматически вызывают изменения визуального состояния в ответ на события приложения.

В этом примере выполняются те же действия, что и в предыдущем, но используется упрощенный синтаксис **Setter** вместо Storyboard для определения изменений свойств. И вместо вызова GoToState в нем используется встроенный триггер состояния [**AdaptiveTrigger**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.adaptivetrigger.aspx) для применения состояния. При использовании триггеров состояния не нужно указывать пустое состояние `DefaultState`. Параметры по умолчанию повторно автоматически применяются при игнорировании условий триггера состояния.

```xaml
<Page ...>
    <Grid>
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup>
                <VisualState>
                    <VisualState.StateTriggers>
                        <!-- VisualState to be triggered when the
                             window width is >=720 effective pixels. -->
                        <AdaptiveTrigger MinWindowWidth="640" />
                    </VisualState.StateTriggers>

                    <VisualState.Setters>
                        <Setter Target="mySplitView.DisplayMode" Value="Inline"/>
                        <Setter Target="mySplitView.IsPaneOpen" Value="True"/>
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <SplitView x:Name="mySplitView" DisplayMode="CompactInline"
                   IsPaneOpen="False" CompactPaneLength="20">
            <!-- SplitView content -->

            <SplitView.Pane>
                <!-- Pane content -->
            </SplitView.Pane>
        </SplitView>
    </Grid>
</Page>
```

> [!Important]
> В предыдущем примере прикрепленное свойство VisualStateManager.visualstategroups настраивается элемент **Grid** . При использовании StateTriggers всегда следует убедиться, что свойство VisualStateGroups является присоединенным к первому дочернему элементу корня, чтобы триггеры запускались автоматически. (Здесь **Grid** — это первый дочерний элемент корня **Page**).

### <a name="attached-property-syntax"></a>Синтаксис присоединенного свойства

В VisualState обычно задается значение для свойства элемента управления или для одного из присоединенных свойств панели, содержащей элемент управления. Когда вы задаете присоединенное свойство, используйте скобки вокруг имени присоединенного свойства.

В этом примере показано, как задать присоединенное свойство [**RelativePanel.AlignHorizontalCenterWithPanel**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.relativepanel.alignhorizontalcenterwithpanel.aspx) в TextBox с именем `myTextBox`. Первый XAML использует синтаксис [**ObjectAnimationUsingKeyFrames**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.animation.objectanimationusingkeyframes.aspx), а второй— синтаксис **Setter**.

```xaml
<!-- Set an attached property using ObjectAnimationUsingKeyFrames. -->
<ObjectAnimationUsingKeyFrames
    Storyboard.TargetProperty="(RelativePanel.AlignHorizontalCenterWithPanel)"
    Storyboard.TargetName="myTextBox">
    <DiscreteObjectKeyFrame KeyTime="0" Value="True"/>
</ObjectAnimationUsingKeyFrames>

<!-- Set an attached property using Setter. -->
<Setter Target="myTextBox.(RelativePanel.AlignHorizontalCenterWithPanel)" Value="True"/>
```

### <a name="custom-state-triggers"></a>Пользовательские триггеры состояния

Вы можете расширить класс [**StateTrigger**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.statetrigger.aspx), чтобы создавать пользовательские триггеры для широкого диапазона сценариев. Например, вы можете создать StateTrigger, чтобы активировать различные состояния на основании типа ввода, а затем расширять поля вокруг элемента управления, когда тип ввода является сенсорным. Также StateTrigger позволяет применять различные состояния, основанные на семействах устройств, на которых запущено приложение. Примеры создания пользовательских триггеров и способы их использования для оптимизации пользовательского интерфейса из единого представления XAML см. в разделе [Пример триггеров состояния](http://go.microsoft.com/fwlink/p/?LinkId=620025).

### <a name="visual-states-and-styles"></a>Визуальные состояния и стили

Ресурсы Style можно использовать в визуальных состояниях для применения набора изменений свойств к нескольким элементам управления. Подробнее об использовании стилей см. в разделе [Настройка стиля элементов управления](../controls-and-patterns/xaml-styles.md).

В этом упрощенном XAML из раздела «Пример триггеров состояния» ресурс Style применяется к Button для настройки размера и полей для ввода мышью или сенсорного ввода. Полный код и определение пользовательского триггера состояния см. в разделе [Пример триггеров состояния](http://go.microsoft.com/fwlink/p/?LinkId=620025).

```xaml
<Page ... >
    <Page.Resources>
        <!-- Styles to be used for mouse vs. touch/pen hit targets -->
        <Style x:Key="MouseStyle" TargetType="Rectangle">
            <Setter Property="Margin" Value="5" />
            <Setter Property="Height" Value="20" />
            <Setter Property="Width" Value="20" />
        </Style>
        <Style x:Key="TouchPenStyle" TargetType="Rectangle">
            <Setter Property="Margin" Value="15" />
            <Setter Property="Height" Value="40" />
            <Setter Property="Width" Value="40" />
        </Style>
    </Page.Resources>

    <RelativePanel>
        <!-- ... -->
        <Button Content="Color Palette Button" x:Name="MenuButton">
            <Button.Flyout>
                <Flyout Placement="Bottom">
                    <RelativePanel>
                        <Rectangle Name="BlueRect" Fill="Blue"/>
                        <Rectangle Name="GreenRect" Fill="Green" RelativePanel.RightOf="BlueRect" />
                        <!-- ... -->
                    </RelativePanel>
                </Flyout>
            </Button.Flyout>
        </Button>
        <!-- ... -->
    </RelativePanel>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="InputTypeStates">
            <!-- Second set of VisualStates for building responsive UI optimized for input type.
                 Take a look at InputTypeTrigger.cs class in CustomTriggers folder to see how this is implemented. -->
            <VisualState>
                <VisualState.StateTriggers>
                    <!-- This trigger indicates that this VisualState is to be applied when MenuButton is invoked using a mouse. -->
                    <triggers:InputTypeTrigger TargetElement="{x:Bind MenuButton}" PointerType="Mouse" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="BlueRect.Style" Value="{StaticResource MouseStyle}" />
                    <Setter Target="GreenRect.Style" Value="{StaticResource MouseStyle}" />
                    <!-- ... -->
                </VisualState.Setters>
            </VisualState>
            <VisualState>
                <VisualState.StateTriggers>
                    <!-- Multiple trigger statements can be declared in the following way to imply OR usage.
                         For example, the following statements indicate that this VisualState is to be applied when MenuButton is invoked using Touch OR Pen.-->
                    <triggers:InputTypeTrigger TargetElement="{x:Bind MenuButton}" PointerType="Touch" />
                    <triggers:InputTypeTrigger TargetElement="{x:Bind MenuButton}" PointerType="Pen" />
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="BlueRect.Style" Value="{StaticResource TouchPenStyle}" />
                    <Setter Target="GreenRect.Style" Value="{StaticResource TouchPenStyle}" />
                    <!-- ... -->
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Page>
```

## <a name="tailored-layouts"></a>Специально разработанные макеты

Если вы внесете значительные изменения в макет пользовательского интерфейса на разных устройствах, вы, возможно, убедитесь, что более удобный способ— это определить отдельный файл пользовательского интерфейса с помощью специально разработанного макета для устройства, а не адаптировать единый пользовательский интерфейс. Если функция работает аналогично на всех устройствах, вы можете определить отдельные представления XAML, которые используют один и тот же файл кода. Если представление и функции на устройствах значительно различаются, вы можете определить отдельные страницы (Pages) и выбрать, к какой из них переходить при загрузке приложения.

### <a name="separate-xaml-views-per-device-family"></a>Отдельные представления XAML на семейство устройств

Используйте представления XAML для создания различных определений пользовательского интерфейса, которые имеют тот же код. Вы можете предоставить собственное определение пользовательского интерфейса для каждого семейства устройств. Выполните следующие действия, чтобы добавить представление XAML в приложение.

**Добавление представления XAML в приложение**
1. Выберите элемент "Проект" > "Добавить новый элемент". Откроется диалоговое окно "Добавление нового элемента".
    > **Совет.**&nbsp;&nbsp;Убедитесь, что в обозревателе решений выбран проект или папка, а не решение.
2. В разделе Visual C# или Visual Basic на левой панели выберите тип шаблона XAML.
3. На центральной панели выберите Представление XAML.
4. Введите имя для представления. Представление должно называться правильно. Подробнее об именовании см. в оставшейся части этого раздела.
5. Нажмите кнопку Добавить. Файл будет добавлен в проект.

Предыдущие действия создают только файл XAML, но не связанный с ним файл кода программной части. Вместо этого представление XAML связывается с существующим файлом кода программной части с помощью квалификатора DeviceName, который является частью имени файла или папки. Это имя квалификатора можно сопоставить со строковым значением, которое представляет семейство устройств для устройства, на котором в настоящее время запущено приложение, например «Настольное», «Планшет» или другое (см. раздел [**ResourceContext.QualifierValues**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.applicationmodel.resources.core.resourcecontext.qualifiervalues.aspx)).

Квалификатор можно добавить в имя файла, либо можно добавить файл в папку, которая содержит имя квалификатора.

**Использование имени файла**

Чтобы использовать имя квалификатора с файлом, применяется следующий формат: *[pageName]*.DeviceFamily-*[qualifierString]*.xaml.

Рассмотрим пример с именем файла MainPage.xaml. Чтобы создать представление для планшетных устройств, задайте для представления XAML имя MainPage.DeviceFamily-Tablet.xaml. Чтобы создать представление для устройств компьютера, задайте для представления имя MainPage.DeviceFamily-Desktop.xaml. Ниже представлен вид решения в Microsoft Visual Studio.

![Представления XAML с соответствующими именами файлов](images/xaml-layout-view-ex-1.png)

**Использование имени папки**

Для организации представлений в проекте Visual Studio с использованием папок можно использовать имя квалификатора с папкой. Для этого назовите папку приблизительно так: DeviceFamily-*[qualifierString]*. В этом случае каждый файл представления XAML имеет такое имя. Не включайте квалификатор в имя файла.

Вот еще один пример с именем файла MainPage.xaml. Чтобы создать представление для планшетных устройств, создайте папку с именем DeviceFamily-Tablet и поместите в нее представление XAML с именем MainPage.xaml. Чтобы создать представление для ПК, создайте папку с именем DeviceFamily-Desktop и поместите в нее другое представление XAML с именем MainPage.xaml. Ниже представлен вид решения в Visual Studio.

![Представления XAML в папках](images/xaml-layout-view-ex-2.png)

В обоих случаях используется уникальное представление для планшетов и ПК. Файл MainPage.xaml используется по умолчанию, если устройство, на котором он открыт, не совпадает ни с одним представлением для семейства устройств.

### <a name="separate-xaml-pages-per-device-family"></a>Отдельные страницы XAML в семействе устройств

Для предоставления уникальных представлений и функциональности можно создать отдельные файлы Page (XAML и код), а затем перейти на соответствующую страницу при необходимости.

**Добавление страницы XAML в приложение**
1. Выберите элемент "Проект" > "Добавить новый элемент". Откроется диалоговое окно "Добавление нового элемента".
    > **Совет.**&nbsp;&nbsp;Убедитесь, что в обозревателе решений выбран проект, а не решение.
2. В разделе Visual C# или Visual Basic на левой панели выберите тип шаблона XAML.
3. На центральной панели выберите элемент «Пустая страница».
4. Введите имя страницы. Например: MainPage_Tablet. Будут созданы файл MainPage_Tablet.xaml и файл кода MainPage_Tablet.xaml.cs/vb/cpp.
5. Нажмите кнопку "Добавить". Файл будет добавлен в проект.

Во время выполнения проверьте семейство устройств, на котором запущено приложение, и перейдите на нужную страницу, например, как эта.

```csharp
if (Windows.System.Profile.AnalyticsInfo.VersionInfo.DeviceFamily == "Windows.Tablet")
{
    rootFrame.Navigate(typeof(MainPage_Tablet), e.Arguments);
}
else
{
    rootFrame.Navigate(typeof(MainPage), e.Arguments);
}
```

Вы также можете использовать другие критерии, чтобы определить, на какую страницу перейти. Дополнительные примеры см. в примере [Несколько специально разработанных представлений](http://go.microsoft.com/fwlink/p/?LinkId=620636), в котором используется функция [**GetIntegratedDisplaySize**](https://msdn.microsoft.com/library/windows/apps/xaml/dn904185.aspx) для проверки физического размера встроенного дисплея.

## <a name="related-topics"></a>Статьи по теме
- [Учебник: создание адаптивных макетов](../basics/xaml-basics-adaptive-layout.md)
- [Пример адаптивных методик (GitHub)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlResponsiveTechniques)
- [Пример триггеров состояния (GitHub)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlStateTriggers)
- [Несколько специально разработанных представлений (GitHub)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlTailoredMultipleViews)
