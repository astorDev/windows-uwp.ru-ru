---
author: Xansky
Description: Learn how to register your UWP app to receive push notifications that you send from Windows Dev Center.
title: Настройка приложения для получения целевых push-уведомлений
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, Microsoft Store Services SDK, целевые push-уведомления, Центр разработки
ms.assetid: 30c832b7-5fbe-4852-957f-7941df8eb85a
ms.localizationpriority: medium
ms.openlocfilehash: 39ea899f9f0ee08d14ca5f182fdf07649ddd1d2e
ms.sourcegitcommit: 82c3fc0b06ad490c3456ad18180a6b23ecd9c1a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "5480523"
---
# <a name="configure-your-app-for-targeted-push-notifications"></a>Настройка приложения для получения целевых push-уведомлений

Вы можете использовать страницу **Push-уведомлений** информационной панели центра разработки для Windows для непосредственного общения с клиентами путем отправки целевых push-уведомлений на устройства, на которых установлено ваше приложение универсальной платформы Windows (UWP). Например, можно использовать целевые push-уведомления для побуждения пользователей к определенным действиям, таким как выставление оценки вашему приложению или использование новой функции. Можно отправлять различные типы push-уведомлений, включая всплывающие уведомления, уведомления на плитках и необработанные уведомления XML. Также можно отслеживать количество запусков приложения, производимых из push-уведомлений. Дополнительные сведения об этой функции см. в разделе [Отправка push-уведомлений пользователям вашего приложения](../publish/send-push-notifications-to-your-apps-customers.md).

Прежде чем отправлять целевые push-уведомления пользователям из Центра разработки, необходимо использовать определенный метод класса [StoreServicesEngagementManager](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager) в пакете Microsoft Store Services SDK, чтобы зарегистрировать приложение для получения уведомлений. Дополнительные методы этого класса можно использовать для уведомления Центра разработки о том, что ваше приложение было запущено в ответ на целевое push-уведомление (если вы хотите отслеживать количество запусков приложения, произведенных из уведомлений), и для прекращения получения уведомлений.

## <a name="configure-your-project"></a>Настройка проекта

Прежде чем писать код, выполните следующие действия, чтобы добавить ссылку на Microsoft Store Services SDK в свой проект.

1. Если это еще не сделано, [установите Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk) на компьютер разработки. 
2. Откройте проект в Visual Studio.
3. В обозревателе решений щелкните правой кнопкой мыши узел **Ссылки** вашего проекта и выберите команду **Добавить ссылку**.
4. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
5. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

## <a name="register-for-push-notifications"></a>Регистрация для получения push-уведомлений

Регистрация приложения для получения целевых push-уведомлений из Центра разработки.

