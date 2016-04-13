---
ms.assetid: DA562509-D893-425A-AAE6-B2AE9E9F8A19
label: Блок текста
template: detail.hbs
---
# Блок текста
 Блок текста — это основной элемент управления для отображения текста в режиме "только для чтения" в приложениях. Его можно использовать для отображения однострочного или многострочного текста, строковых гиперссылок и текста с полужирным шрифтом, курсивом или подчеркиванием.

<span class="sidebar_heading" style="font-weight: bold;">Важные API</span>

-   [**Класс TextBlock**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.aspx)
-   [**Свойство Text**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.text.aspx)
-   [**Свойство Inlines**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.inlines.aspx)

## Выбор правильного элемента управления

Блок текста обычно проще использовать, и он обеспечивает лучшую отрисовку текста по сравнению с блоком отформатированного текста, поэтому он предпочтителен для текста пользовательского интерфейса в большинстве приложений. Вы можете легко получить доступ к тексту и использовать его из блока текста в приложении, получив значение свойства [Text](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.text.aspx). Кроме того, он предоставляет множество таких же параметров форматирования для настройки отрисовки текста. 

Хотя в текст можно поместить разрывы строк, блок текста разработан для отображения одного абзаца и не поддерживает отступ текста. Используйте **RichTextBlock**, если вам требуется поддержка нескольких абзацев, разметка текста с несколькими столбцами или другие сложные текстовые макеты либо встроенные элементы пользовательского интерфейса, например изображения.

Дополнительные сведения о выборе подходящего элемента управления текстом см. в статье [Элементы управления текстом](text-controls.md).

## Примеры


## Создание блока текста

Здесь показано, как определить простой элемент управления TextBlock и задать строку в качестве значения его свойства Text.

```xaml
<TextBlock Text="Hello, world!" />
```

```csharp
TextBlock textBlock1 = new TextBlock();
textBlock1.Text = "Hello, world!";
```

    <TextBlock Text="Hello, world!" />

    TextBlock textBlock1 = new TextBlock();
    textBlock1.Text = "Hello, world!";

### Модель содержимого

Есть два свойства, которые можно использовать для добавления содержимого в TextBlock: [Text](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.text.aspx) и [Inlines](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.inlines.aspx).

Наиболее распространенный способ отображения текста — задание свойству Text строкового значения, как показано в предыдущем примере.

Вы также можете добавить содержимое, поместив в свойство TextBox.Inlines элементы встроенного подвижного содержимого следующим образом.
```xaml
<TextBlock><Run>Text can be <Bold>bold</Bold>, <Italic>italic</Italic>, or <Bold><Italic>both</Italic></Bold>.</Run></TextBlock>
```

Элементы, производные от класса Inline, например Bold, Italic, Run, Span и LineBreak, обеспечивают разное форматирование для разных частей текста. Дополнительные сведения см. в разделе [Форматирование текста](). Строковый элемент Hyperlink позволяет вам добавлять гиперссылку в свой текст. Однако, при использовании элементов Inlines также отключается отрисовка текста с помощью быстрого пути, что рассматривается в следующем разделе.


## Вопросы производительности

Для размещения текста XAML использует более эффективный программный путь, когда это возможно. Этот быстрый путь уменьшает общее потребление памяти и значительно уменьшает время, которое необходимо ЦП на измерение и размещение текста. Этот быстрый путь используется только для объекта TextBlock, поэтому по возможности следует использовать этот объект вместо объекта RichTextBlock.

В некоторых условиях для отрисовки текста объекту TextBlock приходится использовать более функциональный программный путь, потребляющий больше ресурсов ЦП. Чтобы функция отрисовки текста всегда использовала быстрый путь, при настройке перечисленных здесь свойств соблюдайте указанные ниже рекомендации.
- [**Text**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.text.aspx): самое важное условие состоит в том, что быстрый путь используется только тогда, когда вы настраиваете свойство Text, явно указав свойство либо в XAML, либо в коде (как показано в предыдущих примерах). Если настроить текст с помощью коллекции Inlines объекта TextBlock (например, с помощью `<TextBlock>Inline text</TextBlock>`), это быстрый путь будет выключен из-за потенциальной сложности, вызванной использованием нескольких форматов.
- [**CharacterSpacing**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.characterspacing.aspx): быстрому пути соответствует только значение 0, используемое по умолчанию.
- [**Typography**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.documents.typography.aspx): быстрому пути соответствуют только значения по умолчанию для различных свойств Typography.
- [**TextTrimming**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.texttrimming.aspx): только значения **None**, **CharacterEllipsis** и **WordEllipsis** соответствуют быстрому пути. Значение **Clip** выключает быстрый путь.
- [**LineStackingStrategy**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.linestackingstrategy.aspx): если для параметра [LineHeight](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.lineheight.aspx) не установлено значение 0, значения **BaselineToBaseline** и **MaxHeight** отключают быстрый путь.
- [**IsTextSelectionEnabled**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textblock.istextselectionenabled.aspx): только значение **false** соответствует быстрому пути. Если задать для этого свойства значение **true**, быстрый путь будет выключен.

Во время отладки для свойства [DebugSettings.IsTextPerformanceVisualizationEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.debugsettings.istextperformancevisualizationenabled.aspx) можно задать значение **true**, чтобы определить, используется ли быстрый путь для отрисовки текста. Если для этого свойства задано значение true, то текст, отрисовываемый с использованием быстрого пути, будет отображаться ярко-зеленым цветом. 

