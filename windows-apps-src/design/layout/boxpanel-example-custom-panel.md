---
Description: Информация о написании кода для пользовательского класса Panel, реализации методов ArrangeOverride и MeasureOverride, а также использовании свойства Children.
MS-HAID: dev\_ctrl\_layout\_txt.boxpanel\_example\_custom\_panel
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: BoxPanel, пример настраиваемой панели (приложения для Windows)
ms.assetid: 981999DB-81B1-4B9C-A786-3025B62B74D6
label: BoxPanel, an example custom panel
template: detail.hbs
op-migration-status: ready
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 68ca40a48b8b8d04bcd8b01584856233e9a99e7c
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970209"
---
# <a name="boxpanel-an-example-custom-panel"></a>BoxPanel, пример настраиваемой панели

 

Информация о написании кода для пользовательского класса [**Panel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Panel) путем реализации методов [**ArrangeOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.arrangeoverride) и [**MeasureOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.measureoverride), а также использования свойства [**Children**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.panel.children). 

> **Важные API**: [**Panel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Panel), [**ArrangeOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.arrangeoverride), [**MeasureOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.measureoverride). 

В данном примере кода показана реализация пользовательской панели, но не рассматриваются подробно понятия макета, которые влияют на настройку панели для различных сценариев макета. Дополнительную информацию об этих понятиях макета и их применимости к вашему конкретному сценарию макета см. в статье [Обзор пользовательских панелей XAML](custom-panels-overview.md).

*Панель* — это объект, который определяет поведение макета для содержащихся в нем дочерних элементов при работе системы макетов XAML и отрисовке пользовательского интерфейса вашего приложения. Вы можете определить настраиваемые панели для макета XAML, создав пользовательский класс, производный от класса [**Panel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Panel). Чтобы задать поведение для вашей панели, следует переопределить методы [**MeasureOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.arrangeoverride) и [**ArrangeOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.measureoverride), обеспечивающие логику измерения и упорядочения дочерних элементов. Этот пример является производным от **Panel**. Если начать с **Panel**, методы **ArrangeOverride** и **MeasureOverride** не имеют начального поведения. Ваш код предоставляет шлюз, через который дочерние элементы узнаются системой макетов XAML и обрабатываются в пользовательском интерфейсе. Таким образом, очень важно, чтобы ваш код охватывал все дочерние элементы и следовал шаблонам, которых ожидает система.

## <a name="your-layout-scenario"></a>Ваш сценарий макета

При определении пользовательской панели вы определяете сценарий макета.

Критерии определения сценария макета:

-   поведение панели с дочерними элементами;
-   ограничения панели на собственное пространство;
-   определение всех измерений, размещения, позиций и размеров в логике панели, которые в итоге приводят к обработке макета пользовательского интерфейса дочерних элементов.

