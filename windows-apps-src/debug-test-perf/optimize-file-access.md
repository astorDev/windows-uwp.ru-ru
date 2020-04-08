---
ms.assetid: 40122343-1FE3-4160-BABE-6A2DD9AF1E8E
title: Оптимизация доступа к файлам
description: Создайте приложения универсальной платформы Windows (UWP), получающие эффективный доступ к файловой системе, избегая проблем с производительностью, связанных с задержкой диска и тактами запоминания данных в памяти и центральном процессоре.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 3114bc7a86f7f7f4d22c69c814735c146352efbd
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681955"
---
# <a name="optimize-file-access"></a>Оптимизация доступа к файлам


Создайте приложения универсальной платформы Windows (UWP), получающие эффективный доступ к файловой системе, избегая проблем с производительностью, связанных с задержкой диска и тактами запоминания данных в памяти и центральном процессоре.

Когда нужен доступ к большой коллекции файлов и требуется узнать значения свойств, отличающихся от типичных свойств Name, FileType или Path, получите к ним доступ, создав [**QueryOptions**](https://docs.microsoft.com/uwp/api/Windows.Storage.Search.QueryOptions) и вызвав [**SetPropertyPrefetch**](https://docs.microsoft.com/uwp/api/windows.storage.search.queryoptions.setpropertyprefetch). Метод **SetPropertyPrefetch** способен радикально улучшить производительность приложений, показывающих коллекцию элементов, полученных из файловой системы (например, коллекцию изображений). В следующих примерах показан ряд методов доступа к нескольким файлам.

В первом примере применяется метод [**Windows.Storage.StorageFolder.GetFilesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfilesasync) для извлечения сведений об именах для набора файлов. Такой подход обеспечивает высокое быстродействие, поскольку предоставляется доступ лишь к свойству Name.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> StorageFolder library = Windows.Storage.KnownFolders.PicturesLibrary;
> IReadOnlyList<StorageFile> files = await library.GetFilesAsync(Windows.Storage.Search.CommonFileQuery.OrderByDate);
> 
> for (int i = 0; i < files.Count; i++)
> {
>     // do something with the name of each file
>     string fileName = files[i].Name;
> }
> ```
> ```vb
> Dim library As StorageFolder = Windows.Storage.KnownFolders.PicturesLibrary
> Dim files As IReadOnlyList(Of StorageFile) =
>     Await library.GetFilesAsync(Windows.Storage.Search.CommonFileQuery.OrderByDate)
> 
> For i As Integer = 0 To files.Count - 1
>     ' do something with the name of each file
>     Dim fileName As String = files(i).Name
> Next i
> ```

Во втором примере применяется метод [**Windows.Storage.StorageFolder.GetFilesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagefolder.getfilesasync), а затем извлекаются свойства изображения для каждого файла. Этот подход отличается низким быстродействием.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> StorageFolder library = Windows.Storage.KnownFolders.PicturesLibrary;
> IReadOnlyList<StorageFile> files = await library.GetFilesAsync(Windows.Storage.Search.CommonFileQuery.OrderByDate);
> for (int i = 0; i < files.Count; i++)
> {
>     ImageProperties imgProps = await files[i].Properties.GetImagePropertiesAsync();
> 
>     // do something with the date the image was taken
>     DateTimeOffset date = imgProps.DateTaken;
> }
> ```
> ```vb
> Dim library As StorageFolder = Windows.Storage.KnownFolders.PicturesLibrary
> Dim files As IReadOnlyList(Of StorageFile) = Await library.GetFilesAsync(Windows.Storage.Search.CommonFileQuery.OrderByDate)
> For i As Integer = 0 To files.Count - 1
>     Dim imgProps As ImageProperties =
>         Await files(i).Properties.GetImagePropertiesAsync()
> 
>     ' do something with the date the image was taken
>     Dim dateTaken As DateTimeOffset = imgProps.DateTaken
> Next i
> ```

В третьем примере получение сведений о наборе файлов выполняется с помощью метода [**QueryOptions**](https://docs.microsoft.com/uwp/api/Windows.Storage.Search.QueryOptions). Такой подход позволяет достичь значительно лучшей производительности, чем в предыдущем примере.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> // Set QueryOptions to prefetch our specific properties
> var queryOptions = new Windows.Storage.Search.QueryOptions(CommonFileQuery.OrderByDate, null);
> queryOptions.SetThumbnailPrefetch(ThumbnailMode.PicturesView, 100,
>         ThumbnailOptions.ReturnOnlyIfCached);
> queryOptions.SetPropertyPrefetch(PropertyPrefetchOptions.ImageProperties, 
>        new string[] {"System.Size"});
> 
> StorageFileQueryResult queryResults = KnownFolders.PicturesLibrary.CreateFileQueryWithOptions(queryOptions);
> IReadOnlyList<StorageFile> files = await queryResults.GetFilesAsync();
> 
> foreach (var file in files)
> {
>     ImageProperties imageProperties = await file.Properties.GetImagePropertiesAsync();
> 
>     // Do something with the date the image was taken.
>     DateTimeOffset dateTaken = imageProperties.DateTaken;
> 
>     // Performance gains increase with the number of properties that are accessed.
>     IDictionary<String, object> propertyResults =
>         await file.Properties.RetrievePropertiesAsync(
>               new string[] {"System.Size" });
> 
>     // Get/Set extra properties here
>     var systemSize = propertyResults["System.Size"];
> }
> ```
> ```vb
> ' Set QueryOptions to prefetch our specific properties
> Dim queryOptions = New Windows.Storage.Search.QueryOptions(CommonFileQuery.OrderByDate, Nothing)
> queryOptions.SetThumbnailPrefetch(ThumbnailMode.PicturesView,
>             100, Windows.Storage.FileProperties.ThumbnailOptions.ReturnOnlyIfCached)
> queryOptions.SetPropertyPrefetch(PropertyPrefetchOptions.ImageProperties,
>                                  New String() {"System.Size"})
> 
> Dim queryResults As StorageFileQueryResult = KnownFolders.PicturesLibrary.CreateFileQueryWithOptions(queryOptions)
> Dim files As IReadOnlyList(Of StorageFile) = Await queryResults.GetFilesAsync()
> 
> 
> For Each file In files
>     Dim imageProperties As ImageProperties = Await file.Properties.GetImagePropertiesAsync()
> 
>     ' Do something with the date the image was taken.
>     Dim dateTaken As DateTimeOffset = imageProperties.DateTaken
> 
>     ' Performance gains increase with the number of properties that are accessed.
>     Dim propertyResults As IDictionary(Of String, Object) =
>         Await file.Properties.RetrievePropertiesAsync(New String() {"System.Size"})
> 
>     ' Get/Set extra properties here
>     Dim systemSize = propertyResults("System.Size")
> 
> Next file
> ```
При выполнении нескольких операций с объектами Windows.Storage, такими как `Windows.Storage.ApplicationData.Current.LocalFolder`,создайте локальную переменную, ссылающуюся на соответствующий источник хранилища, чтобы каждый раз при обращении к нему не создавать промежуточные объекты.

## <a name="stream-performance-in-c-and-visual-basic"></a>Производительность потока на C# и Visual Basic

### <a name="buffering-between-uwp-and-net-streams"></a>Буферизация между потоками UWP и .NET

Существует много сценариев, когда вам может понадобиться преобразовать поток UWP (например, [**Windows.Storage.Streams.IInputStream**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.IInputStream) или [**IOutputStream**](https://docs.microsoft.com/uwp/api/Windows.Storage.Streams.IOutputStream)) в поток .NET ([**System.IO.Stream**](https://docs.microsoft.com/dotnet/api/system.io.stream)). Например, это может пригодиться, когда вы пишете приложение UWP и хотите использовать уже существующий код .NET, который работает в потоках с файловой системой UWP. Чтобы это осуществить, API .NET для приложений UWP предоставляют методы расширения, которые позволяют вам преобразовывать потоки типа .NET в потоки типа UWP и наоборот. Подробнее см. в разделе [**WindowsRuntimeStreamExtensions**](https://docs.microsoft.com/dotnet/api/system.io.windowsruntimestreamextensions).

Во время преобразования потока UWP в поток .NET вы создаете адаптер для основного потока UWP. При некоторых условиях требуются затраты времени, связанные с применением методов на потоках UWP. Это может повлиять на скорость вашего приложения, особенно в сценариях, где необходимо выполнять много маленьких и частых операций чтения или записи.

Для ускорения приложений адаптеры потоков UWP содержат буфер данных. В следующем примере кода показаны маленькие последовательные операции чтения с использованием адаптера потоков UWP с размером буфера по умолчанию.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> StorageFile file = await Windows.Storage.ApplicationData.Current
>     .LocalFolder.GetFileAsync("example.txt");
> Windows.Storage.Streams.IInputStream windowsRuntimeStream = 
>     await file.OpenReadAsync();
> 
> byte[] destinationArray = new byte[8];
> 
> // Create an adapter with the default buffer size.
> using (var managedStream = windowsRuntimeStream.AsStreamForRead())
> {
> 
>     // Read 8 bytes into destinationArray.
>     // A larger block is actually read from the underlying 
>     // windowsRuntimeStream and buffered within the adapter.
>     await managedStream.ReadAsync(destinationArray, 0, 8);
> 
>     // Read 8 more bytes into destinationArray.
>     // This call may complete much faster than the first call
>     // because the data is buffered and no call to the 
>     // underlying windowsRuntimeStream needs to be made.
>     await managedStream.ReadAsync(destinationArray, 0, 8);
> }
> ```
> ```vb
> Dim file As StorageFile = Await Windows.Storage.ApplicationData.Current -
> .LocalFolder.GetFileAsync("example.txt")
> Dim windowsRuntimeStream As Windows.Storage.Streams.IInputStream =
>     Await file.OpenReadAsync()
> 
> Dim destinationArray() As Byte = New Byte(8) {}
> 
> ' Create an adapter with the default buffer size.
> Dim managedStream As Stream = windowsRuntimeStream.AsStreamForRead()
> Using (managedStream)
> 
>     ' Read 8 bytes into destinationArray.
>     ' A larger block is actually read from the underlying 
>     ' windowsRuntimeStream and buffered within the adapter.
>     Await managedStream.ReadAsync(destinationArray, 0, 8)
> 
>     ' Read 8 more bytes into destinationArray.
>     ' This call may complete much faster than the first call
>     ' because the data is buffered and no call to the 
>     ' underlying windowsRuntimeStream needs to be made.
>     Await managedStream.ReadAsync(destinationArray, 0, 8)
> 
> End Using
> ```

Такое поведение буферизации по умолчанию желательно для многих сценариев, где вы преобразовываете поток UWP в поток .NET. Однако в некоторых сценариях вам может понадобиться оптимизировать поведение буферизации для увеличения производительности.

### <a name="working-with-large-data-sets"></a>Работа с большими наборами данных

При чтении или записи больших наборов данных вы можете увеличить пропускную способность чтения или записи, предоставив буфер большого размера для методов расширения [**AsStreamForRead**](https://docs.microsoft.com/dotnet/api/system.io.windowsruntimestreamextensions.asstreamforread?view=dotnet-uwp-10.0), [**AsStreamForWrite**](https://docs.microsoft.com/dotnet/api/system.io.windowsruntimestreamextensions.asstreamforwrite?view=dotnet-uwp-10.0) и [**AsStream**](https://docs.microsoft.com/dotnet/api/system.io.windowsruntimestreamextensions.asstream?view=dotnet-uwp-10.0). Это обеспечит адаптеру потоков внутренний буфер большего размера. Например, при передаче потока из большого файла в синтаксический анализатор XML анализатор может последовательно выполнить много маленьких операций чтения из потока. Большой буфер может снизить количество обращений к основному потоку UWP и повысить производительность.

> **Примечание.**    Нужно соблюдать осторожность при установке размера буфера выше приблизительно 80 КБ, т. к. это может привести к фрагментации кучи сборщика мусора (см. статью [Improve garbage collection performance](improve-garbage-collection-performance.md) (Повышение производительности сборки мусора)). В следующем примере кода создается управляемый адаптер потоков с буфером размером 81 920 байт.

> [!div class="tabbedCodeSnippets"]
```csharp
// Create a stream adapter with an 80 KB buffer.
Stream managedStream = nativeStream.AsStreamForRead(bufferSize: 81920);
```
```vb
' Create a stream adapter with an 80 KB buffer.
Dim managedStream As Stream = nativeStream.AsStreamForRead(bufferSize:=81920)
```

Методы [**Stream.CopyTo**](https://docs.microsoft.com/dotnet/api/system.io.stream.copyto) и [**CopyToAsync**](https://docs.microsoft.com/dotnet/api/system.io.stream.copytoasync) также выделяют локальный буфер для копирования между потоками. Как и с методом расширения [**AsStreamForRead**](https://docs.microsoft.com/dotnet/api/system.io.windowsruntimestreamextensions.asstreamforread?view=dotnet-uwp-10.0), вы можете получить оптимальную производительность для больших копий потоков, переопределив размер буфера по умолчанию. В следующем примере кода показано изменение размера буфера по умолчанию для вызова **CopyToAsync**.

> [!div class="tabbedCodeSnippets"]
> ```csharp
> MemoryStream destination = new MemoryStream();
> // copies the buffer into memory using the default copy buffer
> await managedStream.CopyToAsync(destination);
> 
> // Copy the buffer into memory using a 1 MB copy buffer.
> await managedStream.CopyToAsync(destination, bufferSize: 1024 * 1024);
> ```
> ```vb
> Dim destination As MemoryStream = New MemoryStream()
> ' copies the buffer into memory using the default copy buffer
> Await managedStream.CopyToAsync(destination)
> 
> ' Copy the buffer into memory using a 1 MB copy buffer.
> Await managedStream.CopyToAsync(destination, bufferSize:=1024 * 1024)
> ```

В этом примере использован размер буфера, равный 1 МБ, что превышает рекомендованные 80 КБ. С таким большим буфером можно улучшить пропускную способность операции копирования для очень больших наборов данных (т. е. сотен мегабайт). Однако для такого буфера выделяется куча для больших объектов, что потенциально может снизить производительность сборки мусора. Большие размеры буфера нужно использовать, только если это заметно улучшит производительность вашего приложения.

При одновременной работе с большим количеством потоков может понадобиться уменьшить или исключить излишки памяти буфера. Вы можете определить меньший буфер или задать для параметра *bufferSize* значение «0», чтобы полностью выключить буферизацию для данного адаптера потоков. Хорошую пропускную способность можно получить без буферизации, если осуществляются большие операции чтения и записи в управляемый поток.

### <a name="performing-latency-sensitive-operations"></a>Выполнение чувствительных к задержке операций

Вам также может понадобиться избегать буферизации, если вы хотите выполнять операции чтения и записи с небольшой задержкой и не хотите выполнять чтение большими блоками из основного потока UWP. Например, вам может понадобиться выполнять чтение и запись с небольшой задержкой, если вы используете поток для передачи данных по сети.

В приложении чата для отправки и получения сообщений вы можете использовать поток через сетевой интерфейс. В этом случае вам необходимо отправлять сообщения по мере готовности, а не ждать заполнения буфера. Если вы зададите размер буфера, равный 0, когда вызываете методы расширения [**AsStreamForRead**](https://docs.microsoft.com/dotnet/api/system.io.windowsruntimestreamextensions.asstreamforread?view=dotnet-uwp-10.0), [**AsStreamForWrite**](https://docs.microsoft.com/dotnet/api/system.io.windowsruntimestreamextensions.asstreamforwrite?view=dotnet-uwp-10.0) и [**AsStream**](https://docs.microsoft.com/dotnet/api/system.io.windowsruntimestreamextensions.asstream?view=dotnet-uwp-10.0), то адаптер не будет выделять буфер, и все вызовы будут напрямую влиять на основной поток UWP.


