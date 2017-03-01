---
author: TylerMSFT
title: "Создание и использование службы приложения"
description: "Узнайте, как написать приложение универсальной платформы Windows (UWP), которое может предоставлять службы другим приложениям UWP, и как использовать эти службы."
ms.assetid: 6E48B8B6-D3BF-4AE2-85FB-D463C448C9D3
keywords: "связь между приложениями, межпроцессная связь, межпроцессное взаимодействие, фоновые сообщения, фоновая связь, от приложения к приложению"
ms.author: twhitney
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 3dcf6a8191553deac5821346718a202bc362c7ff
ms.lasthandoff: 02/07/2017

---

# <a name="create-and-consume-an-app-service"></a>Создание и использование службы приложений


\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Узнайте, как написать приложение универсальной платформы Windows (UWP), которое может предоставлять службы другим приложениям UWP, и как использовать эти службы.

Начиная с Windows 10 версии 1607 можно создавать службы приложения, работающие в том же процессе, что и приложение узла. В этой статье основное внимание уделяется созданию служб приложения, которые выполняются в отдельном фоновом процессе. Подробнее о службах приложения, работающих в том же процессе, что и ведущее приложение, см. в разделе [Преобразование службы приложения для запуска в одном процессе с ее поставщиком](convert-app-service-in-process.md).

## <a name="create-a-new-app-service-provider-project"></a>Создание проекта поставщика службы приложений

В этой инструкции для простоты мы создадим универсальное решение.

