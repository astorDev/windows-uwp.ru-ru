---
author: jwmsft
Description: Learn how Fluent motion fundamentals come together in your app.
title: Движение на практике — анимация в приложениях UWP
label: Motion in practice
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows10, UWP
pm-contact: stmoy
design-contact: jeffarn
doc-status: Draft
ms.localizationpriority: medium
ms.openlocfilehash: 87a17d3f73887c9b1b5029e2096c5b41c9444c4e
ms.sourcegitcommit: 517c83baffd344d4c705bc644d7c6d2b1a4c7e1a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2018
ms.locfileid: "1843917"
---
# <a name="bringing-it-together"></a>Реализация

> [!IMPORTANT]
> В этой статье описана еще не выпущенная функция, которая может быть существенно изменена до коммерческого выпуска. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.

Синхронность, реалистичность анимации, направление и сила притяжения лежат в основе движения Fluent (плавность). Каждую характеристику следует учитывать в контексте других и применять соответствующим образом в контексте вашего приложения.

Ниже приведены три способа применения основных принципов движения Fluent в вашем приложении.

:::row::: :::column::: **Неявная анимация**

        Automatic tween and timing between values in a parameter change to achieve very simple Fluent motion using the standardized values.
    :::column-end:::
    :::column:::
        **Built-in animation**

        System components, such as common controls and shared motion, are "Fluent by default". Fundamentals have been applied in a manner consistent with their implied usage.
    :::column-end:::
    :::column:::
        **Custom animation following guidance recommendations**

        There may be times when the system does not yet provide an exact motion solution for your scenario. In those cases, use the baseline fundamental recommendations as a starting point for your experiences.
    :::column-end:::
:::row-end:::

### **<a name="transition-example"></a>Пример перехода**

![функциональная анимация](images/pageRefresh.gif)

:::row::: :::column::: <b>Направление вперед:</b><br>
        Исчезание: 150 м; Реалистичная анимация: ускорение по умолчанию

        <b>Direction Forward In:</b><br>
        Slide up 150px: 300ms; Easing: Default Decelerate
    :::column-end:::
    :::column:::
         <b>Direction Backward Out:</b><br>
        Slide down 150px: 150ms; Easing: Default Accelerate
      
        <b>Direction Backward In:</b><br>
        Fade in: 300ms; Easing: Default Decelerate
    :::column-end:::
:::row-end:::

 ### **<a name="object-example"></a>Пример объекта**

 ![Движение 300 мс](images/control.gif)
 
:::row::: :::column::: <b>Направление развертывания:</b><br>
        Увеличение: 300 мс; Реалистичная анимация: стандарт :::column-end::: :::column::: <b>Направление стягивания:</b><br>
        Увеличение: 150 мс; Реалистичная анимация: ускорение по умолчанию :::column-end::: :::row-end:::

## <a name="related-articles"></a>Смежные разделы

- [Обзор движения](index.md)
- [Синхронность и реалистичная анимация](timing-and-easing.md)
- [Направление и сила притяжения](directionality-and-gravity.md)