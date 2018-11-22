---
author: mtoepke
title: 'Пошаговое руководство: буферы глубины для объемных теней в Direct3D 11'
description: Это пошаговое руководство демонстрирует, как преобразовать для просмотра объемные тени с помощью карт глубин теней, используя Direct3D11 на устройствах всех функциональных уровней Direct3D.
ms.assetid: d15e6501-1a1d-d99c-d1d8-ad79b849db90
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, игры, directx, объемные тени, буферы глубины, directx 11
ms.localizationpriority: medium
ms.openlocfilehash: 269f8423795d7c2515ce555cf8e65f14a8c63431
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7562307"
---
# <a name="walkthrough-implement-shadow-volumes-using-depth-buffers-in-direct3d-11"></a>Пошаговое руководство: реализация объемных теней с помощью буферов глубины в Direct3D 11



В этом пошаговом руководстве демонстрируется отрисовка объемных теней с помощью карт глубины теней при использовании Direct3D11 на устройствах, соответствующих всем уровням компонентов Direct3D.
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
<td align="left"><p><a href="create-depth-buffer-resource--view--and-sampler-state.md">Создание ресурсов устройства для буфера глубины</a></p></td>
<td align="left"><p>Научитесь создавать ресурсы устройств Direct3D, необходимые для поддержки тестирования глубины для теневых томов.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="render-the-shadow-map-to-the-depth-buffer.md">Прорисовка карты теней в буфере глубины</a></p></td>
<td align="left"><p>Выполняя прорисовку с точки зрения освещения, вы можете создать двумерную карту глубин, представляющую объемную тень.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="render-the-scene-with-depth-testing.md">Прорисовка сцены с тестированием глубины</a></p></td>
<td align="left"><p>Создание эффекта тени путем добавления проверки глубины в шейдер вершин (или геометрии) и в построитель текстуры.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="target-a-range-of-hardware.md">Поддержка карт теней на различном оборудовании</a></p></td>
<td align="left"><p>Более высокое качество отображения теней на быстрых устройствах и быстрое отображение на менее мощных устройствах.</p></td>
</tr>
</tbody>
</table>

 

## <a name="shadow-mapping-application-to-direct3d-9-desktop-porting"></a>Перенос приложения наложения теней в Direct3D9 для рабочего стола


Windows8 добавлены функции сравнения глубин уровни 9\_1 и 9\_3. Теперь код рендеринга с объемными тенями можно переносить в DirectX11, и обработчик Direct3D11 будет совместим с устройствами функционального уровня9. Это пошаговое руководство показывает, как любое приложение или игра Direct3D 11 могут реализовать традиционные объемные тени, используя тестирование глубины. Код включает следующий процесс:

1.  Создание ресурсов устройств Direct3D для наложения теней.
2.  Добавление этапа отрисовки для создания карты глубины теней.
3.  Добавление тестирования глубины в главный этап отрисовки.
4.  Реализация необходимого кода шейдера.
5.  Параметры быстрой отрисовки на оборудовании прежних версий.

По завершении этого пошагового руководства вы должны быть знакомы с реализацией базовых совместимых методик создания объемных теней Direct3D11, которые годятся для функциональных уровней 9\_1 и выше.

## <a name="prerequisites"></a>Необходимые условия


Вам нужно [подготовить среду для разработки игр универсальной платформы Windows (UWP) с использованием DirectX](prepare-your-dev-environment-for-windows-store-directx-game-development.md). Шаблон пока не нужен, но потребуется Microsoft Visual Studio2015, чтобы создать пример кода для этого пошагового руководства.

## <a name="related-topics"></a>Связанные разделы


**Direct3D**

* [Создание шейдеров HLSL в Direct3D9](https://msdn.microsoft.com/library/windows/desktop/bb944006)
* [Создание нового проекта DirectX11 для UWP](user-interface.md)

**Технические статьи по наложению теней**

* [Распространенные методики по улучшению карт глубины теней](https://msdn.microsoft.com/library/windows/desktop/ee416324)
* [Каскадные карты теней](https://msdn.microsoft.com/library/windows/desktop/ee416307)

 

 




