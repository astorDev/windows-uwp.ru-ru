---
author: mtoepke
title: "Сопоставление OpenGL ES 2.0 с Direct3D 11"
description: "Впервые приступая к процессу переноса графической архитектуры из OpenGL ES 2.0 в Direct3D, ознакомьтесь с ключевыми различиями между этими API."
ms.assetid: 7f9b136c-aa22-04b3-d385-6e9e1f38b948
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: d21bbf905797a7b0c14e666f1ec31a85203b30db

---

# Сопоставление OpenGL ES 2.0 с Direct3D 11


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

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
<td align="left"><p>[Планирование переноса из OpenGL ES2.0 в Direct3D](compare-opengl-es-2-0-api-design-to-directx.md)</p></td>
<td align="left"><p>Если вы переносите игру с платформы iOS или Android, вам, вероятно, потребуется существенно углубиться в OpenGL ES2.0. При подготовке к переносу базы кода вашего конвейера графики в Direct3D11 и среду выполнения Windows необходимо перед началом работы решить несколько вопросов.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Сравнение кода EGL с DXGI и Direct3D](moving-from-egl-to-dxgi.md)</p></td>
<td align="left"><p>Программный интерфейс DirectX для работы с графикой (DXGI) и несколько API Direct3D выполняют ту же роль, что и EGL. Этот раздел помогает разобраться в DXGI и Direct3D 11 с точки зрения EGL.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Сравнение атрибутов буферов, однородностей и вершин OpenGL ES 2.0 с Direct3D](porting-uniforms-and-attributes.md)</p></td>
<td align="left"><p>В процессе переноса в Direct3D 11 из OpenGL ES 2.0 необходимо изменить синтаксис и поведение API для передачи данных между приложением и программами-шейдерами.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Сравнение конвейера шейдеров OpenGL ES2.0 с Direct3D](change-your-shader-loading-code.md)</p></td>
<td align="left"><p>Концептуально конвейер шейдеров Direct3D11 очень похож на таковой в OpenGL ES2.0. Однако в терминах проектного решения API главными компонентами для создания и управления стадиями шейдеров являются части двух основных интерфейсов, [<strong>ID3D11Device1</strong>](https://msdn.microsoft.com/library/windows/desktop/hh404575) и [<strong>ID3D11DeviceContext1</strong>](https://msdn.microsoft.com/library/windows/desktop/hh404598). В этом разделе делается попытка сопоставить общие шаблоны API конвейера шейдеров OpenGL ES2.0 с их эквивалентами Direct3D11 в этих интерфейсах.</p></td>
</tr>
</tbody>
</table>

 

## Примечания по конкретным поставщикам OpenGL ES 2.0


В данных разделах используется спецификация Khronos OpenGL ES 2.0 с независимым от платформы C. Как iOS, так и Android используют одинаковые спецификации, а код OpenGL ES 2.0, разработанный для этих платформ, очень похож на фрагменты кода, которые мы разберем, хотя обычно они представляются как объектно-ориентированные API. Кроме того, в силу сложностей и языковых различий каждой из платформ могут существовать мелкие различия, особенно в типах параметров методов и в общем синтаксисе языка. iOS, например, использует Objective-C. У Android есть возможность использовать C++, но некоторые разработчики могли полагаться на чистую реализацию Java. С учетом этого данные разделы должны оставаться полезными, поскольку общие концепции, структура и использование API OpenGL не различаются.

 

 







<!--HONumber=Aug16_HO3-->


