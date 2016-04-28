---
ms.assetid: AE98C22B-A071-4206-ABBB-C0F0FB7EF33C
description: В этой статье описано, как добавить функцию воспроизведения мультимедийного содержимого адаптивной потоковой передачи в приложения UWP. В настоящее время эта функция поддерживает воспроизведение содержимого Http Live Streaming (HLS) и Dynamic Adaptive Streaming over HTTP (DASH).
title: Адаптивная потоковая передача
---

# Адаптивная потоковая передача

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

В этой статье описано, как добавить функцию воспроизведения мультимедийного содержимого адаптивной потоковой передачи в приложения универсальной платформы Windows (UWP). В настоящее время эта функция поддерживает воспроизведение содержимого Http Live Streaming (HLS) и Dynamic Adaptive Streaming over HTTP (DASH).

## Простая адаптивная потоковая передача с помощью MediaElement

Для отображения мультимедийного содержимого адаптивной потоковой передачи в приложении на XAML добавьте на страницу элемент управления [**MediaElement**](https://msdn.microsoft.com/library/windows/apps/br242926).

[!code-xml[MediaElementXAML](./code/AdaptiveStreaming_Win10/cs/MainPage.xaml#SnippetMediaElementXAML)]

Назначьте свойству [**Source**](https://msdn.microsoft.com/library/windows/apps/br227420) элемента **MediaElement** идентификатор URI файла манифеста DASH или HLS в качестве значения.

[!code-cs[ManifestSource](./code/AdaptiveStreaming_Win10/cs/MainPage.xaml.cs#SnippetManifestSource)]

## Адаптивная потоковая передача с помощью AdaptiveMediaSource

Если вашему приложению требуются более сложные функции адаптивной потоковой передачи (например, предоставление настраиваемых заголовков HTTP, контроль текущей скорости скачивания и воспроизведения или настройка коэффициентов, которые определяют, когда система переключает скорости передачи адаптивного потока), используйте объект [**AdaptiveMediaSource**](https://msdn.microsoft.com/library/windows/apps/dn946912).

Интерфейсы API адаптивной потоковой передачи можно найти в пространстве имен [**Windows.Media.Streaming.Adaptive**](https://msdn.microsoft.com/library/windows/apps/dn931279).

[!code-cs[AdaptiveStreamingUsing](./code/AdaptiveStreaming_Win10/cs/MainPage.xaml.cs#SnippetAdaptiveStreamingUsing)]

Инициализируйте объект **AdaptiveMediaSource**, воспользовавшись URI файла манифеста адаптивной потоковой передачи и вызвав метод [**CreateFromUriAsync**](https://msdn.microsoft.com/library/windows/apps/dn931261). Значение [**AdaptiveMediaSourceCreationStatus**](https://msdn.microsoft.com/library/windows/apps/dn946917), возвращенное этим методом, позволяет узнать, был ли источник мультимедиа создан успешно. В этом случае можно задать данный объект в качестве источника потока для элемента **MediaElement**, вызвав метод [**SetMediaStreamSource**](https://msdn.microsoft.com/library/windows/apps/dn299029). В этом примере свойство [**AvailableBitrates**](https://msdn.microsoft.com/library/windows/apps/dn931257) запрашивается, чтобы определить максимальную поддерживаемую скорость передачи для данного потока, а затем установить это значение в качестве начальной скорости. В этом примере также показана регистрация обработчиков событий [**DownloadRequested**](https://msdn.microsoft.com/library/windows/apps/dn931272), [**DownloadBitrateChanged**](https://msdn.microsoft.com/library/windows/apps/dn931269) и [**PlaybackBitrateChanged**](https://msdn.microsoft.com/library/windows/apps/dn931278), которые описаны далее в этой статье.

[!code-cs[InitializeAMS](./code/AdaptiveStreaming_Win10/cs/MainPage.xaml.cs#SnippetInitializeAMS)]

Если необходимо задать настраиваемые заголовки HTTP для получения файла манифеста, можно создать объект [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639), задать необходимые заголовки, а затем передать этот объект в перегрузку **CreateFromUriAsync**.

[!code-cs[InitializeAMSWithHttpClient](./code/AdaptiveStreaming_Win10/cs/MainPage.xaml.cs#SnippetInitializeAMSWithHttpClient)]

Событие [**DownloadRequested**](https://msdn.microsoft.com/library/windows/apps/dn931272) возникает, когда система собирается получить ресурс от сервера. Класс [**AdaptiveMediaSourceDownloadRequestedEventArgs**](https://msdn.microsoft.com/library/windows/apps/dn946935), переданный в обработчик событий, представляет свойства, которые предоставляют запрошенные сведения о ресурсе (например, тип и URI ресурса).

Обработчик событий **DownloadRequested** можно использовать для изменения запроса ресурса путем обновления свойств объекта [**AdaptiveMediaSourceDownloadResult**](https://msdn.microsoft.com/library/windows/apps/dn946942), предоставленных аргументами события. В приведенном ниже примере URI, из которого будет получен ресурс, изменяется путем обновления свойств [**ResourceUri**](https://msdn.microsoft.com/library/windows/apps/dn931250) объекта результата.

Можно переопределить содержимое запрошенного ресурса, задав свойства [**Buffer**](https://msdn.microsoft.com/library/windows/apps/dn946943) или [**InputStream**](https://msdn.microsoft.com/library/windows/apps/dn931249) объекта результата. В приведенном ниже примере содержимое ресурса манифеста заменено путем присвоения значения свойству **Buffer**. Обратите внимание, что при обновлении запроса ресурса с данными, которые получены асинхронно (например, данные, извлеченные с удаленного сервера или в результате асинхронной проверки подлинности пользователя), следует вызвать метод [**AdaptiveMediaSourceDownloadRequestedEventArgs.GetDeferral**](https://msdn.microsoft.com/library/windows/apps/dn946936), чтобы получить отсрочку, а затем вызвать метод [**Complete**](https://msdn.microsoft.com/library/windows/apps/dn946934), когда операция завершится, чтобы сообщить системе, что операция запроса на загрузку может продолжаться.

[!code-cs[AMSDownloadRequested](./code/AdaptiveStreaming_Win10/cs/MainPage.xaml.cs#SnippetAMSDownloadRequested)]

Объект **AdaptiveMediaSource** предоставляет события, которые позволяют реагировать, если скорость скачивания или воспроизведения меняется. В этом примере текущие скорости просто обновляются в пользовательском интерфейсе. Обратите внимание: можно изменять коэффициенты, определяющие, когда система изменяет скорость адаптивного потока. Дополнительные сведения см. в описании свойства [**AdvancedSettings**](https://msdn.microsoft.com/library/windows/apps/mt628697).

[!code-cs[AMSBitrateEvents](./code/AdaptiveStreaming_Win10/cs/MainPage.xaml.cs#SnippetAMSBitrateEvents)]

 

 






<!--HONumber=Mar16_HO1-->


