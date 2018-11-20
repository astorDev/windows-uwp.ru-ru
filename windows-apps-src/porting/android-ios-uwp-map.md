---
author: GrantMeStrength
Description: Compare platform features between iOS, Android, and Windows 10.
Search.Product: eADQiWindows 10XVcnh
title: Сопоставление концепций приложений для Windows для разработчиков iOS и Android
ms.author: jken
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 082736c8-2ac3-41b3-b246-e705edc23f34
ms.localizationpriority: medium
ms.openlocfilehash: f7e211ebfa28421340e716c0176cab80a9511671
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7279939"
---
#<a name="windows-apps-concept-mapping-for-android-and-ios-developers"></a>Сопоставление концепций приложений для Windows для разработчиков iOS и Android

Если вы являетесь разработчиком с навыками разработки или готовым кодом для Android и iOS и хотите перейти на Windows 10 и универсальную платформу Windows (UWP), этот ресурс содержит всю необходимую информацию для сопоставления функций — и ваших знаний — между тремя платформами.

См. также информацию по портированию в разделе [Переход от iOS к UWP](ios-to-uwp-root.md). Этот документ также доступен для [скачивания](https://www.microsoft.com/download/details.aspx?id=52041).

## <a name="user-interface-ui"></a>Пользовательский интерфейс


<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Язык дизайна.</strong><br><br>Набор соглашений, описывающих вид и работу приложений на платформе.</td>
<td align="left">Руководство по <strong>Android Material Design</strong> содержит описание визуального языка для дизайнеров и разработчиков Android.</td>
<td align="left"><strong>Human Interface Guidelines</strong> ("Руководство по пользовательскому интерфейсу") содержит рекомендации для дизайнеров и разработчиков iOS.</td>
<td align="left">В разделе <a href="https://dev.windows.com/design"><strong>Проектирование приложений UWP для Windows</strong></a> показано, как создать приложение, которое превосходно выглядит на любых устройствах под управлением Windows 10. Здесь описаны основы проектирования пользовательского интерфейса, методы создания отзывчивого дизайна и представлен полный список подробных руководств.<br/></td>
</tr>
<tr class="even">
<td align="left"><strong>Язык разметки пользовательского интерфейса.</strong> <br><br>Язык разметки, который отображает и описывает пользовательский интерфейс и его компоненты. Каждая платформа предоставляет программу как для визуального редактирования, так и для редактирования разметки.<br/></td>
<td align="left"><strong>XML-макеты</strong>, редактируемые с помощью <strong>Android Studio</strong> или <strong>Eclipse</strong>.</td>
<td align="left"><strong>XIB-файлы</strong> и <strong>раскадровки</strong>, редактируемые с помощью средства <strong>Interface Builder</strong> в Xcode.</td>
<td align="left"><strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt185595.aspx">XAML-файлы</a></strong>, редактируемые с помощью <strong><a href="https://www.visualstudio.com/">Microsoft Visual Studio</a></strong> и <strong><a href="https://msdn.microsoft.com/library/jj171012.aspx">Blend для Visual Studio</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt228259.aspx">Платформа XAML</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt228349.aspx">Создание пользовательского интерфейса с помощью XAML</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt228350.aspx">Определение макетов с помощью XAML</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Встроенные элементы управления пользовательского интерфейса.</strong> <br><br>Повторно используемые в системе элементы пользовательского интерфейса, такие как кнопки, элементы управления списками и текстом.</td>
<td align="left">Предварительно настроенные классы <strong>представлений</strong> и <strong>групп представлений</strong>, называемые мини-приложениями, макетами, текстовыми полями, контейнерами, элементами управления датой и временем и сложными элементами управления.</td>
<td align="left"><strong>Представления</strong> и <strong>элементы управления</strong>, содержащиеся в библиотеке объектов Xcode и перечисленные в каталоге пользовательского интерфейса UIKit. Представления включают представления изображений, выбора и представления с прокруткой. Элементы управления: кнопки, элементы выбора даты и текстовые поля.</td>
<td align="left">Платформа XAML предоставляет большой набор <strong>встроенных элементов управления</strong>, таких как кнопки, элементы управления списками, панели, текстовые элементы управления, панели команд, средства выбора, мультимедиа и рукописный ввод.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt228345.aspx">Добавление элементов управления и обработка событий</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Обработка событий элементов управления.</strong> <br><br>Описание логики, которая выполняется после активации событий в элементах управления пользовательского интерфейса.</td>
<td align="left"><strong>Обработчики событий</strong> и <strong>прослушиватели событий</strong> добавляются в XML или программными средствами.</td>
<td align="left">Элементы управления отправляют уведомления о <strong>действиях</strong> <strong>целевым объектам</strong>.</td>
<td align="left">Методы для обработки событий элемента управления XAML можно определить в <strong>файле кода программной части</strong>, связанном со страницей XAML. <strong>Обработчики событий</strong> всегда создаются в коде. Но вы можете подключить обработчики к событиям как в разметке XAML, так и в коде.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt228345.aspx">Добавление элементов управления и обработка событий</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt185584.aspx">Общие сведения о событиях и перенаправленных событиях</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Привязка данных.</strong> <br><br>Шаблон проектирования программного обеспечения, который обеспечивает отображение данных в пользовательском интерфейсе приложения и (дополнительно) синхронизацию с этими данными.</td>
<td align="left">Предоставляется <strong>библиотека привязки данных</strong> (все еще только в бета-версии).</td>
<td align="left">В iOS отсутствует встроенная система привязки. Для привязки данных можно использовать механизм <strong>наблюдения за значениями по ключам (KVO)</strong> как с помощью сторонней библиотеки, так и путем написания дополнительного кода. Элементы управления используют для получения данных подход "делегат/обратный вызов".</td>
<td align="left">Платформа UWP обрабатывает <strong>привязку данных</strong> за вас. Расширение разметки <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt204783.aspx">{x:Bind}</a></strong> используется для высокопроизводительной привязки, а расширение разметки <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt204782.aspx">{Binding}</a></strong> — для предоставления большего количества функций. После этого достаточно просто настроить привязку, выбрав использование <strong>односторонней привязки</strong> для отображения в пользовательском интерфейсе значений из источника данных, или <strong>двусторонней привязки</strong> для наблюдения за значениями и обновления интерфейса при их изменении.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt210947.aspx">Привязка данных</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Модель автоматизации пользовательского интерфейса.</strong> <br><br>Программный доступ к элементам пользовательского интерфейса, предназначенный для реализации в приложении специальных возможностей и выполнения автоматизированных сценариев тестирования, взаимодействующих с пользовательским интерфейсом.</td>
<td align="left"><strong>Текстовые метки</strong>, значения атрибутов <strong>contentDescription</strong> и <strong>hint</strong> помогают обеспечить доступ средств автоматизации к элементам пользовательского интерфейса. Android Studio позволяет создавать тесты пользовательского интерфейса с помощью тестирующих платформ <strong>UI Automator</strong> и <strong>Espresso</strong>.</td>
<td align="left">Инструмент <strong>Automation</strong> позволяет создавать автоматизированные сценарии тестирования пользовательского интерфейса, которые определяют элементы управления по параметрам <strong>специальных возможностей</strong> или по расположению элемента в <strong>иерархии элементов</strong>.</td>
<td align="left">В UWP вы получаете программный доступ к встроенным элементам интерфейса с помощью <strong><a href="https://msdn.microsoft.com/library/windows/apps/ee684076.aspx">модели автоматизации пользовательского интерфейса</a></strong>.<br/><strong><a href="https://msdn.microsoft.com/library/windows/apps/mt297667.aspx">Настраиваемые одноранговые классы автоматизации</a></strong> позволяют обеспечить поддержку автоматизации для ваших собственных настраиваемых классов пользовательского интерфейса. <strong><a href="https://msdn.microsoft.com/library/dd286726.aspx#VerifyingCodeUsingCUITCreate">Тестовый проект кода пользовательского интерфейса</a></strong> в Visual Studio позволяет автоматически протестировать все приложение через пользовательский интерфейс или провести изолированное тестирование пользовательского интерфейса.</td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Изменение внешнего вида элемента управления.</strong> <br><br>Изменение размера, цвета и других атрибутов.</td>
<td align="left">Элементы управления имеют <strong>свойства</strong>, которые можно изменять с помощью средства "Designer", разметки XML или программными средствами.</td>
<td align="left">Элементы управления имеют <strong>атрибуты</strong>, которые можно редактировать с помощью средства <strong>Attributes Inspector</strong> в Interface Builder или программно.</td>
<td align="left"><strong>Свойства</strong> элементов управления можно изменять в разметке XAML или программным способом с помощью Visual Studio и Blend для Visual Studio.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt228345.aspx">Добавление элементов управления и обработка событий</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Многократно используемые стили отображения.</strong> <br><br>Применение многократно используемых визуальных изменений к нескольким элементам управления.</td>
<td align="left"><strong>Стили XML</strong>— это наборы свойств, применяемых к одному или нескольким элементам управления.</td>
<td align="left">iOS не имеет встроенной поддержки многократно используемых стилей отображения, однако протокол UIAppearance позволяет нескольким элементам управления иметь общие атрибуты.</td>
<td align="left">Вы можете создавать многократно используемые <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.style.aspx">стили</a></strong>, которые могут применяться к нескольким элементам управления и храниться в <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.resourcedictionary.aspx">ResourceDictionary</a></strong> для повторного использования.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/hh465381.aspx">Краткое руководство: настройка стиля элементов управления</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Редактирование визуальной структуры элементов управления.</strong> <br><br>Настройка визуальной структуры элемента управления, не ограничивающаяся простым изменением свойств или атрибутов, например перемещение текста флажка под флажок.</td>
<td align="left">В Android не существует простого способа изменить визуальную структуру элементов управления.</td>
<td align="left">В iOS не существует простого способа изменить визуальную структуру элементов управления.</td>
<td align="left">Чтобы настроить визуальную структуру элемента управления, вы можете скопировать и изменить его <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.controltemplate.aspx">шаблон элемента управления</a></strong> в разметке XAML.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/hh465374.aspx">Краткое руководство: шаблоны элементов управления</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Встроенные сенсорные жесты.</strong> <br><br>Предоставление настраиваемой поддержки сенсорного ввода путем обработки высокоуровневых абстрагированных событий жестов, таких как касание и двойное касание, в представлениях и элементах управления.</td>
<td align="left"><strong>Детекторы жестов</strong> определяют основные сенсорные жесты, включая прокрутку, долгое нажатие, касание, двойное касание и смахивание.</td>
<td align="left">Платформа UIKit предоставляет встроенные <strong>распознаватели жестов</strong> определяющие сенсорные жесты, включая касание, сжатие, сдвиг, прокрутку, поворот и долгое нажатие.</td>
<td align="left"><strong>Элементы пользовательского интерфейса</strong> дают возможность обрабатывать <strong>статические события жестов</strong>, включая касание, двойное касание, правое касание и удерживание, а также <strong>динамические события жестов</strong>, включая скольжение, прокрутку, вращение, сжатие и растяжение. События жестов являются <strong>перенаправленными событиями</strong> и могут обрабатываться родительскими объектами, содержащими дочерний элемент UIElement.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt185617.aspx">Взаимодействие с помощью сенсорного экрана</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt185599.aspx#gestures__manipulations__and_interactions">Настраиваемые взаимодействия с пользователем — жесты, манипуляции и взаимодействия</a></td>
</tr>
</tbody>
</table>
<h2 id="navigation-and-app-structure">Навигация и структура приложения</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Макеты.</strong> <br><br>Макет определяет структуру пользовательского интерфейса.</td>
<td align="left">Макет состоит из <strong>групп представлений</strong>, таких как <strong>LinearLayout</strong> и <strong>RelativeLayout</strong>, которые могут иметь другие вложенные группы представлений и представления.</td>
<td align="left">Макет состоит из объекта <strong>UIViewController</strong>, содержащего представления <strong> UIView</strong>, которые могут быть вложенными.</td>
<td align="left">XAML предоставляет гибкую систему макетов, состоящую из <strong>классов панелей макета</strong>, таких как <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.canvas.aspx">Canvas</a></strong>, <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.grid.aspx">Grid</a></strong>, <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.relativepanel.aspx">RelativePanel</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx">StackPanel</a></strong> для создания статических и гибких макетов. <strong><a href="https://msdn.microsoft.com/library/ms171352.aspx">Свойства</a></strong> используются для управления размером и положением элементов.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt228350.aspx">Определение макетов с помощью XAML</a><br/></td>
</tr>
<tr class="even">
<td align="left"><strong>Навигация по одноранговым элементам.</strong> <br><br>Представление пользователю методов навигации между страницами, находящимися на одном уровне иерархии.</td>
<td align="left"><strong>Боковая навигация</strong> осуществляется с помощью <strong>вкладок</strong>, <strong>представлений прокрутки</strong> и <strong>навигационных секций</strong>.</td>
<td align="left"><strong>Контроллеры панели вкладок</strong>, <strong>контроллеры разделенного представления</strong> и <strong>контроллеры представления страниц</strong> обеспечивают навигацию между представлениями одного уровня иерархии.</td>
<td align="left">С помощью <strong><a href="https://msdn.microsoft.com/library/windows/apps/dn997788.aspx">вкладок и сводок</a></strong> можно отобразить постоянный список ссылок или вкладок над содержимым. <strong><a href="https://msdn.microsoft.com/library/windows/apps/dn997787.aspx">Панель навигации и комбинированный режим</a></strong> позволяют отображать список ссылок рядом с содержимым.<br/><br/><a href="https://msdn.microsoft.com/windows/uwp/layout/navigation-basics">Навигация</a><br/><br/><a href="https://msdn.microsoft.com/windows/uwp/layout/navigate-between-two-pages">Навигация между двумя страницами</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Иерархическая навигация.</strong> <br><br>Навигация между родительскими и дочерними страницами иерархии.</td>
<td align="left"><strong>Списки</strong>, <strong>сетки</strong>, <strong>кнопки</strong> и другие элементы управления обеспечивают <strong>нисходящую навигацию</strong> при использовании объектов <strong>Intent</strong> для загрузки других классов <strong>Activity</strong>.</td>
<td align="left"><strong>Контроллеры навигации</strong> дают возможность пользователям выполнять переход между уровнями иерархии.</td>
<td align="left"><strong><a href="https://msdn.microsoft.com/library/windows/apps/dn449149.aspx">Центры</a></strong> дают возможность предварительно показать пользователю содержимое, которое можно выбирать для перехода на дочерние страницы. <strong><a href="https://msdn.microsoft.com/library/windows/apps/dn997765.aspx">Шаблон основных и подробных данных</a></strong> дает возможность пользователям выбирать из списка сводок элементов, отображаемых рядом с соответствующим разделом сведений.<br/><br/><a href="https://msdn.microsoft.com/windows/uwp/layout/navigation-basics">Навигация</a><br/><br/><a href="https://msdn.microsoft.com/windows/uwp/layout/navigate-between-two-pages">Навигация между двумя страницами</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Навигация с помощью кнопки "Назад".</strong> <br><br>Переход назад в приложении.</td>
<td align="left">Кнопки <strong>Назад</strong> и <strong>Вверх</strong> в панели действий обеспечивают <strong>наследуемую</strong> и <strong>временную</strong> навигацию с использованием <strong>обратного стека</strong>.</td>
<td align="left">В <strong>контроллер навигации</strong> можно добавить кнопку перехода назад.<br/></td>
<td align="left">Вы можете легко обрабатывать нажатия аппаратной или программной кнопки "Назад" с помощью <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.frame.backstack.aspx">свойства обратного стека</a></strong>, которое позволяет пользователям перемещаться по <strong>журналу навигации</strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt465734.aspx">Навигация с помощью кнопки "Назад"</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Экран-заставка.</strong> <br><br>Изображение, показываемое при запуске приложения и в основном использующееся для добавления фирменной символики.</td>
<td align="left">Экран-заставка не предоставляется по умолчанию и реализуется посредством редактирования <strong>фона темы</strong> первой активности.</td>
<td align="left">Приложения должны иметь <strong>статическое изображение запуска</strong> или <strong>XIB-файл либо файл раскадровки запуска</strong>.</td>
<td align="left">Экран-заставка создается с помощью <strong>изображения</strong> и цветного фона. <a href="https://msdn.microsoft.com/library/windows/apps/mt187309.aspx">Время отображения экрана-заставки можно увеличить</a>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt187306.aspx">Добавление экрана-заставки</a></td>
</tr>
</tbody>
</table>
<h2 id="custom-inputs">Пользовательский ввод</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Речь.</strong> <br><br>Распознавание речи для голосового ввода и дополнительные голосовые возможности.</td>
<td align="left">Голосовой ввод может обеспечиваться любым приложением, в котором реализован класс <strong>RecognizerIntent</strong>, таким как <strong>Голосовой поиск Google</strong>. Класс <strong>SpeechRecognizer</strong> позволяет приложениям использовать API распознавания речи Google.</td>
<td align="left">Приложения могут использовать класс <strong>SFSpeechRecognizer</strong> для реализации речевого ввода и распознавания речи.</td>
<td align="left">Можно использовать API <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt185615.aspx">распознавания речи</a></strong> для взаимодействия с приложением, выполняемым на переднем плане. Вы можете использовать голосовые <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt185598.aspx">взаимодействия с Кортаной</a></strong> для запуска приложений на переднем плане или в фоновом режиме, а также для взаимодействия с фоновыми приложениями.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt185614.aspx">Взаимодействие с помощью голосовых функций</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Настраиваемых пользовательский ввод.</strong> <br><br>Обработка ввода с клавиатуры, мыши, пера и других средств ввода.</td>
<td align="left">Поддерживается взаимодействие с помощью <strong>касаний</strong>, <strong>сенсорной панели</strong>, <strong>пера</strong>, <strong>мыши</strong> и <strong>клавиатуры</strong>. Движение и ввод передается тем же способом, что и касание, но существует возможность получить дополнительные сведения об <strong>устройстве ввода</strong>.</td>
<td align="left">Обеспечивается поддержка <strong>сенсорного ввода</strong>, <strong>Apple Pencil</strong> и ввода с аппаратной <strong>клавиатуры</strong>.</td>
<td align="left">Поддерживается широкий набор взаимодействий, включая <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt185617.aspx">сенсорный ввод</a></strong>, взаимодействие с помощью <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt187313.aspx">сенсорной панели</a></strong>, <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt187311.aspx">пера</a></strong> с функцией рукописного ввода, <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt187308.aspx">мыши</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt185607.aspx">клавиатуры</a></strong>. Ваши приложения могут обрабатывать данные, не зная, какое устройство ввода используется, а также при необходимости получать доступ к необработанным данным устройства ввода.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt404610.aspx">Работа с входными данными указателя</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt185599.aspx">Настраиваемые взаимодействия с пользователем</a></td>
</tr>
</tbody>
</table>
<h2 id="data">Данные</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Локальные данные приложения.</strong> <br><br>Локальное хранение связанных с приложением параметров и файлов.</td>
<td align="left">Локальные файлы можно сохранять с помощью методов <strong>openFileOutput</strong> и <strong>openFileInput</strong>. Для доступа к параметрам в файле <strong>общих параметров</strong> используется метод <strong>getSharedPreferences</strong>.</td>
<td align="left">Локальные файлы можно сохранять в каталоге <strong>Application Support</strong>, доступ к которому осуществляется через класс <strong>NSFileManager</strong>. Параметры в файлах <strong>параметров</strong> доступны через класс <strong>NSUserDefaults</strong>.</td>
<td align="left">Классы <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/br230562.aspx">Windows.Storage</a></strong> обеспечивают унифицированное хранение локальных данных. Параметры хранятся в виде объекта <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.storage.applicationdatacontainer.aspx">ApplicationDataContainer</a></strong>, доступ к которому осуществляется через свойство <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.storage.applicationdata.localsettings.aspx">ApplicationData.LocalSettings</a></strong>. Файлы хранятся в объекте <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.storagefolder.aspx">StorageFolder</a></strong>, доступ к которому осуществляется через свойство <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.storage.applicationdata.localfolder.aspx">ApplicationData.LocalFolder</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt299098.aspx">Хранение и извлечение параметров и прочих данных приложения</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Сохранение в локальной базе данных.</strong> <br><br>Сохранение данных в реляционной базе данных с объектно-реляционными модулями сопоставления (ORM) (если применимо).</td>
<td align="left">Предоставляется база данных <strong>SQLite</strong>. Встроенные ORM отсутствуют. Запросы SQL выполняются с помощью класса <strong>SQLiteDatabase</strong>.</td>
<td align="left">Предоставляется база данных <strong>SQLite</strong>. <strong>CoreData</strong>— это встроенная платформа графов объектов, которая может использоваться с SQLite и предоставляет функции, сравнимые с ORM.</td>
<td align="left">Данные можно хранить с помощью <strong>SQLite</strong>. <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt592863.aspx">Entity Framework</a></strong>— это встроенный объектно-реляционный модуль сопоставления, устраняющий необходимость в написании большого объема кода для доступа к данным и позволяющий с легкостью обращаться к базе данных без написания кода SQL. Запросы SQL выполняются непосредственно в <a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt592864.aspx">библиотеке SQLite</a>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt592862.aspx">Доступ к данным</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Библиотеки HTTP для доступа к REST.</strong> <br><br>Встроенные библиотеки, которые позволяют связываться с веб-службами и веб-серверами с использованием HTTP(S).<br/></td>
<td align="left">Библиотеки HTTP <strong>HttpURLConnection</strong> и <strong>Volley</strong>.</td>
<td align="left"><strong>NSURLSession</strong>, <strong>NSURLConnection</strong> и <strong>NSURLDownload</strong>.</td>
<td align="left">Вы можете использовать встроенный API <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.web.http.httpclient">HttpClient</a></strong> для доступа к основным функциями HTTP, включая команды GET, DELETE, PUT, POST, схемы проверки подлинности, SSL, файлы cookie и информацию о ходе выполнения запроса.</td>
</tr>
<tr class="even">
<td align="left"><strong>Облачные службы резервного копирования.</strong> <br><br>Предоставляемые платформой службы резервного копирования данных приложений.</td>
<td align="left"><strong>Backup manager</strong> Android осуществляет резервное копирование данных приложений с помощью службы <strong>Android Backup Service</strong> .</td>
<td align="left">Пользователи могут настроить <strong>резервное копирование iCloud</strong>, в том числе для данных приложений. Приложения используют совместимые с iCloud данные <strong>Core Data</strong> и хранилища <strong>iCloud key-value store</strong> и <strong>iCloud document storage</strong>.</td>
<td align="left">Любые данные приложения, которые вы сохраняете с помощью API перемещаемых <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.applicationdata.aspx">ApplicationData</a></strong> (включая <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.applicationdata.roamingfolder.aspx">RoamingFolder</a></strong> и <a href="https://msdn.microsoft.com/library/windows/apps/windows.storage.applicationdata.roamingsettings.aspx"><strong>RoamingSettings</strong></a>), автоматически синхронизируются с облаком, а также с остальными устройствами пользователя. Синхронизация выполняется через учетную запись Microsoft пользователя.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/hh465094.aspx">Руководство по перемещаемым данным приложения</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Скачивание файлов по HTTP.</strong> <br><br>Скачивание больших и маленьких файлов по HTTP.</td>
<td align="left">Для скачивания по протоколам HTTP и FTP используются <strong>URLConnection</strong> и <strong>HTTPURLConnection</strong>. Также можно использовать системный <strong>диспетчер загрузки</strong> для скачивания в фоновом режиме.</td>
<td align="left">Для скачивания по протоколам HTTP и FTP можно использовать <strong>NSURLSession</strong> и <strong>NSURLConnection</strong>.</td>
<td align="left"><strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.networking.backgroundtransfer.aspx">API фоновой передачи данных</a></strong> позволяет надежно передавать данные по протоколам HTTP(S) и FTP с учетом приостановки приложения и потерь подключения, а также регулировкой в зависимости от типа подключения и уровня заряда батареи. Для файлов меньшего размера рекомендуется использовать <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.web.http.httpclient.aspx">HttpClient</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt280235.aspx">Какую сетевую технологию выбрать?</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt280377.aspx">Фоновая передача данных</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Сокеты.</strong> <br><br>Создание низкоуровневой датаграммы UDP и сокетов TCP для связи с другими устройствами с помощью собственного протокола.</td>
<td align="left">Класс <strong>Socket</strong> предоставляет сокеты TCP, а класс <strong>DatagramSocket</strong> — сокет UDP.</td>
<td align="left">Классы <strong>NSStream</strong> и <strong>CFStream</strong> предоставляют сокеты TCP, а класс <strong>CFSocket</strong>— сокеты UDP.</td>
<td align="left">Вы можете использовать класс <strong><a href="https://msdn.microsoft.com/library/windows/apps/br241319">DatagramSocket</a></strong> для связи с использованием сокета датаграмм UDP и класс <strong><a href="https://msdn.microsoft.com/library/windows/apps/br226882">StreamSocket</a></strong> для связи по протоколу TCP или Bluetooth RFCOMM.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt280233.aspx">Основы работы с сетями</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt280235.aspx">Какую сетевую технологию выбрать?</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt280234.aspx">Обзор сокетов</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>WebSockets.</strong> <br><br>Обеспечение двусторонней связи между клиентом и сервером и передачи данных в реальном времени.</td>
<td align="left">Встроенные библиотеки WebSockets в Android отсутствуют.</td>
<td align="left">Встроенные библиотеки WebSockets в iOS отсутствуют.</td>
<td align="left">Безопасные подключения к серверам с поддержкой WebSocket можно установить с помощью класса <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.networking.sockets.messagewebsocket.aspx">MessageWebSocket</a></strong> для передачи небольших сообщений с уведомлениями о получении и с помощью класса <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.networking.sockets.streamwebsocket.aspx">StreamWebSocket</a></strong> для передачи больших двоичных файлов с возможностью считывания по разделам.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt280233.aspx">Основы работы с сетями</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt280235.aspx">Какую сетевую технологию выбрать?</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt186447.aspx">Обзор WebSockets</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Библиотеки OAuth.</strong> <br><br>Библиотеки OAuth, обеспечивающие доступ к сторонним поставщикам OAuth и управлению учетными записями, встроенному в платформу.</td>
<td align="left">Универсальные библиотеки OAuth не предоставляются. Класс <strong>GoogleAuthUtil</strong> предоставляется для проверки подлинности OAuth с помощью служб Google Play.<br/></td>
<td align="left">Универсальные библиотеки OAuth не предоставляются. <strong>Платформа учетных записей</strong> предоставляет доступ к сохраненным на устройстве учетным записям пользователей, таких как учетные записи Facebook и Twitter.</td>
<td align="left">Универсальная библиотека OAuth <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt270196.aspx">брокер веб-проверки подлинности</a></strong> позволяет подключаться к службам поставщиков сетевых удостоверений сторонних разработчиков. <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt270189.aspx">Хранилище учетных данных</a></strong> позволяет вашим пользователям сохранить учетные данные и использовать их на нескольких устройствах. Пространство имен <strong><a href="https://msdn.microsoft.com/library/windows/apps/dn896755.aspx">Microsoft.Live</a></strong> позволяет с легкостью получать доступ к протоколу OAuth Live SDK для доступа к службам Microsoft.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt270184.aspx">Проверка подлинности и удостоверение пользователя</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/windows.security.authentication.web.aspx">Документация по API Windows.Security.Authentication.Web documentation</a><br/><br/><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/WebAuthenticationBroker">Пример кода WebAuthenticationBroker</a></td>
</tr>
</tbody>
</table>
<h2 id="tooling">Инструменты</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Интегрированная среда разработки</strong> <br><br>Набор инструментов, используемый для создания приложения.</td>
<td align="left"><strong>Android Studio</strong> и <strong>Eclipse</strong>. Google рекомендует разработчикам использовать Android Studio.</td>
<td align="left"><strong>Xcode</strong></td>
<td align="left">В <strong><a href="https://www.visualstudio.com/features/universal-windows-platform-vs.aspx">Visual Studio</a></strong> и <strong><a href="https://msdn.microsoft.com/library/jj171012.aspx">Blend для Visual Studio</a></strong> вы найдете все инструменты, необходимые для написания, проектирования, подключения, отладки, анализа, оптимизации и тестирования приложений UWP. Visual Studio также предоставляет <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt188754.aspx">эмуляторы</a></strong> устройств под управлением Windows 10, чтобы вы могли протестировать приложение на разнообразных эмулированных устройствах.<br/><br/><a href="https://dev.windows.com/downloads">Скачиваемые файлы и инструменты для UWP</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Организация кода.</strong> <br><br>Базовая структура папок приложения, часто создаваемая из начального шаблона.</td>
<td align="left">Файл <strong>AndroidManifest</strong>, папка <strong>java</strong> с исходными файлами, папка <strong>res</strong> с ресурсами, включая макеты и значения, сценарии сборки <strong>Gradle</strong> в Android Studio и сценарии сборки <strong>Ant</strong> в Eclipse.</td>
<td align="left">Исходные и <strong>вспомогательные файлы</strong>, файлы <strong>Info.plist</strong>, <strong>Main.storyboard</strong> и <strong>LaunchScreen.storyboard</strong>. Изображения хранятся <strong>библиотеках ресурсов</strong>.</td>
<td align="left">Приложение UWP содержит файлы XAML и файлы кода для вашего приложения под названием Example.xaml и Example.xaml.cs, различные изображения в <strong>папке Assets</strong>, начальную страницу (например, <strong>MainPage.xaml</strong> и <strong>MainPage.xaml.cs</strong>) и манифест.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/dn765018.aspx">Создание приложения Hello, world</a></td>
</tr>
</tbody>
</table>
<h2 id="app-lifecycle">Жизненный цикл приложения</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Жизненный цикл приложения.</strong> <br><br>Обработка событий при запуске, приостановке, возобновлении и закрытии приложения, обеспечивающая возможность сохранения и восстановления состояния приложения или выполнения других задач.</td>
<td align="left">Каждая активность имеет собственный <strong>жизненный цикл активности</strong> с такими состояниями как <strong>возобновлено</strong>. <strong>Обратные вызовы жизненного цикла</strong> , например <strong>onResume</strong> реализуются в ваших <strong>классах активности</strong>.</td>
<td align="left"><strong>Жизненный цикл приложения</strong> имеет такие состояния, как <strong>приостановлено</strong>. Такие методы, как <strong>applicationDidEnterBackground:</strong> реализуются в <strong>объекте делегата приложения</strong> для выполнения кода при изменении состояния.</td>
<td align="left">Ваше приложение имеет <strong>состояния выполнения</strong> NotRunning, Activated, Running, Suspending, Suspended и Resuming.<br/><br/>Вы можете реализовать в приложении методы <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.aspx">класса Application</a></strong> OnLaunched, OnActivated, Suspending и Resuming для выполнения кода при изменении состояния.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt243287.aspx">Жизненный цикл приложения</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Фоновые задачи.</strong> <br><br>Задачи, которые выполняют фоновые операции и продолжают выполняться, когда приложение не находится на переднем плане.</td>
<td align="left">Приложения могут запускать <strong>службы</strong>, выполняющие фоновые операции, когда приложение не находится на переднем плане. Службы имеют собственный <strong>жизненный цикл</strong> и регистрируются в манифесте.</td>
<td align="left"><strong>Фоновое выполнение</strong> разрешено только для определенных типов задач.<br/><br/>Приложения объявляют <strong>поддерживаемые фоновые задачи</strong> в файле Info.plist с использованием <strong>UIBackgroundModes</strong>.<br/><br/>Система контролирует момент и продолжительность выполнения фоновых задач.</td>
<td align="left">Можно создать фоновую задачу, реализовав интерфейс <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.ibackgroundtask.aspx">IBackgroundTask</a></strong> и зарегистрировав задачу в манифесте приложения. Можно настроить выполнение задачи по <a href="https://msdn.microsoft.com/library/windows/apps/mt186458.aspx"><strong>таймеру</strong></a>, <a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.systemtriggertype.aspx"><strong>системному триггеру</strong></a> и <a href="https://msdn.microsoft.com/library/windows/apps/mt185632.aspx"><strong>триггеру обслуживания</strong></a>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt299103.aspx">Поддержка приложения с помощью фоновых задач</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt299100.aspx">Создание и регистрация фоновой задачи</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt187310.aspx">Руководство по работе с фоновыми задачами</a></td>
</tr>
</tbody>
</table>
<h2 id="performance">Производительность</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Рекомендации по повышению производительности.</strong> <br><br>Руководство по созданию быстрых и гибких приложений с ускоренным запуском и учетом уровня заряда батареи.</td>
<td align="left">Android предоставляет обучающее руководство <strong>Рекомендации по повышению производительности</strong>.</td>
<td align="left">iOS предоставляет документ <strong>Обзор производительности</strong>.</td>
<td align="left">Вы можете ознакомиться с подробным <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt270266.aspx">Руководством по повышению производительности</a></strong>, описывающим такие темы, как планирование и измерение производительности, управление памятью, плавные анимации, эффективный доступ к файловой системе и инструменты для профилирования и производительности.</td>
</tr>
<tr class="even">
<td align="left"><strong>Оптимизация представлений для ускорения отклика интерфейса пользователя.</strong> <br><br>Повышение производительности путем оптимизации представлений.</td>
<td align="left">Оптимизация <strong>иерархий макетов</strong> с помощью инструмента Hierarchy Viewer, <strong>повторное использование макетов</strong> и загрузка <strong>представлений по требованию</strong> — это методы обеспечения быстрого отклика потока пользовательского интерфейса и предотвращения появления диалоговых окон &quot;"Приложение не отвечает"&quot; (<strong>ANR</strong>).<br/></td>
<td align="left">Устранение проблем интерфейса посредством <strong>закадровой отрисовки</strong>, <strong>режима смешивания слоев</strong> и <strong>растеризации</strong> с помощью инструмента <strong>Core Animation</strong> помогает обеспечить быстроту отклика потока пользовательского интерфейса.</td>
<td align="left">Вы можете легко <strong>оптимизировать</strong> <strong>разметку</strong> и <strong>макеты</strong> XAML, выполнив несколько простых действий. Методы включают уменьшение структуры макета, сокращение количества элементов и перерисовки. <br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt185403.aspx">Обеспечение быстрого отклика потока пользовательского интерфейса</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt204779.aspx">Оптимизация разметки XAML</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt404609.aspx">Оптимизация макета XAML</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Потоки.</strong> <br><br>Использование потоков для обеспечения <strong>скорости отклика интерфейса пользователя</strong> и <strong>параллельного выполнения нескольких задач</strong>.</td>
<td align="left">Использование потоков обеспечивается при помощи классов <strong>Runnable</strong>, <strong>Handler</strong>, <strong>ThreadPoolExecutor</strong> и класса <strong>AsyncTask</strong> на более высоком уровне.</td>
<td align="left">Использование потоков обеспечивается при помощи класса <strong>NSThread</strong>, платформы <strong>Grand Central Dispatch</strong> и класса <strong>NSOperation</strong> на более высоком уровне.</td>
<td align="left">Вы можете работать с потоками, отправляя <strong>рабочие элементы</strong> в <strong>пул потоков</strong> при помощи метода <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.system.threading.threadpool.runasync.aspx">RunAsync</a></strong>. Можно использовать таймер для отправки рабочего элемента, используя метод <strong><a href="https://msdn.microsoft.com/library/windows/apps/br230590.aspx">CreateTimer</a></strong>, и создавать периодические рабочие элементы с помощью метода <strong><a href="https://msdn.microsoft.com/library/windows/apps/br230589.aspx">CreatePeriodicTimer</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt187339.aspx">Отправка рабочего элемента в пул потоков</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt187341.aspx">Отправка рабочего элемента по таймеру</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt187338.aspx">Создание периодического рабочего элемента</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt187336.aspx">Рекомендации по использованию пула потоков</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Асинхронное программирование.</strong> <br><br>Предотвращение использования сложных потоков путем использования шаблонов асинхронного программирования для обеспечения быстроты отклика потока пользовательского интерфейса.</td>
<td align="left">Использование <strong>потоков необходимо</strong> для создания собственных асинхронных классов. Некоторые встроенные классы являются асинхронными.</td>
<td align="left">Использование <strong>потоков необходимо</strong> для создания собственных асинхронных классов. Некоторые встроенные классы являются асинхронными.</td>
<td align="left">Вы можете использовать асинхронные шаблоны, чтобы избежать блокировки главного потока при создании собственных API, например с помощью ключевых слов <strong>Async</strong> и <strong>Await</strong> в C# и Visual Basic. Возможно использование встроенных асинхронных API, имена которых заканчиваются на <strong>Async</strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt187335.aspx">Асинхронное программирование</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt187337.aspx">Вызов асинхронных API в C# и Visual Basic</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Оптимизация представлений списков.</strong> <br><br>Встроенные шаблоны, помогающие оптимизировать списки данных, производительность которых зачастую снижается при отображении большого объема данных.</td>
<td align="left">Во избежание повторного поиска представления используется шаблон проектирования <strong>ViewHolder</strong>, что позволяет использовать элементы интерфейса пользователя повторно.</td>
<td align="left">Для повышения производительности <strong>UITableView</strong> можно применить различные виды оптимизации. Встроенные средства отсутствуют.</td>
<td align="left">Вы можете использовать элементы управления <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listview.aspx">ListView</a> и <a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.gridview.aspx">GridView</a>, обеспечивающие <strong>виртуализацию пользовательского интерфейса</strong>, для более плавного выполнения сдвига и прокрутки и ускорения запуска. Вы также можете реализовать в источнике данных интерфейсы <a href="https://msdn.microsoft.com/library/windows/apps/system.collections.ilist.aspx">IList</a> и <a href="https://msdn.microsoft.com/library/windows/apps/system.collections.specialized.inotifycollectionchanged.aspx">INotifyCollectionChanged</a> для обеспечения <strong>виртуализации данных</strong> и дополнительного повышения производительности.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt204776.aspx">Оптимизация пользовательского интерфейса ListView и GridView</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt574120.aspx">Виртуализация данных ListView и GridView</a></td>
</tr>
</tbody>
</table>
<h2 id="monetization">Получение дохода</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Покупки из приложения.</strong> <br><br>Возможности платформы, позволяющие пользователям совершать покупки в приложениях.</td>
<td align="left">Службы Google предоставляют API <strong>In-app Billing</strong>. Продукты добавляются в <strong>консоли разработчика Google Play</strong>. Покупки из приложения реализуются с помощью библиотеки <strong>Google Play Billing Library</strong>.</td>
<td align="left">Продукты добавляются в <strong>iTunes Connect</strong>. Покупки из приложения реализуются с помощью платформы <strong>StoreKit</strong>.<br/><br/>Покупка продуктов осуществляется посредством классов <strong>SKMutablePayment</strong> и <strong>SKPaymentQueue</strong>.</td>
<td align="left">Внутренние продукты приложения создаются путем <a href="https://msdn.microsoft.com/library/windows/apps/mt148551.aspx">добавление их в приложение и отправки в Store</a>. <br/><br/>Для определения покупок из приложения используется <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentapp.aspx">класс CurrentApp</a></strong>. <br/><br/>Для отображения интерфейса, позволяющего пользователям приобретать продукты, используется класс <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentapp.requestproductpurchaseasync.aspx">CurrentApp.RequestProductPurchaseAsync</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219684.aspx">Поддержка покупки продуктов внутри приложения</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Покупки потребляемых внутренних продуктов приложения.</strong> <br><br>Внутренние продукты приложения, которые можно приобрести, использовать, а затем приобрести еще раз.</td>
<td align="left">Покупка потребляемых продуктов обеспечивается путем совершения обычной покупки и ее потребления с помощью <strong>consumePurchase</strong>, что позволяет приобрести продукт, использовать его, а затем приобрести еще раз.</td>
<td align="left"><strong>Потребляемые продукты определяются</strong> в iTunes Connect.</td>
<td align="left">Поддержка потребляемых продуктов обеспечивается путем <a href="https://msdn.microsoft.com/library/windows/apps/mt148534.aspx">указания типа продукта "Потребляемый" при отправке продукта</a> в Store. После совершения покупки потребляемого продукта вызывается метод <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentapp.reportconsumablefulfillmentasync.aspx">CurrentApp.ReportConsumableFulfillmentAsync</a></strong>, позволяющий пользователю получить доступ к продукту.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219683.aspx">Поддержка покупок потребляемых продуктов внутри приложения</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Тестирование внутренних покупок приложения.</strong> <br><br>Возможность протестировать код внутренней покупки приложения без отправки приложения в Store.</td>
<td align="left">Для тестирования используется <strong>In-app Billing Sandbox</strong>.</td>
<td align="left">Для тестирования используются <strong>учетные записи тестировщиков Sandbox</strong>.</td>
<td align="left">Вы можете тестировать покупки внутри приложения, просто используя класс <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentappsimulator.aspx">CurrentAppSimulator</a></strong> вместо класса CurrentApp.<br/><br/></td>
</tr>
<tr class="even">
<td align="left"><strong>Пробные версии.</strong> <br><br>Возможность ограничения количества доступного содержимого или отключения рекламы в зависимости от версии приложения.</td>
<td align="left">Google Play <strong>официально не поддерживает пробные версии приложений</strong>. Поддержка пробных версий и отключения рекламы обеспечивается путем создания внутренней покупки приложения и выполнения соответствующего кода после подтверждения совершения покупки.</td>
<td align="left">App Store <strong>официально не поддерживает пробные версии</strong>. Поддержка пробных версий и отключения рекламы обеспечивается путем создания внутренней покупки приложения и выполнения соответствующего кода после подтверждения совершения покупки.</td>
<td align="left">Вы можете предлагать бесплатную пробную версию вашего приложения с помощью <strong><a href="https://msdn.microsoft.com/library/windows/apps/mt148548.aspx">параметра "Бесплатная пробная версия"</a></strong> при отправке приложения в Store. Затем можно использовать свойство <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.licenseinformation.istrial.aspx">LicenseInformation.IsTrial</a></strong> для проверки состояния пробной версии приложения и выполнения различных ветвей кода в зависимости от значения свойства. Для получения уведомления о том, что пользователь изменил статус пробной версии во время работы приложения, можно зарегистрировать обработчик <a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.licenseinformation.licensechanged">события LicenseChanged</a>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219685.aspx">Исключение или ограничение функций в пробной версии</a></td>
</tr>
</tbody>
</table>
<h2 id="adapting-to-multiple-platforms">Адаптация для нескольких платформ</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Адаптивный пользовательский интерфейс: гибкие макеты.</strong> <br><br>Поддержка нескольких размеров экрана различной высоты и ширины.</td>
<td align="left">Гибкие макеты реализуются с помощью значений <strong>wrap_content</strong> и <strong>match_parent</strong> объектов LinearLayout или путем использования для выравнивания объектов RelativeLayout.</td>
<td align="left">Гибкие макеты реализуются с помощью <strong>адаптивной модели</strong> с универсальными раскадровками, использующей функцию <strong>Auto Layout</strong> с такими <strong>ограничениями</strong> и <strong>признаками</strong>, как horizontalSizeClass и displayScale, применяемыми к контроллерами представлений.</td>
<td align="left">Вы можете создать гибкий макет, используя <strong>свойства макета</strong> и <strong>панели</strong> фиксированного и динамически изменяемого размера.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt228350.aspx#layout_overview">Определение макетов с помощью XAML — панели и свойства макета</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/dn958435.aspx">Введение в адаптивный дизайн</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Адаптивный пользовательский интерфейс: специально разработанные макеты.</strong> <br><br>Поддержка нескольких размеров экрана с использованием отдельных целевых макетов.</td>
<td align="left">Предоставление альтернативных файлов макета для различных конфигураций экрана в каталоге ресурсов с помощью <strong>квалификаторов конфигурации</strong>, таких как <strong>small</strong>, <strong>large</strong>, <strong>ldpi</strong> и <strong>hdpi</strong>, позволяет создавать отдельные макеты для экранов различных размеров и плотности пикселей.</td>
<td align="left">Определите <strong>отдельные раскадровки для iPhone и iPad</strong>, чтобы адаптировать макеты универсального приложения для различных семейств устройств.</td>
<td align="left">Вы можете создать специально разработанный макет, определив <strong>отдельные файлы разметки XAML</strong> для каждого семейства устройств.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt228350.aspx#tailored_layouts">Определение макетов с помощью XAML — специально разработанные макеты</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Адаптивный пользовательский интерфейс: гибкие макеты</strong> <br><br>Обработка изменений размера экрана, таких как поворот и изменение размеров окна.</td>
<td align="left">Гибкие макеты реализуются посредством <strong>LinearLayout</strong> и <strong>RelativeLayout</strong> или предоставления альтернативных файлов макета для различных ориентаций.</td>
<td align="left">При изменении <strong>размеров</strong> или <strong>признаков</strong> представления применяются указанные в раскадровках <strong>ограничения</strong>.</td>
<td align="left">Вы можете с легкостью адаптировать, перемещать, масштабировать, отображать и заменять разделы пользовательского интерфейса во время выполнения приложения в ответ на изменение размеров окна с помощью классов <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.visualstate.aspx">VisualState</a></strong>, <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.visualstatemanager.aspx">VisualStateManager</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.adaptivetrigger.aspx">AdaptiveTrigger</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt228350.aspx#visual_states_and_state_triggers">Определение макетов с помощью XAML — визуальные состояния и триггеры состояния</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/dn958435.aspx">Введение в адаптивный дизайн</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Поддержка различных возможностей устройств.</strong> <br><br>Использование расширенных аппаратных функций при сохранении поддержки не обладающих ими устройств.</td>
<td align="left">Проверка функций устройства во время выполнения с помощью <strong>PackageManager.hasSystemFeature</strong> позволяет определить, можно ли выполнять аппаратно-зависимый код.</td>
<td align="left">Не существует <strong>единой проверки</strong>, которую можно выполнить для определения функций устройства. Необходимо проверять наличие каждой функции для определения возможности выполнения аппаратно-зависимого кода.</td>
<td align="left">Вы можете добавить к вашему пакету <strong>SDK расширения платформы</strong> для использования расширенных возможностей различных семейств устройств, включая телефоны, настольные устройства и оборудование IoT. Чтобы определить наличие типов и членов при выполнении, используется <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.foundation.metadata.apiinformation.aspx">API ApiInformation</a></strong>. Типы и члены вызываются только при их наличии.</td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Поддержка различных возможностей устройств.</strong> <br><br>Использование расширенных аппаратных функций при сохранении поддержки не обладающих ими устройств.</td>
<td align="left">В пакет приложения можно добавить библиотеку <strong>Android Support Library</strong> для обеспечения доступа к новым API в более старых версиях Android. Проверку уровня API во время выполнения можно произвести с помощью <strong>Build.Version.SDK_INT</strong>.</td>
<td align="left">Для определения доступности API используются стандартные проверки среды выполнения, такие как метод <strong>class</strong>, определяющий, существует ли класс, и <strong>respondsToSelector:</strong> для проверки методов в классах.</td>
<td align="left">Вы можете использовать <strong><a href="https://msdn.microsoft.com/library/windows/apps/dn949005.aspx">ApiInformation.IsApiContractPresent</a></strong> для проверки наличия контракта API с указанным основным и вспомогательным номером версии. Чтобы определить наличие типов и членов при выполнении, используется <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.foundation.metadata.apiinformation.aspx">API ApiInformation</a></strong>. Типы и члены вызываются только при их наличии.</td>
</tr>
</tbody>
</table>
<h2 id="notifications">Уведомления</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Плитки и индикаторы событий.</strong> <br><br>Предоставление пользователям обновлений на начальном экране.</td>
<td align="left"><strong>Мини-приложения приложений</strong>— это представления вашего приложения, которые могут быть размещены на начальном экране и получать периодические обновления. <strong>Система индикаторов событий</strong> в Android отсутствует. Системы, аналогичной плиткам, не существует.</td>
  <td align="left"><strong>Виджеты</strong> в iOS отображаются в центре уведомлений и реализованы как <strong>расширения приложений</strong>. К значку приложения можно также добавить <strong>индикатор событий</strong> с номером, меняющимся при получении локального или удаленного уведомления. Система плиток отсутствует.</td>
