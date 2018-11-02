---
author: PatrickFarley
ms.assetid: e04ebe3f-479c-4b48-99d8-3dd4bb9bfaf4
title: Подготовка портала устройств с использованием пользовательского сертификата SSL
description: TBD
ms.author: pafarley
ms.date: 07/11/2017
ms.topic: article
keywords: Windows 10, uwp, портала устройств
ms.localizationpriority: medium
ms.openlocfilehash: 525c64ab289d26a4835168f410ac4ba3fc14343a
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/02/2018
ms.locfileid: "5945708"
---
# <a name="provision-device-portal-with-a-custom-ssl-certificate"></a>Подготовка портала устройств с использованием пользовательского сертификата SSL
В Windows 10 Creators Update портал устройств Windows добавлены способ для администраторов устройства для установки пользовательского сертификата для использования в связи с HTTPS. 

Хотя это можно сделать на вашем Компьютере, эта возможность в основном предназначена для предприятий, которые у вас есть инфраструктура сертификата на месте.  

Например компания может иметь сертификации (ЦС), который используется для подписания сертификатов для веб-сайтов интрасети отображаться по протоколу HTTPS. Эта функция поверх обозначен инфраструктуры. 

## <a name="overview"></a>Обзор
По умолчанию портала устройств создает самозаверяющий корневого центра сертификации и использует его для подписания сертификатов SSL для каждой конечной точки, которую он прослушивает. Сюда входят `localhost`, `127.0.0.1`, и `::1` (IPv6 localhost).

Также включены имени узла устройства (например, `https://LivingRoomPC`) и каждый локальных IP-адрес, назначенный устройству (до двух [IPv4, IPv6] каждого сетевого адаптера). Вы видите локальных IP-адреса для устройства с рассмотрения средство сеть портала устройств. Они начнем с `10.` или `192.` для IPv4, или `fe80:` для IPv6. 

Настройки по умолчанию предупреждение о сертификате может отображаться в браузере, из-за ненадежных корневого центра сертификации. В частности SSL-сертификата, предоставляемого портала устройства подписан корневого центра сертификации, который браузера или компьютер не является доверенным. Это можно исправить, создав новый доверенному корневому ЦС.

## <a name="create-a-root-ca"></a>Создание корневого центра сертификации

Это может выполняться только если у вашей компании (или home) нет инфраструктуры сертификата, Настройка и может выполняться только один раз. Следующий сценарий PowerShell создает корневого центра сертификации, под названием _WdpTestCA.cer_. Установка этого файла на локальном компьютере доверенные корневые центры сертификации приведет к устройства для доверия сертификаты SSL, подписанные этим корневой центр сертификации. Можно (и следует) установки этого CER-файл на каждом Компьютере, который вы хотите подключиться к порталу устройств Windows.  

```PowerShell
$CN = "PickAName"
$OutputPath = "c:\temp\"

# Create root certificate authority
$FilePath = $OutputPath + "WdpTestCA.cer"
$Subject =  "CN="+$CN
$rootCA = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -Subject $Subject -HashAlgorithm "SHA512" -KeyUsage CertSign,CRLSign
$rootCAFile = Export-Certificate -Cert $rootCA -FilePath $FilePath
```

После создания этого можно использовать файл _WdpTestCA.cer_ для входа сертификаты SSL. 

## <a name="create-an-ssl-certificate-with-the-root-ca"></a>Создание SSL-сертификат с корневого центра сертификации

Сертификаты SSL имеют две важные функции: защита подключения через шифрования и убедиться, что вы фактически общаетесь с адресом, отображается на панели обозревателя (Bing.com, 192.168.1.37, и т.д.) и не вредоносных сторонних производителей.

Следующий сценарий PowerShell создает SSL-сертификат для `localhost` конечной точки. Каждая конечная точка, которая прослушивает портала устройства должно свой собственный сертификат; Вы можете заменить `$IssuedTo` аргументов в сценарии с каждым различные конечные точки для вашего устройства: имя узла, localhost и IP-адреса.

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

Если у вас есть несколько устройств, вы можете повторно использовать файлы PFX-файл localhost, но по-прежнему необходимо создавать сертификаты адрес и имя узла IP-адрес для каждого устройства по отдельности.

Когда создается пакет файлов PFX-файл, необходимо загрузить их в портал устройств Windows. 

## <a name="provision-device-portal-with-the-certifications"></a>Подготовка портала устройств с помощью certification(s)

Для каждого PFX-файл, созданный на устройстве вам потребуется выполнить следующую команду из командной строки с повышенными привилегиями.

```
WebManagement.exe -SetCert <Path to .pfx file> <password for pfx> 
```

См. ниже пример использования:
```
WebManagement.exe -SetCert localhost.pfx PickAPassword
WebManagement.exe -SetCert --1.pfx PickAPassword
WebManagement.exe -SetCert MyLivingRoomPC.pfx PickAPassword
```

После установки сертификатов просто перезагрузите службу, чтобы изменения вступили в силу:

```
sc stop webmanagement
sc start webmanagement
```

> [!TIP]
> IP-адреса может изменяться со временем.
Многие сети используют DHCP для разглашать IP-адреса, чтобы устройства не всегда же IP-адрес, на котором они были ранее. Если вы создали сертификата для IP-адреса на устройстве и измененного адрес устройства, портал устройств Windows будет создан новый сертификат с помощью существующих самозаверяющий сертификат, и завершится с помощью то, что вы создали. Это приведет к страницу предупреждения cert вновь появится в браузере. По этой причине рекомендуется подключение к устройствам с помощью их имена узлов, которое можно задать в портале устройств. Это остается неизменным независимо от IP-адреса.
