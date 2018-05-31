---
author: mijacobs
Description: In a Universal Windows Platform (UWP) app, command elements are the interactive UI elements that enable the user to perform actions, such as sending an email, deleting an item, or submitting a form.
title: Основы проектирования команд для приложений универсальной платформы Windows (UWP)
ms.assetid: 1DB48285-07B7-4952-80EF-02B57D4469F2
label: Command design basics
template: detail.hbs
op-migration-status: ready
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 07b9ce7b5a57f6dc1ba202ed57e8b2d4d93e583f
ms.sourcegitcommit: 0ab8f6fac53a6811f977ddc24de039c46c9db0ad
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/15/2018
ms.locfileid: "1654393"
---
#  <a name="command-design-basics-for-uwp-apps"></a>Основы проектирования команд в приложениях UWP

В приложении универсальной платформы Windows (UWP) *командные элементы* — это интерактивные элементы пользовательского интерфейса, которые позволяют пользователям выполнять действия, например отправлять электронное сообщение, удалять элемент или отправлять форму. 

В этой статье описаны распространенные командные элементы, взаимодействия, которые они поддерживают, и поверхности для команд для их размещения.

![командные элементы в приложении "Карты"](images/maps.png)

Примеры командных элементов в приложение "Карты" см. выше.

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

<div class="mx-responsive-img">
<table>
<thead>
<tr class="header">
<th align="left">Категория</th>
<th align="left">Элементы</th>
<th align="left">Взаимодействие</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><b>Кнопки</b><br/><br/>
    <img src="../controls-and-patterns/images/controls/button.png" alt="button" /></td>
<td align="left"><a href="../controls-and-patterns/buttons.md">Кнопка</a></td>
<td align="left">Запускает немедленное действие. Например отправку электронного сообщения, подтверждение действия в диалоговом окне, отправку данных формы.</td>
</tr>
<tr class="even">
<td align="left">Списки<br/><br/>
    <img src="../controls-and-patterns/images/controls/combo-box-open.png" alt="drop down list" /></td>
<td align="left"><a href="../controls-and-patterns/lists.md">раскрывающийся список, список, представление списка и представление сетки</a></td>
<td align="left">Представляет элементы в интерактивном списке или сетке. Обычно используется для отображения большого числа параметров или элементов.</td>
</tr>
<tr class="odd">
<td align="left">Элементы управления выделением<br/><br/>
    <img src="../controls-and-patterns/images/controls/radio-button.png" alt="radio button" /></td>
<td align="left"><a href="../controls-and-patterns/checkbox.md">флажок</a>, <a href="../controls-and-patterns/radio-button.md">переключатель</a>, <a href="../controls-and-patterns/toggles.md">тумблер</a></td>
<td align="left">Позволяет пользователю выбирать различные варианты, например при прохождении опроса или настройке параметров приложения.</td>
</tr>
<tr class="even">
<td align="left">Управляющие элементы выбора даты и времени<br/><br/>
    <img src="../controls-and-patterns/images/controls/calendar-date-picker-open.png" alt="date picker" /></td>
<td align="left"><a href="../controls-and-patterns/date-and-time.md">выбор даты календаря, представление календаря, выбор даты, выбор времени</a></td>
<td align="left">Позволяет пользователям просматривать и изменять сведения о дате и времени, например создавать событие или устанавливать будильник.</td>
</tr>
<tr class="odd">
<td align="left">Прогнозируемый ввод текста<br/><br/>
    <img src="../controls-and-patterns/images/controls/auto-suggest-box.png" alt="autosuggest box" /></td>
<td align="left"><a href="../controls-and-patterns/auto-suggest-box.md">Поле автозаполнения</a></td>
<td align="left">Ввод данных или выполнение запросов с предоставлением вариантов во время набора.</td>
</tr>
</tbody>
</table>
</div>

