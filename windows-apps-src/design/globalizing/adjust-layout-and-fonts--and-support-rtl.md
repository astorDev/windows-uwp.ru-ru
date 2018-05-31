---
author: stevewhims
Description: Design your app to support the layouts and fonts of multiple languages, including RTL (right-to-left) flow direction.
title: Настройка макета и шрифтов, реализация поддержки написания справа налево
ms.assetid: F2522B07-017D-40F1-B3C8-C4D0DFD03AC3
label: Adjust layout and fonts, and support RTL
template: detail.hbs
ms.author: stwhi
ms.date: 11/09/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, локализуемость, локализация, справа налево, слева направо
ms.localizationpriority: medium
ms.openlocfilehash: cf3a2d781dc916fbda9a9d6386dee4e2e6144873
ms.sourcegitcommit: 2470c6596d67e1f5ca26b44fad56a2f89773e9cc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/22/2018
ms.locfileid: "1673981"
---
# <a name="adjust-layout-and-fonts-and-support-rtl"></a>Настройка макета и шрифтов, реализация поддержки написания справа налево

Проектируйте свое приложение таким образом, чтобы оно поддерживало макеты и шрифты различных языков, в том числе с написанием справа налево. Направление текста — это направление, в котором написан и отображается сценарий, а также направление, в котором элементы пользовательского интерфейса просматриваются на странице взглядом.

## <a name="layout-guidelines"></a>Руководство по макетам

В таких языках, как немецкий и финский, как правило, используется больше символов, чем в английском. Шрифты, использующиеся для дальневосточных языков, как правило, выше обычного. Для таких языков, как арабский и иврит, необходимо, чтобы панели макета и текстовые элементы размещались и читались справа налево.

Из-за разной длины переведенного текста следует использовать динамические механизмы макета пользовательского интерфейса вместо абсолютного размещения, фиксированной ширины или высоты. Псевдолокализация приложения помогает выявить все проблемные случаи, когда элементы пользовательского интерфейса не совпадают по размерам с содержимым надлежащим образом.

Для языков с написанием справа налево (RTL) используйте свойство [**FrameworkElement.FlowDirection**](/uwp/api/Windows.UI.Xaml.FrameworkElement.FlowDirection) и задавайте симметричные поля и заполнение. Панели макета, такие как [**Grid**](/uwp/api/Windows.UI.Xaml.Controls.Grid?branch=live) автоматически масштабируются и зеркально отражаются путем задания свойства **FlowDirection**.

Вот пример того, как предоставить свойство FlowDirection в вашем приложении в качестве ресурса, который локализаторы могут задать соответствующим образом.

Сначала в файл ресурсов (.resw) вашего приложения добавьте идентификатор свойства с именем "MainPage.FlowDirection" (вместо "MainPage" можно использовать любое имя).

Затем используйте **x: Uid**, чтобы связать главный элемент **Page** с идентификатором этого свойства.

```xaml
<Page x:Uid="MainPage">
```

Дополнительные сведения об использовании файлов ресурсов (.resw), идентификаторов свойств и **x:Uid** см. в разделе [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](../../app-resources/localize-strings-ui-manifest.md).

Не следует задавать абсолютные значения макета для любого элемента пользовательского интерфейса на основе языка. Однако, если это абсолютно неизбежно, затем можно создать идентификатор свойства в виде "TitleText.Width".

```xaml
<TextBlock x:Uid="TitleText">
```

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

## <a name="best-practices-for-handling-right-to-left-rtl-languages"></a>Рекомендации по работе с языками, которые используют написание справа налево (RTL)

Если приложение локализуется для языков с написанием справа налево (RTL), используйте API, чтобы задать направление текста по умолчанию для корневой панели макета элемента "Page". В результате все элементы управления, содержащиеся в этой корневой панели, будут соответствующим образом реагировать на направление текста по умолчанию. Если поддерживается несколько языков, используйте `LayoutDirection` для первого языка среды выполнения приложения, чтобы задать значение свойства [**FlowDirection**](/uwp/api/Windows.UI.Xaml.FrameworkElement.FlowDirection) (см. пример кода ниже). Большинство элементов управления в Windows уже используют **FlowDirection**. Если вы реализуете собственные элементы управления, они должны использовать свойство **FlowDirection** для соответствующего изменения макета для языков с написанием справа налево и слева направо.

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

### <a name="rtl-faq"></a>ВОПРОСЫ И ОТВЕТЫ О ЯЗЫКАХ С НАПИСАНИЕМ СПРАВА НАЛЕВО 

**Вопрос.** Задается ли свойство **FlowDirection** автоматически в зависимости от текущего языка? Например, если выбрать английский язык, будет ли текст отображаться слева направо, а если выбрать арабский, будет ли текст отображаться справа налево?

> **Ответ.** Свойство **FlowDirection** не учитывает язык. Вы задали **FlowDirection** для языка, который сейчас отображается. См. пример кода выше.

**Вопрос.** Я не знаком с локализацией. Ресурсы уже содержат направление текста? Можно ли определить направление текста по текущему языку?

> **Ответ.** Если вы используете текущие рекомендации, ресурсы не содержат непосредственно направление текста. Вам необходимо определить направление текста для текущего языка. Это можно сделать двумя способами.
> 
> Рекомендуется использовать **LayoutDirection** для первого предпочитаемого языка в списке, чтобы установить свойство **FlowDirection** объекта RootFrame. Все элементы управления в RootFrame наследуют свойство FlowDirection от RootFrame.
> 
> Другой способ — задать FlowDirection в RESW-файле для нужных языков с написанием справа налево. Например, у вас может быть RESW-файл для арабского языка и RESW-файл для иврита. В этих файлах можно использовать атрибут x:UID для установки FlowDirection. Этот метод более восприимчив к ошибкам, чем программный метод.

## <a name="important-apis"></a>Важные API

* [FrameworkElement.FlowDirection](/uwp/api/Windows.UI.Xaml.FrameworkElement.FlowDirection)
* [LanguageFont](/uwp/api/Windows.Globalization.Fonts.LanguageFont?branch=live)

## <a name="related-topics"></a>Смежные разделы

* [Локализация строк в манифесте пакета приложения и интерфейсе пользователя](../../app-resources/localize-strings-ui-manifest.md)
* [Адаптация ресурсов с учетом языка, масштаба и других квалификаторов](../../app-resources/tailor-resources-lang-scale-contrast.md)
* [Обзор языков профиля пользователя и языков манифеста приложения](manage-language-and-region.md)