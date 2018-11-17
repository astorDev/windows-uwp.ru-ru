---
author: jwmsft
Description: Use content links to embed rich data in your text controls.
title: Ссылки на содержимое в элементах управления текстом
label: Content links
template: detail.hbs
ms.author: jimwalk
ms.date: 03/07/2018
ms.topic: article
keywords: Windows10, UWP
pm-contact: miguelrb
design-contact: ''
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 3939995aa2f29f4590c8c71a877b69f0cb81d2ec
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/16/2018
ms.locfileid: "7146746"
---
# <a name="content-links-in-text-controls"></a>Ссылки на содержимое в элементах управления текстом

Ссылки на содержимое позволяют вставлять в текстовые элементы управления форматированные данные. Благодаря этому пользователь может находить и использовать больше информации о людях и местах, не покидая вашего приложения.

Когда пользователь использует символ амперсанд (@) перед записью в RichEditBox, отображается список людей и/или мест, соответствующих записи. Затем, например, когда пользователь выбирает место, ContentLink для этого места вставляется в текст. Когда пользователь вызывает ссылку на содержимое из RichEditBox, отображается всплывающий элемент с картой и дополнительными сведениями о месте.

> **Важные API-интерфейсы**: [класс ContentLink](/uwp/api/windows.ui.xaml.documents.contentlink), [класс ContentLinkInfo](/uwp/api/windows.ui.text.contentlinkinfo), [класс RichEditTextRange](/uwp/api/windows.ui.text.richedittextrange)

> [!NOTE]
> API-интерфейсы для ссылок на содержимое распределены следующие пространства имен: Windows.UI.Xaml.Controls, Windows.UI.Xaml.Documents и Windows.UI.Text.



## <a name="content-links-in-rich-edit-vs-text-block-controls"></a>Ссылки на содержимое в полях с форматом и элементы управления текстовыми блоками

Существует два различных способа использования ссылок на содержимое:

1. В [RichEditBox](/uwp/api/windows.ui.xaml.controls.richeditbox) пользователь может открыть средство выбора для добавления ссылки на содержимое путем размещения символа @ перед текстом. Ссылка на содержимое хранится как часть содержимого форматированного текста.
1. В [TextBlock](/uwp/api/windows.ui.xaml.controls.textblock) или [RichTextBlock](/uwp/api/windows.ui.xaml.controls.richtextblock) ссылка на содержимое является текстовым элементом, который можно использовать как элемент [Hyperlink](/uwp/api/windows.ui.xaml.documents.hyperlink).

Вот как ссылки на содержимое выглядят по умолчанию в элементах RichEditBox и TextBlock.

![ссылка на содержимое в поле с форматом](images/content-link-default-richedit.png)
![ссылка на содержимое в текстовом блоке](images/content-link-default-textblock.png)

Отличия в использовании, отображении и поведении подробно описаны в следующих разделах. Эта таблица содержит краткое сравнение основных различий между ссылкой на содержимое в элементе RichEditBox и текстовом блоке.

| Компонент   | Элемент управления RichEditBox | Текстовый блок |
| --------- | ----------- | ---------- |
| Usage (Использование) | Экземпляр ContentLinkInfo | Текстовый элемент ContentLink |
| Cursor (Курсор) | Определяется типом ссылки на содержимое; изменение невозможно | Определяется свойством Cursor; значение **null** по умолчанию |
| ToolTip | Не отображается | Отображает дополнительный текст |

## <a name="enable-content-links-in-a-richeditbox"></a>Включение ссылок на содержимое в элементе RichEditBox

Наиболее распространенная практика использования ссылки на содержимое — это предоставление пользователю возможности быстро добавлять информацию путем постановки символа амперсанда (@) перед именем человека или названием места в тексте. При включении ссылок на содержимое в элементе [RichEditBox](/uwp/api/windows.ui.xaml.controls.richeditbox) открывается средство выбора, которое позволяет пользователю вставить человека из списка контактов или ближайшее место, в зависимости от того, какая функция была включена.

Ссылку на содержимое можно сохранить с содержимым форматированного текста, и можно извлечь ее для использования в других частях приложения. Например, в приложении для работы с электронной почтой можно извлечь данные о человеке использовать их, чтобы заполнить поле "Кому:" адресом электронной почты.

> [!NOTE]
> Средство выбора для ссылок на содержимое — это приложение, которое входит в состав Windows, поэтому оно запускается в отдельном процессе из вашего приложения.

### <a name="content-link-providers"></a>Поставщики ссылок на содержимое

