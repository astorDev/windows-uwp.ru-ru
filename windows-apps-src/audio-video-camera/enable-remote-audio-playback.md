---
description: В этой статье показано, как использовать Аудиоплайбаккконнектион для включения удаленных устройств с подключением Bluetooth для воспроизведения звука на локальном компьютере.
title: Включение воспроизведения звука с устройств, подключенных удаленно по Bluetooth
ms.date: 05/03/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 3d4a4ab7664833308fe059e8bf07f68adea82b3e
ms.sourcegitcommit: cc645386b996f6e59f1ee27583dcd4310f8fb2a6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84262755"
---
# <a name="enable-audio-playback-from-remote-bluetooth-connected-devices"></a>Включение воспроизведения звука с устройств, подключенных удаленно по Bluetooth

В этой статье показано, как использовать [аудиоплайбаккконнектион](/uwp/api/windows.media.audio.audioplaybackconnection) для включения удаленных устройств с подключением Bluetooth для воспроизведения звука на локальном компьютере.

Начиная с Windows 10, удаленные источники звука версии 2004 могут выполнять потоковую передачу аудио на устройства Windows, что позволяет выполнять такие сценарии, как Настройка динамиков Bluetooth и предоставление пользователям возможности слышать звук с телефона. Реализация использует компоненты Bluetooth в операционной системе для обработки входящих звуковых данных и воспроизведения их на системных конечных точках системы, таких как встроенные динамики ПК или проводные наушники. Включение базового приемника A2DP Bluetooth осуществляется приложениями, которые отвечают за сценарий конечного пользователя, а не в системе.

Класс [аудиоплайбаккконнектион](/uwp/api/windows.media.audio.audioplaybackconnection) используется для включения и отключения подключений с удаленного устройства, а также для создания подключения, позволяя начинать воспроизведение звука удаленно.

## <a name="add-a-user-interface"></a>Добавление пользовательского интерфейса

В примерах из этой статьи мы будем использовать следующий простой пользовательский интерфейс XAML, который определяет элемент управления **ListView** для вывода доступных удаленных устройств, **TextBlock** для вывода состояния соединения и три кнопки для включения, отключения и открытия соединений.

:::code language="xml" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml" id="snippet_AudioPlaybackConnectionXAML":::

## <a name="use-devicewatcher-to-monitor-for-remote-devices"></a>Использование Девицеватчер для наблюдения за удаленными устройствами

Класс [девицеватчер](/uwp/api/windows.devices.enumeration.devicewatcher) позволяет обнаруживать подключенные устройства. Метод [аудиоплайбаккконнектион. жетдевицеселектор](/uwp/api/windows.media.audio.audioplaybackconnection.getdeviceselector) возвращает строку, которая сообщает наблюдателю устройства о том, какие типы устройств следует отслеживать. Передайте эту строку в конструктор **девицеватчер** . 

Событие [девицеватчер. added](/uwp/api/windows.devices.enumeration.devicewatcher.added) вызывается для каждого устройства, которое подключено при запуске наблюдателя устройства, а также для любого устройства, подключенного во время работы наблюдателя устройства. Событие [девицеватчер. removed](/uwp/api/windows.devices.enumeration.devicewatcher.removed) возникает при отключении ранее подключенного устройства. 

Вызовите [девицеватчер. Start](/uwp/api/windows.devices.enumeration.devicewatcher.start) , чтобы начать наблюдение за подключенными устройствами, поддерживающими подключения воспроизведения звука. В этом примере будет запущен диспетчер устройств при загрузке главного элемента управления **Grid** в пользовательском интерфейсе. Дополнительные сведения об использовании **девицеватчер**см. в разделе [Перечисление устройств](/windows/uwp/devices-sensors/enumerate-devices).

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_MainGridLoaded":::


В **добавленном** событии наблюдателя устройства каждое обнаруженное устройство представлено объектом [девицеинформатион](/uwp/api/Windows.Devices.Enumeration.DeviceInformation) . Добавьте каждое обнаруженное устройство в наблюдаемую коллекцию, привязанную к элементу управления **ListView** в пользовательском интерфейсе.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_DeclareDevices":::


:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_DeviceWatcher_Added":::


## <a name="enable-and-release-audio-playback-connections"></a>Включение и освобождение подключений воспроизведения звука

Перед открытием подключения к устройству необходимо включить подключение. Это информирует систему о том, что имеется новое приложение, которое хочет воспроизводить звук с удаленного устройства на компьютере, но воспроизведение звука не начинается до тех пор, пока не будет открыто подключение, которое показано на следующем шаге.

