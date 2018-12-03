---
Description: Navigation in Universal Windows Platform (UWP) apps is based on a flexible model of navigation structures, navigation elements, and system-level features.
title: Основы навигации для приложений UWP
ms.assetid: B65D33BA-AAFE-434D-B6D5-1A0C49F59664
label: Navigation design basics
template: detail.hbs
op-migration-status: ready
ms.date: 7/16/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 4eb31ed1f802b8827c124958438ceb6c5902aee1
ms.sourcegitcommit: b4c502d69a13340f6e3c887aa3c26ef2aeee9cee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2018
ms.locfileid: "8459966"
---
# <a name="navigation-design-basics-for-uwp-apps"></a>Основы проектирования навигации для приложений UWP

![Заголовок основ навигации](images/nav/navigation-basics-header.jpg)

Если вы рассматриваете приложение как коллекцию страниц, термин *навигация* описывает действие перемещения между страницами и на странице. Навигация является отправной точкой взаимодействия пользователя с интерфейсом. Она служит для поиска содержимого и функций, интересных пользователю. Это очень важно, и иногда сложно сделать правильно.

Есть огромное количество способов реализации навигации. Можно сделать следующее:

:::row:::
    :::column:::
        ![navigation example 1](images/nav/nav-1.svg)

        Require users to go through a series of pages in order.
    :::column-end:::
    :::column:::
        ![navigation example 2](images/nav/nav-2.svg)

        Provide a menu that allows users to jump directly to any page.
    :::column-end:::
    :::column:::
        ![navigation example 3](images/nav/nav-3.svg)

        Place everything on a single page and provide filtering mechanisms for viewing content.
    :::column-end:::
:::row-end:::

Не существует единой схемы навигации, подходящей для каждого приложения. Существует набор принципов и рекомендаций, которым можно следовать, чтобы разработать правильный проект приложения.

## <a name="principles-of-good-navigation"></a>Принципы хорошей навигации

Начнем с базовых принципов разработки удобной навигации:

- **Соблюдайте единообразие:** соответствуйте ожиданиям пользователя.
- **Простота:** не делайте больше, чем нужно сделать.
- **Понятность:** предоставляйте понятные пути навигации и варианты выбора.

### <a name="consistency"></a>Единообразие

