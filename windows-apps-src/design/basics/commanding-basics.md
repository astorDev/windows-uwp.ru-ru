---
Description: В универсальном приложении для Windows (UWP) командные элементы — это интерактивные элементы пользовательского интерфейса, которые позволяют пользователю выполнять действия, например отправлять электронное сообщение, удалять элемент или отправлять форму.
title: Основы проектирования команд на платформе универсальных приложений для Windows (UWP)
ms.assetid: 1DB48285-07B7-4952-80EF-02B57D4469F2
label: Command design basics
template: detail.hbs
op-migration-status: ready
ms.author: mijacobs
ms.date: 11/01/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: ac2bd55d1cea25359c3c609148c7098532d76c46
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57654059"
---
# <a name="command-design-basics-for-uwp-apps"></a>Основы проектирования команд в приложениях UWP

В приложении универсальной платформы Windows (UWP) *команды элементы* являются интерактивных элементов пользовательского интерфейса, которые пользователи могут выполнять действия, такие как отправка сообщения электронной почты, удаление элемента или отправка формы. *Команда интерфейсы* состоят из общих элементов команды, команда поверхности, в которых они размещены, взаимодействий, они поддерживают и опыт, они предоставляют.

## <a name="provide-the-best-command-experience"></a>Обеспечивают лучшее взаимодействие команды

Наиболее важным аспектом командный интерфейс является выделяемый позволить пользователю выполнить. При планировании функциональных возможностей приложения, рассмотрим шаги, необходимые для выполнения этих задач и пользовательских интерфейсов, которые вы хотите включить. После завершения первоначальный черновой вариант из этих функций, а затем можно принять решение о средствах и взаимодействий для их реализации.

Ниже приведены некоторые общего опыта при работе команды.

- Отправка или передача информации
- Выбор параметров и пунктов
- Поиск и фильтрация содержимого
- Открытие, сохранение и удаление файлов
- Изменение или создание содержимого

Будьте изобретательны дизайна своими впечатлениями команды. Выберите, какие устройства ввода приложения поддерживает и как приложение реагирует на каждое устройство. Благодаря поддержке самый широкий спектр возможностей и настройки внесенные приложения как Приемлемая, переносимость и уровень доступности, как возможные (см. в разделе [команды разработки для приложений универсальной платформы Windows (UWP)](../controls-and-patterns/commanding.md) для получения дополнительных сведений).



<!--
When designing a command interface, the most important decision is choosing what a user can do. To plan the right type of interactions, focus on your app - consider the user experiences you want to enable, and what steps users will need to take. Once you decide what you want users to accomplish, then you can provide them the tools to do so.
-->

## <a name="choose-the-right-command-elements"></a>Выберите элементы команд

Использование нужные элементы в интерфейсе командной делает разницу между сложных, толку приложения и интуитивно понятный и простой в использовании приложения. Полный набор командных элементов доступны в универсальной платформы Windows (UWP). Ниже приведен список некоторых из наиболее распространенных элементов команды универсальной платформы Windows.

:::row:::
    :::column:::
        ![button image](images/commanding/thumbnail-button.svg)
    :::column-end:::
    :::column span="2":::
        <b>Buttons</b>

        <a href="../controls-and-patterns/buttons.md" style="text-decoration:none">Buttons</a> trigger an immediate action. Examples include sending an email, submitting form data, or confirming an action in a dialog.
:::row-end:::

:::row:::
    :::column:::
        ![list image](images/commanding/thumbnail-list.svg)
    :::column-end:::
    :::column span="2":::
        <b>Lists</b>

        <a href="../controls-and-patterns/lists.md" style="text-decoration:none">Lists</a> present items in a interactive list or a grid. Usually used for many options or display items. Examples include drop-down list, list box, list view and grid view.
:::row-end:::

