---
Description: NavigationView является элементом адаптивной, который реализует шаблоны Навигация верхнего уровня для приложения.
title: Представление навигации
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: ''
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 1a396377eb332052ae7f238a23865f2b7dc0aa16
ms.sourcegitcommit: f0f933d5cf0be734373a7b03e338e65000cc3d80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65984185"
---
# <a name="navigation-view"></a>Представление навигации

Элемент управления NavigationView предоставляет Навигация верхнего уровня для приложения. Он адаптируется под различные размеры экранов, который поддерживает _верхней_ и _левой_ стилями навигации.

![верхней панели навигации](images/nav-view-header.png)<br/>
_Поддерживает представление навигации в верхней и левой панели навигации или меню_

> **API-интерфейсов платформы**: [Класс Windows.UI.Xaml.Controls.NavigationView](/uwp/api/windows.ui.xaml.controls.navigationview)
>
> **Windows API-интерфейсов библиотеки пользовательского интерфейса**: [Класс Microsoft.UI.Xaml.Controls.NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview)
>
> Некоторые функции NavigationView, такие как _верхней_ навигации, требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии, или [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

NavigationView является элементом управления адаптивной навигации, который хорошо подходит для:

- Предоставляя согласованный навигационный во всем приложении.
- Сохранение площадь экрана в windows меньшего размера.
- Организации доступ к множество категорий навигации.

Для других шаблонов навигации, см. в разделе [основы проектирования навигации](../basics/navigation-basics.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/XAML-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/NavigationView">открыть приложение и увидеть NavigationView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="display-modes"></a>Режимы отображения

> Для свойства PaneDisplayMode требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии, или [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Можно использовать свойство PaneDisplayMode для настройки различных навигации стили или режимы отображения, для NavigationView.

:::row:::
    :::column:::
    ### <a name="top"></a>TOP
    Области находится выше содержимое.</br>
    `PaneDisplayMode="Top"`
    :::column-end:::
    :::column span="2":::
    ![Пример верхней панели навигации](images/displaymode-top.png)
    :::column-end:::
:::row-end:::

Мы рекомендуем _верхней_ навигации при:

- У вас есть 5 или меньшее число категорий Навигация верхнего уровня, которые одинаково важны и любые дополнительные Навигация верхнего уровня, категории, которые в итоге в раскрывающемся меню переполнения, считаются менее важных.
- Вам нужно показать все параметры навигации на экране.
- Требуется больше места для содержимого приложения.
- Значки очевидно, что невозможно описать категории навигации приложения.

:::row:::
    :::column:::
    ### <a name="left"></a>Слева
    Разворачивается и располагаться слева содержимого области.</br>
    `PaneDisplayMode="Left"`
    :::column-end:::
    :::column span="2":::
    ![Пример расширенной навигации в левой области](images/displaymode-left.png)
    :::column-end:::
:::row-end:::

Мы рекомендуем _левой_ навигации при:

- У вас есть 5 – 10 категорий существенный Навигация верхнего уровня.
- Вы хотите категории навигации быть очень выделенной с помощью меньше места для другого содержимого приложения.

:::row:::
    :::column:::
    ### <a name="leftcompact"></a>LeftCompact
    На панели отображаются только те значки, пока не открывается и размещается слева от содержимого.</br>
    `PaneDisplayMode="LeftCompact"`
    :::column-end:::
    :::column span="2":::
    ![Пример compact левой панели навигации](images/displaymode-leftcompact.png)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
    ### <a name="leftminimal"></a>LeftMinimal
    Только кнопка меню отображается, пока не откроется область. При открытии, он располагается слева от содержимого.</br>
    `PaneDisplayMode="LeftMinimal"`
    :::column-end:::
    :::column span="2":::
    ![Пример минимальной левой панели навигации](images/displaymode-leftminimal.png)
    :::column-end:::
:::row-end:::

### <a name="auto"></a>Auto (Автоматически)

По умолчанию PaneDisplayMode имеет значение Auto. В режиме Auto представления навигации адаптируется между LeftMinimal при узкий, чтобы LeftCompact окна и затем остаются без изменений получает ширина окна. Дополнительные сведения см. в разделе [адаптивное поведение](#adaptive-behavior) раздел.

![Адаптивное поведение по умолчанию навигации слева](images/displaymode-auto.png)<br/>
_Адаптивное поведение навигации представление по умолчанию_

## <a name="anatomy"></a>Структура

Эти образы Показать макет панели, заголовка и области содержимого элемента управления при настройке _верхней_ или _левой_ навигации.

![Макет представления верхней панели навигации](images/topnav-anatomy.png)<br/>
_Макет верхней панели навигации_

![Макет представления в левой области навигации](images/leftnav-anatomy.png)<br/>
_Макет навигации слева_

### <a name="pane"></a>Панель

Свойство PaneDisplayMode можно использовать для размещения области над содержимым или слева от содержимого.

На панели NavigationView может содержать:

- [NavigationViewItem](/uwp/api/windows.ui.xaml.controls.navigationviewitem) объектов. Элементы навигации для перехода к определенным страницам.
- [NavigationViewItemSeparator](/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator) объектов. Разделители для группирования элементов навигации. Задайте [непрозрачности](/uwp/api/windows.ui.xaml.uielement.opacity) значение 0 для подготовки к просмотру разделитель в качестве места.
- [NavigationViewItemHeader](/uwp/api/windows.ui.xaml.controls.navigationviewitemheader) объектов. Заголовки для работы с метками групп элементов.
- Необязательный [AutoSuggestBox](auto-suggest-box.md) управления, чтобы для поиска на уровне приложения. Назначьте элементу управления [NavigationView.AutoSuggestBox](/uwp/api/windows.ui.xaml.controls.navigationview.autosuggestbox) свойство.
- Необязательная точка входа для [параметров приложения](../app-settings/app-settings-and-data.md). Чтобы скрыть его параметры, задайте [IsSettingsVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsSettingsVisible) свойства **false**.

Левая панель также содержит:

- Кнопки для переключения панели меню открытия и закрытия. Когда панель открыта в более крупных окнах приложения, вы можете скрыть эту кнопку с помощью свойства [IsPaneToggleButtonVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsPaneToggleButtonVisible).

Представление навигации содержит кнопки "Назад", расположенном в левом верхнем углу области. Тем не менее он автоматически обработать обратной навигации и добавить содержимое в стек переходов назад. Для обеспечения обратной навигации, см. в разделе [обратной навигации](#backwards-navigation) раздел.

Вот составляющие подробные панели для позиций верхней и левой панели.

#### <a name="top-navigation-pane"></a>Верхней части области навигации

![Составляющие верхней области навигации представления](images/navview-pane-anatomy-horizontal.png)

1. Заголовки
1. Элементы навигации
1. Разделители
1. AutoSuggestBox (необязательно)
1. Кнопка "Параметры" (необязательно)

#### <a name="left-navigation-pane"></a>Панель навигации слева

![Представление навигации в левой панели составляющие](images/navview-pane-anatomy-vertical.png)

1. Кнопка меню
1. Элементы навигации
1. Разделители
1. Заголовки
1. AutoSuggestBox (необязательно)
1. Кнопка "Параметры" (необязательно)

#### <a name="pane-footer"></a>Области нижнего колонтитула

Можно поместить произвольные содержимого в области нижнего колонтитула путем добавления его в [PaneFooter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneFooter) свойство.

:::row:::
    :::column:::
    ![Верхней панели навигации по области нижнего колонтитула](images/navview-freeform-footer-top.png)<br>
     _Верхняя область нижнего колонтитула_<br>
    :::column-end:::
    :::column:::
    ![Левая панель навигации по области нижнего колонтитула](images/navview-freeform-footer-left.png)<br>
    _Нижний колонтитул левой панели_<br>
    :::column-end:::
:::row-end:::

#### <a name="pane-title-and-header"></a>Заголовок панели и заголовок

Можно разместить содержимое текста в области заголовка панели, задав [PaneTitle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneTitle) свойство. Он принимает строку и будет отображен текст рядом с кнопкой меню.

Чтобы добавить нетекстовое содержимое, например изображение или логотип, можно поместить любой элемент в заголовке панели путем добавления его в [PaneHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneHeader) свойство.

Если заданы PaneTitle и PaneHeader, содержимое помещается рядом с кнопкой меню, с помощью PaneTitle, ближайший к кнопке меню по горизонтали.

:::row:::
    :::column:::
    ![Верхняя заголовка панели](images/navview-freeform-header-top.png)<br>
     _Заголовок в верхней области_<br>
    :::column-end:::
    :::column:::
    ![Заголовок панели (левая панель)](images/navview-freeform-header-left.png)<br>
    _Заголовок панели слева_<br>
    :::column-end:::
:::row-end:::

#### <a name="pane-content"></a>Содержимое панели

Можно поместить произвольные содержимого в области путем добавления его в [PaneCustomContent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneCustomContent) свойство.

:::row:::
    :::column:::
    ![Панели пользовательского содержимого верхняя](images/navview-freeform-pane-top.png)<br>
     _Настраиваемое содержимое в верхней области_<br>
    :::column-end:::
    :::column:::
    ![Пользовательское содержимое на панели слева](images/navview-freeform-pane-left.png)<br>
    _Пользовательское содержимое левой области_<br>
    :::column-end:::
:::row-end:::

### <a name="header"></a>Header

Можно добавить заголовок страницы, установив [заголовок](/uwp/api/windows.ui.xaml.controls.navigationview.header) свойство.

![Пример области заголовка представления навигации](images/nav-header.png)<br/>
_Заголовок представления навигации_

Области заголовка выравнивается по вертикали с кнопкой навигации в позицию левой панели и располагается ниже области в положение верхней области. Она имеет фиксированную высоту 52 пикселей. Она содержит заголовок страницы категории выбранного навигации. Заголовок закреплен в верхней части страницы и выступает в качестве точки отсечения прокрутки для области содержимого.

Заголовок отображается каждый раз, когда NavigationView находится в режиме минимальной отображения. Вы можете скрыть заголовок в других режимах, используемых для окон большей ширины. Чтобы скрыть заголовок, установите [AlwaysShowHeader](/uwp/api/windows.ui.xaml.controls.navigationview.AlwaysShowHeader) свойства **false**.

### <a name="content"></a>Content

![Пример области содержимого представления навигации](images/nav-content.png)<br/>
_Содержимое представления навигации_

Область содержимого — это область, в которой отображаются основные сведения для выбранной категории навигации.

Мы рекомендуем 12px поля в области содержимого, когда NavigationView находится в **минимальным** режим и 24 точки поля — в противном случае.

## <a name="adaptive-behavior"></a>Адаптивное поведение

По умолчанию представления навигации автоматически изменяет режим отображения, ее исходя объема доступного пространства на экране к нему. [CompactModeThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.compactmodethresholdwidth) и [ExpandedModeThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.expandedmodethresholdwidth) свойства указывают точки останова, в которых изменяется режим отображения. Можно изменить эти значения для настройки поведения Адаптивная регулировка режима.

### <a name="default"></a>Значение по умолчанию

Если PaneDisplayMode присвоено значение по умолчанию **автоматически**, адаптивное поведение — Показать:

- Развернутое левой на крупные значения ширины (1008px или более поздней версии).
- Объект оставить, только для значка, панель навигации (LeftCompact) на ширину среднего размера окна (641px для 1007px).
- Только кнопки меню (LeftMinimal) на небольшое окно ширины (640 пикселей или меньше).

Дополнительные сведения о размерах окна для адаптивное поведение, см. в разделе [экрана размеры и точки останова](../layout/screen-sizes-and-breakpoints-for-responsive-design.md).

![Адаптивное поведение по умолчанию навигации слева](images/displaymode-auto.png)<br/>
_Адаптивное поведение навигации представление по умолчанию_

### <a name="minimal"></a>Минимальный

Второй распространенный шаблон адаптивной — использовать расширенный левой на крупные значения ширины и только кнопку меню на обоих значений ширины средних и малых окно.

Мы рекомендуем этот формат, если:

- Требуется больше места для содержимого приложений меньшего размера ширины окна.
- В категории навигации не может быть представлено четко значки.

![Минимальный адаптивное поведение навигации слева](images/adaptive-behavior-minimal.png)<br/>
_Режим «минимальный» адаптивной представление навигации_

Чтобы настроить это поведение, значение ширины, которое будет использоваться на панели, чтобы свернуть CompactModeThresholdWidth. Здесь он изменяется со значения по умолчанию 640 для 1007. Также следует задать ExpandedModeThresholdWidth, чтобы убедиться, что значения не конфликтуют.

```xaml
<NavigationView CompactModeThresholdWidth="1007" ExpandedModeThresholdWidth="1007"/>
```

### <a name="compact"></a>Компактный

Третий Стандартная адаптивной — использовать расширенный левой на крупные значения ширины и LeftCompact, только для значка, панель навигации по разным средних и малых окно.

Мы рекомендуем этот формат, если:

- Важно, чтобы всегда отображать все параметры навигации на экране.
- В категории навигации могут представляться четко со значками.

![Compact адаптивное поведение навигации слева](images/adaptive-behavior-compact.png)<br/>
_Представление «compact» адаптивного поведения навигации_

Чтобы настроить это поведение, установите CompactModeThresholdWidth 0.

```xaml
<NavigationView CompactModeThresholdWidth="0"/>
```

### <a name="no-adaptive-behavior"></a>Нет адаптивное поведение

Чтобы отключить автоматическое адаптивное поведение, задайте PaneDisplayMode значение, отличное от автоматически. В этом случае, ему присваивается для LeftMinimal, поэтому только кнопку меню отображается независимо от того, ширина окна.

![Слева не адаптивное поведение навигации](images/adaptive-behavior-none.png)<br/>
_Представление навигации с PaneDisplayMode присвоено LeftMinimal_

```xaml
<NavigationView PaneDisplayMode="LeftMinimal" />
```

Как описано ранее в _режимы отображения_ можно задать на панели, чтобы всегда быть в верхней части, всегда развернутой, всегда compact или всегда минимальным. Можно также управлять режимов отображения самостоятельно в коде приложения. Пример этого показан в следующем разделе.

### <a name="top-to-left-navigation"></a>Сверху слева

При использовании верхней панели навигации в приложении, в меню переполнения свернуть элементы навигации как уменьшение ширины окна. Если окно приложения слишком узкий, может предоставлять удобство для переключения PaneDisplayMode сверху, чтобы LeftMinimal навигации не все, что свернуть элементы в меню переполнения.

Мы рекомендуем использовать верхней панели навигации на окон больших размеров и навигации слева на малый при мере размеры периодов:

- У вас есть набор столь же важные Навигация верхнего уровня категорий, отображаться одновременно, таким образом, если одна категория, в этом наборе не помещается на экране, можно свернуть для навигации слева, чтобы предоставить им равные по важности.
- Вы хотите сохранить как доступного содержимого пространства максимально в размерах небольшого окна.

В этом примере показано, как использовать [VisualStateManager](/uwp/api/Windows.UI.Xaml.VisualStateManager) и [AdaptiveTrigger.MinWindowWidth](/uwp/api/windows.ui.xaml.adaptivetrigger.minwindowwidth) свойство для переключения между верхней и LeftMinimal навигации.

![Пример верхнего или левого адаптивное поведение 1](images/navigation-top-to-left.png)

```xaml
<Grid >
    <NavigationView x:Name="NavigationViewControl" >
        <NavigationView.MenuItems>
            <NavigationViewItem Content="A" x:Name="A" />
            <NavigationViewItem Content="B" x:Name="B" />
            <NavigationViewItem Content="C" x:Name="C" />
        </NavigationView.MenuItems>
    </NavigationView>

    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState>
                <VisualState.StateTriggers>
                    <AdaptiveTrigger
                        MinWindowWidth="{x:Bind NavigationViewControl.CompactModeThresholdWidth}" />
                </VisualState.StateTriggers>

                <VisualState.Setters>
                    <Setter Target="NavigationViewControl.PaneDisplayMode" Value="Top"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>

```

> [!TIP]
> При использовании AdaptiveTrigger.MinWindowWidth визуальное состояние активируется в том случае, когда окно шире, чем указанная минимальная ширина. Это означает, что по умолчанию XAML определяет окно узкий и VisualState определяет изменения, которые применяются при получении ширина окна. По умолчанию PaneDisplayMode представления навигации — автоматически, поэтому после ширина окна меньше или равно CompactModeThresholdWidth, LeftMinimal навигации используется. При получении окно шире, VisualState переопределяет значение по умолчанию, и используется верхней панели навигации.

## <a name="navigation"></a>Навигация

Представления навигации не автоматически выполняет все задачи навигации. Когда пользователь касается элемента навигации, представления навигации показывает этот элемент как выделенный и вызывает [ItemInvoked](/uwp/api/windows.ui.xaml.controls.navigationview.ItemInvoked) событий. Если шаблон tap приведет к элемента выбора, [SelectionChanged](/uwp/api/windows.ui.xaml.controls.navigationview.SelectionChanged) также события.

Можно обрабатывать оба события для выполнения задачи, связанные с запрошенной навигации. Так как тот, который должен обрабатывать зависит от результата, необходимого для приложения. Как правило перейдите на запрошенную страницу и обновить заголовок представления навигации в ответ на эти события.

**ItemInvoked** возникает при каждом касании элемент навигации, даже если он установлен. (Элемент может также быть вызван с помощью эквивалентное действие с помощью мыши, клавиатуры или другие входные данные. Дополнительные сведения см. в разделе [входных данных и взаимодействия](../input/index.md).) Если перейти в обработчике ItemInvoked, по умолчанию будет перезагружен страницы и Повторяющаяся запись добавляется в стек навигации. Если перейти при вызове элемента, следует запретить перезагрузки страницы, или убедитесь, что повторяющиеся записи не создается в частях навигации при перезагрузке страницы. (См. Примеры кода).

**SelectionChanged** можно увеличить путем вызова элемента, который не выбран пользователь, или программным способом изменения выбранного элемента. Если изменение выбора происходит, поскольку пользователь вызывается элемент, событие ItemInvoked происходит сначала. Если изменение выбора программный, ItemInvoked не возникает.

### <a name="backwards-navigation"></a>Навигация в обратном направлении

NavigationView имеет встроенные кнопки возврата; но, как и для перехода вперед, она не выполняет обратной навигации автоматически. Когда пользователь касается кнопки возврата, [BackRequested](/uwp/api/windows.ui.xaml.controls.navigationview.BackRequested) события. Обработка этого события для выполнения обратной навигации. Дополнительные сведения и примеры кода, см. в разделе [истории переходов и обратной навигации](../basics/navigation-history-and-backwards-navigation.md).

В режиме минимальной или Compact представления навигации панели открыт как всплывающее меню. Таким образом, нажав кнопку "Назад" будет закрыть область и повышение **PaneClosing** событий вместо этого.

Можно скрыть или отключить "Назад", задав следующие свойства:

- [IsBackButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsBackButtonVisible): используется для отображения и скрытия кнопки «Назад». Это свойство принимает значение [NavigationViewBackButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationviewbackbuttonvisible) перечисления и имеет значение **автоматически** по умолчанию. При сворачивании кнопки не место резервируется для него в макете.
- [IsBackEnabled](/uwp/api/windows.ui.xaml.controls.navigationview.IsBackEnabled): позволяет включить или отключить кнопки «Назад». Можно выполнить привязку данных, это свойство, чтобы [CanGoBack](/uwp/api/windows.ui.xaml.controls.frame.cangoback) свойство навигации рамки. **BackRequested** не происходит, если **IsBackEnabled** — **false**.

:::row:::
    :::column:::
        ![Navigation view back button in the left navigation pane](images/leftnav-back.png)<br/>
        _The back button in the left navigation pane_
    :::column-end:::
    :::column:::
        ![Navigation view back button in the top navigation pane](images/topnav-back.png)<br/>
        _The back button in the top navigation pane_
    :::column-end:::
:::row-end:::

## <a name="code-example"></a>Пример кода

В этом примере показано, как можно использовать NavigationView с верхней части области навигации на окон больших размеров и область навигации слева на размеры небольшого окна. Его можно адаптировать для навигации слева, удалив _верхней_ параметры навигации в VisualStateManager.

В примере рекомендуемый способ настроить данные навигации, которое подойдет для многих распространенных сценариев. Он также демонстрирует способы реализации обратной навигации с задней клавиатуры и кнопки навигации в NavigationView.

Этот код предполагает, что приложение содержит страницы, со следующими именами, чтобы перейти к: _Домашняя страница_, _AppsPage_, _GamesPage_, _MusicPage_, _MyContentPage_, и _SettingsPage_ . Код для этих страниц не указывается.

> [!IMPORTANT]
> Сведения о страницах приложения хранится в [ValueTuple](https://docs.microsoft.com/dotnet/api/system.valuetuple). Эта структура требует минимальной версии для проекта приложения использовались SDK 17763 или более поздней версии. Если вы используете версию WinUI NavigationView для более ранних версий Windows 10, можно использовать [пакет System.ValueTuple NuGet](https://www.nuget.org/packages/System.ValueTuple/) вместо этого.

> [!IMPORTANT]
> Этот код показан способ использования [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) версию NavigationView. Если вместо этого использовать версию платформы NavigationView, минимальной версии для проекта приложения должен быть SDK 17763 или более поздней версии. Чтобы использовать версию платформы, удалите все ссылки на `muxc:`.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<Grid>
    <muxc:NavigationView x:Name="NavView"
                         Loaded="NavView_Loaded"
                         ItemInvoked="NavView_ItemInvoked"
                         BackRequested="NavView_BackRequested">
        <muxc:NavigationView.MenuItems>
            <muxc:NavigationViewItem Tag="home" Icon="Home" Content="Home"/>
            <muxc:NavigationViewItemSeparator/>
            <muxc:NavigationViewItemHeader x:Name="MainPagesHeader"
                                           Content="Main pages"/>
            <muxc:NavigationViewItem Tag="apps" Content="Apps">
                <muxc:NavigationViewItem.Icon>
                    <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xEB3C;"/>
                </muxc:NavigationViewItem.Icon>
            </muxc:NavigationViewItem>
            <muxc:NavigationViewItem Tag="games" Content="Games">
                <muxc:NavigationViewItem.Icon>
                    <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xE7FC;"/>
                </muxc:NavigationViewItem.Icon>
            </muxc:NavigationViewItem>
            <muxc:NavigationViewItem Tag="music" Icon="Audio" Content="Music"/>
        </muxc:NavigationView.MenuItems>

        <muxc:NavigationView.AutoSuggestBox>
            <!-- See AutoSuggestBox documentation for
                 more info about how to implement search. -->
            <AutoSuggestBox x:Name="NavViewSearchBox" QueryIcon="Find"/>
        </muxc:NavigationView.AutoSuggestBox>

        <ScrollViewer>
            <Frame x:Name="ContentFrame" Padding="12,0,12,24" IsTabStop="True"
                   NavigationFailed="ContentFrame_NavigationFailed"/>
        </ScrollViewer>
    </muxc:NavigationView>

    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState>
                <VisualState.StateTriggers>
                    <AdaptiveTrigger
                        MinWindowWidth="{x:Bind NavView.CompactModeThresholdWidth}"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <!-- Remove the next 3 lines for left-only navigation. -->
                    <Setter Target="NavView.PaneDisplayMode" Value="Top"/>
                    <Setter Target="NavViewSearchBox.Width" Value="200"/>
                    <Setter Target="MainPagesHeader.Visibility" Value="Collapsed"/>
                    <!-- Leave the next line for left-only navigation. -->
                    <Setter Target="ContentFrame.Padding" Value="24,0,24,24"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>
```

> [!IMPORTANT]
> Этот код показан способ использования [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) версию NavigationView. Если вместо этого использовать версию платформы NavigationView, минимальной версии для проекта приложения должен быть SDK 17763 или более поздней версии. Чтобы использовать версию платформы, удалите все ссылки на `muxc`.

```csharp
// Add "using" for WinUI controls.
// using muxc = Microsoft.UI.Xaml.Controls;

private void ContentFrame_NavigationFailed(object sender, NavigationFailedEventArgs e)
{
    throw new Exception("Failed to load Page " + e.SourcePageType.FullName);
}

// List of ValueTuple holding the Navigation Tag and the relative Navigation Page
private readonly List<(string Tag, Type Page)> _pages = new List<(string Tag, Type Page)>
{
    ("home", typeof(HomePage)),
    ("apps", typeof(AppsPage)),
    ("games", typeof(GamesPage)),
    ("music", typeof(MusicPage)),
};

private void NavView_Loaded(object sender, RoutedEventArgs e)
{
    // You can also add items in code.
    NavView.MenuItems.Add(new muxc.NavigationViewItemSeparator());
    NavView.MenuItems.Add(new muxc.NavigationViewItem
    {
        Content = "My content",
        Icon = new SymbolIcon((Symbol)0xF1AD),
        Tag = "content"
    });
    _pages.Add(("content", typeof(MyContentPage)));

    // Add handler for ContentFrame navigation.
    ContentFrame.Navigated += On_Navigated;

    // NavView doesn't load any page by default, so load home page.
    NavView.SelectedItem = NavView.MenuItems[0];
    // If navigation occurs on SelectionChanged, this isn't needed.
    // Because we use ItemInvoked to navigate, we need to call Navigate
    // here to load the home page.
    NavView_Navigate("home", new EntranceNavigationTransitionInfo());

    // Add keyboard accelerators for backwards navigation.
    var goBack = new KeyboardAccelerator { Key = VirtualKey.GoBack };
    goBack.Invoked += BackInvoked;
    this.KeyboardAccelerators.Add(goBack);

    // ALT routes here
    var altLeft = new KeyboardAccelerator
    {
        Key = VirtualKey.Left,
        Modifiers = VirtualKeyModifiers.Menu
    };
    altLeft.Invoked += BackInvoked;
    this.KeyboardAccelerators.Add(altLeft);
}

private void NavView_ItemInvoked(muxc.NavigationView sender,
                                 muxc.NavigationViewItemInvokedEventArgs args)
{
    if (args.IsSettingsInvoked == true)
    {
        NavView_Navigate("settings", args.RecommendedNavigationTransitionInfo);
    }
    else if (args.InvokedItemContainer != null)
    {
        var navItemTag = args.InvokedItemContainer.Tag.ToString();
        NavView_Navigate(navItemTag, args.RecommendedNavigationTransitionInfo);
    }
}

<!-- NavView_SelectionChanged is not used in this example, but is shown for completeness.
     You will typically handle either ItemInvoked or SelectionChanged to perform navigation,
     but not both. -->
private void NavView_SelectionChanged(muxc.NavigationView sender,
                                      muxc.NavigationViewSelectionChangedEventArgs args)
{
    if (args.IsSettingsSelected == true)
    {
        NavView_Navigate("settings", args.RecommendedNavigationTransitionInfo);
    }
    else if (args.SelectedItemContainer != null)
    {
        var navItemTag = args.SelectedItemContainer.Tag.ToString();
        NavView_Navigate(navItemTag, args.RecommendedNavigationTransitionInfo);
    }
}

private void NavView_Navigate(string navItemTag, NavigationTransitionInfo transitionInfo)
{
    Type _page = null;
    if (navItemTag == "settings")
    {
        _page = typeof(SettingsPage);
    }
    else
    {
        var item = _pages.FirstOrDefault(p => p.Tag.Equals(navItemTag));
        _page = item.Page;
    }
    // Get the page type before navigation so you can prevent duplicate
    // entries in the backstack.
    var preNavPageType = ContentFrame.CurrentSourcePageType;

    // Only navigate if the selected page isn't currently loaded.
    if (!(_page is null) && !Type.Equals(preNavPageType, _page))
    {
        ContentFrame.Navigate(_page, null, transitionInfo);
    }
}

private void NavView_BackRequested(muxc.NavigationView sender,
                                   muxc.NavigationViewBackRequestedEventArgs args)
{
    On_BackRequested();
}

private void BackInvoked(KeyboardAccelerator sender,
                         KeyboardAcceleratorInvokedEventArgs args)
{
    On_BackRequested();
    args.Handled = true;
}

private bool On_BackRequested()
{
    if (!ContentFrame.CanGoBack)
        return false;

    // Don't go back if the nav pane is overlayed.
    if (NavView.IsPaneOpen &&
        (NavView.DisplayMode == muxc.NavigationViewDisplayMode.Compact ||
         NavView.DisplayMode == muxc.NavigationViewDisplayMode.Minimal))
        return false;

    ContentFrame.GoBack();
    return true;
}

private void On_Navigated(object sender, NavigationEventArgs e)
{
    NavView.IsBackEnabled = ContentFrame.CanGoBack;

    if (ContentFrame.SourcePageType == typeof(SettingsPage))
    {
        // SettingsItem is not part of NavView.MenuItems, and doesn't have a Tag.
        NavView.SelectedItem = (muxc.NavigationViewItem)NavView.SettingsItem;
        NavView.Header = "Settings";
    }
    else if (ContentFrame.SourcePageType != null)
    {
        var item = _pages.FirstOrDefault(p => p.Page == e.SourcePageType);

        NavView.SelectedItem = NavView.MenuItems
            .OfType<muxc.NavigationViewItem>()
            .First(n => n.Tag.Equals(item.Tag));

        NavView.Header =
            ((muxc.NavigationViewItem)NavView.SelectedItem)?.Content?.ToString();
    }
}
```

Ниже приведен [ C++/WinRT](/windows/uwp/cpp-and-winrt-apis/index) версии **NavView_ItemInvoked** обработчик из C# приведенном выше примере кода. Метод в C++/WinRT обработчика включает в себя вы сначала хранения (в теге [ **NavigationViewItem**](/uwp/api/windows.ui.xaml.controls.navigationviewitem)) полное имя типа страницы, к которому требуется перейти. В обработчике, распаковки-преобразования этого значения, превратить его в [ **Windows::UI::Xaml::Interop::TypeName** ](/uwp/api/windows.ui.xaml.interop.typename) и использовать, чтобы перейти к странице назначения. Нет необходимости для сопоставления переменной с именем `_pages` , встречающегося в C# пример; и вы сможете создать модульные тесты, подтверждающее, что значения внутри тегов являются допустимого типа. Также см. в разделе [упаковке и распаковке скалярные значения для IInspectable с C++/WinRT](/windows/uwp/cpp-and-winrt-apis/boxing).

```cppwinrt
void MainPage::NavView_ItemInvoked(Windows::Foundation::IInspectable const & /* sender */, Windows::UI::Xaml::Controls::NavigationViewItemInvokedEventArgs const & args)
{
    if (args.IsSettingsInvoked())
    {
        // Navigate to Settings.
    }
    else if (args.InvokedItemContainer())
    {
        Windows::UI::Xaml::Interop::TypeName pageTypeName;
        pageTypeName.Name = unbox_value<hstring>(args.InvokedItemContainer().Tag());
        pageTypeName.Kind = Windows::UI::Xaml::Interop::TypeKind::Primitive;
        ContentFrame().Navigate(pageTypeName, nullptr);
    }
}
```

## <a name="navigation-view-customization"></a>Настройки представления навигации

### <a name="pane-backgrounds"></a>Цвет фона панели

По умолчанию области NavigationView использует различные фоновые в зависимости от режима отображения:

- область является сплошной серый цвет при развертывании в левой части, рядом с содержимым (в режиме слева).
- области используются acrylic в приложении, когда она открыта как наложение на основе содержимого (в верхнем, минимальным или Compact режиме).

Чтобы изменить фон области, можно переопределить ресурсов тем XAML, используемый для отрисовки фона в каждом режиме. (Этот метод используется вместо одного свойства PaneBackground для поддержки различных фон для различных режимов отображения.)

Эта таблица показывает темы ресурс, используемый в каждом режиме отображения.

| Режим отображения | Темы ресурсов |
| ------------ | -------------- |
| Слева | NavigationViewExpandedPaneBackground |
| LeftCompact<br/>LeftMinimal | NavigationViewDefaultPaneBackground |
| TOP | NavigationViewTopPaneBackground |

В этом примере показано, как переопределить ресурсы темы в файле App.xaml. При переопределении ресурсы темы, всегда необходимо обеспечивать словари ресурсов «Default» и «Высокая контрастность» как минимум и словари для «Светлая» и «Темный» ресурсы при необходимости. Дополнительные сведения см. в разделе [ResourceDictionary.ThemeDictionaries](/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries).

> [!IMPORTANT]
> Этот код показан способ использования [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) версию AcrylicBrush. Если вместо этого использовать версию платформы AcrylicBrush, минимальной версии для проекта приложения должен быть SDK 16299 или более поздней версии. Чтобы использовать версию платформы, удалите все ссылки на `muxm:`.

```xaml
<Application
    <!-- ... -->
    xmlns:muxm="using:Microsoft.UI.Xaml.Media">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <XamlControlsResources xmlns="using:Microsoft.UI.Xaml.Controls"/>
                <ResourceDictionary>
                    <ResourceDictionary.ThemeDictionaries>
                        <ResourceDictionary x:Key="Default">
                            <!-- The "Default" theme dictionary is used unless a specific
                                 light, dark, or high contrast dictionary is provided. These
                                 resources should be tested with both the light and dark themes,
                                 and specific light or dark resources provided as needed. -->
                            <muxm:AcrylicBrush x:Key="NavigationViewDefaultPaneBackground"
                                   BackgroundSource="Backdrop"
                                   TintColor="LightSlateGray"
                                   TintOpacity=".6"/>
                            <muxm:AcrylicBrush x:Key="NavigationViewTopPaneBackground"
                                   BackgroundSource="Backdrop"
                                   TintColor="{ThemeResource SystemAccentColor}"
                                   TintOpacity=".6"/>
                            <LinearGradientBrush x:Key="NavigationViewExpandedPaneBackground"
                                     StartPoint="0.5,0" EndPoint="0.5,1">
                                <GradientStop Color="LightSlateGray" Offset="0.0" />
                                <GradientStop Color="White" Offset="1.0" />
                            </LinearGradientBrush>
                        </ResourceDictionary>
                        <ResourceDictionary x:Key="HighContrast">
                            <!-- Always include a "HighContrast" dictionary when you override
                                 theme resources. This empty dictionary ensures that the 
                                 default high contrast resources are used when the user
                                 turns on high contrast mode. -->
                        </ResourceDictionary>
                    </ResourceDictionary.ThemeDictionaries>
                </ResourceDictionary>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

## <a name="related-topics"></a>См. также

- [Класс NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)
- [Основные и подробные данные](master-details.md)
- [Основы навигации](../basics/navigation-basics.md)
- [Fluent разработка для универсальной платформы Windows Обзор](/windows/apps/fluent-design-system)
