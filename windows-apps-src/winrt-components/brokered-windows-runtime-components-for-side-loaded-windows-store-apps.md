---
title: Пересреда выполнения Windowsные компоненты брокера для загружаемого на сервер приложения UWP
description: В этом документе обсуждается Корпоративная функция, поддерживаемая Windows 10, позволяющая удобным приложениям .NET использовать существующий код, отвечающий за ключевые критически важные для бизнеса операции.
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP
ms.assetid: 81b3930c-6af9-406d-9d1e-8ee6a13ec38a
ms.localizationpriority: medium
ms.openlocfilehash: b28df646bb505889626ced8591c5ef9e6ece3f44
ms.sourcegitcommit: f561efbda5c1d47b85601d91d70d86c5332bbf8c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690350"
---
# <a name="brokered-windows-runtime-components-for-a-side-loaded-uwp-app"></a>Пересреда выполнения Windowsные компоненты брокера для загружаемого на сервер приложения UWP

В этой статье обсуждается Корпоративная функция, поддерживаемая Windows 10, позволяющая удобным приложениям .NET использовать существующий код, отвечающий за ключевые критически важные для бизнеса операции.

## <a name="introduction"></a>Введение

>**Обратите внимание** ,  пример кода, прилагаемого к этой статье, можно скачать для [Visual Studio 2015 & 2017](https://aka.ms/brokeredsample). Шаблон Microsoft Visual Studio для создания компонентов брокера среда выполнения Windows можно скачать здесь: [шаблон Visual Studio 2015, предназначенный для универсальных приложений Windows для Windows 10](https://marketplace.visualstudio.com/items?itemName=vs-publisher-713547.VS2015TemplateBrokeredComponents)

В Windows имеется новая функция, именуемая *брокером среда выполнения Windows компонентов для приложений, загружаемых на стороне*сервера. Мы используем термин IPC (межпроцессное взаимодействие), чтобы описать возможность запуска существующих ресурсов настольного программного обеспечения в одном процессе (компонент рабочего стола) при взаимодействии с этим кодом в приложении UWP. Это знакомая модель для разработчиков предприятий, так как приложения баз данных и приложения, использующие службы NT в Windows, совместно используют аналогичную архитектуру с несколькими процессами.

Загрузка неопубликованного приложения является важнейшим компонентом этой функции.
Корпоративные приложения не имеют общих потребительских Microsoft Store и в организациях предъявляются особые требования к безопасности, конфиденциальности, распределению, настройке и обслуживанию. Таким образом, модель загрузки неопубликованных элементов является обязательным условием для тех, кто использовал эту функцию и важную информацию о реализации.

Приложения, ориентированные на данные, являются ключевым объектом для этой архитектуры приложения. Это преденсконцед, что существующие бизнес-правила, например в SQL Server, будут распространенной частью компонента Desktop. Это, безусловно, не единственный тип функциональности, который может быть предложенной компонентом рабочего стола, но большая часть спроса на эту функцию связана с существующими данными и бизнес-логикой.

Наконец, учитывая подавляющее повышение уязвимости среды выполнения .NET и языка C\# в корпоративной разработке, эта функция была разработана с учетом использования .NET как для приложений UWP, так и для компонентов настольных систем. Хотя существуют и другие языки и среды выполнения для приложения UWP, в прилагаемом образце показан только язык C\#и он ограничен исключительно средой выполнения .NET.

## <a name="application-components"></a>Компоненты приложения

>**Обратите внимание** ,  эта функция предназначена исключительно для использования с .NET. Как клиентское приложение, так и компонент рабочего стола должны быть созданы с помощью .NET.

**Модель приложения**

Эта функция построена на основе общей архитектуры приложений, известной как MVVM (представление представления модели — модель). Таким образом, предполагается, что «модель» полностью размещена в компоненте Desktop. Поэтому следует сразу же очевидно, что компонент настольного компьютера будет «без монитора» (т. е. не содержит пользовательского интерфейса). Представление будет полностью содержаться в загруженном на сервер корпоративном приложении. Хотя не требуется, чтобы это приложение создавалось с конструкцией "View-Model", предполагается, что использование этого шаблона будет распространено.

**Компонент рабочего стола**

Компонент рабочего стола в этой функции — это новый тип приложения, представленный в составе этой функции. Этот компонент рабочего стола может быть написан только на C\# и должен быть предназначен для .NET 4,6 или более поздней версии для Windows 10. Тип проекта — это гибридная среда CLR, предназначенная для использования в качестве межпроцессного формата взаимодействия, который состоит из типов и классов UWP, тогда как компоненту рабочего стола разрешено вызывать все части библиотеки классов среды выполнения .NET. Далее будет подробно рассмотрено влияние проекта Visual Studio. Эта гибридная конфигурация позволяет маршалировать типы UWP между приложением, созданным на настольных компонентах, и позволяет вызывать код среды CLR в реализации компонента настольных систем.

**Архивирован**

Контракт между загружаемым приложением и компонентом Desktop описывается в терминах системы типов UWP. Это подразумевает объявление одного или нескольких классов C\#, которые могут представлять UWP. Дополнительные сведения о создании класса среда выполнения Windows с помощью\#C см. в разделе MSDN [создание среда выполнения Windows компонентов на языке c\# и Visual Basic](https://docs.microsoft.com/previous-versions/windows/apps/br230301(v=vs.140)) .

>**Обратите внимание** ,  перечисления в настоящее время не поддерживаются в контракте среда выполнения Windows компонентов между компонентом рабочего стола и загруженным приложением.

**Приложение, загруженное на стороне**

Загружаемое приложение — это нормальное приложение UWP в каждой части, за исключением одного: оно загружается параллельно, а не устанавливается с помощью Microsoft Store. Большая часть механизмов установки идентична: Упаковка манифеста и приложения аналогична (Дополнительные сведения о манифесте подробно описаны далее). После включения загрузки неопубликованного приложения простой сценарий PowerShell может установить необходимые сертификаты и само приложение. Это обычная рекомендация, когда приложение, загруженное на сервер, проходит тест сертификации WACK, включенный в меню проект или магазин в Visual Studio.

>**Примечание** . Загрузка неопубликованных приложений может быть включена в параметрах —&gt; Update & Security-&gt; для разработчиков.

Важно отметить, что механизм брокера приложений, поставляемый в составе Windows 10, является только 32-разрядным. Компонент Desktop должен иметь 32 бит.
Параллельно загружаемые приложения могут быть 64-разрядными (при условии, что зарегистрированы как 64-разрядные, так и 32-битовые прокси), но это будет нетипично. Создание приложения, загружаемого на стороне, на языке C\# с помощью обычной "нейтральной" конфигурации и "предпочитать 32-bit" по умолчанию естественным образом создает приложения, загружаемые на стороне 32.

**Создание экземпляров и доменов приложений сервера**

Каждое приложение, загруженное на односторонней основе, получает собственный экземпляр сервера компонента Service Broker (так называемый "несколько экземпляров"). Серверный код выполняется внутри одного домена AppDomain. Это позволяет использовать несколько версий библиотек в отдельных экземплярах. Например, приложению A требуется V 1.1 компонента и приложению б требуется v2. Они четко разделены с помощью компонентов V 1.1 и v2 в отдельных серверных каталогах и указывают приложению, какой сервер поддерживает правильную версию.

Реализация серверного кода может совместно использоваться несколькими экземплярами сервера компонента Application Broker, указывая несколько приложений в одном и том же каталоге сервера. По-прежнему будут существовать несколько экземпляров сервера брокера приложений, но они будут работать в одинаковом коде. Все компоненты реализации, используемые в одном приложении, должны находиться в одном пути.

## <a name="defining-the-contract"></a>Определение контракта

Первым шагом в создании приложения с помощью этой функции является создание контракта между загружаемым приложением и компонентом рабочего стола. Это необходимо сделать исключительно с помощью типов среда выполнения Windows.
К счастью, эти классы легко объявить с помощью классов C\#. Однако при определении этих бесед необходимо учитывать важные моменты, которые рассматриваются в следующем разделе.

Последовательность для определения контракта представлена следующим образом:

**Шаг 1.** Создайте новую библиотеку классов в Visual Studio. Не забудьте создать проект с помощью шаблона **библиотеки классов** , а не шаблона **компонента Среда выполнения Windows** .

Очевидна реализация, но этот раздел охватывает только определение межпроцессного контракта. В сопровождающий пример входит следующий класс (EnterpriseServer.cs), начальная форма которого выглядит следующим образом:

```csharp
namespace Fabrikam
{
    public sealed class EnterpriseServer
    {

        public ILis<String> TestMethod(String input)
        {
            throw new NotImplementedException();
        }
        
        public IAsyncOperation<int> FindElementAsync(int input)
        {
            throw new NotImplementedException();
        }
        
        public string[] RetrieveData()
        {
            throw new NotImplementedException();
        }
        
        public event EventHandler<string> PeriodicEvent;
    }
}
```

Это определяет класс "Ентерприсесервер", который можно создать из загруженного приложения. Этот класс предоставляет функции, обещанные в RuntimeClass. RuntimeClass можно использовать для создания ссылки на WinMD-объект, который будет включен в приложение, загруженное в сторону загрузки.

**Шаг 2.** Измените файл проекта вручную, чтобы изменить тип выходных данных проекта на **Среда выполнения Windows компонент**.

Чтобы сделать это в Visual Studio, щелкните правой кнопкой мыши только что созданный проект и выберите "Выгрузить проект", затем щелкните еще раз и выберите "изменить Ентерприсесервер. csproj", чтобы открыть файл проекта, XML-файл для редактирования.

В открывшемся файле найдите тег \<OutputType\> и измените его значение на "winmdobj".

**Шаг 3.** Создайте правило сборки, которое создает ссылку на файл метаданных Windows (WINMD-файл). т. е. не имеет реализации.

**Шаг 4.** Создайте правило сборки, которое создает файл метаданных Windows "реализация", т. е. имеет те же метаданные, но также включает реализацию.

Это делается с помощью следующих сценариев. Добавьте скрипты в командную строку события после сборки, в **свойствах** проекта > **события сборки**.

> **Обратите внимание** , что сценарий отличается в зависимости от версии Windows, для которой выполняется настройка (Windows 10), и от используемой версии Visual Studio.

**Visual Studio 2015**
```cmd
    call "$(DevEnvDir)..\..\vc\vcvarsall.bat" x86 10.0.14393.0

    md "$(TargetDir)"\impl    md "$(TargetDir)"\reference

    erase "$(TargetDir)\impl\*.winmd"
    erase "$(TargetDir)\impl\*.pdb"
    rem erase "$(TargetDir)\reference\*.winmd"

    xcopy /y "$(TargetPath)" "$(TargetDir)impl"
    xcopy /y "$(TargetDir)*.pdb" "$(TargetDir)impl"

    winmdidl /nosystemdeclares /metadata_dir:C:\Windows\System32\Winmetadata "$(TargetPath)"

    midl /metadata_dir "%WindowsSdkDir%UnionMetadata" /iid "$(SolutionDir)BrokeredProxyStub\$(TargetName)_i.c" /env win32 /x86 /h   "$(SolutionDir)BrokeredProxyStub\$(TargetName).h" /winmd "$(TargetName).winmd" /W1 /char signed /nologo /winrt /dlldata "$(SolutionDir)BrokeredProxyStub\dlldata.c" /proxy "$(SolutionDir)BrokeredProxyStub\$(TargetName)_p.c"  "$(TargetName).idl"
    mdmerge -n 1 -i "$(ProjectDir)bin\$(ConfigurationName)" -o "$(TargetDir)reference" -metadata_dir "%WindowsSdkDir%UnionMetadata" -partial

    rem erase "$(TargetPath)"

```


**Visual Studio 2017**
```cmd
    call "$(DevEnvDir)..\..\vc\auxiliary\build\vcvarsall.bat" x86 10.0.16299.0

    md "$(TargetDir)"\impl
    md "$(TargetDir)"\reference

    erase "$(TargetDir)\impl\*.winmd"
    erase "$(TargetDir)\impl\*.pdb"
    rem erase "$(TargetDir)\reference\*.winmd"

    xcopy /y "$(TargetPath)" "$(TargetDir)impl"
    xcopy /y "$(TargetDir)*.pdb" "$(TargetDir)impl"

    winmdidl /nosystemdeclares /metadata_dir:C:\Windows\System32\Winmetadata "$(TargetPath)"

    midl /metadata_dir "%WindowsSdkDir%UnionMetadata" /iid "$(SolutionDir)BrokeredProxyStub\$(TargetName)_i.c" /env win32 /x86 /h "$(SolutionDir)BrokeredProxyStub\$(TargetName).h" /winmd "$(TargetName).winmd" /W1 /char signed /nologo /winrt /dlldata "$(SolutionDir)BrokeredProxyStub\dlldata.c" /proxy "$(SolutionDir)BrokeredProxyStub\$(TargetName)_p.c"  "$(TargetName).idl"
    mdmerge -n 1 -i "$(ProjectDir)bin\$(ConfigurationName)" -o "$(TargetDir)reference" -metadata_dir "%WindowsSdkDir%UnionMetadata" -partial

    rem erase "$(TargetPath)"
```

После создания ссылки на **WinMD** -объект (в папке "Reference" в целевой папке проекта) он переносится (копируется) в каждый заданный проект приложения, загружаемый на сервер, и на который указывает ссылка. Это будет описано далее в следующем разделе. Структура проекта, реализованная в приведенных выше правилах сборки, гарантирует, что реализация и ссылка на **WinMD** находятся в явно разделенных каталогах в иерархии сборки, чтобы избежать путаницы.

## <a name="side-loaded-applications-in-detail"></a>Подробные загружаемые на стороне приложения
Как уже говорилось ранее, загруженное приложение создается как любое другое приложение UWP, но есть еще один дополнительный элемент: объявление о доступности RuntimeClass в манифесте приложения, загруженном в сторону. Это позволяет приложению просто написать новый, чтобы получить доступ к функциональным возможностям компонента Desktop. Новая запись манифеста в разделе <Extension> описывает RuntimeClass, реализованные в классическом компоненте, и сведения о его расположении. Это содержимое объявления в манифесте приложения одинаково для приложений, предназначенных для Windows 10. Пример.

```XML
<Extension Category="windows.activatableClass.inProcessServer">
    <InProcessServer>
        <Path>clrhost.dll</Path>
        <ActivatableClass ActivatableClassId="Fabrikam.EnterpriseServer" ThreadingModel="both">
            <ActivatableClassAttribute Name="DesktopApplicationPath" Type="string" Value="c:\test" />
        </ActivatableClass>
    </InProcessServer>
</Extension>
```

Категория — Инпроцесссервер, так как в категории Аутофпроцесссервер есть несколько записей, неприменимых к этой конфигурации приложения. Обратите внимание, что компонент <Path> должен всегда содержать клрхост. dll (Однако это **не** является обязательным, и указание другого значения не приведет к сбою в неопределенных способах).

Раздел <ActivatableClass> аналогичен истинному в процессе RuntimeClass, предпочтительному компонентом среда выполнения Windows в пакете приложения. <ActivatableClassAttribute> является новым элементом, а атрибуты Name = "Десктопаппликатионпас" и Type = "String" являются обязательными и инвариантными. Атрибут value указывает на расположение, в котором находится WinMD-сервер реализации компонента рабочего стола (Подробнее об этом см. в следующем разделе). Каждый RuntimeClass, предпочитаемый компонентом Desktop, должен иметь собственное <ActivatableClass> дерево элементов. Запись ActivatableClassId должна совпадать с полным именем пространства имен RuntimeClass.

Как упоминалось в разделе «Определение контракта», ссылка на проект должна быть сделана в справочнике по компоненту на рабочем столе. Система проектов Visual Studio обычно создает структуру каталогов с двумя уровнями с тем же именем. В примере это Ентерприсеипкаппликатион\\Ентерприсеипкаппликатион. Ссылка на **WinMD** -объект вручную копируется в этот каталог второго уровня, а затем используется диалоговое окно ссылки на проект (нажмите кнопку **Обзор...** кнопка) для нахождение и ссылки на этот **WinMD**. После этого пространство имен верхнего уровня для компонента рабочего стола (например, Fabrikam) должно отображаться как узел верхнего уровня в части ссылки проекта.

>**Примечание** . Очень важно использовать **ссылку на WinMD** в приложении, загруженном в сторону. Если вы случайно переносите **WinMD** -файлы реализации в каталог приложения, загруженный в сторону, и ссылаетесь на него, скорее всего, будет получена ошибка, связанная с "не удается найти IStringable". Это гарантирует, что указан неправильный **WinMD** . Правила после сборки в приложении IPC Server (подробное описание в следующем разделе) тщательно разделяют эти два **WinMD** в отдельные каталоги.

Переменные среды (особенно% ProgramFiles%) может использоваться в <ActivatableClassAttribute Value="path">. Как отмечалось ранее, брокер приложений поддерживает только 32-bit, поэтому% ProgramFiles% будет разрешаться в C:\\Program Files (x86), если приложение выполняется в 64-разрядной ОС.

## <a name="desktop-ipc-server-detail"></a>Сведения о сервере IPC Desktop

В предыдущих двух разделах описывается объявление класса и механика переноса ссылки на **WinMD** в проект приложения, загруженный на сервер. Основная часть оставшейся работы в классическом компоненте включает реализацию. Поскольку вся точка компонента рабочего стола должна быть способна вызывать Настольный код (обычно для повторного использования существующих ресурсов кода), проект должен быть настроен особым образом.
Как правило, проект Visual Studio, использующий .NET, использует один из двух «Profiles».
Один предназначен для настольных систем (". NetFramework "), а один предназначен для части приложения UWP среды CLR (". NetCore "). Компонент рабочего стола в этой функции является гибридным для этих двух компонентов. В результате раздел References очень тщательно создается для смешения этих двух профилей.

В стандартном проекте приложения UWP нет явных ссылок на проект, так как в ней неявно содержится полностью среда выполнения Windows области API.
Обычно создаются только другие ссылки между проектами. Однако проект компонента рабочего стола имеет очень специальный набор ссылок. Он начинает жизнь как проект классического рабочего стола\\библиотека классов и, следовательно, является настольным проектом. Поэтому необходимо выполнить явные ссылки на среда выполнения Windows API (через ссылки на файлы **WinMD** ). Добавьте правильные ссылки, как показано ниже.

```XML
<ItemGroup>
    <!-- These reference are added by VS automatically when you create a Class Library project-->
    <Reference Include="System" />
    <Reference Include="System.Core" />
    <Reference Include="System.Xml.Linq" />
    <Reference Include="System.Data.DataSetExtensions" />
    <Reference Include="Microsoft.CSharp" />
    <Reference Include="System.Data" />
    <Reference Include="System.Net.Http" />
<Reference Include="System.Xml" />
    <!-- These reference should be added manually by editing .csproj file-->

    <Reference Include="System.Runtime.WindowsRuntime, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089, processorArchitecture=MSIL">
      <HintPath>$(MSBuildProgramFiles32)\Microsoft SDKs\NETCoreSDK\System.Runtime.WindowsRuntime\4.0.10\lib\netcore50\System.Runtime.WindowsRuntime.dll</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows">
      <HintPath>$(MSBuildProgramFiles32)\Windows Kits\10\UnionMetadata\Facade\Windows.WinMD</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Foundation.FoundationContract">
      <HintPath>$(MSBuildProgramFiles32)\Windows Kits\10\References\Windows.Foundation.FoundationContract\1.0.0.0\Windows.Foundation.FoundationContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Foundation.UniversalApiContract">
      <HintPath>$(MSBuildProgramFiles32)\Windows Kits\10\References\Windows.Foundation.UniversalApiContract\1.0.0.0\Windows.Foundation.UniversalApiContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Networking.Connectivity.WwanContract">
      <HintPath>$(MSBuildProgramFiles32)\Windows Kits\10\References\Windows.Networking.Connectivity.WwanContract\1.0.0.0\Windows.Networking.Connectivity.WwanContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Activation.ActivatedEventsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Activation.ActivatedEventsContract\1.0.0.0\Windows.ApplicationModel.Activation.ActivatedEventsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Activation.ActivationCameraSettingsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Activation.ActivationCameraSettingsContract\1.0.0.0\Windows.ApplicationModel.Activation.ActivationCameraSettingsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Activation.ContactActivatedEventsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Activation.ContactActivatedEventsContract\1.0.0.0\Windows.ApplicationModel.Activation.ContactActivatedEventsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Activation.WebUISearchActivatedEventsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Activation.WebUISearchActivatedEventsContract\1.0.0.0\Windows.ApplicationModel.Activation.WebUISearchActivatedEventsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Background.BackgroundAlarmApplicationContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Background.BackgroundAlarmApplicationContract\1.0.0.0\Windows.ApplicationModel.Background.BackgroundAlarmApplicationContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Calls.LockScreenCallContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Calls.LockScreenCallContract\1.0.0.0\Windows.ApplicationModel.Calls.LockScreenCallContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Resources.Management.ResourceIndexerContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Resources.Management.ResourceIndexerContract\1.0.0.0\Windows.ApplicationModel.Resources.Management.ResourceIndexerContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Search.Core.SearchCoreContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Search.Core.SearchCoreContract\1.0.0.0\Windows.ApplicationModel.Search.Core.SearchCoreContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Search.SearchContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Search.SearchContract\1.0.0.0\Windows.ApplicationModel.Search.SearchContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.ApplicationModel.Wallet.WalletContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.ApplicationModel.Wallet.WalletContract\1.0.0.0\Windows.ApplicationModel.Wallet.WalletContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Devices.Custom.CustomDeviceContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Devices.Custom.CustomDeviceContract\1.0.0.0\Windows.Devices.Custom.CustomDeviceContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Devices.Portable.PortableDeviceContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Devices.Portable.PortableDeviceContract\1.0.0.0\Windows.Devices.Portable.PortableDeviceContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Devices.Printers.Extensions.ExtensionsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Devices.Printers.Extensions.ExtensionsContract\1.0.0.0\Windows.Devices.Printers.Extensions.ExtensionsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Devices.Printers.PrintersContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Devices.Printers.PrintersContract\1.0.0.0\Windows.Devices.Printers.PrintersContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Devices.Scanners.ScannerDeviceContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Devices.Scanners.ScannerDeviceContract\1.0.0.0\Windows.Devices.Scanners.ScannerDeviceContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Devices.Sms.LegacySmsApiContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Devices.Sms.LegacySmsApiContract\1.0.0.0\Windows.Devices.Sms.LegacySmsApiContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Gaming.Preview.GamesEnumerationContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Gaming.Preview.GamesEnumerationContract\1.0.0.0\Windows.Gaming.Preview.GamesEnumerationContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Globalization.GlobalizationJapanesePhoneticAnalyzerContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Globalization.GlobalizationJapanesePhoneticAnalyzerContract\1.0.0.0\Windows.Globalization.GlobalizationJapanesePhoneticAnalyzerContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Graphics.Printing3D.Printing3DContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Graphics.Printing3D.Printing3DContract\1.0.0.0\Windows.Graphics.Printing3D.Printing3DContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Management.Deployment.Preview.DeploymentPreviewContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Management.Deployment.Preview.DeploymentPreviewContract\1.0.0.0\Windows.Management.Deployment.Preview.DeploymentPreviewContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Management.Workplace.WorkplaceSettingsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Management.Workplace.WorkplaceSettingsContract\1.0.0.0\Windows.Management.Workplace.WorkplaceSettingsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Media.Capture.AppCaptureContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Media.Capture.AppCaptureContract\1.0.0.0\Windows.Media.Capture.AppCaptureContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Media.Capture.CameraCaptureUIContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Media.Capture.CameraCaptureUIContract\1.0.0.0\Windows.Media.Capture.CameraCaptureUIContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Media.Devices.CallControlContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Media.Devices.CallControlContract\1.0.0.0\Windows.Media.Devices.CallControlContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Media.MediaControlContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Media.MediaControlContract\1.0.0.0\Windows.Media.MediaControlContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Media.Playlists.PlaylistsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Media.Playlists.PlaylistsContract\1.0.0.0\Windows.Media.Playlists.PlaylistsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Media.Protection.ProtectionRenewalContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Media.Protection.ProtectionRenewalContract\1.0.0.0\Windows.Media.Protection.ProtectionRenewalContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Networking.NetworkOperators.LegacyNetworkOperatorsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Networking.NetworkOperators.LegacyNetworkOperatorsContract\1.0.0.0\Windows.Networking.NetworkOperators.LegacyNetworkOperatorsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Networking.Sockets.ControlChannelTriggerContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Networking.Sockets.ControlChannelTriggerContract\1.0.0.0\Windows.Networking.Sockets.ControlChannelTriggerContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Security.EnterpriseData.EnterpriseDataContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Security.EnterpriseData.EnterpriseDataContract\1.0.0.0\Windows.Security.EnterpriseData.EnterpriseDataContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Security.ExchangeActiveSyncProvisioning.EasContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Security.ExchangeActiveSyncProvisioning.EasContract\1.0.0.0\Windows.Security.ExchangeActiveSyncProvisioning.EasContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Services.Maps.GuidanceContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Services.Maps.GuidanceContract\1.0.0.0\Windows.Services.Maps.GuidanceContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Services.Maps.LocalSearchContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Services.Maps.LocalSearchContract\1.0.0.0\Windows.Services.Maps.LocalSearchContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.System.Profile.SystemManufacturers.SystemManufacturersContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.System.Profile.SystemManufacturers.SystemManufacturersContract\1.0.0.0\Windows.System.Profile.SystemManufacturers.SystemManufacturersContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.System.Profile.ProfileHardwareTokenContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.System.Profile.ProfileHardwareTokenContract\1.0.0.0\Windows.System.Profile.ProfileHardwareTokenContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.System.Profile.ProfileRetailInfoContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.System.Profile.ProfileRetailInfoContract\1.0.0.0\Windows.System.Profile.ProfileRetailInfoContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.System.UserProfile.UserProfileContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.System.UserProfile.UserProfileContract\1.0.0.0\Windows.System.UserProfile.UserProfileContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.System.UserProfile.UserProfileLockScreenContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.System.UserProfile.UserProfileLockScreenContract\1.0.0.0\Windows.System.UserProfile.UserProfileLockScreenContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.UI.ApplicationSettings.ApplicationsSettingsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.UI.ApplicationSettings.ApplicationsSettingsContract\1.0.0.0\Windows.UI.ApplicationSettings.ApplicationsSettingsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.UI.Core.AnimationMetrics.AnimationMetricsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.UI.Core.AnimationMetrics.AnimationMetricsContract\1.0.0.0\Windows.UI.Core.AnimationMetrics.AnimationMetricsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.UI.Core.CoreWindowDialogsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.UI.Core.CoreWindowDialogsContract\1.0.0.0\Windows.UI.Core.CoreWindowDialogsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.UI.Xaml.Hosting.HostingContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.UI.Xaml.Hosting.HostingContract\1.0.0.0\Windows.UI.Xaml.Hosting.HostingContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
    <Reference Include="Windows.Web.Http.Diagnostics.HttpDiagnosticsContract">
      <HintPath>$(MsBuildProgramFiles32)\Windows Kits\10\References\Windows.Web.Http.Diagnostics.HttpDiagnosticsContract\1.0.0.0\Windows.Web.Http.Diagnostics.HttpDiagnosticsContract.winmd</HintPath>
      <Private>False</Private>
    </Reference>
</ItemGroup>
```

Приведенные выше ссылки являются тщательным сочетанием еференцес, которые важны для правильной работы этого гибридного сервера. Протокол должен открыть CSPROJ-файл (как описано в разделе изменение файла OutputType) и добавить эти ссылки при необходимости.

После правильной настройки ссылок следует реализовать функциональность сервера. См. раздел рекомендации [по взаимодействию с среда выполнения Windows компонентами (приложения UWP, использующие C\#/ВБ/C++ и XAML)](https://docs.microsoft.com/previous-versions/windows/apps/hh750311(v=win.10)).
Задача заключается в создании библиотеки DLL компонента среда выполнения Windows, которая может вызывать Настольный код в рамках своей реализации. Прилагаемый пример включает основные шаблоны, используемые в среда выполнения Windows:

-   Вызовы методов

-   среда выполнения Windows источников событий компонентом рабочего стола

-   среда выполнения Windows асинхронных операций

-   Возвращение массивов базовых типов

**Установка**

Чтобы установить приложение, скопируйте **WinMD** -файлы реализации в правильный каталог, указанный в манифесте связанного с ним приложения: <ActivatableClassAttribute>value = "путь". Кроме того, скопируйте все связанные файлы поддержки и библиотеку прокси-сервера или заглушки (эти сведения описаны ниже). Если не скопировать реализацию **WinMD** в расположение каталога сервера, все вызовы приложения, загруженные в RuntimeClass, будут вызывать ошибку "класс не зарегистрирован". Сбой установки прокси-сервера или заглушки (или неудачной регистрации) приведет к сбою всех вызовов без возвращаемых значений. Эта последняя ошибка часто **не** связана с видимыми исключениями.
Если из-за ошибки конфигурации наблюдаются исключения, они могут ссылаться на "недопустимое приведение".

**Рекомендации по реализации сервера**

Рабочий стол среда выполнения Windows Server можно рассматривать как "Рабочий" или "задача" на основе. Каждый вызов сервера работает в потоке, отличном от пользовательского интерфейса, и весь код должен поддерживать работу с несколькими потоками и быть в безопасности. Какая часть загруженного в приложение приложения также важна для вызова функциональности сервера. Крайне важно всегда избегать вызова долго выполняемого кода из любого потока пользовательского интерфейса в приложении, загруженном в сторону. Это можно сделать двумя основными способами.

1.  При вызове функциональных возможностей сервера из потока пользовательского интерфейса всегда используйте асинхронный шаблон в общедоступной контактной зоне сервера и реализации.

2.  Вызовите функции сервера из фонового потока в приложении, загруженном на сервер.

**среда выполнения Windows Async на сервере**

Учитывая характер межпроцессной модели приложения, вызовы к серверу имеют больше издержек, чем код, выполняемый исключительно внутри процесса. Обычно можно вызвать простое свойство, которое возвращает значение в памяти, так как оно будет выполняться достаточно быстро, так как блокировка потока пользовательского интерфейса не является проблемой. Однако любой вызов, включающий ввод-вывод любого рода (в том числе все операции обработки файлов и извлечения базы данных), потенциально может блокировать вызывающий поток пользовательского интерфейса и привести к завершению работы приложения из-за недостаточной скорости реагирования. Кроме того, вызовы свойств объектов в этой архитектуре приложения не рекомендуются для повышения производительности.
Это более подробно описано в следующем разделе.

Правильно реализованный сервер обычно реализует вызовы, выполняемые непосредственно из потоков пользовательского интерфейса через шаблон асинхронного среда выполнения Windows. Это можно реализовать, следуя этому шаблону. Во-первых, объявление (опять же, из сопроводительного примера):

```csharp
public IAsyncOperation<int> FindElementAsync(int input)
```

Он объявляет среда выполнения Windows асинхронную операцию, которая возвращает целое число.
Реализация асинхронной операции обычно имеет вид:

```csharp
return Task<int>.Run( () =>
{
    int retval = ...
    // execute some potentially long-running code here 
}).AsAsyncOperation<int>();

```

>**Примечание** . Обычно при написании реализации ожидаются некоторые другие потенциально длительные операции. В этом случае необходимо объявить код **задачи. Run** :

```csharp
return Task<int>.Run(async () =>
{
    int retval = ...
    // execute some potentially long-running code here 
    await ... // some other WinRT async operation or Task
}).AsAsyncOperation<int>();
```

Клиенты этого асинхронного метода могут ожидать эту операцию, как и любые другие среда выполнения Windows операции айснк.

**Вызов функций сервера из фонового потока приложения**

Поскольку как правило, клиент и сервер записываются одной и той же организацией, при программировании может быть принято, что все вызовы к серверу будут выполняться фоновым потоком в приложении, загруженном в сторону загрузки. Прямой вызов, собирающий один или несколько пакетов данных с сервера, можно сделать из фонового потока. Когда результаты полностью извлекаются, пакет данных, который находится в памяти в процессе приложения, обычно может быть получен непосредственно из потока пользовательского интерфейса. Объекты C @ no__t_0_ естественным образом связаны между фоновыми потоками и потоками пользовательского интерфейса, поэтому они особенно полезны для такого рода вызывающих шаблонов.\#

## <a name="creating-and-deploying-the-windows-runtime-proxy"></a>Создание и развертывание прокси-сервера среда выполнения Windows

Поскольку подход IPC включает упаковку среда выполнения Windows интерфейсов между двумя процессами, необходимо использовать глобально зарегистрированный среда выполнения Windows прокси-сервер и заглушку.

**Создание учетной записи-посредника в Visual Studio**

Процесс создания и регистрации прокси-серверов и заглушек для использования внутри обычного пакета приложения UWP описан в разделе [Создание событий в среда выполнения Windows компонентах](https://docs.microsoft.com/previous-versions/windows/apps/dn169426(v=vs.140)).
Действия, описанные в этой статье, сложнее, чем описано ниже, так как это включает регистрацию прокси-сервера или заглушки внутри пакета приложения (в отличие от регистрации в глобальном приложении).

**Шаг 1.** С помощью решения для проекта компонента рабочего стола создайте проект прокси-сервера или заглушки в Visual Studio:

**Решение > Добавить > Проект > Visual C++ > Консоль Win32 выберите параметр DLL.**

Для приведенных ниже действий предполагается, что серверный компонент называется **мивинрткомпонент**.

**Шаг 3.** Удалите все файлы CPP/H из проекта.

**Шаг 4.** В предыдущем разделе «Определение контракта» содержится команда, выполняемая после сборки, которая запускает **winmdidl. exe**, **MIDL. exe**, **мдмерже. exe**и т. д. Один из выходных данных этапа MIDL этой команды после сборки создает четыре важных выхода:

a) DLLDATA. c

б) файл заголовка (например, Мивинрткомпонент. h)

в) \*файл\_i. c (например, Мивинрткомпонент\_i. c)

d) файл \*\_p. c (например, Мивинрткомпонент\_p. c)