С учетом этого показанная здесь панель `BoxPanel` предназначена для определенного сценария. Чтобы код в этом примере оставался на первом плане, мы пока не будем объяснять сценарий подробно и вместо этого сосредоточимся на требуемых шагах и шаблонах кодирования. Если вы хотите сначала получить дополнительную информацию о сценарии, перейдите в раздел [Сценарий для `BoxPanel`](#the-scenario-for-boxpanel), а затем вернитесь к коду.

## <a name="start-by-deriving-from-panel"></a>Начните с вывода из **Panel**

Начните с вывода пользовательского класса из [**Panel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Panel). Возможно, самый легкий способ — определить отдельный файл кода для этого класса с помощью пунктов контекстного меню **Добавить** | **Новый элемент** | **Класс** для проекта из **обозревателя решений** в Microsoft Visual Studio. Задайте имя класса (и файла) `BoxPanel`.

Файл шаблона для класса не начинается с большого количества операторов **using**, так как он не предназначен конкретно для приложений для Windows. Поэтому сначала добавьте операторы **using**. Файл шаблона также начинается с нескольких операторов **using**, которые вам, вероятно, не нужны и которые можно удалить. Далее предлагается список операторов **using**, способных обрабатывать типы, которые понадобятся вам для написания кода стандартной пользовательской панели.

```CSharp
using System;
using System.Collections.Generic; // if you need to cast IEnumerable for iteration, or define your own collection properties
using Windows.Foundation; // Point, Size, and Rect
using Windows.UI.Xaml; // DependencyObject, UIElement, and FrameworkElement
using Windows.UI.Xaml.Controls; // Panel
using Windows.UI.Xaml.Media; // if you need Brushes or other utilities
```

Теперь, когда вы можете обработать класс [**Panel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Panel), сделайте его основой класса `BoxPanel`. Также сделайте класс `BoxPanel` открытым:

```CSharp
public class BoxPanel : Panel
{
}
```

На уровне класса определите несколько значений **int** и **double**, которые будут общими для нескольких логических функций, но которые не нужно будет предоставлять в качестве общедоступных API. В примере эти значения называются `maxrc`, `rowcount`, `colcount`, `cellwidth`, `cellheight`, `maxcellheight` и `aspectratio`.

После этого полный файл кода выглядит следующим образом (комментарии к операторам **using** удалены, так как вы теперь знаете, зачем они):

```CSharp
using System;
using System.Collections.Generic;
using Windows.Foundation;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

public class BoxPanel : Panel 
{
    int maxrc, rowcount, colcount;
    double cellwidth, cellheight, maxcellheight, aspectratio;
}
```

В дальнейшем мы будем показывать по одному определению члена, будь то переопределение метода или поддерживающий элемент, например свойство зависимости. Их можно добавлять в вышеприведенную схему в любом порядке, и мы не будем снова показывать операторы **using** или определение области класса во фрагментах, пока не дойдем до окончательного кода.

## <a name="measureoverride"></a>**MeasureOverride**


```CSharp
protected override Size MeasureOverride(Size availableSize)
{
    Size returnSize;
    // Determine the square that can contain this number of items.
    maxrc = (int)Math.Ceiling(Math.Sqrt(Children.Count));
    // Get an aspect ratio from availableSize, decides whether to trim row or column.
    aspectratio = availableSize.Width / availableSize.Height;

    // Now trim this square down to a rect, many times an entire row or column can be omitted.
    if (aspectratio > 1)
    {
        rowcount = maxrc;
        colcount = (maxrc > 2 && Children.Count < maxrc * (maxrc - 1)) ? maxrc - 1 : maxrc;
    } 
    else 
    {
        rowcount = (maxrc > 2 && Children.Count < maxrc * (maxrc - 1)) ? maxrc - 1 : maxrc;
        colcount = maxrc;
    }

    // Now that we have a column count, divide available horizontal, that's our cell width.
    cellwidth = (int)Math.Floor(availableSize.Width / colcount);
    // Next get a cell height, same logic of dividing available vertical by rowcount.
    cellheight = Double.IsInfinity(availableSize.Height) ? Double.PositiveInfinity : availableSize.Height / rowcount;
           
    foreach (UIElement child in Children)
    {
        child.Measure(new Size(cellwidth, cellheight));
        maxcellheight = (child.DesiredSize.Height > maxcellheight) ? child.DesiredSize.Height : maxcellheight;
    }
    return LimitUnboundedSize(availableSize);
}
```

Необходимый шаблон реализации метода [**MeasureOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.measureoverride) представляет собой цикл с каждым элементом в свойстве [**Panel.Children**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.panel.children). Всегда вызывайте метод [**Measure**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) в каждом из этих элементов. Для метода **Measure** предусмотрен параметр типа [**Size**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Size). В нем передается размер, который ваша панель выделяет для этого конкретного дочернего элемента. Таким образом, перед выполнением цикла и вызовом метода **Measure** необходимо знать, сколько места может выделить каждая ячейка. В самом методе **MeasureOverride** предоставлено значение *availableSize*. Это размер, который родительский объект использовал при вызове метода **Measure**, что и привело изначально к вызову этого метода **MeasureOverride**. Таким образом, согласно обычной логике необходимо придумать схему, в которой каждый дочерний элемент разделяет место, равное общему доступному размеру панели *availableSize*. Затем каждое разделение размера передается методу **Measure** каждого дочернего элемента.

Панель `BoxPanel` разбивает размер довольно просто: место делится на число прямоугольников, в основном зависящее от числа объектов. Размер прямоугольников зависит от числа строк и столбцов, а также от доступного размера. Иногда строка или столбец в квадрате не нужны; тогда они удаляются, и панель становится прямоугольной, а не квадратной с точки зрения соотношения строк и столбцов. Дополнительную информацию о выводе этой логики см. ниже в разделе ["Сценарий для BoxPanel"](#the-scenario-for-boxpanel).

Что же делает проход Measure? Он устанавливает значение свойства только для чтения [**DesiredSize**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.desiredsize) в каждом элементе, в котором был вызван метод [**Measure**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure). Значение **DesiredSize** может быть важным, когда вы дойдете до прохода Arrange, так как **DesiredSize** передает размер, который может или должен быть при упорядочении и в окончательной отрисовке. Даже если вы не используете свойство **DesiredSize** в своей логике, оно необходимо системе.

Эта панель может использоваться, когда компонент Height значения *availableSize* не связан. Тогда высота панели, которую необходимо разделить, неизвестна. В этом случае логика для прохода Measure информирует каждый дочерний элемент о том, что связанная высота еще отсутствует. Для этого структура [**Size**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Size) передается вызову [**Measure**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) для дочерних элементов, в которых свойство [**Size.Height**](https://docs.microsoft.com/uwp/api/windows.foundation.size.height) бесконечно. Это правомерно. Когда вызывается метод **Measure**, согласно логике для свойства [**DesiredSize**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.desiredsize) устанавливается наименьшее из следующих значений: значение, переданное методу **Measure**, либо естественный размер элемента, который определяется такими факторами, как явным образом заданные свойства [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width).

> [!NOTE]
> Внутренняя логика [**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) ведет себя точно так же: **StackPanel** передает бесконечное значение измерения методу [**Measure**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) для дочерних элементов, указывая, что у дочерних элементов нет ограничений в направлении ориентации. Класс **StackPanel** обычно принимает размер динамически, чтобы разместить все дочерние элементы в стеке, который растет в этом измерении.

Однако сама панель не может вернуть структуру [**Size**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Size) с бесконечным значением из метода [**MeasureOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.measureoverride); оно создает исключение во время построения структуры. Поэтому часть логики заключается в том, чтобы выяснить максимальную высоту, которую запрашивает какой-либо дочерний элемент, и использовать эту высоту в качестве высоты ячейки, если она еще не ясна из ограничений на собственный размер панели. Ниже показана вспомогательная функция `LimitUnboundedSize`, на которую ссылался ранее приведенный код и которая затем получает максимальную высоту ячейки и использует ее, чтобы предоставить панели конечную высоту для возврата, а также проверяет, что `cellheight` является конечным значением, до инициации прохода Arrange:

```CSharp
// This method is called only if one of the availableSize dimensions of measure is infinite.
// That can happen to height if the panel is close to the root of main app window.
// In this case, base the height of a cell on the max height from desired size
// and base the height of the panel on that number times the #rows.

Size LimitUnboundedSize(Size input)
{
    if (Double.IsInfinity(input.Height))
    {
        input.Height = maxcellheight * colcount;
        cellheight = maxcellheight;
    }
    return input;
}
```

## <a name="arrangeoverride"></a>**ArrangeOverride**

```CSharp
protected override Size ArrangeOverride(Size finalSize)
{
     int count = 1
     double x, y;
     foreach (UIElement child in Children)
     {
          x = (count - 1) % colcount * cellwidth;
          y = ((int)(count - 1) / colcount) * cellheight;
          Point anchorPoint = new Point(x, y);
          child.Arrange(new Rect(anchorPoint, child.DesiredSize));
          count++;
     }
     return finalSize;
}
```

Необходимый шаблон реализации метода [**ArrangeOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.arrangeoverride) представляет собой цикл с каждым элементом в свойстве [**Panel.Children**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.panel.children). Всегда вызывайте метод [**Arrange**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange) в каждом из этих элементов.

Обратите внимание, что расчетов меньше, чем в методе [**MeasureOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.measureoverride); это нормально. Размер дочерних элементов уже известен из собственной логики **MeasureOverride** панели или из значения [**DesiredSize**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.desiredsize) каждого дочернего элемента, заданного во время прохода Measure. Тем не менее все еще необходимо определить расположение внутри панели, в котором будет отображаться каждый дочерний элемент. На обычной панели каждый дочерний элемент должен преобразовываться на своей позиции. Панель, которая создает перекрывающиеся элементы, нежелательна для обычных сценариев (хотя не исключается создание панелей, которые содержат намеренные перекрытия, если вы действительно задумали такой сценарий).

Эта панель упорядочивается согласно понятию строк и столбцов. Количество строк и столбцов уже было рассчитано (это требовалось для измерения). И теперь форма строк и столбцов и известные размеры каждой ячейки учитываются в логике, определяющей положение отображения (`anchorPoint`) для каждого элемента, который содержится на этой панели. Это положение [**Point**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Point), а также структура [**Size**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Size), уже известная из прохода Measure, используются как два компонента, которые выстраивают структуру [**Rect**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Rect). Структура **Rect** является типом входных данных для метода [**Arrange**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange).

Иногда панелям необходимо сжать содержимое. В таком случае сжатый размер является размером, который представлен в свойстве [**DesiredSize**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.desiredsize), так как логика [**Measure**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.measure) задает его в качестве минимального размера, переданного методу **Measure**, или в качестве коэффициентов естественного размера. Обычно нет необходимости специально проверять сжатие во время прохода [**Arrange**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.arrange): сжатие будет выполняться просто на основе передачи свойства **DesiredSize** через каждый вызов **Arrange**.

Во время прохождения цикла не всегда требуется счетчик, если вся необходимая информация для определения положения отображения известна из других источников. Например, в логике макета [**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) позиция в коллекции [**Children**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.panel.children) не имеет значения. Вся информация, необходимая для определения позиции каждого элемента в **Canvas**, известна из значений [**Canvas.Left**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.left) и [**Canvas.Top**](https://docs.microsoft.com/dotnet/api/system.windows.controls.canvas.top) дочерних элементов в рамках логики размещения. Этой логике `BoxPanel` необходимо сравнить счетчик со счетчиком *colcount*, чтобы узнать, когда начать новую строку и сместить значение *y*.

Обычно входное значение *finalSize* и структура [**Size**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Size), которую вы возвращаете из реализации [**ArrangeOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.arrangeoverride), одинаковы. Дополнительную информацию о причинах см. в разделе "**ArrangeOverride**" статьи [Обзор пользовательских панелей XAML](custom-panels-overview.md).

## <a name="a-refinement-controlling-the-row-vs-column-count"></a>Уточнение: управление счетчиком строк и столбцов

Можно скомпилировать и использовать эту панель так, как сейчас. Однако мы добавим еще одно уточнение. В только что приведенном коде логика вставляет дополнительную строку или столбец с более длинной стороны в пропорции. Но для более точного управления формами ячеек рекомендуется выбрать набор ячеек 4×3 вместо 3×4, даже если собственная пропорция панели книжная. Поэтому мы добавим необязательное свойство зависимости, которое потребитель панели может установить, чтобы управлять этим поведением. Ниже приводится базовое определение свойства зависимости:

```CSharp
public static readonly DependencyProperty UseOppositeRCRatioProperty =
   DependencyProperty.Register("UseOppositeRCRatio", typeof(bool), typeof(BoxPanel), null);

public bool UseSquareCells
{
    get { return (bool)GetValue(UseOppositeRCRatioProperty); }
    set { SetValue(UseOppositeRCRatioProperty, value); }
}
```

А далее показано, как использование `UseOppositeRCRatio` воздействует на логику измерения. Фактически она просто изменяет то, как значения `rowcount` и `colcount` выводятся из значения `maxrc` и действительного значения пропорций, и поэтому все ячейки соответствующим образом различаются по размеру. Когда значением `UseOppositeRCRatio` является **true**, действительное значение пропорций обращается перед использованием для счетчиков строк и столбцов.

```CSharp
if (UseOppositeRCRatio) { aspectratio = 1 / aspectratio;}
```

## <a name="the-scenario-for-boxpanel"></a>Сценарий для BoxPanel

Конкретный сценарий для `BoxPanel` заключается в том, что это панель, для которой один из основных способов определить деление места — узнать число дочерних элементов и разделить известное свободное место для панели. Панели по своей сути являются прямоугольными фигурами. Многие панели работают, разделяя это прямоугольное пространство на прямоугольники меньшего размера; это делает класс [**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) для своих ячеек. В случае класса **Grid** размер ячеек устанавливается по значениям [**ColumnDefinition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ColumnDefinition) и [**RowDefinition**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RowDefinition), и элементы объявляют точную ячейку, в которой они расположены, с помощью присоединенных свойств [**Grid.Row**](https://docs.microsoft.com/dotnet/api/system.windows.controls.grid.row) и [**Grid.Column**](https://docs.microsoft.com/dotnet/api/system.windows.controls.grid.column). Для получения качественного макета от класса **Grid** обычно необходимо знать число дочерних элементов заранее, чтобы было достаточно ячеек и каждый дочерний элемент установил свои свойства в соответствии с размером своей ячейки.

Но что если число дочерних элементов динамическое? Это, конечно, возможно; код вашего приложения может добавлять элементы в коллекции в ответ на любое динамическое условие среды выполнения, которое вы считаете достаточно важным, чтобы обновить ваш пользовательский интерфейс. Если вы используете привязку данных к базовым коллекциям или бизнес-объектам, получение таких обновлений и обновление пользовательского интерфейса обрабатываются автоматически, поэтому этот прием часто является предпочтительным (см. раздел [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth)).

Но не все сценарии приложений приспособлены для привязки данных. Иногда требуется создавать новые элементы пользовательского интерфейса в среде выполнения и делать их видимыми. Панель `BoxPanel` предназначена для данного сценария. Изменяющееся число дочерних элементов не является проблемой для панели `BoxPanel`, потому что она использует счетчик дочерних элементов в расчетах и подстраивает как существующие, так и новые дочерние элементы под новый макет, чтобы все они поместились.

В более сложном сценарии расширения `BoxPanel` (здесь не показан) можно было бы разместить динамические дочерние элементы и использовать свойство [**DesiredSize**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.desiredsize) дочерних элементов как более надежный коэффициент определения размера отдельных ячеек. В этом сценарии могут использоваться различные размеры строк и столбцов или фигуры вне сетки, чтобы оставалось меньше неиспользованного пространства. Для этого требуется стратегия размещения нескольких прямоугольников различных размеров и пропорций в одном прямоугольнике-контейнере в целях эстетики и обеспечения меньшего размера. Панель `BoxPanel` не делает этого, она использует более простой прием для деления места. Прием панели `BoxPanel` состоит в определении наименьшего числа квадратов, которое больше числа дочерних элементов. Например, 9 элементов войдут в квадрат 3×3. Для 10 элементов потребуется квадрат 4×4. Однако часто можно разместить элементы, удаляя строку или столбец исходного квадрата, чтобы сэкономить место. В примере с count=10, помещающемся в этот прямоугольник 4x3 или 3x4.

Возможно, у вас возникнет вопрос, почему панель не выбрала для 10 элементов прямоугольник 5×2, в который аккуратно вошло бы это число элементов. Однако на практике размер панелей настраивается как прямоугольник, который часто не имеет жестко ориентированной пропорции. Прием наименьшего квадрата представляет собой способ настроить логику для работы с обычными фигурами макета; размеры, в которых формы ячеек имеют нестандартные пропорции, не рекомендуются.

## <a name="related-topics"></a>Связанные темы

**Reference**

* [**FrameworkElement.ArrangeOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.arrangeoverride)
* [**FrameworkElement.MeasureOverride**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.measureoverride)
* [**Panel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Panel)

**Концепции**

* [Выравнивание, поля и отбивка](alignment-margin-padding.md)
