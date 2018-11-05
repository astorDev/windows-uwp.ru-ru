---
author: mijacobs
Description: Purposeful, well-designed motion brings your app to life and makes the experience feel crafted and polished. Help users understand context changes, and tie experiences together with visual transitions.
title: Движение и анимация в приложениях UWP
ms.assetid: 21AA1335-765E-433A-85D8-560B340AE966
label: Motion
template: detail.hbs
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: def37c31ef0a64a9b1017d40d281457513fba0db
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6035613"
---
# <a name="motion-for-uwp-apps"></a>Движение в приложениях UWP

![главное изображение](images/header-motion2.svg)

Движения Fluent в вашем приложении должны быть значимыми. Они служат реакцией на поведение пользователя, оживляют пользовательский интерфейс и помогают пользователям работать с вашим приложением. Движения Fluent создают эмоциональную связь между пользователем и цифровым решением. Они основаны на естественных движениях, которые пользователи уже понимают, и расширяем нашу систему, используя их в качестве отправной точки.

## <a name="fluent-motion-principles"></a>Принципы движений Fluent

### <a name="physical"></a>Физическое движение

Двигающиеся объекты ведут себя как объекты в реальном мире. Благодаря плавности и отклику движение делает взаимодействие более естественным, устанавливая с ним эмоциональную связь и делая процесс взаимодействия более персонализированным.

![Пример физического движения в пользовательском интерфейсе](images/Physical.gif)
> При взаимодействии с пользовательским интерфейсом с помощью сенсорного ввода движение интерфейс напрямую связано со скоростью взаимодействия. Так как сенсорное управление— это прямое манипулирование, объект, с которым вы взаимодействуете, влияет на объекты вокруг.

### <a name="functional"></a>Функциональное движение

Движение служит определенной цели и должно быть убедительным. Оно упрощает взаимодействие и позволяет создать иерархию. Движение создает впечатление улучшенной производительность и оптимизирует взаимодействие с пользователем, маскируя задержку.

![Пример функционального движения в пользовательском интерфейсе](images/functional.gif)
> Переходы между страницами являются специализированными. Они подсказывают, как страницы связаны друг с другом. Они перемещаются так, что процесс воспринимается быстро, даже если производительность не самая оптимальная.

### <a name="continuous"></a>Непрерывное движение

Плавное движение из точки в точку естественным образом привлекает взгляд и направляет пользователя. Оно элегантно связывает задачи пользователя, делая процесс более удобным и понятным.

![Пример непрерывного движения в пользовательском интерфейсе](images/continuous3.gif)
> Объекты могут перемещаться между сценами или преобразоваться внутри сцены для обеспечения непрерывности и сохранения контекста.

### <a name="contextual"></a>Контекстуальные движения

Интеллектуальное движение дает пользователям обратную связь в ответ на их взаимодействие с интерфейсом. Взаимодействие ориентировано на пользователя. Движение соответствует форм-фактору и создано специально для сценария использования. Оно должно быть комфортным для каждого пользователя.

![Пример контекстуального движения в пользовательском интерфейсе](images/Contextual.gif)
> Анимация должна быть привязана к взаимодействию с пользователем. Контекстное меню открывается в той точке, где пользователь его активировал. 

## <a name="motion-articles"></a>Статьи о движении

:::row:::
    :::column:::
        ### [Timing and easing](timing-and-easing.md)
        Timing and easing are important elements that make motion feel natural for objects entering, exiting, or moving within the UI.
    :::column-end:::
    :::column:::
        ### [Directionality and gravity](directionality-and-gravity.md)
        Directional signals help provide a solid mental model of the journey a user takes across experiences. Directional movement is subject to forces like gravity, which reinforces the natural feel of the movement.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        ### [Page transitions](page-transitions.md)
        Page transitions navigate users between pages in an app, providing feedback about the relationship between pages. They help users understand where they are in the navigation hierarchy.
    :::column-end:::
    :::column:::
        ### [Connected animation](connected-animation.md)
        Connected animations let you create a dynamic and compelling navigation experience by animating the transition of an element between two different views.
    :::column-end:::
:::row-end:::
