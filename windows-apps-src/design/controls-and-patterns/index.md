---
description: Здесь вы найдете инструкции по проектированию и программированию для добавления элементов управления и шаблонов в приложение для Windows. Свыше 45 функциональных элементов управления для использования с приложением.
title: Элементы управления и шаблоны Windows — разработка приложений для Windows
keywords: элементы управления uwp, пользовательский интерфейс, элементы управления приложения, элементы управления windows
label: Controls & patterns
template: detail.hbs
ms.date: 03/23/2020
ms.topic: article
ms.assetid: ce2e611c-c419-4a14-9095-b88ac711d1b8
ms.localizationpriority: medium
ms.openlocfilehash: 82e6d91b5a4147237cd04b46dc5b69b8151fece9
ms.sourcegitcommit: 6dd6d61c912daab2cc4defe5ba0cf717339f7765
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2020
ms.locfileid: "84978386"
---
# <a name="controls-for-windows-apps"></a>Элементы управления приложений для Windows

![Элементы управления](../images/controls-2x.png)

В разработке приложений для Windows <i>элемент управления</i> — это элемент пользовательского интерфейса, отображающий содержимое или обеспечивающий взаимодействие. Элементы управления — это основа пользовательского интерфейса. <i>Шаблон</i> — это макет для объединения нескольких элементов управления, позволяющий получить что-то новое.

Мы предоставляем более 45 элементов управления — от простых кнопок до мощных элементов управления данными, таких как представление сетки.  Эти элементы управления являются частью системы Fluent Design и помогают создавать удобный и масштабируемый пользовательский интерфейс, который прекрасно выглядит на всех устройствах и экранах разного размера.

В статьях в этом разделе представлены инструкции по проектированию и программированию для добавления элементов управления и шаблонов в приложение для Windows.

## <a name="intro"></a>Вступление

Общие инструкции и примеры кода для добавления и настройки стиля элементов управления на языках XAML и C#.

:::row:::
    :::column:::
      <p><b><a href="controls-and-events-intro.md">Добавление элементов управления и обработка событий</a></b> <br/>
Три основных действия для добавления элементов управления в приложение: добавление элемента управления в пользовательский интерфейс приложения, установка свойств элемента управления и добавление кода в обработчики событий элемента управления, чтобы он начал функционировать.</p>
    :::column-end:::
    :::column:::
      <p><b><a href="xaml-styles.md">Настройка стиля элементов управления</a></b> <br/>
Платформа XAML предоставляет множество способов настройки внешнего вида приложений. С помощью стилей вы можете настраивать свойства элементов управления и многократно использовать эти параметры, чтобы придать единообразный внешний вид нескольким элементам управления.</p>
    :::column-end:::
:::row-end:::

## <a name="get-the-windows-ui-library"></a>Получение библиотеки пользовательского интерфейса Windows

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Некоторые элементы управления доступны только в библиотеке пользовательского интерфейса Windows (WinUI). Это пакет NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса. Чтобы получить ее, ознакомьтесь с [общими сведениями и инструкциями по установке библиотеки пользовательского интерфейса Windows](/uwp/toolkits/winui/).<br/>Начиная с версии WinUI 2.2 стиль по умолчанию для многих элементов управления обновлен для использования скругленных углов. Дополнительные сведения см. в разделе о [радиусе угла](/windows/uwp/design/style/rounded-corner). |

## <a name="alphabetical-index"></a>Алфавитный указатель

Подробные сведения о конкретных элементах управления и шаблонах. (Список, отсортированный по функциям, см. в разделе [Элементы управления по функциям](controls-by-function.md).)

:::row:::
    :::column:::

