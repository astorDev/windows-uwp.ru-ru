---
author: Jwmsft
Description: "Сдвиг и прокрутка дают пользователям возможность получить доступ к содержимому, находящемуся за пределами экрана."
title: "Руководство по полосам прокрутки"
ms.assetid: 1BFF0E81-BF9C-43F7-95F6-EFC6BDD5EC31
label: Scroll bars
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: a3924fef520d7ba70873d6838f8e194e5fc96c62
ms.openlocfilehash: 8ead56e84e21aaf5005530ed0509efa9440bce59

---
# <a name="scroll-bars"></a>Полосы прокрутки

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Сдвиг и прокрутка дают пользователям возможность получить доступ к содержимому, находящемуся за пределами экрана.

Элемент управления "Средство прокрутки" состоит из такого объема содержимого, который помещается в окно просмотра, и одной или двух полос прокрутки. Сенсорные жесты можно использовать для сдвигания и масштабирования (полосы прокрутки затемняются только во время управления), а для прокрутки можно использовать указатель. Быстрое движение пальца заставит содержимое прокручиваться по инерции.

**Примечание**. В Windows различают два режима отображения сдвига в зависимости от используемого пользователем режима ввода: индикаторы сдвиг при использовании сенсорного ввода или геймпада; и интерактивные полосы прокрутки для других устройств ввода, включая мышь, клавиатуру и ручку.

![Пример внешнего вида стандартных элементов управления "Полоса прокрутки" и "Индикатор сдвига"](images/SCROLLBAR.png)

<div class="microsoft-internal-note">
Полный список красных линий см. в [Центре ресурсов разработки](http://designdepot/DesignDepot.FrontEnd/#/ML/Dashboard/1805)
</div>

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Класс ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/br209527)</li>
<li>[**Класс ScrollBar**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.scrollbar.aspx)</li>
</ul>
</div>


## <a name="examples"></a>Примеры

Объект [**ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.aspx) позволяет отображать содержимое на пространстве меньше его фактического размера. Если содержимое видно не полностью, средство прокрутки отображает полосы прокрутки, которые пользователь может использовать для перемещения видимой области содержимого. Область, которая включает все содержимое средства прокрутки, называется *экстент*. Видимая область содержимого называется *окно просмотра*.

![Снимок экрана, иллюстрирующий стандартный элемент управления “Полоса прокрутки”](images/ScrollBar_Standard.jpg)

## <a name="create-a-scroll-viewer"></a>Создание средства прокрутки
Для добавления на страницу вертикальной прокрутки создайте для содержимого страницы оболочку в средстве просмотра прокрутки.

```xaml
<Page
    x:Class="App1.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App1">

    <ScrollViewer>
        <StackPanel>
            <TextBlock Text="My Page Title" Style="{StaticResource TitleTextBlockStyle}"/>
            <!-- more page content -->
        </StackPanel>
    </ScrollViewer>
</Page>
```
В этом примере XAML показано, как размещать изображение в средстве прокрутки и включать масштабирование.

```xaml
<ScrollViewer ZoomMode="Enabled" MaxZoomFactor="10"
              HorizontalScrollMode="Enabled" HorizontalScrollBarVisibility="Visible"
              Height="200" Width="200">
    <Image Source="Assets/Logo.png" Height="400" Width="400"/>
</ScrollViewer>
```

## <a name="scrollviewer-in-a-control-template"></a>ScrollViewer в шаблоне элемента управления

Обычно элемент управления ScrollViewer является составной частью других элементов управления. Объект ScrollViewer с классом поддержки [**ScrollContentPresenter**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollcontentpresenter.aspx) будет отображать окно просмотра вместе с полосами прокрутки только тогда, когда пространство макета основного элемента управления имеет меньший размер, чем размер развернутого содержимого. Такая ситуация часто возникает со списками, поэтому шаблоны [**ListView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.listview.aspx) и [**GridView**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.gridview.aspx) всегда содержат ScrollViewer. Шаблоны [**TextBox**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.aspx) и [**RichEditBox**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.aspx) также включают ScrollViewer.

Если в элементе управления имеется **ScrollViewer**, основной элемент управления зачастую имеет встроенную функцию обработки событий для определенных событий и манипуляций ввода, позволяющую прокручивать содержимое. Например, GridView интерпретирует жест прокрутки, что приводит к горизонтальной прокрутке содержимого. События и необработанные манипуляции ввода, получаемые основным элементом управления, считаются обработанными элементом управления, а события более низкого уровня, такие как [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.pointerpressed.aspx), не будут вызываться и не будут передаваться в какие-либо родительские контейнеры. Вы можете изменять некоторые параметры обработки встроенных элементов управления, переопределяя класс элементов управления и виртуальные методы **включения*** для событий или изменяя шаблон элемента управления. Но в любом случае не рекомендуется просто воспроизводить исходное поведение по умолчанию, которое обычно там присутствует, чтобы элемент управления мог расширенно реагировать на события и на выполняемые пользователем действия и жесты ввода. Поэтому необходимо подумать о том, нужно ли вам на самом деле, чтобы это событие ввода срабатывало. Вы можете проверить, имеются ли другие события ввода или жесты, которые не обрабатываются элементом управления, и использовать их в своем приложении или схеме взаимодействия элементов управления.

