---
title: Пересреда выполнения Windowsные компоненты брокера для загружаемого на сервер приложения UWP
description: В этом документе обсуждается Корпоративная функция, поддерживаемая Windows 10, позволяющая удобным приложениям .NET использовать существующий код, отвечающий за ключевые критически важные для бизнеса операции.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 81b3930c-6af9-406d-9d1e-8ee6a13ec38a
ms.localizationpriority: medium
ms.openlocfilehash: 16996a8706018bde89d3eb08249ee496d7e25bb9
ms.sourcegitcommit: e7c95c156f970fe9fdf7ff98ea81508360a64c12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72172835"
---
# <a name="brokered-windows-runtime-components-for-a-side-loaded-uwp-app"></a>Пересреда выполнения Windowsные компоненты брокера для загружаемого на сервер приложения UWP

В этой статье обсуждается Корпоративная функция, поддерживаемая Windows 10, позволяющая удобным приложениям .NET использовать существующий код, отвечающий за ключевые критически важные для бизнеса операции.

## <a name="introduction"></a>Введение

>**Примечание**  The примеры кода, прилагаемые к этой статье, можно скачать для [Visual Studio 2015 & 2017](https://aka.ms/brokeredsample). Шаблон Microsoft Visual Studio для создания компонентов брокера среда выполнения Windows можно скачать здесь: [Шаблон Visual Studio 2015, предназначенный для универсальных приложений Windows для Windows 10](https://marketplace.visualstudio.com/items?itemName=vs-publisher-713547.VS2015TemplateBrokeredComponents)

В Windows появилась новая функциональность, получившая название *работающих через брокер компонентов среды выполнения Windows для неопубликованных приложений*. Термин "IPC" (inter-process communication, межпроцессное взаимодействие) предполагает, что имеющиеся классические программные активы (классический компонент) могут выполняться в отдельном процессе, взаимодействуя при этом с кодом в приложении UWP. Эта модель знакома разработчикам корпоративных систем, так как приложения баз данных и приложения, в которых используются службы NT в Windows, основаны на одной и той же многопроцессной архитектуре.

Загрузка неопубликованного приложения является важнейшей составляющей этой функциональности.
Корпоративным приложениям не место в Microsoft Store для обычных пользователей, к тому же корпорации предъявляют особые требования к безопасности, конфиденциальности, распространению, установке и обслуживанию. Поэтому модель загрузки неопубликованных приложений — это одновременно и требование возможных пользователей компонента, и критически важное условие его реализации.

Эта архитектура предназначена главным образом для приложений, ориентированных на обработку данных. Она предусматривает включение в классический компонент существующих бизнес-правил — например, из SQL Server. Разумеется, классический компонент располагает и другими функциями, но по большей части он необходим, когда дело касается существующих данных и бизнес-логики.

Наконец, учитывая подавляющее повышение уязвимости среды выполнения .NET и языка C @ no__t-0 при разработке на предприятии, эта функция была разработана с учетом использования .NET как для приложений UWP, так и для компонентов настольных систем. Хотя существуют и другие языки и среды выполнения для приложения UWP, в прилагаемом примере показан только C @ no__t-0 и ограничен только средой выполнения .NET.

## <a name="application-components"></a>Компоненты приложения

>**Примечание** . @no__t 1This-функция предназначена исключительно для использования с .NET. И клиентское приложение, и классический компонент должны быть созданы с использованием .NET.

**Модель приложения**

Эта функциональность строится вокруг общей архитектуры приложений, известной как модель MVVM (Model View View-Model). Предполагается, что "модель" полностью содержится в классическом компоненте. Из этого сразу становится понятно, что классический компонент будет "без монитора" (т. е. в нем не будет пользовательского интерфейса). Представление целиком содержится в неопубликованном корпоративном приложении. И хотя нет требований, связанных с включением в это приложение конструкции "модель представления", мы предполагаем, что использование такого шаблона станет общепринятым.

**Компонент рабочего стола**

Классический компонент в этой функциональности представляет собой приложение нового типа. Этот компонент рабочего стола может быть написан только на языке C @ no__t-0 и должен быть предназначен для .NET 4,6 или более поздней версии для Windows 10. Соответствующий тип проекта представляет собой гибрид со средой CLR, ориентированной на UWP, так как формат межпроцессного взаимодействия охватывает типы и классы UWP, тогда как классический компонент способен вызывать любые элементы библиотеки классов времени выполнения .NET. То, как это влияет на проект Visual Studio, будет подробно рассмотрено далее. Эта гибридная конфигурация обеспечивает маршалинг типов UWP между приложениями на базе классических компонентов. При этом она позволяет вызывать код классической среды CLR внутри реализации классического компонента.

**Архивирован**

Контракт между неопубликованным приложением и классическим компонентом описывается в понятиях системы типов UWP. Это подразумевает объявление одного или нескольких классов C @ no__t-0, которые могут представлять UWP. Конкретные требования к созданию класса среда выполнения Windows с помощью C @ no__t-2 см. в разделе MSDN [создание среда выполнения Windows компонентов в C @ no__t-1 и Visual Basic](https://docs.microsoft.com/previous-versions/windows/apps/br230301(v=vs.140)) .

>**Обратите внимание** ,  Enums не поддерживаются в контракте среда выполнения Windows компонентов между компонентом Desktop и загруженным приложением в данный момент.

**Приложение, загруженное на стороне**

Неопубликованное приложение — это обычное приложение UWP, которое имеет только одну особенность: в отличие от приложений, устанавливаемых через Microsoft Store, оно загружается без публикации. Большинство механизмов установки идентичны: используются аналогичные манифест и упаковка приложения (далее будет подробно описано одно дополнение к манифесту). Если загрузка без публикации включена, установить необходимые сертификаты и само приложение может простой сценарий PowerShell. Рекомендуется, чтобы неопубликованное приложение прошло сертификационный тест WACK (соответствующий инструмент находится в меню "Проект" > "Магазин" Visual Studio).

>**Примечание**. Загрузку без публикации можно включить в меню "Настройки"-&gt; "Обновление и безопасность" -&gt;"Для разработчиков".

Важно помнить, что механизм брокера приложений, предоставляемый в составе Windows 10, является 32-разрядным. Классический компонент должен быть 32-разрядным.
Неопубликованные приложения могут быть 64-разрядными (при условии, что зарегистрированы 64- и 32-разрядные прокси), но это будет нетипичная ситуация. Создание приложения, загружаемого на стороне, в C @ no__t-0 с помощью обычной "нейтральной" конфигурации и "предпочитать 32-bit" по умолчанию естественным образом создает приложения, загружаемые на основе 32-разрядной части.

**Создание экземпляров и доменов приложений сервера**

Каждое неопубликованное приложение получает свой экземпляр сервера брокера приложений (так называемое "создание нескольких экземпляров"). Код сервера выполняется в одном домене приложения. Это позволяет запустить несколько версий библиотеки на разных экземплярах сервера. Например, приложению A требуется версия 1.1, а приложению B — версия 2 компонента. Чтобы их аккуратно разделить, компоненты версий 1.1 и 2 помещаются в разные каталоги сервера, а затем приложению задается тот экземпляр сервера, который поддерживает нужную версию компонента.

Реализацию серверного кода могут использовать несколько экземпляров сервера брокера приложений. Для этого нескольким приложениям задается один и тот же каталог сервера. В результате получатся все те же несколько экземпляров сервера брокера приложений, но они будут выполнять одинаковый код. Все компоненты реализации, используемые в одном приложении, должны находиться по одному и тому же пути.

## <a name="defining-the-contract"></a>Определение контракта

Первый шаг в создании приложения с использованием этой функциональности — это создание контракта между неопубликованным приложением и классическим компонентом. Это необходимо делать исключительно с использованием типов среды выполнения Windows.
К счастью, эти классы легко объявить с помощью классов C @ no__t-0. При определении этих контрактов, однако, необходимо учитывать некоторые связанные с производительностью моменты; они будут рассмотрены в одном из следующих разделов.

Последовательность определения контракта выглядит следующим образом:

**Шаг 1.** Создайте новую библиотеку классов в Visual Studio. Не забудьте создать проект с помощью шаблона **библиотеки классов** , а не шаблона **компонента Среда выполнения Windows** .

О реализации мы поговорим далее, но этот раздел касается только определения межпроцессного контракта. Сопровождающий эту статью пример кода содержит следующий класс (EnterpriseServer.cs), начальная форма которого выглядит так:

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

Этот код определяет класс EnterpriseServer, экземпляры которого можно создавать из неопубликованного приложения. Этот класс предоставляет функциональность, обещанную в RuntimeClass. RuntimeClass можно использовать для создания ссылочного WINMD-файла, который будет входить в состав неопубликованного приложения.

**Шаг 2.** Измените файл проекта вручную, чтобы изменить тип выходных данных проекта на **Среда выполнения Windows компонент**.

Чтобы сделать это в Visual Studio, щелкните только что созданный проект правой кнопкой мыши, выберите "Выгрузить", затем щелкните правой кнопкой мыши еще раз и выберите "Изменить EnterpriseServer.csproj", чтобы открыть файл проекта (XML-файл) для редактирования.

В открывшемся файле найдите тег \<OutputType @ no__t-1 и измените его значение на "winmdobj".

**Шаг 3.** Создайте правило сборки, которое создает ссылку на файл метаданных Windows (WINMD-файл). т. е. не имеет реализации.

**Шаг 4.** Создайте правило сборки, которое создает файл метаданных Windows "реализация", т. е. имеет те же метаданные, но также включает реализацию.

Это делается посредством следующих сценариев. Добавьте сценарии в командную строку "Событие после сборки" (**Свойства** > **События сборки** проекта).

> **Примечание**. Сценарий будет разным в зависимости от версии Windows, на которую нацелен проект (Windows 10), и от используемой версии Visual Studio.

**Visual Studio 2015**
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

Как только ссылочный **winmd**-файл создан (в папке reference внутри папки Target проекта), его необходимо вручную перенести (скопировать) в каждый проект неопубликованного приложения, где он используется, и сослаться не него. Это будет описано в следующем разделе. Структура проекта, определенная приведенными выше правилами сборки, обеспечивает размещение ссылочного **winmd**-файла и аналогичного файла с реализацией в разных каталогах во избежание путаницы.

## <a name="side-loaded-applications-in-detail"></a>Подробнее о неопубликованных приложениях
Как уже упоминалось, неопубликованное приложение создается так же, как любое другое приложение UWP, за исключением одной особенности: объявления доступности классов RuntimeClass в манифесте неопубликованного приложения. Это позволяет приложению просто создать новый объект для доступа к функциональности классического компонента. Новая запись манифеста в разделе <Extension> описывает RuntimeClass, реализованный в классическом компоненте, и предоставляет данные о его расположении. Содержимое этого объявления в манифесте приложения одинаковое для всех приложений, нацеленных на Windows 10. Пример:

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

В качестве категории выбрана inProcessServer, так как в категории outOfProcessServer есть несколько записей, неприменимых к этой конфигурации приложения. Обратите внимание, что компонент <Path> всегда должен содержать файл clrhost.dll (однако это **не** обеспечивается принудительно, и при указании другого значения пути останутся неопределенными).

Раздел <ActivatableClass> совпадает с настоящим RuntimeClass, который используется внутри процесса и предпочтителен для компонента среды выполнения Windows в пакете приложения. <ActivatableClassAttribute> является новым элементом, а атрибуты Name = "Десктопаппликатионпас" и Type = "String" являются обязательными и инвариантными. Атрибут Value указывает расположение WINMD-файла реализации классического компонента (дополнительную информацию о нем см. в следующем разделе). Каждый RuntimeClass, предпочтительный для классического компонента, должен иметь собственное дерево элементов <ActivatableClass>. ActivatableClassId должен соответствовать полному имени пространства имен RuntimeClass.

Как указывалось в разделе "Определение контракта", ссылка на проект должна быть включена в ссылочный WINMD-файл классического компонента. Система проекта Visual Studio обычно создает двухуровневую структуру каталогов с тем же именем. В примере это Ентерприсеипкаппликатион @ no__t-0EnterpriseIPCApplication. Ссылочный **winmd**-файл вручную копируется в этот каталог второго уровня. Затем с помощью диалогового окна "Ссылки проекта" (нажмите кнопку **Обзор...** ) определяется расположение этого ссылочного **winmd**-файла, и на него делается ссылка. После этого принадлежащее классическому компоненту пространство имен верхнего уровня (например, Fabrikam) должно появиться в качестве узла верхнего уровня в части проекта "Ссылки".

>**Примечание** Очень важно использовать в неопубликованном приложении **ссылочный winmd-файл**. Если вы случайно перенесете в каталог неопубликованного приложения **winmd-файл с реализацией** и сошлетесь на него, вы скорее всего получите ошибку "не удается найти IStringable". Это четкое указание на то, что вы ссылаетесь на неверный **winmd**-файл. Правила, применяемые после сборки в приложении сервера IPC (дополнительные сведения см. в следующем разделе), аккуратно разделяют эти два **winmd**-файла, помещая их в разные каталоги.

Переменные среды (особенно% ProgramFiles%) может использоваться в <ActivatableClassAttribute Value="path">. Как отмечалось ранее, брокер приложений поддерживает только 32-bit, поэтому% ProgramFiles% будет разрешаться в C: \\Program Files (x86), если приложение выполняется в 64-разрядной ОС.

## <a name="desktop-ipc-server-detail"></a>Дополнительная информация о классическом сервере IPC

В предыдущих двух разделах описано объявление класса и механизма переноса ссылочного **winmd**-файла в проект неопубликованного приложения. Основная часть оставшейся работы с классическим компонентом заключается в реализации. Так как суть классического компонента заключается в способности вызывать код классического приложения (обычно для повторного использования существующих ресурсов кода), проект необходимо настроить особым образом.
Как правило, проект Visual Studio на базе .NET использует один из двух "профилей".
Один предназначен для классических приложений (".NetFramework"), а второй нацелен на часть среды CLR для приложений UWP (".NetCore"). Классический компонент в этой функциональности представляет собой нечто среднее между ними. Поэтому для совмещения этих двух профилей раздел ссылок создается особенно тщательно.

Обычный проект приложения UWP не содержит явных ссылок на проект, так как неявно в него включается вся совокупность API среды выполнения Windows.
Как правило, указываются только прочие ссылки между проектами. Но проект классического компонента имеет очень специализированный набор ссылок. Он начинает жизнь как проект классического рабочего стола @ no__t-0Class Library и, таким образом, является настольным проектом. Поэтому необходимо делать явные ссылки на API среды выполнения Windows (через ссылки на **winmd**-файлы). Добавьте необходимые ссылки, как показано ниже.

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

Приведенные выше ссылки представляют собой тщательно выверенное сочетание ссылок, которые критически важны для правильной работы этого гибридного сервера. Процедура состоит в том, чтоб открыть CSPROJ-файл (так же, как при редактировании OutputType проекта) и добавить необходимые ссылки.

После того как ссылки настроены надлежащим образом, следующая задача — реализовать функциональность сервера. Ознакомьтесь с разделом MSDN рекомендации [по взаимодействию с среда выполнения Windows компонентами (приложения UWP, использующие C @ no__t-1/C++ VB/и XAML)](https://docs.microsoft.com/previous-versions/windows/apps/hh750311(v=win.10)).
Задача состоит в том, чтобы создать компонент среды выполнения Windows (DLL), способный вызывать классический код в рамках своей реализации. Прилагающийся к статье пример кода содержит основные шаблоны, используемые в среде выполнения Windows:

-   Вызовы методов

-   События среды выполнения Windows, источником которых является классический компонент

-   Асинхронные операции среды выполнения Windows

-   Возвращаемые массивы основных типов

**Установить**

Чтобы установить приложение, скопируйте **winmd** -файл с реализацией в правильный каталог, указанный в соответствующем манифесте неопубликованного приложения: Value="путь" элемента <ActivatableClassAttribute>. Также скопируйте все связанные вспомогательные файлы и DLL-файл прокси/заглушки (о прокси и заглушках см. далее). Если **winmd**-файл с реализацией не скопирован в каталог сервера, все вызовы неопубликованного приложения, обращенные к новом RuntimeClass, будут выдавать ошибку "класс не зарегистрирован". Если не установить (или не зарегистрировать) прокси/заглушку, ни один вызов не сможет вернуть значение. Последняя ошибка часто **не** связана с видимыми исключениями.
Если эта ошибка конфигурации вызывает исключения, их могут отнести к "недопустимому приведению".

**Рекомендации по реализации сервера**

Классический сервер среды выполнения Windows можно рассматривать как ориентированный на "рабочий процесс" или на "задачу". Каждый вызов, поступающий на сервер, выполняется в потоке без пользовательского интерфейса, а весь код должен учитывать многопоточность и быть безопасным. Важно и то, какая часть неопубликованного приложения вызывает функции сервера. Чрезвычайно важно, чтобы неопубликованное приложение всегда избегало вызова из потока пользовательского интерфейса кода с длительным временем выполнения. Этого можно добиться двумя основными способами:

1.  При вызове функциональности сервера из потока пользовательского интерфейса всегда используйте асинхронный шаблон в открытой контактной зоне сервера и в реализации.

2.  Вызывайте функциональность сервера из фонового потока в неопубликованном приложении.

**среда выполнения Windows Async на сервере**

Из-за межпроцессного характера модели приложения вызовы, адресованные серверу, более затратны, нежели код, выполняющийся исключительно внутри процесса. Как правило, вызов простого свойства, возвращающего значение из памяти, безопасен, поскольку выполняется так быстро, что можно не придавать значения блокировке потока пользовательского интерфейса. Однако все вызовы, связанные с любыми операциями ввода-вывода (в том числе с обработкой файлов и с извлечением информации из баз данных), теоретически способны заблокировать вызывающий поток пользовательского интерфейса и привести к завершению работы приложения из-за отсутствия отклика. Кроме того, в этой архитектуре приложений не рекомендуется применять вызовы свойств для объектов из соображений производительности.
Дополнительные сведения об этом см. в следующем разделе.

Правильно реализованный сервер, как правило, выполняет вызовы, полученные непосредственно из потоков пользовательского интерфейса, через асинхронный шаблон среды выполнения Windows. Для этого можно использовать такой шаблон. Во-первых, объявление (также можно взять из примера этой статьи):

```csharp
public IAsyncOperation<int> FindElementAsync(int input)
```

Здесь объявляется асинхронная операция среды выполнения Windows, возвращающая целое число.
Реализация асинхронной операции обычно имеет следующий вид:

```csharp
return Task<int>.Run( () =>
{
    int retval = ...
    // execute some potentially long-running code here 
}).AsAsyncOperation<int>();

```

>**Примечание** При написании реализации обычно ожидаются некоторые другие потенциально длительные операции. В этом случае необходимо объявить код **Task.Run**:

```csharp
return Task<int>.Run(async () =>
{
    int retval = ...
    // execute some potentially long-running code here 
    await ... // some other WinRT async operation or Task
}).AsAsyncOperation<int>();
```

Клиенты этого асинхронного метода могут ожидать эту операцию, как любую другую асинхронную операцию среды выполнения Windows.

**Вызов функций сервера из фонового потока приложения**

Так как обычно код для клиента и сервера пишется в одной организации, можно взять за обыкновение программировать выполнение всех вызовов сервера в неопубликованных приложениях в фоновом потоке. Прямой вызов, собирающий один или несколько пакетов данных с сервера, можно выполнять из фонового потока. Если результаты получены полностью, пакет данных, находящийся в памяти процесса приложения, обычно можно извлечь непосредственно из потока пользовательского интерфейса. Объекты C @ no__t-0 естественным образом связаны с фоновыми потоками и потоками пользовательского интерфейса, поэтому они особенно полезны для такого рода вызывающего шаблона.

## <a name="creating-and-deploying-the-windows-runtime-proxy"></a>Создание и развертывание прокси среды выполнения Windows

В подходе IPC используется маршалинг интерфейсов среды выполнения Windows между двумя процессами, поэтому необходимо использовать глобально зарегистрированные прокси и заглушку среды выполнения Windows.

**Создание учетной записи-посредника в Visual Studio**

Процесс создания и регистрации прокси-серверов и заглушек для использования внутри обычного пакета приложения UWP описан в разделе [Создание событий в среда выполнения Windows компонентах](https://docs.microsoft.com/previous-versions/windows/apps/dn169426(v=vs.140)).
Шаги, представленные в этой статье, сложнее описанного ниже процесса, так как он предполагает регистрацию прокси/заглушки в пакете приложения (в отличие от глобальной регистрации).

**Шаг 1.** С помощью решения для проекта компонента рабочего стола создайте проект прокси-сервера или заглушки в Visual Studio:

**Решение > Добавить > Проект > Visual C++ > Консоль Win32 выберите параметр DLL.**

В следующих шагах предположим, что серверный компонент называется **MyWinRTComponent**.

**Шаг 3.** Удалите все файлы CPP/H из проекта.

**Шаг 4.** В предыдущем разделе «Определение контракта» содержится команда, выполняемая после сборки, которая запускает **winmdidl. exe**, **MIDL. exe**, **мдмерже. exe**и т. д. В результате работы midl.exe в рамках этой послесборочной команды создается четыре выходных файла.

а) Dlldata.c

б) файл заголовка (например, MyWinRTComponent.h)

в) файл \* @ no__t-1i. c (например, Мивинрткомпонент @ no__t-2i. c)

d) файл \* @ no__t-1P. c (например, Мивинрткомпонент @ no__t-2P. c)

