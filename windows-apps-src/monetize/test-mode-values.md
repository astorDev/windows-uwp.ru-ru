---
author: mcleanbyron
ms.assetid: 2ed21281-f996-402d-a968-d1320a4691df
description: "Используйте тестовые значения идентификатора приложения и идентификатора рекламного блока из этой статьи, чтобы увидеть, как отображается реклама во время тестирования."
title: "Значения тестового режима"
ms.author: mcleans
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, объявления, реклама, тестирование"
ms.openlocfilehash: ec5b1c1723a7f58b20234d703fc786647268a3b3
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="test-mode-values"></a>Значения тестового режима

При использовании [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx) или [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) для отображения рекламных объявлений в приложении вы должны указать идентификатор приложения и идентификатор рекламного блока. Во время разработки приложения используйте тестовые значения идентификатора приложения и идентификатора рекламного блока из этой статьи, чтобы увидеть, как отображается реклама в вашем приложении во время тестирования.

При использовании тестовых значений в опубликованном приложении оно не будет получать рекламу. Для получения рекламы в опубликованном приложении необходимо обновить его код, указав идентификатор приложения и идентификатор рекламного блока, предоставленный в информационной панели Центра разработки для Windows. Дополнительные сведения см. в разделе [Настройка групп объявлений в приложении](set-up-ad-units-in-your-app.md).
 
Ниже приведены тестовые значения, который следует использовать для межстраничных объявлений и рекламных баннеров.

* Для межстраничных объявлений:

    <table>
    <colgroup>
    <col width="33%" />
    <col width="33%" />
    <col width="33%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Целевая ОС</th>
    <th align="left">AdUnitId</th>
    <th align="left">AppId</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><p>UWP (Windows10)</p></td>
    <td align="left"><p>test</p></td>
    <td align="left"><p>d25517cb-12d4-4699-8bdc-52040c712cab</p></td>
    </tr>
    <tr class="odd">
    <td align="left"><p>Windows8.x и WindowsPhone8.x</p></td>
    <td align="left"><p>11389925</p></td>
    <td align="left"><p>d25517cb-12d4-4699-8bdc-52040c712cab</p></td>
    </tr>
    </tbody>
    </table>

     
* Для рекламных баннеров:

    <table>
    <colgroup>
    <col width="33%" />
    <col width="33%" />
    <col width="33%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Целевая ОС</th>
    <th align="left">AdUnitId</th>
    <th align="left">AppId</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><p>UWP (Windows10)</p></td>
    <td align="left"><p>test</p></td>
    <td align="left"><p>3f83fe91-d6be-434d-a0ae-7351c5a997f1</p></td>
    </tr>
    <tr class="even">
    <td align="left"><p>Windows8.x и WindowsPhone8.x</p></td>
    <td align="left"><p>10865270</p></td>
    <td align="left"><p>3f83fe91-d6be-434d-a0ae-7351c5a997f1</p></td>
    </tr>
    </tbody>
    </table>


> **Важно!**&nbsp;&nbsp;Размер актуального рекламного объявления для объекта **AdControl** определяется свойствами **Width** и **Height**. Для получения оптимального результата убедитесь, что значения свойств **Width** и **Height** в вашем коде соответствуют одному из [поддерживаемых размеров рекламных баннеров](supported-ad-sizes-for-banner-ads.md). Значения свойств **Width** и **Height** не меняются в зависимости от размера отображаемого рекламного объявления.


 

 
