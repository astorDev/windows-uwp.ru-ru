---
title: Создание службы входа Windows Hello
description: Это вторая часть полного пошагового руководства по использованию Windows Hello в качестве альтернативы традиционным системам проверки подлинности с помощью имени пользователя и пароля в приложениях универсальной платформы Windows 10 (UWP).
ms.assetid: ECC9EF3D-E0A1-4BC4-94FA-3215E6CFF0E4
author: PatrickFarley
ms.author: pafarley
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, безопасность
ms.localizationpriority: medium
ms.openlocfilehash: 839d44c992977fdad8863203b84116b2faeba76d
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2018
ms.locfileid: "5692877"
---
# <a name="create-a-windows-hello-login-service"></a>Создание службы входа Windows Hello

Это вторая часть полного пошагового руководства по использованию Windows Hello в качестве альтернативы традиционным системам проверки подлинности с помощью имени пользователя и пароля в приложениях универсальной платформы Windows 10 (UWP). Эта статья является продолжением первой части [Приложение для входа с использованием Windows Hello](microsoft-passport-login.md) и описывает расширение функций, чтобы продемонстрировать интеграцию Windows Hello в существующие приложения.

Для создания этого проекта необходим опыт работы с C# и XAML. Вам также потребуется использовать Visual Studio 2015 (Community Edition или более старший выпуск) на компьютере под управлением Windows 10.

## <a name="exercise-1-server-side-logic"></a>Упражнение 1. Логика на стороне сервера


В этом упражнении вы будете использовать приложение для Windows Hello, созданное на первом практикуме, и создадите локальные сервер и базу данных. В этом практическом занятии вы узнаете, как интегрировать Windows Hello в существующую систему. С помощью макетного сервера и базы данных устраняется необходимость в настройке многих несущественных параметров. В ваших собственных приложениях потребуется заменить макетные объекты реальными службами и базами данных.

-   Откройте решение PassportLogin из первого практического занятия по Microsoft Passport.
-   Начнем с реализации имитации сервера и базы данных. Создайте новую папку с именем "AuthService". Для этого щелкните правой кнопкой мыши решение «PassportLogin (универсальные приложения для Windows)» в обозревателе решений и выберите «Добавить > Новая папка».
-   Создайте классы UserAccount и PassportDevices, которые будут использоваться в качестве моделей данных, сохраняемых в макетной базе данных. UserAccount будет аналогичен модели пользователя, реализованной на традиционном сервере проверки подлинности. Щелкните правой кнопкой мыши папку AuthService и добавьте класс "UserAccount.cs".

    ![Создание папки авторизации Windows Hello](images/passport-auth-1.png)

    ![Создание класса авторизации Windows Hello](images/passport-auth-2.png)

