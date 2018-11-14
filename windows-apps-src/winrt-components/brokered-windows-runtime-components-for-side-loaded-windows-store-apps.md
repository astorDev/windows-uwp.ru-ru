---
author: msatranjr
title: Компоненты среды выполнения Windows для неопубликованных приложений UWP, выполняемые через посредника
description: В этом документе ориентированная предприятия поддерживаются Windows10, что позволяет использовать существующий код, отвечающий за ключевые бизнес-операции приложения .NET с поддержкой сенсорного ввода.
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 81b3930c-6af9-406d-9d1e-8ee6a13ec38a
ms.localizationpriority: medium
ms.openlocfilehash: 3228cd80e7a9e8efb5dca1ec3a2d469e40a52c8a
ms.sourcegitcommit: f2c9a050a9137a473f28b613968d5782866142c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6252264"
---
# <a name="brokered-windows-runtime-components-for-a-side-loaded-uwp-app"></a>Компоненты среды выполнения Windows для неопубликованных приложений UWP, выполняемые через посредника

В этой статье ориентированная предприятия поддерживаются Windows10, что позволяет использовать существующий код, отвечающий за ключевые бизнес-операции приложения .NET с поддержкой сенсорного ввода.

## <a name="introduction"></a>Введение

>**Примечание**можно скачать этот сопроводительный пример кода для[Visual Studio 2015 и 2017 г](https://aka.ms/brokeredsample). Шаблон Microsoft Visual Studio для построения работающего через посредника компонента среды выполнения Windows можно скачать здесь: [шаблон для Visual Studio 2015, ориентированный на универсальные приложения для Windows10](https://visualstudiogallery.msdn.microsoft.com/10be07b3-67ef-4e02-9243-01b78cd27935)

Windows включает новую функцию под названием*Через брокер компонентов среды выполнения Windows для неопубликованных приложений*. Термин "IPC" (inter-process communication, межпроцессное взаимодействие) предполагает, что имеющиеся классические программные активы (классический компонент) могут выполняться в отдельном процессе, взаимодействуя при этом с кодом в приложении UWP. Эта модель знакома разработчикам корпоративных систем, так как приложения баз данных и приложения, в которых используются службы NT в Windows, основаны на одной и той же многопроцессной архитектуре.

Загрузка неопубликованного приложения является важнейшей составляющей этой функциональности.
Корпоративным приложениям не место в Microsoft Store для обычных пользователей, к тому же корпорации предъявляют особые требования к безопасности, конфиденциальности, распространению, установке и обслуживанию. Поэтому модель загрузки неопубликованных приложений — это одновременно и требование возможных пользователей компонента, и критически важное условие его реализации.

Эта архитектура предназначена главным образом для приложений, ориентированных на обработку данных. Она предусматривает включение в классический компонент существующих бизнес-правил — например, из SQL Server. Разумеется, классический компонент располагает и другими функциями, но по большей части он необходим, когда дело касается существующих данных и бизнес-логики.

И последнее: благодаря широкому применению среды выполнения .NET и языка C\# в разработке корпоративных приложений, особый упор в этом компоненте сделан на использование .NET как в приложениях UWP, так и в неопубликованных классических компонентах. Хотя для разработки приложений UWP можно использовать другую среду выполнения и другие языки, предлагаемый пример кода иллюстрирует только использование C\# и ограничен исключительно средой выполнения .NET.

## <a name="application-components"></a>Компоненты приложения

>**Примечание**эта функциональность предназначена исключительно для использования с .NET. И клиентское приложение, и классический компонент должны быть созданы с использованием .NET.

**Модель приложения**

Эта функциональность строится вокруг общей архитектуры приложений, известной как модель MVVM (Model View View-Model). Предполагается, что "модель" полностью содержится в классическом компоненте. Из этого сразу становится понятно, что классический компонент будет "без монитора" (т.е. в нем не будет пользовательского интерфейса). Представление целиком содержится в неопубликованном корпоративном приложении. И хотя нет требований, связанных с включением в это приложение конструкции "модель представления", мы предполагаем, что использование такого шаблона станет общепринятым.

**Классический компонент**

Классический компонент в этой функциональности представляет собой приложение нового типа. Этот классический компонент может быть написан только на C\# и должен быть нацелен на .NET 4.6 (или более позднюю версию) для Windows10. Соответствующий тип проекта представляет собой гибрид со средой CLR, ориентированной на UWP, так как формат межпроцессного взаимодействия охватывает типы и классы UWP, тогда как классический компонент способен вызывать любые элементы библиотеки классов времени выполнения .NET. То, как это влияет на проект Visual Studio, будет подробно рассмотрено далее. Эта гибридная конфигурация обеспечивает маршалинг типов UWP между приложениями на базе классических компонентов. При этом она позволяет вызывать код классической среды CLR внутри реализации классического компонента.

**Контракт**

Контракт между неопубликованным приложением и классическим компонентом описывается в понятиях системы типов UWP. Это предполагает объявление одного или нескольких классов C\#, которые могут представлять UWP. Конкретные требования, связанные с созданием класса среды выполнения Windows на C\#, см. в разделе MSDN [Создание компонентов среды выполнения Windows в C\# и Visual Basic](https://msdn.microsoft.com/library/br230301.aspx).

>**Примечание**в контракте компонентов среды выполнения Windows между классическим компонентом и неопубликованным приложением в настоящее время не поддерживаются перечисления.

**Неопубликованное приложение**

Неопубликованное приложение— это обычное приложение UWP, которое имеет только одну особенность: в отличие от приложений, устанавливаемых через Microsoft Store, оно загружается без публикации. Большинство механизмов установки идентичны: используются аналогичные манифест и упаковка приложения (далее будет подробно описано одно дополнение к манифесту). Если загрузка без публикации включена, установить необходимые сертификаты и само приложение может простой сценарий PowerShell. Рекомендуется, чтобы неопубликованное приложение прошло сертификационный тест WACK (соответствующий инструмент находится в меню "Проект" > "Магазин" Visual Studio).

>**Примечание**загрузку без публикации можно включить в параметры -&gt; обновление и безопасность -&gt; для разработчиков.

Важно помнить, что механизм брокера приложений, предоставляемый в составе Windows10, является 32-разрядным. Классический компонент должен быть 32-разрядным.
Неопубликованные приложения могут быть 64-разрядными (при условии, что зарегистрированы 64- и 32-разрядные прокси), но это будет нетипичная ситуация. Если при сборке неопубликованного приложения на C\# задать обычную "нейтральную" конфигурацию и не снимать установленный по умолчанию флажок "Предпочитать 32-разрядную", будет создано 32-разрядное неопубликованное приложение.

**Использование экземпляров сервера и домены приложений**

Каждое неопубликованное приложение получает свой экземпляр сервера брокера приложений (так называемое "создание нескольких экземпляров"). Код сервера выполняется в одном домене приложения. Это позволяет запустить несколько версий библиотеки на разных экземплярах сервера. Например, приложению A требуется версия 1.1, а приложению B— версия 2 компонента. Чтобы их аккуратно разделить, компоненты версий 1.1 и 2 помещаются в разные каталоги сервера, а затем приложению задается тот экземпляр сервера, который поддерживает нужную версию компонента.

Реализацию серверного кода могут использовать несколько экземпляров сервера брокера приложений. Для этого нескольким приложениям задается один и тот же каталог сервера. В результате получатся все те же несколько экземпляров сервера брокера приложений, но они будут выполнять одинаковый код. Все компоненты реализации, используемые в одном приложении, должны находиться по одному и тому же пути.

## <a name="defining-the-contract"></a>Определение контракта

Первый шаг в создании приложения с использованием этой функциональности— это создание контракта между неопубликованным приложением и классическим компонентом. Это необходимо делать исключительно с использованием типов среды выполнения Windows.
К счастью, их легко объявить с помощью классов C\#. При определении этих контрактов, однако, необходимо учитывать некоторые связанные с производительностью моменты; они будут рассмотрены в одном из следующих разделов.

Последовательность определения контракта выглядит следующим образом:

**Шаг 1.** Создайте новую библиотеку классов в Visual Studio. Создавать проект необходимо с использованием шаблона "Библиотека классов", а не шаблона "Компонент среды выполнения Windows".

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

**Шаг 2.** Отредактируйте файл проекта вручную, чтобы изменить тип вывода проекта на компонент среды выполнения Windows.

Чтобы сделать это в Visual Studio, щелкните только что созданный проект правой кнопкой мыши, выберите "Выгрузить", затем щелкните правой кнопкой мыши еще раз и выберите "Изменить EnterpriseServer.csproj", чтобы открыть файл проекта (XML-файл) для редактирования.

В открытом файле найдите тег \<OutputType\> и измените его значение на "winmdobj".

**Шаг 3.** Создайте правило сборки, которое создает "ссылочный" файл метаданных Windows (WINMD-файл), т.е. не имеет реализации.

**Шаг 4.** Создайте правило сборки, которое создает файл метаданных Windows с реализацией, т.е. содержащий не только те же метаданные, но и реализацию.

Это делается посредством следующих сценариев. Добавьте сценарии в командную строку "Событие после сборки" (**Свойства** > **События сборки** проекта).

> **Примечание**. Сценарий будет разным в зависимости от версии Windows, на которую нацелен проект (Windows10), и от используемой версии Visual Studio.

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


**Visual Studio 2017**
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

Один раз ссылка**winmd**создается (в папке «ссылки» папки Target проекта), его необходимо вручную используется перенесены (скопировать) в каждый проект неопубликованного приложения и ссылаться на них. Это будет описано в следующем разделе. Структура проекта, определенная в выше правилами сборки убедитесь, что реализация и справочник по**winmd**находятся в разных каталогах во избежание путаницы.

## <a name="side-loaded-applications-in-detail"></a>Подробнее о неопубликованных приложениях
Как уже упоминалось, неопубликованное приложение создается так же, как любое другое приложение UWP, за исключением одной особенности: объявления доступности классов RuntimeClass в манифесте неопубликованного приложения. Это позволяет приложению просто создать новый объект для доступа к функциональности классического компонента. Новая запись манифеста в разделе <Extension> описывает RuntimeClass, реализованный в классическом компоненте, и предоставляет данные о его расположении. Содержимое этого объявления в манифесте приложения одинаковое для всех приложений, нацеленных на Windows10. Пример.

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

В качестве категории выбрана inProcessServer, так как в категории outOfProcessServer есть несколько записей, неприменимых к этой конфигурации приложения. Обратите внимание, что <Path> компонент всегда должен содержать clrhost.dll (Однако это**не**принудительно и указании другого значения завершится ошибкой неопределенными).

Раздел <ActivatableClass> совпадает с настоящим RuntimeClass, который используется внутри процесса и предпочтителен для компонента среды выполнения Windows в пакете приложения. <ActivatableClassAttribute> это новый элемент, а атрибуты Name="DesktopApplicationPath" и Type="string" являются обязательными и не изменяются. Атрибут Value указывает расположение WINMD-файла реализации классического компонента (дополнительную информацию о нем см. в следующем разделе). Каждый RuntimeClass, предпочтительный для классического компонента, должен иметь собственное дерево элементов <ActivatableClass>. ActivatableClassId должен соответствовать полному имени пространства имен RuntimeClass.

Как указывалось в разделе "Определение контракта", ссылка на проект должна быть включена в ссылочный WINMD-файл классического компонента. Система проекта Visual Studio обычно создает двухуровневую структуру каталогов с тем же именем. В примере она называется EnterpriseIPCApplication\\EnterpriseIPCApplication. Справочник по **winmd**вручную копируется в этот каталог второго уровня, а затем диалогового ссылки на проект (щелкните**Обзор..** кнопки) для обнаружения и ссылаться на этот **winmd**. После этого принадлежащее классическому компоненту пространство имен верхнего уровня (например, Fabrikam) должно появиться в качестве узла верхнего уровня в части проекта "Ссылки".

>**Примечание** Очень важно использовать**ссылочного winmd-файла**в неопубликованном приложении. Если вы случайно перенесете**реализации winmd**каталог неопубликованного приложения и ссылку, вы скорее всего появляется сообщение об ошибке связанные с «не удается найти IStringable». Это одна четкое, неверный**winmd**выполнена ссылка на. Правила после построения в IPC сервера приложения (подробные в следующем разделе) тщательно выделять эти два**winmd**в отдельные каталоги.

В <ActivatableClassAttribute Value="path"> можно использовать переменные среды. Как уже отмечалось, брокер приложений поддерживает только 32-разрядные среды, поэтому при выполнении приложения в 64-разрядной операционной системе переменной %ProgramFiles% присваивается значение C:\Program Files (x86).

## <a name="desktop-ipc-server-detail"></a>Дополнительная информация о классическом сервере IPC

В предыдущих двух разделах описано объявление класса и механизма переноса транспортировки ссылка**winmd**в проект неопубликованного приложения. Основная часть оставшейся работы с классическим компонентом заключается в реализации. Так как суть классического компонента заключается в способности вызывать код классического приложения (обычно для повторного использования существующих ресурсов кода), проект необходимо настроить особым образом.
Как правило, проект Visual Studio на базе .NET использует один из двух "профилей".
Один предназначен для классических приложений (".NetFramework"), а второй нацелен на часть среды CLR для приложений UWP (".NetCore"). Классический компонент в этой функциональности представляет собой нечто среднее между ними. Поэтому для совмещения этих двух профилей раздел ссылок создается особенно тщательно.

Обычный проект приложения UWP не содержит явных ссылок на проект, так как неявно в него включается вся совокупность API среды выполнения Windows.
Как правило, указываются только прочие ссылки между проектами. Но проект классического компонента имеет очень специализированный набор ссылок. Он начинает свое существование как проект "Классическое приложение\\Библиотека классов", т.е. является классическим проектом. Поэтому явные ссылки на API среды выполнения Windows (через ссылки на**winmd**файлы) должно быть выполнено. Добавьте необходимые ссылки, как показано ниже.

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

После того как ссылки настроены надлежащим образом, следующая задача— реализовать функциональность сервера. См. в разделе MSDN[рекомендации по взаимодействию с компонентами среды выполнения Windows (приложения UWP на C\ #/ VB/C++ и XAML)](https://msdn.microsoft.com/library/windows/apps/hh750311.aspx).
Задача состоит в том, чтобы создать компонент среды выполнения Windows (DLL), способный вызывать классический код в рамках своей реализации. Прилагающийся к статье пример кода содержит основные шаблоны, используемые в среде выполнения Windows:

-   Вызовы методов

-   События среды выполнения Windows, источником которых является классический компонент

-   Асинхронные операции среды выполнения Windows

-   Возвращаемые массивы основных типов

**Установка**

Чтобы установить приложение, скопируйте реализации**winmd**в правильный каталог, указанный в манифесте связанные неопубликованного приложения: <ActivatableClassAttribute>Value = «путь». Также скопируйте все связанные вспомогательные файлы и DLL-файл прокси/заглушки (о прокси и заглушках см. далее). Не удается скопировать реализации**winmd**сервера каталог обращенные все неопубликованного приложения вызовы новые RuntimeClass вызовет ошибку «класс не зарегистрирован». Если не установить (или не зарегистрировать) прокси/заглушку, ни один вызов не сможет вернуть значение. Последняя ошибка часто**не**являетсясвязана с видимыми исключениями.
Если эта ошибка конфигурации вызывает исключения, их могут отнести к "недопустимому приведению".

**Вопросы реализации сервера**

Классический сервер среды выполнения Windows можно рассматривать как ориентированный на "рабочий процесс" или на "задачу". Каждый вызов, поступающий на сервер, выполняется в потоке без пользовательского интерфейса, а весь код должен учитывать многопоточность и быть безопасным. Важно и то, какая часть неопубликованного приложения вызывает функции сервера. Чрезвычайно важно, чтобы неопубликованное приложение всегда избегало вызова из потока пользовательского интерфейса кода с длительным временем выполнения. Этого можно добиться двумя основными способами:

1.  При вызове функциональности сервера из потока пользовательского интерфейса всегда используйте асинхронный шаблон в открытой контактной зоне сервера и в реализации.

2.  Вызывайте функциональность сервера из фонового потока в неопубликованном приложении.

**Асинхронные операции среды выполнения Windows на сервере**

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

>**Примечание**. При написании реализации обычно ожидаются некоторые другие потенциально длительные операции. Если это так,**Task.Run**код должен быть объявлен:

```csharp
return Task<int>.Run(async () =>
{
    int retval = ...
    // execute some potentially long-running code here 
    await ... // some other WinRT async operation or Task
}).AsAsyncOperation<int>();
```

Клиенты этого асинхронного метода могут ожидать эту операцию, как любую другую асинхронную операцию среды выполнения Windows.

**Вызов функциональности сервера из фонового потока приложения**

Так как обычно код для клиента и сервера пишется в одной организации, можно взять за обыкновение программировать выполнение всех вызовов сервера в неопубликованных приложениях в фоновом потоке. Прямой вызов, собирающий один или несколько пакетов данных с сервера, можно выполнять из фонового потока. Если результаты получены полностью, пакет данных, находящийся в памяти процесса приложения, обычно можно извлечь непосредственно из потока пользовательского интерфейса. Объекты C\# обычно легко перемещаются между фоновыми потоками и потоками пользовательского интерфейса, что особенно удобно для такого шаблона вызовов.

## <a name="creating-and-deploying-the-windows-runtime-proxy"></a>Создание и развертывание прокси среды выполнения Windows

В подходе IPC используется маршалинг интерфейсов среды выполнения Windows между двумя процессами, поэтому необходимо использовать глобально зарегистрированные прокси и заглушку среды выполнения Windows.

**Создание прокси в Visual Studio**

Процесс создания и регистрации прокси и заглушек для использования в пакете обычных приложений UWP описан в разделе[Создание событий в компонентах среды выполнения Windows](https://msdn.microsoft.com/library/windows/apps/dn169426.aspx).
Шаги, представленные в этой статье, сложнее описанного ниже процесса, так как он предполагает регистрацию прокси/заглушки в пакете приложения (в отличие от глобальной регистрации).

**Шаг 1.** Используя решение для проекта классического компонента. создайте в Visual Studio проект прокси/заглушки:

**Решение > Добавить > Проект > Visual C++ > Консольное приложение Win32: выберите "DLL"**

Для действия предполагается, что компонент сервера называется**MyWinRTComponent**.

**Шаг 3.** Удалите из проекта все файлы CPP/H.

**Шаг 4:** Предыдущий раздел «Определение контракта» содержит Послесборочную команду, которая выполняется**winmdidl.exe**,**midl.exe**,**mdmerge.exe**и т. д. В результате работы midl.exe в рамках этой послесборочной команды создается четыре выходных файла.

а) Dlldata.c

б) файл заголовка (например, MyWinRTComponent.h)

в) файл \*\_i.c (например, MyWinRTComponent\_i.c)

г) файл \*\_p.c (например, MyWinRTComponent\_p.c)