Включение ссылок на содержимое в элементе RichEditBox осуществляется путем добавления одного или нескольких поставщиков ссылок на содержимое в коллекцию [RichEditBox.ContentLinkProviders](/uwp/api/windows.ui.xaml.controls.richeditbox.ContentLinkProviders). Существует 2 поставщика ссылок на содержимое, которые встроены в платформу XAML.

- [ContactContentLinkProvider](/uwp/api/windows.ui.xaml.documents.contactcontentlinkprovider) — поиск контактов с помощью приложения **Люди**.
- [PlaceContentLinkProvider](/uwp/api/windows.ui.xaml.documents.placecontentlinkprovider) — поиск мест с помощью приложения **Карты**.

> [!IMPORTANT]
> Значением по умолчанию для свойства RichEditBox.ContentLinkProviders является **null**, а не пустая коллекция. Перед добавлением поставщиков ссылок на содержимое необходимо явно создать коллекцию [ContentLinkProviderCollection](/uwp/api/windows.ui.xaml.documents.contentlinkprovidercollection).

Вот как добавлять поставщиков ссылок на содержимое в XAML-код.

```xaml
<RichEditBox>
    <RichEditBox.ContentLinkProviders>
        <ContentLinkProviderCollection>
            <ContactContentLinkProvider/>
            <PlaceContentLinkProvider/>
        </ContentLinkProviderCollection>
    </RichEditBox.ContentLinkProviders>
</RichEditBox>
```

Можно также добавить поставщиков ссылок на содержимое в элемент Style и применить его к нескольким элементам RichEditBoxes следующим образом.

```xaml
<Page.Resources>
    <Style TargetType="RichEditBox" x:Key="ContentLinkStyle">
        <Setter Property="ContentLinkProviders">
            <Setter.Value>
                <ContentLinkProviderCollection>
                    <PlaceContentLinkProvider/>
                    <ContactContentLinkProvider/>
                </ContentLinkProviderCollection>
            </Setter.Value>
        </Setter>
    </Style>
</Page.Resources>

<RichEditBox x:Name="RichEditBox01" Style="{StaticResource ContentLinkStyle}" />
<RichEditBox x:Name="RichEditBox02" Style="{StaticResource ContentLinkStyle}" />
```

Вот как добавлять поставщиков ссылок на содержимое в код.

```csharp
RichEditBox editor = new RichEditBox();
editor.ContentLinkProviders = new ContentLinkProviderCollection
{
    new ContactContentLinkProvider(),
    new PlaceContentLinkProvider()
};
```

### <a name="content-link-colors"></a>Цвета ссылок на содержимое

Внешний вид ссылки на содержимое определяется ее передним планом, фоном и значком. В элементе RichEditBox можно задать свойства [ContentLinkForegroundColor](/uwp/api/windows.ui.xaml.controls.richeditbox.ContentLinkForegroundColor) и [ContentLinkBackgroundColor](/uwp/api/windows.ui.xaml.controls.richeditbox.ContentLinkBackgroundColor), чтобы изменить цвета по умолчанию. 

Цвет курсора задать нельзя. Курсор отрисовывается элементом RichEditbox в зависимости от типа ссылки на содержимое. [Person](/uwp/api/windows.ui.core.corecursortype) — курсор для ссылки на человека или [Pin](/uwp/api/windows.ui.core.corecursortype) — курсор для ссылки на место.

### <a name="the-contentlinkinfo-object"></a>Объект ContentLinkInfo

Когда пользователь выбирает человека или место с помощью средства выбора, система создает объект [ContentLinkInfo](/uwp/api/windows.ui.text.contentlinkinfo) и добавляет его в свойство **ContentLinkInfo** текущего элемента [RichEditTextRange ](/uwp/api/windows.ui.text.richedittextrange).

Объект ContentLinkInfo содержит сведения, используемые для отображения, вызова и администрирования ссылки на содержимое.

- **DisplayText** — это строка, которая отображается при отображении ссылки на содержимое. В RichEditBox пользователь может редактировать текст ссылки на содержимое после его создания, которое изменяет значение этого свойства.
- **SecondaryText** — эта строка отображается в ToolTip отображенной ссылки на содержимое.
  - В ссылке на содержимое Place, созданной с помощью средства выбора, содержится адрес места (если он доступен).
