---
Description: Использование RichTextBlock с элементами RichTextBlockOverflow позволяет создавать сложные текстовые макеты.
title: RichTextBlock
ms.assetid: E4BE4B1B-418E-4075-88F1-22C09DDF8E45
label: Rich text block
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 9f90ce11c9a35200121722f3b0a0ac7d7047e819
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80081559"
---
# <a name="rich-text-block"></a>Блок форматированного текста

 

Блоки форматированного текста предоставляют ряд функций для создания расширенного текстового макета, который можно использовать, чтобы обеспечить поддержку абзацев, встроенных элементов пользовательского интерфейса или сложных текстовых макетов.

> **API платформы**: [класс RichTextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock), [класс RichTextBlockOverflow](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlockOverflow), [класс Paragraph](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Paragraph), [класс Typography](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Typography).

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте **RichTextBlock**, если вам требуется поддержка нескольких абзацев, разметка с несколькими столбцами или другие сложные текстовые макеты либо встроенные элементы пользовательского интерфейса, например изображения.

Для отображения в приложении текста «только для чтения», как правило, используется **TextBlock**. Его можно использовать для отображения однострочного или многострочного текста, строковых гиперссылок и текста с полужирным шрифтом, курсивом или подчеркиванием. TextBlock предоставляет более простую модель содержимого, поэтому данный элемент обычно проще использовать и он может обеспечить лучшую отрисовку текста по сравнению с элементом RichTextBlock. Это предпочтительный способ для большинства текстов пользовательского интерфейса приложения. Хотя в текст можно поместить разрывы строк, элемент TextBlock разработан для отображения одного абзаца и не поддерживает отступ текста.

