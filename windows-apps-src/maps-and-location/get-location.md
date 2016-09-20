---
author: PatrickFarley
title: "Получение местоположения пользователя"
description: "Определение местоположения пользователя и реагирование на его изменение. Доступ к сведениям о местоположении пользователя управляется параметрами конфиденциальности в приложении «Параметры». В этой статье рассказывается, как проверить, имеется ли у вашего приложения разрешение на доступ к данным о местоположении пользователя."
ms.assetid: 24DC9A41-8CC1-48B0-BC6D-24BF571AFCC8
ms.sourcegitcommit: 98b9bca2528c041d2fdfc6a0adead321737932b4
ms.openlocfilehash: d35bf3ef13e2b36dfed6613a00f65d19b9013464

---

# Получение местоположения пользователя


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Определение местоположения пользователя и реагирование на его изменение. Доступ к сведениям о местоположении пользователя управляется параметрами конфиденциальности в приложении «Параметры». В этой статье рассказывается, как проверить, имеется ли у вашего приложения разрешение на доступ к данным о местоположении пользователя.


            **Подсказка.** Чтобы получить дополнительные сведения о доступе к данным о местоположении в приложении, скачайте следующий пример из [репозитория Windows-universal-samples](http://go.microsoft.com/fwlink/p/?LinkId=619979) на веб-сайте GitHub.

-   [Пример карты универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619977)

## Включение функции определения местоположения


1.  В **обозревателе решений** дважды щелкните файл **package.appxmanifest** и выберите вкладку **Возможности**.
2.  В списке **Возможности** выберите вкладку **Возможности**. Возможность устройства `Location` будет добавлена к файлу манифеста пакета.

```XML
  <Capabilities>
    <!-- DeviceCapability elements must follow Capability elements (if present) -->
    <DeviceCapability Name="location"/>
  </Capabilities>
```

## Получение текущего местоположения


В этом разделе представлены сведения о том, как определить географическое положение пользователя с помощью API в пространстве имен [**Windows.Devices.Geolocation**](https://msdn.microsoft.com/library/windows/apps/br225603).

### Шаг1. Запрос доступа к данным о местоположении пользователя


            **Важно!** Необходимо запросить доступ к данным о местоположении пользователя с помощью метода [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn859152), прежде чем попытаться получить доступ к этим данным. Из потока пользовательского интерфейса необходимо вызвать метод **RequestAccessAsync**, а ваше приложение должно находиться на переднем плане. Приложение не сможет получить доступ к информации о местоположении пользователя, пока пользователь не предоставит разрешение вашему приложению.

```csharp
using Windows.Devices.Geolocation;
...
var accessStatus = await Geolocator.RequestAccessAsync();
```

Метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn859152) запрашивает у пользователя разрешение на доступ к данным о местоположении. Каждое приложение запрашивает у пользователя доступ только один раз. После того как пользователь в первый раз предоставил или запретил доступ, этот метод больше не запрашивает его у пользователя. Чтобы помочь пользователю изменять разрешения на доступ к данным о местоположении после первоначального решения, рекомендуется предоставить ссылку на параметры местоположения, как показано далее в этом разделе.

### Шаг2. Определение местоположения пользователя и регистрация изменений в разрешениях на доступ к данным о местоположении

Метод [**GetGeopositionAsync**](https://msdn.microsoft.com/library/windows/apps/hh973536) выполняет однократное считывание текущего местоположения. В этом примере оператор **switch** используется с **accessStatus** (из предыдущего примера), за счет чего действует, только когда доступ к сведениям о местоположении пользователя разрешен. Если доступ к местоположению пользователя разрешен, код создает объект [**Geolocator**](https://msdn.microsoft.com/library/windows/apps/br225534), регистрирует изменения в разрешениях на доступ к данным о местоположении и запрашивает местоположение пользователя.

```csharp
switch (accessStatus)
{
    case GeolocationAccessStatus.Allowed:
        _rootPage.NotifyUser("Waiting for update...", NotifyType.StatusMessage);

        // If DesiredAccuracy or DesiredAccuracyInMeters are not set (or value is 0), DesiredAccuracy.Default is used.
        Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = _desireAccuracyInMetersValue };

        // Subscribe to the StatusChanged event to get updates of location status changes.
        _geolocator.StatusChanged += OnStatusChanged;
                        
        // Carry out the operation.
        Geoposition pos = await geolocator.GetGeopositionAsync();

        UpdateLocationData(pos);
        _rootPage.NotifyUser("Location updated.", NotifyType.StatusMessage);
        break;

    case GeolocationAccessStatus.Denied:
        _rootPage.NotifyUser("Access to location is denied.", NotifyType.ErrorMessage);
        LocationDisabledMessage.Visibility = Visibility.Visible;
        UpdateLocationData(null);
        break;

    case GeolocationAccessStatus.Unspecified:
        _rootPage.NotifyUser("Unspecified error.", NotifyType.ErrorMessage);
        UpdateLocationData(null);
        break;
}
```

### Шаг3. Обработка изменений в разрешениях, связанных с местоположением

Объект [**Geolocator**](https://msdn.microsoft.com/library/windows/apps/br225534) запускает событие [**StatusChanged**](https://msdn.microsoft.com/library/windows/apps/br225542) для указания того, что местоположение пользователя изменилось. Это событие передает данные о соответствующем состоянии через свойство аргумента **Status** (типа [**PositionStatus**](https://msdn.microsoft.com/library/windows/apps/br225599)). Обратите внимание, что этот метод не вызывается из потока пользовательского интерфейса и объект [**Dispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) фактически вызывает изменения пользовательского интерфейса.

```csharp
using Windows.UI.Core;
...
async private void OnStatusChanged(Geolocator sender, StatusChangedEventArgs e)
{
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        // Show the location setting message only if status is disabled.
        LocationDisabledMessage.Visibility = Visibility.Collapsed;

        switch (e.Status)
        {
            case PositionStatus.Ready:
                // Location platform is providing valid data.
                ScenarioOutput_Status.Text = "Ready";
                _rootPage.NotifyUser("Location platform is ready.", NotifyType.StatusMessage);
                break;

            case PositionStatus.Initializing:
                // Location platform is attempting to acquire a fix. 
                ScenarioOutput_Status.Text = "Initializing";
                _rootPage.NotifyUser("Location platform is attempting to obtain a position.", NotifyType.StatusMessage);
                break;

            case PositionStatus.NoData:
                // Location platform could not obtain location data.
                ScenarioOutput_Status.Text = "No data";
                _rootPage.NotifyUser("Not able to determine the location.", NotifyType.ErrorMessage);
                break;

            case PositionStatus.Disabled:
                // The permission to access location data is denied by the user or other policies.
                ScenarioOutput_Status.Text = "Disabled";
                _rootPage.NotifyUser("Access to location is denied.", NotifyType.ErrorMessage);

                // Show message to the user to go to location settings.
                LocationDisabledMessage.Visibility = Visibility.Visible;

                // Clear any cached location data.
                UpdateLocationData(null);
                break;

            case PositionStatus.NotInitialized:
                // The location platform is not initialized. This indicates that the application 
                // has not made a request for location data.
                ScenarioOutput_Status.Text = "Not initialized";
                _rootPage.NotifyUser("No request for location is made yet.", NotifyType.StatusMessage);
                break;

            case PositionStatus.NotAvailable:
                // The location platform is not available on this version of the OS.
                ScenarioOutput_Status.Text = "Not available";
                _rootPage.NotifyUser("Location is not available on this version of the OS.", NotifyType.ErrorMessage);
                break;

            default:
                ScenarioOutput_Status.Text = "Unknown";
                _rootPage.NotifyUser(string.Empty, NotifyType.StatusMessage);
                break;
        }
    });
}
```

## Реакция на изменение местоположения


В этом разделе описывается, как использовать событие [**PositionChanged**](https://msdn.microsoft.com/library/windows/apps/br225540), чтобы получать обновления данных о местоположении пользователя в течение времени. Поскольку пользователь может в любое время отменить доступ к сведениям о местоположении, важно вызывать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn859152) и использовать событие [**StatusChanged**](https://msdn.microsoft.com/library/windows/apps/br225542), как указано в предыдущем разделе.

В этом разделе предполагается, что вы уже включили определение местоположения и вызвали [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn859152) из потока пользовательского интерфейса вашего приложения, выполняющегося на переднем плане.

### Шаг1. Задание интервала отчетов и регистрация для обновления данных о местоположении

В этом примере оператор **switch** используется с **accessStatus** (из предыдущего примера), за счет чего действует, только когда доступ к сведениям о местоположении пользователя разрешен. Если доступ к местоположению пользователя разрешен, код создает объект [**Geolocator**](https://msdn.microsoft.com/library/windows/apps/br225534), указывающий тип отслеживания, и регистрируется для обновления данных о местоположении.

Объект [**Geolocator**](https://msdn.microsoft.com/library/windows/apps/br225534) может инициировать событие [**PositionChanged**](https://msdn.microsoft.com/library/windows/apps/br225540) на основе изменения положения (отслеживание на основе расстояния) или времени (периодическое отслеживание).

-   Для отслеживания на основе расстояния задайте свойство [**MovementThreshold**](https://msdn.microsoft.com/library/windows/apps/br225539).
-   Для периодического отслеживания задайте свойство [**ReportInterval**](https://msdn.microsoft.com/library/windows/apps/br225541).

Если не задано ни одно, ни другое свойство, положение будет возвращаться каждую секунду (эквивалентно `ReportInterval = 1000`). Здесь используется интервал отчетов, равный 2 секундам (`ReportInterval = 2000`).
```csharp
using Windows.Devices.Geolocation;
...
var accessStatus = await Geolocator.RequestAccessAsync();

switch (accessStatus)
{
    case GeolocationAccessStatus.Allowed:
        // Create Geolocator and define perodic-based tracking (2 second interval).
        _geolocator = new Geolocator { ReportInterval = 2000 };

        // Subscribe to the PositionChanged event to get location updates.
        _geolocator.PositionChanged += OnPositionChanged;

        // Subscribe to StatusChanged event to get updates of location status changes.
        _geolocator.StatusChanged += OnStatusChanged;
                    
        _rootPage.NotifyUser("Waiting for update...", NotifyType.StatusMessage);
        LocationDisabledMessage.Visibility = Visibility.Collapsed;
        StartTrackingButton.IsEnabled = false;
        StopTrackingButton.IsEnabled = true;
        break;

    case GeolocationAccessStatus.Denied:
        _rootPage.NotifyUser("Access to location is denied.", NotifyType.ErrorMessage);
        LocationDisabledMessage.Visibility = Visibility.Visible;
        break;

    case GeolocationAccessStatus.Unspecified:
        _rootPage.NotifyUser("Unspecificed error!", NotifyType.ErrorMessage);
        LocationDisabledMessage.Visibility = Visibility.Collapsed;
        break;
}
```

### Шаг2. Обработка обновлений данных о местоположении

Объект [**Geolocator**](https://msdn.microsoft.com/library/windows/apps/br225534) инициирует событие [**PositionChanged**](https://msdn.microsoft.com/library/windows/apps/br225540) для указания, что местоположение пользователя изменилось или прошло время, в зависимости от настроек. Это событие передает данные о соответствующем местоположении через свойство аргумента **Position** (типа [**Geoposition**](https://msdn.microsoft.com/library/windows/apps/br225543)). В этом примере показано, что метод не вызывается из потока пользовательского интерфейса и объект [**Dispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) фактически вызывает изменения пользовательского интерфейса.

```csharp
using Windows.UI.Core;
...
async private void OnPositionChanged(Geolocator sender, PositionChangedEventArgs e)
{
    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        _rootPage.NotifyUser("Location updated.", NotifyType.StatusMessage);
        UpdateLocationData(e.Position);
    });
}
```

## Изменение параметров конфиденциальности данных о местоположении


Если параметры конфиденциальности данных о местоположении не позволяют вашему приложению получать доступ к данным о местоположении пользователя, рекомендуется реализовать удобную ссылку на **параметры конфиденциальности данных о местоположении** в приложении **Параметры**. В этом примере используется элемент управления «Гиперссылка» для перехода к URI `ms-settings:privacy-location`.

```xml
<!--Set Visibility to Visible when access to location is denied -->  
<TextBlock x:Name="LocationDisabledMessage" FontStyle="Italic" 
                 Visibility="Collapsed" Margin="0,15,0,0" TextWrapping="Wrap" >
          <Run Text="This app is not able to access Location. Go to " />
              <Hyperlink NavigateUri="ms-settings:privacy-location">
                  <Run Text="Settings" />
              </Hyperlink>
          <Run Text=" to check the location privacy settings."/>
</TextBlock>
```

Либо ваше приложение может вызвать метод [**LaunchUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701476), чтобы запустить приложение **Параметры** из кода. Дополнительные сведения см. в разделе [Запуск приложения «Параметры» для Windows](https://msdn.microsoft.com/library/windows/apps/mt228342).

```csharp
using Windows.System;
...
bool result = await Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-location"));
```

## Устранение неполадок приложения


Для того чтобы ваше приложение могло получить доступ к данным о местоположении пользователя, необходимо включить на устройстве функцию **Местоположение**. Убедитесь, что в приложении **Параметры** включены следующие **параметры конфиденциальности данных о местоположении**.

-   
            Параметр **Расположение для этого устройства...** **включен** (неприменимо в Windows 10 Mobile)
-   Параметр **Местоположение** служб определения местоположения **включен**
-   В разделе **Выберите приложения, которым будет разрешено использовать данные о вашем местоположении** для вашего приложения установлено значение **Вкл.**

## Ссылки по теме

* [Пример определения географического положения UWP](http://go.microsoft.com/fwlink/p/?linkid=533278)
* [Рекомендации для разработчиков относительно создания геозон](https://msdn.microsoft.com/library/windows/apps/dn631756)
* [Рекомендации по разработке приложений с определением местоположения](https://msdn.microsoft.com/library/windows/apps/hh465148)





<!--HONumber=Jun16_HO4-->


