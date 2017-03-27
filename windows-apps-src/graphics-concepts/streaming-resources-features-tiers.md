---
title: "Уровни функций для потоковых ресурсов"
description: "Direct3D поддерживает три уровня возможностей, касающихся потоковых ресурсов."
ms.assetid: 6AE7EA72-3929-4BB4-8780-F0CF26192D87
keywords:
- "Уровни функций для потоковых ресурсов"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: ba1ec47e389f8de4bfd2ab190f1dfe078724deab
ms.lasthandoff: 02/07/2017

---

# <a name="streaming-resources-features-tiers"></a>Уровни функций для потоковых ресурсов


Direct3D поддерживает три уровня возможностей, касающихся потоковых ресурсов.

Уровень 1 предоставляет базовые возможности для потоковых ресурсов.

Уровень 2 добавляет новых возможности поверх уровня 1, например, гарантированная незапакованная MIP-карта текстуры, когда размер не меньше одной стандартной формы плитки; инструкции шейдеру для закрепления уровня детализации (LOD) и для получения состояния операции шейдера; также, при считывании из плиток, сопоставленных с NULL, измеренное значение будет равно нулю.

3-й уровень добавляет функции Texture3D поверх 2-го уровня.

В версиях Direct3D есть опрашивающие функции для проверки уровня поддержки потоковых ресурсов оборудованием и драйверами и на каком конкретном уровне.

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
<td align="left"><p>[Уровень 1](tier-1.md)</p></td>
<td align="left"><p>В этом разделе описывается поддержка 1-го уровня.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Уровень 2](tier-2.md)</p></td>
<td align="left"><p>Уровень 2 поддержки потоковых ресурсов добавляет возможности поверх уровня 1, например, гарантированная незапакованная MIP-карта текстуры, когда размер не меньше одной стандартной формы плитки; инструкции шейдеру для закрепления уровня детализации (LOD) и для получения состояния операции шейдера; также, при считывании из плиток, сопоставленных с NULL, измеренное значение будет равно нулю.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Уровень 3](tier-3.md)</p></td>
<td align="left"><p>Уровень 3 добавляет поддержку Texture3D для потоковой передачи ресурсов, в дополнение к возможностям [уровня 2](tier-2.md) .</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Потоковые ресурсы](streaming-resources.md)

 

 




