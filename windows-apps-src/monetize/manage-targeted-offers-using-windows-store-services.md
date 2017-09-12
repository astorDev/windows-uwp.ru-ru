---
author: mcleanbyron
ms.assetid: 9F0A59A1-FAD7-4AD5-B78B-C1280F215D23
description: "Использование API целевых предложений Магазина Windows для заявки на целевые предложения, доступные для приложения."
title: "Управление целевыми предложениями с помощью служб Магазина"
ms.author: mcleans
ms.date: 05/11/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, службы Магазина, API целевых предложений Магазина Windows, целевые предложения"
ms.openlocfilehash: 684c37d4439f415ad607b7f3e6a166966cc9f835
ms.sourcegitcommit: eaacc472317eef343b764d17e57ef24389dd1cc3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2017
---
# <a name="manage-targeted-offers-using-store-services"></a>Управление целевыми предложениями с помощью служб Магазина

При создании *целевого предложения* на странице **Привлечение > Целевые предложения** для вашего приложения на информационной панели Центра разработки для Windows используйте *API целевых предложений Магазина Windows* в коде приложения, чтобы реализовать взаимодействие в приложении для целевого предложения. Дополнительные сведения о целевых предложениях и способах их создания на информационной панели см. в разделе [Использование целевых предложений для повышения вовлеченности и количества конверсий](../publish/use-targeted-offers-to-maximize-engagement-and-conversions.md).

API целевых предложений— это API REST, который можно использовать для выполнения описанных ниже задач.

* Получайте целевые предложения, которые доступны для текущего пользователя, в зависимости от того, является ли пользователь частью клиентского сегмента для целевого предложения.
* Если пользователь приобретает целевое предложение, вы можете отправить заявку в Магазин, чтобы получать бонус, связанный с целевым предложением. Это требуется, только если целевое предложение связано с рекламной акцией, спонсируемой Майкрософт, в рамках которой выплачивается бонус разработчикам за каждую успешную конверсию.

Для использования этого API в коде приложения выполните следующие действия:

1.  [Получите маркер учетной записи Майкрософт](#obtain-a-microsoft-account-token) для текущего пользователя приложения, который вошел в систему.
2.  [Получите целевые предложения для текущего пользователя](#get-targeted-offers).
3.  [Приобретите надстройку, которая связана с целевым предложением](#purchase-add-on).
3.  [Заявка на целевое предложение](#claim-targeted-offer) (если целевое предложение связано с рекламной акцией, спонсируемой Майкрософт, в рамках которой выплачивается бонус разработчикам за каждую успешную конверсию).

> [!NOTE]
> Возможность связать целевое предложение с рекламной акцией, спонсируемой Майкрософт, а затем отправить заявку для предложения на данный момент доступна не всем учетным записям разработчика.

Полный пример кода, демонстрирующий все эти действия, см. в [примере кода](#code-example) в конце данной статьи. Все эти действия более подробно описаны в следующих разделах.

<span id="obtain-a-microsoft-account-token" />
## <a name="get-a-microsoft-account-token-for-the-current-user"></a>Получите маркер учетной записи Майкрософт для текущего пользователя

В коде приложения получите маркер учетной записи Майкрософт (MSA) для текущего пользователя, вошедшего в систему. Необходимо передать этот маркер в заголовок запроса ```Authorization``` для каждого из методов в API целевых предложений Магазина Windows. Этот маркер используется в Магазине для получения целевых предложений, которые доступны для текущего пользователя.

Чтобы получить маркер MSA, используйте класс [WebAuthenticationCoreManager](https://docs.microsoft.com/uwp/api/windows.security.authentication.web.core.webauthenticationcoremanager) для запроса маркера с помощью области ```devcenter_implicit.basic,wl.basic```. В приведенном ниже примере показано, как это сделать. В этом примере приведен фрагмент из [полного примера](#code-example), и он требует **использования** инструкций, которые содержатся в полном примере.

[!code-cs[TargetedOffers](./code/StoreServicesExamples_TargetedOffers/cs/TargetedOffers.cs#GetMSAToken)]

Дополнительные сведения о получении маркеров MSA см. в разделе [Диспетчер учетных записей в Интернете](../security/web-account-manager.md).

<span id="get-targeted-offers" />
## <a name="get-the-targeted-offers-for-the-current-user"></a>Получение целевых предложений для текущего пользователя

После получения маркера MSA для текущего пользователя вызовите метод GET URI ```https://manage.devcenter.microsoft.com/v2.0/my/storeoffers/user``` для получения целевых предложений, доступных для текущего пользователя. Дополнительные сведения об этом методе REST см. в разделе [Получение целевых предложений](get-targeted-offers.md).

Этот метод возвращает ИД продуктов для надстроек, связанных с целевыми предложениями, доступными для текущего пользователя. С помощью этих сведений вы можете показать пользователю одно или несколько целевых предложений как покупку из приложения. Этот метод также возвращает идентификатор отслеживания, который можно использовать для [отправки заявки](#claim-targeted-offer) в Магазин, чтобы вы могли получать любой бонус, связанный с целевыми предложениями.

В примере ниже показано, как получить целевые предложения для текущего пользователя. Этот пример представляет собой фрагмент [полного примера](#code-example). Он требует библиотеку [Json.NET](http://www.newtonsoft.com/json) от Newtonsoft, а также дополнительные классы и инструкции **using**, которые содержатся в полном примере.

[!code-cs[TargetedOffers](./code/StoreServicesExamples_TargetedOffers/cs/TargetedOffers.cs#GetTargetedOffers)]

<span id="purchase-add-on" />
## <a name="purchase-the-add-on-that-is-associated-with-a-targeted-offer"></a>Приобретение надстройки, связанной с целевым предложением

Далее покажите пользователю одно или больше целевых предложений для приобретения. Если пользователь согласится приобрести целевое предложение, используйте один из следующих методов для программного приобретения надстройки, связанной с целевым предложением. Используйте значения кода продукта, полученные при выполнении предыдущего шага, чтобы определить нужную надстройку для приобретения.

* Если ваше приложение предназначено для Windows 10 версии 1607 или более поздней, рекомендуется использовать один из методов **RequestPurchaseAsync** в пространстве имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store). Дополнительные сведения об использовании этих методов см. в разделе [Включение покупок из приложений— приложений и надстроек](enable-in-app-purchases-of-apps-and-add-ons.md).

* Если приложение предназначено для более ранней версии Windows 10, используйте метод [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp#Windows_ApplicationModel_Store_CurrentApp_RequestProductPurchaseAsync_System_String_) в пространстве имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx) для приобретения надстройки. Дополнительные сведения об использовании этого метода см. в разделе [Поддержка покупок продуктов из приложения](enable-in-app-product-purchases.md).

Фрагменты кода, демонстрирующие все эти возможности, см. в [примере кода](#code-example) в конце данной статьи.

> [!NOTE]
> Существует два разных пространства имен для реализации покупок из приложения в приложении UWP: [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) (введено в Windows 10 версии 1607) и [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx) (доступно во всех версиях Windows 10). Дополнительные сведения о различиях между этими пространствами имен см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md).

<span id="claim-targeted-offer" />
## <a name="submit-a-claim-for-a-targeted-offer"></a>Отправка заявки для целевого предложения

Если пользователь покупает целевое предложение, которое связано с рекламной акцией, спонсируемой Майкрософт, в рамках которой выплачивается бонус разработчикам за каждую успешную конверсию, вы должны отправить заявку в Магазин, чтобы получать бонус. При отправке заявки необходимо указать значение, представляющее подтверждение покупки. Способ получения этого подтверждения покупки зависит от того, использует ли ваше приложение API-интерфейсы в пространстве имен **Windows.ApplicationModel.Store** или пространстве имен **Windows.Services.Store** для покупки надстройки.

> [!NOTE]
> Возможность связать целевое предложение с рекламной акцией, спонсируемой Майкрософт, а затем отправить заявку для предложения на данный момент доступна не всем учетным записям разработчика.

### <a name="apps-that-use-the-windowsapplicationmodelstore-namespace"></a>Приложения, использующие пространство имен Windows.ApplicationModel.Store

1. После покупки надстройки с помощью метода [RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store.CurrentApp#Windows_ApplicationModel_Store_CurrentApp_RequestProductPurchaseAsync_System_String_) в пространстве имен **Windows.ApplicationModel.Store** убедитесь, что вы получили [накладную покупки](use-receipts-to-verify-product-purchases.md). Эта накладная доступна в объекте [PurchaseResults.ReceiptXml](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.purchaseresults#Windows_ApplicationModel_Store_PurchaseResults_ReceiptXml_), который возвращается методом **RequestProductPurchaseAsync**. Эта накладная представляет собой подтверждение покупки, которое необходимо использовать при выполнении следующего шага.

2. Отправьте сообщение POST в URI ```https://manage.devcenter.microsoft.com/v2.0/my/storeoffers/user```, чтобы подать заявку для конверсии по целевому предложению. Дополнительные сведения об этом методе REST см. в разделе [Заявка на целевое предложение](claim-a-targeted-offer.md). В тексте запроса передайте следующие данные.

  * Поле *storeOffer*: передайте один из объектов JSON, которые вы получили в тексте ответа метода [Получение целевых предложений](get-targeted-offers.md) (этот объект должен включать поля *offers* и *trackingId*).

  * Поле *receipt*: передайте строку накладной, полученной на предыдущем шаге (доступна в объекте **PurchaseResults.ReceiptXml**, который возвращается методом **RequestProductPurchaseAsync**).

В примере ниже показано, как совершать покупки и заявить о целевом предложении с помощью API в пространстве имен **Windows.ApplicationModel.Store**. Этот пример представляет собой фрагмент [полного примера](#code-example). Он требует библиотеку [Json.NET](http://www.newtonsoft.com/json) от Newtonsoft, а также дополнительные классы и инструкции **using**, которые содержатся в полном примере.

[!code-cs[TargetedOffers](./code/StoreServicesExamples_TargetedOffers/cs/TargetedOffers.cs#ClaimOfferOnAnyVersionWindows10)]

### <a name="apps-that-use-the-windowsservicesstore-namespace"></a>Приложения, использующие пространство имен Windows.Services.Store

1. После покупки надстройки одним из методов **RequestPurchaseAsync** в пространстве имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store), получите *идентификатор заказа* для этой покупки, выполнив следующие действия. Это значение представляет подтверждение покупки.

  1. Вызовите метод [GetUserCollectionAsync](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreContext#Windows_Services_Store_StoreContext_GetUserCollectionAsync_Windows_Foundation_Collections_IIterable_System_String__), чтобы получить коллекцию объектов [StoreProduct](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeproduct.aspx), представляющих приобретенные пользователем надстройки.

  2. Из этой коллекции получите объект **StoreProduct**, который представляет надстройку, приобретенную для целевого предложения.

  3. Получите значение свойства [ExtendedJsonData](https://docs.microsoft.com/uwp/api/Windows.Services.Store.StoreProduct#Windows_Services_Store_StoreProduct_ExtendedJsonData_) этого объекта **StoreProduct**. Это свойство возвращает строку формата JSON, содержащую все относящиеся к Магазину данные для надстройки.

  4. Обработайте эту строку JSON и получите значение поля *orderId* в строковую переменную.

2. Отправьте сообщение POST в URI ```https://manage.devcenter.microsoft.com/v2.0/my/storeoffers/user```, чтобы подать заявку для конверсии по целевому предложению. Дополнительные сведения об этом методе REST см. в разделе [Заявка на целевое предложение](claim-a-targeted-offer.md). В тексте запроса передайте следующие объекты.

  * Поле *storeOffer*: передайте один из объектов JSON, которые вы получили в тексте ответа метода [Получение целевых предложений](get-targeted-offers.md) (этот объект должен включать поля *offers* и *trackingId*).

  * Поле *receipt*: передайте значение *orderId*, полученное в предыдущем шаге.

В примере ниже показано, как совершать покупки и заявить о целевом предложении с помощью API в пространстве имен **Windows.Services.Store**. Этот пример представляет собой фрагмент [полного примера](#code-example). Он требует библиотеку [Json.NET](http://www.newtonsoft.com/json) от Newtonsoft, а также дополнительные классы и инструкции **using**, которые содержатся в полном примере.

[!code-cs[TargetedOffers](./code/StoreServicesExamples_TargetedOffers/cs/TargetedOffers.cs#ClaimOfferOnWindows1607AndLater)]

<span id="code-example" />
## <a name="complete-code-example"></a>Полный пример кода

В следующем примере кода показаны следующие задачи:

* Получение маркера MSA для текущего пользователя.
* Получение всех целевых предложений для текущего пользователя с помощью метода [Получение целевого предложения](get-targeted-offers.md).
* Приобретите надстройку, которая связана с целевым предложением.
* Отправьте заявку на целевое предложение с помощью метода [Заявка на целевое предложение](claim-a-targeted-offer.md).

Для этого примера требуется библиотека [Json.NET](http://www.newtonsoft.com/json) от Newtonsoft. В примере эта библиотека используется для сериализации и десериализации данных формата JSON.

> [!NOTE]
> Существует два разных пространства имен для реализации покупок из приложения в приложении UWP: [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) (введено в Windows 10 версии 1607) и [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx) (доступно во всех версиях Windows 10). В этом примере используется [адаптивный по версиям код](../debug-test-perf/version-adaptive-code.md) для использования двух этих пространств имен в одном приложении в целях покупки надстройки и заявления о целевом предложении. Чтобы использовать этот код, целевая версия ОС вашего проекта должна быть **Windows 10 Anniversary Edition (10.0; сборка 14394)** или более поздней, хотя минимальная версия ОС может быть более ранней. Дополнительные сведения о различиях между этими пространствами имен см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md).

[!code-cs[TargetedOffers](./code/StoreServicesExamples_TargetedOffers/cs/TargetedOffers.cs#GetTargetedOffersSample)]

## <a name="related-topics"></a>Еще по теме:

* [Используйте целевые предложения, чтобы повысить вовлеченность и количество конверсий](../publish/use-targeted-offers-to-maximize-engagement-and-conversions.md)
* [Получение целевых предложений](get-targeted-offers.md)
* [Заявка на целевое предложение](claim-a-targeted-offer.md)
