---
author: Xansky
Description: "В этом разделе описываются шаги, которые необходимо предпринять, чтобы ваше приложение универсальной платформы Windows (UWP) можно было использовать, когда активна тема с высокой контрастностью."
ms.assetid: FD7CA6F6-A8F1-47D8-AA6C-3F2EC3168C45
title: "Темы с высокой контрастностью"
label: High-contrast themes
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: 50c37d71d3455fc2417d70f04e08a9daff2e881e
ms.openlocfilehash: 4201f5a0b08f1fc8d691218da0803ee04ab2c86a

---

# Темы с высокой контрастностью  

В этом разделе описываются шаги, которые необходимо предпринять, чтобы ваше приложение универсальной платформы Windows (UWP) можно было использовать, когда активна тема с высокой контрастностью.

В приложении UWP темы с высокой контрастностью поддерживаются по умолчанию. Если пользователь выбрал тему с высокой контрастностью в системных параметрах или в средствах специальных возможностей, платформа автоматически использует настройки цветов и стиля, обеспечивающие высокую контрастность макета, элементов управления и компонентов в пользовательском интерфейсе.

Такая поддержка по умолчанию основана на использовании шаблонов и тем по умолчанию. Эти темы и шаблоны ссылаются в определениях ресурсов на системные цвета. Когда система использует режим высокой контрастности, источники ресурсов изменяются автоматически. Если же вы используете для элементов управления другие шаблоны, темы и стили, будьте внимательны: не отключайте встроенную поддержку высокой контрастности. Если для создания стиля вы используете один из конструкторов XAML для Microsoft Visual Studio, то вместе с основной темой конструктор будет создавать отдельную тему с высокой контрастностью каждый раз, когда вы определите шаблон, который значительно отличается от шаблона по умолчанию. Отдельные словари тем переходят в коллекцию [**ThemeDictionaries**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.resourcedictionary.themedictionaries.aspx), специальное свойство элемента [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/BR208794).