1. В своем проекте найдите раздел кода, выполняемого во время запуска, в котором можно зарегистрировать приложение для получения уведомлений.
2. Добавьте в начало файла с кодом следующее выражение:

    [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#EngagementNamespace)]

3. Получите объект [StoreServicesEngagementManager](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager) и вызовите один из перегруженных методов [RegisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) в коде запуска, определенном вами ранее. Этот метод следует вызывать каждый раз при запуске приложения.

  * Если вы хотите, чтобы Центр разработки создавал собственный универсальный код ресурса (URI) канала для уведомлений, вызовите перегрузку [RegisterNotificationChannelAsync()](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync).

      [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#RegisterNotificationChannelAsync1)]
      > [!IMPORTANT]
      > Если ваше приложение также вызывает [CreatePushNotificationChannelForApplicationAsync](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannelmanager.createpushnotificationchannelforapplicationasync) для создания канала уведомлений для WNS, убедитесь, что ваш код не вызывает [CreatePushNotificationChannelForApplicationAsync](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannelmanager.createpushnotificationchannelforapplicationasync) и перегрузку [RegisterNotificationChannelAsync()](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) одновременно. Если необходимо вызвать оба этих метода, проследите за тем, чтобы они вызывались последовательно и дожидались возврата из одного метода перед вызовом второго.

  * Если вы хотите указать URI канала, чтобы использовать целевые push-уведомления из Центра разработки, вызовите перегрузку [RegisterNotificationChannelAsync(StoreServicesNotificationChannelParameters)](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync). Например, это может потребоваться, если ваше приложение уже использует службы push-уведомлений Windows (WNS) и вы хотите использовать один и тот же URI канала. Сначала необходимо создать объект [StoreServicesNotificationChannelParameters](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesnotificationchannelparameters) и назначить свойство [CustomNotificationChannelUri](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesnotificationchannelparameters.customnotificationchanneluri) вашему универсальному коду ресурса (URI) канала.

      [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#RegisterNotificationChannelAsync2)]

> [!NOTE]
> При вызове метода **RegisterNotificationChannelAsync** в локальном хранилище данных приложения создается файл с именем MicrosoftStoreEngagementSDKId.txt для вашего приложения (папка, возвращенная свойством [ApplicationData.LocalFolder](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationData.LocalFolder)). Этот файл содержит идентификатор, используемый инфраструктурой целевых push уведомлений. Убедитесь, что ваше приложение не изменило или не удалило этот файл. В противном случае ваши пользователи могут получать несколько экземпляров уведомлений, либо уведомления будут работать неправильно.

<span id="notification-customers" />

### <a name="how-targeted-push-notifications-are-routed-to-customers"></a>Передача целевых push-уведомлений пользователям

Когда ваше приложение вызывает метод **RegisterNotificationChannelAsync**, этот метод получает информацию об учетной записи Майкрософт того пользователя, который в данный момент вошел на устройство. Затем, когда вы отправляете целевое push-уведомление в сегмент, в котором находится данный пользователь, Центр разработки отправляет это уведомление на устройства, связанные с учетной записью Майкрософт этого пользователя.

Помните, что если пользователь, запустивший ваше приложение, передает свое устройство в пользование другому пользователю, то другой пользователь может увидеть уведомление, предназначенное первому пользователю, пока они находятся в системе под своими учетными записями Майкрософт. Это может привести к непредвиденным последствиям, особенно для приложений, предоставляющих услуги, которыми пользователь может воспользоваться, выполнив вход. В этом случае, чтобы запретить другим пользователям просматривать целевые уведомления, вызовите метод [UnregisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.unregisternotificationchannelasync), когда пользователи выйдут из вашего приложения. Дополнительные сведения см. далее в разделе [Отмена регистрации для получения push-уведомлений](#unregister) этой статьи.

### <a name="how-your-app-responds-when-the-user-launches-your-app"></a>Реакция вашего приложения, когда его запускает пользователь

После регистрации приложения для получения уведомлений и [отправки push-уведомлений пользователям вашего приложения из Центра разработки](../publish/send-push-notifications-to-your-apps-customers.md) будет производиться вызов одной из следующих точек входа в вашем приложении, когда пользователь будет запускать приложение в качестве реакции на ваше push-уведомление. Если у вас есть какой-либо код, который требуется выполнять, когда пользователь запускает ваше приложение, можно добавить этот код в одну из этих точек входа в приложении.

  * Если push-уведомление имеет тип активации переднего плана, переопределите метод [OnActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onactivated) класса **App** в вашем проекте и добавьте свой код в этот метод.

  * Если push-уведомление имеет фоновый тип активации, добавьте свой код в метод [Run](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) для [фоновой задачи](../launch-resume/support-your-app-with-background-tasks.md).

Например, вы решите вознаграждать пользователей вашего приложения, которые приобрели какие-либо платные надстройки в приложении, путем предоставления им бесплатной надстройки. В этом случае можно отправлять push-уведомление определенному [сегменту пользователей](../publish/create-customer-segments.md), нацеленное на этих пользователей. Затем можно добавить код, чтобы предоставить им право бесплатной [покупки из приложения](in-app-purchases-and-trials.md), в одну из перечисленных выше точек входа.

## <a name="notify-dev-center-of-your-app-launch"></a>Уведомление Центра разработки о запуске вашего приложения

Если в Центре разработки вы выбрали параметр **Отслеживать количество запусков приложения** для вашего целевого push-уведомления, вызовите метод [ParseArgumentsAndTrackAppLaunch](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.parseargumentsandtrackapplaunch) из соответствующей точки входа в приложении, чтобы уведомить Центр разработки о запуске вашего приложения в ответ на push-уведомление.

Этот метод также возвращает исходные аргументы запуска для приложения. Когда вы выбираете параметр для отслеживания количества запусков приложения для вашего push-уведомления, к аргументам запуска добавляется непрозрачный отслеживающий идентификатор для подсчета запусков приложения в Центре разработки. Необходимо передать аргументы запуска приложения методу [ParseArgumentsAndTrackAppLaunch](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.parseargumentsandtrackapplaunch), и этот метод отправит отслеживающий идентификатор в Центр разработки, удалит его из аргументов запуска и вернет исходные аргументы запуска вашему коду.

Способ вызова этого метода зависит от типа активации push-уведомления:

* Если push-уведомление имеет тип активации переднего плана, вызовите этот метод из переопределения метода [OnActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onactivated) в своем приложении и передайте аргументы, доступные в объекте [ToastNotificationActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs), передаваемом этому методу. В следующем примере кода предполагается, что файл кода содержит операторы **using** для пространств имен **Microsoft.Services.Store.Engagement** и **Windows.ApplicationModel.Activation**.

  [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/App.xaml.cs#OnActivated)]

* Если push-уведомление имеет фоновый тип активации, вызовите этот метод из метода [Run](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) вашей [фоновой задачи](../launch-resume/support-your-app-with-background-tasks.md) и передайте аргументы, доступные в объекте [ToastNotificationActionTriggerDetail](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationActionTriggerDetail), передаваемом этому методу. В следующем примере кода предполагается, что ваш файл кода содержит операторы **using** для пространств имен **Microsoft.Services.Store.Engagement**, **Windows.ApplicationModel.Background** и **Windows.UI.Notifications**.

  [!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#Run)]

<span id="unregister" />

## <a name="unregister-for-push-notifications"></a>Отмена регистрации для получения push-уведомлений

Если вы хотите, чтобы ваше приложение прекратило отправлять целевые push-уведомления из Центра разработки, вызовите метод [UnregisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.unregisternotificationchannelasync).

[!code-cs[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#UnregisterNotificationChannelAsync)]

Обратите внимание, что этот метод делает недействительным канал, который используется для уведомлений, поэтому приложение больше не будет получать push-уведомления *ни от каких* служб. После его закрытия канал больше не удастся использовать ни для каких служб, включая целевые push-уведомления из Центра разработки и другие уведомления, использующие службу WNS. Чтобы возобновить отправку push-уведомлений для этого приложения, приложение должно будет запросить новый канал.

## <a name="related-topics"></a>Статьи по теме

* [Отправка push-уведомлений пользователям вашего приложения](../publish/send-push-notifications-to-your-apps-customers.md)
* [Обзор служб push-уведомлений Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview)
* [Запрос, создание и сохранение канала уведомлений](https://docs.microsoft.com/previous-versions/windows/apps/hh868221(v=win.10))
* [Microsoft Store Services SDK](https://docs.microsoft.com/windows/uwp/monetize/microsoft-store-services-sdk)
