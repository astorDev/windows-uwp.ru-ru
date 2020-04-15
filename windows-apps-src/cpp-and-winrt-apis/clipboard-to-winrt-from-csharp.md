---
title: Перенос примера буфера обмена в C++/WinRT из C# (пример использования)
description: В этой статье рассматривается пример переноса одного [примера приложения универсальной платформы Windows (UWP)](https://github.com/microsoft/Windows-universal-samples) из [C#](/visualstudio/get-started/csharp) в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt).
ms.date: 03/20/2020
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, проекция, перенос, миграция, C#, пример, буфер обмена, пример, исследование
ms.localizationpriority: medium
ms.openlocfilehash: 570f3538bf15616a45a17cdbce9a56066c8036bc
ms.sourcegitcommit: 23c5d8dfaeb6edbca780637ffd26fe892db27519
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123649"
---
# <a name="porting-the-clipboard-sample-tocwinrtfromcmdasha-case-study"></a>Перенос примера буфера обмена в C++/WinRT из C#&mdash; пример использования

В этой статье рассматривается пример переноса одного [примера приложения универсальной платформы Windows (UWP)](https://github.com/microsoft/Windows-universal-samples) из [C#](/visualstudio/get-started/csharp) в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt). Вы можете получить рекомендации и действия по переносу, следуя пошаговому руководству и выполняя перенос образца.

См. также статью [Переход на C++/WinRT с C#](/windows/uwp/cpp-and-winrt-apis/move-to-winrt-from-csharp), в которой представлен ряд разделов, в которых рассматриваются конкретные технические сведения, связанные с переносом с C# на C++/WinRT.

## <a name="download-and-test-the-clipboard-sample"></a>Загрузка и тестирование примера буфера обмена

Перейдите на веб-страницу [примера буфера обмена](https://docs.microsoft.com/samples/microsoft/windows-universal-samples/clipboard/) и щелкните **Скачать ZIP-файл**. Распакуйте скачанный файл и взгляните на структуру папок.

- Версия C# исходного кода примера содержится в папке с именем `cs`. Другие файлы, используемые в версии C#, можно найти в папках `shared` и `SharedContent`.
- Версию C++/WinRT образца исходного кода можно найти в папке [cppwinrt](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/Clipboard/cppwinrt) в репозитории примера на сайте GitHub.

Пошаговое руководство в этом разделе показывает, как можно повторно создать версию C++/WinRT путем ее переноса из исходного кода C#. Это позволит увидеть, как можно перенести собственные проекты C# в C++/WinRT.

Чтобы получить представление о том, что делает пример, откройте C# решение (`\Clipboard_sample\cs\Clipboard.sln`), измените конфигурацию соответствующим образом (возможно, *x64*), выполните сборку и запуск. В пользовательском интерфейсе примера представлено пошаговое руководство по различным функциям.

## <a name="createablankappcwinrt-named-clipboard"></a>Создание пустого приложения (C++/WinRT) с именем Clipboard

> [!NOTE]
> Сведения об установке и использовании расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки) см. в разделе о  [поддержке C++/WinRT в Visual Studio](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

Начните процесс переноса, создав новый проект C++/WinRT в Microsoft Visual Studio. Создайте проект с помощью шаблона проекта **Пустое приложение (C++/WinRT)** . Задайте для него имя *Clipboard* и (чтобы структура папок соответствовала структуре, приведенной в руководстве) убедитесь, что вы сняли флажок **Поместить решение и проект в одном каталоге**.

Чтобы получить базовые показатели, убедитесь, что эта новая, пустая сборка проекта и она выполняется.

## <a name="packageappxmanifest-and-asset-files"></a>Package.appxmanifest и файлы актива

Если версии C# и C++/WinRT примера не нужно устанавливать на одном компьютере, то исходные файлы пакета приложений обоих проектов (`Package.appxmanifest`) могут быть идентичны. В этом случае вы можете просто скопировать `Package.appxmanifest` из проекта C# в проект C++/WinRT, и все готово.

Для сосуществования двух версий выборки необходимы разные идентификаторы. В этом случае откройте файл `Package.appxmanifest` в редакторе XML в проекте C++/WinRT и запишите эти три значения.

- В элементе **/Package/Identity** запишите значение атрибута **Имя**. Это *имя пакета*. Если это только что созданный проект, в проекте ему будет присвоено начальное значение уникального GUID.
- В элементе **/Package/Applications/Application** запишите значение атрибута **ИД**. Это *ИД приложения*.
- В элементе **/Package/mp:PhoneIdentity** запишите значение атрибута **PhoneProductId**. Опять же, если это только что созданный проект, для него будет задан тот же GUID, что и для имени пакета.

Затем скопируйте `Package.appxmanifest` из проекта C# в проект C++/WinRT. Наконец, вы можете восстановить три значения, которые вы записали. Или же вы можете отредактировать скопированные значения, чтобы сделать их уникальными и (или) подходящими для приложения и своей организации (как правило, для нового проекта). Например, в этом случае вместо восстановления значения имени пакета мы можем просто изменить скопированное значение с *Microsoft.SDKSamples.Clipboard.CS* на *Microsoft.SDKSamples.Clipboard.CppWinRT*. И мы можем оставить идентификатор приложения установленным на *App*. Пока либо имя пакета, *либо* идентификатор приложения будет отличаться, два приложения будут иметь разные идентификаторы пользовательской модели приложения (AUMID).

В рамках этого пошагового руководства имеет смысл внести еще несколько изменений в `Package.appxmanifest`. Существует три экземпляра строки *пример буфера обмена C#* . Измените это значение на *пример буфера обмена C++/WinRT*.

В проекте C++/WinRT файл `Package.appxmanifest` и проект теперь не синхронизированы в отношении файлов активов, на которые они ссылаются. Чтобы устранить эту проблему сначала удалите ресурсы из проекта C++/WinRT, выбрав все файлы в папке `Assets` (в обозревателе решений в Visual Studio) и удалив их (выберите **Удалить** в диалоговом окне).

Проект C# ссылается на файлы активов из общей папки. Вы можете сделать то же самое в проекте C++/WinRT, или скопировать файлы, как это делается в этом пошаговом руководстве.

Перейдите к папке `\Clipboard_sample\SharedContent\media`. Выберите семь файлов, включаемых в проект C# (`microsoft-sdk.png` до `windows-sdk.png`), скопируйте их и вставьте в папку `\Clipboard\Clipboard\Assets` в новом проекте.

Щелкните правой кнопкой мыши папку `Assets` (в обозревателе решений в проекте C++/WinRT) > **Добавить** > **существующий элемент...* и перейдите к `\Clipboard\Clipboard\Assets`. В средстве выбора файлов выберите семь файлов и нажмите кнопку **Добавить**.

`Package.appxmanifest` теперь снова синхронизирован с файлами актива проекта.

## <a name="mainpage-including-the-sample-configuration-functionality"></a>**MainPage**, включая функцию настройки образцов

Образец буфера обмена&mdash;, как и все [примеры приложений универсальной платформы Windows (UWP)](https://github.com/microsoft/Windows-universal-samples)&mdash;состоит из набора сценариев, которые пользователь может выполнять по одному за раз. Коллекция сценариев в данном примере настраивается в исходном коде образца. Каждый сценарий в коллекции представляет собой элемент данных, в котором хранится заголовок, а также тип класса в проекте, который реализует этот сценарий.

В C# версии примера при просмотре файла с исходным кодом `SampleConfiguration.cs` вы увидите два класса. Большая часть логики конфигурации находится в классе **MainPage**, который является разделяемым классом (он образует полный класс при объединении с выноской в `MainPage.xaml` и императивным кодом в `MainPage.xaml.cs`). Другим классом в этом файле исходного кода является **Scenario**, его **заголовок** и свойства **ClassType**.

В следующих разделах мы рассмотрим, как переносить классы **MainPage** и **Scenario**.

### <a name="idl-for-the-mainpage-type"></a>IDL для типа **MainPage**

Файлы исходного кода C#, которые совместно реализуют тип **MainPage**: `MainPage.xaml` (который скоро будет перенесен путем копирования), `MainPage.xaml.cs`и `SampleConfiguration.cs`.

В версии для C++/WinRT мы аналогичным образом учтем тип **MainPage** в файлах исходного кода. Мы будем использовать логику в `MainPage.xaml.cs` и преобразовывать ее в `MainPage.h` и `MainPage.cpp`. А для логики в `SampleConfiguration.cs`, мы преобразуем ее в `SampleConfiguration.h` и `SampleConfiguration.cpp`.

Классы в приложении C# универсальной платформы Windows (UWP) являются типами среды выполнения Windows. Однако при создании типа в приложении C++/WinRT вы можете выбрать, будет ли этот тип типом среды выполнения Windows, или обычным классом, структурой или перечислением.

В проекте C++/WinRT **MainPage** уже является типом среды выполнения Windows, поэтому нам не нужно изменять этот аспект. В частности, это *класс среды выполнения*.

- Дополнительные сведения о создании классов среды выполнения см. в статье [Создание интерфейсов API с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/author-apis).
- В C++/WinRT важны понятия *типа исполнения* и *проецируемого типа*. Вы можете узнать о них в упомянутой выше статье, а также в статье [Создание API-интерфейсов с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/consume-apis).
- Сведения о соединении между классами среды выполнения и файлами `.idl` см. в статье [Элементы управления XAML; привязка к свойству C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-property). В этой статье объясняется процесс написания нового класса среды выполнения, первым шагом которого является добавление в проект нового элемента **Midl File (.idl)** .

Для **MainPage**у нас уже есть необходимый файл `MainPage.idl` в проекте C++/WinRT. Но в этом пошаговом руководстве мы добавим в проект *новые* `.idl` файлы.

Вскоре мы увидим, какой именно IDL нужно добавить в существующий файл `MainPage.idl`. До этого у нас есть некоторые причины, чтобы разобраться с тем, что следует передавать в IDL, а что нет.

Чтобы определить, какие члены **MainPage** нужно объявить в `MainPage.idl` (чтобы они стали частью класса **MainPage**) и какие могут просто быть членами **MainPage**, давайте создадим список членов класса C# **MainPage**. Эти члены можно найти в `MainPage.xaml.cs` и в `SampleConfiguration.cs`.

В итоге можно найти двенадцать полей и методов `protected` и `private`. И мы найдем следующих членов `public`.

- Конструктор по умолчанию `MainPage()`.
- Статические поля **Current** и **FEATURE_NAME**.
- Свойства **IsClipboardContentChangedEnabled** и **Scenarios**.
- Методы **BuildClipboardFormatsOutputString**, **DisplayToast**, **EnableClipboardContentChangedNotifications**и **NotifyUser**.

Это члены `public`, являющиеся кандидатами на объявление в `MainPage.idl`. Рассмотрим каждый из них и узнаем, должны ли они быть частью класса среды выполнения **MainPage** или частью его реализации.

- Конструктор по умолчанию `MainPage()`. Для**страницы** XAML обычным является объявление в IDL конструктора по умолчанию. Таким образом, инфраструктура пользовательского интерфейса XAML может активировать тип.
- Статическое поле **Current**, используется в отдельных страницах XAML сценария для доступа к экземпляру приложения **MainPage**. Поскольку **Current** не используется для взаимодействия с платформой XAML (и не используется в единицах компиляции), его можно зарезервировать только как члена типа реализации. При работе с собственными проектами и в таких случаях вы можете сделать это. Но поскольку поле является экземпляром проектируемого типа, логично объявить его в IDL. Это то, что мы сделаем здесь (это также делает код более понятным).
- Это аналогичный случай для статического поля **FEATURE_NAME**, доступ к которому осуществляется в типе **MainPage**. Опять же, его объявление в IDL делает наш код более понятным.
- Свойство **IsClipboardContentChangedEnabled** используется только в классе **OtherScenarios**. Итак, во время переноса мы немного упростим вещи, и сделаем его частным полем класса среды выполнения **OtherScenarios**. Так что один из них не будет находиться в IDL.
- Свойство **Scenarios** — это коллекция объектов типа **Scenario** (тип, который мы упоминали ранее). Мы поговорим о **Scenario** в следующем подразделе, поэтому свойство **Scenarios** мы разберем позже.
- Методы **BuildClipboardFormatsOutputString**, **DisplayToast**и **EnableClipboardContentChangedNotifications** — это служебные функции, которые, кажется, больше относятся к общему состоянию образца, чем к главной странице. Поэтому во время переноса мы выполняем рефакторинг этих трех методов на новый тип служебной программы под названием **SampleState** (который не обязательно должен быть типом среды выполнения Windows). По этой причине эти три метода не будут передаваться в IDL.
- Метод **NotifyUser** вызывается из отдельных страниц XAML на экземпляре **MainPage**, который возвращается из статического поля *Current*. Поскольку (как уже отмечалось) **Current** является экземпляром прогнозируемого типа, нам необходимо объявить **NotifyUser** в IDL. **NotifyUser** принимает параметр типа **NotifyType**. Мы поговорим об этом в следующем подразделе.

Мы разрабатываем ход выполнения: создаем список элементов, которые следует и не следует добавлять в файл `MainPage.idl`. Но нам по-прежнему нужно обсудить свойство **Scenarios** свойства и тип **NotifyType**. Итак, давайте сделаем это.

### <a name="idl-for-the-scenario-and-notifytype-types"></a>IDL для типов **Scenario** и **NotifyType**

Класс **Scenario** определен в `SampleConfiguration.cs`. Нам нужно принять решение о том, как перенести этот класс в C++/WinRT. По умолчанию мы, вероятно, создали бы обычную `struct`C++. Однако если **Scenario** используется в двоичных файлах или для взаимодействия с платформой XAML, его следует объявить в IDL как тип среды выполнения Windows.

Изучая исходный код C#, мы обнаружили, что в этом контексте используется **Scenario**.

```xaml
<ListBox x:Name="ScenarioControl" ... >
```

```csharp
var itemCollection = new List<Scenario>();
int i = 1;
foreach (Scenario s in scenarios)
{
    itemCollection.Add(new Scenario { Title = $"{i++}) {s.Title}", ClassType = s.ClassType });
}
ScenarioControl.ItemsSource = itemCollection;
```

Коллекция объектов **Scenario** назначаются свойству [**ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) **ListBox** (который является элементом управления элементами). Поскольку **Scenario** *необходимо* взаимодействовать с XAML, он должен иметь тип среды выполнения Windows. Поэтому его необходимо определить в IDL. Определение типа **Scenario** в IDL заставляет систему сборки C++/WinRT создавать определение исходного кода для **Scenario** в файле заголовков в фоновом режиме (имя и расположение которого не важны для этого пошагового руководства).

И вы вспомните, что **MainPage.Scenarios** — это коллекция объектов **Scenario**, которые, как мы только что сказали, должны быть в IDL. По этой причине в IDL также следует объявить **MainPage.Scenarios**.

**NotifyType** — это `enum`, объявленное в файле `MainPage.xaml.cs` C#. Поскольку мы передаем **NotifyType** в метод, принадлежащий классу среды выполнения **MainPage**, **NotifyType** также должен быть типом среды выполнения Windows. Его следует определить в `MainPage.idl`.

Теперь добавим в файл `MainPage.idl` новые типы и новый член **Mainpage**, которые мы решили объявить в IDL. В то же время мы удалим из IDL элементы-заполнители **Mainpage**, которые были предоставлены шаблоном проекта Visual Studio.

Поэтому в проекте C++/WinRT откройте `MainPage.idl`и измените его, чтобы он выглядел как показано ниже. Обратите внимание, что одним из изменений является изменение имени пространства имен из **Clipboard** на **SDKTemplate**. При желании текущее содержимое `MainPage.idl`, можно просто удалить и вставить в список ниже. Еще одна оптимизация заключается в том, что мы изменяем имя **Scenario::ClassType** на **Scenario::ClassName**.

```idl
// MainPage.idl
namespace SDKTemplate
{
    struct Scenario
    {
        String Title;
        Windows.UI.Xaml.Interop.TypeName ClassName;
    };

    enum NotifyType
    {
        StatusMessage,
        ErrorMessage
    };

    [default_interface]
    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();

        static MainPage Current{ get; };
        static String FEATURE_NAME{ get; };

        static Windows.Foundation.Collections.IVector<Scenario> scenarios{ get; };

        void NotifyUser(String strMessage, NotifyType type);
    };
}
```

> [!NOTE]
> Дополнительные сведения о содержимом файла `.idl` в проекте C++/WinRT см. в статье [Справочник по языку MIDL 3.0](/uwp/midl-3/).

При работе с переносом вам может не потребоваться изменение имени пространства имен, как было описано выше. Мы делаем это здесь только потому, что пространство имен проекта на C#, который мы переносим, по умолчанию — **SDKTemplate**; а имя проекта и сборки — **Clipboard**.

Но по мере продолжения работы с переносом в этом пошаговом руководстве мы изменим каждое вхождение в исходном коде имени пространства имен **Clipboard** на **SDKTemplate**. Также в свойствах проекта C++/WinRT есть место, где появляется имя пространства имен **Clipboard**, поэтому воспользуемся возможностью изменить его сейчас.

В Visual Studio для проекта C++/WinRT задайте свойство проекта **Общие свойства** \> **C++/WinRT** \> **Корневое пространство имен** на значение *SDKTemplate*.

### <a name="save-the-idl-and-re-generate-stub-files"></a>Сохранение IDL и повторное создание файлов заглушек

Если вы прочитали статью [Элементы управления XAML; привязка к свойству C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-property), вы должны были заметить концепцию *файлов заглушек*. Файлы заглушки создаются автоматически (с помощью средства с именем `cppwinrt.exe`и на основе содержимого файлов `.idl`) при построении проекта C++/WinRT. Этот раздел содержит дополнительные сведения о них.

На этом этапе пошагового руководства мы выполнили временное изменение файла `MainPage.idl`, поэтому его следует сохранить. На данный момент проект не собирается до конца, но выполнение сборки сейчас полезно, так как она создает файлы заглушки для **MainPage**.

Для этого проекта C++/WinRT эти файлы заглушки создаются в папке `\Clipboard\Clipboard\Generated Files\sources`. Их можно найти после завершения частичной сборки (опять же, как и ожидалось, сборка не будет выполнена полностью. Но шаг, который нас интересует, &mdash;создание заглушек&mdash; *, будет* успешным). Файлы, которые нас интересуют — `MainPage.h` и `MainPage.cpp`.

В этих двух файлах заглушки вы увидите реализации заглушек для новых членов **MainPage**, добавленных в IDL (например, **Current** и **FEATURE_NAME**). Эти реализации заглушек будут скопированы в файлы `MainPage.h` и `MainPage.cpp`, уже находящиеся в проекте. В то же время, как и в случае с IDL, мы удалим из этих существующих файлов элементы-заполнители **Mainpage**, которые нам предоставил шаблон проекта Visual Studio (фиктивное свойство с именем **MyProperty**, и обработчик события с именем **ClickHandler**).

Фактически, единственным членом текущей версии **MainPage**, который мы оставим, является конструктор.

После того, как вы скопировали новые члены из файлов заглушки, удалили ненужные нам члены и обновили пространство имен, файлы `MainPage.h` и `MainPage.cpp` в вашем проекте должны выглядеть следующим образом. Обратите внимание, что единственное изменение, внесенное в тип **factory_implementation**, — обновление его пространства имен.

```cppwinrt
// MainPage.h
#pragma once
#include "MainPage.g.h"

namespace winrt::SDKTemplate::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
        MainPage();

        static SDKTemplate::MainPage Current();
        static hstring FEATURE_NAME();
        static Windows::Foundation::Collections::IVector<SDKTemplate::Scenario> scenarios();
        void NotifyUser(hstring const& strMessage, SDKTemplate::NotifyType const& type);
    };
}
namespace winrt::SDKTemplate::factory_implementation
{
    struct MainPage : MainPageT<MainPage, implementation::MainPage>
    {
    };
}
```

```cppwinrt
// MainPage.cpp
#include "pch.h"
#include "MainPage.h"
#include "MainPage.g.cpp"

namespace winrt::SDKTemplate::implementation
{
    MainPage::MainPage()
    {
        InitializeComponent();
    }
    SDKTemplate::MainPage MainPage::Current()
    {
        throw hresult_not_implemented();
    }
    hstring MainPage::FEATURE_NAME()
    {
        throw hresult_not_implemented();
    }
    Windows::Foundation::Collections::IVector<SDKTemplate::Scenario> MainPage::scenarios()
    {
        throw hresult_not_implemented();
    }
    void MainPage::NotifyUser(hstring const& strMessage, SDKTemplate::NotifyType const& type)
    {
        throw hresult_not_implemented();
    }
}
```

Для строк C# использует **System.String**. См. пример метода **MainPage.NotifyUser**. В нашем IDL мы объявляем строку с помощью **String**, а когда средство `cppwinrt.exe` генерирует код C++/WinRT, он использует тип [**winrt::hstring**](/uw/cpp-ref-for-winrt/hstring). Всякий раз, когда мы будем сталкиваться со строкой в коде C#, мы будем переносить ее на **winrt::hstring**. Дополнительные сведения см. в статье [Обработка строк в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/strings).

Описание параметров `const&` в подписях методов см. в разделе [Передача параметров](/windows/uwp/cpp-and-winrt-apis/concurrency#parameter-passing).

### <a name="update-all-remaining-namespace-declarationsreferences-and-build"></a>Обновление всех оставшихся объявлений/ссылок пространств имен и сборка

Перед сборкой проекта C++/WinRT найдите все объявления или ссылки на пространство имен **Clipboard** и измените их на **SDKTemplate**.

- `MainPage.xaml` и `App.xaml`. Пространство имен отображается в значениях атрибутов `x:Class` и `xmlns:local`.
- `App.idl`.
- `App.h`.
- `App.cpp`. Существует две директивы `using namespace` (найдите подстроку `using namespace Clipboard`) и две квалификации типа **MainPage** (найдите `Clipboard::MainPage`). Их нужно изменить.

Поскольку мы удалили обработчик событий из **MainPage**, также перейдите к `MainPage.xaml` и удалите из разметки элемент **Button**.

Сохраните все файлы. Очистите решение (**Сборка** > **Clean Clipboard** (Очистить буфер обмена)), а затем выполните сборку. Сборка будет успешной, если внесенные до сих пор изменения будут действительными.

### <a name="implement-the-mainpage-members-that-we-declared-in-idl"></a>Реализация членов **MainPage**, объявленных в IDL

#### <a name="the-constructor-current-and-feature_name"></a>Конструктор, **Current**, а также **FEATURE_NAME**

Ниже приведен соответствующий код (из проекта C#), который нужно перенести.

```xaml
<!-- MainPage.xaml -->
...
<TextBlock x:Name="SampleTitle" ... />
...
```

```csharp
// MainPage.xaml.cs
...
public sealed partial class MainPage : Page
{
    public static MainPage Current;

    public MainPage()
    {
        InitializeComponent();
        Current = this;
        SampleTitle.Text = FEATURE_NAME;
    }
...
}
...

// SampleConfiguration.cs
...
public partial class MainPage : Page
{
    public const string FEATURE_NAME = "Clipboard C# sample";
...
}
...
```

Вскоре мы будем в полной мере использовать `MainPage.xaml` (путем копирования). Сейчас мы временно добавим элемент **TextBlock** с соответствующим именем в `MainPage.xaml` проекта C++/WinRT.

**FEATURE_NAME** является статическим полем **MainPage** (поле C# `const` по сути является статическим в своем поведении), определенном в `SampleConfiguration.cs`. Для C++/WinRT вместо (статического) поля мы сделаем его выражением C++/WinRT (статического) свойства только для чтения. Способ представления метода получения свойства C++/WinRT — это функция, возвращающая значение свойства и не принимающая параметры (метод доступа). Поэтому статическое поле **FEATURE_NAME** C# станет статической функцией метода доступа C++/WinRT **FEATURE_NAME** (в данном случае возвращает строковый литерал).

Кстати, мы делаем то же самое при переносе свойства C#, доступного только для чтения. Для записываемого свойства C# способом C++/WinRT для выражения метода задания свойства является функция `void`, принимающая значение свойства в качестве параметра (метод изменения). В любом случае, если поле или свойство C# является статическим, то это метод доступа и (или) изменения C++/WinRT.

**Current** является статическим (не константным) полем **MainPage**. Мы также сделаем его (выражение C++/WinRT) свойством только для чтения и опять же статическим. Если **FEATURE_NAME** является константой, **Current** не может быть тоже константой. Итак, в C++/WinRT нам потребуется резервное поле, и наш метод доступа возвратит его. Таким образом, в проекте C++/WinRT мы объявим в `MainPage.h` частное статическое поле с именем **current**, определим/инициализируем **current** в `MainPage.cpp` (поскольку он имеет статическую длительность хранения), и будем обращаться к нему через общедоступную статическую функцию метода доступа с именем **Current**.

Сам конструктор выполняет несколько назначений, которые являются для переноса прямыми.

В проекте C++/WinRT добавьте новый элемент**Visual C++**  > **Code** > **Файл C++ (.cpp)** с именем `SampleConfiguration.cpp`.

Измените `MainPage.xaml`, `MainPage.h`, `MainPage.cpp`и `SampleConfiguration.cpp`, чтобы они соответствовали приведенным ниже спискам.

```xaml
<!-- MainPage.xaml -->
...
<StackPanel ...>
    <TextBlock x:Name="SampleTitle" />
</StackPanel>
...
```

```cppwinrt
// MainPage.h
...
namespace winrt::SDKTemplate::implementation
{
    struct MainPage : MainPageT<MainPage>
    {
...
        static SDKTemplate::MainPage Current() { return current; }
...
    private:
        static SDKTemplate::MainPage current;
...
    };
...
}

// MainPage.cpp
...
namespace winrt::SDKTemplate::implementation
{
    SDKTemplate::MainPage MainPage::current{ nullptr };
...
    MainPage::MainPage()
    {
        InitializeComponent();
        MainPage::current = *this;
        SampleTitle().Text(FEATURE_NAME());
    }
...
}

// SampleConfiguration.cpp
#include "pch.h"
#include "MainPage.h"

using namespace winrt;
using namespace SDKTemplate;

hstring implementation::MainPage::FEATURE_NAME()
{
    return L"Clipboard C++/WinRT Sample";
}
```

Также не забудьте удалить существующие тела функций из `MainPage.cpp` для **MainPage::Current()** и **MainPage::FEATURE_NAME()** , поскольку теперь мы определяем эти методы в других местах.

Как видите, **MainPage::Current** объявлен как имеющий тип **SDKTemplate::MainPage**, который является проецируемым типом. Он не относится к типу **SDKTemplate::implementation::MainPage**, который является типом реализации. Проецируемый тип — это тип, предназначенный для использования в проекте для взаимодействия XAML или в двоичных файлах. Тип реализации — это то, что используется для реализации средств, предоставляемых в проецируемом типе. Поскольку объявление **MainPage::Current** (в `MainPage.h`) появляется в пространстве имен реализации (**winrt::SDKTemplate::implementation**), неквалифицированный класс **MainPage** будет называться типом реализации. Таким образом, для **SDKTemplate::** необходимо указать, что **MainPage::current** будет экземпляром проецируемого типа **winrt::SDKTemplate::MainPage**.

В конструкторе есть некоторые пункты, связанные с `MainPage::current = *this;`, для которых нужно объяснение.
- При использовании указателя `this` внутри члена типа реализации указатель `this` конечно же является *указателем на тип реализации*.
- Чтобы преобразовать указатель `this` в соответствующий проецируемый тип, его следует разыменовать. При условии, что вы создаете тип реализации из IDL (как у нас здесь), тип реализации имеет оператор преобразования, который преобразует его в свой проецируемый тип. Вот почему назначение здесь работает.

Дополнительные сведения об этих подробностях см. [Создание экземпляров, возврат типов реализации и интерфейсов](/windows/uwp/cpp-and-winrt-apis/author-apis#instantiating-and-returning-implementation-types-and-interfaces).

Кроме того, в конструкторе `SampleTitle().Text(FEATURE_NAME());`. Часть `SampleTitle()` является вызовом простой функции метода доступа с именем **SampleTitle**, которая возвращает **TextBlock**, добавленный в XAML. Всякий раз, когда вы заменяете `x:Name` элементом XAML, компилятор XAML создает метод доступа с именем для элемента. Часть `.Text(...)` вызывает функцию метода изменения **Text** в объекте **TextBlock**, возвращаемом методом доступа **SampleTitle**. И `FEATURE_NAME()` вызывает статическую функцию метода доступа **MainPage::FEATURE_NAME** для возврата строкового литерала. В целом, эта строка кода задает свойство **Text** **TextBlock** с именем *SampleTitle*.

Обратите внимание, что поскольку в среде выполнения Windows строки широко распространены, для переноса строкового литерала мы будем добавлять к нему префикс кодировки расширенного набора `L`. Итак, мы изменим (например,) "строковый литерал" на L"строковый литерал". Также см. раздел [Строковые литералы с расширенными символами](/cpp/cpp/string-and-character-literals-cpp#wide-string-literals).

#### <a name="scenarios"></a>**Сценарии**

Вот соответствующий код C#, который нужно перенести.

```csharp
// MainPage.xaml.cs
...
public sealed partial class MainPage : Page
{
...
    public List<Scenario> Scenarios
    {
        get { return this.scenarios; }
    }
...
}
...

// SampleConfiguration.cs
...
public partial class MainPage : Page
{
...
    List<Scenario> scenarios = new List<Scenario>
    {
        new Scenario() { Title = "Copy and paste text", ClassType = typeof(CopyText) },
        new Scenario() { Title = "Copy and paste an image", ClassType = typeof(CopyImage) },
        new Scenario() { Title = "Copy and paste files", ClassType = typeof(CopyFiles) },
        new Scenario() { Title = "Other Clipboard operations", ClassType = typeof(OtherScenarios) }
    };
...
}
...
```

Из предыдущего исследования мы понимаем, что эта коллекция объектов **Scenario** отображается в **ListBox**. В C++/WinRT есть ограничения для *типа коллекции*, который можно назначить свойству **ItemsSource** элемента управления элементами. Коллекция должна быть либо вектором, либо наблюдаемым вектором, и ее элементы должны быть одним из следующих элементов.

- Классы среды выполнения или
- [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable).

В случае **IInspectable**, если элементы не являются классами среды выполнения, эти элементы должны иметь тип, который может быть упакован и распакован в [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable). И это значит, что они должны быть типами среды выполнения Windows (см. [Упаковка и распаковка скалярных значений в IInspectable](/windows/uwp/cpp-and-winrt-apis/boxing)).

В этом примере мы не делали **Scenario** классом среды выполнения. Тем не менее, это все еще целесообразный вариант. Кроме того, в вашем личном опыте переноса будут случаи, когда класс среды выполнения будет лучшим из возможных вариантов. Например, если вам нужно сделать тип элемента *наблюдаемым*. (см. [Элементы управления XAML; привязка к свойству C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-property)), или если элемент должен иметь методы по любой другой причине, и это больше, чем просто набор членов данных.

Поскольку в этом пошаговом руководстве мы *не* работаем с классом среды выполнения для типа **Scenario**, нам нужно подумать об упаковке. Если бы мы сделали **Scenario** обычной `struct`, C++ то не смогли бы его упаковывать. Но мы объявили **Scenario** как `struct` в IDL, и поэтому мы *можем* его упаковывать.

У нас остаётся выбор: упаковать **Scenario** заранее, или подождать, пока мы не назначим **ItemsSource**, и упаковать его в случае необходимости. Ниже приведены некоторые рекомендации, касающиеся этих двух вариантов.

- Упаковка с опережением. Для этого параметра наш член данных является коллекцией **IInspectable**, готовой к назначению пользовательскому интерфейсу. При инициализации мы упаковываем объекты **Scenario** в этот член данных. Нам нужна только одна копия этой коллекции, но мы должны распаковывать элемент каждый раз, когда нам нужно прочитать его поля.
- Упаковка по мере необходимости. Для этого параметра наш член данных является коллекцией **Scenario**. Когда приступает время для назначения пользовательского интерфейса, мы упаковываем объекты **Scenario** из члена данных в новую коллекцию **IInspectable**. Поля элементов в члене данных можно считывать без распаковки, однако нам нужны две копии коллекции.

Как видите, для небольшой коллекции, наподобие этой, преимущества и недостатки делают ее ненужной. Итак, в нашем примере мы перейдем к JIT-параметру.

Элементы **scenarios** — это поле **MainPage**, которое определяется и инициализируется в `SampleConfiguration.cs`. И **Scenarios** — это свойство только для чтения **MainPage**, определенное в `MainPage.xaml.cs` (и реализованное для простого возврата поля **scenarios**). Мы выполним что-то подобное в проекте C++/WinRT; но сделаем эти два элемента статическими (поскольку нам нужен только один экземпляр в приложении, и так чтобы к ним можно было получить доступ без экземпляра класса). Мы назовем их *scenariosInner* и *scenarios* соответственно. Мы объявляем *scenariosInner* в `MainPage.h`. И, поскольку он имеет статическую длительность хранения, мы определим или инициализируем его в файле `.cpp` (в данном случае — `SampleConfiguration.cpp`).

Измените `MainPage.h` и `SampleConfiguration.cpp`, чтобы они соответствовали приведенным ниже спискам.

```cppwinrt
// MainPage.h
...
struct MainPage : MainPageT<MainPage>
{
...
    static Windows::Foundation::Collections::IVector<Scenario> scenarios() { return scenariosInner; }
...
private:
    static winrt::Windows::Foundation::Collections::IVector<Scenario> scenariosInner;
...
};

// SampleConfiguration.cpp
...
using namespace Windows::Foundation::Collections;
...
IVector<Scenario> implementation::MainPage::scenariosInner = winrt::single_threaded_observable_vector<Scenario>(
{
    Scenario{ L"Copy and paste text", xaml_typename<SDKTemplate::CopyText>() },
    Scenario{ L"Copy and paste an image", xaml_typename<SDKTemplate::CopyImage>() },
    Scenario{ L"Copy and paste files", xaml_typename<SDKTemplate::CopyFiles>() },
    Scenario{ L"History and roaming", xaml_typename<SDKTemplate::HistoryAndRoaming>() },
    Scenario{ L"Other Clipboard operations", xaml_typename<SDKTemplate::OtherScenarios>() },
});
```

Также не забудьте удалить существующий текст функции из `MainPage.cpp` для **MainPage::scenarios()** , так как теперь мы определяем этот метод в файле заголовка.

Как видите, в `SampleConfiguration.cpp`мы инициализируем статический член данных *scenariosInner*, вызывая вспомогательную функцию C++/WinRT с именем [winrt::single_threaded_observable_vector](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector). Эта функция создает новый объект коллекции среды выполнения Windows и возвращает его как интерфейс [**IObservableVector**](/uwp/api/windows.foundation.collections.iobservablevector_t_). Поскольку в данном примере коллекция не является *наблюдаемой* (это не обязательно, т. к. она не добавляет и не удаляет элементы после инициализации), мы могли бы вместо этого вызвать [winrt::single_threaded_vector](/uwp/cpp-ref-for-winrt/single-threaded-vector). Эта функция возвращает коллекцию в виде интерфейса [**IVector**](/uwp/api/windows.foundation.collections.ivector_t_).

Дополнительные сведения о коллекциях и их привязке см. в разделах [Элементы управления XAML; привязка к коллекции C++/WinRT](/windows/uwp/cpp-and-winrt-apis/binding-collection) и [Коллекции с C++/WinRT](/windows/uwp/cpp-and-winrt-apis/collections).

Код инициализации, который вы только что добавили, ссылается на типы, еще не находящиеся в проекте (например, **winrt::SDKTemplate::CopyText**. Чтобы устранить эту проблему, добавим в проект пять новых пустых страниц XAML.

#### <a name="add-five-new-blank-xaml-pages"></a>Добавление пяти новых пустых страниц XAML

Добавьте новый элемент **XAML** > **Blank Page (C++/WinRT)**   в проект (убедитесь в том, что это шаблон элемента **Blank Page (C++/WinRT)** , а не сама **Blank Page** (Пустая страница)). Назовите его  `CopyText`. Новая страница XAML определяется в пространстве имен **SDKTemplate**, это как раз нам и нужно.

Повторите описанный выше процесс еще четыре раза, а также назовите страницы XAML `CopyImage`, `CopyFiles`, `HistoryAndRoaming`, а также `OtherScenarios`.

Теперь вы сможете выполнить сборку снова, если захотите.

#### <a name="notifyuser"></a>**NotifyUser**

В проекте C# вы найдете реализацию метода **MainPage.NotifyUser** в `MainPage.xaml.cs`. **MainPage.NotifyUser** зависит от **MainPage.UpdateStatus**, а этот метод, в свою очередь, зависит от элементов XAML, которые еще не были перенесены. Итак, теперь мы просто выберем метод **UpdateStatus** в проекте C++/WinRT, и перенесем его позже.

Вот соответствующий код C#, который нужно перенести.

```csharp
// MainPage.xaml.cs
...
public void NotifyUser(string strMessage, NotifyType type)
if (Dispatcher.HasThreadAccess)
{
    UpdateStatus(strMessage, type);
}
else
{
    var task = Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => UpdateStatus(strMessage, type));
}
private void UpdateStatus(string strMessage, NotifyType type) { ... }{
...
```

**NotifyUser** использует перечисление [**Windows.UI.Core.CoreDispatcherPriority**](/uwp/api/windows.ui.core.coredispatcherpriority). В C++/WinRT при необходимости использовать тип из пространств имен Windows необходимо включить соответствующий файл заголовка пространства имен C++/WinRT Windows (Дополнительные сведения см. в разделе [Начало работы с C++/WinRT](/windows/uwp/cpp-and-winrt-apis/get-started)). В этом случае, как видно из приведенного ниже кода, заголовок — `winrt/Windows.UI.Core.h`, и мы будем включать его в `pch.h`.

**UpdateStatus** является частным. Итак, мы выполним этот частный метод в нашем типе реализации **MainPage**. **UpdateStatus** не предназначен для вызова в классе среды выполнения, поэтому мы не будем объявлять его в IDL.

После переноса **MainPage.NotifyUser** и вывода **MainPage.UpdateStatus**, это то, что мы имеем в проекте C++/WinRT. После этого примера кода мы рассмотрим некоторые детали.

```cppwinrt
// pch.h
...
#include <winrt/Windows.UI.Core.h>
...

// MainPage.h
...
struct MainPage : MainPageT<MainPage>
{
...
    void NotifyUser(hstring const& strMessage, SDKTemplate::NotifyType const& type);
...
private:
    void UpdateStatus(hstring const& strMessage, SDKTemplate::NotifyType const& type);
...
};

// MainPage.cpp
...
void MainPage::NotifyUser(hstring const& strMessage, SDKTemplate::NotifyType const& type)
{
    if (Dispatcher().HasThreadAccess())
    {
        UpdateStatus(strMessage, type);
    }
    else
    {
        Dispatcher().RunAsync(Windows::UI::Core::CoreDispatcherPriority::Normal, [strMessage, type, this]()
            {
                UpdateStatus(strMessage, type);
            });
    }
}
void MainPage::UpdateStatus(hstring const& strMessage, SDKTemplate::NotifyType const& type)
{
    throw hresult_not_implemented();
}
...
```

В C#, вы *установите точку* во вложенных свойствах. Таким образом, тип C# **MainPage** может получить доступ к своему собственному свойству **Dispatcher** с синтаксисом `Dispatcher`. А C# может далее *сделать точку* в этом значении с таким синтаксисом, как `Dispatcher.HasThreadAccess`. В C++/WinRT свойства реализуются как функции доступа, поэтому синтаксис отличается только тем, что вы добавляете круглые скобки для каждого вызова функции.

|C#|C++/WinRT|
|-|-|
|`Dispatcher.HasThreadAccess`|`Dispatcher().HasThreadAccess()`|

Когда C# версия **NotifyUser** вызывает [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync), он реализует асинхронный делегат обратного вызова в виде лямбда-функции. Версия C++/WinRT делает то же самое, однако ее синтаксис немного отличается. В C++/WinRT мы  *захватываем* два параметра, которые будем использовать, а также указатель `this` (так как мы собираемся вызывать функцию члена). Дополнительные сведения о реализации обратных вызовов в качестве лямбда-выражений и примеры кода см. в статье [Обработка событий с помощью делегатов в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/handle-events). Кроме того, в этом конкретном случае можно проигнорировать часть `var task =`. Мы не ожидаем возврата возвращенного асинхронного объекта, поэтому нет необходимости хранить его. 

### <a name="implement-the-remaining-mainpage-members"></a>Реализация оставшихся членов **MainPage**

Составим полный список членов **MainPage** (реализованные через `MainPage.xaml.cs` и `SampleConfiguration.cs`), чтобы было видно, какие из них мы уже перенесли, а какие еще предстоит перенести.

|Член|Доступ|Состояние|
|-|-|-|
|Конструктор **MainPage**|`public`|Перенесено|
|Свойство **Current**|`public`|Перенесено|
|Свойство **FEATURE_NAME**|`public`|Перенесено|
|Свойство **IsClipboardContentChangedEnabled**|`public`|Не запущено|
|Свойство **Scenarios**|`public`|Перенесено|
|Метод **BuildClipboardFormatsOutputString**|`public`|Не запущено|
|Метод **DisplayToast**|`public`|Не запущено|
|Метод **EnableClipboardContentChangedNotifications**|`public`|Не запущено|
|Метод **NotifyUser**|`public`|Перенесено|
|Метод **OnNavigatedTo**|`protected`|Не запущено|
|Поле **isApplicationWindowActive**|`private`|Не запущено|
|Поле **needToPrintClipboardFormat**|`private`|Не запущено|
|Поле **scenarios**|`private`|Перенесено|
|Метод **Button_Click**|`private`|Не запущено|
|Метод **DisplayChangedFormats**|`private`|Не запущено|
|Метод **Footer_Click**|`private`|Не запущено|
|Метод **HandleClipboardChanged**|`private`|Не запущено|
|Метод **OnClipboardChanged**|`private`|Не запущено|
|Метод **OnWindowActivated**|`private`|Не запущено|
|Метод **ScenarioControl_SelectionChanged**|`private`|Не запущено|
|Метод **UpdateStatus**|`private`|Создано|

В следующих подразделах мы поговорим о не перенесенных членах.

> [!NOTE]
> Время от времени мы будем переключаться между ссылками в исходном коде на элементы пользовательского интерфейса в разметке XAML (в `MainPage.xaml`). По мере получения этих ссылок, мы временно обсудим их, добавляя в XAML простые элементы заполнителя. Таким образом, построение проекта будет продолжатся после каждого подраздела. Альтернативой является разрешение ссылок путем копирования *всего* содержимого `MainPage.xaml` из проекта C# в проект C++/WinRT. Но если мы это сделаем, то пройдет много времени, прежде чем мы сможем прийти к перезапуску и повторной сборке (что потенциально может скрывать опечатки или другие ошибки, которые мы делаем).
>
> После завершения переноса императивного кода для класса **MainPage**, мы *затем* скопируем содержимое файла XAML и убедимся, что проект будет по-прежнему построен.

#### <a name="isclipboardcontentchangedenabled"></a>**IsClipboardContentChangedEnabled**

Это свойство C#, которое по умолчанию имеет значение `false`. Оно является членом **MainPage** и определено в `SampleConfiguration.cs`.

Для C++/WinRT нам потребуется функция метода доступа, функция метода изменения и резервный член данных в качестве поля. Поскольку **IsClipboardContentChangedEnabled** представляет состояние одного из сценариев в образце, а не состояние **MainPage**, мы создадим новые элементы в новом типе служебной программы с именем **SampleState**. Мы реализуем его в нашем файле исходного кода `SampleConfiguration.cpp`, и сделаем членами `static` (поскольку нам нужен только один экземпляр в приложении, и так что к ним можно получить доступ, не требуя экземпляра класса).

Чтобы присвоить `SampleConfiguration.cpp` в проекте C++/WinRT, добавьте новый элемент **Visual C++**  > **Code** > **Файл заголовка (.h)`SampleConfiguration.h` с именем**. Измените `SampleConfiguration.h` и `SampleConfiguration.cpp`, чтобы они соответствовали приведенным ниже спискам.

```cppwinrt
// SampleConfiguration.h
#pragma once 
#include "pch.h"

namespace winrt::SDKTemplate
{
    struct SampleState
    {
        static bool IsClipboardContentChangedEnabled();
        static void IsClipboardContentChangedEnabled(bool checked);
    private:
        static bool isClipboardContentChangedEnabled;
    };
}

// SampleConfiguration.cpp
...
#include "SampleConfiguration.h"
...
bool SampleState::isClipboardContentChangedEnabled = false;
...
bool SampleState::IsClipboardContentChangedEnabled()
{
    return isClipboardContentChangedEnabled;
}
void SampleState::IsClipboardContentChangedEnabled(bool checked)
{
    if (isClipboardContentChangedEnabled != checked)
    {
        isClipboardContentChangedEnabled = checked;
    }
}
```

Кроме того, поле с хранилищем `static` (например, **SampleState::isClipboardContentChangedEnabled**) должно быть определено в приложении один раз, а файл `.cpp` является хорошим местом для этого (`SampleConfiguration.cpp` в этом случае).

#### <a name="buildclipboardformatsoutputstring"></a>**BuildClipboardFormatsOutputString**

Этот метод является общедоступным членом **MainPage**и определен в `SampleConfiguration.cs`.

```csharp
// SampleConfiguration.cs
...
public string BuildClipboardFormatsOutputString()
{
    DataPackageView clipboardContent = Windows.ApplicationModel.DataTransfer.Clipboard.GetContent();
    StringBuilder output = new StringBuilder();

    if (clipboardContent != null && clipboardContent.AvailableFormats.Count > 0)
    {
        output.Append("Available formats in the clipboard:");
        foreach (var format in clipboardContent.AvailableFormats)
        {
            output.Append(Environment.NewLine + " * " + format);
        }
    }
    else
    {
        output.Append("The clipboard is empty");
    }
    return output.ToString();
}
...
```

В C++/WinRT мы сделаем **BuildClipboardFormatsOutputString** общедоступным статическим методом **SampleState**. Мы можем сделать его `static`, поскольку он не обращается к членам экземпляра.

Чтобы использовать в C++/WinRT типы **Clipboard** и **DataPackageView**, необходимо включить файл заголовка пространства имен C++/WinRT Windows `winrt/Windows.ApplicationModel.DataTransfer.h`.

В C# свойство **DataPackageView.AvailableFormats** является **IReadOnlyList**, поэтому мы можем получить доступ к его свойству **Count**. В C++/WinRT функция метода доступа **DataPackageView::AvailableFormats** возвращает **IVectorView**, имеющий функцию **Size**, которую можно вызвать.

Для переноса использования типа C# **System.Text.StringBuilder** мы будем использовать стандартный тип C++ [**std::wostringstream**](/cpp/standard-library/sstream-typedefs#wostringstream). Этот тип является потоком вывода для расширенных строк (и для его использования необходимо включить файл заголовка `sstream`). Вместо использования метода **Append**, как это было с **StringBuilder**, используется [оператор вывода](/cpp/standard-library/using-insertion-operators-and-controlling-format) (`<<`) с таким потоком вывода, как **westringstream**. Дополнительную информацию см. статью [Программирование iostream](/cpp/standard-library/iostream-programming) и раздел [Форматирование строк](/windows/uwp/cpp-and-winrt-apis/strings#formatting-strings).

Код C# конструирует **StringBuilder** с ключевым словом `new`. В C# объекты являются ссылочными типами по умолчанию, объявленными в куче с `new`. В современных стандартах C++ объекты являются типами значений по умолчанию, объявленными в стеке (без использования `new`). Поэтому мы переносим `StringBuilder output = new StringBuilder();` в C++/WinRT просто как `std::wostringstream output;`.

Ключевое слово C# `var` предложит компилятору определить тип. Вы переносите `var` в `auto` в C++/WinRT. Однако в C++/WinRT есть случаи, когда (во избежание копирования) требуется *ссылка* на выводимый (или выведенный) тип. Вы выражаете это с помощью `auto&`. Существуют также случаи, когда требуется специальный тип ссылки, который выполняет привязку правильно, независимо от того, инициализируется он с *lvalue* или c *rvalue*. Вы выражаете это с помощью `auto&&`. Это форма, которая используется в цикле `for` в приведенном ниже коде. Общие сведения о значениях *lvalue* и *rvalue* см. в статье [Категории значений и ссылки на них](/windows/uwp/cpp-and-winrt-apis/cpp-value-categories).

Измените `pch.h`, `SampleConfiguration.h` и `SampleConfiguration.cpp` чтобы они соответствовали приведенным ниже спискам.

```cppwinrt
// pch.h
...
#include <sstream>
#include "winrt/Windows.ApplicationModel.DataTransfer.h"
...

// SampleConfiguration.h
...
static hstring BuildClipboardFormatsOutputString();
...

// SampleConfiguration.cpp
...
using namespace Windows::ApplicationModel::DataTransfer;
...
hstring SampleState::BuildClipboardFormatsOutputString()
{
    DataPackageView clipboardContent{ Clipboard::GetContent() };
    std::wostringstream output;

    if (clipboardContent && clipboardContent.AvailableFormats().Size() > 0)
    {
        output << L"Available formats in the clipboard:";
        for (auto&& format : clipboardContent.AvailableFormats())
        {
            output << std::endl << L" * " << std::wstring_view(format);
        }
    }
    else
    {
        output << L"The clipboard is empty";
    }

    return hstring{ output.str() };
}
```

> [!NOTE]
> Синтаксис в строке кода `DataPackageView clipboardContent{ Clipboard::GetContent() };` использует возможность современного стандартного языка C++, которая называется *равномерная инициализация*, с ее характерным использованием фигурных скобок вместо знака `=`. Этот синтаксис ясно показывает, что происходит инициализация, а не назначение. Если вы предпочитаете синтаксис, который *выглядит* как назначение (но на самом деле не является им), приведенный выше синтаксис можно заменить на эквивалентный `DataPackageView clipboardContent = Clipboard::GetContent();`. Тем не менее неплохо было бы освоить оба способа выражения инициализации, поскольку, скорее всего, в коде, с которым вы сталкиваетесь, часто используются оба способа.

#### <a name="displaytoast"></a>**DisplayToast**

**DisplayToast** — это общедоступный статический метод класса C# **MainPage**, который можно найти определенным в `SampleConfiguration.cs`. В C++/WinRT мы сделаем его общедоступным статическим методом **SampleState**.

Мы уже обнаружили большую часть сведений и методов, относящихся к переносу этого метода. Следует отметить, что вы переносите строковый литерал C# verbatim (`@`) на стандартный строковый литерал C++ [необработанный строковый литерал](/cpp/cpp/string-and-character-literals-cpp#raw-string-literals-c11) (`LR`).

Кроме того, при ссылке в C++/WinRT на типы [**ToastNotification**](/uwp/api/windows.ui.notifications.toastnotification) и [**XmlDocument**](/uwp/api/windows.data.xml.dom.xmldocument) их можно указать по имени пространства имен или изменить `SampleConfiguration.cpp` и добавить директивы `using namespace`, как показано в следующем примере.

```cppwinrt
using namespace Windows::UI::Notifications;
```

Вы можете выбрать тот же вариант при ссылке на тип [**XmlDocument**](/uwp/api/windows.data.xml.dom.xmldocument) и при каждом обращении к любому другому типу среды выполнения Windows.

Помимо этих пунктов, просто следуйте тем же указаниям, чтобы выполнить следующие шаги.

- Объявите метод в `SampleConfiguration.h`, а также определите его в `SampleConfiguration.cpp`.
- Измените `pch.h`, чтобы включить все необходимые файлы заголовков пространства имен C++/WinRT.
- Создайте объекты C++/WinRT в стопке, а не в куче.
- Замените вызова к методам доступа свойству get с синтаксисом вызова функций (`()`).

Очень распространенная причина ошибок компилятора/компоновщика — забыть включить нужные файлы заголовков пространства имен Windows C++/WinRT. Дополнительные сведения об одной из возможных ошибок см. в разделе [Почему компоновщик отображает сообщение об ошибке "LNK2019: неразрешенный внешний символ?](/windows/uwp/cpp-and-winrt-apis/faq#why-is-the-linker-giving-me-a-lnk2019-unresolved-external-symbol-error).

Если вы хотите ознакомиться с пошаговым руководством и перенести **DisplayToast** самостоятельно, вы можете сравнить ваши результаты с кодом в C++/WinRT версии загруженного вами примера исходного кода Clipboard (он в [`Windows-universal-samples/Samples/Clipboard/cppwinrt`](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/Clipboard/cppwinrt)`/Clipboard.sln`).

#### <a name="enableclipboardcontentchangednotifications"></a>**EnableClipboardContentChangedNotifications**

**EnableClipboardContentChangedNotifications** — это общедоступный статический метод класса C# **MainPage**, который определен в `SampleConfiguration.cs`.

```csharp
// SampleConfiguration.cs
...
public bool EnableClipboardContentChangedNotifications(bool enable)
{
    if (IsClipboardContentChangedEnabled == enable)
    {
        return false;
    }

    IsClipboardContentChangedEnabled = enable;
    if (enable)
    {
        Clipboard.ContentChanged += OnClipboardChanged;
        Window.Current.Activated += OnWindowActivated;
    }
    else
    {
        Clipboard.ContentChanged -= OnClipboardChanged;
        Window.Current.Activated -= OnWindowActivated;
    }
    return true;
}
...
private void OnClipboardChanged(object sender, object e) { ... }
private void OnWindowActivated(object sender, WindowActivatedEventArgs e) { ... }
...
```

В C++/WinRT мы сделаем его общедоступным статическим методом **SampleState**.

В C# для регистрации и отзыва делегатов обработки событий вы используете синтаксис оператора `+=` и `-=`. В C++/WinRT есть несколько синтаксических параметров для регистрации или отзыва делегата, как описано в статье [Обработка событий с помощью делегатов в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/handle-events). Однако общая форма заключается в том, что регистрация и отзыв осуществляются с помощью вызова пары функций с именем для события. Для регистрации вы передаете свой делегат в регистрирующую функцию, а взамен получаете токен отзыва (a [**winrt::event_token**](/uwp/cpp-ref-for-winrt/event-token)). Для отзыва этот маркер передается функции отзыва. В этом случае обработчик является статическим и (как видно из приведенного ниже списка кода) синтаксис вызова функции прост.

Тип **объекта** отображается в подписях обработчика событий C#. На языке C# **объект** представляет собой [псевдоним](/dotnet/csharp/language-reference/builtin-types/reference-types) для типа .NET [**System.Object**](/dotnet/api/system.object). Эквивалентом в C++/WinRT является [**winrt::Windows::Foundation::IInspectable**](/windows/win32/api/inspectable/nn-inspectable-iinspectable). Таким образом, вы увидите **IInspectable** в обработчиках событий C++/WinRT.

Измените `SampleConfiguration.h` и `SampleConfiguration.cpp`, чтобы они соответствовали приведенным ниже спискам.

```cppwinrt
// SampleConfiguration.h
...
private:
    static event_token clipboardContentChangedToken;
    static event_token activatedToken;
    static void OnClipboardChanged(Windows::Foundation::IInspectable const& sender, Windows::Foundation::IInspectable const& e);
    static void OnWindowActivated(Windows::Foundation::IInspectable const& sender, Windows::UI::Core::WindowActivatedEventArgs const& e);
...

// SampleConfiguration.cpp
...
using namespace Windows::Foundation;
using namespace Windows::UI::Core;
using namespace Windows::UI::Xaml;
...
event_token SampleState::clipboardContentChangedToken;
event_token SampleState::activatedToken;
...
bool SampleState::EnableClipboardContentChangedNotifications(bool enable)
{
    if (isClipboardContentChangedEnabled == enable)
    {
        return false;
    }

    IsClipboardContentChangedEnabled(enable);
    if (enable)
    {
        clipboardContentChangedToken = Clipboard::ContentChanged(OnClipboardChanged);
        activatedToken = Window::Current().Activated(OnWindowActivated);
    }
    else
    {
        Clipboard::ContentChanged(clipboardContentChangedToken);
        Window::Current().Activated(activatedToken);
    }
    return true;
}
void SampleState::OnClipboardChanged(IInspectable const&, IInspectable const&){}
void SampleState::OnWindowActivated(IInspectable const&, WindowActivatedEventArgs const& e){}
```

Не закрывайте сами делегаты обработки событий (**OnClipboardChanged** и **OnWindowActivated**) в качестве заглушек. Они уже находятся в нашем списке членов переноса, поэтому мы будем работать с ними в последующих подразделах.

#### <a name="onnavigatedto"></a>**OnNavigatedTo**

**OnNavigatedTo** — это защищенный метод класса C# **MainPage**, который определен в `MainPage.xaml.cs`. В данном случае, вместе с XAML **ListBox**, на который он ссылается.

```xaml
<!-- MainPage.xaml -->
...
<ListBox x:Name="ScenarioControl" ... />
...
```

```csharp
// MainPage.xaml.cs
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    // Populate the scenario list from the SampleConfiguration.cs file
    var itemCollection = new List<Scenario>();
    int i = 1;
    foreach (Scenario s in scenarios)
    {
        itemCollection.Add(new Scenario { Title = $"{i++}) {s.Title}", ClassType = s.ClassType });
    }
    ScenarioControl.ItemsSource = itemCollection;

    if (Window.Current.Bounds.Width < 640)
    {
        ScenarioControl.SelectedIndex = -1;
    }
    else
    {
        ScenarioControl.SelectedIndex = 0;
    }
}
```

Это важный и интересный метод, потому что именно в нем наша коллекция объектов **Scenario** присваивается пользовательскому интерфейсу. Код C# создает [**System.Collections.Generic.List**](/dotnet/api/system.collections.generic.list-1) объектов **Scenario**, и назначает их свойству [**ItemsSource**](/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) элемента **ListBox** (который является компонентом управления элементами). Кроме того, для создания заголовка каждого объекта **Scenario** в C# используется [интерполяция строк](/dotnet/csharp/language-reference/tokens/interpolated) (обратите внимание на использование специального символа `$`).

В C++/WinRT мы сделаем **OnNavigatedTo** общедоступным методом **MainPage**. Мы добавим элемент заглушки **ListBox** в XAML, чтобы сборка была успешной. После этого примера кода мы рассмотрим некоторые детали.

```xaml
<!-- MainPage.xaml -->
...
<StackPanel ...>
    ...
    <ListBox x:Name="ScenarioControl" />
</StackPanel>
...
```

```cppwinrt
// MainPage.h
...
void OnNavigatedTo(Windows::UI::Xaml::Navigation::NavigationEventArgs const& e);
...

// MainPage.cpp
...
using namespace winrt::Windows::UI::Xaml;
using namespace winrt::Windows::UI::Xaml::Navigation;
...
void MainPage::OnNavigatedTo(NavigationEventArgs const& /* e */)
{
    auto itemCollection = winrt::single_threaded_observable_vector<IInspectable>();
    int i = 1;
    for (auto s : MainPage::scenarios())
    {
        s.Title = winrt::to_hstring(i++) + L") " + s.Title;
        itemCollection.Append(winrt::box_value(s));
    }
    ScenarioControl().ItemsSource(itemCollection);

    if (Window::Current().Bounds().Width < 640)
    {
        ScenarioControl().SelectedIndex(-1);
    }
    else
    {
        ScenarioControl().SelectedIndex(0);
    }
}
...
```

Мы снова вызываем функцию [winrt::single_threaded_observable_vector](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector), однако на этот раз для создания коллекции [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable). Это было частью решения, которое мы сделали, чтобы выполнить упаковку объектов **Scenario** на основе JIT.

И вместо того чтобы использовать здесь интерполяцию [строк](/dotnet/csharp/language-reference/tokens/interpolated) на C#, мы используем комбинацию функции [**to_hstring**](/uwp/cpp-ref-for-winrt/to-hstring) и [оператора конкатенации](/uwp/cpp-ref-for-winrt/hstring#operator-concatenation-operator) из **winrt::hstring**.

#### <a name="isapplicationwindowactive"></a>**isApplicationWindowActive**

В C# **isApplicationWindowActive** — это простое частное поле `bool`, принадлежащее классу **MainPage**, и оно определено в `SampleConfiguration.cs`. По умолчанию имеет значение `false`. В C++/WinRT мы сделаем его общедоступным статическим полем **SampleState** (по причинам, которые мы уже описывали) в файлах `SampleConfiguration.h` и `SampleConfiguration.cpp` с одним и тем же значением по умолчанию.

Мы уже видели, как объявлять, определять и инициализировать статическое поле. Чтобы освежить знания, вспомните, что мы делали с полем **isClipboardContentChangedEnabled**, и сделайте то же самое с полем **isApplicationWindowActive**.

#### <a name="needtoprintclipboardformat"></a>**needToPrintClipboardFormat**

Тот же шаблон, что и **isApplicationWindowActive** (см. заголовок непосредственно перед этим).

#### <a name="button_click"></a>**Button_Click**

**Button_Click** — это частный метод (обработка событий) класса C# **MainPage**, который определен в `MainPage.xaml.cs`. В данном случае, вместе с XAML **SplitView**, на который он ссылается.

```xaml
<!-- MainPage.xaml -->
...
<SplitView x:Name="Splitter" ... />
...
```

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    Splitter.IsPaneOpen = !Splitter.IsPaneOpen;
}
```

А также эквивалент, перенесенный в C++/WinRT. Обратите внимание, что в версии C++/WinRT обработчик событий — `public` (как вы видите, он объявляется *до* объявлений `private:`).

```xaml
<!-- MainPage.xaml -->
...
<StackPanel ...>
    ...
    <SplitView x:Name="Splitter" />
</StackPanel>
...
```

```cppwinrt
// MainPage.h
...
    void Button_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const& e);
private:
...

// MainPage.cpp
void MainPage::Button_Click(Windows::Foundation::IInspectable const& /* sender */, Windows::UI::Xaml::RoutedEventArgs const& /* e */)
{
    Splitter().IsPaneOpen(!Splitter().IsPaneOpen());
}
```

#### <a name="displaychangedformats"></a>**DisplayChangedFormats**

В C# **DisplayChangedFormats** является частным методом, принадлежащим классу **MainPage** и определенным в `SampleConfiguration.cs`.

```csharp
private void DisplayChangedFormats()
{
    string output = "Clipboard content has changed!" + Environment.NewLine;
    output += BuildClipboardFormatsOutputString();
    NotifyUser(output, NotifyType.StatusMessage);
}
```

В C++/WinRT мы сделаем его частным статическим полем**SampleState** (оно не обращается к членам экземпляра) в файлах `SampleConfiguration.h` и `SampleConfiguration.cpp`. Код на C# для этого метода не использует **System.Text.StringBuilder**; но он делает достаточно строкового форматирования, чтобы для версии на C++/WinRT можно было использовать **std::wostringstream**.

Вместо статического свойства [**System.Environment.NewLine**](/dotnet/api/system.environment.newline), которое используется в коде C#, мы будем вставлять в поток вывода стандартный C++ `std::endl` (символ новой строки).

```cppwinrt
// SampleConfiguration.h
...
private:
    static void DisplayChangedFormats();
...

// SampleConfiguration.cpp
void SampleState::DisplayChangedFormats()
{
    std::wostringstream output;
    output << L"Clipboard content has changed!" << std::endl;
    output << BuildClipboardFormatsOutputString().c_str();
    MainPage::Current().NotifyUser(output.str(), NotifyType::StatusMessage);
}
```

Проектирование версии C++/WinRT, описанной выше, может быть неэффективным. Сначала мы создадим **std::wostringstream**. Однако мы также вызываем метод **BuildClipboardFormatsOutputString** (который был перенесен ранее). Этот метод создает собственный **std::wostringstream**. Он превращает свой поток в **winrt::hstring** и возвращает его. Мы вызываем функцию [**hstring::c_str**](/uwp/cpp-ref-for-winrt/hstring#hstringc_str-function), чтобы она возвратила **hstring** обратно в строку в стиле C, а затем вставила ее в наш поток. Было бы более эффективно создать только один **std::wostringstream** и передать (ссылку на него), чтобы методы могли вставлять строки в него напрямую.

Это то, что мы делаем в версии C++/WinRT примера [исходного кода](https://github.com/microsoft/Windows-universal-samples/tree/master/Samples/Clipboard/cppwinrt) Clipboard. В этом исходном коде есть новый частный статический метод с именем **SampleState::AddClipboardFormatsOutputString**, который принимает и преобразует ссылку в поток вывода. А затем методы **SampleState::DisplayChangedFormats** и **SampleState::BuildClipboardFormatsOutputString**, чтобы вызвать этот новый метод. Он функционально эквивалентен перечислениям кода в этом разделе, но более эффективен.

#### <a name="footer_click"></a>**Footer_Click**

**Footer_Click** — это асинхронный обработчик событий, принадлежащий классу C# **MainPage**, который определен в `MainPage.xaml.cs`. Приведенный ниже пример кода функционально эквивалентен методу в скачанном исходном коде. Но здесь он распакован с одной строки на четыре, чтобы было легче увидеть, что он делает, и, следовательно, как его следует переносить.

```csharp
async void Footer_Click(object sender, RoutedEventArgs e)
{
    var hyperlinkButton = (HyperlinkButton)sender;
    string tagUrl = hyperlinkButton.Tag.ToString();
    Uri uri = new Uri(tagUrl);
    await Windows.System.Launcher.LaunchUriAsync(uri);
}
```

Хотя, технически, метод является асинхронным, он не выполняет никаких действий после `await`, поэтому не требуется `await` (и ключевое слово `async`). Скорее всего, их используют, чтобы избежать сообщения IntelliSense в Visual Studio.

Эквивалентный метод C++/WinRT также будет асинхронным (поскольку он вызывает [**Launcher.LaunchUriAsync**](/uwp/api/windows.system.launcher.launchuriasync)). Однако ему не нужно ни `co_await`, ни возвращать асинхронный объект. Сведения о `co_await` и асинхронных объектах см. в статье [Параллельная обработка и выполнение асинхронных операций с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/concurrency).

Теперь давайте поговорим о том, что делает метод. Поскольку это обработчик события **Click** от **HyperlinkButton**, объект с именем *sender* на самом деле является **HyperlinkButton**. Поэтому приведение является безусловным (его можно было бы выразить как `sender as HyperlinkButton`). Далее мы получаем значение свойства **Тега** (если вы посмотрите на XAML-разметку в проекте C#, то увидите, что оно задано в строку, представляющую собой URL-адрес в Интернете). Несмотря на то, что свойство **FrameworkElement.Tag** (**HyperlinkButton** является **FrameworkElement**) имеет тип **object**, в C# мы можем преобразовать его в строку с помощью [**Object.ToString**](/dotnet/api/system.object.tostring). В полученной строке мы создадим **URI** объекта. И, наконец, (с помощью Shell) мы запускаем браузер и переходим по URL-адресу.

Ниже приведен метод, перенесенный в C++/WinRT (опять же, развернутый для ясности), после чего следует описание сведений.

```cppwinrt
// pch.h
...
#include "winrt/Windows.System.h"
...

// MainPage.h
...
    void Footer_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const& e);
private:
...

// MainPage.cpp
...
using namespace winrt::Windows::Foundation;
using namespace winrt::Windows::UI::Xaml::Controls;
...
void MainPage::Footer_Click(Windows::Foundation::IInspectable const& sender, Windows::UI::Xaml::RoutedEventArgs const&)
{
    auto hyperlinkButton{ sender.as<HyperlinkButton>() };
    hstring tagUrl{ winrt::unbox_value<hstring>(hyperlinkButton.Tag()) };
    Uri uri{ tagUrl };
    Windows::System::Launcher::LaunchUriAsync(uri);
}
```

Как всегда, мы делаем обработчик события `public`. Мы используем функцию [**as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) в объекте *sender*, чтобы преобразовать его в **HyperlinkButton**. В C++/WinRT свойство **Тега** является [**IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) (эквивалент [**Object**](/dotnet/api/system.object)). Но в **IInspectable** нет **Tostring**. Вместо этого нам нужно распаковать **IInspectable** в скалярное значение (в данном случае строка). Дополнительные сведения об упаковке и распаковке см. в статъе [Упаковка-преобразование и распаковка-преобразование скалярных значений в IInspectable с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/boxing).

Последние две строки передают шаблоны переноса, которые мы видели ранее, и они практически перекликаются с версией на C#.

#### <a name="handleclipboardchanged"></a>**HandleClipboardChanged**

В переносе этого метода нет ничего нового. Вы можете сравнить версии C# и C++/WinRT в образце исходного кода.

#### <a name="onclipboardchanged-and-onwindowactivated"></a>**OnClipboardChanged** и **OnWindowActivated**

До сих пор у нас есть только пустые заглушки для этих двух обработчиков событий. Но перенос этих компонентов прост, и он не создает ничего нового для обсуждения.

#### <a name="scenariocontrol_selectionchanged"></a>**ScenarioControl_SelectionChanged**

Это еще один частный обработчик событий, принадлежащий классу C# **MainPage** и определенный в `MainPage.xaml.cs`. В C++/WinRT мы сделаем его общедоступным и реализуем его в `MainPage.h` и `MainPage.cpp`.

Для этого метода требуется **MainPage::navigating**, который является закрытым логическим полем, инициализированным в `false`. И вам понадобится **Frame** в `MainPage.xaml`, названный *ScenarioFrame*. Но помимо этих деталей, перенос этого метода не раскрывает новые методы.

#### <a name="updatestatus"></a>**UpdateStatus**

У нас есть только заглушка для **MainPage.UpdateStatus**. Перенос его реализации, опять же, охватывает самое старое заземление. Следует отметить, что если в C# можно сравнить **строку** и **String.Empty**, то в C++/WinRT вместо этого вызывается функция [**winrt::hstring::empty**](/uwp/cpp-ref-for-winrt/hstring#hstringempty-function). Другой заключается в том, что `nullptr` является стандартным аналогом C++ для C#`null`.

Остальную часть переноса можно выполнить с помощью уже названных методов. Ниже приведен список типов действий, которые необходимо выполнить перед компиляцией переданной версии этого метода.

- В `MainPage.xaml`, добавьте **Border** с именем *StatusBorder*.
- В `MainPage.xaml`, добавьте **TextBlock** с именем *StatusBlock*.
- В `MainPage.xaml`, добавьте **StackPanel** с именем *StatusPanel*.
- В `pch.h`, добавьте `#include "winrt/Windows.UI.Xaml.Media.h"`.
- В `pch.h`, добавьте `#include "winrt/Windows.UI.Xaml.Automation.Peers.h"`.
- В `MainPage.cpp`, добавьте `using namespace winrt::Windows::UI::Xaml::Media;`.
- В `MainPage.cpp`, добавьте `using namespace winrt::Windows::UI::Xaml::Automation::Peers;`.

### <a name="copy-the-xaml-and-styles-necessary-to-finish-up-porting-mainpage"></a>Скопируйте XAML и стили, необходимые для завершения переноса **MainPage**

В идеальном случае для XAML можно использовать *ту же* XAML-разметку в C# и проекте C++/WinRT. В одном из этих случаев используется пример Clipboard.

В своем файле `Styles.xaml` пример Clipboard имеет XAML **ResourceDictionary** стилей, которые применяются к кнопкам, меню и другим элементам пользовательского интерфейса по всему пользовательскому интерфейсу приложения. Страница `Styles.xaml` объединена в `App.xaml`. Для пользовательского интерфейса еще есть стандартная `MainPage.xaml` отправная точка, которую мы уже вкратце видели. Теперь мы можем повторно использовать эти три файла `.xaml` без изменений в C++/WinRT версии проекта.

Как и в случае с файлами актива, вы можете ссылаться на одни и те же общие файлы XAML из нескольких версий приложения. В этом пошаговом руководстве, ради простоты, мы будем копировать файлы в проект C++/WinRT и добавлять их таким образом.

Перейдите в папку `\Clipboard_sample\SharedContent\xaml`, выберите и скопируйте `App.xaml` и `MainPage.xaml`, а затем вставьте эти два файла в папку `\Clipboard\Clipboard` проекта C++/WinRT, заменив файлы при появлении соответствующего запроса.

Добавьте новую папку в проект C++/WinRT, непосредственно под узлом проекта и с именем `Styles`. Перейдите в папку `\Clipboard_sample\SharedContent\xaml`, выберите и скопируйте `Styles.xaml`и вставьте этот файл в папку `\Clipboard\Clipboard\Styles` проекта C++/WinRT. Щелкните правой кнопкой мыши папку `Styles` (в обозревателе решений в проекте C++/WinRT) > **Добавить** > **Существующий элемент...** и перейдите к `\Clipboard\Clipboard\Styles`. В средстве выбора файлов выберите `Styles` и щелкните **Добавить**.

Мы закончили перенос **MainPage**, и если вы следовали этим шагам, то теперь ваш проект C++/WinRT будет собран и запустится.

## <a name="consolidate-your-idl-files"></a>Объединение файлов `.idl`

В дополнение к стандартной начальной точке `MainPage.xaml` для пользовательского интерфейса в примере Clipboard содержится пять страниц XAML, зависящих от сценария, а также соответствующие файлы кода программной части. Мы будем повторно использовать фактическую разметку XAML всех этих страниц в C++/WinRT версии проекта, не изменяя ее. Мы рассмотрим сведения о переносе кода программной части в следующих нескольких основных разделах. Но до этого давайте поговорим об IDL.

В консолидации классов среды выполнения в один IDL-файл есть некоторая ценность (см. раздел [Разделение классов среды выполнения на файлы Midl (.idl)](/windows/uwp/cpp-and-winrt-apis/author-apis#factoring-runtime-classes-into-midl-files-idl)). Далее мы будем объединять содержимое `CopyFiles.idl`, `CopyImage.idl`, `CopyText.idl`, `HistoryAndRoaming.idl`, а также `OtherScenarios.idl`, перемещая этот IDL в один файл с именем `Project.idl` (а затем удаляя исходные файлы).

Пока мы делаем это, давайте также удалим автоматически созданное фиктивное свойство (`Int32 MyProperty;` и его реализацию) из каждой из этих пяти типов страниц XAML.

Сначала добавьте новый элемент файла **Midl (.idl)** в проект C++/WinRT. Назовите его `Project.idl`. Удалите содержимое по умолчанию `Project.idl`, а на его месте вставьте приведенный ниже список.

```idl
// Project.idl
namespace SDKTemplate
{
    [default_interface]
    runtimeclass CopyFiles : Windows.UI.Xaml.Controls.Page
    {
        CopyFiles();
    }

    [default_interface]
    runtimeclass CopyImage : Windows.UI.Xaml.Controls.Page
    {
        CopyImage();
    }

    [default_interface]
    runtimeclass CopyText : Windows.UI.Xaml.Controls.Page
    {
        CopyText();
    }

    [default_interface]
    runtimeclass HistoryAndRoaming : Windows.UI.Xaml.Controls.Page
    {
        HistoryAndRoaming();
    }

    [default_interface]
    runtimeclass OtherScenarios : Windows.UI.Xaml.Controls.Page
    {
        OtherScenarios();
    }
}
```

Как видите, это просто копия содержимого отдельных файлов `.idl`, все в одном пространстве имен, и с удалением `MyProperty` из каждого класса выполнения.

В обозревателе решений в Visual Studio выберите все исходные IDL-файлы (`CopyFiles.idl`, `CopyImage.idl`, `CopyText.idl`, `HistoryAndRoaming.idl`, `OtherScenarios.idl`) и выберите **Изменить** > **Удалить** (выберите **Удалить** в диалоговом окне).

Наконец,&mdash;и для завершения удаления `MyProperty`&mdash;в файлах `.h` и `.cpp` для каждого из пяти типов страниц XAML удалите объявления и определения функций метода доступа `int32_t MyProperty()` и метода изменения `void MyProperty(int32_t)`.

## <a name="copyfiles"></a>**CopyFiles**

В проекте C# тип страницы XAML **CopyFiles** реализован в файлах исходного кода `CopyFiles.xaml` и `CopyFiles.xaml.cs`. Давайте рассмотрим каждый из членов **CopyFiles** по очереди.

### <a name="rootpage"></a>**rootPage**

Это частное поле.

```csharp
// CopyFiles.xaml.cs
...
public sealed partial class CopyFiles : Page
{
    MainPage rootPage = MainPage.Current;
    ...
}
...
```

В C++/WinRT мы можем определить и инициализировать его следующим образом.

```cppwinrt
// CopyFiles.h
...
struct CopyFiles : CopyFilesT<CopyFiles>
{
    ...
private:
    SDKTemplate::MainPage rootPage{ MainPage::Current() };
};
...
```

Опять же (как и с **MainPage::current**), **CopyFiles::rootPage** объявлен типом **SDKTemplate::MainPage**, который является проецируемым типом, а не типом реализации.

### <a name="copyfiles-the-constructor"></a>**CopyFiles** (конструктор)

В проекте C++/WinRT тип **CopyFiles** уже имеет конструктор, содержащий нужный нам код (он просто вызывает **InitializeComponent**).

### <a name="copybutton_click"></a>**CopyButton_Click**

Метод C# **CopyButton_Click** является обработчиком событий, и по ключевому слову `async` в его подписи можно определить, что метод выполняет асинхронную работу. В C++/WinRT мы реализуем асинхронный метод в виде *сопрограммы*. Общие сведения о параллелизме в C++/WinRT, а также описание того, что такое *сопрограмма*, см. в статье [Параллельные обработка и выполнение асинхронных операций с помощью C++/WinRT](/windows/uwp/cpp-and-winrt-apis/concurrency).

Обычно после завершения сопрограмм нужно запланировать дальнейшую работу, и в таких случаях сопрограмма будет возвращать некоторый тип асинхронного объекта, который может быть ожидаемым, и при необходимости сообщать о ходе выполнения. Но эти рекомендации обычно не применяются к обработчику событий. Поэтому при наличии обработчика событий, выполняющего асинхронные операции, его можно реализовать как сопрограмму, возвращающую **winrt::fire_and_forget**. Дополнительные сведения см. в разделе [Принцип "Выполнил и забыл"](/windows/uwp/cpp-and-winrt-apis/concurrency-2#fire-and-forget).

Хотя идея сопрограммы по принципу "Выполнил и забыл" заключается в том, что вам не нужно ожидать завершения работы, поскольку она все равно продолжается (или приостанавливается в ожидании возобновления) в фоновом режиме. Из реализации C# видно, что **CopyButton_Click** зависит от указателя `this` (он обращается к члену данных экземпляра `rootPage`). Поэтому мы должны быть уверены, что указатель `this` (указатель на объект **CopyFiles**) превосходит сопрограмму **CopyButton_Click**. В ситуации, аналогичной этому примеру приложения, когда пользователь перемещается между страницами пользовательского интерфейса, мы не можем напрямую контролировать время существования этих страниц. Если страница **CopyFiles** будет уничтожена (путем перехода от нее), в то время как **CopyButton_Click** все еще перемещается в фоновом потоке, доступ к`rootPage` не будет безопасным. Чтобы обеспечить правильное выполнение сопрограммы, необходимо получить строгую ссылку на указатель `this` и сохранить эту ссылку на время выполнения сопрограммы. Дополнительные сведения см. в статье [Сильные и слабые ссылки в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/weak-references).

Если взглянуть на версию образца на C++/WinRT, в **CopyFiles::CopyButton_Click** вы увидите, что это делается с помощью простого объявления в стеке.

```cppwinrt
fire_and_forget CopyFiles::CopyButton_Click(IInspectable const&, RoutedEventArgs const&)
{
    auto lifetime{ get_strong() };
    ...
}
```

Рассмотрим другие аспекты передаваемого кода, которые заслуживают внимания.

В коде мы создаем экземпляр объекта [**FileOpenPicker**](/uwp/api/windows.storage.pickers.fileopenpicker), а через две строки получаем доступ к свойству этого объекта [**FileTypeFilter**](/uwp/api/windows.storage.pickers.fileopenpicker.filetypefilter). Тип возвращаемого значения этого свойства реализует **IVector** строк. И в этом **IVector** мы вызываем метод [IVector<T>.ReplaceAll(T[])](/uwp/api/windows.foundation.collections.ivector-1.replaceall). Интересным аспектом является значение, которое мы передаем методу, в котором ожидается массив. Вот строка кода.

```cppwinrt
filePicker.FileTypeFilter().ReplaceAll({ L"*" });
```

Значение, которое мы передаем (`{ L"*" }`), является стандартным *списком инициализаторов* C++. В данном случае он содержит один объект, однако список инициализатора может содержать любое количество объектов, разделенных запятыми. Элементы C++/WinRT, которые предоставляют возможности для передачи списка инициализаторов в метод, такой как этот, описаны в разделе [Стандартные списки инициализаторов](/windows/uwp/cpp-and-winrt-apis/std-cpp-data-types#standard-initializer-lists).

Мы переносим ключевое слово C# `await` в `co_await` C++/WinRT. Ниже приведен пример кода.

```cppwinrt
auto storageItems{ co_await filePicker.PickMultipleFilesAsync() };
```

Далее рассмотрим эту строку кода C#.

```csharp
dataPackage.SetStorageItems(storageItems);
```

C# способен неявно преобразовать **IReadOnlyList<StorageFile>** , представленный *storageItems*, в **IEnumerable<IStorageItem>** , ожидаемый [**DataPackage.SetStorageItems**](/uwp/api/windows.applicationmodel.datatransfer.datapackage.setstorageitems). Однако в C++/WinRT нам нужно явно привести тип от **IVectorView<StorageFile>** до **IIterable<IStorageItem>** . Итак, у нас есть еще один пример функции [**as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function) в действии.

```cppwinrt
dataPackage.SetStorageItems(storageItems.as<IVectorView<IStorageItem>>());
```

Там, где в C# мы используем ключевое слово `null` (например, `Clipboard.SetContentWithOptions(dataPackage, null)`), в C++/WinRT мы используем `nullptr` (например, `Clipboard::SetContentWithOptions(dataPackage, nullptr)`).

### <a name="pastebutton_click"></a>**PasteButton_Click**

Это еще один обработчик событий в виде сопрограммы по принципу "Выполнил и забыл". Рассмотрим аспекты переносимого кода, которые заслуживают внимания.

В C# версии примера мы перехватываем исключения с помощью `catch (Exception ex)`. В переданном коде C++/WinRT вы увидите выражение `catch (winrt::hresult_error const& ex)`. Дополнительные сведения о [**winrt::hresult_error**](/uwp/cpp-ref-for-winrt/error-handling/hresult-error) и о работе с ней см. в разделе [Обработка ошибок в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/error-handling).

Пример проверки того, является ли объект C# `null` или не является `if (storageItems != null)`. В C++/WinRT можно положиться на оператор преобразования в `bool`, который выполняет внутреннюю проверку на соответствие `nullptr`.

Ниже приведена немного упрощенная версия фрагмента кода из перенесенной C++/WinRT версии примера.

```cppwinrt
std::wostringstream output;
output << std::wstring_view(ApplicationData::Current().LocalFolder().Path());
```

Создание **std::wstring_view** из **winrt::hstring**, подобное этому, демонстрирует альтернативу вызову функции [**hstring::c_str**](/uwp/cpp-ref-for-winrt/hstring#hstringc_str-function) (чтобы преобразовать **winrt::hstring** в строку в стиле C). Эта альтернатива работает благодаря оператору преобразования **hstring**[ в **std::wstring_view**](/uwp/cpp-ref-for-winrt/hstring#hstringoperator-stdwstring_view).

Рассмотрим этот фрагмент C#.

```csharp
var file = storageItem as StorageFile;
if (file != null)
...
```

Мы уже несколько раз видели, как для переноса ключевого слова C# `as` в C++/WinRT использовалась функция [**as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknownas-function). Эта функция создает исключение, если приведение завершается ошибкой. Однако если мы хотим, чтобы приведение возвращало `nullptr` в случае сбоя (чтобы мы могли обработать это условие в коде), вместо этого мы используем функцию [**try_as**](/uwp/cpp-ref-for-winrt/windows-foundation-iunknown#iunknowntry_as-function).

```cppwinrt
auto file{ storageItem.try_as<StorageFile>() };
if (file)
...
```

### <a name="copy-the-xaml-necessary-to-finish-up-porting-copyfiles"></a>Скопируйте XAML, необходимый для завершения переноса **CopyFiles**

Теперь вы можете выбрать все содержимое файла `CopyFiles.xaml` из проекта C# и вставить его в файл `CopyFiles.xaml` в проекте C++/WinRT (заменив существующее содержимое этого файла в проекте C++/WinRT).

Наконец, измените `CopyFiles.h` и `.cpp` и удалите фиктивную функцию **ClickHandler**, поскольку мы только что переписали соответствующую разметку XAML.

Теперь мы завершили перенос **CopyFiles**, и если вы следовали шагам, проект C++/WinRT будет создан и запущен, а сценарий **CopyFiles** будет работать.

## <a name="copyimage"></a>**CopyImage**

Чтобы перенести тип страницы XAML **CopyImage**, выполните тот же процесс, что и для **CopyFiles**. При переносе **CopyImage** будет использоваться [*оператор using*](/dotnet/csharp/language-reference/keywords/using-statement) C#, что гарантирует, что объекты, реализующие интерфейс [**IDisposable**](/dotnet/api/system.idisposable), будут удалены правильно.

```csharp
if (imageReceived != null)
{
    using (var imageStream = await imageReceived.OpenReadAsync())
    {
        ... // Pass imageStream to other APIs, and do other work.
    }
}
```

Эквивалентный интерфейс в C++/WinRT — [**IClosable**](/uwp/api/windows.foundation.iclosable), с его одним методом **Close**. Ниже указан эквивалент C++/WinRT для приведенного выше C# кода.

```cppwinrt
if (imageReceived)
{
    auto imageStream{ co_await imageReceived.OpenReadAsync() };
    ... // Pass imageStream to other APIs, and do other work.
    imageStream.Close();
}
```

Объекты C++/WinRT реализуют **IClosable** в основном для помощи при использовании языков, в которых отсутствует детерминированное завершение. C++/WinRT имеет детерминированное завершение, поэтому зачастую нам не нужно вызывать **IClosable::Close** при написании C++/WinRT. Но бывают случаи, когда его можно вызвать, и сейчас один из них. Здесь идентификатор *imageStream* — это программа-оболочка с автоматическим подсчетом ссылок вокруг объекта базовой среды выполнения Windows (в данном случае это объект, реализующий [**IRandomAccessStreamWithContentType**](/uwp/api/windows.storage.streams.irandomaccessstreamwithcontenttype)). Несмотря на то что метод завершения *imageStream* (его деструктор) будет выполняться в конце включающей области (фигурные скобки), мы не можем быть уверенными в том, что метод завершения будет вызывать функцию **Close**. Это связано с тем, что мы передавали *imageStream* другим API и они все еще могут содействовать при подсчете ссылок базового объекта среды выполнения Windows. Так что в этом случае лучше всего явно вызвать **Close**. Подробнее см. в разделе [Нужно ли вызывать IClosable::Close для классов среды выполнения, которые я использую?](/windows/uwp/cpp-and-winrt-apis/faq#do-i-need-to-call-iclosableclose-on-runtime-classes-that-i-consume).

Далее рассмотрим выражение C# `(uint)(imageDecoder.OrientedPixelWidth * 0.5)`, которое можно найти в обработчике событий **OnDeferredImageRequestedHandler**. Это выражение умножает `uint` на `double`, что приводит к `double`. Затем этот объект приводится к `uint`. В C++/WinRT мы *можем* использовать схожее приведение в стиле С (`(uint32_t)(imageDecoder.OrientedPixelWidth() * 0.5)`), однако предпочтительнее четко указать, какое именно приведение мы собираемся использовать, и в этом случае мы сделаем это с помощью `static_cast<uint32_t>(imageDecoder.OrientedPixelWidth() * 0.5)`.

C# версия **CopyImage.OnDeferredImageRequestedHandler** содержит предложение `finally`, а не предложение `catch`. Мы немного подробнее рассмотрели версию C++/WinRT и реализовали предложение `catch`, чтобы можно было сообщить о том, была ли отложенная визуализация успешной.

Перенос оставшейся части этой XAML-страницы не нуждается в новых обсуждениях. Как и в случае с **CopyFiles**, последний шаг в переносе — выделить все содержимое `CopyImage.xaml`и вставить его в тот же файл в проекте C++/WinRT.

## <a name="copytext"></a>**CopyText**

Вы можете переносить `CopyText.xaml` и `CopyText.xaml.cs` с помощью уже изученных методов.

## <a name="historyandroaming"></a>**HistoryAndRoaming**

Существует ряд вопросов, которые возникают при переносе типа страницы **HistoryAndRoaming** XAML.

Сначала посмотрите на исходный код C# и следуйте потоку элемента управления от **OnNavigatedTo** через обработчик событий **OnHistoryEnabledChanged** и, наконец, к асинхронной функции **CheckHistoryAndRoaming** (ее выполнения не нужно ждать, поскольку это принцип "Выполнить и забыть"). Поскольку **CheckHistoryAndRoaming** является асинхронным, необходимо внимательно изучить в C++/WinRT время существования указателя `this`. Результат можно увидеть, если взглянуть на реализацию в файле исходного кода `HistoryAndRoaming.cpp`. Во-первых, при вложении делегатов в **Clipboard::HistoryEnabledChanged** и **Clipboard::RoamingEnabledChanged** мы принимаем только слабую ссылку на объект страницы **HistoryAndRoaming**. Для этого создается делегат с зависимостью от значения, возвращаемого из [**winrt::get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsget_weak-function), а не от указателя `this`. Это означает, что сам делегат, который в конечном итоге вызывает асинхронный код, не сохраняет в памяти страницу **HistoryAndRoaming** при переходе от нее.

А во-вторых, когда мы, наконец, дойдем до сопрограммы типа "Выполнил и забыл" **CheckHistoryAndRoaming**, первое, что мы сделаем — примем сильную ссылку на `this`, чтобы гарантировать, что страница **HistoryAndRoaming** будет существовать по крайней мере до завершения сопрограммы. Дополнительные сведения об обоих описанных аспектах см. в разделе [Сильные и слабые ссылки в C++/WinRT](/windows/uwp/cpp-and-winrt-apis/weak-references).

Мы нашли другой интересный момент при переносе **CheckHistoryAndRoaming**. Он содержит код для обновления пользовательского интерфейса; поэтому необходимо убедиться, что мы выполняем действие в основном потоке пользовательского интерфейса. Как правило, асинхронный метод может выполняться и (или) возобновляться в любом произвольном потоке. В C# решение предназначено для вызова [**CoreDispatcher.RunAsync**](/uwp/api/windows.ui.core.coredispatcher.runasync) и обновления пользовательского интерфейса из лямбда-функции. В C++/WinRT для приостановки сопрограммы и немедленного возобновления на главном потоке пользовательского интерфейса можно использовать функцию [**winrt::resume_foreground**](/uwp/cpp-ref-for-winrt/resume-foreground) с `this`[**диспетчером**](/uwp/api/windows.ui.xaml.dependencyobject.dispatcher) указателя.

Соответствующее выражение — `co_await winrt::resume_foreground(Dispatcher());`. В качестве альтернативы, хотя и с меньшей ясностью, мы могли бы просто выразить это как `co_await Dispatcher();`. Укороченная версия достигается благодаря оператору преобразования, поставляемому C++/WinRT.

## <a name="otherscenarios"></a>**OtherScenarios**

Вы можете переносить `OtherScenarios.xaml` и `OtherScenarios.xaml.cs` с помощью уже изученных методов.

## <a name="conclusion"></a>Заключение

Надеемся, что это пошаговое руководство вооружило вас достаточной информацией о переносе и технологиях, которые вы теперь можете использовать для переноса ваших собственных приложений C# на C++/WinRT. С помощью обновителя вы можете продолжать ссылаться на предыдущие (C#) и последующие (C++/WinRT) версии исходного кода в примере Cliboard и сравнивать их бок о бок, чтобы увидеть соответствие.

## <a name="related-topics"></a>Связанные темы
* [Переход на C++/WinRT с C#](/windows/uwp/cpp-and-winrt-apis/move-to-winrt-from-csharp)