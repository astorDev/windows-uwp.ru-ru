---
title: Совместное использование сертификатов приложениями
description: Приложения универсальной платформы Windows (UWP), требующие безопасной проверки подлинности помимо идентификатора пользователя и пароля, могут использовать для проверки подлинности сертификаты.
ms.assetid: 159BA284-9FD4-441A-BB45-A00E36A386F9
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, uwp, безопасность
ms.localizationpriority: medium
ms.openlocfilehash: 1caa7361011b535a0dd63da53e0aba2eadff72be
ms.sourcegitcommit: b5c9c18e70625ab770946b8243f3465ee1013184
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "7989908"
---
# <a name="share-certificates-between-apps"></a>Совместное использование сертификатов приложениями




Приложения универсальной платформы Windows (UWP), требующие безопасной проверки подлинности помимо идентификатора пользователя и пароля, могут использовать для проверки подлинности сертификаты. Проверка подлинности с помощью сертификата обеспечивает высокий уровень доверия при проверке подлинности пользователя. В некоторых случаях группе служб может понадобиться проверить подлинность пользователя для нескольких приложений. В этой статье показано, как можно проверить несколько приложений с помощью одного сертификата и как предоставить пользователю удобный код, чтобы он мог импортировать сертификат, необходимый для доступа к защищенным веб-службам.

Приложения могут выполнять проверку подлинности для веб-службы с помощью сертификата, причем несколько приложений могут использовать один и тот же сертификат из хранилища сертификатов для проверки подлинности одного и того же пользователя. Если сертификат в хранилище отсутствует, вы можете добавить код в приложение, чтобы импортировать сертификат из PFX-файла.

## <a name="enable-microsoft-internet-information-services-iis-and-client-certificate-mapping"></a>Включение служб Microsoft IIS сопоставление сертификатов клиентов


В данной статье службы Microsoft IIS используются в качестве примера. Служба IIS не включена по умолчанию. IIS можно включить с помощью панели управления.

1.  Откройте панель управления и выберите **Программы**.
2.  Выберите **Включение или отключение компонентов Windows**.
3.  Разверните разделы **Службы IIS** и **Службы Интернета**. Разверните **Компоненты для разработки приложений** и выберите **ASP.NET 3.5** и **ASP.NET 4.5**. При этом автоматически включатся **службы IIS**.
4.  Нажмите **ОК**, чтобы применить изменения.

## <a name="create-and-publish-a-secured-web-service"></a>Создание и публикация защищенной веб-службы


1.  Запустите Microsoft Visual Studio от имени администратора и выберите **Создать проект** на начальной странице. Для публикации веб-службы на сервере IIS требуется доступ с правами администратора. В диалоговом окне "Создание проекта" измените платформу на **.NET Framework 3.5**. Выберите **Visual C#** -&gt; **Web** -&gt; **Visual Studio** -&gt; **Приложение веб-службы ASP.NET**. Назовите приложение FirstContosoBank. Чтобы создать проект, нажмите кнопку **OК**.
2.  В файле **Service1.asmx.cs** замените веб-метод по умолчанию **HelloWorld** следующим методом Login.
    ```cs
            [WebMethod]
            public string Login()
            {
                // Verify certificate with CA
                var cert = new System.Security.Cryptography.X509Certificates.X509Certificate2(
                    this.Context.Request.ClientCertificate.Certificate);
                bool test = cert.Verify();
                return test.ToString();
            }
    ```

3.  Сохраните файл **Service1.asmx.cs**.
4.  В **обозревателе решений** щелкните правой кнопкой мыши приложение FirstContosoBank и выберите **Опубликовать**.
5.  В диалоговом окне **Публикации веб-сайта** создайте новый профиль и назовите его ContosoProfile. Нажмите кнопку **Далее**
6.  На следующей странице введите имя вашего сервера IIS и укажите имя сайта "Веб-сайт по умолчанию"/FirstContosoBank. Нажмите **Опубликовать** , чтобы опубликовать свою веб-службу.

## <a name="configure-your-web-service-to-use-client-certificate-authentication"></a>Настройка веб-службы на использование проверки подлинности сертификата клиента