:::row:::
    :::column:::
        ![selection control image](images/commanding/thumbnail-selection.svg)
    :::column-end:::
    :::column span="2":::
        <b>Selection controls</b>

        Lets users choose from a few options, such as when completing a survey or configuring app settings. Examples include <a href="../controls-and-patterns/checkbox.md">check box</a>, <a href="../controls-and-patterns/radio-button.md">radio button</a>, and <a href="../controls-and-patterns/toggles.md">toggle switch</a>.
:::row-end:::

:::row:::
    :::column:::
        ![Calendar  image](images/commanding/thumbnail-calendar.svg)
    :::column-end:::
    :::column span="2":::
        <b>Calendar, date and time pickers</b>

        <a href="../controls-and-patterns/date-and-time.md">Calendar, date and time pickers</a> enable users to view and modify date and time info, such as when creating an event or setting an alarm. Examples include calendar date picker, calendar view, date picker, time picker.
:::row-end:::

:::row:::
    :::column:::
        ![Predictive text entry image](images/commanding/thumbnail-autosuggest.svg)
    :::column-end:::
    :::column span="2":::
        <b>Predictive text entry</b>

        Provides suggestions as users type, such as when entering data or performing queries. Examples include <a href="../controls-and-patterns/auto-suggest-box.md">auto-suggest box</a>.<br>
:::row-end:::

Полный список см. в разделе [Элементы управления и пользовательского интерфейса](../controls-and-patterns/index.md)

## <a name="place-commands-on-the-right-surface"></a>Размещение команд на нужной поверхности

Можно поместить элементы команды на число устройств Surface в вашем приложении, включая холсте приложения или контейнеры специальные команды, такие как панели команд, всплывающего меню панели команд, меню или диалогового окна.

Всегда попытаться разрешить пользователям, которые работают непосредственно с содержимым, а не через команды, действующие на содержимое, например перетаскивание для изменения порядка элементов списка, а не вверх и вниз кнопки команд. 

Тем не менее это может оказаться невозможным с определенных устройств ввода, или при размещении прав определенных пользователей и настроек. В этом случае укажите столько команд читаемости максимально и поместить эти элементы команды в рабочей области команды в приложении.

Ниже приведен список некоторых наиболее распространенных поверхностей для команд.

:::row:::
    :::column:::
        ![app canvas image](images/commanding/thumbnail-canvas.svg)
    :::column-end:::
    :::column span="2":::
        <b>App canvas (content area)</b>

        If a command is constantly needed for users to complete core scenarios, put it on the canvas. Because you can put commands near (or on) the objects they affect, putting commands on the canvas makes them easy and obvious to use. However, choose the commands you put on the canvas carefully. Too many commands on the app canvas take up valuable screen space and can overwhelm the user. If the command won't be frequently used, consider putting it in another command surface.
:::row-end:::

:::row:::
    :::column:::
        ![commandbar image](images/commanding/thumbnail-commandbar.svg)
    :::column-end:::
    :::column span="2":::
        <b>Command bars and menu bars</b>

        <a href="../controls-and-patterns/app-bars.md">Command bars</a> help organize commands and make them easy to access. Command bars can be placed at the top of the screen, at the bottom of the screen, or at both the top and bottom of the screen (a <a href="../controls-and-patterns/menus.md#create-a-menu-bar">MenuBar</a> can also be used when the functionality in your app is too complex for a command bar).
:::row-end:::

:::row:::
    :::column:::
        ![context menu image](images/commanding/thumbnail-contextmenu.svg)
    :::column-end:::
    :::column span="2":::
        <b>Menus and context menus</b>

        <p>Menus and context menus save space by organizing commands and hiding them until the user needs them. Users typically access a menu or context menu by clicking a button or right-clicking a control.</p> 

        <p>The <a href="../controls-and-patterns/command-bar-flyout.md">command bar flyout </a> is a type of contextual menu that combines the benefits of a command bar and a context menu into a single control. It can provide shortcuts to commonly-used actions and provide access to secondary commands that are only relevant in certain contexts, such as clipboard or custom commands.</p>

        <p>UWP also provides a set of traditional menus and context menus; for more info, see the <a href="../controls-and-patterns/menus.md">menus and context menus overview</a>.</p>
