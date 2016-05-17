---
author: PatrickFarley
title: Настройка геозоны
description: Настройте геозоны в своем приложении и узнайте, как обрабатывать уведомления на переднем и фоновом планах.
ms.assetid: A3A46E03-0751-4DBD-A2A1-2323DB09BDBA
---

# Настройка геозоны


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x, см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Настройте [**геозоны**](https://msdn.microsoft.com/library/windows/apps/dn263587) в своем приложении и узнайте, как обрабатывать уведомления на переднем и фоновом планах.

**Подсказка.** Чтобы получить дополнительные сведения о доступе к данным о расположении в приложении, загрузите следующий пример из [репозитория Windows-universal-samples](http://go.microsoft.com/fwlink/p/?LinkId=619979) на веб-сайте GitHub.

-   [Пример карты универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619977)

## Включение функции определения расположения


1.  В **Обозревателе решений** дважды щелкните файл **package.appxmanifest** и выберите вкладку **Возможности**.
2.  В списке **Расположение** выберите пункт **Расположение**. Возможность устройства `Location` будет добавлена к файлу манифеста пакета.

```xml
  <Capabilities>
    <!-- DeviceCapability elements must follow Capability elements (if present) -->
    <DeviceCapability Name="location"/>
  </Capabilities>
```

## Настройка геозоны


### Шаг 1. Запрос доступа к данным о расположении пользователя

**Важно!** Необходимо запросить доступ к данным о местоположении пользователя с помощью метода [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn859152), прежде чем попытаться получить доступ к этим данным. Из потока пользовательского интерфейса необходимо вызвать метод **RequestAccessAsync**, а ваше приложение должно находиться на переднем плане. Приложение не сможет получить доступ к информации о местоположении пользователя, пока пользователь не предоставит разрешение вашему приложению.

```csharp
using Windows.Devices.Geolocation;
...
var accessStatus = await Geolocator.RequestAccessAsync();
```

Метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn859152) запрашивает у пользователя разрешение на доступ к данным о местоположении. Каждое приложение запрашивает у пользователя доступ только один раз. После того как пользователь в первый раз предоставил или запретил доступ, этот метод больше не запрашивает его у пользователя. Чтобы помочь пользователю изменять разрешения на доступ к данным о местоположении после первоначального решения, рекомендуется предоставить ссылку на параметры местоположения, как показано далее в этом разделе.

### Шаг 2. Регистрация изменений состояния геозоны и разрешений на доступ к данным о расположении

В этом примере оператор **switch** используется с **accessStatus** (из предыдущего примера), за счет чего действует, только когда доступ к сведениям о местоположении пользователя разрешен. Если доступ к расположению пользователя разрешен, код получает доступ к текущим геозонам, регистрируется на получение изменений состояния геозоны и изменений разрешений на доступ к информации о расположении.

**Подсказка.** При использовании геозоны для отслеживания изменений в разрешениях на доступ к информации о расположении, используйте событие [**StatusChanged**](https://msdn.microsoft.com/library/windows/apps/dn263646) класса GeofenceMonitor вместо события StatusChanged из класса Geolocator. Состояние [**GeofenceMonitorStatus**](https://msdn.microsoft.com/library/windows/apps/dn263599) значения **Disabled** эквивалентно отключению [**PositionStatus**](https://msdn.microsoft.com/library/windows/apps/br225599) – оба параметра указывают, что приложение не имеет разрешения на доступ к информации о расположении пользователя.

```csharp
switch (accessStatus)
{
    case GeolocationAccessStatus.Allowed:
        geofences = GeofenceMonitor.Current.Geofences;

        FillRegisteredGeofenceListBoxWithExistingGeofences();
        FillEventListBoxWithExistingEvents();

        // Register for state change events.
        GeofenceMonitor.Current.GeofenceStateChanged += OnGeofenceStateChanged;
        GeofenceMonitor.Current.StatusChanged += OnGeofenceStatusChanged;
        break;

    case GeolocationAccessStatus.Denied:
        _rootPage.NotifyUser("Access denied.", NotifyType.ErrorMessage);
        break;

    case GeolocationAccessStatus.Unspecified:
        _rootPage.NotifyUser("Unspecified error.", NotifyType.ErrorMessage);
        break;
}
```

Затем при переходе из приложения переднего плана отмените регистрацию прослушивателей событий.

```csharp
protected override void OnNavigatingFrom(NavigatingCancelEventArgs e)
{
    GeofenceMonitor.Current.GeofenceStateChanged -= OnGeofenceStateChanged;
    GeofenceMonitor.Current.StatusChanged -= OnGeofenceStatusChanged;

    base.OnNavigatingFrom(e);
}
```

### Шаг 3. Создание геозоны

Теперь вы можете приступить к определению и настройке объекта [**Geofence**](https://msdn.microsoft.com/library/windows/apps/dn263587). В зависимости от ваших потребностей на выбор предлагаются несколько различных перегрузок конструктора. В наиболее простом конструкторе геозоны укажите только [**Id**](https://msdn.microsoft.com/library/windows/apps/dn263724) и [**Geoshape**](https://msdn.microsoft.com/library/windows/apps/dn263718), как показано здесь.

```csharp
// Set the fence ID.
string fenceId = "fence1";

// Define the fence location and radius.
BasicGeoposition position;
position.Latitude = 47.6510;
position.Longitude = -122.3473;
position.Altitude = 0.0;
double radius = 10; // in meters

// Set a circular region for the geofence.
Geocircle geocircle = new Geocircle(position, radius);

// Create the geofence.
Geofence geofence = new Geofence(fenceId, geocircle);
```

Дальнейшие настройки геозоны можно выполнить с помощью других конструкторов. В следующем примере конструктор геозоны определяет следующие дополнительные параметры:

-   флаг [
              **MonitoredStates**
            ](https://msdn.microsoft.com/library/windows/apps/dn263728) указывает, уведомления о каких событиях геозоны вы хотите получать: появление в заданной области, уход из заданной области или удаление геозоны;
-   флаг [
              **SingleUse**
            ](https://msdn.microsoft.com/library/windows/apps/dn263732) удаляет геозону, как только она достигнет всех отслеживаемых состояний;
-   флаг [
              **DwellTime**
            ](https://msdn.microsoft.com/library/windows/apps/dn263703) указывает, сколько времени пользователь должен находиться внутри или вне заданной зоны до инициации событий входа-выхода;
-   флаг [
              **StartTime**
            ](https://msdn.microsoft.com/library/windows/apps/dn263735) указывает время начала слежения за геозоной;
-   флаг [
              **Duration**
            ](https://msdn.microsoft.com/library/windows/apps/dn263697) указывает период, в течение которого выполняется слежение за геозоной.

```csharp
// Set the fence ID.
string fenceId = "fence2";

// Define the fence location and radius.
BasicGeoposition position;
position.Latitude = 47.6510;
position.Longitude = -122.3473;
position.Altitude = 0.0;
double radius = 10; // in meters

// Set the circular region for geofence.
Geocircle geocircle = new Geocircle(position, radius);

// Remove the geofence after the first trigger.
bool singleUse = true;

// Set the monitored states.
MonitoredGeofenceStates monitoredStates = 
                MonitoredGeofenceStates.Entered | 
                MonitoredGeofenceStates.Exited | 
                MonitoredGeofenceStates.Removed;

// Set how long you need to be in geofence for the enter event to fire.
TimeSpan dwellTime = TimeSpan.FromMinutes(5);

// Set how long the geofence should be active.
TimeSpan duration = TimeSpan.FromDays(1);

// Set up the start time of the geofence.
DateTimeOffset startTime = DateTime.Now;

// Create the geofence.
Geofence geofence = new Geofence(fenceId, geocircle, monitoredStates, singleUse, dwellTime, startTime, duration);
```

### Шаг 4. Обработка изменений в разрешениях, связанных с расположением

Объект [**GeofenceMonitor**](https://msdn.microsoft.com/library/windows/apps/dn263595) запускает событие [**StatusChanged**](https://msdn.microsoft.com/library/windows/apps/dn263646) для указания того, что расположение пользователя меняется. Это событие передает данные о соответствующем состоянии через свойство аргумента **sender.Status** (типа [**GeofenceMonitorStatus**](https://msdn.microsoft.com/library/windows/apps/dn263599)). Обратите внимание, что этот метод не вызывается из потока пользовательского интерфейса и объект [**Dispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211) фактически вызывает изменения пользовательского интерфейса.

```csharp
using Windows.UI.Core;
...
public async void OnGeofenceStatusChanged(GeofenceMonitor sender, object e)
{
   await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
   {
    // Show the location setting message only if the status is disabled.
    LocationDisabledMessage.Visibility = Visibility.Collapsed;

    switch (sender.Status)
    {
     case GeofenceMonitorStatus.Ready:
      _rootPage.NotifyUser("The monitor is ready and active.", NotifyType.StatusMessage);
      break;

     case GeofenceMonitorStatus.Initializing:
      _rootPage.NotifyUser("The monitor is in the process of initializing.", NotifyType.StatusMessage);
      break;

     case GeofenceMonitorStatus.NoData:
      _rootPage.NotifyUser("There is no data on the status of the monitor.", NotifyType.ErrorMessage);
      break;

     case GeofenceMonitorStatus.Disabled:
      _rootPage.NotifyUser("Access to location is denied.", NotifyType.ErrorMessage);

      // Show the message to the user to go to the location settings.
      LocationDisabledMessage.Visibility = Visibility.Visible;
      break;

     case GeofenceMonitorStatus.NotInitialized:
      _rootPage.NotifyUser("The geofence monitor has not been initialized.", NotifyType.StatusMessage);
      break;

     case GeofenceMonitorStatus.NotAvailable:
      _rootPage.NotifyUser("The geofence monitor is not available.", NotifyType.ErrorMessage);
      break;

     default:
      ScenarioOutput_Status.Text = "Unknown";
      _rootPage.NotifyUser(string.Empty, NotifyType.StatusMessage);
      break;
    }
   });
}
```

## Настройка уведомлений на переднем плане


После создания геозон потребуется добавить логику для обработки действий при возникновении события геозоны. В зависимости от настройки элемента [**MonitoredStates**](https://msdn.microsoft.com/library/windows/apps/dn263728) вы можете получить событие, если:

-   пользователь вошел в отслеживаемую область;
-   пользователь покинул отслеживаемую область;
-   срок действия геозоны истек или она была удалена. Обратите внимание, что для события удаления фоновое приложение не активируется.

Вы можете прослушивать события непосредственно из приложения, в котором работаете, или зарегистрировать фоновую задачу, чтобы получить фоновое уведомление при возникновении события.

### Шаг 1. Регистрация событий при изменении состояния геозоны

Чтобы приложение получало уведомление переднего плана об изменении состояния геозоны, необходимо зарегистрировать обработчик событий. Как правило, этот обработчик настраивается при создании геозоны.

```csharp
private void Initialize()
{
    // Other initialization logic

    GeofenceMonitor.Current.GeofenceStateChanged += OnGeofenceStateChanged;
}

```

### Шаг 2. Реализация обработчика событий геозоны

Следующий шаг — реализация обработчиков событий. Действия, выполняемые на этом этапе, зависят от целей использования геозоны в приложении.

```csharp
public async void OnGeofenceStateChanged(GeofenceMonitor sender, object e)
{
    var reports = sender.ReadReports();

    await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
    {
        foreach (GeofenceStateChangeReport report in reports)
        {
            GeofenceState state = report.NewState;

            Geofence geofence = report.Geofence;

            if (state == GeofenceState.Removed)
            {
                // Remove the geofence from the geofences collection.
                GeofenceMonitor.Current.Geofences.Remove(geofence);
            }
            else if (state == GeofenceState.Entered)
            {
                // Your app takes action based on the entered event.

                // NOTE: You might want to write your app to take a particular
                // action based on whether the app has internet connectivity.

            }
            else if (state == GeofenceState.Exited)
            {
                // Your app takes action based on the exited event.

                // NOTE: You might want to write your app to take a particular
                // action based on whether the app has internet connectivity.

            }
        }
    });
}



```

## Настройка фоновых уведомлений


После создания геозон потребуется добавить логику для обработки действий при возникновении события геозоны. В зависимости от настройки элемента [**MonitoredStates**](https://msdn.microsoft.com/library/windows/apps/dn263728) вы можете получить событие, если:

-   пользователь вошел в отслеживаемую область;
-   пользователь покинул отслеживаемую область;
-   срок действия геозоны истек или она была удалена. Обратите внимание, что для события удаления фоновое приложение не активируется.

Прослушивание событий геозоны в фоновом режиме

-   Объявите фоновую задачу в манифесте приложения.
-   Зарегистрируйте фоновую задачу в приложении. Если приложению требуется доступ к Интернету, например к облачной службе, вы можете при возникновении события установить для него флаг. Можно установить еще один флаг, чтобы убедиться в присутствии пользователя при инициации события. Таким образом вы будете уверены, что пользователь получил уведомление.
-   Так как приложение выполняется на переднем плане, предложите пользователю разрешить приложению доступ к информации о расположении.

### Шаг 1. Регистрация событий при изменении состояния геозоны

В манифесте приложения на вкладке **Объявления** добавьте объявление для фоновой задачи расположения. Для этого выполните следующие действия.

-   Добавьте объявление типа **Фоновые задачи**.
-   Присвойте задаче свойства тип **Расположение**.
-   Задайте точку входа в приложение для вызова при инициации события.

### Шаг 2. Регистрация фоновой задачи

Код, представленный в этом параграфе, регистрирует фоновую задачу настройки геозоны: Помните, что при создании геозоны мы проверили разрешения на доступ к информации о местоположении. Подробнее см. в разделе [Настройка геозоны](#setup).

```csharp
async private void RegisterBackgroundTask(object sender, RoutedEventArgs e)
{
    // Get permission for a background task from the user. If the user has already answered once,
    // this does nothing and the user must manually update their preference via PC Settings.
    BackgroundAccessStatus backgroundAccessStatus = await BackgroundExecutionManager.RequestAccessAsync();

    // Regardless of the answer, register the background task. Note that the user can use
    // the Settings app to prevent your app from running background tasks.
    // Create a new background task builder.
    BackgroundTaskBuilder geofenceTaskBuilder = new BackgroundTaskBuilder();

    geofenceTaskBuilder.Name = SampleBackgroundTaskName;
    geofenceTaskBuilder.TaskEntryPoint = SampleBackgroundTaskEntryPoint;

    // Create a new location trigger.
    var trigger = new LocationTrigger(LocationTriggerType.Geofence);

    // Associate the location trigger with the background task builder.
    geofenceTaskBuilder.SetTrigger(trigger);

    // If it is important that there is user presence and/or
    // internet connection when OnCompleted is called
    // the following could be called before calling Register().
    // SystemCondition condition = new SystemCondition(SystemConditionType.UserPresent | SystemConditionType.InternetAvailable);
    // geofenceTaskBuilder.AddCondition(condition);

    // Register the background task.
    geofenceTask = geofenceTaskBuilder.Register();

    // Associate an event handler with the new background task.
    geofenceTask.Completed += new BackgroundTaskCompletedEventHandler(OnCompleted);

    BackgroundTaskState.RegisterBackgroundTask(BackgroundTaskState.LocationTriggerBackgroundTaskName);

    switch (backgroundAccessStatus)
    {
    case BackgroundAccessStatus.Unspecified:
    case BackgroundAccessStatus.Denied:
        rootPage.NotifyUser("This app is not allowed to run in the background.", NotifyType.ErrorMessage);
        break;

    }
}


```

### Шаг 3. Обработка фонового уведомления

Действие, выполняемое для уведомления пользователя, зависит от возможностей приложения. Вы можете отобразить всплывающее уведомление, воспроизвести какой-нибудь звук или обновить живую плитку. Код, представленный в этом параграфе, служит для обработки уведомления.

```csharp
async private void OnCompleted(IBackgroundTaskRegistration sender, BackgroundTaskCompletedEventArgs e)
{
    if (sender != null)
    {
        // Update the UI with progress reported by the background task.
        await Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            try
            {
                // If the background task threw an exception, display the exception in
                // the error text box.
                e.CheckResult();

                // Update the UI with the completion status of the background task.
                // The Run method of the background task sets the LocalSettings. 
                var settings = ApplicationData.Current.LocalSettings;

                // Get the status.
                if (settings.Values.ContainsKey("Status"))
                {
                    rootPage.NotifyUser(settings.Values["Status"].ToString(), NotifyType.StatusMessage);
                }

                // Do your app work here.

            }
            catch (Exception ex)
            {
                // The background task had an error.
                rootPage.NotifyUser(ex.ToString(), NotifyType.ErrorMessage);
            }
        });
    }
}


```

## Изменение параметров конфиденциальности


Если параметры конфиденциальности данных о местоположении не позволяют вашему приложению получать доступ к данным о местоположении пользователя, рекомендуется реализовать удобную ссылку на **параметры конфиденциальности данных о местоположении** в приложении **Параметры**. В этом примере используется элемент управления «Гиперссылка» для перехода к URI `ms-settings:privacy-location`.

```xml
<!--Set Visibility to Visible when access to the user's location is denied. -->  
<TextBlock x:Name="LocationDisabledMessage" FontStyle="Italic" 
                 Visibility="Collapsed" Margin="0,15,0,0" TextWrapping="Wrap" >
          <Run Text="This app is not able to access Location. Go to " />
              <Hyperlink NavigateUri="ms-settings:privacy-location">
                  <Run Text="Settings" />
              </Hyperlink>
          <Run Text=" to check the location privacy settings."/>
</TextBlock>
```

Ваше приложение также может вызвать метод [**LaunchUriAsync**](https://msdn.microsoft.com/library/windows/apps/hh701476), чтобы запустить приложение **Параметры** из кода. Дополнительные сведения см. в разделе [Запуск приложения «Параметры» для Windows](https://msdn.microsoft.com/library/windows/apps/mt228342).

```csharp
using Windows.System;
...
bool result = await Launcher.LaunchUriAsync(new Uri("ms-settings:privacy-location"));
```

## Тестирование и отладка приложения


Тестирование и отладка приложений с функцией геозон могут вызывать трудности, поскольку такие приложения зависят от расположения устройства. В этом разделе приведен краткий обзор методов тестирования приложений с функцией геозон, работающих как на переднем плане, так и в фоновом режиме.

**Отладка приложения с функцией геозон**

1.  Физически переместите устройство в новое расположение.
2.  Протестируйте вход в геозону, создав регион геозоны, включающий ваше текущее физическое расположение. В этом случае вы уже будете находиться внутри геозоны и сразу произойдет событие "вход в геозону".
3.  Для моделирования расположений устройства используйте эмулятор Microsoft Visual Studio.

### Тестирование и отладка приложения с функцией геозон, которое работает на переднем плане

**Чтобы протестировать приложение с функцией геозон, которое работает на переднем плане, сделайте следующее.**

1.  Выполните сборку приложения в Visual Studio.
2.  Запустите приложение в эмуляторе Visual Studio.
3.  Используйте эти инструменты, чтобы моделировать различные расположения внутри и вне региона геозоны. Подождите достаточное время после момента, указанного в свойстве [**DwellTime**](https://msdn.microsoft.com/library/windows/apps/dn263703), пока не произойдет событие. Ответьте утвердительно на предложение разрешить доступ приложения к данным о расположении. Подробнее о моделировании местоположений см. в разделе [Установка смоделированного географического положения устройства](http://go.microsoft.com/fwlink/p/?LinkID=325245).
4.  Эмулятор также может использоваться для оценки размера зон и определения количества времени, примерно необходимого для их обнаружения с разной скоростью.

### Тестирование и отладка приложения с функцией геозон, которое работает в фоновом режиме

**Чтобы протестировать приложение с функцией геозон, которое работает в фоновом режиме, сделайте следующее.**

1.  Выполните сборку приложения в Visual Studio. Обратите внимание, что в вашем приложении должен быть задан тип фоновой задачи **расположение**.
2.  Сначала разверните приложение локально.
3.  Закройте свое локально работающее приложение.
4.  Запустите приложение в эмуляторе Visual Studio. Учтите, что моделирование геозон в фоновом режиме в эмуляторе возможно одновременно только для одного приложения. Не запускайте в эмуляторе сразу несколько приложений с функцией геозон.
5.  В эмуляторе смоделируйте различные расположения внутри и вне региона геозоны. Подождите достаточное время после момента, указанного в свойстве [**DwellTime**](https://msdn.microsoft.com/library/windows/apps/dn263703), пока не произойдет событие. Ответьте утвердительно на предложение разрешить доступ приложения к данным о расположении.
6.  Запустите фоновую задачу определения расположения в Visual Studio. Дополнительные сведения о запуске фоновых задач в Visual Studio см. в разделе [Активация фоновых задач](http://go.microsoft.com/fwlink/p/?LinkID=325378).

## Устранение неполадок приложения


Для того чтобы ваше приложение могло получить доступ к данным о расположении, необходимо включить на устройстве функцию **расположение**. Убедитесь, что в приложении **Параметры** включены следующие **параметры конфиденциальности данных о расположении**.

-   Параметр **Расположение для этого устройства...** **включен** (неприменимо в Windows 10 Mobile)
-   Параметр **Местоположение** служб определения местоположения **включен**
-   В разделе **Выберите приложения, которым будет разрешено использовать данные о вашем местоположении** для вашего приложения установлено значение **Вкл.**

## Ссылки по теме

* [Пример определения географического положения UWP](http://go.microsoft.com/fwlink/p/?linkid=533278)
* [Рекомендации для разработчиков относительно создания геозон](https://msdn.microsoft.com/library/windows/apps/dn631756)
* [Рекомендации по разработке приложений с определением местоположения](https://msdn.microsoft.com/library/windows/apps/hh465148)




<!--HONumber=May16_HO2-->


