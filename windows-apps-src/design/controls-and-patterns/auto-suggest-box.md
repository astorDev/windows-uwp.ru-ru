---
author: Jwmsft
Description: A text entry box that provides suggestions as the user types.
title: Руководство по полям автозаполнения
ms.assetid: 1F608477-F795-4F33-92FA-F200CC243B6B
dev.assetid: 54F8DB8A-120A-4D79-8B5A-9315A3764C2F
label: Auto-suggest box
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: efb6c8a9324419ea099041f34c341152351575f6
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5743186"
---
# <a name="auto-suggest-box"></a>Поле автозаполнения

Используйте AutoSuggestBox, чтобы предоставить список предложений, из которых пользователь может выбрать нужное по мере набора.

> **Важные API-интерфейсы**: [класс AutoSuggestBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.aspx), [событие TextChanged](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.textchanged.aspx), [событие SuggestionChose](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.suggestionchosen.aspx), [событие QuerySubmitted](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.querysubmitted.aspx)

![Поле автозаполнения](images/controls/auto-suggest-box-open.png)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Если вам нужен простой настраиваемый элемент управления, который обеспечивает текстовый поиск со списком предложений, выберите поле автозаполнения.

Дополнительные сведения о выборе подходящего элемента управления текстом можно найти в статье [Элементы управления текстом](text-controls.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/AutoSuggestBox">открыть приложение и увидеть AutoSuggestBox в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Поле автозаполнения в приложении "Музыка Groove".

![Поле автозаполнения в приложении "Музыка Groove"](images/control-examples/auto-suggest-box-groove.png)

## <a name="anatomy"></a>Структура
Точка входа для поля автозаполнения состоит из необязательного заголовка и текстового поля с необязательной подсказкой:

![Пример точки входа для элемента управления автозаполнения](images/controls_autosuggest_entrypoint.png)

Список результатов автозаполнения заполняется автоматически, как только пользователь начинает вводить текст. Список результатов может отображаться над текстовым полем или под ним. Появляется кнопка "Очистить все".

![Пример развернутого элемента управления автозаполнения](images/controls_autosuggest_expanded01.png)

## <a name="create-an-auto-suggest-box"></a>Создайте поле автозаполнения

Для использования AutoSuggestBox необходимо ответить на 3 действия пользователя.

- Изменение текста — обновляйте список предложений при вводе текста пользователем.
- Выбор предложения — обновите текстовое поле при выборе пользователем предложения из списка вариантов.
- Отправка запроса — отобразите результаты запроса при отправке запроса пользователем.

### <a name="text-changed"></a>Изменение текста

Событие [TextChanged](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.textchanged.aspx) возникает, когда обновляется содержимое текстового поля. Используйте свойство [Причина](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestboxtextchangedeventargs.reason.aspx) аргументов события, чтобы определить, было ли изменение внесено пользователем. Если причиной изменения является **UserInput**, отфильтруйте данные на основе введенных данных. После этого установите отфильтрованные данные как свойство [ItemsSource](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemssource.aspx) элемента AutoSuggestBox для обновления списка предложений.

Для управления отображением элементов в списке предложений можно использовать [DisplayMemberPath](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.displaymemberpath.aspx) или [ItemTemplate](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.itemscontrol.itemtemplate.aspx).

- Для отображения текста одного свойства элемента данных задайте свойство DisplayMemberPath, чтобы выбрать, какое свойство объекта отображать в списке предложений.
- Для определения пользовательского вида каждого элемента в списке используйте свойство ItemTemplate.

### <a name="suggestion-chosen"></a>Выбор предложения

Когда пользователь переходит с помощью клавиатуры по списку предложений, необходимо обновлять текст в текстовом поле соответствующим образом.

Вы можете настроить свойство [TextMemberPath](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.textmemberpath.aspx), чтобы выбрать, какое свойство объекта данных отображать в текстовом поле. Если вы определяете TextMemberPath, то текстовое поле обновляется автоматически. Обычно необходимо указать одно и то же значение DisplayMemberPath и TextMemberPath, чтобы текст совпадал в списке предложений и текстовом поле.

Если нужно отобразить нечто большее, чем простое свойство, обработайте событие [SuggestionChosen](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.suggestionchosen.aspx), чтобы заполнить текстовое поле пользовательским текстом на основе выбранного элемента.

### <a name="query-submitted"></a>Отправка запроса

Обработайте событие [QuerySubmitted](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.querysubmitted.aspx), чтобы выполнить соответствующий приложению запрос и показать пользователю результат.

Событие QuerySubmitted происходит, когда пользователь отправляет строку запроса. Пользователь может отправить запрос одним из следующих способов.
- Нажать клавишу ВВОД или щелкнуть значок запроса, в то время как фокус находится в текстовом поле. Свойство аргумента события [ChosenSuggestion](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestboxquerysubmittedeventargs.chosensuggestion.aspx)— **null**.
- Нажать клавишу ВВОД, щелкнуть или нажать элемент, в то время как фокус находится в списке предложений. Свойство аргумента события ChosenSuggestion содержит элемент, который был выбран в списке.

Во всех случаях свойство аргумента [QueryText](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestboxquerysubmittedeventargs.querytext.aspx) содержит текст из текстового поля.

Вот простой класс AutoSuggestBox с необходимыми обработчиками событий.

```xaml
<AutoSuggestBox PlaceholderText="Search" QueryIcon="Find" Width="200"
                TextChanged="AutoSuggestBox_TextChanged"
                QuerySubmitted="AutoSuggestBox_QuerySubmitted"
                SuggestionChosen="AutoSuggestBox_SuggestionChosen"/>
```

```csharp
private void AutoSuggestBox_TextChanged(AutoSuggestBox sender, AutoSuggestBoxTextChangedEventArgs args)
{
    // Only get results when it was a user typing,
    // otherwise assume the value got filled in by TextMemberPath
    // or the handler for SuggestionChosen.
    if (args.Reason == AutoSuggestionBoxTextChangeReason.UserInput)
    {
        //Set the ItemsSource to be your filtered dataset
        //sender.ItemsSource = dataset;
    }
}


private void AutoSuggestBox_SuggestionChosen(AutoSuggestBox sender, AutoSuggestBoxSuggestionChosenEventArgs args)
{
    // Set sender.Text. You can use args.SelectedItem to build your text string.
}


private void AutoSuggestBox_QuerySubmitted(AutoSuggestBox sender, AutoSuggestBoxQuerySubmittedEventArgs args)
{
    if (args.ChosenSuggestion != null)
    {
        // User selected an item from the suggestion list, take an action on it here.
    }
    else
    {
        // Use args.QueryText to determine what to do.
    }
}
```

## <a name="use-autosuggestbox-for-search"></a>Использование AutoSuggestBox для поиска

Используйте AutoSuggestBox, чтобы предоставить список предложений, из которых пользователь по мере ввода текста может выбрать нужное.

По умолчанию поле ввода текста не имеет кнопки запроса. Вы можете настроить свойство [QueryIcon](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.autosuggestbox.queryicon.aspx), чтобы добавить кнопку с выбранным значком справа от текстового поля. Например, чтобы AutoSuggestBox выглядело как типичное поле поиска, добавьте значок поиска.

```xaml
<AutoSuggestBox QueryIcon="Find"/>
```

Вот пример AutoSuggestBox со значком поиска.

![Пример точки входа для элемента управления автозаполнения](images/controls_autosuggest_entrypoint.png)

## <a name="dos-and-donts"></a>Рекомендации

-   При использовании поля автозаполнения, если для введенного текста поиск не дает результатов, выводите вместо результатов строку сообщения "Результатов нет", чтобы пользователь знал, что поисковый запрос был выполнен.

    ![Пример поля автозаполнения без результатов поиска](images/controls_autosuggest_noresults.png)

<!--
<div class="microsoft-internal-note">
**Globalization and localization checklist**

<table>
<tr>
<th>Vertical spacing</th><td>Use non-Latin characters for vertical spacing to ensure non-Latin scripts will display properly, including numbers.</td>
</tr>
<tr>
<th>Scrolling</th><td>When auto suggest text is selected, user should be able to scroll to end of string.</td>
</tr>
</table>
</div>
-->

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
