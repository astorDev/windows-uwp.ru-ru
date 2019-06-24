---
title: Запуск приложения для результатов
description: Узнайте, как запустить приложение из другого приложения и обмениваться данными между двумя приложениями. Эта процедура называется запуском приложения для результатов.
ms.assetid: AFC53D75-B3DD-4FF6-9FC0-9335242EE327
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 64a093ddd8a53d72ccb6780b73f280e7b2874612
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67320952"
---
# <a name="launch-an-app-for-results"></a>Запуск приложения для результатов




**Важные API**

-   [**LaunchUriForResultsAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriforresultsasync)
-   [**ValueSet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet)

Узнайте, как запустить приложение из другого приложения и обмениваться данными между двумя приложениями. Эта процедура называется *запуском приложения для результатов*. В приведенном примере показано, как запустить приложение для результатов с помощью метода [**LaunchUriForResultsAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriforresultsasync).

Обмен данными нового приложения в приложение API в Windows 10 делают возможным для Windows (и Windows веб-приложениях) для запуска приложения и обмена данными и файлов. Это позволяет выполнять построение смешанных решений из нескольких приложений. С помощью этих новых API сложные задачи, для которых раньше приходилось использовать несколько приложений, теперь можно выполнять без проблем. Например, ваше приложение может запустить приложение социальной сети для выбора контакта или приложение для оформления заказа, чтобы завершить оплату.

Приложение, которое запускается для результатов, называется запущенным приложением. Приложение, которое запускает другое приложение, называется вызывающим приложением. В этом примере мы создадим и вызывающее, и запущенное приложение.

## <a name="step-1-register-the-protocol-to-be-handled-in-the-app-that-youll-launch-for-results"></a>Шаг 1. Регистрация протокола для обработки в приложении, запускаемую для результатов


В файле Package.appxmanifest запущенного приложения добавьте расширение протокола в раздел **&lt;Приложение&gt;** . В этом примере используется вымышленный протокол с именем **test-app2app**.

Атрибут **ReturnResults** в расширении протокола принимает одно из указанных ниже значений.

-   **optional** — приложение может запускаться для результатов с помощью метода [**LaunchUriForResultsAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriforresultsasync) или не для результатов с помощью метода [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync). Если вы используете ключевое слово **optional**, то запущенное приложение должно определить, было ли оно запущено для результатов. Это можно сделать путем проверки аргумента события [**OnActivated**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onactivated). Если свойство аргумента [**IActivatedEventArgs.Kind**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.iactivatedeventargs.kind) возвращает перечисление [**ActivationKind.ProtocolForResults**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ActivationKind) или тип аргумента события равен [**ProtocolActivatedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ProtocolActivatedEventArgs), то приложение было запущено с помощью метода **LaunchUriForResultsAsync**.
-   **always** — приложение может быть запущено только для результатов, то есть оно может реагировать только на метод [**LaunchUriForResultsAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriforresultsasync).
-   **none** — приложение не может быть запущено для результатов, то есть оно может реагировать только на метод [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync).

В этом примере расширения протокола приложение можно запустить только для результатов. Это упрощает логику в методе **OnActivated**, описанном ниже, так как нам придется обрабатывать только случай запуска для результатов, а не другие возможные способы активации приложения.

```xml
<Applications>
   <Application ...>

     <Extensions>
       <uap:Extension Category="windows.protocol">
         <uap:Protocol Name="test-app2app" ReturnResults="always">
           <uap:DisplayName>Test app-2-app</uap:DisplayName>
         </uap:Protocol>
       </uap:Extension>
     </Extensions>

   </Application>
</Applications>
```

## <a name="step-2-override-applicationonactivated-in-the-app-that-youll-launch-for-results"></a>Шаг 2. Переопределить Application.OnActivated в приложении, запускаемую для результатов


Если этого метода уже нет в запущенном приложении, создайте его внутри класса `App`, определенного в файле App.xaml.cs.

