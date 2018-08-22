---
author: stevewhims
Description: If you want your app to support different display languages, and you have string literals in your code or XAML markup or app package manifest, then move those strings into a Resources File (.resw). You can then make a translated copy of that Resources File for each language that your app supports.
title: Локализация строк в манифесте пакета приложения и интерфейсе пользователя
ms.assetid: E420B9BB-C0F6-4EC0-BA3A-BA2875B69722
label: Localize strings in your UI and app package manifest
template: detail.hbs
ms.author: stwhi
ms.date: 11/01/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: c9db9f3ce4397bec6fb0b6b339875c206d17c3fd
ms.sourcegitcommit: f2f4820dd2026f1b47a2b1bf2bc89d7220a79c1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2018
ms.locfileid: "2791739"
---
# <a name="localize-strings-in-your-ui-and-app-package-manifest"></a>Локализация строк в манифесте пакета приложения и интерфейсе пользователя
Дополнительные сведения о преимуществах локализации приложений см. в разделе [Глобализация и локализация](../design/globalizing/globalizing-portal.md).

Если вы хотите, чтобы ваше приложение поддерживало разные языки интерфейса, а в вашем коде или разметке XAML либо манифесте пакета приложения есть строковые литералы, переместите эти строки из кода или разметки в файл ресурсов (.resw). Затем можно создать переведенную копию этого файла ресурсов для каждого языка, поддерживаемого вашим приложением.

Жестко закодированные строковые литералы могут появляться в императивном коде или в разметке XAML, например в виде свойства **Text** объекта **TextBlock**. Они также могут появляться в исходном файле манифесте пакета приложения (файле `Package.appxmanifest`), например в качестве значения для отображаемого имени на вкладке "Приложение" в конструкторе манифеста Visual Studio. Переместите эти строки в файл ресурсов (.resw) и замените жестко закодированные строковые литералы в своем приложении и в манифесте ссылками на идентификаторы ресурсов.

В отличие от ресурсов изображений, где в файле ресурсов содержится только один ресурс изображения, в файле строковых ресурсов содержится *много* строковых ресурсов. Файл строковых ресурсов представляет собой файл ресурсов (.resw), и обычно такой файл ресурсов создается в папке \Strings проекта. Общие сведения об использовании квалификаторов в именах файлов ресурсов (.resw) приводятся в разделе [Адаптация ресурсов для языка, масштаба и других квалификаторов](tailor-resources-lang-scale-contrast.md).

## <a name="create-a-resources-file-resw-and-put-your-strings-in-it"></a>Создайте файл ресурсов (.resw) и поместите в него свои строковые ресурсы
1. Установите язык по умолчанию для приложения.
    1. Открыв решение в Visual Studio, откройте файл `Package.appxmanifest`.
    2. На вкладке «Приложения» убедитесь, что язык по умолчанию задан соответствующим образом (например, en или en-US). На дальнейших этапах предполагается, что вы задали язык по умолчанию en-US.
    <br>**Примечание**. Как минимум, необходимо предоставить строковые ресурсы, локализованные для данного языка по умолчанию. Это и есть ресурсы, которые будут загружены в том случае, если не будет найдено более подходящих для выбранного пользователем языка или языка отображения ресурсов.
