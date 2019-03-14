---
title: Выполнение геокодирования и обратного геокодирования
description: В этом руководстве показано, как для преобразования адресов в географические расположения (геокодирования) и преобразования географических расположениях, в адресах улиц (обратного геокодирования) путем вызова методов класса MapLocationFinder в пространстве имен Windows.Services.Maps.
ms.assetid: B912BE80-3E1D-43BB-918F-7A43327597D2
ms.date: 07/02/2018
ms.topic: article
keywords: windows 10, uwp, геокодирование, карта, расположение
ms.localizationpriority: medium
ms.openlocfilehash: a30ca89242b15866019fffc6972bdae7086f3f7e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57637629"
---
# <a name="perform-geocoding-and-reverse-geocoding"></a>Выполнение геокодирования и обратного геокодирования

В этом руководстве показано, как преобразовать улиц географических расположениях (геокодирования) и преобразовать географических расположений в адресах улиц (обратного геокодирования), вызывая методы класса [ **MapLocationFinder** ](https://msdn.microsoft.com/library/windows/apps/dn627550) в класс [ **Windows.Services.Maps** ](https://msdn.microsoft.com/library/windows/apps/dn636979) пространства имен.

> [!TIP]
> Дополнительные сведения об использовании карт в вашем приложении, загрузите [MapControl](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl) образец из [Windows универсальной примеры репозитория](h https://github.com/Microsoft/Windows-universal-samples) на сайте GitHub.

Классы, участвующие в геокодирование и обратного геокодирования упорядочены следующим образом.

-   [ **MapLocationFinder** ](https://msdn.microsoft.com/library/windows/apps/dn627550) класс содержит методы, обрабатывающие геокодирования ([**FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925)) и обратное геокодирование ([ **FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928)).
-   Оба эти методы возвращают [ **MapLocationFinderResult** ](https://msdn.microsoft.com/library/windows/apps/dn627551) экземпляра.
-   [ **Расположения** ](https://msdn.microsoft.com/library/windows/apps/dn627552) свойство [ **MapLocationFinderResult** ](https://msdn.microsoft.com/library/windows/apps/dn627551) предоставляет доступ к коллекции [  **MapLocation** ](https://msdn.microsoft.com/library/windows/apps/dn627549) объектов. 
-   [**MapLocation** ](https://msdn.microsoft.com/library/windows/apps/dn627549) объекты имеют оба [ **адрес** ](https://msdn.microsoft.com/library/windows/apps/dn636929) свойство, которое предоставляет [ **MapAddress** ](https://msdn.microsoft.com/library/windows/apps/dn627533) Объект, представляющий адрес улицы и [ **точки** ](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.point) свойство, которое предоставляет [ **Geopoint** ](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geopoint) объекта представляющий географическое расположение.

> [!IMPORTANT]
> Перед тем как использовать карты служб необходимо указать ключ проверки подлинности карты. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

## <a name="get-a-location-geocode"></a>Получение сведений о расположении (геокодирование)

В этом разделе показано, как преобразовать почтовый адрес или название места географическое расположение (геокодирования).

1.  Вызовите одну из перегрузок [ **FindLocationsAsync** ](https://msdn.microsoft.com/library/windows/apps/dn636925) метод [ **MapLocationFinder** ](https://msdn.microsoft.com/library/windows/apps/dn627550) класс с именем месте или улицы адрес.
2.  [ **FindLocationsAsync** ](https://msdn.microsoft.com/library/windows/apps/dn636925) возвращает [ **MapLocationFinderResult** ](https://msdn.microsoft.com/library/windows/apps/dn627551) объекта.
3.  Используйте [ **расположения** ](https://msdn.microsoft.com/library/windows/apps/dn627552) свойство [ **MapLocationFinderResult** ](https://msdn.microsoft.com/library/windows/apps/dn627551) для предоставления коллекции [  **MapLocation** ](https://msdn.microsoft.com/library/windows/apps/dn627549) объектов. Может быть несколько [ **MapLocation** ](https://msdn.microsoft.com/library/windows/apps/dn627549) объекты, так как система может найти несколько расположений, которые соответствуют заданный входной.

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

1.  Вызовите метод [**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928) класса [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550).
2.  Метод [**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928) возвращает объект [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551), который содержит коллекцию подходящих объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549).
3.  Используйте [ **расположения** ](https://msdn.microsoft.com/library/windows/apps/dn627552) свойство [ **MapLocationFinderResult** ](https://msdn.microsoft.com/library/windows/apps/dn627551) для предоставления коллекции [  **MapLocation** ](https://msdn.microsoft.com/library/windows/apps/dn627549) объектов. Может быть несколько [ **MapLocation** ](https://msdn.microsoft.com/library/windows/apps/dn627549) объекты, так как система может найти несколько расположений, которые соответствуют заданный входной.
4.  Доступ [ **MapAddress** ](https://msdn.microsoft.com/library/windows/apps/dn627533) объектов через [ **адрес** ](https://msdn.microsoft.com/library/windows/apps/dn636929) свойства каждого [ **MapLocation** ](https://msdn.microsoft.com/library/windows/apps/dn627549).

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

## <a name="related-topics"></a>Статьи по теме

* [Образец карты универсальной платформы Windows](https://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Пример приложения UWP трафика](https://go.microsoft.com/fwlink/p/?LinkId=619982)
* [Рекомендации по проектированию для карт](https://msdn.microsoft.com/library/windows/apps/dn596102)
* [Видео: Использование карты и расположение между телефоном, планшетом и ПК в приложениях Windows](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Центр разработчиков Bing Maps](https://www.bingmapsportal.com/)
* [**MapLocationFinder** класса](https://msdn.microsoft.com/library/windows/apps/dn627550)
* [**FindLocationsAsync** метод](https://msdn.microsoft.com/library/windows/apps/dn636925)
* [**FindLocationsAtAsync** метод](https://msdn.microsoft.com/library/windows/apps/dn636928)
