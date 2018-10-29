---
description: Здесь вы найдете инструкции по проектированию и программированию для добавления элементов управления и шаблонов в приложение UWP. Свыше 45 функциональных элементов управления для использования с приложением.
title: Элементы управления и шаблоны UWP — разработка приложений для Windows
author: mijacobs
keywords: элементы управления uwp, пользовательский интерфейс, элементы управления приложения
label: Controls & patterns
template: detail.hbs
ms.author: mijacobs
ms.date: 11/16/2017
ms.topic: article
ms.assetid: ce2e611c-c419-4a14-9095-b88ac711d1b8
ms.localizationpriority: medium
ms.openlocfilehash: fdaa8cd73bb2f1199e7214206ec2b0b52628a906
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5750712"
---
# <a name="controls-and-patterns-for-uwp-apps"></a>Элементы управления и шаблоны для приложений UWP
 

В приложениях UWP <i>элемент управления</i> — это элемент пользовательского интерфейса, отображающий содержимое или обеспечивающий взаимодействие. Элементы управления— это основа пользовательского интерфейса. <i>Шаблон</i> — это макет для объединения нескольких элементов управления, позволяющий получить что-то новое.

Мы предоставляем более 45 элементов управления — от простых кнопок до мощных элементов управления данными, таких как представление сетки.  Эти элементы управления являются частью системы проектирования Fluent Design и помогают создавать удобный и масштабируемый пользовательский интерфейс, который прекрасно выглядит на всех устройствах и экранах разного размера. 

В статьях в этом разделе представлены инструкции по проектированию и программированию для добавления элементов управления и шаблонов в приложение UWP. 

## <a name="intro"></a>Введение

Общие инструкции и примеры кода для добавления и настройки стиля элементов управления на языках XAML и C#.

:::row:::
    :::column:::
      <p><b><a href="controls-and-events-intro.md">Добавление элементов управления и обработка событий</a></b> <br/>
Есть три основных этапа добавления элементов управления в приложение: добавление элемента управления в пользовательский интерфейс приложения, установка свойств элемента управления и добавление кода в обработчики событий элемента управления, чтобы он начал функционировать.</p>
    :::column-end:::
    :::column:::
      <p><b><a href="xaml-styles.md">Настройка стиля элементов управления</a></b> <br/>
Платформа XAML предоставляет множество способов настройки внешнего вида приложений. С помощью стилей можно настраивать свойства элементов управления и многократно использовать эти параметры, чтобы придать единообразный внешний вид нескольким элементам управления.</p>
    :::column-end:::
:::row-end:::

## <a name="get-the-windows-ui-library"></a>Получить библиотеку пользовательского интерфейса Windows
Некоторые элементы управления доступны только в библиотеке пользовательского интерфейса Windows. Чтобы получить его, см. в разделе [Библиотека пользовательского интерфейса Windows Общие сведения и инструкции по установке](/uwp/toolkits/winui/).

## <a name="alphabetical-index"></a>Алфавитный указатель 

Подробные сведения о конкретных элементах управления и шаблонах. (Список, отсортированный по функциям, см. в разделе <a href="controls-by-function.md">Элементы управления по функциям</a>.)

<div style="column-count: 2; column-gap: 40px; margin-top: 40px;" >
<ul style="margin-top: 0px; padding-top: 0px; list-style-type: none;">
<li style="list-style-type: none;"><a href="auto-suggest-box.md">Поле автозаполнения</a></li>

<li style="list-style-type: none;"><a href="app-bars.md">Панели</a></li>

<li style="list-style-type: none;"><a href="buttons.md">Кнопки</a></li>

<li style="list-style-type: none;"><a href="checkbox.md">Checkbox </a></li>

<li style="list-style-type: none;"><a href="color-picker.md">Палитра</a></li>

<li style="list-style-type: none;"><a href="contact-card.md">Карточка контакта</a></li>

<li style="list-style-type: none;"><a href="date-and-time.md">Элементы управления датой и временем</a></li>

<li style="list-style-type: none;"><a href="dialogs-and-flyouts/index.md">Диалоговые окна и всплывающие элементы</a></li>

<li style="list-style-type: none;"><a href="flipview.md">Представление пролистывания</a></li>

<li style="list-style-type: none;"><a href="forms.md">Формы</a></li>

<li style="list-style-type: none;"><a href="hub.md">Центр</a></li>

<li style="list-style-type: none;"><a href="hyperlinks.md">Гиперссылки</a></li>

