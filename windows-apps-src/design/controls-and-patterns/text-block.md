---
ms.assetid: DA562509-D893-425A-AAE6-B2AE9E9F8A19
Description: Блок текста — это основной элемент управления для отображения текста в режиме "только для чтения" в приложениях.
title: Блок текста
label: Text block
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 3b69bc093fb9aae6e35618949bf6eebe8c36c893
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66364172"
---
# <a name="text-block"></a>Блок текста

 

 Блок текста — это основной элемент управления для отображения текста в режиме "только для чтения" в приложениях. Его можно использовать для отображения однострочного или многострочного текста, строковых гиперссылок и текста с полужирным шрифтом, курсивом или подчеркиванием.
 
 > **Важные API**: [Класс TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock), [свойство Text](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.text), [свойства Inlines](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.inlines)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Блок текста обычно проще использовать, и он обеспечивает лучшую отрисовку текста по сравнению с блоком отформатированного текста, поэтому он предпочтителен для текста пользовательского интерфейса в большинстве приложений. Вы можете легко получить доступ к тексту и использовать его из блока текста в приложении, получив значение свойства [Text](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.text). Кроме того, он предоставляет множество таких же параметров форматирования для настройки отрисовки текста.

Хотя в текст можно поместить разрывы строк, блок текста разработан для отображения одного абзаца и не поддерживает отступ текста. Используйте **RichTextBlock**, если вам требуется поддержка нескольких абзацев, разметка текста с несколькими столбцами или другие сложные текстовые макеты либо встроенные элементы пользовательского интерфейса, например изображения.

