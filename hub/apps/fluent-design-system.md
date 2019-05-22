---
description: Изучите систему проектирования Fluent Design и способы ее интеграции в приложения.
title: Система Fluent Design для Windows
keywords: структура приложения uwp, универсальная платформа windows, проектирование приложений, интерфейс, система fluent design
ms.date: 03/07/2018
ms.topic: article
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 850f721d69eafc0f730abd85093f0cb028f62254
ms.sourcegitcommit: f0f933d5cf0be734373a7b03e338e65000cc3d80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65984944"
---
# <a name="the-fluent-design-system-for-windows-app-creators"></a>Система Fluent Design для разработчиков приложений для Windows

![Заголовок Fluent Design](images/fluent/fluentdesign-app-header.jpg)

## <a name="introduction"></a>Введение

Fluent Design — это наша система для создания адаптивных, отзывчивых и красивых пользовательских интерфейсов.

## <a name="principles"></a>Принципы

**Адаптивность: взаимодействие интерфейса Fluent с пользователем естественно на любом устройстве**

Пользовательский интерфейс Fluent адаптируется к среде. Пользовательский интерфейс Fluent удобен как на планшете, так и на компьютере и Xbox и прекрасно подходит даже для гарнитуры смешанной реальности. А при добавлении нового оборудования, такого как дополнительный монитор для вашего компьютера, пользовательский интерфейс Fluent использует и его.

**Отзывчивость: пользовательский интерфейс Fluent интуитивно понятен и эффективен**

Пользовательский интерфейс Fluent адаптируется к поведению и намерениям пользователя &mdash; он понимает и заранее предугадывает то, что от него требуется. Он объединяет людей и идеи, которые находятся в противоположных уголках земного шара или совсем рядом друг с другом.

**Красота: пользовательский интерфейс Fluent привлекателен и иммерсивен**

Используя элементы реального мира, пользовательский интерфейс Fluent устанавливают связь с чем-то важным. Он использует свет, тень, движение, глубину и текстуру для организации информации интуитивно и инстинктивно понятным образом.


## <a name="applying-fluent-design-to-your-app-with-uwp"></a>Применение Fluent Design для приложения UWP

![Логотип Fluent Design](images/fluent/fluentdesign_header.png)

Наши рекомендации по проектированию позволяют понять, как применять принципы Fluent Design к приложениям. Какие типы приложений подходят? Хотя многие наши рекомендации можно применять для любой платформы, мы создали универсальную платформу Windows (UWP) для поддержки Fluent Design.

Функции Fluent Design встроены в UWP. Некоторые из этих функций, &mdash;такие как эффективные пиксели и универсальная система ввода&mdash;, являются автоматическими. Для их использования не нужно создавать дополнительный код. Другие функции, например акриловые элементы, являются дополнительными. Они добавляются в приложение путем создания соответствующего кода.

> Теперь элементы управления UWP доступны для использования на компьютере, чтобы вы могли улучшить внешний вид и функциональные возможности своих приложений WPF или приложений для Windows с помощью возможностей Fluent Design. Чтобы узнать больше, ознакомьтесь с разделом [Стандартные элементы управления UWP в приложениях WPF и Windows Forms](/windows/uwp/xaml-platform/xaml-host-controls).

<!-- To apply Fluent Design to your app, follow our guidelines and use UWP (Universal Windows Platform) you can use UWP UI features combined with best practices for creating apps that perform beautifully on all types of Windows-powered devices. -->

Помимо рекомендаций по проектированию, наши статьи о Fluent Design также содержат приемы программирования, позволяющие реализовать проект. UWP использует XAML — язык разметки, который помогает создавать пользовательские интерфейсы. Ниже приведен пример:

```xaml
<Grid BorderBrush="Blue" BorderThickness="4">
    <TextBox Text="Design with XAML" Margin="20" Padding="16,24"/>
</Grid>
```

![](images/fluent/xaml-example.png)


> Если вы мало знакомы с разработкой для UWP, перейдите на нашу [страницу материалов для начала работы с UWP](/windows/uwp/get-started).

## <a name="find-a-natural-fit"></a>Найдите естественный подход

