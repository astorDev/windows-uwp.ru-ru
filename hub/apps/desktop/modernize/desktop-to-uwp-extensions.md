---
Description: Можно использовать расширения для интеграции вашего упакованного классического приложения с Windows 10 предопределенными способами.
title: Интеграция упакованные приложения рабочего стола с Windows 10 и универсальной платформы Windows (мост для классических приложений)
ms.date: 04/18/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 0a8cedac-172a-4efd-8b6b-67fd3667df34
ms.localizationpriority: medium
ms.openlocfilehash: a7815639c8dafa555be6d4f0eac0eb07b43dba98
ms.sourcegitcommit: f0f933d5cf0be734373a7b03e338e65000cc3d80
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65984454"
---
# <a name="integrate-your-packaged-desktop-app-with-windows-10-and-uwp"></a>Интеграция упакованные приложения рабочего стола с Windows 10 и UWP

Если вы [упаковки классического приложения в контейнере MSIX](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root), расширения можно использовать для интеграции упакованные приложения рабочего стола с Windows 10 с помощью предварительно определенных расширений в [манифест пакета приложения](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/schema-root).

Например можно воспользоваться таким расширением, создать исключение брандмауэра, сделать приложение приложением по умолчанию для типа файла или пункт start плитки упакованную версию приложения. Чтобы использовать расширение, просто добавьте разметку XML в файл манифеста пакета приложения. Никакой код не требуется.

В этой статье описываются эти расширения и задачи, которые можно выполнять с помощью их.

> [!NOTE]
> Функции, описанные в этой статье требуется создать пакет приложения Windows для своего настольного приложения. Если вы еще не сделали этого, см. в разделе [пакет приложений для настольных компьютеров](https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-root).

## <a name="transition-users-to-your-app"></a>Переход пользователей на ваше приложение

Помогите пользователям выполнить переход на ваше упакованное приложение.

