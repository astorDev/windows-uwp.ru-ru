---
ms.assetid: E2B73380-D673-48C6-9026-96976D745017
description: Приступая к работе: общие элементы управления
title: Приступая к работе: общие элементы управления
---

# Приступая к работе: общие элементы управления

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x, см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]

## Список общих элементов управления

В предыдущем разделе вы работали только с двумя элементами управления: кнопками и текстовыми блоками. Разумеется, существует множество других элементов управления, которые вы можете использовать. Вот некоторые элементы управления, часто используемые в приложениях, и их аналоги в iOS. Элементы управления iOS перечислены в алфавитном порядке. Напротив них указаны эквивалентные элементы управления универсальной платформы Windows (UWP).

Довольно продуманной функцией элементов управления UWP является то, что они могут распознавать тип устройств, на которых они запущены, и менять свой внешний вид и функции соответствующим образом. Например, если в проекте используется элемент управления [**DatePicker**](https://msdn.microsoft.com/library/windows/apps/br211681), он достаточно продуман, чтобы самостоятельно оптимизировать внешний вид и вести себя на настольном компьютере иначе, чем на телефоне. Ничего делать не нужно: элементы управления настраиваются самостоятельно во время работы.

| Элемент управления iOS (класс и протокол) | Эквивалентный элемент управления приложения Магазина Windows |
|------------------------------|--------------------------------------|
| Индикатор действий (**UIActivityIndicatorView**) | [**ProgressRing**](https://msdn.microsoft.com/library/windows/apps/br227538) <br/> См. также [Краткое руководство. Добавление элементов управления ходом выполнения](https://msdn.microsoft.com/library/windows/apps/xaml/hh780651) |
| Представление баннера (**ADBannerView**) и делегат представления баннера (**ADBannerViewDelegate**) | См. раздел [Пакет Microsoft Advertising SDK](http://go.microsoft.com/fwlink/p/?LinkId=263494) |
| Кнопка (UIButton) | [Button](https://msdn.microsoft.com/library/windows/apps/br209265) <br/> См. также [Краткое руководство: добавление кнопок](https://msdn.microsoft.com/library/windows/apps/xaml/jj153346) |
| Управляющий элемент выбора даты (UIDatePicker) | [DatePicker](https://msdn.microsoft.com/library/windows/apps/br211681) |
| Представление изображения (UIImageView) | [Image](https://msdn.microsoft.com/library/windows/apps/br242752) <br/> См. также статью [Image и ImageBrush](https://msdn.microsoft.com/library/windows/apps/mt280382) |
| Подпись (UILabel) | [TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652) <br/> См. также [Краткое руководство: отображение текста](https://msdn.microsoft.com/library/windows/apps/xaml/hh700392). |
| Представление карты (MKMapView) и делегат представления карты (MKMapViewDelegate) | См. также [Карты Bing для приложений Магазина Windows](http://go.microsoft.com/fwlink/p/?LinkId=263496). |
| Контроллер навигации (UINavigationController) и делегат контроллера навигации (UINavigationControllerDelegate) | [Frame](https://msdn.microsoft.com/library/windows/apps/br242682) <br/> См. также раздел [Навигация](https://msdn.microsoft.com/library/windows/apps/mt187344). |
| Элемент управления страницей (UIPageControl) | [Page](https://msdn.microsoft.com/library/windows/apps/br227503) <br/> См. также раздел [Навигация](https://msdn.microsoft.com/library/windows/apps/mt187344). |
| Представление выбора (UIPickerView) и делегат представления выбора (UIPickerViewDelegate) | [ComboBox](https://msdn.microsoft.com/library/windows/apps/br209348) <br/> См. также [Добавление полей со списком и списков](https://msdn.microsoft.com/library/windows/apps/xaml/hh780616). |
| Индикатор выполнения (UIProgressView) | [ProgressBar](https://msdn.microsoft.com/library/windows/apps/br227529) <br/> См. также [Краткое руководство. Добавление элементов управления ходом выполнения](https://msdn.microsoft.com/library/windows/apps/xaml/hh780651) |
| Представление с прокруткой (UIScrollView) и делегат представления с прокруткой (UIScrollViewDelegate) | [ScrollViewer](https://msdn.microsoft.com/library/windows/apps/br209527) <br/>  См. также [Примеры прокрутки, сдвига и масштабирования в XAML](http://go.microsoft.com/fwlink/p/?LinkId=238577) |
| Панель поиска (UISearchBar) и делегат панели поиска (UISearchBarDelegate) | См. раздел [Добавление поиска в приложение](https://msdn.microsoft.com/library/windows/apps/xaml/jj130767) <br/>  См. также [Краткое руководство. Добавление поиска в приложение](https://msdn.microsoft.com/library/windows/apps/xaml/hh868180) |
| Сегментированный элемент управления (UISegmentedControl) | Нет |
| Ползунок (UISlider) | [Slider](https://msdn.microsoft.com/library/windows/apps/br209614) <br/>  См. также раздел [Добавление ползунка](https://msdn.microsoft.com/library/windows/apps/xaml/hh868197) |
| Контроллер разделенного представления (UISplitViewController) и делегат контроллера разделенного представления (UISplitViewControllerDelegate) | Нет |
| Переключатель (UISwitch) | [ToggleSwitch](https://msdn.microsoft.com/library/windows/apps/br209712) <br/>  См. также раздел [Добавление переключателя](https://msdn.microsoft.com/library/windows/apps/xaml/hh868198) |
| Контроллер панели вкладок (UITabBarController) и делегат контроллера панели вкладок (UITabBarControllerDelegate) | Нет |
| Контроллер представления таблицы (UITableViewController), представление таблицы (UITableView), делегат представления таблицы (UITableViewDelegate) и ячейка таблицы (UITableViewCell) | [ListView](https://msdn.microsoft.com/library/windows/apps/br242878) <br/>  См. также [Краткое руководство: добавление элементов управления ListView и GridView](https://msdn.microsoft.com/library/windows/apps/xaml/hh780650). |
| Текстовое поле (UITextField) и делегат текстового поля (UITextFieldDelegate) | [TextBox](https://msdn.microsoft.com/library/windows/apps/br209683) <br/>  См. также статью [Отображение и редактирование текста](https://msdn.microsoft.com/library/windows/apps/mt280218) |
| Текстовое представление (UITextView) и делегат текстового представления (UITextViewDelegate) | [TextBlock](https://msdn.microsoft.com/library/windows/apps/br209652) <br/>  См. также [Краткое руководство: отображение текста](https://msdn.microsoft.com/library/windows/apps/xaml/hh700392). |
| Представление (UIView) и контроллер представления (UIViewController) | [Page](https://msdn.microsoft.com/library/windows/apps/br227503) <br/>  См. также раздел [Навигация](https://msdn.microsoft.com/library/windows/apps/mt187344). |
| Веб-представление (UIWebView) и делегат веб-представления (UIWebViewDelegate) | [WebView](https://msdn.microsoft.com/library/windows/apps/br227702) <br/>  См. также [Пример элемента управления WebView на XAML](http://go.microsoft.com/fwlink/p/?LinkId=238582) |
| Окно (UIWindow) | [Frame](https://msdn.microsoft.com/library/windows/apps/br242682) <br/>  См. также раздел [Навигация](https://msdn.microsoft.com/library/windows/apps/mt187344). |

Сведения о других элементах управления: [Список элементов управления](https://msdn.microsoft.com/library/windows/apps/mt185406).

**Примечание.**  Список элементов управления для приложений Магазина Windows на JavaScript и HTML см. в статье [Список элементов управления](https://msdn.microsoft.com/library/windows/apps/hh465453).

### Следующие шаги

[Приступая к работе: навигация](getting-started-navigation.md)

## Связанные разделы

* [build 2014: как насчет пользовательского интерфейса XAML и элементов управления?](http://go.microsoft.com/fwlink/p/?LinkID=397897)
* [build 2014: разработка приложений с помощью общей инфраструктуры пользовательского интерфейса XAML](http://go.microsoft.com/fwlink/p/?LinkID=397898)
* [build 2014: использование Visual Studio для создания универсальных приложений XAML](http://go.microsoft.com/fwlink/p/?LinkID=397876)


<!--HONumber=Mar16_HO1-->


