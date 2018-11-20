---
author: Jwmsft
Description: The master/detail pattern displays a master list and the details for the currently selected item. This pattern is frequently used for email and contact lists/address books.
title: Основные и подробные данные
ms.assetid: 45C9FE8B-ECA6-44BF-8DDE-7D12ED34A7F7
label: Master/details
template: detail.hbs
ms.author: jimwalk
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b30835e31e86c0c98d0c134ed28adca4413650c9
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7303564"
---
# <a name="masterdetails-pattern"></a>Шаблон основных и подробных данных

 

В шаблоне основных и подробных есть главная панель (обычно с [представлением списка](lists.md)) и область сведений для содержимого. Если выбрать элемент в главном списке, область сведений обновляется. Этот шаблон часто используется для работы с электронной почтой и адресными книгами.

> **Важные API-интерфейсы**: [класс ListView](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.ListView), [класс SplitView](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.splitview)

![Пример шаблона основных и подробных данных](images/HIGSecOne_MasterDetail.png)

## <a name="is-this-the-right-pattern"></a>Выбор правильного шаблона

Шаблон основных и подробных данных хорошо подходит, если вам нужно:

-   выполнить сборку приложения электронной почты, адресной книги или любого приложения на основе макета списка;
-   найти и назначить приоритет для большой коллекции содержимого;
-   разрешить быстрое добавление и удаление элементов из списка при работе между контекстами;

## <a name="choose-the-right-style"></a>выбрать правильный стиль.

При реализации шаблона основных и подробных данных рекомендуется использовать стиль стопкой или рядом в зависимости от доступного места на экране.

| Доступная ширина окна | Рекомендуемый стиль |
|------------------------|-------------------|
| 320–640 epx        | Стопкой           |
| 641 epx или шире       | Рядом      |

 
## <a name="stacked-style"></a>Стиль стопкой

При использовании расположения стопкой отображается только одна панель: главная или область сведений.

![Основные данные в режиме стопкой](images/patterns-md-stacked.png)

Пользователь начинает с главной панели и детализирует до области сведений, выбирая элемент в главном списке. Пользователям кажется, что основное и подробное представления существуют на двух отдельных страницах.

### <a name="create-a-stacked-masterdetails-pattern"></a>Создание шаблона основных и подробных данных стопкой

Один из способов создания шаблона основных и подробных данных заключается в использовании отдельных страниц для главной панели и области сведений. Разместите представление основных данных на одной странице, а панель подробных данных на отдельной странице.

![Части основного и подробного представления с расположением стопкой](images/patterns-md-stacked-parts.png)

На странице основных сведений [представление списка](lists.md) хорошо подходит для отображения списков, которые могут содержать изображения и текст. 

На странице подробных сведений используйте [элемент содержимого](../layout/layout-panels.md), который подходит лучше всего. Если имеется множество отдельных полей, можно использовать макет **Grid** (сетка) для размещения элементов в форме.

Сведения о навигации между страницами см. в разделе [Журнал навигации и навигация в обратном направлении для приложений UWP](../basics/navigation-history-and-backwards-navigation.md).

## <a name="side-by-side-style"></a>Стиль рядом

В стиле рядом главная панель и область сведений отображается одновременно.

![Шаблон основных и подробных данных](images/patterns-masterdetail-400x227.png)

В списке на главной панели есть визуальный элемент выбора для указания выбранного элемента. При выборе нового элемента в главном списке обновляется область сведений.

### <a name="create-a-side-by-side-masterdetails-pattern"></a>Создание шаблона основных и подробных данных рядом

Чтобы создать шаблон основных и подробных данных, размещенных рядом, можно использовать элемент управления [комбинированный режим](split-view.md). Разместите представление основных данных в области комбинированного режима, а представление подробных данных в содержимом комбинированного режима.

![части представлений основных и подробных данных в комбинированном режиме](images/patterns_md_splitview_parts.png)

На главной панели элемент управления [Представление списка](lists.md) хорошо подходит для отображения списков, которые могут содержать изображения и текст.

Для содержимого подробных сведений используйте [элемент содержимого](../layout/layout-panels.md), который подходит лучше всего. Если имеется множество отдельных полей, можно использовать макет **Grid** (сетка) для размещения элементов в форме.

## <a name="adaptive-layout"></a>Адаптивный макет

Чтобы реализовать шаблон основных и подробных данных для экрана любого размера, создайте пользовательский интерфейс с быстрым откликом, используя [адаптивный макет](../layout/layouts-with-xaml.md).

![адаптивный макет основных и подробных данных](images/patterns_masterdetail.png)

### <a name="create-an-adaptive-masterdetails-pattern"></a>Создание адаптивного шаблона основных и подробных данных
Чтобы создать адаптивный макет, определите разные состояния [**VisualStates**](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.visualstate) для пользовательского интерфейса и объявите точки останова для различных состояний с помощью [**AdaptiveTriggers**](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.AdaptiveTrigger).

## <a name="get-the-sample-code"></a>Получить пример кода

В следующих примерах показана реализация шаблона основных и подробных данных с помощью адаптивных макетов, а также демонстрируется привязывание данных к статическим, веб-ресурсам и ресурсам базы данных. 
- [Пример основных и подробных данных](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlMasterDetail) 
- [Пример основных и подробных данных и пример элемента выбора](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlListView)
- [Пример основных и подробных данных в Windows Template Studio](https://github.com/Microsoft/WindowsTemplateStudio/tree/master/templates/Uwp/Pages/MasterDetail)
- [Пример базы данных заказов клиента](https://github.com/Microsoft/Windows-appsample-customers-orders-database)
- [Пример средства чтения RSS](https://github.com/Microsoft/Windows-appsample-rssreader)

## <a name="related-articles"></a>Еще по теме

- [Списки](lists.md)
- [Поиск](search.md)
- [Панель приложения и панель команд](app-bars.md)
- [Класс ListView](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.ListView)
- [Класс SplitView](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.controls.splitview)
