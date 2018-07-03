---
author: serenaz
Description: Control that provides top-level app navigation with an automatically adapting, collapsible left navigation menu
title: Представление навигации
ms.assetid: ''
label: Navigation view
template: detail.hbs
ms.author: sezhen
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: vasriram
design-contact: kimsea
dev-contact: mitra
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: c7817bf7ff60a52ea48c988bdebd6d4d2eeacdb7
ms.sourcegitcommit: 618741673a26bd718962d4b8f859e632879f9d61
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2018
ms.locfileid: "1992153"
---
# <a name="navigation-view"></a>Представление навигации

Элемент управления "Представление навигации" предоставляет сворачиваемое меню навигации для навигации верхнего уровня в вашем приложении. Этот элемент управления реализует шаблон панели навигации или меню типа "гамбургер" и автоматически адаптирует режим отображения области к разным размерам окон.

> **Важные API-интерфейсы**: [класс NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview), [класс NavigationViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitem), [перечисление NavigationViewDisplayMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewdisplaymode)

![Пример NavigationView](images/navview_wireframe.png)

## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev010/player]

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

NavigationView хорошо подходит для следующего:

-  Большое число элементов навигации верхнего уровня одинакового типа. (Например, спортивное приложение с категориями "Хоккей", "Волейбол", "Баскетбол", "Футбол" и т. д.)
-  Для среднего и большого количества (5-10) категорий навигаций верхнего уровня.
-  Обеспечение единообразия интерфейса навигации. Панель должна содержать только элементы навигации, но не действия.
-  Экономия свободного экранного пространства для окон меньшего размера.

NavigationView — это только один из нескольких элементов навигации, которые можно использовать. Дополнительные сведения о других шаблонах навигации и элементах см. в разделе [Основы проектирования навигации](../basics/navigation-basics.md).

Элемент управления NavigationView имеет множество встроенных событий, реализующих простой шаблон панели навигации. Если для навигации требуется более сложное поведение, которое не поддерживается в NavigationView, можно попробовать использовать шаблон [Основные и подробные данные](master-details.md).

## <a name="examples"></a>Примеры.
<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/NavigationView">открыть приложение и увидеть NavigationView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="navigationview-sections"></a>Разделы представления навигации

![Разделы представления навигации](images/navview_sections.png)

### <a name="pane"></a>Панель

Встроенная кнопка навигации ("гамбургер") позволяет пользователям открывать и закрывать панель. Когда панель открыта в более крупных окнах приложения, вы можете скрыть эту кнопку с помощью свойства [IsPaneToggleButtonVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsPaneToggleButtonVisible). Текстовая метка рядом с кнопкой-гамбургером — это свойство [PaneTitle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneTitle).

