---
author: Jwmsft
Description: Use the pull-to-refresh control to get new content into a list.
title: Обновление путем оттягивания
label: Pull-to-refresh
template: detail.hbs
ms.author: jimwalk
ms.date: 03/07/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: aaeb1e74-b795-4015-bf41-02cb1d6f467e
pm-contact: predavid
design-contact: kimsea
dev-contact: stpete
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 8b1dd6bd1bc165a79ba123c94e63e1dcfa58ec21
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6985456"
---
# <a name="pull-to-refresh"></a>Обновление путем оттягивания

Обновление путем оттягивания позволяет пользователю потянуть список данных вниз, чтобы получить дополнительные данные. Эта функция широко используется на устройствах с сенсорным экраном. Вы можете использовать представленные в этом разделе API-интерфейсы для реализации запроса на обновление путем оттягивания в своем приложении.

> **Важные API-интерфейсы**: [RefreshContainer](/uwp/api/windows.ui.xaml.controls.refreshcontainer), [RefreshVisualizer](/uwp/api/windows.ui.xaml.controls.refreshvisualizer)

![Изображение "Обновление путем оттягивания"](images/Pull-To-Refresh.gif)

## <a name="is-this-the-right-control"></a>Выбор подходящего элемента управления

Используйте функцию обновления путем оттягивания при работе со списком или сеткой с данными, которые пользователю может потребоваться регулярно обновлять, и если ваше приложение предназначено для работы на устройствах, рассчитанным на сенсорное управление.

Можно также использовать [RefreshVisualizer](/uwp/api/windows.ui.xaml.controls.refreshvisualizer) для создания согласованных возможностей обновления, вызываемых другими способами, например, с помощью кнопки "Обновить".

## <a name="refresh-controls"></a>Элементу управления обновлением

Функция обновление путем оттягивания реализуется двумя элементами управления.

- **RefreshContainer** — ContentControl, предоставляющий оболочку для взаимодействия типа "обновление путем оттягивания". Он обрабатывает сенсорное взаимодействие и управляет состоянием внутреннего визуализатора обновления.
- **RefreshVisualizer** — инкапсулирует визуализацию обновления, описанную в следующем разделе.

Основным элементом управления является **RefreshContainer**, который размещается в качестве оболочки содержимого, которое пользователь хочет обновить. RefreshContainer работает только с сенсорным вводом, и поэтому мы также рекомендуем использовать кнопку "Обновить" для пользователей, у которых нет сенсорного интерфейса. Кнопку "Обновить" можно разместить в подходящем месте в приложении — на панели команд или в месте, находящемся рядом с обновляемой поверхностью.

## <a name="refresh-visualization"></a>Визуализация обновления

Визуализация обновления по умолчанию — это циклический индикатор хода выполнения, который сообщает о том, когда произойдет обновление. После чего инициируется процесс обновления. Визуализатор обновления имеет 5 состояний.

 Расстояние, на которое пользователь должен оттянуть список вниз для инициации обновления, называется _threshold_ (пороговое значение). Визуализатор [State](/uwp/api/windows.ui.xaml.controls.refreshvisualizer.State) определяется по состоянию оттягивания в отношении этого порогового значения. Допустимые значения находятся в перечислении [RefreshVisualizerState](/uwp/api/windows.ui.xaml.controls.refreshvisualizerstate).

### <a name="idle"></a>Состояние Idle (в покое)

Состоянием визуализатора по умолчанию является **Idle**. Пользователь не взаимодействует с RefreshContainer посредством сенсорного ввода и обновление не выполняется.

Визуальные признаки работы визуализатора обновления отсутствуют.

### <a name="interacting"></a>Состояние Interacting (взаимодействие)

Когда пользователь оттягивает список в направлении, заданном свойством PullDirection, и до достижения порогового значения, визуализатор находится в состоянии **Interacting**.

- Если пользователь отпускает элемент управления в этом состоянии, элемент управления возвращается в состояние **Idle**.

    ![Состояние функции обновления путем оттягивания до достижения порогового значения](images/ptr-prethreshold.png)

    Визуально значок отображается как отключенный (прозрачность 60%). Кроме того, значок производит один полный оборот с помощью действия прокрутки.

