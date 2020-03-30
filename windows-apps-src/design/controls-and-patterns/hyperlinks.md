---
Description: Гиперссылки используются для перехода в другую часть приложения, в другое приложение либо по указанному универсальному коду ресурса (URI) в отдельном приложении браузера.
title: Гиперссылки
ms.assetid: 74302FF0-65FC-4820-B59A-718A765EF7F0
label: Hyperlinks
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: kimsea
dev-contact: stpete
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: fa4ff0cbc2bd361b241f660f9c6b28f03bc7c24c
ms.sourcegitcommit: af4050f69168c15b0afaaa8eea66a5ee38b88fed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80080904"
---
# <a name="hyperlinks"></a>Гиперссылки

Гиперссылки используются для перехода в другую часть приложения, в другое приложение либо по указанному универсальному коду ресурса (URI) в отдельном приложении браузера. Существует два способа добавления гиперссылки в приложение XAML: текстовый элемент **гиперссылки** и элемент управления **HyperlinkButton**.

> **API платформы**: [текстовый элемент гиперссылки](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Hyperlink) и [элемент управления HyperlinkButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.HyperlinkButton)

![Кнопка с гиперссылкой](images/controls/hyperlink-button.png)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте гиперссылки, если вам необходим текст, который будет реагировать на выбор его пользователем и перенаправлять последнего туда, где он сможет получить дополнительные сведения.

Выбирайте необходимый тип гиперссылки в зависимости от своих потребностей.

-   Используйте текстовый элемент встроенной **гиперссылки** внутри элемента управления текстом. Элемент гиперссылки сочетается с другими текстовыми элементами, и его можно использовать в любом InlineCollection. Используйте текстовую гиперссылку, если нужно, чтобы обтекание текстом осуществлялось автоматически, и если нет необходимости в большом элементе управления. Текст гиперссылки может быть небольшим, и на него бывает трудно попасть, особенно на устройствах с сенсорным экраном.
-   Для отдельных гиперссылок можно использовать **HyperlinkButton**. HyperlinkButton — это специализированный элемент управления "кнопка", который можно использовать везде, где вы бы добавили кнопку.
-   Используйте элемент **HyperlinkButton** с [Image](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.image) в качестве содержимого этого элемента, чтобы создать гиперизображение.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/HyperlinkButton">открыть приложение и увидеть HyperlinkButton в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-hyperlink-text-element"></a>Создание текстового элемента гиперссылки

В этом примере продемонстрировано использование текстового элемента гиперссылки в [TextBlock](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock).

```xml
<StackPanel Width="200">
    <TextBlock Text="Privacy" Style="{StaticResource SubheaderTextBlockStyle}"/>
    <TextBlock TextWrapping="WrapWholeWords">
        <Span xml:space="preserve"><Run>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Read the </Run><Hyperlink NavigateUri="http://www.contoso.com">Contoso Privacy Statement</Hyperlink><Run> in your browser.</Run> Donec pharetra, enim sit amet mattis tincidunt, felis nisi semper lectus, vel porta diam nisi in augue.</Span>
    </TextBlock>
</StackPanel>

```
Гиперссылка отображается внутри текстового блока, окружающий текст обтекает ее.

![Пример гиперссылки в виде текстового элемента](images/controls_hyperlink-element.png) 

> **Подсказка**&nbsp;&nbsp;При использовании гиперссылки в текстовом элементе управления в XAML разместите содержимое в контейнере [Span](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.span) и примените атрибут `xml:space="preserve"` к Span, чтобы сохранить пробел между гиперссылкой и другими элементами.

## <a name="create-a-hyperlinkbutton"></a>Создание HyperlinkButton

Далее приведен пример использования HyperlinkButton, как с текстом, так и с изображением.

```xml
<StackPanel>
    <TextBlock Text="About" Style="{StaticResource TitleTextBlockStyle}"/>
    <HyperlinkButton NavigateUri="http://www.contoso.com">
        <Image Source="Assets/ContosoLogo.png"/>
    </HyperlinkButton>
    <TextBlock Text="Version: 1.0.0001" Style="{StaticResource CaptionTextBlockStyle}"/>
    <HyperlinkButton Content="Contoso.com" NavigateUri="http://www.contoso.com"/>
    <HyperlinkButton Content="Acknowledgments" NavigateUri="http://www.contoso.com"/>
    <HyperlinkButton Content="Help" NavigateUri="http://www.contoso.com"/>
</StackPanel>

```

