---
author: normesta
Description: "Процедура ручной упаковки классического приложения для Windows (например, Win32, WPF и Windows Forms) для Windows 10."
Search.Product: eADQiWindows 10XVcnh
title: "Упаковка приложения вручную (мост для классических приложений)"
ms.author: normesta
ms.date: 05/25/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: e8c2a803-9803-47c5-b117-73c4af52c5b6
ms.openlocfilehash: e8a09b6e362662b9bb207117d8a3fcc905da6ef4
ms.sourcegitcommit: ae93435e1f9c010a054f55ed7d6bd2f268223957
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/10/2017
---
# <a name="package-an-app-manually-desktop-bridge"></a>Упаковка приложения вручную (мост для классических приложений)

В этой статье описывается, как упаковать приложение без использования таких средств, как Visual Studio или Desktop App Converter (DAC).

<div style="float: left; padding: 10px">
    ![ручная процедура](images/desktop-to-uwp/manual-flow.png)
</div>

Чтобы упаковать приложение вручную, создайте файл манифеста пакета, а затем запустите программу командной строки для создания пакета приложения для Windows.

Используйте ручную упаковку, если вы устанавливаете приложение с помощью команды xcopy либо если вам известно, какие изменения вносит в систему ваш установщик приложения, и вы хотите более детально управлять этим процессом.