**Шаг 5.** Добавьте эти четыре файла в проект "MyWinRTProxy".

**Шаг 6:** Добавьте def-файла в проект «MyWinRTProxy»**(проект > Добавить новый элемент > код > файл определения модуля**) и обновления содержимого будет:

LIBRARY MyWinRTComponent.Proxies.dll

EXPORTS

DllCanUnloadNow PRIVATE

DllGetClassObject PRIVATE

DllRegisterServer PRIVATE

DllUnregisterServer PRIVATE

**Шаг 7.** Откройте свойства проекта "MyWinRTProxy":

**Свойства конфигурации > Общие > Целевое имя:**

MyWinRTComponent.Proxies

**C/C++ > Определения препроцессора > Добавить**

"WIN32;\_WINDOWS;REGISTER\_PROXY\_DLL"

**C/C++ > Предварительно скомпилированный заголовок: выберите "Не использовать предварительно скомпилированные заголовки"**

**Компоновщик > Общие > Пропустить библиотеку импорта: выберите "Да"**

**Компоновщик > Ввод > Дополнительные зависимости: добавьте rpcrt4.lib;runtimeobject.lib**

**Компоновщик > Метаданные Windows > Создавать метаданные Windows: выберите "Нет"**

**Шаг 8.** Соберите проект "MyWinRTProxy".

