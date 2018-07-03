---
author: mijacobs
Description: In a Universal Windows Platform (UWP) app, command elements are the interactive UI elements that enable the user to perform actions, such as sending an email, deleting an item, or submitting a form.
title: Основы проектирования команд для приложений универсальной платформы Windows (UWP)
ms.assetid: 1DB48285-07B7-4952-80EF-02B57D4469F2
label: Command design basics
template: detail.hbs
op-migration-status: ready
ms.author: mijacobs
ms.date: 05/04/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 09f775ad0ba596379b6d3ddf158285849520111f
ms.sourcegitcommit: 517c83baffd344d4c705bc644d7c6d2b1a4c7e1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "1842571"
---
#  <a name="command-design-basics-for-uwp-apps"></a>Основы проектирования команд в приложениях UWP

В приложении универсальной платформы Windows (UWP) *командные элементы* — это интерактивные элементы пользовательского интерфейса, которые позволяют пользователям выполнять действия, например отправлять электронное сообщение, удалять элемент или отправлять форму. В этой статье описаны распространенные командные элементы, взаимодействия, которые они поддерживают, и поверхности для команд для их размещения.

## <a name="provide-the-right-type-of-interactions"></a>Укажите правильный тип взаимодействий

При проектировании командного интерфейса самое важное решение — это выбрать, что пользователи смогут делать. Чтобы внедрить подходящие типы взаимодействий, определите основное назначение своего приложения— подумайте о том, какие действия для пользователей вы хотите добавить, а также, какие шаги для выполнения этих действий пользователям придется выполнить. После того как вы решите, чем должны заниматься пользователи, предоставьте им необходимые для этого инструменты.

Некоторые взаимодействия, которые можно добавить в приложение:

- Отправка или передача информации 
- Выбор параметров и пунктов
- Поиск и фильтрация содержимого
- Открытие, сохранение и удаление файлов
- Изменение или создание содержимого

## <a name="use-the-right-command-element-for-the-interaction"></a>Используйте для взаимодействия правильный элемент команды

Использование подходящих элементов для взаимодействий с помощью команд позволяет сделать приложение интуитивно понятным. В противном случае оно будет сложным или запутанным. Универсальная платформа Windows (UWP) предоставляет широкий набор командных элементов, которые можно использовать в приложении. Вот список некоторых наиболее распространенных элементов управления и краткое описание взаимодействий, которые они обеспечивают.

:::row::: :::column::: ![изображение кнопки](images/commanding/thumbnail-button.svg) :::column-end::: :::column span="2"::: <b>Кнопки</b>

        <a href="../controls-and-patterns/buttons.md" style="text-decoration:none">Buttons</a> trigger an immediate action. Examples include sending an email, submitting form data, or confirming an action in a dialog.
:::row-end:::

:::row::: :::column::: ![изображение списка](images/commanding/thumbnail-list.svg) :::column-end::: :::column span="2"::: <b>Списки</b>

        <a href="../controls-and-patterns/lists.md" style="text-decoration:none">Lists</a> present items in a interactive list or a grid. Usually used for many options or display items. Examples include drop-down list, list box, list view and grid view.
:::row-end:::

:::row::: :::column::: ![изображения элемента для выбора](images/commanding/thumbnail-selection.svg) :::column-end::: :::column span="2"::: <b>Элементы управления для выбора</b>

        Lets users choose from a few options, such as when completing a survey or configuring app settings. Examples include <a href="../controls-and-patterns/checkbox.md">check box</a>, <a href="../controls-and-patterns/radio-button.md">radio button</a>, and <a href="../controls-and-patterns/toggles.md">toggle switch</a>.
:::row-end:::

:::row::: :::column::: ![Изображение календаря](images/commanding/thumbnail-calendar.svg) :::column-end::: :::column span="2"::: <b>Календарь, средства выбора даты и времени</b>

        <a href="../controls-and-patterns/date-and-time.md">Calendar, date and time pickers</a> enable users to view and modify date and time info, such as when creating an event or setting an alarm. Examples include calendar date picker, calendar view, date picker, time picker.
:::row-end:::

:::row::: :::column::: ![Изображение прогнозируемого ввода текста](images/commanding/thumbnail-autosuggest.svg) :::column-end::: :::column span="2"::: <b>Прогнозируемый ввод текста</b>

        Provides suggestions as users type, such as when entering data or performing queries. Examples include <a href="../controls-and-patterns/auto-suggest-box.md">auto-suggest box</a>.<br>
:::row-end:::

Полный список см. в разделе [Элементы управления и пользовательского интерфейса](../controls-and-patterns/index.md)

##  <a name="place-commands-on-the-right-surface"></a>Размещение команд на нужной поверхности
Командные элементы можно размещать на ряде поверхностей приложения, в том числе на холсте приложения или в специальных командных элементах, таких как панели команд, меню, диалоговые окна и всплывающие элементы.