Дополнительные сведения о темах и шаблонах элементов управления см. в статье [Краткое руководство: шаблоны элементов управления](https://msdn.microsoft.com/library/windows/apps/xaml/Hh465374). Зачастую полезно ознакомиться со словарями ресурсов XAML и темами для конкретных элементов управления и узнать, как строятся темы и как они ссылаются на ресурсы, которые в целом похожи, но все же различаются для каждой возможной настройки высокой контрастности.

## Тематические словари

Если необходимо использовать цвет, отличный от используемого системой по умолчанию, или добавить изображения для украшения, например установить фоновое изображение, создайте коллекцию **ThemeDictionaries** для своего приложения.

* Начните с создания надлежащих элементов подключения, если они еще не созданы. В файле App.xaml создайте коллекцию **ThemeDictionaries**:

``` xaml
 <Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <!-- Default is a fallback if a more precise theme isn't called out below -->
            <ResourceDictionary x:Key="Default">

            </ResourceDictionary>
            <!-- HighContrast is used in any high contrast theme -->
            <ResourceDictionary x:Key="HighContrast">

            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>
    </ResourceDictionary>
</Application.Resources
```

* 
            **HighContrast** — не единственное возможное имя ключа. Также существуют **HighContrastBlack**, **HighContrastWhite** и **HighContrastCustom**. В большинстве случаев **HighContrast** будет достаточно.
* В разделе **Default** создайте тот тип инструмента [**Brush**](http://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.media.brush.aspx), который вам необходим; чаще всего это **SolidColorBrush**. Задайте ему имя **x:Key**, указывающее его предназначение.<br/>
    `<SolidColorBrush x:Key="BrandedPageBackground" />`
* Назначьте ему требуемое значение цвета **Color**.<br/>
    `<SolidColorBrush x:Key="BrandedPageBackground" Color="Red" />`
* Скопируйте этот инструмент **Brush** в **HighContrast**.

``` xaml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <!-- Default is a fallback if a more precise theme isn't called out below -->
            <ResourceDictionary x:Key="Default">
                <SolidColorBrush x:Key="BrandedPageBackground" Color="Red" />
            </ResourceDictionary>
            <!-- HighContrast is used in any high contrast theme -->
            <ResourceDictionary x:Key="HighContrast">
                <SolidColorBrush x:Key="BrandedPageBackground" Color="Red" />
            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

* Определите, какого цвета должен быть ваш инструмент **Brush**, и измените его в **HighContrast**.

Определение цвета для режима высокой контрастности требует определенной подготовки. Созданные вами ранее элементы подключения облегчают изменение кода.

## Высококонтрастные цвета

Пользователи могут переключаться в режим высокой контрастности на странице параметров. Существует 4 темы с высокой контрастностью по умолчанию. Когда пользователь выбирает какой-либо вариант, на странице отображается предварительный образец того, как будут выглядеть приложения.

![Параметры высокой контрастности](images/high-contrast-settings.png)<br/>
_Параметры высокой контрастности_

 Каждый квадрат на предварительном образце можно нажать, чтобы изменить его значение. Каждый квадрат также непосредственно сопоставляется системному ресурсу.

![Высококонтрастные ресурсы](images/high-contrast-resources.png)<br/>
_Высококонтрастные ресурсы_

Если использовать для приведенных выше имен префиксы _SystemColor_ и постфиксы _Color_, например **SystemColorWindowTextColor**, они будут динамически обновляться в соответствии с параметрами, которые укажет пользователь. Это позволяет вам не выбирать конкретный цвет для режима высокой контрастности. Вместо этого выберите системный ресурс, соответствующий тому, для чего используется этот цвет. В приведенном выше примере мы задали цвету фона страницы имя **SolidColorBrushBrandedPageBackground**. Поскольку этот элемент будет использоваться для фона, можно сопоставить ему **SystemColorWindowColor** в режиме высокой контрастности.

``` xaml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <!-- Default is a fallback if a more precise theme isn't called out below -->
            <ResourceDictionary x:Key="Default">
                <SolidColorBrush x:Key="BrandedPageBackground" Color="Red" />
            </ResourceDictionary>
            <!-- HighContrast is used in any high contrast theme -->
            <ResourceDictionary x:Key="HighContrast">
                <SolidColorBrush x:Key="BrandedPageBackground" Color="{ThemeResource SystemColorWindowColor}" />
            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

При работе с исходной палитрой из 8 высококонтрастных цветов не требуется создавать какие-либо дополнительные высококонтрастные словари **ResourceDictionaries**. Эта ограниченная палитра может вызвать определенные сложности при представлении сложных визуальных состояний. Зачастую добавление границы к области только в режиме высокой контрастности может улучшить ситуацию.

### Рекомендации

* Проводите тестирование в режиме высокой контрастности часто и на ранних стадиях разработки.
* Присваивайте имена цветам с указанием их предназначения.
* Помещайте примитивы, такие как **Color**, **Brush** и **Thickness** в словари **ThemeDictionaries**. Старайтесь не размещать в них более сложные ресурсы, такие как **Style**. Следующий пример работает отлично.

``` xaml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <!-- Default is a fallback if a more precise theme isn't called out below -->
            <ResourceDictionary x:Key="Default">
                <SolidColorBrush x:Key="BrandedPageBackground" Color="Red" />
            </ResourceDictionary>
            <!-- HighContrast is used in any high contrast theme -->
            <ResourceDictionary x:Key="HighContrast">
                <SolidColorBrush x:Key="BrandedPageBackground" Color="{ThemeResource SystemColorWindowColor}" />
            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>

        <Style x:Key="MyButtonStyle" TargetType="Button">
            <Setter Property="Foreground" Value="{ThemeResource BrandedPageBackground}" />
        </Style>
    </ResourceDictionary>
</Application.Resources>

...

<Button Style="{StaticResource MyButtonStyle}" />
```

* Используйте высококонтрастные цвета переднего плана для элементов пользовательского интерфейса, расположенных на переднем плане.
* Используйте высококонтрастные цвета с заданными цветовыми парами. Например, всегда используйте **BUTTONTEXT** с **BUTTONFACE**, особенно в ситуациях с использованием переднего и заднего плана.
* Используйте рекомендуемые пары высококонтрастных цветов для определенных элементов пользовательского интерфейса, чтобы достичь требуемого коэффициента контрастности 14:1.
* Не разбивайте пары высококонтрастных цветов и не смешивайте высококонтрастные цвета произвольным образом. Зачастую это приводит к образованию невидимых элементов пользовательского интерфейса хотя бы в одной из предустановленных высококонтрастных тем.
* Не используйте объекты **Brush**, созданные за пределами коллекции **ThemeDictionaries**.
* Ни в коем случае не используйте **StaticResource** для ссылок на ресурс в коллекции **ThemeDictionaries**. Возможно, это будет работать, но только до тех пор, пока пользователь не сменит тему во время работы приложения. Вместо этого используйте **ThemeResource**.
* Не используйте жестко заданные значения цвета.
* Не используйте какой-либо цвет просто потому, что он вам нравится.

Дополнительные сведения см. в разделе [Ресурсы тем в XAML](https://msdn.microsoft.com/windows/uwp/controls-and-patterns/xaml-theme-resources).

## Когда следует использовать границы
В режиме высокой контрастности добавляйте границы элементу пользовательского интерфейса, если необходимо сохранить узнаваемые очертания этого элемента. Используйте границы для различения между областями, используемыми для содержимого, навигации и действий.

![Область навигации отделена от других частей страницы](images/high-contrast-actions-content.png)<br/>
_Область навигации отделена от других частей страницы_

Если элемент пользовательского интерфейса _не_ имеет границы или фона по умолчанию, не добавляйте границу или фон в состояние по умолчанию для режима высокой контрастности.

Если элемент пользовательского интерфейса _имеет_ границу по умолчанию, сохраните границу в режиме высокой контрастности.

Перекрывающиеся и соседствующие цвета должны отличаться от друг от друга, но они не обязательно должны достигать коэффициента цветовой контрастности 14:1. Однако для таких случаев рекомендуется использовать коэффициент контрастности 3:1.

Если для различения перекрывающихся элементов пользовательского интерфейса используются высококонтрастные цвета фона, единственным методом гарантированного обеспечения контрастности между этими элементами является применение границ.

## Обнаружение включения темы с высокой контрастностью  
Используйте члены класса [**AccessibilitySettings**](https://msdn.microsoft.com/library/windows/apps/BR242237), чтобы определить текущие параметры тем с высокой контрастностью. Свойство [**HighContrast**](https://msdn.microsoft.com/library/windows/apps/windows.ui.viewmanagement.accessibilitysettings.highcontrast) определяет, установлена ли в данный момент тема с высокой контрастностью. Если свойство **HighContrast** имеет значение **true**, то нужно проверить значение свойства [**HighContrastScheme**](https://msdn.microsoft.com/library/windows/apps/windows.ui.viewmanagement.accessibilitysettings.highcontrastscheme), чтобы получить имя используемой высококонтрастной темы. Типичные значения **HighContrastScheme**, на которые должен реагировать ваш код, — "High Contrast White" (Контрастная белая) и "High Contrast Black" (Контрастная черная). Определяемые в XAML ключи [**ResourceDictionary**](https://msdn.microsoft.com/library/windows/apps/BR208794) не могут содержать пробелов. Поэтому ключи этих тем в словаре ресурсов обычно имеют вид "HighContrastWhite" и "HighContrastBlack" соответственно. Следует также предусмотреть логику перехода для темы с высокой контрастностью по умолчанию на случай, если значение задается другой строкой. 
            Такую логику демонстрирует [пример XAML с высокой контрастностью](http://go.microsoft.com/fwlink/p/?linkid=254993).

> [!NOTE]
> Убедитесь, что конструктор [**AccessibilitySettings**](https://msdn.microsoft.com/library/windows/apps/BR242237) вызывается из области, в которой приложение инициализировано и уже отображает содержимое.

Приложения могут переключаться на значения ресурсов для высокой контрастности во время выполнения. Это работает, если ресурсы запрашиваются при помощи [расширения разметки {ThemeResource}](https://msdn.microsoft.com/library/windows/apps/Mt185591) в коде XAML стиля или шаблона. Такой прием с расширением разметки {ThemeResource} действует для всех тем по умолчанию (generic.xaml). Благодаря этому при использовании тем элементов управления по умолчанию можно переходить на высокую контрастность во время работы приложения. Если применить такой же прием с ресурсами расширения разметки {ThemeResource} в пользовательских шаблонах и стилях, эта возможность будет доступна также для пользовательских элементов управления и их стилей.

## Связанные разделы  
* [Специальные возможности](accessibility.md)
* [Пример контрастности и параметров пользовательского интерфейса](http://go.microsoft.com/fwlink/p/?linkid=231539)
* [Пример XAML accessibility](http://go.microsoft.com/fwlink/p/?linkid=238570)
* [Пример XAML с высокой контрастностью](http://go.microsoft.com/fwlink/p/?linkid=254993)
* [**AccessibilitySettings**](https://msdn.microsoft.com/library/windows/apps/BR242237)



<!--HONumber=Jul16_HO1-->


