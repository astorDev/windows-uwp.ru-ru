---
author: Karl-Bridge-Microsoft
Description: Receive, process, and manage input data from pointing devices such as touch, mouse, pen/stylus, and touchpad, in your Universal Windows Platform (UWP) applications.
title: Работа с входными данными указателя
ms.assetid: BDBC9E33-4037-4671-9596-471DCF855C82
label: Handle pointer input
template: detail.hbs
keywords: перо, мышь, сенсорная панель, сенсорный ввод, указатель, ввод, взаимодействие с пользователем
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: ba685f30eb0cf94314996587073a82440cf6c951
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "7159801"
---
# <a name="handle-pointer-input"></a>Работа с входными данными указателя

Вы можете получать, обрабатывать и контролировать входные данные от указывающих устройств (таких как устройство сенсорного ввода, мышь, перо/стилус и сенсорная панель) в приложениях универсальной платформы Windows (UWP).

> [!Important]
> Создавайте пользовательские взаимодействия, только если существует явное, четко определенное требование и если взаимодействия, поддерживаемые элементами управления платформы, не поддерживают ваш сценарий.  
> При настройке взаимодействий в Windows-приложении следует учитывать, что пользователи хотят, чтобы они были согласованными, интуитивно понятными и легко обнаруживаемыми. По этой причине рекомендуется моделировать пользовательские взаимодействия в сценариях, поддерживаемых [элементами управления платформы](../controls-and-patterns/controls-by-function.md). Элементы управления платформы обеспечивают все механизмы взаимодействия с пользователем, используемые в приложениях универсальной платформы Windows (UWP), в том числе стандартные взаимодействия, анимированные физические эффекты, визуальную обратную связь и специальные возможности. 

