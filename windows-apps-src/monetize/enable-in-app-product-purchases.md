---
author: Xansky
Description: Whether your app is free or not, you can sell content, other apps, or new app functionality (such as unlocking the next level of a game) from right within the app. Here we show you how to enable these products in your app.
title: Поддержка покупки продуктов из приложения
ms.assetid: D158E9EB-1907-4173-9889-66507957BD6B
keywords: UWP, надстройки, покупки из приложения, IAP, Windows.ApplicationModel.Store
ms.author: mhopkins
ms.date: 08/25/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 89e9fff8f041c4beb2a897c7be75b2f6e009f809
ms.sourcegitcommit: 144f5f127fc4fbd852f2f6780ef26054192d68fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5968718"
---
# <a name="enable-in-app-product-purchases"></a>Поддержка покупки продуктов из приложения

Независимо от того, является ли ваше приложение бесплатным или нет, вы можете продавать содержимое, другие приложения или новые функциональные возможности (например, разблокирование следующего уровня игры) прямо из приложения. В этом разделе рассказывается о том, как предоставить возможность совершать такие покупки.

> [!IMPORTANT]
> В этой статье показано, как использовать элементы пространства имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx) для включения покупок продуктов в приложении. Это пространство имен больше не дополняется новыми функциями, и мы рекомендуем вместо него использовать пространство имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx). Пространство имен **Windows.Services.Store** поддерживает новейшие типы надстроек, таких как подписки и потребляемые надстройки, управляемые магазином, а его архитектура обеспечивает совместимость с будущими продуктами и компонентами, которые поддерживаются центром партнеров и хранилище. Пространство имен **Windows.Services.Store** впервые появилось в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio. Дополнительные сведения о включении внутренние продукты приложения, использующие пространство имен **Windows.Services.Store** см. [в этой статье](enable-in-app-purchases-of-apps-and-add-ons.md).

> [!NOTE]
> Внутренние продукты приложения не могут предлагаться в пробной версии. Пользователи пробной версии приложения смогут купить внутренний продукт приложения, только если приобретут полную версию этого приложения.

## <a name="prerequisites"></a>Необходимые условия