**Шаг 5.** Добавьте эти четыре созданных файла в проект "Мивинртпрокси".

**Шаг 6.** Добавьте DEF-файл в проект "Мивинртпрокси" **(проект > добавить новый элемент > код > файл определения модуля**) и обновите содержимое следующим образом:

Библиотека Мивинрткомпонент. прокси. dll

ЭКСПОРТ

DllCanUnloadNow закрытый

DllGetClassObject закрытый

Функция DllRegisterServer PRIVATE

DllUnregisterServer закрытый

**Шаг 7.** Откройте свойства проекта "Мивинртпрокси":

**Комфигуратион свойства > Общее > имя целевого объекта:**

Мивинрткомпонент. прокси

**Определение препроцессора C/C++ > > Добавление**

Платформа\_WINDOWS; РЕГИСТРАЦИЯ\_прокси\_DLL "

**C/C++ > предкомпилированный заголовок: выберите "не использовать предкомпилированный заголовок"**

**Компоновщик > Общие > игнорировать библиотеку импорта: выберите "Да"**

**Компоновщик > входные > Дополнительные зависимости: Add rpcrt4. lib; рунтимеобжект. lib**

**Компоновщик > метаданные Windows > создать метаданные Windows: выберите "нет"**

**Шаг 8.** Создайте проект "Мивинртпрокси".

