---
Description: В этом разделе описывается новый пользовательский интерфейс Windows для вращения и приводятся рекомендации по работе с пользователем, которые следует учитывать при использовании этого нового механизма взаимодействия в приложении Windows.
title: Смена
ms.assetid: f098bc05-35b3-46b2-9e9b-9ff292d067ca
label: Rotation
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 08d0eb18d59c9a5c19826eb7b6e8d4b65179b6fd
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970109"
---
# <a name="rotation"></a>Смена


В этой статье описывается новый пользовательский интерфейс Windows для вращения и приводятся рекомендации по работе с пользователем, которые следует учитывать при использовании этого нового механизма взаимодействия в приложении Windows.

> **Важные API-интерфейсы**: [**Windows.UI.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input), [**Windows.UI.Xaml.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input)

## <a name="dos-and-donts"></a>Полезные советы

-   Используйте поворот, чтобы дать пользователям возможность непосредственно поворачивать элементы пользовательского интерфейса.

## <a name="additional-usage-guidance"></a>Дополнительное рекомендации по использованию


**Обзор поворота**

Поворот — это методика, оптимизированная для использования приложениями Windows, позволяющая пользователям преобразовывать объект в круглом направлении (по часовой стрелке или против часовой стрелки).

Способ выполнения поворота зависит от устройства ввода:

-   Мышью или активным пером, перемещая поворотный захват выбранного объекта.
-   Касанием или неактивным пером, поворачивая объект в требуемом направлении жестом поворота.

**Варианты использования поворота**

Используйте поворот, чтобы дать пользователям возможность непосредственно поворачивать элементы пользовательского интерфейса. На следующих схемах представлены некоторые из поддерживаемых положений пальцев для поворота.

![Схема с различными поддерживаемыми положениями пальцев для поворота.](images/ux-rotate-positions.png)

**Обратите внимание**    на интуитивно понятное, и в большинстве случаев точка вращения является одной из двух точек касания, если пользователь не может указать точку вращения, не связанную с контактными точками (например, в приложении для рисования или верстки). Следующие изображения демонстрируют, насколько хуже может стать взаимодействие с пользователем, если точка поворота не ограничена таким образом.

На первом изображении представлены начальная (большой палец) и дополнительная (указательный палец) точки касания: указательный палец касается дерева, а большой палец — бревна.

![Изображение с двумя начальными точками касания для жеста поворота.](images/ux-rotate-points1.png)
На втором изображении поворот осуществляется вокруг начальной точки касания (большого пальца). После поворота указательный палец все еще касается ствола дерева, а большой палец — бревна (точки поворота).

![Изображение с повернутым рисунком и точкой поворота, ограниченной одной или двумя начальными точками касания.](images/ux-rotate-points2.png)
На третьем изображении центр рисунка определен приложением (или задан пользователем) как центр поворота. После поворота иллюзия прямого манипулирования исчезает, так как рисунок не вращается вокруг одного из пальцев (если пользователь не выбрал этот параметр).

![Изображение с повернутым рисунком и точкой поворота, ограниченной центром рисунка, а не одной из двух начальных точек касания.](images/ux-rotate-points3.png)
На последнем изображении точка посередине левого края рисунка определена приложением (или задана пользователем) как центр поворота. Если пользователь не выбрал этот параметр, иллюзия прямого манипулирования в этом случае также исчезнет.

![Изображение с повернутым рисунком и точкой поворота, ограниченной центром левой стороны рисунка, а не одной из двух начальных точек касания.](images/ux-rotate-points4.png)

 

Windows 10 поддерживает три типа вращения: Free, CONSTRAINED и Объединенная.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Type</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Свободный поворот</td>
<td align="left"><p>Свободный поворот позволяет пользователю поворачивать содержимое свободно, в любом месте и на 360 градусов. Когда пользователь отпускает объект, он остается в выбранном положении. Свободный поворот полезен в приложениях для рисования и обработки макетов, таких как Microsoft PowerPoint, Word, Visio и Paint, а также Adobe Photoshop, Illustrator и Flash.</p></td>
</tr>
<tr class="even">
<td align="left">Ограниченный поворот</td>
<td align="left"><p>Ограниченный поворот поддерживает свободный поворот во время манипулирования, но применяет точки прикрепления с шагом 90 градусов (0, 90, 180 и 270), когда пользователь отпускает объект. Когда пользователь отпускает объект, он автоматически перемещается к ближайшей точке прикрепления.</p>
<p>Ограниченный поворот является наиболее часто используемым методом поворота и действует подобно прокрутке содержимого. Точки прикрепления позволяют пользователю допускать неточности, обеспечивая при этом достижение цели. Ограниченный поворот полезен в таких приложениях, как веб-браузеры и фотоальбомы.</p></td>
</tr>
<tr class="odd">
<td align="left">Комбинированный поворот</td>
<td align="left"><p>Комбинированный поворот поддерживает свободный поворот с зонами (подобно направляющим в <a href="guidelines-for-panning.md">Руководстве по функции сдвига</a>) каждой из точек прикрепления с шагом 90 градусов, которые применяет ограниченный поворот. Если пользователь отпускает объект за пределами одной из зон точек с шагом 90 градусов, объект остается в этом положении. В ином случае объект автоматически поворачивается до точки прикрепления.</p>
<div class="alert">
<strong>Примечание</strong>  . шина пользовательского интерфейса — это функция, в которой область вокруг целевого объекта ограничивает движение по отношению к определенному значению или расположению, влияющему на выбор.
</div>
<div>
 
</div></td>
</tr>
</tbody>
</table>

## <a name="related-topics"></a>Связанные темы

### <a name="samples"></a>Примеры

- [Пример базового ввода](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
- [Пример ввода с малой задержкой](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
- [Пример режима взаимодействия с пользователем](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
- [Пример визуальных элементов фокуса](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)

### <a name="archive-samples"></a>Примеры архива

- [Ввод: пример событий пользовательского ввода на XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20XAML%20user%20input%20events%20sample)
- [Ввод: пример возможностей устройства](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/Input%20Device%20capabilities%20sample%20(Windows%208))
- [Ввод: пример тестирования сенсорного ввода](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20desktop%20samples/%5BC%2B%2B%5D-Windows%208%20desktop%20samples/C%2B%2B/Windows%208%20desktop%20samples/Input%20Touch%20hit%20testing%20sample)
- [Пример прокрутки XAML, панорамирования и масштабирования](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Universal%20Windows%20app%20samples/111487-Universal%20Windows%20app%20samples/XAML%20scrolling%2C%20panning%2C%20and%20zooming%20sample)
- [Ввод: пример упрощенного рукописного ввода](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Simplified%20ink%20sample)
- [Ввод: жесты и манипуляции с помощью GestureRecognizer](https://docs.microsoft.com/samples/browse/?redirectedfrom=MSDN-samples)
- [Входные данные: пример манипуляций и жестов](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Input%20Gestures%20and%20manipulations%20with%20GestureRecognizer)
- [Пример сенсорного ввода в DirectX](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%2B%2B%5D-Windows%208%20app%20samples/C%2B%2B/Windows%208%20app%20samples/DirectX%20touch%20input%20sample%20(Windows%208))
