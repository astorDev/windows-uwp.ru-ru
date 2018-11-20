---
author: drewbatgit
ms.assetid: BF877F23-1238-4586-9C16-246F3F25AE35
description: В этой статье описывается, как добавить возможности адаптивной потоковой передачи мультимедийного содержимого с защитой Microsoft PlayReady в приложение универсальной платформы Windows (UWP).
title: Адаптивная потоковая передача с защитой PlayReady
ms.author: drewbat
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7132de481f22f53269cd9bd69a38819c5b71cb55
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7297729"
---
# <a name="adaptive-streaming-with-playready"></a>Адаптивная потоковая передача с защитой PlayReady


В этой статье описывается, как добавить возможности адаптивной потоковой передачи мультимедийного содержимого с защитой Microsoft PlayReady в приложение универсальной платформы Windows (UWP). 

Эта функция в настоящее время поддерживает воспроизведение содержимого Dynamic Streaming over HTTP (DASH).

HLS (HTTP Live Streaming компании Apple) не поддерживается PlayReady.

Smooth Streaming также сейчас не поддерживается изначально; однако технология PlayReady является расширяемой и с помощью дополнительного кода или библиотек можно обеспечить поддержку Smooth Streaming с защитой PlayReady, с использованием программного или даже аппаратного DRM (управления цифровыми правами).

В этой статье рассматриваются только аспекты адаптивной потоковой передачи, связанные с PlayReady. Общие сведения о реализации адаптивной потоковой передачи см. в разделе [Адаптивная потоковая передача](adaptive-streaming.md).

В данной статье используется код из [Примера адаптивной потоковой передачи](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming) в репозитории **Универсальных примеров для Windows** корпорации Майкрософт на GitHub. В сценарии 4 рассматривается использование адаптивной потоковой передачи с помощью PlayReady. Вы можете скачать пример из репозитория в ZIP-файле путем перехода на корневой уровень репозитория и нажав кнопку **Скачать ZIP**.

Вам потребуются следующие операторы **using**:

```csharp
using LicenseRequest;
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Runtime.InteropServices;
using System.Threading.Tasks;
using Windows.Foundation.Collections;
using Windows.Media.Protection;
using Windows.Media.Protection.PlayReady;
using Windows.Media.Streaming.Adaptive;
using Windows.UI.Xaml.Controls;
```

Пространство имен **LicenseRequest** определено в **CommonLicenseRequest.cs**, файле PlayReady, который корпорация Майкрософт предоставляет лицензиатам.

Необходимо объявить несколько глобальных переменных:

```csharp
private AdaptiveMediaSource ams = null;
private MediaProtectionManager protectionManager = null;
private string playReadyLicenseUrl = "";
private string playReadyChallengeCustomData = "";
```

Также требуется объявить следующую константу:

```csharp
private const uint MSPR_E_CONTENT_ENABLING_ACTION_REQUIRED = 0x8004B895;
```

## <a name="setting-up-the-mediaprotectionmanager"></a>Настройка MediaProtectionManager

