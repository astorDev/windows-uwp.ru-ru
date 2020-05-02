---
Description: Поле текстового ввода, где отображается предлагаемый текст по мере ввода пользователем.
title: Поле со списком и список
label: Combo box and list box
template: detail.hbs
ms.date: 10/02/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: ''
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 31b3bcc2388a98941fc5e8aa44d18beee53de5c7
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80081097"
---
# <a name="combo-box-and-list-box"></a>Поле со списком и список

Поле со списком (также известное как раскрывающийся список) позволяет представить список элементов, из которых пользователь может выбирать. Сначала поле со списком представлено в компактном состоянии, а затем развертывается для отображения списка элементов, доступных для выбора. Элемент ListBox аналогичен полю со списком, но он не сворачивается и не имеет компактной формы. Дополнительные сведения о списках приведены в конце этой статьи.

Если поле со списком закрыто, отображается либо текущий выбор, либо пустое поле (если ни один из элементов не выбран). Когда пользователь развертывает поле со списком, оно отображает список элементов, доступных для выбора.

![Пример раскрывающегося списка в компактном состоянии](images/combo_box_collapsed.png)

> _Поле со списком в компактном состоянии с заголовком_

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Библиотека пользовательского интерфейса Windows 2.2 или более поздних версий содержит новый шаблон для этого элемента управления, который использует закругленные углы. Дополнительные сведения см. в разделе о [радиусе угла](/windows/uwp/design/style/rounded-corner). WinUI — это пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **API платформы:** [класс ComboBox](/uwp/api/Windows.UI.Xaml.Controls.ComboBox), [свойство IsEditable](/uwp/api/windows.ui.xaml.controls.combobox.iseditable), [свойство Text](/uwp/api/Windows.UI.Xaml.Controls.ComboBox), [событие TextSubmitted](/uwp/api/Windows.UI.Xaml.Controls.ComboBox)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

- Используйте раскрывающийся список, чтобы дать пользователям возможность выбрать одно значение из набора элементов, которые можно надлежащим образом представить с помощью отдельных строк текста.
- Используйте представление списка или сетки вместо поля со списком для отображения элементов, состоящих из нескольких строк текста или изображений.
- Если элементов меньше пяти, возможно, лучше воспользоваться [переключателями](radio-button.md) (если можно выбрать только один элемент) или [флажками](checkbox.md) (если можно выбрать несколько элементов).
- Используйте поле со списком, если элементы выбора не имеют большого значения для работы приложения. Если для большинства пользователей в большей части ситуаций рекомендуется использовать вариант по умолчанию, отображение всех элементов в списке может привлечь излишнее внимание к вариантам. Поле со списком позволяет экономить пространство и не отвлекать пользователя.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">коллекции элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ComboBox">открыть приложение и увидеть элемент ComboBox в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/p/xaml-controls-gallery/9msvh128x2zt">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
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

## <a name="create-a-combo-box"></a>Создание поля со списком

Для заполнения полей со списком нужно напрямую добавить объекты в коллекцию [элементов](/uwp/api/windows.ui.xaml.controls.itemscontrol.items) или привязать свойство [ItemsSource](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) к источнику данных. Добавляемые в ComboBox элементы помещаются в контейнеры [ComboBoxItem](/uwp/api/windows.ui.xaml.controls.comboboxitem).

Здесь представлено простое поле со списком с добавленными элементами в формате XAML.

```xaml
<ComboBox Header="Colors" PlaceholderText="Pick a color" Width="200">
    <x:String>Blue</x:String>
    <x:String>Green</x:String>
    <x:String>Red</x:String>
    <x:String>Yellow</x:String>
</ComboBox>
```

В следующем примере демонстрируется привязка поля со списком к коллекции объектов FontFamily.

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

Подобно ListView и GridView, элемент ComboBox является производным от элемента выбора [Selector](/uwp/api/windows.ui.xaml.controls.primitives.selector), поэтому выбор пользователя можно получить стандартным образом.

