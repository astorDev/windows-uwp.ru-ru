---
description: Изучите систему проектирования Fluent и способы ее интеграции в приложения.
title: Система проектирования Fluent для Windows
author: mijacobs
keywords: структура приложения uwp, универсальная платформа windows, проектирование приложений, интерфейс, система проектирования fluent
ms.author: mijacobs
ms.date: 3/7/2018
ms.topic: article
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: c61eb71a82234a1339295536140121d80f83a033
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7297812"
---
# <a name="the-fluent-design-system-for-windows-app-creators"></a>Creators приложения Fluent разработки системы для Windows

![Fluent заголовок проектирования](images/fluentdesign-app-header.jpg)

## <a name="introduction"></a>Введение

Система проектирования Fluent Design — это наша система для создания адаптивных, отзывчивых и красивых пользовательских интерфейсов.

## <a name="principles"></a>Принципы

**Адаптивность: взаимодействия типа Fluent естественны на любом устройстве**

Взаимодействия типа Fluent адаптируются к среде. Взаимодействия типа Fluent удобны как на планшете, ПК и Xbox, прекрасно подходят даже для гарнитуры смешанной реальности. А при добавлении нового оборудования, такого как дополнительный монитор для вашего компьютера взаимодействие Fluent использует и его.

**Отзывчивость: взаимодействия типа Fluent интуитивно понятны и эффективны**

Взаимодействия типа Fluent адаптируются к поведению и намерениям пользователя &mdash; они понимают и заранее предугадывают то, что от них требуется. Они объединяют людей и идеи, которые находятся в противоположных уголках земного шара или сосем рядом друг с другом.

**Красота: взаимодействия типа Fluent привлекательны и иммерсивны**

Включая элементы реального мира, взаимодействия типа Fluent устанавливают связь с фундаментальными аспектами. Они используют свет, тень, движение, глубину и текстуру для организации информации интуитивно и инстинктивно понятным образом.


## <a name="applying-fluent-design-to-your-app-with-uwp"></a>Применение Fluent Design для вашего приложения с помощью UWP

![Логотип проектирования Fluent Design](images/fluentdesign_header.png)

Наши рекомендации по проектированию о том, как применить принципов проектирования Fluent Design для приложений. Какой тип приложения? Хотя многие из наших рекомендациях могут применяться на любую платформу, мы создали UWP (универсальной платформы Windows) для поддержки системы проектирования Fluent Design.

Функции Fluent Design встроены в UWP. Некоторые из этих функций, такие как эффективные пиксели и универсальная система ввода, являются автоматическими. Для их использования не нужно создавать дополнительный код. Другие функции, например акриловые элементы, являются дополнительными; они добавляются в приложение путем создания соответствующего кода.

> Теперь элементы управления UWP доступны для использования на компьютере, чтобы вы могли улучшить внешний вид и функциональные возможности своих WPF- или Windows-приложений с помощью функций системы проектирования Fluent Design. Дополнительные сведения см. в разделе [элементы управления UWP узла в приложениях WPF и Windows Forms](/windows/uwp/xaml-platform/xaml-host-controls).

<!-- To apply Fluent Design to your app, follow our guidelines and use UWP (Universal Windows Platform) you can use UWP UI features combined with best practices for creating apps that perform beautifully on all types of Windows-powered devices. -->

В дополнение к проектированию наши статьи проектирования Fluent Design также показано, как написать код, который делает ваших проектах произойдет. Платформа UWP использует XAML, на основе разметки языка, который облегчает для создания пользовательских интерфейсов. Вот пример.

```xaml
<Grid BorderBrush="Blue" BorderThickness="4">
    <TextBox Text="Design with XAML" Margin="20" Padding="24,16"/>
</Grid>
```

![](images/xaml-example.png)


