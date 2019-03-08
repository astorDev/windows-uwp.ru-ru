---
title: Параметры создания потоковых ресурсов
description: Существуют определенные ограничения на тип ресурсов Direct3D, которые можно создать в качестве потоковых.
ms.assetid: 6FC5AD93-6F47-479E-947C-895C99B427BC
keywords:
- Параметры создания потоковых ресурсов
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 1ddb150e570e25af7162a50309b9b0fc30cedf60
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57617339"
---
# <a name="streaming-resource-creation-parameters"></a>Параметры создания потоковых ресурсов


Существуют определенные ограничения на тип ресурсов Direct3D, которые можно создать в качестве потоковых.

<span id="Supported-Resource-Type"></span><span id="supported-resource-type"></span><span id="SUPPORTED-RESOURCE-TYPE"></span>**Поддерживаемый тип ресурса**  
Texture2D\[массива\] (включая TextureCube\[массива\], который является разновидностью Texture2D\[массива\]) или буфера.

**НЕ поддерживается:  **Texture1D\[массива\] .

<span id="Supported-Resource-Usage"></span><span id="supported-resource-usage"></span><span id="SUPPORTED-RESOURCE-USAGE"></span>**Использование поддерживаемых ресурсов**  
Использование по умолчанию.

**НЕ поддерживается:  **динамические, промежуточной или неизменяемые.

<span id="Supported-Resource-Misc-Flags"></span><span id="supported-resource-misc-flags"></span><span id="SUPPORTED-RESOURCE-MISC-FLAGS"></span>**Прочие флаги поддерживаемых ресурсов**  
Разбитые на плитки; то есть с потоковой передачей (по определению), текстурный куб, аргументы непрямой прорисовки, буфер разрешает необработанные представления, структурированный буфер, фиксация ресурса, формирование MIP-карт.

**НЕ поддерживается:  **совместное использование, совместное с ключом мьютекс, GDI-совместимый, общих NT дескриптор, ограниченные содержимое, ограничить общий ресурс, ограничить драйвер общий ресурс, защищенный или плитку пула.

<span id="Supported-Bind-Flags"></span><span id="supported-bind-flags"></span><span id="SUPPORTED-BIND-FLAGS"></span>**Флаги поддерживаемые привязки**  
Привязать как ресурс шейдера, цель прорисовки, буфер глубины или неупорядоченный доступ.

**НЕ поддерживается:  **привязать как буфер констант, буфера вершин (привязка мозаичного буфер поддерживается SRV/UAV/RTV), указатель буфера, выходные данные stream, декодер или кодировщик видео.

<span id="Supported-Formats"></span><span id="supported-formats"></span><span id="SUPPORTED-FORMATS"></span>**Поддерживаемые форматы**  
Все форматы, которые будут доступны для определенной конфигурации независимо от разбиения на плитки с некоторыми исключениями.

<span id="Supported-Sample-Description--Multisample-count--quality-"></span><span id="supported-sample-description--multisample-count--quality-"></span><span id="SUPPORTED-SAMPLE-DESCRIPTION--MULTISAMPLE-COUNT--QUALITY-"></span>**Поддерживаемые описание образца (множественное число, качество)**  
То, что будет поддерживаться для определенной конфигурации независимо от разбиения на плитки с некоторыми исключениями.

<span id="Supported-Width-Height-MipLevels-ArraySize"></span><span id="supported-width-height-miplevels-arraysize"></span><span id="SUPPORTED-WIDTH-HEIGHT-MIPLEVELS-ARRAYSIZE"></span>**Поддерживаемые ширины и высоты/MipLevels/размера массива**  
Полные размеры, поддерживаемые Direct3D. Ресурсы потоковой передачи не имеют ограничения на общий размер памяти, который налагается на ресурсы не потоковой передачи. Ресурсы потоковой передачи ограничиваются только общими пределами пространства виртуальных адресов. См. [Адресное пространство, доступное для потоковых ресурсов](address-space-available-for-streaming-resources.md).

Начальное содержимое пула памяти плиток не определено.

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
<td align="left"><p><a href="address-space-available-for-streaming-resources.md">Адресное пространство для потоковой передачи ресурсов</a></p></td>
<td align="left"><p>В этом разделе указано виртуальное адресное пространство, которое доступно для потоковых ресурсов.</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Связанные разделы


[Создание потоковой передачи ресурсов](creating-streaming-resources.md)

 

 




