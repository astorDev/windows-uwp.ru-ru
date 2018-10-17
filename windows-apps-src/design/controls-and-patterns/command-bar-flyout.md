---
author: jwmsft
Description: Command bar flyouts give users inline access to your app's most common tasks.
title: Всплывающее меню панели команд
label: Command bar flyout
template: detail.hbs
ms.author: jimwalk
ms.date: 10/2/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: abarlow
design-contact: ksulliv
dev-contact: llongley
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 22d965d14c4f10f904a4d94a18ce83721c49491c
ms.sourcegitcommit: 1c6325aa572868b789fcdd2efc9203f67a83872a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "4743243"
---
# <a name="command-bar-flyout"></a>Всплывающее меню панели команд

Всплывающий элемент панели команд позволяет предоставляют пользователям удобный доступ к распространенным задачам, отображая команды в плавающей панели инструментов, связанные с элементом на холст пользовательского интерфейса.

![Всплывающий элемент панели команд развернутого текста](images/command-bar-flyout-text-full.png)

> Связанные сведения см. в разделе [всплывающие элементы](../controls-and-patterns/dialogs-and-flyouts/flyouts.md) [меню и контекстные меню](menus.md)и [панели команд](app-bars.md).

Например, [CommandBar](app-bars.md)CommandBarFlyout имеет **свойствам PrimaryCommands** и **SecondaryCommands** свойства, которые можно использовать для добавления команд. Можно поместить команды в коллекцию, либо оба. Когда и как основные и дополнительные команды отображаются зависит от режима отображения.

Всплывающий элемент панели команд поддерживает два режима отображения: *сворачивается* и *расширяется*.

- В свернутом режиме отображаются только основные команды. Если ваш всплывающий элемент панели команд содержит основные и дополнительные команды, кнопка «Дополнительно», представленное многоточие \ [•••\], отображается. Это позволяет пользователю получить доступ ко второстепенным командам, переходит в развернутом режиме.
- В развернутом режиме отображаются основные и дополнительные команды. (Если элемент управления имеет только вспомогательные элементы, они отображаются в аналогично тому, как элемент MenuFlyout.)

| **Получить библиотеку пользовательского интерфейса Windows** |
| - |
| Этот элемент управления не включен в библиотеке пользовательского интерфейса Windows, пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке см. в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

| **API-интерфейсы платформы** | **API библиотеки пользовательского интерфейса Windows** |
| - | - |
| [Класс CommandBarFlyout](/uwp/api/windows.ui.xaml.controls.commandbarflyout), [TextCommandBarFlyout класса](/uwp/api/windows.ui.xaml.controls.textcommandbarflyout), [класс AppBarButton](/uwp/api/windows.ui.xaml.controls.appbarbutton), [класс AppBarToggleButton](/uwp/api/windows.ui.xaml.controls.appbartogglebutton), [класс AppBarSeparator](/uwp/api/windows.ui.xaml.controls.appbarseparator) | [Класс CommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.commandbarflyout) [TextCommandBarFlyout класса](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout) |

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Используйте элемент управления CommandBarFlyout для отображения коллекции команд для пользователя, таких как кнопки и пунктов меню, в контексте текущего элемента на холсте приложения.

TextCommandBarFlyout отображает текст команды в элементах управления TextBox, TextBlock, RichEditBox, RichTextBlock и PasswordBox. Команды автоматически настраиваются соответствующим образом для выделенного текста. Используйте CommandBarFlyout для замены команд текста по умолчанию на элементы управления текстом.

Для отображения контекстно-зависимые команды для элементов списка следуйте рекомендациям в [Контекстные команды для коллекций и списков](collection-commanding.md).

### <a name="commandbarflyout-vs-menuflyout"></a>CommandBarFlyout vs MenuFlyout