**Развертывание прокси-сервера**

Прокси-сервер должен быть глобально зарегистрирован. Самый простой способ сделать это — сделать так, чтобы процесс установки вызывал функцию DllRegisterServer на прокси-библиотеке DLL. Обратите внимание, что поскольку компонент поддерживает только серверы, созданные для платформы x86 (т. е. без поддержки 64 разрядов), простейшей конфигурацией является использование 32-разрядного сервера, 32-разрядный прокси-сервер и 32-разрядное приложение, загружаемое на сервер. Прокси-сервер обычно находится рядом с реализацией **WinMD** для настольного компонента.

Необходимо выполнить один дополнительный шаг настройки. Чтобы загрузить и запустить прокси-сервер в процессе загрузки, каталог должен быть помечен как "Read/Execute" для ALL_APPLICATION_PACKAGES. Это делается с помощью средства командной строки **icacls. exe** . Эта команда должна выполняться в каталоге, где находится реализация **WinMD** , а также прокси-сервер или библиотека DLL-заглушек:

*icacls. /T/Грант \*S-1-15-2-1: RX*

## <a name="patterns-and-performance"></a>Шаблоны и производительность

Очень важно, чтобы производительность межпроцессного транспорта была тщательно отслеживаться. Кросс-процессный вызов по крайней мере в два раза больше затрат на внутрипроцессный вызов. Создание «неактивных» бесед, а также повторная передача больших объектов, таких как растровые изображения, может привести к непредвиденной и нежелательной производительности приложения.

