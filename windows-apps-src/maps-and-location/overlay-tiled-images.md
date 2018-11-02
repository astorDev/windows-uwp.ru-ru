---
author: normesta
title: Наложение мозаичных изображений на карту
description: Наложение сторонних или настраиваемых мозаичных изображений на карте с помощью источников мозаичных данных. Используйте источники мозаичных данных для наложения специальных сведений, таких как данные о погоде, населении или сейсмические данные, а также для полной замены карты по умолчанию.
ms.assetid: 066BD6E2-C22B-4F5B-AA94-5D6C86A09BDF
ms.author: normesta
ms.date: 07/19/2018
ms.topic: article
keywords: Windows 10, uwp, карта, расположение, изображения, наложение
ms.localizationpriority: medium
ms.openlocfilehash: 7e01b115d3e2b2a305468357440acff50a06f3fd
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5946241"
---
# <a name="overlay-tiled-images-on-a-map"></a>Наложение плиточных изображений на карту

Наложение сторонних или настраиваемых мозаичных изображений на карте с помощью источников мозаичных данных. Используйте источники плиточных данных для наложения специальных сведений, таких как данные о погоде, населении или сейсмической активности, а также для полной замены карты по умолчанию.

**Совет**. Чтобы получить дополнительные сведения об использовании карт в приложении, скачайте [пример карты универсальной платформы Windows (UWP)](http://go.microsoft.com/fwlink/p/?LinkId=619977) с сайта GitHub.

<a id="tileintro" />

## <a name="tiled-image-overview"></a>Общие сведения о мозаичном изображении

Службы карт, например Карты Nokia и Карты Bing, нарезают карты на квадратные плитки для быстрого восстановления и отображения. Эти плитки имеют размер 256пикселей на 256пикселей и предварительно обрабатываются на нескольких уровнях детализации. Многие сторонние службы также предоставляют данные на основе карт, порезанные на плитки. Используйте источники плиточных данных для получения сторонних плиток или создания собственных пользовательских плиток и наложите их на карту, которая отображается в [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004).

**Важные**  при использовании источников плиточных данных не нужно писать код для запроса или размещения отдельных плиток. [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004) запрашивает плитки по мере необходимости. Каждый запрос задает координаты X и Y и масштаб для отдельной плитки. Можно просто задать формат универсального кода ресурса (URI) или имя файла, которые будут использоваться для получения плиток, в свойстве **UriFormatString**. То есть, вы вставляете заменяемые параметры в базовый универсальный код ресурса (URI) или имя файла, чтобы указать, где нужно передать координаты X и Y, а также масштаб для каждой плитки.

Вот пример свойства [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636992) для [**HttpMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636986), в котором показаны заменяемые параметры для координат X и Y и масштаба.

```syntax
http://www.<web service name>.com/z={zoomlevel}&x={x}&y={y}
```

(Координаты X и Y обозначают размещение отдельной плитки на карте мира при заданном уровне детализации. Система нумерации плиток начинается с {0, 0} в верхнем левом углу карты. Например, плитка с координатами {1, 2} находится во втором столбце в третьей строке сетки плиток.)

Дополнительные сведения о системе плиток, использующейся службами карт, см. в разделе [Система плиток Карт Bing](http://go.microsoft.com/fwlink/p/?LinkId=626692).

### <a name="overlay-tiles-from-a-tile-source"></a>Наложение плиток из источника плиточных данных

Наложите плиточные изображения из источника плиточных данных на карту с помощью [**MapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn637141).

1.  Создайте экземпляр одного из трех классов источника плиточных данных, производный от [**MapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn637141).

    -   [**HttpMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636986)
    -   [**LocalMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636994)
    -   [**CustomMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636983)

    Настройте **UriFormatString** для запросов плиток путем вставки заменяемых параметров в базовый универсальный код ресурса (URI) или имя файла.

    Следующий пример создает экземпляр [**HttpMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636986). В этом примере задано значение [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636992) в конструкторе **HttpMapTileDataSource**.

    ```csharp
        HttpMapTileDataSource dataSource = new HttpMapTileDataSource(
          "http://www.<web service name>.com/z={zoomlevel}&x={x}&y={y}");
    ```

2.  Создайте экземпляр и настройте [**MapTileSource**](https://msdn.microsoft.com/library/windows/apps/dn637144). Задайте [**MapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn637141), настроенный на предыдущем шаге как [**DataSource**](https://msdn.microsoft.com/library/windows/apps/dn637149) для **MapTileSource**.

    В следующем примере [**DataSource**](https://msdn.microsoft.com/library/windows/apps/dn637149) указано в конструкторе [**MapTileSource**](https://msdn.microsoft.com/library/windows/apps/dn637144).

    ```csharp
        MapTileSource tileSource = new MapTileSource(dataSource);
    ```

    Можно ограничить условия, при которых отображаются плитки, с помощью свойств [**MapTileSource**](https://msdn.microsoft.com/library/windows/apps/dn637144).

    -   Отображайте плитки только в определенной географической зоне, указав значение для свойства [**Bounds**](https://msdn.microsoft.com/library/windows/apps/dn637147).
    -   Отображайте плитки только при определенном уровне детализации, указав значения для свойства [**ZoomLevelRange**](https://msdn.microsoft.com/library/windows/apps/dn637171).

    При необходимости настройте другие свойства для [**MapTileSource**](https://msdn.microsoft.com/library/windows/apps/dn637144), которые влияют на загрузку или отображение плиток, например [**Layer**](https://msdn.microsoft.com/library/windows/apps/dn637157), [**AllowOverstretch**](https://msdn.microsoft.com/library/windows/apps/dn637145), [**IsRetryEnabled**](https://msdn.microsoft.com/library/windows/apps/dn637153) и [**IsTransparencyEnabled**](https://msdn.microsoft.com/library/windows/apps/dn637155).

3.  Добавьте [**MapTileSource**](https://msdn.microsoft.com/library/windows/apps/dn637144) в коллекцию [**TileSources**](https://msdn.microsoft.com/library/windows/apps/dn637053) элемента [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004).

    ```csharp
         MapControl1.TileSources.Add(tileSource);
    ```

## <a name="overlay-tiles-from-a-web-service"></a>Наложение плиток из веб-службы


Наложите плиточные изображения из веб-службы с помощью [**HttpMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636986).

1.  Создайте экземпляр [**HttpMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636986).
2.  Укажите формат универсального кода ресурса (URI), который веб-служба ожидает в качестве значения свойства [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636992). Чтобы создать это значение, вставьте заменяемые параметры в базовый универсальный код ресурса (URI). Например, в следующем примере кода значение свойства **UriFormatString** является таким:

    ``` syntax
    http://www.<web service name>.com/z={zoomlevel}&x={x}&y={y}
    ```

    Веб-служба должна поддерживать универсальный код ресурса (URI), который содержит заменяемые параметры {x}, {y} и {zoomlevel}. Большинство веб-служб (например, Nokia, Bing и Google) поддерживают URI такого формата. Если веб-служба требует дополнительных аргументов, которые недоступны со свойством [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636992), следует создать пользовательский универсальный код ресурса (URI). Создайте и верните универсальный код ресурса (URI) путем обработки события [**UriRequested**](https://msdn.microsoft.com/library/windows/apps/dn636993). Подробнее см. в разделе [Указание пользовательского универсального кода ресурса (URI)](#customuri) далее в данной статье.

3.  Затем выполните оставшиеся шаги, описанные ранее в разделе [Общие сведения о мозаичных изображениях](#tileintro).

В следующем примере показано наложение плиток из вымышленной веб-службы на карту Северной Америки. Значение для [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636992) указано в конструкторе [**HttpMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636986). В этом примере плитки отображаются только внутри географических границ, заданных необязательным свойством [**Bounds**](https://msdn.microsoft.com/library/windows/apps/dn637147).

```csharp
private void AddHttpMapTileSource()
{
    // Create the bounding box in which the tiles are displayed.
    // This example represents North America.
    BasicGeoposition northWestCorner =
        new BasicGeoposition() { Latitude = 48.38544, Longitude = -124.667360 };
    BasicGeoposition southEastCorner =
        new BasicGeoposition() { Latitude = 25.26954, Longitude = -80.30182 };
    GeoboundingBox boundingBox = new GeoboundingBox(northWestCorner, southEastCorner);

    // Create an HTTP data source.
    // This example retrieves tiles from a fictitious web service.
    HttpMapTileDataSource dataSource = new HttpMapTileDataSource(
        "http://www.<web service name>.com/z={zoomlevel}&x={x}&y={y}");

    // Optionally, add custom HTTP headers if the web service requires them.
    dataSource.AdditionalRequestHeaders.Add("header name", "header value");

    // Create a tile source and add it to the Map control.
    MapTileSource tileSource = new MapTileSource(dataSource);
    tileSource.Bounds = boundingBox;
    MapControl1.TileSources.Add(tileSource);
}
```

```cppwinrt
...
#include <winrt/Windows.Devices.Geolocation.h>
#include <winrt/Windows.UI.Xaml.Controls.Maps.h>
...
void MainPage::AddHttpMapTileSource()
{
    Windows::Devices::Geolocation::BasicGeoposition northWest{ 48.38544, -124.667360 };
    Windows::Devices::Geolocation::BasicGeoposition southEast{ 25.26954, -80.30182 };
    Windows::Devices::Geolocation::GeoboundingBox boundingBox{ northWest, southEast };

    Windows::UI::Xaml::Controls::Maps::HttpMapTileDataSource dataSource{
        L"http://www.<web service name>.com/z={zoomlevel}&x={x}&y={y}" };

    dataSource.AdditionalRequestHeaders().Insert(L"header name", L"header value");

    Windows::UI::Xaml::Controls::Maps::MapTileSource tileSource{ dataSource };
    tileSource.Bounds(boundingBox);

    MapControl1().TileSources().Append(tileSource);
}
...
```

```cpp
void MainPage::AddHttpMapTileSource()
{
    BasicGeoposition northWest = { 48.38544, -124.667360 };
    BasicGeoposition southEast = { 25.26954, -80.30182 };
    GeoboundingBox^ boundingBox = ref new GeoboundingBox(northWest, southEast);

    auto dataSource = ref new Windows::UI::Xaml::Controls::Maps::HttpMapTileDataSource(
        "http://www.<web service name>.com/z={zoomlevel}&x={x}&y={y}");

    dataSource->AdditionalRequestHeaders->Insert("header name", "header value");

    auto tileSource = ref new Windows::UI::Xaml::Controls::Maps::MapTileSource(dataSource);
    tileSource->Bounds = boundingBox;

    this->MapControl1->TileSources->Append(tileSource);
}
```

## <a name="overlay-tiles-from-local-storage"></a>Наложение плиток из локального хранилища


Наложите плиточные изображения, хранящиеся как файлы в локальном хранилище, с помощью [**LocalMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636994). Обычно такие файлы упаковываются и распространяются вместе с приложением.

1.  Создайте экземпляр [**LocalMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636994).
2.  Задайте формат имен файлов в качестве значения свойства [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636998). Чтобы создать это значение, вставьте заменяемые параметры в базовое имя файла. Например, в следующем примере кода значение свойства [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636992) является таким:

    ``` syntax
        Tile_{zoomlevel}_{x}_{y}.png
    ```

    Если формат имен файлов требует дополнительных аргументов, которые недоступны со свойством [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636998), следует создать пользовательский универсальный код ресурса (URI). Создайте и верните универсальный код ресурса (URI) путем обработки события [**UriRequested**](https://msdn.microsoft.com/library/windows/apps/dn637001). Подробнее см. в разделе [Указание пользовательского универсального кода ресурса (URI)](#customuri) далее в данной статье.

3.  Затем выполните оставшиеся шаги, описанные ранее в разделе [Общие сведения о мозаичных изображениях](#tileintro).

Для загрузки мозаичных данных из локального хранилища можно использовать следующие протоколы и адреса:

| Универсальный код ресурса (URI) | Подробнее |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| ms-appx:/// | Указывает на корневой каталог папки установки приложения. |
|  | Это расположение, на которое ссылается свойство [Package.InstalledLocation](https://msdn.microsoft.com/library/windows/apps/br224681). |
| ms-appdata:///local | Указывает на корневой каталог локального хранилища приложения. |
|  | Это расположение, на которое ссылается свойство [ApplicationData.LocalFolder](https://msdn.microsoft.com/library/windows/apps/br241621). |
| ms-appdata:///temp | Указывает на временную папку приложения. |
|  | Это расположение, на которое ссылается свойство [ApplicationData.TemporaryFolder](https://msdn.microsoft.com/library/windows/apps/br241629). |

 

В следующем примере плитки, хранящиеся в виде файлов в установочной папке приложения, загружаются с помощью протокола `ms-appx:///`. Значение для [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636998) указано в конструкторе [**LocalMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636994). В этом примере плитки отображаются, только если масштаб карты находится в диапазоне, заданном с помощью необязательного свойства [**ZoomLevelRange**](https://msdn.microsoft.com/library/windows/apps/dn637171).

```csharp
        void AddLocalMapTileSource()
        {
            // Specify the range of zoom levels
            // at which the overlaid tiles are displayed.
            MapZoomLevelRange range;
            range.Min = 11;
            range.Max = 20;

            // Create a local data source.
            LocalMapTileDataSource dataSource = new LocalMapTileDataSource(
                "ms-appx:///TileSourceAssets/Tile_{zoomlevel}_{x}_{y}.png");

            // Create a tile source and add it to the Map control.
            MapTileSource tileSource = new MapTileSource(dataSource);
            tileSource.ZoomLevelRange = range;
            MapControl1.TileSources.Add(tileSource);
        }
```

<a id="customuri" />

## <a name="provide-a-custom-uri"></a>Указание пользовательского универсального кода ресурса (URI)

Если заменяемых параметров, доступных со свойством [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636992) класса [**HttpMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636986) или со свойством [**UriFormatString**](https://msdn.microsoft.com/library/windows/apps/dn636998) класса [**LocalMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636994), недостаточно для получения плиток, следует создать пользовательский универсальный код ресурса (URI). Создайте и верните пользовательский универсальный код ресурса (URI), предоставив пользовательский обработчик для события **UriRequested**. Событие **UriRequested** вызывается для каждой отдельной плитки.

1.  Для создания пользовательского универсального кода ресурса (URI) объедините в пользовательском обработчике для события **UriRequested** необходимые пользовательские аргументы со свойствами [**X**](https://msdn.microsoft.com/library/windows/apps/dn610743), [**Y**](https://msdn.microsoft.com/library/windows/apps/dn610744) и [**ZoomLevel**](https://msdn.microsoft.com/library/windows/apps/dn610745) класса [**MapTileUriRequestedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn637177).
2.  Верните пользовательский универсальный код ресурса (URI) в свойстве [**Uri**](https://msdn.microsoft.com/library/windows/apps/dn610748) класса [**MapTileUriRequest**](https://msdn.microsoft.com/library/windows/apps/dn637173), который содержится в свойстве [**Request**](https://msdn.microsoft.com/library/windows/apps/dn637179) класса [**MapTileUriRequestedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn637177).

В следующем примере показано, как предоставить пользовательский универсальный код ресурса (URI) путем создания пользовательского обработчика для события **UriRequested**. В этом примере также показано, как реализовать шаблон откладывания, если необходимо выполнить действие асинхронно для создания пользовательского универсального кода ресурса (URI).

```csharp
using Windows.UI.Xaml.Controls.Maps;
using System.Threading.Tasks;
...
            var httpTileDataSource = new HttpMapTileDataSource();
            // Attach a handler for the UriRequested event.
            httpTileDataSource.UriRequested += HandleUriRequestAsync;
            MapTileSource httpTileSource = new MapTileSource(httpTileDataSource);
            MapControl1.TileSources.Add(httpTileSource);
...
        // Handle the UriRequested event.
        private async void HandleUriRequestAsync(HttpMapTileDataSource sender,
            MapTileUriRequestedEventArgs args)
        {
            // Get a deferral to do something asynchronously.
            // Omit this line if you don't have to do something asynchronously.
            var deferral = args.Request.GetDeferral();

            // Get the custom Uri.
            var uri = await GetCustomUriAsync(args.X, args.Y, args.ZoomLevel);

            // Specify the Uri in the Uri property of the MapTileUriRequest.
            args.Request.Uri = uri;

            // Notify the app that the custom Uri is ready.
            // Omit this line also if you don't have to do something asynchronously.
            deferral.Complete();
        }

        // Create the custom Uri.
        private async Task<Uri> GetCustomUriAsync(int x, int y, int zoomLevel)
        {
            // Do something asynchronously to create and return the custom Uri.        }
        }
```

## <a name="overlay-tiles-from-a-custom-source"></a>Наложение плиток из пользовательского источника

Наложение пользовательских плиток с помощью [**CustomMapTileDataSource**](https://msdn.microsoft.com/library/windows/apps/dn636983). Создайте плитки программным образом в памяти на лету или напишите собственный код для загрузки имеющихся плиток из другого источника.

Для создания или загрузки пользовательских плиток укажите пользовательский обработчик для события [**BitmapRequested**](https://msdn.microsoft.com/library/windows/apps/dn636984). Событие **BitmapRequested** вызывается для каждой отдельной плитки.

1.  Для создания или получения пользовательской плитки объедините в пользовательском обработчике для события [**BitmapRequested**](https://msdn.microsoft.com/library/windows/apps/dn636984) необходимые пользовательские аргументы со свойствами [**X**](https://msdn.microsoft.com/library/windows/apps/dn637135), [**Y**](https://msdn.microsoft.com/library/windows/apps/dn637136) и [**ZoomLevel**](https://msdn.microsoft.com/library/windows/apps/dn637137) класса [**MapTileBitmapRequestedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn637132).
2.  Верните пользовательскую плитку в свойстве [**PixelData**](https://msdn.microsoft.com/library/windows/apps/dn637140) класса [**MapTileBitmapRequest**](https://msdn.microsoft.com/library/windows/apps/dn637128), который содержится в свойстве [**Request**](https://msdn.microsoft.com/library/windows/apps/dn637134) класса [**MapTileBitmapRequestedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn637132). Свойство **PixelData** имеет тип [**IRandomAccessStreamReference**](https://msdn.microsoft.com/library/windows/apps/hh701664).

В следующем примере показано, как предоставить пользовательские плитки путем создания пользовательского обработчика для события **BitmapRequested**. В этом примере создаются идентичные красные частично непрозрачные плитки. В этом примере игнорируются свойства [**X**](https://msdn.microsoft.com/library/windows/apps/dn637135), [**Y**](https://msdn.microsoft.com/library/windows/apps/dn637136) и [**ZoomLevel**](https://msdn.microsoft.com/library/windows/apps/dn637137) класса [**MapTileBitmapRequestedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn637132). Несмотря на то, что это не реальный пример, в нем показано, как можно создавать пользовательские плитки в памяти на лету. В этом примере также показано, как реализовать шаблон откладывания, если необходимо выполнить действие асинхронно для создания настраиваемой плитки.

```csharp
using Windows.UI.Xaml.Controls.Maps;
using Windows.Storage.Streams;
using System.Threading.Tasks;
...
        CustomMapTileDataSource customDataSource = new CustomMapTileDataSource();
        // Attach a handler for the BitmapRequested event.
        customDataSource.BitmapRequested += customDataSource_BitmapRequestedAsync;
        customTileSource = new MapTileSource(customDataSource);
        MapControl1.TileSources.Add(customTileSource);
...
        // Handle the BitmapRequested event.
        private async void customDataSource_BitmapRequestedAsync(
            CustomMapTileDataSource sender,
            MapTileBitmapRequestedEventArgs args)
        {
            var deferral = args.Request.GetDeferral();
            args.Request.PixelData = await CreateBitmapAsStreamAsync();
            deferral.Complete();
        }

        // Create the custom tiles.
        // This example creates red tiles that are partially opaque.
        private async Task<RandomAccessStreamReference> CreateBitmapAsStreamAsync()
        {
            int pixelHeight = 256;
            int pixelWidth = 256;
            int bpp = 4;

            byte[] bytes = new byte[pixelHeight * pixelWidth * bpp];

            for (int y = 0; y < pixelHeight; y++)
            {
                for (int x = 0; x < pixelWidth; x++)
                {
                    int pixelIndex = y * pixelWidth + x;
                    int byteIndex = pixelIndex * bpp;

                    // Set the current pixel bytes.
                    bytes[byteIndex] = 0xff;        // Red
                    bytes[byteIndex + 1] = 0x00;    // Green
                    bytes[byteIndex + 2] = 0x00;    // Blue
                    bytes[byteIndex + 3] = 0x80;    // Alpha (0xff = fully opaque)
                }
            }

            // Create RandomAccessStream from byte array.
            InMemoryRandomAccessStream randomAccessStream =
                new InMemoryRandomAccessStream();
            IOutputStream outputStream = randomAccessStream.GetOutputStreamAt(0);
            DataWriter writer = new DataWriter(outputStream);
            writer.WriteBytes(bytes);
            await writer.StoreAsync();
            await writer.FlushAsync();
            return RandomAccessStreamReference.CreateFromStream(randomAccessStream);
        }
```

```cppwinrt
...
#include <winrt/Windows.Storage.Streams.h>
...
Windows::Foundation::IAsyncOperation<Windows::Storage::Streams::InMemoryRandomAccessStream> MainPage::CustomRandomAccessStream()
{
    constexpr int pixelHeight{ 256 };
    constexpr int pixelWidth{ 256 };
    constexpr int bpp{ 4 };

    std::array<uint8_t, pixelHeight * pixelWidth * bpp> bytes;

    for (int y = 0; y < pixelHeight; y++)
    {
        for (int x = 0; x < pixelWidth; x++)
        {
            int pixelIndex{ y * pixelWidth + x };
            int byteIndex{ pixelIndex * bpp };

            // Set the current pixel bytes.
            bytes[byteIndex] = (byte)(std::rand() % 256);        // Red
            bytes[byteIndex + 1] = (byte)(std::rand() % 256);    // Green
            bytes[byteIndex + 2] = (byte)(std::rand() % 256);    // Blue
            bytes[byteIndex + 3] = (byte)((std::rand() % 56) + 200);    // Alpha (0xff = fully opaque)
        }
    }

    // Create RandomAccessStream from byte array.
    Windows::Storage::Streams::InMemoryRandomAccessStream randomAccessStream;
    Windows::Storage::Streams::IOutputStream outputStream{ randomAccessStream.GetOutputStreamAt(0) };
    Windows::Storage::Streams::DataWriter writer{ outputStream };
    writer.WriteBytes(bytes);

    co_await writer.StoreAsync();
    co_await writer.FlushAsync();

    co_return randomAccessStream;
}
...
```

```cpp
InMemoryRandomAccessStream^ TileSources::CustomRandomAccessStream::get()
{
    int pixelHeight = 256;
    int pixelWidth = 256;
    int bpp = 4;

    Array<byte>^ bytes = ref new Array<byte>(pixelHeight * pixelWidth * bpp);

    for (int y = 0; y < pixelHeight; y++)
    {
        for (int x = 0; x < pixelWidth; x++)
        {
            int pixelIndex = y * pixelWidth + x;
            int byteIndex = pixelIndex * bpp;

            // Set the current pixel bytes.
            bytes[byteIndex] = (byte)(std::rand() % 256);        // Red
            bytes[byteIndex + 1] = (byte)(std::rand() % 256);    // Green
            bytes[byteIndex + 2] = (byte)(std::rand() % 256);    // Blue
            bytes[byteIndex + 3] = (byte)((std::rand() % 56) + 200);    // Alpha (0xff = fully opaque)
        }
    }

    // Create RandomAccessStream from byte array.
    InMemoryRandomAccessStream^ randomAccessStream = ref new InMemoryRandomAccessStream();
    IOutputStream^ outputStream = randomAccessStream->GetOutputStreamAt(0);
    DataWriter^ writer = ref new DataWriter(outputStream);
    writer->WriteBytes(bytes);

    create_task(writer->StoreAsync()).then([writer](unsigned int)
    {
        create_task(writer->FlushAsync());
    });

    return randomAccessStream;
}
```

## <a name="replace-the-default-map"></a>Замена карты по умолчанию

Чтобы полностью заменить карту по умолчанию сторонними или пользовательскими плитками, выполните следующие действия.

-   Укажите [**MapTileLayer**](https://msdn.microsoft.com/library/windows/apps/dn637143).**BackgroundReplacement** в качестве значения свойства [**Layer**](https://msdn.microsoft.com/library/windows/apps/dn637157) для [**MapTileSource**](https://msdn.microsoft.com/library/windows/apps/dn637144).
-   Укажите [**MapStyle**](https://msdn.microsoft.com/library/windows/apps/dn637127).**None** в качестве значения свойства [**Style**](https://msdn.microsoft.com/library/windows/apps/dn637051) для [**MapControl**](https://msdn.microsoft.com/library/windows/apps/dn637004).

## <a name="related-topics"></a>Ссылки по теме

* [Центр разработки Карт Bing](https://www.bingmapsportal.com/)
* [Пример карты UWP](http://go.microsoft.com/fwlink/p/?LinkId=619977)
* [Руководство по разработке карт](https://msdn.microsoft.com/library/windows/apps/dn596102)
* [Видео c конференции Build 2015: использование карт и местоположений в приложениях для Windows на телефонах, планшетах и ПК](https://channel9.msdn.com/Events/Build/2015/2-757)
* [Пример приложения UWP для работы с картами](http://go.microsoft.com/fwlink/p/?LinkId=619982)
