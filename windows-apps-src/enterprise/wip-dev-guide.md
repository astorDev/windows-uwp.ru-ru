---
author: normesta
Description: "Это руководство поможет обучить свое приложение тому, как следует обрабатывать корпоративные данные, попадающие под действие политики Windows Information Protection (WIP), а также персональные данные."
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: "Создание грамотного приложения, принимающего и корпоративные, и персональные данные"
translationtype: Human Translation
ms.sourcegitcommit: 0da731e1211544ce6b07e783ddc2407da57781c2
ms.openlocfilehash: 8ead30471371b9b6aca32088f115da9f68784922

---

# Создание грамотного приложения, принимающего и корпоративные, и персональные данные

__Примечание.__ Политика Windows Information Protection (WIP) может применяться в Windows 10 версии 1607.

*Грамотное* приложение различает корпоративные и персональные данные и знает, какие из них следует защищать в соответствии с политиками Windows Information Protection (WIP), определенными администратором.

В этом руководстве мы покажем вам, как создать такое приложение. После этого администраторы политик смогут доверять вашему приложению в вопросе использования данных их организации. А сотрудникам понравится, что вы храните их персональные данные в целости и сохранности на устройстве, даже если они откажутся от участия в системе управления мобильными устройствами (MDM) или совсем уйдут из компании.

Более подробные сведения о WIP и грамотных приложениях можно найти здесь: [Windows Information Protection (WIP)](wip-hub.md).

Полный пример можно найти [здесь](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/EnterpriseDataProtection).

Если вы готовы пройти все задачи, приступим.

## Во-первых, соберите все, что вам понадобится

Необходимые ресурсы указаны ниже.

* Доступ к учетной записи Microsoft Intune.

* Компьютер для разработки под управлением Windows 10 версии 1607.

* Тестовое устройство под управлением Windows 10 версии 1607. Отладка приложения будет производиться на этом тестовом устройстве.

  Отладка на том же устройстве, зарегистрированном в MDM, невозможна. Именно поэтому нужно отдельное тестовое устройство.

  Чтобы не усложнять, предположим, что вашим тестовым устройством является компьютер или виртуальная машина.

## Настройка вашей среды разработки

Необходимо выполнить следующие действия.

* Зарегистрируйте свой тестовый компьютер.

* Создайте политику защиты.

* Скачайте политику на тестовый компьютер.

* Настройте проект Visual Studio.

* Настройте удаленную отладку.

* Добавьте пространства имен в файлы кода

**Регистрация тестового компьютера**

 Для регистрации тестового компьютера необходимо добавить вашу учетную запись Intune на странице **Параметры**->**Доступ на рабочем месте или в учебном учреждении** на тестовом компьютере.

 ![Подключение к MDM](images/connect-v2.png)

 Затем имя вашего компьютера появится в консоли администратора Intune.

**Создание политики защиты**