Чтобы добавить защиту содержимого PlayReady в приложение UWP, необходимо настроить объект [MediaProtectionManager](https://msdn.microsoft.com/library/windows/apps/br207040). Для этого нужно инициализировать объект [**AdaptiveMediaSource**](https://msdn.microsoft.com/library/windows/apps/dn946912).

Следующий код настраивает объект [MediaProtectionManager](https://msdn.microsoft.com/library/windows/apps/br207040):

```csharp
private void SetUpProtectionManager(ref MediaElement mediaElement)
{
    protectionManager = new MediaProtectionManager();

    protectionManager.ComponentLoadFailed += 
        new ComponentLoadFailedEventHandler(ProtectionManager_ComponentLoadFailed);

    protectionManager.ServiceRequested += 
        new ServiceRequestedEventHandler(ProtectionManager_ServiceRequested);

    PropertySet cpSystems = new PropertySet();

    cpSystems.Add(
        "{F4637010-03C3-42CD-B932-B48ADF3A6A54}", 
        "Windows.Media.Protection.PlayReady.PlayReadyWinRTTrustedInput");

    protectionManager.Properties.Add("Windows.Media.Protection.MediaProtectionSystemIdMapping", cpSystems);

    protectionManager.Properties.Add(
        "Windows.Media.Protection.MediaProtectionSystemId", 
        "{F4637010-03C3-42CD-B932-B48ADF3A6A54}");

    protectionManager.Properties.Add(
        "Windows.Media.Protection.MediaProtectionContainerGuid", 
        "{9A04F079-9840-4286-AB92-E65BE0885F95}");

    mediaElement.ProtectionManager = protectionManager;
}
```

Этот код можно просто скопировать в приложение, поскольку он необходим для настройки защиты содержимого.

Событие [ComponentLoadFailed](https://msdn.microsoft.com/library/windows/apps/br207041) инициируется при ошибке загрузки двоичных данных. Нам нужно добавить обработчик этого события, которое сообщает о том, что загрузка не завершена:

```csharp
private void ProtectionManager_ComponentLoadFailed(
    MediaProtectionManager sender, 
    ComponentLoadFailedEventArgs e)
{
    e.Completion.Complete(false);
}
```

Также необходимо обработчик события [ServiceRequested](https://msdn.microsoft.com/library/windows/apps/br207045), которое активируется при запросе службы. Этот код проверяет тип запроса и реагирует соответствующим образом:

```csharp
private async void ProtectionManager_ServiceRequested(
    MediaProtectionManager sender, 
    ServiceRequestedEventArgs e)
{
    if (e.Request is PlayReadyIndividualizationServiceRequest)
    {
        PlayReadyIndividualizationServiceRequest IndivRequest = 
            e.Request as PlayReadyIndividualizationServiceRequest;

        bool bResultIndiv = await ReactiveIndivRequest(IndivRequest, e.Completion);
    }
    else if (e.Request is PlayReadyLicenseAcquisitionServiceRequest)
    {
        PlayReadyLicenseAcquisitionServiceRequest licenseRequest = 
            e.Request as PlayReadyLicenseAcquisitionServiceRequest;

        LicenseAcquisitionRequest(
            licenseRequest, 
            e.Completion, 
            playReadyLicenseUrl, 
            playReadyChallengeCustomData);
    }
}
```

## <a name="individualization-service-requests"></a>Запросы на обслуживание индивидуализации

Следующий код реактивно отправляет запрос на обслуживание индивидуализации PlayReady. Мы передаем запрос в качестве параметра функции. Вызов помещаем в блок try/catch, и если исключений нет, говорим, что запрос выполнен успешно:

```csharp
async Task<bool> ReactiveIndivRequest(
    PlayReadyIndividualizationServiceRequest IndivRequest, 
    MediaProtectionServiceCompletion CompletionNotifier)
{
    bool bResult = false;
    Exception exception = null;

    try
    {
        await IndivRequest.BeginServiceRequest();
    }
    catch (Exception ex)
    {
        exception = ex;
    }
    finally
    {
        if (exception == null)
        {
            bResult = true;
        }
        else
        {
            COMException comException = exception as COMException;
            if (comException != null && comException.HResult == MSPR_E_CONTENT_ENABLING_ACTION_REQUIRED)
            {
                IndivRequest.NextServiceRequest();
            }
        }
    }

    if (CompletionNotifier != null) CompletionNotifier.Complete(bResult);
    return bResult;
}
```

Или же мы можем использовать заранее отправить запрос на обслуживание индивидуализации. В этом случае, мы вызываем функцию ниже вместо вызова `ReactiveIndivRequest` в `ProtectionManager_ServiceRequested`:

```csharp
async void ProActiveIndivRequest()
{
    PlayReadyIndividualizationServiceRequest indivRequest = new PlayReadyIndividualizationServiceRequest();
    bool bResultIndiv = await ReactiveIndivRequest(indivRequest, null);
}
```

## <a name="license-acquisition-service-requests"></a>Запросы на обслуживание для получения лицензии

Если запросом был [PlayReadyLicenseAcquisitionServiceRequest](https://msdn.microsoft.com/library/windows/apps/dn986285), мы вызываем списке функцию ниже, чтобы запросить и получить лицензию PlayReady. Мы сообщаем объекту **MediaProtectionServiceCompletion**, который мы передали, был ли запрос выполнен успешно или нет, и завершаем запрос:

```csharp
async void LicenseAcquisitionRequest(
    PlayReadyLicenseAcquisitionServiceRequest licenseRequest, 
    MediaProtectionServiceCompletion CompletionNotifier, 
    string Url, 
    string ChallengeCustomData)
{
    bool bResult = false;
    string ExceptionMessage = string.Empty;

    try
    {
        if (!string.IsNullOrEmpty(Url))
        {
            if (!string.IsNullOrEmpty(ChallengeCustomData))
            {
                System.Text.UTF8Encoding encoding = new System.Text.UTF8Encoding();
                byte[] b = encoding.GetBytes(ChallengeCustomData);
                licenseRequest.ChallengeCustomData = Convert.ToBase64String(b, 0, b.Length);
            }

            PlayReadySoapMessage soapMessage = licenseRequest.GenerateManualEnablingChallenge();

            byte[] messageBytes = soapMessage.GetMessageBody();
            HttpContent httpContent = new ByteArrayContent(messageBytes);

            IPropertySet propertySetHeaders = soapMessage.MessageHeaders;

            foreach (string strHeaderName in propertySetHeaders.Keys)
            {
                string strHeaderValue = propertySetHeaders[strHeaderName].ToString();

                if (strHeaderName.Equals("Content-Type", StringComparison.OrdinalIgnoreCase))
                {
                    httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse(strHeaderValue);
                }
                else
                {
                    httpContent.Headers.Add(strHeaderName.ToString(), strHeaderValue);
                }
            }

            CommonLicenseRequest licenseAcquision = new CommonLicenseRequest();

            HttpContent responseHttpContent = 
                await licenseAcquision.AcquireLicense(new Uri(Url), httpContent);

            if (responseHttpContent != null)
            {
                Exception exResult = licenseRequest.ProcessManualEnablingResponse(
                                         await responseHttpContent.ReadAsByteArrayAsync());

                if (exResult != null)
                {
                    throw exResult;
                }
                bResult = true;
            }
            else
            {
                ExceptionMessage = licenseAcquision.GetLastErrorMessage();
            }
        }
        else
        {
            await licenseRequest.BeginServiceRequest();
            bResult = true;
        }
    }
    catch (Exception e)
    {
        ExceptionMessage = e.Message;
    }

    CompletionNotifier.Complete(bResult);
}
```

## <a name="initializing-the-adaptivemediasource"></a>Инициализация AdaptiveMediaSource

Наконец, вам потребуется функция для инициализации объекта [AdaptiveMediaSource](https://msdn.microsoft.com/library/windows/apps/dn946912), созданного на основе заданных [Uri](https://msdn.microsoft.com/library/windows/apps/xaml/system.uri.aspx) и [MediaElement](https://msdn.microsoft.com/library/windows/apps/br242926). **Uri** — это ссылка на файл мультимедиа (HLS или DASH), а элемент **MediaElement** должен быть определен в коде XAML.

```csharp
async private void InitializeAdaptiveMediaSource(System.Uri uri, MediaElement m)
{
    AdaptiveMediaSourceCreationResult result = await AdaptiveMediaSource.CreateFromUriAsync(uri);
    if (result.Status == AdaptiveMediaSourceCreationStatus.Success)
    {
        ams = result.MediaSource;
        SetUpProtectionManager(ref m);
        m.SetMediaStreamSource(ams);
    }
    else
    {
        // Error handling
    }
}
```

Можно вызвать эту функцию в любом событии, которое обрабатывает начало адаптивной потоковой передачи, например в событии нажатия кнопки.

## <a name="see-also"></a>См. также
- [PlayReady DRM](playready-client-sdk.md)




