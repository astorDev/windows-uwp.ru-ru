---
ms.assetid: EFCF84D0-2F4C-454D-97DA-249E9EAA806C
description: Класс SystemMediaTransportControls позволяет приложению использовать встроенные в Windows системные элементы управления транспортом мультимедиа и обновлять отображаемые элементом управления метаданные о мультимедиа, воспроизводимом приложением.
title: Ручное управление системными элементами управления воспроизведением мультимедиа
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 0a4163105b934f5c1e2970fab9f51b76d69d1bd8
ms.sourcegitcommit: c95915f8a13736705eab74951a12b2cf528ea612
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876224"
---
# <a name="manual-control-of-the-system-media-transport-controls"></a>Ручное управление системными элементами управления воспроизведением мультимедиа


Начиная c Windows 10 версии 1607, приложения UWP, использующие класс [**MediaPlayer**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer) для воспроизведения мультимедиа, по умолчанию автоматически интегрируются с системными элементами управления транспортировкой мультимедиа (SMTC). Это рекомендуемый способ взаимодействия с SMTC для большинства сценариев. Дополнительные сведения о настройке интеграции SMTC с **MediaPlayer** по умолчанию см. в разделе [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md).

Несколько ситуаций, в которых может потребоваться реализовать ручной элемент управления SMTC. К ним относятся те, в которых [**MediaTimelineController**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaTimelineController) используется для управления воспроизведением одного или нескольких проигрывателей мультимедиа. Если вы используете несколько проигрывателей мультимедиа и хотите иметь только один экземпляр SMTC для вашего приложения. Необходимо вручную управлять SMTC, если вы используете [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement) для воспроизведения мультимедиа.

## <a name="set-up-transport-controls"></a>Настройка элементов управления транспортом
Если вы используете **MediaPlayer** для воспроизведения мультимедиа, вы можете получить экземпляр класса [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls) путем получения доступа к свойству [**MediaPlayer.SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.systemmediatransportcontrols). Если вы собираетесь вручную управлять SMTC, отключите автоматическую интеграцию, обеспечиваемую **MediaPlayer**, задав свойству [**CommandManager.IsEnabled**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackcommandmanager.isenabled) значение false.

> [!NOTE] 
> Если вы отключите [**MediaPlaybackCommandManager**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackCommandManager) в объекте [**MediaPlayer**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlayer), установив для свойства [**IsEnabled**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackcommandmanager.isenabled) значение false, это нарушит связь между свойством [**TransportControls**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement.transportcontrols) объекта **MediaPlayer**, предоставляемым классом **MediaPlayerElement**, поэтому встроенные элементы управления транспортом больше не будут автоматически управлять воспроизведением мультимедиа. Вместо этого вам нужно будет реализовать собственные элементы управления для управления воспроизведением **MediaPlayer**.

[!code-cs[InitSMTCMediaPlayer](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetInitSMTCMediaPlayer)]

Также можно получить экземпляр класса [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls), вызвав метод [**GetForCurrentView**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.getforcurrentview). Необходимо получить объект с помощью этого метода, если вы используете **MediaElement** для воспроизведения мультимедиа.

[!code-cs[InitSMTCMediaElement](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetInitSMTCMediaElement)]

Включите кнопки, которые будут использоваться приложением, настроив соответствующее свойство "is enabled" объекта **SystemMediaTransportControls**, например [**IsPlayEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.isplayenabled), [**IsPauseEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.ispauseenabled), [**IsNextEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.isnextenabled) и [**IsPreviousEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.ispreviousenabled). Полный список доступных элементов управления см. в справочной документации для **SystemMediaTransportControls**.

[!code-cs[EnableContols](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetEnableContols)]

Зарегистрируйте обработчик события [**ButtonPressed**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.buttonpressed), чтобы получать уведомления, когда пользователь нажимает кнопку.

[!code-cs[RegisterButtonPressed](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetRegisterButtonPressed)]

## <a name="handle-system-media-transport-controls-button-presses"></a>Обработка нажатий кнопок системных элементов управления транспортом мультимедиа

При нажатии одной из включенных кнопок системные элементы управления транспортом создают событие [**ButtonPressed**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.buttonpressed). Свойство [**Button**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsbuttonpressedeventargs.button) объекта [**SystemMediaTransportControlsButtonPressedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControlsButtonPressedEventArgs), переданное в обработчик события, является участником [**SystemMediaTransportControlsButton**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControlsButton) перечисления, которое указывает, какие из включенных кнопок нажаты.

