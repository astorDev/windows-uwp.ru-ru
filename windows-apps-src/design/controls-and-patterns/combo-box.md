---
author: Jwmsft
Description: A text entry box that provides suggestions as the user types.
title: Поле со списком (раскрывающийся список)
label: Combo box
template: detail.hbs
ms.author: jimwalk
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: ''
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 518ce49ddb631e3e914a6c7662b4e74de247c29c
ms.sourcegitcommit: 310a4555fedd4246188a98b31f6c094abb33ec60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/19/2018
ms.locfileid: "5126721"
---
# <a name="combo-box"></a>Поле со списком

Используйте поле со списком (также известной как раскрывающегося списка) для представления списка элементов, которые пользователь может выбрать нужный элемент. Поле со списком начинается в компактном состоянии и разворачивается для отображения списка предлагаемых.

Когда закрывается в поле со списком, он отображает текущий выбор либо будет пустым, если выбранного элемента нет. При развертывании в поле со списком, отображается список доступных для выбора элементов.

> **Важные API -интерфейсы**: [класс ComboBox](/uwp/api/Windows.UI.Xaml.Controls.ComboBox), [IsEditable свойство](/uwp/api/windows.ui.xaml.controls.combobox.iseditable), [свойство Text](/uwp/api/Windows.UI.Xaml.Controls.ComboBox), [TextSubmitted событий](/uwp/api/Windows.UI.Xaml.Controls.ComboBox)

Поле со списком в компактном состоянии с заголовком.

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
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">Галереи элементов управления XAML</strong> , щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ComboBox">открыть приложение и увидеть поля со списком в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
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

## <a name="create-a-combo-box"></a>Создание поля со списком

В поле со списком заполнения, добавляя объекты непосредственно к коллекции [элементов](/uwp/api/windows.ui.xaml.controls.itemscontrol.items) или путем привязки свойства [ItemsSource](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) с источником данных. Элементы, добавленные в поле со списком упаковываются в [ComboBoxItem](/uwp/api/windows.ui.xaml.controls.comboboxitem) контейнеров.

Вот простой списком с элементами, добавленных в XAML.

```xaml
<ComboBox Header="Colors" PlaceholderText="Pick a color" Width="200">
    <x:String>Blue<x:String>
    <x:String>Green<x:String>
    <x:String>Red<x:String>
    <x:String>Yellow<x:String>
</ComboBox>
```

В следующем примере показано, привязка к коллекции объектов FontFamily поле со списком.

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

Как ListView и GridView ComboBox является производным от [выбора](/uwp/api/windows.ui.xaml.controls.primitives.selector), чтобы вы смогли Выбор пользователя так же standard.

Можно получить или установите в поле со списком выбранных элементов с помощью свойства [SelectedItem](/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem) и получить или указать индекс выбранного элемента с помощью свойства [SelectedIndex](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex) .

Чтобы получить значение конкретного свойства на выбранный элемент данных, можно использовать свойство [SelectedValue](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedvalue) . В этом случае задайте [SelectedValuePath](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedvaluepath) для задания свойства выбранного элемента, чтобы получить значение.

> [!TIP]
> Если задано SelectedItem или SelectedIndex, чтобы указать значение по умолчанию, если свойство имеет значение, прежде чем заполнено в коллекцию Items поле со списком, возникает исключение. Если вы определяете своих элементов в XAML, лучше обрабатывать события Loaded поле со списком и настроить SelectedItem или SelectedIndex в обработчике события Loaded.

Можно выполнить привязку к эти свойства в XAML или обработать событие [SelectionChanged](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) , чтобы реагировать на изменения.

В случае код обработчика, вы можете получить выбранный элемент из свойства [SelectionChangedEventArgs.AddedItems](/uwp/api/windows.ui.xaml.controls.selectionchangedeventargs.addeditems) . Ранее выбранный элемент (если применимо) можно получить из свойства [SelectionChangedEventArgs.RemovedItems](/uwp/api/windows.ui.xaml.controls.selectionchangedeventargs.removeditems) . Каждый из коллекции AddedItems и RemovedItems содержат только 1 элемента, так как поле со списком не поддерживает выбор нескольких элементов.

В этом примере показано, как обрабатывать событие SelectionChanged, а также как привязать к выбранному элементу.

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

#### <a name="selectionchanged-and-keyboard-navigation"></a>Навигация по SelectionChanged и клавиатуры

По умолчанию событие SelectionChanged происходит, когда пользователь нажимает кнопку, касается или нажимает клавишу ВВОД на элемент в списке, чтобы принять их выбор и закрывает в поле со списком. Выбор не меняется при переходе пользователя открыть со списком с помощью клавиш со стрелками.

Чтобы сделать поле с раскрытым списком, «» во время переход списка открыть с помощью клавиш со стрелками (например, шрифт выбора раскрывающийся список), задайте для [параметра SelectionChangedTrigger](/uwp/api/windows.ui.xaml.controls.combobox.selectionchangedtrigger) значение [всегда](/uwp/api/windows.ui.xaml.controls.comboboxselectionchangedtrigger). Это вызывает событие SelectionChanged возникает при изменении фокуса на другой элемент в списке открыть.

#### <a name="selected-item-behavior-change"></a>Изменение поведения выбранного элемента

