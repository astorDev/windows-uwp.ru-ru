---
Description: NavigationView — это адаптивный элемент управления, который реализует шаблоны навигации верхнего уровня для вашего приложения.
title: Представление навигации
template: detail.hbs
ms.date: 05/02/2020
ms.topic: article
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: ''
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 2ac2e95e4233a490187066e0d19f1eeb4f330265
ms.sourcegitcommit: 3b8fac693c0b031def5cedc8ae3632a2aa00f1f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84467754"
---
# <a name="navigation-view"></a>Представление навигации

Элемент управления NavigationView обеспечивает навигацию верхнего уровня для вашего приложения. Он адаптируется к различным размерам экрана и поддерживает оба типа панели навигации: _верхнюю_ и _левую_.

![верхняя панель навигации](images/nav-view-header.png)<br/>
_Представление навигации поддерживает как верхнюю, так и левую панель или меню навигации_

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Элемент управления **NavigationView** является частью библиотеки пользовательского интерфейса Windows, пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений Windows. Дополнительные сведения, включая инструкции по установке, см. в [обзорной статье о библиотеке пользовательского интерфейса Windows](/uwp/toolkits/winui/). |

> **API платформы**: [класс Windows.UI.Xaml.Controls.NavigationView](/uwp/api/windows.ui.xaml.controls.navigationview).
>
> **API библиотеки пользовательского интерфейса Windows**: [класс Microsoft.UI.Xaml.Controls.NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview).
>
> Для некоторых функций NavigationView, таких как _верхняя область навигации_ и _иерархическая навигация_, требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздняя, либо [библиотека пользовательского интерфейса Windows](/uwp/toolkits/winui/).

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
<td><img src="images/XAML-controls-gallery-app-icon-sm.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">XAML Controls Gallery</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/NavigationView">открыть приложение и увидеть NavigationView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="display-modes"></a>Режимы отображения

> Для использования свойства PaneDisplayMode требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздняя, либо [библиотека пользовательского интерфейса Windows](/uwp/toolkits/winui/).

Вы можете использовать свойство PaneDisplayMode для настройки различных видов панели навигации или режимов отображения для элемента NavigationView.

:::row:::
    :::column:::
    ### <a name="top"></a>Начало
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
    ### <a name="left"></a>Левый
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

- Кнопка меню для переключения открытой и закрытой панели. Когда панель открыта в более крупных окнах приложения, вы можете скрыть эту кнопку с помощью свойства [IsPaneToggleButtonVisible](/uwp/api/windows.ui.xaml.controls.navigationview.IsPaneToggleButtonVisible).

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

В нижнем колонтитуле панели можно разместить содержимое в произвольном формате, добавив его в свойство [PaneFooter](/uwp/api/windows.ui.xaml.controls.navigationview.PaneFooter).

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

В области верхнего колонтитула панели можно разместить текстовое содержимое, задав свойство [PaneTitle](/uwp/api/windows.ui.xaml.controls.navigationview.PaneTitle). Это свойство принимает строковое значение и отображает текст рядом с кнопкой меню.

Чтобы добавить нетекстовое содержимое, например изображение или логотип, вы можете поместить любой элемент в верхний колонтитул панели, добавив его в свойство [PaneHeader](/uwp/api/windows.ui.xaml.controls.navigationview.PaneHeader).

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

На панели можно разместить содержимое в произвольном формате, добавив его в свойство [PaneCustomContent](/uwp/api/windows.ui.xaml.controls.navigationview.PaneCustomContent).

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

### <a name="default"></a>Значение по умолчанию

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
        ![Кнопка "Назад" в представлении навигации в левой области навигации](images/leftnav-back.png)<br/>
        _Кнопка "Назад" в левой области навигации_
    :::column-end:::
    :::column:::
        ![Кнопка "Назад" в верхней области навигации](images/topnav-back.png)<br/>
        _Кнопка "Назад" в верхней области навигации_
    :::column-end:::
:::row-end:::

## <a name="code-example"></a>Пример кода

> [!IMPORTANT]
> Для любого проекта, использующего набор средств для работы с библиотеками пользовательского интерфейса Windows (WinUI), необходимо выполнить те же предварительные шаги по установке. См. подробнее о настройке и использовании в руководстве по [началу работы с библиотекой пользовательского интерфейса Windows](/uwp/toolkits/winui/getting-started).

