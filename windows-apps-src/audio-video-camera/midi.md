---
ms.assetid: 9146212C-8480-4C16-B74C-D7F08C7086AF
description: В этой статье показано, как перечислять MIDI-устройства (Musical Instrument Digital Interface), а также отправлять и получать сообщения MIDI из универсального приложения для Windows.
title: MIDI
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 73806735401f53a73b1051f37c72119b45b574be
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67318293"
---
# <a name="midi"></a>MIDI



В этой статье показано, как перечислять MIDI-устройства (Musical Instrument Digital Interface), а также отправлять и получать сообщения MIDI из универсального приложения для Windows. Windows 10 поддерживает MIDI по USB (класс совместимым и наиболее собственные драйверы), MIDI по Bluetooth с НИЗКИМ (Юбилейное обновление Windows 10 и более поздних версий) и с помощью свободно доступных продуктов независимых производителей, MIDI через Ethernet и перенаправленные MIDI.

## <a name="enumerate-midi-devices"></a>Перечисление MIDI-устройств

Перед перечислением и использованием MIDI-устройств добавьте в свой проект следующие пространства имен.

[!code-cs[Using](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetUsing)]

Добавьте на свою XAML-страницу элемент управления [**ListBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListBox), который позволит пользователю выбрать одно из подключенных к системе устройств ввода MIDI. Добавьте другой элемент управления для списка устройств вывода MIDI.