Полный список см. в разделе [Элементы управления и пользовательского интерфейса](https://dev.windows.com/design/controls-patterns)

##  <a name="place-commands-on-the-right-surface"></a>Размещение команд на нужной поверхности
Командные элементы можно размещать на ряде поверхностей приложения, в том числе на холсте приложения или в специальных командных элементах, таких как панели команд, меню, диалоговые окна и всплывающие элементы.

Всегда старайтесь сделать так, чтобы пользователи могли напрямую управлять содержимым на холсте приложения, не перегружая его обособленными командами. Например, разрешите пользователям изменять расположение списков путем перетаскивания и удаления элементов списка, а не с помощью кнопок "вверх" и "вниз".
  
В противном случае разместите командные элементы на поверхности команд приложения, если пользователи не могут управлять содержимым напрямую:

<div class="mx-responsive-img">
<table class="uwpd-top-aligned-table">

<tr class="header">
<th align="left">Поверхность</th>
<th align="left">Описание</th>
<th align="left">Пример.</th>
</tr>

<tr class="odd">
<td align="left" style="vertical-align: top">Холст приложения (область содержимого)
<p><img src="images/content-area.png" alt="The content area of an app" /></p></td>

<td align="left" style="vertical-align: top;">Если команда постоянно необходима пользователю для выполнения основных сценариев, поместите ее на холст. Так как вы можете разместить команды возле объектов, на которые они влияют (или на них), если разместить команды на холсте, их будет просто понять и использовать.
<p>Но выбирайте команды, которые помещаете на холст, тщательно. Слишком много команд на холсте приложения занимают ценное место на экране и могут перегружать восприятие интерфейса пользователем. Если команда будет использоваться не часто, ее можно поместить на другую поверхность для команд.</p> 
</td><td>
Поле автозаполнения на холсте приложения "Карты".
<br></br>
  <img src="images/maps-canvas.png" alt="autosuggest box on Maps app canvas"/>
</td>
</tr>

<tr class="even">
<td align="left" style="vertical-align: top;"><a href="../controls-and-patterns/app-bars.md">Панель команд</a>
<p><img src="../controls-and-patterns/images/controls_appbar_icons.png" alt="Example of a command bar with icons" /></p></td>
<td align="left" style="vertical-align: top;"> Панели команд помогают организовать команды и облегчают к ним доступ. Панели команд можно разместить в верхней части экрана, в нижней части экрана, или как в верхней, так и в нижней части экрана. 
</td>
<td>
Панель команд в верхней части приложения "Карты".
<br></br>
<img src="images/maps-commandbar.png" alt="command bar in Maps app"/>
</td>
</tr>

<tr class="odd">
<td align="left" style="vertical-align: top;"><a href="../controls-and-patterns/menus.md">Меню и контекстные меню</a>
<p><img src="images/controls-contextmenu-singlepane.png" alt="Example of a single-pane context menu" /></p></td>
<td align="left" style="vertical-align: top;">Иногда эффективнее группировать несколько команд в меню команд для экономии пространства. В меню и контекстных меню отображается список команд или параметров, когда пользователь запрашивает их.
<p>Контекстные меню могут содержать ярлыки на часто используемые действия и предоставлять доступ ко второстепенным командам, которые актуальны только в определенном контексте, например командам буфера обмена или пользовательским командам. Вызов контекстных меню обычно осуществляется щелчком правой кнопкой мыши.</p>
</td><td>
Открытие контекстного меню при щелчке правой кнопкой мыши в приложении "Карты".
<br></br>
  <img src="images/maps-contextmenu.png" alt="context menu in Maps app"/>
</td>
</tr>
</table>
</div>

## <a name="provide-feedback-for-interactions"></a>Предоставление обратной связи по взаимодействиям

Раздел отзывов используется для передачи результатов выполнения команд и позволяет пользователям понять, что они делают в настоящий момент и что они могут сделать потом. В идеальном случае раздел отзывов должен быть интегрирован естественным образом в пользовательский интерфейс, чтобы пользователям не приходилось прерывать работу с приложением или выполнять дополнительные действия, только если это совершенно необходимо 

Ниже перечислены некоторые способы предоставления отзывов в приложении.

<div class="mx-responsive-img">
<table class="uwpd-top-aligned-table">

<tr class="header">
<th align="left">Поверхность</th>
<th align="left">Описание</th>
</tr>

<tr class="odd">
<td align="left" style="vertical-align: top;"> <a href="../controls-and-patterns/app-bars.md">Панель команд</a>
<p><img src="../controls-and-patterns/images/controls_appbar_icons.png" alt="Example of a command bar with icons" /></p>
</td>
<td align="left" style="vertical-align: top;"> Область содержимого панели команд— это интуитивно понятная область для обмена с пользователями данными о состоянии, если им требуется увидеть отзыв.
<p>
  <img src="images/commandbar_anatomy.png" alt="Command bar content area for feedback"/>
  </p>
</td>
</tr>

<tr class="even">
<td align="left" style="vertical-align: top;"><a href="../controls-and-patterns/dialogs.md">Всплывающий элемент</a>
<p><img src="images/controls-flyout-default-200.png" alt="Image of default flyout" /></p></td>
<td align="left" style="vertical-align: top;">
Облегченное всплывающее контекстно-зависимое окно, которое можно закрыть, если щелкнуть участок экрана за его пределами.
<p>
  <img src="../controls-and-patterns/images/controls/flyout.png" alt="Flyout above button"/>
  </p>
</td>
</tr>

<tr class="odd">
<td align="left" style="vertical-align: top;"><a href="../controls-and-patterns/dialogs.md">Диалоговые элементы управления</a>
<p><img src="images/controls-dialog-twobutton-200.png" alt="Example of a simple two-button dialog" /></p></td>
<td align="left" style="vertical-align: top;">Диалоговые окна — это модальные наложения пользовательского интерфейса, которые предоставляют контекстную информацию о приложении. В большинстве случаев диалоговые окна блокируют взаимодействие с окном приложения, пока не будут закрыты явным образом, и зачастую требуют от пользователя выполнить некое действие.
<p>Диалоговые окна могут отвлекать и должны использоваться только в определенных ситуациях. Подробнее см. в разделе [Когда подтверждать или отменять действия](#when-to-confirm-or-undo-actions).</p>
<p>
  <img src="../controls-and-patterns/images/dialogs/dialog_RS2_delete_file.png" alt="dialog delete file"/></p>
</td>
</tr>

</table>
</div>

> [!TIP]
> Обратите внимание, сколько диалоговых окон подтверждения использует ваше приложение; они могут оказаться очень полезными, если пользователь совершил ошибку, но они также выступают помехой, когда пользователь пытается выполнить действие намеренно.

### <a name="when-to-confirm-or-undo-actions"></a>Когда подтверждать или отменять действия

Независимо от того, насколько правильно разработан пользовательский интерфейс и насколько осторожно действует пользователь, в определенный момент все пользователи выполняют действие, которое они не хотели бы выполнять. Ваше приложение может помочь в этой ситуации, требуя, чтобы пользователь подтвердил действие, или предоставляя возможность отменить последние действия.

-   Для имеющих серьезные последствия действий, которые нельзя отменить, рекомендуется использовать диалоговое окно подтверждения. Примеры таких действий:
    -   перезапись файла;
    -   закрытие файла без сохранения;
    -   подтверждение окончательного удаления файла или данных;
    -   покупка (если пользователь не отказался от обязательного подтверждения);
    -   отправка формы, например регистрация.
-   Для действий, которые можно отменить, обычно достаточно простой команды "Отменить". Примеры таких действий:
    -   удаление файла;
    -   удаление электронного письма (не окончательное);
    -   изменение содержимого или редактирование текста;
    -   Переименование файла

##  <a name="optimize-for-specific-input-types"></a>Оптимизация для определенных типов ввода

Подробнее об оптимизации взаимодействия с пользователем на основе определенного типа ввода или устройства см. в разделе [Азбука взаимодействия](../input/index.md).