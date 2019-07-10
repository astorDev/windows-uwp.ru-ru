---
Description: NavigationView — это адаптивный элемент управления, который реализует шаблоны навигации верхнего уровня для вашего приложения.
title: Представление навигации
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: ''
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 1a396377eb332052ae7f238a23865f2b7dc0aa16
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65984185"
---
# <a name="navigation-view"></a>Представление навигации

Элемент управления NavigationView обеспечивает навигацию верхнего уровня для вашего приложения. Он адаптируется к различным размерам экрана и поддерживает оба типа панели навигации: _верхнюю_ и _левую_.

![верхняя панель навигации](images/nav-view-header.png)<br/>
_Представление навигации поддерживает как верхнюю, так и левую панель или меню навигации_

> **API платформы**: [класс Windows.UI.Xaml.Controls.NavigationView](/uwp/api/windows.ui.xaml.controls.navigationview).
>
> **API библиотеки пользовательского интерфейса Windows**: [класс Microsoft.UI.Xaml.Controls.NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview).
>
> Для некоторых функций NavigationView, таких как _верхняя_ панель навигации, требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздняя, либо [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

NavigationView — это адаптивный элемент управления навигацией, который обеспечивает следующее:

- согласованность навигации в приложении;
- сохранение свободного экранного пространства в окнах меньших размеров;
- организацию доступа ко многим категориям навигации.

Сведения о других шаблонах навигации см. в статье [Основы проектирования навигации для приложений UWP](../basics/navigation-basics.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/XAML-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">XAML Controls Gallery</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/NavigationView">открыть приложение и увидеть NavigationView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="display-modes"></a>Режимы отображения

> Для использования свойства PaneDisplayMode требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздняя, либо [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Вы можете использовать свойство PaneDisplayMode для настройки различных видов панели навигации или режимов отображения для элемента NavigationView.

:::row:::
    :::column:::
    ### <a name="top"></a>Первые
    Панель расположена над содержимым.</br>
    `PaneDisplayMode="Top"`
    :::column-end:::
    :::column span="2":::
    ![Пример верхней панели навигации](images/displaymode-top.png)
    :::column-end:::
:::row-end:::

Рекомендуем использовать _верхнюю_ панель навигации в следующих случаях:

- у вас есть 5 или меньше категорий навигации верхнего уровня одинаковой степени важности, а дополнительные категории навигации верхнего уровня, попадающие в раскрывающееся меню переполнения, менее важны;
- вы хотите отобразить все параметры навигации на экране;
- для содержимого вашего приложения требуется больше пространства;
- значки не четко описывают категории навигации приложения.

:::row:::
    :::column:::
    ### <a name="left"></a>Влево
    Панель развернута и расположена слева от содержимого.</br>
    `PaneDisplayMode="Left"`
    :::column-end:::
    :::column span="2":::
    ![Пример развернутой левой панели навигации](images/displaymode-left.png)
    :::column-end:::
:::row-end:::

Рекомендуем использовать _левую_ панель навигации в следующих случаях:

- у вас есть 5–10 одинаково важных категорий навигации верхнего уровня;
- вам необходимо, чтобы категории навигации были очень заметными, а для другого содержимого приложения достаточно небольшого пространства.

:::row:::
    :::column:::
    ### <a name="leftcompact"></a>LeftCompact
    На панели отображаются лишь неоткрытые значки, расположенные слева от содержимого.</br>
    `PaneDisplayMode="LeftCompact"`
    :::column-end:::
    :::column span="2":::
    ![Пример левой панели навигации в компактном режиме отображения](images/displaymode-leftcompact.png)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
    ### <a name="leftminimal"></a>LeftMinimal
    Пока панель закрыта, отображается лишь кнопка меню. После открытия панели она располагается слева от содержимого.</br>
    `PaneDisplayMode="LeftMinimal"`
    :::column-end:::
    :::column span="2":::
    ![Пример левой панели навигации в минимальном режиме отображения](images/displaymode-leftminimal.png)
    :::column-end:::
:::row-end:::

### <a name="auto"></a>Auto (Автоматически)

По умолчанию для PaneDisplayMode установлено значение Auto. В автоматическом режиме представление навигации переключается c LeftMinimal на LeftCompact, если окно узкое, а когда оно становится шире — на режим отображения слева. Дополнительные сведения см. в разделе об [адаптивном поведении](#adaptive-behavior).

![Адаптивное поведение левой панели навигации в режиме отображения по умолчанию](images/displaymode-auto.png)<br/>
_Адаптивное поведение представления навигации в режиме отображения по умолчанию_

## <a name="anatomy"></a>Структура

На приведенных ниже изображениях представлена структура панели, верхнего колонтитула и области содержимого элемента управления при настройке _верхней_ или _левой_ панели навигации.

![Макет представления верхней панели навигации](images/topnav-anatomy.png)<br/>
_Структура верхней панели навигации_

![Макет представления левой панели навигации](images/leftnav-anatomy.png)<br/>
_Структура левой панели навигации_

### <a name="pane"></a>Панель

Вы можете использовать свойство PaneDisplayMode для размещения панели над содержимым или слева от содержимого.

Панель NavigationView может содержать следующие объекты:

- Объект [NavigationViewItem](/uwp/api/windows.ui.xaml.controls.navigationviewitem). Элементы навигации для перехода на определенные страницы.
- Объекты [NavigationViewItemSeparator](/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator). Разделители для группировки элементов навигации. Установите для свойства [Opacity](/uwp/api/windows.ui.xaml.uielement.opacity) значение 0, чтобы разделитель отображался как пробел.
- Объекты [NavigationViewItemHeader](/uwp/api/windows.ui.xaml.controls.navigationviewitemheader). Заголовки для маркировки групп элементов.
- Необязательный элемент управления [AutoSuggestBox](auto-suggest-box.md) для поиска на уровне приложения. Назначьте элемент управления свойству [NavigationView.AutoSuggestBox](/uwp/api/windows.ui.xaml.controls.navigationview.autosuggestbox).
- Необязательная точка входа для [параметров приложения](../app-settings/app-settings-and-data.md). Чтобы скрыть элемент параметров, задайте для свойства [IsSettingsVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsSettingsVisible) значение **false**.

Левая панель также содержит следующие объекты:

- Кнопка меню для переключения открытой и закрытой панели. Когда панель открыта в более крупных окнах приложения, вы можете скрыть эту кнопку с помощью свойства [IsPaneToggleButtonVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsPaneToggleButtonVisible).

В представлении навигации есть кнопка возврата, расположенная в верхнем левом углу панели. Но она автоматически не обрабатывает переход в обратном направлении и не добавляет содержимое в обратный стек. Сведения о включении навигации в обратном направлении см. в [этом разделе](#backwards-navigation).

Ниже приведена подробная структура верхней и левой панелей.

#### <a name="top-navigation-pane"></a>Верхняя панель навигации

![Структура представления верхней панели навигации](images/navview-pane-anatomy-horizontal.png)

1. Заголовки
1. Элементы навигации
1. Разделители
1. AutoSuggestBox (необязательно)
1. Кнопка "Параметры" (необязательно)

#### <a name="left-navigation-pane"></a>Левая панель навигации

![Структура представления левой панели навигации](images/navview-pane-anatomy-vertical.png)

1. Кнопка меню
1. Элементы навигации
1. Разделители
1. Заголовки
1. AutoSuggestBox (необязательно)
1. Кнопка "Параметры" (необязательно)

#### <a name="pane-footer"></a>Нижний колонтитул панели

В нижнем колонтитуле панели можно разместить содержимое в произвольном формате, добавив его в свойство [PaneFooter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneFooter).

:::row:::
    :::column:::
    ![Нижний колонтитул верхней панели навигации](images/navview-freeform-footer-top.png)<br>
     _Нижний колонтитул верхней панели_<br>
    :::column-end:::
    :::column:::
    ![Нижний колонтитул левой панели навигации](images/navview-freeform-footer-left.png)<br>
    _Нижний колонтитул левой панели_<br>
    :::column-end:::
:::row-end:::

#### <a name="pane-title-and-header"></a>Заголовок и верхний колонтитул панели

В области верхнего колонтитула панели можно разместить текстовое содержимое, задав свойство [PaneTitle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneTitle). Это свойство принимает строковое значение и отображает текст рядом с кнопкой меню.

Чтобы добавить нетекстовое содержимое, например изображение или логотип, вы можете поместить любой элемент в верхний колонтитул панели, добавив его в свойство [PaneHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneHeader).

Если заданы оба свойства (PaneTitle и PaneHeader), содержимое будет расположено горизонтально рядом с кнопкой меню. При этом PaneTitle будет располагаться ближе к кнопке меню.

:::row:::
    :::column:::
    ![Верхний колонтитул, верхняя панель навигации](images/navview-freeform-header-top.png)<br>
     _Верхний колонтитул верхней панели навигации_<br>
    :::column-end:::
    :::column:::
    ![Верхний колонтитул, левая панель навигации](images/navview-freeform-header-left.png)<br>
    _Верхний колонтитул левой панели навигации_<br>
    :::column-end:::
:::row-end:::

#### <a name="pane-content"></a>Содержимое панели

На панели можно разместить содержимое в произвольном формате, добавив его в свойство [PaneCustomContent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneCustomContent).

:::row:::
    :::column:::
    ![Настраиваемое содержимое, верхняя панель навигации](images/navview-freeform-pane-top.png)<br>
     _Настраиваемое содержимое верхней панели навигации_<br>
    :::column-end:::
    :::column:::
    ![Настраиваемое содержимое, левая панель навигации](images/navview-freeform-pane-left.png)<br>
    _Настраиваемое содержимое левой панели навигации_<br>
    :::column-end:::
:::row-end:::

### <a name="header"></a>Заголовок

Вы можете добавить заголовок страницы, задав свойство [Header](/uwp/api/windows.ui.xaml.controls.navigationview.header).

![Пример области заголовка в представлении навигации](images/nav-header.png)<br/>
_Заголовок в представлении навигации_

Область заголовка выровнена по вертикали относительно кнопки навигации в левой панели и находится под верхней панелью. Она имеет фиксированную высоту в 52 пикселя. Она содержит заголовок страницы выбранной категории навигации. Заголовок закреплен в верхней части страницы и выступает в качестве точки отсечения прокрутки для области содержимого.

Заголовок отображается каждый раз, когда элемент NavigationView находится в минимальном режиме отображения. Вы можете скрыть заголовок в других режимах, используемых для окон большей ширины. Чтобы скрыть заголовок, задайте свойству [AlwaysShowHeader](/uwp/api/windows.ui.xaml.controls.navigationview.AlwaysShowHeader) значение **false**.

### <a name="content"></a>Содержимое

![Пример области содержимого в представлении навигации](images/nav-content.png)<br/>
_Содержимое представления навигации_

Область содержимого — это область, в которой отображаются основные сведения для выбранной категории навигации.

Рекомендуем использовать поля в 12 пикселей для области содержимого, когда NavigationView находится в **минимальном режиме отображения**, в противном случае — поля в 24 пикселя.

## <a name="adaptive-behavior"></a>Адаптивное поведение

По умолчанию представление навигации автоматически изменяет режим отображения в зависимости от доступного экранного пространства. Свойства [CompactModeThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.compactmodethresholdwidth) и [ExpandedModeThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.expandedmodethresholdwidth) определяют точки останова, в которых изменяется режим отображения. Вы можете изменить эти значения, чтобы настроить поведение адаптивного режима отображения.

### <a name="default"></a>По умолчанию

Когда для параметра PaneDisplayMode задано значение по умолчанию **Auto**, адаптивное поведение должно отображать следующее:

- Развернутая левая панель для окон большой ширины (1008 пикселей или больше).
- Левая панель навигации, содержащая только значки, (LeftCompact) для окон средней ширины (641–1007 пикселей).
- Только кнопка меню (LeftMinimal) для окон небольшой ширины (640 пикселей и меньше).

Дополнительные сведения о размерах окон для адаптивного поведения см. в статье [Размеры экрана и точки останова](../layout/screen-sizes-and-breakpoints-for-responsive-design.md).

![Адаптивное поведение левой панели навигации в режиме отображения по умолчанию](images/displaymode-auto.png)<br/>
_Адаптивное поведение представления навигации в режиме отображения по умолчанию_

### <a name="minimal"></a>Минимальный

Вторым распространенным адаптивным шаблоном является использование развернутой левой панели для окон большой ширины и кнопок меню — для окон средней и небольшой ширины.

Рекомендуем использовать его в следующих случаях:

- вам требуется больше пространства для содержимого приложения в окне небольшой ширины;
- ваши категории навигации невозможно представить значками.

![Адаптивное поведение левой панели навигации в минимальном режиме отображения](images/adaptive-behavior-minimal.png)<br/>
_Адаптивное поведение представления навигации в минимальном режиме отображения_

Чтобы настроить это поведение, задайте для свойства CompactModeThresholdWidth ширину, при которой панель необходимо свернуть. В этом примере значение по умолчанию (640) изменено на 1007. Кроме того, задайте соответствующее значение для ExpandedModeThresholdWidth, чтобы избежать конфликта значений.

```xaml
<NavigationView CompactModeThresholdWidth="1007" ExpandedModeThresholdWidth="1007"/>
```

### <a name="compact"></a>Компактный

Третьим распространенным адаптивным шаблоном является использование развернутой левой панели для окон большой ширины и панели навигации LeftCompact, содержащей лишь значки, — для окон средней и небольшой ширины.

Рекомендуем использовать его в следующих случаях:

- вам необходимо, чтобы на экране всегда отображались все возможности навигации;
- ваши категории навигации можно представить значками.

![Адаптивное поведение левой панели навигации в компактном режиме отображения](images/adaptive-behavior-compact.png)<br/>
_Адаптивное поведение представления навигации в компактном режиме отображения_

Чтобы настроить это поведение, задайте для свойства CompactModeThresholdWidth значение 0.

```xaml
<NavigationView CompactModeThresholdWidth="0"/>
```

### <a name="no-adaptive-behavior"></a>Без адаптивного поведения

Чтобы отключить автоматическое адаптивное поведение, задайте для параметра PaneDisplayMode значение, отличное от Auto. В этом случае установлено значение LeftMinimal, поэтому отображается только кнопка меню независимо от ширины окна.

![Левая панель навигации без адаптивного поведения](images/adaptive-behavior-none.png)<br/>
_Представление навигации с LeftMinimal в качестве значения для параметра PaneDisplayMode_

```xaml
<NavigationView PaneDisplayMode="LeftMinimal" />
```

Как указано выше в разделе _Режимы отображения_, вы можете настроить панель таким образом, чтобы она всегда отображалась сверху или же в развернутом, компактном или минимальном режимах. Вы также можете самостоятельно управлять режимами отображения в коде своего приложения. Соответствующий фрагмент кода приведен в следующем разделе.

### <a name="top-to-left-navigation"></a>Переключение верхней панели навигации на левую

При использовании верхней панели навигации в приложении элементы навигации свертываются в меню переполнения по причине уменьшения ширины окна. Если окно вашего приложения узкое, вы можете переключить режим PaneDisplayMode с верхней панели навигации (Top) на левую в минимальном виде (LeftMinimal), предотвратив свертывание элементов в меню переполнения.

Рекомендуем использовать верхнюю панель навигации для окон большого размера, а левую панель навигации для окон небольших размеров в следующих случаях:

- у вас есть набор одинаково важных категорий навигации верхнего уровня, которые должны отображаться вместе, поэтому если одна категория в этом наборе не помещается на экране, вы переключаетесь на левую панель навигации, чтобы придать им одинаковую степень важности;
- вам необходимо сохранить как можно больше содержимого в окнах небольшого размера.

В приведенном ниже примере показано, как использовать свойства [VisualStateManager](/uwp/api/Windows.UI.Xaml.VisualStateManager) и [AdaptiveTrigger.MinWindowWidth](/uwp/api/windows.ui.xaml.adaptivetrigger.minwindowwidth) для переключения между верхней панелью навигации (Top) и левой в минимальном виде (LeftMinimal).

![Пример 1. Адаптивное поведение верхней или левой панели навигации](images/navigation-top-to-left.png)

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
> При использовании AdaptiveTrigger.MinWindowWidth визуальное состояние активируется, если ширина окна превышает указанное минимальное значение. Это означает, что XAML по умолчанию определяет узкое окно, а VisualState определяет модификации, которые применяются при увеличении ширины окна. PaneDisplayMode для представления навигации по умолчанию задано значение Auto, поэтому, когда ширина окна меньше или равна значению CompactModeThresholdWidth, используется левая панель навигации в минимальном виде (LeftMinimal). Если окно становится шире, VisualState переопределяет значение по умолчанию и используется верхняя панель навигации.

## <a name="navigation"></a>Навигация

Представление навигации не выполняет никаких задач навигации автоматически. При касании элемента навигации представление навигации отображает этот элемент как выбранный и вызывает событие [ItemInvoked](/uwp/api/windows.ui.xaml.controls.navigationview.ItemInvoked). Если касание приводит к выбору нового элемента, представление также вызывает событие [SelectionChanged](/uwp/api/windows.ui.xaml.controls.navigationview.SelectionChanged).

Вы можете обрабатывать любое событие для выполнения задач, связанных с запрошенной навигацией. Какое из событий необходимо обработать зависит от поведения вашего приложения. Обычно в ответ на эти события необходимо перейти на запрошенную страницу и обновить заголовок представления навигации.

Событие **ItemInvoked** вызывается каждый раз, когда пользователь касается элемента навигации, даже если он уже выбран. (Элемент также можно вызвать аналогичным действием с помощью мыши, клавиатуры или другого ввода. Дополнительные сведения см. в статье [Методы ввода и взаимодействие](../input/index.md).) При переходе в обработчик ItemInvoked по умолчанию страница перезагрузится, а в стек навигации будет добавлена повторяющаяся запись. Если вы выполняете переход при вызове элемента, запретите перезагрузку страницы или убедитесь, что при повторной загрузке страницы в обратном стеке навигации не создается повторяющаяся запись. (См. примеры кода.)

Событие **SelectionChanged** может быть инициировано пользователем, который вызывает не выбранный в настоящее время элемент или изменяет выбранный элемент программным способом. Если изменение выбора происходит из-за вызова элемента, сначала возникает событие ItemInvoked. Если изменение выбора выполняется программным способом, событие ItemInvoked не возникает.

### <a name="backwards-navigation"></a>Навигация в обратном направлении

NavigationView содержит встроенную кнопку возврата. Но, как и переход вперед, навигация в обратном направлении не выполняется автоматически. При нажатии кнопки возврата возникает событие [BackRequested](/uwp/api/windows.ui.xaml.controls.navigationview.BackRequested). Вы обрабатываете это событие для выполнения перехода в обратном направлении. Дополнительные сведения и примеры кода см. в статье [Журнал навигации и навигация в обратном направлении для приложений UWP](../basics/navigation-history-and-backwards-navigation.md).

В минимальном и компактном режимах отображения представление панели навигации открывается во всплывающем окне. В этом случае нажатие кнопки возврата закроет панель и вызовет событие **PaneClosing**.

Вы можете скрыть или отключить кнопку возврата, задав следующие свойства:

- [IsBackButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsBackButtonVisible) — используйте, чтобы отобразить и скрыть кнопку возврата. Это свойство принимает перечисление [NavigationViewBackButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationviewbackbuttonvisible), и для него по умолчанию установлено значение **Auto**. Если кнопка свернута, в макете для нее не зарезервировано пространство.
- [IsBackEnabled](/uwp/api/windows.ui.xaml.controls.navigationview.IsBackEnabled) — используйте, чтобы включить или отключить кнопку возврата. Вы можете привязать данные этого свойства к свойству [CanGoBack](/uwp/api/windows.ui.xaml.controls.frame.cangoback) вашего фрейма навигации. Если для параметра **IsBackEnabled** задано значение **false**, событие **BackRequested** не вызывается.

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

В этом примере показано, как можно использовать элемент NavigationView с верхней панелью навигации для окон больших размеров и с левой панелью навигации для окон небольших размеров. Его можно адаптировать только для левой панели навигации, удалив параметры _верхней_ панели навигации в VisualStateManager.

В примере демонстрируется рекомендуемый способ настройки данных навигации, применимый во многих распространенных сценариях. Кроме того, показана реализация навигации в обратном направлении с помощью кнопки возврата элемента NavigationView и клавиатуры.

В этом коде предполагается, что в вашем приложении можно перейти на следующие страницы: _HomePage_, _AppsPage_, _GamesPage_, _MusicPage_, _MyContentPage_ и _SettingsPage_. Код для этих страниц не отображается.

> [!IMPORTANT]
> Сведения о страницах приложения хранятся в [ValueTuple](https://docs.microsoft.com/dotnet/api/system.valuetuple). Для использования этой структуры в проекте приложения должен использоваться пакет SDK по крайней мере версии 17763 или выше. Если вы используете для NavigationView версию WinUI, предназначенную для более ранних версий Windows 10, вместо указанной выше версии можно использовать [пакет NuGet System.ValueTuple](https://www.nuget.org/packages/System.ValueTuple/).

> [!IMPORTANT]
> В этом коде показано использование [библиотеки интерфейса пользователя Windows](https://docs.microsoft.com/uwp/toolkits/winui/) для NavigationView. Если вы используете для NavigationView версию платформы, минимальной версией пакета SDK проекта приложения должна быть 17763 или выше. Для использования версии платформы удалите все ссылки на `muxc:`.

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
> В этом коде показано использование [библиотеки интерфейса пользователя Windows](https://docs.microsoft.com/uwp/toolkits/winui/) для NavigationView. Если вы используете для NavigationView версию платформы, минимальной версией пакета SDK проекта приложения должна быть 17763 или выше. Для использования версии платформы удалите все ссылки на `muxc`.

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

Ниже приведен обработчик [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/index) (версия обработчика **NavView_ItemInvoked**) из описанного выше примера кода C#. Методика в обработчике C++/WinRT предполагает, что вы сначала сохраняете (в теге [**NavigationViewItem**](/uwp/api/windows.ui.xaml.controls.navigationviewitem)) полное имя типа страницы, к которой необходимо перейти. В обработчике вы распаковываете это значение, преобразуете его в объект [**Windows::UI::Xaml::Interop::TypeName**](/uwp/api/windows.ui.xaml.interop.typename), который используете для перехода на целевую страницу. Переменная сопоставления `_pages`, приведенная в примере C#, не нужна. Вы сможете создавать модульные тесты, подтверждающие, что значения внутри ваших тегов имеют допустимый тип. Дополнительные сведения см. в статье [Упаковка-преобразование и распаковка-преобразование скалярных значений в IInspectable с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/boxing).

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

## <a name="navigation-view-customization"></a>Настройка представления навигации

### <a name="pane-backgrounds"></a>Фоновый цвет панелей

По умолчанию панель NavigationView использует фоновый цвет в зависимости от режима отображения:

- при развертывании слева рядом с содержимым (в режиме отображения слева) цветом фона панели является сплошной серый цвет;
- при открытии панели в качестве наложения поверх содержимого используется акрил приложения (в режиме отображения сверху, минимальном или компактном режимах).

Чтобы изменить фоновый цвет панели, вы можете переопределить ресурсы темы XAML, используемые для отображения фона в каждом режиме. (Для обеспечения поддержки разных фоновых цветов для различных режимов отображения обычно используется этот метод вместо свойства PaneBackground.)

В приведенной ниже таблице описано использование ресурсов темы в каждом режиме отображения.

| Режим отображения | Ресурсы темы |
| ------------ | -------------- |
| Влево | NavigationViewExpandedPaneBackground |
| LeftCompact<br/>LeftMinimal | NavigationViewDefaultPaneBackground |
| Первые | NavigationViewTopPaneBackground |

В этом примере показано, как переопределить ресурсы темы в файле App.xaml. При переопределении ресурсов темы всегда необходимо предоставлять как минимум словарь ресурсов по умолчанию и HighContrast, а также при необходимости словари для светлых или темных ресурсов. Дополнительные сведения см. в статье о свойстве [ResourceDictionary.ThemeDictionaries](/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries).

> [!IMPORTANT]
> В этом коде показано использование [библиотеки интерфейса пользователя Windows](https://docs.microsoft.com/uwp/toolkits/winui/) для AcrylicBrush. Если вы используете для AcrylicBrush версию платформы, минимальной версией пакета SDK проекта приложения должна быть версия 16299 или выше. Для использования версии платформы удалите все ссылки на `muxm:`.

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

## <a name="related-topics"></a>Статьи по теме

- [NavigationView Class](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview) (Класс NavigationView)
- [Основные и подробные данные](master-details.md)
- [Основы навигации](../basics/navigation-basics.md)
- [Система Fluent Design для разработчиков приложений для Windows](/windows/apps/fluent-design-system)
