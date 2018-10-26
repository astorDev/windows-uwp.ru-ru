---
author: PatrickFarley
Description: Follow these best practices for geofencing in your app.
title: Руководство по приложениям, использующим функцию геозон
ms.assetid: F817FA55-325F-4302-81BE-37E6C7ADC281
ms.author: pafarley
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, карта, расположение, геозоны
ms.localizationpriority: medium
ms.openlocfilehash: 86104f00ed0189290fd0cd718042573d9d592cc3
ms.sourcegitcommit: d0e836dfc937ebf7dfa9c424620f93f3c8e0a7e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5639312"
---
# <a name="guidelines-for-geofencing-apps"></a>Руководство по приложениям, использующим функцию геозон




**Важные API**

-   [**Geofence class (XAML)**](https://msdn.microsoft.com/library/windows/apps/dn263587)
-   [**Geolocator class (XAML)**](https://msdn.microsoft.com/library/windows/apps/br225534)

Следуйте этим рекомендациям по [**созданию и настройке геозон**](https://msdn.microsoft.com/library/windows/apps/dn263744) в своем приложении.

## <a name="recommendations"></a>Рекомендации


-   Если при возникновении события [**Geofence**](https://msdn.microsoft.com/library/windows/apps/dn263587) вашему приложению понадобится доступ к Интернету, возможно, вы захотите проверить наличие такого доступа перед созданием геозоны.
    -   Если у приложения на данный момент нет доступа к Интернету, вы можете предложить пользователю подключиться к Интернету до настройки геозоны.
    -   Если возможность подключения к Интернету отсутствует, проследите за тем, чтобы функция геозон не расходовала электроэнергию на проверки расположения.
-   Убедитесь в правильности уведомлений функции геозон. Для этого проверьте метку времени и текущее расположение, когда событие геозоны показывает изменение состояния [**Entered**](https://msdn.microsoft.com/library/windows/apps/dn263660) или **Exited**. Дополнительную информацию см. далее в разделе **Проверка метки времени и текущего расположения**.
Дополнительные сведения см. далее в разделе (#меткавремени).
-   Создайте исключения для обработки сценариев, когда устройство не может получить доступ к сведениям о расположении, и при необходимости настройте уведомления для пользователя. Сведения о расположении могут оказаться недоступными при отключенных разрешениях, отсутствии радиомодуля GPS в устройстве, блокировке GPS-сигнала или слабом сигнале Wi-Fi.
-   Обычно нет необходимости прослушивать события геозон одновременно и в фоновом режиме, и на переднем плане. Если вашему приложению все же необходимо прослушивать события геозон в обоих режимах:

    -   Вызовите метод [**ReadReports**](https://msdn.microsoft.com/library/windows/apps/dn263633), чтобы узнать, произошло ли событие геозоны.
    -   Отмените регистрацию прослушивателей геозон на переднем плане, если ваше приложение невидимо для пользователя, и повторно зарегистрируйте его, когда приложение снова станет видимым.

    Примеры кода и дополнительную информацию можно найти в разделе [Прослушиватели в фоновом режиме и на переднем плане](#background-and-foreground-listeners).

-   Не используйте более 1000геозон на приложение. Фактически система поддерживает несколько тысяч геозон на приложение, но вы можете обеспечить хорошую производительность приложения, снижая потребление памяти при использовании не более 1000геозон.
-   Не создавайте геозоны с размером менее 50метров. Если приложению требуются небольшие геозоны, рекомендуйте пользователям использовать такое приложение на устройстве с радиомодулем GPS, чтобы обеспечить наилучшую производительность.

## <a name="additional-usage-guidance"></a>Дополнительные рекомендации по использованию

### <a name="checking-the-time-stamp-and-current-location"></a>Проверка метки времени и текущего расположения

Когда событие указывает на изменение состояния [**Entered**](https://msdn.microsoft.com/library/windows/apps/dn263660) или **Exited**, следует проверять и метку времени события, и текущее расположение. На фактическую обработку события пользователем могут влиять различные факторы: например, у системы не хватает ресурсов на запуск фоновой задачи, пользователь не заметил уведомления или устройство перешло в режим ожидания (в Windows). К примеру, может произойти такая последовательность событий.

-   Ваше приложение создает геозону и отслеживает события входа и выхода из нее.
-   Пользователь перемещает устройство внутрь геозоны, в результате чего срабатывает событие входа.
-   Приложение отправляет пользователю уведомление о том, что он теперь находится в геозоне.
-   Пользователь в этот момент занят и замечает уведомление только 10 минут спустя.
-   За эти 10 минут он снова выходит за пределы геозоны.

По метке времени можно понять, что событие произошло в прошлом. По текущему расположению можно понять, что пользователь в данный момент находится за пределами геозоны. В зависимости от функционала вашего приложения вы, возможно, захотите отфильтровать это событие.

### <a name="background-and-foreground-listeners"></a>Прослушиватели в фоновом режиме и на переднем плане

Обычно вашему приложению не нужно прослушивать события [**Geofence**](https://msdn.microsoft.com/library/windows/apps/dn263587) одновременно и в фоновом режиме, и на переднем плане. Но в случае, когда все же необходимо и то и другое, лучший способ решить проблему — обрабатывать уведомления в фоновом режиме. Если настроить прослушиватели геозон на переднем плане и в фоновом режиме, невозможно гарантировать, что какой-то из них сработает первым, поэтому необходимо вызывать метод [**ReadReports**](https://msdn.microsoft.com/library/windows/apps/dn263633), чтобы выяснить, произошло ли событие.

Если вы настроили прослушиватели геозон одновременно на переднем плане и в фоновом режиме, регистрация прослушивателей геозон на переднем плане должна отменяться, когда ваше приложение невидимо для пользователя, и выполняться повторно, когда оно становится видимым. Вот образец кода, который регистрирует события видимости.

```csharp
    Windows.UI.Core.CoreWindow coreWindow;    

    // This needs to be set before InitializeComponent sets up event registration for app visibility
    coreWindow = CoreWindow.GetForCurrentThread();
    coreWindow.VisibilityChanged += OnVisibilityChanged;
```

```javascript
 document.addEventListener("visibilitychange", onVisibilityChanged, false);
```

При изменении видимости можно включать или отключать обработчики событий на переднем плане, как показано здесь.

```csharp
private void OnVisibilityChanged(CoreWindow sender, VisibilityChangedEventArgs args)
{
    // NOTE: After the app is no longer visible on the screen and before the app is suspended
    // you might want your app to use toast notification for any geofence activity.
    // By registering for VisibiltyChanged the app is notified when the app is no longer visible in the foreground.

    if (args.Visible)
    {
        // register for foreground events
        GeofenceMonitor.Current.GeofenceStateChanged += OnGeofenceStateChanged;
        GeofenceMonitor.Current.StatusChanged += OnGeofenceStatusChanged;
    }
    else
    {
        // unregister foreground events (let background capture events)
        GeofenceMonitor.Current.GeofenceStateChanged -= OnGeofenceStateChanged;
        GeofenceMonitor.Current.StatusChanged -= OnGeofenceStatusChanged;
    }
}
```

```javascript
function onVisibilityChanged() {
    // NOTE: After the app is no longer visible on the screen and before the app is suspended
    // you might want your app to use toast notification for any geofence activity.
    // By registering for VisibiltyChanged the app is notified when the app is no longer visible in the foreground.

    if (document.msVisibilityState === "visible") {
        // register for foreground events
        Windows.Devices.Geolocation.Geofencing.GeofenceMonitor.current.addEventListener("geofencestatechanged", onGeofenceStateChanged);
        Windows.Devices.Geolocation.Geofencing.GeofenceMonitor.current.addEventListener("statuschanged", onGeofenceStatusChanged);
    } else {
        // unregister foreground events (let background capture events)
        Windows.Devices.Geolocation.Geofencing.GeofenceMonitor.current.removeEventListener("geofencestatechanged", onGeofenceStateChanged);
        Windows.Devices.Geolocation.Geofencing.GeofenceMonitor.current.removeEventListener("statuschanged", onGeofenceStatusChanged);
    }
}
```

### <a name="sizing-your-geofences"></a>Определение размера геозон

Хотя GPS может предоставить наиболее точные сведения о местонахождении, для определения текущего расположения пользователя функция геозон может также использовать Wi-Fi или другие датчики местонахождения. Тем не менее использование других методов может повлиять на размер создаваемых геозон. Если степень точности невысока, бесполезно создавать маленькие геозоны. В общем случае рекомендуется не создавать геозоны с радиусом менее 50метров. Кроме того, фоновые задачи геозон выполняются в Windows только периодически. Если вы используете маленькие геозоны, может случиться так, что вы вообще пропустите событие [**Enter**](https://msdn.microsoft.com/library/windows/apps/dn263660) или **Exit**.

Если приложению требуются небольшие геозоны, рекомендуйте пользователям использовать такое приложение на устройстве с радиомодулем GPS, чтобы обеспечить наилучшую производительность.

## <a name="related-topics"></a>Связанные разделы


* [Настройка геозоны](https://msdn.microsoft.com/library/windows/apps/mt219702)
* [Получение сведений о текущем расположении](https://msdn.microsoft.com/library/windows/apps/mt219698)
* [Пример расположения для универсальной платформы Wndows (UWP) (geolocation)](http://go.microsoft.com/fwlink/p/?linkid=533278)
 

 
