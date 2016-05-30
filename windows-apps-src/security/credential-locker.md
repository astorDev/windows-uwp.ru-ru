---
title: Хранилище учетных данных
description: В этой статье описано, как приложения универсальной платформы Windows (UWP) могут использовать хранилище учетных данных для безопасного хранения и получения учетных данных пользователя и их перемещения между устройствами с помощью учетной записи Майкрософт пользователя.
ms.assetid: 7BCC443D-9E8A-417C-B275-3105F5DED863
author: awkoren
---

# Хранилище учетных данных


\[ Обновлено для приложений UWP в Windows 10. Статьи для Windows 8.x можно найти в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этой статье описано, как приложения универсальной платформы Windows (UWP) могут использовать хранилище учетных данных для безопасного хранения и получения учетных данных пользователя и их перемещения между устройствами с помощью учетной записи Майкрософт пользователя.

Предположим, ваше приложение подключается к некоторой службе для получения доступа к защищенным ресурсам (например, файлам мультимедиа, социальным сетям и т. д.). Эта служба запрашивает учетные данные пользователя. Вы добавили в приложение элементы управления для ввода имени и пароля пользователя, которые затем используются для входа в службу. С помощью API хранилища учетных данных вы сможете сохранить имя и пароль пользователя, а затем, при следующем запуске приложения, извлечь их, чтобы автоматически выполнить вход для пользователя независимо от устройства.

Работа хранилища учетных данных немного отличается для учетных записей домена. Если учетную запись Майкрософт, с которой связаны учетные данные пользователя, связать с учетной записью домена (которая, например, используется на работе), то учетные данные пользователя будут перемещены в эту учетную запись домена. Однако новые учетные данные, добавленные при входе с учетной записью домена, не будут перемещаться. Это делается для того, чтобы частные учетные данные в домене не были доступны из-за пределов домена.

## Сохранение учетных данных пользователя


1.  Получите ссылку на хранилище учетных данных с помощью объекта [**PasswordVault**](https://msdn.microsoft.com/library/windows/apps/br227081) из пространства имен [**Windows.Security.Credentials**](https://msdn.microsoft.com/library/windows/apps/br227089).
2.  Создайте объект [**PasswordCredential**](https://msdn.microsoft.com/library/windows/apps/br227061), содержащий идентификатор приложения, имя и пароль пользователя, и передайте этот объект в качестве входного параметра в метод [**PasswordVault.Add**](https://msdn.microsoft.com/library/windows/apps/hh701231), после чего учетные данные будут добавлены в хранилище.

```cs
var vault = new Windows.Security.Credentials.PasswordVault();
vault.Add(new Windows.Security.Credentials.PasswordCredential(
    "My App", username, password));
```

## Получение учетных данных пользователя


После получения ссылки на объект [**PasswordVault**](https://msdn.microsoft.com/library/windows/apps/br227081) будет доступно несколько вариантов получения учетных данных пользователя из хранилища учетных данных.

-   С помощью метода [**PasswordVault.RetrieveAll**](https://msdn.microsoft.com/library/windows/apps/br227088) можно получить из хранилища все учетные данные, введенные пользователем в приложении.

-   Если известно имя пользователя сохраненных учетных данных, то с помощью метода [**PasswordVault.FindAllByUserName**](https://msdn.microsoft.com/library/windows/apps/br227084) можно получить все учетные данные для этого имени пользователя.

-   Если известно имя ресурса из сохраненных учетных данных, то с помощью метода [**PasswordVault.FindAllByResource**](https://msdn.microsoft.com/library/windows/apps/br227083) можно получить все учетные данные для этого имени ресурса.

-   Наконец, если известны имя пользователя и имя ресурса для учетных данных, то эти учетные данные можно получить с помощью метода [**PasswordVault.Retrieve**](https://msdn.microsoft.com/library/windows/apps/br227087).

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

## Удаление учетных данных пользователя


Удаление учетных данных пользователя из хранилища учетных данных также требует всего двух действий.

1.  Получите ссылку на хранилище учетных данных с помощью объекта [**PasswordVault**](https://msdn.microsoft.com/library/windows/apps/br227081) из пространства имен [**Windows.Security.Credentials**](https://msdn.microsoft.com/library/windows/apps/br227089).

2.  Передайте учетные данные, которые следует удалить, в метод [**PasswordVault.Remove**](https://msdn.microsoft.com/library/windows/apps/hh701242).

```cs
var vault = new Windows.Security.Credentials.PasswordVault();
vault.Remove(new Windows.Security.Credentials.PasswordCredential(
    "My App", username, password));
```

## Рекомендации


Хранилище учетных данных предназначено только для паролей. Его не следует использовать для больших двоичных объектов.

Сохраняйте пароли в хранилище учетных данных только в том случае, если:

-   пользователь успешно выполнил вход;
-   пользователь дал согласие на сохранение паролей.

Не храните учетные данные в виде обычного текста с помощью данных приложения или параметров перемещения.

<!--HONumber=May16_HO2-->


