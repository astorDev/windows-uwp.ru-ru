---
title: Создание и использование службы приложения
description: Узнайте, как написать приложение универсальной платформы Windows (UWP), которое может предоставлять службы другим приложениям UWP, и как использовать эти службы.
ms.assetid: 6E48B8B6-D3BF-4AE2-85FB-D463C448C9D3
keywords: приложения для связи между приложениями, межпроцессная связь, Межпроцессное взаимодействие, фоновые сообщения, фоновая связь, приложения в приложение, приложение служба
ms.date: 1/16/2019
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 9029c8ee3a930e66ebdbd0c4d0681d87486a8393
ms.sourcegitcommit: 6e2027f8ebc1d891d27ea6b2e4676d592871bcc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "9011264"
---
# <a name="create-and-consume-an-app-service"></a>Создание и использование службы приложений

Службы приложений— это приложения UWP, которые могут предоставлять службы другим приложениям UWP. Это похоже на веб-службы на устройстве. Служба приложения выполняется как фоновой задачи в приложении узла и обеспечить его службы другим приложениям. Например служба приложения может предоставить службу сканер штрих-кода, которая может использовать другие приложения. Или корпоративный набор приложений может использовать общую службу проверки орфографии, которая доступна всем приложениям в наборе.  Службы приложений позволяют создавать службы без пользовательского интерфейса, которые приложения могут вызывать на одном устройстве и, начиная с Windows 10 версии 1607, на удаленных устройствах.

Начиная с Windows 10 версии 1607, вы можете создавать службы приложений, выполняющиеся в том же процессе, что и ведущее приложение. В этой статье основное внимание уделяется созданию и использованию служб приложения, которые выполняются в отдельном фоновом процессе. Подробнее о службах приложения, работающих в том же процессе, что и ведущее приложение, см. в разделе [Преобразование службы приложения для запуска в одном процессе с ее поставщиком](convert-app-service-in-process.md).

Пример кода службы приложений см. на странице с [примерами приложений для универсальной платформы Windows (UWP)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices).

## <a name="create-a-new-app-service-provider-project"></a>Создание проекта поставщика службы приложений

В этой инструкции для простоты мы создадим универсальное решение.

1. В Visual Studio 2015 или более поздней версии создайте новый проект приложения UWP и назовите его **AppServiceProvider**.
    1. Выберите **файл > > проект …** 
    2. В диалоговом окне **Новый проект** выберите **Установленные > Visual C# > пустое приложение (универсальное приложение Windows)**. Это приложение будет предоставлять службу приложения другим приложениям UWP.
    3. Назовите проект **AppServiceProvider**, выберите расположение для него и нажмите кнопку **ОК**.

2. При появлении запроса выберите **целевой** и **минимальной версии** проекта, выберите по крайней мере **10.0.14393**. Если вы хотите использовать новый атрибут **SupportsMultipleInstances** , необходимо использовать Visual Studio 2017 и выбрать **10.0.15063** (**Windows 10 Creators Update**) или более поздней версии.

<span id="appxmanifest"/>

## <a name="add-an-app-service-extension-to-packageappxmanifest"></a>Добавление расширения службы приложений в Package.appxmanifest

В проект **AppServiceProvider** откройте файл **Package.appxmanifest** в текстовом редакторе. 

1. Щелкните правой кнопкой мыши его в **Обозревателе решений**. 
2. Выберите **Открыть с помощью**. 
3. Выберите **редактор (текстовый) XML**. 

Добавьте следующий `AppService` расширение `<Application>` элемента. В этом примере показано, как объявить службу `com.microsoft.inventory` и указать, что приложение является поставщиком службы приложений. Сама служба будет реализована в виде фоновой задачи. Проект приложения службы приложений предоставляет службу другим приложениям. Для имени службы мы рекомендуем использовать обратное доменное имя.

Обратите внимание, что префикс пространства имен `xmlns:uap4` и атрибут `uap4:SupportsMultipleInstances` действительны, только если вы используете пакета Windows SDK 10.0.15063 или более поздней версии. Их можно удалить, если используется более ранняя версия SDK.

