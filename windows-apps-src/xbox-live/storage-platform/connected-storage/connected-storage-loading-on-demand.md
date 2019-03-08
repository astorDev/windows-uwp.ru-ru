---
title: Подключение хранилища загрузка по требованию
description: Узнайте, как для загрузки данных хранилища, подключенных по требованию, а не за один раз.
ms.assetid: a0797a14-c972-4017-864c-c6ba0d5a3363
ms.date: 02/27/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, подключенные устройства хранения
ms.localizationpriority: medium
ms.openlocfilehash: 31c1893f09e6d56682b4e718ee8b905ce72c7ad8
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57631719"
---
# <a name="connected-storage-loading-on-demand"></a>Подключение хранилища загрузка по требованию

`GetSyncOnDemandForUserAsync` позволяет загружать на основе облака данные из подключенных дискового пространства «по требованию» вместо того, чтобы все сразу. Это может повысить производительность по сравнению `GetForUserAsync` для случаев, где сохранит файл особенно велики.

## <a name="to-load-data-from-a-connected-storage-space-on-demand"></a>Для загрузки данных из подключенного дискового пространства по требованию

### <a name="1--call-getsyncondemandforuserasync"></a>1.  Вызов `GetSyncOnDemandForUserAsync`

Это активирует частичная синхронизация, который загружает список контейнеров и их метаданные из облака, но не их содержимое. Эта операция выполняется быстро и в условиях хорошее сетевое пользователь не увидит любой загрузки пользовательского интерфейса.

```cpp
auto op = ConnectedStorageSpace::GetSyncOnDemandForUserAsync(user);
op->Completed = ref new AsyncOperationCompletedHandler<ConnectedStorageSpace^>(
    [=](IAsyncOperation<ConnectedStorageSpace^>^ operation, Windows::Foundation::AsyncStatus status)
{
    switch (status)
    {
    case Windows::Foundation::AsyncStatus::Completed:
        auto storageSpace = operation->GetResults();
        break;
    case Windows::Foundation::AsyncStatus::Error:
    case Windows::Foundation::AsyncStatus::Canceled:
        // Present user option: ?Would you like to continue without saving progress??
        if (GetUserInputYesOrNo())
            SetGameState(LoadSaveState::NO_SAVE_MODE);
        else
            SetGameState(LoadSaveState::RETRY_LOAD);
        break;
    }
});
```

```csharp
var users = await Windows.System.User.FindAllAsync();

GameSaveProvider gameSaveProvider;

GameSaveProviderGetResult gameSaveTask = await GameSaveProvider.GetSyncOnDemandForUserAsync(users[0], context.AppConfig.ServiceConfigurationId); 
//Paramaters
//Windows.System.User user
//string SCID

if(gameSaveTask.Status == GameSaveErrorStatus.Ok)
{
    gameSaveProvider = gameSaveTask.Value;
}
```


### <a name="2--perform-a-container-query-using-getcontainerinfo2async"></a>2.  Выполнить запрос контейнера с помощью `GetContainerInfo2Async`

Возвращает коллекцию `ContainerInfo2`, который содержит 3 новые поля метаданных:

    -   `DisplayName`: Любой отображаемое имя, написанный с помощью перегрузки `SubmitUpdatesAsync` , принимающий в качестве параметра строку отображаемое имя. Мы рекомендуем всегда использовать это поле для хранения понятного имени.
    -   `LastModifiedTime`: Время последнего изменения этого контейнера. Обратите внимание, что в случае конфликтующие отметки времени локальные и удаленные, удаленного из них.
    -   `NeedsSync`: Типа boolean, если этот контейнер содержит данные, должен быть загружен из облака.

    Используя эти дополнительные метаданные, можно предоставить пользователю с базовую информацию о своих игр сохраняет (включая имя, время последнего использования и выбрав один будет, требуется ли загрузить) без фактического выполнения полной загрузки из облака.

