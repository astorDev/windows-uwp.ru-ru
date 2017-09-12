---
author: Jwmsft
title: "Комбинированный режим"
ms.assetid: E9E4537F-1160-4183-9A83-26602FCFDC9A
description: "Элемент управления &quot;Комбинированный режим&quot; содержит разворачиваемую и сворачиваемую панель, а также область содержимого."
label: Split view
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: tpaine
doc-status: Published
ms.openlocfilehash: 126fab3db9a0728626289788757f576648a43856
ms.sourcegitcommit: 10d6736a0827fe813c3c6e8d26d67b20ff110f6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2017
---
# <a name="split-view-control"></a>Элемент управления «Комбинированный режим»

<link rel="stylesheet" href="https://az835927.vo.msecnd.net/sites/uwp/Resources/css/custom.css"> 

Элемент управления "Комбинированный режим" содержит разворачиваемую и сворачиваемую панель, а также область содержимого.

> **Важные API-интерфейсы**: [класс SplitView](https://msdn.microsoft.com/library/windows/apps/dn864360)

Вот пример использования SplitView в Microsoft Edge для отображения Центра.

![Пример использования комбинированного режима в Microsoft Edge](images/split_view_Edge.png)


 Область содержимого комбинированного режима всегда является видимой. Панель можно развернуть или свернуть либо оставить открытой; ее можно расположить в левой или правой части окна приложения. Панель можно использовать в четырех режимах.

-   **Наложение**

    Панель скрыта до тех пор, пока не будет открыта. После открытия панель перекрывает область содержимого.

-   **Встроенный**

    Панель всегда видима и не перекрывает область содержимого. Доступное пространство экрана делится между областью панели и областью содержимого.

-   **CompactOverlay**

    В этом режиме отображается узкая часть панели, ширины которой хватает лишь для отображения значков. Ширина закрытой панели по умолчанию составляет 48 пикселей и может быть изменена с помощью свойства `CompactPaneLength`. При открытии панель перекрывает область содержимого.

-   **CompactInline**

    В этом режиме отображается узкая часть панели, ширины которой хватает лишь для отображения значков. Ширина закрытой панели по умолчанию составляет 48 пикселей и может быть изменена с помощью свойства `CompactPaneLength`. Когда панель открыта, уменьшается пространство, доступное для содержимого, так как панель занимает его место.

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Элемент управления "Комбинированный режим" можно использовать для создания [панели навигации](navigationview.md). Для создания такого шаблона необходимо добавить кнопку развертывания и свертывания ("кнопку-гамбургер") и представление списка для элементов навигации.

Элемент управления "Комбинированный режим" можно также использовать для создания взаимодействия типа "секция", позволяющего пользователям открывать и закрывать дополнительную панель.

## <a name="create-a-split-view"></a>Создание комбинированного режима

Здесь представлен элемент управления SplitView вместе с открытой панелью (Pane), отображающейся рядом с содержимым (Content).
```xaml
<SplitView IsPaneOpen="True"
           DisplayMode="Inline"
           OpenPaneLength="296">
    <SplitView.Pane>
        <TextBlock Text="Pane"
                   FontSize="24"
                   VerticalAlignment="Center"
                   HorizontalAlignment="Center"/>
    </SplitView.Pane>

    <Grid>
        <TextBlock Text="Content"
                   FontSize="24"
                   VerticalAlignment="Center"
                   HorizontalAlignment="Center"/>
    </Grid>
</SplitView>
```



## <a name="related-topics"></a>Еще по теме
* [Шаблон панели навигации](navigationview.md)
* [Представление списка](lists.md)
 

 
