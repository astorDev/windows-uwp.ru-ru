---
title: Отслеживание изменений файловой системы в фоновом режиме
description: Описывается, как отслеживать изменения в файлы и папки в фоновом режиме, как пользователи перемещать их системы.
ms.date: 11/20/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e90692753924572a932767b9c188ed6d24f94593
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8892145"
---
# <a name="track-file-system-changes-in-the-background"></a>Отслеживание изменений файловой системы в фоновом режиме

Класс [StorageLibraryChangeTracker](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageLibraryChangeTracker) позволяет приложениям отслеживать изменения в файлы и папки, как пользователям перемещать их системы. С помощью класса **StorageLibraryChangeTracker** , приложение может отслеживать:

- Файловые операции, включая добавление, удаление и изменение.
- Папка таких операций, удаления и переименования.
- Файлы и папки, переход на диске.

Используйте это руководство Узнайте модель программирования для работы с объект отслеживания изменений, просмотреть пример кода и ознакомиться с различными типами файлов операций, которые будут отслеживаться с **StorageLibraryChangeTracker**.

**StorageLibraryChangeTracker** работает для библиотек пользователя или для любой папки на локальном компьютере. Это включает в себя дополнительные диски или съемные носители, но не включает NAS дисков и сетевых дисков.

## <a name="using-the-change-tracker"></a>С помощью объект отслеживания изменений

Объект отслеживания изменений реализуется в системе как кольцевой буфер хранения последнего операций *N* файловой системы. Приложения могут читать изменения за пределами буфера и затем обработать их в собственные взаимодействия. После завершения приложения с изменениями его отмечает изменения при обработке и никогда не будут видеть снова.

Чтобы использовать объект отслеживания изменений для папки, выполните следующие действия.

1. Включите отслеживание изменений для папки.
2. Подождите, пока изменения.
3. Чтение изменения.
4. Принимаете изменения.

В этом руководстве следующих разделах через все этапы с примерами кода. Полный пример кода приведен в конце данной статьи.

### <a name="enable-the-change-tracker"></a>Включить объект отслеживания изменений

— Это первое, что приложению требуется, чтобы сообщить системе, что он является заинтересованы в определенной библиотеке отслеживания изменений. Это достигается путем вызова метода [Включить](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) на объект отслеживания изменений для использования в библиотеке объектов на карте.

```csharp
StorageLibrary videosLib = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
StorageLibraryChangeTracker videoTracker = videosLib.ChangeTracker;
videoTracker.Enable();
```

Некоторые важные сведения.

