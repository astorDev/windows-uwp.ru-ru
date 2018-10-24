---
author: laurenhughes
title: Создание сертификата для подписывания пакета
description: Создавайте и экспортируйте сертификат для пакета приложения, подписывая его с использованием инструментов PowerShell.
ms.author: lahugh
ms.date: 09/30/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 7bc2006f-fc5a-4ff6-b573-60933882caf8
ms.localizationpriority: medium
ms.openlocfilehash: 448b4405381c3100f8b92abd0f2889799c0354fc
ms.sourcegitcommit: 4b97117d3aff38db89d560502a3c372f12bb6ed5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "5439107"
---
# <a name="create-a-certificate-for-package-signing"></a>Создание сертификата для подписывания пакета


В этой статье объясняется, как создать и экспортировать сертификат для подписывания пакета приложений с помощью инструментов PowerShell. Рекомендуется использовать Visual Studio для [упаковки приложений UWP](https://msdn.microsoft.com/windows/uwp/packaging/packaging-uwp-apps), однако готовые к публикации в Магазине приложения можно упаковать вручную, если для их разработки не использовалась среда Visual Studio.

> [!IMPORTANT] 
> Если для разработки приложения использовали Visual Studio, рекомендуется применить мастер Visual Studio, чтобы импортировать сертификат и подписать пакет приложения. Дополнительные сведения см. в разделе [Упаковка приложения UWP с помощью Visual Studio](https://msdn.microsoft.com/windows/uwp/packaging/packaging-uwp-apps).

## <a name="prerequisites"></a>Предварительные условия

- **Упакованное или распакованное приложение**  
Приложение, содержащее файл AppxManifest.xml. При создании сертификата, который будет использоваться для подписывания окончательного пакета приложений, потребуется сослаться на этот файл манифеста. Сведения о том, как вручную упаковать приложение, см. в разделе [Создание пакета приложений с помощью инструмента MakeAppx.exe](https://msdn.microsoft.com/windows/uwp/packaging/create-app-package-with-makeappx-tool).

- **Командлеты инфраструктуры открытых ключей (PKI)**  
Вам потребуются командлеты PKI для создания и экспорта сертификата для подписи. Дополнительные сведения см. в разделе [Командлеты инфраструктуры открытых ключей](https://docs.microsoft.com/powershell/module/pkiclient/).

## <a name="create-a-self-signed-certificate"></a>Создание самозаверяющего сертификата

Самозаверяющий сертификат полезен для тестирования приложения, прежде чем вы будете готовы опубликовать его в магазине. Выполните описанные в этом разделе действия,чтобы создать самозаверяющий сертификат.

### <a name="determine-the-subject-of-your-packaged-app"></a>Определение субъекта вашего упакованного приложения  

Чтобы использовать сертификат для подписывания пакета приложений, значение в поле сертификата "Субъект" **должно** совпадать с данными в разделе "Издатель" манифеста вашего приложения.

Например, раздел "Идентификатор" файла AppxManifest.xml вашего приложения должен выглядеть примерно следующим образом:
```
  <Identity Name="Contoso.AssetTracker" 
    Version="1.0.0.0" 
    Publisher="CN=Contoso Software, O=Contoso Corporation, C=US"/>
```

"Издатель" в данном случае— "CN=Contoso Software, O=Contoso Corporation, C=US", и именно его следует использовать для создания сертификата. 

### <a name="use-new-selfsignedcertificate-to-create-a-certificate"></a>Использование командлета **New-SelfSignedCertificate** для создания сертификата
Используйте командлет PowerShell **New-SelfSignedCertificate** для создания самозаверяющего сертификата. Командлет **New-SelfSignedCertificate** имеет несколько параметров для настройки, однако в этой статье мы сконцентрируемся на создании простого сертификата, который будет работать вместе с **SignTool**. Дополнительные примеры и варианты использования этого командлета см. в статье [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/New-SelfSignedCertificate).

Основываясь на файле AppxManifest.xml из предыдущего примера, используйте следующий синтаксис для создания сертификата. В строке с повышенными привилегиями PowerShell:
```
New-SelfSignedCertificate -Type Custom -Subject "CN=Contoso Software, O=Contoso Corporation, C=US" -KeyUsage DigitalSignature -FriendlyName <Your Friendly Name> -CertStoreLocation "Cert:\LocalMachine\My"
```

После выполнения этой команды сертификат будет добавлен в локальное хранилище сертификатов, как указано в параметре "-CertStoreLocation". Результат выполнения команды будет также создан отпечаток сертификата.  

**Примечание.**  
Сертификат можно просмотреть в окне PowerShell, выполнив следующие команды:
```
Set-Location Cert:\LocalMachine\My
Get-ChildItem | Format-Table Subject, FriendlyName, Thumbprint
```
Это позволит отобразить все сертификаты в вашем локальном хранилище.

## <a name="export-a-certificate"></a>Экспорт сертификата 

Чтобы экспортировать сертификат из локального хранилища в файл обмена личной информацией (PFX), используйте командлет **Export-PfxCertificate**.

При использовании командлета **Export-PfxCertificate** следует либо создать и использовать пароль, либо с помощью параметра "-ProtectTo" указать, какие пользователи или группы могут осуществлять доступ к файлу без пароля. Обратите внимание, что если вы не используете параметр "-Password" или "-ProtectTo", отобразится ошибка.

- **Использование пароля**
```
$pwd = ConvertTo-SecureString -String <Your Password> -Force -AsPlainText 
Export-PfxCertificate -cert "Cert:\LocalMachine\My\<Certificate Thumbprint>" -FilePath <FilePath>.pfx -Password $pwd
```

- **Использование ProtectTo**
```
Export-PfxCertificate -cert Cert:\LocalMachine\My\<Certificate Thumbprint> -FilePath <FilePath>.pfx -ProtectTo <Username or group name>
```

После создания и экспорт сертификата вы готовы подписать пакет приложений с помощью **SignTool**. Следующий шаг в процессе упаковки вручную описан в разделе [Подпись пакета приложения с использованием инструмента SignTool](https://msdn.microsoft.com/windows/uwp/packaging/sign-app-package-using-signtool).

## <a name="security-considerations"></a>Вопросы обеспечения безопасности 
Добавив сертификат в [хранилища сертификатов локальной машины](https://msdn.microsoft.com/windows/hardware/drivers/install/local-machine-and-current-user-certificate-stores), вы меняете доверие сертификатов всех пользователей на компьютере. Рекомендуется удалить эти сертификаты, когда они больше не требуется, чтобы их невозможно было использовать для нарушения доверия системы.