``` xml
<Package
    ...
    xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
    xmlns:uap4="http://schemas.microsoft.com/appx/manifest/uap/windows10/4"
    ...
    <Applications>
        <Application Id="AppServiceProvider.App"
          Executable="$targetnametoken$.exe"
          EntryPoint="AppServiceProvider.App">
          ...
          <Extensions>
            <uap:Extension Category="windows.appService" EntryPoint="MyAppService.Inventory">
              <uap3:AppService Name="com.microsoft.inventory" uap4:SupportsMultipleInstances="true"/>
            </uap:Extension>
          </Extensions>
          ...
        </Application>
    </Applications>
```

`Category` Атрибут это приложение идентифицируется как поставщик службы приложений.

`EntryPoint` Атрибут идентифицирует класс в пространстве имен, реализующий службу, которую мы реализуем позже.

`SupportsMultipleInstances` Атрибута означает, что при каждом вызове службы приложений, его необходимо запускать в новом процессе. Это не является обязательным, но возможно, если вам необходимы эти функции и вы используете пакет 10.0.15063 SDK (**Windows 10 Creators Update**) или более поздней версии. В качестве префикса следует использовать пространство имен `uap4`.

## <a name="create-the-app-service"></a>Создание службы приложений

1.  Служба приложений будет реализована в виде фоновой задачи. Это позволяет приложению переднего плана вызывать службу приложений в другом приложении. Чтобы создать службу приложения в качестве фоновой задачи, добавьте новый проект компонента среды выполнения Windows в решение (**файл &gt; добавить &gt; новый проект**) с именем **MyAppService**. В диалоговом окне **Добавление нового проекта** выберите **Установленные > Visual C# > компонента среды выполнения Windows (универсальное приложение Windows)**.
2.  В проект **AppServiceProvider** добавьте в проект ссылку на новый проект **MyAppService** (в **Обозревателе решений**щелкните правой кнопкой мыши щелкните > проект **AppServiceProvider** **Добавить**  >  ** Ссылки** > **проекты** > **решений**, выберите **MyAppService** > **OK**). Этот шаг очень важен: если вы не добавите ссылку, служба приложения не сможет подключиться во время выполнения.
3.  В проекте **MyAppService** добавьте следующие операторы **using** в верхнюю часть **файла Class1.cs**:
    ```cs
    using Windows.ApplicationModel.AppService;
    using Windows.ApplicationModel.Background;
    using Windows.Foundation.Collections;
    ```