1.  Запустите **диспетчер служб IIS**.
2.  Разверните сайты вашего сервера IIS. В разделе **Веб-сайт по умолчанию** выберите новую веб-службу FirstContosoBank. В разделе **Действия** выберите **Дополнительные параметры**.
3.  Установите для параметра **Пул приложений** значение **.NET v2.0** и нажмите **ОК**.
4.  В **диспетчере служб IIS** выберите свой сервер IIS и дважды щелкните **Сертификаты сервера**. В разделе **Действия** выберите **Создать самозаверяющий сертификат...**. Введите ContosoBank в качестве понятного имени сертификата и нажмите кнопку **ОК**. При этом будет создан новый сертификат, используемый сервером IIS в форме &lt;имя-сервера&gt;.&lt;имя-домена&gt;.
5.  В **диспетчере служб IIS** выберите веб-сайт по умолчанию. В разделе **Действия** выберите **Привязка**, а затем нажмите **Добавить...**. Выберите https в качестве типа, задайте порт 443 и введите полное имя узла вашего сервера IIS (&lt;имя_сервера&gt;.&lt;имя_домена&gt;). Установите для SSL-сертификата значение ContosoBank. Нажмите кнопку **ОК**. Нажмите кнопку **Закрыть** в окне **Привязки сайта**.
6.  В **диспетчере служб IIS** выберите веб-службу FirstContosoBank. Дважды щелкните **Параметры SSL**. Установите флажок **Требовать SSL**. В разделе **Сертификаты клиента** выберите **Требовать**. В разделе **Действия** нажмите кнопку **Применить**.
7.  Вы можете проверить правильность настройки веб-службы, открыв браузер и введя следующий веб-адрес: "https://&lt;имя-сервера&gt;.&lt;имя-домена&gt;/FirstContosoBank/Service1.asmx". Например: https://myserver.example.com/FirstContosoBank/Service1.asmx. Если ваша веб-служба настроена правильно, вы получите предложение выбрать сертификат клиента, чтобы получить доступ к веб-службе.

Вы можете повторить предыдущие шаги, чтобы создать несколько веб-служб, доступ к которым можно получить с помощью одного и того же сертификата клиента.

## <a name="create-a-uwp-app-that-uses-certificate-authentication"></a>Создание приложения UWP, использующего проверку подлинности сертификата


