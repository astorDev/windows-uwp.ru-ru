---
description: Описание расширенной схемы данных JSON для продуктов из Магазина в пространстве имен Windows.Services.Store.
title: Схемы данных для продуктов из Магазина
ms.date: 09/26/2017
ms.topic: article
keywords: windows 10, uwp, ExtendedJsonData, продукты Store, схема
ms.localizationpriority: medium
ms.openlocfilehash: 8f51f0fffae3fa8e9a54214f78aa93fe39eab080
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8208737"
---
# <a name="data-schemas-for-store-products"></a>Схемы данных для продуктов из Store

При отправке в Магазин какого-либо продукта, например приложения или надстройки, Магазин поддерживает полный набор данных для продукта и его лицензий. В коде вашего приложения вы может программным способом получить доступ к некоторым из этих данных с помощью свойств в пространстве имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx). Например, вы можете получить описание и цену текущего приложения или надстройки для текущего приложения с помощью свойств [StoreProduct.Description](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.Description) и [StoreProduct.Price](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.Price).

Тем не менее, значительная часть данных для продуктов в Магазине не обеспечивает доступ с помощью предопределенных свойств в пространстве имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx). Чтобы получить доступ ко всем данным продукта в коде, вместо этого можно использовать следующие общие свойства:

* [StoreProduct.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.ExtendedJsonData)
* [StoreSku.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storesku.ExtendedJsonData)
* [StoreAvailability.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability.ExtendedJsonData)
*   [StoreCollectionData.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storecollectiondata.ExtendedJsonData)
*   [StoreAppLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.ExtendedJsonData)
* [StoreLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storelicense.ExtendedJsonData)
*   [StorePurchaseProperties.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storepurchaseproperties.ExtendedJsonData)

Эти свойства возвращают полный набор данных для соответствующего объекта в строке формата JSON. В этой статье представлена полная схема данных, возвращаемых этими свойствами.

> [!NOTE]
> Продукты в Магазине упорядочены в виде иерархии из объектов: [продукт](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct), [SKU](https://docs.microsoft.com/uwp/api/windows.services.store.storesku) и [доступность](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability). Дополнительные сведения см. в разделе [Продукты, SKU и доступности](in-app-purchases-and-trials.md#products-skus).

## <a name="schema-for-storeproduct-storesku-storeavailability-and-storecollectiondata"></a>Схема для StoreProduct, StoreSku, StoreAvailability и StoreCollectionData

Следующая схема содержит строку в формате JSON, возвращаемую свойством [StoreProduct.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.ExtendedJsonData). Свойства [StoreSku.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storesku.ExtendedJsonData), [StoreAvailability.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability.ExtendedJsonData) и [StoreCollectionData.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storecollectiondata.ExtendedJsonData) возвращают только те части этой схемы, которые определены в разделах пути ```DisplaySkuAvailabilities\Sku```, ```DisplaySkuAvailabilities\Availabilities``` и ```DisplaySkuAvailabilities\Sku\CollectionData```, соответственно.

Пример строки формата JSON, возвращаемой свойством [StoreProduct.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.ExtendedJsonData), см. [в этом разделе](#product-example).

[!code[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StoreProduct.ExtendedJsonData.json#L1-L729)]

<span id="product-example" />

### <a name="example"></a>Пример

В следующем примере показана строка формата JSON, возвращенная свойством [StoreProduct.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.ExtendedJsonData) для приложения.

[!code[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StoreProduct.ExtendedJsonDataExample.json#L1-L268)]

## <a name="schema-for-storeapplicense-and-storelicense"></a>Схема для StoreAppLicense и StoreLicense

Следующая схема содержит строку в формате JSON, возвращаемую свойством [StoreAppLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.ExtendedJsonData). Свойство [StoreLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storelicense.ExtendedJsonData) возвращает только те части схемы, которые определены в разделе пути ```productAddOns```.

Пример строки формата JSON, возвращаемой свойством [StoreAppLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.ExtendedJsonData), см. [в этом разделе](#license-example).

[!code[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StoreAppLicense.ExtendedJsonData.json#L1-L80)]

<span id="license-example" />

### <a name="example"></a>Пример

В следующем примере показана строка формата JSON, возвращенная свойством [StoreAppLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.ExtendedJsonData) для приложения.

[!code[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StoreAppLicense.ExtendedJsonDataExample.json#L1-L28)]

## <a name="schema-for-storepurchaseproperties"></a>Схема для StorePurchaseProperties

Следующая схема содержит строку в формате JSON, возвращаемую свойством [StorePurchaseProperties.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storepurchaseproperties.ExtendedJsonData).

[!code[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StorePurchaseProperties.ExtendedJsonData.json#L1-L12)]

## <a name="related-topics"></a>Еще по теме:

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md)
* [Получение информации о лицензии для приложений и надстроек](get-license-info-for-apps-and-add-ons.md)
* [Поддержка покупок приложений и надстроек внутри приложения](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Поддержка покупок потребляемых надстроек внутри приложения](enable-consumable-add-on-purchases.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