- **URI** — ссылка на дополнительные сведения об объекте ссылки на содержимое. Этот URI может открыть установленное приложение или веб-сайт.
- **Id** — это нередактируемый параметр, который используется для каждого элемента управления и создается счетчиком элемента управления RichEditBox. Он позволяет отслеживать сведения ContentLinkInfo во время выполнения действий, таких как удаление или изменение. При вырезании и вставке сведений ContentLinkInfo обратно в элемент управления, он получает новый идентификатор. Значения идентификаторов являются приращиваемыми.
- **LinkContentKind** — строка, которая описывает тип ссылки на содержимое. Встроенные типы содержимого: _Places_ и _Contacts_. Значение обрабатывается с учетом регистра.

#### <a name="link-content-kind"></a>Тип ссылки на содержимое

Существует несколько ситуаций, когда важен элемент LinkContentKind.

- Когда пользователь копирует ссылку на содержимое из RichEditBox и вставляет ее в другой элемент RichEditBox, у обоих элементов управления должен быть ContentLinkProvider для данного типа содержимого. В противном случае ссылка вставляется в виде текста.
- LinkContentKind можно использовать в обработчике событий [ContentLinkChanged](/uwp/api/windows.ui.xaml.controls.richeditbox.ContentLinkChanged), чтобы определить, что следует делать с ссылкой на содержимое при ее использовании в других частях приложения. См. дополнительные сведения в разделе "Пример".
- LinkContentKind влияет на то, как система открывает URI при вызове ссылки. Это будет рассмотрено далее, в обсуждении запуска URI.

#### <a name="uri-launching"></a>Запуск URI

Свойство URI работает как свойство NavigateUri гиперссылки. Когда пользователь щелкает его, происходит запуск URI в браузере по умолчанию или в приложении, которое зарегистрировано для конкретного протокола, указанного в значении URI.

Здесь описываются особенности поведения двух встроенных типов ссылок на содержимое.

##### <a name="places"></a>Places (Места)

Средство выбора Places создает ContentLinkInfo с корнем URI: https://maps.windows.com/. Эту ссылку можно открыть тремя способами:

- Если LinkContentKind = "Places", открывается карточка сведений во всплывающем элементе. Всплывающий элемент аналогичен всплывающему элементу средства выбора ссылки на содержимое. Он является частью Windows и запускается в виде отдельного процесса из приложения.
- Если значением LinkContentKind не является "Places", происходит попытка открыть приложение **Карты** в указанном расположении. Например, это может произойти, если вы изменили LinkContentKind в обработчике событий ContentLinkChanged.
- Если не удается открыть URI в приложении "Карты", карта открывается в браузере по умолчанию. Как правило, это происходит, когда пользовательские параметры раздела _Приложения для веб-сайтов_ запрещают открытие URI с помощью приложения **Карты**.

##### <a name="people"></a>People (Люди)

Средство выбора People создает ContentLinkInfo с URI, который использует протокол **ms-people**.

- Если LinkContentKind = "People", открывается карточка сведений во всплывающем элементе. Всплывающий элемент аналогичен всплывающему элементу средства выбора ссылки на содержимое. Он является частью Windows и запускается в виде отдельного процесса из приложения.
- Если значение LinkContentKind отлично от "People", открывается приложение **Люди**. Например, это может произойти, если вы изменили LinkContentKind в обработчике событий ContentLinkChanged.

> [!TIP]
> Дополнительные сведения об открытии других приложений и веб-сайтов из вашего приложения см. в разделах [Запуск приложения с помощью Uri](/windows/uwp/launch-resume/launch-app-with-uri).

#### <a name="invoked"></a>Событие Invoked (вызов)

Когда пользователь вызывает ссылку на содержимое, событие [ContentLinkInvoked](/uwp/api/windows.ui.xaml.controls.richeditbox.ContentLinkInvoked) создается до возникновения поведения по умолчанию — то есть запуска URI. Вы можете обработать это событие, чтобы переопределить или отменить поведение по умолчанию.

В этом примере показано, как переопределить поведение запуска по умолчанию для ссылки на содержимое Place. Вместо открытия карты в карточке сведений Place, приложении "Карты" или веб-браузере по умолчанию можно пометить событие как Handled и открыть карту во встроенном в приложение элементе управления [WebView](/uwp/api/windows.ui.xaml.controls.webview).

```xaml
<RichEditBox x:Name="editor"
             ContentLinkInvoked="editor_ContentLinkInvoked">
    <RichEditBox.ContentLinkProviders>
        <ContentLinkProviderCollection>
            <PlaceContentLinkProvider/>
        </ContentLinkProviderCollection>
    </RichEditBox.ContentLinkProviders>
</RichEditBox>

<WebView x:Name="webView1"/>
```

