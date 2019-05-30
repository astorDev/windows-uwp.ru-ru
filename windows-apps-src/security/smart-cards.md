---
title: Смарт-карты
description: В этом разделе объясняется, каким образом приложения универсальной платформы для Windows (UWP) могут использовать смарт-карты для подключения пользователей к защищенным сетевым службам, включая получение доступа к физическим устройствам чтения смарт-карт, создание виртуальных смарт-карт, связь со смарт-картами, проверку подлинности пользователей, сброс ПИН-кодов пользователей и удаление или отключение смарт-карт.
ms.assetid: 86524267-50A0-4567-AE17-35C4B6D24745
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp, security
ms.localizationpriority: medium
ms.openlocfilehash: 5498480e0dbe2c8be96d92df766b15676a3e6b7b
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371936"
---
# <a name="smart-cards"></a>Смарт-карты




В этом разделе объясняется, каким образом приложения универсальной платформы для Windows (UWP) могут использовать смарт-карты для подключения пользователей к защищенным сетевым службам, включая получение доступа к физическим устройствам чтения смарт-карт, создание виртуальных смарт-карт, связь со смарт-картами, проверку подлинности пользователей, сброс ПИН-кодов пользователей и удаление или отключение смарт-карт. 

## <a name="configure-the-app-manifest"></a>Настройка манифеста приложения


Перед проверкой подлинности пользователей с помощью смарт-карт или виртуальных смарт-карт необходимо задать в файле проекта Package.appxmanifest возможность **Общие сертификаты пользователей**.

## <a name="access-connected-card-readers-and-smart-cards"></a>Доступ к подключенным устройствам чтения карт и смарт-картам


