---
title: Создание и использование службы приложений
description: Узнайте, как написать приложение универсальной платформы Windows (UWP), которое может предоставлять службы другим приложениям UWP, и как использовать эти службы.
ms.assetid: 6E48B8B6-D3BF-4AE2-85FB-D463C448C9D3
keywords: обмен данными приложения в приложение, межпроцессного взаимодействия, IPC, фон, обмен сообщениями, фоновой связи, приложения в приложение, приложение службы
ms.date: 01/16/2019
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 96ecad8494ad82dc4927b3675ad322f07a8ca7f3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57643579"
---
# <a name="create-and-consume-an-app-service"></a>Создание и использование службы приложений

Службы приложений — это приложения UWP, которые могут предоставлять службы другим приложениям UWP. Это похоже на веб-службы на устройстве. Служба приложения выполняется как фоновой задачи в приложении узла и обеспечить его службы другим приложениям. Например служба приложения может предоставить службу сканер штрих-кода, которая может использовать другие приложения. Или корпоративный набор приложений может использовать общую службу проверки орфографии, которая доступна всем приложениям в наборе.  Службы приложений позволяют создавать службы без пользовательского интерфейса, которые приложения могут вызывать на одном устройстве и, начиная с Windows 10 версии 1607, на удаленных устройствах.

Начиная с Windows 10 версии 1607 можно создавать службы приложения, работающие в том же процессе, что и приложение узла. В этой статье основное внимание уделяется созданию и использованию служб приложения, которые выполняются в отдельном фоновом процессе. Подробнее о службах приложения, работающих в том же процессе, что и ведущее приложение, см. в разделе [Преобразование службы приложения для запуска в одном процессе с ее поставщиком](convert-app-service-in-process.md).

Пример кода службы приложений см. на странице с [примерами приложений для универсальной платформы Windows (UWP)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices).

## <a name="create-a-new-app-service-provider-project"></a>Создание проекта поставщика службы приложений

В этой инструкции для простоты мы создадим универсальное решение.

1. В Visual Studio 2015 или более поздней версии, создайте новый проект приложения универсальной платформы Windows и назовите его **AppServiceProvider**.
    1. Выберите **файл > Создать > проект...** 
    2. В **новый проект** выберите **установленные > Visual C# > пустое приложение (универсальные Windows)**. Это приложение будет предоставлять службу приложения другим приложениям UWP.
    3. Назовите проект **AppServiceProvider**, выберите расположение для нее и нажмите кнопку **ОК**.

2. Ответ на приглашение выберите **целевой** и **Минимальная версия** для проекта, выберите по крайней мере **10.0.14393**. Если вы хотите использовать новый **SupportsMultipleInstances** атрибут, необходимо использовать Visual Studio 2017 и целевой **10.0.15063** (**Windows 10 Creators Update**) или более поздней версии.

<span id="appxmanifest"/>

## <a name="add-an-app-service-extension-to-packageappxmanifest"></a>Добавить расширение службы приложений в Package.appxmanifest

В **AppServiceProvider** откройте проект **Package.appxmanifest** файл в текстовом редакторе: 

1. Щелкните правой кнопкой мыши в **обозревателе решений**. 
2. Выберите **открыть с помощью**. 
3. Выберите **редактор (текстовый) XML**. 

Добавьте следующий `AppService` расширения внутри `<Application>` элемент. В этом примере показано, как объявить службу `com.microsoft.inventory` и указать, что приложение является поставщиком службы приложений. Сама служба будет реализована в виде фоновой задачи. Проект приложения службы приложений предоставляет службу другим приложениям. Для имени службы мы рекомендуем использовать обратное доменное имя.

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

`Category` Атрибут определяет это приложение в качестве поставщика службы приложений.

`EntryPoint` Атрибут определяет пространство имен класса, реализующего службу, которая затем реализуется.

`SupportsMultipleInstances` Атрибут указывает, что при каждом вызове службы приложений, он должен выполняться в новом процессе. Это не является обязательным, но доступных пользователю, если приложение предназначено для 10.0.15063 и требуются функциональные возможности пакета SDK (**Windows 10 Creators Update**) или более поздней версии. В качестве префикса следует использовать пространство имен `uap4`.

## <a name="create-the-app-service"></a>Создание службы приложений

