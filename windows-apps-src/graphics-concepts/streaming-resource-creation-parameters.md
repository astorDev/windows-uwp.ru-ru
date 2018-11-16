---
title: Параметры создания потоковых ресурсов
description: Имеются некоторые ограничения по типу ресурсов Direct3D, которые можно создавать как ресурс потоковой передачи.
ms.assetid: 6FC5AD93-6F47-479E-947C-895C99B427BC
keywords:
- Параметры создания потоковых ресурсов
author: michaelfromredmond
ms.author: mithom
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 0129b44b6f1c6c8b18555e3e0e0b350a695cabe1
ms.sourcegitcommit: e38b334edb82bf2b1474ba686990f4299b8f59c7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6844500"
---
# <a name="streaming-resource-creation-parameters"></a>Параметры создания потоковых ресурсов


Имеются некоторые ограничения по типу ресурсов Direct3D, которые можно создавать как ресурс потоковой передачи.

<span id="Supported-Resource-Type"></span><span id="supported-resource-type"></span><span id="SUPPORTED-RESOURCE-TYPE"></span>**Поддерживаемый тип ресурсов**  
Texture2D\ [Array\] (включая TextureCube\ [Array\], который является вариантом Texture2D\[Array\]) или Buffer.

**Не поддерживается:** Texture1D\ [Array\].

<span id="Supported-Resource-Usage"></span><span id="supported-resource-usage"></span><span id="SUPPORTED-RESOURCE-USAGE"></span>**Поддерживаемый вариант использования ресурсов**  
Использование по умолчанию.

**Не поддерживается:** Динамический, промежуточной или постоянный.

<span id="Supported-Resource-Misc-Flags"></span><span id="supported-resource-misc-flags"></span><span id="SUPPORTED-RESOURCE-MISC-FLAGS"></span>**Различные флаги поддерживаемых ресурсов**  
Разбитые на плитки; то есть с потоковой передачей (по определению), текстурный куб, аргументы непрямой прорисовки, буфер разрешает необработанные представления, структурированный буфер, фиксация ресурса, формирование MIP-карт.

**Не поддерживается:** общий, общий с ключом мьютекс, GDI-совместимый, общих дескриптор NT, ограниченное содержимое, ограниченный общий ресурс, ограничить драйвер общего ресурса, защищенный или пул плиток.

<span id="Supported-Bind-Flags"></span><span id="supported-bind-flags"></span><span id="SUPPORTED-BIND-FLAGS"></span>**Поддерживаемые флаги привязки**  
Привязать как ресурс шейдера, цель прорисовки, буфер глубины или неупорядоченный доступ.

**Не поддерживается:** Привязка как буфер констант, буфер вершин (привязка разбитого буфера, как SRV/UAV/RTV поддерживается), индекс буфера, вывод потока, декодера или кодировщика видео.

<span id="Supported-Formats"></span><span id="supported-formats"></span><span id="SUPPORTED-FORMATS"></span>**Поддерживаемые форматы**  
Все форматы, которые будут доступны для определенной конфигурации независимо от разбиения на плитки с некоторыми исключениями.

<span id="Supported-Sample-Description--Multisample-count--quality-"></span><span id="supported-sample-description--multisample-count--quality-"></span><span id="SUPPORTED-SAMPLE-DESCRIPTION--MULTISAMPLE-COUNT--QUALITY-"></span>**Поддерживаемые описание образца (объем множественной выборки, качество)**  
То, что будет поддерживаться для определенной конфигурации независимо от разбиения на плитки с некоторыми исключениями.

<span id="Supported-Width-Height-MipLevels-ArraySize"></span><span id="supported-width-height-miplevels-arraysize"></span><span id="SUPPORTED-WIDTH-HEIGHT-MIPLEVELS-ARRAYSIZE"></span>**Поддерживаемые Width/Height/MipLevels/ArraySize**  
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
<th align="left">Статья</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="address-space-available-for-streaming-resources.md">Адресное пространство, доступное для потоковых ресурсов</a></p></td>
<td align="left"><p>В этом разделе указано виртуального адресное пространство, доступное для потоковой передачи ресурсов.</p></td>
</tr>
</tbody>
</table>

 

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>Статьи по теме


[Создание потоковых ресурсов](creating-streaming-resources.md)

 

 




