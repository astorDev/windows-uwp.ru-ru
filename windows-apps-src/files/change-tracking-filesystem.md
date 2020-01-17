---
title: Отслеживание изменений файловой системы в фоновом режиме
description: Описывается, как отслеживать изменения в файлах и папках в фоновом режиме, когда пользователи перемещают их в системе.
ms.date: 12/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1cef2fb660681d3e382eb8ca7dcb92456756f627
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75685236"
---
# <a name="track-file-system-changes-in-the-background"></a>Отслеживание изменений файловой системы в фоновом режиме

**Важные API**

-   [**StorageLibraryChangeTracker**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageLibraryChangeTracker)
-   [**StorageLibraryChangeReader**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader)
-   [**StorageLibraryChangedTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.StorageLibraryContentChangedTrigger)
-   [**StorageLibrary**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary)

Класс [**StorageLibraryChangeTracker**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageLibraryChangeTracker) позволяет приложениям отслеживать изменения в файлах и папках, когда пользователи перемещают их в системе. С помощью класса **StorageLibraryChangeTracker** приложение может отслеживать:

- операции с файлами, включая добавление, удаление и изменение;
- операции с папками, такие как переименование и удаление;
- перемещение файлов и папок на диске.

В этом руководстве описывается модель программирования для работы со средством отслеживания изменений, приводятся примеры кода и рассматриваются различные типы операций с файлами, которые отслеживаются с помощью **StorageLibraryChangeTracker**.

**StorageLibraryChangeTracker** подходит для библиотек пользователей или для любой папки на локальном компьютере. Поддерживаются дополнительные и съемные диски, но не поддерживаются диски NAS или сетевые диски.

## <a name="using-the-change-tracker"></a>Использование средства отслеживания изменений

Средство отслеживания изменений реализуется в системе как кольцевой буфер, содержащий последние *N* операций файловой системы. Приложения могут считывать изменения из буфера, а затем обрабатывать их и использовать для каких-либо своих функций. После того, как приложение завершит обработку изменений, оно помечает их как обработанные и больше никогда не увидит их снова.

Чтобы использовать средство отслеживания изменений для папки, выполните следующие действия.

1. Включите отслеживание изменений для папки.
2. Дождитесь изменений.
3. Выполните чтение изменений.
4. Примите изменения.

В следующих разделах подробно рассматривается каждый из этих шагов и приводятся примеры кода. Полный пример кода приведен в конце этой статьи.

### <a name="enable-the-change-tracker"></a>Включение средства отслеживания изменений

Первое, что приложению требуется сделать, — сообщить системе, что его интересует отслеживание изменений в определенной библиотеке. Для этого вызывается метод [**Enable**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) средства отслеживания изменений для выбранной библиотеки.

```csharp
StorageLibrary videosLib = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
StorageLibraryChangeTracker videoTracker = videosLib.ChangeTracker;
videoTracker.Enable();
```

Несколько важных замечаний:

