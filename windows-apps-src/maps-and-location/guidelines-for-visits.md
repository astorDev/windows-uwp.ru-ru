---
author: PatrickFarley
Description: Learn how to use the powerful Visits Tracking feature for more practical location tracking.
title: Рекомендации по использованию функции отслеживания посещений
ms.assetid: 0c101684-48a9-4592-9ed5-6c20f3b830f2
ms.author: pafarley
ms.date: 05/18/2017
ms.topic: article
keywords: windows 10, uwp, карта, местоположение, geovisit, geovisits
ms.localizationpriority: medium
ms.openlocfilehash: 3a78b2689a10dff50696e5e65cc44f79a6f1ea7d
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "7152429"
---
# <a name="guidelines-for-using-visits-tracking"></a>Рекомендации по использованию функции отслеживания посещений

Функция посещений упрощает процесс отслеживания местоположения, чтобы повысить его эффективность при фактическом использовании многих приложений. Посещение определяется как значительная географическая область, в которую пользователь входит и из которой выходит. Посещения похожи на [геозоны](guidelines-for-geofencing.md), так как они позволяют отправлять приложению уведомление, только если пользователь входит в определенные целевые области или покидает их, что устраняет необходимость постоянного отслеживания местоположения и, как следствие, замедляет снижение уровня заряда. Тем не менее, в отличие от геозон, области посещения динамически определяются на уровне платформы и не требуют явного определения отдельными приложениями. Кроме того, выбор посещений для отслеживания приложением регулируется одним параметром детализации, а не подпиской на отдельные места.

## <a name="preliminary-setup"></a>Предварительная настройка

Прежде чем продолжить, убедитесь, что ваше приложение может получить доступ к местоположению устройства. Необходимо объявить возможность `Location` в манифесте и вызвать метод **[Geolocator.RequestAccessAsync](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator.RequestAccessAsync)**, чтобы пользователи предоставили приложению разрешение на отслеживание местоположения. Дополнительные сведения о том, как это сделать, см. в разделе [Получение местоположения пользователя](get-location.md). 

Обязательно добавьте пространство имен `Geolocation` в ваш класс. Это потребуется для работы всех фрагментов кода в этом руководстве.

```csharp
using Windows.Devices.Geolocation;
```

## <a name="check-the-latest-visit"></a>Проверка последнего посещения
Самый простой способ использования функции отслеживания посещений заключается в получении последнего известного изменения состояния, связанного с посещением. Изменение состояния— это событие, заносимое в журнал платформой, которое происходит в трех случаях: пользователь входит в значимое местоположение или покидает его, пользователь перемещается на существенное расстояние с момента последнего отчета, либо местоположение пользователя теряется (см. перечисление **[VisitStateChange](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.visitstatechange)**). Изменения состояния представлены экземплярами **[Geovisit](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geovisit)**. Чтобы получить экземпляр **Geovisit** для последнего зарегистрированного изменения состояния, используйте указанный метод в классе **[GeovisitMonitor](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geovisitmonitor)**.

> [!NOTE]
> Проверка последнего занесенного в журнал посещения не гарантирует, что система отслеживает посещения. Для отслеживания посещений в режиме реального времени необходимо выполнять их мониторинг на переднем плане или зарегистрироваться для фонового отслеживания (см. разделы ниже).

```csharp
private async void GetLatestStateChange() {
    // retrieve the Geovisit instance
    Geovisit latestVisit = await GeovisitMonitor.GetLastReportAsync();

    // Using the properties of "latestVisit", parse out the time that the state 
    // change was recorded, the device's location when the change was recorded,
    // and the type of state change.
}
```

### <a name="parse-a-geovisit-instance-optional"></a>Анализ экземпляра Geovisit (необязательно)
Следующий метод преобразовывает все данные, хранящиеся в экземпляре **Geovisit**, в удобную для чтения строку. Его можно использовать в любом из сценариев в этом руководстве, чтобы упростить отправку отчетов по регистрируемым посещениям.

