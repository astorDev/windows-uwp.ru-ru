---
description: C#/WinRT — это набор инструментов, обеспечивающий поддержку проекций WinRT для кода C#.
title: C#/WinRT
ms.date: 05/19/2020
ms.topic: article
keywords: Windows 10, UWP, Standard, c#, winrt, cswinrt, проекция
ms.localizationpriority: medium
ms.openlocfilehash: e52763d78937405b308c4c4fe06f6d231fa3abcf
ms.sourcegitcommit: d0f479f1955881afb62c2af249db5d0b053b63e5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83580281"
---
# <a name="cwinrt"></a>C#/WinRT

> [!IMPORTANT]
> Набор инструментов C#/WinRT находится на стадии общедоступной предварительной версии и может быть существенно изменен перед окончательным выпуском. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.

C#/WinRT — это набор средств в виде пакета NuGet, обеспечивающий поддержку проекций среды выполнения Windows (WinRT) для языка C#. *Проекция* — это уровень перевода (например, сборка взаимодействия), который позволяет программировать API-интерфейсы WinRT естественным и привычным способом для целевого языка. Например, проекция C#/WinRT скрывает сведения о взаимодействии между интерфейсами C# и WinRT и обеспечивает сопоставление многих типов WinRT соответствующим эквивалентам .NET, например строкам, URI, общим типам значений и универсальным коллекциям.

В настоящее время C#/WinRT обеспечивает поддержку использования типов WinRT, а текущая предварительная версия позволяет [создавать](#create-an-interop-assembly) сборки взаимодействия WinRT и [ссылаться](#reference-an-interop-assembly) на них. В будущих выпусках C#/WinRT будет добавлена поддержка создания типов WinRT в C#.

## <a name="motivation-for-cwinrt"></a>Обоснование для использования C#/WinRT

