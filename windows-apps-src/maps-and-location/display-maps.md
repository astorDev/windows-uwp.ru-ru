---
title: Отображение карт с помощью двумерных и трехмерных представлений, а также с помощью представлений Streetside
description: Карту можно показывать в исчезающем окне, называемом *карточкой места*, или в полнофункциональном элементе управления с картой.
ms.assetid: 3839E00B-2C1E-4627-A45F-6DDA98D7077F
ms.date: 03/19/2018
ms.topic: article
keywords: windows 10, uwp, карта, расположение, элемент управления картой, представления карты
ms.localizationpriority: medium
ms.openlocfilehash: cc12f6c9b9177bce9a91288fdd2c43c118be5f61
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79210310"
---
# <a name="display-maps-with-2d-3d-and-streetside-views"></a>Отображение карт с помощью двумерных и трехмерных представлений, а также с помощью представлений Streetside

Карту можно показывать во всплывающем окне, называемом *карточкой места*, или в полнофункциональном элементе управления с картой.

Загрузите [пример карты](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl), чтобы опробовать некоторые функции, описанные в данном руководстве.

<a id="placecard" />

## <a name="display-map-in-a-placecard"></a>Показ карты в карточке места
Можно отобразить карту внутри облегченного всплывающего окна выше, ниже или рядом с элементом пользовательского интерфейса или области приложения, которых касается пользователь. На карте можно отображать город или адрес, относящийся к информации в приложении.  

На этой карточке места показан город Сиэтл.

![карточка места с Сиэтлом](images/placecard-city.png)

Вот код, который показывает Сиэтл в карточке места под кнопкой.

```csharp
private void Seattle_Click(object sender, RoutedEventArgs e)
{
    Geopoint seattlePoint = new Geopoint
        (new BasicGeoposition { Latitude = 47.6062, Longitude = -122.3321 });

    PlaceInfo spaceNeedlePlace = PlaceInfo.Create(seattlePoint);

    FrameworkElement targetElement = (FrameworkElement)sender;

    GeneralTransform generalTransform =
        targetElement.TransformToVisual((FrameworkElement)targetElement.Parent);

    Rect rectangle = generalTransform.TransformBounds(new Rect(new Point
        (targetElement.Margin.Left, targetElement.Margin.Top), targetElement.RenderSize));

    spaceNeedlePlace.Show(rectangle, Windows.UI.Popups.Placement.Below);
}
```

Эта карточка места показывает расположение башни Спейс-Нидл в Сиэтле.

![карточка места с расположением спейс-нидл](images/placecard-needle.png)

Вот код, который показывает Спейс-Нидл в карточке места под кнопкой.

```csharp
private void SpaceNeedle_Click(object sender, RoutedEventArgs e)
{
    Geopoint spaceNeedlePoint = new Geopoint
        (new BasicGeoposition { Latitude = 47.6205, Longitude = -122.3493 });

    PlaceInfoCreateOptions options = new PlaceInfoCreateOptions();

    options.DisplayAddress = "400 Broad St, Seattle, WA 98109";
    options.DisplayName = "Seattle Space Needle";

    PlaceInfo spaceNeedlePlace =  PlaceInfo.Create(spaceNeedlePoint, options);

    FrameworkElement targetElement = (FrameworkElement)sender;

    GeneralTransform generalTransform =
        targetElement.TransformToVisual((FrameworkElement)targetElement.Parent);

    Rect rectangle = generalTransform.TransformBounds(new Rect(new Point
        (targetElement.Margin.Left, targetElement.Margin.Top), targetElement.RenderSize));

    spaceNeedlePlace.Show(rectangle, Windows.UI.Popups.Placement.Below);
}
```

<a id="map-control" />

## <a name="display-map-in-a-control"></a>Показ карты в элементе управления

Используйте элемент управления с картой для отображения полнофункциональной и настраиваемой карты в приложении. Элемент управления картой может отображать карты дорог и гибридный трехмерный вид, маршруты, результаты поиска и сведения о ситуации на дорогах. На карте можно показать положение пользователя, маршруты и интересные места. Карта может также показывать трехмерный гибридный вид, представления Streetside, предоставлять сведения о ситуации на дорогах и показывать сведения о местных организациях и заведениях.

