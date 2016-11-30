---
author: mcleanbyron
ms.assetid: 9630AF6D-6887-4BE3-A3CB-D058F275B58F
description: "Узнайте, как использовать пространство имен Windows.Services.Store для получения информации о лицензии для текущего приложения и его надстроек."
title: "Получение информации о лицензии для приложения и надстроек"
translationtype: Human Translation
ms.sourcegitcommit: 18d5c2ecf7d438355c3103ad2aae32dc84fc89ed
ms.openlocfilehash: 710800bcd5491407d90e8293006a687e27d06d2d

---

# Получение информации о лицензии для приложений и надстроек

Приложения, предназначенные для Windows 10 версии 1607 и выше, могут использовать методы класса [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx) из пространства имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx) для получения информации о лицензии для текущего приложения и его надстроек (также известных как внутренние продукты приложения или IAP). Например, с помощью этих сведений можно определить, активны ли лицензии для приложения или его надстроек или они являются лицензиями пробной версии.

>**Примечание.**&nbsp;&nbsp;Эта статья относится к приложениям, предназначенным для Windows 10 версии 1607 и старше. Если приложение предназначено для предыдущих версий Windows 10, необходимо использовать пространство имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx), а не пространство имен **Windows.Services.Store**. Подробнее см. в разделе [Внутренние покупки приложения и пробные версии, использующие пространство имен Windows.ApplicationModel.Store](in-app-purchases-and-trials-using-the-windows-applicationmodel-store-namespace.md).

## Предварительные условия и необходимые компоненты

Для этого примера необходимо выполнение следующих предварительных условий:
* Создан проект Visual Studio для приложения универсальной платформы Windows (UWP), предназначенный для Windows 10 версии 1607 и выше.
* Создано приложение на панели Центра разработки для Windows, и это приложение опубликовано и доступно в Магазине. Это может быть приложение, которое вы хотите предложить пользователям, или базовое приложение, соответствующее минимальным требованиям [комплекта сертификации приложений для Windows](https://developer.microsoft.com/windows/develop/app-certification-kit) и используемое только для тестирования. Подробнее см. в [руководстве по тестированию](in-app-purchases-and-trials.md#testing).

В коде из этого примера предполагается следующее:
* Код выполняется в контексте страницы [Page](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.page.aspx), которая содержит [ProgressRing](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.progressring.aspx) с именем ```workingProgressRing``` и [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) с именем ```textBlock```. Эти объекты используются для индикации выполнения асинхронной операции и отображения выводимых сообщений, соответственно.
* Файл кода содержит оператор **using** для пространства имен **Windows.Services.Store**.
* Приложение — однопользовательское и выполняется только в контексте пользователя, запустившего его. Подробнее см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md#api_intro).

>**Примечание.**&nbsp;&nbsp;Если у вас есть классическое приложение, которое использует [мост для настольных ПК](https://developer.microsoft.com/windows/bridges/desktop), вам может потребоваться добавить дополнительный код, не показанный в этом примере, для настройки объекта [StoreContext](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.aspx). Дополнительные сведения см. в разделе [Использование класса StoreContext в классическом приложение, в котором применяется мост для настольных компьютеров](in-app-purchases-and-trials.md#desktop).

## Пример кода

Для получения информации о лицензии для текущего приложения используйте метод [GetAppLicenseAsync](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storecontext.getapplicenseasync.aspx). Это асинхронный метод, который возвращает объект [StoreAppLicense](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.aspx), предоставляющий сведения о лицензии для приложения, включая свойства, указывающие, имеется ли у пользователя лицензия на использование приложения ([IsActive](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.isactive.aspx)) и распространяется ли лицензия на пробную версию ([IsTrial](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.istrial.aspx)).

Чтобы извлечь лицензии на надстройки для приложения, используйте свойство [AddOnLicenses](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.addonlicenses.aspx) объекта [StoreAppLicense](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storeapplicense.aspx). Это свойство возвращает коллекцию объектов [StoreLicense](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storelicense.aspx), представляющих лицензии на надстройки для приложения. Чтобы определить, есть ли у пользователя лицензия на использование надстройки, используйте свойство [IsActive](https://msdn.microsoft.com/library/windows/apps/windows.services.store.storelicense.isactive.aspx).

```csharp
private StoreContext context = null;

public async void GetLicenseInfo()
{
    if (context == null)
    {
        context = StoreContext.GetDefault();
        // If your app is a desktop app that uses the Desktop Bridge, you
        // may need additional code to configure the StoreContext object.
        // For more info, see https://aka.ms/storecontext-for-desktop.
    }

    workingProgressRing.IsActive = true;
    StoreAppLicense appLicense = await context.GetAppLicenseAsync();
    workingProgressRing.IsActive = false;

    if (appLicense == null)
    {
        textBlock.Text = "An error occurred while retrieving the license.";
        return;
    }

    // Use members of the appLicense object to access license info...

    // Access the add on licenses for add-ons for this app.
    foreach (KeyValuePair<string, StoreLicense> item in appLicense.AddOnLicenses)
    {
        StoreLicense addOnLicense = item.Value;
        // Use members of the addOnLicense object to access license info
        // for the add-on...
    }
}
```

Полный пример приложения см. в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store).

## Связанные статьи

* [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md)
* [Получение информации о продукте для приложений и надстроек](get-product-info-for-apps-and-add-ons.md)
* [Поддержка покупок приложений и надстроек внутри приложения](enable-in-app-purchases-of-apps-and-add-ons.md)
* [Поддержка покупок потребляемых надстроек внутри приложения](enable-consumable-add-on-purchases.md)
* [Реализация пробной версии приложения](implement-a-trial-version-of-your-app.md)
* [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)



<!--HONumber=Nov16_HO1-->


