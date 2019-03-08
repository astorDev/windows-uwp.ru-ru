---
Description: Вы можете определить настраиваемые панели для макета XAML, создав пользовательский класс, производный от класса Panel.
MS-HAID: dev\_ctrl\_layout\_txt.xaml\_custom\_panels\_overview
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: Обзор настраиваемых панелей XAML
ms.assetid: 0CD395CD-E2AB-429D-BB49-56A71C5CC35D
label: XAML custom panels overview (Windows apps)
template: detail.hbs
op-migration-status: ready
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9999ebb121916a7804546784ea98ac4e0f4222e5
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57620349"
---
# <a name="xaml-custom-panels-overview"></a>Обзор настраиваемых панелей XAML

 

*Панель* — это объект, который определяет поведение макета для содержащихся в нем дочерних элементов при работе системы макетов XAML и отрисовке пользовательского интерфейса вашего приложения. 


> **Важные API-интерфейсы**: [**Панель**](https://msdn.microsoft.com/library/windows/apps/br227511), [ **ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711), [ **MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730)

Вы можете определить настраиваемые панели для макета XAML, создав пользовательский класс, производный от класса [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511). Поведение для вашей панели предоставляется путем переопределения методов [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) и [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711), обеспечивающих логику, которая измеряет и упорядочивает дочерние элементы.

## <a name="the-panel-base-class"></a>Базовый класс **Panel**


Чтобы определить класс настраиваемой панели, можно непосредственно создать класс, производный от [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511), или создать класс, производный от одного из реально используемых незапечатанных классов панелей, например [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704) или [**StackPanel**](https://msdn.microsoft.com/library/windows/apps/br209635). Наследовать от класса **Panel** проще, поскольку на панели, где уже есть функции макета, сложнее разобраться с существующей логикой. Кроме того, на готовой панели могут быть свойства, которые не имеют отношения к функциям макета вашей панели.

Настраиваемая панель наследует от класса [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511) следующие API:

-   Свойство [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514).
-   Свойства [**Background**](https://msdn.microsoft.com/library/windows/apps/br227512), [**ChildrenTransitions**](https://msdn.microsoft.com/library/windows/apps/br227515) и [**IsItemsHost**](https://msdn.microsoft.com/library/windows/apps/br227517), а также идентификаторы свойств зависимостей. Ни одно из этих свойств не является виртуальным, поэтому обычно их не нужно переопределять или заменять. Как правило, они не нужны для настраиваемых панелей, даже для считывания значений.
-   Методы переопределения макета [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) и [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711). Изначально они определены в [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706). В базовом классе [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511) они не переопределяются, но в реальных панелях, таких как [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704), переопределения реализованы в собственном коде и выполняются системой. Основная часть работы по определению настраиваемой панели состоит в создании новых (или дополнительных) реализаций методов **ArrangeOverride** и **MeasureOverride**.
-   Все прочие API классов [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706), [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911) и [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/br242356), в том числе [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height), [**Visibility**](https://msdn.microsoft.com/library/windows/apps/br208992) и пр. Иногда в переопределяемых методах макета упоминаются значения этих свойств, однако они не являются виртуальными и поэтому обычно не переопределяются и не заменяются.

Здесь мы рассмотрим основные понятия макета XAML, чтобы вы смогли учесть все возможности работы настраиваемой панели с макетом. Если вы хотите сразу перейти к примеру реализации настраиваемой панели, см. статью [Краткое руководство: BoxPanel, пример реализации настраиваемой панели](boxpanel-example-custom-panel.md).

## <a name="the-children-property"></a>Свойство **Children**


Свойство [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514) важно для настраиваемой панели, поскольку все классы, производные от [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511), используют свойство **Children** для хранения дочерних элементов в коллекции. Свойство **Children** служит свойством содержимого XAML для класса **Panel**, и все классы, производные от **Panel**, могут наследовать поведение свойства содержимого XAML. Если свойство является свойством содержимого XAML, то в разметке XAML можно не указывать элемент этого свойства, а значениями задаются непосредственные потомки разметки (содержимое). Например, если создать класс с именем **CustomPanel**, производный от **Panel**, где не определяется нового поведения, то по-прежнему можно пользоваться такой разметкой:

```XAML
<local:CustomPanel>
  <Button Name="button1"/>
  <Button Name="button2"/>
</local:CustomPanel>
```

Когда модуль синтаксического анализа XAML считывает эту разметку, то известно, что свойство [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514) является свойством содержимого XAML для всех типов, производных от [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511), поэтому модуль синтаксического анализа добавляет два элемента [**Button**](https://msdn.microsoft.com/library/windows/apps/br209265) к значению [**UIElementCollection**](https://msdn.microsoft.com/library/windows/apps/br227633) свойства **Children**. Свойство содержимого XAML облегчает создание упрощенных связей между родительскими и дочерними элементами в разметке XAML, используемой для определения пользовательского интерфейса. Дополнительные сведения о свойствах содержимого XAML и о заполнении свойств коллекции при анализе XAML см. в [руководстве по синтаксису XAML](https://msdn.microsoft.com/library/windows/apps/mt185596).

Типом коллекции, в которой хранится значение свойства [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514), является класс [**UIElementCollection**](https://msdn.microsoft.com/library/windows/apps/br227633). Коллекция **UIElementCollection** строго типизирована, и для всех элементов принудительно задается тип [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911). Базовый тип **UIElement** наследуется сотнями реально используемых типов элементов пользовательского интерфейса, поэтому здесь намеренно предусмотрено не слишком строгое соблюдение типов. Однако оно запрещает, например, элементу [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush) быть непосредственным потомком класса [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511) и обычно предполагает, что дочерними для **Panel** будут считаться только элементы, которые должны быть видимыми в пользовательском интерфейсе и включаться в макет.

Обычно настраиваемая панель принимает любой дочерний элемент [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911) по определению XAML и использует характеристики свойства [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514) как есть. В более сложных сценариях вы можете реализовать дополнительную проверку типов дочерних элементов во время прохода по коллекции в переопределяемых методах макета.

Помимо перебора коллекции [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514) в переопределенных методах, на логику панели может повлиять и значение `Children.Count`. Возможно, ваша логика выделяет пространство, в какой-то мере учитывая количество элементов, а не требуемые размеры и другие характеристики отдельных элементов.

## <a name="overriding-the-layout-methods"></a>Переопределение методов макета


Базовая модель переопределенных методов макета ([**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) и [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711)) предусматривает проход по всем дочерним элементам и вызов метода макета, соответствующего каждому дочернему элементу. Первый цикл макета начинается, когда система макета XAML задает визуальный элемент для корневого окна. Поскольку каждый родительский элемент вызывает макет в дочерних элементах, вызов распространяется на методы макета в каждом возможном элементе пользовательского интерфейса, который должен входить в макет. Работа макета XAML выполняется в два этапа: измерение и упорядочение.

В базовом классе [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511) нет встроенных моделей поведения для методов макета [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) и [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711). Элементы в свойстве [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514) не будут автоматически отрисовываться в составе визуального дерева XAML. Элементы необходимо сделать известными процессу макета. Для этого вызовите методы макета в каждом элементе свойства **Children** с помощью прохождения этапа разметки в реализациях **MeasureOverride** и **ArrangeOverride**.

Нет оснований вызывать базовые реализации в переопределенных элементах макета, если только у вас нет собственного наследования. Собственные методы поведения макета будут вызываться в любом случае (если они существуют), и собственное поведение будет выполняться даже без вызова базовой реализации из переопределенных методов.

На этапе измерения логика макета запрашивает у каждого дочернего элемента требуемый размер, вызывая в этом элементе метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952). Посредством вызова метода **Measure** определяется значение свойства [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921). Возвращаемое значение [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) представляет собой требуемый размер самой панели.

На этапе упорядочения положения и размеры дочерних элементов определяются в пространстве x-y, и составленный макет готовится к отрисовке. Метод [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914) должен вызываться в коде для каждого дочернего элемента в [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514), чтобы система макета определила, что данный элемент принадлежит макету. Вызов **Arrange** выполняется перед компоновкой и отрисовкой. Он сообщает системе макета о положении элемента, когда скомпонованный макет отправляется на отрисовку.

На работу логики макета во время выполнения влияет множество свойств и значений. Чтобы разобраться в обработке макета, отметим, что элементы, не имеющие дочерних (обычно это элементы пользовательского интерфейса с максимальным уровнем вложенности), могут первыми завершать измерения. У них нет зависимостей от дочерних элементов, которые влияют на их требуемый размер. Их собственный требуемый размер (если он задан) будет считаться рекомендуемым до момента фактического размещения элементов. Затем этап измерения продолжает движение вверх по визуальному дереву, пока не будут получены измерения корневого элемента, после чего измерения фиксируются.

Макет-кандидат должен умещаться в текущем окне приложения. В противном случае некоторые части пользовательского интерфейса будут отсечены. Логика отсечения часто определяется в панелях. Логика панели может определить доступный размер в реализации [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730). Ей также может понадобиться наложить ограничения на размер дочерних элементов и так распределить между ними пространство, чтобы наилучшим образом осуществить размещение всех элементов. В идеальном случае результирующий макет будет использовать различные свойства всех своих частей, но при этом умещаться в окне приложения. Для этого требуется хорошая реализация логики макета в панелях, а также продуманная структура пользовательского интерфейса со стороны кода любого приложения, в пользовательском интерфейсе которого используется эта панель. Любая панель будет выглядеть плохо, если в общей структуре пользовательского интерфейса окажется больше дочерних элементов, чем в принципе может уместиться в приложении.

Главным принципом работы системы макета является наличие в каждом элементе, основанном на [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706), некоторого собственного поведения в случае, когда он выступает дочерним элементом в контейнере. Например, в классе **FrameworkElement** есть несколько API, которые передают нужную информацию для поведения макета или принципиально необходимы для работы макета. К ним можно отнести следующие.

-   [**Желаемый размер** ](https://msdn.microsoft.com/library/windows/apps/br208921) (фактически [ **UIElement** ](https://msdn.microsoft.com/library/windows/apps/br208911) свойство)
-   [**ActualHeight** ](https://msdn.microsoft.com/library/windows/apps/br208707) и [ **ActualWidth**](https://msdn.microsoft.com/library/windows/apps/br208709)
-   [**Высота** ](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [ **ширины**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width)
-   [**Поле**](https://msdn.microsoft.com/library/windows/apps/br208724)
-   [**LayoutUpdated** ](https://msdn.microsoft.com/library/windows/apps/br208722) событий
-   [**HorizontalAlignment** ](https://msdn.microsoft.com/library/windows/apps/br208720) и [ **VerticalAlignment**](https://msdn.microsoft.com/library/windows/apps/br208749)
-   [**ArrangeOverride** ](https://msdn.microsoft.com/library/windows/apps/br208711) и [ **MeasureOverride** ](https://msdn.microsoft.com/library/windows/apps/br208730) методы
-   [**Упорядочить** ](https://msdn.microsoft.com/library/windows/apps/br208914) и [ **мер** ](https://msdn.microsoft.com/library/windows/apps/br208952) методы: они имеют собственных реализаций, определенные в [ **FrameworkElement** ](https://msdn.microsoft.com/library/windows/apps/br208706) уровне, так как обрабатывать действия на уровне элемента макета

## <a name="measureoverride"></a>**MeasureOverride**


Возвращаемое значение метода [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) используется системой макета в качестве начального значения [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921) для самой панели, когда метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) вызывается для панели ее родительским элементом в макете. Выбор логики в методе так же важен, как и возвращаемое значение, и логика часто влияет на возвращаемое значение.

Все реализации [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) должны циклически проходить по содержимому свойства [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514) и вызывать для каждого дочернего элемента метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952). Посредством вызова метода **Measure** определяется значение свойства [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921). Оно может сообщать, сколько пространства нужно для самой панели, а также каким образом пространство распределяется между элементами и какой размер имеет конкретный дочерний элемент.

Вот простейшая схема метода [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730):

```CSharp
protected override Size MeasureOverride(Size availableSize)
{
    Size returnSize; //TODO might return availableSize, might do something else
     
    //loop through each Child, call Measure on each
    foreach (UIElement child in Children)
    {
        child.Measure(new Size()); // TODO determine how much space the panel allots for this child, that's what you pass to Measure
        Size childDesiredSize = child.DesiredSize; //TODO determine how the returned Size is influenced by each child's DesiredSize
        //TODO, logic if passed-in Size and net DesiredSize are different, does that matter?
    }
    return returnSize;
}
```

Ко времени компоновки макета элементы часто имеют естественный размер. После этапа измерения этот естественный размер может отражаться в свойстве [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921), если параметр *availableSize*, переданный в метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952), имел меньшее значение. Если естественный размер превышает значение параметра *availableSize*, переданного в метод **Measure**, то размер **DesiredSize** ограничивается значением *availableSize*. Так работает внутренняя реализация метода **Measure**, и это нужно принимать во внимание в переопределенных методах макета.

Некоторые элементы не имеют естественного размера, поскольку их свойства [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) и [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) имеют значение **Auto**. Для таких элементов используется полный размер *availableSize*, потому что значение **Auto** представляет размер элемента вплоть до максимального доступного, который передается непосредственным родительским элементом в макете путем вызова [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) с параметром *availableSize*. На практике всегда есть некоторые измерения, пользовательского интерфейса изменяется в соответствии с (даже если это окно верхнего уровня). В конечном счете, проход измерения разрешает все **автоматически** значения родительского ограничения, а также все **автоматически** элементов значение get реальные измерения (который можно получить, проверив [  **ActualWidth** ](https://msdn.microsoft.com/library/windows/apps/br208709) и [ **ActualHeight**](https://msdn.microsoft.com/library/windows/apps/br208707)— после завершения макета).

В метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) допустимо передавать размер, который имеет по крайней мере одну бесконечную размерность, чтобы показать, что размер панели будет увеличиваться таким образом, чтобы вместить любое содержимое. Любой дочерний элемент при измерении устанавливает свое значение [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921), используя свой естественный размер. Затем на этапе упорядочения панель, как правило, использует этот размер.

Для текстовых элементов, подобных [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652), значения [**ActualWidth**](https://msdn.microsoft.com/library/windows/apps/br208709) и [**ActualHeight**](https://msdn.microsoft.com/library/windows/apps/br208707) вычисляются по текстовой строке и текстовым свойствам, даже если не задано значение [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) или [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width). В вашей логике панели следует учитывать эти измерения. Отсечение текста совершенно недопустимо в пользовательском интерфейсе.

Даже если в вашей реализации не используются измерения требуемого размера, следует вызвать метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) для каждого дочернего элемента, поскольку при вызове **Measure** запускается собственное внутреннее поведение. Чтобы включить элемент в макет, необходимо вызвать метод **Measure** для каждого дочернего элемента на этапе измерения и метод [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914) на этапе упорядочения. При вызове этих методов устанавливаются внутренние флаги объекта и заполняются значения (например, свойство [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921)), которые необходимы системной логике макета при создании визуального дерева и отрисовке пользовательского интерфейса.

Возвращаемое значение [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) определяется тем, как в логике панели учитываются значение [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921) и другие требования к размеру для каждого из дочерних элементов в [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514), когда для них вызывается метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952). Логика определяет, как обрабатываются значения **DesiredSize** из дочерних элементов и как они используются возвращаемым значением **MeasureOverride**. Обычно нельзя просто сложить все значения без изменения, потому что входным параметром **MeasureOverride** часто служит фиксированный доступный размер, который предлагается родительским элементом панели. Если этот размер будет превышен, то сама панель может оказаться усеченной. Как правило, общий размер дочерних элементов сравнивается с доступным размером панели, а затем вносятся необходимые коррективы.

### <a name="tips-and-guidance"></a>Советы и рекомендации

-   В идеальном случае настраиваемая панель должна подходить на роль первого визуального элемента в структуре пользовательского интерфейса — возможно, сразу после элемента [**Page**](https://msdn.microsoft.com/library/windows/apps/br227503), [**UserControl**](https://msdn.microsoft.com/library/windows/apps/br227647) или другого элемента, служащего корнем XAML-страницы. В реализациях [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) не следует просто возвращать входной параметр [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995) без проверки значений. Если в возвращаемом параметре **Size** окажется значение **Infinity**, то в логике макета во время выполнения может возникнуть исключение. Значение **Infinity** может передаваться из главного окна приложения, которое поддерживает прокрутку и поэтому не имеет максимальной высоты. Другое содержимое с возможностью прокрутки может вести себя аналогичным образом.
-   Другой распространенной ошибкой в реализациях [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) является возвращение нового значения [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995) по умолчанию (с нулевыми значениями высоты и ширины). С этого значения можно начинать, и оно даже может оказаться правильным, если окажется, что не нужно отрисовывать ни один из дочерних элементов панели. Однако если задать значение **Size** по умолчанию, то основной элемент панели неправильно определит ее размер. Для панели не будет запрошено (и не будет получено) пространство в пользовательском интерфейсе, и в итоге она не будет отрисовываться. Весь остальной код панели может работать правильно, однако ни панель, ни ее содержимое не будут видны, если при компоновке для нее заданы нулевая высота и нулевая ширина.
-   В переопределяемых методах не поддавайтесь искушению приводить дочерние элементы к типу [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706) и использовать в качестве результата макета вычисляемые свойства, главным образом [**ActualWidth**](https://msdn.microsoft.com/library/windows/apps/br208709) и [**ActualHeight**](https://msdn.microsoft.com/library/windows/apps/br208707). В самых распространенных сценариях вы можете опираться в логике на значение [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921) дочернего элемента, и тогда не понадобятся никакие свойства дочернего элемента, связанные с [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) или [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width). В особых случаях, когда тип элемента известен и имеется дополнительная информация, например естественный размер файла изображения, вы можете воспользоваться этими данными элемента, поскольку такое значение не меняется системами макета. Включение в логику свойств, вычисляемых макетом, значительно повышает риск непреднамеренного определения цикла в макете. Такие циклы приводят к ситуации, когда не удается создать правильный макет, и, если цикл невозможно прервать, система создает исключение [**LayoutCycleException**](https://msdn.microsoft.com/library/windows/apps/hh673799).
-   Панели обычно распределяют свое доступное пространство между несколькими дочерними элементами, но точное распределение пространства может меняться. Например, в классе [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704) реализована логика макета, которая с помощью значений [**RowDefinition**](https://msdn.microsoft.com/library/windows/apps/br227606) и [**ColumnDefinition**](https://msdn.microsoft.com/library/windows/apps/br209324) делит пространство на ячейки **Grid**, поддерживая и режим звезды, и значения в пикселях. Если используются значения в пикселях, то для каждого дочернего элемента уже известен доступный размер, и он передается в качестве входного размера в метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952) (табличного типа).
-   Сами панели могут резервировать некоторое заполняющее пространство между элементами. В этом случае предоставьте измерения как свойство, которое отлично от [**Margin**](https://msdn.microsoft.com/library/windows/apps/br208724) и любых свойств **Padding**.
-   Значения свойств [**ActualWidth**](https://msdn.microsoft.com/library/windows/apps/br208709) и [**ActualHeight**](https://msdn.microsoft.com/library/windows/apps/br208707) у элементов могут зависеть от предыдущего этапа разметки. Если значения изменились, то код пользовательского интерфейса приложения может разместить обработчики для события [**LayoutUpdated**](https://msdn.microsoft.com/library/windows/apps/br208722) в элементах, где требуется особая логика для запуска, но обычно логика панели не проверяет наличие изменений путем обработки событий. Система макета самостоятельно определяет, когда следует повторить составление макета из-за изменившегося значения свойства, связанного с макетом, и методы панели [**MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730) или [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711) автоматически вызываются, когда это необходимо.

## <a name="arrangeoverride"></a>**ArrangeOverride**


Метод [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711) имеет возвращаемое значение [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995), которое используется системой макета при отрисовке самой панели, когда метод [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914) вызывается для панели ее родительским элементом в макете. Как правило, входное значение *finalSize* совпадает со значением **Size**, возвращаемым методом **ArrangeOverride**. Если эти значения различаются, это говорит о том, что панель пытается получить размер, который отличается от доступного для других участников требования макета. Окончательный размер зависит от ранее выполненного этапа измерения макета в коде панели, и поэтому редко возвращается другой размер: это значит, что вы намеренно игнорируете логику измерения.

Не возвращайте значение [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995) с компонентом **Infinity**. Попытка использования такого значения **Size** приводит к возникновению исключения во внутреннем макете.

Все реализации [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711) должны циклически проходить по содержимому свойства [**Children**](https://msdn.microsoft.com/library/windows/apps/br227514) и вызывать для каждого дочернего элемента метод [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914). Как и метод [**Measure**](https://msdn.microsoft.com/library/windows/apps/br208952), метод **Arrange** не имеет возвращаемого значения. В отличие от метода **Measure**, в качестве результата не задаются никакие вычисляемые свойства (однако исследуемый элемент обычно создает событие [**LayoutUpdated**](https://msdn.microsoft.com/library/windows/apps/br208722)).

Вот простейшая схема метода [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711):

```CSharp
protected override Size ArrangeOverride(Size finalSize)
{
    //loop through each Child, call Arrange on each
    foreach (UIElement child in Children)
    {
        Point anchorPoint = new Point(); //TODO more logic for topleft corner placement in your panel
       // for this child, and based on finalSize or other internal state of your panel
        child.Arrange(new Rect(anchorPoint, child.DesiredSize)); //OR, set a different Size 
    }
    return finalSize; //OR, return a different Size, but that's rare
}
```

Этап упорядочения макета может выполняться без предшествующего ему этапа измерения. Однако так случается только в случае, если система макета определила, что не изменились никакие свойства, которые могли повлиять на предыдущие измерения. Например, если изменилось выравнивание элемента, то для него не нужно повторять измерение, поскольку его значение [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921) не меняется в зависимости от выравнивания. С другой стороны, если для любого элемента в макете меняется свойство [**ActualHeight**](https://msdn.microsoft.com/library/windows/apps/br208707), необходимо провести новый этап измерения. Система макета автоматически обнаруживает реальные изменения измерений и запускает новый этап измерения, а затем новый этап упорядочения.

Метод [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914) в качестве входного параметра принимает значение типа [**Rect**](https://msdn.microsoft.com/library/windows/apps/br225994). Чаще всего для создания объекта **Rect** используется конструктор, имеющий входные параметры типа [**Point**](https://msdn.microsoft.com/library/windows/apps/br225870) и [**Size**](https://msdn.microsoft.com/library/windows/apps/br225995). Параметр типа **Point** задает точку, соответствующую расположению верхнего левого угла ограничивающего прямоугольника для элемента. Параметр типа **Size** задает размеры, используемые при отрисовке данного элемента. Для этого элемента в качестве значения **Size** часто используется [**DesiredSize**](https://msdn.microsoft.com/library/windows/apps/br208921), поскольку этап измерения как раз выполняется с целью получить значение **DesiredSize** для всех элементов, входящих в макет. (На этапе измерения общие размеры элементов определяются итеративно, чтобы система макета могла оптимизировать расположение элементов после перехода на этап упорядочения.)

Реализации [**ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711) обычно различаются логикой, по которой панель определяет компонент [**Point**](https://msdn.microsoft.com/library/windows/apps/br225870), используемый в упорядочении каждого дочернего элемента. Панель с абсолютным позиционированием, например [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267), использует явную информацию о размещении, получаемую из значений [**Canvas.Left**](https://msdn.microsoft.com/library/windows/apps/hh759771) и [**Canvas.Top**](https://msdn.microsoft.com/library/windows/apps/hh759772) каждого элемента. Панель с разделением пространства, например [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704), выполняет математические операции, делящие доступное пространство на ячейки; у каждой ячейки имеется значение x-y, в соответствии с которым размещается и упорядочивается ее содержимое. Адаптивная панель, например [**StackPanel**](https://msdn.microsoft.com/library/windows/apps/br209635), может расширяться таким образом, чтобы уместить все содержимое в направлении своей ориентации.

На расположение элементов в макете влияют и другие факторы помимо тех, которые вы можете явным образом контролировать и передавать в метод [**Arrange**](https://msdn.microsoft.com/library/windows/apps/br208914). Это влияние объясняется собственной внутренней реализацией метода **Arrange**, общей для всех типов, производных от [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706), и дополненной некоторыми другими типами, например текстовыми элементами. Например, какие-то элементы могут иметь поля и различное выравнивание, а какие-то — заполнение. Эти свойства часто взаимодействуют друг с другом. Дополнительные сведения см. в статье [Выравнивание, поле и заполнение](alignment-margin-padding.md).

## <a name="panels-and-controls"></a>Панели и элементы управления


Не помещайте в настраиваемую панель функции, которые следует реализовать в пользовательском элементе управления. Задача панели — представить все содержащиеся в ней дочерние элементы в виде автоматически срабатывающей функции макета. Панель может добавлять к содержимому элементы оформления (аналогично тому, как [**Border**](https://msdn.microsoft.com/library/windows/apps/br209250) добавляет границу вокруг элемента) или выполнять другие изменения, относящиеся к макету, например добавлять заполнение. Однако на этом фактически заканчиваются возможности расширения визуального дерева, не привлекающие информацию от дочерних элементов и не связанные с составлением отчетов.

Если пользователю должно быть доступно какое-либо взаимодействие, его нужно реализовать в пользовательском элементе управления, а не посредством панели. Например, панель не должна добавлять окна просмотра с прокруткой к своему содержимому даже для того, чтобы исключить отсечение, поскольку полосы прокрутки, бегунки и прочие компоненты относятся к интерактивным элементам управления. (Содержимое может иметь полосу прокрутки, но она должна подчиняться логике дочернего элемента. Не будет применить его, добавив прокрутка как операция макета.) Можно создать элемент управления и также написать пользовательскую панель, играет важную роль в визуальном дереве элемента управления, когда дело доходит до представления содержимого в этом элементе управления. Однако элемент управления и панель должны быть различными объектами кода.

Различение между элементом управления и панелью важно для модели автоматизации пользовательского интерфейса Microsoft Windows, а также для реализации специальных возможностей. Панели определяют визуальное поведение макета, а не логическое. Обычно для сценариев специальных возможностей неважно, каким образом визуально представлен элемент пользовательского интерфейса. Специальные возможности предполагают предоставление частей приложения, логически важных для понимания пользовательского интерфейса. Если требуется взаимодействие, элементы управления должны предоставлять возможности взаимодействия инфраструктуре автоматизации пользовательского интерфейса. Дополнительную информацию см. в разделе [Настраиваемые одноранговые элементы автоматизации](https://msdn.microsoft.com/library/windows/apps/mt297667).

## <a name="other-layout-api"></a>Другие API макета


В систему макета входят и другие API, которые не объявлены в классе [**Panel**](https://msdn.microsoft.com/library/windows/apps/br227511). Их можно использовать в реализации панели или в пользовательском элементе управления, в котором применяются панели.

-   [**UpdateLayout**](https://msdn.microsoft.com/library/windows/apps/br208989), [ **InvalidateMeasure**](https://msdn.microsoft.com/library/windows/apps/br208930), и [ **InvalidateArrange** ](https://msdn.microsoft.com/library/windows/apps/br208929) способами, Инициируйте прохода макета. Метод **InvalidateArrange** может не запустить этап измерения, но два других метода запускают его. Никогда не запускайте эти методы из переопределенного метода макета, потому что в этом случае почти наверняка возникнет зацикливание макета. В коде элемента управления, как правило, тоже нет необходимости вызывать эти методы. Большинство аспектов макета активируются автоматически, когда обнаруживаются изменения в свойствах макета, определяемых платформой, например [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) и т. д.
-   [**LayoutUpdated** ](https://msdn.microsoft.com/library/windows/apps/br208722) является событие, возникающее при изменении некоторых аспектов макет элемента. Это справедливо не только для панелей. Данное событие определено в классе [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706).
-   [**SizeChanged** ](https://msdn.microsoft.com/library/windows/apps/br208742) является событие, которое запускается только в том случае, после этапов разметки завершаются, а также указывает, что [ **ActualHeight** ](https://msdn.microsoft.com/library/windows/apps/br208707) или [ **ActualWidth**  ](https://msdn.microsoft.com/library/windows/apps/br208709) были изменены в результате. Это событие также определяется в классе [**FrameworkElement**](https://msdn.microsoft.com/library/windows/apps/br208706). Существуют ситуации, когда событие [**LayoutUpdated**](https://msdn.microsoft.com/library/windows/apps/br208722) генерируется, а событие **SizeChanged** — нет. Например, порядок расположения внутреннего содержимого изменился, но размер элементов не изменился.


## <a name="related-topics"></a>Статьи по теме

**Reference**
* [**FrameworkElement.ArrangeOverride**](https://msdn.microsoft.com/library/windows/apps/br208711)
* [**FrameworkElement.MeasureOverride**](https://msdn.microsoft.com/library/windows/apps/br208730)
* [**Панель**](https://msdn.microsoft.com/library/windows/apps/br227511)

**Концепции**
* [Выравнивание, поля и заполнение](alignment-margin-padding.md)
