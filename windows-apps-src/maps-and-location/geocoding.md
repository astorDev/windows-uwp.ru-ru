---
title: Выполнение геокодирования и обратного геокодирования
description: В этом руководстве показано, как для преобразования адресов в географические расположения (геокодирования) и преобразования географических расположениях, в адресах улиц (обратного геокодирования) путем вызова методов класса MapLocationFinder в пространстве имен Windows.Services.Maps.
ms.assetid: B912BE80-3E1D-43BB-918F-7A43327597D2
ms.date: 07/02/2018
ms.topic: article
keywords: windows 10, uwp, геокодирование, карта, расположение
ms.localizationpriority: medium
ms.openlocfilehash: 88687f6e7074ff7c914927a81a08720336b3c60c
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371876"
---
# <a name="perform-geocoding-and-reverse-geocoding"></a>Выполнение геокодирования и обратного геокодирования

В этом руководстве показано, как преобразовать улиц географических расположениях (геокодирования) и преобразовать географических расположений в адресах улиц (обратного геокодирования), вызывая методы класса [ **MapLocationFinder** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) в класс [ **Windows.Services.Maps** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) пространства имен.

> [!TIP]
> Дополнительные сведения об использовании карт в вашем приложении, загрузите [MapControl](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl) образец из [Windows универсальной примеры репозитория](h https://github.com/Microsoft/Windows-universal-samples) на сайте GitHub.

Классы, участвующие в геокодирование и обратного геокодирования упорядочены следующим образом.

-   [ **MapLocationFinder** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) класс содержит методы, обрабатывающие геокодирования ([**FindLocationsAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync)) и обратное геокодирование ([ **FindLocationsAtAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync)).
-   Оба эти методы возвращают [ **MapLocationFinderResult** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) экземпляра.
-   [ **Расположения** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) свойство [ **MapLocationFinderResult** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) предоставляет доступ к коллекции [  **MapLocation** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) объектов. 
-   [**MapLocation** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) объекты имеют оба [ **адрес** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.address) свойство, которое предоставляет [ **MapAddress** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapAddress) Объект, представляющий адрес улицы и [ **точки** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.point) свойство, которое предоставляет [ **Geopoint** ](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geopoint) объекта представляющий географическое расположение.

> [!IMPORTANT]
> Перед тем как использовать карты служб необходимо указать ключ проверки подлинности карты. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

## <a name="get-a-location-geocode"></a>Получение сведений о расположении (геокодирование)

В этом разделе показано, как преобразовать почтовый адрес или название места географическое расположение (геокодирования).

1.  Вызовите одну из перегрузок [ **FindLocationsAsync** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync) метод [ **MapLocationFinder** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) класс с именем месте или улицы адрес.
2.  [ **FindLocationsAsync** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync) возвращает [ **MapLocationFinderResult** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) объекта.
3.  Используйте [ **расположения** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) свойство [ **MapLocationFinderResult** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) для предоставления коллекции [  **MapLocation** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) объектов. Может быть несколько [ **MapLocation** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) объекты, так как система может найти несколько расположений, которые соответствуют заданный входной.

```csharp
using Windows.Services.Maps;
using Windows.Devices.Geolocation;
...
private async void geocodeButton_Click(object sender, RoutedEventArgs e)
{
   // The address or business to geocode.
   string addressToGeocode = "Microsoft";

   // The nearby location to use as a query hint.
   BasicGeoposition queryHint = new BasicGeoposition();
   queryHint.Latitude = 47.643;
   queryHint.Longitude = -122.131;
   Geopoint hintPoint = new Geopoint(queryHint);

   // Geocode the specified address, using the specified reference point
   // as a query hint. Return no more than 3 results.
   MapLocationFinderResult result =
         await MapLocationFinder.FindLocationsAsync(
                           addressToGeocode,
                           hintPoint,
                           3);

   // If the query returns results, display the coordinates
   // of the first result.
   if (result.Status == MapLocationFinderStatus.Success)
   {
      tbOutputText.Text = "result = (" +
            result.Locations[0].Point.Position.Latitude.ToString() + "," +
            result.Locations[0].Point.Position.Longitude.ToString() + ")";
   }
}
```

Этот код отображает в текстовом поле `tbOutputText` указанные ниже результаты.

``` syntax
result = (47.6406099647284,-122.129339994863)
```

## <a name="get-an-address-reverse-geocode"></a>Получение адреса (обратное геокодирование)

В этом разделе показано, как преобразовать географического расположения в адрес (обратного геокодирования).

1.  Вызовите метод [**FindLocationsAtAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync) класса [**MapLocationFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder).
2.  Метод [**FindLocationsAtAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync) возвращает объект [**MapLocationFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult), который содержит коллекцию подходящих объектов [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation).
3.  Используйте [ **расположения** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) свойство [ **MapLocationFinderResult** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) для предоставления коллекции [  **MapLocation** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) объектов. Может быть несколько [ **MapLocation** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) объекты, так как система может найти несколько расположений, которые соответствуют заданный входной.
4.  Доступ [ **MapAddress** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapAddress) объектов через [ **адрес** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.address) свойства каждого [ **MapLocation** ](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation).

```csharp
using Windows.Services.Maps;
using Windows.Devices.Geolocation;
...
private async void reverseGeocodeButton_Click(object sender, RoutedEventArgs e)
{
   // The location to reverse geocode.
   BasicGeoposition location = new BasicGeoposition();
   location.Latitude = 47.643;
   location.Longitude = -122.131;
   Geopoint pointToReverseGeocode = new Geopoint(location);

   // Reverse geocode the specified geographic location.
   MapLocationFinderResult result =
         await MapLocationFinder.FindLocationsAtAsync(pointToReverseGeocode);

   // If the query returns results, display the name of the town
   // contained in the address of the first result.
   if (result.Status == MapLocationFinderStatus.Success)
   {
      tbOutputText.Text = "town = " +
            result.Locations[0].Address.Town;
   }
}
```

Этот код отображает в текстовом поле `tbOutputText` указанные ниже результаты.

``` syntax
town = Redmond
```

## <a name="related-topics"></a>См. также

* [Пример карты UWP](https://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Пример приложения UWP для работы с трафиком](https://go.microsoft.com/fwlink/p/?LinkId=619982)
* [Руководство по разработке карт](https://docs.microsoft.com/windows/uwp/maps-and-location/controls-map)
* [Видео: использование карт и расположений в приложениях для Windows на телефонах, планшетах и компьютерах](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Bing Maps Developer Center](https://www.bingmapsportal.com/)
* [**MapLocationFinder** класса](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder)
* [**FindLocationsAsync** метод](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync)
* [**FindLocationsAtAsync** метод](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync)