```cpp
auto containerQuery = storageSpace->CreateContainerInfoQuery(nullptr); //return list of containers in ConnectedStorageSpace
auto queryOperation = containerQuery->GetContainerInfo2Async();

queryOperation->Completed = ref new AsyncOperationCompletedHandler<IVectorView<ContainerInfo2>^ >( 
    [=] (IAsyncOperation<IVectorView<ContainerInfo2>^ >^ operation, Windows::Foundation::AsyncStatus status)
    {
        switch (status)
        {
        case Windows::Foundation::AsyncStatus::Completed:
            // get the resulting vector of container info
            auto infoVector = operation->GetResults();
            break;
        case Windows::Foundation::AsyncStatus::Error:
        case Windows::Foundation::AsyncStatus::Canceled:
            // handle error cases
            break;
        }
    });
```

```csharp
GameSaveContainerInfoQuery infoQuery = gameSaveProvider.createContainerInfoQuery();
GameSaveContainerInfoGetResult containerInfoResult = await infoQuery.GetContainerInfoAsync();
var containerInfoList;

if(containerInfoResult.Status == GameSaveErrorStatus.Ok)
{
    containerInfoList = containerInfoResult.value;
}

// Use the containerInfoList to inform further actions or display container data to user. 
```

### <a name="3--trigger-a-sync"></a>3.  Запуск синхронизации

Synce подключенные хранилища будет инициироваться вызова любого из следующих существующий интерфейс API подключенные устройства хранения:

**C++**

    -   BlobInfoQueryResult::GetBlobInfoAsync
    -   BlobInfoQueryResult::GetItemCountAsync
    -   ConnectedStorageContainer::GetAsync
    -   ConnectedStorageContainer::ReadAsync
    -   ConnectedStorageSpace::DeleteContainerAsync

**C#**

    -   GameSaveBlobInfoQuery.GetBlobInfoAsync
    -   GameSaveBlobInfoQuery.GetItemCountAsync
    -   GameSaveContainer.GetAsync
    -   GameSaveContainer.ReadAsync
    -   GameSaveProvider.DeleteContainerAsync

В результате пользователю видеть панели обычные синхронизации пользовательского интерфейса и ход выполнения в ходе загрузки данных из выбранного контейнера. Обратите внимание, что только данные из выбранного контейнера является синхронизированным; данные из других контейнеров не загружается.

При вызове этих API в контексте синхронизации по требованию, эти операции могут давать следующие новые коды ошибок:

-   `ConnectedStorageErrorStatus::ContainerSyncFailed`(`GameSaveErrorStatus.ContainerSyncFailed` в UWP C# API): Эта ошибка означает, что произошел сбой операции и контейнер, не будут синхронизироваться с облаком. Наиболее вероятной причиной является условий сети пользователя, вызвавшего к сбою синхронизации. Они могут захотеть повторите попытку, когда они исправили своей сети, или они могут начать использовать контейнер, у них нет синхронизации. Пользовательский Интерфейс должен позволять любого из этих параметров. Диалоговое окно не повторных попыток не требуется, так как они будут уже видели диалогового окна "Повторить" пользовательского интерфейса системы.

-   `ConnectedStorageErrorStatus::ContainerNotInSync`(`GameSaveErrorStatus.ContainerNotInSync` в UWP C# API): Эта ошибка означает, что заголовок вашей некорректно использован для записи в контейнер несинхронизированные. Вызов `ConnectedStorageContainer::SubmitUpdatesAsync`(`GameSaveContainer.SubmitUpdatesAsync` в UWP C# API) для контейнера, который имеет флаг NeedsSync задано значение true не допускается. Должен сначала считывать контейнер, чтобы запустить синхронизацию перед записью в него. При записи в контейнер без чтения из него, вполне вероятно, название обнаруживается ошибка, где вы можете потерять ход реализации пользовательских.

Это поведение отличается от при воспроизведении автономного пользователя. При автономном режиме, нет никаких признаков того, из синхронизацию контейнеры, поэтому пользователь должен разрешать любые конфликты в дальнейшем. Таким образом однако система понимает, что пользователю необходимо синхронизировать, поэтому он не дают возможность получить в неисправном состоянии, с помощью устаревшей контейнера (хотя если им, они могут перезапустить заголовок и воспроизвести ее в автономный режим).

### <a name="4--use-the-rest-of-the-connected-storage-api-as-normal"></a>4:  Использовать оставшуюся часть API подключенного хранилища в обычном режиме

Поведение подключенного хранилища остается без изменений, при синхронизации по требованию.