В этом примере показано, как можно использовать **NavigationView** с верхней областью навигации для окон больших размеров и левой областью навигации для окон небольших размеров. Этот элемент можно адаптировать только для области навигации слева, удалив параметры области навигации *вверху* в **VisualStateManager**.

В примере демонстрируется рекомендуемый способ настройки данных навигации, применимый во многих распространенных сценариях. Кроме того, здесь показано, как реализовать навигацию в обратном направлении с помощью кнопки возврата **NavigationView** и клавиатуры.

В этом коде предполагается, что в вашем приложении можно перейти на следующие страницы: *HomePage*, *AppsPage*, *GamesPage*, *MusicPage*, *MyContentPage* и *SettingsPage*. Код для этих страниц не отображается.

> [!IMPORTANT]
> Сведения о страницах приложения хранятся в [ValueTuple](/dotnet/api/system.valuetuple). Для использования этой структуры в проекте приложения должен использоваться пакет SDK по крайней мере версии 17763 или выше. Если вы используете для NavigationView версию WinUI, предназначенную для более ранних версий Windows 10, вместо указанной выше версии можно использовать [пакет NuGet System.ValueTuple](https://www.nuget.org/packages/System.ValueTuple/).

> [!IMPORTANT]
> В этом коде показано использование [библиотеки интерфейса пользователя Windows](/uwp/toolkits/winui/) для NavigationView. Если вы используете для NavigationView версию платформы, минимальной версией пакета SDK проекта приложения должна быть 17763 или выше. Для использования версии платформы удалите все ссылки на `muxc:`.

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
                        MinWindowWidth="{x:Bind NavViewCompactModeThresholdWidth}"/>
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
> В этом коде показано использование [библиотеки интерфейса пользователя Windows](/uwp/toolkits/winui/) для NavigationView. Если вы используете для NavigationView версию платформы, минимальной версией пакета SDK проекта приложения должна быть 17763 или выше. Для использования версии платформы удалите все ссылки на `muxc`.

```csharp
// Add "using" for WinUI controls.
// using muxc = Microsoft.UI.Xaml.Controls;

private double NavViewCompactModeThresholdWidth { get { return NavView.CompactModeThresholdWidth; } }

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
    NavView_Navigate("home", new Windows.UI.Xaml.Media.Animation.EntranceNavigationTransitionInfo());

    // Add keyboard accelerators for backwards navigation.
    var goBack = new KeyboardAccelerator { Key = Windows.System.VirtualKey.GoBack };
    goBack.Invoked += BackInvoked;
    this.KeyboardAccelerators.Add(goBack);

    // ALT routes here
    var altLeft = new KeyboardAccelerator
    {
        Key = Windows.System.VirtualKey.Left,
        Modifiers = Windows.System.VirtualKeyModifiers.Menu
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

// NavView_SelectionChanged is not used in this example, but is shown for completeness.
// You will typically handle either ItemInvoked or SelectionChanged to perform navigation,
// but not both.
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

private void NavView_Navigate(string navItemTag, Windows.UI.Xaml.Media.Animation.NavigationTransitionInfo transitionInfo)
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

> [!NOTE]
> Для версии [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/index) этого примера кода начните с создания нового проекта на основе шаблона проекта **Пустое приложение (C++/WinRT**), а затем добавьте код в листинг в указанные файлы исходного кода. Чтобы использовать исходный код, как показано в листинге, назовите новый проект *NavigationViewCppWinRT*.

```cppwinrt
// MainPage.idl
runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
{
    ...
    Double NavViewCompactModeThresholdWidth{ get; };
}

// pch.h
...
#include "winrt/Windows.UI.Xaml.Input.h"
#include "winrt/Windows.UI.Xaml.Media.Animation.h"
#include "winrt/Microsoft.UI.Xaml.Controls.h"
#include "winrt/Microsoft.UI.Xaml.XamlTypeInfo.h"

// MainPage.h
#pragma once

#include "MainPage.g.h"

namespace muxc
{
    using namespace winrt::Microsoft::UI::Xaml::Controls;
};

namespace wuxc
{
    using namespace winrt::Windows::UI::Xaml::Controls;
};

namespace winrt::NavigationViewCppWinRT::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
        MainPage()
        {
            InitializeComponent();
            m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>(L"home", winrt::xaml_typename<NavigationViewCppWinRT::HomePage>()));
            m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>(L"apps", winrt::xaml_typename<NavigationViewCppWinRT::AppsPage>()));
            m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>(L"games", winrt::xaml_typename<NavigationViewCppWinRT::GamesPage>()));
            m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>(L"music", winrt::xaml_typename<NavigationViewCppWinRT::MusicPage>()));
        }

        double MainPage::NavViewCompactModeThresholdWidth()
        {
            return NavView().CompactModeThresholdWidth();
        }

        void ContentFrame_NavigationFailed(Windows::Foundation::IInspectable const& /* sender */, Windows::UI::Xaml::Navigation::NavigationFailedEventArgs const& args)
        {
            throw winrt::hresult_error(E_FAIL, winrt::hstring(L"Failed to load Page ") + args.SourcePageType().Name);
        }

        // List of ValueTuple holding the Navigation Tag and the relative Navigation Page
        std::vector<std::pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>> m_pages;

        void NavView_Loaded(Windows::Foundation::IInspectable const& /* sender */, Windows::UI::Xaml::RoutedEventArgs const& /* args */)
        {
            // You can also add items in code.
            NavView().MenuItems().Append(muxc::NavigationViewItemSeparator());
            muxc::NavigationViewItem navigationViewItem;
            navigationViewItem.Content(winrt::box_value(L"My content"));
            navigationViewItem.Icon(wuxc::SymbolIcon(static_cast<wuxc::Symbol>(0xF1AD)));
            navigationViewItem.Tag(winrt::box_value(L"content"));
            NavView().MenuItems().Append(navigationViewItem);
            m_pages.push_back(std::make_pair<std::wstring, Windows::UI::Xaml::Interop::TypeName>(L"content", winrt::xaml_typename<NavigationViewCppWinRT::MyContentPage>()));

            // Add handler for ContentFrame navigation.
            ContentFrame().Navigated({ this, &MainPage::On_Navigated });

            // NavView doesn't load any page by default, so load home page.
            NavView().SelectedItem(NavView().MenuItems().GetAt(0));
            // If navigation occurs on SelectionChanged, this isn't needed.
            // Because we use ItemInvoked to navigate, we need to call Navigate
            // here to load the home page.
            NavView_Navigate(L"home", Windows::UI::Xaml::Media::Animation::EntranceNavigationTransitionInfo());

            // Add keyboard accelerators for backwards navigation.
            Windows::UI::Xaml::Input::KeyboardAccelerator goBack;
            goBack.Key(Windows::System::VirtualKey::GoBack);
            goBack.Invoked({ this, &MainPage::BackInvoked });
            KeyboardAccelerators().Append(goBack);

            // ALT routes here
            Windows::UI::Xaml::Input::KeyboardAccelerator altLeft;
            goBack.Key(Windows::System::VirtualKey::Left);
            goBack.Modifiers(Windows::System::VirtualKeyModifiers::Menu);
            goBack.Invoked({ this, &MainPage::BackInvoked });
            KeyboardAccelerators().Append(altLeft);
        }

        void MainPage::NavView_ItemInvoked(Windows::Foundation::IInspectable const& /* sender */, muxc::NavigationViewItemInvokedEventArgs const& args)
        {
            if (args.IsSettingsInvoked())
            {
                NavView_Navigate(L"settings", args.RecommendedNavigationTransitionInfo());
            }
            else if (args.InvokedItemContainer())
            {
                NavView_Navigate(winrt::unbox_value_or<winrt::hstring>(args.InvokedItemContainer().Tag(), L"").c_str(), args.RecommendedNavigationTransitionInfo());
            }
        }

        // NavView_SelectionChanged is not used in this example, but is shown for completeness.
        // You will typically handle either ItemInvoked or SelectionChanged to perform navigation,
        // but not both.
        void NavView_SelectionChanged(muxc::NavigationView const& /* sender */, muxc::NavigationViewSelectionChangedEventArgs const& args)
        {
            if (args.IsSettingsSelected())
            {
                NavView_Navigate(L"settings", args.RecommendedNavigationTransitionInfo());
            }
            else if (args.SelectedItemContainer())
            {
                NavView_Navigate(winrt::unbox_value_or<winrt::hstring>(args.SelectedItemContainer().Tag(), L"").c_str(), args.RecommendedNavigationTransitionInfo());
            }
        }

        void NavView_Navigate(std::wstring navItemTag, Windows::UI::Xaml::Media::Animation::NavigationTransitionInfo const& transitionInfo)
        {
            Windows::UI::Xaml::Interop::TypeName pageTypeName;
            if (navItemTag == L"settings")
            {
                pageTypeName = winrt::xaml_typename<NavigationViewCppWinRT::SettingsPage>();
            }
            else
            {
                for (auto&& eachPage : m_pages)
                {
                    if (eachPage.first == navItemTag)
                    {
                        pageTypeName = eachPage.second;
                        break;
                    }
                }
            }
            // Get the page type before navigation so you can prevent duplicate
            // entries in the backstack.
            Windows::UI::Xaml::Interop::TypeName preNavPageType = ContentFrame().CurrentSourcePageType();

            // Navigate only if the selected page isn't currently loaded.
            if (pageTypeName.Name != L"" && preNavPageType.Name != pageTypeName.Name)
            {
                ContentFrame().Navigate(pageTypeName, nullptr, transitionInfo);
            }
        }

        void NavView_BackRequested(muxc::NavigationView const& /* sender */, muxc::NavigationViewBackRequestedEventArgs const& /* args */)
        {
            On_BackRequested();
        }

        void BackInvoked(Windows::UI::Xaml::Input::KeyboardAccelerator const& /* sender */, Windows::UI::Xaml::Input::KeyboardAcceleratorInvokedEventArgs const& args)
        {
            On_BackRequested();
            args.Handled(true);
        }

        bool On_BackRequested()
        {
            if (!ContentFrame().CanGoBack())
                return false;

            // Don't go back if the nav pane is overlaid.
            if (NavView().IsPaneOpen() &&
                (NavView().DisplayMode() == muxc::NavigationViewDisplayMode::Compact ||
                    NavView().DisplayMode() == muxc::NavigationViewDisplayMode::Minimal))
                return false;

            ContentFrame().GoBack();
            return true;
        }

        void On_Navigated(Windows::Foundation::IInspectable const& /* sender */, Windows::UI::Xaml::Navigation::NavigationEventArgs const& args)
        {
            NavView().IsBackEnabled(ContentFrame().CanGoBack());

            if (ContentFrame().SourcePageType().Name == winrt::xaml_typename<NavigationViewCppWinRT::SettingsPage>().Name)
            {
                // SettingsItem is not part of NavView.MenuItems, and doesn't have a Tag.
                NavView().SelectedItem(NavView().SettingsItem().as<muxc::NavigationViewItem>());
                NavView().Header(winrt::box_value(L"Settings"));
            }
            else if (ContentFrame().SourcePageType().Name != L"")
            {
                for (auto&& eachPage : m_pages)
                {
                    if (eachPage.second.Name == args.SourcePageType().Name)
                    {
                        for (auto&& eachMenuItem : NavView().MenuItems())
                        {
                            auto navigationViewItem = eachMenuItem.try_as<muxc::NavigationViewItem>();
                            {
                                if (navigationViewItem)
                                {
                                    winrt::hstring hstringValue = winrt::unbox_value_or<winrt::hstring>(navigationViewItem.Tag(), L"");
                                    if (hstringValue == eachPage.first)
                                    {
                                        NavView().SelectedItem(navigationViewItem);
                                        NavView().Header(navigationViewItem.Content());
                                    }
                                }
                            }
                        }
                        break;
                    }
                }
            }
        }
    };
}

