---
author: mtoepke
title: "Пошаговое руководство - реализация объемных теней с помощью буферов глубины в Direct3D 11"
description: "Это пошаговое руководство демонстрирует, как преобразовать для просмотра объемные тени с помощью карт глубин теней, используя Direct3D 11 на устройствах всех функциональных уровней Direct3D."
ms.assetid: d15e6501-1a1d-d99c-d1d8-ad79b849db90
ms.author: mtoepke
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "windows 10, uwp, игры, directx, объемные тени, буферы глубины, directx 11"
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 00e823b667a4622f6fa2dd213c3277bec9d616a2
ms.lasthandoff: 02/07/2017

---

# <a name="walkthrough-implement-shadow-volumes-using-depth-buffers-in-direct3d-11"></a>Пошаговое руководство: реализация объемных теней с помощью буферов глубины в Direct3D 11


\[ Обновлено для приложений UWP в Windows 10. Статьи, касающиеся Windows 8.x, см. в разделе [Архив](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

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
<td align="left"><p>[Создание ресурсов устройства для буфера глубины](create-depth-buffer-resource--view--and-sampler-state.md)</p></td>
<td align="left"><p>Научитесь создавать ресурсы устройств Direct3D, необходимые для поддержки тестирования глубины для теневых томов.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Прорисовка карты теней в буфере глубины](render-the-shadow-map-to-the-depth-buffer.md)</p></td>
<td align="left"><p>Выполняя прорисовку с точки зрения освещения, вы можете создать двумерную карту глубин, представляющую объемную тень.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[Прорисовка сцены с тестированием глубины](render-the-scene-with-depth-testing.md)</p></td>
<td align="left"><p>Создание эффекта тени путем добавления проверки глубины в шейдер вершин (или геометрии) и в построитель текстуры.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[Поддержка карт теней на различном оборудовании](target-a-range-of-hardware.md)</p></td>
<td align="left"><p>Более высокое качество отображения теней на быстрых устройствах и быстрое отображение на менее мощных устройствах.</p></td>
</tr>
</tbody>
</table>

 

## <a name="shadow-mapping-application-to-direct3d-9-desktop-porting"></a>Перенос приложения наложения теней в Direct3D 9 для рабочего стола


В Windows 8 добавлены функции сравнения глубин в функциональные уровни 9\_1 и 9\_3. Теперь код рендеринга с объемными тенями можно переносить в DirectX 11, и обработчик Direct3D 11 будет совместим с устройствами функционального уровня 9. Это пошаговое руководство показывает, как любое приложение или игра Direct3D 11 могут реализовать традиционные объемные тени, используя тестирование глубины. Код включает следующий процесс:

1.  Создание ресурсов устройств Direct3D для наложения теней.
2.  Добавление этапа отрисовки для создания карты глубины теней.
3.  Добавление тестирования глубины в главный этап отрисовки.
4.  Реализация необходимого кода шейдера.
5.  Параметры быстрой отрисовки на оборудовании прежних версий.

По завершении этого пошагового руководства вы должны быть знакомы с реализацией базовых совместимых методик создания объемных теней Direct3D 11, которые годятся для функциональных уровней 9\_1 и выше.

## <a name="prerequisites"></a>Необходимые условия


Вам нужно [подготовить среду для разработки игр универсальной платформы Windows (UWP) с использованием DirectX](prepare-your-dev-environment-for-windows-store-directx-game-development.md). Шаблон пока не нужен, но чтобы создать пример кода для этого руководства, потребуется Microsoft Visual Studio 2015.

## <a name="related-topics"></a>Связанные разделы


**Direct3D**

* [Создание шейдеров HLSL в Direct3D 9](https://msdn.microsoft.com/library/windows/desktop/bb944006)
* [Создание нового проекта DirectX 11 для UWP](user-interface.md)

**Технические статьи по наложению теней**

* [Распространенные методики по улучшению карт глубины теней](https://msdn.microsoft.com/library/windows/desktop/ee416324)
* [Каскадные карты теней](https://msdn.microsoft.com/library/windows/desktop/ee416307)

 

 





