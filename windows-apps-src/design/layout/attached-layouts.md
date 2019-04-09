---
Description: Можно определить вложенный макетов для использования с контейнерами, например элемент управления ItemsRepeater.
title: AttachedLayout
label: AttachedLayout
template: detail.hbs
ms.date: 03/13/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 6ff73b13acb5f5970bb79755b0bf5706fb12545a
ms.sourcegitcommit: c10d7843ccacb8529cb1f53948ee0077298a886d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58913994"
---
# <a name="attached-layouts"></a>Вложенные макеты

Контейнер (например, панель), который делегирует его логику макета в другой объект зависит от объекта присоединенного макета для обеспечения поведения макета для дочерних элементов.  Модель макета присоединенного обеспечивает гибкость для приложения, изменять макет элементов во время выполнения, или более легко обмениваться аспекты макета между различными частями интерфейса пользователя (например, элементы в строках таблицы, которые могут быть выровнены в столбце).

В этом разделе мы рассмотрим, что именно задействуется при создании присоединенного макетом (виртуализации и без виртуализации), основные понятия и классы, которые вы должны понимать, и компромиссы необходимо учитывать при выборе между ними.

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| Этот элемент управления входит в состав библиотеки пользовательского интерфейса Windows, пакет NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений универсальной платформы Windows. Дополнительные сведения, включая инструкции по установке, см. в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **Важные API**:

> * [ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)
> * [ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater)
> * [Макет](/uwp/api/microsoft.ui.xaml.controls.layout)
>     * [NonVirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout)
>     * [VirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout)
> * [LayoutContext](/uwp/api/microsoft.ui.xaml.controls.layoutcontext)
>     * [NonVirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext)
>     * [VirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext)
> * [LayoutPanel](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) (Предварительная версия)

## <a name="key-concepts"></a>Основные понятия

Выполнение макет требует, что два ответить на вопросы, для каждого элемента:

1. Что ***размер*** будет этот элемент?

2. Что будет ***позиции*** данного элемента быть?

Система макета в XAML, который отвечает на эти вопросы, кратко рассматривается как часть обсуждение [пользовательских панелей](/windows/uwp/design/layout/custom-panels-overview).

### <a name="containers-and-context"></a>Контейнеры и контекстом

По существу, XAML [панели](/uwp/api/windows.ui.xaml.controls.panel) заполняет два важных ролей в framework:

1. Он может содержать дочерние элементы и вводит ветвления в дереве элементов.
2. Он применяется стратегию определенный макет для этих дочерних действий.

По этой причине панель в XAML, зачастую было синонимом макета, но технически говоря, выполняет больше, чем просто макет.

[ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater) также ведет себя подобно панель, но в отличие от панели, он не предоставляет свойством Children, которая позволила бы программное добавление и удаление дочерних элементов UIElement.  Вместо этого время существования его дочерних элементов автоматически управляются платформой для соответствуют коллекция элементов данных.  Несмотря на то, что он не является производным от панели, он ведет себя и обрабатываются платформой как панель.

> [!NOTE]
> [LayoutPanel](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) представляет собой контейнер, производный от панель, которая делегирует его логику для вложенного [макета](/uwp/api/microsoft.ui.xaml.controls.layoutpanel.layout) объекта.  LayoutPanel находится в *предварительной версии* и в настоящее время доступна только в *предварительный выпуск* удаляет WinUI пакета.

#### <a name="containers"></a>Контейнеры

По существу [панели](/uwp/api/windows.ui.xaml.controls.panel) — это контейнер элементов, которое также позволяет построить пикселей для [фона](/uwp/api/windows.ui.xaml.controls.panel.background).  Панели позволяют инкапсулировать общую логику макета в пакете прост в использовании.