Как сделать приложение предельно удобным на различных устройствах? Необходимо создать ощущение, что оно было разработано с учетом каждого из них. Макет пользовательского интерфейса, адаптированный для различных размеров экрана таким образом, &mdash;чтобы отсутствовало неиспользуемое пространство (а интерфейс при этом не был перегружен)&mdash;, делает взаимодействие естественным, как будто интерфейс был разработан для конкретного устройства.

:::row:::
    :::column:::
        ![fpo image](images/fluent/thumbnail-size-classes.jpg)
    :::column-end:::
    :::column span="2":::
        **Design for the right breakpoints**

        Instead of designing for every individual screen size, focusing on a few key widths (also called "breakpoints") can greatly simplify your designs and code while still making your app look great on small to large screens.

        [Learn about screen sizes and breakpoints](/windows/uwp/design/layout/screen-sizes-and-breakpoints-for-responsive-design)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/fluent/rspd-resize.gif)
    :::column-end:::
    :::column span="2":::
        **Create a responsive layout**

        For an app to feel natural, it should adapt its layout to different screen sizes and devices. You can use automatic sizing, layout panels, visual states, and even separate UI definitions in XAML to create a responsive UI.

        [Learn about responsive design](/windows/uwp/design/layout/responsive-design)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/fluent/devices.jpg)
    :::column-end:::
    :::column span="2":::
        **Design for a spectrum of devices**

        UWP apps can run on a wide variety of Windows-powered devices. It's helpful to understand which devices are available, what they're made for, and how users interact with them.

        [Learn about UWP devices](/windows/uwp/design/devices/)
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/fluent/keyboard-shortcuts.jpg)
    :::column-end:::
    :::column span="2":::
        **Optimize for the right input**

        UWP apps automatically support common mouse, keyboard, pen, and touch interactions&mdash;there's nothing extra you have to do. But you can enhance your app with optimized support for specific inputs, like pen and the Surface Dial.

        [Learn about inputs and interactions](/windows/uwp/design/input/input-primer)
:::row-end:::

## <a name="make-it-intuitive"></a>Сделайте его интуитивно понятным

Взаимодействие является интуитивным, когда оно соответствует тому, что ожидает пользователь. Используя заданные элементы управления и шаблоны, а также поддержку платформой специальных возможностей и глобализации, можно создать не требующий усилий пользовательский интерфейс, который помогает пользователям эффективнее выполнять задачи.

Проявление отзывчивости означает выполнение нужных действий в нужное время.

Пользовательский интерфейс Fluent использует элементы управления и шаблоны согласованно, чтобы они работали так, как ожидает пользователь. Пользовательский интерфейс Fluent доступен для людей с широким диапазоном физических возможности и включает в себя функции глобализации, чтобы его могли использовать люди по всему миру.

:::row:::
    :::column:::
        ![fpo image](images/fluent/thumbnail-navview.png)
    :::column-end:::
    :::column span="2":::
        **Provide the right navigation**

        Create an effortless experience by using the right app structure and navigation components.

        [Learn about navigation](/windows/uwp/design/basics/navigation-basics/)
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/fluent/thumbnail-commanding.png)
    :::column-end:::
    :::column span="2":::
        **Be interactive**

        Buttons, command bars, keyboard shortcuts, and context menus enable users to interact with your app; they're the tools that change a static experience into something dynamic.

        [Learn about commanding](/windows/uwp/design/basics/commanding-basics/)
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/fluent/thumbnail-controls-2.jpg)
    :::column-end:::
    :::column span="2":::
        **Use the right control for the job**

        Controls are the building blocks of the user interface; using the right control helps you create a user interface that behaves the way users expect it to.  UWP provides more than 45 controls,ranging from simple buttons to powerful data controls.

        [Learn about UWP controls](/windows/uwp/design/controls-and-patterns/)
:::row-end:::

:::row:::
    :::column:::
        ![inclusive image](images/fluent/thumbnail-inclusive.png)
    :::column-end:::
    :::column span="2":::
        **Be inclusive**
        A well-design app is accessible to people with disabilities. With some extra coding, you can share your app with people around the world.

        [Learn about Usability](/windows/uwp/design/usability/)
:::row-end:::

## <a name="be-engaging-and-immersive"></a>Обеспечьте привлекательность и иммерсивность

