---
description: Описание действий, необходимых для того, чтобы приложение Windows можно было использовать при активной теме с высокой контрастностью.
ms.assetid: FD7CA6F6-A8F1-47D8-AA6C-3F2EC3168C45
title: Темы с высокой контрастностью
template: detail.hbs
ms.date: 09/28/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: ce3fe9ea96f4b4ce2f541fb5f7a9682a0dee5e0e
ms.sourcegitcommit: 87fd0ec1e706a460832b67f936a3014f0877a88c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83234029"
---
# <a name="high-contrast-themes"></a>Высококонтрастные темы  

Windows поддерживает темы с высокой контрастностью для операционной системы и приложений, которые пользователи могут включить. В темах с высокой контрастностью используется небольшая палитра контрастных цветов, благодаря чему интерфейс хорошо видно.

![Калькулятор в светлой теме и в черной теме с высокой контрастностью.](images/high-contrast-calculators.png)

*Калькулятор в светлой теме и в черной теме с высокой контрастностью.*

Для переключения на тему с высокой контрастностью последовательно щелкните *Параметры > Специальные возможности > Высокая контрастность*.

> [!NOTE]
> Не путайте темы с высокой контрастностью со светлыми и темными темами. Последние представлены в гораздо более разнообразной цветовой палитре, которая не считается палитрой с высокой контрастностью. Более подробные сведения о светлых и темных темах см. в статье о [цвете](../style/color.md).

Стандартные элементы управления предоставляются с полной бесплатной поддержкой высокой контрастности, а вот при пользовательской настройке интерфейса следует проявлять осторожность. Самая распространенная ошибка в области высокой контрастности вызвана жестким программированием цвета в элементе управления.

```xaml
<!-- Don't do this! -->
<Grid Background="#E6E6E6">

<!-- Instead, create BrandedPageBackgroundBrush and do this. -->
<Grid Background="{ThemeResource BrandedPageBackgroundBrush}">
```

Если цвет `#E6E6E6` задан в самом элементе (см. первый пример), этот цвет фона будет использоваться во всех темах сетки. Если пользователь переключается на черную тему с высокой контрастностью, ожидается, что у приложения будет черный фон. Поскольку `#E6E6E6` почти белый, некоторые пользователи не смогут взаимодействовать с вашим приложением.

Во втором примере [**расширение разметки {ThemeResource}**](../../xaml-platform/themeresource-markup-extension.md) используется для ссылки на цвет в коллекции [**ThemeDictionaries**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.resourcedictionary.themedictionaries), которая представляет собой выделенное свойство элемента [**ResourceDictionary**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.ResourceDictionary). **Семедиктионариес** позволяет XAML автоматически менять цвета на основе текущей темы пользователя.

## <a name="theme-dictionaries"></a>Словари тем

Если необходимо изменить цвет, заданный в системе по умолчанию, создайте для приложения коллекцию ThemeDictionaries.

1. Начните с создания надлежащих элементов подключения, если они еще не созданы. В App. XAML создайте коллекцию **семедиктионариес** , включая по крайней мере **по умолчанию** и **HighContrast** .
2. В **по умолчанию**создайте необходимый тип [кисти](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Brush) , обычно это **SolidColorBrush**. Задайте ему имя *x:Key*, указывающее его предназначение.
3. Назначьте ему требуемое значение цвета **Color**.
4. Скопируйте этот инструмент **Brush** в **HighContrast**.