Вы можете получить или задать выбранный в поле со списком элемент с помощью свойства [SelectedItem](/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem), а индекс выбранного элемента — с помощью свойства [SelectedIndex](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex).

Чтобы получить значение конкретного свойства для выбранного элемента данных, можно использовать свойство [SelectedValue](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedvalue). В этом случае задайте свойство [SelectedValuePath](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedvaluepath) для указания свойства выбранного элемента, из которого необходимо получить значение.

> [!TIP]
> В случае задания свойства SelectedItem или SelectedIndex для указания выбора по умолчанию возникает исключение, если свойство задано до заполнения коллекции элементов для поля со списком. Если вы не определяете элементы в коде XAML, лучше всего обработать событие Loaded поля со списком и задать параметр SelectedItem или SelectedIndex в обработчике событий Loaded.

Вы можете осуществить привязку к этим свойствам в XAML или обрабатывать событие [SelectionChanged](/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) для реагирования на изменения выбора.

В коде обработчика событий выбранный элемент можно получить из свойства [SelectionChangedEventArgs.AddedItems](/uwp/api/windows.ui.xaml.controls.selectionchangedeventargs.addeditems). Ранее выбранный элемент (при наличии) можно получить из свойства [SelectionChangedEventArgs.RemovedItems](/uwp/api/windows.ui.xaml.controls.selectionchangedeventargs.removeditems). Каждая из коллекций AddedItems и RemovedItems содержит только один элемент, так как поле со списком не поддерживает выбор нескольких элементов.

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

#### <a name="selectionchanged-and-keyboard-navigation"></a>Событие SelectionChanged и навигация с помощью клавиатуры

По умолчанию событие SelectionChanged возникает в том случае, когда пользователь щелкает элемент в списке, касается его или нажимает клавишу ВВОД, наведя на элемент указатель, чтобы зафиксировать свой выбор, и поле со списком закрывается. Выбор не изменяется в том случае, когда пользователь осуществляет навигацию по открытому полю со списком с помощью клавиш со стрелками на клавиатуре.

Чтобы создать поле со списком, которое обновляется в режиме реального времени, пока пользователь использует клавиши со стрелками (например, раскрывающийся список выбора шрифтов), задайте для свойства [SelectionChangedTrigger](/uwp/api/windows.ui.xaml.controls.combobox.selectionchangedtrigger) значение [Always](/uwp/api/windows.ui.xaml.controls.comboboxselectionchangedtrigger). В результате событие SelectionChanged возникает при переходе фокуса на другой элемент в открытом списке.

#### <a name="selected-item-behavior-change"></a>Изменение алгоритма поведения выбранного элемента

