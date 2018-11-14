---
author: Karl-Bridge-Microsoft
Description: Respond to keystroke actions from hardware or software keyboards in your apps using both keyboard and class event handlers.
title: События клавиатуры
ms.assetid: ac500772-d6ed-4a3a-825b-210a9c3c8f59
label: Keyboard events
template: detail.hbs
keywords: клавиатура, геймпад, пульт управления, специальные возможности, навигация, фокус, текст, ввод, взаимодействия с пользователем, клавиша вверх, клавиша вниз
ms.author: kbridge
ms.date: 03/29/2017
ms.topic: article
pm-contact: chigy
design-contact: kimsea
dev-contact: niallm
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 4a9abc16a4992dedead598f96061811c82c5a5c5
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6200211"
---
# <a name="keyboard-events"></a>События клавиатуры

## <a name="keyboard-events-and-focus"></a>События клавиатуры и фокус

Следующие событие клавиатуры могут возникнуть при использовании как аппаратной, так и сенсорной клавиатуры.

| Событие                                      | Описание                    |
|--------------------------------------------|--------------------------------|
| [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) | Происходит, когда клавиша нажата.  |
| [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942)     | Происходит, когда клавиша отпущена. |

> [!IMPORTANT]
> Некоторые элементы управления среды выполнения Windows обрабатывают события ввода внутренне. В этом случае может показаться, что событие ввода не происходит, поскольку прослушиватель событий не вызывает связанный обработчик. Обычно это подмножество клавиш обрабатывается обработчиком классов для обеспечения встроенной поддержки базовых специальных возможностей клавиатуры. Например, класс [**Button**](https://msdn.microsoft.com/library/windows/apps/br209265) переопределяет события [**OnKeyDown**](https://msdn.microsoft.com/library/windows/apps/hh967982) для клавиш ПРОБЕЛ и ВВОД (а также [**OnPointerPressed**](https://msdn.microsoft.com/library/windows/apps/hh967989)) и перенаправляет их в событие [**Click**](https://msdn.microsoft.com/library/windows/apps/br227737) элемента управления. Если нажатие клавиши обрабатывается классом элемента управления, события [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) и [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) не вызываются.  
> Это предоставляет эквивалентную встроенную клавиатуру для вызова кнопки, что похоже на касание пальцем или щелчок мышью. Клавиши, отличные от клавиш ПРОБЕЛ и ВВОД, вызывают события [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) и [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942). Дополнительные сведения об обработке событий на базе класса можно найти в статье [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584) (в частности, в разделе "Обработчики событий ввода в элементах управления").


Элементы пользовательского интерфейса создают события клавиатуры только при наличии у них фокуса ввода. Отдельный элемент управления получает фокус, когда пользователь щелкает этот элемент или касается его непосредственно в макете либо переходит на него с помощью клавиши Tab по последовательности табуляции в пределах области размещения содержимого.

Также можно принудительно установить фокус, вызвав метод [**Focus**](https://msdn.microsoft.com/library/windows/apps/hh702161) элемента управления. Это необходимо при реализации сочетаний клавиш, поскольку по умолчанию фокус клавиатуры не устанавливается при загрузке пользовательского интерфейса. Дополнительные сведения см. в разделе **Пример использования сочетания клавиш** далее в этой теме.

Для того чтобы элемент управления получил фокус, он должен быть включенным, видимым, а его свойства [**IsTabStop**](https://msdn.microsoft.com/library/windows/apps/br209422) и [**HitTestVisible**](https://msdn.microsoft.com/library/windows/apps/br208933) должны иметь значение **true**. Для большинства элементов управления это состояние по умолчанию. При наличии у элемента управления фокуса ввода он может вызывать события клавиатурного ввода и реагировать на них, как описано далее в этой теме. Также вы можете реагировать на получение или потерю фокуса элементом управления, обрабатывая события [**GotFocus**](https://msdn.microsoft.com/library/windows/apps/br208927) и [**LostFocus**](https://msdn.microsoft.com/library/windows/apps/br208943).

По умолчанию последовательность табуляции для элементов управления аналогична порядку, в котором они указаны в XAML (Extensible Application Markup Language). Однако этот порядок можно изменить с помощью свойства [**TabIndex**](https://msdn.microsoft.com/library/windows/apps/br209461). Дополнительные сведения см. в разделе [Реализация специальных возможностей клавиатуры](https://msdn.microsoft.com/library/windows/apps/hh868161).

## <a name="keyboard-event-handlers"></a>Обработчики событий клавиатуры


Обработчик событий ввода реализует делегат, который предоставляет следующие сведения:

-   Отправитель события. Отправитель сообщает, к какому объекту присоединен обработчик событий.
-   Данные события. Для событий клавиатуры эти данные представляют собой экземпляр [**KeyRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943072). Делегатом для обработчиков является [**KeyEventHandler**](https://msdn.microsoft.com/library/windows/apps/br227904). Наиболее значимыми свойствами **KeyRoutedEventArgs** в большинстве сценариев использования обработчиков являются [**Key**](https://msdn.microsoft.com/library/windows/apps/hh943074) и, возможно, [**KeyStatus**](https://msdn.microsoft.com/library/windows/apps/hh943075).
-   [**OriginalSource**](https://msdn.microsoft.com/library/windows/apps/br208810). Поскольку события клавиатуры являются перенаправленными событиями, данные события включают **OriginalSource**. Если вы намеренно разрешаете восходящую маршрутизацию событий в дереве объектов, проблемы более вероятны с **OriginalSource**, а не с отправителем. Однако это зависит от разработки. Дополнительные сведения о возможности использования **OriginalSource** вместо отправителя см. в разделе "Перенаправленные события клавиатуры" этой статьи или в статье [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

### <a name="attaching-a-keyboard-event-handler"></a>Присоединение обработчика событий клавиатуры

Функции обработчика событий клавиатуры можно прикрепить к любому объекту, включающему это событие в качестве члена. К таким объектам относятся и любые производные классы [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911). В следующем примере XAML показано, как присоединять обработчики для события [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) элемента [**Grid**](https://msdn.microsoft.com/library/windows/apps/br242704).

```xaml
<Grid KeyUp="Grid_KeyUp">
  ...
</Grid>
```

Кроме того, обработчик событий можно прикрепить и в коде. Дополнительные сведения см. в разделе [Общие сведения о событиях и перенаправленных событиях](https://msdn.microsoft.com/library/windows/apps/mt185584).

### <a name="defining-a-keyboard-event-handler"></a>Определение обработчика событий клавиатуры

В следующем примере показано неполное определение обработчика событий [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942), прикрепленного в предыдущем примере.

```csharp
void Grid_KeyUp(object sender, KeyRoutedEventArgs e)
{
    //handling code here
}
```

```vb
Private Sub Grid_KeyUp(ByVal sender As Object, ByVal e As KeyRoutedEventArgs)
    ' handling code here
End Sub
```

```c++
void MyProject::MainPage::Grid_KeyUp(
  Platform::Object^ sender,
  Windows::UI::Xaml::Input::KeyRoutedEventArgs^ e)
  {
      //handling code here
  }
```

### <a name="using-keyroutedeventargs"></a>Использование KeyRoutedEventArgs

Все события клавиатуры используют [**KeyRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943072) для указания данных события. Объект **KeyRoutedEventArgs** содержит следующие свойства.

-   [**Клавиша**](https://msdn.microsoft.com/library/windows/apps/hh943074)
-   [**KeyStatus**](https://msdn.microsoft.com/library/windows/apps/hh943075)
-   [**Handled**](https://msdn.microsoft.com/library/windows/apps/hh943073)
-   [**OriginalSource**](https://msdn.microsoft.com/library/windows/apps/br208810) (наследуется от [**RoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/br208809))

### <a name="key"></a>Ключ

Событие [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) создается, когда нажата клавиша. Подобным образом событие [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) создается, когда клавиша отпущена. Обычно прослушивание событий выполняется для обработки определенного значения клавиши. Чтобы определить, какая клавиша нажата или отпущена, проверьте значение [**Key**](https://msdn.microsoft.com/library/windows/apps/hh943074) в данных события. **Key** возвращает значение [**VirtualKey**](https://msdn.microsoft.com/library/windows/apps/br241812) value. Перечисление **VirtualKey** содержит все поддерживаемые клавиши.

### <a name="modifier-keys"></a>Клавиши-модификаторы

Клавиши-модификаторы — это такие клавиши, как CTRL или SHIFT, которые пользователи обычно нажимают в сочетании с другими клавишами. В вашем приложении такие сочетания могут использоваться для вызова различных команд.

Чтобы обнаружить сочетание клавиш, используйте код в обработчиках событий [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) и [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942). Затем вы можете отследить, какие из интересующих вас клавиш-модификаторов нажаты. Если событие клавиатуры связано не с клавишей-модификатором, можно проверить, не нажата ли одновременно клавиша-модификатор.

> [!NOTE]
> Клавиша ALT представлена значением **VirtualKey.Menu**.

 

### <a name="shortcut-keys-example"></a>Пример использования сочетаний клавиш


В следующем примере показано, как реализовать сочетания клавиш. В этом примере пользователь может управлять воспроизведением мультимедиа с помощью кнопок "Воспроизведение", "Пауза" или "Остановка" либо клавиш Ctrl+P, Ctrl+A и Ctrl+S. XAML-код кнопки отображает сочетания клавиш с помощью всплывающих подсказок и свойств [**AutomationProperties**](https://msdn.microsoft.com/library/windows/apps/br209081) в подписях кнопок. Такое самодокументирование важно для увеличения удобства вашего приложения и его доступности для людей с ограниченными возможностями. Дополнительную информацию см. в статье [Специальные возможности клавиатуры](https://msdn.microsoft.com/library/windows/apps/mt244347).

Обратите также внимание, что страница при загрузке устанавливает фокус ввода на себя. Без этого действия никакой элемент управления не получает первоначальный фокус ввода, и приложение не вызывает событие ввода до тех пор, пока пользователь вручную не установит фокус ввода (например, перейдя с помощью клавиши TAB или щелкнув элемент управления).

```xaml
<Grid KeyDown="Grid_KeyDown">

  <Grid.RowDefinitions>
    <RowDefinition Height="Auto" />
    <RowDefinition Height="Auto" />
  </Grid.RowDefinitions>

  <MediaElement x:Name="DemoMovie" Source="xbox.wmv"
    Width="500" Height="500" Margin="20" HorizontalAlignment="Center" />

  <StackPanel Grid.Row="1" Margin="10"
    Orientation="Horizontal" HorizontalAlignment="Center">

    <Button x:Name="PlayButton" Click="MediaButton_Click"
      ToolTipService.ToolTip="Shortcut key: Ctrl+P"
      AutomationProperties.AcceleratorKey="Control P">
      <TextBlock>Play</TextBlock>
    </Button>

    <Button x:Name="PauseButton" Click="MediaButton_Click"
      ToolTipService.ToolTip="Shortcut key: Ctrl+A"
      AutomationProperties.AcceleratorKey="Control A">
      <TextBlock>Pause</TextBlock>
    </Button>

    <Button x:Name="StopButton" Click="MediaButton_Click"
      ToolTipService.ToolTip="Shortcut key: Ctrl+S"
      AutomationProperties.AcceleratorKey="Control S">
      <TextBlock>Stop</TextBlock>
    </Button>

  </StackPanel>

</Grid>
```

```c++
//showing implementations but not header definitions
void MainPage::OnNavigatedTo(NavigationEventArgs^ e)
{
    (void) e;    // Unused parameter
    this->Loaded+=ref new RoutedEventHandler(this,&amp;MainPage::ProgrammaticFocus);
}
void MainPage::ProgrammaticFocus(Object^ sender, RoutedEventArgs^ e) {
    this->Focus(Windows::UI::Xaml::FocusState::Programmatic);
}

void KeyboardSupport::MainPage::MediaButton_Click(Platform::Object^ sender, Windows::UI::Xaml::RoutedEventArgs^ e)
{
    FrameworkElement^ fe = safe_cast<FrameworkElement^>(sender);
    if (fe->Name == "PlayButton") {DemoMovie->Play();}
    if (fe->Name == "PauseButton") {DemoMovie->Pause();}
    if (fe->Name == "StopButton") {DemoMovie->Stop();}
}


bool KeyboardSupport::MainPage::IsCtrlKeyPressed()
{
    auto ctrlState = CoreWindow::GetForCurrentThread()->GetKeyState(VirtualKey::Control);
    return (ctrlState & CoreVirtualKeyStates::Down) == CoreVirtualKeyStates::Down;
}

void KeyboardSupport::MainPage::Grid_KeyDown(Platform::Object^ sender, Windows::UI::Xaml::Input::KeyRoutedEventArgs^ e)
{
    if (e->Key == VirtualKey::Control) isCtrlKeyPressed = true;
}


void KeyboardSupport::MainPage::Grid_KeyUp(Platform::Object^ sender, Windows::UI::Xaml::Input::KeyRoutedEventArgs^ e)
{
    if (IsCtrlKeyPressed()) {
        if (e->Key==VirtualKey::P) { DemoMovie->Play(); }
        if (e->Key==VirtualKey::A) { DemoMovie->Pause(); }
        if (e->Key==VirtualKey::S) { DemoMovie->Stop(); }
    }
}
```

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    // Set the input focus to ensure that keyboard events are raised.
    this.Loaded += delegate { this.Focus(FocusState.Programmatic); };
}

private void MediaButton_Click(object sender, RoutedEventArgs e)
{
    switch ((sender as Button).Name)
    {
        case "PlayButton": DemoMovie.Play(); break;
        case "PauseButton": DemoMovie.Pause(); break;
        case "StopButton": DemoMovie.Stop(); break;
    }
}

private static bool IsCtrlKeyPressed()
{
    var ctrlState = CoreWindow.GetForCurrentThread().GetKeyState(VirtualKey.Control);
    return (ctrlState & CoreVirtualKeyStates.Down) == CoreVirtualKeyStates.Down;
}

private void Grid_KeyDown(object sender, KeyRoutedEventArgs e)
{
    if (IsCtrlKeyPressed())
    {
        switch (e.Key)
        {
            case VirtualKey.P: DemoMovie.Play(); break;
            case VirtualKey.A: DemoMovie.Pause(); break;
            case VirtualKey.S: DemoMovie.Stop(); break;
        }
    }
}
```

```VisualBasic
Private isCtrlKeyPressed As Boolean
Protected Overrides Sub OnNavigatedTo(e As Navigation.NavigationEventArgs)

End Sub

Private Function IsCtrlKeyPressed As Boolean
    Dim ctrlState As CoreVirtualKeyStates = CoreWindow.GetForCurrentThread().GetKeyState(VirtualKey.Control);
    Return (ctrlState & CoreVirtualKeyStates.Down) == CoreVirtualKeyStates.Down;
End Function

Private Sub Grid_KeyDown(sender As Object, e As KeyRoutedEventArgs)
    If IsCtrlKeyPressed() Then
        Select Case e.Key
            Case Windows.System.VirtualKey.P
                DemoMovie.Play()
            Case Windows.System.VirtualKey.A
                DemoMovie.Pause()
            Case Windows.System.VirtualKey.S
                DemoMovie.Stop()
        End Select
    End If
End Sub

Private Sub MediaButton_Click(sender As Object, e As RoutedEventArgs)
    Dim fe As FrameworkElement = CType(sender, FrameworkElement)
    Select Case fe.Name
        Case "PlayButton"
            DemoMovie.Play()
        Case "PauseButton"
            DemoMovie.Pause()
        Case "StopButton"
            DemoMovie.Stop()
    End Select
End Sub
```

> [!NOTE]
> Параметр [**AutomationProperties.AcceleratorKey**](https://msdn.microsoft.com/library/windows/apps/hh759762) или [**AutomationProperties.AccessKey**](https://msdn.microsoft.com/library/windows/apps/hh759763) в XAML предоставляет строковые сведения, которые регистрируют сочетание клавиш для вызова определенного действия. Эти сведения записываются клиентами автоматизации пользовательского интерфейса Майкрософт, например экранным диктором, и, как правило, предоставляются пользователю напрямую.
>
> Параметр **AutomationProperties.AcceleratorKey** или **AutomationProperties.AccessKey** не имеет собственных действий. Но вам все равно нужно подключить обработчики для событий [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) или [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942), чтобы фактически реализовать применение сочетания клавиш в вашем приложении. Подчеркивание клавиши доступа не предоставляется автоматически. Вы должны явным образом подчеркнуть текст, относящийся к определенной назначенной клавише в качестве встроенного форматирования [**Underline**](https://msdn.microsoft.com/library/windows/apps/br209982), если вы хотите, чтобы подчеркнутый текст отображался в пользовательском интерфейсе.

 

## <a name="keyboard-routed-events"></a>Перенаправленные события клавиатуры


Некоторые события являются перенаправленными, включая [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) и [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942). Перенаправленные события используют стратегию восходящей маршрутизации. Стратегия восходящего перенаправления означает, что событие происходит из дочернего объекта, а затем направляется по последующим родительским объектам в дереве объектов. Это предоставляет еще одну возможность обработки того же события и взаимодействия с его данными.

Рассмотрим следующий пример кода XAML, в котором обрабатываются события [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) для объекта [**Canvas**](https://msdn.microsoft.com/library/windows/apps/br209267) и двух объектов [**Button**](https://msdn.microsoft.com/library/windows/apps/br209265). В этом случае, если отпустить клавишу во время удержания фокуса любым из объектов **Button**, будет вызвано событие **KeyUp**. Затем событие передается родительскому объекту **Canvas**.

```xaml
<StackPanel KeyUp="StackPanel_KeyUp">
  <Button Name="ButtonA" Content="Button A"/>
  <Button Name="ButtonB" Content="Button B"/>
  <TextBlock Name="statusTextBlock"/>
</StackPanel>
```

В следующем примере показана реализация обработчика событий [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) для соответствующего содержимого XAML из предыдущего примера.

```csharp
void StackPanel_KeyUp(object sender, KeyRoutedEventArgs e)
{
    statusTextBlock.Text = String.Format(
        "The key {0} was pressed while focus was on {1}",
        e.Key.ToString(), (e.OriginalSource as FrameworkElement).Name);
}
```

Обратите внимание на использование свойства [**OriginalSource**](https://msdn.microsoft.com/library/windows/apps/br208810) в предыдущем обработчике. Здесь **OriginalSource** сообщает об объекте, вызвавшем событие. Этим объектом не может быть [**StackPanel**](https://msdn.microsoft.com/library/windows/apps/br209635), поскольку **StackPanel** не является элементом управления и не может содержать фокус. Событие могло быть вызвано только одной из двух кнопок внутри **StackPanel**, но какой именно? Определить исходный объект текущего события, если оно обрабатывается на уровне родительского объекта, позволяет свойство **OriginalSource**.

### <a name="the-handled-property-in-event-data"></a>Свойство Handled в данных события

В зависимости от выбранной стратегии обработки событий для реакции на восходящее событие можно использовать только один обработчик событий. Например, если имеется особый обработчик [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942), прикрепленный к одному из элементов управления [**Button**](https://msdn.microsoft.com/library/windows/apps/br209265), у него в первую очередь будет возможность обработать данное событие. В этом случае не нужно, чтобы событие было также обработано родительской панелью. Для данного сценария можно воспользоваться свойством [**Handled**](https://msdn.microsoft.com/library/windows/apps/hh943073) в данных события.

Цель свойства [**Handled**](https://msdn.microsoft.com/library/windows/apps/hh943073) в классе данных перенаправленного события–сообщить о том, что другой ранее зарегистрированный обработчик на маршруте события уже был задействован. Это влияет на поведение системы перенаправленных событий. При установке для **Handled** значения **true** в обработчике событий перенаправление этого события прекращается и оно не передается последующим родительским элементам.

### <a name="addhandler-and-already-handled-keyboard-events"></a>AddHandler и уже обработанные события клавиатуры

Для присоединения обработчиков, которые могут реагировать на события, уже отмеченные как обработанные, можно использовать специальную методику. При этом для регистрации обработчика используется метод [**AddHandler**](https://msdn.microsoft.com/library/windows/apps/hh702399) вместо атрибутов XAML или зависящего от языка синтаксиса для добавления обработчиков, например "+=" в языке C\#.

Обычным ограничением для этой методики является то, что API **AddHandler** принимает параметр типа [**RoutedEvent**](https://msdn.microsoft.com/library/windows/apps/br208808), который идентифицирует рассматриваемое перенаправленное событие. Идентификатор **RoutedEvent** предоставляется не всеми перенаправленными событиями, и это соображение влияет на то, какие перенаправленные события все же могут быть обработаны в случае [**Handled**](https://msdn.microsoft.com/library/windows/apps/hh943073). События [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) и [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) имеют идентификаторы перенаправленных событий ([**KeyDownEvent**](https://msdn.microsoft.com/library/windows/apps/hh702416) и [**KeyUpEvent**](https://msdn.microsoft.com/library/windows/apps/hh702418)) в [**UIElement**](https://msdn.microsoft.com/library/windows/apps/br208911). Однако другие события, например [**TextBox.TextChanged**](https://msdn.microsoft.com/library/windows/apps/br209706), не имеют идентификаторов перенаправленных событий, и поэтому для них нельзя использовать методику **AddHandler**.

### <a name="overriding-keyboard-events-and-behavior"></a>Переопределение событий и поведения клавиатуры

Вы можете переопределить основные события для определенных элементов управления (таких как [**GridView**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.GridView)) для обеспечения согласованного перемещения фокуса для различных устройств ввода, в том числе клавиатуры и игрового контроллера.

В следующем примере мы создаем подкласс элемента управления и переопределяем поведение KeyDown, чтобы переместить фокус на GridView содержимого при нажатии любой клавиши со стрелками.

```csharp
public class CustomGridView : GridView
  {
    protected override void OnKeyDown(KeyRoutedEventArgs e)
    {
      // Override arrow key behaviors.
      if (e.Key != Windows.System.VirtualKey.Left && e.Key !=
        Windows.System.VirtualKey.Right && e.Key !=
          Windows.System.VirtualKey.Down && e.Key !=
            Windows.System.VirtualKey.Up)
              base.OnKeyDown(e);
      else
        FocusManager.TryMoveFocus(FocusNavigationDirection.Down);
    }
  }
```

> [!NOTE]
> Если GridView используется только для макета, рассмотрите возможность использования других элементов управления, таких как [**ItemsControl**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ItemsControl) с [**ItemsWrapGrid**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ItemsWrapGrid).

## <a name="commanding"></a>Командные элементы

Небольшое количество элементов пользовательского интерфейса предоставляет встроенную поддержку команд. В командах используются связанные с вводом перенаправленные события в базовой реализации. Это позволяет обрабатывать соответствующий ввод в пользовательском интерфейсе, например определенное действие указателя или нажатие определенного сочетания клавиш, путем вызова единого обработчика команды.

Если для элемента пользовательского интерфейса доступен командный интерфейс, подумайте об использовании соответствующих API командного интерфейса вместо любых дискретных событий ввода. Подробнее: [**ButtonBase.Command**](https://msdn.microsoft.com/library/windows/apps/br227740).

Также можно реализовать [**ICommand**](https://msdn.microsoft.com/library/windows/apps/br227885) для инкапсуляции функциональных возможностей команд, вызываемых из обычных обработчиков событий. Это позволяет использовать команды даже при отсутствии свойства **Command**.

## <a name="text-input-and-controls"></a>Ввод текста и элементы управления

Некоторые элементы управления реагируют на события клавиатуры собственной обработкой. Например, [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683)–элемент управления, предназначенный для захвата и дальнейшего визуального представления текста, введенного с помощью клавиатуры. В нем используются [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) и [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) с собственной логикой перехвата нажатий клавиш, а затем инициируется собственное событие [**TextChanged**](https://msdn.microsoft.com/library/windows/apps/br209706), если текст был изменен.

Вы по-прежнему можете добавлять обработчики для [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) и [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) в [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683) или любой связанный элемент управления, предназначенный для обработки текстового ввода. Однако элемент управления может быть разработан так, что будет отвечать не на все значения клавиш, направляемые ему через события клавиш. Для каждого элемента управления задается своя реакция.

Например, [**ButtonBase**](https://msdn.microsoft.com/library/windows/apps/br227736) (базовый класс для [**Button**](https://msdn.microsoft.com/library/windows/apps/br209265)) обрабатывает [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) таким образом, что он может проверять клавиши ПРОБЕЛ или ВВОД. **ButtonBase** считает **KeyUp** эквивалентом нажатия левой кнопки мыши в целях инициации события [**Click**](https://msdn.microsoft.com/library/windows/apps/br227737). Эта обработка события выполняется, когда **ButtonBase** переопределяет виртуальный метод [**OnKeyUp**](https://msdn.microsoft.com/library/windows/apps/hh967983). При реализации он устанавливает [**Handled**](https://msdn.microsoft.com/library/windows/apps/hh943073) на **true**. В результате получается, что любой родительский элемент кнопки, прослушивающий событие клавиши, при нажатии клавиши ПРОБЕЛ не получит уже обработанное событие для своих обработчиков.

Еще один пример–[**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683). Некоторые клавиши, такие как клавиши со стрелками, не рассматриваются объектом **TextBox** как текстовые, а относятся к специальным реакциям пользовательского интерфейса элементов управления. **TextBox** отмечает эти случаи событий как обработанные.

Пользовательские элементы управления могут реализовывать собственные аналогичные реакции для событий клавиш путем переопределения [**OnKeyDown**](https://msdn.microsoft.com/library/windows/apps/hh967982) / [**OnKeyUp**](https://msdn.microsoft.com/library/windows/apps/hh967983). Если пользовательский элемент управления обрабатывает определенные сочетания клавиш либо реагирует как элемент управления или фокус аналогично сценарию, описанному для [**TextBox**](https://msdn.microsoft.com/library/windows/apps/br209683), следует применить эту логику к собственным переопределениям **OnKeyDown** / **OnKeyUp**.

## <a name="the-touch-keyboard"></a>Сенсорная клавиатура

Элементы управления вводом текста обеспечивают автоматическую поддержку сенсорной клавиатуры. Когда пользователь устанавливает фокус ввода на текстовый элемент управления посредством сенсорного ввода, сенсорная клавиатура автоматически активируется. Когда фокус ввода не находится на текстовом элементе управления, сенсорная клавиатура скрыта.

При появлении сенсорной клавиатуры она автоматически изменяет положение пользовательского интерфейса так, чтобы содержащий фокус элемент оставался видимым. Это может вызвать отдаление других важных областей пользовательского интерфейса за пределы экрана. Однако можно отключить поведение по умолчанию и вносить собственные изменения в пользовательский интерфейс при появлении сенсорной клавиатуры. Подробнее см. в разделе [Пример сенсорной клавиатуры](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/TouchKeyboard).

При создании пользовательского элемента управления текстовым вводом, не происходящего от стандартного элемента управления текстовым вводом, можно добавить поддержку сенсорной клавиатуры, реализовав нужные шаблоны элементов управления автоматизацией пользовательского интерфейса. Подробнее см. в разделе [Пример сенсорной клавиатуры](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/TouchKeyboard).

Нажатия клавиш на сенсорной клавиатуре вызывают события [**KeyDown**](https://msdn.microsoft.com/library/windows/apps/br208941) и [**KeyUp**](https://msdn.microsoft.com/library/windows/apps/br208942) так же, как и нажатия клавиш на аппаратной клавиатуре. Однако сенсорная клавиатура не вызывает событий ввода для сочетаний клавиш Ctrl+A, Ctrl+Z, Ctrl+X, Ctrl+C и Ctrl+V, которые зарезервированы для обработки текста в элементе управления вводом.

Пользователи могут вводить данные в вашем приложении гораздо быстрее и проще, если настроить тип вводимых данных элемента управления текстом на соответствие типу данных, которые пользователь должен вводить. Тип вводимых данных предоставляет подсказку о типе ожидаемого текстового ввода с помощью элемента управления, таким образом система может предоставить специальную раскладку сенсорной клавиатуры в соответствии с типом ввода. Например, если текстовое поле используется только для ввода 4-значного ПИН-кода, установите для свойства [**InputScope**](https://msdn.microsoft.com/library/windows/apps/hh702632) значение [**Number**](https://msdn.microsoft.com/library/windows/apps/hh702028). Это сообщает системе, что нужно отобразить раскладку цифровой клавиатуры, благодаря чему пользователю будет проще вводить ПИН-код. Дополнительную информацию можно найти в статье [Использование типа вводимых данных для изменения сенсорной клавиатуры](https://msdn.microsoft.com/library/windows/apps/mt280229).

## <a name="related-articles"></a>Статьи по теме

**Разработчикам**
* [Взаимодействие с помощью клавиатуры](keyboard-interactions.md)
* [Определение устройств ввода](identify-input-devices.md)
* [Реакция на наличие сенсорной клавиатуры](respond-to-the-presence-of-the-touch-keyboard.md)

**Проектировщикам**
* [Руководство по сенсорной клавиатуре](https://msdn.microsoft.com/library/windows/apps/hh972345)

**Примеры**
* [Пример сенсорной клавиатуры](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/TouchKeyboard)
* [Пример базового ввода](http://go.microsoft.com/fwlink/p/?LinkID=620302)
* [Пример ввода с малой задержкой](http://go.microsoft.com/fwlink/p/?LinkID=620304)
* [Пример визуальных элементов фокуса](http://go.microsoft.com/fwlink/p/?LinkID=619895)

**Примеры архивов**
* [Пример ввода](http://go.microsoft.com/fwlink/p/?linkid=226855)
* [Ввод: пример возможностей устройства](http://go.microsoft.com/fwlink/p/?linkid=231530)
* [Ввод: пример сенсорной клавиатуры](http://go.microsoft.com/fwlink/p/?linkid=246019)
* [Пример реакции на появление экранной клавиатуры](http://go.microsoft.com/fwlink/p/?linkid=231633)
* [Пример редактирования текста XAML](http://go.microsoft.com/fwlink/p/?LinkID=251417)
 

 
