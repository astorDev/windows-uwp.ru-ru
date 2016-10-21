---
author: mtoepke
title: "Пошаговое руководство. Перенос простого приложения Direct3D 9 в DirectX 11 и UWP"
description: "Это упражнение по переносу научит вас переносить простую структуру прорисовки из Direct3D9 в Direct3D11 и универсальную платформу Windows (UWP)."
ms.assetid: d4467e1f-929b-a4b8-b233-e142a8714c96
translationtype: Human Translation
ms.sourcegitcommit: 6530fa257ea3735453a97eb5d916524e750e62fc
ms.openlocfilehash: 53e0b3f5a69f133e74430b1a2e32a13180569f06

---

# Пошаговое руководство: перенос простого приложения Direct3D 9 в DirectX 11 и UWP


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Это упражнение по переносу научит вас переносить простую структуру прорисовки из Direct3D9 в Direct3D11 и универсальную платформу Windows (UWP).
## 
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Тема</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p>[Инициализация Direct3D 11](simple-port-from-direct3d-9-to-11-1-part-1--initializing-direct3d.md)</p></td>
<td align="left"><p>Здесь показано, как перенести код инициализации Direct3D 9 в Direct3D 11, в том числе как получить дескрипторы и контекст устройства Direct3D, а также как использовать DXGI для создания цепочки буферов.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Преобразование инфраструктуры отрисовки](simple-port-from-direct3d-9-to-11-1-part-2--rendering.md)</p></td>
<td align="left"><p>Здесь показано, как перенести простую инфраструктуру отрисовки с Direct3D 9 на Direct3D 11, включая перенос геометрических буферов, компиляцию и загрузку программ-шейдеров на языке HLSL, а также реализацию цепочки отрисовки в Direct3D 11.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Портирование игрового цикла](simple-port-from-direct3d-9-to-11-1-part-3--viewport-and-game-loop.md)</p></td>
<td align="left"><p>Здесь показано, как реализовать окно для игрового приложения UWP и перенести игровой цикл, включая создание интерфейса [<strong>IFrameworkView</strong>](https://msdn.microsoft.com/library/windows/apps/hh700478) для управления полноэкранным [<strong>CoreWindow</strong>](https://msdn.microsoft.com/library/windows/apps/br208225).</p></td>
</tr>
</tbody>
</table>

 

В этом разделе подробно разобраны две ветви кода, выполняющие одну и ту же базовую задачу обработки графики: отображение вращающегося куба с затененными вершинами. В обоих случаях код включает следующий процесс.

1.  Создание устройства Direct3D и цепочки буферов.
2.  Создание буфера вершин и буфера индексов для отображения цветной кубической сетки.
3.  Создание вершинного шейдера для преобразования вершин в экранное пространство и пиксельного построителя текстуры для смешения значений цвета, компиляция построителей текстуры и их загрузка в качестве ресурсов Direct3D.
4.  Реализация цепи отрисовки и отображение отрисованного куба на экране.
5.  Создание окна, запуск основного цикла и обработка оконных сообщений.

По завершении этого пошагового руководства вы получите представление о следующих основных различиях между Direct3D9 и Direct3D11:

-   Разделение устройства, контекста устройства и графической инфраструктуры.
-   Процесс компиляции шейдеров и загрузки их байт-кода во время выполнения.
-   Настройка данных по каждой вершине для этапа сборщика входных данных (IA).
-   Использование [**IFrameworkView**](https://msdn.microsoft.com/library/windows/apps/hh700478) для создания представления CoreWindow.

Обратите внимание, что пошаговое руководство использует [**CoreWindow**](https://msdn.microsoft.com/library/windows/apps/br208225) для простоты и не описывает межпрограммное взаимодействие XAML.

## Необходимые условия


Вам нужно [подготовить среду для разработки игр UWP с использованием DirectX](prepare-your-dev-environment-for-windows-store-directx-game-development.md). Шаблон пока не нужен, но потребуется Microsoft Visual Studio 2015, чтобы загрузить примеры кода для этого руководства.

Чтобы лучше понять принципы программирования для DirectX11 и UWP, которые демонстрируются в этом руководстве, ознакомьтесь с разделом [Принципы и аспекты переноса](porting-considerations.md).

## Связанные разделы


**Direct3D**
[Создание шейдеров HLSL в Direct3D 9](https://msdn.microsoft.com/library/windows/desktop/bb944006)

[Создание нового проекта DirectX11 для UWP](user-interface.md)

**Магазин Windows**
[**Microsoft::WRL::ComPtr**](https://msdn.microsoft.com/library/windows/apps/br244983.aspx)

[**Оператор дескриптора объекта (^)**]https://msdn.microsoft.com/library/windows/apps/yk97tc08.aspx

 

 







<!--HONumber=Aug16_HO3-->


