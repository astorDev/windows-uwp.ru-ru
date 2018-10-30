---
author: stevewhims
Description: MakePri.exe has the set of commands createconfig, dump, new, resourcepack, and versioned. This topic details their use.
title: Параметры командной строки MakePRI.exe
template: detail.hbs
ms.author: stwhi
ms.date: 04/10/2018
ms.topic: article
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: c6c71cf98d9720cc5bfea219f565529b3308c49c
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5756790"
---
# <a name="makepriexe-command-line-options"></a>Параметры командной строки MakePRI.exe

[MakePri.exe](compile-resources-manually-with-makepri.md) имеет следующий набор команд: `createconfig`, `dump`, `new`, `resourcepack` и `versioned`. В данном разделе описаны параметры командной строки для их использования.

> [!NOTE]
> MakePri.exe устанавливается при проверке вариант **Windows SDK для управляемых приложений UWP** при установке пакета средств разработки программного обеспечения Windows. Он устанавливается на путь `%WindowsSdkDir%bin\<WindowsTargetPlatformVersion>\x64\makepri.exe` (а также в папки с именем для других архитектур). Например, `C:\Program Files (x86)\Windows Kits\10\bin\10.0.17713.0\x64\makepri.exe`.

## <a name="makepri-commands"></a>Команды MakePri

Запустите `MakePri.exe help`, чтобы увидеть все команды, которые можно использовать с MakePri.exe.

```
C:\>makepri help

Usage:
------
    MakePri.exe <command> [options]

Example:
--------
    MakePri.exe new /cf C:\MyApp\priconfig.xml /pr C:\MyApp\src\ /in PackageName

Description:
------------
    Creates, dumps, and performs utility functions on a PRI file. A PRI file is 
    an index of application resources, such as strings and image files.

Command Options:
----------------
    MakePri.exe createconfig   Creates a PRI config file for use with other
                               commands
    MakePri.exe dump           Dumps the contents of a PRI file
    MakePri.exe new            Creates a new PRI file from scratch
    MakePri.exe resourcepack   Creates a PRI file that contains additional
                               resource variants for a base PRI file
    MakePri.exe versioned      Creates a PRI file based on a previous version

Help:
-----
    MakePri.exe help           Show this help page
    MakePri.exe <command> /?   Shows detailed help for <command>

    For example,
    MakePri.exe createconfig /?
```

## <a name="createconfig-command"></a>Команда Createconfig

Команда `createconfig` создает новый инициализированный PRI файл конфигурации, определяющий заданные вами значения по умолчанию для квалификатора. Запустите `MakePri.exe createconfig /?`, чтобы увидеть подробную справку для этой команды.

```
C:\>makepri createconfig /?

Usage:
------
    MakePri.exe createconfig /cf <config file destination> /dq
    <default qualifiers> [options]

Example:
--------
    MakePri.exe createconfig /cf C:\MyApp\priconfig.xml /dq lang-en-US /o /pv 10.0.0

Description:
------------
    Creates a PRI configuration file at <config file destination> with default 
    qualifiers specified by <default qualifiers>. Multiple qualifiers are separated 
    by underscores (_)

Required Parameters:
--------------------
    /ConfigXml(cf)    : <FILEPATH> Configuration file output location
    /Default(dq)      : <QUALIFIERS> The default qualifiers to set in the
                        configuration file. A language qualifier is required

Options:
--------
    /ExtensionDll(ex) : <FILEPATH> Location of the Resource Management System
                        environment extension DLL. This DLL must be signed by
                        a Microsoft-issued certificate. Default is an empty path
                        (no DLL will be used)
    /Overwrite(o)     : Overwrite an existing output file of the same name
                        without prompting
    /Platform(pv)     : <VERSION> Platform version to use for generated
                        configuration file

    FILEPATH          - a path to a file, either relative to the current
                        directory or absolute
    QUALIFIERS        - a valid qualifier token
                        (for example, lang-en-US_scale-100_contrast-high)

Help:
-----
    /Help(h, ?)       : Display the usage help text
```

## <a name="dump-command"></a>Команда Dump

Команда `dump` выводит содержащий дамп XML-файл со списком всех ресурсов в указанном файле PRI. Запустите `MakePri.exe dump /?`, чтобы увидеть подробную справку для этой команды.

> [!NOTE]
> Пакет ресурсов без схемы был создан с помощью параметра *omitSchemaFromResourcePacks* в PRI-файле конфигурации. Чтобы создать дамп пакета ресурсов без схемы, используйте параметр `/es <main_package_PRI_file>`. Если основной файл не указан, появится сообщение об ошибке "*Файл resources.pri в пакете поврежден, что привело к сбою шифрования (ошибка PRI222: 0xdef0000f— произошла неизвестная ошибка)* ".