Создайте политику и разверните ее на тестовом компьютере. См. [Создание политики Windows Information Protection (WIP) с помощью Microsoft Intune](https://technet.microsoft.com/itpro/windows/keep-secure/create-edp-policy-using-intune).

**Загрузка политики на устройство**

На тестовом компьютере откройте страницу **Параметры**, затем выберите **Доступ на рабочем месте или в учебном учреждении**-> **Информация**->**Синхронизация**.

![Синхронизируйте параметры с MDM](images/sync.png)

**Настройка проекта Visual Studio**

1. На компьютере разработки откройте свой проект.

2. Добавьте ссылку на настольные и мобильные надстройки для универсальной платформы Windows (UWP).

    ![Добавьте расширения UWP](images/extensions.png)

3. Добавьте следующие возможности в файл манифеста пакета.

    ```xml
       <Capability Name="privateNetworkClientServer" />
       <rescap:Capability Name="enterpriseDataPolicy"/>
    ```
   >*Optional Reading*: префикс "rescap" означает *ограниченная возможность*. См. [Особые и ограниченные возможности](https://msdn.microsoft.com/windows/uwp/packaging/app-capability-declarations).

4. Добавьте это пространство имен в файл манифеста пакета:

    ```xml
      xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
    ```
5. Добавьте префикс пространства имен в элемент ``<ignorableNamespaces>`` файла манифеста пакета.

    ```xml
        <IgnorableNamespaces="uap mp rescap">
    ```

    Таким образом, если ваше приложение будет выполняться на версии операционной системы Windows, не поддерживающей ограниченные возможности, Windows будет игнорировать возможность ``enterpriseDataPolicy``.

**Настройка удаленной отладки**

Установите инструменты удаленной отладки Visual Studio на тестовый компьютер. Затем запустите на компьютере разработки удаленный отладчик и проверьте, выполняется ли ваше приложение на целевом компьютере.

См. [Инструкции для удаленных компьютеров](https://msdn.microsoft.com/windows/uwp/debug-test-perf/deploying-and-debugging-uwp-apps#remote-pc-instructions).

**Добавление пространств имен в файлы кода**

Добавьте эти операторы using в верхней части файлов кода (они используются в фрагментах кода в этом руководстве):

```csharp
using System.Threading.Tasks;
using Windows.Security.EnterpriseData;
using Windows.ApplicationModel.DataTransfer;
using Windows.Web.Http;
using Windows.Storage.Streams;
using Windows.Web.Http.Filters;
using Windows.Storage;
using Windows.Foundation.Metadata;
using Windows.UI.Xaml.Controls;
using Windows.Data.Xml.Dom;
```

## Определение того, поддерживает ли WIP та операционная система, в которой выполняется ваше приложение

Для этого используйте функцию [**IsApiContractPresent**](https://msdn.microsoft.com/en-us/library/windows/apps/windows.foundation.metadata.apiinformation.isapicontractpresent.aspx).

```csharp
bool wipSupported = ApiInformation.IsApiContractPresent("Windows.Security.EnterpriseData.EnterpriseDataContract", 3);

if (wipSupported)
{
    // WIP is supported on the platform
}
else
{
    // WIP is not supported on the platform
}
```

Windows Information Protection теперь поддерживается в Windows 10 версии 1607.

## Чтение корпоративных данных

Файл, конечные точки сети, данные буфера обмена и данные, которые вы принимаете по контракту отправки данных, — все они имеют корпоративный идентификатор.

Для чтения данных изо всех этих источников ваше приложении должно будет убедиться, что корпоративный идентификатор управляется политикой.

Начнем с файлов.

### Чтение данных из файла

**Шаг 1. Получите дескриптор файла**

```csharp
    Windows.Storage.StorageFolder storageFolder =
        Windows.Storage.ApplicationData.Current.LocalFolder;

    Windows.Storage.StorageFile file =
        await storageFolder.GetFileAsync(fileName);
```

**Шаг 2. Определите, может ли приложение открыть файл**

Определите, защищен ли файл. Если да, ваше приложение сможет открыть этот файл, если выполняются следующие два условия.

* Удостоверение файла управляется политикой.
* Ваше приложение включено в список разрешенных для этой политики.

Если какое-либо из этих условий не выполняется, [**ProtectionPolicyManager.IsIdentityManaged**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.isidentitymanaged.aspx) возвращает **false**, и открыть файл вам не удастся.

```csharp
FileProtectionInfo protectionInfo = await FileProtectionManager.GetProtectionInfoAsync(file);

if (protectionInfo.Status == FileProtectionStatus.Protected)
{
    if (!ProtectionPolicyManager.IsIdentityManaged(protectionInfo.Identity))
    {
        return false;
    }
}
else if (protectionInfo.Status == FileProtectionStatus.Revoked)
{
    // Code goes here to handle this situation. Perhaps, show UI
    // saying that the user's data has been revoked.
}
```
> **Интерфейсы API** <br>
[FileProtectionManager.GetProtectionInfoAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionmanager.getprotectioninfoasync.aspx)<br>
[FileProtectionInfo](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectioninfo.aspx)<br>
[FileProtectionStatus](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionstatus.aspx)<br>
[ProtectionPolicyManager.IsIdentityManaged](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.isidentitymanaged.aspx)

**Шаг 3. Выполните чтение файла в поток или буфер**

*Чтение файла в поток*

```csharp
var stream = await file.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);
```

*Чтение файла в буфер*

```csharp
var buffer = await Windows.Storage.FileIO.ReadBufferAsync(file);
```

### Чтение данных из конечной точки сети

Создайте защищенный контекст потока для чтения из корпоративной конечной точки.

**Шаг 1. Получите идентификатор конечной точки сети**

```csharp
Uri resourceURI = new Uri("http://contoso.com/stockData.xml");

Windows.Networking.HostName hostName =
    new Windows.Networking.HostName(resourceURI.Host);

string identity = await ProtectionPolicyManager.
    GetPrimaryManagedIdentityForNetworkEndpointAsync(hostName);
```

Если конечная точка не управляется политикой, вам возвращается пустая строка.

> **Интерфейсы API** <br>
[ProtectionPolicyManager.GetPrimaryManagedIdentityForNetworkEndpointAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getprimarymanagedidentityfornetworkendpointasync.aspx)


**Шаг 2. Создайте защищенный контекст потока**

Если конечная точка управляется политикой, создайте защищенный контекст потока. Это присвоит метки всем сетевым подключения, которые вы устанавливаете в одном потоке с идентификатором.

Это также дает вам доступ к ресурсам корпоративной сети, которые управляются данной политикой.

```csharp
HttpClient client = null;

if (!string.IsNullOrEmpty(identity))
{
    using (ThreadNetworkContext threadNetworkContext =
            ProtectionPolicyManager.CreateCurrentThreadNetworkContext(identity))
    {
        client = new HttpClient();

        // Add code here to get data from the endpoint.
    }
}
```
Этот пример содержит вызовы сокетов в блоке ``using``. Если вы этого не сделаете, убедитесь, что закрыли контекст потока после получения ресурса. См. [ThreadNetworkContext.Close](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.threadnetworkcontext.close.aspx).

Не создавайте никаких личных файлов в этом защищенном потоке, поскольку эти файлы будут автоматически зашифрованы.

Метод [**ProtectionPolicyManager.CreateCurrentThreadNetworkContext**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.createcurrentthreadnetworkcontext.aspx) возвращает объект [**ThreadNetworkContext**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.threadnetworkcontext.aspx) независимо от того, управляется ли конечная точка политикой. Если ваше приложение обрабатывает персональные и корпоративным ресурсы, вызовите [**ProtectionPolicyManager.CreateCurrentThreadNetworkContext**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.createcurrentthreadnetworkcontext.aspx) для всех удостоверений.  После получения ресурса утилизируйте ThreadNetworkContext, чтобы удалить все идентификационные метки из текущего потока.

> **Интерфейсы API** <br>
[ProtectionPolicyManager.GetForCurrentView](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getforcurrentview.aspx)<br>
[ProtectionPolicyManager.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.aspx)<br>
[ProtectionPolicyManager.CreateCurrentThreadNetworkContext](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.createcurrentthreadnetworkcontext.aspx)

**Шаг 3. Прочитайте ресурс в буфер**

```csharp
IBuffer data = await client.GetBufferAsync(resourceURI);
```


**Обработайте перенаправления страницы**

Иногда веб-сервер перенаправляет трафик на более актуальную версию ресурса.

Для обработки такого случая отправляйте запросы, пока состояние ответа на запрос не будет иметь значение **ОК**.

Затем используйте URI этого ответа, чтобы получить идентификатор конечной точки. Вот как можно это сделать:

```csharp
public static async Task<IBuffer> getDataFromNetworkResource(Uri resourceURI)
{
    bool finalURL = false;

    HttpResponseMessage response = null;

    while (!finalURL)
    {

        Windows.Networking.HostName hostName =
            new Windows.Networking.HostName(resourceURI.Host);

        string identity = await ProtectionPolicyManager.
            GetPrimaryManagedIdentityForNetworkEndpointAsync(hostName);

        HttpClient client = null;

        HttpBaseProtocolFilter filter = new HttpBaseProtocolFilter();
        filter.AllowAutoRedirect = false;

        if (!string.IsNullOrEmpty(m_EnterpriseId))
        {
            using (ThreadNetworkContext threadNetworkContext =
                    ProtectionPolicyManager.CreateCurrentThreadNetworkContext(identity))
            {
                client = new HttpClient(filter);
            }
        }
        else
        {
            client = new HttpClient(filter);
        }
        HttpRequestMessage message = new HttpRequestMessage(HttpMethod.Get, resourceURI);
        response = await client.SendRequestAsync(message);

        if (response.StatusCode == HttpStatusCode.MultipleChoices &&
            response.StatusCode == HttpStatusCode.MovedPermanently &&
            response.StatusCode == HttpStatusCode.Found &&
            response.StatusCode == HttpStatusCode.SeeOther &&
            response.StatusCode == HttpStatusCode.NotModified &&
            response.StatusCode == HttpStatusCode.UseProxy &&
            response.StatusCode == HttpStatusCode.TemporaryRedirect &&
            response.StatusCode == HttpStatusCode.PermanentRedirect)
        {
            resourceURI = message.RequestUri;
        }
        else
        {
            finalURL = true;
        }
    }

    IBuffer data = await response.Content.ReadAsBufferAsync();

    return data;

}
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.GetPrimaryManagedIdentityForNetworkEndpointAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getprimarymanagedidentityfornetworkendpointasync.aspx)<br>
[ProtectionPolicyManager.CreateCurrentThreadNetworkContext](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.createcurrentthreadnetworkcontext.aspx)<br>
[ProtectionPolicyManager.GetForCurrentView](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getforcurrentview.aspx)<br>
[ProtectionPolicyManager.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.aspx)

### Чтение данных из буфера обмена

**Получите разрешение на использование данных из буфера обмена**

Чтобы получить данные из буфера обмена, запросите разрешение у Windows. Для этого используйте [**DataPackageView.RequestAccessAsync**](https://msdn.microsoft.com/library/windows/apps/dn706645.aspx).

```csharp
public static async Task PasteText(TextBox textBox)
{
    DataPackageView dataPackageView = Clipboard.GetContent();

    if (dataPackageView.Contains(StandardDataFormats.Text))
    {
        ProtectionPolicyEvaluationResult result = await dataPackageView.RequestAccessAsync();

        if (result == ProtectionPolicyEvaluationResult..Allowed)
        {
            string contentsOfClipboard = await dataPackageView.GetTextAsync();
            textBox.Text = contentsOfClipboard;
        }
    }
}
```

> **Интерфейсы API** <br>
[DataPackageView.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/dn706645.aspx)

**Скройте или отключите функции, использующие данные буфера обмена**

Определите, имеет ли текущее представление разрешение на доступ к данным в буфере обмена.

Если не имеет, вы можете отключить или скрыть элементы управления, позволяющие пользователям вставлять данные из буфера обмена или предварительно просматривать его содержимое.

```csharp
private bool IsClipboardAllowedAsync()
{
    ProtectionPolicyEvaluationResult protectionPolicyEvaluationResult = ProtectionPolicyEvaluationResult.Blocked;

    DataPackageView dataPackageView = Clipboard.GetContent();

    if (dataPackageView.Contains(StandardDataFormats.Text))

        protectionPolicyEvaluationResult =
            ProtectionPolicyManager.CheckAccess(dataPackageView.Properties.EnterpriseId,
                ProtectionPolicyManager.GetForCurrentView().Identity);

    return (protectionPolicyEvaluationResult == ProtectionPolicyEvaluationResult.Allowed |
        protectionPolicyEvaluationResult == ProtectionPolicyEvaluationResult.ConsentRequired);
}
```

> **Интерфейсы API** <br>
[ProtectionPolicyEvaluationResult](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicyevaluationresult.aspx)<br>
[ProtectionPolicyManager.GetForCurrentView](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getforcurrentview.aspx)<br>
[ProtectionPolicyManager.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.aspx)

**Предотвратите показ пользователям диалогового окна с запросом согласия**

Новый документ не является *персональным* или *корпоративным*. Он просто новый. Если пользователь вставляет в него корпоративные данные, Windows принудительно применяет политику, и для пользователя отображается диалоговое окно с запросом согласия. Этот код предотвращает такую операцию. Эта задача не направлена на защиту данных. Она теснее связана с предотвращением показа пользователям диалогового окна с запросом согласия в тех случаях, когда ваше приложение создает совершенно новый элемент.

```csharp
private async void PasteText(bool isNewEmptyDocument)
{
    DataPackageView dataPackageView = Clipboard.GetContent();

    if (dataPackageView.Contains(StandardDataFormats.Text))
    {
        if (!string.IsNullOrEmpty(dataPackageView.Properties.EnterpriseId))
        {
            if (isNewEmptyDocument)
            {
                ProtectionPolicyManager.TryApplyProcessUIPolicy(dataPackageView.Properties.EnterpriseId);
                string contentsOfClipboard = contentsOfClipboard = await dataPackageView.GetTextAsync();
                // add this string to the new item or document here.          

            }
            else
            {
                ProtectionPolicyEvaluationResult result = await dataPackageView.RequestAccessAsync();

                if (result == ProtectionPolicyEvaluationResult.Allowed)
                {
                    string contentsOfClipboard = contentsOfClipboard = await dataPackageView.GetTextAsync();
                    // add this string to the new item or document here.
                }
            }
        }
    }
}
```

> **Интерфейсы API** <br>
[DataPackageView.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/dn706645.aspx)<br>
[ProtectionPolicyEvaluationResult](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicyevaluationresult.aspx)<br>
[ProtectionPolicyManager.TryApplyProcessUIPolicy](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.tryapplyprocessuipolicy.aspx)


### Чтение данных из контракта отправки данных

Когда сотрудники выбирают ваше приложение для обеспечения общего доступа к их данным, ваше приложение откроет новый элемент, в котором будет находиться данное содержимое.

Как отмечено ранее, новый элемент не является *персональным* или *корпоративным*. Он просто новый. Если ваш код добавляет корпоративное содержимое в элемент, Windows принудительно применяет политику, а для пользователя выводится диалоговое окно с запросом согласия. Этот код предотвращает такую операцию.

```csharp
protected override async void OnShareTargetActivated(ShareTargetActivatedEventArgs args)
{
    bool isNewEmptyDocument = true;
    string identity = "corp.microsoft.com";

    ShareOperation shareOperation = args.ShareOperation;
    if (shareOperation.Data.Contains(StandardDataFormats.Text))
    {
        if (!string.IsNullOrEmpty(shareOperation.Data.Properties.EnterpriseId))
        {
            if (isNewEmptyDocument)
                // If this is a new and empty document, and we're allowed to access
                // the data, then we can avoid popping the consent dialog
                ProtectionPolicyManager.TryApplyProcessUIPolicy(shareOperation.Data.Properties.EnterpriseId);
            else
            {
                // In this case, we can't optimize the workflow, so we just
                // request consent from the user in this case.

                ProtectionPolicyEvaluationResult protectionPolicyEvaluationResult = await shareOperation.Data.RequestAccessAsync();

                if (protectionPolicyEvaluationResult == ProtectionPolicyEvaluationResult.Allowed)
                {
                    string text = await shareOperation.Data.GetTextAsync();

                    // Do something with that text.
                }
            }
        }
        else
        {
            // If the data has no enterprise identity, then we already have access.
            string text = await shareOperation.Data.GetTextAsync();

            // Do something with that text.
        }

    }

}
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.RequestAccessAsync](https://msdn.microsoft.com/library/windows/apps/dn705789.aspx)<br>
[ProtectionPolicyEvaluationResult](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicyevaluationresult.aspx)<br>
[ProtectionPolicyManager.TryApplyProcessUIPolicy](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.tryapplyprocessuipolicy.aspx)

## Защита корпоративных данных

Защищайте корпоративные данные, покидающие ваше приложение. Данные, покидающие ваше приложение при их отображении на странице, сохранении в файл или конечную точку сети или с помощью контракта отправки данных.

### <a id="display-data"></a>Защита данных, отображаемых на страницах

При отображении данных на странице сообщите Windows, к какому типу относятся эти данные (персональные или корпоративные). Для этого *присвойте метку* текущему представлению приложения или всему процессу приложения.

При добавлении метки для представления или процесса Windows принудительно применяет к нему политику. Это поможет предотвратить утечки данных, возникающие в результате действий, которые ваше приложение не контролирует. Например, на компьютере пользователь может использовать CTRL-V для копирования корпоративных данных из представления и затем вставить эти данные в другом приложении. Windows обеспечивает защиту от таких действий. Windows также помогает принудительно применять контракты на отправку данных.

**Добавьте метку текущему представлению приложения**

Сделайте это, если у вашего приложения несколько представлений, часть из которых используют корпоративные данные, а другая часть — персональные данные.

```csharp

// tag as enterprise data. "identity" the string that contains the enterprise ID.
// You'd get that from a file, network endpoint, or clipboard data package.
ProtectionPolicyManager.GetForCurrentView().Identity = identity;

// tag as personal data.
ProtectionPolicyManager.GetForCurrentView().Identity = String.Empty();
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.GetForCurrentView](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getforcurrentview.aspx)<br>
[ProtectionPolicyManager.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.aspx)

**Добавьте метку процессу**

Сделайте это, если все представления вашего приложения будут работать только с одним типом данных (персональным или корпоративным).

Это позволит избавиться от необходимости управления представлениями с разными метками.

```csharp


// tag as enterprise data. "identity" the string that contains the enterprise ID.
// You'd get that from a file, network endpoint, or clipboard data package.
bool result =
            ProtectionPolicyManager.TryApplyProcessUIPolicy(identity);

// tag as personal data.
bool result =
            ProtectionPolicyManager.TryApplyProcessUIPolicy(String.Empty());
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.TryApplyProcessUIPolicy](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.tryapplyprocessuipolicy.aspx)

### Защита данных в файле

Создайте защищенный файла и выполните запись в него.

**Шаг 1. Определите, сможет ли ваше приложение создать корпоративный файл**

Ваше приложение может создать корпоративный файл, если строка удостоверения управляется политикой, и ваше приложения включено в список разрешенных для этой политики.

```csharp
  if (!ProtectionPolicyManager.IsIdentityManaged(identity)) return false;
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.IsIdentityManaged](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.isidentitymanaged.aspx)


**Шаг 2. Создайте файл и обеспечьте его защиту по идентификатору**

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile storageFile = await storageFolder.CreateFileAsync("sample.txt",
    CreationCollisionOption.ReplaceExisting);

FileProtectionInfo fileProtectionInfo =
    await FileProtectionManager.ProtectAsync(storageFile, identity);
```

> **Интерфейсы API** <br>
[FileProtectionManager.ProtectAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionmanager.protectasync.aspx)

**Шаг 3. Запишите поток или буфер в файл**

*Запись потока*

```csharp
    if (fileProtectionInfo.Identity == identity &&
        fileProtectionInfo.Status == FileProtectionStatus.Protected)
    {
        var stream = await storageFile.OpenAsync(FileAccessMode.ReadWrite);

        using (var outputStream = stream.GetOutputStreamAt(0))
        {
            using (var dataWriter = new DataWriter(outputStream))
            {
                dataWriter.WriteString(enterpriseData);
            }
        }

    }
```

*Запись буфера*

```csharp
     if (fileProtectionInfo.Identity == identity &&
         fileProtectionInfo.Status == FileProtectionStatus.Protected)
     {
         var buffer = Windows.Security.Cryptography.CryptographicBuffer.ConvertStringToBinary(
             enterpriseData, Windows.Security.Cryptography.BinaryStringEncoding.Utf8);

         await FileIO.WriteBufferAsync(storageFile, buffer);

      }
```

> **Интерфейсы API** <br>
[FileProtectionInfo](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectioninfo.aspx)<br>
[FileProtectionStatus](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionstatus.aspx)<br>



### Защита данных в файле в виде фонового процесса

Этот код может выполняться, когда экран устройства заблокирован. Если администратор настроил безопасную политику "Защита данных в режиме блокировки" (DPL), Windows удаляет ключи шифрования, необходимые для получения доступа к защищенным ресурсам из памяти устройства. Это предотвращает утечки данных при потере устройства. Эта же функция также удаляет ключи, связанные с защищенными файлами, при закрытии их дескрипторов.

Вам придется использовать подход, который держит дескриптор файла открытым при создании файла.  

**Шаг 1. Определите, можете ли вы создать корпоративный файл**

Вы можете создать корпоративный файл, если используемая вами строка удостоверения управляется политикой, и ваше приложение включено в список разрешенных для этой политики.

```csharp
if (!ProtectionPolicyManager.IsIdentityManaged(identity)) return false;
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.IsIdentityManaged](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.isidentitymanaged.aspx)

**Шаг 2. Создайте файл и обеспечьте его защиту по идентификатору**

[**FileProtectionManager.CreateProtectedAndOpenAsync**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionmanager.createprotectedandopenasync.aspx) создает защищенный файл и держит дескриптор файла открытым при записи в него.

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;

ProtectedFileCreateResult protectedFileCreateResult =
    await FileProtectionManager.CreateProtectedAndOpenAsync(storageFolder,
        "sample.txt", identity, CreationCollisionOption.ReplaceExisting);
```

> **Интерфейсы API** <br>
[FileProtectionManager.CreateProtectedAndOpenAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionmanager.createprotectedandopenasync.aspx)

**Шаг 3. Запишите поток или буфер в файл**

Этот пример записывает поток в файл.

```csharp
if (protectedFileCreateResult.ProtectionInfo.Identity == identity &&
    protectedFileCreateResult.ProtectionInfo.Status == FileProtectionStatus.Protected)
{
    IOutputStream outputStream =
        protectedFileCreateResult.Stream.GetOutputStreamAt(0);

    using (DataWriter writer = new DataWriter(outputStream))
    {
        writer.WriteString(enterpriseData);
        await writer.StoreAsync();
        await writer.FlushAsync();
    }

    outputStream.Dispose();
}
else if (protectedFileCreateResult.ProtectionInfo.Status == FileProtectionStatus.AccessSuspended)
{
    // Perform any special processing for the access suspended case.
}

```

> **Интерфейсы API** <br>
[ProtectedFileCreateResult.ProtectionInfo](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectedfilecreateresult.protectioninfo.aspx)<br>
[FileProtectionStatus](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionstatus.aspx)<br>
[ProtectedFileCreateResult.Stream](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectedfilecreateresult.stream.aspx)<br>

### Защита части файла

В большинстве случаев безопаснее хранить корпоративные и персональные данные раздельно, но при необходимости можно сохранить их в один файл. Например, Microsoft Outlook может хранить корпоративные почтовые сообщения вместе с персональными в одном архивном файле.

Используйте шифрование только для корпоративных данных, а не для всего файла. Таким образом пользователи смогут продолжать использовать этот файл даже после отмены регистрации в MDM и после отзыва прав доступа к корпоративным данным. Кроме того ваше приложение должно отслеживать, какие данные оно шифрует, чтобы оно понимало, какие данные необходимо защищать при повторном чтении файла в память.

**Шаг 1. Добавьте корпоративные данные в шифрованный поток или буфер**

```csharp
string enterpriseDataString = "<employees><employee><name>Bill</name><social>xxx-xxx-xxxx</social></employee></employees>";

var enterpriseData= Windows.Security.Cryptography.CryptographicBuffer.ConvertStringToBinary(
        enterpriseDataString, Windows.Security.Cryptography.BinaryStringEncoding.Utf8);

BufferProtectUnprotectResult result =
   await DataProtectionManager.ProtectAsync(enterpriseData, identity);

enterpriseData= result.Buffer;
```

> **Интерфейсы API** <br>
[DataProtectionManager.ProtectAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.dataprotectionmanager.protectasync.aspx)<br>
[BufferProtectUnprotectResult.buffer](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.bufferprotectunprotectresult.buffer.aspx)


**Шаг 2. Добавьте персональные данные в незашифрованный поток или буфер**

```csharp
string personalDataString = "<recipies><recipe><name>BillsCupCakes</name><cooktime>30</cooktime></recipe></recipies>";

var personalData = Windows.Security.Cryptography.CryptographicBuffer.ConvertStringToBinary(
    personalDataString, Windows.Security.Cryptography.BinaryStringEncoding.Utf8);
```

**Шаг 3. Запишите оба потока или буфера в файл**

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;

StorageFile storageFile = await storageFolder.CreateFileAsync("data.xml",
    CreationCollisionOption.ReplaceExisting);

 // Write both buffers to the file and save the file.

var stream = await storageFile.OpenAsync(FileAccessMode.ReadWrite);

using (var outputStream = stream.GetOutputStreamAt(0))
{
    using (var dataWriter = new DataWriter(outputStream))
    {
        dataWriter.WriteBuffer(enterpriseData);
        dataWriter.WriteBuffer(personalData);

        await dataWriter.StoreAsync();
        await outputStream.FlushAsync();
    }
}
```

**Шаг 4. Отслеживайте расположение корпоративных данных в файле**

Отслеживание корпоративных данных в этом файле входит в зону ответственности вашего приложения.

Вы можете хранить эти сведения в свойстве, связанном с файлом, в базе данных или в каком-либо текстовом заголовке в файле.

В этом примере такие сведения сохраняются в отдельный XML-файл.

```csharp
StorageFile metaDataFile = await storageFolder.CreateFileAsync("metadata.xml",
   CreationCollisionOption.ReplaceExisting);

await Windows.Storage.FileIO.WriteTextAsync
    (metaDataFile, "<EnterpriseDataMarker start='0' end='" + enterpriseData.Length.ToString() +
    "'></EnterpriseDataMarker>");
```

### Чтение защищенной части файла

Вот как следует выполнять чтение корпоративных данных из этого файла.

**Шаг 1. Определите расположение корпоративных данных в файле**

```csharp
Windows.Storage.StorageFolder storageFolder =
    Windows.Storage.ApplicationData.Current.LocalFolder;

 Windows.Storage.StorageFile metaDataFile =
   await storageFolder.GetFileAsync("metadata.xml");

string metaData = await Windows.Storage.FileIO.ReadTextAsync(metaDataFile);

XmlDocument doc = new XmlDocument();

doc.LoadXml(metaData);

uint startPosition =
    Convert.ToUInt16((doc.FirstChild.Attributes.GetNamedItem("start")).InnerText);

uint endPosition =
    Convert.ToUInt16((doc.FirstChild.Attributes.GetNamedItem("end")).InnerText);
```

**Шаг 2. Откройте файл данных и убедитесь, что он не защищен.**

```csharp
Windows.Storage.StorageFile dataFile =
    await storageFolder.GetFileAsync("data.xml");

FileProtectionInfo protectionInfo =
    await FileProtectionManager.GetProtectionInfoAsync(dataFile);

if (protectionInfo.Status == FileProtectionStatus.Protected)
    return false;
```

> **Интерфейсы API** <br>
[FileProtectionManager.GetProtectionInfoAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionmanager.getprotectioninfoasync.aspx)<br>
[FileProtectionInfo](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectioninfo.aspx)<br>
[FileProtectionStatus](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionstatus.aspx)<br>

**Шаг 3. Выполните чтение корпоративных данных из файла**

```csharp
var stream = await dataFile.OpenAsync(Windows.Storage.FileAccessMode.ReadWrite);

stream.Seek(startPosition);

Windows.Storage.Streams.Buffer tempBuffer = new Windows.Storage.Streams.Buffer(50000);

IBuffer enterpriseData = await stream.ReadAsync(tempBuffer, endPosition, InputStreamOptions.None);
```

**Шаг 4. Расшифруйте буфер, содержащий корпоративные данные**

```csharp
DataProtectionInfo dataProtectionInfo =
   await DataProtectionManager.GetProtectionInfoAsync(enterpriseData);

if (dataProtectionInfo.Status == DataProtectionStatus.Protected)
{
    BufferProtectUnprotectResult result = await DataProtectionManager.UnprotectAsync(enterpriseData);
    enterpriseData = result.Buffer;
}
else if (dataProtectionInfo.Status == DataProtectionStatus.Revoked)
{
    // Code goes here to handle this situation. Perhaps, show UI
    // saying that the user's data has been revoked.
}

```

> **Интерфейсы API** <br>
[DataProtectionInfo](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.dataprotectioninfo.aspx)<br>
[DataProtectionManager.GetProtectionInfoAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.dataprotectionmanager.getstreamprotectioninfoasync.aspx)<br>


### Защита данных в папке

Вы можете создать папку и защитить ее. Таким образом все элементы, которые вы добавите в эту папку, будут автоматически защищены.

```csharp
private async Task<bool> CreateANewFolderAndProtectItAsync(string folderName, string identity)
{
    if (!ProtectionPolicyManager.IsIdentityManaged(identity)) return false;

    StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
    StorageFolder newStorageFolder =
        await storageFolder.CreateFolderAsync(folderName);

    FileProtectionInfo fileProtectionInfo =
        await FileProtectionManager.ProtectAsync(newStorageFolder, identity);

    if (fileProtectionInfo.Identity != identity ||
        fileProtectionInfo.Status != FileProtectionStatus.Protected)
    {
        // Protection failed.
        return false;
    }
    return true;
}
```

Перед установкой защиты для папки убедитесь, что она пуста. Невозможно установить защиту для папки, в которой уже содержатся какие-либо элементы.

> **Интерфейсы API** <br>
[ProtectionPolicyManager.IsIdentityManaged](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.isidentitymanaged.aspx)<br>
[FileProtectionManager.ProtectAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionmanager.protectasync.aspx)<br>
[FileProtectionInfo.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectioninfo.identity.aspx)<br>
[FileProtectionInfo.Status](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectioninfo.status.aspx)


### Защита данных в конечной точке сети

Создайте защищенный контекст потока для отправки данных в корпоративную конечную точку.  

**Шаг 1. Получите идентификатор конечной точки сети**

```csharp
Windows.Networking.HostName hostName =
    new Windows.Networking.HostName(resourceURI.Host);

string identity = await ProtectionPolicyManager.
    GetPrimaryManagedIdentityForNetworkEndpointAsync(hostName);
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.GetPrimaryManagedIdentityForNetworkEndpointAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getprimarymanagedidentityfornetworkendpointasync.aspx)

**Шаг 2. Создайте защищенный контекст потока и отправьте данные в конечную точку сети**

```csharp
HttpClient client = null;

if (!string.IsNullOrEmpty(m_EnterpriseId))
{
    ProtectionPolicyManager.GetForCurrentView().Identity = identity;

    using (ThreadNetworkContext threadNetworkContext =
            ProtectionPolicyManager.CreateCurrentThreadNetworkContext(identity))
    {
        client = new HttpClient();
        HttpRequestMessage message = new HttpRequestMessage(HttpMethod.Put, resourceURI);
        message.Content = new HttpStreamContent(dataToWrite);

        HttpResponseMessage response = await client.SendRequestAsync(message);

        if (response.StatusCode == HttpStatusCode.Ok)
            return true;
        else
            return false;
    }
}
else
{
    return false;
}
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.GetForCurrentView](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getforcurrentview.aspx)<br>
[ProtectionPolicyManager.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.aspx)<br>
[ProtectionPolicyManager.CreateCurrentThreadNetworkContext](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.createcurrentthreadnetworkcontext.aspx)

### Защита данных, которыми ваше приложение делится с помощью контракта на отправку данных

Если необходимо, чтобы пользователи могли совместно использовать содержимое из вашего приложения, вам потребуется реализовать контракт на отправку данных и обработать событие [**DataTransferManager.DataRequested**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.datatransfer.datatransfermanager.datarequested).

В обработчике событий установите корпоративный контекст удостоверения в пакете данных.

```csharp
private void OnShareSourceOperation(object sender, RoutedEventArgs e)
{
    // Register the current page as a share source (or you could do this earlier in your app).
    DataTransferManager.GetForCurrentView().DataRequested += OnDataRequested;
    DataTransferManager.ShowShareUI();
}

private void OnDataRequested(DataTransferManager sender, DataRequestedEventArgs args)
{
    if (!string.IsNullOrEmpty(this.shareSourceContent))
    {
        var protectionPolicyManager = ProtectionPolicyManager.GetForCurrentView();
        DataPackage requestData = args.Request.Data;
        requestData.Properties.Title = this.shareSourceTitle;
        requestData.Properties.EnterpriseId = protectionPolicyManager.Identity;
        requestData.SetText(this.shareSourceContent);
    }
}
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.GetForCurrentView](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getforcurrentview.aspx)<br>
[ProtectionPolicyManager.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.aspx)


### Защита файлов, копируемых в другое расположение

```csharp
private async void CopyProtectionFromOneFileToAnother
    (StorageFile sourceStorageFile, StorageFile targetStorageFile)
{
    bool copyResult = await
        FileProtectionManager.CopyProtectionAsync(sourceStorageFile, targetStorageFile);

    if (!copyResult)
    {
        // Copying failed. To diagnose, you could check the file's status.
        // (call FileProtectionManager.GetProtectionInfoAsync and
        // check FileProtectionInfo.Status).
    }
}
```

> **Интерфейсы API** <br>
[FileProtectionManager.CopyProtectionAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.fileprotectionmanager.copyprotectionasync.aspx)<br>


### Защита корпоративных данных при блокировке экрана устройства

Удалите все конфиденциальные данные из памяти, когда устройство блокируется. Когда пользователь разблокирует устройство, ваше приложение может без проблем снова добавить эти данные.

Обработайте событие [**ProtectionPolicyManager.ProtectedAccessSuspending**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.protectedaccesssuspending.aspx), чтобы ваше приложение понимало, когда экран блокируется. Это событие инициируется, только если администратор настроит надежную защиту данных в политике блокировки. Windows временно удаляет ключи защиты данных, доступные на устройстве. Windows удаляет эти ключи для предотвращения несанкционированного доступа к зашифрованным данным, когда устройство заблокировано и, возможно, не находится в распоряжении его владельца.  

Обработайте событие [**ProtectionPolicyManager.ProtectedAccessResumed**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.protectedaccessresumed.aspx), чтобы ваше приложение понимало, когда экран разблокируется. Это событие инициируется независимо от того, настроил ли администратор надежную защиту данных в политике блокировки.

#### Удаление конфиденциальных данных из памяти при блокировке экрана

Обеспечьте защиту конфиденциальных данных и закройте все файловые потоки, которые ваше приложение открывало на защищенных файлах, чтобы помочь предотвратить кэширование конфиденциальных данных системой в памяти.

В этом примере все содержимое из текстового поля сохраняется в зашифрованный буфер и удаляется из этого текстового поля.

```csharp
private async void ProtectionPolicyManager_ProtectedAccessSuspending(object sender, ProtectedAccessSuspendingEventArgs e)
{
    Deferral deferral = e.GetDeferral();

    if (ProtectionPolicyManager.GetForCurrentView().Identity != String.Empty)
    {
        IBuffer documentBodyBuffer = CryptographicBuffer.ConvertStringToBinary
           (documentTextBlock.Text, BinaryStringEncoding.Utf8);

        BufferProtectUnprotectResult result = await DataProtectionManager.ProtectAsync
            (documentBodyBuffer, ProtectionPolicyManager.GetForCurrentView().Identity);

        if (result.ProtectionInfo.Status == DataProtectionStatus.Protected)
        {
            this.protectedDocumentBuffer = result.Buffer;
            documentTextBlock.Text = null;
        }
    }

    // Close any open streams that you are actively working with
    // to make sure that we have no unprotected content in memory.

    // Optionally, code goes here to use e.Deadline to determine whether we have more
    // than 15 seconds left before the suspension deadline. If we do then process any
    // messages queued up for sending while we are still able to access them.

    deferral.Complete();
}
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.ProtectedAccessSuspending](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.protectedaccesssuspending.aspx)<br>
[ProtectionPolicyManager.GetForCurrentView](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getforcurrentview.aspx)<br>
[ProtectionPolicyManager.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.aspx)</br>
[DataProtectionManager.ProtectAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.dataprotectionmanager.protectasync.aspx)<br>
[BufferProtectUnprotectResult.buffer](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.bufferprotectunprotectresult.buffer.aspx)<br>
[ProtectedAccessSuspendingEventArgs.GetDeferral](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectedaccesssuspendingeventargs.getdeferral.aspx)<br>
[Deferral.Complete](https://msdn.microsoft.com/library/windows/apps/windows.foundation.deferral.complete.aspx)<br>

#### Добавление конфиденциальных данных после разблокировки устройства

[**ProtectionPolicyManager.ProtectedAccessResumed**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.protectedaccessresumed.aspx) инициируется, когда устройство разблокируется, и ключи снова становятся доступны на нем.

[**ProtectedAccessResumedEventArgs.Identities**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectedaccessresumedeventargs.identities.aspx) является пустой коллекцией, если администратор не настроил надежную защиту данных для политики блокировки.

В этом примере выполняется обратная предыдущему примеру операция. Здесь расшифровывается буфер, данные из этого буфера добавляются обратно в текстовое поле, а затем удаляются из буфера.

```csharp
private async void ProtectionPolicyManager_ProtectedAccessResumed(object sender, ProtectedAccessResumedEventArgs e)
{
    if (ProtectionPolicyManager.GetForCurrentView().Identity != String.Empty)
    {
        BufferProtectUnprotectResult result = await DataProtectionManager.UnprotectAsync
            (this.protectedDocumentBuffer);

        if (result.ProtectionInfo.Status == DataProtectionStatus.Unprotected)
        {
            // Restore the unprotected version.
            documentTextBlock.Text = CryptographicBuffer.ConvertBinaryToString
                (BinaryStringEncoding.Utf8, result.Buffer);
            this.protectedDocumentBuffer = null;
        }
    }

}
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager.ProtectedAccessResumed](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.protectedaccessresumed.aspx)<br>
[ProtectionPolicyManager.GetForCurrentView](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.getforcurrentview.aspx)<br>
[ProtectionPolicyManager.Identity](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.aspx)</br>
[DataProtectionManager.UnprotectAsync](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.dataprotectionmanager.unprotectasync.aspx)<br>
[BufferProtectUnprotectResult.Status](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.bufferprotectunprotectresult.aspx)<br>