``` xaml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <!-- Default is a fallback if a more precise theme isn't called
            out below -->
            <ResourceDictionary x:Key="Default">
                <SolidColorBrush x:Key="BrandedPageBackgroundBrush" Color="#E6E6E6" />
            </ResourceDictionary>

            <!-- Optional, Light is used in light theme.
            If included, Default will be used for Dark theme -->
            <ResourceDictionary x:Key="Light">
                <SolidColorBrush x:Key="BrandedPageBackgroundBrush" Color="#E6E6E6" />
            </ResourceDictionary>

            <!-- HighContrast is used in all high contrast themes -->
            <ResourceDictionary x:Key="HighContrast">
                <SolidColorBrush x:Key="BrandedPageBackgroundBrush" Color="#E6E6E6" />
            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

Наконец, нужно определить, какой цвет использовать в режиме высокой контрастности (см. следующий раздел).

> [!NOTE]
> **HighContrast** не является единственным доступным именем ключа. Есть также **хигхконтрастблакк**, **хигхконтраствхите**и **хигхконтрасткустом**. В большинстве случаев **HighContrast** будет достаточно.

## <a name="high-contrast-colors"></a>Цвета с высокой контрастностью

На странице *Параметры > Специальные возможности > Высокая контрастность* по умолчанию доступны 4 темы с высокой контрастностью. 


![Параметры высокой контрастности](images/high-contrast-settings.png)  

*После того как пользователь выберет один из параметров, на странице отображается образец для предварительного просмотра.*  

![Высококонтрастные ресурсы](images/high-contrast-resources.png)  

*Можно щелкнуть каждый цветовой образец в предварительной версии, чтобы изменить его значение. Каждый образец также напрямую сопоставлен с ресурсом цвета XAML.*  

Каждый ресурс **SystemColor*Color** представляет собой переменную, которая автоматически обновляет цвет, когда пользователь меняет высококонтрастные темы. Ниже приведены рекомендации о том, где и когда использовать каждый ресурс.

Ресурс | Использование |
|--------|-------|
**SystemColorWindowTextColor** | Основной текст, заголовки, списки; любой текст, с которым невозможно осуществлять взаимодействие |
| **SystemColorHotlightColor** | Гиперссылки |
| **SystemColorGrayTextColor** | Отключенный пользовательский интерфейс |
| **SystemColorHighlightTextColor** | Основной цвет текста или элементов пользовательского интерфейса, которые в настоящее время выполняются, выбраны или участвуют во взаимодействии |
| **SystemColorHighlightColor** | Фоновый цвет текста или элементов пользовательского интерфейса, которые в настоящее время выполняются, выбраны или участвуют во взаимодействии |
| **SystemColorButtonTextColor** | Основной цвет кнопок и любых элементов пользовательского интерфейса, с которыми можно осуществлять взаимодействие |
| **SystemColorButtonFaceColor** | Фоновый цвет кнопок и любых элементов пользовательского интерфейса, с которыми можно осуществлять взаимодействие |
| **SystemColorWindowColor** | Фон страниц, областей, всплывающих окон и панелей |

Часто полезно взглянуть на существующие приложения, меню «Пуск» и стандартные элементы управления, чтобы понять, как другие разработчики решают аналогичные задачи по созданию высококонтрастного интерфейса.

**Рекомендуется**

* По возможности сохранять пары основных и фоновых цветов.
* Протестировать работающее приложение во всех 4 темах с высокой контрастностью. После переключения тем не должно возникать необходимости в перезапуске приложения.
* Соблюдать единообразие.

**Не рекомендуется**

* Выполнять жесткое кодирование цвета в теме **HighContrast**; используйте ресурсы **SystemColor*Color**.
* Выберите ресурс цвета, исходя из эстетических предпочтений. Помните, что цвета меняются с темой!
* Не используйте **SystemColorGrayTextColor** для основного текста, который является дополнительным или выступает в качестве подсказки.


Чтобы продолжить предыдущий пример, необходимо выбрать ресурс для **BrandedPageBackgroundBrush**. Поскольку имя указывает, что он будет использован для фона, имеет смысл выбрать **SystemColorWindowColor**.

``` xaml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.ThemeDictionaries>
            <!-- Default is a fallback if a more precise theme isn't called
            out below -->
            <ResourceDictionary x:Key="Default">
                <SolidColorBrush x:Key="BrandedPageBackgroundBrush" Color="#E6E6E6" />
            </ResourceDictionary>

            <!-- Optional, Light is used in light theme.
            If included, Default will be used for Dark theme -->
            <ResourceDictionary x:Key="Light">
                <SolidColorBrush x:Key="BrandedPageBackgroundBrush" Color="#E6E6E6" />
            </ResourceDictionary>

            <!-- HighContrast is used in all high contrast themes -->
            <ResourceDictionary x:Key="HighContrast">
                <SolidColorBrush x:Key="BrandedPageBackgroundBrush" Color="{ThemeResource SystemColorWindowColor}" />
            </ResourceDictionary>
        </ResourceDictionary.ThemeDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

Теперь можно задать фон в приложении.

```xaml
<Grid Background="{ThemeResource BrandedPageBackgroundBrush}">
```

Обратите внимание, что ** \{ семересаурце \} ** используется дважды, один раз для ссылки на **системколорвиндовколор** и снова для ссылки на **брандедпажебаккграундбруш**. Оба необходимы для правильного использования темы в приложении во время выполнения. Это хорошая возможность протестировать эту функциональность в вашем приложении. Фон сетки автоматически обновляется по мере переключения на высококонтрастную тему. Фон также обновляется при переключении между разными высококонтрастными темами.

## <a name="when-to-use-borders"></a>Когда следует использовать границы

**SystemColorWindowColor** следует использовать для фона страниц, областей, всплывающих окон и панелей в режиме высокой контрастности. Чтобы сохранить важные границы в пользовательском интерфейсе, при необходимости можно добавить границу высокой контрастности.

![Область навигации отделена от других частей страницы](images/high-contrast-actions-content.png)  

*В области навигации и на странице один и тот же цвет фона имеет высокий контраст. Для разделения этих полей важно иметь границу с высокой контрастностью.*


