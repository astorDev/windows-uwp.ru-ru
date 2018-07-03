---
author: mijacobs
Description: Dialogs and flyouts display transient UI elements that appear when the user requests them or when something happens that requires notification or approval.
title: Диалоговые окна и всплывающие элементы
label: Dialogs
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: ad6affd9-a3c0-481f-a237-9a1ecd561be8
pm-contact: yulikl
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 7b263fda1de798473f581e2191d3fa01385060e6
ms.sourcegitcommit: e4627686138ec8c885696c4c511f2f05195cf8ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2018
ms.locfileid: "1893849"
---
# <a name="dialogs-and-flyouts"></a>Диалоговые окна и всплывающие элементы



Диалоговые окна и всплывающие элементыиспользуются для временного отображения элементов пользовательского интерфейса, если происходит нечто, требующее уведомления, подтверждения или дополнительных сведений от пользователя.

> **Важные API-интерфейсы**: [класс ContentDialog](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog), [класс Flyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout)


:::row::: :::column::: **Диалоговые окна**
        
        ![Example of a dialog](images/dialogs/dialog_RS2_delete_file.png)

        Dialogs are modal UI overlays that provide contextual app information. Dialogs block interactions with the app window until being explicitly dismissed. They often request some kind of action from the user.
    :::column-end:::
    :::column::: 
        **Flyouts**

        ![Example of a flyout](images/flyout-example2.png)

        A flyout is a lightweight contextual popup that displays UI related to what the user is doing. It includes placement and sizing logic, and can be used to reveal a secondary control or show more detail about an item.

        Unlike a dialog, a flyout can be quickly dismissed by tapping or clicking somewhere outside the flyout, pressing the Escape key or Back button, resizing the app window, or changing the device's orientation.
    :::column-end:::
:::row-end:::


## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

* Диалоговые окна используются для уведомления пользователей о важной информации или запроса подтверждения либо дополнительных сведений перед совершением действия.
* Не используйте всплывающий элемент вместо [подсказки](tooltips.md) или [контекстного меню](menus.md). Используйте подсказку, чтобы вывести краткое описание, которое исчезает через определенное время. Для контекстных действий, связанных с элементом пользовательского интерфейса, например копированием или вставкой, используйте контекстное меню.  


Диалоговые окна и всплывающие элементы позволяют пользователям быть в курсе важной информации, но также мешают взаимодействию с пользователем. Поскольку диалоговые окна модальны (приводят к блокировке), они прерывают работу пользователей и мешают им выполнять дальнейшие действия, пока не произойдет взаимодействие с диалоговым окном. Всплывающие элементы в меньшей степени мешают взаимодействию с пользователем, но отображение слишком большого числа всплывающих элементов может отвлекать.

Учитывайте важность информации, которую требуется отобразить: важна ли она настолько, чтобы прерывать пользователя? Также учтите частоту отображения информации. Если диалоговое окно или уведомление отображается каждые несколько минут, возможно, следует выделить для этой информации место в основном пользовательском интерфейсе. Возьмем, к примеру, клиент чата. Вместо того чтобы отображать всплывающий элемент каждый раз, когда друг выполняет вход, можно отображать список друзей, находящихся на данный момент в сети, и выделять друзей, выполняющих вход.

Диалоговые окна часто используются для подтверждения действия (например, удаления файла) перед его выполнением. Если ожидается, что пользователь будет часто выполнять определенное действие, вы можете предоставить пользователю возможность отменить ошибочное действие, чем запрашивать его подтверждение каждый раз.

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы открыть приложение и увидеть <a href="xamlcontrolsgallery:/item/ContentDialog">ContentDialog</a> или <a href="xamlcontrolsgallery:/item/Flyout">Flyout</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="dialogs-vs-flyouts"></a>Диалоговые окна и всплывающие элементы

Определив, собираетесь ли вы использовать диалоговое окно или всплывающий элемент, необходимо выбрать один из этих вариантов.

Учитывая то, что, в отличие от всплывающих элементов, диалоговые окна блокируют взаимодействие, последние следует использовать в тех случаях, когда пользователь должен уделить все внимание определенной информации или ответить на вопрос. С другой стороны, всплывающие элементы можно использовать для привлечения внимания к определенной информации, которую пользователь вполне может проигнорировать.

:::row::: :::column:::
   <p><b>Используйте диалоговое окно в следующих целях.</b> <br/>