Теперь, когда у вас есть одна или несколько защищенных веб-служб, ваши приложения могут использовать сертификаты для проверки подлинности в этих веб-службах. Когда вы отправляете запрос в веб-службу с проверкой подлинности с помощью объекта [**HttpClient**](https://msdn.microsoft.com/library/windows/apps/dn298639), начальный запрос не будет содержать сертификат клиента. Веб-служба с проверкой подлинности ответит запросом на проверку подлинности клиента. При этом клиент Windows автоматически запросит хранилище сертификатов на наличие доступных сертификатов клиента. Пользователь может выбрать из этих сертификатов нужный, чтобы пройти проверку подлинности в веб-службе. Некоторые сертификаты защищены паролем, поэтому следует предоставить пользователю возможность ввести пароль для сертификата.

Если доступных сертификатов клиента нет, пользователю понадобится добавить сертификат в хранилище сертификатов. Вы можете включить в свое приложение код, который позволяет пользователю выбрать PFX-файл, содержащий сертификат клиента, и импортировать этот сертификат в хранилище сертификатов клиента.

**Совет**makecert.exe можно использовать для создания PFX-файл для использования с помощью этого краткого руководства. Подробнее об использовании makecert.exe см. в разделе [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968).

 

1.  Откройте Visual Studio и создайте новый проект на начальной странице. Назовите новый проект FirstContosoBankApp. Чтобы создать проект, нажмите **ОК** .
2.  В файле MainPage.xaml добавьте следующий XAML-код в элемент **Grid** по умолчанию. Этот XAML-код включает кнопку для перехода к импортируемому PFX-файлу, текстовое поле для ввода пароля в случае защищенного паролем PFX-файла, кнопку для импорта выбранного PFX-файла, кнопку входа в защищенную веб-службу и текстовый блок для отображения статуса текущего действия.
    ```xml
    <Button x:Name="Import" Content="Import Certificate (PFX file)" HorizontalAlignment="Left" Margin="352,305,0,0" VerticalAlignment="Top" Height="77" Width="260" Click="Import_Click" FontSize="16"/>
    <Button x:Name="Login" Content="Login" HorizontalAlignment="Left" Margin="611,305,0,0" VerticalAlignment="Top" Height="75" Width="240" Click="Login_Click" FontSize="16"/>
    <TextBlock x:Name="Result" HorizontalAlignment="Left" Margin="355,398,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Height="153" Width="560"/>
    <PasswordBox x:Name="PfxPassword" HorizontalAlignment="Left" Margin="483,271,0,0" VerticalAlignment="Top" Width="229"/>
    <TextBlock HorizontalAlignment="Left" Margin="355,271,0,0" TextWrapping="Wrap" Text="PFX password" VerticalAlignment="Top" FontSize="18" Height="32" Width="123"/>
    <Button x:Name="Browse" Content="Browse for PFX file" HorizontalAlignment="Left" Margin="352,189,0,0" VerticalAlignment="Top" Click="Browse_Click" Width="499" Height="68" FontSize="16"/>
    <TextBlock HorizontalAlignment="Left" Margin="717,271,0,0" TextWrapping="Wrap" Text="(Optional)" VerticalAlignment="Top" Height="32" Width="83" FontSize="16"/>
    ```
    
3.  Сохраните файл MainPage.xaml.
4.  В файл MainPage.xaml.cs добавьте следующие операторы using.
    ```cs
    using Windows.Web.Http;
    using System.Text;
    using Windows.Security.Cryptography.Certificates;
    using Windows.Storage.Pickers;
    using Windows.Storage;
    using Windows.Storage.Streams;
    ```

5.  В файл MainPage.xaml.cs добавьте следующие переменные в класс **MainPage**. Они указывают адрес защищенного метода Login вашей веб-службы FirstContosoBank и глобальную переменную, содержащую сертификат PFX для импорта в хранилище сертификатов. Обновите &lt;имя-сервера&gt; до полного имени сервера Microsoft Internet Information Server (IIS).
    ```cs
    private Uri requestUri = new Uri("https://<server-name>/FirstContosoBank/Service1.asmx?op=Login");
    private string pfxCert = null;
    ```

6.  В файл MainPage.xaml.cs добавьте следующий обработчик щелчка для кнопки входа и метод доступа к защищенной веб-службе.
    ```cs
    private void Login_Click(object sender, RoutedEventArgs e)
    {
        MakeHttpsCall();
    }

    private async void MakeHttpsCall()
    {

        StringBuilder result = new StringBuilder("Login ");
        HttpResponseMessage response;
        try
        {
            Windows.Web.Http.HttpClient httpClient = new Windows.Web.Http.HttpClient();
            response = await httpClient.GetAsync(requestUri);
            if (response.StatusCode == HttpStatusCode.Ok)
            {
                result.Append("successful");
            }
            else
            {
                result = result.Append("failed with ");
                result = result.Append(response.StatusCode);
            }
        }
        catch (Exception ex)
        {
            result = result.Append("failed with ");
            result = result.Append(ex.Message);
        }

        Result.Text = result.ToString();
    }
    ```

7.  В файл MainPage.xaml.cs добавьте следующие обработчики щелчка для кнопки перехода к PFX-файлу и кнопки импорта выбранного PFX-файла в хранилище сертификатов.
    ```cs
    private async void Import_Click(object sender, RoutedEventArgs e)
    {
        try
        {
            Result.Text = "Importing selected certificate into user certificate store....";
            await CertificateEnrollmentManager.UserCertificateEnrollmentManager.ImportPfxDataAsync(
                pfxCert,
                PfxPassword.Password,
                ExportOption.Exportable,
                KeyProtectionLevel.NoConsent,
                InstallOptions.DeleteExpired,
                "Import Pfx");

            Result.Text = "Certificate import succeded";
        }
        catch (Exception ex)
        {
            Result.Text = "Certificate import failed with " + ex.Message;
        }
    }

    private async void Browse_Click(object sender, RoutedEventArgs e)
    {

        StringBuilder result = new StringBuilder("Pfx file selection ");
        FileOpenPicker pfxFilePicker = new FileOpenPicker();
        pfxFilePicker.FileTypeFilter.Add(".pfx");
        pfxFilePicker.CommitButtonText = "Open";
        try
        {
            StorageFile pfxFile = await pfxFilePicker.PickSingleFileAsync();
            if (pfxFile != null)
            {
                IBuffer buffer = await FileIO.ReadBufferAsync(pfxFile);
                using (DataReader dataReader = DataReader.FromBuffer(buffer))
                {
                    byte[] bytes = new byte[buffer.Length];
                    dataReader.ReadBytes(bytes);
                    pfxCert = System.Convert.ToBase64String(bytes);
                    PfxPassword.Password = string.Empty;
                    result.Append("succeeded");
                }
            }
            else
            {
                result.Append("failed");
            }
        }
        catch (Exception ex)
        {
            result.Append("failed with ");
            result.Append(ex.Message); ;
        }

        Result.Text = result.ToString();
    }
    ```

8.  Запустите приложение и войдите в свою защищенную веб-службу, а затем импортируйте PFX-файл в локальное хранилище сертификатов.

Выполняя эти шаги, вы можете создать несколько приложений, которые используют один и тот же сертификат пользователя для доступа к одной и той же или разным защищенным веб-службам.