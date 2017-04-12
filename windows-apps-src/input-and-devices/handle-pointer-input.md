---
author: Karl-Bridge-Microsoft
Description: "Вы можете получать, обрабатывать и контролировать входные данные от указывающих устройств, таких как устройство сенсорного ввода, мышь, перо или стилус и сенсорная панель, в приложениях универсальной платформы Windows (UWP)."
title: "Работа с входными данными указателя"
ms.assetid: BDBC9E33-4037-4671-9596-471DCF855C82
label: Handle pointer input
template: detail.hbs
keywords: "перо, мышь, сенсорная панель, сенсорный ввод, указатель, ввод, взаимодействие с пользователем"
ms.author: kbridge
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.openlocfilehash: 5923b02d2d41a9d5311f39cb02f0bc58f5f6188c
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="handle-pointer-input"></a>Работа с входными данными указателя
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Вы можете получать, обрабатывать и контролировать входные данные от указывающих устройств, таких как устройство сенсорного ввода, мышь, перо или стилус и сенсорная панель, в приложениях универсальной платформы Windows (UWP).

<div class="important-apis" >
<b>Важные API</b><br/>
<ul>
<li>[**Windows.Devices.Input**](https://msdn.microsoft.com/library/windows/apps/br225648)</li>
<li>[**Windows.UI.Input**](https://msdn.microsoft.com/library/windows/apps/br208383)</li>
<li>[**Windows.UI.Xaml.Input**](https://msdn.microsoft.com/library/windows/apps/br242084)</li>
</ul>
</div>

**Важно**  
Если вы реализуете собственную поддержку взаимодействий, помните: пользователи ожидают, что способ взаимодействия с элементами пользовательского интерфейса приложения будет интуитивно понятным. Рекомендуется моделировать взаимодействия с пользователем на базе [списка элементов управления](https://msdn.microsoft.com/library/windows/apps/mt185406) для обеспечения единообразия элементов и возможности их обнаруживать. Элементы управления платформы обеспечивают все механизмы взаимодействия с пользователем, используемые в приложениях универсальной платформы Windows (UWP), в том числе стандартные взаимодействия, анимированные физические эффекты, визуальную обратную связь и специальные возможности. Создавайте пользовательские взаимодействия, только если существует явное, четко определенное требование и если ни один стандартный механизм взаимодействия не поддерживает ваш сценарий.


## <a name="pointers"></a>Указатели
Зачастую взаимодействие предполагает определение пользователем объекта, с которым он собирается взаимодействовать, путем указания на него с помощью сенсорного ввода, мыши, пера/стилуса и сенсорной панели. Поскольку необработанные данные устройства HID, предоставляемые такими устройствами ввода, содержат много общих свойств, эта информация преобразовывается в единый входной стек и представляется в виде консолидированных, не зависящих от устройства данных указателя. Ваши приложения UWP смогут использовать эти данные независимо от того, какое устройство ввода используется.

**Примечание.** Сведения, касающиеся устройства, также извлекаются из необработанных данных HID, если это необходимо для вашего приложения.

 

Каждая точка ввода (или контакта) на входном стеке представляется объектом [**Pointer**](https://msdn.microsoft.com/library/windows/apps/br227968), доступ к которому предоставляется с помощью параметра [**PointerRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943076) на основании различных событий указателя. В случае ввода несколькими перьями или мультисенсорного ввода каждый контакт считается уникальной точкой ввода.

## <a name="pointer-events"></a>События указателя


События указателя предоставляют основную информацию, например, о состоянии обнаружения (на расстоянии или в контакте) и типе устройства, а также дополнительную информацию, например, о давлении и геометрии контакта. Кроме того, предоставляются сведения о свойствах конкретных устройств, например, о том, какую кнопку мыши нажал пользователь и не использует ли он стирающий конец пера. Если ваше приложение должно различать устройства ввода и их возможности, см. раздел [Определение типов устройств ввода](identify-input-devices.md).

Приложения UWP могут воспринимать приведенные ниже события указателя.

**Примечание.** Вызовите метод [**CapturePointer**](https://msdn.microsoft.com/library/windows/apps/br208918), чтобы ограничить ввод указателя определенным элементом пользовательского интерфейса. Когда указатель захвачен элементом, только этот объект получает сведения о событиях ввода указателя, даже если указатель перемещается за пределы выбранной области. Обычно указатель захватывается в обработчике событий [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208971), поскольку свойство [**IsInContact**](https://msdn.microsoft.com/library/windows/apps/br227976) (нажатие кнопки мыши, сенсорный ввод или контакт со стилусом) должно быть действительным для успешного выполнения команды захвата указателя **CapturePointer**.

 

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
<td align="left"><p>([<strong>PointerCanceled</strong>]https://msdn.microsoft.com/library/windows/apps/br208964)</p></td>
<td align="left"><p>Происходит, когда указатель отменяется платформой.</p>
<ul>
<li>Указатели касания отменяются в том случае, если перо обнаруживается в пределах поверхности ввода.</li>
<li>Активный контакт не обнаруживается дольше 100мс.</li>
<li>Изменены настройки монитора/дисплея (разрешение, параметры, конфигурация с несколькими мониторами).</li>
<li>Рабочий стол заблокирован или пользователь вышел из системы.</li>
<li>Количество одновременных контактов превысило значение, поддерживаемое устройством.</li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><p>([<strong>PointerCaptureLost</strong>]https://msdn.microsoft.com/library/windows/apps/br208965)</p></td>
<td align="left"><p>Происходит, если указатель захватывается другим элементом пользовательского интерфейса, если указатель отпущен пользователем или если происходит захват другого указателя программным путем.</p>
<div class="alert">
<strong>Примечание.</strong> Соответствующее событие захвата указателя не происходит.
</div>
<div>
 
</div></td>
</tr>
<tr class="odd">
<td align="left"><p>[<strong>PointerEntered</strong>](https://msdn.microsoft.com/library/windows/apps/br208968)</p></td>
<td align="left"><p>Происходит, когда указатель входит в контактный участок элемента. Это может происходить немного по-разному в случае сенсорного ввода, использования сенсорной панели, мыши и пера.</p>
<ul>
<li>В случае сенсорного ввода для запуска этого события требуется контакт с пальцем либо путем непосредственного прикосновения к элементу, либо путем касания и перемещения пальца в контактный участок элемента.</li>
<li>При использовании мыши и сенсорной панели на экране присутствует курсор, который постоянно отображается и запускает событие, даже если кнопка мыши или сенсорной панели не нажата.</li>
<li>Как и в случае сенсорного ввода, перо запускает это событие при непосредственном контакте с элементом либо при перемещении в контактный участок элемента. Однако перо также имеет состояние наведения ([<strong>IsInRange</strong>] (https://msdn.microsoft.com/library/windows/apps/br227977)), которое, если активно, запускает это событие.</li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><p>[<strong>PointerExited</strong>](https://msdn.microsoft.com/library/windows/apps/br208969)</p></td>
<td align="left"><p>Происходит, когда указатель выходит из контактного участка элемента. Это может происходить немного по-разному в случае сенсорного ввода, использования сенсорной панели, мыши и пера.</p>
<ul>
<li>В случае сенсорного ввода для запуска этого события требуется контакт с пальцем, когда указатель выходит за пределы контактного участка элемента.</li>
<li>При использовании мыши и сенсорной панели на экране присутствует курсор, который постоянно отображается и запускает событие, даже если кнопка мыши или сенсорной панели не нажата.</li>
<li>Как и в случае сенсорного ввода, перо запускает это событие при выходе за пределы контактного участка элемента. Однако перо также имеет состояние наведения ([<strong>IsInRange</strong>] (https://msdn.microsoft.com/library/windows/apps/br227977)), которое запускает это событие при изменении состояния с "true" на "false".</li>
</ul></td>
</tr>
<tr class="odd">
<td align="left"><p>[<strong>PointerMoved</strong>](https://msdn.microsoft.com/library/windows/apps/br208970)</p></td>
<td align="left"><p>Происходит, когда указатель изменяет координаты, состояние кнопок, давление, наклон или геометрию контакта (например, ширину и высоту) в пределах контактного участка элемента. Это может происходить немного по-разному в случае сенсорного ввода, использования сенсорной панели, мыши и пера.</p>
<ul>
<li>Сенсорный ввод требует контакта с пальцем и запускает это событие только в случае контакта в пределах контактного участка элемента.</li>
<li>При использовании мыши и сенсорной панели на экране присутствует курсор, который постоянно отображается и запускает событие, даже если кнопка мыши или сенсорной панели не нажата.</li>
<li>Как и в случае сенсорного ввода, перо запускает это событие при контакте в пределах контактного участка элемента. Однако перо также имеет состояние наведения ([<strong>IsInRange</strong>] (https://msdn.microsoft.com/library/windows/apps/br227977)), которое запускает это событие, если имеет значение "true" и происходит в пределах контактного участка.</li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><p>[<strong>PointerPressed</strong>](https://msdn.microsoft.com/library/windows/apps/br208971)</p></td>
<td align="left"><p>Происходит, когда указатель указывает действие нажатия (например, касание, нажатие кнопки мыши, касание пера или нажатие кнопки сенсорной панели) в пределах контактного участка элемента.</p>
<p>Для этого события необходимо вызывать [<strong>CapturePointer</strong>](https://msdn.microsoft.com/library/windows/apps/br208918) из обработчика.</p></td>
</tr>
<tr class="odd">
<td align="left"><p>[<strong>PointerReleased</strong>](https://msdn.microsoft.com/library/windows/apps/br208972)</p></td>
<td align="left"><p>Происходит, когда указатель указывает действие отпускания (например, разрыв контакта с пальцем или пером, поднятие кнопки мыши или кнопки сенсорной панели) на контактном участке элемента или, в случае захвата указателя, за пределами контактного участка.</p></td>
</tr>
<tr class="even">
<td align="left"><p>[<strong>PointerWheelChanged</strong>](https://msdn.microsoft.com/library/windows/apps/br208973)</p></td>
<td align="left"><p>Происходит при вращении колесика мыши.</p>
<p>Ввод мыши связывается с отдельным указателем, назначаемым при первом обнаружении ввода мыши. При щелчке кнопки мыши (левой, правой или колеса) создается вспомогательная связь между указателем и этой кнопкой с помощью события [<strong>PointerMoved</strong>](https://msdn.microsoft.com/library/windows/apps/br208970).</p></td>
</tr>
</tbody>
</table>

 

## <a name="example"></a>Пример.


Ниже представлены примеры кода из основного приложения отслеживания указателя, которые показывают, как просматривать и обрабатывать события указателя и получать сведения о различных свойствах активных указателей.

### <a name="create-the-ui"></a>Создание пользовательского интерфейса

В этом примере мы используем в качестве целевого объекта для ввода от указателя прямоугольник (`targetContainer`). Цвет целевого объекта изменяется при изменении состояния указателя.

Сведения для каждого указателя отображаются в перемещаемом текстовом блоке, который движется вместе с указателем. Сами события указателя отображаются слева от прямоугольника (для отображения последовательности событий).

Вот XAML-код для этого примера.

```XAML
<Page
    x:Class="PointerInput.MainPage"
    IsTabStop="false"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:PointerInput"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    Name="page">

    <Grid Background="{StaticResource ApplicationForegroundThemeBrush}">
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="69.458" />
            <ColumnDefinition Width="80.542"/>
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
        <Button Name="buttonClear"
                Foreground="White"
                Width="100"
                Height="100">
            clear
        </Button>
        <TextBox Name="eventLog" 
                 Grid.Column="1"
                 Grid.Row="0"
                 Grid.RowSpan="3" 
                 Background="#000000" 
                 TextWrapping="Wrap" 
                 Foreground="#FFFFFF" 
                 ScrollViewer.VerticalScrollBarVisibility="Visible" 
                 BorderThickness="0" Grid.ColumnSpan="2"/>
    </Grid>
</Page>
```

### <a name="listen-for-pointer-events"></a>Ожидание событий указателя

В большинстве случаев мы рекомендуем получать сведения от указателя посредством [**PointerRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943076) обработчика событий.

Если аргумент события не предоставляет необходимых данных указателя, можно получить доступ к расширенным сведениям [**PointerPoint**](https://msdn.microsoft.com/library/windows/apps/br242038), выраженным через методы [**GetCurrentPoint**](https://msdn.microsoft.com/library/windows/apps/hh943077) и [**GetIntermediatePoints**](https://msdn.microsoft.com/library/windows/apps/hh943078) для [**PointerRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943076).

В этом примере в качестве целевого объекта для ввода от указателя мы используем прямоугольник (`targetContainer`). Цвет целевого объекта изменяется при изменении состояния указателя.

Следующий код задает целевой объект, объявляет глобальные переменные и определяет прослушиватели для различных событий указателя для целевого объекта.

```CSharp
        // For this example, we track simultaneous contacts in case the 
        // number of contacts has reached the maximum supported by the device.
        // Depending on the device, additional contacts might be ignored 
        // (PointerPressed not fired). 
        uint numActiveContacts;
        Windows.Devices.Input.TouchCapabilities touchCapabilities = new Windows.Devices.Input.TouchCapabilities();

        // Dictionary to maintain information about each active contact. 
        // An entry is added during PointerPressed/PointerEntered events and removed 
        // during PointerReleased/PointerCaptureLost/PointerCanceled/PointerExited events.
        Dictionary<uint, Windows.UI.Xaml.Input.Pointer> contacts;

        public MainPage()
        {
            this.InitializeComponent();
            numActiveContacts = 0;
            // Initialize the dictionary.
            contacts = new Dictionary<uint, Windows.UI.Xaml.Input.Pointer>((int)touchCapabilities.Contacts);
            // Declare the pointer event handlers.
            Target.PointerPressed += new PointerEventHandler(Target_PointerPressed);
            Target.PointerEntered += new PointerEventHandler(Target_PointerEntered);
            Target.PointerReleased += new PointerEventHandler(Target_PointerReleased);
            Target.PointerExited += new PointerEventHandler(Target_PointerExited);
            Target.PointerCanceled += new PointerEventHandler(Target_PointerCanceled);
            Target.PointerCaptureLost += new PointerEventHandler(Target_PointerCaptureLost);
            Target.PointerMoved += new PointerEventHandler(Target_PointerMoved);
            Target.PointerWheelChanged += new PointerEventHandler(Target_PointerWheelChanged);

            buttonClear.Click += new RoutedEventHandler(ButtonClear_Click);
        }

        private void ButtonClear_Click(object sender, RoutedEventArgs e)
        {
            eventLog.Text = "";
        }

```

### <a name="handle-pointer-events"></a>Обработка событий указателя

Далее мы воспользуемся обратной связью пользовательского интерфейса для демонстрации обработчиков основных событий указателя.

-   Этот обработчик управляет событием [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208971). Мы добавляем это событие в журнал событий, добавляем указатель в массив указателей, используемый для отслеживания интересующих нас указателей, и отображаем данные указателя.

    **Примечание.** События [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208971) и [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972) не всегда возникают парами. Приложение должно ожидать и обрабатывать любые события, которые могут содержать действие указателя "вниз" (например, [**PointerExited**](https://msdn.microsoft.com/library/windows/apps/br208969), [**PointerCanceled**](https://msdn.microsoft.com/library/windows/apps/br208964) и [**PointerCaptureLost**](https://msdn.microsoft.com/library/windows/apps/br208965)).

     

```    CSharp
        // PointerPressed and PointerReleased events do not always occur in pairs. 
            // Your app should listen for and handle any event that might conclude a pointer down action 
            // (such as PointerExited, PointerCanceled, and PointerCaptureLost).
            // For this example, we track the number of contacts in case the 
            // number of contacts has reached the maximum supported by the device.
            // Depending on the device, additional contacts might be ignored 
            // (PointerPressed not fired). 
            void Target_PointerPressed(object sender, PointerRoutedEventArgs e)
            {
                Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

                // Update event sequence.
                eventLog.Text += "\nDown: " + ptr.PointerId;

                // Change background color of target when pointer contact detected.
                Target.Fill = new SolidColorBrush(Windows.UI.Colors.Green);

                // Prevent most handlers along the event route from handling the same event again.
                e.Handled = true;

                // Lock the pointer to the target.
                Target.CapturePointer(e.Pointer);

                // Update event sequence.
                eventLog.Text += "\nPointer captured: " + ptr.PointerId;

                // Check if pointer already exists (for example, enter occurred prior to press).
                if (contacts.ContainsKey(ptr.PointerId))
                {
                    return;
                }
                // Add contact to dictionary.
                contacts[ptr.PointerId] = ptr;
                ++numActiveContacts;

                // Display pointer details.
                createInfoPop(e);
            }
```

-   Этот обработчик управляет событием [**PointerEntered**](https://msdn.microsoft.com/library/windows/apps/br208968). Мы добавляем это событие в журнал событий, добавляем указатель в коллекцию указателей и отображаем данные указателя.

```    CSharp
        private void Target_PointerEntered(object sender, PointerRoutedEventArgs e)
            {
                Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

                // Update event sequence.
                eventLog.Text += "\nEntered: " + ptr.PointerId;

                if (contacts.Count == 0)
                {
                    // Change background color of target when pointer contact detected.
                    Target.Fill = new SolidColorBrush(Windows.UI.Colors.Blue);
                }

                // Check if pointer already exists (if enter occurred prior to down).
                if (contacts.ContainsKey(ptr.PointerId))
                {
                    return;
                }

                // Add contact to dictionary.
                contacts[ptr.PointerId] = ptr;
                ++numActiveContacts;

                // Prevent most handlers along the event route from handling the same event again.
                e.Handled = true;

                // Display pointer details.
                createInfoPop(e);
            }
```

-   Этот обработчик управляет событием [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208970). Мы добавляем событие в журнал событий и обновляем данные указателя.

    **Важно!** Ввод с мыши связывается с отдельным указателем, назначаемым при первом обнаружении ввода с мыши. При щелчке кнопки мыши (левой, правой или колеса) создается вспомогательная связь между указателем и этой кнопкой с помощью события [**PointerPressed**](https://msdn.microsoft.com/library/windows/apps/br208971). Событие [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972) инициируется только тогда, когда отпускается та же кнопка мыши (никакая другая кнопка не может быть связана с указателем до завершения события). Из-за этой исключительной связи другие щелчки кнопок мыши обрабатываются с помощью события [**PointerMoved**](https://msdn.microsoft.com/library/windows/apps/br208970).

     

```    CSharp
private void Target_PointerMoved(object sender, PointerRoutedEventArgs e)
    {
        Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

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
        if (ptr.PointerDeviceType == Windows.Devices.Input.PointerDeviceType.Mouse)
        {
            // To get mouse state, we need extended pointer details.
            // We get the pointer info through the getCurrentPoint method
            // of the event argument. 
            Windows.UI.Input.PointerPoint ptrPt = e.GetCurrentPoint(Target);
            if (ptrPt.Properties.IsLeftButtonPressed)
            {
                eventLog.Text += "\nLeft button: " + ptrPt.PointerId;
            }
            if (ptrPt.Properties.IsMiddleButtonPressed)
            {
                eventLog.Text += "\nWheel button: " + ptrPt.PointerId;
            }
            if (ptrPt.Properties.IsRightButtonPressed)
            {
                eventLog.Text += "\nRight button: " + ptrPt.PointerId;
            }
        }

        // Prevent most handlers along the event route from handling the same event again.
        e.Handled = true;

        // Display pointer details.
        updateInfoPop(e);
    }
```

-   Этот обработчик управляет событием [**PointerWheelChanged**](https://msdn.microsoft.com/library/windows/apps/br208973). Мы добавляем это событие в журнал событий, при необходимости добавляем указатель в массив указателей и отображаем данные указателя.

```    CSharp
private void Target_PointerWheelChanged(object sender, PointerRoutedEventArgs e)
    {
        Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

        // Update event sequence.
        eventLog.Text += "\nMouse wheel: " + ptr.PointerId;

        // Check if pointer already exists (for example, enter occurred prior to wheel).
        if (contacts.ContainsKey(ptr.PointerId))
        {
            return;
        }

        // Add contact to dictionary.
        contacts[ptr.PointerId] = ptr;
        ++numActiveContacts;

        // Prevent most handlers along the event route from handling the same event again.
        e.Handled = true;

        // Display pointer details.
        createInfoPop(e);
    }
```

-   Этот обработчик управляет событием [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972), когда контакт с дигитайзером прекращен. Мы добавляем это событие в журнал событий, удаляем указатель из коллекции указателей и обновляем данные указателя.

```    CSharp
void Target_PointerReleased(object sender, PointerRoutedEventArgs e)
    {
        Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

        // Update event sequence.
        eventLog.Text += "\nUp: " + ptr.PointerId;

        // If event source is mouse or touchpad and the pointer is still 
        // over the target, retain pointer and pointer details.
        // Return without removing pointer from pointers dictionary.
        // For this example, we assume a maximum of one mouse pointer.
        if (ptr.PointerDeviceType != Windows.Devices.Input.PointerDeviceType.Mouse)
        {
            // Update target UI.
            Target.Fill = new SolidColorBrush(Windows.UI.Colors.Red);

            destroyInfoPop(ptr);

            // Remove contact from dictionary.
            if (contacts.ContainsKey(ptr.PointerId))
            {
                contacts[ptr.PointerId] = null;
                contacts.Remove(ptr.PointerId);
                --numActiveContacts;
            }

            // Release the pointer from the target.
            Target.ReleasePointerCapture(e.Pointer);

            // Update event sequence.
            eventLog.Text += "\nPointer released: " + ptr.PointerId;

            // Prevent most handlers along the event route from handling the same event again.
            e.Handled = true;
        }
        else
        {
            Target.Fill = new SolidColorBrush(Windows.UI.Colors.Blue);
        }

    }
```

-   Этот обработчик управляет событием [**PointerExited**](https://msdn.microsoft.com/library/windows/apps/br208969), когда контакт с дигитайзером поддерживается. Мы добавляем это событие в журнал событий, удаляем указатель из массива указателей и обновляем данные указателя.

```    CSharp
private void Target_PointerExited(object sender, PointerRoutedEventArgs e)
    {
        Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

        // Update event sequence.
        eventLog.Text += "\nPointer exited: " + ptr.PointerId;

        // Remove contact from dictionary.
        if (contacts.ContainsKey(ptr.PointerId))
        {
            contacts[ptr.PointerId] = null;
            contacts.Remove(ptr.PointerId);
            --numActiveContacts;
        }

        // Update the UI and pointer details.
        destroyInfoPop(ptr);

        if (contacts.Count == 0)
        {
            Target.Fill = new SolidColorBrush(Windows.UI.Colors.Red);
        }
        
        // Prevent most handlers along the event route from handling the same event again.
        e.Handled = true;
    }
```

-   Этот обработчик управляет событием [**PointerCanceled**](https://msdn.microsoft.com/library/windows/apps/br208964). Мы добавляем это событие в журнал событий, удаляем указатель из массива указателей и обновляем данные указателя.

```    CSharp
// Fires for for various reasons, including: 
    //    - Touch contact canceled by pen coming into range of the surface.
    //    - The device doesn&#39;t report an active contact for more than 100ms.
    //    - The desktop is locked or the user logged off. 
    //    - The number of simultaneous contacts exceeded the number supported by the device.
    private void Target_PointerCanceled(object sender, PointerRoutedEventArgs e)
    {
        Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

        // Update event sequence.
        eventLog.Text += "\nPointer canceled: " + ptr.PointerId;

        // Remove contact from dictionary.
        if (contacts.ContainsKey(ptr.PointerId))
        {
            contacts[ptr.PointerId] = null;
            contacts.Remove(ptr.PointerId);
            --numActiveContacts;
        }

        destroyInfoPop(ptr);

        if (contacts.Count == 0)
        {
            Target.Fill = new SolidColorBrush(Windows.UI.Colors.Black);
        }
        // Prevent most handlers along the event route from handling the same event again.
        e.Handled = true;
    }
```

-   Этот обработчик управляет событием [**PointerCaptureLost**](https://msdn.microsoft.com/library/windows/apps/br208965). Мы добавляем это событие в журнал событий, удаляем указатель из массива указателей и обновляем данные указателя.

    **Примечание.** Событие [**PointerCaptureLost**](https://msdn.microsoft.com/library/windows/apps/br208965) может произойти вместо события [**PointerReleased**](https://msdn.microsoft.com/library/windows/apps/br208972). Захват указателя может быть потерян по разным причинам.

     

```    CSharp
// Fires for for various reasons, including: 
    //    - User interactions
    //    - Programmatic capture of another pointer
    //    - Captured pointer was deliberately released
    // PointerCaptureLost can fire instead of PointerReleased. 
    private void Target_PointerCaptureLost(object sender, PointerRoutedEventArgs e)
    {
        Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

        // Update event sequence.
        eventLog.Text += "\nPointer capture lost: " + ptr.PointerId;

        // Remove contact from dictionary.
        if (contacts.ContainsKey(ptr.PointerId))
        {
            contacts[ptr.PointerId] = null;
            contacts.Remove(ptr.PointerId);
            --numActiveContacts;
        }

        destroyInfoPop(ptr);

        if (contacts.Count == 0)
        {
            Target.Fill = new SolidColorBrush(Windows.UI.Colors.Black);
        }
        // Prevent most handlers along the event route from handling the same event again.
        e.Handled = true;
    }
```

### <a name="get-pointer-properties"></a>Получение свойств указателя

Как упоминалось ранее, вам необходимо получить наиболее полные сведения указателя от объекта [**Windows.UI.Input.PointerPoint**](https://msdn.microsoft.com/library/windows/apps/br242038), получаемые с помощью методов [**GetCurrentPoint**](https://msdn.microsoft.com/library/windows/apps/hh943077) и [**GetIntermediatePoints**](https://msdn.microsoft.com/library/windows/apps/hh943078) класса [**PointerRoutedEventArgs**](https://msdn.microsoft.com/library/windows/apps/hh943076).

-   Сначала мы создаем новый [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652) для каждого указателя.

```    CSharp
        void createInfoPop(PointerRoutedEventArgs e)
            {
                TextBlock pointerDetails = new TextBlock();
                Windows.UI.Input.PointerPoint ptrPt = e.GetCurrentPoint(Target);
                pointerDetails.Name = ptrPt.PointerId.ToString();
                pointerDetails.Foreground = new SolidColorBrush(Windows.UI.Colors.White);
                pointerDetails.Text = queryPointer(ptrPt);

                TranslateTransform x = new TranslateTransform();
                x.X = ptrPt.Position.X + 20;
                x.Y = ptrPt.Position.Y + 20;
                pointerDetails.RenderTransform = x;

                Container.Children.Add(pointerDetails);
            }
```

-   Затем мы предоставляем способ обновления сведений об указателе в существующем элементе [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/br209652), связанном с этим указателем.

```    CSharp
        void updateInfoPop(PointerRoutedEventArgs e)
            {
                foreach (var pointerDetails in Container.Children)
                {
                    if (pointerDetails.GetType().ToString() == "Windows.UI.Xaml.Controls.TextBlock")
                    {
                        TextBlock _TextBlock = (TextBlock)pointerDetails;
                        if (_TextBlock.Name == e.Pointer.PointerId.ToString())
                        {
                            // To get pointer location details, we need extended pointer info.
                            // We get the pointer info through the getCurrentPoint method
                            // of the event argument. 
                            Windows.UI.Input.PointerPoint ptrPt = e.GetCurrentPoint(Target);
                            TranslateTransform x = new TranslateTransform();
                            x.X = ptrPt.Position.X + 20;
                            x.Y = ptrPt.Position.Y + 20;
                            pointerDetails.RenderTransform = x;
                            _TextBlock.Text = queryPointer(ptrPt);
                        }
                    }
                }
            }
```

-   Наконец, мы запрашиваем различные свойства указателя.

```    CSharp
         String queryPointer(PointerPoint ptrPt)
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

                 GeneralTransform gt = Target.TransformToVisual(page);
                 Point screenPoint;

                 screenPoint = gt.TransformPoint(new Point(ptrPt.Position.X, ptrPt.Position.Y));
                 details += "\nPointer Id: " + ptrPt.PointerId.ToString() +
                     "\nPointer location (parent): " + ptrPt.Position.X + ", " + ptrPt.Position.Y +
                     "\nPointer location (screen): " + screenPoint.X + ", " + screenPoint.Y;
                 return details;
             }
```

### <a name="complete-example"></a>Полный пример

Ниже приводится код C\# для этого примера. Ссылки на более сложные примеры см. в разделе "Связанные разделы" в нижней части страницы.

```CSharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Input;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at http://go.microsoft.com/fwlink/?LinkId=234238

namespace PointerInput
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        // For this example, we track simultaneous contacts in case the 
        // number of contacts has reached the maximum supported by the device.
        // Depending on the device, additional contacts might be ignored 
        // (PointerPressed not fired). 
        uint numActiveContacts;
        Windows.Devices.Input.TouchCapabilities touchCapabilities = new Windows.Devices.Input.TouchCapabilities();

        // Dictionary to maintain information about each active contact. 
        // An entry is added during PointerPressed/PointerEntered events and removed 
        // during PointerReleased/PointerCaptureLost/PointerCanceled/PointerExited events.
        Dictionary<uint, Windows.UI.Xaml.Input.Pointer> contacts;

        public MainPage()
        {
            this.InitializeComponent();
            numActiveContacts = 0;
            // Initialize the dictionary.
            contacts = new Dictionary<uint, Windows.UI.Xaml.Input.Pointer>((int)touchCapabilities.Contacts);
            // Declare the pointer event handlers.
            Target.PointerPressed += new PointerEventHandler(Target_PointerPressed);
            Target.PointerEntered += new PointerEventHandler(Target_PointerEntered);
            Target.PointerReleased += new PointerEventHandler(Target_PointerReleased);
            Target.PointerExited += new PointerEventHandler(Target_PointerExited);
            Target.PointerCanceled += new PointerEventHandler(Target_PointerCanceled);
            Target.PointerCaptureLost += new PointerEventHandler(Target_PointerCaptureLost);
            Target.PointerMoved += new PointerEventHandler(Target_PointerMoved);
            Target.PointerWheelChanged += new PointerEventHandler(Target_PointerWheelChanged);

            buttonClear.Click += new RoutedEventHandler(ButtonClear_Click);
        }

        private void ButtonClear_Click(object sender, RoutedEventArgs e)
        {
            eventLog.Text = "";
        }


        // PointerPressed and PointerReleased events do not always occur in pairs. 
        // Your app should listen for and handle any event that might conclude a pointer down action 
        // (such as PointerExited, PointerCanceled, and PointerCaptureLost).
        // For this example, we track the number of contacts in case the 
        // number of contacts has reached the maximum supported by the device.
        // Depending on the device, additional contacts might be ignored 
        // (PointerPressed not fired). 
        void Target_PointerPressed(object sender, PointerRoutedEventArgs e)
        {
            Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

            // Update event sequence.
            eventLog.Text += "\nDown: " + ptr.PointerId;

            // Change background color of target when pointer contact detected.
            Target.Fill = new SolidColorBrush(Windows.UI.Colors.Green);

            // Prevent most handlers along the event route from handling the same event again.
            e.Handled = true;

            // Lock the pointer to the target.
            Target.CapturePointer(e.Pointer);

            // Update event sequence.
            eventLog.Text += "\nPointer captured: " + ptr.PointerId;

            // Check if pointer already exists (for example, enter occurred prior to press).
            if (contacts.ContainsKey(ptr.PointerId))
            {
                return;
            }
            // Add contact to dictionary.
            contacts[ptr.PointerId] = ptr;
            ++numActiveContacts;

            // Display pointer details.
            createInfoPop(e);
        }

        void Target_PointerReleased(object sender, PointerRoutedEventArgs e)
        {
            Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

            // Update event sequence.
            eventLog.Text += "\nUp: " + ptr.PointerId;

            // If event source is mouse or touchpad and the pointer is still 
            // over the target, retain pointer and pointer details.
            // Return without removing pointer from pointers dictionary.
            // For this example, we assume a maximum of one mouse pointer.
            if (ptr.PointerDeviceType != Windows.Devices.Input.PointerDeviceType.Mouse)
            {
                // Update target UI.
                Target.Fill = new SolidColorBrush(Windows.UI.Colors.Red);

                destroyInfoPop(ptr);

                // Remove contact from dictionary.
                if (contacts.ContainsKey(ptr.PointerId))
                {
                    contacts[ptr.PointerId] = null;
                    contacts.Remove(ptr.PointerId);
                    --numActiveContacts;
                }

                // Release the pointer from the target.
                Target.ReleasePointerCapture(e.Pointer);

                // Update event sequence.
                eventLog.Text += "\nPointer released: " + ptr.PointerId;

                // Prevent most handlers along the event route from handling the same event again.
                e.Handled = true;
            }
            else
            {
                Target.Fill = new SolidColorBrush(Windows.UI.Colors.Blue);
            }

        }

        private void Target_PointerMoved(object sender, PointerRoutedEventArgs e)
        {
            Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

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
            if (ptr.PointerDeviceType == Windows.Devices.Input.PointerDeviceType.Mouse)
            {
                // To get mouse state, we need extended pointer details.
                // We get the pointer info through the getCurrentPoint method
                // of the event argument. 
                Windows.UI.Input.PointerPoint ptrPt = e.GetCurrentPoint(Target);
                if (ptrPt.Properties.IsLeftButtonPressed)
                {
                    eventLog.Text += "\nLeft button: " + ptrPt.PointerId;
                }
                if (ptrPt.Properties.IsMiddleButtonPressed)
                {
                    eventLog.Text += "\nWheel button: " + ptrPt.PointerId;
                }
                if (ptrPt.Properties.IsRightButtonPressed)
                {
                    eventLog.Text += "\nRight button: " + ptrPt.PointerId;
                }
            }

            // Prevent most handlers along the event route from handling the same event again.
            e.Handled = true;

            // Display pointer details.
            updateInfoPop(e);
        }

        private void Target_PointerEntered(object sender, PointerRoutedEventArgs e)
        {
            Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

            // Update event sequence.
            eventLog.Text += "\nEntered: " + ptr.PointerId;

            if (contacts.Count == 0)
            {
                // Change background color of target when pointer contact detected.
                Target.Fill = new SolidColorBrush(Windows.UI.Colors.Blue);
            }

            // Check if pointer already exists (if enter occurred prior to down).
            if (contacts.ContainsKey(ptr.PointerId))
            {
                return;
            }

            // Add contact to dictionary.
            contacts[ptr.PointerId] = ptr;
            ++numActiveContacts;

            // Prevent most handlers along the event route from handling the same event again.
            e.Handled = true;

            // Display pointer details.
            createInfoPop(e);
        }

        private void Target_PointerWheelChanged(object sender, PointerRoutedEventArgs e)
        {
            Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

            // Update event sequence.
            eventLog.Text += "\nMouse wheel: " + ptr.PointerId;

            // Check if pointer already exists (for example, enter occurred prior to wheel).
            if (contacts.ContainsKey(ptr.PointerId))
            {
                return;
            }

            // Add contact to dictionary.
            contacts[ptr.PointerId] = ptr;
            ++numActiveContacts;

            // Prevent most handlers along the event route from handling the same event again.
            e.Handled = true;

            // Display pointer details.
            createInfoPop(e);
        }

        // Fires for for various reasons, including: 
        //    - User interactions
        //    - Programmatic capture of another pointer
        //    - Captured pointer was deliberately released
        // PointerCaptureLost can fire instead of PointerReleased. 
        private void Target_PointerCaptureLost(object sender, PointerRoutedEventArgs e)
        {
            Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

            // Update event sequence.
            eventLog.Text += "\nPointer capture lost: " + ptr.PointerId;

            // Remove contact from dictionary.
            if (contacts.ContainsKey(ptr.PointerId))
            {
                contacts[ptr.PointerId] = null;
                contacts.Remove(ptr.PointerId);
                --numActiveContacts;
            }

            destroyInfoPop(ptr);

            if (contacts.Count == 0)
            {
                Target.Fill = new SolidColorBrush(Windows.UI.Colors.Black);
            }
            // Prevent most handlers along the event route from handling the same event again.
            e.Handled = true;
        }

        // Fires for for various reasons, including: 
        //    - Touch contact canceled by pen coming into range of the surface.
        //    - The device doesn&#39;t report an active contact for more than 100ms.
        //    - The desktop is locked or the user logged off. 
        //    - The number of simultaneous contacts exceeded the number supported by the device.
        private void Target_PointerCanceled(object sender, PointerRoutedEventArgs e)
        {
            Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

            // Update event sequence.
            eventLog.Text += "\nPointer canceled: " + ptr.PointerId;

            // Remove contact from dictionary.
            if (contacts.ContainsKey(ptr.PointerId))
            {
                contacts[ptr.PointerId] = null;
                contacts.Remove(ptr.PointerId);
                --numActiveContacts;
            }

            destroyInfoPop(ptr);

            if (contacts.Count == 0)
            {
                Target.Fill = new SolidColorBrush(Windows.UI.Colors.Black);
            }
            // Prevent most handlers along the event route from handling the same event again.
            e.Handled = true;
        }

        private void Target_PointerExited(object sender, PointerRoutedEventArgs e)
        {
            Windows.UI.Xaml.Input.Pointer ptr = e.Pointer;

            // Update event sequence.
            eventLog.Text += "\nPointer exited: " + ptr.PointerId;

            // Remove contact from dictionary.
            if (contacts.ContainsKey(ptr.PointerId))
            {
                contacts[ptr.PointerId] = null;
                contacts.Remove(ptr.PointerId);
                --numActiveContacts;
            }

            // Update the UI and pointer details.
            destroyInfoPop(ptr);

            if (contacts.Count == 0)
            {
                Target.Fill = new SolidColorBrush(Windows.UI.Colors.Red);
            }
            
            // Prevent most handlers along the event route from handling the same event again.
            e.Handled = true;
        }

        void createInfoPop(PointerRoutedEventArgs e)
        {
            TextBlock pointerDetails = new TextBlock();
            Windows.UI.Input.PointerPoint ptrPt = e.GetCurrentPoint(Target);
            pointerDetails.Name = ptrPt.PointerId.ToString();
            pointerDetails.Foreground = new SolidColorBrush(Windows.UI.Colors.White);
            pointerDetails.Text = queryPointer(ptrPt);

            TranslateTransform x = new TranslateTransform();
            x.X = ptrPt.Position.X + 20;
            x.Y = ptrPt.Position.Y + 20;
            pointerDetails.RenderTransform = x;

            Container.Children.Add(pointerDetails);
        }

        void destroyInfoPop(Windows.UI.Xaml.Input.Pointer ptr)
        {
            foreach (var pointerDetails in Container.Children)
            {
                if (pointerDetails.GetType().ToString() == "Windows.UI.Xaml.Controls.TextBlock")
                {
                    TextBlock _TextBlock = (TextBlock)pointerDetails;
                    if (_TextBlock.Name == ptr.PointerId.ToString())
                    {
                        Container.Children.Remove(pointerDetails);
                    }
                }
            }
        }

        void updateInfoPop(PointerRoutedEventArgs e)
        {
            foreach (var pointerDetails in Container.Children)
            {
                if (pointerDetails.GetType().ToString() == "Windows.UI.Xaml.Controls.TextBlock")
                {
                    TextBlock _TextBlock = (TextBlock)pointerDetails;
                    if (_TextBlock.Name == e.Pointer.PointerId.ToString())
                    {
                        // To get pointer location details, we need extended pointer info.
                        // We get the pointer info through the getCurrentPoint method
                        // of the event argument. 
                        Windows.UI.Input.PointerPoint ptrPt = e.GetCurrentPoint(Target);
                        TranslateTransform x = new TranslateTransform();
                        x.X = ptrPt.Position.X + 20;
                        x.Y = ptrPt.Position.Y + 20;
                        pointerDetails.RenderTransform = x;
                        _TextBlock.Text = queryPointer(ptrPt);
                    }
                }
            }
        }

         String queryPointer(PointerPoint ptrPt)
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

             GeneralTransform gt = Target.TransformToVisual(page);
             Point screenPoint;

             screenPoint = gt.TransformPoint(new Point(ptrPt.Position.X, ptrPt.Position.Y));
             details += "\nPointer Id: " + ptrPt.PointerId.ToString() +
                 "\nPointer location (parent): " + ptrPt.Position.X + ", " + ptrPt.Position.Y +
                 "\nPointer location (screen): " + screenPoint.X + ", " + screenPoint.Y;
             return details;
         }
    }
}
```

## <a name="related-articles"></a>Связанные разделы


**Примеры**
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
 

 




