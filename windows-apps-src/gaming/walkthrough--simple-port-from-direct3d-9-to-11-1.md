---
author: mtoepke
title: 'Пошаговое руководство: перенос из Direct3D9 в DirectX11 и UWP'
description: Это упражнение по переносу научит вас переносить простую структуру отрисовки из Direct3D9 в Direct3D11 и универсальную платформу Windows (UWP).
ms.assetid: d4467e1f-929b-a4b8-b233-e142a8714c96
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, перенос, direct3d 9, direct3d 11
ms.localizationpriority: medium
ms.openlocfilehash: bd0a8c07be58d670e60aa3a23504d3f5119e6b50
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6253382"
---
# <a name="walkthrough-port-a-simple-direct3d-9-app-to-directx-11-and-universal-windows-platform-uwp"></a>Пошаговое руководство: перенос простого приложения Direct3D 9 в DirectX 11 и UWP



Из этого упражнения вы узнаете, как перенести простую платформу отрисовки из Direct3D9 на Direct3D11 и универсальную платформу Windows (UWP).
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
<td align="left"><p><a href="simple-port-from-direct3d-9-to-11-1-part-1--initializing-direct3d.md">Инициализация Direct3D 11</a></p></td>
<td align="left"><p>Здесь показано, как перенести код инициализации Direct3D 9 в Direct3D 11, в том числе как получить дескрипторы и контекст устройства Direct3D, а также как использовать DXGI для создания цепочки буферов.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="simple-port-from-direct3d-9-to-11-1-part-2--rendering.md">Преобразование инфраструктуры отрисовки</a></p></td>
<td align="left"><p>Здесь показано, как перенести простую инфраструктуру отрисовки с Direct3D 9 на Direct3D 11, включая перенос геометрических буферов, компиляцию и загрузку программ-шейдеров на языке HLSL, а также реализацию цепочки отрисовки в Direct3D 11.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="simple-port-from-direct3d-9-to-11-1-part-3--viewport-and-game-loop.md">Портирование игрового цикла</a></p></td>
<td align="left"><p>Здесь показано, как реализовать окно для игрового приложения UWP и перенести игровой цикл, включая создание интерфейса <a href="https://msdn.microsoft.com/library/windows/apps/hh700478"><strong>IFrameworkView</strong></a> для управления полноэкранным <a href="https://msdn.microsoft.com/library/windows/apps/br208225"><strong>CoreWindow</strong></a>.</p></td>
</tr>
</tbody>
</table>

 

В этой статье подробно разобраны две ветви кода, выполняющие одну и ту же базовую задачу обработки графики: отображение вращающегося куба с затененными вершинами. В обоих случаях код включает следующий процесс.

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

## <a name="prerequisites"></a>Необходимые условия


Вам нужно [подготовить среду для разработки игр UWP с использованием DirectX](prepare-your-dev-environment-for-windows-store-directx-game-development.md). Шаблон пока не нужен, но потребуется Microsoft Visual Studio2015, чтобы загрузить примеры кода для этого пошагового руководства.

Чтобы лучше понять принципы программирования для DirectX11 и UWP, которые демонстрируются в этом руководстве, ознакомьтесь с разделом [Принципы и аспекты переноса](porting-considerations.md).

## <a name="related-topics"></a>Связанные разделы

**Direct3D**

* [Создание шейдеров HLSL в Direct3D9](https://msdn.microsoft.com/library/windows/desktop/bb944006)
* [Шаблоны проектов игр DirectX](user-interface.md)

**Microsoft Store**

* [**Microsoft::WRL::ComPtr**](https://msdn.microsoft.com/library/windows/apps/br244983.aspx)
* [**Оператор дескриптора объекта (^)**](https://msdn.microsoft.com/library/windows/apps/yk97tc08.aspx)