1.  Служба приложений будет реализована в виде фоновой задачи. Это позволяет приложению переднего плана вызывать службу приложений в другом приложении. Чтобы создать службу приложений как фоновая задача, добавить в решение новый проект компонента среды выполнения Windows (**файл &gt; добавить &gt; новый проект**) с именем **MyAppService**. В **Добавление нового проекта** диалоговое окно, выберите **установленные > Visual C# > компонент среды выполнения Windows (универсальных Windows)**.
2.  В **AppServiceProvider** проекта, добавьте ссылку проекта на проект в новый **MyAppService** проекта (в **обозревателе решений**, щелкните правой кнопкой мыши на  **AppServiceProvider** проекта > **добавить** > **ссылку** > **проекты**  >   **Решение**выберите **MyAppService** > **ОК**). Этот шаг очень важен: если вы не добавите ссылку, служба приложения не сможет подключиться во время выполнения.
3.  В **MyAppService** проекта, добавьте следующий **с помощью** операторы в верхнюю часть **Class1.cs**:
    ```cs
    using Windows.ApplicationModel.AppService;
    using Windows.ApplicationModel.Background;
    using Windows.Foundation.Collections;
    ```

4.  Переименуйте **Class1.cs** для **Inventory.cs**и замените код заглушки для **Class1** с новый класс фоновой задачи с именем **инвентаризации**:

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

    **Запустите** вызывается, когда создается фоновая задача. Так как по завершении работы метода **Run** работа фоновых задач также завершается, код создает отсрочку, и фоновая задача может продолжать обрабатывать запросы. Службы приложений, реализованы в виде фоновой задачи будет оставаться активным в течение около 30 секунд после получает вызов, если только он снова вызывается в пределах заданного периода времени или изъятие отсрочки. Если в службе приложений реализована в том же процессе, что и вызывающий объект, время существования службы приложений привязывается к времени существования вызывающего объекта.

    Срок жизни службы приложения зависит от вызывающей стороны.

    * Если вызывающий объект находится на переднем плане, время существования приложения службы является таким же, как вызывающий объект.
    * Если вызывающий объект находится в фоновом режиме, в службе приложений получает 30 секунд для выполнения. Активация задержки дает службе пять дополнительных секунд.

    **OnTaskCanceled** вызывается, когда задача была отменена. Задача отменяется, когда клиентское приложение удаляет [AppServiceConnection](https://msdn.microsoft.com/library/windows/apps/dn921704), клиентского приложения приостанавливается, ОС завершается или переходит в спящий режим или операционной системе не хватает ресурсов для выполнения задачи.

## <a name="write-the-code-for-the-app-service"></a>Написание кода для службы приложений

**OnRequestReceived** — от назначения кода для службы приложений. Замените заглушку **OnRequestReceived** в **MyAppService** **Inventory.cs** с кодом из этого примера. Этот код получает индекс складской позиции и передает его в строке команды в службу, чтобы получить название и цену указанной складской позиции. В собственные проекты добавьте код обработки ошибок.

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

Обратите внимание, что **OnRequestReceived** — **async** так, как awaitable метод вызов сделан [SendResponseAsync](https://msdn.microsoft.com/library/windows/apps/dn921722) в этом примере.

Отсрочки берется таким образом, служба может использовать **async** методы в **OnRequestReceived** обработчика. Это гарантирует, что вызов **OnRequestReceived** не завершится до тех пор, пока не будет обработано сообщение.  [SendResponseAsync](https://msdn.microsoft.com/library/windows/apps/dn921722) отправляет результат вызывающей стороне. **SendResponseAsync** не сигнализирует о завершении вызова. Это задержка, сообщающий о завершении [SendMessageAsync](https://msdn.microsoft.com/library/windows/apps/dn921712) , **OnRequestReceived** завершения. Вызов **SendResponseAsync** заключается в блок try/finally, так как необходимо выполнить, даже если отсрочки **SendResponseAsync** возникло исключение.

Приложения служб, использующих [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131) объектов для обмена данными. Размер данных, которые можно передать, ограничен только ресурсами системы. Не существует предопределенных параметров, которые можно использовать в **ValueSet**. Вам необходимо решить, какие значения параметров вы будете использовать для определения протокола для службы приложений. Вызывающий код необходимо писать с учетом этого протокола. В этом примере мы выбрали параметр с именем `Command`, значение которого указывает, что должна предоставить служба приложений: сведения об имени складской позиции или ее цене. Индекс имени складской позиции хранится в параметре `ID`. Возвращаемое значение хранится в параметре `Result`.

[AppServiceClosedStatus](https://msdn.microsoft.com/library/windows/apps/dn921703) enum возвращается вызывающему объекту указать, успешно ли выполнено вызов службы приложений. Сбой вызова службы приложений может возникнуть, если ОС прерывает конечную точку службы, так как ее ресурсы исчерпаны. Может возвращать Дополнительные сведения об ошибке с помощью [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131). В этом примере мы используем параметр `Status`, чтобы возвращать в вызывающий код более подробные сведения об ошибке.

Вызов [SendResponseAsync](https://msdn.microsoft.com/library/windows/apps/dn921722) возвращает [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131) вызывающему объекту.

## <a name="deploy-the-service-app-and-get-the-package-family-name"></a>Развертывание приложения службы и получение имени семейства пакетов

Поставщик службы приложений должны быть развернуты перед вызовом его из клиента. Его можно развернуть, выбрав **сборки > развернуть решение** в Visual Studio.

Также необходимо будет имя семейства пакетов поставщика службы приложения для его вызова. Это можно сделать, открыв **AppServiceProvider** проекта **Package.appxmanifest** файл в представлении конструктора (дважды щелкните его в **обозревателе решений**). Выберите **упаковки** вкладке, скопируйте значение рядом с полем **имени семейства пакетов**и сохраните его в блокноте, сейчас.

## <a name="write-a-client-to-call-the-app-service"></a>Написание клиента для вызова службы приложений

1.  Добавьте в решение новый пустой проект универсального приложения для Windows (для этого последовательно выберите пункты **Файл &gt; Добавить &gt; Создать проект**). В **Добавление нового проекта** диалоговое окно, выберите **установленные > Visual C# > пустое приложение (универсальные Windows)** и назовите его **ClientApp**.

2.  В **ClientApp** проекта, добавьте следующий **с помощью** инструкцию в верхнюю часть **MainPage.xaml.cs**:
    ```cs
    using Windows.ApplicationModel.AppService;
    ```

3.  Добавьте текстовое поле, называемое **textBox** и кнопка для **MainPage.xaml**.

4.  Добавление кнопки щелкните обработчик для кнопки вызывается **button_Click**и добавьте ключевое слово **async** сигнатуру обработчик кнопки.

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
    > Убедитесь в том, что для вставки в строковый литерал, а не поместить его в переменной. Он не будет работать при использовании переменной.

    Сначала код устанавливает связь со службой приложения. Подключение останется открытым, пока вы не удалите объект `this.inventoryService`. Имя службы приложений должно соответствовать `AppService` элемента `Name` атрибут, добавленный к **AppServiceProvider** проекта **Package.appxmanifest** файла. В этом примере это `<uap3:AppService Name="com.microsoft.inventory"/>`.

    Объект [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131) с именем `message` создается для задания текста команды, нужно отправить в службу приложений. Пример службы приложения будет ждать команду, чтобы указать, какое из 2 действий необходимо выполнить. Мы получить индекс из текстового поля в клиентском приложении, а затем вызвать службу `Item` команду, чтобы получить описание элемента. Затем с помощью команды `Price` мы получаем цену элемента. Результат используется в качестве текста кнопки.

    Так как [AppServiceResponseStatus](https://msdn.microsoft.com/library/windows/apps/dn921724) определяет только то, удалось ли операционная система для совершения вызова в службе приложений, мы проверяем `Status` в [ValueSet](https://msdn.microsoft.com/library/windows/apps/dn636131) мы получаем от приложения службы, чтобы убедиться, что он смог выполнить запрос.

6. Задайте **ClientApp** запускаемого проекта (щелкните правой кнопкой мыши в **обозревателе решений** > **Назначить запускаемым проектом**) и запустить решение. Введите число 1 в текстовое поле и нажмите кнопку. Вы должны получить «Chair: Цена = 88.99" службой обратно.

    ![Образец приложения, в котором отображаются данные: Chair price=88.99](images/appserviceclientapp.png)

Если вызов службы приложения завершается ошибкой, проверьте следующее **ClientApp** проекта:

1.  Убедитесь, что имя семейства пакетов, назначенный соединению инвентаризации службы совпадает имя семейства пакетов из **AppServiceProvider** приложения. См. в строке **кнопку\_щелкните** с `this.inventoryService.PackageFamilyName = "...";`.
2.  В **кнопку\_щелкните**, убедитесь, что имя службы приложений, присвоенный подключения службы инвентаризации совпадает имя службы приложений в **AppServiceProvider**в  **Package.appxmanifest** файл. См. `this.inventoryService.AppServiceName = "com.microsoft.inventory";`.
3.  Убедитесь, что **AppServiceProvider** приложение развернуто. (В **обозревателе решений**, щелкните правой кнопкой мыши решение и выберите пункт **развернуть решение**).

## <a name="debug-the-app-service"></a>Отладка службы приложений

1.  Перед отладкой убедитесь, что решение развернуто, так как перед вызовом службы необходимо развернуть приложение поставщика службы приложений. (Для этого в Visual Studio последовательно выберите пункты **Сборка &gt; Развернуть решение**).
2.  В **обозревателе решений**, щелкните правой кнопкой мыши **AppServiceProvider** проекта и выберите **свойства**. На вкладке **Отладка** измените значение параметра **Действие при запуске** на **Не запускать, а отлаживать мой код при открытии**. (Обратите внимание, что при использовании C++ для реализации поставщика службы приложений на вкладке **Отладка** необходимо изменить значение параметра **Запуск приложения** на **Нет**.)
3.  В **MyAppService** проекта **Inventory.cs** файл, установите точку останова в **OnRequestReceived**.
4.  Задайте **AppServiceProvider** проекта запускаемого проекта и нажмите клавишу **F5**.
5.  Запуск **ClientApp** из меню "Пуск" (не из Visual Studio).
6.  Введите число 1 в текстовое поле и нажмите кнопку. Отладчик остановится на вызове службы приложений в точке ее останова.

## <a name="debug-the-client"></a>Отладка клиента

1.  Для отладки клиента, вызывающего службу приложений, выполните инструкции из предыдущего шага.
2.  Запустите **ClientApp** из меню "Пуск".
3.  Подключить отладчик к **ClientApp.exe** процесса (не **ApplicationFrameHost.exe** процесс). (В Visual Studio последовательно выберите пункты **Отладка &gt; Присоединиться к процессу...**).
4.  В **ClientApp** проектов, установите точку останова в **кнопку\_щелкните**.
5.  Теперь будут достигнуты точки останова в службе приложений и клиент, при вводе числа 1 в текстовое поле из **ClientApp** и нажмите кнопку.

## <a name="general-app-service-troubleshooting"></a>Устранение общих неполадок службы приложений

При возникновении **AppUnavailable** состояние после попытки подключения к службе приложений, проверьте следующее:

- Убедитесь, что проект поставщика службы приложений и проект службы приложений развернуты. Необходимо развернуть оба проекта перед запуском клиента, так как в противном случае клиенту не к чему будет подключаться. Развертывание можно выполнить в Visual Studio с помощью команды **Сборка** > **Развернуть решение**.
- В **обозревателе решений**, убедитесь, что проект приложения службы поставщика имеет ссылку проекта на проект в проект, который реализует службу приложений.
- Убедитесь, что `<Extensions>` запись и его дочерние элементы были добавлены **Package.appxmanifest** файлов, относящихся к проекту поставщик службы приложений, как указано выше в [добавлять расширения службы приложений Package.appxmanifest](#appxmanifest).
- Убедитесь, что [AppServiceConnection.AppServiceName](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.appservicename) соответствует строке в ваш клиент, который вызывает поставщика службы приложения `<uap3:AppService Name="..." />` указанным в проекте приложения службы поставщика **Package.appxmanifest**  файл.
- Убедитесь, что [AppServiceConnection.PackageFamilyName](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.packagefamilyname) соответствует имя семейства пакетов компонента приложения службы поставщика, как указано выше в [добавить расширение службы приложений в Package.appxmanifest](#appxmanifest)
- Убедитесь, что для служб приложений вне процесса, такую как в этом примере, `EntryPoint` указано в `<uap:Extension ...>` элемент из проекта приложения службы поставщика **Package.appxmanifest** файл соответствует пространству имен и Имя класса открытый класс, реализующий [IBackgroundTask](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask) в проекте службы приложений.

### <a name="troubleshoot-debugging"></a>Устранение неполадок отладки

Если отладчик не останавливается на точках останова в проекте поставщика службы приложений или проекте службы приложений, выполните следующие действия.

- Убедитесь, что проект поставщика службы приложений и проект службы приложений развернуты. Оба проекта должны быть развернуты перед запуском клиента. Развертывание можно выполнить в Visual Studio с помощью команды **Сборка** > **Развернуть решение**.
- Убедитесь, что проект, необходимо выполнить отладку задан в качестве запускаемого проекта и, чтобы не запустить проект задаются свойства отладки для этого проекта при **F5** нажата. Щелкните правой кнопкой мыши проект, выберите пункт **Свойства** и щелкните **Отладка** (или **Отладка** в C++). При работе на C# измените значение параметра **Действие при запуске** на **Не запускать, а отлаживать мой код при открытии**. При работе на C++ задайте для параметра **Запуск приложения** значение **Нет**.

## <a name="remarks"></a>Замечания

В этом примере показано, как создать службу приложений, которая выполняется как фоновая задача, и как вызвать ее из другого приложения. Ключа обратить внимание:

* Создайте в фоновом режиме для размещения службы приложений.
* Добавить `windows.appService` расширение для поставщика службы приложения **Package.appxmanifest** файла.
* Получите имя семейства пакетов поставщика службы приложения, чтобы мы могли подключаться к нему из клиентского приложения.
* Добавьте ссылку проекта на проект из проекта приложения службы поставщика в проект приложения службы.
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

## <a name="related-topics"></a>Статьи по теме

* [Преобразование службы приложений для запуска в одном процессе с ведущим приложением](convert-app-service-in-process.md)
* [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md)
* [Пример кода для приложения службы (C#, C++ и VB)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices)