Для отображения команд в контекстном меню, можно использовать CommandBarFlyout или MenuFlyout. Мы рекомендуем CommandBarFlyout, так как он предоставляет дополнительные функции по сравнению с MenuFlyout. Поведение и вид MenuFlyout или используйте всплывающий элемент панели команд полный вместе с главных и вспомогательных команд можно использовать CommandBarFlyout с только вспомогательные команды.

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

Для вызова всплывающий элемент или меню, связанный с элементом на холсте пользовательского интерфейса обычно существует два способа: _упреждающего вызова_ и _реактивный вызов_.

Упреждающее вызову команды отображаются автоматически, когда пользователь взаимодействует с элементом, который команды связаны с. Например команды форматирования текста может мультимедийном пользователь выбирает текст в текстовом поле. В этом случае всплывающий элемент панели команд не имеет фокуса. Вместо этого он представляет соответствующих команд вблизи элемент, который пользователь взаимодействует с. Если пользователь не взаимодействовать с командами, они являются закрыта.

В группе реактивный вызов команды отображаются в ответ на явное действие пользователя для запроса команды; например щелчок правой кнопкой мыши. Это соответствует традиционных концепция [контекстное меню](menus.md).

Вы можете использовать CommandBarFlyout способом или даже оба типа привязки.

## <a name="create-a-command-bar-flyout"></a>Создание всплывающий элемент панели команд

