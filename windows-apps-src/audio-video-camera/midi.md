---
author: drewbatgit
ms.assetid: 9146212C-8480-4C16-B74C-D7F08C7086AF
description: В этой статье показано, как перечислять MIDI-устройства (Musical Instrument Digital Interface), а также отправлять и получать сообщения MIDI из универсального приложения для Windows.
title: MIDI
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 36d1e4afd620b871d4273699aea5c02cc9faec80
ms.sourcegitcommit: 3257416aebb5a7b1515e107866806f8bd57845a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2018
ms.locfileid: "7158077"
---
# <a name="midi"></a>MIDI



В этой статье показано, как перечислять MIDI-устройства (Musical Instrument Digital Interface), а также отправлять и получать сообщения MIDI из универсального приложения для Windows. Windows 10 поддерживает MIDI по USB (класс со стандартами и наиболее принадлежит драйверов), MIDI через Bluetooth с низким ЭНЕРГОПОТРЕБЛЕНИЕМ (Windows 10 Anniversary Edition и более поздние версии) и через свободно доступных продуктов сторонних поставщиков, MIDI через Ethernet и перенаправленных MIDI.

## <a name="enumerate-midi-devices"></a>Перечисление MIDI-устройств

Перед перечислением и использованием MIDI-устройств добавьте в свой проект следующие пространства имен.

[!code-cs[Using](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetUsing)]

