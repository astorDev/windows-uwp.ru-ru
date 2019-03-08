---
title: Вход в систему с SignInManager в Unity
description: Обзор диспетчера подключаемого модуля входа Unity
ms.date: 05/08/2018
ms.topic: get-started-article
keywords: Xbox live, xbox, игры, универсальной платформы Windows, windows 10, xbox, один, unity
ms.openlocfilehash: e6d066fe7792912f8918cb139d45ff05d105feaa
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57641729"
---
# <a name="scripting-sign-in"></a>Сценарии входа в систему

Чтобы добавить входа в систему в собственных пользовательских объектов для игр, вам необходимо будет внесена его в объект GameObject. Сообщите нам, скажем, PlayerAuthentication prefab не укладывается игры и вы бы хотели иметь свои собственные панель входа в систему, в этой статье поможет выполнить основные этапы добавления логики входа в заголовок вашей.

## <a name="sign-in-with-the-signinmanager"></a>Войдите с помощью SignInManager

Xbox Live Unity подключаемый модуль содержит скрипт для `SignInManager` в путь к файлу **активы >> XboxLive >> Сценарии >> SignInManager.cs**. Диспетчер является Singleton-класс, который можно использовать формы в любом месте в заголовок вашей, обратившись к заголовку *экземпляр* из `SignInManager`. Это *экземпляр* не должны быть инициализированы, а воспользуйтесь ИТ сразу же начинает игру. Доступны все общие свойства и функции, ссылаясь на *экземпляр* как `SignInManager.Instance`.

`SignInManager` Содержит весь код, необходимые для управление проверкой подлинности названия, это включает в себя входа в систему, выхода из системы, и получение сведений о пользователей, которые вошли в систему как какие проигрывателя.

### <a name="calls-and-results"></a>Вызовы и результаты

`SignInManager` Имеет три асинхронные функции совместного подпрограмма `SignInPlayer(int playerNumber)`, `SignOutPlayer(int playerNumber)`, и `SwitchUser(int playerNumber)`, функции этого триггера события собирать результаты вызова и действовать соответствующим образом. Можно добавить сценарий соответствующим функциям и их назначение `SignInManager.Instance`элемента списка обратных вызовов. Функции события являются `OnPlayerSignIn(int playerNumber, UnityAction<XboxLiveUser, XboxLiveAuthStatus, string> callback)`, `OnPlayerSignOut(int playerNumber, UnityAction<XboxLiveUser, XboxLiveAuthStatus, string> callback)`, `OnAnyPlayerSignIn(UnityAction<XboxLiveUser, XboxLiveAuthStatus, string> callback)`, и `OnAnyPlayerSignOut(UnityAction<XboxLiveUser, XboxLiveAuthStatus, string> callback)`. Каждый на функций событий ожидает передачи данных для событий, описанных в его имени. Можно добавить свои собственные функции для списка обратных вызовов от диспетчера в заголовок вашей `Start()` функции следующим кодом.

```csharp
using Microsoft.Xbox.Services;
using Microsoft.Xbox.Services.Client;

void Start () {
    try
    {
        SignInManager.Instance.OnPlayerSignOut(this.playerNumber, this.OnPlayerSignOut);
        SignInManager.Instance.OnPlayerSignIn(this.playerNumber, this.OnPlayerSignIn);
    }
    catch (Exception ex)
    {
        Debug.LogWarning(ex.Message);
    }

}
```

Этот фрагмент кода добавляет вход и выход из него прослушиватели для проигрыватель, связанный с этой GameObject playerNumber. Этот объект GameObject `OnPlayerSignIn` функция будет вызываться при `SignInManager` обнаруживает попытку входа завершен и его `OnPlayerSignOut` функция будет вызываться, когда обнаруживает выхода SignInManager. Функции событий в ваш объект GameObject должна иметь тип возвращаемого значения и параметры в соответствии с типом функции, вызванных SignInManager. Как `OnPlayerSignIn` и `OnPlayerSignOut` void функций, которые должны `XboxLiveUser`, `XboxLiveAuthStatus`, а строку как свои параметры. Оболочка функций может выглядеть следующим образом:

```csharp
using Microsoft.Xbox.Services;
using Microsoft.Xbox.Services.Client;

private void OnPlayerSignIn(XboxLiveUser xboxLiveUserParam, XboxLiveAuthStatus authStatus, string errorMessage)
{
}

private void OnPlayerSignOut(XboxLiveUser xboxLiveUserParam, XboxLiveAuthStatus authStatus, string errorMessage)
{
}
```

В обе функции проверьте `XboxLiveAuthStatus` чтобы убедиться в том, что при вызове `SignInManager.Instance` прошла успешно. Для успешного вызова `XboxLiveUser` будет `XboxLiveUser`, который выполнил вход в нашей развертывание путем `SignInManager`. Если вызов завершается неудачно `errorMessage` строка будет содержать сведения о причине сбоя.

Добавление нескольких строк кода для проверки успешного вызова приведет код, аналогичный следующему:

```csharp
using Microsoft.Xbox.Services;
using Microsoft.Xbox.Services.Client;

private void OnPlayerSignIn(XboxLiveUser xboxLiveUserParam, XboxLiveAuthStatus authStatus, string errorMessage)
{
    if(authStatus == XboxLiveAuthStatus.Succeeded)
    {
        this.xboxLiveUser = xboxLiveUserParam; //store the xboxLiveUser SignedIn
        this.signedIn = true;
    }
    else
    {
        if (XboxLiveServicesSettings.Instance.DebugLogsOn)
        {
            Debug.LogError(errorMessage); //Log the error message in case of unsuccessful call. 
        }
    }
}

private void OnPlayerSignOut(XboxLiveUser xboxLiveUserParam, XboxLiveAuthStatus authStatus, string errorMessage)
{
    if (authStatus == XboxLiveAuthStatus.Succeeded)
    {
        this.xboxLiveUser = null;
        this.signedIn = false;
    }
    else
    {
        if (XboxLiveServicesSettings.Instance.DebugLogsOn)
        {
            Debug.LogError(errorMessage);
        }
    }
}
```

