---
Description: Диалоговые окна и всплывающие элементы используются для временного отображения элементов пользовательского интерфейса по запросу пользователя, либо если происходит нечто, требующее уведомления или подтверждения.
title: Диалоговые элементы управления
label: Dialogs
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: ad6affd9-a3c0-481f-a237-9a1ecd561be8
pm-contact: yulikl
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: d5014532be613e8284a1e2d6496f71b9f2b8e892
ms.sourcegitcommit: 5002836b3c8d3e0f2970da1333ec8f42da861ae3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77071256"
---
# <a name="dialog-controls"></a>Диалоговые элементы управления

Диалоговые элементы управления — это модальные наложения пользовательского интерфейса, которые предоставляют контекстную информацию о приложении. Они блокируют взаимодействие с окном приложения, пока пользователь явно их не закроет. Они часто требуют от пользователя совершения каких-либо действий.

![Пример диалогового окна](../images/dialogs/dialog_RS2_delete_file.png)


> **Важные API**: [Класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Диалоговые окна используются для уведомления пользователей о важной информации или запроса подтверждения либо дополнительных сведений перед совершением действия.

Рекомендации по целесообразности использования диалоговых окон и всплывающих элементов (аналогичных элементов управления) приведены в разделе [Диалоговые окна и всплывающие элементы](index.md). 

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="../images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">XAML Controls Gallery</strong>, щелкните здесь, чтобы открыть его и увидеть <a href="xamlcontrolsgallery:/item/ContentDialog">ContentDialog</a> или <a href="xamlcontrolsgallery:/item/Flyout">Flyout</a> в действии.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
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
    -   Убедитесь, что диалоговое окно имеет по крайней мере одну кнопку, соответствующую безопасному обратимому действию, например "Понятно", "Закрыть" или "Отмена". Используйте API CloseButton, чтобы добавить эту кнопку.
    -   В качестве текста кнопки используйте конкретные варианты ответа на основную инструкцию или содержимое. Например: "Разрешить приложению AppName доступ к вашему расположению?", после которого следуют кнопки "Разрешить" и "Запретить". Конкретные варианты ответа воспринимаются быстрее, что помогает эффективно принимать решения.
    - Убедитесь, что текст кнопки действия является кратким. Короткие строки позволяют пользователю сделать выбор быстро и уверенно.
    - Помимо безопасного обратимого действия можно при необходимости представить пользователю одну или две кнопки действия, связанные с основной инструкцией. Эти кнопки действия "выполнить" подтверждают главную цель диалогового окна. Используйте API SecondaryButton и PrimaryButton для добавления этих действий "выполнить".
    - Кнопки действий "выполнить" должны отображаться как крайние левые кнопки. Безопасное обратимое действие должно быть представлено правой кнопкой.
    - При необходимости можно выделить одну из трех кнопок в качестве кнопки диалогового окна по умолчанию. Используйте API DefaultButton для выделения одной из кнопок.  
-   Не используйте диалоговые окна при возникновении контекстно-зависимых от определенной области на странице ошибок, таких как ошибки при проверке (например, в полях для ввода паролей). Используйте непосредственно элемент Canvas приложения для отображения внутренних ошибок.
- Используйте [класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog) для создания элементов диалогового окна. Не используйте устаревший API MessageDialog.

## <a name="how-to-create-a-dialog"></a>Как создать диалоговое окно
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

## <a name="provide-a-safe-action"></a>Предоставление безопасного действия
Так как диалоговые окна блокируют взаимодействие с пользователем, а кнопки — это основной механизм, позволяющий пользователям закрыть диалоговое окно, убедитесь, что диалоговое окно содержит по крайней мере одну кнопку безопасного и обратимого действия, например "Закрыть" или "Понятно". **Все диалоговые окна должны содержать по крайней мере одну кнопку безопасного действия, позволяющую закрыть диалоговое окно.** Это гарантирует, что пользователь сможет спокойно закрыть диалоговое окно без выполнения какого-либо действия.<br>![Диалоговое окно с одной кнопкой](../images/dialogs/dialog_RS2_one_button.png)

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

Когда диалоговые окна используются для отображения блокирующего вопроса, диалоговое окно должно содержать кнопки действий, связанные с этим вопросом. Кнопка "безопасного" и обратимого действия может сопровождаться одной или двумя кнопками действия "выполнить". Предлагая пользователю несколько вариантов на выбор, убедитесь, что кнопки точно соответствуют действиям "выполнить" и безопасным действиям "не выполнять", связанным с предложенным вопросом.

![Диалоговое окно с двумя кнопками](../images/dialogs/dialog_RS2_two_button.png)

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

Диалоговые окна с тремя кнопками используются, когда вы предлагаете пользователю два действия "выполнить" и одно действие "не выполнять". Диалоговые окна с тремя кнопками следует использовать не слишком часто. В них должны четко различаться дополнительное действие и безопасное действие (закрытие).

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

## <a name="the-three-dialog-buttons"></a>Три кнопки в диалоговом окне
ContentDialog поддерживает три типа кнопки, которые можно использовать для создания диалоговых окон.

- **CloseButton** (обязательный) представляет безопасное, обратимое действие, которое позволяет пользователю закрыть диалоговое окно. Отображается в виде правой кнопки.
- **PrimaryButton** (необязательный) представляет первое действие "выполнить". Отображается в виде левой кнопки.
- **SecondaryButton** (необязательный) представляет второе действие "выполнить". Отображается в виде средней кнопки.

Использование встроенных кнопок позволяет их правильно разместить в окне, а также гарантирует, что они будут правильно реагировать на события клавиатуры, что область команд останется видимой, даже когда отображена экранная клавиатура, и что диалоговое окно будет выглядеть согласованно с другими диалоговыми окнами.

### <a name="closebutton"></a>CloseButton
Каждое диалоговое окно должно содержать кнопку безопасного и обратимого действия, которая позволяет пользователям спокойно закрыть диалоговое окно.

Используйте API ContentDialog.CloseButton для создания этой кнопки. Это позволит обеспечить правильное взаимодействие с пользователем для всех методов ввода, включая мышь, клавиатуру, сенсорный ввод и игровой контроллер. Это взаимодействие будет происходить в следующих случаях:
<ol>
    <li>пользователь нажимает кнопку CloseButton или касается ее; </li>
    <li>пользователь нажимает системную кнопку "Назад"; </li>
    <li>пользователь нажимает клавишу "ESC" на клавиатуре; </li>
    <li>пользователь нажимает кнопку "B" игрового контроллера. </li>
</ol>

Когда пользователь нажимает кнопку диалогового кона, метод [ShowAsync](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает [ContentDialogResult](/uwp/api/Windows.UI.Xaml.Controls.ContentDialogResult), чтобы сообщить вам, какую кнопку нажал пользователь. При нажатии кнопки CloseButton возвращается ContentDialogResult.None.

### <a name="primarybutton-and-secondarybutton"></a>PrimaryButton и SecondaryButton
Помимо CloseButton, вы можете также предоставить пользователю одну или две кнопки действия, связанные с основной инструкцией.
Используйте PrimaryButton для первого действия "выполнить" и SecondaryButton — для второго действия "выполнить". В диалоговых окнах с тремя кнопками PrimaryButton обычно представляет подтверждающее действие "выполнить", а SecondaryButton обычно представляет нейтральное или вспомогательное действие "выполнить".
Например, приложение может предложить пользователю подписаться на службу. PrimaryButton в качестве подтверждающего действия "выполнить" будет содержать текст "Подписаться", а SecondaryButton в качестве нейтрального действия "выполнить" будет содержать текст "Попробовать". CloseButton позволит пользователю отменить операцию без выполнения какого-либо действия.

Когда пользователь нажимает кнопку PrimaryButton, метод [ShowAsync](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает ContentDialogResult.Primary.
Когда пользователь нажимает кнопку SecondaryButton, метод [ShowAsync](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog.ShowAsync) возвращает ContentDialogResult.Secondary.

![Диалоговое окно с тремя кнопками](../images/dialogs/dialog_RS2_three_button.png)

### <a name="defaultbutton"></a>DefaultButton
При необходимости можно выделить одну из трех кнопок в качестве кнопки по умолчанию. Если задать кнопку по умолчанию, то происходит следующее.
- Кнопка оформляется как кнопка подтверждения действия.
- Кнопка будет автоматически реагировать на нажатие клавиши "ВВОД".
    - Когда пользователь нажимает клавишу "ВВОД" на клавиатуре, активируется обработчик нажатия, связанный с кнопкой, и ContentDialogResult вернет значение, связанное с кнопкой по умолчанию.
    - Если пользователь поместил фокус клавиатуры на элемент управления, который обрабатывает кнопку "ВВОД", то кнопка по умолчанию не будет реагировать на нажатия клавиши "ВВОД".
- Фокус перейдет на эту кнопку автоматически при открытии диалогового окна, если оно содержит элементы пользовательского интерфейса, поддерживающие фокус.

Используйте свойство ContentDialog.DefaultButton, чтобы указать кнопку по умолчанию. По умолчанию кнопка по умолчанию не задана.

![Кнопка по умолчанию в диалоговом окне с тремя кнопками](../images/dialogs/dialog_RS2_three_button_default.png)

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

## <a name="contentdialog-in-appwindow-or-xaml-islands"></a>ContentDialog в объектах AppWindow или XAML Island

> ПРМЕЧАНИЕ. Этот раздел относится только к приложениям, предназначенным для Windows 10 версии 1903 или более поздней версии. Объекты AppWindow и XAML Island недоступны в более ранних версиях. Дополнительные сведения об управлении версиями см. в статье [Приложения с адаптивным к версии кодом](../../../debug-test-perf/version-adaptive-apps.md).

По умолчанию диалоговые окна содержимого модально связаны с корневым объектом [ApplicationView](/uwp/api/windows.ui.viewmanagement.applicationview). При использовании ContentDialog в объекте [AppWindow](/uwp/api/windows.ui.windowmanagement.appwindow) или [XAML Island](/windows/apps/desktop/modernize/xaml-islands) необходимо вручную задать [XamlRoot](/uwp/api/windows.ui.xaml.uielement.xamlroot) для диалогового окна, указав корень узла XAML.

Для этого задайте для свойства XamlRoot элемента ContentDialog то же значение XamlRoot, что и у элемента, уже заданного в AppWindow или XAML Island, как показано ниже.

```csharp
private async void DisplayNoWifiDialog()
{
    ContentDialog noWifiDialog = new ContentDialog
    {
        Title = "No wifi connection",
        Content = "Check your connection and try again.",
        CloseButtonText = "Ok"
    };

    // Use this code to associate the dialog to the appropriate AppWindow by setting
    // the dialog's XamlRoot to the same XamlRoot as an element that is already present in the AppWindow.
    if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 8))
    {
        noWifiDialog.XamlRoot = elementAlreadyInMyAppWindow.XamlRoot;
    }

    ContentDialogResult result = await noWifiDialog.ShowAsync();
}
```

> [!WARNING]
> В потоке может существовать только один элемент ContentDialog одновременно. Попытка открыть два элемента ContentDialogs породит исключение, даже если они пытаются открыться в отдельных объектах AppWindow.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи
- [Подсказки](../tooltips.md)
- [Меню и контекстное меню](../menus.md)
- [Класс Flyout](/uwp/api/Windows.UI.Xaml.Controls.Flyout)
- [Класс ContentDialog](/uwp/api/Windows.UI.Xaml.Controls.ContentDialog)
