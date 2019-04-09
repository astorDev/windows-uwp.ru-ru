---
Description: Дополнительные сведения о регистрации приложения универсальной платформы Windows для приема Push-уведомления, отправляемые из центра партнеров.
title: Настройка приложения для получения целевых push-уведомлений
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, универсальной платформы Windows, Microsoft Store Services SDK, целевые Push-уведомлений, центра партнеров
ms.assetid: 30c832b7-5fbe-4852-957f-7941df8eb85a
ms.localizationpriority: medium
ms.openlocfilehash: a23da0bf740abfeece0047b8afab2ebff987f9d1
ms.sourcegitcommit: 6a7dd4da2fc31ced7d1cdc6f7cf79c2e55dc5833
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58335052"
---
# <a name="configure-your-app-for-targeted-push-notifications"></a>Настройка приложения для получения целевых push-уведомлений

Можно использовать **Push-уведомления** страницы в центре партнеров хочет напрямую привлечь клиентов, отправляя целевые Push-уведомлений на устройства, на которых установлено приложение универсальной платформы Windows (UWP). Например, можно использовать целевые push-уведомления для побуждения пользователей к определенным действиям, таким как выставление оценки вашему приложению или использование новой функции. Можно отправлять различные типы push-уведомлений, включая всплывающие уведомления, уведомления на плитках и необработанные уведомления XML. Также можно отслеживать количество запусков приложения, производимых из push-уведомлений. Дополнительные сведения об этой функции см. в разделе [Отправка push-уведомлений пользователям вашего приложения](../publish/send-push-notifications-to-your-apps-customers.md).

Прежде чем отправлять целевые Push-уведомления своим клиентам из центра партнеров, необходимо использовать метод [StoreServicesEngagementManager](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager) класс в Microsoft Store Services SDK для регистрации приложения для приема уведомления. Дополнительные методы этого класса можно использовать для уведомления центра партнеров, что приложение было запущено в ответ на целевых Push-уведомления (Если вы хотите отслеживать скорость запусков приложений, которые были приняты уведомлений) и прекратить получение уведомлений.

## <a name="configure-your-project"></a>Настройка проекта

Прежде чем писать код, выполните следующие действия, чтобы добавить ссылку на Microsoft Store Services SDK в свой проект.