- Анимированный визуальный проигрыватель (см. сведения о [Lottie](/windows/communitytoolkit/animations/lottie)) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Поле автозаполнения](auto-suggest-box.md)
- [Кнопка](buttons.md)
- [Выбор даты в календаре](calendar-date-picker.md)
- [Представление календаря](calendar-view.md)
- [Флажок](checkbox.md)
- [Палитра](color-picker.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Поле со списком](combo-box.md)
- [Панель команд](app-bars.md)
- [Всплывающее меню панели команд](command-bar-flyout.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Карточка контакта](contact-card.md)
- [Диалоговое окно содержимого](dialogs-and-flyouts/dialogs.md)
- [Ссылка на содержимое](content-links.md)
- [Контекстное меню](menus.md)
- [Выбор даты](date-picker.md)
- [Диалоговые окна и всплывающие элементы](dialogs-and-flyouts/index.md)
- [Кнопка раскрывающегося списка](buttons.md#create-a-drop-down-button) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Представление пролистывания](flipview.md)
- [Flyout](dialogs-and-flyouts/flyouts.md)
- [Формы](forms.md) (шаблон)
- [Представление в виде сетки](listview-and-gridview.md)
- [Гиперссылка](hyperlinks.md)
- [Кнопка с гиперссылкой](hyperlinks.md#create-a-hyperlinkbutton)
- [Изображения и кисти изображений](images-imagebrushes.md)
- [Элементы управления рукописным вводом](inking-controls.md)
- [Представление списка](listview-and-gridview.md)
- [Элемент управления картой](../../maps-and-location/controls-map.md)
- [Основные и подробные данные](master-details.md) (шаблон)
- [Воспроизведение мультимедиа](media-playback.md)
- [Строка меню](menus.md#create-a-menu-bar) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Всплывающее меню](menus.md)
- [Представление навигации](navigationview.md) ![Логотип WinUI](images/winui-logo-16x16.png)

    :::column-end:::
    :::column:::

- [Поле ввода номера](number-box.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Представление параллакса](..\motion\parallax.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Поле ввода пароля](password-box.md)
- [Изображение пользователя](person-picture.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Сводка](pivot.md)
- [Индикатор выполнения](progress-controls.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Кольцевой индикатор выполнения](progress-controls.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Переключатель](radio-button.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Элемент управления "Оценка"](rating.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Кнопка повтора](buttons.md#create-a-repeat-button)
- [Поле форматируемого текста](rich-edit-box.md)
- [Блок форматированного текста](rich-text-block.md)
- [Средство прокрутки](scroll-controls.md)
- [Поиск](search.md) (шаблон)
- [Контекстное масштабирование](semantic-zoom.md)
- [Фигуры](shapes.md)
- [Ползунок](slider.md)
- [Кнопка разделения](buttons.md#create-a-split-button) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Комбинированный режим](split-view.md)
- [Элемент управления для сдвига](swipe.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Представление вкладки](tab-view.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Совет по обучению](dialogs-and-flyouts/teaching-tip.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Блок текста](text-block.md)
- [Текстовое поле](text-box.md)
- [Выбор времени](time-picker.md)
- [Тумблер](toggles.md)
- [Выключатель](buttons.md)
- [Выключатель с разделением](buttons.md#create-a-toggle-split-button)
- [Подсказки](tooltips.md)
- [Представление дерева](tree-view.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Представление с двумя панелями](two-pane-view.md) ![Логотип WinUI](images/winui-logo-16x16.png)
- [Представление веб-страницы](web-view.md)

    :::column-end:::
:::row-end:::




## <a name="xaml-controls-gallery"></a>XAML Controls Gallery

Скачайте приложение _XAML Controls Gallery_ с сайта Microsoft Store, чтобы просмотреть эти элементы управления и систему Fluent Design в действии. Данное приложение является интерактивным помощником для этого веб-сайта. После его установки вы можете использовать ссылки на отдельные страницы элементов управления для запуска приложения, чтобы увидеть его в действии.

<a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a>

<a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a>

<img src="images/xaml-controls-gallery.png" alt="XAML Controls Gallery screen" />

## <a name="additional-controls"></a>Дополнительные элементы управления

Дополнительные элементы управления для разработки для Windows предлагают такие компании, как <a href="https://www.telerik.com/">Telerik</a>, <a href="https://www.syncfusion.com/uwp-ui-controls">SyncFusion</a>, <a href="https://www.devexpress.com/Products/NET/Controls/Win10Apps/">DevExpress</a>, <a href="https://www.infragistics.com/products/universal-windows-platform">Infragistics</a>, <a href="https://www.componentone.com/Studio/Platform/UWP">ComponentOne</a> и <a href="https://www.actiprosoftware.com/products/controls/universal">ActiPro</a>. Эти элементы управления обеспечивают дополнительную поддержку для предприятий и разработчиков .NET путем дополнения стандартных системных элементов управления пользовательскими элементами управления и службами.
