---
pm-contact: kisai
design-contact: ksulliv
dev-contact: Shmazlou
doc-status: Published
Description: Команды сдвига — это акселератор сенсорного ввода для контекстных меню.
title: Swipe
label: Swipe
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 315edbddccc51b7e742bf9beffad8497a104ce03
ms.sourcegitcommit: 8be8ed1ef4e496055193924cd8cea2038d2b1525
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80614095"
---
# <a name="swipe"></a>Swipe

Команды сдвига представляют собой акселератор действий в контекстных меню и позволяют пользователям устройств с сенсорным вводом легко получать доступ к частым действиям без изменения состояний в приложении.

> **API библиотеки пользовательского интерфейса Windows**: [SwipeControl](/uwp/api/microsoft.ui.xaml.controls.swipecontrol), [SwipeItem](/uwp/api/microsoft.ui.xaml.controls.swipeitem)
>
> **API платформы**: [SwipeControl](/uwp/api/windows.ui.xaml.controls.swipecontrol), [SwipeItem](/uwp/api/windows.ui.xaml.controls.swipeitem), [ListView class](/uwp/api/Windows.UI.Xaml.Controls.ListView)

![Execute и Reveal в светлой теме](images/LightThemeSwipe.png)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Команды сдвига экономят место. Они полезны в тех случаях, когда пользователю нужно выполнить одно и то же действие с несколькими элементами подряд. Также с помощью этих команд можно выполнять "быстрые действия" с элементами, для которых не требуется полное всплывающее окно или изменение состояния, в рамках одной страницы.

Такие команды следует использовать, если существует потенциально крупная группа элементов, каждый из которых предлагает 1–3 действия, которые пользователь будет выполнять с ними регулярно. Сюда могут относиться, среди прочего:

- удаление;
- установка метки или архивация;
- сохранение или скачивание;
- отправка ответа.

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/SwipeControl">открыть приложение и увидеть SwipeControl в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="video-summary"></a>Видео с кратким описанием

