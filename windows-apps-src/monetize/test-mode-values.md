---
author: mcleanbyron
ms.assetid: 2ed21281-f996-402d-a968-d1320a4691df
description: "Используйте тестовые значения идентификатора приложения и идентификатора рекламного блока из этой статьи, чтобы увидеть, как отображается реклама во время тестирования."
title: "Значения тестового режима"
translationtype: Human Translation
ms.sourcegitcommit: cf695b5c20378f7bbadafb5b98cdd3327bcb0be6
ms.openlocfilehash: 93b20954ba82b613bde96db30a000902dec3b844

---

# Значения тестового режима


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

При использовании [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) или [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) для отображения рекламных объявлений в приложении вы должны указать идентификатор приложения и идентификатор рекламного блока. Во время разработки приложения используйте тестовые значения идентификатора приложения и идентификатора рекламного блока из этой статьи, чтобы увидеть, как отображается реклама в вашем приложении во время тестирования.

> **Важно!** Если в вашем приложении используется рекламный посредник (т. е. объект **AdMediatorControl**), указывать рекламные блоки не требуется. В этом сценарии рекламные блоки Microsoft Advertising создаются автоматически. Дополнительные сведения см. в разделе [Отличия класса AdMediatorControl от класса AdControl](what-is-the-difference-admediatorcontrol-or-adcontrol.md).

При использовании тестовых значений в опубликованном приложении оно не будет получать рекламу. Для получения рекламы в опубликованном приложении необходимо обновить его код, указав идентификатор приложения и идентификатор рекламного блока, предоставленный в информационной панели Центра разработки для Windows. Дополнительные сведения см. в разделе [Настройка блоков рекламы в приложении](set-up-ad-units-in-your-app.md).
 

Ниже приведены тестовые значения, который следует использовать для промежуточной видеорекламы и рекламных баннеров.

* Промежуточная видеореклама:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">AdUnitId</th>
    <th align="left">AppId</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><p>11389925</p></td>
    <td align="left"><p>d25517cb-12d4-4699-8bdc-52040c712cab</p></td>
    </tr>
    </tbody>
    </table>

     
* Рекламные баннеры:

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">AdUnitId</th>
    <th align="left">AppId</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><p>10865270</p></td>
    <td align="left"><p>3f83fe91-d6be-434d-a0ae-7351c5a997f1</p></td>
    </tr>
    </tbody>
    </table>


> **Важно!**   Размер актуального рекламного объявления определяется свойствами **Width** и **Height** объекта **AdControl**. Для получения оптимального результата убедитесь, что значения свойств **Width** и **Height** в вашем коде соответствуют одному из [поддерживаемых размеров рекламных баннеров](supported-ad-sizes-for-banner-ads.md). Значения свойств **Width** и **Height** не меняются в зависимости от размера отображаемого рекламного объявления.



 

 



<!--HONumber=Jun16_HO4-->


