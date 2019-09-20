---
title: Создание и использование службы приложений
description: Узнайте, как написать приложение универсальной платформы Windows (UWP), которое может предоставлять службы другим приложениям UWP, и как использовать эти службы.
ms.assetid: 6E48B8B6-D3BF-4AE2-85FB-D463C448C9D3
keywords: взаимодействие между приложениями, межпроцессное взаимодействие, IPC, фоновый обмен сообщениями, фоновое взаимодействие, приложение для приложения, служба приложений
ms.date: 01/16/2019
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 8d6edc49bc97a336b8d722c496c1980a5f9b0efb
ms.sourcegitcommit: d38e2f31c47434cd6dbbf8fe8d01c20b98fabf02
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70393558"
---
# <a name="create-and-consume-an-app-service"></a>Создание и использование службы приложений

Службы приложений — это приложения UWP, которые могут предоставлять службы другим приложениям UWP. Это похоже на веб-службы на устройстве. Служба приложения выполняется как фоновой задачи в приложении узла и обеспечить его службы другим приложениям. Например служба приложения может предоставить службу сканер штрих-кода, которая может использовать другие приложения. Или корпоративный набор приложений может использовать общую службу проверки орфографии, которая доступна всем приложениям в наборе.  Службы приложений позволяют создавать службы без пользовательского интерфейса, которые приложения могут вызывать на одном устройстве и, начиная с Windows 10 версии 1607, на удаленных устройствах.

Начиная с Windows 10 версии 1607 можно создавать службы приложения, работающие в том же процессе, что и приложение узла. В этой статье основное внимание уделяется созданию и использованию служб приложения, которые выполняются в отдельном фоновом процессе. Подробнее о службах приложения, работающих в том же процессе, что и ведущее приложение, см. в разделе [Преобразование службы приложения для запуска в одном процессе с ее поставщиком](convert-app-service-in-process.md).

Пример кода службы приложений см. на странице с [примерами приложений для универсальной платформы Windows (UWP)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices).

## <a name="create-a-new-app-service-provider-project"></a>Создание проекта поставщика службы приложений

В этой инструкции для простоты мы создадим универсальное решение.