В Windows 10, версия 1809 ([пакет SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздняя, алгоритм поведения выбранных элементов обновлен — теперь они поддерживают редактируемые поля со списком.

В более ранних версиях пакета SDK, чем 17763, значение свойства SelectedItem (и, следовательно, свойств SelectedValue и SelectedIndex) должно было находиться в коллекции элементов поля со списком. В предыдущем примере установка `colorComboBox.SelectedItem = "Pink"` приводит к таким выходным данным:

- SelectedItem = null.
- SelectedValue = null.
- SelectedIndex = -1.

В пакете SDK версии 17763 и более поздних значение свойства SelectedItem (и, следовательно, свойств SelectedValue и SelectedIndex) не должно находиться в коллекции элементов поля со списком. В предыдущем примере установка `colorComboBox.SelectedItem = "Pink"` приводит к таким выходным данным:

- SelectedItem =Pink.
- SelectedValue = Pink.
- SelectedIndex = -1.

### <a name="text-search"></a>Поиск по тексту

Поля со списком автоматически поддерживают поиск в их коллекциях. По мере того, как пользователь вводит символы на физической клавиатуре, выбрав открытое или закрытое поле со списком, отображаются варианты, подходящие под строку пользователя. Эта функция особенно полезна при навигации по длинному списку. Например, при взаимодействии с раскрывающимся списком, который содержит список штатов, пользователь может нажать клавишу W, чтобы представить "Washington" для быстрого выбора. Текстовый поиск не чувствителен к регистру.

Чтобы отключить эту функцию, можно задать для свойства [IsTextSearchEnabled](/uwp/api/windows.ui.xaml.controls.combobox.istextsearchenabled) значение **false**.

## <a name="make-a-combo-box-editable"></a>Создание редактируемого поля со списком

> [!IMPORTANT]
> Для этой функции требуется Windows 10, версия 1809 ([пакет SDK 17763](https://developer.microsoft.com/windows/downloads/windows-10-sdk)) или более поздняя.

По умолчанию поле со списком позволяет пользователю выбирать из предопределенного списка вариантов. Однако существуют случаи, когда список содержит только подмножество допустимых значений и пользователь должен иметь возможность ввести остальные значения, которые не включены в список. Для этого можно создать редактируемое поле со списком.

Чтобы создать редактируемое поле со списком, задайте для свойства [IsEditable](/uwp/api/windows.ui.xaml.controls.combobox.iseditable) значение **true**. Затем обработайте событие [TextSubmitted](/uwp/api/Windows.UI.Xaml.Controls.ComboBox) для работы со значением, которое ввел пользователь.

По умолчанию значение SelectedItem обновляется, когда пользователь фиксирует введенный текст. Это поведение можно переопределить, задав для свойства **Handled** значение **true** в аргументе события TextSubmitted. Если событие отмечено как обработанное, в поле со списком не будут предприниматься дополнительные действия после завершения события и поле будет оставаться редактируемым. Значение SelectedItem не будет обновляться.

В приведенном ниже примере показано простое редактируемое поле со списком. Список содержит простые строки, и любое введенное пользователем значение будет использоваться в том виде, в котором оно было введено.

Элемент выбора "недавно использованные имена" позволяет вводить пользовательские строки. Список RecentlyUsedNames содержит некоторые значения, из которых можно выбирать, но пользователь также может добавлять новые пользовательские значения. Свойство CurrentName представляет имя, введенное в настоящий момент.

```xaml
<ComboBox IsEditable="true"
          ItemsSource="{x:Bind RecentlyUsedNames}"
          SelectedItem="{x:Bind CurrentName, Mode=TwoWay}"/>
```

### <a name="text-submitted"></a>Событие TextSubmitted

Вы можете обработать событие [TextSubmitted](/uwp/api/Windows.UI.Xaml.Controls.ComboBox) для работы со значением, которое ввел пользователь. Как правило, в обработчике событий проверяется правильность введенного пользователем значения, после чего значение используется в приложении. В зависимости от ситуации, можно также добавить значение в поле списка вариантов для будущего использования.

Событие TextSubmitted возникает при выполнении следующих условий:

- Для свойства IsEditable установлено значение **true**.
- Пользователь вводит текст, который не соответствует текущей записи в поле со списком.
- Пользователь нажимает клавишу ВВОД или перемещает фокус с поля со списком.

Событие TextSubmitted не происходит, если пользователь вводит текст, а затем осуществляет навигацию вверх или вниз по списку.

### <a name="sample---validate-input-and-use-locally"></a>Пример. Проверка входных данных и локальное использование

В этом примере элемент выбора размера шрифта содержит набор значений, которые соответствуют таблице размеров шрифта, однако пользователь может также задать значения, которые не внесены в список.

Когда пользователь добавляет значение, которое отсутствует в списке, размер шрифта обновляется, однако значение не заносится в этот список.

Если только что введенное значение не является допустимым, необходимо использовать свойство SelectedValue, чтобы вернуть для свойства Text последнее допустимое значение.

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
        // Update the app's font size.
        _fontSize = newValue;
    }
    else
    {
        // If the item is invalid, reject it and revert the text.
        // Mark the event as handled so the framework doesn't update the selected item.
        sender.Text = sender.SelectedValue.ToString();
        e.Handled = true;
    }
}
```

### <a name="sample---validate-input-and-add-to-list"></a>Пример. Проверка входных данных и добавление значений в список

В этом примере средство выбора избранного цвета содержит наиболее распространенные избранные цвета (красный, синий, зеленый, оранжевый). Однако пользователь также может ввести собственный избранный цвет, который не включен в список. Когда пользователь добавляет допустимый цвет (например, розовый), этот цвет добавляется в список и устанавливается как активный избранный цвет.

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
        // Mark the event as handled so the framework doesn't update the selected item.
        e.Handled = true;
    }
}

bool IsValid(string Text)
{
    // Validate that the string is: not empty; a color.
}
```

## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать

- Ограничьте текстовое содержимое элементов полей со списком одной строкой.
- Сортируйте элементы в поле со списком в логическом порядке. Группируйте связанные параметры и размещайте часто используемые параметры в начале списка. Названия сортируйте в алфавитном порядке, числа — по возрастанию, даты — по хронологии.

## <a name="list-boxes"></a>Окна со списками

Окно со списком позволяет пользователю выбрать один или несколько элементов коллекции. Окна со списками аналогичны раскрывающимся спискам за исключением того, что они всегда открыты — у окна со списком нет компактного (не развернутого) состояния. Элементы списка можно прокручивать, если для отображения списка полностью не хватает места.

### <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

- Окно со списком может быть полезно, если элементы списка достаточно важны и экранного пространства достаточно для отображения всего списка.
- При важном выборе список должен привлечь внимание пользователя к полному списку вариантов. И наоборот, раскрывающийся список изначально привлекает внимание пользователя к выбранному элементу.
- Не используйте список в следующих случаях.
    - Список содержит очень малое число элементов. Список с возможностью выбора одного варианта, в котором содержится два пункта, лучше представить в виде [переключателей](radio-button.md). Также подумайте о возможности использования переключателей, если в списке 3 или 4 статических элемента.
    - Список дает возможность выбрать один вариант из двух. Выбор одного варианта подразумевает, что другой не выбран — например, «включено» и «выключено». Используйте один флажок или тумблер.
    - Очень большое число элементов. Для длинных списков оптимальным решением является представление сетки и представление списка. Для очень длинных списков сгруппированных данных лучше использовать контекстное масштабирование.
    - Список представляет собой непрерывные числовые значения. В этом случае попробуйте использовать [ползунок](slider.md).
    - Элементы выбора не имеют большого значения для работы приложения, или же рекомендуется использовать вариант по умолчанию для большинства пользователей и ситуаций. Вместо списка используйте раскрывающийся список.

### <a name="recommendations"></a>Рекомендации

- Идеальное количество элементов в окне со списком — от 3 до 9.
- Также окно со списком хорошо подходит, если элементы списка могут динамически изменяться.
- По возможности настройте размер окна со списком таким образом, чтобы список элементов не нужно было сдвигать или прокручивать.
- Убедитесь, что цель окна со списком, а также выбранные в данный момент элементы понятны.
- Предусмотрите визуальные эффекты и анимации для реакции на касание и состояние выбора элементов.
- Ограничьте текстовое содержимое элементов списка одной строкой. Если элементы списка — это визуальные элементы, можно настроить их размер. Если элемент содержит несколько строк текста или изображений, используйте представление сетки или представление списка.
- Используйте шрифт по умолчанию, если руководство по использованию корпоративного стиля не требует использования другого шрифта.
- Не используйте окно с списком для выполнения команд или динамического скрытия либо отображения других элементов управления.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.
- [Пример AutoSuggestBox](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlAutoSuggestBox)

## <a name="related-articles"></a>Похожие статьи

- [Текстовые элементы управления](text-controls.md)
- [Проверка орфографии](text-controls.md)
- [Поиск](search.md)
- [Класс TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)
- [Класс Windows.UI.Xaml.Controls PasswordBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox)
- [Свойство String.Length](https://docs.microsoft.com/dotnet/api/system.string.length)
