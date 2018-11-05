---
author: stevewhims
Description: Design your app to support the layouts and fonts of multiple languages, including RTL (right-to-left) flow direction.
title: Настройка макета и шрифтов, реализация поддержки написания справа налево
ms.assetid: F2522B07-017D-40F1-B3C8-C4D0DFD03AC3
label: Adjust layout and fonts, and support RTL
template: detail.hbs
ms.author: stwhi
ms.date: 05/11/2018
ms.topic: article
keywords: windows 10, uwp, локализуемость, локализация, справа налево, слева направо
ms.localizationpriority: medium
ms.openlocfilehash: ac69701eca128d327dfd80cfddc7fad41c50c50e
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6036824"
---
# <a name="adjust-layout-and-fonts-and-support-rtl"></a>Настройка макета и шрифтов, реализация поддержки написания справа налево
Проектируйте свое приложение таким образом, чтобы оно поддерживало макеты и шрифты различных языков, в том числе с написанием справа налево. Направление текста — это направление, в котором написан и отображается сценарий, а также направление, в котором элементы пользовательского интерфейса просматриваются на странице взглядом.

## <a name="layout-guidelines"></a>Руководство по макетам
В таких языках, как немецкий и финский, как правило, используется больше символов, чем в английском. Шрифты, использующиеся для дальневосточных языков, как правило, выше обычного. Для таких языков, как арабский и иврит, необходимо, чтобы панели макета и текстовые элементы размещались и читались справа налево.

В связи с этими отличиями в размерах переведенного текста мы рекомендуем не использовать абсолютное позиционирование, фиксированную ширину и фиксированную высоту в пользовательском интерфейсе (UI). Вместо этого используйте преимущества механизмов динамической компоновки макета, которые встроены в элементы пользовательского интерфейса Windows. Например, элементы управления содержимым (такие как кнопки), элементы управления компонентами (например, представления сетки и представления списка) и панели содержимого (например, сетки и элементы StackPanel) по умолчанию автоматически изменяют размер и адаптируются в соответствии с содержимым. Псевдолокализация приложения помогает выявить все проблемные случаи, когда элементы пользовательского интерфейса не совпадают по размерам с содержимым.

Динамический макет является рекомендуемым способом, и вы сможете использовать его в большинстве случаев. Менее предпочтительным, но все же более приемлемым, чем жесткое указание размеров в разметке пользовательского интерфейса, является способ с установкой значений макета в виде функции языка. Вот пример того, как предоставить свойство Width в вашем приложении в качестве ресурса, который локализаторы могут задать соответствующим образом для каждого языка. Сначала в файл ресурсов (.resw) вашего приложения добавьте идентификатор свойства с именем TitleText.Width (вместо TitleText можно использовать любое имя). Затем используйте **x:Uid**, чтобы связать один или несколько элементов UI с идентификатором этого свойства.

```xaml
<TextBlock x:Uid="TitleText">
```

Дополнительные сведения об использовании файлов ресурсов (.resw), идентификаторов свойств и **x:Uid** см. в разделе [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](../../app-resources/localize-strings-ui-manifest.md).

## <a name="fonts"></a>Шрифты
Используйте класс сопоставления шрифтов [**LanguageFont**](/uwp/api/Windows.Globalization.Fonts.LanguageFont?branch=live) для программного доступа к рекомендуемому семейству, размеру, насыщенности и стилю шрифта определенного языка. Класс **LanguageFont** предоставляет доступ к нужной информации о шрифте для различных категорий содержимого, включая заголовки, уведомления, текст и шрифты доступных для редактирования текстов пользовательского интерфейса.

## <a name="mirroring-images"></a>Зеркальное отображение изображений
Если приложение содержит изображения, которые должны быть отражены (то есть, можно просто отразить то же самое изображение) для написания справа налево, можно использовать свойство **FlowDirection**.

```xaml
<!-- en-US\localized.xaml -->
<Image ... FlowDirection="LeftToRight" />

<!-- ar-SA\localized.xaml -->
<Image ... FlowDirection="RightToLeft" />
```

