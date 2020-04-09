---
Description: Сдвиг и прокрутка дают пользователям возможность получить доступ к содержимому, находящемуся за пределами экрана.
title: Элементы управления "Средство прокрутки"
ms.assetid: 1BFF0E81-BF9C-43F7-95F6-EFC6BDD5EC31
label: Scrollbars
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: Abarlow, pagildea
design-contact: ksulliv
dev-contact: regisb
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: a2123c8baa93356a0bb5adcfb2a32ac71af173cc
ms.sourcegitcommit: af4050f69168c15b0afaaa8eea66a5ee38b88fed
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/21/2020
ms.locfileid: "80081583"
---
# <a name="scroll-viewer-controls"></a>Элементы управления "Средство прокрутки"



При наличии большего содержимого пользовательского интерфейса, чем можно поместить в область, используйте средство прокрутки.

> **Важные API**: [класс ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer), [класс ScrollBar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.scrollbar)

Средства прокрутки позволяют содержимому выходить за пределы окна просмотра (видимой области). Пользователи могут добраться до этого содержимого, манипулируя средством прокрутки с помощью сенсорного ввода, колесика мыши, клавиатуры или игрового планшета, курсора мыши либо пера для взаимодействия с полосой прокрутки средства прокрутки. На рисунке показано несколько примеров элементов управления средством прокрутки.

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

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/ScrollViewer">открыть приложение и увидеть ScrollViewer в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
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

В этом примере XAML показано, как размещать изображение в средстве прокрутки и включать масштабирование.

```xaml
<ScrollViewer ZoomMode="Enabled" MaxZoomFactor="10"
              HorizontalScrollMode="Enabled" HorizontalScrollBarVisibility="Visible"
              Height="200" Width="200">
    <Image Source="Assets/Logo.png" Height="400" Width="400"/>
</ScrollViewer>
```

## <a name="scrollviewer-in-a-control-template"></a>ScrollViewer в шаблоне элемента управления

Обычно элемент управления ScrollViewer является составной частью других элементов управления. Объект ScrollViewer с классом поддержки [ScrollContentPresenter](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollContentPresenter) будет отображать окно просмотра вместе с полосами прокрутки только тогда, когда пространство макета основного элемента управления имеет меньший размер, чем размер развернутого содержимого. Такая ситуация часто возникает со списками, поэтому шаблоны [ListView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) и [GridView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) всегда содержат ScrollViewer. Шаблоны [TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) и [RichEditBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox) также включают ScrollViewer.

Если в элементе управления имеется **ScrollViewer**, основной элемент управления зачастую имеет встроенную функцию обработки событий для определенных событий и манипуляций ввода, позволяющую прокручивать содержимое. Например, GridView интерпретирует жест прокрутки, что приводит к горизонтальной прокрутке содержимого. События и необработанные манипуляции ввода, получаемые основным элементом управления, считаются обработанными элементом управления, а события более низкого уровня, такие как [PointerPressed](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed), не будут вызываться и не будут передаваться в какие-либо родительские контейнеры. Вы можете изменять некоторые параметры обработки встроенных элементов управления, переопределяя класс элементов управления и виртуальные методы **On**_Event_ для событий или изменяя шаблон элемента управления. Но в любом случае не рекомендуется просто воспроизводить исходное поведение по умолчанию, которое обычно там присутствует, чтобы элемент управления мог расширенно реагировать на события и на выполняемые пользователем действия и жесты ввода. Поэтому необходимо подумать о том, нужно ли вам на самом деле, чтобы это событие ввода срабатывало. Вы можете проверить, имеются ли другие события ввода или жесты, которые не обрабатываются элементом управления, и использовать их в своем приложении или схеме взаимодействия элементов управления.

