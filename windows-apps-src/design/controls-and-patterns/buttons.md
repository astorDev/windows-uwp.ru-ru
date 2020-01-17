---
Description: Кнопка предоставляет пользователю возможность вызвать немедленное действие.
title: Кнопки
label: Buttons
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: f04d1a3c-7dcd-4bc8-9586-3396923b312e
pm-contact: kisai
design-contact: kimsea
dev-contact: mitra
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: a3cd8a0c988df08047b10911a4d4f55e3ba1cb6e
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684110"
---
# <a name="buttons"></a>Кнопки

Кнопка предоставляет пользователю возможность вызвать немедленное действие. Некоторые кнопки предназначены специально для конкретных задач, таких как навигация, повторяющиеся действия или отображение меню.

![Примеры кнопок](images/controls/button.png)

[Расширяемый язык разметки для приложений XAML](../../xaml-platform/xaml-overview.md) предоставляет стандартный и несколько специализированных элементов управления "Кнопка".

Элемент | Описание
------- | -----------
[Кнопка](/uwp/api/windows.ui.xaml.controls.button) | Кнопка, инициирующая немедленное действие. Может использоваться в связке со свойством [Command](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) или событием [Click](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.command).
[RepeatButton](/uwp/api/windows.ui.xaml.controls.primitives.repeatbutton) | Кнопка, которая в нажатом состоянии постоянно вызывает событие [Click](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click).
[HyperlinkButton](/uwp/api/windows.ui.xaml.controls.hyperlinkbutton) | Стилизованная под гиперссылку кнопка, которая используется для навигации. См. подробнее о [гиперссылках](hyperlinks.md).
[DropDownButton](/uwp/api/windows.ui.xaml.controls.dropdownbutton) | Кнопка со значком шеврона, которая открывает прикрепленное всплывающее меню.
[SplitButton](/uwp/api/windows.ui.xaml.controls.splitbutton) | Двухсторонняя кнопка. Одна сторона инициирует действие, а другая открывает меню.
[ToggleSplitButton](/uwp/api/windows.ui.xaml.controls.togglesplitbutton) | Двухсторонняя кнопка переключения. Одна сторона выполняет функцию переключателя, а другая сторона открывает меню.
[ToggleButton](/uwp/api/windows.ui.xaml.controls.primitives.togglebutton) | Кнопка, которая может быть включена или отключена.

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| **DropDownButton**, **SplitButton** и **ToggleSplitButton** являются компонентами библиотеки пользовательского интерфейса Windows — пакета NuGet, в который входят новые элементы управления, а также компоненты пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

| **API платформы** | **API библиотеки пользовательского интерфейса Windows** |
| - | - |
| [Событие Click](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click)<br/> [Свойство Command](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.command) | [Класс DropDownButton](/uwp/api/microsoft.ui.xaml.controls.dropdownbutton)<br/> [Класс SplitButton](/uwp/api/microsoft.ui.xaml.controls.splitbutton)<br/> [Класс ToggleSplitButton](/uwp/api/microsoft.ui.xaml.controls.togglesplitbutton) |

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Элемент управления **Button** позволяет пользователю инициировать немедленное действие, например отправку формы.

Не используйте элемент управления **Button** для перехода на другую страницу. Вместо него используйте элемент управления [HyperlinkButton](/uwp/api/windows.ui.xaml.controls.hyperlinkbutton). См. подробнее о [гиперссылках](hyperlinks.md).

> [!IMPORTANT]
> Для навигации по мастеру используйте кнопки *Назад* и *Далее*. Для других видов навигации на уровень выше или ниже используйте [кнопку "Назад"](../basics/navigation-history-and-backwards-navigation.md).

Элемент управления **RepeatButton** позволяет пользователю повторно активировать действие. Например, с помощью **RepeatButton** можно увеличивать или уменьшать значение в счетчике.

Элемент управления **DropDownButton** можно использовать, если у кнопки есть всплывающее меню с дополнительными вариантами. Значок шеврона по умолчанию является визуальным индикатором наличия у кнопки всплывающего меню.

Элемент управления **SplitButton** позволяет пользователю инициировать немедленное действие или по отдельности выбирать дополнительные варианты.