namespace winrt::NavigationViewCppWinRT::factory_implementation
{
    struct MainPage : MainPageT<MainPage, implementation::MainPage>
    {
    };
}

// MainPage.cpp
#include "pch.h"
#include "MainPage.h"
#include "MainPage.g.cpp"

namespace winrt::NavigationViewCppWinRT::implementation
{
}
```

### <a name="alternative-cwinrt-implementation"></a>Альтернативная реализация C++/WinRT

Код C# и C++/WinRT, показанный выше, написан так, чтобы вы могли использовать одну и ту же разметку XAML для обеих версий. Но существует и другой способ реализации версии C++/WinRT, описанной в этом разделе, который может быть предпочтительным.

Ниже показана альтернативная версия обработчика **NavView_ItemInvoked**. Методика, используемая с этой версией обработчика, предполагает, что вы сначала сохраняете (в теге [**NavigationViewItem**](/uwp/api/windows.ui.xaml.controls.navigationviewitem)) полное имя типа страницы, к которой необходимо перейти. В обработчике вы распаковываете это значение, преобразуете его в объект [**Windows::UI::Xaml::Interop::TypeName**](/uwp/api/windows.ui.xaml.interop.typename), который используете для перехода на целевую страницу. Переменная сопоставления `_pages`, приведенная в примерах выше, не нужна. Вы сможете создавать модульные тесты, подтверждающие, что значения внутри ваших тегов имеют допустимый тип. Дополнительные сведения см. в статье [Упаковка-преобразование и распаковка-преобразование скалярных значений в IInspectable с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/boxing).

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

## <a name="hierarchical-navigation"></a>Иерархическая навигация
Некоторые приложения могут иметь более сложную иерархическую структуру, которая требует больше, чем просто неструктурированный список элементов навигации. Для отображения категорий страниц можно использовать элементы навигации верхнего уровня, при этом дочерние элементы отображают определенные страницы. Это также полезно при наличии сводных страниц, которые связаны только с другими страницами. В подобных случаях следует создать иерархическое представление NavigationView.

Чтобы отобразить иерархический список вложенных элементов навигации на панели, используйте свойство [MenuItems](/uwp/api/microsoft.ui.xaml.controls.navigationviewitem.menuitems?view=winui-2.4) или свойство [MenuItemsSource](/uwp/api/microsoft.ui.xaml.controls.navigationviewitem.menuitemssource?view=winui-2.4) объекта **NavigationViewItem**.
Каждый объект NavigationViewItem может содержать другие объекты NavigationViewItem и организационные элементы, такие как заголовки элементов и разделители. Чтобы отобразить иерархический список при использовании `MenuItemsSource`, назначьте `ItemTemplate` объектом NavigationViewItem и привяжите его свойство `MenuItemsSource` к следующему уровню иерархии.

Хотя объект NavigationViewItem может содержать любое количество вложенных уровней, рекомендуется не делать иерархию навигации вашего приложения излишне глубокой. Мы считаем, что двухуровневая навигация идеально сочетает удобство использования и понятность.

Представление NavigationView отображает иерархию режимах отображения Top, Left и LeftCompact. Вот как выглядит развернутое поддерево в каждом из режимов отображения панели:

![Представление NavigationView с иерархией](images/navigation-view-hierarchy-labeled.png)

### <a name="adding-a-hierarchy-of-items-in-markup"></a>Добавление иерархии элементов в разметку
Объявление иерархии навигации по приложению в разметке.

```Xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<muxc:NavigationView>
    <muxc:NavigationView.MenuItems>
        <muxc:NavigationViewItem Content="Home" Icon="Home" ToolTipService.ToolTip="Home"/>
        <muxc:NavigationViewItem Content="Collections" Icon="Keyboard" ToolTipService.ToolTip="Collections">
            <muxc:NavigationViewItem.MenuItems>
                <muxc:NavigationViewItem Content="Notes" Icon="Page" ToolTipService.ToolTip="Notes"/>
                <muxc:NavigationViewItem Content="Mail" Icon="Mail" ToolTipService.ToolTip="Mail"/>
            </muxc:NavigationViewItem.MenuItems>
        </muxc:NavigationViewItem>
    </muxc:NavigationView.MenuItems>