* [Выберите существующий начала плитки и кнопки панели задач упакованные приложения](#point)
* [Упакованные приложения открывать файлы, а не Классические приложения](#make)
* [Сопоставить упакованные приложения с набором типов файлов](#associate)
* [Добавьте параметры в контекстные меню файлов определенных типов файлов](#add)
* [Открыть определенные типы файлов непосредственно с помощью URL-адреса](#open)

<a id="point" />

### <a name="point-existing-start-tiles-and-taskbar-buttons-to-your-packaged-app"></a>Определение существующих плиток начального экрана и кнопок на панели задач в качестве указателей на упакованное приложение

Может оказаться так, что пользователи закрепили ваше классическое приложение на панели задач или в меню "Пуск". Вы можете определить эти ярлыки в качестве указателей на новое упакованное приложение.

#### <a name="xml-namespace"></a>Пространство имен XML

http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3

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

|Имя | Описание |
|-------|-------------|
|Category |Всегда ``windows.desktopAppMigration``.
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

[Программа просмотра изображений WPF с перехода или миграции/удаления](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<a id="make" />

### <a name="make-your-packaged-application-open-files-instead-of-your-desktop-app"></a>Упакованные приложения открывать файлы, а не Классические приложения

Можно убедиться в том, что пользователи открывать нового упакованные приложения по умолчанию для определенных типов файлов, вместо открытия настольной версии приложения.

Для этого укажите [программный идентификатор (ProgID)](https://msdn.microsoft.com/library/windows/desktop/cc144152.aspx) каждого приложения, из которого необходимо наследовать сопоставления файлов.

#### <a name="xml-namespaces"></a>Пространства имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10/3
* http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
<FileTypeAssociation Name="[AppID]">
         <MigrationProgIds>
            <MigrationProgId>"[ProgID]"</MigrationProgId>
        </MigrationProgIds>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. Этот идентификатор предназначен для внутреннего использования и применяется для генерации хэшированного [программного идентификатора (ProgID)](https://msdn.microsoft.com/library/windows/desktop/cc144152.aspx), ассоциированного с вашим сопоставлением типов файлов. Вы можете использовать этот идентификатор для контроля изменений в будущих версиях своего приложения. |
|MigrationProgId |[Программный идентификатор (ProgID)](https://msdn.microsoft.com/library/windows/desktop/cc144152.aspx) , описывающий приложение, компонент и версии приложения рабочего стола, из которого требуется выполнить наследование сопоставления файлов.|

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
          <uap3:FileTypeAssociation Name="Contoso">
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

[Программа просмотра изображений WPF с перехода или миграции/удаления](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<a id="associate" />

### <a name="associate-your-packaged-application-with-a-set-of-file-types"></a>Сопоставить упакованные приложения с набором типов файлов

Упакованные приложения может быть связан расширений типов файлов. Если пользователь щелкает правой кнопкой мыши файл и затем выбирает **открыть с помощью** параметр, приложение появится в списке предложений.

#### <a name="xml-namespace"></a>Пространство имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10
* http://schemas.microsoft.com/appx/manifest/uap/windows10/3

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[AppID]">
        <SupportedFileTypes>
            <FileType>"[file extension]"</FileType>
        </SupportedFileTypes>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. Этот идентификатор предназначен для внутреннего использования и применяется для генерации хэшированного [программного идентификатора (ProgID)](https://msdn.microsoft.com/library/windows/desktop/cc144152.aspx), ассоциированного с вашим сопоставлением типов файлов. Вы можете использовать этот идентификатор для контроля изменений в будущих версиях своего приложения.   |
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
          <uap3:FileTypeAssociation Name="Contoso">
            <uap:SupportedFileTypes>
            <uap:FileType>.txt</uap:FileType>
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

[Программа просмотра изображений WPF с перехода или миграции/удаления](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<a id="add" />

### <a name="add-options-to-the-context-menus-of-files-that-have-a-certain-file-type"></a>Добавление параметров в контекстное меню файлов определенного типа

В большинстве случаев пользователи дважды щелкают файлы, чтобы открыть их. Если пользователи щелкают файл правой кнопкой мыши, отображаются различные параметры.

Вы можете добавить параметры в это меню. Они предоставят пользователям другие способы взаимодействия с вашим файлом, например печать, редактирование или предварительный просмотр.

#### <a name="xml-namespaces"></a>Пространства имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10
* http://schemas.microsoft.com/appx/manifest/uap/windows10/2
* http://schemas.microsoft.com/appx/manifest/uap/windows10/3

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[AppID]">
        <SupportedVerbs>
           <Verb Id="[ID]" Extended="[Extended]" Parameters="[parameters]">"[verb label]"</Verb>
        </SupportedVerbs>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category | Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. |
|Команда |Имя, которое отображается в контекстном меню проводника. Эта строка может быть локализована с помощью ```ms-resource```.|
|Id |Уникальный идентификатор команды. Если ваше приложение приложения универсальной платформы Windows, он передается в приложение как часть его аргументы события активации, чтобы оно обрабатывало выбора пользователя. Если приложение является полным доверием упакованного приложения, оно получает параметры вместо (см. следующий пункт). |
|Параметры |Связанный с командой список параметров и значений аргументов. Если приложение является полным доверием упакованного приложения, эти параметры передаются приложению в виде аргументы события, при активации приложения. Можно настроить поведение приложения в зависимости от различных активации команд. Если переменная может содержать путь к файлу, заключите значение этого параметра в кавычки. Это позволит избежать проблем, которые возникают в случаях, когда путь содержит пробелы. Если приложение является приложение UWP, невозможно передать параметры. Вместо этого приложение получит идентификатор Id (см. предыдущий пункт).|
|Расширенный |Указывает, что команда появляется, только если пользователь отобразил контекстное меню, удерживая клавишу **SHIFT** перед нажатием правой кнопки мыши. Этот атрибут необязателен и при отсутствии по умолчанию имеет значение **False** (всегда отображать команду). Это поведение задается отдельно для каждой команды (кроме команды «Открыть», для которой всегда используется значение **False**).|

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
          <uap3:FileTypeAssociation Name="Contoso">
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

[Программа просмотра изображений WPF с перехода или миграции/удаления](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<a id="open" />

### <a name="open-certain-types-of-files-directly-by-using-a-url"></a>Открытие определенных типов файлов напрямую с помощью URL-адреса

Можно убедиться в том, что пользователи открывать нового упакованные приложения по умолчанию для определенных типов файлов, вместо открытия настольной версии приложения.

#### <a name="xml-namespaces"></a>Пространства имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10
* http://schemas.microsoft.com/appx/manifest/uap/windows10/3"

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[AppID]" UseUrl="true" Parameters="%1">
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. |
|UseUrl |Указывает, следует ли открывать файлы непосредственно из целевого URL-адреса. Если это значение не задано, попыток приложения таким образом, чтобы открыть файл с помощью причина URL-адрес системы для первой загрузки файл локально. |
|Параметры |Дополнительные параметры. |
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
            <uap3:FileTypeAssociation Name="documenttypes" UseUrl="true" Parameters="%1">
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

* [Создать исключение брандмауэра для вашего приложения](#rules)
* [Разместите файлы DLL в любой папке пакета](#load-paths)

<a id="rules" />

### <a name="create-firewall-exception-for-your-app"></a>Создание исключения брандмауэра для вашего приложения

Если приложению требуется обмен данными через порт, можно добавить приложение в список исключений брандмауэра.

#### <a name="xml-namespace"></a>Пространство имен XML

http://schemas.microsoft.com/appx/manifest/desktop/windows10/2

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

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.firewallRules``|
|Исполняемый файл |Имя исполняемого файла, который необходимо добавить в список исключений брандмауэра |
|Direction |Указывает направление правила подключения: входящее или исходящее |
|IPProtocol |Протокол взаимодействия |
|LocalPortMin |Наименьшее значение номера порта в диапазоне номеров локального порта. |
|LocalPortMax |Наибольшее значение номера порта в диапазоне номеров локального порта. |
|RemotePortMin |Наименьшее значение номера порта в диапазоне номеров удаленного порта. |
|RemotePortMin |Наибольшее значение номера порта в диапазоне номеров удаленного порта. |
|Профиль |Тип сети |

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

### <a name="place-your-dll-files-into-any-folder-of-the-package"></a>Поместите файлы DLL в любую папку пакета

Используйте расширение для идентификации этих папок. Так система сможет найти и загрузить файлы, которые вы там разместите. Представьте, что это расширение — замена переменной среды _%PATH%_.

Если не использовать это расширение, система выполнит поиск графа зависимости пакетов для этого процесса, корневую папку пакета и системный каталог (_%SystemRoot%\system32_) в указанном порядке. Дополнительные сведения см. в разделе [Порядок поиска приложений для Windows](https://msdn.microsoft.com/library/windows/desktop/ms682586.aspx#_search_order_for_windows_store_apps).

Каждый пакет может содержать только одно из этих расширений. Это означает, что одно из них можно добавить в главный пакет, а затем добавить по одному к каждому из [необязательных пакетов и связанных наборов](https://docs.microsoft.com/windows/uwp/packaging/optional-packages).

#### <a name="xml-namespace"></a>Пространство имен XML

http://schemas.microsoft.com/appx/manifest/uap/windows10/6

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

Объявите это расширение на уровне пакета манифеста вашего приложения.

```XML
<Extension Category="windows.loaderSearchPathOverride">
  <LoaderSearchPathOverride>
    <LoaderSearchPathEntry FolderPath="[path]"/>
  </LoaderSearchPathOverride>
</Extension>

```

|Имя | Описание |
|-------|-------------|
|Category |Всегда ``windows.loaderSearchPathOverride``.
|FolderPath | Путь к папке, содержащей файлы dll. Укажите путь, который задан относительно корневой папки пакета. Можно задать до пяти полей в одном расширении. Если нужно, чтобы система искала файлы в корневой папке пакета, используйте для одного из этих путей пустую строку. Не включайте дублирующиеся пути и убедитесь, что ваши пути не содержат косых черт и обратных косых черт в начале и в конце. <br><br> Система не может выполнять поиск вложенных папок, поэтому убедитесь, что каждая пака, содержащая файлы DLL, которая должна быть загружена системой, включена в список явным образом.|

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

Помогите пользователям упорядочить ваши файлы и работать с ними привычными способами.

* [Определите поведение приложения, когда пользователи выбирают и открытия нескольких файлов одновременно](#define)
* [Показать содержимое файла в эскиз в проводнике](#show)
* [Показать содержимое файла в области просмотра проводника](#preview)
* [Разрешить пользователям проходить группу файлов с помощью типа столбца в проводнике](#enable)
* [Доступность свойств файла для поиска, индекс, диалоговые окна свойств и в области сведений](#make-file-properties)
* [Предоставить файлы из облачной службы в проводнике](#cloud-files)

<a id="define" />

### <a name="define-how-your-application-behaves-when-users-select-and-open-multiple-files-at-the-same-time"></a>Определите поведение приложения, когда пользователи выбирают и открытия нескольких файлов одновременно

Укажите, как приложение ведет себя, когда пользователь открывает несколько файлов одновременно.

#### <a name="xml-namespaces"></a>Пространства имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10
* http://schemas.microsoft.com/appx/manifest/uap/windows10/2
* http://schemas.microsoft.com/appx/manifest/uap/windows10/3

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[AppID]" MultiSelectModel="[SelectionModel]">
        <SupportedVerbs>
            <Verb Id="Edit" MultiSelectModel="[SelectionModel]">Edit</Verb>
        </SupportedVerbs>
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. |
|MultiSelectModel |См. ниже. |
|FileType |Соответствующие расширения файлов. |

**MultSelectModel**

Для упакованных классических приложений, как и для обычных классических приложений, имеется три варианта.

* ``Player``. Приложение активируется один раз. Все выбранные файлы передаются в приложение в виде параметров аргумента.
* ``Single``. Приложение активируется один раз для первого выбранного файла. Другие файлы игнорируются.
* ``Document``. Новый отдельный экземпляр приложения активируется для всех выбранных файлов.

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
          <uap3:FileTypeAssociation Name="myapp" MultiSelectModel="Document">
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

Если пользователь открывает 15 или менее файлов, значение по умолчанию для атрибута **MultiSelectModel** — *Player*. В противном случае значением по умолчанию является *Document*. Приложения UWP всегда запускаются в режиме *Player*.

<a id="show" />

### <a name="show-file-contents-in-a-thumbnail-image-within-file-explorer"></a>Отображение содержимого файла в эскизе в проводнике

Добавьте для пользователей возможность просматривать эскиз содержимого файла, когда значок файла имеет большой, средний или очень большой размер.

#### <a name="xml-namespace"></a>Пространство имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10
* http://schemas.microsoft.com/appx/manifest/uap/windows10/2
* http://schemas.microsoft.com/appx/manifest/uap/windows10/3
* http://schemas.microsoft.com/appx/manifest/desktop/windows10/2

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[AppID]">
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
        <ThumbnailHandler
            Clsid  ="[Clsid  ]" />
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. |
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
          <uap3:FileTypeAssociation Name="Contoso">
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

Позволяет пользователям просматривать содержимое файла в области предварительного просмотра в проводнике.

#### <a name="xml-namespace"></a>Пространство имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10
* http://schemas.microsoft.com/appx/manifest/uap/windows10/2
* http://schemas.microsoft.com/appx/manifest/uap/windows10/3
* http://schemas.microsoft.com/appx/manifest/desktop/windows10/2

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[AppID]">
        <SupportedFileTypes>
            <FileType>"[FileExtension]"</FileType>
        </SupportedFileTypes>
        <DesktopPreviewHandler Clsid  ="[Clsid  ]" />
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. |
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
          <uap3:FileTypeAssociation Name="Contoso">
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

Вы можете связать одно или несколько стандартных значений для ваших типов файлов с помощью поля **Kind** (Вид).

В проводнике пользователей смогут группировать эти файлы с помощью данного поля. Компоненты системы также используют это поле для различных целей, например индексации.

Дополнительную информацию о поле **Kind** и его возможных значениях см. в разделе [Использование имен Kind](https://msdn.microsoft.com/library/windows/desktop/cc144136.aspx).

#### <a name="xml-namespaces"></a>Пространства имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10
* http://schemas.microsoft.com/appx/manifest/foundation/windows10/restrictedcapabilities/3

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fileTypeAssociation">
    <FileTypeAssociation Name="[AppID]">
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

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. |
|FileType |Соответствующие расширения файлов. |
|value |Допустимое [значение Kind](https://msdn.microsoft.com/en-us/library/windows/desktop/cc144136.aspx#kind_hierarchy). |

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
           <uap:FileTypeAssociation Name="Contoso">
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

### <a name="make-file-properties-available-to-search-index-property-dialogs-and-the-details-pane"></a>Включение свойств файлов в поиск, индекс, диалоговые окна свойств и область сведений

#### <a name="xml-namespace"></a>Пространство имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10
* http://schemas.microsoft.com/appx/manifest/uap/windows10/3
* http://schemas.microsoft.com/appx/manifest/desktop/windows10/2

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<uap:Extension Category="windows.fileTypeAssociation">
    <uap:FileTypeAssociation Name="[AppID]">
        <SupportedFileTypes>
            <FileType>.bar</FileType>
        </SupportedFileTypes>
        <DesktopPropertyHandler Clsid ="[Clsid]"/>
    </uap:FileTypeAssociation>
</uap:Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Имя |Уникальный идентификатор для вашего приложения. |
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
          <uap3:FileTypeAssociation Name="Contoso">
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

<a id="cloud-files" />

### <a name="make-files-from-your-cloud-service-appear-in-file-explorer"></a>Настройка доступа к файлам из облачной службы в проводнике

Регистрация обработчиков, реализованных в вашем приложении. Можно также добавить параметры контекстного меню, отображаемые, когда пользователи щелкают правой кнопкой ваши облачные файлы в проводнике.

#### <a name="xml-namespace"></a>Пространство имен XML

* http://schemas.microsoft.com/appx/manifest/desktop/windows10

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

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.cloudfiles``.
|iconResource |Значок, который представляет службу поставщика облачных файлов. Этот значок отображается в области навигации проводника.  Пользователи выбирают этот значок, чтобы показать файлы из вашей облачной службы. |
|CustomStateHandler Clsid |Идентификатор класса, реализующего CustomStateHandler приложения. Система использует этот идентификатор класса для запроса пользовательских состояний и столбцов для облачных файлов. |
|ThumbnailProviderHandler Clsid |Идентификатор класса, реализующего ThumbnailProviderHandler приложения. Система использует этот идентификатор класса для запроса эскизов для облачных файлов. |
|ExtendedPropertyHandler Clsid |Идентификатор класса, реализующего ExtendedPropertyHandler приложения.  Система использует этот идентификатор класса для запроса расширенных свойств для облачного файла. |
|Команда |Имя, отображаемое в контекстном меню проводника для файлов, предоставляемых облачной службы. |
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

## <a name="start-your-application-in-different-ways"></a>Запустите приложение по-разному

* [Запустите приложение с использованием протоколов](#protocol)
* [Запустите приложение с помощью псевдонима](#alias)
* [Запустить исполняемый файл, когда пользователь входит в Windows](#executable)
* [Позволяют пользователям запускать приложение при подключении устройства к ПК](#autoplay)
* [Автоматический перезапуск после получения обновления от Microsoft Store](#updates)

<a id="protocol" />

### <a name="start-your-application-by-using-a-protocol"></a>Запустите приложение с использованием протоколов

Сопоставления протоколов позволяют другим программам и компонентам системы взаимодействовать с упакованным приложением. Когда упакованное приложение запускается с использованием протоколов, можно указать определенные параметры для передачи аргументов событий активации, он может вести себя соответствующим образом. Параметры поддерживаются только для упакованных приложений с полным доверием. Приложения UWP не могут использовать параметры.

#### <a name="xml-namespace"></a>Пространство имен XML

http://schemas.microsoft.com/appx/manifest/uap/windows10/3

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

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.protocol``.
|Имя |Имя протокола. |
|Параметры |Список параметров и значений, передаваемые в приложение в виде аргументов событий при активации приложения. Если переменная может содержать путь к файлу, заключите значение этого параметра в кавычки. Это позволит избежать проблем, которые возникают в случаях, когда путь содержит пробелы. |

### <a name="example"></a>Пример

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

<a id="alias" />

### <a name="start-your-application-by-using-an-alias"></a>Запустите приложение с помощью псевдонима

Пользователи и другие процессы могут использовать псевдоним для запуска приложения без необходимости указывать полный путь к приложению. Вы можете указать имя этого псевдонима.

#### <a name="xml-namespaces"></a>Пространства имен XML

* http://schemas.microsoft.com/appx/manifest/uap/windows10/3
* http://schemas.microsoft.com/appx/manifest/desktop/windows10

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

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.appExecutionAlias``.
|Исполняемый файл |Относительный путь к исполняемому файлу для запуска приложения при применении псевдонима. |
|Alias |Короткое имя для вашего приложения. Оно всегда должно завершаться расширением .exe. Для каждого приложения в пакете можно указать только один псевдоним выполнения приложения. Если несколько приложений зарегистрированы с одним и тем же псевдонимом, система будет активировать псевдоним, который был зарегистрирован последним, поэтому следует выбирать уникальный псевдоним, который не будет переопределен другими приложениями.
|

#### <a name="example"></a>Пример

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
 
...
</Package>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

<a id="executable" />

### <a name="start-an-executable-file-when-users-log-into-windows"></a>Запуск исполняемого файла при входе пользователей в Windows

Задачи запуска с помощью приложение для автоматического запуска исполняемого файла каждый раз, когда пользователь выполняет вход.

> [!NOTE]
> Пользователь должен запустить приложение по крайней мере один раз для регистрации этой задачи запуска.

Приложение можно объявить несколько задач запуска. Каждая задача запускается независимо друг от друга. Все задачи автозагрузки отображаются в диспетчере задач во вкладке **Автозагрузка** и имеют значки вашего приложения и имена, указанные вами в манифесте приложения. Диспетчер задач автоматически анализирует влияние ваших задач на процесс запуска.

Пользователи могут вручную отключить задачи автозагрузки вашего приложения через диспетчер задач. Если пользователь отключил задачу, ее нельзя включить повторно программным способом.

#### <a name="xml-namespace"></a>Пространство имен XML

http://schemas.microsoft.com/appx/manifest/desktop/windows10

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

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.startupTask``.|
|Исполняемый файл |Относительный путь к исполняемому файлу для запуска. |
|TaskId |Уникальный идентификатор для вашей задачи. Используя этот идентификатор, приложение может вызвать API-интерфейсы [Windows.ApplicationModel.StartupTask](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.StartupTask) класс программно включить или отключить задачу запуска. |
|Enabled |Указывает, включен или отключен начальный запуск задачи. Включенная задача будет запущена при следующем входе пользователя (если только пользователь не отключил ее). |
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

### <a name="enable-users-to-start-your-application-when-they-connect-a-device-to-their-pc"></a>Позволяют пользователям запускать приложение при подключении устройства к ПК

Автозапуск можно представить как параметр приложения, когда пользователь подключает устройство к ПК.

#### <a name="xml-namespace"></a>Пространство имен XML

http://schemas.microsoft.com/appx/manifest/desktop/windows10/3

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

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.autoPlayHandler``.
|ActionDisplayName |Строка, представляющая действие, которое пользователи могут выполнять с устройством, в которой они подключаются к Компьютеру (например: «Импортировать файлы» или «Play видео»). |
|ProviderDisplayName | Строка, представляющая ваше приложение или служба (например: «Contoso видеопроигрывателя»). |
|ContentEvent |Имя события содержимого, при котором ``ActionDisplayName``и ``ProviderDisplayName`` отправляют запрос пользователям. Событие содержимого возникает, когда к компьютеру подключается устройство тома, например карта памяти камеры, флэш-накопитель или DVD-диск. Полный список этих событий можно найти [здесь ](https://docs.microsoft.com/windows/uwp/launch-resume/auto-launching-with-autoplay#autoplay-event-reference).  |
|Команда |Параметр команды определяет значение, которое передается в приложение для выбранного параметра. Для события автозапуска можно указать несколько действий при запуске и использовать параметр Verb, чтобы определить, какой вариант пользователь выбрал для приложения. Выбранный пользователем вариант можно узнать, проверив свойство verb аргументов события запуска, переданных приложению. Для параметра Verb можно использовать любое значение, кроме значения open, которое зарезервировано. |
|DropTargetHandler |Идентификатор класса приложения, которое реализует [IDropTarget](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget?view=visualstudiosdk-2017) интерфейс. Файлы со съемного носителя передаются методу [Drop](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget.drop?view=visualstudiosdk-2017#Microsoft_VisualStudio_OLE_Interop_IDropTarget_Drop_Microsoft_VisualStudio_OLE_Interop_IDataObject_System_UInt32_Microsoft_VisualStudio_OLE_Interop_POINTL_System_UInt32__) вашей реализации [IDropTarget](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget?view=visualstudiosdk-2017).  |
|Параметры |Не требуется реализовывать интерфейс [IDropTarget](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget?view=visualstudiosdk-2017) для всех событий содержимого. Для любого из событий содержимого можно указать параметры командной строки вместо реализации интерфейса [IDropTarget](https://docs.microsoft.com/dotnet/api/microsoft.visualstudio.ole.interop.idroptarget?view=visualstudiosdk-2017). Для этих событий автозапуска будет запустить приложение с помощью этих параметров командной строки. Можно проанализировать эти параметры в коде инициализации приложения, чтобы определить, было ли оно запущено с помощью автозапуска, а затем предоставить пользовательскую реализацию. |
|DeviceEvent |Имя события устройства, при котором ``ActionDisplayName``и ``ProviderDisplayName`` отправляют запрос пользователям. Событие устройства создается, если устройство подключено к ПК. События устройства начинаются со строки ``WPD`` и их можно найти [здесь](https://docs.microsoft.com/windows/uwp/launch-resume/auto-launching-with-autoplay#autoplay-event-reference). |
|HWEventHandler |Идентификатор класса приложения, которое реализует [IHWEventHandler](https://msdn.microsoft.com/library/windows/desktop/bb775492.aspx) интерфейс. |
|InitCmdLine |Параметр строки, который требуется передать в метод [Initialize](https://msdn.microsoft.com/en-us/library/windows/desktop/bb775495.aspx) интерфейса [IHWEventHandler](https://msdn.microsoft.com/library/windows/desktop/bb775492.aspx). |

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

Если приложение открыто, если пользователи устанавливают обновления к нему, закрытия приложения.

Если это приложение перезагрузки после обновления завершения, вызовите [RegisterApplicationRestart](https://msdn.microsoft.com/library/windows/desktop/aa373347.aspx) функции в каждый процесс, который требуется перезапустить.

Получает каждого активного окна в приложении [WM_QUERYENDSESSION](https://msdn.microsoft.com/library/windows/desktop/aa376890.aspx) сообщения. На этом этапе приложение может вызвать [RegisterApplicationRestart](https://msdn.microsoft.com/library/windows/desktop/aa373347.aspx) функцию еще раз, чтобы при необходимости обновите командной строки.

При получении каждого активного окна в приложении [WM_ENDSESSION](https://msdn.microsoft.com/library/windows/desktop/aa376889.aspx) сообщения, приложения следует сохранить данные и завершить работу.

>[!NOTE]
Active windows также получают [WM_CLOSE](https://msdn.microsoft.com/library/windows/desktop/ms632617.aspx) сообщений в случае, если приложение не обрабатывает [WM_ENDSESSION](https://msdn.microsoft.com/library/windows/desktop/aa376889.aspx) сообщения.

На этом этапе приложение должно закрыть собственный процессы 30 секунд или платформы принудительно завершает их.

После завершения обновления приложение перезапускается.

## <a name="work-with-other-applications"></a>Работа с другими приложениями

Интеграция с другими приложениями, запуск других процессов или совместное использование информации.

* [Приложения отображаются как объект печати в приложениях, поддерживающих печати](#printing)
* [Совместное использование шрифтов с другими приложениями Windows](#fonts)
* [Запустить процесс Win32 из приложения универсальной платформы Windows (UWP)](#win32-process)

<a id="printing" />

### <a name="make-your-application-appear-as-the-print-target-in-applications-that-support-printing"></a>Приложения отображаются как объект печати в приложениях, поддерживающих печати

Если требуется печатать данные из другого приложения, например в блокноте, могут сделать приложение отображается как целевой объект печати в списке приложений из доступных целевых объектов для печати.

Необходимо изменить приложение так, чтобы он получал печати данных в формате XML Paper Specification (XPS).

#### <a name="xml-namespaces"></a>Пространства имен XML

http://schemas.microsoft.com/appx/manifest/desktop/windows10/2

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.appPrinter">
    <AppPrinter
        DisplayName="[DisplayName]"
        Parameters="[Parameters]" />
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-desktop2-appprinter).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.appPrinter``.
|DisplayName |Имя, которое должно отображаться в списке целевых объектов печати для приложения. |
|Параметры |Любые параметры, которые требуются приложению для правильной обработки запроса. |

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

Пример, использующий это расширение, находится [здесь](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/PrintToPDF)

<a id="fonts" />

### <a name="share-fonts-with-other-windows-applications"></a>Совместное использование шрифтов с другими Windows-приложениями

Совместное использование пользовательских шрифтов с другими Windows-приложениями.

#### <a name="xml-namespaces"></a>Пространства имен XML

http://schemas.microsoft.com/appx/manifest/desktop/windows10/2

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.sharedFonts">
    <SharedFonts>
      <Font File="[FontFile]" />
    </SharedFonts>
  </Extension>
```

Полный справочник по схеме можно найти [здесь](/uwp/schemas/appxpackage/uapmanifestschema/element-uap4-sharedfonts).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.sharedFonts``.
|Файл |Файл, содержащий шрифты, которые вы хотите предоставить в общий доступ. |

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

### <a name="start-a-win32-process-from-a-universal-windows-platform-uwp-app"></a>Запуск процесса Win32 из приложения для универсальной платформы Windows (UWP)

Запуск процесса Win32, который выполняется с полным доверием.

#### <a name="xml-namespaces"></a>Пространства имен XML

http://schemas.microsoft.com/appx/manifest/desktop/windows10

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<Extension Category="windows.fullTrustProcess" Executable="[executable file]">
  <FullTrustProcess>
    <ParameterGroup GroupId="[GroupID]" Parameters="[Parameters]"/>
  </FullTrustProcess>
</Extension>
```

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fullTrustProcess``.
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

Это расширение может быть полезно, если вы хотите создать универсальный Windows платформы пользовательский интерфейс, который запускается на всех устройствах, но вам требуется, чтобы компоненты приложения Win32, чтобы продолжить выполнение с полным уровнем доверия.

Просто создайте пакет приложения Windows для вашего приложения Win32. Затем добавьте это расширение в файл пакета вашего приложения UWP. Этого расширения указывает, что вы хотите запустить исполняемый файл в пакет приложения Windows.  Если вы хотите обмениваться данными между вашими приложениями UWP и Win32, для этого можно настроить одну или несколько [служб приложений](/windows/uwp/launch-resume/app-services.md). Подробнее об этом сценарии см. [здесь](https://blogs.msdn.microsoft.com/appconsult/2016/12/19/desktop-bridge-the-migrate-phase-invoking-a-win32-process-from-a-uwp-app/).

## <a name="next-steps"></a>Следующие шаги

**Найдите ответы на ваши вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Отправить отзыв или предложения по функциям**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial)