Кнопки-гиперссылки с текстовым содержимым отображаются в виде размеченного текста. Изображение логотипа Contoso также является доступной для нажатия гиперссылкой.

![Пример гиперссылки в виде кнопки](images/controls_hyperlink-button-image.png)

Этот пример демонстрирует, как создать HyperlinkButton в коде.

```csharp
var helpLinkButton = new HyperlinkButton();
helpLinkButton.Content = "Help";
helpLinkButton.NavigateUri = new Uri("http://www.contoso.com");
```

## <a name="handle-navigation"></a>Обработка навигации

Для обоих типов гиперссылок навигация обрабатывается одним способом; вы можете установить свойство **NavigateUri** или обработать событие **Click**.

**Переход к URI**

Чтобы использовать гиперссылку для перехода к URI, задайте свойство NavigateUri. Когда пользователь нажимает на гиперссылку, указанный URI открывается в браузере по умолчанию. Браузер по умолчанию запускается в виде отдельного процесса из приложения.

> [!NOTE]
> Идентификатор URI представлен классом [Windows.Foundation.Uri](/uwp/api/windows.foundation.uri). При программировании с помощью .NET этот класс скрывается. Вам нужно использовать класс [System.Uri](https://docs.microsoft.com/dotnet/api/system.uri). См. подробнее на страницах справочной документации по этим классам.

Использовать схемы **http:** или **https:** не обязательно. Можно использовать такие схемы, как **ms-appx:** , **ms-appdata:** или **ms-resources:** , если содержимое ресурса в этих расположениях подходит для загрузки в браузере. При этом схема **file:** блокируется. Подробнее см. в разделе [Схемы URI](https://docs.microsoft.com/previous-versions/windows/apps/jj655406(v=win.10)).

Когда пользователь нажимает на гиперссылку, значение свойства NavigateUri передается в обработчик системы для получения типов и схем URI. После этого система запускает приложение, которое зарегистрировано для схемы URI, предоставляемой для NavigateUri.

Если вы не хотите, чтобы гиперссылка загружала содержимое в браузере по умолчанию (и не хотите, чтобы отображался браузер), не устанавливайте значение для NavigateUri. Вместо этого обработайте событие нажатия и напишите код, обеспечивающий ваш желаемый результат.


**Обработка события Click**

Используйте событие Click для действий, отличных от запуска URI в браузере, например для навигации внутри приложения. Например, чтобы загрузить новую страницу приложения вместо открытия браузера, вызовите метод [Frame.Navigate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.navigate) в обработчике события Click для перехода к новой странице приложения. Чтобы внешний абсолютный URI загружался в элементе управления [WebView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview), который также присутствует в приложении, вызовите [WebView.Navigate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.webview.navigate) как часть логики обработчика нажатия.

События Click обычно не обрабатываются, если указывается значение NavigateUri, поскольку они представляют два разных способа использования элемента гиперссылки. Чтобы открыть URI в браузере по умолчанию, если указано значение для NavigateUri, не обрабатывайте событие Click. И наоборот, если нужно обработать событие Click, не задавайте NavigateUri.

В обработчике события Click невозможно предотвратить загрузку какого-либо допустимого целевого элемента, указанного для NavigateUri, в браузере по умолчанию; это действие выполняется автоматически (асинхронно) при активации гиперссылки, и его нельзя отменить в обработчике события Click. 

## <a name="hyperlink-underlines"></a>Подчеркивание гиперссылок
По умолчанию гиперссылки подчеркиваются. Подчеркивание имеет большое значение, поскольку это помогает удовлетворить требованиям специальных возможностей. Страдающие дальтонизмом пользователи по подчеркиванию отличают гиперссылки от другого текста. Если вы хотите отключить подчеркивание, рекомендуется добавить другой тип отличительного форматирования, чтобы гиперссылка выделялась на фоне остального текста, например это может быть FontWeight или FontStyle.

**Текстовые элементы гиперссылки**

Чтобы отключить подчеркивание, можно задать свойство [UnderlineStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.hyperlink.underlinestyle). В таком случае рассмотрите возможность использования [FontWeight](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.textelement.fontweight) или [FontStyle](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.textelement.fontstyle) для выделения текста ссылки.

**HyperlinkButton** 

По умолчанию элемент HyperlinkButton отображается как подчеркнутый текст, если для значения свойства [Content](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.contentcontrol.content) задать строку.

Текст отображается без подчеркивания в следующих случаях.
- В качестве значения для свойства Content указано значение [TextBlock](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock), а в TextBlock задано свойство [Text](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.text).
- Шаблон HyperlinkButton и имя части шаблона [ContentPresenter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.contentpresenter) изменились.

Если необходимо, чтобы кнопка отображалась как текст без подчеркивания, воспользуйтесь стандартным элементом управления Button и примените встроенный ресурс системы `TextBlockButtonStyle` к его свойству Style.

## <a name="notes-for-hyperlink-text-element"></a>Примечания по использованию текстового элемента гиперссылки

Сведения, приведенные в этом разделе, относятся только к текстовым элементам гиперссылки и не относятся к элементу управления HyperlinkButton.

**События ввода**

Поскольку гиперссылки не являются элементами [UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement), они не содержат набор событий ввода элементов пользовательского интерфейса, например Tapped, PointerPressed и т.д. Вместо этого гиперссылки содержат собственные события Click, а также неявно указанное поведение системы при загрузке какого-либо URI как элемента NavigateUri. Система обрабатывает все действия ввода, которые должны вызывать действия Hyperlink, и вызывает в ответ событие Click.

**Содержимое**

Гиперссылка имеет ограничения относительно содержимого, которое существует в его коллекции [Inlines](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.span.inlines). В частности, гиперссылка разрешает только [Run](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.run) и другие типы [Span](/uwp/api/windows.ui.xaml.documents.span), которые не являются другим элементом Hyperlink. [InlineUIContainer](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.inlineuicontainer) не может находиться в коллекции Inlines гиперссылки. Попытка добавления содержимого с ограничениями вызовет исключение недопустимого аргумента или исключение анализа XAML.

**Поведение гиперссылки, темы и стиля**

Гиперссылка не наследуется из [Control](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control), поэтому она не содержит свойства [Style](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.style) или [Template](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.template). Можно редактировать свойства, унаследованные из [TextElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.textelement), такие как Foreground или FontFamily, чтобы изменить внешний вид гиперссылки, но нельзя использовать общий стиль или шаблон, чтобы применить изменения. Вместо шаблона используйте общие ресурсы для значений свойств гиперссылки, чтобы обеспечить согласованность. Некоторые свойства гиперссылки используют значения по умолчанию из значения расширения разметки {ThemeResource}, предоставляемого системой. Это позволяет изменять внешний вид гиперссылки, если пользователь изменяет системную тему во время выполнения.

По умолчанию для цвета гиперссылки используется цвет элементов системы. Для переопределения этой настройки можно использовать свойство [Foreground](https://docs.microsoft.com/uwp/api/windows.ui.xaml.documents.textelement.foreground).

## <a name="recommendations"></a>Рекомендации

-   Используйте гиперссылки только для навигации. Не используйте их для других действий.
-   Используйте стиль текста из набора шрифтов для гиперссылок на основе текста. См. подробнее о [шрифтах и наборе шрифтов Windows 10](../style/typography.md).
-   Располагайте отдельные гиперссылки на значительном расстоянии друг от друга, чтобы пользователю было удобно отличать их и делать выбор между ними.
-   Добавляйте к гиперссылкам подсказки, которые информируют о том, куда будет перенаправлен пользователь. Если пользователь будет перенаправляться на внешний сайт, включите в подсказку указание доменного имени верхнего уровня и выделите текст с помощью дополнительного цвета шрифта.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи

- [Текстовые элементы управления](text-controls.md)
- [Руководство по использованию подсказок](tooltips.md)

**Разработчикам (XAML)**
- [Класс Windows.UI.Xaml.Documents.Hyperlink](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Documents.Hyperlink)
- [Класс Windows.UI.Xaml.Controls.HyperlinkButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.HyperlinkButton)