<li style="list-style-type: none;"><a href="images-imagebrushes.md">Изображения и кисти изображений</a></li>

<li style="list-style-type: none;"><a href="inking-controls.md">Элементы управления рукописным вводом</a></li>

<li style="list-style-type: none;"><a href="lists.md">Списки</a></li>

<li style="list-style-type: none;"><a href="../../maps-and-location/controls-map.md">Элемент управления картой</a></li>

<li style="list-style-type: none;"><a href="master-details.md">Основные и подробные данные</a></li>

<li style="list-style-type: none;"><a href="media-playback.md">Воспроизведение мультимедиа</a></li>

<li style="list-style-type: none;"><a href="menus.md">Меню и контекстные меню</a></li>

<li style="list-style-type: none;"><a href="navigationview.md">Представление навигации</a></li>

<li style="list-style-type: none;"><a href="person-picture.md">Изображение пользователя</a></li>

<li style="list-style-type: none;"><a href="pivot.md">Сводка</a></li>

<li style="list-style-type: none;"><a href="progress-controls.md">Элементы управления "Ход выполнения"</a></li>

<li style="list-style-type: none;"><a href="radio-button.md">Переключатель</a></li>

<li style="list-style-type: none;"><a href="rating.md">Элемент управления "Оценка"</a></li>

<li style="list-style-type: none;"><a href="scroll-controls.md">Элементы управления прокруткой и панорамированием</a></li>

<li style="list-style-type: none;"><a href="search.md">Поиск</a></li>

<li style="list-style-type: none;"><a href="semantic-zoom.md">Контекстное масштабирование</a></li>

<li style="list-style-type: none;"><a href="shapes.md">Фигуры</a></li>

<li style="list-style-type: none;"><a href="slider.md">Ползунок</a></li>

<li style="list-style-type: none;"><a href="split-view.md">Комбинированный режим</a></li>

<li style="list-style-type: none;"><a href="text-controls.md">Элементы управления текстом</a></li>

<li style="list-style-type: none;"><a href="index.md">Плитки, индикаторы событий и уведомления</a></li>


<li style="list-style-type: none;"><a href="toggles.md">Toggle</a></li>
<li style="list-style-type: none;"><a href="tooltips.md">Подсказки</a></li>

<li style="list-style-type: none;"><a href="tree-view.md">Представление в виде дерева</a></li>

<li style="list-style-type: none;"><a href="web-view.md">Представление веб-страницы</a></li>
</ul>
</div>

## <a name="xaml-controls-gallery"></a>Галерея элементов управления XAML

Скачайте приложение _Галерея элементов управления XAML_ в магазине Microsoft Store, чтобы просмотреть эти элементы управления и систему проектирования Fluent Design в действии. Это приложение является интерактивным помощником для этого веб-сайта. После его установки вы можете использовать ссылки на отдельные страницы элементов управления для запуска приложения, чтобы увидеть его в действии.

<a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a>

<a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a>

<img src="images/xaml-controls-gallery.png" alt="XAML Controls Gallery screen" />

## <a name="additional-controls"></a>Дополнительные элементы управления

Дополнительные элементы управления для разработки UWP предлагаются такими компаниями, как <a href="http://www.telerik.com/">Telerik</a>, <a href="https://www.syncfusion.com/products/uwp">SyncFusion</a>, <a href="https://www.devexpress.com/Products/NET/Controls/Win10Apps/">DevExpress</a>, <a href="http://www.infragistics.com/products/universal-windows-platform">Infragistics</a>, <a href="https://www.componentone.com/Studio/Platform/UWP">ComponentOne</a> и <a href="http://www.actiprosoftware.com/products/controls/universal">ActiPro</a>. Эти элементы управления обеспечивают дополнительную поддержку для предприятий и разработчиков .NET путем дополнения стандартных системных элементов управления пользовательскими элементами управления и службами.  

Если вы хотите подробнее узнать об этих элементах управления, ознакомьтесь с примером кода <a href="https://github.com/Microsoft/Windows-appsample-customers-orders-database">Базы данных заказов клиентов</a> на сайте GitHub. В этом примере используется элемент управления сеткой данных и выполняется проверка вводимых данных средствами Telerik, которые являются частью пользовательского интерфейса для набора UWP. Пользовательский интерфейс для набора UWP — это коллекция из более чем 20 элементов управления, доступная в качестве проекта с открытым исходным кодом в фонде .NET.