[!code-xml[MidiListBoxes](./code/MIDIWin10/cs/MainPage.xaml#SnippetMidiListBoxes)]

Класс [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation) метода [**FindAllAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync) используется для перечисления различных типов устройств, обнаруженных Windows. Чтобы указать, что вам требуется только метод для поиска устройств ввода MIDI, используйте строку селектора, возвращенную [**MidiInPort.GetDeviceSelector**](https://docs.microsoft.com/uwp/api/windows.devices.midi.midiinport.getdeviceselector). **FindAllAsync** возвращает коллекцию [**DeviceInformationCollection**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationCollection), которая содержит **DeviceInformation** для всех зарегистрированных в системе устройств ввода MIDI. Если возвращаемая коллекция не содержит ни одного элемента, то доступные устройства ввода MIDI отсутствуют. Если в коллекции есть элементы, создайте цикл по объектам **DeviceInformation** и добавьте имя каждого устройства в список **ListBox** для устройств ввода MIDI.

[!code-cs[EnumerateMidiInputDevices](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetEnumerateMidiInputDevices)]

Перечисление устройств вывода MIDI работает аналогичным образом, за исключением того, что вам следует указать строку селектора, возвращенную [**MidiOutPort.GetDeviceSelector**](https://docs.microsoft.com/uwp/api/windows.devices.midi.midioutport.getdeviceselector) при вызове **FindAllAsync**.

[!code-cs[EnumerateMidiOutputDevices](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetEnumerateMidiOutputDevices)]



## <a name="create-a-device-watcher-helper-class"></a>Создание вспомогательного класса для наблюдателя устройств

Пространство имен [**Windows.Devices.Enumeration**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration) предоставляет объект [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher), который может уведомлять ваше приложение о добавлении устройства в систему или об удалении его из системы, либо об обновлении информации устройства. Поскольку приложениям с поддержкой MIDI обычно требуются как устройства ввода, так и устройства вывода, в этом примере создается вспомогательный класс, реализующий шаблон **DeviceWatcher**, а для устройств ввода и вывода MIDI можно использовать один и тот же код, не прибегая к дублированию.

Добавьте в свой проект новый класс, выступающий в качестве наблюдателя устройств. В данном примере он называется **MyMidiDeviceWatcher**. Остальная часть кода в этом разделе используется для реализации вспомогательного класса.

Добавьте в класс несколько переменных-членов:

-   Объект [**DeviceWatcher**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceWatcher), который будет отслеживать изменения устройств.
-   Строку селектора устройства, которая будет содержать строку селектора входного MIDI-порта для одного экземпляра и строку селектора выходного MIDI-порта для другого экземпляра.
-   Элемент управления [**ListBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListBox), который будет заполнен именами доступных устройств.
-   [  **CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher), необходимый для обновления пользовательского интерфейса из потока, отличного от потока пользовательского интерфейса.

[!code-cs[WatcherVariables](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherVariables)]

Добавьте свойство [**DeviceInformationCollection**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformationCollection), которое используется для получения доступа к текущему списку устройств из внешнего вспомогательного класса.

[!code-cs[DeclareDeviceInformationCollection](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetDeclareDeviceInformationCollection)]

В конструкторе класса вызывающая сторона передает строку селектора MIDI-устройства, объект **ListBox** со списком устройств и объект **Dispatcher**, необходимый для обновления пользовательского интерфейса.

Вызовите [**DeviceInformation.CreateWatcher**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.createwatcher), чтобы создать новый экземпляр класса **DeviceWatcher**, передав строку селектора MIDI-устройства.

Зарегистрируйте обработчики для обработчиков событий наблюдателя.

[!code-cs[WatcherConstructor](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherConstructor)]

**DeviceWatcher** содержит следующие события.

-   [**Добавлен** ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.added) -возникает при добавлении нового устройства в системе.
-   [**Удалить** ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.removed) — вызывается, когда устройство будет удалено из системы.
-   [**Обновить** ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.updated) -возникает при обновлении данные, связанные с существующее устройство.
-   [**EnumerationCompleted** ](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.enumerationcompleted) -возникает после завершения его перечисления этого типа запрошенном устройстве наблюдатель.

В обработчике событий для каждого из этих событий вызывается вспомогательный метод **UpdateDevices**, который обновляет **ListBox** с помощью текущего списка устройств. Поскольку **UpdateDevices** обновляет элементы пользовательского интерфейса, а эти обработчики событий не вызываются в потоке пользовательского интерфейса, каждый вызов должен быть заключен в вызов [**RunAsync**](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.runasync), который запускает указанный код в потоке пользовательского интерфейса.

[!code-cs[WatcherEventHandlers](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherEventHandlers)]

Вспомогательный метод **UpdateDevices** вызывает [**DeviceInformation.FindAllAsync**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.findallasync) и обновляет **ListBox** с помощью имен возвращенных устройств, как это было описано ранее в этой статье.

[!code-cs[WatcherUpdateDevices](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherUpdateDevices)]

Добавьте методы для запуска наблюдателя с помощью метода [**Start**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.start) объекта **DeviceWatcher** и для остановки наблюдателя с помощью метода [**Stop**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.stop).

[!code-cs[WatcherStopStart](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherStopStart)]

Предоставьте деструктор, чтобы отменить регистрацию обработчиков событий для наблюдателя, и установите для наблюдателя устройств значение NULL.

[!code-cs[WatcherDestructor](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherDestructor)]

## <a name="create-midi-ports-to-send-and-receive-messages"></a>Создание портов MIDI для отправки и получения сообщений

В коде программной части для своей страницы объявите переменные-члены для хранения двух экземпляров вспомогательного класса **MyMidiDeviceWatcher**: один для устройств ввода, а другой для устройств вывода.

[!code-cs[DeclareDeviceWatchers](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetDeclareDeviceWatchers)]

Создайте экземпляр вспомогательных классов для наблюдателя, передав строку селектора устройств, чтобы заполнить список **ListBox**, и объект **CoreDispatcher**, который может быть получен через свойство **Dispatcher** этой страницы. Затем вызовите этот метод, чтобы запустить **DeviceWatcher** каждого объекта.

Вскоре после запуска каждого объекта **DeviceWatcher** он завершит перечисление устройств, которые в настоящий момент подключены к системе, и вызовет его событие [**EnumerationCompleted**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher.enumerationcompleted), которое обновит список текущих устройств MIDI в каждом объекте **ListBox**.

[!code-cs[StartWatchers](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetStartWatchers)]

Когда пользователь выбирает элемент из списка **ListBox** входных устройств MIDI, возникает событие [**SelectionChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged). В обработчике для этого события обратитесь к свойству **DeviceInformationCollection** вспомогательного класса, чтобы получить текущий список устройств. Если в списке есть записи, выберите объект **DeviceInformation** с помощью индекса, соответствующего [**SelectedIndex**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex) элемента управления **ListBox**.

Создайте объект [**MidiInPort**](https://docs.microsoft.com/uwp/api/Windows.Devices.Midi.MidiInPort), представляющий выбранное устройство ввода, вызвав [**MidiInPort.FromIdAsync**](https://docs.microsoft.com/uwp/api/windows.devices.midi.midiinport.fromidasync) и передав свойство [**Id**](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.deviceinformation.id) выбранного устройства.

Зарегистрируйте обработчик для события [**MessageReceived**](https://docs.microsoft.com/uwp/api/windows.devices.midi.midiinport.messagereceived), которое вызывается каждый раз при получении MIDI-сообщения через указанное устройство.

[!code-cs[DeclareMidiPorts](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetDeclareMidiPorts)]

[!code-cs[InPortSelectionChanged](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetInPortSelectionChanged)]

Когда вызывается обработчик **MessageReceived**, это сообщение содержится в свойстве [**Message**](https://docs.microsoft.com/uwp/api/Windows.Devices.Midi.MidiMessageReceivedEventArgs) для **MidiMessageReceivedEventArgs**. [  **Type**](https://docs.microsoft.com/uwp/api/windows.devices.midi.imidimessage.type) объекта сообщения — это значение из перечисления [**MidiMessageType**](https://docs.microsoft.com/uwp/api/Windows.Devices.Midi.MidiMessageType), указывающее тип полученного сообщения. Данные сообщения зависят от его типа. Этот пример выполняет проверку, определяющую, является ли данное сообщение инициирующим, и если это так, выводит MIDI-канал, примечание и скорость сообщения.

[!code-cs[MessageReceived](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetMessageReceived)]

Обработчик [**SelectionChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectionchanged) для элемента управления **ListBox** выходного устройства работает аналогично обработчику для входного устройства, только в нем нет зарегистрированных обработчиков событий.

[!code-cs[OutPortSelectionChanged](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetOutPortSelectionChanged)]

После создания выходного устройства вы можете отправить сообщение, создав [**IMidiMessage**](https://docs.microsoft.com/uwp/api/Windows.Devices.Midi.IMidiMessage) для желаемого типа сообщения. В этом примере сообщение — это [**NoteOnMessage**](https://docs.microsoft.com/uwp/api/Windows.Devices.Midi.MidiNoteOnMessage). Для отправки этого сообщения вызывается метод [**SendMessage**](https://docs.microsoft.com/uwp/api/windows.devices.midi.imidioutport.sendmessage) объекта [**IMidiOutPort**](https://docs.microsoft.com/uwp/api/Windows.Devices.Midi.IMidiOutPort).

[!code-cs[SendMessage](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetSendMessage)]

Не забудьте очистить ресурсы приложения после его деактивации. Отмените регистрацию обработчиков событий и установите значение NULL для объектов входного и выходного MIDI-порта. Остановите наблюдатели устройств и установите их в значение NULL.

[!code-cs[CleanUp](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetCleanUp)]

## <a name="using-the-built-in-windows-general-midi-synth"></a>Использование встроенного в Windows синтезатора General MIDI

При перечислении устройств вывода MIDI, используя описанный выше способ, ваше приложение обнаружит MIDI-устройство «Microsoft GS Wavetable Synth». Это встроенный синтезатор General MIDI, который можно использовать в приложении. Однако если попытаться создать выходной порт MIDI для этого устройства, возникнет ошибка, если вы не включили расширение SDK для встроенного синтезатора в свой проект.

**Чтобы включить расширение общие SDK MIDI-синтезатор в проекте приложения**

1.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку...** .
2.  Разверните узел **Универсальное приложение для Windows**.
3.  Выберите **Расширения**.
4.  В списке расширений выберите **Microsoft General MIDI DLS для универсальных приложений для Windows**.
    > [!NOTE] 
    > Если доступно несколько версий расширения, выберите ту из них, которая соответствует целевой версии SDK вашего приложения. Вы можете узнать, для какой версии SDK предназначено приложение на вкладке **Приложение** свойств проекта.

 

 




