---
ms.assetid: 9630AF6D-6887-4BE3-A3CB-D058F275B58F
description: Узнайте, как использовать пространство имен Windows.Services.Store для получения информации о лицензии для текущего приложения и его надстроек.
title: Получение информации о лицензии для приложения и надстроек
ms.date: 12/04/2017
ms.topic: article
keywords: windows 10, uwp, лицензии, приложения, надстройки, покупки из приложения, IAP, Windows.Services.Store
ms.localizationpriority: medium
ms.openlocfilehash: 4d7c832907af17436d588f0fac6c5039d4affa82
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8894715"
---
# <a name="get-license-info-for-apps-and-add-ons"></a>Получение информации о лицензии для приложений и надстроек

В этой статье рассказывается, как использовать методы класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) из пространства имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) для получения информации о лицензии для текущего приложения и его надстроек. Например, с помощью этих сведений можно определить, активны ли лицензии для приложения или его надстроек или они являются лицензиями пробной версии.

> [!NOTE]
> Пространство имен **Windows.Services.Store** впервые появилось в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Дополнительные сведения см. в [этой статье](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## <a name="prerequisites"></a>Что вам понадобится

Для этого примера необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для **Windows 10 Anniversary Edition (10.0; сборка 14393)** и более поздних выпусков.
* У вас есть [создали отправку приложения](https://msdn.microsoft.com/windows/uwp/publish/app-submissions) в центре партнеров, и это приложение опубликовано в магазине. При необходимости можно настроить приложение, чтобы его нельзя было найти в Магазине, пока вы его тестируете. Подробнее см. в нашем [руководстве по тестированию](in-app-purchases-and-trials.md#testing).
* Если вы хотите получить лицензионные сведения о надстройке для приложения, необходимо также [Создать надстройку в центре партнеров](../publish/add-on-submissions.md).

В коде из этого примера предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение является однопользовательским и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

> [!NOTE]
> Если у вас есть классическое приложение, которое использует [мост для классических приложений](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этом примере, для настройки объекта [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

## <a name="code-example"></a>Пример кода

Для получения информации о лицензии для текущего приложения используйте метод [GetAppLicenseAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getapplicenseasync). Это асинхронный метод, который возвращает объект [StoreAppLicense](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.aspx), предоставляющий сведения о лицензии для приложения, включая свойства, указывающие, имеется ли сейчас у пользователя действительная лицензия на использование приложения ([IsActive](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.isactive)) и распространяется ли лицензия на пробную версию ([IsTrial](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.istrial)).

Для доступа к лицензиям для постоянных надстроек текущего приложения, для которых у пользователя есть права на использование, используйте свойство [AddOnLicenses](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.addonlicenses) объекта [StoreAppLicense](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.aspx). Это свойство возвращает коллекцию объектов [StoreLicense](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storelicense.aspx), представляющих лицензии на надстройки.

> [!div class="tabbedCodeSnippets"]
[!code-cs[GetLicenseInfo](./code/InAppPurchasesAndLicenses_RS1/cs/GetLicenseInfoPage.xaml.cs#GetLicenseInfo)]

Полный пример приложения см. в разделе [Пример для Store](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

## <a name="related-topics"></a>Статьи по теме

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md)
* [Поддержка покупок приложений и надстроек внутри приложения](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Поддержка покупок потребляемых надстроек внутри приложения](enable-consumable-add-on-purchases.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)
