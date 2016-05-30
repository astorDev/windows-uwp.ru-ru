---
author: mcleanbyron
ms.assetid: 3aeddb83-5314-447b-b294-9fc28273cd39
description: Узнайте о том, как устанавливать библиотеки Microsoft Advertising.
title: Установка библиотек Microsoft Advertising

---

# Установка библиотек Microsoft Advertising


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x, см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Библиотеки Microsoft Advertising для приложений для Windows включены в [пакет SDK Microsoft Store Engagement and Monetization](http://aka.ms/store-em-sdk). Этот пакет SDK является расширением Visual Studio 2015 или Visual Studio 2013.

Руководство по установке см. в статье [Монетизация приложений и привлечение пользователей с помощью пакета SDK Microsoft Store Engagement and Monetization](https://msdn.microsoft.com/windows/uwp/monetize/monetize-your-app-with-the-microsoft-store-engagement-and-monetization-sdk).

> **Примечание.** Если вы установили SDK Windows 10 Anniversary, предварительная сборка 14295 или выше, с Visual Studio 2015, необходимо также установить библиотеку WinJS, если вы хотите добавлять рекламу в приложение JavaScript/HTML. Эта библиотека ранее включалась в предыдущие версии Windows SDK для Windows 10, но, начиная с предварительной сборки Windows 10 Anniversary SDK 14295, ее необходимо устанавливать отдельно. Сведения по установке WinJS см. в разделе [Скачать WinJS](http://try.buildwinjs.com/download/GetWinJS/).

## Имена библиотек для рекламных объявлений и рекламного посредника


Пакет SDK Microsoft Store Engagement and Monetization включает два набора рекламных библиотек: библиотеки для Microsoft Advertising (предоставляющие классы [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) и [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) для приложений XAML и JavaScript/HTML) и библиотеки для рекламного посредника (предоставляющие класс **AdMediatorControl**).

В этом документе описано использование классов **AdControl** и **InterstitialAd** в библиотеках Microsoft Advertising для отображения рекламных баннеров или промежуточной видеорекламы от Майкрософт. Дополнительные сведения об использовании рекламного посредника см. в разделе [Использование рекламного посредника для повышения дохода](https://msdn.microsoft.com/windows/uwp/monetize/use-ad-mediation-to-maximize-revenue).


Прежде чем использовать какие-либо элементы управления рекламой в коде приложения, необходимо создать в проекте ссылку на нужную библиотеку. В следующих таблицах перечислены названия всех библиотек пакета SDK Microsoft Store Engagement and Monetization в том виде, в котором они отображаются в диалоговом окне **Диспетчер ссылок** в Visual Studio.


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

 

 

 


<!--HONumber=May16_HO2-->