**Шаг 5.** Добавьте эти четыре созданных файла в проект "Мивинртпрокси".

**Шаг 6.** Добавьте DEF-файл в проект "Мивинртпрокси" **(проект > добавить новый элемент > код > файл определения модуля**) и обновите содержимое следующим образом:

LIBRARY MyWinRTComponent.Proxies.dll

EXPORTS

DllCanUnloadNow PRIVATE

DllGetClassObject PRIVATE

DllRegisterServer PRIVATE

DllUnregisterServer PRIVATE

**Шаг 7.** Откройте свойства проекта "Мивинртпрокси":

**Комфигуратион свойства > Общее > имя целевого объекта:**

MyWinRTComponent.Proxies

**Определение препроцессора C/C++ > > Добавление**

"WIN32; \_WINDOWS; REGISTER @ NO__T-1PROXY @ NO__T-2DLL "

**C/C++ > предкомпилированный заголовок: Выберите "не использовать предкомпилированный заголовок"**

**Linker > Общие > игнорировать библиотеку импорта: Выберите "Да"**

**Linker > ввод > Дополнительные зависимости: Добавление rpcrt4. lib; рунтимеобжект. lib @ no__t-0

**Linker > метаданные Windows > создания метаданных Windows: Выберите "No"**

**Шаг 8.** Создайте проект "Мивинртпрокси".