**Развертывание прокси**

Прокси необходимо зарегистрировать глобально. Проще всего это сделать с помощью вызова DllRegisterServer из proxy dll в процессе установки. Обратите внимание, что, поскольку этот компонент поддерживает только серверы для архитектуры x86 (т.е. не поддерживает 64-разрядные системы), то в простейшей конфигурации используется 32-разрядный сервер, 32-разрядный прокси и 32-разрядное неопубликованное приложение. Прокси обычно размещается вместе с реализацией**winmd**для классического компонента.

Необходимо выполнить дополнительный шаг настройки. Чтобы процесс загрузки без публикации загружал и выполнял прокси, необходимо присвоить каталогу отметку "чтение/выполнение" для ALL_APPLICATION_PACKAGES. Это осуществляется с помощью**icacls.exe**средство командной строки. Эту команду нужно выполнить в каталоге, где реализации**winmd**и dll прокси/заглушки:

*icacls . /T /grant \*S-1-15-2-1:RX*

## <a name="patterns-and-performance"></a>Шаблоны и производительность

Очень важно тщательно отслеживать производительность межпроцессного транспорта. Вызов между процессами требует как минимум в два раза больше ресурсов, чем вызов внутри процесса. Создание межпроцессного взаимодействия для насыщенных сеансов или регулярная передача больших объектов (например, точечных рисунков) может неожиданно снизить производительность приложения.

