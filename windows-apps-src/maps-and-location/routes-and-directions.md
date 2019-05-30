---
title: Отображение маршрутов и направлений на карте
description: Запрос маршрутов и направлений и их отображение в вашем приложении.
ms.assetid: BBB4C23A-8F10-41D1-81EA-271BE01AED81
ms.date: 09/20/2017
ms.topic: article
keywords: Windows 10, uwp, маршрут, карта, расположение, направления
ms.localizationpriority: medium
ms.openlocfilehash: 196cb4801436e8094dae4ead363ff86cc746034e
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371694"
---
# <a name="display-routes-and-directions-on-a-map"></a>Отображение маршрутов и направлений на карте



Запрос маршрутов и направлений и их отображение в вашем приложении.

>[!Note]
>Чтобы получить дополнительные сведения об использовании карт в приложении, скачайте [пример карты универсальной платформы Windows (UWP)](https://go.microsoft.com/fwlink/p/?LinkId=619977).
>Если работа с картами является второстепенной функцией приложения, то рекомендуется использовать приложение "Карты Windows". Вы можете использовать `bingmaps:`, `ms-drive-to:`и схемы URI `ms-walk-to:` для запуска приложения «Карты Windows» с определенными картами и маршрутами. Дополнительные сведения см. в разделе [Запуск приложения «Карты Windows»](https://docs.microsoft.com/windows/uwp/launch-resume/launch-maps-app).

 
## <a name="an-intro-to-maproutefinder-results"></a>Общие сведения о результатах MapRouteFinder


Классы маршрутов и направлений связаны следующим образом.

* Класс [**MapRouteFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteFinder) использует методы, позволяющие получать маршруты и направления. Эти методы возвращают [**MapRouteFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteFinderResult).

* [  **MapRouteFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteFinderResult) содержит объект [**MapRoute**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRoute). Получите доступ к этому объекту через свойство [**Route**](https://docs.microsoft.com/uwp/api/windows.services.maps.maproutefinderresult.route) класса **MapRouteFinderResult**.

* [  **MapRoute**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRoute) содержит коллекцию объектов [**MapRouteLeg**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteLeg). Получите доступ к этой коллекции с помощью свойства [**Legs**](https://docs.microsoft.com/uwp/api/windows.services.maps.maproute.legs) класса **MapRoute**.

* Каждый [**MapRouteLeg**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteLeg) содержит коллекцию объектов [**MapRouteManeuver**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteManeuver). Получите доступ к этой коллекции с помощью свойства [**Maneuvers**](https://docs.microsoft.com/uwp/api/windows.services.maps.maprouteleg.maneuvers) класса **MapRouteLeg**.

Получите маршрут и направления, чтобы добраться до места пешком или на машине, вызвав методы класса [**MapRouteFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteFinder). Например, [**GetDrivingRouteAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maproutefinder.getdrivingrouteasync) или [**GetWalkingRouteAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maproutefinder.getwalkingrouteasync).

При запросе маршрута можно указать следующее:

* Можно указать только начальную и конечную точки или несколько точек для составления маршрута.

    Пункты *Stop* добавляют дополнительные отрезки маршрута, каждому из которых соответствует свой объект Itinerary. Для указания пунктов *stop* используйте любую из перегрузок [**GetDrivingRouteFromWaypointsAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maproutefinder.getwalkingroutefromwaypointsasync).

    Пункт *Via* определяет промежуточные точки между пунктами *stop*. Они не добавляют отрезки маршрута.  Они представляют собой простые точки, через которые должен пролегать маршрут. Для указания пунктов *via* используйте любую из перегрузок [**GetDrivingRouteFromEnhancedWaypointsAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maproutefinder.getdrivingroutefromenhancedwaypointsasync).

* Можно задать оптимизацию (например, использовать кратчайший путь).

* Можно указать ограничения (например, избегать магистрали).

## <a name="display-directions"></a>Отображение маршрутов

В объекте [**MapRouteFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteFinderResult) содержится объект [**MapRoute**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRoute), доступ к которому можно получить с помощью свойства [**Route**](https://docs.microsoft.com/uwp/api/windows.services.maps.maproutefinderresult.route).

Созданный класс [**MapRoute**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRoute) имеет свойства, показывающие время прохождения маршрута и его длину, а также содержит коллекцию объектов [**MapRouteLeg**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteLeg), составляющих отрезки маршрута. Каждый объект **MapRouteLeg** содержит коллекцию объектов [**MapRouteManeuver**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRouteManeuver). В объекте **MapRouteManeuver** содержатся направления, доступ к которым можно получить с помощью свойства [**InstructionText**](https://docs.microsoft.com/uwp/api/windows.services.maps.maproutemaneuver.instructiontext).

>[!IMPORTANT]
>Прежде чем использовать службы карт, необходимо указать ключ проверки подлинности карт. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

 

```csharp
using System;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.Services.Maps;
using Windows.Devices.Geolocation;
...
private async void button_Click(object sender, RoutedEventArgs e)
{
   // Start at Microsoft in Redmond, Washington.
   BasicGeoposition startLocation = new BasicGeoposition() {Latitude=47.643,Longitude=-122.131};

   // End at the city of Seattle, Washington.
   BasicGeoposition endLocation = new BasicGeoposition() {Latitude = 47.604,Longitude= -122.329};

   // Get the route between the points.
   MapRouteFinderResult routeResult =
         await MapRouteFinder.GetDrivingRouteAsync(
         new Geopoint(startLocation),
         new Geopoint(endLocation),
         MapRouteOptimization.Time,
         MapRouteRestrictions.None);

   if (routeResult.Status == MapRouteFinderStatus.Success)
   {
      System.Text.StringBuilder routeInfo = new System.Text.StringBuilder();

      // Display summary info about the route.
      routeInfo.Append("Total estimated time (minutes) = ");
      routeInfo.Append(routeResult.Route.EstimatedDuration.TotalMinutes.ToString());
      routeInfo.Append("\nTotal length (kilometers) = ");
      routeInfo.Append((routeResult.Route.LengthInMeters / 1000).ToString());

      // Display the directions.
      routeInfo.Append("\n\nDIRECTIONS\n");

      foreach (MapRouteLeg leg in routeResult.Route.Legs)
      {
         foreach (MapRouteManeuver maneuver in leg.Maneuvers)
         {
            routeInfo.AppendLine(maneuver.InstructionText);
         }
      }

      // Load the text box.
      tbOutputText.Text = routeInfo.ToString();
   }
   else
   {
      tbOutputText.Text =
            "A problem occurred: " + routeResult.Status.ToString();
   }
}
```

Этот пример отображает в текстовом поле `tbOutputText` указанные ниже результаты.

``` syntax
Total estimated time (minutes) = 18.4833333333333
Total length (kilometers) = 21.847

DIRECTIONS
Head north on 157th Ave NE.
Turn left onto 159th Ave NE.
Turn left onto NE 40th St.
Turn left onto WA-520 W.
Enter the freeway WA-520 from the right.
Keep left onto I-5 S/Portland.
Keep right and leave the freeway at exit 165A towards James St..
Turn right onto James St.
You have reached your destination.
```

## <a name="display-routes"></a>Отображение маршрутов


Чтобы отобразить [**MapRoute**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapRoute) в [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl), постройте [**MapRouteView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapRouteView) с **MapRoute**. Затем добавьте **MapRouteView** в коллекцию [**Routes**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.routes) элемента **MapControl**.

>[!IMPORTANT]
>Прежде чем использовать службы карт или элемент управления картой, необходимо указать ключ проверки подлинности карт. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

 

```csharp
using System;
using Windows.Devices.Geolocation;
using Windows.Services.Maps;
using Windows.UI;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Maps;
...
private async void ShowRouteOnMap()
{
   // Start at Microsoft in Redmond, Washington.
   BasicGeoposition startLocation = new BasicGeoposition() { Latitude = 47.643, Longitude = -122.131 };

   // End at the city of Seattle, Washington.
   BasicGeoposition endLocation = new BasicGeoposition() { Latitude = 47.604, Longitude = -122.329 };


   // Get the route between the points.
   MapRouteFinderResult routeResult =
         await MapRouteFinder.GetDrivingRouteAsync(
         new Geopoint(startLocation),
         new Geopoint(endLocation),
         MapRouteOptimization.Time,
         MapRouteRestrictions.None);

   if (routeResult.Status == MapRouteFinderStatus.Success)
   {
      // Use the route to initialize a MapRouteView.
      MapRouteView viewOfRoute = new MapRouteView(routeResult.Route);
      viewOfRoute.RouteColor = Colors.Yellow;
      viewOfRoute.OutlineColor = Colors.Black;

      // Add the new MapRouteView to the Routes collection
      // of the MapControl.
      MapWithRoute.Routes.Add(viewOfRoute);

      // Fit the MapControl to the route.
      await MapWithRoute.TrySetViewBoundsAsync(
            routeResult.Route.BoundingBox,
            null,
            Windows.UI.Xaml.Controls.Maps.MapAnimationKind.None);
   }
}
```

В этом примере показан следующий элемент управления [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) с именем **MapWithRoute**.

![Отображение карты с маршрутом](images/routeonmap.png)

Вот версия этого примера, в которой используется пункт *via* между двумя пунктами *stop*:

```csharp
using System;
using Windows.Devices.Geolocation;
using Windows.Services.Maps;
using Windows.UI;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Maps;
...
private async void ShowRouteOnMap()
{
  Geolocator locator = new Geolocator();
  locator.DesiredAccuracyInMeters = 1;
  locator.PositionChanged += Locator_PositionChanged;

  BasicGeoposition point1 = new BasicGeoposition() { Latitude = 47.649693, Longitude = -122.144908 };
  BasicGeoposition point2 = new BasicGeoposition() { Latitude = 47.6205, Longitude = -122.3493 };
  BasicGeoposition point3 = new BasicGeoposition() { Latitude = 48.649693, Longitude = -122.144908 };

  // Get Driving Route from point A  to point B thru point C
  var path = new List<EnhancedWaypoint>();

  path.Add(new EnhancedWaypoint(new Geopoint(point1), WaypointKind.Stop));
  path.Add(new EnhancedWaypoint(new Geopoint(point2), WaypointKind.Via));
  path.Add(new EnhancedWaypoint(new Geopoint(point3), WaypointKind.Stop));

  MapRouteFinderResult routeResult =  await MapRouteFinder.GetDrivingRouteFromEnhancedWaypointsAsync(path);

  if (routeResult.Status == MapRouteFinderStatus.Success)
  {
      MapRouteView viewOfRoute = new MapRouteView(routeResult.Route);
      viewOfRoute.RouteColor = Colors.Yellow;
      viewOfRoute.OutlineColor = Colors.Black;

      myMap.Routes.Add(viewOfRoute);

      await myMap.TrySetViewBoundsAsync(
            routeResult.Route.BoundingBox,
            null,
            Windows.UI.Xaml.Controls.Maps.MapAnimationKind.None);
  }
}
```

## <a name="related-topics"></a>См. также

* [Bing Maps Developer Center](https://www.bingmapsportal.com/)
* [Пример карты UWP](https://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Руководство по разработке карт](https://docs.microsoft.com/windows/uwp/maps-and-location/controls-map)
* [Видео с конференции Build 2015: использование карт и расположений в приложениях для Windows на телефонах, планшетах и компьютерах](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Пример приложения UWP для работы с трафиком](https://go.microsoft.com/fwlink/p/?LinkId=619982)
