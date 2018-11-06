---
author: stevewhims
Description: This topic explains the general concept of qualifiers, how to use them, and the purpose of each of the qualifier names.
title: Адаптация ресурсов с учетом языка, масштаба, высокой контрастности и других квалификаторов
template: detail.hbs
ms.author: stwhi
ms.date: 10/10/2017
ms.topic: article
keywords: Windows 10, uwp, ресурс, изображение, средство, MRT, квалификатор
ms.localizationpriority: medium
ms.openlocfilehash: 018740b9ceaa10425ec71f6a2775d547b7c30e82
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6048483"
---
# <a name="tailor-your-resources-for-language-scale-high-contrast-and-other-qualifiers"></a>Адаптация ресурсов с учетом языка, масштаба, высокой контрастности и других квалификаторов

В этом разделе описывается общая концепция квалификаторов ресурсов, особенности их использования и назначение каждого из имен квалификаторов. Справочную таблицу всех возможных значений квалификаторов см. в разделе [**ResourceContext.QualifierValues**](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.QualifierValues).

Ваше приложение может загружать ресурсы, адаптированные под контексты среды выполнения, такие как язык интерфейса, высокая контрастность, [коэффициент масштабирования монитора](../design/layout/screen-sizes-and-breakpoints-for-responsive-design.md#effective-pixels-and-scale-factor) и многие другие. Для этого необходимо назвать папки или файлы ресурсов согласно именам и значениям квалификаторов, соответствующим этим контекстам. Например, ваше приложение может загружать другой набор ресурсов изображений в режиме высокой контрастности.

Дополнительные сведения о преимуществах локализации приложений см. в разделе [Глобализация и локализация](../design/globalizing/globalizing-portal.md).

## <a name="qualifier-name-qualifier-value-and-qualifier"></a>Имя квалификатора, значение квалификатора и квалификатор

Имя квалификатора— это ключ, сопоставленный с набором значений квалификатора. Ниже представлены имя и значения квалификатора для контрастности.

| Контекст | Имя квалификатора | Значения квалификатора |
| :--------------- | :--------------- | :--------------- |
| Параметры высокой контрастности | контрастность | стандартная, высокая, черная, белая |

Квалификатор образуется путем объединения имени и значения квалификатора. `<qualifier name>-<qualifier value>` является форматом квалификатора. `contrast-standard` является примером квалификатора.

Таким образом, для высокой контрастности используется набор квалификаторов `contrast-standard`, `contrast-high`, `contrast-black` и `contrast-white`. В именах и значениях квалификаторов регистр не учитывается. Например, `contrast-standard` и `Contrast-Standard`— это один и тот же квалификатор.

## <a name="use-qualifiers-in-folder-names"></a>Использование квалификаторов в именах папок

Ниже приведен пример использования квалификаторов в именах папок, содержащих файлы ресурсов. Используйте квалификаторы в именах папок при наличии нескольких файлов ресурсов на квалификатор. В этом случае квалификатор устанавливается один раз на уровне папки и применяется ко всему ее содержимому.

```console
\Assets\Images\contrast-standard\<logo.png, and other image files>
\Assets\Images\contrast-high\<logo.png, and other image files>
\Assets\Images\contrast-black\<logo.png, and other image files>
\Assets\Images\contrast-white\<logo.png, and other image files>
```

Если назвать папки, как в примере выше, приложение использует параметр высокой контрастности для загрузки файлов ресурсов из папки, имя которой содержит соответствующий квалификатор. Таким образом, если параметру задано значение "Черная тема высокой контрастности", загружаются файлы ресурсов в папке `\Assets\Images\contrast-black`. Если параметру задано значение "Нет" (то есть режим высокой контрастности на компьютере не включен), загружаются файлы в папке `\Assets\Images\contrast-standard`.

## <a name="use-qualifiers-in-file-names"></a>Использование квалификаторов в именах файлов

Вместо создания и именования папок можно использовать квалификатор в именах самих файлов ресурсов. Это оптимальный вариант при наличии только одного файла ресурса на квалификатор. Вот пример.

```console
\Assets\Images\logo.contrast-standard.png
\Assets\Images\logo.contrast-high.png
\Assets\Images\logo.contrast-black.png
\Assets\Images\logo.contrast-white.png
```

Загружается файл, имя которого содержит наиболее соответствующий параметру квалификатор. Эта логика сопоставления работает одинаково как для имен файлов, так и для имен папок.

## <a name="reference-a-string-or-image-resource-by-name"></a>Ссылка на ресурс строки или изображения по имени

См. разделы [Ссылка на идентификатор строкового ресурса из разметки XAML](localize-strings-ui-manifest.md#refer-to-a-string-resource-identifier-from-xaml-markup), [Ссылка на идентификатор строкового ресурса из кода](localize-strings-ui-manifest.md#refer-to-a-string-resource-identifier-from-code) и [Ссылки на изображения и другие ресурсы из кода и разметки XAML](images-tailored-for-scale-theme-contrast.md#reference-an-image-or-other-asset-from-xaml-markup-and-code).

## <a name="actual-and-neutral-qualifier-matches"></a>Фактические и нейтральные соответствия квалификаторов
Нет необходимости предоставлять файл ресурса для *каждого* значения квалификатора. Например, если вам требуется только один визуальный ресурс для высокой контрастности и один— для стандартной, назвать эти ресурсы можно следующим образом.

```console
\Assets\Images\logo.contrast-high.png
\Assets\Images\logo.png
```

Имя первого файла содержит квалификатор `contrast-high`. Этот квалификатор является *фактическим* соответствием для любого параметра высокой контрастности, если режим высокой контрастности *включен*. Иными словами, это близкое соответствие, поэтому ему отдается предпочтение. *Фактическое* совпадение возможно, только если квалификатор содержит *фактическое* значение, как в этом случае. В данном случае `high` — *фактическое* значение для `contrast`.

Файл с именем `logo.png` не содержит квалификатора контрастности. Отсутствие квалификатора является *нейтральным* значением. Если не удается найти предпочтительное значение, нейтральное значение используется в качестве резервного соответствия. В этом примере, если режим высокой контрастности *отключен*, фактического соответствия нет. Наиболее близкое соответствие, которое удается найти,— *нейтральное*, поэтому загружается ресурс `logo.png`.

Если изменить имя файла `logo.png` на `logo.contrast-standard.png`, оно будет содержать фактическое значение квалификатора. При выключенном режиме высокой контрастности файл ресурса `logo.contrast-standard.png` был бы фактическим соответствием, и именно он и был бы загружен. Таким образом, были бы загружены одни и те же файлы при одинаковых условиях, но из-за разных соответствий.

Если вам нужен только один набор ресурсов для высокой контрастности и один— для стандартной контрастности, можно использовать имена папок вместо имен файлов. В этом случае полный пропуск имени папки обеспечивает нейтральное соответствие.

```console
\Assets\Images\contrast-high\<logo.png, and other images to load when high contrast theme is not None>
\Assets\Images\<logo.png, and other images to load when high contrast theme is None>
```

Дополнительные сведения о принципах соответствий квалификаторов см. в разделе [Система управления ресурсами](resource-management-system.md).

## <a name="multiple-qualifiers"></a>Несколько квалификаторов

Можно сочетать квалификаторы в именах папок и файлов. Например, вы можете сделать так, чтобы приложение загружало ресурсы изображений, когда режим высокой контрастности включен *и* коэффициент масштабирования дисплея равен 400. Этого можно добиться с помощью вложенных папок.

```console
\Assets\Images\contrast-high\scale-400\<logo.png, and other image files>
```

Для загрузки `logo.png` и других файлов параметры должны соответствовать *обоим* квалификаторам.

Кроме того, можно объединить несколько квалификаторов в одном имени папки.

```console
\Assets\Images\contrast-high_scale-400\<logo.png, and other image files>
```

При добавлении нескольких квалификаторов в имя папки они разделяются символом подчеркивания. `<qualifier1>[_<qualifier2>...]` — это формат.

Можно объединить несколько квалификаторов в имени файла в одном формате.

```console
\Assets\Images\logo.contrast-high_scale-400.png
```

В зависимости от средств и рабочего процесса, используемых для создания ресурсов, а также от того, что вам кажется более удобным в чтении и управлении, вы можете выбрать единую стратегию именования для всех квалификаторов или сочетать возможные варианты для разных квалификаторов.

## <a name="alternateform"></a>AlternateForm

С помощью квалификатора `alternateform` можно задать альтернативную форму ресурса для особых целей. Он нужен главным образом японским разработчикам для создания строк фуригана, для которых зарезервировано значение `msft-phonetic` (см. раздел "Включайте поддержку фуриганы для строк на японском языке, которые могут подвергаться сортировке" в статье [Как подготовиться к локализации](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh967762)).

Целевая система или приложение должны предоставить значение, с которым сопоставляются квалификаторы `alternateform`. При указании своих значений квалификатора `alternateform` не добавляйте префикс `msft-`.

## <a name="configuration"></a>Configuration

Маловероятно, что вам потребуется имя квалификатора `configuration`. Его можно использовать для указания ресурсов, применимых только в данной среде времени разработки, например тестовых ресурсов.

Квалификатор `configuration` используется для загрузки ресурса, наиболее соответствующего значению переменной среды `MS_CONFIGURATION_ATTRIBUTE_VALUE`. Таким образом, можно задать для переменной строковое значение, присвоенное релевантным ресурсам, например `designer` или `test`.

## <a name="contrast"></a>Contrast

Квалификатор `contrast` используется для предоставления ресурсов, наиболее соответствующих параметрам высокой контрастности.

## <a name="custom"></a>Custom

Приложение может установить значение для квалификатора `custom`, после чего загружаются ресурсы, наиболее соответствующие этому значению. Например, можно загружать ресурсы с учетом лицензии приложения. При запуске приложение проверяет лицензию и использует ее как значение для квалификатора `custom` путем вызова метода [SetGlobalQualifierValue](/uwp/api/windows.applicationmodel.resources.core.resourcecontext#Windows_ApplicationModel_Resources_Core_ResourceContext_SetGlobalQualifierValue_System_String_System_String_Windows_ApplicationModel_Resources_Core_ResourceQualifierPersistence_), как показано в примере кода.

```csharp
public void SetLicenseLevel(BrandID brand)
{
    if (brand == BrandID.Premium)
    {
        ResourceContext.SetGlobalQualifierValue("Custom", "Premium", ResourceQualifierPersistence.LocalMachine);
    }
    else if (brand == BrandID.Standard)
    {
        ResourceContext.SetGlobalQualifierValue("Custom", " Standard", ResourceQualifierPersistence.LocalMachine);
    }
    else
    {
        ResourceContext.SetGlobalQualifierValue("Custom", "Trial", ResourceQualifierPersistence.LocalMachine);
    }
}
```

Далее в этом сценарии предоставляются имена ресурсов, содержащие квалификаторы `custom-premium`, `custom-standard` и `custom-trial`.

## <a name="devicefamily"></a>DeviceFamily

Маловероятно, что вам потребуется имя квалификатора `devicefamily`. Вы можете (и вам следует) избегать его использования, так как вместо этого можно использовать более удобные и надежные способы. Эти способы описаны в разделах [Определение платформы, на которой работает приложение](../porting/wpsl-to-uwp-input-and-sensors.md#detecting-the-platform-your-app-is-running-on) и [Адаптивный к версии код](https://docs.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

Но в крайнем случае можно использовать квалификаторы devicefamily для именования папок, содержащих ваши представления XAML (представление XAML— это файл XAML, содержащий макет пользовательского интерфейса и элементы управления).

```console
\devicefamily-desktop\<MainPage.xaml, and other markup files to load when running on a desktop computer>
\devicefamily-mobile\<MainPage.xaml, and other markup files to load when running on a phone>
```

Кроме того, можно именовать файлы.

```console
\MainPage.devicefamily-desktop.xaml
\MainPage.devicefamily-mobile.xaml
```

В обоих случаях каждая копия `MainPage.[<qualifier>].xaml` содержит общий файл `MainPage.xaml.cs`, который остается неизменным в проекте с точки зрения имени, расположения и содержимого.

Также можно использовать квалификатор devicefamily для именования файла (`.resw`) или папки ресурсов. Например, если приложение запускается на мобильном семействе устройств, для элемента `<TextBlock x:Uid="DeviceFriendlyName"/>` пользовательского интерфейса будут использоваться ресурсы текста и переднего плана, определенные в содержащемся в нем файле `Resources.devicefamily-mobile.resw`

```xml
<data name="DeviceFriendlyName.Foreground">
    <value>Red</value>
</data>
<data name="DeviceFriendlyName.Text">
    <value>Mobile device</value>
</data>
```

Дополнительные сведения об использовании файла ресурсов см. в разделе [Локализация строк пользовательского интерфейса](localize-strings-ui-manifest.md).

## <a name="dxfeaturelevel"></a>DXFeatureLevel

Маловероятно, что вам потребуется имя квалификатора `dxfeaturelevel`. Этот квалификатор предназначен для использования с игровыми ресурсами Direct3D. Он обеспечивает загрузку низкоуровневых ресурсов в соответствии с распространенной на тот момент конфигурацией оборудования крайне низкого уровня. Но на сегодняшний день такая конфигурация встречается настолько редко, что мы не рекомендуем использовать этот квалификатор.

## <a name="homeregion"></a>HomeRegion

Квалификатор `homeregion` соответствует параметру страны или региона пользователя. Он представляет домашнее расположение пользователя. Его значением может быть любой действительный [тег региона BCP-47](http://go.microsoft.com/fwlink/p/?linkid=227302). то есть любой двухбуквенный код региона **ISO 3166-1 alpha-2** или трехзначный географический код для составных регионов **ISO 3166-1 numeric** (см. [Состав кодов регионов подразделения статистики ООН M49](http://go.microsoft.com/fwlink/p/?linkid=247929)). Коды выбранных экономических и других групп недействительны.

## <a name="language"></a>Language

Квалификатор `language` соответствует параметру языка интерфейса. Его значением может быть любой действительный [тег языка BCP-47](http://go.microsoft.com/fwlink/p/?linkid=227302). Список языков см. в [реестре подтегов языков IANA](http://go.microsoft.com/fwlink/p/?linkid=227303).

Если вы хотите, чтобы ваше приложение поддерживало разные языки интерфейса, а в вашем коде или разметке XAML есть строковые литералы, переместите эти строки из кода или разметки в файл ресурсов (`.resw`). Затем можно создать переведенную копию этого файла ресурсов для каждого языка, поддерживаемого вашим приложением.

Как правило, квалификатор `language` используется для именования папок, содержащих файлы ресурсов (`.resw`).

```console
\Strings\language-en\Resources.resw
\Strings\language-ja\Resources.resw
```

Можно опустить часть `language-` квалификатора `language` (то есть имя квалификатора). Это можно сделать только с этим типом квалификатора и только в имени папки.

```console
\Strings\en\Resources.resw
\Strings\ja\Resources.resw
```

Вместо именования папок можно использовать квалификаторы `language` для именования самих файлов ресурсов.

```console
\Strings\Resources.language-en.resw
\Strings\Resources.language-ja.resw
```

Дополнительные сведения об использовании строковых ресурсов для обеспечения возможности локализации приложения и о добавлении в приложение ссылок на строковые ресурсы см. в разделе [Локализация строк пользовательского интерфейса](localize-strings-ui-manifest.md).

## <a name="layoutdirection"></a>LayoutDirection

Квалификатор `layoutdirection` соответствует направлению макета параметра языка интерфейса. Например, для арабского языка или иврита необходимо отразить изображение слева направо. Панели макета и изображения в пользовательском интерфейсе будут реагировать на направление макета должным образом, если настроить их свойство[FlowDirection](/uwp/api/Windows.UI.Xaml.FrameworkElement.FlowDirection) (см. раздел [Настройка макета и шрифтов, реализация поддержки написания справа налево](../design/globalizing/adjust-layout-and-fonts--and-support-rtl.md)). Однако если простого отражения недостаточно, используется квалификатор `layoutdirection`. Он дает возможность реагировать на определенный порядок чтения и выравнивание текста более общими способами.

## <a name="scale"></a>Масштаб

Windows автоматически выбирает коэффициент масштабирования для каждого дисплея на основе его показателя DPI (точек на дюйм) и расстояния от экрана устройства. См. раздел [Эффективные пиксели и коэффициент масштабирования](../design/layout/screen-sizes-and-breakpoints-for-responsive-design.md#effective-pixels-and-scale-factor). Следует создавать изображения в нескольких рекомендуемых размерах (по крайней мере 100, 200 и 400), чтобы Windows могла либо выбрать идеальный размер, либо использовать ближайший размер с масштабированием. Для того чтобы Windows могла определить, какой физический файлы содержит изображение нужного размера для коэффициента масштабирования дисплея, используйте квалификатор `scale`. Масштаб ресурса сопоставляется со значением [DisplayInformation.ResolutionScale](/uwp/api/windows.graphics.display.displayinformation.ResolutionScale) или со следующим ресурсом с наибольшим масштабом.

Вот пример настройки квалификатора на уровне папки.

```console
\Assets\Images\scale-100\<logo.png, and other image files>
\Assets\Images\scale-200\<logo.png, and other image files>
\Assets\Images\scale-400\<logo.png, and other image files>
```

А в этом примере квалификатор настраивается на уровне файла.

```console
\Assets\Images\logo.scale-100.png
\Assets\Images\logo.scale-200.png
\Assets\Images\logo.scale-400.png
```

Сведения о квалификации ресурса для `scale` и `targetsize` одновременно см. в разделе [Квалификация ресурса изображения по targetsize](images-tailored-for-scale-theme-contrast.md#qualify-an-image-resource-for-targetsize).

## <a name="targetsize"></a>TargetSize

Квалификатор `targetsize` используется в основном для указания [значков сопоставления типов файлов](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh127427) или [значков протоколов](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/bb266530), отображаемых в проводнике. Значение этого квалификатора представляет длину стороны квадратного изображения в физических (необработанных) пикселях. Загружается ресурс, значение которого соответствует параметру "Представление" в проводнике, или следующий ресурс с наибольшим значением при отсутствии точного соответствия.

Можно определить ресурсы, представляющие несколько размеров значения квалификатора `targetsize` для значка приложения (`/Assets/Square44x44Logo.png`), на вкладке "Визуальные ресурсы" конструктора манифеста пакета приложения.

Сведения о квалификации ресурса для `scale` и `targetsize` одновременно см. в разделе [Квалификация ресурса изображения по targetsize](images-tailored-for-scale-theme-contrast.md#qualify-an-image-resource-for-targetsize).

## <a name="theme"></a>Theme

Квалификатор `theme` используется для предоставления ресурсов, наиболее соответствующих установленному по умолчанию параметру режима приложения или переопределению приложения с использованием [Application.RequestedTheme](/uwp/api/windows.ui.xaml.application?branch=master.RequestedTheme).

## <a name="important-apis"></a>Важные API

* [ResourceContext.QualifierValues](/uwp/api/windows.applicationmodel.resources.core.resourcecontext.QualifierValues)
* [SetGlobalQualifierValue](/uwp/api/windows.applicationmodel.resources.core.resourcecontext#Windows_ApplicationModel_Resources_Core_ResourceContext_SetGlobalQualifierValue_System_String_System_String_Windows_ApplicationModel_Resources_Core_ResourceQualifierPersistence_)

## <a name="related-topics"></a>Статьи по теме

* [Эффективные пиксели и коэффициент масштабирования](../design/layout/screen-sizes-and-breakpoints-for-responsive-design.md#effective-pixels-and-scale-factor)
* [Система управления ресурсами](resource-management-system.md)
* [Подготовка к локализации](https://msdn.microsoft.com/en-us/library/windows/apps/xaml/hh967762)
* [Определение платформы, на которой работает приложение](../porting/wpsl-to-uwp-input-and-sensors.md#detecting-the-platform-your-app-is-running-on)
* [Общие сведения о семействах устройств](https://docs.microsoft.com/uwp/extension-sdks/device-families-overview)
* [Локализация строк пользовательского интерфейса](localize-strings-ui-manifest.md)
* [BCP-47](http://go.microsoft.com/fwlink/p/?linkid=227302)
* [Сочетание кодов регионов по версии статистического отдела ООН M49](http://go.microsoft.com/fwlink/p/?linkid=247929)
* [Реестр подтегов языков IANA](http://go.microsoft.com/fwlink/p/?linkid=227303)
* [Настройка макета и шрифтов, реализация поддержки написания справа налево](../design/globalizing/adjust-layout-and-fonts--and-support-rtl.md)