**Развертывание прокси-сервера**

Прокси необходимо зарегистрировать глобально. Проще всего это сделать с помощью вызова DllRegisterServer из proxy dll в процессе установки. Обратите внимание, что, поскольку этот компонент поддерживает только серверы для архитектуры x86 (т. е. не поддерживает 64-разрядные системы), то в простейшей конфигурации используется 32-разрядный сервер, 32-разрядный прокси и 32-разрядное неопубликованное приложение. Прокси обычно находится рядом с **winmd**-файлом с реализацией для классического компонента.

Необходимо выполнить дополнительный шаг настройки. Чтобы процесс загрузки без публикации загружал и выполнял прокси, необходимо присвоить каталогу отметку "чтение/выполнение" для ALL_APPLICATION_PACKAGES. Это делается с помощью инструмента командной строки **icacls.exe**. Эту команду нужно выполнить в каталоге, содержащем **winmd** -файл с реализацией и DLL-файл прокси/заглушки:

*icacls. /T/Грант \*S-1-15-2-1: RX*

## <a name="patterns-and-performance"></a>Шаблоны и производительность

Очень важно тщательно отслеживать производительность межпроцессного транспорта. Вызов между процессами требует как минимум в два раза больше ресурсов, чем вызов внутри процесса. Создание межпроцессного взаимодействия для насыщенных сеансов или регулярная передача больших объектов (например, точечных рисунков) может неожиданно снизить производительность приложения.

