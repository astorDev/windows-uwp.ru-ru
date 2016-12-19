---
author: mcleanbyron
ms.assetid: 89178FD9-850B-462F-9016-1AD86D1F6F7F
description: "Узнайте, как использовать пространство имен Windows.Services.Store, чтобы получить связанные с Магазином сведения о продукте для текущего приложения или одной из его надстроек."
title: "Получение информации о продукте для приложений и надстроек"
translationtype: Human Translation
ms.sourcegitcommit: ffda100344b1264c18b93f096d8061570dd8edee
ms.openlocfilehash: dd58103d22314081985cd5ce0f98f2f25e1e7287

---

# <a name="get-product-info-for-apps-and-add-ons"></a>Получение информации о продукте для приложений и надстроек

Приложения, предназначенные для Windows 10 версии 1607 и выше, могут использовать методы класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) из пространства имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) для доступа к связанной с Магазином информации для текущего приложения или одной из его надстроек (также известных как внутренние продукты приложения или IAP). В следующих примерах в этой статье показано, как это сделать для различных сценариев. Полный пример см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

>**Примечание.**&nbsp;&nbsp;Эта статья относится к приложениям, предназначенным для Windows 10 версии 1607 и старше. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Подробнее см. в разделе [Внутренние покупки приложения и пробные версии, использующие пространство имен Windows.ApplicationModel.Store](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## <a name="prerequisites"></a>Предварительные условия и необходимые компоненты

Для этих примеров необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для Windows 10 версии 1607 и выше.
* Создано приложение на панели Центра разработки для Windows, и это приложение опубликовано и доступно в Магазине. Это может быть приложение, которое вы хотите предложить пользователям, или базовое приложение, соответствующее минимальным требованиям [комплекта сертификации приложений для Windows](https://developer.microsoft.com/windows/develop/app-certification-kit) и используемое только для тестирования. Подробнее см. в [руководстве по тестированию](in-app-purchases-and-trials.md#testing).

В коде из этих примеров предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение является однопользовательским и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

>**Примечание.**&nbsp;&nbsp;Если у вас есть классическое приложение, которое использует [мост для настольных ПК](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этих примерах, для настройки объекта [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

## <a name="get-info-for-the-current-app"></a>Получение информации для текущего приложения

Чтобы получить информацию о продукте Магазина для текущего приложения, используйте метод [GetStoreProductForCurrentAppAsync](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.getstoreproductforcurrentappasync.aspx). Этот асинхронный метод возвращает объект [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), который можно использовать для получения такой информации, как цена.

> [!div class="tabbedCodeSnippets"]
[!code-cs[GetProductInfo](./code/InAppPurchasesAndLicenses_RS1/cs/GetAppInfoPage.xaml.cs#GetAppInfo)]

## <a name="get-info-for-products-with-known-store-ids"></a>Получение информации для продуктов с известными кодами продукта в Магазине

Чтобы получить информацию о продукте Магазина для приложений или надстроек с уже известными [кодами продукта в Магазине](in-app-purchases-and-trials.md#store_ids), используйте метод [GetStoreProductsAsync](https://msdn.microsoft.com/library/windows/apps/mt706579.aspx). Этот асинхронный метод возвращает коллекцию объектов [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), которые представляют каждое из приложений или надстроек. Помимо кодов продукта в Магазине, этому методу необходимо передать список строк, которые определяют типы надстроек. Список поддерживаемых строковых значений см. в описании свойства [ProductKind](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.productkind.aspx).

В следующем примере извлекается информация для постоянных надстроек с заданными кодами продукта в Магазине.

> [!div class="tabbedCodeSnippets"]
[!code-cs[GetProductInfo](./code/InAppPurchasesAndLicenses_RS1/cs/GetProductInfoPage.xaml.cs#GetProductInfo)]

## <a name="get-info-for-add-ons-that-are-available-for-the-current-app"></a>Получение информации для надстроек, доступных для текущего приложения

Чтобы получить информацию о продукте Магазина для надстроек, которые доступны для текущего приложения, используйте метод [GetAssociatedStoreProductsAsync](https://msdn.microsoft.com/library/windows/apps/mt706571.aspx). Этот асинхронный метод возвращает коллекцию объектов [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), которые представляют каждую из доступных надстроек. Этому методу необходимо передать список строк, определяющих типы надстроек, которые требуется извлечь. Список поддерживаемых строковых значений см. в описании свойства [ProductKind](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.productkind.aspx).

>**Примечание.**&nbsp;&nbsp;Если приложение содержит много надстроек, можно также использовать метод [GetAssociatedStoreProductsWithPagingAsync](https://msdn.microsoft.com/library/windows/apps/mt706572.aspx), чтобы разбивать возвращаемые результаты для надстроек на страницы.

В следующем примере извлекается информации для всех постоянных надстроек, потребляемых надстроек, управляемых Магазином, и потребляемых надстроек, управляемых разработчиком.

> [!div class="tabbedCodeSnippets"]
[!code-cs[GetProductInfo](./code/InAppPurchasesAndLicenses_RS1/cs/GetAddOnInfoPage.xaml.cs#GetAddOnInfo)]


## <a name="get-info-for-add-ons-for-the-current-app-that-the-current-user-is-entitled-to-use"></a>Получение информации о надстройках для текущего приложения, которые может использовать текущий пользователь

Чтобы получить информацию о продукте Магазина для надстроек, которые доступны текущему пользователю, используйте метод [GetUserCollectionAsync](https://msdn.microsoft.com/library/windows/apps/mt706580.aspx). Этот асинхронный метод возвращает коллекцию объектов [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), которые представляют каждую из надстроек. Этому методу необходимо передать список строк, определяющих типы надстроек, которые требуется извлечь. Список поддерживаемых строковых значений см. в описании свойства [ProductKind](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.productkind.aspx).

>**Примечание.**&nbsp;&nbsp;Если приложение содержит много надстроек, можно также использовать метод [GetUserCollectionWithPagingAsync](https://msdn.microsoft.com/library/windows/apps/mt706581.aspx), чтобы разбивать возвращаемые результаты для надстроек на страницы.

В следующем примере извлекается информация для постоянных надстроек с заданными кодами продукта в Магазине.

> [!div class="tabbedCodeSnippets"]
[!code-cs[GetProductInfo](./code/InAppPurchasesAndLicenses_RS1/cs/GetUserCollectionPage.xaml.cs#GetUserCollection)]

## <a name="related-topics"></a>Статьи по теме

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о лицензии для приложений и надстроек](get-license-info-for-apps-and-add-ons.md)
* [Поддержка покупок приложений и надстроек внутри приложения](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Поддержка покупок потребляемых надстроек внутри приложения](enable-consumable-add-on-purchases.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)



<!--HONumber=Dec16_HO1-->


