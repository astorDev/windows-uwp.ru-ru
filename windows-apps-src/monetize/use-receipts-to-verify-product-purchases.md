---
ms.assetid: E322DFFE-8EEC-499D-87BC-EDA5CFC27551
description: Каждая транзакция Microsoft Store, которая заканчивается успешной сделкой, может дополнительно возвращать квитанцию транзакции.
title: Проверка покупок продуктов с помощью квитанций
ms.date: 04/16/2018
ms.topic: article
keywords: Windows 10, UWP, покупки из приложения, IAP, квитанции, Windows.ApplicationModel.Store
ms.localizationpriority: medium
ms.openlocfilehash: b71d55d71a63060a66265051fafc8bdf7313e77b
ms.sourcegitcommit: c01c29cd97f1cbf050950526e18e15823b6a12a0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8702555"
---
# <a name="use-receipts-to-verify-product-purchases"></a>Проверка покупок продуктов с помощью квитанций

Каждая транзакция Microsoft Store, которая заканчивается успешной сделкой, может дополнительно возвращать квитанцию транзакции. Эта квитанция предоставляет клиенту сведения об указанном продукте и денежных расходах.

Доступ к этой информации поддерживает сценарии, в которых ваше приложение должно проверить покупку пользователем приложения или надстройки (продукта из приложения или IAP) в Microsoft Store. Например, представим игру, предлагающую загруженное содержимое. Если пользователь, который приобрел игровое содержимое, захочет поиграть в эту игру на другом устройстве, то вам нужно будет проверить, действительно ли этот пользователь купил данное содержимое. Вот как это сделать.