2. Создайте файл ресурсов (.resw) для языка по умолчанию.
    1. В узле проекта создайте новую папку и назовите ее Strings.
    2. В разделе `Strings` создайте новую вложенную папку и назовите ее en-US.
    3. В разделе `en-US` создайте новый файл ресурсов (.resw) и убедитесь, что он называется Resources.resw.
    <br>**Примечание**. При наличии файлов ресурсов .NET (.resx), которые требуется перенести, см. раздел [Перенос XAML и пользовательского интерфейса](../porting/wpsl-to-uwp-porting-xaml-and-ui.md#localization-and-globalization).
3.  Откройте `Resources.resw` и добавьте эти строковые ресурсы.

    `Strings/en-US/Resources.resw`

    ![Добавление ресурса (английский язык)](images/addresource-en-us.png)

    В этом примере Greeting является идентификатором строкового ресурса, на который можно ссылаться из разметки, как мы увидим далее. Для идентификатора Greeting предоставляются строка для свойства Text и строка для свойства Width. Greeting.Text — пример идентификатора свойства, так как он соответствует свойству элемента пользовательского интерфейса. Можно также добавить, например Greeting.Foreground в столбце Name и присвоить ему значение Red. Идентификатор Farewell представляет собой идентификатор простого строкового ресурса; у него нет собственных свойств, и его можно загружать из императивного кода, как мы увидим далее. Столбец Comment будет удобен для предоставления особых инструкций переводчикам.

    В этом примере, так как у нас есть идентификатор простого строкового ресурса с именем Farewell, мы не может *также* использовать идентификаторы свойств на основе этого же идентификатора. Таким образом, добавление Farewell.Text вызовет ошибку дубликата элемента при построении `Resources.resw`.

    Идентификаторы ресурсов не чувствительны к регистру и должны быть уникальны для файла ресурсов. Используйте осмысленные идентификаторы ресурсов — это даст дополнительный контекст для перевода. Не меняйте идентификаторы ресурсов после отправки строковых ресурсов на перевод. Переводчики используют идентификаторы ресурсов, чтобы отслеживать добавление, удаление и обновление данных в ресурсах. Изменение идентификаторов ресурсов, называемое также сдвигом идентификаторов ресурсов, приводит к необходимости повторного перевода строк, поскольку ситуация выглядит таким образом, будто прежние строки были удалены и затем добавлены новые.

## <a name="refer-to-a-string-resource-identifier-from-xaml-markup"></a>Ссылка на идентификатор строкового ресурса из разметки XAML
Вы используете [x:Uid directive](../xaml-platform/x-uid-directive.md) для привязки элемента управления или другого элемента в разметке к идентификатору строкового ресурса.

```xaml
<TextBlock x:Uid="Greeting"/>
```

Во время выполнения загружается `\Strings\en-US\Resources.resw` (поскольку сейчас это единственный файл ресурсов в проекте). Директива **X:Uid** для **TextBlock** вызывает выполнение поиска в `Resources.resw` для обнаружения идентификаторов свойств, содержащих идентификатор строкового ресурса Greeting. Обнаруживаются идентификаторы свойств Greeting.Text и Greeting.Width, и их значения применяются к **TextBlock**, переопределяя значения, заданные локально в разметке. Значение Greeting.Foreground также будет применяться, если оно добавлено. Однако только идентификаторы свойств используются для задания свойств элементов разметки XAML, поэтому установка **x:Uid** со значением Farewell для этого TextBlock не даст никакого эффекта. `Resources.resw` *does* содержит идентификатор строкового ресурса Farewell, но не содержит идентификаторы свойств для него.

При назначении идентификатора строкового ресурса элементу XAML следите за тем, чтобы *все* идентификаторы свойств для этого идентификатора подходили этому элементу XAML. Например, если установить `x:Uid="Greeting"` для **TextBlock**, то значение Greeting.Text будет обработано, поскольку у типа **TextBlock** есть свойство Text. Но если вы задали `x:Uid="Greeting"` для **Button**, то Greeting.Text приведет к ошибке во время выполнения, поскольку у типа **Button** нет свойства Text. Возможное решение проблемы в этом случае — создать идентификатор свойства с именем ButtonGreeting.Content и задать `x:Uid="ButtonGreeting"` для **Button**.

Вместо определения значения **Width** из файла ресурсов, возможно, логично будет разрешить динамическую адаптацию размера элементов управления по содержимому.

**Примечание**. Для [присоединенных свойств](../xaml-platform/attached-properties-overview.md) требуется особый синтаксис в столбце Name в файле .resw. Например, вот что надо ввести в столбце Name, чтобы задать значение для присоединенного свойства [**AutomationProperties.Name**](/uwp/api/windows.ui.xaml.automation.automationproperties.NameProperty) для идентификатора Greeting.

```xml
Greeting.[using:Windows.UI.Xaml.Automation]AutomationProperties.Name
```

## <a name="refer-to-a-string-resource-identifier-from-code"></a>Ссылка на идентификатор строкового ресурса из кода
Можно загрузить строковый ресурс явным образом по идентификатору простого строкового ресурса.

> [!NOTE]
> Если вы вызываете любой метод **GetForCurrentView**, который *может* выполняться в фоновом или рабочем потоке, защитите этот вызов тестом `if (Windows.UI.Core.CoreWindow.GetForCurrentThread() != null)`. Вызов **GetForCurrentView** из фонового или рабочего потока приводит к исключению "Не удается создать *&lt;typename&gt; для потоков без CoreWindow*".

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView();
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("Farewell");
```

```cppwinrt
auto resourceLoader{ Windows::ApplicationModel::Resources::ResourceLoader::GetForCurrentView() };
myXAMLTextBlockElement().Text(resourceLoader.GetString(L"Farewell"));
```

```cpp
auto resourceLoader = Windows::ApplicationModel::Resources::ResourceLoader::GetForCurrentView();
this->myXAMLTextBlockElement->Text = resourceLoader->GetString("Farewell");
```

Тот же самый код можно использовать из проекта с библиотекой классов (Universal Windows) или [библиотекой среды выполнения Windows (универсальная платформа Windows)](../winrt-components/index.md). Во время выполнения загружаются ресурсы приложения, в котором размещается библиотека. Рекомендуется, чтобы библиотека загружала ресурсы из приложения, в котором она расположена, поскольку выше вероятность, что это приложение локализовано в большей степени. Если библиотеке не требуется предоставлять ресурсы, то она должна обеспечить приложению возможность заменять их при вызове.

Если Сегментированные имя ресурса (он содержит «.» символов), затем заменить точек с косой черты («/») символов в имени ресурса. Идентификаторы свойств, например, содержать несколько точек; Поэтому необходимо сделать этот substition, чтобы загрузить один из них из кода.

```csharp
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("Fare/Well"); // <data name="Fare.Well" ...> ...
```

В случае сомнения [MakePri.exe](makepri-exe-command-options.md) можно использовать для записи файла PRI вашего приложения. Каждый ресурс `uri` , показан в файле выводимых.

```xml
<ResourceMapSubtree name="Fare"><NamedResource name="Well" uri="ms-resource://<GUID>/Resources/Fare/Well">...
```

## <a name="refer-to-a-string-resource-identifier-from-your-app-package-manifest"></a>Ссылка на идентификатор строкового ресурса из манифеста пакета приложения
1. Откройте исходный файл манифест пакета приложения (файл `Package.appxmanifest`), в котором по умолчанию отображаемое имя вашего приложения выражено в виде строкового литерала.

   ![Добавление ресурса (английский язык)](images/display-name-before.png)

2. Чтобы создать локализованную версию этой строки, откройте `Resources.resw` и добавьте новый строковый ресурс с именем AppDisplayName и значением Adventure Works Cycles.

3. Замените строковый литерал отображаемого имени ссылкой на идентификатор строкового ресурса, который вы только что создали (AppDisplayName). Для этого необходимо использовать схему `ms-resource` URI (универсальный код ресурса).

   ![Добавление ресурса (английский язык)](images/display-name-after.png)

4. Повторите эту процедуру для каждой строки в манифесте, которую требуется локализовать. Например, короткое название вашего приложения (которое может отображаться на плитке приложения на начальном экране). Список всех элементов в манифесте пакета приложения, которые можно локализовать, см. в разделе [Локализуемые элементы манифеста](/uwp/schemas/appxpackage/uapmanifestschema/localizable-manifest-items-win10?branch=live).

## <a name="localize-the-string-resources"></a>Локализация строковых ресурсов
1. Создайте копию файла ресурсов (.resw) для другого языка.
    1. В разделе Strings создайте новую вложенную папку и назовите ее de-DE для немецкого языка (Германия).
   <br>**Примечание**. В имени папки можно использовать любой [языковой тег BCP-47](http://go.microsoft.com/fwlink/p/?linkid=227302). Дополнительные сведения о языковых квалификаторах и список распространенных языковых тегов приводятся в разделе [Адаптация ресурсов с учетом языка, масштаба и других квалификаторов](tailor-resources-lang-scale-contrast.md).
   2. Создайте копию `Strings/en-US/Resources.resw` в папке `Strings/de-DE`.
2. Переведите строки.
    1. Откройте `Strings/de-DE/Resources.resw` и переведите значения в столбце Value. Комментарии переводить не нужно.

    `Strings/de-DE/Resources.resw`

    ![Добавление ресурса (немецкий язык)](images/addresource-de-de.png)

При желании можно повторить шаги 1 и 2 для еще одного языка.

`Strings/fr-FR/Resources.resw`

![Добавление ресурса (французский язык)](images/addresource-fr-fr.png)

## <a name="test-your-app"></a>Тестирование приложения
Протестируйте приложение для языка интерфейса по умолчанию. Можно изменить язык отображения в разделе **Параметры** > **Время и язык** > **Регион и язык** > **Язык** и повторно протестировать приложение. Рассмотрим строки в пользовательском интерфейсе и в оболочке (например, заголовок окна&mdash;это отображаемое имя&mdash;и сокращенное имя на плитках).

**Примечание**. Если можно найти папку с именем, соответствующим заданному языку отображения, загружается файл ресурсов из этой папки. В противном случае происходит возврат к исходным значениям, и используются ресурсы для языка по умолчанию вашего приложения.

## <a name="factoring-strings-into-multiple-resources-files"></a>Распределение строк по нескольким файлам ресурсов
Все строки можно хранить в одном файле ресурсов (resw) либо распределить их по нескольким файлам ресурсов. Например, вы решите хранить сообщения об ошибках в одном файле ресурсов, строки из манифеста пакета приложения — в другом, а строки пользовательского интерфейса — в третьем. Вот как будет выглядеть структура папок в таком случае.

![Добавление ресурса (английский язык)](images/manifest-resources.png)

Чтобы ссылка на идентификатор строкового ресурса указывала на определенный файл, просто добавьте `/<resources-file-name>/` перед идентификатором. В примере разметки ниже предполагается, что `ErrorMessages.resw` содержит ресурс с именем PasswordTooWeak.Text, значение которого описывает ошибку.

```xaml
<TextBlock x:Uid="/ErrorMessages/PasswordTooWeak"/>
```

Необходимо лишь добавить `/<resources-file-name>/` перед идентификатором строкового ресурса для файлов ресурсов, *отличных от* `Resources.resw`. Это вызвано тем, что Resources.resw — имя файла по умолчанию, и оно используется, если имя файла не указано (как в предыдущих примерах в этом разделе).

В коде ниже предполагается, что `ErrorMessages.resw` содержит ресурс с именем MismatchedPasswords, значение которого описывает ошибку.

> [!NOTE]
> Если вы вызываете любой метод **GetForCurrentView**, который *может* выполняться в фоновом или рабочем потоке, защитите этот вызов тестом `if (Windows.UI.Core.CoreWindow.GetForCurrentThread() != null)`. Вызов **GetForCurrentView** из фонового или рабочего потока приводит к исключению "Не удается создать *&lt;typename&gt; для потоков без CoreWindow*".

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView("ErrorMessages");
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("MismatchedPasswords");
```

```cppwinrt
auto resourceLoader{ Windows::ApplicationModel::Resources::ResourceLoader::GetForCurrentView(L"ErrorMessages") };
myXAMLTextBlockElement().Text(resourceLoader.GetString(L"MismatchedPasswords"));
```

```cpp
auto resourceLoader = Windows::ApplicationModel::Resources::ResourceLoader::GetForCurrentView("ErrorMessages");
this->myXAMLTextBlockElement->Text = resourceLoader->GetString("MismatchedPasswords");
```

Если вы решите переместить ресурс AppDisplayName из `Resources.resw` в `ManifestResources.resw`, то в манифесте пакета приложения необходимо изменить `ms-resource:AppDisplayName` на `ms-resource:/ManifestResources/AppDisplayName`.

Если Сегментированные имя файла ресурсов (он содержит «.» символов), оставьте точки в имени при создании ссылки. **Не** замените точек знаков косой черты («/»), так же, как имя ресурса.

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView("Err.Msgs");
```

В случае сомнения [MakePri.exe](makepri-exe-command-options.md) можно использовать для записи файла PRI вашего приложения. Каждый ресурс `uri` , показан в файле выводимых.

```xml
<ResourceMapSubtree name="Err.Msgs"><NamedResource name="MismatchedPasswords" uri="ms-resource://<GUID>/Err.Msgs/MismatchedPasswords">...
```

## <a name="load-a-string-for-a-specific-language-or-other-context"></a>Загрузка строки для конкретного языка или другого контекста
По умолчанию [**ResourceContext**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext?branch=live) (полученный из [**ResourceContext.GetForCurrentView**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.GetForCurrentView)) содержит значение квалификатора для каждого имени квалификатора, представляя контекст среды выполнения по умолчанию (другими словами, параметры для текущего пользователя и компьютера). Файлы ресурсов (.resw) сопоставляются&mdash;на основании квалификаторов в именах&mdash;со значениями квалификаторов в этом контексте среды выполнения.

Но в некоторых ситуациях вашему приложению требуется переопределить параметры системы и явно задать использование языка, масштаба или другого значения квалификатора при поиске подходящего файла ресурсов для загрузки. Например, вы решите предоставить пользователю возможность выбрать другой язык для подсказок или сообщений об ошибках.

Это можно сделать, создав новый **ResourceContext** (вместо используемого по умолчанию) и переопределив его значения, а затем использовать этот объект контекста при поиске строки.

```csharp
var resourceContext = new Windows.ApplicationModel.Resources.Core.ResourceContext(); // not using ResourceContext.GetForCurrentView
resourceContext.QualifierValues["Language"] = "de-DE";
var resourceMap = Windows.ApplicationModel.Resources.Core.ResourceManager.Current.MainResourceMap.GetSubtree("Resources");
this.myXAMLTextBlockElement.Text = resourceMap.GetValue("Farewell", resourceContext).ValueAsString;
```

Использование **QualifierValues**, как в примере кода выше, работает для любого квалификатора. В случае с языком можно также применить следующий метод.

```csharp
resourceContext.Languages = new string[] { "de-DE" };
```

Для обеспечения того же эффекта на глобальном уровне *можно* переопределить значения квалификатора в **ResourceContext** по умолчанию. Но вместо этого рекомендуется вызвать [**ResourceContext.SetGlobalQualifierValue**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.setglobalqualifiervalue?branch=live#Windows_ApplicationModel_Resources_Core_ResourceContext_SetGlobalQualifierValue_System_String_System_String_Windows_ApplicationModel_Resources_Core_ResourceQualifierPersistence_). Следует задать значения один раз с помощью вызова **SetGlobalQualifierValue** и затем эти значения всегда используются в **ResourceContext** по умолчанию для поиска.

```csharp
Windows.ApplicationModel.Resources.Core.ResourceContext.SetGlobalQualifierValue("Language", "de-DE");
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView();
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("Farewell");
```

У некоторых квалификаторов есть системный поставщик данных. Поэтому вместо вызова **SetGlobalQualifierValue** можно настроить поставщик через его собственный API. Например, в этом коде показано, как задать [**PrimaryLanguageOverride**](/uwp/api/Windows.Globalization.ApplicationLanguages.PrimaryLanguageOverride).

```csharp
Windows.Globalization.ApplicationLanguages.PrimaryLanguageOverride = "de-DE";
```

## <a name="updating-strings-in-response-to-qualifier-value-change-events"></a>Обновление строк в качестве реакции на события изменения значений квалификаторов
Запущенное приложение может реагировать на изменения в параметрах системы, которые влияют на значения квалификаторов в **ResourceContext** по умолчанию. Любой из этих параметров системы вызывает событие [**MapChanged**](/uwp/api/windows.foundation.collections.iobservablemap-2.mapchanged?branch=live) на [**ResourceContext.QualifierValues**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.QualifierValues).

В ответ на это событие можно снова загрузить строки из **ResourceContext** по умолчанию.

```csharp
public MainPage()
{
    this.InitializeComponent();

    ...

    // Subscribe to the event that's raised when a qualifier value changes.
    var qualifierValues = Windows.ApplicationModel.Resources.Core.ResourceContext.GetForCurrentView().QualifierValues;
    qualifierValues.MapChanged += new Windows.Foundation.Collections.MapChangedEventHandler<string, string>(QualifierValues_MapChanged);
}

private async void QualifierValues_MapChanged(IObservableMap<string, string> sender, IMapChangedEventArgs<string> @event)
{
    var dispatcher = this.myXAMLTextBlockElement.Dispatcher;
    if (dispatcher.HasThreadAccess)
    {
        this.RefreshUIText();
    }
    else
    {
        await dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => this.RefreshUIText());
    }
}