- Если пользователь оттянет список, превысив значение порогового значения, визуализатор перейдет из состояния **Interacting** в состояние **Pending**.

    ![Состояние функции обновления путем оттягивания при достижении порогового значения](images/ptr-atthreshold.png)

    Визуально прозрачность значка составит 100% и он будет меняться в размере до 150% и обратно до 100% во время процесса перехода состояний.

### <a name="pending"></a>Состояние Pending (ожидание)

Когда пользователь оттягивает список, превысив пороговое значение, визуализатор переходит в состояние **Pending**.

- Когда пользователь перемещает список обратно, выше порогового значения, не отпуская его, визуализатор переходит в состояние **Interacting**.
- Если пользователь отпускает список, отправляется запрос на обновление и визуализатор переходит в состояние **Refreshing**.

![Состояние функции обновления путем оттягивания после достижения порогового значения](images/ptr-postthreshold.png)

Визуально размер и прозрачность значка составляют 100%. В этом состоянии значок продолжает перемещаться вниз с помощью действия прокрутки, но больше не вращается.

### <a name="refreshing"></a>Состояние Refreshing (обновление)

Когда пользователь отпускает визуализатор за пороговое значение, он переходит в состояние **Refreshing**.

При входе в это состояние вызывается событие **RefreshRequested**. Это сигнал для запуска обновления содержимого приложения. Аргументы события ([RefreshRequestedEventArgs](/uwp/api/windows.ui.xaml.controls.refreshrequestedeventargs)) содержат объект [Deferral](/uwp/api/windows.foundation.deferral), который следует обработать в обработчике событий. Затем отсрочку следует пометить как завершенную, когда код завершил выполнение обновления.

По завершении обновления визуализатор возвращается в состояние **Idle**.

Визуально значок возвращается обратно в пороговое положение и вращается на протяжении процесса обновления. Этот цикл вращения используется для демонстрации хода выполнения обновления и заменяется анимацией появляющегося содержимого.

### <a name="peeking"></a>Состояние Peeking (просмотр)

Когда пользователь оттягивает список в направлении обновления из начальной точки, в которой обновление запрещено, визуализатор переходит в состояние **Peeking**. Как правило, это происходит, если элемент ScrollViewer не находится в положении 0, когда пользователь начинает оттягивать список.

- Если пользователь отпускает элемент управления в этом состоянии, элемент управления возвращается в состояние **Idle**.

## <a name="pull-direction"></a>Направление оттягивания

По умолчанию пользователь оттягивает список сверху вниз, чтобы обновить его. Если у вас есть список или сетка с разной ориентацией, следует изменить направление оттягивания, чтобы обеспечить соответствие контейнера обновления.

Свойство [PullDirection](/uwp/api/windows.ui.xaml.controls.refreshcontainer.PullDirection) принимает одно из следующих значений [RefreshPullDirection](/uwp/api/windows.ui.xaml.controls.refreshpulldirection): **BottomToTop**, **TopToBottom**, **RightToLeft** или **LeftToRight**.

При изменении направления оттягивания начальное положение индикатора хода выполнения визуализатора начинает автоматически вращаться, и таким образом стрелка размещается в положении, соответствующем направлению оттягивания. При необходимости можно изменить свойство [RefreshVisualizer.Orientation](/uwp/api/windows.ui.xaml.controls.refreshvisualizer.Orientation), чтобы переопределить автоматическое поведение. В большинстве случаев мы рекомендуем оставлять значение по умолчанию: **Auto**.

## <a name="implement-pull-to-refresh"></a>Реализация обновления путем оттягивания

Чтобы добавить функцию обновления путем оттягивания в список, нужно выполнить всего несколько действий.

1. Перенесите список в элемент управления **RefreshContainer**.
1. Обработайте событие **RefreshRequested**, чтобы обновить содержимое.
1. При необходимости запустите обновление, вызвав метод **RequestRefresh** (например, нажатием кнопки).

