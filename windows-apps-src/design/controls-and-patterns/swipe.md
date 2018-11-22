---
author: jwmsft
pm-contact: kisai
design-contact: ksulliv
dev-contact: Shmazlou
doc-status: Published
Description: Swipe commanding is a touch accelerator for context menus.
title: Прокрутка (сдвиг)
label: Swipe
template: detail.hbs
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: Windows10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: daea428d6fad34116dac743655162c2f32315bea
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2018
ms.locfileid: "7575730"
---
# <a name="swipe"></a>Прокрутка (сдвиг)

Команды прокрутки представляют собой ускорение действий в контекстных меню и позволяют пользователям устройств с сенсорным вводом легко получать доступ к частым действиям без необходимости изменения состояния в приложении.

> **Важные API-интерфейсы**: [SwipeControl](/uwp/api/windows.ui.xaml.controls.swipecontrol), [SwipeItem](/uwp/api/windows.ui.xaml.controls.swipeitem), [ListView class](/uwp/api/Windows.UI.Xaml.Controls.ListView)

![Execute и Reveal в светлой теме](images/LightThemeSwipe.png)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Команды прокрутки экономят место. Они полезны в случаях, когда пользователю необходимо выполнить одно и то же действие с несколькими элементами подряд. Также с помощью этих команд можно выполнять "быстрые действия" с элементами, для которых не требуется полное всплывающее окно или изменение состояния, в рамках одной страницы.

Такие команды следует использовать, если существует потенциально крупная группа элементов, каждый из которых предлагает 1–3 действия, которые пользователь может регулярно выполнять с ними. В число этих действий помимо прочего входят следующие.

- Удаление
- Установка метки или архивация
- Сохранение или загрузка
- Ответ

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/SwipeControl">открыть приложение и увидеть SwipeControl в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev015/player]

## <a name="how-does-swipe-work"></a>Как работают команды прокрутки?

Командный интерфейс прокрутки UWP имеет два режима: [Показ](/uwp/api/windows.ui.xaml.controls.swipemode) и [Выполнение](/uwp/api/windows.ui.xaml.controls.swipemode). Он также поддерживает четыре разных направления прокрутки: вверх, вниз, влево и вправо.

### <a name="reveal-mode"></a>Режим показа

В режим показа пользователь сдвигает элемент, чтобы открыть меню с одной или несколькими командами, и для ее выполнения команды ему необходимо нажать ее. Когда пользователь сдвигает элемент и отпускает его, меню остается открытым, пока не будет выбрана какая-либо команда, либо меню может быть закрыто сдвигом в обратную сторону, нажатием вне элемента или путем прокрутки открытого сдвигом элемента за пределы экрана.

![Сдвиг для показа](images/SwipeCommand-Reveal_v2.gif)

Режим показа — это более безопасный и универсальный режим, который можно использовать для большинства различных действий меню, даже потенциально разрушительных действий, таких как удаление.

При выборе одного из вариантов меню, отображаемых в режиме показа и покоя, вызывается команда для этого элемента, и элемент управления прокрутки закрывается.

### <a name="execute-mode"></a>Режим выполнения

В режиме выполнения пользователь сдвигает элемент и открывает его для показа и выполнения единственной команды одним движением сдвига. Если пользователь отпускает сдвигаемый элемент до перемещения за пороговое значение, меню закрывается, и команда не выполняется. Если пользователь сдвигает элемент за пороговое значение и затем отпускает элемент, команда выполняется незамедлительно.

![Сдвиг для выполнения](images/SwipeCommand_Delete_v2.gif)

Если пользователь не отпускает палец после достижения порогового значения и сдвигает элемент обратно, закрывая индикатор действия, команда не выполняется и над элементом не производится никакое действие.

Режим выполнения обеспечивает дополнительные визуальную индикацию путем изменения цвета и ориентации текстовой метки во время перетаскивания элемента.

Выполнение лучше использовать, когда пользователю предлагается выполнить наиболее распространенное действие.

Это также можно использовать для более выполнения более деструктивных действий, например, для удаления элемента. Однако, имейте в виду, что для режима выполнения необходимо выполнить только одно действие сдвига в одном направлении, в отличие от режима показа, когда требуется явно нажать кнопку.

### <a name="swipe-directions"></a>Направления сдвига

Прокрутка работает во всех основных направлениях: вверх, вниз, влево и вправо. Каждому направлению сдвига можно назначить собственные отображаемые элементы или содержимое, но каждому сдвигаемому элементу можно задать лишь один экземпляр направления.

