---
author: normesta
ms.assetid: 27914C0A-2A02-473F-BDD5-C931E3943AA0
title: "Создание, запись и чтение файла"
description: "Считайте и запишите файл с помощью объекта StorageFile."
translationtype: Human Translation
ms.sourcegitcommit: 24f50f2c4ac7fcfe931759d42ba73e758d77f6e8
ms.openlocfilehash: 2a9f5de71712e657bc9a0a39b668085c399ce9fb

---

# Создание, запись и чтение файла


\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


**Важные API**

-   [**Класс StorageFolder**](https://msdn.microsoft.com/library/windows/apps/br227230)
-   [**Класс StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171)
-   [**Класс FileIO**](https://msdn.microsoft.com/library/windows/apps/hh701440)

Считайте и запишите файл с помощью объекта [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171).

> **Примечание.** См. также [пример доступа к файлам](http://go.microsoft.com/fwlink/p/?linkid=619995).

## Необходимые условия

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C# или Visual Basic см. в статье [Вызов асинхронных API в C# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

-   **Знание порядка вызова файла для считывания, записи или выполнения обеих этих операций**

    Сведения о вызове файла с помощью средства выбора файлов см. в разделе [Открытие файлов и папок с помощью средства выбора](quickstart-using-file-and-folder-pickers.md).

## Создание файла

Ниже приведен порядок создания файла в локальной папке приложения. Заменим его, если он уже существует.
> [!div class="tabbedCodeSnippets"]
```cs
// Create sample file; replace if exists.
Windows.Storage.StorageFolder storageFolder =
    Windows.Storage.ApplicationData.Current.LocalFolder;
Windows.Storage.StorageFile sampleFile =
    await storageFolder.CreateFileAsync("sample.txt",
        Windows.Storage.CreationCollisionOption.ReplaceExisting);
```
```vb
' Create sample file; replace if exists.
Dim storageFolder As StorageFolder = Windows.Storage.ApplicationData.Current.LocalFolder
Dim sampleFile As StorageFile = Await storageFolder.CreateFileAsync("sample.txt", CreationCollisionOption.ReplaceExisting)
```

## Запись в файл


Ниже приведен порядок записи в доступный для записи файл на диске с помощью класса [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171). Первое действие для каждого из способов записи в файл (если запись в файл происходит не сразу же после его создания) — это получение файла с помощью [**StorageFolder.GetFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227272).
> [!div class="tabbedCodeSnippets"]
```cs
Windows.Storage.StorageFolder storageFolder =
    Windows.Storage.ApplicationData.Current.LocalFolder;
Windows.Storage.StorageFile sampleFile =
    await storageFolder.GetFileAsync("sample.txt");
```
```vb
Dim storageFolder As StorageFolder = Windows.Storage.ApplicationData.Current.LocalFolder
Dim sampleFile As StorageFile = Await storageFolder.GetFileAsync("sample.txt")
```

**Запись текста в файл**

Чтобы записать текст в файл, вызовите метод [**WriteTextAsync**](https://msdn.microsoft.com/library/windows/apps/hh701505) класса [**FileIO**](https://msdn.microsoft.com/library/windows/apps/hh701440).
> [!div class="tabbedCodeSnippets"]
```cs
await Windows.Storage.FileIO.WriteTextAsync(sampleFile, "Swift as a shadow");
```
```vb
Await Windows.Storage.FileIO.WriteTextAsync(sampleFile, "Swift as a shadow")
```

**Запись байт в файл с использованием буфера (2 действия)**

1.  Сначала вызовите [**ConvertStringToBinary**](https://msdn.microsoft.com/library/windows/apps/br241385) для получения буфера байтов (на основе произвольной строки), которые требуется записать в файл.
> [!div class="tabbedCodeSnippets"]
```cs
var buffer = Windows.Security.Cryptography.CryptographicBuffer.ConvertStringToBinary(
        "What fools these mortals be", Windows.Security.Cryptography.BinaryStringEncoding.Utf8);
```
```vb
Dim buffer = Windows.Security.Cryptography.CryptographicBuffer.ConvertStringToBinary(
                    "What fools these mortals be",
                    Windows.Security.Cryptography.BinaryStringEncoding.Utf8)
```

2.  Затем запишите байты из буфера в файл, вызвав метод [**WriteBufferAsync**](https://msdn.microsoft.com/library/windows/apps/hh701490) класса [**FileIO**](https://msdn.microsoft.com/library/windows/apps/hh701440).
> [!div class="tabbedCodeSnippets"]
```cs
await Windows.Storage.FileIO.WriteBufferAsync(sampleFile, buffer);
```
```vb
Await Windows.Storage.FileIO.WriteBufferAsync(sampleFile, buffer)
```

**Запись текста в файл с использованием потока (4 действия)**

1.  Сначала откройте файл, вызвав метод [**StorageFile.OpenAsync**](https://msdn.microsoft.com/library/windows/apps/dn889851). По завершении операции открытия будет возвращен поток содержимого файла.
> [!div class="tabbedCodeSnippets"]
```cs
var stream = await sampleFile.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);
```
```vb
Dim stream = Await sampleFile.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite)
```

2.  Далее получите выходной поток, вызвав метод [**GetOutputStreamAt**](https://msdn.microsoft.com/library/windows/apps/br241738) из `stream`. Вставьте его в оператор **using**, чтобы управлять временем существования выходного потока.
> [!div class="tabbedCodeSnippets"]
```cs
using (var outputStream = stream.GetOutputStreamAt(0))
    {
        // We'll add more code here in the next step.
    }
    stream.Dispose(); // Or use the stream variable (see previous code snippet) with a using statement as well.
```
```vb
Using outputStream = stream.GetOutputStreamAt(0)
  ' We'll add more code here in the next step.
End Using
```

3.  Теперь добавьте этот код в пределах существующего оператора **using** для записи в поток вывода, создав новый объект [**DataWriter**](https://msdn.microsoft.com/library/windows/apps/br208154) и вызвав метод [**DataWriter.WriteString**](https://msdn.microsoft.com/library/windows/apps/br241642).
> [!div class="tabbedCodeSnippets"]
```cs
using (var dataWriter = new Windows.Storage.Streams.DataWriter(outputStream))
    {
        dataWriter.WriteString("DataWriter has methods to write to various types, such as DataTimeOffset.");
    }
```
```vb
    Dim dataWriter As New DataWriter(outputStream)
    dataWriter.WriteString("DataWriter has methods to write to various types, such as DataTimeOffset.")
```

4.  Наконец, добавьте этот код (во внутреннем операторе **using**) для сохранения текста в файл с помощью [**StoreAsync**](https://msdn.microsoft.com/library/windows/apps/br208171) и закрытия потока с помощью [**FlushAsync**](https://msdn.microsoft.com/library/windows/apps/br241729).
> [!div class="tabbedCodeSnippets"]
```cs
    await dataWriter.StoreAsync();
        await outputStream.FlushAsync();
```
```vb
    Await dataWriter.StoreAsync()
        Await outputStream.FlushAsync()
```

## Чтение из файла


Ниже приведен порядок чтения из файла на диске с помощью класса [**StorageFile**](https://msdn.microsoft.com/library/windows/apps/br227171). Первое действие для каждого из способов чтения из файла — это получение файла с помощью [**StorageFolder.GetFileAsync**](https://msdn.microsoft.com/library/windows/apps/br227272).
> [!div class="tabbedCodeSnippets"]
```cs
Windows.Storage.StorageFolder storageFolder =
    Windows.Storage.ApplicationData.Current.LocalFolder;
Windows.Storage.StorageFile sampleFile =
    await storageFolder.GetFileAsync("sample.txt");
```
```vb
Dim storageFolder As StorageFolder = Windows.Storage.ApplicationData.Current.LocalFolder
Dim sampleFile As StorageFile = Await storageFolder.GetFileAsync("sample.txt")
```

**Чтение текста из файла**

Чтобы выполнить чтение текста из файла, вызовите метод [**ReadTextAsync**](https://msdn.microsoft.com/library/windows/apps/hh701482) класса [**FileIO**](https://msdn.microsoft.com/library/windows/apps/hh701440).
> [!div class="tabbedCodeSnippets"]
```cs
string text = await Windows.Storage.FileIO.ReadTextAsync(sampleFile);
```
```vb
Dim text As String = Await Windows.Storage.FileIO.ReadTextAsync(sampleFile)
```

**Чтение текста из файла с использованием буфера (2 действия)**

1.  Сначала вызовите метод [**ReadBufferAsync**](https://msdn.microsoft.com/library/windows/apps/hh701468) класса [**FileIO**](https://msdn.microsoft.com/library/windows/apps/hh701440).
> [!div class="tabbedCodeSnippets"]
```cs
var buffer = await Windows.Storage.FileIO.ReadBufferAsync(sampleFile);
```
```vb
Dim buffer = Await Windows.Storage.FileIO.ReadBufferAsync(sampleFile)
```

2.  Затем используйте объект [**DataReader**](https://msdn.microsoft.com/library/windows/apps/br208119), чтобы сначала считать длину буфера, а затем его содержимое.
> [!div class="tabbedCodeSnippets"]
```cs
using (var dataReader = Windows.Storage.Streams.DataReader.FromBuffer(buffer))
    {
        string text = dataReader.ReadString(buffer.Length);
    }
```
```vb
Dim dataReader As DataReader = Windows.Storage.Streams.DataReader.FromBuffer(buffer)
    Dim text As String = dataReader.ReadString(buffer.Length)
```

**Чтение текста из файла с использованием потока (4 действия)**

1.  Откройте поток для файла, вызвав метод [**StorageFile.OpenAsync**](https://msdn.microsoft.com/library/windows/apps/dn889851). По завершении операции будет возвращен поток содержимого файла.
> [!div class="tabbedCodeSnippets"]
```cs
var stream = await sampleFile.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);
```
```vb
Dim stream = Await sampleFile.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite)
```

2.  Получите размер потока для последующего использования.
> [!div class="tabbedCodeSnippets"]
```cs
ulong size = stream.Size;
```
```vb
Dim size = stream.Size
```

3.  Получите входной поток, вызвав метод [**GetInputStreamAt**](https://msdn.microsoft.com/library/windows/apps/br241737). Вставьте его в оператор **using**, чтобы получить возможность управления временем существования потока. При вызове **GetInputStreamAt** укажите значение 0, чтобы установить положение в начале потока.
> [!div class="tabbedCodeSnippets"]
```cs
using (var inputStream = stream.GetInputStreamAt(0))
    {
        // We'll add more code here in the next step.
    }
```
```vb
Using inputStream = stream.GetInputStreamAt(0)
    ' We'll add more code here in the next step.
End Using
```

4.  Наконец, добавьте этот код в пределах существующего оператора **using**, чтобы получить объект [**DataReader**](https://msdn.microsoft.com/library/windows/apps/br208119) в потоке, а затем считайте текст, вызвав [**DataReader.LoadAsync**](https://msdn.microsoft.com/library/windows/apps/br208135) и [**DataReader.ReadString**](https://msdn.microsoft.com/library/windows/apps/br208147).
> [!div class="tabbedCodeSnippets"]
```cs
using (var dataReader = new Windows.Storage.Streams.DataReader(inputStream))
    {
        uint numBytesLoaded = await dataReader.LoadAsync((uint)size);
        string text = dataReader.ReadString(numBytesLoaded);
    }
```
```vb
Dim dataReader As New DataReader(inputStream)
    Dim numBytesLoaded As UInteger = Await dataReader.LoadAsync(CUInt(size))
    Dim text As String = dataReader.ReadString(numBytesLoaded)
```

 

 



<!--HONumber=Nov16_HO1-->