Вы можете запросить наличие устройств чтения и подключенных смарт-карт, передав код устройства (заданный в [**DeviceInformation**](https://docs.microsoft.com/uwp/api/Windows.Devices.Enumeration.DeviceInformation)) методу [**SmartCardReader.FromIdAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardreader.fromidasync). Для доступа к смарт-картам, подключенным к возвращенному устройству чтения, вызовите метод [**SmartCardReader.FindAllCardsAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardreader.findallcardsasync).

```cs
string selector = SmartCardReader.GetDeviceSelector();
DeviceInformationCollection devices =
    await DeviceInformation.FindAllAsync(selector);

foreach (DeviceInformation device in devices)
{
    SmartCardReader reader =
        await SmartCardReader.FromIdAsync(device.Id);

    // For each reader, we want to find all the cards associated
    // with it.  Then we will create a SmartCardListItem for
    // each (reader, card) pair.
    IReadOnlyList<SmartCard> cards =
        await reader.FindAllCardsAsync();
}
```

Вы также должны разрешить приложению просматривать события [**CardAdded**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardreader.cardadded), реализовав метод для обработки реакции приложения на вставку карты.

```cs
private void reader_CardAdded(SmartCardReader sender, CardAddedEventArgs args)
{
  // A card has been inserted into the sender SmartCardReader.
}
```

Затем каждый возвращенный объект [**SmartCard**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCard) можно передать в объект [**SmartCardProvisioning**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning) для доступа к методам, которые позволяют приложению обращаться к карте и настраивать ее конфигурацию.

## <a name="create-a-virtual-smart-card"></a>Создание виртуальной смарт-карты


Перед созданием виртуальной смарт-карты с помощью [**SmartCardProvisioning**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning) приложение должно указать понятное имя, ключ администратора и экземпляр [**SmartCardPinPolicy**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardPinPolicy). Понятное имя обычно вводится в приложение, а само приложение должно предоставить ключ администратора и создать экземпляр текущей версии **SmartCardPinPolicy**, а затем передать все три значения в [**RequestVirtualSmartCardCreationAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestvirtualsmartcardcreationasync).

1.  Создайте новый экземпляр [**SmartCardPinPolicy**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardPinPolicy).
2.  Создайте ключ администратора, вызвав метод [**CryptographicBuffer.GenerateRandom**](https://docs.microsoft.com/uwp/api/windows.security.cryptography.cryptographicbuffer.generaterandom) со значением ключа администратора, предоставленным службой или средством управления.
3.  Передайте эти значения вместе со строкой *FriendlyNameText* в [**RequestVirtualSmartCardCreationAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestvirtualsmartcardcreationasync).

```cs
SmartCardPinPolicy pinPolicy = new SmartCardPinPolicy();
pinPolicy.MinLength = 6;

IBuffer adminkey = CryptographicBuffer.GenerateRandom(24);

SmartCardProvisioning provisioning = await
     SmartCardProvisioning.RequestVirtualSmartCardCreationAsync(
          "Card friendly name",
          adminkey,
          pinPolicy);
```

Когда метод [**RequestVirtualSmartCardCreationAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestvirtualsmartcardcreationasync) вернет связанный объект [**SmartCardProvisioning**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning), виртуальная смарт-карта будет создана и готова к использованию.

## <a name="handle-authentication-challenges"></a>Обработка запросов проверки подлинности


Для проверки подлинности со смарт-картами или виртуальными смарт-картами ваше приложение должно выполнять запросы на проверку по данным о ключе администратора, хранящимся на карте, и данным о ключе администратора, хранящимся на сервере проверки подлинности или в средстве управления.

В следующем коде показано, как поддерживать проверку подлинности с помощью смарт-карты для служб или изменение данных физической или виртуальной карты. Если данные, созданные по ключу администратора на карте (challenge), совпадают с данными о ключе администратора, предоставленными сервером или средством управления (adminkey), то проверка подлинности проходит успешно.

```cs
static class ChallengeResponseAlgorithm
{
    public static IBuffer CalculateResponse(IBuffer challenge, IBuffer adminkey)
    {
        if (challenge == null)
            throw new ArgumentNullException("challenge");
        if (adminkey == null)
            throw new ArgumentNullException("adminkey");

        SymmetricKeyAlgorithmProvider objAlg = SymmetricKeyAlgorithmProvider.OpenAlgorithm(SymmetricAlgorithmNames.TripleDesCbc);
        var symmetricKey = objAlg.CreateSymmetricKey(adminkey);
        var buffEncrypted = CryptographicEngine.Encrypt(symmetricKey, challenge, null);
        return buffEncrypted;
    }
}
```

Ссылки на код будут встречаться в остальной части раздела, когда мы будем изучать выполнение действия проверки подлинности и внесение изменений в информацию на смарт-карте и виртуальной смарт-карте.

## <a name="verify-smart-card-or-virtual-smart-card-authentication-response"></a>Проверка ответа при проверке подлинности с помощью смарт-карты или виртуальной смарт-карты


Мы определили логику для запросов проверки подлинности и можем связаться с устройством чтения для доступа к смарт-карте или получить доступ к виртуальной смарт-карте для проверки подлинности.

1.  Для начала запроса вызовите метод [**GetChallengeContextAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardprovisioning.getchallengecontextasync) из объекта [**SmartCardProvisioning**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning), связанного со смарт-картой. Будет создан экземпляр [**SmartCardChallengeContext**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardChallengeContext), который содержит значение [**Challenge**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardchallengecontext.challenge) для карты.

2.  Затем передайте значение запроса карты и ключ администратора, предоставленный службой или средством управления, в **ChallengeResponseAlgorithm** , определенный в предыдущем примере.

3.  [**VerifyResponseAsync** ](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardchallengecontext.verifyresponseasync) вернет **true** при успешном выполнении проверки.

```cs
bool verifyResult = false;
SmartCard card = await rootPage.GetSmartCard();
SmartCardProvisioning provisioning =
    await SmartCardProvisioning.FromSmartCardAsync(card);

using (SmartCardChallengeContext context =
       await provisioning.GetChallengeContextAsync())
{
    IBuffer response = ChallengeResponseAlgorithm.CalculateResponse(
        context.Challenge,
        rootPage.AdminKey);

    verifyResult = await context.VerifyResponseAsync(response);
}
```

## <a name="change-or-reset-a-user-pin"></a>Изменение или сброс ПИН-кода пользователя


Чтобы изменить ПИН-код, связанный со смарт-картой, сделайте следующее.

1.  Обратитесь к карте и создайте связанный объект [**SmartCardProvisioning**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning).
2.  Вызовите метод [**RequestPinChangeAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestpinchangeasync), чтобы показать пользователю интерфейс для выполнения этой операции.
3.  Если ПИН-код успешно изменен, этот вызов возвращает значение **true**.

```cs
SmartCardProvisioning provisioning =
    await SmartCardProvisioning.FromSmartCardAsync(card);

bool result = await provisioning.RequestPinChangeAsync();
```

Чтобы запросить сброс ПИН-кода, сделайте следующее.

1.  Вызовите метод [**RequestPinResetAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestpinresetasync) для запуска операции. В этот вызов входит метод [**SmartCardPinResetHandler**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardpinresethandler), который представляет смарт-карту, и запрос на сброс ПИН-кода.
2.  [**SmartCardPinResetHandler** ](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardpinresethandler) сведения, наши **ChallengeResponseAlgorithm**заключен в [ **SmartCardPinResetDeferral** ](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardPinResetDeferral) вызов, использует для сравнения значения запрос карты и ключ администратора, предоставляемые службы или средства управления для аутентификации запроса.

3.  Если запрос выполняется успешно, то вызов [**RequestPinResetAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestpinresetasync) завершается, и возвращается значение **true**, если ПИН-код успешно сброшен.

```cs
SmartCardProvisioning provisioning =
    await SmartCardProvisioning.FromSmartCardAsync(card);

bool result = await provisioning.RequestPinResetAsync(
    (pinResetSender, request) =>
    {
        SmartCardPinResetDeferral deferral =
            request.GetDeferral();

        try
        {
            IBuffer response =
                ChallengeResponseAlgorithm.CalculateResponse(
                    request.Challenge,
                    rootPage.AdminKey);
            request.SetResponse(response);
        }
        finally
        {
            deferral.Complete();
        }
    });
}
```

## <a name="remove-a-smart-card-or-virtual-smart-card"></a>Удаление смарт-карты или виртуальной смарт-карты


При удалении физической смарт-карты создается событие [**CardRemoved**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardreader.cardremoved).

Свяжите создание этого события в устройстве чтения карт с методом, который определяет реакцию приложения на удаление карты или устройства чтения, в качестве обработчика событий. Такой реакцией может быть простое уведомление пользователя об удалении карты.

```cs
reader = card.Reader;
reader.CardRemoved += HandleCardRemoved;
```

Удаление виртуальной смарт-карты обрабатывается в программном коде. Сначала запрашивается карта, а затем вызывается метод [**RequestVirtualSmartCardDeletionAsync**](https://docs.microsoft.com/uwp/api/windows.devices.smartcards.smartcardprovisioning.requestvirtualsmartcarddeletionasync) из возвращенного объекта [**SmartCardProvisioning**](https://docs.microsoft.com/uwp/api/Windows.Devices.SmartCards.SmartCardProvisioning).

```cs
bool result = await SmartCardProvisioning
    .RequestVirtualSmartCardDeletionAsync(card);
```