Навигация должна быть согласована с ожиданиями пользователей. С помощью [стандартных элементов управления](#use-the-right-controls) , что пользователи, знакомы с и следующие стандартные соглашения для значков, расположение и оформление позволит сделать навигацию предсказуемой и интуитивно понятным для пользователей.

![изображение с компонентами страницы](images/nav/page-components.svg)

> Пользователь ожидает найти определенные элементы пользовательского интерфейса в стандартных местах.

### <a name="simplicity"></a>Простота

Отсутствие большого числа элементов навигации упрощает принятие решений для пользователей. Реализация удобного доступа к важным местам и скрытие менее важных элементов поможет пользователям находить то, что им нужно, быстрее.

:::row:::
    :::column:::
        ![do example](images/nav/do.svg)

        ![navview good](images/nav/navview-good.svg)

        Present navigation items in a familiar navigation menu.
    :::column-end:::
    :::column:::
        ![don't example](images/nav/dont.svg)

        ![navview bad](images/nav/navview-bad.svg)

        Overwhelm users with many navigation options.
    :::column-end:::
:::row-end:::

### <a name="clarity"></a>Понятность

Понятные пути навигации обеспечивают возможность логической навигации для пользователей. Благодаря очевидным вариантам навигации и упрощению связей между страницами пользователь не потеряется в приложении.

![пример исполнения](images/nav/clarity-image.svg)

> Места назначения должны иметь четкую разметку, чтобы пользователи знали, в каком разделе они находятся.

## <a name="general-recommendations"></a>Общие рекомендации

Теперь на основе знаний о наших принципах проектирования — единообразии, простоте и понятности — попробуем сформировать некоторые общие рекомендации.

1. Подумайте о пользователях. Подумайте над тем, какие пути пользователи могут часто использовать для навигации в вашем приложении, а применительно к страницам, поразмышляйте о том, почему пользователь находится на конкретной странице и куда он собирается перейти в последствии.

2. Избегайте иерархий подробный навигации. Если уровней навигации будет больше трех, вы рискуете тем, что пользователь завязнет в многоуровневой иерархии, которую ему сложно будет покинуть.

3. Избегайте прыжков вверх и вниз. Прыжки вверх и вниз возникают, когда есть связанное содержимое, но для перехода к нему необходимо, чтобы пользователь перешел на один уровень вверх, а затем снова вниз.

## <a name="use-the-right-structure"></a>Используйте подходящую структуру

Теперь, когда вы ознакомились с общими принципами навигации, какую структуру вы бы создали для приложения? Существует две общие структуры: плоская и иерархическая.

:::row:::
    :::column:::
        ![Pages arranged in a flat structure](images/nav/flat-lateral-structure.svg)
    :::column-end:::
    :::column span="2":::
        ### Flat/lateral

        In a flat/lateral structure, pages exist side-by-side. You can go from one page to another in any order.

        We recommend using a flat structure when:

        - Страницы могут просматриваться в любом порядке.
        - Страницы явно отличаются друг от друга и не имеют очевидных отношений типа «предок — потомок».
        - Существует в группе менее 8 страниц. <br>
        (Если в группе больше страниц, пользователю может быть трудно понять, чем они отличаются друг от друга, или определить свое текущее местоположение в группе. Если вам кажется, что затруднений не возникнет, используйте одноранговую модель организации страниц. В противном случае используйте иерархическую структуру, чтобы разбить страницы на две или несколько более мелких групп.)

    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![Pages arranged in a hierarchy](images/nav/hierarchical-structure.svg)
    :::column-end:::
    :::column span="2":::
        ### Hierarchical

        In a hierarchical structure, pages are organized into a tree-like structure. Each child page has one parent, but a parent can have one or more child pages. To reach a child page, you travel through the parent.

        Hierarchical structures are good for organizing complex content that spans lots of pages. The downside is some navigation overhead: the deeper the structure, the more clicks it takes to get from page to page.

        We recommend a hierarchical structure when:
        
        - Просмотр страниц должен осуществляться в определенном порядке.
        - Существует четкая иерархическая связь между страницами — "родительский‑дочерний".
        - Группа содержит более 7 страниц.
        
    :::column-end:::
:::row-end:::

:::row:::
    :::column:::
        ![an app with a hybrid structure](images/nav/combining-structures.svg)
    :::column-end:::
    :::column span="2":::
        ### Combining structures

        You don't have choose to one structure or the other; many well-design apps use both. An app can use flat structures for top-level pages that can be viewed in any order, and hierarchical structures for pages that have more complex relationships.

        If your navigation structure has multiple levels, we recommend that peer-to-peer navigation elements only link to the peers within their current subtree. Consider the adjacent illustration, which shows a navigation structure that has two levels:

        - На уровне 1 элемент одноранговой модели навигации должен предоставлять доступ к страницам A, B, C и D.
        - На уровне 2 элементы одноранговой модели навигации страниц A2 должны ссылаться только на другие страницы A2. Они не должны ссылаться на страницы уровня 2 поддерева C.
    :::column-end:::
:::row-end:::

## <a name="use-the-right-controls"></a>Использование правильных элементов управления

Когда вы решите, какую структуру страниц хотите использовать, вам нужно решить, как пользователи будут переходить по этим страницам. UWP предоставляет разнообразные элементы управления навигацией, позволяющие обеспечить единообразную и надежную навигацию в приложении.

:::row:::
    :::column:::
        ![Frame image](images/nav/thumbnail-frame.svg)
    :::column-end:::
    :::column span="2":::
        [**Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame)

        With few exceptions, any app that has multiple pages uses a frame. Typically, an app has a main page that contains the frame and a primary navigation element, such as a navigation view control. When the user selects a page, the frame loads and displays it.
:::row-end:::

:::row:::
    :::column:::
        ![tabs and pivot image](images/nav/thumbnail-tabs-pivot.svg)
    :::column-end:::
    :::column span="2":::
        [**Top navigation and tabs**](../controls-and-patterns/navigationview.md)

        Displays a horizontal list of links to pages at the same level. The [NavigationView](../controls-and-patterns/navigationview.md) control implements the top navigation and tabs patterns.
        
        Use top navigation when:

        - Вы хотите показать все параметры навигации на экране.
        - Желание больше места для содержимого вашего приложения.
        - Значки не описывают четко навигационных категорий.
        
        Используйте вкладки, когда:

        - Вы хотите сохранить состояние страницу и журнал навигации.
        - Предполагается, что пользователи могут переключаться между вкладками часто.

:::row-end:::

:::row:::
    :::column:::
        ![navview image](images/nav/thumbnail-navview.svg)
    :::column-end:::
    :::column span="2":::
        [**Left navigation**](../controls-and-patterns/navigationview.md)

        Displays a vertical list of links to top-level pages. Use when:
        
        - Страницы располагаются на верхнем уровне.
        - Существует множество элементов навигации (более 5)
        - Предполагается, что пользователи не будут часто переключаться между страницами.
        
:::row-end:::

:::row:::
    :::column:::
        ![Master details image](images/nav/thumbnail-master-detail.svg)
    :::column-end:::
    :::column span="2":::
        [**Master/details**](../controls-and-patterns/master-details.md)

        Displays a list (master view) of items. Selecting an item displays its corresponding page in the details section. Use when:
        
        - Предполагается, что пользователи будут часто переключаться между дочерними элементами.
        - Вы хотите позволить пользователю выполнять операции высокого уровня, например удаление или сортировку, с отдельными элементами или группами элементов, а также хотите позволить им просматривать или обновлять сведения о каждом элементе.

        Элемент управления "Основные и подробные данные" хорошо подходит для почтовых приложений, списков контактов и ввода данных.
:::row-end:::

:::row:::
    :::column:::
        ![Hyperlinks and buttons image](images/nav/thumbnail-hyperlinks-buttons.svg)
    :::column-end:::
    :::column span="2":::
        [**Hyperlinks**](../controls-and-patterns/hyperlinks.md)

        Embedded navigation elements can appear in a page's content. Unlike other navigation elements, which should be consistent across the pages, content-embedded navigation elements are unique from page to page.
:::row-end:::

## <a name="next-add-navigation-code-to-your-app"></a>Далее: добавьте в приложение код навигации

В следующей статье [Реализация базовой навигации](navigate-between-two-pages.md) показан код, необходимый для использования элемента управления Frame, чтобы реализовать базовую навигацию между двумя страницами в приложении.
