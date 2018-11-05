---
author: jwmsft
description: Предоставляет значение для любого атрибута XAML, обрабатывая ссылку на ресурс. Дополнительная системная логика возвращает различные ресурсы в зависимости от активной темы.
title: Расширение разметки ThemeResource
ms.assetid: 8A1C79D2-9566-44AA-B8E1-CC7ADAD1BCC5
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 024e48380941c0d79eef65780396ec9b89edc3c7
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6039523"
---
# <a name="themeresource-markup-extension"></a>Расширение разметки {ThemeResource}

Предоставляет значение для любого атрибута XAML, обрабатывая ссылку на ресурс. Дополнительная системная логика возвращает различные ресурсы в зависимости от активной темы. Аналогично ресурсу [Расширение разметки {StaticResource}](staticresource-markup-extension.md), ресурсы определены в [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794), а в **ThemeResource** используются ссылки на ключи ресурсов в **ResourceDictionary**.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

``` syntax
<object property="{ThemeResource key}" .../>
```

## <a name="xaml-values"></a>Значения XAML

| Термин | Описание |
|------|-------------|
| key | Ключ для запрашиваемого ресурса. Изначально ключ назначается атрибутом [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794). Ключом ресурса может быть любая строка, определенная в грамматике XamlName. |

## <a name="remarks"></a>Комментарии

**ThemeResource** — это метод получения для атрибута XAML значений, которые определены где-то в словаре ресурсов XAML. Это расширение разметки выполняет ту же основную функцию, что и [Расширение разметки {StaticResource}](staticresource-markup-extension.md). Отличие от расширения разметки {StaticResource} в том, что ссылка **ThemeResource** может динамически использовать разные словари в качестве основного места поиска в зависимости от того, какая тема используется системой в данный момент.

Когда приложение запускается в первый раз, все ссылки на ресурсы при помощи **ThemeResource** обрабатываются для темы, которая используется при запуске. Если после этого пользователь сменит активную тему во время выполнения, то система заново обрабатывает все ссылки **ThemeResource**, получает ресурс, соответствующий новой теме (он может быть другим), и отображает приложение с новыми значениями ресурсов во всех соответствующих местах визуального дерева. Значение **StaticResource** определяется во время загрузки XAML или запуска приложения и не обрабатывается заново во время выполнения. (Существуют и другие приемы, например визуальные состояния, которые динамически перезагружают код XAML, но они работают на более высоком уровне по сравнению с базовой обработкой ресурсов, поддерживаемой ресурсом [Расширение разметки {StaticResource}](staticresource-markup-extension.md).)

**ThemeResource** принимает один аргумент, в котором задается ключ для запрашиваемого ресурса. Ключ ресурса всегда является строкой XAML в среде выполнения Windows. Дополнительные сведения о том, как изначально задается ключ ресурса, см. в разделе [Атрибут x:Key](x-key-attribute.md).

