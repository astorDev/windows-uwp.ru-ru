---
author: serenaz
Description: NavigationView is an adaptive control that implements top-level navigation patterns for your app.
title: Представление навигации
template: detail.hbs
ms.author: sezhen
ms.date: 08/06/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: ''
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 4c0857005d584b1fde0eb52a6ab0ef5ec29eaf44
ms.sourcegitcommit: 9c79fdab9039ff592edf7984732d300a14e81d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "2810915"
---
# <a name="navigation-view-preview-version"></a>Представление "Навигация" (Предварительная версия)

> **Это версия preview**: в этой статье описываются новые версии элемента управления NavigationView, который находится в разработке. Для использования этой теперь требуется [последние построения изнутри Windows и пакет SDK](https://insider.windows.com/for-developers/) или [Библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Элемент управления NavigationView содержит навигации верхнего уровня для вашего приложения. Он преобразует различных размеров экрана поддерживает несколько стилей навигации.

> **API -интерфейсы библиотеки пользовательского интерфейса Windows**: [класс Microsoft.UI.Xaml.Controls.NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview)

> **Интерфейсы API платформы**: [класс Windows.UI.Xaml.Controls.NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)

## <a name="get-the-windows-ui-library"></a>Получение библиотека интерфейса пользователя Windows

Этот элемент управления включается в состав библиотека интерфейса пользователя Windows, NuGet пакет, содержащий новые элементы управления и возможности пользовательского интерфейса приложений UWP. Дополнительные сведения, включая инструкции по установке в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). 

## <a name="navigation-styles"></a>Стили навигации

Поддерживает NavigationView:

**Меню или левой панели переходов**

![Расширенное области переходов](images/displaymode-left.png)

**Панель навигации верхнего уровня или в меню**

![верхней панели навигации](images/displaymode-top.png)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

NavigationView является элементом управления адаптивный навигации, который хорошо подходит для:

- Предоставление единообразная переходов во всем вашего приложения.
- Сохранение недвижимость экрана на окна меньшего размера.
- Упорядочение доступ к многие категории навигации.

Другие элементы навигации в разделе [основы структуры навигации](../basics/navigation-basics.md).

Если для навигации требуется более сложное поведение, которое не поддерживается в NavigationView, можно попробовать использовать шаблон [Основные и подробные данные](master-details.md).

:::row:::
    :::column:::
        ![Некоторые изображения](images/XAML-controls-gallery-app-icon.png)
    :::column-end:::
    ::: диапазон столбцов = «2»::: **Коллекция элементов управления XAML**<br>
        При наличии установленного приложения коллекция элементов управления XAML, щелкните <a href="xamlcontrolsgallery:/item/NavigationView">здесь</a> для открытия приложения и просмотреть NavigationView в действии.

        <a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Get the XAML Controls Gallery app (Microsoft Store)</a><br>
        <a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Get the source code (GitHub)</a>
    :::column-end:::
:::row-end:::

## <a name="display-modes"></a>Режимы отображения

NavigationView может быть задано в различных режимах посредством `PaneDisplayMode` свойства:

:::row:::
    :::column:::
    ### Left
    Displays an expanded left positioned pane.
    :::column-end:::
    :::column span="2":::
    ![left nav pane expanded](images/displaymode-left.png)
    :::column-end:::
:::row-end:::

Мы рекомендуем левой панели навигации при:

- Имеется несколько среднего (5-10) категорий менее важно навигации верхнего уровня.
- Вы можете очень наиболее важные навигационных категорий с меньше места для другого контента приложения.

:::row:::
    :::column:::
    ### Top
    Displays a top positioned pane.
    :::column-end:::
    :::column span="2":::
    ![top navigation](images/displaymode-top.png)
    :::column-end:::
:::row-end:::

Мы рекомендуем верхней панели навигации при:

