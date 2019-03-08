---
Description: Поле текстового ввода, где отображается предлагаемый текст по мере ввода пользователем.
title: Поле со списком (раскрывающегося списка)
label: Combo box
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: ''
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 21a6c698fa0e07587e2c25ae827dc6654a8ced9d
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57618629"
---
# <a name="combo-box"></a>поле со списком;

Используйте поле со списком (стрелку раскрывающегося списка) для представления элементов, которые пользователь может выбрать из списка. Поле со списком начинается в состоянии compact и раскрывается, отображая список выбираемых элементов.

При закрытии поле со списком, он отображает текущее выделение или является пустым, если выбранный элемент отсутствует. Когда пользователь разворачивает поле со списком, он отображает список выбираемых элементов.

> **Важные API-интерфейсы**: [ComboBox-класс](/uwp/api/Windows.UI.Xaml.Controls.ComboBox), [свойство IsEditable](/uwp/api/windows.ui.xaml.controls.combobox.iseditable), [свойство Text](/uwp/api/Windows.UI.Xaml.Controls.ComboBox), [TextSubmitted событий](/uwp/api/Windows.UI.Xaml.Controls.ComboBox)

Поле со списком в состоянии compact с заголовком.

![Пример раскрывающегося списка в компактном состоянии](images/combo_box_collapsed.png)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

- Используйте раскрывающийся список, чтобы дать пользователям возможность выбрать одно значение из набора элементов, которые можно надлежащим образом представить с помощью отдельных строк текста.
- Используйте представление списка или сетки вместо поля со списком для отображения элементов, состоящих из нескольких строк текста или изображений.
- Если элементов меньше пяти, возможно, лучше воспользоваться [переключателями](radio-button.md) (если можно выбрать только один элемент) или [флажками](checkbox.md) (если можно выбрать несколько элементов).
- Используйте поле со списком, если элементы выбора не имеют большого значения для работы приложения. Если для большинства пользователей в большей части ситуаций рекомендуется использовать вариант по умолчанию, отображение всех элементов в списке может привлечь излишнее внимание к вариантам. Поле со списком позволяет экономить пространство и не отвлекать пользователя.

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас есть <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong> приложения. Щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ComboBox">откройте приложение и поле со списком в действии см. в разделе</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получение коллекции элементов управления XAML приложения (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Поле со списком в компактном состоянии может отображать заголовок.

![Пример раскрывающегося списка в компактном состоянии](images/combo_box_collapsed.png)

Хотя поля со списком разворачиваются для поддержки более длинных строк, избегайте слишком длинных строк, так как их сложно читать.

![Пример раскрывающегося списка с длинной текстовой строкой](images/combo_box_listitemstate.png)

Если коллекция в поле со списком достаточно длинная, появляется полоса прокрутки. Логически группируйте элементы в списке.

![Пример полосы прокрутки в раскрывающемся списке](images/combo_box_scroll.png)

## <a name="create-a-combo-box"></a>Создать поле со списком

Заполнить поле со списком, добавив объекты непосредственно к [элементы](/uwp/api/windows.ui.xaml.controls.itemscontrol.items) коллекции или путем привязки [ItemsSource](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) свойства к источнику данных. Элементы, добавленные к элементу управления ComboBox, упаковываются в [ComboBoxItem](/uwp/api/windows.ui.xaml.controls.comboboxitem) контейнеры.

Ниже приведен простой списком с элементы, добавленные в XAML.

```xaml
<ComboBox Header="Colors" PlaceholderText="Pick a color" Width="200">
    <x:String>Blue</x:String>
    <x:String>Green</x:String>
    <x:String>Red</x:String>
    <x:String>Yellow</x:String>
</ComboBox>
```

В следующем примере выполняется привязка к коллекции объектов FontFamily поле со списком.

```xaml
<ComboBox x:Name="FontsCombo" Header="Fonts" Height="44" Width="296"
          ItemsSource="{x:Bind fonts}" DisplayMemberPath="Source"/>
```