Если вашему приложению требуется другое изображение для правильного отражения, можно использовать систему управления ресурсами с квалификатором `LayoutDirection` (см. раздел LayoutDirection в статье [Адаптация ресурсов с учетом языка, масштаба и других квалификаторов](../../app-resources/tailor-resources-lang-scale-contrast.md#layoutdirection)). Система выбирает изображение с именем `file.layoutdir-rtl.png`, когда в качестве языка среды выполнения приложения (см. раздел [Обзор языков профиля пользователя и языков манифеста приложения](manage-language-and-region.md)) выбран язык с написанием справа налево. Этот подход может применяться, если одна часть изображения зеркально отражается, а другая— нет.

## <a name="handling-right-to-left-rtl-languages"></a>Обработка языков с написанием справа налево (RTL)
Если ваше приложение локализуется для языков с написанием справа налево (RTL), используйте свойство [**FrameworkElement.FlowDirection**](/uwp/api/Windows.UI.Xaml.FrameworkElement.FlowDirection) и задавайте симметричные поля и заполнение. Панели макета, такие как [**Grid**](/uwp/api/Windows.UI.Xaml.Controls.Grid?branch=live), автоматически масштабируются и зеркально отражаются путем задания свойства **FlowDirection**.

Задайте **FlowDirection** для корневой панели макета (или кадра) на странице или для самой страницы. В результате все элементы управления внутри этого элемента унаследуют это свойство.

> [!IMPORTANT]
> Тем не менее, **FlowDirection** *не* задается автоматически на основании выбранного языка интерфейса пользователя в параметрах Windows, а также не изменяется динамически при изменении языка отображения пользователем. Например, если пользователь переключает параметры Windows с английского языка на арабский, то свойство **FlowDirection** *не* выполняет автоматического переключения с написания слева направо на написание справа налево. Как разработчику приложения вам необходимо соответствующим образом задать **FlowDirection** для языка, который сейчас отображается.

Программный способ состоит в использовании свойства `LayoutDirection` выбранного языка интерфейса, чтобы задать свойство [**FlowDirection**](/uwp/api/Windows.UI.Xaml.FrameworkElement.FlowDirection) (см. пример кода ниже). Большинство элементов управления в Windows уже используют **FlowDirection**. Если вы реализуете собственный элемент управления, он должен использовать свойство **FlowDirection** для соответствующего изменения макета для языков с написанием справа налево и слева направо.

```csharp    
this.languageTag = Windows.Globalization.ApplicationLanguages.Languages[0];

// For bidirectional languages, determine flow direction for the root layout panel, and all contained UI.

var flowDirectionSetting = Windows.ApplicationModel.Resources.Core.ResourceContext.GetForCurrentView().QualifierValues["LayoutDirection"];
if (flowDirectionSetting == "LTR")
{
    this.layoutRoot.FlowDirection = Windows.UI.Xaml.FlowDirection.LeftToRight;
}
else
{
    this.layoutRoot.FlowDirection = Windows.UI.Xaml.FlowDirection.RightToLeft;
}
```

```cppwinrt
#include <winrt/Windows.ApplicationModel.Resources.Core.h>
#include <winrt/Windows.Globalization.h>
...

m_languageTag = Windows::Globalization::ApplicationLanguages::Languages().GetAt(0);

// For bidirectional languages, determine flow direction for the root layout panel, and all contained UI.

auto flowDirectionSetting = Windows::ApplicationModel::Resources::Core::ResourceContext::GetForCurrentView().QualifierValues().Lookup(L"LayoutDirection");
if (flowDirectionSetting == L"LTR")
{
    layoutRoot().FlowDirection(Windows::UI::Xaml::FlowDirection::LeftToRight);
}
else
{
    layoutRoot().FlowDirection(Windows::UI::Xaml::FlowDirection::RightToLeft);
}
```

```cpp
this->languageTag = Windows::Globalization::ApplicationLanguages::Languages->GetAt(0);

// For bidirectional languages, determine flow direction for the root layout panel, and all contained UI.

auto flowDirectionSetting = Windows::ApplicationModel::Resources::Core::ResourceContext::GetForCurrentView()->QualifierValues->Lookup("LayoutDirection");
if (flowDirectionSetting == "LTR")
{
    this->layoutRoot->FlowDirection = Windows::UI::Xaml::FlowDirection::LeftToRight;
}
else
{
    this->layoutRoot->FlowDirection = Windows::UI::Xaml::FlowDirection::RightToLeft;
}
```

Этот способ делает **FlowDirection** функцией свойства `LayoutDirection` выбранного пользователем языка интерфейса. Если по какой-либо причине вы не хотите использовать эту логику, можно предоставить свойство FlowDirection в вашем приложении в качестве ресурса, который локализаторы могут задать соответствующим образом для каждого языка, на который они переводят.

Сначала в файл ресурсов (.resw) вашего приложения добавьте идентификатор свойства с именем "MainPage.FlowDirection" (вместо "MainPage" можно использовать любое имя). Затем используйте **x:Uid**, чтобы связать главный элемент **Page** (или его корневую панель макета, или кадр) с идентификатором этого свойства.

```xaml
<Page x:Uid="MainPage">
```

Вместо одной строки кода для всех языков этот вариант зависит от переводчика, который должен надлежащим образом «перевести» этот ресурс свойства для каждого языка, поэтому имейте в виду, что при использовании этой методики возникают дополнительные возможности для человеческой ошибки.

## <a name="important-apis"></a>Важные API
* [FrameworkElement.FlowDirection](/uwp/api/Windows.UI.Xaml.FrameworkElement.FlowDirection)
* [LanguageFont](/uwp/api/Windows.Globalization.Fonts.LanguageFont?branch=live)

## <a name="related-topics"></a>Смежные разделы
* [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](../../app-resources/localize-strings-ui-manifest.md)
* [Адаптация ресурсов с учетом языка, масштаба и других квалификаторов](../../app-resources/tailor-resources-lang-scale-contrast.md)
* [Обзор языков профиля пользователя и языков манифеста приложения](manage-language-and-region.md)