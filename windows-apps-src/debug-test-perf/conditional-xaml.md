---
title: Условный XAML
description: Используйте новые API в разметке XAML, сохраняя совместимость с предыдущими версиями
ms.date: 10/10/2017
ms.topic: article
keywords: Windows10, UWP
ms.localizationpriority: medium
ms.openlocfilehash: 3c75a6c487fe4a7f7cb56deff869b36309a4b9c7
ms.sourcegitcommit: d7613c791107f74b6a3dc12a372d9de916c0454b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2018
ms.locfileid: "8744608"
---
# <a name="conditional-xaml"></a>Условный XAML

*Условный XAML* обеспечивает возможность использовать метод [ApiInformation.IsApiContractPresent](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.isapicontractpresent) в разметке XAML. Благодаря этому можно задавать свойства и создавать экземпляры объектов в разметке в зависимости от наличия API, а необходимость использования кода программной части отсутствует. Условный XAML выполняет выборочный синтаксический разбор элементов или атрибутов, чтобы определить, будут ли они доступны во время выполнения. Условные операторы оцениваются во время выполнения, и если в результате оценки получено значение **true**, выполняется синтаксический разбор элементов с тегом "Условный XAML"; в противном случае эти элементы игнорируются.

Условный XAML доступен, начиная с обновления Creators Update (версия 1703, сборка 15063). Использовать условный XAML можно, если в качестве минимальной версии проекта Visual Studio указана сборка 15063 (Creators Update) или выше, а в качестве целевой версии— версия выше минимальной. См. дополнительные сведения о настройке вашего проекта Visual Studio в разделе [Адаптивные к версии приложения](version-adaptive-apps.md).

> [!NOTE]
> Чтобы создать адаптивное к версии приложение с минимальной версией ниже сборки 15063, воспользуйтесь [адаптивным к версии кодом](version-adaptive-code.md), а не XAML.

Важные справочные сведения об ApiInformation и контрактах API доступны в разделе [Адаптивные к версии приложения](version-adaptive-apps.md).

## <a name="conditional-namespaces"></a>Условные пространства имен

Чтобы использовать условный метод в XAML, необходимо сначала объявить [условное пространство имен XAML](../xaml-platform/xaml-namespaces-and-namespace-mapping.md) вверху страницы. Ниже приводится пример псевдокода для условного пространства имен:

```xaml
xmlns:myNamespace="schema?conditionalMethod(parameter)"
```

Условное пространство имен можно разделить на две части с помощью разделителя "?". 

- Содержимое до разделителя обозначает пространство имен или схему, которая содержит указанный API. 
- Содержимое после разделителя "?" обозначает условный метод, который определяет результат оценки условного пространства имен: **true** или **false**.

В большинстве случаев в качестве схемы используется пространство имен XAML по умолчанию:

```xaml
xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
```

Условный XAML поддерживает следующие условные методы:

Метод | Inverse
------ | -------
IsApiContractPresent(ContractName, VersionNumber) | IsApiContractNotPresent(ContractName, VersionNumber)
IsTypePresent(ControlType) | IsTypeNotPresent(ControlType)
IsPropertyPresent(ControlType, PropertyName) | IsPropertyNotPresent(ControlType, PropertyName)

Мы рассмотрим эти методы позже, в следующих разделах этой статьи.

> [!NOTE]
> Рекомендуется использовать методы IsApiContractPresent и IsApiContractNotPresent. Другие условные методы не полностью поддерживаются в среде разработки Visual Studio.

## <a name="create-a-namespace-and-set-a-property"></a>Создание пространства имен и задание свойства

В этом примере необходимо отобразить текст «Здравствуйте, "условный XAML"» в качестве содержимого текстового блока, если приложение выполняется в обновлении Fall Creators Update или более поздней версии, и по умолчанию не отображать никакого содержимого, если приложение выполняется в предыдущей версии операционной системы.

