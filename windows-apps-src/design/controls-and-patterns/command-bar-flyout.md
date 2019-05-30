---
Description: Всплывающие окна командной строки предоставляет пользователям доступ встроенный для наиболее распространенных задач приложения.
title: Всплывающее меню панели команд
label: Command bar flyout
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: abarlow
design-contact: ksulliv
dev-contact: llongley
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: d5774b5301f7e8ce0616df72cfbf4fc81d0d0cf7
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66363248"
---
# <a name="command-bar-flyout"></a>Всплывающее меню панели команд

Всплывающее окно командной строки позволяет обеспечить пользователям удобный доступ к общим задачам, воспользовавшись командами в плавающую панель инструментов, относящиеся к элементу на холсте пользовательского интерфейса.

![Всплывающее окно расширенной текстовой командной строке](images/command-bar-flyout-header.png)

> CommandBarFlyout требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии, или [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

> - **API-интерфейсов платформы**: [Класс CommandBarFlyout](/uwp/api/windows.ui.xaml.controls.commandbarflyout), [класс TextCommandBarFlyout](/uwp/api/windows.ui.xaml.controls.textcommandbarflyout), [класс AppBarButton](/uwp/api/windows.ui.xaml.controls.appbarbutton), [класс AppBarToggleButton](/uwp/api/windows.ui.xaml.controls.appbartogglebutton), [ Класс AppBarSeparator](/uwp/api/windows.ui.xaml.controls.appbarseparator)
>- **Windows API-интерфейсов библиотеки пользовательского интерфейса**: [Класс CommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.commandbarflyout), [TextCommandBarFlyout-класс](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout)

Как и [CommandBar](app-bars.md), имеет CommandBarFlyout **PrimaryCommands** и **SecondaryCommands** свойства, которые можно использовать для добавления команды. Команды можно поместить в коллекцию, или оба. Когда и как первичный и вторичный команды отображаются зависит от режима отображения.

Всплывающее окно командной строки имеет два режима отображения: *свернуты* и *расширен*.

- В свернутом режиме отображаются только основные команды. Если ваш всплывающего меню панели команд имеет основного и дополнительного команды, кнопку «Показать больше», которая представлена многоточие \[••• выбора\], отображается. Это позволяет пользователю получить доступ к дополнительные команды путем перехода в развернутом режиме.
- В развернутом режиме отображаются первичный и вторичный команды. (Если элемент управления имеет только вторичных элементы, они показаны так же, как к элементу управления MenuFlyout.)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте элемент управления CommandBarFlyout, чтобы отобразить набор команд для пользователя, такие как кнопки и пункты меню, в контексте элемента на холсте приложения.

TextCommandBarFlyout отображает текст команды в элементах управления TextBox, TextBlock, RichEditBox, RichTextBlock и PasswordBox. Команды автоматически настроены правильно для выделенного текста. Позволяет заменить текст команды по умолчанию на текстовые элементы управления CommandBarFlyout.

Для отображения контекстных команд для элементов списка, следуйте указаниям в [контекстные команды для коллекций и списков](collection-commanding.md).

### <a name="commandbarflyout-vs-menuflyout"></a>CommandBarFlyout vs MenuFlyout

Чтобы отобразить команды в контекстном меню, можно использовать CommandBarFlyout или MenuFlyout. Мы рекомендуем CommandBarFlyout, так как предоставляет больше возможностей, чем MenuFlyout. Можно использовать CommandBarFlyout с только дополнительные команды для получения поведение и вид MenuFlyout или использовать всплывающее окно панели полная версия команды с помощью основного и дополнительного команд.

> Информация, см. в разделе [всплывающие окна](../controls-and-patterns/dialogs-and-flyouts/flyouts.md), [меню и контекстные меню](menus.md), и [панелей команд](app-bars.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/CommandBarFlyout">откройте приложение и CommandBarFlyout в действии см. в разделе</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="proactive-vs-reactive-invocation"></a>Упреждающее и реактивное вызова

Для вызова всплывающего меню или меню, который связан с элементом на холсте пользовательского интерфейса обычно существует два способа: _упреждающего вызова_ и _реактивного вызова_.

Упреждающее вызова команды отображаются автоматически, при взаимодействии пользователя с элементом, который команды, связанные с. Например команды форматирования текста может всплывающее окно, когда пользователь выбирает текст в текстовом поле. В этом случае всплывающее окно командной строки не имеет фокуса. Вместо этого он предоставляет соответствующие команды близко к элемент, который пользователь взаимодействует с. Если пользователь не взаимодействует с помощью команд, они игнорируются.

Реактивное вызова команды отображаются в ответ на явное действия пользователя для запроса команды; Например, щелкните правой кнопкой мыши. Это соответствует традиционных концепцию [контекстное меню](menus.md).

Можно использовать CommandBarFlyout способом, или даже сочетание двух.

## <a name="create-a-command-bar-flyout"></a>Создание всплывающего меню панели команд

В этом примере показано, как создать всплывающее окно командной строки и использовать его как упреждающее, так и по факту. При выборе элемента изображения, всплывающее окно отображается в свернутом режиме. При отображении в качестве контекстного меню, всплывающее окно отображается в развернутом режиме. В любом случае пользователь может развернуть или свернуть элемент управления flyout, после его открытия.

![Пример всплывающего меню свернутого командной строке](images/command-bar-flyout-img-collapsed.png)

> _Всплывающее окно Команда для свернутой панели_

![Пример всплывающего меню панели расширенная команда](images/command-bar-flyout-img-expanded.png)

> _Раскрывающаяся панель расширенная команда_

```xaml
<Grid>
    <Grid.Resources>
        <CommandBarFlyout x:Name="ImageCommandsFlyout">
            <AppBarButton Icon="OutlineStar" ToolTipService.ToolTip="Favorite"/>
            <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
            <AppBarButton Icon="Share" ToolTipService.ToolTip="Share"/>
            <CommandBarFlyout.SecondaryCommands>
                <AppBarButton Label="Select all"/>
                <AppBarButton Label="Delete" Icon="Delete"/>
            </CommandBarFlyout.SecondaryCommands>
        </CommandBarFlyout>
    </Grid.Resources>

    <Image Source="Assets/image1.png" Width="300"
           Tapped="Image_Tapped" FlyoutBase.AttachedFlyout="{x:Bind ImageCommandsFlyout}"
           ContextFlyout="{x:Bind ImageCommandsFlyout}"/>
</Grid>
```

```csharp
private void Image_Tapped(object sender, TappedRoutedEventArgs e)
{
    var flyout = FlyoutBase.GetAttachedFlyout((FrameworkElement)sender);
    var options = new FlyoutShowOptions()
    {
        // Position shows the flyout next to the pointer.
        // "Transient" ShowMode makes the flyout open in its collapsed state.
        Position = e.GetPosition((FrameworkElement)sender),
        ShowMode = FlyoutShowMode.Transient
    };
    flyout?.ShowAt((FrameworkElement)sender, options);
}
```

### <a name="show-commands-proactively"></a>Показать команды, заранее

При отображении команды контекстного меню заранее, только основные команды должен отображаться по умолчанию (должен быть свернуто всплывающее окно командной строки). Поместите наиболее важные команды в коллекции основные команды, а также дополнительные команды, которые традиционно пойдет в контекстном меню в коллекцию дополнительные команды.

Чтобы заранее Показать команды, обычно вы обрабатываете [щелкните](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) или [Tapped](/uwp/api/windows.ui.xaml.uielement.tapped) событие для отображения всплывающего меню панели команд. Задайте всплывающего меню [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) для **временных** или **TransientWithDismissOnPointerMoveAway** чтобы открыть всплывающее окно в свернутом режиме без перевода фокуса.

Начиная с Windows 10 Insider Preview, текстовые элементы управления имеют **SelectionFlyout** свойство. Когда вы назначаете всплывающего меню к этому свойству, автоматически отображается при выделении текста.

### <a name="show-commands-reactively"></a>Показать команды, по факту

При отображении команды контекстного меню по факту, в качестве контекстного меню, дополнительные команды отображаются по умолчанию (всплывающее окно командной строки, развернута). В этом случае всплывающее окно командной строки могут иметь основного и дополнительного команды или только дополнительные команды.

Для отображения команды в контекстном меню, обычно назначают всплывающего меню относительно [ContextFlyout](/uwp/api/windows.ui.xaml.uielement.contextflyout) свойство элемента пользовательского интерфейса. Таким образом, открыв всплывающее окно обрабатывается с помощью элемента и больше ничего делать не нужно.

Если вы обработки, отображения всплывающего меню самостоятельно (для примера перейдите на [RightTapped](/uwp/api/windows.ui.xaml.uielement.righttapped) событий), задайте всплывающего меню [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) для **стандартный** чтобы открыть всплывающее окно в развернутом режиме и Установите на нем фокус.

> [!TIP]
> Дополнительные сведения о параметрах при отображении всплывающего меню и способы управления расположение всплывающего меню, см. в разделе [всплывающие окна](../controls-and-patterns/dialogs-and-flyouts/flyouts.md).

## <a name="commands-and-content"></a>Команды и содержимое

Элемент управления CommandBarFlyout имеет 2 свойства, которые можно использовать для добавления команды и содержимое: [PrimaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.primarycommands) и [SecondaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.secondarycommands).

По умолчанию элементы панели команд добавляются в коллекцию **PrimaryCommands**. Эти команды отображаются на панели команд и отображаются в режимах свернутым и развернутым. В отличие от панели команд основные команды, не выполняйте автоматическое переполнение дополнительные команды и могут быть усечены.

Можно также добавить команды, чтобы **SecondaryCommands** коллекции. Дополнительные команды отображаются в меню части элемента управления и являются видимыми только в развернутом режиме.

### <a name="app-bar-buttons"></a>Кнопки панели приложения

Можно заполнить PrimaryCommands и SecondaryCommands непосредственно с [AppBarButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton), [AppBarToggleButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarToggleButton), и [AppBarSeparator](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarSeparator) элементов управления.

Кнопки панели приложения характеризуются значком и текстовой меткой. Эти элементы управления оптимизированы для использования в панели команд, и их внешний вид меняется в зависимости от того, отображается ли элемент управления в панели команд или в меню переполнения.

- Используется в качестве основной команды кнопок панели приложения отображаются на панели команд с помощью только их значка. Текстовая метка не отображается. Мы рекомендуем использовать подсказку для отображения текстовое описание команды, как показано ниже.
    ```xaml
    <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
    ```
- Используется в качестве вторичных команд кнопок панели приложения отображаются в меню, метку и значок отображается.

### <a name="other-content"></a>Другое содержимое

Можно добавить другие элементы управления для всплывающего меню панели команд, заключив их в AppBarElementContainer. Это позволяет добавлять элементы управления, такие как [DropDownButton](buttons.md) или [SplitButton](buttons.md), или добавить контейнеры, такие как [StackPanel](buttons.md) для создания более сложного интерфейса пользователя.

Для добавления к коллекциям первичный или вторичный команд всплывающего меню панели команд, необходимо реализовать элемент [ICommandBarElement](/uwp/api/windows.ui.xaml.controls.icommandbarelement) интерфейс. AppBarElementContainer — это оболочка, который реализует этот интерфейс, поэтому можно добавить элемент на панель команд, даже если он не реализует сам интерфейс.

Здесь AppBarElementContainer используется для добавления дополнительных элементов всплывающего меню панели команд. Основные команды для разрешения выбора цветов добавляется SplitButton. StackPanel добавляются дополнительные команды, чтобы разрешить более сложном макете для элементы управления масштабом.

> [!TIP]
> По умолчанию, элементы, созданные для приложения canvas может выглядеть неправильно панели команд. При добавлении элемента с помощью AppBarElementContainer, существует ряд действий, которые следует предпринять, чтобы сделать элемент соответствует другие элементы командной строке:
>
> - Переопределить кисти по умолчанию с [упрощенный стиль](/windows/uwp/design/controls-and-patterns/xaml-styles#lightweight-styling) фон и границы кнопок панели приложения соответствует этого элемента.
> - Настройте размер и положение элемента.
> - В Viewbox с ширину и высоту 16px вокруг значков.

> [!NOTE]
> Этот пример показывает только командной строке всплывающее окно пользовательского интерфейса, он не реализует все команды, которые отображаются. Дополнительные сведения о реализации команд, см. в разделе [кнопки](buttons.md) и [основы проектирования команда](../basics/commanding-basics.md).

![Всплывающее окно командной строке с разворачивающуюся кнопку](images/command-bar-flyout-split-button.png)

> _Команда для свернутой панели всплывающего меню по открытым SplitButton_

![Всплывающего меню панели команд с помощью сложного интерфейса пользователя](images/command-bar-flyout-custom-ui.png)

> _Расширенная команда панели всплывающего меню по пользовательский масштаб пользовательского интерфейса в меню_


```xaml
<CommandBarFlyout>
    <AppBarButton Icon="Cut" ToolTipService.ToolTip="Cut"/>
    <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
    <AppBarButton Icon="Paste" ToolTipService.ToolTip="Paste"/>
    <!-- Alignment controls -->
    <AppBarElementContainer>
        <SplitButton ToolTipService.ToolTip="Alignment">
            <SplitButton.Resources>
                <!-- Override default brushes to make the SplitButton 
                     match other command bar elements. -->
                <Style TargetType="SplitButton">
                    <Setter Property="Height" Value="38"/>
                </Style>
                <SolidColorBrush x:Key="SplitButtonBackground"
                                 Color="Transparent"/>
                <SolidColorBrush x:Key="SplitButtonBackgroundPressed"
                                 Color="{ThemeResource SystemListMediumColor}"/>
                <SolidColorBrush x:Key="SplitButtonBackgroundPointerOver"
                                 Color="{ThemeResource SystemListLowColor}"/>
                <SolidColorBrush x:Key="SplitButtonBorderBrush" Color="Transparent"/>
                <SolidColorBrush x:Key="SplitButtonBorderBrushPointerOver"
                                 Color="Transparent"/>
                <SolidColorBrush x:Key="SplitButtonBorderBrushChecked"
                                 Color="Transparent"/>
            </SplitButton.Resources>
            <SplitButton.Content>
                <Viewbox Width="16" Height="16" Margin="0,2,0,0">
                    <SymbolIcon Symbol="AlignLeft"/>
                </Viewbox>
            </SplitButton.Content>
            <SplitButton.Flyout>
                <MenuFlyout>
                    <MenuFlyoutItem Icon="AlignLeft" Text="Align left"/>
                    <MenuFlyoutItem Icon="AlignCenter" Text="Center"/>
                    <MenuFlyoutItem Icon="AlignRight" Text="Align right"/>
                </MenuFlyout>
            </SplitButton.Flyout>
        </SplitButton>
    </AppBarElementContainer>
    <!-- end Alignment controls -->
    <CommandBarFlyout.SecondaryCommands>
        <!-- Zoom controls -->
        <AppBarElementContainer>
            <AppBarElementContainer.Resources>
                <!-- Override default brushes to make the Buttons 
                     match other command bar elements. -->
                <SolidColorBrush x:Key="ButtonBackground"
                                 Color="Transparent"/>
                <SolidColorBrush x:Key="ButtonBackgroundPressed"
                                 Color="{ThemeResource SystemListMediumColor}"/>
                <SolidColorBrush x:Key="ButtonBackgroundPointerOver"
                                 Color="{ThemeResource SystemListLowColor}"/>
                <SolidColorBrush x:Key="ButtonBorderBrush"
                                 Color="Transparent"/>
                <SolidColorBrush x:Key="ButtonBorderBrushPointerOver"
                                 Color="Transparent"/>
                <SolidColorBrush x:Key="ButtonBorderBrushChecked"
                                 Color="Transparent"/>
                <Style TargetType="TextBlock">
                    <Setter Property="VerticalAlignment" Value="Center"/>
                </Style>
                <Style TargetType="Button">
                    <Setter Property="Height" Value="40"/>
                    <Setter Property="Width" Value="40"/>
                </Style>
            </AppBarElementContainer.Resources>
            <Grid Margin="12,-4">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto"/>
                    <ColumnDefinition Width="76"/>
                    <ColumnDefinition Width="Auto"/>
                </Grid.ColumnDefinitions>
                <Viewbox Width="16" Height="16" Margin="0,2,0,0">
                    <SymbolIcon Symbol="Zoom"/>
                </Viewbox>
                <TextBlock Text="Zoom" Margin="10,0,0,0" Grid.Column="1"/>
                <StackPanel Orientation="Horizontal" Grid.Column="2">
                    <Button ToolTipService.ToolTip="Zoom out">
                        <Viewbox Width="16" Height="16">
                            <SymbolIcon Symbol="ZoomOut"/>
                        </Viewbox>
                    </Button>
                    <TextBlock Text="50%" Width="40"
                               HorizontalTextAlignment="Center"/>
                    <Button ToolTipService.ToolTip="Zoom in">
                        <Viewbox Width="16" Height="16">
                            <SymbolIcon Symbol="ZoomIn"/>
                        </Viewbox>
                    </Button>
                </StackPanel>
            </Grid>
        </AppBarElementContainer>
        <!-- end Zoom controls -->
        <AppBarSeparator/>
        <AppBarButton Label="Undo" Icon="Undo"/>
        <AppBarButton Label="Redo" Icon="Redo"/>
        <AppBarButton Label="Select all" Icon="SelectAll"/>
    </CommandBarFlyout.SecondaryCommands>
</CommandBarFlyout>
```

## <a name="create-a-context-menu-with-secondary-commands-only"></a>Создание контекстного меню с помощью только вторичных команд

Можно использовать только дополнительные команды как CommandBarFlyout [контекстное меню](menus.md), вместо MenuFlyout.

![Всплывающего меню панели команд с только вторичных команд](images/command-bar-flyout-context-menu.png)

> _Всплывающий элемент строку команды в качестве контекстного меню_

```xaml
<Grid>
    <Grid.Resources>
        <!-- A command bar flyout with only secondary commands. -->
        <CommandBarFlyout x:Name="ContextMenu">
            <CommandBarFlyout.SecondaryCommands>
                <AppBarButton Label="Copy" Icon="Copy"/>
                <AppBarButton Label="Save" Icon="Save"/>
                <AppBarButton Label="Print" Icon="Print"/>
                <AppBarSeparator />
                <AppBarButton Label="Properties"/>
            </CommandBarFlyout.SecondaryCommands>
        </CommandBarFlyout>
    </Grid.Resources>

    <Image Source="Assets/image1.png" Width="300"
           ContextFlyout="{x:Bind ContextMenu}"/>
</Grid>
```

Можно также использовать CommandBarFlyout с DropDownButton для создания стандартного меню.

![Команда панели всплывающее окно с качестве раскрывающегося меню кнопки](images/command-bar-flyout-dropdown.png)

> _Раскрывающееся меню кнопки в панели команд всплывающего меню_

```xaml
<CommandBarFlyout>
    <AppBarButton Icon="Placeholder"/>
    <AppBarElementContainer>
        <DropDownButton Content="Mail">
            <DropDownButton.Resources>
                <!-- Override default brushes to make the DropDownButton 
                     match other command bar elements. -->
                <Style TargetType="DropDownButton">
                    <Setter Property="Height" Value="38"/>
                </Style>
                <SolidColorBrush x:Key="ButtonBackground"
                                 Color="Transparent"/>
                <SolidColorBrush x:Key="ButtonBackgroundPressed"
                                 Color="{ThemeResource SystemListMediumColor}"/>
                <SolidColorBrush x:Key="ButtonBackgroundPointerOver"
                                 Color="{ThemeResource SystemListLowColor}"/>

                <SolidColorBrush x:Key="ButtonBorderBrush"
                                 Color="Transparent"/>
                <SolidColorBrush x:Key="ButtonBorderBrushPointerOver"
                                 Color="Transparent"/>
                <SolidColorBrush x:Key="ButtonBorderBrushChecked"
                                 Color="Transparent"/>
            </DropDownButton.Resources>
            <DropDownButton.Flyout>
                <CommandBarFlyout Placement="BottomEdgeAlignedLeft">
                    <CommandBarFlyout.SecondaryCommands>
                        <AppBarButton Icon="MailReply" Label="Reply"/>
                        <AppBarButton Icon="MailReplyAll" Label="Reply all"/>
                        <AppBarButton Icon="MailForward" Label="Forward"/>
                    </CommandBarFlyout.SecondaryCommands>
                </CommandBarFlyout>
            </DropDownButton.Flyout>
        </DropDownButton>
    </AppBarElementContainer>
    <AppBarButton Icon="Placeholder"/>
    <AppBarButton Icon="Placeholder"/>
</CommandBarFlyout>
```

## <a name="command-bar-flyouts-for-text-controls"></a>Всплывающие окна панели команд для текстовых элементов управления

[TextCommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout) является всплывающим окном панели специализированными командами, содержащий команды для редактирования текста. Каждый текстовый элемент управления отображается TextCommandBarFlyout автоматически в качестве контекстного меню (правой кнопкой мыши), или при выделении текста. Всплывающего меню панели команд текста адаптируется к выделению текста для отображения только используемые команды.

![Панель команд всплывающего меню свернутого текста](images/command-bar-flyout-text-selection.png)

> _Всплывающее окно строке текста команды на выделенный текст_

![Всплывающее окно расширенной текстовой командной строке](images/command-bar-flyout-text-full.png)

> _Всплывающее окно расширенной текстовой командной строке_


### <a name="available-commands"></a>Доступные команды

В этой таблице показаны команды, которые включены в TextCommandBarFlyout и в случае, когда они отображаются.

| Command | Показано... |
| ------- | -------- |
| Bold | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Italic | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Underline | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Проверки правописания | При IsSpellCheckEnabled **true** и выбран неправильно написанного текста. |
| Вырезать | Если текстовый элемент управления не только для чтения и выделенный текст. |
| Копировать | Если выбран текст. |
| Вставка | Когда текстовый элемент управления не только для чтения и обмена содержимого. |
| Отменить | Если имеется действие, которое может быть отменено. |
| Выделить все | время выбора текста. |

### <a name="custom-text-command-bar-flyouts"></a>Всплывающие окна панели команд пользовательского текста

TextCommandBarFlyout невозможно настроить и автоматически управляет каждого текстового элемента управления. Тем не менее можно заменить значение по умолчанию TextCommandBarFlyout с пользовательскими командами.

- Чтобы заменить значение по умолчанию TextCommandBarFlyout, отображаемые на выделенный текст, можно создать пользовательские CommandBarFlyout (или других типа "Flyout") и назначьте его **SelectionFlyout** свойство. Если задано значение SelectionFlyout **null**, команды не отображаются на выбор.
- Чтобы заменить значение по умолчанию TextCommandBarFlyout, который отображается в виде контекстного меню, присваивать пользовательские CommandBarFlyout (или других типа "Flyout") **ContextFlyout** свойство элемента управления text. Если задано значение ContextFlyout **null**, всплывающего меню показано в предыдущих версиях текста вместо TextCommandBarFlyout показом элемента управления.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример команды XAML](https://go.microsoft.com/fwlink/p/?LinkId=620019)

## <a name="related-articles"></a>Связанные статьи

- [Основы проектирования команд в приложениях UWP](../basics/commanding-basics.md)
- [Класс CommandBar](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar)
