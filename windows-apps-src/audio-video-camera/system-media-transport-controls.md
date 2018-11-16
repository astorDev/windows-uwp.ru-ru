---
author: drewbatgit
ms.assetid: EFCF84D0-2F4C-454D-97DA-249E9EAA806C
description: Класс SystemMediaTransportControls позволяет приложению использовать встроенные в Windows системные элементы управления транспортом мультимедиа и обновлять отображаемые элементом управления метаданные о мультимедиа, воспроизводимом приложением.
title: Ручное управление системными элементами управления транспортировкой мультимедиа
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b0ece9a25a2fd2892553d66847c39637e7faae70
ms.sourcegitcommit: e2fca6c79f31e521ba76f7ecf343cf8f278e6a15
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2018
ms.locfileid: "6973936"
---
# <a name="manual-control-of-the-system-media-transport-controls"></a>Ручное управление системными элементами управления транспортировкой мультимедиа


Начиная c Windows 10 версии 1607, приложения UWP, использующие класс [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer) для воспроизведения мультимедиа, по умолчанию автоматически интегрируются с системными элементами управления транспортировкой мультимедиа (SMTC). Это рекомендуемый способ взаимодействия с SMTC для большинства сценариев. Дополнительные сведения о настройке интеграции SMTC с **MediaPlayer** по умолчанию см. в разделе [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md).

Несколько ситуаций, в которых может потребоваться реализовать ручной элемент управления SMTC. К ним относятся те, в которых [**MediaTimelineController**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.MediaTimelineController) используется для управления воспроизведением одного или нескольких проигрывателей мультимедиа. Если вы используете несколько проигрывателей мультимедиа и хотите иметь только один экземпляр SMTC для вашего приложения. Необходимо вручную управлять SMTC, если вы используете [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaElement) для воспроизведения мультимедиа.

## <a name="set-up-transport-controls"></a>Настройка элементов управления транспортом
Если вы используете **MediaPlayer** для воспроизведения мультимедиа, вы можете получить экземпляр класса [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.SystemMediaTransportControls) путем получения доступа к свойству [**MediaPlayer.SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.SystemMediaTransportControls). Если вы собираетесь вручную управлять SMTC, отключите автоматическую интеграцию, обеспечиваемую **MediaPlayer**, задав свойству [**CommandManager.IsEnabled**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.IsEnabled) значение false.

> [!NOTE] 
> Если вы отключите [**MediaPlaybackCommandManager**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager) в объекте [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer), установив для свойства [**IsEnabled**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackCommandManager.IsEnabled) значение false, это нарушит связь между свойством [**TransportControls**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement.TransportControls) объекта **MediaPlayer**, предоставляемым классом **MediaPlayerElement**, поэтому встроенные элементы управления транспортом больше не будут автоматически управлять воспроизведением мультимедиа. Вместо этого вам нужно будет реализовать собственные элементы управления для управления воспроизведением **MediaPlayer**.

[!code-cs[InitSMTCMediaPlayer](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetInitSMTCMediaPlayer)]

Также можно получить экземпляр класса [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677), вызвав метод [**GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/dn278708). Необходимо получить объект с помощью этого метода, если вы используете **MediaElement** для воспроизведения мультимедиа.

[!code-cs[InitSMTCMediaElement](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetInitSMTCMediaElement)]

Включите кнопки, которые будут использоваться приложением, настроив соответствующее свойство "is enabled" объекта **SystemMediaTransportControls**, например [**IsPlayEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278714), [**IsPauseEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278713), [**IsNextEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278712) и [**IsPreviousEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278715). Полный список доступных элементов управления см. в справочной документации для **SystemMediaTransportControls**.

[!code-cs[EnableContols](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetEnableContols)]