```
C:\>makepri dump /?

Usage:
------
    MakePri.exe dump [options]

Example:
--------
    MakePri.exe dump /if C:\MyApp\resources.pri /of C:\resources.pri.xml

Description:
------------
    Outputs a dumped xml file at <output file> containing a list of all 
    resources in <index file>.

Options:
--------
    /DumpType(dt)       : <STRING> Format of the dumped file, default is
                          Basic
    /ExtensionDll(ex)   : <FILEPATH> Location of the Resource Management System
                          environment extension DLL. This DLL must be signed by a
                          Microsoft-issued certificate. Default is an empty path
                          (no DLL will be used)
    /ExternalSchema(es) : <FILEPATH> Location of the external schema file
    /IndexFile(if)      : <FILEPATH> Location of the PRI file to dump from.
                          Default is .\resources.pri
    /OutputFile(of)     : <FILEPATH> Output location of the dump file, default
                          is .\[indexfile].xml
    /OutputOptions(oo)  : <OPTIONS> Options to provide detailed control over
                          contents of XML output files.
    /Overwrite(o)       : Overwrite an existing output file of the same name
                          without prompting
    /Verbose(v)         : Causes verbose messages to be output to the console

    Dump Type:
        Either 'Basic', 'Detailed', 'Schema', or 'Summary'

    FILEPATH            - a path to a file, either relative to the current
                          directory or absolute
Help:
-----
    /Help(h, ?)         : Display the usage help text
```

## <a name="new-command"></a>Команда New

Команда `new` создает новый файл PRI путем индексирования файлов в проекте согласно указаниям в файле конфигурации. Запустите `MakePri.exe new /?`, чтобы увидеть подробную справку для этой команды.

```
C:\>makepri new /?

Usage:
------
    MakePri.exe new /cf <config file> /pr <project root> [options]

Example:
--------
    MakePri.exe new /cf C:\MyApp\priconfig.xml /pr C:\MyApp\src\ 
    /mn C:\MyApp\AppXManifest.xml /o /of C:\MyApp\src\resources.pri

Description:
------------
    Creates a PRI file at <output file> by indexing all files in
    <project root> and its subdirectories as directed by <config file>. The
    index will be assigned <index name> to reference resources in the app

Required Parameters:
--------------------
    /ConfigXml(cf)    : <FILEPATH> Configuration file location. Use the
                        'Makepri.exe createconfig' command to generate one
    /ProjectRoot(pr)  : <FOLDERPATH> Root location of project files

Options:
--------
    /AutoMerge(am)    : This flag is not recommended for normal use with AppX
                        packages. It causes Makepri.exe to set the auto
                        merge flag within the PRI file. Default is not set.
    /ExtensionDll(ex) : <FILEPATH> Location of the Resource Management System
                        environment extension DLL. This DLL must be signed by
                        a Microsoft-issued certificate. Default is an empty path
                        (no DLL will be used)
    /IndexLog(il)     : <FILEPATH> XML Log of indexed resources, no file
                        generated by default
    /IndexName(in)    : <STRING> Name for the generated index of resources.
                        Typically matches the AppX package name, class library
                        simple name, etc. May be supplied via the
                        [manifest] parameter.
    /IndexOptions(io) : <OPTIONS> Options to provide detailed control over
                        behavior of resource indexers.
    /Manifest(mn)     : <FILEPATH> Location of the application or component's
                        manifest. This parameter is ignored if [indexname]
                        is given. Default is [projectroot]\AppXManifest.xml
    /MappingFile(mf)  : <MAPPINGFILETYPE> Generate a mapping file in the given
                        file format.
    /OutputFile(of)   : <FILEPATH> Output location of PRI file, default is
                        .\resources.pri
    /Overwrite(o)     : Overwrite an existing output file of the same name
                        without prompting
    /ReverseMap(rm)   : Generate a reverse mapping section in the PRI file
                        which can be used for debugging purposes.
    /SchemaFile(sf)   : <FILEPATH> Output location of XML resource schema
                        description.
    /Verbose(v)       : Causes verbose messages to be output to the console
    /VersionMajor(vma): <INTEGER> [Deprecated] Major version number for
                        index, default is 1

    FOLDERPATH        - a path to a folder
    FILEPATH          - a path to a file, either relative to the current
                        directory or absolute
    MAPPINGFILETYPE   - Supported File type(s): 'AppX'

Help:
-----
    /Help(h, ?)        : Display the usage help text
```