```csharp
private void editor_ContentLinkInvoked(RichEditBox sender, ContentLinkInvokedEventArgs args)
{
    if (args.ContentLinkInfo.LinkContentKind == "Places")
    {
        args.Handled = true;
        webView1.Navigate(args.ContentLinkInfo.Uri);
    }
}
```

#### <a name="contentlinkchanged"></a>ContentLinkChanged

Событие [ContentLinkChanged](/uwp/api/windows.ui.xaml.controls.richeditbox.ContentLinkChanged) можно использовать, чтобы получать уведомление при добавлении, изменении или удалении ссылки на содержимое. Это позволяет извлекать ссылку на содержимое из текста и использовать ее в других местах в вашем приложении. Об этом рассказывается далее в разделе "Примеры".

Свойства [ContentLinkChangedEventArgs](/uwp/api/windows.ui.xaml.controls.contentlinkchangedeventargs):

- **ChangedKind** — это перечисление ContentLinkChangeKind, которое является действием в ContentLink. Например, этим действием может быть вставка, удаление или изменение ContentLink.
- **Info** — элемент ContentLinkInfo, который был целью действия.

Это событие вызывается для каждого действия ContentLinkInfo. Например, если пользователь копирует и вставляет одновременно несколько ссылок на содержимое в RichEditBox, это событие вызывается для каждого добавленного элемента. Или если пользователь выбирает и удаляет несколько ссылок на содержимое одновременно, это событие вызывается для каждого удаленного элемента.

Это событие вызывается для RichEditBox после вызова события **TextChanging** и перед вызовом события **TextChanged**.

#### <a name="enumerate-content-links-in-a-richeditbox"></a>Перечисление ссылок на содержимое в элементе RichEditBox

Свойство RichEditTextRange.ContentLink можно использовать для получения ссылки на содержимое из документа с форматированием. Перечисление TextRangeUnit имеет значение ContentLink, чтобы указать ссылку на содержимое в качестве единицы при перемещении по диапазону текста.

В этом примере показано, как можно перечислить все ссылки на содержимое в RichEditBox и извлечь людей в список.

```xaml
<StackPanel Width="300">
    <RichEditBox x:Name="Editor" Height="200">
        <RichEditBox.ContentLinkProviders>
            <ContentLinkProviderCollection>
                <ContactContentLinkProvider/>
                <PlaceContentLinkProvider/>
            </ContentLinkProviderCollection>
        </RichEditBox.ContentLinkProviders>
    </RichEditBox>

    <Button Content="Get people" Click="Button_Click"/>

    <ListView x:Name="PeopleList" Header="People">
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="ContentLinkInfo">
                <TextBlock>
                    <ContentLink Info="{x:Bind}" Background="Transparent"/>
                </TextBlock>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackPanel>
```

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    PeopleList.Items.Clear();
    RichEditTextRange textRange = Editor.Document.GetRange(0, 0) as RichEditTextRange;

    do
    {
        // The Expand method expands the Range EndPosition 
        // until it finds a ContentLink range. 
        textRange.Expand(TextRangeUnit.ContentLink);
        if (textRange.ContentLinkInfo != null
            && textRange.ContentLinkInfo.LinkContentKind == "People")
        {
            PeopleList.Items.Add(textRange.ContentLinkInfo);
        }
    } while (textRange.MoveStart(TextRangeUnit.ContentLink, 1) > 0);
}
```

## <a name="contentlink-text-element"></a>Текстовый элемент ContentLink

Для применения ссылки на содержимое в элементах управления TextBlock или RichTextBlock используется текстовый элемент [ContentLink](/uwp/api/windows.ui.xaml.documents.contentlink) (из пространства имен Windows.UI.Xaml.Documents).

Ниже перечислены стандартные источники для ContentLink в текстовом блоке.

- Ссылка на содержимое, созданная с помощью средства выбора и извлеченная из элемента управления RichTextBlock.
- Ссылка на содержимое для места, создаваемого в коде.

В других случаях, как правило, лучше использовать текстовый элемент Hyperlink.

### <a name="contentlink-appearance"></a>Внешний вид ContentLink

Внешний вид ссылки на содержимое определяется ее передним планом, фоном и курсором. В текстовом блоке можно задать свойства Foreground (из TextElement) и [Background](/uwp/api/windows.ui.xaml.documents.contentlink.background), чтобы изменить цвета по умолчанию.

По умолчанию курсор [Hand](/uwp/api/windows.ui.core.corecursortype) отображается в том случае, когда пользователь наводит его на ссылку на содержимое. В отличие от RichEditBlock элементы управления текстовыми блоками не изменяют курсор автоматически в зависимости от типа ссылки. Свойство [Cursor](/uwp/api/windows.ui.xaml.documents.contentlink.Cursor) можно настроить для изменения типа курсора в зависимости от типа ссылки или других факторов.

Вот пример ContentLink в элементе TextBlock. ContentLinkInfo создается в коде и назначается свойству Info текстового элемента ContentLink, созданного в XAML.

```xaml
<StackPanel>
    <TextBlock>
        <Span xml:space="preserve">
            <Run>This valcano erupted in 1980: </Run><ContentLink x:Name="placeContentLink" Cursor="Pin"/>
            <LineBreak/>
        </Span>
    </TextBlock>

    <Button Content="Show" Click="Button_Click"/>
