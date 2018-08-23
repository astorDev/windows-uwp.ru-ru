---
author: PatrickFarley
ms.assetid: e04ebe3f-479c-4b48-99d8-3dd4bb9bfaf4
title: Подготовка к работе устройства портала с настраиваемой SSL-сертификата
description: TBD
ms.author: pafarley
ms.date: 07/11/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, портал устройства
ms.localizationpriority: medium
ms.openlocfilehash: 1192c200cd42ab28cc7e763c06fd8a5638aa3400
ms.sourcegitcommit: 9c79fdab9039ff592edf7984732d300a14e81d92
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/23/2018
ms.locfileid: "2815246"
---
# <a name="provision-device-portal-with-a-custom-ssl-certificate"></a>Подготовка к работе устройства портала с настраиваемой SSL-сертификата
В обновлении создателей 10 Windows портал устройства Windows добавлен администраторам устройства для установки пользовательских сертификатов для использования в HTTPS-соединений. 

Хотя это можно сделать на персональном Компьютере, эта функция большей части предназначен для предприятий, которые имеют существующей инфраструктуры сертификатов на месте.  

Например компания может иметь сертификации (ЦС), используемый для подписи сертификатов для интрасети веб-сайтов, использующих протокол HTTPS. Эта функция означает на основе этой инфраструктуры. 

## <a name="overview"></a>Обзор
По умолчанию устройства портала создает самозаверяющий корневого центра сертификации, а затем использует, для подписи сертификатов SSL для каждой конечной точки, которую он прослушивает. Этот компонент включает `localhost`, `127.0.0.1`, и `::1` (IPv6 localhost).

Также включены программы имя узла устройства (например, `https://LivingRoomPC`) и каждого локальных IP-адрес устройства (до двух [IPv4, IPv6] каждого сетевого адаптера). Для проверки локальных для канала адресов IP-адресов для устройства средство сети в портале устройства. Их запуска с `10.` или `192.` для IPv4, или `fe80:` для IPv6. 

В программе установки по умолчанию предупреждения о сертификате может отображаться в браузере, из-за недоверенный корневой центр сертификации. В частности SSL-сертификата, автор портала устройства подписано корневой центр сертификации, который не доверять браузера или ПК. Это можно устранить путем создания нового доверенного корневого центра сертификации.

## <a name="create-a-root-ca"></a>Создание корневого ЦС

Это должно выполняться только если вашей компании (или домашней) не содержит сертификат инфраструктуры, Настройка и может выполняться только один раз. Приведенный ниже сценарий PowerShell создает корневого центра сертификации, называется _WdpTestCA.cer_. Установка этого файла на локальный компьютер доверенные корневые центры сертификации будет отображено устройства, чтобы доверять сертификаты SSL, которые должны быть подписаны корневой ЦС. Можно (и следует установки этот CER-файл на каждый компьютер, чтобы подключиться к порталу устройств Windows.  

```PowerShell
$CN = "PickAName"
$OutputPath = "c:\temp\"

# Create root certificate authority
$FilePath = $OutputPath + "WdpTestCA.cer"
$Subject =  "CN="+$CN
$rootCA = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -Subject $Subject -HashAlgorithm "SHA512" -KeyUsage CertSign,CRLSign
$rootCAFile = Export-Certificate -Cert $rootCA -FilePath $FilePath
```

После создания этого файла _WdpTestCA.cer_ можно использовать для подписи сертификатов SSL. 

## <a name="create-an-ssl-certificate-with-the-root-ca"></a>Создание SSL-сертификата с корневого ЦС

SSL-сертификаты имеют две важные функции: обеспечение безопасности подключения через шифрования и убедившись, что Вы действительно общаетесь адрес, отображаемый в строке браузера (помощью Bing.com, 192.168.1.37, и т.д.) и не вредоносных сторонних производителей.

Приведенный ниже сценарий PowerShell создает сертификат SSL для `localhost` конечной точки. Каждой конечной точки, который прослушивает портала устройства требуется собственный сертификат; можно заменить `$IssuedTo` аргумент в сценарии с каждым из разных конечных точек для устройства: имя узла, localhost и IP-адреса.

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

Если у вас есть несколько устройств, можно повторно использовать файлы localhost PFX-файл, но по-прежнему вам потребуется создать сертификаты IP-адрес и имя узла для каждого устройства отдельно.

При создании пакета файлы PFX-файл, необходимо загрузить их в портал устройств Windows. 

## <a name="provision-device-portal-with-the-certifications"></a>Подготовка к работе устройств портала с certification(s)

Для каждого PFX-файл, который вы создали для устройства вам потребуется выполните следующую команду из командной строки с повышенными привилегиями.

```
WebManagement.exe -SetCert <Path to .pfx file> <password for pfx> 
```

Просмотрите ниже пример использования:
```
WebManagement.exe -SetCert localhost.pfx PickAPassword
WebManagement.exe -SetCert --1.pfx PickAPassword
WebManagement.exe -SetCert MyLivingRoomPC.pfx PickAPassword
```

После завершения установки сертификатов просто перезапустите службу, чтобы изменения вступили в силу.

```
sc stop webmanagement
sc start webmanagement
```

> [!TIP]
> IP-адреса могут изменяться.
Во многих сетях использование протокола DHCP для IP-адресов, поэтому устройства не всегда получайте же IP-адрес, который они использовался ранее. Если вы создали сертификат для IP-адреса на устройстве, измененного адрес устройства портала устройства Windows создаст новый сертификат, с помощью существующего самозаверяющего сертификата и остановит с помощью то, что вы создали. Это приведет к странице cert предупреждение может выглядеть в обозревателе. По этой причине корпорация Майкрософт рекомендует подключение устройств через их имена узлов, которую можно установить на портале устройства. Они будут не изменяются вне зависимости от IP-адресов.