> Если вы новичок в разработке для UWP, ознакомьтесь с нашей [Начало работы с UWP страницы](https://developer.microsoft.com/windows/apps/getstarted).

## <a name="find-a-natural-fit"></a>Найдите естественный подход

Как сделать приложение предельно удобным на различных устройствах? Необходимо создать ощущение, что оно было разработано с учетом каждого из них. Макет пользовательского интерфейса, адаптированный для различных размеров экрана таким образом, чтобы отсутствовало неиспользуемое пространство (а интерфейс при этом не был перегружен), делает взаимодействие естественным, как будто интерфейс был разработан для конкретного устройства.

:::row:::
    :::column:::
        ![fpo image](images/thumbnail-size-classes.jpg)
    :::column-end:::
    :::column span="2":::
        **Design for the right breakpoints**

        Instead of designing for every individual screen size, focusing on a few key widths (also called "breakpoints") can greatly simplify your designs and code while still making your app look great on small to large screens.

        [Learn about screen sizes and breakpoints](/windows/uwp/design/layout/screen-sizes-and-breakpoints-for-responsive-design)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/rspd-resize.gif)
    :::column-end:::
    :::column span="2":::
        **Create a responsive layout**

        For an app to feel natural, it should adapt its layout to different screen sizes and devices. You can use automatic sizing, layout panels, visual states, and even separate UI definitions in XAML to create a responsive UI.

        [Learn about responsive design](/windows/uwp/design/layout/responsive-design)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/devices.jpg)
    :::column-end:::
    :::column span="2":::
        **Design for a spectrum of devices**

        UWP apps can run on a wide variety of Windows-powered devices. It's helpful to understand which devices are available, what they're made for, and how users interact with them.

        [Learn about UWP devices](/windows/uwp/design/devices/)
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/keyboard-shortcuts.jpg)
    :::column-end:::
    :::column span="2":::
        **Optimize for the right input**

        UWP apps automatically support common mouse, keyboard, pen, and touch interactions&mdash;there's nothing extra you have to do. But you can enhance your app with optimized support for specific inputs, like pen and the Surface Dial.

        [Learn about inputs and interactions](/windows/uwp/design/input/input-primer)
:::row-end:::

## <a name="make-it-intuitive"></a>Облегчения управления ими интуитивный

Взаимодействие является интуитивным, когда оно соответствует тому, что ожидает пользователь. Используя заданные элементы управления и шаблоны, а также поддержку платформой специальных возможностей и глобализации, можно создавать не требующие усилий взаимодействия, которые помогают пользователям эффективнее выполнять задачи.

Проявление отзывчивости означает выполнение нужных действий в нужное время.

Взаимодействия типа Fluent используют элементы управления и шаблоны согласованно, чтобы они работали так, как ожидает пользователь. Взаимодействия типа Fluent доступным для людей с широким диапазоном физических возможности и включают функции глобализации, чтобы их могли использовать люди по всему миру.

:::row:::
    :::column:::
        ![fpo image](images/thumbnail-navview.png)
    :::column-end:::
    :::column span="2":::
        **Provide the right navigation**

        Create an effortless experience by using the right app structure and navigation components.

        [Learn about navigation](/windows/uwp/design/basics/navigation-basics/)
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/thumbnail-commanding.png)
    :::column-end:::
    :::column span="2":::
        **Be interactive**

        Buttons, command bars, keyboard shortcuts, and context menus enable users to interact with your app; they're the tools that change a static experience into something dynamic.

        [Learn about commanding](/windows/uwp/design/basics/commanding-basics/)
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/thumbnail-controls-2.jpg)
    :::column-end:::
    :::column span="2":::
        **Use the right control for the job**

        Controls are the building blocks of the user interface; using the right control helps you create a user interface that behaves the way users expect it to.  UWP provides more than 45 controls,ranging from simple buttons to powerful data controls.

        [Learn about UWP controls](/windows/uwp/design/controls-and-patterns/)
:::row-end:::

:::row:::
    :::column:::
        ![inclusive image](images/thumbnail-inclusive.png)
    :::column-end:::
    :::column span="2":::
        **Be inclusive**
        A well-design app is accessible to people with disabilities. With some extra coding, you can share your app with people around the world.

        [Learn about Usability](/windows/uwp/design/usability/)
:::row-end:::

## <a name="be-engaging-and-immersive"></a>Обеспечьте привлекательность и иммерсивность

