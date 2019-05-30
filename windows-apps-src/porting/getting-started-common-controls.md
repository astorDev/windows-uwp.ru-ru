---
ms.assetid: E2B73380-D673-48C6-9026-96976D745017
description: Приступая к работе с общими элементами управления
title: Приступая к работе с общими элементами управления
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: a64dbd6a9530f81c55b0d4b52e4c0fd55c4b9956
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372829"
---
# <a name="getting-started-common-controls"></a>Приступая к работе: Стандартные элементы управления


## <a name="common-controls-list"></a>Список общих элементов управления

В предыдущем разделе вы работали только с двумя элементами управления: кнопками и текстовыми блоками. Конечно, существует намного больше элементов, которые будут доступны пользователю. Вот некоторые элементы управления, часто используемые в приложениях, и их аналоги в iOS. Элементы управления iOS перечислены в алфавитном порядке. Напротив них указаны эквивалентные элементы управления универсальной платформы Windows (UWP).

Довольно продуманной функцией элементов управления UWP является то, что они могут распознавать тип устройств, на которых они запущены, и менять свой внешний вид и функции соответствующим образом. Например, если в проекте используется элемент управления [**DatePicker**](https://docs.microsoft.com/previous-versions/windows/apps/br211681(v=win.10)), он достаточно продуман, чтобы самостоятельно оптимизировать внешний вид и вести себя на настольном компьютере иначе, чем на телефоне. Ничего делать не нужно: элементы управления настраиваются самостоятельно во время работы.

| Элемент управления iOS (класс и протокол) | Эквивалентный элемент управления универсальной платформы Windows |
|------------------------------|--------------------------------------|
| Индикатор действий (**UIActivityIndicatorView**) | [**ProgressRing**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) <br/> См. также [Краткое руководство. Добавление элементов управления ходом выполнения](https://docs.microsoft.com/previous-versions/windows/apps/hh780651(v=win.10)) |
| Представление баннера (**ADBannerView**) и делегат представления баннера (**ADBannerViewDelegate**) | [AdControl](https://docs.microsoft.com/uwp/api/microsoft.advertising.winrt.ui.adcontrol) <br/> См. также [Показ рекламы в приложении](../monetize/display-ads-in-your-app.md) |
| Кнопка (UIButton) | [Button](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) <br/> См. также [краткое руководство: Добавление элементов управления кнопки](https://docs.microsoft.com/previous-versions/windows/apps/jj153346(v=win.10)) |
| Управляющий элемент выбора даты (UIDatePicker) | [DatePicker](https://docs.microsoft.com/previous-versions/windows/apps/br211681(v=win.10)) |
| Представление изображения (UIImageView) | [Изображение](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) <br/> См. также статью [Image и ImageBrush](https://docs.microsoft.com/windows/uwp/controls-and-patterns/images-imagebrushes) |
| Подпись (UILabel) | [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) <br/> См. также [Краткое руководство: отображение текста](https://docs.microsoft.com/previous-versions/windows/apps/hh700392(v=win.10)). |
| Представление карты (MKMapView) и делегат представления карты (MKMapViewDelegate) | См. в разделе [карт Bing для приложений UWP](https://go.microsoft.com/fwlink/p/?LinkId=263496) |
| Контроллер навигации (UINavigationController) и делегат контроллера навигации (UINavigationControllerDelegate) | [Frame](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) <br/> См. также раздел [Навигация](https://docs.microsoft.com/windows/uwp/layout/navigation-basics). |
| Элемент управления страницей (UIPageControl) | [Страница](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page) <br/> См. также раздел [Навигация](https://docs.microsoft.com/windows/uwp/layout/navigation-basics). |
| Представление выбора (UIPickerView) и делегат представления выбора (UIPickerViewDelegate) | [ComboBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ComboBox) <br/> См. также [Добавление полей со списком и списков](https://docs.microsoft.com/previous-versions/windows/apps/hh780616(v=win.10)). |
| Индикатор выполнения (UIProgressView) | [ProgressBar](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressBar) <br/> См. также [Краткое руководство. Добавление элементов управления ходом выполнения](https://docs.microsoft.com/previous-versions/windows/apps/hh780651(v=win.10)) |
| Представление с прокруткой (UIScrollView) и делегат представления с прокруткой (UIScrollViewDelegate) | [ScrollViewer](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ScrollViewer) <br/>  См. также [Примеры прокрутки, сдвига и масштабирования в XAML](https://go.microsoft.com/fwlink/p/?LinkId=238577) |
| Панель поиска (UISearchBar) и делегат панели поиска (UISearchBarDelegate) | См. раздел [Добавление поиска в приложение](https://docs.microsoft.com/previous-versions/windows/apps/jj130767(v=win.10)) <br/>  См. также [краткое руководство: Добавление поиска в приложение](https://docs.microsoft.com/previous-versions/windows/apps/hh868180(v=win.10)) |
| Сегментированный элемент управления (UISegmentedControl) | Нет |
| Ползунок (UISlider) | [Slider](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider) <br/>  См. также раздел [Добавление ползунка](https://docs.microsoft.com/previous-versions/windows/apps/hh868197(v=win.10)) |
| Контроллер разделенного представления (UISplitViewController) и делегат контроллера разделенного представления (UISplitViewControllerDelegate) | Нет |
| Переключатель (UISwitch) | [ToggleSwitch](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ToggleSwitch) <br/>  См. также раздел [Добавление переключателя](https://docs.microsoft.com/previous-versions/windows/apps/hh868198(v=win.10)) |
| Контроллер панели вкладок (UITabBarController) и делегат контроллера панели вкладок (UITabBarControllerDelegate) | Нет |
| Контроллер представления таблицы (UITableViewController), представление таблицы (UITableView), делегат представления таблицы (UITableViewDelegate) и ячейка таблицы (UITableViewCell) | [ListView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) <br/>  См. также [Краткое руководство: добавление элементов управления ListView и GridView](https://docs.microsoft.com/previous-versions/windows/apps/hh780650(v=win.10)). |
| Текстовое поле (UITextField) и делегат текстового поля (UITextFieldDelegate) | [TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) <br/>  См. также статью [Отображение и редактирование текста](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/text-controls) |
| Текстовое представление (UITextView) и делегат текстового представления (UITextViewDelegate) | [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) <br/>  См. также [Краткое руководство: отображение текста](https://docs.microsoft.com/previous-versions/windows/apps/hh700392(v=win.10)). |
| Представление (UIView) и контроллер представления (UIViewController) | [Страница](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Page) <br/>  См. также раздел [Навигация](https://docs.microsoft.com/windows/uwp/layout/navigation-basics). |
| Веб-представление (UIWebView) и делегат веб-представления (UIWebViewDelegate) | [WebView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.WebView) <br/>  См. также [Пример элемента управления WebView на XAML](https://go.microsoft.com/fwlink/p/?LinkId=238582) |
| Окно (UIWindow) | [Frame](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) <br/>  См. также раздел [Навигация](https://docs.microsoft.com/windows/uwp/layout/navigation-basics). |

Сведения о других элементах управления: [Список элементов управления](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/).

**Примечание**  список элементов управления для приложений универсальной платформы Windows, с помощью JavaScript и HTML, см. в разделе [список элементов управления](https://docs.microsoft.com/previous-versions/windows/apps/hh465453(v=win.10)).

### <a name="next-step"></a>Дальнейшие действия

[Приступая к работе: Навигации](getting-started-navigation.md)

## <a name="related-topics"></a>См. также

* [сборка 2014: Как насчет пользовательского интерфейса XAML и элементов управления?](https://go.microsoft.com/fwlink/p/?LinkID=397897)
* [сборка 2014: Разработка приложений с помощью общей инфраструктуры пользовательского интерфейса XAML](https://go.microsoft.com/fwlink/p/?LinkID=397898)
* [сборка 2014: С помощью Visual Studio для создания XAML Конвергированных приложений](https://go.microsoft.com/fwlink/p/?LinkID=397876)
