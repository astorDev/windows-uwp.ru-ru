---
author: normesta
Description: "Можно использовать расширения для интеграции вашего упакованного классического приложения с Windows 10 предопределенными способами."
Search.Product: eADQiWindows 10XVcnh
title: "Интеграция приложения с Windows 10 (мост для классических приложений)"
ms.author: normesta
ms.date: 05/25/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.assetid: 0a8cedac-172a-4efd-8b6b-67fd3667df34
ms.openlocfilehash: 0c3427a7b49b17fda9a3ba0680e59b134732e9fa
ms.sourcegitcommit: 38ef208ef457ce1857038c9cde3658c884d29b75
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2017
---
# <a name="integrate-your-app-with-windows-10-desktop-bridge"></a>Интеграция приложения с Windows 10 (мост для классических приложений)

Используйте расширения для интеграции вашего приложения с Windows 10 предопределенными способами.

Например, используйте расширение, чтобы добавить исключение брандмауэра, указать ваше приложение как приложение по умолчанию для определенного типа файлов или определить плитки начального экрана в качестве указателей на упакованную версию вашего приложения. Чтобы использовать расширение, просто добавьте разметку XML в файл манифеста пакета приложения. Никакой код не требуется.

В этой статье описаны эти расширения и задачи, которые можно выполнить с их помощью.

## <a name="transition-users-to-your-app"></a>Переход пользователей на ваше приложение

Помогите пользователям выполнить переход на ваше упакованное приложение.