- Убедитесь, что у приложения в манифесте есть разрешение для соответствующей библиотеки, прежде чем создавать объект [**StorageLibrary**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary). Дополнительные сведения см. в разделе [Разрешения на доступ к файлам](https://docs.microsoft.com/windows/uwp/files/file-access-permissions).
- Метод [**Enable**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) является потокобезопасным и не приведет к сбросу указателя. Его можно вызывать сколько угодно раз (мы рассмотрим его подробнее позже).

![Включение пустого средства отслеживания изменений](images/changetracker-enable.png)

### <a name="wait-for-changes"></a>Ожидание изменений

После инициализации средства отслеживания изменений оно начнет записывать все операции в библиотеке, даже когда приложение не выполняется. Приложения можно зарегистрировать, чтобы они активировались каждый раз, когда происходит изменение. Для этого их нужно зарегистрировать для события [**StorageLibraryChangedTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.StorageLibraryContentChangedTrigger).

![Добавление изменений в средство отслеживания изменений без считывая их приложением](images/changetracker-waiting.png)

### <a name="read-the-changes"></a>Чтение изменений

Приложение может выполнить опрос для проверки наличия изменений в средстве отслеживания изменений и получить список изменений со времени последней проверки. В приведенном ниже коде показано, как получить список изменений из средства отслеживания изменений.

```csharp
StorageLibrary videosLibrary = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
videosLibrary.ChangeTracker.Enable();
StorageLibraryChangeReader videoChangeReader = videosLibrary.ChangeTracker.GetChangeReader();
IReadOnlyList changeSet = await changeReader.ReadBatchAsync();
```

После этого приложение отвечает за обработку изменений для использования в собственных функциях или базе данных, если это необходимо.

![Чтение изменений из средства отслеживания изменений в базу данных приложения](images/changetracker-reading.png)

> [!TIP]
> Второй вызов для включения предназначен для защиты от состязания в случае, если пользователь добавляет другую папку в библиотеку, пока приложение считывает изменения. Если пользователь изменяет папки в своей библиотеке, то без дополнительного вызова **Enable** код завершится ошибкой ecSearchFolderScopeViolation (0x80070490).

### <a name="accept-the-changes"></a>Принятие изменений

Когда приложение завершит обработку изменений, оно должно указать системе больше не показывать эти изменения, вызвав метод [**AcceptChangesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.acceptchangesasync).

```csharp
await changeReader.AcceptChangesAsync();
```

![Пометка изменений как прочитанных, чтобы они больше не отображались](images/changetracker-accepting.png)

Теперь при чтении данных из средства отслеживания изменений приложение будет получать только новые изменения.

- Если изменения произошли между вызовом [**ReadBatchAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.readbatchasync) и [AcceptChangesAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.acceptchangesasync), указатель будет перемещен на последнее изменение, считанное приложением. Остальные изменения все еще будут доступны, когда оно снова вызовет метод **ReadBatchAsync**.
- Если не принять изменения, система вернет тот же набор изменений в следующий раз, когда приложение вызовет **ReadBatchAsync**.

## <a name="important-things-to-remember"></a>Важные замечания

При использовании средства отслеживания изменений следует помнить несколько моментов, чтобы все работало правильно.

### <a name="buffer-overruns"></a>Переполнение буфера

Несмотря на то, что мы пытаемся резервировать достаточно места в средстве отслеживания изменений для хранения всех операций в системе, пока ваше приложение их не считает, очень просто представить себе ситуацию, когда приложение не считало изменения перед тем, как циклический буфер был перезаписан. Особенно в том случае, если пользователь восстанавливает данные из резервной копии или синхронизирует большую коллекцию изображений из телефона с камерой.

В этом случае **ReadBatchAsync** вернет код ошибки [**StorageLibraryChangeType.ChangeTrackingLost**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetype). Если приложение получило этот код ошибки, это означает следующее:.

* Буфер был перезаписан после последнего обращения приложения к нему. Оптимальным решением является выполнить обход контента библиотеки, так как любая информация из средства отслеживания будет неполной.
* Средство отслеживания изменений не вернет какие-либо дополнительные изменения, пока не будет вызван метод [**Reset**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.reset). После того, как приложение вызовет сброс, указатель переместится на последнее изменение, и отслеживание возобновится в обычном режиме.

Такие ситуации должны возникать редко, но в сценариях, когда пользователь перемещает много файлов на диске, мы не хотим, чтобы данные средства отслеживания изменений разрослись и заняли слишком много места на диске. Это позволит приложениям реагировать на большое число операций файловой системы, не нарушая работу пользователей в Windows.

### <a name="changes-to-a-storagelibrary"></a>Изменения в StorageLibrary

Класс [**StorageLibrary**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary) существует в виде виртуальной группы корневых папок, содержащих другие папки. Чтобы согласовать это со средством отслеживания изменений файловой системы, мы добавили следующие возможности выбора.

- Любые изменения нисходящих элементов корневых папок библиотеки будут представлены в средстве отслеживания изменений. Корневые папки библиотеки можно найти с помощью свойства [**Folders**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.folders).
- Добавление или удаление корневых папок в **StorageLibrary** (с помощью [**RequestAddFolderAsync**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.requestaddfolderasync) и [**RequestRemoveFolderAsync** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.requestremovefolderasync)) не приводит к созданию записи в средстве отслеживания изменений. Эти изменения можно отследить с помощью события [**DefinitionChanged**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.definitionchanged) или путем перечисления корневых папок в библиотеке с использованием свойства [**Folders**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.folders).
- Если папка с содержимым добавляется в библиотеку, не будет создаваться уведомление об изменении или записи в средстве отслеживания изменений. Все последующие изменения нисходящих элементов этой папки будут вызывать создание уведомлений и записей в средстве отслеживания изменений.

### <a name="calling-the-enable-method"></a>Вызов метода Enable

Приложения должны вызывать метод [**Enable**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) сразу после начала отслеживания файловой системы и перед каждым перечислением изменений. Это гарантирует, что все изменения будут записаны средством отслеживания изменений.  

## <a name="putting-it-together"></a>Подведем итог

Ниже приведен весь код, который используется для регистрации для получения изменений в библиотеке видео и начала получения изменений из средства отслеживания изменений.

```csharp
private async void EnableChangeTracker()
{
    StorageLibrary videosLib = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
    StorageLibraryChangeTracker videoTracker = videosLib.ChangeTracker;
    videoTracker.Enable();
}

private async void GetChanges()
{
    StorageLibrary videosLibrary = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
    videosLibrary.ChangeTracker.Enable();
    StorageLibraryChangeReader videoChangeReader = videosLibrary.ChangeTracker.GetChangeReader();
    IReadOnlyList changeSet = await changeReader.ReadBatchAsync();


    //Below this line is for the blog post. Above the line is for the magazine
    foreach (StorageLibraryChange change in changeSet)
    {
        if (change.ChangeType == StorageLibraryChangeType.ChangeTrackingLost)
        {
            //We are in trouble. Nothing else is going to be valid.
            log("Resetting the change tracker");
            videosLibrary.ChangeTracker.Reset();
            return;
        }
        if (change.IsOfType(StorageItemTypes.Folder))
        {
            await HandleFileChange(change);
        }
        else if (change.IsOfType(StorageItemTypes.File))
        {
            await HandleFolderChange(change);
        }
        else if (change.IsOfType(StorageItemTypes.None))
        {
            if (change.ChangeType == StorageLibraryChangeType.Deleted)
            {
                RemoveItemFromDB(change.Path);
            }
        }
    }
    await changeReader.AcceptChangesAsync();
}
```
