---
author: laurenhughes
title: Установка связанного набора с помощью файла Установщика приложений
description: В этом разделе описываются действия, которые необходимо выполнить, чтобы обеспечить установку связанного набора с помощью Установщика приложений. Также здесь рассматриваются шаги по созданию файла *.appinstaller, определяющего связанный набор.
ms.author: lahugh
ms.date: 1/4/2018
ms.topic: article
keywords: windows 10, uwp, установщик приложений, AppInstaller, загрузка неопубликованных приложений, связанный набор, дополнительные пакеты
ms.localizationpriority: medium
ms.openlocfilehash: 4caf4333bb3d442779aedac2028b0996cbd17645
ms.sourcegitcommit: cd00bb829306871e5103db481cf224ea7fb613f0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "5883622"
---
# <a name="install-a-related-set-using-an-app-installer-file"></a>Установка связанного набора с помощью файла Установщика приложений

Если вы только начинаете работать с дополнительными пакетами или связанными наборами UWP, ознакомьтесь со следующими статьями, чтобы получить хорошую основу для начала работы. 

1.  [Расширение приложения с помощью дополнительных пакетов](https://blogs.msdn.microsoft.com/appinstaller/2017/04/05/uwpoptionalpackages/)
2.  [Сборка первого дополнительного пакета](https://blogs.msdn.microsoft.com/appinstaller/2017/05/09/build-your-first-optional-package/)
3.  [Загрузка кода из дополнительного пакета](https://blogs.msdn.microsoft.com/appinstaller/2017/05/11/loading-code-from-an-optional-package/)
4.  [Инструменты для создания связанного набора](https://blogs.msdn.microsoft.com/appinstaller/2017/05/12/tooling-to-create-a-related-set/)
5.  [Разработка дополнительных пакетов и связанных наборов](https://docs.microsoft.com/windows/uwp/packaging/optional-packages)

Начиная с Windows 10 Fall Creators Update, связанные наборы можно устанавливать с помощью Установщика приложений. Такой подход позволяет распространять и развертывать пакеты приложений связанных наборов для пользователей. 

## <a name="how-to-install-a-related-set"></a>Инструкции по установке связанного набора 
Связанный набор— это не один объект, а сочетание основного и дополнительных пакетов. Чтобы получить возможность установить связанный набор как один объект, необходимо указать основной и дополнительные пакеты как один объект. Для этого потребуется создать XML-файл с расширением ".appinstaller", чтобы определить связанный набор. Установщик приложений использует файл *.appinstaller и позволяет пользователю установить все определенные пакеты одним нажатием. 

Прежде чем углубиться в детали, ознакомьтесь с полным примером файла *.appinstaller:

```xml
<?xml version="1.0" encoding="utf-8"?>
<AppInstaller
    xmlns="http://schemas.microsoft.com/appx/appinstaller/2017/2"
    Version="1.0.0.0" 
    Uri="http://mywebservice.azurewebsites.net/appset.appinstaller" > 
   
    <MainBundle 
        Name="Contoso.MainApp"
        Publisher="CN=Contoso"
        Version="2.23.12.43"
        Uri="http://mywebservice.azurewebsites.net/mainapp.appxbundle" />
        
    <OptionalPackages>
        <Bundle
            Name="Contoso.OptionalApp1"
            Publisher="CN=Contoso"
            Version="2.23.12.43"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp1.appxbundle" />

        <Bundle
            Name="Contoso.OptionalApp2"
            Publisher="CN=Contoso"
            Version="2.23.12.43"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp2.appxbundle" />

        <Package
            Name="Fabrikam.OptionalApp3"
            Publisher="CN=Fabrikam"
            Version="10.34.54.23"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp3.appx" />

    </OptionalPackages>

</AppInstaller>
```

При развертывании файл Установщика приложений сверяется с пакетами приложений, на которые ссылается элемент `Uri`. таким образом, значения `Name`, `Publisher` и `Version` должны соответствовать элементу [Package/Identity](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) в манифесте пакета приложения. 

## <a name="how-to-create-an-app-installer-file"></a>Инструкции по созданию файла Установщика приложений. 

Для распределения связанного набора как одного объекта необходимо создать файл Установщика приложений, содержащий элементы, которые требуются соответствующей [схеме Установщика приложений](https://docs.microsoft.com/uwp/schemas/appinstallerschema/app-installer-file).

### <a name="step-1-create-the-appinstaller-file"></a>Шаг 1. Создание файла *.appinstaller
Используя текстовый редактор, создайте файл (который будет содержать XML) и назовите его &lt;имя_файла&gt;.appinstaller 

### <a name="step-2-add-the-basic-template"></a>Шаг 2. Добавление базового шаблона
Базовый шаблон включает в себя сведения о файле Установщика приложений. 
```xml
<?xml version="1.0" encoding="utf-8"?>
<AppInstaller
    xmlns="http://schemas.microsoft.com/appx/appinstaller/2017/2"
    Version="1.0.0.0" 
    Uri="http://mywebservice.azurewebsites.net/appset.appinstaller" > 
</AppInstaller>
```

### <a name="step-3-add-the-main-package-information"></a>Шаг 3. Добавление сведений об основном пакете 
Если основной пакет приложения является файлом .appxbundle или .msixbundle, используйте `<MainBundle>` показано ниже. Если основной пакет приложения является файлом .appx или .msix, используйте `<MainPackage>` вместо `<MainBundle>` в фрагменте кода. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<AppInstaller
    xmlns="http://schemas.microsoft.com/appx/appinstaller/2017/2"
    Version="1.0.0.0" 
    Uri="http://mywebservice.azurewebsites.net/appset.appinstaller" > 
   
    <MainBundle 
        Name="Contoso.MainApp"
        Publisher="CN=Contoso"
        Version="2.23.12.43"
        Uri="http://mywebservice.azurewebsites.net/mainapp.appxbundle" />

</AppInstaller>
```
Сведения в атрибуте `<MainBundle>` или `<MainPackage>` должны совпадать с элементом [Package/Identity](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) в манифесте пакета приложений или пакета приложения соответственно. 

### <a name="step-4-add-the-optional-packages"></a>Шаг 4. Добавление дополнительных пакетов 
Как и атрибут основного пакета приложений, если дополнительный пакет может являться пакетом приложения или пакетом приложений, дочерний элемент с атрибутом `<OptionalPackages>` должен являться `<Package>` или `<Bundle>`. Сведения о пакете в дочерних элементах должны соответствовать элементу идентификатора в манифеста пакета приложений или пакета приложения. 

``` xml
<?xml version="1.0" encoding="utf-8"?>
<AppInstaller
    xmlns="http://schemas.microsoft.com/appx/appinstaller/2017/2"
    Version="1.0.0.0" 
    Uri="http://mywebservice.azurewebsites.net/appset.appinstaller" > 
   
    <MainBundle 
        Name="Contoso.MainApp"
        Publisher="CN=Contoso"
        Version="2.23.12.43"
        Uri="http://mywebservice.azurewebsites.net/mainapp.appxbundle" />
        
    <OptionalPackages>
        <Bundle
            Name="Contoso.OptionalApp1"
            Publisher="CN=Contoso"
            Version="2.23.12.43"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp1.appxbundle" />

        <Bundle
            Name="Contoso.OptionalApp2"
            Publisher="CN=Contoso"
            Version="2.23.12.43"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp2.appxbundle" />

        <Package
            Name="Fabrikam.OptionalApp3"
            Publisher="CN=Fabrikam"
            Version="10.34.54.23"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp3.appx" />

    </OptionalPackages>

</AppInstaller>
```

### <a name="step-5-add-dependencies"></a>Шаг 5. Добавление зависимостей 
В элементе зависимостей можно указать требуемые пакеты платформы для основного пакета или дополнительных пакетов. 

``` xml
<?xml version="1.0" encoding="utf-8"?>
<AppInstaller
    xmlns="http://schemas.microsoft.com/appx/appinstaller/2017/2"
    Version="1.0.0.0" 
    Uri="http://mywebservice.azurewebsites.net/appset.appinstaller" > 
   
    <MainBundle 
        Name="Contoso.MainApp"
        Publisher="CN=Contoso"
        Version="2.23.12.43"
        Uri="http://mywebservice.azurewebsites.net/mainapp.appxbundle" />
        
    <OptionalPackages>
        <Bundle
            Name="Contoso.OptionalApp1"
            Publisher="CN=Contoso"
            Version="2.23.12.43"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp1.appxbundle" />

        <Bundle
            Name="Contoso.OptionalApp2"
            Publisher="CN=Contoso"
            Version="2.23.12.43"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp2.appxbundle" />

        <Package
            Name="Fabrikam.OptionalApp3"
            Publisher="CN=Fabrikam"
            Version="10.34.54.23"
            ProcessorArchitecture="x86"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp3.appx" />

    </OptionalPackages>

    <Dependencies>
        <Package Name="Microsoft.VCLibs.140.00" Publisher="CN=Microsoft Corporation, O=Microsoft Corporation, L=Redmond, S=Washington, C=US" Version="14.0.24605.0" ProcessorArchitecture="x86" Uri="http://foobarbaz.com/fwkx86.appx" />
        <Package Name="Microsoft.VCLibs.140.00" Publisher="CN=Microsoft Corporation, O=Microsoft Corporation, L=Redmond, S=Washington, C=US" Version="14.0.24605.0" ProcessorArchitecture="x64" Uri="http://foobarbaz.com/fwkx64.appx" />
    </Dependencies>

</AppInstaller>
```

### <a name="step-6-add-update-setting"></a>Шаг 6. Добавление параметра обновления 
В файле Установщика приложений можно также указать параметр обновления, чтобы связанные наборы автоматически обновлялись при публикации нового файла Установщика приложений. Элемент **<UpdateSettings>** необязателен. В **<UpdateSettings>** параметр OnLaunch указывает, что проверку обновлений следует выполнять при запуске приложения, а значение HoursBetweenUpdateChecks="12" указывает, что проверять обновления нужно каждые 12 часов. Если параметр HoursBetweenUpdateChecks не задан, интервал проверки обновлений по умолчанию— 24 часа.
``` xml
<?xml version="1.0" encoding="utf-8"?>
<AppInstaller
    xmlns="http://schemas.microsoft.com/appx/appinstaller/2017/2"
    Version="1.0.0.0" 
    Uri="http://mywebservice.azurewebsites.net/appset.appinstaller" > 
   
    <MainBundle 
        Name="Contoso.MainApp"
        Publisher="CN=Contoso"
        Version="2.23.12.43"
        Uri="http://mywebservice.azurewebsites.net/mainapp.appxbundle" />
        
    <OptionalPackages>
        <Bundle
            Name="Contoso.OptionalApp1"
            Publisher="CN=Contoso"
            Version="2.23.12.43"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp1.appxbundle" />

        <Bundle
            Name="Contoso.OptionalApp2"
            Publisher="CN=Contoso"
            Version="2.23.12.43"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp2.appxbundle" />

        <Package
            Name="Fabrikam.OptionalApp3"
            Publisher="CN=Fabrikam"
            Version="10.34.54.23"
            ProcessorArchitecture="x86"
            Uri="http://mywebservice.azurewebsites.net/OptionalApp3.appx" />

    </OptionalPackages>

    <Dependencies>
        <Package Name="Microsoft.VCLibs.140.00" Publisher="CN=Microsoft Corporation, O=Microsoft Corporation, L=Redmond, S=Washington, C=US" Version="14.0.24605.0" ProcessorArchitecture="x86" Uri="http://foobarbaz.com/fwkx86.appx" />
        <Package Name="Microsoft.VCLibs.140.00" Publisher="CN=Microsoft Corporation, O=Microsoft Corporation, L=Redmond, S=Washington, C=US" Version="14.0.24605.0" ProcessorArchitecture="x64" Uri="http://foobarbaz.com/fwkx64.appx" />
    </Dependencies>
    
    <UpdateSettings>
        <OnLaunch HoursBetweenUpdateChecks="12" />
    </UpdateSettings>

</AppInstaller>
```

Полные сведения о схеме XML см. в разделе [Справочник по файлу Установщика приложений](https://docs.microsoft.com/uwp/schemas/appinstallerschema/app-installer-file).

> [!NOTE]
> 
> Файл Установщика приложений— это новый тип файлов в Windows 10 Fall Creators Update. В предыдущих версиях Windows 10 развертывание приложений UWP с помощью файла Установщика приложений не поддерживается.
> Следует также отметить, что элемент **HoursBetweenUpdateChecks** станет новым в следующем основном обновлении Windows 10.