- Убедитесь, что ваше приложение имеет разрешение на соответствующую библиотеку в манифесте перед созданием [StorageLibrary](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary) объекта. Дополнительные сведения см. [Разрешения на доступ к файлам](https://docs.microsoft.com/en-us/windows/uwp/files/file-access-permissions) .
- [Включение](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) является потокобезопасной, не приведет к сбросу указатель и может быть вызван сколько угодно вам нравится (об этом подробнее позже).

![Включение регистрации пустой изменение](images/changetracker-enable.png)

### <a name="wait-for-changes"></a>Подождите, пока изменения

После инициализации объект отслеживания изменений начнет записывает все операции, которые возникают в библиотеку, даже в том случае, если приложение не выполняется. Приложения могут регистрировать необходимо активировать всякий раз, когда изменяется путем регистрации для событий [StorageLibraryChangedTrigger](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.StorageLibraryContentChangedTrigger) .

![Изменения, добавляемого объект отслеживания изменений без их чтения приложения](images/changetracker-waiting.png)

### <a name="read-the-changes"></a>Чтение изменения

Приложение можно запросить изменения из объект отслеживания изменений и получать список изменения с момента последнего он установлен. В следующем примере кода показано, как получить список изменений из объект отслеживания изменений.

```csharp
StorageLibrary videosLibrary = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
videosLibrary.ChangeTracker.Enable();
StorageLibraryChangeReader videoChangeReader = videosLibrary.ChangeTracker.GetChangeReader();
IReadOnlyList changeSet = await changeReader.ReadBatchAsync();
```

Затем приложение отвечает за обработку изменения в свой собственный возможностями или базы данных при необходимости.

![Считывание изменения из объект отслеживания изменений в базе данных приложения](images/changetracker-reading.png)

> [!TIP]
> Второй вызов для включения — противодействие гонки, если пользователь добавляет другую папку в библиотеку, когда ваше приложение выполняет чтение изменения. Без дополнительных вызов для **включения** кода приведет к сбою с ecSearchFolderScopeViolation (0x80070490) Если пользователь изменяет папок в библиотеке

### <a name="accept-the-changes"></a>Принять изменения

После его завершения обработки изменений, оно должно сообщить системе о необходимости никогда не показывать эти изменения, вызвав метод [AcceptChangesAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.acceptchangesasync) .

```csharp
await changeReader.AcceptChangesAsync();
```

![Отметка изменения как чтение, они никогда не будут отображаться снова](images/changetracker-accepting.png)

Приложения теперь получит только новые изменения при чтении объект отслеживания изменений в будущем.

- Если изменения произошло между вызывающего [ReadBatchAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.readbatchasync) и [AcceptChangesAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.acceptchangesasync), указатель будет только опытным последнее изменение показано приложение. Эти другие изменения по-прежнему будут доступны следующий раз, когда он вызывает **ReadBatchAsync**.
- Не принимает изменения приведет к система возвращает один и тот же набор изменений в следующий раз, приложение вызывает **ReadBatchAsync**.

## <a name="important-things-to-remember"></a>Необходимо помнить

Если вы используете объект отслеживания изменений, существует несколько вещей, которые следует иметь в виду убедитесь, что все работает правильно.

### <a name="buffer-overruns"></a>Переполнений буфера

Несмотря на то, что мы стараемся зарезервировать достаточно места в объект отслеживания изменений для хранения всех операций, возникающих в системе, пока ваше приложение может считывать их, это очень легко представьте сценарий, в которых приложение не читает изменения до самого перезаписывает циклического буфера. Особенно в том случае, если пользователь восстановление данных из резервной копии или синхронизации для большой коллекции изображений с телефона камеры.

В этом случае **ReadBatchAsync** вернет код ошибки [StorageLibraryChangeType.ChangeTrackingLost](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetype). Если ваше приложение получает этот код ошибки, это означает следующее:

* Буфер, перезаписывает сам с момента последнего окончательная его. Лучший план действий является выполнить обход в библиотеке, так как любые сведения в инспекторе, будут неполными.
* Объект отслеживания изменений не возвращает все дополнительные изменения после вызова [вернуть в исходное состояние](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.reset). После приложение вызывает сброс, указатель мыши перемещается в последнее изменение и отслеживания будет возобновлен обычным образом.

Она должна быть редких для получения этих случаев, но в сценариях, где пользователь перемещается большого количества файлов на диске вокруг мы не хотим объект отслеживания изменений выноска и занимают слишком много хранилища. Это должно позволяют реагировать на операций значительное файловой системы, при этом не повреждение взаимодействие с клиентом в Windows.

### <a name="changes-to-a-storagelibrary"></a>Изменения StorageLibrary

Класс [StorageLibrary](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary) существует в виде виртуальных группе корневой папки, содержащие другие папки. Чтобы согласовать это с помощью объект отслеживания изменений файлов системы, мы сделали один из следующих вариантов:

- Любые изменения потомком корневой папки библиотеки будут представлены в объект отслеживания изменений. Корневой папки библиотеки можно найти с помощью свойства [папки](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.folders) .
- Добавление или удаление корневой папки **StorageLibrary** (с помощью [RequestAddFolderAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.requestaddfolderasync) и [RequestRemoveFolderAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.requestremovefolderasync)) не создает запись в объект отслеживания изменений. Эти изменения могут отслеживаться через события [DefinitionChanged](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.definitionchanged) или перечисление корневой папки в библиотеку с помощью свойства [папки](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.folders) .
- Если папка с содержимым в нем уже добавляется в библиотеку, не будет изменение уведомления или изменение регистрации записям, созданным. Любые последующие изменения потомков этой папке будут создавать уведомления и измените регистрация записей.

### <a name="calling-the-enable-method"></a>Вызов метода включить

Приложения должны вызывать [Включить](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) сразу же после их запуска отслеживания в файловой системе и до каждого перечисления изменений. Это позволит гарантировать, что все изменения будут записаны, объект отслеживания изменений.  

## <a name="putting-it-together"></a>Чтобы продемонстрировать вместе

Вот весь код, используемый для Регистрация изменений в библиотеке видео и запустите похожий изменения из объект отслеживания изменений.

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