Дополнительные сведения о том, как определять ресурсы и правильно использовать [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794), включая образец кода, см. в разделе [Ссылки ResourceDictionary и XAML](https://msdn.microsoft.com/library/windows/apps/mt187273).

**Важно** Как и **StaticResource**, расширение **ThemeResource** не должно пытаться создать опережающую ссылку на ресурс, который лексически определен далее в XAML-файле. Создание таких ссылок не поддерживается. Даже если опережающая ссылка не вызовет ошибку, ее создание приведет к снижению производительности. Для достижения наилучших результатов составляйте словари ресурсов так, чтобы обходиться без опережающих ссылок.

Если задать в **ThemeResource** ключ, который не удается разрешить, то во время выполнения создается исключение синтаксического анализа XAML. Средства разработки также могут выдавать предупреждения и ошибки.

В реализации процессора XAML в среде выполнения Windows отсутствует представление класса резервирования для функций **ThemeResource**. Наиболее близким аналогом в коде является использование API из коллекции [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794), например вызов [**Contains**](https://msdn.microsoft.com/library/windows/apps/jj635925) или [**TryGetValue**](https://msdn.microsoft.com/library/windows/apps/jj603139).

**ThemeResource** является расширением разметки. Расширения разметки обычно реализуются, если необходимо, чтобы значения атрибутов являлись буквенными значениями или именами обработчиков, и это требование является более глобальным, чем простая настройка преобразователей типов для определенных типов или свойств. Для всех расширений разметки в XAML в синтаксисе атрибутов используются символы "{" и "}". Это соответствует соглашению, по которому процессор XAML распознает, что расширение разметки должно обработать атрибут.

### <a name="when-and-how-to-use-themeresource-rather-than-staticresource"></a>Когда и как следует использовать {ThemeResource} вместо {StaticResource}

Правила, по которым **ThemeResource** разрешается в элемент из словаря ресурсов, обычно те же, что для **StaticResource**. Поиск **ThemeResource** может распространяться на файлы [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794), на которые ссылается коллекция [**ThemeDictionaries**](https://msdn.microsoft.com/library/windows/apps/br208807), но такая возможность есть и в **StaticResource**. Разница состоит в том, что **ThemeResource** может повторно обрабатываться во время выполнения, а **StaticResource** — нет.

Набор ключей в словаре каждой темы должен предоставлять одинаковый набор ресурсов по ключу независимо от активной темы. Если ресурс, задаваемый по ключу, существует в словаре темы **HighContrast**, то в темах **Light** и **Default** должен существовать другой ресурс с таким же именем. Если это не так, то при смене темы поиск ресурса может оказаться неудачным, и приложение будет выглядеть неправильно. Возможно, что словарь темы будет содержать ресурсы с ключами, обращение к которым для получения дополнительных значений выполняется только из своей области. Такие ресурсы не обязательно должны быть эквивалентными во всех темах.

Обычно ресурсы следует размещать в словарях тем и обращаться к ним с помощью **ThemeResource** только в случаях, когда значения могут быть разными в разных темах или зависят от значений, которые могут меняться. Это справедливо для следующих типов ресурсов.

-   Кисти, в частности цвета для [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/br242962). На них приходится примерно 80% случаев использования **ThemeResource** в шаблонах элементов управления XAML по умолчанию (generic.xaml).
-   Значения в пикселях для границ, смещений, полей, заполнения и т. п.
-   Свойства шрифтов, в частности **FontFamily** или **FontSize**.
-   Законченные шаблоны для ограниченного числа элементов управления, стиль которых обычно определяется системой и которые служат для динамического представления данных, например [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/hh738501) и [**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/br242919).
-   Стили отображения текста (обычно для изменения цвета шрифта, фона и иногда размера).

Среда выполнения Windows предоставляет набор ресурсов, который специально предназначен для того, чтобы на него ссылалась **ThemeResource**. Все они перечислены в составе XAML-файла themeresources.xaml, находящегося в папке include/winrt/xaml/design как компонент пакета средств разработки программного обеспечения для Windows (SDK). Документацию по кистям и дополнительным стилям темы, определенным в themeresources.xaml, см. в разделе [Справочник ресурсов темы XAML](https://msdn.microsoft.com/library/windows/apps/mt187274). Информация о кистях приведена в таблице с данными о том, какое значение цвета имеет каждая кисть для каждой из трех возможных активных тем.

Если имеется базовый ресурс, который может измениться вместе с темой, то определения XAML для визуальных состояний в шаблоне элемента управления должны использовать ссылки **ThemeResource**. Изменение системной темы, как правило, не ведет к изменению визуального состояния. Ресурсы в этом случае должны использовать ссылки **ThemeResource**, чтобы можно было повторно обработать значения для остающегося активным визуального состояния. Например, если некоторое визуальное состояние меняет цвет кисти в какой-то части пользовательского интерфейса и одно из его свойств, а цвет этой кисти зависит от темы, то следует использовать ссылку **ThemeResource** для получения значения этого свойства в шаблоне по умолчанию и во всех изменениях шаблона по умолчанию для визуальных состояний.

Использование **ThemeResource** можно представить как последовательность зависимых значений. Например, значение [**Color**](https://msdn.microsoft.com/library/windows/apps/hh673723), используемое параметром [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/br242962), который также является ресурсом, доступным по ключу, может использовать ссылку **ThemeResource**. Но все свойства пользовательского интерфейса, использующие ресурс **SolidColorBrush** с ключом, будут использовать также ссылку **ThemeResource**, поэтому динамическое изменение значения при смене темы обеспечивается каждым свойством типа [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush).

**Примечание** `{ThemeResource}` и разрешение ресурсов во время выполнения при смене темы поддерживается в Windows8.1 XAML, но не поддерживается в XAML для приложений, предназначенных для Windows8.

### <a name="system-resources"></a>Системные ресурсы

Некоторые ресурсы темы ссылаются на значения системных ресурсов в качестве базового значения. Системный ресурс имеет особое значение, которое не входит ни в один словарь ресурсов XAML. Такие значения зависят от поддержки XAML в среде выполнения Windows, которая получает значения из системы и представляет их в формате, на который может ссылаться ресурс XAML. Например, системный ресурс "SystemColorButtonFaceColor" представляет цвет RGB. Этот цвет берется из аспектов системных цветов и тем, которые не привязаны к среде выполнения Windows и приложениям среды выполнения Windows.

Системные ресурсы часто служат базовыми значениями для тем с высокой контрастностью. Пользователь может выбирать цвета для своих тем с высокой контрастностью и использует для выбора возможности системы, которые также не привязаны к приложениям среды выполнения Windows. Указывая системные ресурсы в ссылках **ThemeResource**, можно использовать в темах с высокой контрастностью для приложений среды выполнения Windows специальные значения, которые выбираются пользователем и предоставляются системой. Кроме того, если система обнаруживает изменение темы во время выполнения, то ссылки помечаются для повторной оценки.

### <a name="an-example-themeresource-usage"></a>Пример использования {ThemeResource}

Вот пример XAML, взятый с файлов generic.xaml и themeresources.xaml по умолчанию, чтобы проиллюстрировать варианты использования **ThemeResource**. Рассмотрим один шаблон (стандартный [**Button**](https://msdn.microsoft.com/library/windows/apps/br209265)) и объявление двух свойств ([**Background**](https://msdn.microsoft.com/library/windows/apps/br209395) и [**Foreground**](https://msdn.microsoft.com/library/windows/apps/br209414)), которые должны реагировать на изменение темы.

```xml
    <!-- Default style for Windows.UI.Xaml.Controls.Button -->
    <Style TargetType="Button">
        <Setter Property="Background" Value="{ThemeResource ButtonBackgroundThemeBrush}" />
        <Setter Property="Foreground" Value="{ThemeResource ButtonForegroundThemeBrush}"/>
...
```

Свойства принимают значение [**Brush**](/uwp/api/Windows.UI.Xaml.Media.Brush), а ссылка на ресурсы [**SolidColorBrush**](https://msdn.microsoft.com/library/windows/apps/br242962) с именами `ButtonBackgroundThemeBrush` и `ButtonForegroundThemeBrush` создается с помощью **ThemeResource**.

Эти же свойства также изменяются некоторыми визуальными состояниями для [**Button**](https://msdn.microsoft.com/library/windows/apps/br209265). Обратите внимание, что при нажатии кнопки меняется цвет фона. В этом случае анимации [**Background**](https://msdn.microsoft.com/library/windows/apps/br209395) и [**Foreground**](https://msdn.microsoft.com/library/windows/apps/br209414) в раскадровке визуального состояния также используют объекты [**DiscreteObjectKeyFrame**](https://msdn.microsoft.com/library/windows/apps/br243132) и ссылки на кисти с **ThemeResource** в качестве значения опорного кадра.

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

## <a name="windows8-behavior"></a>Поведение Windows8

Windows8 не поддерживалось расширение разметки **ThemeResource** , он доступен, начиная с Windows8.1. Кроме того Windows8 не поддерживалось динамическое переключение ресурсов темы для приложения среды выполнения Windows. Чтобы обработать изменение темы для шаблонов и стилей XAML, приложение необходимо было перезапускать. Это не хорошее взаимодействие, поэтому настоятельно рекомендуется перекомпиляции и целевой объект Windows8.1 приложения, чтобы они могли использовать стили с использованием **ThemeResource** и динамически переключать темы по выбору пользователя. Приложения, которые скомпилированы для Windows8, но выполняются в Windows8.1 продолжают использовать Windows8.

## <a name="design-time-tools-support-for-the-themeresource-markup-extension"></a>Поддержка расширения разметки **{ThemeResource}** средствами "Время разработки"

Microsoft Visual Studio2013 может содержать возможные значения ключей в раскрывающихся списков Microsoft IntelliSense при использовании расширения разметки **{ThemeResource}** в XAML-страницы. Например, при вводе "{ThemeResource", отображаются ключи ресурсов из [ресурсов тем XAML](https://msdn.microsoft.com/library/windows/apps/mt187274).

Если ключ ресурса существует при использовании **{ThemeResource}**, функция **Перейти к определению** (F12) может обрабатывать этот ресурс и отображать generic.xaml для времени разработки, в котором определен ресурс темы. Ресурсы тем определяются неоднократно (для каждой темы), поэтому команда **Перейти к определению** осуществляет переход к первому определению, найденному в файле, которое является определением для **Default**. Если требуются другие определения, можно выполнить в файле поиск по имени ключа и найти определения других тем.

## <a name="related-topics"></a>Ссылки по теме

* [Ссылки на ресурсы ResourceDictionary и XAML](https://msdn.microsoft.com/library/windows/apps/mt187273)
* [Ресурсы темы XAML](https://msdn.microsoft.com/library/windows/apps/mt187274)
* [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/br208794)
* [Атрибут x:Key](x-key-attribute.md)
 