private void RefreshUIText()
{
    var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView();
    this.myXAMLTextBlockElement.Text = resourceLoader.GetString("Farewell");
}
```

## <a name="loading-strings-from-a-class-library-or-a-windows-runtime-library"></a>Загрузка строк из библиотеки классов или библиотеки среды выполнения Windows
Строковые ресурсы в указываемой ссылкой библиотеке классов (универсальная платформа Windows) или [библиотеке среды выполнения Windows (универсальная платформа Windows)](../winrt-components/index.md) обычно добавляются в подпапку пакета, в который они включаются во время сборки. Идентификатор ресурса такой строки обычно принимает форму *LibraryName/ResourcesFileName/ResourceIdentifier*.

Библиотека может получить ResourceLoader для собственных ресурсов. Например, в код ниже показано, как библиотека или приложение, которое ссылается на нее, может получить ResourceLoader для строковых ресурсов библиотеки.

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView("ContosoControl/Resources");
this.myXAMLTextBlockElement.Text = resourceLoader.GetString("exampleResourceName");
```

Для библиотеки среды выполнения Windows (универсальные Windows), если Сегментированные пространство имен по умолчанию (он содержит «.» символов), затем использовать точки в имени ресурса карты.

```csharp
var resourceLoader = Windows.ApplicationModel.Resources.ResourceLoader.GetForCurrentView("Contoso.Control/Resources");
```