Ниже приведен неисчерпывающий список моментов, которые следует учитывать:

-   Вызовы синхронных методов из потока пользовательского интерфейса приложения на сервер всегда следует избегать. Вызовите метод из фонового потока в приложении, а затем используйте Коревиндовдиспатчер для получения результатов в поток пользовательского интерфейса, если это необходимо.

-   Вызов асинхронных операций из потока пользовательского интерфейса приложения является надежным, но следует учитывать проблемы производительности, описанные ниже.

-   При выполнении небольшого объема передаваемых результатов уменьшается Многопроцессная беседа. Обычно это выполняется с помощью конструкции массива среда выполнения Windows.

-   Возврат *списка<T>* , где *t* — это объект из асинхронной операции или выборки свойств, приведет к большому количеству интерактивных бесед. Например, предположим, что вы возвращаете*список&lt;людей&gt;* объекты. Каждый проход итерации будет вызываться между процессами. Каждый возвращаемый объект *People* представлен прокси-сервером, и каждый вызов метода или свойства для этого отдельного объекта приведет к вызову между процессами. Таким образом, список "безобидным" *&lt;людям&gt;* Object, где *Count* является большим, приведет к большому числу медленных вызовов. Повышение производительности при выполнении групповой пересылки структур содержимого в массиве. Пример.

