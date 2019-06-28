---
description: Этот учебник демонстрирует добавление пользовательских интерфейсах XAML UWP, создание MSIX пакеты и другие современные компоненты включить в приложении WPF.
title: Перенос Contoso расходы приложений в .NET Core 3
ms.topic: article
ms.date: 06/27/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, универсальной платформы Windows, windows forms, wpf, о-ва xaml
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: e718de7a22873ccf347e60c661f724ce3abdd2cf
ms.sourcegitcommit: 1eec0e4fd8a5ba82803fdce6e23fcd01b9488523
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2019
ms.locfileid: "67420128"
---
# <a name="part-1-migrate-the-contoso-expenses-app-to-net-core-3"></a>Часть 1. Перенос Contoso расходы приложений в .NET Core 3

Это первая часть учебник, в котором показано, как модернизировать пример классического приложения WPF с именем Contoso расходы. Обзор учебника, предварительные требования и инструкции для загрузки примера приложения, см. в разделе [руководства: Модернизация приложения WPF](modernize-wpf-tutorial.md).
  
В этой части руководства будут перенесены всего приложения Contoso расходы с .NET Framework 4.7.2 [.NET Core 3](modernize-wpf-tutorial.md#net-core-3). Перед началом этой части руководства убедитесь, что вы выполните следующее:

* [Откройте и постройте образец ContosoExpenses](modernize-wpf-tutorial.md#get-the-contoso-expenses-sample-app) в Visual Studio 2019.
* При использовании версии выпуска Visual Studio 2019, включите в предварительных версиях пакета SDK для .NET Core. В Visual Studio, перейдите в раздел **Сервис > Параметры**, в поле поиска введите «Preview» и выберите **использовать предварительные версии пакета SDK для .NET Core**. Если вы используете [предварительную версию Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/), выберите этот параметр, так как предварительные версии .NET Core, включены по умолчанию не нужно.

## <a name="migrate-the-contosoexpenses-project-to-net-core-3"></a>Перенос проекта ContosoExpenses .NET Core 3

В этом разделе необходимо перенести проект ContosoExpenses в приложения Contoso для .NET Core 3. Это будет сделать, создав новый файл проекта, который содержит те же файлы, что существующий проект ContosoExpenses, но целевых объектов .NET Core 3 вместо .NET Framework 4.7.2. Это позволяет поддерживать единое решение с .NET Framework и .NET Core версии приложения.

1. Убедитесь, что ContosoExpenses в настоящее время проект ориентированного на .NET Framework 4.7.2. В обозревателе решений щелкните правой кнопкой мыши **ContosoExpenses** проекта, выберите **свойства**и убедитесь, что **требуемой версии .NET framework** свойство  **Приложение** вкладка имеет значение .NET Framework 4.7.2.

    ![Платформа .NET framework версии 4.7.2 для проекта](images/wpf-modernize-tutorial/NETFramework472.png)

3. В проводнике Windows перейдите к **C:\WinAppsModernizationWorkshop\Lab\Exercise1\01-Start\ContosoExpenses** папки и создайте новый текстовый файл с именем **ContosoExpenses.Core.csproj**.

4. Щелкните правой кнопкой файл и выберите **открыть с помощью**и откройте его в текстовом редакторе по своему усмотрению, например Блокноте, Visual Studio Code или Visual Studio.

5. Скопируйте следующий текст в файл и сохраните его.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.WindowsDesktop">

      <PropertyGroup>
        <OutputType>WinExe</OutputType>
        <TargetFramework>netcoreapp3.0</TargetFramework>
        <UseWPF>true</UseWPF>
     </PropertyGroup>

    </Project>
    ```

6. Закройте файл и вернуться к **ContosoExpenses** решения в Visual Studio.

7. Щелкните правой кнопкой мыши **ContosoExpenses** решений и выберите **Добавить -> существующий проект**. Выберите **ContosoExpenses.Core.csproj** файл был только что создали в `C:\WinAppsModernizationWorkshop\Lab\Exercise1\01-Start\ContosoExpenses` папки, чтобы добавить его в решение.

**ContosoExpenses.Core.csproj** включает следующие элементы:

* **Проекта** элемент указывает версию пакета SDK для **Microsoft.NET.Sdk.WindowsDesktop**. Это относится к .NET приложений для Windows Desktop, а также компоненты для приложений WPF и Windows Forms.
* **PropertyGroup** элемент содержит дочерние элементы, которые указывают выходные данные проекта — это исполняемый файл (не DLL), предназначенного .NET Core 3 и использует WPF. Для приложения Windows Forms, будет использовать **UseWinForms** вместо элемента **UseWPF** элемент.

> [!NOTE]
> При работе с в формат .csproj, представленные в .NET Core 3.0, все файлы в той же папке, что CSPROJ-файле, считаются частью проекта. Таким образом вам нет необходимости указывать каждый файл, включенный в проект. Необходимо указать только те файлы, для которых вы хотите определить действия пользовательские сборки или, которые требуется исключить.

## <a name="migrate-the-contosoexpensesdata-project-to-net-standard"></a>Перенос проекта ContosoExpenses.Data .NET Standard

**ContosoExpenses** решение включает в себя **ContosoExpenses.Data** библиотеку классов, которая содержит модели и интерфейсы для служб и предназначена для .NET 4.7.2. Приложения .NET core 3.0 можно использовать библиотеки .NET Framework, до тех пор, пока они не используют API, которые недоступны в .NET Core. Тем не менее оптимальный путь модернизации является перемещение библиотек для .NET Standard. Это будет убедитесь, что библиотека полностью поддерживается приложением .NET Core 3.0. Кроме того вы можете повторно использовать библиотеки, а также с других платформ, таких как веб-(с помощью ASP.NET Core) и mobile (с помощью Xamarin).

Для переноса **ContosoExpenses.Data** проект для .NET Standard:

1. В Visual Studio щелкните правой кнопкой мыши **ContosoExpenses.Data** проекта и выберите **выгрузить проект**. Щелкните правой кнопкой мыши проект и выберите **изменить ContosoExpenses.Data.csproj**.

2. Удалите все ее содержимое файла проекта.

3. Скопируйте и вставьте следующий XML-код и сохраните файл.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">

      <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
      </PropertyGroup>

    </Project>
    ```

4. Щелкните правой кнопкой мыши **ContosoExpenses.Data** проекта и выберите **перезагрузить проект**.

## <a name="configure-nuget-packages-and-dependencies"></a>Настройка пакетов NuGet и зависимостей

Когда вы перенесли **ContosoExpenses.Core** и **ContosoExpenses.Data** проектов в предыдущих разделах, ссылки на пакеты NuGet удалены из проектов. В этом разделе вы добавите эти ссылки обратно.

Чтобы настроить пакеты NuGet для **ContosoExpenses.Data** проекта:

1.  В **ContosoExpenses.Data** проекта, разверните **зависимости** узла. Обратите внимание, что **NuGet** отсутствует раздел.

    ![Пакеты NuGet](images/wpf-modernize-tutorial/NuGetPackages.png)

    Если вы откроете **Packages.config** в **обозревателе решений** вы найдете «старый» ссылки на пакеты NuGet используется проект, когда он использовал полной версии платформы .NET Framework.

    ![Зависимости и пакеты](images/wpf-modernize-tutorial/Packages.png)

    Вот содержимое **Packages.config** файла. Обратите внимание, что все пакеты NuGet нацелены на полную версию .NET Framework 4.7.2:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <packages>
      <package id="Bogus" version="26.0.2" targetFramework="net472" />
      <package id="LiteDB" version="4.1.4" targetFramework="net472" />
    </packages>
    ```

2. В **ContosoExpenses.Data** проекта, удалите **Packages.config** файла.

4. В **ContosoExpenses.Data** щелкните правой кнопкой мыши проект **зависимости** узел и выберите **управление пакетами NuGet**.

  ![Управление пакетами NuGet...](images/wpf-modernize-tutorial/ManageNugetNETCORE3.png)

5. В **диспетчер пакетов NuGet** окно, нажмите кнопку **Обзор**. Поиск `Bogus` пакет и установите его.

    ![Фиктивный пакета NuGet](images/wpf-modernize-tutorial/Bogus.png)

6. Поиск `LiteDB` пакет и установите его.

    ![Пакет LiteDB NuGet](images/wpf-modernize-tutorial/LiteDB.png)

    Читатель может спросить, где хранится эти список пакетов NuGet, так как проект больше не имеет файл packages.config. Указанные пакеты NuGet хранятся непосредственно в CSPROJ-файл. Это можно проверить, просмотрев содержимое **ContosoExpenses.Data.csproj** файл проекта в текстовом редакторе. Вы найдете следующие строки в конец файла добавляется:

    ```xml
    <ItemGroup>
       <PackageReference Include="Bogus" Version="26.0.2" />
       <PackageReference Include="LiteDB" Version="4.1.4" />
    </ItemGroup>
    ```

    > [!NOTE]
    > Также можно заметить, что вы устанавливаете те же пакеты, для этого проекта .NET Core 3 уже используется в проектах .NET Framework 4.7.2. Пакеты NuGet поддерживает многоплатформенного нацеливания. Библиотека авторы могут включать различные версии библиотеки, в том же пакете, скомпилированные для различных архитектур и платформ. Эти пакеты поддерживают полную версию .NET Framework, а также .NET Standard 2.0, который совместим с проектами .NET Core 3. Дополнительные сведения о различиях .NET Framework, .NET Core и .NET Standard см. в разделе [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

Чтобы настроить пакеты NuGet для **ContosoExpenses.Core** проекта:

1. В **ContosoExpenses.Core** откройте проект **packages.config** файла. Обратите внимание на то, что он в настоящее время содержит следующие ссылки, предназначенных для .NET Framework 4.7.2.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <packages>
      <package id="CommonServiceLocator" version="2.0.2" targetFramework="net472" />
      <package id="MvvmLightLibs" version="5.4.1.1" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
      <package id="Unity" version="5.10.2" targetFramework="net472" />
    </packages>
    ```

    В следующих шагах будет .NET Standard версии `MvvmLightLibs` и `Unity` пакетов. Два других зависимостей, скачанных, NuGet автоматически при установке этих двух библиотек.

2. В **ContosoExpenses.Core** проекта, удалите **Packages.config** файла.

3. Щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта и выберите **управление пакетами NuGet**.

4. В **диспетчер пакетов NuGet** окно, нажмите кнопку **Обзор**. Поиск `Unity` пакет и установите его.

    ![Пакет Unity](images/wpf-modernize-tutorial/UnityPackage.png)

5. Поиск `MvvmLightLibsStd10` пакет и установите его. Это .NET Standard версия `MvvmLightLibs` пакета. Для этого пакета автор выбрала для упаковки .NET Standard версии библиотеки в отдельном пакете, чем версия .NET Framework.

    ! Пакет MvvmLightsLibs[](images/wpf-modernize-tutorial/MvvmLightsLibsPackage.png)

6. В **ContosoExpenses.Core** щелкните правой кнопкой мыши проект **зависимости** узел и выберите **добавить ссылку**.

7. В **проекты > решение** категорию **ContosoExpenses.Data** и нажмите кнопку **ОК**.

    ![Добавление ссылки](images/wpf-modernize-tutorial/AddReference.png)

## <a name="disable-auto-generated-assembly-attributes"></a>Отключить атрибутов автоматически генерируемым сборки

На этом пункт в процессе миграции, при попытке выполнить сборку **ContosoExpenses.Core** проекта, вы увидите некоторые ошибки.

![Новые ошибки построения .NET core 3](images/wpf-modernize-tutorial/NETCORE3BuildNewErrors.png)

Эта проблема происходит потому, что новый формат CSPROJ появилась в .NET Core 3.0 хранилищ сведения о сборке в файле проекта, а не **AssemblyInfo.cs** файл. Чтобы избежать этих ошибок, отключить это поведение и позвольте продолжать использовать проект **AssemblyInfo.cs** файл.

1. В Visual Studio щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта и выберите **выгрузить проект**. Щелкните правой кнопкой мыши проект и выберите **изменить ContosoExpenses.Core.csproj**.

1. Добавьте следующий элемент в **PropertyGroup** раздела и сохраните файл.

    ```XML
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
    ```

    После добавления этого элемента **PropertyGroup** раздел теперь должен выглядеть следующим образом:

    ```XML
    <PropertyGroup>
      <OutputType>WinExe</OutputType>
      <TargetFramework>netcoreapp3.0</TargetFramework>
      <UseWPF>true</UseWPF>
      <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
    </PropertyGroup>
    ```

3. Щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта и выберите **перезагрузить проект**.

4. Щелкните правой кнопкой мыши **ContosoExpenses.Data** проекта и выберите **выгрузить проект**. Щелкните правой кнопкой мыши проект и выберите **изменить ContosoExpenses.Data.csproj**.

5. Добавьте ту же запись в **PropertyGroup** раздела и сохраните файл.

    ```xml
    <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
    ```

    После добавления этого элемента **PropertyGroup** раздел теперь должен выглядеть следующим образом:

    ```xml
    <PropertyGroup>
      <TargetFramework>netstandard2.0</TargetFramework>
      <GenerateAssemblyInfo>false</GenerateAssemblyInfo>
    </PropertyGroup>
    ```

6. Щелкните правой кнопкой мыши **ContosoExpenses.Data** проекта и выберите **перезагрузить проект**.

## <a name="add-the-windows-compatibility-pack"></a>Добавьте пакет обеспечения совместимости Windows

Если теперь попытаться выполнить компиляцию **ContosoExpenses.Core** и **ContosoExpenses.Data** проектов, вы увидите что будут исправлены ошибки, выданные, но по-прежнему имеются ошибки в  **ContosoExpenses.Data** библиотеки, подобным приведенным ниже.

`Services\RegistryService.cs(9,26,9,34): error CS0103: The name 'Registry' does not exist in the current context`
`Services\RegistryService.cs(12,26,12,34): error CS0103: The name 'Registry' does not exist in the current context`
`Services\RegistryService.cs(12,97,12,123): error CS0103: The name 'RegistryKeyPermissionCheck' does not exist in the current context`

Эти ошибки представляют собой результат преобразования **ContosoExpenses.Data** проекта из библиотеки .NET Framework (которая отображается для Windows) в библиотеку .NET Standard, которая может выполняться на нескольких платформах, включая Linux, Android, iOS, и многое другое. **ContosoExpenses.Data** проект содержит класс с именем **RegistryService**, который взаимодействует с реестром, это концепция только для Windows.

Чтобы устранить эти ошибки, установите [совместимости Windows](https://www.nuget.org/packages/Microsoft.Windows.Compatibility) пакет NuGet. Этот пакет предоставляет поддержку для многих API-интерфейсам конкретной Windows для использования в библиотеке .NET Standard. Библиотека больше не будет кросс платформенных после с помощью этого пакета, но он по-прежнему будет применять .NET Standard. 

1. Щелкните правой кнопкой мыши **ContosoExpenses.Data** проекта.
2. Выберите **управление пакетами NuGet**.
3. В **диспетчер пакетов NuGet** окно, нажмите кнопку **Обзор**. Поиск `Microsoft.Windows.Compatibility` пакет и установите его. 

    ![](images/wpf-modernize-tutorial/WindowsCompatibilityPack.png)

4. Теперь попробуйте еще раз скомпилировать проект, щелкнув правой кнопкой **ContosoExpenses.Data** проекта и выбрав **построения**.

Настоящее время процесс сборки завершится без ошибок.

## <a name="test-and-debug-the-migration"></a>Тестирование и отладка миграции

Теперь, когда проекты сборка успешно, вы готовы запустить и протестировать приложение, чтобы увидеть, если имеются ошибки среды выполнения.

1. Щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта и выберите **Назначить запускаемым проектом**.

2. Нажмите клавишу F5, чтобы запустить **ContosoExpenses.Core** проекта в отладчике. Вы увидите исключение следующего вида.

    ![Исключение, отображается в Visual Studio](images/wpf-modernize-tutorial/ExceptionNETCore3.png)

    Это исключение вызывается, поскольку при удалении содержимого из CSPROJ-файл в начале миграции, были удалены сведения о **действие при сборке** для файлов изображений. Эта проблема устранена следующие действия.

3. Остановите отладчик.

4. Щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта и выберите **выгрузить проект**. Щелкните правой кнопкой мыши проект и выберите **изменить ContosoExpenses.Core.csproj**.

5. Перед закрывающей скобкой **проекта** элемента, добавьте следующую запись:

    ```xml
    <ItemGroup>
      <Content Include="Images/*">
        <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
      </Content>
    </ItemGroup>
    ```

6. Щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта и выберите **перезагрузить проект**.

7. Чтобы назначить Contoso.ico в приложение, щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта и выберите **свойства**. На странице «открытый» нажмите кнопку раскрывающегося списка **значок** и выберите `Images\contoso.ico`.

    ![Значок Contoso в свойствах проекта](images/wpf-modernize-tutorial/ContosoIco.png)

8. Нажмите кнопку **Сохранить**.

9. Нажмите клавишу F5, чтобы запустить **ContosoExpenses.Core** проекта в отладчике. Убедитесь, что приложение теперь выполняется.

## <a name="next-steps"></a>Следующие шаги

На этом этапе в этом руководстве, вы успешно перенесли приложения Contoso для .NET Core 3. Теперь вы готовы для [часть 2: Добавьте элемент управления InkCanvas универсальной платформы Windows, с помощью XAML о-ва](modernize-wpf-tutorial-2.md).

> [!NOTE]
> При наличии высокого разрешения экрана, можно заметить, что приложение выглядит очень мало. Будет решить эту задачу на следующем шаге этого руководства.