Используйте элемент управления картой, если нужно показать карту в приложении, которое позволяет пользователям просматривать сведения, зависящие от приложения или общие географические сведения. Наличие элемента управления картой в вашем приложении означает, что пользователям не нужно выходить из вашего приложения для получения соответствующих сведений.

> [!NOTE]
>Если ничего опасного в выходе пользователей за пределы вашего приложения нет, рассмотрите возможность использования приложения Карты Windows для предоставления этих сведений. Ваше приложение может использовать приложение Карты Windows для отображения определенных карт, маршрутов и результатов поиска. Дополнительные сведения см. в разделе [Запуск приложения «Карты Windows»](https://docs.microsoft.com/windows/uwp/launch-resume/launch-maps-app).

### <a name="add-a-map-control-to-your-app"></a>Добавление элемента управления с картой в приложение

Карту можно отображать на странице XAML, добавив класс [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl). Для использования **MapControl** необходимо объявить пространство имен [**Windows.UI.Xaml.Controls.Maps**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps) на странице XAML или в коде. Если перетащить элемент управления с панели элементов, объявление пространства имен будет добавлено автоматически. Если вы добавляете **MapControl** на страницу XAML вручную, придется вручную добавить и объявление пространства имен в верхнюю часть страницы.

В примере ниже показано, как отобразить базовый элемент управления картой и настроить карту для отображения элементов управления масштабом и наклоном в дополнение к функции приема сенсорного ввода.

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

### <a name="get-and-set-a-maps-authentication-key"></a>Получение и установка ключа проверки подлинности карт

Прежде чем использовать класс [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) и службы карт, необходимо в качестве значения свойства [**MapServiceToken**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.mapservicetoken) указать ключ проверки подлинности карт. В предыдущих примерах замените код `EnterYourAuthenticationKeyHere` ключом, полученным из [Центра разработки Карт Bing](https://www.bingmapsportal.com/). Текст **Внимание! Не указан MapServiceToken** будет отображаться под элементом управления, до тех пор пока вы не укажете ключ проверки подлинности карт. Дополнительные сведения о получении и установке ключа проверки подлинности карт см. в статье [Запрос ключа проверки подлинности карт](authentication-key.md).

## <a name="set-the-location-of-a-map"></a>Задайте местоположение для карты
Направьте карту на любое местоположение, которое требуется использовать, или текущее расположение пользователя.  

### <a name="set-a-starting-location-for-the-map"></a>Задайте начальное расположение для карты

Укажите расположение, которое должно отображаться на карте, задав свойство [**Center**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.center) класса [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl) в коде или путем привязки свойства в разметке XAML. В примере ниже показано, как отобразить карту, в центре которой расположен город Сиэтл.

> [!NOTE]
> Так как строку невозможно преобразовать в класс [**Geopoint**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geopoint), то, соответственно, невозможно задать значение для свойства [**Center**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.center) в разметке XAML, если вы не используете привязку данных. (Это ограничение также применяется к вложенному свойству [**MapControl.Location**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.setlocation).)

 
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

### <a name="set-the-current-location-of-the-map"></a>Настройте карту в соответствии с текущим местоположением

Перед тем как приложение сможет получить доступ к сведениям о местоположении пользователя, оно должно вызвать метод [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync). В этот момент ваше приложение должно находиться на переднем плане, а из потока пользовательского интерфейса должен быть вызван метод **RequestAccessAsync**. До тех пор пока пользователь не предоставит вашему приложению разрешение на доступ к данным о местоположении, приложение не сможет использовать такие данные.

С помощью метода [**GetGeopositionAsync**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.getgeopositionasync) класса [**Geolocator**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator) получите текущее местоположение устройства (если доступна функция определения местоположения). Чтобы получить соответствующий класс [**Geopoint**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geopoint), используйте свойство [**Point**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geocoordinate.point) геокоординаты геоположения. Дополнительные сведения см. в статье [Получение сведений о текущем местоположении](get-location.md).

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

При отображении расположения устройства на карте рекомендуем отображать рисунки и задавать масштаб на основе точности данных о расположении. Дополнительные сведения см. в статье [Руководство по приложениям с определением местонахождения](https://docs.microsoft.com/windows/uwp/maps-and-location/guidelines-and-checklist-for-detecting-location).

### <a name="change-the-location-of-the-map"></a>Изменение расположения на карте

Чтобы изменить расположение, отображаемое на двумерной карте, вызовите одну из перегрузок метода [**TrySetViewAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.trysetviewasync). Используйте этот метод для определения новых значений для [**Center**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.center), [**ZoomLevel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.zoomlevel), [**Heading**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.heading), и [**Pitch**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.pitch). Кроме того, можно настроить использование дополнительной анимации при изменениях представления с помощью константы из перечисления [**MapAnimationKind**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapAnimationKind).

Чтобы изменить расположение на трехмерной карте, используйте метод [**TrySetSceneAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.trysetsceneasync). Дополнительные сведения см. в статье [Отображение трехмерных гибридных представлений](#3Dviews).

Вызовите метод [**TrySetViewBoundsAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.trysetviewboundsasync), чтобы отобразить содержимое [**GeoboundingBox**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.GeoboundingBox) на карте. Например, с помощью этого метода можно отобразить на карте маршрут или часть маршрута. Подробности см. в разделе [Отображение маршрутов и направлений на карте](routes-and-directions.md).

## <a name="change-the-appearance-of-a-map"></a>Изменение внешнего вида карты

Чтобы настроить внешний вид карты, задайте свойство [**StyleSheet**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.StyleSheet) элемента управления картой любому из существующих объектов [**MapStyleSheet**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapstylesheet).

```csharp
myMap.StyleSheet = MapStyleSheet.RoadDark();
```

![Карта в темном стиле](images/style-dark.png)

Можно также использовать JSON для определения пользовательских стилей и затем использовать этот JSON для создания объекта [**MapStyleSheet**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapstylesheet).

JSON таблицы стилей можно создать в интерактивном режиме с помощью приложения [редактора таблицы стилей карт](https://www.microsoft.com/p/map-style-sheet-editor/9nbhtcjt72ft) .

```csharp
myMap.StyleSheet = MapStyleSheet.ParseFromJson(@"
    {
        ""version"": ""1.0"",
        ""settings"": {
            ""landColor"": ""#FFFFFF"",
            ""spaceColor"": ""#000000""
        },
        ""elements"": {
            ""mapElement"": {
                ""labelColor"": ""#000000"",
                ""labelOutlineColor"": ""#FFFFFF""
            },
            ""water"": {
                ""fillColor"": ""#DDDDDD""
            },
            ""area"": {
                ""fillColor"": ""#EEEEEE""
            },
            ""political"": {
                ""borderStrokeColor"": ""#CCCCCC"",
                ""borderOutlineColor"": ""#00000000""
            }
        }
    }
");
```

![Карта в пользовательском стиле](images/style-custom.png)

Полный справочник по JSON см. в разделе [Пример таблицы стилей карты](elements-of-map-style-sheet.md).

Можно начать с существующего листа и затем использовать JSON для переопределения любых требуемых элементов. Этот пример начинается с существующего стиля и использует JSON для изменения цвета водоемов на карте.

```csharp
 MapStyleSheet \customSheet = MapStyleSheet.ParseFromJson(@"
    {
        ""version"": ""1.0"",
        ""elements"": {
            ""water"": {
                ""fillColor"": ""#DDDDDD""
            }
        }
    }
");

MapStyleSheet builtInSheet = MapStyleSheet.RoadDark();

myMap.StyleSheet = MapStyleSheet.Combine(new List<MapStyleSheet> { builtInSheet, customSheet });
```

![Сочетание стилей карты](images/style-combined.png)

>[!NOTE]
>Стили, определенные во второй таблице стилей, обладают приоритетом перед стилями в первой таблице.

## <a name="set-orientation-and-perspective"></a>Задание ориентации и точки обзора

Приближение, отдаление, поворот и наклон камеры карты для получения именно того угла, который необходим для вашего эффекта. Попробуйте использовать следующие свойства.

-   Установите географическую точку как **центр** карты, задав соответствующее значение для свойства [**Center**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.center).
-   Установите **масштаб карты**, присвоив значение свойству [**ZoomLevel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.zoomlevel) от 1 до 20.
-   Установите **вращение** карты, задав свойство [**Heading**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.heading), для которого 0 или 360 градусов обозначают север, 90 — восток, 180 — юг и 270 — запад.
-   Установите **наклон** карты, присвоив значение свойству [**DesiredPitch**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.desiredpitch) от 0 до 65 градусов.

## <a name="show-and-hide-map-features"></a>Показ и сокрытие элементов карты

Показывайте или скрывайте элементы карты, такие как дороги и ориентиры, путем установки значений следующих свойств объекта [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl).

* Отобразите **здания и ориентиры** на карте, включив или отключив свойство [**LandmarksVisible**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.landmarksvisible).

  > [!NOTE]
  > Можно показать или скрыть здания, но вы не можете предотвратить их появление в трехмерном виде.  

* Чтобы показать на карте **объекты для пешеходов**, например надземные переходы, включите или отключите свойство [**PedestrianFeaturesVisible**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.pedestrianfeaturesvisible).
* Чтобы показать на карте **пробки**, включите или отключите свойство [**TrafficFlowVisible**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.trafficflowvisible).
* Чтобы указать, необходимо ли отображать **водяной знак** на карте, присвойте свойству [**WatermarkMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.watermarkmode) значение одной из констант [**MapWatermarkMode**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapWatermarkMode).
* Чтобы показать на карте **маршруты для движения пешком или на автомобиле**, добавьте [**MapRouteView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapRouteView) в коллекцию [**Routes**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.routes) элемента управления картой. Подробности и примеры см. в разделе [Отображение маршрутов и направлений на карте](routes-and-directions.md).

Сведения о том, как отображать вешки, фигуры и элементы управления XAML в объекте [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl), см. в статье [Отображение объектов на карте](display-poi.md).

## <a name="display-streetside-views"></a>Отображение представлений Streetside


Представление Streetside — это перспектива уровня улицы для определенного расположения, отображаемая в верхней части элемента управления картой.

![Пример представления Streetside элемента управления картой.](images/onlystreetside-730width.png)

Рассмотрите возможность отделения взаимодействия «внутри» представления Streetside от карты, изначально отображаемой в элементе управления картой. Например, изменение расположения в представлении Streetside не изменяет расположение и внешний вид карты в представлении Streetside. После закрытия представления Streetside (с помощью значка **X** в правом верхнем углу элемента управления) исходная карта останется неизменной.

Чтобы отобразить представление Streetside, выполните указанные ниже действия.

1.  Проверьте свойство [**IsStreetsideSupported**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.isstreetsidesupported), чтобы узнать, поддерживаются ли представления Streetside на устройстве.
2.  Если представление Streetside поддерживается, создайте класс [**StreetsidePanorama**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.StreetsidePanorama) возле указанного местоположения, вызвав метод [**FindNearbyAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.streetsidepanorama.findnearbyasync).
3.  Чтобы узнать, обнаружена ли ближайшая панорама, проверьте, имеет ли класс [**StreetsidePanorama**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.StreetsidePanorama) значение null.
4.  Если ближайшая панорама обнаружена, создайте объект [**StreetsideExperience**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.streetsideexperience) для свойства [**CustomExperience**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.customexperience) элемента управления картой.

В этом примере показано, как отображать представление Streetside, аналогичное предыдущему изображению.

**Обратите внимание** ,  карту обзора не отображается, если размер элемента управления картой слишком мал.

 

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

<a id="3Dviews" />
## <a name="display-aerial-3d-views"></a>Отображение трехмерных гибридных представлений


Задайте трехмерную перспективу карты с помощью класса [**MapScene**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapScene). Сцена карты представляет трехмерное представление, отображаемое на карте. Класс [**MapCamera**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapCamera) представляет положение камеры, отображающей такое представление.

![Схема расположения MapCamera для расположения сцены карты](images/mapcontrol-techdiagram.png)

Чтобы здания и другие объекты на карте отображались в трехмерном представлении, задайте для свойства [**Style**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.style) элемента управления картой значение [**MapStyle.Aerial3DWithRoads**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapStyle). Это пример трехмерного представления со стилем **Aerial3DWithRoads**.

![Пример трехмерного представления карты.](images/only3d-730width.png)

Чтобы отобразить трехмерное представление, выполните указанные ниже действия.

1.  Проверьте свойство [**Is3DSupported**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.is3dsupported), чтобы узнать, поддерживаются ли трехмерные представления на устройстве.
2.  Если трехмерные представления поддерживаются, задайте для свойства [**Style**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.style) элемента управления картой значение [**MapStyle.Aerial3DWithRoads**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapStyle).
3.  Создайте объект [**MapScene**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapScene) с помощью одного из множества методов **CreateFrom**, например [**CreateFromLocationAndRadius**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapscene.createfromlocationandradius) и [**CreateFromCamera**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapscene.createfromcamera).
4.  Вызовите метод [**TrySetSceneAsync**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.trysetsceneasync), чтобы отобразить трехмерное представление. Кроме того, можно настроить использование дополнительной анимации при изменениях представления с помощью константы из перечисления [**MapAnimationKind**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapAnimationKind).

В данном примере показано, как отображать трехмерное представление.

```csharp
private async void display3DLocation()
{
   if (MapControl1.Is3DSupported)
   {
      // Set the aerial 3D view.
      MapControl1.Style = MapStyle.Aerial3DWithRoads;

      // Specify the location.
      BasicGeoposition hwGeoposition = new BasicGeoposition() { Latitude = 43.773251, Longitude = 11.255474};
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

## <a name="get-info-about-locations"></a>Получении информации о местоположениях


Чтобы получить информацию о местоположениях на карте, вызовите указанные ниже методы объекта [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl).

-   Метод [**трижетлокатионфромоффсет**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.getlocationfromoffset) — получает географическое расположение, соответствующее заданной точке в окне просмотра элемента управления картой.
-   Метод [**жетоффсетфромлокатион**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.getoffsetfromlocation) — возвращает точку в окне просмотра элемента управления картой, которая соответствует указанному географическому расположению.
-   Метод [**ислокатионинвиев**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.islocationinview) — определяет, отображается ли указанное географическое расположение в области просмотра элемента управления картой.
-   Метод [**финдмапелементсатоффсет**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.findmapelementsatoffset) — получение элементов на карте, расположенных в указанной точке окна просмотра элемента управления картой.

## <a name="handle-interaction-and-changes"></a>Обработка взаимодействия и изменений


Чтобы обработать жесты ввода пользователя на карте, необходимо обработать указанные ниже события объекта [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl). Получите информацию о географическом положении на карте и физическом положении в окне просмотра, где был выполнен жест, проверив значения свойств [**Location**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapinputeventargs.location) и [**Position**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapinputeventargs.position) класса [**MapInputEventArgs**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapInputEventArgs).

-   [**маптаппед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.maptapped)
-   [**мапдаублетаппед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.mapdoubletapped)
-   [**мафолдинг**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.mapholding)

Определите, загружена карта или только загружается, с помощью обработки события [**LoadingStatusChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.loadingstatuschanged) элемента управления.

Чтобы обработать изменения, которые происходят при изменении пользователем или приложением параметров карты, необходимо обработать указанные ниже события объекта [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl). [Руководства по картам](https://docs.microsoft.com/windows/uwp/maps-and-location/controls-map)

-   [**центерчанжед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.centerchanged)
-   [**хеадингчанжед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.headingchanged)
-   [**питччанжед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.pitchchanged)
-   [**зумлевелчанжед**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.zoomlevelchanged)

## <a name="best-practice-recommendations"></a>Рекомендации

-   Используйте свободное место на экране (или весь экран) для отображения карты, чтобы пользователям не приходилось постоянно сдвигать и масштабировать карту для просмотра географических сведений.

-   Если карта используется только для представления статического, информационного представления, то более подходящим будет использование карты меньшего размера. Если вы будете использовать статическую карту меньшего размера, задайте ее размеры на основании удобства использования. Используйте небольшой размер, чтобы сэкономить место на экране, но достаточный, чтобы отобразить нужную информацию.

-   Укажите достопримечательности на карте, используя [**map elements**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.maps.mapcontrol.mapelementsproperty). Любые дополнительные сведения могут быть отображены в качестве прозрачного наложения пользовательского интерфейса на карту.

## <a name="related-topics"></a>Связанные разделы

* [Bing Maps Developer Center](https://www.bingmapsportal.com/)
* [Пример карты UWP](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/MapControl)
* [Получение сведений о текущем расположении](get-location.md)
* [Рекомендации по разработке приложений, учитывающих данные о расположении](https://docs.microsoft.com/windows/uwp/maps-and-location/guidelines-and-checklist-for-detecting-location)
* [Руководство по разработке карт](https://docs.microsoft.com/windows/uwp/maps-and-location/controls-map)
* [Сборка видео 2015: использование карт и расположения на телефоне, планшете и ПК в приложениях Windows](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Пример приложения UWP для работы с трафиком](https://github.com/Microsoft/Windows-appsample-trafficapp)
* [**MapControl**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Maps.MapControl)
