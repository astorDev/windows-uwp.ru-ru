---
author: jwmsft
Description: Command bar flyouts give users inline access to your app's most common tasks.
title: Всплывающее меню панели команд
label: Command bar flyout
template: detail.hbs
ms.author: jimwalk
ms.date: 10/2/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: abarlow
design-contact: ksulliv
dev-contact: llongley
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 95d99c41ff2679e3ef3e0471dd583fe78458922c
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6968840"
---
# <a name="command-bar-flyout"></a>Всплывающее меню панели команд

Всплывающий элемент панели команд позволяет предоставить пользователям удобный доступ к распространенным задачам путем отображения команд в плавающей панели инструментов, связанные с элементом на холст пользовательского интерфейса.

![Всплывающий элемент панели команд развернутого текста](images/command-bar-flyout-header.png)

> CommandBarFlyout требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии или [Библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

> - **API -интерфейсы платформы**: [класс CommandBarFlyout](/uwp/api/windows.ui.xaml.controls.commandbarflyout), [TextCommandBarFlyout класса](/uwp/api/windows.ui.xaml.controls.textcommandbarflyout), [класс AppBarButton](/uwp/api/windows.ui.xaml.controls.appbarbutton), [класс AppBarToggleButton](/uwp/api/windows.ui.xaml.controls.appbartogglebutton), [класс AppBarSeparator](/uwp/api/windows.ui.xaml.controls.appbarseparator)
>- **API -интерфейсы библиотеки пользовательского интерфейса Windows**: [класс CommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.commandbarflyout), [класс TextCommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout)

Например, [CommandBar](app-bars.md)CommandBarFlyout имеет **свойствам PrimaryCommands** и **SecondaryCommands** свойства, которые можно использовать для добавления команд. Можно поместить команды в коллекцию, либо оба. Когда и как главных и вспомогательных команд отображаются зависит от режима отображения.

Всплывающий элемент панели команд поддерживает два режима отображения: *сворачивается* и *расширяется*.

- В свернутом режиме отображаются только основные команды. Если ваш всплывающий элемент панели команд содержит основные и дополнительные команды, кнопка «Дополнительно», представленное многоточие \ [•••\], отображается. Это позволяет пользователю получить доступ ко второстепенным командам, переход к развернутом режиме.
- В развернутом режиме отображаются основные и дополнительные команды. (Если элемент управления имеет только вспомогательные элементы, они отображаются в аналогично тому, как элемент MenuFlyout.)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Используйте элемент управления CommandBarFlyout для отображения набор команд для пользователя, таких как кнопки и пунктов меню, в контексте текущего элемента на холсте приложения.

TextCommandBarFlyout отображает текст команды в элементах управления TextBox, TextBlock, RichEditBox, RichTextBlock и PasswordBox. Команды автоматически настраиваются соответствующим образом для текущего выделения текста. Используйте CommandBarFlyout для замены команд текста по умолчанию на элементы управления текстом.

Для отображения контекстно-зависимые команды для элементов списка следуйте рекомендациям в [Контекстные команды для коллекций и списков](collection-commanding.md).

### <a name="commandbarflyout-vs-menuflyout"></a>CommandBarFlyout vs MenuFlyout

Для отображения команд в контекстном меню, можно использовать CommandBarFlyout или MenuFlyout. Мы рекомендуем CommandBarFlyout, так как он предоставляет дополнительные функции по сравнению с MenuFlyout. Поведение и вид MenuFlyout или используйте всплывающий элемент панели команд полный вместе с главных и вспомогательных команд можно использовать CommandBarFlyout с только вспомогательные команды.

> Связанные сведения см. в разделе [всплывающие элементы](../controls-and-patterns/dialogs-and-flyouts/flyouts.md) [меню и контекстные меню](menus.md)и [панели команд](app-bars.md).

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">Галереи элементов управления XAML</strong> , щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/CommandBarFlyout">открыть приложение и увидеть CommandBarFlyout в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="proactive-vs-reactive-invocation"></a>Упреждающее и реактивный вызова

Для вызова всплывающий элемент или меню, связанный с элементом на холсте вашего пользовательского интерфейса обычно существует два способа: _упреждающего вызова_ и _реактивный вызов_.

Упреждающее вызову команды отображаются автоматически, когда пользователь взаимодействует с элементом, который команды связаны с. Например команды форматирования текста может мультимедийном пользователь выбирает текст в текстовом поле. В этом случае всплывающий элемент панели команд не принимать фокус. Вместо этого он представляет соответствующих команд вблизи элемент, который пользователь взаимодействует с. Если пользователь не взаимодействовать с командами, они являются закрыта.

Реактивные вызову команды отображаются в ответ на явное действие пользователя для запроса команды; Например, щелчок правой кнопкой мыши. Это соответствует традиционных концепция [контекстное меню](menus.md).

Вы можете использовать CommandBarFlyout способ или даже оба типа привязки.

## <a name="create-a-command-bar-flyout"></a>Создание всплывающий элемент панели команд

В этом примере показано, как создать всплывающий элемент панели команд и использовать его заблаговременно и устраняйте. При касании изображения, всплывающий элемент отображается в свернутом режиме. При отображении как контекстного меню, всплывающий элемент отображается в развернутом режиме. В любом случае пользователь может развернуть или свернуть всплывающий элемент после ее открытия.

![Пример всплывающий элемент панели свернуто команд](images/command-bar-flyout-img-collapsed.png)

> _Всплывающий элемент панели свернуто команд_

![Пример всплывающий элемент панели развернутого команд](images/command-bar-flyout-img-expanded.png)

> _Всплывающий элемент панели развернутого команд_

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

### <a name="show-commands-proactively"></a>Отображение команд заблаговременно

При отображении контекстно-зависимые команды заблаговременно, только основные команды должны отображаться по умолчанию (всплывающий элемент панели команд должны быть свернуто). Поместите наиболее важные команды в коллекцию основные команды и дополнительные команды, которые традиционно поступает в контекстном меню в коллекцию вспомогательные команды.

Заблаговременно отобразить команды, обычно обработать событие [щелкните](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) или [Tapped](/uwp/api/windows.ui.xaml.uielement.tapped) для отображения всплывающий элемент панели команд. Задайте всплывающий элемент [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) **промежуточный** или **TransientWithDismissOnPointerMoveAway** открывать всплывающий элемент в свернутом режиме без прохождения фокус.

Начиная с Windows 10 Insider Preview, текстовые элементы управления имеют свойство **SelectionFlyout** . При назначении всплывающий элемент этого свойства, оно автоматически будет отображаться при выделении текста.

### <a name="show-commands-reactively"></a>Отображение команд проектировать

При отображении контекстно-зависимые команды проектировать контекстного меню, дополнительные команды отображаются по умолчанию (должен быть развернут всплывающий элемент панели команд). В этом случае всплывающий элемент панели команд могут иметь основные и дополнительные команды, или только вспомогательные команды.

Для отображения команд в контекстном меню, вы обычно назначать всплывающий элемент свойство [ContextFlyout](/uwp/api/windows.ui.xaml.uielement.contextflyout) элемента пользовательского интерфейса. Таким образом, открыв всплывающий элемент обрабатывается с помощью элемента, и вам не нужно ничего более.

Если нужно обработать, отображается всплывающий элемент самостоятельно (например, в событии [RightTapped](/uwp/api/windows.ui.xaml.uielement.righttapped) ), задайте всплывающий элемент [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) в **стандартных** открывать всплывающий элемент в развернутом режиме и установить на него фокус.

> [!TIP]
> Дополнительные сведения о параметрах при отображении всплывающий элемент, а также как для размещения всплывающего элемента управления см. в разделе [всплывающие элементы](../controls-and-patterns/dialogs-and-flyouts/flyouts.md).

## <a name="commands-and-content"></a>Команды и содержимое

Элемент управления CommandBarFlyout имеет 2 свойства, можно использовать для добавления команд и содержимого: [свойствам PrimaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.primarycommands) и [SecondaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.secondarycommands).

По умолчанию элементы панели команд добавляются в коллекцию **PrimaryCommands**. Эти команды отображаются на панели команд и отображаются в свернутым и развернутым режимах. В отличие от CommandBar основные команды не автоматически переполнения ко второстепенным командам и может быть усечен.

Вы также можете добавить команды в коллекцию **SecondaryCommands** . Вспомогательные команды отображаются в меню часть элемента управления и отображаются только в развернутом режиме.

### <a name="app-bar-buttons"></a>Кнопки панели приложения

Свойствам PrimaryCommands и SecondaryCommands можно заполнить непосредственно с элементами управления [AppBarButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarbutton.aspx), [AppBarToggleButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbartogglebutton.aspx)и [AppBarSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarseparator.aspx) .

Кнопки панели приложения характеризуются значком и текстовой меткой. Эти элементы управления оптимизированы для использования на панели команд и меняют свой вид в зависимости от того, отображаются ли элемент управления на панели команд или в меню переполнения.

- Кнопки панели приложения, используется в качестве основных команд отображаются на панели команд с помощью только их значок; Текстовая метка не отображается. Мы рекомендуем использовать всплывающую подсказку для отображения текстового описания команды, как показано ниже.
    ```xaml
    <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
    ```
- Кнопки панели приложения, используемые как вспомогательные команды отображаются в меню, метки и значок отображается.

### <a name="other-content"></a>Другое содержимое

Всплывающий элемент панели команд можно добавить другие элементы управления, обтекания в AppBarElementContainer. Это позволяет добавлять элементы управления, такие как [DropDownButton]() или [SplitButton]()или добавить контейнеры как [StackPanel]() для создания более сложных пользовательского интерфейса.

Чтобы добавить к коллекции основной или вспомогательной команду всплывающий элемент панели команд, элемент должен реализовать интерфейс [ICommandBarElement](/uwp/api/windows.ui.xaml.controls.icommandbarelement) . AppBarElementContainer является оболочкой, реализующий этот интерфейс, поэтому можно добавить элемент на панели команд, даже если он не реализовано самого интерфейса.

Здесь AppBarElementContainer используется для добавления дополнительных элементов всплывающий элемент панели команд. SplitButton добавляется к основным командам, чтобы разрешить выбор цвета. StackPanel добавляется ко второстепенным командам, чтобы разрешить более сложные макет для элементы управления масштабированием.

> [!TIP]
> По умолчанию элементы, разработанный для на холсте приложения может выглядеть неправильно на панели команд. При добавлении элемента с помощью AppBarElementContainer, существует несколько действий, которые необходимо выполнить, чтобы сделать элемент сопоставить другие элементы панели команд.
>
> - Переопределите кисти по умолчанию с помощью [Облегченное определение стиля](/design/controls-and-patterns/xaml-styles#lightweight-styling) фона элемента и border соответствуют кнопок панели приложения.
> - Измените размер и положение элемента.
> - Заключите значки в Viewbox с ширину и высоту 16px.

> [!NOTE]
> В этом примере показан только всплывающий элемент панели команд пользовательского интерфейса, не реализует все команды, которые отображаются. Дополнительные сведения о реализации команд см. в разделе [кнопок](buttons.md) и [основы проектирования команд](../basics/commanding-basics.md).

![Всплывающий элемент панели команд с кнопкой комбинированный режим](images/command-bar-flyout-split-button.png)

> _Всплывающий элемент панели свернуто команд с открытым SplitButton_

![Всплывающий элемент панели команд с помощью сложного пользовательского интерфейса](images/command-bar-flyout-custom-ui.png)

> _Всплывающий элемент панели команд развернутого с пользовательской масштабирования пользовательского интерфейса в меню_


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

## <a name="create-a-context-menu-with-secondary-commands-only"></a>Создать контекстное меню с помощью только вспомогательные команды

Как [контекстное меню](menus.md)вместо MenuFlyout, можно использовать CommandBarFlyout с только вспомогательные команды.

![Всплывающий элемент панели команд с помощью только вспомогательные команды](images/command-bar-flyout-context-menu.png)

> _Всплывающий элемент панели команд как контекстного меню_

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

![Всплывающий элемент панели команд с помощью как раскрывающегося меню кнопки](images/command-bar-flyout-dropdown.png)

> _Раскрывающегося меню кнопки в всплывающий элемент панели команд_

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

## <a name="command-bar-flyouts-for-text-controls"></a>Всплывающие элементы панели команд для элементов управления текстом

[TextCommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout) — это всплывающий элемент панели специализированных команд, который содержит команды для редактирования текста. Каждый текстовый элемент управления автоматически отображается TextCommandBarFlyout как контекстного меню (щелкните правой кнопкой мыши), или при выделении текста. Всплывающий элемент панели команд текст адаптируется к выделению текста, чтобы отображались только соответствующих команд.

![Всплывающий элемент панели команд свернутого текста](images/command-bar-flyout-text-selection.png)

> _Всплывающий элемент панели текст команд на выделение текста_

![Всплывающий элемент панели команд развернутого текста](images/command-bar-flyout-text-full.png)

> _Всплывающий элемент панели команд развернутого текста_


### <a name="available-commands"></a>Доступные команды

В этой таблице показаны все команды, которые включены в TextCommandBarFlyout, и если они отображаются.

| Команда | Показано … |
| ------- | -------- |
| Полужирный | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Курсив | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Подчеркнутый | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Проверки правописания | Когда IsSpellCheckEnabled имеет **значение true** и с ошибками текст будет выделен. |
| Вырезать | Если текстовый элемент управления не только для чтения и текст выделен. |
| Копировать | При выборе текста. |
| Вставка | Если текстовый элемент управления не только для чтения и буфер обмена содержит содержимое. |
| Отменить | При наличии действие, которое может быть отменено. |
| Выделить все | Если текст может быть выбран. |

### <a name="custom-text-command-bar-flyouts"></a>Всплывающие элементы панели команд пользовательского текста

TextCommandBarFlyout невозможно изменить и автоматически управляется каждый текстовый элемент управления. Тем не менее вы можете заменить значение по умолчанию TextCommandBarFlyout пользовательские команды.

- Чтобы заменить TextCommandBarFlyout, которая отображается при выделении текста по умолчанию, можно создать пользовательские CommandBarFlyout (или другой тип всплывающий элемент) и назначить его свойству **SelectionFlyout** . Если SelectionFlyout значение **null**, команды не будут отображаться на выбор.
- Чтобы заменить значение по умолчанию TextCommandBarFlyout, которая отображается как контекстного меню, назначение пользовательских CommandBarFlyout (или другой тип всплывающий элемент), **ContextFlyout** свойству элемента управления текстом. Если ContextFlyout значение **null**, вместо TextCommandBarFlyout отображается всплывающее меню, показанный в предыдущих версиях управления текстом.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример команд XAML](http://go.microsoft.com/fwlink/p/?LinkId=620019)

## <a name="related-articles"></a>Смежные разделы

- [Основы проектирования команд в приложениях UWP](../basics/commanding-basics.md)
- [Класс CommandBar](https://msdn.microsoft.com/library/windows/apps/dn279427)