```csharp
ObservableCollection<FontFamily> fonts = new ObservableCollection<FontFamily>();

public MainPage()
{
    this.InitializeComponent();
    fonts.Add(new FontFamily("Arial"));
    fonts.Add(new FontFamily("Courier New"));
    fonts.Add(new FontFamily("Times New Roman"));
}
```

### <a name="item-selection"></a>Выбор элементов

Подобно ListView и GridView, поля со списком является производным от [селектор](/uwp/api/windows.ui.xaml.controls.primitives.selector), поэтому можно получить так же стандартный выбора пользователя.

Можно получить или задать поле со списком выбранный элемент с помощью [SelectedItem](/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem) свойство и get или set индекс выбранного элемента с помощью [SelectedIndex](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex) свойство.

Чтобы получить значение конкретного свойства для выбранного элемента данных, можно использовать [SelectedValue](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedvalue) свойство. В этом случае значение [SelectedValuePath](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedvaluepath) чтобы указывать, какие свойства выбранного элемента, чтобы получить значение из.

> [!TIP]
> Если задан SelectedItem или SelectedIndex, чтобы указать значение по умолчанию, возникает исключение, если свойство имеет значение, прежде чем заполняется поле со списком элементов коллекции. Если вы определяете элементы в XAML, лучше всего обработать событие Loaded поле со списком и установить SelectedItem или SelectedIndex в обработчике события Loaded.

Можно привязать к этим свойствам в XAML, или обрабатывать [SelectionChanged](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) событий реагировать на изменения выделения.

В событии код обработчика, можно получить выбранный элемент из [SelectionChangedEventArgs.AddedItems](/uwp/api/windows.ui.xaml.controls.selectionchangedeventargs.addeditems) свойство. Можно получить ранее выбранного элемента (если таковые имеются) [SelectionChangedEventArgs.RemovedItems](/uwp/api/windows.ui.xaml.controls.selectionchangedeventargs.removeditems) свойство. Каждый из коллекции AddedItems и RemovedItems содержат только один элемент, так как поле со списком не поддерживает выбор нескольких элементов.

В этом примере показано, как обрабатывать событие SelectionChanged, а также как выполнить привязку к выбранному элементу.

```xaml
<StackPanel>
    <ComboBox x:Name="colorComboBox" Width="200"
              Header="Colors" PlaceholderText="Pick a color"
              SelectionChanged="ColorComboBox_SelectionChanged">
        <x:String>Blue</x:String>
        <x:String>Green</x:String>
        <x:String>Red</x:String>
        <x:String>Yellow</x:String>
    </ComboBox>

    <Rectangle x:Name="colorRectangle" Height="30" Width="100"
               Margin="0,8,0,0" HorizontalAlignment="Left"/>

    <TextBlock Text="{x:Bind colorComboBox.SelectedIndex, Mode=OneWay}"/>
    <TextBlock Text="{x:Bind colorComboBox.SelectedItem, Mode=OneWay}"/>
</StackPanel>
```

```csharp
private void ColorComboBox_SelectionChanged(object sender, SelectionChangedEventArgs e)
{
    // Add "using Windows.UI;" for Color and Colors.
    string colorName = e.AddedItems[0].ToString();
    Color color;
    switch (colorName)
    {
        case "Yellow":
            color = Colors.Yellow;
            break;
        case "Green":
            color = Colors.Green;
            break;
        case "Blue":
            color = Colors.Blue;
            break;
        case "Red":
            color = Colors.Red;
            break;
    }
    colorRectangle.Fill = new SolidColorBrush(color);
}
```

#### <a name="selectionchanged-and-keyboard-navigation"></a>Навигации SelectionChanged и клавиатуры

По умолчанию это событие SelectionChanged возникает, когда пользователь нажимает кнопку, касается или клавиши ВВОД на элемент в списке, чтобы зафиксировать их выделение, и закрывает поле со списком. Выбор не изменяется при переходе пользователя открыть поле со списком с помощью клавиши со стрелками.

