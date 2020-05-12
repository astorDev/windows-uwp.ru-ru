---
Description: Вы можете определить подключенные макеты, которые следует использовать с контейнерами, такими как элемент управления ItemsRepeater.
title: AttachedLayout
label: AttachedLayout
template: detail.hbs
ms.date: 03/13/2019
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9ee88e32ed0ce0fd193fe79e48814a11f494d062
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970219"
---
# <a name="attached-layouts"></a>Подключенные макеты

Контейнер (например, Panel), который делегирует свою логику макета другому объекту, использует подключенный объект макета, чтобы предоставить поведение макета для своих дочерних элементов.  Модель подключенного макета делает приложение гибким в отношении изменения макета элементов во время выполнения, или упрощает совместное использование пропорций макета между различными частями пользовательского интерфейса (например, элементами в строках таблицы, которые следует согласовать в столбце).

В этом разделе мы расскажем о том, что происходит при создании подключенного макета (с виртуализацией и без нее), о концепциях и классах, которые необходимо понимать, и альтернативах, которые следует учитывать при выборе между ними.

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| Этот элемент управления является частью библиотеки пользовательского интерфейса Windows — пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений для Windows. Дополнительные сведения, включая инструкции по установке, см. в [обзорной статье о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **Важные API**:

> * [ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)
> * [ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater)
> * [Макет](/uwp/api/microsoft.ui.xaml.controls.layout)
>     * [NonVirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout)
>     * [VirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout)
> * [LayoutContext](/uwp/api/microsoft.ui.xaml.controls.layoutcontext)
>     * [NonVirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext)
>     * [VirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext)
> * [LayoutPanel](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) (ознакомительная версия)

## <a name="key-concepts"></a>Основные понятия

Чтобы выполнить макет, для каждого элемента следует ответить на два вопроса:

1. Какого ***размера*** будет этот элемент?

2. Что будет ***позицией*** этого элемента?

Система макетов XAML, которая отвечает на эти вопросы, кратко рассматривается как часть обсуждения в статье [Обзор пользовательских панелей XAML](/windows/uwp/design/layout/custom-panels-overview).

### <a name="containers-and-context"></a>Контейнеры и контекст

По сути, [Panel](/uwp/api/windows.ui.xaml.controls.panel) XAML имеет две важные роли на платформе:

1. Этот объект может содержать дочерние элементы и представляет ветвление в дереве элементов.
2. Он применяет определенную стратегию макета к этим дочерним элементам.

По этой причине Panel в XAML часто является синонимом макета, однако, выражаясь в технических терминах, он делает больше, чем макет.

[ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater) также ведет себя как Panel, но, в отличие от Panel, не предоставляет свойство Children, которое позволило бы программно добавлять или удалять дочерние элементы UIElement.  Вместо этого время существования его дочерних элементов автоматически управляется платформой для соответствия коллекции элементов данных.  Хотя этот объект не является производным от Panel, он ведет себя и рассматривается платформой как Panel.

> [!NOTE]
> [LayoutPanel](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) — это контейнер, производный от Panel, который делегирует свою логику вложенному объекту [Layout](/uwp/api/microsoft.ui.xaml.controls.layoutpanel.layout).  LayoutPanel сейчас в *ознакомительной версии* и в настоящее время доступен только в *предварительном выпуске* пакета WinUI.

#### <a name="containers"></a>Контейнеры

По сути, [Panel](/uwp/api/windows.ui.xaml.controls.panel) — это контейнер элементов, которые также могут преобразовывать пиксели для [фона](/uwp/api/windows.ui.xaml.controls.panel.background).  Они предоставляют способ инкапсуляции общей логики макета в простой в использовании пакет.

Концепция **подключенного макета** делает различие между двумя ролями контейнера и макета более четким.  Если контейнер делегирует свою логику макета другому объекту, мы вызываем этот объект в подключенный макет, как показано в приведенном ниже фрагменте кода. Контейнеры, которые наследуют от [FrameworkElement](/uwp/api/windows.ui.xaml.frameworkelement), например LayoutPanel, автоматически обеспечивают общие свойства, которые предоставляют входные данные процессу макета XAML (например, высоту и ширину).

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

В процессе макета для измерения и упорядочивания дочерних элементов контейнер полагается на подключенный *UniformGridLayout*.

#### <a name="per-container-state"></a>Состояние на контейнер

При наличии подключенного макета один экземпляр объекта макета может быть связан с *многими* контейнерами, как показано в фрагменте кода ниже. Поэтому он не должен зависеть от контейнера узла или напрямую ссылаться на него.  Например:

```xaml
<!-- ... --->
<Page.Resources>
    <ExampleLayout x:Name="exampleLayout"/>
<Page.Resources>

<LayoutPanel x:Name="example1" Layout="{StaticResource exampleLayout}"/>
<LayoutPanel x:Name="example2" Layout="{StaticResource exampleLayout}"/>
<!-- ... --->
```

В этой ситуации *ExampleLayout* следует тщательно учитывать состояние, которое он использует в расчете макета, а также место его хранения, чтобы избежать воздействия на макет для элементов на одной панели с другой.  Это было бы аналогично пользовательскому Panel, логика MeasureOverride и ArrangeOverride которого зависит от значений его *статических* свойств.

#### <a name="layoutcontext"></a>LayoutContext

Цель [LayoutContext](/uwp/api/microsoft.ui.xaml.controls.layoutcontext) заключается в решении этих запросов защиты.  Он предоставляет подключенному макету возможность взаимодействия с контейнером основного элемента, например получение дочерних элементов, без введения прямой зависимости между ними. Контекст также позволяет макету хранить любое требуемое состояние, которое может быть связано с дочерними элементами контейнера.

Простые макеты, не связанные с виртуализацией, часто не нуждаются в поддержании какого-либо состояния, что делает их несущественными. Однако более сложная структура, например Grid, может поддерживать состояние между вызовом меры и компоновки, чтобы избежать повторного вычисления значения.

Макеты виртуализации *часто* должны поддерживать некоторое состояние как между мерой и компоновкой, так и между этапами итеративной разметки.

#### <a name="initializing-and-uninitializing-per-container-state"></a>Инициализация и отмена инициализации состояния на контейнер

При подключении макета к контейнеру вызывается его метод [InitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore), который дает возможность инициализировать объект до состояния хранилища.

Аналогичным образом при удалении макета из контейнера будет вызван метод [UninitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore).  Это дает макету возможность очистить любое состояние, связанное с этим контейнером.

Объект состояния макета можно хранить в контейнере и извлекать из него с помощью свойства [LayoutState](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate) в контексте.

### <a name="ui-virtualization"></a>Виртуализация пользовательского интерфейса

Виртуализация пользовательского интерфейса означает задержку создания объекта пользовательского интерфейса до времени, _когда это будет необходимо_.  Это сделано для оптимизации производительности.  Для сценариев без прокрутки определение _когда необходимо_ может быть основано на любом количестве вещей, которые являются специфическими для приложений.  В таких случаях приложения должны использовать [x:Load](../../xaml-platform/x-load-attribute.md). Это не предусматривает специальной обработки в макете.

В сценариях на основе прокрутки, таких как список, определение _когда необходимо_ часто основывается на "будет ли он видимым для пользователя", что в значительной степени зависит от его размещения во время процесса макета, и требует особых рекомендаций.  Этот сценарий находится в центре внимания данного документа.

> [!NOTE]
> Хотя это и не рассматривается в этом документе, те же возможности, которые позволяют реализовать виртуализацию пользовательского интерфейса в сценариях с прокруткой, можно применять в сценариях без нее.  Например, управляемый данными элемент управления ToolBar, который управляет временем существования команд, которые он представляет, и реагирует на изменения в доступном пространстве, перезапуская или перемещая элементы между видимой областью и меню переполнения.

## <a name="getting-started"></a>Начало работы

Сначала определите, должен ли создаваемый макет поддерживать виртуализацию пользовательского интерфейса.

**Нескольку вещей, которые следует иметь ввиду...**

1. Разработать макеты без виртуализации намного проще. Если количество элементов всегда будет небольшим, рекомендуется создавать макет без виртуализации.
2. Платформа предоставляет набор подключенных макетов, которые работают с [ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater#change-the-layout-of-items) и [LayoutPanel](/uwp/api/microsoft.ui.xaml.controls.layoutpanel) для удовлетворения общих потребностей.  Ознакомьтесь с ними, прежде чем принимать решение о необходимости определения пользовательского макета.
3. Макеты виртуализации всегда имеют дополнительные затраты на ЦП и память, накладные расходы, а также они более сложные по сравнению с макетом без виртуализации.  Как правило, если дочерние элементы, которыми должен управлять макет, поместятся в область, которая в 3 раза больше окна просмотра, макет виртуализации может не принести большой пользы. 3-кратный размер более подробно рассматривается далее в этом документе, но он связан с асинхронной природой прокрутки в Windows и ее влиянием на виртуализацию.

> [!TIP]
> В качестве отправной точки, настройки по умолчанию для [ListView](/uwp/api/windows.ui.xaml.controls.listview) (и [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)) заключаются в том, что переработка не начинается, пока количество элементов не будет достаточным, чтобы заполнить 3-кратный размер текущего окна просмотра.

**Выбор базового типа**

![иерархия подключенных макетов](images/xaml-attached-layout-hierarchy.png)

Базовый тип [макета](/uwp/api/microsoft.ui.xaml.controls.layout) имеет два производных типа, которые служат начальной точкой для создания подключенного макета:

1. [NonVirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout)
2. [VirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout)

## <a name="non-virtualizing-layout"></a>Макет без виртуализации

Подход к созданию макета без виртуализации должен быть знаком всем, кто создавал [пользовательские панели](/windows/uwp/design/layout/custom-panels-overview).  Здесь применяются те же принципы.  Основное отличие заключается в том, что для доступа к коллекции [Children](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext.children) используется [NonVirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayoutcontext), а макет может сохранять состояние.

1. Производный от базового типа [NonVirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout) (а не Panel).
2. *(Необязательно)* Определяет свойства зависимости, которые после изменения сделают макет недействительным.
3. _(**Новое**/необязательное)_ Инициализирует любой объект состояния, необходимый для макета, как часть [InitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore). Хранит его в контейнере узла, используя [LayoutState](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate), поставляемый с контекстом.
4. Переопределяет [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout.measureoverride) и вызывает метод [Measure](/uwp/api/windows.ui.xaml.uielement.measure) для всех дочерних элементов.
5. Переопределяет [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.nonvirtualizinglayout.arrangeoverride) и вызывает метод [Arrange](/uwp/api/windows.ui.xaml.uielement.arrange) для всех дочерних элементов.
6. *(**Новое**/дополнительное)* Очищает любое состояние, сохраненное как часть [UninitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore).

### <a name="example-a-simple-stack-layout-varying-sized-items"></a>Пример: Простой макет "стопка" (элементы разного размера)

![MyStackLayout](images/xaml-attached-layout-mystacklayout.png)

Ниже приведен очень простой макет "стопка" без виртуализации, для элементов разного размера. У него отсутствуют свойства для настройки поведения макета. В приведенной ниже реализации показано, как макет основывается на объекте контекста, предоставленном контейнером, чтобы:

1. Получить количество дочерних элементов.
2. Получить доступ к каждому дочернему элементу по индексу.

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

## <a name="virtualizing-layouts"></a>Макеты виртуализации

Общие действия для макета виртуализации идентичны таковым для макета без виртуализации.  Сложность во многом заключается в определении того, какие элементы будут находиться в окне просмотра и какие следует реализовать.

1. Производный от базового типа [VirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout).
2. (Необязательно) Определяет свойства зависимости, которые после изменения сделают макет недействительным.
3. Инициализирует любой объект состояния, необходимый для макета, как часть [InitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.initializeforcontextcore). Хранит его в контейнере узла, используя [LayoutState](/uwp/api/microsoft.ui.xaml.controls.layoutcontext.layoutstate), поставляемый с контекстом.
4. Переопределяет [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride) и вызывает метод [Measure](/uwp/api/windows.ui.xaml.uielement.measure) для каждого дочернего элемента, который следует реализовать.
   1. Для получения UIElement, подготовленного платформой, используется метод [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) (например, к примененным привязкам к данным).
5. Переопределяет [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride) и вызывает метод [Arrange](/uwp/api/windows.ui.xaml.uielement.arrange) для каждого реализованного дочернего элемента.
6. (Дополнительно) Очищает любое состояние, сохраненное как часть [UninitializeForContextCore](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.uninitializeforcontextcore).

> [!TIP]
> Значение, возвращаемое [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout), используется в качестве размера виртуализированного содержимого.

При создании макета виртуализации необходимо учитывать два общих подхода.  Выбор одного или другого параметра зависит от типа определения размера элемента.  Если достаточно определить индекс элемента в наборе данных или сами данные зависят от его окончательного размера, мы будем рассматривать его как **зависящий от данных**.  Их легче создавать.  Однако, если единственный способ определить размер элемента — создать и измерить пользовательский интерфейс, тогда он будет **зависящим от содержимого**.  Они более сложны.

### <a name="the-layout-process"></a>Процесс макета

Независимо от того, создаете ли вы макет, зависящий от содержимого или данных, важно понимать процесс макета и влияние асинхронной прокрутки Windows.

(Более) упрощенный вид шагов, выполняемых платформой от запуска до отображения пользовательского интерфейса на экране состоит в том, что:

1. Он анализирует разметку.

2. Создает дерево элементов.

3. Выполняет этап разметки.

4. Выполняет этап прорисовки.

При виртуализации пользовательского интерфейса создание элементов, которое обычно выполнялось бы на шаге 2, откладывается или заканчивается раньше после определения того, что для заполнения окна просмотра было создано достаточное содержимое. Контейнер виртуализации (например, ItemsRepeater) для обработки этого процесса полагается на подключенный к нему макет. Он предоставляет подключенный макет с [VirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext), который содержит дополнительные сведения, необходимые для макета виртуализации.

**RealizationRect (т. е. окно просмотра)**

Прокрутка в Windows происходит асинхронно в потоке пользовательского интерфейса. Он не контролируется макетом платформы.  Вместо этого взаимодействие и перемещение происходит в компоновщике системы. Преимущество такого подхода заключается в том, что панорамирование контента всегда можно выполнить со скоростью 60 кадров в секунду.  Проблема, однако, заключается в том, что "окно просмотра", как видно из макета, может быть слегка устаревшим по отношению к тому, что на самом деле видно на экране. При быстрой прокрутке пользователь может обогнать скорость потока пользовательского интерфейса для создания нового содержимого и выполнить "панорамирование на черный цвет". По этой причине для макета виртуализации часто требуется создавать дополнительный буфер подготовленных элементов, достаточный для заполнения области, превышающей окно просмотра. При более высокой нагрузке во время прокрутки пользователю все равно предоставляется содержимое.

![Прямоугольник реализации](images/xaml-attached-layout-realizationrect.png)

Так как создание элементов дорогостоящее, виртуализация контейнеров (например, [ItemsRepeater](/windows/uwp/design/controls-and-patterns/items-repeater)) изначально предоставит подключенный макет с [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect), который соответствует окну просмотра. В момент простоя контейнер может расширить буфер подготовленного содержимого, делая повторные вызовы к макету и используя все большее значение прямоугольника реализации. Такое поведение является оптимизацией производительности, которая пытается установить баланс между быстрым временем запуска и хорошим интерфейсом панорамирования. Максимальный размер буфера, создаваемого ItemsRepeater, управляется его свойствами [VerticalCacheLength](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.verticalcachelength) и [HorizontalCacheLength](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.verticalcachelength).

**Повторное использование элементов (перезапуск)**

Предполагается, что макет будет иметь размер и расположение элементов, заполняющих [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect) при каждом запуске. По умолчанию [VirtualizingLayout](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout) будет перезапускать все неиспользуемые элементы в конце каждого этапа разметки.

[VirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext), который передается в макет в составе [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride) и [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride) предоставляет дополнительные сведения о требованиях к макету виртуализации. Некоторые из наиболее часто используемых вещей, которые он предоставляет, это способность:

1. Запросить количество элементов в данных ([ItemCount](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.itemcount)).
2. Получить конкретный элемент с помощью метода [GetItemAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getitemat).
3. Извлечь [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect), представляющий окно просмотра и буфер, которые макет должен заполнить реализованными элементами.
4. Запросить UIElement для определенного элемента с помощью метода [GetOrCreateElement](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat).

Запрос элемента для заданного индекса приведет к тому, что для этого этапа макета элемент будет помечен как "используется". Если элемент еще не существует, он будет реализован и автоматически подготовлен к использованию (например, при использовании дерева пользовательского интерфейса, определенного в DataTemplate, обработке любой привязки данных и т. д.).  В противном случае он будет извлечен из пула существующих экземпляров.

В конце каждого этапа измерения любой существующий, реализованный элемент, который не был помечен как "используется", автоматически считается доступным для повторного использования, если только при извлечении элемента методом [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) не был использован параметр [SuppressAutoRecycle](/uwp/api/microsoft.ui.xaml.controls.elementrealizationoptions). Платформа автоматически переместит его в пул очистки и сделает доступным. Впоследствии его можно извлечь для использования другим контейнером. При возможности платформа пытается избежать этого, поскольку с переподчинением элемента связаны некоторые затраты.

Если макету виртуализации в начале каждого измерения будет известно, какие элементы больше не будут входить в состав прямоугольника реализации, он сможет оптимизировать повторное использование. Вместо того, чтобы полагаться на поведение платформы по умолчанию. Макет может переносить элементы в пул повторного использования, используя метод [RecycleElement](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recycleelement).  Вызов этого метода перед запросом новых элементов приводит к тому, что существующие элементы становятся доступными, когда компоновщик позднее выдает ошибку запроса [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat) на индекс, который еще не связан с элементом.

VirtualizingLayoutContext предоставляет два дополнительных свойства, предназначенных для авторов макета, создающих макет, зависящий от содержимого. Они рассматриваются более подробно далее.

1. [RecommendedAnchorIndex](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex), предоставляющий необязательные _входные данные_ для макета.
2. [LayoutOrigin](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.layoutorigin), который является необязательным _выводом_ макета.

## <a name="data-dependent-virtualizing-layouts"></a>Макеты виртуализации, зависящие от данных

Создание макета виртуализации проще, если вы знаете, каков должен быть размер каждого элемента, и вам не нужно измерять содержимое для отображения.  В этом документе мы просто будем называть эту категорию виртуализации **макеты данных**, поскольку они обычно требуют проверки данных.  На основе данных приложение может выбрать визуальное представление с известным размером, возможно, из-за того, что он является частью данных или был ранее определен проектированием.

Общий подход заключается в том, чтобы макет мог:

1. Вычислять размер и позицию каждого элемента.
2. Как часть [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride):
   1. Использовать [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect), чтобы определить, какие элементы должны отображаться в окне просмотра.
   2. Извлекать объект UIElement, который должен представлять элемент с помощью метода [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat).
   3. [Измерять](/uwp/api/windows.ui.xaml.uielement.measure) UIElement с помощью предварительно вычисленного размера.
3. Как часть [ArrangeOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.arrangeoverride) [размещать](/uwp/api/windows.ui.xaml.uielement.arrange) каждый реализованный UIElement, используя предварительно вычисленную позицию.

> [!NOTE]
> Подход "макет данных" часто несовместим с _виртуализацией данных_.  В частности, когда в память загружаются только данные, необходимые для заполнения видимого пользователю содержимого.  Виртуализация данных не ссылается на отложенную или добавочную загрузку данных, поскольку пользователь прокручивает данные вниз, где они остаются резидентными.  Скорее, это относится к тому моменту, когда элементы освобождаются из памяти, поскольку они прокручиваются вне поля зрения.  Наличие макета данных, который проверяет каждый элемент данных как часть макета данных, не позволит виртуализации данных работать так, как ожидалось.  Исключением являются макеты типа UniformGridLayout, который предполагает, что все имеет одинаковый размер.

> [!TIP]
> Если вы создаете пользовательский элемент управления для библиотеки элементов управления, который будет использоваться другими пользователями в самых разных ситуациях, то макет данных может не подойти вам.

### <a name="example-xbox-activity-feed-layout"></a>Пример: Макет веб-канала активности Xbox

Пользовательский интерфейс для веб-канала активности Xbox использует повторяющийся шаблон, где каждая строка имеет широкую плитку, за которой следуют две узкие плитки, перевернутые на следующей строке. В этом макете размер каждого элемента является функцией позиции элемента в наборе данных и известного размера для плиток (широкие и узкие).

![Веб-канал активности Xbox](images/xaml-attached-layout-activityfeedscreenshot.png)

В приведенном ниже коде рассматривается общий подход к использованию пользовательского интерфейса виртуализации для веб-канала активности, который можно использовать для **макета данных**.

<table>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ItemsRepeater">ItemsRepeater</a> в действии, используя этот пример макета.</p>
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

### <a name="optional-managing-the-item-to-uielement-mapping"></a>(Необязательно) Управление сопоставлением элементов UIElement

По умолчанию [VirtualizingLayoutContext](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext) поддерживает сопоставление между реализованными элементами и индексом в источнике данных, который они представляют.  Макет может управлять этим сопоставлением, всегда запрашивая параметр [SuppressAutoRecycle](/uwp/api/microsoft.ui.xaml.controls.elementrealizationoptions) при получении элемента с помощью метода [GetOrCreateElementAt](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.getorcreateelementat), который предотвращает автоматический перезапуск по умолчанию.  Макет может сделать это, например, если он будет использоваться только при ограниченной прокрутке в одном направлении, а рассматриваемые элементы всегда будут смежными (т. е. чтобы знать все элементы, которые следует реализовать, достаточно знать индекс первого и последнего элемента).

#### <a name="example-xbox-activity-feed-measure"></a>Пример: Мера веб-канала активности Xbox

В приведенном ниже фрагменте кода показана дополнительная логика, которую в предыдущем примере можно добавить к MeasureOverride для управления сопоставлением.

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

Если для определения точного размера сначала следует измерить содержимое пользовательского интерфейса — это **макет, зависящий от содержимого**.  Его также можно рассматривать как макет, в котором макет не определяет размер элементов, поскольку каждый элемент в нем имеет свой размер. Макеты виртуализации, относящиеся к этой категории, более сложные.

> [!NOTE]
> Макеты, зависящие от содержимого, не должны прерывать виртуализацию данных.

### <a name="estimations"></a>Оценки

Чтобы предположить размер нереализованного содержимого и положение реализованного содержимого, зависимые от содержимого макеты опираются на оценку. По мере изменения этих оценок реализованное содержимое будет регулярно перемещаться в пределах прокручиваемой области. Если эту проблему не устранить, пользователь столкнется с очень затрудненным и раздражающим пользовательским интерфейсом. Здесь обсуждаются потенциальные проблемы и способы их устранения.

> [!NOTE]
> Макеты данных, которые учитывают каждый элемент, и которым известен точный размер всех элементов (реализованных или нет), а ещё их позиции могут полностью избежать этих проблем.

**Привязка прокрутки**

XAML предоставляет механизм для устранения внезапного сдвига окна просмотра, его элементы управления прокрутки поддерживают [привязку прокрутки](/uwp/api/windows.ui.xaml.controls.iscrollanchorprovider), реализуя интерфейс [IScrollAnchorPovider](/uwp/api/windows.ui.xaml.controls.iscrollanchorprovider). По мере того, как пользователь манипулирует содержимым, элемент управления прокруткой постоянно выбирает элемент из набора кандидатов, выбранных для отслеживания. Если положение элемента привязки смещается во время разметки, то элемент управления прокруткой автоматически сдвигает окно просмотра, чтобы обеспечить его выполнение.

Значение [RecommendedAnchorIndex](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex), предоставленное для макета, может отражать текущий элемент привязки, выбранный элементом управления прокрутки. Кроме того, если разработчик явно запрашивает, чтобы элемент был реализован для индекса с помощью метода [GetOrCreateElement](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater.getorcreateelement) в [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater), то на следующий этап разметки этот индекс присваивается [RecommendedAnchorIndex](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex). Это позволяет подготовить макет к вероятному сценарию, в котором разработчик реализует элемент, а затем запрашивает его представление методом [StartBringIntoView](/uwp/api/windows.ui.xaml.uielement.startbringintoview).

[RecommendedAnchorIndex](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.recommendedanchorindex) — это индекс элемента в источнике данных, макет зависящий от содержимого,который должен расположить первым при оценке позиции его элементов. Он должен служить в качестве отправной точки для размещения других реализованных элементов.

**Влияние на полосы прокрутки**

Даже при использовании привязки прокрутки, если оценки макета имеют большое значение, возможно, из-за существенных вариаций размера содержимого, может показаться, что положение бегунка для полосы прокрутки резко меняется.  Пользователя может раздражать тот факт, что при перетаскивании бегунок не будет отслеживать положение указателя мыши.

Чем точнее оценки макета, тем менее вероятно, что пользователь увидит прыжки бегунка полосы прокрутки.

### <a name="layout-corrections"></a>Исправления макета

Зависящий от содержимого макет следует подготовить к рационализации оценки с реальностью.  Например, по мере того, как пользователь прокручивает содержимое вверх, а макет реализует самый первый элемент, он может обнаружить, что ожидаемое положение элемента по отношению к элементу, с которого он начал, приведет к тому, что он будет находиться в месте, отличном от расположения источника (x:0, y:0). В этом случае, чтобы задать положение, вычисленное в качестве нового источника макета, макет может использовать свойство [LayoutOrigin](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.layoutorigin).  Итоговый результат похож на привязку прокрутки, в которой окно просмотра элемента управления окна прокрутки автоматически корректируется, чтобы учитывать положение содержимого, сообщаемого макетом.

![Исправление LayoutOrigin](images/xaml-attached-layout-origincorrection.png)

### <a name="disconnected-viewports"></a>Отключенные окна просмотра

Размер, возвращаемый методом макета [MeasureOverride](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayout.measureoverride), представляет наилучшее предположение размера содержимого, которое может измениться при каждой последующей разметке.  Поскольку пользователь прокручивает макет, он будет постоянно переоцениваться с помощью обновленного [RealizationRect](/uwp/api/microsoft.ui.xaml.controls.virtualizinglayoutcontext.realizationrect).

Если пользователь перетаскивает бегунок очень быстро, окно просмотра можно открыть с позиции макета, чтобы сделать большие переходы в местах, где предыдущее положение не перекрывает текущее.  Это обусловлено асинхронной природой прокрутки. Кроме того, приложение, использующее макет, может запросить отображение в представлении элемента, который в настоящее время не реализован, и рассматривается, как таков, что находится вне текущего диапазона, отслеживаемого макетом.

Если макет обнаруживает, что его догадка неверна и/или видит неожиданное смещение окна просмотра, ему необходимо переориентировать начальное положение.  Макеты виртуализации, которые поставляются как часть элементов управления XAML, разрабатываются как макеты, зависящие от содержимого, поскольку они размещают меньше ограничений на характер содержимого, которое будет отображаться.


### <a name="example-simple-virtualizing-stack-layout-for-variable-sized-items"></a>Пример: Простой макет "стопка" виртуализации для элементов с изменяемыми размерами

В примере ниже показан простой макет "стопка" для элементов с переменным размером, который:

* поддерживает виртуализацию пользовательского интерфейса;
* использует оценки для предположения размера нереализованных элементов;
* осведомлен о возможности прерывистых сдвигов окна просмотра;
* применяет исправления макета, чтобы учитывать эти сдвиги.

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

**CodeBehind: Main.cs**

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

## <a name="related-articles"></a>Похожие статьи

- [ItemsRepeater](/uwp/api/microsoft.ui.xaml.controls.itemsrepeater)
- [ScrollViewer](/uwp/api/windows.ui.xaml.controls.scrollviewer)