Вот далеко не полный список того, чему необходимо уделить внимание:

-   Следует избегать вызова синхронных методов из потока пользовательского интерфейса приложения, адресованных серверу. Если требуется, вызывайте методы из фонового потока в приложении, а затем используйте CoreWindowDispatcher, чтобы передать результаты в поток пользовательского интерфейса.

-   Вызов асинхронных операций из потока пользовательского интерфейса приложения безопасен, но не забывайте об упомянутых выше проблемах с производительностью.

-   Массовая передача результатов снижает межпроцессное взаимодействие. Обычно она выполняется с помощью конструкции Array среды выполнения Windows.

-   Возврат*список<T>* где*T*— объект из асинхронной операции или извлеченное свойство, потребует много межпроцессных. Например, предположим, что вы возвращаете*список&lt;людей&gt;* объектов. Каждая итерация будет межпроцессным вызовом. Каждый*людей*объекта, возвращенного представляется прокси-сервера и каждый вызов метода или свойства этого отдельного объекта приведет к межпроцессному вызову. Поэтому «безобидный»*список&lt;людей&gt;* объект где*количество*большой приведет к большое количество вызовов медленно. Массовая передача структур содержимого в массиве обеспечивает более высокую производительность. Пример.

```csharp
struct PersonStruct
{
    String LastName;
    String FirstName;
    int Age;
   // etc.
}
```

