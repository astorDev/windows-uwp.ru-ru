---
ms.assetid: e04ebe3f-479c-4b48-99d8-3dd4bb9bfaf4
title: Подготовка портала устройств с использованием пользовательского SSL-сертификата
description: Узнайте, как подготовить портал устройств Windows с помощью пользовательского сертификата для использования в обмене данными по протоколу HTTPS.
ms.date: 04/19/2019
ms.topic: article
keywords: Windows 10, UWP, портал устройств
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: ce4e45bc23f4efb636618bb4891b9d6e9d207490
ms.sourcegitcommit: fca0132794ec187e90b2ebdad862f22d9f6c0db8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63774147"
---
# <a name="provision-device-portal-with-a-custom-ssl-certificate"></a>Подготовка портала устройств с использованием пользовательского SSL-сертификата

В Windows 10 Creators Update на портале устройств Windows для администраторов устройств появилась возможность устанавливать пользовательский сертификат для обмена данными по протоколу HTTPS.

Хотя вы можете сделать это и на собственном компьютере, данная возможность предназначена в первую очередь для предприятий с уже развернутой инфраструктурой сертификатов.  

Например, в компании может быть центр сертификации (ЦС) для подписи сертификатов для веб-сайтов интрасети, обслуживаемых по протоколу HTTPS. Эта возможность основана на данной инфраструктуре.

## <a name="overview"></a>Обзор

По умолчанию портал устройств создает самозаверяющий сертификат корневого ЦС, а затем использует его для подписывания SSL-сертификатов для каждой конечной точки, на которой ожидается передача данных. К ним относятся `localhost`, `127.0.0.1` и `::1` (localhost IPv6).

Также сюда входят имя узла устройства (например, `https://LivingRoomPC`) и каждый IP-адрес локальной связи, назначенный устройству (до двух [IPv4, IPv6] на сетевой адаптер).
IP-адреса локальной связи устройства можно просмотреть с помощью инструмента "Сеть" на портале устройств. Они начинаются с `10.` или `192.` для IPv4 или с `fe80:` для IPv6.

В конфигурации по умолчанию в браузере может появиться предупреждение о сертификате из-за недоверенного корневого ЦС. В частности, SSL-сертификат, предоставленный порталом устройств, подписан корневым ЦС, которому не доверяет браузер или компьютер. Это можно исправить, создав новый доверенный корневой ЦС.

## <a name="create-a-root-ca"></a>Создание корневого ЦС

Это следует сделать однократно и только в том случае, если в вашей компании (или у вас дома) не настроена инфраструктура сертификатов. Следующий сценарий PowerShell создает коневой ЦС с именем _WdpTestCA.cer_. После установки этого файла в доверенном корневом центре сертификации на локальном компьютере ваше устройство начнет доверять SSL-сертификатам, подписанным этим корневым ЦС. Вы можете (и вам следует) установить этот CER-файл на каждом компьютере, который должен подключаться к порталу устройств Windows.  

```PowerShell
$CN = "PickAName"
$OutputPath = "c:\temp\"

# Create root certificate authority
$FilePath = $OutputPath + "WdpTestCA.cer"
$Subject =  "CN="+$CN
$rootCA = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -Subject $Subject -HashAlgorithm "SHA512" -KeyUsage CertSign,CRLSign
$rootCAFile = Export-Certificate -Cert $rootCA -FilePath $FilePath
```

После создания этого ЦС можно использовать файл _WdpTestCA.cer_ для подписывания SSL-сертификатов.

## <a name="create-an-ssl-certificate-with-the-root-ca"></a>Создание SSL-сертификата с помощью корневого ЦС

SSL-сертификаты выполняют две важные функции: защищают подключения путем шифрования, а также обеспечивают обмен данными именно с адресом, отображаемым в строке браузера (Bing.com, 192.168.1.37 и т. д.), а не со злоумышленником.

Следующий сценарий PowerShell создает SSL-сертификат для конечной точки `localhost`. Каждой конечной точке, на которой портал устройств ожидает передачи данных, нужен собственный сертификат. Вы можете заменить аргумент `$IssuedTo` в сценарии каждой из различных конечных точек для своего устройства, указав имя узла, localhost или IP-адреса.

```PowerShell
$IssuedTo = "localhost"
$Password = "PickAPassword"
$OutputPath = "c:\temp\"
$rootCA = Import-Certificate -FilePath C:\temp\WdpTestCA.cer -CertStoreLocation Cert:\CurrentUser\My\

# Create SSL cert signed by certificate authority
$IssuedToClean = $IssuedTo.Replace(":", "-").Replace(" ", "_")
$FilePath = $OutputPath + $IssuedToClean + ".pfx"
$Subject = "CN="+$IssuedTo
$cert = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -Subject $Subject -DnsName $IssuedTo -Signer $rootCA -HashAlgorithm "SHA512"
$certFile = Export-PfxCertificate -cert $cert -FilePath $FilePath -Password (ConvertTo-SecureString -String $Password -Force -AsPlainText)
```

Если у вас есть несколько устройств, можно использовать PFX-файлы многократно, но для каждого устройства по-прежнему необходимо создать сертификаты на основе IP-адресов и имен узла.

После создания пакета PFX-файлов их необходимо загрузить на портал устройств Windows.

## <a name="provision-device-portal-with-the-certifications"></a>Подготовка портала устройств с использованием сертификатов

Для каждого PFX-файла, созданного для устройства, в командной строке с повышенными привилегиями необходимо выполнить следующую команду.

```cmd
WebManagement.exe -SetCert <Path to .pfx file> <password for pfx>
```

Пример использования приведен ниже.

```cmd
WebManagement.exe -SetCert localhost.pfx PickAPassword
WebManagement.exe -SetCert --1.pfx PickAPassword
WebManagement.exe -SetCert MyLivingRoomPC.pfx PickAPassword
```

После установки сертификатов перезапустите службу, чтобы изменения вступили в силу.

```cmd
sc stop webmanagement
sc start webmanagement
```

> [!TIP]
> IP-адреса могут со временем меняться.
Во многих сетях для выдачи IP-адресов используется протокол DHCP, поэтому устройства не всегда получают те же IP-адреса, что были у них ранее. Если вы создали сертификат для IP-адреса устройства, а адрес этого устройства изменился, портал устройств Windows создаст новый сертификат с использованием существующего самозаверяющего сертификата и прекратит использовать сертификат, созданный вами. Из-за этого в браузере снова отобразится страница с предупреждением о сертификате. По этой причине мы рекомендуем подключаться к устройствам с помощью их имен узла, которые можно установить на портале устройств. Эти имена остаются неизменными независимо от IP-адресов.