Fluent Design не использует яркие эффекты. Он использует физические эффекты, которые действительно улучшают взаимодействие с пользователем, так как эмулируют взаимодействие, которое наш мозг запрограммирован обрабатывать эффективно.

## <a name="use-light"></a>Используйте свет

Свет всегда привлекает внимание. Он создает определенную атмосферу и ощущение места, это практический инструмент для подсветки важной информации.

Добавьте свет в свое приложение UWP:

:::row:::
    :::column:::
        ![fpo image](../style/images/Nav_Reveal_Animation.gif)
    :::column-end:::
    :::column span="2":::
        **Reveal highlight**

        [Reveal highlight](../style/reveal.md) uses light to make interactive elements stand out. Light illuminates the elements the user can interact with, revealing hidden borders. Reveal is automatically enabled on some controls, such as list view and grid view. You can enable it on other controls by applying our predefined Reveal highlight styles.
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](../style/images/traveling-focus-fullscreen-light-rf.gif)
    :::column-end:::
    :::column span="2":::
        **Reveal focus**

        [Reveal focus](../style/reveal-focus.md) uses light to call attention to the element that currently has input focus.
:::row-end:::

## <a name="create-a-sense-of-depth"></a>Создайте эффект глубины

Мы живем в трехмерном мире. Специально реализуя глубину в пользовательском интерфейсе, мы преобразуем плоский двухмерный интерфейс в нечто большее, в нечто, что эффективно представляет информацию и понятия, создавая визуальную иерархию. Такой интерфейс по-новому представляет связи между объектами в многослойной физической среде.

Добавьте глубину в свое приложение UWP:

:::row:::
    :::column:::
        ![fpo image](../motion/images/_parallax_v2.gif)
    :::column-end:::
    :::column span="2":::
        **Parallax**

        [Parallax](../motion/parallax.md) creates the illusion of depth by making items in the foreground appear to move more quickly than items in the background.
:::row-end:::

## <a name="incorporate-motion"></a>Включите движение

Проектирование движения можно расценивать как съемку фильма. Плавные переходы позволяют сконцентрироваться на происходящем и оживляют изображение. Если реализовать это в пользовательском интерфейсе, пользователи будут логично и удобно, словно в кино, переходить от одной задачи к другой.

Добавьте движение в свое приложение UWP:

:::row:::
    :::column:::
        ![continuity gif](images/continuityXbox.gif)
    :::column-end:::
    :::column span="2":::
        **Connected animations**

        [Connected animations](../motion/connected-animation.md) help the user maintain context by creating a seamless transition between scenes.
:::row-end:::

## <a name="build-it-with-the-right-material"></a>Используйте подходящие материалы

В реальном мире нас окружают доступные чувственному восприятию, полные жизни вещи. Они гнутся, тянутся, пружинят, разбиваются и скользят. Эти качества материалов можно перенести и в цифровую среду, чтобы пользователям захотелось прикоснуться к тому, что они видят на экранах.

Добавьте материалы в свое приложение UWP:

:::row:::
    :::column:::
        ![fpo image](../style/images/acrylic_lighttheme_base.png)
    :::column-end:::
    :::column span="2":::
        **Acrylic**

        [Acrylic](../style/acrylic.md) is a translucent material that lets the user see layers of content, establishing a hierarchy of UI elements.
:::row-end:::

## <a name="design-toolkits-and-code-samples"></a>Наборы инструментов для проектирования и примеры кода

Хотите приступить к созданию собственных приложений с помощью системы проектирования Fluent Design? Наши наборы инструментов для Adobe XD, Adobe Illustrator, Adobe Photoshop, Framer и Sketchим помогут быстро приступить к работе над проектом, а образцы — быстрее перейти к кодированию.

:::row:::
    :::column:::
        ![fpo image](images/thumbnail-toolkits.jpg)
    :::column-end:::
    :::column span="2":::
        **Design toolkits and samples page**

        Check out our [Design toolkits and samples page](/windows/uwp/design/downloads/)
:::row-end:::









