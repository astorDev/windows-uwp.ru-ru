---
author: mcleanbyron
ms.assetid: 3aeddb83-5314-447b-b294-9fc28273cd39
description: "Узнайте о том, как устанавливать библиотеки Microsoft Advertising."
title: "Установка библиотек Microsoft Advertising"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, реклама, рекламные объявления, установка, SDK, библиотеки"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 034b597c945f9f12700ac442e4b3014b0bc84c78
ms.lasthandoff: 02/07/2017

---

# <a name="install-the-microsoft-advertising-libraries"></a>Установка библиотек Microsoft Advertising




Для приложений универсальной платформы Windows (UWP) для Windows 10 библиотеки Microsoft Advertising включены в пакет [Microsoft Store Services SDK](http://aka.ms/store-em-sdk). Этот пакет SDK является расширением Visual Studio 2015 и последующих версий. Дополнительные сведения об установке этого пакета SDK см. [в этом разделе](microsoft-store-services-sdk.md).

> **Примечание.**&nbsp;&nbsp;Если вы установили пакет SDK Windows 10 (14393) или более новой версии, необходимо также установить библиотеку WinJS, если требуется добавлять рекламу на JavaScript/HTML в приложения UWP. Эта библиотека ранее включалась в предыдущие версии SDK для Windows 10, но начиная с пакета SDK для Windows 10 (14393) ее необходимо устанавливать отдельно. Сведения по установке WinJS см. в разделе [Скачать WinJS](http://try.buildwinjs.com/download/GetWinJS/).

Для приложений XAML и JavaScript/HTML для Windows 8.1 и Windows Phone 8.x библиотеки Microsoft Advertising входят в пакет [Microsoft Advertising SDK для Windows и Windows Phone 8.x](http://aka.ms/store-8-sdk). Этот пакет SDK является расширением Visual Studio 2015 или Visual Studio 2013.

Для приложений Windows Phone Silverlight 8.x библиотеки Microsoft Advertising доступны в пакете NuGet, который можно загрузить и установить в проект. Дополнительные сведения см. в разделе [AdControl в Windows Phone Silverlight](adcontrol-in-windows-phone-silverlight.md).

## <a name="library-names-for-advertising"></a>Имена библиотек для рекламы


Существует несколько различных рекламных библиотек в пакетах Microsoft Store Services SDK и Microsoft Advertising SDK для Windows и Windows Phone 8.x:

* В пакет Microsoft Store Services SDK входят библиотеки Microsoft Advertising (предоставляющие классы [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) и [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) для приложений XAML и JavaScript/HTML).

* В пакет Microsoft Advertising SDK для Windows и Windows Phone 8.x входят два набора рекламных библиотек: библиотеки для Microsoft Advertising (предоставляющие классы [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) и [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) для приложений XAML и JavaScript/HTML) и библиотеки для рекламного посредника (предоставляющие класс **AdMediatorControl**).

В этом документе описано использование классов **AdControl** и **InterstitialAd** в библиотеках Microsoft Advertising для отображения рекламных баннеров или промежуточной видеорекламы. Сведения об использовании рекламного посредника для приложений Windows 8.1 и Windows Phone 8.x см. в разделе [Использование рекламного посредника для повышения дохода](https://msdn.microsoft.com/library/windows/apps/xaml/dn864359.aspx).

>**Примечание.**&nbsp;&nbsp;Рекламный посредник с использованием класса **AdMediatorControl** в настоящее время не поддерживается для приложений UWP для Windows 10. Посредники на стороне сервера для приложений UWP станут доступны в ближайшее время на основе тех же API-интерфейсов, что используются для баннеров (**AdControl**) и промежуточной видеорекламы (**InterstitialAd**).

Прежде чем использовать какие-либо элементы управления рекламой в коде приложения, необходимо создать в проекте ссылку на нужную библиотеку. В следующих таблицах перечислены названия всех библиотек в том виде, в котором они отображаются в диалоговом окне **Диспетчер ссылок** в Visual Studio.


<table>
    <thead>
        <tr><th>Название элемента управления</th><th>Тип проекта</th><th>Название библиотеки в диспетчере ссылок</th><th>Номер версии</th></tr>
    </thead>
    <tbody>
    <tr>
            <td rowspan="3">**AdControl** и **InterstitialAd** (XAML)</td>
            <td>UWP</td>
            <td>SDK Microsoft Advertising для XAML</td>
            <td>10.0</td>
        </tr>
        <tr>
            <td>Windows 8.1</td>
            <td>SDK Ad Mediator для XAML в Windows 8.1</td>
            <td>1.0</td>
        </tr>
        <tr>
            <td>Windows Phone 8.1</td>
            <td>SDK Ad Mediator для XAML Windows Phone 8.1</td>
            <td>1.0</td>
        </tr>
    <tr>
            <td rowspan="3">**AdControl** и **InterstitialAd** (JavaScript/HTML)</td>
            <td>UWP</td>
            <td>SDK Microsoft Advertising для JavaScript</td>
            <td>10.0</td>
        </tr>
        <tr>
            <td>Windows 8.1;</td>
            <td>SDK Microsoft Advertising для Windows 8.1 Native (JS)</td>
            <td>8.5</td>
        </tr>
        <tr>
            <td>Windows Phone 8.1</td>
            <td>SDK Microsoft Advertising для Windows Phone 8.1 Native (JS)</td>
            <td>8.5</td>
        </tr>
    <tr>
            <td rowspan="3">**AdMediatorControl** (только XAML)</td>
            <td>UWP</td>
            <td>Универсальный SDK Microsoft Advertising</td>
            <td>1.0</td>
        </tr>
        <tr>
            <td>Windows 8.1;</td>
            <td>SDK Ad Mediator для XAML в Windows 8.1</td>
            <td>1.0</td>
        </tr>
        <tr>
            <td>Windows Phone 8.1</td>
            <td>SDK Ad Mediator для XAML Windows Phone 8.1</td>
            <td>1.0</td>
        </tr>
    </tbody>
</table>

 

 

 

