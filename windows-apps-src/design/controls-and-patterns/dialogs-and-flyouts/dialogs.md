---
author: mijacobs
Description: Dialogs and flyouts display transient UI elements that appear when the user requests them or when something happens that requires notification or approval.
title: Диалоговые элементы управления
label: Dialogs
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ad6affd9-a3c0-481f-a237-9a1ecd561be8
pm-contact: yulikl
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 9ba4bfcd38acba2bcd7c8399b8b17184edacc15a
ms.sourcegitcommit: 4d88adfaf544a3dab05f4660e2f59bbe60311c00
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2018
ms.locfileid: "6447635"
---
## <a name="dialog-controls"></a>Диалоговые элементы управления

Элементы управления диалогового окна — это модальные наложения пользовательского интерфейса, которые предоставляют контекстную информацию о приложении. Они блокируют взаимодействие с окном приложения, пока не будут закрыты явным образом. Они часто требуют от пользователя совершения каких-либо действий.

![Пример диалогового окна](../images/dialogs/dialog_RS2_delete_file.png)


> **Важные API -интерфейсы**: [класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Диалоговые окна используются для уведомления пользователей о важной информации или запроса подтверждения либо дополнительных сведений перед совершением действия.

Рекомендации о том, когда следует использовать диалоговое окно и когда следует использовать всплывающий элемент (аналогичного элемента управления), см. в разделе [диалоговые окна и всплывающие элементы](index.md). 

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="../images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ContentDialog">ContentDialog</a> или <a href="xamlcontrolsgallery:/item/Flyout">Flyout</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="general-guidelines"></a>Общие рекомендации

-   В первой строке диалогового сообщения явно изложите, в чем проблема или что требуется от пользователя.
-   Заголовок диалогового окна — это основная инструкция. Он необязателен.
    -   Используйте краткий заголовок для пояснений, что пользователям следует сделать в диалоговом окне.
    -   Если вы используете диалоговое окно для простого сообщения, указания на ошибку или вопроса, заголовок можно опустить. Ключевую информацию должен передавать текст сообщения.
    -   Убедитесь, что заголовок соответствует предлагаемому выбору кнопок.
-   Содержимое диалогового окна включает текст описания. Оно обязательно.
    -   Изложите сообщение, ошибку или блокирующий вопрос как можно проще.
    -   Если имеется заголовок диалогового окна, используйте область содержимого для более подробных сведений или пояснения терминологии. Не повторяйте заголовок в немного измененной формулировке.
-   Должна иметься по крайней мере одна диалоговая кнопка.
    -   Убедитесь, что диалоговое окно имеет по крайней мере одну кнопку, соответствующую безопасному обратимому действию, например "Получено!", "Закрыть" или "Отмена". Используйте CloseButton API, чтобы добавить эту кнопку.
    -   Конкретные варианты ответа на основную инструкцию или содержание используйте в качестве текста кнопки. Например: "Разрешить приложению AppName доступ к вашему расположению?", после которого следуют кнопки "Разрешить" и "Запретить". Конкретные варианты ответа воспринимаются быстрее, что помогает эффективно принимать решения.
    - Убедитесь, что текст кнопки действий должно быть кратким. Короткие строки позволяют пользователю сделать выбор быстро и эффективно.
    - Помимо безопасного обратимого действия можно при необходимости представить пользователю одну или две кнопки действия, связанные с основной инструкцией. Эти кнопки действия "do it" подтверждают главную точку главного окна. Используйте API SecondaryButton и PrimaryButton для добавления этих действий "do it".
    - Кнопки действий "do it" должны отображаться как крайние левые. Безопасное обратимое действие должно появиться в качестве правой кнопки.
    - При необходимости можно отличить одну из трех кнопок как кнопки диалогового окна по умолчанию. Используйте DefaultButton API для различения одной из кнопок.  
-   Не используйте диалоговые окна при возникновении контекстно-зависимых от определенной области на странице ошибок, таких как ошибки при проверке (например, в полях для ввода паролей). Используйте непосредственно элемент Canvas приложения для отображения внутренних ошибок.
- Используйте [класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog) для построения работы в диалоговом окне. Не используйте устаревший MessageDialog API.

## <a name="how-to-create-a-dialog"></a>Создание диалогового окна
Для создания диалогового окна используется [класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog). Диалоговое окно можно создать в виде кода или разметки. Хотя обычно проще определять элементы пользовательского интерфейса в XAML, при создании простого диалогового окна удобнее использовать обычный код. В этом примере показано создание диалогового окна для уведомления пользователя об отсутствии подключения к сети WiFi, а для отображения диалогового окна используется метод [ShowAsync](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync).

```csharp
private async void DisplayNoWifiDialog()
{
    ContentDialog noWifiDialog = new ContentDialog
    {
        Title = "No wifi connection",
        Content = "Check your connection and try again.",
        CloseButtonText = "Ok"
    };

    ContentDialogResult result = await noWifiDialog.ShowAsync();
}
```

Когда пользователь нажимает кнопку диалогового кона, метод [ShowAsync](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает [ContentDialogResult](/uwp/api/Windows.UI.Xaml.Controls.ContentDialogResult), чтобы сообщить вам, какую кнопку нажал пользователь.

В этом примере в диалоговом окне задается вопрос и используется возвращенный [ContentDialogResult](/uwp/api/Windows.UI.Xaml.Controls.ContentDialogResult), чтобы определить ответ пользователя.

```csharp
private async void DisplayDeleteFileDialog()
{
    ContentDialog deleteFileDialog = new ContentDialog
    {
        Title = "Delete file permanently?",
        Content = "If you delete this file, you won't be able to recover it. Do you want to delete it?",
        PrimaryButtonText = "Delete",
        CloseButtonText = "Cancel"
    };

    ContentDialogResult result = await deleteFileDialog.ShowAsync();

    // Delete the file if the user clicked the primary button.
    /// Otherwise, do nothing.
    if (result == ContentDialogResult.Primary)
    {
        // Delete the file.
    }
    else
    {
        // The user clicked the CLoseButton, pressed ESC, Gamepad B, or the system back button.
        // Do nothing.
    }
}
```

## <a name="provide-a-safe-action"></a>Обеспечить безопасное действие
Поскольку диалоговые окна блокируют взаимодействие с пользователем, и кнопки — это основной механизм, позволяющий пользователям закрыть диалоговое окно, убедитесь, что диалоговое окно содержит по крайней мере одну кнопку "Безопасность" и обратимое, например, "Закрыть" или "Получено!". **Все диалоговые окна должны содержать, по крайней мере, одну безопасную кнопку действия, чтобы закрыть диалоговое окно.** Это гарантирует, что пользователь может уверенно закрыть диалоговое окно без выполнения действий пользователя.<br>![Диалоговое окно с одной кнопкой](../images/dialogs/dialog_RS2_one_button.png)

```csharp
private async void DisplayNoWifiDialog()
{
    ContentDialog noWifiDialog = new ContentDialog
    {
        Title = "No wifi connection",
        Content = "Check your connection and try again.",
        CloseButtonText = "Ok"
    };

    ContentDialogResult result = await noWifiDialog.ShowAsync();
}
```

Когда диалоговые окна используются для отображения блокирующий вопрос, диалоговое окно необходимо представить пользователю кнопки действий, связанных с вопроса. Кнопка "Безопасность" и обратимая кнопка может сопровождаться одним или двумя «сделать» кнопки действий. Во время презентации пользователя с помощью нескольких параметров, ясно опишите кнопки действия "do it" и безопасно и "don’t do it", связанные с предложенным вопросом.

![Диалогового окна с двумя кнопками](../images/dialogs/dialog_RS2_two_button.png)

```csharp
private async void DisplayLocationPromptDialog()
{
    ContentDialog locationPromptDialog = new ContentDialog
    {
        Title = "Allow AppName to access your location?",
        Content = "AppName uses this information to help you find places, connect with friends, and more.",
        CloseButtonText = "Block",
        PrimaryButtonText = "Allow"
    };

    ContentDialogResult result = await locationPromptDialog.ShowAsync();
}
```

Диалоговые окна с тремя кнопками используются, когда вы представляете пользователю два действия "do it" и "don’t do it". Диалоговые окна с тремя кнопками следует использовать слишком часто с очистить различий между вспомогательное действие и меры безопасности или закрыть.

![Диалоговое окно с тремя кнопками](../images/dialogs/dialog_RS2_three_button.png)

```csharp
private async void DisplaySubscribeDialog()
{
    ContentDialog subscribeDialog = new ContentDialog
    {
        Title = "Subscribe to App Service?",
        Content = "Listen, watch, and play in high definition for only $9.99/month. Free to try, cancel anytime.",
        CloseButtonText = "Not Now",
        PrimaryButtonText = "Subscribe",
        SecondaryButtonText = "Try it"
    };

    ContentDialogResult result = await subscribeDialog.ShowAsync();
}
```

## <a name="the-three-dialog-buttons"></a>Диалоговое окно с тремя кнопками
ContentDialog имеет три различных типа кнопки, которые можно использовать для взаимодействия с диалоговое окно.

- **CloseButton** - требуется - представляет безопасный, обратимое действие, которое позволяет пользователю закрыть диалоговое окно. Отображается в виде правой кнопки.
- **PrimaryButton** - необязательный — представляет первое действие «выполнить». Отображается в виде левой кнопки.
- **SecondaryButton** - необязательный — представляет второе действие «выполнить». Отображается в виде средней кнопки.

С помощью встроенных кнопок положение кнопки правильно, убедитесь, что правильно реагировать на события клавиатуры гарантирует, что область команд остается видимой, даже когда экранной клавиатуры вверх и будет сделать диалоговое окно согласованность с другими диалоговые окна.

### <a name="closebutton"></a>CloseButton
Каждый диалоговое окно должен содержать безопасный обратимое действие кнопка, которая позволяет пользователям уверенно закрыть диалоговое окно.

Используйте ContentDialog.CloseButton API для создания этой кнопки. Это позволяет создать взаимодействие с пользователем правильного все входные данные, включая мышь, клавиатуру, сенсорный ввод и игрового контроллера. Этот опыт будет происходить при:
<ol>
    <li>Пользователь щелкает или касается CloseButton </li>
    <li>Пользователь нажимает системную кнопку "Назад" </li>
    <li>Пользователь нажимает кнопку ESC на клавиатуре </li>
    <li>Пользователь нажимает кнопку B игрового контроллера </li>
</ol>

Когда пользователь нажимает кнопку диалогового кона, метод [ShowAsync](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает [ContentDialogResult](/uwp/api/Windows.UI.Xaml.Controls.ContentDialogResult), чтобы сообщить вам, какую кнопку нажал пользователь. При нажатии клавиши на CloseButton возвращает ContentDialogResult.None.

### <a name="primarybutton-and-secondarybutton"></a>PrimaryButton и SecondaryButton
Помимо CloseButton вы при необходимости представить пользователю с одну или две кнопки действия, связанные с основная инструкция.
Использовать PrimaryButton для первого действия «выполнить» и SecondaryButton для второго действия «выполнить». В диалоговых окнах три кнопки PrimaryButton обычно представляет согласие действие «сделать», а SecondaryButton обычно представляет нейтральный или вспомогательной действие «выполнить».
Например приложение может предложить пользователю подписаться на службу. PrimaryButton как согласие действие «сделать» будут размещены Subscribe текста, при SecondaryButton как нейтральный действие «сделать» будут размещены Try его текста. CloseButton позволит пользователю отменить без выполнения либо действие.

Когда пользователь нажимает на PrimaryButton [ShowAsync](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) метод возвращает ContentDialogResult.Primary.
Когда пользователь нажимает кнопку SecondaryButton, метод [ShowAsync](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает ContentDialogResult.Secondary.

![Диалоговое окно три кнопки](../images/dialogs/dialog_RS2_three_button.png)

### <a name="defaultbutton"></a>DefaultButton
При необходимости можно дифференциировать одну из трех кнопок как кнопку по умолчанию. Указание кнопку по умолчанию вызывает происходит следующее:
- Кнопка получает визуальное представление акцент кнопки
- Кнопка будет реагировать на клавишу ВВОД автоматически
    - Когда пользователь нажимает клавишу ввод с клавиатуры, обработчика нажатия кнопки, связанные с кнопкой по умолчанию будет запущена и ContentDialogResult возвратит значение, связанное с кнопкой «по умолчанию»
    - Если пользователь разместил фокус клавиатуры на элемент управления, который обрабатывает ввод, кнопку по умолчанию не будет реагировать на ввод нажатия
- Кнопки получит фокус автоматически при открытии диалогового окна, если содержимое диалоговое окно содержит фокус пользовательского интерфейса

Используйте свойство ContentDialog.DefaultButton, чтобы указать кнопку по умолчанию. По умолчанию значение отсутствует кнопка по умолчанию.

![Три кнопки диалоговое окно с кнопкой по умолчанию](../images/dialogs/dialog_RS2_three_button_default.png)

```csharp
private async void DisplaySubscribeDialog()
{
    ContentDialog subscribeDialog = new ContentDialog
    {
        Title = "Subscribe to App Service?",
        Content = "Listen, watch, and play in high definition for only $9.99/month. Free to try, cancel anytime.",
        CloseButtonText = "Not Now",
        PrimaryButtonText = "Subscribe",
        SecondaryButtonText = "Try it",
        DefaultButton = ContentDialogButton.Primary
    };

    ContentDialogResult result = await subscribeDialog.ShowAsync();
}
```

## <a name="confirmation-dialogs-okcancel"></a>Диалоговые окна подтверждения ("ОК" и "Отмена")
Диалоговое окно подтверждения позволяет пользователям подтвердить действие. Они могут подтвердить выполнение действия или отменить его.  
Типичное диалоговое окно подтверждения обычно содержит две кнопки: подтверждения ("ОК") и отмены.  

<ul>
    <li>
        <p>В целом кнопка подтверждения должна быть слева (основная кнопка), а кнопка отмены (вспомогательная) — справа.</p>
        <img alt="An OK/cancel dialog" src="../images/dialogs/dialog_RS2_delete_file.png" />
    </li>
    <li>Как указано в разделе "Общие рекомендации", используйте кнопки с текстом, который описывает конкретные варианты ответа на основную инструкцию или содержимое.
    </li>
</ul>

> На некоторых платформах кнопка подтверждения размещена справа, а не слева. Так почему рекомендуется поместить ее слева?  Если предполагается, что большинство пользователей — правши и держат телефон в правой руке, им будет удобнее нажимать кнопку подтверждения слева, так как более вероятно, что она будет расположена в пределах досягаемости больших пальцев пользователя. Чтобы нажать кнопки в правой части экрана, пользователю необходимо переместить большой палец в менее удобное положение.





## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме
- [Подсказки](../tooltips.md)
- [Меню и контекстное меню](../menus.md)
- [Класс Flyout](/uwp/api/Windows.UI.Xaml.Controls.Flyout)
- [Класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)