-   Приложение для Windows, в которое предполагается добавить компоненты для продажи.
-   Когда вы создадите код для продаж внутренних продуктов приложения и будете проверять его в первый раз, используйте объект [CurrentAppSimulator](https://msdn.microsoft.com/library/windows/apps/hh779766) вместо объекта [CurrentApp](https://msdn.microsoft.com/library/windows/apps/hh779765). В этом случае вы сможете проверить логику лицензирования путем имитации обращения к серверу лицензирования вместо вызова реального сервера. Для этого необходимо изменить файл с именем WindowsStoreProxy.xml в папке %userprofile%\\AppData\\local\\packages\\&lt;имя пакета&gt;\\LocalState\\Microsoft\\Windows Store\\ApiData. Имитатор Microsoft Visual Studio создает этот файл при первом запуске приложения. Также можно загрузить собственный его вариант во время выполнения. Дополнительные сведения см. в разделе [Использование файла WindowsStoreProxy.xml с CurrentAppSimulator](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md#proxy).
-   В этом разделе также приведены ссылки на примеры кода из статьи [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store). Этот пример дает отличную возможность поэкспериментировать с разными способами оплаты, доступными для приложений универсальной платформы Windows (UWP).

## <a name="step-1-initialize-the-license-info-for-your-app"></a>Шаг 1. Инициализация сведений о лицензии для приложения

Во время инициализации приложения получите для него объект [LicenseInformation](https://msdn.microsoft.com/library/windows/apps/br225157) путем инициализации [CurrentApp](https://msdn.microsoft.com/library/windows/apps/hh779765) или [CurrentAppSimulator](https://msdn.microsoft.com/library/windows/apps/hh779766), чтобы включить покупки внутренних продуктов приложения.

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnableInAppPurchases](./code/InAppPurchasesAndLicenses/cs/EnableInAppPurchases.cs#InitializeLicenseTest)]

## <a name="step-2-add-the-in-app-offers-to-your-app"></a>Шаг 2. Добавление в приложение функции продажи из приложения

Для каждого компонента, который должен быть доступен как продукт внутри приложения, реализуйте функцию продажи и добавьте ее в свое приложение.

> [!IMPORTANT]
> В приложение необходимо добавить все продукты, которые предназначены для продажи клиентам, до отправки в Магазин. Если потом вы захотите добавить новые продукты из приложения, вам придется обновить приложение и отправить в Магазин новую версию.

1.  **Создание маркера продажи из приложения**

    Вы определяете каждый внутренний продукт приложения в своем приложении по маркеру. Маркер— это строка, которую вы определяете и используете в своем приложении и в Магазине для идентификации определенного продукта из приложения. Присвойте маркеру имя, понятное и уникальное для вашего приложения, чтобы быстро определять, какой компонент он представляет, при написании кода. Вот несколько примеров имен:

    * "SpaceMissionLevel4"
    * "ContosoCloudSave"
    * "RainbowThemePack"

  > [!NOTE]
  > Маркер продажи из приложения, используемый в ваш код должен соответствовать значению [код продукта](../publish/set-your-add-on-product-id.md#product-id) , указывается при [определении соответствующей надстройки для приложения в центре партнеров](../publish/add-on-submissions.md).

2.  **Помещение кода компонента в условный блок**

    Код компонента, связанного с внутренним продуктом приложения, необходимо поместить в условный блок, который проверяет наличие у пользователя лицензии на использование этого компонента.

    Вот пример кода для возможности продукта под названием **featureName** в условном блоке лицензирования. Строка **featureName**— это маркер, однозначно определяющий этот продукт в приложении и в Магазине.

    > [!div class="tabbedCodeSnippets"]
    [!code-cs[EnableInAppPurchases](./code/InAppPurchasesAndLicenses/cs/EnableInAppPurchases.cs#CodeFeature)]

3.  **Добавление пользовательского интерфейса покупки для данного компонента**

    В приложении вам также следует реализовать возможность покупки продукта или компонента, предлагаемого для продукта из приложения. Пользователи не могут приобрести их через Магазин, как приобрели само приложение.

    В следующем примере показано, как проверить, есть ли у пользователя продукт, предлагаемый для продажи из приложения, и если нет, отобразить диалоговое окно покупки. Замените комментарий "показать диалоговое окно покупки" своим кодом для вызова диалогового окна (например, страницы с понятной кнопкой "Купить это приложение") .

    > [!div class="tabbedCodeSnippets"]
    [!code-cs[EnableInAppPurchases](./code/InAppPurchasesAndLicenses/cs/EnableInAppPurchases.cs#BuyFeature)]

## <a name="step-3-change-the-test-code-to-the-final-calls"></a>Шаг 3. Замена тестового кода окончательной версией

Это просто: замените в коде приложения все ссылки на [CurrentAppSimulator](https://msdn.microsoft.com/library/windows/apps/hh779766) ссылками на [CurrentApp](https://msdn.microsoft.com/library/windows/apps/hh779765). Файл WindowsStoreProxy.xml больше не нужен, поэтому удалите его из пути приложения (хотя его можно сохранить для справки до следующего этапа настройки продажи из приложения).

## <a name="step-4-configure-the-in-app-product-offer-in-the-store"></a>Шаг 4. Настройка продажи продукта из приложения в Магазине

В центре партнеров перейдите к приложения и [Создание надстройки](../publish/add-on-submissions.md) , соответствующую предлагаемому продукту в приложении. Определите код продукта, тип, цену и другие свойства для надстройки. Убедитесь, что настройка идентична выполненной в WindowsStoreProxy.xml настройке при тестировании.

  > [!NOTE]
  > Маркер продажи из приложения, используемый в коде должно соответствовать значению [код продукта](../publish/set-your-add-on-product-id.md#product-id) , указанные при определении соответствующей надстройки в центре партнеров.

## <a name="remarks"></a>Примечания

Если вы хотите предоставить пользователям возможность покупать потребляемые товары из приложения (элементы, которые можно приобрести, использовать, а затем по желанию приобрести еще раз), перейдите к разделу [Поддержка покупок потребляемых внутренних продуктов приложения](enable-consumable-in-app-product-purchases.md).

Если вам нужны квитанции для подтверждения совершения пользователем покупки из приложения, ознакомьтесь с разделом [Проверка покупок продуктов с помощью квитанций](use-receipts-to-verify-product-purchases.md).

## <a name="related-topics"></a>Связанные разделы


* [Поддержка покупок потребляемых внутренних продуктов приложения](enable-consumable-in-app-product-purchases.md)
* [Управление большим каталогом внутренних продуктов приложения](manage-a-large-catalog-of-in-app-products.md)
* [Проверка покупок продуктов с помощью квитанций](use-receipts-to-verify-product-purchases.md)
* [Пример для Магазина (демонстрация пробных версий и покупок из приложения)](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store)
