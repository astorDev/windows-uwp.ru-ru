---
author: msatranjr
title: "Отображение объектов на карте"
description: "Добавляйте объекты на карту, используя вешки, изображения, фигуры и элементы пользовательского интерфейса XAML."
ms.assetid: CA00D8EB-6C1B-4536-8921-5EAEB9B04FCA
translationtype: Human Translation
ms.sourcegitcommit: 92285ce32548bd6035c105e35c2b152432f8575a
ms.openlocfilehash: aec420d6591546e63c6343d7151afe9e95d1afd8

---

# Отображение объектов на карте


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Добавляйте объекты на карту, используя вешки, изображения, фигуры и элементы пользовательского интерфейса XAML. Объект— это определенная точка на карте, представляющая что-нибудь интересное. Например, это может быть расположение компании, города или друга.

**Совет.** Чтобы получить дополнительные сведения об отображении объектов в приложении, скачайте следующий пример из [репозитория Windows-universal-samples](http://go.microsoft.com/fwlink/p/?LinkId=619979) на сайте GitHub.

-   [Пример карты универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619977)

Вы можете отображать вешки, изображения и фигуры на карте, добавляя объекты [**MapIcon**](https://msdn.microsoft.com/library/windows/apps/dn637077), [**MapPolygon**](https://msdn.microsoft.com/library/windows/apps/dn637103) и [**MapPolyline**](https://msdn.microsoft.com/library/windows/apps/dn637114) в коллекцию [**MapElements**](https://msdn.microsoft.com/library/windows/apps/dn637033) элемента управления картой. Используйте привязку данных или добавляйте элементы программным способом. Невозможно выполнить привязку к коллекции **MapElements** с помощью объявлений в разметке XAML.

Чтобы отобразить элементы пользовательского интерфейса XAML, например [**Button**](https://msdn.microsoft.com/library/windows/apps/br209265), [**HyperlinkButton**](https://msdn.microsoft.com/library/windows/apps/br242739) или [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652), на карте, добавьте их в качестве [**Children**](https://msdn.microsoft.com/library/windows/apps/dn637008) (дочерних элементов) объекта [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004). Кроме того, можно добавить их в объект [**MapItemsControl**](https://msdn.microsoft.com/library/windows/apps/dn637094) или привязать объект **MapItemsControl** к коллекции элементов.

Краткие сведения.

-   [Добавление объекта MapIcon на карту](#mapicon) для отображения изображения, например вешки с необязательным текстом.
-   [Добавление объекта MapPolygon на карту](#mappolygon) для отображения многоточечной фигуры.
-   [Добавление объекта MapPolyline на карту](#mappolyline) для отображения линий на карте.
-   [Добавление кода XAML на карту](#mapxaml) для отображения настраиваемых элементов пользовательского интерфейса.

Если необходимо разместить на карте большое количество элементов, можно [наложить на нее динамически перераспределяемые изображения](overlay-tiled-images.md). Сведения о том, как отобразить дороги на карте, см. в статье [Отображение дорог и направлений](routes-and-directions.md).

## Добавление объекта MapIcon


Чтобы показать изображение, например вешку, на карте и при желании добавить к нему текст, используйте класс [**MapIcon**](https://msdn.microsoft.com/library/windows/apps/dn637077). Можно принять изображение по умолчанию или указать пользовательское изображение с помощью свойства [**Image**](https://msdn.microsoft.com/library/windows/apps/dn637078). На рисунке ниже показано изображение по умолчанию для объекта **MapIcon**, у которого не задано значение для свойства [**Title**](https://msdn.microsoft.com/library/windows/apps/dn637088), с коротким названием, длинным названием и очень длинным названием.

![Пример MapIcon с названиями разной длины.](images/mapctrl-mapicons.png)

В примере ниже показано, как отобразить карту Сиэтла и добавить объект [**MapIcon**](https://msdn.microsoft.com/library/windows/apps/dn637077) с изображением по умолчанию и необязательным заголовком, чтобы указать расположение башни Спейс-Нидл. Кроме того, в примере показано, как выполнить центровку карты по отношению к значку и увеличить ее масштаб. Общие сведения об использовании элемента управления картой см. в статье [Отображение карт с помощью двумерных и трехмерных представлений, а также представлений Streetside](display-maps.md).

```csharp
      private void displayPOIButton_Click(object sender, RoutedEventArgs e)
      {
         // Specify a known location.
         BasicGeoposition snPosition = new BasicGeoposition() { Latitude = 47.620, Longitude = -122.349 };
         Geopoint snPoint = new Geopoint(snPosition);

         // Create a MapIcon.
         MapIcon mapIcon1 = new MapIcon();
         mapIcon1.Location = snPoint;
         mapIcon1.NormalizedAnchorPoint = new Point(0.5, 1.0);
         mapIcon1.Title = "Space Needle";
         mapIcon1.ZIndex = 0;

         // Add the MapIcon to the map.
         MapControl1.MapElements.Add(mapIcon1);

         // Center the map over the POI.
         MapControl1.Center = snPoint;
         MapControl1.ZoomLevel = 14;
      }
```

В этом примере отображаются указанные ниже объекты на карте (изображение по умолчанию находится в центре).

![Карта с объектом MapIcon](images/displaypoidefault.png)

Указанная ниже строка кода отображает объект [**MapIcon**](https://msdn.microsoft.com/library/windows/apps/dn637077) с пользовательским изображением, сохраненным в папке Assets проекта. Свойство [**Image**](https://msdn.microsoft.com/library/windows/apps/dn637078) объекта **MapIcon** принимает значение с типом [**RandomAccessStreamReference**](https://msdn.microsoft.com/library/windows/apps/hh701813). Этому типу требуется оператор **using** для пространства имен [**Windows.Storage.Streams**](https://msdn.microsoft.com/library/windows/apps/br241791).

**Совет.** Чтобы обеспечить максимальную производительность при использовании одного и того же изображения для нескольких значков карт, объявите объект [**RandomAccessStreamReference**](https://msdn.microsoft.com/library/windows/apps/hh701813) на уровне страницы или приложения.

```csharp
    MapIcon1.Image =
        RandomAccessStreamReference.CreateFromUri(new Uri("ms-appx:///Assets/customicon.png"));
```

При работе с классом [**MapIcon**](https://msdn.microsoft.com/library/windows/apps/dn637077) учитывайте указанные ниже соображения.

-   Свойство [**Image**](https://msdn.microsoft.com/library/windows/apps/dn637078) поддерживает изображения размером не более 2048×2048 пикселей.
-   По умолчанию отображение изображения значка карты не гарантируется. Этот элемент может быть скрыт, если он заслоняет другие элементы или метки на карте. Чтобы он оставался видимым, задайте для свойства [**CollisionBehaviorDesired**](https://msdn.microsoft.com/library/windows/apps/dn974327) значение [**MapElementCollisionBehavior.RemainVisible**](https://msdn.microsoft.com/library/windows/apps/dn974314).
-   Отображение необязательного свойства [**Title**](https://msdn.microsoft.com/library/windows/apps/dn637088) объекта [**MapIcon**](https://msdn.microsoft.com/library/windows/apps/dn637077) не гарантируется. Если текст не отображается, уменьшите масштаб, уменьшив значение свойства [**ZoomLevel**](https://msdn.microsoft.com/library/windows/apps/dn637068) объекта [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004).
-   При показе изображения [**MapIcon**](https://msdn.microsoft.com/library/windows/apps/dn637077), которое указывает определенное местоположение на карте, например вешки или стрелки, попробуйте присвоить значению свойства [**NormalizedAnchorPoint**](https://msdn.microsoft.com/library/windows/apps/dn637082) приблизительное местоположение указателя на изображении. Если для свойства **NormalizedAnchorPoint** оставить значение по умолчанию (0,0), которое соответствует верхнему левому углу изображения, то в результате изменений свойства карты [**ZoomLevel**](https://msdn.microsoft.com/library/windows/apps/dn637068) изображение может указывать на другое местоположение.

## Добавление объекта MapPolygon


Для отображения многоточечной фигуры на карте используйте класс [**MapPolygon**](https://msdn.microsoft.com/library/windows/apps/dn637103). В показанном ниже фрагменте из [примера карты UWP](http://go.microsoft.com/fwlink/p/?LinkId=619977) показано, как отобразить красное поле с синей рамкой на карте.

```csharp
private void mapPolygonAddButton_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
{
   double centerLatitude = myMap.Center.Position.Latitude;
   double centerLongitude = myMap.Center.Position.Longitude;
   MapPolygon mapPolygon = new MapPolygon();
   mapPolygon.Path = new Geopath(new List<BasicGeoposition>() {
         new BasicGeoposition() {Latitude=centerLatitude+0.0005, Longitude=centerLongitude-0.001 },                
         new BasicGeoposition() {Latitude=centerLatitude-0.0005, Longitude=centerLongitude-0.001 },                
         new BasicGeoposition() {Latitude=centerLatitude-0.0005, Longitude=centerLongitude+0.001 },
         new BasicGeoposition() {Latitude=centerLatitude+0.0005, Longitude=centerLongitude+0.001 },

   });
           
   mapPolygon.ZIndex = 1;
   mapPolygon.FillColor = Colors.Red;
   mapPolygon.StrokeColor = Colors.Blue;
   mapPolygon.StrokeThickness = 3;
   mapPolygon.StrokeDashed = false;
   myMap.MapElements.Add(mapPolygon);
}
```

## Добавление объекта MapPolyline


Для отображения линии на карте используйте класс [**MapPolyline**](https://msdn.microsoft.com/library/windows/apps/dn637114). В показанном ниже фрагменте из [примера карты UWP](http://go.microsoft.com/fwlink/p/?LinkId=619977) показано, как отобразить пунктирную линию на карте.

```csharp
private void mapPolylineAddButton_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
{
   double centerLatitude = myMap.Center.Position.Latitude;
   double centerLongitude = myMap.Center.Position.Longitude;
   MapPolyline mapPolyline = new MapPolyline();
   mapPolyline.Path = new Geopath(new List<BasicGeoposition>() {                
         new BasicGeoposition() {Latitude=centerLatitude-0.0005, Longitude=centerLongitude-0.001 },                
         new BasicGeoposition() {Latitude=centerLatitude+0.0005, Longitude=centerLongitude+0.001 },
   });
              
   mapPolyline.StrokeColor = Colors.Black;
   mapPolyline.StrokeThickness = 3;
   mapPolyline.StrokeDashed = true;
   myMap.MapElements.Add(mapPolyline);
}
```

## Добавление XAML


Для отображения настраиваемых элементов пользовательского интерфейса на карте используйте XAML. Чтобы расположить XAML на карте, укажите расположение и нормализованную точку привязки XAML.

-   Чтобы задать местоположение на карте, где необходимо разместить XAML, вызовите метод [**SetLocation**](https://msdn.microsoft.com/library/windows/desktop/ms704369).
-   Чтобы настроить относительное местоположение в XAML, соответствующее указанному местоположению, вызовите метод [**SetNormalizedAnchorPoint**](https://msdn.microsoft.com/library/windows/apps/dn637050).

В примере ниже показано, как отобразить карту Сиэтла и добавить элемент управления XAML [**Border**](https://msdn.microsoft.com/library/windows/apps/br209250), чтобы указать местоположение башни Спейс-Нидл. Кроме того, в примере показано, как выполнить центровку карты по отношению к области и увеличить ее масштаб. Общие сведения об использовании элемента управления картой см. в статье [Отображение карт с помощью двумерных и трехмерных представлений, а также представлений Streetside](display-maps.md).

```csharp
private void displayXAMLButton_Click(object sender, RoutedEventArgs e)
{
   // Specify a known location.
   BasicGeoposition snPosition = new BasicGeoposition() { Latitude = 47.620, Longitude = -122.349 };
   Geopoint snPoint = new Geopoint(snPosition);

   // Create a XAML border.
   Border border = new Border
   {
      Height = 100,
      Width = 100,
      BorderBrush = new SolidColorBrush(Windows.UI.Colors.Blue),
      BorderThickness = new Thickness(5),
   };

   // Center the map over the POI.
   MapControl1.Center = snPoint;
   MapControl1.ZoomLevel = 14;

   // Add XAML to the map.
   MapControl1.Children.Add(border);
   MapControl.SetLocation(border, snPoint);
   MapControl.SetNormalizedAnchorPoint(border, new Point(0.5, 0.5));
}
```

В этом примере показано, как отобразить синюю рамку на карте.

![](images/displaypoixaml.png)

В примерах ниже показано, как добавить элементы пользовательского интерфейса XAML непосредственно в разметку XAML страницы, используя привязку данных. Как и другие элементы XAML, отображающие содержимое, [**Children**](https://msdn.microsoft.com/library/windows/apps/dn637008)— это свойство содержимого по умолчанию объекта [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004), и его не нужно явно задавать в разметке XAML.

В этом примере показано, как отобразить два элемента управления XAML в качестве неявных потомков класса [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004).

```xml
<maps:MapControl>
    <TextBox Text="Seattle" maps:MapControl.Location="{Binding SeattleLocation}"/>
    <TextBox Text="Bellevue" maps:MapControl.Location="{Binding BellevueLocation}"/>
</maps:MapControl>
```

В этом примере показано, как отобразить два элемента управления XAML, содержащиеся в классе [**MapItemsControl**](https://msdn.microsoft.com/library/windows/apps/dn637094).

```xml
<maps:MapControl>
  <maps:MapItemsControl>
    <TextBox Text="Seattle" maps:MapControl.Location="{Binding SeattleLocation}"/>
    <TextBox Text="Bellevue" maps:MapControl.Location="{Binding BellevueLocation}"/>
  </maps:MapItemsControl>
</maps:MapControl>
```

В этом примере показано, как отобразить коллекцию элементов XAML, связанных с классом [**MapItemsControl**](https://msdn.microsoft.com/library/windows/apps/dn637094).

```xml
<maps:MapControl x:Name="MapControl" MapTapped="MapTapped" MapDoubleTapped="MapTapped" MapHolding="MapTapped">
  <maps:MapItemsControl ItemsSource="{Binding StateOverlays}">
    <maps:MapItemsControl.ItemTemplate>
      <DataTemplate>
        <StackPanel Background="Black" Tapped="Overlay_Tapped">
          <TextBlock maps:MapControl.Location="{Binding Location}" Text="{Binding Name}" maps:MapControl.NormalizedAnchorPoint="0.5,0.5" FontSize="20" Margin="5"/>
        </StackPanel>
      </DataTemplate>
    </maps:MapItemsControl.ItemTemplate>
  </maps:MapItemsControl>
</maps:MapControl>
```

## Связанные разделы

* [Центр разработки Карт Bing](https://www.bingmapsportal.com/)
* [Пример карты UWP](http://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Руководство по разработке карт](https://msdn.microsoft.com/library/windows/apps/dn596102)
* [Видео c конференции Build 2015: использование карт и местоположений в приложениях для Windows на телефонах, планшетах и ПК](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Пример приложения UWP для работы с картами](http://go.microsoft.com/fwlink/p/?LinkId=619982)
* [**MapIcon**](https://msdn.microsoft.com/library/windows/apps/dn637077)
* [**MapPolygon**](https://msdn.microsoft.com/library/windows/apps/dn637103)
* [**MapPolyline**](https://msdn.microsoft.com/library/windows/apps/dn637114)





<!--HONumber=Aug16_HO3-->