## <a name="resourcepack-command"></a>Команда ResourcePack

Команда `resourcepack` создает новый файл PRI путем индексирования файлов в проекте согласно указаниям в файле конфигурации. Файл PRI пакета ресурсов содержит только дополнительные варианты ресурсов, уже заданных в существующем файле PRI. Запустите `MakePri.exe resourcepack /?`, чтобы увидеть подробную справку для этой команды.

```
C:\>makepri resourcepack /?

Usage:
------
    MakePri.exe resourcepack /pr <project root> /cf <config file> [options]

Example:
--------
    MakePri.exe resourcepack /cf C:\MyAppEs\priconfig.xml /pr C:\MyAppEs\src\ 
    /if C:\MyApp\1.2\resources.pri /o /of C:\MyAppEs\resources.pri

Description:
------------
    Creates a PRI file at <output file> by indexing all files in 
    <project root> and its subdirectories as directed by <config file>. A 
    resource pack PRI file contains only additional variants of resources 
    already specified in <index file>.

Required Parameters:
--------------------
    /ConfigXml(cf)    : <FILEPATH> Configuration file location. Use
                        'Makepri.exe createconfig' command to generate one
    /ProjectRoot(pr)  : <FOLDERPATH> Root location of project files

Options:
--------
    /AutoMerge(am)    : This flag is not recommended for normal use with AppX
                        packages. It causes Makepri.exe to set the auto
                        merge flag within the PRI file. By default it is set
                        to same as the base PRI file.
    /ExtensionDll(ex) : <FILEPATH> Location of the Resource Management System
                        environment extension DLL. This DLL must be signed by
                        a Microsoft-issued certificate. Default is an empty path
                        (no DLL will be used)
    /IndexFile(if)    : <FILEPATH> Location of the base PRI or XML schema file.
                        Default is <ProjectRoot>\resources.pri
    /IndexLog(il)     : <FILEPATH> XML Log of indexed resources, no file
                        generated by default
    /IndexOptions(io) : <OPTIONS> Options to provide detailed control over
                        behavior of resource indexers.
    /MappingFile(mf)  : <MAPPINGFILETYPE> Generate a mapping file in the given
                        file format.
    /OutputFile(of)   : <FILEPATH> Output location of PRI file, default is
                        .\resources.pri
    /Overwrite(o)     : Overwrite an existing output file of the same name
                        without prompting
    /ReverseMap(rm)   : Generate a reverse mapping section in the PRI file
                        which can be used for debugging purposes.
    /SchemaFile(sf)   : <FILEPATH> Output location of XML resource schema
                        description.
    /Verbose(v)       : Causes verbose messages to be output to the console

    FOLDERPATH        - a path to a folder
    FILEPATH          - a path to a file, either relative to the current
                        directory or absolute
    MAPPINGFILETYPE   - Supported File type(s): 'AppX'

Help:
-----
    /Help(h, ?)        : Display the usage help text
```

## <a name="versioned-command"></a>Команда Versioned

Команда `versioned` создает версионный файл PRI путем индексирования файлов в проекте согласно указаниям в файле конфигурации. Запустите `MakePri.exe versioned /?`, чтобы увидеть подробную справку для этой команды.

```
C:\>makepri versioned /?

Usage:
------
    MakePri.exe versioned /cf <config file> /pr <project root> [options]

Example:
--------
    MakePri.exe versioned /cf C:\MyApp\priconfig.xml /pr C:\MyApp\src 
    /if C:\MyApp\1.2\resources.pri /o /of C:\MyApp\src\resources.pri /o

Description:
------------
    Creates a versioned PRI file at <output file> by indexing all files in 
    <project root> and its subdirectories as directed by <config file>.

Required Parameters:
--------------------
    /ConfigXml(cf)    : <FILEPATH> Configuration file location. Use
                        'Makepri.exe createconfig' command to generate one
    /ProjectRoot(pr)  : <FOLDERPATH> Root location of project files

Options:
--------
    /AutoMerge(am)    : This flag is not recommended for normal use with AppX
                        packages. It causes Makepri.exe to set the auto
                        merge flag within the PRI file. By default it is set
                        to same as the base PRI file.
    /ExtensionDll(ex) : <FILEPATH> Location of the Resource Management System
                        environment extension DLL. This DLL must be signed by
                        a Microsoft-issued certificate. Default is an empty path
                        (no DLL will be used)
    /IndexFile(if)    : <FILEPATH> Location of the base PRI or XML schema file
                        to version from. Default is <ProjectRoot>\resources.pri
    /IndexLog(il)     : <FILEPATH> XML Log of indexed resources, no file
                        generated by default
    /IndexOptions(io) : <OPTIONS> Options to provide detailed control over
                        behavior of resource indexers.
    /MappingFile(mf)  : <MAPPINGFILETYPE> Generate a mapping file in the given
                        file format.
    /OutputFile(of)   : <FILEPATH> Output location of PRI file, default is
                        [current directory]\resources.pri
    /Overwrite(o)     : Overwrite an existing output file of the same name
                        without prompting
    /ReverseMap(rm)   : Generate a reverse mapping section in the PRI file
                        which can be used for debugging purposes.
    /SchemaFile(sf)   : <FILEPATH> Output location of XML resource schema
                        description.
    /Verbose(v)       : Causes verbose messages to be output to the console

    FOLDERPATH        - a path to a folder
    FILEPATH          - a path to a file, either relative to the current
                        directory or absolute
    MAPPINGFILETYPE   - Supported File type(s): 'AppX'

Help:
-----
    /Help(h, ?)        : Display the usage help text
```

