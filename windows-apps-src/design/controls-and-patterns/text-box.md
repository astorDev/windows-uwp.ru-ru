---
ms.assetid: CC1BF51D-3DAC-4198-ADCB-1770B901C2FC
Description: The TextBox control lets a user enter text into an app.
title: Текстовое поле
label: Text box
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: a40449b66d2187aacb60501240a0c38de2dc3abc
ms.sourcegitcommit: b11f305dbf7649c4b68550b666487c77ea30d98f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/27/2018
ms.locfileid: "7854146"
---
# <a name="text-box"></a>Текстовое поле

 

Элемент управления TextBox позволяет пользователю вводить текст в приложение. Он обычно используется для записи одной строки текста, но его можно настроить для записи нескольких строк текста. Текст отображается на экране в простом, однообразном формате обычного текста.

Элемент TextBox имеет несколько компонентов, которые могут упростить ввод текста. Он поставляется со знакомым, встроенным контекстным меню с поддержкой копирования и вставки текста. Кнопка "Очистить все" позволяет пользователю быстро удалить весь текст, который был введен. В нем также есть функции проверки правописания, встраиваемые и включаемые по умолчанию.

> **Важные API-интерфейсы**: [класс TextBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.aspx), [свойство Text](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.text.aspx)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте элемент управления **TextBox**, чтобы позволить пользователю вводить и редактировать неформатированный текст, в частности, в форме. С помощью свойства [Text](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.text.aspx) можно получить и задать текст в элементе управления TextBox.

Вы можете сделать элемент TextBox предназначенным только для чтения, но это должно быть временным, условным состоянием. Если текст не редактируется, попробуйте использовать элемент [TextBlock](text-block.md).

Элемент управления [PasswordBox](password-box.md) может использоваться для сбора паролей или других конфиденциальных данных, например номеров карт социального страхования. Поле ввода пароля внешне похоже на поле текстового ввода с той разницей, что в нем вместо вводимого текста отображаются маркеры.

Используйте элемент управления [AutoSuggestBox](auto-suggest-box.md), чтобы позволить пользователю искать термины или показать пользователю список рекомендаций для выбора во время ввода.

Элемент [RichEditBox](rich-edit-box.md) служит для отображения и правки текстовых файлов.

