---
title: "Подключение к поставщикам удостоверений с помощью диспетчера учетных записей в Интернете"
description: "В этой статье описан метод использования AccountsSettingsPane для подключения приложения универсальной платформы Windows (UWP) к внешним поставщикам удостоверений, например Майкрософт или Facebook, с помощью новых API диспетчера учетных записей в Интернете для Windows10."
author: awkoren
translationtype: Human Translation
ms.sourcegitcommit: e16977a9a11b292ea9624ff421aa964c11d615be
ms.openlocfilehash: d234811b395790a35ad50dea9ef4cc56d60458e8

---
# Подключение к поставщикам удостоверений с помощью диспетчера учетных записей в Интернете

В этой статье описан метод отображения AccountsSettingsPane и подключения приложения универсальной платформы Windows (UWP) к внешним поставщикам удостоверений, например Майкрософт или Facebook, с помощью новых API диспетчера учетных записей в Интернете для Windows10. Вы узнаете, как запросить разрешение пользователя на использование учетной записи Майкрософт, получить маркер доступа и использовать его для выполнения базовых операций (например, для получения данных профиля или отправки файлов в OneDrive). Порядок действий при получении разрешения пользователя и доступа с помощью любого поставщика удостоверений, поддерживающего диспетчер учетных записей в Интернете, практически одинаков.