Вот далеко не полный список того, чему необходимо уделить внимание:

-   Следует избегать вызова синхронных методов из потока пользовательского интерфейса приложения, адресованных серверу. Если требуется, вызывайте методы из фонового потока в приложении, а затем используйте CoreWindowDispatcher, чтобы передать результаты в поток пользовательского интерфейса.

-   Вызов асинхронных операций из потока пользовательского интерфейса приложения безопасен, но не забывайте об упомянутых выше проблемах с производительностью.

-   Массовая передача результатов снижает межпроцессное взаимодействие. Обычно она выполняется с помощью конструкции Array среды выполнения Windows.

-   Возвращение структуры *List<T>* , где *T* — объект из асинхронной операции или извлеченное свойство, потребует большого количества межпроцессных взаимодействий. Например, предположим, что вы возвращаете объекты *List&lt;People&gt;* . Каждая итерация будет межпроцессным вызовом. Каждый возвращаемый объект *People* будет представлен с помощью прокси, и каждый вызов метода или свойства этого отдельного объекта приведет к межпроцессному вызову. Поэтому "безобидный" объект *List&lt;People&gt;* с большим значением переменной *Count* приведет к большому количеству медленно выполняемых вызовов. Массовая передача структур содержимого в массиве обеспечивает более высокую производительность. Пример:

```csharp
struct PersonStruct
{
    String LastName;
    String FirstName;
    int Age;
   // etc.
}
```

Затем возвратите * Персонструкт @ no__t-0 @ no__t-1 * вместо *List @ no__t-3PersonObject @ no__t-4*.
В этом случае все данные будут получены за один сеанс межпроцессного взаимодействия.

Как всегда в вопросах производительности, большую роль играют измерение и тестирование. В идеале телеметрию нужно включать в разные операции, чтобы определить их длительность. Важно получить показания в диапазоне. Например, сколько времени действительно занимает получение всех объектов *People* для конкретного запроса в неопубликованном приложении?

Еще один способ — переменное нагрузочное тестирование. Для этого в приложение включаются тестовые обработчики производительности, которые вводят различные отсроченные загрузки в выполняемые на сервере процессы обработки. Таким образом имитируются различные типы нагрузки и реакция приложения на изменение производительности сервера.
В примере показано, как правильно использовать асинхронный метод для включения в код задержек по времени. Точное количество включаемых в код задержек и диапазон случайной выборки для этой искусственной нагрузки зависит от конструкции приложения и предполагаемой среды, в которой оно будет выполняться.

## <a name="development-process"></a>Процесс разработки

При внесении изменений в сервер необходимо убедиться, что не выполняются его предыдущие экземпляры. COM в конечном счете очистит процесс, но время очистки будет больше, чем это требуется для эффективной последовательной разработки. Таким образом, завершение работы предыдущего экземпляра является нормальным действием в процессе разработки. При этом разработчику необходимо следить за тем, в каком экземпляре процесса dllhost.exe располагается сервер.

