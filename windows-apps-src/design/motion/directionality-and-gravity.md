---
author: jwmsft
Description: Learn how Fluent motion uses directionality and gravity.
title: Направление и сила притяжения — анимация в приложениях UWP
label: Directionality and gravity
template: detail.hbs
ms.author: jimwalk
ms.date: 10/02/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: b61abf00d5ab8820457742f16feb9b496b7d7d1c
ms.sourcegitcommit: 106aec1e59ba41aae2ac00f909b81bf7121a6ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "4610067"
---
# <a name="directionality-and-gravity"></a>Направление и сила притяжения

Направленные сигналы помогают закрепить ментальную модель пути, который пользователь проделывает в интерфейсе. Очень важно, чтобы направление любого движения поддерживало и непрерывность пространства, и целостность объектов в пространстве.

Направленное движение подвержено воздействию таких сил, как сила притяжения. Приложение силы к движению усиливает естественное восприятие движения.

## <a name="direction-of-movement"></a>Направление движения

:::row:::
    :::column:::
        Direction of movement corresponds to physical motion. Just like in nature, objects can move in any world axis - X,Y,Z. This is how we think of the movement of objects on the screen.

        When you move objects, avoid unnatural collisions. Keep in mind where objects come from and go to, and alway support higher level constructs that may be used in the scene, such as scroll direction or layout hierarchy.
    :::column-end:::
    :::column:::
        ![direction backward in](images/Direction.gif)
    :::column-end:::
:::row-end:::

## <a name="direction-of-navigation"></a>Направление навигации

Для направления навигации между сценами в приложении характерна концептуальность. Пользователи переходят вперед и назад. Сцены появляются в поле зрения и уходят из него. Эти концепции в сочетании с физическим перемещением помогают пользователю.

Когда навигация вызывает перемещение объекта из предыдущей сцены в новую, объект просто перемещается из точки A в точку B. Чтобы повысить реалистичность перемещения объекта, добавляется стандартная анимация, а также ощущение силы притяжения.

При переходе назад перемещение отображается в обратном порядке (из точки B в точку A). Когда пользователи переходят назад, они рассчитывают как можно быстрее вернуться к предыдущему состоянию. Движение происходит быстрее, более прямолинейно и с замедлением в анимации.

Здесь эти принципы применяются, когда выбранный элемент остается на экране во время перехода вперед и назад.

![Пример непрерывного движения в пользовательском интерфейсе](images/continuous3.gif)

Когда навигация вызывает вымещение расположенных на экране элементов, важно показать, куда уходит старая сцена и откуда появляется новая.

Это дает ряд преимуществ:

- Закрепление ментальной модели пространства у пользователя.
- Показ исчезающей сцены предоставляет больше времени для подготовки содержимого для анимации в появляющейся сцене.
- Зрительное повышение производительности приложения.

Существует 4 отдельных направления навигации, которые необходимо учитывать.

:::row:::
    :::column:::
        **Forward-In**

        Celebrate content entering the scene in a manner that does not collide with outgoing content. Content decelerates into the scene.
    :::column-end:::
    :::column:::
        ![direction forward in](images/forwardIN.gif)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        **Forward-Out**

        Content exits quickly. Objects accelerate off screen.
    :::column-end:::
    :::column:::
        ![direction forward out](images/forwardOUT.gif)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        **Backward-In**

        Same as Forward-In, but reversed.
    :::column-end:::
    :::column:::
        ![direction backward in](images/backwardIN.gif)
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        **Backward-Out**

        Same as Forward-Out, but reversed.
    :::column-end:::
    :::column:::
        ![direction backward out](images/backwardOUT.gif)
    :::column-end:::
:::row-end:::

## <a name="gravity"></a>Сила тяготения

Сила тяготения делает процессы более естественными. Объекты, перемещающиеся по оси Z и не привязанные к сцене каким-либо экранным элементом, могут быть подвержены воздействию силы тяготения. Когда объект отрывается от сцены и до достижения им скорости ухода с экрана, сила тяготения тянет объект вниз, что обеспечивает более естественную траекторию перемещения объекта.

Сила тяготения обычно проявляется, когда объекту требуется перейти из одной сцены в другую. Поэтому в приведенной здесь анимации используется концепция силы тяготения.

Здесь на элемент в верхней строке сетки воздействует гравитация, что вызывает его небольшое падение по мере отрыва от своего места и перемещения на передний план.

![направление назад и внутрь](images/continuity-photos.gif)

## <a name="related-articles"></a>Смежные разделы

- [Обзор движения](index.md)
- [Синхронность и реалистичная анимация](timing-and-easing.md)