Не нужно сделать это для библиотеки классов (универсальные Windows). В случае сомнения [MakePri.exe](makepri-exe-command-options.md) можно использовать для вывода компонента или файл библиотеки PRI. Каждый ресурс `uri` , показан в файле выводимых.

```xml
<NamedResource name="exampleResourceName" uri="ms-resource://Contoso.Control/Contoso.Control/ReswFileName/exampleResourceName">...
```

## <a name="loading-strings-from-other-packages"></a>Загрузка строк из других пакетов
Управляемые и получить доступ из пакета ресурсов для пакета приложения владельцем верхнего уровня [**ResourceMap**](/uwp/api/windows.applicationmodel.resources.core.resourcemap?branch=live) , доступный с текущей [**Диспетчер ресурсов**](/uwp/api/windows.applicationmodel.resources.core.resourcemanager?branch=live). В каждом пакете различные компоненты могут иметь собственные поддеревья ResourceMap, доступ к которым можно получить через [**ResourceMap.GetSubtree**](/uwp/api/windows.applicationmodel.resources.core.resourcemap.getsubtree?branch=live).

Платформенный пакет может обращаться к собственным ресурсам с использованием абсолютного идентификатора ресурса (URI). См. также раздел [Схемы URI](uri-schemes.md).

## <a name="important-apis"></a>Важные API
* [ApplicationModel.Resources.ResourceLoader](https://msdn.microsoft.com/library/windows/apps/br206014)
* [ResourceContext.SetGlobalQualifierValue](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.setglobalqualifiervalue?branch=live#Windows_ApplicationModel_Resources_Core_ResourceContext_SetGlobalQualifierValue_System_String_System_String_Windows_ApplicationModel_Resources_Core_ResourceQualifierPersistence_)
* [MapChanged](/uwp/api/windows.foundation.collections.iobservablemap-2.mapchanged?branch=live)

## <a name="related-topics"></a>Статьи по теме
* [Перенос XAML и пользовательского интерфейса](../porting/wpsl-to-uwp-porting-xaml-and-ui.md#localization-and-globalization)
* [Директива x:Uid](../xaml-platform/x-uid-directive.md)
* [вложенные свойства](../xaml-platform/attached-properties-overview.md)
* [Локализуемые элементы манифеста](/uwp/schemas/appxpackage/uapmanifestschema/localizable-manifest-items-win10?branch=live)
* [Тег языка BCP-47](http://go.microsoft.com/fwlink/p/?linkid=227302)
* [Адаптация ресурсов с учетом языка, масштаба и других квалификаторов](tailor-resources-lang-scale-contrast.md)
* [Загрузка строковых ресурсов](https://msdn.microsoft.com/library/windows/apps/xaml/hh965323)