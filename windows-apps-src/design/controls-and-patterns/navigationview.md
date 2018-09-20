---
author: QuinnRadich
Description: NavigationView is an adaptive control that implements top-level navigation patterns for your app.
title: Представление навигации
template: detail.hbs
ms.author: quradic
ms.date: 06/25/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: ''
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 6c75169f118e2c8ef575fa251a7badc8cfe44247
ms.sourcegitcommit: 4f6dc806229a8226894c55ceb6d6eab391ec8ab6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2018
ms.locfileid: "4087003"
---
# <a name="navigation-view-preview-version"></a>Представление переходов (Предварительная версия)

> **Это предварительная версия**: в этой статье описывается новая версия элемента управления NavigationView, находящегося в разработке. Чтобы использовать его теперь требуется [последние сборки программы предварительной оценки Windows и пакет SDK](https://insider.windows.com/for-developers/) или [Библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Элемент управления NavigationView обеспечивает навигацию верхнего уровня для вашего приложения. Его адаптируется к различным поддерживает размеров экрана несколько стилей навигации.

> **API -интерфейсы библиотеки пользовательского интерфейса Windows**: [класс Microsoft.UI.Xaml.Controls.NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview)

> **API -интерфейсы платформы**: [класс Windows.UI.Xaml.Controls.NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)

## <a name="get-the-windows-ui-library"></a>Получить библиотеку пользовательского интерфейса Windows

Этот элемент управления не включен в библиотеке Windows пользовательского интерфейса, пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке см. в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). 

## <a name="navigation-styles"></a>Стили навигации

Поддерживает NavigationView.

**Левой панели навигации или меню**

![расширены панель навигации](images/displaymode-left.png)

**Верхняя панель навигации или меню**

![Основные навигации](images/displaymode-top.png)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

NavigationView — это элемент управления адаптивной навигации, который лучше всего подойдет для:

- Предоставление единообразия интерфейса навигации во всем приложении.
- Экономия свободного экранного пространства для окон меньшего размера.
- Организации доступа к многие категории навигации.

Для других элементов навигации см. в разделе [основы проектирования навигации](../basics/navigation-basics.md).

Если для навигации требуется более сложное поведение, которое не поддерживается в NavigationView, можно попробовать использовать шаблон [Основные и подробные данные](master-details.md).

:::row:::
    :::column:::
        ![Некоторые изображения](images/XAML-controls-gallery-app-icon.png)
    :::column-end:::
    ::: column span = «2»::: **Галереи элементов управления XAML**<br>
        Если у вас установлено приложение галереи элементов управления XAML, щелкните <a href="xamlcontrolsgallery:/item/NavigationView">здесь</a> , чтобы открыть приложение и увидеть NavigationView в действии.

        <a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Get the XAML Controls Gallery app (Microsoft Store)</a><br>
        <a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Get the source code (GitHub)</a>
    :::column-end:::
:::row-end:::

## <a name="display-modes"></a>Режимы отображения

NavigationView можно задать для различных режимов отображения, через `PaneDisplayMode` свойства:

:::row:::
    :::column:::
    ### Left
    Displays an expanded left positioned pane.
    :::column-end:::
    :::column span="2":::
    ![left nav pane expanded](images/displaymode-left.png)
    :::column-end:::
:::row-end:::

Мы рекомендуем левой навигационной при:

- У вас есть среднего и высокого количества (5-10) категорий менее важно навигации верхнего уровня.
- Желание очень четкой навигационных категорий, используя меньше пространства для другого содержимого приложения.

:::row:::
    :::column:::
    ### Top
    Displays a top positioned pane.
    :::column-end:::
    :::column span="2":::
    ![top navigation](images/displaymode-top.png)
    :::column-end:::
:::row-end:::

Мы рекомендуем верхней навигационной при:

