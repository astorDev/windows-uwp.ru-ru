---
author: PatrickFarley
title: "Выполнение геокодирования и обратного геокодирования"
description: "Вы можете преобразовывать адреса в географические расположения (выполнять геокодирование) и наоборот (выполнять обратное геокодирование), вызывая методы класса MapLocationFinder в пространстве имен Windows.Services.Maps."
ms.assetid: B912BE80-3E1D-43BB-918F-7A43327597D2
ms.author: pafarley
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, геокодирование, карта, расположение"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 16be7bcafaf286a71e79fb4bca01511ddc7a1ae0
ms.lasthandoff: 02/07/2017

---

# <a name="perform-geocoding-and-reverse-geocoding"></a>Выполнение геокодирования и обратного геокодирования


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Вы можете преобразовывать адреса в географические положения (выполнять геокодирование) и географические положения — в адреса (выполнять обратное геокодирование), вызывая методы класса [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550) в пространстве имен [**Windows.Services.Maps**](https://msdn.microsoft.com/library/windows/apps/dn636979).

**Совет.** Чтобы получить дополнительные сведения об использовании карт в приложении, скачайте следующий пример из [репозитория Windows-universal-samples](http://go.microsoft.com/fwlink/p/?LinkId=619979) на веб-сайте GitHub.

-   [Пример карты универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619977)

Ниже показано, как связаны классы для геокодирования и обратного геокодирования.

-   Класс [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550) использует методы, выполняющие прямое ([**FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925)) и обратное геокодирование ([**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928)).
-   Эти методы возвращают класс [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551).
-   Класс [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551) содержит коллекцию объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549). Чтобы получить доступ к этой коллекции, воспользуйтесь свойством [**Locations**](https://msdn.microsoft.com/library/windows/apps/dn627552) класса **MapLocationFinderResult**.
-   Каждый объект [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549) содержит объект [**MapAddress**](https://msdn.microsoft.com/library/windows/apps/dn627533). Получить доступ к этому объекту можно через свойство [**Address**](https://msdn.microsoft.com/library/windows/apps/dn636929) любого объекта **MapLocation**.

**Важно!** Прежде чем использовать службы карт, необходимо указать ключ проверки подлинности карт. Дополнительные сведения см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

 

## <a name="get-a-location-geocode"></a>Получение сведений о местоположении (геокодирование)


Вы можете преобразовать адрес или название места в географическое положение (выполнить геокодирование), последовательно выполнив следующие действия.

1.  Вызовите одну из перегрузок метода [**FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925) класса [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550).
2.  Метод [**FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925) возвращает объект [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551), который содержит коллекцию подходящих объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549).
3.  Чтобы получить доступ к этой коллекции, воспользуйтесь свойством [**Locations**](https://msdn.microsoft.com/library/windows/apps/dn627552) класса [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551).

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


Вы можете преобразовать географическое положение в адрес (выполнить обратное геокодирование), последовательно выполнив следующие действия.

1.  Вызовите метод [**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928) класса [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550).
2.  Метод [**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928) возвращает объект [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551), который содержит коллекцию подходящих объектов [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549).
3.  Чтобы получить доступ к этой коллекции, воспользуйтесь свойством [**Locations**](https://msdn.microsoft.com/library/windows/apps/dn627552) класса [**MapLocationFinderResult**](https://msdn.microsoft.com/library/windows/apps/dn627551).
4.  Получить доступ к объекту [**MapAddress**](https://msdn.microsoft.com/library/windows/apps/dn627533) можно через свойство [**Address**](https://msdn.microsoft.com/library/windows/apps/dn636929) любого объекта [**MapLocation**](https://msdn.microsoft.com/library/windows/apps/dn627549).

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

* [Центр разработки Карт Bing](https://www.bingmapsportal.com/)
* [Пример карты UWP](http://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Руководство по разработке карт](https://msdn.microsoft.com/library/windows/apps/dn596102)
* [Видео c конференции Build 2015: использование карт и местоположений в приложениях для Windows на телефонах, планшетах и ПК](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Пример приложения UWP для работы с картами](http://go.microsoft.com/fwlink/p/?LinkId=619982)
* [**MapLocationFinder**](https://msdn.microsoft.com/library/windows/apps/dn627550)
* [**FindLocationsAsync**](https://msdn.microsoft.com/library/windows/apps/dn636925)
* [**FindLocationsAtAsync**](https://msdn.microsoft.com/library/windows/apps/dn636928)

