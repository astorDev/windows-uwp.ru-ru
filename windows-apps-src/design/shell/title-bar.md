---
author: jwmsft
description: Настройте заголовок окна классического приложения с учетом индивидуальных особенностей приложения.
title: Настройка заголовка окна
template: detail.hbs
ms.author: jimwalk
ms.date: 10/10/2017
ms.topic: article
keywords: windows 10, uwp, заголовок окна
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 2ebe590f98afef031ab183589fc7dcfc29cd9493
ms.sourcegitcommit: ca96031debe1e76d4501621a7680079244ef1c60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "5813010"
---
# <a name="title-bar-customization"></a>Настройка заголовка окна



Если приложение выполняется в окне рабочего стола, можно настроить заголовки окон с учетом индивидуальных особенностей приложения. API-интерфейсы для настройки заголовка окна позволяют указать цвета элементов заголовка окна или расширить содержимое приложения до области заголовка окна и получить над ним полный контроль.

> **Важные API-интерфейсы**: [свойство ApplicationView.TitleBar](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview), [класс ApplicationViewTitleBar](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationviewtitlebar), [класс CoreApplicationViewTitleBar](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar)

## <a name="how-much-to-customize-the-title-bar"></a>Степень настройки заголовка окна

Существует два уровня настройки, которые можно применить к заголовку окна.

Для простой настройки цвета можно установить свойствам [ApplicationViewTitleBar](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationviewtitlebar) значения в соответствии с цветами, в которые требуется окрасить элементы заголовка окна. В этом случае система продолжает управлять всеми остальными аспектами заголовка окна, такими как отрисовка названия приложения и определение перетаскиваемых областей.

Другой вариант— скрыть заголовок окна по умолчанию и заменить его собственным содержимым XAML. Например, в области заголовка окна можно разместить текст, кнопки или пользовательские меню. Кроме того, этот вариант следует использовать для расширения [акрилового](../style/acrylic.md) фона до области заголовка окна.

Выбирая полную настройку, вы отвечаете за размещение содержимого в области заголовка окна и можете определить собственную перетаскиваемую область. Системные кнопки "Назад", "Закрыть", "Свернуть" и "Развернуть" остаются доступными и обрабатываются системой, в отличие от таких элементов, как название приложения. Вам потребуется самостоятельно создать эти элементы с учетом потребностей приложения.

> [!NOTE]
> С помощью XAML, DirectX и HTML можно выполнить простую настройку цвета в приложениях UWP. Полная настройка доступна только для приложений UWP с использованием XAML.

## <a name="simple-color-customization"></a>Простая настройка цвета

Если вам требуется настроить только цвета заголовка окна, а не выполнить более сложную задачу (такую как размещение вкладок в заголовке окна), можно настроить свойства цветов в [ApplicationViewTitleBar](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationviewtitlebar) для окна приложения.

В этом примере показано, как получить экземпляр ApplicationViewTitleBar и настроить его свойства цветов.

```csharp
// using Windows.UI.ViewManagement;

var titleBar = ApplicationView.GetForCurrentView().TitleBar;

// Set active window colors
titleBar.ForegroundColor = Windows.UI.Colors.White;
titleBar.BackgroundColor = Windows.UI.Colors.Green;
titleBar.ButtonForegroundColor = Windows.UI.Colors.White;
titleBar.ButtonBackgroundColor = Windows.UI.Colors.SeaGreen;
titleBar.ButtonHoverForegroundColor = Windows.UI.Colors.White;
titleBar.ButtonHoverBackgroundColor = Windows.UI.Colors.DarkSeaGreen;
titleBar.ButtonPressedForegroundColor = Windows.UI.Colors.Gray;
titleBar.ButtonPressedBackgroundColor = Windows.UI.Colors.LightGreen;

// Set inactive window colors
titleBar.InactiveForegroundColor = Windows.UI.Colors.Gray;
titleBar.InactiveBackgroundColor = Windows.UI.Colors.SeaGreen;
titleBar.ButtonInactiveForegroundColor = Windows.UI.Colors.Gray;
titleBar.ButtonInactiveBackgroundColor = Windows.UI.Colors.SeaGreen;
```

> [!NOTE]
> Этот код можно разместить в методе [OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched) приложения (_App.xaml.cs_) после вызова [Window.Activate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.Activate) или на первой странице приложения.