Встроенная кнопка "Назад" отображается в левом верхнем углу панели. NavigationView не добавляет автоматически содержимое в стек переходов назад. Чтобы реализовать навигацию в обратном направлении, см. раздел [Навигация в обратном направлении](#backwards-navigation).

Панель NavigationView может также содержать:

- навигационные элементы в форме [NavigationViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitem) для перехода на определенные страницы;
- разделители в виде [NavigationViewItemSeparator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator) для группировки элементов навигации;
- заголовки в виде [NavigationViewItemHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemheader) для маркировки групп элементов;
- Дополнительное поле [AutoSuggestBox](auto-suggest-box.md) для поиска на уровне приложения
- Необязательная точка входа для [параметров приложения](../app-settings/app-settings-and-data.md). Чтобы скрыть элемент параметров, используйте свойство [IsSettingsVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsSettingsVisible);
- содержимое в произвольном формате в нижнем колонтитуле панели при добавлении в свойство [PaneFooter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneFooter).

#### <a name="visual-style"></a>Визуальный стиль

Элементы NavigationView поддерживают следующие визуальные стили: выбранный, отключенный, наведенный указатель, нажат и сфокусированный.

![NavigationView элементы состояний: отключен, указатель мыши над, нажата, фокус](images/navview_item-states.png)

При соблюдении требований к оборудованию и программному обеспечению NavigationView автоматически использует новый [акриловый материал](../style/acrylic.md) и [эффект отображения](../style/reveal.md) на панели.

### <a name="header"></a>Заголовок

Области заголовка вертикально выравнивается с помощью кнопки навигации и имеет фиксированную высоту в 52 пикселя. Она содержит заголовок страницы категории выбранного навигации. Заголовок закреплен в верхней части страницы и выступает в качестве точки отсечения прокрутки для области содержимого.

Заголовок должен быть видимым, когда представление NavigationView находится в минимальном режиме. Вы можете скрыть заголовок в других режимах, используемых для окон большей ширины. Для этого задайте свойству [AlwaysShowHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.AlwaysShowHeader) значение **false**.

### <a name="content"></a>Содержимое

Область содержимого — это область, в которой отображаются основные сведения для выбранной категории навигации. 

Мы рекомендуем использовать поля в 12 пикселей для области содержимого, когда NavigationView находится в минимальном режиме отображения; или в противном случае поля в 24 пикселя.

## <a name="navigationview-display-modes"></a>Режимы отображения NavigationView
Панель NavigationView может быть открыта или закрыта и поддерживает три режима отображения.
-  **Минимальный.** Зафиксирована только кнопка-гамбургер, а панель скрывается и отображается при необходимости.
-  **Компактный.** Панель всегда отображается узкой полосой, которую можно открыть на полную ширину.
-  **Развернутый.** Панель остается открытой рядом с содержимым. После закрытия с помощью кнопки-гамбургера панель становится узкой полосой.

По умолчанию система автоматически выбирает оптимальный режим на основе объема экранного пространства, доступного элементу управления. (Можно [переопределить](#overriding-the-default-adaptive-behavior) этот параметр.)

### <a name="minimal"></a>Минимальный

![NavigationView в минимальный режим отображения области закрытые и открытые](images/navview_minimal.png)

-  Когда закрыта, панель скрыта по умолчанию. Видима только кнопка навигации.
-  Предоставляет навигацию по требованию, сохраняя при этом свободное экранное пространство. Идеально подходит для приложений на телефонах и фаблетах.
-  Нажатие кнопки навигации открывает и закрывает область, которая рисует в качестве наложения выше заголовка и содержимое. Содержимое не адаптируется.
-  При открытой, панель временное и могут быть закрыты при помощи исчезновения жестов, например выбора, нажав кнопку «Назад» или коснуться за пределами панели.
-  После открытия панели наложение выбранного элемента становится видимым.
-  При соблюдении требований в качестве фона открытой панели используется [акриловый материал приложения](../style/acrylic.md#acrylic-blend-types).
-  По умолчанию представление NavigationView находится в минимальном режиме, если его общая ширина меньше или равна 640пикселям.

### <a name="compact"></a>Компактный

![NavigationView в компактном режиме, отображая закрытую и открытую области](images/navview_compact.png)

-  После закрытия на вертикальной полосе панели отображаются только значки и кнопка навигации.
-  Обозначает выбранное расположение и не занимает много места на экране.
-  Этот режим хорошо подходит для средних экранов, например для планшетов и [на большом расстоянии](../devices/designing-for-tv.md).
-  Нажатие кнопки навигации открывает и закрывает область, которая рисует в качестве наложения выше заголовка и содержимое. Содержимое не адаптируется.
-  Заголовок не является обязательным и может быть скрыто для предоставления содержимого больше вертикального пространства.
-  Выбранный элемент показывает визуальный индикатор для выделения места нахождения пользователя в дереве навигации.
-  При соблюдении требований в качестве фона панели используется [акриловый материал приложения](../style/acrylic.md#acrylic-blend-types).
-  По умолчанию представление NavigationView работает в компактном режиме, если его общая ширина находится в диапазоне от 641 до 1007пикселей.

### <a name="expanded"></a>Развернутый

![NavigationView в развернутом режиме с отображением открытой панели](images/navview_expanded.png)

-  По умолчанию, панель остается открытой. Этот режим хорошо подходит для больших экранов.
-  Панель отображается рядом с заголовком и содержимым, которое занимает доступное пространство.
-  Когда панель закрывается с помощью кнопки навигации, она отображается как узкая полоса рядом с заголовком и содержимым.
-  Заголовок не является обязательным и может быть скрыт, чтобы освободить для содержимого больше вертикального пространства.
-  Выбранный элемент показывает визуальный индикатор для выделения пользователь ориентировался в дереве переходов.
-  При соблюдении требований в качестве фона панели используется [фоновый акриловый материал](../style/acrylic.md#acrylic-blend-types).
-  По умолчанию NavigationView находится в развернутом режиме, если его общая ширина превышает 1007пикселей.

### <a name="overriding-the-default-adaptive-behavior"></a>Переопределение адаптивного поведения по умолчанию

NavigationView автоматически изменяет режим отображения в зависимости от доступного экранного пространства.

> [!NOTE]
> NavigationView следует использовать в качестве корневого контейнера приложения. Этот элемент управления предназначен для развертывания по всей ширине и высоте окна приложения.
Вы можете переопределить ширину, при которой представление навигации изменяет режим отображения с помощью свойств [CompactModeThresholdWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.CompactModeThresholdWidth) и [ExpandedModeThresholdWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ExpandedModeThresholdWidth)

Рассмотрим следующие сценарии, в которых показано, когда может потребоваться изменить поведение режима отображения по умолчанию.

- **Частые навигации** Если предполагается, что пользователям переходить между часто некоторой области приложения, рекомендуется сохранялась области в меньшей ширины окна. Музыкальное приложение с помощью композиций и альбомов и исполнителей области переходов может согласие ширина панели 280px и оставаться в режиме развернутая, когда окно приложения имеет шире, чем 560px.
```xaml
<NavigationView OpenPaneLength="280" CompactModeThresholdWidth="560" ExpandedModeThresholdWidth="560"/>
```

- **Редкая навигация.** Если предполагается, что пользователи будут очень редко переходить между областями приложения, скройте панель при большей ширине окна. Отказаться от калькулятора с раскладок могут оставаться в минимальный режим, даже когда приложение развернуто на дисплее с разрешением 1080p.
```xaml
<NavigationView CompactModeThresholdWidth="1920" ExpandedModeThresholdWidth="1920"/>
```

- **Уточнение значков.** Если области навигации приложения не преобразуются в понятные значки, не используйте компактный режим. Просмотр приложения с помощью коллекции изображения или альбомы или может отказаться от папки области переходов для отображения NavigationView в минимальный режим на узких и среднего шириной и в режиме развернутая широкий ширину.
```xaml
<NavigationView CompactModeThresholdWidth="1008"/>
```

## <a name="interaction"></a>Взаимодействие

Когда пользователи выбирают элемент навигации на панели, NavigationView отображает этот элемент как выбранный и вызывает событие [ItemInvoked](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ItemInvoked). Если касание приводит к выбору нового элемента, NavigationView также вызывает событие [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.SelectionChanged). 

Ваше приложение отвечает за обновление заголовка и содержимого соответствующей информацией в ответ на взаимодействие с пользователем. Кроме того мы рекомендуем программным путем перемещать [фокус](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.control.FocusState) от элемента навигации к содержимому. При установке исходного фокуса касания происходит оптимизация процесса перемещения пользователя по интерфейсу и сводится к минимуму предполагаемое число движений фокуса клавиатуры.

## <a name="backwards-navigation"></a>Навигация в обратном направлении
В NavigationView есть встроенная кнопка "Назад", которую можно включить с помощью следующих свойств:
- [**IsBackButtonVisible**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsBackButtonVisible) — это перечисление NavigationViewBackButtonVisible по умолчанию. Его значением по умолчанию является "Автоматически". Это свойство используется, чтобы показать или скрыть кнопку "Назад". Если кнопка не отображается, пространство для отрисовки кнопки "Назад" будет свернуто.
- Значением свойства [**IsBackEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsBackEnabled) является "false" по умолчанию. Оно используется для переключения состояний кнопки "Назад".
- [**BackRequested**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.BackRequested) возникает, когда пользователь нажимает кнопку "Назад".
    - В минимальном/компактном режиме отображения, когда панель NavigationView.Pane открыта в виде всплывающего элемента, нажатие кнопки "Назад" приведет к закрытию панели и запуску события **PaneClosing**.
    - Событие не возникает, если значением IsBackEnabled является "false".

![Кнопка "Назад" элемента NavigationView](../basics/images/back-nav/NavView.png)

## <a name="code-example"></a>Пример кода

Ниже приведен простой пример, как NavigationView можно внедрить в приложение. 

Мы покажем, как реализовать обратную навигацию с помощью кнопки "Назад" элемента NavigationView. Обратите внимание, что использование свойств обратной навигации NavigationView подразумевает наличие [Windows 10 Insider Preview (версия 10.0.17110.0)](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewSDK).

Мы также покажем локализацию строк содержимого элементов навигации с помощью `x:Uid`. Дополнительные сведения о локализации см. в разделе [Локализация строк пользовательского интерфейса](../../app-resources/localize-strings-ui-manifest.md).

```xaml
<Page
    x:Class="NavigationViewSample.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:NavigationViewSample"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <NavigationView x:Name="NavView"
                    ItemInvoked="NavView_ItemInvoked"
                    Loaded="NavView_Loaded"
                    BackRequested="NavView_BackRequested">

        <NavigationView.MenuItems>
            <NavigationViewItem x:Uid="HomeNavItem" Content="Home" Tag="home">
                <NavigationViewItem.Icon>
                    <FontIcon Glyph="&#xE10F;"/>
                </NavigationViewItem.Icon>
            </NavigationViewItem>
            <NavigationViewItemSeparator/>
            <NavigationViewItemHeader Content="Main pages"/>
            <NavigationViewItem x:Uid="AppsNavItem" Icon="AllApps" Content="Apps" Tag="apps"/>
            <NavigationViewItem x:Uid="GamesNavItem" Icon="Video" Content="Games" Tag="games"/>
            <NavigationViewItem x:Uid="MusicNavItem" Icon="Audio" Content="Music" Tag="music"/>
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

        <NavigationView.PaneFooter>
            <HyperlinkButton x:Name="MoreInfoBtn"
                             Content="More info"
                             Click="More_Click"
                             Margin="12,0"/>
        </NavigationView.PaneFooter>

        <Frame x:Name="ContentFrame" Margin="24">
            <Frame.ContentTransitions>
                <TransitionCollection>
                    <NavigationThemeTransition/>
                </TransitionCollection>
            </Frame.ContentTransitions>
        </Frame>

    </NavigationView>
</Page>
```

```csharp
private void NavView_Loaded(object sender, RoutedEventArgs e)
{
    // you can also add items in code behind
    NavView.MenuItems.Add(new NavigationViewItemSeparator());
    NavView.MenuItems.Add(new NavigationViewItem()
    { Content = "My content", Icon = new SymbolIcon(Symbol.Folder), Tag = "content" });

    // set the initial SelectedItem 
    foreach (NavigationViewItemBase item in NavView.MenuItems)
    {
        if (item is NavigationViewItem && item.Tag.ToString() == "home")
        {
            NavView.SelectedItem = item;
            break;
        }
    }
            
    ContentFrame.Navigated += On_Navigated;

    // add keyboard accelerators for backwards navigation
    KeyboardAccelerator GoBack = new KeyboardAccelerator();
    GoBack.Key = VirtualKey.GoBack;
    GoBack.Invoked += BackInvoked;
    KeyboardAccelerator AltLeft = new KeyboardAccelerator();
    AltLeft.Key = VirtualKey.Left;
    AltLeft.Invoked += BackInvoked;
    this.KeyboardAccelerators.Add(GoBack);
    this.KeyboardAccelerators.Add(AltLeft);
    // ALT routes here
    AltLeft.Modifiers = VirtualKeyModifiers.Menu;
    
}

private void NavView_ItemInvoked(NavigationView sender, NavigationViewItemInvokedEventArgs args)
{  
    if (args.IsSettingsInvoked)
    {
        ContentFrame.Navigate(typeof(SettingsPage));
    }
    else
    {
        // find NavigationViewItem with Content that equals InvokedItem
        var item = sender.MenuItems.OfType<NavigationViewItem>().First(x => (string)x.Content == (string)args.InvokedItem);
        NavView_Navigate(item as NavigationViewItem);
    }
}

private void NavView_Navigate(NavigationViewItem item)
{
    switch (item.Tag)
    {
        case "home":
            ContentFrame.Navigate(typeof(HomePage));
            break;

        case "apps":
            ContentFrame.Navigate(typeof(AppsPage));
            break;

        case "games":
            ContentFrame.Navigate(typeof(GamesPage));
            break;

        case "music":
            ContentFrame.Navigate(typeof(MusicPage));
            break;

        case "content":
            ContentFrame.Navigate(typeof(MyContentPage));
            break;
    }           
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
    bool navigated = false;

    // don't go back if the nav pane is overlayed
    if (NavView.IsPaneOpen && (NavView.DisplayMode == NavigationViewDisplayMode.Compact || NavView.DisplayMode == NavigationViewDisplayMode.Minimal))
    {
        return false;
    }
    else
    {
        if (ContentFrame.CanGoBack)
        {
            ContentFrame.GoBack();
            navigated = true;
        }
    }
    return navigated;
}

private void On_Navigated(object sender, NavigationEventArgs e)
{
    NavView.IsBackEnabled = ContentFrame.CanGoBack;

    if (ContentFrame.SourcePageType == typeof(SettingsPage))
    {
        NavView.SelectedItem = NavView.SettingsItem as NavigationViewItem;
    }
    else 
    {
        Dictionary<Type, string> lookup = new Dictionary<Type, string>()
        {
            {typeof(HomePage), "home"},
            {typeof(AppsPage), "apps"},
            {typeof(GamesPage), "games"},
            {typeof(MusicPage), "music"},
            {typeof(MyContentPage), "content"}    
        };

        String stringTag = lookup[ContentFrame.SourcePageType];

        // set the new SelectedItem  
        foreach (NavigationViewItemBase item in NavView.MenuItems)
        {
            if (item is NavigationViewItem && item.Tag.Equals(stringTag))
            {
                item.IsSelected = true;
                break;
            }
        }        
    }
}
```

## <a name="customizing-backgrounds"></a>Настройка фона

Чтобы изменить фон главной области NavigationView, задайте его свойству `Background` значение выбранной вами кисти.

Фон панели показывает акрил приложения, когда NavigationView находится в режиме Minimal или Compact, а акрил фона — в режиме Expanded. Чтобы изменить это поведение или настроить внешний вид акрила панели, измените два ресурс темы, заменив их в файле App.xaml.

```xaml
<Application.Resources>
    <ResourceDictionary>
        <AcrylicBrush x:Key="NavigationViewDefaultPaneBackground"
        BackgroundSource="Backdrop" TintColor="Yellow" TintOpacity=".6"/>
        <AcrylicBrush x:Key="NavigationViewExpandedPaneBackground"
        BackgroundSource="HostBackdrop" TintColor="Orange" TintOpacity=".8"/>
    </ResourceDictionary>
</Application.Resources>
```

## <a name="extending-your-app-into-the-title-bar"></a>Использование акриловой поверхности в заголовке окна

Чтобы взгляд легче скользил по окну вашего приложения, рекомендуем использовать NavigationView и акриловую панель в области заголовка окна приложения. Это позволит избежать отображения визуально непривлекательной формы, создаваемой заголовком окна, содержимым NavigationView со сплошным цветом и акриловой частью области NavigationView.

Для этого добавьте такой код в App.xaml.cs:

```csharp
//draw into the title bar
var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;
coreTitleBar.ExtendViewIntoTitleBar = true;

//remove the solid-colored backgrounds behind the caption controls and system back button
var viewTitleBar = ApplicationView.GetForCurrentView().TitleBar;
viewTitleBar.ButtonBackgroundColor = Colors.Transparent;
viewTitleBar.ButtonInactiveBackgroundColor = Colors.Transparent;
viewTitleBar.ButtonForegroundColor = (Color)Resources["SystemBaseHighColor"];
```

Рисование в строке окна отрицательно сказывается на скрытии названия вашего приложения. Чтобы этого избежать, восстановите заголовок, добавив собственный элемент TextBlock. Добавьте следующую разметку в корневую страницу, содержащую ваш элемент NavigationView.

```xaml
<Grid>
    <TextBlock x:Name="AppTitle"
        xmlns:appmodel="using:Windows.ApplicationModel"
        Text="{x:Bind appmodel:Package.Current.DisplayName}"
        Style="{StaticResource CaptionTextBlockStyle}"
        IsHitTestVisible="False"
        Canvas.ZIndex="1"/>
    

    <NavigationView Canvas.ZIndex="0" ... />

</Grid>
```

Вам также потребуется настроить поля AppTitle в зависимости от видимости кнопки "Назад". Когда приложение находится в режиме FullScreenMode, необходимо удалить пространство для стрелки назад, даже если в элементе TitleBar зарезервировано место для нее.

```csharp
var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;
Window.Current.SetTitleBar(AppTitle);
coreTitleBar.ExtendViewIntoTitleBar = true;

void UpdateAppTitle()
{
    var full = (ApplicationView.GetForCurrentView().IsFullScreenMode);
    var left = 12 + (full ? 0 : CoreApplication.GetCurrentView().TitleBar.SystemOverlayLeftInset);
    AppTitle.Margin = new Thickness(left, 8, 0, 0);
}

Window.Current.CoreWindow.SizeChanged += (s, e) => UpdateAppTitle();
coreTitleBar.LayoutMetricsChanged += (s, e) => UpdateAppTitle();
```

Дополнительные сведения о настройке заголовков окон см. в разделе [Настройка заголовка окна](../shell/title-bar.md).

## <a name="related-topics"></a>Смежные разделы

- [Класс NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)
- [Основные и подробные данные](master-details.md)
- [Элемент управления Pivot](tabs-pivot.md)
- [Основы навигации](../basics/navigation-basics.md)
- [Обзор системы проектирования Fluent Design для UWP](../fluent-design-system/index.md)

