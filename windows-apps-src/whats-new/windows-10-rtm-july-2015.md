---
author: QuinnRadich
Description: Windows 10 и новые средства разработчика предоставляют инструменты, функции и возможности на основе новой универсальной платформы Windows (UWP).
title: Новые возможности для разработчиков в Windows 10, версия RTM, июль 2015 г.
---

# Новые возможности для разработчиков в Windows 10, версия RTM, июль 2015 г.


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

Windows 10 и новые средства разработчика предоставляют инструменты, функции и возможности на основе новой универсальной платформы Windows (UWP). [Установив средства и пакет SDK](https://dev.windows.com/downloads) в Windows 10, вы можете сразу приступить либо к [созданию нового проекта универсального приложения для Windows](https://msdn.microsoft.com/library/windows/apps/bg124288) либо к использованию [существующего кода приложения в ОС Windows](https://msdn.microsoft.com/library/windows/apps/mt238321).

Ниже мы рассмотрим сгруппированные по функциям новые возможности для разработчиков в Windows 10, версия RTM.

## Адаптивные макеты


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Несколько представлений для адаптированного содержимого</td>
<td align="left">XAML позволяет определять адаптированные представления (XAML-файлы), которые используют один и тот же файл кода. Это упрощает создание и поддержку различных представлений, адаптированных для определенного семейства устройств или сценария. Если ваше приложение имеет своеобразный пользовательский интерфейс, макет или модели навигации, которые существенно отличаются в зависимости от разных сценариев, создавайте несколько представлений. Например, вы можете задействовать [<strong>сводную таблицу</strong>](https://msdn.microsoft.com/library/windows/apps/dn608241) со средствами навигации, оптимизированными для работы одной рукой, в приложении для мобильных устройств и [<strong>SplitView</strong>](https://msdn.microsoft.com/library/windows/apps/dn864360) с меню навигации, оптимизированным для работы мышью, в классическом приложении.</td>
</tr>
<tr class="even">
<td align="left">Триггеры состояния</td>
<td align="left">С помощью новой функции [<strong>VisualState.StateTriggers</strong>](https://msdn.microsoft.com/library/windows/apps/dn890396) можно определять свойства в зависимости от условий, таких как высота и ширина окна, а также в зависимости от пользовательского триггера. Ранее в коде было необходимо обрабатывать события окна [<strong>SizeChanged</strong>](https://msdn.microsoft.com/library/windows/apps/br209055) и вызывать [<strong>VisualStateManager.GoToState</strong>](https://msdn.microsoft.com/library/windows/apps/br209025).</td>
</tr>
<tr class="odd">
<td align="left">Задающие методы</td>
<td align="left"><p>Благодаря новому синтаксису [<strong>VisualState.Setters</strong>](https://msdn.microsoft.com/library/windows/apps/dn890395) можно использовать упрощенную разметку для определения изменений свойств в [<strong>VisualStateManager</strong>](https://msdn.microsoft.com/library/windows/apps/br209021). Ранее было необходимо использовать раскадровку и создавать анимации для изменения свойств, например изменения ориентации [<strong>StackPanel</strong>](https://msdn.microsoft.com/library/windows/apps/br209635) с горизонтальной на вертикальную. В универсальных приложениях для Windows можно использовать упрощенный синтаксис метода задания:</p>
<p><code>&lt;setter target=&quot;stackPanel1.Orientation&quot; value=&quot;Vertical&quot; /&gt;</code></p></td>
</tr>
</tbody>
</table>

 

## Возможности XAML


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Скомпилированные привязки данных (x:Bind)</td>
<td align="left"><p>В универсальных приложениях для Windows можно использовать новый механизм привязки на основе компилятора, который включается свойством x:Bind. Привязки на основе компилятора являются строго типизированными и обрабатываются во время компиляции, что является более быстрым способом и позволяет выявлять ошибки во время компиляции, если типы привязки не совпадают. Так как привязки преобразуются в компилированный код приложения, теперь можно выполнять отладку привязок путем перебора кода в Visual Studio для диагностики определенных проблем, связанных с привязками. x:Bind можно также использовать для привязки к методу:</p>
<p><code>&lt;textblock text=&quot;{x:Bind Customer.Address.ToString()}&quot; /&gt;</code></p>
<p>В типичных сценариях привязки можно использовать x:Bind вместо Binding для улучшения производительности и удобства поддержки.</p></td>
</tr>
<tr class="even">
<td align="left">Декларативная добавочная отрисовка списков (x:Phase)</td>
<td align="left"><p>В универсальных приложениях для Windows новый атрибут x:Phase позволяет выполнять добавочную или поэтапную отрисовку списков при помощи XAML, а не кода. При прокрутке длинных списков, содержащих сложные элементы, ваше приложение может не отрисовывать элементы достаточно быстро в соответствии со скоростью прокрутки, что ухудшает процесс взаимодействия пользователя с вашим приложением. Поэтапная отрисовка позволяет указать приоритет отрисовки отдельных элементов пункта списка, так что в сценариях быстрой прокрутки будут отрисовываться только важные части элемента списка. Это обеспечивает более плавную прокрутку при взаимодействии с пользователем.</p>
<p>В Windows 8.1 можно было обрабатывать событие [<strong>ContainerContentChanging</strong>](https://msdn.microsoft.com/library/windows/apps/dn298914) и писать код для поэтапной отрисовки элементов списка. В приложениях UWP можно декларативно выполнять поэтапную отрисовку с помощью атрибута x:Phase. При использовании вместе с компилированными привязками x:Bind атрибут x:Phase позволяет без труда определять приоритет отрисовки каждого привязанного элемента в шаблоне данных. При прокрутке отрисовка элементов разделяется по времени в зависимости от этапа, что позволяет отрисовывать элемент по частям.</p></td>
</tr>
<tr class="odd">
<td align="left">Отложенная загрузка элементов пользовательского интерфейса (x:DeferLoadingStrategy)</td>
<td align="left">В универсальных приложениях для Windows новая директива x:DeferLoadingStrategy позволяет откладывать загрузку определенных частей пользовательского интерфейса, что повышает производительность при запуске и сокращает объем задействованной памяти в приложении. Например, если пользовательский интерфейс вашего приложения содержит элемент для подтверждения данных, который отображается только при вводе неправильных данных, вы можете отложить его загрузку до того времени, когда он понадобится. В этом случае элементы-объекты создаются не при загрузке страницы, а только когда появляется ошибка данных и возникает необходимость добавить эти элементы в визуальное дерево страницы.</td>
</tr>
<tr class="even">
<td align="left">SplitView</td>
<td align="left">Новый элемент управления [<strong>SplitView</strong>](https://msdn.microsoft.com/library/windows/apps/dn864360) позволяет без труда показывать и скрывать промежуточное содержимое. Как правило, он используется для сценариев навигации верхнего уровня, например для &quot;кнопки-гамбургера&quot;, где средства навигации скрыты, но при необходимости отображаются в ответ на действия пользователя.</td>
</tr>
<tr class="odd">
<td align="left">RelativePanel</td>
<td align="left">[<strong>RelativePanel</strong>](https://msdn.microsoft.com/library/windows/apps/dn879546) — это новая панель макета, которая позволяет располагать и выравнивать дочерние объекты относительно друг друга или родительской панели. Например, можно указать, что некоторый текст всегда должен располагаться с левой стороны панели, а кнопка всегда должна находиться под текстом. Используйте <strong>RelativePanel</strong> при создании пользовательских интерфейсов, которые не имеют четкого линейного шаблона, который вызывал бы [<strong>StackPanel</strong>](https://msdn.microsoft.com/library/windows/apps/br209635) или [<strong>Grid</strong>](https://msdn.microsoft.com/library/windows/apps/br242704).</td>
</tr>
<tr class="even">
<td align="left">CalendarView</td>
<td align="left">Элемент управления [<strong>CalendarView</strong>](https://msdn.microsoft.com/library/windows/apps/dn890052) упрощает просмотр и выбор дат и их диапазонов с помощью настраиваемого представления на основе месяцев. <strong>CalendarView</strong> поддерживает такие функции, как минимальные, максимальные и закрытые даты в целях ограничения доступных для выбора дат. Вы также можете задать пользовательские шкалы плотности, которые используются для отображения общей &quot;заполненности&quot; расписания на определенный день.</td>
</tr>
<tr class="odd">
<td align="left">CalendarDatePicker</td>
<td align="left">[<strong>CalendarDatePicker</strong>](https://msdn.microsoft.com/library/windows/apps/dn950083) — это раскрывающийся элемент управления, оптимизированный для выбора отдельной даты из [<strong>CalendarView</strong>](https://msdn.microsoft.com/library/windows/apps/dn890052), когда важен контекст, например день недели или заполненность календаря. Он аналогичен элементу управления [<strong>DatePicker</strong>](https://msdn.microsoft.com/library/windows/apps/dn298584), но <strong>DatePicker</strong> оптимизирован для выбора известной даты, например дня рождения.</td>
</tr>
<tr class="even">
<td align="left">MediaTransportControls</td>
<td align="left">Новый класс [<strong>MediaTransportControls</strong>](https://msdn.microsoft.com/library/windows/apps/dn831962) упрощает настройку элементов управления транспортировкой [<strong>MediaElement</strong>](https://msdn.microsoft.com/library/windows/apps/br242926). В Windows 8.1 вы можете включить встроенные элементы управления транспортировкой <strong>MediaElement</strong> или создать собственные элементы управления транспортировкой, вызывающие методы <strong>MediaElement</strong>. Теперь можно использовать встроенные функциональные возможности элементов управления <strong>MediaTransportControls</strong> и без труда настраивать внешний вид вашего приложения.</td>
</tr>
<tr class="odd">
<td align="left">Уведомления об изменении свойств</td>
<td align="left"><p>В универсальных приложениях для Windows можно прослушивать изменения свойств в объектах [<strong>DependencyObject</strong>](https://msdn.microsoft.com/library/windows/apps/br242356), в том числе тех, у которых нет соответствующих событий изменения.</p>
<p>Уведомление используется как событие, при этом фактически представляется как обратный вызов. Обратный вызов использует аргумент отправителя так же, как обработчик событий, но при этом не использует аргумент события. Вместо этого передается только идентификатор свойства для обозначения конкретного свойства. На основе этой информации ваше приложение может определить единый обработчик для нескольких уведомлений о свойствах. Дополнительные сведения см. в разделах, посвященных [<strong>RegisterPropertyChangedCallback</strong>](https://msdn.microsoft.com/library/windows/apps/dn831902) и [<strong>UnregisterPropertyChangedCallback</strong>](https://msdn.microsoft.com/library/windows/apps/dn831910).</p></td>
</tr>
<tr class="even">
<td align="left">Карты</td>
<td align="left"><p>Класс [<strong>MapControl</strong>](https://msdn.microsoft.com/library/windows/apps/dn637004) был обновлен, чтобы обеспечить трехмерное представление с воздуха и виды на уровне улиц. Эти новые функции и более ранние функции карт теперь доступны в универсальных приложениях для Windows. Добавьте карты в свое приложение с помощью следующих API:</p>
<ul>
<li>пространство имен [<strong>Windows.UI.Xaml.Controls.Maps</strong>](https://msdn.microsoft.com/library/windows/apps/dn610751) — отображение карт;</li>
<li>пространство имен [<strong>Windows.Services.Maps</strong>](https://msdn.microsoft.com/library/windows/apps/dn636979) — поиск расположения и маршрутов.</li>
</ul>
<p>Для запуска с помощью этих API в универсальном приложении для Windows сегодня запросите ключ из [центра разработки Карт Bing](https://www.bingmapsportal.com/). Подробнее см. в разделе о [проверке подлинности приложения, использующего карты](https://msdn.microsoft.com/library/windows/apps/xaml/dn741528). Еще одной новинкой для пользователей Windows 10, ПК и телефонов является возможность загрузки карт для автономного использования из приложения "Параметры". При соответствующей возможности автономные карты используются элементом [<strong>MapControl</strong>](https://msdn.microsoft.com/library/windows/apps/dn637004) для отображения карт при отсутствии доступа к Интернету.</p></td>
</tr>
<tr class="odd">
<td align="left">Сопоставление кнопки ввода</td>
<td align="left">Класс [<strong>Windows.UI.Xaml.Input.KeyRoutedEventArgs</strong>](https://msdn.microsoft.com/library/windows/apps/hh943072) имеет новое свойство [<strong>OriginalKey</strong>](https://msdn.microsoft.com/library/windows/apps/dn960168), которое наряду с соответствующим обновлением [<strong>Windows.System.VirtualKey</strong>](https://msdn.microsoft.com/library/windows/apps/br241812) позволяет получить оригинальную несопоставленную кнопку ввода, связанную с событием ввода с клавиатуры.</td>
</tr>
<tr class="even">
<td align="left">Рукописный ввод</td>
<td align="left"><p>В приложениях среды выполнения Windows, использующих язык C++, C# или Visual Basic, теперь можно с удобством применять широкие возможности рукописного ввода благодаря элементу управления [<strong>InkCanvas</strong>](https://msdn.microsoft.com/library/windows/apps/dn858535) и основным классам [<strong>InkPresenter</strong>](https://msdn.microsoft.com/library/windows/apps/dn922011).</p>
<p>Элемент управления [<strong>InkCanvas</strong>](https://msdn.microsoft.com/library/windows/apps/dn858535) определяет область наложения для начертания и воспроизведения росчерков пера. Функциональные возможности этого элемента управления (ввод, обработка, воспроизведение) основаны на классах [<strong>InkPresenter</strong>](https://msdn.microsoft.com/library/windows/apps/dn922011), [<strong>InkStroke</strong>](https://msdn.microsoft.com/library/windows/apps/br208485), [<strong>InkRecognizer</strong>](https://msdn.microsoft.com/library/windows/apps/br208478) и [<strong>InkSynchronizer</strong>](https://msdn.microsoft.com/library/windows/apps/dn903979).</p>
<p></p>
<div class="alert">
<strong>Внимание!</strong>  Эти классы не поддерживаются в приложениях для Windows, использующих JavaScript.
</div>
<div>
 
</div></td>
</tr>
</tbody>
</table>

 

## Обновленные функции XAML


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Обновления CommandBar и AppBar</td>
<td align="left"><p>Элементы управления [<strong>CommandBar</strong>](https://msdn.microsoft.com/library/windows/apps/dn279427) и [<strong>AppBar</strong>](https://msdn.microsoft.com/library/windows/apps/hh701927) были обновлены для соответствия интерфейса API, поведения и удобства использования в приложениях UWP во всех семействах устройств.</p>
<p>Элемент управления [<strong>CommandBar</strong>](https://msdn.microsoft.com/library/windows/apps/dn279427) для универсальных приложений для Windows был улучшен и теперь предоставляет супермножество функциональных возможностей [<strong>AppBar</strong>](https://msdn.microsoft.com/library/windows/apps/hh701927) и большую гибкость при использовании приложения. Необходимо использовать <strong>CommandBar</strong> для всех новых универсальных приложений для Windows 10. В <strong>CommandBar</strong> для Windows 8.1 можно было использовать только те элементы управления, которые реализовывали [<strong>ICommandBarElement</strong>](https://msdn.microsoft.com/library/windows/apps/dn251969), например [<strong>AppBarButton</strong>](https://msdn.microsoft.com/library/windows/apps/dn279244). В универсальных приложениях для Windows теперь в <strong>CommandBar</strong>, как и в кнопки <strong>AppBarButton</strong>, можно помещать пользовательское содержимое.</p>
<p>Элемент управления [<strong>AppBar</strong>](https://msdn.microsoft.com/library/windows/apps/hh701927) был обновлен, что позволяет без труда перевести ваши приложения Windows 8.1, которые используют <strong>AppBar</strong>, на платформу универсальных приложений для Windows. <strong>AppBar</strong> предназначается для использования в полноэкранных приложениях и вызова с помощью краевых жестов. Обновления этого элемента управления решают проблемы, связанные с оконными приложениями и отсутствием краевых жестов в Windows 10.</p>
<p>Скрытый режим [<strong>AppBar.ClosedDisplayMode</strong>](https://msdn.microsoft.com/library/windows/apps/dn633872), ранее присутствовавший только в Windows Phone, теперь поддерживается всеми семействами устройств, что позволяет перемещаться между различными уровнями подсказок для команд. По умолчанию [<strong>AppBar</strong>](https://msdn.microsoft.com/library/windows/apps/hh701927) отображает минимальную подсказку, обеспечивая согласованность при обновлении приложений Windows 8.1 до универсальных приложений для Windows, когда больше нет возможности использовать поддержку платформой краевых жестов.</p>
<p><strong>Новый интерфейс API </strong> [<strong>AppBar</strong>](https://msdn.microsoft.com/library/windows/apps/hh701927) <strong>:</strong>[<strong>Closing</strong>](https://msdn.microsoft.com/library/windows/apps/dn996483), [<strong>OnClosing</strong>](https://msdn.microsoft.com/library/windows/apps/dn996484), [<strong>Opening</strong>](https://msdn.microsoft.com/library/windows/apps/dn996486), [<strong>OnOpening</strong>](https://msdn.microsoft.com/library/windows/apps/dn996485), [<strong>TemplateSettings</strong>](https://msdn.microsoft.com/library/windows/apps/dn996487).</p>
<p><strong>Новый интерфейс API </strong> [<strong>CommandBar</strong>](https://msdn.microsoft.com/library/windows/apps/dn279427) <strong>:</strong>[<strong>CommandBarOverflowPresenterStyle</strong>](https://msdn.microsoft.com/library/windows/apps/dn975227) и [<strong>CommandBarOverflowPresenter</strong>](https://msdn.microsoft.com/library/windows/apps/dn975225).</p></td>
</tr>
<tr class="even">
<td align="left">Обновления GridView</td>
<td align="left">До выхода Windows 10 ориентация макета [<strong>GridView</strong>](https://msdn.microsoft.com/library/windows/apps/br242705) по умолчанию была горизонтальной в Windows и вертикальной в Windows Phone. В приложениях UWP <strong>GridView</strong> использует по умолчанию вертикальный макет для всех семейств устройств, что обеспечивает согласованность представления.</td>
</tr>
<tr class="odd">
<td align="left">Свойство AreStickyGroupHeadersEnabled</td>
<td align="left">При отображении сгруппированных данных в элементах управления [<strong>ListView</strong>](https://msdn.microsoft.com/library/windows/apps/br242878) или [<strong>GridView</strong>](https://msdn.microsoft.com/library/windows/apps/br242705) заголовки группы отображаются при прокрутке списка. Это важно при работе с большими наборами данных, когда заголовок является контекстом для просматриваемых пользователем данных. Однако в случае наличия в каждой группе только нескольких элементов, может понадобиться убирать с экрана заголовки вместе с элементами при прокрутке. Для управления этим поведением можно задать свойство [<strong>AreStickyGroupHeadersEnabled</strong>](https://msdn.microsoft.com/library/windows/apps/dn932028) для [<strong>ItemsStackPanel</strong>](https://msdn.microsoft.com/library/windows/apps/dn298795) и [<strong>ItemsWrapGrid</strong>](https://msdn.microsoft.com/library/windows/apps/dn298849).</td>
</tr>
<tr class="even">
<td align="left">Метод GroupHeaderContainerFromItemContainer</td>
<td align="left">При отображении сгруппированных данных в [<strong>ItemsControl</strong>](https://msdn.microsoft.com/library/windows/apps/br242803) можно вызвать метод [<strong>GroupHeaderContainerFromItemContainer</strong>](https://msdn.microsoft.com/library/windows/apps/dn903984) для получения ссылки на родительский заголовок группы. Например, если пользователь удаляет последний элемент в группе, можно получить ссылку на заголовок группы и удалить элемент вместе с заголовком группы.</td>
</tr>
<tr class="odd">
<td align="left">Событие ChoosingGroupHeaderContainer</td>
<td align="left">Новое событие [<strong>ChoosingGroupHeaderContainer</strong>](https://msdn.microsoft.com/library/windows/apps/dn899082) в [<strong>ListViewBase</strong>](https://msdn.microsoft.com/library/windows/apps/br242879) позволяет определять состояние заголовков группы в элементе управления [<strong>ListView</strong>](https://msdn.microsoft.com/library/windows/apps/br242878) или [<strong>GridView</strong>](https://msdn.microsoft.com/library/windows/apps/br242705). Например, это событие можно обработать для установки свойства [<strong>AutomationProperties.NameProperty</strong>](https://msdn.microsoft.com/library/windows/apps/br209099) для заголовка группы, чтобы представлять эту группу в специальных возможностях.</td>
</tr>
<tr class="even">
<td align="left">Событие ChoosingItemContainer</td>
<td align="left">Новое событие [<strong>ChoosingItemContainer</strong>](https://msdn.microsoft.com/library/windows/apps/dn903989) для [<strong>ListViewBase</strong>](https://msdn.microsoft.com/library/windows/apps/br242879) предоставляет более широкие возможности управления виртуализацией пользовательского интерфейса в [<strong>ListView</strong>](https://msdn.microsoft.com/library/windows/apps/br242878) или [<strong>GridView</strong>](https://msdn.microsoft.com/library/windows/apps/br242705). Используйте это событие вместе с событием [<strong>ContainerContentChanging</strong>](https://msdn.microsoft.com/library/windows/apps/dn298914) для поддержания собственной очереди повторно использованных контейнеров, которые могут использоваться по мере необходимости. Например, если источник данных был сброшен вследствие фильтрации, можно быстро сопоставить уже существующий набор визуальных элементов (контейнеры ItemContainer) с соответствующими данными, достигая тем самым оптимальной производительности.</td>
</tr>
<tr class="odd">
<td align="left">Виртуализация прокрутки списка</td>
<td align="left"><p>В элементы управления XAML [<strong>ListView</strong>](https://msdn.microsoft.com/library/windows/apps/br242878) и [<strong>GridView</strong>](https://msdn.microsoft.com/library/windows/apps/br242705) добавлено новое событие [<strong>ListViewBase.ChoosingItemContainer</strong>](https://msdn.microsoft.com/library/windows/apps/dn903989), позволяющее улучшить работу элемента управления в случае изменения набора данных.</p>
<p>Вместо полной перезагрузки списка, при которой воспроизводится анимационный эффект открытия, система теперь сохраняет текущее представление, а также состояния фокусирования и выбора, а новые и удаленные элементы плавно добавляются в окно просмотра или удаляются из него с соответствующим анимационным эффектом. После того как в наборе данных происходит изменение (но при этом контейнеры в нем сохраняются), приложение может быстро сопоставить все &quot;старые&quot; элементы с содержимым предыдущего контейнера и пропустить дальнейшие этапы обработки методов с перезаписью жизненного цикла контейнера. Только &quot;новые&quot; элементы обрабатываются и привязываются к повторно используемым или новым контейнерам.</p></td>
</tr>
<tr class="even">
<td align="left">Метод SelectRange и свойство SelectedRanges</td>
<td align="left">В универсальных приложениях для Windows элементы управления [<strong>ListView</strong>](https://msdn.microsoft.com/library/windows/apps/br242878) и [<strong>GridView</strong>](https://msdn.microsoft.com/library/windows/apps/br242705) теперь позволяют выбрать элементы на основе диапазона индексов элементов, а не ссылок на объекты элементов. Это более эффективный способ описания выбора элемента, так как объекты элементов не должны создаваться для каждого выбранного элемента. Дополнительные сведения см. в разделах, посвященных [<strong>ListViewBase.SelectedRanges</strong>](https://msdn.microsoft.com/library/windows/apps/dn904244), [<strong>ListViewBase.SelectRange</strong>](https://msdn.microsoft.com/library/windows/apps/dn904245) и [<strong>ListViewBase.DeselectRange</strong>](https://msdn.microsoft.com/library/windows/apps/dn904241).</td>
</tr>
<tr class="odd">
<td align="left">Новые интерфейсы API ListViewItemPresenter</td>
<td align="left">[<strong>ListView</strong>](https://msdn.microsoft.com/library/windows/apps/br242878) и [<strong>GridView</strong>](https://msdn.microsoft.com/library/windows/apps/br242705) используют заместители элементов для предоставления визуальных элементов для выбора и фокусировки по умолчанию. В приложениях UWP [<strong>ListViewItemPresenter</strong>](https://msdn.microsoft.com/library/windows/apps/dn298500) и [<strong>GridViewItemPresenter</strong>](https://msdn.microsoft.com/library/windows/apps/dn279298) имеют новые свойства, которые позволяют в большей мере настраивать визуальные элементы списка. Новые свойства: [<strong>CheckBoxBrush</strong>](https://msdn.microsoft.com/library/windows/apps/dn913905), [<strong>CheckMode</strong>](https://msdn.microsoft.com/library/windows/apps/dn913923), [<strong>FocusSecondaryBorderBrush</strong>](https://msdn.microsoft.com/library/windows/apps/dn898370), [<strong>PointerOverForeground</strong>](https://msdn.microsoft.com/library/windows/apps/dn898372), [<strong>PressedBackground</strong>](https://msdn.microsoft.com/library/windows/apps/dn913931) и [<strong>SelectedPressedBackground</strong>](https://msdn.microsoft.com/library/windows/apps/dn913937).</td>
</tr>
<tr class="even">
<td align="left">Обновления SemanticZoom</td>
<td align="left"><p>Элемент управления [<strong>SemanticZoom</strong>](https://msdn.microsoft.com/library/windows/apps/hh702601) теперь имеет единое согласованное поведение в приложениях UWP по всем семействам устройств.</p>
<p>Действием по умолчанию для переключения между увеличенным представлением и уменьшенным представлением является нажатие заголовка группы в увеличенном представлении. Это не отличается от поведения в Windows Phone 8.1, но отличается от поведения в Windows Phone 8.1, где необходимо было использовать жест сжатия для изменения масштаба. Чтобы менять представление с помощью жестов сжатия, задайте значение [<strong>ScrollViewer.ZoomMode</strong>](https://msdn.microsoft.com/library/windows/apps/br209601)=&quot;Enabled&quot; для внутреннего объекта [<strong>ScrollViewer</strong>](https://msdn.microsoft.com/library/windows/apps/br209527) в [<strong>SemanticZoom</strong>](https://msdn.microsoft.com/library/windows/apps/hh702601).</p>
<p>В универсальных приложениях для Windows уменьшенное представление заменяет увеличенное представление и не отличается по размерам от заменяемого представления. Это же поведение свойственно Windows 8.1, однако отличается от Windows Phone 8.1, где уменьшенное представление занимало весь экран и отрисовывалось поверх всего прочего содержимого.</p></td>
</tr>
<tr class="odd">
<td align="left">Обновления DatePicker и TimePicker</td>
<td align="left">Теперь элементы управления [<strong>DatePicker</strong>](https://msdn.microsoft.com/library/windows/apps/dn298584) и [<strong>TimePicker</strong>](https://msdn.microsoft.com/library/windows/apps/dn299280) реализуются согласованно для универсальных приложений для Windows на всех семействах устройств. Кроме того, в Windows 10 они выглядят по-новому. Всплывающая часть теперь использует элементы управления [<strong>DatePickerFlyout</strong>](https://msdn.microsoft.com/library/windows/apps/dn625013) и [<strong>TimePickerFlyout</strong>](https://msdn.microsoft.com/library/windows/apps/dn608313) на всех устройствах. Это не отличается от поведения в Windows Phone 8.1, но отличается от поведения в Windows 8.1, где применялись элементы управления [<strong>ComboBox</strong>](https://msdn.microsoft.com/library/windows/apps/br209348). Использование элементов управления "Всплывающий элемент" позволяет без труда создавать настраиваемые элементы выбора даты и времени.</td>
</tr>
<tr class="even">
<td align="left">Новые интерфейсы API ScrollViewer</td>
<td align="left">[<strong>ScrollViewer</strong>](https://msdn.microsoft.com/library/windows/apps/br209527) имеет новые события [<strong>DirectManipulationStarted</strong>](https://msdn.microsoft.com/library/windows/apps/dn858544) и [<strong>DirectManipulationCompleted</strong>](https://msdn.microsoft.com/library/windows/apps/dn858543), которые используются для уведомления приложения о начале и завершении прокрутки касанием. Эти события можно обрабатывать для согласования пользовательского интерфейса с действиями пользователя.</td>
</tr>
<tr class="odd">
<td align="left">Обновления MenuFlyout</td>
<td align="left">В универсальных приложениях для Windows появились новые интерфейсы API, которые позволяют без труда создавать улучшенные контекстные меню. Новый метод [<strong>MenuFlyout.ShowAt</strong>](https://msdn.microsoft.com/library/windows/apps/dn913174) позволяет указывать, где должен отображаться всплывающий элемент относительно другого элемента. (Элемент [<strong>MenuFlyout</strong>](https://msdn.microsoft.com/library/windows/apps/dn299030) может даже перекрывать границы окна приложения.) Используйте новый класс [<strong>MenuFlyoutSubItem</strong>](https://msdn.microsoft.com/library/windows/apps/dn913170) для создания каскадных меню.</td>
</tr>
<tr class="even">
<td align="left">Новые свойства Border для ContentPresenter, Grid и StackPanel</td>
<td align="left">Общие элементы управления контейнерами имеют новые свойства границы, которые позволяют рисовать вокруг них границу без добавления дополнительного элемента Border в XAML. [<strong>ContentPresenter</strong>](https://msdn.microsoft.com/library/windows/apps/br209378), [<strong>Grid</strong>](https://msdn.microsoft.com/library/windows/apps/br242704) и [<strong>StackPanel</strong>](https://msdn.microsoft.com/library/windows/apps/br209635) имеют следующие новые свойства: [<strong>BorderBrush</strong>](https://msdn.microsoft.com/library/windows/apps/dn960179), [<strong>BorderThickness</strong>](https://msdn.microsoft.com/library/windows/apps/dn960181), [<strong>CornerRadius</strong>](https://msdn.microsoft.com/library/windows/apps/dn960183) и [<strong>Padding</strong>](https://msdn.microsoft.com/library/windows/apps/dn960187).</td>
</tr>
<tr class="odd">
<td align="left">Новые интерфейсы API текста в ContentPresenter</td>
<td align="left">[<strong>ContentPresenter</strong>](https://msdn.microsoft.com/library/windows/apps/br209378) имеет новые интерфейсы API, которые предоставляют вам больший контроль над отображением текста: [<strong>LineHeight</strong>](https://msdn.microsoft.com/library/windows/apps/dn903982), [<strong>LineStackingStrategy</strong>](https://msdn.microsoft.com/library/windows/apps/dn831933), [<strong>MaxLines</strong>](https://msdn.microsoft.com/library/windows/apps/dn831935) и [<strong>TextWrapping</strong>](https://msdn.microsoft.com/library/windows/apps/dn831937).</td>
</tr>
<tr class="even">
<td align="left">Визуальные элементы фокуса системы</td>
<td align="left">Визуальные элементы фокуса для элементов управления XAML теперь создаются системой, а не объявляются в качестве элементов XAML в шаблоне элементов управления. Визуальные элементы фокуса, как правило, не нужны на мобильных устройствах, поэтому предоставление возможностей по их созданию и управлению ими системе повышает производительность приложений. Если вам необходим больший контроль над визуальными элементами фокуса, можно переопределить поведение системы и предоставить пользовательский шаблон элемента управления, в котором определяются визуальные элементы фокуса. Дополнительные сведения см. в разделах [<strong>UseSystemFocusVisuals</strong>](https://msdn.microsoft.com/library/windows/apps/dn899076) и [<strong>IsTemplateFocusTarget</strong>](https://msdn.microsoft.com/library/windows/apps/dn899074).</td>
</tr>
<tr class="odd">
<td align="left">PasswordBox.PasswordRevealMode</td>
<td align="left"><p>В универсальных приложениях для Windows свойство [<strong>PasswordRevealMode</strong>](https://msdn.microsoft.com/library/windows/apps/dn890867) заменяет свойство [<strong>IsPasswordRevealButtonEnabled</strong>](https://msdn.microsoft.com/library/windows/apps/hh702579), обеспечивая единообразное поведение на всех семействах устройств.</p>
<p></p>
<div class="alert">
<strong>Внимание!</strong>  До Windows 10 кнопка показа пароля не отображалась по умолчанию, а в универсальных приложениях для Windows эта кнопка отображается по умолчанию. Если в вашем приложении необходимо всегда скрывать пароль, установите для параметра [<strong>PasswordRevealMode</strong>](https://msdn.microsoft.com/library/windows/apps/dn890867) значение "Hidden".
</div>
<div>
 
</div></td>
</tr>
<tr class="even">
<td align="left">Control.IsTextScaleFactorEnabled</td>
<td align="left">Свойство [<strong>IsTextScaleFactorEnabled</strong>](https://msdn.microsoft.com/library/windows/apps/dn624997), доступное в Windows Phone 8.1, теперь доступно для универсальных приложений для Windows на всех семействах устройств.</td>
</tr>
<tr class="odd">
<td align="left">Элемент управления AutoSuggestBox</td>
<td align="left">Элемент управления [<strong>AutoSuggestBox</strong>](https://msdn.microsoft.com/library/windows/apps/dn633874), используемый в Windows Phone 8.1, теперь доступен для универсальных приложений для Windows на всех семействах устройств, и его следует использовать вместо [<strong>SearchBox</strong>](https://msdn.microsoft.com/library/windows/apps/dn252771). <strong>AutoSuggestBox</strong> отображает предложения по мере пользовательского ввода и хорошо работает с различными способами ввода, включая сенсорный ввод, ввод с клавиатуры и редакторы методов ввода. Он также включает некоторые новые члены, которые улучшают его работу в качестве поля поиска: свойство [<strong>QueryIcon</strong>](https://msdn.microsoft.com/library/windows/apps/dn996494) и событие [<strong>QuerySubmitted</strong>](https://msdn.microsoft.com/library/windows/apps/dn996497).</td>
</tr>
<tr class="even">
<td align="left">ContentDialog</td>
<td align="left">Элемент управления [<strong>ContentDialog</strong>](https://msdn.microsoft.com/library/windows/apps/dn633972), свойственный Windows Phone 8.1, теперь доступен в универсальных приложениях для Windows на всех семействах устройств. <strong>ContentDialog</strong> позволяет отображать настраиваемое модальное диалоговое окно, которое хорошо работает на различных типах устройств.</td>
</tr>
<tr class="odd">
<td align="left">Pivot</td>
<td align="left">Элемент управления [<strong>Pivot</strong>](https://msdn.microsoft.com/library/windows/apps/dn608241), свойственный Windows Phone 8.1, теперь доступен в универсальных приложениях для Windows на всех семействах устройств. Теперь элемент управления <strong>Pivot</strong> можно использовать в приложении для мобильных и настольных устройств. <strong>Pivot</strong> обеспечивает адаптивное поведение на основе размера экрана и типа ввода. Вы можете стилизовать элемент управления <strong>Pivot</strong> под вкладку, определив различные представления данных для каждого элемента сводной таблицы.</td>
</tr>
</tbody>
</table>

 

## Текст


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Ключевые интерфейсы API текста в Windows</td>
<td align="left"><p>Новое пространство имен [<strong>Windows.UI.Text.Core</strong>](https://msdn.microsoft.com/library/windows/apps/dn958238) основано на клиент-серверной системе, которая обрабатывает данные, вводимые с клавиатуры, централизованно на одном сервере.</p>
<p>Эту функцию можно использовать в работе с буфером редактирования пользовательского элемента управления входными текстовыми данными. Сервер входных текстовых данных обеспечивает постоянное соответствие содержимого в вашем элементе управления входными текстовыми данными и в своем буфере редактирования, используя для этого канал асинхронной передачи данных между приложением и сервером.</p></td>
</tr>
<tr class="even">
<td align="left">Значки вектора</td>
<td align="left">Элемент [<strong>Glyphs</strong>](https://msdn.microsoft.com/library/windows/apps/br209921) имеет новые свойства [<strong>IsColorFontEnabled</strong>](https://msdn.microsoft.com/library/windows/apps/dn900468) и [<strong>ColorFontPalleteIndex</strong>](https://msdn.microsoft.com/library/windows/apps/dn900466) для поддержки цветных шрифтов; теперь можно использовать файл шрифта для отрисовки значков на основе шрифтов. При использовании <strong>ColorFontPalleteIndex</strong> для переключения цветовых палитр можно отрисовывать один и тот же значок с использованием различных наборов цветов, чтобы, к примеру, отобразить включенный и выключенный значок.</td>
</tr>
<tr class="odd">
<td align="left">События окна редактора метода ввода</td>
<td align="left">Иногда пользователи вводят текст в редакторе метода ввода, который отображается в окне непосредственно под полем ввода текста (это свойственно, как правило, для восточно-азиатских языков). Можно использовать событие [<strong>CandidateWindowBoundsChanged</strong>](https://msdn.microsoft.com/library/windows/apps/dn955144) и свойство [<strong>DesiredCandidateWindowAlignment</strong>](https://msdn.microsoft.com/library/windows/apps/dn955145) для [<strong>TextBox</strong>](https://msdn.microsoft.com/library/windows/apps/br209683) и [<strong>RichEditBox</strong>](https://msdn.microsoft.com/library/windows/apps/br227548), чтобы пользовательский интерфейс приложения лучше работал в окне IME.</td>
</tr>
<tr class="even">
<td align="left">События композиции текста</td>
<td align="left">[<strong>TextBox</strong>](https://msdn.microsoft.com/library/windows/apps/br209683) и [<strong>RichEditBox</strong>](https://msdn.microsoft.com/library/windows/apps/br227548) теперь имеют новые события, информирующие ваше приложение о составлении теста с помощью редактора методов ввода: [<strong>TextCompositionStarted</strong>](https://msdn.microsoft.com/library/windows/apps/dn891458), [<strong>TextCompositionEnded</strong>](https://msdn.microsoft.com/library/windows/apps/dn891457) и [<strong>TextCompositionChanged</strong>](https://msdn.microsoft.com/library/windows/apps/dn891456). Эти события можно обрабатывать для координации кода вашего приложения с процессом композиции текста IME. Например, можно реализовать встроенную функцию автозаполнения для восточно-азиатских языков.</td>
</tr>
<tr class="odd">
<td align="left">Улучшенная обработка двунаправленного текста</td>
<td align="left"><p>Элементы управления текстом XAML имеют новый интерфейс API, который улучшает обработку двунаправленного текста, что приводит к оптимальному выравниванию текста и расположению абзацев независимо от языка ввода.</p>
<p>Значение свойства [<strong>TextReadingOrder</strong>](https://msdn.microsoft.com/library/windows/apps/dn949133) по умолчанию было изменено на <strong>DetectFromContent</strong>, поэтому поддержка определения порядка чтения определяется по умолчанию. Свойство <strong>TextReadingOrder</strong> также было добавлено в объекты [<strong>PasswordBox</strong>](https://msdn.microsoft.com/library/windows/apps/br227519), [<strong>RichEditBox</strong>](https://msdn.microsoft.com/library/windows/apps/br227548) и [<strong>TextBox</strong>](https://msdn.microsoft.com/library/windows/apps/br209683).</p>
<p>Можно определить для свойства [<strong>TextAlignment</strong>](https://msdn.microsoft.com/library/windows/apps/br209703) элементов управления текста новое значение <strong>DetectFromContent</strong>, чтобы выравнивание определялось автоматически в зависимости от содержимого.</p></td>
</tr>
<tr class="even">
<td align="left">Отрисовка текста</td>
<td align="left">Теперь в Windows 10 текст в приложениях XAML отрисовывается в большинстве случаев в два раза быстрее, чем в Windows 8.1. В большинстве случаев вы ощутите преимущества этого улучшения в своих приложениях. Помимо более быстрой отрисовки, эти улучшения также снижают объем потребляемой приложениями XAML памяти на 5 %.</td>
</tr>
</tbody>
</table>

 

## Модель приложений


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Кортана</td>
<td align="left"><p>Расширены основные функции <strong>Кортаны</strong> за счет голосовых команд, которые запускают и выполняют одно действие во внешнем приложении.</p>
<p>Благодаря интеграции основных функций вашего приложения и предоставлению центральной точки входа пользователю для выполнения большинства задач без непосредственного открытия <strong>Кортаны</strong> может стать связующим звеном между вашим приложением и пользователем. Во многих случаях это может существенно сэкономить время и усилия пользователя.</p>
<p>Сведения об [интеграции приложения в полотно Кортаны](https://msdn.microsoft.com/library/windows/apps/xaml/dn974230). Если вам нужны новые идеи, вы можете изучить рекомендации по проектированию и руководства по взаимодействию с пользователем, относящиеся непосредственно к <strong>Кортане</strong>, в разделе [Основы разработки универсальных приложений для Windows](https://dev.windows.com/design/design-basics).</p></td>
</tr>
<tr class="even">
<td align="left">Проводник</td>
<td align="left">Новые методы [<strong>Windows.System.Launcher.LaunchFolderAsync</strong>](https://msdn.microsoft.com/library/windows/apps/dn889616) позволяют запускать проводник и выводить на экран содержимое указанной папки.</td>
</tr>
<tr class="odd">
<td align="left">Общее хранилище</td>
<td align="left">Новый класс [<strong>Windows.ApplicationModel.DataTransfer.SharedStorageAccessManager</strong>](https://msdn.microsoft.com/library/windows/apps/dn889985) и его методы позволяют открывать доступ к файлу для другого приложения путем передачи маркера общего доступа при запуске этого приложения с помощью активации универсального кода ресурса (URI). Конечное приложение активирует маркер и получает доступ к файлу из исходного приложения.</td>
</tr>
<tr class="even">
<td align="left">Параметры</td>
<td align="left"><p>Вы можете выводить на экран страницы со встроенными параметрами с помощью протокола ms-settings в рамках метода [<strong>LaunchUriAsync</strong>](https://msdn.microsoft.com/library/windows/apps/hh701476). Например, указанный далее код выводит страницу параметров Wi-Fi.</p>
<p><code>bool result = await Launcher.LaunchUriAsync(new Uri(&quot;ms-settings://network/wifi&quot;));</code></p>
<p>Список страниц с параметрами, которые можно вывести на экран, см. в разделе [Вывод на экран страниц со встроенными параметрами с помощью протокола ms-settings](https://msdn.microsoft.com/library/windows/apps/jj207014.aspx).</p></td>
</tr>
<tr class="odd">
<td align="left">Связь между приложениями</td>
<td align="left"><p>Благодаря новым API [для связи между приложениями](https://msdn.microsoft.com/library/windows/apps/xaml/dn997827) в Windows 10 приложения для Windows (а также веб-приложения для Windows) могут запускать друга и обмениваться данными и файлами.</p>
<p>С помощью этих новых API сложные задачи, для которых раньше приходилось использовать несколько приложений, теперь можно выполнять без проблем. Например, ваше приложение может запустить приложение социальной сети для выбора контакта или приложение для оформления заказа, чтобы завершить оплату.</p></td>
</tr>
<tr class="even">
<td align="left">Услуги для приложений</td>
<td align="left">Благодаря службе приложения приложение может предоставлять услуги для других приложений в Windows 10. Служба приложения принимает форму фоновой задачи. Приложения переднего плана могут вызывать службу приложения в другом приложении для выполнения задач в фоновом режиме. Справочную информацию по API службы приложения см. в разделе [<strong>Windows.ApplicationModel.AppService</strong>](https://msdn.microsoft.com/library/windows/apps/dn921731).</td>
</tr>
<tr class="odd">
<td align="left">Манифест пакета приложения</td>
<td align="left"><p>Обновления справочных материалов по [схеме манифеста пакета](https://msdn.microsoft.com/library/windows/apps/br211474) для Windows 10 включают элементы, которые были добавлены, удалены и изменены.</p>
<p>Справочную информацию по всем элементам, атрибутам и типам в схеме см. в разделе [Иерархия элементов](https://msdn.microsoft.com/library/windows/apps/dn934819).</p></td>
</tr>
</tbody>
</table>

 

## Устройства


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Microsoft Surface Hub</td>
<td align="left"><p>Microsoft Surface Hub – это мощное устройство для совместной групповой работы и платформа с большим экраном для универсальных приложений для Windows, которые предназначены для работы на Surface Hub или на подключенном устройстве.</p>
<p>Создавайте собственные приложения, разработанные специально для вашего бизнеса и использующие преимущества большого экрана, сенсорного и рукописного управления, а также различного встроенного оборудования, такого как камеры и датчики.</p>
<p>Изучите рекомендации по проектированию и руководства UX, относящиеся к Surface Hub, в разделе [Основы проектирования универсальных приложений для Windows](https://dev.windows.com/design/design-basics). В этих документах объясняются принципы разработки универсальных приложений для Windows.</p>
<p>Подробные сведения о поддержке общих приложений см. в разделе [<strong>SharedModeSettings</strong>](https://msdn.microsoft.com/library/windows/apps/dn949019).</p>
<p>Сведения о рукописном вводе и поддержке многоточечного рукописного ввода новым элементом управления [<strong>InkCanvas</strong>](https://msdn.microsoft.com/library/windows/apps/dn858535) см. в разделах [<strong>Windows.UI.Input.Inking</strong>](https://msdn.microsoft.com/library/windows/apps/br208524) и [<strong>Windows.UI.Input.Inking.Core</strong>](https://msdn.microsoft.com/library/windows/apps/dn958452).</p>
<p>Сведения об обработке входных данных датчика см. в разделе [Интеграция устройств, принтеров и датчиков](https://msdn.microsoft.com/library/windows/apps/br229563).</p></td>
</tr>
<tr class="even">
<td align="left">Местоположение</td>
<td align="left"><p>В Windows 10 представлен новый метод запроса разрешения на доступ к местоположению пользователя — [<strong>RequestAccessAsync</strong>](https://msdn.microsoft.com/library/windows/apps/dn859152).</p>
<p>Пользователь устанавливает уровень защиты данных о местоположении с помощью <strong>настроек конфиденциальности сведений о местоположении</strong> в разделе приложения <strong>Настройки</strong>. Ваше приложение может получать доступ к данным о расположении пользователя только тогда, когда:</p>
<ul>
<li><strong>включен параметр Местоположение для этого устройства</strong> (не относится к Windows 10 для телефонов);</li>
<li>параметр "<strong>Местоположение</strong>" служб определения местоположения <strong>включен</strong>;</li>
<li>в разделе <strong>Выберите приложения, которые могут использовать данные о вашем местоположении</strong> для вашего приложения установлено значение <strong>Вкл.</strong></li>
</ul>
<p>Метод [<strong>RequestAccessAsync</strong>](https://msdn.microsoft.com/library/windows/apps/dn859152) должен обязательно вызываться перед получением доступа к данным о местоположении пользователя. В этот момент ваше приложение должно находиться на переднем плане, а из потока пользовательского интерфейса должен быть вызван метод <strong>RequestAccessAsync</strong>. До тех пор пока пользователь не предоставит вашему приложению разрешение на доступ к данным о местоположении, приложение не сможет использовать такие данные.</p></td>
</tr>
<tr class="odd">
<td align="left">AllJoyn</td>
<td align="left"><p>Пространство имен среды выполнения Windows [<strong>Windows.Devices.AllJoyn</strong>](https://msdn.microsoft.com/library/windows/apps/dn894971) — это реализации платформы и служб программного обеспечения с открытым исходным кодом AllJoyn корпорации Майкрософт. Благодаря данным API ваше универсальное приложение для устройства Windows сможет участвовать вместе с дургими устройствами в сценариях на базе платформы AllJoyn и концепции «Internet of Things» (IoT). Чтобы узнать больше о C API для AllJoyn, загрузите документацию с сайта [AllSeen Alliance](https://allseenalliance.org/).</p>
<p>В данный выпуск включено средство [средство AllJoynCodeGen](https://msdn.microsoft.com/library/windows/apps/dn913809), с помощью которого можно создать компонент Windows для активации сценариев AllJoyn на вашем приложении для устройства.</p>
<div class="alert">
<strong>Примечание.</strong>  ОС Windows 10 IoT Базовая теперь доступна для нового класса небольших устройств, что позволяет создавать устройства в рамках концепции "Интернет вещей" (IoT) с помощью Windows и Visual Studio. Подробнее о Windows IoT см. на сайте [WindowsOnDevices.com](http://www.windowsondevices.com/).
</div>
<div>
 
</div></td>
</tr>
<tr class="even">
<td align="left">Интерфейсы API для печати на мобильных устройствах (XAML)</td>
<td align="left">Это единый унифицированный набор интерфейсов API, который позволяет печатать из приложений UWP на основе XAML на устройствах из любых семейств, включая мобильные устройства. Теперь можно добавить функцию печати в мобильное приложение, используя знакомые интерфейсы API печати из пространств имен [<strong>Windows.Graphics.Printing</strong>](https://msdn.microsoft.com/library/windows/apps/br226489) и [<strong>Windows.UI.Xaml.Printing</strong>](https://msdn.microsoft.com/library/windows/apps/br243325).</td>
</tr>
<tr class="odd">
<td align="left">Аккумулятор</td>
<td align="left"><p>С помощью API аккумулятора в пространстве имен [<strong>Windows.Devices.Power</strong>](https://msdn.microsoft.com/library/windows/apps/dn895017) приложение может получать больше данных о любых аккумуляторах, подключенных к устройству, на котором работает это приложение.</p>
<p>Вы можете создать объект [<strong>Battery</strong>](https://msdn.microsoft.com/library/windows/apps/dn895004) для представления отдельного контроллера батареи или совокупности всех контроллеров батареи (создается [<strong>FromIdAsync</strong>](https://msdn.microsoft.com/library/windows/apps/dn895013) или [<strong>AggregateBattery</strong>](https://msdn.microsoft.com/library/windows/apps/dn895011) соответственно).</p>
<p>Метод [<strong>GetReport</strong>](https://msdn.microsoft.com/library/windows/apps/dn895015) используется для получения объекта [<strong>BatteryReport</strong>](https://msdn.microsoft.com/library/windows/apps/dn895005), сообщающего об уровне заряда, емкости и состоянии соответствующих аккумуляторов.</p></td>
</tr>
<tr class="even">
<td align="left">Устройства MIDI</td>
<td align="left"><p>С помощью нового пространства имен [<strong>Windows.Devices.Midi</strong>](https://msdn.microsoft.com/library/windows/apps/dn895002) можно создать следующее.</p>
<ul>
<li>Приложения, способные связываться с внешними устройствами MIDI.</li>
<li>Приложения и внение устройства, напрямую связывающиеся с программным синтезатором Microsoft GS MIDI.</li>
<li>Сценарии, предусматривающие одновременный доступ нескольких клиентов к одному порту MIDI.</li>
</ul></td>
</tr>
<tr class="odd">
<td align="left">Поддержка пользовательских датчиков</td>
<td align="left">Пространство имен [<strong>Windows.Devices.Sensors.Custom</strong>](https://msdn.microsoft.com/library/windows/apps/dn895032) позволяет разработчикам оборудования определять новые типы пользовательских датчиков, например датчик углекислого газа.</td>
</tr>
<tr class="even">
<td align="left">Эмуляция карты на основе узла (HCE)</td>
<td align="left"><p>Эмуляция карты на основе узла позволяет реализовывать службы эмуляции карты NFC, размещенные в ОС, и по-прежнему связываться с внешним терминалом приложения для чтения через датчик NFC.</p>
<p>Реализация фоновой задачи для эмуляции смарт-карты через NFC. Чтобы запустить фоновую задачу, используйте класс [<strong>SmartCardTrigger</strong>](https://msdn.microsoft.com/library/windows/apps/dn624853).</p>
<p>Значение <strong>EmulatorHostApplicationActivated</strong> в перечислении [<strong>SmartCardTriggerType</strong>](https://msdn.microsoft.com/library/windows/apps/dn608017) позволяет приложению узнать, что событие HCE произошло.</p></td>
</tr>
</tbody>
</table>

 

## Графика


|                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DirectX              | DirectX 12 в Windows 10 позволяет воспользоваться новой версией Microsoft Direct3D – интерфейсом API для трехмерной графики, лежащего в основе DirectX. [Графика Direct3D 12](https://msdn.microsoft.com/library/windows/desktop/dn903821) обеспечивает эффективность и быстродействие низкоуровневых API, работающих по типу консоли. API Direct3D 12 работает с непревзойденной скоростью и эффективностью. С его помощью можно реализовать более сложные сцены и эффекты, увеличить количество объектов и использовать преимущества современных аппаратных графических средств.                                                                                                                                                                                                                                                                                     |
| SoftwareBitmapSource | В универсальных приложениях для Windows можно использовать новый тип [**SoftwareBitmapSource**](https://msdn.microsoft.com/library/windows/apps/dn997854) в качестве источника изображений XAML. Это позволяет передавать незакодированные изображения в инфраструктуру XAML для незамедлительного отображения на экране в обход раскодированию изображения в инфраструктуре XAML. Можно достигать более быстрой отрисовки изображения, например отрисовки низкоинерционных фотографий непосредственно с камеры, использование пользовательских декодировщиков, захват кадров с поверхностей DirectX или даже создание изображений в памяти "с нуля" и отрисовку их непосредственно в XAML с низкой задержкой и незначительной нагрузкой на память.                                                                                                     |
| Камера перспективы   | В универсальных приложениях для Windows XAML имеет новый интерфейс Transform3D API, который позволяет применять преобразования перспективы к дереву (или сцене) XAML, что приводит к преобразованию всех дочерних элементов XAML в соответствии с отдельным преобразованием всей сцены (или камеры). Раньше это можно было сделать с помощью MatrixTransform и сложных математических расчетов, однако Transform3D значительно упрощает применение этого эффекта и допускает даже его анимацию. Подробнее: свойство [**UIElement.Transform3D**](https://msdn.microsoft.com/library/windows/apps/dn906919), [**Transform3D**](https://msdn.microsoft.com/library/windows/apps/dn914748), [**CompositeTransform3D**](https://msdn.microsoft.com/library/windows/apps/dn914714) и [**PerspectiveTransform3D**](https://msdn.microsoft.com/library/windows/apps/dn914740). |

 

## Мультимедиа


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Потоковая трансляция через HTTP</td>
<td align="left">С помощью нового класса [<strong>AdaptiveMediaSource</strong>](https://msdn.microsoft.com/library/windows/apps/dn946912) в приложении можно реализовать адаптивную трансляцию видео. Чтобы выполнялась инициализация объекта, он должен указывать на файл манифеста потоковой трансляции. Поддерживаемые форматы манифеста: Http Live Streaming (HLS) и Dynamic Adaptive Streaming over HTTP (DASH). Сразу после привязывания объекта к элементу списка воспроизведения XAML запускается адаптивное воспроизведение. Такие свойства потоковой трансляции, как доступная, минимальная или максимальная скорость, можно при необходимости запрашивать и настраивать.</td>
</tr>
<tr class="even">
<td align="left">Поддержка видеопроцессора Media Foundation Transcode Video Processor (XVP) в Media Foundation Transforms (MFTs)</td>
<td align="left"><p>Приложения для Windows, использующие Media Foundation Transforms (MFTs), теперь могут преобразовывать, масштабировать и трансформировать необработанные видеоданные с помощью видеопроцессора <strong>Media Foundation Transcode Video Processor</strong> (XVP), как указано ниже.</p>
<p>Новый атрибут [MF_XVP_CALLER_ALLOCATES_OUTPUT](https://msdn.microsoft.com/library/windows/desktop/dn803919) обеспечивает вывод данных для распределенных между абонентами текстур даже в режиме ускорения видео Microsoft DirectX Video Acceleration (DXVA).</p>
<p>Новый интерфейс [<strong>IMFVideoProcessorControl2</strong>](https://msdn.microsoft.com/library/windows/desktop/dn800741) позволяет приложению активировать эффекты оборудования, отправлять запросы на поддерживаемые эффекты оборудования и перезаписывать данные о выполнении вращения видеопроцессором.</p></td>
</tr>
<tr class="odd">
<td align="left">Перекодировка</td>
<td align="left">Новый интерфейс API [<strong>MediaProcessingTrigger</strong>](https://msdn.microsoft.com/library/windows/apps/dn806005) позволяет приложению перекодировать мультимедиа в фоновой задаче. Благодаря этому перекодировка может продолжаться, даже когда приложение переднего плана завершило работу.</td>
</tr>
<tr class="even">
<td align="left">События ошибки мультимедиа MediaElement</td>
<td align="left">В универсальных приложениях для Windows [<strong>MediaElement</strong>](https://msdn.microsoft.com/library/windows/apps/br242926) будет воспроизводить содержимое с несколькими потоками, даже если при декодировании одного из них возникнет ошибка. Основное условие для этого — в содержимом должен быть по крайней мере один допустимый поток. Например, если при воспроизведении содержимого, которое включает в себя аудио- и видеопоток, происходит ошибка видеопотока, <strong>MediaElement</strong> по-прежнему будет воспроизводить аудиопоток. Событие [<strong>PartialMediaFailureDetected</strong>](https://msdn.microsoft.com/library/windows/apps/dn889635) уведомляет о том, что один из потоков в общем потоке не может быть декодирован. Оно также позволяет узнать тип потока, затронутого ошибкой, поэтому вы можете отразить соответствующие сведения в своем пользовательском интерфейсе. Если ошибка затрагивает все потоки в общем потоке мультимедиа, порождается событие [<strong>MediaFailed</strong>](https://msdn.microsoft.com/library/windows/apps/br227393).</td>
</tr>
<tr class="odd">
<td align="left">Поддержка адаптивного потокового видеовоспроизведения с помощью MediaElement</td>
<td align="left">У [<strong>MediaElement</strong>](https://msdn.microsoft.com/library/windows/apps/br242926) появился новый метод [<strong>SetPlaybackSource</strong>](https://msdn.microsoft.com/library/windows/apps/dn899085), поддерживающий адаптивное потоковое видеовоспроизведение. Используйте этот метод, чтобы задать источник мультимедиа как [<strong>AdaptiveMediaSource</strong>](https://msdn.microsoft.com/library/windows/apps/dn946912).</td>
</tr>
<tr class="even">
<td align="left">Приведение с использованием MediaElement и Image</td>
<td align="left">Элементы управления [<strong>MediaElement</strong>](https://msdn.microsoft.com/library/windows/apps/br242926) и [<strong>Image</strong>](https://msdn.microsoft.com/library/windows/apps/br242752) включают новый метод [<strong>GetAsCastingSource</strong>](https://msdn.microsoft.com/library/windows/apps/dn920012). Этот метод можно использовать для программной передачи содержимого из любого элемента мультимедиа или изображения на различные удаленные устройства, такие как Bluetooth, Miracast и DLNA.Эта функциональная возможность включается автоматически при определении для объекта [<strong>AreTransportControlsEnabled</strong>](https://msdn.microsoft.com/library/windows/apps/dn298977) значения "true" в <strong>MediaElement</strong>.</td>
</tr>
<tr class="odd">
<td align="left">Элементы управления транспортировкой мультимедиа для классических приложений</td>
<td align="left">Интерфейс [<strong>ISystemMediaTransportControls</strong>](https://msdn.microsoft.com/library/windows/desktop/dn892299) и соответствующие интерфейсы API позволяют классическим приложениям взаимодействовать со встроенными системными элементами управления транспортировкой мультимедиа. Это включает в себя отклик на взаимодействие пользователей с кнопками управления транспортировкой и обновление отображаемых элементов управления транспортировкой, позволяющее показать метаданные о мультимедийном содержимом, которое воспроизводится в настоящее время.</td>
</tr>
<tr class="even">
<td align="left">Кодирование и декодирование JPEG с прямым доступом</td>
<td align="left">Новые методы WIC [<strong>IWICJpegFrameEncode</strong>](https://msdn.microsoft.com/library/windows/desktop/dn903864) и [<strong>IWICJpegFrameDecode</strong>](https://msdn.microsoft.com/library/windows/desktop/dn903834) позволяют кодировать и декодировать изображения JPEG. Теперь вы также можете включить индексирование графических данных, обеспечивающее эффективный прямой доступ к крупным изображениям за счет большего объема памяти.</td>
</tr>
<tr class="odd">
<td align="left">Наложение слоев для композиций мультимедиа</td>
<td align="left">Новые интерфейсы API [<strong>MediaOverlay</strong>](https://msdn.microsoft.com/library/windows/apps/dn764793) и [<strong>MediaOverlayLayer</strong>](https://msdn.microsoft.com/library/windows/apps/dn764795) упрощают добавление нескольких слоев статического или динамического мультимедийного содержимого к композиции мультимедиа. Для каждого слоя можно настроить прозрачность, положение и расчет времени. Вы даже можете внедрить собственный пользовательский компоновщик для входных слоев.</td>
</tr>
<tr class="even">
<td align="left">Новая платформа эффектов</td>
<td align="left">Пространство имен [<strong>Windows.Media.Effects</strong>](https://msdn.microsoft.com/library/windows/apps/dn278802) предоставляет простую и интуитивно понятную платформу для добавления эффектов к аудио- и видеопотокам. В платформу включены базовые интерфейсы, которые можно внедрить, чтобы создавать пользовательские аудио- и видеоэффекты и вставлять их в конвейер мультимедиа.</td>
</tr>
</tbody>
</table>

 

## Сеть


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Сокеты</td>
<td align="left"><p>Далее перечислены новые возможности, связанные с сокетами.</p>
<ul>
<li><strong>Брокер сокетов.</strong> Брокер сокетов может устанавливать и прекращать соединение между сокетами от имени приложения на любом этапе жизненного цикла приложения. Благодаря этому быстрее происходит обнаружение приложений и соответствующих служб. Например, через брокер сокетов служба Win32 может принимать входящие подключения сокетов даже тогда, когда она не работает.</li>
<li><strong>Повышение пропускной способности.</strong> Пропускная способность сокета оптимизирована для приложений, использующих пространство имен [<strong>Windows.Networking.Sockets</strong>](https://msdn.microsoft.com/library/windows/apps/br226960).</li>
</ul></td>
</tr>
<tr class="even">
<td align="left">Задачи постобработки для фоновой передачи данных</td>
<td align="left">Новые API в пространстве имен [<strong>Windows.Networking.BackgroundTransfer</strong>](https://msdn.microsoft.com/library/windows/apps/br207242) позволяют регистрировать группы задач постобработки. Ваше приложение, даже если оно не выполняется на переднем плане, может незамедлительно реагировать на удачные или неудачные процессы фоновой передачи данных, не дожидаясь перезапуска от пользователя.</td>
</tr>
<tr class="odd">
<td align="left">Поддержка Bluetooth для рекламных объявлений</td>
<td align="left">Благодаря пространству имен [<strong>Windows.Devices.Bluetooth.Advertisement</strong>](https://msdn.microsoft.com/library/windows/apps/dn894325) ваше приложение может отправлять, получать и фильтровать рекламные объявления через соединение Bluetooth с низким энергопотреблением.</td>
</tr>
<tr class="even">
<td align="left">Новые возможности, связанные с API Wi-Fi Direct</td>
<td align="left"><p>Брокер устройства теперь может выполнять связывание без необходимости выхода из приложения. Благодаря дополнениям в пространстве имен [<strong>Windows.Devices.WiFiDirect</strong>](https://msdn.microsoft.com/library/windows/apps/dn297687) устройство теперь может переходить в состояние доступности для обнаружения другими устройствами, а также отслеживать уведомления о входящих подключениях.</p>
<div class="alert">
<strong>Примечание</strong>  В данном выпуске усовершенствованные функции Wi-Fi Direct не реализованы с точки зрения взаимодействия с пользователем, они работают только на основе кнопочного управления. Кроме того, в этом выпуске поддерживается только одно активное соединение.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left">Улучшенная поддержка JSON</td>
<td align="left">В пространстве имен [<strong>Windows.Data.Json</strong>](https://msdn.microsoft.com/library/windows/apps/br240639) реализована улучшенная поддержка существующих стандартных определений и возможностей разработки при преобразовании объектов JSON в ходе сеансов отладки.</td>
</tr>
</tbody>
</table>

 

## Безопасность


|                             |                                                                      |
|-----------------------------|----------------------------------------------------------------------|
| Шифрование ECC              | Новые интерфейсы API в пространстве имен [**Windows.Security.Cryptography**](https://msdn.microsoft.com/library/windows/apps/br241404) обеспечивают поддержку шифрования на основе эллиптических кривых (Elliptical Curve Cryptography, ECC) — метода шифрования с открытым ключом на основе эллиптических кривых в полях с конечными размерами. Метод ECC математически сложнее метода RSA, при этом он создает ключи меньшего размера, а также позволяет сократить потребление памяти и повысить производительность. Этот метод предусматривает использование параметров кривых согласно стандартам NIST (Национальный институт стандартов и технологий) и представляет собой альтернативу ключам RSA для служб и клиентов Microsoft. |
| Microsoft Passport          | Служба Microsoft Passport — это альтернативный метод проверки подлинности, который заменяет пароли с асимметричными алгоритмами шифрования и жест. Классы в пространстве имен [**Credentials**](https://msdn.microsoft.com/library/windows/apps/br227089), например [**KeyCredentialManager**](https://msdn.microsoft.com/library/windows/apps/dn973043), позволяют разработчикам с легкостью создавать приложения при помощи службы Microsoft Passport без шифрования и биометрии.  |
| Microsoft Passport for Work | Служба Microsoft Passport for Work — это альтернативный метод подписи в Windows с помощью учетной записи Azure Active Directory, который не использует пароли, смарт-карту и виртуальные смарт-карты. Вы можете отключить или включить этот параметр политики. |
| Брокер маркеров                | Брокер маркеров — это новая платформа проверки подлинности, с помощью которой приложениям проще подключаться к сетевым поставщикам удостоверений (например, Facebook). Благодаря таким функциям, как управление именем пользователя и паролем учетной записи и упрощенный пользовательский интерфейс, проверка подлинности для пользователей стала значительно лучше. |

 

## Системные службы


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Электропитание</td>
<td align="left"><p>Ваше классическое приложение для Windows теперь может получать уведомления о включении и выключении режима экономии заряда. Реагируя на изменения условий энергопотребления, приложение поможет продлить время работы батареи.</p>
<p>[<strong>GUID_POWER_SAVING_STATUS</strong>](https://msdn.microsoft.com/library/windows/desktop/hh448380): используйте этот новый GUID с функцией [<strong>PowerSettingRegisterNotification</strong>](https://msdn.microsoft.com/library/windows/desktop/hh769082) для получения уведомлений о включении и выключении режима экономии заряда.</p>
<p>[<strong>SYSTEM_POWER_STATUS</strong>](https://msdn.microsoft.com/library/windows/desktop/aa373232): эта структура теперь поддерживает режим экономии заряда. Четвертый элемент <strong>SystemStatusFlag</strong> (ранее – Reserved1) теперь сообщает о том, включен или выключен режим экономии заряда. Чтобы получить указатель для этой структуры, используйте функцию [<strong>GetSystemPowerStatus</strong>](https://msdn.microsoft.com/library/windows/desktop/aa372693).</p></td>
</tr>
<tr class="even">
<td align="left">Версия</td>
<td align="left"><p>Для определения версии операционной системы можно воспользоваться [вспомогательными функциями версии](https://msdn.microsoft.com/library/windows/desktop/dn424972). В Windows 10 среди таких вспомогательных функций представлена новая — [<strong>IsWindows10OrGreater</strong>](https://msdn.microsoft.com/library/windows/desktop/dn905474). Чтобы определить версию системы, следует использовать именно вспомогательные функции, а не устаревшие функции [<strong>GetVersionEx</strong>](https://msdn.microsoft.com/library/windows/desktop/ms724451) и [<strong>GetVersion</strong>](https://msdn.microsoft.com/library/windows/desktop/ms724439). Дополнительные сведения о том, как определить версию системы, см. в разделе [Определение версии системы](https://msdn.microsoft.com/library/windows/desktop/ms724429).</p>
<p>Если для получения данных о версии все же используется устаревшая функция [<strong>GetVersionEx</strong>](https://msdn.microsoft.com/library/windows/desktop/ms724451) или [<strong>GetVersion</strong>](https://msdn.microsoft.com/library/windows/desktop/ms724439) в структуре [<strong>OSVERSIONINFOEX</strong>](https://msdn.microsoft.com/library/windows/desktop/ms724833) или [<strong>OSVERSIONINFO</strong>](https://msdn.microsoft.com/library/windows/desktop/ms724834) следует учитывать, что номер версии в этих структурах увеличивается с 6.3 для Windows 8.1 и Windows Server 2012 R2 до 10.0 для Windows 10. Подробнее о номерах версий операционной системы см. разделе [Версия операционной системы](https://msdn.microsoft.com/library/windows/desktop/ms724832).</p>
<p>Кроме того, в приложении должно быть конкретное указание на Windows 8.1 или Windows 10, чтобы функция [<strong>GetVersionEx</strong>](https://msdn.microsoft.com/library/windows/desktop/ms724451) или [<strong>GetVersion</strong>](https://msdn.microsoft.com/library/windows/desktop/ms724439) возвращала правильные данные об этих версиях. Сведения о способах указания на эти версии Windows в приложении см. в разделе [Указание на Windows в приложении](https://msdn.microsoft.com/library/windows/desktop/dn481241).</p></td>
</tr>
<tr class="odd">
<td align="left">Сведения о пользователе</td>
<td align="left">Новые API в пространстве имен [<strong>Windows.System</strong>](https://msdn.microsoft.com/library/windows/apps/br241814) облегчают доступ к сведениям о пользователе, например к имени пользователя и аватару. Они также дают возможность реагировать на события пользователя, такие как вход и выход.</td>
</tr>
<tr class="even">
<td align="left">Управление памятью и ее профилирование</td>
<td align="left">Поддержка API для профилирования памяти в [<strong>Windows.System</strong>](https://msdn.microsoft.com/library/windows/apps/br241814) расширена для всех платформ, при этом в них добавлены новые классы и функции.</td>
</tr>
</tbody>
</table>

 

## Хранилище


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">API с функцией поиска в файлах для Windows Phone</td>
<td align="left"><p>Издатель может зарегистрировать свое приложение для размещения в общей папке вместе с другими приложениями, которые он издает, путем добавления расширения к манифесту приложения. Затем следует вызвать метод [<strong>Windows.Storage.ApplicationData.GetPublisherCacheFolder</strong>](https://msdn.microsoft.com/library/windows/apps/dn889607), чтобы получить данные расположения общего хранилища.</p>
<p>Надежная система защиты в приложениях среды выполнения Windows, как правило, не позволяет приложениям обмениваться данными между собой. При этом в отдельных случаях может потребоваться обмен файлами и настройками между приложениями одного и того же издателя.</p></td>
</tr>
</tbody>
</table>

 

## Средства


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Визуальное дерево в режиме реального времени в Visual Studio</td>
<td align="left">Visual Studio теперь имеет новую функциональную возможность визуального дерева в режиме реального времени. Можно использовать ее во время отладки, чтобы лучше понять состояние визуального дерева вашего приложения и определить, как были определены свойства элементов. Кроме того, она позволяет менять значения свойств непосредственно во время работы приложения, поэтому вы можете оптимизировать настройки и экспериментировать с ними без необходимости перезапуска.</td>
</tr>
<tr class="even">
<td align="left">Ведение журнала трассировки</td>
<td align="left"><p>[TraceLogging](https://msdn.microsoft.com/library/windows/desktop/dn904636) — новый API трассировки событий для приложений, работающих в пользовательском режиме, и драйверов, работающих в режиме ядра. Он работает на основе [трассировки событий Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803) (ETW). Этот API позволяет быстро получить код инструментария и включить структурированные данные для событий без отдельного XML-файла манифеста инструментирования.</p>
<p>Доступны интерфейсы API TraceLogging для WinRT, .NET и C/C++, позволяющие удовлетворить потребности различных групп разработчиков.</p></td>
</tr>
</tbody>
</table>

 

## Взаимодействие с пользователем


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Распознавание речи</td>
<td align="left">Универсальная платформа Windows теперь поддерживает непрерывное распознавание речи для сценариев длительной диктовки. Узнайте, как включить непрерывную диктовку, изучив документы по речевому взаимодействию.</td>
</tr>
<tr class="even">
<td align="left">Возможности перетаскивания между различными платформами приложений</td>
<td align="left"><p>Новое пространство имен [<strong>Windows.ApplicationModel.DataTransfer.DragDrop</strong>](https://msdn.microsoft.com/library/windows/apps/dn894216) позволяет реализовать функции перетаскивания в универсальных приложениях для Windows. Ранее обычные сценарии перетаскивания для классических программ, например перетаскивание документа из папки в сообщение Outlook в качестве вложения, невозможно было реализовать в универсальных приложениях для Windows. Благодаря этим новым интерфейсам API в вашем приложении можно легко перемещать данные между различными универсальными приложениями для Windows и классическими программами.</p>
<p>Для поддержки функции перетаскивания между приложениями в XAML были добавлены следующие интерфейсы API:</p>
<ul>
<li>[<strong>ListViewBase.DragItemsCompleted</strong>](https://msdn.microsoft.com/library/windows/apps/dn831959)</li>
<li>[<strong>UIElement</strong>](https://msdn.microsoft.com/library/windows/apps/br208911): [<strong>CanDrag</strong>](https://msdn.microsoft.com/library/windows/apps/dn903558), [<strong>DragStarting</strong>](https://msdn.microsoft.com/library/windows/apps/dn903560), [<strong>StartDragAsync</strong>](https://msdn.microsoft.com/library/windows/apps/dn903562), [<strong>DropCompleted</strong>](https://msdn.microsoft.com/library/windows/apps/dn903561)</li>
<li>[<strong>DragOperationDeferral</strong>](https://msdn.microsoft.com/library/windows/apps/dn831917), [<strong>DragUI</strong>](https://msdn.microsoft.com/library/windows/apps/dn985714), [<strong>DragUIOverride</strong>](https://msdn.microsoft.com/library/windows/apps/dn985715)</li>
<li>[<strong>DragEventArgs</strong>](https://msdn.microsoft.com/library/windows/apps/br242372): [<strong>AcceptedOperation</strong>](https://msdn.microsoft.com/library/windows/apps/dn831912), [<strong>DataView</strong>](https://msdn.microsoft.com/library/windows/apps/dn831913), [<strong>DragUIOverride</strong>](https://msdn.microsoft.com/library/windows/apps/dn985710), [<strong>GetDeferral</strong>](https://msdn.microsoft.com/library/windows/apps/dn831914), [<strong>Modifiers</strong>](https://msdn.microsoft.com/library/windows/apps/dn831915)</li>
<li>[<strong>DragItemsCompletedEventArgs</strong>](https://msdn.microsoft.com/library/windows/apps/dn831953), [<strong>DropCompletedEventArgs</strong>](https://msdn.microsoft.com/library/windows/apps/dn903549), [<strong>DragStartingEventArgs</strong>](https://msdn.microsoft.com/library/windows/apps/dn903540)</li>
</ul></td>
</tr>
<tr class="odd">
<td align="left">Пользовательские заголовки окон</td>
<td align="left">Для приложений UWP, предназначенных для семейств настольных компьютеров, теперь можно использовать класс [<strong>ApplicationViewTitleBar</strong>](https://msdn.microsoft.com/library/windows/apps/dn906115) вместе со свойством [<strong>ApplicationView.TitleBar</strong>](https://msdn.microsoft.com/library/windows/apps/dn906131) и методом [<strong>Window.SetTitleBar</strong>](https://msdn.microsoft.com/library/windows/apps/dn965560) для замены содержимого заголовка окон по умолчанию на собственное содержимое XAML. Содержимое XAML обрабатывается как &quot;системный хром&quot;, поэтому события ввода будут обрабатываться не вашим приложением, а операционной системой Windows. Это означает, что пользователь может перетаскивать окно и изменять его размеры, даже щелкая пользовательское содержимое заголовка окна.</td>
</tr>
</tbody>
</table>

 

## Интернет


<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left">Internet Explorer</td>
<td align="left"><p>В Internet Explorer реализован режим Edge — новый режим &quot;живого&quot; документа, позволяющий добиться максимально эффективного взаимодействия с другими современными браузерами и новыми формами веб-содержимого. Это экспериментальный режим, который постепенно внедряется путем распространения среди случайным образом выбранных пользователей Windows 10. Режим Edge можно включить и выключить вручную с помощью нового механизма в IE <strong>about:flags</strong>. См. также:</p>
<ul>
<li>[Режим Edge — еще один шаг к эффективному использованию Интернета](http://blogs.msdn.com/b/ie/archive/2014/11/11/living-on-the-edge-our-next-step-in-interoperability.aspx).</li>
<li>[Руководство разработчика по Internet Explorer для Windows 10](https://dev.windows.com/microsoft-edge/).</li>
</ul></td>
</tr>
<tr class="even">
<td align="left">Режим просмотра веб-страниц WebView Edge</td>
<td align="left">Элемент управления [<strong>WebView</strong>](https://msdn.microsoft.com/library/windows/apps/br227702) использует тот же модуль отрисовки, что и новый браузер Edge. Он предоставляет самый точный режим отрисовки HTML, совместимый со всеми стандартами.</td>
</tr>
<tr class="odd">
<td align="left">WebView вне потока</td>
<td align="left">Можно указать значение [<strong>WebView.ExecutionMode</strong>](https://msdn.microsoft.com/library/windows/apps/dn932034), чтобы включить обработку и отображение веб-содержимого в отдельном фоновом потоке. Это может повысить производительность в определенных, специализированных сценариях.</td>
</tr>
<tr class="even">
<td align="left">Событие WebView.UnsupportedUriSchemeIdentified</td>
<td align="left"><p>Новое событие [<strong>WebView.UnsupportedUriSchemeIdentified</strong>](https://msdn.microsoft.com/library/windows/apps/dn974400) позволяет указать, как ваше приложение будет обрабатывать неподдерживаемую схему URI. Можно обработать это событие, чтобы ваше приложение по-своему обрабатывало неподдерживаемые схемы URI.</p>
<p>Что касается элемента управления HTML WebView, изучите раздел, посвященный событию [<strong>MSWebViewUnsupportedUriSchemeIdentified</strong>](https://msdn.microsoft.com/library/windows/apps/dn803906.aspx).</p></td>
</tr>
<tr class="odd">
<td align="left">Событие WebView.NewWindowRequested</td>
<td align="left"><p>Новое событие [<strong>WebView.NewWindowRequested</strong>](https://msdn.microsoft.com/library/windows/apps/dn974397) позволяет реагировать на запрос сценарием в WebView нового окна браузера.</p>
<p>Что касается элемента управления HTML WebView, изучите раздел, посвященный событию [<strong>MSWebViewNewWindowRequested</strong>](https://msdn.microsoft.com/library/windows/apps/dn806030).</p></td>
</tr>
<tr class="even">
<td align="left">Событие WebView.PermissionRequested</td>
<td align="left"><p>Новое событие [<strong>WebView.PermissionRequested</strong>](https://msdn.microsoft.com/library/windows/apps/dn974398) позволяет содержимому WebView задействовать новые интерфейсы API HTML5, имеющие богатый набор функций и требующие специального разрешения пользователя, например определение географического расположения.</p>
<p>Что касается элемента управления WebView HTML, изучите раздел, посвященный событию [<strong>MSWebViewPermissionRequested</strong>](https://msdn.microsoft.com/library/windows/apps/dn806030.aspx).</p></td>
</tr>
<tr class="odd">
<td align="left">Событие WebView.UnviewableContentIdentified</td>
<td align="left"><p>Новое событие [<strong>WebView.UnviewableContentIdentified</strong>](https://msdn.microsoft.com/library/windows/apps/dn299351) позволяет реагировать на переход [<strong>WebView</strong>](https://msdn.microsoft.com/library/windows/apps/br227702) к другому содержимому, например PDF-файлу или документу Office.</p>
<p>Элементы управления WebView HTML описаны в разделе, посвященном событию [<strong>MSWebViewUnviewableContentIdentified</strong>](https://msdn.microsoft.com/library/windows/apps/dn609716).</p></td>
</tr>
<tr class="even">
<td align="left">Метод WebView.AddWebAllowedObject</td>
<td align="left"><p>Можно вызвать новый метод [<strong>WebView.AddWebAllowedObject</strong>](https://msdn.microsoft.com/library/windows/apps/dn903993) для вставки объекта WinRT в [<strong>WebView</strong>](https://msdn.microsoft.com/library/windows/apps/br227702) XAML, а затем вызвать соответствующие функции из доверенного кода JavaScript, размещенного в этом <strong>WebView</strong>. Например, веб-содержимое может отображать системные уведомления, запросив у родительского приложения вызов интерфейса API [<strong>ToastNotificationManager</strong>](https://msdn.microsoft.com/library/windows/apps/br208642) WinRT.</p>
<p>Сведения об элементе управления [<strong>WebView</strong>](https://msdn.microsoft.com/library/windows/apps/br227702) HTML см. в разделе, посвященном методу [<strong>addWebAllowedObject</strong>](https://msdn.microsoft.com/library/windows/apps/dn926632).</p></td>
</tr>
<tr class="odd">
<td align="left">Метод WebView.ClearTemporaryWebDataAync</td>
<td align="left">Если пользователь взаимодействует с веб-содержимым в [<strong>WebView</strong>](https://msdn.microsoft.com/library/windows/apps/br227702) кода XAML, элемент управления <strong>WebView</strong> кэширует данные на основе этого пользовательского сеанса. Можно вызвать метод [<strong>ClearTemporaryWebDataAsync</strong>](https://msdn.microsoft.com/library/windows/apps/dn974394) для очистки этого кэша. Например, можно очистить кэш, если один пользователь выходит из приложения, чтобы другой пользователь не смог получить доступ к любым данным предыдущего сеанса.</td>
</tr>
</tbody>
</table>



<!--HONumber=May16_HO2-->


