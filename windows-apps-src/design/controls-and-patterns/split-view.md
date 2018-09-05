---
author: QuinnRadich
title: Комбинированный режим
ms.assetid: E9E4537F-1160-4183-9A83-26602FCFDC9A
description: Элемент управления "Комбинированный режим" содержит разворачиваемую и сворачиваемую панель, а также область содержимого.
label: Split view
template: detail.hbs
ms.author: quradic
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp
pm-contact: yulikl
design-contact: kimsea
dev-contact: tpaine
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: cde4b5d95a0c978faa647fcc108d74874ff52c40
ms.sourcegitcommit: 7aa1933e6970f878faf50d59e1f799b90afd7cc7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2018
ms.locfileid: "3379417"
---
# <a name="split-view-control"></a>Элемент управления «Комбинированный режим»

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

Элемент управления "Комбинированный режим" можно использовать для создания взаимодействия типа "секция", позволяющего пользователям открывать и закрывать дополнительную панель. Например, можно использовать SplitView для создания шаблона [основных и подробных данных](master-details.md).

Если вы хотите создать меню навигации с кнопками развертывания/свертывания и список элементов навигации, используйте элемент управления [NavigationView](navigationview.md).

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/SplitView">открыть приложение и увидеть SplitView в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

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

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-topics"></a>Смежные разделы
- [Шаблон панели навигации](navigationview.md)
- [Представление списка](lists.md)
- [Основные и подробные данные](master-details.md)