---
author: drewbatgit
ms.assetid: AE98C22B-A071-4206-ABBB-C0F0FB7EF33C
description: В этой статье описано, как добавить функцию воспроизведения мультимедийного содержимого адаптивной потоковой передачи в приложения универсальной платформы Windows (UWP). В настоящее время эта функция поддерживает воспроизведение содержимого Http Live Streaming (HLS) и Dynamic Adaptive Streaming over HTTP (DASH).
title: Адаптивная потоковая передача
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ef8e3ab4abd9ee9159dc7d5aa757f55e00817a51
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7563819"
---
# <a name="adaptive-streaming"></a>Адаптивная потоковая передача


В этой статье описано, как добавить функцию воспроизведения мультимедийного содержимого адаптивной потоковой передачи в приложения универсальной платформы Windows (UWP). Эта функция поддерживает воспроизведение содержимого Http Live Streaming (HLS) и Dynamic Adaptive Streaming over HTTP (DASH). Начиная с Windows 10 версии 1803 Smooth Streaming поддерживается в **[AdaptiveMediaSource](https://docs.microsoft.com/uwp/api/Windows.Media.Streaming.Adaptive.AdaptiveMediaSource)**.

Список поддерживаемых тегов протокола HLS см. в разделе [Поддержка тегов HLS](hls-tag-support.md). 

Список поддерживаемых профилей DASH см. в разделе [Поддержка профилей DASH](dash-profile-support.md). 

> [!NOTE] 
> В этой статье используется код, адаптированный из [примера адаптивной потоковой передачи](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/AdaptiveStreaming).

## <a name="simple-adaptive-streaming-with-mediaplayer-and-mediaplayerelement"></a>Простая адаптивная потоковая передача с использованием MediaPlayer и MediaPlayerElement

Для потокового воспроизведения адаптивного мультимедиа в приложении UWP создайте объект **Uri**, указывающий на файл манифеста DASH или HLS. Создайте экземпляр класса [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer). Вызовите [**MediaSource.CreateFromUri**](https://msdn.microsoft.com/library/windows/apps/dn930912), чтобы создать объект **MediaSource**, и задайте его в качестве значения свойства [**Source**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Source) объекта **MediaPlayer**. Вызовите [**Play**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Play), чтобы начать воспроизведение мультимедиа-содержимого.

[!code-cs[DeclareMediaPlayer](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetDeclareMediaPlayer)]

[!code-cs[ManifestSourceNoUI](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetManifestSourceNoUI)]

Приведенный выше пример воспроизводит аудио из мультимедиа-содержимого, но не отображает автоматически содержимое в пользовательском интерфейсе. Большинству приложений, которые воспроизводят видео, требуется отображать содержимое на странице XAML.  Для этого добавьте элемент управления [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement) на страницу XAML.

[!code-xml[MediaPlayerElementXAML](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml#SnippetMediaPlayerElementXAML)]

Вызовите метод [**MediaSource.CreateFromUri**](https://msdn.microsoft.com/library/windows/apps/dn930912), чтобы создать **MediaSource** на основе URI файла манифеста DASH или HLS. Затем задайте значение свойства [**Source**](https://msdn.microsoft.com/library/windows/apps/br227420) класса **MediaPlayerElement**. **MediaPlayerElement** автоматически создаст объект **MediaPlayer** для содержимого. Вы можете вызвать метод **Play** объекта **MediaPlayer** для воспроизведения содержимого.

[!code-cs[ManifestSource](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetManifestSource)]

> [!NOTE] 
> Начиная с Windows 10 версии 1607 рекомендуется использовать класс **MediaPlayer**, чтобы воспроизводить элементы мультимедиа. **MediaPlayerElement** — это облегченный элемент управления XAML, который используется для отображения содержимого **MediaPlayer** на странице XAML. Элемент управления **MediaElement** продолжает поддерживаться для обратной совместимости. Дополнительные сведения об использовании **MediaPlayer** и **MediaPlayerElement** для воспроизведения мультимедиа см. в разделе [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md). Сведения об использовании **MediaSource** и связанных API для работы с мультимедиа см. в разделе [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md).

## <a name="adaptive-streaming-with-adaptivemediasource"></a>Адаптивная потоковая передача с помощью AdaptiveMediaSource

Если вашему приложению требуются более сложные функции адаптивной потоковой передачи (например, предоставление настраиваемых заголовков HTTP, контроль текущей скорости скачивания и воспроизведения или настройка коэффициентов, которые определяют, когда система переключает скорости передачи адаптивного потока), используйте объект **[AdaptiveMediaSource](https://docs.microsoft.com/uwp/api/Windows.Media.Streaming.Adaptive.AdaptiveMediaSource)**.

Интерфейсы API адаптивной потоковой передачи можно найти в пространстве имен [**Windows.Media.Streaming.Adaptive**](https://msdn.microsoft.com/library/windows/apps/dn931279). В примерах в этой статьи используются API-интерфейсы из следующих пространств имен.

[!code-cs[AdaptiveStreamingUsing](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAdaptiveStreamingUsing)]

## <a name="initialize-an-adaptivemediasource-from-a-uri"></a>Инициализация объекта AdaptiveMediaSource с помощью URI.

Инициализируйте объект **AdaptiveMediaSource**, воспользовавшись URI файла манифеста адаптивной потоковой передачи и вызвав метод [**CreateFromUriAsync**](https://msdn.microsoft.com/library/windows/apps/dn931261). Значение [**AdaptiveMediaSourceCreationStatus**](https://msdn.microsoft.com/library/windows/apps/dn946917), возвращенное этим методом, позволяет узнать, был ли источник мультимедиа создан успешно. Если был, можно задать объект в качестве источника потока для **MediaPlayer** путем создания объекта **MediaSource**, вызвав метод [**MediaSource.CreateFromAdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaSource.AdaptiveMediaSource) и затем назначив его свойству [**Source**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer.Source) проигрывателя мультимедиа. В этом примере свойство [**AvailableBitrates**](https://msdn.microsoft.com/library/windows/apps/dn931257) запрашивается, чтобы определить максимальную поддерживаемую скорость передачи для данного потока, а затем установить это значение в качестве начальной скорости. В этом примере также показана регистрация нескольких событий **AdaptiveMediaSource**, которые описываются далее в этой статье.

[!code-cs[InitializeAMS](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetInitializeAMS)]

## <a name="initialize-an-adaptivemediasource-using-httpclient"></a>Инициализация объекта AdaptiveMediaSource с помощью HttpClient

Если необходимо задать настраиваемые заголовки HTTP для получения файла манифеста, можно создать объект [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639), задать необходимые заголовки, а затем передать этот объект в перегрузку **CreateFromUriAsync**.

[!code-cs[InitializeAMSWithHttpClient](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetInitializeAMSWithHttpClient)]

Событие [**DownloadRequested**](https://msdn.microsoft.com/library/windows/apps/dn931272) возникает, когда система собирается получить ресурс от сервера. Класс [**AdaptiveMediaSourceDownloadRequestedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn946935), переданный в обработчик событий, представляет свойства, которые предоставляют запрошенные сведения о ресурсе (например, тип и URI ресурса).

## <a name="modify-resource-request-properties-using-the-downloadrequested-event"></a>Изменение свойства запроса ресурса с помощью события DownloadRequested

Обработчик событий **DownloadRequested** можно использовать для изменения запроса ресурса путем обновления свойств объекта [**AdaptiveMediaSourceDownloadResult**](https://msdn.microsoft.com/library/windows/apps/dn946942), предоставленных аргументами события. В приведенном ниже примере URI, из которого будет получен ресурс, изменяется путем обновления свойств [**ResourceUri**](https://msdn.microsoft.com/library/windows/apps/dn931250) объекта результата. Можно также переписать смещение байтового диапазона и длину сегментов мультимедиа или, как показано в примере ниже, изменить URI ресурса, чтобы загрузить полный ресурс и задать для смещения байтового диапазона и длины значение null.

Можно переопределить содержимое запрошенного ресурса, задав свойства [**Buffer**](https://msdn.microsoft.com/library/windows/apps/dn946943) или [**InputStream**](https://msdn.microsoft.com/library/windows/apps/dn931249) объекта результата. В приведенном ниже примере содержимое ресурса манифеста заменено путем присвоения значения свойству **Buffer**. Обратите внимание, что при обновлении запроса ресурса данными, которые получены асинхронно (например, данные, извлеченные с удаленного сервера или в результате асинхронной проверки подлинности пользователя), следует вызвать метод [**AdaptiveMediaSourceDownloadRequestedEventArgs.GetDeferral**](https://msdn.microsoft.com/library/windows/apps/dn946936), чтобы получить отсрочку, а затем вызвать метод [**Complete**](https://msdn.microsoft.com/library/windows/apps/dn946934), когда операция завершится, чтобы сообщить системе, что операция запроса на загрузку может продолжаться.

[!code-cs[AMSDownloadRequested](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAMSDownloadRequested)]

## <a name="use-bitrate-events-to-manage-and-respond-to-bitrate-changes"></a>Использование событий скорости передачи для управления и реагирования на изменение скорости

Объект **AdaptiveMediaSource** предоставляет события, которые позволяют реагировать, если скорость скачивания или воспроизведения меняется. В этом примере текущие скорости просто обновляются в пользовательском интерфейсе. Обратите внимание: можно изменять коэффициенты, определяющие, когда система изменяет скорость адаптивного потока. Дополнительные сведения см. в описании свойства [**AdvancedSettings**](https://msdn.microsoft.com/library/windows/apps/mt628697).

[!code-cs[AMSBitrateEvents](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAMSBitrateEvents)]

## <a name="handle-download-completion-and-failure-events"></a>Обработка событий завершения скачивания и сбоев
Объект **AdaptiveMediaSource** вызывает событие [**DownloadFailed**](https://docs.microsoft.com/uwp/api/Windows.Media.Streaming.Adaptive.AdaptiveMediaSource.DownloadFailed), если происходит сбой скачивания запрашиваемого ресурса. Это событие можно использовать для обновления пользовательского интерфейса в ответ на сбой. Можно также использовать событие для записи статистических данных об операции скачивания и сбое. 

Объект [**AdaptiveMediaSourceDownloadFailedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.Media.Streaming.Adaptive.AdaptiveMediaSourceDownloadFailedEventArgs), переданный в обработчик событий, содержит метаданные о сбое скачивания ресурса, такие как тип ресурса, URI ресурса и положение в потоке, где произошел сбой. Объект [**RequestId**](https://docs.microsoft.com/uwp/api/Windows.Media.Streaming.Adaptive.AdaptiveMediaSourceDownloadFailedEventArgs.RequestId) получает созданный системой уникальный идентификатор для запроса, который можно использовать, чтобы соотнести сведения об отдельном запросе в нескольких событиях.

Свойство [**Statistics**](https://docs.microsoft.com/uwp/api/Windows.Media.Streaming.Adaptive.AdaptiveMediaSourceDownloadFailedEventArgs.Statistics) возвращает объект [**AdaptiveMediaSourceDownloadStatistics**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasourcedownloadstatistics), который содержит подробные сведения о количестве байтов, полученных во время события, и время различных этапов операции скачивания. Эти сведения можно регистрировать для выявления проблем производительности в реализации адаптивной потоковой передачи.

[!code-cs[AMSDownloadFailed](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAMSDownloadFailed)]


Событие [**DownloadCompleted**](https://docs.microsoft.com/uwp/api/Windows.Media.Streaming.Adaptive.AdaptiveMediaSource.DownloadCompleted) происходит после завершения скачивания ресурсов и предоставляет аналогичные данные для события **DownloadFailed**. Команда **RequestId** также предоставляется для сопоставления событий для одного запроса. Кроме того, предоставляется объект **AdaptiveMediaSourceDownloadStatistics**, чтобы была возможность вести журнал статистики скачивания.

[!code-cs[AMSDownloadCompleted](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAMSDownloadCompleted)]

## <a name="gather-adaptive-streaming-telemetry-data-with-adaptivemediasourcediagnostics"></a>Сбор телеметрических данных адаптивной потоковой передачи с помощью AdaptiveMediaSourceDiagnostics
**AdaptiveMediaSource** предоставляет свойство [**Diagnostics**](https://docs.microsoft.com/uwp/api/Windows.Media.Streaming.Adaptive.AdaptiveMediaSource?branch=master.Diagnostics), которое возвращает объект [**AdaptiveMediaSourceDiagnostics**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasourcediagnostics). Этот объект используется для регистрации события [**DiagnosticAvailable**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasourcediagnostics.DiagnosticAvailable). Это событие предназначено для использования для сбора телеметрии и не должно использоваться для изменения поведения приложения во время выполнения. Это событие диагностики создается по разным причинам. Смотрите свойства [**DiagnosticType**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasourcediagnosticavailableeventargs.DiagnosticType) объекта [**AdaptiveMediaSourceDiagnosticAvailableEventArgs**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasourcediagnosticavailableeventargs), переданного с событием, чтобы определить причину возникновения последнего. К возможным причинам относятся ошибки доступа к запрашиваемому ресурсу и ошибки анализа файла манифеста потоковой передачи. Список ситуаций, которые могут инициировать событие диагностики, см. в разделе [**AdaptiveMediaSourceDiagnosticType**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasourcediagnostictype). Как и аргументы для других событий адаптивной потоковой передачи, **AdaptiveMediaSourceDiagnosticAvailableEventArgs** предоставляет свойство **RequestId** для сопоставления сведений о запросе между различными событиями.

[!code-cs[AMSDiagnosticAvailable](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAMSDiagnosticAvailable)]

## <a name="defer-binding-of-adaptive-streaming-content-for-items-in-a-playback-list-by-using-mediabinder"></a>Откладывание привязки содержимого адаптивной потоковой передачи для элементов в списке воспроизведения с помощью MediaBinder
Класс [**MediaBinder**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaBinder) позволяет откладывать привязку мультимедийного содержимого в [**MediaPlaybackList**](https://msdn.microsoft.com/library/windows/apps/dn930955). Начиная с Windows 10 версии 1703, можно предоставить [**AdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource) как связанное содержимое. Процесс отложенной привязки источника адаптивных мультимедиа в значительной степени аналогичен привязке других типов мультимедиа, описанной в разделе [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md). 

Создайте экземпляр **MediaBinder**, установите определяемую приложением строку [**Token**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaBinder.Token), чтобы определить содержимое для привязки, и зарегистрируйте для события [**Binding**](https://docs.microsoft.com/uwp/api/Windows.Media.Core.MediaBinder.Binding). Создайте **MediaSource** из **Binder**, вызвав метод [**MediaSource.CreateFromMediaBinder**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.createfrommediabinder). Затем создайте **MediaPlaybackItem** из **MediaSource** и добавьте его в список воспроизведения.

[!code-cs[InitMediaBinder](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetInitMediaBinder)]

В обработчике событий **Binding** используйте строку токена для определения содержимого для привязки, а затем создайте источник адаптивных мультимедиа , вызвав одну из перегрузок **[CreateFromStreamAsync](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource.createfromstreamasync)** или **[CreateFromUriAsync](https://docs.microsoft.com/uwp/api/windows.media.streaming.adaptive.adaptivemediasource.createfromuriasync)**. Поскольку это асинхронные методы, необходимо сначала вызвать метод [**MediaBindingEventArgs.GetDeferral**](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs.GetDeferral), чтобы указать системе ожидать операции завершения перед продолжением.  Задайте источник адаптивных мультимедиа как связанного содержимого, вызвав метод **[SetAdaptiveMediaSource](https://docs.microsoft.com/uwp/api/windows.media.core.mediabindingeventargs.setadaptivemediasource)**. Наконец вызовите [**Deferral.Complete**](https://docs.microsoft.com/uwp/api/windows.foundation.deferral.Complete) после завершения операции, чтобы указать системе продолжать.

[!code-cs[BinderBindingAMS](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetBinderBindingAMS)]

Если требуется зарегистрировать обработчики событий для источника привязанных адаптивных мультимедиа, это можно сделать в обработчике для события [**CurrentItemChanged**](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplaybacklist.CurrentItemChanged) из списка **MediaPlaybackList**. Свойство [**CurrentMediaPlaybackItemChangedEventArgs.NewItem**](https://docs.microsoft.com/uwp/api/windows.media.playback.currentmediaplaybackitemchangedeventargs.NewItem) содержит новый воспроизводящийся в текущий момент элемент **MediaPlaybackItem** в списке. Получите экземпляр **AdaptiveMediaSource**, представляющий новый элемент, обратившись к свойству [**Source**](https://docs.microsoft.com/uwp/api/Windows.Media.Playback.MediaPlaybackItem.Source) элемента **MediaPlaybackItem**, а затем к свойству [**AdaptiveMediaSource**](https://docs.microsoft.com/uwp/api/windows.media.core.mediasource.AdaptiveMediaSource) источника мультимедиа. Это свойство будет иметь значение null, если новый элемент воспроизведения не является **AdaptiveMediaSource**, поэтому необходимо проверить на значение null, прежде чем пытаться зарегистрировать обработчики для любого из событий объектов.

[!code-cs[AMSBindingCurrentItemChanged](./code/MediaSource_RS1/cs/MainPage.xaml.cs#SnippetAMSBindingCurrentItemChanged)]

## <a name="related-topics"></a>Статьи по теме
* [Воспроизведение мультимедиа](media-playback.md)
* [Поддержка тега HLS](hls-tag-support.md) 
* [Поддержка профиля Dash](dash-profile-support.md) 
* [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Воспроизведение мультимедиа в фоновом режиме](background-audio.md) 