В RS5 (Windows SDK версии 10.0.NNNNN.0 (Windows 10 версии YYMM), поведение выбранных элементов обновляется для поддержки редактируемого поля со списком.

До RS5, значение свойства SelectedItem (и, следовательно, SelectedValue и SelectedIndex) должно было быть в коллекции элементов в поле со списком. В предыдущем примере, установив `colorComboBox.SelectedItem = "Pink"` приводит к:

- SelectedItem = null
- SelectedValue = null
- SelectedIndex = -1

В RS5 и более поздних версиях значение свойства SelectedItem (и, следовательно, SelectedValue и SelectedIndex) не обязательно должны быть в коллекции элементов в поле со списком. В предыдущем примере, установив `colorComboBox.SelectedItem = "Pink"` приводит к:

- SelectedItem = розовый
- SelectedValue = розовый
- SelectedIndex = -1

### <a name="text-search"></a>Поиск по тексту

Поля со списком автоматически поддерживают поиск в их коллекциях. По мере того, как пользователь вводит символы на физической клавиатуре, выбрав открытое или закрытое поле со списком, отображаются варианты, подходящие под строку пользователя. Эта функция особенно полезна при навигации по длинному списку. Например при взаимодействии с раскрывающимся содержащим список штатов, пользователей можно нажать клавишу «w» чтобы отобразить «Washington» для быстрого выбора. Поиск текста не учитывается.

Свойства [IsTextSearchEnabled](/uwp/api/windows.ui.xaml.controls.combobox.istextsearchenabled) значение **false,** отключить эту функцию.

## <a name="make-a-combo-box-editable"></a>Сделайте поле со списком, доступных для редактирования

> [!IMPORTANT]
> Для этой функции требуется [последние сборки Windows 10 Insider Preview и пакет SDK](https://insider.windows.com/for-developers/).

По умолчанию поле со списком позволяет пользователю выбирать значение из предопределенных список параметров. Однако бывают случаи, где список содержит только подмножество допустимые значения, а пользователь должен иметь возможность ввести другие значения, которые не указаны. Для этого в поле со списком можно сделать доступным для редактирования.

Поле со списком редактируется, задайте свойство [IsEditable](/uwp/api/windows.ui.xaml.controls.combobox.iseditable) значение **true**. Затем обработайте событие [TextSubmitted](/uwp/api/Windows.UI.Xaml.Controls.ComboBox) для работы со значением, введенные пользователем.

По умолчанию значение SelectedItem обновляется, когда пользователь подтверждает пользовательский текст. Можно переопределить это поведение, задав **Handled** значение **true** в аргументов события TextSubmitted. Когда событие помечается как обработанное, в поле со списком будет дальнейших действий не после события и будет оставаться в состоянии редактирования. SelectedItem не будут обновляться.

В этом примере показан простой редактируемое поле со списком. Список содержит простые строки и любое значение, введенные пользователем используется как при вводе.

Элемента выбора «недавно использовавшихся имена» дает пользователю возможность ввести пользовательских строк. В списке «RecentlyUsedNames» содержит несколько значений, которые пользователь может выбрать из, но пользователь может также добавить новые, пользовательские значение. Свойство «CurrentName» представляет в настоящее время введенное имя.

```xaml
<ComboBox IsEditable="true"
          ItemsSource="{x:Bind RecentlyUsedNames}"
          SelectedItem="{x:Bind CurrentName, Mode=TwoWay}"/>
```

### <a name="text-submitted"></a>Текст отправки

Вы можете обработать событие [TextSubmitted](/uwp/api/Windows.UI.Xaml.Controls.ComboBox) для работы со значением, введенные пользователем. В случае обработчик, вы обычно проверит правильность введенных пользователем значение, затем используйте значение в вашем приложении. В зависимости от ситуации можно также добавить значение в поле со списком список параметров для использования в будущем.

Событие TextSubmitted возникает при выполнении следующих условий:

- Свойство IsEditable имеет **значение true**
- Пользователь вводит текст, который не соответствует существующей записи в раскрывающемся списке
- Пользователь нажимает клавишу ВВОД или перемещает фокус в поле со списком.

Событие TextSubmitted не происходит, если пользователь вводит текст, а затем переходит вверх или вниз по списку.

### <a name="sample---validate-input-and-use-locally"></a>Пример: проверка ввода и использовать локально

В этом examle выбора размера шрифта содержит набор значений, соответствующих шрифтов размер шрифта, но пользователь может ввести размеры шрифтов, которые не входят в списке.

Когда пользователь добавляет значение, которое не находится в списке, обновления размер шрифта, но значение не добавляется в список размеры шрифтов.

Если недавно введенное значение не является допустимым, используйте SelectedValue вернуться к последнему свойство Text известное хорошо значение.

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

### <a name="sample---validate-input-and-add-to-list"></a>Пример: проверка ввода и добавить в список

Здесь «выбор избранные цвета» содержит наиболее распространенные любимых цветов (красный, синий, зеленый, Orange), но пользователь может ввести любимый цвет, который не находится в списке. Когда пользователь добавляет является допустимым цветом (например, розовый), вновь введенного цвет добавлен в список и задать в качестве активного «favorite color».

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

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.
- [Пример AutoSuggestBox](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlAutoSuggestBox)

## <a name="related-articles"></a>Смежные разделы

- [Текстовые элементы управления](text-controls.md)
- [Проверка правописания](text-controls.md)
- [Поиск](search.md)
- [Класс TextBox](https://msdn.microsoft.com/library/windows/apps/br209683)
- [Класс Windows.UI.Xaml.Controls PasswordBox](https://msdn.microsoft.com/library/windows/apps/br227519)
- [Свойство String.Length](https://msdn.microsoft.com/library/system.string.length.aspx)
