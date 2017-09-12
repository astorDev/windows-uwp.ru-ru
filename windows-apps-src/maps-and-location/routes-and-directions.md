---
author: normesta
title: "Отображение маршрутов и направлений на карте"
description: "Запрос маршрутов и направлений и их отображение в приложении."
ms.assetid: BBB4C23A-8F10-41D1-81EA-271BE01AED81
ms.author: normesta
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Windows 10, uwp, маршрут, карта, расположение, направления"
ms.openlocfilehash: 83985d986f15602923a21db3d308931397a01767
ms.sourcegitcommit: 378382419f1fda4e4df76ffa9c8cea753d271e6a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/08/2017
---
# <a name="display-routes-and-directions-on-a-map"></a>Отображение маршрутов и направлений на карте


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Запрос маршрутов и направлений и их отображение в вашем приложении.

**Подсказка.** Чтобы получить дополнительные сведения об использовании карт в приложении, скачайте следующий пример из [репозитория Windows-universal-samples](http://go.microsoft.com/fwlink/p/?LinkId=619979) на веб-сайте GitHub.

-   [Пример карты универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619977)

**Подсказка.** Если работа с картами является второстепенной функцией приложения, то рекомендуется использовать приложение «Карты Windows». Вы можете использовать `bingmaps:`, `ms-drive-to:`и схемы URI `ms-walk-to:` для запуска приложения "Карты Windows" с определенными картами и маршрутами. Дополнительные сведения см. в разделе [Запуск приложения "Карты Windows"](https://msdn.microsoft.com/library/windows/apps/mt228341).

 

## <a name="an-intro-to-maproutefinder-results"></a>Общие сведения о результатах MapRouteFinder


Классы маршрутов и направлений связаны следующим образом.

-   Класс [**MapRouteFinder**](https://msdn.microsoft.com/library/windows/apps/dn636938) использует методы, позволяющие получать маршруты и направления.
-   Эти методы возвращают [**MapRouteFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn636939).
-   [**MapRouteFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn636939) содержит объект [**MapRoute**](https://msdn.microsoft.com/library/windows/apps/dn636937). Получите доступ к этому объекту через свойство [**Route**](https://msdn.microsoft.com/library/windows/apps/dn636940) класса **MapRouteFinderResult**.
-   [**MapRoute**](https://msdn.microsoft.com/library/windows/apps/dn636937) содержит коллекцию объектов [**MapRouteLeg**](https://msdn.microsoft.com/library/windows/apps/dn636955). Получите доступ к этой коллекции с помощью свойства [**Legs**](https://msdn.microsoft.com/library/windows/apps/dn636973) класса **MapRoute**.
-   Каждый [**MapRouteLeg**](https://msdn.microsoft.com/library/windows/apps/dn636955) содержит коллекцию объектов [**MapRouteManeuver**](https://msdn.microsoft.com/library/windows/apps/dn636961). Получите доступ к этой коллекции с помощью свойства [**Maneuvers**](https://msdn.microsoft.com/library/windows/apps/dn636959) класса **MapRouteLeg**.

## <a name="display-directions"></a>Отображение направлений


Получите маршрут и направления, чтобы добраться до места пешком или на машине, вызвав методы класса [**MapRouteFinder**](https://msdn.microsoft.com/library/windows/apps/dn636938), например [**GetDrivingRouteAsync**](https://msdn.microsoft.com/library/windows/apps/dn636943) или [**GetWalkingRouteAsync**](https://msdn.microsoft.com/library/windows/apps/dn636953). В объекте [**MapRouteFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn636939) содержится объект [**MapRoute**](https://msdn.microsoft.com/library/windows/apps/dn636937), доступ к которому можно получить с помощью свойства [**Route**](https://msdn.microsoft.com/library/windows/apps/dn636940).

При запросе маршрута можно указать следующее:

-   Можно указать только начальную и конечную точки или несколько точек для составления маршрута.
-   Можно задать оптимизацию (например, использовать кратчайший путь).
-   Можно указать ограничения (например, избегать магистрали).

Созданный класс [**MapRoute**](https://msdn.microsoft.com/library/windows/apps/dn636937) имеет свойства, показывающие время прохождения маршрута и его длину, а также содержит коллекцию объектов [**MapRouteLeg**](https://msdn.microsoft.com/library/windows/apps/dn636955), составляющих отрезки маршрута. Каждый объект **MapRouteLeg** содержит коллекцию объектов [**MapRouteManeuver**](https://msdn.microsoft.com/library/windows/apps/dn636961). В объекте **MapRouteManeuver** содержатся направления, доступ к которым можно получить с помощью свойства [**InstructionText**](https://msdn.microsoft.com/library/windows/apps/dn636964).

**Важно!** Прежде чем использовать службы карт, необходимо указать ключ проверки подлинности карт. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

 

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


Чтобы отобразить [**MapRoute**](https://msdn.microsoft.com/library/windows/apps/dn636937) в [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004), постройте [**MapRouteView**](https://msdn.microsoft.com/library/windows/apps/dn637122) с **MapRoute**. Затем добавьте **MapRouteView** в коллекцию [**Routes**](https://msdn.microsoft.com/library/windows/apps/dn637047) элемента **MapControl**.

**Важно!** Прежде чем использовать службы карт или элемент управления картой, необходимо указать ключ проверки подлинности карт. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

 

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

В этом примере показан следующий элемент управления [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004) с именем **MapWithRoute**.

![map control with route displayed.](images/routeonmap.png)

## <a name="related-topics"></a>Связанные разделы

* [Центр разработки Карт Bing](https://www.bingmapsportal.com/)
* [Пример карты UWP](http://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Руководство по разработке карт](https://msdn.microsoft.com/library/windows/apps/dn596102)
* [Видео c конференции Build 2015: использование карт и местоположений в приложениях для Windows на телефонах, планшетах и ПК](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Пример приложения UWP для работы с картами](http://go.microsoft.com/fwlink/p/?LinkId=619982)