```csharp
struct PersonStruct
{
    String LastName;
    String FirstName;
    int Age;
   // etc.
}
```

Затем возвратите * Персонструкт\[\]* вместо *List&lt;персонобжект&gt;* .
Все данные переключаются по одному межпроцессному прыжку.

Как и во всех соображениях производительности, измерение и тестирование являются критически важными. В идеале данные телеметрии следует вставлять в различные операции, чтобы определить, как долго они выполняются. Важно измерять диапазон: например, сколько времени действительно требуется для использования всех объектов *People* для конкретного запроса в загружаемом приложении?

Другой способ — тестирование нагрузки с переменной. Это можно сделать, поместив обработчики тестов производительности в приложение, которое приводит к замедлению загрузок переменной в серверную обработку. Это может имитировать различные виды нагрузки и реакцию приложения на различные производительность сервера.
В этом примере показано, как добавлять задержки времени в код с помощью правильных асинхронных методов. Точное количество задержек, которое нужно внедрить, и диапазон случайного выбора, который следует разместить в этой искусственной нагрузке, зависит от структуры приложения и ожидаемой среды, в которой будет выполняться приложение.

## <a name="development-process"></a>Процесс разработки

При внесении изменений на сервер необходимо убедиться, что все ранее работающие экземпляры больше не работают. Модель COM в конечном итоге будет очищать процесс, но таймер очистки занимает больше времени, чем эффективно для итеративной разработки. Таким шагом, выполнение ранее запущенного экземпляра является обычным этапом во время разработки. Для этого необходимо, чтобы разработчик отследить, какой экземпляр Dllhost размещает сервер.