</muxc:NavigationView>
```

### <a name="adding-a-hierarchy-of-items-using-data-binding"></a>Добавление иерархии элементов с помощью привязки данных

Добавление иерархии пунктов меню в представления NavigationView с помощью 
* привязки свойства MenuItemsSource к иерархическим данным
* определения шаблона элемента как объекта NavigationViewMenuItem, когда его содержимое должно быть меткой элемента меню, а свойство MenuItemsSource привязано к следующему уровню иерархии

В этом примере также демонстрируются события [развертывание](/uwp/api/microsoft.ui.xaml.controls.navigationview.expanding?view=winui-2.4) и [свертывание](/uwp/api/microsoft.ui.xaml.controls.navigationview.collapsed?view=winui-2.4). Эти события вызываются для пункта меню с дочерними элементами.

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<DataTemplate x:Key="NavigationViewMenuItem" x:DataType="local:Category">
    <muxc:NavigationViewItem Content="{x:Bind Name}" MenuItemsSource="{x:Bind Children}"/>
</DataTemplate>
<muxc:NavigationView x:Name="navview" 
    MenuItemsSource="{x:Bind categories, Mode=OneWay}" 
    MenuItemTemplate="{StaticResource NavigationViewMenuItem}" 
    ItemInvoked="{x:Bind OnItemInvoked}" 
    Expanding="OnItemExpanding" 
    Collapsed="OnItemCollapsed" 
    PaneDisplayMode="Left">
    
    <StackPanel Margin="10,10,0,0">
        <TextBlock Margin="0,10,0,0" x:Name="ExpandingItemLabel" Text="Last Expanding: N/A"/>
        <TextBlock x:Name="CollapsedItemLabel" Text="Last Collapsed: N/A"/>
    </StackPanel>    
</muxc:NavigationView>
```

