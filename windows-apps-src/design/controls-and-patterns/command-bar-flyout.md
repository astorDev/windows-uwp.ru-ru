---
Description: Всплывающее меню панели команд предоставляет пользователям доступ к самым распространенным задачам приложения.
title: Всплывающее меню панели команд
label: Command bar flyout
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: abarlow
design-contact: ksulliv
dev-contact: llongley
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 9388df4159d7e9acd68c75163465339183b41314
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82968799"
---
# <a name="command-bar-flyout"></a>Всплывающее меню панели команд

Всплывающее меню панели команд позволяет обеспечить пользователям удобный доступ к распространенным задачам, отображая команды в плавающей панели инструментов, связанной с элементом на холсте пользовательского интерфейса.

![Развернутое всплывающее меню панели команд для работы с текстом](images/command-bar-flyout-header.png)

Как и элемент [CommandBar](app-bars.md), CommandBarFlyout содержит свойства **PrimaryCommands** и **SecondaryCommands**, которые можно использовать для добавления команд. Вы можете разместить команды в одной из коллекций или же в обеих. Условие и способ отображения основных и второстепенных команд зависит от режима отображения.

Для всплывающего меню панели команд доступно два режима отображения: *свернутый* и *развернутый*.

- В свернутом режиме отображаются только основные команды. Если всплывающее меню панели команд содержит основные и второстепенные команды, отображается кнопка "Показать больше" в виде многоточия \[***\]. Нажав ее, пользователь может получить доступ к второстепенным командам путем перехода в развернутый режим.
- В развернутом режиме отображаются как основные, так и второстепенные команды. (Если элемент управления содержит только второстепенные элементы, они отображаются примерно так же, как в элементе управления MenuFlyout.)

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Элемент управления **CommandBarFlyout** является частью библиотеки пользовательского интерфейса Windows — пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений для Windows. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

