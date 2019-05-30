---
title: Биометрия отпечатков пальцев
description: В этой статье рассказывается, как добавить биометрию отпечатков пальцев в приложение универсальной платформы Windows (UWP).
ms.assetid: 55483729-5F8A-401A-8072-3CD611DDFED2
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, security
ms.localizationpriority: medium
ms.openlocfilehash: bbb40dc9fa65515a2b01d7a2c92145b27e04f075
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372567"
---
# <a name="fingerprint-biometrics"></a>Биометрия отпечатков пальцев




В этой статье рассказывается, как добавить биометрию отпечатков пальцев в приложение универсальной платформы Windows (UWP). Включив запрос на биометрическую проверку подлинности (по отпечатку пальца), когда пользователь должен давать согласие на определенное действие, вы повысите безопасность своего приложения. Например, вы можете требовать проходить проверку подлинности по отпечатку пальца перед авторизацией покупки из приложения или перед открытием ресурсов с ограниченным доступом. Управление проверкой подлинности по отпечатку пальца осуществляет класс [**UserConsentVerifier**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials.UI.UserConsentVerifier) в пространстве имен [**Windows.Security.Credentials.UI**](https://docs.microsoft.com/uwp/api/Windows.Security.Credentials.UI).

## <a name="check-the-device-for-a-fingerprint-reader"></a>Проверка наличия сканера отпечатков пальцев в устройстве


Чтобы узнать, оснащено ли устройство сканером отпечатков пальцев, вызовите метод [**UserConsentVerifier.CheckAvailabilityAsync**](https://docs.microsoft.com/uwp/api/windows.security.credentials.ui.userconsentverifier.checkavailabilityasync). Даже если устройство поддерживает проверку подлинности по отпечатку пальца, ваше приложение должно предоставлять пользователям возможность включать и выключать ее в разделе «Параметры».

```cs
public async System.Threading.Tasks.Task<string> CheckFingerprintAvailability()
{
    string returnMessage = "";

    try
    {
        // Check the availability of fingerprint authentication.
        var ucvAvailability = await Windows.Security.Credentials.UI.UserConsentVerifier.CheckAvailabilityAsync();

        switch (ucvAvailability)
        {
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.Available:
                returnMessage = "Fingerprint verification is available.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.DeviceBusy:
                returnMessage = "Biometric device is busy.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.DeviceNotPresent:
                returnMessage = "No biometric device found.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.DisabledByPolicy:
                returnMessage = "Biometric verification is disabled by policy.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerifierAvailability.NotConfiguredForUser:
                returnMessage = "The user has no fingerprints registered. Please add a fingerprint to the " +
                                "fingerprint database and try again.";
                break;
            default:
                returnMessage = "Fingerprints verification is currently unavailable.";
                break;
        }
    }
    catch (Exception ex)
    {
        returnMessage = "Fingerprint authentication availability check failed: " + ex.ToString();
    }

    return returnMessage;
}
```

## <a name="request-consent-and-return-results"></a>Запрос согласия и возврат результатов


Чтобы запросить согласие пользователя на сканирование отпечатка пальца, вызовите метод [**UserConsentVerifier.RequestVerificationAsync**](https://docs.microsoft.com/uwp/api/windows.security.credentials.ui.userconsentverifier.requestverificationasync). Для проверки подлинности по отпечатку пальца пользователь должен добавить в базу данных отпечатков свою «подпись» в виде отпечатка.

Когда вы вызываете метод [**UserConsentVerifier.RequestVerificationAsync**](https://docs.microsoft.com/uwp/api/windows.security.credentials.ui.userconsentverifier.requestverificationasync), для пользователя открывается модальное диалоговое окно, запрашивающее сканирование отпечатка пальца. Методу **UserConsentVerifier.RequestVerificationAsync** можно передать сообщение, которое будет отображаться в этом модальном диалоговом окне, как показано на следующем изображении.

```cs
private async System.Threading.Tasks.Task<string> RequestConsent(string userMessage)
{
    string returnMessage;

    if (String.IsNullOrEmpty(userMessage))
    {
        userMessage = "Please provide fingerprint verification.";
    }

    try
    {
        // Request the logged on user's consent via fingerprint swipe.
        var consentResult = await Windows.Security.Credentials.UI.UserConsentVerifier.RequestVerificationAsync(userMessage);

        switch (consentResult)
        {
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.Verified:
                returnMessage = "Fingerprint verified.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.DeviceBusy:
                returnMessage = "Biometric device is busy.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.DeviceNotPresent:
                returnMessage = "No biometric device found.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.DisabledByPolicy:
                returnMessage = "Biometric verification is disabled by policy.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.NotConfiguredForUser:
                returnMessage = "The user has no fingerprints registered. Please add a fingerprint to the " +
                                "fingerprint database and try again.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.RetriesExhausted:
                returnMessage = "There have been too many failed attempts. Fingerprint authentication canceled.";
                break;
            case Windows.Security.Credentials.UI.UserConsentVerificationResult.Canceled:
                returnMessage = "Fingerprint authentication canceled.";
                break;
            default:
                returnMessage = "Fingerprint authentication is currently unavailable.";
                break;
        }
    }
    catch (Exception ex)
    {
        returnMessage = "Fingerprint authentication failed: " + ex.ToString();
    }

    return returnMessage;
}
```