1. Если это еще не сделано, [установите Microsoft Store Services SDK](microsoft-store-services-sdk.md#install-the-sdk) на компьютер разработки. 
2. Откройте проект в Visual Studio.
3. В обозревателе решений щелкните правой кнопкой мыши узел **Ссылки** вашего проекта и выберите команду **Добавить ссылку**.
4. В диалоговом окне **Диспетчер ссылок** разверните список **Универсальная платформа Windows** и выберите **Расширения**.
5. В списке пакетов SDK установите флажок рядом с пунктом **Microsoft Engagement Framework** и нажмите кнопку **ОК**.

## <a name="register-for-push-notifications"></a>Регистрация для получения push-уведомлений

Регистрация приложения для приема целевых Push-уведомлений из центра партнеров:

1. В своем проекте найдите раздел кода, выполняемого во время запуска, в котором можно зарегистрировать приложение для получения уведомлений.
2. Добавьте в начало файла с кодом следующий оператор.

    [!code-csharp[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#EngagementNamespace)]

3. Получите объект [StoreServicesEngagementManager](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager) и вызовите один из перегруженных методов [RegisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) в коде запуска, определенном вами ранее. Этот метод следует вызывать каждый раз при запуске приложения.

  * Если требуется, чтобы центр партнеров, чтобы создать свой собственный URI канала для получения уведомлений, вызовите [RegisterNotificationChannelAsync()](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) перегрузки.

      [!code-csharp[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#RegisterNotificationChannelAsync1)]
      > [!IMPORTANT]
      > Если ваше приложение также вызывает [CreatePushNotificationChannelForApplicationAsync](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannelmanager.createpushnotificationchannelforapplicationasync) для создания канала уведомлений для WNS, убедитесь, что ваш код не вызывает [CreatePushNotificationChannelForApplicationAsync](https://docs.microsoft.com/uwp/api/windows.networking.pushnotifications.pushnotificationchannelmanager.createpushnotificationchannelforapplicationasync) и перегрузку [RegisterNotificationChannelAsync()](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) одновременно. Если необходимо вызвать оба этих метода, проследите за тем, чтобы они вызывались последовательно и дожидались возврата из одного метода перед вызовом второго.

  * Если вы хотите указать канал URI, используемый для целевых Push-уведомлений из центра партнеров, вызов [RegisterNotificationChannelAsync(StoreServicesNotificationChannelParameters)](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.registernotificationchannelasync) перегрузки. Например, это может потребоваться, если ваше приложение уже использует службы push-уведомлений Windows (WNS) и вы хотите использовать один и тот же URI канала. Сначала необходимо создать объект [StoreServicesNotificationChannelParameters](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesnotificationchannelparameters) и назначить свойство [CustomNotificationChannelUri](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesnotificationchannelparameters.customnotificationchanneluri) вашему универсальному коду ресурса (URI) канала.

      [!code-csharp[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#RegisterNotificationChannelAsync2)]

> [!NOTE]
> При вызове метода **RegisterNotificationChannelAsync** в локальном хранилище данных приложения создается файл с именем MicrosoftStoreEngagementSDKId.txt для вашего приложения (папка, возвращенная свойством [ApplicationData.LocalFolder](https://docs.microsoft.com/uwp/api/Windows.Storage.ApplicationData.LocalFolder)). Этот файл содержит идентификатор, используемый инфраструктурой целевых push уведомлений. Убедитесь, что ваше приложение не изменило или не удалило этот файл. В противном случае ваши пользователи могут получать несколько экземпляров уведомлений, либо уведомления будут работать неправильно.

<span id="notification-customers" />

### <a name="how-targeted-push-notifications-are-routed-to-customers"></a>Передача целевых push-уведомлений пользователям

Когда ваше приложение вызывает метод **RegisterNotificationChannelAsync**, этот метод получает информацию об учетной записи Майкрософт того пользователя, который в данный момент вошел на устройство. Позже при отправке целевых Push-уведомление на сегмент, который включает в себя этого клиента центра партнеров отправляет уведомление на устройствах, которые связаны с учетной записью Майкрософт этого клиента.

Помните, что если пользователь, запустивший ваше приложение, передает свое устройство в пользование другому пользователю, то другой пользователь может увидеть уведомление, предназначенное первому пользователю, пока они находятся в системе под своими учетными записями Майкрософт. Это может привести к непредвиденным последствиям, особенно для приложений, предоставляющих услуги, которыми пользователь может воспользоваться, выполнив вход. В этом случае, чтобы запретить другим пользователям просматривать целевые уведомления, вызовите метод [UnregisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.unregisternotificationchannelasync), когда пользователи выйдут из вашего приложения. Дополнительные сведения см. далее в разделе [Отмена регистрации для получения push-уведомлений](#unregister) этой статьи.

### <a name="how-your-app-responds-when-the-user-launches-your-app"></a>Реакция вашего приложения, когда его запускает пользователь

После регистрации приложения для получения уведомлений и вы [отправлять Push-уведомление приложения клиентам из центра партнеров](../publish/send-push-notifications-to-your-apps-customers.md), одной из следующих точек входа в вашем приложении будет вызываться, когда пользователь запускает приложение в ответ Чтобы Push-уведомления. Если у вас есть какой-либо код, который требуется выполнять, когда пользователь запускает ваше приложение, можно добавить этот код в одну из этих точек входа в приложении.

  * Если push-уведомление имеет тип активации переднего плана, переопределите метод [OnActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onactivated) класса **App** в вашем проекте и добавьте свой код в этот метод.

  * Если push-уведомление имеет фоновый тип активации, добавьте свой код в метод [Run](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) для [фоновой задачи](../launch-resume/support-your-app-with-background-tasks.md).

Например, вы решите вознаграждать пользователей вашего приложения, которые приобрели какие-либо платные надстройки в приложении, путем предоставления им бесплатной надстройки. В этом случае можно отправлять push-уведомление определенному [сегменту пользователей](../publish/create-customer-segments.md), нацеленное на этих пользователей. Затем можно добавить код, чтобы предоставить им право бесплатной [покупки из приложения](in-app-purchases-and-trials.md), в одну из перечисленных выше точек входа.

## <a name="notify-partner-center-of-your-app-launch"></a>Уведомления центра партнеров из запуск своего приложения

При выборе **скорости запуска приложений Track** вариант для целевых Push-уведомления в центре партнеров, вызов [ParseArgumentsAndTrackAppLaunch](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.parseargumentsandtrackapplaunch) метод из точки соответствующую запись в приложении уведомления центра партнеров, что приложение было запущено в ответ на Push-уведомление.

Этот метод также возвращает исходные аргументы запуска для приложения. При выборе для отслеживания скорости запуска приложений для Push-уведомления, непрозрачный отслеживания, добавляется идентификатор запуска аргументы запуска для отслеживания приложения в центре партнеров. Необходимо передать аргументы запуска для приложения, чтобы [ParseArgumentsAndTrackAppLaunch](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.parseargumentsandtrackapplaunch) метод и этот метод отправляет идентификатор отслеживания для центра партнеров, удаляет идентификатор отслеживания из аргументы запуска и возвращается исходное Запустите аргументы в код.

Способ вызова этого метода зависит от типа активации push-уведомления:

* Если push-уведомление имеет тип активации переднего плана, вызовите этот метод из переопределения метода [OnActivated](https://docs.microsoft.com/uwp/api/windows.ui.xaml.application.onactivated) в своем приложении и передайте аргументы, доступные в объекте [ToastNotificationActivatedEventArgs](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Activation.ToastNotificationActivatedEventArgs), передаваемом этому методу. В следующем примере кода предполагается, что файл кода содержит операторы **using** для пространств имен **Microsoft.Services.Store.Engagement** и **Windows.ApplicationModel.Activation**.

  [!code-csharp[DevCenterNotifications](./code/StoreSDKSamples/cs/App.xaml.cs#OnActivated)]

* Если push-уведомление имеет фоновый тип активации, вызовите этот метод из метода [Run](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.ibackgroundtask.run) вашей [фоновой задачи](../launch-resume/support-your-app-with-background-tasks.md) и передайте аргументы, доступные в объекте [ToastNotificationActionTriggerDetail](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotificationActionTriggerDetail), передаваемом этому методу. В следующем примере кода предполагается, что ваш файл кода содержит операторы **using** для пространств имен **Microsoft.Services.Store.Engagement**, **Windows.ApplicationModel.Background** и **Windows.UI.Notifications**.

  [!code-csharp[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#Run)]

<span id="unregister" />

## <a name="unregister-for-push-notifications"></a>Отмена регистрации для получения push-уведомлений

Если вы хотите, чтобы отказаться от получения приложения целевых Push-уведомлений из центра партнеров, вызов [UnregisterNotificationChannelAsync](https://docs.microsoft.com/uwp/api/microsoft.services.store.engagement.storeservicesengagementmanager.unregisternotificationchannelasync) метод.

[!code-csharp[DevCenterNotifications](./code/StoreSDKSamples/cs/DevCenterNotifications.cs#UnregisterNotificationChannelAsync)]

Обратите внимание, что этот метод делает недействительным канал, который используется для уведомлений, поэтому приложение больше не будет получать push-уведомления *ни от каких* служб. После его закрытия канала не может использоваться снова для всех служб, включая целевые Push-уведомления из центра партнеров и других уведомлений, с помощью WNS. Чтобы возобновить отправку push-уведомлений для этого приложения, приложение должно будет запросить новый канал.

## <a name="related-topics"></a>См. также

* [Отправка уведомлений пользователям вашего приложения](../publish/send-push-notifications-to-your-apps-customers.md)
* [Обзор служб push-уведомлений Windows (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview)
* [Запрос, создание и сохранение канала уведомлений с помощью](https://docs.microsoft.com/previous-versions/windows/apps/hh868221(v=win.10))
* [Microsoft Store Services SDK](https://docs.microsoft.com/windows/uwp/monetize/microsoft-store-services-sdk)
