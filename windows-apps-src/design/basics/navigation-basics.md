---
author: serenaz
Description: Navigation in Universal Windows Platform (UWP) apps is based on a flexible model of navigation structures, navigation elements, and system-level features.
title: Основы навигации для приложений UWP
ms.assetid: B65D33BA-AAFE-434D-B6D5-1A0C49F59664
label: Navigation design basics
template: detail.hbs
op-migration-status: ready
ms.author: sezhen
ms.date: 11/27/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 0980f24394a075596b60e4a8005b303857b91304
ms.sourcegitcommit: 517c83baffd344d4c705bc644d7c6d2b1a4c7e1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "1843074"
---
# <a name="navigation-design-basics-for-uwp-apps"></a>Основы проектирования навигации для приложений UWP

![Заголовок основ навигации](images/nav/navigation-basics-header.jpg)

Если вы рассматриваете приложение как коллекцию страниц, термин *навигация* описывает действие перемещения между страницами и на странице. Навигация является отправной точкой взаимодействия пользователя с интерфейсом. Она служит для поиска содержимого и функций, интересных пользователю. Это очень важно, и иногда сложно сделать правильно.

Есть огромное количество способов реализации навигации. Можно сделать следующее:

:::row::: :::column::: ![Пример навигации 1](images/nav/nav-1.svg)

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

