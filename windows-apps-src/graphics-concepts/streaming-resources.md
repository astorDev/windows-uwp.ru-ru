---
title: "Потоковые ресурсы"
description: "Потоковые ресурсы это большие логические ресурсы, потребляющие малые объемы физической памяти. Вместо того, чтобы передавать весь большой ресурс, потоком передаются только малые части ресурса. Потоковыми ресурсами раньше назывались динамически распределяемые ресурсы."
ms.assetid: 04F0486E-4B71-4073-88DA-2AF505F4F0C1
keywords:
- "Потоковые ресурсы"
- "ресурсы, потоковая передача"
- "ресурсы, динамически распределяемые"
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.localizationpriority: medium
ms.openlocfilehash: 54441d62107f08abe18715a91920d5cb30c75470
ms.sourcegitcommit: c80b9e6589a1ee29c5032a0b942e6a024c224ea7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/22/2017
---
# <a name="streaming-resources"></a>Потоковые ресурсы


*Потоковые ресурсы* это большие логические ресурсы, потребляющие малые объемы физической памяти. Вместо того, чтобы передавать весь большой ресурс, потоком передаются только малые части ресурса. Потоковыми ресурсами раньше назывались *динамически распределяемые ресурсы*.

## <a name="span-idin-this-sectionspanin-this-section"></a><span id="in-this-section"></span>В этом разделе


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Раздел</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[Потребность в потоковых ресурсах](the-need-for-streaming-resources.md)</p></td>
<td align="left"><p>Потоковые ресурсы нужны для того, чтобы память GPU не расходовалась на хранение областей поверхностей, к которым не будет осуществляться доступ, а также затем, чтобы указать аппаратному обеспечению как вести фильтрацию по близко расположенным плиткам.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Создание потоковых ресурсов](creating-streaming-resources.md)</p></td>
<td align="left"><p>Установка флага при создании ресурса, обозначает что ресурс является потоковым.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Конвейерный доступ к потоковым ресурсам](pipeline-access-to-streaming-resources.md)</p></td>
<td align="left"><p>Потоковые ресурсы могут использоваться в представлениях ресурсов шейдера (SRV), представлениях однобуферной прорисовки (RTV), представления трафарета глубины (DSV) и представлениях неупорядоченного доступа (UAV), а также как некоторые точки привязки, где представления не используются, например, как привязки буфера вершин.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Уровни функций для потоковых ресурсов](streaming-resources-features-tiers.md)</p></td>
<td align="left"><p>Direct3D поддерживает три уровня возможностей, касающихся потоковых ресурсов.</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Обучающее руководство по графике Direct3D](index.md)

[Ресурсы](resources.md)

 

 




