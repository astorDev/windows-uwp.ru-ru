---
Description: In this scenario, we'll make a new app to represent our custom build system. We'll create a resource indexer and add strings and other kinds of resources to it. Then we'll generate and dump a PRI file.
title: Сценарий 1. Создание PRI-файла из строковых ресурсов и файлов ресурсов
template: detail.hbs
ms.date: 05/07/2018
ms.topic: article
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: 9b14e413a5629dfb5447750e32c42c4efafef8fa
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/30/2018
ms.locfileid: "8211062"
---
# <a name="scenario-1-generate-a-pri-file-from-string-resources-and-asset-files"></a>Сценарий 1. Создание PRI-файла из строковых ресурсов и файлов ресурсов
В этом сценарии мы будем использовать [API-интерфейсы индексирования ресурсов пакета (PRI)](https://msdn.microsoft.com/library/windows/desktop/mt845690), чтобы новое приложение представляло нашу систему сборки. Цель этой пользовательской системы сборки, как вы помните,— создание PRI-файлов для целевого приложения UWP. Итак, в рамках этого пошагового руководства мы создадим файлы ресурсов для представления ресурсов целевого приложения UWP (содержащих строки и другие типы ресурсов).

## <a name="new-project"></a>Новый проект
Начните с создания нового проекта в Microsoft Visual Studio. Создайте проект **Консольное приложение Visual C++ для Windows** и назовите его *CBSConsoleApp* (для "консольного приложения пользовательской системы сборки").

Выберите *x64* из раскрывающегося списка **Платформы решений**.

## <a name="headers-static-library-and-dll"></a>Заголовки, статическая библиотека и DLL
API-интерфейсы PRI объявляются в файле заголовка MrmResourceIndexer.h (который устанавливается на `%ProgramFiles(x86)%\Windows Kits\10\Include\<WindowsTargetPlatformVersion>\um\`). Откройте файл `CBSConsoleApp.cpp` и добавьте заголовок, а также другие заголовки, которые вам понадобятся.

```cppwinrt
#include <string>
#include <windows.h>
#include <MrmResourceIndexer.h>
```

API-интерфейсы реализуются в библиотеке MrmSupport.dll, для доступа к которой используется привязка к статической библиотеке MrmSupport.lib. Откройте **свойства** вашего проекта, щелкните **Компоновщик** > **Ввод**, измените свойство **AdditionalDependencies** и добавьте `MrmSupport.lib`.

Выполните построение решения, а затем скопируйте `MrmSupport.dll` из `C:\Program Files (x86)\Windows Kits\10\bin\<WindowsTargetPlatformVersion>\x64\` в выходную папку сборки (возможно, `C:\Users\%USERNAME%\source\repos\CBSConsoleApp\x64\Debug\`).

Добавьте следующую вспомогательную функцию для `CBSConsoleApp.cpp`, так как она нам потребуется.

```cppwinrt
inline void ThrowIfFailed(HRESULT hr)
{
    if (FAILED(hr))
    {
        // Set a breakpoint on this line to catch Win32 API errors.
        throw new std::exception();
    }
}
```

Добавьте вызовы для инициализации и отмены инициализации COM в функцию `main()`.

```cppwinrt
int main()
{
    ::ThrowIfFailed(::CoInitializeEx(nullptr, COINIT_MULTITHREADED));
    
    // More code will go here.
    
    ::CoUninitialize();
}
```

## <a name="resource-files-belonging-to-the-target-uwp-app"></a>Файлы ресурсов, связанные с целевым приложением UWP
Сейчас мы создадим файлы ресурсов для представления ресурсов целевого приложения UWP (содержащих строки и другие типы ресурсов). Они, конечно, могут быть расположены в любом месте в файловой системе. Однако для этого пошагового руководства удобнее поместить их в папку проекта CBSConsoleApp чтобы все было в одном месте. Вам необходимо только добавить эти файлы ресурсов в файловую систему, не добавляйте их в проект CBSConsoleApp.

В ту же папке, содержащую `CBSConsoleApp.vcxproj`, добавьте новую вложенную папку `UWPAppProjectRootFolder`. Внутри новой вложенной папки создайте эти файлы ресурсов.

### <a name="uwpappprojectrootfoldersample-imagepng"></a>\UWPAppProjectRootFolder\sample-image.png
Этот файл может содержать любые изображения в формате PNG.

### <a name="uwpappprojectrootfolderresourcesresw"></a>\UWPAppProjectRootFolder\resources.resw
```xml
<?xml version="1.0"?>
<root>
    <data name="LocalizedString1">
        <value>LocalizedString1-neutral</value>
    </data>
    <data name="LocalizedString2">
        <value>LocalizedString2-neutral</value>
    </data>
    <data name="NeutralOnlyString">
        <value>NeutralOnlyString-neutral</value>
    </data>
</root>
```

### <a name="uwpappprojectrootfolderde-deresourcesresw"></a>\UWPAppProjectRootFolder\de-DE\resources.resw
```xml
<?xml version="1.0"?>
<root>
    <data name="LocalizedString2">
        <value>LocalizedString2-de-DE</value>
    </data>
</root>
```

### <a name="uwpappprojectrootfolderen-usresourcesresw"></a>\UWPAppProjectRootFolder\en-US\resources.resw
```xml
<?xml version="1.0"?>
<root>
    <data name="LocalizedString1">
        <value>LocalizedString1-en-US</value>
    </data>
    <data name="EnOnlyString">
        <value>EnOnlyString-en-US</value>
    </data>
</root>
```

## <a name="index-the-resources-and-create-a-pri-file"></a>Индексирование ресурсов и создание PRI-файла
В функции `main()` перед вызовом для инициализации COM объявите некоторые строки, которые нам потребуются, а также создавать выходную папку, в которой мы создадим наш PRI-файл.

```cppwinrt
std::wstring projectRootFolderUWPApp{ L"UWPAppProjectRootFolder" };
std::wstring generatedPRIsFolder{ projectRootFolderUWPApp + L"\\Generated PRIs" };
std::wstring filePathPRI{ generatedPRIsFolder + L"\\resources.pri" };
std::wstring filePathPRIDumpBasic{ generatedPRIsFolder + L"\\resources-pri-dump-basic.xml" };

::CreateDirectory(generatedPRIsFolder.c_str(), nullptr);
```

Сразу после вызова для инициализации COM объявите дескриптор индексатора ресурса, а затем вызовите метод [**MrmCreateResourceIndexer**]() для создания индексатора ресурсов.

```cppwinrt
MrmResourceIndexerHandle indexer;
::ThrowIfFailed(::MrmCreateResourceIndexer(
    L"OurUWPApp",
    projectRootFolderUWPApp.c_str(),
    MrmPlatformVersion::MrmPlatformVersion_Windows10_0_0_0,
    L"language-en_scale-100_contrast-standard",
    &indexer));
```

Здесь приведено описание аргументов, передаваемых методу **MrmCreateResourceIndexer**.

- Имя семейства пакетов нашего целевого приложения UWP, которое будет использоваться как имя схемы ресурсов при создании PRI-файла из индексатора ресурсов позднее.
- Корень проекта целевого приложения UWP. Другими словами, это путь к файлам ресурсов. Мы указываем, чтобы затем можно было определить относительные пути к этой корневой папке в последующих вызовах API того же индексатора ресурсов.
- Целевая версия Windows.
- Список квалификаторов ресурсов по умолчанию.
- Указатель на дескриптор индексатора ресурсов, с помощью которой эта функция может настроить дескриптор.

Следующий шаг— добавление ресурсов в созданный индексатор ресурса. `resources.resw` — это файл ресурсов (.resw), содержащий нейтральные строки для целевого приложения UWP. Прокрутите экран (в этом разделе), если вы хотите просмотреть его содержимое. `de-DE\resources.resw` содержит строки на немецком языке, а `en-US\resources.resw`— на английском языке. Чтобы добавить строковые ресурсы из файла ресурсов в индексатор ресурсов, вызовите метод [**MrmIndexResourceContainerAutoQualifiers**](). В-третьих, мы вызываем функцию [**MrmIndexFile**]() для файла, содержащего нейтральный ресурс изображения, и индексатора ресурсов.

```cppwinrt
::ThrowIfFailed(::MrmIndexResourceContainerAutoQualifiers(indexer, L"resources.resw"));
::ThrowIfFailed(::MrmIndexResourceContainerAutoQualifiers(indexer, L"de-DE\\resources.resw"));
::ThrowIfFailed(::MrmIndexResourceContainerAutoQualifiers(indexer, L"en-US\\resources.resw"));
::ThrowIfFailed(::MrmIndexFile(indexer, L"ms-resource:///Files/sample-image.png", L"sample-image.png", L""));
```

В вызове функции **MrmIndexFile** значение L"ms-resource:///Files/sample-image.png"— это URI ресурса. Первый сегмент пути — это "Files", и он будет использоваться как имя поддерева схемы ресурсов при создании PRI-файла из этого ресурса индексатора позднее.

После опроса индексатора ресурсов о файлах ресурсов мы создадим PRI-файл на диске, вызвав функцию [**MrmCreateResourceFile**]().

```cppwinrt
::ThrowIfFailed(::MrmCreateResourceFile(indexer, MrmPackagingModeStandaloneFile, MrmPackagingOptionsNone, generatedPRIsFolder.c_str()));
```

После этого PRI-файл с именем `resources.pri` будет создан в папке с именем `Generated PRIs`. После работы с индексатором ресурсов мы вызываем функцию [**MrmDestroyIndexerAndMessages**](), чтобы удалить дескриптор и освободить все выделенные ресурсы.

```cppwinrt
::ThrowIfFailed(::MrmDestroyIndexerAndMessages(indexer));
```

Так как PRI-файл— двоичный файл, будет проще просмотреть то, что мы создали, если мы создадим дамп двоичного PRI-файла в эквивалентом XML-файле. Для этого мы вызываем [**MrmDumpPriFile**]().

```cppwinrt
::ThrowIfFailed(::MrmDumpPriFile(filePathPRI.c_str(), nullptr, MrmDumpType::MrmDumpType_Basic, filePathPRIDumpBasic.c_str()));
```

Здесь приведено описание аргументов, передаваемых методу **MrmDumpPriFile**.

- Путь к PRI-файлу, дамп которого требуется создать. Мы не используем индексатор ресурсов в этом вызове (мы только что удалили его), поэтому нам необходимо указать полный путь к файлу.
- Файл схемы отсутствует. Мы обсудим схему далее в этом разделе.
- Только базовые сведения.
- Путь к создаваемому XML-файлу.

Вот что содержит PRI-файл, дамп которого был добавлен в XML-файл.

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<PriInfo>
    <ResourceMap name="OurUWPApp" version="1.0" primary="true">
        <Qualifiers>
            <Language>en-US,de-DE</Language>
        </Qualifiers>
        <ResourceMapSubtree name="Files">
            <NamedResource name="sample-image.png" uri="ms-resource://OurUWPApp/Files/sample-image.png">
                <Candidate type="Path">
                    <Value>sample-image.png</Value>
                </Candidate>
            </NamedResource>
        </ResourceMapSubtree>
        <ResourceMapSubtree name="resources">
            <NamedResource name="EnOnlyString" uri="ms-resource://OurUWPApp/resources/EnOnlyString">
                <Candidate qualifiers="Language-en-US" isDefault="true" type="String">
                    <Value>EnOnlyString-en-US</Value>
                </Candidate>
            </NamedResource>
            <NamedResource name="LocalizedString1" uri="ms-resource://OurUWPApp/resources/LocalizedString1">
                <Candidate qualifiers="Language-en-US" isDefault="true" type="String">
                    <Value>LocalizedString1-en-US</Value>
                </Candidate>
                <Candidate type="String">
                    <Value>LocalizedString1-neutral</Value>
                </Candidate>
            </NamedResource>
            <NamedResource name="LocalizedString2" uri="ms-resource://OurUWPApp/resources/LocalizedString2">
                <Candidate qualifiers="Language-de-DE" type="String">
                    <Value>LocalizedString2-de-DE</Value>
                </Candidate>
                <Candidate type="String">
                    <Value>LocalizedString2-neutral</Value>
                </Candidate>
            </NamedResource>
            <NamedResource name="NeutralOnlyString" uri="ms-resource://OurUWPApp/resources/NeutralOnlyString">
                <Candidate type="String">
                    <Value>NeutralOnlyString-neutral</Value>
                </Candidate>
            </NamedResource>
        </ResourceMapSubtree>
    </ResourceMap>
</PriInfo>
```

Данные начинаются со схемы ресурсов, которой присваивается имя семейства пакетов целевого приложения UWP. В схеме ресурса размещены два поддерева схемы ресурсов: одна для файловых ресурсов, которые мы проиндексировали, и другая для строковых ресурсов. Обратите внимание, что имя семейства пакетов вставлено во все URI ресурсов.

Первый строковый ресурс— это *EnOnlyString* из `en-US\resources.resw`, который содержит только одного кандидата (соответствующий квалификатору *language-en-US*). Далее следует *LocalizedString1* из `resources.resw` и `en-US\resources.resw`. Следовательно, он содержит два кандидата: соответствующий *language-en-US*и резервный нейтральный кандидат, соответствующий любому контексту. Аналогичным образом *LocalizedString2* содержит два кандидата: *language-de-DE*и нейтральный. И, наконец, *NeutralOnlyString* существует только в нейтральной форме. Мы присвоили это имя, чтобы пояснить, что его не следует переводить.

## <a name="summary"></a>Краткий обзор
В этом сценарии мы показали, как использовать [API-интерфейсы индексирования ресурсов пакетов (PRI)](https://msdn.microsoft.com/library/windows/desktop/mt845690) для создания индексатора ресурсов. Мы добавили строковые ресурсы и файлы ресурсов в индексатор ресурсов. Затем мы использовали индексатор ресурсов для созданного двоичного PRI-файла. И, наконец, мы создали дамп PRI-файла в формате XML, чтобы убедиться, что он содержит сведения, которые мы ожидали.

## <a name="important-apis"></a>Важные API
* [Справочник по индексированию ресурсов пакетов (PRI)](https://msdn.microsoft.com/library/windows/desktop/mt845690)

## <a name="related-topics"></a>Статьи по теме
* [API-интерфейса индексирования ресурсов пакета (PRI) и пользовательские системы сборки](pri-apis-custom-build-systems.md)
