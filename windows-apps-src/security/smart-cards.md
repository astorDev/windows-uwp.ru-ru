---
title: "Смарт-карты"
description: "В этом разделе объясняется, каким образом приложения универсальной платформы для Windows (UWP) могут использовать смарт-карты для подключения пользователей к защищенным сетевым службам, включая получение доступа к физическим устройствам чтения смарт-карт, создание виртуальных смарт-карт, связь со смарт-картами, проверку подлинности пользователей, сброс ПИН-кодов пользователей и удаление или отключение смарт-карт."
ms.assetid: 86524267-50A0-4567-AE17-35C4B6D24745
author: awkoren
translationtype: Human Translation
ms.sourcegitcommit: 2ea21aeee5dd93bb44de3a1793b352d2046b3839
ms.openlocfilehash: d0646aca9863f3f326df9b3a86adb2481fdcda70

---

# Смарт-карты


\[ Обновлено для приложений UWP в Windows10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этом разделе объясняется, каким образом приложения универсальной платформы для Windows (UWP) могут использовать смарт-карты для подключения пользователей к защищенным сетевым службам, включая получение доступа к физическим устройствам чтения смарт-карт, создание виртуальных смарт-карт, связь со смарт-картами, проверку подлинности пользователей, сброс ПИН-кодов пользователей и удаление или отключение смарт-карт. 

## Настройка манифеста приложения


Перед проверкой подлинности пользователей с помощью смарт-карт или виртуальных смарт-карт необходимо задать в файле проекта Package.appxmanifest возможность **Общие сертификаты пользователей**.

## Доступ к подключенным устройствам чтения карт и смарт-картам


Вы можете запросить наличие устройств чтения и подключенных смарт-карт, передав код устройства (заданный в [**DeviceInformation**](https://msdn.microsoft.com/library/windows/apps/br225393)) методу [**SmartCardReader.FromIdAsync**](https://msdn.microsoft.com/library/windows/apps/dn263890). Для доступа к смарт-картам, подключенным к возвращенному устройству чтения, вызовите метод [**SmartCardReader.FindAllCardsAsync**](https://msdn.microsoft.com/library/windows/apps/dn263887).

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

Вы также должны разрешить приложению просматривать события [**CardAdded**](https://msdn.microsoft.com/library/windows/apps/dn263866), реализовав метод для обработки реакции приложения на вставку карты.

```cs
private void reader_CardAdded(SmartCardReader sender, CardAddedEventArgs args)
{
  // A card has been inserted into the sender SmartCardReader.
}
```

Затем каждый возвращенный объект [**SmartCard**](https://msdn.microsoft.com/library/windows/apps/dn297565) можно передать в объект [**SmartCardProvisioning**](https://msdn.microsoft.com/library/windows/apps/dn263801) для доступа к методам, которые позволяют приложению обращаться к карте и настраивать ее конфигурацию.

## Создание виртуальной смарт-карты


Перед созданием виртуальной смарт-карты с помощью [**SmartCardProvisioning**](https://msdn.microsoft.com/library/windows/apps/dn263801) приложение должно указать понятное имя, ключ администратора и экземпляр [**SmartCardPinPolicy**](https://msdn.microsoft.com/library/windows/apps/dn297642). Понятное имя обычно вводится в приложение, а само приложение должно предоставить ключ администратора и создать экземпляр текущей версии **SmartCardPinPolicy**, а затем передать все три значения в [**RequestVirtualSmartCardCreationAsync**](https://msdn.microsoft.com/library/windows/apps/dn263830).

1.  Создайте новый экземпляр [**SmartCardPinPolicy**](https://msdn.microsoft.com/library/windows/apps/dn297642).
2.  Создайте ключ администратора, вызвав метод [**CryptographicBuffer.GenerateRandom**](https://msdn.microsoft.com/library/windows/apps/br241392) со значением ключа администратора, предоставленным службой или средством управления.
3.  Передайте эти значения вместе со строкой *FriendlyNameText* в [**RequestVirtualSmartCardCreationAsync**](https://msdn.microsoft.com/library/windows/apps/dn263830).

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

Когда метод [**RequestVirtualSmartCardCreationAsync**](https://msdn.microsoft.com/library/windows/apps/dn263830) вернет связанный объект [**SmartCardProvisioning**](https://msdn.microsoft.com/library/windows/apps/dn263801), виртуальная смарт-карта будет создана и готова к использованию.

## Обработка запросов проверки подлинности


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

## Проверка ответа при проверке подлинности с помощью смарт-карты или виртуальной смарт-карты


Мы определили логику для запросов проверки подлинности и можем связаться с устройством чтения для доступа к смарт-карте или получить доступ к виртуальной смарт-карте для проверки подлинности.

1.  Для начала запроса вызовите метод [**GetChallengeContextAsync**](https://msdn.microsoft.com/library/windows/apps/dn263811) из объекта [**SmartCardProvisioning**](https://msdn.microsoft.com/library/windows/apps/dn263801), связанного со смарт-картой. Будет создан экземпляр [**SmartCardChallengeContext**](https://msdn.microsoft.com/library/windows/apps/dn297570), который содержит значение [**Challenge**](https://msdn.microsoft.com/library/windows/apps/dn297578) для карты.

2.  Затем передайте значение запроса карты и ключ администратора, предоставленный службой или средством управления, в **ChallengeResponseAlgorithm** , определенный в предыдущем примере.

3.  
              Если проверка подлинности проходит успешно, [**VerifyResponseAsync**](https://msdn.microsoft.com/library/windows/apps/dn297627) возвращает значение **true**.

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

## Изменение или сброс ПИН-кода пользователя


Чтобы изменить ПИН-код, связанный со смарт-картой, сделайте следующее.

1.  Обратитесь к карте и создайте связанный объект [**SmartCardProvisioning**](https://msdn.microsoft.com/library/windows/apps/dn263801).
2.  Вызовите метод [**RequestPinChangeAsync**](https://msdn.microsoft.com/library/windows/apps/dn263823), чтобы показать пользователю интерфейс для выполнения этой операции.
3.  Если ПИН-код успешно изменен, этот вызов возвращает значение **true**.

```cs
SmartCardProvisioning provisioning =
    await SmartCardProvisioning.FromSmartCardAsync(card);

bool result = await provisioning.RequestPinChangeAsync();
```

Чтобы запросить сброс ПИН-кода, сделайте следующее.

1.  Вызовите метод [**RequestPinResetAsync**](https://msdn.microsoft.com/library/windows/apps/dn263825) для запуска операции. В этот вызов входит метод [**SmartCardPinResetHandler**](https://msdn.microsoft.com/library/windows/apps/dn297701), который представляет смарт-карту, и запрос на сброс ПИН-кода.
2.  [**SmartCardPinResetHandler**](https://msdn.microsoft.com/library/windows/apps/dn297701) предоставляет информацию, которая используется алгоритмом **ChallengeResponseAlgorithm**, заключенным в вызов [**SmartCardPinResetDeferral**](https://msdn.microsoft.com/library/windows/apps/dn297693), для сравнения значения запроса карты и ключа администратора, предоставленного службой или средством управления, для проверки подлинности запроса.

3.  Если запрос выполняется успешно, то вызов [**RequestPinResetAsync**](https://msdn.microsoft.com/library/windows/apps/dn263825) завершается, и возвращается значение **true**, если ПИН-код успешно сброшен.

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

## Удаление смарт-карты или виртуальной смарт-карты


При удалении физической смарт-карты создается событие [**CardRemoved**](https://msdn.microsoft.com/library/windows/apps/dn263875).

Свяжите создание этого события в устройстве чтения карт с методом, который определяет реакцию приложения на удаление карты или устройства чтения, в качестве обработчика событий. Такой реакцией может быть простое уведомление пользователя об удалении карты.

```cs
reader = card.Reader;
reader.CardRemoved += HandleCardRemoved;
```

Удаление виртуальной смарт-карты обрабатывается в программном коде. Сначала запрашивается карта, а затем вызывается метод [**RequestVirtualSmartCardDeletionAsync**](https://msdn.microsoft.com/library/windows/apps/dn263850) из возвращенного объекта [**SmartCardProvisioning**](https://msdn.microsoft.com/library/windows/apps/dn263801).

```cs
bool result = await SmartCardProvisioning
    .RequestVirtualSmartCardDeletionAsync(card);
```


<!--HONumber=Aug16_HO3-->