Дополнительные сведения о выборе подходящего элемента управления текстом см. в статье [Элементы управления текстом](text-controls.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/TextBox">открыть приложение и увидеть TextBox в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

![Текстовое поле](images/text-box.png)

## <a name="create-a-text-box"></a>Создание текстового поля

Вот XAML для простого текстового поля с заголовком и замещающим текстом.

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

Текстовое поле часто используется для принятия ввода данных в форму и использования свойства [Text](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.text.aspx) для получения полной текстовой строки из текстового поля. Обычно вы используете событие, например, нажатие кнопки "Отправить", для получения доступа к свойству Text, но вы можете обрабатывать событие [TextChanged](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.textchanged.aspx) или [TextChanging](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.textchanging.aspx), если необходимо выполнить какие-либо действия в случае изменения текста.

К элементу выбора даты в календаре можно добавить [Header](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.header.aspx) (или метку) и [PlaceholderText](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.placeholdertext.aspx) (или водяной знак), чтобы пользователь понимал, для чего он используется. Чтобы настроить оформление заголовка, задайте свойство [HeaderTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.headertemplate.aspx) вместо Header. *Сведения о проектировании см. в Руководстве по использованию меток*.

Вы можете ограничить число вводимых пользователем символов, настроив свойство [MaxLength](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.maxlength.aspx). Однако свойство MaxLength не ограничивает длину вставленного текста. Используйте событие [Paste](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.paste.aspx), чтобы изменить вставленный текст, если это важно для вашего приложения.

Текстовое поле включает в себя кнопку "Очистить все" ("X"), которая появляется при введении текста в поле. Когда пользователь нажимает кнопку "X", текст в поле удаляется. Процесс выглядит следующим образом.

![Текстовое поле с кнопкой "Очистить все"](images/text-box-clear-all.png)

Кнопка "Очистить все" отображается только для редактируемых, однострочных текстовых полей, которые содержат текст и имеют фокусировку.

Кнопка "Очистить все" не отображается в любом из перечисленных ниже случаев.
- Параметр **IsReadOnly** имеет значение **true**
- Параметр **AcceptsReturn** имеет значение **true**
- **TextWrap** имеет значение, отличное от **NoWrap**

### <a name="make-a-text-box-read-only"></a>Текст, предназначенный только для чтения

Текстовое поле можно сделать доступным только для чтения, если присвоить свойству [IsReadOnly](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.isreadonly.aspx) значение **true**. Обычно вы переключаете это свойство в приложении, исходя из условий в приложении. Если вам нужен текст, который всегда предназначен для чтения, воспользуйтесь элементом TextBlock.

Объект TextBox можно сделать доступным только для чтения, если присвоить свойству IsReadOnly значение true. Например, у вас может быть элемент TextBox для пользователя, который вводит комментарии, включающийся только при определенных условиях. Элемент TextBox можно сделать предназначенным только для чтения, пока не будут выполняться условия. Если вам нужно только отобразить текст, воспользуйтесь элементом TextBlock или RichTextBlock.

Текстовое поле, предназначенное только для чтения, выглядит аналогично текстовому полю, предназначенному для чтения и записи. Поэтому пользователь может растеряться.
Пользователь может выделять и копировать текст.
IsEnabled


### <a name="enable-multi-line-input"></a>Включение многострочного ввода

Есть два свойства, которые позволяют управлять отображением текста на нескольких строках в текстовом поле. Обычно настраиваются два свойства, чтобы сделать текстовое поле многострочным.
- Чтобы позволить текстовому полю разрешать и отображать символы новой строки или возврата, установите для свойства [AcceptsReturn](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.acceptsreturn.aspx) значение **true**.
- Чтобы включить обтекание текстом, установите для свойства [TextWrapping](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.textwrapping.aspx) значение **Wrap**. В результате будет включено обтекание текстом, когда он будет достигать края текстового поля, независимо от символов разделителя строки.

> **Примечание.**&nbsp;&nbsp;Элементы TextBox и RichEditBox не поддерживают значение **WrapWholeWords** для своих свойств TextWrapping. Если попытаться использовать WrapWholeWords в качестве значения для параметр TextBox.TextWrapping или RichEditBox.TextWrapping, отобразится исключение относительно недействительного аргумента.

Многострочное текстовое поле будет по-прежнему увеличиваться по вертикали по мере ввода текста, если он не будет ограничен своим свойством [Height](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.height.aspx) или [MaxHeight](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.maxheight.aspx) или родительским контейнером. Необходимо убедиться, что многострочечное текстовое поле не увеличивается за пределы своей видимой зоны, и ограничить его увеличение, если оно выходит за пределы. Мы рекомендуем всегда указывать надлежащую высоту для многострочечного текстового поля и не позволять ему увеличиваться в высоту во время ввода пользователем.

Прокрутка с помощью колесика прокрутки или пальца включается автоматически по мере необходимости. Однако вертикальные полосы прокрутки не отображаются по умолчанию. Вы можете отобразить вертикальные полосы прокрутки, установив для параметра [ScrollViewer.VerticalScrollBarVisibility](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.verticalscrollbarvisibility.aspx) значение **Auto** на встроенном ScrollViewer, как показано здесь.

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

Используйте свойство [TextAlignment]() для выравнивания текста в текстовом поле. Чтобы выровнять текстовое поле в рамках макета страницы, используйте свойства [HorizontalAlignment](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.horizontalalignment.aspx) и [VerticalAlignment](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.verticalalignment.aspx).

Хотя текстовое поле поддерживает только неформатированный текст, вы можете настроить то, как текст будет отображаться в текстовом поле согласно фирменному оформлению. Можно задать стандартные свойства [Control](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.aspx), например, [FontFamily](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.fontfamily.aspx), [FontSize](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.fontsize.aspx), [FontStyle](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.fontstyle.aspx), [Background](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.background.aspx), [Foreground](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.foreground.aspx) и [CharacterSpacing](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.control.characterspacing.aspx), чтобы изменить внешний вид текста. Эти свойства влияют только на то, как текстовое поле отображает текст локально, поэтому если, например, скопировать и вставить текст в элемент управления для форматированного текста, форматирование не будет применено.

В этом примере показано текстовое поле, предназначенное только для чтения, с несколькими свойствами для настройки внешнего вида текста.

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

Созданное текстовое поле выглядит следующим образом.

![Форматированное текстовое поле](images/text-box-formatted.png)

### <a name="modify-the-context-menu"></a>Изменение контекстного меню

По умолчанию команды, отображаемые в контекстном меню текстового поля, зависят от состояния текстового поля. Например, если текстовое поле является редактируемым, отображаются следующие команды.

Команда | Отображается, когда...
------- | -------------
Копировать | текст выделен.
Вырезать | текст выделен.
Вставка | буфер обмена содержит текст.
Выделить все | элемент TextBox содержит текст.
Отменить | текст был изменен.

Чтобы изменить команды, отображаемые в контекстном меню, обработайте событие [ContextMenuOpening](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.contextmenuopening.aspx). Пример см. в сценарии 2 элемента [ContextMenu sample](http://go.microsoft.com/fwlink/p/?linkid=234891). Сведения о проектировании см. в Руководстве по контекстным меню.

### <a name="select-copy-and-paste"></a>Выделение, копирование и вставка

Получить выбранный текст из текстового поля или задать его можно с помощью свойства [SelectedText](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectedtext.aspx). Чтобы управлять выделением текста, используйте свойства [SelectionStart](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectionstart.aspx) и [SelectionLength](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectionlength.aspx), а также методы [Select](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.select.aspx) и [SelectAll](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectall.aspx). Событие [SelectionChanged](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectionchanged.aspx) позволяет выполнять действия, пока пользователь выбирает или отменяет выбор текста. Вы можете изменить цвет, используемый для выделения выбранного текста, настроив свойство [SelectionHighlightColor](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectionhighlightcolor.aspx).

Элемент TextBox поддерживает копирование и вставку по умолчанию. Вы можете предоставить в приложении пользовательскую обработку события [Paste](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.paste.aspx) в редактируемых текстовых элементах управления. Например, вы можете удалить разрывы строк из многострочного адреса при ее вставке в однострочное поле поиска. Или вы можете проверить длину вставленного текста и предупредить пользователя, если он превышает максимальную длину, которую можно сохранить в базу данных. Дополнительные сведения и примеры см. в разделе о событии [Paste](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.paste.aspx).

Ниже приведен пример применяемых для этого свойств и методов. При выборе текста в первом текстовом поле выбранный текст отображается во втором текстовом поле, который доступен только для чтения. Значения свойств [SelectionLength](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectionlength.aspx) и [SelectionStart](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectionstart.aspx) отображаются в двух текстовых полях. Для этого используется событие [SelectionChanged](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.selectionchanged.aspx).

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

Чтобы упростить пользователям ввод данных с помощью сенсорной клавиатуры или панели функционального ввода, можно настроить тип вводимых данных элемента управления текстом, чтобы он соответствовал типу данных, которые должен вводить пользователь.

Сенсорная клавиатура позволяет вводить текст, если приложение выполняется на устройстве с сенсорным экраном. Сенсорная клавиатура появляется, когда пользователь касается редактируемого поля ввода, например, TextBox или RichEditBox. Пользователи могут вводить данные в вашем приложении гораздо быстрее и проще, если настроить тип вводимых данных элемента управления текстом на соответствие типу данных, которые пользователь должен вводить. Тип вводимых данных предоставляет системе подсказку о типе текстового ввода, ожидаемого элементом управления. Благодаря этому система может отобразить специальную раскладку сенсорной клавиатуры в соответствии с используемым типом ввода.

Например, если текстовое поле используется только для ввода 4-значного PIN-кода, установите для свойства [InputScope](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.inputscope.aspx) значение **Number**. Это сообщает системе, что нужно отобразить раскладку цифровой клавиатуры, благодаря чему пользователю проще вводить PIN-код.

> **Внимание!**&nbsp;&nbsp;Тип вводимых данных не вызывает выполнение проверки ввода, а также не препятствует пользователю вводить любые данные через аппаратную клавиатуру или другое устройство ввода. При необходимости вы по-прежнему несете ответственность за проверку вводимых данных в коде.

Другие свойства, влияющие на сенсорную клавиатуру: [IsSpellCheckEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.isspellcheckenabled.aspx), [IsTextPredictionEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.istextpredictionenabled.aspx) и [PreventKeyboardDisplayOnProgrammaticFocus](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.preventkeyboarddisplayonprogrammaticfocus.aspx). (IsSpellCheckEnabled также влияет на элемент TextBox при использовании аппаратной клавиатуры.)

Дополнительные сведения и примеры см. в разделе [Использование типа вводимых данных для изменения сенсорной клавиатуры](https://msdn.microsoft.com/library/windows/apps/mt280229) и документацию свойства.

## <a name="recommendations"></a>Рекомендации

-   Используйте метку или подстановочный текст, если назначение текстового поля не очевидно. Метка отображается независимо от того, указано ли значение в поле ввода. Подстановочный текст отображается внутри поля ввода текста и пропадает после ввода значения.
-   Задайте для текстового поля достаточную ширину, чтобы можно было ввести ряд значений. Длина слов различается в зависимости от языков, поэтому если вы собираетесь выводить свое приложение на международный уровень, следует учитывать особенности локализации.
-   Поле текстового ввода обычно состоит из одной строки (`TextWrap = "NoWrap"`). Если пользователям придется вводить или изменять длинную строку, используйте многострочное текстовое поле (`TextWrap = "Wrap"`).
-   В целом текстовое поле используется для ввода редактируемого текста. Однако можно сделать текстовое поле доступным только для чтения, чтобы его содержимое можно было читать, выделять и копировать, но не изменять.
-   Если необходимо избежать замусоривания представления, стоит сделать набор полей текстового ввода появляющимся только после установки управляющего флажка. Состояние включения поля текстового ввода также можно привязать к элементу управления, например к Checkbox.
-   Подумайте о том, как должно вести себя поле текстового ввода, содержащее значение, когда его выбирает пользователь. Поведение по умолчанию подходит для редактирования значений, а не их замены. Точка вставки помещается между словами, и ничего не выделяется. Если замена является наиболее распространенным вариантом использования для данного текстового поля, то можно установить выделение всего текста в поле при получении фокуса элементом управления. Вводимый с клавиатуры текст заменяет выделенный текст.

**Поля ввода из одной строки**

-   Используйте несколько однострочных текстовых полей для сбора различных фрагментов текстовой информации. Если текстовые поля связаны между собой, сгруппируйте их вместе.

-   Делайте однострочные текстовые поля немного шире предполагаемого текста. Если элемент управления получится слишком широким, разделите его на два элемента. Например, можно разделить однострочное поле адреса на "Адрес, строка 1" и "Адрес, строка 2".
-   Задайте максимальную длину вводимого текста в символах. Если базовый источник данных не позволяет вводить длинные строки текста, ограничьте ввод и сообщите пользователю об ограничении с помощью всплывающего окна.
-   Чтобы пользователи имели возможность вводить маленькие фрагменты текста, используйте элементы управления для ввода одной строки.

    В следующем примере показано текстовое поле для записи ответа на вопрос безопасности. Так как предполагается краткий ответ, то здесь вполне подходит поле, состоящее из одной строки.

    ![Ввод простых данных](images/guidelines_and_checklist_for_singleline_text_input_type_text.png)

-   Для ввода данных особого формата используйте набор коротких однострочных элементов управления фиксированного размера.

    ![Ввод данных определенного формата](images/textinput_example_productkey.png)

-   Для ввода или правки текста используйте однострочный элемент управления неограниченного размера в сочетании с кнопкой, нажав на которую, пользователь сможет выбрать допустимое значение.

    ![Ввод данных с выбором](images/textinput_example_assisted.png)


**Элементы управления для ввода многострочного текста**

-   При создании текстового поля в формате RTF следует предусмотреть кнопки стиля и реализовать их действия.
-   Используйте шрифт, соответствующий стилю вашего приложения.
-   Высота элемента управления должна быть достаточной для ввода стандартных записей.
-   При записи больших объемов текста с ограничением количества символов или слов используйте простое текстовое поле и предоставьте динамический счетчик, показывающий пользователю, сколько символов или слов у него осталось до достижения максимального ограничения. Счетчик необходимо создать самостоятельно. Разместите его под текстовым полем и обеспечьте динамическое обновление при вводе пользователем символов или слов.

    ![Длинный текст](images/guidelines_and_checklist_for_multiline_text_input_text_limits.png)

-   При вводе пользователем текста элементы управления не должны увеличиваться по высоте.
-   Не используйте поле для многострочного текста там, где требуется ввести всего одну строку.
-   Не используйте поле с форматом там, где достаточно элемента управления обычного текста.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Текстовые элементы управления](text-controls.md)
- [Руководство по проверке орфографии](text-controls.md)
- [Добавление поиска](https://msdn.microsoft.com/library/windows/apps/hh465231)
- [Руководство по текстовому вводу](text-controls.md)
- [Класс TextBox](https://msdn.microsoft.com/library/windows/apps/br209683)
- [Класс PasswordBox](https://msdn.microsoft.com/library/windows/apps/br227519)
- [Свойство String.Length](https://msdn.microsoft.com/library/system.string.length(v=vs.110).aspx)
