---
title: Перенос из OpenGL ES2.0 в Direct3D11
description: Включает статьи, обзоры и пошаговые руководства по переносу графического конвейера из OpenGL ES 2.0 в Direct3D 11 и среду выполнения Windows.
ms.assetid: 1e1cf668-a15f-0c7b-8daf-3260d27c6d9c
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, opengl, direct3d 11, перенос, графика
ms.localizationpriority: medium
ms.openlocfilehash: 9af5e42a27e21b8a4300edc4b8171f7abc64bac7
ms.sourcegitcommit: 49d58bc66c1c9f2a4f81473bcb25af79e2b1088d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8929276"
---
# <a name="port-from-opengl-es-20-to-direct3d-11"></a>Перенос из OpenGL ES 2.0 в Direct3D 11



Включает статьи, обзоры и пошаговые руководства по переносу графического конвейера из OpenGL ES 2.0 в Direct3D 11 и среду выполнения Windows.

> **Примечание**  — промежуточного шага при переносе проекта OpenGL ES 2.0 можно использовать ANGLE для Microsoft Store. ANGLE позволяет запускать содержимое OpenGLES в Windows путем преобразования вызовов API OpenGLES в вызовы API DirectX11. Дополнительные сведения об ANGLE см. в [вики об ANGLE для Microsoft Store](http://go.microsoft.com/fwlink/p/?linkid=618387).

 

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
<td align="left"><p><a href="map-concepts-and-infrastructure.md">Сопоставление OpenGL ES 2.0 и Direct3D 11.1</a></p></td>
<td align="left"><p>Впервые приступая к процессу переноса графической архитектуры из OpenGL ES 2.0 в Direct3D, ознакомьтесь с ключевыми различиями между этими API. Темы данного раздела помогут вам спланировать стратегию переноса и изменения API, которые необходимо внести при переносе обработки графики в Direct3D.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="port-a-simple-opengl-es-2-0-renderer-to-directx-11-1.md">Инструкции: перенос простого обработчика OpenGL ES 2.0 в Direct3D 11.1</a></p></td>
<td align="left"><p>В качестве упражнения в переносе начнем с основ — с переноса простого обработчика для вращающегося куба с затенением по вершинам из OpenGL ES 2.0 в Direct3D, чтобы он соответствовал шаблону "Приложение DirectX 11 (универсальные приложения для Windows)" из Visual Studio 2015.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="opengl-es-2-0-to-directx-11-1-reference.md">Справочник по переносу с OpenGL ES 2.0 на Direct3D 11.1</a></p></td>
<td align="left"><p>Используйте эти справочные разделы для поиска соответствий API и коротких примеров кода при переносе из OpenGL ES 2.0 в Direct3D 11.</p></td>
</tr>
</tbody>
</table>

 

 

 




