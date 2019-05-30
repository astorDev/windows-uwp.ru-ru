---
title: 'Пошаговое руководство: перенос из Direct3D 9 в DirectX 11 и UWP'
description: Это упражнение по переносу научит вас переносить простую структуру прорисовки из Direct3D 9 в Direct3D 11 и универсальную платформу Windows (UWP).
ms.assetid: d4467e1f-929b-a4b8-b233-e142a8714c96
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, перенос, direct3d 9, direct3d 11
ms.localizationpriority: medium
ms.openlocfilehash: 5d4aef73b9b28d631a492436ff90761541134220
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66367427"
---
# <a name="walkthrough-port-a-simple-direct3d-9-app-to-directx-11-and-universal-windows-platform-uwp"></a>Пошаговое руководство: Порт простое приложение Direct3D 9 в DirectX 11 и универсальной платформы Windows (UWP)



Это упражнение по переносу научит вас переносить простую структуру прорисовки из Direct3D 9 в Direct3D 11 и универсальную платформу Windows (UWP).
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
<td align="left"><p><a href="simple-port-from-direct3d-9-to-11-1-part-1--initializing-direct3d.md">Инициализировать Direct3D 11</a></p></td>
<td align="left"><p>Здесь показано, как перенести код инициализации Direct3D 9 в Direct3D 11, в том числе как получить дескрипторы и контекст устройства Direct3D, а также как использовать DXGI для создания цепочки буферов.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="simple-port-from-direct3d-9-to-11-1-part-2--rendering.md">Преобразовать платформу для отображения</a></p></td>
<td align="left"><p>Здесь показано, как перенести простую инфраструктуру отрисовки с Direct3D 9 на Direct3D 11, включая перенос геометрических буферов, компиляцию и загрузку программ-шейдеров на языке HLSL, а также реализацию цепочки отрисовки в Direct3D 11.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="simple-port-from-direct3d-9-to-11-1-part-3--viewport-and-game-loop.md">Порт цикл игры</a></p></td>
<td align="left"><p>Здесь показано, как реализовать окно для игрового приложения UWP и перенести игровой цикл, включая создание интерфейса <a href="https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.IFrameworkView"><strong>IFrameworkView</strong></a> для управления полноэкранным <a href="https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow"><strong>CoreWindow</strong></a>.</p></td>
</tr>
</tbody>
</table>

 

В этом разделе подробно разобраны две ветви кода, выполняющие одну и ту же базовую задачу обработки графики: отображение вращающегося куба с затененными вершинами. В обоих случаях код включает следующий процесс.

1.  Создание устройства Direct3D и цепочки буферов.
2.  Создание буфера вершин и буфера индексов для отображения цветной кубической сетки.
3.  Создание вершинного шейдера для преобразования вершин в экранное пространство и пиксельного построителя текстуры для смешения значений цвета, компиляция построителей текстуры и их загрузка в качестве ресурсов Direct3D.
4.  Реализация цепи отрисовки и отображение отрисованного куба на экране.
5.  Создание окна, запуск основного цикла и обработка оконных сообщений.

По завершении этого пошагового руководства вы получите представление о следующих основных различиях между Direct3D 9 и Direct3D 11:

-   Разделение устройства, контекста устройства и графической инфраструктуры.
-   Процесс компиляции шейдеров и загрузки их байт-кода во время выполнения.
-   Настройка данных по каждой вершине для этапа сборщика входных данных (IA).
-   Использование [**IFrameworkView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.IFrameworkView) для создания представления CoreWindow.

Обратите внимание, что пошаговое руководство использует [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) для простоты и не описывает межпрограммное взаимодействие XAML.

## <a name="prerequisites"></a>предварительные требования


Вам нужно [подготовить среду для разработки игр UWP с использованием DirectX](prepare-your-dev-environment-for-windows-store-directx-game-development.md). Не требуется шаблон еще, но вам потребуется Microsoft Visual Studio 2015 для загрузки примеры кода для этого пошагового руководства.

Чтобы лучше понять принципы программирования для DirectX 11 и UWP, которые демонстрируются в этом руководстве, ознакомьтесь с разделом [Принципы и аспекты переноса](porting-considerations.md).

## <a name="related-topics"></a>См. также

**Direct3D**

* [Создание шейдеров HLSL в Direct3D 9](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-writing-shaders-9)
* [Шаблоны проекта игр DirectX](user-interface.md)

**Microsoft Store**

* [**Microsoft::WRL::ComPtr**](https://docs.microsoft.com/cpp/windows/comptr-class)
* [**Оператор дескриптора объекта (^)** ](https://docs.microsoft.com/cpp/windows/handle-to-object-operator-hat-cpp-component-extensions)

