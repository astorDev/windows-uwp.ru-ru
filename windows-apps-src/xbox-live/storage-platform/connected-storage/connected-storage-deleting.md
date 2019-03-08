---
title: Использование хранилища, подключенных для удаления данных
description: Сведения об использовании хранилища, подключенных для удаления BLOB-объектов и контейнер данных.
ms.assetid: ccf7488c-5d55-480e-b3aa-412220d03104
ms.date: 02/27/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, подключенные устройства хранения
ms.localizationpriority: medium
ms.openlocfilehash: 756de46d05cdbf64d85491b4e8c6f783122f2356
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57601319"
---
# <a name="use-connected-storage-to-delete-data"></a>Использование хранилища, подключенных для удаления данных

Большие двоичные объекты данных удаляются асинхронно путем создания `ConnectedStorageContainer` в `ConnectedStorageSpace` для пользователя и вызова `SubmitUpdatesAsync` метод для предоставления списка строк, представляющих именованные больших двоичных объектов для удаления параметра blobsToDelete контейнера.

Контейнеры данных удаляются асинхронно путем создания `ConnectedStorageContainer` и вызов его `DeleteContainerAsync` метод.

## <a name="to-delete-blob-data-from-connected-storage"></a>Для удаления данных большого двоичного объекта из хранилища, подключения

1.  Получить `ConnectedStorageSpace` для пользователя путем вызова `GetForUserAsync`.

    В примере XDK, возвращенный `ConnectedStorageSpace` объект добавляется на карту, чтобы включить простое управление `ConnectedStorageSpace` объектов для нескольких пользователей.

2.  Создание `ConnectedStorageContainer` путем вызова метода `CreateContainer` на `ConnectedStorageSpace` объекта.
3.  Вызовите `SubmitUpdatesAsync` на `ConnectedStorageContainer` объекта.

## <a name="to-delete-a-container-from-connected-storage"></a>Чтобы удалить контейнер из подключенных хранилища

1.  Получить `ConnectedStorageSpace` для пользователя путем вызова `GetForUserAsync`.

    В примере XDK, возвращенный `ConnectedStorageSpace` объект добавляется на карту, чтобы включить простое управление `ConnectedStorageSpace` объектов для нескольких пользователей.

2.  Вызовите `DeleteContainerAsync` метод ConnectedStorageSpace методов.

## <a name="c-xdk-sample"></a>Пример C++ XDK
```cpp
auto gConnectedStorageSpaceForUsers = ref new Platform::Collections::Map<unsigned int, Windows::Xbox::Storage::ConnectedStorageSpace^>();

bool GetUserInputYesOrNo() { return true; };

User^ gCurrentUser;
IBuffer^ WrapRawBuffer(void* ptr, size_t size);

// Acquire a Connected Storage space for a user. A Connected Storage space is required to manipulate Connected Storage Data.
void PrepareConnectedStorage(User^ user)
{
  auto op = ConnectedStorageSpace::GetForUserAsync(user);
  op->Completed = ref new AsyncOperationCompletedHandler<ConnectedStorageSpace^>(
    [=](IAsyncOperation<ConnectedStorageSpace^>^ operation, Windows::Foundation::AsyncStatus status)
    {
      switch(status)
      {
        case Windows::Foundation::AsyncStatus::Completed:
          gConnectedStorageSpaceForUsers->Insert(user->Id, operation->GetResults());
          break;
        case Windows::Foundation::AsyncStatus::Error:
        case Windows::Foundation::AsyncStatus::Canceled:
          // Present user option: ?Would you like to continue without saving progress??
          if( GetUserInputYesOrNo() )
            //If the users opts yes, continue in offline mode
          else
            //If the users opts no, retry.
          break;
      }
    });
}

// Delete data from a fixed container/blob name into an IBuffer
void DeleteBlob(User^ user)
{

    //Create a list of blob names to delete
    std::vector<Platform::String^> blobsToDelete;

    //Add the blob name to your Generic Iterable
    blobsToDelete.push_back(L"someBlobName");

    auto blobNames = ref new Platform::Collections::VectorView<Platform::String^>(blobsToDelete);

    auto storageSpace = gConnectedStorageSpaceForUsers->Lookup(user->Id);
    auto container = storageSpace->CreateContainer("Saves/Checkpoint");

    //Delete is occurring asynchronously at this point.

    auto op = container->SubmitUpdatesAsync(nullptr, blobNames);

    op->Completed = ref new AsyncActionCompletedHandler(
        [=](IAsyncAction^ a, Windows::Foundation::AsyncStatus status){
            switch(status)
            {
                case Windows::Foundation::AsyncStatus::Completed:
                //Delete was successful no need for action
                break;
                case Windows::Foundation::AsyncStatus::Error:
                case Windows::Foundation::AsyncStatus::Canceled:
                // Handle failed deletion
            }
     });
}

void DeleteContainer(User^ user)
{
    auto storageSpace = gConnectedStorageSpaceForUsers->Lookup(user->Id);

    auto deleteOperation = storageSpace->DeleteContainerAsync("Saves/Checkpoint");

    deleteOperation->Completed = ref new AsyncActionCompletedHandler(
        [=](IAsyncAction^ a, Windows::Foundation::AsyncStatus status){
            switch(status)
            {
                case Windows::Foundation::AsyncStatus::Completed:
                //Delete was successful no need for action
                break;
                case Windows::Foundation::AsyncStatus::Error:
                case Windows::Foundation::AsyncStatus::Canceled:
                // Handle failed deletion
            }
     });

}
```

