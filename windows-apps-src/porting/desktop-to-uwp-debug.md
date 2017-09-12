---
author: normesta
Description: "Запустите неподписанное упакованное приложение и посмотрите, как оно выглядит. Затем установите точки останова и пошагово выполните код. Когда будете готовы протестировать приложение в рабочей среде, подпишите его, а затем установите."
Search.Product: eADQiWindows 10XVcnh
title: "Запуск, отладка и тестирование упакованного классического приложения (мост для классических приложений)"
ms.author: normesta
ms.date: 06/20/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: f45d8b14-02d1-42e1-98df-6c03ce397fd3
ms.openlocfilehash: c160fecc530a6366de48f4f2ecc24df2463c0469
ms.sourcegitcommit: 77bbd060f9253f2b03f0b9d74954c187bceb4a30
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/11/2017
---
# <a name="run-debug-and-test-a-packaged-desktop-app-desktop-bridge"></a>Запуск, отладка и тестирование упакованного классического приложения (мост для классических приложений)

Запустите неподписанное упакованное приложение и посмотрите, как оно выглядит. Затем установите точки останова и пошагово выполните код. Когда будете готовы протестировать приложение в рабочей среде, подпишите его, а затем установите. В данной статье приведено подробное описание выполнения каждого из этих действий.

<span id="run-app" />
## <a name="run-your-app"></a>Запуск приложения

Чтобы запустить приложение для локальной проверки, не обязательно получать сертификат и подписывать его.

Если ваш пакет создан с помощью проекта UWP в Visual Studio, просто задайте упаковывающий проект в качестве стартового проекта и нажмите сочетание клавиш CTRL+F5, чтобы запустить приложение.

Если вы использовали Desktop App Converter или упаковали приложение вручную, откройте командную строку Windows PowerShell и из вложенной папки **PacakgeFiles** в вашей папке выходных данных запустите этот командлет:

```
Add-AppxPackage –Register AppxManifest.xml
```
Чтобы запустить приложение, найдите его в меню "Пуск" Windows.

![Упакованное приложение в меню "Пуск"](images/desktop-to-uwp/converted-app-installed.png)

> [!NOTE]
> Упакованное приложение всегда выполняется от лица текущего пользователя, а диск, на который производится установка приложения, должен быть отформатирован в NTFS.

## <a name="debug-your-app"></a>Отладка приложения

Выбирайте свой пакет в диалоговом окне каждый раз при отладке приложения или установке расширения и отладке приложения, при этом не требуется выбирать пакет при каждом запуске сеанса.

### <a name="debug-your-app-by-selecting-the-package"></a>Отладка приложения путем выбора пакета

Этот вариант быстро настраивается, но при каждом запуске сеанса отладки необходимо выполнить дополнительное действие.