Добавьте на свою XAML-страницу элемент управления [**ListBox**](https://msdn.microsoft.com/library/windows/apps/br242868), который позволит пользователю выбрать одно из подключенных к системе устройств ввода MIDI. Добавьте другой элемент управления для списка устройств вывода MIDI.

[!code-xml[MidiListBoxes](./code/MIDIWin10/cs/MainPage.xaml#SnippetMidiListBoxes)]

Класс [**DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/br225393) метода [**FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/br225432) используется для перечисления различных типов устройств, обнаруженных Windows. Чтобы указать, что вам требуется только метод для поиска устройств ввода MIDI, используйте строку селектора, возвращенную [**MidiInPort.GetDeviceSelector**](https://msdn.microsoft.com/library/windows/apps/dn894779). **FindAllAsync** возвращает коллекцию [**DeviceInformationCollection**](https://msdn.microsoft.com/library/windows/apps/br225395), которая содержит **DeviceInformation** для всех зарегистрированных в системе устройств ввода MIDI. Если возвращаемая коллекция не содержит ни одного элемента, то доступные устройства ввода MIDI отсутствуют. Если в коллекции есть элементы, создайте цикл по объектам **DeviceInformation** и добавьте имя каждого устройства в список **ListBox** для устройств ввода MIDI.

[!code-cs[EnumerateMidiInputDevices](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetEnumerateMidiInputDevices)]

Перечисление устройств вывода MIDI работает аналогичным образом, за исключением того, что вам следует указать строку селектора, возвращенную [**MidiOutPort.GetDeviceSelector**](https://msdn.microsoft.com/library/windows/apps/dn894845) при вызове **FindAllAsync**.

[!code-cs[EnumerateMidiOutputDevices](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetEnumerateMidiOutputDevices)]



## <a name="create-a-device-watcher-helper-class"></a>Создание вспомогательного класса для наблюдателя устройств

Пространство имен [**Windows.Devices.Enumeration**](https://msdn.microsoft.com/library/windows/apps/br225459) предоставляет объект [**DeviceWatcher**](https://msdn.microsoft.com/library/windows/apps/br225446), который может уведомлять ваше приложение о добавлении устройства в систему или об удалении его из системы, либо об обновлении информации устройства. Поскольку приложениям с поддержкой MIDI обычно требуются как устройства ввода, так и устройства вывода, в этом примере создается вспомогательный класс, реализующий шаблон **DeviceWatcher**, а для устройств ввода и вывода MIDI можно использовать один и тот же код, не прибегая к дублированию.

Добавьте в свой проект новый класс, выступающий в качестве наблюдателя устройств. В данном примере он называется **MyMidiDeviceWatcher**. Остальная часть кода в этом разделе используется для реализации вспомогательного класса.

Добавьте в класс несколько переменных-членов:

-   Объект [**DeviceWatcher**](https://msdn.microsoft.com/library/windows/apps/br225446), который будет отслеживать изменения устройств.
-   Строку селектора устройства, которая будет содержать строку селектора входного MIDI-порта для одного экземпляра и строку селектора выходного MIDI-порта для другого экземпляра.
-   Элемент управления [**ListBox**](https://msdn.microsoft.com/library/windows/apps/br242868), который будет заполнен именами доступных устройств.
-   [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211), необходимый для обновления пользовательского интерфейса из потока, отличного от потока пользовательского интерфейса.

[!code-cs[WatcherVariables](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherVariables)]

Добавьте свойство [**DeviceInformationCollection**](https://msdn.microsoft.com/library/windows/apps/br225395), которое используется для получения доступа к текущему списку устройств из внешнего вспомогательного класса.

[!code-cs[DeclareDeviceInformationCollection](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetDeclareDeviceInformationCollection)]

В конструкторе класса вызывающая сторона передает строку селектора MIDI-устройства, объект **ListBox** со списком устройств и объект **Dispatcher**, необходимый для обновления пользовательского интерфейса.

Вызовите [**DeviceInformation.CreateWatcher**](https://msdn.microsoft.com/library/windows/apps/br225427), чтобы создать новый экземпляр класса **DeviceWatcher**, передав строку селектора MIDI-устройства.

Зарегистрируйте обработчики для обработчиков событий наблюдателя.

[!code-cs[WatcherConstructor](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherConstructor)]

**DeviceWatcher** содержит следующие события.

-   [**Added**](https://msdn.microsoft.com/library/windows/apps/br225450). Создается при добавлении в систему нового устройства.
-   [**Removed**](https://msdn.microsoft.com/library/windows/apps/br225453). Создается при удалении устройства из системы.
-   [**Updated**](https://msdn.microsoft.com/library/windows/apps/br225458). Создается при обновлении информации, связанной с существующим устройством.
-   [**EnumerationCompleted**](https://msdn.microsoft.com/library/windows/apps/br225451). Создается, когда наблюдатель завершил перечисление типов запрошенных устройств.

В обработчике событий для каждого из этих событий вызывается вспомогательный метод **UpdateDevices**, который обновляет **ListBox** с помощью текущего списка устройств. Поскольку **UpdateDevices** обновляет элементы пользовательского интерфейса, а эти обработчики событий не вызываются в потоке пользовательского интерфейса, каждый вызов должен быть заключен в вызов [**RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317), который запускает указанный код в потоке пользовательского интерфейса.

[!code-cs[WatcherEventHandlers](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherEventHandlers)]

Вспомогательный метод **UpdateDevices** вызывает [**DeviceInformation.FindAllAsync**](https://msdn.microsoft.com/library/windows/apps/br225432) и обновляет **ListBox** с помощью имен возвращенных устройств, как это было описано ранее в этой статье.

[!code-cs[WatcherUpdateDevices](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherUpdateDevices)]

Добавьте методы для запуска наблюдателя с помощью метода [**Start**](https://msdn.microsoft.com/library/windows/apps/br225454) объекта **DeviceWatcher** и для остановки наблюдателя с помощью метода [**Stop**](https://msdn.microsoft.com/library/windows/apps/br225456).

[!code-cs[WatcherStopStart](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherStopStart)]

Предоставьте деструктор, чтобы отменить регистрацию обработчиков событий для наблюдателя, и установите для наблюдателя устройств значение NULL.

[!code-cs[WatcherDestructor](./code/MIDIWin10/cs/MyMidiDeviceWatcher.cs#SnippetWatcherDestructor)]

## <a name="create-midi-ports-to-send-and-receive-messages"></a>Создание портов MIDI для отправки и получения сообщений

В коде программной части для своей страницы объявите переменные-члены для хранения двух экземпляров вспомогательного класса **MyMidiDeviceWatcher**: один для устройств ввода, а другой для устройств вывода.

[!code-cs[DeclareDeviceWatchers](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetDeclareDeviceWatchers)]

Создайте экземпляр вспомогательных классов для наблюдателя, передав строку селектора устройств, чтобы заполнить список **ListBox**, и объект **CoreDispatcher**, который может быть получен через свойство **Dispatcher** этой страницы. Затем вызовите этот метод, чтобы запустить **DeviceWatcher** каждого объекта.

Вскоре после запуска каждого объекта **DeviceWatcher** он завершит перечисление устройств, которые в настоящий момент подключены к системе, и вызовет его событие [**EnumerationCompleted**](https://msdn.microsoft.com/library/windows/apps/br225451), которое обновит список текущих устройств MIDI в каждом объекте **ListBox**.

[!code-cs[StartWatchers](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetStartWatchers)]

Когда пользователь выбирает элемент из списка **ListBox** входных устройств MIDI, возникает событие [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/br209776). В обработчике для этого события обратитесь к свойству **DeviceInformationCollection** вспомогательного класса, чтобы получить текущий список устройств. Если в списке есть записи, выберите объект **DeviceInformation** с помощью индекса, соответствующего [**SelectedIndex**](https://msdn.microsoft.com/library/windows/apps/br209768) элемента управления **ListBox**.

Создайте объект [**MidiInPort**](https://msdn.microsoft.com/library/windows/apps/dn894770), представляющий выбранное устройство ввода, вызвав [**MidiInPort.FromIdAsync**](https://msdn.microsoft.com/library/windows/apps/dn894776) и передав свойство [**Id**](https://msdn.microsoft.com/library/windows/apps/br225437) выбранного устройства.

Зарегистрируйте обработчик для события [**MessageReceived**](https://msdn.microsoft.com/library/windows/apps/dn894781), которое вызывается каждый раз при получении MIDI-сообщения через указанное устройство.

[!code-cs[DeclareMidiPorts](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetDeclareMidiPorts)]

[!code-cs[InPortSelectionChanged](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetInPortSelectionChanged)]

Когда вызывается обработчик **MessageReceived**, это сообщение содержится в свойстве [**Message**](https://msdn.microsoft.com/library/windows/apps/dn894783) для **MidiMessageReceivedEventArgs**. [**Type**](https://msdn.microsoft.com/library/windows/apps/dn894726) объекта сообщения — это значение из перечисления [**MidiMessageType**](https://msdn.microsoft.com/library/windows/apps/dn894786), указывающее тип полученного сообщения. Данные сообщения зависят от его типа. Этот пример выполняет проверку, определяющую, является ли данное сообщение инициирующим, и если это так, выводит MIDI-канал, примечание и скорость сообщения.

[!code-cs[MessageReceived](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetMessageReceived)]

Обработчик [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/br209776) для элемента управления **ListBox** выходного устройства работает аналогично обработчику для входного устройства, только в нем нет зарегистрированных обработчиков событий.

[!code-cs[OutPortSelectionChanged](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetOutPortSelectionChanged)]

После создания выходного устройства вы можете отправить сообщение, создав [**IMidiMessage**](https://msdn.microsoft.com/library/windows/apps/dn911508) для желаемого типа сообщения. В этом примере сообщение — это [**NoteOnMessage**](https://msdn.microsoft.com/library/windows/apps/dn894817). Для отправки этого сообщения вызывается метод [**SendMessage**](https://msdn.microsoft.com/library/windows/apps/dn894730) объекта [**IMidiOutPort**](https://msdn.microsoft.com/library/windows/apps/dn894727).

[!code-cs[SendMessage](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetSendMessage)]

Не забудьте очистить ресурсы приложения после его деактивации. Отмените регистрацию обработчиков событий и установите значение NULL для объектов входного и выходного MIDI-порта. Остановите наблюдатели устройств и установите их в значение NULL.

[!code-cs[CleanUp](./code/MIDIWin10/cs/MainPage.xaml.cs#SnippetCleanUp)]

## <a name="using-the-built-in-windows-general-midi-synth"></a>Использование встроенного в Windows синтезатора General MIDI

При перечислении устройств вывода MIDI, используя описанный выше способ, ваше приложение обнаружит MIDI-устройство «Microsoft GS Wavetable Synth». Это встроенный синтезатор General MIDI, который можно использовать в приложении. Однако если попытаться создать выходной порт MIDI для этого устройства, возникнет ошибка, если вы не включили расширение SDK для встроенного синтезатора в свой проект.

**Включение расширения SDK синтезатора General MIDI в проект приложения**

1.  В **обозревателе решений** щелкните правой кнопкой мыши **Ссылки** и выберите **Добавить ссылку...**.
2.  Разверните узел **Универсальное приложение для Windows**.
3.  Выберите **Расширения**.
4.  В списке расширений выберите **Microsoft General MIDI DLS для универсальных приложений для Windows**.
    > [!NOTE] 
    > Если доступно несколько версий расширения, выберите ту из них, которая соответствует целевой версии SDK вашего приложения. Вы можете узнать, для какой версии SDK предназначено приложение на вкладке **Приложение** свойств проекта.

 

 