Концепция **присоединенного макета** различий между двумя ролями контейнера и макет более четко.  Если контейнер делегирует его логику макета к другому объекту мы вызовем этот объект присоединенного макета как показано в приведенном ниже фрагменте кода. Контейнеры, которые наследуют от [FrameworkElement](/uwp/api/windows.ui.xaml.frameworkelement), например LayoutPanel, автоматически предоставляется доступ к общим свойствам, предоставить входные данные для прохода разметки в XAML (например, высота и ширина).

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

Во время процесса макет контейнера зависит от вложенного *UniformGridLayout* для измерения и упорядочивания его дочерних элементов.

#### <a name="per-container-state"></a>Состояние каждого контейнера

С макетом присоединенного один экземпляр объекта макета может быть связано с *многих* контейнеры, как показано в приведенном ниже фрагменте кода; таким образом, он должен не зависят от или прямой ссылки на узел контейнера.  Пример:

```xaml
<!-- ... --->
<Page.Resources>
    <ExampleLayout x:Name="exampleLayout"/>
<Page.Resources>

<LayoutPanel x:Name="example1" Layout="{StaticResource exampleLayout}"/>
<LayoutPanel x:Name="example2" Layout="{StaticResource exampleLayout}"/>
<!-- ... --->
```

В этом случае *ExampleLayout* должны тщательно отслеживать состояние, чтобы избежать влияния на макет для элементов на одной панели с другими используется макет вычислений и хранения состояния.  Он будет работать аналогично на пользовательскую панель, логика которых MeasureOverride и ArrangeOverride зависит от значения его *статический* свойства.

#### <a name="layoutcontext"></a>LayoutContext

Цель [LayoutContext](/uwp/api/microsoft.ui.xaml.controls.layoutcontext) призвана решить эти сложности.  Вложенные структуры обеспечивается возможность взаимодействия с контейнером, таких как извлечение дочерние элементы, не внося прямую зависимость между двумя. Контекст также позволяет хранить любое необходимое состояние, могут быть связаны с дочерние элементы контейнера макета.

Макеты простой, отличных от виртуализации часто не нужно поддерживать любое состояние, делая не стало проблемой. Более сложные макеты, например сетки, однако может выбрать для поддержания состояния между измерения и упорядочения вызов, чтобы избежать повторного вычисления значения.

Виртуализация макеты *часто* должны поддерживать некоторые состояние между как меру и также упорядочить между этапов итеративный разметки.

#### <a name="initializing-and-uninitializing-per-container-state"></a>Состояние-Container инициализации и деинициализации

При присоединении макета в контейнер, его [InitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore) метод вызывается и предоставляет возможность инициализировать объект для сохранения состояния.

Аналогичным образом, если макет удаляется из контейнера, [UninitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore) метод будет вызываться.  Это дает возможность очищать любые состояния, он, связанный с этим контейнером макета.

Объект состояния макета можно хранить с и извлекается из контейнера с [LayoutState](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate) свойство контекста.

### <a name="ui-virtualization"></a>Виртуализация пользовательского интерфейса

Виртуализация пользовательского интерфейса означает, откладывая создание объект пользовательского интерфейса, пока не _при необходимости_.  Это способ оптимизации производительности.  Для сценариев, непрокручиваемых определение _при необходимости_ могут основываться на различные действия, которые зависят от конкретного приложения.  В таких случаях приложения следует рассмотреть возможность использования [x: Load](../../xaml-platform/x-load-attribute.md). Он не требует никакой специальной обработки в макете.

В ситуациях, основанных на прокрутку, например список определение _при необходимости_ часто зависит от «будет видимым для пользователя» во многом полагается на котором он был помещен в процессе разметки и требуются особые рекомендации.  Этот сценарий является задачей для этого документа.

> [!NOTE]
> Несмотря на то, что в этом документе не рассматривается, те же возможности, обеспечивающие виртуализацию пользовательского интерфейса в прокрутки сценариев может быть применена в сценариях без прокрутки.  Например элемент управляемой данными инструментов управления, управляет временем существования команды она представляет и реагирует на изменения в доступное пространство, повторное использование или перемещение элементов между видимой области и меню переполнения.