Всегда старайтесь сделать так, чтобы пользователи могли напрямую управлять содержимым на холсте приложения, не перегружая его обособленными командами. Например, разрешите пользователям изменять расположение списков путем перетаскивания и удаления элементов списка, а не с помощью кнопок "вверх" и "вниз".

В противном случае разместите командные элементы на поверхности команд приложения, если пользователи не могут управлять содержимым напрямую. Ниже приведен список некоторых наиболее распространенных поверхностей для команд.

:::row::: :::column::: ![изображение холста приложения](images/commanding/thumbnail-canvas.svg) :::column-end::: :::column span="2"::: <b>Холст приложения (область содержимого)</b>

        If a command is constantly needed for users to complete core scenarios, put it on the canvas. Because you can put commands near (or on) the objects they affect, putting commands on the canvas makes them easy and obvious to use. However, choose the commands you put on the canvas carefully. Too many commands on the app canvas take up valuable screen space and can overwhelm the user. If the command won't be frequently used, consider putting it in another command surface.
:::row-end:::

:::row::: :::column::: ![изображение панели команд](images/commanding/thumbnail-commandbar.svg) :::column-end::: :::column span="2"::: <b>Панели команд</b>

        <a href="../controls-and-patterns/app-bars.md">Command bars</a> help organize commands and make them easy to access. Command bars can be placed at the top of the screen, at the bottom of the screen, or at both the top and bottom of the screen.
:::row-end:::

:::row::: :::column::: ![изображение контекстного меню](images/commanding/thumbnail-contextmenu.svg) :::column-end::: :::column span="2"::: <b>Меню и контекстные меню</b>

        Sometimes it is more efficient to group multiple commands into a command menu to save space. <a href="../controls-and-patterns/menus.md">Menus and context menus</a> display a list of commands or options when the user requests them. Context menus can provide shortcuts to commonly-used actions and provide access to secondary commands that are only relevant in certain contexts, such as clipboard or custom commands. Context menus are usually prompted by a user right-clicking.
:::row-end:::

## <a name="provide-feedback-for-interactions"></a>Предоставление обратной связи по взаимодействиям

Раздел отзывов используется для передачи результатов выполнения команд и позволяет пользователям понять, что они делают в настоящий момент и что они могут сделать потом. В идеальном случае раздел отзывов должен быть интегрирован естественным образом в пользовательский интерфейс, чтобы пользователям не приходилось прерывать работу с приложением или выполнять дополнительные действия, только если это совершенно необходимо 

Ниже перечислены некоторые способы предоставления отзывов в приложении.

:::row::: :::column::: ![изображение области содержимого панели команд](images/commanding/thumbnail-commandbar2.svg) :::column-end::: :::column span="2"::: <b>Панель команд</b>

        The content area of the <a href="../controls-and-patterns/app-bars.md">command bar</a> is an intuitive place to communicate status to users if they'd like to see feedback.
:::row-end:::

:::row::: :::column::: ![изображение всплывающего элемента](images/commanding/thumbnail-flyout.svg) :::column-end::: :::column span="2"::: <b>Всплывающие элементы</b>

       <a href="../controls-and-patterns/dialogs.md">Flyouts</a> are lightweight contextual popups that can be dismissed by tapping or clicking somewhere outside the flyout.
:::row-end:::

:::row::: :::column::: ![Изображение диалогового окна](images/commanding/thumbnail-dialog.svg) :::column-end::: :::column span="2"::: <b>Элементы управления диалогового окна</b>

        <a href="../controls-and-patterns/dialogs.md">Dialog controls</a> are modal UI overlays that provide contextual app information. In most cases, dialogs block interactions with the app window until being explicitly dismissed, and often request some kind of action from the user. Dialogs can be disruptive and should only be used in certain situations. For more info, see the [When to confirm or undo actions](#when-to-confirm-or-undo-actions) section.
    :::column-end:::
:::row-end:::

> [!TIP]
> Обратите внимание, сколько диалоговых окон подтверждения использует ваше приложение; они могут оказаться очень полезными, если пользователь совершил ошибку, но они также выступают помехой, когда пользователь пытается выполнить действие намеренно.

### <a name="when-to-confirm-or-undo-actions"></a>Когда подтверждать или отменять действия

Независимо от того, насколько правильно разработан пользовательский интерфейс и насколько осторожно действует пользователь, в определенный момент все пользователи выполняют действие, которое они не хотели бы выполнять. Ваше приложение может помочь в этой ситуации, требуя, чтобы пользователь подтвердил действие, или предоставляя возможность отменить последние действия.

:::row::: :::column::: ![изображение действия](images/do.svg)

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