- У вас есть 5 или менее категории менее важно навигации верхнего уровня, таким образом, чтобы все дополнительные навигации верхнего уровня категории, которые в конечном счете, в раскрывающемся списке переполнения меню считаются столь важны.
- Необходимо отобразить все параметры навигации на экране.
- Желание больше места для содержимого приложения.
- Значки невозможно описать четко категорий навигации вашего приложения.

:::row:::
    :::column:::
    ### LeftCompact
    Displays a thin sliver with icons on the left.
    :::column-end:::
    :::column span="2":::
    ![nav pane compact](images/displaymode-leftcompact.png)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
    ### LeftMinimal
    Displays only the menu button.
    :::column-end:::
    :::column span="2":::
    ![nav pane minimal](images/displaymode-leftminimal.png)
    :::column-end:::
:::row-end:::

### <a name="auto"></a>Auto (автоматически)

![Адаптивное поведение GIF левая по умолчанию](images/displaymode-auto.png)

Адаптирует между LeftMinimal на небольшими, LeftCompact на средних экранов и влево на больших экранах. Дополнительные сведения в разделе [адаптивное поведение](#adaptive-behavior) .

## <a name="anatomy"></a>Структура

<b>Левая панель навигации</b><br>

![левый разделы представления навигации](images/leftnav-anatomy.png)

<b>Верхней панели навигации</b><br>

![основные разделы представления навигации](images/topnav-anatomy.png)

## <a name="pane"></a>Панель

Панель может располагаться на верхней или левой части окна, через `PanePosition` свойства.

Вот Анатомия подробные панели для позиции верхний и области.

<b>Левая панель навигации</b><br>

![Анатомия NavigationView](images/navview-pane-anatomy-vertical.png)

1. Кнопка меню
1. Элементы навигации
1. Разделители
1. Заголовки
1. AutoSuggestBox (необязательно)
1. Кнопка «Параметры» (необязательно)

<b>Верхней панели навигации</b><br>

![Анатомия NavigationView](images/navview-pane-anatomy-horizontal.png)

1. Заголовки
1. Элементы навигации
1. Разделители
1. AutoSuggestBox (необязательно)
1. Кнопка «Параметры» (необязательно)

Кнопка «Назад» отображается в верхнем левом углу панели, но NavigationView не добавляет автоматически содержимое в стеке переходов назад. Чтобы реализовать навигацию в обратном направлении, см. в разделе [обратной навигации](#backwards-navigation) раздел.

Панель NavigationView может также содержать:

1. Навигационные элементы в виде [NavigationViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitem), для перехода на определенные страницы.
2. Разделители в виде [NavigationViewItemSeparator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator)для группировки элементов навигации. Задайте свойства [Opacity](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator.opacity) 0 для отрисовки разделитель места.
3. Заголовки в виде [NavigationViewItemHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemheader)для маркировки групп элементов.
4. Необязательные [AutoSuggestBox](auto-suggest-box.md) для поиска на уровне приложения.
5. Необязательная точка входа для [параметров приложения](../app-settings/app-settings-and-data.md). Чтобы скрыть элемент параметров, используйте свойство [IsSettingsVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsSettingsVisible) .

Содержит расположенной слева области:

6. Кнопка меню для включения и выключения панели Открытие и закрытие. Когда панель открыта в более крупных окнах приложения, вы можете скрыть эту кнопку с помощью свойства [IsPaneToggleButtonVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsPaneToggleButtonVisible).

### <a name="pane-footer"></a>Нижнем колонтитуле панели

содержимое в произвольном формате в нижнем колонтитуле панели при добавлении в свойство [PaneFooter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneFooter).

:::row:::
    :::column:::
    <b>Левая панель навигации</b><br>
    ![Навигации левом нижнем колонтитуле панели](images/navview-freeform-footer-left.png)<br>
    :::column-end:::
    :::column:::
     <b>Верхней панели навигации</b><br>
    ![Верхней панели навигации заголовка панели](images/navview-freeform-footer-top.png)<br>
    :::column-end:::
:::row-end:::

### <a name="pane-header"></a>Области заголовка

Содержимое в области заголовка, при добавлении к свойству [PaneHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneHeader)

:::row:::
    :::column:::
    <b>Левая панель навигации</b><br>
    ![Области заголовка левой навигации](images/navview-freeform-header-left.png)<br>
    :::column-end:::
    :::column:::
     <b>Верхней панели навигации</b><br>
    ![Верхней панели навигации заголовка панели](images/navview-freeform-header-top.png)<br>
    :::column-end:::
:::row-end:::

### <a name="pane-content"></a>Область содержимого

Содержимое в области, при добавлении к свойству [PaneCustomContent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneCustomContent)

:::row:::
    :::column:::
    <b>Левая панель навигации</b><br>
    ![Панель навигации настраиваемых contentleft](images/navview-freeform-pane-left.png)<br>
    :::column-end:::
    :::column:::
     <b>Верхней панели навигации</b><br>
    ![Пользовательские панели содержимого верхнего навигации](images/navview-freeform-pane-top.png)<br>
    :::column-end:::
:::row-end:::

### <a name="visual-style"></a>Визуальный стиль

При соблюдении требований к оборудованию и программному обеспечению NavigationView автоматически использует [Акриловый материал](../style/acrylic.md) на панели и [эффект](../style/reveal.md) только на левой панели.

## <a name="header"></a>Заголовок

![navview стандартное изображение области заголовка](images/nav-header.png)

Области заголовка вертикально выравнивается с кнопкой навигацией в левой области положении и располагается под области в положении верхней панели. Он имеет фиксированную высоту в 52 пикс. Она содержит заголовок страницы категории выбранного навигации. Заголовок закреплен в верхней части страницы и выступает в качестве точки отсечения прокрутки для области содержимого.

Заголовок должен быть видимым, когда NavigationView находится в режиме минимальной отображения. Вы можете скрыть заголовок в других режимах, используемых для окон большей ширины. Для этого задайте свойству [AlwaysShowHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.AlwaysShowHeader) значение **false**.

## <a name="content"></a>Содержимое

![navview стандартное изображение области содержимого](images/nav-content.png)

Область содержимого — это область, в которой отображаются основные сведения для выбранной категории навигации.

Мы рекомендуем использовать поля в 12 пикселей для области содержимого, когда NavigationView находится в минимальном режиме отображения; или в противном случае поля в 24 пикселя.

## <a name="adaptive-behavior"></a>Адаптивное поведение

NavigationView автоматически изменяет режим отображения в зависимости от доступного экранного пространства. Тем не менее может потребоваться настроить поведение режима адаптивный экран.

### <a name="default"></a>По умолчанию

Адаптивное поведение по умолчанию NavigationView — это для отображения развернутого левой для больших окон ширины, левую только значок панель навигации для ширины среднее окно и кнопки меню гамбургер для ширины небольшого окна. Дополнительные сведения о размеров окон для адаптивное поведение см. в разделе [размеров экрана и точки останова](../layout/screen-sizes-and-breakpoints-for-responsive-design.md).

![Адаптивное поведение GIF левая по умолчанию](images/displaymode-auto.png)

```xaml
<NavigationView />
```

### <a name="minimal"></a>Минимальный

Второй распространенный шаблон адаптивного — использовать расширенные расположенной слева области на больших окон ширины и гамбургера для обоих ширины окна средних и мелких.

![Адаптивное поведение левая GIF 2](images/adaptive-behavior-minimal.png)

```xaml
<NavigationView CompactModeThresholdWidth="1008" ExpandedModeThresholdWidth="1007" />
```

Мы рекомендуем использовать этот формат, если:

- Желание больше места для содержимого приложения на более мелкие ширины окна.
- Ваш категорий навигации не могут быть представлены четко со значками.

### <a name="compact"></a>Компактный

Третий распространенный шаблон адаптивного — использовать расширенные расположенной слева области на больших окон ширины и левую только значок панель навигации для обоих ширины окна средних и мелких. Хорошим примером этого является приложение "Почта".

![Адаптивное поведение левая GIF 3](images/adaptive-behavior-compact.png)

```xaml
<NavigationView CompactModeThresholdWidth="0" ExpandedModeThresholdWidth="1007" />
```

Мы рекомендуем использовать этот формат, если:

- Очень важно всегда отображаться все параметры навигации на экране.
- Ваш категорий навигации могут появляться четко со значками.

### <a name="no-adaptive-behavior"></a>Нет адаптивное поведение

Иногда вам может не требоваться все адаптивное поведение вообще. Вы можете задать панели, чтобы всегда быть развернут, всегда compact или всегда минимальной.

![Адаптивное поведение левая GIF 4](images/adaptive-behavior-none.png)

```xaml
<NavigationView PaneDisplayMode="LeftMinimal" />
```

### <a name="top-to-left-navigation"></a>Сверху левой навигационной

Мы рекомендуем использовать верхней навигационной большие размеры экрана и левой навигационной на небольшие размеры окна, когда:

- У вас есть набор одинаково важные навигации верхнего уровня категорий для отображения друг с другом, таким образом, если одну категорию в этот набор не помещаются на экране, можно свернуть, чтобы левой навигационной, чтобы предоставить им одинаково важны.
- Вы хотите сохранить доступного содержимого места максимально небольшое окно размеров.

Пример.

![Адаптивное поведение GIF верхней или левой навигации 1](images/navigation-top-to-left.png)

```xaml
<Grid >
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup>
            <VisualState>
                <VisualState.StateTriggers>
                    <AdaptiveTrigger MinWindowWidth="{x:Bind NavigationViewControl.CompactModeThresholdWidth}" />
                </VisualState.StateTriggers>

                <VisualState.Setters>
                    <Setter Target="NavigationViewControl.PaneDisplayMode" Value="Top"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>

    <NavigationView x:Name="NavigationViewControl" >
        <NavigationView.MenuItems>
            <NavigationViewItem Content="A" x:Name="A" />
            <NavigationViewItem Content="B" x:Name="B" />
            <NavigationViewItem Content="C" x:Name="C" />
        </NavigationView.MenuItems>
    </NavigationView>
</Grid>

```

Иногда приложения необходимо привязать различные данные на верхней панели и левой панели. Представитель дополнительные элементы навигации в левой области.

Пример.

![Адаптивное поведение навигации верхней или левой GIF 2](images/navigation-top-to-left2.png)

```xaml
<Page >
    <Page.Resources>
        <DataTemplate x:name="navItem_top_temp" x:DataType="models:Item">
            <NavigationViewItem Background= Icon={x:Bind TopIcon}, Content={x:Bind TopContent}, Visibility={x:Bind TopVisibility} />
        </DataTemplate>

        <DataTemplate x:name="navItem_temp" x:DataType="models:Item">
            <NavigationViewItem Icon={x:Bind Icon}, Content={x:Bind Content}, Visibility={x:Bind Visibility} />
        </DataTemplate>
        
        <services:NavViewDataTemplateSelector x:Key="navview_selector" 
              NavItemTemplate="{StaticResource navItem_temp}" 
              NavItemTopTemplate="{StaticResource navItem_top_temp}" 
              NavPaneDisplayMode="{x:Bind NavigationViewControl.PaneDisplayMode}">
        </services:NavViewDataTemplateSelector>
    </Page.Resources>

    <Grid >
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup>
                <VisualState>
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="{x:Bind NavigationViewControl.CompactModeThresholdWidth}" />
                    </VisualState.StateTriggers>

                    <VisualState.Setters>
                        <Setter Target="NavigationViewControl.PaneDisplayMode" Value="Top"/>
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <NavView x:Name='NavigationViewControl' MenuItemsSource={x:Bind items}   
                 PanePosition = "Top" MenuItemTemplateSelector="navview_selector" />
    </Grid>
</Page>

```

```csharp
ObservableCollection<Item> items = new ObservableCollection<Item>();
items.Add(new Item() {
    Content = "Aa",
    TopContent ="A",
    Icon = new BitmapIcon() { UriSource = new Uri("ms-appx:///testimage.jpg") },
    TopIcon = new BitmapIcon(),
    ItemVisibility = Visibility.Visible,
    TopItemVisiblity = Visibility.Visible 
});
items.Add(new Item() {
    Content = "Bb",
    TopContent = "B",
    Icon = new BitmapIcon() { UriSource = new Uri("ms-appx:///testimage.jpg") },
    TopIcon = new BitmapIcon(),
    ItemVisibility = Visibility.Visible,
    TopItemVisiblity = Visibility.Visible 
});
items.Add(new Item() {
    Content = "Cc",
    TopContent = "C",
    Icon = new BitmapIcon() { UriSource = new Uri("ms-appx:///testimage.jpg") },
    TopIcon = new BitmapIcon(),
    ItemVisibility = Visibility.Visible,
    TopItemVisiblity = Visibility.Visible 
});

public class NavViewDataTemplateSelector : DataTemplateSelector
    {
        public DataTemplate NavItemTemplate { get; set; }

        public DataTemplate NavItemTopTemplate { get; set; }    

     public NavigationViewPaneDisplayMode NavPaneDisplayMode { get; set; }

        protected override DataTemplate SelectTemplateCore(object item)
        {
            Item currItem = item as Item;
            if (NavPaneDisplayMode == NavigationViewPanePosition.Top)
                return NavItemTopTemplate;
            else 
                return NavItemTemplate;
        }   

    }

```

## <a name="interaction"></a>Взаимодействие

Когда пользователи выбирают элемент навигации на панели, NavigationView отображает этот элемент как выбранный и вызывает событие [ItemInvoked](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ItemInvoked). Если касание приводит к выбору нового элемента, NavigationView также вызывает событие [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.SelectionChanged).

Ваше приложение отвечает за обновление заголовка и содержимого соответствующей информацией в ответ на взаимодействие с пользователем. Кроме того мы рекомендуем программным путем перемещать [фокус](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.FocusState) от элемента навигации к содержимому. При установке исходного фокуса касания происходит оптимизация процесса перемещения пользователя по интерфейсу и сводится к минимуму предполагаемое число движений фокуса клавиатуры.

### <a name="tabs"></a>Вкладки

В модели вкладки которые привязаны выбора и фокусировки. Действие, обычно парадигм фокус также сместится выбора. В ниже примере правой arrowing бы переместите выбора индикатор отображается Экранная лупа. Этого можно достичь, задав свойство [SelectionFollowsFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.selectionfollowsfocus) включен.

![Снимок экрана только текст верхнего navview](images/nav-tabs.png)

Ниже приведен пример XAML для этого:

```xaml
<NavigationView PanePosition="Top" SelectionFollowsFocus="Enabled" >
   <NavigationView.MenuItems>
        <NavigationViewItem Content="Display" />
        <NavigationViewItem Content="Magnifier"  />
        <NavigationViewItem Content="Keyboard" />
    </NavigationView.MenuItems>
</NavigationView>

```

Для замены содержимого при изменении Выбор вкладки, можно использовать метод [NavigateWithOptions](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.NavigateToType) кадра FrameNavigationOptions.IsNavigationStackEnabled имеет значение False и NavigateOptions.TransitionInfoOverride равным соответствующие стороны в сторону Анимация перетаскивания. Например см. в [примере кода](#code-example) ниже.

Если вы хотите изменить стиль по умолчанию, вы можете переопределить свойство [MenuItemContainerStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.menuitemcontainerstyle) NavigationView. Можно также задать свойство [MenuItemTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.menuitemtemplate) для указания шаблона различных данных.

## <a name="backwards-navigation"></a>Навигация в обратном направлении

В NavigationView есть встроенная кнопка "Назад", которую можно включить с помощью следующих свойств:

- [**IsBackButtonVisible**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsBackButtonVisible) — это перечисление NavigationViewBackButtonVisible по умолчанию. Его значением по умолчанию является "Автоматически". Это свойство используется, чтобы показать или скрыть кнопку "Назад". Если кнопка не отображается, пространство для отрисовки кнопки "Назад" будет свернуто.
- Значением свойства [**IsBackEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsBackEnabled) является "false" по умолчанию. Оно используется для переключения состояний кнопки "Назад".
- [**BackRequested**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.BackRequested) возникает, когда пользователь нажимает кнопку "Назад".
    - В минимальном/компактном режиме отображения, когда панель NavigationView.Pane открыта в виде всплывающего элемента, нажатие кнопки "Назад" приведет к закрытию панели и запуску события **PaneClosing**.
    - Событие не возникает, если значением IsBackEnabled является "false".

:::row:::
    :::column:::
    <b>Левая панель навигации</b><br>
    ![NavigationView кнопки "Назад" на левый навигации](images/leftnav-back.png)
    :::column-end:::
    :::column:::
     <b>Верхней панели навигации</b><br>
    ![NavigationView кнопки "Назад" на верхней панели навигации](images/topnav-back.png)
    :::column-end:::
:::row-end:::

## <a name="code-example"></a>Пример кода

> [!NOTE]
> NavigationView следует использовать в качестве корневого контейнера приложения. Этот элемент управления предназначен для развертывания по всей ширине и высоте окна приложения.
Вы можете переопределить ширину, при которой представление навигации изменяет режим отображения с помощью свойств [CompactModeThresholdWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.CompactModeThresholdWidth) и [ExpandedModeThresholdWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ExpandedModeThresholdWidth)

Ниже приведен пример начала до конца как вы можете внедрить NavigationView верхняя панель навигации на большие размеры экрана и левой навигационной панели на небольших размеры экрана.

В этом примере мы ожидаем, что конечные пользователи часто выбрать новые категории навигации и поэтому мы:

- Присвойте свойству [SelectionFollowsFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PanePostion) включено
- Используйте переходов кадров, которые не добавляйте в стеке навигации.
- Оставьте значение по умолчанию в свойстве [ShoulderNavigationEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PanePostion) , который используется для указания, если левый и правый бамперы на игровом навигации по категориям навигации верхнего уровня вашего приложения. Значение по умолчанию — «WhenSelectionFollowsFocus». Другие возможные значения: «Всегда» и «Никогда».

Мы также покажем, как реализовать обратную навигацию с помощью кнопки "Назад" элемента NavigationView.

Вот запись в этом образце демонстрируются.

![Пример NavigationView End до конца](images/nav-code-example.gif)

Вот пример кода:

> [!NOTE]
> Если вы используете [Библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/), то вам потребуется добавить ссылку на набор средств: `xmlns:controls="using:Microsoft.UI.Xaml.Controls"`.

```xaml
<Page
    x:Class="NavigationViewSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:NavigationViewSample"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <VisualStateManager.VisualStateGroups>
            <VisualStateGroup>
                <VisualState>
                    <VisualState.StateTriggers>
                        <AdaptiveTrigger MinWindowWidth="{x:Bind NavView.CompactModeThresholdWidth}" />
                    </VisualState.StateTriggers>

                    <VisualState.Setters>
                        <Setter Target="NavView.PaneDisplayMode" Value="Top"/>
                    </VisualState.Setters>
                </VisualState>
            </VisualStateGroup>
        </VisualStateManager.VisualStateGroups>

        <NavigationView x:Name="NavView"
                    SelectionFollowsFocus="Enabled"
                    ItemInvoked="NavView_ItemInvoked"
                    IsSettingsVisible="True"
                    Loaded="NavView_Loaded"
                    BackRequested="NavView_BackRequested"
                    Header="Welcome">

            <NavigationView.MenuItems>
                <NavigationViewItem Content="Home" x:Name="home" Tag="home">
                    <NavigationViewItem.Icon>
                        <FontIcon Glyph="&#xE10F;"/>
                    </NavigationViewItem.Icon>
                </NavigationViewItem>
                <NavigationViewItemSeparator/>
                <NavigationViewItemHeader Content="Main pages"/>
                <NavigationViewItem Icon="AllApps" Content="Apps" x:Name="apps" Tag="apps"/>
                <NavigationViewItem Icon="Video" Content="Games" x:Name="games" Tag="games"/>
                <NavigationViewItem Icon="Audio" Content="Music" x:Name="music" Tag="music"/>
            </NavigationView.MenuItems>

            <NavigationView.AutoSuggestBox>
                <AutoSuggestBox x:Name="ASB" QueryIcon="Find"/>
            </NavigationView.AutoSuggestBox>

            <NavigationView.HeaderTemplate>
                <DataTemplate>
                    <Grid Margin="24,10,0,0">
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto"/>
                            <ColumnDefinition/>
                        </Grid.ColumnDefinitions>
                        <TextBlock Style="{StaticResource TitleTextBlockStyle}"
                           FontSize="28"
                           VerticalAlignment="Center"
                           Text="Welcome"/>
                        <CommandBar Grid.Column="1"
                            HorizontalAlignment="Right"
                            VerticalAlignment="Top"
                            DefaultLabelPosition="Right"
                            Background="{ThemeResource SystemControlBackgroundAltHighBrush}">
                            <AppBarButton Label="Refresh" Icon="Refresh"/>
                            <AppBarButton Label="Import" Icon="Import"/>
                        </CommandBar>
                    </Grid>
                </DataTemplate>
            </NavigationView.HeaderTemplate>

            <Frame x:Name="ContentFrame" Margin="24"/>

        </NavigationView>
    </Grid>
</Page>
```

> [!NOTE]
> Если вы используете [Библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/), то вам потребуется добавить ссылку на набор средств: `using MUXC = Microsoft.UI.Xaml.Controls;`.

```csharp
// List of ValueTuple holding the Navigation Tag and the relative Navigation Page 
private readonly IList<(string Tag, Type Page)> _pages = new List<(string Tag, Type Page)>
{
    ("home", typeof(HomePage)),
    ("apps", typeof(AppsPage)),
    ("games", typeof(GamesPage)),
    ("music", typeof(MusicPage)),
};

private void NavView_Loaded(object sender, RoutedEventArgs e)
{
    // You can also add items in code behind
    NavView.MenuItems.Add(new NavigationViewItemSeparator());
    NavView.MenuItems.Add(new NavigationViewItem
    {
        Content = "My content",
        Icon = new SymbolIcon(Symbol.Folder),
        Tag = "content"
    });
    _pages.Add(("content", typeof(MyContentPage)));

    ContentFrame.Navigated += On_Navigated;

    // NavView doesn't load any page by default: you need to specify it
    NavView_Navigate("home");

    // Add keyboard accelerators for backwards navigation
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

private void NavView_ItemInvoked(NavigationView sender, NavigationViewItemInvokedEventArgs args)
{

    if (args.IsSettingsInvoked)
        ContentFrame.Navigate(typeof(SettingsPage));
    else
    {
        // Getting the Tag from Content (args.InvokedItem is the content of NavigationViewItem)
        var navItemTag = NavView.MenuItems
            .OfType<NavigationViewItem>()
            .First(i => args.InvokedItem.Equals(i.Content))
            .Tag.ToString();

        NavView_Navigate(navItemTag);
    }
}

private void NavView_Navigate(string navItemTag)
{
    var item = _pages.First(p => p.Tag.Equals(navItemTag));
    ContentFrame.Navigate(item.Page);
}

private void NavView_BackRequested(NavigationView sender, NavigationViewBackRequestedEventArgs args)
{
    On_BackRequested();
}

private void BackInvoked(KeyboardAccelerator sender, KeyboardAcceleratorInvokedEventArgs args)
{
    On_BackRequested();
    args.Handled = true;
}

private bool On_BackRequested()
{
    if (!ContentFrame.CanGoBack)
        return false;

    // Don't go back if the nav pane is overlayed
    if (NavView.IsPaneOpen &&
        (NavView.DisplayMode == NavigationViewDisplayMode.Compact ||
        NavView.DisplayMode == NavigationViewDisplayMode.Minimal))
        return false;

    ContentFrame.GoBack();
    return true;
}

private void On_Navigated(object sender, NavigationEventArgs e)
{
    NavView.IsBackEnabled = ContentFrame.CanGoBack;

    if (ContentFrame.SourcePageType == typeof(SettingsPage))
    {
        // SettingsItem is not part of NavView.MenuItems, and doesn't have a Tag
        NavView.SelectedItem = (NavigationViewItem)NavView.SettingsItem;
    }
    else
    {
        var item = _pages.First(p => p.Page == e.SourcePageType);

        NavView.SelectedItem = NavView.MenuItems
            .OfType<NavigationViewItem>()
            .First(n => n.Tag.Equals(item.Tag));
    }
}
```

## <a name="customizing-backgrounds"></a>Настройка фона

Чтобы изменить фон главной области NavigationView, задайте его свойству `Background` значение выбранной вами кисти.

Фон панели показывает акрил приложения, когда NavigationView находится в верхней части страницы минимальный, или в компактном режиме. Чтобы изменить это поведение или настроить внешний вид акрила панели, измените два ресурс темы, заменив их в файле App.xaml.

```xaml
<Application.Resources>
    <ResourceDictionary>
        <AcrylicBrush x:Key="NavigationViewDefaultPaneBackground"
        BackgroundSource="Backdrop" TintColor="Yellow" TintOpacity=".6"/>
        <AcrylicBrush x:Key="NavigationViewTopPaneBackground"
        BackgroundSource="Backdrop" TintColor="Yellow" TintOpacity=".6"/>
        <AcrylicBrush x:Key="NavigationViewExpandedPaneBackground"
        BackgroundSource="HostBackdrop" TintColor="Orange" TintOpacity=".8"/>
    </ResourceDictionary>
</Application.Resources>
```

## <a name="scroll-content-under-top-pane"></a>Содержимое прокрутки в верхней панели

Скользил + вид Если ваше приложение содержит страницы, использующие ScrollViewer и панели навигации верхнего в заданное положение, рекомендуется создание содержимого прокрутки под верхней навигационной панели.

Этого можно достичь, задав свойство [CanContentRenderOutsideBounds](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.cancontentrenderoutsidebounds) на соответствующих ScrollViewer значение true.

![области переходов navview прокрутки](images/nav-scroll-content.png)

Если ваше приложение имеет очень длинным прокрутке содержимого, может потребоваться следует рассмотреть возможность включения закрепленные заголовки, которые присоединяются к верхней навигационной панели и формирования плавного поверхности. 

![Залипание заголовок navview прокрутки](images/nav-scroll-stickyheader.png)

Этого можно достичь, задав свойство [ContentOverlay](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ContentOverlay) в NavigationView. 

Иногда Если пользователь прокрутка вниз, может потребоваться скрыть панель навигации, достичь, задав свойство [IsPaneVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ContentOverlay) на NavigationView значение "false".

![navview прокрутки скрыть навигации](images/nav-scroll-hidepane.png)

## <a name="related-topics"></a>Еще по теме:

- [Класс NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)
- [Основные и подробные данные](master-details.md)
- [Элемент управления Pivot](tabs-pivot.md)
- [Основы навигации](../basics/navigation-basics.md)
- [Обзор системы проектирования Fluent Design для UWP](../fluent-design-system/index.md)