-   Измените определение класса на общий (public), а затем добавьте следующие открытые свойства. Вам потребуется следующая справочная информация.

    ```cs
    using System.ComponentModel.DataAnnotations;
     
    namespace PassportLogin.AuthService
    {
        public class UserAccount
        {
            [Key, Required]
            public Guid UserId { get; set; }
            [Required]
            public string Username { get; set; }
            public string Password { get; set; }
            // public List<PassportDevice> PassportDevices = new List<PassportDevice>();
        }
    }
    ```

    Возможно, вы заметили закомментированный список PassportDevices. Это изменение следует внести в существующую модель пользователя в текущей реализации. Список PassportDevices будет содержать deviceID, открытый ключ, созданный в Windows Hello, и класс [**KeyCredentialAttestationResult**](https://msdn.microsoft.com/library/windows/apps/dn973034). В этом практическом занятии вам потребуется реализовать keyAttestationResult, поскольку аттестация ключа предоставляется Windows Hello только на устройствах с микросхемой доверенного платформенного модуля (TPM). Класс **KeyCredentialAttestationResult** представляет собой комбинацию нескольких свойств и должен быть разделен для сохранения свойств в базе данных и их загрузки из базы данных.

-   Создайте новый класс под названием "PassportDevice.cs" в папке AuthService. Это модель для устройств Windows Hello, упомянутая ранее. Измените определение класса на общий (public) и добавьте следующие свойства.

    ```cs
    namespace PassportLogin.AuthService
    {
        public class PassportDevice
        {
            // These are the new variables that will need to be added to the existing UserAccount in the Database
            // The DeviceName is used to support multiple devices for the one user.
            // This way the correct public key is easier to find as a new public key is made for each device.
            // The KeyAttestationResult is only used if the User device has a TPM (Trusted Platform Module) chip, 
            // in most cases it will not. So will be left out for this hands on lab.
            public Guid DeviceId { get; set; }
            public byte[] PublicKey { get; set; }
            // public KeyCredentialAttestationResult KeyAttestationResult { get; set; }
        }
    }
    ```

-   Вернитесь в UserAccount.cs и удалите комментарий для списка устройств Windows Hello.

    ```cs
    using System.Collections.Generic;
     
    namespace PassportLogin.AuthService
    {
        public class UserAccount
        {
            [Key, Required]
            public Guid UserId { get; set; }
            [Required]
            public string Username { get; set; }
            public string Password { get; set; }
            public List<PassportDevice> PassportDevices = new List<PassportDevice>();
        }
    }
    ```

-   После создания моделей UserAccount и PassportDevice необходимо создать в AuthService еще один класс, который будет служить макетной базой данных. Это макетная база данных, которую вы будете использовать для сохранения и загрузки списка пользовательских учетных записей локально. В реальных сценариях это была бы ваша реализация базы данных. Создайте в AuthService новый класс "MockStore.cs". Измените определение класса на public.
-   Так как макетное хранилище сохраняет и загружает список учетных записей пользователей локально, вы можете реализовать логику сохранения и загрузки списка, используя XmlSerializer. Вам также необходимо запомнить имя файла и его расположение. Реализуйте в файле MockStore.cs следующие элементы.

```cs
    using System.IO;
    using System.Linq;
    using System.Xml.Serialization;
    using Windows.Storage;

    namespace PassportLogin.AuthService
    {
        public class MockStore
        {
            private const string USER_ACCOUNT_LIST_FILE_NAME = "userAccountsList.txt";
            // This cannot be a const because the LocalFolder is accessed at runtime
            private string _userAccountListPath = Path.Combine(
                ApplicationData.Current.LocalFolder.Path, USER_ACCOUNT_LIST_FILE_NAME);
            private List<UserAccount> _mockDatabaseUserAccountsList;
     
    #region Save and Load Helpers
            /// <summary>
            /// Create and save a useraccount list file. (Replacing the old one)
            /// </summary>
            private async void SaveAccountListAsync()
            {
                string accountsXml = SerializeAccountListToXml();
     
                if (File.Exists(_userAccountListPath))
                {
                    StorageFile accountsFile = await StorageFile.GetFileFromPathAsync(_userAccountListPath);
                    await FileIO.WriteTextAsync(accountsFile, accountsXml);
                }
                else
                {
                    StorageFile accountsFile = await ApplicationData.Current.LocalFolder.CreateFileAsync(USER_ACCOUNT_LIST_FILE_NAME);
                    await FileIO.WriteTextAsync(accountsFile, accountsXml);
                }
            }
     
            /// <summary>
            /// Gets the useraccount list file and deserializes it from XML to a list of useraccount objects.
            /// </summary>
            /// <returns>List of useraccount objects</returns>
            private async void LoadAccountListAsync()
            {
                if (File.Exists(_userAccountListPath))
                {
                    StorageFile accountsFile = await StorageFile.GetFileFromPathAsync(_userAccountListPath);
     
                    string accountsXml = await FileIO.ReadTextAsync(accountsFile);
                    DeserializeXmlToAccountList(accountsXml);
                }
     
                // If the UserAccountList does not contain the sampleUser Initialize the sample users
                // This is only needed as it in a Hand on Lab to demonstrate a user migrating
                // In the real world user accounts would just be in a database
                if (!_mockDatabaseUserAccountsList.Any(f => f.Username.Equals("sampleUsername")))
                {
                    //If the list is empty InitializeSampleAccounts and return the list
                    //InitializeSampleUserAccounts();
                }
            }
     
            /// <summary>
            /// Uses the local list of accounts and returns an XML formatted string representing the list
            /// </summary>
            /// <returns>XML formatted list of accounts</returns>
            private string SerializeAccountListToXml()
            {
                XmlSerializer xmlizer = new XmlSerializer(typeof(List<UserAccount>));
                StringWriter writer = new StringWriter();
                xmlizer.Serialize(writer, _mockDatabaseUserAccountsList);
                return writer.ToString();
            }
     
            /// <summary>
            /// Takes an XML formatted string representing a list of accounts and returns a list object of accounts
            /// </summary>
            /// <param name="listAsXml">XML formatted list of accounts</param>
            /// <returns>List object of accounts</returns>
            private List<UserAccount> DeserializeXmlToAccountList(string listAsXml)
            {
                XmlSerializer xmlizer = new XmlSerializer(typeof(List<UserAccount>));
                TextReader textreader = new StreamReader(new MemoryStream(Encoding.UTF8.GetBytes(listAsXml)));
                return _mockDatabaseUserAccountsList = (xmlizer.Deserialize(textreader)) as List<UserAccount>;
            }
    #endregion
        }
    }
```

-   Возможно, вы заметили, что метод InitializeSampleUserAccounts в методе Load закомментирован. Необходимо создать этот метод в MockStore.cs. Он будет заполнять список учетных записей, чтобы можно было произвести вход. В реальных сценариях база данных пользователей будет уже заполнена. На этом шаге вы также создадите конструктор для инициализации списка пользователей и вызова загрузки.

    ```cs
    namespace PassportLogin.AuthService
    {
        public class MockStore
        {
            private const string USER_ACCOUNT_LIST_FILE_NAME = "userAccountsList.txt";
            // This cannot be a const because the LocalFolder is accessed at runtime
            private string _userAccountListPath = Path.Combine(
                ApplicationData.Current.LocalFolder.Path, USER_ACCOUNT_LIST_FILE_NAME);
            private List<UserAccount> _mockDatabaseUserAccountsList;
     
            public MockStore()
            {
                _mockDatabaseUserAccountsList = new List& lt; UserAccount & gt; ();
                LoadAccountListAsync();
            }

            private void InitializeSampleUserAccounts()
            {
                // Create a sample Traditional User Account that only has a Username and Password
                // This will be used initially to demonstrate how to migrate to use Windows Hello

                UserAccount sampleUserAccount = new UserAccount()
                {
                    UserId = Guid.NewGuid(),
                    Username = "sampleUsername",
                    Password = "samplePassword",
                };

                // Add the sampleUserAccount to the _mockDatabase
                _mockDatabaseUserAccountsList.Add(sampleUserAccount);
                SaveAccountListAsync();
            }
        }
    }
    ```

-   Теперь, когда метод InitalizeSampleUserAccounts существует, удалите комментарий вызова этого метода в методе LoadAccountListAsync.

    ```cs
    private async void LoadAccountListAsync()
    {
        if (File.Exists(_userAccountListPath))
        {
            StorageFile accountsFile = await StorageFile.GetFileFromPathAsync(_userAccountListPath);

            string accountsXml = await FileIO.ReadTextAsync(accountsFile);
            DeserializeXmlToAccountList(accountsXml);
        }

        // If the UserAccountList does not contain the sampleUser Initialize the sample users
        // This is only needed as it in a Hand on Lab to demonstrate a user migrating
        // In the real world user accounts would just be in a database
        if (!_mockDatabaseUserAccountsList.Any(f = > f.Username.Equals("sampleUsername")))
                {
            //If the list is empty InitializeSampleAccounts and return the list
            InitializeSampleUserAccounts();
        }
    }
    ```

-   Теперь можно использовать макетное хранилище для сохранения и загрузки списка учетных записей пользователей. Доступ к этому списку потребуется и другим частям приложения, поэтому необходимо реализовать методы для получения этих данных. Добавьте следующие методы Get в метод InitializeSampleUserAccounts. Они позволят вам получить идентификатор пользователя, одиночного пользователя, список пользователей на определенном устройстве Windows Hello, а также открытый ключ для пользователя определенного устройства.

    ```cs
    public Guid GetUserId(string username)
    {
        if (_mockDatabaseUserAccountsList.Any())
        {
            UserAccount account = _mockDatabaseUserAccountsList.FirstOrDefault(f => f.Username.Equals(username));
            if (account != null)
            {
                return account.UserId;
            }
        }
        return Guid.Empty;
    }

    public UserAccount GetUserAccount(Guid userId)
    {
        return _mockDatabaseUserAccountsList.FirstOrDefault(f => f.UserId.Equals(userId));
    }

    public List<UserAccount> GetUserAccountsForDevice(Guid deviceId)
    {
        List<UserAccount> usersForDevice = new List<UserAccount>();

        foreach (UserAccount account in _mockDatabaseUserAccountsList)
        {
            if (account.PassportDevices.Any(f => f.DeviceId.Equals(deviceId)))
            {
                usersForDevice.Add(account);
            }
        }

        return usersForDevice;
    }

    public byte[] GetPublicKey(Guid userId, Guid deviceId)
    {
        UserAccount account = _mockDatabaseUserAccountsList.FirstOrDefault(f => f.UserId.Equals(userId));
        if (account != null)
        {
            if (account.PassportDevices.Any())
            {
                return account.PassportDevices.FirstOrDefault(p => p.DeviceId.Equals(deviceId)).PublicKey;
            }
        }
        return null;
    }
    ```

-   Следующие методы, которые необходимо реализовать, обрабатывают простые операции добавления и удаления учетной записи, а также удаления устройства. Удаление устройства необходимо, так как Windows Hello учитывает конкретное устройство. Windows Hello создает новую пару открытых и закрытых ключей для каждого устройства, на котором выполняется вход. Это похоже на использование для входа отдельного пароля для каждого устройства, но при этом вам не нужно запоминать все эти пароли, так как за вас это делает сервер. Добавьте в файл MockStore.cs следующие методы.

    ```cs
    public UserAccount AddAccount(string username)
    {
        UserAccount newAccount = null;
        try
        {
            newAccount = new UserAccount()
            {
                UserId = Guid.NewGuid(),
                Username = username,
            };

            _mockDatabaseUserAccountsList.Add(newAccount);
            SaveAccountListAsync();
        }
        catch (Exception)
        {
            throw;
        }
        return newAccount;
    }

    public bool RemoveAccount(Guid userId)
    {
        UserAccount userAccount = GetUserAccount(userId);
        if (userAccount != null)
        {
            _mockDatabaseUserAccountsList.Remove(userAccount);
            SaveAccountListAsync();
            return true;
        }
        return false;
    }

    public bool RemoveDevice(Guid userId, Guid deviceId)
    {
        UserAccount userAccount = GetUserAccount(userId);
        PassportDevice deviceToRemove = null;
        if (userAccount != null)
        {
            foreach (PassportDevice device in userAccount.PassportDevices)
            {
                if (device.DeviceId.Equals(deviceId))
                {
                    deviceToRemove = device;
                    break;
                }
            }
        }

        if (deviceToRemove != null)
        {
            //Remove the PassportDevice
            userAccount.PassportDevices.Remove(deviceToRemove);
            SaveAccountListAsync();
        }

        return true;
    }
    ```

- В класс MockStore добавьте метод, который вносит данные Windows Hello в существующий UserAccount. Этот метод будет называться PassportUpdateDetails и использовать параметры для идентификации пользователя и сведения Windows Hello. KeyAttestationResult при создании PassportDevice был закомментирован. В реальном приложении вам потребуется его использовать.

    ```cs
    using Windows.Security.Credentials;

    public void PassportUpdateDetails(Guid userId, Guid deviceId, byte[] publicKey, 
        KeyCredentialAttestationResult keyAttestationResult)
    {
        UserAccount existingUserAccount = GetUserAccount(userId);
        if (existingUserAccount != null)
        {
            if (!existingUserAccount.PassportDevices.Any(f => f.DeviceId.Equals(deviceId)))
            {
                existingUserAccount.PassportDevices.Add(new PassportDevice()
                {
                    DeviceId = deviceId,
                    PublicKey = publicKey,
                    // KeyAttestationResult = keyAttestationResult
                });
            }
        }
        SaveAccountListAsync();
    }
    ```

- Класс MockStore готов, и поскольку он представляет базу данных, то должен считаться закрытым. Для доступа к MockStore и управления содержимым базы данных требуется класс AuthService. Создайте новый класс под названием "AuthService.cs" в папке AuthService. Измените определение класса на public и добавьте одноэлементный шаблон экземпляра, чтобы всегда создавался только один экземпляр класса.

    ```cs
    namespace PassportLogin.AuthService
    {
        public class AuthService
        {
            // Singleton instance of the AuthService
            // The AuthService is a mock of what a real world server and service implementation would be
            private static AuthService _instance;
            public static AuthService Instance
            {
                get
                {
                    if (null == _instance)
                    {
                        _instance = new AuthService();
                    }
                    return _instance;
                }
            }

            private AuthService()
            { }
        }
    }
    ```

-   Класс AuthService должен создавать экземпляр класса MockStore и обеспечивать доступ к свойствам объекта MockStore.

    ```cs
    namespace PassportLogin.AuthService
    {
        public class AuthService
        {
            //Singleton instance of the AuthService
            //The AuthService is a mock of what a real world server and database implementation would be
            private static AuthService _instance;
            public static AuthService Instance
            {
                get
                {
                    if (null == _instance)
                    {
                        _instance = new AuthService();
                    }
                    return _instance;
                }
            }
     
            private MockStore _mockStore = new MockStore();
     
            public Guid GetUserId(string username)
            {
                return _mockStore.GetUserId(username);
            }
     
            public UserAccount GetUserAccount(Guid userId)
            {
                return _mockStore.GetUserAccount(userId);
            }
     
            public List<UserAccount> GetUserAccountsForDevice(Guid deviceId)
            {
                return _mockStore.GetUserAccountsForDevice(deviceId);
            }
        }
    }
    ```

-   В классе AuthService вам потребуются методы для доступа к методам добавления, удаления и изменения сведений Microsoft Passport в объекте MockStore. Добавьте в конец класса AuthService следующие методы.

    ```cs
    using Windows.Security.Credentials;

    public void Register(string username)
    {
        _mockStore.AddAccount(username);
    }

    public bool PassportRemoveUser(Guid userId)
    {
        return _mockStore.RemoveAccount(userId);
    }

    public bool PassportRemoveDevice(Guid userId, Guid deviceId)
    {
        return _mockStore.RemoveDevice(userId, deviceId);
    }

    public void PassportUpdateDetails(Guid userId, Guid deviceId, byte[] publicKey, 
        KeyCredentialAttestationResult keyAttestationResult)
    {
        _mockStore.PassportUpdateDetails(userId, deviceId, publicKey, keyAttestationResult);
    }
    ```

-   Классу AuthService потребуется метод для проверки учетных данных. Этот метод принимает имя пользователя и пароль и проверяет существование учетной записи и действительность пароля. В существующей системе должен быть аналогичный метод, проверяющий, авторизован ли пользователь. Добавьте в файл AuthService.cs следующий метод ValidateCredentials.

    ```cs
    public bool ValidateCredentials(string username, string password)
    {
        if (!string.IsNullOrEmpty(username) && !string.IsNullOrEmpty(password))
        {
            // This would be used for existing accounts migrating to use Passport
            Guid userId = GetUserId(username);
            if (userId != Guid.Empty)
            {
                UserAccount account = GetUserAccount(userId);
                if (account != null)
                {
                    if (string.Equals(password, account.Password))
                    {
                        return true;
                    }
                }
            }
        }
        return false;
    }
    ```

-   Классу AuthService нужен метод запроса задачи, который возвращает задачу клиенту для проверки подлинности пользователя. Также в классе AuthService необходим метод для получения подписанной задачи от клиента. В этом практическом занятии метод, определяющий завершение подписанной задачи, оставлен незаконченным. Реализации Windows Hello в существующих системах проверки подлинности будут немного отличаться друг от друга. Хранящийся на сервере открытый ключ должен совпадать с результатом, возвращаемым клиентом на сервер. Добавьте эти два метода в файл AuthService.cs.

    ```cs
    using Windows.Security.Cryptography;
    using Windows.Storage.Streams;

    public IBuffer PassportRequestChallenge()
    {
        return CryptographicBuffer.ConvertStringToBinary("ServerChallenge", BinaryStringEncoding.Utf8);
    }

    public bool SendServerSignedChallenge(Guid userId, Guid deviceId, byte[] signedChallenge)
    {
        // Depending on your company polices and procedures this step will be different
        // It is at this point you will need to validate the signedChallenge that is sent back from the client.
        // Validation is used to ensure the correct user is trying to access this account. 
        // The validation process will use the signedChallenge and the stored PublicKey 
        // for the username and the specific device signin is called from.
        // Based on the validation result you will return a bool value to allow access to continue or to block the account.

        // For this sample validation will not happen as a best practice solution does not apply and will need to 
           // be configured for each company.
        // Simply just return true.

        // You could get the User's Public Key with something similar to the following:
        byte[] userPublicKey = _mockStore.GetPublicKey(userId, deviceId);
        return true;
    }
    ```

## <a name="exercise-2-client-side-logic"></a>Упражнение 2. Логика на стороне клиента

В этом упражнении вы будете вносить изменения в представления и вспомогательные классы на стороне клиента для использования класса AuthService. В реальных сценариях AuthService будет сервером проверки подлинности, и вам потребуется использовать веб-API для отправки и получения данных с сервера. В этом практическом занятии используются локальные клиент и сервер для упрощения разработки. Цель занятия — научиться использовать API Windows Hello.

-   В файле MainPage.xaml.cs вызов метода AccountHelper.LoadAccountListAsync в методе loaded можно удалить, так как класс AuthService создает экземпляр MockStore, загружающий список учетных записей. Метод loaded теперь должен выглядеть следующим образом. Обратите внимание, что определение асинхронного метода удалено, так как отсутствуют ожидающие задачи.

    ```cs
    private void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        Frame.Navigate(typeof(UserSelection));
    }
    ```

-   Обновите интерфейс страницы входа для запроса ввода паспорта. В этом практическом занятии показано, как перевести существующую систему на использование Windows Hello. Существующие учетные записи будут иметь имя пользователя и пароль. Измените объяснение в нижней части XAML, добавив пароль по умолчанию. Измените следующий код XAML в файле Login.xaml.

    ```xml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
      <StackPanel Orientation="Vertical">
        <TextBlock Text="Login" FontSize="36" Margin="4" TextAlignment="Center"/>

        <TextBlock x:Name="ErrorMessage" Text="" FontSize="20" Margin="4" Foreground="Red" TextAlignment="Center"/>

        <TextBlock Text="Enter your credentials below" Margin="0,0,0,20"
                   TextWrapping="Wrap" Width="300"
                   TextAlignment="Center" VerticalAlignment="Center" FontSize="16"/>

        <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
          <!-- Username Input -->
          <TextBlock x:Name="UserNameTextBlock" Text="Username: "
                 FontSize="20" Margin="4" Width="100"/>
          <TextBox x:Name="UsernameTextBox" PlaceholderText="sampleUsername" Width="200" Margin="4"/>
        </StackPanel>

        <StackPanel Orientation="Horizontal" HorizontalAlignment="Center">
          <!-- Password Input -->
          <TextBlock x:Name="PasswordTextBlock" Text="Password: "
                 FontSize="20" Margin="4" Width="100"/>
          <PasswordBox x:Name="PasswordBox" PlaceholderText="samplePassword" Width="200" Margin="4"/>
        </StackPanel>

        <Button x:Name="PassportSignInButton" Content="Login" Background="DodgerBlue" Foreground="White"
            Click="PassportSignInButton_Click" Width="80" HorizontalAlignment="Center" Margin="0,20"/>

        <TextBlock Text="Don't have an account?"
                    TextAlignment="Center" VerticalAlignment="Center" FontSize="16"/>
        <TextBlock x:Name="RegisterButtonTextBlock" Text="Register now"
                   PointerPressed="RegisterButtonTextBlock_OnPointerPressed"
                   Foreground="DodgerBlue"
                   TextAlignment="Center" VerticalAlignment="Center" FontSize="16"/>

        <Border x:Name="PassportStatus" Background="#22B14C"
                   Margin="0,20" Height="100">
          <TextBlock x:Name="PassportStatusText" Text="Windows Hello is ready to use!"
                 Margin="4" TextAlignment="Center" VerticalAlignment="Center" FontSize="20"/>
        </Border>

        <TextBlock x:Name="LoginExplaination" FontSize="24" TextAlignment="Center" TextWrapping="Wrap" 
            Text="Please Note: To demonstrate a login, validation will only occur using the default username 
            'sampleUsername' and default password 'samplePassword'"/>

      </StackPanel>
    </Grid>
    ```

-   В программной части кода класса Login вам необходимо изменить частную переменную Account вверху кода на UserAccount. Измените событие OnNavigateTo для передачи типа UserAccount. Вам потребуется следующая справочная информация.

    ```cs
    using PassportLogin.AuthService;

    namespace PassportLogin.Views
    {
        public sealed partial class Login : Page
        {
            private UserAccount _account;
            private bool _isExistingAccount;

            public Login()
            {
                this.InitializeComponent();
            }

            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                //Check Windows Hello is setup and available on this machine
                if (await MicrosoftPassportHelper.MicrosoftPassportAvailableCheckAsync())
                {
                    if (e.Parameter != null)
                    {
                        _isExistingAccount = true;
                        //Set the account to the existing account being passed in
                        _account = (UserAccount)e.Parameter;
                        UsernameTextBox.Text = _account.Username;
                        SignInPassport();
                    }
                }
            }
        }
    }
    ```

-   Поскольку на странице входа используется объект UserAccount вместо предыдущего объекта Account, необходимо обновить класс MicrosoftPassportHelper.cs для использования UserAccount в качестве параметра для некоторых методов. Необходимо изменить следующие параметры для методов CreatePassportKeyAsync, RemovePassportAccountAsync и GetPassportAuthenticationMessageAsync. Так как в классе UserAccount в качестве идентификатора пользователя используется GUID, вы будете использовать идентификатор в большем количестве случаев для большей конкретики.

    ```cs
    public static async Task<bool> CreatePassportKeyAsync(Guid userId, string username)
    {
        KeyCredentialRetrievalResult keyCreationResult = await KeyCredentialManager.RequestCreateAsync(username, KeyCredentialCreationOption.ReplaceExisting);
    }

    public static async void RemovePassportAccountAsync(UserAccount account)
    {

    }
    public static async Task<bool> GetPassportAuthenticationMessageAsync(UserAccount account)
    {
        KeyCredentialRetrievalResult openKeyResult = await KeyCredentialManager.OpenAsync(account.Username);
        //Calling OpenAsync will allow the user access to what is available in the app and will not require user credentials again.
        //If you wanted to force the user to sign in again you can use the following:
        //var consentResult = await Windows.Security.Credentials.UI.UserConsentVerifier.RequestVerificationAsync(account.Username);
        //This will ask for the either the password of the currently signed in Microsoft Account or the PIN used for Windows Hello.

        if (openKeyResult.Status == KeyCredentialStatus.Success)
        {
            //If OpenAsync has succeeded, the next thing to think about is whether the client application requires access to backend services.
            //If it does here you would Request a challenge from the Server. The client would sign this challenge and the server
            //would check the signed challenge. If it is correct it would allow the user access to the backend.
            //You would likely make a new method called RequestSignAsync to handle all this
            //e.g. RequestSignAsync(openKeyResult);
            //Refer to the second Windows Hello sample for information on how to do this.

            //For this sample there is not concept of a server implemented so just return true.
            return true;
        }
        else if (openKeyResult.Status == KeyCredentialStatus.NotFound)
        {
            //If the _account is not found at this stage. It could be one of two errors. 
            //1. Windows Hello has been disabled
            //2. Windows Hello has been disabled and re-enabled cause the Windows Hello Key to change.
            //Calling CreatePassportKey and passing through the account will attempt to replace the existing Windows Hello Key for that account.
            //If the error really is that Windows Hello is disabled then the CreatePassportKey method will output that error.
            if (await CreatePassportKeyAsync(account.UserId, account.Username))
            {
                //If the Passport Key was again successfully created, Windows Hello has just been reset.
                //Now that the Passport Key has been reset for the _account retry sign in.
                return await GetPassportAuthenticationMessageAsync(account);
            }
        }

        // Can't use Passport right now, try again later
        return false;
    }
    ```

-   Метод SignInPassport в файле Login.xaml.cs нужно обновить для использования AuthService вместо AccountHelper. Проверка учетных данных происходит через AuthService. В этом практическом занятии настроена всего одна учетная запись — "sampleUsername". Эта учетная запись создается методом InitializeSampleUserAccounts в файле MockStore.cs. Включите приведенный ниже фрагмент кода в метод SignInPassport в файле Login.xaml.cs.

    ```cs
    private async void SignInPassportAsync()
    {
        if (_isExistingLocalAccount)
        {
            if (await MicrosoftPassportHelper.GetPassportAuthenticationMessageAsync(_account))
            {
                Frame.Navigate(typeof(Welcome), _account);
            }
        }
        else if (AuthService.AuthService.Instance.ValidateCredentials(UsernameTextBox.Text, PasswordBox.Password))
        {
            Guid userId = AuthService.AuthService.Instance.GetUserId(UsernameTextBox.Text);

            if (userId != Guid.Empty)
            {
                //Now that the account exists on server try and create the necessary passport details and add them to the account
                bool isSuccessful = await MicrosoftPassportHelper.CreatePassportKeyAsync(userId, UsernameTextBox.Text);
                if (isSuccessful)
                {
                    Debug.WriteLine("Successfully signed in with Windows Hello!");
                    //Navigate to the Welcome Screen. 
                    _account = AuthService.AuthService.Instance.GetUserAccount(userId);
                    Frame.Navigate(typeof(Welcome), _account);
                }
                else
                {
                    //The passport account creation failed.
                    //Remove the account from the server as passport details were not configured
                    AuthService.AuthService.Instance.PassportRemoveUser(userId);

                    ErrorMessage.Text = "Account Creation Failed";
                }
            }
        }
        else
        {
            ErrorMessage.Text = "Invalid Credentials";
        }
    }
    ```

-   Ввиду того что Windows Hello создает собственную пару открытого и закрытого ключей для каждой учетной записи на каждом устройстве, на странице приветствия необходимо отобразить список зарегистрированных устройств для текущей учетной записи и добавить возможность забыть каждое из них. Добавьте следующий код XAML под элементом ForgetButton в файле Welcome.xaml. Фрагмент реализует кнопку "Забыть устройство", текстовую область ошибки и список, отображающий все устройства.

    ```xml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
      <StackPanel Orientation="Vertical">
        <TextBlock x:Name="Title" Text="Welcome" FontSize="40" TextAlignment="Center"/>
        <TextBlock x:Name="UserNameText" FontSize="28" TextAlignment="Center" Foreground="Black"/>

        <Button x:Name="BackToUserListButton" Content="Back to User List" Click="Button_Restart_Click"
               HorizontalAlignment="Center" Margin="0,20" Foreground="White" Background="DodgerBlue"/>

        <Button x:Name="ForgetButton" Content="Forget Me" Click="Button_Forget_User_Click"
               Foreground="White"
               Background="Gray"
               HorizontalAlignment="Center"/>

        <Button x:Name="ForgetDeviceButton" Content="Forget Device" Click="Button_Forget_Device_Click"
               Foreground="White"
               Background="Gray"
               Margin="0,40,0,20"
               HorizontalAlignment="Center"/>

        <TextBlock x:Name="ForgetDeviceErrorTextBlock" Text="Select a device first"
                  TextWrapping="Wrap" Width="300" Foreground="Red"
                  TextAlignment="Center" VerticalAlignment="Center" FontSize="16" Visibility="Collapsed"/>

        <ListView x:Name="UserListView" MaxHeight="500" MinWidth="350" Width="350" HorizontalAlignment="Center">
          <ListView.ItemTemplate>
            <DataTemplate>
              <Grid Background="Gray" Height="50" Width="350" HorizontalAlignment="Center" VerticalAlignment="Stretch" >
                <TextBlock Text="{Binding DeviceId}" HorizontalAlignment="Center" TextAlignment="Center" VerticalAlignment="Center"
                          Foreground="White"/>
              </Grid>
            </DataTemplate>
          </ListView.ItemTemplate>
        </ListView>
      </StackPanel>
    </Grid>
    ```

-   В файле Welcome.xaml.cs нужно изменить частную переменную Account в верхней части класса на частную переменную UserAccount. Теперь отредактируйте метод OnNavigatedTo для использования AuthService и получения сведений для текущей учетной записи. При наличии сведений об учетной записи вы можете задать в свойстве ItemsSource отображение списка устройств. Вам потребуется добавить ссылку на пространство имен AuthService.

    ```cs
    using PassportLogin.AuthService;

    namespace PassportLogin.Views
    {
        public sealed partial class Welcome : Page
        {
            private UserAccount _activeAccount;

            public Welcome()
            {
                InitializeComponent();
            }

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                _activeAccount = (UserAccount)e.Parameter;
                if (_activeAccount != null)
                {
                    UserAccount account = AuthService.AuthService.Instance.GetUserAccount(_activeAccount.UserId);
                    if (account != null)
                    {
                        UserListView.ItemsSource = account.PassportDevices;
                        UserNameText.Text = account.Username;
                    }
                }
            }
        }
    }
    ```

-   Так как для удаления учетной записи вы будете использовать AuthService, ссылку на AccountHelper в методе Button\_Forget\_User\_Click можно убрать. Метод должен выглядеть следующим образом.

    ```cs
    private void Button_Forget_User_Click(object sender, RoutedEventArgs e)
    {
        //Remove it from Windows Hello
        MicrosoftPassportHelper.RemovePassportAccountAsync(_activeAccount);

        Debug.WriteLine("User " + _activeAccount.Username + " deleted.");

        //Navigate back to UserSelection page.
        Frame.Navigate(typeof(UserSelection));
    }
    ```

-   Метод MicrosoftPassportHelper не использует AuthService для удаления учетной записи. Необходимо вызвать AuthService и передать идентификатор.

    ```cs
    public static async void RemovePassportAccountAsync(UserAccount account)
    {
        //Open the account with Windows Hello
        KeyCredentialRetrievalResult keyOpenResult = await KeyCredentialManager.OpenAsync(account.Username);

        if (keyOpenResult.Status == KeyCredentialStatus.Success)
        {
            // In the real world you would send key information to server to unregister
            AuthService.AuthService.Instance.PassportRemoveUser(account.UserId);
        }

        //Then delete the account from the machines list of Passport Accounts
        await KeyCredentialManager.DeleteAsync(account.Username);
    }
    ```

-   Перед завершением реализации класса страницы приветствия необходимо создать в файле MicrosoftPassportHelper.cs метод, который позволит удалять устройства. Создайте новый метод для вызова PassportRemoveDevice в AuthService.

    ```cs
    public static void RemovePassportDevice(UserAccount account, Guid deviceId)
    {
        AuthService.AuthService.Instance.PassportRemoveDevice(account.UserId, deviceId);
    }
    ```

-   В Welcome.xaml.cs реализуйте событие нажатия кнопки "Забыть устройство". Будет использоваться выбранное устройство из списка устройств и класс MicrosoftPassportHelper для вызова удаления устройства.

    ```cs
    private void Button_Forget_Device_Click(object sender, RoutedEventArgs e)
    {
        PassportDevice selectedDevice = UserListView.SelectedItem as PassportDevice;
        if (selectedDevice != null)
        {
            //Remove it from Windows Hello
            MicrosoftPassportHelper.RemovePassportDevice(_activeAccount, selectedDevice.DeviceId);

            Debug.WriteLine("User " + _activeAccount.Username + " deleted.");

            if (!UserListView.Items.Any())
            {
                //Navigate back to UserSelection page.
                Frame.Navigate(typeof(UserSelection));
            }
        }
        else
        {
            ForgetDeviceErrorTextBlock.Visibility = Visibility.Visible;
        }
    }
    ```

-   Далее отредактируйте страницу UserSelection. Страница UserSelection должна будет использовать AuthService для получения учетных записей всех пользователей для текущего устройства. В данный момент вы не можете получить идентификатор устройства, передать его в AuthService и получить учетные записи пользователей для этого устройства. Создайте в папке Utils новый класс под названием "Helpers.cs". Измените определение класса на общий статический (public static), а затем добавьте следующий метод, который позволит получить идентификатор текущего устройства.

    ```cs
    using Windows.Security.ExchangeActiveSyncProvisioning;

    namespace PassportLogin.Utils
    {
        public static class Helpers
        {
            public static Guid GetDeviceId()
            {
                //Get the Device ID to pass to the server
                EasClientDeviceInformation deviceInformation = new EasClientDeviceInformation();
                return deviceInformation.Id;
            }
        }
    }
    ```

-   В классе страницы UserSelection необходимо изменить только код программной части, но не пользовательский интерфейс. В файле UserSelection.xaml.cs отредактируйте метод loaded и метод выбора пользователя для использования класса UserAccount вместо класса Account. Вам также потребуется получить всех пользователей для этого устройства посредством AuthService.

    ```cs
    using System.Linq;
    using PassportLogin.AuthService;

    namespace PassportLogin.Views
    {
        public sealed partial class UserSelection : Page
        {
            public UserSelection()
            {
                InitializeComponent();
                Loaded += UserSelection_Loaded;
            }

            private void UserSelection_Loaded(object sender, RoutedEventArgs e)
            {
                List<UserAccount> accounts = AuthService.AuthService.Instance.GetUserAccountsForDevice(Helpers.GetDeviceId());

                if (accounts.Any())
                {
                    UserListView.ItemsSource = accounts;
                    UserListView.SelectionChanged += UserSelectionChanged;
                }
                else
                {
                    //If there are no accounts navigate to the LoginPage
                    Frame.Navigate(typeof(Login));
                }
            }

            /// <summary>
            /// Function called when an account is selected in the list of accounts
            /// Navigates to the Login page and passes the chosen account
            /// </summary>
            private void UserSelectionChanged(object sender, RoutedEventArgs e)
            {
                if (((ListView)sender).SelectedValue != null)
                {
                    UserAccount account = (UserAccount)((ListView)sender).SelectedValue;
                    if (account != null)
                    {
                        Debug.WriteLine("Account " + account.Username + " selected!");
                    }
                    Frame.Navigate(typeof(Login), account);
                }
            }
        }
    }
    ```

-   Для страницы PassportRegister необходимо изменить код программной части, пользовательский интерфейс менять не нужно. В файле PassportRegister.xaml.cs в верхней части класса удалите частную переменную Account, которая больше не требуется. Обновите событие нажатия кнопки RegisterButton для использования AuthService. Этот метод создает новую учетную запись, а затем пытается обновить ее сведения Passport. Если Microsoft Passport не удается создать ключ, учетная запись удаляется по причине сбоя процесса регистрации.

    ```cs
    private async void RegisterButton_Click_Async(object sender, RoutedEventArgs e)
    {
        ErrorMessage.Text = "";

        //Validate entered credentials are acceptable
        if (!string.IsNullOrEmpty(UsernameTextBox.Text))
        {
            //Register an Account on the AuthService so that we can get back a userId
            AuthService.AuthService.Instance.Register(UsernameTextBox.Text);
            Guid userId = AuthService.AuthService.Instance.GetUserId(UsernameTextBox.Text);

            if (userId != Guid.Empty)
            {
                //Now that the account exists on server try and create the necessary passport details and add them to the account
                bool isSuccessful = await MicrosoftPassportHelper.CreatePassportKeyAsync(userId, UsernameTextBox.Text);
                if (isSuccessful)
                {
                    //Navigate to the Welcome Screen. 
                    Frame.Navigate(typeof(Welcome), AuthService.AuthService.Instance.GetUserAccount(userId));
                }
                else
                {
                    //The passport account creation failed.
                    //Remove the account from the server as passport details were not configured
                    AuthService.AuthService.Instance.PassportRemoveUser(userId);

                    ErrorMessage.Text = "Account Creation Failed";
                }
            }
        }
        else
        {
            ErrorMessage.Text = "Please enter a username";
        }
    }
    ```

-   Выполните сборку и запустите приложение (F5). Войдите в тестовую учетную запись, используя учетные данные "sampleUsername " и "samplePassword". Вы можете заметить, что на экране приветствия отображается кнопка "Забыть устройства", но устройства отсутствуют. При создании или переносе пользователя для работы с Windows Hello сведения паспорта не отправляются в AuthService.

    ![Экран входа Windows Hello](images/passport-auth-3.png)

    ![Успешный вход Windows Hello](images/passport-auth-4.png)

-   Для передачи сведений паспорта в AuthService необходимо обновить файл MicrosoftPassportHelper.cs. Метод CreatePassportKeyAsync должен не только возвращать значение true в случае успеха, но и вызывать новый метод, который попытается получить KeyAttestation. Несмотря на то, что в этом практическом занятии сведения в AuthService не записываются, вы узнаете, как получить их на стороне клиента. Обновите метод CreatePassportKeyAsync.

    ```cs
    public static async Task<bool> CreatePassportKeyAsync(Guid userId, string username)
    {
        KeyCredentialRetrievalResult keyCreationResult = await KeyCredentialManager.RequestCreateAsync(username, KeyCredentialCreationOption.ReplaceExisting);

        switch (keyCreationResult.Status)
        {
            case KeyCredentialStatus.Success:
                Debug.WriteLine("Successfully made key");
                await GetKeyAttestationAsync(userId, keyCreationResult);
                return true;
            case KeyCredentialStatus.UserCanceled:
                Debug.WriteLine("User cancelled sign-in process.");
                break;
            case KeyCredentialStatus.NotFound:
                // User needs to setup Windows Hello
                Debug.WriteLine("Windows Hello is not setup!\nPlease go to Windows Settings and set up a PIN to use it.");
                break;
            default:
                break;
        }

        return false;
    }
    ```

-   Создайте метод GetKeyAttestationAsync в классе MicrosoftPassportHelper.cs. В данном методе показано, как получить необходимые сведения, предоставляемые Windows Hello для каждой учетной записи на конкретном устройстве.

    ```cs
    using Windows.Storage.Streams;

    private static async Task GetKeyAttestationAsync(Guid userId, KeyCredentialRetrievalResult keyCreationResult)
    {
        KeyCredential userKey = keyCreationResult.Credential;
        IBuffer publicKey = userKey.RetrievePublicKey();
        KeyCredentialAttestationResult keyAttestationResult = await userKey.GetAttestationAsync();
        IBuffer keyAttestation = null;
        IBuffer certificateChain = null;
        bool keyAttestationIncluded = false;
        bool keyAttestationCanBeRetrievedLater = false;
        KeyCredentialAttestationStatus keyAttestationRetryType = 0;

        if (keyAttestationResult.Status == KeyCredentialAttestationStatus.Success)
        {
            keyAttestationIncluded = true;
            keyAttestation = keyAttestationResult.AttestationBuffer;
            certificateChain = keyAttestationResult.CertificateChainBuffer;
            Debug.WriteLine("Successfully made key and attestation");
        }
        else if (keyAttestationResult.Status == KeyCredentialAttestationStatus.TemporaryFailure)
        {
            keyAttestationRetryType = KeyCredentialAttestationStatus.TemporaryFailure;
            keyAttestationCanBeRetrievedLater = true;
            Debug.WriteLine("Successfully made key but not attestation");
        }
        else if (keyAttestationResult.Status == KeyCredentialAttestationStatus.NotSupported)
        {
            keyAttestationRetryType = KeyCredentialAttestationStatus.NotSupported;
            keyAttestationCanBeRetrievedLater = false;
            Debug.WriteLine("Key created, but key attestation not supported");
        }

        Guid deviceId = Helpers.GetDeviceId();
        //Update the Pasport details with the information we have just gotten above.
        //UpdatePassportDetails(userId, deviceId, publicKey.ToArray(), keyAttestationResult);
    }
    ```

-   Вы, возможно, заметили, что в только что добавленном методе GetKeyAttestationAsync закоментирована последняя строка. Эта последняя строка станет новым методом, который вы создадите и который будет отправлять всю информацию Windows Hello в AuthService. В реальных сценариях данные должны отправляться на сервер с помощью веб-API.

    ```cs
    using System.Runtime.InteropServices.WindowsRuntime;

    public static bool UpdatePassportDetails(Guid userId, Guid deviceId, byte[] publicKey, KeyCredentialAttestationResult keyAttestationResult)
    {
        //In the real world you would use an API to add Passport signing info to server for the signed in _account.
        //For this tutorial we do not implement a WebAPI for our server and simply mock the server locally 
        //The CreatePassportKey method handles adding the Windows Hello account locally to the device using the KeyCredential Manager

        //Using the userId the existing account should be found and updated.
        AuthService.AuthService.Instance.PassportUpdateDetails(userId, deviceId, publicKey, keyAttestationResult);
        return true;
    }
    ```

-   Удалите комментарий для последней строки метода GetKeyAttestationAsync, чтобы сведения Windows Hello отправлялись в AuthService.
-   Выполните сборку, запустите приложение и войдите, используя учетные данные по умолчанию, как и раньше. Теперь на экране приветствия отображается идентификатор устройства. Если вы войдете в систему на другом устройстве, оно также появится в списке (при использовании облачной службы проверки подлинности). В этом практическом занятии отображается фактический идентификатор устройства. В реальном сценарии желательно использовать понятное имя, которое пользователь сможет использовать для определения каждого устройства.

    ![Идентификатор устройства, где успешно выполнен вход Windows Hello](images/passport-auth-5.png)

-   21. В завершение этого практического занятия необходимо запросить задачу для пользователя, если он осуществляет повторный вход со страницы выбора пользователя. В AuthService есть два созданных вами метода для запроса задачи, один из которых использует подписанную задачу. Создайте в MicrosoftPassportHelper.cs новый метод "RequestSignAsync". Он будет запрашивать задачу в AuthService, локально подписывать ее с помощью API Passport и отправлять подписанную задачу в AuthService. В этом практическом занятии AuthService получает подписанную задачу и возвращает значение true. В реальных сценариях необходимо реализовать механизм проверки, чтобы определить, что задача была подписана правильным пользователем на правильном устройстве. Добавьте следующий метод в файл MicrosoftPassportHelper.cs.

    ```cs
    private static async Task<bool> RequestSignAsync(Guid userId, KeyCredentialRetrievalResult openKeyResult)
    {
        // Calling userKey.RequestSignAsync() prompts the uses to enter the PIN or use Biometrics (Windows Hello).
        // The app would use the private key from the user account to sign the sign-in request (challenge)
        // The client would then send it back to the server and await the servers response.
        IBuffer challengeMessage = AuthService.AuthService.Instance.PassportRequestChallenge();
        KeyCredential userKey = openKeyResult.Credential;
        KeyCredentialOperationResult signResult = await userKey.RequestSignAsync(challengeMessage);

        if (signResult.Status == KeyCredentialStatus.Success)
        {
            // If the challenge from the server is signed successfully
            // send the signed challenge back to the server and await the servers response
            return AuthService.AuthService.Instance.SendServerSignedChallenge(
                userId, Helpers.GetDeviceId(), signResult.Result.ToArray());
        }
        else if (signResult.Status == KeyCredentialStatus.UserCanceled)
        {
            // User cancelled the Windows Hello PIN entry.
        }
        else if (signResult.Status == KeyCredentialStatus.NotFound)
        {
            // Must recreate Windows Hello key
        }
        else if (signResult.Status == KeyCredentialStatus.SecurityDeviceLocked)
        {
            // Can't use Windows Hello right now, remember that hardware failed and suggest restart
        }
        else if (signResult.Status == KeyCredentialStatus.UnknownError)
        {
            // Can't use Windows Hello right now, try again later
        }

        return false;
    }
    ```

-   22. В классе MicrosoftPassportHelper вызовите метод RequestSignAsync из метода GetPassportAuthenticationMessageAsync.

    ```cs
    public static async Task<bool> GetPassportAuthenticationMessageAsync(UserAccount account)
    {
        KeyCredentialRetrievalResult openKeyResult = await KeyCredentialManager.OpenAsync(account.Username);
        // Calling OpenAsync will allow the user access to what is available in the app and will not require user credentials again.
        // If you wanted to force the user to sign in again you can use the following:
        // var consentResult = await Windows.Security.Credentials.UI.UserConsentVerifier.RequestVerificationAsync(account.Username);
        // This will ask for the either the password of the currently signed in Microsoft Account or the PIN used for Windows Hello.

        if (openKeyResult.Status == KeyCredentialStatus.Success)
        {
            //If OpenAsync has succeeded, the next thing to think about is whether the client application requires access to backend services.
            //If it does here you would Request a challenge from the Server. The client would sign this challenge and the server
            //would check the signed challenge. If it is correct it would allow the user access to the backend.
            //You would likely make a new method called RequestSignAsync to handle all this
            //e.g. RequestSignAsync(openKeyResult);
            //Refer to the second Windows Hello sample for information on how to do this.

            return await RequestSignAsync(account.UserId, openKeyResult);
        }
        else if (openKeyResult.Status == KeyCredentialStatus.NotFound)
        {
            //If the _account is not found at this stage. It could be one of two errors. 
            //1. Windows Hello has been disabled
            //2. Windows Hello has been disabled and re-enabled cause the Windows Hello Key to change.
            //Calling CreatePassportKey and passing through the account will attempt to replace the existing Windows Hello Key for that account.
            //If the error really is that Windows Hello is disabled then the CreatePassportKey method will output that error.
            if (await CreatePassportKeyAsync(account.UserId, account.Username))
            {
                //If the Passport Key was again successfully created, Windows Hello has just been reset.
                //Now that the Passport Key has been reset for the _account retry sign in.
                return await GetPassportAuthenticationMessageAsync(account);
            }
        }

        // Can't use Windows Hello right now, try again later
        return false;
    }
    ```

-   В данном упражнении вы обновили клиентское приложение для использования AuthService. Таким образом вам удалось исключить необходимость в классах Account и AccountHelper. Удалите класс Account, папку Models и класс AccountHelper в папке Utils. Чтобы успешно выполнить сборку приложения, необходимо удалить все ссылки на пространство имен Models.
-   Выполните сборку и запустите приложение. Теперь вы можете использовать Windows Hello c макетной службой и базой данных.

На это практическом занятии вы научились использовать API Windows Hello вместо пароля при проверке подлинности на компьютере под управлением Windows 10. Если учесть количество усилий, прилагаемых для хранения паролей и восстановления потерянных паролей, становится понятным преимущество перехода на новую систему проверки подлинности с помощью Windows Hello.

Мы оставили аспекты реализации проверки подлинности на стороне службы и сервера на ваше усмотрение в качестве упражнения. Ожидается, что у большинства из вас уже есть системы, которые необходимо перенести, чтобы начать работу с Windows Hello, и у каждой из них есть свои особенности.

## <a name="related-topics"></a>Еще по теме

* [Windows Hello](microsoft-passport.md)
* [Приложение для входа с использованием Windows Hello](microsoft-passport-login.md)