Если вы не знаете точно, какие изменения вносит в систему ваш установщик, или если предпочитаете использовать автоматические средства для создания пакета манифеста, попробуйте воспользоваться любым из [этих](desktop-to-uwp-root.md#convert) параметров.

## <a name="first-consider-how-youll-distribute-your-app"></a>Выбор способа распространения приложения
Если вы планируете опубликовать свое приложение в [Магазине Windows](https://www.microsoft.com/store/apps), начните с заполнения [этой формы](https://developer.microsoft.com/windows/projects/campaigns/desktop-bridge). После этого Майкрософт свяжется с вами, чтобы начать процесс публикации. В рамках этого процесса вы зарезервируете имя в Магазине и получите информацию, необходимую для упаковки приложения.

## <a name="create-a-package-manifest"></a>Создание пакета манифеста

Создайте файл, назовите его **appxmanifest.xml**, а затем добавьте в него XML-код, представленный ниже.

Это базовый шаблон, который содержит элементы и атрибуты, требуемые для вашего пакета. Мы добавим для них значения в следующем разделе.

```XML
<?xml version="1.0" encoding="utf-8"?>
<Package
    xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities">
  <Identity Name="" Version="" Publisher="" ProcessorArchitecture="" />
    <Properties>
       <DisplayName></DisplayName>
       <PublisherDisplayName></PublisherDisplayName>
             <Description></Description>
      <Logo></Logo>
    </Properties>
    <Resources>
      <Resource Language="" />
    </Resources>
      <Dependencies>
      <TargetDeviceFamily Name="Windows.Desktop" MinVersion="" MaxVersionTested="" />
      </Dependencies>
      <Capabilities>
        <rescap:Capability Name="runFullTrust"/>
      </Capabilities>
    <Applications>
      <Application Id="" Executable="" EntryPoint="Windows.FullTrustApplication">
        <uap:VisualElements DisplayName="" Description=""   Square150x150Logo=""
                   Square44x44Logo=""   BackgroundColor="" />
      </Application>
     </Applications>
  </Package>
```

## <a name="fill-in-the-package-level-elements-of-your-file"></a>Заполнение элементов уровня пакета для файла

Заполните этот шаблон сведениями о вашем пакете.

### <a name="identity-information"></a>Сведения об удостоверении

Вот пример элемента **Identity** с замещающим текстом для атрибутов. Вы можете задать атрибуту ``ProcessorArchitecture`` значение ``x64`` или ``x86``.

```XML
<Identity Name="MyCompany.MySuite.MyApp"
          Version="1.0.0.0"
          Publisher="CN=MyCompany, O=MyCompany, L=MyCity, S=MyState, C=MyCountry"
                ProcessorArchitecture="x64">
```
> [!NOTE]
> Если вы зарезервировали имя приложения в Магазине Windows, вы можете получить значения атрибутов Name (Имя) и Publisher (Издатель) с помощью информационной панели Центра разработки для Windows. Если планируется, что приложение будет загружено на другие системы в неопубликованном виде, вы можете указать для этих атрибутов собственные имена при условии, что имя издателя совпадает с именем, указанным в сертификате, который используется для подписания приложения.

### <a name="properties"></a>Свойства

Элемент [Properties](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-properties) имеет три обязательных дочерних элемента. Вот пример узла **Properties** с замещающим текстом для элементов. **DisplayName**— это имя приложения, которое вы резервируете в Магазине для отправляемых в этот Магазин приложений.

```XML
<Properties>
  <DisplayName>MyApp</DisplayName>
  <PublisherDisplayName>MyCompany</PublisherDisplayName>
  <Logo>images\icon.png</Logo>
</Properties>
```

### <a name="resources"></a>Ресурсы

Вот пример узла [Resources](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-resources).

```XML
<Resources>
  <Resource Language="en-us" />
</Resources>
```
### <a name="dependencies"></a>Зависимости

Для приложений, перенесенных из классических приложений, всегда устанавливайте атрибуту ``Name`` значение ``Windows.Desktop``.

```XML
<Dependencies>
<TargetDeviceFamily Name="Windows.Desktop" MinVersion="10.0.14316.0" MaxVersionTested="10.0.15063.0" />
</Dependencies>
```

### <a name="capabilities"></a>Возможности
Для приложений, перенесенных из классического приложения, необходимо добавить возможность ``runFullTrust``.

```XML
<Capabilities>
  <rescap:Capability Name="runFullTrust"/>
</Capabilities>
```
## <a name="fill-in-the-application-level-elements"></a>Заполните элементы уровня приложения

Заполните этот шаблон сведениями о вашем приложении.

### <a name="application-element"></a>Элемент приложения

Для приложений, перенесенных из классических приложений, атрибут ``EntryPoint`` элемента Application всегда имеет значение ``Windows.FullTrustApplication``.

```XML
<Applications>
  <Application Id="MyApp"     
        Executable="MyApp.exe" EntryPoint="Windows.FullTrustApplication">
   </Application>
</Applications>
```

### <a name="visual-elements"></a>Визуальные элементы

Вот пример узла [VisualElements](https://docs.microsoft.com/uwp/schemas/appxpackage/appxmanifestschema/element-visualelements).

```XML
<uap:VisualElements
    BackgroundColor="#464646"
    DisplayName="My App"
    Square150x150Logo="images\icon.png"
    Square44x44Logo="images\small_icon.png"
    Description="A useful description" />
```

## <a name="optional-add-target-based-unplated-assets"></a>Добавление специальных ресурсов без покрытия (необязательно)

Специальные ресурсы предназначены для значков и плиток, которые отображаются на панели задач Windows, в представлении задач, в окне, вызываемом сочетанием клавиш ALT+TAB, в Snap Assist и в правом нижнем углу плиток начального экрана. Подробнее об этом см. [здесь](https://docs.microsoft.com/windows/uwp/controls-and-patterns/tiles-and-notifications-app-assets#target-based-assets).

1. Получите правильные изображения 44x44 и скопируйте их в папку с изображениями (например, "Ресурсы").

2. Создайте копию каждого изображения 44x44 в той же папке и добавьте **.targetsize-44_altform-unplated** к имени файла. Вам потребуются две копии каждого значка, каждый из которых должен быть назван особым образом. Например, после завершения процесса папка ресурсов может содержать файлы **MYAPP_44x44.png** и **MYAPP_44x44.targetsize 44_altform unplated.png**.

   > [!NOTE]
   > В этом примере значок **MYAPP_44x44.png**— это значок, который вы укажете в атрибуте логотипа ``Square44x44Logo`` вашего пакета приложения для Windows.

3.  В пакете приложения для Windows задайте ``BackgroundColor`` для каждого значка, который должен быть прозрачным.

4.  Откройте командную строку, измените каталог на корневую папку пакета, а затем создайте файл priconfig.xml с помощью команды ``makepri createconfig /cf priconfig.xml /dq en-US``.

5.  Создайте файлы resources.pri с помощью команды ``makepri new /pr <PHYSICAL_PATH_TO_FOLDER> /cf <PHYSICAL_PATH_TO_FOLDER>\priconfig.xml``.

    Например, команда для вашего приложения может выглядеть так: ``makepri new /pr c:\MYAPP /cf c:\MYAPP\priconfig.xml``.

6.  Запакуйте приложение для Windows, руководствуясь инструкциями в следующем шаге, чтобы увидеть результат.

<span id="make-appx" />
## <a name="generate-a-windows-app-package"></a>Создание пакета приложений для Windows

Используйте **MakeAppx.exe**, чтобы создать пакет приложения для Windows для вашего проекта. Он входит в пакет Windows 10 SDK, и если у вас установлена Visual Studio, доступ к нему можно получить через командную строку разработчика для вашей версии Visual Studio.

См. раздел [Создание пакета приложения с помощью средства MakeAppx.exe](https://docs.microsoft.com/windows/uwp/packaging/create-app-package-with-makeappx-tool)

## <a name="run-the-packaged-app"></a>Запуск упакованного приложения

Чтобы запустить приложение для локальной проверки, не обязательно получать сертификат и подписывать его. Просто запустите этот командлет PowerShell:

```Add-AppxPackage –Register AppxManifest.xml```

Чтобы обновить файлы .exe или .dll приложения, замените существующие файлы в пакете новыми, повысьте номер версии в AppxManifest.xml, а затем выполните указанную выше команду повторно.

> [!NOTE]
> Упакованное приложение всегда выполняется от лица текущего пользователя, а диск, на который производится установка приложения, должен быть отформатирован в NTFS.

## <a name="next-steps"></a>Следующие шаги

**Пошаговое выполнение кода, поиск и устранение проблем**

См. [Запуск, отладка и тестирование упакованного классического приложения (мост для классических приложений)](desktop-to-uwp-debug.md)

**Подписание и распространение приложения**

См. [Распространение упакованного классического приложения (мост для классических приложений)](desktop-to-uwp-distribute.md)

**Поиск ответов на конкретные вопросы**

Наша команда следит за этими [тегами StackOverflow](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge).

**Оставьте отзыв об этой статье**

Используйте раздел комментариев ниже.
