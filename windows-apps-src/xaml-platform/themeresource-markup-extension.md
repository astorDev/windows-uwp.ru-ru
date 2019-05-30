---
description: Предоставляет значение для любого атрибута XAML, обрабатывая ссылку на ресурс. Дополнительная системная логика возвращает различные ресурсы в зависимости от активной темы.
title: Расширение разметки ThemeResource
ms.assetid: 8A1C79D2-9566-44AA-B8E1-CC7ADAD1BCC5
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4f4ad8c6fe4108546a66a2915ef1c453d812dff5
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371116"
---
# <a name="themeresource-markup-extension"></a>Расширение разметки {ThemeResource}

Предоставляет значение для любого атрибута XAML, обрабатывая ссылку на ресурс. Дополнительная системная логика возвращает различные ресурсы в зависимости от активной темы. Аналогично ресурсу [Расширение разметки {StaticResource}](staticresource-markup-extension.md), ресурсы определены в [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary), а в **ThemeResource** используются ссылки на ключи ресурсов в **ResourceDictionary**.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object property="{ThemeResource key}" .../>
```

## <a name="xaml-values"></a>Значения XAML

| Термин | Описание |
|------|-------------|
| ключ | Ключ для запрашиваемого ресурса. Изначально ключ назначается атрибутом [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary). Ключом ресурса может быть любая строка, определенная в грамматике XamlName. |

## <a name="remarks"></a>Примечания

**ThemeResource** — это метод получения для атрибута XAML значений, которые определены где-то в словаре ресурсов XAML. Это расширение разметки выполняет ту же основную функцию, что и [Расширение разметки {StaticResource}](staticresource-markup-extension.md). Отличие от расширения разметки {StaticResource} в том, что ссылка **ThemeResource** может динамически использовать разные словари в качестве основного места поиска в зависимости от того, какая тема используется системой в данный момент.

Когда приложение запускается в первый раз, все ссылки на ресурсы при помощи **ThemeResource** обрабатываются для темы, которая используется при запуске. Если после этого пользователь сменит активную тему во время выполнения, то система заново обрабатывает все ссылки **ThemeResource**, получает ресурс, соответствующий новой теме (он может быть другим), и отображает приложение с новыми значениями ресурсов во всех соответствующих местах визуального дерева. Значение **StaticResource** определяется во время загрузки XAML или запуска приложения и не обрабатывается заново во время выполнения. (Существуют и другие приемы, например визуальные состояния, которые динамически перезагружают код XAML, но они работают на более высоком уровне по сравнению с базовой обработкой ресурсов, поддерживаемой ресурсом [Расширение разметки {StaticResource}](staticresource-markup-extension.md).)

**ThemeResource** принимает один аргумент, в котором задается ключ для запрашиваемого ресурса. Ключ ресурса всегда является строкой XAML в среде выполнения Windows. Дополнительные сведения о том, как изначально задается ключ ресурса, см. в разделе [Атрибут x:Key](x-key-attribute.md).

Дополнительные сведения о том, как определять ресурсы и правильно использовать [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary), включая образец кода, см. в разделе [Ссылки ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/resourcedictionary-and-xaml-resource-references).

**Важно** Как и **StaticResource**, расширение **ThemeResource** не должно пытаться создать опережающую ссылку на ресурс, который лексически определен далее в XAML-файле. Создание таких ссылок не поддерживается. Даже если опережающая ссылка не вызовет ошибку, ее создание приведет к снижению производительности. Для достижения наилучших результатов составляйте словари ресурсов так, чтобы обходиться без опережающих ссылок.

Если задать в **ThemeResource** ключ, который не удается разрешить, то во время выполнения создается исключение синтаксического анализа XAML. Средства разработки также могут выдавать предупреждения и ошибки.

В реализации процессора XAML в среде выполнения Windows отсутствует представление класса резервирования для функций **ThemeResource**. Наиболее близким аналогом в коде является использование API из коллекции [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary), например вызов [**Contains**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.contains) или [**TryGetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.trygetvalue).

**ThemeResource** является расширением разметки. Расширения разметки обычно реализуются, если необходимо, чтобы значения атрибутов являлись буквенными значениями или именами обработчиков, и это требование является более глобальным, чем простая настройка преобразователей типов для определенных типов или свойств. Для всех расширений разметки в XAML в синтаксисе атрибутов используются символы "{" и "}". Это соответствует соглашению, по которому процессор XAML распознает, что расширение разметки должно обработать атрибут.

### <a name="when-and-how-to-use-themeresource-rather-than-staticresource"></a>Когда и как следует использовать {ThemeResource} вместо {StaticResource}

Правила, по которым **ThemeResource** разрешается в элемент из словаря ресурсов, обычно те же, что для **StaticResource**. Поиск **ThemeResource** может распространяться на файлы [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary), на которые ссылается коллекция [**ThemeDictionaries**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries), но такая возможность есть и в **StaticResource**. Разница состоит в том, что **ThemeResource** может повторно обрабатываться во время выполнения, а **StaticResource** — нет.

Набор ключей в словаре каждой темы должен предоставлять одинаковый набор ресурсов по ключу независимо от активной темы. Если ресурс, задаваемый по ключу, существует в словаре темы **HighContrast**, то в темах **Light** и **Default** должен существовать другой ресурс с таким же именем. Если это не так, то при смене темы поиск ресурса может оказаться неудачным, и приложение будет выглядеть неправильно. Возможно, что словарь темы будет содержать ресурсы с ключами, обращение к которым для получения дополнительных значений выполняется только из своей области. Такие ресурсы не обязательно должны быть эквивалентными во всех темах.

Обычно ресурсы следует размещать в словарях тем и обращаться к ним с помощью **ThemeResource** только в случаях, когда значения могут быть разными в разных темах или зависят от значений, которые могут меняться. Это справедливо для следующих типов ресурсов.

-   Кисти, в частности цвета для [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush). На них приходится примерно 80% случаев использования **ThemeResource** в шаблонах элементов управления XAML по умолчанию (generic.xaml).
-   Значения в пикселях для границ, смещений, полей, заполнения и т. п.
-   Свойства шрифтов, в частности **FontFamily** или **FontSize**.
-   Законченные шаблоны для ограниченного числа элементов управления, стиль которых обычно определяется системой и которые служат для динамического представления данных, например [**GridViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridViewItem) и [**ListViewItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListViewItem).
-   Стили отображения текста (обычно для изменения цвета шрифта, фона и иногда размера).

Среда выполнения Windows предоставляет набор ресурсов, который специально предназначен для того, чтобы на него ссылалась **ThemeResource**. Все они перечислены в составе XAML-файла themeresources.xaml, находящегося в папке include/winrt/xaml/design как компонент пакета средств разработки программного обеспечения для Windows (SDK). Документацию по кистям и дополнительным стилям темы, определенным в themeresources.xaml, см. в разделе [Справочник ресурсов темы XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/xaml-theme-resources). Информация о кистях приведена в таблице с данными о том, какое значение цвета имеет каждая кисть для каждой из трех возможных активных тем.

Если имеется базовый ресурс, который может измениться вместе с темой, то определения XAML для визуальных состояний в шаблоне элемента управления должны использовать ссылки **ThemeResource**. Изменение системной темы, как правило, не ведет к изменению визуального состояния. Ресурсы в этом случае должны использовать ссылки **ThemeResource**, чтобы можно было повторно обработать значения для остающегося активным визуального состояния. Например, если некоторое визуальное состояние меняет цвет кисти в какой-то части пользовательского интерфейса и одно из его свойств, а цвет этой кисти зависит от темы, то следует использовать ссылку **ThemeResource** для получения значения этого свойства в шаблоне по умолчанию и во всех изменениях шаблона по умолчанию для визуальных состояний.

Использование **ThemeResource** можно представить как последовательность зависимых значений. Например, значение [**Color**](https://docs.microsoft.com/uwp/api/Windows.UI.Color), используемое параметром [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush), который также является ресурсом, доступным по ключу, может использовать ссылку **ThemeResource**. Но все свойства пользовательского интерфейса, использующие ресурс **SolidColorBrush** с ключом, будут использовать также ссылку **ThemeResource**, поэтому динамическое изменение значения при смене темы обеспечивается каждым свойством типа [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush).

**Примечание**   `{ThemeResource}` и поддерживается в Windows 8.1 XAML на переключение тем анализ ресурсов во время выполнения, но не поддерживается в XAML для приложения, предназначенные для Windows 8.

### <a name="system-resources"></a>Системные ресурсы

Некоторые ресурсы темы ссылаются на значения системных ресурсов в качестве базового значения. Системный ресурс имеет особое значение, которое не входит ни в один словарь ресурсов XAML. Такие значения зависят от поддержки XAML в среде выполнения Windows, которая получает значения из системы и представляет их в формате, на который может ссылаться ресурс XAML. Например, системный ресурс "SystemColorButtonFaceColor" представляет цвет RGB. Этот цвет берется из аспектов системных цветов и тем, которые не привязаны к среде выполнения Windows и приложениям среды выполнения Windows.

Системные ресурсы часто служат базовыми значениями для тем с высокой контрастностью. Пользователь может выбирать цвета для своих тем с высокой контрастностью и использует для выбора возможности системы, которые также не привязаны к приложениям среды выполнения Windows. Указывая системные ресурсы в ссылках **ThemeResource**, можно использовать в темах с высокой контрастностью для приложений среды выполнения Windows специальные значения, которые выбираются пользователем и предоставляются системой. Кроме того, если система обнаруживает изменение темы во время выполнения, то ссылки помечаются для повторной оценки.

### <a name="an-example-themeresource-usage"></a>Пример использования {ThemeResource}

Вот пример XAML, взятый с файлов generic.xaml и themeresources.xaml по умолчанию, чтобы проиллюстрировать варианты использования **ThemeResource**. Рассмотрим один шаблон (стандартный [**Button**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button)) и объявление двух свойств ([**Background**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.background) и [**Foreground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground)), которые должны реагировать на изменение темы.

```xml
    <!-- Default style for Windows.UI.Xaml.Controls.Button -->
    <Style TargetType="Button">
        <Setter Property="Background" Value="{ThemeResource ButtonBackgroundThemeBrush}" />
        <Setter Property="Foreground" Value="{ThemeResource ButtonForegroundThemeBrush}"/>