> [!NOTE]
> Можно создать отдельный экземпляр RefreshVisualizer. Тем не менее рекомендуется переносить содержимое в RefreshContainer и использовать RefreshVisualizer, предоставляемый свойством RefreshContainer.Visualizer даже для сценариев без сенсорного ввода. В этой статье предполагается, что визуализатор всегда берется из контейнера обновления.

> Кроме того, используйте члены контейнера обновления RequestRefresh и RefreshRequested для удобства. `refreshContainer.RequestRefresh()` эквивалентен `refreshContainer.Visualizer.RequestRefresh()` и вызовет события RefreshContainer.RefreshRequested и RefreshVisualizer.RefreshRequested.

### <a name="request-a-refresh"></a>Запрос на обновление

Контейнер обновления обрабатывает сенсорные взаимодействия, чтобы дать пользователю возможность обновить содержимое, с помощью сенсорного ввода. Мы рекомендуем реализовывать другие возможности для интерфейсов без сенсорного ввода, например, кнопку "Обновить" или управление голосом.

Чтобы запустить обновление, вызовите метод [RequestRefresh](/uwp/api/windows.ui.xaml.controls.refreshcontainer.RequestRefresh).

```csharp
// See the Examples section for the full code.
private void RefreshButtonClick(object sender, RoutedEventArgs e)
{
    RefreshContainer.RequestRefresh();
}
```

При вызове RequestRefresh визуализатор напрямую переходит из состояния **Idle** в состояние **Refreshing**.

### <a name="handle-a-refresh-request"></a>Обработка запроса на обновление

Чтобы получить обновленное содержимое, когда это необходимо, обработайте событие RefreshRequested. В обработчике событий, вам потребуется добавить код для конкретного приложения, чтобы получить обновленное содержимое.

Аргументы события ([RefreshRequestedEventArgs](/uwp/api/windows.ui.xaml.controls.refreshrequestedeventargs)) содержат объект [отсрочки](/uwp/api/windows.foundation.deferral). Получите дескриптор отсрочки в обработчике событий. Затем отсрочку следует пометить как завершенную, когда код завершил выполнение обновления.

```csharp
// See the Examples section for the full code.
private async void RefreshContainer_RefreshRequested(RefreshContainer sender, RefreshRequestedEventArgs args)
{
    // Respond to a request by performing a refresh and using the deferral object.
    using (var RefreshCompletionDeferral = args.GetDeferral())
    {
        // Do some async operation to refresh the content

         await FetchAndInsertItemsAsync(3);

        // The 'using' statement ensures the deferral is marked as complete.
        // Otherwise, you'd call
        // RefreshCompletionDeferral.Complete();
        // RefreshCompletionDeferral.Dispose();
    }
}
```

### <a name="respond-to-state-changes"></a>Реагирование на изменения состояния

Вы можете реагировать на изменения в состоянии визуализатора при необходимости. Например, для предотвращения отправки нескольких запросов на обновление можно отключить кнопку "Обновить", когда визуализатор обновляется.

```csharp
// See the Examples section for the full code.
private void Visualizer_RefreshStateChanged(RefreshVisualizer sender, RefreshStateChangedEventArgs args)
{
    // Respond to visualizer state changes.
    // Disable the refresh button if the visualizer is refreshing.
    if (args.NewState == RefreshVisualizerState.Refreshing)
    {
        RefreshButton.IsEnabled = false;
    }
    else
    {
        RefreshButton.IsEnabled = true;
    }
}
```

## <a name="examples"></a>Примеры.

### <a name="using-a-scrollviewer-in-a-refreshcontainer"></a>Использование ScrollViewer в RefreshContainer

В этом примере показано, как использовать функцию обновления путем оттягивания со средством просмотра прокрутки.

```xaml
<RefreshContainer>
    <ScrollViewer VerticalScrollMode="Enabled"
                  VerticalScrollBarVisibility="Auto"
                  HorizontalScrollBarVisibility="Auto">
 
        <!-- Scrollviewer content -->

    </ScrollViewer>
</RefreshContainer>
```

### <a name="adding-pull-to-refresh-to-a-listview"></a>Добавление функции обновления путем оттягивания в ListView

В этом примере показано, как использовать функцию обновления путем оттягивания с представлением списка.

