---
author: Jwmsft
Description: Hyperlinks navigate the user to another part of the app, to another app, or launch a specific uniform resource identifier (URI) using a separate browser app.
title: Гиперссылки
ms.assetid: 74302FF0-65FC-4820-B59A-718A765EF7F0
label: Hyperlinks
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: kisai
design-contact: kimsea
dev-contact: stpete
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: e6679f789f2530a34a2bf527556c144e7a8e03c3
ms.sourcegitcommit: 4d88adfaf544a3dab05f4660e2f59bbe60311c00
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2018
ms.locfileid: "6454036"
---
# <a name="hyperlinks"></a>Гиперссылки

 

Гиперссылки используются для перехода в другую часть приложения, в другое приложение либо по указанному универсальному коду ресурса (URI) в отдельном приложении браузера. Существует два способа добавления гиперссылки в приложение XAML: текстовый элемент **гиперссылки** и элемент управления **HyperlinkButton**.

> **Важные API-интерфейсы**: [текстовый элемент гиперссылки](https://msdn.microsoft.com/library/windows/apps/dn279356), [элемент управления HyperlinkButton](https://msdn.microsoft.com/library/windows/apps/br242739)

![Кнопка с гиперссылкой](images/controls/hyperlink-button.png)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте гиперссылки, если вам необходим текст, который будет реагировать на выбор его пользователем и перенаправлять последнего туда, где он сможет получить дополнительные сведения.

Выбирайте необходимый тип гиперссылки в зависимости от своих потребностей.

-   Используйте текстовый элемент встроенной **гиперссылки** внутри элемента управления текстом. Элемент гиперссылки сочетается с другими текстовыми элементами, и его можно использовать в любом InlineCollection. Используйте текстовую гиперссылку, если нужно, чтобы обтекание текстом осуществлялось автоматически, и если нет необходимости в большом элементе управления. Текст гиперссылки может быть небольшим, и на него бывает трудно попасть, особенно на устройствах с сенсорным экраном.
-   Для отдельных гиперссылок можно использовать **HyperlinkButton**. HyperlinkButton— это специализированный элемент управления "кнопка", который можно использовать везде, где вы бы добавили кнопку.
-   Используйте элемент **HyperlinkButton** с [Image](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.image.aspx) в качестве содержимого этого элемента, чтобы создать гиперизображение.

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/HyperlinkButton">открыть приложение и увидеть HyperlinkButton в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="create-a-hyperlink-text-element"></a>Создание текстового элемента гиперссылки

В этом примере продемонстрировано использование текстового элемента гиперссылки в [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx).

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

> **Совет.**&nbsp;&nbsp;При использовании гиперссылки в текстовом элементе управления в XAML разместите содержимое в контейнере [Span](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.span.aspx) и примените атрибут `xml:space="preserve"` к Span для сохранения пробела между гиперссылкой и другими элементами.

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
> Идентификатор URI, представленный классом [Windows.Foundation.Uri](/uwp/api/windows.foundation.uri). При программировании с помощью .NET этот класс скрыт и следует использовать класс [System.Uri](https://docs.microsoft.com/dotnet/api/system.uri). Дополнительные сведения см. на страницах справки, посвященных этим классам.

Использовать схемы **http:** или **https:** не обязательно. Можно использовать такие схемы, как **ms-appx:**, **ms-appdata:** или **ms-resources:**, если содержимое ресурса в этих расположениях подходит для загрузки в браузере. Однако, схема **file:**, в частности, блокируется. Подробнее см. в разделе [Схемы URI](https://msdn.microsoft.com/library/windows/apps/jj655406.aspx).

Когда пользователь нажимает на гиперссылку, значение свойства NavigateUri передается в обработчик системы для получения типов и схем URI. После этого система запускает приложение, которое зарегистрировано для схемы URI, предоставляемой для NavigateUri.

Если вы не хотите, чтобы гиперссылка загружала содержимое в браузере по умолчанию (и не хотите, чтобы отображался браузер), не устанавливайте значение для NavigateUri. Вместо этого обработайте событие нажатия и напишите код, обеспечивающий ваш желаемый результат.


**Обработка события Click**

Используйте событие Click для действий, отличных от запуска URI в браузере, например для навигации внутри приложения. Например, чтобы загрузить новую страницу приложения вместо открытия браузера, вызовите метод [Frame.Navigate](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.frame.navigate.aspx) в обработчике события Click для перехода к новой странице приложения. Чтобы внешний абсолютный URI загружался в элементе управления [WebView](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.webview.aspx), который также присутствует в приложении, вызовите [WebView.Navigate](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.webview.navigate.aspx) как часть логики обработчика нажатия.

События Click обычно не обрабатываются, если указывается значение NavigateUri, поскольку они представляют два разных способа использования элемента гиперссылки. Чтобы открыть URI в браузере по умолчанию, если указано значение для NavigateUri, не обрабатывайте событие Click. И наоборот, если нужно обработать событие Click, не задавайте NavigateUri.

В обработчике события Click невозможно предотвратить загрузку какого-либо допустимого целевого элемента, указанного для NavigateUri, в браузере по умолчанию; это действие выполняется автоматически (асинхронно) при активации гиперссылки, и его нельзя отменить в обработчике события Click. 

## <a name="hyperlink-underlines"></a>Подчеркивание гиперссылок
По умолчанию гиперссылки подчеркиваются. Подчеркивание имеет большое значение, поскольку это помогает удовлетворить требованиям специальных возможностей. Страдающие дальтонизмом пользователи по подчеркиванию отличают гиперссылки от другого текста. Если вы хотите отключить подчеркивание, рекомендуется добавить другой тип отличительного форматирования, чтобы гиперссылка выделялась на фоне остального текста, например это может быть FontWeight или FontStyle.

**Текстовые элементы гиперссылки**

Чтобы отключить подчеркивание, можно задать свойство [UnderlineStyle](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.hyperlink.underlinestyle.aspx). В таком случае рассмотрите возможность использования [FontWeight](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.textelement.fontweight.aspx) или [FontStyle](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.textelement.fontstyle.aspx) для выделения текста ссылки.

**HyperlinkButton** 

По умолчанию элемент HyperlinkButton отображается как подчеркнутый текст, если для значения свойства [Content](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.contentcontrol.content.aspx) задать строку.

Текст отображается без подчеркивания в следующих случаях.
- В качестве значения для свойства Content указано значение [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx), а в TextBlock задано свойство [Text](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.text.aspx).
- Шаблон HyperlinkButton и имя части шаблона [ContentPresenter](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.contentpresenter.aspx) изменились.

Если необходимо, чтобы кнопка отображалась как текст без подчеркивания, воспользуйтесь стандартным элементом управления Button и примените встроенный ресурс системы `TextBlockButtonStyle` к его свойству Style.

## <a name="notes-for-hyperlink-text-element"></a>Примечания по использованию текстового элемента гиперссылки

Сведения, приведенные в этом разделе, относятся только к текстовым элементам гиперссылки и не относятся к элементу управления HyperlinkButton.

**События ввода**

Поскольку гиперссылки не являются элементами [UIElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.uielement.aspx), они не содержат набор событий ввода элементов пользовательского интерфейса, например Tapped, PointerPressed и т.д. Вместо этого гиперссылки содержат собственные события Click, а также неявно указанное поведение системы при загрузке какого-либо URI как элемента NavigateUri. Система обрабатывает все действия ввода, которые должны вызывать действия Hyperlink, и вызывает в ответ событие Click.

**Содержимое**

Гиперссылка имеет ограничения относительно содержимого, которое существует в его коллекции [Inlines](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.span.inlines.aspx). В частности, гиперссылка разрешает только [Run](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.run.aspx) и другие типы [Span](), которые не являются другим элементом Hyperlink. [InlineUIContainer](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.inlineuicontainer.aspx) не может находиться в коллекции Inlines гиперссылки. Попытка добавления содержимого с ограничениями вызовет исключение недопустимого аргумента или исключение анализа XAML.

**Гиперссылка и поведение темы/стиля**

Гиперссылка не наследуется из [Control](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.control.aspx), поэтому она не содержит свойства [Style](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.style.aspx) или [Template](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.control.template.aspx). Можно редактировать свойства, унаследованные из [TextElement](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.textelement.aspx), такие как Foreground или FontFamily, чтобы изменить внешний вид гиперссылки, но нельзя использовать общий стиль или шаблон, чтобы применить изменения. Вместо шаблона используйте общие ресурсы для значений свойств гиперссылки, чтобы обеспечить согласованность. Некоторые свойства гиперссылки используют значения по умолчанию из значения расширения разметки {ThemeResource}, предоставляемого системой. Это позволяет изменять внешний вид гиперссылки, если пользователь изменяет системную тему во время выполнения.

По умолчанию для цвета гиперссылки используется цвет элементов системы. Для переопределения этой настройки можно использовать свойство [Foreground](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.documents.textelement.foreground.aspx).

## <a name="recommendations"></a>Рекомендации

-   Используйте гиперссылки только для навигации. Не используйте их для других действий.
-   Используйте стиль текста из набора шрифтов для гиперссылок на основе текста. Дополнительные сведения о [шрифтах и наборе шрифтов в Windows 10](../style/typography.md).
-   Располагайте отдельные гиперссылки на значительном расстоянии друг от друга, чтобы пользователю было удобно отличать их и делать выбор между ними.
-   Добавляйте к гиперссылкам подсказки, которые информируют о том, куда будет перенаправлен пользователь. Если пользователь будет перенаправляться на внешний сайт, включите в подсказку указание доменного имени верхнего уровня и выделите текст с помощью дополнительного цвета шрифта.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

- [Текстовые элементы управления](text-controls.md)
- [Руководство по всплывающим подсказкам](tooltips.md)

**Для разработчиков (XAML)**
- [Класс Windows.UI.Xaml.Documents.Hyperlink](https://msdn.microsoft.com/library/windows/apps/dn279356)
- [Класс Windows.UI.Xaml.Controls.HyperlinkButton](https://msdn.microsoft.com/library/windows/apps/br242739)