-   В Microsoft Visual Studio 2015 создайте проект приложения UWP и назовите его AppServiceProvider. (Для этого в диалоговом окне **Создание проекта** последовательно выберите пункты **Шаблоны &gt; Другие языки &gt; Visual C# &gt;Windows &gt; Универсальные &gt; Пустое приложение (универсальное приложение Windows**)). Это приложение будет предоставлять службу приложений.

## <a name="add-an-app-service-extension-to-packageappxmanifest"></a>Добавление в файл package.appxmanifest расширения службы приложений

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

## <a name="create-the-app-service"></a>Создание службы приложений

1.  Служба приложений будет реализована в виде фоновой задачи. Это позволяет приложению переднего плана вызывать службу приложений в другом приложении, чтобы выполнять задачи незаметно. Добавьте в решение новый проект компонента среды выполнения Windows (**Файл &gt; Добавить &gt; Создать проект**) с именем MyAppService. (Для этого в диалоговом окне **Добавление нового проекта** последовательно выберите пункты **Установленные &gt; Другие языки &gt; Visual C# &gt; Windows &gt; Универсальные &gt; Компонент среды выполнения Windows (универсальное приложение Windows)**).
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
            this.backgroundTaskDeferral = taskInstance.GetDeferral(); // Get a deferral so that the service isn't terminated.
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

## <a name="write-the-code-for-the-app-service"></a>Написание кода для службы приложений

Код службы приложений помещается в метод **OnRequestedReceived()**. Замените заглушку **OnRequestedReceived()** в файле Class1.cs проекта MyAppService кодом из этого примера. Этот код получает индекс складской позиции и передает его в строке команды в службу, чтобы получить название и цену указанной складской позиции. Код обработки ошибки был удален для краткости.

```cs
private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
{
    // Get a deferral because we use an awaitable API below to respond to the message
    // and we don't want this call to get cancelled while we are waiting.
    var messageDeferral = args.GetDeferral();

    ValueSet message = args.Request.Message;
    ValueSet returnData = new ValueSet();

    string command = message["Command"] as string;
    int? inventoryIndex = message["ID"] as int?;

    if ( inventoryIndex.HasValue &&
         inventoryIndex.Value >= 0 &&
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
    messageDeferral.Complete(); // Complete the deferral so that the platform knows that we're done responding to the app service call.
}
```

Обратите внимание, что метод **OnRequestedReceived()** принадлежит к типу **async**, так как в данном примере мы выполняем поддерживающий ожидание вызов метода [**SendResponseAsync**](https://msdn.microsoft.com/library/windows/apps/dn921722).

Отсрочка используется для того, чтобы служба могла использовать методы **async** в обработчике OnRequestReceived. Это гарантирует, что вызов OnRequestReceived не завершится до тех пор, пока не будет обработано сообщение. [**SendResponseAsync**](https://msdn.microsoft.com/library/windows/apps/dn921722) используется для отправки отклика по завершении. **SendResponseAsync** не сигнализирует о завершении вызова. Сигнал для [**SendMessageAsync**](https://msdn.microsoft.com/library/windows/apps/dn921712) о завершении OnRequestReceived передается по завершении отсрочки.

Для обмена информацией службы приложений используют [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131). Размер данных, которые можно передать, ограничен только ресурсами системы. Не существует предопределенных параметров, которые можно использовать в **ValueSet**. Вам необходимо решить, какие значения параметров вы будете использовать для определения протокола для службы приложений. Вызывающий код необходимо писать с учетом этого протокола. В этом примере мы выбрали параметр с именем "Command", значение которого указывает, что должна предоставить служба приложений: сведения об имени складской позиции или ее цене. Индекс имени складской позиции хранится в параметре "ID". Возвращаемое значение хранится в параметре "Result".

Вызывающему коду возвращается перечисление [**AppServiceClosedStatus**](https://msdn.microsoft.com/library/windows/apps/dn921703), указывающее на то, был ли вызов службы приложений успешным. Сбой вызова службы приложений может возникнуть, если ОС прерывает конечную точку службы, исчерпаны все ресурсы и т. д. Вы можете возвращать дополнительные сведения об ошибке с помощью [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131). В этом примере мы используем параметр с именем "Status", чтобы возвращать в вызывающий код более подробные сведения об ошибке.

Вызов [**SendResponseAsync**](https://msdn.microsoft.com/library/windows/apps/dn921722) возвращает [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131) в вызывающий код.

## <a name="deploy-the-service-app-and-get-the-package-family-name"></a>Развертывание приложения службы и получение имени семейства пакетов

Необходимо развернуть приложение поставщика служб приложений, прежде чем можно будет вызывать его из клиента. Кроме того, чтобы вызвать приложение службы приложений, вам понадобится имя его семейства пакетов.

-   Один из способов получить имя семейства пакетов приложения службы приложений состоит в том, чтобы получить свойство [**Windows.ApplicationModel.Package.Current.Id.FamilyName**](https://msdn.microsoft.com/library/windows/apps/br224670) из проекта **AppServiceProvider** (например, из метода `public App()` в файле App.xaml.cs) и запомнить его значение. Чтобы запустить AppServiceProvider в Microsoft Visual Studio, настройте его в качестве запускаемого проекта в окне обозревателя решений и запустите проект.
-   Другой способ получить имя семейства пакетов — это развернуть решение (для этого последовательно выберите пункты **Сборка &gt; Развернуть решение**) и запомнить полное имя пакета, которое отображается в окне вывода (для этого последовательно выберите пункты **Вид &gt; Вывод**). Чтобы получить имя пакета, необходимо удалить сведения о платформе из строки в окне вывода. Например, если в окне вывода отображается полное имя пакета "9fe3058b-3de0-4e05-bea7-84a06f0ee4f0\_1.0.0.0\_x86\_\_yd7nk54bq29ra", то следует удалить подстроку "1.0.0.0\_x86\_\_", в результате чего вы получите имя семейства пакетов "9fe3058b-3de0-4e05-bea7-84a06f0ee4f0\_yd7nk54bq29ra".

## <a name="write-a-client-to-call-the-app-service"></a>Написание клиента для вызова службы приложений

1.  Добавьте в решение новый пустой проект универсального приложения для Windows (для этого последовательно выберите пункты **Файл &gt; Добавить &gt; Создать проект**) с именем ClientApp. (Для этого в диалоговом окне **Добавление нового проекта** последовательно выберите пункты **Установленные &gt; Другие языки &gt; Visual C# &gt; Windows &gt; Универсальные &gt; Пустое приложение (универсальное приложение Windows)**).
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

            // Here, we use the app service name defined in the app service provider's Package.appxmanifest file in the <Extension> section.
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

    Замените имя семейства пакетов в строке `this.inventoryService.PackageFamilyName = "replace with the package family name";` именем семейства пакета проекта **AppServiceProvider**, который вы получили в \[Разделе 5: "Развертывание приложения службы и получение имени семейства пакетов"].

    Сначала код устанавливает связь со службой приложения. Подключение останется открытым, пока вы не удалите объект**this.inventoryService**. Имя службы приложения должно соответствовать атрибуту **AppService Name**, который вы добавили в файл Package.appxmanifest проекта AppServiceProvider. В этом примере это `<uap:AppService Name="com.microsoft.inventory"/>`.

    Сообщение с именем [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131) **** создается, чтобы определить команду, которую требуется отправить службе приложения. Пример службы приложения будет ждать команду, чтобы указать, какое из 2 действий необходимо выполнить. Мы получаем индекс из текстового поля в ClientApp и вызываем службу с командой "Элемент", чтобы получить описание элемента. Затем с помощью команды "Цена" мы получаем цену элемента. Результат используется в качестве текста кнопки.

    Так как [**AppServiceResponseStatus**](https://msdn.microsoft.com/library/windows/apps/dn921724) показывает только то, удалось ли операционной системе подключить вызов к службе приложения, мы проверяем ключ "Status" key в классе [**ValueSet**](https://msdn.microsoft.com/library/windows/apps/dn636131), который мы получаем от службы приложений, чтобы убедиться, что ей удалось выполнить запрос.

6.  В Visual Studio настройте проект ClientApp в качестве запускаемого проекта в окне обозревателя решений и запустите решение. Введите число 1 в текстовое поле и нажмите кнопку. Служба возвратит следующие данные: "Chair : Price = 88.99".

    ![Образец приложения, в котором отображаются данные: Chair price=88.99](images/appserviceclientapp.png)

Если не удается выполнить вызов службы приложений, выполните указанные ниже проверки для ClientApp.

1.  Убедитесь, что имя семейства пакетов, назначенное подключению к службе складских запасов, совпадает с именем семейства пакетов приложения AppServiceProvider. См. метод: **button\_Click()**`this.inventoryService.PackageFamilyName = "...";`).
2.  В методе **button\_Click()** проверьте, что имя службы приложений, назначенное подключению к службе складских запасов, совпадает с именем службы приложений в файле Package.appxmanifest проекта AppServiceProvider. См. `this.inventoryService.AppServiceName = "com.microsoft.inventory";`.
3.  Убедитесь, что приложение AppServiceProvider развернуто (в обозревателе решений щелкните правой кнопкой мыши решение и выберите пункт **Развернуть**).

## <a name="debug-the-app-service"></a>Отладка службы приложений


1.  Перед отладкой убедитесь, что решение развернуто полностью, так как перед вызовом службы необходимо развернуть приложение поставщика службы приложений. (Для этого в Visual Studio последовательно выберите пункты **Сборка &gt; Развернуть решение**).
2.  В обозревателе решений щелкните правой кнопкой мыши проект AppServiceProvider и выберите пункт **Свойства**. На вкладке **Отладка** измените значение параметра **Действие при запуске** на **Не запускать, а отлаживать мой код при открытии**.
3.  В проекте MyAppService в файле Class1.cs установите точку останова в методе OnRequestReceived().
4.  Настройте проект AppServiceProvider в качестве запускаемого и нажмите клавишу F5.
5.  Запустите ClientApp из меню "Пуск" (не из Visual Studio).
6.  Введите число 1 в текстовое поле и нажмите кнопку. Отладчик остановится на вызове службы приложений в точке ее останова.

## <a name="debug-the-client"></a>Отладка клиента

1.  Для отладки службы приложений выполните инструкции из предыдущего шага.
2.  Запустите ClientApp из меню "Пуск".
3.  Подключите отладчик к процессу ClientApp.exe (а не к процессу ApplicationFrameHost.exe). (В Visual Studio последовательно выберите пункты **Отладка &gt; Присоединиться к процессу...**).
4.  В проекте ClientApp установите точку останова в **button\_Click()**.
5.  Теперь, если вы введете число 1 в текстовое поле приложения ClientApp и нажмете кнопку, сработают точки останова и в клиенте, и в службе приложений.

## <a name="remarks"></a>Примечания

В этом простом примере показано, как создать службу приложений и вызвать ее из другого приложения. Ключевые моменты, которые следует запомнить, — это создание фоновой задачи для размещения службы приложений, добавление расширения windows.appservice в файл Package.appxmanifest приложения поставщика службы приложений, получение имени семейства пакетов приложения поставщика службы приложений (чтобы к нему можно было подключиться из клиентского приложения) и использование [**Windows.ApplicationModel.AppService.AppServiceConnection**](https://msdn.microsoft.com/library/windows/apps/dn921704) для вызова службы.

## <a name="full-code-for-myappservice"></a>Полный код MyAppService

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
            this.backgroundTaskDeferral = taskInstance.GetDeferral(); // Get a deferral so that the service isn't terminated.
            taskInstance.Canceled += OnTaskCanceled; // Associate a cancellation handler with the background task.

            // Retrieve the app service connection and set up a listener for incoming app service requests.
            var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
            appServiceconnection = details.AppServiceConnection;
            appServiceconnection.RequestReceived += OnRequestReceived;
        }

        private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
        {
            // Get a deferral because we use an awaitable API below to respond to the message
            // and we don't want this call to get cancelled while we are waiting.
            var messageDeferral = args.GetDeferral();

            ValueSet message = args.Request.Message;
            ValueSet returnData = new ValueSet();

            string command = message["Command"] as string;
            int? inventoryIndex = message["ID"] as int?;

            if (inventoryIndex.HasValue &&
                 inventoryIndex.Value >= 0 &&
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
            messageDeferral.Complete(); // Complete the deferral so that the platform knows that we're done responding to the app service call.
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

## <a name="related-topics"></a>Связанные статьи

* [Преобразование службы приложения для ее запуска в одном процессе с ведущим приложением](convert-app-service-in-process.md)
* [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md)

