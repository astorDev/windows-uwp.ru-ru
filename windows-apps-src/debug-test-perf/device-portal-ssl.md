---
author: PatrickFarley
ms.assetid: e04ebe3f-479c-4b48-99d8-3dd4bb9bfaf4
title: Подготовка портала устройств с использованием пользовательского сертификата SSL
description: TBD
ms.author: pafarley
ms.date: 07/11/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, портала устройств
ms.localizationpriority: medium
ms.openlocfilehash: 1192c200cd42ab28cc7e763c06fd8a5638aa3400
ms.sourcegitcommit: 3727445c1d6374401b867c78e4ff8b07d92b7adc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2018
ms.locfileid: "2916761"
---
# <a name="provision-device-portal-with-a-custom-ssl-certificate"></a>Подготовка портала устройств с использованием пользовательского сертификата SSL
В Windows 10 Creators Update портал устройств Windows добавлены способ для администраторов устройства для установки пользовательского сертификата для использования в связи с HTTPS. 

Хотя это можно сделать на вашем Компьютере, эту функцию в основном предназначен для предприятий с существующей инфраструктуры сертификат на месте.  

Например компания может иметь сертификации (ЦС), который используется для подписания сертификатов для веб-сайтов интрасети отображаться по протоколу HTTPS. Эта функция поверх обозначен инфраструктуры. 

## <a name="overview"></a>Обзор
По умолчанию портала устройств создает самозаверяющий корневого центра сертификации и затем использует эти данные для подписания сертификатов SSL для каждой конечной точки, в которых она ожидает на. Сюда входят `localhost`, `127.0.0.1`, и `::1` (IPv6 localhost).

Также включены имени узла устройства (например, `https://LivingRoomPC`) и каждый локальных IP-адрес, назначенный устройству (до двух [IPv4, IPv6] каждого сетевого адаптера). Вы видите локальных IP-адреса на устройстве с рассмотрения средство сетевого взаимодействия в портале устройств. Они начнем с `10.` или `192.` для IPv4, или `fe80:` для IPv6. 

Настройки по умолчанию предупреждение о сертификате может отображаться в браузере, из-за ненадежных корневого центра сертификации. В частности SSL-сертификата, предоставляемого портала устройства подписан корневого центра сертификации, браузер или компьютер не доверяете. Это можно исправить, создав новый доверенному корневому ЦС.

## <a name="create-a-root-ca"></a>Создание корневого центра сертификации

Это может выполняться только если у вашей компании (или home) нет инфраструктуры сертификата, Настройка и может выполняться только один раз. Следующий сценарий PowerShell создает корневого центра сертификации, под названием _WdpTestCA.cer_. Установка этого файла на локальном компьютере доверенных корневых центров сертификации приведет к устройства для доверия сертификаты SSL, подписанные этим корневой центр сертификации. Можно (и следует) установкой этого CER-файл на каждом Компьютере, который вы хотите подключиться к порталу устройств Windows.  

```PowerShell
$CN = "PickAName"
$OutputPath = "c:\temp\"

# Create root certificate authority
$FilePath = $OutputPath + "WdpTestCA.cer"
$Subject =  "CN="+$CN
$rootCA = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -Subject $Subject -HashAlgorithm "SHA512" -KeyUsage CertSign,CRLSign
$rootCAFile = Export-Certificate -Cert $rootCA -FilePath $FilePath
```

После создания этот файл _WdpTestCA.cer_ можно использовать для входа сертификаты SSL. 

## <a name="create-an-ssl-certificate-with-the-root-ca"></a>Создание SSL-сертификат с корневого центра сертификации

Сертификаты SSL имеют две важные функции: защита подключения через шифрования и убедитесь, что вы фактически общаетесь с адресом отображается на панели обозревателя (Bing.com, 192.168.1.37, и т.д.) и не вредоносных сторонних производителей.

Следующий сценарий PowerShell создает SSL-сертификата для `localhost` конечной точки. Каждая конечная точка, которая прослушивает портала устройств должно свой собственный сертификат; Вы можете заменить `$IssuedTo` аргументов в сценарии с каждым из различных конечных точек для вашего устройства: имя узла, localhost, IP-адреса серверов.

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

Если имеется несколько устройств, вы можете повторно использовать файлы localhost PFX-файл, но по-прежнему необходимо создавать сертификаты адрес и имя узла IP-адрес для каждого устройства по отдельности.

При создании пакета PFX-файлах, необходимо загрузить их в портал устройств Windows. 

## <a name="provision-device-portal-with-the-certifications"></a>Подготовка портала устройств с использованием certification(s)

Для каждого PFX-файл, созданный на устройстве вам потребуется выполните следующую команду из командной строки с повышенными привилегиями.

```
WebManagement.exe -SetCert <Path to .pfx file> <password for pfx> 
```

См. ниже пример использования:
```
WebManagement.exe -SetCert localhost.pfx PickAPassword
WebManagement.exe -SetCert --1.pfx PickAPassword
WebManagement.exe -SetCert MyLivingRoomPC.pfx PickAPassword
```

После установки сертификатов, просто перезагрузите службу, чтобы изменения вступили в силу:

```
sc stop webmanagement
sc start webmanagement
```

> [!TIP]
> IP-адреса может изменяться со временем.
Многие сети, используют DHCP для IP-адреса, чтобы устройства не всегда получают одного и того же IP-адрес, на котором они были ранее. Если вы создали сертификата для IP-адреса на устройстве и измененного адрес устройства, портал устройств Windows будет создан новый сертификат с помощью существующих самозаверяющий сертификат, и завершится с помощью то, что вы создали. Это приведет к странице cert предупреждение вновь появится в браузере. По этой причине рекомендуется подключение к устройствам через их имена узлов, которое можно задать в портале устройств. Это остается неизменным независимо от IP-адреса.