Путем вызова входа в систему и захват результирующее событие для результата, может обрабатывать вход и выход из него названия.

## <a name="get-signed-in-player-information"></a>Выполнить вход сведения проигрывателя

Кроме подписывания игроков в службу SignInManager следит за всех пользователей в системе. На Компьютере это будет обязательно должен быть единственным, подписанных в проигрывателе, и на Xbox она ограничена 16. Вы можете проверить, как предельному вы, используя для сравнения результат `SignInManager.Instance.GetCurrentNumberOfPlayers()` к результату `SignInManager.Instance.GetMaximumNumberOfPlayers()`. Словарь игроков вошедшего в систему, индексированных по, проигрыватель имеет SignInManager *playerNumber*. Это можно использовать для получения некоторые основные сведения о доступных проигрыватель из связанные с ними `XboxLiveUser`.

```csharp
if (SignInManager.Instance.GetPlayer(this.playerNumber).IsSignedIn) // If there is a player signed in for this gameObjects player number
            {
                this.displayedGamertag = SignInManager.Instance.GetPlayer(this.playerNumber).Gamertag; // Set that users gamertag to the gamertag displayed
            }
```

Этот небольшой фрагмент кода проверяет, имеется ли исполнителем выполнил вход в слот player чисел для этого объект GameObject, а затем сохраняет: тег игрока, пользователям будет отображаться, если они подписаны. Хотя `XboxLiveUser` содержит со знаком в игрока пользователей, а также идентификатор пользователя Xbox (xuid), необходимо будет вызывать другие службы, такие как `SocialManager` для доступа к данным, таких как gamerpic и игровые достижения.

## <a name="destroying-your-sign-in-gameobject"></a>Уничтожение GameObject вашего входа в систему

При удалении игровому объекту, который использует один из диспетчеров подключаемый модуль Xbox Live, такие как `SignInManager` или `SocialManager`, обычно при загрузке новой сцены, важно удалить любые функции, добавляемый в список прослушивателей событий для диспетчера. В примере кода для данной статьи нам понадобится для удаления функций, которые мы добавили в прослушиватели событий для входа в систему и выхода. Мы бы удалили этих функций с `SignInManager` в `OnDestroy()` функции наших GameObject.

```csharp
private void OnDestroy()
{
    if (SignInManager.Instance != null)
    {
        SignInManager.Instance.RemoveCallbackFromPlayer(this.PlayerNumber, this.OnPlayerSignOut);
        SignInManager.Instance.RemoveCallbackFromPlayer(this.PlayerNumber, this.OnPlayerSignIn);
    }
```

Этот код приведет к удалению функций ответного вызова входа в систему и выхода для проигрыватель, связанный с этой GameObject.

## <a name="testing-you-code-in-visual-studio"></a>Тестирование кода вы в Visual Studio

В дополнение к [шаги, необходимые для создания игры в Visual Studio](configure-xbox-live-in-unity.md#build-and-test-the-project), упомянутую в [настроить ваш Xbox Live Title для Unity](configure-xbox-live-in-unity.md) статьи, имеется дополнительный шаг, необходимой для тестирования должным образом в игры Visual Studio. Необходимо будет обновить свойства файла (Package.appxmanifest.XML). Выполните указанные ниже действия.

1. Поиск в обозревателе решений для файла (Package.appxmanifest.XML)
2. Щелкните файл правой кнопкой мыши и выберите Просмотр кода
3. В разделе `<Properties><\/Properties>` разделе, добавьте следующую строку: "< uap:SupportedUsers > несколько <\/uap:SupportedUsers >.
4. Разверните игры игровой приставки Xbox путем запуска удаленной отладки сборки из Visual Studio. Вы найдете инструкции по настройке названием на Xbox в [Настройка вашей универсальной платформы Windows в среде разработки Xbox](../../xbox-apps/development-environment-setup.md) статьи.

> [!NOTE]
> Часть Конфигурация изменена может выглядеть, как он Включение многопользовательских, но по-прежнему необходимо запустить игру в сценариях одного участника.

## <a name="policies-and-limitations"></a>Политики и ограничения

Существует несколько политики входа и ограничений заголовка который требуется учитывать при разработке входа в систему.

- После заголовок вашей начальной входа в систему необходимо хранить по крайней мере один проигрыватель вошедшего в систему. `SignInManager` Будет выдавать ошибку и сбой вызова при попытке выйти из системы последнего вошедшего пользователя. Также важно отметить, что нельзя вызвать `SignInManager.Instance.SwitchUser(int playerNumber)`на последней автоматический в проигрывателе, так как он будет пытаться выйти проигрыватель перед входом нового игрока.

- ПК может только войти один пользователь, в консоли может за один раз войти до 16 игроков.

- Заголовок фактически разрешения выполнить выход проигрывателя из операционной системы, из-за этого SignOut могут не работать должным образом. SignInManager может войти пользователь, и где это касается заголовка, но не может выйти любой пользователь из компьютера, на котором развертывается заголовок.

- Несколько вход пользователя доступен только в одной консоли Xbox.

- Гостевые учетные записи не доступные заведение Xbox Live Creators Program.