...
```

Свойства принимают значение [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush), а ссылка на ресурсы [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) с именами `ButtonBackgroundThemeBrush` и `ButtonForegroundThemeBrush` создается с помощью **ThemeResource**.

Эти же свойства также изменяются некоторыми визуальными состояниями для [**Button**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button). Обратите внимание, что при нажатии кнопки меняется цвет фона. В этом случае анимации [**Background**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.background) и [**Foreground**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.control.foreground) в раскадровке визуального состояния также используют объекты [**DiscreteObjectKeyFrame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DiscreteObjectKeyFrame) и ссылки на кисти с **ThemeResource** в качестве значения опорного кадра.

```xml
<VisualState x:Name="Pressed">
  <Storyboard>
    <ObjectAnimationUsingKeyFrames Storyboard.TargetName="Border"
        Storyboard.TargetProperty="Background">
      <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonPressedBackgroundThemeBrush}" />
    </ObjectAnimationUsingKeyFrames>
    <ObjectAnimationUsingKeyFrames Storyboard.TargetName="ContentPresenter"
         Storyboard.TargetProperty="Foreground">
       <DiscreteObjectKeyFrame KeyTime="0" Value="{ThemeResource ButtonPressedForegroundThemeBrush}" />
    </ObjectAnimationUsingKeyFrames>
  </Storyboard>
