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
ms.openlocfilehash: 0c3e713d9a2bb7c10bda0d9517f5cb882d5e2e57
ms.sourcegitcommit: 6b772d2a224f8a9c557dc517c6ec0592545e9a43
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/02/2017
---
# <a name="test-mode-values"></a>Значения тестового режима

При использовании [AdControl](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.adcontrol.aspx), [InterstitialAd](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.interstitialad.aspx) или [](https://msdn.microsoft.com/library/windows/apps/microsoft.advertising.winrt.ui.nativead.aspx) для отображения объявлений в приложении вы должны указать ИД группы объявлений и ИД приложения в свойствах **AdUnitId** и **ApplicationId**. Во время разработки приложения используйте тестовые значения идентификатора приложения и идентификатора рекламного блока из этой статьи, чтобы увидеть, как отображается реклама в вашем приложении во время тестирования.

При использовании тестовых значений в опубликованном приложении оно не будет получать рекламу. Для получения рекламы в опубликованном приложении необходимо обновить его код, указав идентификатор приложения и идентификатор рекламного блока, предоставленный в информационной панели Центра разработки для Windows. Дополнительные сведения см. в разделе [Настройка групп объявлений в приложении](set-up-ad-units-in-your-app.md).
 
Ниже приведены тестовые значения, который следует использовать для различных типов объявлений.

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
    <th align="left">ApplicationId</th>
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
    <th align="left">ApplicationId</th>
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

* Для собственных объявлений:

    <table>
    <col width="33%" />
    <col width="33%" />
    <col width="33%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Целевая ОС</th>
    <th align="left">AdUnitId</th>
    <th align="left">ApplicationId</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left"><p>UWP (Windows 10)</p></td>
    <td align="left"><p>test</p></td>
    <td align="left"><p>d25517cb-12d4-4699-8bdc-52040c712cab</p></td>
    </tbody>
    </table>

> [!IMPORTANT]
> Размер актуального рекламного объявления для объекта **AdControl** определяется свойствами **Width** и **Height**. Для получения оптимального результата убедитесь, что значения свойств **Width** и **Height** в вашем коде соответствуют одному из [поддерживаемых размеров рекламных баннеров](supported-ad-sizes-for-banner-ads.md). Значения свойств **Width** и **Height** не меняются в зависимости от размера отображаемого рекламного объявления.


 

 
