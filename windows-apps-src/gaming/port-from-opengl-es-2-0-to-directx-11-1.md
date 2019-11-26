---
title: Перенос из OpenGL ES 2.0 в Direct3D 11
description: Включает статьи, обзоры и пошаговые руководства по переносу графического конвейера из OpenGL ES 2.0 в Direct3D 11 и среду выполнения Windows.
ms.assetid: 1e1cf668-a15f-0c7b-8daf-3260d27c6d9c
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, opengl, direct3d 11, перенос, графика
ms.localizationpriority: medium
ms.openlocfilehash: 9215c06c97fb8e45a445ccd3691f484a6b2ad513
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258455"
---
# <a name="port-from-opengl-es-20-to-direct3d-11"></a>Перенос из OpenGL ES 2.0 в Direct3D 11



Включает статьи, обзоры и пошаговые руководства по переносу графического конвейера из OpenGL ES 2.0 в Direct3D 11 и среду выполнения Windows.

> **Обратите внимание** ,   промежуточный шаг к переносу проекта OpenGL ES 2,0 заключается в использовании угла для Microsoft Store. ANGLE позволяет запускать содержимое OpenGL ES в Windows путем преобразования вызовов API OpenGL ES в вызовы API DirectX 11. Дополнительные сведения об ANGLE см. в [вики об ANGLE для Microsoft Store](https://github.com/microsoft/angle/wiki).

 

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
<td align="left"><p><a href="map-concepts-and-infrastructure.md">Сопоставьте OpenGL ES 2,0 с Direct3D 11,1</a></p></td>
<td align="left"><p>Впервые приступая к процессу переноса графической архитектуры из OpenGL ES 2.0 в Direct3D, ознакомьтесь с ключевыми различиями между этими API. Темы данного раздела помогут вам спланировать стратегию переноса и изменения API, которые необходимо внести при переносе обработки графики в Direct3D.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="port-a-simple-opengl-es-2-0-renderer-to-directx-11-1.md">Как перенести простой модуль подготовки OpenGL ES 2,0 в Direct3D 11,1</a></p></td>
<td align="left"><p>В качестве упражнения в переносе начнем с основ — с переноса простого обработчика для вращающегося куба с затенением по вершинам из OpenGL ES 2.0 в Direct3D, чтобы он соответствовал шаблону "Приложение DirectX 11 (универсальные приложения для Windows)" из Visual Studio 2015.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="opengl-es-2-0-to-directx-11-1-reference.md">Справочник по OpenGL ES 2,0 к Direct3D 11,1</a></p></td>
<td align="left"><p>Используйте эти справочные разделы для поиска соответствий API и коротких примеров кода при переносе из OpenGL ES 2.0 в Direct3D 11.</p></td>
</tr>
</tbody>
</table>

 

 

 