<td align="left">Приложение имеет <strong>плитку</strong>, которую можно закрепить на начальном экране и использовать для отображения выбранного вами текста, изображений и <strong>индикатора событий</strong> с глифами и числами. Содержимое плитки можно обновлять из приложения через push-уведомления или по предопределенному расписанию. Плитки могут быть адаптивными и меняться в зависимости от места отображения.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt185605.aspx">Создание плиток</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt590880.aspx">Создание адаптивных плиток</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt187193.aspx">Выбор способа доставки уведомлений</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/hh465403.aspx">Руководство по плиткам и индикаторам событий</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Отображение уведомлений.</strong> <br><br>Типы отображаемых уведомлений.</td>
<td align="left">Уведомления могут отображаться в <strong>области уведомлений</strong> и <strong>секции уведомлений</strong>. <strong>Всплывающие уведомления</strong> отображаются в небольшом перемещаемом окне. Можно добавить к уведомлениям действия, определив <strong>PendingIntent</strong>.</td>
<td align="left">Всплывающие уведомления отображаются в виде <strong>баннеров</strong> и <strong>предупреждений</strong>. К <strong>интерактивным уведомлениям</strong> с помощью <strong>UIMutableUserNotificationAction</strong> можно добавить настраиваемые кнопки действий.</td>
<td align="left">Вы можете создавать адаптивные <strong>всплывающие уведомления</strong>. Всплывающие уведомления можно определять в XML, добавляя визуальное содержимое и <strong>действия</strong> в виде кнопок, полей ввода и голосовых команд.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt631604.aspx">Адаптивные и интерактивные всплывающие уведомления</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt187193.aspx">Выбор способа доставки уведомлений</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/hh465391.aspx">Руководство по всплывающим уведомлениям</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Планирование локальных уведомлений.</strong> <br><br>Локальные уведомления, отправляемые приложением в запланированное время.</td>
<td align="left">Уведомления и действия определяются с помощью класса <strong>NotificationCompat.Builder</strong> и могут быть запланированы и обрабатываться внутри приложения с использованием классов <strong>AlarmManager</strong> и <strong>BroadcastReceiver</strong>.</td>
<td align="left">Локальные уведомления создаются с помощью <strong>UILocalNotification</strong> и могут быть запланированы с использованием <b> UILocalNotification.scheduleLocalNotification:<strong>. | Пользователь может запланировать всплывающее уведомление с помощью </strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.notifications.scheduledtoastnotification.aspx">ScheduledToastNotification</a><strong>. Пользователь может отправить уведомление на плитке из приложения с помощью класса </strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.notifications.tilenotification.aspx">TileNotification</a><strong> или создать расписание уведомления на плитке с помощью <a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.notifications.scheduledtilenotification.aspx">ScheduledTileNotification</a>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt631604.aspx">Адаптивные и интерактивные всплывающие уведомления</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt593299.aspx">Отправка локального уведомления на плитке</a> | | </strong>Отправка push-уведомлений.</b> Уведомление, отправленное с сервера push-уведомлений и при необходимости обрабатываемое в приложении.</td>
<td align="left">Поддержку push-уведомлений в Android обеспечивает <strong>Google Cloud Messaging</strong>.</td>
</tr>
</tbody>
</table>
<h2 id="media-capture-and-rendering">Захват и отображение мультимедийного содержимого</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Захват мультимедиа.</strong> <br><br>Запись звука и визуального содержимого.</td>
<td align="left">Используя такое <strong>намерение</strong>, как MediaStore.ACTION_VIDEO_CAPTURE можно производить захват мультимедиа с помощью встроенного приложения камеры. Использование <strong>android.hardware.camera2</strong> или библиотеки <strong>камеры</strong> позволяет реализовать пользовательский интерфейс камеры. API <strong>MediaRecorder</strong> можно использовать для записи звука.</td>
<td align="left"><strong>UIImagePickerController</strong> обеспечивает захват видео и фотографий с помощью системного интерфейса. Классы <strong>AVFoundation</strong>, такие как <strong>AVCaptureSession</strong> обеспечивают прямой доступ к камере. <br/>Класс <strong>AVAudioRecorder</strong> обеспечивает запись звука.</td>
<td align="left">Можно захватывать фотографии и видео с использованием встроенного пользовательского интерфейса камеры с помощью <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.media.capture.cameracaptureui.aspx">класса CameraCaptureUI</a></strong>. Вы можете взаимодействовать с камерой на низком уровне и записывать звук с помощью классов пространства имен <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.media.capture.aspx">Windows.Media.Capture</a></strong>, таких как <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.media.capture.mediacapture.aspx">MediaCapture API</a></strong>. <br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt282142.aspx">Фото- и видеосъемка с помощью CameraCaptureUI</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt243896.aspx">Захват фотографий и видео с помощью MediaCapture</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Воспроизведение мультимедиа.</strong> <br><br>Воспроизведение звуковых и видеофайлов.</td>
<td align="left">Для воспроизведения звуковых и видеофайлов используются классы <strong>MediaPlayer</strong> и <strong>AudioManager</strong>.</td>
<td align="left">Для воспроизведения звуковых и видеофайлов используются <strong>платформа AVKit</strong>, <strong>AVAudioPlayer</strong> и <strong>Media Player Framework</strong>.</td>
<td align="left">Вы можете использовать классы <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.media.core.mediasource.aspx">MediaSource</a></strong>, <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.mediaelement.aspx">MediaElement</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.media.playback.mediaplayer.aspx">MediaPlayer</a></strong> для воспроизведения звука и видео из таких источников, как локальные и удаленные файлы.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt592657.aspx">Воспроизведение мультимедиа с помощью класса MediaSource</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Редактирование мультимедиа.</strong> <br><br>Составление новых файлов мультимедиа из существующих записей и применение специальных эффектов.</td>
<td align="left">Для редактирования содержимого можно использовать такие низкоуровневые классы, как <strong>MediaCodec</strong>, <strong>MediaMuxer</strong> и <strong>android.media.effect</strong>.</td>
<td align="left">Для редактирования содержимого можно использовать классы платформы <strong>AV Foundation</strong>, такие как <strong>AVMutableComposition</strong>, <strong>AVMutableVideoComposition</strong> и <strong>AVMutableAudioMix</strong>.</td>
<td align="left">Вы можете использовать API <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.media.editing.aspx">Windows.Media.Editing</a></strong>, такие как <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.media.editing.mediacomposition.aspx">MediaComposition</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.media.editing.mediaclip.aspx">MediaClip</a></strong> для создания композиций мультимедиа из звуковых и видеофайлов. Поддерживается добавление фото- и видеоналожений, группировка видеоклипов, добавление фоновых аудиодорожек и применение звуковых и видеоэффектов.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt204792.aspx">Создание и редактирование композиций мультимедиа</a></td>
</tr>
</tbody>
</table>
<h2 id="sensors">Датчики</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Датчики.</strong> <br><br>Отслеживание перемещения и положения устройства, а также свойств окружающей среды.</td>
<td align="left">Для доступа к аппаратным и программным датчикам используются такие классы <strong>платформы датчиков</strong>, как <strong>SensorManager</strong> и <strong>SensorEvent</strong>.</td>
<td align="left">Для доступа к необработанным и обработанным данным датчиков используется <strong>платформа Core Motion</strong>.</td>
<td align="left">Вы можете использовать классы пространства имен <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.devices.sensors.aspx">Windows.Devices.Sensors</a></strong> для доступа к показаниям датчиков и событиям, запускаемым при получении новых данных показаний от датчика.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt187358.aspx">Датчики</a></td>
</tr>
</tbody>
</table>
<h2 id="location-and-mapping">Местоположение и карты</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Расположение.</strong> <br><br>Определение <strong>текущего</strong> местоположения устройства и отслеживание <strong>изменений</strong>.</td>
<td align="left">API местоположения служб Google Play предоставляют высокоуровневый доступ к <strong>последнему известному местоположению</strong> с помощью <strong>единого определителя местоположения</strong> посредством методов <strong>getLastLocation</strong> и <strong>requestLocationUpdates</strong>. Низкоуровневый доступ предоставляется в библиотеках Android с помощью <strong>LocationManager</strong>.</td>
<td align="left">Для отслеживания местоположения устройства используются платформа <strong>Core Location</strong> и класс <strong>CLLocationManager</strong>. Метод <strong>startUpdatingLocation</strong> применяется для стандартного определения местоположения, <strong>startMonitoringSignificantLocationChanges</strong> — для определения <strong>значительных изменений</strong> местоположения.</td>
<td align="left">Вы можете отслеживать местоположение с помощью классов пространства имен <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.devices.geolocation.aspx">Windows.Devices.Geolocation</a></strong>. Используйте <strong><a href="https://msdn.microsoft.com/library/windows/apps/br225537.aspx">Geolocator.GetGeopositionAsync</a></strong> для однократного считывания. Используйте <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.devices.geolocation.geolocator.positionchanged.aspx">Geolocator.PositionChanged</a></strong> для регулярного получения сведений о расположении по таймеру или уведомлений об изменении местоположения.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219698.aspx">Получение местоположения пользователя</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Отображение карт.</strong> <br><br>Отображение <strong>интерактивной встроенной карты</strong> и добавление <strong>объектов на карту</strong>.</td>
<td align="left">Классы <strong>GoogleMap</strong>, <strong>MapFragment</strong> и <strong>MapView</strong> в <strong>API Карт Google в Android</strong> позволяют встраивать карты в приложения. Объекты могут отображаться на карте с помощью <strong>маркеров</strong> и настраиваемого класса <strong>Marker</strong>.</td>
<td align="left">Карты встраиваются в приложения iOS с помощью класса <strong>MKMapView</strong> <strong>платформы MapKit</strong>. К приложениям можно добавлять <strong>аннотации</strong> для отображения объектов на карте с помощью таких классов объектов, как <strong>MKPointAnnotation</strong>, и таких классов представлений, как <strong>MKPinAnnotationView</strong>.</td>
<td align="left">Вы можете внедрить в свое приложение карты с помощью встроенного элемента управления XAML <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.maps.mapcontrol.aspx">MapControl</a></strong>, обеспечивающего двумерные и трехмерные представления, а также представления Streetside. Можно добавлять объекты на карту с помощью вешек, изображений и фигур, используя классы <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.maps.mapicon.aspx">MapIcon</a></strong>, <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.maps.mappolygon.aspx">MapPolygon</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.maps.mappolyline.aspx">MapPolyline</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219695.aspx">Отображение карт с помощью двумерных и трехмерных представлений, а также с помощью представлений Streetside</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219696.aspx">Отображение объектов на карте</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Создание геозон.</strong> <br><br>Отслеживание входа и выхода из определенного географического региона.</td>
<td align="left">Геозоны отслеживаются с помощью <strong>служб определения местоположения</strong> в SDK служб Google Play.</td>
<td align="left">Регионы отслеживаются посредством класса <strong>CLCircularRegion</strong> и регистрируются с помощью метода <strong>CLLocationManager.startMonitoringForRegion:</strong>.</td>
<td align="left">Вы можете создавать геозоны с помощью класса <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.devices.geolocation.geofencing.geofence.aspx">Geofence</a></strong> и определять <strong>отслеживаемые состояния</strong>, такие как вход и выход из региона. Обработка геозон на переднем плане осуществляется с помощью класса <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.devices.geolocation.geofencing.geofencemonitor.aspx">GeofenceMonitor</a></strong>, а в фоновом режиме — с помощью фонового класса <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.background.locationtrigger.aspx">LocationTrigger background class</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219702.aspx">Настройка геозоны</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Геокодирование и обратное геокодирование.</strong> <br><br>Преобразование адресов в географические расположения (геокодирование) и географических расположений в адреса (обратное геокодирование).<br/></td>
<td align="left">Для геокодирования и обратного геокодирования используется класс <strong>Geocoder</strong>.</td>
<td align="left">Для геокодирования используется класс <strong>CLGeocoder</strong>.</td>
<td align="left">Вы можете выполнять геокодирование с помощью <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.services.maps.maplocationfinder.aspx">класса MapLocationFinder</a></strong> в пространстве имен <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.services.maps.aspx">Windows.Services.Maps</a></strong>. Для геокодирования используется <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.services.maps.maplocationfinder.findlocationsasync.aspx">FindLocationsAsync</a></strong>, а для обратного геокодирования — <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.services.maps.maplocationfinder.findlocationsatasync.aspx">FindLocationsAtAsync</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219697.aspx">Выполнение геокодирования и обратного геокодирования</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Маршруты и направления.</strong> <br><br>Предоставление маршрутов, расстояний и направлений между двумя географическими расположениями.</td>
<td align="left">Google предоставляет веб-службу <strong>Google Maps Directions API</strong>, которую можно использовать в Android, однако SDK не предоставляется.</td>
<td align="left">Платформа Map Kit предоставляет API <strong>MKDirections</strong>, которое можно использовать для получения информации о маршруте и направлениях.</td>
<td align="left">Вы можете сделать запрос пешеходного или автомобильного маршрута с помощью класса <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.services.maps.maproutefinder.aspx">MapRouteFinder</a></strong> в пространстве имен <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.services.maps.aspx">Windows.Services.Maps</a></strong>. Маршруты возвращаются в виде экземпляра <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.services.maps.maproute.aspx">MapRoute</a></strong>, который легко отобразить в MapControl. Направления возвращаются внутри объекта <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.services.maps.maproutemaneuver.aspx">MapRouteManeuver</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt219701.aspx">Отображение маршрутов и направлений на карте</a></td>
</tr>
</tbody>
</table>
<h2 id="app-to-app-communication">Связь между приложениями</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Вызов другого приложения.</strong> <br><br>Запуск другого приложения и, при необходимости, общий доступ к данным (ссылкам, тексту, фотографиям, видео и файлам).</td>
<td align="left">Для запуска другого приложения используется <strong>неявное намерение</strong> путем определения <strong>действия</strong> и необязательного определения данных в <strong>намерении</strong> и его вызова с помощью <strong>startActivityForResult</strong>.<br/></td>
<td align="left">Для предоставления доступа к данным приложения другому приложению можно использовать <strong>расширения приложений</strong>. <strong>Схемы URL-адресов</strong> дают возможность передавать URL-адрес в другое приложение.</td>
<td align="left">Вы можете запустить приложение с зарегистрированной схемой URI с помощью <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.system.launcher.launchuriasync.aspx">Launcher.LaunchUriAsync</a></strong> или использовать <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.system.launcher.launchuriforresultsasync.aspx">Launcher.LaunchUriForResultsAsync</a></strong> для запуска приложения для результатов и получения данных из запущенного приложения. Для передачи файла для обработки в другое приложение можно использовать метод <strong><a href="https://msdn.microsoft.com/library/windows/apps/hh701471.aspx">Launcher.LaunchFileAsync</a></strong>.<br/><br/>Для легкого обмена данными между приложениями используются <strong>контракты отправки данных</strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt228340.aspx">Запуск приложения по умолчанию для URI</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt269386.aspx">Запуск приложения для результатов</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt299102.aspx">Запуск приложения по умолчанию для файла</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt243293.aspx">Предоставление общего доступа к данным</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Разрешение вызова вашего приложения.</strong> <br><br>Возможность ответа вашего приложения на запрос из другого приложения.</td>
<td align="left">Приложения регистрируют <strong>активность обработки намерений</strong> с <strong>фильтром намерений</strong> для ответа на неявные намерения других приложений.</td>
<td align="left">Добавление в пакет <strong>расширения приложения</strong> позволяет обмениваться данными с другими приложениями. Приложения могут регистрировать <strong>настраиваемую схему URL-адресов</strong> с помощью ключа <strong>CFBundleURLTypes</strong> в файле Info.plist.</td>
<td align="left">Вы можете зарегистрировать приложение в качестве обработчика по умолчанию для <strong>имени схемы URI</strong>, зарегистрировав <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.activation.activationkind.aspx#Protocol">протокол</a></strong> в манифесте пакета и обновив обработчик событий <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onactivated.aspx">Application.OnActivated</a></strong> для дополнительного возврата результатов. Аналогичным образом вы можете зарегистрировать приложение в качестве обработчика по умолчанию для определенных типов файлов путем добавления объявления в манифест пакета и обработки события <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.application.onfileactivated.aspx">Application.OnFileActivated</a></strong>.<br/><br/>Вы можете обрабатывать запросы контракта отправки данных, зарегистрировав приложение в манифесте в качестве получателя данных и обрабатывая событие <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.application.onsharetargetactivated.aspx">Application.OnShareTargetActivated</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt269386.aspx">Запуск приложения для результатов</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/mt269385.aspx">Обработка активации файла</a><br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt243292.aspx">Получение данных</a></td>
</tr>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Копирование и вставка.</strong> <br><br>Копирование и вставка текста и другого содержимого между приложениями.</td>
<td align="left">Для реализации операций копирования и вставки можно использовать классы <strong>ClipboardManager</strong> и <strong>ClipData</strong> <strong>платформы буфера обмена</strong>.</td>
<td align="left">Для реализации операций копирования и вставки можно использовать <strong>UIPasteboard</strong>, <strong>UIMenuController</strong> и <strong>UIResponderStandardEditActions</strong>.</td>
<td align="left">Многие элементы управления XAML по умолчанию поддерживают копирование и вставку. Вы можете реализовать поддержку копирования и вставки самостоятельно с помощью классов <strong><a href="https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datapackage.aspx">DataPackage</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.applicationmodel.datatransfer.clipboard.aspx">Clipboard</a></strong> в пространстве имен <strong><a href="https://msdn.microsoft.com/library/windows/apps/br205967">Windows.ApplicationModel.DataTransfer</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt243291.aspx">Копирование и вставка</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Перетаскивание.</strong> <br><br>Перетаскивание содержимого между приложениями.</td>
<td align="left">Перетаскивание можно реализовать в рамках одного приложения с помощью платформы <strong>Android drag/drop framework</strong>.</td>
<td align="left">Высокоуровневые API перетаскивания в iOS не предоставляются.</td>
<td align="left">Вы можете реализовать возможности перетаскивания между приложениями UWP, из классического приложения в приложение UWP и из приложения UWP в классическое приложение. Поддержка перетаскивания реализовывается в классе UIElement с помощью свойств <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.allowdrop.aspx">AllowDrop</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.candrag.aspx">CanDrag</a></strong> и событий <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.dragover.aspx">DragOver</a></strong> и <strong><a href="https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.drop.aspx">Drop</a></strong>.<br/><br/><a href="https://msdn.microsoft.com/library/windows/apps/xaml/mt227651.aspx">Перетаскивание</a></td>
</tr>
</tbody>
</table>
<h2 id="software-design">Проектирование программного обеспечения</h2>
<table style="width:100%">
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="40%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"><strong>Универсальное понятие</strong></th>
<th align="left"><strong>Android</strong></th>
<th align="left"><strong>iOS</strong></th>
<th align="left"><strong>Windows 10 UWP</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd" style="background-color: #f2f2f2">
<td align="left"><strong>Шаблоны проектирования программного обеспечения.</strong> <br><br>Рекомендуемые для платформы шаблоны проектирования.</td>
<td align="left">В Android отсутствуют рекомендованные или предоставленные формальные шаблоны, однако бета-версия платформы привязки данных может обеспечить более широкое использование шаблона <strong>Model-View-ViewModel (MVVM)</strong>. В ряде сторонних статей и платформ рекомендуется использовать подходы <strong>model-view-presenter (MVP)</strong> и <strong>MVVM</strong>.</td>
<td align="left"><strong>Model-View-Controller (MVC)</strong>— распространенный шаблон, используемый в iOS и интегрированный в платформу.</td>
<td align="left">При создании приложений UWP отсутствует ограничение по использованию определенного шаблона.<br/><br/>Вы можете использовать встроенный шаблон <a href="https://msdn.microsoft.com/library/windows/apps/mt210947.aspx">привязки данных</a> для обеспечения четкого разделения задач, связанных с данными, и функций пользовательского интерфейса. При этом отсутствует необходимость в создании кода обработчиков событий пользовательского интерфейса, обновляющих значения свойств.<br/><br/>Вы можете расширить привязку данных в соответствии с шаблоном <strong>Model-View-ViewModel (MVVM)</strong> с помощью сторонних библиотек MVVM, таких как <a href="https://mvvmlight.codeplex.com/">MVVM Light Toolkit</a>, либо самостоятельно, отделив логику от кода программной части.<br/><br/><a href="https://msdn.microsoft.com/library/hh848246.aspx">Шаблон MVVM</a><br/><br/><a href="https://github.com/Windows-XAML/Template10/wiki">Шаблоны проектов Visual Studio Template 10</a></td>
</tr>
</tbody>
</table>
