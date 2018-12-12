---
ms.assetid: B356C442-998F-4B2C-B550-70070C5E4487
description: Узнайте, как использовать пространство имен Windows.Services.Store для покупки приложения или одной из его надстроек.
title: Поддержка покупок приложений и надстроек из приложения
keywords: windows 10, uwp, надстройки, покупки из приложения, IAP, Windows.Services.Store
ms.date: 08/25/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: a64a52005221c418ea82e8fffa9ecf94b6d1bef3
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8929087"
---
# <a name="enable-in-app-purchases-of-apps-and-add-ons"></a>Поддержка покупок приложений и надстроек из приложения

В этой статье показано, как использовать члены из пространства имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) для запроса приобретения для пользователя текущего приложения или одной из его надстроек. Например, если пользователь в данный момент имеет пробную версию приложения, можно использовать этот процесс, чтобы приобрести для пользователя полную лицензию. Кроме того, этот процесс можно использовать для покупки для пользователя надстройки, например нового уровня игры.

Для запроса приобретения приложения или надстройки в пространстве имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) предусмотрено несколько различных методов.
* Если вы знаете [код продукта в Магазине](in-app-purchases-and-trials.md#store_ids) для приложения или надстройки, можно использовать метод [RequestPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestpurchaseasync) класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx).
* Если уже имеется [объект **StoreProduct**, **StoreSku** или **StoreAvailability**](in-app-purchases-and-trials.md#products-skus), который представляет приложение или надстройку, можно использовать методы **RequestPurchaseAsync** этих объектов. Примеры различных способов получения **StoreProduct** в коде можно найти в разделе [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md).

Каждый метод предоставляет стандартный интерфейс покупки для пользователя, затем завершается асинхронно после завершения транзакции. Метод возвращает объект, который указывает, была ли транзакция выполнена успешно.

> [!NOTE]
> Пространство имен **Windows.Services.Store** впервые появилось в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Дополнительные сведения см. в [этой статье](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## <a name="prerequisites"></a>Что вам понадобится

Для этого примера необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для **Windows 10 Anniversary Edition (10.0; сборка 14393)** и более поздних выпусков.
* У вас есть [создали отправку приложения](https://msdn.microsoft.com/windows/uwp/publish/app-submissions) в центре партнеров, и это приложение опубликовано в магазине. При необходимости можно настроить приложение, чтобы его нельзя было найти в Магазине, пока вы его тестируете. Подробнее см. в нашем [руководстве по тестированию](in-app-purchases-and-trials.md#testing).
* Если вы хотите включить покупки из приложения для надстройки для приложения, необходимо также [Создать надстройку в центре партнеров](../publish/add-on-submissions.md).

В коде из этого примера предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение является однопользовательским и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

> [!NOTE]
> Если у вас есть классическое приложение, которое использует [мост для классических приложений](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этом примере, для настройки объекта [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

## <a name="code-example"></a>Пример кода

В этом примере показано, как использовать метод [RequestPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.requestpurchaseasync) класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) для приобретения приложения или надстройки с известным [кодом продукта в Магазине](in-app-purchases-and-trials.md#store-ids). Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnablePurchases](./code/InAppPurchasesAndLicenses_RS1/cs/PurchaseAddOnPage.xaml.cs#PurchaseAddOn)]

## <a name="video"></a>Видео

Посмотрите видеоролик, чтобы ознакомиться с общими сведениями о том, как реализовать покупки из приложения в вашем приложении.
<br/>
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/One-Dev-Minute/Adding-In-App-Purchases-to-Your-UWP-App/player]

## <a name="related-topics"></a>Еще по теме:

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md)
* [Получение информации о лицензии для приложений и надстроек](get-license-info-for-apps-and-add-ons.md)
* [Поддержка покупок потребляемых надстроек](enable-consumable-add-on-purchases.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)