```xaml
<StackPanel Margin="0,40" Width="280">
    <CommandBar OverflowButtonVisibility="Collapsed">
        <AppBarButton x:Name="RefreshButton" Click="RefreshButtonClick"
                      Icon="Refresh" Label="Refresh"/>
        <CommandBar.Content>
            <TextBlock Text="List of items" 
                       Style="{StaticResource TitleTextBlockStyle}"
                       Margin="12,8"/>
        </CommandBar.Content>
    </CommandBar>

    <RefreshContainer x:Name="RefreshContainer">
        <ListView x:Name="ListView1" Height="400">
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:ListItemData">
                    <Grid Height="80">
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="*" />
                        </Grid.RowDefinitions>
                        <TextBlock Text="{x:Bind Path=Header}"
                                   Style="{StaticResource SubtitleTextBlockStyle}"
                                   Grid.Row="0"/>
                        <TextBlock Text="{x:Bind Path=Date}"
                                   Style="{StaticResource CaptionTextBlockStyle}"
                                   Grid.Row="1"/>
                        <TextBlock Text="{x:Bind Path=Body}"
                                   Style="{StaticResource BodyTextBlockStyle}"
                                   Grid.Row="2"
                                   Margin="0,4,0,0" />
                    </Grid>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </RefreshContainer>
</StackPanel>
```

```csharp
public sealed partial class MainPage : Page
{
    public ObservableCollection<ListItemData> Items { get; set; } 
        = new ObservableCollection<ListItemData>();

    public MainPage()
    {
        this.InitializeComponent();

        Loaded += MainPage_Loaded;
        ListView1.ItemsSource = Items;
    }

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        Loaded -= MainPage_Loaded;
        RefreshContainer.RefreshRequested += RefreshContainer_RefreshRequested;
        RefreshContainer.Visualizer.RefreshStateChanged += Visualizer_RefreshStateChanged;

        // Add some initial content to the list.
        await FetchAndInsertItemsAsync(2);
    }

    private void RefreshButtonClick(object sender, RoutedEventArgs e)
    {
        RefreshContainer.RequestRefresh();
    }

    private async void RefreshContainer_RefreshRequested(RefreshContainer sender, RefreshRequestedEventArgs args)
    {
        // Respond to a request by performing a refresh and using the deferral object.
        using (var RefreshCompletionDeferral = args.GetDeferral())
        {
            // Do some async operation to refresh the content

            await FetchAndInsertItemsAsync(3);

            // The 'using' statement ensures the deferral is marked as complete.
            // Otherwise, you'd call
            // RefreshCompletionDeferral.Complete();
            // RefreshCompletionDeferral.Dispose();
        }
    }

    private void Visualizer_RefreshStateChanged(RefreshVisualizer sender, RefreshStateChangedEventArgs args)
    {
        // Respond to visualizer state changes.
        // Disable the refresh button if the visualizer is refreshing.
        if (args.NewState == RefreshVisualizerState.Refreshing)
        {
            RefreshButton.IsEnabled = false;
        }
        else
        {
            RefreshButton.IsEnabled = true;
        }
    }

    // App specific code to get fresh data.
    private async Task FetchAndInsertItemsAsync(int updateCount)
    {
        for (int i = 0; i < updateCount; ++i)
        {
            // Simulate delay while we go fetch new items.
            await Task.Delay(1000);
            Items.Insert(0, GetNextItem());
        }
    }

    private ListItemData GetNextItem()
    {
        return new ListItemData()
        {
            Header = "Header " + DateTime.Now.Second.ToString(),
            Date = DateTime.Now.ToLongDateString(),
            Body = DateTime.Now.ToLongTimeString()
        };
    }
}

public class ListItemData
{
    public string Header { get; set; }
    public string Date { get; set; }
    public string Body { get; set; }
}
```

## <a name="related-articles"></a>Смежные разделы

- [Взаимодействия с помощью сенсорного ввода](../input/touch-interactions.md)
- [Представления списка и сетки](listview-and-gridview.md)
- [Контейнеры и шаблоны элементов](item-containers-templates.md)
- [Анимация с помощью выражений](../../composition/composition-animation.md)
