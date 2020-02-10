---
Description: 'MakePri.exe имеет следующий набор команд: createconfig, dump, new, resourcepack и versioned. В этом разделе описывается их использование.'
title: Параметры командной строки MakePRI.exe
template: detail.hbs
ms.date: 04/10/2018
ms.topic: article
keywords: Windows 10, uwp, ресурс, изображение, актив, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: 638de4d2795b3b248edfdcc35dc72fa21d31fc3b
ms.sourcegitcommit: 3e7a4f7605dfb4e87bac2d10b6d64f8b35229546
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089328"
---
# <a name="makepriexe-command-line-options"></a>Параметры командной строки MakePRI.exe

[MakePri.exe](compile-resources-manually-with-makepri.md) имеет следующий набор команд: `createconfig`, `dump`, `new`, `resourcepack` и `versioned`. В данном разделе описаны параметры командной строки для их использования.

> [!NOTE]
> Передаваемые программе MakePRI. exe устанавливается при установке пакета средств разработки программного обеспечения Windows в параметрах **Windows SDK для приложений под управлением UWP** . Он устанавливается по пути `%WindowsSdkDir%bin\<WindowsTargetPlatformVersion>\x64\makepri.exe` (а также в папках с именами для других архитектур). Например, `C:\Program Files (x86)\Windows Kits\10\bin\10.0.17713.0\x64\makepri.exe`.

## <a name="getting-help-from-the-command-line"></a>Получение справки из командной строки

Вы можете запустить `MakePri.exe help` или `MakePri.exe /?`, чтобы просмотреть команды, которые можно использовать с передаваемые программе MakePRI. exe. Вы также можете выдать `MakePri.exe <command> /?`, чтобы просмотреть конкретные сведения о команде и, в редких случаях, даже `MakePri.exe <command> <option>`, чтобы увидеть конкретные сведения о параметре.

## <a name="makepri-commands"></a>Команды MakePri

```console
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

```console
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
> Пакет ресурсов без схемы был создан с помощью параметра *omitSchemaFromResourcePacks* в PRI-файле конфигурации. Чтобы создать дамп пакета ресурсов без схемы, используйте параметр `/es <main_package_PRI_file>`. Если основной файл не указан, появится сообщение об ошибке "*Файл resources.pri в пакете поврежден, что привело к сбою шифрования (ошибка PRI222: 0xdef0000f — произошла неизвестная ошибка)* ".

```console
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

```console
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
    /AutoMerge(am)    : This flag is not recommended for normal use with .appx
                        packages. It causes Makepri.exe to set the auto
                        merge flag within the PRI file. Default is not set.
    /ExtensionDll(ex) : <FILEPATH> Location of the Resource Management System
                        environment extension DLL. This DLL must be signed by
                        a Microsoft-issued certificate. Default is an empty path
                        (no DLL will be used)
    /IndexLog(il)     : <FILEPATH> XML Log of indexed resources, no file
                        generated by default
    /IndexName(in)    : <STRING> Name for the generated index of resources.
                        Typically matches the .appx package name, class library
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

```console
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
    /AutoMerge(am)    : This flag is not recommended for normal use with .appx
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

```console
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
    /AutoMerge(am)    : This flag is not recommended for normal use with .appx
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

```console
/IndexFile(if) <FILEPATH>
```

**FILEPATH** является токеном, указывающим расположение входного PRI-файла или файла схемы PRI.

## <a name="47indexoptionsio-option"></a>&#47;Индексоптионс (IO), параметр

Для указания параметров, обеспечивающих детальный контроль над поведением индексаторов ресурсов, используется параметр индекса (/ИО) с параметрами `new`, `resourcepack`и `versioned`. По умолчанию параметры индекса отключены.

```console
/IndexOptions(io) <OPTIONS>
```

**Параметры** — это разделенный запятыми список, состоящий из следующих параметров.

- +/-Хидденфилес (HF). Индексировать (+) или игнорировать (-) скрытые файлы и папки.
- +/-Линкедфилес (LF). Индексирование (+) или игнорирование (-) связанных файлов и папок.

## <a name="47mappingfilemf-option"></a>&#47;MappingFile(mf) параметр

Используйте параметр файла сопоставления (/mf) с командами `new`, `resourcepack` и `versioned` для создания файла сопоставления. Средство [MakeAppx.exe](/windows/msix/package/create-app-package-with-makeappx-tool) использует файл сопоставления для создания пакетов приложений.

```console
/MappingFile(mf) <MAPPINGFILETYPE>
```

**MAPPINGFILETYPE** — это токен, который указывает формат файла сопоставления. Поддерживается только формат `appx`.

```console
/mf appx
```

Это пример содержимого главного файла сопоставления.

```console
"ResourceDimensions"                   "language-de-de"
```

А это пример содержимого файла сопоставления пакета ресурсов.

```console
"ResourceId"                           "Resources184.la5decaf08"
"ResourceDimensions"                   "language-de-de"
```

## <a name="output-summary"></a>Сводка вывода

Если создаются пакеты ресурсов, сводка вывода из MakePRI.exe будет более подробной. Рассмотрим пример.

```console
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

```console
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

```console
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

```console
'VersionMajor (vma)' input parameter has been deprecated. Please specify major version in the configuration file using 'majorVersion' attribute on 'resources' node.
```

Вместо него используйте атрибут [resources@majorVersion](makepri-exe-configuration.md) в файле конфигурации для указания номера основной версии.

## <a name="related-topics"></a>Связанные разделы

* [Передаваемые программе MakePRI. exe](compile-resources-manually-with-makepri.md)
