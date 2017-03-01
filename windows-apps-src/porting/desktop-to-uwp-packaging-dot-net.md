---
author: awkoren
Description: "В этом руководстве объясняется, как настроить решение Visual Studio для изменения, отладки и упаковки приложений, преобразованных с помощью моста для классических приложений."
Search.Product: eADQiWindows 10XVcnh
title: "Руководство по упаковке классических приложений .NET с использованием моста для классических приложений с помощью Visual Studio"
ms.author: alkoren
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 807a99a7-d285-46e7-af6a-7214da908907
translationtype: Human Translation
ms.sourcegitcommit: 5645eee3dc2ef67b5263b08800b0f96eb8a0a7da
ms.openlocfilehash: 8aa68312d6ce81c809c79ddcafe7732944a628be
ms.lasthandoff: 02/08/2017

---

# <a name="desktop-bridge-packaging-guide-for-net-desktop-apps-with-visual-studio"></a>Руководство по упаковке классических приложений .NET с использованием моста для классических приложений с помощью Visual Studio

Благодаря юбилейному обновлению Windows 10 разработчики могут использовать мост для классических приложений для упаковки имеющихся приложений Win32 с помощью новой модели пакета (.appx), которая обеспечивает возможность публикации в Магазине и удобной загрузки неопубликованных приложений. В этом руководстве объясняется, как настроить решение Visual Studio для изменения, отладки и упаковки приложения. 

