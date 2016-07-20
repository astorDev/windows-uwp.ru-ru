---
author: TylerMSFT
Description: "В этом разделе представлены примеры задач по созданию кода для наиболее распространенных сценариев защиты корпоративных данных (EDP) на уровне файлов."
MS-HAID: dev\_files.protect\_your\_enterprise\_data\_with\_edp
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: "Использование защиты корпоративных данных (EDP) для защиты файлов"
translationtype: Human Translation
ms.sourcegitcommit: 9b9e9ecb70f3a0bb92038ae94f45ddcee3357dbd
ms.openlocfilehash: a31fc65599f43be5b302b568774a51ab77065300

---

# Использование защиты корпоративных данных (EDP) для защиты файлов

> [!NOTE]
> Политику защиты корпоративных данных (EDP) нельзя применить в Windows 10 версии 1511 (сборка 10586) или более ранних версий.

В этом разделе представлены примеры задач по созданию кода для наиболее распространенных сценариев защиты корпоративных данных (EDP) на уровне файлов. Полное описание взаимосвязи EDP с файлами, потоками, буфером обмена, сетями, фоновыми задачами и защитой данных в режиме блокировки для разработчиков см. в разделе [Защита корпоративных данных (EDP)](../enterprise/edp-hub.md).

> [!NOTE]
> В [примере защиты корпоративных данных (EDP)](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409) рассматриваются многие сценарии, обсуждаемые в этом разделе.

## Необходимые условия

