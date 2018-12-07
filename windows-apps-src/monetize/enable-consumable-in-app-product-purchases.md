---
Description: Offer consumable in-app products&\#8212;items that can be purchased, used, and purchased again&\#8212;through the Store commerce platform to provide your customers with a purchase experience that is both robust and reliable.
title: Поддержка покупки продуктов из приложения
ms.assetid: F79EE369-ACFC-4156-AF6A-72D1C7D3BDA4
keywords: UWP, потребляемые, надстройки, покупки из приложения, IAP, Windows.ApplicationModel.Store
ms.date: 08/25/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 5588558eff3e9c9b2954f0726995765a2862c43b
ms.sourcegitcommit: a3dc929858415b933943bba5aa7487ffa721899f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/07/2018
ms.locfileid: "8808439"
---
# <a name="enable-consumable-in-app-product-purchases"></a>Поддержка покупки продуктов из приложения

Предоставьте пользователям возможность покупки из приложения потребляемых внутренних продуктов приложения (товаров, которые можно покупать, использовать и покупать снова) через Магазин. Покупка из приложения — удобный и надежный способ приобрести товар. Это особенно удобно при покупке виртуальной валюты для игр (золота, монет и др.), которую можно потом использовать в процессе игры.

> [!IMPORTANT]
> В этой статье показано, как использовать элементы пространства имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx) для включения покупок потребляемых продуктов в приложении. Это пространство имен больше не дополняется новыми функциями, и мы рекомендуем вместо него использовать пространство имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx). Пространство имен **Windows.Services.Store** поддерживает новейшие типы надстроек, включая потребляемые надстройки, управляемые магазином и подписки, а его архитектура обеспечивает совместимость с будущими продуктами и компонентами, которые поддерживаются центром партнеров и магазин. Пространство имен **Windows.Services.Store** впервые появилось в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio. Дополнительные сведения о включении возможности покупки потребляемых продуктов из приложения с помощью пространства имен **Windows.Services.Store** см. в [этой статье](enable-consumable-add-on-purchases.md).

## <a name="prerequisites"></a>Что вам понадобится

