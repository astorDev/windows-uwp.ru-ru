---
author: laurenhughes
title: Создание пакета приложений с помощью средства MakeAppx.exe
description: MakeAppx.exe создает, шифрует, расшифровывает и извлекает файлы из пакетов приложения и пакетов приложений.
ms.author: lahugh
ms.date: 06/21/2018
ms.topic: article
keywords: windows 10, uwp, упаковка
ms.assetid: 7c1c3355-8bf7-4c9f-b13b-2b9874b7c63c
ms.localizationpriority: medium
ms.openlocfilehash: 1d5cc0d73975b591d7584b1ac606aa3323cd6da3
ms.sourcegitcommit: 70ab58b88d248de2332096b20dbd6a4643d137a4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "5925239"
---
# <a name="create-an-app-package-with-the-makeappxexe-tool"></a>Создание пакета приложения с помощью средства MakeAppx.exe


**MakeAppx.exe** создает как пакеты приложения, так и пакеты приложений. **MakeAppx.exe** также извлекает файлы из пакета приложения или пакета приложений и шифрует или расшифровывает пакеты приложения и пакеты приложений. Это средство входит в состав пакета SDK Windows 10, и его можно использовать через командную строку или файл сценария.

> [!IMPORTANT] 
> Если для разработки приложения использовали Visual Studio, рекомендуется применять мастер Visual Studio для создания пакета приложения. Дополнительные сведения см. в разделе [Упаковка приложения UWP с помощью Visual Studio](https://msdn.microsoft.com/windows/uwp/packaging/packaging-uwp-apps).

Обратите внимание, что **MakeAppx.exe** не создает файл .appxupload. Файл .appxupload создается в процессе упаковки Visual Studio и содержит два других файла: .msix или .appx и .appxsym. Файл .appxsym — это сжатый файл .pdb, содержащий общедоступные символы вашего приложения, используемые для [аналитики по сбоям](https://blogs.windows.com/buildingapps/2015/07/13/crash-analysis-in-the-unified-dev-center/) в Центре разработки для Windows. Можно также отправить обычный файл .appx, но аналитика по сбоям или сведения отладки не будут доступны. Подробнее об отправке пакетов в Store см. в разделе [Отправка пакетов приложений](https://msdn.microsoft.com/windows/uwp/publish/upload-app-packages). 

 Обновления этого средства в последней версии Windows 10 не влияют на использование пакета .appx. Можно продолжать использовать это средство с AppX-пакеты или использовать средство с поддержкой для пакетов, .msix, как описано ниже.

Создание файла .appxupload вручную:
- Поместите .msix и .appxsym в папку
- Упакуйте папку в ZIP-архив
- Измените расширение ZIP-архива с .zip на .appxupload

## <a name="using-makeappxexe"></a>Использование MakeAppx.exe

В зависимости от пути установки пакета SDK **MakeAppx.exe** может находиться в следующих расположениях на компьютере с Windows 10:
- x86: C:\Program Files (x86)\Windows Kits\10\bin\x86\makeappx.exe
- x64: C:\Program Files (x86)\Windows Kits\10\bin\x64\makeappx.exe

Версия ARM этого средства отсутствует.

### <a name="makeappxexe-syntax-and-options"></a>Синтаксис и параметры MakeAppx.exe

Общий синтаксис **MakeAppx.exe**:

``` Usage
MakeAppx <command> [options]      
```

В следующей таблице описаны команды для **MakeAppx.exe**.

| **Команда**   | **Описание**                       |
|---------------|---------------------------------------|
| pack          | Создает пакет.                    |
| unpack        | Извлекает все файлы в указанном пакете в указанный выходной каталог. |
| bundle        | Создает пакет приложений.                     |
| unbundle      | Распаковывает все пакеты в подкаталог по указанному выходному пути, названный полным именем пакета. |
| encrypt       | Создает зашифрованный пакет приложения или пакет приложений из входного пакета приложения/пакета приложений по указанному выходному пакету приложения/пакету приложений. |
| decrypt       | Создает расшифрованный пакет приложения или пакет приложений из входного пакета приложения/пакета приложений по указанному выходному пакету приложения/пакету приложений. |


Этот список параметров применим ко всем командам:

| **Параметр**    | **Описание**                       |
|---------------|---------------------------------------|
| /d            | Указывает входные или выходные данные либо каталог содержимого. |
| /l            | Используется для локализованных пакетов. Пути проверки по умолчанию для локализованных пакетов. Этот параметр отключает только указанную проверку, не требуя отключения всех проверок. |
| /kf           | Шифрует или расшифровывает пакет приложения или пакет приложений с помощью ключа из указанного файла ключа. Не может использоваться с /kt. |
| /kt           | Шифрует или расшифровывает пакет приложения или пакет приложений с помощью глобального тестового ключа. Не может использоваться с /kf. |
| /no           | Предотвращает перезапись выходного файла, если он существует. Если не указать этот параметр или параметр /o, пользователь должен подтвердить перезапись файла. |
| /nv           | Пропускает семантическую проверку. Если не указать этот параметр, средство выполняет полную проверку пакета. |
| /o            | Перезаписывает выходной файл, если он существует. Если не указать этот параметр или параметр /no, пользователь должен подтвердить перезапись файла. |
| /p            | Указывает пакет приложения или пакет приложений.  |
| /v            | Включает вывод подробных сведений в журнал на консоли. |
| /?            | Отображает текст справки.                   |


Следующий список содержит возможные аргументы:

| **Аргумент**                          | **Описание**                       |
|---------------------------------------|---------------------------------------|
| &lt;output package name&gt;           | Имя созданного пакета. Это имя файла с .msix или .appx. |
| &lt;encrypted output package name&gt; | Имя созданного зашифрованного пакета. Это имя файла с добавлением .eappx или .emsix. |
| &lt;input package name&gt;            | Имя пакета. Это имя файла с .msix или .appx. |
| &lt;encrypted input package name&gt;  | Имя зашифрованного пакета. Это имя файла с добавлением .eappx или .emsix. |
| &lt;output bundle name&gt;            | Имя созданного пакета приложений. Это имя файла с .msixbundle или appxbundle. |
| &lt;encrypted output bundle name&gt;  | Имя созданного зашифрованного пакета приложений. Это имя файла с добавлением .eappxbundle или .emsixbundle. |
| &lt;input bundle name&gt;             | Имя пакета приложений. Это имя файла с .msixbundle или appxbundle. |
| &lt;encrypted input bundle name&gt;   | Имя зашифрованного пакета приложений. Это имя файла с добавлением .eappxbundle или .emsixbundle. |
| &lt;content directory&gt;             | Путь для содержимого пакета приложения или пакета приложений. |
| &lt;mapping file&gt;                  | Имя файла, в котором указаны источник и назначение пакета. |
| &lt;output directory&gt;              | Путь к каталогу для выходных пакетов приложения и пакетов приложений. |
| &lt;key file&gt;                      | Имя файла, содержащего ключ для шифрования или расшифровки. |
| &lt;algorithm ID&gt;                  | Алгоритмы, используемые при создании сопоставления блоков. Допустимые алгоритмы: SHA256 (по умолчанию), SHA384, SHA512. |


### <a name="create-an-app-package"></a>Создание пакета приложения

Пакет приложения — это полный набор файлов приложения, упакованный в файл пакета .appx или .msix. Чтобы создать пакет приложения с помощью команды **pack**, необходимо предоставить каталог содержимого или файл сопоставления для расположения пакета. Вы также можете зашифровать пакет во время создания. Если вам требуется зашифровать пакет, необходимо использовать параметр /ep и указать, используете ли вы файл ключа (/kf) или глобальный тестовый ключ (/kt). Подробнее о создании зашифрованного пакета см. в разделе [Шифрование и расшифровка пакета приложения или пакета приложений](#encrypt-or-decrypt-a-package-or-bundle).

Параметры, относящиеся к команде **pack**:

| **Параметр**    | **Описание**                       |
|---------------|---------------------------------------|
| /f            | Указывает файл сопоставления.           |
| /h            | Указывает хэш-алгоритм, используемый при создании сопоставления блоков. Этот параметр можно использовать только с командой pack. Допустимые алгоритмы: SHA256 (по умолчанию), SHA384, SHA512. |
| /m            | Указывает путь к входному манифесту приложения, на основе которого будет создан выходной пакет приложения или манифест пакета ресурсов.  При использовании этого параметра необходимо также использовать параметр /f и включить раздел [ResourceMetadata] в файл сопоставления, чтобы указать размеры ресурсов, которые будут включены в созданный манифест.|
| /nc           | Предотвращает сжатие файлов пакета. По умолчанию файлы сжимаются в зависимости от выявленного типа файлов. |
| /r            | Собирает пакет ресурсов. Этот параметр необходимо использовать с параметром /m, а также он предполагает использование параметра /l. |  


В следующих примерах использования показаны некоторые возможные варианты синтаксиса для команды **pack**:

``` syntax 
MakeAppx pack [options] /d <content directory> /p <output package name>
MakeAppx pack [options] /f <mapping file> /p <output package name>
MakeAppx pack [options] /m <app package manifest> /f <mapping file> /p <output package name>
MakeAppx pack [options] /r /m <app package manifest> /f <mapping file> /p <output package name>
MakeAppx pack [options] /d <content directory> /ep <encrypted output package name> /kf <key file>
MakeAppx pack [options] /d <content directory> /ep <encrypted output package name> /kt

```
Далее приведены примеры командной строки для команды **pack**:

``` examples
MakeAppx pack /v /h SHA256 /d "C:\My Files" /p MyPackage.msix
MakeAppx pack /v /o /f MyMapping.txt /p MyPackage.msix
MakeAppx pack /m "MyApp\AppxManifest.xml" /f MyMapping.txt /p AppPackage.msix
MakeAppx pack /r /m "MyApp\AppxManifest.xml" /f MyMapping.txt /p ResourcePackage.msix
MakeAppx pack /v /h SHA256 /d "C:\My Files" /ep MyPackage.emsix /kf MyKeyFile.txt
MakeAppx pack /v /h SHA256 /d "C:\My Files" /ep MyPackage.emsix /kt
```

### <a name="create-an-app-bundle"></a>Создание пакета приложений

Пакет приложений похож на пакет приложения, но он может сократить размер приложения, скачиваемого пользователями. Пакеты приложения полезны, к примеру, для ресурсов, связанных с конкретным языком, различных ресурсов с определенным масштабом изображений или ресурсов, которые применяются к конкретным версиям Microsoft DirectX. По аналогии с созданием зашифрованного пакета приложения, вы также можете зашифровать пакет приложений при его сборке. Чтобы зашифровать пакет приложений, используйте параметр /ep и укажите, используете ли вы файл ключа (/kf) или глобальный тестовый ключ (/kt). Подробнее о создании зашифрованного пакета приложений см. в разделе [Шифрование и расшифровка пакета приложения или пакета приложений](#encrypt-or-decrypt-a-package-or-bundle).

Параметры, относящиеся к команде **bundle**:

| **Параметр**    | **Описание**                       |
|---------------|---------------------------------------|
| /bv           | Указывает номер версии пакета. Номер версии должен состоять из четырех частей, разделенных точками, в следующей форме: &lt;Основная версия&gt;.&lt;Вспомогательная версия&gt;.&lt;Сборка&gt;.&lt;Редакция&gt;. |
| /f            | Указывает файл сопоставления.           |

Обратите внимание, что если версия пакета не указана или указана в виде "0.0.0.0", пакет создается с использованием текущих даты и времени.

В следующих примерах использования показаны некоторые возможные варианты синтаксиса для команды **bundle**:

``` syntax
MakeAppx bundle [options] /d <content directory> /p <output bundle name>
MakeAppx bundle [options] /f <mapping file> /p <output bundle name>
MakeAppx bundle [options] /d <content directory> /ep <encrypted output bundle name> /kf MyKeyFile.txt
MakeAppx bundle [options] /f <mapping file> /ep <encrypted output bundle name> /kt
```
В следующем блоке содержатся примеры для команды **bundle**:

``` examples
MakeAppx bundle /v /d "C:\My Files" /p MyBundle.msixbundle
MakeAppx bundle /v /o /bv 1.0.1.2096 /f MyMapping.txt /p MyBundle.msixbundle
MakeAppx bundle /v /o /bv 1.0.1.2096 /f MyMapping.txt /ep MyBundle.emsixbundle /kf MyKeyFile.txt
MakeAppx bundle /v /o /bv 1.0.1.2096 /f MyMapping.txt /ep MyBundle.emsixbundle /kt
```

### <a name="extract-files-from-a-package-or-bundle"></a>Извлечение файлов из пакета приложения или пакета приложений

Помимо упаковки приложений в пакеты приложения или пакеты приложений, **MakeAppx.exe** также может распаковывать существующие пакеты. Вам потребуется предоставить каталог содержимого в качестве назначения для извлеченных файлов. При попытке извлечь файлы из зашифрованного пакета приложения или пакета приложений вы можете расшифровать и извлечь файлы одновременно с помощью параметра /ep. При этом необходимо указать способ расшифровки: с помощью файла ключа (/kf) или глобального тестового ключа (/kt). Подробнее о расшифровке пакета приложения или пакета приложений см. в разделе [Шифрование и расшифровка пакета приложения или пакета приложений](#encrypt-or-decrypt-a-package-or-bundle).

Параметры, относящиеся к командам **unpack** и **unbundle**:

| **Параметр**    | **Описание**                       |
|---------------|---------------------------------------|
| /nd           | Не выполняет расшифровку при распаковке пакета приложения или пакета приложений. |
| /pfn          | Распаковывает все файлы в подкаталог по указанному выходному пути, названный полным именем пакета приложения или пакета приложений |

В следующих примерах использования показаны некоторые возможные варианты синтаксиса для команд **unpack** и **unbundle**:

``` syntax
MakeAppx unpack [options] /p <input package name> /d <output directory>
MakeAppx unpack [options] /ep <encrypted input package name> /d <output directory> /kf <key file>
MakeAppx unpack [options] /ep <encrypted input package name> /d <output directory> /kt

MakeAppx unbundle [options] /p <input bundle name> /d <output directory>
MakeAppx unbundle [options] /ep <encrypted input bundle name> /d <output directory> /kf <key file>
MakeAppx unbundle [options] /ep <encrypted input bundle name> /d <output directory> /kt
```

В следующем блоке содержатся примеры использования команд **unpack** и **unbundle**:

``` examples
MakeAppx unpack /v /p MyPackage.msix /d "C:\My Files"
MakeAppx unpack /v /ep MyPackage.emsix /d "C:\My Files" /kf MyKeyFile.txt
MakeAppx unpack /v /ep MyPackage.emsix /d "C:\My Files" /kt

MakeAppx unbundle /v /p MyBundle.msixbundle /d "C:\My Files"
MakeAppx unbundle /v /ep MyBundle.emsixbundle /d "C:\My Files" /kf MyKeyFile.txt
MakeAppx unbundle /v /ep MyBundle.emsixbundle /d "C:\My Files" /kt
```

### <a name="encrypt-or-decrypt-a-package-or-bundle"></a>Шифрование и расшифровка пакета приложения или пакета приложений

Средство **MakeAppx.exe** также может зашифровать или расшифровать существующий пакет приложения или пакет приложений. Просто укажите имя пакета, имя выходного пакета, а также способ шифрования или расшифровки: с помощью файла ключа (/kf) или глобального тестового ключа (/kt).

Шифрование и расшифровку нельзя выполнить через мастер упаковки Visual Studio. 

Параметры, относящиеся к командам **encrypt** и **decrypt**:

| **Параметр**    | **Описание**                       |
|---------------|---------------------------------------|
| /ep           | Указывает зашифрованный пакет приложения или пакет приложений. |

В следующих примерах использования показаны некоторые возможные варианты синтаксиса для команд **encrypt** и **decrypt**:

``` syntax
MakeAppx encrypt [options] /p <package name> /ep <output package name> /kf <key file>
MakeAppx encrypt [options] /p <package name> /ep <output package name> /kt

MakeAppx decrypt [options] /ep <package name> /p <output package name> /kf <key file>
MakeAppx decrypt [options] /ep <package name> /p <output package name> /kt
```

Следующий блок содержит примеры использования команд **encrypt** и **decrypt**:

``` examples
MakeAppx.exe encrypt /p MyPackage.msix /ep MyEncryptedPackage.emsix /kt
MakeAppx.exe encrypt /p MyPackage.msix /ep MyEncryptedPackage.emsix /kf MyKeyFile.txt

MakeAppx.exe decrypt /p MyPackage.msix /ep MyEncryptedPackage.emsix /kt
MakeAppx.exe decrypt p MyPackage.msix /ep MyEncryptedPackage.emsix /kf MyKeyFile.txt
```

## <a name="key-files"></a>Файлы ключей

Файлы ключей должны начинаться строкой, содержащей строку "[Keys]", за которой следуют строки с описанием ключей, используемых для шифрования каждого пакета. Каждый ключ представлен парой строк в кавычках, разделенных пробелами или табуляцией. Первая строка представляет собой идентификатор 32-байтового ключа в кодировке base64, а вторая — 32-байтовый ключ шифрования в кодировке base64. Файл ключа должен быть простым текстовым файлом.

Пример файла ключа:

``` Example
[Keys]
"OWVwSzliRGY1VWt1ODk4N1Q4R2Vqc04zMzIzNnlUREU="    "MjNFTlFhZGRGZEY2YnVxMTBocjd6THdOdk9pZkpvelc="
```

## <a name="mapping-files"></a>Файлы сопоставления
Файлы сопоставления должны начинаться строкой, содержащей строку "[Files]", за которой следуют строки с описанием файлов, которые будут добавлены в пакет. Каждый файл описывается парой путей в кавычках, разделенных пробелами или табуляцией. Каждый файл представляет свой источник (на диске) и назначение (в пакете). Файл сопоставления должен быть простым текстовым файлом.

Пример файла сопоставления (без параметра /m):

``` Example
[Files]
"C:\MyApp\StartPage.html"               "default.html"
"C:\Program Files (x86)\example.txt"    "misc\example.txt"
"\\MyServer\path\icon.png"              "icon.png"
"my app files\readme.txt"               "my app files\readme.txt"
"CustomManifest.xml"                    "AppxManifest.xml"
``` 

При использовании файла сопоставления вы можете выбрать, следует ли использовать параметр /m. Параметр /m позволяет пользователю указывать метаданные ресурса в файле сопоставления, которые будут включены в созданный манифест. Если вы используете параметр /m, файл сопоставления должен содержать раздел, начинающийся строкой "[ResourceMetadata]", за которой следуют строки с указанием "ResourceDimensions" и "ResourceId." Пакет приложения может содержать несколько "ResourceDimensions", но всегда только один "ResourceId".

Пример файла сопоставления (с параметром /m):

``` Example
[ResourceMetadata]
"ResourceDimensions"                    "language-en-us"
"ResourceId"                            "English"

[Files]
"images\en-us\logo.png"                 "en-us\logo.png"
"en-us.pri"                             "resources.pri"
```

## <a name="semantic-validation-performed-by-makeappxexe"></a>Выполнение семантической проверки средством MakeAppx.exe

**MakeAppx.exe** выполняет ограниченную семантическую проверку, которая предназначена для выявления наиболее распространенных ошибок разработки и помогает обеспечить допустимость пакета приложения. Если вам требуется пропустить проверку при использовании **MakeAppx.exe**, см. параметр /nv. 

Эта проверка гарантирует следующее:
- все файлы, ссылки на которые присутствуют в манифесте пакета, входят в состав пакета приложения;
- в приложении нет двух идентичных ключей;
- приложение не регистрирует запрещенный протокол из этого списка: SMB, FILE, MS-WWA-WEB, MS-WWA. 

Это не полноценная семантическая проверка, так как она позволяет выявлять только распространенные ошибки. Готовность к установке пакетов, созданных с помощью **MakeAppx.exe**, не гарантируется.