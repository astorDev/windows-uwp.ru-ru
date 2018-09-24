---
author: jwmsft
Description: Command bar flyouts give users inline access to your app's most common tasks.
title: Всплывающий элемент панели команд
label: Command bar flyout
template: detail.hbs
ms.author: jimwalk
ms.date: 07/19/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: abarlow
design-contact: ksulliv
dev-contact: llongley
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: ec532749fc2dacfc56e80ee2830da36f71c75b2f
ms.sourcegitcommit: 194ab5aa395226580753869c6b66fce88be83522
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2018
ms.locfileid: "4151373"
---
# <a name="command-bar-flyout"></a>Всплывающий элемент панели команд

> [!IMPORTANT]
> В этой статье описана еще не выпущенная функция, которая может быть существенно изменена до коммерческого выпуска. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации. Функции предварительного просмотра, требуют [последние сборки Windows 10 Insider Preview и пакет SDK](https://insider.windows.com/for-developers/) или [Библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

Всплывающий элемент панели команд позволяет предоставляют пользователям удобный доступ к распространенным задачам, отображая команды в перемещаемом инструментов, связанных с элементом на холст пользовательского интерфейса.

![Всплывающий элемент развернутое текстовое командной строке](images/command-bar-flyout-text-full.png)

> Связанные сведения см. в разделе [всплывающие элементы](../controls-and-patterns/dialogs-and-flyouts/flyouts.md) [меню и контекстные меню](menus.md)и [панели команд](app-bars.md).

Например, [CommandBar](app-bars.md)CommandBarFlyout имеет **свойствам PrimaryCommands** и **SecondaryCommands** свойства, которые можно использовать для добавления команд. Можно поместить команды в коллекцию, либо оба. Когда и как отображаются основные и дополнительные команды, зависит от режима отображения.

Всплывающий элемент панели команд имеет два режима отображения: *свернуты* , либо *развернуты*.

- В свернутом режиме отображаются только основные команды. Если ваш всплывающий элемент панели команд содержит основные и дополнительные команды, кнопка «Дополнительно», представленное многоточие \ [•••\], отображается. Это позволяет пользователю получить доступ ко второстепенным командам, которую осуществляется в развернутом режиме.
- В развернутом режиме отображаются основные и дополнительные команды. (Если элемент управления имеет только вспомогательные элементы, они отображаются в аналогично тому, как элемент MenuFlyout.)

| **Получить библиотеку пользовательского интерфейса Windows** |
| - |
| Этот элемент управления не включен в библиотеке Windows пользовательского интерфейса, пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке см. в разделе [Общие сведения о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

| **API-интерфейсы платформы** | **API библиотеки пользовательского интерфейса Windows** |
| - | - |
| [Класс CommandBarFlyout](/uwp/api/windows.ui.xaml.controls.commandbarflyout), [TextCommandBarFlyout класса](/uwp/api/windows.ui.xaml.controls.textcommandbarflyout), [класс AppBarButton](/uwp/api/windows.ui.xaml.controls.appbarbutton), [класс AppBarToggleButton](/uwp/api/windows.ui.xaml.controls.appbartogglebutton), [класс AppBarSeparator](/uwp/api/windows.ui.xaml.controls.appbarseparator) | [Класс CommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.commandbarflyout) [TextCommandBarFlyout класса](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout) |

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Используйте элемент управления CommandBarFlyout для отображения коллекции команд для пользователя, таких как кнопки и пунктов меню, в контексте элемента на холсте приложения.

TextCommandBarFlyout отображает текст команды в элементах управления TextBox, TextBlock, RichEditBox, RichTextBlock и PasswordBox. Команды, автоматически настраиваются соответствующим образом на текущий выбор текста. Используйте CommandBarFlyout для замены команд текста по умолчанию на элементы управления текстом.

Для отображения контекстно-зависимые команды для элементов списка следуйте рекомендациям в [Контекстные команды для коллекций и списков](collection-commanding.md).

### <a name="commandbarflyout-vs-menuflyout"></a>CommandBarFlyout vs MenuFlyout

Для отображения команд в контекстном меню, можно использовать CommandBarFlyout или MenuFlyout. Мы рекомендуем CommandBarFlyout, так как он предоставляет дополнительные функции по сравнению с MenuFlyout. Для получения поведение и вид MenuFlyout или используйте всплывающий элемент полной командной строке с главных и вспомогательных команд можно использовать CommandBarFlyout с только вспомогательные команды.

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

Для вызова всплывающий элемент или меню, связанный с элементом на холст пользовательского интерфейса обычно существует два способа: _упреждающего вызова_ и _реактивный вызов_.

В группе упреждающего вызов команды отображаются автоматически, когда пользователь взаимодействует с элементом, который команды связаны с. Например команды форматирования текста может мультимедийном пользователь выбирает текст в текстовом поле. В этом случае всплывающий элемент панели команд не принимать фокус. Вместо этого он представляет соответствующих команд вблизи элемент, который пользователь взаимодействует с. Если пользователь не взаимодействовать с командами, они являются закрывается.

В группе реактивный вызов команды отображаются в ответ на явное действие пользователя для запроса команды; например щелчок правой кнопкой мыши. Это соответствует традиционных концепцию [контекстное меню](menus.md).

Вы можете использовать CommandBarFlyout способ или даже оба типа привязки.

## <a name="create-a-command-bar-flyout"></a>Создание всплывающего элемента панель команд

> **Предварительный просмотр**: CommandBarFlyout требуется [последние сборки Windows 10 Insider Preview и пакет SDK](https://insider.windows.com/for-developers/) или [Библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/).

В этом примере показано, как создать всплывающий элемент панели команд и использовать его как заблаговременно, так и устраняйте. При касании изображения, всплывающий элемент отображается в свернутом режиме. При отображении как контекстного меню, всплывающий элемент отображается в развернутом режиме. В любом случае пользователь может развернуть или свернуть всплывающий элемент после открытия.

:::row:::
    :::column:::
        Всплывающий элемент свернуто командной строке<br/>
        ![Пример свернуто командной строке всплывающего элемента](images/command-bar-flyout-img-collapsed.png)
    :::column-end:::
    :::column:::
        Всплывающий элемент панели расширенная команда<br/>
        ![Пример панели всплывающем элементе расширенная команда](images/command-bar-flyout-img-expanded.png)
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

При отображении контекстно-зависимые команды заблаговременно, только основные команды должны отображаться по умолчанию (свернуто всплывающий элемент панели команд следует). Поместите наиболее важные команды в коллекцию основные команды и дополнительные команды, которые традиционно поступает в контекстном меню в коллекцию вспомогательные команды.

Заблаговременно отобразить команды, вы обычно обрабатывать событие [щелкните](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) или [Tapped](/uwp/api/windows.ui.xaml.uielement.tapped) для отображения всплывающего элемента панель команд. Задайте всплывающий элемент [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) **промежуточный** или **TransientWithDismissOnPointerMoveAway** открывать всплывающий элемент в свернутом режиме без прохождения фокус.

Начиная с Windows 10 Insider Preview, текстовые элементы управления имеют свойство **SelectionFlyout** . При назначении всплывающий элемент этого свойства, автоматически отображается при выделении текста.

### <a name="show-commands-reactively"></a>Отображение команд устраняйте

При отображении контекстно-зависимые команды устраняйте контекстного меню, дополнительные команды отображаются по умолчанию (должен быть развернут всплывающий элемент панели команд). В этом случае всплывающий элемент панели команд могут иметь основные и дополнительные команды, или только вспомогательные команды.

Для отображения команд в контекстном меню, вы обычно назначать всплывающий элемент свойство [ContextFlyout](/uwp/api/windows.ui.xaml.uielement.contextflyout) элемента пользовательского интерфейса. Таким образом, открыв всплывающий элемент обрабатывается с помощью элемента, и вам не нужно ничего более.

Если нужно обработать, отображается всплывающий элемент самостоятельно (например, в событии [RightTapped](/uwp/api/windows.ui.xaml.uielement.righttapped) ), задать всплывающий элемент [ShowMode](/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.showmode) в **стандартной** открывать всплывающий элемент в развернутом режиме и установить на него фокус.

> [!TIP]
> Дополнительные сведения о параметрах при отображении всплывающий элемент, а также как управлять размещения всплывающего элемента см. в разделе [всплывающие элементы](../controls-and-patterns/dialogs-and-flyouts/flyouts.md).

## <a name="commands-and-content"></a>Команды и содержимое

Элемент управления CommandBarFlyout имеет 2 свойства, которые можно использовать для добавления команд и содержимого: [PrimaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.primarycommands) и [SecondaryCommands](/uwp/api/windows.ui.xaml.controls.commandbarflyout.secondarycommands).

По умолчанию элементы панели команд добавляются в коллекцию **PrimaryCommands**. Эти команды отображаются на панели команд и отображаются в обоих режимах свернутым и развернутым. В отличие от CommandBar основные команды не автоматически переполнения ко второстепенным командам и может быть усечен.

Вы также можете добавить команды в коллекцию **SecondaryCommands** . Вспомогательные команды отображаются в меню часть элемента управления и отображаются только в развернутом режиме.

### <a name="app-bar-buttons"></a>Кнопки панели приложения

Свойствам PrimaryCommands и SecondaryCommands можно заполнить непосредственно с элементами управления [AppBarButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarbutton.aspx), [AppBarToggleButton](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbartogglebutton.aspx)и [AppBarSeparator](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.appbarseparator.aspx) .

Кнопки панели приложения характеризуются значком и текстовой меткой. Эти элементы управления оптимизированы для использования на панели команд и меняют свой вид в зависимости от того, отображаются ли элемент управления на панели команд или в меню переполнения.

- Кнопки панели приложения, используется в качестве основных команд отображаются на панели команд с только их значком; Текстовая метка не отображается. Мы рекомендуем использовать всплывающую подсказку для отображения предоставление текстового описания команды, как показано здесь.
    ```xaml
    <AppBarButton Icon="Copy" ToolTipService.ToolTip="Copy"/>
    ```
- Кнопки панели приложения, используемые как вспомогательные команды отображаются в меню с помощью метки и значок отображается.

### <a name="other-content"></a>Другое содержимое

Можно добавить другие элементы управления всплывающий элемент панели команд, обтекания в AppBarElementContainer. Это позволяет добавлять элементы управления, такие как [DropDownButton]() или [SplitButton]()или добавить контейнеры, такие как [StackPanel]() для создания более сложных пользовательских Интерфейсов.

> [!NOTE]
> Чтобы добавить к коллекциям основной или вспомогательной команд командной строке всплывающего элемента, элемент должен реализовывать интерфейс [ICommandBarElement](/uwp/api/windows.ui.xaml.controls.icommandbarelement) . AppBarElementContainer является оболочкой, реализующий этот интерфейс, поэтому можно добавить элемент на панели команд, даже если он не реализовано самого интерфейса.

Здесь AppBarElementContainer используется для добавления дополнительных элементов во всплывающем элементе панели команд. SplitButton добавляется к основным командам, чтобы разрешить выбор цвета. StackPanel добавляется вспомогательные команды, чтобы разрешить более сложные макет для элементы управления масштабированием.

> [!NOTE]
> В этом примере показано только командной строке всплывающий элемент пользовательского интерфейса, не реализует все команды, которые отображаются. Дополнительные сведения о реализации команд см. в разделе [кнопки](buttons.md) и [основы проектирования команд](../basics/commanding-basics.md).

:::row:::
    :::column:::
        Всплывающий элемент панели свернуто команду с открытым SplitButton<br/>
        ![Всплывающий элемент панели команд с кнопкой "комбинированный режим"](images/command-bar-flyout-split-button.png)
    :::column-end:::
    :::column:::
        Расширенная команда окна всплывающий элемент с пользовательской масштабирования пользовательского интерфейса в меню<br/>
        ![Всплывающий элемент панели команд с сложный пользовательский Интерфейс](images/command-bar-flyout-complex-ui.png)
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

![Командной строке всплывающий элемент с помощью как раскрывающемся меню кнопки](images/command-bar-flyout-button-menu.png)

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

[TextCommandBarFlyout](/uwp/api/microsoft.ui.xaml.controls.textcommandbarflyout) — это всплывающий элемент специализированных командной строке, содержащий команды для редактирования текста. Каждый текстовый элемент управления автоматически отображается TextCommandBarFlyout как контекстного меню (щелкните правой кнопкой мыши), или при выделении текста. Всплывающий элемент панели команд текст адаптируется к выделению текста, чтобы отображались только соответствующие команды.

:::row:::
    :::column:::
        Всплывающий элемент панели команд текст на выделение текста<br/>
        ![Всплывающий элемент панели команд свернутого текста](images/command-bar-flyout-text-selection.png)
    :::column-end:::
    :::column:::
        Всплывающий элемент развернутое текстовое командной строке<br/>
        ![Всплывающий элемент развернутое текстовое командной строке](images/command-bar-flyout-text-full.png)
    :::column-end:::
:::row-end:::

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
| Вставка | Если текстовый элемент управления не только для чтения и буфера обмена имеет содержимого. |
| Отменить | При наличии действие, которое может быть отменено. |
| Выделить все | Если текст может быть выбран. |

### <a name="custom-text-command-bar-flyouts"></a>Всплывающие элементы панели команд пользовательского текста

TextCommandBarFlyout невозможно изменить и автоматически управляется каждый текстовый элемент управления. Тем не менее можно заменить значение по умолчанию TextCommandBarFlyout пользовательские команды.

- Чтобы заменить TextCommandBarFlyout, которая отображается при выделении текста по умолчанию, можно создать пользовательские CommandBarFlyout (или другой тип всплывающий элемент) и назначить его свойству **SelectionFlyout** . Если SelectionFlyout присвоено **значение null**, команды не будут отображаться на выбор.
- Чтобы заменить значение по умолчанию TextCommandBarFlyout, которая отображается как контекстного меню, назначьте пользовательские CommandBarFlyout (или другой тип всплывающий элемент) свойство **ContextFlyout** для элемента управления текстом. Если ContextFlyout присвоено **значение null**, вместо TextCommandBarFlyout отображается всплывающее меню, показанный в предыдущих версиях управления текстом.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример команд XAML](http://go.microsoft.com/fwlink/p/?LinkId=620019)

## <a name="related-articles"></a>Смежные разделы

- [Основы проектирования команд в приложениях UWP](../basics/commanding-basics.md)
- [Класс CommandBar](https://msdn.microsoft.com/library/windows/apps/dn279427)