</VisualState>
```

Каждая из этих кистей определена ранее в файле generic.xaml. Они должны быть определены до использования в шаблонах, чтобы избежать опережающих ссылок XAML. Вот эти определения для словаря темы "Default".

```xml
    <ResourceDictionary.ThemeDictionaries>
        <ResourceDictionary x:Key="Default">
...
            <SolidColorBrush x:Key="ButtonBackgroundThemeBrush" Color="Transparent" />
            <SolidColorBrush x:Key="ButtonForegroundThemeBrush" Color="#FFFFFFFF" />
...
            <SolidColorBrush x:Key="ButtonPressedBackgroundThemeBrush" Color="#FFFFFFFF" />
            <SolidColorBrush x:Key="ButtonPressedForegroundThemeBrush" Color="#FF000000" />
...
```

Затем эти кисти определяются в каждом из других словарей темы, например:

```xml
        <ResourceDictionary x:Key="HighContrast">
            <!-- High Contrast theme resources -->
...
            <SolidColorBrush x:Key="ButtonBackgroundThemeBrush" Color="{ThemeResource SystemColorButtonFaceColor}" />
            <SolidColorBrush x:Key="ButtonForegroundThemeBrush" Color="{ThemeResource SystemColorButtonTextColor}" />

...
            <SolidColorBrush x:Key="ButtonPressedBackgroundThemeBrush" Color="{ThemeResource SystemColorButtonTextColor}" />
            <SolidColorBrush x:Key="ButtonPressedForegroundThemeBrush" Color="{ThemeResource SystemColorButtonFaceColor}" />