1. В Visual Studio 2015 или более поздней версии создайте новый проект приложения UWP и назовите его **аппсервицепровидер**.
    1. Выберите **файл > создать > проект...** 
    2. В диалоговом окне **Создание нового проекта** выберите **пустое приложение (универсальные приложения Windows C#)** . Это приложение будет предоставлять службу приложения другим приложениям UWP.
    3. Нажмите кнопку **Далее**, а затем назовите проект **аппсервицепровидер**, выберите расположение для него и нажмите кнопку **создать**.

2. При запросе на выбор **целевой** и **минимальной версии** проекта выберите по крайней мере **10.0.14393**. Если вы хотите использовать новый атрибут **суппортсмултиплеинстанцес** , необходимо использовать visual Studio 2017 или visual Studio 2019, а также целевой **10.0.15063** (**Windows 10 Creators Update**) или более поздней версии.

<span id="appxmanifest"/>

## <a name="add-an-app-service-extension-to-packageappxmanifest"></a>Добавление расширения службы приложений в Package. appxmanifest

В проекте **аппсервицепровидер** откройте файл **Package. appxmanifest** в текстовом редакторе: 

1. Щелкните его правой кнопкой мыши в **Обозреватель решений**. 
2. Выберите **Открыть с помощью**. 
3. Выберите **Редактор XML (текстовый)** . 

Добавьте в `<Application>` элемент `AppService` следующее расширение. В этом примере показано, как объявить службу `com.microsoft.inventory` и указать, что приложение является поставщиком службы приложений. Сама служба будет реализована в виде фоновой задачи. Проект приложения службы приложений предоставляет службу другим приложениям. Для имени службы мы рекомендуем использовать обратное доменное имя.

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

`Category` Атрибут идентифицирует это приложение как поставщика службы приложений.

`EntryPoint` Атрибут определяет квалифицированный класс пространства имен, реализующий службу, которая будет реализована далее.

`SupportsMultipleInstances` Атрибут указывает, что каждый раз при вызове службы приложений необходимо, чтобы она выполнялась в новом процессе. Это не является обязательным, но доступно вам, если вам нужна эта функция и они предназначены для 10.0.15063 SDK (**Windows 10 Creators Update**) или более поздней версии. В качестве префикса следует использовать пространство имен `uap4`.

## <a name="create-the-app-service"></a>Создание службы приложений

1.  Служба приложений будет реализована в виде фоновой задачи. Это позволяет приложению переднего плана вызывать службу приложений в другом приложении. Чтобы создать службу приложений в качестве фоновой задачи, добавьте в решение новый проект среда выполнения Windows компонента (**файл &gt; добавить &gt; новый проект**) с именем **мяппсервице**. В диалоговом окне **Добавление нового проекта** выберите **установленный > Visual C# > Среда выполнения Windows компонент (универсальное Windows)** .
2.  В проекте **аппсервицепровидер** добавьте ссылку проекта на проект в новый проект **мяппсервице** (в **Обозреватель решений**щелкните правой кнопкой мыши проект **аппсервицепровидер** > **Добавить**  > .  **Эталонное** > **решение**проектов > выберитемяппсервицеОК > ). Этот шаг очень важен: если вы не добавите ссылку, служба приложения не сможет подключиться во время выполнения.
3.  В проекте **мяппсервице** добавьте следующие операторы **using** в начало **Class1.CS**:
    ```cs
    using Windows.ApplicationModel.AppService;
    using Windows.ApplicationModel.Background;
    using Windows.Foundation.Collections;
    ```

4.  Переименуйте **Class1.CS** в **Inventory.CS**и замените код заглушки для **Class1** на новый класс фоновой задачи с именем **Inventory**:

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

    **Запуск** вызывается при создании фоновой задачи. Так как по завершении работы метода **Run** работа фоновых задач также завершается, код создает отсрочку, и фоновая задача может продолжать обрабатывать запросы. Служба приложений, реализованная в качестве фоновой задачи, будет оставаться в активном состоянии примерно через 30 секунд после получения вызова, если только она не будет вызвана в течение этого периода времени или не будет вычислена отсрочка. Если служба приложений реализована в том же процессе, что и вызывающий объект, время существования службы приложений связано со временем существования вызывающей стороны.

    Срок жизни службы приложения зависит от вызывающей стороны.

    * Если вызывающий объект находится на переднем плане, время существования службы приложений совпадает с вызывающим объектом.
    * Если вызывающий объект находится в фоновом режиме, служба приложений получает 30 секунд для выполнения. Активация задержки дает службе пять дополнительных секунд.

    **Онтаскканцелед** вызывается при отмене задачи. Задача отменяется, когда клиентское приложение удаляет [аппсервицеконнектион](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceConnection), клиентское приложение приостанавливается, ОС завершает работу или закрывается, либо ОС не хватает ресурсов для выполнения задачи.

## <a name="write-the-code-for-the-app-service"></a>Написание кода для службы приложений

**Онрекуестрецеивед** — это место, куда переходит код для службы приложений. Замените **онрекуестрецеивед** заглушки в **Inventory.CS** **мяппсервице**кодом из этого примера. Этот код получает индекс складской позиции и передает его в строке команды в службу, чтобы получить название и цену указанной складской позиции. В собственные проекты добавьте код обработки ошибок.

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

Обратите внимание, что **онрекуестрецеивед** является **асинхронным** , так как в этом примере мы создаем ожидающий вызов метода в [сендреспонсеасинк](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appservicerequest.sendresponseasync) .

Предпринимается отсрочка, чтобы служба могла использовать **асинхронные** методы в обработчике **онрекуестрецеивед** . Это гарантирует, что вызов **OnRequestReceived** не завершится до тех пор, пока не будет обработано сообщение.  [Сендреспонсеасинк](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appservicerequest.sendresponseasync) отправляет результат вызывающему объекту. **SendResponseAsync** не сигнализирует о завершении вызова. Это завершение задержки, которое сигнализирует [сендмессажеасинк](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.sendmessageasync) , что **онрекуестрецеивед** завершено. Вызов **сендреспонсеасинк** заключается в блок try/finally, так как необходимо завершить РБП, даже если **сендреспонсеасинк** создает исключение.

Службы [приложений используют объекты](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet) для обмена данными. Размер данных, которые можно передать, ограничен только ресурсами системы. Не существует предопределенных параметров, которые можно использовать в **ValueSet**. Вам необходимо решить, какие значения параметров вы будете использовать для определения протокола для службы приложений. Вызывающий код необходимо писать с учетом этого протокола. В этом примере мы выбрали параметр с именем `Command`, значение которого указывает, что должна предоставить служба приложений: сведения об имени складской позиции или ее цене. Индекс имени складской позиции хранится в параметре `ID`. Возвращаемое значение хранится в параметре `Result`.

Перечисление [аппсервицеклоседстатус](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceClosedStatus) возвращается вызывающему объекту, чтобы указать, успешно ли был выполнен вызов службы приложений. Сбой вызова службы приложений может возникнуть, если ОС прерывает конечную точку службы, так как ее ресурсы исчерпаны. Дополнительные сведения об ошибке можно получить с помощью этого же [параметра](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet). В этом примере мы используем параметр `Status`, чтобы возвращать в вызывающий код более подробные сведения об ошибке.

Вызов [сендреспонсеасинк](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appservicerequest.sendresponseasync) возвращает в вызывающий объект [значение](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet) .

## <a name="deploy-the-service-app-and-get-the-package-family-name"></a>Развертывание приложения службы и получение имени семейства пакетов

Необходимо развернуть поставщик службы приложений, прежде чем его можно будет вызвать из клиента. Его можно развернуть, выбрав **сборка > развернуть решение** в Visual Studio.

Вам также потребуется имя семейства пакетов для поставщика службы приложений, чтобы вызвать его. Его можно получить, открыв файл **Package. appxmanifest** проекта **аппсервицепровидер** в представлении конструктора (дважды щелкните его в **Обозреватель решений**). Перейдите на вкладку **Упаковка** , скопируйте значение рядом с полем **имя семейства пакетов**и вставьте его в любом месте, как в блокноте.

## <a name="write-a-client-to-call-the-app-service"></a>Написание клиента для вызова службы приложений

1.  Добавьте в решение новый пустой проект универсального приложения для Windows (для этого последовательно выберите пункты **Файл &gt; Добавить &gt; Создать проект**). В диалоговом окне **Добавление нового проекта** выберите пункт **установленные > Visual C# > пустое приложение (универсальные приложения Windows)** и назовите его **ClientApp**.

2.  В проекте **ClientApp** добавьте следующую инструкцию **using** в верхнюю часть **MainPage.XAML.CS**:
    ```cs
    using Windows.ApplicationModel.AppService;
    ```

3.  Добавьте текстовое поле с именем **TextBox** и кнопку в **MainPage. XAML**.

4.  Добавьте обработчик нажатия кнопки с именем **button_Click**и добавьте ключевое слово **Async** в сигнатуру обработчика кнопки.

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
    > Обязательно вставьте строковый литерал вместо помещения его в переменную. Он не будет работать при использовании переменной.

    Сначала код устанавливает связь со службой приложения. Подключение останется открытым, пока вы не удалите объект `this.inventoryService`. Имя службы приложений `AppService` должно соответствовать `Name` атрибуту элемента, добавленному в файл **Package. appxmanifest** проекта **аппсервицепровидер** . В этом примере это `<uap3:AppService Name="com.microsoft.inventory"/>`.

    Набор [значений](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet) с именем `message` создается для указания команды, которую нужно отправить в службу приложений. Пример службы приложения будет ждать команду, чтобы указать, какое из 2 действий необходимо выполнить. Мы получаем индекс из текстового поля в клиентском приложении, а затем вызываем службу с помощью `Item` команды, чтобы получить описание элемента. Затем с помощью команды `Price` мы получаем цену элемента. Результат используется в качестве текста кнопки.

    Поскольку [AppServiceResponseStatus](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceResponseStatus) указывает, удалось ли операционной системе подключаться к службе приложений, мы проверяем `Status` ключ в методе [ValueSet](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.ValueSet), полученном из службы приложений, чтобы убедиться, что он смог выполнить получения.

6. Задайте проект **ClientApp** в качестве запускаемого проекта (щелкните его правой кнопкой мыши в **Обозреватель решений** > **задать в качестве запускаемого проекта**) и запустите решение. Введите число 1 в текстовое поле и нажмите кнопку. Вы должны получить стул: Price = 88,99 "назад от службы.

    ![Образец приложения, в котором отображаются данные: Chair price=88.99](images/appserviceclientapp.png)

В случае сбоя вызова службы приложений проверьте следующее в проекте **ClientApp** :

1.  Убедитесь, что имя семейства пакетов, назначенное подключению службы инвентаризации, совпадает с именем семейства пакетов для приложения **аппсервицепровидер** . Просмотрите строку в нажатии **кнопки\_** с `this.inventoryService.PackageFamilyName = "...";`.
2.  При **нажатии кнопки\_** убедитесь, что имя службы приложений, назначенное подключению службы инвентаризации, соответствует имени службы приложений в файле **Package. appxmanifest** **аппсервицепровидер**. См. `this.inventoryService.AppServiceName = "com.microsoft.inventory";`.
3.  Убедитесь, что приложение **аппсервицепровидер** развернуто. (В **Обозреватель решений**щелкните правой кнопкой мыши решение и выберите пункт **Развернуть решение**).

## <a name="debug-the-app-service"></a>Отладка службы приложений

1.  Перед отладкой убедитесь, что решение развернуто, так как перед вызовом службы необходимо развернуть приложение поставщика службы приложений. (Для этого в Visual Studio последовательно выберите пункты **Сборка &gt; Развернуть решение**).
2.  В **Обозреватель решений**щелкните правой кнопкой мыши проект **аппсервицепровидер** и выберите пункт **Свойства**. На вкладке **Отладка** измените значение параметра **Действие при запуске** на **Не запускать, а отлаживать мой код при открытии**. (Обратите внимание, что при использовании C++ для реализации поставщика службы приложений на вкладке **Отладка** необходимо изменить значение параметра **Запуск приложения** на **Нет**.)
3.  В проекте **мяппсервице** в файле **Inventory.CS** установите точку останова в **онрекуестрецеивед**.
4.  Задайте проект **аппсервицепровидер** в качестве запускаемого проекта и нажмите клавишу **F5**.
5.  Запустите **ClientApp** из меню "Пуск" (не из Visual Studio).
6.  Введите число 1 в текстовое поле и нажмите кнопку. Отладчик остановится на вызове службы приложений в точке ее останова.

## <a name="debug-the-client"></a>Отладка клиента

1.  Для отладки клиента, вызывающего службу приложений, выполните инструкции из предыдущего шага.
2.  Запустите **ClientApp** из меню "Пуск".
3.  Подключите отладчик к процессу **ClientApp. exe** (а не к процессу **аппликатионфрамехост. exe** ). (В Visual Studio последовательно выберите пункты **Отладка &gt; Присоединиться к процессу...** ).
4.  В проекте **ClientApp** задайте точку останова при **нажатии\_кнопки**.
5.  Точки останова как в клиенте, так и в службе приложений теперь будут достигнуты при вводе числа 1 в текстовое поле **ClientApp** и нажатии кнопки.

## <a name="general-app-service-troubleshooting"></a>Устранение общих неполадок службы приложений

Если вы сталкиваетесь с состоянием **аппунаваилабле** после попытки подключения к службе приложений, проверьте следующее:

- Убедитесь, что проект поставщика службы приложений и проект службы приложений развернуты. Необходимо развернуть оба проекта перед запуском клиента, так как в противном случае клиенту не к чему будет подключаться. Развертывание можно выполнить в Visual Studio с помощью команды **Сборка** > **Развернуть решение**.
- В **Обозреватель решений**убедитесь, что проект поставщика службы приложений содержит ссылку проекта на проект, который реализует службу приложений.
- Убедитесь, что `<Extensions>` запись и ее дочерние элементы добавлены в файл **Package. appxmanifest** , принадлежащий проекту поставщика службы приложений, как указано выше в поле [Добавление расширения службы приложений в Package. appxmanifest](#appxmanifest).
- Убедитесь, что строка [аппсервицеконнектион. аппсервиценаме](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.appservicename) в клиенте, вызывающая поставщик службы приложений, соответствует `<uap3:AppService Name="..." />` указанному в файле **Package. appxmanifest** проекта поставщика службы приложений.
- Убедитесь, что [аппсервицеконнектион. паккажефамилинаме](https://docs.microsoft.com/uwp/api/windows.applicationmodel.appservice.appserviceconnection.packagefamilyname) соответствует имени семейства пакетов компонента поставщика службы приложений, как указано выше в списке [Добавление расширения службы приложений в Package. appxmanifest](#appxmanifest)
- Для внепроцессных служб приложений, таких как в этом примере, убедитесь, что `EntryPoint` указанный `<uap:Extension ...>` в элементе файла **Package. appxmanifest** проекта поставщика службы приложений соответствует пространству имен и имени класса открытого объекта. класс, реализующий [IBackgroundTask](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask) в проекте службы приложений.

### <a name="troubleshoot-debugging"></a>Устранение неполадок отладки

Если отладчик не останавливается на точках останова в проекте поставщика службы приложений или проекте службы приложений, выполните следующие действия.

- Убедитесь, что проект поставщика службы приложений и проект службы приложений развернуты. Оба проекта должны быть развернуты перед запуском клиента. Развертывание можно выполнить в Visual Studio с помощью команды **Сборка** > **Развернуть решение**.
- Убедитесь, что проект, для которого требуется выполнить отладку, задан в качестве запускаемого проекта и что для свойств отладки этого проекта задано значение не запускать проект при нажатии **клавиши F5** . Щелкните правой кнопкой мыши проект, выберите пункт **Свойства** и щелкните **Отладка** (или **Отладка** в C++). При работе на C# измените значение параметра **Действие при запуске** на **Не запускать, а отлаживать мой код при открытии**. При работе на C++ задайте для параметра **Запуск приложения** значение **Нет**.

## <a name="remarks"></a>Примечания

В этом примере показано, как создать службу приложений, которая выполняется как фоновая задача, и как вызвать ее из другого приложения. Ключевые моменты, которые следует отметить:

* Создайте фоновую задачу для размещения службы приложений.
* Добавьте расширение в файл **Package. appxmanifest** поставщика службы приложений. `windows.appService`
* Получите имя семейства пакетов поставщика службы приложений, чтобы мы могли подключиться к нему из клиентского приложения.
* Добавьте ссылку проекта на проект из проекта поставщика службы приложений в проект службы приложений.
* Для вызова службы используйте [Windows. ApplicationModel. AppService. аппсервицеконнектион](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.AppService.AppServiceConnection) .

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

## <a name="related-topics"></a>См. также

* [Преобразование службы приложений для запуска в одном процессе с ведущим приложением](convert-app-service-in-process.md)
* [Поддержка приложения с помощью фоновых задач](support-your-app-with-background-tasks.md)
* [Пример кода службы приложений (C#, C++и VB)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AppServices)
