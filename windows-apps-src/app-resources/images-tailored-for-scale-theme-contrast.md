---
author: stevewhims
Description: Your app can load image resource files containing images tailored for display scale factor, theme, high contrast, and other runtime contexts.
title: Загрузка изображений и ресурсов, адаптированных по масштабированию, теме, высокой контрастности и другим аспектам
template: detail.hbs
ms.author: stwhi
ms.date: 10/10/2017
ms.topic: article
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: 4db96cea273348b4e1bc7059446f7528ba30a645
ms.sourcegitcommit: 6cc275f2151f78db40c11ace381ee2d35f0155f9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5557908"
---
# <a name="load-images-and-assets-tailored-for-scale-theme-high-contrast-and-others"></a>Загрузка изображений и ресурсов, адаптированных по масштабированию, теме, высокой контрастности и другим аспектам
Приложение может загружать файлы ресурсов изображений (или другие файлы ресурсов), адаптированные по контексту [коэффициента масштабирования дисплея](../design/layout/screen-sizes-and-breakpoints-for-responsive-design.md), темы, высокой контрастности и другим аспектам. На эти изображения можно ссылаться из императивного кода или разметки XAML, например в виде свойства **Source** объекта **Image**. Они также могут появляться в исходном файле манифеста пакета приложения (файле `Package.appxmanifest`)— например, в качестве значения для App Icon на вкладке Visual Assets в конструкторе манифеста Visual Studio— или на плитке и всплывающих уведомлениях. Используя квалификаторы в именах файлов изображений при необходимости динамической их загрузке с помощью [**ResourceContext**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext?branch=live), можно обеспечить загрузку наиболее подходящего файла изображения, который наилучшим образом соответствует параметрам среды выполнения пользователя для масштабирования дисплея, темы, высокой контрастности, языку и другим аспектам.

Ресурс изображения содержится в файле ресурсов изображений. Также можно представить себе изображение в качестве ресурса, а содержащий его файл — в качестве файла ресурса; и эти файлы ресурсов находятся в папке \Assets вашего проекта. Общие сведения об использовании квалификаторов в именах файлов ресурсов изображений приводятся в разделе [Адаптация ресурсов для языка, масштаба и других квалификаторов](tailor-resources-lang-scale-contrast.md).

