---
title: Загрузка данных с помощью подключения хранилища
description: Сведения об использовании хранилища, подключенных для загрузки данных.
ms.assetid: c660a456-fe7d-453a-ae7b-9ecaa2ba0a15
ms.date: 02/27/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, подключенные устройства хранения
ms.localizationpriority: medium
ms.openlocfilehash: a2f7498e8063e290dc506de72b34d2c77d29b26e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57637139"
---
# <a name="use-connected-storage-to-load-data"></a>Загрузка данных с помощью подключения хранилища

Данные считываются асинхронно с помощью `ReadAsync` или `GetAsync` подключенных метод хранения.

### <a name="to-load-data-from-connected-storage"></a>Для загрузки данных из хранилища, подключения

1.  Получить `ConnectedStorageSpace` для пользователя путем вызова `GetForUserAsync`.

    В примере XDK, возвращенный `ConnectedStorageSpace` добавляется на карту, чтобы включить простое управление `ConnectedStorageSpace` объектов для нескольких пользователей.

2.  Создание `ConnectedStorageContainer` путем вызова `CreateContainer` на `ConnectedStorageSpace`.
3.  Получить данные путем вызова `ReadAsync` или `GetAsync` на `ConnectedStorageContainer`. `ReadAsync` требует передачи в буфере при `GetAsync` выделяет новый буферов для хранения данных, который доступен для чтения.

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

void OnLoadCompleted(IAsyncAction^ action, Windows::Foundation::AsyncStatus status);

// Load data from a fixed container/blob name into an IBuffer
void Load(Windows::Storage::Streams::IBuffer^ buffer)
{

    auto reads = ref new Platform::Collections::Map<Platform::String^, Windows::Storage::Streams::IBuffer^>();
    reads->Insert("data", buffer);

    auto storageSpace = gConnectedStorageSpaceForUsers->Lookup(gCurrentUser->Id);
    auto container = storageSpace->CreateContainer("Saves/Checkpoint");

    //Save Data is Loading

    auto op = container->ReadAsync(reads->GetView());

    op->Completed = ref new AsyncActionCompletedHandler(OnLoadCompleted);
}

void OnLoadCompleted(IAsyncAction^ action, Windows::Foundation::AsyncStatus status)
{
    switch (action->Status)
    {
    case Windows::Foundation::AsyncStatus::Completed:
        //Successful load logic here.
        break;

    case Windows::Foundation::AsyncStatus::Error:
    case Windows::Foundation::AsyncStatus::Canceled:
        //Fail logic here
        break;

    default:
        //all other possible values of action->status are also failures, alternate fail logic here. 
        break;
    }
}
```

Можно найти XDK подключенных API-интерфейсов хранилища см. в CHM-файле XDK по пути: **Xbox один XDK >> Справочник по API >> Справочник по API платформы >> Справочник по API системы >> Windows.Xbox.Storage**.
API-интерфейсы XDK также описаны на [developer.microsoft.com сайта](https://developer.microsoft.com/en-us/games/xbox/docs/xdk/storage-xbox-microsoft-n).
Ссылку на API-интерфейсы XDK требует наличия Microsoft Account(MSA), разрешенное для доступа к Kit(XDK) разработчиков Xbox.
Windows.Xbox.Storage — имя пространства имен подключения хранилища для консолей Xbox One.

## <a name="c-uwp-sample"></a>C#Образец универсальной платформы Windows

Хотя XDK игр и приложений универсальной платформы Windows может использовать различные интерфейсы API, API универсальной платформы Windows моделируется XDK API очень внимательно. Для загрузки данных по-прежнему необходимо будет выполнить те же основные действия во время создания Примечание о некоторых изменениях имя пространства имен и класса. Вместо того чтобы использовать пространство имен `Windows::Xbox::Storage` будет использоваться `Windows.Gaming.XboxLive.Storage`. Класс `ConnectedStorageSpace`, эквивалентно `GameSaveProvider`. Класс `ConnectedStorageContainer` эквивалентен `GameSaveContainer`. Эти изменения описаны дополнительные подключения хранилища из раздела [перенос Xbox Live код из XDK в UWP](../../using-xbox-live/porting-xbox-live-code-from-xdk-to-uwp.md).

```csharp
//Namespace Required
Windows.Gaming.XboxLive.Storage

//Get The User
var users = await Windows.System.User.FindAllAsync();

int intData = 0;
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
    //throw new Exception("Game Save Provider Initialization failed");;
}

//Now you have a GameSaveProvider
//Next you need to call CreateContainer to get a GameSaveContainer

GameSaveContainer gameSaveContainer = gameSaveProvider.CreateContainer(c_saveContainerName);
//Parameter
//string name (name of the GameSaveContainer Created)

//form an array of strings containing the blob names you would like to read.
string[] blobsToRead = new string[] { c_saveBlobName };

// GetAsync allocates a new Dictionary to hold the retrieved data. You can also use ReadAsync
// to provide your own preallocated Dictionary.
GameSaveBlobGetResult result = await gameSaveContainer.GetAsync(blobsToRead);

int loadedData = 0;

//Check status to make sure data was read from the container
if(result.Status == GameSaveErrorStatus.Ok)
{
    //prepare a buffer to receive blob
    IBuffer loadedBuffer;

    //retrieve the named blob from the GetAsync result, place it in loaded buffer.
    result.Value.TryGetValue(c_saveBlobName, out loadedBuffer);

    if(loadedBuffer == null)
    {

        throw new Exception(String.Format("Didn't find expected blob \"{0}\" in the loaded data.", c_saveBlobName));

    }
    DataReader reader = DataReader.FromBuffer(loadedBuffer);
    loadedData = reader.ReadInt32();
}
```

Подключение API службы хранилища для приложений универсальной платформы Windows описаны в [Справочник по Xbox Live API](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.xboxlive.storage).
Для просмотра другой пример, использующий подключение хранилища извлечение [Xbox Live API примеры игры сохранить проект](https://github.com/Microsoft/xbox-live-samples/tree/master/Samples/ID%40XboxSDK/GameSave).