:::row-end:::

## <a name="provide-command-feedback"></a>Команда отзыв 

Команда связь пользователям обнаружено взаимодействия или команды, как команда интерпретировать и обрабатывать, что ли команда выполнена успешно или нет. Это поможет пользователям понять, что они это делали, и что они могут сделать. В идеальном случае раздел отзывов должен быть интегрирован естественным образом в пользовательский интерфейс, чтобы пользователям не приходилось прерывать работу с приложением или выполнять дополнительные действия, только если это совершенно необходимо

> [!NOTE]
> Оставить отзыв, только при необходимости и только в том случае, если он не доступен в другом месте. Оставьте приложение пользовательского интерфейса четком и лаконичном представлении, если только вы добавляете значение.

Ниже перечислены некоторые способы предоставления отзывов в приложении.

:::row:::
    :::column:::
        ![commandbar content area image](images/commanding/thumbnail-commandbar2.svg)
    :::column-end:::
    :::column span="2":::
        <b>Command bar</b>

        The content area of the <a href="../controls-and-patterns/app-bars.md">command bar</a> is an intuitive place to communicate status to users if they'd like to see feedback.
:::row-end:::

:::row:::
    :::column:::
        ![Flyout image](images/commanding/thumbnail-flyout.svg)
    :::column-end:::
    :::column span="2":::
        <b>Flyouts</b>

       <a href="../controls-and-patterns/dialogs-and-flyouts/index.md">Всплывающие окна</a> являются упрощенных контекстные всплывающих окон, которые можно отклонить, коснувшись или щелкнув где-либо за пределами всплывающего меню.
:::row-end:::

:::row:::
    :::column:::
        ![Dialog image](images/commanding/thumbnail-dialog.svg)
    :::column-end:::
    :::column span="2":::
        <b>Dialog controls</b>

        <a href="../controls-and-patterns/dialogs-and-flyouts/index.md">Dialog controls</a> are modal UI overlays that provide contextual app information. In most cases, dialogs block interactions with the app window until being explicitly dismissed, and often request some kind of action from the user. Dialogs can be disruptive and should only be used in certain situations. For more info, see the [When to confirm or undo actions](#when-to-confirm-or-undo-actions) section.
    :::column-end:::
:::row-end:::

> [!TIP]
> Обратите внимание, сколько диалоговых окон подтверждения использует ваше приложение; они могут оказаться очень полезными, если пользователь совершил ошибку, но они также выступают помехой, когда пользователь пытается выполнить действие намеренно.

### <a name="when-to-confirm-or-undo-actions"></a>Когда подтверждать или отменять действия

Независимо от того, как хорошо спроектированной является пользовательского интерфейса приложения, всем пользователям выполнять действие, которое они неплохо, если они еще не. Приложение может помочь в таких ситуациях, требуя подтверждения действия, либо решение, позволяющее последних действий.

:::row:::
    :::column:::
        ![do image](images/do.svg)

        For actions that can't be undone and have major consequences, we recommend using a confirmation dialog. Examples of such actions include:
        -   Overwriting a file
        -   Not saving a file before closing
        -   Confirming permanent deletion of a file or data
        -   Making a purchase (unless the user opts out of requiring a confirmation)
        -   Submitting a form, such as signing up for something
    :::column-end:::
    :::column:::
        ![do image](images/do.svg)

        For actions that can be undone, offering a simple undo command is usually enough. Examples of such actions include:
        -   Deleting a file
        -   Deleting an email (not permanently)
        -   Modifying content or editing text
        -   Renaming a file
:::row-end:::

##  <a name="optimize-for-specific-input-types"></a>Оптимизация для определенных типов ввода

Подробнее об оптимизации взаимодействия с пользователем на основе определенного типа ввода или устройства см. в разделе [Азбука взаимодействия](../input/index.md).

