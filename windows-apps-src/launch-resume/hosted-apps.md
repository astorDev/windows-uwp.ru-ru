---
Description: Узнайте, как создать размещенное приложение, которое наследует исполняемый файл, атрибуты точки входа и среды выполнения ведущего приложения.
title: Создание размещенных приложений
ms.date: 04/23/2020
ms.topic: article
keywords: Windows 10, Настольный компьютер, пакет, удостоверение, MSIX, Win32
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 1847fb707d633cc7960b3b9767db974452414a25
ms.sourcegitcommit: eae9859ee06c1e5e4afa08d8d3da072ad06d24a8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84110391"
---
# <a name="create-hosted-apps"></a>Создание размещенных приложений

Начиная с Windows 10 версии 2004, можно создавать *размещенные приложения*. Размещенное приложение использует тот же исполняемый файл и определение, что и родительское *ведущее* приложение, но выглядит и ведет себя как отдельное приложение в системе.

Размещенные приложения полезны в сценариях, где требуется, чтобы компонент (например, исполняемый файл или файл сценария) работал как автономное приложение Windows 10, но для работы компонента требуется процесс узла. Например, сценарий PowerShell или Python можно доставить как размещенное приложение, для выполнения которого требуется установка узла. Размещенное приложение может иметь собственные плитку начального экрана, удостоверение и глубокую интеграцию с такими компонентами Windows 10, как фоновые задачи, уведомления, плитки и получатели данных.

Функция размещенных приложений поддерживается несколькими элементами и атрибутами в манифесте пакета, которые позволяют размещенному приложению использовать исполняемый файл и определение в пакете ведущего приложения. Когда пользователь запускает размещенное приложение, операционная система автоматически запускает исполняемый файл узла с удостоверением размещенного приложения. Узел может загружать визуальные ресурсы, содержимое или вызывать API в качестве размещенного приложения. Размещенное приложение получает пересечение возможностей, объявленных между узлом и размещенным приложением. Это означает, что размещенное приложение не может запросить больше возможностей, чем предоставляет узел.

## <a name="define-a-host"></a>Определение узла

*Узел* — это основной исполняемый файл или процесс среды выполнения для размещенного приложения. В настоящее время единственными поддерживаемыми узлами являются классические приложения (.NET или C++/Win32), имеющие *удостоверение пакета*. В настоящее время приложения UWP не поддерживаются как узлы. Существует несколько способов, с помощью которых классическое приложение имеет удостоверение пакета:

* Самый распространенный способ предоставить удостоверение пакета для классического приложения — [упаковать его в пакет MSIX](https://docs.microsoft.com/windows/msix).
* В некоторых случаях вы можете предоставить удостоверение пакета, создав [разреженный пакет](https://docs.microsoft.com/windows/apps/desktop/modernize/grant-identity-to-nonpackaged-apps). Этот параметр полезен, если не удается внедрить MSIX упаковку для развертывания классического приложения.

Узел объявляется в манифесте пакета с помощью расширения [**uap10: хострунтиме**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap10-hostruntime) . Это расширение имеет атрибут **ID** , которому необходимо присвоить значение, на которое также ссылается манифест пакета для размещенного приложения. Когда размещенное приложение активируется, узел запускается по идентификатору размещенного приложения и может загружать содержимое или двоичные файлы из пакета размещенного приложения.

В следующем примере показано, как определить узел в манифесте пакета. Расширение **uap10: хострунтиме** является частью всего пакета и, следовательно, объявлено как дочерний элемент [**пакета**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-package) .

``` xml
<Package xmlns:uap10="http://schemas.microsoft.com/appx/manifest/uap/windows10/10">

  <Extensions>
    <uap10:Extension Category="windows.hostRuntime"  
        Executable="PyScriptEngine\PyScriptEngine.exe"  
        uap10:RuntimeBehavior="packagedClassicApp"  
        uap10:TrustLevel="mediumIL">
      <uap10:HostRuntime Id="PythonHost" />
    </uap10:Extension>
  </Extensions>

</Package>
```

Запишите эти важные сведения о следующих элементах.

| Элемент              | Сведения |
|----------------------|-------|
| [**uap10:Extension**](https://docs.microsoft.com/wp/schemas/appxpackage/uapmanifestschema/element-uap10-extension) | `windows.hostRuntime`Категория объявляет расширение для всего пакета, которое определяет сведения среды выполнения, используемые при активации размещенного приложения. Размещенное приложение будет выполняться с определениями, объявленными в расширении. При использовании ведущего приложения, объявленного в предыдущем примере, размещенное приложение будет выполняться как исполняемый файл **пискриптенгине. exe** на уровне доверия **медиумил** .<br/><br/>Атрибуты **исполняемого файла**, **uap10: рунтимебехавиор**и **uap10: TrustLevel** указывают имя двоичного процесса хоста в пакете и то, как будут выполняться размещенные приложения. Например, размещенное приложение, использующее атрибуты из предыдущего примера, будет выполняться как исполняемый файл Пискриптенгине. exe на уровне доверия Медиумил. |
| [**uap10:HostRuntime**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap10-hostruntime) | Атрибут **ID** объявляет уникальный идентификатор этого конкретного ведущего приложения в пакете. Пакет может иметь несколько ведущих приложений, каждый из которых должен иметь элемент **uap10: хострунтиме** с уникальным **идентификатором**.

## <a name="declare-a-hosted-app"></a>Объявление размещенного приложения

*Размещенное приложение* объявляет зависимость пакета на *узле*. Размещенное приложение использует идентификатор узла (то есть атрибут **ID** расширения **uap10: хострунтиме** в пакете узла) для активации вместо указания исполняемого файла точки входа в собственном пакете. Размещенное приложение обычно содержит содержимое, визуальные ресурсы, скрипты или двоичные файлы, к которым может получить доступ узел. Значение [**TargetDeviceFamily**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-targetdevicefamily) в размещенном пакете приложения должно указывать на то же значение, что и узел.

Пакеты размещенных приложений могут быть подписаны или не подписаны:

* Подписанные пакеты могут содержать исполняемые файлы. Это полезно в сценариях с механизмом двоичного расширения, который позволяет узлу загружать библиотеку DLL или зарегистрированный компонент в пакете размещенного приложения.
* Неподписанные пакеты могут содержать только неисполняемые файлы. Это полезно в тех случаях, когда узлу требуется только загрузка изображений, ресурсов и содержимого или файлов скриптов. Неподписанные пакеты должны включать в себя специальное `OID` значение в элементе [**Identity**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) , иначе они не смогут регистрироваться. Это предотвращает конфликт неподписанных пакетов с или подмену удостоверения подписанного пакета.

Чтобы определить размещенное приложение, объявите в манифесте пакета следующие элементы:

* Элемент [**uap10: хострунтимедепенденци**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap10-hostruntimedependency) . Это дочерний элемент элемента [Dependencies](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-dependencies) .
* Атрибут **uap10: HostID** элемента [**Application**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) (для приложения) или элемента [**Extension**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-1-extension) (для расширения активируемого).

В следующем примере показаны соответствующие разделы манифеста пакета для неподписанного размещенного приложения.

``` xml
<Package xmlns:uap10="http://schemas.microsoft.com/appx/manifest/uap/windows10/10">

  <Identity Name="NumberGuesserManifest"
    Publisher="CN=AppModelSamples, OID.2.25.311729368913984317654407730594956997722=1"
    Version="1.0.0.0" />

  <Dependencies>
    <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.19041.0" MaxVersionTested="10.0.19041.0" />
    <uap10:HostRuntimeDependency Name="PyScriptEnginePackage" Publisher="CN=AppModelSamples" MinVersion="1.0.0.0"/>
  </Dependencies>

  <Applications>
    <Application Id="NumberGuesserApp"  
      uap10:HostId="PythonHost"  
      uap10:Parameters="-Script &quot;NumberGuesser.py&quot;">
    </Application>
  </Applications>

</Package>
```

Запишите эти важные сведения о следующих элементах.

| Элемент              | Сведения |
|----------------------|-------|
| [**Идентификация**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) | Так как пакет размещенного приложения в этом примере не подписан, атрибут **издателя** должен содержать `OID.2.25.311729368913984317654407730594956997722=1` строку. Это гарантирует, что неподписанный пакет не сможет подменить удостоверение подписанного пакета. |
| [**TargetDeviceFamily**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-targetdevicefamily) | Атрибут **MinVersion** должен указывать 10.0.19041.0 или более ПОЗДНЮЮ версию ОС. |
| [**uap10:HostRuntimeDependency**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap10-hostruntimedependency)  | Этот элемент объявляет зависимость от пакета главного приложения. Это состоит из **имени** и **издателя** пакета узла и набора **MinVersion** , от которого он зависит. Эти значения можно найти в элементе [Identity](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) в пакете узла. |
| [**Развертывание**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) | Атрибут **uap10: HostID** выражает зависимость от узла. Пакет размещенного приложения должен объявлять этот атрибут вместо обычных атрибутов **исполняемого файла** и **точки входа** для элемента [**приложения**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) или [**расширения**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-1-extension) . В результате размещенное приложение наследует атрибуты **исполняемого файла**, **точки входа** и среды выполнения от узла с соответствующим значением **HostID** .<br/><br/>Атрибут **uap10: Parameters** задает параметры, которые передаются в функцию точки входа исполняемого объекта узла. Поскольку ведущему приложению необходимо узнать, что делать с этими параметрами, существует подразумеваемый контракт между узлом и размещенным приложением. |

## <a name="register-an-unsigned-hosted-app-package-at-run-time"></a>Регистрация пакета неподписанного размещенного приложения во время выполнения

Одно из преимуществ расширения **uap10: хострунтиме** заключается в том, что оно позволяет узлу динамически создавать размещенный пакет приложения во время выполнения и регистрировать его с помощью API [**PackageManager**](https://docs.microsoft.com/uwp/api/windows.management.deployment.packagemanager) , не требуя подписывать его. Это позволяет узлу динамически создавать содержимое и манифест для размещенного пакета приложения, а затем регистрировать его.

Используйте следующие методы класса [**PackageManager**](https://docs.microsoft.com/uwp/api/windows.management.deployment.packagemanager) для регистрации неподписанного пакета размещенного приложения. Эти методы доступны начиная с Windows 10 версии 2004.

* [**Аддпаккажебюриасинк**](https://docs.microsoft.com/uwp/api/windows.management.deployment.packagemanager.addpackagebyuriasync): регистрирует неподписанный пакет MSIX с помощью свойства **алловунсигнед** параметра *Options* .
* [**Регистерпаккажебюриасинк**](https://docs.microsoft.com/uwp/api/windows.management.deployment.packagemanager.registerpackagebyuriasync): выполняет нежесткую регистрацию файла манифеста пакета. Если пакет подписан, то папка, содержащая манифест, должна включать p7x- [файл](https://docs.microsoft.com/windows/msix/overview#inside-an-msix-package) и каталог. Если значение не подписано, должно быть задано свойство **алловунсигнед** параметра *Options* .

### <a name="requirements-for-unsigned-hosted-apps"></a>Требования к неподписанным размещенным приложениям

* Элементы [**приложения**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) или [**расширения**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-1-extension) в манифесте пакета не могут содержать данные **активации, такие**как **исполняемые**, входные или **TrustLevel** атрибуты. Вместо этого эти элементы могут содержать только атрибут **uap10: HostID** , который выражает зависимость от узла и атрибута **uap10: Parameters** .
* Пакет должен быть основным пакетом. Это не может быть набор, пакет платформы, ресурс или дополнительный пакет.

### <a name="requirements-for-a-host-that-installs-and-registers-an-unsigned-hosted-app-package"></a>Требования для узла, который устанавливает и регистрирует неподписанный пакет размещенного приложения

* Узел должен иметь [удостоверение пакета](#define-a-host).
* Узел должен иметь [ограниченную функцию](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations#restricted-capabilities) **packageManagement** .
    ```xml
    <rescap:Capability Name="packageManagement" />
    ```

<!--
* If the host runs in app container (for example, it is a UWP app), it must also have the unsigned package management [custom capability](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations#custom-capabilities) and a [Signed Custom Capability Descriptor (SCCD) file](https://docs.microsoft.com/windows-hardware/drivers/devapps/hardware-support-app--hsa--steps-for-driver-developers#preparing-the-signed-custom-capability-descriptor-sccd-file).
    ```xml
    <uap4:CustomCapability Name="Microsoft.unsignedPackageManagement_cw5n1h2txyewy" />
    ```
-->

## <a name="sample"></a>Образец

Для полнофункционального примера приложения, которое объявляет себя как узел, а затем динамически регистрирует размещенный пакет приложения во время выполнения, см. [Пример размещенного приложения](https://github.com/microsoft/AppModelSamples/tree/master/Samples/HostedApps).

### <a name="the-host"></a>Узел.

Узел называется **пискриптенгине**. Это оболочка, написанная на языке C# и выполняющая скрипты Python. При запуске с `-Register` параметром обработчик скриптов устанавливает размещенное приложение, содержащее скрипт Python. Когда пользователь пытается запустить только что установленное размещенное приложение, узел запускается и выполняет сценарий Python **нумбергуессер** .

Манифест пакета для ведущего приложения (файл Package. appxmanifest в папке Пискриптенгинепаккаже) содержит расширение **uap10: хострунтиме** , которое объявляет приложение в качестве узла с идентификатором **писонхост** и исполняемым файлом **пискриптенгине. exe**.  

> [!NOTE]
> В этом примере манифест пакета называется Package. appxmanifest и является частью [проекта упаковки приложений Windows](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-packaging-dot-net). При построении этого проекта [создается манифест с именем appxmanifest. XML](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/generate-package-manifest) и создается пакет MSIX для ведущего приложения.

### <a name="the-hosted-app"></a>Размещенное приложение

Размещенное приложение состоит из скрипта Python и артефактов пакета, таких как манифест пакета. Он не содержит PE.

Манифест пакета для размещенного приложения (файл Нумбергуессер/AppxManifest. XML) содержит следующие элементы:

* Атрибут **издателя** элемента [**Identity**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-identity) содержит `OID.2.25.311729368913984317654407730594956997722=1` идентификатор, который необходим для неподписанного пакета.
* Атрибут **uap10: HostID** элемента [**Application**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-application) определяет **писонхост** в качестве своего узла.

### <a name="run-the-sample"></a>Запуск примера

Для примера требуется версия 10.0.19041.0 или более поздняя версия Windows 10 и Windows SDK.

1. Скачайте [Пример](https://aka.ms/hostedappsample) в папку на компьютере разработчика.
2. Откройте решение Пискриптенгине. sln в Visual Studio и установите проект **пискриптенгинепаккаже** в качестве запускаемого проекта.
3. Создайте проект **пискриптенгинепаккаже** .
4. В обозреватель решений щелкните правой кнопкой мыши проект **пискриптенгинепаккаже** и выберите пункт **развернуть**.
5. Откройте окно командной строки в каталоге, в который были скопированы образцы файлов, и выполните следующую команду, чтобы зарегистрировать пример приложения **нумбергуессер** (размещенное приложение). Перейдите в путь, по `D:\repos\HostedApps` которому скопированы образцы файлов.

    ```CMD
    D:\repos\HostedApps>pyscriptengine -Register D:\repos\HostedApps\NumberGuesser\AppxManifest.xml
    ```

    > [!NOTE]
    > Можно выполнить `pyscriptengine` в командной строке, так как узел в примере объявляет [**аппексекутионалиас**](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap5-appexecutionalias).

6. Откройте меню " **Пуск** " и выберите **нумбергуессер** , чтобы запустить размещенное приложение.