> **Предварительный просмотр**: CommandBarFlyout требуется [последние сборки Windows 10 Insider Preview и пакет SDK](https://insider.windows.com/for-developers/) или [Библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

В этом примере показано, как создать всплывающий элемент панели команд и использовать его как заблаговременно, так и устраняйте. При касании изображения, всплывающий элемент отображается в свернутом режиме. При отображении как контекстного меню, всплывающий элемент отображается в развернутом режиме. В любом случае пользователь может развернуть или свернуть всплывающий элемент после открытия.

:::row:::
    :::column:::
        A collapsed command bar flyout<br/>
        ![Example of a collapsed command bar flyout](images/command-bar-flyout-img-collapsed.png)
    :::column-end:::
    :::column:::
        An expanded command bar flyout<br/>
        ![Example of an expanded command bar flyout](images/command-bar-flyout-img-expanded.png)
    :::column-end:::
:::row-end:::

```xaml
<Grid>
    <Grid.Resources>
        <CommandBarFlyout x:Name="ImageCommandsFlyout">
            <AppBarButton Icon="OutlineStar" ToolTipService.ToolTip="Favorite"/>
            <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
            <AppBarButton Icon="Share" ToolTipService.ToolTip="Share"/>
            <CommandBarFlyout.SecondaryCommands>
                <AppBarButton Label="Rotate" Icon="Rotate"/>
                <AppBarButton Label="Delete" Icon="Delete"/>
            </CommandBarFlyout.SecondaryCommands>
        </CommandBarFlyout>
    </Grid.Resources>

    <Image Source="Assets/licorice.png" Width="300"
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

При отображении контекстно-зависимые команды заблаговременно, только основные команды должно отображаться по умолчанию (всплывающий элемент панели команд должны быть свернуто). Поместите наиболее важные команды в коллекцию основные команды, а дополнительные команды, которые обычно поступает в контекстном меню в коллекцию вспомогательные команды.

Заблаговременно отобразить команды, обычно обработать событие [щелкните](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) или [Tapped](/uwp/api/windows.ui.xaml.uielement.tapped) для отображения всплывающий элемент панели команд. Задайте всплывающий элемент [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) **промежуточный** или **TransientWithDismissOnPointerMoveAway** , чтобы открыть всплывающее окно в свернутом режиме без учета фокус.

Начиная с Windows 10 Insider Preview, текстовые элементы управления имеют свойство **SelectionFlyout** . При назначении всплывающий элемент этого свойства, оно автоматически будет отображаться при выделении текста.

### <a name="show-commands-reactively"></a>Отображение команд устраняйте

При отображении контекстно-зависимые команды устраняйте контекстного меню, дополнительные команды отображаются по умолчанию (должен быть развернут всплывающий элемент панели команд). В этом случае всплывающий элемент панели команд могут иметь основные и дополнительные команды, или только вспомогательные команды.

Для отображения команд в контекстном меню, вы обычно назначить всплывающий элемент свойство [ContextFlyout](/uwp/api/windows.ui.xaml.uielement.contextflyout) элемента пользовательского интерфейса. Таким образом, открыв всплывающий элемент обрабатывается с помощью элемента, и больше ничего делать не нужно.

Если нужно обработать, отображается всплывающий элемент самостоятельно (например, в событии [RightTapped](/uwp/api/windows.ui.xaml.uielement.righttapped) ), задайте всплывающий элемент [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) в **стандартной** открывать всплывающий элемент в развернутом режиме и установить на него фокус.

> [!TIP]
> Дополнительные сведения о параметрах при отображении всплывающий элемент, а также как для размещения всплывающего элемента управления см. в разделе [всплывающие элементы](../controls-and-patterns/dialogs-and-flyouts/flyouts.md).

## <a name="commands-and-content"></a>Команды и содержимое

Элемент управления CommandBarFlyout имеет 2 свойства, которые можно использовать для добавления команд и содержимого: [PrimaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.primarycommands) и [SecondaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.secondarycommands).

По умолчанию элементы панели команд добавляются в коллекцию **PrimaryCommands**. Эти команды отображаются на панели команд и отображаются в свернутым и развернутым режимах. В отличие от CommandBar основные команды не автоматически переполнения ко второстепенным командам и может быть усечен.

Вы также можете добавить команды в коллекцию **SecondaryCommands** . Вспомогательные команды отображаются в меню часть элемента управления и отображаются только в развернутом режиме.

### <a name="app-bar-buttons"></a>Кнопки панели приложения

Свойствам PrimaryCommands и SecondaryCommands можно заполнить непосредственно с элементами управления [AppBarButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarbutton.aspx), [AppBarToggleButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbartogglebutton.aspx)и [AppBarSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarseparator.aspx) .

Кнопки панели приложения характеризуются значком и текстовой меткой. Эти элементы управления оптимизированы для использования на панели команд и меняют свой вид в зависимости от того, отображаются ли элемент управления на панели команд или в меню переполнения.

- Кнопки панели приложения, используется в качестве основных команд отображаются на панели команд с помощью только их значок; Текстовая метка не отображается. Мы рекомендуем использовать всплывающую подсказку для отображения текстового описания команды, как показано ниже.
    ```xaml
    <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
    ```
- Используется как вспомогательные команды, отображаются в меню, значок отображается и метки кнопок панели приложения.

### <a name="other-content"></a>Другое содержимое

Всплывающий элемент панели команд можно добавить другие элементы управления, обтекания в AppBarElementContainer. Это позволяет добавлять элементы управления, такие как [DropDownButton]() или [SplitButton]()или добавить контейнеры, такие как [StackPanel]() для создания более сложных пользовательских Интерфейсов.

> [!NOTE]
> Чтобы добавить к коллекции основной или вспомогательной команду всплывающий элемент панели команд, элемент должен реализовывать интерфейс [ICommandBarElement](/uwp/api/windows.ui.xaml.controls.icommandbarelement) . AppBarElementContainer является оболочкой, реализующий этот интерфейс, поэтому можно добавить элемент на панели команд, даже если он не реализовано самого интерфейса.

Здесь AppBarElementContainer используется для добавления дополнительных элементов всплывающий элемент панели команд. SplitButton добавляется к основным командам, чтобы разрешить выбор цвета. StackPanel добавляется вспомогательные команды, чтобы разрешить более сложные макет для элементы управления масштабированием.

> [!NOTE]
> В этом примере показан только всплывающий элемент панели команд пользовательского интерфейса, не реализует все команды, которые отображаются. Дополнительные сведения о реализации команд см. в разделе [кнопки](buttons.md) и [основы проектирования команд](../basics/commanding-basics.md).

:::row:::
    :::column:::
        A collapsed command bar flyout with an open SplitButton<br/>
        ![A command bar flyout with a split button](images/command-bar-flyout-split-button.png)
    :::column-end:::
    :::column:::
        An expanded command bar flyout with custom zoom UI in the menu<br/>
        ![A command bar flyout with complex UI](images/command-bar-flyout-complex-ui.png)
    :::column-end:::
:::row-end:::

```xaml
<CommandBarFlyout>
    <AppBarButton Icon="Cut" ToolTipService.ToolTip="Cut"/>
    <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
    <AppBarButton Icon="Paste" ToolTipService.ToolTip="Paste"/>
    <!-- Color controls -->
    <AppBarElementContainer>
        <SplitButton Height="Auto" Margin="0,4,0,0"
                     ToolTipService.ToolTip="Colors"
                     Background="{ThemeResource AppBarItemBackgroundThemeBrush}">
            <SplitButton.Content>
                <Rectangle Width="20" Height="20">
                    <Rectangle.Fill>
                        <SolidColorBrush Color="Red"/>
                    </Rectangle.Fill>
                </Rectangle>
            </SplitButton.Content>
            <SplitButton.Flyout>
                <MenuFlyout>
                    <MenuFlyoutItem Text="Red"/>
                    <MenuFlyoutItem Text="Yellow"/>
                    <MenuFlyoutItem Text="Green"/>
                    <MenuFlyoutItem Text="Blue"/>
                </MenuFlyout>
            </SplitButton.Flyout>
        </SplitButton>
    </AppBarElementContainer>
    <!-- end Color controls -->
    <CommandBarFlyout.SecondaryCommands>
        <!-- Zoom controls -->
        <AppBarElementContainer>
            <AppBarElementContainer.Resources>
                <Style TargetType="Button">
                    <Setter Property="Background"
                            Value="{ThemeResource AppBarItemBackgroundThemeBrush}"/>
                </Style>
                <Style TargetType="TextBlock">
                    <Setter Property="VerticalAlignment" Value="Center"/>
                </Style>
            </AppBarElementContainer.Resources>
            <Grid Margin="12,0">
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="86"/>
                    <ColumnDefinition Width="Auto"/>
                </Grid.ColumnDefinitions>
                <TextBlock Text="Zoom"/>
                <StackPanel Orientation="Horizontal" Grid.Column="1">
                    <Button>
                        <SymbolIcon Symbol="Remove"/>
                    </Button>
                    <TextBlock Text="50%" Width="40"
                               HorizontalTextAlignment="Center"/>
                    <Button>
                        <SymbolIcon Symbol="Add"/>
                    </Button>
                </StackPanel>
            </Grid>
        </AppBarElementContainer>
        <!-- end Zoom controls -->
        <AppBarSeparator/>
        <AppBarButton Label="Undo" Icon="Undo"/>
        <AppBarButton Label="Redo" Icon="Redo"/>
        <AppBarButton Label="Select all"/>
    </CommandBarFlyout.SecondaryCommands>
</CommandBarFlyout>
```

## <a name="create-a-context-menu-with-secondary-commands-only"></a>Создать контекстное меню с помощью только вспомогательные команды

Как [контекстное меню](menus.md)вместо MenuFlyout, можно использовать CommandBarFlyout с только вспомогательные команды.

![Всплывающий элемент панели команд с помощью только вспомогательные команды](images/command-bar-flyout-context-menu.png)

```xaml
<Grid>
    <Grid.Resources>
        <!-- A command bar flyout with only secondary commands. -->
        <CommandBarFlyout x:Name="ContextMenu">
            <CommandBarFlyout.SecondaryCommands>
                <AppBarButton Label="Pin" Icon="Pin"/>
                <AppBarButton Label="Unpin" Icon="UnPin"/>
                <AppBarButton Label="Copy" Icon="Copy"/>
                <AppBarSeparator />
                <AppBarButton Label="Properties"/>
            </CommandBarFlyout.SecondaryCommands>
        </CommandBarFlyout>
    </Grid.Resources>

    <Image Source="Assets/licorice.png" Width="300"
           ContextFlyout="{x:Bind ContextMenu}"/>
</Grid>
```

Можно также использовать CommandBarFlyout с DropDownButton для создания стандартного меню.

![Всплывающий элемент панели команд с в раскрывающемся меню кнопки](images/command-bar-flyout-button-menu.png)

```xaml
<DropDownButton Content="Mail">
    <DropDownButton.Flyout>
        <CommandBarFlyout Placement="BottomEdgeAlignedLeft">
            <CommandBarFlyout.SecondaryCommands>
                <AppBarButton Icon="MailForward" Label="Forward"/>
                <AppBarButton Icon="MailReply" Label="Reply"/>
                <AppBarButton Icon="MailReplyAll" Label="Reply all"/>
            </CommandBarFlyout.SecondaryCommands>
        </CommandBarFlyout>
    </DropDownButton.Flyout>
</DropDownButton>
```

## <a name="command-bar-flyouts-for-text-controls"></a>Всплывающие элементы панели команд для элементов управления текстом

[TextCommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout) — это специализированный команду всплывающий элемент панели, содержащий команды для редактирования текста. Каждый текстовый элемент управления автоматически отображается TextCommandBarFlyout как контекстного меню (щелкните правой кнопкой мыши), или при выделении текста. Всплывающий элемент панели команд текст адаптируется к выделению текста, чтобы отображались только соответствующие команды.

:::row:::
    :::column:::
        A text command bar flyout on text selection<br/>
        ![A collapsed text command bar flyout](images/command-bar-flyout-text-selection.png)
    :::column-end:::
    :::column:::
        An expanded text command bar flyout<br/>
        ![An expanded text command bar flyout](images/command-bar-flyout-text-full.png)
    :::column-end:::
:::row-end:::

### <a name="available-commands"></a>Доступные команды

В этой таблице показаны все команды, которые включены в TextCommandBarFlyout, и если они отображаются.

| Команда | Отображается … |
| ------- | -------- |
| Полужирный | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Курсив | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Подчеркнутый | Если текстовый элемент управления не только для чтения (RichEditBox только). |
| Проверки правописания | Когда IsSpellCheckEnabled имеет **значение true** и с ошибками текст будет выделен. |
| Вырезать | Если текстовый элемент управления не только для чтения и текст выделен. |
| Копировать | При выборе текста. |
| Вставка | Если текстовый элемент управления не только для чтения и имеет содержимое буфера обмена. |
| Отменить | При наличии действие, которое может быть отменено. |
| Выделить все | Если текст может быть выбран. |

### <a name="custom-text-command-bar-flyouts"></a>Всплывающие элементы панели команд пользовательского текста

TextCommandBarFlyout не может быть настроен и автоматически управляется каждый текстовый элемент управления. Тем не менее можно заменить значение по умолчанию TextCommandBarFlyout пользовательские команды.

- Чтобы заменить TextCommandBarFlyout, которая отображается при выделении текста по умолчанию, можно создать пользовательские CommandBarFlyout (или другой тип всплывающий элемент) и присвоить свойству **SelectionFlyout** . Если SelectionFlyout присвоено **значение null**, команды не будут отображаться на выбор.
- Для замены по умолчанию TextCommandBarFlyout, которая отображается как контекстного меню, назначьте пользовательские CommandBarFlyout (или другого типа всплывающий элемент), **ContextFlyout** к свойству элемента управления текстом. Если ContextFlyout присвоено **значение null**, вместо TextCommandBarFlyout отображается всплывающее меню, показанный в предыдущих версиях текстовый элемент управления.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример команд XAML](http://go.microsoft.com/fwlink/p/?LinkId=620019)

## <a name="related-articles"></a>Смежные разделы

- [Основы проектирования команд в приложениях UWP](../basics/commanding-basics.md)
- [Класс CommandBar](https://msdn.microsoft.com/library/windows/apps/dn279427)