Вот некоторые распространенные квалификаторы изображений: [scale](tailor-resources-lang-scale-contrast.md#scale), [theme](tailor-resources-lang-scale-contrast.md#theme), [contrast](tailor-resources-lang-scale-contrast.md#contrast) и [targetsize](tailor-resources-lang-scale-contrast.md#targetsize).

## <a name="qualify-an-image-resource-for-scale-theme-and-contrast"></a>Квалификация ресурса изображения по масштабу, теме и контрастности
По умолчанию для квалификатора `scale` используется значение `scale-100`. Таким образом, эти два варианта эквивалентны (они предоставляют изображение в масштабе 100, то есть с коэффициентом масштабирования 1).

```
\Assets\Images\logo.png
\Assets\Images\logo.scale-100.png
```

Можно использовать квалификаторы в именах папок вместо имен файлов. Это более подходящий метод, если у вас есть несколько файлов ресурсов для каждого квалификатора. Для наглядности ниже приведены еще два варианта, эквивалентные приведенным выше.

```
\Assets\Images\logo.png
\Assets\Images\scale-100\logo.png
```

Далее приведен пример того, как можно предоставлять варианты ресурсов изображений— с именем `/Assets/Images/logo.png`— для различных параметров масштабирования дисплея, темы и высокой контрастности. В этом примере используется именование папки.

```
\Assets\Images\contrast-standard\theme-dark
    \scale-100\logo.png
    \scale-200\logo.png
\Assets\Images\contrast-standard\theme-light
    \scale-100\logo.png
    \scale-200\logo.png
\Assets\Images\contrast-high
    \scale-100\logo.png
    \scale-200\logo.png
```

## <a name="reference-an-image-or-other-asset-from-xaml-markup-and-code"></a>Ссылки на изображения и другие ресурсы из кода и разметки XAML
Именем— или идентификатором— ресурса изображения является его путь и имя файла без каких-либо квалификаторов. Если вы назовете папки или файлы как в любом из примеров в предыдущем разделе, у вас будет один ресурс изображений, а его имя (как абсолютный путь) будет `/Assets/Images/logo.png`. Вот как использовать это имя в разметке XAML.

```xaml
<Image x:Name="myXAMLImageElement" Source="ms-appx:///Assets/Images/logo.png"/>
```

Обратите внимание, что вы используете схему URI `ms-appx`, так как ссылаетесь на файл, который поступает из пакета приложения. См. раздел [Схемы URI](uri-schemes.md). А вот как добавить ссылку на тот же ресурс изображения в императивный код.

```csharp
this.myXAMLImageElement.Source = new BitmapImage(new Uri("ms-appx:///Assets/Images/logo.png"));
```

Можно использовать `ms-appx` для загрузки любого произвольного файла из пакета приложения.

```csharp
var uri = new System.Uri("ms-appx:///Assets/anyAsset.ext");
var storagefile = await Windows.Storage.StorageFile.GetFileFromApplicationUriAsync(uri);
```

Схема `ms-appx-web` обращается к тем же файлам, что и `ms-appx`, но в разделе web.

```xaml
<WebView x:Name="myXAMLWebViewElement" Source="ms-appx-web:///Pages/default.html"/>
```

```csharp
this.myXAMLWebViewElement.Source = new Uri("ms-appx-web:///Pages/default.html");
```

Для любых сценариев, показанных в этих примерах, используйте перегрузку [конструктора Uri](https://docs.microsoft.com/en-us/dotnet/api/system.uri.-ctor?view=netcore-2.0#System_Uri__ctor_System_String_), которая выводит [UriKind](https://docs.microsoft.com/en-us/dotnet/api/system.urikind). Укажите допустимый абсолютный URI, включая схему и полномочия, или просто оставьте для полномочий значение по умолчанию для пакета приложения, как показано в примере выше.

Обратите внимание, как в кодах URI в этом примере за схемой (`ms-appx` или `ms-appx-web`) следует `://`, а далее указан абсолютный путь. В абсолютном пути начальный символ `/` обеспечивает его интерпретацию из корневого каталога пакета.

**Примечание**. Схемы URI `ms-resource` (для [строковых ресурсов](localize-strings-ui-manifest.md)) и `ms-appx(-web)` (для изображений и других ресурсов) производят автоматическое сопоставление квалификаторов для обнаружения наиболее подходящего в текущем контексте ресурса. Схема URI `ms-appdata` (которая используется для загрузки данных приложения) не выполняет никакое автоматическое сопоставление, но вы можете реагировать на содержимое [ResourceContext.QualifierValues](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.QualifierValues) и явно загружать соответствующие ресурсы из данных приложения, используя полное имя физического файла в URI. Подробнее о данных приложения: [Хранение и извлечение параметров и прочих данных приложения](../design/app-settings/store-and-retrieve-app-data.md). Веб-схемы URI (например, `http`, `https` и `ftp`) также не выполняют автоматическое сопоставление. Сведения о том, что делать в этом случае, см. в разделе [Размещение и загрузка изображений в облаке](../design/shell/tiles-and-notifications/tile-toast-language-scale-contrast.md#hosting-and-loading-images-in-the-cloud).

Абсолютные пути удобны, если файлы изображений остаются там, где они находятся в структуре проекта. Если вы хотите иметь возможность перемещать файл изображения, но следите за тем, чтобы он оставался в том же расположении относительно ссылающегося на него файла разметки XAML, то вместо абсолютного пути вам может потребоваться использовать путь, который задается относительно файла, содержащего разметку. Если применяется такой способ, использовать схему URI не требуется. В этом случае вы по-прежнему сможете использовать преимущества автоматического сопоставления квалификаторов, но только потому, что используете относительный путь в разметке XAML.

```xaml
<Image Source="Assets/Images/logo.png"/>
```

См. также [Поддержка плитки и всплывающих уведомлений для языка, масштаба и высокой контрастности ](../design/shell/tiles-and-notifications/tile-toast-language-scale-contrast.md).

## <a name="qualify-an-image-resource-for-targetsize"></a>Квалификация ресурса изображения по targetsize
Можно использовать квалификаторы `scale` и `targetsize` для различных вариантов одного ресурса изображения, но нельзя использовать оба этих квалификатора для одного варианта ресурса. Также необходимо задать хотя бы один вариант без квалификатора `TargetSize`. Этот вариант должен либо определять значение для `scale`, либо разрешать использовать по умолчанию `scale-100`. Таким образом, эти два варианта ресурса `/Assets/Square44x44Logo.png` являются допустимыми.

```
\Assets\Square44x44Logo.scale-200.png
\Assets\Square44x44Logo.targetsize-24.png
```

И эти два варианта допустимы. 

```
\Assets\Square44x44Logo.png // defaults to scale-100
\Assets\Square44x44Logo.targetsize-24.png
```

Но этот вариант недопустим.

```
\Assets\Square44x44Logo.scale-200_targetsize-24.png
```

## <a name="refer-to-an-image-file-from-your-app-package-manifest"></a>Ссылка на файл изображения из манифеста пакета приложения
Если вы назовете папки или файлы как в любом из двух допустимых примеров в предыдущем разделе, у вас будет один ресурс изображения со значком приложения, а его имя (как относительный путь) будет `Assets\Square44x44Logo.png`. В манифесте пакета приложения просто приведите ссылку на ресурс по имени. Нет необходимости использовать какую-либо схему URI.

![Добавление ресурса (английский язык)](images/app-icon.png)

Это все, что необходимо сделать, и операционная система будет выполнять автоматическое сопоставление квалификаторов для поиска ресурса, который больше всего подходит текущему контексту. Список всех элементов в манифесте пакета приложения, которые можно локализовать или иначе квалифицировать таким образом, см. в разделе [Локализуемые элементы манифеста](/uwp/schemas/appxpackage/uapmanifestschema/localizable-manifest-items-win10?branch=live).

## <a name="qualify-an-image-resource-for-layoutdirection"></a>Квалификация ресурса изображения по layoutdirection
См. раздел [Зеркальное отображение изображений](../design/globalizing/adjust-layout-and-fonts--and-support-rtl.md#mirroring-images).

## <a name="load-an-image-for-a-specific-language-or-other-context"></a>Загрузка изображения для конкретного языка или другого контекста
Дополнительные сведения о преимуществах локализации приложений см. в разделе [Глобализация и локализация](../design/globalizing/globalizing-portal.md).

По умолчанию [**ResourceContext**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext?branch=live) (полученный из [**ResourceContext.GetForCurrentView**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.GetForCurrentView)) содержит значение квалификатора для каждого имени квалификатора, представляя контекст среды выполнения по умолчанию (другими словами, параметры для текущего пользователя и компьютера). Файлы изображений сопоставляются— на основании квалификаторов в именах— со значениями квалификаторов в этом контексте среды выполнения.

Но в некоторых ситуациях вашему приложению требуется переопределить параметры системы и явно задать использование языка, масштаба или другого значения квалификатора при поиске подходящего изображения для загрузки. Например, может потребоваться точное управление загрузкой определенных высококонтрастных изображений.

Это можно сделать, создав новый **ResourceContext** (вместо используемого по умолчанию) и переопределив его значения, а затем использовать этот объект контекста при поиске изображения.

```csharp
var resourceContext = new Windows.ApplicationModel.Resources.Core.ResourceContext(); // not using ResourceContext.GetForCurrentView 
resourceContext.QualifierValues["Contrast"] = "high";
var namedResource = Windows.ApplicationModel.Resources.Core.ResourceManager.Current.MainResourceMap[@"Files/Assets/Logo.png"];
var resourceCandidate = namedResource.Resolve(resourceContext);
var imageFileStream = resourceCandidate.GetValueAsStreamAsync().GetResults();
var bitmapImage = new Windows.UI.Xaml.Media.Imaging.BitmapImage();
bitmapImage.SetSourceAsync(imageFileStream);
this.myXAMLImageElement.Source = bitmapImage;
```

Для обеспечения того же эффекта на глобальном уровне *можно* переопределить значения квалификатора в **ResourceContext** по умолчанию. Но вместо этого рекомендуется вызвать [**ResourceContext.SetGlobalQualifierValue**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.setglobalqualifiervalue?branch=live#Windows_ApplicationModel_Resources_Core_ResourceContext_SetGlobalQualifierValue_System_String_System_String_Windows_ApplicationModel_Resources_Core_ResourceQualifierPersistence_). Следует задать значения один раз с помощью вызова **SetGlobalQualifierValue** и затем эти значения всегда используются в **ResourceContext** по умолчанию для поиска. По умолчанию класс [**ResourceManager**](/uwp/api/windows.applicationmodel.resources.core.resourcemanager?branch=live) использует **ResourceContext** по умолчанию.

```csharp
Windows.ApplicationModel.Resources.Core.ResourceContext.SetGlobalQualifierValue("Contrast", "high");
var namedResource = Windows.ApplicationModel.Resources.Core.ResourceManager.Current.MainResourceMap[@"Files/Assets/Logo.png"];
this.myXAMLImageElement.Source = new Windows.UI.Xaml.Media.Imaging.BitmapImage(namedResource.Uri);
```

## <a name="updating-images-in-response-to-qualifier-value-change-events"></a>Обновление изображений в качестве реакции на события изменения значений квалификаторов
Запущенное приложение может реагировать на изменения в параметрах системы, которые влияют на значения квалификаторов в контексте ресурсов по умолчанию. Любой из этих параметров системы вызывает событие [**MapChanged**](/uwp/api/windows.foundation.collections.iobservablemap-2.mapchanged?branch=live) на [**ResourceContext.QualifierValues**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.QualifierValues).

В ответ на это событие можно снова загрузить изображения с помощью **ResourceContext** по умолчанию, который [**ResourceManager**](/uwp/api/windows.applicationmodel.resources.core.resourcemanager?branch=live) использует по умолчанию.

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
    var dispatcher = this.myImageXAMLElement.Dispatcher;
    if (dispatcher.HasThreadAccess)
    {
        this.RefreshUIImages();
    }
    else
    {
        await dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () => this.RefreshUIImages());
    }
}

private void RefreshUIImages()
{
    var namedResource = Windows.ApplicationModel.Resources.Core.ResourceManager.Current.MainResourceMap[@"Files/Assets/Logo.png"];
    this.myImageXAMLElement.Source = new Windows.UI.Xaml.Media.Imaging.BitmapImage(namedResource.Uri);
}
```

## <a name="important-apis"></a>Важные API
* [ResourceContext](/uwp/api/windows.applicationmodel.resources.core.resourcecontext?branch=live)
* [ResourceContext.SetGlobalQualifierValue](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.setglobalqualifiervalue?branch=live#Windows_ApplicationModel_Resources_Core_ResourceContext_SetGlobalQualifierValue_System_String_System_String_Windows_ApplicationModel_Resources_Core_ResourceQualifierPersistence_)
* [MapChanged](/uwp/api/windows.foundation.collections.iobservablemap-2.mapchanged?branch=live)

## <a name="related-topics"></a>Статьи по теме
* [Адаптация ресурсов с учетом языка, масштаба и других квалификаторов](tailor-resources-lang-scale-contrast.md)
* [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](localize-strings-ui-manifest.md)
* [Хранение и извлечение параметров и прочих данных приложения](../design/app-settings/store-and-retrieve-app-data.md)
* [Поддержка плитки и всплывающих уведомлений для языка, масштаба и высокой контрастности](tile-toast-language-scale-contrast.md)
* [Локализуемые элементы манифеста](/uwp/schemas/appxpackage/uapmanifestschema/localizable-manifest-items-win10?branch=live)
* [Зеркальное отображение изображений](../design/globalizing/adjust-layout-and-fonts--and-support-rtl.md#mirroring-images)
* [Глобализация и локализация](../design/globalizing/globalizing-portal.md)
