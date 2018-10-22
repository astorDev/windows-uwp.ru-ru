---
author: Xansky
ms.assetid: 32572890-26E3-4FBB-985B-47D61FF7F387
description: Узнайте, как включить покупки из приложения и пробные версии в приложениях UWP, предназначенных для выпусков Windows 10 до версии 1607.
title: Покупки из приложения и пробные версии, использующие пространство имен Windows.ApplicationModel.Store
ms.author: mhopkins
ms.date: 08/25/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: uwp, покупки из приложения, IAP, надстройки, пробные версии, Windows.ApplicationModel.Store
ms.localizationpriority: medium
ms.openlocfilehash: bb2c242ea4b7e3881751874c096165279854aa5e
ms.sourcegitcommit: c4d3115348c8b54fcc92aae8e18fdabc3deb301d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2018
ms.locfileid: "5400380"
---
# <a name="in-app-purchases-and-trials-using-the-windowsapplicationmodelstore-namespace"></a>Покупки из приложения и пробные версии, использующие пространство имен Windows.ApplicationModel.Store

Вы можете использовать элементы в пространстве имен [Windows.ApplicationModel.Store](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.aspx) для добавления функций совершения покупок из приложения и пробных версий в ваше приложение универсальной платформы Windows (UWP), чтобы получать доход от приложения. Эти API также предоставляют доступ к лицензионной информации вашего приложения.

