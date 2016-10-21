---
Description: "Показывает как выполнить ручное преобразование классического приложения для Windows (например, Win32, WPF и Windows Forms) в приложение для универсальной платформы Windows (UWP)."
Search.Product: eADQiWindows 10XVcnh
title: "Ручное преобразование классического приложения для Windows в приложение для универсальной платформы Windows (UWP)"
translationtype: Human Translation
ms.sourcegitcommit: 2c1a8ea38081c947f90ea835447a617c388aec08
ms.openlocfilehash: 646a5b88cb7ca97f18bf4552950979a2ceead398

---

# Ручное преобразование классического приложения для Windows в приложение для универсальной платформы Windows (UWP)

\[Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, прямых или косвенных, в отношении указанной здесь информации.\]

Преобразователь удобен в применении и работает в автоматическом режиме. Его полезно использовать, если вы не уверены, какими возможностями обладает ваш установщик. Но если установка приложения выполняется с помощью команды xcopy, или если вы знаете, какие изменения ваш установщик приложения вносит в систему, можно создать манифест пакета приложения вручную.

Ниже приведены шаги по созданию пакета вручную:

## Создайте манифест вручную.

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

## Запустите средство MakeAppX.

Используйте [Упаковщик приложений (MakeAppx.exe)](https://msdn.microsoft.com/library/windows/desktop/hh446767(v=vs.85).aspx), чтобы создать пакет AppX для проекта. Файл MakeAppx.exe входит в пакет SDK для Windows 10. 

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

## Подписывание пакета AppX

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

- [SignTool.exe (инструмент подписывания)](https://msdn.microsoft.com/library/8s9b9yaz.aspx)




<!--HONumber=Sep16_HO2-->