</StackPanel>
```

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    var info = new Windows.UI.Text.ContentLinkInfo();
    info.DisplayText = "Mount St. Helens";
    info.SecondaryText = "Washington State";
    info.LinkContentKind = "Places";
    info.Uri = new Uri("https://maps.windows.com?cp=46.1912~-122.1944");
    placeContentLink.Info = info;
}
```

> [!TIP]
> При использовании ContentLink в текстовом элементе управления в XAML разместите содержимое в контейнере [Span](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.span.aspx) и примените атрибут `xml:space="preserve"` к Span для сохранения пробела между ContentLink и другими элементами.

## <a name="examples"></a>Примеры.

В этом примере пользователь может добавить в RickTextBlock ссылку на содержимое о человеке или месте. Обработка события ContentLinkChanged осуществляется для извлечения ссылок на содержимое и сохранения их в актуальном состоянии в списке людей или в списке мест.

В шаблонах элементов для списков TextBlock используется с текстовым элементом ContentLink для отображения сведений ссылки на содержимое. Элемент ListView задает собственный фон для каждого элемента, поэтому для элемента ContentLink задан фон Transparent (прозрачный).

```xaml
<StackPanel Orientation="Horizontal" Grid.Row="1">
    <RichEditBox x:Name="Editor"
                 ContentLinkChanged="Editor_ContentLinkChanged"
                 Margin="20" Width="300" Height="200"
                 VerticalAlignment="Top">
        <RichEditBox.ContentLinkProviders>
            <ContentLinkProviderCollection>
                <ContactContentLinkProvider/>
                <PlaceContentLinkProvider/>
            </ContentLinkProviderCollection>
        </RichEditBox.ContentLinkProviders>
    </RichEditBox>

    <ListView x:Name="PeopleList" Header="People">
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="ContentLinkInfo">
                <TextBlock>
                    <ContentLink Info="{x:Bind}"
                                 Background="Transparent"
                                 Cursor="Person"/>
                </TextBlock>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>

    <ListView x:Name="PlacesList" Header="Places">
        <ListView.ItemTemplate>
            <DataTemplate x:DataType="ContentLinkInfo">
                <TextBlock>
                    <ContentLink Info="{x:Bind}"
                                 Background="Transparent"
                                 Cursor="Pin"/>
                </TextBlock>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>
</StackPanel>
```

```csharp
private void Editor_ContentLinkChanged(RichEditBox sender, ContentLinkChangedEventArgs args)
{
    var info = args.ContentLinkInfo;
    var change = args.ChangeKind;
    ListViewBase list = null;

    // Determine whether to update the people or places list.
    if (info.LinkContentKind == "People")
    {
        list = PeopleList;
    }
    else if (info.LinkContentKind == "Places")
    {
        list = PlacesList;
    }

    // Determine whether to add, delete, or edit.
    if (change == ContentLinkChangeKind.Inserted)
    {
        Add();
    }
    else if (args.ChangeKind == ContentLinkChangeKind.Removed)
    {
        Remove();
    }
    else if (change == ContentLinkChangeKind.Edited)
    {
        Remove();
        Add();
    }

    // Add content link info to the list. It's bound to the
    // Info property of a ContentLink in XAML.
    void Add()
    {
        list.Items.Add(info);
    }

    // Use ContentLinkInfo.Id to find the item,
    // then remove it from the list using its index.
    void Remove()
    {
        var items = list.Items.Where(i => ((ContentLinkInfo)i).Id == info.Id);
        var item = items.FirstOrDefault();
        var idx = list.Items.IndexOf(item);

        list.Items.RemoveAt(idx);
    }
}
```