Статьи в этом разделе содержат подробные руководства и примеры кода для использования элементов в пространстве имен **Windows.ApplicationModel.Store** для нескольких распространенных сценариев. Обзор базовых концепций, связанных с покупками из приложения в приложениях UWP, см. в разделе [Покупки из приложения и пробные версии](in-app-purchases-and-trials.md). Полный пример, в котором показано внедрение пробных версий и покупок из приложения с использованием пространства имен **Windows.ApplicationModel.Store**, доступен в разделе [Пример для Магазина](https://github.com/Microsoft/Windows-universal-samples/tree/win10-1507/Samples/Store).

> [!IMPORTANT]
> Пространство имен **Windows.ApplicationModel.Store** больше не обновляется с добавлением новых функций. Если проект приложения ориентирован на **Windows 10 Anniversary Edition (10.0; сборка 14393)** или более поздние версии в Visual Studio (то есть, вы ориентируете приложение на Windows 10 версии 1607 и более поздние версии), рекомендуется вместо этого использовать пространство имен [Windows.Services.Store](https://msdn.microsoft.com/library/windows/apps/windows.services.store.aspx). Подробнее см. в разделе [Покупки из приложения и пробные версии](https://msdn.microsoft.com/windows/uwp/monetize/in-app-purchases-and-trials). Пространство имен **Windows.ApplicationModel.Store** не поддерживается в классических приложениях Windows, использующих [мост для классических приложений](https://developer.microsoft.com/windows/bridges/desktop), и в приложениях и играх, использующих изолированную среду разработки в центре разработки (например, такая ситуация возникает с любой игрой, которая интегрируется с Xbox Live). Эти продукты должны использовать для реализации покупок из приложения и пробных версий пространство имен **Windows.Services.Store**.

## <a name="get-started-with-the-currentapp-and-currentappsimulator-classes"></a>Начало работы с классами CurrentApp и CurrentAppSimulator

Главной точкой входа в пространство имен **Windows.ApplicationModel.Store** является класс [CurrentApp](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentapp.aspx). Этот класс предоставляет статические свойства и методы, которые можно использовать, чтобы получить сведения о текущем приложении и его доступных надстройках, получить лицензионные сведения о текущем приложении или его надстройках, приобрести приложение или надстройку для текущего пользователя и решить другие задачи.

Класс [CurrentApp](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentapp.aspx) получает свои данные из Microsoft Store, поэтому для успешного использования этого класса в своем приложении вам потребуется учетная запись разработчика, а приложение должно быть опубликовано в Store. Перед отправкой приложения в Store вы можете протестировать код при помощи смоделированной версии этого класса с именем [CurrentAppSimulator](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentappsimulator.aspx). После тестирования приложения и перед его отправкой в Microsoft Store необходимо заменить экземпляры **CurrentAppSimulator** на **CurrentApp**. Ваше приложение не пройдет сертификацию, если оно использует **CurrentAppSimulator**.

При использовании **CurrentAppSimulator** начальное состояние лицензирования и внутренних продуктов вашего приложения описано в локальном файле с именем WindowsStoreProxy.xml на вашем компьютере разработчика. Подробнее об этом файле см. в разделе [Использование файла WindowsStoreProxy.xml с CurrentAppSimulator](#proxy).

Дополнительные сведения об общих задачах, которые можно выполнять с помощью **CurrentApp** и **CurrentAppSimulator**, см. в следующих статьях.

| Раздел       | Описание                 |
|----------------------------|-----------------------------|
| [Исключение или ограничение функций в пробной версии](exclude-or-limit-features-in-a-trial-version-of-your-app.md) | Если вы разрешаете пользователям бесплатно пользоваться вашим приложением в течение испытательного срока, можно привлечь их к обновлению до полной версии приложения путем удаления или ограничения некоторых функций в течение пробного периода. |
| [Поддержка покупки продуктов из приложения](enable-in-app-product-purchases.md)      |  Независимо от того, является ли ваше приложение бесплатным или нет, вы можете продавать содержимое, другие приложения или новые функциональные возможности (например, разблокирование следующего уровня игры) прямо из приложения. В этом разделе рассказывается о том, как предоставить возможность совершать такие покупки.  |
| [Поддержка покупок потребляемых внутренних продуктов приложения](enable-consumable-in-app-product-purchases.md)      | Предоставьте пользователям возможность покупки из приложения потребляемых внутренних продуктов приложения (товаров, которые можно покупать, использовать и покупать снова) через Магазин. Покупка из приложения — удобный и надежный способ приобрести товар. Это особенно удобно при покупке виртуальной валюты для игр (например, золота или монет), которую можно потом использовать в процессе игры. |
| [Управление большим каталогом внутренних продуктов приложения](manage-a-large-catalog-of-in-app-products.md)      |   Если ваше приложение предлагает большой каталог продуктов внутри приложения, то для облегчения управления каталогом вы можете выполнить действия, описанные в этом разделе.    |
| [Проверка покупок продуктов с помощью квитанций](use-receipts-to-verify-product-purchases.md)      |   Каждая транзакция Microsoft Store, которая заканчивается успешной покупкой продукта, может дополнительно возвращать пользователю квитанцию транзакции с информацией об указанном продукте и денежных расходах. Доступ к этой информации поддерживает сценарии, в которых ваше приложение должно проверить покупку пользователем приложения или продуктов из приложения в Microsoft Store. |

<span id="proxy" />

## <a name="using-the-windowsstoreproxyxml-file-with-currentappsimulator"></a>Использование файла WindowsStoreProxy.xml с CurrentAppSimulator

При использовании **CurrentAppSimulator** начальное состояние лицензирования и внутренних продуктов вашего приложения описано в локальном файле с именем WindowsStoreProxy.xml на вашем компьютере разработчика. Методы **CurrentAppSimulator**, изменяющие состояние приложения, например путем покупки лицензии или осуществления покупки из приложения, обновляют только состояние объекта **CurrentAppSimulator** в памяти. Содержимое WindowsStoreProxy.xml не изменяется. При очередном запуске приложения состояние лицензии возвращается к описанному в WindowsStoreProxy.xml.

По умолчанию файл WindowsStoreProxy.xml создается в следующем расположении: %UserProfile%\AppData\Local\Packages\\&lt;папка пакета приложения&gt;\LocalState\Microsoft\Windows Store\ApiData. Вы можете изменить этот файл, чтобы определить сценарий, который требуется смоделировать в свойствах **CurrentAppSimulator**.

Хотя вы можете изменять значения в этом файле, мы рекомендуем создать собственный файл WindowsStoreProxy.xml (в папке с данными вашего проекта Visual Studio) для использования классом **CurrentAppSimulator**. При имитации транзакции вызовите [ReloadSimulatorAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentappsimulator.reloadsimulatorasync), чтобы загрузить файл. Если не вызвать **ReloadSimulatorAsync** для загрузки собственного файла WindowsStoreProxy.xml, **CurrentAppSimulator** создаст или загрузит (но не перезапишет) файл WindowsStoreProxy.xml по умолчанию.

> [!NOTE]
> Помните о том, что **CurrentAppSimulator** не инициализируется полностью до завершения **ReloadSimulatorAsync**. А так как **ReloadSimulatorAsync** — асинхронный метод, следует проявлять осторожность, чтобы избежать состояния гонки при запросе **CurrentAppSimulator** в одном потоке, пока он инициализируется в другом. Один из возможных методов состоит в использовании флага для указания завершения инициализации. Приложение, установленное из Microsoft Store, должно использовать **CurrentApp** вместо **CurrentAppSimulator**. В этом случае **ReloadSimulatorAsync** не вызывается, а поэтому упомянутое выше состояние гонки невозможно. По этой причине следует проектировать код так, чтобы он работал в обоих случаях — как синхронно, так и асинхронно.


<span id="proxy-examples" />

### <a name="examples"></a>Примеры

Этот пример представляет собой файл WindowsStoreProxy.xml (с кодировкой UTF-16), описывающий приложение с пробным режимом, срок действия которого истекает в 05:00 (UTC) 19 января 2015 г.

> [!div class="tabbedCodeSnippets"]
```xml
<?xml version="1.0" encoding="UTF-16"?>
<CurrentApp>
  <ListingInformation>
    <App>
      <AppId>2B14D306-D8F8-4066-A45B-0FB3464C67F2</AppId>
      <LinkUri>http://apps.windows.microsoft.com/app/2B14D306-D8F8-4066-A45B-0FB3464C67F2</LinkUri>
      <CurrentMarket>en-US</CurrentMarket>
      <AgeRating>3</AgeRating>
      <MarketData xml:lang="en-us">
        <Name>App with a trial license</Name>
        <Description>Sample app for demonstrating trial license management</Description>
        <Price>4.99</Price>
        <CurrencySymbol>$</CurrencySymbol>
      </MarketData>
    </App>
  </ListingInformation>
  <LicenseInformation>
    <App>
      <IsActive>true</IsActive>
      <IsTrial>true</IsTrial>
      <ExpirationDate>2015-01-19T05:00:00.00Z</ExpirationDate>
    </App>
  </LicenseInformation>
  <Simulation SimulationMode="Automatic">
    <DefaultResponse MethodName="LoadListingInformationAsync_GetResult" HResult="E_FAIL"/>
  </Simulation>
</CurrentApp>
```

Следующий пример— это файл WindowsStoreProxy.xml (с кодировкой UTF-16), описывающий приобретенное приложение, срок действия одной из функций которого истекает в 05:00 (UTC) 19 января 2015 г., и в котором есть потребляемая покупка из приложения.

> [!div class="tabbedCodeSnippets"]
```xml
<?xml version="1.0" encoding="utf-16" ?>
<CurrentApp>
  <ListingInformation>
    <App>
      <AppId>988b90e4-5d4d-4dea-99d0-e423e414ffbc</AppId>
      <LinkUri>http://apps.windows.microsoft.com/app/988b90e4-5d4d-4dea-99d0-e423e414ffbc</LinkUri>
      <CurrentMarket>en-us</CurrentMarket>
      <AgeRating>3</AgeRating>
      <MarketData xml:lang="en-us">
        <Name>App with several in-app products</Name>
        <Description>Sample app for demonstrating an expiring in-app product and a consumable in-app product</Description>
        <Price>5.99</Price>
        <CurrencySymbol>$</CurrencySymbol>
      </MarketData>
    </App>
    <Product ProductId="feature1" LicenseDuration="10" ProductType="Durable">
      <MarketData xml:lang="en-us">
        <Name>Expiring Item</Name>
        <Price>1.99</Price>
        <CurrencySymbol>$</CurrencySymbol>
      </MarketData>
    </Product>
    <Product ProductId="consumable1" LicenseDuration="0" ProductType="Consumable">
      <MarketData xml:lang="en-us">
        <Name>Consumable Item</Name>
        <Price>2.99</Price>
        <CurrencySymbol>$</CurrencySymbol>
      </MarketData>
    </Product>
  </ListingInformation>
  <LicenseInformation>
    <App>
      <IsActive>true</IsActive>
      <IsTrial>false</IsTrial>
    </App>
    <Product ProductId="feature1">
      <IsActive>true</IsActive>
      <ExpirationDate>2015-01-19T00:00:00.00Z</ExpirationDate>
    </Product>
  </LicenseInformation>
  <ConsumableInformation>
    <Product ProductId="consumable1" TransactionId="00000001-0000-0000-0000-000000000000" Status="Active"/>
  </ConsumableInformation>
</CurrentApp>
```


<span id="proxy-schema" />

### <a name="schema"></a>Схема

В этом разделе указан XSD-файл, определяющий структуру файла WindowsStoreProxy.xml. Чтобы применить эту схему к редактору XML в Visual Studio при работе с файлом WindowsStoreProxy.xml, сделайте следующее:

1. Откройте файл WindowsStoreProxy.xml в Visual Studio.
2. В меню **XML** нажмите **Создать схему**. При этом будет создан временный файл WindowsStoreProxy.xsd на основе содержимого XML-файла.
3. Замените содержимое этого XSD-файла схемой ниже.
4. Сохраните файл в таком расположении, откуда его можно применить к нескольким проектам приложений.
5. Переключитесь на ваш файл WindowsStoreProxy.xml в Visual Studio.
6. В меню **XML** нажмите **Схемы** и найдите строку в списке для файла WindowsStoreProxy.xsd. Если указанное расположение для файла не соответствует требуемому (например, если все еще отображается временный файл), нажмите **Добавить**. Перейдите к нужному файлу и нажмите **ОК**. Теперь вы должны увидеть этот файл в списке. Убедитесь, что в столбце **Использовать** для этой схемы появился флажок.

После этого изменения, внесенные в WindowsStoreProxy.xml, будут зависеть от схемы. Дополнительные сведения см. в разделе [Практическое руководство: выбор схем XML для использования](http://go.microsoft.com/fwlink/p/?LinkId=403014).

> [!div class="tabbedCodeSnippets"]
```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema attributeFormDefault="unqualified" elementFormDefault="qualified" xmlns:xs="http://www.w3.org/2001/XMLSchema">
  <xs:import namespace="http://www.w3.org/XML/1998/namespace"/>
  <xs:element name="CurrentApp" type="CurrentAppDefinition"></xs:element>
  <xs:complexType name="CurrentAppDefinition">
    <xs:sequence>
      <xs:element name="ListingInformation" type="ListingDefinition" minOccurs="1" maxOccurs="1"/>
      <xs:element name="LicenseInformation" type="LicenseDefinition" minOccurs="1" maxOccurs="1"/>
      <xs:element name="ConsumableInformation" type="ConsumableDefinition" minOccurs="0" maxOccurs="1"/>
      <xs:element name="Simulation" type="SimulationDefinition" minOccurs="0" maxOccurs="1"/>
    </xs:sequence>
  </xs:complexType>
  <xs:simpleType name="ResponseCodes">
    <xs:restriction base="xs:string">
      <xs:enumeration value="S_OK">
        <xs:annotation>
          <xs:documentation>0x00000000</xs:documentation>
        </xs:annotation>
      </xs:enumeration>
      <xs:enumeration value="E_INVALIDARG">
        <xs:annotation>
          <xs:documentation>0x80070057</xs:documentation>
        </xs:annotation>
      </xs:enumeration>
      <xs:enumeration value="E_CANCELLED">
        <xs:annotation>
          <xs:documentation>0x800704C7</xs:documentation>
        </xs:annotation>
      </xs:enumeration>
      <xs:enumeration value="E_FAIL">
        <xs:annotation>
          <xs:documentation>0x80004005</xs:documentation>
        </xs:annotation>
      </xs:enumeration>
      <xs:enumeration value="E_OUTOFMEMORY">
        <xs:annotation>
          <xs:documentation>0x8007000E</xs:documentation>
        </xs:annotation>
      </xs:enumeration>
      <xs:enumeration value="ERROR_ALREADY_EXISTS">
        <xs:annotation>
          <xs:documentation>0x800700B7</xs:documentation>
        </xs:annotation>
      </xs:enumeration>
    </xs:restriction>
  </xs:simpleType>
  <xs:simpleType name="ConsumableStatus">
    <xs:restriction base="xs:string">
      <xs:enumeration value="Active"/>
      <xs:enumeration value="PurchaseReverted"/>
      <xs:enumeration value="PurchasePending"/>
      <xs:enumeration value="ServerError"/>
    </xs:restriction>
  </xs:simpleType>
  <xs:simpleType name="StoreMethodName">
    <xs:restriction base="xs:string">
      <xs:enumeration value="RequestAppPurchaseAsync_GetResult" id="RPPA"/>
      <xs:enumeration value="RequestProductPurchaseAsync_GetResult" id="RFPA"/>
      <xs:enumeration value="LoadListingInformationAsync_GetResult" id="LLIA"/>
      <xs:enumeration value="ReportConsumableFulfillmentAsync_GetResult" id="RPFA"/>
      <xs:enumeration value="LoadListingInformationByKeywordsAsync_GetResult" id="LLIKA"/>
      <xs:enumeration value="LoadListingInformationByProductIdAsync_GetResult" id="LLIPA"/>
      <xs:enumeration value="GetUnfulfilledConsumablesAsync_GetResult" id="GUC"/>
      <xs:enumeration value="GetAppReceiptAsync_GetResult" id="GARA"/>
    </xs:restriction>
  </xs:simpleType>
  <xs:simpleType name="SimulationMode">
    <xs:restriction base="xs:string">
      <xs:enumeration value="Interactive"/>
      <xs:enumeration value="Automatic"/>
    </xs:restriction>
  </xs:simpleType>
  <xs:complexType name="ListingDefinition">
    <xs:sequence>
      <xs:element name="App" type="AppListingDefinition"/>
      <xs:element name="Product" type="ProductListingDefinition" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>
  <xs:complexType name="ConsumableDefinition">
    <xs:sequence>
      <xs:element name="Product" type="ConsumableProductDefinition" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>
  <xs:complexType name="AppListingDefinition">
    <xs:sequence>
      <xs:element name="AppId" type="xs:string" minOccurs="1" maxOccurs="1"/>
      <xs:element name="LinkUri" type="xs:anyURI" minOccurs="1" maxOccurs="1"/>
      <xs:element name="CurrentMarket" type="xs:language" minOccurs="1" maxOccurs="1"/>
      <xs:element name="AgeRating" type="xs:unsignedInt" minOccurs="1" maxOccurs="1"/>
      <xs:element name="MarketData" type="MarketSpecificAppData" minOccurs="1" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>
  <xs:complexType name="MarketSpecificAppData">
    <xs:sequence>
      <xs:element name="Name" type="xs:string" minOccurs="1" maxOccurs="1"/>
      <xs:element name="Description" type="xs:string" minOccurs="1" maxOccurs="1"/>
      <xs:element name="Price" type="xs:float" minOccurs="1" maxOccurs="1"/>
      <xs:element name="CurrencySymbol" type="xs:string" minOccurs="1" maxOccurs="1"/>
      <xs:element name="CurrencyCode" type="xs:string" minOccurs="0" maxOccurs="1"/>
    </xs:sequence>
    <xs:attribute ref="xml:lang" use="required"/>
  </xs:complexType>
  <xs:complexType name="MarketSpecificProductData">
    <xs:sequence>
      <xs:element name="Name" type="xs:string" minOccurs="1" maxOccurs="1"/>
      <xs:element name="Price" type="xs:float" minOccurs="1" maxOccurs="1"/>
      <xs:element name="CurrencySymbol" type="xs:string" minOccurs="1" maxOccurs="1"/>
      <xs:element name="CurrencyCode" type="xs:string" minOccurs="0" maxOccurs="1"/>
      <xs:element name="Description" type="xs:string" minOccurs="0" maxOccurs="1"/>
      <xs:element name="Tag" type="xs:string" minOccurs="0" maxOccurs="1"/>
      <xs:element name="Keywords" type="KeywordDefinition" minOccurs="0" maxOccurs="1"/>
      <xs:element name="ImageUri" type="xs:anyURI" minOccurs="0" maxOccurs="1"/>
    </xs:sequence>
    <xs:attribute ref="xml:lang" use="required"/>
  </xs:complexType>
  <xs:complexType name="ProductListingDefinition">
    <xs:sequence>
      <xs:element name="MarketData" type="MarketSpecificProductData" minOccurs="1" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="ProductId" use="required">
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:maxLength value="100"/>
          <xs:pattern value="[^,]*"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="LicenseDuration" type="xs:integer" use="optional"/>
    <xs:attribute name="ProductType" type="xs:string" use="optional"/>
  </xs:complexType>
  <xs:simpleType name="guid">
    <xs:restriction base="xs:string">
      <xs:pattern value="[\da-fA-F]{8}-[\da-fA-F]{4}-[\da-fA-F]{4}-[\da-fA-F]{4}-[\da-fA-F]{12}"/>
    </xs:restriction>
  </xs:simpleType>
  <xs:complexType name="ConsumableProductDefinition">
    <xs:attribute name="ProductId" use="required">
      <xs:simpleType>
        <xs:restriction base="xs:string">
          <xs:maxLength value="100"/>
          <xs:pattern value="[^,]*"/>
        </xs:restriction>
      </xs:simpleType>
    </xs:attribute>
    <xs:attribute name="TransactionId" type="guid" use="required"/>
    <xs:attribute name="Status" type="ConsumableStatus" use="required"/>
    <xs:attribute name="OfferId" type="xs:string" use="optional"/>
  </xs:complexType>
  <xs:complexType name="LicenseDefinition">
    <xs:sequence>
      <xs:element name="App" type="AppLicenseDefinition"/>
      <xs:element name="Product" type="ProductLicenseDefinition" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
  </xs:complexType>
  <xs:complexType name="AppLicenseDefinition">
    <xs:sequence>
      <xs:element name="IsActive" type="xs:boolean" minOccurs="1" maxOccurs="1"/>
      <xs:element name="IsTrial" type="xs:boolean" minOccurs="1" maxOccurs="1"/>
      <xs:element name="ExpirationDate" type="xs:dateTime" minOccurs="0" maxOccurs="1"/>
    </xs:sequence>
  </xs:complexType>
  <xs:complexType name="ProductLicenseDefinition">
    <xs:sequence>
      <xs:element name="IsActive" type="xs:boolean" minOccurs="1" maxOccurs="1"/>
      <xs:element name="ExpirationDate" type="xs:dateTime" minOccurs="0" maxOccurs="1"/>
    </xs:sequence>
    <xs:attribute name="ProductId" type="xs:string" use="required"/>
    <xs:attribute name="OfferId" type="xs:string" use="optional"/>
  </xs:complexType>
  <xs:complexType name="SimulationDefinition" >
    <xs:sequence>
      <xs:element name="DefaultResponse" type="DefaultResponseDefinition" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="SimulationMode" type="SimulationMode" use="optional"/>
  </xs:complexType>
  <xs:complexType name="DefaultResponseDefinition">
    <xs:attribute name="MethodName" type="StoreMethodName" use="required"/>
    <xs:attribute name="HResult" type="ResponseCodes" use="required"/>
  </xs:complexType>
  <xs:complexType name="KeywordDefinition">
    <xs:sequence>
      <xs:element name="Keyword" type="xs:string" minOccurs="0" maxOccurs="10"/>
    </xs:sequence>
  </xs:complexType>
</xs:schema>
```


<span id="proxy-descriptions" />

### <a name="element-and-attribute-descriptions"></a>Описание элементов и атрибутов

В этом разделе описываются элементы и атрибуты в файле WindowsStoreProxy.xml.

Корневым элементом этого файла является **CurrentApp**, который представляет текущее приложение. Этот элемент содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  |  Описание   |
|-------------|------------|--------|--------|
|  [ListingInformation](#listinginformation)  |    Да        |  1  |  Содержит данные из описания приложения.            |
|  [LicenseInformation](#licenseinformation)  |     Да       |   1    |   Описывает лицензии, доступные для этого приложения и его надстроек длительного использования.     |
|  [ConsumableInformation](#consumableinformation)  |      Нет      |   0или1   |   Описывает потребляемые надстройки, доступные для этого приложения.      |
|  [Simulation](#simulation)  |     Нет       |      0или1      |   Описывает поведение вызовов различных методов [CurrentAppSimulator](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentappsimulator.aspx) в приложении во время тестирования.    |

<span id="listinginformation" />

#### <a name="listinginformation-element"></a>Элемент ListingInformation

Этот элемент содержит данные из описания приложения. **ListingInformation**— обязательный дочерний элемент элемента **CurrentApp**.

**ListingInformation** содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  |  Описание   |
|-------------|------------|--------|--------|
|  [App](#app-child-of-listinginformation)  |    Да   |  1   |    Предоставляет данные о приложении.         |
|  [Product](#product-child-of-listinginformation)  |    Нет  |  0 или более   |      Описывает надстройку для приложения.     |     |

<span id="app-child-of-listinginformation"/>

#### <a name="app-element-child-of-listinginformation"></a>Элемент приложения (дочерний элемент ListingInformation)

Этот элемент описывает лицензию приложения. **App** является обязательным дочерним элементом элемента [ListingInformation](#listinginformation).

**App** содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  | Описание   |
|-------------|------------|--------|--------|
|  **AppId**  |    Да   |  1   |   GUID, определяющий приложение в Магазине. Это может быть любой GUID для тестирования.        |
|  **LinkUri**  |    Да  |  1   |    Универсальный код ресурса (URI) страницы с описанием в Магазине. Это может быть любой допустимый универсальный код ресурса (URI) для тестирования.         |
|  **CurrentMarket**  |    Да  |  1   |    Страна или регион клиента.         |
|  **AgeRating**  |    Да  |  1   |     Целое число, представляющее минимальную возрастную категорию приложения. Это то же значение, которое вы указываете на информационной панели Центра разработки во время отправки приложения. В Магазине используются значения 3, 7, 12 и 16. Подробнее об этих категориях см. в разделе [Возрастные категории](../publish/age-ratings.md).        |
|  [MarketData](#marketdata-child-of-app)  |    Да  |  1 или более      |    Содержит сведения о приложении в данной стране/регионе. Для каждой страны/региона, в которых зарегистрировано приложение, необходимо указать элемент **MarketData**.       |    |

<span id="marketdata-child-of-app"/>

#### <a name="marketdata-element-child-of-app"></a>Элемент MarketData (дочерний элемент App)

Этот элемент предоставляет сведения о приложении для определенной страны/региона. Для каждой страны/региона, в которых зарегистрировано приложение, необходимо указать элемент **MarketData**. **MarketData** является обязательным дочерним элементом элемента [App](#app-child-of-listinginformation).

**MarketData** содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  | Описание   |
|-------------|------------|--------|--------|
|  **Name**  |    Да   |  1   |   Имя приложения в этой стране/регионе.        |
|  **Description**  |    Да  |  1   |      Описание приложения в этой стране/регионе.       |
|  **Price**  |    Да  |  1   |     Цена приложения в этой стране/регионе.        |
|  **CurrencySymbol**  |    Да  |  1   |     Обозначение денежной единицы, используемой в этой стране/регионе.        |
|  **CurrencyCode**  |    Нет  |  0или1      |      Код валюты, используемой в этой стране/регионе.         |  |

**MarketData** содержит следующие атрибуты.

|  Атрибут  |  Обязательный  |  Описание   |
|-------------|------------|----------------|
|  **xml:lang**  |    Да        |     Указывает страну/регион, к которым применимы данные о рынке.          |  |

<span id="product-child-of-listinginformation"/>

#### <a name="product-element-child-of-listinginformation"></a>Элемент Product (дочерний элемент ListingInformation)

Этот элемент описывает надстройку для приложения. **Product** является необязательным дочерним элементом элемента [ListingInformation](#listinginformation) и содержит один или несколько элементов [MarketData](#marketdata-child-of-product).

**Product** содержит следующие атрибуты.

|  Атрибут  |  Обязательный  |  Описание   |
|-------------|------------|----------------|
|  **ProductId**  |    Да        |    Содержит строку, с помощью которой приложение определяет надстройку.           |
|  **LicenseDuration**  |    Нет        |    Указывает количество дней, в течение которых лицензия будет действительна после покупки товара. Дата истечения срока действия новой лицензии, созданной при покупке продукта, — это дана покупки плюс длительность лицензии. Этот атрибут используется, только если атрибуту **ProductType** задано значение **Durable**; этот атрибут игнорируется для потребляемых надстроек.           |
|  **ProductType**  |    Нет        |    Содержит значение для определения сохраняемости внутреннего продукта приложения. Поддерживаются значения **Durable** (по умолчанию) и **Consumable**. Для товаров длительного пользования дополнительные сведения описываются элементом [Product](#product-child-of-licenseinformation), являющимся дочерним для элемента [LicenseInformation](#licenseinformation); для потребляемых товаров дополнительные сведения описываются элементом [Product](#product-child-of-consumableinformation), являющимся дочерним для элемента [ConsumableInformation](#consumableinformation).           |  |

<span id="marketdata-child-of-product"/>

#### <a name="marketdata-element-child-of-product"></a>Элемент MarketData (дочерний элемент Product)

Этот элемент предоставляет сведения о надстройке для определенной страны/региона. Для каждой страны/региона, в которых зарегистрирована надстройка, необходимо указать элемент **MarketData**. **MarketData** является обязательным дочерним элементом элемента [Product](#product-child-of-listinginformation).

**MarketData** содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  | Описание   |
|-------------|------------|--------|--------|
|  **Name**  |    Да   |  1   |   Имя надстройки в этой стране/регионе.        |
|  **Price**  |    Да  |  1   |     Цена надстройки в этой стране/регионе.        |
|  **CurrencySymbol**  |    Да  |  1   |     Обозначение денежной единицы, используемой в этой стране/регионе.        |
|  **CurrencyCode**  |    Нет  |  0или1      |      Код валюты, используемой в этой стране/регионе.         |  
|  **Description**  |    Нет  |   0или1   |      Описание надстройки в этой стране/регионе.       |
|  **Tag**  |    Нет  |   0или1   |      [Пользовательские данные, определенные разработчиком](../publish/enter-add-on-properties.md#custom-developer-data) (также называются тегом) для надстройки.       |
|  **Keywords**  |    Нет  |   0или1   |      Содержит до 10 элементов **Keyword**, которые содержат [ключевые слова](../publish/enter-add-on-properties.md#keywords) для надстройки.       |
|  **ImageUri**  |    Нет  |   0или1   |      [Универсальной код ресурса (URI) для изображения](../publish/create-add-on-store-listings.md#icon) в описании надстройки.           |  |

**MarketData** содержит следующие атрибуты.

|  Атрибут  |  Обязательный  |  Описание   |
|-------------|------------|----------------|
|  **xml:lang**  |    Да        |     Указывает страну/регион, к которым применимы данные о рынке.          |  |

<span id="licenseinformation"/>

#### <a name="licenseinformation-element"></a>Элемент LicenseInformation

Этот элемент описывает лицензии, доступные для этого приложения и его внутренних продуктов длительного пользования. **LicenseInformation**— обязательный дочерний элемент элемента **CurrentApp**.

**LicenseInformation** содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  | Описание   |
|-------------|------------|--------|--------|
|  [App](#app-child-of-licenseinformation)  |    Да   |  1   |    Описывает лицензию приложения.         |
|  [Product](#product-child-of-licenseinformation)  |    Нет  |  0 или более   |      Описывает состояние лицензии надстройки длительного пользования в приложении.         |   |

В следующей таблице показано, как смоделировать некоторые распространенные условия путем сочетания значений в элементах **App** и **Product**.

|  Имитируемое условие  |  IsActive  |  IsTrial  | ExpirationDate   |
|-------------|------------|--------|--------|
|  Полная лицензия  |    true   |  false  |    Отсутствует. Фактически, может присутствовать и указывать будущую дату, но рекомендуется опустить этот элемент в файле XML. Если он присутствует и указывает прошедшую дату, элемент **IsActive** игнорируется и принимается со значением false.          |
|  Пробный период  |    true  |  true   |      &lt;Дата и время в будущем&gt; Этот элемент должен присутствовать, так как значение элемента **IsTrial** — true. Вы можете посетить веб-сайт, где отображается текущее всемирное координированное время (UTC), чтобы узнать, как далеко в будущем установить этот элемент, чтобы получить требуемый оставшийся пробный период.         |
|  Пробный период истек  |    false  |  true   |      &lt;Дата и время в прошлом&gt; Этот элемент должен присутствовать, так как значение элемента **IsTrial** — true. Вы можете посетить веб-сайт, где отображается текущее всемирное координированное время (UTC), чтобы узнать этот "прошлый" момент в формате UTC.         |
|  Недопустимо  |    false  | false       |     &lt;любое значение или опущен&gt;          |  |

<span id="app-child-of-licenseinformation"/>

#### <a name="app-element-child-of-licenseinformation"></a>Элемент App (дочерний элемент LicenseInformation)

Этот элемент описывает лицензию приложения. **App** является обязательным дочерним элементом элемента [LicenseInformation](#licenseinformation).

**App** содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  | Описание   |
|-------------|------------|--------|--------|
|  **IsActive**  |    Да   |  1   |    Описывает текущее состояние лицензии приложения. Значение **true** указывает, что лицензия действительна; значение **false** указывает, что лицензия недействительна. Обычно используется значение **true** независимо от наличия у приложения пробного периода.  Установите значение **false**, чтобы проверить поведение вашего приложения при недействительной лицензии.           |
|  **IsTrial**  |    Да  |  1   |      Описывает текущее состояние пробного периода приложения. Значение **true** указывает, что приложение используется во время пробного периода; значение **false** указывает, что приложение не находится в пробном периоде, так как либо оно было приобретено, либо пробный период истек.         |
|  **ExpirationDate**  |    Нет  |  0или1       |     Дата истечения пробного периода этого приложения в формате UTC. Дата должна быть выражена следующим образом: гггг-мм-ддTчч:мм:сс.ссZ. Например, время 05:00 19 января 2015г. будет указано как 2015-01-19T05:00:00.00Z. Этот элемент обязателен, если элементу **IsTrial** задано значение **true**. В противном случае он необязателен.          |  |

<span id="product-child-of-licenseinformation"/>

#### <a name="product-element-child-of-licenseinformation"></a>Элемент Product (дочерний элемент LicenseInformation)

Этот элемент описывает состояние лицензии надстройки длительного пользования в приложении. **Product** является необязательным дочерним элементом элемента [LicenseInformation](#licenseinformation).

**Product** содержит следующие дочерние элементы.

|  Элемент  |  Обязательный  |  Количество  | Описание   |
|-------------|------------|--------|--------|
|  **IsActive**  |    Да   |  1     |    Описывает текущее состояние лицензии надстройки. Значение **true** указывает, что надстройку можно использовать; значение **false** указывает, что надстройку нельзя использовать или что она не была приобретена           |
|  **ExpirationDate**  |    Нет   |  0или1     |     Дата истечения срока действия надстройки в формате UTC. Дата должна быть выражена следующим образом: гггг-мм-ддTчч:мм:сс.ссZ. Например, время 05:00 19 января 2015г. будет указано как 2015-01-19T05:00:00.00Z. Если этот элемент присутствует, у надстройки есть дата истечения срока действия. Если его нет, срок действия надстройки неограничен.  |  

**Product** содержит следующие атрибуты.

|  Атрибут  |  Обязательный  |  Описание   |
|-------------|------------|----------------|
|  **ProductId**  |    Да        |   Содержит строку, с помощью которой приложение определяет надстройку.            |
|  **OfferId**  |     Нет       |   Содержит строку, с помощью которой приложение определяет категорию, к которой относится надстройка. Это обеспечивает поддержку больших каталогов элементов, как описано в разделе [Управление большим каталогом внутренних продуктов приложения](manage-a-large-catalog-of-in-app-products.md).           |

<span id="simulation"/>

#### <a name="simulation-element"></a>Элемент Simulation

Этот элемент описывает поведение вызовов различных методов [CurrentAppSimulator](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.store.currentappsimulator.aspx) в приложении во время тестирования. **Simulation** является необязательным дочерним элементом элемента **CurrentApp** и содержит ноль или больше элементов [DefaultResponse](#defaultresponse).

**Simulation** содержит следующие атрибуты.

|  Атрибут  |  Обязательный  |  Описание   |
|-------------|------------|----------------|
|  **SimulationMode**  |    Нет        |      Допустимые значения: **Interactive** или **Automatic**. Если этому атрибуту установить значение **Automatic**, методы автоматически вернут указанные коды ошибок HRESULT. Это можно использовать при выполнении автоматизированных тестов.       |

<span id="defaultresponse"/>

#### <a name="defaultresponse-element"></a>Элемент DefaultResponse

Этот элемент описывает код ошибки по умолчанию, возвращаемый методом **CurrentAppSimulator**. **DefaultResponse** является необязательным дочерним элементом элемента [Simulation](#simulation).

**DefaultResponse** содержит следующие атрибуты.

|  Атрибут  |  Обязательный  |  Описание   |
|-------------|------------|----------------|
|  **MethodName**  |    Да        |   Назначьте этот атрибут одному из значений перечисления, показанных для типа **StoreMethodName** в [схеме](#schema). Каждое из этих значений перечисления представляет метод **CurrentAppSimulator**, для которого требуется смоделировать возвращаемое значение кода ошибки в вашем приложении во время тестирования. Например, значение **RequestAppPurchaseAsync_GetResult** указывает, что вам требуется смоделировать возвращаемое значение кода ошибки метода [RequestAppPurchaseAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store.currentappsimulator.requestapppurchaseasync).            |
|  **HResult**  |     Да       |   Назначьте этот атрибут одному из значений перечисления, показанных для типа **ResponseCodes** в [схеме](#schema). Каждое из этих значений перечисления представляет код ошибки, который требуется вернуть для метода, назначенного атрибуту **MethodName** для этого элемента **DefaultResponse**.           |

<span id="consumableinformation"/>

#### <a name="consumableinformation-element"></a>Элемент ConsumableInformation

Этот элемент описывает потребляемые надстройки, доступные для этого приложения. **ConsumableInformation** является необязательным дочерним элементом элемента **CurrentApp** и может содержать ноль или больше элементов [Product](#product-child-of-consumableinformation).

<span id="product-child-of-consumableinformation"/>

#### <a name="product-element-child-of-consumableinformation"></a>Элемент Product (дочерний элемент ConsumableInformation)

Этот элемент описывает потребляемую надстройку. **Product** является необязательным дочерним элементом элемента [ConsumableInformation](#consumableinformation).

**Product** содержит следующие атрибуты.

|  Атрибут  |  Обязательный  |  Описание   |
|-------------|------------|----------------|
|  **ProductId**  |    Да        |   Содержит строку, с помощью которой приложение определяет потребляемую надстройку.            |
|  **TransactionId**  |     Да       |   Содержит GUID (в виде строки), с помощью которого приложение отслеживает транзакцию покупки потребляемого продукта посредством процесса исполнения. См. раздел [Поддержка покупки потребляемых внутренних продуктов приложений](enable-consumable-in-app-product-purchases.md).            |
|  **Status**  |      Да      |  Содержит строку, с помощью которой приложение указывает состояние исполнения потребляемого продукта. Допустимые значения: **Active**, **PurchaseReverted**, **PurchasePending** или **ServerError**.             |
|  **OfferId**  |     Нет       |    Содержит строку, с помощью которой приложение определяет категорию, к которой относится потребляемый продукт. Это обеспечивает поддержку больших каталогов элементов, как описано в разделе [Управление большим каталогом внутренних продуктов приложения](manage-a-large-catalog-of-in-app-products.md).           |
