---
author: mcleanbyron
Description: "Узнайте, как зарегистрировать приложение UWP для получения push-уведомлений, отправляемых из Центра разработки для Windows."
title: "Настройка приложения для получения push-уведомлений Центра разработки"
translationtype: Human Translation
ms.sourcegitcommit: 126fee708d82f64fd2a49b844306c53bb3d4cc86
ms.openlocfilehash: 0e6ac52f1e76c0f59cc428b2ff26dc524e93cbde

---

# Настройка приложения для получения push-уведомлений Центра разработки

Вы можете использовать страницу **Push-уведомлений** информационной панели центра разработки для Windows для непосредственного общения с клиентами путем отправки целевых push-уведомлений на устройства, на которых установлено ваше приложение универсальной платформы Windows (UWP). Например, можно использовать целевые push-уведомления для побуждения пользователей к определенным действиям, таким как выставление оценки вашему приложению или использование новой функции. Можно отправлять различные типы push-уведомлений, включая всплывающие уведомления, уведомления на плитках и необработанные уведомления XML. Также можно отслеживать количество запусков приложения, производимых из push-уведомлений. Дополнительные сведения об этой функции см. в разделе [Отправка push-уведомлений пользователям вашего приложения](../publish/send-push-notifications-to-your-apps-customers.md).

Прежде чем отправлять целевые push-уведомления пользователям из Центра разработки, необходимо использовать определенный метод класса [StoreServicesEngagementManager](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesengagementmanager.aspx) в пакете Microsoft Store Services SDK, чтобы зарегистрировать приложение для получения уведомлений. Дополнительные методы этого класса можно использовать для уведомления Центра разработки о том, что ваше приложение было запущено в ответ на целевое push-уведомление (если вы хотите отслеживать количество запусков приложения, произведенных из уведомлений), и для прекращения получения уведомлений.

## Настройка проекта

Прежде чем писать код, выполните следующие действия, чтобы добавить ссылку на Microsoft Store Services SDK в свой проект.

1. Если это еще не сделано, [установите Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk) на компьютер разработки. Помимо API-интерфейса для регистрации приложения на получение уведомления этот пакет SDK также предоставляет API-интерфейсы для других функций, например для проведения экспериментов с A/B-тестированием и показа рекламы. 
2. Откройте проект в Visual Studio.
3. В обозревателе решений щелкните правой кнопкой мыши узел **Ссылки** вашего проекта и выберите команду **Добавить ссылку**.
4. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
5. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

## Регистрация для получения push-уведомлений

Регистрация приложения для получения целевых push-уведомлений из Центра разработки.

1. В проекте найдите раздел кода, выполняемого во время запуска, в котором вы можете зарегистрировать приложение для получения уведомлений из Центра разработки.
2. Добавьте в начало файла с кодом следующий оператор.

  ```csharp
  using Microsoft.Services.Store.Engagement;
  ```