```csharp
public class Category
{
    public String Name { get; set; }
    public String Icon { get; set; }
    public ObservableCollection<Category> Children { get; set; }
}
    
public sealed partial class HierarchicalNavigationViewDataBinding : Page
{
    public HierarchicalNavigationViewDataBinding()
    {
        this.InitializeComponent();
    }  
    
    public ObservableCollection<Category> Categories = new ObservableCollection<Category>()
    {
        new Category(){
            Name = "Menu Item 1",
            Icon = "Icon",
            Children = new ObservableCollection<Category>() {
               new Category(){
                    Name = "Menu Item 2",
                    Icon = "Icon",
                    Children = new ObservableCollection<Category>() {
                        new Category() { 
                            Name  = "Menu Item 2", 
                            Icon = "Icon",
                            Children = new ObservableCollection<Category>() {
                                new Category() { Name  = "Menu Item 3", Icon = "Icon" },
                                new Category() { Name  = "Menu Item 4", Icon = "Icon" }
                            }
                        }
                    }
                }
            }
        },
        new Category(){
            Name = "Menu Item 5",
            Icon = "Icon",
            Children = new ObservableCollection<Category>() {
                new Category(){
                    Name = "Menu Item 6",
                    Icon = "Icon",
                    Children = new ObservableCollection<Category>() {
                        new Category() { Name  = "Menu Item 7", Icon = "Icon" },
                        new Category() { Name  = "Menu Item 8", Icon = "Icon" }
                    }
                }
            }
        },
        new Category(){ Name = "Menu Item 9", Icon = "Icon" }
    };
    private void OnItemInvoked(object sender, NavigationViewItemInvokedEventArgs e)
    {
        var clickedItem = e.InvokedItem;
        var clickedItemContainer = e.InvokedItemContainer;
    }
    private void OnItemExpanding(object sender, NavigationViewItemExpandingEventArgs e)
    {
        var nvib = e.ExpandingItemContainer;
        var name = "Last Expanding: " + nvib.Content.ToString();
        ExpandingItemLabel.Text = name;
    }
    private void OnItemCollapsed(object sender, NavigationViewItemCollapsedEventArgs e)
    {
        var nvib = e.CollapsedItemContainer;
        var name = "Last Collapsed: " + nvib.Content;
        CollapsedItemLabel.Text = name;
    }
}
```
### <a name="selection"></a>Выбор
По умолчанию любой элемент может содержать дочерние элементы, быть вызванным или выбранным.
Предоставляя пользователям иерархическое дерево вариантов навигации можно сделать родительские элементы невыбираемыми, например, если у приложения нет конечной страницы, связанной с этим родительским элементом. Если родительские элементы _доступны_ для выбора, рекомендуется использовать режимы представления Left-Expanded или Top. Режим LeftCompact приведет к тому, что пользователь будет переходить к родительскому элементу, чтобы открывать дочернее дерево при каждом вызове элемента.