Чтобы позволить элементам управления, имеющим в своем составе ScrollViewer, влиять на поведение и свойства, имеющие отношение к ScrollViewer, элемент ScrollViewer определяет несколько подключенных свойств XAML, которые можно настраивать в стилях и использовать в привязках шаблонов. Подробнее о подключенных свойствах см. в разделе [Общие сведения о подключенных свойствах](../../xaml-platform/attached-properties-overview.md).

**Подключенные свойства XAML для ScrollViewer**

ScrollViewer определяет следующие подключенные свойства XAML:

- [ScrollViewer.BringIntoViewOnFocusChange](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.bringintoviewonfocuschange)
- [ScrollViewer.HorizontalScrollBarVisibility](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollbarvisibility)
- [ScrollViewer.HorizontalScrollMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.horizontalscrollmode)
- [ScrollViewer.IsDeferredScrollingEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.isdeferredscrollingenabled)
- [ScrollViewer.IsHorizontalRailEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.ishorizontalrailenabled)
- [ScrollViewer.IsHorizontalScrollChainingEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.ishorizontalscrollchainingenabled)
- [ScrollViewer.IsScrollInertiaEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.isscrollinertiaenabled)
- [ScrollViewer.IsVerticalRailEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.isverticalrailenabled)
- [ScrollViewer.IsVerticalScrollChainingEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.isverticalscrollchainingenabled)
- [ScrollViewer.IsZoomChainingEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.iszoominertiaenabled)
- [ScrollViewer.IsZoomInertiaEnabled](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.iszoominertiaenabled)
- [ScrollViewer.VerticalScrollBarVisibility](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollbarvisibilityproperty)
- [ScrollViewer.VerticalScrollMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.verticalscrollmode)
- [ScrollViewer.ZoomMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.scrollviewer.zoommode)

Эти подключенные свойства XAML предназначены для случаев, когда применение ScrollViewer подразумевается, например когда ScrollViewer имеется в стандартном шаблоне для ListView или GridView, и вы хотите иметь возможность влиять на поведение элемента управления при прокрутке без осуществления доступа к частям шаблона.

Например, вот как можно сделать вертикальные полосы прокрутки всегда видимыми во встроенном средстве прокрутки в ListView.

```xaml
<ListView ScrollViewer.VerticalScrollBarVisibility="Visible"/>
```

В случаях, когда ScrollViewer подразумевается в вашем XAML, как показано в примере кода, вам не нужно использовать синтаксис подключенных свойств. Просто используйте синтаксис атрибута, например `<ScrollViewer VerticalScrollBarVisibility="Visible"/>`.


## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать

- По возможности используйте вертикальную, а не горизонтальную прокрутку.
- Применяйте сдвиг вдоль одной оси для областей содержимого, выходящих за одну из границ окна просмотра (вертикальную или горизонтальную). Применяйте сдвиг по двум осям для областей содержимого, которые выходят за обе границы окна просмотра (вертикальную и горизонтальную).
- Используйте встроенную функцию прокрутки в представлении списка, представлении сетки, поле со списком, списке, поле текстового ввода и элементах управления "Главный раздел". Если элементов слишком много и невозможно отобразить их сразу, с помощью этих элементов управления пользователь может прокручивать список элементов по горизонтали или по вертикали.
- Если вы хотите, чтобы пользователь мог применять сдвиг в обоих направлениях на довольно большой области и, возможно, также масштабирование, например если вы даете пользователю возможность сдвигать и масштабировать полноразмерное изображение (а не изображение по размеру экрана), то поместите это изображение в средство прокрутки.
- Если пользователь прокручивает большой объем текста, настройте средство прокрутки так, чтобы прокрутка осуществлялась только по вертикали.
- Средство прокрутки должно содержать только один объект. Имейте в виду, что один объект может являться панелью макета, в свою очередь содержащей любое количество собственных объектов.
- Не помещайте элемент управления [Сведение](pivot.md) внутрь средства просмотра прокрутки, чтобы избежать конфликтов с логикой прокрутки этого элемента управления.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-topics"></a>Связанные темы

**Разработчикам (XAML)**

* [Класс ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer)