Используйте элемент управления **ToggleButton**, чтобы пользователи могли моментально переключаться между двумя взаимоисключающими состояниями, и при этом кнопка лучше всего вписывается в пользовательский интерфейс. Если кнопка не подходит, лучше использовать [AppBarToggleButton](/uwp/api/windows.ui.xaml.controls.appbartogglebutton), [CheckBox](checkbox.md), [RadioButton](radio-button.md) или [ToggleSwitch](toggles.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">XAML Controls Gallery</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/Button">открыть приложение и увидеть кнопку в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

В этом примере используются две кнопки, **Разрешить** и **Блокировать**, в диалоговом окне, которое запрашивает доступ к расположению.

![Пример кнопок, используемых в диалоговом окне](images/dialogs/dialog_RS2_two_button.png)

## <a name="create-a-button"></a>Создание кнопки

В этом примере рассматривается кнопка, которая реагирует на щелчок.

Создайте кнопку в XAML.

```xaml
<Button Content="Subscribe" Click="SubscribeButton_Click"/>
```

Либо создайте кнопку в коде.

```csharp
Button subscribeButton = new Button();
subscribeButton.Content = "Subscribe";
subscribeButton.Click += SubscribeButton_Click;

// Add the button to a parent container in the visual tree.
stackPanel1.Children.Add(subscribeButton);
```

Обработайте событие [Click](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click).

```csharp
private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
{
    // Call app specific code to subscribe to the service. For example:
    ContentDialog subscribeDialog = new ContentDialog
    {
        Title = "Subscribe to App Service?",
        Content = "Listen, watch, and play in high definition for only $9.99/month. Free to try, cancel anytime.",
        CloseButtonText = "Not Now",
        PrimaryButtonText = "Subscribe",
        SecondaryButtonText = "Try it"
    };

    ContentDialogResult result = await subscribeDialog.ShowAsync();
}
```

### <a name="button-interaction"></a>Взаимодействие с кнопкой

Если коснуться элемента управления **Button** пальцем или стилусом либо навести на нее указатель и нажать левую кнопку мыши, кнопка вызывает событие [Click](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click). Если кнопка находится в фокусе клавиатуры, при нажатии клавиши ВВОД или ПРОБЕЛ также происходит вызов события **Click**.

Обрабатывать низкоуровневые события [PointerPressed](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed) с помощью объекта **Button** обычно нельзя, поскольку для него предусмотрено поведение **Click**. Дополнительные сведения см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://docs.microsoft.com/windows/uwp/xaml-platform/events-and-routed-events-overview).

Порядок вызова кнопкой события **Click** можно менять путем изменения свойства [ClickMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.clickmode). Значением по умолчанию для **ClickMode** является **Release**, но для него также можно задать значения **Hover** или **Press**. Если для **ClickMode** задано значение **Hover**, событие **Click** невозможно вызвать нажатием клавиши или касанием.


### <a name="button-content"></a>Содержимое кнопки

**Button** является элементом управления содержимым класса [ContentControl](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentControl). Ее свойство содержимого XAML — [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.contentcontrol.content), благодаря чему возможно использование подобного синтаксиса: `<Button>A button's content</Button>`. В качестве содержимого кнопки можно задать любой объект. Если содержимым является объект [UIElement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement), он отображается на кнопке. Если содержимым является другой тип объекта, в кнопке отображается его строковое представление.

Содержимое кнопки обычно представляет собой текст. При создании этого текста следуйте таким рекомендациям:

-  Используйте краткий, конкретный, не требующий разъяснений текст, который четко описывает действие, выполняемое кнопкой. Обычно текст надписи на кнопке состоит из одного слова, как правило, глагола.

-  Используйте шрифт, заданный по умолчанию, если не требуется использовать другой.

-  При размещении короткого текста избегайте использования узких кнопок. Минимальная ширина кнопки должна составлять 120 пикселей.

- При размещении длинного текста избегайте использования широких кнопок. Длина текста должна составлять не более 26 символов.

-  Если текст надписи на кнопке динамический ([локализуемый](../globalizing/globalizing-portal.md)), продумайте, каким образом можно изменять размер кнопки и как это отразится на элементах управления рядом с ней.

<table>
<tr>
<td> <b>Необходимо исправить.</b><br> Кнопки с переполняющим текстом. </td>
<td> <img src="images/button-wraptext.png"/> </td>
</tr>
<tr>
<td> <b>Вариант 1.</b><br> Увеличьте ширину кнопки, разместите кнопки друг под другом и перенесите текст, если его длина превышает 26 символов. </td>
<td> <img src="images/button-wraptext1.png"> </td>
</tr>
<tr>
<td> <b>Вариант 2.</b><br> Увеличьте высоту кнопки и перенесите текст. </td>
<td> <img src="images/button-wraptext2.png"> </td>
</tr>
</table>

Можно также настроить визуальные элементы, составляющие внешний вид кнопки. Например, можно заменить текст значком или использовать значок вместе с текстом.

В данном случае класс **StackPanel**, содержащий изображение и текст, задан в качестве содержимого кнопки.

```xaml
<Button Click="Button_Click"
        Background="LightGray"
        Height="100" Width="80">
    <StackPanel>
        <Image Source="Assets/Photo.png" Height="62"/>
        <TextBlock Text="Photos" Foreground="Black"
                   HorizontalAlignment="Center"/>
    </StackPanel>
</Button>
```

Кнопка выглядит следующим образом.

![Кнопка с содержимым в виде изображения и текста](images/button-orange.png)

## <a name="create-a-repeat-button"></a>Создание кнопки повтора

Элемент управления [RepeatButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.repeatbutton) — это кнопка, у которой события [Click](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) повторяются до тех пор, пока она нажата. Задайте свойство [Delay](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.repeatbutton.delay), чтобы указать время задержки для элемента управления **RepeatButton**, по прошествии которого начнется повторение действия щелчка. Задайте свойство [Interval](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.repeatbutton.interval), чтобы указать время между повторениями действия щелчка. Время для обоих свойств указывается в миллисекундах.

В следующем примере показаны два элемента управления **RepeatButton**, у которых события **Click** увеличивают и уменьшают значение в блоке текста.

```xaml
<StackPanel>
    <RepeatButton Width="100" Delay="500" Interval="100" Click="Increase_Click">Increase</RepeatButton>
    <RepeatButton Width="100" Delay="500" Interval="100" Click="Decrease_Click">Decrease</RepeatButton>
    <TextBlock x:Name="clickTextBlock" Text="Number of Clicks:" />
</StackPanel>
```

```csharp
private static int _clicks = 0;
private void Increase_Click(object sender, RoutedEventArgs e)
{
    _clicks += 1;
    clickTextBlock.Text = "Number of Clicks: " + _clicks;
}

private void Decrease_Click(object sender, RoutedEventArgs e)
{
    if(_clicks > 0)
    {
        _clicks -= 1;
        clickTextBlock.Text = "Number of Clicks: " + _clicks;
    }
}
```

## <a name="create-a-drop-down-button"></a>Создание раскрывающейся кнопки

> Для создания раскрывающейся кнопки **DropDownButton** требуется [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) или Windows 10 версии 1809 (пакет SDK 17763) или последующей. Последнюю версию пакета SDK можно скачать [здесь](https://developer.microsoft.com/windows/downloads/windows-10-sdk), а его предыдущие версии — [здесь](https://developer.microsoft.com/windows/downloads/sdk-archive).

[DropDownButton](/uwp/api/windows.ui.xaml.controls.dropdownbutton) — это кнопка, использующая шеврон в качестве визуального индикатора прикрепленного всплывающего меню, которое содержит дополнительные варианты. Она действует как и стандартный элемент управления**Button** со всплывающим меню, но ее внешний вид отличается.

Раскрывающаяся кнопка наследует событие **Click**, но обычно оно не используется. Вместо этого используется свойство **Flyout** для присоединения всплывающего меню и вызова действий с помощью команд в этом меню. Всплывающее меню открывается автоматически при нажатии кнопки.
Не забудьте задать для всплывающего меню свойство [Placement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.placement), чтобы разместить меню по отношению к кнопке нужным образом. Алгоритм размещения по умолчанию может не обеспечить предполагаемое размещение во всех ситуациях.

> [!TIP]
> Дополнительные сведения о всплывающих меню см. в статье [Меню и контекстные меню](menus.md).

### <a name="example---drop-down-button"></a>Пример. Раскрывающаяся кнопка

В приведенном здесь примере показано, как создать раскрывающуюся кнопку со всплывающим меню, которое содержит команды для выравнивания абзаца в элементе управления **RichEditBox**. (Дополнительные сведения и код см. в статье [Блок форматируемого текста](rich-edit-box.md).)

![Раскрывающаяся кнопка с командами выравнивания](images/drop-down-button-align.png)

```xaml
<DropDownButton ToolTipService.ToolTip="Alignment">
    <TextBlock FontFamily="Segoe MDL2 Assets" FontSize="14" Text="&#xE8E4;"/>
    <DropDownButton.Flyout>
        <MenuFlyout Placement="BottomEdgeAlignedLeft">
            <MenuFlyoutItem Text="Left" Icon="AlignLeft" Tag="left"
                            Click="AlignmentMenuFlyoutItem_Click"/>
            <MenuFlyoutItem Text="Center" Icon="AlignCenter" Tag="center"
                            Click="AlignmentMenuFlyoutItem_Click"/>
            <MenuFlyoutItem Text="Right" Icon="AlignRight" Tag="right"
                            Click="AlignmentMenuFlyoutItem_Click"/>
        </MenuFlyout>
    </DropDownButton.Flyout>
</DropDownButton>
```

```csharp
private void AlignmentMenuFlyoutItem_Click(object sender, RoutedEventArgs e)
{
    var option = ((MenuFlyoutItem)sender).Tag.ToString();

    Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
    if (selectedText != null)
    {
        // Apply the alignment to the selected paragraphs.
        var paragraphFormatting = selectedText.ParagraphFormat;
        if (option == "left")
        {
            paragraphFormatting.Alignment = Windows.UI.Text.ParagraphAlignment.Left;
        }
        else if (option == "center")
        {
            paragraphFormatting.Alignment = Windows.UI.Text.ParagraphAlignment.Center;
        }
        else if (option == "right")
        {
            paragraphFormatting.Alignment = Windows.UI.Text.ParagraphAlignment.Right;
        }
    }
}
```

## <a name="create-a-split-button"></a>Создание разворачивающейся кнопки

 > [!IMPORTANT]
 > Для создания кнопки **SplitButton** требуется [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) или Windows 10, версия 1809 (пакет SDK 17763) или выше. Последнюю версию пакета SDK можно скачать [здесь](https://developer.microsoft.com/windows/downloads/windows-10-sdk), а его предыдущие версии — [здесь](https://developer.microsoft.com/windows/downloads/sdk-archive).

Элемент управления [SplitButton](/uwp/api/windows.ui.xaml.controls.splitbutton) состоит из двух частей, которые можно вызывать по отдельности. Одна часть представляет собой стандартную кнопку, которая вызывает немедленное действие. Другая часть позволяет вызывать всплывающий элемент с дополнительными параметрами, которые пользователь может выбрать.

> [!NOTE]
> При вызове действия с помощью касания разворачивающаяся кнопка выполняет функцию раскрывающейся кнопки. Обе стороны кнопки вызывают всплывающее меню. При использовании других методов ввода пользователь может вызвать каждый элемент кнопки отдельно.

Стандартное поведение разворачивающейся кнопки:

- При нажатии одной из сторон кнопки обрабатывается событие **Click** для инициирования варианта, выбранного в раскрывающемся меню.

- При открытии раскрывающегося списка должен обрабатываться вызов элементов в нем для изменения выбранного варианта, а затем его вызова. Вызов элемента во всплывающем меню играет важную роль, так как событие **Click** не будет запускаться при касании.

> [!TIP]
> Существует много способов расположения элементов в раскрывающемся списке и обработки их вызова. Если вы используете **ListView** или **GridView**, можно обработать событие **SelectionChanged**. После этого задайте для параметра [SingleSelectionFollowsFocus](/uwp/api/windows.ui.xaml.controls.listviewbase.singleselectionfollowsfocus) значение **false**. Эта настройка позволит пользователям переходить по вариантам с помощью клавиатуры без вызова варианта при каждом внесении изменений.

### <a name="example---split-button"></a>Пример. Разворачивающаяся кнопка

В приведенном здесь примере показано, как создать разворачивающуюся кнопку для изменения цвета переднего плана выбранного текста в элементе управления **RichEditBox**. (Дополнительные сведения и код см. в статье [Блок форматируемого текста](rich-edit-box.md).)
Всплывающее меню разворачивающейся кнопки по умолчанию использует для свойства [Placement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.placement) значение [BottomEdgeAlignedLeft](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutplacementmode). Вы не можете переопределить это значение.

![Разворачивающаяся кнопка для выбора цвета переднего плана](images/split-button-rtb.png)

```xaml
<SplitButton ToolTipService.ToolTip="Foreground color"
             Click="BrushButtonClick">
    <Border x:Name="SelectedColorBorder" Width="20" Height="20"/>
    <SplitButton.Flyout>
        <Flyout x:Name="BrushFlyout">
            <!-- Set SingleSelectionFollowsFocus="False"
                 so that keyboard navigation works correctly. -->
            <GridView ItemsSource="{x:Bind ColorOptions}"
                      SelectionChanged="BrushSelectionChanged"
                      SingleSelectionFollowsFocus="False"
                      SelectedIndex="0" Padding="0">
                <GridView.ItemTemplate>
                    <DataTemplate>
                        <Rectangle Fill="{Binding}" Width="20" Height="20"/>
                    </DataTemplate>
                </GridView.ItemTemplate>
                <GridView.ItemContainerStyle>
                    <Style TargetType="GridViewItem">
                        <Setter Property="Margin" Value="2"/>
                        <Setter Property="MinWidth" Value="0"/>
                        <Setter Property="MinHeight" Value="0"/>
                    </Style>
                </GridView.ItemContainerStyle>
            </GridView>
        </Flyout>
    </SplitButton.Flyout>
</SplitButton>
```

```csharp
public sealed partial class MainPage : Page
{
    // Color options that are bound to the grid in the split button flyout.
    private List<SolidColorBrush> ColorOptions = new List<SolidColorBrush>();
    private SolidColorBrush CurrentColorBrush = null;

    public MainPage()
    {
        this.InitializeComponent();

        // Add color brushes to the collection.
        ColorOptions.Add(new SolidColorBrush(Colors.Black));
        ColorOptions.Add(new SolidColorBrush(Colors.Red));
        ColorOptions.Add(new SolidColorBrush(Colors.Orange));
        ColorOptions.Add(new SolidColorBrush(Colors.Yellow));
        ColorOptions.Add(new SolidColorBrush(Colors.Green));
        ColorOptions.Add(new SolidColorBrush(Colors.Blue));
        ColorOptions.Add(new SolidColorBrush(Colors.Indigo));
        ColorOptions.Add(new SolidColorBrush(Colors.Violet));
        ColorOptions.Add(new SolidColorBrush(Colors.White));
    }

    private void BrushButtonClick(object sender, object e)
    {
        // When the button part of the split button is clicked,
        // apply the selected color.
        ChangeColor();
    }

    private void BrushSelectionChanged(object sender, SelectionChangedEventArgs e)
    {
        // When the flyout part of the split button is opened and the user selects
        // an option, set their choice as the current color, apply it, then close the flyout.
        CurrentColorBrush = (SolidColorBrush)e.AddedItems[0];
        SelectedColorBorder.Background = CurrentColorBrush;
        ChangeColor();
        BrushFlyout.Hide();
    }

    private void ChangeColor()
    {
        // Apply the color to the selected text in a RichEditBox.
        Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
        if (selectedText != null)
        {
            Windows.UI.Text.ITextCharacterFormat charFormatting = selectedText.CharacterFormat;
            charFormatting.ForegroundColor = CurrentColorBrush.Color;
            selectedText.CharacterFormat = charFormatting;
        }
    }
}
```

## <a name="create-a-toggle-split-button"></a>Создание разворачивающейся кнопки с переключателем

> [!NOTE]
> Для создания разворачивающейся кнопки с переключателем **SplitButton** требуется [библиотека пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/) или Windows 10, версия 1809 (пакет SDK 17763) или выше. Последнюю версию пакета SDK можно скачать [здесь](https://developer.microsoft.com/windows/downloads/windows-10-sdk), а его предыдущие версии — [здесь](https://developer.microsoft.com/windows/downloads/sdk-archive).

Элемент управления [ToggleSplitButton](/uwp/api/windows.ui.xaml.controls.togglesplitbutton) состоит из двух частей, которые можно вызывать по отдельности. Одна часть выполняет функцию переключателя. Другая часть позволяет вызывать всплывающий элемент с дополнительными параметрами, которые пользователь может выбрать.

Разворачивающаяся кнопка с переключателем обычно используется для активации или деактивации компонента, если компонент содержит множество возможностей, из которых пользователь может выбирать. Например, в редакторе документов она может использоваться для включения или выключения списков, а раскрывающийся список — для выбора стиля списка.

> [!NOTE]
> При вызове с помощью касания разворачивающаяся кнопка с переключателем работает аналогично раскрывающейся кнопке. При использовании других способов ввода пользователь может переключать и вызывать два элемента кнопки по отдельности. При касании оба элемента кнопки вызывают всплывающее меню. Следовательно, необходимо добавить в содержимое всплывающего меню вариант для включения и выключения кнопки.


### <a name="differences-with-togglebutton"></a>Отличия от кнопки переключения

В отличие от [ToggleButton](/uwp/api/windows.ui.xaml.controls.primitives.togglebutton) у **ToggleSplitButton** нет неопределенного состояния. По этой причине вы должны учитывать следующие различия:

- У **ToggleSplitButton** нет свойства **IsThreeState** или события **Indeterminate**.
- Свойство [ToggleSplitButton.IsChecked](/uwp/api/windows.ui.xaml.controls.togglesplitbutton.ischecked) является логическим значением и не **допускает значение NULL<bool>** .
- **ToggleSplitButton** использует только событие [IsCheckedChanged](/uwp/api/windows.ui.xaml.controls.togglesplitbutton.ischeckedchanged) и не поддерживает отдельные события **Checked** и **Unchecked**.


### <a name="example---toggle-split-button"></a>Пример. Разворачивающаяся кнопка с переключателем

В следующем примере показано, как разворачивающаяся кнопка с переключателем может использоваться для включения или выключения форматирования списка, а также изменения стиля списка в элементе управления **RichEditBox**. (Дополнительные сведения и код см. в статье [Блок форматируемого текста](rich-edit-box.md).)
Всплывающее меню разворачивающейся кнопки с переключателем использует по умолчанию [BottomEdgeAlignedLeft](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutplacementmode) для свойства [Placement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.placement). Вы не можете переопределить это значение.

![Разворачивающаяся кнопка с переключателем для выбора стиля списка](images/toggle-split-button-open.png)

```xaml
<ToggleSplitButton x:Name="ListButton"
                   ToolTipService.ToolTip="List style"
                   Click="ListButton_Click"
                   IsCheckedChanged="ListStyleButton_IsCheckedChanged">
    <TextBlock FontFamily="Segoe MDL2 Assets" FontSize="14" Text="&#xE8FD;"/>
    <ToggleSplitButton.Flyout>
        <Flyout>
            <ListView x:Name="ListStylesListView"
                      SelectionChanged="ListStylesListView_SelectionChanged"
                      SingleSelectionFollowsFocus="False">
                <StackPanel Tag="bullet" Orientation="Horizontal">
                    <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xE7C8;"/>
                    <TextBlock Text="Bullet" Margin="8,0"/>
                </StackPanel>
                <StackPanel Tag="alpha" Orientation="Horizontal">
                    <TextBlock Text="A" FontSize="24" Margin="2,0"/>
                    <TextBlock Text="Alpha" Margin="8"/>
                </StackPanel>
                <StackPanel Tag="numeric" Orientation="Horizontal">
                    <FontIcon FontFamily="Segoe MDL2 Assets" Glyph="&#xF146;"/>
                    <TextBlock Text="Numeric" Margin="8,0"/>
                </StackPanel>
                <TextBlock Tag="none" Text="None" Margin="28,0"/>
            </ListView>
        </Flyout>
    </ToggleSplitButton.Flyout>
</ToggleSplitButton>
```

```csharp
private void ListStyleButton_IsCheckedChanged(ToggleSplitButton sender, ToggleSplitButtonIsCheckedChangedEventArgs args)
{
    // Use the toggle button to turn the selected list style on or off.
    if (((ToggleSplitButton)sender).IsChecked == true)
    {
        // On. Apply the list style selected in the drop down to the selected text.
        var listStyle = ((FrameworkElement)(ListStylesListView.SelectedItem)).Tag.ToString();
        ApplyListStyle(listStyle);
    }
    else
    {
        // Off. Make the selected text not a list,
        // but don't change the list style selected in the drop down.
        ApplyListStyle("none");
    }
}

private void ListStylesListView_SelectionChanged(object sender, SelectionChangedEventArgs e)
{
    var listStyle = ((FrameworkElement)(e.AddedItems[0])).Tag.ToString();

    if (ListButton.IsChecked == true)
    {
        // Toggle button is on. Turn it off...
        if (listStyle == "none")
        {
            ListButton.IsChecked = false;
        }
        else
        {
            // or apply the new selection.
            ApplyListStyle(listStyle);
        }
    }
    else
    {
        // Toggle button is off. Turn it on, which will apply the selection
        // in the IsCheckedChanged event handler.
        ListButton.IsChecked = true;
    }
}

private void ApplyListStyle(string listStyle)
{
    Windows.UI.Text.ITextSelection selectedText = editor.Document.Selection;
    if (selectedText != null)
    {
        // Apply the list style to the selected text.
        var paragraphFormatting = selectedText.ParagraphFormat;
        if (listStyle == "none")
        {  
            paragraphFormatting.ListType = Windows.UI.Text.MarkerType.None;
        }
        else if (listStyle == "bullet")
        {
            paragraphFormatting.ListType = Windows.UI.Text.MarkerType.Bullet;
        }
        else if (listStyle == "numeric")
        {
            paragraphFormatting.ListType = Windows.UI.Text.MarkerType.Arabic;
        }
        else if (listStyle == "alpha")
        {
            paragraphFormatting.ListType = Windows.UI.Text.MarkerType.UppercaseEnglishLetter;
        }
        selectedText.ParagraphFormat = paragraphFormatting;
    }
}
```

## <a name="recommendations"></a>Рекомендации

- Сделайте так, чтобы назначение и состояние кнопки было понятно пользователю.

- Если для одного решения предусмотрено несколько кнопок (например, в диалоговом окне подтверждения), расположите эти кнопки в таком порядке, где [Выполнить] и [Не выполнять] будут конкретными вариантами ответа на основную команду:
  - ОК/[Выполнить]/Да
    - [Не выполнять]/Нет
    - Отмена

- Сделайте одновременно доступными пользователю только одну или две кнопки, например **Принять** и **Отмена**. Если требуется предоставить пользователю больше действий, используйте в интерфейсе [флажки](checkbox.md) или [переключатели](radio-button.md), с помощью которых пользователь сможет выбрать нужные действия, а затем путем нажатия одной кнопки начать выполнение всех этих действий.

- Для действия, которое требуется выполнить над множеством страниц вашего приложения, лучше использовать [нижнюю панель приложения](app-bars.md), а не повторять соответствующую кнопку на каждой странице.


### <a name="recommended-single-button-layout"></a>Рекомендуемый макет с одной кнопкой

Если в макете требуется только одна кнопка, она должна быть выровнена либо по левому, либо по правому краю на основе контекста ее контейнера.

  - В диалоговых окнах с одной кнопкой требуется выравнивание кнопки **по правому краю**. Если диалоговое окно содержит только одну кнопку, убедитесь, что она выполняет безопасное, обратимое действие. Если вы используете [ContentDialog](dialogs.md) и укажете одну кнопку, она будет автоматически выровнена по правому краю.

    ![Кнопка в диалоговом окне](images/pushbutton_doc_dialog.png)

  - Если кнопка отображается внутри контейнера пользовательского интерфейса (например, во всплывающем уведомлении, всплывающем элементе меню или элементе представления списка), необходимо выровнять кнопку в контейнере **по правому краю**.

    ![Кнопка внутри контейнера](images/pushbutton_doc_container.png)

  - На страницах, содержащих одну кнопку (например, кнопку **Применить** в нижней части страницы параметров), необходимо выровнять кнопку **по левому краю**. Это обеспечит соответствие кнопки остальной части содержимого страницы.

    ![Кнопка на странице](images/pushbutton_doc_page.png)


## <a name="back-buttons"></a>Кнопки "Назад"

Кнопка "Назад" — это предоставленный системой элемент пользовательского интерфейса для обратной навигации через стек или журнал навигации пользователя. Вы не обязаны создавать собственную кнопку "Назад", но вы можете выполнить определенные действия, чтобы реализовать хорошие возможности обратной навигации. Дополнительные сведения см. в руководству по [журналу навигации и навигации в обратном направлении для приложений UWP](../basics/navigation-history-and-backwards-navigation.md).


## <a name="get-the-sample-code"></a>Получение примера кода

- [XAML Controls Gallery](https://github.com/Microsoft/Xaml-Controls-Gallery). Пример, демонстрирующий работу всех элементов управления XAML в интерактивном формате.


## <a name="related-articles"></a>Похожие статьи

- [Класс Button](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.button)
- [Переключатели](radio-button.md)
- [Флажки](checkbox.md)
- [Тумблеры](toggles.md)