## <a name="list-items"></a>Элементы списка

В режиме высокой контрастности для элементов в [ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview) при наведении, нажатии или выборе используется фон **SystemColorHighlightColor**. Сложные элементы списка содержат ошибку, когда содержимое элемента списка не меняет цвет при наведении на элемент, нажатии или выборе элемента. В результате прочитать элемент невозможно.

![Простой список в светлой теме и черной теме высокой контрастности](images/high-contrast-list1.png)

*Простой список в светлой теме (слева) и высокая контрастность Черная тема (справа). Второй элемент выбран; Обратите внимание, что цвет текста инвертирован при высокой контрастности.*


### <a name="list-items-with-colored-text"></a>Элементы списка с цветным текстом

Одно из затруднений — настройка параметра TextBlock.Foreground в элементе [DataTemplate](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemtemplate) представления списка. Это обычно делается, чтобы установить визуальную иерархию. Свойство Foreground настраивается в элементе [ListViewItem](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listviewitem), а элемент TextBlocks в шаблоне DataTemplate наследует правильный цвет фона при наведении на элемент, нажатии или выборе элемента. Однако при настройке параметра Foreground наследование нарушается.

![Сложный список в светлой теме и черной теме высокой контрастности](images/high-contrast-list2.png)

*Сложный список в светлой теме (слева) и высокая контрастность «Черная тема» (справа). В высоком контрасте не удалось обратить вторую строку выбранного элемента.*  

Чтобы решить эту проблему, можно условно настроить параметр Foreground в элементе Style, который находится в коллекции **ThemeDictionaries**. Поскольку параметр **Foreground** не настраивается элементом **SecondaryBodyTextBlockStyle** из коллекции **HighContrast**, его цвет будет меняться правильно.

```xaml
<!-- In App.xaml... -->
<ResourceDictionary.ThemeDictionaries>
    <ResourceDictionary x:Key="Default">
        <Style
            x:Key="SecondaryBodyTextBlockStyle"
            TargetType="TextBlock"
            BasedOn="{StaticResource BodyTextBlockStyle}">
            <Setter Property="Foreground" Value="{StaticResource SystemControlForegroundBaseMediumBrush}" />
        </Style>
    </ResourceDictionary>

    <ResourceDictionary x:Key="Light">
        <Style
            x:Key="SecondaryBodyTextBlockStyle"
            TargetType="TextBlock"
            BasedOn="{StaticResource BodyTextBlockStyle}">
            <Setter Property="Foreground" Value="{StaticResource SystemControlForegroundBaseMediumBrush}" />
        </Style>
    </ResourceDictionary>

    <ResourceDictionary x:Key="HighContrast">
        <!-- The Foreground Setter is omitted in HighContrast -->
        <Style
            x:Key="SecondaryBodyTextBlockStyle"
            TargetType="TextBlock"
            BasedOn="{StaticResource BodyTextBlockStyle}" />
    </ResourceDictionary>
</ResourceDictionary.ThemeDictionaries>

<!-- Usage in your DataTemplate... -->
<DataTemplate>
    <StackPanel>
        <TextBlock Style="{StaticResource BodyTextBlockStyle}" Text="Double line list item" />

        <!-- Note how ThemeResource is used to reference the Style -->
        <TextBlock Style="{ThemeResource SecondaryBodyTextBlockStyle}" Text="Second line of text" />
    </StackPanel>
</DataTemplate>
```


## <a name="detecting-high-contrast"></a>Обнаружение высокой контрастности

Можно программным способом проверить, является ли текущая тема темой с высокой контрастностью, воспользовавшись элементами класса [**AccessibilitySettings**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.AccessibilitySettings).

> [!NOTE]
> Убедитесь, что конструктор **AccessibilitySettings** вызывается из области, в которой приложение инициализировано и уже отображает содержимое.

## <a name="related-topics"></a>Связанные темы  
* [Специальные возможности](accessibility.md)
* [Пример контрастности и параметров пользовательского интерфейса](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/XAML%20high%20contrast%20style%20sample%20(Windows%208))
* [Пример специальных возможностей XAML](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/XAML%20accessibility%20sample)
* [Пример XAML с высокой контрастностью](https://github.com/microsoftarchive/msdn-code-gallery-microsoft/tree/411c271e537727d737a53fa2cbe99eaecac00cc0/Official%20Windows%20Platform%20Sample/Windows%208%20app%20samples/%5BC%23%5D-Windows%208%20app%20samples/C%23/Windows%208%20app%20samples/XAML%20high%20contrast%20style%20sample%20(Windows%208))
* [**AccessibilitySettings**](https://docs.microsoft.com/uwp/api/Windows.UI.ViewManagement.AccessibilitySettings)
