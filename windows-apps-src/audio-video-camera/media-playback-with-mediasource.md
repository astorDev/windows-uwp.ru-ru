---
author: drewbatgit
ms.assetid: C5623861-6280-4352-8F22-80EB009D662C
description: В этой статье показано, как использовать класс MediaSource, предоставляющий универсальный способ обращения к данным мультимедиа и их воспроизведения из различных источников (например, из локальных или удаленных файлов) и универсальную модель получения доступа к данным мультимедиа независимо от их формата.
title: Элементы, списки воспроизведения и звуковые дорожки мультимедиа
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 73b6a19e2385f1a9b8afa4672df50d17ac16ec97
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7288922"
---
# <a name="media-items-playlists-and-tracks"></a>Элементы, списки воспроизведения и звуковые дорожки мультимедиа


 В этой статье показано, как использовать класс [**MediaSource**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaSource), предоставляющий универсальный способ обращения к данным мультимедиа и их воспроизведения из различных источников (например, из локальных или удаленных файлов) и универсальную модель получения доступа к данным мультимедиа независимо от их формата. Класс [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/dn930939) расширяет функциональные возможности класса **MediaSource**. Он позволяет выбирать необходимые дорожки звука, видео и метаданных в файле мультимедиа и управлять ими. Класс [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/dn930955) позволяет создавать списки воспроизведения из одного или нескольких элементов воспроизведения мультимедиа.


## <a name="create-and-play-a-mediasource"></a>Создание и воспроизведение MediaSource

Создайте новый экземпляр **MediaSource**, вызвав один из фабричных методов, которые предоставляются следующими классами:

-   [**CreateFromAdaptiveMediaSource**](https://msdn.microsoft.com/library/windows/apps/dn930906)
-   [**CreateFromIMediaSource**](https://msdn.microsoft.com/library/windows/apps/dn965527)
-   [**CreateFromMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn930907)
-   [**CreateFromMseStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn930908)
-   [**CreateFromStorageFile**](https://msdn.microsoft.com/library/windows/apps/dn930909)
-   [**CreateFromStream**](https://msdn.microsoft.com/library/windows/apps/dn930910)
-   [**CreateFromStreamReference**](https://msdn.microsoft.com/library/windows/apps/dn930911)
-   [**CreateFromUri**](https://msdn.microsoft.com/library/windows/apps/dn930912)
-   [**CreateFromDownloadOperation**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.createfromdownloadoperation)

Созданный экземпляр **MediaSource** можно воспроизводить с помощью класса [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/dn652535), задав свойство [**Источник**](https://msdn.microsoft.com/library/windows/apps/dn987010). Начиная c Windows10 версии 1607, можно назначить класс **MediaPlayer** классу [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement), вызвав метод [**SetMediaPlayer**](https://msdn.microsoft.com/library/windows/apps/mt708764), чтобы отобразить содержимое проигрывателя мультимедиа на странице XAML. Этот метод является предпочтительным по сравнению с использованием класса **MediaElement**. Дополнительные сведения об использовании класса **MediaPlayer** см. в разделе [**Воспроизведение аудио и видео с помощью класса MediaPlayer**](play-audio-and-video-with-mediaplayer.md).

В следующем примере показано, как воспроизвести выбранный пользователем файл мультимедиа в классе **MediaPlayer** с помощью **MediaSource**.

Для этого сценария потребуется включить пространства имен [**Windows.Media.Core**](https://msdn.microsoft.com/library/windows/apps/dn278962) и [**Windows.Media.Playback**](https://msdn.microsoft.com/library/windows/apps/dn640562).

[!code-cs[Using](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetUsing)]

Объявите переменную типа **MediaSource**. В примерах этой статьи источник мультимедиа объявлен как член класса, чтобы к нему можно было получить доступ из разных расположений.

[!code-cs[DeclareMediaSource](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetDeclareMediaSource)]

Объявите переменную, чтобы сохранить объект **MediaPlayer**, и если требуется отобразить содержимое мультимедиа в XAML, добавьте на свою страницу элемент управления **MediaPlayerElement**.

[!code-cs[DeclareMediaPlayer](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetDeclareMediaPlayer)]

[!code-xml[MediaPlayerElement](./code/MediaSource_RS1/cs/MainPage.xaml#SnippetMediaPlayerElement)]

Чтобы пользователь мог выбрать файл мультимедиа для воспроизведения, используйте класс [**FileOpenPicker**](https://msdn.microsoft.com/library/windows/apps/br207847). С помощью объекта [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171), который был возвращен методом [**PickSingleFileAsync**](https://msdn.microsoft.com/library/windows/apps/jj635275) средства выбора файлов, инициализируйте новый объект MediaObject, вызвав метод [**MediaSource.CreateFromStorageFile**](https://msdn.microsoft.com/library/windows/apps/dn930909). Наконец, установите источник мультимедиа в качестве источника воспроизведения для класса **MediaElement**, вызвав метод [**SetPlaybackSource**](https://msdn.microsoft.com/library/windows/apps/dn899085).

[!code-cs[PlayMediaSource](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetPlayMediaSource)]

По умолчанию **MediaPlayer** не начинает воспроизведение автоматически, когда задан источник мультимедиа. Можно начать воспроизведение вручную, вызвав метод [**Play**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Play).

[!code-cs[Play](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetPlay)]

Кроме того, можно задать для свойства [**AutoPlay**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.AutoPlay) класса **MediaPlayer** значение true, чтобы проигрыватель начал воспроизведение, как только источник мультимедиа будет задан.

[!code-cs[AutoPlay](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAutoPlay)]

### <a name="create-a-mediasource-from-a-downloadoperation"></a>Создание MediaSource из DownloadOperation
Начиная с Windows версии 1803, вы можете создать объект **MediaSource** из **DownloadOperation**.

[!code-cs[CreateMediaSourceFromDownload](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetCreateMediaSourceFromDownload)]

Хоть вы и можете создать **MediaSource** из операции загрузки, не начав ее и не установив значение true для ее свойства **IsRandomAccessRequired**, обязательно помните, что вы должны выполнить оба этих действия перед привязкой **MediaSource** к **MediaPlayer** или к **MediaPlayerElement** для последующего воспроизведения мультимедиа.

[!code-cs[StartDownload](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetStartDownload)]


## <a name="handle-multiple-audio-video-and-metadata-tracks-with-mediaplaybackitem"></a>Обработка нескольких дорожек звука, видео и метаданных с помощью MediaPlaybackItem

Класс [**MediaSource**](https://msdn.microsoft.com/library/windows/apps/dn930905) удобно использовать для воспроизведения, поскольку он предоставляет универсальный способ воспроизведения мультимедиа из различных источников, однако класс [**MediaPlaybackItem**](https://msdn.microsoft.com/library/windows/apps/dn930939), созданный на основе класса **MediaSource**, предоставляет более широкие возможности. К ним относится доступ к различным дорожкам звука, видео и данных в элементе мультимедиа и управление ими.

Объявите переменную для хранения объекта **MediaPlaybackItem**.

[!code-cs[DeclareMediaPlaybackItem](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetDeclareMediaPlaybackItem)]

Создайте объект **MediaPlaybackItem**, вызвав конструктор и передав ему инициализированный объект **MediaSource**.

Если ваше приложение поддерживает несколько дорожек звука, видео и данных в элементе мультимедиа, зарегистрируйте обработчики событий [**AudioTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930948), [**VideoTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930954) или [**TimedMetadataTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930952).

Наконец, установите в качестве источника воспроизведения класса **MediaElement** или **MediaPlayer** элемент **MediaPlaybackItem**.

[!code-cs[PlayMediaPlaybackItem](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetPlayMediaPlaybackItem)]

> [!NOTE] 
> Класс **MediaSource** может быть связан только с одним классом **MediaPlaybackItem**. После создания объекта **MediaPlaybackItem** из источника попытка создать другой элемент воспроизведения из этого же источника приведет к ошибке. Кроме того, после создания класса **MediaPlaybackItem** из источника мультимедиа вы не сможете непосредственно задать объект **MediaSource** в качестве источника для **MediaPlayer**. Вместо него следует использовать класс **MediaPlaybackItem**.

Событие [**VideoTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930954) возникает после того, как объект **MediaPlaybackItem**, содержащий несколько видеодорожек, назначается в качестве источника воспроизведения. Это событие может возникнуть повторно, если список видеодорожек меняется при изменении элемента мультимедиа. Обработчик этого события позволяет обновить пользовательский интерфейс, чтобы позволить пользователю переключаться между доступными дорожками. В этом примере для отображения доступных видеодорожек используется элемент управления [**ComboBox**](https://msdn.microsoft.com/library/windows/apps/br209348).

[!code-xml[VideoComboBox](./code/MediaSource_RS1/cs/MainPage.xaml#SnippetVideoComboBox)]

В обработчике события **VideoTracksChanged** циклически переберите все дорожки в списке [**VideoTracks**](https://msdn.microsoft.com/library/windows/apps/dn930953) каждого элемента воспроизведения. Для каждой дорожки создается новый элемент управления [**ComboBoxItem**](https://msdn.microsoft.com/library/windows/apps/br209349). Если у дорожки еще нет метки, она создается из индекса дорожки. Свойству [**Tag**](https://msdn.microsoft.com/library/windows/apps/br208745) поля со списком присваивается значение индекса дорожки. Это позволит идентифицировать свойство позже. Наконец, элемент добавляется в поле со списком. Обратите внимание, что эти операции выполняются внутри вызова [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317), поскольку все изменения пользовательского интерфейса следует осуществлять в потоке пользовательского интерфейса, а это событие создается в другом потоке.

[!code-cs[VideoTracksChanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetVideoTracksChanged)]

В обработчике события [**SelectionChanged**](https://msdn.microsoft.com/library/windows/apps/br209776) в поле со списком индекс дорожки извлекается из свойства **Tag** выбранного элемента. Если установить значение свойства [**SelectedIndex**](https://msdn.microsoft.com/library/windows/apps/dn956634) для списка [**VideoTracks**](https://msdn.microsoft.com/library/windows/apps/dn930953) элемента воспроизведения мультимедиа, то **MediaElement** или **MediaPlayer** изменят активную видеодорожку на дорожку с указанным индексом.

[!code-cs[VideoTracksSelectionChanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetVideoTracksSelectionChanged)]

Управление элементами мультимедиа с несколькими звуковыми дорожками осуществляется точно так же, как в случае с видеодорожками. Обработайте событие [**AudioTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930948), чтобы обновить пользовательский интерфейс и отобразить звуковые дорожки, имеющиеся в списке [**AudioTracks**](https://msdn.microsoft.com/library/windows/apps/dn930947) элемента воспроизведения. Когда пользователь выбирает звуковую дорожку, установите соответствующее значение для свойства [**SelectedIndex**](https://msdn.microsoft.com/library/windows/apps/dn930937) списка **AudioTracks**, чтобы **MediaElement** или **MediaPlayer** переключили активную звуковую дорожку на дорожку с указанным индексом.

[!code-xml[AudioComboBox](./code/MediaSource_RS1/cs/MainPage.xaml#SnippetAudioComboBox)]

[!code-cs[AudioTracksChanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAudioTracksChanged)]

[!code-cs[AudioTracksSelectionChanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAudioTracksSelectionChanged)]

Помимо звуковых дорожек и видеодорожек, в объекте **MediaPlaybackItem** могут присутствовать (или же отсутствовать) объекты [**TimedMetadataTrack**](https://msdn.microsoft.com/library/windows/apps/dn956580). Синхронизированная дорожка метаданных может содержать текст субтитров или надписей, а также специальные данные, используемые в приложении. Синхронизированная дорожка метаданных содержит список подсказок, представленных объектами, которые унаследованы от [**IMediaCue**](https://msdn.microsoft.com/library/windows/apps/dn930899), например [**DataCue**](https://msdn.microsoft.com/library/windows/apps/dn930892) или [**TimedTextCue**](https://msdn.microsoft.com/library/windows/apps/dn956655). Каждая подсказка имеет время начала и длительность. Они определяют, когда подсказка должна активироваться и на какое время.

Так же, как и в случае со звуковыми дорожкам и видеодорожками, синхронизированные дорожки метаданных для элемента мультимедиа могут быть обнаружены с помощью обработки события [**TimedMetadataTracksChanged**](https://msdn.microsoft.com/library/windows/apps/dn930952) объекта **MediaPlaybackItem**. Однако пользователю может потребоваться включить несколько дорожек метаданных одновременно. Кроме того, в зависимости от сценария вашего приложения вы можете включать либо отключать дорожки метаданных автоматически, без вмешательства пользователя. В демонстрационных целях в этом примере добавляется [**ToggleButton**](https://msdn.microsoft.com/library/windows/apps/br209795) для каждой дорожки метаданных в элементе мультимедиа, чтобы пользователь мог включать либо отключать дорожку. Свойству **Tag** каждой кнопки присваивается значение индекса связанной дорожки метаданных, чтобы при нажатии кнопки дорожку можно было идентифицировать.

[!code-xml[MetaStackPanel](./code/MediaSource_RS1/cs/MainPage.xaml#SnippetMetaStackPanel)]

[!code-cs[TimedMetadataTrackschanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetTimedMetadataTrackschanged)]

Так как одновременно могут быть активны несколько дорожек метаданных, нельзя просто установить активный индекс для списка дорожек метаданных. Вместо этого вызовите метод [**SetPresentationMode**](https://msdn.microsoft.com/library/windows/apps/dn986977) объекта **MediaPlaybackItem**, передав ему индекс дорожки, на которую нужно переключиться, а затем укажите значение из перечисления [**TimedMetadataTrackPresentationMode**](https://msdn.microsoft.com/library/windows/apps/dn987016). Режим презентации, который вы выбираете, зависит от реализации приложения. В следующем примере при включении дорожке метаданных присваивается значение **PlatformPresented**. Для текстовых дорожек это означает, что система будет автоматически показывать текстовые подсказки в дорожке. При выключении переключателя режим презентации **отключается**, то есть текст не отображается и события подсказки не вызываются. События подсказок будут рассмотрены в этой статье ниже.

[!code-cs[ToggleChecked](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetToggleChecked)]

[!code-cs[ToggleUnchecked](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetToggleUnchecked)]

При обработке дорожек метаданных можно получить доступ к набору подсказок, находящихся в дорожке, с помощью свойств [**Cues**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.TimedMetadataTrack.Cues) или [**ActiveCues**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.TimedMetadataTrack.ActiveCues). Это можно сделать, чтобы обновить ваш пользовательский интерфейс и отображать в нем расположения подсказок для элемента мультимедиа.

## <a name="handle-unsupported-codecs-and-unknown-errors-when-opening-media-items"></a>Обработка неподдерживаемых кодеков и неизвестных ошибок при открытии элементов мультимедиа
Начиная c Windows10 версии 1607, можно проверить, поддерживается ли кодек, необходимый для воспроизведения элемента мультимедиа, полностью или частично на устройстве, на котором выполняется приложение. В обработчике событий для событий изменения дорожек **MediaPlaybackItem**, таких как [**AudioTracksChanged**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.AudioTracksChanged), сначала проверьте, не является ли изменение дорожки вставкой новой дорожки. Если это так, то можно получить ссылку на вставляемую дорожку, использовав индекс, переданный в параметре **IVectorChangedEventArgs.Index** с подходящей коллекцией дорожек из параметра **MediaPlaybackItem**, например, с коллекцией [**AudioTracks**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.AudioTracks).

Получив ссылку на вставляемую дорожку, проверьте значение [**DecoderStatus**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrackSupportInfo.DecoderStatus) свойства [**SupportInfo**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrack.SupportInfo) дорожки. Если установлено значение [**FullySupported**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaDecoderStatus), то соответствующий кодек, необходимый для воспроизведения дорожки, присутствует на устройстве. Если установлено значение [**Degraded**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaDecoderStatus), дорожку можно воспроизвести в системе, однако качество воспроизведения так или иначе ухудшится. Например, звуковая дорожка 5.1 может воспроизводиться как 2-канальное стерео. В этом случае имеет смысл обновить свой пользовательский интерфейс, чтобы оповестить пользователя об ухудшении качества воспроизведения. Если установлено значение [**UnsupportedSubtype**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaDecoderStatus) или [**UnsupportedEncoderProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.MediaDecoderStatus), то с установленными на устройстве кодеками воспроизвести дорожку невозможно. Возможно, следует предупредить пользователя и пропустить воспроизведение элемента или внедрить пользовательский интерфейс, позволяющий загрузить нужный кодек. Метод [**GetEncodingProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrack.GetEncodingProperties) дорожки можно использовать для определения требуемого кодека для воспроизведения.

Наконец, можно зарегистрироваться для события дорожки [**OpenFailed**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrack.OpenFailed), которое будет возникать, если дорожка поддерживается на устройстве, но открыть ее не удалось из-за неизвестной ошибки в конвейере.

[!code-cs[AudioTracksChanged_CodecCheck](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAudioTracksChanged_CodecCheck)]

В обработчике событий [**OpenFailed**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Core.AudioTrack.OpenFailed) можно проверить, известен ли статус **MediaSource**. Если статус неизвестен, можно программными средствами выбрать другую дорожку для воспроизведения, разрешить пользователю выбрать другую дорожку или отказаться от воспроизведения.

[!code-cs[OpenFailed](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetOpenFailed)]

## <a name="set-display-properties-used-by-the-system-media-transport-controls"></a>Задание свойств отображения, используемых системными элементами управления транспортировкой мультимедиа
Начиная с Windows 10 версии 1607 мультимедиа, воспроизводимые в классе [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer), автоматически интегрируются в системные элементы управления транспортировкой мультимедиа (SMTC) по умолчанию. Можно задать метаданные, которые будут отображаться в SMTC, обновив свойства воспроизведения для класса **MediaPlaybackItem**. Получите объект, представляющий свойства отображения для элемента, вызвав метод [**GetDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItem.GetDisplayProperties). Укажите, относится ли элемент воспроизведения к музыке или видео, задав свойство [**Тип**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.Type). Затем задайте значения свойств объекта [**VideoProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.VideoProperties) или [**MusicProperties**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaItemDisplayProperties.MusicProperties). Вызовите метод [**ApplyDisplayProperties**](https://msdn.microsoft.com/library/windows/apps/mt489923) для обновления свойств элемента согласно предоставленным вами значениям. Обычно приложение извлекает значения отображения из веб-службы динамически, но в следующем примере показано извлечение со значениями, заданными в коде.

[!code-cs[SetVideoProperties](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetSetVideoProperties)]

[!code-cs[SetMusicProperties](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetSetMusicProperties)]

## <a name="add-external-timed-text-with-timedtextsource"></a>Добавление внешнего синхронизированного текста с помощью класса TimedTextSource

В некоторых случаях у вас могут быть внешние файлы, содержащие синхронизированный текст, который связан с элементом мультимедиа (например, отдельные файлы с субтитрами на разных языках). Для загрузки внешних файлов с синхронизированным текстом из потока или URI используйте класс [**TimedTextSource**](https://msdn.microsoft.com/library/windows/apps/dn956679).

В этом примере в коллекции **Dictionary** хранится список источников синхронизированного текста для элемента мультимедиа. URI источника и объект **TimedTextSource** используются в качестве пары "ключ/значение", чтобы идентифицировать дорожки после их обработки.

[!code-cs[TimedTextSourceMap](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetTimedTextSourceMap)]

Создайте объект **TimedTextSource** для каждого внешнего файла с синхронизированным текстом, вызвав метод [**CreateFromUri**](https://msdn.microsoft.com/library/windows/apps/dn708190). Добавьте запись в коллекцию **Dictionary** для источника синхронизированного текста. Добавьте обработчик для события [**TimedTextSource.Resolved**](https://msdn.microsoft.com/library/windows/apps/dn965540), чтобы обрабатывать ситуации, когда элемент не удалось загрузить либо, устанавливать для него дополнительные свойства после успешной загрузки.

Зарегистрируйте все объекты **TimedTextSource** в **MediaSource**, добавив их в коллекцию [**ExternalTimedTextSources**](https://msdn.microsoft.com/library/windows/apps/dn930916). Обратите внимание, что внешние источники синхронизированного текста добавляются напрямую в **MediaSource**, а не в **MediaPlaybackItem**, созданный из источника. Чтобы отразить в пользовательском интерфейсе внешние текстовые дорожки, зарегистрируйте и обработайте событие **TimedMetadataTracksChanged**, как было описано выше.

[!code-cs[TimedTextSource](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetTimedTextSource)]

В обработчике события [**TimedTextSource.Resolved**](https://msdn.microsoft.com/library/windows/apps/dn965540) проверьте свойство **Error** класса [**TimedTextSourceResolveResultEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn965537), передаваемое обработчику, чтобы определить, не произошла ли в процессе загрузки синхронизированных текстовых данных ошибка. Если элемент был успешно найден, то этот обработчик можно использовать для обновления дополнительных свойств найденной дорожки. В этом примере для каждой дорожки добавляется метка на основе URI, сохраненного ранее в **Dictionary**.

[!code-cs[TimedTextSourceResolved](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetTimedTextSourceResolved)]

## <a name="add-additional-metadata-tracks"></a>Добавление дополнительных дорожек метаданных

Вы можете динамически создавать пользовательские дорожки метаданных в коде и привязывать их к источнику мультимедиа. Создаваемые дорожки могут содержать текст субтитров или надписей либо специальные данные приложения.

Создайте объект [**TimedMetadataTrack**](https://msdn.microsoft.com/library/windows/apps/dn956580), вызвав конструктор и указав идентификатор, идентификатор языка и значение из перечисления [**TimedMetadataKind**](https://msdn.microsoft.com/library/windows/apps/dn956578). Зарегистрируйте обработчики для событий [**CueEntered**](https://msdn.microsoft.com/library/windows/apps/dn956583) и [**CueExited**](https://msdn.microsoft.com/library/windows/apps/dn956584). Эти события возникают, когда достигнуто время начала подсказки и когда время действия подсказки истекло.

Создайте новый объект подсказки, подходящий для типа созданной дорожки метаданных, и задайте идентификатор, время начала и длительность дорожки. В этом примере создается дорожка данных, поэтому формируется набор объектов [**DataCue**](https://msdn.microsoft.com/library/windows/apps/dn930892) и для каждой подсказки предоставляется буфер, содержащий специальные данные приложения. Чтобы зарегистрировать новую дорожку, добавьте ее в коллекцию [**ExternalTimedMetadataTracks**](https://msdn.microsoft.com/library/windows/apps/dn930915) объекта **MediaSource**.

Начиная с Windows 10 версии 1703, свойство **DataCue.Properties** предоставляет набор [**PropertySet**](https://docs.microsoft.com/uwp/api/windows.foundation.collections.propertyset), который служит для хранения пользовательских свойств в парах "ключ/данные", извлекаемых из событий **CueEntered** и **CueExited**.  

[!code-cs[AddDataTrack](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAddDataTrack)]

Событие **CueEntered** инициируется, когда достигнуто время начала подсказки при условии, что связанная дорожка имеет режим представления **ApplicationPresented**, **Hidden** или **PlatformPresented.** События подсказки не вызываются для дорожек метаданных в режиме отображения **Disabled**. Следующий пример просто выводит пользовательские данные, связанные с подсказкой, в окно отладки.

[!code-cs[DataCueEntered](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetDataCueEntered)]

В следующем примере для добавления пользовательской текстовой дорожки при создании дорожки указывается значение **TimedMetadataKind**, а объекты [**TimedTextCue**](https://msdn.microsoft.com/library/windows/apps/dn956655) используются для добавления подсказок к дорожке.

[!code-cs[AddTextTrack](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAddTextTrack)]

[!code-cs[TextCueEntered](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetTextCueEntered)]

## <a name="play-a-list-of-media-items-with-mediaplaybacklist"></a>Воспроизведение списка элементов мультимедиа с помощью MediaPlaybackList

Класс [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/dn930955) позволяет создать список воспроизведения элементов мультимедиа, которые представлены объектами **MediaPlaybackItem**.

**Примечание**элементов в [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/dn930955) обрабатываются с помощью непрерывного воспроизведения. Система будет использовать предоставленные метаданные в файлах, закодированных в формате MP3 или AAC, чтобы определить задержку или компенсацию заполнения, необходимую для непрерывного воспроизведения. Если файлы, закодированные в формате MP3 или AAC не предоставляют этих данных, система определяет задержку и заполнение эвристически. В случае форматов без потери качества, таких как PCM, FLAC или ALAC система не предпринимает никаких действий, поскольку эти кодировщики не создают задержки или заполнения.

Сначала объявите переменную для хранения объекта **MediaPlaybackList**.

[!code-cs[DeclareMediaPlaybackList](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetDeclareMediaPlaybackList)]

Создайте объект **MediaPlaybackItem** для каждого элемента мультимедиа, который нужно добавить в список, с помощью процедуры, описанной выше. Инициализируйте объект **MediaPlaybackList** и добавьте в него элементы воспроизведения мультимедиа. Зарегистрируйте обработчик события [**CurrentItemChanged**](https://msdn.microsoft.com/library/windows/apps/dn930957). Это событие позволяет обновить пользовательский интерфейс, чтобы отобразился элемент мультимедиа, который воспроизводится в данный момент. Также можно зарегистрировать событие [ItemOpened](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackList.ItemOpened), которое возникает при успешном открытии элемента в списке, и событие [ItemFailed](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackList.ItemFailed), которое возникает, когда не удается открыть элемент в списке.

Начиная с Windows 10 версии 1703, можно указать максимальное количество объектов **MediaPlaybackItem** в списке **MediaPlaybackList**. Этот список остается открытым после воспроизведения всех элементов, если задано свойство [MaxPlayedItemsToKeepOpen](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackList.MaxPlayedItemsToKeepOpen). Когда список **MediaPlaybackItem** остается открытым, воспроизведение элемента начинается мгновенно, как только пользователь переходит к этому элементу. Это связано с тем, что элемент не требуется загружать повторно. Тем не менее, при открытом списке элементов приложение потребляет больше ресурсов памяти. Значение свойства нужно подбирать так, чтобы сохранялся баланс между потреблением памяти и быстродействием. 

Чтобы включить воспроизведение списка, выберите в качестве источника воспроизведения для **MediaPlayer** ваш список **MediaPlaybackList**.

[!code-cs[PlayMediaPlaybackList](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetPlayMediaPlaybackList)]

В обработчике события **CurrentItemChanged** обновите пользовательский интерфейс, чтобы отобразить воспроизводимый в настоящее время элемент, который можно получить с помощью свойства [**NewItem**](https://msdn.microsoft.com/library/windows/apps/dn930930) объекта [**CurrentMediaPlaybackItemChangedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn930929), переданного данному событию. Помните, что если вы обновляете пользовательский интерфейс с помощью этого события, необходимо делать это в рамках вызова [**CoreDispatcher.RunAsync**](https://msdn.microsoft.com/library/windows/apps/hh750317), чтобы эти обновления выполнялись в потоке пользовательского интерфейса.

Начиная с Windows 10 версии 1703, можно получить значение свойства [CurrentMediaPlaybackItemChangedEventArgs.Reason](https://docs.microsoft.com/uwp/api/windows.media.playback.currentmediaplaybackitemchangedeventargs.Reason), которое указывает причину изменения элемента (например, программное переключение элементов приложением, завершение воспроизведения предыдущего элемента или ошибка).

[!code-cs[MediaPlaybackListItemChanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetMediaPlaybackListItemChanged)]


Вызовите метод [**MovePrevious**](https://msdn.microsoft.com/library/windows/apps/mt146455) или [**MoveNext**](https://msdn.microsoft.com/library/windows/apps/mt146454), чтобы проигрыватель мультимедиа воспроизвел предыдущий или следующий элемент в списке **MediaPlaybackList**.

[!code-cs[PrevButton](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetPrevButton)]

[!code-cs[NextButton](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetNextButton)]

Значение свойства [**ShuffleEnabled**](https://msdn.microsoft.com/library/windows/apps/mt146457) определяет, должен ли проигрыватель мультимедиа воспроизводить элементы списка в случайном порядке.

[!code-cs[ShuffleButton](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetShuffleButton)]

Значение свойства [**AutoRepeatEnabled**](https://msdn.microsoft.com/library/windows/apps/mt146452) определяет, должен ли проигрыватель мультимедиа воспроизводить элементы списка циклически.

[!code-cs[RepeatButton](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetRepeatButton)]


### <a name="handle-the-failure-of-media-items-in-a-playback-list"></a>Обработка сбоев элементов мультимедиа в списке воспроизведения
Событие [**ItemFailed**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackList.ItemFailed) возникает, если не удается открыть элемент в списке. Свойство [**ErrorCode**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItemError.ErrorCode) объекта [**MediaPlaybackItemError**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlaybackItemError), переданное в обработчик, по возможности перечисляет конкретные причины сбоя, включая сетевые ошибки, ошибки декодирования и шифрования.

[!code-cs[ItemFailed](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetItemFailed)]

### <a name="disable-playback-of-items-in-a-playback-list"></a>Отключение воспроизведения элементов в списке воспроизведения
Начиная с Windows 10 версии 1703, можно отключить воспроизведение одного или нескольких элементов в списке **MediaPlaybackItemList**, установив для свойства [IsDisabledInPlaybackList](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackItem.IsDisabledInPlaybackList) в [MediaPlaybackItem](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackItem) значение false. 

Типичный сценарий для этой функции— приложения, воспроизводящие потоковую музыку из Интернета. Приложение может отслеживать изменения состояния сетевого подключения на устройстве и отключать воспроизведение элементов, которые загружены не полностью. В следующем примере регистрируется обработчик для события [NetworkInformation.NetworkStatusChanged](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkInformation.NetworkStatusChanged).

[!code-cs[RegisterNetworkStatusChanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetRegisterNetworkStatusChanged)]

В обработчике для события **NetworkStatusChanged** необходимо проверить, возвращает ли [GetInternetConnectionProfile](https://docs.microsoft.com/uwp/api/Windows.Networking.Connectivity.NetworkInformation.GetInternetConnectionProfile) значение null, которое указывает на отсутствие подключения к сети. Если это так, циклически переберите все элементы в списке воспроизведения. Если значение параметра [TotalDownloadProgress](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybackitem.TotalDownloadProgress) для элемента меньше 1, это означает, что элемент загружен не полностью и его следует отключить. Если сетевое подключение установлено, циклически переберите все элементы в списке воспроизведения и включите каждый элемент.

[!code-cs[NetworkStatusChanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetNetworkStatusChanged)]

### <a name="defer-binding-of-media-content-for-items-in-a-playback-list-by-using-mediabinder"></a>Отложенная привязка мультимедийного содержимого для элементов в списке воспроизведения с помощью MediaBinder
В предыдущих примерах на основе файла, URL-адреса или потока создается источник **MediaSource**. Затем создается элемент **MediaPlaybackItem**, который добавляется в список **MediaPlaybackList**. В некоторых сценариях, например, когда с пользователя взимается плата за просмотр содержимого, можно отложить получение содержимого из источника **MediaSource** до момента, когда элемент в списке воспроизведения будет полностью готов. Чтобы реализовать этот сценарий, создайте экземпляр класса [**MediaBinder**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaBinder). Задайте для свойства [**Token**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaBinder.Token) строку приложения, определяющую отложенное содержимое, затем зарегистрируйте обработчик события [**Binding**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaBinder.Binding). Далее создайте источник **MediaSource** на основе **Binder**, вызвав метод [**MediaSource.CreateFromMediaBinder**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.createfrommediabinder). Затем создайте элемент **MediaPlaybackItem** на основе **MediaSource** и добавьте его в список воспроизведения обычным способом.

[!code-cs[InitMediaBinder](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetInitMediaBinder)]

Когда система определяет, что необходимо получить содержимое, связанное с **MediaBinder**, возникает событие **Binding**. В обработчике для этого события можно получить экземпляр **MediaBinder** из класса [**MediaBindingEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs), передаваемого в событие. Получите строку, указанную в свойстве **Token**, и используйте ее для определения извлекаемого содержимого. Класс **MediaBindingEventArgs** предоставляет методы для настройки связанного содержимого в нескольких разных представлениях, включая [**SetStorageFile**](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs.setstoragefile), [**SetStream**](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs.setstream), [**SetStreamReference**](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs.setstreamreference) и [**SetUri**](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs.seturi). 

[!code-cs[BinderBinding](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetBinderBinding)]

Обратите внимание: при выполнении асинхронных операций, таких как веб-запросы, обработчик события **Binding** должен вызвать метод [**MediaBindingEventArgs.GetDeferral**](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs.GetDeferral). Этот метод дает системе инструкцию ожидать завершения операции. Вызовите метод [**Deferral.Complete**](https://docs.microsoft.com/uwp/api/windows.foundation.deferral.Complete) после завершения операции, чтобы система продолжила работу.

Начиная с Windows 10 версии 1703, можно предоставить [**AdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource) в качестве связанного содержимого, вызвав метод [**SetAdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs.setadaptivemediasource). Дополнительные сведения об использовании адаптивной потоковой передачи в приложении см. в разделе [Адаптивная потоковая передача](adaptive-streaming.md).



## <a name="related-topics"></a>Статьи по теме
* [Воспроизведение мультимедиа](media-playback.md)
* [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Интеграция с системными элементами управления транспортировкой мультимедиа](integrate-with-systemmediatransportcontrols.md)
* [Воспроизведение мультимедиа в фоновом режиме](background-audio.md)

