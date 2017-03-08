---
author: awkoren
Description: "Процедура ручного преобразования классического приложения для Windows (например, Win32, WPF и Windows Forms) в приложение универсальной платформы Windows (UWP)."
Search.Product: eADQiWindows 10XVcnh
title: "Ручное преобразование классического приложения для Windows в приложение универсальной платформы Windows (UWP)"
ms.author: alkoren
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: e8c2a803-9803-47c5-b117-73c4af52c5b6
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 345296a3fa9faeb8daa8e03fbb633863380d2424
ms.lasthandoff: 02/08/2017

---

# <a name="manually-convert-your-app-to-uwp-using-the-desktop-bridge"></a>Ручное преобразование приложения в приложение UWP с помощью моста для классических приложений

[Desktop App Converter (DAC)](desktop-to-uwp-run-desktop-app-converter.md) — удобное автоматическое средство, пользоваться которым имеет смысл, если вы не знаете точно, что делает ваша программа установки. Но если установка приложения выполняется с помощью команды xcopy, или если вы знаете, какие изменения ваш установщик приложения вносит в систему, можно создать манифест пакета приложения вручную. В этой статье приведены шаги для начала работы. В ней также объясняется, как добавлять ресурсы без основы в приложения, что недоступно в DAC. 

Вот как начать ручное преобразование. Если у вас приложение .NET и вы используете Visual Studio, альтернативный вариант см. в статье [Руководство по упаковке классических приложений .NET с использованием моста для классических приложений с помощью Visual Studio](desktop-to-uwp-packaging-dot-net.md).  

## <a name="create-a-manifest-by-hand"></a>Создайте манифест вручную

В файле _appxmanifest.xml_ должно быть указанное ниже содержимое (как минимум). Измените заполнители формата \*\*\*THIS\*\*\* на фактические значения приложения.

```XML
    <?xml version="1.0" encoding="utf-8"?>
    <Package
       xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
       xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
       xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities">
      <Identity Name="***YOUR_PACKAGE_NAME_HERE***"
        ProcessorArchitecture="x64"
        Publisher="CN=***COMPANY_NAME***, O=***ORGANIZATION_NAME***, L=***CITY***, S=***STATE***, C=***COUNTRY***"
        Version="***YOUR_PACKAGE_VERSION_HERE***" />
      <Properties>
        <DisplayName>***YOUR_PACKAGE_DISPLAY_NAME_HERE***</DisplayName>
        <PublisherDisplayName>Reserved</PublisherDisplayName>
        <Description>No description entered</Description>
        <Logo>***YOUR_PACKAGE_RELATIVE_DISPLAY_LOGO_PATH_HERE***</Logo>
      </Properties>
      <Resources>
        <Resource Language="en-us" />
      </Resources>
      <Dependencies>
        <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.14316.0" MaxVersionTested="10.0.14316.0" />
      </Dependencies>
      <Capabilities>
        <rescap:Capability Name="runFullTrust"/>
      </Capabilities>
      <Applications>
        <Application Id="***YOUR_PRAID_HERE***" Executable="***YOUR_PACKAGE_RELATIVE_EXE_PATH_HERE***" EntryPoint="Windows.FullTrustApplication">
          <uap:VisualElements
           BackgroundColor="#464646"
           DisplayName="***YOUR_APP_DISPLAY_NAME_HERE***"
           Square150x150Logo="***YOUR_PACKAGE_RELATIVE_PNG_PATH_HERE***"
           Square44x44Logo="***YOUR_PACKAGE_RELATIVE_PNG_PATH_HERE***"
           Description="***YOUR_APP_DESCRIPTION_HERE***" />
        </Application>
      </Applications>
    </Package>
```

