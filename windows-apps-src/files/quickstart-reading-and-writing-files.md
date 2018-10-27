---
author: laurenhughes
ms.assetid: 27914C0A-2A02-473F-BDD5-C931E3943AA0
title: Создание, запись и чтение файла
description: Считайте и запишите файл с помощью объекта StorageFile.
ms.author: lahugh
ms.date: 06/28/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
- vb
ms.openlocfilehash: 9bc19460fe1b9b9c6b637606a737e1157d98feef
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2018
ms.locfileid: "5691982"
---
# <a name="create-write-and-read-a-file"></a>Создание, запись и чтение файла

**Важные API**

-   [**Класс StorageFolder**](/uwp/api/windows.storage.storagefolder)
-   [**Класс StorageFile**](/uwp/api/windows.storage.storagefile)
-   [**Класс FileIO**](/uwp/api/windows.storage.fileio)

Считайте и запишите файл с помощью объекта [**StorageFile**](/uwp/api/windows.storage.storagefile).

> [!NOTE]
> [См. также [Пример доступа к файлам](http://go.microsoft.com/fwlink/p/?linkid=619995).]

## <a name="prerequisites"></a>Необходимые условия

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](/windows/uwp/threading-async/call-asynchronous-apis-in-csharp-or-visual-basic). Сведения о создании асинхронных приложений на C + +/ WinRT, см. в разделе [параллельная обработка и асинхронные операции с помощью C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/concurrency). Сведения о создании асинхронных приложений на C + +/ CX, см. в разделе [асинхронное программирование на C + +/ CX](/windows/uwp/threading-async/asynchronous-programming-in-cpp-universal-windows-platform-apps).

-   **Знание порядка вызова файла для считывания, записи или выполнения обеих этих операций**

    Сведения о вызове файла с помощью средства выбора файлов см. в разделе [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md).

## <a name="creating-a-file"></a>Создание файла

Ниже приведен порядок создания файла в локальной папке приложения. Заменим его, если он уже существует.

```csharp
// Create sample file; replace if exists.
Windows.Storage.StorageFolder storageFolder =
    Windows.Storage.ApplicationData.Current.LocalFolder;
Windows.Storage.StorageFile sampleFile =
    await storageFolder.CreateFileAsync("sample.txt",
        Windows.Storage.CreationCollisionOption.ReplaceExisting);
```

```cppwinrt
// MainPage.h
#include <winrt/Windows.Storage.h>
...
Windows::Foundation::IAsyncAction ExampleCoroutineAsync()
{
    // Create a sample file; replace if exists.
    Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
    co_await storageFolder.CreateFileAsync(L"sample.txt", Windows::Storage::CreationCollisionOption::ReplaceExisting);
}
```

```cpp
// Create a sample file; replace if exists.
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
concurrency::create_task(storageFolder->CreateFileAsync("sample.txt", CreationCollisionOption::ReplaceExisting));
```

```vb
' Create sample file; replace if exists.
Dim storageFolder As StorageFolder = Windows.Storage.ApplicationData.Current.LocalFolder
Dim sampleFile As StorageFile = Await storageFolder.CreateFileAsync("sample.txt", CreationCollisionOption.ReplaceExisting)
```

## <a name="writing-to-a-file"></a>Запись в файл

Ниже приведен порядок записи в доступный для записи файл на диске с помощью класса [**StorageFile**](/uwp/api/windows.storage.storagefile). Первое действие для каждого из способов записи в файл (если запись в файл происходит не сразу же после его создания) — это получение файла с помощью [**StorageFolder.GetFileAsync**](/uwp/api/windows.storage.storagefolder.getfileasync).

```csharp
Windows.Storage.StorageFolder storageFolder =
    Windows.Storage.ApplicationData.Current.LocalFolder;
Windows.Storage.StorageFile sampleFile =
    await storageFolder.GetFileAsync("sample.txt");
```

```cppwinrt
// MainPage.h
#include <winrt/Windows.Storage.h>
...
Windows::Foundation::IAsyncAction ExampleCoroutineAsync()
{
    Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
    auto sampleFile{ co_await storageFolder.CreateFileAsync(L"sample.txt", Windows::Storage::CreationCollisionOption::ReplaceExisting) };
    // Process sampleFile
}
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile) 
{
    // Process file
});
```

```vb
Dim storageFolder As StorageFolder = Windows.Storage.ApplicationData.Current.LocalFolder
Dim sampleFile As StorageFile = Await storageFolder.GetFileAsync("sample.txt")
```

**Запись текста в файл**

Запись текста в файл, вызвав метод [**FileIO.WriteTextAsync**](/uwp/api/windows.storage.fileio.writetextasync) .

```csharp
await Windows.Storage.FileIO.WriteTextAsync(sampleFile, "Swift as a shadow");
```

```cppwinrt
// MainPage.h
#include <winrt/Windows.Storage.h>
...
Windows::Foundation::IAsyncAction ExampleCoroutineAsync()
{
    Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
    auto sampleFile{ co_await storageFolder.GetFileAsync(L"sample.txt") };
    // Write text to the file.
    co_await Windows::Storage::FileIO::WriteTextAsync(sampleFile, L"Swift as a shadow");
}
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile) 
{
    //Write text to a file
    create_task(FileIO::WriteTextAsync(sampleFile, "Swift as a shadow"));
});
```

```vb
Await Windows.Storage.FileIO.WriteTextAsync(sampleFile, "Swift as a shadow")
```

**Запись байт в файл с использованием буфера (2 действия)**

1.  Во-первых, вызовите метод [**CryptographicBuffer.ConvertStringToBinary**](/uwp/api/windows.security.cryptography.cryptographicbuffer.convertstringtobinary) для получения буфера байтов (на основе строки), вы хотите записать в файл.

```csharp
var buffer = Windows.Security.Cryptography.CryptographicBuffer.ConvertStringToBinary(
    "What fools these mortals be", Windows.Security.Cryptography.BinaryStringEncoding.Utf8);
```

```cppwinrt
// MainPage.h
#include <winrt/Windows.Security.Cryptography.h>
#include <winrt/Windows.Storage.h>
#include <winrt/Windows.Storage.Streams.h>
...
Windows::Foundation::IAsyncAction ExampleCoroutineAsync()
{
    Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
    auto sampleFile{ co_await storageFolder.GetFileAsync(L"sample.txt") };
    // Create the buffer.
    Windows::Storage::Streams::IBuffer buffer{
        Windows::Security::Cryptography::CryptographicBuffer::ConvertStringToBinary(
            L"What fools these mortals be", Windows::Security::Cryptography::BinaryStringEncoding::Utf8)};
    // The code in step 2 goes here.
}
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile)
{
    // Create the buffer
    IBuffer^ buffer = CryptographicBuffer::ConvertStringToBinary
    ("What fools these mortals be", BinaryStringEncoding::Utf8);
});
```

```vb
Dim buffer = Windows.Security.Cryptography.CryptographicBuffer.ConvertStringToBinary(
    "What fools these mortals be",
    Windows.Security.Cryptography.BinaryStringEncoding.Utf8)
```

2.  Затем запишите байты из буфера в файл, вызвав метод [**FileIO.WriteBufferAsync**](/uwp/api/windows.storage.fileio.writebufferasync) .

```csharp
await Windows.Storage.FileIO.WriteBufferAsync(sampleFile, buffer);
```

```cppwinrt
co_await Windows::Storage::FileIO::WriteBufferAsync(sampleFile, buffer);
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile)
{
    // Create the buffer
    IBuffer^ buffer = CryptographicBuffer::ConvertStringToBinary
    ("What fools these mortals be", BinaryStringEncoding::Utf8);      
    // Write bytes to a file using a buffer
    create_task(FileIO::WriteBufferAsync(sampleFile, buffer));
});
```

```vb
Await Windows.Storage.FileIO.WriteBufferAsync(sampleFile, buffer)
```

**Запись текста в файл с использованием потока (4 действия)**

1.  Сначала откройте файл, вызвав метод [**StorageFile.OpenAsync**](/uwp/api/windows.storage.storagefile.openasync). По завершении операции открытия будет возвращен поток содержимого файла.

```csharp
var stream = await sampleFile.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);
```

```cppwinrt
// MainPage.h
#include <winrt/Windows.Storage.h>
#include <winrt/Windows.Storage.Streams.h>
...
Windows::Foundation::IAsyncAction ExampleCoroutineAsync()
{
    Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
    auto sampleFile{ co_await storageFolder.GetFileAsync(L"sample.txt") };
    Windows::Storage::Streams::IRandomAccessStream stream{ co_await sampleFile.OpenAsync(Windows::Storage::FileAccessMode::ReadWrite) };
    // The code in step 2 goes here.
}
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile)
{
    create_task(sampleFile->OpenAsync(FileAccessMode::ReadWrite)).then([sampleFile](IRandomAccessStream^ stream)
    {
        // Process stream
    });
});
```

```vb
Dim stream = Await sampleFile.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite)
```

2.  Затем получите выходной поток, вызвав метод [**IRandomAccessStream.GetOutputStreamAt**](/uwp/api/windows.storage.streams.irandomaccessstream.getoutputstreamat) из `stream`. Если вы используете C#, заключите это в оператор **using** для управления временем существования выходного потока. Если вы используете [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), то можно управлять своим временем существования, включающего в блоке, или значение `nullptr` после завершения с ним.

```csharp
using (var outputStream = stream.GetOutputStreamAt(0))
{
    // We'll add more code here in the next step.
}
stream.Dispose(); // Or use the stream variable (see previous code snippet) with a using statement as well.
```

```cppwinrt
Windows::Storage::Streams::IOutputStream outputStream{ stream.GetOutputStreamAt(0) };
// The code in step 3 goes here.
```

```cpp
// Add to "Process stream" in part 1
IOutputStream^ outputStream = stream->GetOutputStreamAt(0);
```

```vb
Using outputStream = stream.GetOutputStreamAt(0)
' We'll add more code here in the next step.
End Using
```

3.  Теперь добавьте следующий код для записи в поток вывода, создав новый объект [**DataWriter**](/uwp/api/windows.storage.streams.datawriter) и вызвав метод [**DataWriter.WriteString**](/uwp/api/windows.storage.streams.datawriter.writestring) (Если вы используете C# в пределах существующего оператора **using** ).

```csharp
using (var dataWriter = new Windows.Storage.Streams.DataWriter(outputStream))
{
    dataWriter.WriteString("DataWriter has methods to write to various types, such as DataTimeOffset.");
}
```

```cppwinrt
Windows::Storage::Streams::DataWriter dataWriter;
dataWriter.WriteString(L"DataWriter has methods to write to various types, such as DataTimeOffset.");
// The code in step 4 goes here.
```

```cpp
// Added after code from part 2
DataWriter^ dataWriter = ref new DataWriter(outputStream);
dataWriter->WriteString("DataWriter has methods to write to various types, such as DataTimeOffset.");
```

```vb
Dim dataWriter As New DataWriter(outputStream)
dataWriter.WriteString("DataWriter has methods to write to various types, such as DataTimeOffset.")
```

4.  И наконец, добавьте следующий код для сохранения текста в файл с помощью [**DataWriter.StoreAsync**](/uwp/api/windows.storage.streams.datawriter.storeasync) и закрытия потока с помощью [**IOutputStream.FlushAsync**](/uwp/api/windows.storage.streams.ioutputstream.flushasync)(Если вы используете C# в пределах оператор **с помощью** внутреннего).

```csharp
await dataWriter.StoreAsync();
await outputStream.FlushAsync();
```

```cppwinrt
dataWriter.StoreAsync();
outputStream.FlushAsync();
```

```cpp
// Added after code from part 3
dataWriter->StoreAsync();
outputStream->FlushAsync();
```

```vb
Await dataWriter.StoreAsync()
Await outputStream.FlushAsync()
```

## <a name="reading-from-a-file"></a>Чтение из файла

Ниже приведен порядок чтения из файла на диске с помощью класса [**StorageFile**](/uwp/api/Windows.Storage.StorageFile). Первое действие для каждого из способов чтения из файла — это получение файла с помощью [**StorageFolder.GetFileAsync**](/uwp/api/windows.storage.storagefolder.getfileasync).

```csharp
Windows.Storage.StorageFolder storageFolder =
    Windows.Storage.ApplicationData.Current.LocalFolder;
Windows.Storage.StorageFile sampleFile =
    await storageFolder.GetFileAsync("sample.txt");
```

```cppwinrt
Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
auto sampleFile{ co_await storageFolder.GetFileAsync(L"sample.txt") };
// Process file
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile)
{
    // Process file
});
```

```vb
Dim storageFolder As StorageFolder = Windows.Storage.ApplicationData.Current.LocalFolder
Dim sampleFile As StorageFile = Await storageFolder.GetFileAsync("sample.txt")
```

**Чтение текста из файла**

Чтение текста из файла, вызвав метод [**FileIO.ReadTextAsync**](/uwp/api/windows.storage.fileio.readtextasync) .

```csharp
string text = await Windows.Storage.FileIO.ReadTextAsync(sampleFile);
```

```cppwinrt
Windows::Foundation::IAsyncOperation<winrt::hstring> ExampleCoroutineAsync()
{
    Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
    auto sampleFile{ co_await storageFolder.GetFileAsync(L"sample.txt") };
    co_return co_await Windows::Storage::FileIO::ReadTextAsync(sampleFile);
}
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile)
{
    return FileIO::ReadTextAsync(sampleFile);
});
```

```vb
Dim text As String = Await Windows.Storage.FileIO.ReadTextAsync(sampleFile)
```

**Чтение текста из файла с использованием буфера (2 действия)**

1.  Во-первых вызовите метод [**FileIO.ReadBufferAsync**](/uwp/api/windows.storage.fileio.readbufferasync) .

```csharp
var buffer = await Windows.Storage.FileIO.ReadBufferAsync(sampleFile);
```

```cppwinrt
Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
auto sampleFile{ co_await storageFolder.GetFileAsync(L"sample.txt") };
Windows::Storage::Streams::IBuffer buffer{ co_await Windows::Storage::FileIO::ReadBufferAsync(sampleFile) };
// The code in step 2 goes here.
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile)
{
    return FileIO::ReadBufferAsync(sampleFile);

}).then([](Streams::IBuffer^ buffer)
{
    // Process buffer
});
```

```vb
Dim buffer = Await Windows.Storage.FileIO.ReadBufferAsync(sampleFile)
```

2.  Затем используйте объект [**DataReader**](/uwp/api/windows.storage.streams.datareader), чтобы сначала считать длину буфера, а затем его содержимое.

```csharp
using (var dataReader = Windows.Storage.Streams.DataReader.FromBuffer(buffer))
{
    string text = dataReader.ReadString(buffer.Length);
}
```

```cppwinrt
auto dataReader{ Windows::Storage::Streams::DataReader::FromBuffer(buffer) };
winrt::hstring bufferText{ dataReader.ReadString(buffer.Length()) };
```

```cpp
// Add to "Process buffer" section from part 1
auto dataReader = DataReader::FromBuffer(buffer);
String^ bufferText = dataReader->ReadString(buffer->Length);
```

```vb
Dim dataReader As DataReader = Windows.Storage.Streams.DataReader.FromBuffer(buffer)
Dim text As String = dataReader.ReadString(buffer.Length)
```

**Чтение текста из файла с использованием потока (4 действия)**

1.  Откройте поток для файла, вызвав метод [**StorageFile.OpenAsync**](/uwp/api/windows.storage.storagefile.openasync). По завершении операции будет возвращен поток содержимого файла.

```csharp
var stream = await sampleFile.OpenAsync(Windows.Storage.FileAccessMode.Read);
```

```cppwinrt
Windows::Storage::StorageFolder storageFolder{ Windows::Storage::ApplicationData::Current().LocalFolder() };
auto sampleFile{ co_await storageFolder.GetFileAsync(L"sample.txt") };
Windows::Storage::Streams::IRandomAccessStream stream{ co_await sampleFile.OpenAsync(Windows::Storage::FileAccessMode::Read) };
// The code in step 2 goes here.
```

```cpp
StorageFolder^ storageFolder = ApplicationData::Current->LocalFolder;
create_task(storageFolder->GetFileAsync("sample.txt")).then([](StorageFile^ sampleFile)
{
    create_task(sampleFile->OpenAsync(FileAccessMode::Read)).then([sampleFile](IRandomAccessStream^ stream)
    {
        // Process stream
    });
});
```

```vb
Dim stream = Await sampleFile.OpenAsync(Windows.Storage.FileAccessMode.Read)
```

2.  Получите размер потока для последующего использования.

```csharp
ulong size = stream.Size;
```

```cppwinrt
uint64_t size{ stream.Size() };
// The code in step 3 goes here.
```

```cpp
// Add to "Process stream" from part 1
UINT64 size = stream->Size;
```

```vb
Dim size = stream.Size
```

3.  Получите входной поток, вызвав метод [**IRandomAccessStream.GetInputStreamAt**](/uwp/api/windows.storage.streams.irandomaccessstream.getinputstreamat) . Вставьте его в оператор **using**, чтобы получить возможность управления временем существования потока. При вызове **GetInputStreamAt** укажите значение 0, чтобы установить положение в начале потока.

```csharp
using (var inputStream = stream.GetInputStreamAt(0))
{
    // We'll add more code here in the next step.
}
```

```cppwinrt
Windows::Storage::Streams::IInputStream inputStream{ stream.GetInputStreamAt(0) };
Windows::Storage::Streams::DataReader dataReader{ inputStream };
// The code in step 4 goes here.
```

```cpp
// Add after code from part 2
IInputStream^ inputStream = stream->GetInputStreamAt(0);
auto dataReader = ref new DataReader(inputStream);
```

```vb
Using inputStream = stream.GetInputStreamAt(0)
' We'll add more code here in the next step.
End Using
```

4.  Наконец, добавьте этот код в пределах существующего оператора **using**, чтобы получить объект [**DataReader**](/uwp/api/windows.storage.streams.datareader) в потоке, а затем считайте текст, вызвав [**DataReader.LoadAsync**](/uwp/api/windows.storage.streams.datareader.loadasync) и [**DataReader.ReadString**](/uwp/api/windows.storage.streams.datareader.readstring).

```csharp
using (var dataReader = new Windows.Storage.Streams.DataReader(inputStream))
{
    uint numBytesLoaded = await dataReader.LoadAsync((uint)size);
    string text = dataReader.ReadString(numBytesLoaded);
}
```

```cppwinrt
unsigned int cBytesLoaded{ co_await dataReader.LoadAsync(size) };
winrt::hstring streamText{ dataReader.ReadString(cBytesLoaded) };
```

```cpp
// Add after code from part 3
create_task(dataReader->LoadAsync(size)).then([sampleFile, dataReader](unsigned int numBytesLoaded)
{
    String^ streamText = dataReader->ReadString(numBytesLoaded);
});
```

```vb
Dim dataReader As New DataReader(inputStream)
Dim numBytesLoaded As UInteger = Await dataReader.LoadAsync(CUInt(size))
Dim text As String = dataReader.ReadString(numBytesLoaded)
```