- 5 или меньше категорий менее важно навигации верхнего уровня, таким образом, что все категории Дополнительные навигации верхнего уровня, в итоге в раскрывающемся списке переполнения меню считается менее важных.
- Вам нужно показать все параметры навигации на экране.
- Вы можете больше пространства для содержимого приложения.
- Значки нельзя четко описаны категории навигации вашего приложения.

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

![Адаптивное поведение по умолчанию левая GIF](images/displaymode-auto.png)

Преобразует между LeftMinimal на небольших экранов, LeftCompact на средней экранов и слева на больших экранах. Для получения дополнительных сведений в разделе [адаптивное поведение](#adaptive-behavior) .

## <a name="anatomy"></a>Структура

<b>Левая навигационная панель</b><br>

![слева NavigationView разделы](images/leftnav-anatomy.png)

<b>Верхняя панель навигации</b><br>

![наиболее распространенные NavigationView разделы](images/topnav-anatomy.png)

## <a name="pane"></a>Панель

Области могут располагаться на верхней или слева, с помощью `PanePosition` свойство.

Ниже приводится подробное области устройство для положения верхней и левой области:

<b>Левая навигационная панель</b><br>

![Устройство NavigationView](images/navview-pane-anatomy-vertical.png)

1. Кнопка меню
1. Элементы навигации
1. Разделители
1. Заголовки
1. AutoSuggestBox (необязательно)
1. Кнопка параметров (необязательно)

<b>Верхняя панель навигации</b><br>

![Устройство NavigationView](images/navview-pane-anatomy-horizontal.png)

1. Заголовки
1. Элементы навигации
1. Разделители
1. AutoSuggestBox (необязательно)
1. Кнопка параметров (необязательно)

Кнопки "Назад" отображается в левом верхнем углу области, но NavigationView не создается автоматически содержимое назад. Чтобы включить обратной навигации, обратитесь к разделу [обратной навигации](#backwards-navigation) раздел.

В области NavigationView также может содержать:

1. Элементы навигации в форме [NavigationViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitem)для перехода на отдельные страницы.
2. Разделители, в виде [NavigationViewItemSeparator](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator)для группировки элементов навигации. Присвойте свойству [непрозрачность](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemseparator.opacity) значение 0, чтобы отобразить разделителя групп разрядов как пространство.
3. Заголовки в виде [NavigationViewItemHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationviewitemheader)для маркировки групп элементов.
4. Необязательные [AutoSuggestBox](auto-suggest-box.md) для поиска на уровне приложения.
5. Необязательная точка входа для [параметров приложения](../app-settings/app-settings-and-data.md). Чтобы скрыть параметры элемента, используйте свойство [IsSettingsVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsSettingsVisible) .

Левая панель содержит:

6. Кнопка меню для включения и отключения области открытия и закрытия. Когда панель открыта в более крупных окнах приложения, вы можете скрыть эту кнопку с помощью свойства [IsPaneToggleButtonVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsPaneToggleButtonVisible).

### <a name="pane-footer"></a>Область нижнего колонтитула

содержимое в произвольном формате в нижнем колонтитуле панели при добавлении в свойство [PaneFooter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneFooter).

:::row:::
    :::column:::
    <b>Левая навигационная панель</b><br>
    ![Левая навигационная панель нижний колонтитул области](images/navview-freeform-footer-left.png)<br>
    :::column-end:::
    :::column:::
     <b>Верхняя панель навигации</b><br>
    ![Верхняя панель навигации по области заголовка](images/navview-freeform-footer-top.png)<br>
    :::column-end:::
:::row-end:::

### <a name="pane-header"></a>Область заголовка

Произвольный контента в область заголовка, при добавлении свойство [PaneHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneHeader)

:::row:::
    :::column:::
    <b>Левая навигационная панель</b><br>
    ![Левая навигационная панель заголовка области](images/navview-freeform-header-left.png)<br>
    :::column-end:::
    :::column:::
     <b>Верхняя панель навигации</b><br>
    ![Верхняя панель навигации по области заголовка](images/navview-freeform-header-top.png)<br>
    :::column-end:::
:::row-end:::

### <a name="pane-content"></a>Области контента

Произвольный контента в области при добавлении свойство [PaneCustomContent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PaneCustomContent)

:::row:::
    :::column:::
    <b>Левая навигационная панель</b><br>
    ![Панель навигации contentleft настраиваемые области](images/navview-freeform-pane-left.png)<br>
    :::column-end:::
    :::column:::
     <b>Верхняя панель навигации</b><br>
    ![Область настраиваемого контента верхняя](images/navview-freeform-pane-top.png)<br>
    :::column-end:::
:::row-end:::

### <a name="visual-style"></a>Визуальный стиль

При соблюдении требований к оборудованию и программному обеспечению, NavigationView автоматически использует [Acrylic материалов](../style/acrylic.md) в его области и [Показать выделение](../style/reveal.md) только в ее левой панели.

## <a name="header"></a>Заголовок

![Универсальный изображение navview области заголовка](images/nav-header.png)

Область заголовка выравнивается по вертикали кнопку навигации в левой области положение и располагается под область в положение верхней области. Она имеет фиксированную высоту 52 точек. Она содержит заголовок страницы категории выбранного навигации. Заголовок закреплен в верхней части страницы и выступает в качестве точки отсечения прокрутки для области содержимого.

Заголовок должен быть видны при NavigationView находится в режиме отображения минимальной. Вы можете скрыть заголовок в других режимах, используемых для окон большей ширины. Для этого задайте свойству [AlwaysShowHeader](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.AlwaysShowHeader) значение **false**.

## <a name="content"></a>Содержимое

![Универсальный изображение navview области содержимого](images/nav-content.png)

Область содержимого — это область, в которой отображаются основные сведения для выбранной категории навигации.

Мы рекомендуем использовать поля в 12 пикселей для области содержимого, когда NavigationView находится в минимальном режиме отображения; или в противном случае поля в 24 пикселя.

## <a name="adaptive-behavior"></a>Адаптивное поведение

NavigationView автоматически изменяет режим отображения в зависимости от доступного экранного пространства. Тем не менее можно настроить поведение режима адаптивный экран.

### <a name="default"></a>По умолчанию

Адаптивное поведение по умолчанию NavigationView — Показать расширенное левой панели на ширину окна больших, в средней окно ширины области левая навигационная панель только значок и кнопки меню гамбургер в небольшом окне ширины. Дополнительные сведения о размеров окна для адаптивное поведение можно [размеров экрана и точки останова](../layout/screen-sizes-and-breakpoints-for-responsive-design.md).

![Адаптивное поведение по умолчанию левая GIF](images/displaymode-auto.png)

```xaml
<NavigationView />
```

### <a name="minimal"></a>Минимальный

Второй общий принцип адаптивный является использование расширенной левой панели на большие окна ширины и меню гамбургер на обоих ширину окна малого и среднего размера.

![Адаптивное поведение левая GIF 2](images/adaptive-behavior-minimal.png)

```xaml
<NavigationView CompactModeThresholdWidth="1008" ExpandedModeThresholdWidth="1007" />
```

Рекомендуется использовать этот формат, если:

- Вы можете пространство для контента приложения на небольших ширина окна.
- Значки навигационных категорий не может быть четко представлено.

### <a name="compact"></a>Компактный

Третий общий принцип адаптивный является использование расширенной левой области ширину окна больших и левая навигационная панель только значок область на обоих ширину окна малого и среднего размера. Хороший пример — это почтовое приложение.

![Адаптивное поведение левая GIF 3](images/adaptive-behavior-compact.png)

```xaml
<NavigationView CompactModeThresholdWidth="0" ExpandedModeThresholdWidth="1007" />
```

Рекомендуется использовать этот формат, если:

- Важно всегда отображать все параметры навигации на экране.
- навигационных категорий может быть четко представлена в виде значков.

### <a name="no-adaptive-behavior"></a>Адаптивное поведение не

В некоторых случаях может не необходимыми для работы любой адаптивное поведение вообще. Можно задать области, чтобы быть всегда расширенной, всегда compact или всегда минимальной.

![Адаптивное поведение левая GIF 4](images/adaptive-behavior-none.png)

```xaml
<NavigationView PaneDisplayMode="LeftMinimal" />
```

### <a name="top-to-left-navigation"></a>На левой панели навигации в начало

Рекомендуется использовать верхней панели навигации на крупные размеры и левой области переходов на небольших размеров окна, когда:

- У вас есть набор одинаково важных навигации верхнего уровня категорий будет отображаться вместе, таким образом, если на экране не подходит одну категорию в этот набор, свернуть для навигации слева, чтобы дать им равно важности.
- Вы хотите сохранить как намного контента пространства по мере возможности в небольшом окне размеры.

Пример.

![Адаптивное поведение верхняя или левая навигационная панель GIF 1](images/navigation-top-to-left.png)

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

В некоторых случаях приложения нужно привязка данных к верхней области и левой панели. Часто левой области включает в себя дополнительные элементы навигации.

Пример.

![Адаптивное поведение верхняя или левая навигационная панель GIF 2](images/navigation-top-to-left2.png)

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

В этой модели вкладок связаны Выбор и фокус. Действия, что обычно смены фокуса также сместится выбора. В следующем примере правом arrowing перемещает индикатор выделения из отображения для Экранная лупа. Для этого свойства [SelectionFollowsFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.selectionfollowsfocus) значение включено.

![Снимок экрана с текстовыми верхней navview](images/nav-tabs.png)

Ниже приведен пример XAML, которые:

```xaml
<NavigationView PanePosition="Top" SelectionFollowsFocus="Enabled" >
   <NavigationView.MenuItems>
        <NavigationViewItem Content="Display" />
        <NavigationViewItem Content="Magnifier"  />
        <NavigationViewItem Content="Keyboard" />
    </NavigationView.MenuItems>
</NavigationView>

```

Замена содержимого при изменении выбора вкладки, можно использовать метод [NavigateWithOptions](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.NavigateToType) фрейма FrameNavigationOptions.IsNavigationStackEnabled имеет значение False и NavigateOptions.TransitionInfoOverride установите соответствующие для параллельных анимация слайда. Например см в следующем [примере кода](#code-example) .

Если вы хотите изменить стиль по умолчанию, можно переопределить свойство [MenuItemContainerStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.menuitemcontainerstyle) NavigationView's. Можно также установить свойство [MenuItemTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.menuitemtemplate) для указания шаблона данных.

## <a name="backwards-navigation"></a>Навигация в обратном направлении

В NavigationView есть встроенная кнопка "Назад", которую можно включить с помощью следующих свойств:

- [**IsBackButtonVisible**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsBackButtonVisible) — это перечисление NavigationViewBackButtonVisible по умолчанию. Его значением по умолчанию является "Автоматически". Это свойство используется, чтобы показать или скрыть кнопку "Назад". Если кнопка не отображается, пространство для отрисовки кнопки "Назад" будет свернуто.
- Значением свойства [**IsBackEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.IsBackEnabled) является "false" по умолчанию. Оно используется для переключения состояний кнопки "Назад".
- [**BackRequested**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.BackRequested) возникает, когда пользователь нажимает кнопку "Назад".
    - В минимальном/компактном режиме отображения, когда панель NavigationView.Pane открыта в виде всплывающего элемента, нажатие кнопки "Назад" приведет к закрытию панели и запуску события **PaneClosing**.
    - Событие не возникает, если значением IsBackEnabled является "false".

:::row:::
    :::column:::
    <b>Левая навигационная панель</b><br>
    ![Слева NavigationView кнопку возврата на панель навигации](images/leftnav-back.png)
    :::column-end:::
    :::column:::
     <b>Верхняя панель навигации</b><br>
    ![Кнопку возврата NavigationView на верхней панели навигации](images/topnav-back.png)
    :::column-end:::
:::row-end:::

## <a name="code-example"></a>Пример кода

> [!NOTE]
> NavigationView следует использовать в качестве корневого контейнера приложения. Этот элемент управления предназначен для развертывания по всей ширине и высоте окна приложения.
Вы можете переопределить ширину, при которой представление навигации изменяет режим отображения с помощью свойств [CompactModeThresholdWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.CompactModeThresholdWidth) и [ExpandedModeThresholdWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ExpandedModeThresholdWidth)

Ниже приведен пример начала до конца как можно использовать NavigationView с на размеров окна больших панели навигации верхнего уровня и в небольшом окне размеры области навигации слева.

В этом примере мы рассчитываем конечным пользователям часто выберите новые категории навигации и поэтому мы:

- Задайте для свойства [SelectionFollowsFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PanePostion) значение включено
- Используйте переходы кадров, которые не следует добавлять в стеке навигации.
- Оставьте значение по умолчанию на свойство [ShoulderNavigationEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.PanePostion) , которое используется для указания, если нему влево или вправо на игровой последовательно выберите категории навигации верхнего уровня приложения. Значение по умолчанию — «WhenSelectionFollowsFocus». Возможные значения, «Всегда» и «Никогда».

Мы также приведены инструкции по реализации обратной переходов с помощью элемента NavigationView кнопки "Назад".

Ниже приведен пример демонстрирует запись.

![Пример NavigationView-конца](images/nav-code-example.gif)

Ниже приведен пример кода:

> [!NOTE]
> Если вы используете [Библиотека интерфейса пользователя Windows](https://docs.microsoft.com/uwp/toolkits/winui/), то необходимо добавить ссылку на набор средств: `xmlns:controls="using:Microsoft.UI.Xaml.Controls"`.

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
> Если вы используете [Библиотека интерфейса пользователя Windows](https://docs.microsoft.com/uwp/toolkits/winui/), то необходимо добавить ссылку на набор средств: `using MUXC = Microsoft.UI.Xaml.Controls;`.

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

Фоновые области показано acrylic из приложения, когда NavigationView находится в верхней минимальной, или режим. Чтобы изменить это поведение или настроить внешний вид акрила панели, измените два ресурс темы, заменив их в файле App.xaml.

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

## <a name="scroll-content-under-top-pane"></a>Прокручивание содержимого в разделе верхней области

Для облегчения внешний вид + интерфейса пользователя Если ваше приложение имеет страницы, которые используют ScrollViewer и области переходов в начало размещенный, мы рекомендуем необходимости контента прокрутки ниже области верхней панели навигации.

Это можно сделать путем установки свойства [CanContentRenderOutsideBounds](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.cancontentrenderoutsidebounds) соответствующих ScrollViewer имеет значение true.

![navview прокрутки области переходов](images/nav-scroll-content.png)

Если ваше приложение имеет слишком долго прокрутка содержимого, может потребоваться стремиться решения о заголовков, которые присоединяются к области верхней панели навигации и формы поверхность гладким. 

![решения о заголовке navview прокрутки](images/nav-scroll-stickyheader.png)

Этого можно достичь путем установки свойства [ContentOverlay](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ContentOverlay) NavigationView. 

В некоторых случаях если пользователь прокрутки вниз, можно скрыть область навигации, полученной за путем установки свойства [IsPaneVisible](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview.ContentOverlay) NavigationView значение false.

![navview прокрутки скрыть панель навигации](images/nav-scroll-hidepane.png)

## <a name="related-topics"></a>Еще по теме:

- [Класс NavigationView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.navigationview)
- [Основные и подробные данные](master-details.md)
- [Элемент управления Pivot](tabs-pivot.md)
- [Основы навигации](../basics/navigation-basics.md)
- [Обзор системы проектирования Fluent Design для UWP](../fluent-design-system/index.md)