Например, в одном и том же SwipeControl не может быть два определения [LeftItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.LeftItems).

## <a name="how-to-create-a-swipe-command"></a>Создание команды прокрутки

Команды прокрутки содержат два компонента, которые необходимо определить:

- [SwipeControl](/uwp/api/windows.ui.xaml.controls.swipecontrol) служит оболочкой для содержимого. В коллекции, например в ListView, этот компонент находится в пределах элемента DataTemplate.
- Элементы меню прокрутки, которые являются одним или несколькими объектами [SwipeItem](/uwp/api/windows.ui.xaml.controls.swipeitem), размещены в контейнерах направления элемента управления прокрутки: [LeftItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.LeftItems), [RightItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.RightItems), [TopItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.TopItems) или [BottomItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.BottomItems)

Содержимое прокрутки можно встроить или определить в разделе "Ресурсы" страницы или приложения.

Вот простой пример SwipeControl как оболочки для текста: В нем представлена иерархия элементов XAML, необходимых для создания команды прокрутки.

```xaml
<SwipeControl HorizontalAlignment="Center" VerticalAlignment="Center">
    <SwipeControl.LeftItems>
        <SwipeItems>
            <SwipeItem Text="Pin">
                <SwipeItem.IconSource>
                    <SymbolIconSource Symbol="Pin"/>
                </SwipeItem.IconSource>
            </SwipeItem>
        </SwipeItems>
    </SwipeControl.LeftItems>

     <!-- Swipeable content -->
    <Border Width="180" Height="44" BorderBrush="Black" BorderThickness="2">
        <TextBlock Text="Swipe to Pin" Margin="4,8,0,0"/>
    </Border>
</SwipeControl>
```

Теперь рассмотрим более подробный пример использования команд прокрутки в списке. В этом примере вы настроите команду удаления, которая использует режим выполнения и меню других команд, использующее режим показа. Оба набора команд определены в разделе "Ресурсы" страницы. Вы примените команды прокрутки к элементам в ListView.

Сначала создайте элементы прокрутки, представляющие команды, как ресурсы уровня страницы. SwipeItem использует [IconSource](/uwp/api/windows.ui.xaml.controls.iconsource) в качестве своего значка. Создайте значки как и ресурсы.

```xaml
<Page.Resources>
    <SymbolIconSource x:Key="ReplyIcon" Symbol="MailReply"/>
    <SymbolIconSource x:Key="DeleteIcon" Symbol="Delete"/>
    <SymbolIconSource x:Key="PinIcon" Symbol="Pin"/>

    <SwipeItems x:Key="RevealOptions" Mode="Reveal">
        <SwipeItem Text="Reply" IconSource="{StaticResource ReplyIcon}"/>
        <SwipeItem Text="Pin" IconSource="{StaticResource PinIcon}"/>
    </SwipeItems>

    <SwipeItems x:Key="ExecuteDelete" Mode="Execute">
        <SwipeItem Text="Delete" IconSource="{StaticResource DeleteIcon}"
                   Background="Red"/>
    </SwipeItems>
</Page.Resources>
```

Помните, что текст элементов меню, скрытых под жестом сдвига, должен быть кратким и точным. Эти действия должны быть основными, то есть такими, которые могут понадобиться пользователю несколько раз за короткий период.

Настройка команды прокрутки для работы в коллекции или ListView производится точно так же, как определение одной команды прокрутки (как в приведенном выше примере), за исключением того, что определение элементов SwipeControl задается в DataTemplate, поэтому они применяются ко всем элементам в коллекции.

Вот ListView вместе со SwipeControl, примененным к DataTemplate элемента. Свойства LeftItems и RightItems ссылаются на элементы прокрутки, которые вы создали как ресурсы.

```xaml
<ListView x:Name="sampleList" Width="300">
    <ListView.ItemContainerStyle>
        <Style TargetType="ListViewItem">
            <Setter Property="HorizontalContentAlignment" Value="Stretch"/>
            <Setter Property="VerticalContentAlignment" Value="Stretch"/>
        </Style>
    </ListView.ItemContainerStyle>
    <ListView.ItemTemplate>
        <DataTemplate x:DataType="x:String">
            <SwipeControl x:Name="ListViewSwipeContainer"
                          LeftItems="{StaticResource RevealOptions}"
                          RightItems="{StaticResource ExecuteDelete}"
                          Height="60">
                <StackPanel Orientation="Vertical">
                    <TextBlock Text="{x:Bind}" FontSize="18"/>
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Text="Lorem ipsum dolor sit amet, consectetur adipiscing elit..." FontSize="12"/>
                    </StackPanel>
                </StackPanel>
            </SwipeControl>
        </DataTemplate>
    </ListView.ItemTemplate>
    <x:String>Item 1</x:String>
    <x:String>Item 2</x:String>
    <x:String>Item 3</x:String>
    <x:String>Item 4</x:String>
    <x:String>Item 5</x:String>
</ListView>
```