Есть ресурсы без основы, которые вы хотите добавить? Дополнительные сведения о том, как это сделать, см. в разделе о [ресурсах без основы](#unplated-assets) далее в этой статье.

## <a name="run-the-makeappx-tool"></a>Запустите средство MakeAppX.

Используйте [упаковщик приложений (MakeAppx.exe)](https://msdn.microsoft.com/library/windows/desktop/hh446767(v=vs.85).aspx), чтобы создать пакет AppX для проекта. Файл MakeAppx.exe входит в пакет SDK для Windows 10. 

Чтобы запустить средство MakeAppx, сначала убедитесь, что вы создали файл манифеста, как описано выше. 

Затем создайте файл сопоставления. Файл должен начинаться с элемента **[Files]**, а под ним должен быть приведен список всех исходных файлов на диске с указанием конечного пути к ним в пакете. Пример. 

```
[Files]
"C:\MyApp\StartPage.htm"     "default.html"
"C:\MyApp\readme.txt"        "doc\readme.txt"
"\\MyServer\path\icon.png"   "icon.png"
"MyCustomManifest.xml"       "AppxManifest.xml"
```

Затем выполните следующую команду: 

```cmd
MakeAppx.exe pack /f mapping_filepath /p filepath.appx
```

## <a name="sign-your-appx-package"></a>Подписывание пакета AppX

Для командлета Add-AppxPackage необходимо, чтобы развертываемый пакет приложения (.appx) был подписан. Используйте файл [SignTool.exe](https://msdn.microsoft.com/library/windows/desktop/aa387764(v=vs.85).aspx), который входит в состав Microsoft Windows 10 SDK, чтобы подписать пакет .appx.

Пример использования: 

```cmd
C:\> MakeCert.exe -r -h 0 -n "CN=<publisher_name>" -eku 1.3.6.1.5.5.7.3.3 -pe -sv <my.pvk> <my.cer>
C:\> pvk2pfx.exe -pvk <my.pvk> -spc <my.cer> -pfx <my.pfx>
C:\> signtool.exe sign -f <my.pfx> -fd SHA256 -v .\<outputAppX>.appx
```
При запуске файла MakeCert.exe, когда вам будет предложено ввести пароль, выберите **Нет**. Дополнительные сведения о сертификатах и процессе подписывания см. в следующих разделах: 

- [Практическое руководство: создание временных сертификатов для использования во время разработки](https://msdn.microsoft.com/library/ms733813.aspx)
- [SignTool](https://msdn.microsoft.com/library/windows/desktop/aa387764.aspx)
- [SignTool.exe (Sign Tool)](https://msdn.microsoft.com/library/8s9b9yaz.aspx)

<span id="unplated-assets" />
## <a name="add-unplated-assets"></a>Добавление ресурсов без основы

Вот как можно дополнительно настроить ресурсы 44x44, которые отображаются на панели задач, для приложения. 

1. Получите верные изображения 44x44 и скопируйте их в папку с вашими изображениями (например, "Ресурсы").

2. Создайте копию каждого изображения 44x44 в той же папке и добавьте *.targetsize-44_altform-unplated* к имени файла. Вам потребуются две копии каждого значка, каждый из которых должен быть назван особым образом. Например, после завершения процесса ваша папка ресурсов может содержать файлы *MYAPP_44x44.png* и *MYAPP_44x44.targetsize-44_altform-unplated.png* (обратите внимание, что первый файл — это значок, ссылка на который содержится в appxmanifest в атрибуте *Square44x44Logo* VisualElements). 

3.    В AppXManifest установите атрибуту BackgroundColor для каждого значка, к которому применяется исправление, значение "transparent". Этот атрибут находится в элементе VisualElements для каждого приложения.

4.    Откройте CMD, измените каталог на корневую папку пакета и создайте файл priconfig.xml с помощью команды ```makepri createconfig /cf priconfig.xml /dq en-US```.

5.    Оставаясь в корневой папке пакета, используйте CMD для создания одного или нескольких файлов resources.pri с помощью команды ```makepri new /pr <PHYSICAL_PATH_TO_FOLDER> /cf <PHYSICAL_PATH_TO_FOLDER>\priconfig.xml```. Например, команда для вашего приложения может выглядеть так: ```makepri new /pr c:\MYAPP /cf c:\MYAPP\priconfig.xml```. 

6.    Упакуйте AppX, руководствуясь инструкциями в следующем шаге, чтобы увидеть результат.
