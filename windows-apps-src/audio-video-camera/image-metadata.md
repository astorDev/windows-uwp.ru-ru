---
author: laurenhughes
ms.assetid: D5D98044-7221-4C2A-9724-56E59F341AB0
description: В этой статье описано, как считывать и записывать свойства метаданных изображения, а также как добавить геотег к файлам с помощью служебного класса GeotagHelper.
title: Метаданные изображения
ms.author: lahugh
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: e69508ae558d93281e0170913fa0ccdc5891cc64
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.locfileid: "223854"
---
# <a name="image-metadata"></a>Метаданные изображения

\[ Обновлено для приложений UWP в Windows10. Статьи, касающиеся Windows8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этой статье описано, как считывать и записывать свойства метаданных изображения, а также как добавить геотег к файлам с помощью служебного класса [**GeotagHelper**](https://msdn.microsoft.com/library/windows/apps/dn903683).

## <a name="image-properties"></a>Свойства изображения

Свойство [**StorageFile.Properties**](https://msdn.microsoft.com/library/windows/apps/br227225) возвращает объект [**StorageItemContentProperties**](https://msdn.microsoft.com/library/windows/apps/hh770642), предоставляющий доступ к связанным с содержимым сведениям о файле. Чтобы получить свойства изображения, вызовите [**GetImagePropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/hh770646). Возвращенный объект [**ImageProperties**](https://msdn.microsoft.com/library/windows/apps/br207718) предоставляет члены, содержащие поля основных метаданных изображения, такие как название изображения и дата захвата.

[!code-cs[GetImageProperties](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetGetImageProperties)]

Для получения доступа к более широкому спектру метаданных файла используйте систему свойств Windows— набор свойств метаданных файла, которые можно получить с помощью уникального идентификатора строки. Создайте список строк и добавьте идентификаторы для всех свойств, которые требуется получить. Метод [**ImageProperties.RetrievePropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br207732) использует этот список строк и возвращает словарь пар "ключ-значение", где ключ— идентификатор свойства, а значение— значение свойства.

[!code-cs[GetWindowsProperties](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetGetWindowsProperties)]

-   Полный список свойств Windows, включая идентификаторы и типы для каждого свойства, см. в разделе [Свойства Windows](https://msdn.microsoft.com/library/windows/desktop/dd561977).

-   Некоторые свойства поддерживаются только для определенных контейнеров файлов и кодеков изображения. Список метаданных изображения, поддерживаемых для каждого типа изображения, см. в разделе [Политика метаданных фотографии](https://msdn.microsoft.com/library/windows/desktop/ee872003).

-   Поскольку неподдерживаемые свойства при получении могут возвращать значение NULL, обязательно проверьте это, прежде чем использовать возвращенное значение метаданных.

## <a name="geotag-helper"></a>GeotagHelper

GeotagHelper— это служебный класс, который помогает отмечать изображения с географическими данными, используя непосредственно API [**Windows.Devices.Geolocation**](https://msdn.microsoft.com/library/windows/apps/br225603), без необходимости анализировать или создавать формат метаданных вручную.

Если уже имеется объект [**Geopoint**](https://msdn.microsoft.com/library/windows/apps/dn263675), представляющий расположение, которое нужно отметить на изображении, после предыдущего использования API географического расположения или из другого источника, то вы можете задать данные геотега, вызвав метод [**GeotagHelper.SetGeotagAsync**](https://msdn.microsoft.com/library/windows/apps/dn903685) и передав [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171) и **Geopoint**.

[!code-cs[SetGeoDataFromPoint](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSetGeoDataFromPoint)]

Чтобы задать данные геотега с текущим расположением устройства, создайте новый объект [**Geolocator**](https://msdn.microsoft.com/library/windows/apps/br225534) и вызовите метод [**GeotagHelper.SetGeotagFromGeolocatorAsync**](https://msdn.microsoft.com/library/windows/apps/dn903686), передав **Geolocator** и файл, который нужно отметить.

[!code-cs[SetGeoDataFromGeolocator](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetSetGeoDataFromGeolocator)]

-   Необходимо включить возможность устройства **location** в манифест приложения для использования API [**SetGeotagFromGeolocatorAsync**](https://msdn.microsoft.com/library/windows/apps/dn903686).

-   Прежде чем вызвать [**RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn859152), необходимо вызвать [**SetGeotagFromGeolocatorAsync**](https://msdn.microsoft.com/library/windows/apps/dn903686), чтобы убедиться, что пользователь предоставил приложению разрешение использовать его расположение.

-   Дополнительные сведения об API географического расположения см. в разделе [Карты и расположение](https://msdn.microsoft.com/library/windows/apps/mt219699).

Чтобы получить объект GeoPoint, представляющий географическое расположение, отмеченное в файле изображения, вызовите метод [**GetGeotagAsync**](https://msdn.microsoft.com/library/windows/apps/dn903684).

[!code-cs[GetGeoData](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetGetGeoData)]

## <a name="decode-and-encode-image-metadata"></a>Декодирование и кодирование метаданных изображения

Наиболее современный способ работы с данными изображения — чтение и запись свойств на уровне потока с помощью [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/br226176) или [BitmapEncoder](bitmapencoder-options-reference.md). Для этих операций можно использовать свойства Windows, чтобы указать данные для чтения или записи. Однако вы также можете использовать язык запросов метаданных, предоставленный компонентом обработки изображений Windows (WIC), чтобы указать путь к запрашиваемому свойству.

Для чтения метаданных изображения с помощью этого метода требуется наличие [**BitmapDecoder**](https://msdn.microsoft.com/library/windows/apps/br226176), созданного с использованием потока файла исходного изображения. Сведения о том, как это сделать, см. в разделе [Обработка изображений](imaging.md).

Получив декодер, создайте список строк и добавьте новые записи для всех свойств метаданных, которые требуется получить, используя строку идентификатора свойства Windows или запрос метаданных WIC. Вызовите метод [**BitmapPropertiesView.GetPropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br226250) в члене декодера [**BitmapProperties**](https://msdn.microsoft.com/library/windows/apps/br226248) для запроса указанных свойств. Свойства возвращаются в словаре пар "ключ-значение", в котором содержатся имя свойства или путь и значение свойства.

[!code-cs[ReadImageMetadata](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetReadImageMetadata)]

-   Подробные сведения о поддерживаемых свойствах и языке запросов метаданных WIC см. в разделе [Запросы собственных метаданных формата изображений WIC](https://msdn.microsoft.com/library/windows/desktop/ee719904).

-   Многие свойства метаданных поддерживаются только некоторыми типами изображений. Вызов метода [**GetPropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br226250) приведет к сбою с кодом ошибки 0x88982F41, если одно из запрашиваемых свойств не поддерживается изображением, связанным с декодером, и с кодом ошибки 0x88982F81, если изображение полностью не поддерживает метаданные. Константы, связанные с этими кодами ошибок,— это WINCODEC\_ERR\_PROPERTYNOTSUPPORTED и WINCODEC\_ERR\_UNSUPPORTEDOPERATION. Они определены в файле заголовка winerror.h.
-   Так как изображение может не содержать значение для указанного свойства, используйте **IDictionary.ContainsKey**, чтобы проверить, присутствует ли свойство в результатах, прежде чем получить доступ к нему.

Для записи метаданных изображения в поток требуется **BitmapEncoder**, связанный с файлом выходного изображения.

Создайте объект [**BitmapPropertySet**](https://msdn.microsoft.com/library/windows/apps/hh974338), который будет содержать необходимые значения свойств. Создайте объект [**BitmapTypedValue**](https://msdn.microsoft.com/library/windows/apps/hh700687), который будет представлять значение свойства. Этот объект использует **object** в качестве значения и члена перечисления [**PropertyType**](https://msdn.microsoft.com/library/windows/apps/br225871), определяющего тип значения. Добавьте **BitmapTypedValue** в **BitmapPropertySet**, а затем вызовите [**BitmapProperties.SetPropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br226252), чтобы кодировщик записал свойства в поток.

[!code-cs[WriteImageMetadata](./code/ImagingWin10/cs/MainPage.xaml.cs#SnippetWriteImageMetadata)]

-   Дополнительные сведения о том, какие свойства поддерживаются для определенных типов файлов изображений, см. в разделах [Свойства Windows](https://msdn.microsoft.com/library/windows/desktop/dd561977), [Политика метаданных фотографии](https://msdn.microsoft.com/library/windows/desktop/ee872003) и [Запросы собственных метаданных формата изображений WIC](https://msdn.microsoft.com/library/windows/desktop/ee719904).

-   Вызов метода [**SetPropertiesAsync**](https://msdn.microsoft.com/library/windows/apps/br226252) приведет к сбою с кодом ошибки 0x88982F41, если одно из запрашиваемых свойств не поддерживается изображением, связанным с кодировщиком.

## <a name="related-topics"></a>Еще по теме

* [Обработка изображений](imaging.md)
 

 