-   **Настройка для EDP**

    См. раздел [Настройка компьютера для EDP](../enterprise/edp-hub.md#set-up-your-computer-for-EDP).

-   **Создание корпоративно-грамотного приложения**

    Приложение, которое автономно гарантирует, что корпоративные данные останутся под управлением предприятия, называют корпоративно-грамотным приложением. Грамотное приложение более мощное, функциональное, гибкое и надежное, чем неграмотное. Ваше приложение сообщает системе, что оно грамотное, объявляя ограниченную возможность **enterpriseDataPolicy**. Но чтобы приложение было грамотным, недостаточно просто объявить соответствующую возможность. Подробнее: [Корпоративно-грамотные приложения](../enterprise/edp-hub.md#enterprise-enlightened-apps).

-   **Общее представление об асинхронном программировании для приложений универсальной платформы Windows (UWP)**

    Описание процесса написания асинхронных приложений на C\# или Visual Basic см. в статье [Вызов асинхронных API в C\# и Visual Basic](https://msdn.microsoft.com/library/windows/apps/mt187337). Сведения о создании асинхронных приложений на C++ см. в статье [Асинхронное программирование на языке C++](https://msdn.microsoft.com/library/windows/apps/mt187334).

## Путь к локальной папке и просмотр защищенных файлов в проводнике


Вы можете создать приложение для размещения примеров кода из этого раздела, чтобы попробовать их в деле. Примеры кода записывают файлы в локальную папку приложения; при этом ее точное расположение на диске уникально для вашего приложения. Чтобы указать расположение локальной папки приложения, добавьте следующий код.

```CSharp
// Put a breakpoint on the line after the line of code below. Use the value of localFolderPath
// in File Explorer to find the output file that the later code examples create.
string localFolderPath = ApplicationData.Current.LocalFolder.Path;
```

Получив путь, вы сможете использовать проводник для поиска файлов, которые создает ваше приложение. Так вы сможете убедиться, что они защищены и доступны только соответствующему удостоверению.

В проводнике выберите **Изменить параметры папок и поиска** и на вкладке **Вид** установите флажок **Отображать зашифрованные файлы другим цветом**. Также используйте команду **Вид**&gt;**Добавить столбцы** проводника, чтобы добавить столбец **Зашифровано в** и увидеть удостоверение предприятия, для которого вы защищаете файлы.

## Защита корпоративных данных в новом файле (для интерактивного приложения)


Существует множество способов, с помощью которых корпоративные данные могут попасть в ваше приложение, в том числе из некоторых конечных точек сети, файлов, буфера обмена или контракта отправки данных. Ваше приложение также может создавать новые корпоративные данные. Какими бы способами корпоративные данные не попадали бы в ваше грамотное приложение, оно должно защищать их для управляемого корпоративного удостоверения при их сохранении в новом файле.

Рекомендуется использовать API обычного хранилища для создания файла, использовать API EDP для защиты файла для корпоративного удостоверения, а затем (также с помощью API обычного хранилища) записать данные в файл. Обязательно защитите файл перед записью данных в него (как показано в примере ниже). Для защиты файла применяется метод [**FileProtectionManager.ProtectAsync**](https://msdn.microsoft.com/library/windows/apps/dn705157). И, как всегда, защищать данные имеет смысл только для управляемого удостоверения. Подробнее об этом и о том, как приложение может определить удостоверение предприятия, на котором оно запущено, см. в разделе [Подтверждение того, что удостоверение управляемое](../enterprise/edp-hub.md#confirming-an-identity-is-managed).

```CSharp
using Windows.Security.EnterpriseData;
using Windows.Storage;

...

private async void SaveEnterpriseDataToFile(string enterpriseData, string identity)
{
    // You should only protect to an identity that is managed.
    if (!ProtectionPolicyManager.IsIdentityManaged(identity)) return false;

    StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
    StorageFile storageFile = storageFolder.CreateFileAsync("sample.txt",
        CreationCollisionOption.ReplaceExisting);

    // It's important to protect a file *before* writing enterprise data to it.
    FileProtectionInfo fileProtectionInfo =
        await FileProtectionManager.ProtectAsync(storageFile, identity);

    // If the file is now protected, to the intended identity, then we can write to it.
    if (fileProtectionInfo.Identity == identity &&
        fileProtectionInfo.Status == FileProtectionStatus.Protected)
        await Windows.Storage.FileIO.WriteTextAsync(storageFile, enterpriseData);
}
```

## Защита корпоративных данных в новом файле (для фоновой задачи)


API [**FileProtectionManager.ProtectAsync**](https://msdn.microsoft.com/library/windows/apps/dn705157), который мы использовали в предыдущем разделе, подходит только для интерактивных приложений. Для фоновой задачи код может выполняться в режиме блокировки экрана. Организация может применять безопасную политику «защиты данных в режиме блокировки экрана» (DPL), при которой для доступа к защищенным ресурсам, временно удаленным из памяти устройства при блокировке экрана, необходимы ключи шифрования. Это предотвращает утечки данных при потере устройства. Данная функция также удаляет ключи, связанные с защищенными файлами, при закрытии их дескрипторов. Однако можно создать новые защищенные файлы во время окна блокировки (промежутка времени между блокировкой и разблокировкой устройства) и получить к ним доступ, не закрывая дескриптор файла. 
            **StorageFolder.CreateFileAsync** закрывает дескриптор при создании файла, поэтому данный алгоритм использовать нельзя.

1.  Создайте новый файл с помощью **StorageFolder.CreateFileAsync**.
2.  Зашифруйте его с помощью **FileProtectionManager.ProtectAsync**.
3.  Откройте файл и выполните запись в него.

Поскольку 1-й этап включает закрытие дескриптора файла (и даже если на 1-м этапе дескриптор не будет закрыт, он будет закрыт на 2-м этапе), 3-й этап невозможен, так как ключи шифрования для доступа к файлу уже недоступны.

Для обработки этого сценария можно использовать [**FileProtectionManager.CreateProtectedAndOpenAsync**](https://msdn.microsoft.com/library/windows/apps/dn705153) для создания защищенного файла и возвращения в него **IRandomAccessStream**. Это позволяет приложениям вносить несколько записей в файл, не закрывая дескриптор файла.

Пример кода демонстрирует элементарное почтовое приложение, которое записывает новый файл при получении письма. Почтовые приложения должны синхронизировать почту, когда устройство заблокировано. Когда это приложение получает новое сообщение, оно создает новый файл с помощью [**CreateProtectedAndOpenAsync**](https://msdn.microsoft.com/library/windows/apps/dn705153), получает выходной поток и записывает в него текст сообщения.

```CSharp
using Windows.Security.EnterpriseData;
using Windows.Storage;
using Windows.Storage.Streams;

...

private async void SaveEnterpriseDataToFile(string enterpriseData, string identity)
{
    // You should only protect to an identity that is managed.
    if (!ProtectionPolicyManager.IsIdentityManaged(identity)) return false;

    StorageFolder storageFolder = ApplicationData.Current.LocalFolder;

    ProtectedFileCreateResult protectedFileCreateResult =
        await FileProtectionManager.CreateProtectedAndOpenAsync(storageFolder,
            "sample.txt", identity, CreationCollisionOption.ReplaceExisting);

    // It's important to successfully protect a file *before* writing enterprise data to it.
    if (protectedFileCreateResult.ProtectionInfo.Identity == identity &&
        protectedFileCreateResult.ProtectionInfo.Status == FileProtectionStatus.Protected)
    {
        using (IOutputStream outputStream =
            protectedFileCreateResult.Stream.GetOutputStreamAt(0))
        {
            using (DataWriter writer = new DataWriter(outputStream))
            {
                writer.WriteString(enterpriseData);
                await writer.StoreAsync();
                await writer.FlushAsync();
            }
        }
    }
    else if (protectedFileCreateResult.ProtectionInfo.Status == FileProtectionStatus.AccessSuspended)
    {
        // Perform any special processing for the access suspended case.
    }
}
```

## Защита папки, которая содержит корпоративные данные


Если вы хотите, чтобы каждый элемент в папке был защищен, это также возможно. Можно использовать [**FileProtectionManager.ProtectAsync**](https://msdn.microsoft.com/library/windows/apps/dn705157) для защиты пустой папки. После этого все файлы и папки, создаваемые в данной папке, также будут защищены. Можно защитить существующую папку или создать новую папку для защиты (в примере ниже создается новая папка). Но в любом случае для успешной защиты папка должна быть пуста. В противном случае [**FileProtectionInfo.Status**](https://msdn.microsoft.com/library/windows/apps/dn705150) будет содержать значение [**FileProtectionStatus.NotProtectable**](https://msdn.microsoft.com/library/windows/apps/dn279147).

```CSharp
using Windows.Security.EnterpriseData;
using Windows.Storage;

...

private async void CreateANewFolderAndProtectItAsync(string folderName, string identity)
{
    if (!ProtectionPolicyManager.IsIdentityManaged(identity)) return false;

    StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
    StorageFolder newStorageFolder =
        await storageFolder.CreateFolderAsync(folderName);

    FileProtectionInfo fileProtectionInfo =
        await FileProtectionManager.ProtectAsync(newStorageFolder, identity);

    if (fileProtectionInfo.Identity != identity ||
        fileProtectionInfo.Status != FileProtectionStatus.Protected)
    {
        // Protection failed.
    }
}
```

## Защита от копирования из одного файла в другой


В этом сценарии уже существует защищенный файл для соответствующего корпоративного удостоверения. Вы можете без труда реплицировать данную защиту для другого файла. Вам даже не нужно знать, что это за удостоверение: вам необходимо только знать, что это нужное удостоверение.

Чтобы создать простую копию защищенного файла, можно вызывать [**StorageFile.CopyAsync**](https://msdn.microsoft.com/library/windows/apps/br227190). Полученная копия файла будет защищена так же, как и исходный файл.

Для защиты существующего незащищенного файла до записи в него корпоративных данных вместо вызова [**FileProtectionManager.ProtectAsync**](https://msdn.microsoft.com/library/windows/apps/dn705157) (что мы видели в предыдущем сценарии, где нужно было передать управляемое удостоверение) можно вызвать [**FileProtectionManager.CopyProtectionAsync**](https://msdn.microsoft.com/library/windows/apps/dn705152), как показано в примере кода.

```CSharp
using Windows.Security.EnterpriseData;
using Windows.Storage;

...

private async void CopyProtectionFromOneFileToAnother
    (StorageFile sourceStorageFile, StorageFile targetStorageFile)
{
    bool copyResult = await
        FileProtectionManager.CopyProtectionAsync(sourceStorageFile, targetStorageFile);

    if (!copyResult)
    {
        // Copying failed. To diagnose, you could check the file's status.
        // (call FileProtectionManager.GetProtectionInfoAsync and
        // check FileProtectionInfo.Status).
    }
}
```

## Обработка отказа в доступе к защищенному вами файлу


В этом случае приложение пытается получить доступ к файлу, который оно ранее защитило, и получает отказ. Необходимо проверить состояние файла, чтобы определить, в чем проблема. В этом примере кода приложение вызывает API [**FileProtectionManager.GetProtectionInfoAsync**](https://msdn.microsoft.com/library/windows/apps/dn705154), чтобы запросить состояние и определить, вызвано ли оно тем, что доступ к файлу был отменен в результате удаленного управления.

```CSharp
using Windows.Security.EnterpriseData;
using Windows.Storage;

...

private async System.Threading.Tasks.Task<bool> IsFileProtectionStatusRevoked
    (StorageFile storageFile)
{
    FileProtectionInfo fileProtectionInfo =
        await FileProtectionManager.GetProtectionInfoAsync(storageFile);

    if (fileProtectionInfo.Status == FileProtectionStatus.Revoked)
    {
        // Inform the user that their data has been revoked.
    }
    return fileProtectionInfo.Status == FileProtectionStatus.Revoked;
}
```

## Включение реализации политики пользовательского интерфейса на базе защищенного удостоверения файла


Если ваше приложение должно отобразить содержимое защищенного файла (например, PDF) в своем пользовательском интерфейсе, то оно должно включить реализацию политики пользовательского интерфейса на базе удостоверения защиты файла. Необходимо запросить данные о защите файла и включить системную реализацию политики пользовательского интерфейса из полученного удостоверения.

```CSharp
using Windows.Security.EnterpriseData;
using Windows.Storage;

...

private async void EnableUIPolicyFromFile(StorageFile storageFile)
{
    FileProtectionInfo fileProtectionInfo = 
        await FileProtectionManager.GetProtectionInfoAsync(storageFile);

    if (fileProtectionInfo.Status != FileProtectionStatus.Protected)
    {
        // No policy enforcement required, unless the file is inaccessible
        // (Revoked, ProtectedToOtherIdentity) in which case that should
        // be handled in an app-specific way.
        return;
    }

    ProtectionPolicyManager.TryApplyProcessUIPolicy(fileProtectionInfo.Identity);
}
```

> '!NOTE] Эта статья адресована разработчикам приложений для Windows10 на базе универсальной платформы Windows (UWP). При разработке приложений для Windows8.x или Windows Phone8.x см. раздел [архивной документации](http://go.microsoft.com/fwlink/p/?linkid=619132).

 

## Связанные разделы

- [Пример защиты корпоративных данных (EDP)](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409)

- [**Пространство имен Windows.Security.EnterpriseData**](https://msdn.microsoft.com/library/windows/apps/dn279153)








<!--HONumber=Jul16_HO1-->