>**Совет**. Эта функция подробно описана в данном семинаре Build 2015 [Производительность XAML: методы для расширения взаимодействия с универсальными приложениями для Windows, созданными с помощью XAML](https://channel9.msdn.com/Events/Build/2015/3-698).

 

В общем случае необходимо задавать параметры отладки в методе [OnLaunched](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.application.onlaunched.aspx), чтобы переопределить страницу кода для файла App.xaml, как показано здесь.
```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.IsTextPerformanceVisualizationEnabled = true;
    }
#endif

// ...

}
```

В этом примере отрисовка первого объекта TextBlock выполняется с использованием быстрого пути, а второго — нет.
```xaml
<StackPanel>
    <TextBlock Text="This text is on the fast path."/>
    <TextBlock>This text is NOT on the fast path.</TextBlock>
<StackPanel/>
```

При запуске этого XAML в режиме отладки, когда для свойства IsTextPerformanceVisualizationEnabled задано значение true, результат выглядит указанным ниже образом.

![Текст, отрисованный в режиме отладки](images/text-block-rendering-performance.png)

>**Осторожно**! Цвет текста, для отрисовки которого не используется быстрый путь, не изменяется. Если в вашем приложении есть текст, для которого указан ярко-зеленый цвет, он будет отображаться ярко-зеленым и в более медленном пути отрисовки. Будьте внимательны, чтобы не спутать текст, для которого в приложении установлен зеленый цвет, с текстом, отрисовываемый с использованием быстрого пути, который зеленый из-за параметров отладки.

## Форматирование текста

Хотя свойство Text хранит обычный текст, к элементу управления TextBlock можно применять различные параметры форматирования для настройки отрисовки текста в приложении. Можно задать стандартные свойства элемента управления, например FontFamily, FontSize, FontStyle, Foreground и CharacterSpacing, чтобы изменить внешний вид текста. Для форматирования текста также можно использовать строковые текстовые элементы и вложенные свойства Typography. Эти параметры влияют только на то, как TextBlock отображает текст локально, поэтому если, например, скопировать и вставить текст в элемент управления для форматированного текста, форматирование не будет применено.

>**Примечание**. Как отмечено в предыдущем разделе, помните о том, что быстрый путь не используется для отрисовки строковых текстовых элементов и нестандартных значений оформления.
 

### Строковые элементы

Пространство имен [Windows.UI.Xaml.Documents](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.documents.aspx) предоставляет разнообразные строковые элементы, которые можно использовать для форматирования текста, например Bold, Italic, Run, Span и LineBreak. 

В объекте TextBlock можно отобразить ряд строк с разным форматированием. Это можно сделать, воспользовавшись элементом Run для отображения каждой строки с ее форматированием и разделив каждый элемент Run с помощью элемента LineBreak.

Ниже показано, как определить несколько по-разному отформатированных текстовых строк в объекте TextBlock, используя объекты Run, разделенные элементами LineBreak.
```xaml
<TextBlock FontFamily="Arial" Width="400" Text="Sample text formatting runs">
    <LineBreak/>
    <Run Foreground="Gray" FontFamily="Courier New" FontSize="24"> 
        Courier New 24 
    </Run>
    <LineBreak/>
    <Run Foreground="Teal" FontFamily="Times New Roman" FontSize="18" FontStyle="Italic"> 
        Times New Roman Italic 18 
    </Run>
    <LineBreak/>
    <Run Foreground="SteelBlue" FontFamily="Verdana" FontSize="14" FontWeight="Bold"> 
        Verdana Bold 14 
    </Run>
</TextBlock>
```

Вот результат.

![Текст, отформатированный с помощью запущенных элементов](images/text-block-run-examples.png)

### Шрифтовое оформление

Вложенные свойства класса [Typography](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.documents.typography.aspx) предоставляют доступ к набору типографских свойств Microsoft OpenType. Эти присоединенные свойства можно задать либо в объекте TextBlock, либо в отдельных строковых текстовых элементах. В этих примерах показаны оба действия.
```xaml
<TextBlock Text="Hello, world!"
           Typography.Capitals="SmallCaps"
           Typography.StylisticSet4="True"/>
```

```csharp
TextBlock textBlock1 = new TextBlock();
textBlock1.Text = "Hello, world!";
Windows.UI.Xaml.Documents.Typography.SetCapitals(textBlock1, FontCapitals.SmallCaps);
Windows.UI.Xaml.Documents.Typography.SetStylisticSet4(textBlock1, true);
```

```xaml
<TextBlock>12 x <Run Typography.Fraction="Slashed">1/3</Run> = 4.</TextBlock>
```

## Рекомендации



\[Эта статья содержит сведения, относящиеся к приложениям универсальной платформы Windows (UWP) и Windows 10. Для получения инструкций по Windows 8.1, скачайте [рекомендации для Windows 8.1 в формате PDF](https://go.microsoft.com/fwlink/p/?linkid=258743).\]

## Связанные статьи

[Элементы управления текстом](text-controls.md)

**Проектировщикам**
- [Руководство по проверке орфографии](spell-checking-and-prediction.md)
- [Добавление поиска](https://msdn.microsoft.com/library/windows/apps/hh465231)
- [Руководство по текстовому вводу](text-controls.md)

**Разработчикам (XAML)**
- [**Класс TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683)
- [**Класс Windows.UI.Xaml.Controls PasswordBox**](https://msdn.microsoft.com/library/windows/apps/br227519)


**Разработчикам (прочее)**
- [Свойство String.Length](https://msdn.microsoft.com/library/system.string.length(v=vs.110).aspx)


<!--HONumber=Mar16_HO1-->