Чтобы сделать поле со списком поле «live updates», пока пользователь перемещается Открытие списка с помощью клавиш со стрелками (например, шрифт выбора раскрывающегося списка), установите [SelectionChangedTrigger](/uwp/api/windows.ui.xaml.controls.combobox.selectionchangedtrigger) для [всегда](/uwp/api/windows.ui.xaml.controls.comboboxselectionchangedtrigger). В результате событие SelectionChanged должно происходить при изменении фокуса в другой элемент в списке open.

#### <a name="selected-item-behavior-change"></a>Изменение поведения выбранного элемента

В Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии, поведение выбранных элементов обновляется для поддержки редактирования со списком.

До пакета SDK 17763, значение свойства SelectedItem (и, следовательно, SelectedValue и SelectedIndex) должно было быть в поле со списком элементов коллекции. В предыдущем примере, установка `colorComboBox.SelectedItem = "Pink"` приводит к:

- SelectedItem = null
- SelectedValue = null
- SelectedIndex = -1

В пакете SDK 17763 и более поздние версии, значение свойства SelectedItem (и, следовательно, SelectedValue и SelectedIndex) не обязательно должна быть в поле со списком элементов коллекции. В предыдущем примере, установка `colorComboBox.SelectedItem = "Pink"` приводит к:

- SelectedItem = розовый
- SelectedValue = розовый
- SelectedIndex = -1

### <a name="text-search"></a>Поиск по тексту

Поля со списком автоматически поддерживают поиск в их коллекциях. По мере того, как пользователь вводит символы на физической клавиатуре, выбрав открытое или закрытое поле со списком, отображаются варианты, подходящие под строку пользователя. Эта функция особенно полезна при навигации по длинному списку. Например при взаимодействии с раскрывающимся списком содержащий список состояний пользователей можно нажать клавишу «w» для отображения в представлении «Вашингтон», для быстрого выбора. Текст поиска не учитывается.

Можно задать [IsTextSearchEnabled](/uwp/api/windows.ui.xaml.controls.combobox.istextsearchenabled) свойства **false** Чтобы отключить эту функцию.

## <a name="make-a-combo-box-editable"></a>Редактировать поле со списком

