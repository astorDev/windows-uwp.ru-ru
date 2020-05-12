---
ms.assetid: CC1BF51D-3DAC-4198-ADCB-1770B901C2FC
Description: Элемент управления TextBox позволяет пользователю вводить текст в приложение.
title: Текстовое поле
label: Text box
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 825f2cec4723139f187da6e9ea0d4b2dbb14457c
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970679"
---
# <a name="text-box"></a>Текстовое поле

Элемент управления TextBox позволяет пользователю вводить текст в приложение. Обычно он используется для записи одной строки текста. Но элемент можно настроить и для записи нескольких строк. Текст отображается на экране в простом унифицированном формате обычного текста.

Элемент TextBox имеет несколько компонентов, которые могут упростить ввод текста. В этом элементе работает привычное встроенное контекстное меню с поддержкой копирования и вставки текста. Кнопка "Очистить все" позволяет пользователю быстро удалить весь введенный текст. В нем также есть встроенные функции проверки орфографии, которые включены по умолчанию.

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Библиотека пользовательского интерфейса Windows 2.2 или более поздних версий содержит новый шаблон для этого элемента управления, который использует закругленные углы. Дополнительные сведения см. в разделе о [радиусе угла](/windows/uwp/design/style/rounded-corner). WinUI — это пакет NuGet, содержащий новые элементы управления и функции пользовательского интерфейса для приложений для Windows. Дополнительные сведения, включая инструкции по установке, см. в описании [библиотеки пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

> **API платформы**: [Класс TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox), [свойство Text](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.text).

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте элемент управления **TextBox**, чтобы пользователь мог вводить и редактировать неформатированный текст, в частности, в форме. С помощью свойства [Text](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.text) можно получить и задать текст в элементе управления TextBox.

Вы можете сделать элемент TextBox предназначенным только для чтения. Но это должно быть временным, условным состоянием. Если текст не редактируется, попробуйте использовать элемент [TextBlock](text-block.md).

Элемент управления [PasswordBox](password-box.md) можно использовать для сбора паролей или других конфиденциальных данных, например номеров карт социального страхования. Поле ввода пароля внешне похоже на поле текстового ввода с той разницей, что в нем вместо вводимого текста отображаются маркеры.

Используйте элемент управления [AutoSuggestBox](auto-suggest-box.md), чтобы позволить пользователю искать термины или показать пользователю список рекомендаций для выбора во время ввода.

Элемент [RichEditBox](rich-edit-box.md) служит для отображения и правки текстовых файлов.

Дополнительные сведения можно найти в статье об [элементах управления текстом](text-controls.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/TextBox">открыть приложение и увидеть TextBox в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

![Текстовое поле](images/text-box.png)

## <a name="create-a-text-box"></a>Создание текстового поля

Вот код XAML для простого текстового поля с заголовком и замещающим текстом.

```xaml
<TextBox Width="500" Header="Notes" PlaceholderText="Type your notes here"/>
```

```csharp
TextBox textBox = new TextBox();
textBox.Width = 500;
textBox.Header = "Notes";
textBox.PlaceholderText = "Type your notes here";
// Add the TextBox to the visual tree.
rootGrid.Children.Add(textBox);
```

Ниже показано текстовое поле, полученное в результате выполнения этого фрагмента кода XAML.

![Простое текстовое поле](images/text-box-ex1.png)

### <a name="use-a-text-box-for-data-input-in-a-form"></a>Использование текстового поля для ввода данных в форму

Текстовое поле часто используется, чтобы принять ввод данных в форму и получить полную текстовую строку из текстового поля с помощью свойства [Text](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.text). Обычно доступ к свойству Text осуществляется в таких событиях, как нажатие кнопки "Отправить". Но вы можете использовать событие [TextChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.textchanged) или [TextChanging](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.textchanging), если нужно выполнить какие-либо действия сразу при изменении текста.

В этом примере показано, как получить и задать текущее содержимое текстового поля.

```xaml
<TextBox name="SampleTextBox" Text="Sample Text"/>
```

```csharp
string sampleText = SampleTextBox.Text;
...
SampleTextBox.Text = "Sample text retrieved";
```

К текстовому полю можно добавить [Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.header) (или метку) и [PlaceholderText](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.placeholdertext) (или водяной знак), чтобы пользователь понимал, для чего оно используется. Чтобы настроить оформление заголовка, задайте свойство [HeaderTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.headertemplate) вместо Header. *Сведения о проектировании см. в руководстве по использованию меток*.

Вы можете ограничить число вводимых пользователем символов, настроив свойство [MaxLength](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.maxlength). Но свойство MaxLength не ограничивает длину вставленного текста. Используйте событие [Paste](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.paste), чтобы изменить вставленный текст, если это важно для вашего приложения.

В текстовом поле есть кнопка "Очистить все" ("X"), которая появляется при введении текста в поле. Когда пользователь нажимает кнопку "X", текст в поле удаляется. Это выглядит следующим образом:

![Текстовое поле с кнопкой "Очистить все"](images/text-box-clear-all.png)

Кнопка "Очистить все" отображается только для редактируемых однострочных текстовых полей, которые содержат текст и имеют фокус.

Кнопка "Очистить все" не отображается в любом из перечисленных ниже случаев.

- Параметр **IsReadOnly** имеет значение **true**.
- Параметр **AcceptsReturn** имеет значение **true**.
- **TextWrap** имеет значение, отличное от **NoWrap**.

В этом примере показано, как получить и задать текущее содержимое текстового поля.

```xaml
<TextBox name="SampleTextBox" Text="Sample Text"/>
```

```csharp
string sampleText = SampleTextBox.Text;
...
SampleTextBox.Text = "Sample text retrieved";
```


### <a name="make-a-text-box-read-only"></a>Текст, предназначенный только для чтения

Текстовое поле можно сделать доступным только для чтения, присвоив свойству [IsReadOnly](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.isreadonly) значение **true**. Обычно вы переключаете это свойство в приложении, исходя из условий в приложении. Если вам нужен текст, который всегда предназначен только для чтения, воспользуйтесь элементом TextBlock.

Объект TextBox можно сделать доступным только для чтения, присвоив свойству IsReadOnly значение true. Например, у вас может быть элемент TextBox для пользовательских комментариев, который включается только при определенных условиях. Элемент TextBox можно сделать предназначенным только для чтения, пока не будут выполняться эти условия. Если вам нужно только отобразить текст, воспользуйтесь элементом TextBlock или RichTextBlock.

Текстовое поле, предназначенное только для чтения, ничем не отличается от текстового поля, предназначенного для чтения и записи. Поэтому пользователь может растеряться.
Пользователь может выделять и копировать текст.
IsEnabled

### <a name="enable-multi-line-input"></a>Включение многострочного ввода

Есть два свойства, которые позволяют управлять отображением текста на нескольких строках в текстовом поле. Обычно нужно указать оба эти свойства, чтобы сделать текстовое поле многострочным.

- Чтобы позволить текстовому полю разрешать и отображать символы новой строки или возврата, установите для свойства [AcceptsReturn](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.acceptsreturn) значение **true**.
- Чтобы включить обтекание текстом, установите для свойства [TextWrapping](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.textwrapping) значение **Wrap**. Теперь текст будет переноситься на новую строку, когда он достигает края текстового поля, независимо от символов разделителя строки.

> **Примечание.** &nbsp;&nbsp;Элементы TextBox и RichEditBox не поддерживают значение **WrapWholeWords** для своих свойств TextWrapping. Если попытаться указать WrapWholeWords в качестве значения для параметра TextBox.TextWrapping или RichEditBox.TextWrapping, отобразится исключение с сообщением о недействительном аргументе.

Многострочное текстовое поле будет постоянно увеличиваться по вертикали по мере ввода текста, если нет ограничений в свойстве [Height](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.height), [MaxHeight](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.maxheight) или родительском контейнере. Следите за тем, чтобы многострочное текстовое поле не выходило за пределы видимой зоны. Если это произошло, ограничьте его увеличение. Мы рекомендуем всегда указывать целесообразную высоту для многострочного текстового поля и не позволять ему увеличиваться в высоту во время ввода текста пользователем.

Прокрутка с помощью колесика прокрутки или пальца включается автоматически по мере необходимости. Но вертикальные полосы прокрутки не отображаются по умолчанию. Вы можете отобразить вертикальные полосы прокрутки, установив для параметра [ScrollViewer.VerticalScrollBarVisibility](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollbarvisibility) значение **Auto** для встроенного класса ScrollViewer, как показано здесь.

```xaml
<TextBox AcceptsReturn="True" TextWrapping="Wrap"
         MaxHeight="172" Width="300" Header="Description"
         ScrollViewer.VerticalScrollBarVisibility="Auto"/>
```

```csharp
TextBox textBox = new TextBox();
textBox.AcceptsReturn = true;
textBox.TextWrapping = TextWrapping.Wrap;
textBox.MaxHeight = 172;
textBox.Width = 300;
textBox.Header = "Description";
ScrollViewer.SetVerticalScrollBarVisibility(textBox, ScrollBarVisibility.Auto);
```

Вот как выглядит текстовое поле после добавления текста.

![Поле многострочного текста](images/text-box-multi-line.png)

### <a name="format-the-text-display"></a>Форматирование отображаемого текста

Используйте свойство [TextAlignment](/uwp/api/windows.ui.xaml.controls.textbox.textalignment) для выравнивания текста в текстовом поле. Чтобы выровнять текстовое поле в рамках макета страницы, используйте свойства [HorizontalAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.horizontalalignment) и [VerticalAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.verticalalignment).

Хотя текстовое поле поддерживает только неформатированный текст, вы можете настроить отображение текста в текстовом поле в соответствии с фирменным оформлением. Можно задать стандартные свойства [Control](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control), например, [FontFamily](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.fontfamily), [FontSize](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.fontsize), [FontStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.fontstyle), [Background](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.background), [Foreground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground) и [CharacterSpacing](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.characterspacing), чтобы изменить внешний вид текста. Эти свойства влияют только на то, как текстовое поле отображает текст локально. Например, если скопировать и вставить текст в элемент управления для форматированного текста, форматирование не будет применено.

В этом примере показано текстовое поле, предназначенное только для чтения, с несколькими настроенными свойствами для внешнего вида текста.

```xaml
<TextBox Text="Sample Text" IsReadOnly="True"
         FontFamily="Verdana" FontSize="24"
         FontWeight="Bold" FontStyle="Italic"
         CharacterSpacing="200" Width="300"
         Foreground="Blue" Background="Beige"/>
```

```csharp
TextBox textBox = new TextBox();
textBox.Text = "Sample Text";
textBox.IsReadOnly = true;
textBox.FontFamily = new FontFamily("Verdana");
textBox.FontSize = 24;
textBox.FontWeight = Windows.UI.Text.FontWeights.Bold;
textBox.FontStyle = Windows.UI.Text.FontStyle.Italic;
textBox.CharacterSpacing = 200;
textBox.Width = 300;
textBox.Background = new SolidColorBrush(Windows.UI.Colors.Beige);
textBox.Foreground = new SolidColorBrush(Windows.UI.Colors.Blue);
// Add the TextBox to the visual tree.
rootGrid.Children.Add(textBox);
```

Созданное текстовое поле выглядит следующим образом:

![Форматированное текстовое поле](images/text-box-formatted.png)

### <a name="modify-the-context-menu"></a>Изменение контекстного меню

По умолчанию команды, отображаемые в контекстном меню текстового поля, зависят от состояния текстового поля. Например, если текстовое поле является редактируемым, отображаются следующие команды:

Команда | Отображается, когда...
------- | -------------
Копировать | ...текст выделен.
Вырезать | ...текст выделен.
Вставить | ...буфер обмена содержит текст.
Выбрать все | ...элемент TextBox содержит текст.
Отмена | ...текст был изменен.

Чтобы изменить команды, отображаемые в контекстном меню, обрабатывайте событие [ContextMenuOpening](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.contextmenuopening). См. **пример** такой обработки с добавлением свойства Share в поле CommandBarFlyout элемента управления RichEditBox в <a href="xamlcontrolsgallery:/item/RichEditBox">коллекции элементов управления XAML</a>. Сведения о проектировании см. в [руководстве по контекстным меню](menus.md).

### <a name="select-copy-and-paste"></a>Выделение, копирование и вставка

Получить выбранный текст из текстового поля или задать его можно с помощью свойства [SelectedText](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectedtext). Чтобы управлять выделением текста, используйте свойства [SelectionStart](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectionstart) и [SelectionLength](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectionlength), а также методы [Select](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.select) и [SelectAll](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectall). Событие [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectionchanged) позволяет выполнять действия, пока пользователь выбирает или отменяет выбор текста. Вы можете изменить цвет, используемый для выделения выбранного текста, настроив свойство [SelectionHighlightColor](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectionhighlightcolor).

Элемент TextBox поддерживает копирование и вставку по умолчанию. Вы можете реализовать в приложении пользовательскую обработку события [Paste](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.paste) в редактируемых текстовых элементах управления. Например, можно удалить разрывы строк из многострочного адреса при вставке в однострочное поле поиска. Или вы можете проверить длину вставленного текста и предупредить пользователя, если превышает максимальную длину текста, который можно сохранить в базе данных. Дополнительные сведения и примеры см. в статье о событии [Paste](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.paste).

Ниже приведен пример применяемых для этого свойств и методов. При выборе текста в первом текстовом поле выбранный текст отображается во втором текстовом поле, которое доступно только для чтения. Значения свойств [SelectionLength](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectionlength) и [SelectionStart](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectionstart) отображаются в двух текстовых блоках. Для этого используется событие [SelectionChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.selectionchanged).

```xaml
<StackPanel>
   <TextBox x:Name="textBox1" Height="75" Width="300" Margin="10"
         Text="The text that is selected in this TextBox will show up in the read only TextBox below."
         TextWrapping="Wrap" AcceptsReturn="True"
         SelectionChanged="TextBox1_SelectionChanged" />
   <TextBox x:Name="textBox2" Height="75" Width="300" Margin="5"
         TextWrapping="Wrap" AcceptsReturn="True" IsReadOnly="True"/>
   <TextBlock x:Name="label1" HorizontalAlignment="Center"/>
   <TextBlock x:Name="label2" HorizontalAlignment="Center"/>
</StackPanel>
```

```csharp
private void TextBox1_SelectionChanged(object sender, RoutedEventArgs e)
{
    textBox2.Text = textBox1.SelectedText;
    label1.Text = "Selection length is " + textBox1.SelectionLength.ToString();
    label2.Text = "Selection starts at " + textBox1.SelectionStart.ToString();
}
```

Вот результат выполнения этого кода.

![Выбранный текст в текстовом поле](images/text-box-selection.png)

## <a name="choose-the-right-keyboard-for-your-text-control"></a>Выбор подходящей клавиатуры для элемента управления текстом

Чтобы упростить пользователям ввод данных с помощью сенсорной клавиатуры или панели функционального ввода, можно настроить тип вводимых данных элемента управления для ввода текста таким образом, чтобы элемент управления соответствовал типу данных, которые должен вводить пользователь.

Сенсорная клавиатура позволяет вводить текст, если приложение выполняется на устройстве с сенсорным экраном. Сенсорная клавиатура появляется, когда пользователь касается редактируемого поля ввода, например, TextBox или RichEditBox. Пользователи смогут вводить данные в вашем приложении гораздо быстрее и с меньшими усилиями, если вы настроите тип вводимых данных элемента управления текстом в соответствии с типом вводимых данных. Тип вводимых данных предоставляет системе подсказку о типе текстового ввода, ожидаемого элементом управления. Благодаря этому система может отобразить специальную раскладку сенсорной клавиатуры в соответствии с используемым типом ввода.

Например, если текстовое поле используется только для ввода 4-значного PIN-кода, установите для свойства [InputScope](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.inputscope) значение **Number**. Это сообщает системе, что нужно отобразить раскладку цифровой клавиатуры, благодаря чему пользователю проще вводить PIN-код.

> **Внимание!** &nbsp;&nbsp;Тип вводимых данных не вызывает выполнение проверки ввода, а также не препятствует пользователю вводить любые данные через аппаратную клавиатуру или другое устройство ввода. Вы по-прежнему несете ответственность за проверку вводимых данных в коде.

Другие свойства, влияющие на сенсорную клавиатуру: [IsSpellCheckEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.isspellcheckenabled), [IsTextPredictionEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.istextpredictionenabled) и [PreventKeyboardDisplayOnProgrammaticFocus](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.preventkeyboarddisplayonprogrammaticfocus). (IsSpellCheckEnabled также влияет на элемент TextBox при использовании аппаратной клавиатуры.)

Дополнительные сведения и примеры см. в статье [Использование типа вводимых данных для изменения сенсорной клавиатуры](https://docs.microsoft.com/windows/uwp/design/input/use-input-scope-to-change-the-touch-keyboard) и в документации по свойствам.

## <a name="recommendations"></a>Рекомендации

- Используйте метку или подстановочный текст, если назначение текстового поля не очевидно. Метка отображается независимо от того, указано ли значение в поле ввода. Подстановочный текст отображается внутри поля ввода текста и пропадает после ввода значения.
- Задайте для текстового поля достаточную ширину для всех его возможных значений. Длина слов различается в зависимости от языка. Поэтому если вы собираетесь выводить свое приложение на международный уровень, следует учитывать особенности локализации.
- Поле текстового ввода обычно состоит из одной строки (`TextWrap = "NoWrap"`). Если пользователям придется вводить или изменять длинную строку, используйте многострочное текстовое поле (`TextWrap = "Wrap"`).
- Обычно текстовое поле используется для ввода редактируемого текста. Но можно сделать текстовое поле доступным только для чтения, чтобы его содержимое можно было читать, выделять и копировать, но не изменять.
- Если нужно избежать заполнения представления ненужными данными, сделайте так, чтобы набор полей текстового ввода появлялся только после установки управляющего флажка. Состояние включения поля текстового ввода также можно привязать к элементу управления, например к флажку.
- Подумайте о том, как должно вести себя поле текстового ввода, содержащее значение, когда его выбирает пользователь. Поведение по умолчанию подходит для редактирования значений, но не для замены. Точка вставки помещается между словами, и ничего не выделяется. Если замена является наиболее распространенным вариантом использования для этого текстового поля, то можно настроить выделение всего текста в поле при получении фокуса элементом управления. Вводимый с клавиатуры текст заменит выделенный текст.

### <a name="single-line-input-boxes"></a>Поля ввода из одной строки

- Используйте несколько однострочных текстовых полей для сбора небольших фрагментов текстовой информации. Если текстовые поля связаны между собой, сгруппируйте их.

- Делайте однострочные текстовые поля немного шире предполагаемого текста. Если элемент управления получится слишком широким, разделите его на два элемента. Например, можно разделить однострочное поле адреса на "Адрес, строка 1" и "Адрес, строка 2".
- Задайте максимальную длину вводимого текста в символах. Если базовый источник данных не позволяет вводить длинные строки текста, ограничьте ввод и сообщите пользователю об ограничении с помощью всплывающего окна.
- Чтобы пользователи могли вводить маленькие фрагменты текста, используйте однострочные элементы управления.

    В следующем примере показано текстовое поле для записи ответа на вопрос безопасности. Так как предполагается краткий ответ, то здесь вполне подходит поле, состоящее из одной строки.

    ![Ввод простых данных](images/guidelines_and_checklist_for_singleline_text_input_type_text.png)

- Для ввода данных особого формата используйте набор коротких однострочных элементов управления фиксированного размера.

    ![Ввод форматированных данных](images/textinput-example-productkey.png)

- Для ввода или правки текста используйте однострочный элемент управления неограниченного размера в сочетании с кнопкой, нажав на которую, пользователь сможет выбрать допустимое значение.

    ![Ввод данных с выбором](images/textinput-example-assisted.png)

### <a name="multi-line-text-input-controls"></a>Элементы управления для ввода многострочного текста

- При создании текстового поля в формате RTF следует предусмотреть кнопки стиля и реализовать их действия.
- Используйте шрифт, соответствующий стилю вашего приложения.
- Высота текстового элемента управления должна быть достаточной для ввода типичных записей.
- При записи больших объемов текста с ограничением количества символов или слов используйте простое текстовое поле. Предоставьте динамический счетчик, показывающий пользователю, сколько символов или слов у него осталось до достижения максимального ограничения. Счетчик придется создать самостоятельно. Разместите его под текстовым полем и обеспечьте динамическое обновление при вводе пользователем символов или слов.

    ![Длинный текст](images/guidelines_and_checklist_for_multiline_text_input_text_limits.png)

- При вводе пользователем текста элементы управления не должны увеличиваться по высоте.
- Не используйте поле для многострочного текста там, где требуется ввести всего одну строку.
- Не используйте поле с форматом там, где достаточно элемента управления обычного текста.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи

- [Текстовые элементы управления](text-controls.md)
- [Руководство по проверке орфографии](text-controls.md)
- [Добавление поиска](https://docs.microsoft.com/previous-versions/windows/apps/hh465231(v=win.10))
- [Руководство по текстовому вводу](text-controls.md)
- [Класс TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)
- [Класс PasswordBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox)
- [Свойство String.Length](https://docs.microsoft.com/dotnet/api/system.string.length)
