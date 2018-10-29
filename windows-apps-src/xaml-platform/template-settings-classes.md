---
author: jwmsft
description: Классы параметров шаблона
title: Классы параметров шаблона
ms.assetid: CAE933C6-EF13-465A-9831-AB003AF23907
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4d7b08138ab22d4cf2cbf4fb5273759f000a7c94
ms.sourcegitcommit: 753e0a7160a88830d9908b446ef0907cc71c64e7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/29/2018
ms.locfileid: "5759387"
---
# <a name="template-settings-classes"></a>Классы параметров шаблона


## <a name="prerequisites"></a>Необходимые условия

Предполагается, что вы умеете добавлять элементы управления в пользовательский интерфейс, задавать их свойства и подключать обработчики событий. Инструкции по добавлению элементов управления в приложение см. в разделе [Добавление элементов управления и обработка событий](https://msdn.microsoft.com/library/windows/apps/mt228345). Предполагается также, что вы знаете, как определить настраиваемый шаблон для элемента управления, сделав копию шаблона по умолчанию и отредактировав ее. Дополнительные сведения об этом см. в разделе [кратком руководстве по шаблонам элементов управления](https://msdn.microsoft.com/library/windows/apps/xaml/hh465374).

## <a name="the-scenario-for-templatesettings-classes"></a>Сценарий для классов **TemplateSettings**

Классы **TemplateSettings** предоставляют набор свойств, которые используются при определении нового шаблона элементов управления для элемента управления. Свойства имеют значения, такие как размеры в пикселях для определенных составных частей пользовательского интерфейса. Значения могут быть вычисленными значениями логики управления, которую обычно легко переопределить или даже получить к ней доступ. Некоторые свойства используются как значения **From** и **To**, которые управляют переходами и анимациями компонентов, и поэтому соответствующие свойства **TemplateSettings** идут парами.

Есть несколько классов **TemplateSettings**. Все они находятся в пространстве имен [**Windows.UI.Xaml.Controls.Primitives**](https://msdn.microsoft.com/library/windows/apps/br209818). Вот список классов и ссылка на свойства **TemplateSettings** соответствующего элемента управления. Это свойство **TemplateSettings** определяет способ получения доступа к значениям **TemplateSettings** для элемента управления и может установить привязки шаблона к его свойствам:

-   [**ComboBoxTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br227752): значение [**ComboBox.TemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br209364)
-   [**GridViewItemTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/hh738499): значение [**GridViewItem.TemplateSettings**](https://msdn.microsoft.com/library/windows/apps/hh738503)
-   [**ListViewItemTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/hh701948): значение [**ListViewItem.TemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br242923)
-   [**ProgressBarTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br227856): значение [**ProgressBar.TemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br227537)
-   [**ProgressRingTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/hh702248): значение [**ProgressRing.TemplateSettings**](https://msdn.microsoft.com/library/windows/apps/hh702581)
-   [**SettingsFlyoutTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/dn298721): значение [**SettingsFlyout.TemplateSettings**](https://msdn.microsoft.com/library/windows/apps/dn252826)
-   [**ToggleSwitchTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br209804): значение [**ToggleSwitch.TemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br209731)
-   [**ToolTipTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br209813): значение [**ToolTip.TemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br227629)

Свойства **TemplateSettings** предназначены для использования в XAML, а не в коде. Это составляющие свойства только для чтения свойства **TemplateSettings** только для чтения родительского элемента управления. В случае сложного сценария пользовательского элемента управления, в котором вы создаете новый класс на основе [**Control**](https://msdn.microsoft.com/library/windows/apps/br209390) и таким образом можете повлиять на логику управления, рекомендуется определить пользовательское свойство **TemplateSettings** для элемента управления, чтобы передавать информацию, которая может быть полезна всем, кто создает шаблон элемента управления повторно. Так как это свойство — значение только для чтения, определите новый класс **TemplateSettings**, связанный с элементом управления, который содержит свойства только для чтения для каждого из информационных элементов, необходимых для измерений шаблона, размещения анимации и т.д., и предоставьте абонентам экземпляр среды выполнения этого класса, инициализированного с помощью логики управления. Классы **TemplateSettings** являются производными от [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/br242356), чтобы свойства могли использовать систему свойств зависимостей для обратных вызовов при изменении свойств. Но идентификаторы свойств зависимостей для свойств не отображаются как общедоступный API, так как свойства **TemplateSettings** должны быть доступны абонентам только для чтения.

## <a name="how-to-use-templatesettings-in-a-control-template"></a>Использование **TemplateSettings** в шаблоне элемента управления

Вот пример запуска шаблонов элементов управления XAML по умолчанию. Это пример шаблона [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) по умолчанию:

```xml
<Ellipse
    x:Name="E1"
    Style="{StaticResource ProgressRingEllipseStyle}"
    Width="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseDiameter}"
    Height="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseDiameter}"
    Margin="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseOffset}"
    Fill="{TemplateBinding Foreground}"/>
```

Полный код XAML для шаблона [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) состоит из сотен строк, так что это всего лишь крошечный отрывок. Этот код XAML определяет часть элемента управления, одну из 6 элементов [**Ellipse**](/uwp/api/Windows.UI.Xaml.Shapes.Ellipse), которая изображает анимацию вращения для неопределенного хода выполнения. Вам как разработчику могут не нравится круги, и вы можете использовать другой графический примитив или другую базовую фигуру для выполнения анимации. Например, вы можете создать компонент **ProgressRing**, который вместо этого использует набор элементов [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle), размещенных в квадрате. В этом случае каждый отдельный компонент **Rectangle** нового шаблона может выглядеть следующим образом:

```xml
<Rectangle
    x:Name="R1"
    Width="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseDiameter}"
    Height="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseDiameter}"
    Margin="{Binding RelativeSource={RelativeSource TemplatedParent}, 
        Path=TemplateSettings.EllipseOffset}"
    Fill="{TemplateBinding Foreground}"/>
```

Польза свойств **TemplateSettings** здесь заключается в том, что это вычисленные значения базовой логики управления [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538). В ходе вычисления разбиваются общие значения [**ActualWidth**](https://msdn.microsoft.com/library/windows/apps/br208709) и [**ActualHeight**](https://msdn.microsoft.com/library/windows/apps/br208707) элемента **ProgressRing** и выделяется вычисленное значение измерения для каждого из элементов движения в своих шаблонах, чтобы части шаблона могли соответствовать содержимому.

Вот другой пример использования в шаблонах элементов управления XAML по умолчанию, в котором показан один из наборов свойств со значениями анимации **From** и **To**. Это из шаблона [**ComboBox**](https://msdn.microsoft.com/library/windows/apps/br209348) по умолчанию:

```xml
<VisualStateGroup x:Name="DropDownStates">
    <VisualState x:Name="Opened">
        <Storyboard>
            <SplitOpenThemeAnimation
               OpenedTargetName="PopupBorder"
               ContentTargetName="ScrollViewer"
               ClosedTargetName="ContentPresenter"
               ContentTranslationOffset="0"
               OffsetFromCenter="{Binding RelativeSource={RelativeSource TemplatedParent}, 
                 Path=TemplateSettings.DropDownOffset}"
               OpenedLength="{Binding RelativeSource={RelativeSource TemplatedParent}, 
                 Path=TemplateSettings.DropDownOpenedHeight}"
               ClosedLength="{Binding RelativeSource={RelativeSource TemplatedParent},
                 Path=TemplateSettings.DropDownClosedHeight}" />
        </Storyboard>
   </VisualState>
...
</VisualStateGroup>
```

Опять же в шаблоне много кодов XAML, поэтому показан только отрывок. И это только одно из нескольких состояний и анимаций тем, в которых используются одинаковые свойства [**ComboBoxTemplateSettings**](https://msdn.microsoft.com/library/windows/apps/br227752). Для [**ComboBox**](https://msdn.microsoft.com/library/windows/apps/br209348) использование значений **ComboBoxTemplateSettings** через привязки обеспечивает то, что соответствующие анимации в шаблоне будут остановлены и запущены в позициях, основой которых являются общие значения, результатом чего будет плавный переход.

**Примечание**  при использовании значения **TemplateSettings** в составе шаблона элемента управления, убедитесь, что задаете свойства, которые соответствуют типу значения. В противном случае вам придется создать преобразователь значений для привязки, чтобы тип целевого объекта привязки можно было преобразовать из другого исходного типа значения **TemplateSettings**. Дополнительные сведения см. в разделе [**IValueConverter**](https://msdn.microsoft.com/library/windows/apps/br209903).

## <a name="related-topics"></a>Ссылки по теме

* [Краткое руководство: шаблоны элементов управления](https://msdn.microsoft.com/library/windows/apps/xaml/hh465374)