Навигация должна быть согласована с ожиданиями пользователей. Использование [стандартных элементов управления](#use-the-right-controls), с которыми пользователи уже знакомы, и общепринятых обозначений для значков, расположений и стилей позволит сделать навигацию предсказуемой и интуитивно понятной для пользователей.

![изображение с компонентами страницы](images/nav/page-components.svg)

> Пользователь ожидает найти определенные элементы пользовательского интерфейса в стандартных местах.

### <a name="simplicity"></a>Простота

Отсутствие большого числа элементов навигации упрощает принятие решений для пользователей. Реализация удобного доступа к важным местам и скрытие менее важных элементов поможет пользователям находить то, что им нужно, быстрее.

:::row::: :::column::: ![пример исполнения](images/nav/do.svg)

        ![navview good](images/nav/navview-good.svg)

        Present navigation items in a familiar navigation menu.
    :::column-end:::
    :::column:::
        ![don't example](images/nav/dont.svg)

        ![navview bad](images/nav/navview-bad.svg)

        Constantly provide many navigation options to overwhelm the user.
    :::column-end:::
:::row-end:::

### <a name="clarity"></a>Понятность

Понятные пути навигации обеспечивают возможность логической навигации для пользователей. Благодаря очевидным вариантам навигации и упрощению связей между страницами пользователь не потеряется в приложении.

![пример исполнения](images/nav/clarity-image.svg)

> Места назначения должны иметь четкую разметку, чтобы пользователи знали, в каком разделе они находятся.

## <a name="general-recommendations"></a>Общие рекомендации

Теперь на основе знаний о наших принципах проектирования — единообразии, простоте и понятности — попробуем сформировать некоторые общие рекомендации.

1. Подумайте о пользователях. Подумайте над тем, какие пути пользователи могут часто использовать для навигации в вашем приложении, а применительно к страницам, поразмышляйте о том, почему пользователь находится на конкретной странице и куда он собирается перейти в последствии. 

2. Избегайте глубоких навигационных иерархий. Если уровней навигации будет больше трех, вы рискуете тем, что пользователь завязнет в многоуровневой иерархии, которую ему сложно будет покинуть.

3. Избегайте прыжков вверх и вниз. Прыжки вверх и вниз возникают, когда есть связанное содержимое, но для перехода к нему необходимо, чтобы пользователь перешел на один уровень вверх, а затем снова вниз.

## <a name="use-the-right-structure"></a>Используйте подходящую структуру

Теперь, когда вы ознакомились с общими принципами навигации, какую структуру вы бы создали для приложения? Существует две общие структуры: плоская и иерархическая.

:::row::: :::column::: ![Страницы, упорядоченные в виде плоской структуры](images/nav/flat-lateral-structure.svg) :::column-end::: :::column span="2":::
        ### Flat/lateral

        In a flat/lateral structure, pages exist side-by-side. You can go from on page to another in any order. 

        We recommend using a flat structure when:
        <ul>
        <li>The pages can be viewed in any order.</li>
        <li>The pages are clearly distinct from each other and don't have an obvious parent/child relationship.</li>
        <li>There are fewer than 8 pages in the group.<br/>
        (When there are more pages, it might be difficult for users to understand how the pages are unique or to understand their current location within the group. If you don't think that's an issue for your app, go ahead and make the pages peers. Otherwise, consider using a hierarchical structure to break the pages into two or more smaller groups.)</li>
        </ul>

    :::column-end:::
:::row-end:::

:::row::: :::column::: ![Иерархическая организация страниц](images/nav/hierarchical-structure.svg) :::column-end::: :::column span="2":::
        ### Hierarchical

        In a hierarchical structure, pages are organized into a tree-like structure. Each child page has one parent, but a parent can have one or more child pages. To reach a child page, you travel through the parent.

        Hierarchical structures are good for organizing complex content that spans lots of pages. The downside is some navigation overhead: the deeper the structure, the more clicks it takes to get from page to page. 

        We recommend a hiearchical structure when:
        <ul>
        <li>Pages should be traversed in a specific order.</li>
        <li>There is a clear parent-child relationship between pages.</li>
        <li>There are more than 7 pages in the group.</li>
        </ul>
    :::column-end:::
:::row-end:::

:::row::: :::column::: ![приложение с гибридной структурой](images/nav/combining-structures.svg) :::column-end::: :::column span="2":::
        ### Combining structures

        You don't have choose to one structure or the other; many well-design apps use both. An app can use flat structures for top-level pages that can be viewed in any order, and hierarchical structures for pages that have more complex relationships.

        If your navigation structure has multiple levels, we recommend that peer-to-peer navigation elements only link to the peers within their current subtree. Consider the adjacent illustration, which shows a navigation structure that has two levels:

        - At level 1, the peer-to-peer navigation element should provide access to pages A, B, C, and D.
        - At level 2, the peer-to-peer navigation elements for the A2 pages should only link to the other A2 pages. They should not link to level 2 pages in the C subtree. 
    :::column-end:::
:::row-end:::

## <a name="use-the-right-controls"></a>Использование правильных элементов управления

Когда вы решите, какую структуру страниц хотите использовать, вам нужно решить, как пользователи будут переходить по этим страницам. UWP предоставляет разнообразные элементы управления навигацией, позволяющие обеспечить единообразную и надежную навигацию в приложении. 

Элемент управления навигацией рекомендуется выбирать на основе числа элементов навигации в приложении. Если вы используете пять или менее элементов навигации, используйте навигацию верхнего уровня, например, [вкладки и сводки](../controls-and-patterns/tabs-pivot.md). Если в приложении шесть или более элементов навигации, используйте левое меню навигации, например, [представление навигации](../controls-and-patterns/navigationview.md) или [основные и подробные данные](../controls-and-patterns/master-details.md).

:::row::: :::column::: ![Изображение рамки](images/nav/thumbnail-frame.svg) :::column-end::: :::column span="2"::: <a href="https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.Frame"><b>Рамка</b></a>

        With few exceptions, any app that has multiple pages uses a frame. Typically, an app has a main page that contains the frame and a primary navigation element, such as a navigation view control. When the user selects a page, the frame loads and displays it.
:::row-end:::

:::row::: :::column::: ![изображение вкладок и сводки](images/nav/thumbnail-tabs-pivot.svg) :::column-end::: :::column span="2"::: <a href="../controls-and-patterns/tabs-pivot.md"><b>Вкладки и сводка</b></a><br>

        Displays a horizontal list of links to pages at the same level. Use when:
        <ul>
        <li>There are 2-5 pages. (You can use tabs/pivots when there are more than 5 pages, but it might be difficult to fit all the tabs/pivots on the screen.)</li>
        <li>You expect users to switch between pages frequently.</li>
        </ul>
:::row-end:::

:::row::: :::column::: ![изображение вкладок и сводки](images/nav/thumbnail-navview.svg) :::column-end::: :::column span="2"::: <a href="../controls-and-patterns/navigationview.md"><b>Представление навигации</b></a><br>

        Displays a vertical list of links to top-level pages. Use when:
        <ul>
        <li>The pages exist at the top level.</li>
        <li>There are many navigational items (more than 5).</li>
        <li>You don't expect users to switch between pages frequently.</li>
        </ul>
:::row-end:::

:::row::: :::column::: ![Изображение основных и подробных данных](images/nav/thumbnail-master-detail.svg) :::column-end::: :::column span="2"::: <a href="../controls-and-patterns/master-details.md"><b>Основные и подробные данные</b></a><br>

        Displays a list (master view) of items. Selecting an item displays its corresponding page in the details section. Use when:
        <ul>
        <li>You expect users to switch between child items frequently.</li>
        <li>You want to enable the user to perform high-level operations, such as deleting or sorting, on individual items or groups of items, and also want to enable the user to view or update the details for each item.</li>
        </ul>

        Master/details is well suited for email inboxes, contact lists, and data entry.
:::row-end:::

:::row::: :::column::: ![Изображение гиперссылок и кнопок](images/nav/thumbnail-hyperlinks-buttons.svg) :::column-end::: :::column span="2"::: <a href="../controls-and-patterns/hyperlinks.md"><b>Гиперссылки</b></a> и <a href="../controls-and-patterns/buttons.md"><b>кнопки</b></a><br>

        Embedded navigation elements can appear in a page's content. Unlike other navigation elements, which should be consistent across the pages, content-embedded navigation elements are unique from page to page.
:::row-end:::

## <a name="next-add-navigation-code-to-your-app"></a>Далее: добавьте в приложение код навигации

В следующей статье [Реализация базовой навигации](navigate-between-two-pages.md) показан код, необходимый для использования элемента управления Frame, чтобы реализовать базовую навигацию между двумя страницами в приложении. 