---
author: mijacobs
Description: Good icons harmonize with typography and with the rest of the design language. They don’t mix metaphors, and they communicate only what’s needed, as speedily and simply as possible.
title: Значки
ms.assetid: b90ac02d-5467-4304-99bd-292d6272a014
label: Icons
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
design-contact: Judysa
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 61157ad23eb55447137531922ea23fa0120e2b98
ms.sourcegitcommit: 0ab8f6fac53a6811f977ddc24de039c46c9db0ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/15/2018
ms.locfileid: "1653923"
---
# <a name="icons-for-uwp-apps"></a>Значки для приложений UWP



Хорошие значки согласуются со шрифтовым оформлением и языком проектирования в целом. В них метафоры не смешиваются— они сообщают только то, что нужно, как можно скорее и проще. 

## <a name="linear-scaling-size-ramps"></a>Линейное изменение размера 

<table>
    <tr> 
        <td>16×16 пкс</td>
        <td>24×24 пкс</td>
        <td>32×32 пкс</td>
        <td>48×48 пкс</td>
    </tr>
    <tr> 
        <td><img src="images/icons-16x16.png" alt="Icons at 16x16 effective pixels" /></td>
        <td><img src="images/icons-24x24.png" alt="Icons at 24x24 effective pixels" /></td>
        <td><img src="images/icons-32x32.png" alt="Icons at 32x32 effective pixels" /></td>
        <td><img src="images/icons-48x48.png" alt="Icons at 48x48 effective pixels" /></td>
    </tr>
</table>

## <a name="common-shapes"></a>Общие фигуры

Как правило, значки должны заполнять все отведенное им пространство с небольшой отбивкой. Эти формы— начальная точка для работы с размерами базовых фигур. 

![Сетка 32×32 пкс](images/icons-common-shapes.png)

Используйте фигуру, которая соответствует ориентации значка, и учитывайте в разработке следующие базовые параметры. Значки не обязательно должны полностью соответствовать очертаниям фигуры — при необходимости их можно корректировать для достижения оптимального равновесия. 

<table class="uwpd-noborder">
    <tr>
        <td>Круг<td>
        <td>Квадрат</td>
        <td>Треугольник</td>
    </tr>
    <tr>
        <td><img src="images/icons-common-shapes-examples-1.png" alt="A circle" /><td>
        <td><img src="images/icons-common-shapes-examples-2.png" alt="A square" /></td>
        <td><img src="images/icons-common-shapes-examples-3.png" alt="A triangle " /></td>
    </tr>
        <tr>
        <td>Горизонтальный прямоугольник<td>
        <td colspan="2">Вертикальный прямоугольник</td>        
        </tr>
    <tr>
        <td><img src="images/icons-common-shapes-examples-4.png" alt="A horizontal rectangle" /><td>
        <td colspan="2"><img src="images/icons-common-shapes-examples-5.png" alt="A vertical rectangle" /></td>
         
    </tr>

</table>

## <a name="angles"></a>Углы

Помимо использования одинаковых сеток и толщины линий при создании значков важно использовать общие элементы. 

Использование только этих углов при создании фигур обеспечивает единообразие всех значков и их правильную отрисовку. 

При создании значков эти линии можно объединять, комбинировать, поворачивать и отражать. 

<table>
    <tr>
        <td><b>1:1</b><br/>45°</td>
        <td><b>1:2</b><br />26,57° (вертикальн.)<br/>63,43°(горизонтальн.)</td>
        <td><b>1:3</b><br/>18,43° (вертикальн.)<br/>71,57°(горизонтальн.)</td>
        <td><b>1:4</b><br/>14,04° (вертикальн.)<br/>75,96°(горизонтальн.)</td>
    </tr>
    <tr>
        
        <td><img src="images/icons-grid-1-1.png" alt="1:1" /></td>
        <td><img src="images/icons-grid-1-2.png" alt="1:2" /></td>
        <td><img src="images/icons-grid-1-3.png" alt="1:3" /></td>
        <td><img src="images/icons-grid-1-4.png" alt="1:4" /></td>
    </tr>  
</table>

<p>Вот несколько примеров:</p>