Зарегистрируйте обработчик события [**ButtonPressed**](https://msdn.microsoft.com/library/windows/apps/dn278706), чтобы получать уведомления, когда пользователь нажимает кнопку.

[!code-cs[RegisterButtonPressed](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetRegisterButtonPressed)]

## <a name="handle-system-media-transport-controls-button-presses"></a>Обработка нажатий кнопок системных элементов управления транспортом мультимедиа

При нажатии одной из включенных кнопок системные элементы управления транспортом создают событие [**ButtonPressed**](https://msdn.microsoft.com/library/windows/apps/dn278706). Свойство [**Button**](https://msdn.microsoft.com/library/windows/apps/dn278685) объекта [**SystemMediaTransportControlsButtonPressedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn278683), переданное в обработчик события, является участником [**SystemMediaTransportControlsButton**](https://msdn.microsoft.com/library/windows/apps/dn278681) перечисления, которое указывает, какие из включенных кнопок нажаты.

Чтобы можно было из обработчика событий [**ButtonPressed**](https://msdn.microsoft.com/library/windows/apps/dn278706) обновлять объекты в потоке пользовательского интерфейса, например объект [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), необходимо маршалировать вызовы через объект [**CoreDispatcher**](https://msdn.microsoft.com/library/windows/apps/br208211). Это необходимо, из-за того что обработчик событий **ButtonPressed** не вызывается из потока пользовательского интерфейса. Поэтому при попытке непосредственного изменения пользовательского интерфейса возникнет исключение.

[!code-cs[SystemMediaTransportControlsButtonPressed](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsButtonPressed)]

## <a name="update-the-system-media-transport-controls-with-the-current-media-status"></a>Обновление системных элементов управления транспортом мультимедиа с помощью сведений о текущем состоянии мультимедиа

При изменении состояния мультимедиа необходимо уведомлять об этом класс [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677), чтобы система могла обновить элементы управления согласно текущему состоянию. Чтобы сделать это, задайте для свойства [**PlaybackStatus**](https://msdn.microsoft.com/library/windows/apps/dn278719) соответствующее значение [**MediaPlaybackStatus**](https://msdn.microsoft.com/library/windows/apps/dn278665) из события [**CurrentStateChanged**](https://msdn.microsoft.com/library/windows/apps/br227375) объекта [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), возникшего при изменении состояния мультимедиа.

[!code-cs[SystemMediaTransportControlsStateChange](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsStateChange)]

## <a name="update-the-system-media-transport-controls-with-media-info-and-thumbnails"></a>Обновление системных элементов управления транспортом мультимедиа с помощью эскизов и сведений о мультимедиа

С помощью класса [**SystemMediaTransportControlsDisplayUpdater**](https://msdn.microsoft.com/library/windows/apps/dn278686) можно обновить сведения о мультимедиа, отображаемые элементами управления транспортом, например название композиции или обложку альбома для воспроизводимого элемента мультимедиа. Получите экземпляр этого класса с помощью свойства [**SystemMediaTransportControls.DisplayUpdater**](https://msdn.microsoft.com/library/windows/apps/dn278707). Для обычных сценариев рекомендуем способ передачи метаданных, который заключается в вызове метода [**CopyFromFileAsync**](https://msdn.microsoft.com/library/windows/apps/dn278694) и передачи в него воспроизводящегося файла мультимедиа. Модуль обновления отображения автоматически извлечет метаданные и изображение эскиза из файла.

Вызовите метод [**Update**](https://msdn.microsoft.com/library/windows/apps/dn278701), чтобы заставить системные элементы управления транспортом мультимедиа обновить свой интерфейс, используя новые метаданные и эскиз.

[!code-cs[SystemMediaTransportControlsUpdater](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetSystemMediaTransportControlsUpdater)]

Если это необходимо для вашего сценария, можно вручную обновить метаданные, отображаемые системными элементами управления транспортом, настроив значения свойств [**MusicProperties**](https://msdn.microsoft.com/library/windows/apps/dn278696), [**ImageProperties**](https://msdn.microsoft.com/library/windows/apps/dn278695) или [**VideoProperties**](https://msdn.microsoft.com/library/windows/apps/dn278702), предоставленных классом [**DisplayUpdater**](https://msdn.microsoft.com/library/windows/apps/dn278707).

[!code-cs[SystemMediaTransportControlsUpdaterManual](./code/SMTCWin10/cs/MainPage.xaml.cs#SystemMediaTransportControlsUpdaterManual)]

## <a name="update-the-system-media-transport-controls-timeline-properties"></a>Обновление свойств временной шкалы системных элементов управления транспортом мультимедиа

Системные элементы управления транспортом отображают информацию о временной шкале для воспроизводимого элемента мультимедиа, включая текущую позицию воспроизведения, время начала и время окончания элемента мультимедиа. Чтобы обновить свойства временной шкалы системных элементов управления транспортом, создайте объект [**SystemMediaTransportControlsTimelineProperties**](https://msdn.microsoft.com/library/windows/apps/mt218746). Настройте свойства объекта, чтобы отразить текущее состояние воспроизводящегося элемента мультимедиа. Вызовите метод [**SystemMediaTransportControls.UpdateTimelineProperties**](https://msdn.microsoft.com/library/windows/apps/mt218760), чтобы заставить элементы управления обновить временную шкалу.

[!code-cs[UpdateTimelineProperties](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetUpdateTimelineProperties)]

-   Необходимо указать значение для свойств [**StartTime**](https://msdn.microsoft.com/library/windows/apps/mt218751), [**EndTime**](https://msdn.microsoft.com/library/windows/apps/mt218747) и [**Position**](https://msdn.microsoft.com/library/windows/apps/mt218755), чтобы системные элементы управления отображали временную шкалу для воспроизводящегося элемента.

-   С помощью свойств [**MinSeekTime**](https://msdn.microsoft.com/library/windows/apps/mt218749) и [**MaxSeekTime**](https://msdn.microsoft.com/library/windows/apps/mt218748) можно указать диапазон в пределах временной шкалы, в котором пользователь может выполнять поиск. Для этого можно использовать типичный сценарий, заключающийся в том, чтобы разрешить поставщикам содержимого включать рекламные паузы в их мультимедиа.

    Необходимо настроить свойства [**MinSeekTime**](https://msdn.microsoft.com/library/windows/apps/mt218749) и [**MaxSeekTime**](https://msdn.microsoft.com/library/windows/apps/mt218748), чтобы можно было создать событие [**PositionChangeRequest**](https://msdn.microsoft.com/library/windows/apps/mt218755).

-   Рекомендуется синхронизировать системные элементы управления с процессом воспроизведения мультимедиа. Для этого во время воспроизведения необходимо обновлять эти свойства приблизительно каждые 5 секунд, а также при изменении состояния воспроизведения, например при приостановке или при поиске новой позиции.

## <a name="respond-to-player-property-changes"></a>Реакция на изменение свойств проигрывателя

Существует набор свойств системных элементов управления транспортом, относящихся к текущему состоянию мультимедиапроигрывателя, а не к состоянию воспроизводимого элемента мультимедиа. Каждое из этих свойств сопоставлено событию, которое создается, когда пользователь изменяет связанный элемент управления. Эти свойства и события перечислены ниже.

| Свойство                                                                  | Событие                                                                                                   |
|---------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [**AutoRepeatMode**](https://msdn.microsoft.com/library/windows/apps/mt218753) | [**AutoRepeatModeChangeRequested**](https://msdn.microsoft.com/library/windows/apps/mt218754) |
| [**PlaybackRate**](https://msdn.microsoft.com/library/windows/apps/mt218756)     | [**PlaybackRateChangeRequested**](https://msdn.microsoft.com/library/windows/apps/mt218757)     |
| [**ShuffleEnabled**](https://msdn.microsoft.com/library/windows/apps/mt218758) | [**ShuffleEnabledChangeRequested**](https://msdn.microsoft.com/library/windows/apps/mt218759) |

 
Чтобы можно было обрабатывать взаимодействие с пользователем с помощью одного из этих элементов управления, сначала зарегистрируйте обработчик для связанного события.

[!code-cs[RegisterPlaybackChangedHandler](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetRegisterPlaybackChangedHandler)]

Прежде всего убедитесь, что в обработчике события необходимое значение находится в допустимом и ожидаемом диапазоне. Если это так, настройте соответствующее свойство объекта [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926), а затем— соответствующее свойство объекта [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677).

[!code-cs[PlaybackChangedHandler](./code/SMTCWin10/cs/MainPage.xaml.cs#SnippetPlaybackChangedHandler)]

-   Чтобы возникло одно из этих событий свойств проигрывателя, необходимо настроить начальное значение для свойства. Например, событие [**PlaybackRateChangeRequested**](https://msdn.microsoft.com/library/windows/apps/mt218757) не будет создано до тех пор, пока вы хотя бы один раз не присвоите значение свойству [**PlaybackRate**](https://msdn.microsoft.com/library/windows/apps/mt218756).

## <a name="use-the-system-media-transport-controls-for-background-audio"></a>Использование системных элементов управления транспортом мультимедиа для воспроизведения звука в фоновом режиме

Если вы не используете автоматическую интеграцию SMTC, обеспечиваемую **MediaPlayer**, необходимо вручную провести интеграцию с SMTC для включения звука в фоновом режиме. Как минимум, ваше приложение должно включить кнопки воспроизведения и приостановки, задав свойствам [**IsPlayEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278714) и [**IsPauseEnabled**](https://msdn.microsoft.com/library/windows/apps/dn278713) значение true. Кроме того, ваше приложение должно обрабатывать событие [**ButtonPressed**](https://msdn.microsoft.com/library/windows/apps/dn278706). Если ваше приложение не соответствует этим требованиям, воспроизведение звука будет прекращаться при переходе приложения в фоновый режим.

Приложения, которые используют новую модель с одним процессом для воспроизведения звука в фоновом режиме, должны получать экземпляр [**SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn278677) путем вызова [**GetForCurrentView**](https://msdn.microsoft.com/library/windows/apps/dn278708). Приложения, использующие устаревшую модель с двумя процессами для фонового воспроизведения звука, должны использовать [**BackgroundMediaPlayer.Current.SystemMediaTransportControls**](https://msdn.microsoft.com/library/windows/apps/dn926635) для получения доступа к SMTC из своего фонового процесса.

Дополнительные сведения о воспроизведении звука в фоновом режиме см. в разделе [Воспроизведение мультимедиа в фоновом режиме](background-audio.md).

## <a name="related-topics"></a>Еще по теме
* [Воспроизведение мультимедиа](media-playback.md)
* [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md) 
* [Пример системной транспортировки мультимедиа](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/SystemMediaTransportControls) 

 