Серверный процесс можно найти и завершить с помощью диспетчера задач или других сторонних приложений. Программа командной строки * * TaskList. exe * * также включена и имеет гибкий синтаксис, например:

  
 | **Команда** | **Действие** |
 | ------------| ---------- |
 | задач | Список всех запущенных процессов в приблизительном порядке времени создания с самыми последними созданными процессами в нижней части. |
 | tasklist/FI "IMAGENAME EQ Dllhost. exe"/M | Выводит сведения обо всех экземплярах Dllhost. exe. Параметр/M перечисляет модули, которые были загружены. |
 | tasklist/FI "PID EQ 12564"/M | Этот параметр можно использовать для выполнения запроса к Dllhost. exe, если известно его PID. |

Список модулей для сервера брокера должен иметь список *клрхост. dll* в своем списке загруженных модулей.

## <a name="resources"></a>Ресурсы

-   [Шаблоны проектов компонентов WinRT в брокере для Windows 10 и VS 2015](https://marketplace.visualstudio.com/items?itemName=vs-publisher-713547.VS2015TemplateBrokeredComponents)

-   [Пример компонента WinRT брокера Норсвиндрт](https://go.microsoft.com/fwlink/p/?LinkID=397349)

-   [Предоставление надежных и надежных приложений Microsoft Store](https://go.microsoft.com/fwlink/p/?LinkID=393644)

-   [Контракты и расширения приложений (приложения для Магазина Windows)](https://docs.microsoft.com/previous-versions/windows/apps/hh464906(v=win.10))

-   [Как загружать неопубликованные приложения в Windows 10](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)

-   [Развертывание приложений UWP для предприятий](https://go.microsoft.com/fwlink/p/?LinkID=264770)