Индикаторы выбора будут отрисовываться на выбранных элементах вдоль левого края в режиме Left или на нижней границе в режиме Top. Ниже показаны объекты NavigationView в режиме Left и Top, если выбран родительский элемент.

![Представление NavigationView в режиме Left с выбранным родительским элементом](images/navigation-view-selection.png)

![Представление NavigationView в режиме Top с выбранным родительским элементом](images/navigation-view-selection-top.png)

Выбранный элемент может не всегда оставаться видимым. Если выбран дочерний элемент в свернутом или развернутом поддереве, то их первый видимый предок будет показан как выбранный. Индикатор выделения вернется к выбранному элементу при развертывании поддерева.

Например, на рисунке выше пользователь может выбрать элемент Календаря, а затем может свернуть его поддерево. В этом случае индикатор выбора будет отображаться под элементом учетной записи, так как учетная запись является первым видимым предком в Календаре. Индикатор выделения вернется к элементу Календаря, когда пользователь снова развернет поддерево. 

Представление NavigationView будет отображать не более одного индикатора выбора.

В режимах Top и Left при щелчке по стрелкам в объектах NavigationViewItem поддерево разворачивается или сворачивается. Если щелкнуть или коснуться в другом месте _помимо_ NavigationViewItem, будет активировано событие `ItemInvoked`, а также будет свернуто или развернуто поддерево.

