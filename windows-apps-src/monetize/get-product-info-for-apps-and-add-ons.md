---
author: mcleanbyron
ms.assetid: 89178FD9-850B-462F-9016-1AD86D1F6F7F
description: "Узнайте, как использовать пространство имен Windows.Services.Store, чтобы получить связанные с Магазином сведения о продукте для текущего приложения или одной из его надстроек."
title: "Получение информации о продукте для приложений и надстроек"
translationtype: Human Translation
ms.sourcegitcommit: 5f975d0a99539292e1ce91ca09dbd5fac11c4a49
ms.openlocfilehash: c453dc74730fc451bbe9babdffb2ce4d72712082

---

# Получение информации о продукте для приложений и надстроек

Приложения, предназначенные для Windows 10 версии 1607 и выше, могут использовать методы класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) из пространства имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) для доступа к связанной с Магазином информации для текущего приложения или одной из его надстроек (также известных как внутренние продукты приложения или IAP). В следующих примерах в этой статье показано, как это сделать для различных сценариев. Полный пример см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

>**Примечание.**&nbsp;&nbsp;Эта статья относится к приложениям, предназначенным для Windows 10 версии 1607 и старше. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Подробнее см. в разделе [Внутренние покупки приложения и пробные версии, использующие пространство имен Windows.ApplicationModel.Store](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## Предварительные условия и необходимые компоненты

Для этих примеров необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для Windows 10 версии 1607 и выше.
* Создано приложение на панели Центра разработки для Windows, и это приложение опубликовано и доступно в Магазине. Это может быть приложение, которое вы хотите предложить пользователям, или базовое приложение, соответствующее минимальным требованиям [комплекта сертификации приложений для Windows](https://developer.microsoft.com/windows/develop/app-certification-kit) и используемое только для тестирования. Подробнее см. в [руководстве по тестированию](in-app-purchases-and-trials.md#testing).

В коде из этих примеров предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение является однопользовательским и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

## Получение информации для текущего приложения

Чтобы получить информацию о продукте Магазина для текущего приложения, используйте метод [GetStoreProductForCurrentAppAsync](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.getstoreproductforcurrentappasync.aspx). Этот асинхронный метод возвращает объект [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), который можно использовать для получения такой информации, как цена.

```csharp
private StoreContext context = null;

public async void GetAppInfo()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }

    // Get app store product details. Because this might take several moments,   
    // display a ProgressRing during the operation.
    workingProgressRing.IsActive = true;
    StoreProductResult queryResult = await context.GetStoreProductForCurrentAppAsync();
    workingProgressRing.IsActive = false;

    if (queryResult.ExtendedError != null)
    {
        // The user may be offline or there might be some other server failure.
        textBlock.Text = $"ExtendedError: {queryResult.ExtendedError.Message}";
        return;
    }

    if (queryResult.Product == null)
    {
        // The Store catalog returned an unexpected result.
        textBlock.Text = "Something went wrong, the product was not returned.";
        return;
    }

    // Display the price of the app.
    textBlock.Text = $"The price of this app is: {queryResult.Product.Price.FormattedBasePrice}";
}
```

## Получение информации для продуктов с известными кодами продукта в Магазине

Чтобы получить информацию о продукте Магазина для приложений или надстроек с уже известными [кодами продукта в Магазине](in-app-purchases-and-trials.md#store_ids), используйте метод [GetStoreProductsAsync](https://msdn.microsoft.com/library/windows/apps/mt706579.aspx). Этот асинхронный метод возвращает коллекцию объектов [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), которые представляют каждое из приложений или надстроек. Помимо кодов продукта в Магазине, этому методу необходимо передать список строк, которые определяют типы надстроек. Список поддерживаемых строковых значений см. в описании свойства [ProductKind](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.productkind.aspx).

Следующий пример извлекает информацию для постоянных надстроек с заданными кодами продукта в Магазине.

```csharp
private StoreContext context = null;

public async void GetProductInfo()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }

    // Specify the kinds of add-ons to retrieve.
    string[] productKinds = { "Durable" };
    List<String> filterList = new List<string>(productKinds);

    // Specify the Store IDs of the products to retrieve.
    string[] storeIds = new string[] { "9NBLGGH4TNMP", "9NBLGGH4TNMN" };

    workingProgressRing.IsActive = true;
    StoreProductQueryResult queryResult =
        await context.GetStoreProductsAsync(filterList, storeIds);
    workingProgressRing.IsActive = false;

    if (queryResult.ExtendedError != null)
    {
        // The user may be offline or there might be some other server failure.
        textBlock.Text = $"ExtendedError: {queryResult.ExtendedError.Message}";
        return;
    }

    foreach (KeyValuePair<string, StoreProduct> item in queryResult.Products)
    {
        // Access the Store info for the product.
        StoreProduct product = item.Value;

        // Use members of the product object to access info for the product...
    }
}
```

## Получение информацию для надстроек, доступных для текущего приложения

Чтобы получить информацию о продукте Магазина для надстроек, которые доступны для текущего приложения, используйте метод [GetAssociatedStoreProductsAsync](https://msdn.microsoft.com/library/windows/apps/mt706571.aspx). Этот асинхронный метод возвращает коллекцию объектов [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), которые представляют каждую из доступных надстроек. Этому методу необходимо передать список строк, определяющих типы надстроек, которые требуется извлечь. Список поддерживаемых строковых значений см. в описании свойства [ProductKind](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.productkind.aspx).

Следующий пример извлекает информацию для всех постоянных надстроек, потребляемых надстроек, управляемых Магазином, и потребляемых надстроек, управляемых разработчиком.

```csharp
private StoreContext context = null;

public async void GetAddOnInfo()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }

    // Specify the kinds of add-ons to retrieve.
    string[] productKinds = { "Durable", "Consumable", "UnmanagedConsumable" };
    List<String> filterList = new List<string>(productKinds);

    workingProgressRing.IsActive = true;
    StoreProductQueryResult queryResult = await context.GetAssociatedStoreProductsAsync(filterList);
    workingProgressRing.IsActive = false;

    if (queryResult.ExtendedError != null)
    {
        // The user may be offline or there might be some other server failure.
        textBlock.Text = $"ExtendedError: {queryResult.ExtendedError.Message}";
        return;
    }

    foreach (KeyValuePair<string, StoreProduct> item in queryResult.Products)
    {
        // Access the Store product info for the add-on.
        StoreProduct product = item.Value;

        // Use members of the product object to access listing info for the add-on...
    }
}
```

>**Примечание.**&nbsp;&nbsp;Если приложение содержит много надстроек, можно также использовать метод [GetAssociatedStoreProductsWithPagingAsync](https://msdn.microsoft.com/library/windows/apps/mt706572.aspx), чтобы разбивать возвращаемые результаты для надстроек на страницы.


## Получение информации о надстройках для текущего приложения, которые может использовать текущий пользователь

Чтобы получить информацию о продукте Магазина для надстроек, которые доступны текущему пользователю, используйте метод [GetUserCollectionAsync](https://msdn.microsoft.com/library/windows/apps/mt706580.aspx). Этот асинхронный метод возвращает коллекцию объектов [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), которые представляют каждую из надстроек. Этому методу необходимо передать список строк, определяющих типы надстроек, которые требуется извлечь. Список поддерживаемых строковых значений см. в описании свойства [ProductKind](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.productkind.aspx).

Следующий пример извлекает информацию для постоянных надстроек с заданными кодами продукта в Магазине.

```csharp
private StoreContext context = null;

public async void GetUserCollection()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
    }

    // Specify the kinds of add-ons to retrieve.
    string[] productKinds = { "Durable" };
    List<String> filterList = new List<string>(productKinds);

    workingProgressRing.IsActive = true;
    StoreProductQueryResult queryResult = await context.GetUserCollectionAsync(filterList);
    workingProgressRing.IsActive = false;

    if (queryResult.ExtendedError != null)
    {
        // The user may be offline or there might be some other server failure.
        textBlock.Text = $"ExtendedError: {queryResult.ExtendedError.Message}";
        return;
    }

    foreach (KeyValuePair<string, StoreProduct> item in queryResult.Products)
    {
        StoreProduct product = item.Value;

        // Use members of the product object to access info for the product...
    }
}
```

>**Примечание.**&nbsp;&nbsp;Если приложение содержит много надстроек, можно также использовать метод [GetUserCollectionWithPagingAsync](https://msdn.microsoft.com/library/windows/apps/mt706581.aspx), чтобы разбивать возвращаемые результаты для надстроек на страницы.

## Связанные разделы

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о лицензии для приложений и надстроек](get-license-info-for-apps-and-add-ons.md)
* [Поддержка покупок приложений и надстроек внутри приложения](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Поддержка покупок потребляемых надстроек внутри приложения](enable-consumable-add-on-purchases.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)



<!--HONumber=Aug16_HO5-->