Чтобы можно было из обработчика событий [**ButtonPressed**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.buttonpressed) обновлять объекты в потоке пользовательского интерфейса, например объект [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement), необходимо маршалировать вызовы через объект [**CoreDispatcher**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreDispatcher). Это необходимо, из-за того что обработчик событий **ButtonPressed** не вызывается из потока пользовательского интерфейса. Поэтому при попытке непосредственного изменения пользовательского интерфейса возникнет исключение.

[!code-cs[SystemMediaTransportControlsButtonPressed](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsButtonPressed)]

## <a name="update-the-system-media-transport-controls-with-the-current-media-status"></a>Обновление системных элементов управления транспортом мультимедиа с помощью сведений о текущем состоянии мультимедиа

При изменении состояния мультимедиа необходимо уведомлять об этом класс [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls), чтобы система могла обновить элементы управления согласно текущему состоянию. Чтобы сделать это, задайте для свойства [**PlaybackStatus**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackstatus) соответствующее значение [**MediaPlaybackStatus**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaPlaybackStatus) из события [**CurrentStateChanged**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaelement.currentstatechanged) объекта [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement), возникшего при изменении состояния мультимедиа.

[!code-cs[SystemMediaTransportControlsStateChange](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsStateChange)]

## <a name="update-the-system-media-transport-controls-with-media-info-and-thumbnails"></a>Обновление системных элементов управления транспортом мультимедиа с помощью эскизов и сведений о мультимедиа

С помощью класса [**SystemMediaTransportControlsDisplayUpdater**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControlsDisplayUpdater) можно обновить сведения о мультимедиа, отображаемые элементами управления транспортом, например название композиции или обложку альбома для воспроизводимого элемента мультимедиа. Получите экземпляр этого класса с помощью свойства [**SystemMediaTransportControls.DisplayUpdater**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.displayupdater). Для обычных сценариев рекомендуем способ передачи метаданных, который заключается в вызове метода [**CopyFromFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.copyfromfileasync) и передачи в него воспроизводящегося файла мультимедиа. Модуль обновления отображения автоматически извлечет метаданные и изображение эскиза из файла.

Вызовите метод [**Update**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.update), чтобы заставить системные элементы управления транспортом мультимедиа обновить свой интерфейс, используя новые метаданные и эскиз.

[!code-cs[SystemMediaTransportControlsUpdater](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsUpdater)]

Если это необходимо для вашего сценария, можно вручную обновить метаданные, отображаемые системными элементами управления транспортом, настроив значения свойств [**MusicProperties**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.musicproperties), [**ImageProperties**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.imageproperties) или [**VideoProperties**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.videoproperties), предоставленных классом [**DisplayUpdater**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.displayupdater).

