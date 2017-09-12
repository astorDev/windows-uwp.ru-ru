---
author: Karl-Bridge-Microsoft
pm-contact: miguelrb
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.openlocfilehash: 8389d1f089d507a087693879a793b95572d7860b
ms.sourcegitcommit: 5ece992c31870df4c089360ef47501bd4ce14fa9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/22/2017
---
# <a name="managing-focus-navigation"></a>Управление перемещением фокуса

Многие варианты ввода, а именно с помощью клавиатуры, средств специальных возможностей, например экранного диктора Windows, геймпада и пульта дистанционного управления, имеют в своей основе общий механизм для перемещения визуального фокуса вокруг пользовательского интерфейса ваших приложений. Дополнительные сведения о визуальном фокусе и навигации см. в документах [Взаимодействие с помощью клавиатуры](keyboard-interactions.md) и [Проектирование для Xbox и телевизора](designing-for-tv.md#xy-focus-navigation-and-interaction).

Ниже описан способ ввода, независимый от приложения. Таким образом можно перемещать фокус по пользовательскому интерфейсу приложения и написать единый код, благодаря которому приложение будет отлично работать с несколькими типами ввода.

## <a name="navigation-strategies-properties-to-fine-tune-focus-movements"></a>Свойства стратегий навигации для тонкой настройки перемещения фокуса

Используйте свойства стратегии навигации XYFocus, чтобы указать, какой элемент управления должен получать фокус на основании нажатий клавиши со стрелками. Это следующие свойства:

-   XYFocusUpNavigationStrategy
-   XYFocusDownNavigationStrategy
-   XYFocusLeftNavigationStrategy
-   XYFocusRightNavigationStrategy

Эти свойства имеют значение типа **XYFocusNavigationStrategy**. Если установлено значение **Auto**, поведение элемента основано на его родительских элементах. Если для всех элементов установлено значение **Auto**, используется **Projection**.

Эти стратегии навигации применимы к клавиатуре, геймпаду и пульту дистанционного управления.

### <a name="projection"></a>Projection

Стратегия Projection перемещает фокус на первый элемент, встреченный при проецировании края текущего элемента, имеющего фокус, в направлении навигации.

> [!NOTE]
> Другие факторы, например ранее сфокусированный элемент и приближение к оси направления навигации, могут повлиять на результат.

![projection](images/keyboard/projection.png)

*Фокус перемещается с A к D вниз на основе проекции нижнего края A*

### <a name="navigationdirectiondistance"></a>NavigationDirectionDistance

Стратегия NavigationDirectionDistance перемещает фокус на элемент, наиболее близкий к оси направления навигации.

Края ограничивающего прямоугольника, соответствующий направлению перехода, расширяется и проецируется для определения потенциальных целевых объектов. Первый обнаруженный элемент считается целевым. При наличии нескольких кандидатов целевым элементом становится самый ближний из них. Если кандидатов по-прежнему несколько, кандидатом становится самый верхний и самый левый элемент.

![Расстояние по направлению навигации](images/keyboard/navigation-direction-distance.png)

*Фокус перемещается вниз от A к C, а затем от C к B*


### <a name="rectilineardistance"></a>RectilinearDistance

Стратегия RectilinearDistance перемещает фокус на ближайший элемент, который определяется по самому короткому 2D-расстоянию («манхэттенскому расстоянию»). Это расстояние рассчитывается путем сложения основного и дополнительного расстояния каждого потенциального кандидата. При одинаковом результате выбирается первый элемент слева, если направление вверх или вниз, или первый элемент вверху, если направление влево или вправо.

На следующем рисунке показано, что фокус с элемента А перемещается на элемент B, так как:

-   Расстояние (A, B, вниз) = 10 + 0 = 10
-   Расстояние (A, C, вниз) = 0 + 30 = 30
-   Расстояние (A, D, вниз) 30 + 0 = 30

![Расстояние по прямой](images/keyboard/rectilinear-distance.png)

*При использовании стратегии RectilinearDistance фокус, находящийся на элементе А, переместится на элемент B*

На следующем рисунке показано, как стратегия навигации применяется собственно к элементу, но не к дочерним элементам (в отличие от XYFocusKeyboardNavigation).

Например, если фокус находится на элементе E, при нажатии клавиши со стрелкой вправо фокус переместится на элемент F, если используется стратегия RectilinearDistance, но если фокус находится на элементе D, при нажатии клавиши со стрелкой вправо фокус переместится на элемент H при использовании стратегии Projection.

Обратите внимание, что стратегия основного контейнера (расстояние направления навигации) не применяется. Она используется, только когда фокус находится на элементе H, F или G.

![Разные стратегии навигации](images/keyboard/different-navigation-strategies.png)

*Разные варианты поведения фокуса, основанные на применяемой стратегии навигации*

Следующий пример имитирует поведение панели плиток в меню "Пуск" Windows 10. В данном случае при нажатии клавиш со стрелками вверх и вниз используется стратегия NavigationDirectionDistance, а при нажатии клавиш со стрелками влево и вправо используется стратегия Projection.

```XAML
<start:TileGridView
        XYFocusKeyboardNavigation ="Default"
        XYFocusUpNavigationStrategy=" NavigationDirectionDistance "
        XYFocusDownNavigationStrategy=" NavigationDirectionDistance "
        XYFocusLeftNavigationStrategy="Projection"
        XYFocusRightNavigationStrategy="Projection"
/>
```

## <a name="move-focus-programmatically"></a>Перемещение фокуса программными средствами

Для программного перемещения фокуса используйте метод [FocusManager.TryMoveFocus](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/Windows.UI.Xaml.Input.FocusManager.TryMoveFocus) или [FocusManager.FindNextElement](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/Windows.UI.Xaml.Input.FocusManager.FindNextFocusableElement).

TryMoveFocus устанавливает фокус, как если бы была нажата клавиша навигации.
FindNextElement возвращает элемент (DependencyObject), к которому переместился бы TryMoveFocus.

**ПРИМЕЧАНИЕ.** Рекомендуется использовать метод **FindNextElement** вместо **FindNextFocusableElement**. FindNextFocusableElement пытается вернуть объект UIElement, но если следующим фокусируемым элементом является Hyperlink, возвращается значение null, так как Hyperlink не является UIElement. Вместо этого метод FindNextElement возвращает DependencyObject.

### <a name="find-a-focus-candidate-in-a-scope"></a>Поиск кандидата для фокуса в области

FocusManager.FindNextElement и FocusManager.TryMoveFocus позволяют настраивать поведение следующего доступного для фокусировки элемента. Можно выполнять поиск в определенном дереве пользовательского интерфейса или исключить элементы из определенной области навигации.

Этот пример взят из реализации игры TicTacToe и показывает, как использовать методы FindNextElement и TryMoveFocus:

```XAML
<StackPanel Orientation="Horizontal"
                VerticalAlignment="Center"
                HorizontalAlignment="Center" >
        <Button Content="Start Game" />
        <Button Content="Undo Movement" />
        <Grid x:Name="TicTacToeGrid" KeyDown="OnKeyDown">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="50" />
                <ColumnDefinition Width="50" />
                <ColumnDefinition Width="50" />
            </Grid.ColumnDefinitions>
            <Grid.RowDefinitions>
                <RowDefinition Height="50" />
                <RowDefinition Height="50" />
                <RowDefinition Height="50" />
            </Grid.RowDefinitions>
            <myControls:TicTacToeCell Grid.Column="0" Grid.Row="0" x:Name="Cell00" />
            <myControls:TicTacToeCell Grid.Column="1" Grid.Row="0" x:Name="Cell10"/>
            <myControls:TicTacToeCell Grid.Column="2" Grid.Row="0" x:Name="Cell20"/>
            <myControls:TicTacToeCell Grid.Column="0" Grid.Row="1" x:Name="Cell01"/>
            <myControls:TicTacToeCell Grid.Column="1" Grid.Row="1" x:Name="Cell11"/>
            <myControls:TicTacToeCell Grid.Column="2" Grid.Row="1" x:Name="Cell21"/>
            <myControls:TicTacToeCell Grid.Column="0" Grid.Row="2" x:Name="Cell02"/>
            <myControls:TicTacToeCell Grid.Column="1" Grid.Row="2" x:Name="Cell22"/>
            <myControls:TicTacToeCell Grid.Column="2" Grid.Row="2" x:Name="Cell32"/>
        </Grid>
    </StackPanel>
```
```csharp
private void OnKeyDown(object sender, KeyRoutedEventArgs e)
        {
            DependencyObject candidate = null;

            var options = new FindNextElementOptions ()
            {
                SearchRoot = TicTacToeGrid,
                NavigationStrategy = NavigationStrategyMode.Heuristic
            };

            switch (e.Key)
            {
                case Windows.System.VirtualKey.Up:
                    candidate = FocusManager.FindNextElement(FocusNavigationDirection.Up, options);
                    break;
                case Windows.System.VirtualKey.Down:
                    candidate = FocusManager.FindNextElement(FocusNavigationDirection.Down, options);
                    break;
                case Windows.System.VirtualKey.Left:
                    candidate = FocusManager.FindNextElement(FocusNavigationDirection.Left, options);
                    break;
                case Windows.System.VirtualKey.Right:
                    candidate = FocusManager.FindNextElement(FocusNavigationDirection.Right, options);
                    break;
            }
         //Note you should also consider whether is a Hyperlink, WebView, or TextBlock.
            if (candidate != null && candidate is Control)
            {
                (candidate as Control).Focus(FocusState.Keyboard);
            }
        }
```

**ПРИМЕЧАНИЕ.** FocusNavigationDirection.Left и FocusNavigationDirection.Right являются логическими (близко и далеко) для поддержки сценариев с написанием справа налево. (Это соответствует остальной части Xaml.)

Поиск кандидатов для фокуса можно настроить с помощью класса FindNextElementOptions. У этого объекта имеются следующие свойства:

-   **SearchRoot** DependencyObject: определение области поиска кандидатов для дочерних элементов этого DependencyObject. Значение null указывает на полное визуальное дерево приложения.
-   **ExclusionRect** Rect: исключение из поиска тех элементов, которые во время отрисовки перекрываются по крайней мере на один пиксель с этим прямоугольником.
-   **HintRect** Rect: в качестве опорного элемента при вычислении потенциальных кандидатов используется элемент с фокусом. Этот прямоугольник дает разработчикам возможность указать другую ссылку вместо элемента с фокусом. «Воображаемый» прямоугольник используется только для вычислений. Он никогда не преобразуется в реальный прямоугольник и не будет добавлен в визуальное дерево.
-   **XYFocusNavigationStrategyOverride** XYFocusNavigationStrategyOverride: стратегия навигации, используемая для перемещения фокуса. Тип является перечислением со значениями None (нулевое), Auto, RectilinearDistance, NavigationDirectionDistance и Projection.
    **Важно!** **SearchRoot** не используется для расчета отображаемой области (геометрической) или получения кандидатов внутри нее. Поэтому, если преобразования применяются к потомкам DependencyObject и окажутся таким образом вне области направления, эти элементы по-прежнему будут считаться кандидатами.

Перегрузка параметров FindNextElement не может использоваться с навигацией по клавише табуляции и поддерживает только направленную навигацию.

На следующем рисунке показаны эти параметры.

Например, когда фокус находится на элементе B, вызов FindNextElement (с различными установленными параметрами, указывающими на перемещение фокуса вправо) устанавливает фокус на элементе I. Ниже перечислены причины.

1.  Ссылка является не элементом B, а элементом A из-за HintRect
2.  Элемент C исключается, так как потенциальный кандидат должен находиться на MyPanel (SearchRoot)
3.  Элемент F исключается, потому что перекрывает прямоугольник исключений

![Подсказки для навигации](images/keyboard/navigation-hints.png)

*Поведение фокуса на основе подсказок навигации*

### <a name="no-focus-candidate-available"></a>Нет доступного кандидата для фокуса

Событие UIElement.NoFocusCandidateFound возникает, когда пользователь пытается переместить фокус с помощью клавиши табуляции или клавиш со стрелками, но нет ни одного возможного кандидата в указанном направлении. Это событие не возникает для FocusManager.TryMoveFocus().

Поскольку это перенаправленное событие, оно передается от сфокусированного элемента наверх через последующие родительские объекты в корневой элемент дерева объектов. Таким образом можно обработать событие там, где это уместно.

<a name="split-view-code-sample"></a>В следующем примере показана сетка, которая открывает разделенное представление, когда пользователь пытается переместить фокус на левый или крайний левый фокусируемый элемент управления, как описано в [документации по проектированию для телевизора](designing-for-tv.md#navigation-pane).

```XAML
<Grid NoFocusCandidateFound="OnNoFocusCandidateFound">  ...</Grid>
```

```csharp
private void OnNoFocusCandidateFound (UIElement sender, NoFocusCandidateFoundEventArgs args)
{
            if(args.NavigationDirection == FocusNavigationDirection.Left)
            {
         if(args.InputDevice == FocusInputDeviceKind.Keyboard ||
            args.InputDevice == FocusInputDeviceKind.GameController )
                {
                OpenSplitPaneView();
                }
            args.Handled = true;
            }
}
```

## <a name="focus-events"></a>События фокуса

События [UIElement.GotFocus](http://msdn.microsoft.com/en-us/library/windows/apps/xaml/Windows.UI.Xaml.UIElement.GotFocus) и UIElement.LostFocus возникают, когда элемент управления получает или, соответственно, теряет фокус. Это событие не возникает для FocusManager.TryMoveFocus().

Поскольку это перенаправленное событие, оно передается от сфокусированного элемента наверх через последующие родительские объекты в корневой элемент дерева объектов. Таким образом можно обработать событие там, где это уместно.

События **UIElement.GettingFocus** и **UIElement.LosingFocus** также передаются от элемента управления, но *до* изменения фокуса. Это дает приложению возможность перенаправить или отменить изменение фокуса.

Обратите внимание, что события GettingFocus и LosingFocus синхронные, тогда как события GotFocus и LostFocus асинхронные. Например, при перемещении фокуса из-за вызова приложением метода Control.Focus() GettingFocus возникает во время вызова, а GotFocus возникает через некоторое время после вызова.

UIElements можно подключить на события GettingFocus и LosingFocus и изменить целевой объект (используя свойство NewFocusedElement) до перемещения фокуса. Даже если целевой объект был изменен, событие по-прежнему передается вверх, а другой родительский элемент может изменить целевой объект снова.

Чтобы избежать проблем с повторным входом, создаются исключения при попытке переместить фокус (с помощью FocusManager.TryMoveFocus или Control.Focus) во время передачи этих событий.

Такие события возникают независимо от причины перемещения фокуса (например, навигации с помощью клавиши табуляции, навигации XYFocus, программного).

На следующем рисунке показано, как при перемещении вправо от элемента A XYFocus выбирает LVI4 в качестве кандидата. LVI4 затем запускает событие GettingFocus, в котором у ListView есть возможность для переназначения фокуса на LVI3.

![События фокуса](images/keyboard/focus-events.png)

*События фокуса*

В этом примере кода показано, как обрабатывать событие OnGettingFocus и перенаправлять фокус на основе местоположения предыдущего фокуса.

```XAML
<StackPanel Orientation="Horizontal">
    <Button Content="A" />
    <ListView x:Name="MyListView" SelectedIndex="2" GettingFocus="OnGettingFocus">
        <ListViewItem>LV1</ListViewItem>
        <ListViewItem>LV2</ListViewItem>
        <ListViewItem>LV3</ListViewItem>
        <ListViewItem>LV4</ListViewItem>
        <ListViewItem>LV5</ListViewItem>
    </ListView>
</StackPanel>
```

```csharp
private void OnGettingFocus(UIElement sender, GettingFocusEventArgs args)
  {

                 //Redirect the focus only when the focus comes from outside of the ListView.
       // move the focus to the selected item
            if (MyListView.SelectedIndex != -1 && IsNotAChildOf(MyListView, args.OldFocusedElement))
            {
                var selectedContainer = MyListView.ContainerFromItem(MyListView.SelectedItem);
                if (args.FocusState == FocusState.Keyboard && args.NewFocusedElement != selectedContainer)
                {
                    args.NewFocusedElement = MyListView.ContainerFromItem(MyListView.SelectedItem);
                    args.Handled = true;
                }
            }        
  }
```

В этом примере кода показано, как обрабатывать событие OnLosingFocus для объекта CommandBar и ожидать установки фокуса до закрытия раскрывающегося меню.

```XAML
<CommandBar x:Name="MyCommandBar" LosingFocus="OnLosingFocus">
     <AppBarButton Icon="Back" Label="Back" />
     <AppBarButton Icon="Stop" Label="Stop" />
     <AppBarButton Icon="Play" Label="Play" />
     <AppBarButton Icon="Forward" Label="Forward" />

     <CommandBar.SecondaryCommands>
         <AppBarButton Icon="Like" Label="Like" />
         <AppBarButton Icon="Share" Label="Share" />
     </CommandBar.SecondaryCommands>
 </CommandBar>
```

```csharp
private void OnLosingFocus(UIElement sender, LosingFocusEventArgs args)
{
          if (MyCommandBar.IsOpen == true && IsNotAChildOf(MyCommandBar, args.NewFocusedElement))
          {
              args.Cancel = true;
              args.Handled = true;
          }
}
```

### <a name="losingfocus-and-gettingfocus-event-order"></a>Порядок событий LosingFocus и GettingFocus

LosingFocus и GettingFocus являются синхронными событиями, поэтому фокус не будет перемещаться, когда эти события передаются. Но события LostFocus и GotFocus асинхронные, и поэтому нет гарантии, что фокус не будет перемещаться снова перед выполнением обработчика. Гарантируется только то, что обработчик LostFocus выполняется до обработчика GotFocus.

Ниже описан порядок выполнения.

1.  Синхронизация LosingFocus: если LosingFocus снова устанавливает фокус на элементе, который терял фокус или устанавливал для параметра Cancel значение true, дальнейшие события не происходят
2.  Синхронизация GettingFocus: если GettingFocus снова устанавливает фокус на элементе, который терял фокус или устанавливал для параметра Cancel значение true, дальнейшие события не происходят
3.  Асинхронный LostFocus
4.  Асинхронный GotFocus

## <a name="find-the-first-and-last-focusable-element-a-namefindfirstfocusableelement"></a>Поиск первого и последнего фокусируемого элемента <a name="findfirstfocusableelement">

Методы **FocusManager.FindFirstFocusableElement** и **FocusManager.FindLastFocusableElement** позволяют перемещать фокус к первому или последнему фокусируемому элементу в рамках объекта (дерево элементов UIElement или дерево текста TextElement). Укажите область при вызове этих методов. Если аргумент имеет значение null, область будет текущим окном.

**Примечание** Эти методы могут возвращать значение null, если в области нет фокусируемых элементов.

<a name="popup-ui-code-sample"></a>В следующем примере кода показано, как указать, что кнопки CommandBar имеют поведение направления с обтеканием, описанное в статье [Взаимодействие с помощью клавиатуры](keyboard-interactions.md#popup-ui).

```XAML
<CommandBar x:Name="MyCommandBar" LosingFocus="OnLosingFocus">
            <AppBarButton Icon="Back" Label="Back" />
            <AppBarButton Icon="Stop" Label="Stop" />
            <AppBarButton Icon="Play" Label="Play" />
            <AppBarButton Icon="Forward" Label="Forward" />

            <CommandBar.SecondaryCommands>
                <AppBarButton Icon="Like" Label="Like" />
                <AppBarButton Icon="ReShare" Label="Share" />
            </CommandBar.SecondaryCommands>
</CommandBar>
```

```csharp
private void OnLosingFocus(UIElement sender, LosingFocusEventArgs args)
        {
            if (IsNotAChildOf(MyCommandBar, args.NewFocussedElement))
            {
                DependencyObject candidate = null;
                if (args.Direction == FocusNavigationDirection.Left)
                {
                    candidate = FocusManager.FindLastFocusableElement(MyCommandBar);
                }
                else if (args.Direction == FocusNavigationDirection.Right)
                {
                    candidate = FocusManager.FindFirstFocusableElement(MyCommandBar);
                }
                if (candidate != null)
                {
                    args.NewFocusedElement = candidate;
                    args.Handled = true;
                }
            }
        }
```
