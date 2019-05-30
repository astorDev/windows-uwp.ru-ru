---
ms.assetid: 9630AF6D-6887-4BE3-A3CB-D058F275B58F
description: Узнайте, как использовать пространство имен Windows.Services.Store для получения информации о лицензии для текущего приложения и его надстроек.
title: Получение информации о лицензии для приложения и надстроек
ms.date: 12/04/2017
ms.topic: article
keywords: windows 10, uwp, лицензии, приложения, надстройки, покупки из приложения, IAP, Windows.Services.Store
ms.localizationpriority: medium
ms.openlocfilehash: c8bef40cb34412fbb92585d2ccd25682c72ffe5c
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371628"
---
# <a name="get-license-info-for-apps-and-add-ons"></a>Получение информации о лицензии для приложений и надстроек

В этой статье рассказывается, как использовать методы класса [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) из пространства имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) для получения информации о лицензии для текущего приложения и его надстроек. Например, с помощью этих сведений можно определить, активны ли лицензии для приложения или его надстроек или они являются лицензиями пробной версии.

> [!NOTE]
> Пространство имен **Windows.Services.Store** впервые появилось в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store), а не пространство имен **Windows.Services.Store**. Дополнительные сведения см. в [этой статье](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## <a name="prerequisites"></a>предварительные требования

Для этого примера необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для **Windows 10 Anniversary Edition (10.0; сборка 14393)** и более поздних выпусков.
* У вас есть [создан Отправка приложения](https://docs.microsoft.com/windows/uwp/publish/app-submissions) в центре партнеров и это приложение публикуется в Store. При необходимости можно настроить приложение, чтобы его нельзя было найти в Магазине, пока вы его тестируете. Подробнее см. в нашем [руководстве по тестированию](in-app-purchases-and-trials.md#testing).
* Если требуется получить сведения о лицензии для надстройки для приложения, необходимо также [Создайте надстройку в центре партнеров](../publish/add-on-submissions.md).

В коде из этого примера предполагается следующее:
* Код выполняется в контексте страницы [Page](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page), которая содержит [ProgressRing](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.progressring) с именем ```workingProgressRing``` и [TextBlock](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

> [!NOTE]
> Если у вас есть классическое приложение, которое использует [мост для классических приложений](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этом примере, для настройки объекта [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

## <a name="code-example"></a>Пример кода

Для получения информации о лицензии для текущего приложения используйте метод [GetAppLicenseAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getapplicenseasync). Это асинхронный метод, который возвращает объект [StoreAppLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense), предоставляющий сведения о лицензии для приложения, включая свойства, указывающие, имеется ли сейчас у пользователя действительная лицензия на использование приложения ([IsActive](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.isactive)) и распространяется ли лицензия на пробную версию ([IsTrial](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.istrial)).

Для доступа к лицензиям для постоянных надстроек текущего приложения, для которых у пользователя есть права на использование, используйте свойство [AddOnLicenses](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.addonlicenses) объекта [StoreAppLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense). Это свойство возвращает коллекцию объектов [StoreLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storelicense), представляющих лицензии на надстройки.

> [!div class="tabbedCodeSnippets"]
[!code-csharp[GetLicenseInfo](./code/InAppPurchasesAndLicenses_RS1/cs/GetLicenseInfoPage.xaml.cs#GetLicenseInfo)]

Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

## <a name="related-topics"></a>См. также

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получите сведения о продукте для приложения и надстройки](get-product-info-for-apps-and-add-ons.md)
* [Включить покупки из приложений, приложений и надстроек](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Включить покупки готовых к использованию надстройки](enable-consumable-add-on-purchases.md)
* [Реализуйте пробную версию приложения](implement-a-trial-version-of-your-app.md)
* [Пример Store](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)