[!code-cs[SystemMediaTransportControlsUpdaterManual](./code/SMTCWin10/cs/MainPage.xaml.cs#SystemMediaTransportControlsUpdaterManual)]

> [!Note]
> Приложения должны задавать значение для [свойства системмедиатранспортконтролсдисплайупдатер. Type](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolsdisplayupdater.type#Windows_Media_SystemMediaTransportControlsDisplayUpdater_Type
) , даже если они не предоставляют другие метаданные носителя, отображаемые элементами управления транспортного носителя системы. Это значение помогает системе правильно обменять содержимое мультимедиа, включая предотвращение активации экранной заставки во время воспроизведения.


## <a name="update-the-system-media-transport-controls-timeline-properties"></a>Обновление свойств временной шкалы системных элементов управления транспортом мультимедиа

Системные элементы управления транспортом отображают информацию о временной шкале для воспроизводимого элемента мультимедиа, включая текущую позицию воспроизведения, время начала и время окончания элемента мультимедиа. Чтобы обновить свойства временной шкалы системных элементов управления транспортом, создайте объект [**SystemMediaTransportControlsTimelineProperties**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControlsTimelineProperties). Настройте свойства объекта, чтобы отразить текущее состояние воспроизводящегося элемента мультимедиа. Вызовите метод [**SystemMediaTransportControls.UpdateTimelineProperties**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.updatetimelineproperties), чтобы заставить элементы управления обновить временную шкалу.

[!code-cs[UpdateTimelineProperties](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetUpdateTimelineProperties)]

-   Необходимо указать значение для свойств [**StartTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.starttime), [**EndTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.endtime) и [**Position**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackpositionchangerequested), чтобы системные элементы управления отображали временную шкалу для воспроизводящегося элемента.

-   [**Минсиктиме**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.minseektime) и [**макссиктиме**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.maxseektime) позволяют указать диапазон в пределах временной шкалы, который пользователь может искать. Для этого можно использовать типичный сценарий, заключающийся в том, чтобы разрешить поставщикам содержимого включать рекламные паузы в их мультимедиа.

    Необходимо настроить свойства [**MinSeekTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.minseektime) и [**MaxSeekTime**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrolstimelineproperties.maxseektime), чтобы можно было создать событие [**PositionChangeRequest**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackpositionchangerequested).

-   Рекомендуется синхронизировать системные элементы управления с процессом воспроизведения мультимедиа. Для этого во время воспроизведения необходимо обновлять эти свойства приблизительно каждые 5 секунд, а также при изменении состояния воспроизведения, например при приостановке или при поиске новой позиции.

## <a name="respond-to-player-property-changes"></a>Реакция на изменение свойств проигрывателя

Существует набор свойств системных элементов управления транспортом, относящихся к текущему состоянию мультимедиапроигрывателя, а не к состоянию воспроизводимого элемента мультимедиа. Каждое из этих свойств сопоставлено событию, которое создается, когда пользователь изменяет связанный элемент управления. Эти свойства и события перечислены ниже.

| Свойство                                                                  | событие                                                                                                   |
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [**ауторепеатмоде**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.autorepeatmode) | [**ауторепеатмодечанжерекуестед**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.autorepeatmodechangerequested) |
| [**плайбаккрате**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackrate)     | [**плайбаккратечанжерекуестед**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackratechangerequested)     |
| [**шуффлинаблед**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.shuffleenabled) | [**шуффлинабледчанжерекуестед**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.shuffleenabledchangerequested) |

 
Чтобы можно было обрабатывать взаимодействие с пользователем с помощью одного из этих элементов управления, сначала зарегистрируйте обработчик для связанного события.

[!code-cs[RegisterPlaybackChangedHandler](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetRegisterPlaybackChangedHandler)]

Прежде всего убедитесь, что в обработчике события необходимое значение находится в допустимом и ожидаемом диапазоне. Если это так, настройте соответствующее свойство объекта [**MediaElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.MediaElement), а затем — соответствующее свойство объекта [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls).

[!code-cs[PlaybackChangedHandler](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetPlaybackChangedHandler)]

-   Чтобы возникло одно из этих событий свойств проигрывателя, необходимо настроить начальное значение для свойства. Например, событие [**PlaybackRateChangeRequested**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackratechangerequested) не будет создано до тех пор, пока вы хотя бы один раз не присвоите значение свойству [**PlaybackRate**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.playbackrate).

## <a name="use-the-system-media-transport-controls-for-background-audio"></a>Использование системных элементов управления транспортом мультимедиа для воспроизведения звука в фоновом режиме

Если вы не используете автоматическую интеграцию SMTC, обеспечиваемую **MediaPlayer**, необходимо вручную провести интеграцию с SMTC для включения звука в фоновом режиме. Как минимум, ваше приложение должно включить кнопки воспроизведения и приостановки, задав свойствам [**IsPlayEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.isplayenabled) и [**IsPauseEnabled**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.ispauseenabled) значение true. Кроме того, ваше приложение должно обрабатывать событие [**ButtonPressed**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.buttonpressed). Если ваше приложение не соответствует этим требованиям, воспроизведение звука будет прекращаться при переходе приложения в фоновый режим.

Приложения, которые используют новую модель с одним процессом для воспроизведения звука в фоновом режиме, должны получать экземпляр [**SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/Windows.Media.SystemMediaTransportControls) путем вызова [**GetForCurrentView**](https://docs.microsoft.com/uwp/api/windows.media.systemmediatransportcontrols.getforcurrentview). Приложения, использующие устаревшую модель с двумя процессами для фонового воспроизведения звука, должны использовать [**BackgroundMediaPlayer.Current.SystemMediaTransportControls**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.systemmediatransportcontrols) для получения доступа к SMTC из своего фонового процесса.

Дополнительные сведения о воспроизведении звука в фоновом режиме см. в разделе [Воспроизведение мультимедиа в фоновом режиме](background-audio.md).

## <a name="related-topics"></a>См. также
* [Воспроизведение мультимедиа](media-playback.md)
* [Интеграция с элементами управления транспортом системных носителей](integrate-with-systemmediatransportcontrols.md) 
* [Пример System Media транпорт](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/SystemMediaTransportControls) 

 