В приложении, которое позволяет выбирать друзей в социальной сети, эта функция может быть размещена там, где вы выбираете страницу выбора людей. В примере ниже страница с именем **LaunchedForResultsPage** отображается, если приложение активировано для результатов. Убедитесь, что оператор **using** включен в начало файла.

```cs
using Windows.ApplicationModel.Activation;
...
protected override void OnActivated(IActivatedEventArgs args)
{
    // Window management
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame == null)
    {
        rootFrame = new Frame();
        Window.Current.Content = rootFrame;
    }

    // Code specific to launch for results
    var protocolForResultsArgs = (ProtocolForResultsActivatedEventArgs)args;
    // Open the page that we created to handle activation for results.
    rootFrame.Navigate(typeof(LaunchedForResultsPage), protocolForResultsArgs);

    // Ensure the current window is active.
    Window.Current.Activate();
}
```

Поскольку расширение протокола в файле Package.appxmanifest определяет **ReturnResults** как **always**, указанный код может приводить `args` непосредственно в [**ProtocolForResultsActivatedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ProtocolForResultsActivatedEventArgs), и мы можем быть уверенны, что только свойство **ProtocolForResultsActivatedEventArgs** будет передано в метод **OnActivated** этого приложения. Если ваше приложение может активироваться способами, отличными от запуска для результатов, можно проверить, возвращает ли свойство [**IActivatedEventArgs.Kind**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.iactivatedeventargs.kind) перечисление [**ActivationKind.ProtocolForResults**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ActivationKind), чтобы узнать, было ли приложение запущено для результатов.

## <a name="step-3-add-a-protocolforresultsoperation-field-to-the-app-you-launch-for-results"></a>Шаг 3. Добавление поля ProtocolForResultsOperation в приложение запуска для результатов


```cs
private Windows.System.ProtocolForResultsOperation _operation = null;
```

Вы сможете использовать поле [**ProtocolForResultsOperation**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.protocolforresultsactivatedeventargs.protocolforresultsoperation), чтобы сообщить, когда запущенное приложение будет готово вернуть результат вызывающему приложению. В этом примере поле добавлено в класс **LaunchedForResultsPage**, так как вы будете выполнять операцию запуска для результатов с этой страницы и вам понадобится соответствующий доступ.

## <a name="step-4-override-onnavigatedto-in-the-app-you-launch-for-results"></a>Шаг 4. Переопределить OnNavigatedTo() в приложении запуска для результатов


Переопределите метод [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto) на странице, которая будет отображена при запуске приложения для результатов. Если этот метод отсутствует, создайте его внутри класса для страницы, определенной в файле &lt;pagename&gt;.xaml.cs. Убедитесь, что следующий оператор **using** включен в начало файла:

```cs
using Windows.ApplicationModel.Activation
```

Объект [**NavigationEventArgs**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Navigation.NavigationEventArgs) в методе [**OnNavigatedTo**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.onnavigatedto) содержит данные, переданные из вызывающего приложения. Данные хранятся в объекте [**ValueSet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet), а их объем не может превышать 100 КБ.

В этом примере кода запущенное приложение ожидает, что данные, отправленные из вызывающего приложения, будут находиться в разделе с именем **TestData** класса [**ValueSet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet), так как вызывающее приложение запрограммировано отправлять их именно таким образом.

```cs
using Windows.ApplicationModel.Activation;
...
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    var protocolForResultsArgs = e.Parameter as ProtocolForResultsActivatedEventArgs;
    // Set the ProtocolForResultsOperation field.
    _operation = protocolForResultsArgs.ProtocolForResultsOperation;

    if (protocolForResultsArgs.Data.ContainsKey("TestData"))
    {
        string dataFromCaller = protocolForResultsArgs.Data["TestData"] as string;
    }
}
...
private Windows.System.ProtocolForResultsOperation _operation = null;
```

## <a name="step-5-write-code-to-return-data-to-the-calling-app"></a>Шаг 5. Написание кода для возврата данных для вызывающего приложения