## <a name="47extensiondllex"></a>&#47;ExtensionDll(ex)

Используйте дополнительный DLL-параметр (/ex) с `createconfig`, `dump`, `new`, `resourcepack` и `versioned` для указания расположения DLL расширения среды Системы управления ресурсами.

## <a name="logging47metadata-file"></a>Logging&#47;файл метаданных

MakePri может включать сведения, относящиеся к пакету ресурсов, в файл метаданных индексатора. Ниже приведен пример файла журнала для `resources.pri` с файлами PRI ресурсов `german.pri` и `highresolution.pri`.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<root>
  <package filename="resources.pri">
    <instance itemname="Files\logo.jpg" qualifiers="Scale-100" src="" type="Path">
      <value>logo.scale-100.jpg</value>
    </instance>
    <instance itemname="resources\string2" qualifiers="Language-en-us" src="C:\Users\alias\Desktop\repro\app4\project\en-us\resources.resw" type="String">
      <value>value2</value>
    </instance>
    <instance itemname="resources\string1" qualifiers="Language-en-us" src="C:\Users\alias\Desktop\repro\app4\project\en-us\resources.resw" type="String">
      <value>value1</value>
    </instance>
  </package>
  <package filename="german.pri">
    <instance itemname="resources\string2" qualifiers="Language-de-de" src="C:\Users\alias\Desktop\repro\app4\project\de-de\resources.resw" type="String">
      <value>value2</value>
    </instance>
    <instance itemname="resources\string1" qualifiers="Language-de-de" src="C:\Users\alias\Desktop\repro\app4\project\de-de\resources.resw" type="String">
      <value>value1</value>
    </instance>
  </package>
  <package filename="highresolution.pri">
    <instance itemname="Files\logo.jpg" qualifiers="Scale-200" src="" type="Path">
      <value>logo.scale-200.jpg</value>
    </instance>
  </package>
</root>
```

## <a name="47indexfileif-option"></a>&#47;IndexFile(if) параметр

Используйте параметр файла указателя индекса (/if) с командами `dump`, `resourcepack` и `versioned`для указания входного PRI-файла.

Для `resourcepack` и `versioned` вместо предоставления файла PRI как входного параметра для /IndexFile(if) можно предоставить файл схемы.

```
/IndexFile(if) <FILEPATH>
```

**FILEPATH** является токеном, указывающим расположение входного PRI-файла или файла схемы PRI.

## <a name="47indexoptionsio-option"></a>& #47;IndexOptions(io) параметр

При использовании параметра параметры индекса (/ операций ввода-вывода) с `new`, `resourcepack`, и `versioned` указать параметры, которые предоставляют подробные контроль над поведением индексаторы для ресурса. По умолчанию отключены параметры индекса.

```
/IndexOptions(io) <OPTIONS>
```

**Параметры** — это разделенный запятыми список, состоящий из следующих вариантов.

- Положительным HiddenFiles(hf). Индекс (+) или игнорировать (-) скрытые файлы и папки.
- Положительным LinkedFiles(lf). Индекс (+) или игнорировать (-) связанные файлы и папки.

## <a name="47mappingfilemf-option"></a>&#47;MappingFile(mf) параметр

Используйте параметр файла сопоставления (/mf) с командами `new`, `resourcepack` и `versioned` для создания файла сопоставления. Средство [MakeAppx.exe](../packaging/create-app-package-with-makeappx-tool.md) использует файл сопоставления для создания пакетов приложений.

```
/MappingFile(mf) <MAPPINGFILETYPE>
```

**MAPPINGFILETYPE** — это токен, который указывает формат файла сопоставления. Поддерживается только формат `appx`.

```
/mf appx
```

Это пример содержимого главного файла сопоставления.

```
"ResourceDimensions"                   "language-de-de"
```

А это пример содержимого файла сопоставления пакета ресурсов.

```
"ResourceId"                           "Resources184.la5decaf08"
"ResourceDimensions"                   "language-de-de"
```

## <a name="output-summary"></a>Сводка вывода

Если создаются пакеты ресурсов, сводка вывода из MakePRI.exe будет более подробной. Вот пример.

```
Index Pass Completed: ResourcePackTests\TestApp_ResourcePack
Language Qualifiers: fr-FR, de-DE