Дополнительные сведения о выборе подходящего элемента управления текстом можно найти в статье [Элементы управления текстом](text-controls.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/TextBlock">открыть приложение и увидеть TextBlock в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-text-block"></a>Создание блока текста

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

### <a name="content-model"></a>Модель содержимого

Существует два свойства, которые можно использовать для добавления содержимого в TextBlock. [Текст](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.text) и [Inlines](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.inlines).

Наиболее распространенный способ отображения текста — задание свойству Text строкового значения, как показано в предыдущем примере.

Вы также можете добавить содержимое, поместив в свойство TextBox.Inlines элементы встроенного подвижного содержимого следующим образом.
```xaml
<TextBlock>Text can be <Bold>bold</Bold>, <Underline>underlined</Underline>, 
    <Italic>italic</Italic>, or a <Bold><Italic>combination</Italic></Bold>.</TextBlock>
```

Элементы, производные от класса Inline, например Bold, Italic, Run, Span и LineBreak, обеспечивают разное форматирование для разных частей текста. Дополнительные сведения см. в разделе [Форматирование текста](#formatting-text). Строковый элемент Hyperlink позволяет вам добавлять гиперссылку в свой текст. Однако, при использовании элементов Inlines также отключается отрисовка текста с помощью быстрого пути, что рассматривается в следующем разделе.


## <a name="performance-considerations"></a>Вопросы производительности

Для размещения текста XAML использует более эффективный программный путь, когда это возможно. Этот быстрый путь уменьшает общее потребление памяти и значительно уменьшает время, которое необходимо ЦП на измерение и размещение текста. Этот быстрый путь используется только для объекта TextBlock, поэтому по возможности следует использовать этот объект вместо объекта RichTextBlock.

В некоторых условиях для отрисовки текста объекту TextBlock приходится использовать более функциональный программный путь, потребляющий больше ресурсов ЦП. Чтобы функция отрисовки текста всегда использовала быстрый путь, при настройке перечисленных здесь свойств соблюдайте указанные ниже рекомендации.
- [Текст](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.text): Самое важное условие является то, что быстрый путь используется только в том случае, если значение текста, явно задав свойство Text, XAML или в коде (как показано в предыдущих примерах). Если настроить текст с помощью коллекции Inlines объекта TextBlock (например, с помощью `<TextBlock>Inline text</TextBlock>`), это быстрый путь будет выключен из-за потенциальной сложности, вызванной использованием нескольких форматов.
- [CharacterSpacing](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.characterspacing): Только значение по умолчанию 0 — оптимальный маршрут.
- [TextTrimming](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.texttrimming): Только **None**, **CharacterEllipsis**, и **WordEllipsis** значения: быстрого пути. Значение **Clip** выключает быстрый путь.

> **Примечание.** &nbsp;&nbsp;В версиях Windows, предшествующих Windows 10 версии 1607, дополнительные свойства также влияют на быстрый путь. При запуске приложения в более ранней версии Windows эти условия также приведут к обработке текста по медленному пути. Дополнительные сведения о версиях см. в статье «Адаптивный к версии код».
- [Оформление](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Typography): Только значения по умолчанию для различных свойств оформления, быстрого пути.
- [LineStackingStrategy](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.linestackingstrategy): Если [LineHeight](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.lineheight) является не равен 0, **BaselineToBaseline** и **MaxHeight** значения Отключение быстрого пути.
- [IsTextSelectionEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.istextselectionenabled): Только **false** — оптимальный маршрут. Если задать для этого свойства значение **true**, быстрый путь будет выключен.

Во время отладки для свойства [DebugSettings.IsTextPerformanceVisualizationEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.debugsettings.istextperformancevisualizationenabled) можно задать значение **true**, чтобы определить, используется ли быстрый путь для отрисовки текста. Если для этого свойства задано значение true, то текст, отрисовываемый с использованием быстрого пути, будет отображаться ярко-зеленым цветом.

>**Совет**&nbsp;&nbsp;эта функция подробное объяснение в этом сеансе из сборки 2015 - [производительности XAML: Методики по достижению максимальной опыт приложение универсальной Windows, созданных с помощью XAML](https://channel9.msdn.com/Events/Build/2015/3-698).



В общем случае необходимо задавать параметры отладки в методе [OnLaunched](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onlaunched), чтобы переопределить страницу кода для файла App.xaml, как показано здесь.
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

>**Внимание**&nbsp;&nbsp;Цвет текста, для отрисовки которого не используется быстрый путь, не изменяется. Если в вашем приложении есть текст, для которого указан ярко-зеленый цвет, он будет отображаться ярко-зеленым и в более медленном пути отрисовки. Будьте внимательны, чтобы не спутать текст, для которого в приложении установлен зеленый цвет, с текстом, отрисовываемый с использованием быстрого пути, который зеленый из-за параметров отладки.

## <a name="formatting-text"></a>Форматирование текста

Хотя свойство Text хранит обычный текст, к элементу управления TextBlock можно применять различные параметры форматирования для настройки отрисовки текста в приложении. Можно задать стандартные свойства элемента управления, например FontFamily, FontSize, FontStyle, Foreground и CharacterSpacing, чтобы изменить внешний вид текста. Для форматирования текста также можно использовать строковые текстовые элементы и вложенные свойства Typography. Эти параметры влияют только на то, как TextBlock отображает текст локально, поэтому если, например, скопировать и вставить текст в элемент управления для форматированного текста, форматирование не будет применено.

>**Примечание.** &nbsp;&nbsp;Как отмечено в предыдущем разделе, помните о том, что быстрый путь не используется для отрисовки строковых текстовых элементов и нестандартных значений оформления.


### <a name="inline-elements"></a>Строковые элементы

Пространство имен [Windows.UI.Xaml.Documents](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents) предоставляет разнообразные строковые элементы, которые можно использовать для форматирования текста, например Bold, Italic, Run, Span и LineBreak.

В объекте TextBlock можно отобразить ряд строк с разным форматированием. Это можно сделать, воспользовавшись элементом Run для отображения каждой строки с ее форматированием и разделив каждый элемент Run с помощью элемента LineBreak.

Ниже показано, как определить несколько по-разному отформатированных текстовых строк в объекте TextBlock, используя объекты Run, разделенные элементами LineBreak.
```xaml
<TextBlock FontFamily="Segoe UI" Width="400" Text="Sample text formatting runs">
    <LineBreak/>
    <Run Foreground="Gray" FontFamily="Segoe UI Light" FontSize="24">
        Segoe UI Light 24
    </Run>
    <LineBreak/>
    <Run Foreground="Teal" FontFamily="Georgia" FontSize="18" FontStyle="Italic">
        Georgia Italic 18
    </Run>
    <LineBreak/>
    <Run Foreground="Black" FontFamily="Arial" FontSize="14" FontWeight="Bold">
        Arial Bold 14
    </Run>
</TextBlock>
```

Вот результат.

![Текст, отформатированный с помощью запущенных элементов](images/text-block-run-examples.png)

### <a name="typography"></a>Оформление

Вложенные свойства класса [Typography](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Typography) предоставляют доступ к набору типографских свойств Microsoft OpenType. Эти присоединенные свойства можно задать либо в объекте TextBlock, либо в отдельных строковых текстовых элементах. В этих примерах показаны оба действия.
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

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Связанные статьи

- [Текстовые элементы управления](text-controls.md)
- [Рекомендации для проверки орфографии](text-controls.md)
- [Добавление поиска](search.md)
- [Рекомендации для ввода текста](text-controls.md)
- [Класс TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)
- [Класс Windows.UI.Xaml.Controls PasswordBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox)
- [Свойство String.Length](https://msdn.microsoft.com/library/system.string.length(v=vs.110).aspx)
