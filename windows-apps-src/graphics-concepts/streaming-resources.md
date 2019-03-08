---
title: Потоковые ресурсы
description: Потоковые ресурсы это большие логические ресурсы, потребляющие малые объемы физической памяти. Вместо того, чтобы передавать весь большой ресурс, потоком передаются только малые части ресурса. Потоковыми ресурсами раньше назывались динамически распределяемые ресурсы.
ms.assetid: 04F0486E-4B71-4073-88DA-2AF505F4F0C1
keywords:
- Потоковые ресурсы
- ресурсы, потоковая передача
- ресурсы, динамически распределяемые
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: c15c8a82219109a96d0a9ca192c4dfff5d86c9aa
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57598239"
---
# <a name="streaming-resources"></a>Потоковые ресурсы


*Потоковые ресурсы* это большие логические ресурсы, потребляющие малые объемы физической памяти. Вместо того, чтобы передавать весь большой ресурс, потоком передаются только малые части ресурса. Ранее потоковые ресурсы назывались *мозаичными ресурсами*.

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
<td align="left"><p><a href="the-need-for-streaming-resources.md">Потребность в потоковую передачу ресурсов</a></p></td>
<td align="left"><p>Потоковые ресурсы нужны для того, чтобы память GPU не расходовалась на хранение областей поверхностей, к которым не будет осуществляться доступ, а также затем, чтобы указать аппаратному обеспечению как вести фильтрацию по близко расположенным плиткам.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="creating-streaming-resources.md">Создание потоковой передачи ресурсов</a></p></td>
<td align="left"><p>Потоковые ресурсы создаются путем указания флага при создании ресурса. Этот флаг должен указывать на то, что ресурс является потоковым.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="pipeline-access-to-streaming-resources.md">Конвейер доступ к потоковой передачи ресурсов</a></p></td>
<td align="left"><p>Потоковые ресурсы можно использовать в представлениях ресурсов шейдера (SRV), представлениях целевого объекта отрисовки (RTV), представлениях трафарета глубины (DSV) и представлениях неупорядоченного доступа (UAV), а также как точки привязки, где представления не используются, например как привязки буфера вершин.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="streaming-resources-features-tiers.md">Уровни функции потоковой передачи ресурсов</a></p></td>
<td align="left"><p>Direct3D поддерживает три уровня возможностей, касающихся потоковых ресурсов.</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Руководство по обучения графики Direct3D](index.md)

[Ресурсы](resources.md)

 

 




