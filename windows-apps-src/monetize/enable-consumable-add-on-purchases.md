---
author: Xansky
ms.assetid: FD381669-F962-465E-940B-AED9C8D19C90
description: Узнайте, как использовать пространство имен Windows.Services.Store для работы с потребляемыми надстройками.
title: Поддержка покупок потребляемых надстроек
keywords: windows 10, uwp, потребляемые, надстройки, покупки из приложения, IAP, Windows.Services.Store
ms.author: mhopkins
ms.date: 05/09/2018
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: fc67e2b48779e7b22f20dc4adfbe28580bc325fc
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "5883288"
---
# <a name="enable-consumable-add-on-purchases"></a>Поддержка покупок потребляемых надстроек

В этой статье рассказывается, как использовать методы класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) в пространстве имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) для управления покупкой пользователями потребляемых надстроек в приложениях UWP. Используйте потребляемые надстройки для элементов, которые можно приобретать, использовать и снова приобретать. Это особенно удобно при покупке виртуальной валюты для игр (золота, монет и др.), которую можно потом использовать в процессе игры.

> [!NOTE]
> Пространство имен **Windows.Services.Store** впервые появилось в Windows 10 версии 1607 и может использоваться только в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Дополнительные сведения см. в [этой статье](enable-consumable-in-app-product-purchases.md).

## <a name="overview-of-consumable-add-ons"></a>Обзор потребляемых надстроек

Приложения могут предлагать два типа потребляемых надстроек, которые отличаются способом управления покупками:

* **Потребляемый элемент, управляемый разработчиком**. Для этого типа потребляемых элементов вы отвечаете за отслеживание баланса пользователя для элементов, представляемых настройкой, а также за сообщение Магазину о том, что купленная надстройка израсходована, после того как пользователь израсходует все элементы. Пользователь не может снова приобрести надстройку, пока ваше приложение не сообщит об израсходовании предыдущей покупки.

  Например, если ваша надстройка представляет 100 игровых монет и пользователь израсходовал 10 монет, ваше приложение или служба должны поддерживать для пользователя новый баланс остатка в 90 монет. Когда пользователь израсходует все 100 монет, ваше приложение должно сообщить, что надстройка израсходована, после чего пользователь снова сможет приобрести 100 монет из надстройки.

* **Потребляемый элемент, управляемый Магазином**. Для этого типа потребляемых элементов, представляемых надстройкой, учет баланса пользователя производится Магазином. Когда пользователь использует какие-либо элементы, вы должны сообщить Магазину, что эти элементы израсходованы, после чего Магазин обновляет баланс пользователя. Пользователь может приобрести надстройку сколько угодно раз (для этого не требуется сначала использовать элементы). Приложение может в любой момент запросить текущий баланс пользователя в Store.

  Например, если надстройка представляет начальную сумму в 100 игровых монет и пользователь израсходовал 50 монет, приложение сообщает Store, что израсходованы 50 единиц надстройки, и Store обновляет баланс. Если пользователь затем снова приобретает надстройку, чтобы получить еще 100 монет, общее число монет составит 150.
    > [!NOTE]
    > Потребляемые надстройки, управляемые Store, были представлены в Windows 10 (версия 1607).

Чтобы предложить пользователю потребляемую надстройку, следуйте приведенному ниже общему процессу.

1. Разрешите пользователями [приобретать надстройку](enable-in-app-purchases-of-apps-and-add-ons.md) из приложения.
3. Когда пользователь израсходует надстройку (например, истратит монеты в игре), [сообщите, что надстройка израсходована](enable-consumable-add-on-purchases.md#report_fulfilled).

В любой момент можно также [получить оставшийся баланс](enable-consumable-add-on-purchases.md#get_balance) для потребляемого элемента, управляемого Магазином.

## <a name="prerequisites"></a>Предварительные условия и необходимые компоненты

Для этих примеров необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для **Windows 10 Anniversary Edition (10.0; сборка 14393)** и более поздних выпусков.
* Создана [отправка приложения](https://msdn.microsoft.com/windows/uwp/publish/app-submissions) на информационной панели Центра разработки для Windows, и это приложение опубликовано в Магазине. При необходимости можно настроить приложение, чтобы его нельзя было найти в Магазине, пока вы его тестируете. Подробнее см. в нашем [руководстве по тестированию](in-app-purchases-and-trials.md#testing).
* Создана [потребляемая надстройка для приложения](../publish/add-on-submissions.md) на информационной панели Центра разработки.

В коде из этих примеров предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение является однопользовательским и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

> [!NOTE]
> Если у вас есть классическое приложение, которое использует [мост для классических приложений](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этих примерах, для настройки объекта [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

<span id="report_fulfilled" />

## <a name="report-a-consumable-add-on-as-fulfilled"></a>Сообщение о том, что потребляемая надстройка израсходована

После того как пользователь [приобрел надстройку](enable-in-app-purchases-of-apps-and-add-ons.md) из приложения и израсходовал ее, приложение должно сообщить об израсходовании надстройки, вызвав метод [ReportConsumableFulfillmentAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.reportconsumablefulfillmentasync) класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Этому методу необходимо передать следующую информацию:

* [Код продукта в Магазине](in-app-purchases-and-trials.md#store-ids) для надстройки, об израсходовании которой требуется сообщить.
* Единицы надстройки, об израсходовании которой требуется сообщить.
  * В случае потребляемого элемента, управляемого разработчиком, укажите для параметра *quantity* значение «1». Таким образом Магазин извещается, что потребляемый элемент израсходован, и пользователь снова может приобрести этот потребляемый элемент. Пользователь не может снова приобрести потребляемый элемент, пока приложение не уведомит Магазин о том, что этот элемент израсходован.
  * В случае потребляемого элемента, управляемого Магазином, укажите фактическое число израсходованных единиц. Магазин обновит оставшийся баланс для потребляемого элемента.
* Код отслеживания для расхода. Это предоставляемый разработчиком идентификатор GUID, который определяет конкретную транзакцию, с которой связана операция расхода для целей отслеживания. Подробнее см. в примечаниях в разделе [ReportConsumableFulfillmentAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.reportconsumablefulfillmentasync).

Этот пример показывает, как сообщить об израсходовании потребляемого элемента, управляемого Магазином.

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnableConsumables](./code/InAppPurchasesAndLicenses_RS1/cs/ConsumeAddOnPage.xaml.cs#ConsumeAddOn)]

<span id="get_balance" />

## <a name="get-the-remaining-balance-for-a-store-managed-consumable"></a>Получение оставшегося баланса для потребляемого элемента, управляемого Магазином

Этот пример демонстрирует использование метода [GetConsumableBalanceRemainingAsync](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.getconsumablebalanceremainingasync) класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) для получения оставшегося баланса потребляемой надстройки, управляемой Магазином.

> [!div class="tabbedCodeSnippets"]
[!code-cs[EnableConsumables](./code/InAppPurchasesAndLicenses_RS1/cs/GetRemainingAddOnBalancePage.xaml.cs#GetRemainingAddOnBalance)]

## <a name="related-topics"></a>Связанные разделы

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md)
* [Получение информации о лицензии для приложений и надстроек](get-license-info-for-apps-and-add-ons.md)
* [Поддержка покупок приложений и надстроек внутри приложения](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)