Затем вернитесь*PersonStruct\ [\]* вместо*список&lt;PersonObject&gt;*.
В этом случае все данные будут получены за один сеанс межпроцессного взаимодействия.

Как всегда в вопросах производительности, большую роль играют измерение и тестирование. В идеале телеметрию нужно включать в разные операции, чтобы определить их длительность. Важно получить показания в диапазоне: например, как долго его действительно занимает получение всех*людей*объекты для конкретного запроса в неопубликованном приложении?

Еще один способ — переменное нагрузочное тестирование. Для этого в приложение включаются тестовые обработчики производительности, которые вводят различные отсроченные загрузки в выполняемые на сервере процессы обработки. Таким образом имитируются различные типы нагрузки и реакция приложения на изменение производительности сервера.
В примере показано, как правильно использовать асинхронный метод для включения в код задержек по времени. Точное количество включаемых в код задержек и диапазон случайной выборки для этой искусственной нагрузки зависит от конструкции приложения и предполагаемой среды, в которой оно будет выполняться.

## <a name="development-process"></a>Процесс разработки

При внесении изменений в сервер необходимо убедиться, что не выполняются его предыдущие экземпляры. COM в конечном счете очистит процесс, но время очистки будет больше, чем это требуется для эффективной последовательной разработки. Таким образом, завершение работы предыдущего экземпляра является нормальным действием в процессе разработки. При этом разработчику необходимо следить за тем, в каком экземпляре процесса dllhost.exe располагается сервер.