Finished building
Version: 1.0
Resource Map Name: AppTest
Named Resources: 11

Resource PRI: fr-FR.pri
Version: 1.0
Resource Candidates: 4
Language: fr-FR

Resource PRI: de-DE.pri
Version: 1.0
Resource Candidates: 4
Language: de-DE

Output File(s) at TempTestResults
Successfully Completed
```

## <a name="47overwriteo-option"></a>&#47;Overwrite(o) параметр

Если параметр перезаписи (/o) не указан, а указанный выходной файл уже существует, MakePri.exe требует подтверждения перед перезаписью.

```
Following file(s) already exist at output location:
<file(s)>
Overwrite these file(s)? [Y]es (any other key to cancel):
```

## <a name="47outputfileof-option"></a>&#47;OutputFile(of) параметр

Используйте параметр файла выходных данных (/of) с командами `dump`, `new`, `resourcepack` и `versioned`, чтобы указать расположение и имя файла PRI, который следует создать. Если MakePri.exe создает несколько PRI-файлов ресурсов, они помещаются в родительскую папку конечного файла. Например, если указать `/of MyParentFolder\TargetFile.pri`, то MakePri.exe создаст `TargetFile.language-en.pri` и `TargetFile.scale-100.pri` вместе с `TargetFile.pri` в папке `ParentFolder`.

Вот пример ошибки и соответствующего сообщения об ошибке.

| Ошибка | Сообщение об ошибке |
| --------------- | ------------- |
| Имя выходного файла совпадает с одним из имен пакетов ресурсов в конфигурации. | Недопустимая конфигурация: имя пакета ресурсов <resource pack name> не должно совпадать с именем выходного файла <имя_выходного_файла.pri>. |

## <a name="reversemaprm-option"></a>Параметр /ReverseMap(RM)

Параметр обратного сопоставления (/rm) используется с командами `new`, `resourcepack` и `versioned`, чтобы создать раздел обратного сопоставления в файле PRI, который можно использовать для отладки.

## <a name="47schemafilesf-option"></a>&#47;SchemaFile(sf) параметр

Используйте параметр файла схемы (/sf) с командами `new`, `resourcepack` и `versioned` для записи файла схемы в указанном расположении.

Для `resourcepack` и `versioned` вместо предоставления файла PRI как входного параметра для /IndexFile(if) можно предоставить файл схемы.

```
/SchemaFile(sf) <FILEPATH>
```

**FILEPATH** — это токен, который указывает расположение, в котором будет записан файл схемы.

Вот пример файла схемы.

```xml
<PriInfo>
    <ResourceMap name="IndexName" resourceVersion="1.0"> 
        <ResourceMapSubtree name="Resources" index="1">
            <NamedResource name="String1" index="1"/>
            <NamedResource name="String2" index="1"/>
        </ResourceMapSubtree>
        <ResourceMapSubtree name="Files" index="2">
            <NamedResource name="logo.png" index="2"/>
            <ResourceMapSubtree name="images" index="3">
                <NamedResource name="success.png" index="3"/>
                <NamedResource name="error.png" index="3"/>
            </ResourceMapSubtree>
        </ResourceMapSubtree>
    </ResourceMap>
</PriInfo>
```

## <a name="47versionmajorvma-is-deprecated"></a>&#47;VersionMajor(vma) устарел

Параметр основной версии (/vma) (для команды `new`) считается устаревшим, и его использование приведет к появлению этого предупреждающего сообщения.

```
'VersionMajor (vma)' input parameter has been deprecated. Please specify major version in the configuration file using 'majorVersion' attribute on 'resources' node.
```

Вместо него используйте атрибут [resources@majorVersion](makepri-exe-configuration.md) в файле конфигурации для указания номера основной версии.

## <a name="related-topics"></a>Статьи по теме

* [MakePri.exe](compile-resources-manually-with-makepri.md)