> [!IMPORTANT]
> Для этой функции требуется Windows 10, версия 1809 ([SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздней версии.

По умолчанию в поле со списком позволяет пользователю выбрать из списка предварительно определенных параметров. Однако бывают случаи, где список содержит только подмножество допустимые значения, и пользователь должен иметь возможность ввести другие значения, которые не указаны. Для этого в поле со списком можно сделать доступным для редактирования.

Чтобы редактировать поле со списком, задайте [IsEditable](/uwp/api/windows.ui.xaml.controls.combobox.iseditable) свойства **true**. Затем обрабатывать [TextSubmitted](/uwp/api/Windows.UI.Xaml.Controls.ComboBox) работы со значением, введенным пользователем события.

По умолчанию значение SelectedItem обновляется, когда пользователь фиксирует пользовательским текстом. Это поведение можно переопределить, задав **обработанных** для **true** в TextSubmitted аргументы события. Когда событие помечено как обработанное, поле со списком будет предпринимать никаких дополнительных действий после события и будет оставаться в состоянии редактирования. SelectedItem не обновляется.

Этот пример простого редактирования списком. Список содержит простые строки, и любое значение, введенное пользователем используется как введено.

Элемента выбора «недавно использовавшиеся имена» позволяет пользователю ввести пользовательские строки. В списке «RecentlyUsedNames» содержит некоторые значения, которые пользователь может выбирать, но пользователь также может добавлять пользовательские значения. Свойство «CurrentName» представляет момент введенное имя.

```xaml
<ComboBox IsEditable="true"
          ItemsSource="{x:Bind RecentlyUsedNames}"
          SelectedItem="{x:Bind CurrentName, Mode=TwoWay}"/>
```

### <a name="text-submitted"></a>Тексту

Можно обрабатывать [TextSubmitted](/uwp/api/Windows.UI.Xaml.Controls.ComboBox) работы со значением, введенным пользователем события. В случае обработчик, вы обычно проверит допустимость значения, введенные пользователем, затем используйте значение в приложении. В зависимости от ситуации можно также добавить значение в поле со списком список параметров для использования в будущем.

Это событие TextSubmitted возникает при выполнении этих условий:

- Свойство IsEditable **true**
- Пользователь вводит текст, который не соответствует существующей записи в поле со списком
- Пользователь нажимает клавишу ВВОД или убирает фокус с поля со списком.

TextSubmitted событие не происходит, если пользователь вводит текст и затем выполняет переход вверх или вниз по списку.

### <a name="sample---validate-input-and-use-locally"></a>Пример — проверки входных данных и использовать локально

В этом примере элемента выбора размера шрифта содержит набор значений, соответствующих увеличения размера шрифта, но пользователь может ввести размеры шрифтов, которых нет в списке.

Когда пользователь добавляет значение, которое не находится в списке, обновления размер шрифта, но значение не добавляется в список размеров шрифтов.

Если только что введенное значение не является допустимым, использовать выбранное значение SelectedValue для вернуться к последним свойства Text известное хорошее значение.

```xaml
<ComboBox x:Name="fontSizeComboBox"
          IsEditable="true"
          ItemsSource="{x:Bind ListOfFontSizes}"
          TextSubmitted="FontSizeComboBox_TextSubmitted"/>
```

```csharp
private void FontSizeComboBox_TextSubmitted(ComboBox sender, ComboBoxTextSubmittedEventArgs e)
{
    if (byte.TryParse(e.Text, out double newValue))
    {
        // Update the app’s font size.
        _fontSize = newValue;
    }
    else
    {
        // If the item is invalid, reject it and revert the text.
        // Mark the event as handled so the framework doesn’t update the selected item.
        sender.Text = sender.SelectedValue.ToString();
        e.Handled = true;
    }
}
```

### <a name="sample---validate-input-and-add-to-list"></a>Пример — проверки входных данных и добавить в список

Здесь «выбор любимого цвета» содержит наиболее часто применяемые цвета избранные (красный, синий, зеленый, оранжевым цветом), но пользователь может ввести любимого цвета, который не находится в списке. Когда пользователь добавляет является допустимым цветом (например, розовый цвет), вновь введенного цвет добавляется в список и установить как активный «любимого цвета».

```xaml
<ComboBox x:Name="favoriteColorComboBox"
          IsEditable="true"
          ItemsSource="{x:Bind ListOfColors}"
          TextSubmitted="FavoriteColorComboBox_TextSubmitted"/>
```

```csharp
private void FavoriteColorComboBox_TextSubmitted(ComboBox sender, ComboBoxTextSubmittedEventArgs e)
{
    if (IsValid(e.Text))
    {
        FavoriteColor newColor = new FavoriteColor()
        {
            ColorName = e.Text,
            Color = ColorFromStringConverter(e.Text)
        }
        ListOfColors.Add(newColor);
    }
    else
    {
        // If the item is invalid, reject it but do not revert the text.
        // Mark the event as handled so the framework doesn’t update the selected item.
        e.Handled = true;
    }
}

bool IsValid(string Text)
{
    // Validate that the string is: not empty; a color.
}
```

## <a name="dos-and-donts"></a>Возможности и ограничения

- Ограничьте текстовое содержимое элементов полей со списком одной строкой.
- Сортируйте элементы в поле со списком в логическом порядке. Группируйте связанные параметры и размещайте часто используемые параметры в начале списка. Названия сортируйте в алфавитном порядке, числа — по возрастанию, даты — по хронологии.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример AutoSuggestBox](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlAutoSuggestBox)

## <a name="related-articles"></a>Связанные статьи

- [Текстовые элементы управления](text-controls.md)
- [Проверка орфографии](text-controls.md)
- [Поиск](search.md)
- [Класс TextBox](https://msdn.microsoft.com/library/windows/apps/br209683)
- [Класс Windows.UI.Xaml.Controls PasswordBox](https://msdn.microsoft.com/library/windows/apps/br227519)
- [Свойство String.Length](https://msdn.microsoft.com/library/system.string.length.aspx)