1. Обязательно запустите свое упакованное приложение хотя бы один раз, чтобы оно полностью установилось на локальном компьютере.

   См. раздел [Запуск приложения](#run-app) выше.

2. Запустите Visual Studio.

   Если вы хотите выполнить отладку приложения с более высоким уровнем разрешений, запустите Visual Studio с помощью команды **Запуск от имени администратора**.

3. В Visual Studio выберите **Отладка**->**Другие цели отладки**->**Отладка установленного пакета приложения**.

4. В списке **Установленные пакеты приложения** выберите соответствующий пакет приложения, а затем нажмите кнопку **Вложить**.


### <a name="debug-your-app-without-having-to-select-the-package"></a>Отладка приложения без необходимости выбора пакета

Настройка этого варианта занимает больше времени, но вам не придется выбирать установленный пакет при каждом запуске вашего приложения. Для использования этого подхода необходимо установить [Visual Studio 2017](https://www.visualstudio.com/vs/whatsnew/).

1. Сначала установите проект [Desktop Bridge Debugging Project](http://go.microsoft.com/fwlink/?LinkId=797871).

2. Запустите Visual Studio и откройте проект классического приложения.

6. Добавьте проект **Desktop Bridge Debugging Project** в ваше решение.

   Шаблон проекта можно найти в группе установленных шаблонов **Другие типы проектов**.

    ![alt](images/desktop-to-uwp/debug-2.png)

    Проект **Desktop Bridge Debugging Project** появится в вашем решении.

    ![alt](images/desktop-to-uwp/debug-3.png)

7. Откройте страницу свойств для проекта **Desktop Bridge Debugging Project**.

8. Укажите в поле **Макет пакета** расположение вашего файла манифеста (AppxManifest.xml) и выберите исполняемый файл приложения из раскрывающегося списка **Start Up Tile** (Плитка запуска).

     ![alt](images/desktop-to-uwp/debug-4.png)

8. Откройте файл AppXPackageFileList.xml в редакторе кода.

9. Раскомментируйте блок XML-кода и добавьте значения для следующих элементов:

   **MyProjectOutputPath**: относительный путь к папке отладки классического приложения.

   **LayoutFile**: исполняемый файл, который находится в папке отладки классического приложения.

   **PackagePath**: полное имя исполняемого файла (классического приложения), который был скопирован в папку пакета приложения для Windows во время процесса преобразования.

    Пример:

    ```XML
  <?xml version="1.0" encoding="utf-8"?>
  <Project ToolsVersion="14.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <PropertyGroup>
     <MyProjectOutputPath>..\MyDesktopApp\bin\Debug</MyProjectOutputPath>
    </PropertyGroup>
    <ItemGroup>
      <LayoutFile Include="$(MyProjectOutputPath)\MyDesktopApp.exe">
        <PackagePath>$(PackageLayout)\MyDesktopApp.exe</PackagePath>
      </LayoutFile>
    </ItemGroup>
  </Project>
    ```

  Если в вашем решении приложение использует файлы DLL, созданные из других проектов, и вы хотите выполнить шаг с заходом в код, содержащийся в этих файлах, добавьте элемент **LayoutFile** для каждого из этих DLL.

  ```XML
  ...
      <LayoutFile Include="$(MyProjectOutputPath)\MyDesktopApp.Models.dll">
      <PackagePath>$(PackageLayout)\MyDesktopApp.Models.dll</PackagePath>
      </LayoutFile>
  ...
  ```

10. Установите проект упаковки в качестве запускаемого проекта.  

    ![alt](images/desktop-to-uwp/debug-5.png)

11. Установите точки останова в коде классического приложения, а затем запустите отладчик.

  ![Кнопка отладки](images/desktop-to-uwp/debugger-button.png)

  Visual Studio копирует исполняемые файлы и библиотеки DLL, указанные в XML-файле для вашего пакета приложения для Windows, и затем запускает отладчик.

#### <a name="handle-multiple-build-configurations"></a>Обработка нескольких конфигураций сборки

Если определено несколько конфигураций сборки (например, выпуск (Release) и отладка (Debug)), можно изменить файл AppXPackageFileList.xml таким образом, чтобы копировать только те файлы, которые соответствуют конфигурации сборки, выбранной в Visual Studio при запуске отладчика.

Вот пример:

```XML
<PropertyGroup>
    <MyProjectOutputPath Condition="$(Configuration) == 'Debug'">..\MyDesktopApp\bin\Debug</MyProjectOutputPath>
    <MyProjectOutputPath Condition="$(Configuration) == 'Release'"> ..\MyDesktopApp\bin\Release</MyProjectOutputPath>
</PropertyGroup>
```

#### <a name="debug-uwp-enhancements-to-your-app"></a>Отладка дополнительных возможностей UWP для вашего приложения

Возможно, вам захочется улучшить приложение с помощью современных возможностей, таких как живые плитки. Для этого можно использовать условную компиляцию, чтобы включить пути кода с определенными конфигурациями сборок.

1. Сначала в Visual Studio определите конфигурацию сборки и присвойте ей имя, например "DesktopUWP".

2. Укажите это имя в свойствах проекта на вкладке **Сборка** в поле **Символы условной компиляции**.

     ![alt](images/desktop-to-uwp/debug-8.png)

3. Добавьте блоки условного кода. Этот код компилируется только для конфигурации сборки **DesktopUWP**.

    ```csharp
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

### <a name="debug-the-entire-app-lifecycle"></a>Отладка полного жизненного цикла приложения

В некоторых случаях вам может потребоваться более точное управление процессом отладки, включая возможность отладки приложения до его запуска.

Воспользуйтесь [PLMDebug](https://msdn.microsoft.com/library/windows/hardware/jj680085(v=vs.85).aspx), чтобы получить полный контроль над жизненным циклом приложения, включая приостановку, возобновление и завершение работы.

[PLMDebug](https://msdn.microsoft.com/library/windows/hardware/jj680085(v=vs.85).aspx) входит в состав Windows SDK.


### <a name="modify-your-app-in-between-debug-sessions"></a>Изменение приложения между сеансами отладки

Чтобы внести в приложение изменения для устранения неполадок, распакуйте его с помощью средства MakeAppx. См. раздел [Запуск средства MakeAppx](desktop-to-uwp-manual-conversion.md#make-appx).

## <a name="test-your-app"></a>Тестирование приложения

Чтобы протестировать приложение в реалистичной среде во время подготовки к распространению, лучше всего подписать приложение и затем установить его.

Если приложение упаковано с помощью Visual Studio, можно запустить сценарий, чтобы подписать приложение, а затем установить его. См. раздел [Загрузка пакета неопубликованного приложения](../packaging/packaging-uwp-apps.md#sideload-your-app-package).

Если приложение упаковано с помощью Desktop App Converter, используйте параметр ``sign``, чтобы автоматически подписать приложение с помощью сгенерированного сертификата. Вам необходимо установить этот сертификат, а затем установить приложение. См. раздел [Запуск упакованного приложения](desktop-to-uwp-run-desktop-app-converter.md#run-app).   

Кроме того, можно подписать приложение вручную. Вот как это сделать:

1. Создайте сертификат. См. статью [Создание сертификата](../packaging/create-certificate-package-signing.md).

2. Установите этот сертификат на свой компьютер в хранилище сертификатов **Доверенные корневые** или **Доверенные лица**.

3. Подпишите приложение с помощью этого сертификата; см. статью [Подписание пакета приложения с помощью SignTool](../packaging/sign-app-package-using-signtool.md).

  > [!IMPORTANT]
  > Убедитесь, что имя издателя на сертификате совпадает с именем издателя вашего приложения.

### <a name="related-sample"></a>Связанный пример

[SigningCerts](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/SigningCerts)


### <a name="test-your-app-for-windows-10-s"></a>Тестирование приложения на Windows 10 S

Прежде чем публиковать приложение, убедитесь, что оно сможет корректно работать на устройствах под управлением Windows 10 S. На самом деле, если вы планируете опубликовать приложение в Магазине Windows, это надо сделать обязательно, поскольку таковы требования Магазина. Приложения, которые не могут правильно работать на устройствах под управлением Windows 10 S, не пройдут сертификацию. 

См. статью [Тестирование приложения для Windows на Windows 10 S](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-test-windows-s).

### <a name="run-another-process-inside-the-full-trust-container"></a>Запуск другого процесса в контейнере с полным доверием

Вы можете вызывать пользовательские процессы внутри контейнера указанного пакета приложения. Это бывает полезно при тестировании,— например, если у вас пользовательское окружение теста и вам требуется протестировать выходные данные приложения. Сделать это можно с помощью командлета ```Invoke-CommandInDesktopPackage``` PowerShell:

```CMD
Invoke-CommandInDesktopPackage [-PackageFamilyName] <string> [-AppId] <string> [-Command] <string> [[-Args]
    <string>]  [<CommonParameters>]
```

## <a name="next-steps"></a>Следующие шаги

**Поиск ответов на конкретные вопросы**

Наша команда следит за этими [тегами StackOverflow](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge).

**Оставьте отзыв об этой статье**

Используйте раздел комментариев ниже.