Чтобы позволить элементам управления, имеющим в своем составе ScrollViewer, влиять на поведение и свойства, имеющие отношение к ScrollViewer, элемент ScrollViewer определяет несколько подключенных свойств XAML, которые можно настраивать в стилях и использовать в привязках шаблонов. Подробнее о подключенных свойствах см. в разделе [Общие сведения о подключенных свойствах](../xaml-platform/attached-properties-overview.md).

**Подключенные свойства XAML для ScrollViewer**

ScrollViewer определяет следующие подключенные свойства XAML:
- [ScrollViewer.BringIntoViewOnFocusChange](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.bringintoviewonfocuschange.aspx)
- [ScrollViewer.HorizontalScrollBarVisibility](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.horizontalscrollbarvisibility.aspx)
- [ScrollViewer.HorizontalScrollMode](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.horizontalscrollmode.aspx)
- [ScrollViewer.IsDeferredScrollingEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.isdeferredscrollingenabled.aspx)
- [ScrollViewer.IsHorizontalRailEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.ishorizontalrailenabled.aspx)
- [ScrollViewer.IsHorizontalScrollChainingEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.ishorizontalscrollchainingenabled.aspx)
- [ScrollViewer.IsScrollInertiaEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.isscrollinertiaenabled.aspx)
- [ScrollViewer.IsVerticalRailEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.isverticalrailenabled.aspx)
- [ScrollViewer.IsVerticalScrollChainingEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.isverticalscrollchainingenabled.aspx)
- [ScrollViewer.IsZoomChainingEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.iszoominertiaenabled.aspx)
- [ScrollViewer.IsZoomInertiaEnabled](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.iszoominertiaenabled.aspx)
- [ScrollViewer.VerticalScrollBarVisibility](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.verticalscrollbarvisibilityproperty.aspx)
- [ScrollViewer.VerticalScrollMode](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.verticalscrollmode.aspx)
- [ScrollViewer.ZoomMode](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollviewer.zoommode.aspx)

Эти подключенные свойства XAML предназначены для случаев, когда применение ScrollViewer подразумевается, например когда ScrollViewer имеется в стандартном шаблоне для ListView или GridView, и вы хотите иметь возможность влиять на поведение элемента управления при прокрутке без осуществления доступа к частям шаблона.

Например, вот как можно сделать вертикальные полосы прокрутки всегда видимыми во встроенном средстве прокрутки в ListView.
```xaml
<ListView ScrollViewer.VerticalScrollBarVisibility="Visible"/>
```

В случаях, когда ScrollViewer подразумевается в вашем XAML, как показано в примере кода, вам не нужно использовать синтаксис подключенных свойств. Просто используйте синтаксис атрибута, например `<ScrollViewer VerticalScrollBarVisibility="Visible"/>`.


## <a name="dos-and-donts"></a>Возможности и ограничения

-   По возможности используйте вертикальную, а не горизонтальную прокрутку.
-   Применяйте сдвиг вдоль одной оси для областей содержимого, выходящих за одну из границ окна просмотра (вертикальную или горизонтальную). Применяйте сдвиг по двум осям для областей содержимого, которые выходят за обе границы окна просмотра (вертикальную и горизонтальную).
-   Используйте встроенную функцию прокрутки в представлении списка, представлении сетки, поле со списком, списке, поле текстового ввода и элементах управления "Главный раздел". Если элементов слишком много и невозможно отобразить их сразу, с помощью этих элементов управления пользователь может прокручивать список элементов по горизонтали или по вертикали.
-   Если вы хотите, чтобы пользователь мог применять сдвиг в обоих направлениях на довольно большой области и, возможно, также масштабирование, например если вы даете пользователю возможность сдвигать и масштабировать полноразмерное изображение (а не изображение по размеру экрана), то поместите это изображение в средство прокрутки.
-   Если пользователь прокручивает большой объем текста, настройте средство прокрутки так, чтобы прокрутка осуществлялась только по вертикали.
-   Средство прокрутки должно содержать только один объект. Имейте в виду, что один объект может являться панелью макета, в свою очередь содержащей любое количество собственных объектов.
-   Не помещайте элемент управления [Сведение](tabs-pivot.md) внутрь средства просмотра прокрутки, чтобы избежать конфликтов с логикой прокрутки этого элемента управления.

## <a name="related-topics"></a>Связанные статьи

**Для разработчиков (XAML)**
* [**Класс ScrollViewer**](https://msdn.microsoft.com/library/windows/apps/br209527)



<!--HONumber=Dec16_HO2-->


