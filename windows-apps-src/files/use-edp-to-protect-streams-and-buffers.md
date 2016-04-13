---
Description: 'В этом разделе показаны примеры задач программирования, необходимых для реализации некоторых наиболее распространенных сценариев защиты корпоративных данных (EDP), связанных с потоками и буферами.'
MS-HAID: 'dev\_files.use\_edp\_to\_protect\_streams\_and\_buffers'
MSHAttr: 'PreferredLib:/library/windows/apps'
Search.Product: eADQiWindows 10XVcnh
title: 'Использование защиты корпоративных данных (EDP) для защиты потоков и буферов'
---

# Использование защиты корпоративных данных (EDP) для защиты потоков и буферов

__Примечание__ Политику защиты корпоративных данных (EDP) нельзя применить в Windows 10 версии 1511 (сборка 10586) или более ранних версий.

В этом разделе показаны примеры задач программирования, необходимых для реализации некоторых наиболее распространенных сценариев защиты корпоративных данных (EDP), связанных с потоками и буферами. Полное описание взаимосвязи EDP с файлами, потоками, буфером обмена, сетями, фоновыми задачами и защитой данных в режиме блокировки для разработчиков см. в разделе [Защита корпоративных данных (EDP)](../enterprise/edp-hub.md).

**Примечание**  В [примере защиты корпоративных данных](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409) проиллюстрированы многие сценарии файлов, обсуждаемые в этом разделе.

## Необходимые условия


