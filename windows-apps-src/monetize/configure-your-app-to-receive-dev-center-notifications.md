---
Description: Learn how to register your UWP app to receive push notifications that you send from Partner Center.
title: Настройка приложения для получения целевых push-уведомлений
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, Microsoft Store Services SDK, целевые Push-уведомления, центр партнеров
ms.assetid: 30c832b7-5fbe-4852-957f-7941df8eb85a
ms.localizationpriority: medium
ms.openlocfilehash: f60780186256e7f78a9596c979c79bfc704ae4c2
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8217030"
---
# <a name="configure-your-app-for-targeted-push-notifications"></a>Настройка приложения для получения целевых push-уведомлений

Страница **Push-уведомлений** в центре партнеров для непосредственного общения с клиентами путем отправки целевых Push-уведомлений на устройствах, на которых установлено ваше приложение универсальной платформы Windows (UWP). Например, можно использовать целевые push-уведомления для побуждения пользователей к определенным действиям, таким как выставление оценки вашему приложению или использование новой функции. Можно отправлять различные типы push-уведомлений, включая всплывающие уведомления, уведомления на плитках и необработанные уведомления XML. Также можно отслеживать количество запусков приложения, производимых из push-уведомлений. Дополнительные сведения об этой функции см. в разделе [Отправка push-уведомлений пользователям вашего приложения](../publish/send-push-notifications-to-your-apps-customers.md).

Прежде чем отправлять целевые Push-уведомления пользователям из центра партнеров, чтобы зарегистрировать приложение для получения уведомлений необходимо использовать метод класса [StoreServicesEngagementManager](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager) в Microsoft Store Services SDK. Дополнительные методы этого класса можно использовать для уведомления центра партнеров, в том, что ваше приложение было запущено в ответ на целевое Push-уведомление (Если вы хотите отслеживать количество запусков приложения, произведенных из уведомлений) и для прекращения получения уведомлений.

## <a name="configure-your-project"></a>Настройка проекта

Прежде чем писать код, выполните следующие действия, чтобы добавить ссылку на Microsoft Store Services SDK в свой проект.

1. Если это еще не сделано, [установите Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk) на компьютер разработки. 
2. Откройте проект в Visual Studio.
3. В обозревателе решений щелкните правой кнопкой мыши узел **Ссылки** вашего проекта и выберите команду **Добавить ссылку**.
4. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
5. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

## <a name="register-for-push-notifications"></a>Регистрация для получения push-уведомлений

Чтобы зарегистрировать приложение для получения целевых Push-уведомлений из центра партнеров:

1. В своем проекте найдите раздел кода, выполняемого во время запуска, в котором можно зарегистрировать приложение для получения уведомлений.
2. Добавьте в начало файла с кодом следующее выражение:

    [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#EngagementNamespace)]

3. Получите объект [StoreServicesEngagementManager](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager) и вызовите один из перегруженных методов [RegisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) в коде запуска, определенном вами ранее. Этот метод следует вызывать каждый раз при запуске приложения.

  * Если вы хотите центр партнеров, чтобы создать собственный URI канала для уведомлений, вызовите перегрузку [RegisterNotificationChannelAsync()](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) .

      [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#RegisterNotificationChannelAsync1)]
      > [!IMPORTANT]
      > Если ваше приложение также вызывает [CreatePushNotificationChannelForApplicationAsync](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannelmanager.createpushnotificationchannelforapplicationasync) для создания канала уведомлений для WNS, убедитесь, что ваш код не вызывает [CreatePushNotificationChannelForApplicationAsync](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannelmanager.createpushnotificationchannelforapplicationasync) и перегрузку [RegisterNotificationChannelAsync()](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) одновременно. Если необходимо вызвать оба этих метода, проследите за тем, чтобы они вызывались последовательно и дожидались возврата из одного метода перед вызовом второго.

  * Если вы хотите указать URI канала, чтобы использовать целевые Push-уведомления из центра партнеров, вызовите перегрузку [RegisterNotificationChannelAsync(StoreServicesNotificationChannelParameters)](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) . Например, это может потребоваться, если ваше приложение уже использует службы push-уведомлений Windows (WNS) и вы хотите использовать один и тот же URI канала. Сначала необходимо создать объект [StoreServicesNotificationChannelParameters](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesnotificationchannelparameters) и назначить свойство [CustomNotificationChannelUri](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesnotificationchannelparameters.customnotificationchanneluri) вашему универсальному коду ресурса (URI) канала.

      [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#RegisterNotificationChannelAsync2)]

> [!NOTE]
> При вызове метода **RegisterNotificationChannelAsync** в локальном хранилище данных приложения создается файл с именем MicrosoftStoreEngagementSDKId.txt для вашего приложения (папка, возвращенная свойством [ApplicationData.LocalFolder](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationData.LocalFolder)). Этот файл содержит идентификатор, используемый инфраструктурой целевых push уведомлений. Убедитесь, что ваше приложение не изменило или не удалило этот файл. В противном случае ваши пользователи могут получать несколько экземпляров уведомлений, либо уведомления будут работать неправильно.

<span id="notification-customers" />

### <a name="how-targeted-push-notifications-are-routed-to-customers"></a>Передача целевых push-уведомлений пользователям

Когда ваше приложение вызывает метод **RegisterNotificationChannelAsync**, этот метод получает информацию об учетной записи Майкрософт того пользователя, который в данный момент вошел на устройство. Позже когда вы отправляете целевое Push-уведомление сегмент, включающий данного клиента, центр партнеров отправляет уведомление на устройствах, которые связаны с учетной записью Майкрософт этого пользователя.

Помните, что если пользователь, запустивший ваше приложение, передает свое устройство в пользование другому пользователю, то другой пользователь может увидеть уведомление, предназначенное первому пользователю, пока они находятся в системе под своими учетными записями Майкрософт. Это может привести к непредвиденным последствиям, особенно для приложений, предоставляющих услуги, которыми пользователь может воспользоваться, выполнив вход. В этом случае, чтобы запретить другим пользователям просматривать целевые уведомления, вызовите метод [UnregisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.unregisternotificationchannelasync), когда пользователи выйдут из вашего приложения. Дополнительные сведения см. далее в разделе [Отмена регистрации для получения push-уведомлений](#unregister) этой статьи.

### <a name="how-your-app-responds-when-the-user-launches-your-app"></a>Реакция вашего приложения, когда его запускает пользователь

После регистрации приложения для получения уведомлений и [отправлять Push-уведомлений пользователям вашего приложения в центре партнеров](../publish/send-push-notifications-to-your-apps-customers.md), один из следующих точек входа в приложении будет вызываться при пользователь запускает ваше приложение в ответ на ваше Push-уведомлений уведомление. Если у вас есть какой-либо код, который требуется выполнять, когда пользователь запускает ваше приложение, можно добавить этот код в одну из этих точек входа в приложении.

  * Если push-уведомление имеет тип активации переднего плана, переопределите метод [OnActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onactivated) класса **App** в вашем проекте и добавьте свой код в этот метод.

  * Если push-уведомление имеет фоновый тип активации, добавьте свой код в метод [Run](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) для [фоновой задачи](../launch-resume/support-your-app-with-background-tasks.md).

Например, вы решите вознаграждать пользователей вашего приложения, которые приобрели какие-либо платные надстройки в приложении, путем предоставления им бесплатной надстройки. В этом случае можно отправлять push-уведомление определенному [сегменту пользователей](../publish/create-customer-segments.md), нацеленное на этих пользователей. Затем можно добавить код, чтобы предоставить им право бесплатной [покупки из приложения](in-app-purchases-and-trials.md), в одну из перечисленных выше точек входа.

## <a name="notify-partner-center-of-your-app-launch"></a>Уведомления о центре партнеров запуске вашего приложения

При выборе пункта **Отслеживание скорости запуска приложений** для вашего целевого Push-уведомления в центре партнеров, вызовите метод [ParseArgumentsAndTrackAppLaunch](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.parseargumentsandtrackapplaunch) из соответствующей точки входа в вашем приложении, чтобы уведомить центр партнеров, который был вашего приложения запущено в ответ на Push-уведомление.

Этот метод также возвращает исходные аргументы запуска для приложения. При выборе Отслеживание скорости запуска приложения для Push-уведомления, непрозрачный отслеживающий идентификатор добавляется в аргументы запуска для отслеживания его запуска в центре партнеров. Методу [ParseArgumentsAndTrackAppLaunch](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.parseargumentsandtrackapplaunch) необходимо передать аргументы запуска для вашего приложения, и этот метод отправит отслеживающий идентификатор в центр партнеров, удалит его из аргументов запуска и вернет исходные аргументы запуска для вашего код.

Способ вызова этого метода зависит от типа активации push-уведомления:

* Если push-уведомление имеет тип активации переднего плана, вызовите этот метод из переопределения метода [OnActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onactivated) в своем приложении и передайте аргументы, доступные в объекте [ToastNotificationActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs), передаваемом этому методу. В следующем примере кода предполагается, что файл кода содержит операторы **using** для пространств имен **Microsoft.Services.Store.Engagement** и **Windows.ApplicationModel.Activation**.

  [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/App.xaml.cs#OnActivated)]

* Если push-уведомление имеет фоновый тип активации, вызовите этот метод из метода [Run](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) вашей [фоновой задачи](../launch-resume/support-your-app-with-background-tasks.md) и передайте аргументы, доступные в объекте [ToastNotificationActionTriggerDetail](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationActionTriggerDetail), передаваемом этому методу. В следующем примере кода предполагается, что ваш файл кода содержит операторы **using** для пространств имен **Microsoft.Services.Store.Engagement**, **Windows.ApplicationModel.Background** и **Windows.UI.Notifications**.

  [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#Run)]

<span id="unregister" />

## <a name="unregister-for-push-notifications"></a>Отмена регистрации для получения push-уведомлений

Если вы хотите ваше приложение прекратило целевые Push-уведомления из центра партнеров, вызовите метод [UnregisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.unregisternotificationchannelasync) .

[!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#UnregisterNotificationChannelAsync)]

Обратите внимание, что этот метод делает недействительным канал, который используется для уведомлений, поэтому приложение больше не будет получать push-уведомления *ни от каких* служб. После его закрытия, канал невозможно использовать ни для каких служб, включая целевые Push-уведомления из центра партнеров и другие уведомления с помощью WNS. Чтобы возобновить отправку push-уведомлений для этого приложения, приложение должно будет запросить новый канал.

## <a name="related-topics"></a>Статьи по теме

* [Отправка push-уведомлений пользователям вашего приложения](../publish/send-push-notifications-to-your-apps-customers.md)
* [Обзор служб push-уведомлений Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview)
* [Запрос, создание и сохранение канала уведомлений](https://docs.microsoft.com/previous-versions/windows/apps/hh868221(v=win.10))
* [Microsoft Store Services SDK](https://docs.microsoft.com/windows/uwp/monetize/microsoft-store-services-sdk)