> [!IMPORTANT]
> В этой статье показано, как использовать элементы пространства имен [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Store) для получения и проверки квитанции о покупке из приложения. Если вы используете пространство имен [Windows.Services.Store](https://docs.microsoft.com/uwp/api/Windows.Services.Store) для покупок из приложения (появились в Windows 10 версии 1607 и доступны в проектах, предназначенных для **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздней версии в Visual Studio), это пространство имен не предоставляет API-интерфейс для получения квитанций о покупках из приложения. Тем не менее для получения данных о транзакции покупки можно использовать метод REST в API коллекции Microsoft Store. Дополнительные сведения см. в разделе [Квитанции для покупок из приложения](in-app-purchases-and-trials.md#receipts).

## <a name="requesting-a-receipt"></a>Запрос квитанции


Пространство имен **Windows.ApplicationModel.Store** поддерживает несколько способов получения квитанции:

* Когда вы совершаете покупку, используя метод [CurrentApp.RequestAppPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestapppurchaseasync) или [CurrentApp.RequestProductPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.requestproductpurchaseasync) (или одну из других перегрузок этого метода), возвращаемое значение содержит квитанцию.
* Можно вызвать метод [CurrentApp.GetAppReceiptAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentapp.getappreceiptasync), чтобы получить актуальные сведения о квитанциях для приложения и любых надстроек в нем.

Квитанция приложения выглядит так.

> [!NOTE]
> Этот пример отформатирован, чтобы XML-код было удобнее читать. В квитанциях в настоящем приложении нет пробелов между элементами.

> [!div class="tabbedCodeSnippets"]
```xml
<Receipt Version="1.0" ReceiptDate="2012-08-30T23:10:05Z" CertificateId="b809e47cd0110a4db043b3f73e83acd917fe1336" ReceiptDeviceId="4e362949-acc3-fe3a-e71b-89893eb4f528">
    <AppReceipt Id="8ffa256d-eca8-712a-7cf8-cbf5522df24b" AppId="55428GreenlakeApps.CurrentAppSimulatorEventTest_z7q3q7z11crfr" PurchaseDate="2012-06-04T23:07:24Z" LicenseType="Full" />
    <ProductReceipt Id="6bbf4366-6fb2-8be8-7947-92fd5f683530" ProductId="Product1" PurchaseDate="2012-08-30T23:08:52Z" ExpirationDate="2012-09-02T23:08:49Z" ProductType="Durable" AppId="55428GreenlakeApps.CurrentAppSimulatorEventTest_z7q3q7z11crfr" />
    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        <SignedInfo>
            <CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
            <SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
            <Reference URI="">
                <Transforms>
                    <Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                </Transforms>
                <DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <DigestValue>cdiU06eD8X/w1aGCHeaGCG9w/kWZ8I099rw4mmPpvdU=</DigestValue>
            </Reference>
        </SignedInfo>
        <SignatureValue>SjRIxS/2r2P6ZdgaR9bwUSa6ZItYYFpKLJZrnAa3zkMylbiWjh9oZGGng2p6/gtBHC2dSTZlLbqnysJjl7mQp/A3wKaIkzjyRXv3kxoVaSV0pkqiPt04cIfFTP0JZkE5QD/vYxiWjeyGp1dThEM2RV811sRWvmEs/hHhVxb32e8xCLtpALYx3a9lW51zRJJN0eNdPAvNoiCJlnogAoTToUQLHs72I1dECnSbeNPXiG7klpy5boKKMCZfnVXXkneWvVFtAA1h2sB7ll40LEHO4oYN6VzD+uKd76QOgGmsu9iGVyRvvmMtahvtL1/pxoxsTRedhKq6zrzCfT8qfh3C1w==</SignatureValue>
    </Signature>
</Receipt>
```

Квитанция продукта выглядит так.

> [!NOTE]
> Этот пример отформатирован, чтобы XML-код было удобнее читать. В квитанциях по настоящим продуктам нет пробелов между элементами.

> [!div class="tabbedCodeSnippets"]
```xml
<Receipt Version="1.0" ReceiptDate="2012-08-30T23:08:52Z" CertificateId="b809e47cd0110a4db043b3f73e83acd917fe1336" ReceiptDeviceId="4e362949-acc3-fe3a-e71b-89893eb4f528">
    <ProductReceipt Id="6bbf4366-6fb2-8be8-7947-92fd5f683530" ProductId="Product1" PurchaseDate="2012-08-30T23:08:52Z" ExpirationDate="2012-09-02T23:08:49Z" ProductType="Durable" AppId="55428GreenlakeApps.CurrentAppSimulatorEventTest_z7q3q7z11crfr" />
    <Signature xmlns="http://www.w3.org/2000/09/xmldsig#">
        <SignedInfo>
            <CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
            <SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
            <Reference URI="">
                <Transforms>
                    <Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                </Transforms>
                <DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <DigestValue>Uvi8jkTYd3HtpMmAMpOm94fLeqmcQ2KCrV1XmSuY1xI=</DigestValue>
            </Reference>
        </SignedInfo>
        <SignatureValue>TT5fDET1X9nBk9/yKEJAjVASKjall3gw8u9N5Uizx4/Le9RtJtv+E9XSMjrOXK/TDicidIPLBjTbcZylYZdGPkMvAIc3/1mdLMZYJc+EXG9IsE9L74LmJ0OqGH5WjGK/UexAXxVBWDtBbDI2JLOaBevYsyy+4hLOcTXDSUA4tXwPa2Bi+BRoUTdYE2mFW7ytOJNEs3jTiHrCK6JRvTyU9lGkNDMNx9loIr+mRks+BSf70KxPtE9XCpCvXyWa/Q1JaIyZI7llCH45Dn4SKFn6L/JBw8G8xSTrZ3sBYBKOnUDbSCfc8ucQX97EyivSPURvTyImmjpsXDm2LBaEgAMADg==</SignatureValue>
    </Signature>
</Receipt>
```

Для подтверждения кода проверки можно использовать любой из этих примеров квитанций. Дополнительные сведения о содержимом квитанции см. в разделе [Описания элементов и атрибутов](#receipt-descriptions).

## <a name="validating-a-receipt"></a>Проверка квитанции

Для проверки подлинности квитанции серверная система (веб-служба или аналогичный объект) должна проверить подпись на квитанции с помощью открытого сертификата. Чтобы получить этот сертификат, используйте URL-адрес ```https://go.microsoft.com/fwlink/p/?linkid=246509&cid=CertificateId```, где ```CertificateId```— значение **CertificateId** в квитанции.

Пример процедуры валидации приведен ниже. Этот код выполняется в консольном приложении .NET Framework, которое содержит ссылку на сборку **System.Security**.

> [!div class="tabbedCodeSnippets"]
[!code-cs[ReceiptVerificationSample](./code/ReceiptVerificationSample/cs/Program.cs#ReceiptVerificationSample)]

<span id="receipt-descriptions" />

## <a name="element-and-attribute-descriptions-for-a-receipt"></a>Описания элементов и атрибутов для квитанции

В этом разделе описаны элементы и атрибуты в квитанции.

### <a name="receipt-element"></a>Элемент квитанции

Корневой элемент этого файла — это элемент **Receipt**, который содержит сведения о приложении и покупках из приложения. Этот элемент содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  |  Описание   |
|-------------|------------|--------|--------|
|  [AppReceipt](#appreceipt)  |    Нет        |  0или1  |  Содержит информацию о покупках для текущего приложения.            |
|  [ProductReceipt](#productreceipt)  |     Нет       |  0 или более    |   Содержит сведения о покупке из приложения для текущего приложения.     |
|  Signature  |      Да      |  1   |   Этот элемент — стандартная [конструкция XML-DSIG](http://go.microsoft.com/fwlink/p/?linkid=251093). Он содержит элемент **SignatureValue** с подписью, который можно использовать для проверки квитанции, а также элемент **SignedInfo**.      |

**Receipt** содержит следующие атрибуты.

|  Атрибут  |  Описание   |
|-------------|-------------------|
|  **Version**  |    Номер версии квитанции.            |
|  **CertificateId**  |     Отпечаток сертификата, который используется для добавления подписи на квитанцию.          |
|  **ReceiptDate**  |    Дата подписания и загрузки квитанции.           |  
|  **ReceiptDeviceId**  |   Определяет устройство, используемое для запроса этой квитанции.         |  |

<span id="appreceipt" />

### <a name="appreceipt-element"></a>Элемент AppReceipt

Этот элемент содержит информацию о покупках для текущего приложения.

**AppReceipt** содержит следующие атрибуты.

|  Атрибут  |  Описание   |
|-------------|-------------------|
|  **Id**  |    Идентифицирует покупку.           |
|  **AppId**  |     Значение имени семейства пакетов, используемое ОС для приложения.           |
|  **LicenseType**  |    **Полная**, если пользователь приобрел полную версию приложения. **Пробная**, если пользователь загрузил пробную версию приложения.           |  
|  **PurchaseDate**  |    Дата приобретения приложения.          |  |

<span id="productreceipt" />

### <a name="productreceipt-element"></a>Элемент ProductReceipt

Этот элемент содержит сведения о покупке из приложения для текущего приложения.

**ProductReceipt** содержит следующие атрибуты.

|  Атрибут  |  Описание   |
|-------------|-------------------|
|  **Id**  |    Идентифицирует покупку.           |
|  **AppId**  |     Определяет приложение, через которое пользователь совершил покупку.           |
|  **ProductId**  |     Определяет приобретенный продукт.           |
|  **ProductType**  |    Определяет тип продукта. В настоящее время поддерживает только значение **Durable**.          |  
|  **PurchaseDate**  |    Дата покупки.          |  |

 

 
