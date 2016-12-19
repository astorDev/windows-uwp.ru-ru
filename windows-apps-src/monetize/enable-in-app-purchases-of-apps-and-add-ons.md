---
author: mcleanbyron
ms.assetid: B356C442-998F-4B2C-B550-70070C5E4487
description: "Узнайте, как использовать пространство имен Windows.Services.Store для покупки приложения или одной из его надстроек."
title: "Поддержка покупок приложений и надстроек из приложения"
keywords: "Пример кода продажи из приложения"
translationtype: Human Translation
ms.sourcegitcommit: ffda100344b1264c18b93f096d8061570dd8edee
ms.openlocfilehash: 05a93f3124324d7308f5494ad14a15bfd6a4e698

---

# <a name="enable-in-app-purchases-of-apps-and-add-ons"></a>Поддержка покупок приложений и надстроек из приложения

Приложения, предназначенные для Windows 10 версии 1607 и выше, могут использовать члены из пространства имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) для запроса приобретения для пользователя текущего приложения или одной из его надстроек (также называемых внутренними продуктами приложения или IAP). Например, если пользователь в данный момент имеет пробную версию приложения, можно использовать этот процесс, чтобы приобрести для пользователя полную лицензию. Кроме того, этот процесс можно использовать для покупки для пользователя надстройки, например нового уровня игры.

Для запроса приобретения приложения или надстройки в пространстве имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) предусмотрено несколько различных методов.
* Если вы знаете [код продукта в Магазине](in-app-purchases-and-trials.md#store_ids) для приложения или надстройки, можно использовать метод [RequestPurchaseAsync](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.requestpurchaseasync.aspx) класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx).
* Если уже имеется объект [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), [StoreSku](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storesku.aspx) или [StoreAvailability](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeavailability.aspx), который представляет приложение или надстройку, можно использовать методы **RequestPurchaseAsync** этих объектов.

Каждый метод предоставляет стандартный интерфейс покупки для пользователя, затем завершается асинхронно после завершения транзакции. Метод возвращает объект, который указывает, была ли транзакция выполнена успешно.

>**Примечание.**&nbsp;&nbsp;Эта статья относится к приложениям, предназначенным для Windows 10 версии 1607 и старше. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Подробнее см. в разделе [Внутренние покупки приложения и пробные версии, использующие пространство имен Windows.ApplicationModel.Store](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## <a name="prerequisites"></a>Предварительные условия и необходимые компоненты

Для этого примера необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для Windows 10 версии 1607 и выше.
* Создано приложение на панели Центра разработки для Windows, и это приложение опубликовано и доступно в Магазине. Это может быть приложение, которое вы хотите предложить пользователям, или базовое приложение, соответствующее минимальным требованиям [комплекта сертификации приложений для Windows](https://developer.microsoft.com/windows/develop/app-certification-kit) и используемое только для тестирования. Подробнее см. в [руководстве по тестированию](in-app-purchases-and-trials.md#testing).

В коде из этого примера предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

>**Примечание.**&nbsp;&nbsp;Если у вас есть классическое приложение, которое использует [мост для настольных ПК](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этом примере, для настройки объекта [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

## <a name="code-example"></a>Пример кода

В этом примере показано, как использовать метод [RequestPurchaseAsync](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.requestpurchaseasync.aspx) класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) для приобретения приложения или надстройки с известным [кодом продукта в Магазине](in-app-purchases-and-trials.md#store_ids).

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnablePurchases](./code/InAppPurchasesAndLicenses_RS1/cs/PurchaseAddOnPage.xaml.cs#PurchaseAddOn)]

Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

## <a name="related-topics"></a>Статьи по теме

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md)
* [Получение информации о лицензии для приложений и надстроек](get-license-info-for-apps-and-add-ons.md)
* [Поддержка покупок потребляемых надстроек](enable-consumable-add-on-purchases.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)



<!--HONumber=Dec16_HO1-->


