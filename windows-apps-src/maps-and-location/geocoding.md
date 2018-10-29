---
author: PatrickFarley
title: Выполнение геокодирования и обратного геокодирования
description: В этом руководстве показано, как преобразовывать адреса в географические расположения (геокодирование) и географические в адреса (обратное геокодирование), вызывая методы класса MapLocationFinder в пространстве имен Windows.Services.Maps.
ms.assetid: B912BE80-3E1D-43BB-918F-7A43327597D2
ms.author: pafarley
ms.date: 07/02/2018
ms.topic: article
keywords: windows 10, uwp, геокодирование, карта, расположение
ms.localizationpriority: medium
ms.openlocfilehash: bdd956dece4435ceb8e14121ec2b545095af3a11
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5768854"
---
# <a name="perform-geocoding-and-reverse-geocoding"></a>Выполнение геокодирования и обратного геокодирования

В этом руководстве показано, как преобразовывать адреса в географические расположения (геокодирование) и географические в адреса (обратное геокодирование), вызывая методы класса [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550) в [**Windows.Services.Maps **](https://msdn.microsoft.com/library/windows/apps/dn636979)пространства имен.

> [!TIP]
> Чтобы узнать больше об использовании карт в приложении, скачайте пример [MapControl](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl) в [репозитории универсальных примеров Windows](hhttps://github.com/Microsoft/Windows-universal-samples) на GitHub.

Классы, использованные для геокодирования и обратного геокодирования с точки зрения следующим образом.

-   [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550) класс содержит методы, которые обрабатывают геокодирование ([**FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925)) и обратное геокодирование ([**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928)).
-   Оба эти методы возвращают экземпляра [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551) .
-   Свойство [**расположения**](https://msdn.microsoft.com/library/windows/apps/dn627552) [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551) предоставляет коллекцию объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549) . 
-   [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549) объекты имеют свойства [**адреса**](https://msdn.microsoft.com/library/windows/apps/dn636929) , который предоставляет [**MapAddress**](https://msdn.microsoft.com/library/windows/apps/dn627533) объект, представляющий является почтовым адресом и свойство [**точки**](https://docs.microsoft.com/uwp/api/windows.services.maps.maplocation.point) , которое предоставляет объект [**Geopoint**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geopoint) , представляющий географическое расположение.

> [!IMPORTANT]
> Прежде чем использовать службы карт, необходимо указать ключ проверки подлинности карт. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

## <a name="get-a-location-geocode"></a>Получение сведений о местоположении (геокодирование)

В этом разделе показано, как преобразовать почтовый адрес или название места в географическое положение (выполнить геокодирование).

1.  Вызовите одну из перегрузок метода [**FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925) класса [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550) с название места или почтовый адрес.
2.  Метод [**FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925) возвращает объект [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551) .
3.  Свойство [**расположения**](https://msdn.microsoft.com/library/windows/apps/dn627552) [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551) для предоставления объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549) коллекции. Так как система может оказаться разных расположений, которые соответствуют данного ввода может быть несколько объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549) .

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

В этом разделе показано, как преобразовать географическое положение в адреса (обратное геокодирование).

1.  Вызовите метод [**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928) класса [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550).
2.  Метод [**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928) возвращает объект [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551), который содержит коллекцию подходящих объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549).
3.  Свойство [**расположения**](https://msdn.microsoft.com/library/windows/apps/dn627552) [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551) для предоставления объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549) коллекции. Так как система может оказаться разных расположений, которые соответствуют данного ввода может быть несколько объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549) .
4.  Доступ к объектам [**MapAddress**](https://msdn.microsoft.com/library/windows/apps/dn627533) через свойство [**адрес**](https://msdn.microsoft.com/library/windows/apps/dn636929) каждого [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549).

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

## <a name="related-topics"></a>Связанные разделы

* [Пример карты UWP](http://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Пример приложения UWP для работы с картами](http://go.microsoft.com/fwlink/p/?LinkId=619982)
* [Руководство по разработке карт](https://msdn.microsoft.com/library/windows/apps/dn596102)
* [Видео: Использование карт и расположения на телефонах, планшетов и ПК в приложениях Windows](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Центр разработки Карт Bing](https://www.bingmapsportal.com/)
* [Класс **MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550)
* [Метод **FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925)
* [Метод **FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928)
