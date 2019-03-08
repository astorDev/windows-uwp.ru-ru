---
title: Используйте подключенные хранилища для сохранения данных
description: Сведения об использовании подключения хранилища для сохранения данных.
ms.assetid: ccf7488c-5d55-480e-b3aa-412220d03104
ms.date: 02/27/2018
ms.topic: article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10 для настольных ПК, xbox, подключенные устройства хранения
ms.localizationpriority: medium
ms.openlocfilehash: 4140e3bbe0f0ab229e3637008e01892f4179292e
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57617619"
---
# <a name="use-connected-storage-to-save-data"></a>Используйте подключенные хранилища для сохранения данных


Данные сохраняются асинхронно путем создания `ConnectedStorageContainer` в `ConnectedStorageSpace` для пользователя и вызова `SubmitUpdatesAsync` метод в контейнере.

> [!IMPORTANT]
> Зависимости данных по контейнерам подключенные устройства хранения являются небезопасными. Например передача из одного контейнера в облако может полностью, другой может оставаться в очереди передачи. Если пользователь перемещена на другой консоли, операция синхронизации позволит первый контейнер для синхронизации и доступе к второй консоли без первого контейнера, они отсутствуют.

## <a name="to-save-data-to-connected-storage"></a>Для сохранения данных для подключения хранилища

1.  Получить `ConnectedStorageSpace` для пользователя путем вызова `GetForUserAsync`.

    В примере XDK, возвращенный `ConnectedStorageSpace` объект добавляется на карту, чтобы включить простое управление `ConnectedStorageSpace` объектов для нескольких пользователей.

2.  Создание `ConnectedStorageContainer` путем вызова метода `CreateContainer` на `ConnectedStorageSpace` объекта.
3.  Вызовите `SubmitUpdatesAsync` на `ConnectedStorageContainer` игр сохранить большой двоичный объект данных, как с помощью `blobsToWrite` параметра.

## <a name="c-xdk-sample"></a>Пример C++ XDK

```cpp
auto gConnectedStorageSpaceForUsers = ref new Platform::Collections::Map<unsigned int, Windows::Xbox::Storage::ConnectedStorageSpace^>();

bool GetUserInputYesOrNo() {return true;};

User^ gCurrentUser;
IBuffer^ WrapRawBuffer( void* ptr, size_t size );

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

enum Color { RED, BLUE };
enum EngineSize { BIG, SMALL };

struct CarData
{
    Color color;
    bool hasWheels;
    bool hasFancyRims;
    EngineSize engineSize;
};


const int MAX_CARS = 10;

struct SaveData
{
    CarData cars[MAX_CARS];
    int numCars;
    int currentCar;
    int cash;
};

SaveData gMySaveData;

void SaveCheckpoint(Windows::Storage::Streams::IBuffer^ buffer, User^ user);

bool ItIsTimeToSaveACheckpoint() {return true;};

void Update()
{
    if (ItIsTimeToSaveACheckpoint())
        SaveCheckpoint(WrapRawBuffer(&gMySaveData,sizeof(SaveData)),gCurrentUser);
}


void SaveCheckpoint(Windows::Storage::Streams::IBuffer^ buffer, User^ user)
{
     auto storageSpace = gConnectedStorageSpaceForUsers->Lookup( user->Id );

     auto container = storageSpace->CreateContainer("Saves/Checkpoint");

     auto updates = ref new Platform::Collections::Map<Platform::String^, Windows::Storage::Streams::IBuffer^>();
     updates->Insert("data", buffer);

     auto op = container->SubmitUpdatesAsync(updates->GetView(), nullptr);

     //Save is happening here asynchronously

     op->Completed = ref new AsyncActionCompletedHandler(
               [=](IAsyncAction^ a, Windows::Foundation::AsyncStatus status){
                   //Save function has completed
                   //This area can be filled with further post save logic.
     });
}
```

Можно найти XDK подключенных API-интерфейсов хранилища см. в CHM-файле XDK по пути: **Xbox один XDK >> Справочник по API >> Справочник по API платформы >> Справочник по API системы >> Windows.Xbox.Storage**.
API-интерфейсы XDK также описаны на [developer.microsoft.com сайта](https://developer.microsoft.com/en-us/games/xbox/docs/xdk/storage-xbox-microsoft-n).
Ссылку на API-интерфейсы XDK требует наличия Microsoft Account(MSA), разрешенное для доступа к Kit(XDK) разработчиков Xbox.
Windows.Xbox.Storage — имя пространства имен подключения хранилища для консолей Xbox One.

## <a name="c-uwp-sample"></a>C#Образец универсальной платформы Windows

Хотя XDK игр и приложений универсальной платформы Windows может использовать различные интерфейсы API, API универсальной платформы Windows моделируется XDK API очень внимательно. Для сохранения данных будет по-прежнему необходимо выполните те же основные действия, делая Примечание о некоторых изменениях имя пространства имен и класса. Вместо того чтобы использовать пространство имен `Windows::Xbox::Storage` будет использоваться `Windows.Gaming.XboxLive.Storage`. Класс `ConnectedStorageSpace`, эквивалентно `GameSaveProvider`. Класс `ConnectedStorageContainer` эквивалентен `GameSaveContainer`. Эти изменения описаны дополнительные подключения хранилища из раздела [перенос Xbox Live код из XDK в UWP](../../using-xbox-live/porting-xbox-live-code-from-xdk-to-uwp.md).

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
//string name

// To store a value in the container, it needs to be written into a buffer, then stored with
// a blob name in a Dictionary.

DataWriter writer = new DataWriter();

writer.WriteInt32(intData); //some number you want to save, in this case 23.

IBuffer dataBuffer = writer.DetachBuffer();

var blobsToWrite = new Dictionary<string, IBuffer>();

blobsToWrite.Add(c_saveBlobName, dataBuffer);

GameSaveOperationResult gameSaveOperationResult = await gameSaveContainer.SubmitUpdatesAsync(blobsToWrite, null, c_saveContainerDisplayName);
//IReadOnlyDictionary<String, IBuffer> blobsToWrite
//IEnumerable<string> blobsToDelete
//string displayName
```

Подключение API службы хранилища для приложений универсальной платформы Windows описаны в [Справочник по Xbox Live API](https://docs.microsoft.com/en-us/uwp/api/windows.gaming.xboxlive.storage).
Для просмотра другой пример, использующий подключение хранилища извлечение [Xbox Live API примеры игры сохранить проект](https://github.com/Microsoft/xbox-live-samples/tree/master/Samples/ID%40XboxSDK/GameSave).