```csharp
private string ParseGeovisit(Geovisit visit){
    string visitString = null;

    // Use a DateTimeFormatter object to process the timestamp. The following
    // format configuration will give an intuitive representation of the date/time
    Windows.Globalization.DateTimeFormatting.DateTimeFormatter formatterLongTime;
    
    formatterLongTime = new Windows.Globalization.DateTimeFormatting.DateTimeFormatter(
        "{hour.integer}:{minute.integer(2)}:{second.integer(2)}", new[] { "en-US" }, "US", 
        Windows.Globalization.CalendarIdentifiers.Gregorian, 
        Windows.Globalization.ClockIdentifiers.TwentyFourHour);
    
    // use this formatter to convert the timestamp to a string, and add to "visitString"
    visitString = formatterLongTime.Format(visit.Timestamp);

    // Next, add on the state change type value
    visitString += " " + visit.StateChange.ToString();

    // Next, add the position information (if any is provided; this will be null if 
    // the reported event was "TrackingLost")
    if (visit.Position != null) {
        visitString += " (" +
        visit.Position.Coordinate.Point.Position.Latitude.ToString() + "," +
        visit.Position.Coordinate.Point.Position.Longitude.ToString() + 
        ")";
    }

    return visitString;
}
```

## <a name="monitor-visits-in-the-foreground"></a>Мониторинг посещений на переднем плане

Класс **GeovisitMonitor**, используемый в предыдущем разделе, отвечает также за обработку сценария прослушивания изменений состояния за определенный период времени. Для этого необходимо создать экземпляр данного класса, зарегистрировать метод обработчика этого события и вызвать метод `Start`.

```csharp
// this GeovisitMonitor instance will belong to the class scope
GeovisitMonitor monitor;

public void RegisterForVisits() {

    // Create and initialize a new monitor instance.
    monitor = new GeovisitMonitor();
    
    // Attach a handler to receive state change notifications.
    monitor.VisitStateChanged += OnVisitStateChanged;
    
    // Calling the start method will start Visits tracking for a specified scope:
    // For higher granularity such as venue/building level changes, choose Venue.
    // For lower granularity in the range of zipcode level changes, choose City.
    monitor.Start(VisitMonitoringScope.Venue);
}
```

В этом примере метод `OnVisitStateChanged` будет обрабатывать входящие отчеты о посещениях. Соответствующий экземпляр **Geovisit** передается через параметр события.

```csharp
private void OnVisitStateChanged(GeoVisitWatcher sender, GeoVisitStateChangedEventArgs args) {
    Geovisit visit = args.Visit;
    
    // Using the properties of "visit", parse out the time that the state 
    // change was recorded, the device's location when the change was recorded,
    // and the type of state change.
}
```
Когда приложение завершит мониторинг изменений состояния, связанных с посещениями, оно должно остановить работу монитора и отменить регистрацию обработчиков событий. Это также необходимо сделать, если приложение приостановлено или закрыто.

```csharp
public void UnregisterFromVisits() {
    
    // Stop the monitor to stop tracking Visits. Otherwise, tracking will
    // continue until the monitor instance is destroyed.
    monitor.Stop();
    
    // Remove the handler to stop receiving state change events.
    monitor.VisitStateChanged -= OnVisitStateChanged;
}
```

## <a name="monitor-visits-in-the-background"></a>Мониторинг посещений в фоновом режиме

Также можно реализовать мониторинг посещений в фоновой задаче, чтобы действия, связанные с посещениями, обрабатывались на устройстве, даже если приложение закрыто. Это рекомендуемый метод, так как он более универсален и требует меньше энергии. 