## <a name="handle-an-invoked-swipe-command"></a>Обработка к вызванной команде прокрутки

Чтобы выполнить действие с командной прокрутки, необходимо обработать событие [Invoked](/uwp/api/windows.ui.xaml.controls.swipeitem.Invoked). (Дополнительные сведения о том, как пользователь может вызвать команду, см. в разделе _Как работают команды прокрутки?_ выше в этой статье.) Как правило, команда прокрутки выполняется в элементе управления ListView или сценарии со списком. В этом случае, когда команда вызывается, необходимо выполнить действие над перетаскиваемым элементом.

Ниже описано, как обрабатывать событие Invoked элемента прокрутки _delete_ (удалить), созданного ранее.

```xaml
<SwipeItems x:Key="ExecuteDelete" Mode="Execute">
    <SwipeItem Text="Delete" IconSource="{StaticResource DeleteIcon}"
               Background="Red" Invoked="Delete_Invoked"/>
</SwipeItems>
```

Элемент данных является DataContext элемента управления SwipeControl. В коде можно получить доступ к элементу, который был сдвинут, получив свойство SwipeControl.DataContext из аргументов события, как показано ниже.

```csharp
 private void Delete_Invoked(SwipeItem sender, SwipeItemInvokedEventArgs args)
 {
     sampleList.Items.Remove(args.SwipeControl.DataContext);
 }
```

> [!NOTE]
> Здесь элементы были добавлены непосредственно к коллекции ListView.Items для простоты, чтобы удаление элемента выполнялось таким же образом. Если вместо этого элемент ListView.ItemsSource задается для коллекции, что происходит чаще, необходимо удалить элемент из коллекции источника.

В этом конкретном случае происходит удаление элемента из списка, поэтому окончательное визуальное состояние сдвинутого элемента не является важным. Однако в ситуациях, когда вы просто хотите выполнить действие и затем снова свернуть команду прокрутки, можно задать свойству [BehaviorOnInvoked](/uwp/api/windows.ui.xaml.controls.swipeitem.BehaviorOnInvoked) одно из значений перечисления [SwipeBehaviorOnInvoked](/uwp/api/windows.ui.xaml.controls.swipebehavioroninvoked).

- **Auto (автоматически)**
  - В режиме выполнения открытый элемент прокрутки останется открытым при его вызове.
  - В режиме показа открытый элемент прокрутки будет свернут при его вызове.
- **Close (закрыть)**
  - При вызове элемента команда прокрутки всегда будет сворачиваться и возвращаться в стандартное состояние независимо от режима.
- **RemainOpen (оставаться открытым)**
  - При вызове элемента команда прокрутки всегда будет оставаться открытой независимо от режима.

В примере ниже элемент прокрутки _reply_ (ответ) настроен на закрытие после его вызова.

```xaml
<SwipeItem Text="Reply" IconSource="{StaticResource ReplyIcon}"
           Invoked="Reply_Invoked"
           BehaviorOnInvoked = "Close"/>
```

## <a name="dos-and-donts"></a>Рекомендации

- Не используйте прокрутку в FlipViews, центрах и сводках Их сочетание может запутать пользователя из-за конфликта направлений прокрутки.
- Не объединяйте горизонтальный сдвиг с горизонтальной навигацией и вертикальный сдвиг с вертикальной навигацией.
- Следите за тем, чтобы при использование пользователем сдвига выполнялось одно и то же действие и поведение было единообразным для всех связанных элементов, которые могут быть сдвинуты.
- Используйте прокрутку для основных действий, которые захочет выполнять пользователь.
- Используйте прокрутку для элементов, где одно действие повторяется многократно.
- Используйте горизонтальный сдвиг на широких элементах, а вертикальный сдвиг — на вертикальных элементах.
- Используйте краткие и точные текстовые метки.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Смежные разделы

- [Представление списка и сетки](listview-and-gridview.md)
- [Контейнеры и шаблоны элементов](item-containers-templates.md)
- [Обновление путем оттягивания](pull-to-refresh.md)
