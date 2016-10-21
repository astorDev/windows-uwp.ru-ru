---
author: drewbatgit
ms.assetid: C5623861-6280-4352-8F22-80EB009D662C
description: "В этой статье показано, как использовать класс MediaSource, предоставляющий универсальный способ обращения к данным мультимедиа и их воспроизведения из различных источников (например, из локальных или удаленных файлов) и универсальную модель получения доступа к данным мультимедиа независимо от их формата."
title: "Элементы, списки воспроизведения и звуковые дорожки мультимедиа"
translationtype: Human Translation
ms.sourcegitcommit: c2e337e88f9dda3380dd62c32ca6e5d942366636
ms.openlocfilehash: bb49af7a386356647000e268bcc6983351eaf4b8

---

# Элементы, списки воспроизведения и звуковые дорожки мультимедиа

\[ Обновлено для приложений UWP в Windows10. Статьи для Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

 В этой статье показано, как использовать класс [**MediaSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaSource), предоставляющий универсальный способ обращения к данным мультимедиа и их воспроизведения из различных источников (например, из локальных или удаленных файлов) и универсальную модель получения доступа к данным мультимедиа независимо от их формата. Класс [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/dn930939) расширяет функциональные возможности класса **MediaSource**. Он позволяет выбирать необходимые дорожки звука, видео и метаданных в файле мультимедиа и управлять ими. [
              Класс **MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/dn930955) позволяет создавать списки воспроизведения из одного или нескольких элементов воспроизведения мультимедиа.


## Создание и воспроизведение MediaSource

Создайте новый экземпляр **MediaSource**, вызвав один из фабричных методов, которые предоставляются следующими классами:

-   [**CreateFromAdaptiveMediaSource**](https://msdn.microsoft.com/library/windows/apps/dn930906)
-   [**CreateFromIMediaSource**](https://msdn.microsoft.com/library/windows/apps/dn965527)
-   [**CreateFromMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn930907)
-   [**CreateFromMseStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn930908)
-   [**CreateFromStorageFile**](https://msdn.microsoft.com/library/windows/apps/dn930909)
-   [**CreateFromStream**](https://msdn.microsoft.com/library/windows/apps/dn930910)
-   [**CreateFromStreamReference**](https://msdn.microsoft.com/library/windows/apps/dn930911)
-   [**CreateFromUri**](https://msdn.microsoft.com/library/windows/apps/dn930912)

Созданный экземпляр **MediaSource** можно воспроизводить с помощью класса [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/dn652535), задав свойство [**Источник**](https://msdn.microsoft.com/library/windows/apps/dn987010). Начиная c Windows10 версии 1607, можно назначить класс **MediaPlayer** классу [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement), вызвав метод [**SetMediaPlayer**](https://msdn.microsoft.com/library/windows/apps/mt708764), чтобы отобразить содержимое проигрывателя мультимедиа на странице XAML. Этот метод является предпочтительным по сравнению с использованием класса **MediaElement**. Дополнительные сведения об использовании класса **MediaPlayer** см. в разделе [**Воспроизведение аудио и видео с помощью класса MediaPlayer**](play-audio-and-video-with-mediaplayer.md).

В следующем примере показано, как воспроизвести выбранный пользователем файл мультимедиа в классе **MediaPlayer** с помощью **MediaSource**.

Для этого сценария потребуется включить пространства имен [**Windows.Media.Core**](https://msdn.microsoft.com/library/windows/apps/dn278962) и [**Windows.Media.Playback**](https://msdn.microsoft.com/library/windows/apps/dn640562).

[!code-cs[Использование](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetUsing)]

Объявите переменную типа **MediaSource**. В примерах этой статьи источник мультимедиа объявлен как член класса, чтобы к нему можно было получить доступ из разных расположений.

[!code-cs[DeclareMediaSource](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetDeclareMediaSource)]

Объявите переменную, чтобы сохранить объект **MediaPlayer**, и если требуется отобразить содержимое мультимедиа в XAML, добавьте на свою страницу элемент управления **MediaPlayerElement**.

[!code-cs[DeclareMediaPlayer](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetDeclareMediaPlayer)]

[!code-xml[MediaPlayerElement](./code/MediaSource_RS1/cs/MainPage.xaml#SnippetMediaPlayerElement)]

Чтобы пользователь мог выбрать файл мультимедиа для воспроизведения, используйте класс [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847). С помощью объекта [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), который был возвращен методом [**PickSingleFileAsync**](https://msdn.microsoft.com/library/windows/apps/jj635275) средства выбора файлов, инициализируйте новый объект MediaObject, вызвав метод [**MediaSource.CreateFromStorageFile**](https://msdn.microsoft.com/library/windows/apps/dn930909). Наконец, установите источник мультимедиа в качестве источника воспроизведения для класса **MediaElement**, вызвав метод [**SetPlaybackSource**](https://msdn.microsoft.com/library/windows/apps/dn899085).

[!code-cs[PlayMediaSource](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetPlayMediaSource)]

По умолчанию **MediaPlayer** не начинает воспроизведение автоматически, когда задан источник мультимедиа. Можно начать воспроизведение вручную, вызвав метод [**Play**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Play).

[!code-cs[Play](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetPlay)]

Кроме того, можно задать для свойства [**AutoPlay**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.AutoPlay) класса **MediaPlayer** значение true, чтобы проигрыватель начал воспроизведение, как только источник мультимедиа будет задан.

[!code-cs[Автозапуск](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAutoPlay)]

## Обработка нескольких дорожек звука, видео и метаданных с помощью MediaPlaybackItem

Класс [**MediaSource**](https://msdn.microsoft.com/library/windows/apps/dn930905) удобно использовать для воспроизведения, поскольку он предоставляет универсальный способ воспроизведения мультимедиа из различных источников, однако класс [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/dn930939), созданный на основе класса **MediaSource**, предоставляет более широкие возможности. К ним относится доступ к различным дорожкам звука, видео и данных в элементе мультимедиа и управление ими.

Объявите переменную для хранения объекта **MediaPlaybackItem**.

[!code-cs[DeclareMediaPlaybackItem](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetDeclareMediaPlaybackItem)]

Создайте объект **MediaPlaybackItem**, вызвав конструктор и передав ему инициализированный объект **MediaSource**.

Если ваше приложение поддерживает несколько дорожек звука, видео и данных в элементе мультимедиа, зарегистрируйте обработчики событий [**AudioTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930948), [**VideoTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930954) или [**TimedMetadataTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930952).

Наконец, установите в качестве источника воспроизведения класса **MediaElement** или **MediaPlayer** элемент **MediaPlaybackItem**.

[!code-cs[PlayMediaPlaybackItem](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetPlayMediaPlaybackItem)]

> [!NOTE] 
> Класс **MediaSource** может быть связан только с одним классом **MediaPlaybackItem**. После создания объекта **MediaPlaybackItem** из источника попытка создать другой элемент воспроизведения из этого же источника приведет к ошибке. Кроме того, после создания класса **MediaPlaybackItem** из источника мультимедиа вы не сможете непосредственно задать объект **MediaSource** в качестве источника для **MediaPlayer**. Вместо него следует использовать класс **MediaPlaybackItem**.

Событие [**VideoTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930954) возникает после того, как объект **MediaPlaybackItem**, содержащий несколько видеодорожек, назначается в качестве источника воспроизведения. Это событие может возникнуть повторно, если список видеодорожек меняется при изменении элемента мультимедиа. Обработчик этого события позволяет обновить пользовательский интерфейс, чтобы позволить пользователю переключаться между доступными дорожками. В этом примере для отображения доступных видеодорожек используется элемент управления [**ComboBox**](https://msdn.microsoft.com/library/windows/apps/br209348).

[!code-xml[VideoComboBox](./code/MediaSource_Win10/cs/MainPage.xaml#SnippetVideoComboBox)]

В обработчике события **VideoTracksChanged** циклически переберите все дорожки в списке [**VideoTracks**](https://msdn.microsoft.com/library/windows/apps/dn930953) каждого элемента воспроизведения. Для каждой дорожки создается новый элемент управления [**ComboBoxItem**](https://msdn.microsoft.com/library/windows/apps/br209349). Если у дорожки еще нет метки, она создается из индекса дорожки. Свойству [**Tag**](https://msdn.microsoft.com/library/windows/apps/br208745) поля со списком присваивается значение индекса дорожки. Это позволит идентифицировать свойство позже. Наконец, элемент добавляется в поле со списком. Обратите внимание, что эти операции выполняются внутри вызова [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317), поскольку все изменения пользовательского интерфейса следует осуществлять в потоке пользовательского интерфейса, а это событие создается в другом потоке.

[!code-cs[VideoTracksChanged](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetVideoTracksChanged)]

В обработчике события [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/br209776) в поле со списком индекс дорожки извлекается из свойства **Tag** выбранного элемента. Если установить значение свойства [**SelectedIndex**](https://msdn.microsoft.com/library/windows/apps/dn956634) для списка [**VideoTracks**](https://msdn.microsoft.com/library/windows/apps/dn930953) элемента воспроизведения мультимедиа, то **MediaElement** или **MediaPlayer** изменят активную видеодорожку на дорожку с указанным индексом.

[!code-cs[VideoTracksSelectionChanged](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetVideoTracksSelectionChanged)]

Управление элементами мультимедиа с несколькими звуковыми дорожками осуществляется точно так же, как в случае с видеодорожками. Обработайте событие [**AudioTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930948), чтобы обновить пользовательский интерфейс и отобразить звуковые дорожки, имеющиеся в списке [**AudioTracks**](https://msdn.microsoft.com/library/windows/apps/dn930947) элемента воспроизведения. Когда пользователь выбирает звуковую дорожку, установите соответствующее значение для свойства [**SelectedIndex**](https://msdn.microsoft.com/library/windows/apps/dn930937) списка **AudioTracks**, чтобы **MediaElement** или **MediaPlayer** переключили активную звуковую дорожку на дорожку с указанным индексом.

[!code-xml[AudioComboBox](./code/MediaSource_Win10/cs/MainPage.xaml#SnippetAudioComboBox)]

[!code-cs[AudioTracksChanged](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetAudioTracksChanged)]

[!code-cs[AudioTracksSelectionChanged](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetAudioTracksSelectionChanged)]

Помимо звуковых дорожек и видеодорожек, в объекте **MediaPlaybackItem** могут присутствовать (или же отсутствовать) объекты [**TimedMetadataTrack**](https://msdn.microsoft.com/library/windows/apps/dn956580). Синхронизированная дорожка метаданных может содержать текст субтитров или надписей, а также специальные данные, используемые в приложении. Синхронизированная дорожка метаданных содержит список подсказок, представленных объектами, которые унаследованы от [**IMediaCue**](https://msdn.microsoft.com/library/windows/apps/dn930899), например [**DataCue**](https://msdn.microsoft.com/library/windows/apps/dn930892) или [**TimedTextCue**](https://msdn.microsoft.com/library/windows/apps/dn956655). Каждая подсказка имеет время начала и длительность. Они определяют, когда подсказка должна активироваться и на какое время.

Так же, как и в случае со звуковыми дорожкам и видеодорожками, синхронизированные дорожки метаданных для элемента мультимедиа могут быть обнаружены с помощью обработки события [**TimedMetadataTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930952) объекта **MediaPlaybackItem**. Однако пользователю может потребоваться включить несколько дорожек метаданных одновременно. Кроме того, в зависимости от сценария вашего приложения вы можете включать либо отключать дорожки метаданных автоматически, без вмешательства пользователя. Чтобы продемонстрировать это, в следующем примере для каждой дорожки метаданных в элементе мультимедиа добавим элементы управления [**ToggleButton**](https://msdn.microsoft.com/library/windows/apps/br209795), позволяющие пользователю включать либо отключать дорожку. Свойству **Tag** каждой кнопки присваивается значение индекса связанной дорожки метаданных, что позволяет идентифицировать его при нажатии кнопки.

[!code-xml[MetaStackPanel](./code/MediaSource_Win10/cs/MainPage.xaml#SnippetMetaStackPanel)]

[!code-cs[TimedMetadataTrackschanged](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetTimedMetadataTrackschanged)]

Так как одновременно могут быть активны несколько дорожек метаданных, нельзя просто установить активный индекс для списка дорожек метаданных. Вместо этого вызовите метод [**SetPresentationMode**](https://msdn.microsoft.com/library/windows/apps/dn986977) объекта **MediaPlaybackItem**, передав ему индекс дорожки, на которую нужно переключиться, а затем укажите значение из перечисления [**TimedMetadataTrackPresentationMode**](https://msdn.microsoft.com/library/windows/apps/dn987016). Режим презентации, который вы выбираете, зависит от реализации приложения. В следующем примере при включении дорожке метаданных присваивается значение **PlatformPresented**. В случае текстовых дорожек это означает, что система автоматически отобразит текстовую подсказку из данной дорожки. Когда переключатель переводится в выключенное состояние, для режима презентации устанавливается значение **Disabled**. При этом текст не отображается, и события подсказок не возникают. События подсказок будут рассмотрены в этой статье ниже.

[!code-cs[ToggleChecked](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetToggleChecked)]

[!code-cs[ToggleUnchecked](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetToggleUnchecked)]

При обработке дорожек метаданных можно получить доступ к набору подсказок, находящихся в дорожке, с помощью свойств [**Cues**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.TimedMetadataTrack.Cues) или [**ActiveCues**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.TimedMetadataTrack.ActiveCues). Это можно сделать, чтобы обновить ваш пользовательский интерфейс и отображать в нем расположения подсказок для элемента мультимедиа.

## Обработка неподдерживаемых кодеков и неизвестных ошибок при открытии элементов мультимедиа
Начиная c Windows10 версии 1607, можно проверить, поддерживается ли кодек, необходимый для воспроизведения элемента мультимедиа, полностью или частично на устройстве, на котором выполняется приложение. Если речь идет об обработчике событий изменения дорожек **MediaPlaybackItem**, таких как [**AudioTracksChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.AudioTracksChanged), сначала проверьте, не является ли изменение дорожки вставкой новой дорожки. В этом случае можно получить ссылку на дорожку, вставляемую с использованием индекса, который передается в параметре **IVectorChangedEventArgs.Index** с соответствующей коллекцией дорожек параметра **MediaPlaybackItem**, например коллекцией [**AudioTracks**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.AudioTracks).

Получив ссылку на вставляемую дорожку, проверьте значение [**DecoderStatus**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrackSupportInfo.DecoderStatus) свойства [**SupportInfo**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrack.SupportInfo) дорожки. Если установлено значение [**FullySupported**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaDecoderStatus), то соответствующий кодек, необходимый для воспроизведения дорожки, присутствует на устройстве. Если установлено значение [**Degraded**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaDecoderStatus), дорожку можно воспроизвести в системе, однако качество воспроизведения так или иначе ухудшится. Например, звуковая дорожка 5.1 может воспроизводиться как 2-канальное стерео. В этом случае имеет смысл обновить свой пользовательский интерфейс, чтобы оповестить пользователя об ухудшении качества воспроизведения. Если установлено значение [**UnsupportedSubtype**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaDecoderStatus) или [**UnsupportedEncoderProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaDecoderStatus), то с установленными на устройстве кодеками воспроизвести дорожку невозможно. Возможно, следует предупредить пользователя и пропустить воспроизведение элемента или внедрить пользовательский интерфейс, позволяющий загрузить нужный кодек. Метод [**GetEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrack.GetEncodingProperties) дорожки можно использовать для определения требуемого кодека для воспроизведения.

Наконец, можно зарегистрироваться для события дорожки [**OpenFailed**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrack.OpenFailed), которое будет возникать, если дорожка поддерживается на устройстве, но открыть ее не удалось из-за неизвестной ошибки в конвейере.

[!code-cs[AudioTracksChanged_CodecCheck](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAudioTracksChanged_CodecCheck)]

В обработчике событий [**OpenFailed**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrack.OpenFailed) можно проверить, известен ли статус **MediaSource**. Если статус неизвестен, можно программными средствами выбрать другую дорожку для воспроизведения, разрешить пользователю выбрать другую дорожку или отказаться от воспроизведения.

[!code-cs[OpenFailed](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetOpenFailed)]

## Задание свойств отображения, используемых системными элементами управления транспортировкой мультимедиа
Начиная с Windows 10 версии 1607 мультимедиа, воспроизводимые в классе [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer), автоматически интегрируются в системные элементы управления транспортировкой мультимедиа (SMTC) по умолчанию. Можно задать метаданные, которые будут отображаться в SMTC, обновив свойства воспроизведения для класса **MediaPlaybackItem**. Получите объект, представляющий свойства отображения для элемента, вызвав метод [**GetDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.GetDisplayProperties). Укажите, относится ли элемент воспроизведения к музыке или видео, задав свойство [**Тип**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.Type). Затем задайте значения свойств объекта [**VideoProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.VideoProperties) или [**MusicProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.MusicProperties). Вызовите метод [**ApplyDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/mt489923), чтобы задать для обновления свойств элемента предоставленные вами значения. Обычно приложение извлекает значения отображения из веб-службы динамически, но в следующем примере показано извлечение со значениями, заданными в коде.

[!code-cs[SetVideoProperties](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetSetVideoProperties)]

[!code-cs[SetMusicProperties](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetSetMusicProperties)]

## Добавление внешнего синхронизированного текста с помощью класса TimedTextSource

В некоторых случаях у вас могут быть внешние файлы, содержащие синхронизированный текст, который связан с элементом мультимедиа (например, отдельные файлы с субтитрами на разных языках). Для загрузки внешних файлов с синхронизированным текстом из потока или URI используйте класс [**TimedTextSource**](https://msdn.microsoft.com/library/windows/apps/dn956679).

В этом примере в коллекции **Dictionary** хранится список источников синхронизированного текста для элемента мультимедиа. URI источника и объект **TimedTextSource** используются в качестве пары "ключ/значение", чтобы идентифицировать дорожки после их обработки.

[!code-cs[TimedTextSourceMap](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetTimedTextSourceMap)]

Создайте объект **TimedTextSource** для каждого внешнего файла с синхронизированным текстом, вызвав метод [**CreateFromUri**](https://msdn.microsoft.com/library/windows/apps/dn708190). Добавьте запись в коллекцию **Dictionary** для источника синхронизированного текста. Добавьте обработчик для события [**TimedTextSource.Resolved**](https://msdn.microsoft.com/library/windows/apps/dn965540), чтобы обрабатывать ситуации, когда элемент не удалось загрузить либо, устанавливать для него дополнительные свойства после успешной загрузки.

Зарегистрируйте все объекты **TimedTextSource** в **MediaSource**, добавив их в коллекцию [**ExternalTimedTextSources**](https://msdn.microsoft.com/library/windows/apps/dn930916). Обратите внимание, что внешние источники синхронизированного текста добавляются напрямую в **MediaSource**, а не в **MediaPlaybackItem**, созданный из источника. Чтобы отразить в пользовательском интерфейсе внешние текстовые дорожки, зарегистрируйте и обработайте событие **TimedMetadataTracksChanged**, как было описано выше.

[!code-cs[TimedTextSource](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetTimedTextSource)]

В обработчике события [**TimedTextSource.Resolved**](https://msdn.microsoft.com/library/windows/apps/dn965540) проверьте свойство **Error** класса [**TimedTextSourceResolveResultEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn965537), передаваемое обработчику, чтобы определить, не произошла ли в процессе загрузки синхронизированных текстовых данных ошибка. Если элемент был обработан успешно, вы можете использовать этот обработчик для обновления дополнительных свойств обработанной дорожки. В следующем примере для каждой дорожки добавляется метка на основе идентификатора URI, ранее сохраненного в коллекции **Dictionary**.

[!code-cs[TimedTextSourceResolved](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetTimedTextSourceResolved)]

## Добавление дополнительных дорожек метаданных

Вы можете динамически создавать пользовательские дорожки метаданных в коде и привязывать их к источнику мультимедиа. Создаваемые дорожки могут содержать текст субтитров или надписей либо специальные данные приложения.

Создайте объект [**TimedMetadataTrack**](https://msdn.microsoft.com/library/windows/apps/dn956580), вызвав конструктор и указав идентификатор, идентификатор языка и значение из перечисления [**TimedMetadataKind**](https://msdn.microsoft.com/library/windows/apps/dn956578). Зарегистрируйте обработчики для событий [**CueEntered**](https://msdn.microsoft.com/library/windows/apps/dn956583) и [**CueExited**](https://msdn.microsoft.com/library/windows/apps/dn956584). Эти события возникают, когда достигнуто время начала подсказки и когда время действия подсказки истекло.

Создайте объект подсказки, соответствующий типу созданной дорожки метаданных, и укажите для дорожки идентификатор, время начала и продолжительность. В следующем примере создается дорожка данных, поэтому генерируется набор объектов [**DataCue**](https://msdn.microsoft.com/library/windows/apps/dn930892). Для каждой подсказки предоставляется буфер, содержащий специальные данные приложения. Чтобы зарегистрировать новую дорожку, добавьте ее в коллекцию [**ExternalTimedMetadataTracks**](https://msdn.microsoft.com/library/windows/apps/dn930915) объекта **MediaSource**.

[!code-cs[AddDataTrack](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetAddDataTrack)]

Событие **CueEntered** инициируется, когда достигнуто время начала подсказки при условии, что связанная дорожка имеет режим представления **ApplicationPresented**, **Hidden** или **PlatformPresented.** События подсказки не вызываются для дорожек метаданных в режиме отображения **Disabled**. Следующий пример просто выводит пользовательские данные, связанные с подсказкой, в окно отладки.

[!code-cs[DataCueEntered](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetDataCueEntered)]

В следующем примере для добавления пользовательской текстовой дорожки при создании дорожки указывается значение **TimedMetadataKind**, а объекты [**TimedTextCue**](https://msdn.microsoft.com/library/windows/apps/dn956655) используются для добавления подсказок к дорожке.

[!code-cs[AddTextTrack](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetAddTextTrack)]

[!code-cs[TextCueEntered](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetTextCueEntered)]

## Воспроизведение списка элементов мультимедиа с помощью MediaPlaybackList

Класс [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/dn930955) позволяет создать список воспроизведения элементов мультимедиа, которые представлены объектами **MediaPlaybackItem**.

**Примечание**. Элементы в классе [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/dn930955) обрабатываются с помощью непрерывного воспроизведения. Система будет использовать предоставленные метаданные в файлах, закодированных в формате MP3 или AAC, чтобы определить задержку или компенсацию заполнения, необходимую для непрерывного воспроизведения. Если файлы, закодированные в формате MP3 или AAC не предоставляют этих данных, система определяет задержку и заполнение эвристически. В случае форматов без потери качества, таких как PCM, FLAC или ALAC система не предпринимает никаких действий, поскольку эти кодировщики не создают задержки или заполнения.

Сначала объявите переменную для хранения объекта **MediaPlaybackList**.

[!code-cs[DeclareMediaPlaybackList](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetDeclareMediaPlaybackList)]

Создайте объект **MediaPlaybackItem** для каждого элемента мультимедиа, который нужно добавить в список, с помощью процедуры, описанной выше. Инициализируйте объект **MediaPlaybackList** и добавьте в него элементы воспроизведения мультимедиа. Зарегистрируйте обработчик события [**CurrentItemChanged**](https://msdn.microsoft.com/library/windows/apps/dn930957). Это событие позволяет обновить пользовательский интерфейс, чтобы отобразить элемент мультимедиа, который воспроизводится в данный момент. Наконец, установите в качестве источника воспроизведения класса **MediaPlayer** список **MediaPlaybackList**.

[!code-cs[PlayMediaPlaybackList](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetPlayMediaPlaybackList)]

В обработчике события **CurrentItemChanged** обновите пользовательский интерфейс, чтобы отобразить воспроизводимый в настоящее время элемент, который можно получить с помощью свойства [**NewItem**](https://msdn.microsoft.com/library/windows/apps/dn930930) объекта [**CurrentMediaPlaybackItemChangedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn930929), переданного данному событию. Помните, что если вы обновляете пользовательский интерфейс с помощью этого события, необходимо делать это в рамках вызова [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317), чтобы эти обновления выполнялись в потоке пользовательского интерфейса.

> [!NOTE] 
> Система автоматически не удаляет элементы мультимедиа после воспроизведения. Это означает, что если пользователь возвращается в списке назад, ранее воспроизведенные композиции можно снова воспроизвести непрерывно, однако в этом случае увеличится потребление памяти вашим приложением по мере того, как все больше композиций из списка будут воспроизводиться. Необходимо периодически освобождать ресурсы для ранее воспроизведенных элементов мультимедиа. Это особенно важно в случаях, когда приложение воспроизводит в фоновом режиме и ресурсы сильно ограничены. 

Для освобождения ресурсов от ранее воспроизведенных элементов мультимедиа можно воспользоваться событием **CurrentItemChanged**. Чтобы сохранить ссылку на ранее воспроизведенные элементы, создайте коллекцию **Очередь**. Установите переменную, определяющую максимальное количество элементов мультимедиа, которые следует хранить в памяти. В обработчике получите ссылку на ранее воспроизведенный элемент и добавьте ее в очередь, а самую старую запись в очереди удалите. Вызовите метод [**Reset**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaSource.Reset) для возвращенного элемента, чтобы освободить его ресурсы, но сначала убедитесь, что элемент удален из очереди и в настоящее время не воспроизводится. Это актуально для ситуаций, когда элемент воспроизводится несколько раз.

[!code-cs[DeclareItemQueue](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetDeclareItemQueue)]

[!code-cs[MediaPlaybackListItemChanged](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetMediaPlaybackListItemChanged)]

Вызовите метод [**MovePrevious**](https://msdn.microsoft.com/library/windows/apps/mt146455) или [**MoveNext**](https://msdn.microsoft.com/library/windows/apps/mt146454), чтобы проигрыватель мультимедиа воспроизвел предыдущий или следующий элемент в списке **MediaPlaybackList**.

[!code-cs[PrevButton](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetPrevButton)]

[!code-cs[NextButton](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetNextButton)]

Значение свойства [**ShuffleEnabled**](https://msdn.microsoft.com/library/windows/apps/mt146457) определяет, должен ли проигрыватель мультимедиа воспроизводить элементы списка в случайном порядке.

[!code-cs[ShuffleButton](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetShuffleButton)]

Значение свойства [**AutoRepeatEnabled**](https://msdn.microsoft.com/library/windows/apps/mt146452) определяет, должен ли проигрыватель мультимедиа воспроизводить элементы списка циклически.

[!code-cs[RepeatButton](./code/MediaSource_Win10/cs/MainPage.xaml.cs#SnippetRepeatButton)]


###Обработка сбоев элементов мультимедиа в списке воспроизведения
Событие [**ItemFailed**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackList.ItemFailed) возникает, если не удается открыть элемент в списке. Свойство [**ErrorCode**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItemError.ErrorCode) объекта [**MediaPlaybackItemError**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItemError), переданное в обработчик, по возможности перечисляет конкретные причины сбоя, включая сетевые ошибки, ошибки декодирования и шифрования.

[!code-cs[ItemFailed](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetItemFailed)]

## Связанные статьи
* [Воспроизведение мультимедиа](media-playback.md)
* [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md)
* [Воспроизведение мультимедиа в фоновом режиме](background-audio.md)




<!--HONumber=Aug16_HO3-->