```

Здесь значением [**Color**](/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush.Color) является другая ссылка **ThemeResource** на системный ресурс. Если ссылка на системный ресурс должна изменяться в ответ на изменение темы, то также надо использовать **ThemeResource**.

## <a name="windows8-behavior"></a>Поведение Windows 8

Windows 8 не поддерживали **ThemeResource** расширения разметки, он доступен начиная с Windows 8.1. Кроме того Windows 8 не поддерживали динамического переключения ресурсов, зависимых от темы, для приложения среды выполнения Windows. Чтобы обработать изменение темы для шаблонов и стилей XAML, приложение необходимо было перезапускать. Это не является эффективное взаимодействие с пользователем, поэтому приложения, настоятельно рекомендуется перекомпилировать и предназначенных для Windows 8.1, чтобы они могли использовать стили с **ThemeResource** данные об использовании и динамически переключать темы, при выполнении пользователем. Приложения, которые были скомпилированы для Windows 8, но под управлением Windows 8.1 продолжать использовать поведение Windows 8.

## <a name="design-time-tools-support-for-the-themeresource-markup-extension"></a>Поддержка расширения разметки **{ThemeResource}** средствами "Время разработки"

Microsoft Visual Studio 2013 можно включить возможные значения клавиш в раскрывающихся списках Microsoft IntelliSense при использовании **{ThemeResource}** расширение разметки на странице XAML. Например, при вводе "{ThemeResource", отображаются ключи ресурсов из [ресурсов тем XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/xaml-theme-resources).

Если ключ ресурса существует при использовании **{ThemeResource}** , функция **Перейти к определению** (F12) может обрабатывать этот ресурс и отображать generic.xaml для времени разработки, в котором определен ресурс темы. Ресурсы тем определяются неоднократно (для каждой темы), поэтому команда **Перейти к определению** осуществляет переход к первому определению, найденному в файле, которое является определением для **Default**. Если требуются другие определения, можно выполнить в файле поиск по имени ключа и найти определения других тем.

## <a name="related-topics"></a>См. также

* [Ссылки на ресурсы ResourceDictionary и XAML](https://docs.microsoft.com/windows/uwp/controls-and-patterns/resourcedictionary-and-xaml-resource-references)
* [Ресурсы XAML темы](https://docs.microsoft.com/windows/uwp/controls-and-patterns/xaml-theme-resources)
* [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary)
* [x: Key-атрибут](x-key-attribute.md)
 

