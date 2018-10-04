---
author: mijacobs
Description: The universal design features included in every UWP app help you build apps that scale beautifully across a range of devices.
title: Введение в проектирование приложений универсальной платформы Windows (UWP) (приложений для Windows)
ms.assetid: 50A5605E-3A91-41DB-800A-9180717C1E86
ms.author: mijacobs
ms.date: 05/05/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 602a0af685e812f5c65f94d07297cac9fc411923
ms.sourcegitcommit: 5c9a47b135c5f587214675e39c1ac058c0380f4c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2018
ms.locfileid: "4358167"
---
# <a name="introduction-to-uwp-app-design"></a>Введение в проектирование приложений UWP

![пример приложения для работы с освещением](images/introUWP-header.jpg)

Руководство по проектированию для универсальной платформы Windows (UWP) — это ресурс, который поможет проектировать и разрабатывать прекрасные, тщательно проработанные приложения.

Это не список нормативных правил — это постоянно обновляемый документ, изменения в который вносятся по мере развития нашей [системы проектирования Fluent Design](../fluent-design-system/index.md) и с учетом меняющихся потребностей нашего сообщества разработчиков приложений.

Этот раздел основных сведений содержит обзор универсальных функций проектирования, которые включены в каждое приложение UWP, для создания пользовательских интерфейсов, которые превосходно масштабируются для отображения на различных устройствах.

## <a name="effective-pixels-and-scaling"></a>Эффективные пиксели и масштабирование

Приложения UWP работать на всех [устройствах с Windows 10](../devices/index.md), от Телевизора до планшета или настольного компьютера. Так как проектировать пользовательский Интерфейс, который выглядит хорошо на различных устройствах и экранах разного размера?

![одно приложение на различных устройствах](images/universal-image-1.jpg)

UWP обеспечивает путем автоматической настройки элементов пользовательского интерфейса, чтобы они были разборчивыми и легко взаимодействовать на всех устройствах и экранах разного размера.

При запуске приложения на устройстве система использует алгоритм, позволяющий оптимизировать отображение элементов пользовательского интерфейса на экране. Этот алгоритм масштабирования учитывает расстояние, на котором осуществляется просмотр, и растровую плотность (количество пикселей на дюйм) и оптимизирует воспринимаемый (а не физический) размер элементов. Алгоритм масштабирования позволяет добиться того, что шрифт размером 24 пикселя на Surface Hub, который располагается на расстоянии трех метров от пользователя, будет таким же читаемым, как и шрифт аналогичного размера, отображаемый на телефоне с диагональю экрана 5 дюймов, который располагается на расстоянии нескольких сантиметров от пользователя.

![Дистанция просмотра для различных устройств](images/scaling-chart.png)

В силу особенностей работы системы масштабирования, создавая приложение UWP, вы ведете разработку в эффективных, а не физических пикселях. Эффективные пиксели (epx) — это виртуальные единицы измерения, которые используются для обозначения размеров макета и интервалов независимо от плотности экрана. (В наших рекомендациях обозначения epx, ep и px взаимозаменяемы.)

При разработке вы можете игнорировать плотность пикселей и фактическое разрешение экрана. Выполняйте разработку с учетом эффективного разрешения (разрешения в эффективных пикселях) для класса размеров (дополнительные сведения см. в статье [Размеры экрана и точки останова](../layout/screen-sizes-and-breakpoints-for-responsive-design.md)).

> [!TIP]
> При создании макетов в графических редакторах установите количество точек на дюйм равным 72 и соотнесите размеры изображения с эффективным разрешением целевого класса размеров. Список классов размеров и эффективные разрешения см. в статье [Размеры экрана и точки останова](../layout/screen-sizes-and-breakpoints-for-responsive-design.md).

### <a name="multiples-of-four"></a>Кратность 4