Процесс сервера можно найти и завершить с помощью диспетчера задач или других, сторонних приложений. Средство командной строки**TaskList.exe**также включен и имеет гибким синтаксисом, например:

  
 | **Команда** | **Действие** |
 | ------------| ---------- |
 | tasklist | Выводится список всех запущенных процессов в порядке их создания (самые последние — в конце списка). |
 | tasklist /FI "IMAGENAME eq dllhost.exe" /M | Выводится информация обо всех экземплярах процесса dllhost.exe. Параметр /M выводит список модулей, загруженных этими экземплярами. |
 | tasklist /FI "PID eq 12564" /M | С помощью этого параметра можно запросить информацию о процессе dllhost.exe, используя его PID (идентификационный номер процесса). |

Для сервера брокера должен списке*clrhost.dll*в списке загруженных модулей.

## <a name="resources"></a>Ресурсы

-   [Шаблоны проектов для компонентов WinRT, работающих через брокер, для Windows10 и VS2015](https://visualstudiogallery.msdn.microsoft.com/10be07b3-67ef-4e02-9243-01b78cd27935)

-   [NorthwindRT (пример компонента WinRT, работающего через брокер)](http://go.microsoft.com/fwlink/p/?LinkID=397349)

-   [Создание надежных и заслуживающих доверия приложений Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkID=393644)

-   [Контракты и расширения приложений (приложения Microsoft Store)](https://msdn.microsoft.com/library/windows/apps/hh464906.aspx)

-   [Как загружать неопубликованные приложения в Windows10](https://msdn.microsoft.com/windows/uwp/get-started/enable-your-device-for-development#GroupPolicy)

-   [Развертывание приложений UWP для бизнеса](http://go.microsoft.com/fwlink/p/?LinkID=264770)

