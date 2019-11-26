---
title: Выполнение геокодирования и обратного геокодирования
description: В этом руководство показано, как преобразовать адреса улицы в географические расположения (геокодирование) и преобразовать географические расположения в адреса улицы (обратная кодировка), вызвав методы класса Маплокатионфиндер в пространстве имен Windows. Services. Maps.
ms.assetid: B912BE80-3E1D-43BB-918F-7A43327597D2
ms.date: 07/02/2018
ms.topic: article
keywords: windows 10, uwp, геокодирование, карта, расположение
ms.localizationpriority: medium
ms.openlocfilehash: 5d7e1dda355cf87a2c8e26c11327cfff32e9d0b5
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259366"
---
# <a name="perform-geocoding-and-reverse-geocoding"></a>Выполнение геокодирования и обратного геокодирования

В этом руководство показано, как преобразовать адреса улицы в географические расположения (геокодирование) и преобразовать географические расположения в адреса улицы (обратная кодировка), вызвав методы класса [**маплокатионфиндер**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) в пространстве имен [**Windows. Services. Maps**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps) .

> [!TIP]
> Чтобы узнать больше об использовании карт в приложении, Скачайте пример [MapControl](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl) из [репозитория по универсальным образцам Windows](h https://github.com/Microsoft/Windows-universal-samples) на сайте GitHub.

Классы, вовлеченные в геокодирование и обратную геокодирование, организованы следующим образом.

-   Класс [**маплокатионфиндер**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) содержит методы, обрабатывающие геокодирование ([**финдлокатионсасинк**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync)) и обратную геокодирование ([**финдлокатионсатасинк**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync)).
-   Эти методы возвращают экземпляр [**маплокатионфиндерресулт**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) .
-   Свойство [**Locations**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) объекта [**маплокатионфиндерресулт**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) предоставляет коллекцию объектов [**маплокатион**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) . 
-   Объекты [**маплокатион**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) имеют оба свойства [**Address**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.address) , которые предоставляют объект [**мападдресс**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapAddress) , представляющий адрес улицы, и свойство [**Point**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.point) , которое [**предоставляет объект**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geopoint) географического расположения.

> [!IMPORTANT]
> необходимо указать ключ проверки подлинности карты, прежде чем можно будет использовать службы сопоставления. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

## <a name="get-a-location-geocode"></a>Получение сведений о расположении (геокодирование)

В этом разделе показано, как преобразовать почтовый адрес или имя места в географическое расположение (геокодирование).

1.  Вызовите одну из перегрузок метода [**финдлокатионсасинк**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync) класса [**маплокатионфиндер**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder) с именем места или улицы.
2.  Метод [**финдлокатионсасинк**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync) возвращает объект [**маплокатионфиндерресулт**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) .
3.  Используйте свойство [**Locations**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) объекта [**маплокатионфиндерресулт**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) для предоставления объектов [**маплокатион**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) коллекции. Может существовать несколько объектов [**маплокатион**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) , так как система может найти несколько расположений, соответствующих заданным входам.

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

В этом разделе показано, как преобразовать географическое расположение в адрес (обратная геокодирование).

1.  Вызовите метод [**FindLocationsAtAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync) класса [**MapLocationFinder**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder).
2.  Метод [**FindLocationsAtAsync**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync) возвращает объект [**MapLocationFinderResult**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult), который содержит коллекцию подходящих объектов [**MapLocation**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation).
3.  Используйте свойство [**Locations**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinderresult.locations) объекта [**маплокатионфиндерресулт**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinderResult) для предоставления объектов [**маплокатион**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) коллекции. Может существовать несколько объектов [**маплокатион**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation) , так как система может найти несколько расположений, соответствующих заданным входам.
4.  Доступ к объектам [**мападдресс**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapAddress) осуществляется через свойство [**Address**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.address) каждого [**маплокатион**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocation).

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

* [Пример карты UWP](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)
* [Пример приложения UWP для работы с трафиком](https://github.com/Microsoft/Windows-appsample-trafficapp)
* [Руководство по разработке карт](https://docs.microsoft.com/windows/uwp/maps-and-location/controls-map)
* [Видео. Использование карт и расположения на телефоне, планшете и ПК в приложениях Windows](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Bing Maps Developer Center](https://www.bingmapsportal.com/)
* [Класс **маплокатионфиндер**](https://docs.microsoft.com/uwp/api/Windows.Services.Maps.MapLocationFinder)
* [Метод **финдлокатионсасинк**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsasync)
* [Метод **финдлокатионсатасинк**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocationfinder.findlocationsatasync)