Во-первых, определите пользовательское пространство имен с префиксом 'contract5Present' и используйте пространство имен XAML по умолчанию (http://schemas.microsoft.com/winfx/2006/xaml/presentation) в качестве схемы со свойством [TextBlock.Text](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock.Text). Чтобы сделать это пространство имен условным, добавьте разделитель "?" после схемы.

Затем определите условный оператор, возвращающий значение **true** на устройствах с обновлением Fall Creators Update или выше. Используйте метод ApiInformation **IsApiContractPresent** для проверки наличия пятой версии UniversalApiContract. Версия 5 UniversalApiContract была выпущена вместе с обновлением Fall Creators Update (пакет SDK 16299).

```xaml
xmlns:contract5Present="http://schemas.microsoft.com/winfx/2006/xaml/presentation?IsApiContractPresent(Windows.Foundation.UniversalApiContract,5)"
```

Определив пространство имен, добавьте префикс пространства имен перед свойством Text элемента TextBox, чтобы указать, что это свойство необходимо задать условно во время выполнения.

```xaml
<TextBlock contract5Present:Text="Hello, Conditional XAML"/>
```

Ниже приводится полный код XAML.

```xaml
<Page
    x:Class="ConditionalTest.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:contract5Present="http://schemas.microsoft.com/winfx/2006/xaml/presentation?IsApiContractPresent(Windows.Foundation.UniversalApiContract,5)">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <TextBlock contract5Present:Text="Hello, Conditional XAML"/>
    </Grid>
</Page>
```

При выполнении этого примера в обновлении Fall Creators Update отображается текст «Здравствуйте, "условный XAML"»; если пример выполняется в обновлении Creators Update, никакой текст не отображается.

Условный XAML позволяет выполнять в разметке проверки API, доступные в коде. Ниже приводится эквивалентный код для этой проверки.

```csharp
if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 5))
{
    textBlock.Text = "Hello, Conditional XAML";
}
```

Обратите внимание, что несмотря на то что метод IsApiContractPresent принимает строку для параметра *contractName* в объявлении пространства имен XAML кавычки вокруг (" ") не используются.

## <a name="use-ifelse-conditions"></a>Использование условий if/else

В предыдущем примере свойство Text задается, только если приложение выполняется в Fall Creators Update. Но что делать, если нужно отобразить другой текст при выполнении приложения в обновлении Creators Update? Можно попробовать задать свойство Text без условного квалификатора, например как показано ниже.

```xaml
<!-- DO NOT USE -->
<TextBlock Text="Hello, World" contract5Present:Text="Hello, Conditional XAML"/>
```

Это сработает, если свойство выполняется в обновлении Creators Update, однако если оно выполняется в Fall Creators Update, отобразится ошибка с сообщением, что свойство Text задано несколько раз.

Чтобы задать другой текст для отображения, когда приложение выполняется в других версиях Windows 10, потребуется другое условие. Условный XAML предоставляет метод, обратный каждому поддерживаемому методу ApiInformation, чтобы можно было создавать условные сценарии if/else, подобные этому.

Метод IsApiContractPresent возвращает значение **true**, если на текущем устройстве доступны указанные контракт и номер версии. Допустим, приложение выполняется в Creators Update с четвертой версией универсального контракта API.

Разные вызовы ApiContractPresent дадут следующие результаты:

- IsApiContractPresent(Windows.Foundation.UniversalApiContract, 5) = **false**
- IsApiContractPresent(Windows.Foundation.UniversalApiContract, 4) = true
- IsApiContractPresent(Windows.Foundation.UniversalApiContract, 3) = true
- IsApiContractPresent(Windows.Foundation.UniversalApiContract, 2) = true
- IsApiContractPresent(Windows.Foundation.UniversalApiContract, 1) = true.

IsApiContractNotPresent возвращает значение, обратное значению IsApiContractPresent. Вызовы IsApiContractNotPresent дадут следующие результаты:

- IsApiContractNotPresent(Windows.Foundation.UniversalApiContract, 5) = **true**
- IsApiContractNotPresent(Windows.Foundation.UniversalApiContract, 4) = false
- IsApiContractNotPresent(Windows.Foundation.UniversalApiContract, 3) = false
- IsApiContractNotPresent(Windows.Foundation.UniversalApiContract, 2) = false
- IsApiContractNotPresent(Windows.Foundation.UniversalApiContract, 1) = false

Чтобы использовать обратное условие, необходимо создать второе условное пространство имен XAML, использующее условный оператор **IsApiContractNotPresent**. В этом примере он имеет префикс 'contract5NotPresent'.

```xaml
xmlns:contract5NotPresent="http://schemas.microsoft.com/winfx/2006/xaml/presentation?IsApiContractNotPresent(Windows.Foundation.UniversalApiContract,5)"

xmlns:contract5Present="http://schemas.microsoft.com/winfx/2006/xaml/presentation?IsApiContractPresent(Windows.Foundation.UniversalApiContract,5)"
```

Определив оба пространства имен, можно дважды задать свойство Text при условии, что в качестве префикса к ним будут добавлены квалификаторы, не допускающие одновременного использования обеих настроек свойства. Например:

```xaml
<TextBlock contract5NotPresent:Text="Hello, World"
           contract5Present:Text="Hello, Fall Creators Update"/>
```

Ниже приводится еще один пример, в котором настраивается фон кнопки. Опция [Акриловый материал](../design/style/acrylic.md) доступна, начиная с Fall Creators Update, поэтому вы будете использовать акриловый фон, когда приложение выполняется в Fall Creators Update. В более ранних версиях эта опция недоступна, поэтому для этих случаев вы задаете красный фон.

```xaml
<Button Content="Button"
        contract5NotPresent:Background="Red"
        contract5Present:Background="{ThemeResource SystemControlAcrylicElementBrush}"/>
```

## <a name="create-controls-and-bind-properties"></a>Создание элементов управления и привязка свойств

До этого момента мы рассматривали настройку свойств с использованием условного XAML, однако можно также условно создавать экземпляры элементов управления на основе доступного во время выполнения контракта API.

В этом примере создается экземпляр элемента управления [ColorPicker](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.colorpicker), если приложение выполняется в Fall Creators Update, где доступен этот элемент управления. Элемент управления ColorPicker недоступен в версиях ниже Fall Creators Update, поэтому если приложение выполняется в более ранних версиях, для предоставления пользователю возможности выбрать цвет (в упрощенном варианте) используется элемент управления [ComboBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.combobox).

```xaml
<contract5Present:ColorPicker x:Name="colorPicker"
                              Grid.Column="1"
                              VerticalAlignment="Center"/>

<contract5NotPresent:ComboBox x:Name="colorComboBox"
                              PlaceholderText="Pick a color"
                              Grid.Column="1"
                              VerticalAlignment="Center">
```

Условные квалификаторы можно использовать с разными формами [синтаксиса свойств XAML](../xaml-platform/xaml-syntax-guide.md). В этом примере свойство Fill прямоугольника задается с использованием синтаксиса элементов свойств для Fall Creators Update и синтаксиса атрибутов для предыдущих версий.

```xaml
<Rectangle x:Name="colorRectangle" Width="200" Height="200"
           contract5NotPresent:Fill="{x:Bind ((SolidColorBrush)((FrameworkElement)colorComboBox.SelectedItem).Tag), Mode=OneWay}">
    <contract5Present:Rectangle.Fill>
        <SolidColorBrush contract5Present:Color="{x:Bind colorPicker.Color, Mode=OneWay}"/>
    </contract5Present:Rectangle.Fill>
</Rectangle>
```

При привязке свойства к другому свойству, которое зависит от условного пространства имен, необходимо использовать одно и то же условие для обоих свойств. В этом примере `colorPicker.Color` зависит от условного пространства имен 'contract5Present', поэтому необходимо также добавить префикс 'contract5Present' к свойству SolidColorBrush.Color. (Либо можно добавить префикс 'contract5Present' к свойству SolidColorBrush, а не Color.) В противном случае отобразится ошибка времени компиляции.

```xaml
<SolidColorBrush contract5Present:Color="{x:Bind colorPicker.Color, Mode=OneWay}"/>
```

Ниже приводится полный код XAML, демонстрирующий эти сценарии. В этом примере содержится прямоугольник и пользовательский интерфейс, позволяющий задать цвет прямоугольника.

Если приложение выполняется в Fall Creators Update, используйте элемент управления ColorPicker, чтобы предоставить пользователю возможность выбрать цвет. Элемент управления ColorPicker недоступен в версиях ниже Fall Creators Update, поэтому если приложение выполняется в более ранних версиях, для предоставления пользователю возможности выбрать цвет (в упрощенном варианте) используется элемент управления ComboBox.

```xaml
<Page
    x:Class="ConditionalTest.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:contract5Present="http://schemas.microsoft.com/winfx/2006/xaml/presentation?IsApiContractPresent(Windows.Foundation.UniversalApiContract,5)"
    xmlns:contract5NotPresent="http://schemas.microsoft.com/winfx/2006/xaml/presentation?IsApiContractNotPresent(Windows.Foundation.UniversalApiContract,5)">

    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>

        <Rectangle x:Name="colorRectangle" Width="200" Height="200"
                   contract5NotPresent:Fill="{x:Bind ((SolidColorBrush)((FrameworkElement)colorComboBox.SelectedItem).Tag), Mode=OneWay}">
            <contract5Present:Rectangle.Fill>
                <SolidColorBrush contract5Present:Color="{x:Bind colorPicker.Color, Mode=OneWay}"/>
            </contract5Present:Rectangle.Fill>
        </Rectangle>

        <contract5Present:ColorPicker x:Name="colorPicker"
                                      Grid.Column="1"
                                      VerticalAlignment="Center"/>

        <contract5NotPresent:ComboBox x:Name="colorComboBox"
                                      PlaceholderText="Pick a color"
                                      Grid.Column="1"
                                      VerticalAlignment="Center">
            <ComboBoxItem>Red
                <ComboBoxItem.Tag>
                    <SolidColorBrush Color="Red"/>
                </ComboBoxItem.Tag>
            </ComboBoxItem>
            <ComboBoxItem>Blue
                <ComboBoxItem.Tag>
                    <SolidColorBrush Color="Blue"/>
                </ComboBoxItem.Tag>
            </ComboBoxItem>
            <ComboBoxItem>Green
                <ComboBoxItem.Tag>
                    <SolidColorBrush Color="Green"/>
                </ComboBoxItem.Tag>
            </ComboBoxItem>
        </contract5NotPresent:ComboBox>
    </Grid>
</Page>
```

## <a name="related-articles"></a>Связанные статьи

- [Руководство по приложениям UWP](https://msdn.microsoft.com/windows/uwp/get-started/universal-application-platform-guide)
- [Динамическое обнаружение компонентов с контрактами API](https://blogs.windows.com/buildingapps/2015/09/15/dynamically-detecting-features-with-api-contracts-10-by-10/)
- [Контракты API](https://channel9.msdn.com/Events/Build/2015/3-733) (видео с конференции Build 2015)