---
author: normesta
Description: "В этом руководстве объясняется, как настроить решение Visual Studio для изменения, отладки и упаковки классических приложений для моста для классических приложений."
Search.Product: eADQiWindows 10XVcnh
title: "Упаковка приложения с помощью Visual Studio (мост для классических приложений)"
ms.author: normesta
ms.date: 07/20/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 807a99a7-d285-46e7-af6a-7214da908907
ms.openlocfilehash: d8919448b965f18ff7f8fdaeda325889e495ef85
ms.sourcegitcommit: f6dd9568eafa10ee5cb2b849c0d82d84a1c5fb93
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/02/2017
---
# <a name="package-an-app-by-using-visual-studio-desktop-bridge"></a>Упаковка приложения с помощью Visual Studio (мост для классических приложений)

Visual Studio можно использовать для создания пакета для классического приложения. Затем вы можете опубликовать этот пакет в Магазине Windows или установить его на один или несколько компьютеров без публикации.

В этом руководстве показано, как настроить решение и затем создать пакет для классического приложения.

## <a name="first-consider-how-youll-distribute-your-app"></a>Выбор способа распространения приложения

Если вы планируете опубликовать свое приложение в [Магазине Windows](https://www.microsoft.com/store/apps), начните с заполнения [этой формы](https://developer.microsoft.com/windows/projects/campaigns/desktop-bridge). После этого Майкрософт свяжется с вами, чтобы начать процесс публикации. В рамках этого процесса вы зарезервируете имя в Магазине и получите информацию, необходимую для упаковки приложения.

## <a name="add-a-packaging-project-to-your-solution"></a>Добавление проекта упаковки в решение

1. В Visual Studio откройте решение, содержащее проект вашего классического приложения.

2. Добавьте проект JavaScript **Пустое приложение (универсальная платформа Windows)** в свое решение.

   Вам не потребуется добавлять в него какой-либо код. Он используется только для создания пакета. Мы будем называть этот проект «проектом упаковки».

   ![проект javascript UWP](images/desktop-to-uwp/javascript-uwp-project.png)

   >[!IMPORTANT]
   >Как правило, следует использовать версию JavaScript этого проекта.  В версиях C#, VB.NET и C++ есть ряд проблем, но если вы хотите использовать их, предварительно просмотрите руководство [Известные проблемы](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-known-issues#known-issues-anchor).

## <a name="add-the-desktop-application-binaries-to-the-packaging-project"></a>Добавление двоичных файлов классического приложения в проект упаковки

Добавьте двоичные файлы прямо в проект упаковки.

1. В **Обозревателе решений** разверните папку проекта упаковки, создайте вложенную папку и задайте ей любое имя (например, **win32**).

2. Щелкните правой кнопкой мыши по вложенной папке и выберите **Добавить существующий элемент**.

3. В диалоговом окне **Добавить существующий элемент** найдите и добавьте файлы из папки выходных данных вашего классического приложения. Это включает в себя не только исполняемые файлы, но и все файлы DLL и CONFIG, находящиеся в этой папке.

   ![Пример исполняемого файла](images/desktop-to-uwp/cpp-exe-reference.png)

   При каждом внесении изменений в проект классического приложения вам потребуется копировать новую версию этих файлов в проект упаковки. Это можно автоматизировать, добавив событие после построения в файл проекта упаковки. Вот пример:

   ```XML
   <Target Name="PostBuildEvent">
     <Copy SourceFiles="..\MyWindowsFormsApplication\bin\Debug\MyWindowsFormsApplication.exe"
       DestinationFolder="win32" />
     <Copy SourceFiles="..\MyWindowsFormsApplication\bin\Debug\MyWindowsFormsApplication.exe.config"
       DestinationFolder="win32" />
     <Copy SourceFiles="..\MyWindowsFormsApplication\bin\Debug\MyWindowsFormsApplication.pdb"
       DestinationFolder="win32" />
     <Copy SourceFiles="..\MyWindowsFormsApplication\bin\Debug\MyBusinessLogicLibrary.dll"
       DestinationFolder="win32" />
     <Copy SourceFiles="..\MyWindowsFormsApplication\bin\Debug\MyBusinessLogicLibrary.pdb"
       DestinationFolder="win32" />
   </Target>
   ```

## <a name="modify-the-package-manifest"></a>Изменение манифеста пакета

Проект упаковки содержит файл, описывающий параметры пакета. По умолчанию этот файл описывает приложение UWP, поэтому вам придется изменить его таким образом, чтобы система понимала, что пакет включает в себя классическое приложение, которое работает в режиме полного доверия.  

1. В **Обозревателе решений** разверните проект упаковки, щелкните правой кнопкой мыши по файлу **package.appxmanifest** и затем выберите **Перейти к коду**.

   ![Пример проекта dotnet](images/desktop-to-uwp/reference-dotnet-project.png)

2. Добавьте это пространство имен в верхнюю часть файла и внесите префикс пространства имен в список ``IgnorableNamespaces``.

   ```XML
   xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
   ```
   Когда все будет готово, ваши объявления пространства имен будут выглядеть примерно следующим образом:

   ```XML
   <Package
     xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
     xmlns:mp="http://schemas.microsoft.com/appx/2014/phone/manifest"
     xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
     xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
     IgnorableNamespaces="uap mp rescap">
   ```

3. Найдите элемент ``TargetDeviceFamily`` и задайте атрибуту ``Name`` значение **Windows.Desktop**, атрибуту ``MinVersion`` — минимальную версию проекта упаковки, а атрибуту ``MaxVersionTested`` — целевую версию проекта упаковки.

   ```XML
   <TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.10586.0" MaxVersionTested="10.0.15063.0" />
   ```

   Минимальную и целевую версию можно найти на страницах свойств проекта упаковки.

   ![Параметры минимальной и целевой версии](images/desktop-to-uwp/min-target-version-settings.png)


4. Удалите атрибут ``StartPage`` из элемента ``Application``. Затем добавьте атрибуты ``Executable`` и ``EntryPoint``.

   Элемент ``Application`` будет выглядеть так:

   ```XML
   <Application Id="App"  Executable=" " EntryPoint=" ">
   ```

5. Задайте атрибуту ``Executable`` имя исполняемого файла вашего классического приложения. После этого задайте атрибуту ``EntryPoint`` значение **Windows.FullTrustApplication**.

   Элемент ``Application`` будет выглядеть примерно так:

   ```XML
   <Application Id="App"  Executable="win32\MyWindowsFormsApplication.exe" EntryPoint="Windows.FullTrustApplication">
   ```
6. Добавьте возможность ``runFullTrust`` в элемент ``Capabilities``.

   ```XML
     <rescap:Capability Name="runFullTrust"/>
   ```
   Под этим объявлением может появиться синяя волнистая линия, но ее можно смело игнорировать.

   >[!IMPORTANT]
   Если вы создаете пакет для классического приложения на C++, необходимо внести некоторые дополнительные изменения в файл манифеста, чтобы вы могли разворачивать среды выполнения Visual C++ вместе со своим приложением. См. раздел [Использование сред выполнения Visual C++ в проекте с мостом для классических приложений](https://blogs.msdn.microsoft.com/vcblog/2016/07/07/using-visual-c-runtime-in-centennial-project/).

7. Соберите проект упаковки, чтобы убедиться, что ошибок нет.

8. Если вы хотите проверить свой пакет, см. раздел [Запуск, отладка и тестирование упакованного классического приложения (мост для классических приложений)](desktop-to-uwp-debug.md).

   Затем вернитесь к этому руководству и обратитесь к следующему разделу, чтобы создать свой пакет.

## <a name="generate-a-package"></a>Создание пакета

Чтобы создать пакет для приложения, выполните рекомендации, описанные в этом разделе: [Упаковка приложений UWP](..\packaging\packaging-uwp-apps.md).

По достижении экрана **Выбор и настройка пакетов** определите, какого рода двоичные файлы вы включаете в пакет, перед тем как устанавливать какие-либо флажки.

* Если вы [расширили](desktop-to-uwp-extend.md) классическое приложение, добавив проект универсальной платформы Windows на C#, C++ или VB.NET в свое решение, установите флажки **x86** и **x64**.  

* В противном случае установите флажок **Нейтральные**.

>[!NOTE]
Причина, по которой необходимо точно выбрать каждую поддерживаемую платформу, заключается в том, что расширенное вами решение содержит два типа двоичных файлов: один для проекта UWP и один для проекта классического приложения. Поскольку это различные типы двоичных файлов, .NET Native требуется явным образом создать собственные двоичные файлы для каждой платформы.

Если возникнут ошибки при попытке создания пакета, см. раздел [Известные проблемы](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-known-issues#known-issues-anchor), и если вашей проблемы нет в этом списке, расскажите нам о ней [здесь](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge).

## <a name="next-steps"></a>Следующие шаги

**Запуск приложения / поиск и устранение проблем**

См. [Запуск, отладка и тестирование упакованного классического приложения (мост для классических приложений)](desktop-to-uwp-debug.md)

**Улучшение классического приложения путем добавления API-интерфейсов UWP**

См. [Улучшение классического приложения для Windows 10](desktop-to-uwp-enhance.md)

**Расширение классического приложения путем добавления компонентов UWP**

См. [Расширение классического приложения с помощью современных компонентов UWP](desktop-to-uwp-extend.md)

**Распространение вашего приложения**

См. [Распространение упакованного классического приложения (мост для классических приложений)](desktop-to-uwp-distribute.md)

**Поиск ответов на конкретные вопросы**

Наша команда следит за этими [тегами StackOverflow](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge).

**Оставьте отзыв об этой статье**

Используйте раздел комментариев ниже.