## <a name="getting-started"></a>начало работы

Во-первых определите, должен ли нужный вам нужно создать макет поддерживать виртуализацию пользовательского интерфейса.

**Несколько моментов, которые Имейте в виду...**

1. Виртуализация не макеты легче автора. Если количество элементов всегда будет небольшим рекомендуется затем создания макета, отличных от виртуализации.
2. Платформа предоставляет набор вложенных макетов, которые работают с [ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater#change-the-layout-of-items) и [LayoutPanel](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) для охвата распространенных задач.  Ознакомиться с тех, прежде чем решите, необходимо определить настраиваемый макет.
3. Макеты виртуализации всегда имеют некоторые дополнительная нагрузка на ЦП и памяти/сложности/затраты по сравнению с макета, отличных от виртуализации.  Как правило, если дочерние элементы макета должен управлять скорее всего помещается в области, 3 x размер окна просмотра, то может быть много прибыль на основе виртуализации макета. Размер в 3 раза рассматривается более подробно далее в этом документе, но происходит из-за асинхронного характера прокрутки по Windows и ее влияние на виртуализации.

> [!TIP]
> Как точку ссылки, параметры по умолчанию для [ListView](/uwp/api/windows.ui.xaml.controls.listview) (и [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)), что перезапуск не начинается, пока число элементов, которые будут достаточно, чтобы заполнить 3 раза больше текущей области просмотра.

**Выберите базовый тип**

![Иерархия вложенных макета](images/xaml-attached-layout-hierarchy.png)

Базовый [макета](/uwp/api/microsoft.ui.xaml.controls.layout) тип имеет два производных типов, которые служат в качестве начальной точки для создания вложенный план:

1. [NonVirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout)
2. [VirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout)

## <a name="non-virtualizing-layout"></a>Макет, отличных от виртуализации

Подход для создания макета, отличных от виртуализации, покажется вам знакомым для всех, кто создал [пользовательскую область](/windows/uwp/design/layout/custom-panels-overview).  Те же принципы применимы.  Основное различие заключается в, [NonVirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext) используется для доступа к [дочерние элементы](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext.children) коллекции и макет может выбрать для сохранения состояния.

1. Являются производными от базового типа [NonVirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout) (вместо панели).
2. *(Необязательно)*  Определять свойства зависимостей будет изменено, если к недействительности структуры.
3. _(**New**/Дополнительно)_ инициализации любого объекта состояния, требуется макет как часть [InitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore). Скройте его изменения с узла контейнера с помощью [LayoutState](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate) предоставляются с контекстом.
4. Переопределить [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout.measureoverride) и вызвать [мер](/uwp/api/windows.ui.xaml.uielement.measure) метод на все дочерние элементы.
5. Переопределить [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout.arrangeoverride) и вызвать [расположение](/uwp/api/windows.ui.xaml.uielement.arrange) метод на все дочерние элементы.
6. *(**New**/Дополнительно)* очистить любое сохраненное состояние как часть [UninitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore).

### <a name="example-a-simple-stack-layout-varying-sized-items"></a>Пример. Простой стеке (изменение размера элементов)

![MyStackLayout](images/xaml-attached-layout-mystacklayout.png)

Здесь — это очень простой-виртуализация стека макет различных размеров элементов. У него нет любые свойства, чтобы настроить поведение макета. Реализация ниже показано, как макет использует объект context, предоставляемыми контейнерами для:

1. Получение числа дочерних элементов, и
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

## <a name="virtualizing-layouts"></a>Виртуализация макеты

Как и к макету без виртуализации, общие шаги по виртуализации макета одинаковы.  Сложность является главным образом, при определении новые элементы попадут в области просмотра и должен быть реализован.

1. Являются производными от базового типа [VirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout).
2. (Необязательно) Определение свойств зависимостей будет изменено, если к недействительности структуры.
3. Любой объект состояния, который будет обязателен для макета как часть инициализации [InitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore). Скройте его изменения с узла контейнера с помощью [LayoutState](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate) предоставляются с контекстом.
4. Переопределить [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride) и вызвать [мер](/uwp/api/windows.ui.xaml.uielement.measure) метод для каждого дочернего, который должен быть реализован.
   1. [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) метод используется для извлечения UIElement, который был подготовлен платформой (например привязок данных применяется).
5. Переопределить [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride) и вызвать [расположение](/uwp/api/windows.ui.xaml.uielement.arrange) метод для каждого реализованного дочернего элемента.
6. (Необязательно) Очистить любое сохраненное состояние как часть [UninitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore).

> [!TIP]
> Значение, возвращенное [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout) используется в качестве размера виртуализированных содержимого.

Существует два принципиальных подхода, которые следует учитывать при создании макета виртуализации.  Следует выбирать один из этих во многом зависит от того, «как вы определит размер элемента».  Если его достаточно, чтобы узнать индекс элемента данных, так и набор данных определяет его конечный размер, то мы бы рассмотрим **зависящей от данных**.  Это упрощает создание.  Однако если, единственный способ определить размер элемента — это создание и мер пользовательского интерфейса, а затем было бы сказать **зависит от содержимого**.  Они являются более сложными.

### <a name="the-layout-process"></a>Процесс разметки

При создании данных или содержимого зависит от макета, важно понять процесс разметки и влияние асинхронной прокрутки Windows.

(Чрезмерно) упрощенное представление действий, выполняемых платформой от запуска для отображения на экране пользовательского интерфейса является то, что:

1. Выполняет синтаксический анализ разметки.

2. Создает дерево элементов.

3. Выполняет прохода макета.

4. Выполняет прохода отрисовки.

Виртуализация пользовательского интерфейса, создание элементов, которые обычно можно сделать на шаге 2 будет отложена или завершен раньше один раз его было определено, достаточный объем содержимого будет создана для заполнения окна просмотра. Виртуализирующий контейнер (например ItemsRepeater) делегирует его вложенные структуры для достижения этого процесса. Он предоставляет вложенное макет с [VirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext) , предоставляет дополнительную информацию, необходимую структуру виртуализации.

**RealizationRect (т. е. в окне просмотра)**

Прокрутки по Windows происходит асинхронный поток пользовательского интерфейса. Он не управляется макета платформы.  Вместо этого взаимодействия и перемещение происходит в компоновщике системы. Преимуществом такого подхода является то, что панорамирования содержимое всегда можно в 60 кадров/с.  Запрос, то, что «окна просмотра», как явствует макет, может быть немного устаревшей по отношению к фактически видимые на экране. Если пользователь выполняет прокрутку быстро, они могут превосходят скорости потока пользовательского интерфейса для создания нового содержимого и «сдвиг к черному». По этой причине часто бывает для виртуализации макета создать дополнительный буфер подготовленного элементов достаточно для заполнения области, размер которых превышает окна просмотра. Когда при более высокой нагрузке во время перемещения пользователя по-прежнему предоставляется с содержимым.

![Реализация rect](images/xaml-attached-layout-realizationrect.png)

С момента создания элемента является дорогостоящим, виртуализация контейнеры (например [ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater)) изначально предоставит присоединенного макет с [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) , соответствующий области просмотра. Во время бездействия контейнера может увеличиваться буфера подготовленного содержимого, обращаясь к макету, с помощью все большей параметра реализации rect. Это способ оптимизации производительности, который пытается установить баланс между временем быстрого запуска и панорамирования хорошие условия работы. Максимальный размер буфера, вызывающая ItemsRepeater управляется его [VerticalCacheLength](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.verticalcachelength) и [HorizontalCacheLength](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.verticalcachelength) свойства.

**Повторное использование элементов (перезапуск)**

Ожидается макет, размер и положение элементов для заполнения [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) каждый раз, он выполняется. По умолчанию [VirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout) очищают все неиспользуемые элементы в конце каждого прохода макета.

[VirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext) , передаваемый в макет как часть [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride) и [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride) представлены дополнительные сведения должен виртуализации макета. Ниже приведены некоторые наиболее часто используемые задачи, которые он предоставляет возможность.

1. Запросить число элементов в данных ([ItemCount](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.itemcount)).
2. Извлечь определенный элемент с помощью [GetItemAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getitemat) метод.
3. Получить [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) , представляющий окно просмотра и понял, буфер, который следует заполнить макета элементов.
4. Запросить UIElement конкретный элемент с [GetOrCreateElement](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) метод.

Запрашивает элемент для заданного индекса вызовет этот элемент были помечены как «использующийся» для этого прохода макета. Если элемент еще не существует, затем он понял и автоматически подготавливаются для использования (например, подобных в дереве пользовательского интерфейса, определенные в DataTemplate, обработку любой привязки данных и т. д.).  В противном случае он извлекается из пула имеющихся экземпляров.

В конце каждого проход измерения, любые существующие, поняли, автоматически считается доступным для повторного использования элемента, который не был помечен как «в использовании» Если параметр [SuppressAutoRecycle](/uwp/api/microsoft.ui.xaml.controls.elementrealizationoptions) было использовано, когда элемент был извлечен с помощью [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) метод. Платформа автоматически переводит его повторный запуск пула и делает ее доступной. Он может впоследствии запрошены для использования с другим контейнером. Платформа framework пытается избежать этого по возможности, так как некоторые затраты, связанные с Переподчинение элемент.

Если знает структуру виртуализации, в начале каждого измерения, в которой элементы больше не попадут в реализации rect затем его можно оптимизировать его повторного использования. Вместо того чтобы полагаться на поведение по умолчанию платформы. Макет заблаговременно элементы можно перемещать в пул повторный запуск с помощью [RecycleElement](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recycleelement) метод.  Вызов этого метода, прежде чем запрашивать новые элементы приводит эти существующие элементы, для их в структуру позднее выдает [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) запроса для индекса, которая еще не связан с элементом.

VirtualizingLayoutContext предоставляет два дополнительных свойства, которые предназначены для авторов макета создание макета зависит от содержимого. Они обсуждаются более подробно позже.

1. Объект [RecommendedAnchorIndex](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) , предоставляющий необязательный _входной_ макет.
2. Объект [LayoutOrigin](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.layoutorigin) то есть необязательный _вывода_ макета.

## <a name="data-dependent-virtualizing-layouts"></a>Макеты виртуализации зависящей от данных

Виртуализации макет будет проще, если вы знаете, что размер каждого элемента должен быть без необходимости для измерения содержимое для отображения.  В этом документе мы просто вернусь к этой категории виртуализации макеты как **макетов данных** так, как они обычно включают инспектирования данных.  На основе данных, приложение может возможно выберите визуальное представление с размером известных -, так как его часть данных или ранее определенный с помощью конструктора.

Общий подход заключается в том, макет:

1. Вычислите размер и положение каждого элемента.
2. Как часть [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride):
   1. Используйте [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) для определения, какие элементы должны появляться в области просмотра.
   2. Получить UIElement, которое должно представлять элемент с [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) метод.
   3. [Мера](/uwp/api/windows.ui.xaml.uielement.measure) UIElement с предварительно вычисленными размером.
3. Как часть [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride), [расположение](/uwp/api/windows.ui.xaml.uielement.arrange) каждого понял UIElement с предварительно вычисленными положением.

> [!NOTE]
> Часто подхода к макета данных несовместим с _виртуализации данных_.  В частности, где только данные загружены в память — эти данные, необходимые для заполнения, видимые пользователю.  Виртуализации данных не ссылка на отложенную или добавочной загрузки данных, как пользователь прокручивает где останется этих данных.  Вместо этого он ссылается на при элементы освобождаются из памяти, когда они выполняется вне области просмотра.  Хранение данных макета, который проверяет каждый элемент данных как часть макета данных могли бы помешать виртуализации данных из рабочего должным образом.  Исключение — это макет как UniformGridLayout, где предполагается, что все, что имеет тот же размер.

> [!TIP]
> При создании пользовательского элемента управления для библиотеки элементов управления, который будет использоваться другими пользователями в самых разнообразных ситуациях структура данных не может быть для вас.

### <a name="example-xbox-activity-feed-layout"></a>Пример. Канал активности Xbox макета

Пользовательский Интерфейс для веб-канала активности Xbox использует повторяющийся шаблон, где каждая строка имеет широкая плитка, следуют две узкий плитки, которые обращена последующие строки. В этот макет размер для каждого элемента зависит от позиции элемента в наборе данных и известных размер плитки (расширенный vs сужение области).

![Веб-канале активности Xbox](images/xaml-attached-layout-activityfeedscreenshot.png)

Код ниже описан какие пользовательские виртуализации пользовательского интерфейса для веб-канале активности может быть для иллюстрации общих подход вы, может занять **макет данных**.

<table>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы открыть приложение и просмотреть <a href="xamlcontrolsgallery:/item/ItemsRepeater">ItemsRepeater</a> в действии с помощью этого примера макета.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
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

### <a name="optional-managing-the-item-to-uielement-mapping"></a>(Необязательно) Управление элемента UIElement сопоставления

По умолчанию [VirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext) поддерживает сопоставление с реализованных элементов, а индекс в источнике данных, они представляют.  Можно выбрать макет для управления, это сопоставление сам запросив возможность всегда [SuppressAutoRecycle](/uwp/api/microsoft.ui.xaml.controls.elementrealizationoptions) при извлечении элемента посредством [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) метод, который не позволяет по умолчанию повторное использование автоматическое поведение.  Макет, можно сделать, например, если он будет использоваться только при прокрутке будет ограничен в одном направлении и элементы, которые он считает, что всегда будут последовательными (т. е. в том, что индекс первого и последнего элемента достаточно знать все элементы, которые должны быть rea lized).

#### <a name="example-xbox-activity-feed-measure"></a>Пример. Канал активности Xbox мер

В следующем фрагменте показано дополнительной логики, который может быть добавлен к MeasureOverride в предыдущем примере для управления сопоставление.

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

## <a name="content-dependent-virtualizing-layouts"></a>Содержимое зависит от виртуализации макеты

Если необходимо сначала выполнить измерения содержимого пользовательского интерфейса для элемента выяснить его точный размер, то это **содержимое зависит от макета**.  Можно также считать его макета, где каждый элемент необходимо изменять размер, а не элемент о том, его размер макета. Виртуализации макеты, которые попадают в эту категорию, более сложны.

> [!NOTE]
> Не зависит от содержимого макеты должно нарушать работу виртуализации данных.

### <a name="estimations"></a>Оценки

Макеты зависит от содержимого зависит от оценки угадать размером нереализованных содержимого и положение реализованных содержимого. Как изменить эти оценки произойдет реализованных содержимого необходимо регулярно сдвинуть должностей в прокручиваемой области. Это может привести к очень раздражает и резать глаз взаимодействие с пользователем в противном случае устранен. Здесь рассматриваются возможные проблемы и способы их устранения.

> [!NOTE]
> Макеты данных, рассмотрим каждый элемент и знать точный размер всех элементов, реализованные или нет, а их положения можно полностью избежать этих проблем.

**Привязка прокрутки**

XAML предоставляет механизм для устранения смены внезапного окна просмотра, задав прокрутка поддержки элементов управления [привязка прокрутки](/uwp/api/windows.ui.xaml.controls.iscrollanchorprovider) путем реализации [IScrollAnchorPovider](/uwp/api/windows.ui.xaml.controls.iscrollanchorprovider) интерфейс. Как пользователь управляет содержимое, прокрутки элемента управления постоянно выбирает элемент из набора кандидатов, которые были исключены в которые необходимо отслеживать. Если позиция элемента привязки сдвигает во время структурирования прокрутки автоматически переносится его области просмотра для поддержания окна просмотра.

Значение [RecommendedAnchorIndex](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) для макета может отражать прокрутки управления привязки текущего выбранного элемента. Кроме того Если разработчик явно запрашивает, что элемент реализовать для индекса с [GetOrCreateElement](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.getorcreateelement) метод [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), то этот индекс задается как [ RecommendedAnchorIndex](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) на следующего прохода макета. Это позволяет макета для подготовки вероятный сценарий, что разработчик понимает, что элемент и последовательный запрос, что он принесено в представление с помощью [StartBringIntoView](/uwp/api/windows.ui.xaml.uielement.startbringintoview) метод.

[RecommendedAnchorIndex](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) — это индекс элемента в источнике данных, макета зависит от содержимого следует располагать во-первых, при оценке положение его элементов. Он должен служить отправной точкой для позиционирования других реализованных элементов.

**Влияние на полосы прокрутки**

Даже при наличии привязку прокрутки, если макет оценки различаются, возможно из-за существенное различие в размер содержимого, а затем положение ползунка полосы прокрутки для полосы прокрутки может выполняться переход вокруг.  Это может быть резать глаз пользователю, если бегунка не отображается для отслеживания положение указателя мыши, когда они перетаскивания его.

Более точные макета может находиться в ее оценок, а затем тем меньше вероятность пользователем увидите переход бегунка полосы прокрутки.

### <a name="layout-corrections"></a>Изменения макета

Макет зависит от содержимого должны быть готовы рационализировать его оценку с реальностью.  Например во время прокрутки к верхней части содержимого и макета понимает, что первый элемент, он может оказаться, что ожидаемые положение элемента относительно элемента, из которого запущен приводило к его присутствовать где-нибудь, отличный от начала координат (x: 0 y:0). В таких случаях можно использовать макет [LayoutOrigin](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.layoutorigin) свойство, чтобы установить позицию, оно вычисляется как новый источник макета.  Конечный результат аналогичен прокрутите привязки, в котором окно просмотра прокрутки элемента управления изменяется автоматически для учетной записи для позиции содержимого, предоставленное макета.

![Исправление LayoutOrigin](images/xaml-attached-layout-origincorrection.png)

### <a name="disconnected-viewports"></a>Отключенный Viewports

Возвращенный размер в макете [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride) метод представляет наиболее подходящие значения размера содержимого, что может повлиять на каждый последовательных макет.  Как пользователь прокручивает макет будет постоянно повторного вычисления с обновленным [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect).

Если пользователь перетаскивает бегунок очень быстро возможных для окна просмотра, с точки зрения макета, будут делать большего размера переходит, где предыдущей позиции не пересекается теперь текущей позиции.  Это из-за асинхронного характера прокрутки. Можно также для приложения, использующего макета для запроса, что элемент принесено в представление элемента, который в данный момент не реализован и оценивается для размещения за пределами текущего диапазона, отслеживаются макета.

Когда макет обнаруживает его предположение неверно и (или) видит непредвиденный просмотра shift, ему необходимо переориентировать его начальной позиции.  Виртуализации макетов, входящих в состав элементов управления XAML разрабатываются в виде макеты зависит от содержимого, так как она накладывает меньше ограничений на природу содержимого, которое будет отображаться.


### <a name="example-simple-virtualizing-stack-layout-for-variable-sized-items"></a>Пример. Простой макет стека виртуализации для переменного размера элементов

Следующий пример демонстрирует простой стек макета для переменного размера элементов, которое:

* поддерживает виртуализацию пользовательского интерфейса
* использует оценок, чтобы подобрать размер нереализованных элементов
* учитывает потенциальные смен отдельные окна просмотра, и
* применяет исправления макета для учета этих смены.

**Использование: Разметка**

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

**Фоновый код: Main.cs**

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
