---
Description: NavigationView is an adaptive control that implements top-level navigation patterns for your app.
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
ms.openlocfilehash: 2e436e45e70980e9f75749b3a9377f61b636f890
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8737203"
---
# <a name="navigation-view"></a>Представление навигации

Элемент управления NavigationView обеспечивает навигацию верхнего уровня для вашего приложения. Он адаптируется к различным размерам экрана и поддерживает стили навигации и _вверху_ _слева_ .

![Основные навигации](images/nav-view-header.png)<br/>
_Представление переходов поддерживает верхней и левой панели навигации или меню_

> **API -интерфейсы платформы**: [класс Windows.UI.Xaml.Controls.NavigationView](/uwp/api/windows.ui.xaml.controls.navigationview)
>
> **API -интерфейсы библиотеки пользовательского интерфейса Windows**: [класс Microsoft.UI.Xaml.Controls.NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview)
>
> Для некоторых функций NavigationView, например для навигации _верхнего_ требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии или [Библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

NavigationView — это элемент управления адаптивной навигации, который лучше всего подойдет для:

- Предоставление единообразия интерфейса навигации во всем приложении.
- Экономия свободного экранного пространства для окон меньшего размера.
- Организации доступа к многие категории навигации.

Другие шаблоны навигации см. в разделе [основы проектирования навигации](../basics/navigation-basics.md).

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/XAML-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/NavigationView">открыть приложение и увидеть NavigationView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="display-modes"></a>Режимы отображения

> Свойство PaneDisplayMode требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии или [Библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Можно использовать свойство PaneDisplayMode для настройки различных навигации стили или режимы отображения, для NavigationView.

:::row:::
    :::column:::
    ### <a name="top"></a>Top
    Область расположена над содержимым.</br>
    `PaneDisplayMode="Top"`
    :::column-end:::
    :::column span="2":::
    ![Пример верхней части](images/displaymode-top.png)
    :::column-end:::
:::row-end:::

Мы рекомендуем навигации _верхнего_ при:

- У вас есть 5 или меньше категорий навигации верхнего уровня, которые одинаково важны и любые дополнительные навигации верхнего уровня, категории, которые в конечном счете в раскрывающемся меню переполнения считаются столь важны.
- Вам необходимо отобразить все параметры навигации на экране.
- Требуется больше места для содержимого приложения.
- Значки не описывают четко категорий навигации вашего приложения.

:::row:::
    :::column:::
    ### <a name="left"></a>Влево
    Панель развернута и расположены слева от содержимого.</br>
    `PaneDisplayMode="Left"`
    :::column-end:::
    :::column span="2":::
    ![Пример развернутого левой панели навигации](images/displaymode-left.png)
    :::column-end:::
:::row-end:::

Мы рекомендуем _левой_ навигационной при:

- У вас есть 5-10 категорий менее важно навигации верхнего уровня.
- Требуется категорий навигации будет очень четкой, используя меньше пространства для другого содержимого приложения.

:::row:::
    :::column:::
    ### <a name="leftcompact"></a>LeftCompact
    На панели отображаются только значки, пока открыт и располагается слева от содержимого.</br>
    `PaneDisplayMode="LeftCompact"`
    :::column-end:::
    :::column span="2":::
    ![Пример compact левой панели навигации](images/displaymode-leftcompact.png)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
    ### <a name="leftminimal"></a>LeftMinimal
    Пока при открытии панель отображается только кнопка меню. При открытии, он размещается в левой части содержимого.</br>
    `PaneDisplayMode="LeftMinimal"`
    :::column-end:::
    :::column span="2":::
    ![Пример минимальный левой панели навигации](images/displaymode-leftminimal.png)
    :::column-end:::
:::row-end:::

### <a name="auto"></a>Auto (автоматически)

По умолчанию PaneDisplayMode установлено значение Auto. В автоматический режим представление навигации адаптирует между LeftMinimal при узком для LeftCompact окна и затем слева по мере шире окна. Дополнительные сведения см. в разделе [адаптивное поведение](#adaptive-behavior) .

![Адаптивное поведение по умолчанию левое меню навигации](images/displaymode-auto.png)<br/>
_Адаптивное поведение по умолчанию представление навигации_

## <a name="anatomy"></a>Структура

Эти изображения отображать макет панели, заголовок и области содержимого элемента управления, если этот параметр настроен для навигации в _верхней_ или _левой_ .

![Макет верхней части](images/topnav-anatomy.png)<br/>
_Верхней части макета_

![Макет навигации влево](images/leftnav-anatomy.png)<br/>
_Левой навигационной макета_

### <a name="pane"></a>Панель

Свойство PaneDisplayMode расположить панель над содержимым или слева от содержимого.

Панель NavigationView может содержать:

- Объекты [NavigationViewItem](/uwp/api/windows.ui.xaml.controls.navigationviewitem) . Элементы навигации для перехода на определенные страницы.
- Объекты [NavigationViewItemSeparator](/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator) . Разделители для группировки элементов навигации. Задайте свойства [Opacity](/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator.opacity) 0 для отрисовки разделителя места.
- Объекты [NavigationViewItemHeader](/uwp/api/windows.ui.xaml.controls.navigationviewitemheader) . Заголовки для маркировки групп элементов.
- Дополнительный элемент управления [AutoSuggestBox](auto-suggest-box.md) для поиска на уровне приложения. Назначение элемента управления к свойству [NavigationView.AutoSuggestBox](/uwp/api/windows.ui.xaml.controls.navigationview.autosuggestbox) .
- Необязательная точка входа для [параметров приложения](../app-settings/app-settings-and-data.md). Чтобы скрыть элемент параметров, задайте свойство [IsSettingsVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsSettingsVisible) значение **false**.

Расположенной слева области также содержит:

- Кнопка меню для включения и выключения открывать и закрывать панель. Когда панель открыта в более крупных окнах приложения, вы можете скрыть эту кнопку с помощью свойства [IsPaneToggleButtonVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsPaneToggleButtonVisible).

Представление переходов имеет кнопки "Назад", который размещается в верхнем левом углу панели. Тем не менее он не автоматически обрабатывают обратной навигации и добавлять содержимое в стек переходов назад. Чтобы реализовать навигацию в обратном направлении, см. в разделе [обратной навигации](#backwards-navigation) раздел.

Вот Анатомия подробные панели для положения верхней и левой панели.

#### <a name="top-navigation-pane"></a>Верхняя панель навигации

![Анатомия представление верхней панели навигации](images/navview-pane-anatomy-horizontal.png)

1. Заголовки
1. Элементы навигации
1. Разделители
1. AutoSuggestBox (необязательно)
1. Кнопка «Параметры» (необязательно)

#### <a name="left-navigation-pane"></a>Левой панели навигации

![Представление навигации слева Анатомия панели](images/navview-pane-anatomy-vertical.png)

1. Кнопка меню
1. Элементы навигации
1. Разделители
1. Заголовки
1. AutoSuggestBox (необязательно)
1. Кнопка «Параметры» (необязательно)

#### <a name="pane-footer"></a>Нижнем колонтитуле панели

Произвольное содержимое можно поместить в нижнем колонтитуле панели путем добавления его в свойство [PaneFooter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneFooter) .

:::row:::
    :::column:::
    ![Верхней панели навигации нижнем колонтитуле панели](images/navview-freeform-footer-top.png)<br>
     _Нижний верхней панели_<br>
    :::column-end:::
    :::column:::
    ![Навигации левом нижнем колонтитуле панели](images/navview-freeform-footer-left.png)<br>
    _Нижний расположенной слева области_<br>
    :::column-end:::
:::row-end:::

#### <a name="pane-title-and-header"></a>Название области и заголовок

Текстовое содержимое можно поместить в области заголовка панели, установив свойство [PaneTitle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneTitle) . Он принимает строку и отображает текст рядом с кнопкой меню.

Для добавления содержимого не текст, например изображения или логотип, можно разместить любого элемента в заголовке панели путем добавления его в свойство [PaneHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneHeader) .

Если заданы PaneTitle и PaneHeader, содержимое по горизонтали расположенные рядом с кнопкой меню, с помощью PaneTitle, наиболее близкое к кнопке меню.

:::row:::
    :::column:::
    ![Верхней панели навигации заголовка панели](images/navview-freeform-header-top.png)<br>
     _Заголовок верхней панели_<br>
    :::column-end:::
    :::column:::
    ![Области заголовка левой навигации](images/navview-freeform-header-left.png)<br>
    _Заголовок левой панели_<br>
    :::column-end:::
:::row-end:::

#### <a name="pane-content"></a>Область содержимого

Произвольное содержимое можно поместить в области путем добавления его в свойство [PaneCustomContent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneCustomContent) .

:::row:::
    :::column:::
    ![Пользовательские панели содержимого верхнего навигации](images/navview-freeform-pane-top.png)<br>
     _Пользовательское содержимое верхней панели_<br>
    :::column-end:::
    :::column:::
    ![Пользовательское содержимое панели навигации влево](images/navview-freeform-pane-left.png)<br>
    _Пользовательское содержимое левой панели_<br>
    :::column-end:::
:::row-end:::

### <a name="header"></a>Заголовок

Вы можете добавить заголовок страницы, задав свойство [заголовка](/uwp/api/windows.ui.xaml.controls.navigationview.header) .

![Пример области заголовка представление навигации](images/nav-header.png)<br/>
_Заголовок представления навигации_

Области заголовка вертикально выравнивается с кнопкой навигацией в левой области положении и располагается под области в положении верхней панели. Он имеет фиксированную высоту в 52 пикс. Она содержит заголовок страницы категории выбранного навигации. Заголовок закреплен в верхней части страницы и выступает в качестве точки отсечения прокрутки для области содержимого.

Заголовок отображается каждый раз, когда NavigationView находится в режиме минимальной отображения. Вы можете скрыть заголовок в других режимах, используемых для окон большей ширины. Чтобы скрыть заголовок, задайте свойство [AlwaysShowHeader](/uwp/api/windows.ui.xaml.controls.navigationview.AlwaysShowHeader) значение **false**.

### <a name="content"></a>Содержимое

![Пример области содержимого представления навигации](images/nav-content.png)<br/>
_Содержимое представления навигации_

Область содержимого — это область, в которой отображаются основные сведения для выбранной категории навигации.

Мы рекомендуем поля 12 пикселям для области содержимого, когда NavigationView находится в **минимальном** режиме поля и заполнение значение 24 пикселя в противном случае.

## <a name="adaptive-behavior"></a>Адаптивное поведение

По умолчанию представление переходов автоматически изменяет режим отображения в зависимости от доступного экранного пространства. Свойства [CompactModeThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.compactmodethresholdwidth) и [ExpandedModeThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.expandedmodethresholdwidth) указания точки останова, при которых изменении режима отображения. Вы можете изменить эти значения, чтобы настроить поведение режима адаптивный экран.

### <a name="default"></a>По умолчанию

PaneDisplayMode установлен в значение по умолчанию **автоматически**, адаптивное поведение при для отображения:

- Развернутого левой панели для ширины окна большой (1008 пикселей или более поздней версии).
- Элемент слева, только для значков, панель навигации (LeftCompact) для ширины среднее окно (641 – 1007 пикселей).
- Только кнопки меню (LeftMinimal) для ширины небольшое окно (640 пикселей или меньше).

Дополнительные сведения о размеров окон для адаптивное поведение см. в разделе [размерам экрана и точки останова](../layout/screen-sizes-and-breakpoints-for-responsive-design.md).

![Адаптивное поведение по умолчанию левое меню навигации](images/displaymode-auto.png)<br/>
_Адаптивное поведение по умолчанию представление навигации_

### <a name="minimal"></a>Минимальный

Второй распространенный шаблон адаптивного — использовать расширенные левой панели на больших окон ширины и только кнопки меню на обоих значений ширины окна малых и средних.

Мы рекомендуем использовать этот формат, если:

- Требуется больше места для содержимого приложения на более мелкие ширины окна.
- Навигационных категорий не могут быть представлены четко со значками.

![Минимальный адаптивное поведение навигации влево](images/adaptive-behavior-minimal.png)<br/>
_Навигация представление «минимальное» адаптивное поведение_

Чтобы настроить это поведение, установите CompactModeThresholdWidth по ширине, в котором вы хотите, чтобы панели, чтобы свернуть. Здесь оно переходит из по умолчанию 640 для 1007. Вы также должны задать ExpandedModeThresholdWidth, чтобы убедиться, что не конфликтующие значения.

```xaml
<NavigationView CompactModeThresholdWidth="1007" ExpandedModeThresholdWidth="1007"/>
```

### <a name="compact"></a>Компактный

Третий распространенный шаблон адаптивного — использовать расширенные левой панели на больших окон ширины и LeftCompact, только для значков, панель навигации для обоих ширины окна малых и средних.

Мы рекомендуем использовать этот формат, если:

- Важно всегда отображаться все параметры навигации на экране.
- Навигационных категорий могут появляться четко со значками.

![Compact адаптивное поведение навигации влево](images/adaptive-behavior-compact.png)<br/>
_Поведение навигации с помощью представления «compact» адаптивный_

Чтобы настроить это поведение, равным 0 CompactModeThresholdWidth.

```xaml
<NavigationView CompactModeThresholdWidth="0"/>
```

### <a name="no-adaptive-behavior"></a>Нет адаптивное поведение

Чтобы отключить автоматическое адаптивное поведение, установите PaneDisplayMode значение, отличное от автоматически. Здесь имеет значение для LeftMinimal, чтобы только кнопку "меню" отображается независимо от ширины окна.

![Левый навигации не адаптивное поведение](images/adaptive-behavior-none.png)<br/>
_Представление навигации с помощью PaneDisplayMode, установите значение LeftMinimal_

```xaml
<NavigationView PaneDisplayMode="LeftMinimal" />
```

Как описано ранее в разделе _режимов отображения_ , вы можете задать панель всегда быть в верхнем, всегда развернутого, всегда compact или всегда минимальный. Вы также можете управлять режимов отображения самостоятельно в коде приложения. В качестве примера показан в следующем разделе.

### <a name="top-to-left-navigation"></a>Сверху левое меню навигации

При использовании верхнего навигации в приложении, элементов навигации свернуть в меню переполнения как уменьшение ширины окна. Когда окно вашего приложения слишком мал, он может обеспечить удобство работы для переключения PaneDisplayMode сверху LeftMinimal навигации, а не позволяя все, что свернуть элементы в меню переполнения.

Мы рекомендуем использовать большие размеры экрана и навигации слева от небольшого верхней части окна размеры, когда:

- У вас есть набор одинаково важные навигации верхнего уровня категории для отображения друг с другом, таким образом, если одну категорию в этот набор не помещаются на экране, свернуть в левом навигационном присвоит им одинаково важны.
- Вы хотите сохранить доступного содержимого места максимально небольшое окно размеров.

В этом примере показано, как использовать свойства [элементов VisualStateManager](/uwp/api/Windows.UI.Xaml.VisualStateManager) и [AdaptiveTrigger.MinWindowWidth](/uwp/api/windows.ui.xaml.adaptivetrigger.minwindowwidth) для переключения между верхней и LeftMinimal навигации.

![Пример верхней или левой адаптивное поведение 1](images/navigation-top-to-left.png)

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
> При использовании AdaptiveTrigger.MinWindowWidth визуальное состояние активируется при шире, чем указанный Минимальная ширина окна. Это означает, что по умолчанию XAML определяет узком окне, а VisualState изменений, которые применяются, когда окно получает шире. По умолчанию PaneDisplayMode для представление переходов автоматически, поэтому при ширина окна составляет меньше или равно CompactModeThresholdWidth, LeftMinimal навигации используется. Когда окно получит шире, VisualState переопределяет значение по умолчанию и используется верхней части.

## <a name="navigation"></a>Навигация

Представление переходов не выполняет каких-либо задач навигации автоматически. Когда пользователь касается элемента навигации, представление навигации отображается этот элемент как выбранный и вызывает событие [ItemInvoked](/uwp/api/windows.ui.xaml.controls.navigationview.ItemInvoked) . Если касание приводит нового элемента, также вызывается событие [SelectionChanged](/uwp/api/windows.ui.xaml.controls.navigationview.SelectionChanged) .

Можно обработать любое из этих событий для выполнения задач, связанных с требуемого перехода. Выбор, вы должны обрабатывать зависит от поведение, которое требуется для вашего приложения. Как правило перейдите на страницу запрошенный и обновить заголовок представления навигации в ответ на эти события.

**ItemInvoked** возникает при каждом пользователь касается элемента навигации, даже если он установлен. (Элемент также могут вызываться с эквивалентным действием, с помощью мыши, клавиатуры или других методов ввода. Дополнительные сведения см. в разделе [ввода и взаимодействия](../input/index.md).) При переходе в обработчике ItemInvoked по умолчанию будет перезагружен страницы и повторяющиеся запись добавляется в стеке навигации. Если при вызове элемента, следует запрещает перезагрузки страницы или убедитесь, что повторяющиеся записи не создается в стек переходов при перезагрузке страницы. (См. Примеры кода).

**SelectionChanged** может инициироваться вызов элемента, который не является текущий выбранный пользователем или программным способом, изменяя выбранного элемента. Если происходит изменение выбора, так как пользователь вызывает элемент, сначала возникновении события ItemInvoked. Если программный изменения выделения, ItemInvoked не вызывается.

### <a name="backwards-navigation"></a>Навигация в обратном направлении

NavigationView имеет встроенную кнопку «Назад»; Однако с помощью переходов вперед, он не выполняет обратной навигации автоматически. Когда пользователь касается кнопки "Назад", возникает событие [BackRequested](/uwp/api/windows.ui.xaml.controls.navigationview.BackRequested) . Вы обрабатываете это событие для выполнения обратной навигации. Дополнительные сведения и примеры кода, см. в разделе [Журнал навигации и навигация в обратном навигации](../basics/navigation-history-and-backwards-navigation.md).

В режиме Minimal или Compact представление навигации панель открыта в виде всплывающего элемента. В этом случае нажатие кнопки "Назад" будет закрывать панель и вместо вызова события **PaneClosing** .

Можно скрыть или отключить кнопки "Назад", задав следующие свойства:

- [IsBackButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsBackButtonVisible): используйте, чтобы отобразить или скрыть кнопки "Назад". Это свойство принимает значение перечисления [NavigationViewBackButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationviewbackbuttonvisible) и установлено значение **Auto** по умолчанию. При сворачивании кнопки Нет места резервируется для него в макете.
- [Значением IsBackEnabled](/uwp/api/windows.ui.xaml.controls.navigationview.IsBackEnabled): используйте, чтобы включить или отключить кнопки "Назад". Вы можете выполнить привязку данных этого свойства значение свойства [CanGoBack](/uwp/api/windows.ui.xaml.controls.frame.cangoback) кадра навигации. **BackRequested** не вызывается, если **значением IsBackEnabled** является **"false"**.

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

В этом примере показано, как можно использовать NavigationView верхняя панель навигации на большие размеры экрана и левой навигационной панели на небольших размеры экрана. Его можно было адаптировать для навигации влево путем удаления параметров навигации _верхнего_ в VisualStateManager.

В примере показано рекомендуемый способ настройки данных навигации, которые будут работать для многих распространенных сценариев. В нем также показано, как реализовать обратную навигацию с помощью обратной кнопки и клавиатуры навигации NavigationView.

В этом коде предполагается, что ваше приложение содержит страницы со следующими именами для перехода к: _домашней страницы_, _AppsPage_, _GamesPage_, _MusicPage_, _MyContentPage_и _SettingsPage_. Код для этих страниц не отображается.

> [!IMPORTANT]
> Сведения о страницах приложения хранится в [ValueTuple](https://docs.microsoft.com/dotnet/api/system.valuetuple). Эта структура также требуется минимальную версию для проекта приложения должны SDK 17763 или более поздней версии. Если вы используете версию WinUI NavigationView для более ранних версий Windows 10, [пакет System.ValueTuple NuGet](https://www.nuget.org/packages/System.ValueTuple/) можно использовать вместо него.

> [!IMPORTANT]
> Этот код показано, как использовать [Библиотеку пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) версию NavigationView. Если вместо этого использовать версию платформы NavigationView минимальную версию для проекта приложения должны быть SDK 17763 или более поздней версии. Чтобы использовать версию платформы, удалите все ссылки на `muxc:`.

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
> Этот код показано, как использовать [Библиотеку пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) версию NavigationView. Если вместо этого использовать версию платформы NavigationView минимальную версию для проекта приложения должны быть SDK 17763 или более поздней версии. Чтобы использовать версию платформы, удалите все ссылки на `muxc`.

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

## <a name="navigation-view-customization"></a>Настройка представления навигации

### <a name="pane-backgrounds"></a>Фоновые изображения панелей

По умолчанию панель NavigationView использует различные фоновые в зависимости от режима отображения:

- область является сплошным цветом серую развернутом слева, рядом с содержимым (в левом режиме).
- панель использует акрил приложения при открытой как наложение поверх содержимого (в верхнем, Minimal или Compact режиме).

Чтобы изменить фон панели, можно переопределить ресурсы темы XAML, используются для отрисовки фона в каждом режиме. (Этот способ используется вместо одного свойства PaneBackground поддержки другой фон для различных режимов отображения.)

В этой таблице показано, какие ресурсы темы в каждом режиме отображения.

| Режим отображения | Ресурс темы |
| ------------ | -------------- |
| Влево | NavigationViewExpandedPaneBackground |
| LeftCompact<br/>LeftMinimal | NavigationViewDefaultPaneBackground |
| Top | NavigationViewTopPaneBackground |

В этом примере показано, как переопределить ресурсы темы в файле App.xaml. При переопределении ресурсы темы, необходимо всегда предоставить «По умолчанию» и «Высокая контрастность» ресурсов по меньшей мере и словари для «Света» или «Dark» ресурсы при необходимости. Дополнительные сведения см. в разделе [ResourceDictionary.ThemeDictionaries](/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries).

> [!IMPORTANT]
> Этот код показано, как использовать [Библиотеку пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) версию AcrylicBrush. Если вместо этого использовать версию платформы AcrylicBrush минимальную версию для проекта приложения должны быть пакет SDK 16299 или более поздней версии. Чтобы использовать версию платформы, удалите все ссылки на `muxm:`.

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

## <a name="related-topics"></a>Смежные разделы

- [Класс NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)
- [Основные и подробные данные](master-details.md)
- [Основы навигации](../basics/navigation-basics.md)
- [Обзор системы проектирования Fluent Design для UWP](../fluent-design-system/index.md)