3. Получите объект [StoreServicesEngagementManager](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesengagementmanager.aspx) и вызовите один из перегруженных методов [RegisterNotificationChannelAsync](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync.aspx) в коде запуска, определенном вами ранее. Этот метод следует вызывать каждый раз при запуске приложения.

  * Если вы хотите, чтобы Центр разработки создавал собственный URI канала для уведомлений, вызовите перегрузку [RegisterNotificationChannelAsync()](https://msdn.microsoft.com/library/windows/apps/mt771190.aspx).

    ```csharp
    StoreServicesEngagementManager engagementManager = StoreServicesEngagementManager.GetDefault();
    await engagementManager.RegisterNotificationChannelAsync();
    ```

    >**Важно.**&nbsp;&nbsp;Если ваше приложение также вызывает [CreatePushNotificationChannelForApplicationAsync](https://msdn.microsoft.com/library/windows/apps/windows.networking.pushnotifications.pushnotificationchannelmanager.createpushnotificationchannelforapplicationasync.aspx) для создания канала уведомлений для WNS, убедитесь, что ваш код не вызывает [CreatePushNotificationChannelForApplicationAsync](https://msdn.microsoft.com/library/windows/apps/windows.networking.pushnotifications.pushnotificationchannelmanager.createpushnotificationchannelforapplicationasync.aspx) и перегрузку [RegisterNotificationChannelAsync()](https://msdn.microsoft.com/library/windows/apps/mt771190.aspx) одновременно. Если необходимо вызвать оба этих метода, проследите за тем, чтобы они вызывались последовательно и дожидались возврата из одного метода перед вызовом второго.

  * Если вы хотите указать URI канала, чтобы использовать целевые push-уведомления из Центра разработки, вызовите перегрузку [RegisterNotificationChannelAsync(StoreServicesNotificationChannelParameters)](https://msdn.microsoft.com/library/windows/apps/mt771191.aspx). Например, это может потребоваться, если ваше приложение уже использует службы push-уведомлений Windows (WNS) и вы хотите использовать один и тот же URI канала. Сначала необходимо создать объект [StoreServicesNotificationChannelParameters](https://msdns.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesnotificationchannelparameters.aspx) и назначить свойство [CustomNotificationChannelUri](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesnotificationchannelparameters.customnotificationchanneluri.aspx) вашему URI канала.

    ```csharp
    StoreServicesNotificationChannelParameters parameters =
        new StoreServicesNotificationChannelParameters();
    parameters.CustomNotificationChannelUri = "Assign your channel URI here";

    StoreServicesEngagementManager engagementManager = StoreServicesEngagementManager.GetDefault();
    await engagementManager.RegisterNotificationChannelAsync(parameters);
    ```

  >#### Понимание того, как ваше приложение реагирует, когда пользователь его запускает

  >После регистрации приложения для получения уведомлений и [отправки push-уведомлений пользователям вашего приложения из Центра разработки](../publish/send-push-notifications-to-your-apps-customers.md) будет производиться вызов одной из следующих точек входа в вашем приложении, когда пользователь будет запускать приложение в качестве реакции на ваше push-уведомление. Если у вас есть какой-либо код, который требуется выполнять, когда пользователь запускает ваше приложение, можно добавить этот код в одну из этих точек входа в приложении.

  >* Если push-уведомление имеет тип активации переднего плана, переопределите метод [OnActivated](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onactivated.aspx) класса **App** в вашем проекте и добавьте свой код в этот метод.

  >* Если push-уведомление имеет фоновый тип активации, добавьте свой код в метод [Run](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx) для [фоновой задачи](../launch-resume/support-your-app-with-background-tasks.md).

  >Например, вы решите вознаграждать пользователей вашего приложения, которые приобрели какие-либо платные надстройки в приложении, путем предоставления им бесплатной надстройки. В этом случае можно отправлять push-уведомление определенному [сегменту пользователей](../publish/create-customer-segments.md), нацеленное на этих пользователей. Затем можно добавить код, чтобы предоставить им право бесплатной [покупки из приложения](in-app-purchases-and-trials.md), в одну из перечисленных выше точек входа.

## Уведомление Центра разработки о запуске вашего приложения

При выборе параметра **Отслеживать количество запусков приложения** для push-уведомления Центра разработки необходимо выполнить вызов метода [ParseArgumentsAndTrackAppLaunch](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesengagementmanager.parseargumentsandtrackapplaunch.aspx) из соответствующей точки входа в приложении, чтобы уведомить Центр разработки о запуске вашего приложения в ответ на push-уведомление.

Этот метод также возвращает исходные аргументы запуска для приложения. После принятия решения об отслеживании количества запусков приложения для push-уведомлений Центра разработки к аргументам запуска добавляется непрозрачный отслеживающий идентификатор для подсчета запусков приложения в Центре разработки. Необходимо передать аргументы запуска приложения методу [ParseArgumentsAndTrackAppLaunch](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesengagementmanager.parseargumentsandtrackapplaunch.aspx), и этот метод отправит отслеживающий идентификатор в Центр разработки, удалит его из аргументов запуска и вернет исходные аргументы запуска вашему коду.

Способ вызова этого метода зависит от типа активации целевых push-уведомлений.

* Если push-уведомление имеет тип активации переднего плана, вызовите этот метод из переопределения метода [OnActivated](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onactivated.aspx) в своем приложении и передайте аргументы, доступные в объекте [ToastNotificationActivatedEventArgs](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.activation.toastnotificationactivatedeventargs.aspx), передаваемом этому методу. В следующем примере кода предполагается, что файл кода содержит операторы **using** для пространств имен **Microsoft.Services.Store.Engagement** и **Windows.ApplicationModel.Activation**.

  ```csharp
  protected override void OnActivated(IActivatedEventArgs args)
  {
       base.OnActivated(args);   

       if (args is ToastNotificationActivatedEventArgs)
       {
             var toastActivationArgs = args as ToastNotificationActivatedEventArgs;

             StoreServicesEngagementManager engagementManager = StoreServicesEngagementManager.GetDefault();
             string originalArgs = engagementManager.ParseArgumentsAndTrackAppLaunch(
                 toastActivationArgs.Argument);

             // Use the originalArgs variable to access the original arguments
             // that were passed to the app.
       }
  }
  ```

* Если push-уведомление имеет фоновый тип активации, вызовите этот метод из метода [Run](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.run.aspx) вашей [фоновой задачи](../launch-resume/support-your-app-with-background-tasks.md) и передайте аргументы, доступные в объекте [ToastNotificationActionTriggerDetail](https://msdn.microsoft.com/library/windows/apps/windows.ui.notifications.toastnotificationactiontriggerdetail.aspx), передаваемом этому методу. В следующем примере кода предполагается, что ваш файл кода содержит операторы **using** для пространств имен **Microsoft.Services.Store.Engagement**, **Windows.ApplicationModel.Background** и **Windows.UI.Notifications**.

  ```csharp
  public void Run(IBackgroundTaskInstance taskInstance)
  {
       var details = taskInstance.TriggerDetails as ToastNotificationActionTriggerDetail;

       if (details != null)
       {
            StoreServicesEngagementManager engagementManager = StoreServicesEngagementManager.GetDefault();
            string originalArgs = engagementManager.ParseArgumentsAndTrackAppLaunch(details.Argument);

            // Use the originalArgs variable to access the original arguments
            // that were passed to the app.
       }
  }
  ```

## Отмена регистрации для получения push-уведомлений

Если вы хотите прекратить получение вашим приложением целевых push-уведомлений из Центра разработки для Windows, вызовите метод [UnregisterNotificationChannelAsync](https://msdn.microsoft.com/library/windows/apps/microsoft.services.store.engagement.storeservicesengagementmanager.unregisternotificationchannelasync).

```csharp
StoreServicesEngagementManager engagementManager = StoreServicesEngagementManager.GetDefault();
await engagementManager.UnregisterNotificationChannelAsync();
```

Обратите внимание, что этот метод делает недействительным канал, который используется для уведомлений, поэтому приложение больше не будет получать push-уведомления *ни от каких* служб. После его закрытия канал больше никогда невозможно будет использовать ни для каких служб, включая целевые push-уведомления Центра разработки для Windows и другие уведомления с помощью WNS. Чтобы возобновить отправку push-уведомлений для этого приложения, приложение должно будет запросить новый канал.

## Статьи по теме

* [Отправка push-уведомлений пользователям вашего приложения](../publish/send-push-notifications-to-your-apps-customers.md)
* [Обзор служб push-уведомлений Windows (WNS)](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview)
* [Запрос, создание и сохранение канала уведомлений](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh868221)
* [Microsoft Store Services SDK](https://msdn.microsoft.com/windows/uwp/monetize/microsoft-store-services-sdk)



<!--HONumber=Nov16_HO1-->