-   **Настройка для EDP**

    См. раздел [Настройка компьютера для EDP](../enterprise/edp-hub.md#set-up-your-computer-for-EDP).

-   **Создание корпоративно-грамотного приложения**

    Приложение, которое автономно гарантирует, что корпоративные данные останутся под управлением предприятия, называют корпоративно-грамотным приложением. Грамотное приложение более мощное, функциональное, гибкое и надежное, чем неграмотное. Ваше приложение сообщает системе, что оно грамотное, объявляя ограниченную возможность **enterpriseDataPolicy**. Но чтобы приложение было грамотным, недостаточно просто объявить соответствующую возможность. Подробнее: [Корпоративно-грамотные приложения](../enterprise/edp-hub.md#enterprise-enlightened-apps).

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C\# или Visual Basic см. в статье [Вызов асинхронных API в C\# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

## Защита потока данных для корпоративного удостоверения


**Примечание**  При защите потока или буфера мы настоятельно рекомендуем подписываться на событие [**ProtectionPolicyManager.PolicyChanged**](https://msdn.microsoft.com/library/windows/apps/mt608411), чтобы приложение получало уведомление в случае отключения EDP на устройстве. Если это произойдет, необходимо снять защиту всех потоков и буферов. Любой поток или буфер, которые останутся защищенными, могут быть отозваны, если пользователь отключит устройство от системы управления мобильными устройствами (MDM). Если EDP был отключен при создании ресурса, такой отзыв нежелателен. Это можно предотвратить, отключив EDP и сняв защиту ресурсов.



В этом сценарии приложение имеет доступ к незащищенному потоку, содержащему корпоративные данные. Чтобы обеспечить защиту этого потока при его перемещении внутри и за пределами устройства, приложение защищает данные для того корпоративного удостоверения, которому они принадлежат. Это позволяет предприятию удалять данные в случае необходимости. Чтобы позднее определить, нужно или нет вызывать для потока снимающий защиту метод, приложение должно поддерживать состояние, которое указывает, защищен ли поток. Поэтому функция в данном примере кода возвращает это состояние. Если полученное удостоверение не является управляемым или если приложение не разрешено для удостоверения, поток не будет защищен. При вызове [**DataProtectionManager.ProtectStreamAsync**](https://msdn.microsoft.com/library/windows/apps/dn706021) будет возвращено состояние "Не защищен".

```CSharp
using Windows.Storage.Streams;
using Windows.Security.EnterpriseData;

private async System.Threading.Tasks.Task<bool> ProtectAStream
    (InMemoryRandomAccessStream unprotectedInMemoryRandomAccessStream, string identity,
    InMemoryRandomAccessStream protectedInMemoryRandomAccessStream)
{
    IInputStream unprotectedStream = unprotectedInMemoryRandomAccessStream.GetInputStreamAt(0);
    IOutputStream protectedStream = protectedInMemoryRandomAccessStream.GetOutputStreamAt(0);

    // Protect "inputStream".
    DataProtectionInfo info = 
        await DataProtectionManager.ProtectStreamAsync(unprotectedStream, identity, protectedStream);

    // Indicate to the caller whether the stream was protected successfully. It will only be protected if
    // the identity is managed AND this app is allowed for this identity. Similar to buffers, this status
    // must be stored by the app. UnprotectStreamAsync must only be called if the stream was protected
    // successfully earlier.

    return (info.Status == DataProtectionStatus.Protected);
}
```

Чтобы показать, как можно использовать метод, подобный методу в примере кода выше, здесь представлен вспомогательный метод, использующий его для преобразования строки в незащищенный поток и последующей защиты потока.

```CSharp
using Windows.Storage.Streams;

private async System.Threading.Tasks.Task<bool> ProtectStringAsStreamAsync
    (string unprotectedEnterpriseData, string identity, 
    InMemoryRandomAccessStream protectedInMemoryRandomAccessStream)
{
    using (var unprotectedInMemoryRandomAccessStream = new InMemoryRandomAccessStream())
    {
        using (var dataWriter = new DataWriter(unprotectedInMemoryRandomAccessStream))
        {
            dataWriter.WriteString(unprotectedEnterpriseData);
            await dataWriter.StoreAsync();
            await dataWriter.FlushAsync();
            return await this.ProtectAStream(unprotectedInMemoryRandomAccessStream,
                identity, protectedInMemoryRandomAccessStream);
        }
    }
}
```

## Получение состояния потока


В этом сценарии приложение ранее защищало поток, к которому необходимо предотвратить несанкционированный доступ. Чтобы получить содержимое потока, когда это будет необходимо, ваше приложение может проверить состояние потока.

Обратите внимание, что состояние потока также возвращается из [**DataProtectionManager.UnprotectStreamAsync**](https://msdn.microsoft.com/library/windows/apps/dn706023). Этот API не будет возвращать состояние "Не защищен", так как он требует, чтобы входящий ресурс был защищен (невозможно со 100%-ной уверенностью подтвердить, что ресурс является незащищенным). Помните, если ваш код сравнивает результат с "Не защищен", это предполагает наличие ошибки проектирования. Это означает, что ваш код перестал отслеживать состояние защиты потока.

```CSharp
using Windows.Storage.Streams;
using Windows.Security.EnterpriseData;

private async void CheckProtectedStreamStatus(IInputStream protectedStream)
{
    DataProtectionInfo dataProtectionInfo = 
        await DataProtectionManager.GetStreamProtectionInfoAsync(protectedStream);

    if (dataProtectionInfo.Status == DataProtectionStatus.Revoked)
    {
        // Code goes here to handle this situation. Perhaps, show UI
        // saying that the user's data has been revoked.
    }
    else if (dataProtectionInfo.Status != DataProtectionStatus.Protected)
    {
        // Code goes here to handle the unexpected protection status.
    }
}
```

## Снятие защиты с потока данных


В этом сценарии приложению необходимо снять защиту с ранее защищенного потока. В этом примере код берет защищенный поток (помните, что для выполнения этого процесса поток должен быть защищен) и снимает защиту путем вызова [**DataProtectionManager.UnprotectStreamAsync**](https://msdn.microsoft.com/library/windows/apps/dn706023). После этого он читает строку из потока и возвращает его.

```CSharp
using Windows.Storage.Streams;

private async System.Threading.Tasks.Task<string> UnprotectStreamIntoStringAsync
    (InMemoryRandomAccessStream protectedInMemoryRandomAccessStream)
{
    using (var unprotectedInMemoryRandomAccessStream = new InMemoryRandomAccessStream())
    {
        DataProtectionInfo dataProtectionInfo = 
            await DataProtectionManager.UnprotectStreamAsync(protectedInMemoryRandomAccessStream, 
            unprotectedInMemoryRandomAccessStream);

        using (var inputStream = unprotectedInMemoryRandomAccessStream.GetInputStreamAt(0))
        {
            using (var dataReader = new DataReader(inputStream))
            {
                await dataReader.LoadAsync((uint)unprotectedInMemoryRandomAccessStream.Size);
                return dataReader.ReadString((uint)unprotectedInMemoryRandomAccessStream.Size);
            }
        }
    }
}
```

Чтобы показать, как можно использовать вспомогательные методы, приведем пример обработчика событий, который берет строку из текстового поля, записывает строку в поток, защищает поток, снимает защиту с потока (если он был успешно защищен) и, наконец, считывает строку из незащищенного потока и отображает ее в пользовательском интерфейсе.

```CSharp
using Windows.Storage.Streams;

private async void ProtectAndThenUnprotectStream_Click(object sender, RoutedEventArgs e)
{
    var protectedInMemoryRandomAccessStream = new InMemoryRandomAccessStream();
    bool isStreamProtected = await this.ProtectStringAsStreamAsync
        (this.enterpriseDataTextBox.Text, MainPage.IDENTITY, protectedInMemoryRandomAccessStream);

    // Only unprotect the stream if we're sure that the stream actually was protected.
    if (isStreamProtected)
    {
        this.resultDataTextBlock.Text = 
            await this.UnprotectStreamIntoStringAsync(protectedInMemoryRandomAccessStream);
    }
}
```

## Получение состояния буфера статических данных


В этом сценарии приложение ранее защищало буфер, к которому необходимо предотвратить несанкционированный доступ. Чтобы получить содержимое буфера, когда это будет необходимо, ваше приложение может проверить состояние буфера.

Обратите внимание, что состояние буфера также возвращается из [**DataProtectionManager.UnprotectAsync**](https://msdn.microsoft.com/library/windows/apps/dn706022). Этот API не будет возвращать состояние "Не защищен", так как он требует, чтобы входящий ресурс был защищен.

```CSharp
using Windows.Security.EnterpriseData;
using Windows.Storage.Streams;

private async void CheckProtectedBufferStatus(IBuffer protectedData)
{
    DataProtectionInfo dataProtectionInfo = 
        await DataProtectionManager.GetProtectionInfoAsync(protectedData);

    if (dataProtectionInfo.Status == DataProtectionStatus.Revoked)
    {
        // Code goes here to handle this situation, perhaps show UI
        // saying that the user's data has been revoked.
    }
    else if (dataProtectionInfo.Status != DataProtectionStatus.Protected)
    {
        // Code goes here to handle the unexpected protection status.
    }
}
```

## Защита статических данных, извлеченных из корпоративного ресурса


Этот сценарий описывает те же ситуации, что и в примерах кода потока, с тем исключением, что в нем используется буфер данных. Еще раз напоминаем, что необходимо сохранить состояние, отображающее, был ли защищен буфер, как показано в примере. Если полученное удостоверение не является управляемым или если приложение не разрешено для удостоверения, буфер не будет защищен и при вызове [**DataProtectionManager.ProtectAsync**](https://msdn.microsoft.com/library/windows/apps/dn706020) будет возвращено состояние "Не защищен".

```CSharp
using Windows.Security.Cryptography;
using Windows.Security.EnterpriseData;
using Windows.Storage.Streams;

...

private IBuffer data = null;
private bool isProtected = false;

void StoreBuffer(IBuffer data, bool isProtected)
{
    this.data = data;
    this.isProtected = isProtected;
}

IBuffer GetStoredBuffer(out bool isProtected)
{
    isProtected = this.isProtected;
    return this.data;
}

private string identity = "contoso.com";

private async void ProtectAndThenUnprotectBuffer_Click(object sender, RoutedEventArgs e)
{
    BinaryStringEncoding encoding = BinaryStringEncoding.Utf8;
    IBuffer inputData = CryptographicBuffer.ConvertStringToBinary
        (this.enterpriseDataTextBox.Text, encoding);
    BufferProtectUnprotectResult result =
        await DataProtectionManager.ProtectAsync(inputData, this.identity);

    // Record whether the buffer was protected successfully. It will only be protected if
    // the identity is managed AND this app is allowed for this identity. This status
    // must be stored by the app. UnprotectAsync must only be called if the buffer was
    // protected successfully earlier.
    bool isBufferProtected = 
        (result.ProtectionInfo.Status == DataProtectionStatus.Protected);
    IBuffer outputData = result.Buffer;

    // Store the data away...
    this.StoreBuffer(outputData, isBufferProtected);

    // ...and then later retrieve it.
    outputData = this.GetStoredBuffer(out isBufferProtected);

    string storedString = string.Empty;

    if (isBufferProtected)
    {
        result = await DataProtectionManager.UnprotectAsync(outputData);

        if (result.ProtectionInfo.Status != DataProtectionStatus.Unprotected)
        {
            // Code goes here to handle the situation where the buffer
            // is no longer accessible.
            return;
        }
        outputData = result.Buffer;
    }
    this.resultDataTextBlock.Text = CryptographicBuffer.ConvertBinaryToString(encoding, outputData);
}
```

## Применение политики пользовательского интерфейса на основе защищенного удостоверения потока или буфера


Если ваше приложение должно отобразить содержимое защищенного потока или буфера в своем пользовательском интерфейсе, оно должно включить реализацию политики пользовательского интерфейса на основе удостоверения защиты ресурса. Необходимо запросить данные о защите ресурса и включить системную реализацию политики пользовательского интерфейса из полученного удостоверения.

```CSharp
using Windows.Security.EnterpriseData;
using Windows.Storage.Streams;

...

private async void EnableUIPolicyFromProtectedBuffer(IBuffer buffer)
{
    DataProtectionInfo protectionInfo = 
        await DataProtectionManager.GetProtectionInfoAsync(buffer);

    if (protectionInfo.Status != DataProtectionStatus.Protected)
    {
        // In this case, the app has lost access to the buffer
        // (ProtectedToOtherIdentity, Revoked). This must be handled.
        // &#39;Unprotected&#39; is never returned for GetProtectionInfoAsync().
        return;
    }

    ProtectionPolicyManager.TryApplyProcessUIPolicy(protectionInfo.Identity);
}

```

**Примечание.**  Эта статья адресована разработчикам приложений для Windows 10 на базе универсальной платформы Windows (UWP). При разработке приложений для Windows 8.x или Windows Phone 8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

## Связанные разделы


[Пример защиты корпоративных данных (EDP)](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409)

[**Пространство имен Windows.Security.EnterpriseData**](https://msdn.microsoft.com/library/windows/apps/dn279153)

 

 





<!--HONumber=Mar16_HO5-->