<table>
    <tr>
        <td><img src="images/icons-angles-examples-1.png" alt="A 1:1 angle example" /></td>
        <td><img src="images/icons-angles-examples-2.png" alt="A 1:2 angle example" /></td>
        <td><img src="images/icons-angles-examples-3.png" alt="A 1:3 angle example" /></td>
        <td><img src="images/icons-angles-examples-4.png" alt="A 1:4 angle example" /></td>
    </tr>
</table>

## <a name="curves"></a>Кривые

Кривые линии создаются из секций круга. Наклонять их следует исключительно для прикрепления к пиксельной сетке. 

<table>
    <tr>
        <td>1/4 круга</td>
        <td>1/8 круга</td>
    </tr>
    <tr>
        <td><img src="images/icons-curves-14circle.png" alt="1/4 circle" /></td>
        <td><img src="images/icons-curves-18circle.png" alt="1/8 circle" /></td>
    </tr>
    <tr>
        <td><img src="images/icons-curves-examples-1.png" alt="1/4 cirlce example" /></td>
        <td><img src="images/icons-curves-examples-2.png" alt="1/8 circle example" /></td>
    </tr>    
</table>

## <a name="geometric-construction"></a>Создание объектов по принципам геометрии

При создании значков рекомендуется использовать исключительно геометрические фигуры.

![Значок гитары с геометрическим наложением ](images/icons-geometric-construction.png)

## <a name="filled-shapes"></a>Заполненные фигуры 

Значки при необходимости могут содержать заполненные фигуры, но их размер не должен превышать 4 пкс в значке размером 32×32 пкс. Размер заполненных кругов не должен превышать 6×6 пкс. 

![Заполнение 5×8 пкс ](images/icons-filled-shapes.png)

## <a name="badges"></a>Индикаторы событий

"Индикатор событий"— это общий термин, используемый для обозначения элемента, который добавляется к значку, но который не предназначен для интеграции с основным элементом значка. Как правило, эмблема содержит другие сведения о значке, например его состояние или выполняемое действие. Для обозначения этого элемента используются и другие термины: наложение, аннотация или модификатор. 

![Индикатор событий состояния ](images/icons-badge-status.png)

![Индикатор действий ](images/icons-badge-action.png)

Индикаторы событий состояния используют заполненный цветной объект, который расположен поверх значка, в то время как индикаторы действий интегрируются со значком и совпадают с ним по цвету и толщине линий.

<table>
<tr>
    <td>Стандартные индикаторы событий состояния</td>
    <td>Стандартные индикаторы действий</td>
</tr>
<tr>
    <td><img src="images/icons-badge-common-states-1.png" alt="Status badge " /></td>
    <td><img src="images/icons-badge-common-states-2.png" alt="Action badge " /></td>
</tr>
</table>
<p></p>

### <a name="badge-color"></a>Цвет индикатора событий 

Цвета должны использоваться в индикаторах событий только для обозначения состояния значка. Цвета, используемые в индикаторах событий состояния, передают пользователю определенные эмоционально насыщенные сообщения. 

<table>
<tr><td>Зеленый— #128B44</td><td>Синий— #2C71B9</td><td>Желтый— #FDC214</td></tr>
<tr><td>Положительное: выполнено, завершено </td><td>Нейтральное: справка, уведомление </td><td>Предупреждающие: оповещение, предупреждение </td></tr>
<tr><td><img src="images/icons-color-inbadging-1.png" alt="Green status" /></td><td><img src="images/icons-color-inbadging-2.png" alt="Blue status" /></td>
<td><img src="images/icons-color-inbadging-3.png" alt="Yellow status" /></td></tr>
</table>
<p></p>

### <a name="badge-position"></a>Положение индикатора событий

Положение по умолчанию для любого индикатора событий состояния или действия — внизу справа. Располагать индикаторы иначе следует только при отсутствии технической возможности расположить их указанным способом. 

### <a name="badge-sizing"></a>Размер индикатора событий

Индикаторы событий должны иметь размер 10–18 пкс в сетке 32×32 пкс. 

## <a name="related-articles"></a>Еще по теме

* [Руководство по работе с ресурсами плиток и значков](../shell/tiles-and-notifications/app-assets.md)
