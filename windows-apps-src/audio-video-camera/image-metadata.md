---
ms.assetid: D5D98044-7221-4C2A-9724-56E59F341AB0
description: В этой статье описано, как считывать и записывать свойства метаданных изображения, а также как добавить геотег к файлам с помощью служебного класса GeotagHelper.
title: Метаданные изображения
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 589dd40282fc3186f225e3873295863cc41b6a0c
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66361708"
---
# <a name="image-metadata"></a>Метаданные изображения



В этой статье описано, как считывать и записывать свойства метаданных изображения, а также как добавить геотег к файлам с помощью служебного класса [**GeotagHelper**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileProperties.GeotagHelper).

## <a name="image-properties"></a>Свойства образа

Свойство [**StorageFile.Properties**](https://docs.microsoft.com/uwp/api/windows.storage.storagefile.properties) возвращает объект [**StorageItemContentProperties**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileProperties.StorageItemContentProperties), предоставляющий доступ к связанным с содержимым сведениям о файле. Чтобы получить свойства изображения, вызовите [**GetImagePropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.storageitemcontentproperties.getimagepropertiesasync). Возвращенный объект [**ImageProperties**](https://docs.microsoft.com/uwp/api/Windows.Storage.FileProperties.ImageProperties) предоставляет члены, содержащие поля основных метаданных изображения, такие как название изображения и дата захвата.

[!code-cs[GetImageProperties](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetGetImageProperties)]

Для получения доступа к более широкому спектру метаданных файла используйте систему свойств Windows — набор свойств метаданных файла, которые можно получить с помощью уникального идентификатора строки. Создайте список строк и добавьте идентификаторы для всех свойств, которые требуется получить. Метод [**ImageProperties.RetrievePropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.imageproperties.retrievepropertiesasync) использует этот список строк и возвращает словарь пар "ключ-значение", где ключ — идентификатор свойства, а значение — значение свойства.

[!code-cs[GetWindowsProperties](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetGetWindowsProperties)]

-   Полный список свойств Windows, включая идентификаторы и типы для каждого свойства, см. в разделе [Свойства Windows](https://docs.microsoft.com/windows/desktop/properties/props).

-   Некоторые свойства поддерживаются только для определенных контейнеров файлов и кодеков изображения. Список метаданных изображения, поддерживаемых для каждого типа изображения, см. в разделе [Политика метаданных фотографии](https://docs.microsoft.com/windows/desktop/wic/photo-metadata-policies).

-   Поскольку неподдерживаемые свойства при получении могут возвращать значение Null, обязательно проверьте наличие такого значения, прежде чем использовать возвращенное значение метаданных.

## <a name="geotag-helper"></a>GeotagHelper

GeotagHelper — это служебный класс, который помогает отмечать изображения с географическими данными, используя непосредственно API [**Windows.Devices.Geolocation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation), без необходимости анализировать или создавать формат метаданных вручную.

Если уже имеется объект [**Geopoint**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geopoint), представляющий расположение, которое нужно отметить на изображении, после предыдущего использования API географического расположения или из другого источника, то вы можете задать данные геотега, вызвав метод [**GeotagHelper.SetGeotagAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.geotaghelper.setgeotagasync) и передав [**StorageFile**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageFile) и **Geopoint**.

[!code-cs[SetGeoDataFromPoint](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSetGeoDataFromPoint)]

Чтобы задать данные геотега с текущим расположением устройства, создайте новый объект [**Geolocator**](https://docs.microsoft.com/uwp/api/Windows.Devices.Geolocation.Geolocator) и вызовите метод [**GeotagHelper.SetGeotagFromGeolocatorAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.geotaghelper.setgeotagfromgeolocatorasync), передав **Geolocator** и файл, который нужно отметить.

[!code-cs[SetGeoDataFromGeolocator](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSetGeoDataFromGeolocator)]

-   Необходимо включить возможность устройства **location** в манифест приложения для использования API [**SetGeotagFromGeolocatorAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.geotaghelper.setgeotagfromgeolocatorasync).

-   Прежде чем вызвать [**RequestAccessAsync**](https://docs.microsoft.com/uwp/api/windows.devices.geolocation.geolocator.requestaccessasync), необходимо вызвать [**SetGeotagFromGeolocatorAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.geotaghelper.setgeotagfromgeolocatorasync), чтобы убедиться, что пользователь предоставил приложению разрешение использовать его расположение.

-   Дополнительные сведения об API географического расположения см. в разделе [Карты и расположение](https://docs.microsoft.com/windows/uwp/maps-and-location/index).

Чтобы получить объект GeoPoint, представляющий географическое расположение, отмеченное в файле изображения, вызовите метод [**GetGeotagAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.geotaghelper.getgeotagasync).

[!code-cs[GetGeoData](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetGetGeoData)]

## <a name="decode-and-encode-image-metadata"></a>Декодирование и кодирование метаданных изображения

Наиболее современный способ работы с данными изображения — чтение и запись свойств на уровне потока с помощью [**BitmapDecoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder) или [BitmapEncoder](bitmapencoder-options-reference.md). Для этих операций можно использовать свойства Windows, чтобы указать данные для чтения или записи. Однако вы также можете использовать язык запросов метаданных, предоставленный компонентом обработки изображений Windows (WIC), чтобы указать путь к запрашиваемому свойству.

Для чтения метаданных изображения с помощью этого метода требуется наличие [**BitmapDecoder**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapDecoder), созданного с использованием потока файла исходного изображения. Сведения о том, как это сделать, см. в разделе [Обработка изображений](imaging.md).

Получив декодер, создайте список строк и добавьте новые записи для всех свойств метаданных, которые требуется получить, используя строку идентификатора свойства Windows или запрос метаданных WIC. Вызовите метод [**BitmapPropertiesView.GetPropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmappropertiesview.getpropertiesasync) в члене декодера [**BitmapProperties**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapProperties) для запроса указанных свойств. Свойства возвращаются в словаре пар "ключ-значение", в котором содержатся имя свойства или путь и значение свойства.

[!code-cs[ReadImageMetadata](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetReadImageMetadata)]

-   Подробные сведения о поддерживаемых свойствах и языке запросов метаданных WIC см. в разделе [Запросы собственных метаданных формата изображений WIC](https://docs.microsoft.com/windows/desktop/wic/-wic-native-image-format-metadata-queries).

-   Многие свойства метаданных поддерживаются только некоторыми типами изображений. [**GetPropertiesAsync** ](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmappropertiesview.getpropertiesasync) завершится ошибкой с кодом ошибки 0x88982F41, если один из обязательных свойств не поддерживается для образа, связанный с декодером и 0x88982F81, если изображение не поддерживает метаданные вообще. Используются следующие константы, связанное с этими кодами ошибок WINCODEC\_ERR\_PROPERTYNOTSUPPORTED и WINCODEC\_ERR\_UNSUPPORTEDOPERATION и определяются в файле заголовка winerror.h.
-   Так как изображение может не содержать значение для указанного свойства, используйте **IDictionary.ContainsKey**, чтобы проверить, присутствует ли свойство в результатах, прежде чем получить доступ к нему.

Для записи метаданных изображения в поток требуется **BitmapEncoder**, связанный с файлом выходного изображения.

Создайте объект [**BitmapPropertySet**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapPropertySet), который будет содержать необходимые значения свойств. Создайте объект [**BitmapTypedValue**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Imaging.BitmapTypedValue), который будет представлять значение свойства. Этот объект использует **object** в качестве значения и члена перечисления [**PropertyType**](https://docs.microsoft.com/uwp/api/Windows.Foundation.PropertyType), определяющего тип значения. Добавьте **BitmapTypedValue** в **BitmapPropertySet**, а затем вызовите [**BitmapProperties.SetPropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapproperties.setpropertiesasync), чтобы кодировщик записал свойства в поток.

[!code-cs[WriteImageMetadata](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetWriteImageMetadata)]

-   Дополнительные сведения о том, какие свойства поддерживаются для определенных типов файлов изображений, см. в разделах [Свойства Windows](https://docs.microsoft.com/windows/desktop/properties/props), [Политика метаданных фотографии](https://docs.microsoft.com/windows/desktop/wic/photo-metadata-policies) и [Запросы собственных метаданных формата изображений WIC](https://docs.microsoft.com/windows/desktop/wic/-wic-native-image-format-metadata-queries).

-   [**SetPropertiesAsync** ](https://docs.microsoft.com/uwp/api/windows.graphics.imaging.bitmapproperties.setpropertiesasync) завершится ошибкой с кодом ошибки 0x88982F41, если один из обязательных свойств не поддерживаются изображения, связанного с помощью кодировщика.

## <a name="related-topics"></a>См. также

* [Создание образов](imaging.md)
 

 




