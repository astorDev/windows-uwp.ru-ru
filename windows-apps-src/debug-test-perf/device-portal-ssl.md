---
ms.assetid: e04ebe3f-479c-4b48-99d8-3dd4bb9bfaf4
title: Подготовка портала устройств с использованием пользовательского сертификата SSL
description: Сведения о подготовке Windows Device Portal настраиваемого сертификата для использования в связи по протоколу HTTPS.
ms.date: 04/19/2019
ms.topic: article
keywords: Windows 10, uwp, портал устройств
ms.localizationpriority: medium
ms.custom: 19H1
ms.openlocfilehash: ce4e45bc23f4efb636618bb4891b9d6e9d207490
ms.sourcegitcommit: fca0132794ec187e90b2ebdad862f22d9f6c0db8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63774147"
---
# <a name="provision-device-portal-with-a-custom-ssl-certificate"></a>Подготовка портала устройств с использованием пользовательского сертификата SSL

В Windows 10 Creators Update на портале Windows Device Portal появилась возможность для администраторов устройств устанавливать пользовательский сертификат для подключения по протоколу HTTPS.

Хотя вы можете сделать это и на собственном компьютере, данная возможность предназначена в первую очередь для предприятий с уже развернутой инфраструктурой сертификатов.  

Например, в компании может быть центр сертификации (ЦС) для подписи сертификатов для веб-сайтов интрасети, предоставляемых по протоколу HTTPS. Эта возможность основана на данной инфраструктуре.

## <a name="overview"></a>Обзор

По умолчанию портал устройств создает самозаверяющий сертификат корневого ЦС, а затем использует его для подписи сертификатов SSL для каждой прослушиваемой конечной точки. К ним относятся `localhost`, `127.0.0.1` и `::1` (локальный узел IPv6).

Также сюда входят имя узла устройства (например, `https://LivingRoomPC`) и каждый IP-адрес локальной связи, назначенный устройству (до двух [IPv4, IPv6] на сетевой адаптер).
IP-адреса локальной связи устройства можно просмотреть с помощью сетевого средства на портале устройств. Они начинаются с `10.` или `192.` для IPv4 или с `fe80:` для IPv6.

В конфигурации по умолчанию в браузере может появиться предупреждение о сертификате из-за недоверенного корневого ЦС. В частности, сертификат SSL, предоставленный порталом устройств, подписан корневым ЦС, которому не доверяет браузер или компьютер. Это можно исправить путем создания нового доверенного корневого ЦС.

## <a name="create-a-root-ca"></a>Создание корневого ЦС

Это следует сделать однократно и только в том случае, если в вашей компании (или у вас дома) не настроена инфраструктура сертификатов. Следующий сценарий PowerShell создает коневой ЦС с именем _WdpTestCA.cer_. После установки этого файла в доверенном корневом центре сертификации на локальном компьютере ваше устройство начнет доверять сертификатам SSL, подписанным этим корневым ЦС. Вы можете (и вам следует) установить этот CER-файл на каждом компьютере, который должен подключаться к порталу Windows Device Portal.  

```PowerShell
$CN = "PickAName"
$OutputPath = "c:\temp\"

# Create root certificate authority
$FilePath = $OutputPath + "WdpTestCA.cer"
$Subject =  "CN="+$CN
$rootCA = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -Subject $Subject -HashAlgorithm "SHA512" -KeyUsage CertSign,CRLSign
$rootCAFile = Export-Certificate -Cert $rootCA -FilePath $FilePath
```

После создания этого ЦС можно использовать файл _WdpTestCA.cer_ для подписи сертификатов SSL.

## <a name="create-an-ssl-certificate-with-the-root-ca"></a>Создание сертификата SSL с помощью корневого ЦС

SSL сертификаты выполняют две важные функции: защита подключения путем шифрования, а также обеспечение обмена данными именно с адресом, отображаемым в строке браузера (Bing.com, 192.168.1.37 и т. д.), а не со злоумышленником.

Следующий сценарий PowerShell создает сертификат SSL для конечной точки `localhost`. Каждой конечной точке, которую прослушивает портал устройств, нужен собственный сертификат. Вы можете заменить аргумент `$IssuedTo` в сценарии каждой из различных конечных точек для вашего устройства: именем узла, локальным узлом и IP-адресами.

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

После создания пакета PFX-файлов их необходимо загрузить на портал Windows Device Portal.

## <a name="provision-device-portal-with-the-certifications"></a>Подготовка портала устройств с использованием сертификатов

Для каждого PFX-файла, созданного для устройства, необходимо выполнить следующую команду через командную строку с повышенными привилегиями.

```cmd
WebManagement.exe -SetCert <Path to .pfx file> <password for pfx>
```

Пример использования см. ниже.

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
Во многих сетях для выдачи IP-адресов используется протокол DHCP, поэтому устройства не всегда получают те же IP-адреса, что были у них ранее. Если вы создали сертификат для IP-адреса на устройстве, а адрес этого устройства изменился, портал Windows Device Portal создаст новый сертификат с использованием существующего самозаверяющего сертификата и прекратит использовать созданный вами сертификат. Из-за этого в браузере снова отобразится страница с предупреждением о сертификате. По этой причине мы рекомендуем подключаться к устройствам с помощью их имен узла, которые можно установить на портале устройств. Эти имена остаются неизменными независимо от IP-адресов.