* [Определение существующих плиток начального экрана и кнопок на панели задач в качестве указателей на упакованное приложение](#point)
* [Открытие файлов в упакованном приложении вместо классического приложения](#make)
* [Создание связи вашего упакованного приложения с набором типов файлов](#associate)
* [Добавление параметров в контекстное меню файлов определенного типа](#add)
* [Открытие определенных типов файлов напрямую с помощью URL-адреса](#open)

<span id="point" />
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

[Программа просмотра изображений WPF с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<span id="make" />
### <a name="make-your-packaged-app-open-files-instead-of-your-desktop-app"></a>Открытие файлов в упакованном приложении вместо классического приложения

Вы можете убедиться, что по умолчанию пользователи работают с новым упакованным приложением (а не его классической версией) при открытии определенных типов файлов.

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
|Name |Уникальный идентификатор для вашего приложения. Этот идентификатор предназначен для внутреннего использования и применяется для генерации хэшированного [программного идентификатора (ProgID)](https://msdn.microsoft.com/library/windows/desktop/cc144152.aspx), ассоциированного с вашим сопоставлением типов файлов. Вы можете использовать этот идентификатор для контроля изменений в будущих версиях своего приложения. |
|MigrationProgId |[Программный идентификатор (ProgID)](https://msdn.microsoft.com/library/windows/desktop/cc144152.aspx), описывающий приложение, компонент и версию классического приложения, из которого необходимо наследовать сопоставления файлов.|

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

[Программа просмотра изображений WPF с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<span id="associate" />
### <a name="associate-your-packaged-app-with-a-set-of-file-types"></a>Создание связи вашего упакованного приложения с набором типов файлов

Упакованные приложения можно ассоциировать с расширениями типов файлов. Если пользователь щелкнет файл правой кнопкой мыши и выберет команду **Открыть с помощью**, ваше приложение отобразится в появившемся списке вариантов.

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
|Name |Уникальный идентификатор для вашего приложения. Этот идентификатор предназначен для внутреннего использования и применяется для генерации хэшированного [программного идентификатора (ProgID)](https://msdn.microsoft.com/library/windows/desktop/cc144152.aspx), ассоциированного с вашим сопоставлением типов файлов. Вы можете использовать этот идентификатор для контроля изменений в будущих версиях своего приложения.   |
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

[Программа просмотра изображений WPF с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<span id="add" />
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
|Name |Уникальный идентификатор для вашего приложения. |
|Verb |Имя, которое отображается в контекстном меню проводника. Эта строка может быть локализована с помощью ```ms-resource```.|
|Id |Уникальный идентификатор команды. Если у вас приложение UWP, этот идентификатор передается в ваше приложение в составе аргументов события активации, чтобы приложение могло обработать выбор пользователя соответствующим образом. Если у вас упакованное приложение с полным доверием, оно вместо этого будет получать параметры (см. следующий пункт). |
|Parameters |Связанный с командой список параметров и значений аргументов. Если ваше приложение является упакованным приложением с полным доверием, эти параметры передаются в приложение в качестве аргументов событий при его активации. Вы можете настроить поведение вашего приложения при различных командах активации. Если переменная может содержать путь к файлу, заключите значение этого параметра в кавычки. Это позволит избежать проблем, которые возникают в случаях, когда путь содержит пробелы. Если ваше приложение является приложением UWP, передача параметров невозможна. Вместо этого приложение получит идентификатор Id (см. предыдущий пункт).|
|Extended |Указывает, что команда появляется, только если пользователь отобразил контекстное меню, удерживая клавишу **SHIFT** перед нажатием правой кнопки мыши. Этот атрибут необязателен и при отсутствии по умолчанию имеет значение **False** (всегда отображать команду). Это поведение задается отдельно для каждой команды (кроме команды "Открыть", для которой всегда используется значение **False**).|

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

[Программа просмотра изображений WPF с переходом, переносом и удалением](https://github.com/Microsoft/DesktopBridgeToUWP-Samples/tree/master/Samples/DesktopAppTransition)

<span id="open" />
### <a name="open-certain-types-of-files-directly-by-using-a-url"></a>Открытие определенных типов файлов напрямую с помощью URL-адреса

Вы можете убедиться, что по умолчанию пользователи работают с новым упакованным приложением (а не его классической версией) при открытии определенных типов файлов.

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
|Name |Уникальный идентификатор для вашего приложения. |
|UseUrl |Указывает, следует ли открывать файлы непосредственно из целевого URL-адреса. Если это значение не задано, попытки приложения открыть файл с помощью URL-адреса приведут к тому, что система сначала скачает этот файл локально. |
|Parameters |Дополнительные параметры. |
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

* [Создание исключения брандмауэра для вашего приложения](#rules)

<span id="rules" />
### <a name="create-firewall-exception-for-your-app"></a>Создание исключения брандмауэра для вашего приложения

Если для вашего приложения требуется обмен информацией через порт, можно добавить приложение в список исключений брандмауэра.

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
|Executable |Имя исполняемого файла, который необходимо добавить в список исключений брандмауэра |
|Direction |Указывает направление правила подключения: входящее или исходящее |
|IPProtocol |Протокол взаимодействия |
|LocalPortMin |Наименьшее значение номера порта в диапазоне номеров локального порта. |
|LocalPortMax |Наибольшее значение номера порта в диапазоне номеров локального порта. |
|RemotePortMin |Наименьшее значение номера порта в диапазоне номеров удаленного порта. |
|RemotePortMax |Наибольшее значение номера порта в диапазоне номеров удаленного порта. |
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

## <a name="integrate-with-file-explorer"></a>Интеграция с проводником

Помогите пользователям упорядочить ваши файлы и работать с ними привычными способами.

* [Определение поведения приложения, когда пользователи выбирают и открывают несколько файлов одновременно](#define)
* [Отображение содержимого файла в эскизе в проводнике](#show)
* [Отображение содержимого файла в области предварительного просмотра в проводнике](#preview)
* [Предоставление пользователям возможности группировать файлы с помощью столбца "Вид" в проводнике](#enable)
* [Включение свойств файлов в поиск, индекс, диалоговые окна свойств и область сведений](#make-file-properties)

<span id="define" />
### <a name="define-how-your-app-behaves-when-users-select-and-open-multiple-files-at-the-same-time"></a>Определение поведения приложения, когда пользователи выбирают и открывают несколько файлов одновременно

Укажите поведение приложения, когда пользователь открывает несколько файлов одновременно.

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
|Name |Уникальный идентификатор для вашего приложения. |
|MultiSelectModel |См. ниже. |
|FileType |Соответствующие расширения файлов. |

**MultSelectModel**

Для упакованных классических приложений, как и для обычных классических приложений, имеется три варианта.

 * ``Player``: приложение активируется один раз. Все выбранные файлы передаются приложению в качестве параметров аргумента.
 * ``Single``: приложение активируется один раз для первого выбранного файла. Другие файлы игнорируются.
 * ``Document``: для каждого выбранного файла активируется новый отдельный экземпляр приложения.

 Можно установить разные настройки для разных типов файлов и действий. Например, вы можете решить открывать *документы* в режиме *Document*, а *изображения*— в режиме *Player*.

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

Если пользователь открывает 15 или менее файлов, значение по умолчанию для атрибута **MultiSelectModel**— *Player*. В противном случае значением по умолчанию является *Document*. Приложения UWP всегда запускаются в режиме *Player*.

<span id="show" />
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
            Clsid  ="[Clsid  ]"
            Cutoff="[Cutoff]"
            Treatment="[Treatment]" />
    </FileTypeAssociation>
</Extension>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Name |Уникальный идентификатор для вашего приложения. |
|FileType |Соответствующие расширения файлов. |
|Clsid   |Идентификатор класса для вашего приложения. |
|Cutoff |Минимальный размер значка, для которого используется эскиз. См. раздел [Кэш и размеры эскизов](https://msdn.microsoft.com/library/windows/desktop/cc144118.aspx#cache). |
|Treatment |[Оформление эскиза](https://msdn.microsoft.com/library/windows/desktop/cc144118.aspx#adornments) определяет внешний вид значка эскиза. |

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
              Clsid  ="20000000-0000-0000-0000-000000000001"
              Cutoff="20x20"
              Treatment="Video Sprockets" />
            </uap3:FileTypeAssociation>
         </uap::Extension>
      </Extensions>
    </Application>
  </Applications>
</Package>
```

<span id="preview" />
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
|Name |Уникальный идентификатор для вашего приложения. |
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

<span id="enable" />
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
|Name |Уникальный идентификатор для вашего приложения. |
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
<span id="make-file-properties" />
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
        <DesktopPropertyHandler Clsid ="[Clsid ]"/>
    </uap:FileTypeAssociation>
</uap:Extension>
```
**Описание основных элементов и атрибутов**

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Name |Уникальный идентификатор для вашего приложения. |
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

<span id="start" />
## <a name="start-your-app-in-different-ways"></a>Различные способы запуска приложения

* [Запуск приложения с помощью протокола](#protocol)
* [Запуск приложения с помощью псевдонима](#alias)
* [Запуск исполняемого файла при входе пользователей в Windows](#executable)
* [Автоматический перезапуск после получения обновления из Магазина Windows](#updates)

<span id="protocol" />
### <a name="start-your-app-by-using-a-protocol"></a>Запуск приложения с помощью протокола

Сопоставления протоколов позволяют другим программам и компонентам системы взаимодействовать с упакованным приложением. Когда ваше упакованное приложение запускается с помощью протокола, вы можете определить специальные параметры, которые передаются аргументам его события активации, что позволит приложению реагировать соответствующим образом. Параметры поддерживаются только для упакованных приложений с полным доверием. Приложения UWP не могут использовать параметры.  

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
|Name |Имя протокола. |
|Parameters |Список параметров и значений, передаваемых в приложение в качестве аргументов события при его активации. Если переменная может содержать путь к файлу, заключите значение этого параметра в кавычки. Это позволит избежать проблем, которые возникают в случаях, когда путь содержит пробелы. |

### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  IgnorableNamespaces="uap3">
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
<span id="alias" />
### <a name="start-your-app-by-using-an-alias"></a>Запуск приложения с помощью псевдонима

Пользователи и другие процессы могут использовать для запуска приложения псевдоним и не указывать полный путь к этому приложению. Вы можете указать имя этого псевдонима.

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
|Executable |Относительный путь к исполняемому файлу для запуска приложения при применении псевдонима. |
|Alias |Короткое имя для вашего приложения. Оно всегда должно завершаться расширением EXE. Для каждого приложения в пакете можно указать только один псевдоним выполнения приложения. Если несколько приложений зарегистрированы с одним и тем же псевдонимом, система будет активировать псевдоним, который был зарегистрирован последним, поэтому следует выбирать уникальный псевдоним, который не будет переопределен другими приложениями.
|

#### <a name="example"></a>Пример

```XML
<Package
  xmlns:uap3="http://schemas.microsoft.com/appx/manifest/uap/windows10/3"
  xmlns:desktop="http://schemas.microsoft.com/appx/manifest/desktop/windows10"
  IgnorableNamespaces="uap3, desktop">
  ...
  <uap3:Extension
        Category="windows.appExecutionAlias"
        Executable="exes\launcher.exe"
        EntryPoint="Windows.FullTrustApplication">
        <uap3:AppExecutionAlias>
            <desktop:ExecutionAlias Alias="Contoso.exe" />
        </uap3:AppExecutionAlias>
  </uap3:Extension>
...
</Package>
```

Полный справочник по схеме можно найти [здесь](https://docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap-filetypeassociation).

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fileTypeAssociation``.
|Name |Уникальный идентификатор для вашего приложения. Этот идентификатор предназначен для внутреннего использования и применяется для генерации хэшированного [программного идентификатора (ProgID)](https://msdn.microsoft.com/library/windows/desktop/cc144152.aspx), ассоциированного с вашим сопоставлением типов файлов. Вы можете использовать этот идентификатор для контроля изменений в будущих версиях своего приложения.   |
|FileType |Расширение файла, поддерживаемое вашим приложением. |

<span id="executable" />
### <a name="start-an-executable-file-when-users-log-into-windows"></a>Запуск исполняемого файла при входе пользователей в Windows

Задачи автозагрузки позволяют вашему приложению автоматически запускать исполняемый файл при входе пользователя.

> [!NOTE]
> Чтобы зарегистрировать эту задачу автозагрузки, пользователь должен открыть ваше приложение хотя бы один раз.

Приложение может объявить несколько задач автозагрузки. Каждая задача запускается независимо друг от друга. Все задачи автозагрузки отображаются в диспетчере задач во вкладке **Автозагрузка** и имеют значки вашего приложения и имена, указанные вами в манифесте приложения. Диспетчер задач автоматически анализирует влияние ваших задач на процесс запуска.

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
|Executable |Относительный путь к исполняемому файлу для запуска. |
|TaskId |Уникальный идентификатор для вашей задачи. Используя этот идентификатор, ваше приложение может вызывать функции API в классе [Windows.ApplicationModel.StartupTask](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.StartupTask), чтобы программно включать или отключать задачу автозагрузки. |
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

<span id="updates" />
### <a name="restart-automatically-after-receiving-an-update-from-the-windows-store"></a>Автоматический перезапуск после получения обновления из Магазина Windows

Если ваше приложение открыто, когда пользователи устанавливают обновление для него, приложение закрывается.

Если вы хотите, чтобы приложение перезапустилось после обновления, вызовите функцию [RegisterApplicationRestart](https://msdn.microsoft.com/library/windows/desktop/aa373347.aspx) в каждом процессе, который требуется перезапустить.

Каждое активное окно в приложении получает сообщение [WM_QUERYENDSESSION](https://msdn.microsoft.com/library/windows/desktop/aa376890.aspx). В этот момент приложение может вызвать функцию [RegisterApplicationRestart](https://msdn.microsoft.com/library/windows/desktop/aa373347.aspx) снова, чтобы при необходимости обновить командную строку.

Когда каждое активное окно приложения получает сообщение [WM_ENDSESSION](https://msdn.microsoft.com/library/windows/desktop/aa376889.aspx), приложение должно сохранить данные и завершить работу.

>[!NOTE]
Активные окна также получают сообщение [WM_CLOSE](https://msdn.microsoft.com/library/windows/desktop/ms632617.aspx) в случае, если приложение не обрабатывает сообщение [WM_ENDSESSION](https://msdn.microsoft.com/library/windows/desktop/aa376889.aspx).

В этот момент у приложения есть 30 секунд на закрытие своих процессов, либо платформа завершает их принудительно.

После завершения обновления приложение перезапускается.

## <a name="work-with-other-applications"></a>Работа с другими приложениями

Интеграция с другими приложениями, запуск других процессов или совместное использование информации.

* [Отображение вашего приложения в виде целевого объекта печати в приложениях, поддерживающих печать](#printing)
* [Совместное использование шрифтов с другими Windows-приложениями](#fonts)
* [Запуск процесса Win32 из приложения для универсальной платформы Windows (UWP)](#win32-process)

<span id="printing" />
### <a name="make-your-app-appear-as-the-print-target-in-applications-that-support-printing"></a>Отображение вашего приложения в виде целевого объекта печати в приложениях, поддерживающих печать

Когда пользователи хотят распечатать данные из другого приложения, например, Блокнота, можно отобразить ваше приложение в виде целевого объекта печати в списке доступных целевых объектов печати этого приложения.

Вам необходимо изменить ваше приложение, чтобы оно получало данные для печати в формате XML Paper Specification (XPS).

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
|Parameters |Любые параметры, которые требуются вашему приложению для правильной обработки запроса. |

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

<span id="fonts" />
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

Полный справочник по схеме можно найти [здесь](https://review.docs.microsoft.com/uwp/schemas/appxpackage/uapmanifestschema/element-uap4-sharedfonts).


|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.sharedFonts``.
|File |Файл, содержащий шрифты, которые вы хотите предоставить в общий доступ. |

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
<span id="win32-process" />
### <a name="start-a-win32-process-from-a-universal-windows-platform-uwp-app"></a>Запуск процесса Win32 из приложения для универсальной платформы Windows (UWP)

Запуск процесса Win32, который выполняется с полным доверием.

#### <a name="xml-namespaces"></a>Пространства имен XML

http://schemas.microsoft.com/appx/manifest/desktop/windows10

#### <a name="elements-and-attributes-of-this-extension"></a>Элементы и атрибуты этого расширения

```XML
<xtension Category="windows.fullTrustProcess" Executable="[executable file]">
  <FullTrustProcess>
    <ParameterGroup GroupId="[GroupID]" Parameters="[Parameters]"/>
  </FullTrustProcess>
</Extension>
```

|Имя |Описание |
|-------|-------------|
|Category |Всегда ``windows.fullTrustProcess``.
|GroupID |Строка, определяющая набор параметров, которые необходимо передать в исполняемый файл. |
|Parameters |Параметры, которые необходимо передать в исполняемый файл. |

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
Это расширение может быть полезным, если требуется создать пользовательский интерфейс универсальной платформы Windows, работающий на всех устройствах, но при этом необходимо, чтобы компоненты вашего приложения Win32 продолжали работать с полным доверием.

Просто создайте пакет моста для классических приложений для вашего приложения Win32. Затем добавьте это расширение в файл пакета вашего приложения UWP. Это расширение указывает, что исполняемый файл необходимо запускать в пакете моста для классических приложений.  Если вы хотите обмениваться данными между вашими приложениями UWP и Win32, для этого можно настроить одну или несколько [служб приложений](../launch-resume/app-services.md). Подробнее об этом сценарии см. [здесь](https://blogs.msdn.microsoft.com/appconsult/2016/12/19/desktop-bridge-the-migrate-phase-invoking-a-win32-process-from-a-uwp-app/).

## <a name="next-steps"></a>Следующие шаги

**Поиск ответов на конкретные вопросы**

Наша команда следит за этими [тегами StackOverflow](http://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge).

**Оставьте отзыв об этой статье**

Используйте раздел комментариев ниже.