Можно найти XDK подключенных API-интерфейсов хранилища см. в CHM-файле XDK по пути: **Xbox один XDK >> Справочник по API >> Справочник по API платформы >> Справочник по API системы >> Windows.Xbox.Storage**.
API-интерфейсы XDK также описаны на [developer.microsoft.com сайта](https://developer.microsoft.com/en-us/games/xbox/docs/xdk/storage-xbox-microsoft-n).
Ссылку на API-интерфейсы XDK требует наличия Microsoft Account(MSA), разрешенное для доступа к Kit(XDK) разработчиков Xbox.
Windows.Xbox.Storage — имя пространства имен подключения хранилища для консолей Xbox One.


## <a name="c-uwp-sample"></a>C#Образец универсальной платформы Windows

Хотя XDK игр и приложений универсальной платформы Windows может использовать различные интерфейсы API, API универсальной платформы Windows моделируется XDK API очень внимательно. Для удаления данных будет по-прежнему необходимо выполните те же основные действия, делая Примечание о некоторых изменениях имя пространства имен и класса. Вместо того чтобы использовать пространство имен `Windows::Xbox::Storage` будет использоваться `Windows.Gaming.XboxLive.Storage`. Класс `ConnectedStorageSpace`, эквивалентно `GameSaveProvider`. Класс `ConnectedStorageContainer` эквивалентен `GameSaveContainer`. Эти изменения описаны дополнительные подключения хранилища из раздела [перенос Xbox Live код из XDK в UWP](../../using-xbox-live/porting-xbox-live-code-from-xdk-to-uwp.md).

```csharp
//Namespace Required
Windows.Gaming.XboxLive.Storage

//Get The User
var users = await Windows.System.User.FindAllAsync();

int intData = 23;
const string c_saveBlobName = "Jersey";
const string c_saveContainerDisplayName = "GameSave";
const string c_saveContainerName = "GameSaveContainer";
GameSaveProvider gameSaveProvider;

GameSaveProviderGetResult gameSaveTask = await GameSaveProvider.GetForUserAsync(users[0], context.AppConfig.ServiceConfigurationId);
//Parameters
//Windows.System.User user
//string SCID

if(gameSaveTask.Status == GameSaveErrorStatus.Ok)
{
    gameSaveProvider = gameSaveTask.Value;
}
else
{
    return;
    //throw new Exception("Game Save Provider Initialization failed");
}

//Now you have a GameSaveProvider (formerly ConnectedStorageSpace)
//Next you need to call CreateContainer to get a GameSaveContainer (formerly ConnectedStorageContainer)

GameSaveContainer gameSaveContainer = gameSaveProvider.CreateContainer(c_saveContainerName); // this will create a new named game save container with the name = to the input name
//Parameter
//string name (name of container to be created) 

//DELETE A BLOB
//form an array of strings containing the blob names you would like to delete.
string[] blobsToDelete = new string[] { c_saveBlobName };

//Call SubmitUpdatesAsync with the dictionary of the names of blobs to delete as the second parameter.
GameSaveOperationResult gameSaveOperationResult = await gameSaveContainer.SubmitUpdatesAsync(null, blobsToDelete, c_saveContainerDisplayName);
//Parameters
//IReadOnlyDictionary<String, IBuffer> blobsToWrite
//IEnumerable<string> blobsToDelete
//string displayName

//Check status of the operation to ensure successful delete.
if(gameSaveOperationResult.Status == GameSaveErrorStatus.Ok)
{
    //Successful Deletion Logic
}
else
{
    //handle failed deletion logic. 
}

//DELETE A SAVE CONTAINER
GameSaveOperationResult deleteContainerResult = await gameSaveProvider.DeleteContainerAsync(c_saveContainerName);
//Parameter
//string name (name of container to be deleted)

//Check status of the operation to ensure successful delete.
if(deleteContainerResult.Status == GameSaveErrorStatus.Ok)
{
    //Successful Deletion Logic
}
else
{
    //handle failed deletion logic. 
}
```

Подключение API службы хранилища для приложений универсальной платформы Windows описаны в [Справочник по Xbox Live API](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.xboxlive.storage).
Для просмотра другой пример, использующий подключение хранилища извлечение [Xbox Live API примеры игры сохранить проект](https://github.com/Microsoft/xbox-live-samples/tree/master/Samples/ID%40XboxSDK/GameSave).
