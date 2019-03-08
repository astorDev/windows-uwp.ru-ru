---
title: Отслеживание изменений файловой системы в фоновом режиме
description: Описание действий по отслеживанию изменений в файлы и папки в фоновом режиме как пользователям перемещать их системы.
ms.date: 12/19/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b0ec7762fd64f0f0b8de65faa1aaf079bdaba3a3
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57621579"
---
# <a name="track-file-system-changes-in-the-background"></a>Отслеживание изменений файловой системы в фоновом режиме

**Важные API**

-   [**StorageLibraryChangeTracker**](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageLibraryChangeTracker)
-   [**StorageLibraryChangeReader**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader)
-   [**StorageLibraryChangedTrigger**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.StorageLibraryContentChangedTrigger)
-   [**StorageLibrary**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary)

[ **StorageLibraryChangeTracker** ](https://docs.microsoft.com/uwp/api/Windows.Storage.StorageLibraryChangeTracker) класс позволяет приложения отслеживать изменения в файлы и папки, как пользователи перемещать их системы. С помощью **StorageLibraryChangeTracker** класса, можно отслеживать приложения:

- Файловые операции, включая добавление, удаление и изменение.
- Папка операции, такие как переименование и удаление.
- Файлы и папки, перемещение на диске.

Это руководство используется для сведения о модели программирования для работы с отслеживанием изменений, просмотреть пример кода и ознакомиться с различными типами файловых операций, которые отслеживаются по **StorageLibraryChangeTracker**.

**StorageLibraryChangeTracker** работает для библиотек пользователей или для любой папки на локальном компьютере. Это включает в себя дополнительный диски и съемные диски, но не включает NAS дисков или сетевые диски.

## <a name="using-the-change-tracker"></a>С помощью отслеживания изменений

Объект отслеживания изменений реализуется в системе как кольцевой буфер хранения последнего *N* операциях файловой системы. Приложения, могут считать изменения из буфера, а затем обрабатывать их в свои собственные визуальные элементы. После завершения приложения с учетом изменений, он помечает изменения, обработки и никогда не будет снова увидеть их.

Чтобы использовать объект отслеживания изменений в папке, выполните следующие действия.

1. Включите отслеживание изменений для папки.
2. Дождитесь изменения.
3. Чтение изменений.
4. Примите изменения.

В следующих разделах подробно рассмотрим каждый из шагов с примерами кода. Полный пример кода приведен в конце этой статьи.

### <a name="enable-the-change-tracker"></a>Включение отслеживания изменений

Первое, что приложению требуется только является указывает системе, что он заинтересован в определенной библиотеке отслеживания изменений. Это достигается путем вызова [ **включить** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) метод на объект отслеживания изменений для библиотеки интерес.

```csharp
StorageLibrary videosLib = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
StorageLibraryChangeTracker videoTracker = videosLib.ChangeTracker;
videoTracker.Enable();
```

Несколько важные примечания.

- Убедитесь, что приложение имеет разрешение в правильную библиотеку в манифесте перед созданием [ **StorageLibrary** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary) объекта. См. в разделе [разрешения доступа](https://docs.microsoft.com/en-us/windows/uwp/files/file-access-permissions) для получения дополнительных сведений.
- [**Включить** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) является потокобезопасным и не приведет к сбросу указатель можно вызывать столько раз, сколько угодно (Подробнее об и этом позже).

![Включение отслеживания изменений пустой](images/changetracker-enable.png)

### <a name="wait-for-changes"></a>Дождитесь изменения

После инициализации объекта отслеживания изменений, он начинает регистрировать все операции, которые происходят в библиотеку, даже в том случае, если приложение не запущено. Приложения могут регистрироваться, чтобы быть активирована в любое время, изменяется путем регистрации для [ **StorageLibraryChangedTrigger** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.StorageLibraryContentChangedTrigger) событий.

![Изменения, добавляемый объект отслеживания изменений, не считывая их приложение](images/changetracker-waiting.png)

### <a name="read-the-changes"></a>Чтение изменений

Приложение можно выполнить опрос для изменения из объекта отслеживания изменений и получать список изменений со времени последнего его проверить. В приведенном ниже коде показано, как получить список изменений из объекта отслеживания изменений.

```csharp
StorageLibrary videosLibrary = await StorageLibrary.GetLibraryAsync(KnownLibraryId.Videos);
videosLibrary.ChangeTracker.Enable();
StorageLibraryChangeReader videoChangeReader = videosLibrary.ChangeTracker.GetChangeReader();
IReadOnlyList changeSet = await changeReader.ReadBatchAsync();
```

Приложение отвечает за обработку изменений в собственную базу данных, при необходимости или возможности.

![Считывать изменения из объекта отслеживания изменений в базу данных приложения](images/changetracker-reading.png)

> [!TIP]
> Второй вызов для включения предназначен для защиты от гонки, если пользователь добавляет другую папку в библиотеку, пока приложение считывает изменения. Без дополнительного обращения к **включить** код завершится с ecSearchFolderScopeViolation (0x80070490), если изменяется папки в своей библиотеке пользователем

### <a name="accept-the-changes"></a>Принять изменения

После завершения приложения обработки изменений, он должен указать системе больше не показывать эти изменения, вызвав [ **AcceptChangesAsync** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.acceptchangesasync) метод.

```csharp
await changeReader.AcceptChangesAsync();
```

![Пометки изменения, как чтение, поэтому они никогда не отображаются еще раз](images/changetracker-accepting.png)

Приложение теперь получите только новые изменения при чтении объект отслеживания изменений в будущем.

- Если изменения произошли между вызовом [ **ReadBatchAsync** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.readbatchasync) и [AcceptChangesAsync](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangereader.acceptchangesasync), указатель будет только доходить до последних изменений видел приложение. Этих изменений будет по-прежнему доступны при очередном он вызывает **ReadBatchAsync**.
- Не принимает изменения приведут к система возвращается тот же набор изменений в следующий раз, когда приложение вызывает **ReadBatchAsync**.

## <a name="important-things-to-remember"></a>Необходимо помнить

При использовании отслеживания изменений, есть несколько вещей, которые следует помнить, чтобы убедиться в том, что все работает правильно.

### <a name="buffer-overruns"></a>Переполнение буфера

Несмотря на то, что мы пытаемся зарезервировать достаточно места в объекте отслеживания изменений для хранения всех операций, происходящих в системе, пока ваше приложение может считывать их, это очень просто Представьте себе ситуацию, где приложение не считывает изменения, перед циклического буфера перезаписывает сам. Особенно в том случае, если пользователь восстановление данных из резервной копии или синхронизируется большое количество изображений из ее камерой мобильного телефона.

В этом случае **ReadBatchAsync** вернет код ошибки [ **StorageLibraryChangeType.ChangeTrackingLost**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetype). Если приложение получает этот код ошибки, это означает следующее:

* Буфер, перезаписывает сам со времени последнего, что и его. Оптимальным решением является выполнить обход библиотеке, так как любой информации из средство отслеживания может быть неполным.
* Объект отслеживания изменений не вернет любые дополнительные изменения, пока не будет вызван [ **Сброс**](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.reset). После приложение вызывает сброс, указатель мыши перемещается в последних изменений и отслеживание возобновляться.

Она должна возникать редко, для получения этих случаях, но в сценариях, где пользователь перемещается много файлов на диске мы не хотим привести к значительному увеличению и занимают слишком много хранения отслеживания изменений. Это позволит приложениям реагировать на операции большие файловой системы при не повреждает работы пользователя в Windows.

### <a name="changes-to-a-storagelibrary"></a>Изменения в StorageLibrary

[ **StorageLibrary** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary) класс существует в виде виртуальной группы из корневой папки, содержащие другие папки. Чтобы согласовать это с помощью состояния объекта отслеживания изменений файла системы, мы внесли следующие варианты:

- Любые изменения потомком корневые папки библиотеки будут представлены в объекте отслеживания изменений. Корневые папки библиотеки можно найти, используя [ **папки** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.folders) свойство.
- Добавление или удаление корневые папки из **StorageLibrary** (через [ **RequestAddFolderAsync** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.requestaddfolderasync) и [ **RequestRemoveFolderAsync**  ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.requestremovefolderasync)) не создает запись в объект отслеживания изменений. Эти изменения можно отследить с помощью [ **DefinitionChanged** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.definitionchanged) событий или путем перечисления корневые папки в библиотеке с использованием [ **папки** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrary.folders) свойство.
- Если папка с содержимым уже в нем добавляется в библиотеку, не будет изменений уведомлений или изменение записей отслеживания создан. Все последующие изменения к потомкам из этой папки будет создавать уведомления и изменить записи отслеживания.

### <a name="calling-the-enable-method"></a>Вызов метода Enable

Приложения должны вызывать [ **включить** ](https://docs.microsoft.com/uwp/api/windows.storage.storagelibrarychangetracker.enable) сразу же после их запуска, отслеживания в файловой системе и перед каждой перечисление изменений. Это позволит гарантировать, что все изменения будут отслеживаться с помощью отслеживания изменений.  

## <a name="putting-it-together"></a>Сводная информация

Ниже приведен весь код, который используется для регистрации изменениях в библиотеке видео и их изменения из объекта отслеживания изменений.

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