В обработчике щелчка кнопки **включить подключение к воспроизведению звука** получите идентификатор устройства, связанный с текущим выбранным устройством в элементе управления **ListView** . В этом примере сохраняется словарь включенных объектов **аудиоплайбаккконнектион** . Этот метод сначала проверяет, существует ли уже запись в словаре для выбранного устройства. Затем метод пытается создать **аудиоплайбаккконнектион** для выбранного устройства путем вызова [трикреатефромид](/uwp/api/windows.media.audio.audioplaybackconnection.trycreatefromid) и передачи выбранного идентификатора устройства. 

Если соединение создано успешно, добавьте новый объект **аудиоплайбаккконнектион** в словарь приложения, зарегистрируйте обработчик для события [StateChanged](/uwp/api/windows.media.audio.audioplaybackconnection.statechanged) объекта и вызовите[стартасинк](/uwp/api/windows.media.audio.audioplaybackconnection.startasync) , чтобы уведомить систему о том, что новое соединение включено. 

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_DeclareConnections":::

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_EnableAudioPlaybackConnection":::


## <a name="open-the-audio-playback-connection"></a>Открытие подключения к воспроизведению звука

На предыдущем шаге было создано подключение к воспроизведению звука, но воспроизведение звука не началось до тех пор, пока соединение не будет открыто путем вызова [Open](/uwp/api/windows.media.audio.audioplaybackconnection.open) или [OpenAsync](/uwp/api/windows.media.audio.audioplaybackconnection.openasync). В окне кнопка **подключения для воспроизведения звука** щелкните обработчик, получите текущее выбранное устройство и используйте идентификатор для получения **аудиоплайбаккконнектион** из словаря подключений приложения. Ожидайте вызов **OpenAsync** и проверьте значение **состояния** возвращенного объекта [аудиоплайбаккконнектионопенресултстатус](/uwp/api/windows.media.audio.audioplaybackconnectionopenresult) , чтобы узнать, было ли соединение успешно открыто, и, если да, обновите текстовое поле Состояние подключения.


:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_OpenAudioPlaybackConnectionButton":::

## <a name="monitor-audio-playback-connection-state"></a>Мониторинг состояния подключения для воспроизведения звука

Событие [аудиоплайбаккконнектион. коннектионстатечанжед](/uwp/api/windows.media.audio.audioplaybackconnection.statechanged) возникает при изменении состояния соединения. В этом примере обработчик для этого события обновляет текстовое поле Status (состояние). Не забудьте обновить пользовательский интерфейс внутри вызова [Dispatcher. RunAsync](/uwp/api/windows.ui.core.coredispatcher.runasync) , чтобы убедиться, что обновление выполняется в ПОТОКЕ пользовательского интерфейса.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_ConnectionStateChanged":::

## <a name="release-connections-and-handle-removed-devices"></a>Выпуски подключений и обработку удаленных устройств

В этом примере показана кнопка **выпуска для воспроизведения звука** , позволяющая пользователю освободить подключение к воспроизведению звука. В обработчике этого события мы получаем текущее выбранное устройство и используем идентификатор устройства для поиска **аудиоплайбаккконнектион** в словаре. Вызовите **Dispose** , чтобы освободить ссылку и освободить все связанные ресурсы, и удалите подключение из словаря.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_ReleaseAudioPlaybackConnectionButton":::

Следует обрабатывайте случай, когда устройство удаляется при включенном или открытом соединении. Для этого Реализуйте обработчик для события [девицеватчер. removes](/uwp/api/windows.devices.enumeration.devicewatcher.removed) наблюдателя устройства. Во-первых, идентификатор удаленного устройства используется для удаления устройства из наблюдаемой коллекции, привязанной к элементу управления **ListView** приложения. Затем, если подключение, связанное с этим устройством, находится в словаре приложения, вызывается метод **Dispose** для высвобождения связанных ресурсов, а затем подключение удаляется из словаря. Все это выполняется в вызове **Dispatcher. RunAsync** , чтобы убедиться, что обновления пользовательского интерфейса выполняются в ПОТОКЕ пользовательского интерфейса.

:::code language="csharp" source="~/../snippets-windows/windows-uwp/audio-video-camera/AudioPlaybackConnectionExample/cs/MainPage.xaml.cs" id="snippet_DeviceWatcher_Removed":::

## <a name="related-topics"></a>См. также

[Воспроизведение мультимедиа](media-playback.md)


 




