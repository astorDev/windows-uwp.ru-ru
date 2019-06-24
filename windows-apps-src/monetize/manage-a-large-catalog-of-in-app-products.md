---
ms.assetid: 5E722AFF-539D-456E-8C4A-ADE90CF7674A
description: Если ваше приложение предлагает большой каталог внутренних продуктов приложения, то для облегчения управления каталогом вы можете выполнить действия, описанные в этом разделе.
title: Управление большим каталогом внутренних продуктов приложения
ms.date: 08/25/2017
ms.topic: article
keywords: Windows 10, UWP, покупки из приложения, IAP, надстройки, каталог, Windows.ApplicationModel.Store
ms.localizationpriority: medium
ms.openlocfilehash: e297c01aa19449691e775c4d7d2c33028b234cfa
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67320184"
---
# <a name="manage-a-large-catalog-of-in-app-products"></a>Управление большим каталогом внутренних продуктов приложения

Если ваше приложение предлагает большой каталог внутренних продуктов приложения, то для облегчения управления каталогом вы можете выполнить действия, описанные в этом разделе. В выпусках до Windows 10 в Магазине действовало ограничение в 200 продуктов на одну учетную запись разработчика. Процесс, описанный в этом разделе, можно использовать для обхода этого ограничения. Начиная с Windows 10, Store не имеет ограничения по количеству записей продуктов на одну учетную запись разработчика, и, как описано в этой статье больше не требуется.

> [!IMPORTANT]
> В этой статье показано, как использовать члены пространства имен [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store). Это пространство имен больше не дополняется новыми функциями, и мы рекомендуем вместо него использовать пространство имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store). **Windows.Services.Store** пространство имен поддерживает последние надстройки типы, например управляемые Store пригодных для использования надстроек и подписки и должна быть совместима с типами будущих продуктов и функций, поддерживаемых партнера Центр и Store. Пространство имен **Windows.Services.Store** впервые появилось в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md).

Чтобы включить эту возможность, можно создать множество позиций продуктов для определенных ценовых категорий. Каждая из этих позиций может представлять сотни продуктов в каталоге. Для поддержки этой возможности используйте перегрузку метода [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync). Она позволяет покупателю воспользоваться предложением, определенным непосредственно в приложении и связанным с внутренним продуктом приложения, реализующим продажу из приложения, в Магазине. Помимо указания связи между предложением и продуктовой позицией во время вызова, ваше приложение должно передать объект [ProductPurchaseDisplayProperties](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.ProductPurchaseDisplayProperties), содержащий информацию о предложении из большого каталога. Если эти данные не предоставлены, будет использоваться информация для выставленной продуктовой позиции.

Магазин будет использовать идентификатор *offerId* из запроса на покупку только в итоговом классе [PurchaseResults](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.PurchaseResults). Этот процесс напрямую не меняет информацию, изначально указанную при [выставлении внутреннего продукта приложения в Магазине](../publish/add-on-submissions.md).

## <a name="prerequisites"></a>предварительные требования

-   В этом разделе рассказывается о поддержке представления нескольких предложений для продажи из приложения с использованием одной продуктовой позиции, выставленной в Магазине. Чтобы ознакомиться с покупками из приложения, см. статью [Поддержка покупок внутренних продуктов приложения](enable-in-app-product-purchases.md), из которой вы узнаете о лицензировании и о том, как правильно вносить покупки из приложения в список Магазина.
-   Когда вы создадите код для продаж из приложения и будете проверять его в первый раз, используйте объект [CurrentAppSimulator](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator) вместо объекта [CurrentApp](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp). В этом случае вы сможете проверить логику лицензирования путем имитации обращения к серверу лицензирования вместо вызова реального сервера. Чтобы сделать это, необходимо настроить файл с именем WindowsStoreProxy.xml в папку % userprofile %\\AppData\\локального\\пакетов\\&lt;имя пакета&gt;\\LocalState\\ Microsoft\\Windows Store\\ApiData. Имитатор Microsoft Visual Studio создает этот файл при первом запуске приложения. Также можно загрузить собственный его вариант во время выполнения. Дополнительные сведения см. в разделе [Использование файла WindowsStoreProxy.xml с CurrentAppSimulator](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md#proxy).
-   В этом разделе также приведены ссылки на примеры кода из статьи [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store). Этот пример дает отличную возможность поэкспериментировать с разными вариантами монетизации, доступными для приложений универсальной платформы Windows (UWP).

## <a name="make-the-purchase-request-for-the-in-app-product"></a>Запрос на покупку для продукта из приложения

Запрос на покупку для конкретного продукта в большом каталоге обрабатывается во многом так же, как любой другой запрос на покупку в приложении. Когда ваше приложение вызывает новую перегрузку метода [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync), оно предоставляет идентификатор *OfferId* и объект [ProductPurchaseDisplayProperties](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.productpurchasedisplayproperties) с уже указанным наименованием внутреннего продукта приложения.

> [!div class="tabbedCodeSnippets"]
[!code-csharp[ManageCatalog](./code/InAppPurchasesAndLicenses/cs/ManageCatalog.cs#MakePurchaseRequest)]

## <a name="report-fulfillment-of-the-in-app-offer"></a>Сообщение о выполнении продажи из приложения

Ваше приложение должно сообщить в Магазин о продаже продуктовой позиции сразу после локального выполнения продажи предложения. При использовании большого каталога пользователь не сможет совершать покупки предложений из приложения, используя ту же продуктовую позицию Магазина, если ваше приложение не сообщит о выполнении продажи предложения.

Как уже говорилось, Магазин использует предоставленные данные о предложении только для заполнения класса [PurchaseResults](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.PurchaseResults) и не создает постоянную связь между предложением из большого каталога и продуктовой позицией в Магазине. В результате вы должны отслеживать права пользователя на продукты из большого каталога и предоставлять пользователю контекст, относящийся к продукту (например, название приобретаемого товара или информацию о нем), вне операции [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync).

Приведенный ниже код демонстрирует вызов функции выполнения и шаблон сообщения пользовательского интерфейса, в который вставлена информация о конкретном предложении. Поскольку эта информация отсутствует, в примере используются данные из [ListingInformation](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.ListingInformation) продуктовой позиции.

> [!div class="tabbedCodeSnippets"]
[!code-csharp[ManageCatalog](./code/InAppPurchasesAndLicenses/cs/ManageCatalog.cs#ReportFulfillment)]

## <a name="related-topics"></a>См. также

* [Поддержка покупки внутренних продуктов приложений](enable-in-app-product-purchases.md)
* [Поддержка покупки потребляемых внутренних продуктов приложений](enable-consumable-in-app-product-purchases.md)
* [Пример Store (демонстрирует пробные версии и покупки из приложений)](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store)
* [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync)
* [ProductPurchaseDisplayProperties](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.ProductPurchaseDisplayProperties)
