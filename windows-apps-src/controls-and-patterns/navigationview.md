---
author: Jwmsft
Description: "Элемент управления, в которой располагаются навигации верхнего уровня, сохраняя пространство экрана при."
title: "Представление переходов"
ms.assetid: 
label: Navigation view
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: tpaine
doc-status: Published
ms.openlocfilehash: 98ab8a95288e5a0225a2185f7ce037e16209d173
ms.sourcegitcommit: ba0d20f6fad75ce98c25ceead78aab6661250571
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/24/2017
---
# <a name="navigation-view"></a>Представление переходов

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

> [!IMPORTANT]
> В этой статье описана еще не выпущенная функция, которая может быть существенно изменена до коммерческого выпуска. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.

Элемент управления представлением навигации предоставляет общие вертикальный макет для области приложения через меню сворачиваемую навигации верхнего уровня. Этот элемент управления предназначен для реализации шаблона панель навигации или гамбургера и автоматически адаптируется макет для разных размеров окон.

> **Важные API-интерфейсы**: [класс NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview), [класс NavigationViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitem), [перечисление NavigationViewDisplayMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewdisplaymode)

![Пример NavigationView](images/navview_wireframe.png)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

NavigationView хорошо подходит:

-  Приложения с большим числом элементов навигации верхнего уровня аналогичного типа. Например, спортивное приложение с категориями "Хоккей", "Волейбол", "Баскетбол", "Футбол" и т. д.
-  Предоставление единообразия интерфейса навигации по приложениям. Панель должна содержать только элементы навигации, но не действия.
-  Для среднего и высокого количества (5-10) категорий навигаций верхнего уровня.
-  Экономия свободного экранного пространства для окон меньшего размера.

Представление переходов — это только один из нескольких элементов навигации, которые можно использовать. Дополнительные сведения о шаблонах и других элементах навигации см. в статье [Основы проектирования навигации в приложениях универсальной платформы Windows (UWP)](../layout/navigation-basics.md).

Пример кода как создать панель навигации с помощью SplitView и ListView, загрузите [решение для навигации по XAML](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/XamlNavigation) с GitHub.

## <a name="navigationview-parts"></a>Компоненты NavigationView
Элемент управления является широко разделена на три раздела - панели для навигации по левому краю и заголовок и области содержимого справа.

![Разделы представления навигации](images/navview_sections.png)

### <a name="pane"></a>Панель

Панель навигации может содержать:

