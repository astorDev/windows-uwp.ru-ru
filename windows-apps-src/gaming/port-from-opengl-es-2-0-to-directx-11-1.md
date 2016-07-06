---
author: mtoepke
title: "Перенос из OpenGL ES 2.0 в Direct3D 11"
description: "Включает статьи, обзоры и пошаговые руководства по переносу графического конвейера из OpenGL ES 2.0 в Direct3D 11 и среду выполнения Windows."
ms.assetid: 1e1cf668-a15f-0c7b-8daf-3260d27c6d9c
ms.sourcegitcommit: 814f056eaff5419b9c28ba63cf32012bd82cc554
ms.openlocfilehash: 40380582a9210cb705a5e7e591d4a8f37c42f8dd

---

# Перенос из OpenGL ES 2.0 в Direct3D 11


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Включает статьи, обзоры и пошаговые руководства по переносу графического конвейера из OpenGL ES 2.0 в Direct3D 11 и среду выполнения Windows.

> **Примечание.** В качестве промежуточного действия при переносе проекта OpenGL ES 2.0 можно использовать ANGLE для Магазина Windows. ANGLE позволяет запускать содержимое OpenGL ES в Windows путем преобразования вызовов API OpenGL ES в вызовы API DirectX 11. Дополнительные сведения об ANGLE см. в [вики об ANGLE для Магазина Windows](http://go.microsoft.com/fwlink/p/?linkid=618387).

 

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
<td align="left"><p>[Сопоставление OpenGL ES 2.0 и Direct3D 11.1](map-concepts-and-infrastructure.md)</p></td>
<td align="left"><p>Впервые приступая к процессу переноса графической архитектуры из OpenGL ES 2.0 в Direct3D, ознакомьтесь с ключевыми различиями между этими API. Темы данного раздела помогут вам спланировать стратегию переноса и изменения API, которые необходимо внести при переносе обработки графики в Direct3D.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Инструкции: перенос простого обработчика OpenGL ES 2.0 в Direct3D 11.1](port-a-simple-opengl-es-2-0-renderer-to-directx-11-1.md)</p></td>
<td align="left"><p>В качестве упражнения в переносе начнем с основ — с переноса простого обработчика для вращающегося куба с затенением по вершинам из OpenGL ES 2.0 в Direct3D, чтобы он соответствовал шаблону "Приложение DirectX 11 (универсальные приложения для Windows)" из Visual Studio 2015.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Справочник по переносу с OpenGL ES 2.0 на Direct3D 11.1](opengl-es-2-0-to-directx-11-1-reference.md)</p></td>
<td align="left"><p>Используйте эти справочные разделы для поиска соответствий API и коротких примеров кода при переносе из OpenGL ES 2.0 в Direct3D 11.</p></td>
</tr>
</tbody>
</table>

 

> **Примечание.**  
Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). В случае разработки приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

 

 







<!--HONumber=Jun16_HO5-->