<ul>
<li>Чтобы сообщить важную информацию, которую пользователь <b>должен</b> прочитать и осознать перед продолжением работы. Ниже перечислены примеры таких данных.
<ul>
  <li>угроза безопасности пользователя;</li>
  <li>намерение изменить ценные данные без возможности отмены действия;</li>
  <li>намерение удалить ценные данные;</li>
  <li>подтверждение покупки из приложения;</li>
</ul>

</li>
<li>Сообщения об ошибках, которые относятся к приложению в целом, например ошибка подключения.</li>
<li>Вопросы, если приложению нужно задать пользователю блокирующий вопрос, например, если приложение не может сделать выбор от имени пользователя. Блокирующий вопрос нельзя проигнорировать или отложить. У пользователя должны быть понятные варианты выбора.</li>
</ul>
</p>
:::column-end::: :::column::: <p><b>Используйте всплывающий элемент в следующих целях.</b> <br/>
<ul>
<li>сбор дополнительных сведений для завершения действия;</li>
<li>отображение временно актуальных сведений; например, когда пользователь щелкает эскиз изображения в приложении фотоальбома, можно использовать всплывающий элемент для отображения увеличенного варианта этого изображения;</li>
<li>отображение дополнительной информации, например подробных сведений или более длинных описаний элемента на странице.</li>
</ul></p>
:::column-end::: :::row-end:::



## <a name="dialogs"></a>Диалоговые окна
### <a name="general-guidelines"></a>Общие рекомендации

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
- Используйте [класс ContentDialog](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog) для построения работы в диалоговом окне. Не используйте устаревший MessageDialog API.

### <a name="dialog-scenarios"></a>Диалоговое окно сценариев
Поскольку диалоговые окна блокируют взаимодействие с пользователем, и кнопки — это основной механизм, позволяющий пользователям закрыть диалоговое окно, убедитесь, что диалоговое окно содержит по крайней мере одну кнопку "Безопасность" и обратимое, например, "Закрыть" или "Получено!". **Все диалоговые окна должны содержать, по крайней мере, одну безопасную кнопку действия, чтобы закрыть диалоговое окно.** Это гарантирует, что пользователь может уверенно закрыть диалоговое окно без выполнения действий пользователя.<br>![Диалоговое окно с одной кнопкой](images/dialogs/dialog_RS2_one_button.png)

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

![Диалогового окна с двумя кнопками](images/dialogs/dialog_RS2_two_button.png)

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

![Диалоговое окно с тремя кнопками](images/dialogs/dialog_RS2_three_button.png)

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

### <a name="the-three-dialog-buttons"></a>Диалоговое окно с тремя кнопками
ContentDialog имеет три различных типа кнопки, которые можно использовать для взаимодействия с диалоговое окно.

- **CloseButton** - требуется - представляет безопасный, обратимое действие, которое позволяет пользователю закрыть диалоговое окно. Отображается в виде правой кнопки.
- **PrimaryButton** - необязательный — представляет первое действие «выполнить». Отображается в виде левой кнопки.
- **SecondaryButton** - необязательный — представляет второе действие «выполнить». Отображается в виде средней кнопки.

С помощью встроенных кнопок положение кнопки правильно, убедитесь, что правильно реагировать на события клавиатуры гарантирует, что область команд остается видимой, даже когда экранной клавиатуры вверх и будет сделать диалоговое окно согласованность с другими диалоговые окна.

#### <a name="closebutton"></a>CloseButton
Каждый диалоговое окно должен содержать безопасный обратимое действие кнопка, которая позволяет пользователям уверенно закрыть диалоговое окно.

Используйте ContentDialog.CloseButton API для создания этой кнопки. Это позволяет создать взаимодействие с пользователем правильного все входные данные, включая мышь, клавиатуру, сенсорный ввод и игрового контроллера. Этот опыт будет происходить при:
<ol>
    <li>Пользователь щелкает или касается CloseButton </li>
    <li>Пользователь нажимает системную кнопку "Назад" </li>
    <li>Пользователь нажимает кнопку ESC на клавиатуре </li>
    <li>Пользователь нажимает кнопку B игрового контроллера </li>
</ol>