-   В этом разделе рассказывается о покупках потребляемых внутренних продуктов приложения и отчетах об исполнении сделок в ваших приложениях. Чтобы ознакомиться с продуктами из приложения, см. статью [Поддержка покупок продуктов из приложения](enable-in-app-product-purchases.md), из которой вы узнаете о лицензировании и о том, как правильно вносить продукты из приложения в список Магазина.
-   Когда вы создадите код для продаж внутренних продуктов приложения и будете проверять его в первый раз, используйте объект [CurrentAppSimulator](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentAppSimulator) вместо объекта [CurrentApp](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp). В этом случае вы сможете проверить логику лицензирования путем имитации обращения к серверу лицензирования вместо вызова реального сервера. Для этого необходимо изменить файл с именем WindowsStoreProxy.xml в папке %userprofile%\\AppData\\local\\packages\\&lt;имя пакета&gt;\\LocalState\\Microsoft\\Windows Store\\ApiData. Имитатор Microsoft Visual Studio создает этот файл при первом запуске приложения. Также можно загрузить собственный его вариант во время выполнения. Дополнительные сведения см. в разделе [Использование файла WindowsStoreProxy.xml с CurrentAppSimulator](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md#proxy).
-   В этом разделе также приведены ссылки на примеры кода из статьи [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store). Этот пример дает отличную возможность поэкспериментировать с разными способами оплаты, доступными для приложений универсальной платформы Windows (UWP).

## <a name="step-1-making-the-purchase-request"></a>Шаг 1. Отправка запроса на покупку

Первоначальный запрос на покупку отправляется с помощью [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync), как и для любой покупки из приложения, выполняемой через Магазин. Отличие покупки потребляемых внутренних продуктов приложения состоит в том, что после покупки клиент не сможет снова приобрести тот же товар, пока Магазин не получит уведомления от приложения об успешном исполнении предыдущей покупки. За исполнение покупки потребляемых товаров и уведомление о сделке Магазина отвечает ваше приложение.

В следующем примере показан запрос на покупку потребляемого товара из приложения. В комментариях к коду указано, когда ваше приложение должно осуществить свою часть сделки по приобретению потребляемого внутреннего продукта приложения в двух разных сценариях— при успешном запросе и в случае сбоя запроса, из-за того что предыдущая покупка того же товара не выполнена.

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnableConsumablePurchases](./code/InAppPurchasesAndLicenses/cs/EnableConsumablePurchases.cs#MakePurchaseRequest)]

## <a name="step-2-tracking-local-fulfillment-of-the-consumable"></a>Шаг 2. Отслеживание выполнения покупки приложением

Предоставляя клиентам доступ к потребляемым внутренним продуктам приложения, важно отслеживать, какой товар поставляется (*productId*) и с какой транзакцией связывается эта поставка (*transactionId*).

> [!IMPORTANT]
> Ваше приложение отвечает за то, чтобы предоставить Магазину точный отчет об исполнении сделки. Это необходимо, чтобы сделка была честной и надежной.

Пример ниже демонстрирует, как использовать свойства [PurchaseResults](https://msdn.microsoft.com/library/windows/apps/dn263392) вызова [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync) из предыдущего этапа, чтобы указать, что покупка товара выполнена. Для сохранения сведений о товаре в месте, на которое впоследствии можно будет сослаться, чтобы подтвердить, что приложение выполнило свою часть сделки, используется коллекция.

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnableConsumablePurchases](./code/InAppPurchasesAndLicenses/cs/EnableConsumablePurchases.cs#GrantFeatureLocally)]

Следующий пример показывает, как использовать массив из предыдущего примера, чтобы получить доступ к паре кодов продукта и транзакции, которые в дальнейшем будут использоваться в отчетах о сделке для Магазина.

> [!IMPORTANT]
> Независимо от способа отслеживания и подтверждения сделки ваше приложение должно продемонстрировать добросовестность и доказать, что клиент получил тот товар, за который заплатил.

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnableConsumablePurchases](./code/InAppPurchasesAndLicenses/cs/EnableConsumablePurchases.cs#IsLocallyFulfilled)]

## <a name="step-3-reporting-product-fulfillment-to-the-store"></a>Шаг 3. Отправка в Магазин отчета об исполнении сделки

После выполнения приложением своей части сделки оно должно отправить вызов [ReportConsumableFulfillmentAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.reportconsumablefulfillmentasync), содержащий данные о товаре *productId* и соответствующей сделке.

> [!IMPORTANT]
> Если информация о выполненной покупке потребляемого внутреннего продукта приложения не будет передана в Магазин, пользователь не сможет снова купить тот же товар до поступления отчета о выполнении предыдущей сделки.

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnableConsumablePurchases](./code/InAppPurchasesAndLicenses/cs/EnableConsumablePurchases.cs#ReportFulfillment)]

## <a name="step-4-identifying-unfulfilled-purchases"></a>Шаг 4. Идентификация невыполненных сделок

С помощью метода [GetUnfulfilledConsumablesAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getunfulfilledconsumablesasync) приложение может в любой момент проверить невыполненные покупки потребляемых внутренних продуктов приложения. Этот метод следует вызывать регулярно, чтобы проверять наличие покупок потребляемых товаров, которые не были выполнены из-за непредвиденных событий приложения, таких как сетевые проблемы или завершение работы приложения.

Пример ниже показывает, как можно использовать [GetUnfulfilledConsumablesAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getunfulfilledconsumablesasync) для перечисления потребляемых товаров и как приложение может последовательно проходить по этому списку, чтобы завершить свою часть сделки.

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnableConsumablePurchases](./code/InAppPurchasesAndLicenses/cs/EnableConsumablePurchases.cs#GetUnfulfilledConsumables)]

## <a name="related-topics"></a>Связанные разделы

* [Поддержка покупки продуктов из приложения](enable-in-app-product-purchases.md)
* [Пример для Магазина (демонстрация пробных версий и покупок из приложения)](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store)
* [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/br225197)
 

 
