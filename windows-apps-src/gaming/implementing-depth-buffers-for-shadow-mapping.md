---
title: 'Пошаговое руководство: буферы глубины для объемных теней в Direct3D 11'
description: Это пошаговое руководство демонстрирует, как преобразовать для просмотра объемные тени с помощью карт глубин теней, используя Direct3D 11 на устройствах всех функциональных уровней Direct3D.
ms.assetid: d15e6501-1a1d-d99c-d1d8-ad79b849db90
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, объемные тени, буферы глубины, directx 11
ms.localizationpriority: medium
ms.openlocfilehash: 2ce0cbd310ea89c5fa7b5c68033402f559768a24
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368512"
---
# <a name="walkthrough-implement-shadow-volumes-using-depth-buffers-in-direct3d-11"></a>Пошаговое руководство: Реализуйте тома с помощью буферов глубины в Direct3D 11



Это пошаговое руководство демонстрирует, как преобразовать для просмотра объемные тени с помощью карт глубин теней, используя Direct3D 11 на устройствах всех функциональных уровней Direct3D.
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
<td align="left"><p><a href="create-depth-buffer-resource--view--and-sampler-state.md">Создание ресурсов устройства буфер глубины</a></p></td>
<td align="left"><p>Научитесь создавать ресурсы устройств Direct3D, необходимые для поддержки тестирования глубины для теневых томов.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="render-the-shadow-map-to-the-depth-buffer.md">Визуализации карты тени в буфер глубины</a></p></td>
<td align="left"><p>Выполняя прорисовку с точки зрения освещения, вы можете создать двумерную карту глубин, представляющую объемную тень.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="render-the-scene-with-depth-testing.md">Отрисовка сцены с тестированием глубины</a></p></td>
<td align="left"><p>Создание эффекта тени путем добавления проверки глубины в шейдер вершин (или геометрии) и в построитель текстуры.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="target-a-range-of-hardware.md">Поддерживает карты теней на широкий спектр оборудования</a></p></td>
<td align="left"><p>Более высокое качество отображения теней на быстрых устройствах и быстрое отображение на менее мощных устройствах.</p></td>
</tr>
</tbody>
</table>

 

## <a name="shadow-mapping-application-to-direct3d-9-desktop-porting"></a>Перенос приложения наложения теней в Direct3D 9 для рабочего стола


Windows 8 adde функции сравнения d глубины на функциональном уровне 9\_1 и 9\_3. Теперь код рендеринга с объемными тенями можно переносить в DirectX 11, и обработчик Direct3D 11 будет совместим с устройствами функционального уровня 9. Это пошаговое руководство показывает, как любое приложение или игра Direct3D 11 могут реализовать традиционные объемные тени, используя тестирование глубины. Код включает следующий процесс:

1.  Создание ресурсов устройств Direct3D для наложения теней.
2.  Добавление этапа отрисовки для создания карты глубины теней.
3.  Добавление тестирования глубины в главный этап отрисовки.
4.  Реализация необходимого кода шейдера.
5.  Параметры быстрой отрисовки на оборудовании прежних версий.

После выполнения данного пошагового руководства, необходимо ознакомиться с тем, как реализовать метод тома простой совместимы с тенью в Direct3D 11, которая совместима с функциональный уровень 9\_1 и более поздних версий.

## <a name="prerequisites"></a>предварительные требования


Вам нужно [подготовить среду для разработки игр универсальной платформы Windows (UWP) с использованием DirectX](prepare-your-dev-environment-for-windows-store-directx-game-development.md). Не требуется шаблон еще, но вам потребуется Microsoft Visual Studio 2015 для создания в образце кода в этом пошаговом руководстве.

## <a name="related-topics"></a>См. также


**Direct3D**

* [Создание шейдеров HLSL в Direct3D 9](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-writing-shaders-9)
* [Создание нового проекта DirectX 11 для универсальной платформы Windows](user-interface.md)

**Тени, сопоставление технические статьи**

* [Распространенные способы оптимизации карты глубины тени](https://docs.microsoft.com/windows/desktop/DxTechArts/common-techniques-to-improve-shadow-depth-maps)
* [Карты каскадного теней](https://docs.microsoft.com/windows/desktop/DxTechArts/cascaded-shadow-maps)

 

 