- навигационные элементы в форме [NavigationViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitem) для перехода на определенные страницы;
- разделители в виде [NavigationViewItemSeparator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator) для группировки элементов навигации;
- заголовки в виде [NavigationViewItemHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemheader) для маркировки групп элементов;
- необязательная точка входа для [параметров приложения](../app-settings/app-settings-and-data.md). Чтобы скрыть элемент параметров, используйте свойство [IsSettingsVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview#Windows_UI_Xaml_Controls_NavigationView_IsSettingsVisible);
- содержимое в произвольном формате в нижнем колонтитуле панели при добавлении в свойство [PaneFooter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview#Windows_UI_Xaml_Controls_NavigationView_PaneFooter).

Встроенная кнопка навигации ("гамбургер") позволяет пользователям открывать и закрывать панель. Когда панель открыта в более крупных окнах приложения, вы можете скрыть эту кнопку с помощью свойства [IsPaneToggleButtonVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview#Windows_UI_Xaml_Controls_NavigationView_IsPaneToggleButtonVisible).

### <a name="header"></a>Заголовок

Области заголовка вертикально выравнивается с кнопкой навигацией и имеет фиксированную высоту. Она содержит заголовок страницы категории выбранного навигации. Заголовок закреплен в верхней части страницы и выступает в качестве точки отсечения прокрутки для области содержимого.

Заголовок должен быть видимым, когда представление NavigationView находится в минимальном режиме. Вы можете скрыть заголовок в других режимах, используемых для окон большей ширины. Для этого задайте свойству [AlwaysShowHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview#Windows_UI_Xaml_Controls_NavigationView_AlwaysShowHeader) значение **false**.

### <a name="content"></a>Содержимое

Область содержимого — это область, в которой отображаются основные сведения для выбранной категории навигации. Он может содержать один или несколько элементов и является хорошо область для дополнительных подуровня навигации, например [Pivot](tabs-pivot.md).

Мы рекомендуем поля 12 пикселям сторон ваше содержимое NavigationView находится в режиме минимальной поля и значение 24 пикселя в противном случае.

## <a name="visual-style"></a>Визуальным стилем

<div class="microsoft-internal-note">
Красные линии для этого элемента управления, доступные на [UNI](http://uni/DesignDepot.FrontEnd/#/ProductNav?t=Fluent%20Design%20System%7CControls%20%26%20Patterns%7CNavigationView).<br/><br/>
</div>

Элементы навигации поддержка выбранного, отключено, указатель над, нажата и фокус визуальных состояний.

![NavigationView элементы состояний: отключен, указатель мыши над, нажата, фокус](images/navview_item-states.png)

При соблюдении требований к оборудованию и программному обеспечению NavigationView автоматически использует новый [акриловый материал](../style/acrylic.md) и [эффект отображения](../style/reveal.md) на панели.


## <a name="navigationview-modes"></a>Режимы NavigationView
Панель NavigationView может быть открыта или закрыта и поддерживает три режима отображения.
-  **Минимальный.** Зафиксирована только кнопка-гамбургер, а панель скрывается и отображается при необходимости.
-  **Компактный.** Панель всегда отображается узкой полосой, которую можно открыть на полную ширину.
-  **Развернутый.** Панель остается открытой рядом с содержимым. После закрытия с помощью кнопки-гамбургера панель становится узкой полосой.

По умолчанию система автоматически выбирает оптимальный режим на основе объема экранного пространства, доступного элементу управления. (Этот параметр можно переопределить, Дополнительные сведения см.)

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
-  Этот режим хорошо подходит для средних экранов, например на планшетах и [расстоянии](../input-and-devices/designing-for-tv.md).
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

## <a name="overriding-the-default-adaptive-behavior"></a>Переопределение адаптивного поведения по умолчанию

Представление переходов автоматически изменяет режим отображения в зависимости от доступного экранного пространства.
[!NOTE] NavigationView следует использовать в качестве корневого контейнера приложения. Этот элемент управления предназначен для развертывания по всей ширине и высоте окна приложения.
Вы можете переопределить ширину, при которой представление навигации меняет режим отображения, используя свойства [CompactModeThresholdWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview#Windows_UI_Xaml_Controls_NavigationView_CompactModeThresholdWidth) и ExpandedModeThresholdWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview#Windows_UI_Xaml_Controls_NavigationView_ExpandedModeThresholdWidth). Рассмотрим следующие сценарии, в которых показано, когда может потребоваться изменить поведение режима отображения по умолчанию.

-  **Частые навигации** Если предполагается, что пользователям переходить между часто некоторой области приложения, рекомендуется сохранялась области в меньшей ширины окна. Музыкальное приложение с помощью композиций и альбомов и исполнителей области переходов может согласие ширина панели 280px и оставаться в режиме развернутая, когда окно приложения имеет шире, чем 560px.
```xaml
<NavigationView OpenPaneLength=”280” CompactModeThresholdWidth="560" ExpandedModeThresholdWidth=”560”/>
```
-    **Редкая навигация.** Если предполагается, что пользователи будут очень редко переходить между областями приложения, скройте панель при большей ширине окна. Отказаться от калькулятора с раскладок могут оставаться в минимальный режим, даже когда приложение развернуто на дисплее с разрешением 1080p.
```xaml
<NavigationView CompactModeThresholdWidth=”1920” ExpandedModeThresholdWidth=”1920”/>
```
-    **Уточнение значков.** Если области навигации приложения не преобразуются в понятные значки, не используйте компактный режим. Просмотр приложения с помощью коллекции изображения или альбомы или может отказаться от папки области переходов для отображения NavigationView в минимальный режим на узких и среднего шириной и в режиме развернутая широкий ширину.
```xaml
<NavigationView CompactModeThresholdWidth=”1008”/>
```

## <a name="interaction"></a>Взаимодействие

Когда пользователи выбирают категорию навигации на панели, NavigationView отображает этот элемент как выбранный и вызывает событие [ItemInvoked](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview#Windows_UI_Xaml_Controls_NavigationView_ItemInvoked). Если касание приводит к выбору нового элемента, NavigationView также вызывает событие [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview#Windows_UI_Xaml_Controls_NavigationView_SelectionChanged). Ваше приложение отвечает за обновление заголовка и содержимого соответствующей информацией в ответ на взаимодействие с пользователем. Кроме того мы рекомендуем программным путем перемещения фокуса от элемента навигации к содержимому. Задав исходный фокус нагрузки, оптимизировать потока пользовательского и свести к минимуму ожидаемое число перемещает фокус клавиатуры.

## <a name="how-to-use-navigationview"></a>Как использовать NavigationView

Ниже приведен простой пример, как NavigationView можно внедрить в приложение.

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
                    Loaded="NavView_Loaded">
    <!-- Load NavigationViewItems in NavView_Loaded. -->

        <NavigationView.HeaderTemplate>
            <DataTemplate>
                <Grid>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="Auto"/>
                        <ColumnDefinition/>
                    </Grid.ColumnDefinitions>
                    <TextBlock Style="{StaticResource TitleTextBlockStyle}"
                           FontSize="28"
                           VerticalAlignment="Center"
                           Margin="12,0"
                           Text="Welcome"/>
                    <CommandBar Grid.Column="1"
                            HorizontalAlignment="Right"
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

        <Frame x:Name="ContentFrame">
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
    NavView.MenuItems.Add(new NavigationViewItem()
        { Text = "Apps", Icon = new SymbolIcon(Symbol.AllApps), Tag = "apps" });
    NavView.MenuItems.Add(new NavigationViewItem()
        { Text = "Games", Icon = new SymbolIcon(Symbol.Video), Tag = "games" });
    NavView.MenuItems.Add(new NavigationViewItem()
        { Text = "Music", Icon = new SymbolIcon(Symbol.Audio), Tag = "music" });
    NavView.MenuItems.Add(new NavigationViewItemSeparator());
    NavView.MenuItems.Add(new NavigationViewItem()
        { Text = "My content", Icon = new SymbolIcon(Symbol.Folder), Tag = "content" });

    foreach (NavigationViewItem item in NavView.MenuItems)
    {
        if (item.Tag.ToString() == "play")
        {
            NavView.SelectedItem = item;
            break;
        }
    }
}

private void NavView_ItemInvoked(NavigationView sender, NavigationViewItemInvokedEventArgs args)
{
    if (args.IsSettingsInvoked == true)
    {
        ContentFrame.Navigate(typeof(SettingsPage));
    }
    else
    {
        switch ((args.InvokedItem as NavigationViewItem).Tag)
        {
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
}
```

## <a name="navigation"></a>Навигация

NavigationView автоматически отображать кнопки «Назад» в строке заголовка приложения и не добавлять содержимое в стеке переходов назад. Элемент управления не реагирует на автоматически программного обеспечения или нажатия кнопки Назад оборудования. См. в разделе [журнал и обратной навигации](../layout/navigation-history-and-backwards-navigation.md) Дополнительные сведения о том, как добавить поддержку для навигации в приложении и в этом разделе.


## <a name="related-topics"></a>Еще по теме:

* [Класс NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)
* [Основные и подробные данные](master-details.md)
* [Элемент управления Pivot](tabs-pivot.md)
* [Основы навигации](../layout/navigation-basics.md)
 

 
