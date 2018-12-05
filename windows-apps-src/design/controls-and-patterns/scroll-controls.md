---
Description: Panning and scrolling allows users to reach content that extends beyond the bounds of the screen.
title: Элементы управления "Средство прокрутки"
ms.assetid: 1BFF0E81-BF9C-43F7-95F6-EFC6BDD5EC31
label: Scrollbars
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: Windows 10, uwp
pm-contact: Abarlow, pagildea
design-contact: ksulliv
dev-contact: regisb
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 368454e32ba2c88994161f6c7deb554fff73972a
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8735188"
---
# <a name="scroll-viewer-controls"></a>Элементы управления "Средство прокрутки"



При наличии большего содержимого пользовательского интерфейса, чем можно поместить в область, используйте средство прокрутки.

> **Важные API-интерфейсы**: [класс ScrollViewer](https://msdn.microsoft.com/library/windows/apps/br209527), [класс ScrollBar](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.primitives.scrollbar.aspx)

Средства прокрутки позволяют содержимому выходить за пределы окна просмотра (видимой области). Пользователи могут добраться до этого содержимого, манипулируя средством прокрутки с помощью сенсорного ввода, колесика мыши, клавиатуры или игрового планшета или с помощью курсора мыши либо пера, для взаимодействия с полосой прокрутки средства прокрутки. На рисунке показано несколько примеров элементов управления средством прокрутки.

![Снимок экрана, иллюстрирующий стандартный элемент управления “Полоса прокрутки”](images/ScrollBar_Standard.jpg)

В зависимости от ситуации, полоса прокрутки средства прокрутки использует две различные визуализации, показанные на следующем рисунке: индикатор сдвига (слева) и традиционную полосу прокрутки (справа).

![Пример внешнего вида стандартных элементов управления "Полоса прокрутки" и "Индикатор сдвига"](images/SCROLLBAR.png)

Средство прокрутки знает о методе ввода пользователя и использует его, чтобы определить, какие визуализации отображать.

* При прокрутке области без непосредственного управления полосой прокрутки, например, сенсорным вводом, появится индикатор сдвига, указывающий текущую позицию прокрутки.
* При перемещении курсора мыши или пера через индикатор сдвига он преобразуется в традиционную полосу прокрутки.  Перетаскиванием ползунка полосы прокрутки можно управлять областью прокрутки.

<!--
<div class="microsoft-internal-note">
See complete redlines in [UNI]http://uni/DesignDepot.FrontEnd/#/ProductNav/3378/0/dv/?t=Windows|Controls|ScrollControls&f=RS2
</div>
-->

![Полосы прокрутки в действии](images/conscious-scroll.gif)

> [!NOTE]
> Когда полоса прокрутки отображается, она накладывается на 16 пикселей поверх содержимого внутри ScrollViewer. Чтобы обеспечить качественное проектирование взаимодействия с пользователем, необходимо убедиться, что интерактивное содержимое не замещается перекрытием. Кроме того, если вы не хотите иметь перекрытие взаимодействия с пользователем, оставьте 16 пикселей заполнения границ окна просмотра, чтобы оставить место для полосы прокрутки.

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ScrollViewer">открыть приложение и увидеть ScrollViewer в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

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

Этот код XAML показано, как включить горизонтальной прокрутки, размещать изображение в средстве прокрутки и включать масштабирование.

```xaml
<ScrollViewer ZoomMode="Enabled" MaxZoomFactor="10"
              HorizontalScrollMode="Enabled" HorizontalScrollBarVisibility="Visible"
              Height="200" Width="200">
    <Image Source="Assets/Logo.png" Height="400" Width="400"/>
</ScrollViewer>
```

## <a name="scrollviewer-in-a-control-template"></a>ScrollViewer в шаблоне элемента управления

Обычно элемент управления ScrollViewer является составной частью других элементов управления. Объект ScrollViewer с классом поддержки [ScrollContentPresenter](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.scrollcontentpresenter.aspx) будет отображать окно просмотра вместе с полосами прокрутки только тогда, когда пространство макета основного элемента управления имеет меньший размер, чем размер развернутого содержимого. Такая ситуация часто возникает со списками, поэтому шаблоны [ListView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.listview.aspx) и [GridView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.gridview.aspx) всегда содержат ScrollViewer. Шаблоны [TextBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.textbox.aspx) и [RichEditBox](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.controls.richeditbox.aspx) также включают ScrollViewer.

Если в элементе управления имеется **ScrollViewer**, основной элемент управления зачастую имеет встроенную функцию обработки событий для определенных событий и манипуляций ввода, позволяющую прокручивать содержимое. Например, GridView интерпретирует жест прокрутки, что приводит к горизонтальной прокрутке содержимого. События и необработанные манипуляции ввода, получаемые основным элементом управления, считаются обработанными элементом управления, а события более низкого уровня, такие как [PointerPressed](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.pointerpressed.aspx), не будут вызываться и не будут передаваться в какие-либо родительские контейнеры. Вы можете изменять некоторые параметры обработки встроенных элементов управления, переопределяя класс элементов управления и виртуальные методы **включения*** для событий или изменяя шаблон элемента управления. Но в любом случае не рекомендуется просто воспроизводить исходное поведение по умолчанию, которое обычно там присутствует, чтобы элемент управления мог расширенно реагировать на события и на выполняемые пользователем действия и жесты ввода. Поэтому необходимо подумать о том, нужно ли вам на самом деле, чтобы это событие ввода срабатывало. Вы можете проверить, имеются ли другие события ввода или жесты, которые не обрабатываются элементом управления, и использовать их в своем приложении или схеме взаимодействия элементов управления.

Чтобы позволить элементам управления, имеющим в своем составе ScrollViewer, влиять на поведение и свойства, имеющие отношение к ScrollViewer, элемент ScrollViewer определяет несколько подключенных свойств XAML, которые можно настраивать в стилях и использовать в привязках шаблонов. Подробнее о подключенных свойствах см. в разделе [Общие сведения о подключенных свойствах](../../xaml-platform/attached-properties-overview.md).

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

- По возможности используйте вертикальную, а не горизонтальную прокрутку.
- Применяйте сдвиг вдоль одной оси для областей содержимого, выходящих за одну из границ окна просмотра (вертикальную или горизонтальную). Применяйте сдвиг по двум осям для областей содержимого, которые выходят за обе границы окна просмотра (вертикальную и горизонтальную).
- Используйте встроенную функцию прокрутки в представлении списка, представлении сетки, поле со списком, списке, поле текстового ввода и элементах управления "Главный раздел". Если элементов слишком много и невозможно отобразить их сразу, с помощью этих элементов управления пользователь может прокручивать список элементов по горизонтали или по вертикали.
- Если вы хотите, чтобы пользователь мог применять сдвиг в обоих направлениях на довольно большой области и, возможно, также масштабирование, например если вы даете пользователю возможность сдвигать и масштабировать полноразмерное изображение (а не изображение по размеру экрана), то поместите это изображение в средство прокрутки.
- Если пользователь прокручивает большой объем текста, настройте средство прокрутки так, чтобы прокрутка осуществлялась только по вертикали.
- Средство прокрутки должно содержать только один объект. Имейте в виду, что один объект может являться панелью макета, в свою очередь содержащей любое количество собственных объектов.
- Не помещайте элемент управления [Сводка](tabs-pivot.md) внутрь средства просмотра прокрутки, чтобы избежать конфликтов с логикой прокрутки этого элемента управления.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-topics"></a>Смежные разделы

**Для разработчиков (XAML)**

* [Класс ScrollViewer](https://msdn.microsoft.com/library/windows/apps/br209527)
