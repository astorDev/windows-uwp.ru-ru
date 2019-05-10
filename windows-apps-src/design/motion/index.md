---
Description: Содержательные, хорошо спроектированные эффекты движения оживляют приложение и делают его более профессиональным и тщательно проработанным. Помогите пользователям сориентироваться в изменениях контекста, применяя визуальные переходы.
title: Движение и анимация в приложениях UWP
ms.assetid: 21AA1335-765E-433A-85D8-560B340AE966
label: Motion
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: Windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
doc-status: Published
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 1096bdab340c3f0fef24b5815423f72b0f5c8219
ms.sourcegitcommit: cc0ef75f314658b14376eb60ef8e5bb4d7726e04
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65444166"
---
# <a name="motion-for-uwp-apps"></a>Движение в приложениях UWP

![Значок движения](../images/motion-2x.png)

Движения Fluent в вашем приложении должны быть значимыми. Они служат реакцией на поведение пользователя, оживляют пользовательский интерфейс и помогают пользователям работать с вашим приложением. Движения Fluent создают эмоциональную связь между пользователем и цифровым решением. Они основаны на естественных движениях, которые пользователи уже понимают, и расширяем нашу систему, используя их в качестве отправной точки.

## <a name="examples"></a>Примеры

<table>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните, чтобы <a href="xamlcontrolsgallery:/category/Motion">открыть приложение и увидеть эффект движения в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="fluent-motion-principles"></a>Принципы движений Fluent

### <a name="physical"></a>Физическое движение

Двигающиеся объекты ведут себя как объекты в реальном мире. Благодаря плавности и отклику движение делает взаимодействие более естественным, устанавливая с ним эмоциональную связь и делая процесс взаимодействия более персонализированным.

![Пример физического движения в пользовательском интерфейсе](images/Physical.gif)
> При взаимодействии с пользовательским интерфейсом с помощью сенсорного ввода движение интерфейс напрямую связано со скоростью взаимодействия. Так как сенсорный ввод — это непосредственное управление, объект, с которым вы взаимодействуете, влияет на объекты вокруг.

### <a name="functional"></a>Функциональное движение

Движение служит определенной цели и должно быть убедительным. Оно упрощает взаимодействие и позволяет создать иерархию. Движение создает впечатление повышенной производительности и оптимизирует взаимодействие с пользователем, маскируя задержку.

![Пример функционального движения в пользовательском интерфейсе](images/functional.gif)
> Переходы между страницами являются специализированными. Они подсказывают, как страницы связаны друг с другом. Они перемещаются так, что процесс воспринимается быстро, даже если производительность не самая оптимальная.

### <a name="continuous"></a>С задержкой

Плавное движение из точки в точку естественным образом привлекает взгляд и направляет пользователя. Оно элегантно связывает задачи пользователя, делая процесс более удобным и понятным.

![Пример непрерывного движения в пользовательском интерфейсе](images/continuous3.gif)
> Объекты могут перемещаться между сценами или преобразовываться внутри сцены для обеспечения непрерывности и сохранения контекста.

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