> [!TIP]
> Набор средств сообщества Windows предоставляет расширения, которые позволяют задать эти свойства цветов в XAML. Дополнительные сведения см. в [документации по набору средств сообщества Windows](https://docs.microsoft.com/windows/uwpcommunitytoolkit/extensions/viewextensions).

При настройке цветов заголовка окна следует учитывать несколько особенностей.

- Цвет фона кнопки не применяется к кнопке "Закрыть" в состояниях наведения и нажатия. В этих состояниях для кнопки "Закрыть" всегда используется определенный системой цвет.
- Свойства цветов кнопок применяются к системной кнопке "Назад" при ее использовании. ([См. раздел "Журнал навигации и навигация в обратном направлении"](../basics/navigation-history-and-backwards-navigation.md).)
- Если установить свойству цвета значение **null**, оно будет сброшено к системному цвету по умолчанию.
- Невозможно установить прозрачные цвета. Альфа-канал цвета игнорируется.

Пользователь Windows может применить к заголовку окна любой [цвет элементов](https://docs.microsoft.com/windows/uwp/style/color#accent-color). При установке любого цвета заголовка окна рекомендуется установить все цвета явным образом. Это необходимо, чтобы исключить появление случайных сочетаний цветов из-за определенных пользователем параметров цвета.

## <a name="full-customization"></a>Полная настройка

Если выбрать полную настройку заголовка окна, клиентская область приложения расширяется и закрывает все окно, включая область заголовка окна. Вы отвечаете за отрисовку и обработку входных данных для всего окна за исключением кнопок заголовка, которые накладываются поверх холста приложения.

Чтобы скрыть заголовок окна по умолчанию и расширить собственное содержимое до области заголовка окна, задайте свойству [CoreApplicationViewTitleBar.ExtendViewIntoTitleBar](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar) значение **true**.

В этом примере показано, как получить CoreApplicationViewTitleBar и задать свойству ExtendViewIntoTitleBar значение **true**. Это можно сделать в методе [OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched) приложения (_App.xaml.cs_) или на первой странице приложения.

```csharp
// using Windows.ApplicationModel.Core;

// Hide default title bar.
var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;
coreTitleBar.ExtendViewIntoTitleBar = true;
```

> [!TIP]
> Этот параметр сохраняется даже после закрытия и перезапуска приложения. Если в Visual Studio задать для ExtendViewIntoTitleBar значение **true**, то для возврата к значению по умолчанию следует явным образом задать значение **false** и запустить приложение, чтобы перезаписать сохраненный параметр.

### <a name="draggable-regions"></a>Перетаскиваемые области

Перетаскиваемая область заголовка окна определяет, в каком месте пользователь может щелкнуть и перетащить окно (в отличие от перетаскивания содержимого в пределах холста приложения). Чтобы указать перетаскиваемую область, необходимо вызвать метод [Window.SetTitleBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.window.settitlebar) и передать UIElement, определяющий перетаскиваемую область. (UIElement часто представляет собой панель, содержащую другие элементы.)

Ниже показано, как установить сетку с содержимым в качестве перетаскиваемой области заголовка окна. Этот код добавляется в код XAML и код программной части для первой страницы приложения. Полный код см. в разделе [Пример полной настройки](./title-bar.md#full-customization-example).

```xaml
<Grid x:Name="AppTitleBar" Background="Transparent">
    <!-- Width of the padding columns is set in LayoutMetricsChanged handler. -->
    <!-- Using padding columns instead of Margin ensures that the background
         paints the area under the caption control buttons (for transparent buttons). -->
    <Grid.ColumnDefinitions>
        <ColumnDefinition x:Name="LeftPaddingColumn" Width="0"/>
        <ColumnDefinition/>
        <ColumnDefinition x:Name="RightPaddingColumn" Width="0"/>
    </Grid.ColumnDefinitions>
    <Image Source="Assets/Square44x44Logo.png" 
           Grid.Column="1" HorizontalAlignment="Left" 
           Width="20" Height="20" Margin="12,0"/>
    <TextBlock Text="Custom Title Bar" 
               Grid.Column="1" 
               Style="{StaticResource CaptionTextBlockStyle}" 
               Margin="44,8,0,0"/>
</Grid>
```

```csharp
public MainPage()
{
    this.InitializeComponent();

    var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;
    coreTitleBar.ExtendViewIntoTitleBar = true;

    // Set XAML element as a draggable region.
    AppTitleBar.Height = coreTitleBar.Height;
    Window.Current.SetTitleBar(AppTitleBar);
}
```

Класс UIElement (`AppTitleBar`) входит в состав XAML для приложения. Можно объявить и настроить заголовок окна на неизменной корневой странице или объявить и настроить область заголовка панели на каждой странице внутри приложения. При настройке на каждой странице следует убедиться, что перетаскиваемая область остается согласованной по мере навигации пользователя по приложению.

Можно вызвать SetTitleBar для переключения на новый элемент заголовка окна во время работы приложения. Также можно передать значение **null** как параметр для метода SetTitleBar, чтобы вернуться к поведению перетаскивания по умолчанию. (Дополнительные сведения см. в разделе "Перетаскиваемая область по умолчанию".)

> [!IMPORTANT]
> Указанная перетаскиваемая область должна быть доступной для проверки нажатия, то есть для некоторых элементов может потребоваться настроить прозрачную кисть фона. Дополнительные сведения см. в примечаниях по [VisualTreeHelper.FindElementsInHostCoordinates](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.visualtreehelper.findelementsinhostcoordinates).
>
>Например, если определить сетку как перетаскиваемую область, установите значение `Background="Transparent"`, чтобы сделать ее перетаскиваемой.
>
>Эта сетка не является перетаскиваемой (но видимые элементы в ней можно перетаскивать): `<Grid x:Name="AppTitleBar">`.
>
>Эта сетка выглядит так же, но является полностью перетаскиваемой: `<Grid x:Name="AppTitleBar" Background="Transparent">`.

#### <a name="default-draggable-region"></a>Перетаскиваемая область по умолчанию

Если перетаскиваемая область не указана, в качестве перетаскиваемой области по умолчанию используется прямоугольник с шириной окна и высотой кнопок заголовка, расположенный вдоль верхнего края окна.

Если перетаскиваемая область указана, система сжимает перетаскиваемую область по умолчанию до небольшой области размером с кнопку заголовка, расположенную слева от кнопок заголовка (или справа от них, если кнопки заголовка находятся в левой части окна). Таким образом, в окне всегда присутствует согласованная область для перетаскивания.

### <a name="system-caption-buttons"></a>Системные кнопки заголовка

Система резервирует верхние левый и правый углы окна приложения для системных кнопок заголовка ("Назад", "Свернуть", "Развернуть" и "Закрыть"). Система сохраняет контроль над областью элементов управления заголовка, чтобы обеспечить наличие минимальных возможностей для перетаскивания, свертывания, развертывания и закрытия окна. Система отображает кнопку "Закрыть" в верхнем правом углу для языков с написанием слева направо и в верхнем левом углу для языков с написанием справа налево.

Размеры и расположение области элементов управления заголовка определяются классом CoreApplicationViewTitleBar, поэтому его можно учитывать в макете пользовательского интерфейса заголовка окна. Ширина зарезервированной области с каждой стороны задается свойствами [SystemOverlayLeftInset](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar.SystemOverlayLeftInset) и [SystemOverlayRightInset](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar.SystemOverlayRightInset), а ее высота задается свойством [Height](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar.Height).

Под областью элементов управления заголовка, определенной этими свойствами, можно отображать содержимое, например фон приложения, но не следует размещать здесь элементы пользовательского интерфейса, с которыми пользователь должен взаимодействовать. Эта область не принимает входные данные, так как ввод для элементов управления заголовка обрабатывается системой.

Можно обработать событие [LayoutMetricsChanged](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar.LayoutMetricsChanged), чтобы отреагировать на изменения размера кнопок заголовка. Например, это может произойти, если системная кнопка "Назад" отображается или скрывается. Обработайте это событие для проверки и обновления позиционирования элементов пользовательского интерфейса, который зависят от размера панели заголовка окна.

В этом примере показано, как настроить макет заголовка окна с учетом таких изменений, как отображение или скрытие системной кнопки "Назад". `AppTitleBar`, `LeftPaddingColumn` и `RightPaddingColumn` объявлены в коде XAML, показанном ранее.

```csharp
private void CoreTitleBar_LayoutMetricsChanged(CoreApplicationViewTitleBar sender, object args)
{
    UpdateTitleBarLayout(sender);
}

private void UpdateTitleBarLayout(CoreApplicationViewTitleBar coreTitleBar)
{
    // Get the size of the caption controls area and back button 
    // (returned in logical pixels), and move your content around as necessary.
    LeftPaddingColumn.Width = new GridLength(coreTitleBar.SystemOverlayLeftInset);
    RightPaddingColumn.Width = new GridLength(coreTitleBar.SystemOverlayRightInset);
    TitleBarButton.Margin = new Thickness(0,0,coreTitleBar.SystemOverlayRightInset,0);

    // Update title bar control size as needed to account for system size changes.
    AppTitleBar.Height = coreTitleBar.Height;
}
```

### <a name="interactive-content"></a>Интерактивное содержимое

Интерактивные элементы управления, такие как кнопки, меню или поле поиска, можно разместить в верхней части приложения, чтобы они отображались в строке заголовка. Однако существует несколько правил, которым необходимо следовать, чтобы интерактивные элементы принимали пользовательский ввод.
- Чтобы определить какую-либо область как перетаскиваемую область заголовка окна, необходимо вызвать метод SetTitleBar. Если этого не сделать, система устанавливает перетаскиваемую область по умолчанию в верхней части страницы. Затем система будет обрабатывать все входные данные пользователя в этой области и не позволит входным данным достичь элементов управления.
- Разместите интерактивные элементы управления поверх перетаскиваемой области, определенной вызовом SetTitleBar (с более высоким значением z-порядка). Не делайте интерактивные элементы управления дочерними элементами класса UIElement, переданного методу SetTitleBar. После передачи элемента методу SetTitleBar система считает его частью системного заголовка окна и направляет весь ввод указателя в этот элемент.

В данном случае элемент `TitleBarButton` имеет более высокое значение z-порядка, чем `AppTitleBar`, поэтому он получает ввод пользователя.

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition />
    </Grid.RowDefinitions>

    <Grid x:Name="AppTitleBar" Background="Transparent">
        <!-- Width of the padding columns is set in LayoutMetricsChanged handler. -->
        <!-- Using padding columns instead of Margin ensures that the background
             paints the area under the caption control buttons (for transparent buttons). -->
        <Grid.ColumnDefinitions>
            <ColumnDefinition x:Name="LeftPaddingColumn" Width="0"/>
            <ColumnDefinition/>
            <ColumnDefinition x:Name="RightPaddingColumn" Width="0"/>
        </Grid.ColumnDefinitions>
        <Image Source="Assets/Square44x44Logo.png"
               Grid.Column="1" HorizontalAlignment="Left"
               Width="20" Height="20" Margin="12,0"/>
        <TextBlock Text="Custom Title Bar"
                   Grid.Column="1"
                   Style="{StaticResource CaptionTextBlockStyle}"
                   Margin="44,8,0,0"/>
    </Grid>

    <!-- This Button has a higher z-order than AppTitleBar, 
         so it receives user input. -->
    <Button x:Name="TitleBarButton" Content="Button in the title bar"
        HorizontalAlignment="Right"/>
</Grid>
```

### <a name="transparency-in-caption-buttons"></a>Прозрачность кнопок заголовка

Если задать свойству ExtendViewIntoTitleBar значение **true**, можно сделать фон кнопок заголовка прозрачным, чтобы сквозь него было видно фон приложения. Как правило, для полной прозрачности следует установить для фона значение [Colors.Transparent](https://docs.microsoft.com/uwp/api/windows.ui.colors.Transparent). Для частичной прозрачности настройте альфа-канал для значения [Color](https://docs.microsoft.com/uwp/api/windows.ui.color), заданного свойству.

Следующие свойства ApplicationViewTitleBar могут быть прозрачными:

- ButtonBackgroundColor
- ButtonHoverBackgroundColor
- ButtonPressedBackgroundColor
- ButtonInactiveBackgroundColor

Цвет фона кнопки не применяется к кнопке "Закрыть" в состояниях наведения и нажатия. В этих состояниях для кнопки "Закрыть" всегда используется определенный системой цвет.

Все остальные свойства цвета продолжат игнорировать альфа-канал. Если свойству ExtendViewIntoTitleBar задано значение **false**, все свойства цвета ApplicationViewTitleBar всегда игнорируют альфа-канал.

### <a name="full-screen-and-tablet-mode"></a>Полноэкранный режим и режим планшета

Если приложение выполняется в _полноэкранном режиме_ или _режиме планшета_, система скрывает заголовок окна и кнопки управления заголовка. Тем не менее, пользователь может вызвать заголовок окна, чтобы он отображался поверх пользовательского интерфейса приложения.
Можно обработать событие [CoreApplicationViewTitleBar.IsVisibleChanged](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.coreapplicationviewtitlebar.IsVisibleChanged), чтобы получать уведомление при скрытии или вызове заголовка окна и отображать или скрывать пользовательское содержимое заголовка окна по необходимости.

В этом примере показано, как обработать событие IsVisibleChanged, чтобы отобразить или скрыть показанный ранее элемент `AppTitleBar`.

```csharp
public MainPage()
{
    this.InitializeComponent();

    var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;

    // Register a handler for when the title bar visibility changes.
    // For example, when the title bar is invoked in full screen mode.
    coreTitleBar.IsVisibleChanged += CoreTitleBar_IsVisibleChanged;
}

private void CoreTitleBar_IsVisibleChanged(CoreApplicationViewTitleBar sender, object args)
{
    if (sender.IsVisible)
    {
        AppTitleBar.Visibility = Visibility.Visible;
    }
    else
    {
        AppTitleBar.Visibility = Visibility.Collapsed;
    }
}
```

>[!NOTE]
>_Полноэкранный_ режим можно включить, только если он поддерживается приложением. Дополнительные сведения см. в разделе [ApplicationView.IsFullScreenMode](https://docs.microsoft.com/uwp/api/windows.ui.viewmanagement.applicationview.IsFullScreenMode). [_Режим планшета_](https://support.microsoft.com/help/17210/windows-10-use-your-pc-like-a-tablet) доступен на поддерживаемом оборудовании, поэтому пользователь может запустить любое приложение в режиме планшета.

## <a name="full-customization-example"></a>Пример полной настройки

```xaml
<Page
    x:Class="CustomTitleBar.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:CustomTitleBar"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition />
        </Grid.RowDefinitions>

        <Grid x:Name="AppTitleBar" Background="Transparent">
            <!-- Width of the padding columns is set in LayoutMetricsChanged handler. -->
            <!-- Using padding columns instead of Margin ensures that the background
                 paints the area under the caption control buttons (for transparent buttons). -->
            <Grid.ColumnDefinitions>
                <ColumnDefinition x:Name="LeftPaddingColumn" Width="0"/>
                <ColumnDefinition/>
                <ColumnDefinition x:Name="RightPaddingColumn" Width="0"/>
            </Grid.ColumnDefinitions>
            <Image Source="Assets/Square44x44Logo.png" 
                   Grid.Column="1" HorizontalAlignment="Left" 
                   Width="20" Height="20" Margin="12,0"/>
            <TextBlock Text="Custom Title Bar" 
                       Grid.Column="1" 
                       Style="{StaticResource CaptionTextBlockStyle}" 
                       Margin="44,8,0,0"/>
        </Grid>

        <!-- This Button has a higher z-order than MyTitleBar, 
             so it receives user input. -->
        <Button x:Name="TitleBarButton" Content="Button in the title bar"
                HorizontalAlignment="Right"/>
    </Grid>
</Page>
```

```csharp
public MainPage()
{
    this.InitializeComponent();

    // Hide default title bar.
    var coreTitleBar = CoreApplication.GetCurrentView().TitleBar;
    coreTitleBar.ExtendViewIntoTitleBar = true;
    UpdateTitleBarLayout(coreTitleBar);

    // Set XAML element as a draggable region.
    Window.Current.SetTitleBar(AppTitleBar);

    // Register a handler for when the size of the overlaid caption control changes.
    // For example, when the app moves to a screen with a different DPI.
    coreTitleBar.LayoutMetricsChanged += CoreTitleBar_LayoutMetricsChanged;

    // Register a handler for when the title bar visibility changes.
    // For example, when the title bar is invoked in full screen mode.
    coreTitleBar.IsVisibleChanged += CoreTitleBar_IsVisibleChanged;
}

private void CoreTitleBar_LayoutMetricsChanged(CoreApplicationViewTitleBar sender, object args)
{
    UpdateTitleBarLayout(sender);
}

private void UpdateTitleBarLayout(CoreApplicationViewTitleBar coreTitleBar)
{
    // Get the size of the caption controls area and back button 
    // (returned in logical pixels), and move your content around as necessary.
    LeftPaddingColumn.Width = new GridLength(coreTitleBar.SystemOverlayLeftInset);
    RightPaddingColumn.Width = new GridLength(coreTitleBar.SystemOverlayRightInset);
    TitleBarButton.Margin = new Thickness(0,0,coreTitleBar.SystemOverlayRightInset,0);

    // Update title bar control size as needed to account for system size changes.
    AppTitleBar.Height = coreTitleBar.Height;
}

private void CoreTitleBar_IsVisibleChanged(CoreApplicationViewTitleBar sender, object args)
{
    if (sender.IsVisible)
    {
        AppTitleBar.Visibility = Visibility.Visible;
    }
    else
    {
        AppTitleBar.Visibility = Visibility.Collapsed;
    }
}
```

## <a name="dos-and-donts"></a>Рекомендации

- Обеспечьте явное указание на то, является ли окно активным или неактивным. Как минимум, следует настроить изменение цвета текста, значков и кнопок в заголовке окна.
- Определите перетаскиваемую область вдоль верхнего края холста приложения. Если ее совместить с системными заголовками окон, пользователям будет легче ее найти.
- Определите перетаскиваемую область, соответствующую визуальному заголовку окна (если он есть) холста приложения.

## <a name="related-articles"></a>Статьи по теме

- [Акрил](../style/acrylic.md)
- [Цвет](../style/color.md)