## <a name="important-apis"></a>Важные API
- [Windows.Devices.Input](https://msdn.microsoft.com/library/windows/apps/br225648)
- [Windows.UI.Input](https://msdn.microsoft.com/library/windows/apps/br208383)
- [Windows.UI.Xaml.Input](https://msdn.microsoft.com/library/windows/apps/br242084)

## <a name="pointers"></a>Указатели
Зачастую взаимодействие предполагает определение пользователем объекта, с которым он собирается взаимодействовать, путем указания на него с помощью сенсорного ввода, мыши, пера/стилуса и сенсорной панели. Поскольку необработанные данные устройства HID, предоставляемые такими устройствами ввода, содержат много общих свойств, эти данные преобразовываются в единый входной стек и представляются в виде консолидированных, не зависящих от устройства данных указателя. Ваши приложения UWP смогут использовать эти данные независимо от того, какое устройство ввода используется.

> [!NOTE]
> Сведения, касающиеся устройства, также извлекаются из необработанных данных HID, если это необходимо для вашего приложения.
 

Каждая точка ввода (или контакта) на входном стеке представляется объектом [**Pointer**](https://msdn.microsoft.com/library/windows/apps/br227968), доступ к которому предоставляется с помощью параметра [**PointerRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943076) в различных обработчиках событий указателя. В случае ввода несколькими перьями или мультисенсорного ввода каждый контакт считается уникальным указателем ввода.

## <a name="pointer-events"></a>События указателя


События указателя предоставляют основную информацию, например, о состоянии обнаружения (на расстоянии или в контакте) и типе устройства, а также дополнительную информацию, например, о давлении и геометрии контакта. Кроме того, предоставляются сведения о свойствах конкретных устройств, например, о том, какую кнопку мыши нажал пользователь и не использует ли он стирающий конец пера. Если ваше приложение должно различать устройства ввода и их возможности, см. раздел [Определение типов устройств ввода](identify-input-devices.md).

Приложения UWP могут воспринимать приведенные ниже события указателя.

> [!NOTE]
> Ограничьте ввода указателя определенным элементом пользовательского интерфейса, вызвав метод [**CapturePointer**](https://msdn.microsoft.com/library/windows/apps/br208918) для этого элемента в обработчике событий указателя. Когда указатель захвачен элементом, только этот объект получает сведения о событиях ввода указателя, даже если указатель перемещается за пределы выбранной области. Свойство [**IsInContact**](https://msdn.microsoft.com/library/windows/apps/br227976) (нажатие кнопки мыши, сенсорный ввод или контакт с пером) должно быть действительным для успешного выполнения команды захвата указателя **CapturePointer**.
 

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Событие</th>
<th align="left">Описание</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br208964"><strong>PointerCanceled</strong></a></p></td>
<td align="left"><p>Происходит, когда указатель отменяется платформой. Может возникать при следующих условиях.</p>
<ul>
<li>Указатели касания отменяются в том случае, если перо обнаруживается в пределах поверхности ввода.</li>
<li>Активный контакт не обнаруживается дольше 100мс.</li>
<li>Изменены настройки монитора/дисплея (разрешение, параметры, конфигурация с несколькими мониторами).</li>
<li>Рабочий стол заблокирован или пользователь вышел из системы.</li>
<li>Количество одновременных контактов превысило значение, поддерживаемое устройством.</li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br208965"><strong>PointerCaptureLost</strong></a></p></td>
<td align="left"><p>Происходит, если указатель захватывается другим элементом пользовательского интерфейса, если указатель отпущен пользователем или если происходит захват другого указателя программным путем.</p>
<div class="alert">
<strong>Примечание</strong>есть соответствующее событие захвата указателя не происходит.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br208968"><strong>PointerEntered</strong></a></p></td>
<td align="left"><p>Происходит, когда указатель входит в контактный участок элемента. Это может происходить немного по-разному в случае сенсорного ввода, использования сенсорной панели, мыши и пера.</p>
<ul>
<li>В случае сенсорного ввода для запуска этого события требуется контакт с пальцем либо путем непосредственного прикосновения к элементу, либо путем касания и перемещения пальца в контактный участок элемента.</li>
<li>При использовании мыши и сенсорной панели на экране присутствует курсор, который постоянно отображается и запускает событие, даже если кнопка мыши или сенсорной панели не нажата.</li>
<li>Как и в случае сенсорного ввода, перо запускает это событие при непосредственном контакте с элементом либо при перемещении в контактный участок элемента. Однако перо также имеет состояние наведения ([IsInRange](https://msdn.microsoft.com/library/windows/apps/br227977)), которое, если активно, запускает это событие.</li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br208969"><strong>PointerExited</strong></a></p></td>
<td align="left"><p>Происходит, когда указатель выходит из контактного участка элемента. Это может происходить немного по-разному в случае сенсорного ввода, использования сенсорной панели, мыши и пера.</p>
<ul>
<li>В случае сенсорного ввода для запуска этого события требуется контакт с пальцем, когда указатель выходит за пределы контактного участка элемента.</li>
<li>При использовании мыши и сенсорной панели на экране присутствует курсор, который постоянно отображается и запускает событие, даже если кнопка мыши или сенсорной панели не нажата.</li>
<li>Как и в случае сенсорного ввода, перо запускает это событие при выходе за пределы контактного участка элемента. Однако перо также имеет состояние наведения ([IsInRange](https://msdn.microsoft.com/library/windows/apps/br227977)), которое запускает это событие при изменении состояния с "true" на "false".</li>
</ul></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br208970"><strong>PointerMoved</strong></a></p></td>
<td align="left"><p>Происходит, когда указатель изменяет координаты, состояние кнопок, давление, наклон или геометрию контакта (например, ширину и высоту) в пределах контактного участка элемента. Это может происходить немного по-разному в случае сенсорного ввода, использования сенсорной панели, мыши и пера.</p>
<ul>
<li>Сенсорный ввод требует контакта с пальцем и запускает это событие только в случае контакта в пределах контактного участка элемента.</li>
<li>При использовании мыши и сенсорной панели на экране присутствует курсор, который постоянно отображается и запускает событие, даже если кнопка мыши или сенсорной панели не нажата.</li>
<li>Как и в случае сенсорного ввода, перо запускает это событие при контакте в пределах контактного участка элемента. Однако перо также имеет состояние наведения ([IsInRange](https://msdn.microsoft.com/library/windows/apps/br227977)), которое запускает это событие, если имеет значение "true" и происходит в пределах контактного участка.</li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br208971"><strong>PointerPressed</strong></a></p></td>
<td align="left"><p>Происходит, когда указатель указывает действие нажатия (например, касание, нажатие кнопки мыши, касание пера или нажатие кнопки сенсорной панели) в пределах контактного участка элемента.</p>
<p>Для этого события необходимо вызывать [CapturePointer](https://msdn.microsoft.com/library/windows/apps/br208918) из обработчика.</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br208972"><strong>PointerReleased</strong></a></p></td>
<td align="left"><p>Происходит, когда указатель указывает действие отпускания (например, разрыв контакта с пальцем или пером, поднятие кнопки мыши или кнопки сенсорной панели) на контактном участке элемента или, в случае захвата указателя, за пределами контактного участка.</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="https://msdn.microsoft.com/library/windows/apps/br208973"><strong>PointerWheelChanged</strong></a></p></td>
<td align="left"><p>Происходит при вращении колесика мыши.</p>
<p>Ввод мыши связывается с отдельным указателем, назначаемым при первом обнаружении ввода мыши. При щелчке кнопкой мыши (левой, правой или колеса) создается вспомогательная связь между указателем и этой кнопкой с помощью события [PointerMoved](https://msdn.microsoft.com/library/windows/apps/br208970).</p></td>
</tr>
</tbody>
</table> 

## <a name="pointer-event-example"></a>Пример события указателя

Ниже представлены фрагменты кода из основного приложения отслеживания указателя, которые показывают, как просматривать и обрабатывать события нескольких указателей и получать сведения о различных свойствах для связанных указателей.

![Пользовательский интерфейс приложения указателя](images/pointers/pointers1.gif)

**Скачайте этот пример в разделе [Пример ввода указателя (базовый)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-pointers.zip)**

### <a name="create-the-ui"></a>Создание пользовательского интерфейса

В этом примере мы используем в качестве целевого объекта для ввода от указателя [Rectangle](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.shapes.rectangle) (`Target`). Цвет целевого объекта изменяется при изменении состояния указателя.

Сведения для каждого указателя отображаются в перемещаемом элементе [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock), который движется вместе с указателем. События указателя отображаются в элементе [RichTextBlock](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock) справа прямоугольника.

Вот XAML-код для пользовательского интерфейса в этом примере. 

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.ColumnDefinitions>
        <ColumnDefinition Width="*" />
        <ColumnDefinition Width="250"/>
    </Grid.ColumnDefinitions>
    <Grid.RowDefinitions>
        <RowDefinition Height="*" />
        <RowDefinition Height="320" />
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <Canvas Name="Container" 
            Grid.Column="0"
            Grid.Row="1"
            HorizontalAlignment="Center" 
            VerticalAlignment="Center" 
            Margin="245,0" 
            Height="320"  Width="640">
        <Rectangle Name="Target" 
                    Fill="#FF0000" 
                    Stroke="Black" 
                    StrokeThickness="0"
                    Height="320" Width="640" />
    </Canvas>
    <Grid Grid.Column="1" Grid.Row="0" Grid.RowSpan="3">
        <Grid.RowDefinitions>
            <RowDefinition Height="50" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>
        <Button Name="buttonClear" 
                Grid.Row="0"
                Content="Clear"
                Foreground="White"
                HorizontalAlignment="Stretch" 
                VerticalAlignment="Stretch">
        </Button>
        <ScrollViewer Name="eventLogScrollViewer" Grid.Row="1" 
                        VerticalScrollMode="Auto" 
                        Background="Black">                
            <RichTextBlock Name="eventLog"  
                        TextWrapping="Wrap" 
                        Foreground="#FFFFFF" 
                        ScrollViewer.VerticalScrollBarVisibility="Visible" 
                        ScrollViewer.HorizontalScrollBarVisibility="Disabled"
                        Grid.ColumnSpan="2">
            </RichTextBlock>
        </ScrollViewer>
    </Grid>
</Grid>
```

### <a name="listen-for-pointer-events"></a>Ожидание событий указателя

В большинстве случаев мы рекомендуем получать сведения от указателя посредством [**PointerRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943076) обработчика событий.

Если аргумент события не предоставляет необходимых данных указателя, можно получить доступ к расширенным сведениям [**PointerPoint**](https://msdn.microsoft.com/library/windows/apps/br242038), выраженным через методы [**GetCurrentPoint**](https://msdn.microsoft.com/library/windows/apps/hh943077) и [**GetIntermediatePoints**](https://msdn.microsoft.com/library/windows/apps/hh943078) для [**PointerRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943076).

Следующий код задает глобальный объект словаря для отслеживания каждого активного указателя и определяет прослушиватели для различных событий указателя для целевого объекта.

```CSharp
// Dictionary to maintain information about each active pointer. 
// An entry is added during PointerPressed/PointerEntered events and removed 
// during PointerReleased/PointerCaptureLost/PointerCanceled/PointerExited events.
Dictionary<uint, Windows.UI.Xaml.Input.Pointer> pointers;

public MainPage()
{
    this.InitializeComponent();

    // Initialize the dictionary.
    pointers = new Dictionary<uint, Windows.UI.Xaml.Input.Pointer>();

    // Declare the pointer event handlers.
    Target.PointerPressed += 
        new PointerEventHandler(Target_PointerPressed);
    Target.PointerEntered += 
        new PointerEventHandler(Target_PointerEntered);
    Target.PointerReleased += 
        new PointerEventHandler(Target_PointerReleased);
    Target.PointerExited += 
        new PointerEventHandler(Target_PointerExited);
    Target.PointerCanceled += 
        new PointerEventHandler(Target_PointerCanceled);
    Target.PointerCaptureLost += 
        new PointerEventHandler(Target_PointerCaptureLost);
    Target.PointerMoved += 
        new PointerEventHandler(Target_PointerMoved);
    Target.PointerWheelChanged += 
        new PointerEventHandler(Target_PointerWheelChanged);

    buttonClear.Click += 
        new RoutedEventHandler(ButtonClear_Click);
}
```

### <a name="handle-pointer-events"></a>Обработка событий указателя

Далее мы воспользуемся обратной связью пользовательского интерфейса для демонстрации обработчиков основных событий указателя.

-   Этот обработчик управляет событием [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208971). Мы добавляем это событие в журнал событий, добавляем указатель в активный словарь указателя и отображаем данные указателя.

    > [!NOTE]
    > События [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208971) и [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972) не всегда возникают парами. Приложение должно ожидать и обрабатывать любые события, которые могут содержать действие указателя "вниз" (например, [**PointerExited**](https://msdn.microsoft.com/library/windows/apps/br208969), [**PointerCanceled**](https://msdn.microsoft.com/library/windows/apps/br208964) и [**PointerCaptureLost**](https://msdn.microsoft.com/library/windows/apps/br208965)).      

```csharp
/// <summary>
/// The pointer pressed event handler.
/// PointerPressed and PointerReleased don't always occur in pairs. 
/// Your app should listen for and handle any event that can conclude 
/// a pointer down (PointerExited, PointerCanceled, PointerCaptureLost).
/// </summary>
/// <param name="sender">Source of the pointer event.</param>
/// <param name="e">Event args for the pointer routed event.</param>
void Target_PointerPressed(object sender, PointerRoutedEventArgs e)
{
    // Prevent most handlers along the event route from handling the same event again.
    e.Handled = true;

    PointerPoint ptrPt = e.GetCurrentPoint(Target);

    // Update event log.
    UpdateEventLog("Down: " + ptrPt.PointerId);

    // Lock the pointer to the target.
    Target.CapturePointer(e.Pointer);

    // Update event log.
    UpdateEventLog("Pointer captured: " + ptrPt.PointerId);

    // Check if pointer exists in dictionary (ie, enter occurred prior to press).
    if (!pointers.ContainsKey(ptrPt.PointerId))
    {
        // Add contact to dictionary.
        pointers[ptrPt.PointerId] = e.Pointer;
    }

    // Change background color of target when pointer contact detected.
    Target.Fill = new SolidColorBrush(Windows.UI.Colors.Green);

    // Display pointer details.
    CreateInfoPop(ptrPt);
}
```

-   Этот обработчик управляет событием [**PointerEntered**](https://msdn.microsoft.com/library/windows/apps/br208968). Мы добавляем это событие в журнал событий, добавляем указатель в коллекцию указателей и отображаем данные указателя.

```csharp
/// <summary>
/// The pointer entered event handler.
/// We do not capture the pointer on this event.
/// </summary>
/// <param name="sender">Source of the pointer event.</param>
/// <param name="e">Event args for the pointer routed event.</param>
private void Target_PointerEntered(object sender, PointerRoutedEventArgs e)
{
    // Prevent most handlers along the event route from handling the same event again.
    e.Handled = true;

    PointerPoint ptrPt = e.GetCurrentPoint(Target);

    // Update event log.
    UpdateEventLog("Entered: " + ptrPt.PointerId);

    // Check if pointer already exists (if enter occurred prior to down).
    if (!pointers.ContainsKey(ptrPt.PointerId))
    {
        // Add contact to dictionary.
        pointers[ptrPt.PointerId] = e.Pointer;
    }

    if (pointers.Count == 0)
    {
        // Change background color of target when pointer contact detected.
        Target.Fill = new SolidColorBrush(Windows.UI.Colors.Blue);
    }

    // Display pointer details.
    CreateInfoPop(ptrPt);
}
```

-   Этот обработчик управляет событием [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208970). Мы добавляем событие в журнал событий и обновляем данные указателя.

    > [!Important]
    > Ввод мыши связывается с отдельным указателем, назначаемым при первом обнаружении ввода мыши. При щелчке кнопки мыши (левой, правой или колеса) создается вспомогательная связь между указателем и этой кнопкой с помощью события [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208971). Событие [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972) инициируется только тогда, когда отпускается та же кнопка мыши (никакая другая кнопка не может быть связана с указателем до завершения события). Из-за этой исключительной связи другие щелчки кнопок мыши обрабатываются с помощью события [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208970).     

```csharp
/// <summary>
/// The pointer moved event handler.
/// </summary>
/// <param name="sender">Source of the pointer event.</param>
/// <param name="e">Event args for the pointer routed event.</param>
private void Target_PointerMoved(object sender, PointerRoutedEventArgs e)
{
    // Prevent most handlers along the event route from handling the same event again.
    e.Handled = true;

    PointerPoint ptrPt = e.GetCurrentPoint(Target);

    // Multiple, simultaneous mouse button inputs are processed here.
    // Mouse input is associated with a single pointer assigned when 
    // mouse input is first detected. 
    // Clicking additional mouse buttons (left, wheel, or right) during 
    // the interaction creates secondary associations between those buttons 
    // and the pointer through the pointer pressed event. 
    // The pointer released event is fired only when the last mouse button 
    // associated with the interaction (not necessarily the initial button) 
    // is released. 
    // Because of this exclusive association, other mouse button clicks are 
    // routed through the pointer move event.          
    if (ptrPt.PointerDevice.PointerDeviceType == Windows.Devices.Input.PointerDeviceType.Mouse)
    {
        if (ptrPt.Properties.IsLeftButtonPressed)
        {
            UpdateEventLog("Left button: " + ptrPt.PointerId);
        }
        if (ptrPt.Properties.IsMiddleButtonPressed)
        {
            UpdateEventLog("Wheel button: " + ptrPt.PointerId);
        }
        if (ptrPt.Properties.IsRightButtonPressed)
        {
            UpdateEventLog("Right button: " + ptrPt.PointerId);
        }
    }

    // Display pointer details.
    UpdateInfoPop(ptrPt);
}
```

-   Этот обработчик управляет событием [**PointerWheelChanged**](https://msdn.microsoft.com/library/windows/apps/br208973). Мы добавляем это событие в журнал событий, при необходимости добавляем указатель в массив указателей и отображаем данные указателя.

```csharp
/// <summary>
/// The pointer wheel event handler.
/// </summary>
/// <param name="sender">Source of the pointer event.</param>
/// <param name="e">Event args for the pointer routed event.</param>
private void Target_PointerWheelChanged(object sender, PointerRoutedEventArgs e)
{
    // Prevent most handlers along the event route from handling the same event again.
    e.Handled = true;

    PointerPoint ptrPt = e.GetCurrentPoint(Target);

    // Update event log.
    UpdateEventLog("Mouse wheel: " + ptrPt.PointerId);

    // Check if pointer already exists (for example, enter occurred prior to wheel).
    if (!pointers.ContainsKey(ptrPt.PointerId))
    {
        // Add contact to dictionary.
        pointers[ptrPt.PointerId] = e.Pointer;
    }

    // Display pointer details.
    CreateInfoPop(ptrPt);
}
```

-   Этот обработчик управляет событием [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972), когда контакт с дигитайзером прекращен. Мы добавляем это событие в журнал событий, удаляем указатель из коллекции указателей и обновляем данные указателя.

```csharp
/// <summary>
/// The pointer released event handler.
/// PointerPressed and PointerReleased don't always occur in pairs. 
/// Your app should listen for and handle any event that can conclude 
/// a pointer down (PointerExited, PointerCanceled, PointerCaptureLost).
/// </summary>
/// <param name="sender">Source of the pointer event.</param>
/// <param name="e">Event args for the pointer routed event.</param>
void Target_PointerReleased(object sender, PointerRoutedEventArgs e)
{
    // Prevent most handlers along the event route from handling the same event again.
    e.Handled = true;

    PointerPoint ptrPt = e.GetCurrentPoint(Target);

    // Update event log.
    UpdateEventLog("Up: " + ptrPt.PointerId);

    // If event source is mouse or touchpad and the pointer is still 
    // over the target, retain pointer and pointer details.
    // Return without removing pointer from pointers dictionary.
    // For this example, we assume a maximum of one mouse pointer.
    if (ptrPt.PointerDevice.PointerDeviceType != Windows.Devices.Input.PointerDeviceType.Mouse)
    {
        // Update target UI.
        Target.Fill = new SolidColorBrush(Windows.UI.Colors.Red);

        DestroyInfoPop(ptrPt);

        // Remove contact from dictionary.
        if (pointers.ContainsKey(ptrPt.PointerId))
        {
            pointers[ptrPt.PointerId] = null;
            pointers.Remove(ptrPt.PointerId);
        }

        // Release the pointer from the target.
        Target.ReleasePointerCapture(e.Pointer);

        // Update event log.
        UpdateEventLog("Pointer released: " + ptrPt.PointerId);
    }
    else
    {
        Target.Fill = new SolidColorBrush(Windows.UI.Colors.Blue);
    }
}
```

-   Этот обработчик управляет событием [**PointerExited**](https://msdn.microsoft.com/library/windows/apps/br208969) (когда контакт с дигитайзером поддерживается). Мы добавляем это событие в журнал событий, удаляем указатель из массива указателей и обновляем данные указателя.

```csharp
/// <summary>
/// The pointer exited event handler.
/// </summary>
/// <param name="sender">Source of the pointer event.</param>
/// <param name="e">Event args for the pointer routed event.</param>
private void Target_PointerExited(object sender, PointerRoutedEventArgs e)
{
    // Prevent most handlers along the event route from handling the same event again.
    e.Handled = true;

    PointerPoint ptrPt = e.GetCurrentPoint(Target);

    // Update event log.
    UpdateEventLog("Pointer exited: " + ptrPt.PointerId);

    // Remove contact from dictionary.
    if (pointers.ContainsKey(ptrPt.PointerId))
    {
        pointers[ptrPt.PointerId] = null;
        pointers.Remove(ptrPt.PointerId);
    }

    if (pointers.Count == 0)
    {
        Target.Fill = new SolidColorBrush(Windows.UI.Colors.Red);
    }

    // Update the UI and pointer details.
    DestroyInfoPop(ptrPt);
}
```

-   Этот обработчик управляет событием [**PointerCanceled**](https://msdn.microsoft.com/library/windows/apps/br208964). Мы добавляем это событие в журнал событий, удаляем указатель из массива указателей и обновляем данные указателя.

```csharp
/// <summary>
/// The pointer canceled event handler.
/// Fires for for various reasons, including: 
///    - Touch contact canceled by pen coming into range of the surface.
///    - The device doesn't report an active contact for more than 100ms.
///    - The desktop is locked or the user logged off. 
///    - The number of simultaneous contacts exceeded the number supported by the device.
/// </summary>
/// <param name="sender">Source of the pointer event.</param>
/// <param name="e">Event args for the pointer routed event.</param>
private void Target_PointerCanceled(object sender, PointerRoutedEventArgs e)
{
    // Prevent most handlers along the event route from handling the same event again.
    e.Handled = true;

    PointerPoint ptrPt = e.GetCurrentPoint(Target);

    // Update event log.
    UpdateEventLog("Pointer canceled: " + ptrPt.PointerId);

    // Remove contact from dictionary.
    if (pointers.ContainsKey(ptrPt.PointerId))
    {
        pointers[ptrPt.PointerId] = null;
        pointers.Remove(ptrPt.PointerId);
    }

    if (pointers.Count == 0)
    {
        Target.Fill = new SolidColorBrush(Windows.UI.Colors.Black);
    }

    DestroyInfoPop(ptrPt);
}
```

-   Этот обработчик управляет событием [**PointerCaptureLost**](https://msdn.microsoft.com/library/windows/apps/br208965). Мы добавляем это событие в журнал событий, удаляем указатель из массива указателей и обновляем данные указателя.

    > [!NOTE]
    > Событие [**PointerCaptureLost**](https://msdn.microsoft.com/library/windows/apps/br208965) может произойти вместо события [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972). Захват указателя может быть утерян по разным причинам, включая взаимодействие с пользователем, программный захват другого указателя, вызов метода [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972).     

```csharp
/// <summary>
/// The pointer capture lost event handler.
/// Fires for various reasons, including: 
///    - User interactions
///    - Programmatic capture of another pointer
///    - Captured pointer was deliberately released
// PointerCaptureLost can fire instead of PointerReleased. 
/// </summary>
/// <param name="sender">Source of the pointer event.</param>
/// <param name="e">Event args for the pointer routed event.</param>
private void Target_PointerCaptureLost(object sender, PointerRoutedEventArgs e)
{
    // Prevent most handlers along the event route from handling the same event again.
    e.Handled = true;

    PointerPoint ptrPt = e.GetCurrentPoint(Target);

    // Update event log.
    UpdateEventLog("Pointer capture lost: " + ptrPt.PointerId);

    if (pointers.Count == 0)
    {
        Target.Fill = new SolidColorBrush(Windows.UI.Colors.Black);
    }

    // Remove contact from dictionary.
    if (pointers.ContainsKey(ptrPt.PointerId))
    {
        pointers[ptrPt.PointerId] = null;
        pointers.Remove(ptrPt.PointerId);
    }

    DestroyInfoPop(ptrPt);
}
```

### <a name="get-pointer-properties"></a>Получение свойств указателя

Как упоминалось ранее, вам необходимо получить наиболее полные сведения указателя от объекта [**Windows.UI.Input.PointerPoint**](https://msdn.microsoft.com/library/windows/apps/br242038), получаемые с помощью методов [**GetCurrentPoint**](https://msdn.microsoft.com/library/windows/apps/hh943077) и [**GetIntermediatePoints**](https://msdn.microsoft.com/library/windows/apps/hh943078) класса [**PointerRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943076). Рассмотрим следующие фрагменты кода.

-   Сначала мы создаем новый [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652) для каждого указателя.

```csharp
/// <summary>
/// Create the pointer info popup.
/// </summary>
/// <param name="ptrPt">Reference to the input pointer.</param>
void CreateInfoPop(PointerPoint ptrPt)
{
    TextBlock pointerDetails = new TextBlock();
    pointerDetails.Name = ptrPt.PointerId.ToString();
    pointerDetails.Foreground = new SolidColorBrush(Windows.UI.Colors.White);
    pointerDetails.Text = QueryPointer(ptrPt);

    TranslateTransform x = new TranslateTransform();
    x.X = ptrPt.Position.X + 20;
    x.Y = ptrPt.Position.Y + 20;
    pointerDetails.RenderTransform = x;

    Container.Children.Add(pointerDetails);
}
```

-   Затем мы предоставляем способ обновления сведений об указателе в существующем элементе [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652), связанном с этим указателем.

```csharp
/// <summary>
/// Update the pointer info popup.
/// </summary>
/// <param name="ptrPt">Reference to the input pointer.</param>
void UpdateInfoPop(PointerPoint ptrPt)
{
    foreach (var pointerDetails in Container.Children)
    {
        if (pointerDetails.GetType().ToString() == "Windows.UI.Xaml.Controls.TextBlock")
        {
            TextBlock textBlock = (TextBlock)pointerDetails;
            if (textBlock.Name == ptrPt.PointerId.ToString())
            {
                // To get pointer location details, we need extended pointer info.
                // We get the pointer info through the getCurrentPoint method
                // of the event argument. 
                TranslateTransform x = new TranslateTransform();
                x.X = ptrPt.Position.X + 20;
                x.Y = ptrPt.Position.Y + 20;
                pointerDetails.RenderTransform = x;
                textBlock.Text = QueryPointer(ptrPt);
            }
        }
    }
}
```

-   Наконец, мы запрашиваем различные свойства указателя.

```csharp
/// <summary>
/// Get pointer details.
/// </summary>
/// <param name="ptrPt">Reference to the input pointer.</param>
/// <returns>A string composed of pointer details.</returns>
String QueryPointer(PointerPoint ptrPt)
{
    String details = "";

    switch (ptrPt.PointerDevice.PointerDeviceType)
    {
        case Windows.Devices.Input.PointerDeviceType.Mouse:
            details += "\nPointer type: mouse";
            break;
        case Windows.Devices.Input.PointerDeviceType.Pen:
            details += "\nPointer type: pen";
            if (ptrPt.IsInContact)
            {
                details += "\nPressure: " + ptrPt.Properties.Pressure;
                details += "\nrotation: " + ptrPt.Properties.Orientation;
                details += "\nTilt X: " + ptrPt.Properties.XTilt;
                details += "\nTilt Y: " + ptrPt.Properties.YTilt;
                details += "\nBarrel button pressed: " + ptrPt.Properties.IsBarrelButtonPressed;
            }
            break;
        case Windows.Devices.Input.PointerDeviceType.Touch:
            details += "\nPointer type: touch";
            details += "\nrotation: " + ptrPt.Properties.Orientation;
            details += "\nTilt X: " + ptrPt.Properties.XTilt;
            details += "\nTilt Y: " + ptrPt.Properties.YTilt;
            break;
        default:
            details += "\nPointer type: n/a";
            break;
    }

    GeneralTransform gt = Target.TransformToVisual(this);
    Point screenPoint;

    screenPoint = gt.TransformPoint(new Point(ptrPt.Position.X, ptrPt.Position.Y));
    details += "\nPointer Id: " + ptrPt.PointerId.ToString() +
        "\nPointer location (target): " + Math.Round(ptrPt.Position.X) + ", " + Math.Round(ptrPt.Position.Y) +
        "\nPointer location (container): " + Math.Round(screenPoint.X) + ", " + Math.Round(screenPoint.Y);

    return details;
}
```

## <a name="primary-pointer"></a>Основной указатель
Некоторые устройства ввода, например сенсорный дигитайзер или сенсорная панель, поддерживают дополнительные указатели помимо обычных одиночных указателей мыши или пера (в большинстве случаев Surface Hub поддерживает два способа ввода с помощью пера). 

Используйте нередактируемое свойство **[IsPrimary](https://docs.microsoft.com/uwp/api/windows.ui.input.pointerpointproperties.IsPrimary)** класса **[PointerPointerProperties](https://docs.microsoft.com/uwp/api/windows.ui.input.pointerpointproperties)** для идентификации и различения одиночного основного указателя (основной указатель всегда является первым указателем, обнаруживаемым во время последовательности ввода). 

Определив основной указатель, можно использовать его для эмуляции ввода с помощью мыши или пера, настройки взаимодействий или предоставления некоторых других специальных функций пользовательского интерфейса.

> [!NOTE]
> Если основной указатель был отпущен, отменен или потерян во время последовательности ввода, он не создается до инициирования новой последовательности ввода (последовательность ввода заканчивается, когда все указатели были отпущены, отменены или потеряны).

## <a name="primary-pointer-animation-example"></a>Пример анимации основного указателя

В этих фрагментах кода показано, как можно предоставить специальную визуальную обратную связь, чтобы пользователь мог различать разные входные данные указателя в приложении.

В этом приложении используется цвет и анимация для выделения основного указателя.

![Указатель приложения с анимированной визуальной обратной связью](images/pointers/pointers-usercontrol-animation.gif)

**Скачайте этот пример в разделе [Пример ввода указателя (UserControl с анимацией)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-pointers-animation.zip)**

### <a name="visual-feedback"></a>Визуальная обратная связь

Мы определяем **[UserControl](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.usercontrol)** в зависимости от XAML-объекта **[Ellipse](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.ellipse)**, который выделяет место каждого указателя на полотне и использует **[Storyboard](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.media.animation.storyboard)** для анимации эллипса, который соответствует основному указателю.

**Вот код XAML.**

```xaml
<UserControl
    x:Class="UWP_Pointers.PointerEllipse"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:UWP_Pointers"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    d:DesignHeight="100"
    d:DesignWidth="100">

    <UserControl.Resources>
        <Style x:Key="EllipseStyle" TargetType="Ellipse">
            <Setter Property="Transitions">
                <Setter.Value>
                    <TransitionCollection>
                        <ContentThemeTransition/>
                    </TransitionCollection>
                </Setter.Value>
            </Setter>
        </Style>
        
        <Storyboard x:Name="myStoryboard">
            <!-- Animates the value of a Double property between 
            two target values using linear interpolation over the 
            specified Duration. -->
            <DoubleAnimation
              Storyboard.TargetName="ellipse"
              Storyboard.TargetProperty="(RenderTransform).(ScaleTransform.ScaleY)"  
              Duration="0:0:1" 
              AutoReverse="True" 
              RepeatBehavior="Forever" From="1.0" To="1.4">
            </DoubleAnimation>

            <!-- Animates the value of a Double property between 
            two target values using linear interpolation over the 
            specified Duration. -->
            <DoubleAnimation
              Storyboard.TargetName="ellipse"
              Storyboard.TargetProperty="(RenderTransform).(ScaleTransform.ScaleX)"  
              Duration="0:0:1" 
              AutoReverse="True" 
              RepeatBehavior="Forever" From="1.0" To="1.4">
            </DoubleAnimation>

            <!-- Animates the value of a Color property between 
            two target values using linear interpolation over the 
            specified Duration. -->
            <ColorAnimation 
                Storyboard.TargetName="ellipse" 
                EnableDependentAnimation="True" 
                Storyboard.TargetProperty="(Fill).(SolidColorBrush.Color)" 
                From="White" To="Red"  Duration="0:0:1" 
                AutoReverse="True" RepeatBehavior="Forever"/>
        </Storyboard>
    </UserControl.Resources>

    <Grid x:Name="CompositionContainer">
        <Ellipse Name="ellipse" 
        StrokeThickness="2" 
        Width="{x:Bind Diameter}" 
        Height="{x:Bind Diameter}"  
        Style="{StaticResource EllipseStyle}" />
    </Grid>
</UserControl>
```

А вот код программной части.
```csharp
using Windows.Foundation;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;

// The User Control item template is documented at 
// https://go.microsoft.com/fwlink/?LinkId=234236

namespace UWP_Pointers
{
    /// <summary>
    /// Pointer feedback object.
    /// </summary>
    public sealed partial class PointerEllipse : UserControl
    {
        // Reference to the application canvas.
        Canvas canvas;

        /// <summary>
        /// Ellipse UI for pointer feedback.
        /// </summary>
        /// <param name="c">The drawing canvas.</param>
        public PointerEllipse(Canvas c)
        {
            this.InitializeComponent();
            canvas = c;
        }

        /// <summary>
        /// Gets or sets the pointer Id to associate with the PointerEllipse object.
        /// </summary>
        public uint PointerId
        {
            get { return (uint)GetValue(PointerIdProperty); }
            set { SetValue(PointerIdProperty, value); }
        }
        // Using a DependencyProperty as the backing store for PointerId.  
        // This enables animation, styling, binding, etc...
        public static readonly DependencyProperty PointerIdProperty =
            DependencyProperty.Register("PointerId", typeof(uint), 
                typeof(PointerEllipse), new PropertyMetadata(null));


        /// <summary>
        /// Gets or sets whether the associated pointer is Primary.
        /// </summary>
        public bool PrimaryPointer
        {
            get { return (bool)GetValue(PrimaryPointerProperty); }
            set
            {
                SetValue(PrimaryPointerProperty, value);
            }
        }
        // Using a DependencyProperty as the backing store for PrimaryPointer.  
        // This enables animation, styling, binding, etc...
        public static readonly DependencyProperty PrimaryPointerProperty =
            DependencyProperty.Register("PrimaryPointer", typeof(bool), 
                typeof(PointerEllipse), new PropertyMetadata(false));


        /// <summary>
        /// Gets or sets the ellipse style based on whether the pointer is Primary.
        /// </summary>
        public bool PrimaryEllipse 
        {
            get { return (bool)GetValue(PrimaryEllipseProperty); }
            set
            {
                SetValue(PrimaryEllipseProperty, value);
                if (value)
                {
                    SolidColorBrush fillBrush = 
                        (SolidColorBrush)Application.Current.Resources["PrimaryFillBrush"];
                    SolidColorBrush strokeBrush = 
                        (SolidColorBrush)Application.Current.Resources["PrimaryStrokeBrush"];

                    ellipse.Fill = fillBrush;
                    ellipse.Stroke = strokeBrush;
                    ellipse.RenderTransform = new CompositeTransform();
                    ellipse.RenderTransformOrigin = new Point(.5, .5);
                    myStoryboard.Begin();
                }
                else
                {
                    SolidColorBrush fillBrush = 
                        (SolidColorBrush)Application.Current.Resources["SecondaryFillBrush"];
                    SolidColorBrush strokeBrush = 
                        (SolidColorBrush)Application.Current.Resources["SecondaryStrokeBrush"];
                    ellipse.Fill = fillBrush;
                    ellipse.Stroke = strokeBrush;
                }
            }
        }
        // Using a DependencyProperty as the backing store for PrimaryEllipse.  
        // This enables animation, styling, binding, etc...
        public static readonly DependencyProperty PrimaryEllipseProperty =
            DependencyProperty.Register("PrimaryEllipse", 
                typeof(bool), typeof(PointerEllipse), new PropertyMetadata(false));


        /// <summary>
        /// Gets or sets the diameter of the PointerEllipse object.
        /// </summary>
        public int Diameter
        {
            get { return (int)GetValue(DiameterProperty); }
            set { SetValue(DiameterProperty, value); }
        }
        // Using a DependencyProperty as the backing store for Diameter.  This enables animation, styling, binding, etc...
        public static readonly DependencyProperty DiameterProperty =
            DependencyProperty.Register("Diameter", typeof(int), 
                typeof(PointerEllipse), new PropertyMetadata(120));
    }
}
```

### <a name="create-the-ui"></a>Создание пользовательского интерфейса
Пользовательский интерфейс в этом примере ограничен входными данными **[Canvas](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.canvas)**, где мы отслеживаем все указатели, а также отображаем индикаторы указателей и анимацию основного указателя (если применимо) вместе с панелью заголовка, содержащей счетчик указателя и идентификатор основного указателя.

Вот код MainPage.xaml.

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <StackPanel x:Name="HeaderPanel" 
                Orientation="Horizontal" 
                Grid.Row="0">
        <StackPanel.Transitions>
            <TransitionCollection>
                <AddDeleteThemeTransition/>
            </TransitionCollection>
        </StackPanel.Transitions>
        <TextBlock x:Name="Header" 
                    Text="Basic pointer tracking sample - IsPrimary" 
                    Style="{ThemeResource HeaderTextBlockStyle}" 
                    Margin="10,0,0,0" />
        <TextBlock x:Name="PointerCounterLabel"
                    VerticalAlignment="Center"                 
                    Style="{ThemeResource BodyTextBlockStyle}"
                    Text="Number of pointers: " 
                    Margin="50,0,0,0"/>
        <TextBlock x:Name="PointerCounter"
                    VerticalAlignment="Center"                 
                    Style="{ThemeResource BodyTextBlockStyle}"
                    Text="0" 
                    Margin="10,0,0,0"/>
        <TextBlock x:Name="PointerPrimaryLabel"
                    VerticalAlignment="Center"                 
                    Style="{ThemeResource BodyTextBlockStyle}"
                    Text="Primary: " 
                    Margin="50,0,0,0"/>
        <TextBlock x:Name="PointerPrimary"
                    VerticalAlignment="Center"                 
                    Style="{ThemeResource BodyTextBlockStyle}"
                    Text="n/a" 
                    Margin="10,0,0,0"/>
    </StackPanel>
    
    <Grid Grid.Row="1">
        <!--The canvas where we render the pointer UI.-->
        <Canvas x:Name="pointerCanvas"/>
    </Grid>
</Grid>
```

### <a name="handle-pointer-events"></a>Обработка событий указателя

Наконец мы определяем основные обработчики событий указателя в коде программной части MainPage.xaml.cs. Мы не воспроизводим здесь код, подобно основным принципам, рассмотренным в предыдущем примере, но вы можете скачать рабочий пример в разделе [Пример ввода указателя (UserControl с анимацией)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-pointers-animation.zip).

## <a name="related-articles"></a>Смежные разделы

**Примеры в разделе**
* [Пример ввода указателя (базовый)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-pointers.zip)
* [Пример ввода указателя (UserControl с анимацией)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-pointers-animation.zip)

**Другие примеры**
* [Пример базового ввода](http://go.microsoft.com/fwlink/p/?LinkID=620302)
* [Пример ввода с малой задержкой](http://go.microsoft.com/fwlink/p/?LinkID=620304)
* [Пример режима взаимодействия с пользователем](http://go.microsoft.com/fwlink/p/?LinkID=619894)
* [Пример визуальных элементов фокуса](http://go.microsoft.com/fwlink/p/?LinkID=619895)

**Примеры архива**
* [Ввод: пример событий пользовательского ввода на XAML](http://go.microsoft.com/fwlink/p/?linkid=226855)
* [Ввод: пример возможностей устройств](http://go.microsoft.com/fwlink/p/?linkid=231530)
* [Ввод: пример управления и жестов (C++)](http://go.microsoft.com/fwlink/p/?linkid=231605)
* [Ввод: пример тестирования сенсорного ввода](http://go.microsoft.com/fwlink/p/?linkid=231590)
* [Пример прокрутки, сдвига и масштабирования на XAML](http://go.microsoft.com/fwlink/p/?linkid=251717)
* [Ввод: пример упрощенного рукописного ввода](http://go.microsoft.com/fwlink/p/?linkid=246570)