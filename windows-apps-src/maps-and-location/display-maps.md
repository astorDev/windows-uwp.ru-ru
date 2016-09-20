---
author: msatranjr
title: "Отображение карт с помощью двумерных и трехмерных представлений, а также с помощью представлений Streetside"
description: "Отображайте настраиваемые карты в приложении, используя класс MapControl. В этой статье также рассказывается о трехмерных воздушных представлениях и представлениях Streetside."
ms.assetid: 3839E00B-2C1E-4627-A45F-6DDA98D7077F
translationtype: Human Translation
ms.sourcegitcommit: 92285ce32548bd6035c105e35c2b152432f8575a
ms.openlocfilehash: 5569722bdfc8dfb78d43dbe6abf23dcd06f619c0

---

# Отображение карт с помощью двумерных и трехмерных представлений, а также с помощью представлений Streetside


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Отображайте настраиваемые карты в приложении, используя класс [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004). Кроме того, в данной статье рассказывается о трехмерных воздушных представлениях и представлениях Streetside.

**Совет.** Чтобы получить дополнительные сведения об использовании карт в приложении, скачайте следующий пример из [репозитория Windows-universal-samples](http://go.microsoft.com/fwlink/p/?LinkId=619979) на веб-сайте GitHub.

-   [Пример карты универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619977)

## Добавление элемента управления картой в приложение


Карту можно отображать на странице XAML, добавив класс [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004). Для использования **MapControl** необходимо объявить пространство имен [**Windows.UI.Xaml.Controls.Maps**](https://msdn.microsoft.com/library/windows/apps/dn610751) на странице XAML или в коде. Если перетащить элемент управления с панели элементов, объявление пространства имен будет добавлено автоматически. Если вы добавляете **MapControl** на страницу XAML вручную, придется вручную добавить и объявление пространства имен в верхнюю часть страницы.

В примере ниже показано, как отобразить базовый элемент управления картой и настроить карту для отображения элементов управления масштабом и наклоном в дополнение к функции приема сенсорного ввода. Дополнительные сведения о настройке внешнего вида карты см. в статье [Настройка карты](#mapconfig).

```xml
<Page
    x:Class="MapsAndLocation1.DisplayMaps"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:MapsAndLocation1"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    xmlns:Maps="using:Windows.UI.Xaml.Controls.Maps"
    mc:Ignorable="d">

 <Grid x:Name="pageGrid" Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <Maps:MapControl
       x:Name="MapControl1"            
       ZoomInteractionMode="GestureAndControl"
       TiltInteractionMode="GestureAndControl"   
       MapServiceToken="EnterYourAuthenticationKeyHere"/>
  
 </Grid>
</Page>
```

Если вы добавляете элемент управления картой в код, придется вручную добавить и объявление пространства имен в верхнюю часть файла кода.

```csharp
using Windows.UI.Xaml.Controls.Maps;
...

// Add the MapControl and the specify maps authentication key.
MapControl MapControl2 = new MapControl();
MapControl2.ZoomInteractionMode = MapInteractionMode.GestureAndControl;
MapControl2.TiltInteractionMode = MapInteractionMode.GestureAndControl;
MapControl2.MapServiceToken = "EnterYourAuthenticationKeyHere";
pageGrid.Children.Add(MapControl2);
```

## Получение и установка ключа проверки подлинности карт


Прежде чем использовать класс [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004) и службы карт, необходимо в качестве значения свойства [**MapServiceToken**](https://msdn.microsoft.com/library/windows/apps/dn637036) указать ключ проверки подлинности карт. В предыдущих примерах замените код `EnterYourAuthenticationKeyHere` ключом, полученным из [Центра разработки Карт Bing](https://www.bingmapsportal.com/). Текст **Внимание! Не указан MapServiceToken** будет отображаться под элементом управления, до тех пор пока вы не укажете ключ проверки подлинности карт. Дополнительные сведения о получении и установке ключа проверки подлинности карт см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

## Установите начальное местоположение для карты


Укажите местоположение для отображения на карте, задав свойство [**Center**](https://msdn.microsoft.com/library/windows/apps/dn637005) класса [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004) в коде или путем привязки свойства в разметке XAML. В примере ниже показано, как отобразить карту, в центре которой расположен город Сиэтл.

**Совет.** Так как строку невозможно преобразовать в класс [**Geopoint**](https://msdn.microsoft.com/library/windows/apps/dn263675), то, соответственно, невозможно задать значение для свойства [**Center**](https://msdn.microsoft.com/library/windows/apps/dn637005) в разметке XAML, если вы не используете привязку данных. (Это ограничение также применяется к вложенному свойству [**MapControl.Location**](https://msdn.microsoft.com/library/windows/apps/dn653264).)

 

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
   // Specify a known location.
   BasicGeoposition cityPosition = new BasicGeoposition() { Latitude = 47.604, Longitude = -122.329 };
   Geopoint cityCenter = new Geopoint(cityPosition);

   // Set the map location.
   MapControl1.Center = cityCenter;
   MapControl1.ZoomLevel = 12;
   MapControl1.LandmarksVisible = true;
}
```

![Пример элемента управления картой.](images/displaymapsexample1.png)

## Настройте карту в соответствии с текущим местоположением


Перед тем как приложение сможет получить доступ к сведениям о местоположении пользователя, оно должно вызвать метод [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn859152). В этот момент ваше приложение должно находиться на переднем плане, а из потока пользовательского интерфейса должен быть вызван метод **RequestAccessAsync**. До тех пор пока пользователь не предоставит вашему приложению разрешение на доступ к данным о местоположении, приложение не сможет использовать такие данные.

С помощью метода [**GetGeopositionAsync**](https://msdn.microsoft.com/library/windows/apps/hh973536) класса [**Geolocator**](https://msdn.microsoft.com/library/windows/apps/br225534) получите текущее местоположение устройства (если доступна функция определения местоположения). Чтобы получить соответствующий класс [**Geopoint**](https://msdn.microsoft.com/library/windows/apps/dn263675), используйте свойство [**Point**](https://msdn.microsoft.com/library/windows/apps/dn263665) геокоординаты геоположения. Дополнительные сведения см. в статье [Получение сведений о текущем местоположении](get-location.md).

```csharp
// Set your current location.
var accessStatus = await Geolocator.RequestAccessAsync();
switch (accessStatus)
{
   case GeolocationAccessStatus.Allowed:

      // Get the current location.
      Geolocator geolocator = new Geolocator();
      Geoposition pos = await geolocator.GetGeopositionAsync();
      Geopoint myLocation = pos.Coordinate.Point;

      // Set the map location.
      MapControl1.Center = myLocation;
      MapControl1.ZoomLevel = 12;
      MapControl1.LandmarksVisible = true;
      break;

   case GeolocationAccessStatus.Denied:
      // Handle the case  if access to location is denied.
      break;

   case GeolocationAccessStatus.Unspecified:
      // Handle the case if  an unspecified error occurs.
      break;
}
```

При отображении местоположения устройства на карте рекомендуется отображать рисунки и задавать масштаб на основе точности данных о местоположении. Дополнительные сведения см. в статье [Руководство по приложениям с определением местонахождения](https://msdn.microsoft.com/library/windows/apps/hh465148).

## Изменение местоположения на карте


Чтобы изменить местоположение, отображаемое на двумерной карте, вызовите одну из перегрузок метода [**TrySetViewAsync**](https://msdn.microsoft.com/library/windows/apps/dn637060). Используйте этот метод для определения новых значений для [**Center**](https://msdn.microsoft.com/library/windows/apps/dn637005), [**ZoomLevel**](https://msdn.microsoft.com/library/windows/apps/dn637068), [**Heading**](https://msdn.microsoft.com/library/windows/apps/dn637019), и [**Pitch**](https://msdn.microsoft.com/library/windows/apps/dn637044). Кроме того, можно настроить использование дополнительной анимации при изменениях представления с помощью константы из перечисления [**MapAnimationKind**](https://msdn.microsoft.com/library/windows/apps/dn637002).

Чтобы изменить местоположение трехмерной карты, используйте метод [**TrySetSceneAsync**](https://msdn.microsoft.com/library/windows/apps/dn974296). Дополнительные сведения см. в статье [Отображение трехмерных представлений](#display3d).

Вызовите метод [**TrySetViewBoundsAsync**](https://msdn.microsoft.com/library/windows/apps/dn637065), чтобы отобразить содержимое [**GeoboundingBox**](https://msdn.microsoft.com/library/windows/apps/dn607949) на карте. Например, с помощью этого метода можно отобразить на карте маршрут или часть маршрута. Подробности см. в разделе [Отображение маршрутов и направлений на карте](routes-and-directions.md).

## Настройка карты


Чтобы настроить карту и ее внешний вид, установите значения следующих свойств объекта [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004).

**Параметры карты**

-   Установите географическую точку как **центр** карты, присвоив значение свойству [**Center**](https://msdn.microsoft.com/library/windows/apps/dn637005).
-   Установите **масштаб карты**, присвоив значение свойству [**ZoomLevel**](https://msdn.microsoft.com/library/windows/apps/dn637068) от 1 до 20.
-   Установите **вращение** карты, задав свойство [**Heading**](https://msdn.microsoft.com/library/windows/apps/dn637019), для которого 0 или 360 градусов обозначают север, 90— восток, 180— юг и 270— запад.
-   Установите **наклон** карты, присвоив значение свойству [**DesiredPitch**](https://msdn.microsoft.com/library/windows/apps/dn637012) от 0 до 65 градусов.

**Вид карты**

-   Укажите **тип** карты (например, карта дорог или гибридный вид), присвоив свойству [**Style**](https://msdn.microsoft.com/library/windows/apps/dn637051) одну из констант [**MapStyle**](https://msdn.microsoft.com/library/windows/apps/dn637127).
-   Вы можете задать светлую или темную **цветовую схему** карты, присвоив свойству [**ColorScheme**](https://msdn.microsoft.com/library/windows/apps/dn637010) значение одной из констант [**MapColorScheme**](https://msdn.microsoft.com/library/windows/apps/dn637003).

Чтобы отобразить информацию на карте, установите значения указанных ниже свойств объекта [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004).

-   Отобразите **здания и ориентиры** на карте, включив или отключив свойство [**LandmarksVisible**](https://msdn.microsoft.com/library/windows/apps/dn637023).
-   Чтобы показать на карте **объекты для пешеходов**, например надземные переходы, включите или отключите свойство [**PedestrianFeaturesVisible**](https://msdn.microsoft.com/library/windows/apps/dn637042).
-   Чтобы показать на карте **пробки**, включите или отключите свойство [**TrafficFlowVisible**](https://msdn.microsoft.com/library/windows/apps/dn637055).
-   Чтобы указать, необходимо ли отображать **водяной знак** на карте, присвойте свойству [**WatermarkMode**](https://msdn.microsoft.com/library/windows/apps/dn637066) значение одной из констант [**MapWatermarkMode**](https://msdn.microsoft.com/library/windows/apps/dn610749).
-   Чтобы показать на карте **маршруты для движения пешком или на автомобиле**, добавьте [**MapRouteView**](https://msdn.microsoft.com/library/windows/apps/dn637122) в коллекцию [**Routes**](https://msdn.microsoft.com/library/windows/apps/dn637047) элемента управления картой. Подробности и примеры см. в разделе [Отображение маршрутов и направлений на карте](routes-and-directions.md).

Сведения о том, как отображать вешки, фигуры и элементы управления XAML в объекте [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004), см. в статье [Отображение объектов на карте](display-poi.md).

## Отображение представлений Streetside


Представление Streetside— это перспектива уровня улицы для местоположения, отображаемая в верхней части элемента управления картой.

![Пример представления Streetside элемента управления картой.](images/onlystreetside-730width.png)

Рассмотрите возможность отделения взаимодействия «внутри» представления Streetside от карты, изначально отображаемой в элементе управления картой. Например, изменение расположения в представлении Streetside не изменяет расположение и внешний вид карты в представлении Streetside. После закрытия представления Streetside (с помощью значка **X** в правом верхнем углу элемента управления) исходная карта останется неизменной.

Чтобы отобразить представление Streetside, выполните указанные ниже действия.

1.  Проверьте свойство [**IsStreetsideSupported**](https://msdn.microsoft.com/library/windows/apps/dn974271), чтобы узнать, поддерживаются ли представления Streetside на устройстве.
2.  Если представление Streetside поддерживается, создайте класс [**StreetsidePanorama**](https://msdn.microsoft.com/library/windows/apps/dn974360) возле указанного местоположения, вызвав метод [**FindNearbyAsync**](https://msdn.microsoft.com/library/windows/apps/dn974361).
3.  Узнайте, обнаружена ли ближайшая панорама, проверив, имеет ли класс [**StreetsidePanorama**](https://msdn.microsoft.com/library/windows/apps/dn974360) значение null.
4.  Если ближайшая панорама обнаружена, создайте объект [**StreetsideExperience**](https://msdn.microsoft.com/library/windows/apps/dn974356) для свойства [**CustomExperience**](https://msdn.microsoft.com/library/windows/apps/dn974263) элемента управления картой.

В этом примере показано, как отображать представление Streetside, аналогичное предыдущему изображению.

**Примечание.** Если элемент управления картой имеет слишком маленький размер, карта обзора не будет отображаться.

 

```csharp
private async void showStreetsideView()
{
   // Check if Streetside is supported.
   if (MapControl1.IsStreetsideSupported)
   {
      // Find a panorama near Avenue Gustave Eiffel.
      BasicGeoposition cityPosition = new BasicGeoposition() { Latitude = 48.858, Longitude = 2.295};
      Geopoint cityCenter = new Geopoint(cityPosition);
      StreetsidePanorama panoramaNearCity = await StreetsidePanorama.FindNearbyAsync(cityCenter);

      // Set the Streetside view if a panorama exists.
      if (panoramaNearCity != null)
      {
         // Create the Streetside view.
         StreetsideExperience ssView = new StreetsideExperience(panoramaNearCity);
         ssView.OverviewMapVisible = true;
         MapControl1.CustomExperience = ssView;
      }
   }
   else
   {
      // If Streetside is not supported
      ContentDialog viewNotSupportedDialog = new ContentDialog()
      {
         Title = "Streetside is not supported",
         Content ="\nStreetside views are not supported on this device.",
         PrimaryButtonText = "OK"
      };
      await viewNotSupportedDialog.ShowAsync();            
   }
}
```

## Отображение трехмерных гибридных представлений


Задайте трехмерную перспективу карты с помощью класса [**MapScene**](https://msdn.microsoft.com/library/windows/apps/dn974329). Сцена карты представляет трехмерное представление, отображаемое на карте. Класс [**MapCamera**](https://msdn.microsoft.com/library/windows/apps/dn974244) представляет положение камеры, отображающей такое представление.

![](images/mapcontrol-techdiagram.png)

Чтобы здания и другие объекты на карте отображались в трехмерном представлении, задайте для свойства [**Style**](https://msdn.microsoft.com/library/windows/apps/dn637051) элемента управления картой значение [**MapStyle.Aerial3DWithRoads**](https://msdn.microsoft.com/library/windows/apps/dn637127). Это пример трехмерного представления со стилем **Aerial3DWithRoads**.

![Пример трехмерного представления карты.](images/only3d-730width.png)

Чтобы отобразить трехмерное представление, выполните указанные ниже действия.

1.  Проверьте свойство [**Is3DSupported**](https://msdn.microsoft.com/library/windows/apps/dn974265), чтобы узнать, поддерживаются ли трехмерные представления на устройстве.
2.  Если трехмерные представления поддерживаются, задайте для свойства [**Style**](https://msdn.microsoft.com/library/windows/apps/dn637051) элемента управления картой значение [**MapStyle.Aerial3DWithRoads**](https://msdn.microsoft.com/library/windows/apps/dn637127).
3.  Создайте объект [**MapScene**](https://msdn.microsoft.com/library/windows/apps/dn974329) с помощью одного из множества методов **CreateFrom**, например [**CreateFromLocationAndRadius**](https://msdn.microsoft.com/library/windows/apps/dn974336) и [**CreateFromCamera**](https://msdn.microsoft.com/library/windows/apps/dn974334).
4.  Вызовите метод [**TrySetSceneAsync**](https://msdn.microsoft.com/library/windows/apps/dn974296), чтобы отобразить трехмерное представление. Кроме того, можно настроить использование дополнительной анимации при изменениях представления с помощью константы из перечисления [**MapAnimationKind**](https://msdn.microsoft.com/library/windows/apps/dn637002).

В данном примере показано, как отображать трехмерное представление.

```csharp
private async void display3DLocation()
{
   if (MapControl1.Is3DSupported)
   {
      // Set the aerial 3D view.
      MapControl1.Style = MapStyle.Aerial3DWithRoads;

      // Specify the location.
      BasicGeoposition hwGeoposition = new BasicGeoposition() { Latitude = 34.134, Longitude = -118.3216};
      Geopoint hwPoint = new Geopoint(hwGeoposition);

      // Create the map scene.
      MapScene hwScene = MapScene.CreateFromLocationAndRadius(hwPoint,
                                                                           80, /* show this many meters around */
                                                                           0, /* looking at it to the North*/
                                                                           60 /* degrees pitch */);
      // Set the 3D view with animation.
      await MapControl1.TrySetSceneAsync(hwScene,MapAnimationKind.Bow);
   }
   else
   {
      // If 3D views are not supported, display dialog.
      ContentDialog viewNotSupportedDialog = new ContentDialog()
      {
         Title = "3D is not supported",
         Content = "\n3D views are not supported on this device.",
         PrimaryButtonText = "OK"
      };
      await viewNotSupportedDialog.ShowAsync();
   }
}
```

## Получение информации о местоположениях и элементах


Чтобы получить информацию о местоположениях на карте, вызовите указанные ниже методы объекта [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004).

-   
              Метод [**GetLocationFromOffset**](https://msdn.microsoft.com/library/windows/apps/dn637016): получение географического расположения, соответствующего заданной точке в окне просмотра элемента управления картой.
-   
              Метод [**GetOffsetFromLocation**](https://msdn.microsoft.com/library/windows/apps/dn637018): получение в окне просмотра элемента управления картой точки, которая соответствует заданному географическому расположению.
-   
              Метод [**IsLocationInView**](https://msdn.microsoft.com/library/windows/apps/dn637022): определение того, отображается ли в текущий момент заданное географическое расположение в окне просмотра элемента управления картой.
-   
              Метод [**FindMapElementsAtOffset**](https://msdn.microsoft.com/library/windows/apps/dn637014): получение элементов на карте, расположенных в заданной точке окна просмотра элемента управления картой.

## Обработка взаимодействия с пользователем и изменений


Чтобы обработать жесты ввода пользователя на карте, необходимо обработать указанные ниже события объекта [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004). Получите информацию о географическом положении на карте и физическом положении в окне просмотра, где был выполнен жест, проверив значения свойств [**Location**](https://msdn.microsoft.com/library/windows/apps/dn637091) и [**Position**](https://msdn.microsoft.com/library/windows/apps/dn637093) класса [**MapInputEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn637090).

-   [**MapTapped**](https://msdn.microsoft.com/library/windows/apps/dn637038)
-   [**MapDoubleTapped**](https://msdn.microsoft.com/library/windows/apps/dn637032)
-   [**MapHolding**](https://msdn.microsoft.com/library/windows/apps/dn637035)

Определите, загружена ли карта или только загружается, обработав событие [**LoadingStatusChanged**](https://msdn.microsoft.com/library/windows/apps/dn637028) элемента управления.

Чтобы обработать изменения, которые происходят при изменении пользователем или приложением параметров карты, необходимо обработать указанные ниже события объекта [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004). [Руководство по картам](https://msdn.microsoft.com/library/windows/apps/dn596102)

-   [**CenterChanged**](https://msdn.microsoft.com/library/windows/apps/dn637006)
-   [**HeadingChanged**](https://msdn.microsoft.com/library/windows/apps/dn637020)
-   [**PitchChanged**](https://msdn.microsoft.com/library/windows/apps/dn637045)
-   [**ZoomLevelChanged**](https://msdn.microsoft.com/library/windows/apps/dn637069)

## Связанные разделы

* [Центр разработки Карт Bing](https://www.bingmapsportal.com/)
* [Пример карты UWP](http://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Получение сведений о текущем местоположении](get-location.md)
* [Рекомендации по разработке приложений с определением местоположения](https://msdn.microsoft.com/library/windows/apps/hh465148)
* [Руководство по разработке карт](https://msdn.microsoft.com/library/windows/apps/dn596102)
* [Видео c конференции Build 2015: использование карт и местоположений в приложениях для Windows на телефонах, планшетах и ПК](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Пример приложения UWP для работы с картами](http://go.microsoft.com/fwlink/p/?LinkId=619982)
* [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004)





<!--HONumber=Aug16_HO3-->


