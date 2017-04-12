---
author: normesta
Description: "Развертывание и отладка приложений универсальной платформы Windows (UWP), преобразованных из классических приложений для Windows (Win32, WPF и Windows Forms) с помощью Desktop Bridge."
Search.Product: eADQiWindows 10XVcnh
title: "Мост переноса классических приложений на UWP: отладка"
ms.author: normesta
ms.date: 03/09/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: f45d8b14-02d1-42e1-98df-6c03ce397fd3
ms.openlocfilehash: d1ce3054df19b0b51c8203e7fa7296efde848c41
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="desktop-to-uwp-bridge-debug"></a>Мост переноса классических приложений на UWP: отладка

В этой статье содержится информация, которая поможет вам отлаживать приложения после их преобразования в приложения UWP с помощью моста переноса классических приложений на UWP. Существует несколько вариантов отладки преобразованного приложения.

## <a name="attach-to-process"></a>Прикрепление к процессу

Когда Microsoft Visual Studio выполняется «от имени администратора», команды *Начать отладку* и *Запуск без отладки* будут работать для преобразованного проекта, однако запущенное приложение будет выполняться на [среднем уровне целостности](https://msdn.microsoft.com/library/bb625963) (т.е. у него не будет повышенных привилегий). Чтобы предоставить запущенному приложению права администратора, сначала необходимо запустить приложение «от имени администратора» с помощью ярлыка или плитки. После запуска приложения из экземпляра Microsoft Visual Studio, выполняющегося «от имени администратора», вызовите команду __Прикрепить к процессу__ и выберите процесс приложения в диалоговом окне.

## <a name="f5-debug"></a>Отладка с помощью клавиши F5

Visual Studio теперь поддерживает новый вид проекта— пакет упаковки. Проект упаковки позволяет автоматически копировать все обновления, вносимые при создании вашего приложения, в пакет приложения для Windows, создаваемый преобразователем в установщике приложения. После настройки проекта упаковки теперь можно также использовать клавишу F5 для отладки приложений непосредственно в пакете приложения для Windows.

>Примечание. Вы также можете использовать этот параметр для отладки существующего пакета приложения для Windows. Для этого выберите "Отладка" -> "Другие цели отладки" -> "Отладка установленного пакета приложения".

Порядок начала работы:

1. Сначала убедитесь, что у вас все готово для использования Desktop App Converter. Инструкции см. в статье [Desktop App Converter](desktop-to-uwp-run-desktop-app-converter.md).

2. Запустите преобразователь, а затем установщик для своего приложения Win32. Преобразователь выполнит захват макета и все изменения, внесенные в реестр, а затем выведет пакет приложения для Windows с манифестом и файлом registery.dat для виртуализации реестра:

![alt](images/desktop-to-uwp/debug-1.png)

3. Установка и запуск [Visual Studio 2017 RC](https://www.visualstudio.com/downloads/#visual-studio-community-2017-rc).

4. Установите классическое приложение в проект VSIX пакета UWP из [коллекции Visual Studio](http://go.microsoft.com/fwlink/?LinkId=797871).

5. Откройте соответствующее решение Win32, преобразованное в Visual Studio.

6. Добавьте новый пакетный проект в решение, щелкнув правой кнопкой мыши решение и выбрав пункт «Добавить новый проект». Затем выберите пункт «Преобразование классического приложения в пакетный проект UWP» в разделе «Проекты установки и развертывания»:

    ![alt](images/desktop-to-uwp/debug-2.png)

    Созданный проект будет добавлен в ваше решение:

    ![alt](images/desktop-to-uwp/debug-3.png)

    В пакетном проекте AppXFileList отображает сопоставление файлов в виде макета пакета приложения для Windows. Раздел "Ссылки" будет пустым, и его будет необходимо вручную связать с проектом .exe для расстановки компонентов сборки.

7. Проект DesktopToUWPPackaging содержит страницу свойств, позволяющую настраивать корень пакета приложения для Windows и задавать плитки, которые необходимо выполнить:

    ![alt](images/desktop-to-uwp/debug-4.png)

    Установите PackageLayout корневую папку пакета приложения для Windows, которая была создана преобразователем (выше). Затем выберите плитку, которую необходимо выполнить.

8.    Откройте и отредактируйте файл AppXFileList.xml. Этот файл определяет, как копировать выходные данные сборки отладки Win32 в макет пакета приложения для Windows, созданный преобразователем. По умолчанию в файле имеется заполнитель с примером тега и содержимого:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
      <ItemGroup>
    <!— Use the following syntax to copy debug output to the AppX layout
       <AppxPackagedFile Include="$(outdir)\App.exe">
          <PackagePath>App.exe</PackagePath>
        </AppxPackagedFile>
        See http://etc...
    -->
      </ItemGroup>
    </Project>
    ```

    Ниже приводится пример создания сопоставления. В этом случае мы копируем файлы .exe и .dll из папки сборки Win32 в папку макета пакета.

    ```XML
    <?xml version="1.0" encoding=utf-8"?>
    <Project ToolsVersion=14.0" xmlns="http://scehmas.microsoft.com/developer/msbuild/2003">
        <PropertyGroup>
            <MyProjectOutputPath>{relativepath}</MyProjectOutputPath>
        </PropertyGroup>
        <ItemGroup>
            <LayoutFile Include="$(MyProjectOutputPath)\ProjectTracker.exe">
                <PackagePath>$(PackageLayout)\VFS\Program Files (x86)\Contoso Software\Project Tracker\ProjectTracker.exe</PackagePath>
            </LayoutFile>
            <LayoutFile Include="$(MyProjectOutputPath)\ProjectTracker.Models.dll">
                <PackagePath>$(PackageLayout)\VFS\Program Files (x86)\Contoso Software\Project Tracker\ProjectTracker.Models.dll</PackagePath>
            </LayoutFile>
        </ItemGroup>
    </Project>
    ```

    Файл определяется следующим образом:

    Сначала выполняется определение объекта *MyProjectOutputPath*, чтобы он указывал на расположение, в котором выполняется сборка проекта Win32:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <Project ToolsVersion="14.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
        <PropertyGroup>
            <MyProjectOutputPath>..\ProjectTracker\bin\DesktopUWP</MyProjectOutputPath>
        </PropertyGroup>
    ```

    После этого каждый объект *LayoutFile* определяет файл для копирования из папки сборки Win32 в макет пакета приложения для Windows. В этом случае сначала копируется файл .exe, а затем файл .dll.

    ```XML
        <ItemGroup>
            <LayoutFile Include="$(MyProjectOutputPath)\ProjectTracker.exe">
                <PackagePath>$(PackageLayout)\VFS\Program Files (x86)\Contoso Software\Project Tracker\ProjectTracker.exe</PackagePath>
            </LayoutFile>
            <LayoutFile Include="$(MyProjectOutputPath)\ProjectTracker.Models.dll">
                <PackagePath>$(PackageLayout)\VFS\Program Files (x86)\Contoso Software\Project Tracker\ProjectTracker.Models.dll</PackagePath>
            </LayoutFile>
        </ItemGroup>
    </Project>
    ```

9. Установите пакетный проект в качестве запускаемого проекта. При этом файлы Win32 будут скопированы в пакет приложения для Windows, и после создания и запуска проекта необходимо запустить отладчик.  

    ![alt](images/desktop-to-uwp/debug-5.png)

10.    Наконец, вы можете задать точку останова в коде Win32 и нажать клавишу F5, чтобы запустить отладчик. Он скопирует все обновления, которые вы внесли в приложение Win32 во всем пакете приложения для Windows, и вы сможете выполнить отладку непосредственно из Visual Studio.

11.    Если вы обновите приложение, будет необходимо использовать программу MakeAppx, чтобы перепаковать приложение повторно. Дополнительные сведения см. в статье [упаковщик приложений (MakeAppx.exe)](https://msdn.microsoft.com/library/windows/desktop/hh446767(v=vs.85).aspx).

Если у вас несколько конфигураций сборки (например, для выпуска и отладки), можно добавить следующий код в файл AppXFileList.xml, чтобы скопировать сборку Win32 из различных расположений:

```XML
<PropertyGroup>
    <MyProjectOutputPath Condition="$(Configuration) == 'DesktopUWP'">C:\Users\peterfar\Desktop\ProjectTracker\ProjectTracker\bin\DesktopUWP>
    </MyProjectOutputPath>
    <MyProjectOutputPath Condition="$(Configuration) == 'ReleaseDesktopUWP'"> C:\Users\peterfar\Desktop\ProjectTracker\ProjectTracker\bin\ReleaseDesktopUWP</MyProjectOutputPath>
</PropertyGroup>
```

Вы также можете применить условную компиляцию для включения определенных путей кода в случае обновления приложения до приложения UWP, но также при необходимости его сборки для Win32.

1.    В примере ниже код будет скомпилирован только для DesktopUWP и будет показывать плитку с помощью API-интерфейса WinRT.

    ```C#
    [Conditional("DesktopUWP")]
    private void showtile()
    {
        XmlDocument tileXml = TileUpdateManager.GetTemplateContent(TileTemplateType.TileSquare150x150Text01);
        XmlNodeList textNodes = tileXml.GetElementsByTagName("text");
        textNodes[0].InnerText = string.Format("Welcome to DesktopUWP!");
        TileNotification tileNotification = new TileNotification(tileXml);
        TileUpdateManager.CreateTileUpdaterForApplication().Update(tileNotification);
    }
    ```

2.    Вы можете использовать Configuration Manager, чтобы добавить новую конфигурацию сборки:

    ![alt](images/desktop-to-uwp/debug-6.png)

    ![alt](images/desktop-to-uwp/debug-7.png)

3.    Затем в разделе свойств проекта добавьте поддержку символов условной компиляции:

    ![alt](images/desktop-to-uwp/debug-8.png)

4.    Теперь вы можете переключиться между целью сборки и DesktopUWP, если необходимо, чтобы целью сборки был API-интерфейс UWP, который вы добавили.

## <a name="plmdebug"></a>PLMDebug

Клавишу F5 и функцию "Присоединиться к процессу" в Visual Studio можно использовать для отладки запущенного приложения. Но в некоторых случаях вам может потребоваться более точное управление процессом отладки, включая возможность отладки приложения до его запуска. В таких нестандартных ситуациях используйте [**PLMDebug**](https://msdn.microsoft.com/library/windows/hardware/jj680085(v=vs.85).aspx). Это средство позволяет выполнять отладку преобразованного приложения с помощью отладчика Windows и предоставляет полный контроль над жизненным циклом приложения благодаря функциям приостановки, возобновления и завершения.

PLMDebug входит в состав Windows SDK. Подробнее см. в статье [**PLMDebug**](https://msdn.microsoft.com/library/windows/hardware/jj680085(v=vs.85).aspx).

## <a name="run-another-process-inside-the-full-trust-container"></a>Запуск другого процесса в контейнере с полным доверием

Вы можете вызывать пользовательские процессы внутри контейнера указанного пакета приложения. Это бывает полезно при тестировании,— например, если у вас пользовательское окружение теста и вам требуется протестировать выходные данные приложения. Сделать это можно с помощью командлета ```Invoke-CommandInDesktopPackage``` PowerShell:

```CMD
Invoke-CommandInDesktopPackage [-PackageFamilyName] <string> [-AppId] <string> [-Command] <string> [[-Args]
    <string>]  [<CommonParameters>]
```