В этом руководстве будет использоваться модель из раздела [Создание и регистрация внепроцессной фоновой задачи](https://docs.microsoft.com/windows/uwp/launch-resume/create-and-register-a-background-task), согласно которой основные файлы приложения расположены в одном проекте, а файл фоновой задачи— в отдельном проекте в том же решении. Если вы не знакомы с реализацией фоновых задач, рекомендуется в целом следовать указанному руководству и делать необходимые замены ниже, чтобы создать фоновую задачу для обработки посещений.

> [!NOTE]
> В следующих фрагментах кода некоторые важные возможности, такие как обработка ошибок и локальное хранилище, отсутствуют для большего удобства. Действующую реализацию обработки посещений в фоновом режиме см. в [примере приложения](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Geolocation).


Прежде всего, убедитесь, что в вашем приложении объявлены разрешения на выполнение фоновых задач. В элементе `Application/Extensions` файла *Package.appxmanifest* добавьте следующее расширение (добавьте элемент `Extensions`, если он еще не существует).

```xml
<Extension Category="windows.backgroundTasks" EntryPoint="Tasks.VisitBackgroundTask">
    <BackgroundTasks>
        <Task Type="location" />
    </BackgroundTasks>
</Extension>
```

Затем вставьте следующий код в определение класса фоновой задачи. Метод `Run` этой фоновой задачи передаст состав триггера (который содержит сведения о посещениях) отдельному методу.

```csharp
using Windows.ApplicationModel.Background;

namespace Tasks {
    
    public sealed class VisitBackgroundTask : IBackgroundTask {
        
        public void Run(IBackgroundTaskInstance taskInstance) {
            
            // get a deferral
            BackgroundTaskDeferral deferral = taskInstance.GetDeferral();
            
            // this task's trigger will be a Geovisit trigger
            GeovisitTriggerDetails triggerDetails = taskInstance.TriggerDetails as GeovisitTriggerDetails;

            // Handle Visit reports
            GetVisitReports(triggerDetails);         

            finally {
                deferral.Complete();
            }
        }        
    }
}
```

Определите метод `GetVisitReports` в любом месте этого же класса.

```csharp
private void GetVisitReports(GeovisitTriggerDetails triggerDetails) {

    // Read reports from the triggerDetails. This populates the "reports" variable 
    // with all of the Geovisit instances that have been logged since the previous
    // report reading.
    IReadOnlyList<Geovisit> reports = triggerDetails.ReadReports();

    foreach (Geovisit report in reports) {
        // Using the properties of "visit", parse out the time that the state 
        // change was recorded, the device's location when the change was recorded,
        // and the type of state change.
    }

    // Note: depending on the intent of the app, you many wish to store the
    // reports in the app's local storage so they can be retrieved the next time 
    // the app is opened in the foreground.
}
```

Затем в основном проекте приложения необходимо выполнить регистрацию этой фоновой задачи. Создайте метод регистрации, который может быть вызван действием пользователя или вызывается при каждой активации класса.

```csharp
// a reference to this registration should be declared at the class level
private IBackgroundTaskRegistration visitTask = null;

// The app must call this method at some point to allow future use of 
// the background task. 
private async void RegisterBackgroundTask(object sender, RoutedEventArgs e) {
    
    string taskName = "MyVisitTask";
    string taskEntryPoint = "Tasks.VisitBackgroundTask";

    // First check whether the task in question is already registered
    foreach (var task in BackgroundTaskRegistration.AllTasks) {
        if (task.Value.Name == taskName) {
            // if a task is found with the name of this app's background task, then
            // return and do not attempt to register this task
            return;
        }
    }
    
    // Attempt to register the background task.
    try {
        // Get permission for a background task from the user. If the user has 
        // already responded once, this does nothing and the user must manually 
        // update their preference via Settings.
        BackgroundAccessStatus backgroundAccessStatus = await BackgroundExecutionManager.RequestAccessAsync();

        switch (backgroundAccessStatus) {
            case BackgroundAccessStatus.AlwaysAllowed:
            case BackgroundAccessStatus.AllowedSubjectToSystemPolicy:
                // BackgroundTask is allowed
                break;

            default:
                // notify user that background tasks are disabled for this app
                //...
                break;
        }

        // Create a new background task builder
        BackgroundTaskBuilder visitTaskBuilder = new BackgroundTaskBuilder();

        visitTaskBuilder.Name = exampleTaskName;
        visitTaskBuilder.TaskEntryPoint = taskEntryPoint;

        // Create a new Visit trigger
        var trigger = new GeovisitTrigger();

        // Set the desired monitoring scope.
        // For higher granularity such as venue/building level changes, choose Venue.
        // For lower granularity in the range of zipcode level changes, choose City. 
        trigger.MonitoringScope = VisitMonitoringScope.Venue; 

        // Associate the trigger with the background task builder
        visitTaskBuilder.SetTrigger(trigger);

        // Register the background task
        visitTask = visitTaskBuilder.Register();      
    }
    catch (Exception ex) {
        // notify user that the task failed to register, using ex.ToString()
    }
}
```

Таким образом, класс фоновой задачи с именем `VisitBackgroundTask` в пространстве имен `Tasks` выполнит определенное действие с типом триггера `location`. 

После этого ваше приложение сможет регистрировать фоновую задачу обработки посещений, а эта задача будет активироваться каждый раз, когда устройство заносит в журнал изменение состояния, связанного с посещением. Потребуется заполнить логику в классе фоновой задачи, чтобы определить, что делать с этими сведениями об изменении состояния.

## <a name="related-topics"></a>Статьи по теме
* [Создание и регистрация внепроцессной фоновой задачи](https://docs.microsoft.com/windows/uwp/launch-resume/create-and-register-a-background-task)
* [Получение местоположения пользователя](get-location.md)
* [Пространство имен Windows.Devices.Geolocation](https://docs.microsoft.com/uwp/api/windows.devices.geolocation)