Когда пользователь нажимает кнопку диалогового кона, метод [ShowAsync](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает [ContentDialogResult](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialogResult), чтобы сообщить вам, какую кнопку нажал пользователь. При нажатии клавиши на CloseButton возвращает ContentDialogResult.None.

#### <a name="primarybutton-and-secondarybutton"></a>PrimaryButton и SecondaryButton
Помимо CloseButton вы при необходимости представить пользователю с одну или две кнопки действия, связанные с основная инструкция.
Использовать PrimaryButton для первого действия «выполнить» и SecondaryButton для второго действия «выполнить». В диалоговых окнах три кнопки PrimaryButton обычно представляет согласие действие «сделать», а SecondaryButton обычно представляет нейтральный или вспомогательной действие «выполнить».
Например приложение может предложить пользователю подписаться на службу. PrimaryButton как согласие действие «сделать» будут размещены Subscribe текста, при SecondaryButton как нейтральный действие «сделать» будут размещены Try его текста. CloseButton позволит пользователю отменить без выполнения либо действие.

Когда пользователь нажимает на PrimaryButton [ShowAsync](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) метод возвращает ContentDialogResult.Primary.
Когда пользователь нажимает кнопку SecondaryButton, метод [ShowAsync](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает ContentDialogResult.Secondary.

![Диалоговое окно три кнопки](images/dialogs/dialog_RS2_three_button.png)

#### <a name="defaultbutton"></a>DefaultButton
При необходимости можно дифференциировать одну из трех кнопок как кнопку по умолчанию. Указание кнопку по умолчанию вызывает происходит следующее:
- Кнопка получает визуальное представление акцент кнопки
- Кнопка будет реагировать на клавишу ВВОД автоматически
    - Когда пользователь нажимает клавишу ввод с клавиатуры, обработчика нажатия кнопки, связанные с кнопкой по умолчанию будет запущена и ContentDialogResult возвратит значение, связанное с кнопкой «по умолчанию»
    - Если пользователь разместил фокус клавиатуры на элемент управления, который обрабатывает ввод, кнопку по умолчанию не будет реагировать на ввод нажатия
- Кнопки получит фокус автоматически при открытии диалогового окна, если содержимое диалоговое окно содержит фокус пользовательского интерфейса

Используйте свойство ContentDialog.DefaultButton, чтобы указать кнопку по умолчанию. По умолчанию значение отсутствует кнопка по умолчанию.

![Три кнопки диалоговое окно с кнопкой по умолчанию](images/dialogs/dialog_RS2_three_button_default.png)

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

### <a name="confirmation-dialogs-okcancel"></a>Диалоговые окна подтверждения ("ОК" и "Отмена")
Диалоговое окно подтверждения позволяет пользователям подтвердить действие. Они могут подтвердить выполнение действия или отменить его.  
Типичное диалоговое окно подтверждения обычно содержит две кнопки: подтверждения ("ОК") и отмены.  

<ul>
    <li>
        <p>В целом кнопка подтверждения должна быть слева (основная кнопка), а кнопка отмены (вспомогательная) — справа.</p>
        <img alt="An OK/cancel dialog" src="images/dialogs/dialog_RS2_delete_file.png" />
    </li>
    <li>Как указано в разделе "Общие рекомендации", используйте кнопки с текстом, который описывает конкретные варианты ответа на основную инструкцию или содержимое.
    </li>
</ul>

> На некоторых платформах кнопка подтверждения размещена справа, а не слева. Так почему рекомендуется поместить ее слева?  Если предполагается, что большинство пользователей — правши и держат телефон в правой руке, им будет удобнее нажимать кнопку подтверждения слева, так как более вероятно, что она будет расположена в пределах досягаемости больших пальцев пользователя. Чтобы нажать кнопки в правой части экрана, пользователю необходимо переместить большой палец в менее удобное положение.

### <a name="create-a-dialog"></a>Создание диалогового окна
Для создания диалогового окна используется [класс ContentDialog](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog). Диалоговое окно можно создать в виде кода или разметки. Хотя обычно проще определять элементы пользовательского интерфейса в XAML, при создании простого диалогового окна удобнее использовать обычный код. В этом примере показано создание диалогового окна для уведомления пользователя об отсутствии подключения к сети WiFi, а для отображения диалогового окна используется метод [ShowAsync](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync).

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

Когда пользователь нажимает кнопку диалогового кона, метод [ShowAsync](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает [ContentDialogResult](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialogResult), чтобы сообщить вам, какую кнопку нажал пользователь.

В этом примере в диалоговом окне задается вопрос и используется возвращенный [ContentDialogResult](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialogResult), чтобы определить ответ пользователя.

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

## <a name="flyouts"></a>Всплывающие элементы
###  <a name="create-a-flyout"></a>Создание всплывающего элемента

Всплывающий элемент — это исчезающий контейнер, который показывает в качестве своего содержимого произвольный пользовательский интерфейс. Всплывающие элементы могут содержать другие всплывающих элементов или контекстное меню для создания вложенных взаимодействия.

![Контекстное меню размещается внутри всплывающего элемента](images/flyout-nested.png)

Всплывающие элементы связаны с определенными элементами управления. Вы можете использовать свойство [Placement](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase.Placement) для указания места отображения всплывающего элемента: вверху, слева, внизу, справа или в центре. Если выбрать [полный режим размещения](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutPlacementMode), приложение растягивает всплывающий элемент или размещает его в центре окна приложения. Некоторые элементы управления, например [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button), содержат свойство [Flyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button.Flyout), которое можно использовать для привязки всплывающего элемента или [контекстного меню](menus.md).

В этом примере создается простой всплывающий элемент, в котором отображается текст при нажатии кнопки.
````xaml
<Button Content="Click me">
  <Button.Flyout>
     <Flyout>
        <TextBlock Text="This is a flyout!"/>
     </Flyout>
  </Button.Flyout>
</Button>
````

Если элемент управления лишен свойства "Flyout", вы можете использовать присоединенное свойство [FlyoutBase.AttachedFlyout](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.flyoutbase.AttachedFlyoutProperty). При этом также следует вызвать метод [FlyoutBase.ShowAttachedFlyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase#Windows_UI_Xaml_Controls_Primitives_FlyoutBase_ShowAttachedFlyout_Windows_UI_Xaml_FrameworkElement_) для отображения всплывающего элемента.

В этом примере к изображению добавляется простой всплывающий элемент. Когда пользователь нажимает на изображение, приложение отображает всплывающий элемент.

````xaml
<Image Source="Assets/cliff.jpg" Width="50" Height="50"
  Margin="10" Tapped="Image_Tapped">
  <FlyoutBase.AttachedFlyout>
    <Flyout>
      <TextBlock Text="This is some text in a flyout."  />
    </Flyout>        
  </FlyoutBase.AttachedFlyout>
</Image>
````

````csharp
private void Image_Tapped(object sender, TappedRoutedEventArgs e)
{
    FlyoutBase.ShowAttachedFlyout((FrameworkElement)sender);
}
````

В предыдущих примерах всплывающие элементы определялись внутренне. Вы также можете определить всплывающий элемент как статический ресурс и использовать его с несколькими элементами. В этом примере показано создание более сложного всплывающего элемента, в котором отображается увеличенный вариант изображения при нажатии его эскиза.

````xaml
<!-- Declare the shared flyout as a resource. -->
<Page.Resources>
    <Flyout x:Key="ImagePreviewFlyout" Placement="Right">
        <!-- The flyout's DataContext must be the Image Source
             of the image the flyout is attached to. -->
        <Image Source="{Binding Path=Source}"
            MaxHeight="400" MaxWidth="400" Stretch="Uniform"/>
    </Flyout>
</Page.Resources>
````

````xaml
<!-- Assign the flyout to each element that shares it. -->
<StackPanel>
    <Image Source="Assets/cliff.jpg" Width="50" Height="50"
           Margin="10" Tapped="Image_Tapped"
           FlyoutBase.AttachedFlyout="{StaticResource ImagePreviewFlyout}"
           DataContext="{Binding RelativeSource={RelativeSource Mode=Self}}"/>
    <Image Source="Assets/grapes.jpg" Width="50" Height="50"
           Margin="10" Tapped="Image_Tapped"
           FlyoutBase.AttachedFlyout="{StaticResource ImagePreviewFlyout}"
           DataContext="{Binding RelativeSource={RelativeSource Mode=Self}}"/>
    <Image Source="Assets/rainier.jpg" Width="50" Height="50"
           Margin="10" Tapped="Image_Tapped"
           FlyoutBase.AttachedFlyout="{StaticResource ImagePreviewFlyout}"
           DataContext="{Binding RelativeSource={RelativeSource Mode=Self}}"/>
</StackPanel>
````

````csharp
private void Image_Tapped(object sender, TappedRoutedEventArgs e)
{
    FlyoutBase.ShowAttachedFlyout((FrameworkElement)sender);  
}
````

### <a name="style-a-flyout"></a>Стилизация всплывающего элемента
Для стилизации всплывающего элемента изменяется его свойство [FlyoutPresenterStyle](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout.FlyoutPresenterStyle). В этом примере показан абзац создания программы-оболочки текста и делает блок текста доступным для программ чтения с экрана.

![Всплывающий элемент со специальными возможностями с переносом текста](images/flyout-wrapping-text.png)

````xaml
<Flyout>
  <Flyout.FlyoutPresenterStyle>
    <Style TargetType="FlyoutPresenter">
      <Setter Property="ScrollViewer.HorizontalScrollMode"
          Value="Disabled"/>
      <Setter Property="ScrollViewer.HorizontalScrollBarVisibility" Value="Disabled"/>
      <Setter Property="IsTabStop" Value="True"/>
      <Setter Property="TabNavigation" Value="Cycle"/>
    </Style>
  </Flyout.FlyoutPresenterStyle>
  <TextBlock Style="{StaticResource BodyTextBlockStyle}" Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat."/>
</Flyout>
````

#### <a name="styling-flyouts-for-10-foot-experience"></a>Всплывающие элементы стиля для большом расстоянии

Элементы управления с исчезновением, например всплывающий элемент, захватывают фокус клавиатуры или геймпада внутри промежуточного пользовательского интерфейса, пока не будут закрыты. Для предоставления визуальной подсказки по этому поведению элементы управления с исчезновением на Xbox отображают наложение, затемняющее контрастность и видимость вне области действия пользовательского интерфейса. Это поведение можно изменить с помощью нового свойства [`LightDismissOverlayMode`](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.FlyoutBase.LightDismissOverlayMode). По умолчанию всплывающие элементы пользовательского интерфейса отображают наложение с исчезновением на Xbox, но не на других семействах устройств, хотя приложения могут принудительно устанавливать наложению постоянное значение **Вкл.** или **Выкл.**.

![Всплывающий элемент с наложением яркости](images/flyout-smoke.png)

```xaml
<MenuFlyout LightDismissOverlayMode="On">
```

### <a name="light-dismiss-behavior"></a>Поведение с исчезновением
Всплывающие элементы могут быть закрытыми с подсветкой быстрого закрыть действие, в том числе
-   Коснитесь вне всплывающий элемент
-   Нажать клавишу Escape на клавиатуре
-   Нажмите кнопку Назад системы оборудования или программного обеспечения
-   Нажмите кнопку B игрового контроллера

После закрытия касанием этот жест поглощает обычно и не передается пользовательский Интерфейс под. Например если за открытых раскрывающихся отображается кнопка, пользователя при первом касании закрывает всплывающий элемент, но не активирует эту кнопку. Нажатие кнопки требуется второе касание.

Можно изменить это поведение, назначение кнопки в качестве элемента input сквозной всплывающий элемент. Всплывающий элемент закрыть в результате освещения закроет описанных выше действий и будет передавать события касания назначенным `OverlayInputPassThroughElement`. Рассмотрим внедряют это поведение, чтобы ускорить взаимодействия с пользователем на функционально сходных элементов. Если ваше приложение имеет коллекцию в Избранное и каждый элемент в коллекции содержит присоединенных всплывающего, следует предположить, что пользователям может потребоваться взаимодействовать с несколькими всплывающие элементы в последовательные.

[!NOTE] Старайтесь не назначить наложение сквозной элемента input что приводит к деструктивных действий. Пользователи становятся habituated отдельная света закрыть действия, которые не активирует основной пользовательский Интерфейс. Закрыть, удалить или аналогично деструктивными кнопки не должна активировать индикатор закрывать, чтобы избежать непредвиденных и нарушить работу.

В следующем примере будут активированы все три кнопки внутри FavoritesBar на первом касании.

````xaml
<Page>
    <Page.Resources>
        <Flyout x:Name="TravelFlyout" x:Key="TravelFlyout"
                OverlayInputPassThroughElement="{x:Bind FavoritesBar}">
            <StackPanel>
                <HyperlinkButton Content="Washington Trails Association"/>
                <HyperlinkButton Content="Washington Cascades - Go Northwest! A Travel Guide"/>  
            </StackPanel>
        </Flyout>
    </Page.Resources>

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <StackPanel x:Name="FavoritesBar" Orientation="Horizontal">
            <HyperlinkButton x:Name="PageLinkBtn">Bing</HyperlinkButton>  
            <Button x:Name="Folder1" Content="Travel" Flyout="{StaticResource TravelFlyout}"/>
            <Button x:Name="Folder2" Content="Entertainment" Click="Folder2_Click"/>
        </StackPanel>
        <ScrollViewer Grid.Row="1">
            <WebView x:Name="WebContent"/>
        </ScrollViewer>
    </Grid>
</Page>
````
````csharp
private void Folder2_Click(object sender, RoutedEventArgs e)
{
     Flyout flyout = new Flyout();
     flyout.OverlayInputPassThroughElement = FavoritesBar;
     ...
     flyout.ShowAt(sender as FrameworkElement);
{
````

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме
- [Подсказки](tooltips.md)
- [Меню и контекстное меню](menus.md)
- [Класс Flyout](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Flyout)
- [Класс ContentDialog](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)
