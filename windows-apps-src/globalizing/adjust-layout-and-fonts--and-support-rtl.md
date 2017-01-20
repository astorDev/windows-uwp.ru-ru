---
author: DelfCo
Description: "Разработайте свое приложение таким образом, чтобы оно поддерживало макеты и шрифты различных языков, в том числе с написанием справа налево."
title: "Настройка макета и шрифтов, добавление поддержки написания справа налево"
ms.assetid: F2522B07-017D-40F1-B3C8-C4D0DFD03AC3
label: Adjust layout and fonts, and support RTL
template: detail.hbs
translationtype: Human Translation
ms.sourcegitcommit: a3924fef520d7ba70873d6838f8e194e5fc96c62
ms.openlocfilehash: 05208607b148a48e8a680691de90161feeea7700

---

# <a name="adjust-layout-and-fonts-and-support-rtl"></a>Настройка макета и шрифтов, добавление поддержки написания справа налево
<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css">

Разработайте свое приложение таким образом, чтобы оно поддерживало макеты и шрифты различных языков, в том числе с написанием справа налево.

## <a name="layout-guidelines"></a>Руководство по макетам


В некоторых языках (например, немецком и финском) для размещения текста требуется больше места, чем в английском языке. Шрифты некоторых языков, например японского, требуют больше места по вертикали. Для некоторых языков (например, арабского и иврита) необходимо, чтобы макеты текста и приложения читались справа налево.

Используйте гибкие механизмы макета вместо абсолютного позиционирования и фиксированной ширины или высоты. При необходимости отдельные элементы пользовательского интерфейса можно настраивать в зависимости от языка.

Укажите идентификатор **Uid** для элемента.

```XML
<TextBlock x:Uid="Block1">
```

Убедитесь, что файл ResW вашего приложения имеет ресурс для свойства Block1.Width, которое можно задать для каждого языка, на который осуществляется локализация.

В приложениях Магазина Windows на C++, C\# или Visual Basic, для которых нужно обеспечить локализацию макетов с другим направлением письма, используйте свойство [**FlowDirection**](https://msdn.microsoft.com/library/windows/apps/br208716) с симметричными полями и заполнением.

Элементы управления макета XAML, например [**Сетка**](https://msdn.microsoft.com/library/windows/apps/br242704), автоматически масштабируются и зеркально отражаются с помощью свойства [**FlowDirection**](https://msdn.microsoft.com/library/windows/apps/br208716). Предоставьте свое собственное свойство **FlowDirection** в приложении в качестве ресурса для специалистов по локализации.

Укажите идентификатор **Uid** для главной страницы приложения.

```XML
<Page x:Uid="MainPage">
```

Убедитесь, что файл **ResW** вашего приложения имеет ресурс для свойства MainPage.FlowDirection, которое можно задать для каждого языка, на который осуществляется локализация.


## <a name="mirroring-images"></a>Зеркальное отображение изображений

Если приложение содержит изображения, которые должны быть отражены (то есть, можно просто отразить то же самое изображение) для написания справа налево, можно использовать свойство [**FlowDirection**](https://msdn.microsoft.com/library/windows/apps/br208716).

```XML
<!-- en-US\localized.xaml -->
<Image ... FlowDirection="LeftToRight" />

<!-- ar-SA\localized.xaml -->
<Image ... FlowDirection="RightToLeft" />
```


Если вашему приложению требуется другое изображение для правильного отражения, можно использовать систему управления ресурсами с [квалификатором layoutdir](https://msdn.microsoft.com/library/windows/apps/xaml/hh965324). Система выбирает изображение с именем file.layoutdir-rtl.png, когда в качестве [языка приложения](manage-language-and-region.md) задан язык с написанием справа налево. Этот подход может применяться, если одна часть изображения зеркально отражается, а другая — нет.

## <a name="fonts"></a>Шрифты

Используйте API сопоставления шрифтов [**LanguageFont**](https://msdn.microsoft.com/library/windows/apps/br206864) для программного доступа к рекомендуемому семейству, размеру, насыщенности и стилю шрифта определенного языка. Объект **LanguageFont** предоставляет доступ к нужной информации о шрифте для различных категорий содержимого, включая заголовки, уведомления, текст и шрифты доступных для редактирования текстов пользовательского интерфейса.

## <a name="best-practices-for-handling-right-to-left-rtl-languages"></a>Рекомендации по работе с языками, которые используют написание справа налево (RTL)

Если приложение локализуется для языков с написанием справа налево (RTL), используйте API, чтобы задать направление текста по умолчанию для RootFrame. В результате все элементы управления, содержащиеся в этом RootFrame, будут соответствующим образом реагировать на направление текста по умолчанию.  Если поддерживается несколько языков, используйте LayoutDirection для предпочитаемого языка, чтобы задать значение свойства FlowDirection. Большинство элементов управления в Windows уже используют FlowDirection. Если вы реализуете собственные элементы управления, они должны использовать свойство FlowDirection для соответствующего изменения макета для языков с написанием справа налево и слева направо.

C#
```csharp    
// For bidirectional languages, determine flow direction for RootFrame and all derived UI.

    string resourceFlowDirection = ResourceContext.GetForCurrentView().QualifierValues["LayoutDirection"];
    if (resourceFlowDirection == "LTR")
    {
       RootFrame.FlowDirection = FlowDirection.LeftToRight;
    }
    else
    {
       RootFrame.FlowDirection = FlowDirection.RightToLeft;
    }
```

C++:
```
    // Get preferred app language
    m_language = Windows::Globalization::ApplicationLanguages::Languages->GetAt(0);
     
    // Set flow direction accordingly
    m_flowDirection = ResourceManager::Current->DefaultContext->QualifierValues->Lookup("LayoutDirection") != "LTR" ? 
       FlowDirection::RightToLeft : FlowDirection::LeftToRight;
```


### <a name="rtl-faq"></a>ВОПРОСЫ И ОТВЕТЫ О ЯЗЫКАХ С НАПИСАНИЕМ СПРАВА НАЛЕВО 

<dl>
  <dt> <p><b>Вопрос.</b> Задается ли <b>FlowDirection</b> в зависимости от текущего языка? Например, если выбрать английский язык, будет ли текст отображаться слева направо, а если выбрать арабский, будет ли текст отображаться справа налево?</p></dt>

  <dd><p><b>Ответ. Свойство</b> <b>FlowDirection</b> не учитывает язык. Вы задали <b>FlowDirection</b> для языка, который сейчас отображается. См. пример кода выше.</p></dd> 

  <dt> <p><b>Вопрос.</b> Я не знаком с локализацией. Ресурсы уже содержат направление текста? Можно ли определить направление текста по текущему языку?</p></dt>

  <dd> <p><b>Ответ.</b> Если вы используете текущие рекомендации, ресурсы не содержат направление текста. Вам необходимо определить направление текста для текущего языка. Это можно сделать двумя способами. </p>
   <p>Рекомендуется использовать LayoutDirection для предпочитаемого языка, чтобы установить свойство FlowDirection объекта RootFrame. Все элементы управления в RootFrame наследуют свойство FlowDirection от RootFrame.</p>
   <p>Другой способ — задать FlowDirection в RESW-файле для нужных языков с написанием справа налево. Например, у вас может быть RESW-файл для арабского языка и RESW-файл для иврита. В этих файлах можно использовать атрибут x:UID для установки FlowDirection. Этот метод более восприимчив к ошибкам, чем программный метод.</p></dd>
</dl>


## <a name="related-topics"></a>Связанные разделы
[FlowDirection](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.frameworkelement.flowdirection.aspx)



<!--HONumber=Dec16_HO2-->


