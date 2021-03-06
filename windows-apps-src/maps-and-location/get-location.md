---
title: Получение местоположения пользователя
description: Определение местоположения пользователя и реагирование на его изменение. Доступ к сведениям о местоположении пользователя управляется параметрами конфиденциальности в приложении «Параметры». В этой статье рассказывается, как проверить, имеется ли у вашего приложения разрешение на доступ к данным о местоположении пользователя.
ms.assetid: 24DC9A41-8CC1-48B0-BC6D-24BF571AFCC8
ms.date: 11/28/2017
ms.topic: article
keywords: windows 10, uwp, карта, расположение, функция определения местоположения
ms.localizationpriority: medium
ms.openlocfilehash: 50f605164a496d00113b73ffeae669e3ff145535
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74260395"
---
# <a name="get-the-users-location"></a>Получение местоположения пользователя




Определение местоположения пользователя и реагирование на его изменение. Доступ к сведениям о местоположении пользователя управляется параметрами конфиденциальности в приложении «Параметры». В этой статье рассказывается, как проверить, имеется ли у вашего приложения разрешение на доступ к данным о местоположении пользователя.

**Совет.** Чтобы получить дополнительные сведения о доступе к данным о местоположении в приложении, скачайте следующий пример из [репозитория Windows-universal-samples](https://github.com/Microsoft/Windows-universal-samples) на веб-сайте GitHub.

-   [Пример карты универсальной платформы Windows (UWP)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)

## <a name="enable-the-location-capability"></a>Включение функции определения расположения


1.  В **обозревателе решений** дважды щелкните файл **package.appxmanifest** и выберите вкладку **Возможности**.
2.  В списке **Возможности** установите флажок **Расположение**. Возможность устройства `location` будет добавлена к файлу манифеста пакета.

```XML
  <Capabilities>
    <!-- DeviceCapability elements must follow Capability elements (if present) -->
    <DeviceCapability Name="location"/>
  </Capabilities>
```

## <a name="get-the-current-location"></a>Получение текущего местоположения


В этом разделе представлены сведения о том, как определить географическое положение пользователя с помощью API в пространстве имен [**Windows.Devices.Geolocation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation).

### <a name="step-1-request-access-to-the-users-location"></a>Шаг 1. Запрос доступа к данным о расположении пользователя

Если приложение не имеет возможности грубого расположения (см. Примечание), необходимо запросить доступ к расположению пользователя с помощью метода [**рекуестакцессасинк**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync) , прежде чем пытаться получить доступ к расположению. Метод **RequestAccessAsync** необходимо вызвать из потока пользовательского интерфейса, а ваше приложение должно находиться на переднем плане. Приложение не сможет получить доступ к сведениям о расположении пользователя до тех пор, пока пользователь не предоставит разрешение для вашего приложения.\*

```csharp
using Windows.Devices.Geolocation;
...
var accessStatus = await Geolocator.RequestAccessAsync();
```



Метод [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync) запрашивает у пользователя разрешение на доступ к данным о местоположении. Каждое приложение запрашивает у пользователя доступ только один раз. После того как пользователь в первый раз предоставил или запретил доступ, этот метод больше не запрашивает его у пользователя. Чтобы помочь пользователю изменять разрешения на доступ к данным о местоположении после первоначального решения, рекомендуется предоставить ссылку на параметры местоположения, как показано далее в этом разделе.

>Примечание. функция грубого расположения позволяет приложению получить намеренно замаскированное (неточное) расположение, не получая явного разрешения пользователя (параметр расположения на уровне системы должен оставаться **включенным**). Сведения о том, как использовать грубое расположение в приложении, см. в описании метода [**алловфаллбакктоконсентлесспоситионс**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.allowfallbacktoconsentlesspositions) в классе [**геоуказателя**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator) .

### <a name="step-2-get-the-users-location-and-register-for-changes-in-location-permissions"></a>Шаг 2. Определение местоположения пользователя и регистрация изменений в разрешениях на доступ к данным о местоположении

Метод [**GetGeopositionAsync**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.getgeopositionasync) выполняет однократное считывание текущего местоположения. В этом примере оператор **switch** используется с **accessStatus** (из предыдущего примера), за счет чего действует, только когда доступ к сведениям о местоположении пользователя разрешен. Если доступ к местоположению пользователя разрешен, код создает объект [**Geolocator**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator), регистрирует изменения в разрешениях на доступ к данным о местоположении и запрашивает местоположение пользователя.

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

### <a name="step-3-handle-changes-in-location-permissions"></a>Шаг 3. Обработка изменений в разрешениях, связанных с местоположением

Объект [**Geolocator**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator) запускает событие [**StatusChanged**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.statuschanged) для указания того, что местоположение пользователя изменилось. Это событие передает данные о соответствующем состоянии через свойство аргумента **Status** (типа [**PositionStatus**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.PositionStatus)). Обратите внимание, что этот метод не вызывается из потока пользовательского интерфейса и объект [**Dispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) фактически вызывает изменения пользовательского интерфейса.

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

## <a name="respond-to-location-updates"></a>Реакция на изменение местоположения


В этом разделе описывается, как использовать событие [**PositionChanged**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.positionchanged), чтобы получать обновления данных о местоположении пользователя в течение времени. Поскольку пользователь может в любое время отменить доступ к сведениям о местоположении, важно вызывать [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync) и использовать событие [**StatusChanged**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.statuschanged), как указано в предыдущем разделе.

В этом разделе предполагается, что вы уже включили определение местоположения и вызвали [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync) из потока пользовательского интерфейса вашего приложения, выполняющегося на переднем плане.

### <a name="step-1-define-the-report-interval-and-register-for-location-updates"></a>Шаг 1. Задание интервала отчетов и регистрация для обновления данных о местоположении

В этом примере оператор **switch** используется с **accessStatus** (из предыдущего примера), за счет чего действует, только когда доступ к сведениям о местоположении пользователя разрешен. Если доступ к местоположению пользователя разрешен, код создает объект [**Geolocator**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator), указывающий тип отслеживания, и регистрируется для обновления данных о местоположении.

Объект [**Geolocator**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator) может инициировать событие [**PositionChanged**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.positionchanged) на основе изменения положения (отслеживание на основе расстояния) или времени (периодическое отслеживание).

-   Для отслеживания на основе расстояния задайте свойство [**MovementThreshold**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.movementthreshold).
-   Для периодического отслеживания задайте свойство [**ReportInterval**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.reportinterval).

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

### <a name="step-2-handle-location-updates"></a>Шаг 2. Обработка обновлений данных о местоположении

Объект [**Geolocator**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator) инициирует событие [**PositionChanged**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.positionchanged) для указания, что местоположение пользователя изменилось или прошло время, в зависимости от настроек. Это событие передает данные о соответствующем местоположении через свойство аргумента **Position** (типа [**Geoposition**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geoposition)). В этом примере показано, что метод не вызывается из потока пользовательского интерфейса и объект [**Dispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher) фактически вызывает изменения пользовательского интерфейса.

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

## <a name="change-the-location-privacy-settings"></a>Изменение параметров конфиденциальности данных о местоположении


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

Ваше приложение также может вызвать метод [**LaunchUriAsync**](https://docs.microsoft.com/uwp/api/windows.system.launcher.launchuriasync), чтобы запустить приложение **Параметры** из кода. Дополнительные сведения см. в разделе [Запуск приложения "Параметры" в Windows](https://docs.microsoft.com/windows/uwp/launch-resume/launch-settings-app).

```csharp
using Windows.System;
...
bool result = await Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-location"));
```

## <a name="troubleshoot-your-app"></a>Устранение неполадок приложения


Для того чтобы ваше приложение могло получить доступ к данным о местоположении пользователя, необходимо включить на устройстве функцию **Местоположение**. Убедитесь, что в приложении **Параметры** включены следующие **параметры конфиденциальности данных о расположении**.

-   **Расположение для этого устройства...** **включен (** неприменимо в Windows 10 Mobile)
-   Параметр **Местоположение** служб определения местоположения **включен**
-   В разделе **Выберите приложения, которым будет разрешено использовать данные о вашем местоположении** для вашего приложения установлено значение **Вкл.**

## <a name="related-topics"></a>См. также

* [Пример определения географического положения UWP](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Geolocation)
* [Рекомендации по проектированию для георазграничения](https://docs.microsoft.com/windows/uwp/maps-and-location/guidelines-for-geofencing)
* [Рекомендации по разработке приложений, учитывающих данные о расположении](https://docs.microsoft.com/windows/uwp/maps-and-location/guidelines-and-checklist-for-detecting-location)
