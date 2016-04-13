---
title: Создание и использование службы приложения
description: Узнайте, как написать приложение универсальной платформы Windows (UWP), которое может предоставлять службы другим приложениям UWP, и как использовать эти службы.
ms.assetid: 6E48B8B6-D3BF-4AE2-85FB-D463C448C9D3
---

# Создание и использование службы приложения


\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Узнайте, как написать приложение универсальной платформы Windows (UWP), которое может предоставлять службы другим приложениям UWP, и как использовать эти службы.

## Создание проекта поставщика службы приложений


В этой инструкции для простоты мы создадим универсальное решение.

-   В Microsoft Visual Studio 2015 создайте проект приложения UWP и назовите его AppServiceProvider. (Для этого в диалоговом окне **Создание проекта** последовательно выберите пункты **Шаблоны &gt; Другие языки &gt; Visual C# &gt; Windows &gt; Универсальные &gt; Пустое приложение (универсальное приложение Windows)**.) Это приложение будет предоставлять службу приложений.

## Добавление в файл package.appxmanifest расширения службы приложений


В файле Package.appxmanifest проекта AppServiceProvider добавьте указанное ниже расширение AppService в элемент **&lt;Application&gt;**. В этом примере показано, как объявить службу `com.Microsoft.Inventory` и указать, что приложение является поставщиком службы приложений. Сама служба будет реализована в виде фоновой задачи. Приложение службы приложений предоставляет службу другим приложениям. Для имени службы мы рекомендуем использовать обратное доменное имя.

``` syntax
... 
<Applications>
    <Application Id="App"
      Executable="$targetnametoken$.exe"
      EntryPoint="AppServiceProvider.App">
      <Extensions>
        <uap:Extension Category="windows.appService" EntryPoint="MyAppService.Inventory">
          <uap:AppService Name="com.microsoft.inventory"/>
        </uap:Extension>
      </Extensions>
      ...
    </Application>
</Applications>
```

Благодаря атрибуту **Category** это приложение идентифицируется как поставщик службы приложений.

Атрибут **EntryPoint** идентифицирует класс, реализующий службу, которую мы реализуем позже.

## Создание службы приложений


1.  Служба приложений будет реализована в виде фоновой задачи. Это позволяет приложению переднего плана вызывать службу приложений в другом приложении, чтобы выполнять задачи незаметно. Добавьте в решение новый проект компонента среды выполнения Windows (**Файл > Добавить > Создать проект**) с именем MyAppService. (Для этого в диалоговом окне **Добавление нового проекта** последовательно выберите пункты **Установленные &gt; Другие языки &gt; Visual C# &gt; Windows &gt; Универсальные &gt; Компонент среды выполнения Windows (универсальное приложение Windows)**.)
2.  Добавьте в проект AppServiceProvider ссылку на проект MyAppService.
3.  В проекте MyappService добавьте следующие операторы **using** в верхнюю часть файла Class1.cs:
    ```cs
    using Windows.ApplicationModel.AppService;
    using Windows.ApplicationModel.Background;
    using Windows.Foundation.Collections;
    ```

4.  Замените код заглушки для класса **Class1** новым классом фоновой задачи с именем **Inventory**:

    ```cs
    public sealed class Inventory : IBackgroundTask
    {
        private BackgroundTaskDeferral backgroundTaskDeferral;
        private AppServiceConnection appServiceconnection;
        private String[] inventoryItems = new string[] { "Robot vacuum", "Chair" };
        private double[] inventoryPrices = new double[] { 129.99, 88.99 };

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            this.backgroundTaskDeferral = taskInstance.GetDeferral(); // Get a deferral so that the service isn&#39;t terminated.
            taskInstance.Canceled += OnTaskCanceled; // Associate a cancellation handler with the background task.

            // Retrieve the app service connection and set up a listener for incoming app service requests.
            var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
            appServiceconnection = details.AppServiceConnection;
            appServiceconnection.RequestReceived += OnRequestReceived;
        }

        private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
        {
        }

        private void OnTaskCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
        {
            if (this.backgroundTaskDeferral != null)
            {
                // Complete the service deferral.
                this.backgroundTaskDeferral.Complete();
            }
        }
    }
    ```

    Это класс, в котором служба приложений будет выполнять свою работу.

    После создания фоновой задачи будет вызван метод **Run()**. Так как по завершении работы метода **Run** работа фоновых задач также завершается, код создает отсрочку, и фоновая задача может продолжать обрабатывать запросы.

    При отмене задачи будет вызван метод **OnTaskCanceled()**. Отмена задачи происходит в следующих случаях: клиентское приложение ликвидирует [**AppServiceConnection**](https://msdn.microsoft.com/library/windows/apps/dn921704), клиентское приложение приостановлено, ОС завершает работу или переходит в режим сна или когда ОС исчерпала все ресурсы для выполнения задачи.

## Написание кода для службы приложений


Код службы приложений помещается в метод **OnRequestedReceived()**. Замените заглушку **OnRequestedReceived()** в файле Class1.cs проекта MyAppService кодом из этого примера. Этот код получает индекс складской позиции и передает его в строке команды в службу, чтобы получить название и цену указанной складской позиции. Код обработки ошибки был удален для краткости.

```cs
private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
{
    // Get a deferral because we use an awaitable API below to respond to the message
    // and we don&#39;t want this call to get cancelled while we are waiting.
    var messageDeferral = args.GetDeferral();

    ValueSet message = args.Request.Message;
    ValueSet returnData = new ValueSet();

    string command = message["Command"] as string;
    int? inventoryIndex = message["ID"] as int?;

    if ( inventoryIndex.HasValue &amp;&amp;
         inventoryIndex.Value >= 0 &amp;&amp;
         inventoryIndex.Value < inventoryItems.GetLength(0))
    {
        switch (command)
        {
            case "Price":
            {
                returnData.Add("Result", inventoryPrices[inventoryIndex.Value]);
                returnData.Add("Status", "OK");
                break;
            }

            case "Item":
            {
                returnData.Add("Result", inventoryItems[inventoryIndex.Value]);
                returnData.Add("Status", "OK");
                break;
            }

            default:
            {
                returnData.Add("Status", "Fail: unknown command");
                break;
            }
        }
    }
    else
    {
        returnData.Add("Status", "Fail: Index out of range");
    }

    await args.Request.SendResponseAsync(returnData); // Return the data to the caller.
    messageDeferral.Complete(); // Complete the deferral so that the platform knows that we&#39;re done responding to the app service call.
}
```

Обратите внимание, что метод **OnRequestedReceived()** принадлежит к типу **async**, так как в данном примере мы выполняем поддерживающий ожидание вызов метода [**SendResponseAsync**](https://msdn.microsoft.com/library/windows/apps/dn921722).

Отсрочка используется для того, чтобы служба могла использовать методы **async** в обработчике OnRequestReceived. Это гарантирует, что вызов OnRequestReceived не завершится до тех пор, пока не будет обработано сообщение. [**SendResponseAsync**](https://msdn.microsoft.com/library/windows/apps/dn921722) используется для отправки отклика по завершении. **SendResponseAsync** не сигнализирует о завершении вызова. Сигнал для [**SendMessageAsync**](https://msdn.microsoft.com/library/windows/apps/dn921712) о завершении OnRequestReceived передается по завершении отсрочки.

Для обмена информацией службы приложений используют [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131). Размер данных, которые можно передать, ограничен только ресурсами системы. Не существует предопределенных параметров, которые можно использовать в **ValueSet**. Вам необходимо решить, какие значения параметров вы будете использовать для определения протокола для службы приложений. Вызывающий код необходимо писать с учетом этого протокола. В этом примере мы выбрали параметр с именем "Command", значение которого указывает, что должна предоставить служба приложений: сведения об имени складской позиции или ее цене. Индекс имени складской позиции хранится в параметре "ID". Возвращаемое значение хранится в параметре "Result".

Вызывающему коду возвращается перечисление [**AppServiceClosedStatus**](https://msdn.microsoft.com/library/windows/apps/dn921703), указывающее на то, был ли вызов службы приложений успешным. Сбой вызова службы приложений может возникнуть, если ОС прерывает конечную точку службы, исчерпаны все ресурсы и т. д. Вы можете возвращать дополнительные сведения об ошибке с помощью [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131). В этом примере мы используем параметр с именем "Status", чтобы возвращать в вызывающий код более подробные сведения об ошибке.

Вызов [**SendResponseAsync**](https://msdn.microsoft.com/library/windows/apps/dn921722) возвращает [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131) в вызывающий код.

## Развертывание приложения службы и получение имени семейства пакетов


Необходимо развернуть приложение поставщика служб приложений, прежде чем можно будет вызывать его из клиента. Кроме того, чтобы вызвать приложение службы приложений, вам понадобится имя его семейства пакетов.

-   Один из способов получить имя семейства пакетов приложения службы приложений состоит в том, чтобы получить свойство [**Windows.ApplicationModel.Package.Current.Id.FamilyName**](https://msdn.microsoft.com/library/windows/apps/br224670) из проекта **AppServiceProvider** (например, из метода `public App()` в файле App.xaml.cs) и запомнить его значение. Чтобы запустить AppServiceProvider в Microsoft Visual Studio, настройте его в качестве запускаемого проекта в окне обозревателя решений и запустите проект.
-   Другой способ получить имя семейства пакетов — это развернуть решение (для этого последовательно выберите пункты **Сборка > Развернуть решение**) и запомнить полное имя пакета, которое отображается в окне вывода (для этого последовательно выберите пункты **Вид > Вывод**). Чтобы получить имя пакета, необходимо удалить сведения о платформе из строки в окне вывода. Например, если в окне вывода отображается полное имя пакета "9fe3058b-3de0-4e05-bea7-84a06f0ee4f0\_1.0.0.0\_x86\_\_yd7nk54bq29ra", то следует удалить подстроку "1.0.0.0\_x86\_\_", в результате чего вы получите имя семейства пакетов "9fe3058b-3de0-4e05-bea7-84a06f0ee4f0\_yd7nk54bq29ra".

## Написание клиента для вызова службы приложений


1.  Добавьте в решение новый пустой проект универсального приложения для Windows (для этого последовательно выберите пункты **Файл > Добавить > Создать проект**) с именем ClientApp. (Для этого в диалоговом окне **Добавление нового проекта** последовательно выберите пункты **Установленные &gt; Другие языки &gt; Visual C# &gt; Windows &gt; Универсальные &gt; Пустое приложение (универсальное приложение Windows)**.)
2.  В проекте ClientApp добавьте следующий оператор **using** в верхнюю часть файла MainPage.xaml.cs:
    ```cs
    >using Windows.ApplicationModel.AppService;
    ```

3.  Добавьте текстовое поле и кнопку в файл MainPage.xaml.
4.  Добавьте обработчик нажатия кнопки и ключевое слово **async** в подпись обработчика кнопки.
5.  Замените заглушку обработчика нажатия кнопки указанным ниже кодом. Не забудьте включить объявление поля `inventoryService`.

   ```cs
   private AppServiceConnection inventoryService;
    private async void button_Click(object sender, RoutedEventArgs e)
    {
        // Add the connection.
        if (this.inventoryService == null)
        {
            this.inventoryService = new AppServiceConnection();

            // Here, we use the app service name defined in the app service provider&#39;s Package.appxmanifest file in the &lt;Extension&gt; section. 
            this.inventoryService.AppServiceName = "com.microsoft.inventory";

            // Use Windows.ApplicationModel.Package.Current.Id.FamilyName within the app service provider to get this value.
            this.inventoryService.PackageFamilyName = "replace with the package family name";

            var status = await this.inventoryService.OpenAsync();
            if (status != AppServiceConnectionStatus.Success)
            {
                button.Content = "Failed to connect";
                return;
            }
        }

        // Call the service.
        int idx = int.Parse(textBox.Text);
        var message = new ValueSet();
        message.Add("Command", "Item");
        message.Add("ID", idx);
        AppServiceResponse response = await this.inventoryService.SendMessageAsync(message);
        string result = "";

        if (response.Status == AppServiceResponseStatus.Success)
        {
            // Get the data  that the service sent  to us.
            if (response.Message["Status"] as string == "OK")
            {
                result = response.Message["Result"] as string;
            }
        }

        message.Clear();
        message.Add("Command", "Price");
        message.Add("ID", idx);
        response = await this.inventoryService.SendMessageAsync(message);

        if (response.Status == AppServiceResponseStatus.Success)
        {
            // Get the data that the service sent to us.
            if (response.Message["Status"] as string == "OK")
            {
                result += " : Price = " + response.Message["Result"] as string;
            }
        }

        button.Content = result;
    }
    ```

    Replace the package family name in the line `this.inventoryService.PackageFamilyName = "replace with the package family name";` with the package family name of the **AppServiceProvider** project that you obtained in \[Step 5: Deploy the service app and get the package family name\].

    The code first establishes a connection with the app service. The connection will remain open until you dispose **this.inventoryService**. The app service name must match the **AppService Name** attribute that you added to the AppServiceProvider project's Package.appxmanifest file. In this example, it is `<uap:AppService Name="com.microsoft.inventory"/>`.

    A [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131) named **message** is created to specify the command that we want to send to the app service. The example app service expects a command to indicate which of two actions to take. We get the index from the textbox in the ClientApp, and then call the service with the "Item" command to get the description of the item. Then, we make the call with the "Price" command to get the item's price. The button text is set to the result.

    Because [**AppServiceResponseStatus**](https://msdn.microsoft.com/library/windows/apps/dn921724) only indicates whether the operating system was able to connect the call to the app service, we check the "Status" key in the [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131) we receive from the app service to ensure that it was able to fulfill the request.

6.  In Visual Studio, set the ClientApp project to be the startup project in the Solution Explorer window and run the solution. Enter the number 1 into the text box and click the button. You should get "Chair : Price = 88.99" back from the service.

    ![sample app displaying chair price=88.99](images/appserviceclientapp.png)

If the app service call fails, check the following in the ClientApp:

1.  Verify that the package family name assigned to the inventory service connection matches the package family name of the AppServiceProvider app. See: **button\_Click()**`this.inventoryService.PackageFamilyName = "...";`).
2.  In **button\_Click()**, verify that the app service name that is assigned to the inventory service connection matches the app service name in the AppServiceProvider's Package.appxmanifest file. See: `this.inventoryService.AppServiceName = "com.microsoft.inventory";`.
3.  Ensure that the AppServiceProvider app has been deployed (In the Solution Explorer, right-click the solution and choose **Deploy**).

## Debug the app service


1.  Ensure that the entire solution is deployed before debugging because the app service provider app must be deployed before the service can be called. (In Visual Studio, **Build &gt; Deploy Solution**).
2.  In the Solution Explorer, right-click the AppServiceProvider project and choose **Properties**. From the **Debug** tab, change the **Start action** to **Do not launch, but debug my code when it starts**.
3.  In the MyAppService project, in the Class1.cs file, set a breakpoint in OnRequestReceived().
4.  Set the AppServiceProvider project to be the startup project and press F5.
5.  Start ClientApp from the Start menu (not from Visual Studio).
6.  Enter the number 1 into the text box and press the button. The debugger will stop in the app service call on the breakpoint in your app service.

## Debug the client


1.  Follow the instructions in the preceding step to debug the app service.
2.  Launch ClientApp from the Start menu.
3.  Attach the debugger to the ClientApp.exe process (not the ApplicationFrameHost.exe process). (In Visual Studio, choose **Debug &gt; Attach to Process...**.)
4.  In the ClientApp project, set a breakpoint in **button\_Click()**.
5.  The breakpoints in both the client and the app service will now be hit when you enter the number 1 into the text box of the ClientApp and click the button.

## Remarks


This example provides a simple introduction to creating an app service and calling it from another app. The key things to note are the creation of a background task to host the app service, the addition of the windows.appservice extension to the app service provider app's Package.appxmanifest file, obtaining the package family name of the app service provider app so that we can connect to it from the client app, and using [**Windows.ApplicationModel.AppService.AppServiceConnection**](https://msdn.microsoft.com/library/windows/apps/dn921704) to call the service.

## Full code for MyAppService


```cs
using System;
using Windows.ApplicationModel.AppService;
using Windows.ApplicationModel.Background;
using Windows.Foundation.Collections;

namespace MyAppService
{
    public sealed class Inventory : IBackgroundTask
    {
        private BackgroundTaskDeferral backgroundTaskDeferral;
        private AppServiceConnection appServiceconnection;
        private String[] inventoryItems = new string[] { "Robot vacuum", "Chair" };
        private double[] inventoryPrices = new double[] { 129.99, 88.99 };

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            this.backgroundTaskDeferral = taskInstance.GetDeferral(); // Get a deferral so that the service isn&#39;t terminated.
            taskInstance.Canceled += OnTaskCanceled; // Associate a cancellation handler with the background task.

            // Retrieve the app service connection and set up a listener for incoming app service requests.
            var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
            appServiceconnection = details.AppServiceConnection;
            appServiceconnection.RequestReceived += OnRequestReceived;
        }

        private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
        {
            // Get a deferral because we use an awaitable API below to respond to the message
            // and we don&#39;t want this call to get cancelled while we are waiting.
            var messageDeferral = args.GetDeferral();

            ValueSet message = args.Request.Message;
            ValueSet returnData = new ValueSet();

            string command = message["Command"] as string;
            int? inventoryIndex = message["ID"] as int?;

            if (inventoryIndex.HasValue &amp;&amp;
                 inventoryIndex.Value >= 0 &amp;&amp;
                 inventoryIndex.Value < inventoryItems.GetLength(0))
            {
                switch (command)
                {
                    case "Price":
                        {
                            returnData.Add("Result", inventoryPrices[inventoryIndex.Value]);
                            returnData.Add("Status", "OK");
                            break;
                        }

                    case "Item":
                        {
                            returnData.Add("Result", inventoryItems[inventoryIndex.Value]);
                            returnData.Add("Status", "OK");
                            break;
                        }

                    default:
                        {
                            returnData.Add("Status", "Fail: unknown command");
                            break;
                        }
                }
            }
            else
            {
                returnData.Add("Status", "Fail: Index out of range");
            }

            await args.Request.SendResponseAsync(returnData); // Return the data to the caller.
            messageDeferral.Complete(); // Complete the deferral so that the platform knows that we&#39;re done responding to the app service call.
        }


        private void OnTaskCanceled(IBackgroundTaskInstance sender, BackgroundTaskCancellationReason reason)
        {
            if (this.backgroundTaskDeferral != null)
            {
                // Complete the service deferral.
                this.backgroundTaskDeferral.Complete();
            }
        }
    }
}
```

## Связанные разделы


* [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md)

 

 





<!--HONumber=Mar16_HO1-->


