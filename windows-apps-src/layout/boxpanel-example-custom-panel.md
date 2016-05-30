---
author: Jwmsft
Description: 'Информация о написании кода для пользовательского класса Panel, реализации методов ArrangeOverride и MeasureOverride, а также использовании свойства Children.'
MS-HAID: 'dev\_ctrl\_layout\_txt.boxpanel\_example\_custom\_panel'
MSHAttr: 'PreferredLib:/library/windows/apps'
Search.Product: eADQiWindows 10XVcnh
title: 'BoxPanel, пример настраиваемой панели'
ms.assetid: 981999DB-81B1-4B9C-A786-3025B62B74D6
label: BoxPanel, an example custom panel
template: detail.hbs
---

# BoxPanel, пример настраиваемой панели

**Важные API**

-   [**Панель**](https://msdn.microsoft.com/library/windows/apps/br227511)
-   [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711)
-   [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730)

Информация о написании кода для пользовательского класса [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511) путем реализации методов [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711) и [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730), а также использования свойства [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514). В данном примере кода показана реализация пользовательской панели, но не рассматриваются подробно понятия, которые влияют на настройку панели для различных сценариев макета. Дополнительную информацию об этих понятиях макета и их применимости к вашему конкретному сценарию макета см. в статье [Обзор пользовательских панелей XAML](custom-panels-overview.md).

*Панель* — это объект, который определяет поведение макета для содержащихся в нем дочерних элементов при работе системы макетов XAML и отрисовке пользовательского интерфейса вашего приложения. Вы можете определить настраиваемые панели для макета XAML, создав пользовательский класс, производный от класса [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511). Чтобы задать поведение для вашей панели, следует переопределить методы [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208711) и [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208730), обеспечивающие логику измерения и упорядочения дочерних элементов. Этот пример является производным от **Panel**. Если начать с **Panel**, методы **ArrangeOverride** и **MeasureOverride** не имеют начального поведения. Ваш код предоставляет шлюз, через который дочерние элементы узнаются системой макетов XAML и обрабатываются в пользовательском интерфейсе. Таким образом, очень важно, чтобы ваш код охватывал все дочерние элементы и следовал шаблонам, которых ожидает система.

## Ваш сценарий макета

При определении пользовательской панели вы определяете сценарий макета.

Критерии определения сценария макета:

-   поведение панели с дочерними элементами;
-   ограничения панели на собственное пространство;
-   определение всех измерений, размещения, позиций и размеров в логике панели, которые в итоге приводят к обработке макета пользовательского интерфейса дочерних элементов.

С учетом этого показанная здесь панель `BoxPanel` предназначена для определенного сценария. Чтобы код в этом примере оставался на первом плане, мы пока не будем объяснять сценарий подробно и вместо этого сосредоточимся на требуемых шагах и шаблонах кодирования. Если вы хотите сначала получить дополнительную информацию о сценарии, перейдите в раздел [Сценарий для `BoxPanel`](#scenario), а затем вернитесь к коду.

## Начните с вывода из **Panel**

Начните с вывода пользовательского класса из [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511). Возможно, самый легкий способ — определить отдельный файл кода для этого класса с помощью пунктов контекстного меню **Добавить** | **Новый элемент** | **Класс** для проекта из **обозревателя решений** в Microsoft Visual Studio. Задайте имя класса (и файла) `BoxPanel`.

Файл шаблона для класса не начинается с большого количества операторов **using**, так как он не предназначен конкретно для приложений универсальной платформы Windows (UWP). Поэтому сначала добавьте операторы **using**. Файл шаблона также начинается с нескольких операторов **using**, которые вам, вероятно, не нужны и которые можно удалить. Далее предлагается список операторов **using**, способных обрабатывать типы, которые понадобятся вам для написания кода стандартной пользовательской панели.

```CSharp
using System;
using System.Collections.Generic; // if you need to cast IEnumerable for iteration, or define your own collection properties
using Windows.Foundation; // Point, Size, and Rect
using Windows.UI.Xaml; // DependencyObject, UIElement, and FrameworkElement
using Windows.UI.Xaml.Controls; // Panel
using Windows.UI.Xaml.Media; // if you need Brushes or other utilities
```

Теперь, когда вы можете обработать класс [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511), сделайте его основой класса `BoxPanel`. Также сделайте класс `BoxPanel` открытым:

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

## **MeasureOverride**


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
        colcount = (maxrc > 2 &amp;&amp; Children.Count < maxrc * (maxrc - 1)) ? maxrc - 1 : maxrc;
    } 
    else 
    {
        rowcount = (maxrc > 2 &amp;&amp; Children.Count < maxrc * (maxrc - 1)) ? maxrc - 1 : maxrc;
        colcount = maxrc;
    }

    // Now that we have a column count, divide available horizontal, that&#39;s our cell width.
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

Необходимый шаблон реализации метода [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) представляет собой цикл с каждым элементом в свойстве [**Panel.Children**](https://msdn.microsoft.com/library/windows/apps/br227514). Всегда вызывайте метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) в каждом из этих элементов. Для метода **Measure** предусмотрен параметр типа [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995). В нем передается размер, который ваша панель выделяет для этого конкретного дочернего элемента. Таким образом, перед выполнением цикла и вызовом метода **Measure** необходимо знать, сколько места может выделить каждая ячейка. В самом методе **MeasureOverride** предоставлено значение *availableSize*. Это размер, который родительский объект использовал при вызове метода **Measure**, что и привело изначально к вызову этого метода **MeasureOverride**. Таким образом, согласно обычной логике необходимо придумать схему, в которой каждый дочерний элемент разделяет место, равное общему доступному размеру панели *availableSize*. Затем каждое разделение размера передается методу **Measure** каждого дочернего элемента.

Панель `BoxPanel` разбивает размер довольно просто: место делится на число прямоугольников, в основном зависящее от числа объектов. Размер прямоугольников зависит от числа строк и столбцов, а также от доступного размера. Иногда строка или столбец в квадрате не нужны; тогда они удаляются, и панель становится прямоугольной, а не квадратной с точки зрения соотношения строк и столбцов. Дополнительную информацию о выводе этой логики см. ниже в разделе ["Сценарий для BoxPanel"](#scenario).

Что же делает проход Measure? Он устанавливает значение свойства только для чтения [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921) в каждом элементе, в котором был вызван метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952). Значение **DesiredSize** может быть важным, когда вы дойдете до прохода Arrange, так как **DesiredSize** передает размер, который может или должен быть при упорядочении и в окончательной отрисовке. Даже если вы не используете свойство **DesiredSize** в своей логике, оно необходимо системе.

Эта панель может использоваться, когда компонент Height значения *availableSize* не связан. Тогда высота панели, которую необходимо разделить, неизвестна. В этом случае логика для прохода Measure информирует каждый дочерний элемент о том, что связанная высота еще отсутствует. Для этого структура [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995) передается вызову [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) для дочерних элементов, в которых свойство [**Size.Height**](https://msdn.microsoft.com/library/windows/apps/hh763910) бесконечно. Это правомерно. Когда вызывается метод **Measure**, согласно логике для свойства [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921) устанавливается наименьшее из следующих значений: значение, переданное методу **Measure**, либо естественный размер элемента, который определяется такими факторами, как явным образом заданные свойства [**Height**](https://msdn.microsoft.com/library/windows/apps/br208718) и [**Width**](https://msdn.microsoft.com/library/windows/apps/br208751).

**Примечание**
            &nbsp;&nbsp;У внутренней логики класса [**StackPanel**](https://msdn.microsoft.com/library/windows/apps/br209635) то же поведение: **StackPanel** передает бесконечное значение измерения методу [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) для дочерних элементов, указывая, что у дочерних элементов нет ограничений в измерении ориентации. Класс **StackPanel** обычно принимает размер динамически, чтобы разместить все дочерние элементы в стеке, который растет в этом измерении.

Однако сама панель не может вернуть структуру [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995) с бесконечным значением из метода [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730); оно создает исключение во время построения структуры. Поэтому часть логики заключается в том, чтобы выяснить максимальную высоту, которую запрашивает какой-либо дочерний элемент, и использовать эту высоту в качестве высоты ячейки, если она еще не ясна из ограничений на собственный размер панели. Ниже показана вспомогательная функция `LimitUnboundedSize`, на которую ссылался ранее приведенный код и которая затем получает максимальную высоту ячейки и использует ее, чтобы предоставить панели конечную высоту для возврата, а также проверяет, что `cellheight` является конечным значением, до инициации прохода Arrange:

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

## **ArrangeOverride**

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

Необходимый шаблон реализации метода [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711) представляет собой цикл с каждым элементом в свойстве [**Panel.Children**](https://msdn.microsoft.com/library/windows/apps/br227514). Всегда вызывайте метод [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914) в каждом из этих элементов.

Обратите внимание, что расчетов меньше, чем в методе [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730); это нормально. Размер дочерних элементов уже известен из собственной логики **MeasureOverride** панели или из значения [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921) каждого дочернего элемента, заданного во время прохода Measure. Тем не менее все еще необходимо определить расположение внутри панели, в котором будет отображаться каждый дочерний элемент. На обычной панели каждый дочерний элемент должен преобразовываться на своей позиции. Панель, которая создает перекрывающиеся элементы, нежелательна для обычных сценариев (хотя не исключается создание панелей, которые содержат намеренные перекрытия, если вы действительно задумали такой сценарий).

Эта панель упорядочивается согласно понятию строк и столбцов. Количество строк и столбцов уже было рассчитано (это требовалось для измерения). И теперь форма строк и столбцов и известные размеры каждой ячейки учитываются в логике, определяющей положение отображения (`anchorPoint`) для каждого элемента, который содержится на этой панели. Это положение [**Point**](https://msdn.microsoft.com/library/windows/apps/br225870), а также структура [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995), уже известная из прохода Measure, используются как два компонента, которые выстраивают структуру [**Rect**](https://msdn.microsoft.com/library/windows/apps/br225994). Структура **Rect** является типом входных данных для метода [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914).

Иногда панелям необходимо сжать содержимое. В таком случае сжатый размер является размером, который представлен в свойстве [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921), так как логика [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) задает его в качестве минимального размера, переданного методу **Measure**, или в качестве коэффициентов естественного размера. Обычно нет необходимости специально проверять сжатие во время прохода [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914): сжатие будет выполняться просто на основе передачи свойства **DesiredSize** через каждый вызов **Arrange**.

Во время прохождения цикла не всегда требуется счетчик, если вся необходимая информация для определения положения отображения известна из других источников. Например, в логике макета [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267) позиция в коллекции [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514) не имеет значения. Вся информация, необходимая для определения позиции каждого элемента в **Canvas**, известна из значений [**Canvas.Left**](https://msdn.microsoft.com/library/windows/apps/hh759771) и [**Canvas.Top**](https://msdn.microsoft.com/library/windows/apps/hh759772) дочерних элементов в рамках логики размещения. Этой логике `BoxPanel` необходимо сравнить счетчик со счетчиком *colcount*, чтобы узнать, когда начать новую строку и сместить значение *y*.

Обычно входное значение *finalSize* и структура [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995), которую вы возвращаете из реализации [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711), одинаковы. Дополнительную информацию о причинах см. в разделе «**ArrangeOverride**» статьи [Обзор пользовательских панелей XAML](custom-panels-overview.md).

## Уточнение: управление счетчиком строк и столбцов

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

## Сценарий для BoxPanel

Конкретный сценарий для `BoxPanel` заключается в том, что это панель, для которой один из основных способов определить деление места — узнать число дочерних элементов и разделить известное свободное место для панели. Панели по своей сути являются прямоугольными фигурами. Многие панели работают, разделяя это прямоугольное пространство на прямоугольники меньшего размера; это делает класс [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704) для своих ячеек. В случае класса **Grid** размер ячеек устанавливается по значениям [**ColumnDefinition**](https://msdn.microsoft.com/library/windows/apps/br209324) и [**RowDefinition**](https://msdn.microsoft.com/library/windows/apps/br227606), и элементы объявляют точную ячейку, в которой они расположены, с помощью присоединенных свойств [**Grid.Row**](https://msdn.microsoft.com/library/windows/apps/hh759795) и [**Grid.Column**](https://msdn.microsoft.com/library/windows/apps/hh759774). Для получения качественного макета от класса **Grid** обычно необходимо знать число дочерних элементов заранее, чтобы было достаточно ячеек и каждый дочерний элемент установил свои свойства в соответствии с размером своей ячейки.

Но что если число дочерних элементов динамическое? Это, конечно, возможно; код вашего приложения может добавлять элементы в коллекции в ответ на любое динамическое условие среды выполнения, которое вы считаете достаточно важным, чтобы обновить ваш пользовательский интерфейс. Если вы используете привязку данных к базовым коллекциям или бизнес-объектам, получение таких обновлений и обновление пользовательского интерфейса обрабатываются автоматически, поэтому этот прием часто является предпочтительным (см. раздел [Подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946)).

Но не все сценарии приложений приспособлены для привязки данных. Иногда требуется создавать новые элементы пользовательского интерфейса в среде выполнения и делать их видимыми. `BoxPanel` Панель  предназначена для данного сценария. Изменяющееся число дочерних элементов не является проблемой для панели `BoxPanel`, потому что она использует счетчик дочерних элементов в расчетах и подстраивает как существующие, так и новые дочерние элементы под новый макет, чтобы все они поместились.

В более сложном сценарии расширения `BoxPanel` (здесь не показан) можно было бы разместить динамические дочерние элементы и использовать свойство [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921) дочерних элементов как более надежный коэффициент определения размера отдельных ячеек. В этом сценарии могут использоваться различные размеры строк и столбцов или фигуры вне сетки, чтобы оставалось меньше неиспользованного пространства. Для этого требуется стратегия размещения нескольких прямоугольников различных размеров и пропорций в одном прямоугольнике-контейнере в целях эстетики и обеспечения меньшего размера. `BoxPanel` Панель  не делает этого, она использует более простой прием для деления места. `BoxPanel`прием состоит в определении наименьшего числа квадратов, которое больше числа дочерних элементов. Например, 9 элементов войдут в квадрат 3×3. Для 10 элементов потребуется квадрат 4×4. Однако часто можно разместить элементы, удаляя строку или столбец исходного квадрата, чтобы сэкономить место. В примере с count=10, помещающемся в этот прямоугольник 4x3 или 3x4.

Возможно, у вас возникнет вопрос, почему панель не выбрала для 10 элементов прямоугольник 5×2, в который аккуратно вошло бы это число элементов. Однако на практике размер панелей настраивается как прямоугольник, который часто не имеет жестко ориентированной пропорции. Прием наименьшего квадрата представляет собой способ настроить логику для работы с обычными фигурами макета; размеры, в которых формы ячеек имеют нестандартные пропорции, не рекомендуются.

**Примечание**
            &nbsp;&nbsp;Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

## Ссылки по теме

**Справочник**

[**FrameworkElement.ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711)

[**FrameworkElement.MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730)

[**Панель**](https://msdn.microsoft.com/library/windows/apps/br227511)

**Понятия**

[Выравнивание, поле и заполнение](alignment-margin-padding.md)


<!--HONumber=May16_HO2-->