[.NET Core](https://docs.microsoft.com/dotnet/core/) — это основное направление развития платформы .NET, а .NET 5 — следующий основной выпуск. Это кроссплатформенная среда выполнения с открытым исходным кодом, с помощью которой можно создавать приложения для устройств, облаков и Интернета вещей.

В предыдущие версии .NET Framework и .NET Core были встроены средства поддержки технологии Windows под названием WinRT. С целью обеспечения переносимости и повышения эффективности .NET 5 мы переместили средства поддержки проекции WinRT из компилятора и среды выполнения .NET в набор средств C#/WinRT. Задача C#/WinRT заключается в обеспечении такого же уровня поддержки WinRT, как и в более ранних версиях компилятора C# и среды выполнения .NET. Дополнительные сведения см. в статье [Сопоставление типов .NET с типами среды выполнения Windows](https://docs.microsoft.com/windows/uwp/winrt-components/net-framework-mappings-of-windows-runtime-types).

C#/WinRT также поддерживает WinUI 3.0. Данный выпуск WinUI избавляет операционную систему от встроенных элементов управления и компонентов пользовательского интерфейса Майкрософт. Это позволяет разработчикам приложений использовать новейшие элементы управления и визуальные элементы в Windows 10 версии 1803 и более поздних.

Наконец, C#/WinRT является общим набором средств и предназначен для поддержки других сценариев, при которых встроенная поддержка WinRT недоступна в компиляторе C# или в среде выполнения .NET. C#/WinRT поддерживает версии среды выполнения .NET, совместимые со всеми версиями вплоть до .NET Standard 2.0, например Mono 5.4.

Дополнительные сведения о C#/WinRT см. в [репозитории C#/WinRT на сайте GitHub](https://aka.ms/cswinrt/repo).

## <a name="create-an-interop-assembly"></a>Создание сборки взаимодействия

API-интерфейсы WinRT определены в файлах метаданных Windows (WINMD). Пакет NuGet набора средств C#/WinRT включает компилятор C#/WinRT (**cswinrt**), который можно использовать для обработки файлов метаданных Windows и создания кода C# для .NET Standard 2.0. Эти исходные файлы можно скомпилировать в сборки взаимодействия по тому же принципу, по которому [C++/WinRT](../cpp-and-winrt-apis/index.md) создает заголовки для языковой проекции C++. Затем можно распространить сборки взаимодействия C#/WinRT, чтобы приложения могли ссылаться на них на ряду со сборками среды выполнения C#/WinRT.

### <a name="invoke-cswinrtexe"></a>Вызов cswinrt.exe

Чтобы отобразить параметры командной строки, введите `cswinrt -?`. Для вызова cswinrt.exe из проекта рекомендуется использовать файл Directory.Build.Targets. В следующем фрагменте проекта показан простой вызов **cswinrt** с целью создания источников проекции для типов в пространстве имен Contoso. Затем эти источники будут включены в сборку проекта.

```xml
  <Target Name="GenerateProjection" BeforeTargets="Build">
    <PropertyGroup>
      <CsWinRTParams>
# This sample demonstrates using a response file for cswinrt execution.
# Run "cswinrt -h" to see all command line options.
-verbose
# Include Windows SDK metadata to satisfy references to 
# Windows types from project-specific metadata.
-in 10.0.18362.0
# Don't project referenced Windows types, as these are 
# provided by the Windows interop assembly.
-exclude Windows 
# Reference project-specific winmd files, defined elsewhere,
# such as from a NuGet package.
-in @(ContosoWinMDs->'"%(FullPath)"', ' ')
# Include project-specific namespaces/types in the projection
-include Contoso 
# Write projection sources to the "Generated Files" folder,
# which should be excluded from checkin (e.g., .gitignored).
-out "$(ProjectDir)Generated Files"
      </CsWinRTParams>
    </PropertyGroup>
    <WriteLinesToFile
        File="$(CsWinRTResponseFile)" Lines="$(CsWinRTParams)"
        Overwrite="true" WriteOnlyWhenDifferent="true" />
    <Message Text="$(CsWinRTCommand)" Importance="$(CsWinRTVerbosity)" />
    <Exec Command="$(CsWinRTCommand)" />
  </Target>

  <Target Name="IncludeProjection" BeforeTargets="CoreCompile" AfterTargets="GenerateProjection">
    <ItemGroup>
      <Compile Include="$(ProjectDir)Generated Files/*.cs" Exclude="@(Compile)" />
    </ItemGroup>
  </Target>
```

### <a name="distribute-the-interop-assembly"></a>Распространение сборки взаимодействия

Сборка взаимодействия обычно распространяется в виде пакета NuGet вместе с зависимостью от пакета NuGet C#/WinRT для требуемой сборки среды выполнения C#/WinRT (**winrt.runtime.dll**). Существует две версии сборки среды выполнения C#/WinRT: одна для .NET Standard 2.0, а другая для .Net 5.0. В зависимости от целевой платформы приложения развертывается либо одна, либо другая. 

* Платформа .NET Standard 2.0 подходит в качестве целевой для кроссплатформенных приложений нижнего уровня, которые должны обеспечивать облегченную функциональность в Windows.
* Платформу .NET 5.0 рекомендуется использовать в качестве целевой для современных приложений Windows, которые требуют правильной сборки мусора по ссылкам на собственные объекты, например приложений XAML.

При добавлении в файл nuspec условия `targetFramework` сборка взаимодействия может гарантировать развертывание правильной версии среды выполнения C#/WinRT для приложения.

```xml
<?xml version="1.0" encoding="utf-8"?>
<package xmlns="http://schemas.microsoft.com/packaging/2013/05/nuspec.xsd">
  <metadata>
    <dependencies>
      <group targetFramework=".NETStandard2.0">
        <dependency id="Microsoft.Windows.CsWinRT" version="0.1.0" />
      </group>
      <group targetFramework=".NET5.0">
        <dependency id="Microsoft.Windows.CsWinRT" version="0.1.0" />
      </group>
    </dependencies>
  </metadata>
</package>
```

> [!NOTE]
> Моникер целевой платформы для .NET 5.0 перемещается из ".NETCoreApp5.0" в ".NET5.0". В предварительных выпусках C#/WinRT будет использоваться один или другой.

## <a name="reference-an-interop-assembly"></a>Ссылка на сборку взаимодействия

Как правило, на сборки взаимодействия C#/WinRT ссылаются проекты приложений. Однако на них также могут ссылаться промежуточные сборки взаимодействия. Например, сборка взаимодействия WinUI ссылается на сборку взаимодействия Windows SDK.

Чтобы использовать планируемые типы C#/WinRT, добавьте в проект ссылку на соответствующий пакет NuGet сборки взаимодействия C#/WinRT. В результате в проект будут добавлены сборка взаимодействия и сборка среды выполнения C#/WinRT.

Если вы распространяете сторонний компонент WinRT без официальной сборки взаимодействия, для создания собственных частных источников проекции в проекте приложения можно выполнить процедуру [создания сборки взаимодействия](#create-an-interop-assembly). Мы не рекомендуем использовать этот подход, так как он может привести к конфликтам проекций одного и того же типа в рамках одного процесса. Для предотвращения таких ситуаций используются пакеты NuGet, созданные по схеме [семантического версионирования](https://semver.org). Предпочтительно использовать официальную сборку взаимодействия стороннего производителя.

### <a name="winrt-type-activation"></a>Активация типа WinRT

C#/WinRT поддерживает активацию типов WinRT, размещенных в операционной системе, а также сторонних компонентов, например [Win2D](https://www.nuget.org/packages/Win2D.uwp/). Поддержка активации сторонних компонентов в классическом приложении включается путем [активации WinRT без регистрации](https://blogs.windows.com/windowsdeveloper/2019/04/30/enhancing-non-packaged-desktop-apps-using-windows-runtime-components/), доступной в Windows 10 версии 1903 и более поздних. Если компонент создан специально для приложений UWP, для нее также может потребоваться пакет [серверов пересылки VCRT](https://www.nuget.org/packages/Microsoft.VCRTForwarders.140/).

C#/WinRT также предоставляет резервный путь активации, если Windows не удается активировать тип, как описано выше. В этом случае C#/WinRT пытается определить расположение собственной библиотеки DLL реализации на основе полного имени типа, постепенно удаляя элементы. Например, резервная логика попытается активировать тип Contoso.Controls.Widget из перечисленных ниже модулей в такой последовательности:

1. Contoso.Controls.Widget.dll
2. Contoso.Controls.dll
3. Contoso.dll

C#/WinRT использует для поиска библиотеки DLL реализации [альтернативный порядок поиска LoadLibrary](https://docs.microsoft.com/windows/win32/dlls/dynamic-link-library-search-order?#alternate-search-order-for-desktop-applications). Пакет приложения, в котором предусмотрено использование такого резервного способа, наряду с модулем приложения должен включать библиотеку DLL реализации.

## <a name="known-issues"></a>Известные проблемы

В текущей предварительной версии C#/WinRT есть некоторые известные проблемы с производительностью, связанные с взаимодействием. Они будут устранены до окончательного выпуска в конце 2020 года.

Если у вас возникли какие-либо функциональные проблемы с пакетом NuGet программы C#/WinRT, компилятором cswinrt.exe или созданными источниками проекции, сообщите нам о них на [странице проблем с C#/WinRT](https://github.com/microsoft/CsWinRT/issues).

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Репозиторий C#/WinRT на сайте GitHub](https://aka.ms/cswinrt/repo)