> [!VIDEO https://channel9.msdn.com/Events/Windows/Windows-Developer-Day-Fall-Creators-Update/WinDev015/player]

## <a name="how-does-swipe-work"></a>Как работают команды прокрутки?

Командный интерфейс прокрутки UWP имеет два режима: [Показ](/uwp/api/windows.ui.xaml.controls.swipemode) и [Выполнение](/uwp/api/windows.ui.xaml.controls.swipemode). Он также поддерживает четыре разных направления прокрутки: вверх, вниз, влево и вправо.

### <a name="reveal-mode"></a>Режим показа

В режиме показа пользователь сдвигает элемент, чтобы открыть меню с одной или несколькими командами, а для ее выполнения команды нажимает на нее. Когда пользователь сдвигает элемент и отпускает его, меню остается открытым, пока не будет выбрана какая-либо команда. Также меню можно закрыть сдвигом в обратную сторону, нажатием вне элемента или сдвигом открытого элемента за пределы экрана.

![Сдвиг для показа](images/SwipeCommand-Reveal_v2.gif)

Режим показа — это более безопасный и универсальный режим, который можно использовать для большинства типов меню, даже с потенциально разрушительными действиями, такими как удаление.

При выборе одного из вариантов меню, отображаемых в режиме показа и покоя, вызывается команда для этого элемента, и элемент управления прокрутки закрывается.

### <a name="execute-mode"></a>Режим выполнения

В режиме выполнения пользователь сдвигает элемент для показа и выполнения единственной команды одним движением сдвига. Если пользователь отпускает сдвигаемый элемент до перемещения за пороговое значение, меню закрывается, и команда не выполняется. Если пользователь сдвигает элемент за пороговое значение и затем отпускает элемент, команда немедленно выполняется.

![Сдвиг для выполнения](images/SwipeCommand_Delete_v2.gif)

Если пользователь не отпускает палец после достижения порогового значения и сдвигает элемент обратно, команда не выполняется и действие над элементом не производится.

Режим выполнения поддерживает дополнительную визуальную индикацию путем изменения цвета и ориентации текстовой метки во время перетаскивания элемента.

Режим выполнения лучше использовать для наиболее распространенного действия пользователя.

Также его можно применить для более деструктивных действий, например, для удаления элемента. Однако, имейте в виду, что для режима выполнения необходимо выполнить только одно действие сдвига в одном направлении, в отличие от режима показа, когда требуется явно нажать кнопку.

### <a name="swipe-directions"></a>Направления сдвига

Сдвиг работает во всех основных направлениях: вверх, вниз, влево и вправо. Каждому направлению сдвига можно назначить собственные отображаемые элементы или содержимое, но каждому сдвигаемому элементу можно задать лишь один экземпляр конкретного направления.

Например, в одном и том же SwipeControl не может быть два определения [LeftItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.LeftItems).

## <a name="how-to-create-a-swipe-command"></a>Создание команды сдвига

Команды сдвига содержат два компонента, которые необходимо определить:

- [SwipeControl](/uwp/api/windows.ui.xaml.controls.swipecontrol) служит оболочкой для содержимого. В коллекции, например в ListView, этот компонент находится в пределах элемента DataTemplate.
- Элементы меню сдвига, которые являются одним или несколькими объектами [SwipeItem](/uwp/api/windows.ui.xaml.controls.swipeitem), размещены в контейнерах направления элемента управления прокрутки: [LeftItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.LeftItems), [RightItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.RightItems), [TopItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.TopItems) или [BottomItems](/uwp/api/windows.ui.xaml.controls.swipecontrol.BottomItems).

Содержимое сдвига можно включить в код или определить в разделе "Ресурсы" для страницы или приложения.

Вот простой пример SwipeControl как оболочки для текста: В нем представлена иерархия элементов XAML, необходимых для создания команды сдвига.

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

Теперь рассмотрим более подробный пример использования команд сдвига в списке. В этом примере вы настроите команду удаления в режиме выполнения и меню для других команд, использующее в режиме показа. Оба набора команд определены в разделе "Ресурсы" для страницы. Вы примените команды сдвига к элементам в ListView.

Сначала создайте элементы сдвига, в которых команды представлены как ресурсы уровня страницы. SwipeItem использует [IconSource](/uwp/api/windows.ui.xaml.controls.iconsource) в качестве значка. Значки тоже следует создавать как ресурсы.

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

Помните, что текст элементов меню, скрытых под жестом сдвига, должен быть кратким и точным. Эти действия должны быть базовыми, то есть такими, которые могут понадобиться пользователю несколько раз за короткий период.

Настройка команды сдвига для работы в коллекции или ListView производится точно так же, как определение одной команды прокрутки (как в приведенном выше примере), за исключением того, что определение элементов SwipeControl задается в DataTemplate, поэтому они применяются ко всем элементам в коллекции.

Вот пример ListView со SwipeControl, примененного к DataTemplate элемента. Свойства LeftItems и RightItems ссылаются на элементы сдвига, которые вы создали как ресурсы.

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

## <a name="handle-an-invoked-swipe-command"></a>Обработка вызванной команды сдвига

Чтобы выполнить действие по команде прокрутки, следует обработать событие [Invoked](/uwp/api/windows.ui.xaml.controls.swipeitem.Invoked). (Дополнительные сведения о том, как пользователь может вызвать команду, см. в разделе _Как работают команды прокрутки?_ выше в этой статье.) Команды сдвига чаще всего применяются в ListView или другом режиме списка. В этом случае, когда команда вызывается, необходимо выполнить действие над перетаскиваемым элементом.

Ниже описано, как обрабатывать событие Invoked для созданного ранее элемента сдвига _delete_ (удалить).

```xaml
<SwipeItems x:Key="ExecuteDelete" Mode="Execute">
    <SwipeItem Text="Delete" IconSource="{StaticResource DeleteIcon}"
               Background="Red" Invoked="Delete_Invoked"/>
</SwipeItems>
```

Элемент данных размещается в DataContext элемента управления SwipeControl. В коде можно получить доступ к элементу, который был сдвинут, получив свойство SwipeControl.DataContext из аргументов события, как показано ниже.

```csharp
 private void Delete_Invoked(SwipeItem sender, SwipeItemInvokedEventArgs args)
 {
     sampleList.Items.Remove(args.SwipeControl.DataContext);
 }
```

> [!NOTE]
> Здесь элементы были добавлены непосредственно к коллекции ListView.Items для простоты, чтобы удаление элемента выполнялось таким же образом. Если же элемент ListView.ItemsSource сохранен в коллекции, что происходит чаще, необходимо удалить элемент из исходной коллекции.

В этом конкретном случае происходит удаление элемента из списка, поэтому окончательное визуальное состояние сдвинутого элемента не является важным. Однако в ситуациях, когда вы просто хотите выполнить действие и затем снова свернуть меню сдвига, можно задать свойству [BehaviorOnInvoked](/uwp/api/windows.ui.xaml.controls.swipeitem.BehaviorOnInvoked) одно из значений перечисления [SwipeBehaviorOnInvoked](/uwp/api/windows.ui.xaml.controls.swipebehavioroninvoked).

- **Автоматически**
  - В режиме выполнения открытый элемент прокрутки останется открытым при его вызове.
  - В режиме показа открытый элемент прокрутки будет свернут при его вызове.
- **Close** (закрывать)
  - При вызове элемента команда сдвига всегда будет сворачиваться и возвращаться в стандартное состояние независимо от режима.
- **RemainOpen** (оставаться открытым)
  - При вызове элемента команда сдвига всегда будет оставаться открытой независимо от режима.

В примере ниже элемент прокрутки _reply_ (ответ) настроен на закрытие после его вызова.

```xaml
<SwipeItem Text="Reply" IconSource="{StaticResource ReplyIcon}"
           Invoked="Reply_Invoked"
           BehaviorOnInvoked = "Close"/>
```

## <a name="dos-and-donts"></a>Рекомендации

- Не используйте прокрутку в элементах FlipViews, Hub или Pivot. Такое сочетание может запутать пользователя из-за противоречивых направлений сдвига.
- Не объединяйте горизонтальный сдвиг с горизонтальной навигацией и вертикальный сдвиг с вертикальной навигацией.
- Следите за тем, чтобы сдвиг всегда выполнял одно и то же действие единообразно для всех родственных элементов, которые могут быть сдвинуты.
- Используйте сдвиг для основных действий, которые захочет выполнять пользователь.
- Используйте сдвиг для элементов, для которых одно действие повторяется многократно.
- Используйте горизонтальный сдвиг на широких элементах, а вертикальный сдвиг — на вертикальных элементах.
- Используйте краткие и точные текстовые метки.

## <a name="get-the-sample-code"></a>Получение примера кода

- [Пример из коллекции элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном режиме.

## <a name="related-articles"></a>Похожие статьи

- [Представления списка и сетки](listview-and-gridview.md)
- [Контейнеры и шаблоны элементов](item-containers-templates.md)
- [Обновление путем оттягивания](pull-to-refresh.md)
