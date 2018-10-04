---
author: QuinnRadich
description: Позволяет пользователям просматривать и ставить оценки, отражающие степень удовлетворенности содержимым и службами.
title: Элемент управления "Оценка"
template: detail.hbs
ms.author: quradic
ms.date: 10/25/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows10, UWP
pm-contact: abarlow
design-contact: kimsea
dev-contact: mitra
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: 242ecdaf128e1e01b1bdeac4cce649504b8efc74
ms.sourcegitcommit: e6daa7ff878f2f0c7015aca9787e7f2730abcfbf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/03/2018
ms.locfileid: "4319372"
---
# <a name="rating-control"></a>Элемент управления "Оценка"

Элемент управления "Оценка" позволяет пользователям просматривать и ставить оценки, отражающие степень удовлетворенности содержимым и службами. Пользователи могут взаимодействовать с элементом управления оценками с помощью сенсорного ввода, пера, мыши, игрового контроллера или клавиатуры. В следующем руководстве показано, как использовать функции управления оценками, чтобы обеспечить гибкость и возможность настройки.

> **Важные API-интерфейсы**: [класс RatingControl](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.ratingcontrol)

![Пример элемента управления "Оценка"](images/rating_rs2_doc_ratings_intro.png)

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/RatingControl">открыть приложение и увидеть RatingControl в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

### <a name="editable-rating-with-placeholder-value"></a>Редактируемая оценка со значением заполнителя

Возможно, наиболее распространенный способ использования элемента управления оценками — отображение средней оценки с возможностью для пользователя вводить свои собственные значения оценки. В этом сценарии элемент управления оценками первоначально настроен на отражение средней удовлетворенности всех пользователей конкретной службой или типом содержимого (например, музыки, видео, книгами и т. д.). Он остается в этом состоянии, пока пользователь взаимодействует с элементом управления с целью оценки элемента по отдельности. Это взаимодействие изменяет состояние элемента управления оценками, чтобы отражать личную удовлетворенность пользователя.

#### <a name="initial-average-rating-state"></a>Начальное состояние средней оценки
![Начальное состояние средней оценки](images/rating_rs2_doc_movie_aggregate.png)

#### <a name="representation-of-user-rating-once-set"></a>Представление оценки после ее настройки

![Представление оценки после ее настройки](images/rating_rs2_doc_movie_user.png)

```XAML
<RatingControl x:Name="MyRating" ValueChanged="RatingChanged"/>
```

```csharp
private void RatingChanged(RatingControl sender, object args)
{
    if (sender.Value == null)
    {
        MyRating.Caption = "(" + SomeWebService.HowManyPreviousRatings() + ")";
    }

    else
    {
        MyRating.Caption = "Your rating";
    }
}
```

### <a name="read-only-rating-mode"></a>Режим оценки "Только чтение"

Иногда вам необходимо отображать оценки дополнительного содержимого, например, оценки, отображаемые в рекомендованном содержимом, или при отображении списка комментариев и их соответствующих оценок. В этом случае пользователь не сможет изменить оценку. Поэтому элемент управления можно сделать доступным только для чтения.
Режим только для чтения также является рекомендованным способом использования элемента управления оценками при использовании в больших виртуализированных списках содержимого по причинам проектирования и производительности пользовательского интерфейса.

![Длинный список только для чтения](images/rating_rs2_doc_reviews.png)

Для этого необходимо выполнить следующие действия.

```XAML
<RatingControl IsReadOnly="True"/>
```

## <a name="additional-functionality"></a>Дополнительные функциональные возможности

Элемент управления оценками содержит многие дополнительные функции, которые можно использовать. Подробные сведения об использовании этих функций можно найти в справочной документации в нашей библиотеке MSDN.
Ниже приведен неполный список дополнительных функций:
-   Отличная производительность длинного списка
-   Компактные размеры для строгих сценариев пользовательского интерфейса
-   Оценка и заливка непрерывного значения
-   Настройка интервала
-   Отключение анимации роста
-   Настройка количества звезд

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.