Чтобы элемент не отображал индикатор выбора при вызове, установите для свойства [SelectsOnInvoked](/uwp/api/microsoft.ui.xaml.controls.navigationviewitem.selectsoninvoked?view=winui-2.3) значение false, как показано ниже:

```xaml
<!-- xmlns:muxc="using:Microsoft.UI.Xaml.Controls" -->
<DataTemplate x:Key="NavigationViewMenuItem" x:DataType="local:Category">
    <muxc:NavigationViewItem Content="{x:Bind Name}" 
        MenuItemsSource="{x:Bind Children}"
        SelectsOnInvoked="{x:Bind IsLeaf}" />
</DataTemplate>
<muxc:NavigationView x:Name="navview" 
    MenuItemsSource="{x:Bind categories, Mode=OneWay}" 
    MenuItemTemplate="{StaticResource NavigationViewMenuItem}">
   
</muxc:NavigationView>
```

```csharp
public class Category
{
    public String Name { get; set; }
    public String Icon { get; set; }
    public ObservableCollection<Category> Children { get; set; }
    public bool IsLeaf { get; set; }
}
    
public sealed partial class HierarchicalNavigationViewDataBinding : Page
{
    public HierarchicalNavigationViewDataBinding()
    {
        this.InitializeComponent();
    }      
    
    public ObservableCollection<Category> Categories = new ObservableCollection<Category>()
    {
        new Category(){
            Name = "Menu Item 1",
            Icon = "Icon",
            Children = new ObservableCollection<Category>() {
                new Category(){
                    Name = "Menu Item 2",
                    Icon = "Icon",
                    Children = new ObservableCollection<Category>() {
                        new Category() { 
                            Name  = "Menu Item 2", 
                            Icon = "Icon",
                            Children = new ObservableCollection<Category>() {
                                new Category() { Name  = "Menu Item 3", Icon = "Icon", IsLeaf = true },
                                new Category() { Name  = "Menu Item 4", Icon = "Icon", IsLeaf = true }
                            }
                        }
                    }
                }
            }
        },
        new Category(){
            Name = "Menu Item 5",
            Icon = "Icon",
            Children = new ObservableCollection<Category>() {
                new Category(){
                    Name = "Menu Item 6",
                    Icon = "Icon",
                    Children = new ObservableCollection<Category>() {
                        new Category() { Name  = "Menu Item 7", Icon = "Icon", IsLeaf = true },
                        new Category() { Name  = "Menu Item 8", Icon = "Icon", IsLeaf = true }
                    }
                }
            }
        },
        new Category(){ Name = "Menu Item 9", Icon = "Icon", IsLeaf = true }
    };
}
```

### <a name="keyboarding-within-hierarchical-navigationview"></a>Навигация с помощью клавиатуры в иерархическом представлении NavigationView
Пользователи могут перемещать фокус по представлению навигации с помощью [клавиатуры](/windows/uwp/design/input/keyboard-interactions). Клавиши со стрелками позволяют реализовать "внутреннюю навигацию" по панели и взаимодействуют с объектами в [представлении дерева](/windows/uwp/design/controls-and-patterns/tree-view). Действия клавиш изменяются при навигации по представлению NavigationView или его всплывающем меню, которое отображается в режимах Top и Left представления HierarchicalNavigationView. Ниже приведены действия, которые каждая клавиша может выполнить в иерархическом представлении NavigationView:

| Клавиши      |      В режиме Left      |  В режиме Top | Во всплывающем меню  |
|----------|------------------------|--------------|------------|
| Up |Перемещает фокус на элемент непосредственно над элементом, на котором фокус находится сейчас. | Не выполняет никаких действий. |Перемещает фокус на элемент непосредственно над элементом, на котором фокус находится сейчас.|
| Down|Перемещает фокус на элемент непосредственно под элементом, на котором фокус находится сейчас.* | Не выполняет никаких действий. | Перемещает фокус на элемент непосредственно под элементом, на котором фокус находится сейчас.* |
| Правый |Не выполняет никаких действий.  |Перемещает фокус на элемент непосредственно справа от элемента, на котором фокус находится сейчас. |Не выполняет никаких действий.|
| Левый |Не выполняет никаких действий. | Перемещает фокус на элемент непосредственно слева от элемента, на котором фокус находится сейчас.  |Не выполняет никаких действий. |
| ПРОБЕЛ/ВВОД |Если элемент содержит дочерние элементы, разворачивает или сворачивает элемент и не меняет фокус.   | Если элемент содержит дочерние элементы, разворачивает дочерние элементы во всплывающий элемент и помещает фокус на первый элемент во всплывающем элементе. | Вызывает или выбирает элемент и закрывает всплывающий элемент. |
| ESC | Не выполняет никаких действий. | Не выполняет никаких действий. | Закрывает всплывающий элемент.|