## Обработка корпоративных данных, когда право доступа к защищенному содержимому отзывается

Если вы хотите, чтобы ваше приложение получало уведомление о том, что для устройства отменена регистрация в MDM, или что администратор явно отозвал права доступа к корпоративным данным, обработайте событие [**ProtectionPolicyManager_ProtectedContentRevoked**](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.protectedcontentrevoked.aspx).

В этом примере определяется, отозвано ли у приложения электронной почты право доступа к данным в корпоративном в почтовом ящике.

```csharp
private string mailIdentity = "contoso.com";

void MailAppSetup()
{
    ProtectionPolicyManager.ProtectedContentRevoked += ProtectionPolicyManager_ProtectedContentRevoked;
    // Code goes here to set up mailbox for 'mailIdentity'.
}

private void ProtectionPolicyManager_ProtectedContentRevoked(object sender, ProtectedContentRevokedEventArgs e)
{
    if (!new System.Collections.Generic.List<string>(e.Identities).Contains
        (this.mailIdentity))
    {
        // This event is not for our identity.
        return;
    }

    // Code goes here to delete any metadata associated with 'mailIdentity'.
}
```

> **Интерфейсы API** <br>
[ProtectionPolicyManager_ProtectedContentRevoked](https://msdn.microsoft.com/library/windows/apps/windows.security.enterprisedata.protectionpolicymanager.protectedcontentrevoked.aspx)<br>

## Статьи по теме

[Пример Windows Information Protection (WIP)](http://go.microsoft.com/fwlink/p/?LinkId=620031&clcid=0x409)
 

 



<!--HONumber=Aug16_HO3-->


