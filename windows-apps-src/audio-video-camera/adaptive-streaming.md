---
author: drewbatgit
ms.assetid: AE98C22B-A071-4206-ABBB-C0F0FB7EF33C
description: "В этой статье описано, как добавить функцию воспроизведения мультимедийного содержимого адаптивной потоковой передачи в приложения универсальной платформы Windows (UWP). В настоящее время эта функция поддерживает воспроизведение содержимого Http Live Streaming (HLS) и Dynamic Adaptive Streaming over HTTP (DASH)."
title: "Адаптивная потоковая передача"
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
translationtype: Human Translation
ms.sourcegitcommit: c6b64cff1bbebc8ba69bc6e03d34b69f85e798fc
ms.openlocfilehash: 3afd0440d8e552ebc3459c5fe30dd766db3ae8b9
ms.lasthandoff: 02/07/2017

---

# <a name="adaptive-streaming"></a>Адаптивная потоковая передача

\[ Обновлено для приложений UWP в Windows 10. Статьи по Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этой статье описано, как добавить функцию воспроизведения мультимедийного содержимого адаптивной потоковой передачи в приложения универсальной платформы Windows (UWP). В настоящее время эта функция поддерживает воспроизведение содержимого Http Live Streaming (HLS) и Dynamic Adaptive Streaming over HTTP (DASH).

Список поддерживаемых тегов протокола HLS см. в разделе [Поддержка тегов HLS](hls-tag-support.md). 

> [!NOTE] 
> В этой статье используется код, адаптированный из [примера адаптивной потоковой передачи](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/AdaptiveStreaming).

## <a name="simple-adaptive-streaming-with-mediaplayer-and-mediaplayerelement"></a>Простая адаптивная потоковая передача с использованием MediaPlayer и MediaPlayerElement

Для потокового воспроизведения адаптивного мультимедиа в приложении UWP создайте объект **Uri**, указывающий на файл манифеста DASH или HLS. Создайте экземпляр класса [**MediaPlayer**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer). Вызовите [**MediaSource.CreateFromUri**](https://msdn.microsoft.com/library/windows/apps/dn930912), чтобы создать объект **MediaSource**, и задайте его в качестве значения свойства [**Source**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Source) объекта **MediaPlayer**. Вызовите [**Play**](https://msdn.microsoft.com/library/windows/apps/Windows.Media.Playback.MediaPlayer.Play), чтобы начать воспроизведение мультимедиа.

[!code-cs[DeclareMediaPlayer](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetDeclareMediaPlayer)]

[!code-cs[ManifestSourceNoUI](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetManifestSourceNoUI)]

Приведенный выше пример воспроизводит звук мультимедиа, но не отображает содержимое автоматически в пользовательском интерфейсе. Большинству приложений, которые воспроизводят видео, требуется отображать содержимое на странице XAML.  Для этого добавьте элемент управления [**MediaPlayerElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.MediaPlayerElement) на страницу XAML.

[!code-xml[MediaPlayerElementXAML](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml#SnippetMediaPlayerElementXAML)]

Вызовите метод [**MediaSource.CreateFromUri**](https://msdn.microsoft.com/library/windows/apps/dn930912), чтобы создать **MediaSource** на основе URI файла манифеста DASH или HLS. Затем задайте значение свойства [**Source**](https://msdn.microsoft.com/library/windows/apps/br227420) класса **MediaPlayerElement**. **MediaPlayerElement** автоматически создаст объект **MediaPlayer** для содержимого. Вы можете вызвать метод **Play** объекта **MediaPlayer** для воспроизведения содержимого.

[!code-cs[ManifestSource](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetManifestSource)]

> [!NOTE] 
> Начиная с Windows 10 версии 1607 рекомендуется использовать класс **MediaPlayer**, чтобы воспроизводить элементы мультимедиа. **MediaPlayerElement** — это облегченный элемент управления XAML, который используется для отображения содержимого **MediaPlayer** на странице XAML. Элемент управления **MediaElement** продолжает поддерживаться для обратной совместимости. Дополнительные сведения об использовании **MediaPlayer** и **MediaPlayerElement** для воспроизведения мультимедиа см. в разделе [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md). Сведения об использовании **MediaSource** и связанных API для работы с мультимедиа см. в разделе [Элементы, списки воспроизведения и звуковые дорожки мультимедиа](media-playback-with-mediasource.md).

## <a name="adaptive-streaming-with-adaptivemediasource"></a>Адаптивная потоковая передача с помощью AdaptiveMediaSource

Если вашему приложению требуются более сложные функции адаптивной потоковой передачи (например, предоставление настраиваемых заголовков HTTP, контроль текущей скорости скачивания и воспроизведения или настройка коэффициентов, которые определяют, когда система переключает скорости передачи адаптивного потока), используйте объект [**AdaptiveMediaSource**](https://msdn.microsoft.com/library/windows/apps/dn946912).

Интерфейсы API адаптивной потоковой передачи можно найти в пространстве имен [**Windows.Media.Streaming.Adaptive**](https://msdn.microsoft.com/library/windows/apps/dn931279).

[!code-cs[AdaptiveStreamingUsing](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAdaptiveStreamingUsing)]

Инициализируйте объект **AdaptiveMediaSource**, воспользовавшись URI файла манифеста адаптивной потоковой передачи и вызвав метод [**CreateFromUriAsync**](https://msdn.microsoft.com/library/windows/apps/dn931261). Значение [**AdaptiveMediaSourceCreationStatus**](https://msdn.microsoft.com/library/windows/apps/dn946917), возвращенное этим методом, позволяет узнать, был ли источник мультимедиа создан успешно. В этом случае можно задать данный объект в качестве источника потока для элемента **MediaPlayer**, вызвав метод [**SetMediaSource**](https://msdn.microsoft.com/library/windows/apps/dn652653). В этом примере свойство [**AvailableBitrates**](https://msdn.microsoft.com/library/windows/apps/dn931257) запрашивается, чтобы определить максимальную поддерживаемую скорость передачи для данного потока, а затем установить это значение в качестве начальной скорости. В этом примере также показана регистрация обработчиков событий [**DownloadRequested**](https://msdn.microsoft.com/library/windows/apps/dn931272), [**DownloadBitrateChanged**](https://msdn.microsoft.com/library/windows/apps/dn931269) и [**PlaybackBitrateChanged**](https://msdn.microsoft.com/library/windows/apps/dn931278), которые описаны далее в этой статье.

[!code-cs[InitializeAMS](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetInitializeAMS)]

Если необходимо задать настраиваемые заголовки HTTP для получения файла манифеста, можно создать объект [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639), задать необходимые заголовки, а затем передать этот объект в перегрузку **CreateFromUriAsync**.

[!code-cs[InitializeAMSWithHttpClient](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetInitializeAMSWithHttpClient)]

Событие [**DownloadRequested**](https://msdn.microsoft.com/library/windows/apps/dn931272) возникает, когда система собирается получить ресурс от сервера. Класс [**AdaptiveMediaSourceDownloadRequestedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn946935), переданный в обработчик событий, представляет свойства, которые предоставляют запрошенные сведения о ресурсе (например, тип и URI ресурса).

Обработчик событий **DownloadRequested** можно использовать для изменения запроса ресурса путем обновления свойств объекта [**AdaptiveMediaSourceDownloadResult**](https://msdn.microsoft.com/library/windows/apps/dn946942), предоставленных аргументами события. В приведенном ниже примере URI, из которого будет получен ресурс, изменяется путем обновления свойств [**ResourceUri**](https://msdn.microsoft.com/library/windows/apps/dn931250) объекта результата.

Можно переопределить содержимое запрошенного ресурса, задав свойства [**Buffer**](https://msdn.microsoft.com/library/windows/apps/dn946943) или [**InputStream**](https://msdn.microsoft.com/library/windows/apps/dn931249) объекта результата. В приведенном ниже примере содержимое ресурса манифеста заменено путем присвоения значения свойству **Buffer**. Обратите внимание, что при обновлении запроса ресурса данными, которые получены асинхронно (например, данные, извлеченные с удаленного сервера или в результате асинхронной проверки подлинности пользователя), следует вызвать метод [**AdaptiveMediaSourceDownloadRequestedEventArgs.GetDeferral**](https://msdn.microsoft.com/library/windows/apps/dn946936), чтобы получить отсрочку, а затем вызвать метод [**Complete**](https://msdn.microsoft.com/library/windows/apps/dn946934), когда операция завершится, чтобы сообщить системе, что операция запроса на загрузку может продолжаться.

[!code-cs[AMSDownloadRequested](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAMSDownloadRequested)]

Объект **AdaptiveMediaSource** предоставляет события, которые позволяют реагировать, если скорость скачивания или воспроизведения меняется. В этом примере текущие скорости просто обновляются в пользовательском интерфейсе. Обратите внимание: можно изменять коэффициенты, определяющие, когда система изменяет скорость адаптивного потока. Дополнительные сведения см. в описании свойства [**AdvancedSettings**](https://msdn.microsoft.com/library/windows/apps/mt628697).

[!code-cs[AMSBitrateEvents](./code/AdaptiveStreaming_RS1/cs/MainPage.xaml.cs#SnippetAMSBitrateEvents)]

## <a name="related-topics"></a>Связанные статьи
* [Воспроизведение мультимедиа](media-playback.md)
* [Поддержка тега HLS](hls-tag-support.md) 
* [Воспроизведение аудио и видео с помощью MediaPlayer](play-audio-and-video-with-mediaplayer.md)
* [Воспроизведение мультимедиа в фоновом режиме](background-audio.md) 