Дополнительные сведения можно найти в статье об [элементах управления текстом](text-controls.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/RichTextBlock">открыть приложение и увидеть RichTextBlock в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-rich-text-block"></a>Создание блока форматированного текста

Свойство содержимого RichTextBlock представляет собой свойство [Blocks](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richtextblock.blocks), которое поддерживает текст на основе абзаца с помощью элемента [Paragraph](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Paragraph). У него нет свойства **Text**, которое можно использовать для удобного доступа к текстовому содержимому элементов управления в приложении. Однако RichTextBlock предусматривает несколько уникальных возможностей, которые недоступны с элементом TextBlock. 

RichTextBlock поддерживает следующие функции.
- Несколько абзацев. Установите отступ для абзацев, задав свойство [TextIndent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richtextblock.textindent).
- Строковые элементы пользовательского интерфейса. Используйте [InlineUIContainer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.InlineUIContainer), чтобы отобразить элементы пользовательского интерфейса, например изображения, встроенными в текст.
- Контейнеры переполнения. Используйте элементы [RichTextBlockOverflow](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlockOverflow), чтобы создать текстовые макеты с несколькими столбцами.

### <a name="paragraphs"></a>Абзацы

Элементы [Paragraph](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Paragraph) используются для определения текстовых блоков, которые будут отображаться внутри элемента управления RichTextBlock. Каждый элемент RichTextBlock должен включать хотя бы один элемент Paragraph. 

Можно установить отступ для всех абзацев в элементе RichTextBlock, задав свойство [RichTextBlock.TextIndent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richtextblock.textindent). Этот параметр можно переопределить для конкретных абзацев в элементе RichTextBlock, задав для свойства [Paragraph.TextIndent](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.paragraph.textindent) другое значение.

```xaml
<RichTextBlock TextIndent="12">
  <Paragraph TextIndent="24">First paragraph.</Paragraph>
  <Paragraph>Second paragraph.</Paragraph>
  <Paragraph>Third paragraph. <Bold>With an inline.</Bold></Paragraph>
</RichTextBlock>
```

### <a name="inline-ui-elements"></a>Строковые элементы пользовательского интерфейса

Класс [InlineUIContainer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.InlineUIContainer) позволяет встроить в текст любой элемент UIElement. Распространенный сценарий — вставка элемента Image в текст, но можно использовать и интерактивные элементы, такие как Button или CheckBox.

Если в одно и то же место нужно встроить несколько элементов, используйте панель в качестве отдельного дочернего элемента InlineUIContainer, а затем поместите несколько элементов на эту панель.

В этом примере показано, как использовать InlineUIContainer для вставки изображения в RichTextBlock. 

```xaml
<RichTextBlock>
    <Paragraph>
        <Italic>This is an inline image.</Italic>
        <InlineUIContainer>
            <Image Source="Assets/Square44x44Logo.png" Height="30" Width="30"/>
        </InlineUIContainer>
        Mauris auctor tincidunt auctor.
    </Paragraph>
</RichTextBlock>
```

## <a name="overflow-containers"></a>Контейнеры переполнения

Вы можете использовать RichTextBlock с элементами [RichTextBlockOverflow](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlockOverflow), чтобы создать многостолбцовые или другие сложные макеты страниц. Содержимое элемента RichTextBlockOverflow всегда берется из элемента RichTextBlock. Элементы RichTextBlockOverflow связываются путем их настройки как OverflowContentTarget объекта RichTextBlock или другого элемента RichTextBlockOverflow.

Вот простой пример, создающий макет с двумя столбцами. Более сложные примеры см. в разделе "Примеры".

```xaml
<Grid>
    <Grid.ColumnDefinitions>
        <ColumnDefinition/>
        <ColumnDefinition/>
    </Grid.ColumnDefinitions>
    <RichTextBlock Grid.Column="0" 
                   OverflowContentTarget="{Binding ElementName=overflowContainer}" >
        <Paragraph>
            Proin ac metus at quam luctus ultricies.
        </Paragraph>
    </RichTextBlock>
    <RichTextBlockOverflow x:Name="overflowContainer" Grid.Column="1"/>
</Grid>
```

## <a name="formatting-text"></a>Форматирование текста

Хотя RichTextBlock содержит обычный текст, для настройки отрисовки текста в приложении можно применять различные параметры форматирования. Можно задать стандартные свойства элемента управления, например FontFamily, FontSize, FontStyle, Foreground и CharacterSpacing, чтобы изменить внешний вид текста. Для форматирования текста также можно использовать строковые текстовые элементы и вложенные свойства Typography. Эти параметры влияют только на то, как RichTextBlock отображает текст локально, поэтому если, например, скопировать и вставить текст в поле с форматом, форматирование не будет применено.

### <a name="inline-elements"></a>Строковые элементы

Пространство имен [Windows.UI.Xaml.Documents](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents) предоставляет разнообразные строковые элементы, которые можно использовать для форматирования текста, например Bold, Italic, Run, Span и LineBreak. Типичный способ применить форматирование к отдельным частям текста — это поместить текст в элемент Run или Span и задать свойства этого элемента.

Вот элемент Paragraph, первая фраза которого отображается полужирным шрифтом 16 пт в синем цвете.

```xaml
<Paragraph>
    <Bold><Span Foreground="DarkSlateBlue" FontSize="16">Lorem ipsum dolor sit amet</Span></Bold>
    , consectetur adipiscing elit.
</Paragraph>
```

### <a name="typography"></a>Шрифтовое оформление

Вложенные свойства класса [Typography](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Typography) предоставляют доступ к набору типографских свойств Microsoft OpenType. Эти подключенные свойства можно задать либо в объекте RichTextBlock, либо в отдельных строковых текстовых элементах, как показано здесь.

```xaml
<RichTextBlock Typography.StylisticSet4="True">
    <Paragraph>
        <Span Typography.Capitals="SmallCaps">Lorem ipsum dolor sit amet</Span>
        , consectetur adipiscing elit.
    </Paragraph>
</RichTextBlock>
```

## <a name="recommendations"></a>Рекомендации

См. руководство по типографии и шрифтам.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи

[Текстовые элементы управления](text-controls.md)

**Проектировщикам**
- [Руководство по проверке орфографии](text-controls.md)
- [Добавление поиска](https://docs.microsoft.com/previous-versions/windows/apps/hh465231(v=win.10))
- [Руководство по текстовому вводу](text-controls.md)

**Разработчикам (XAML)**
- [Класс TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)
- [Класс Windows.UI.Xaml.Controls PasswordBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox)


**Разработчикам (прочее)**
- [Свойство String.Length](https://docs.microsoft.com/dotnet/api/system.string.length)
