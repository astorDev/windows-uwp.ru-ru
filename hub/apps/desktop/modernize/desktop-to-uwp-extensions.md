---
Description: Можно использовать расширения для интеграции вашего упакованного классического приложения с Windows 10 предопределенными способами.
title: Модернизация существующих классических приложений с помощью моста для классических приложений
ms.date: 04/18/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 0a8cedac-172a-4efd-8b6b-67fd3667df34
ms.author: mcleans
author: mcleanbyron
ms.localizationpriority: medium
ms.openlocfilehash: d1f01774d5950dbb73cff2e5c38f16167b4b812b
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79209720"
---
# <a name="integrate-your-desktop-app-with-windows-10-and-uwp"></a>Интеграция классического приложения с Windows 10 и UWP

Если у классического приложения есть [идентификатор пакета](modernize-packaged-apps.md), можно применить расширения для интеграции приложения с Windows 10, используя предопределенные расширения в [манифесте пакета](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/schema-root).

Например, используйте расширение, чтобы добавить исключение брандмауэра, указать ваше приложение как приложение по умолчанию для определенного типа файлов или определить плитки начального экрана в качестве указателей на ваше приложение. Чтобы использовать расширение, просто добавьте разметку XML в файл манифеста пакета приложения. Никакой код не требуется.

В этой статье описаны эти расширения и задачи, которые можно выполнить с их помощью.

> [!NOTE]
> Для реализации возможностей, описанных в этой статье, требуется, чтобы классическое приложение получило [идентификатор пакета](modernize-packaged-apps.md) либо путем [упаковки классического приложения в пакет MSIX](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root), либо путем [предоставления идентификатора для приложения с помощью разреженного пакета](grant-identity-to-nonpackaged-apps.md).

## <a name="transition-users-to-your-app"></a>Переход пользователей на ваше приложение

Помогите пользователям перейти на ваше упакованное приложение:

* [Задайте ссылки на упакованное приложение для существующих плиток начального экрана и кнопок на панели задач.](#point)
* [Настройте открытие файлов в упакованном приложении вместо классического приложения.](#make)
* [Создайте связь между упакованным приложением и некоторыми типами файлов.](#associate)
* [Добавьте параметры в контекстное меню файлов определенного типа.](#add)
* [Настройте открытие определенных типов файлов напрямую с использованием URL-адреса.](#open)

<a id="point" />

### <a name="point-existing-start-tiles-and-taskbar-buttons-to-your-packaged-app"></a>Определение существующих плиток начального экрана и кнопок на панели задач в качестве указателей на упакованное приложение

Может оказаться, что пользователи закрепили ваше классическое приложение на панели задач или в меню "Пуск". Вы можете определить эти ярлыки в качестве указателей на новое упакованное приложение.

#### <a name="xml-namespace"></a>Пространство имен XML

`http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.desktopAppMigration">
    <DesktopAppMigration>
        <DesktopApp AumId="[your_app_aumid]" />
        <DesktopApp ShortcutPath="[path]" />
    </DesktopAppMigration>
</Extension>

```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-rescap3-desktopappmigration).

|Название | Описание |
|-------|-------------|
|Категория |Всегда ``windows.desktopAppMigration``.
|AumID |Идентификатор модели пользователя приложения для упакованного приложения. |
|ShortcutPath |Путь к LNK-файлам, которые запускают классическую версию вашего приложения. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:rescap3="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3"
  IgnorableNamespaces="rescap3">
  <Applications>
    <Application>
      <Extensions>
        <rescap3:Extension Category="windows.desktopAppMigration">
          <rescap3:DesktopAppMigration>
            <rescap3:DesktopApp AumId="[your_app_aumid]" />
            <rescap3:DesktopApp ShortcutPath="%USERPROFILE%\Desktop\[my_app].lnk" />
            <rescap3:DesktopApp ShortcutPath="%APPDATA%\Microsoft\Windows\Start Menu\Programs\[my_app].lnk" />
            <rescap3:DesktopApp ShortcutPath="%PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs\[my_app_folder]\[my_app].lnk"/>
         </rescap3:DesktopAppMigration>
        </rescap3:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

#### <a name="related-sample"></a>Связанный пример

[Программа WPF просмотра изображений с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<a id="make" />

### <a name="make-your-packaged-application-open-files-instead-of-your-desktop-app"></a>Настройка открытия файлов в упакованном приложении вместо классического приложения

Вы можете обеспечить, что по умолчанию пользователи будут работать с новым упакованным приложением (а не его классической версией) при открытии определенных типов файлов.

Для этого укажите [программный идентификатор (ProgID)](https://docs.microsoft.com/windows/desktop/shell/fa-progids) каждого приложения, из которого необходимо наследовать сопоставления файлов.

#### <a name="xml-namespaces"></a>Пространства имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`
* `http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[Name]">
         <MigrationProgIds>
            <MigrationProgId>"[ProgID]"</MigrationProgId>
        </MigrationProgIds>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fileTypeAssociation``.
|Название |Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов. |
|MigrationProgId |[Программный идентификатор (ProgID)](https://docs.microsoft.com/windows/desktop/shell/fa-progids), описывающий приложение, компонент и версию классического приложения, из которого необходимо наследовать сопоставления файлов.|

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  xmlns:rescap3="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3"
  IgnorableNamespaces="uap3, rescap3">
  <Applications>
    <Application>
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap3:FileTypeAssociation Name="myfiletypes">
            <rescap3:MigrationProgIds>
              <rescap3:MigrationProgId>Foo.Bar.1</rescap3:MigrationProgId>
              <rescap3:MigrationProgId>Foo.Bar.2</rescap3:MigrationProgId>
            </rescap3:MigrationProgIds>
          </uap3:FileTypeAssociation>
        </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

#### <a name="related-sample"></a>Связанный пример

[Программа WPF просмотра изображений с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<a id="associate" />

### <a name="associate-your-packaged-application-with-a-set-of-file-types"></a>Создание связи вашего упакованного приложения с набором типов файлов

Упакованные приложения можно связать с расширениями типов файлов. Если пользователь щелкнет файл правой кнопкой мыши и выберет команду **Открыть с помощью**, ваше приложение будет показано в появившемся списке вариантов.

#### <a name="xml-namespaces"></a>Пространства имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[Name]">
        <SupportedFileTypes>
            <FileType>"[file extension]"</FileType>
        </SupportedFileTypes>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fileTypeAssociation``.
|Название | Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов.   |
|FileType |Расширение файла, поддерживаемое вашим приложением. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  IgnorableNamespaces="uap, uap3">
  <Applications>
    <Application>
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap3:FileTypeAssociation Name="mediafiles">
            <uap:SupportedFileTypes>
            <uap:FileType>.avi</uap:FileType>
            </uap:SupportedFileTypes>
          </uap3:FileTypeAssociation>
        </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

#### <a name="related-sample"></a>Связанный пример

[Программа WPF просмотра изображений с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<a id="add" />

### <a name="add-options-to-the-context-menus-of-files-that-have-a-certain-file-type"></a>Добавление параметров в контекстное меню файлов определенного типа

В большинстве случаев пользователи дважды щелкают файлы, чтобы открыть их. Если пользователь щелкает файл правой кнопкой мыши, отображаются различные параметры.

Вы можете добавить параметры в это меню. Они предоставят пользователям другие способы взаимодействия с вашим файлом, например позволят выполнить печать, редактирование или предварительный просмотр.

#### <a name="xml-namespaces"></a>Пространства имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/2`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[Name]">
        <SupportedVerbs>
           <Verb Id="[ID]" Extended="[Extended]" Parameters="[parameters]">"[verb label]"</Verb>
        </SupportedVerbs>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория | Всегда ``windows.fileTypeAssociation``.
|Название |Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов. |
|Команда |Имя, которое отображается в контекстном меню проводника. Эта строка может быть локализована с помощью ```ms-resource```.|
|Id |Уникальный идентификатор команды. Если у вас приложение UWP, этот идентификатор передается в ваше приложение в составе аргументов события активации, чтобы приложение могло обработать выбор пользователя соответствующим образом. Если у вас упакованное приложение с полным доверием, оно вместо этого будет получать параметры (см. следующий пункт). |
|Параметры |Связанный с командой список параметров и значений аргументов. Если ваше приложение является упакованным приложением с полным доверием, эти параметры передаются в приложение в качестве аргументов событий при его активации. Вы можете настроить поведение вашего приложения при различных командах активации. Если переменная может содержать путь к файлу, заключите значение этого параметра в кавычки. Это позволит избежать проблем, которые возникают в случаях, когда путь содержит пробелы. Если ваше приложение является приложением UWP, передача параметров невозможна. Вместо этого приложение получит идентификатор Id (см. предыдущий пункт).|
|Расширенное |Указывает, что команда появляется, только если пользователь открыл контекстное меню, удерживая клавишу **SHIFT** перед нажатием правой кнопки мыши. Этот атрибут необязателен и при отсутствии по умолчанию имеет значение **False** (всегда отображать команду). Это поведение задается отдельно для каждой команды (кроме команды «Открыть», для которой всегда используется значение **False**).|

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap2="http://schemas.microsoft.com/appx/manifest/uap/windows10/2"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"

  IgnorableNamespaces="uap, uap2, uap3">
  <Applications>
    <Application>
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap3:FileTypeAssociation Name="myfiletypes">
            <uap2:SupportedVerbs>
              <uap3:Verb Id="Edit" Parameters="/e &quot;%1&quot;">Edit</uap3:Verb>
              <uap3:Verb Id="Print" Extended="true" Parameters="/p &quot;%1&quot;">Print</uap3:Verb>
            </uap2:SupportedVerbs>
          </uap3:FileTypeAssociation>
        </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

#### <a name="related-sample"></a>Связанный пример

[Программа WPF просмотра изображений с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<a id="open" />

### <a name="open-certain-types-of-files-directly-by-using-a-url"></a>Открытие определенных типов файлов напрямую с использованием URL-адреса

Вы можете обеспечить, что по умолчанию пользователи будут работать с новым упакованным приложением (а не его классической версией) при открытии определенных типов файлов.

#### <a name="xml-namespaces"></a>Пространства имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[Name]" UseUrl="true" Parameters="%1">
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fileTypeAssociation``.
|Название |Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов. |
|UseUrl |Указывает, следует ли открывать файлы напрямую из целевого расположения URL-адреса. Если это значение не задано, попытки приложения открыть файл с использованием URL-адреса приведут к тому, что система сначала скачает этот файл локально. |
|Параметры | Дополнительные параметры. |
|FileType |Соответствующие расширения файлов. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  IgnorableNamespaces="uap, uap3">
  <Applications>
      <Application>
        <Extensions>
          <uap:Extension Category="windows.fileTypeAssociation">
            <uap3:FileTypeAssociation Name="myfiletypes" UseUrl="true" Parameters="%1">
              <uap:SupportedFileTypes>
                <uap:FileType>.txt</uap:FileType>
                <uap:FileType>.doc</uap:FileType>
              </uap:SupportedFileTypes>
            </uap3:FileTypeAssociation>
          </uap:Extension>
        </Extensions>
      </Application>
    </Applications>
</Package>
```

## <a name="perform-setup-tasks"></a>Выполнение задач установки

* [Создание исключения брандмауэра для вашего приложения](#rules)
* [Размещение DLL-файлов в любой папке пакета](#load-paths)

<a id="rules" />

### <a name="create-firewall-exception-for-your-app"></a>Создание исключения брандмауэра для вашего приложения

Если для вашего приложения требуется обмен информацией через порт, можно добавить приложение в список исключений брандмауэра.

#### <a name="xml-namespace"></a>Пространство имен XML

`http://schemas.microsoft.com/appx/manifest/desktop/windows10/2`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.firewallRules">
  <FirewallRules Executable="[executable file name]">
    <Rule
      Direction="[Direction]"
      IPProtocol="[Protocol]"
      LocalPortMin="[LocalPortMin]"
      LocalPortMax="LocalPortMax"
      RemotePortMin="RemotePortMin"
      RemotePortMax="RemotePortMax"
      Profile="[Profile]"/>
  </FirewallRules>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-desktop2-firewallrules).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.firewallRules``.|
|Исполняемый файл |Имя исполняемого файла, который необходимо добавить в список исключений брандмауэра. |
|Направление |Указывает направление правила подключения: входящее или исходящее. |
|IPProtocol |Протокол взаимодействия. |
|LocalPortMin |Наименьшее значение номера порта в диапазоне номеров локального порта. |
|LocalPortMax |Наибольшее значение номера порта в диапазоне номеров локального порта. |
|RemotePortMax |Наименьшее значение номера порта в диапазоне номеров удаленного порта. |
|RemotePortMax |Наибольшее значение номера порта в диапазоне номеров удаленного порта. |
|Профиль |Тип сети. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:desktop2="http://schemas.microsoft.com/appx/manifest/desktop/windows10/2"
  IgnorableNamespaces="desktop2">
  <Extensions>
    <desktop2:Extension Category="windows.firewallRules">
      <desktop2:FirewallRules Executable="Contoso.exe">
          <desktop2:Rule Direction="in" IPProtocol="TCP" Profile="all"/>
          <desktop2:Rule Direction="in" IPProtocol="UDP" LocalPortMin="1337" LocalPortMax="1338" Profile="domain"/>
          <desktop2:Rule Direction="in" IPProtocol="UDP" LocalPortMin="1337" LocalPortMax="1338" Profile="public"/>
          <desktop2:Rule Direction="out" IPProtocol="UDP" LocalPortMin="1339" LocalPortMax="1340" RemotePortMin="15"
                         RemotePortMax="19" Profile="domainAndPrivate"/>
          <desktop2:Rule Direction="out" IPProtocol="GRE" Profile="private"/>
      </desktop2:FirewallRules>
  </desktop2:Extension>
</Extensions>
</Package>
```

<a id="load-paths" />

### <a name="place-your-dll-files-into-any-folder-of-the-package"></a>Размещение DLL-файлов в любой папке пакета

Используйте расширение для идентификации этих папок. Так система сможет найти и загрузить файлы, которые вы там разместите. Представьте, что это расширение — замена переменной среды _%PATH%_ .

Если не использовать это расширение, система выполнит поиск графа зависимости пакетов для этого процесса, корневую папку пакета и системный каталог ( _%SystemRoot%\system32_) в указанном порядке. Дополнительные сведения см. в разделе [Порядок поиска приложений для Windows](https://docs.microsoft.com/windows/desktop/Dlls/dynamic-link-library-search-order).

Каждый пакет может содержать только одно из этих расширений. Это означает, что одно из них можно добавить в главный пакет, а затем добавить по одному к каждому из [необязательных пакетов и связанных наборов](/windows/msix/package/optional-packages).

#### <a name="xml-namespace"></a>Пространство имен XML

`http://schemas.microsoft.com/appx/manifest/uap/windows10/6`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

Объявите это расширение на уровне пакета манифеста вашего приложения.

```XML
<Extension Category="windows.loaderSearchPathOverride">
  <LoaderSearchPathOverride>
    <LoaderSearchPathEntry FolderPath="[path]"/>
  </LoaderSearchPathOverride>
</Extension>

```

|Название | Описание |
|-------|-------------|
|Категория |Всегда ``windows.loaderSearchPathOverride``.
|FolderPath | Путь к папке, содержащей DLL-файлы. Укажите путь относительно корневой папки пакета. Можно указать до пяти путей в одном расширении. Если нужно, чтобы система искала файлы в корневой папке пакета, используйте для одного из этих путей пустую строку. Не добавляйте дублирующиеся пути и убедитесь, что ваши пути не содержат косых черт и обратных косых черт в начале и в конце. <br><br> Система не может выполнять поиск вложенных папок, поэтому убедитесь, что каждая пака, содержащая DLL-файлы, которая должна быть загружена системой, включена в список явным образом.|

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/6"
  IgnorableNamespaces="uap6">
  ...
    <Extensions>
      <uap6:Extension Category="windows.loaderSearchPathOverride">
        <uap6:LoaderSearchPathOverride>
          <uap6:LoaderSearchPathEntry FolderPath=""/>
          <uap6:LoaderSearchPathEntry FolderPath="folder1/subfolder1"/>
          <uap6:LoaderSearchPathEntry FolderPath="folder2/subfolder2"/>
        </uap6:LoaderSearchPathOverride>
      </uap6:Extension>
    </Extensions>
...
</Package>
```

## <a name="integrate-with-file-explorer"></a>Интеграция с проводником

Помогите пользователям упорядочить файлы и работать с ними привычными способами:

* [Определите поведения приложения, когда пользователи выбирают и открывают несколько файлов одновременно.](#define)
* [Настройте отображение содержимого файла в эскизе в проводнике.](#show)
* [Настройте отображение содержимого файла в области предварительного просмотра в проводнике.](#preview)
* [Предоставьте пользователям возможность группировать файлы с помощью столбца "Вид" в проводнике.](#enable)
* [Обеспечьте доступность свойств файлов для поиска, индексирования, в диалоговых окнах свойств и области сведений.](#make-file-properties)
* [Укажите обработчик контекстного меню для типа файла.](#context-menu)
* [Настройте доступ к файлам из облачной службы в проводнике.](#cloud-files)

<a id="define" />

### <a name="define-how-your-application-behaves-when-users-select-and-open-multiple-files-at-the-same-time"></a>Определение поведения приложения, когда пользователи выбирают и открывают несколько файлов одновременно

Укажите поведение приложения, когда пользователь открывает несколько файлов одновременно.

#### <a name="xml-namespaces"></a>Пространства имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/2`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[Name]" MultiSelectModel="[SelectionModel]">
        <SupportedVerbs>
            <Verb Id="Edit" MultiSelectModel="[SelectionModel]">Edit</Verb>
        </SupportedVerbs>
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fileTypeAssociation``.
|Название |Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов. |
|MultiSelectModel |См. ниже. |
|FileType |Соответствующие расширения файлов. |

**MultiSelectModel**

Для упакованных классических приложений, как и для обычных классических приложений, есть три варианта.

* ``Player``: приложение активируется один раз. Все выбранные файлы передаются приложению в качестве параметров аргумента.
* ``Single``: приложение активируется один раз для первого выбранного файла. Другие файлы игнорируются.
* ``Document``: для каждого выбранного файла активируется новый отдельный экземпляр приложения.

 Можно установить разные настройки для разных типов файлов и действий. Например, вы можете решить открывать *документы* в режиме *Document*, а *изображения* — в режиме *Player*.

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap2="http://schemas.microsoft.com/appx/manifest/uap/windows10/2"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  IgnorableNamespaces="uap, uap2, uap3">
  <Applications>
    <Application>
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap3:FileTypeAssociation Name="myfiletypes" MultiSelectModel="Document">
            <uap2:SupportedVerbs>
              <uap3:Verb Id="Edit" MultiSelectModel="Player">Edit</uap3:Verb>
              <uap3:Verb Id="Preview" MultiSelectModel="Document">Preview</uap3:Verb>
            </uap2:SupportedVerbs>
            <uap:SupportedFileTypes>
              <uap:FileType>.txt</uap:FileType>
            </uap:SupportedFileTypes>
        </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

Если пользователь открывает 15 или меньше файлов, значение по умолчанию для атрибута **MultiSelectModel** — *Player*. В противном случае значением по умолчанию является *Document*. Приложения UWP всегда запускаются в режиме *Player*.

<a id="show" />

### <a name="show-file-contents-in-a-thumbnail-image-within-file-explorer"></a>Отображение содержимого файла в эскизе в проводнике

Добавьте для пользователей возможность просматривать эскиз содержимого файла, когда значок файла большого, среднего или очень большого размера.

#### <a name="xml-namespace"></a>Пространство имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/2`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`
* `http://schemas.microsoft.com/appx/manifest/desktop/windows10/2`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[Name]">
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
        <ThumbnailHandler
            Clsid  ="[Clsid  ]" />
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fileTypeAssociation``.
|Название |Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов. |
|FileType |Соответствующие расширения файлов. |
|Clsid   |Идентификатор класса для вашего приложения. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap2="http://schemas.microsoft.com/appx/manifest/uap/windows10/2"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  xmlns:desktop2="http://schemas.microsoft.com/appx/manifest/desktop/windows10/2"
  IgnorableNamespaces="uap, uap2, uap3, desktop2">
  <Applications>
    <Application>
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap3:FileTypeAssociation Name="myfiletypes">
            <uap2:SupportedFileTypes>
              <uap:FileType>.bar</uap:FileType>
            </uap2:SupportedFileTypes>
            <desktop2:ThumbnailHandler
              Clsid  ="20000000-0000-0000-0000-000000000001"  />
            </uap3:FileTypeAssociation>
         </uap::Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="preview" />

### <a name="show-file-contents-in-the-preview-pane-of-file-explorer"></a>Отображение содержимого файла в области предварительного просмотра в проводнике

Предоставьте пользователям возможность просматривать содержимое файла в области предварительного просмотра в проводнике.

#### <a name="xml-namespace"></a>Пространство имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/2`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`
* `http://schemas.microsoft.com/appx/manifest/desktop/windows10/2`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[Name]">
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
        <DesktopPreviewHandler Clsid  ="[Clsid  ]" />
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fileTypeAssociation``.
|Название |Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов. |
|FileType |Соответствующие расширения файлов. |
|Clsid   |Идентификатор класса для вашего приложения. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap2="http://schemas.microsoft.com/appx/manifest/uap/windows10/2"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  xmlns:desktop2="http://schemas.microsoft.com/appx/manifest/desktop/windows10/2"
  IgnorableNamespaces="uap, uap2, uap3, desktop2">
  <Applications>
    <Application>
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap3:FileTypeAssociation Name="myfiletypes">
            <uap2SupportedFileTypes>
              <uap:FileType>.bar</uap:FileType>
                </uap2SupportedFileTypes>
              <desktop2:DesktopPreviewHandler Clsid ="20000000-0000-0000-0000-000000000001" />
           </uap3:FileTypeAssociation>
        </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="enable" />

### <a name="enable-users-to-group-files-by-using-the-kind-column-in-file-explorer"></a>Предоставление пользователям возможности группировать файлы с помощью столбца "Вид" в проводнике

Вы можете связать одно или несколько предопределенных значений для ваших типов файлов с полем **Вид**.

В проводнике пользователи смогут группировать эти файлы с помощью этого поля. Компоненты системы также используют это поле для различных целей, например индексации.

Дополнительные сведения о поле **Вид** и значениях, которые можно использовать для этого поля, см. в статье [Использование имен для свойства вида](https://docs.microsoft.com/windows/desktop/properties/building-property-handlers-user-friendly-kind-names).

#### <a name="xml-namespaces"></a>Пространства имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10`
* `http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[Name]">
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
        <KindMap>
            <Kind value="[KindValue]">
        </KindMap>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fileTypeAssociation``.
|Название |Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов. |
|FileType |Соответствующие расширения файлов. |
|value |Допустимое [значение для свойства вида](https://docs.microsoft.com/windows/desktop/properties/building-property-handlers-user-friendly-kind-names). |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:rescap="http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3"
  IgnorableNamespaces="uap, rescap">
  <Applications>
    <Application>
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
           <uap:FileTypeAssociation Name="mediafiles">
             <uap:SupportedFileTypes>
               <uap:FileType>.m4a</uap:FileType>
               <uap:FileType>.mta</uap:FileType>
             </uap:SupportedFileTypes>
             <rescap:KindMap>
               <rescap:Kind value="Item">
               <rescap:Kind value="Communications">
               <rescap:Kind value="Task">
             </rescap:KindMap>
          </uap:FileTypeAssociation>
      </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="make-file-properties" />

### <a name="make-file-properties-available-to-search-index-property-dialogs-and-the-details-pane"></a>Обеспечение доступности свойств файлов для поиска, индексирования, в диалоговых окнах свойств и области сведений

#### <a name="xml-namespace"></a>Пространство имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10`
* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`
* `http://schemas.microsoft.com/appx/manifest/desktop/windows10/2`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<uap:Extension Category="windows.fileTypeAssociation">
    <uap:FileTypeAssociation Name="[Name]">
        <SupportedFileTypes>
            <FileType>.bar</FileType>
        </SupportedFileTypes>
        <DesktopPropertyHandler Clsid ="[Clsid]"/>
    </uap:FileTypeAssociation>
</uap:Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fileTypeAssociation``.
|Название |Название сопоставления типов файлов. Это название можно использовать, чтобы упорядочить и сгруппировать типы файлов. Название должно состоять полностью из букв в нижнем регистре без пробелов. |
|FileType |Соответствующие расширения файлов. |
|Clsid  |Идентификатор класса для вашего приложения. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  xmlns:desktop2="http://schemas.microsoft.com/appx/manifest/desktop/windows10/2"
  IgnorableNamespaces="uap, uap3, desktop2">
  <Applications>
    <Application>
      <Extensions>
        <uap:Extension Category="windows.fileTypeAssociation">
          <uap3:FileTypeAssociation Name="myfiletypes">
            <uap:SupportedFileTypes>
              <uap:FileType>.bar</uap:FileType>
            </uap:SupportedFileTypes>
            <desktop2:DesktopPropertyHandler Clsid ="20000000-0000-0000-0000-000000000001"/>
          </uap3:FileTypeAssociation>
        </uap:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="context-menu" />

### <a name="specify-a-context-menu-handler-for-a-file-type"></a>Указание обработчика контекстного меню для типа файла

Если классическое приложение определяет [обработчик контекстного меню](https://docs.microsoft.com/windows/desktop/shell/context-menu-handlers), используйте это расширение для регистрации обработчика меню.

#### <a name="xml-namespaces"></a>Пространства имен XML

* `http://schemas.microsoft.com/appx/manifest/foundation/windows10`
* `http://schemas.microsoft.com/appx/manifest/desktop/windows10/4`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extensions>
    <com:Extension Category="windows.comServer">
        <com:ComServer>
            <com:SurrogateServer AppId="[AppID]" DisplayName="[DisplayName]">
                <com:Class Id="[Clsid]" Path="[Path]" ThreadingModel="[Model]"/>
            </com:SurrogateServer>
        </com:ComServer>
    </com:Extension>
    <desktop4:Extension Category="windows.fileExplorerContextMenus">
        <desktop4:FileExplorerContextMenus>
            <desktop4:ItemType Type="[Type]">
                <desktop4:Verb Id="[ID]" Clsid="[Clsid]" />
            </desktop4:ItemType>
        </desktop4:FileExplorerContextMenus>
    </desktop4:Extension>
</Extensions>
```

Вот ссылки на полный справочник по схеме: [com:ComServer](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-com-comserver) и [desktop4:FileExplorerContextMenus](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-desktop4-fileexplorercontextmenus).

#### <a name="instructions"></a>Инструкции

Чтобы зарегистрировать обработчик контекстного меню, выполните следующие инструкции:

1. В классическом приложении реализуйте [обработчик контекстного меню](https://docs.microsoft.com/windows/desktop/shell/context-menu-handlers) с помощью [IExplorerCommand](https://docs.microsoft.com/windows/desktop/api/shobjidl_core/nn-shobjidl_core-iexplorercommand) или интерфейса [IExplorerCommandState](https://docs.microsoft.com/windows/desktop/api/shobjidl_core/nn-shobjidl_core-iexplorercommandstate). Для примера просмотрите образец кода [ExplorerCommandVerb](https://github.com/microsoft/Windows-classic-samples/tree/master/Samples/Win7Samples/winui/shell/appshellintegration/ExplorerCommandVerb). Убедитесь, что для каждого объекта реализации определен глобальный уникальный идентификатор (GUID) класса. Например, следующий код определяет идентификатор класса для реализации [IExplorerCommand](https://docs.microsoft.com/windows/desktop/api/shobjidl_core/nn-shobjidl_core-iexplorercommand).

    ```cpp
    class __declspec(uuid("d0c8bceb-28eb-49ae-bc68-454ae84d6264")) CExplorerCommandVerb;
    ```

2. В манифесте пакета укажите расширение приложения [com:ComServer](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-com-comserver), которое регистрирует суррогатный COM-сервер с использованием идентификатора класса для реализации обработчика контекстного меню.

    ```xml
    <com:Extension Category="windows.comServer">
        <com:ComServer>
            <com:SurrogateServer AppId="d0c8bceb-28eb-49ae-bc68-454ae84d6264" DisplayName="ContosoHandler">
                <com:Class Id="d0c8bceb-28eb-49ae-bc68-454ae84d6264" Path="ExplorerCommandVerb.dll" ThreadingModel="STA"/>
            </com:SurrogateServer>
        </com:ComServer>
    </com:Extension>
    ```

2. В манифесте пакета укажите расширение приложения [desktop4:FileExplorerContextMenus](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-desktop4-fileexplorercontextmenus), которое регистрирует реализацию обработчика контекстного меню.

    ```xml
    <desktop4:Extension Category="windows.fileExplorerContextMenus">
        <desktop4:FileExplorerContextMenus>
            <desktop4:ItemType Type=".rar">
                <desktop4:Verb Id="Command1" Clsid="d0c8bceb-28eb-49ae-bc68-454ae84d6264" />
            </desktop4:ItemType>
        </desktop4:FileExplorerContextMenus>
    </desktop4:Extension>
    ```

#### <a name="example"></a>Пример

```XML
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:desktop4="http://schemas.microsoft.com/appx/manifest/desktop/windows10/4"
  IgnorableNamespaces="desktop4">
  <Applications>
    <Application>
      <Extensions>
        <com:Extension Category="windows.comServer">
          <com:ComServer>
            <com:SurrogateServer AppId="d0c8bceb-28eb-49ae-bc68-454ae84d6264" DisplayName="ContosoHandler"">
              <com:Class Id="Id="d0c8bceb-28eb-49ae-bc68-454ae84d6264" Path="ExplorerCommandVerb.dll" ThreadingModel="STA"/>
            </com:SurrogateServer>
          </com:ComServer>
        </com:Extension>
        <desktop4:Extension Category="windows.fileExplorerContextMenus">
          <desktop4:FileExplorerContextMenus>
            <desktop4:ItemType Type=".contoso">
              <desktop4:Verb Id="Command1" Clsid="d0c8bceb-28eb-49ae-bc68-454ae84d6264" />
            </desktop4:ItemType>
          </desktop4:FileExplorerContextMenus>
        </desktop4:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="cloud-files" />

### <a name="make-files-from-your-cloud-service-appear-in-file-explorer"></a>Настройка доступа к файлам из облачной службы в проводнике

Зарегистрируйте обработчики, которые реализуются в приложении. Можно также добавить параметры контекстного меню, которые отображаются, когда пользователи щелкают правой кнопкой мыши ваши облачные файлы в проводнике.

#### <a name="xml-namespace"></a>Пространство имен XML

* `http://schemas.microsoft.com/appx/manifest/desktop/windows10`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.cloudfiles" >
    <CloudFiles IconResource="[Icon]">
        <CustomStateHandler Clsid ="[Clsid]"/>
        <ThumbnailProviderHandler Clsid ="[Clsid]"/>
        <ExtendedPropertyhandler Clsid ="[Clsid]"/>
        <CloudFilesContextMenus>
            <Verb Id ="Command3" Clsid= "[GUID]">[Verb Label]</Verb>
        </CloudFilesContextMenus>
    </CloudFiles>
</Extension>

```

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.cloudfiles``.
|iconResource |Значок, который представляет службу поставщика облачных файлов. Этот значок отображается в области навигации проводника.  Пользователи выбирают этот значок, чтобы показать файлы из вашей облачной службы. |
|CustomStateHandler Clsid |Идентификатор класса приложения для реализации CustomStateHandler. Система использует этот идентификатор класса для запросов пользовательских состояний и столбцов для облачных файлов. |
|ThumbnailProviderHandler Clsid |Идентификатор класса приложения для реализации ThumbnailProviderHandler. Система использует этот идентификатор класса для запросов эскизов для облачных файлов. |
|ExtendedPropertyHandler Clsid |Идентификатор класса приложения для реализации ExtendedPropertyHandler.  Система использует этот идентификатор класса для запросов расширенных свойств для облачного файла. |
|Команда |Имя, отображаемое в контекстном меню проводника, для файлов, предоставляемых облачной службой. |
|Id |Уникальный идентификатор команды. |

#### <a name="example"></a>Пример

```XML
<Package
    xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
    IgnorableNamespaces="desktop">
  <Applications>
    <Application>
      <Extensions>
        <Extension Category="windows.cloudfiles" >
            <CloudFiles IconResource="images\Wide310x150Logo.png">
                <CustomStateHandler Clsid ="20000000-0000-0000-0000-000000000001"/>
                <ThumbnailProviderHandler Clsid ="20000000-0000-0000-0000-000000000001"/>
                <ExtendedPropertyhandler Clsid ="20000000-0000-0000-0000-000000000001"/>
                <desktop:CloudFilesContextMenus>
                    <desktop:Verb Id ="keep" Clsid=
                       "20000000-0000-0000-0000-000000000001">
                       Always keep on this device</desktop:Verb>
                </desktop:CloudFilesContextMenus>
            </CloudFiles>
          </Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="start" />

## <a name="start-your-application-in-different-ways"></a>Различные способы запуска приложения

* [Запуск приложения с использованием протокола](#protocol)
* [Запуск приложения с использованием псевдонима](#alias)
* [Запуск исполняемого файла при входе пользователей в Windows](#executable)
* [Предоставление пользователям возможности запускать приложение при подключении устройства к ПК](#autoplay)
* [Автоматический перезапуск после получения обновления из Microsoft Store](#updates)

<a id="protocol" />

### <a name="start-your-application-by-using-a-protocol"></a>Запуск приложения с использованием протокола

Сопоставления протоколов позволяют другим программам и компонентам системы взаимодействовать с упакованным приложением. Когда ваше упакованное приложение запускается с использованием протокола, вы можете определить специальные параметры, которые передаются аргументам события активации, что позволит приложению реагировать соответствующим образом. Параметры поддерживаются только для упакованных приложений с полным доверием. Приложения UWP не могут использовать параметры.

#### <a name="xml-namespace"></a>Пространство имен XML

`http://schemas.microsoft.com/appx/manifest/uap/windows10/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension
    Category="windows.protocol">
  <Protocol
      Name="[Protocol name]"
      Parameters="[Parameters]" />
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-protocol).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.protocol``.
|Название |Имя протокола. |
|Параметры |Список параметров и значений, передаваемых в приложение в качестве аргументов события при его активации. Если переменная может содержать путь к файлу, заключите значение этого параметра в кавычки. Это позволит избежать проблем, которые возникают в случаях, когда путь содержит пробелы. |

### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
  IgnorableNamespaces="uap3, desktop">
  <Applications>
    <Application>
      <Extensions>
        <uap3:Extension
          Category="windows.protocol">
          <uap3:Protocol
            Name="myapp-cmd"
            Parameters="/p &quot;%1&quot;" />
        </uap3:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="alias" />

### <a name="start-your-application-by-using-an-alias"></a>Запуск приложения с использованием псевдонима

Пользователи и различные процессы могут использовать для запуска приложения псевдоним и не указывать полный путь к этому приложению. Вы можете указать имя этого псевдонима.

#### <a name="xml-namespaces"></a>Пространства имен XML

* `http://schemas.microsoft.com/appx/manifest/uap/windows10/3`
* `http://schemas.microsoft.com/appx/manifest/desktop/windows10`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension
    Category="windows.appExecutionAlias"
    Executable="[ExecutableName]"
    EntryPoint="Windows.FullTrustApplication">
    <AppExecutionAlias>
        <desktop:ExecutionAlias Alias="[AliasName]" />
    </AppExecutionAlias>
</Extension>
```

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.appExecutionAlias``.
|Исполняемый файл |Относительный путь к исполняемому файлу для запуска приложения при применении псевдонима. |
|Псевдоним |Короткое имя для вашего приложения. Оно всегда должно завершаться расширением .exe. Для каждого приложения в пакете можно указать только один псевдоним выполнения приложения. Если несколько приложений зарегистрированы с одним и тем же псевдонимом, система будет активировать псевдоним, который был зарегистрирован последним, поэтому следует выбирать уникальный псевдоним, который не будет переопределен другими приложениями.
|

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  IgnorableNamespaces="uap3">
  <Applications>
    <Application>
      <Extensions>
         <uap3:Extension
                Category="windows.appExecutionAlias"
                Executable="exes\launcher.exe"
                EntryPoint="Windows.FullTrustApplication">
            <uap3:AppExecutionAlias>
                <desktop:ExecutionAlias Alias="Contoso.exe" />
            </uap3:AppExecutionAlias>
        </uap3:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

<a id="executable" />

### <a name="start-an-executable-file-when-users-log-into-windows"></a>Запуск исполняемого файла при входе пользователей в Windows

Задачи запуска позволяют приложению автоматически запускать исполняемый файл при каждом входе пользователя в систему.

> [!NOTE]
> Чтобы зарегистрировать эту задачу запуска, пользователь должен открыть ваше приложение хотя бы один раз.

Для приложения можно объявить несколько задач запуска. Каждая задача запускается независимо друг от друга. Все задачи запуска отображаются в диспетчере задач во вкладке **Запуск** со значком вашего приложения и именем, указанным вами в манифесте приложения. Диспетчер задач автоматически анализирует влияние ваших задач на процесс запуска.

Пользователи могут вручную отключить задачу запуска вашего приложения через диспетчер задач. Если пользователь отключил задачу, ее нельзя включить повторно программным способом.

#### <a name="xml-namespace"></a>Пространство имен XML

`http://schemas.microsoft.com/appx/manifest/desktop/windows10`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension
    Category="windows.startupTask"
    Executable="[ExecutableName]"
    EntryPoint="Windows.FullTrustApplication">
  <StartupTask
      TaskId="[TaskID]"
      Enabled="true"
      DisplayName="[DisplayName]" />
</Extension>
```

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.startupTask``.|
|Исполняемый файл |Относительный путь к исполняемому файлу для запуска. |
|TaskId |Уникальный идентификатор для вашей задачи. Используя этот идентификатор, ваше приложение может вызывать функции API в классе [Windows.ApplicationModel.StartupTask](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.StartupTask), чтобы программным способом включать или отключать задачу запуска. |
|Включен |Указывает, запускается ли задача первый раз включенной или отключенной. Включенная задача будет запущена при следующем входе пользователя (если только пользователь не отключил ее). |
|DisplayName |Имя задачи, которое отображается в диспетчере задач. Эту строку можно локализовать с помощью ```ms-resource```. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
  IgnorableNamespaces="desktop">
  <Applications>
    <Application>
      <Extensions>
        <desktop:Extension
          Category="windows.startupTask"
          Executable="bin\MyStartupTask.exe"
          EntryPoint="Windows.FullTrustApplication">
        <desktop:StartupTask
          TaskId="MyStartupTask"
          Enabled="true"
          DisplayName="My App Service" />
        </desktop:Extension>
      </Extensions>
    </Application>
  </Applications>
 </Package>
```

<a id="autoplay" />

### <a name="enable-users-to-start-your-application-when-they-connect-a-device-to-their-pc"></a>Предоставление пользователям возможности запускать приложение при подключении устройства к ПК

Автозапуск может представлять ваше приложение как один из вариантов, когда пользователь подключает устройство к своему ПК.

#### <a name="xml-namespace"></a>Пространство имен XML

`http://schemas.microsoft.com/appx/manifest/desktop/windows10/3`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.autoPlayHandler">
  <AutoPlayHandler>
    <InvokeAction ActionDisplayName="[action string]" ProviderDisplayName="[name of your app/service]">
      <Content ContentEvent="[Content event]" Verb="[any string]" DropTargetHandler="[Clsid]" />
      <Content ContentEvent="[Content event]" Verb="[any string]" Parameters="[Initialization parameter]"/>
      <Device DeviceEvent="[Device event]" HWEventHandler="[Clsid]" InitCmdLine="[Initialization parameter]"/>
    </InvokeAction>
  </AutoPlayHandler>
```

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.autoPlayHandler``.
|ActionDisplayName |Строка, представляющая действие, которое пользователи могут выполнить с устройством, которое они подключают к ПК (например: "Импорт файлов" или "Воспроизведение видео"). |
|ProviderDisplayName | Строка, представляющая ваше приложение или службу (например: "Видеопроигрыватель Contoso"). |
|ContentEvent |Имя события содержимого, при котором ``ActionDisplayName`` и ``ProviderDisplayName`` отправляют запрос пользователям. Событие содержимого возникает, когда к компьютеру подключается устройство тома, например карта памяти камеры, флэш-накопитель, или в дисковод вставляется DVD-диск. Полный список этих событий можно найти [здесь ](https://docs.microsoft.com/windows/uwp/launch-resume/auto-launching-with-autoplay#autoplay-event-reference).  |
|Команда |Параметр Verb определяет значение, передаваемое приложению для выбранного варианта. Для события автозапуска можно указать несколько действий при запуске и использовать параметр Verb, чтобы определить, какой вариант пользователь выбрал для приложения. Выбранный пользователем вариант можно узнать, проверив свойство verb аргументов события запуска, переданных приложению. Для параметра Verb можно использовать любое значение, кроме значения open, которое зарезервировано. |
|DropTargetHandler |Идентификатор класса приложения для реализации интерфейса [IDropTarget](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget?view=visualstudiosdk-2017). Файлы со съемного носителя передаются методу [Drop](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget.drop?view=visualstudiosdk-2017#Microsoft_VisualStudio_OLE_Interop_IDropTarget_Drop_Microsoft_VisualStudio_OLE_Interop_IDataObject_System_UInt32_Microsoft_VisualStudio_OLE_Interop_POINTL_System_UInt32__) вашей реализации [IDropTarget](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget?view=visualstudiosdk-2017).  |
|Параметры |Не требуется реализовывать интерфейс [IDropTarget](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget?view=visualstudiosdk-2017) для всех событий содержимого. Для любого из событий содержимого можно указать параметры командной строки вместо реализации интерфейса [IDropTarget](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget?view=visualstudiosdk-2017). Для этих событий Автозапуск запустит ваше приложение с использованием этих параметров командной строки. Можно проанализировать эти параметры в коде инициализации приложения, чтобы определить, было ли оно запущено с помощью Автозапуска, а затем предоставить пользовательскую реализацию. |
|DeviceEvent |Имя события устройства, при котором ``ActionDisplayName`` и ``ProviderDisplayName`` отправляют запрос пользователям. Событие устройства создается, если устройство подключено к ПК. События устройства начинаются со строки ``WPD`` и их можно найти [здесь](https://docs.microsoft.com/windows/uwp/launch-resume/auto-launching-with-autoplay#autoplay-event-reference). |
|HWEventHandler |Идентификатор класса приложения для реализации интерфейса [IHWEventHandler](https://docs.microsoft.com/windows/desktop/api/shobjidl/nn-shobjidl-ihweventhandler). |
|InitCmdLine |Параметр строки, который требуется передать в метод [Initialize](https://docs.microsoft.com/windows/desktop/api/shobjidl/nf-shobjidl-ihweventhandler-initialize) интерфейса [IHWEventHandler](https://docs.microsoft.com/windows/desktop/api/shobjidl/nn-shobjidl-ihweventhandler). |

### <a name="example"></a>Пример

```XML
<Package
  xmlns:desktop3="http://schemas.microsoft.com/appx/manifest/desktop/windows10/3"
  IgnorableNamespaces="desktop3">
  <Applications>
    <Application>
      <Extensions>
        <desktop3:Extension Category="windows.autoPlayHandler">
          <desktop3:AutoPlayHandler>
            <desktop3:InvokeAction ActionDisplayName="Import my files" ProviderDisplayName="ms-resource:AutoPlayDisplayName">
              <desktop3:Content ContentEvent="ShowPicturesOnArrival" Verb="show" DropTargetHandler="CD041BAE-0DEA-4472-9B7B-C98043D26EA8"/>
              <desktop3:Content ContentEvent="PlayVideoFilesOnArrival" Verb="play" Parameters="%1" />
              <desktop3:Device DeviceEvent="WPD\ImageSource" HWEventHandler="CD041BAE-0DEA-4472-9B7B-C98043D26EA8" InitCmdLine="/autoplay"/>
            </desktop3:InvokeAction>
          </desktop3:AutoPlayHandler>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="updates" />

### <a name="restart-automatically-after-receiving-an-update-from-the-microsoft-store"></a>Автоматический перезапуск после получения обновления из Microsoft Store

Если ваше приложение открыто, когда пользователь устанавливает обновление для него, приложение закрывается.

Если вы хотите, чтобы приложение перезапустилось после обновления, укажите вызов функции [RegisterApplicationRestart](https://docs.microsoft.com/windows/desktop/api/winbase/nf-winbase-registerapplicationrestart) для каждого процесса, который требуется перезапустить.

Каждое активное окно в приложении получает сообщение [WM_QUERYENDSESSION](https://docs.microsoft.com/windows/desktop/Shutdown/wm-queryendsession). В этот момент приложение может вызвать функцию [RegisterApplicationRestart](https://docs.microsoft.com/windows/desktop/api/winbase/nf-winbase-registerapplicationrestart) снова, чтобы при необходимости обновить командную строку.

Когда каждое активное окно приложения получает сообщение [WM_ENDSESSION](https://docs.microsoft.com/windows/desktop/Shutdown/wm-endsession), приложение должно сохранить данные и завершить работу.

>[!NOTE]
Активные окна также получают сообщение [WM_CLOSE](https://docs.microsoft.com/windows/desktop/winmsg/wm-close) в случае, если приложение не обрабатывает сообщение [WM_ENDSESSION](https://docs.microsoft.com/windows/desktop/Shutdown/wm-endsession).

В этот момент у приложения есть 30 секунд на закрытие своих процессов, либо платформа завершает их принудительно.

После завершения обновления приложение перезапускается.

## <a name="work-with-other-applications"></a>Работа с другими приложениями

Выполните интеграцию с другими приложениями, запустите другие процессы или предоставьте общий доступ к информации.

* [Отображение вашего приложения в виде целевого объекта печати в приложениях, поддерживающих печать](#printing)
* [Предоставление общего доступа к шрифтам другим приложениям для Windows](#fonts)
* [Запуск процесса Win32 из приложения для универсальной платформы Windows (UWP)](#win32-process)

<a id="printing" />

### <a name="make-your-application-appear-as-the-print-target-in-applications-that-support-printing"></a>Отображение вашего приложения в виде целевого объекта печати в приложениях, поддерживающих печать

Когда пользователи хотят распечатать данные из другого приложения, например Блокнота, можно показывать ваше приложение в виде целевого объекта печати в списке доступных объектов печати в этом приложении.

Вам необходимо изменить ваше приложение, чтобы оно получало данные для печати в формате XPS.

#### <a name="xml-namespaces"></a>Пространства имен XML

`http://schemas.microsoft.com/appx/manifest/desktop/windows10/2`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.appPrinter">
    <AppPrinter
        DisplayName="[DisplayName]"
        Parameters="[Parameters]" />
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-desktop2-appprinter).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.appPrinter``.
|DisplayName |Имя, которое должно отображаться в списке целевых объектов печати для приложения. |
|Параметры |Любые параметры, которые требуются вашему приложению для правильной обработки запроса. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:desktop2="http://schemas.microsoft.com/appx/manifest/desktop/windows10/2"
  IgnorableNamespaces="desktop2">
  <Applications>
  <Application>
    <Extensions>
      <desktop2:Extension Category="windows.appPrinter">
        <desktop2:AppPrinter
          DisplayName="Send to Contoso"
          Parameters="/insertdoc %1" />
      </desktop2:Extension>
    </Extensions>
  </Application>
</Applications>
</Package>
```

Пример, использующий это расширение, см. [здесь](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/PrintToPDF).

<a id="fonts" />

### <a name="share-fonts-with-other-windows-applications"></a>Предоставление общего доступа к шрифтам другим приложениям для Windows

Предоставьте совместный доступ к пользовательским шрифтам другим приложениям для Windows.

#### <a name="xml-namespaces"></a>Пространства имен XML

`http://schemas.microsoft.com/appx/manifest/desktop/windows10/2`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.sharedFonts">
    <SharedFonts>
      <Font File="[FontFile]" />
    </SharedFonts>
  </Extension>
```

Полный справочник по схеме можно найти [здесь](/uwp/schemas/appxpackage/uapmanifestschema/element-uap4-sharedfonts).

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.sharedFonts``.
|Файл |Файл, содержащий шрифты для общего доступа. |

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap4="http://schemas.microsoft.com/appx/manifest/uap/windows10/4"
  IgnorableNamespaces="uap4">
  <Applications>
    <Application>
      <Extensions>
        <uap4:Extension Category="windows.sharedFonts">
          <uap4:SharedFonts>
            <uap4:Font File="Fonts\JustRealize.ttf" />
            <uap4:Font File="Fonts\JustRealizeBold.ttf" />
          </uap4:SharedFonts>
        </uap4:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<a id="win32-process" />

### <a name="start-a-win32-process-from-a-universal-windows-platform-uwp-app"></a>Запуск процесса Win32 из приложения для универсальной платформы Windows (UWP)

Запустите процесс Win32, который выполняется с полным доверием.

#### <a name="xml-namespaces"></a>Пространства имен XML

`http://schemas.microsoft.com/appx/manifest/desktop/windows10`

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fullTrustProcess" Executable="[executable file]">
  <FullTrustProcess>
    <ParameterGroup GroupId="[GroupID]" Parameters="[Parameters]"/>
  </FullTrustProcess>
</Extension>
```

|Название |Описание |
|-------|-------------|
|Категория |Всегда ``windows.fullTrustProcess``.
|GroupID |Строка, определяющая набор параметров, которые необходимо передать в исполняемый файл. |
|Параметры |Параметры, которые необходимо передать в исполняемый файл. |

#### <a name="example"></a>Пример

```XML
<Package xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
         xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
         xmlns:rescap=
"http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities"
         xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10">
  ...
  <Capabilities>
      <rescap:Capability Name="runFullTrust"/>
  </Capabilities>
  <Applications>
    <Application>
      <Extensions>
          <desktop:Extension Category="windows.fullTrustProcess" Executable="fulltrustprocess.exe">
              <desktop:FullTrustProcess>
                  <desktop:ParameterGroup GroupId="SyncGroup" Parameters="/Sync"/>
                  <desktop:ParameterGroup GroupId="OtherGroup" Parameters="/Other"/>
              </desktop:FullTrustProcess>
           </desktop:Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

Это расширение может быть полезным, если требуется создать пользовательский интерфейс универсальной платформы Windows, работающий на всех устройствах, но при этом необходимо, чтобы компоненты вашего приложения Win32 продолжали работать с полным доверием.

Создайте пакет приложения Windows для приложения Win32. Затем добавьте это расширение в файл пакета вашего приложения UWP. Это расширение указывает, что исполняемый файл необходимо запускать в пакете приложения для Windows.  Если вы хотите, чтобы происходил обмен данными между вашими приложениями UWP и Win32, для этого можно настроить одну или несколько [служб приложений](/windows/uwp/launch-resume/app-services.md). Подробнее об этом сценарии см. [здесь](https://blogs.msdn.microsoft.com/appconsult/2016/12/19/desktop-bridge-the-migrate-phase-invoking-a-win32-process-from-a-uwp-app/).

## <a name="next-steps"></a>Дальнейшие действия

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).