Система Fluent Design не использует яркие эффекты. Она использует физические эффекты, которые действительно улучшают взаимодействие с пользователем, так как моделируют взаимодействие, которое наш мозг запрограммирован обрабатывать эффективно.

## <a name="use-light"></a>Используйте свет

Свет всегда привлекает внимание. Он создает определенную атмосферу и ощущение места, это практический инструмент для подсветки важной информации.

Добавьте свет в свое приложение UWP

:::row:::
    :::column:::
        ![fpo image](images/fluent/Nav_Reveal_Animation.gif)
    :::column-end:::
    :::column span="2":::
        **Reveal highlight**

        [Reveal highlight](/windows/uwp/design/style/reveal) uses light to make interactive elements stand out. Light illuminates the elements the user can interact with, revealing hidden borders. Reveal is automatically enabled on some controls, such as list view and grid view. You can enable it on other controls by applying our predefined Reveal highlight styles.
:::row-end:::

:::row:::
    :::column:::
        ![fpo image](images/fluent/traveling-focus-fullscreen-light-rf.gif)
    :::column-end:::
    :::column span="2":::
        **Reveal focus**

        [Reveal focus](/windows/uwp/design/style/reveal-focus) uses light to call attention to the element that currently has input focus.
:::row-end:::

## <a name="create-a-sense-of-depth"></a>Создайте эффект глубины

Мы живем в трехмерном мире. Специально реализуя глубину в пользовательском интерфейсе, мы преобразуем плоский двухмерный интерфейс в нечто большее &mdash; в нечто, что эффективно представляет информацию и понятия, создавая визуальную иерархию. Такой интерфейс по-новому представляет связи между объектами в многослойной физической среде.

Добавьте глубину в свое приложение UWP

:::row:::
    :::column:::
        ![fpo image](images/fluent/_parallax_v2.gif)
    :::column-end:::
    :::column span="2":::
        **Parallax**

        [Parallax](/windows/uwp/design/motion/parallax) creates the illusion of depth by making items in the foreground appear to move more quickly than items in the background.
:::row-end:::

## <a name="incorporate-motion"></a>Включите движение

Проектирование движения можно расценивать как съемку фильма. Плавные переходы позволяют сконцентрироваться на происходящем и оживляют изображение. Если реализовать это в пользовательском интерфейсе, пользователи будут логично и удобно, словно в кино, переходить от одной задачи к другой.

Добавьте движение в свое приложение UWP

:::row:::
    :::column:::
        ![continuity gif](images/fluent/continuityXbox.gif)
    :::column-end:::
    :::column span="2":::
        **Connected animations**

        [Connected animations](/windows/uwp/design/motion/connected-animation) help the user maintain context by creating a seamless transition between scenes.
:::row-end:::

## <a name="build-it-with-the-right-material"></a>Используйте подходящие материалы

В реальном мире нас окружают доступные чувственному восприятию, полные жизни вещи. Они гнутся, тянутся, пружинят, разбиваются и скользят. Эти качества материалов можно перенести и в цифровую среду, чтобы пользователям захотелось прикоснуться к тому, что они видят на экранах.

Добавьте материалы в свое приложение UWP

:::row:::
    :::column:::
        ![fpo image](images/fluent/acrylic_lighttheme_base.png)
    :::column-end:::
    :::column span="2":::
        **Acrylic**

        [Acrylic](/windows/uwp/design/style/acrylic) is a translucent material that lets the user see layers of content, establishing a hierarchy of UI elements.
:::row-end:::

## <a name="design-toolkits-and-code-samples"></a>Наборы средств для проектирования и примеры кода

Хотите приступить к созданию собственных приложений с помощью системы Fluent Design? Наши наборы средств для Adobe XD, Adobe Illustrator, Adobe Photoshop, Framer и Sketch помогут быстро приступить к работе над проектом, а примеры кода — быстрее перейти к кодированию.

:::row:::
    :::column:::
        ![fpo image](images/fluent/thumbnail-toolkits.jpg)
    :::column-end:::
    :::column span="2":::
        **Design toolkits and samples page**

        Check out our [Design toolkits and samples page](/windows/uwp/design/downloads/)
:::row-end:::









