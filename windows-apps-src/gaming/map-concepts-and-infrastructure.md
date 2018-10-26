---
author: mtoepke
title: Сопоставление OpenGL ES 2.0 с Direct3D 11
description: Впервые приступая к процессу переноса графической архитектуры из OpenGL ES 2.0 в Direct3D, ознакомьтесь с ключевыми различиями между этими API.
ms.assetid: 7f9b136c-aa22-04b3-d385-6e9e1f38b948
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, opengl, direct3d, перенос
ms.localizationpriority: medium
ms.openlocfilehash: 532c2a0a9779ae3eaedb2217175dc0805514f792
ms.sourcegitcommit: b7e3d222e229cdbf04e837fcb94fb7d84a93de09
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5622083"
---
# <a name="map-opengl-es-20-to-direct3d-11"></a>Сопоставление OpenGL ES 2.0 с Direct3D 11



Впервые приступая к процессу переноса графической архитектуры из OpenGL ES 2.0 в Direct3D, ознакомьтесь с ключевыми различиями между этими API. Темы данного раздела помогут вам спланировать стратегию переноса и изменения API, которые необходимо внести при переносе обработки графики в Direct3D.
## 
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
<td align="left"><p><a href="compare-opengl-es-2-0-api-design-to-directx.md">Планирование переноса из OpenGL ES2.0 в Direct3D</a></p></td>
<td align="left"><p>Если вы переносите игру с платформы iOS или Android, вам, вероятно, потребуется существенно углубиться в OpenGL ES2.0. При подготовке к переносу базы кода вашего конвейера графики в Direct3D11 и среду выполнения Windows необходимо перед началом работы решить несколько вопросов.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="moving-from-egl-to-dxgi.md">Сравнение кода EGL с DXGI и Direct3D</a></p></td>
<td align="left"><p>Программный интерфейс DirectX для работы с графикой (DXGI) и несколько API Direct3D выполняют ту же роль, что и EGL. Этот раздел помогает разобраться в DXGI и Direct3D 11 с точки зрения EGL.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="porting-uniforms-and-attributes.md">Сравнение атрибутов буферов, однородностей и вершин OpenGL ES 2.0 с Direct3D</a></p></td>
<td align="left"><p>В процессе переноса в Direct3D 11 из OpenGL ES 2.0 необходимо изменить синтаксис и поведение API для передачи данных между приложением и программами-шейдерами.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="change-your-shader-loading-code.md">Сравнение конвейера шейдеров OpenGL ES2.0 с Direct3D</a></p></td>
<td align="left"><p>Концептуально конвейер шейдеров Direct3D11 очень похож на таковой в OpenGL ES2.0. Однако в терминах проектного решения API главными компонентами для создания и управления стадиями шейдеров являются части двух основных интерфейсов, <a href="https://msdn.microsoft.com/library/windows/desktop/hh404575"><strong>ID3D11Device1</strong></a> и <a href="https://msdn.microsoft.com/library/windows/desktop/hh404598"><strong>ID3D11DeviceContext1</strong></a>. В этом разделе делается попытка сопоставить общие шаблоны API конвейера шейдеров OpenGL ES2.0 с их эквивалентами Direct3D11 в этих интерфейсах.</p></td>
</tr>
</tbody>
</table>

 

## <a name="notes-on-specific-opengl-es-20-providers"></a>Примечания по конкретным поставщикам OpenGL ES 2.0


В данных разделах используется спецификация Khronos OpenGL ES 2.0 с независимым от платформы C. Как iOS, так и Android используют одинаковые спецификации, а код OpenGL ES 2.0, разработанный для этих платформ, очень похож на фрагменты кода, которые мы разберем, хотя обычно они представляются как объектно-ориентированные API. Кроме того, в силу сложностей и языковых различий каждой из платформ могут существовать мелкие различия, особенно в типах параметров методов и в общем синтаксисе языка. iOS, например, использует Objective-C. У Android есть возможность использовать C++, но некоторые разработчики могли полагаться на чистую реализацию Java. С учетом этого данные разделы должны оставаться полезными, поскольку общие концепции, структура и использование API OpenGL не различаются.

 

 