Процесс сервера можно найти и завершить с помощью диспетчера задач или других, сторонних приложений. Кроме того, имеется средство командной строки **TaskList.exe **с гибким синтаксисом, например:

  
 | **Кнопки** | **Действие** |
 | ------------| ---------- |
 | tasklist | Выводится список всех запущенных процессов в порядке их создания (самые последние — в конце списка). |
 | tasklist /FI "IMAGENAME eq dllhost.exe" /M | Выводится информация обо всех экземплярах процесса dllhost.exe. Параметр /M выводит список модулей, загруженных этими экземплярами. |
 | tasklist /FI "PID eq 12564" /M | С помощью этого параметра можно запросить информацию о процессе dllhost.exe, используя его PID (идентификационный номер процесса). |

В списке модулей сервера брокера должен присутствовать файл *clrhost.dll* (в списке загруженных модулей).

## <a name="resources"></a>Ресурсы

-   [Шаблоны проектов компонентов WinRT в брокере для Windows 10 и VS 2015](https://marketplace.visualstudio.com/items?itemName=vs-publisher-713547.VS2015TemplateBrokeredComponents)

-   [Пример компонента WinRT брокера Норсвиндрт](https://go.microsoft.com/fwlink/p/?LinkID=397349)

-   [Предоставление надежных и надежных приложений Microsoft Store](https://go.microsoft.com/fwlink/p/?LinkID=393644)

-   [Контракты и расширения приложений (приложения для Магазина Windows)](https://docs.microsoft.com/previous-versions/windows/apps/hh464906(v=win.10))

-   [Как загружать неопубликованные приложения в Windows 10](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development)

-   [Развертывание приложений UWP для предприятий](https://go.microsoft.com/fwlink/p/?LinkID=264770)