> Примечание. Полный пример кода см. в [образце кода WebAccountManagement на GitHub](http://go.microsoft.com/fwlink/p/?LinkId=620621).

## Подготовка

Сначала создайте новое пустое приложение в Visual Studio. 

Затем необходимо связать приложение с Магазином для подключения к поставщикам удостоверений. Для этого щелкните проект правой кнопкой, выберите **Магазин** > **Связать приложение с Магазином** и следуйте инструкциям мастера. 

Затем создайте простейший пользовательский интерфейс, состоящий из кнопки XAML и двух текстовых полей.

```XML
<StackPanel HorizontalAlignment="Center" VerticalAlignment="Center">
    <Button x:Name="LoginButton" Content="Log in" Click="LoginButton_Click" />
    <TextBlock x:Name="UserIdTextBlock"/>
    <TextBlock x:Name="UserNameTextBlock"/>
</StackPanel>
```

Добавьте прикрепленный к кнопке обработчик событий в выделенном коде:

```C#
private void LoginButton_Click(object sender, RoutedEventArgs e)
{   
}
```

И наконец, чтобы в дальнейшем не нужно было беспокоиться о проблемах со ссылками, добавьте следующие пространства имен: 

```C#
using System;
using Windows.Security.Authentication.Web.Core;
using Windows.System;
using Windows.UI.ApplicationSettings;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.Data.Json;
using Windows.UI.Xaml.Navigation;
using Windows.Web.Http;
```

## Отображение AccountSettingsPane

Система предоставляет встроенный пользовательский интерфейс для управления поставщиками удостоверений и учетными записями в Интернете под названием AccountSettingsPane. Отобразить интерфейс можно следующим образом:

```C#
private void LoginButton_Click(object sender, RoutedEventArgs e)
{
    AccountsSettingsPane.Show(); 
}
```

При запуске приложения и нажатии кнопки входа должно отобразиться пустое окно. 

![Панель параметров учетных записей](images/tb-1.png)

Панель пуста, поскольку система предоставляет лишь оболочку пользовательского интерфейса, — разработчику необходимо программно добавить в панель поставщиков удостоверений. 

## Регистрация AccountCommandsRequested

Для добавления команд на панель необходимо сначала зарегистрировать обработчик событий AccountCommandsRequested. Это сообщает системе о необходимости выполнить логику сборки, когда пользователь запрашивает отображение панели (например, щелкает кнопку XAML). 

Переопределите события OnNavigatedTo и OnNavigatedFrom в коде программной части и добавьте в них следующий код. 

```C#
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    AccountsSettingsPane.GetForCurrentView().AccountCommandsRequested += BuildPaneAsync; 
}
```

```C#
protected override void OnNavigatedFrom(NavigationEventArgs e)
{
    AccountsSettingsPane.GetForCurrentView().AccountCommandsRequested -= BuildPaneAsync; 
}
```

Пользователи не очень часто взаимодействуют с учетными записями, поэтому подобная регистрация и отмена регистрации обработчика событий помогает предотвратить утечку памяти. Таким образом, настроенная вами панель находится в памяти, только когда велик шанс того, что пользователь запросит ее отображение (например, если он находится на странице параметров или входа). 

## Создание панели параметров учетных записей

Метод BuildPaneAsync вызывается каждый раз при отображении AccountSettingsPane. Именно в него добавляется код для настройки команд для отображения на панели. 

Начните с получения отсроченного объекта. Это сообщает системе о том, что AccountsSettingsPane следует отображать только после ее создания.

```C#
private async void BuildPaneAsync(AccountsSettingsPane s,
    AccountsSettingsPaneCommandsRequestedEventArgs e)
{
    var deferral = e.GetDeferral();
        
    deferral.Complete(); 
}
```

Затем получите поставщика с помощью метода WebAuthenticationCoreManager.FindAccountProviderAsync. У каждого поставщика свой URL-адрес, который можно найти в документации поставщика. Для учетных записей Майкрософт и Azure Active Directory используется адрес "https://login.microsoft.com". 

```C#
private async void BuildPaneAsync(AccountsSettingsPane s,
    AccountsSettingsPaneCommandsRequestedEventArgs e)
{
    var deferral = e.GetDeferral();
        
    var msaProvider = await WebAuthenticationCoreManager.FindAccountProviderAsync(
        "https://login.microsoft.com", "consumers"); 
        
    deferral.Complete(); 
}
```

Обратите внимание, что мы также передаем строку "consumers" необязательному параметру *authority*. Это связано с тем, что Майкрософт предоставляет два различных типа проверки подлинности: учетные записи Майкрософт (MSA) для "потребителей" и Azure Active Directory (AAD) для "организаций". Значение "consumers" означает, что мы хотим использовать учетную запись Майкрософт. Если вы разрабатываете корпоративное приложение, используйте строку "organizations".

В заключение добавьте поставщика на панель AccountsSettingsPane, создав новый экземпляр класса WebAccountProviderCommand следующим образом: 

```C#
private async void BuildPaneAsync(AccountsSettingsPane s,
    AccountsSettingsPaneCommandsRequestedEventArgs e)
{
    var deferral = e.GetDeferral();

    var msaProvider = await WebAuthenticationCoreManager.FindAccountProviderAsync(
        "https://login.microsoft.com", "consumers");

    var command = new WebAccountProviderCommand(msaProvider, GetMsaTokenAsync);  

    e.WebAccountProviderCommands.Add(command);

    deferral.Complete(); 
}
```

Обратите внимание, что метод GetMsaToken, который мы передавали в новый WebAccountProviderCommand, еще не существует (мы создадим его на следующем шаге). На данном этапе вы можете добавить его в виде пустого метода.

Запустите приведенный выше код. Панель должна выглядеть примерно так: 

![Панель параметров учетных записей](images/tb-2.png)

### Запрос маркера

После добавления учетной записи Майкрософт на AccountsSettingsPane необходимо обработать события, возникающие, когда пользователь выбирает учетную запись. Мы зарегистрировали метод GetMsaToken, который вызывается, когда пользователь выбирает вход с помощью учетной записи Майкрософт, поэтому мы будем запрашивать маркер здесь. 

Чтобы получить маркер, используйте метод RequestTokenAsync следующим образом. 

```C#
private async void GetMsaTokenAsync(WebAccountProviderCommand command)
{
    WebTokenRequest request = new WebTokenRequest(command.WebAccountProvider, "wl.basic");
    WebTokenRequestResult = await WebAuthenticationCoreManager.RequestTokenAsync(request);
}
```

В этом примере мы передаем строку "wl.basic" параметру scope. Scope (область) представляет собой тип информации, которую вы запрашиваете у поставщика о конкретном пользователе. Некоторые области предоставляют доступ только к базовой информации пользователя, такой как имя и адрес электронной почты. Другие области могут предоставлять доступ к конфиденциальной информации, такой как фотографии или входящие сообщения пользователя. Как правило, ваше приложение должно использовать область, для которой необходимо наименьшее количество разрешений, если для него не требуются дополнительные разрешения (например, не запрашивайте доступ к конфиденциальной информации если она не обязательна для работы приложения. 

Поставщики услуг предоставляют документацию, описывающую области, которые необходимо указать для получения маркеров, которые будут использоваться с их службой. 

* Описание областей Office 365 и Outlook.com см. в разделе (Проверка подлинности API Office 365 и Outlook.com с помощью конечной точки проверки подлинности версии 2.0)[https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2]. 
* Информацию о OneDrive см. в разделе (Проверка подлинности и вход в OneDrive)[https://dev.onedrive.com/auth/msa_oauth.htm#authentication-scopes]. 

Если вы разрабатываете корпоративное приложение, вы скорее всего захотите подключиться к экземпляру Azure Active Directory (AAD) и использовать Microsoft Graph API вместо обычных служб учетной записи Майкрософт. В этом случае используйте следующий код: 

```C#
private async void GetAadTokenAsync(WebAccountProviderCommand command)
{
    string clientId = "your_guid_here"; // Obtain your clientId from the Azure Portal
    WebTokenRequest request = new WebTokenRequest(provider, "User.Read", clientId);
    request.Properties.Add("resource", "https://graph.microsoft.com");
    WebTokenRequestResult = await WebAuthenticationCoreManager.RequestTokenAsync(request);
}
```

В остальной части этой статьи продолжается описание сценария с учетной записью Майкрософт, но код для AAD очень похож на этот пример. Дополнительные сведения о AAD/Graph, включая полный пример в GitHub, см. в [документации Microsoft Graph](https://graph.microsoft.io/docs/platform/get-started).

## Использование маркера

Метод RequestTokenAsync возвращает объект WebTokenRequestResult, содержащий результаты запроса. Если запрос выполнен успешно, он будет содержать маркер.  

```C#
private async void GetMsaTokenAsync(WebAccountProviderCommand command)
{
    WebTokenRequest request = new WebTokenRequest(command.WebAccountProvider, "wl.basic");
    WebTokenRequestResult result = await WebAuthenticationCoreManager.RequestTokenAsync(request);
    
    if (result.ResponseStatus == WebTokenRequestStatus.Success)
    {
        string token = result.ResponseData[0].Token; 
    }
}
```

> Примечание. Если вы получаете ошибку при запросе маркера, убедитесь, что ваше приложение связано с Магазином, как описано в шаге 1. Ваше приложение не сможет получить маркер, если вы пропустили этот шаг. 

После получения маркера можно использовать его для вызова API вашего поставщика. В следующем коде мы вызываем API Microsoft Live, чтобы получить основную информацию о пользователе и отобразить ее в пользовательском интерфейсе. 

```C#
private async void GetMsaTokenAsync(WebAccountProviderCommand command)
{
    WebTokenRequest request = new WebTokenRequest(command.WebAccountProvider, "wl.basic");
    WebTokenRequestResult result = await WebAuthenticationCoreManager.RequestTokenAsync(request);
    
    if (result.ResponseStatus == WebTokenRequestStatus.Success)
    {
        string token = result.ResponseData[0].Token; 
        
        var restApi = new Uri(@"https://apis.live.net/v5.0/me?access_token=" + token);

        using (var client = new HttpClient())
        {
            var infoResult = await client.GetAsync(restApi);
            string content = await infoResult.Content.ReadAsStringAsync();

            var jsonObject = JsonObject.Parse(content);
            string id = jsonObject["id"].GetString();
            string name = jsonObject["name"].GetString();

            UserIdTextBlock.Text = "Id: " + id; 
            UserNameTextBlock.Text = "Name: " + name;
        }
    }
}
```

Способы вызова API REST различаются в зависимости от поставщика; сведения об использовании вашего маркера см. в документации по API поставщика. 

## Сохранение состояния учетной записи

Маркеры полезны для мгновенного получения сведений о пользователе, но как правило имеют различный срок действия. Маркеры учетной записи Майкрософт, например, действительны в течение всего нескольких часов. К счастью, повторно отображать AccountsSettingsPane каждый раз, когда истекает срок действия маркера, не требуется. Пользователю достаточно разрешить доступ вашему приложению лишь однажды, после чего вы сможете хранить сведения об учетной записи для последующего использования. 

Для этого используйте класс WebAccount. WebAccount возвращается вместе с запросом маркера:

```C#
private async void GetMsaTokenAsync(WebAccountProviderCommand command)
{
    WebTokenRequest request = new WebTokenRequest(command.WebAccountProvider, "wl.basic");
    WebTokenRequestResult result = await WebAuthenticationCoreManager.RequestTokenAsync(request);
    
    if (result.ResponseStatus == WebTokenRequestStatus.Success)
    {
        WebAccount account = result.ResponseData[0].WebAccount; 
    }
}
```

Получив WebAccount, можно с легкостью его сохранить. В следующем примере мы используем LocalSettings: 

```C#
private async void StoreWebAccount(WebAccount account)
{
    ApplicationData.Current.LocalSettings.Values["CurrentUserProviderId"] = account.WebAccountProvider.Id;
    ApplicationData.Current.LocalSettings.Values["CurrentUserId"] = account.Id; 
}
```

В следующий раз, когда пользователь запустит приложение, можно попытаться получить маркер без вывода уведомлений (в фоновом режиме) следующим образом: 

```C#
private async Task<string> GetTokenSilentlyAsync()
{
    string providerId = ApplicationData.Current.LocalSettings.Values["CurrentUserProviderId"]?.ToString();
    string accountId = ApplicationData.Current.LocalSettings.Values["CurrentUserId"]?.ToString();

    if (null == providerId || null == accountId)
    {
        return null; 
    }

    WebAccountProvider provider = await WebAuthenticationCoreManager.FindAccountProviderAsync(providerId);
    WebAccount account = await WebAuthenticationCoreManager.FindAccountAsync(provider, accountId);

    WebTokenRequest request = new WebTokenRequest(provider, "wl.basic");

    WebTokenRequestResult result = await WebAuthenticationCoreManager.GetTokenSilentlyAsync(request, account);
    if (result.ResponseStatus == WebTokenRequestStatus.UserInteractionRequired)
    {
        // Unable to get a token silently - you'll need to show the UI
        return null; 
    }
    else if (result.ResponseStatus == WebTokenRequestStatus.Success)
    {
        // Success
        return result.ResponseData[0].Token;
    }
    else
    {
        // Other error 
        return null; 
    }
}
```

Поскольку процесс получения маркера без уведомлений очень прост, необходимо использовать этот процесс для обновления маркера между сеансами, а не помещать существующий маркер в кэш (так как срок действия маркера может истечь в любой момент).

Обратите внимание, что в приведенном выше примере рассматриваются только базовые случаи успеха или отказа. Ваше приложение также должно предусматривать необычные сценарии (например отмену пользователем разрешения для вашего приложения или удаление учетной записи из Windows) и корректно обрабатывать их.  

## Выход из учетной записи 

Если вы сохраняете WebAccount, можно реализовать функцию "выхода из системы", чтобы ваши пользователи могли сменить учетную запись или просто отвязать учетную запись от приложения. Для этого сначала удалите все сохраненные сведения об учетной записи и поставщике. Затем вызовите WebAccount.SignOutAsync() для очистки кэша и аннулируйте все имеющиеся в приложении маркеры. 

```C#
private async Task SignOutAccountAsync(WebAccount account)
{
    ApplicationData.Current.LocalSettings.Values.Remove("CurrentUserProviderId");
    ApplicationData.Current.LocalSettings.Values.Remove("CurrentUserId"); 
    account.SignOutAsync(); 
}
```

## Добавление поставщиков, не поддерживающих WebAccountManager

Если вы хотите включить в ваше приложение проверку подлинности службы, которая не поддерживает WebAccountManager (например Google+ или Twitter), вы можете вручную добавить этого поставщика на AccountsSettingsPane. Для этого создайте новый объект WebAccountProvider, укажите собственное имя поставщика и значок с расширением PNG, после чего добавьте объект в WebAccountProviderCommands. Вот код заглушки: 

 ```C#
private async void BuildPaneAsync(AccountsSettingsPane s, AccountsSettingsPaneCommandsRequestedEventArgs e)
{
    // other code here 

    var twitterProvider = new WebAccountProvider("twitter", "Twitter", new Uri(@"ms-appx:///Assets/twitter-auth-icon.png")); 
    var twitterCmd = new WebAccountProviderCommand(twitterProvider, GetTwitterTokenAsync);
    e.WebAccountProviderCommands.Add(twitterCmd);   
    
    // other code here
}

private async void GetTwitterTokenAsync(WebAccountProviderCommand command)
{
    // Manually handle Twitter login here
}

```

Обратите внимание, что данный код лишь добавляет значок в AccountsSettingsPane и при нажатии на значок запускает определенный вами метод (в данном случае — GetTwitterTokenAsync). Вам необходимо написать код, который будет обрабатывать сам процесс проверки подлинности. Дополнительные сведения см. в разделе (Брокер веб-проверки подлинности)[web-authentication-broker], в котором описаны вспомогательные методы проверки подлинности с помощью служб REST. 

## Добавление настраиваемого заголовка

Панель параметров учетных записей можно настроить с помощью свойства HeaderText следующим образом: 

```C#
private async void BuildPaneAsync(AccountsSettingsPane s, AccountsSettingsPaneCommandsRequestedEventArgs e)
{
    // other code here 
    
    args.HeaderText = "MyAwesomeApp works best if you're signed in.";   
    
    // other code here
}
```

![Панель параметров учетных записей](images/tb-3.png)

Не делайте текст заголовка слишком длинным, пусть он будет лаконичным и понятным. Если процесс входа усложнен и вам необходимо отобразить дополнительные сведения, добавьте пользовательскую ссылку на отдельную страницу. 

## Добавление настраиваемых ссылок

На AccountsSettingsPane можно добавлять пользовательские команды, которые отображаются в виде ссылок под списком поддерживаемых WebAccountProviders. Пользовательские команды прекрасно подходят для выполнения простых задач, связанных с учетной записью пользователя, таких как отображение политики конфиденциальности или переход на страницу поддержки пользователей, столкнувшихся с проблемами. 

Пример. 

```C#
private async void BuildPaneAsync(AccountsSettingsPane s, AccountsSettingsPaneCommandsRequestedEventArgs e)
{
    // other code here 
    
    var settingsCmd = new SettingsCommand(
        "settings_privacy", 
        "Privacy policy", 
        async (x) => await Launcher.LaunchUriAsync(new Uri(@"https://privacy.microsoft.com/en-US/"))); 

    e.Commands.Add(settingsCmd); 
    
    // other code here
}
```

![Панель параметров учетных записей](images/tb-4.png)

Теоретически команды параметров можно использовать для любых целей. Однако мы рекомендуем ограничить их использование простыми сценариями, связанными с учетными записями, такими как описанные выше. 

## См. также

[Пространство имен Windows.Security.Authentication.Web.Core](https://msdn.microsoft.com/library/windows/apps/windows.security.authentication.web.core.aspx)

[Пространство имен Windows.Security.Credentials](https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.aspx)

[AccountsSettingsPane](https://msdn.microsoft.com/library/windows/apps/windows.ui.applicationsettings.accountssettingspane)

[Брокер веб-проверки подлинности](web-authentication-broker.md)

[Пример WebAccountManagement](http://go.microsoft.com/fwlink/p/?LinkId=620621)



<!--HONumber=Nov16_HO1-->


