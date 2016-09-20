---
author: mcleanbyron
ms.assetid: 5E722AFF-539D-456E-8C4A-ADE90CF7674A
description: "Если ваше приложение предлагает большой каталог внутренних продуктов приложения, то для облегчения управления каталогом вы можете выполнить действия, описанные в этом разделе."
title: "Управление большим каталогом внутренних продуктов приложения"
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 0927df3cd696e5a6fbd3a235d2b87074f1d63929

---

# Управление большим каталогом внутренних продуктов приложения


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Если ваше приложение предлагает большой каталог внутренних продуктов приложения, то для облегчения управления каталогом вы можете выполнить действия, описанные в этом разделе. Вы сможете создать множество позиций продуктов для определенных ценовых категорий. Каждая из этих позиций может представлять сотни продуктов в каталоге.

Для поддержки этой возможности используйте перегрузку метода [**RequestProductPurchaseAsync**](https://msdn.microsoft.com/library/windows/apps/dn263382). Она позволяет покупателю воспользоваться предложением, определенным непосредственно в приложении и связанным с внутренним продуктом приложения, выставленным в Магазине. Помимо указания связи между предложением и продуктовой позицией во время вызова, ваше приложение должно передать объект [**ProductPurchaseDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/dn263384), содержащий информацию о предложении из большого каталога. Если эти данные не предоставлены, будет использоваться информация для выставленной продуктовой позиции.

Магазин будет использовать идентификатор *offerId* из запроса на покупку только в итоговом классе [**PurchaseResults**](https://msdn.microsoft.com/library/windows/apps/dn263392). Этот процесс напрямую не меняет информацию, изначально указанную при [выставлении внутреннего продукта приложения в Магазине](https://msdn.microsoft.com/library/windows/apps/mt148551).


            **Примечание.** Начиная c Windows10 в Магазине отсутствует ограничение на количество продуктов на одну учетную запись разработчика. В предыдущих выпусках в Магазине действовало ограничение в 200 продуктов на одну учетную запись разработчика. Процесс, описанный в этом разделе, можно использовать для обхода этого ограничения.

## Необходимые условия

-   В этом разделе рассказывается о поддержке представления нескольких предложений для продажи из приложения с использованием одной продуктовой позиции, выставленной в Магазине. Чтобы ознакомиться с покупками из приложения, см. статью [Поддержка покупок внутренних продуктов приложения](enable-in-app-product-purchases.md), из которой вы узнаете о лицензировании и о том, как правильно вносить покупки из приложения в список Магазина.
-   Когда вы создадите код для продаж из приложения и будете проверять его в первый раз, используйте объект [**CurrentAppSimulator**](https://msdn.microsoft.com/library/windows/apps/hh779766) вместо объекта [**CurrentApp**](https://msdn.microsoft.com/library/windows/apps/hh779765). В этом случае вы сможете проверить логику лицензирования путем имитации обращения к серверу лицензирования вместо вызова реального сервера. Для этого необходимо изменить файл с именем WindowsStoreProxy.xml. в папке %userprofile%\\AppData\\local\\packages\\&lt;package name&gt;\\LocalState\\Microsoft\\Windows Store\\ApiData. Имитатор Microsoft Visual Studio создает этот файл при первом запуске приложения. Также можно загрузить собственный его вариант во время выполнения. Подробнее см. в статье **CurrentAppSimulator**.
-   В этом разделе также приведены ссылки на примеры кода из статьи [Пример для Магазина](http://go.microsoft.com/fwlink/p/?LinkID=627610). Этот пример дает отличную возможность поэкспериментировать с разными вариантами монетизации, доступными для приложений универсальной платформы Windows (UWP).

## Запрос на покупку для продукта из приложения

Запрос на покупку для конкретного продукта в большом каталоге обрабатывается во многом так же, как любой другой запрос на покупку в приложении. Когда ваше приложение вызывает новую перегрузку метода [**RequestProductPurchaseAsync**](https://msdn.microsoft.com/library/windows/apps/dn263382), оно предоставляет идентификатор *OfferId* и объект [**ProductPurchaseDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/dn263390) с уже указанным наименованием внутреннего продукта приложения.

```CSharp
string offerId = "1234";
string displayPropertiesName = "MusicOffer1";
var displayProperties = new ProductPurchaseDisplayProperties(displayPropertiesName);

try
{
    PurchaseResults purchaseResults = await CurrentAppSimulator.RequestProductPurchaseAsync("product1", offerId, displayProperties);
    switch (purchaseResults.Status)
    {
        case ProductPurchaseStatus.Succeeded:
            // Grant the user their purchase here, and then pass the product ID and transaction ID to currentAppSimulator.reportConsumableFulfillment
            // To indicate local fulfillment to the Windows Store.
            break;
        case ProductPurchaseStatus.NotFulfilled:
            // First check for unfulfilled purchases and grant any unfulfilled purchases from an earlier transaction.
            // Once products are fulfilled pass the product ID and transaction ID to currentAppSimulator.reportConsumableFulfillment
            // To indicate local fulfillment to the Windows Store.
            break;
        case ProductPurchaseStatus.NotPurchased:
            // Notify user that the purchase was not completed due to cancellation or error.
            break;
    }
}
catch (Exception)
{
    //Notify the user of the purchase error.
}
```

## Сообщение о выполнении продажи из приложения

Ваше приложение должно сообщить в Магазин о продаже продуктовой позиции сразу после локального выполнения продажи предложения. При использовании большого каталога пользователь не сможет совершать покупки предложений из приложения, используя ту же продуктовую позицию Магазина, если ваше приложение не сообщит о выполнении продажи предложения.

Как уже говорилось, Магазин использует предоставленные данные о предложении только для заполнения класса [**PurchaseResults**](https://msdn.microsoft.com/library/windows/apps/dn263392) и не создает постоянную связь между предложением из большого каталога и продуктовой позицией в Магазине. В результате вы должны отслеживать права пользователя на продукты из большого каталога и предоставлять пользователю контекст, относящийся к продукту (например, название приобретаемого товара или информацию о нем), вне операции [**RequestProductPurchaseAsync**](https://msdn.microsoft.com/library/windows/apps/dn263382).

Приведенный ниже код демонстрирует вызов функции выполнения и шаблон сообщения пользовательского интерфейса, в который вставлена информация о конкретном предложении. Поскольку эта информация отсутствует, в примере используются данные из [**ListingInformation**](https://msdn.microsoft.com/library/windows/apps/br225163) продуктовой позиции.

```CSharp
string offerId = "1234";
product1ListingName = product1.Name;
string displayPropertiesName = "MusicOffer1";

if (String.IsNullOrEmpty(displayPropertiesName))
{
    displayPropertiesName = product1ListingName;
}
var offerIdMsg = " with offer id " + offerId;
if (String.IsNullOrEmpty(offerId))
{
    offerIdMsg = " with no offer id";
}

FulfillmentResult result = await CurrentAppSimulator.ReportConsumableFulfillmentAsync(productId, transactionId);
switch (result)
{
    case FulfillmentResult.Succeeded:
        Log("You bought and fulfilled " + displayPropertiesName + offerIdMsg, NotifyType.StatusMessage);
        break;
    case FulfillmentResult.NothingToFulfill:
        Log("There is no purchased product 1 to fulfill.", NotifyType.StatusMessage);
        break;
    case FulfillmentResult.PurchasePending:
        Log("You bought product 1. The purchase is pending so we cannot fulfill the product.", NotifyType.StatusMessage);
        break;
    case FulfillmentResult.PurchaseReverted:
        Log("You bought product 1. But your purchase has been reverted.", NotifyType.StatusMessage);
        // Since the user' s purchase was revoked, they got their money back.
        // You may want to revoke the user' s access to the consumable content that was granted.
        break;
    case FulfillmentResult.ServerError:
        Log("You bought product 1. There was an error when fulfilling.", NotifyType.StatusMessage);
        break;
}
```

## Ссылки по теме

* [Поддержка покупок внутренних продуктов приложения](enable-in-app-product-purchases.md)
* [Поддержка покупок потребляемых внутренних продуктов приложения](enable-consumable-in-app-product-purchases.md)
* [Пример для Магазина (демонстрация пробных версий и покупок из приложения)](http://go.microsoft.com/fwlink/p/?LinkID=627610)
* [**RequestProductPurchaseAsync**](https://msdn.microsoft.com/library/windows/apps/dn263382)
* [**ProductPurchaseDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/dn263384)



<!--HONumber=Jun16_HO4-->