4.  Переименуйте **файл Class1.cs** на **Inventory.cs**и замените код заглушки для **класса Class1** с новым классом фоновой задачи с именем **инвентаризации**:

    ```cs
    public sealed class Inventory : IBackgroundTask
    {
        private BackgroundTaskDeferral backgroundTaskDeferral;
        private AppServiceConnection appServiceconnection;
        private String[] inventoryItems = new string[] { "Robot vacuum", "Chair" };
        private double[] inventoryPrices = new double[] { 129.99, 88.99 };

        public void Run(IBackgroundTaskInstance taskInstance)
        {
            // Get a deferral so that the service isn't terminated.
            this.backgroundTaskDeferral = taskInstance.GetDeferral();

            // Associate a cancellation handler with the background task.
            taskInstance.Canceled += OnTaskCanceled;

            // Retrieve the app service connection and set up a listener for incoming app service requests.
            var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
            appServiceconnection = details.AppServiceConnection;
            appServiceconnection.RequestReceived += OnRequestReceived;
        }

        private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
        {
            // This function is called when the app service receives a request.
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

    **Запустите** вызывается при создании фоновой задачи. Так как по завершении работы метода **Run** работа фоновых задач также завершается, код создает отсрочку, и фоновая задача может продолжать обрабатывать запросы. Служба приложения, которая реализована как фоновая задача, остается активной около 30секунд после получения вызова, если она не будет вызвана снова в течение этого периода или будет инициализирована задержка. Если служба приложения реализована в том же процессе, что и вызывающий объект, ее жизненный цикл связан с жизненным циклом вызывающего объекта.

    Срок жизни службы приложения зависит от вызывающей стороны.

    * Если вызывающий объект находится на переднем плане, жизненный цикл службы приложения совпадает с вызывающей стороне.
    * Если вызывающий объект находится в фоновом режиме, служба приложения получает 30 секунд для выполнения. Активация задержки дает службе пять дополнительных секунд.

    **OnTaskCanceled** вызывается при отмене задачи. Задача будет отменено, если клиентское приложение ликвидирует [AppServiceConnection](https://msdn.microsoft.com/library/windows/apps/dn921704), клиентское приложение приостановлено, ОС завершает работу или находится в спящем режиме или ОС выполняет ресурсы для выполнения задачи.

## <a name="write-the-code-for-the-app-service"></a>Написание кода для службы приложений

**OnRequestReceived** — это код службы приложений. Замените заглушку **OnRequestReceived** в **файле** **Inventory.cs** с кодом из этого примера. Этот код получает индекс складской позиции и передает его в строке команды в службу, чтобы получить название и цену указанной складской позиции. В собственные проекты добавьте код обработки ошибок.

```cs
private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
{
    // Get a deferral because we use an awaitable API below to respond to the message
    // and we don't want this call to get canceled while we are waiting.
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

    try
    {
        // Return the data to the caller.
        await args.Request.SendResponseAsync(returnData);
    }
    catch (Exception e)
    {
        // Your exception handling code here.
    }
    finally
    {
        // Complete the deferral so that the platform knows that we're done responding to the app service call.
        // Note for error handling: this must be called even if SendResponseAsync() throws an exception.
        messageDeferral.Complete();
    }
}
```

Обратите внимание, что **OnRequestReceived** является **асинхронным** , так как мы выполняем поддерживающий ожидание вызов [SendResponseAsync](https://msdn.microsoft.com/library/windows/apps/dn921722) в этом примере метода.

Таким образом, чтобы служба могла использовать **асинхронные** методы в обработчике **OnRequestReceived** берется задержку. Это гарантирует, что вызов **OnRequestReceived** не завершится до тех пор, пока не будет обработано сообщение.  [SendResponseAsync](https://msdn.microsoft.com/library/windows/apps/dn921722) отправляет результаты вызывающей стороне. **SendResponseAsync** не сигнализирует о завершении вызова. Сигнал для [SendMessageAsync](https://msdn.microsoft.com/library/windows/apps/dn921712) о завершении **OnRequestReceived** передается по завершении отсрочки. Вызов **SendResponseAsync** упаковывается в блоке try/finally, поскольку вы должны завершить отсрочку, даже если **SendResponseAsync** приводит к возникновению исключения.

Службы приложений используют объекты [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131) для обмена данными. Размер данных, которые можно передать, ограничен только ресурсами системы. Не существует предопределенных параметров, которые можно использовать в **ValueSet**. Вам необходимо решить, какие значения параметров вы будете использовать для определения протокола для службы приложений. Вызывающий код необходимо писать с учетом этого протокола. В этом примере мы выбрали параметр с именем `Command`, значение которого указывает, что должна предоставить служба приложений: сведения об имени складской позиции или ее цене. Индекс имени складской позиции хранится в параметре `ID`. Возвращаемое значение хранится в параметре `Result`.

Вызывающему коду возвращается перечисление [AppServiceClosedStatus](https://msdn.microsoft.com/library/windows/apps/dn921703), указывающее на то, был ли вызов службы приложений успешным. Сбой вызова службы приложений может возникнуть, если ОС прерывает конечную точку службы, так как ее ресурсы исчерпаны. Вы можете возвращать дополнительные сведения об ошибке с помощью [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131). В этом примере мы используем параметр `Status`, чтобы возвращать в вызывающий код более подробные сведения об ошибке.

Вызов [SendResponseAsync](https://msdn.microsoft.com/library/windows/apps/dn921722) возвращает [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131) в вызывающий код.

## <a name="deploy-the-service-app-and-get-the-package-family-name"></a>Развертывание приложения службы и получение имени семейства пакетов

Должны быть развернуты поставщика службы приложений, прежде чем можно будет вызывать его из клиента. Также необходимо будет имя семейства пакетов службы приложения для ее вызова.

Один из способов получить имя семейства пакетов приложения службы приложения является вызов [Windows.ApplicationModel.Package.Current.Id.FamilyName](https://msdn.microsoft.com/library/windows/apps/br224670) из в проект **AppServiceProvider** (например, из конструктора **приложения** в ** App.XAML.cs**) и запомнить. Чтобы запустить **AppServiceProvider** в Visual Studio, настройте его в качестве запускаемого проекта в окне **Обозревателя решений** и запустите проект.

Другой способ получить имя семейства пакетов — это развернуть решение (**сборка &gt; развернуть решение**) и запомнить полное имя пакета в окне **вывода** (**представление &gt; вывода**). Необходимо удалить сведения о платформе из строки в окне **вывода** , чтобы получить имя пакета. Например если в окне **вывода** указано полное имя пакета были:

`Microsoft.SDKSamples.AppServicesProvider.CPP_1.0.0.0_x86__8wekyb3d8bbwe`

Затем необходимо извлечь `1.0.0.0\_x86\_\_`, оставив следующее имя семейства пакетов:

`Microsoft.SDKSamples.AppServicesProvider.CPP_8wekyb3d8bbwe`

## <a name="write-a-client-to-call-the-app-service"></a>Написание клиента для вызова службы приложений

1.  Добавьте в решение новый пустой проект универсального приложения для Windows (для этого последовательно выберите пункты **Файл &gt; Добавить &gt; Создать проект**). В диалоговом окне **Добавление нового проекта** выберите **Установленные > Visual C# > пустое приложение (универсальное приложение Windows)** и назовите его **ClientApp**.

2.  В проекте **ClientApp** добавьте следующий оператор **using** в верхнюю часть **файла MainPage.xaml.cs**:
    ```cs
    using Windows.ApplicationModel.AppService;
    ```

3.  Добавьте текстовое поле, называемое **текстовое поле** и кнопку в **файл MainPage.xaml**.

4.  Добавление кнопки обработчик для кнопки под названием **button_Click**нажатия и добавьте ключевое слово **async** подпись обработчика кнопки.

5. Замените заглушку обработчика нажатия кнопки указанным ниже кодом. Не забудьте включить объявление поля `inventoryService`.
    ```cs
   private AppServiceConnection inventoryService;

   private async void button_Click(object sender, RoutedEventArgs e)
   {
       // Add the connection.
       if (this.inventoryService == null)
       {
           this.inventoryService = new AppServiceConnection();

           // Here, we use the app service name defined in the app service 
           // provider's Package.appxmanifest file in the <Extension> section.
           this.inventoryService.AppServiceName = "com.microsoft.inventory";

           // Use Windows.ApplicationModel.Package.Current.Id.FamilyName 
           // within the app service provider to get this value.
           this.inventoryService.PackageFamilyName = "Replace with the package family name";

           var status = await this.inventoryService.OpenAsync();

           if (status != AppServiceConnectionStatus.Success)
           {
               textBox.Text= "Failed to connect";
               this.inventoryService = null;
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
           // Get the data  that the service sent to us.
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

       textBox.Text = result;
   }
   ```
    
    Замените имя семейства пакетов в строке `this.inventoryService.PackageFamilyName = "Replace with the package family name";` именем семейства пакета проекта **AppServiceProvider**, которое вы получили выше в разделе [Развертывание приложения службы и получение имени семейства пакетов](#deploy-the-service-app-and-get-the-package-family-name).

    > [!NOTE]
    > Убедитесь, что для вставки в строковый литерал, а не размещая его в переменной. Он не будет работать, если вы используете переменную.

    Сначала код устанавливает связь со службой приложения. Подключение останется открытым, пока вы не удалите объект `this.inventoryService`. Имя службы приложения должно соответствовать `AppService` элемента `Name` атрибут, который вы добавили в файл **Package.appxmanifest** проекта **AppServiceProvider** . В этом примере это `<uap3:AppService Name="com.microsoft.inventory"/>`.

    [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131) с именем `message` создается для определить команду, которую требуется отправить службе приложения. Пример службы приложения будет ждать команду, чтобы указать, какое из 2 действий необходимо выполнить. Мы получаем индекс из текстового поля в клиентское приложение, а затем вызвать службу с `Item` команду, чтобы получить описание элемента. Затем с помощью команды `Price` мы получаем цену элемента. Результат используется в качестве текста кнопки.

    Так как [AppServiceResponseStatus](https://msdn.microsoft.com/library/windows/apps/dn921724) показывает только то, удалось ли операционной системе подключить вызов к службе приложения, мы проверяем ключ `Status` в классе [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131), который мы получаем от службы приложений, чтобы убедиться, что ей удалось выполнить запрос.

6. Настройте проект **ClientApp** в качестве запускаемого проекта (щелкните правой кнопкой мыши в **Обозревателе решений** > **Назначить запускаемым проектом**) и запустите решение. Введите число 1 в текстовое поле и нажмите кнопку. Служба возвратит следующие данные: "Chair : Price = 88.99".

    ![Образец приложения, в котором отображаются данные: Chair price=88.99](images/appserviceclientapp.png)

Если не удается выполнить вызов службы приложения, указанные ниже проверки в проекте **ClientApp** .

1.  Убедитесь, что имя семейства пакетов, назначенные службе складских совпадает имя семейства пакетов приложения **AppServiceProvider** . См. в строке **button\_Click** с `this.inventoryService.PackageFamilyName = "...";`.
2.  В **button\_Click**убедитесь, что имя службы приложений, которая назначена к службе складских соответствует имени службы приложения в файле **Package.appxmanifest** **AppServiceProvider**проекта. См. `this.inventoryService.AppServiceName = "com.microsoft.inventory";`.
3.  Убедитесь, что приложение **AppServiceProvider** развернуто. (В **Обозревателе решений**щелкните правой кнопкой мыши решение и выберите **Развернуть решение**).

## <a name="debug-the-app-service"></a>Отладка службы приложений

1.  Перед отладкой убедитесь, что решение развернуто, так как перед вызовом службы необходимо развернуть приложение поставщика службы приложений. (Для этого в VisualStudio последовательно выберите пункты **Сборка &gt; Развернуть решение**).
2.  В **Обозревателе решений**щелкните правой кнопкой мыши проект **AppServiceProvider** и выберите **Свойства**. На вкладке **Отладка** измените значение параметра **Действие при запуске** на **Не запускать, а отлаживать мой код при открытии**. (Обратите внимание, что при использовании C++ для реализации поставщика службы приложений на вкладке **Отладка** необходимо изменить значение параметра **Запуск приложения** на **Нет**.)
3.  В проекте **MyAppService** в файле **Inventory.cs** , установите точку останова в **OnRequestReceived**.
4.  Настройте проект **AppServiceProvider** в качестве запускаемого проекта и нажмите клавишу **F5**.
5.  Запустите **ClientApp** из меню "Пуск" (не из Visual Studio).
6.  Введите число 1 в текстовое поле и нажмите кнопку. Отладчик остановится на вызове службы приложений в точке ее останова.

## <a name="debug-the-client"></a>Отладка клиента

1.  Для отладки клиента, вызывающего службу приложений, выполните инструкции из предыдущего шага.
2.  Запустите **ClientApp** из меню "Пуск".
3.  Присоедините отладчик к процессу **ClientApp.exe** (не процесс **ApplicationFrameHost.exe** ). (В VisualStudio последовательно выберите пункты **Отладка &gt; Присоединиться к процессу...**).
4.  В проекте **ClientApp** установите точку останова в **button\_Click**.
5.  В клиента и службы приложения будет теперь сработают точки останова при введите число 1 в текстовое поле **ClientApp** и нажмите кнопку "".

## <a name="general-app-service-troubleshooting"></a>Устранение общих неполадок службы приложений

Столкнувшись статус **AppUnavailable** сообщения об ошибке при попытке подключения к службе приложения, проверьте следующее:

- Убедитесь, что проект поставщика службы приложений и проект службы приложений развернуты. Необходимо развернуть оба проекта перед запуском клиента, так как в противном случае клиенту не к чему будет подключаться. Развертывание можно выполнить в Visual Studio с помощью команды **Сборка** > **Развернуть решение**.
- В **Обозревателе решений**убедитесь, что проект поставщика службы приложения имеет проекта в проект ссылку на проект, который реализует службу приложений.
- Убедитесь, что `<Extensions>` ввода и его дочерние элементы были добавлены в файл **Package.appxmanifest** , принадлежащий проекту поставщика службы приложений, как указано выше в разделе [Добавление расширения службы приложений в Package.appxmanifest](#appxmanifest).
- Убедитесь, что строка [AppServiceConnection.AppServiceName](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.appservicename) в клиенте, которая вызывает поставщика службы приложений совпадает `<uap3:AppService Name="..." />` указанные в файле **Package.appxmanifest** проекта поставщика службы приложений.
- Убедитесь, что [AppServiceConnection.PackageFamilyName](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.packagefamilyname) совпадает с именем семейства пакета компонента поставщика службы приложений, как указано выше в разделе [Добавление расширения службы приложений в Package.appxmanifest](#appxmanifest)
- Для приложения выполняемые вне служб, как в этом примере, убедитесь, что `EntryPoint` указано в `<uap:Extension ...>` элемент файла **Package.appxmanifest** вашего проекта поставщика службы приложений соответствует пространству имен и имя класса открытого класса, реализует [IBackgroundTask](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask) в вашем проекте службы приложений.

### <a name="troubleshoot-debugging"></a>Устранение неполадок отладки

Если отладчик не останавливается на точках останова в проекте поставщика службы приложений или проекте службы приложений, выполните следующие действия.

- Убедитесь, что проект поставщика службы приложений и проект службы приложений развернуты. Оба проекта должны быть развернуты перед запуском клиента. Развертывание можно выполнить в Visual Studio с помощью команды **Сборка** > **Развернуть решение**.
- Убедитесь, что проект, который вы хотите выполнить отладку задан в качестве запускаемого и что свойства отладки для данного проекта заданы в проект не запускается при нажатии кнопки **F5** . Щелкните правой кнопкой мыши проект, выберите пункт **Свойства** и щелкните **Отладка** (или **Отладка** в C++). При работе на C# измените значение параметра **Действие при запуске** на **Не запускать, а отлаживать мой код при открытии**. При работе на C++ задайте для параметра **Запуск приложения** значение **Нет**.

## <a name="remarks"></a>Примечания.

В этом примере показано, как создать службу приложений, которая выполняется как фоновая задача, и как вызвать ее из другого приложения. Ключевых аспектов, которые являются:

* Создание фоновой задачи для размещения службы приложений.
* Добавление `windows.appService` расширение для файла **Package.appxmanifest** приложения поставщика службы.
* Получите имя семейства пакетов приложения поставщика службы, чтобы мы могли подключаться к нему из клиентского приложения.
* Добавьте ссылку на проект из проекта поставщика службы приложений в проект службы приложений.
* Используйте [Windows.ApplicationModel.AppService.AppServiceConnection](https://msdn.microsoft.com/library/windows/apps/dn921704) для вызова службы.

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
            // Get a deferral so that the service isn't terminated.
            this.backgroundTaskDeferral = taskInstance.GetDeferral();

            // Associate a cancellation handler with the background task.
            taskInstance.Canceled += OnTaskCanceled;

            // Retrieve the app service connection and set up a listener for incoming app service requests.
            var details = taskInstance.TriggerDetails as AppServiceTriggerDetails;
            appServiceconnection = details.AppServiceConnection;
            appServiceconnection.RequestReceived += OnRequestReceived;
        }

        private async void OnRequestReceived(AppServiceConnection sender, AppServiceRequestReceivedEventArgs args)
        {
            // Get a deferral because we use an awaitable API below to respond to the message
            // and we don't want this call to get canceled while we are waiting.
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

            // Return the data to the caller.
            await args.Request.SendResponseAsync(returnData);

            // Complete the deferral so that the platform knows that we're done responding to the app service call.
            // Note for error handling: this must be called even if SendResponseAsync() throws an exception.
            messageDeferral.Complete();
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
* [Пример кода службы приложения (C#, C++ и VB)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices)