Начните с заполнения формы на странице [Публикация существующих приложений и игр в Магазине Windows с помощью моста для классических приложений](https://developer.microsoft.com/windows/projects/campaigns/desktop-bridge). После этого Майкрософт свяжется с вами, чтобы начать процесс публикации. После утверждения учетной записи для отправки приложений, преобразованных с помощью моста для классических приложений, следуйте инструкциям в этом документе, чтобы подготовить пакет appxupload к отправке. 

> Хотите поделиться мнением или столкнулись с проблемами в ходе работы с мостом для классических приложений? Предлагать новые функции лучше всего на платформе [UserVoice для разработчиков для Windows](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial). Вопросы и отчеты об ошибках модно оставлять на [форумах разработчиков универсальных приложений для Windows](https://social.msdn.microsoft.com/Forums/home?forum=wpdevelop).

## <a name="default-universal-windows-platform-packages"></a>Пакеты универсальной платформы Windows по умолчанию

С помощью Visual Studio можно создавать пакеты отладки и выпусков, которые можно распространять через Магазин Windows или путем загрузки неопубликованных приложений. Для упрощения создания пакетов Visual Studio помогает создать файл appxupload, готовый к отправке в Магазин. Дополнительные сведения см. в разделе [Упаковка приложений UWP](..\packaging\packaging-uwp-apps.md).

## <a name="desktop-bridge-packages"></a>Пакеты моста для классических приложений

С помощью [моста для классических приложений](desktop-to-uwp-root.md) можно использовать различные конфигурации для интеграции двоичных файлов Win32 в пакете приложения (appx). Ход использования моста для классических приложений можно разделить на четыре основных этапа. 

- **Этап 1. Преобразование**. Упакуйте имеющиеся двоичные файлы Win32 с минимальными изменениями в коде или без изменений.
- **Этап 2. Улучшение**. Добавьте базовые функции UWP (например, живую плитку) в существующее приложение путем создания ссылки на файл Windows.winmd в имеющемся коде Win32.
- **Этап 3. Расширение**. Добавьте расширенные возможности UWP (например, фоновые задачи) в существующее приложение. Если компоненты UWP и Win32 создаются с помощью управляющих языков (например, C# или VB.Net), в готовом пакете будут смешанные двоичные файлы, которые следует тщательно обработать, чтобы обеспечить верную обработку .NET Native. 
- **Этап 4. Перенос**. Вы перенесли пользовательский интерфейс на современные языки XAML и C#/VB.NET, но у вас по-прежнему традиционный код Win32. Точкой входа стал исполняемый файл .NET UWP, но в пакете остались двоичные файлы, использующие некоторые API Win32.

В следующей таблице указаны некоторые различия для вашего приложения на каждом из четырех этапов. 

| Этап | Двоичные файлы | EntryPoint | .NET Native | Отладка с помощью клавиши F5 |
|---|---|---|---|---|
| 1 (преобразование) | Win32 | Win32 | Н/Д | Расширение VS |
| 2 (улучшение) | Ссылки на WinMD | Win32 | Н/Д | Расширение VS |
| 3 (расширение) | Win32 + CoreCLR (*) | Win32 | По пользователю (**) | Расширение VS |
| 4 (перенос)    | CoreCLR (*) + Win32 | UWP | По пользователю (**) | VS |
| 5 (UWP) | CoreCLR | UWP |По Магазину | VS |

(*) [CoreCLR](https://github.com/dotnet/coreclr) ссылается на среду выполнения .NET Core, от которой зависят компоненты, написанные на управляемом языке (C#/VB.NET). Для этих компонентов также потребуется обработка .NET Native.

(**) На этапах 3 и 4 пользователю следует обработать сборки CoreCLR, чтобы создать собственные двоичные файлы .NET и соответствующие символы перед публикацией в магазине.

## <a name="configure-your-visual-studio-solution"></a>Настройка решения Visual Studio

Visual Studio содержит инструменты, необходимые для настройки пакета приложения, например редактор манифестов и мастер создания пакетов. Для использования этих инструментов потребуется проект UWP, который будет выполнять роль контейнера appx для приложения. Хотя можно использовать любой проект UWP (включая C#, VB.NET, C++ или JavaScript), с проектами на C#, VB.NET и C++ связаны некоторые известные проблемы (см. раздел [Известные проблемы](#known-issues-anchor) далее в этом документе), так что в этом примере мы будем использовать проект JavaScript. 

Если вам требуется выполнить отладку приложения в контексте модели приложений appx, необходимо добавить еще один проект, который обеспечит возможность отладки appx с помощью клавиши F5. Подробнее см. в разделе [Отладка приложения, преобразованного с помощью моста для классических приложений](#debugging-anchor).

Приступим к этапу 1.

### <a name="step-1-convert"></a>Этап 1. Преобразование

На этом этапе показано, как создать приложение, преобразованное с помощью моста для классических приложений, на основе существующего проекта Win32. В этом примере мы будем использовать базовый проект WinForms, выполняющий операции чтения и записи в реестре.

![](images/desktop-to-uwp/net-1.png)

#### <a name="add-the-uwp-project"></a>Добавление проекта UWP 

Чтобы создать пакет моста для классических приложений, добавьте проект UWP JavaScript в то же решение.

> Примечание. Хотя мы используем шаблон UWP JavaScript, мы не будем писать код на JavaScript. Мы используем проект только как инструмент.

![](images/desktop-to-uwp/net-2.png)

#### <a name="add-the-win32-binaries-to-the-win32-folder"></a>Добавление двоичных файлов Win32 в папку win32

Все двоичные файлы Win32 будут храниться в проекте UWP в папке win32 (не обязательно использовать именно это имя — можно выбрать любое другое).

Если вы используете Visual Studio, можно автоматизировать проект и настроить копирование файлов после каждой сборки, чтобы улучшить рабочий процесс разработки. Измените файл проекта (в этом примере — .csproj) так, чтобы он включал целевой объект AfterBuild, который скопирует все выходные файлы Win32 в папку win32 в проекте UWP, как показано ниже: 

```xml
  <Target Name="AfterBuild">
    <PropertyGroup>
      <TargetUWP>..\MyDesktopApp.Package\win32\</TargetUWP>
    </PropertyGroup>
     <ItemGroup>
       <Win32Binaries Include="$(TargetDir)\*" />
     </ItemGroup>
    <Copy SourceFiles="@(Win32Binaries)" DestinationFolder="$(TargetUWP)" />
  </Target>
```

Если вы используете другое средство для создания двоичных файлов Win32, скопируйте все необходимые во время выполнения файлы в папку win32. 

#### <a name="edit-the-app-manifest-to-enable-the-desktop-bridge-extensions"></a>Изменение манифеста приложения для включения расширений моста для классических приложений

Этот шаблон включает файл package.appxmanifest, с помощью которого можно добавлять расширения моста для классических приложений. Для изменения этого файла щелкните правой кнопкой мыши, выберите "Перейти к коду" и добавьте или измените следующие элементы: 

- `<Package xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10" xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10" xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities" IgnorableNamespaces="uap rescap">`

- `<TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.14393.0" MaxVersionTested="10.0.14393.0" />`

- `<rescap:Capability Name="runFullTrust" />`

- `<Application Id="MyDesktopAppStep1" Executable="win32\MyDesktopApp.exe" EntryPoint="Windows.FullTrustApplication">`

Вот полный пример файла манифеста: 

```xml
<?xml version="1.0" encoding="utf-8"?>
<Package xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
        xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"

        xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
        xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
        IgnorableNamespaces="uap rescap mp">
  <Identity Name="MyDesktopAppStep1"
            ProcessorArchitecture="x64"
            Publisher="CN=Microsoft Corporation, O=Microsoft Corporation, L=Redmond, S=Washington, C=US"
            Version="1.0.0.5" />
  <mp:PhoneIdentity PhoneProductId="6f6600a4-6da1-4d91-b493-35808d01f8de" PhonePublisherId="00000000-0000-0000-0000-000000000000" />
  <Properties>
    <DisplayName>MyDesktopAppStep1</DisplayName>
    <PublisherDisplayName>CN=Test</PublisherDisplayName>
    <Logo>Assets\SampleAppx.150x150.png</Logo>
  </Properties>
  <Resources>
    <Resource Language="en-us" />
  </Resources>
  <Dependencies>
    <TargetDeviceFamily Name="Windows.Desktop" 
                        MinVersion="10.0.14393.0" 
                        MaxVersionTested="10.0.14393.0" />
  </Dependencies>
  <Capabilities>
    <rescap:Capability Name="runFullTrust" />
  </Capabilities>
  <Applications>
    <Application Id="MyDesktopAppStep1" 
                 Executable="win32\MyDesktopApp.exe" 
                 EntryPoint="Windows.FullTrustApplication">
      <uap:VisualElements DisplayName="MyDesktopAppStep1" 
                          Description="MyDesktopAppStep1" 
                          BackgroundColor="#777777" 
                          Square150x150Logo="Assets\SampleAppx.150x150.png" 
                          Square44x44Logo="Assets\SampleAppx.44x44.png">
      </uap:VisualElements>
    </Application>
  </Applications>
</Package>
```

#### <a name="configure-the-win32-binaries"></a>Настройка двоичных файлов Win32

Чтобы добавить необходимые приложению двоичные файлы в выходной пакет, выберите каждый файл в Visual Studio. В его свойствах установите "Содержимое", а в качестве поведения при сборке укажите "Копировать более позднюю версию". 

![](images/desktop-to-uwp/net-3.png)

Если вам не нужно фиксировать двоичные файлы в репозитории исходного кода, можно исключить все файлы в папке win32 с помощью файла .gitignore. 

#### <a name="optional-use-wildcards-to-specify-the-files-in-your-win32-folder"></a>Необязательно. Использование подстановочных знаков для указания файлов в папке win32

Если приложению Win32 требуется несколько файлов, можно изменить файл проекта и задать шаблон, чтобы указать, какие файлы следует пометить как "Содержимое" на основе выражений с шаблонами. Откройте файл jsproj в текстовом редакторе и добавьте необходимые файлы, как показано ниже:

```xml
<Content Include="win32\*.dll">
  <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
</Content>
<Content Include="win32\*.exe">
  <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
</Content>
<Content Include="win32\*.config">
  <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
</Content>
<Content Include="win32\*.pdb">
  <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
</Content>
```

### <a name="step-2-enhance"></a>Этап 2. Улучшение

Чтобы вызвать доступные API UWP из кода Win32, добавьте ссылку на `\Program Files (x86)\Windows Kits\10\UnionMetadata\Windows.winmd`. Полный список API UWP, доступных для приложения, указан в статье [Поддерживаемые API UWP для приложений, преобразованных с помощью моста для классических приложений](desktop-to-uwp-supported-api.md).  

Поскольку этот файл не требуется в Windows 10, распространять его не нужно. В свойствах ссылки задайте свойству "Копирование локальных файлов" значение false.

![](images/desktop-to-uwp/net-4.png)

Используйте инструкции из этапа 1 для добавления двоичных файлов Win32. 

### <a name="step-3-extend"></a>Этап 3. Расширение 

В этом примере мы расширим приложение Win32 с помощью фоновой задачи. Для этого необходимо зарегистрировать фоновую задачу в файле package.appxmanifest приложения UWP и добавить ссылки на проект, в котором реализуется фоновая задача, как показано ниже.

```xml
<Extensions>
  <Extension Category="windows.backgroundTasks" 
              EntryPoint="BackgroundTasks.MyBackgroundTask">
    <BackgroundTasks>
      <Task Type="timer" />
    </BackgroundTasks>
  </Extension>
</Extensions>
```

Если фоновая задача реализуется с помощью C# или VB.NET, результат будет содержать двоичные файлы CoreCLR, которые необходимые обработать с помощью цепочки инструментов .NET Native перед отправкой в магазин, как описано в этапах 3 и 4. Создание appxupload со смешанными двоичными файлами.

### <a name="step-4-migrate"></a>Этап 4. Перенос

В этом сценарии уже есть точка входа UWP C#, поэтому добавлять еще один проект UWP не нужно. Однако необходимо добавить и настроить двоичные файлы Win32, следуя инструкциям на этапе 1.

Для выполнения процесса Win32 используйте API [**FullTrustProcessLauncher**](https://msdn.microsoft.com/library/windows/apps/Windows.ApplicationModel.FullTrustProcessLauncher). Для использования этих API потребуется добавить расширение рабочего стола и возможность *fullTrustProcess* в манифест приложения, как показано ниже: 

```xml
..
xmlns:desktop=http://schemas.microsoft.com/appx/manifest/desktop/windows10
..
<desktop:Extension Category="windows.fullTrustProcess" 
                    Executable="win32\MyDesktopApp.exe" />
```

## <a name="generate-packages-for-your-desktop-bridge-app"></a>Создание пакетов для приложения, преобразованного с помощью моста для классических приложений

После выполнения указанных выше инструкций можно приступить к созданию пакетов при помощи Visual Studio, как описано в разделе [Упаковка приложений UWP](..\packaging\packaging-uwp-apps.md). 

### <a name="steps-1-and-2-create-appxupload-with-win32-binaries"></a>Этапы 1 и 2. Создание appxupload с двоичными файлами Win32

Для отправки пакетов с возможностью *fullTrust* необходимо создать файл appxupload, включающий символы для каждой платформы в файле appxsym, и пакет, содержащий пакеты платформы appx.

На этапах 1 и 2 пакет не содержит двоичных файлов CoreCLR, поэтому можно выбрать любую платформу. Выберите "Нейтральная" и "Выпуск (любой ЦП)", как показано на рисунке ниже.

![](images/desktop-to-uwp/net-5.png)

Как только вы выберете параметр "Создать пакеты для магазина", мастер создаст файл appxupload, готовый к отправке в магазин.

### <a name="step-3-and-4-create-appxupload-with-mixed-binaries"></a>Этапы 3 и 4. Создание appxupload со смешанными двоичными файлами

Вам также следует создать сборку для конфигурации "Выпуск", и в этом случае необходимо указать целевые платформы, так как это необходимо .NET Native для создания собственных двоичных файлов для каждой платформы.

![](images/desktop-to-uwp/net-6.png)

Для создания нового файла appxupload мы создадим новый ZIP-архив, чтобы включить созданные файлы appxsym и appxbundle из папки _Test.

Создайте новый ZIP-файл, содержащий файлы appxsym и appxbundle, а затем смените его расширение на appxupload.

![](images/desktop-to-uwp/net-7.png)

<span id="debugging-anchor" />
## <a name="debugging-your-desktop-bridge-app"></a>Отладка приложения, преобразованного с помощью моста для классических приложений

Хотя можно запускать проекты в Visual Studio без отладки (CTRL+F5), известна проблема, связанная с тем, что Visual Studio не может автоматически подключиться к запущенному процессу. Однако подключение можно выполнить позже с помощью одного из следующих методов:

### <a name="attach-to-the-running-app"></a>Подключение к запущенному приложению

#### <a name="attach-to-an-existing-process"></a>Подключение к существующему процессу

После успешного запуска приложения с помощью сочетания клавиш CTRL+F5 вы можете выполнить подключение к процессу Win32. Но вы не сможете отладить модули .NET Native. 

![](images/desktop-to-uwp/net-8.png)

#### <a name="attach-to-an-installed-app"></a>Подключение к установленному приложению

Вы также можете выполнить подключение к любому существующему пакету Appx. Для этого выберите "Отладка" -> "Другие цели отладки" -> "Отладка установленного пакета приложения".

![](images/desktop-to-uwp/net-9.png)

В этом разделе можно выбрать локальный компьютер или подключиться к удаленному.

![](images/desktop-to-uwp/net-10.png)

С помощью этого параметра можно отладить код .NET Native.

### <a name="use-visual-studio-extension-to-debug-your-desktop-bridge-app"></a>Использование расширения Visual Studio для отладки приложения, преобразованного с помощью моста для классических приложений 

Если вы предпочитаете запускать отладку приложения клавишей F5, потребуется установить расширение Visual Studio 2017, [проект отладки моста для классических приложений](https://marketplace.visualstudio.com/items?itemName=VisualStudioProductTeam.DesktoptoUWPPackagingProject), из коллекции Visual Studio.

С помощью этого проекта можно отладить любое приложение Win32, переносимое на платформу UWP, с использованием Visual Studio (как описано в этом документе) или Desktop App Converter.

#### <a name="add-the-debugging-project-to-your-solution"></a>Добавление проекта отладки в решение

Чтобы начать, добавьте проект отладки моста для классических приложений в проект в составе решения.

![](images/desktop-to-uwp/net-11.png)

Для настройки этого проекта необходимо определить свойство PackageLayout в окне свойств для каждой конфигурации или платформы, которую требуется использовать для отладки.
Чтобы выполнить настройку для конфигурации "Отладка/x86", мы установим свойству макета пакета значение папки "bin\x86\debug" проекта UWP с помощью относительного пути: `..\MyDesktopApp.Package\bin\x86\Debug`. 

![](images/desktop-to-uwp/net-12.png)

Также изменим файл AppXFileLayout.xml, указав точку входа:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="14.0" 
         xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <MyProjectOutputPath>$(PackageLayout)</MyProjectOutputPath>
  </PropertyGroup>
  <ItemGroup>
    <LayoutFile Include="$(MyProjectOutputPath)\win32\MyDesktopApp.exe">
      <PackagePath>$(PackageLayout)\win32\MyDesktopApp.exe</PackagePath>
    </LayoutFile>
  </ItemGroup>
</Project>
```

Наконец следует настроить зависимости решения, чтобы обеспечить верный порядок сборки проектов. 

В качестве примера рассмотрим решение, созданное для этапа 3.

![](images/desktop-to-uwp/net-13.png)

Для настройки порядка сборки можно использовать конфигурацию "Зависимости проектов". Щелкните правой кнопкой мыши решение и выберите параметр "Зависимости проектов". Установив верные зависимости, можно проверить порядок сборки, как показано ниже (для этапа 3):

![](images/desktop-to-uwp/net-14.png)

<span id="known-issues-anchor" />
## <a name="known-issues-with-cvbnet-and-c-uwp-projects"></a>Известные проблемы проектов UWP на C#/VB.NET и C++

Если вы предпочитаете использовать проект на C# для упаковки приложения, необходимо учесть следующие известные проблемы. 

- **При сборке приложения в режиме "Отладка" происходит ошибка: Microsoft.Net.CoreRuntime.targets(235,5): ошибка : Приложения с пользовательскими исполняемыми файлами точек входа не поддерживаются. Проверьте атрибут Executable элемента Application в манифесте проекта.** В качестве обходного пути используйте режим "Выпуск".

- **Двоичные файлы Win32, хранящиеся в корневой папке проекта UWP, удаляются в режиме "Выпуск"**. Если вы храните двоичные файлы Win32 не в папке, компилятор .NET Native удалит их из конечного пакета, из-за чего возникнет ошибка проверки манифеста, так как точка входа исполняемого файла не будет найдена.