:::row:::
    :::column span:::
        The sizes, margins, and positions of UI elements should always be in **multiples of 4 epx** in your UWP apps.

        UWP scales across a range of devices with scaling plateaus of 100%, 125%, 150%, 175%, 200%, 225%, 250%, 300%, 350%, and 400%. The base unit is 4 because it's the only integer that can be scaled by non-whole numbers (e.g. 4*1.5 = 6). Using multiples of four aligns all UI elements with whole pixels and ensures UI elements have crisp, sharp edges. (Note that text doesn't have this requirement; text can have any size and position.)
    :::column-end:::
    :::column:::
        ![grid](images/4epx.svg)
    :::column-end:::
:::row-end:::

## <a name="layout"></a>Макет

Поскольку приложения UWP автоматически масштабируются на всех устройствах, разработка приложения UWP для любого устройства выполняется по тем же принципам. Начнем с самого начала пользовательского интерфейса вашего приложения UWP.

### <a name="windows-frames-and-pages"></a>Окна, кадры и страницы

:::row:::
    :::column:::
        When a UWP app is launched on any Windows 10 device, it launches in a [Window](/uwp/api/Windows.UI.Xaml.Controls.Window) with a [Frame](/uwp/api/Windows.UI.Xaml.Controls.Frame), which can navigate between [Page](/uwp/api/Windows.UI.Xaml.Controls.Page) instances.
    :::column-end:::
    :::column:::
        ![Frame](images/frame.svg)
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        You can think of your app's UI as a collection of pages. It's up to you to decide what should go on each page, and the relationships between pages.

        To learn how you can organize your pages, see [Navigation basics](navigation-basics.md).
    :::column-end:::
    :::column:::
        ![Frame](images/collection-pages.svg)
    :::column-end:::
:::row-end:::

### <a name="page-layout"></a>Макет страницы

Как должны выглядеть эти страницы? Большинство страниц соответствуют общей структуре, чтобы обеспечить единообразие и пользователи могли легко переходить между страницами приложения и внутри них. Страницы обычно содержат три типа элементов пользовательского интерфейса:

- [Элементы навигации](navigation-basics.md) помогают пользователям выбрать то содержимое, которое они хотят отобразить.
- [Командные элементы](commanding-basics.md) служат для выполнения действий, таких как управление, сохранение или отправка содержимого.
- [Элементы содержимого](content-basics.md) служат для отображения содержимого приложения.

![Распространенный шаблон макета](../layout/images/page-components.svg)

Дополнительные сведения о том, как применять общие шаблоны приложения UWP, см. в статье [Макет страницы](../layout/page-layout.md).

