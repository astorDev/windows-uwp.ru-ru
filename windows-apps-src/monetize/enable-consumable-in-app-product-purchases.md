---
author: mcleanbyron
Description: "Предоставьте пользователям возможность покупки из приложения потребляемых внутренних продуктов приложения (товаров, которые можно покупать, использовать и покупать снова) через Магазин. Покупка из приложения — удобный и надежный способ приобрести товар."
title: "Поддержка покупки потребляемых внутренних продуктов приложения"
ms.assetid: F79EE369-ACFC-4156-AF6A-72D1C7D3BDA4
keywords: in-app offer code sample
translationtype: Human Translation
ms.sourcegitcommit: 36bc5dcbefa6b288bf39aea3df42f1031f0b43df
ms.openlocfilehash: 25f09e043d61f1705f9f0a4fa34114fd06166fa4

---

# Поддержка покупки потребляемых внутренних продуктов приложения


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Предоставьте пользователям возможность покупки из приложения потребляемых внутренних продуктов приложения (товаров, которые можно покупать, использовать и покупать снова) через Магазин. Покупка из приложения — удобный и надежный способ приобрести товар. Он особенно удобен при покупке виртуальной валюты для игр (например, золота или монет), которую можно потом использовать в процессе игры.

## Необходимые условия

-   В этом разделе рассказывается о покупках потребляемых внутренних продуктов приложения и отчетах об исполнении сделок в ваших приложениях. Чтобы ознакомиться с продуктами из приложения, см. статью [Поддержка покупок продуктов из приложения](enable-in-app-product-purchases.md), из которой вы узнаете о лицензировании и о том, как правильно вносить продукты из приложения в список Магазина.
-   Когда вы создадите код для продаж внутренних продуктов приложения и будете проверять его в первый раз, используйте объект [**CurrentAppSimulator**](https://msdn.microsoft.com/library/windows/apps/hh779766) вместо объекта [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765). В этом случае вы сможете проверить логику лицензирования путем имитации обращения к серверу лицензирования вместо вызова реального сервера. Для этого необходимо изменить файл с именем WindowsStoreProxy.xml. в папке %userprofile%\\AppData\\local\\packages\\&lt;package name&gt;\\LocalState\\Microsoft\\Windows Store\\ApiData. Имитатор Microsoft Visual Studio создает этот файл при первом запуске приложения. Также можно загрузить собственный его вариант во время выполнения. Подробнее см. в статье **CurrentAppSimulator**.
-   В этом разделе также приведены ссылки на примеры кода из статьи [Пример для Магазина](http://go.microsoft.com/fwlink/p/?LinkID=627610). Этот пример дает отличную возможность поэкспериментировать с разными способами оплаты, доступными для приложений универсальной платформы Windows (UWP).

## Шаг 1. Отправка запроса на покупку

Первоначальный запрос на покупку отправляется с помощью [**RequestProductPurchaseAsync**](https://msdn.microsoft.com/library/windows/apps/dn263381), как и для любой покупки из приложения, выполняемой через Магазин. Отличие покупки потребляемых внутренних продуктов приложения состоит в том, что после покупки клиент не сможет снова приобрести тот же товар, пока Магазин не получит уведомления от приложения об успешном исполнении предыдущей покупки. За исполнение покупки потребляемых товаров и уведомление о сделке Магазина отвечает ваше приложение.

В следующем примере показан запрос на покупку потребляемого товара из приложения. В комментариях к коду указано, когда ваше приложение должно осуществить свою часть сделки по приобретению потребляемого внутреннего продукта приложения в двух разных сценариях — при успешном запросе и в случае сбоя запроса, из-за того что предыдущая покупка того же товара не выполнена.

```CSharp
PurchaseResults purchaseResults = await CurrentAppSimulator.RequestProductPurchaseAsync("product1");
switch (purchaseResults.Status)
{
    case ProductPurchaseStatus.Succeeded:
        product1TempTransactionId = purchaseResults.TransactionId;

        // Grant the user their purchase here, and then pass the product ID and transaction ID to currentAppSimulator.reportConsumableFulfillment
        // To indicate local fulfillment to the Windows Store.
        break;

    case ProductPurchaseStatus.NotFulfilled:
        product1TempTransactionId = purchaseResults.TransactionId;

        // First check for unfulfilled purchases and grant any unfulfilled purchases from an earlier transaction.
        // Once products are fulfilled pass the product ID and transaction ID to currentAppSimulator.reportConsumableFulfillment
        // To indicate local fulfillment to the Windows Store.
        break;
}
```

## Шаг 2. Отслеживание выполнения покупки приложением

Предоставляя клиентам доступ к потребляемым внутренним продуктам приложения, важно отслеживать, какой товар поставляется (*productId*) и с какой транзакцией связывается эта поставка (*transactionId*).

**Важно!** Ваше приложение отвечает за предоставление в Магазин точного отчета об исполнении сделки. Это необходимо, чтобы сделка была честной и надежной.

Пример ниже демонстрирует, как использовать свойства [**PurchaseResults**](https://msdn.microsoft.com/library/windows/apps/dn263392) вызова [**RequestProductPurchaseAsync**](https://msdn.microsoft.com/library/windows/apps/dn263381) из предыдущего этапа, чтобы указать, что покупка товара выполнена. Для сохранения сведений о товаре в месте, на которое впоследствии можно будет сослаться, чтобы подтвердить, что приложение выполнило свою часть сделки, используется массив.

```CSharp
private void GrantFeatureLocally(string productId, Guid transactionId)
{
    if (!grantedConsumableTransactionIds.ContainsKey(productId))
    {
        grantedConsumableTransactionIds.Add(productId, new List<Guid>());
    }
    grantedConsumableTransactionIds[productId].Add(transactionId);

    // Grant the user their content. You will likely increase some kind of gold/coins/some other asset count.
}
```

Следующий пример показывает, как использовать массив из предыдущего примера, чтобы получить доступ к паре кодов продукта и транзакции, которые в дальнейшем будут использоваться в отчетах о сделке для Магазина.

**Важно!** Независимо от способа отслеживания и подтверждения сделки приложение должно продемонстрировать честный подход и доказать, что клиент платит только за тот товар, за который он заплатил.

```CSharp
private Boolean IsLocallyFulfilled(string productId, Guid transactionId)
{
    return grantedConsumableTransactionIds.ContainsKey(productId) && grantedConsumableTransactionIds[productId].Contains(transactionId);
}
```

## Шаг 3. Отправка в Магазин отчета об исполнении сделки

После выполнения приложением своей части сделки оно должно отправить вызов [**ReportConsumableFulfillmentAsync**](https://msdn.microsoft.com/library/windows/apps/dn263380), содержащий данные о товаре *productId* и соответствующей сделке.

**Важно!** Если информация о выполненной покупке потребляемого внутреннего продукта приложения не будет передана в Магазин, пользователь не сможет снова купить тот же товар до поступления отчета о выполнении предыдущей сделки.

```CSharp
FulfillmentResult result = await CurrentAppSimulator.ReportConsumableFulfillmentAsync("product2", product2TempTransactionId);
```

## Шаг 4. Идентификация невыполненных сделок

С помощью метода [**GetUnfulfilledConsumablesAsync**](https://msdn.microsoft.com/library/windows/apps/dn263379) приложение может в любой момент проверить невыполненные покупки потребляемых внутренних продуктов приложения. Этот метод следует вызывать регулярно, чтобы проверять наличие покупок потребляемых товаров, которые не были выполнены из-за непредвиденных событий приложения, таких как сетевые проблемы или завершение работы приложения.

Пример ниже показывает, как можно использовать [**GetUnfulfilledConsumablesAsync**](https://msdn.microsoft.com/library/windows/apps/dn263379) для перечисления потребляемых товаров и как приложение может последовательно проходить по этому списку, чтобы завершить свою часть сделки.

```CSharp
private async void GetUnfulfilledConsumables()
{
    products = await CurrentApp.GetUnfulfilledConsumablesAsync();

    foreach (UnfulfilledConsumable product in products)
    {
        logMessage += "\nProduct Id: " + product.ProductId + " Transaction Id: " + product.TransactionId;
        // This is where you would pass the product ID and transaction ID to currentAppSimulator.reportConsumableFulfillment
    // To indicate local fulfillment to the Windows Store.
    }
}
```

## Связанные разделы

* [Поддержка покупок внутренних продуктов приложения](enable-in-app-product-purchases.md)
* [Пример для Магазина (демонстрация пробных версий и покупок из приложения)](http://go.microsoft.com/fwlink/p/?LinkID=627610)
* [**Windows.ApplicationModel.Store**](https://msdn.microsoft.com/library/windows/apps/br225197)
 

 







<!--HONumber=Jun16_HO4-->


