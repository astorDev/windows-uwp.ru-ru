---
author: mcleanbyron
ms.assetid: FD381669-F962-465E-940B-AED9C8D19C90
description: "Узнайте, как использовать пространство имен Windows.Services.Store для работы с потребляемыми надстройками."
title: "Поддержка покупок потребляемых надстроек внутри приложения"
keywords: "Пример кода продажи из приложения"
translationtype: Human Translation
ms.sourcegitcommit: 962bee0cae8c50407fe1509b8000dc9cf9e847f8
ms.openlocfilehash: eb188ed8e69f90727c5b57af1c407fac07eaf87d

---

# Поддержка покупок потребляемых надстроек внутри приложения

Приложения, предназначенные для Windows 10 версии 1607 и выше, могут использовать методы класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) в пространстве имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) для управления покупкой пользователями потребляемых надстроек в приложениях UWP (надстройки также называются внутренними продуктами приложения или IAP). Используйте потребляемые надстройки для элементов, которые можно приобретать, использовать и снова приобретать. Это особенно удобно при покупке виртуальной валюты для игр (например, золота или монет), которую можно потом использовать в процессе игры.

>**Примечание.**&nbsp;&nbsp;Эта статья относится к приложениям, предназначенным для Windows 10 версии 1607 и старше. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Подробнее см. в разделе [Внутренние покупки приложения и пробные версии, использующие пространство имен Windows.ApplicationModel.Store](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## Обзор потребляемых надстроек

Приложения, предназначенные для Windows 10 версии 1607 и выше, могут предлагать два типа потребляемых надстроек, которые отличаются способом управления покупками:

* **Потребляемый элемент, управляемый разработчиком**. Для этого типа потребляемых элементов вы отвечаете за отслеживание баланса пользователя для элементов, представляемых настройкой, а также за сообщение Магазину о том, что купленная надстройка израсходована, после того как пользователь израсходует все элементы. Пользователь не может снова приобрести надстройку, пока ваше приложение не сообщит об израсходовании предыдущей покупки.

  Например, если ваша надстройка представляет 100 игровых монет и пользователь израсходовал 10 монет, ваше приложение или служба должны поддерживать для пользователя новый баланс остатка в 90 монет. Когда пользователь израсходует все 100 монет, ваше приложение должно сообщить, что надстройка израсходована, после чего пользователь снова сможет приобрести 100 монет из надстройки.

* **Потребляемый элемент, управляемый Магазином**. Для этого типа потребляемых элементов, представляемых надстройкой, учет баланса пользователя производится Магазином. Когда пользователь использует какие-либо элементы, вы должны сообщить Магазину, что эти элементы израсходованы, после чего Магазин обновляет баланс пользователя. Приложение может в любой момент запросить текущий баланс пользователя. Когда пользователь израсходует все элементы, он снова может приобрести надстройку.

  Например, если надстройка представляет начальную сумму в 100 игровых монет и пользователь израсходовал 10 монет, приложение сообщает Магазину, что израсходованы 10 единиц надстройки, и Магазин обновляет баланс. Когда пользователь израсходует все 100 монет, он сможет снова купить 100 монет надстройки.

  >**Примечание.**&nbsp;&nbsp;Потребляемые элементы, управляемые Магазином, доступны начиная с Windows 10 версии 1607. Скоро будет реализована возможность создавать потребляемые элементы, управляемые Магазином, на информационной панели Центра разработки для Windows.

Чтобы предложить пользователю потребляемую надстройку, следуйте приведенному ниже общему процессу.

1. Разрешите пользователями [приобретать надстройку](enable-in-app-purchases-of-apps-and-add-ons.md) из приложения.
3. Когда пользователь израсходует надстройку (например, истратит монеты в игре), [сообщите, что надстройка израсходована](enable-consumable-add-on-purchases.md#report_fulfilled).

В любой момент можно также [получить оставшийся баланс](enable-consumable-add-on-purchases.md#get_balance) для потребляемого элемента, управляемого Магазином.

## Предварительные условия и необходимые компоненты

Для этих примеров необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для Windows 10 версии 1607 и выше.
* На панели Центра разработки для Windows создано приложение с потребляемыми надстройками (также называемыми внутренними продуктами приложения или IAP), и это приложение опубликовано и доступно в Магазине. Это может быть приложение, которое вы хотите предложить пользователям, или базовое приложение, соответствующее минимальным требованиям [комплекта сертификации приложений для Windows](https://developer.microsoft.com/windows/develop/app-certification-kit) и используемое только для тестирования. Подробнее см. в [руководстве по тестированию](in-app-purchases-and-trials.md#testing).

В коде из этих примеров предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение является однопользовательским и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

>**Примечание.**&nbsp;&nbsp;Если у вас есть классическое приложение, которое использует [мост для настольных ПК](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этих примерах, для настройки объекта [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

<span id="report_fulfilled" />
## Сообщение о том, что потребляемая надстройка израсходована

После того как пользователь [приобрел надстройку](enable-in-app-purchases-of-apps-and-add-ons.md) из приложения и израсходовал ее, приложение должно сообщить об израсходовании надстройки, вызвав метод [ReportConsumableFulfillmentAsync](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.reportconsumablefulfillmentasync.aspx) класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Этому методу необходимо передать следующую информацию:

* [Код продукта в Магазине](in-app-purchases-and-trials.md#store_ids) для надстройки, об израсходовании которой требуется сообщить.
* Единицы надстройки, об израсходовании которой требуется сообщить.
  * В случае потребляемого элемента, управляемого разработчиком, укажите для параметра *quantity* значение «1». Таким образом Магазин извещается, что потребляемый элемент израсходован, и пользователь снова может приобрести этот потребляемый элемент. Пользователь не может снова приобрести потребляемый элемент, пока приложение не уведомит Магазин о том, что этот элемент израсходован.
  * В случае потребляемого элемента, управляемого Магазином, укажите фактическое число израсходованных единиц. Магазин обновит оставшийся баланс для потребляемого элемента.
* Код отслеживания для расхода. Это предоставляемый разработчиком идентификатор GUID, который определяет конкретную транзакцию, с которой связана операция расхода для целей отслеживания. Подробнее см. в примечаниях в разделе [ReportConsumableFulfillmentAsync](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.reportconsumablefulfillmentasync.aspx).

Этот пример показывает, как сообщить об израсходовании потребляемого элемента, управляемого Магазином.

```csharp
private StoreContext context = null;

public async void ConsumeAddOn(string storeId)
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
        // If your app is a desktop app that uses the Desktop Bridge, you
        // may need additional code to configure the StoreContext object.
        // For more info, see https://aka.ms/storecontext-for-desktop.
    }

    // This is an example for a Store-managed consumable, where you specify the actual number
    // of units that you want to report as consumed so the Store can update the remaining
    // balance. For a developer-managed consumable where you maintain the balance, specify 1
    // to just report the add-on as fulfilled to the Store.
    uint quantity = 10;
    string addOnStoreId = "9NBLGGH4TNNR";
    Guid trackingId = Guid.NewGuid();

    workingProgressRing.IsActive = true;
    StoreConsumableResult result = await context.ReportConsumableFulfillmentAsync(
        addOnStoreId, quantity, trackingId);
    workingProgressRing.IsActive = false;

    if (result.ExtendedError != null)
    {
        // The user may be offline or there might be some other server failure.
        textBlock.Text = $"ExtendedError: {result.ExtendedError.Message}";
        return;
    }

    switch (result.Status)
    {
        case StoreConsumableStatus.Succeeded:
            textBlock.Text = "The fulfillment was successful. Remaining balance: " +
                result.BalanceRemaining;
            break;

        case StoreConsumableStatus.InsufficentQuantity:
            textBlock.Text = "The fulfillment was unsuccessful because the user " +
            "doesn't have enough remaining balance." + result.BalanceRemaining;
            break;

        case StoreConsumableStatus.NetworkError:
            textBlock.Text = "The fulfillment was unsuccessful due to a network error.";
            break;

        case StoreConsumableStatus.ServerError:
            textBlock.Text = "The fulfillment was unsuccessful due to a server error.";
            break;

        default:
            textBlock.Text = "The fulfillment was unsuccessful due to an unknown error.";
            break;
    }
}
```

<span id="get_balance" />
## Получение оставшегося баланса для потребляемого элемента, управляемого Магазином

Этот пример демонстрирует использование метода [GetConsumableBalanceRemainingAsync](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.getconsumablebalanceremainingasync.aspx) класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) для получения оставшегося баланса потребляемой надстройки, управляемой Магазином.

```csharp
private StoreContext context = null;

public async void GetRemainingBalance(string storeId)
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
        // If your app is a desktop app that uses the Desktop Bridge, you
        // may need additional code to configure the StoreContext object.
        // For more info, see https://aka.ms/storecontext-for-desktop.
    }

    string addOnStoreId = "9NBLGGH4TNNR";

    workingProgressRing.IsActive = true;
    StoreConsumableResult result = await context.GetConsumableBalanceRemainingAsync(addOnStoreId);
    workingProgressRing.IsActive = false;

    if (result.ExtendedError != null)
    {
        // The user may be offline or there might be some other server failure.
        textBlock.Text = $"ExtendedError: {result.ExtendedError.Message}";
        return;
    }

    switch (result.Status)
    {
        case StoreConsumableStatus.Succeeded:
            textBlock.Text = "Remaining balance: " + result.BalanceRemaining;
            break;

        case StoreConsumableStatus.NetworkError:
            textBlock.Text = "Could not retrieve balance due to a network error.";
            break;

        case StoreConsumableStatus.ServerError:
            textBlock.Text = "Could not retrieve balance due to a server error.";
            break;

        default:
            textBlock.Text = "Could not retrieve balance due to an unknown error.";
            break;
    }
}
```

## Связанные разделы

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md)
* [Получение информации о лицензии для приложений и надстроек](get-license-info-for-apps-and-add-ons.md)
* [Поддержка покупок приложений и надстроек внутри приложения](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)



<!--HONumber=Nov16_HO1-->