Можно также использовать [Windows Template Studio](https://github.com/Microsoft/WindowsTemplateStudio/tree/master) в Visual Studio, чтобы начать работу с макетом приложения.

## <a name="controls"></a>Элементы управления

Платформа разработки UWP предоставляет набор стандартных элементов управления, которые точно будут хорошо работать на всех устройствах под управлением Windows, и они соответствуют нашим принципам [системы Fluent Design](../fluent-design-system/index.md). Эти элементы управления включают в себя как простые элементы управления, такие как кнопки и текстовые элементы, так и сложные элементы управления, которые могут создавать списки на основе набора данных и шаблона.

![Элементы управления UWP](../style/images/color/windows-controls.svg)

Полный список элементов управления UWP и шаблонов, которые могут быть созданы на их основе, см. в разделе [Элементы управления и шаблоны](../controls-and-patterns/index.md).

## <a name="style"></a>Стиль

К стандартным элементам управления автоматически применяются тема системы и цвет элементов системы, они работают со всеми типами ввода данных и масштабируются на всех устройствах. Таким образом они отражают принципы системы Fluent Design — они адаптивны, приятны и красивы. Стандартные элементы управления используют свет, движение и глубину в своих стилях по умолчанию, и с их помощью вы внедряете нашу систему Fluent Design в свое приложение.

Стандартные элементы управления обладают широкими возможностями настройки— можно настроить основной цвет элемента управления или полностью изменить его внешний вид. Чтобы переопределить стили по умолчанию в элементах управления, используйте [облегченное определение стиля](../controls-and-patterns/xaml-styles.md#lightweight-styling) или создайте [пользовательские элементы управления](../controls-and-patterns/control-templates.md) в XAML.

![gif с цветами элементов](images/intro-style.gif)

## <a name="shell"></a>Оболочка

:::row:::
    :::column:::
        Your UWP app will interact with the broader Windows experience with tiles and notifications in the Windows [Shell](../shell/tiles-and-notifications/creating-tiles.md).

        Tiles are displayed in the Start menu and when your app launches, and they provide a glimpse of what's going on in your app. Their power comes from the content behind them, and the intelligence and craft with which they're offered up.

        UWP apps have four tile sizes (small, medium, wide, and large) that can be customized with the app's icon and identity. For guidance on designing tiles for your UWP app, see [Guidelines for tile and icon assets](../shell/tiles-and-notifications/app-assets.md).
    :::column-end:::
    :::column:::
        ![tiles on start menu](images/shell.svg)
    :::column-end:::
:::row-end:::

## <a name="inputs"></a>Элементы ввода данных

:::row:::
    :::column:::
        UWP apps rely on smart interactions. You can design around a click interaction without having to know or define whether the click comes from a mouse, a stylus, or a tap of a finger. However, you can also design your apps for [specific input modes](../input/input-primer.md).
    :::column-end:::
    :::column:::
        ![inputs](images/inputs.svg)
    :::column-end:::
:::row-end:::

## <a name="devices"></a>Устройства

![устройства](../layout/images/size-classes.svg)

Аналогичным образом, хотя UWP автоматически масштабирует приложение для различных устройств, вы также можете [оптимизировать приложение UWP для конкретных устройств](../devices/index.md).

## <a name="usability"></a>Удобство использования

<img src="https://img-prod-cms-rt-microsoft-com.akamaized.net/cms/api/am/imageFileData/REYaAb?ver=727c">

Последнее, но не менее важное— это удобство использования благодаря специальным возможностям. Они позволяют сделать ваше приложение доступным для всех пользователей. Любой пользователь может воспользоваться преимуществами по-настоящему инклюзивных возможностей. См. раздел [Удобство использования приложений UWP](../usability/index.md) чтобы узнать, как сделать приложение удобным для всех пользователей.

Если вы разрабатываете продукт для международной аудитории, вам может пригодиться раздел [Глобализация и локализация](../globalizing/globalizing-portal.md).

Вы также можете добавить в приложение [специальные возможности](../accessibility/accessibility-overview.md) для слабослышащих и слабовидящих пользователей, а также пользователей с трудностями в передвижении. Если специальные возможности встроены в проект вашего приложения с самого начала, [реализация полной доступности вашего приложения](../accessibility/accessibility-in-the-store.md) займет очень мало дополнительного времени и усилий.

## <a name="tools-and-design-toolkits"></a>Средства и наборы инструментов для проектирования

Теперь, когда вы знакомы с основными особенностями проектирования, давайте приступим к разработке собственного приложения UWP.

Мы предоставляем разнообразные инструменты для разработки:

- На [странице наборов инструментов для проектирования](../downloads/index.md) представлены XD, Illustrator, Photoshop, Framer, Sketch, а также другие инструменты и шрифты.

- Чтобы подготовить компьютер к написанию кода для приложений UWP, изучите статью [Начало работы &gt;Начало работы](../../get-started/get-set-up.md).

- Идеи по внедрению пользовательского интерфейса для UWP можно найти в разделе с готовыми [примерами приложений UWP](https://developer.microsoft.com/windows/samples).

## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Blogs/One-Dev-Minute/Designing-Universal-Windows-Platform-apps/player]

## <a name="next-fluent-design-system"></a>Далее: система проектирования Fluent Design

Если вы хотите узнать о принципах проектирования с помощью Fluent Design (системы проектирования корпорации Майкрософт) и ознакомиться с дополнительными компонентами, которые можно встроить в приложение UWP, см. раздел [Система проектирования Fluent Design](../fluent-design-system/index.md).

## <a name="related-articles"></a>Смежные разделы

- [Что такое приложение UWP?](../../get-started/universal-application-platform-guide.md)
- [Система Fluent Design](../fluent-design-system/index.md)
- [Обзор платформы XAML](../../xaml-platform/index.md)
