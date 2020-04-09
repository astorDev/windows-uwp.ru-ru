---
description: В этом руководстве показано, как добавлять пользовательские интерфейсы UWP XAML, создавать пакеты MSIX и включать другие актуальные компоненты в приложения WPF.
title: Перенос приложения Contoso Expenses в .NET Core 3
ms.topic: article
ms.date: 06/27/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, UWP, Windows Forms, WPF, объекты XAML Island
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 6a52e12f9d60ee4abb4b1aed3043a69c25845267
ms.sourcegitcommit: f34deba1d4460d85ed08fe9648999fe03ff6a3dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71317105"
---
# <a name="part-1-migrate-the-contoso-expenses-app-to-net-core-3"></a>Часть 1. Перенос приложения Contoso Expenses в .NET Core 3

Это первая часть руководства, в котором рассматривается модернизация примера классического приложения WPF Contoso Expenses. Общие сведения об этом руководстве, предварительные требования и инструкции по скачиванию примера приложения см. в разделе [Руководство. Модернизация приложения WPF](modernize-wpf-tutorial.md).
  
В этой части руководства вы полностью перенесете приложение Contoso Expenses с платформы .NET Framework 4.7.2 на [.NET Core 3](modernize-wpf-tutorial.md#net-core-3). Прежде чем приступить к этой части руководства, убедитесь, что вы [открыли пример приложения и выполнили его сборку](modernize-wpf-tutorial.md#get-the-contoso-expenses-sample-app) в Visual Studio 2019.

> [!NOTE]
> Дополнительные сведения о переносе приложения WPF с платформы .NET Framework на .NET Core 3 доступны в [этой серии записей блога](https://devblogs.microsoft.com/dotnet/migrating-a-sample-wpf-app-to-net-core-3-part-1/).

## <a name="migrate-the-contosoexpenses-project-to-net-core-3"></a>Перенос проекта ContosoExpenses на платформу .NET Core 3

В этом разделе вы перенесете проект ContosoExpenses в приложение Contoso Expenses на платформе .NET Core 3. Для этого вы создадите новый файл проекта, содержащий те же файлы, что и существующий проект ContosoExpenses, но нацеленный на .NET Core 3 вместо .NET Framework 4.7.2. Это позволит поддерживать одно решение с версиями приложения для платформ .NET Framework и .NET Core.

1. Убедитесь, что проект ContosoExpenses нацелен на .NET Framework 4.7.2. В обозревателе решений щелкните правой кнопкой мыши проект **ContosoExpenses**, выберите **Свойства** и убедитесь, что для свойства **Целевая платформа** на вкладке **Приложение** выбрана платформа .NET Framework 4.7.2.

    ![Платформа .NET Framework 4.7.2 для проекта](images/wpf-modernize-tutorial/NETFramework472.png)

3. В проводнике перейдите к папке **C:\WinAppsModernizationWorkshop\Lab\Exercise1\01-Start\ContosoExpenses** и создайте в ней текстовый файл **ContosoExpenses.Core.csproj**.

4. Щелкните этот файл правой кнопкой мыши, выберите **Открыть с помощью**, а затем откройте его в любом текстовом редакторе — Блокноте, Visual Studio Code или Visual Studio.

5. Скопируйте в файл приведенный ниже текст и сохраните его.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.WindowsDesktop">

      <PropertyGroup>
        <OutputType>WinExe</OutputType>
        <TargetFramework>netcoreapp3.0</TargetFramework>
        <UseWPF>true</UseWPF>
     </PropertyGroup>

    </Project>
    ```

6. Закройте файл и вернитесь к решению **ContosoExpenses** в Visual Studio.

7. Щелкните правой кнопкой мыши решение **ContosoExpenses** и выберите **Добавить > Существующий проект**. Выберите файл **ContosoExpenses.Core.csproj**, который вы только что создали в папке `C:\WinAppsModernizationWorkshop\Lab\Exercise1\01-Start\ContosoExpenses`, чтобы добавить его в решение.

Файл **ContosoExpenses.Core.csproj** содержит следующие элементы:

* Элемент **Project** указывает версию пакета SDK **Microsoft.NET.Sdk.WindowsDesktop**. Он предназначен для приложений .NET для Windows Desktop и включает в себя компоненты для приложений WPF и Windows Forms.
* Элемент **PropertyGroup** содержит дочерние элементы, указывающие, что выходной файл проекта является исполняемым (а не библиотекой DLL), нацелен на .NET Core 3 и использует WPF. Для приложения Windows Forms вместо элемента **UseWinForms** следует использовать элемент **UseWPF**.

> [!NOTE]
> При использовании формата CSPROJ, который появился в .NET Core 3.0, все файлы в папке, в которой находится CSPROJ-файл, считаются частью проекта. Поэтому не нужно указывать каждый файл, включаемый в проект. Необходимо указать только те файлы, для которых нужно определить настраиваемое действие сборки или которые необходимо исключить.

## <a name="migrate-the-contosoexpensesdata-project-to-net-standard"></a>Перенос проекта ContosoExpenses.Data на платформу .NET Standard

Решение **ContosoExpenses** включает в себя библиотеку классов **ContosoExpenses.Data**, которая содержит модели и интерфейсы для служб и нацелена на .NET 4.7.2. Приложения .NET Core 3.0 могут использовать библиотеки .NET Framework, если они не используют интерфейсы API, которые недоступны в .NET Core. Однако наилучший способ модернизации — переместить свои библиотеки в .NET Standard. Благодаря этому они будут полностью поддерживаться приложением .NET Core 3.0. Кроме того, библиотеку можно повторно использовать на других платформах, таких как веб-сайты (посредством ASP.NET Core) и мобильные платформы (посредством Xamarin).

Вот ка можно перенести проект **ContosoExpenses.Data** на платформу .NET Standard:

1. В Visual Studio щелкните правой кнопкой мыши проект **ContosoExpenses.Data** и выберите **Выгрузить проект**. Снова щелкните проект правой кнопкой мыши и выберите **Изменить ContosoExpenses.Data.csproj**.

2. Удалите все содержимое файла проекта.

3. Скопируйте и вставьте в этот файл приведенный ниже код XML, затем сохраните файл.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">

      <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
      </PropertyGroup>

    </Project>
    ```

4. Щелкните правой кнопкой мыши проект **ContosoExpenses.Data** и выберите **Перезагрузить проект**.

## <a name="configure-nuget-packages-and-dependencies"></a>Настройка пакетов NuGet и зависимостей

При переносе проектов **ContosoExpenses.Core** и **ContosoExpenses.Data** в предыдущих разделах вы удалили из них ссылки на пакеты NuGet. В этом разделе вы снова добавите эти ссылки.

Чтобы настроить пакеты NuGet для проекта **ContosoExpenses.Data**, сделайте следующее:

1. В проекте **ContosoExpenses.Data** разверните узел **Зависимости**. Обратите внимание на то, что отсутствует раздел **NuGet**.

    ![Пакеты NuGet](images/wpf-modernize-tutorial/NuGetPackages.png)

    При открытии **Packages.config** в **обозревателе решений** вы увидите "старые" ссылки на пакеты NuGet, которые использовались в проекте, когда он был основан на полной платформе .NET Framework.

    ![Зависимости и пакеты](images/wpf-modernize-tutorial/Packages.png)

    Ниже приведено содержимое файла **Packages.config**. Вы увидите, что все пакеты NuGet нацелены на полную платформу .NET Framework 4.7.2.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <packages>
      <package id="Bogus" version="26.0.2" targetFramework="net472" />
      <package id="LiteDB" version="4.1.4" targetFramework="net472" />
    </packages>
    ```

2. В проекте **ContosoExpenses.Data** удалите файл **Packages.config**.

4. В проекте **ContosoExpenses.Data** щелкните правой кнопкой мыши узел **Зависимости** и выберите **Управление пакетами NuGet**.

  ![Управление пакетами NuGet](images/wpf-modernize-tutorial/ManageNugetNETCORE3.png)

5. В окне **диспетчера пакетов NuGet** щелкните **Обзор**. Найдите пакет `Bogus` и установите его последнюю стабильную версию.

    ![Фиктивный пакет NuGet](images/wpf-modernize-tutorial/Bogus.png)

6. Найдите пакет `LiteDB` и установите его последнюю стабильную версию.

    ![Пакет NuGet LiteDB](images/wpf-modernize-tutorial/LiteDB.png)

    Возможно, вас интересует, где хранится список пакетов NuGet, так как проект больше не содержит файл Packages.config. Пакеты NuGet, на которые указывают ссылки, хранятся непосредственно в CSPROJ-файле. Это можно проверить, просмотрев содержимое файла проекта **ContosoExpenses.Data.csproj** в текстовом редакторе. В конце файла будут добавлены приведенные ниже строки.

    ```xml
    <ItemGroup>
       <PackageReference Include="Bogus" Version="26.0.2" />
       <PackageReference Include="LiteDB" Version="4.1.4" />
    </ItemGroup>
    ```

    > [!NOTE]
    > Вы также можете заметить, что для этого проекта .NET Core 3 вы устанавливаете те же пакеты, что и для проектов .NET Framework 4.7.2. Пакеты NuGet поддерживают несколько платформ. Авторы библиотек могут добавить в один пакет разные версии библиотеки, скомпилированные для разных архитектур и платформ. Эти пакеты поддерживают полную платформу .NET Framework и платформу .NET Standard 2.0, совместимые с проектами .NET Core 3. Дополнительные сведения о различиях платформ .NET Framework, .NET Core и .NET Standard см. в описании [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Чтобы настроить пакеты NuGet для проекта **ContosoExpenses.Core**, сделайте следующее:

1. В проекте **ContosoExpenses.Core** откройте файл **Packages. config**. Обратите внимание на то, что в настоящее время он содержит ссылки для .NET Framework 4.7.2, как показано ниже.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <packages>
      <package id="CommonServiceLocator" version="2.0.2" targetFramework="net472" />
      <package id="MvvmLightLibs" version="5.4.1.1" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
      <package id="Unity" version="5.10.2" targetFramework="net472" />
    </packages>
    ```

    На следующих шагах вы используете версии пакетов `MvvmLightLibs` и `Unity` для .NET Standard. Два других пакета — это зависимости, автоматически скачиваемые NuGet при установке этих двух библиотек.

2. В проекте **ContosoExpenses.Core** удалите файл **Packages.config**.

3. Щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Управление пакетами NuGet**.

4. В окне **диспетчера пакетов NuGet** щелкните **Обзор**. Найдите пакет `Unity` и установите его последнюю стабильную версию.

    ![Пакет Unity](images/wpf-modernize-tutorial/UnityPackage.png)

5. Найдите пакет `MvvmLightLibsStd10` и установите его последнюю стабильную версию. Это версия пакета `MvvmLightLibs` для .NET Standard. Для этого пакета автор упаковал версию библиотеки для .NET Standard в отдельный пакет, отличный от пакета версии .NET Framework.

    ![Пакет MvvmLightsLibs](images/wpf-modernize-tutorial/MvvmLightsLibsPackage.png)

6. В проекте **ContosoExpenses.Core** щелкните правой кнопкой мыши узел **Зависимости** и выберите **Добавить ссылку**.

7. В категории **Проекты > Решение** выберите **ContosoExpenses.Data** и нажмите кнопку **ОК**.

    ![Добавление ссылки](images/wpf-modernize-tutorial/AddReference.png)

## <a name="disable-auto-generated-assembly-attributes"></a>Отключение автоматически создаваемых атрибутов сборки

Если на этом этапе переноса вы попытаетесь выполнить сборку проекта **ContosoExpenses.Core**, то увидите сообщения об ошибках.

![Новые ошибки сборки для .NET Core 3](images/wpf-modernize-tutorial/NETCORE3BuildNewErrors.png)

Эта проблема возникает потому, что при использовании нового формата CSPROJ, появившегося в .NET Core 3.0, сведения о сборке хранятся в файле проекта, а не в файле **AssemblyInfo.cs**. Чтобы устранить эти ошибки, отключите это поведение и позвольте проекту продолжить использовать файл **AssemblyInfo.cs**.

1. В Visual Studio щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Выгрузить проект**. Снова щелкните проект правой кнопкой мыши и выберите **Изменить ContosoExpenses.Core.csproj**.

1. Добавьте приведенный ниже элемент в раздел **PropertyGroup** и сохраните файл.

    ```XML
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
    ```

    После добавления этого элемента раздел **PropertyGroup** должен выглядеть следующим образом.

    ```XML
    <PropertyGroup>
      <OutputType>WinExe</OutputType>
      <TargetFramework>netcoreapp3.0</TargetFramework>
      <UseWPF>true</UseWPF>
      <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
    </PropertyGroup>
    ```

3. Щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Перезагрузить проект**.

4. Щелкните правой кнопкой мыши проект **ContosoExpenses.Data** и выберите **Выгрузить проект**. Снова щелкните проект правой кнопкой мыши и выберите **Изменить ContosoExpenses.Data.csproj**.

5. Добавьте такую же запись в раздел **PropertyGroup** и сохраните файл.

    ```xml
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
    ```

    После добавления этого элемента раздел **PropertyGroup** должен выглядеть следующим образом.

    ```xml
    <PropertyGroup>
      <TargetFramework>netstandard2.0</TargetFramework>
      <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
    </PropertyGroup>
    ```

6. Щелкните правой кнопкой мыши проект **ContosoExpenses.Data** и выберите **Перезагрузить проект**.

## <a name="add-the-windows-compatibility-pack"></a>Добавление пакета обеспечения совместимости Windows

Если вы сейчас попытаетесь скомпилировать **ContosoExpenses.Core** и **ContosoExpenses.Data**, вы увидите, что предыдущие ошибки исправлены, но по-прежнему имеются ошибки в библиотеке **ContosoExpenses.Data**, как показано ниже.

`Services\RegistryService.cs(9,26,9,34): error CS0103: The name 'Registry' does not exist in the current context`
`Services\RegistryService.cs(12,26,12,34): error CS0103: The name 'Registry' does not exist in the current context`
`Services\RegistryService.cs(12,97,12,123): error CS0103: The name 'RegistryKeyPermissionCheck' does not exist in the current context`

Эти ошибки являются результатом преобразования проекта **ContosoExpenses.Data**, использовавшего библиотеку .NET Framework (для Windows), в проект на основе библиотеки .NET Standard, который может выполняться на нескольких платформах, включая Linux, Android, iOS и пр. Проект **ContosoExpenses.Data** содержит класс **RegistryService**, который взаимодействует с реестром, что характерно только для Windows.

Чтобы устранить эти ошибки, установите пакет NuGet [Windows Compatibility](https://www.nuget.org/packages/Microsoft.Windows.Compatibility). Этот пакет обеспечивает поддержку многих интерфейсов API Windows, используемых в библиотеке .NET Standard. После применения этого пакета библиотека больше не будет кроссплатформенной, но она по-прежнему будет нацелена на платформу .NET Standard. 

1. Щелкните правой кнопкой мыши проект **ContosoExpenses.Data**.
2. Выберите **Управление пакетами NuGet**.
3. В окне **диспетчера пакетов NuGet** щелкните **Обзор**. Найдите пакет `Microsoft.Windows.Compatibility` и установите его последнюю стабильную версию.

    ![](images/wpf-modernize-tutorial/WindowsCompatibilityPack.png)

4. Теперь повторите попытку компиляции проекта, щелкнув правой кнопкой мыши проект **ContosoExpenses.Data** и выбрав **Сборка**.

На этот раз процесс сборки завершится без ошибок.

## <a name="test-and-debug-the-migration"></a>Тестирование и отладка после переноса

Теперь, когда сборка проектов успешно выполнена, вы можете запустить и протестировать приложение, чтобы проверить наличие ошибок времени выполнения.

1. Щелкните проект **ContosoExpenses.Core** правой кнопкой мыши и выберите **Назначить запускаемым проектом**.

2. Нажмите клавишу F5, чтобы запустить проект **ContosoExpenses.Core** в отладчике. Вы увидите исключение, аналогичное приведенному ниже.

    ![Исключение в Visual Studio](images/wpf-modernize-tutorial/ExceptionNETCore3.png)

    Это исключение возникает из-за того, что при удалении содержимого из CSPROJ-файла в начале переноса вы удалили сведения о **действии сборки** для файлов образа. Чтобы устранить эту проблему, выполните следующие действия.

3. Остановите отладчик.

4. Щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Выгрузить проект**. Снова щелкните проект правой кнопкой мыши и выберите **Изменить ContosoExpenses.Core.csproj**.

5. Перед закрывающим тегом элемента **Project** добавьте следующую запись.

    ```xml
    <ItemGroup>
      <Content Include="Images/*">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
      </Content>
    </ItemGroup>
    ```

6. Щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Перезагрузить проект**.

7. Чтобы назначить приложению значок Contoso.ico, щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Свойства**. На открывшейся странице щелкните раскрывающийся список **Значок** и выберите `Images\contoso.ico`.

    ![Значок Contoso в свойствах проекта](images/wpf-modernize-tutorial/ContosoIco.png)

8. Нажмите кнопку **Сохранить**.

9. Нажмите клавишу F5, чтобы запустить проект **ContosoExpenses.Core** в отладчике. Убедитесь, что теперь приложение запустилось.

## <a name="next-steps"></a>Дальнейшие действия

На этом этапе работы с руководством вы успешно перенесли приложение Contoso Expenses на платформу .NET Core 3. Теперь вы готовы перейти к статье [Часть 2. Добавление элемента управления InkCanvas универсальной платформы Windows с помощью XAML Islands](modernize-wpf-tutorial-2.md)

> [!NOTE]
> Если у вас есть экран с высоким разрешением, вы можете заметить, что на нем приложение выглядит очень маленьким. Эту проблему можно устранить в следующей части руководства.