Пробел или клавиша ВВОД всегда вызывает или выбирает элемент.

*Обратите внимание, что элементы не должны быть визуально смежными, фокус будет перемещен от последнего элемента в списке панели к элементу параметров. 

## <a name="navigation-view-customization"></a>Настройка представления навигации

### <a name="pane-backgrounds"></a>Фоновый цвет панелей

По умолчанию панель NavigationView использует фоновый цвет в зависимости от режима отображения:

- при развертывании слева рядом с содержимым (в режиме отображения слева) цветом фона панели является сплошной серый цвет;
- при открытии панели в качестве наложения поверх содержимого используется акрил приложения (в режиме отображения сверху, минимальном или компактном режимах).

Чтобы изменить фоновый цвет панели, вы можете переопределить ресурсы темы XAML, используемые для отображения фона в каждом режиме. (Для обеспечения поддержки разных фоновых цветов для различных режимов отображения обычно используется этот метод вместо свойства PaneBackground.)

В приведенной ниже таблице описано использование ресурсов темы в каждом режиме отображения.

| Режим отображения | Ресурсы темы |
| ------------ | -------------- |
| Левый | NavigationViewExpandedPaneBackground |
| LeftCompact<br/>LeftMinimal | NavigationViewDefaultPaneBackground |
| Начало | NavigationViewTopPaneBackground |

В этом примере показано, как переопределить ресурсы темы в файле App.xaml. При переопределении ресурсов темы всегда необходимо предоставлять как минимум словарь ресурсов по умолчанию и HighContrast, а также при необходимости словари для светлых или темных ресурсов. Дополнительные сведения см. в статье о свойстве [ResourceDictionary.ThemeDictionaries](/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries).

> [!IMPORTANT]
> В этом коде показано использование [библиотеки интерфейса пользователя Windows](/uwp/toolkits/winui/) для AcrylicBrush. Если вы используете для AcrylicBrush версию платформы, минимальной версией пакета SDK проекта приложения должна быть версия 16299 или выше. Для использования версии платформы удалите все ссылки на `muxm:`.

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

### <a name="top-whitespace"></a>Пустое пространство вверху
В некоторых приложениях можно [настроить заголовок окна](/windows/uwp/design/shell/title-bar), чтобы расширить содержимое приложения за счет этой области. Если NavigationView является корневым элементом в приложениях, в которых с помощью **API [ExtendViewIntoTitleBar](/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar.extendviewintotitlebar)** можно выполнить такое расширение, элемент управления автоматически скорректирует расположение интерактивных элементов, чтобы не допустить перекрытия с [перетаскиваемой областью](/windows/uwp/design/shell/title-bar#draggable-regions). 
![Приложение, расширяющееся за счет строки заголовка](images/navigation-view-with-titlebar-padding.png)

Если приложение определяет перетаскиваемую область, вызывая метод [Window.SetTitleBar](/uwp/api/windows.ui.xaml.window.settitlebar), а вы хотите, чтобы кнопка назад и кнопка меню располагались ближе к верхней части окна приложения, установите для `IsTitleBarAutoPaddingEnabled` значение false.

![Приложение, расширяющееся за счет строки заголовка без дополнительного заполнения](images/navigation-view-no-titlebar-padding.png)

```Xaml
<muxc:NavigationView x:Name="NavView" IsTitleBarAutoPaddingEnabled="False">
```

#### <a name="remarks"></a>Замечания
Чтобы скорректировать расположение области заголовка NavigationView, переопределите ресурс темы XAML *NavigationViewHeaderMargin* (например, в ресурсах страницы).

```Xaml
<Page.Resources>
    <Thickness x:Key="NavigationViewHeaderMargin">12,0</Thickness>
</Page.Resources>
```

Этот ресурс темы изменяет поля вокруг [NavigationView.Header](/uwp/api/windows.ui.xaml.controls.navigationview.header).

## <a name="related-topics"></a>Связанные темы

- [NavigationView Class](/uwp/api/windows.ui.xaml.controls.navigationview) (Класс NavigationView)
- [Основные и подробные данные](master-details.md)
- [Основы навигации](../basics/navigation-basics.md)
- [Общие сведения о Fluent Design](/windows/apps/fluent-design-system)
