---
Description: Можно определить присоединенные макеты для использования с контейнерами, такими как элемент управления Итемсрепеатер.
title: аттачедлайаут
label: AttachedLayout
template: detail.hbs
ms.date: 03/13/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: dc23e86f85c5db3dd10c5cec152047be387d4513
ms.sourcegitcommit: 445320ff0ee7323d823194d4ec9cfa6e710ed85d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282289"
---
# <a name="attached-layouts"></a>Подключенные макеты

Контейнер (например, Panel), который делегирует свою логику макета другому объекту, использует присоединенный объект макета для предоставления поведения макета для своих дочерних элементов.  Присоединенная модель макета обеспечивает гибкость приложения для изменения макета элементов во время выполнения или упрощает совместное использование аспектов макета между различными частями пользовательского интерфейса (например, элементами в строках таблицы, которые должны быть согласованы в столбце).

В этом разделе мы расскажем о том, что происходит при создании присоединенного макета (виртуализация и не виртуализация), о концепциях и классах, которые необходимо понимать, и о компромиссах, которые необходимо учитывать при выборе между ними.

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| Этот элемент управления является частью библиотеки пользовательского интерфейса Windows, пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в [обзоре библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **Важные API**:

> * [ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)
> * [ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater)
> * [Макет](/uwp/api/microsoft.ui.xaml.controls.layout)
>     * [нонвиртуализинглайаут](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout)
>     * [виртуализинглайаут](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout)
> * [лайаутконтекст](/uwp/api/microsoft.ui.xaml.controls.layoutcontext)
>     * [нонвиртуализинглайаутконтекст](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext)
>     * [виртуализинглайаутконтекст](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext)
> * [Лайаутпанел](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) (Предварительная версия)

## <a name="key-concepts"></a>Основные понятия

Для выполнения макета необходимо ответить на два вопроса для каждого элемента:

1. Какой ***Размер*** будет использоваться для этого элемента?

2. Каково ***Расположение*** этого элемента?

Система макета XAML, которая отвечает на эти вопросы, кратко рассматривается как часть обсуждения [пользовательских панелей](/windows/uwp/design/layout/custom-panels-overview).

### <a name="containers-and-context"></a>Контейнеры и контекст

По сути, [панель](/uwp/api/windows.ui.xaml.controls.panel) XAML в платформе заполняет две важные роли:

1. Он может содержать дочерние элементы и представляет ветвление в дереве элементов.
2. Он применяет определенную стратегию макета к этим дочерним элементам.

По этой причине панель в XAML часто является синонимом макета, но технически говоря, не просто разметки.

[Итемсрепеатер](/windows/uwp/design/controls-and-patterns/items-repeater) также ведет себя как Panel, но, в отличие от панели, она не предоставляет свойство Children, которое позволило бы программно добавлять или удалять дочерние элементы UIElement.  Вместо этого время существования своих дочерних элементов автоматически управляется платформой для соответствия коллекции элементов данных.  Несмотря на то, что он не является производным от Panel, он ведет себя и обрабатывается инфраструктурой, такой как панель.

> [!NOTE]
> [Лайаутпанел](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) — это контейнер, производный от Panel, который делегирует свою логику присоединенному объекту [макета](/uwp/api/microsoft.ui.xaml.controls.layoutpanel.layout) .  Лайаутпанел находится в *режиме предварительной* версии и в настоящее время доступен только в *отпадает от* пакета винуи.

#### <a name="containers"></a>Контейнеры

По сути, [панель](/uwp/api/windows.ui.xaml.controls.panel) — это контейнер элементов, которые также могут отображать Пиксели для [фона](/uwp/api/windows.ui.xaml.controls.panel.background).  Панели предоставляют способ инкапсуляции общей логики макета в простой в использовании пакет.

Концепция **присоединенного макета** делает различие между двумя ролями контейнера и макета более четким.  Если контейнер делегирует свою логику макета другому объекту, мы вызываем этот объект в присоединенный макет, как показано в приведенном ниже фрагменте кода. Контейнеры, которые наследуют от [FrameworkElement](/uwp/api/windows.ui.xaml.frameworkelement), например лайаутпанел, автоматически предоставляют общие свойства, которые обеспечивают входные данные для процесса макета XAML (например, высота и ширина).

```xaml
<LayoutPanel>
    <LayoutPanel.Layout>
        <UniformGridLayout/>
    </LayoutPanel.Layout>
    <Button Content="1"/>
    <Button Content="2"/>
    <Button Content="3"/>
</LayoutPanel>
```

Во время процесса макета контейнер использует присоединенные *униформгридлайаут* для измерения и упорядочивания дочерних элементов.

#### <a name="per-container-state"></a>Состояние контейнера

При наличии присоединенного макета один экземпляр объекта макета может быть связан с *множеством* контейнеров, как в приведенном ниже фрагменте кода. Поэтому он не должен зависеть от контейнера узла или напрямую ссылаться на него.  Например:

```xaml
<!-- ... --->
<Page.Resources>
    <ExampleLayout x:Name="exampleLayout"/>
<Page.Resources>

<LayoutPanel x:Name="example1" Layout="{StaticResource exampleLayout}"/>
<LayoutPanel x:Name="example2" Layout="{StaticResource exampleLayout}"/>
<!-- ... --->
```

В этой ситуации *ексамплелайаут* необходимо тщательно рассмотреть состояние, которое используется в вычислении макета и где хранится это состояние, чтобы избежать влияния на макет для элементов на одной панели с другой.  Это было бы аналогом с пользовательской панелью, логика MeasureOverride и ArrangeOverride которой зависит от значений его *статических* свойств.

#### <a name="layoutcontext"></a>лайаутконтекст

Цель [лайаутконтекст](/uwp/api/microsoft.ui.xaml.controls.layoutcontext) заключается в том, чтобы справиться с этими проблемами.  Он предоставляет присоединенному макету возможность взаимодействия с контейнером размещения, например получение дочерних элементов, без введения прямой зависимости между ними. Контекст также позволяет макету сохранять любое состояние, которое ему необходимо, может быть связано с дочерними элементами контейнера.

Простые макеты, не связанные с виртуализацией, часто не требуют поддержания какого-либо состояния, делая их недействительными. Однако более сложная структура, например Grid, может поддерживать состояние между мерой и компоновкой вызова, чтобы избежать повторной вычисления значения.

Для виртуализации макетов *часто* требуется поддерживать некоторое состояние между мерой и структурой, а также между итеративными проходами макета.

#### <a name="initializing-and-uninitializing-per-container-state"></a>Инициализация и отмена инициализации состояния контейнера

При присоединении макета к контейнеру вызывается его метод [инитиализефорконтексткоре](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore) , который предоставляет возможность инициализировать объект для хранения состояния.

Аналогичным образом при удалении макета из контейнера будет вызван метод [унинитиализефорконтексткоре](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore) .  Это дает макету возможность очистить любое состояние, связанное с этим контейнером.

Объект состояния макета может храниться и извлекаться из контейнера с помощью свойства [лайаутстате](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate) в контексте.

### <a name="ui-virtualization"></a>Виртуализация пользовательского интерфейса

Виртуализация пользовательского интерфейса означает задержку создания объекта пользовательского интерфейса до тех пор, пока _он не понадобится_.  Это оптимизация производительности.  Для сценариев без прокрутки, определяющих, _когда это необходимо_ , может быть основано на любом количестве объектов, зависящих от приложения.  В таких случаях приложения должны использовать [x:Load](../../xaml-platform/x-load-attribute.md). В макете не требуется специальная обработка.

В сценариях на основе прокрутки, таких как список, определение, _когда это необходимо_ , часто основывается на «будет ли он видимым для пользователя», который, в большой степени, зависит от того, где он был помещен во время процесса макета и требует особых рекомендаций.  Этот сценарий посвящен этому документу.

> [!NOTE]
> Хотя это и не рассматривается в этом документе, те же возможности, которые позволяют реализовать виртуализацию пользовательского интерфейса в сценариях прокрутки, можно применять в сценариях без прокрутки.  Например, управляемый данными элемент управления ToolBar, который управляет временем существования команд, которые он представляет, и реагирует на изменения в доступном пространстве, перезапуская или перемещая элементы между видимой областью и меню переполнения.

## <a name="getting-started"></a>начало работы

Сначала определите, должен ли макет, который необходимо создать, поддерживать виртуализацию пользовательского интерфейса.

**Необходимо помнить о некоторых вещах...**

1. Разработка макетов без виртуализации упрощается. Если число элементов всегда будет небольшим, то рекомендуется создавать макет без виртуализации.
2. Платформа предоставляет набор подключенных макетов, которые работают с [итемсрепеатер](/windows/uwp/design/controls-and-patterns/items-repeater#change-the-layout-of-items) и [лайаутпанел](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) для удовлетворения распространенных потребностей.  Ознакомьтесь с ними, прежде чем принимать решение о необходимости определения пользовательского макета.
3. Виртуализация макетов всегда имеет дополнительные затраты на ЦП и память, а сложность и нагрузку на них по сравнению с макетом без виртуализации.  Как правило, если дочерние элементы, которыми должен управлять макет, скорее всего помещаются в области, 3 раза размер окна просмотра, то в случае виртуализации может не быть значительного размера. Размер 3 раза более подробно рассматривается далее в этом документе, но он связан с асинхронной природой прокрутки окон и его влиянием на виртуализацию.

> [!TIP]
> В качестве точки ссылки параметры по умолчанию для [ListView](/uwp/api/windows.ui.xaml.controls.listview) (и [итемсрепеатер](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)) заключаются в том, что повторный запуск не начинается, пока количество элементов не будет достаточно, чтобы заполнить 3 раза до размера текущего окна просмотра.

**Выбор базового типа**

![Иерархия присоединенных макетов](images/xaml-attached-layout-hierarchy.png)

Базовый тип [макета](/uwp/api/microsoft.ui.xaml.controls.layout) имеет два производных типа, которые служат начальной точкой для создания присоединенного макета:

1. [нонвиртуализинглайаут](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout)
2. [виртуализинглайаут](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout)

## <a name="non-virtualizing-layout"></a>Макет без виртуализации

Подход к созданию макета без виртуализации должен быть знаком всем, кто создал [пользовательскую панель](/windows/uwp/design/layout/custom-panels-overview).  Применяются те же принципы.  Основное отличие заключается в том, что [нонвиртуализинглайаутконтекст](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext) используется для доступа к коллекции [Children](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext.children) , а макет — для хранения состояния.

1. Наследуется от базового типа [нонвиртуализинглайаут](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout) (а не с панели).
2. *(Необязательно)* Определить свойства зависимостей, которые при изменении не сделают макет недействительным.
3. _(**New**/оптионал)_ Инициализируйте любой объект состояния, необходимый для макета, в составе [инитиализефорконтексткоре](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore). Скройте его с контейнером размещения, используя [лайаутстате](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate) , предоставленный в контексте.
4. Переопределите [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout.measureoverride) и вызовите метод [Measure](/uwp/api/windows.ui.xaml.uielement.measure) для всех дочерних элементов.
5. Переопределите [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout.arrangeoverride) и вызовите метод [упорядочения](/uwp/api/windows.ui.xaml.uielement.arrange) для всех дочерних элементов.
6. *(**New**/оптионал)* Очистите все сохраненные состояния как часть [унинитиализефорконтексткоре](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore).

### <a name="example-a-simple-stack-layout-varying-sized-items"></a>Пример. простой макет стека (элементы разного размера)

![мистакклайаут](images/xaml-attached-layout-mystacklayout.png)

Ниже приведен очень простой макет стека, не являющийся виртуализацией, для элементов разного размера. У него отсутствуют свойства для настройки поведения макета. В приведенной ниже реализации показано, как макет основывается на объекте контекста, предоставленном контейнером, чтобы:

1. Получение числа дочерних элементов и
2. Доступ к каждому дочернему элементу по индексу.

```csharp
public class MyStackLayout : NonVirtualizingLayout
{
    protected override Size MeasureOverride(NonVirtualizingLayoutContext context, Size availableSize)
    {
        double extentHeight = 0.0;
        foreach (var element in context.Children)
        {
            element.Measure(availableSize);
            extentHeight += element.DesiredSize.Height;
        }

        return new Size(availableSize.Width, extentHeight);
    }

    protected override Size ArrangeOverride(NonVirtualizingLayoutContext context, Size finalSize)
    {
        double offset = 0.0;
        foreach (var element in context.Children)
        {
            element.Arrange(
                new Rect(0, offset, finalSize.Width, element.DesiredSize.Height));
            offset += element.DesiredSize.Height;
        }

        return finalSize;
    }
}
```

```xaml
 <LayoutPanel MaxWidth="196">
    <LayoutPanel.Layout>
        <local:MyStackLayout/>
    </LayoutPanel.Layout>

    <Button HorizontalAlignment="Stretch">1</Button>
    <Button HorizontalAlignment="Right">2</Button>
    <Button HorizontalAlignment="Center">3</Button>
    <Button>4</Button>

</LayoutPanel>
```

## <a name="virtualizing-layouts"></a>Виртуализация макетов

Как и в случае с макетом без виртуализации, общие действия для макета виртуализации одинаковы.  Сложность в основном определяется тем, какие элементы будут находиться в окне просмотра и должны быть реализованы.

1. Производные от базового типа [виртуализинглайаут](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout).
2. Используемых Определите свойства зависимостей, которые после изменения станут недействительными для макета.
3. Инициализируйте любой объект состояния, который потребуется для макета в составе [инитиализефорконтексткоре](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore). Скройте его с контейнером размещения, используя [лайаутстате](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate) , предоставленный в контексте.
4. Переопределите [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride) и вызовите метод [Measure](/uwp/api/windows.ui.xaml.uielement.measure) для каждого дочернего элемента, который должен быть реализован.
   1. Метод [жеторкреатилементат](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) используется для получения UIElement, подготовленного платформой (например, к примененным привязкам к данным).
5. Переопределите [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride) и вызовите метод [упорядочения](/uwp/api/windows.ui.xaml.uielement.arrange) для каждого реализованного дочернего элемента.
6. Используемых Очистите все сохраненные состояния как часть [унинитиализефорконтексткоре](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore).

> [!TIP]
> Значение, возвращаемое [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout) , используется в качестве размера виртуализированного содержимого.

При создании макета виртуализации необходимо учитывать два общих подхода.  Выбор одного или другого параметра зависит от того, как будет определяться размер элемента.  Если достаточно определить индекс элемента в наборе данных или сами данные определяют его в конечном итоге, мы будем считать его **зависимым от данных**.  Они более просты в создании.  Однако, если единственный способ определить размер элемента — создать и измерить пользовательский интерфейс, тогда он будет **зависеть от содержимого**.  Они более сложны.

### <a name="the-layout-process"></a>Процесс макета

Независимо от того, создаете ли вы данные или макет, зависящий от содержимого, важно понимать процесс макета и влияние асинхронной прокрутки Windows.

Более (более) упрощенное представление шагов, выполняемых платформой при запуске, для отображения пользовательского интерфейса на экране состоит в том, что:

1. Он анализирует разметку.

2. Создает дерево элементов.

3. Выполняет проход макета.

4. Выполняет проход прорисовки.

С помощью виртуализации пользовательского интерфейса создание элементов, которые обычно выполняются на шаге 2, откладывается или завершается на раннем этапе после определения того, что для заполнения окна просмотра было создано достаточное содержимое. Контейнер виртуализации (например, Итемсрепеатер) откладывается на присоединенный к нему макет для обработки этого процесса. Он предоставляет присоединенный макет с [виртуализинглайаутконтекст](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext) , который содержит дополнительные сведения, необходимые для макета виртуализации.

**Реализатионрект (т. е. окно просмотра)**

Прокрутка окон происходит асинхронно в потоке пользовательского интерфейса. Он не контролируется макетом платформы.  Вместо этого взаимодействие и перемещение происходит в системе компоновки системы. Преимуществом этого подхода является то, что содержимое панорамирования всегда можно выполнять на 60fps.  Однако проблема заключается в том, что «окно просмотра», как видно в макете, может быть немного неизменным относительно того, что фактически видимо на экране. Если пользователь быстро прокручивается, он может увеличить скорость потока пользовательского интерфейса для создания нового содержимого и "Панорама к черному". По этой причине для макета виртуализации часто требуется создавать дополнительный буфер подготовленных элементов, достаточный для заполнения области, превышающей область просмотра. При более плотной нагрузке при прокрутке пользователя все равно отображается содержимое.

![Прямоугольник реализации](images/xaml-attached-layout-realizationrect.png)

Поскольку создание элемента является дорогостоящим, виртуализация контейнеров (например, [итемсрепеатер](/windows/uwp/design/controls-and-patterns/items-repeater)) изначально предоставляет присоединенный макет с [реализатионрект](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) , который соответствует области просмотра. В момент простоя контейнер может расширить буфер подготовленного содержимого, делая повторные вызовы к макету, используя все большее значение прямоугольника реализации. Такое поведение является оптимизацией производительности, которая пытается установить баланс между быстрым временем запуска и хорошим интерфейсом панорамирования. Максимальный размер буфера, создаваемого Итемсрепеатер, управляется его свойствами [вертикалкачеленгс](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.verticalcachelength) и [хоризонталкачеленгс](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.verticalcachelength) .

**Повторное использование элементов (перезапуск)**

Предполагается, что макет имеет размер и размещает элементы для заполнения [реализатионрект](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) при каждом запуске. По умолчанию [виртуализинглайаут](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout) будет перезапускать все неиспользуемые элементы в конце каждого прохода макета.

[Виртуализинглайаутконтекст](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext) , который передается в макет как часть [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride) и [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride) , предоставляет дополнительные сведения, необходимые для виртуализации. Некоторые из наиболее часто используемых вещей — это возможность:

1. Запросите количество элементов в данных ([ItemCount](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.itemcount)).
2. Получите конкретный элемент с помощью метода [жетитемат](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getitemat) .
3. Получение [реализатионрект](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) , представляющего окно просмотра и буфер, которые должны быть заполнены в макете реализованными элементами.
4. Запросите UIElement для определенного элемента с помощью метода [жеторкреатилемент](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) .

Запрос элемента для заданного индекса приведет к тому, что этот элемент будет помечен как "используется" для этого этапа макета. Если элемент еще не существует, он будет реализован и автоматически подготовлен к использованию (например, при использовании дерева пользовательского интерфейса, определенного в DataTemplate, обработке любой привязки данных и т. д.).  В противном случае он будет извлечен из пула существующих экземпляров.

В конце каждого прохода меры любой существующий реализованный элемент, который не был отмечен как "используется", автоматически считается доступным для повторного использования, если только параметр [суппрессауторецикле](/uwp/api/microsoft.ui.xaml.controls.elementrealizationoptions) не был использован при извлечении элемента с помощью метода [жеторкреатилементат](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) . Платформа автоматически переместит ее в пул очистки и сделает ее доступной. Впоследствии он может быть извлечен для использования другим контейнером. Платформа пытается избежать этого, если это возможно, так как существует несколько затрат на повторное наследование элемента.

Если структура виртуализации известна в начале каждой меры, элементы которой больше не будут находиться в области применения, то она может оптимизировать его повторное использование. Вместо того, чтобы полагаться на поведение платформы по умолчанию. Макет может выполнять вытеснение элементов в пул очистки с помощью метода [рециклилемент](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recycleelement) .  Вызов этого метода перед запросом новых элементов приводит к тому, что существующие элементы будут доступны, когда макет порождает в дальнейшем запрос [жеторкреатилементат](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) для индекса, который еще не связан с элементом.

Виртуализинглайаутконтекст предоставляет два дополнительных свойства, предназначенных для авторов макета при создании макета, зависящего от содержимого. Они рассматриваются более подробно далее.

1. Объект [рекоммендеданчориндекс](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) , предоставляющий необязательный _Вход_ для макета.
2. [Лайауторигин](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.layoutorigin) , который является необязательным _выходом_ макета.

## <a name="data-dependent-virtualizing-layouts"></a>Макеты виртуализации, зависящие от данных

Разметка виртуализации упрощается, если известно, какой размер каждого элемента должен быть без необходимости измерять содержимое для отображения.  В этом документе мы просто будем называть эту категорию виртуализации в качестве **макетов данных** , так как обычно они требуют проверки данных.  На основе данных приложение может выбрать визуальное представление с известным размером, возможно, из-за того, что его часть данных или ранее была определена конструкцией.

Общий подход заключается в том, чтобы макет был следующим:

1. Вычислите размер и позицию каждого элемента.
2. Как часть [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride):
   1. Используйте [реализатионрект](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) , чтобы определить, какие элементы должны отображаться в окне просмотра.
   2. Получите объект UIElement, который должен представлять элемент с помощью метода [жеторкреатилементат](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) .
   3. [Измерьте](/uwp/api/windows.ui.xaml.uielement.measure) UIElement с предварительно вычисленным размером.
3. В рамках [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride) [упорядочите](/uwp/api/windows.ui.xaml.uielement.arrange) каждый реализованный UIElement с предварительно вычисленной позицией.

> [!NOTE]
> Подход к компоновке данных часто несовместим с _виртуализацией данных_.  В частности, когда в память загружены только данные, необходимые для заполнения видимых пользователю данных.  Виртуализация данных не ссылается на отложенную или добавочную загрузку данных по мере прокрутки пользователя, когда данные остаются резидентными.  Вместо этого он ссылается на то, когда элементы освобождаются из памяти по мере их прокрутки в представлении.  Наличие макета данных, проверяющего каждый элемент данных как часть структуры данных, не позволит виртуализации данных работать должным образом.  Исключение — это макет, такой как Униформгридлайаут, который предполагает, что все имеет одинаковый размер.

> [!TIP]
> Если вы создаете пользовательский элемент управления для библиотеки элементов управления, который будет использоваться другими пользователями в самых разных ситуациях, то макет данных может быть недоступен для вас.

### <a name="example-xbox-activity-feed-layout"></a>Пример: макет веб-канала активности Xbox

Пользовательский интерфейс для веб-канала активности Xbox использует Повторяющийся шаблон, где каждая строка имеет широкую плитку, за которой следуют две узкие плитки, Инвертированные на следующей строке. В этом макете размер каждого элемента является функцией позиции элемента в наборе данных и известного размера для плиток (широкие и узкие).

![Веб-канал активности Xbox](images/xaml-attached-layout-activityfeedscreenshot.png)

В приведенном ниже коде описано, какой пользовательский интерфейс виртуализации для веб-канала активности может быть показан на общем подходе, который может потребоваться для **макета данных**.

<table>
<td>
    <p>Если приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> установлено, щелкните здесь, чтобы открыть приложение и просмотреть <a href="xamlcontrolsgallery:/item/ItemsRepeater">итемсрепеатер</a> в действии с этим примером макета.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

#### <a name="implementation"></a>Реализация

```csharp
/// <summary>
///  This is a custom layout that displays elements in two different sizes
///  wide (w) and narrow (n). There are two types of rows 
///  odd rows - narrow narrow wide
///  even rows - wide narrow narrow
///  This pattern repeats.
/// </summary>

public class ActivityFeedLayout : VirtualizingLayout // STEP #1 Inherit from base attached layout
{
    // STEP #2 - Parameterize the layout
    #region Layout parameters

    // We'll cache copies of the dependency properties to avoid calling GetValue during layout since that
    // can be quite expensive due to the number of times we'd end up calling these.
    private double _rowSpacing;
    private double _colSpacing;
    private Size _minItemSize = Size.Empty;

    /// <summary>
    /// Gets or sets the size of the whitespace gutter to include between rows
    /// </summary>
    public double RowSpacing
    {
        get { return _rowSpacing; }
        set { SetValue(RowSpacingProperty, value); }
    }

    /// <summary>
    /// Gets or sets the size of the whitespace gutter to include between items on the same row
    /// </summary>
    public double ColumnSpacing
    {
        get { return _colSpacing; }
        set { SetValue(ColumnSpacingProperty, value); }
    }

    public Size MinItemSize
    {
        get { return _minItemSize; }
        set { SetValue(MinItemSizeProperty, value); }
    }

    public static readonly DependencyProperty RowSpacingProperty =
        DependencyProperty.Register(
            nameof(RowSpacing),
            typeof(double),
            typeof(ActivityFeedLayout),
            new PropertyMetadata(0, OnPropertyChanged));

    public static readonly DependencyProperty ColumnSpacingProperty =
        DependencyProperty.Register(
            nameof(ColumnSpacing),
            typeof(double),
            typeof(ActivityFeedLayout),
            new PropertyMetadata(0, OnPropertyChanged));

    public static readonly DependencyProperty MinItemSizeProperty =
        DependencyProperty.Register(
            nameof(MinItemSize),
            typeof(Size),
            typeof(ActivityFeedLayout),
            new PropertyMetadata(Size.Empty, OnPropertyChanged));

    private static void OnPropertyChanged(DependencyObject obj, DependencyPropertyChangedEventArgs args)
    {
        var layout = obj as ActivityFeedLayout;
        if (args.Property == RowSpacingProperty)
        {
            layout._rowSpacing = (double)args.NewValue;
        }
        else if (args.Property == ColumnSpacingProperty)
        {
            layout._colSpacing = (double)args.NewValue;
        }
        else if (args.Property == MinItemSizeProperty)
        {
            layout._minItemSize = (Size)args.NewValue;
        }
        else
        {
            throw new InvalidOperationException("Don't know what you are talking about!");
        }

        layout.InvalidateMeasure();
    }

    #endregion

    #region Setup / teardown // STEP #3: Initialize state

    protected override void InitializeForContextCore(VirtualizingLayoutContext context)
    {
        base.InitializeForContextCore(context);

        var state = context.LayoutState as ActivityFeedLayoutState;
        if (state == null)
        {
            // Store any state we might need since (in theory) the layout could be in use by multiple
            // elements simultaneously
            // In reality for the Xbox Activity Feed there's probably only a single instance.
            context.LayoutState = new ActivityFeedLayoutState();
        }
    }

    protected override void UninitializeForContextCore(VirtualizingLayoutContext context)
    {
        base.UninitializeForContextCore(context);

        // clear any state
        context.LayoutState = null;
    }

    #endregion

    #region Layout // STEP #4,5 - Measure and Arrange

    protected override Size MeasureOverride(VirtualizingLayoutContext context, Size availableSize)
    {
        if (this.MinItemSize == Size.Empty)
        {
            var firstElement = context.GetOrCreateElementAt(0);
            firstElement.Measure(new Size(double.PositiveInfinity, double.PositiveInfinity));

            // setting the member value directly to skip invalidating layout
            this._minItemSize = firstElement.DesiredSize;
        }

        // Determine which rows need to be realized.  We know every row will have the same height and
        // only contain 3 items.  Use that to determine the index for the first and last item that
        // will be within that realization rect.
        var firstRowIndex = Math.Max(
            (int)(context.RealizationRect.Y / (this.MinItemSize.Height + this.RowSpacing)) - 1,
            0);
        var lastRowIndex = Math.Min(
            (int)(context.RealizationRect.Bottom / (this.MinItemSize.Height + this.RowSpacing)) + 1,
            (int)(context.ItemCount / 3));

        // Determine which items will appear on those rows and what the rect will be for each item
        var state = context.LayoutState as ActivityFeedLayoutState;
        state.LayoutRects.Clear();

        // Save the index of the first realized item.  We'll use it as a starting point during arrange.
        state.FirstRealizedIndex = firstRowIndex * 3;

        // ideal item width that will expand/shrink to fill available space
        double desiredItemWidth = Math.Max(this.MinItemSize.Width, (availableSize.Width - this.ColumnSpacing * 3) / 4);

        // Foreach item between the first and last index,
        //     Call GetElementOrCreateElementAt which causes an element to either be realized or retrieved
        //       from a recycle pool
        //     Measure the element using an appropriate size
        //
        // Any element that was previously realized which we don't retrieve in this pass (via a call to
        // GetElementOrCreateAt) will be automatically cleared and set aside for later re-use.
        // Note: While this work fine, it does mean that more elements than are required may be
        // created because it isn't until after our MeasureOverride completes that the unused elements
        // will be recycled and available to use.  We could avoid this by choosing to track the first/last
        // index from the previous layout pass.  The diff between the previous range and current range
        // would represent the elements that we can pre-emptively make available for re-use by calling
        // context.RecycleElement(element).
        for (int rowIndex = firstRowIndex; rowIndex < lastRowIndex; rowIndex++)
        {
            int firstItemIndex = rowIndex * 3;
            var boundsForCurrentRow = CalculateLayoutBoundsForRow(rowIndex, desiredItemWidth);

            for (int columnIndex = 0; columnIndex < 3; columnIndex++)
            {
                var index = firstItemIndex + columnIndex;
                var rect = boundsForCurrentRow[index % 3];
                var container = context.GetOrCreateElementAt(index);

                container.Measure(
                    new Size(boundsForCurrentRow[columnIndex].Width, boundsForCurrentRow[columnIndex].Height));

                state.LayoutRects.Add(boundsForCurrentRow[columnIndex]);
            }
        }

        // Calculate and return the size of all the content (realized or not) by figuring out
        // what the bottom/right position of the last item would be.
        var extentHeight = ((int)(context.ItemCount / 3) - 1) * (this.MinItemSize.Height + this.RowSpacing) + this.MinItemSize.Height;

        // Report this as the desired size for the layout
        return new Size(desiredItemWidth * 4 + this.ColumnSpacing * 2, extentHeight);
    }

    protected override Size ArrangeOverride(VirtualizingLayoutContext context, Size finalSize)
    {
        // walk through the cache of containers and arrange
        var state = context.LayoutState as ActivityFeedLayoutState;
        var virtualContext = context as VirtualizingLayoutContext;
        int currentIndex = state.FirstRealizedIndex;

        foreach (var arrangeRect in state.LayoutRects)
        {
            var container = virtualContext.GetOrCreateElementAt(currentIndex);
            container.Arrange(arrangeRect);
            currentIndex++;
        }

        return finalSize;
    }

    #endregion
    #region Helper methods

    private Rect[] CalculateLayoutBoundsForRow(int rowIndex, double desiredItemWidth)
    {
        var boundsForRow = new Rect[3];

        var yoffset = rowIndex * (this.MinItemSize.Height + this.RowSpacing);
        boundsForRow[0].Y = boundsForRow[1].Y = boundsForRow[2].Y = yoffset;
        boundsForRow[0].Height = boundsForRow[1].Height = boundsForRow[2].Height = this.MinItemSize.Height;

        if (rowIndex % 2 == 0)
        {
            // Left tile (narrow)
            boundsForRow[0].X = 0;
            boundsForRow[0].Width = desiredItemWidth;
            // Middle tile (narrow)
            boundsForRow[1].X = boundsForRow[0].Right + this.ColumnSpacing;
            boundsForRow[1].Width = desiredItemWidth;
            // Right tile (wide)
            boundsForRow[2].X = boundsForRow[1].Right + this.ColumnSpacing;
            boundsForRow[2].Width = desiredItemWidth * 2 + this.ColumnSpacing;
        }
        else
        {
            // Left tile (wide)
            boundsForRow[0].X = 0;
            boundsForRow[0].Width = (desiredItemWidth * 2 + this.ColumnSpacing);
            // Middle tile (narrow)
            boundsForRow[1].X = boundsForRow[0].Right + this.ColumnSpacing;
            boundsForRow[1].Width = desiredItemWidth;
            // Right tile (narrow)
            boundsForRow[2].X = boundsForRow[1].Right + this.ColumnSpacing;
            boundsForRow[2].Width = desiredItemWidth;
        }

        return boundsForRow;
    }

    #endregion
}

internal class ActivityFeedLayoutState
{
    public int FirstRealizedIndex { get; set; }

    /// <summary>
    /// List of layout bounds for items starting with the
    /// FirstRealizedIndex.
    /// </summary>
    public List<Rect> LayoutRects
    {
        get
        {
            if (_layoutRects == null)
            {
                _layoutRects = new List<Rect>();
            }

            return _layoutRects;
        }
    }

    private List<Rect> _layoutRects;
}
```

### <a name="optional-managing-the-item-to-uielement-mapping"></a>Используемых Управление сопоставлением элемента с UIElement

По умолчанию [виртуализинглайаутконтекст](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext) поддерживает сопоставление между реализованными элементами и индексом в источнике данных, который они представляют.  Макет может управлять этим сопоставлением, всегда запрашивая параметр [суппрессауторецикле](/uwp/api/microsoft.ui.xaml.controls.elementrealizationoptions) при извлечении элемента с помощью метода [жеторкреатилементат](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) , который предотвращает поведение автоматического повторного запуска по умолчанию.  Макет может быть выбран для этого, например, если он будет использоваться только в том случае, если прокрутка ограничена одним направлением, а элементы, которые он считает, всегда будут непрерывными (т. е. знание индекса первого и последнего элемента достаточно для того, чтобы знать все элементы, которые должны быть реа лизед).

#### <a name="example-xbox-activity-feed-measure"></a>Пример: мера веб-канала активности Xbox

В приведенном ниже фрагменте кода показана дополнительная логика, которую можно добавить к MeasureOverride в предыдущем примере для управления сопоставлением.

```csharp
    protected override Size MeasureOverride(VirtualizingLayoutContext context, Size availableSize)
    {
        //...

        // Determine which items will appear on those rows and what the rect will be for each item
        var state = context.LayoutState as ActivityFeedLayoutState;
        state.LayoutRects.Clear();

         // Recycle previously realized elements that we know we won't need so that they can be used to
        // fill in gaps without requiring us to realize additional elements.
        var newFirstRealizedIndex = firstRowIndex * 3;
        var newLastRealizedIndex = lastRowIndex * 3 + 3;
        for (int i = state.FirstRealizedIndex; i < newFirstRealizedIndex; i++)
        {
            context.RecycleElement(state.IndexToElementMap.Get(i));
            state.IndexToElementMap.Clear(i);
        }

        for (int i = state.LastRealizedIndex; i < newLastRealizedIndex; i++)
        {
            context.RecycleElement(context.IndexElementMap.Get(i));
            state.IndexToElementMap.Clear(i);
        }

        // ...

        // Foreach item between the first and last index,
        //     Call GetElementOrCreateElementAt which causes an element to either be realized or retrieved
        //       from a recycle pool
        //     Measure the element using an appropriate size
        //
        for (int rowIndex = firstRowIndex; rowIndex < lastRowIndex; rowIndex++)
        {
            int firstItemIndex = rowIndex * 3;
            var boundsForCurrentRow = CalculateLayoutBoundsForRow(rowIndex, desiredItemWidth);

            for (int columnIndex = 0; columnIndex < 3; columnIndex++)
            {
                var index = firstItemIndex + columnIndex;
                var rect = boundsForCurrentRow[index % 3];
                UIElement container = null;
                if (state.IndexToElementMap.Contains(index))
                {
                    container = state.IndexToElementMap.Get(index);
                }
                else
                {
                    container = context = context.GetOrCreateElementAt(index, ElementRealizationOptions.ForceCreate | ElementRealizationOptions.SuppressAutoRecycle);
                    state.IndexToElementMap.Add(index, container);
                }

                container.Measure(
                    new Size(boundsForCurrentRow[columnIndex].Width, boundsForCurrentRow[columnIndex].Height));

                state.LayoutRects.Add(boundsForCurrentRow[columnIndex]);
            }
        }

        // ...
   }

internal class ActivityFeedLayoutState
{
    // ...
    Dictionary<int, UIElement> IndexToElementMap { get; set; }
    // ...
}
```

## <a name="content-dependent-virtualizing-layouts"></a>Макеты виртуализации, зависящие от содержимого

Если необходимо сначала измерить содержимое пользовательского интерфейса для элемента, чтобы выяснить его точный размер, это будет структура, **зависящая от содержимого**.  Его также можно рассматривать как макет, где каждый элемент должен иметь свой размер, а не макет, сообщающий элементу его размер. Виртуализация макетов, относящихся к этой категории, является более сложной.

> [!NOTE]
> Макеты, зависящие от содержимого, не должны прерывать виртуализацию данных.

### <a name="estimations"></a>Оценку

Макеты, зависящие от содержимого, основываются на оценке, чтобы выгадать как размер нереализованного содержимого, так и положение реализованного содержимого. По мере изменения этих оценок это приведет к тому, что реализованное содержимое будет регулярно перемещаться в пределах прокручиваемой области. Это может привести к очень неприятному и жарринг пользовательскому интерфейсу, если это не было устранено. Здесь обсуждаются потенциальные проблемы и способы их устранения.

> [!NOTE]
> Макеты данных, которые изменяют каждый элемент и заведомоируют точный размер всех элементов, реализованы или нет, и их положения могут полностью избежать этих проблем.

**Прокрутка с привязкой**

XAML предоставляет механизм для устранения внезапного сдвига окна просмотра, если элементы управления прокруткой поддерживают [привязку прокрутки](/uwp/api/windows.ui.xaml.controls.iscrollanchorprovider) путем реализации интерфейса [искролланчорповидер](/uwp/api/windows.ui.xaml.controls.iscrollanchorprovider) . По мере того, как пользователь манипулирует содержимым, элемент управления прокрутки непрерывно выбирает элемент из набора кандидатов, которые были выбраны для отслеживания. Если положение элемента привязки смещается во время макета, то элемент управления Scroll автоматически сдвигает окно просмотра для поддержки окна просмотра.

Значение [рекоммендеданчориндекс](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) , предоставленное для макета, может отражать выбранный в данный момент элемент привязки, выбранный элементом управления прокрутки. Кроме того, если разработчик явно запрашивает, что элемент был реализован для индекса с помощью метода [жеторкреатилемент](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.getorcreateelement) в [итемсрепеатер](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), этот индекс присваивается в качестве [рекоммендеданчориндекс](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) для следующего прохода макета. Это позволяет подготовить макет для наиболее вероятного сценария, что разработчик реализует элемент, а затем запрашивает его представление с помощью метода [стартбрингинтовиев](/uwp/api/windows.ui.xaml.uielement.startbringintoview) .

[Рекоммендеданчориндекс](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) — это индекс элемента в источнике данных, который должен быть первым расположен в зависимости от содержимого при оценке позиции его элементов. Он должен служить в качестве отправной точки для размещения других реализованных элементов.

**Влияние на полосы прокрутки**

Даже при использовании привязки с прокруткой, если оценки макета имеют большое значение, возможно, из-за существенных вариаций размера содержимого, может показаться, что для полосы прокрутки отображается положение бегунка.  Это может быть жарринг для пользователя, если бегунок не будет относиться к положению указателя мыши при его перетаскивании.

Чем точнее макет, тем менее вероятно, что пользователь увидит бегунок полосы прокрутки.

### <a name="layout-corrections"></a>Исправления макета

Макет, зависящий от содержимого, должен быть подготовлен для рационализировать своей оценки с реальности.  Например, по мере того, как пользователь прокручивается до верхней части содержимого, а макет — как первый элемент, он может обнаружить, что ожидаемое положение элемента относительно элемента, с которого оно было запущено, приведет к тому, что он будет находиться в другом месте, чем происхождение (x:0 , y:0). В этом случае макет может использовать свойство [лайауторигин](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.layoutorigin) , чтобы задать положение, вычисленное в качестве нового источника макета.  Итоговый результат похож на привязку к прокрутке, в которой окно просмотра окна прокрутки автоматически корректируется, чтобы учитывать положение содержимого, сообщаемое макетом.

![Исправление Лайауторигин](images/xaml-attached-layout-origincorrection.png)

### <a name="disconnected-viewports"></a>Отключенные окна просмотра

Размер, возвращаемый методом [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride) макета, представляет наилучшее предположение на размере содержимого, которое может измениться при каждом последовательном макете.  Так как пользователь прокручивает макет, он будет постоянно переоцениваться с помощью обновленного [реализатионрект](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect).

Если пользователь перетаскивает бегунок очень быстро, то его можно открыть с точки зрения макета, чтобы сделать большие переходы, где предыдущее положение не перекрывает текущую позицию.  Это обусловлено асинхронной природой прокрутки. Кроме того, приложение, использующее макет, может запросить отображение элемента в представлении для элемента, который в настоящее время не реализован, и будет считаться, что он находится вне текущего диапазона, отслеживающегося макетом.

Если макет обнаруживает, что его предполагаемое положение неверна, и/или видит непредвиденную смену окна просмотра, ему нужно переориентировать его начальную положение.  Макеты виртуализации, которые поставляются как часть элементов управления XAML, разрабатываются как макеты, зависящие от содержимого, так как они размещают меньше ограничений на характер содержимого, которое будет отображаться.


### <a name="example-simple-virtualizing-stack-layout-for-variable-sized-items"></a>Пример. простой макет стека виртуализации для элементов с изменяемыми размерами

В примере ниже показан простой макет стека для элементов с переменным размером, которые:

* поддерживает виртуализацию пользовательского интерфейса,
* использует оценки для подбора размера нереализованных элементов,
* осведомлен о возможностях непостоянного окна просмотра.
* применяет исправления макета для учета этих смен.

**Использование: разметка**

```xaml
<ScrollViewer>

  <ItemsRepeater x:Name="repeater" >
    <ItemsRepeater.Layout>

      <local:VirtualizingStackLayout />

    </ItemsRepeater.Layout>
    <ItemsRepeater.ItemTemplate>
      <DataTemplate x:Key="item">
        <UserControl IsTabStop="True" UseSystemFocusVisuals="True" Margin="5">
          <StackPanel BorderThickness="1" Background="LightGray" Margin="5">
            <Image x:Name="recipeImage" Source="{Binding ImageUri}"  Width="100" Height="100"/>
              <TextBlock x:Name="recipeDescription"
                         Text="{Binding Description}"
                         TextWrapping="Wrap"
                         Margin="10" />
          </StackPanel>
        </UserControl>
      </DataTemplate>
    </ItemsRepeater.ItemTemplate>
  </ItemsRepeater>

</ScrollViewer>
```

**Код поддержки: Main.cs**

```csharp
string _lorem = @"Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam laoreet erat vel massa rutrum, eget mollis massa vulputate. Vivamus semper augue leo, eget faucibus nulla mattis nec. Donec scelerisque lacus at dui ultricies, eget auctor ipsum placerat. Integer aliquet libero sed nisi eleifend, nec rutrum arcu lacinia. Sed a sem et ante gravida congue sit amet ut augue. Donec quis pellentesque urna, non finibus metus. Proin sed ornare tellus. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam laoreet erat vel massa rutrum, eget mollis massa vulputate. Vivamus semper augue leo, eget faucibus nulla mattis nec. Donec scelerisque lacus at dui ultricies, eget auctor ipsum placerat. Integer aliquet libero sed nisi eleifend, nec rutrum arcu lacinia. Sed a sem et ante gravida congue sit amet ut augue. Donec quis pellentesque urna, non finibus metus. Proin sed ornare tellus.";

var rnd = new Random();
var data = new ObservableCollection<Recipe>(Enumerable.Range(0, 300).Select(k =>
               new Recipe
               {
                   ImageUri = new Uri(string.Format("ms-appx:///Images/recipe{0}.png", k % 8 + 1)),
                   Description = k + " - " + _lorem.Substring(0, rnd.Next(50, 350))
               }));

repeater.ItemsSource = data;
```

**Код: VirtualizingStackLayout.cs**

```csharp
// This is a sample layout that stacks elements one after
// the other where each item can be of variable height. This is
// also a virtualizing layout - we measure and arrange only elements
// that are in the viewport. Not measuring/arranging all elements means
// that we do not have the complete picture and need to estimate sometimes.
// For example the size of the layout (extent) is an estimation based on the
// average heights we have seen so far. Also, if you drag the mouse thumb
// and yank it quickly, then we estimate what goes in the new viewport.

// The layout caches the bounds of everything that are in the current viewport.
// During measure, we might get a suggested anchor (or start index), we use that
// index to start and layout the rest of the items in the viewport relative to that
// index. Note that since we are estimating, we can end up with negative origin when
// the viewport is somewhere in the middle of the extent. This is achieved by setting the
// LayoutOrigin property on the context. Once this is set, future viewport will account
// for the origin.
public class VirtualizingStackLayout : VirtualizingLayout
{
    // Estimation state
    List<double> m_estimationBuffer = Enumerable.Repeat(0d, 100).ToList();
    int m_numItemsUsedForEstimation = 0;
    double m_totalHeightForEstimation = 0;

    // State to keep track of realized bounds
    int m_firstRealizedDataIndex = 0;
    List<Rect> m_realizedElementBounds = new List<Rect>();

    Rect m_lastExtent = new Rect();

    protected override Size MeasureOverride(VirtualizingLayoutContext context, Size availableSize)
    {
        var viewport = context.RealizationRect;
        DebugTrace("MeasureOverride: Viewport " + viewport);

        // Remove bounds for elements that are now outside the viewport.
        // Proactive recycling elements means we can reuse it during this measure pass again.
        RemoveCachedBoundsOutsideViewport(viewport);

        // Find the index of the element to start laying out from - the anchor
        int startIndex = GetStartIndex(context, availableSize);

        // Measure and layout elements starting from the start index, forward and backward.
        Generate(context, availableSize, startIndex, forward:true);
        Generate(context, availableSize, startIndex, forward:false);

        // Estimate the extent size. Note that this can have a non 0 origin.
        m_lastExtent = EstimateExtent(context, availableSize);
        context.LayoutOrigin = new Point(m_lastExtent.X, m_lastExtent.Y);
        return new Size(m_lastExtent.Width, m_lastExtent.Height);
    }

    protected override Size ArrangeOverride(VirtualizingLayoutContext context, Size finalSize)
    {
        DebugTrace("ArrangeOverride: Viewport" + context.RealizationRect);
        for (int realizationIndex = 0; realizationIndex < m_realizedElementBounds.Count; realizationIndex++)
        {
            int currentDataIndex = m_firstRealizedDataIndex + realizationIndex;
            DebugTrace("Arranging " + currentDataIndex);

            // Arrange the child. If any alignment needs to be done, it
            // can be done here.
            var child = context.GetOrCreateElementAt(currentDataIndex);
            var arrangeBounds = m_realizedElementBounds[realizationIndex];
            arrangeBounds.X -= m_lastExtent.X;
            arrangeBounds.Y -= m_lastExtent.Y;
            child.Arrange(arrangeBounds);
        }

        return finalSize;
    }

    // The data collection has changed, since we are maintaining the bounds of elements
    // in the viewport, we will update the list to account for the collection change.
    protected override void OnItemsChangedCore(VirtualizingLayoutContext context, object source, NotifyCollectionChangedEventArgs args)
    {
        InvalidateMeasure();
        if (m_realizedElementBounds.Count > 0)
        {
            switch (args.Action)
            {
                case NotifyCollectionChangedAction.Add:
                    OnItemsAdded(args.NewStartingIndex, args.NewItems.Count);
                    break;
                case NotifyCollectionChangedAction.Replace:
                    OnItemsRemoved(args.OldStartingIndex, args.OldItems.Count);
                    OnItemsAdded(args.NewStartingIndex, args.NewItems.Count);
                    break;
                case NotifyCollectionChangedAction.Remove:
                    OnItemsRemoved(args.OldStartingIndex, args.OldItems.Count);
                    break;
                case NotifyCollectionChangedAction.Reset:
                    m_realizedElementBounds.Clear();
                    m_firstRealizedDataIndex = 0;
                    break;
                default:
                    throw new NotImplementedException();
            }
        }
    }

    // Figure out which index to use as the anchor and start laying out around it.
    private int GetStartIndex(VirtualizingLayoutContext context, Size availableSize)
    {
        int startDataIndex = -1;
        var recommendedAnchorIndex = context.RecommendedAnchorIndex;
        bool isSuggestedAnchorValid = recommendedAnchorIndex != -1;

        if (isSuggestedAnchorValid)
        {
            if (IsRealized(recommendedAnchorIndex))
            {
                startDataIndex = recommendedAnchorIndex;
            }
            else
            {
                ClearRealizedRange();
                startDataIndex = recommendedAnchorIndex;
            }
        }
        else
        {
            // Find the first realized element that is visible in the viewport.
            startDataIndex = GetFirstRealizedDataIndexInViewport(context.RealizationRect);
            if (startDataIndex < 0)
            {
                startDataIndex = EstimateIndexForViewport(context.RealizationRect, context.ItemCount);
                ClearRealizedRange();
            }
        }

        // We have an anchorIndex, realize and measure it and
        // figure out its bounds.
        if (startDataIndex != -1 & context.ItemCount > 0)
        {
            if (m_realizedElementBounds.Count == 0)
            {
                m_firstRealizedDataIndex = startDataIndex;
            }

            var newAnchor = EnsureRealized(startDataIndex);
            DebugTrace("Measuring start index " + startDataIndex);
            var desiredSize = MeasureElement(context, startDataIndex, availableSize);

            var bounds = new Rect(
                0,
                newAnchor ?
                    (m_totalHeightForEstimation / m_numItemsUsedForEstimation) * startDataIndex : GetCachedBoundsForDataIndex(startDataIndex).Y,
                availableSize.Width,
                desiredSize.Height);
            SetCachedBoundsForDataIndex(startDataIndex, bounds);
        }

        return startDataIndex;
    }


    private void Generate(VirtualizingLayoutContext context, Size availableSize, int anchorDataIndex, bool forward)
    {
        // Generate forward or backward from anchorIndex until we hit the end of the viewport
        int step = forward ? 1 : -1;
        int previousDataIndex = anchorDataIndex;
        int currentDataIndex = previousDataIndex + step;
        var viewport = context.RealizationRect;
        while (IsDataIndexValid(currentDataIndex, context.ItemCount) &&
            ShouldContinueFillingUpSpace(previousDataIndex, forward, viewport))
        {
            EnsureRealized(currentDataIndex);
            DebugTrace("Measuring " + currentDataIndex);
            var desiredSize = MeasureElement(context, currentDataIndex, availableSize);
            var previousBounds = GetCachedBoundsForDataIndex(previousDataIndex);
            Rect currentBounds = new Rect(0,
                                          forward ? previousBounds.Y + previousBounds.Height : previousBounds.Y - desiredSize.Height,
                                          availableSize.Width,
                                          desiredSize.Height);
            SetCachedBoundsForDataIndex(currentDataIndex, currentBounds);
            previousDataIndex = currentDataIndex;
            currentDataIndex += step;
        }
    }

    // Remove bounds that are outside the viewport, leaving one extra since our
    // generate stops after generating one extra to know that we are outside the
    // viewport.
    private void RemoveCachedBoundsOutsideViewport(Rect viewport)
    {
        int firstRealizedIndexInViewport = 0;
        while (firstRealizedIndexInViewport < m_realizedElementBounds.Count &&
               !Intersects(m_realizedElementBounds[firstRealizedIndexInViewport], viewport))
        {
            firstRealizedIndexInViewport++;
        }

        int lastRealizedIndexInViewport = m_realizedElementBounds.Count - 1;
        while (lastRealizedIndexInViewport >= 0 &&
            !Intersects(m_realizedElementBounds[lastRealizedIndexInViewport], viewport))
        {
            lastRealizedIndexInViewport--;
        }

        if (firstRealizedIndexInViewport > 0)
        {
            m_firstRealizedDataIndex += firstRealizedIndexInViewport;
            m_realizedElementBounds.RemoveRange(0, firstRealizedIndexInViewport);
        }

        if (lastRealizedIndexInViewport >= 0 && lastRealizedIndexInViewport < m_realizedElementBounds.Count - 2)
        {
            m_realizedElementBounds.RemoveRange(lastRealizedIndexInViewport + 2, m_realizedElementBounds.Count - lastRealizedIndexInViewport - 3);
        }
    }

    private bool Intersects(Rect bounds, Rect viewport)
    {
        return !(bounds.Bottom < viewport.Top ||
            bounds.Top > viewport.Bottom);
    }

    private bool ShouldContinueFillingUpSpace(int dataIndex, bool forward, Rect viewport)
    {
        var bounds = GetCachedBoundsForDataIndex(dataIndex);
        return forward ?
            bounds.Y < viewport.Bottom :
            bounds.Y > viewport.Top;
    }

    private bool IsDataIndexValid(int currentDataIndex, int itemCount)
    {
        return currentDataIndex >= 0 && currentDataIndex < itemCount;
    }

    private int EstimateIndexForViewport(Rect viewport, int dataCount)
    {
        double averageHeight = m_totalHeightForEstimation / m_numItemsUsedForEstimation;
        int estimatedIndex = (int)(viewport.Top / averageHeight);
        // clamp to an index within the collection
        estimatedIndex = Math.Max(0, Math.Min(estimatedIndex, dataCount));
        return estimatedIndex;
    }

    private int GetFirstRealizedDataIndexInViewport(Rect viewport)
    {
        int index = -1;
        if (m_realizedElementBounds.Count > 0)
        {
            for (int i = 0; i < m_realizedElementBounds.Count; i++)
            {
                if (m_realizedElementBounds[i].Y < viewport.Bottom &&
                   m_realizedElementBounds[i].Bottom > viewport.Top)
                {
                    index = m_firstRealizedDataIndex + i;
                    break;
                }
            }
        }

        return index;
    }

    private Size MeasureElement(VirtualizingLayoutContext context, int index, Size availableSize)
    {
        var child = context.GetOrCreateElementAt(index);
        child.Measure(availableSize);

        int estimationBufferIndex = index % m_estimationBuffer.Count;
        bool alreadyMeasured = m_estimationBuffer[estimationBufferIndex] != 0;
        if (!alreadyMeasured)
        {
            m_numItemsUsedForEstimation++;
        }

        m_totalHeightForEstimation -= m_estimationBuffer[estimationBufferIndex];
        m_totalHeightForEstimation += child.DesiredSize.Height;
        m_estimationBuffer[estimationBufferIndex] = child.DesiredSize.Height;

        return child.DesiredSize;
    }

    private bool EnsureRealized(int dataIndex)
    {
        if (!IsRealized(dataIndex))
        {
            int realizationIndex = RealizationIndex(dataIndex);
            Debug.Assert(dataIndex == m_firstRealizedDataIndex - 1 ||
                dataIndex == m_firstRealizedDataIndex + m_realizedElementBounds.Count ||
                m_realizedElementBounds.Count == 0);

            if (realizationIndex == -1)
            {
                m_realizedElementBounds.Insert(0, new Rect());
            }
            else
            {
                m_realizedElementBounds.Add(new Rect());
            }

            if (m_firstRealizedDataIndex > dataIndex)
            {
                m_firstRealizedDataIndex = dataIndex;
            }

            return true;
        }

        return false;
    }

    // Figure out the extent of the layout by getting the number of items remaining
    // above and below the realized elements and getting an estimation based on
    // average item heights seen so far.
    private Rect EstimateExtent(VirtualizingLayoutContext context, Size availableSize)
    {
        double averageHeight = m_totalHeightForEstimation / m_numItemsUsedForEstimation;

        Rect extent = new Rect(0, 0, availableSize.Width, context.ItemCount * averageHeight);

        if (context.ItemCount > 0 && m_realizedElementBounds.Count > 0)
        {
            extent.Y = m_firstRealizedDataIndex == 0 ?
                            m_realizedElementBounds[0].Y :
                            m_realizedElementBounds[0].Y - (m_firstRealizedDataIndex - 1) * averageHeight;

            int lastRealizedIndex = m_firstRealizedDataIndex + m_realizedElementBounds.Count;
            if (lastRealizedIndex == context.ItemCount - 1)
            {
                var lastBounds = m_realizedElementBounds[m_realizedElementBounds.Count - 1];
                extent.Y = lastBounds.Bottom;
            }
            else
            {
                var lastBounds = m_realizedElementBounds[m_realizedElementBounds.Count - 1];
                int lastRealizedDataIndex = m_firstRealizedDataIndex + m_realizedElementBounds.Count;
                int numItemsAfterLastRealizedIndex = context.ItemCount - lastRealizedDataIndex;
                extent.Height = lastBounds.Bottom + numItemsAfterLastRealizedIndex * averageHeight - extent.Y;
            }
        }

        DebugTrace("Extent " + extent + " with average height " + averageHeight);
        return extent;
    }

    private bool IsRealized(int dataIndex)
    {
        int realizationIndex = dataIndex - m_firstRealizedDataIndex;
        return realizationIndex >= 0 && realizationIndex < m_realizedElementBounds.Count;
    }

    // Index in the m_realizedElementBounds collection
    private int RealizationIndex(int dataIndex)
    {
        return dataIndex - m_firstRealizedDataIndex;
    }

    private void OnItemsAdded(int index, int count)
    {
        // Using the old indexes here (before it was updated by the collection change)
        // if the insert data index is between the first and last realized data index, we need
        // to insert items.
        int lastRealizedDataIndex = m_firstRealizedDataIndex + m_realizedElementBounds.Count - 1;
        int newStartingIndex = index;
        if (newStartingIndex > m_firstRealizedDataIndex &&
            newStartingIndex <= lastRealizedDataIndex)
        {
            // Inserted within the realized range
            int insertRangeStartIndex = newStartingIndex - m_firstRealizedDataIndex;
            for (int i = 0; i < count; i++)
            {
                // Insert null (sentinel) here instead of an element, that way we do not
                // end up creating a lot of elements only to be thrown out in the next layout.
                int insertRangeIndex = insertRangeStartIndex + i;
                int dataIndex = newStartingIndex + i;
                // This is to keep the contiguousness of the mapping
                m_realizedElementBounds.Insert(insertRangeIndex, new Rect());
            }
        }
        else if (index <= m_firstRealizedDataIndex)
        {
            // Items were inserted before the realized range.
            // We need to update m_firstRealizedDataIndex;
            m_firstRealizedDataIndex += count;
        }
    }

    private void OnItemsRemoved(int index, int count)
    {
        int lastRealizedDataIndex = m_firstRealizedDataIndex + m_realizedElementBounds.Count - 1;
        int startIndex = Math.Max(m_firstRealizedDataIndex, index);
        int endIndex = Math.Min(lastRealizedDataIndex, index + count - 1);
        bool removeAffectsFirstRealizedDataIndex = (index <= m_firstRealizedDataIndex);

        if (endIndex >= startIndex)
        {
            ClearRealizedRange(RealizationIndex(startIndex), endIndex - startIndex + 1);
        }

        if (removeAffectsFirstRealizedDataIndex &&
            m_firstRealizedDataIndex != -1)
        {
            m_firstRealizedDataIndex -= count;
        }
    }

    private void ClearRealizedRange(int startRealizedIndex, int count)
    {
        m_realizedElementBounds.RemoveRange(startRealizedIndex, count);
        if (startRealizedIndex == 0)
        {
            m_firstRealizedDataIndex = m_realizedElementBounds.Count == 0 ? 0 : m_firstRealizedDataIndex + count;
        }
    }

    private void ClearRealizedRange()
    {
        m_realizedElementBounds.Clear();
        m_firstRealizedDataIndex = 0;
    }

    private Rect GetCachedBoundsForDataIndex(int dataIndex)
    {
        return m_realizedElementBounds[RealizationIndex(dataIndex)];
    }

    private void SetCachedBoundsForDataIndex(int dataIndex, Rect bounds)
    {
        m_realizedElementBounds[RealizationIndex(dataIndex)] = bounds;
    }

    private Rect GetCachedBoundsForRealizationIndex(int relativeIndex)
    {
        return m_realizedElementBounds[relativeIndex];
    }

    void DebugTrace(string message, params object[] args)
    {
        Debug.WriteLine(message, args);
    }
}
```

## <a name="related-articles"></a>Связанные статьи

- [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)
- [ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)