В приложении, запущенном для результатов, используйте команду [**ProtocolForResultsOperation**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.activation.protocolforresultsactivatedeventargs.protocolforresultsoperation) для возвращения данных вызывающему приложению. В этом примере кода создается объект [**ValueSet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet), который содержит значение для возвращения в вызывающее приложение. Затем для отправки значения в вызывающее приложение используется поле **ProtocolForResultsOperation**.

```cs
    ValueSet result = new ValueSet();
    result["ReturnedData"] = "The returned result";
    _operation.ReportCompleted(result);
```

## <a name="step-6-write-code-to-launch-the-app-for-results-and-get-the-returned-data"></a>Шаг 6. Написать код, чтобы запустить приложение для результатов и получения возвращаемых данных


Запустите приложение из асинхронного метода в вашем вызывающем приложении, как показано в примере кода ниже. Обратите внимание, что для компиляции кода требуются следующие операторы **using**:

```cs
using System.Threading.Tasks;
using Windows.System;
...

async Task<string> LaunchAppForResults()
{
    var testAppUri = new Uri("test-app2app:"); // The protocol handled by the launched app
    var options = new LauncherOptions();
    options.TargetApplicationPackageFamilyName = "67d987e1-e842-4229-9f7c-98cf13b5da45_yd7nk54bq29ra";

    var inputData = new ValueSet();
    inputData["TestData"] = "Test data";

    string theResult = "";
    LaunchUriResult result = await Windows.System.Launcher.LaunchUriForResultsAsync(testAppUri, options, inputData);
    if (result.Status == LaunchUriStatus.Success &&
        result.Result != null &&
        result.Result.ContainsKey("ReturnedData"))
    {
        ValueSet theValues = result.Result;
        theResult = theValues["ReturnedData"] as string;
    }
    return theResult;
}
```

В этом примере [**ValueSet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet) с параметром **TestData** передается в запущенное приложение. Запущенное приложение создает **ValueSet** с параметром **ReturnedData**, который содержит результат, возвращаемый вызывающей стороне.

Перед запуском вызывающего приложения следует собрать и развернуть приложение, которое будет запущено для результатов. В противном случае [**LaunchUriResult.Status**](https://docs.microsoft.com/uwp/api/Windows.System.LaunchUriStatus) сообщит об ошибке **LaunchUriStatus.AppUnavailable**.

Вам потребуется указать имя семейства запущенного приложения, чтобы задать значение [**TargetApplicationPackageFamilyName**](https://docs.microsoft.com/uwp/api/windows.system.launcheroptions.targetapplicationpackagefamilyname). Один из способов получить имя семейства — это выполнить следующий вызов из запущенного приложения:

```cs
string familyName = Windows.ApplicationModel.Package.Current.Id.FamilyName;
```

## <a name="remarks"></a>Примечания


В примере из этой инструкции представлены основы по запуску приложения для результатов. Следует помнить о том, что новый API [**LaunchUriForResultsAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriforresultsasync) позволяет запускать приложение асинхронно и поддерживать связь посредством класса [**ValueSet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet). Объем передаваемых через **ValueSet** данных не может превышать 100 КБ. Если нужно передать больший объем данных, вы можете поделиться файлами, используя класс [**SharedStorageAccessManager**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.DataTransfer.SharedStorageAccessManager), чтобы создать маркеры файлов, которые можно передавать между приложениями. Например, если у вас есть **ValueSet** с именем `inputData`, вы можете сохранить маркер в файле, которым нужно поделиться с запущенным приложением:

```cs
inputData["ImageFileToken"] = SharedStorageAccessManager.AddFile(myFile);
```

Затем передайте маркер в запущенное приложение с помощью метода **LaunchUriForResultsAsync**.

## <a name="related-topics"></a>См. также


* [**LaunchUri**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync)
* [**LaunchUriForResultsAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriforresultsasync)
* [**ValueSet**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet)

 

 
