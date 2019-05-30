---
title: Сопоставление OpenGL ES 2.0 с Direct3D 11
description: Впервые приступая к процессу переноса графической архитектуры из OpenGL ES 2.0 в Direct3D, ознакомьтесь с ключевыми различиями между этими API.
ms.assetid: 7f9b136c-aa22-04b3-d385-6e9e1f38b948
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, opengl, direct3d, перенос
ms.localizationpriority: medium
ms.openlocfilehash: 525b97700b1362bb19a1b328183f3cbf9da3b006
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368527"
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
<td align="left"><p><a href="compare-opengl-es-2-0-api-design-to-directx.md">Планирование вашей перенос с OpenGL ES 2.0 на Direct3D</a></p></td>
<td align="left"><p>Если вы переносите игру с платформы iOS или Android, вам, вероятно, потребуется существенно углубиться в OpenGL ES 2.0. При подготовке к переносу базы кода вашего конвейера графики в Direct3D 11 и среду выполнения Windows необходимо перед началом работы решить несколько вопросов.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="moving-from-egl-to-dxgi.md">Сравните код EGL DXGI и Direct3D</a></p></td>
<td align="left"><p>Программный интерфейс DirectX для работы с графикой (DXGI) и несколько API Direct3D выполняют ту же роль, что и EGL. Этот раздел помогает разобраться в DXGI и Direct3D 11 с точки зрения EGL.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="porting-uniforms-and-attributes.md">Сравнить буферов, форменная одежда и атрибуты вершины на Direct3D OpenGL ES 2.0</a></p></td>
<td align="left"><p>В процессе переноса в Direct3D 11 из OpenGL ES 2.0 необходимо изменить синтаксис и поведение API для передачи данных между приложением и программами-шейдерами.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="change-your-shader-loading-code.md">Сравнение конвейера шейдеров OpenGL ES 2.0 на Direct3D</a></p></td>
<td align="left"><p>Концептуально конвейер шейдеров Direct3D 11 очень похож на таковой в OpenGL ES 2.0. Однако в терминах проектного решения API главными компонентами для создания и управления стадиями шейдеров являются части двух основных интерфейсов, <a href="https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1"><strong>ID3D11Device1</strong></a> и <a href="https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1"><strong>ID3D11DeviceContext1</strong></a>. В этом разделе делается попытка сопоставить общие шаблоны API конвейера шейдеров OpenGL ES 2.0 с их эквивалентами Direct3D 11 в этих интерфейсах.</p></td>
</tr>
</tbody>
</table>

 

## <a name="notes-on-specific-opengl-es-20-providers"></a>Примечания по конкретным поставщикам OpenGL ES 2.0


В данных разделах используется спецификация Khronos OpenGL ES 2.0 с независимым от платформы C. Как iOS, так и Android используют одинаковые спецификации, а код OpenGL ES 2.0, разработанный для этих платформ, очень похож на фрагменты кода, которые мы разберем, хотя обычно они представляются как объектно-ориентированные API. Кроме того, в силу сложностей и языковых различий каждой из платформ могут существовать мелкие различия, особенно в типах параметров методов и в общем синтаксисе языка. iOS, например, использует Objective-C. У Android есть возможность использовать C++, но некоторые разработчики могли полагаться на чистую реализацию Java. С учетом этого данные разделы должны оставаться полезными, поскольку общие концепции, структура и использование API OpenGL не различаются.

 

 