>**API библиотеки пользовательского интерфейса Windows**: [класс CommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.commandbarflyout), [класс TextCommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout).
>
>**API платформы**: [класс CommandBarFlyout](/uwp/api/windows.ui.xaml.controls.commandbarflyout), [класс TextCommandBarFlyout](/uwp/api/windows.ui.xaml.controls.textcommandbarflyout), [класс AppBarButton](/uwp/api/windows.ui.xaml.controls.appbarbutton), [класс AppBarToggleButton](/uwp/api/windows.ui.xaml.controls.appbartogglebutton), [класс AppBarSeparator](/uwp/api/windows.ui.xaml.controls.appbarseparator).
>
> Для использования всплывающего меню панели (CommandBarFlyout) требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздняя, либо [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

С помощью элемента управления CommandBarFlyout можно отобразить для пользователя коллекцию команд, таких как кнопки и пункты меню, в контексте элемента на холсте приложения.

TextCommandBarFlyout отображает команды для работы с текстом в элементах управления TextBox, TextBlock, RichEditBox, RichTextBlock и PasswordBox. Команды автоматически настраиваются в соответствии с текущим выделением текста. С помощью CommandBarFlyout в элементах управления текстом можно заменять команды для работы с текстом по умолчанию.

Чтобы отобразить контекстно-зависимые команды в элементах списка, выполните инструкции, приведенные в статье [Контекстно-зависимые команды для коллекций и списков](collection-commanding.md).

### <a name="commandbarflyout-vs-menuflyout"></a>CommandBarFlyout и MenuFlyout

Для отображения команд в контекстном меню вы можете воспользоваться CommandBarFlyout или MenuFlyout. Рекомендуем использовать элемент управления CommandBarFlyout, потому что он предоставляет больше функциональных возможностей, чем MenuFlyout. Вы можете использовать CommandBarFlyout только со второстепенными командами, что обеспечит поведение и внешний вид MenuFlyout, или же использовать полнофункциональное всплывающее меню панели команд с основными и второстепенными командами.

> Дополнительные сведения см. в следующих статьях: [Всплывающие элементы](../controls-and-patterns/dialogs-and-flyouts/flyouts.md), [Меню и контекстные меню](menus.md), [Панель команд](app-bars.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/CommandBarFlyout">открыть приложение и увидеть CommandBarFlyout в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="proactive-vs-reactive-invocation"></a>Упреждающий и реактивный вызов

Существует два способа вызвать всплывающее меню, связанное с элементом на холсте пользовательского интерфейса: _упреждающий вызов_ и _реактивный вызов_.

При упреждающем вызове команды появляются автоматически, когда пользователь взаимодействует с элементом, с которым связаны команды. Например, команды форматирования текста могут появиться при выборе текста в текстовом поле. В этом случае всплывающее меню панели команд не имеет фокуса. Вместо этого в нем содержатся соответствующие команды рядом с элементом, с которым взаимодействует пользователь. Если пользователь не взаимодействует с командами, они отклоняются.

При реактивном вызове команды отображаются в ответ на явное действие пользователя для запроса команд, например щелчок правой кнопкой мыши. Этот способ соответствует традиционной концепции [контекстного меню](menus.md).

Вы можете использовать CommandBarFlyout одним из этих способов или же двумя.

## <a name="create-a-command-bar-flyout"></a>Создание всплывающего меню панели команд

В этом примере показано создание всплывающего меню панели команд и его использование двумя способами: выполнение упреждающего и реактивного вызова. При касании изображения всплывающее меню отображается в свернутом режиме. При отображении в виде контекстного меню всплывающий элемент отображается в развернутом режиме. В любом случае пользователь может развернуть или свернуть всплывающее меню после открытия.

![Пример свернутого всплывающего меню панели команд](images/command-bar-flyout-img-collapsed.png)

> _Свернутое всплывающее меню панели команд_

![Пример развернутого всплывающего меню панели команд](images/command-bar-flyout-img-expanded.png)

> _Развернутое всплывающее меню панели команд_

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

### <a name="show-commands-proactively"></a>Упреждающее отображение команд

При упреждающем отображении контекстно-зависимых команд по умолчанию должны отображаться только основные команды (всплывающее меню панели команд должно быть свернуто). Поместите наиболее важные команды в коллекцию основных команд, а дополнительные команды, которые по умолчанию содержатся в контекстном меню, в коллекцию второстепенных команд.

Для упреждающего отображения команд обычно необходимо обработать событие [Click](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) или [Tapped](/uwp/api/windows.ui.xaml.uielement.tapped), чтобы отобразить всплывающее меню панели команд. Установите для параметра всплывающего меню [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) значение **Transient** или **TransientWithDismissOnPointerMoveAway**, чтобы открыть всплывающее меню в свернутом режиме без фокуса.

Начиная с Windows 10 Insider Preview, элементы управления текстом содержат свойство **SelectionFlyout**. Если назначить всплывающее меню этому свойству, это меню будет автоматически отображаться при выделении текста.

### <a name="show-commands-reactively"></a>Реактивное отображение команд

При реактивном отображении контекстно-зависимых команд второстепенные команды отображаются в виде контекстного меню по умолчанию (всплывающее меню панели команд должно быть развернуто). В этом случае всплывающее меню панели команд может содержать как основные, так и второстепенные команды или же только последние.

Чтобы отобразить команды в контекстном меню, обычно необходимо назначить всплывающему меню свойство [ContextFlyout](/uwp/api/windows.ui.xaml.uielement.contextflyout) элемента пользовательского интерфейса. Таким образом, открытие всплывающего меню обрабатывается элементом, и вам не нужно выполнять дополнительных действий.

Если вы сами обрабатываете отображение всплывающего меню (например, в случае события [RightTapped](/uwp/api/windows.ui.xaml.uielement.righttapped)), укажите для параметра всплывающего элемента [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) значение **Standard**, чтобы открыть всплывающее меню в развернутом режиме и перевести на него фокус.

> [!TIP]
> Дополнительные сведения о параметрах при отображении всплывающего меню и об управлении размещением всплывающего меню см. в [этой статье](../controls-and-patterns/dialogs-and-flyouts/flyouts.md).

## <a name="commands-and-content"></a>Команды и содержимое

Элемент управления CommandBarFlyout имеет два свойства, которые можно использовать для добавления команд и содержимого: [PrimaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.primarycommands) и [SecondaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.secondarycommands).

По умолчанию элементы панели команд добавляются в коллекцию **PrimaryCommands**. Эти команды отображаются на панели команд как в свернутом, так и в развернутом режимах. В отличие от CommandBar, в CommandBarFlyout основные команды не переходят к второстепенным автоматически, а также их можно усечь.

Вы можете также добавлять команды в коллекцию **SecondaryCommands**. Второстепенные команды отображаются в определенной части меню элемента управления и только в развернутом режиме.

### <a name="app-bar-buttons"></a>Кнопки панели приложения

Вы можете присвоить свойствам PrimaryCommands и SecondaryCommands значения [AppBarButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarButton), [AppBarToggleButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarToggleButton) и [AppBarSeparator](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.AppBarSeparator), соответствующие элементам управления.

Кнопки панели приложения характеризуются значком и текстовой меткой. Эти элементы управления оптимизированы для использования на панели команд и меняют свой вид в зависимости от того, отображается ли элемент управления на панели команд или в меню переполнения.

- Кнопки панели приложения, используемые в качестве основных команд, отображаются на панели команд только со значком — текстовая метка не отображается. Рекомендуем использовать подсказку для отображения текстового описания команды, как показано ниже.
    ```xaml
    <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
    ```
- Кнопки панели приложения, используемые в качестве второстепенных команд, отображаются в меню с меткой и значком.

### <a name="other-content"></a>Другое содержимое

Вы можете добавить другие элементы управления во всплывающее меню панели команд, поместив их в оболочку AppBarElementContainer. Таким образом вы сможете добавлять элементы управления, такие как [DropDownButton](buttons.md) или [SplitButton](buttons.md), или же контейнеры, такие как [StackPanel](buttons.md), для создания более сложного пользовательского интерфейса.

Для добавления в коллекцию основных или второстепенных команд всплывающего меню панели команд элемент должен реализовывать интерфейс [ICommandBarElement](/uwp/api/windows.ui.xaml.controls.icommandbarelement). AppBarElementContainer — это оболочка, реализующая данный интерфейс, которая позволяет добавить элемент на панель команд, даже если он не реализует сам интерфейс.

В данном случае AppBarElementContainer используется для добавления дополнительных элементов во всплывающее меню панели команд. Элемент управления SplitButton добавляется к основным командам, что обеспечивает выбор цвета. StackPanel добавляется ко второстепенным командам, чтобы обеспечить более сложную структуру для элементов управления масштабом.

> [!TIP]
> По умолчанию элементы, разработанные для холста приложения, могут иметь неправильный вид на панели команд. При добавлении элементов с помощью AppBarElementContainer вы должны выполнить приведенные ниже шаги, чтобы элемент соответствовал другим элементам на панели команд:
>
> - Переопределите кисти по умолчанию, задав [упрощенный стиль](/windows/uwp/design/controls-and-patterns/xaml-styles#lightweight-styling), чтобы фон и граница элемента соответствовали кнопкам на панели приложения.
> - Скорректируйте размер и положение элемента.
> - Перенесите значки в окно просмотра с шириной и высотой 16 пикселей.

> [!NOTE]
> В этом примере показан лишь пользовательский интерфейс всплывающего меню панели задач, а не реализация отображаемых команд. Дополнительные сведения о реализации команд см. в статьях [Кнопки](buttons.md) и [Основы проектирования команд в приложениях UWP](../basics/commanding-basics.md).

![Всплывающее меню панели команд с разворачивающейся кнопкой](images/command-bar-flyout-split-button.png)

> _Свернутое всплывающее меню панели команд с открытым элементом управления SplitButton_

![Всплывающее меню панели команд со сложным интерфейсом](images/command-bar-flyout-custom-ui.png)

> _Развернутое всплывающее меню панели команд с настраиваемым пользовательским интерфейсом масштабирования в меню_


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

## <a name="create-a-context-menu-with-secondary-commands-only"></a>Создание контекстного меню, содержащего лишь второстепенные команды

Вы можете использовать элемент CommandBarFlyout, содержащий лишь второстепенные команды, в качестве [контекстного меню](menus.md) вместо MenuFlyout.

![Всплывающее меню панели команд, содержащее лишь второстепенные команды](images/command-bar-flyout-context-menu.png)

> _Всплывающее меню панели команд в виде контекстного меню_

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

Вы также можете использовать CommandBarFlyout с DropDownButton для создания стандартного меню.

![Всплывающий элемент панели команд с раскрывающимся меню кнопок](images/command-bar-flyout-dropdown.png)

> _Раскрывающееся меню кнопок во всплывающем элементе панели команд_

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

## <a name="command-bar-flyouts-for-text-controls"></a>Всплывающее меню панели команд для элементов управления текстом

[TextCommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout) — это специальное всплывающее меню панели команд, содержащее команды для редактирования текста. Каждый элемент управления текстом автоматически (или при выделении текста) отображает TextCommandBarFlyout в виде контекстного меню (щелчок правой кнопкой мыши). Всплывающее меню панели команд для работы с текстом адаптируется к выделенному тексту и отображает только соответствующие команды.

![Свернутое всплывающее меню панели команд для работы с текстом](images/command-bar-flyout-text-selection.png)

> _Всплывающее меню панели команд для работы с текстом, отображающееся при выделении текста_

![Развернутое всплывающее меню панели команд для работы с текстом](images/command-bar-flyout-text-full.png)

> _Развернутое всплывающее меню панели команд для работы с текстом_


### <a name="available-commands"></a>Доступные команды

В приведенной ниже таблице указаны команды, содержащиеся в TextCommandBarFlyout, и условие их отображения.

| Команда | Условие отображения |
| ------- | -------- |
| Полужирный шрифт | Элемент управления текстом не является доступным только для чтения (только RichEditBox). |
| Курсив | Элемент управления текстом не является доступным только для чтения (только RichEditBox). |
| Underline | Элемент управления текстом не является доступным только для чтения (только RichEditBox). |
| Проверка правописания | Для параметра IsSpellCheckEnabled задано значение **true**, и выбран текст с ошибкой. |
| Вырезать | Элемент управления текстом не является доступным только для чтения, и текст выделен. |
| Копировать | Выделен текст. |
| Вставить | Элемент управления текстом не является доступным только для чтения, и в буфере обмена есть содержимое. |
| Отмена | Наличие действия, которое можно отменить. |
| Выбрать все | Текст может быть выбран. |

### <a name="custom-text-command-bar-flyouts"></a>Настраиваемые всплывающие меню панели команд для работы с текстом

Настройка элемента TextCommandBarFlyout невозможна. Он автоматически управляется каждым элементом управления текстом. Тем не менее, вы можете заменить TextCommandBarFlyout по умолчанию на настраиваемые команды.

- Чтобы заменить элемент TextCommandBarFlyout по умолчанию, отображаемый при выделении текста, вы можете создать настраиваемый элемент CommandBarFlyout (или другой тип всплывающего меню) и назначить его свойству **SelectionFlyout**. Если для SelectionFlyout установлено значение **null**, при выделении команды отображаться не будут.
- Чтобы заменить элемент TextCommandBarFlyout по умолчанию, отображаемый в виде контекстного меню, назначьте настраиваемый элемент CommandBarFlyout (или другой тип всплывающего меню) свойству **ContextFlyout** в элементе управления текстом. Если для ContextFlyout установлено значение **null**, всплывающее меню, отображаемое в предыдущих версиях элемента управления текстом, отображается вместо TextCommandBarFlyout.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.
- [Пример команд XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlCommanding)

## <a name="related-articles"></a>Похожие статьи

- [Основы проектирования команд в приложениях для Windows](../basics/commanding-basics.md)
- [CommandBar class](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CommandBar) (Класс CommandBar)
