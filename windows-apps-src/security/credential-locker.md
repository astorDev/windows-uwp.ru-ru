---
title: Хранилище учетных данных
description: В этой статье описано, как приложения универсальной платформы Windows (UWP) могут использовать хранилище учетных данных для безопасного хранения и получения учетных данных пользователя и их перемещения между устройствами с помощью учетной записи Майкрософт пользователя.
ms.assetid: 7BCC443D-9E8A-417C-B275-3105F5DED863
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, security
ms.localizationpriority: medium
ms.openlocfilehash: 83f58f34ce7251415652496e74d83e24156015aa
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372617"
---
# <a name="credential-locker"></a>Хранилище учетных данных




В этой статье описано, как приложения универсальной платформы Windows (UWP) могут использовать хранилище учетных данных для безопасного хранения и получения учетных данных пользователя и их перемещения между устройствами с помощью учетной записи Майкрософт пользователя.

Предположим, ваше приложение подключается к некоторой службе для получения доступа к защищенным ресурсам (например, файлам мультимедиа, социальным сетям и т. д.). Эта служба запрашивает учетные данные пользователя. Вы добавили в приложение элементы управления для ввода имени и пароля пользователя, которые затем используются для входа в службу. С помощью API хранилища учетных данных вы сможете сохранить имя и пароль пользователя, а затем, при следующем запуске приложения, извлечь их, чтобы автоматически выполнить вход для пользователя независимо от устройства.

Срок действия пользовательских учетных данных, сохраненных в CredentialLocker, *не* истекает, на них *не* влияет [**ApplicationData.RoamingStorageQuota**](https://docs.microsoft.com/uwp/api/windows.storage.applicationdata.roamingstoragequota), и они *не* будут удалены из-за неактивности, как традиционные перемещаемые данные. Однако в CredentialLocker можно сохранить не более 20 учетных данных на приложение.

Работа хранилища учетных данных немного отличается для учетных записей домена. Если учетную запись Майкрософт, с которой связаны учетные данные пользователя, связать с учетной записью домена (которая, например, используется на работе), то учетные данные пользователя будут перемещены в эту учетную запись домена. Однако новые учетные данные, добавленные при входе с учетной записью домена, не будут перемещаться. Это делается для того, чтобы частные учетные данные в домене не были доступны из-за пределов домена.

## <a name="storing-user-credentials"></a>Сохранение учетных данных пользователя


1.  Получите ссылку на хранилище учетных данных с помощью объекта [**PasswordVault**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials.PasswordVault) из пространства имен [**Windows.Security.Credentials**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials).
2.  Создайте объект [**PasswordCredential**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials.PasswordCredential), содержащий идентификатор приложения, имя и пароль пользователя, и передайте этот объект в качестве входного параметра в метод [**PasswordVault.Add**](https://docs.microsoft.com/uwp/api/windows.security.credentials.passwordvault.add), после чего учетные данные будут добавлены в хранилище.

```cs
var vault = new Windows.Security.Credentials.PasswordVault();
vault.Add(new Windows.Security.Credentials.PasswordCredential(
    "My App", username, password));
```

## <a name="retrieving-user-credentials"></a>Получение учетных данных пользователя


После получения ссылки на объект [**PasswordVault**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials.PasswordVault) будет доступно несколько вариантов получения учетных данных пользователя из хранилища учетных данных.

-   С помощью метода [**PasswordVault.RetrieveAll**](https://docs.microsoft.com/uwp/api/windows.security.credentials.passwordvault.retrieveall) можно получить из хранилища все учетные данные, введенные пользователем в приложении.

-   Если известно имя пользователя сохраненных учетных данных, то с помощью метода [**PasswordVault.FindAllByUserName**](https://docs.microsoft.com/uwp/api/windows.security.credentials.passwordvault.findallbyusername) можно получить все учетные данные для этого имени пользователя.

-   Если известно имя ресурса из сохраненных учетных данных, то с помощью метода [**PasswordVault.FindAllByResource**](https://docs.microsoft.com/uwp/api/windows.security.credentials.passwordvault.findallbyresource) можно получить все учетные данные для этого имени ресурса.

-   Наконец, если известны имя пользователя и имя ресурса для учетных данных, то эти учетные данные можно получить с помощью метода [**PasswordVault.Retrieve**](https://docs.microsoft.com/uwp/api/windows.security.credentials.passwordvault.retrieve).

Рассмотрим следующий пример. Мы глобально сохранили имя ресурса в приложении и автоматически выполняем вход для пользователя, если находим его учетные данные. Если для одного пользователя находится несколько учетных данных, мы предлагаем ему выбрать учетные данные, которые по умолчанию будут использоваться для входа.

```cs
private string resourceName = "My App";
private string defaultUserName;

private void Login()
{
    var loginCredential = GetCredentialFromLocker();

    if (loginCredential != null)
    {
        // There is a credential stored in the locker.
        // Populate the Password property of the credential
        // for automatic login.
        loginCredential.RetrievePassword();
    }
    else
    {
        // There is no credential stored in the locker.
        // Display UI to get user credentials.
        loginCredential = GetLoginCredentialUI();
    }

    // Log the user in.
    ServerLogin(loginCredential.UserName, loginCredential.Password);
}


private Windows.Security.Credentials.PasswordCredential GetCredentialFromLocker()
{
    Windows.Security.Credentials.PasswordCredential credential = null;

    var vault = new Windows.Security.Credentials.PasswordVault();
    var credentialList = vault.FindAllByResource(resourceName);
    if (credentialList.Count > 0)
    {
        if (credentialList.Count == 1)
        {
            credential = credentialList[0];
        }
        else
        {
            // When there are multiple usernames,
            // retrieve the default username. If one doesn't
            // exist, then display UI to have the user select
            // a default username.

            defaultUserName = GetDefaultUserNameUI();

            credential = vault.Retrieve(resourceName, defaultUserName);
        }
    }

    return credential;
}
```

## <a name="deleting-user-credentials"></a>Удаление учетных данных пользователя


Удаление учетных данных пользователя из хранилища учетных данных также требует всего двух действий.

1.  Получите ссылку на хранилище учетных данных с помощью объекта [**PasswordVault**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials.PasswordVault) из пространства имен [**Windows.Security.Credentials**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials).

2.  Передайте учетные данные, которые следует удалить, в метод [**PasswordVault.Remove**](https://docs.microsoft.com/uwp/api/windows.security.credentials.passwordvault.remove).

```cs
var vault = new Windows.Security.Credentials.PasswordVault();
vault.Remove(new Windows.Security.Credentials.PasswordCredential(
    "My App", username, password));
```

## <a name="best-practices"></a>Рекомендации


Хранилище учетных данных предназначено только для паролей. Его не следует использовать для больших двоичных объектов.

Сохраняйте пароли в хранилище учетных данных только в том случае, если:

-   пользователь успешно выполнил вход;
-   пользователь дал согласие на сохранение паролей.

Не храните учетные данные в виде обычного текста с помощью данных приложения или параметров перемещения.
