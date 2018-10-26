---
title: Экспозиция потоковых ресурсов HLSL
description: Для поддержки потоковых ресурсов в модели шейдера 5 требуется определенный синтаксис HLSL.
ms.assetid: 00A40D82-0565-43DC-82AB-0675B7E772E3
keywords:
- Экспозиция потоковых ресурсов HLSL
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: a8523f4895c541ffb3b92ee00d5b62c57343ae00
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5571383"
---
# <a name="hlsl-streaming-resources-exposure"></a>Экспозиция потоковых ресурсов HLSL


Для поддержки потоковых ресурсов в [модели шейдера 5](https://msdn.microsoft.com/library/windows/desktop/ff471356) требуется определенный синтаксис HLSL.

Синтаксис HLSL модели шейдера 5 разрешен только на устройствах с поддержкой потоковых ресурсов. Каждый соответствующий метод HLSL для потоковых ресурсов в следующей таблице принимает один (feedback) или два (сначала clamp, а затем feedback) дополнительных необязательных параметра. Например, метод **Sample** выглядит так:

**Sample(sampler, location \[, offset \[, clamp \[, feedback\] \] \])**

Пример метода **Sample**— [**Texture2D.Sample(S,float,int,float,uint)**](https://msdn.microsoft.com/library/windows/desktop/dn393787).

Параметры offset, clamp и feedback необязательны. Необходимо указать все необязательные параметры вплоть до требуемого в соответствии с правилами C++ касательно аргументов функций по умолчанию. Например, если требуется состояние feedback, необходимо явным образом предоставить параметры offset и clamp методу **Sample**, даже если логически они не нужны.

Параметр clamp— скалярное значение с плавающей точкой. Литеральное значение clamp=0.0f указывает, что операция прикрепления не выполнена.

Параметр feedback— это переменная **uint**, которую можно передать встроенной функции опроса доступа к памяти [**CheckAccessFullyMapped**](https://msdn.microsoft.com/library/windows/desktop/dn292083). Значение параметра feedback нельзя изменять или интерпретировать. Но компилятор не предоставляет средств расширенного анализа и диагностики для обнаружения изменений значения.

Вот синтаксис [**CheckAccessFullyMapped**](https://msdn.microsoft.com/library/windows/desktop/dn292083):

**bool CheckAccessFullyMapped(in uint FeedbackVar);**

[**CheckAccessFullyMapped**](https://msdn.microsoft.com/library/windows/desktop/dn292083) интерпретирует значение *FeedbackVar* и возвращает значение true, если все данные, к которым осуществляется доступ, сопоставлены в ресурсе. В противном случае **CheckAccessFullyMapped** возвращает значение false.

Если параметр clamp или feedback присутствует, компилятор выдает вариант базовой инструкции. Например, выборка потокового ресурса приводит к созданию инструкции `sample_cl_s`.

Если параметры clamp и feedback не указаны, компилятор выдает базовую инструкцию, чтобы исключить отклонение от текущего поведения.

Если параметру clamp присвоено значение 0.0f, прикрепление не выполнено. Компилятор драйвера может дополнительно настроить инструкцию под целевое оборудование. Если значением параметра feedback является регистр NULL в инструкции, параметр feedback не используется. Таким образом, компилятор драйвера может дополнительно настроить инструкцию под целевую архитектуру.

Если компилятор HLSL делает логический вывод, что значение параметра clamp— 0.0f, а параметр feedback не используется, компилятор выдает соответствующую базовую инструкцию (например, `sample` вместо `sample_cl_s`).

Если доступ к потоковому ресурсу состоит из нескольких инструкций байт-кода, например для структурированных ресурсов, компилятор выполняет статистическое вычисление отдельных значений параметра feedback через операцию ИЛИ, чтобы получить окончательное значение параметра feedback. Таким образом, для такого сложного доступа вы получаете одно значение параметра feedback.

Это сводная таблица методов HLSL, измененных для обеспечения поддержки параметров feedback и clamp. Все эти методы работают с плиточными и не потоковыми ресурсами любых размеров. Не потоковые ресурсы всегда отображаются как полностью сопоставленные.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><a href="https://msdn.microsoft.com/library/windows/desktop/ff471359">Объекты HLSL</a> </th>
<th align="left">Встроенные методы с параметром feedback (*)— также с параметром clamp</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[RW]Texture2D</p>
<p>[RW]Texture2DArray</p>
<p>TextureCUBE</p>
<p>TextureCUBEArray</p></td>
<td align="left"><p>Gather</p>
<p>GatherRed</p>
<p>GatherGreen</p>
<p>GatherBlue</p>
<p>GatherAlpha</p>
<p>GatherCmp</p>
<p>GatherCmpRed</p>
<p>GatherCmpGreen</p>
<p>GatherCmpBlue</p>
<p>GatherCmpAlpha</p></td>
</tr>
<tr class="even">
<td align="left"><p>[RW]Texture1D</p>
<p>[RW]Texture1DArray</p>
<p>[RW]Texture2D</p>
<p>[RW]Texture2DArray</p>
<p>[RW]Texture3D</p>
<p>TextureCUBE</p>
<p>TextureCUBEArray</p></td>
<td align="left"><p>Sample*</p>
<p>SampleBias*</p>
<p>SampleCmp*</p>
<p>SampleCmpLevelZero</p>
<p>SampleGrad*</p>
<p>SampleLevel</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[RW]Texture1D</p>
<p>[RW]Texture1DArray</p>
<p>[RW]Texture2D</p>
<p>Texture2DMS</p>
<p>[RW]Texture2DArray</p>
<p>Texture2DArrayMS</p>
<p>[RW]Texture3D</p>
<p>[RW]Buffer</p>
<p>[RW]ByteAddressBuffer</p>
<p>[RW]StructuredBuffer</p></td>
<td align="left">Load</td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Конвейерный доступ к потоковым ресурсам](pipeline-access-to-streaming-resources.md)

 

 




