---
description: "В этой статье рассказывается, как добавить возможность перетаскивания в приложение универсальной платформы Windows (UWP)."
title: "Перетаскивание"
ms.assetid: A15ED2F5-1649-4601-A761-0F6C707A8B7E
author: awkoren
ms.author: alkoren
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: 84f78d43a0d9a34b8ba992a2357f08ad374b32d1
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="drag-and-drop"></a>Перетаскивание

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этой статье рассказывается, как добавить возможность перетаскивания в приложение универсальной платформы Windows (UWP). Перетаскивание — это классический естественный способ взаимодействовать с таким содержимым, как изображения и файлы. После реализации перетаскивание работает без проблем во всех направлениях, в том числе из приложения UWP в приложение UWP, из приложения UWP в классическое приложение и из классического приложения в приложение UWP.

## <a name="set-valid-areas"></a>Определение допустимых областей

С помощью свойств [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.AllowDrop) и [**CanDrag**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.CanDrag) можно назначить области приложения, в которых будет работать перетаскивание.

Следующая разметка демонстрирует, как сделать перетаскивание доступным для конкретной области приложения, используя [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.AllowDrop) в XAML. Если пользователь попытается отпустить перетаскиваемое содержимое в другом месте, система не позволит сделать это. Если вы хотите, чтобы пользователи могли использовать перетаскивание в любом месте вашего приложения, установите весь фон в качестве места переноса.

[!code-xml[Основной блок](./code/drag_drop/cs/MainPage.xaml#SnippetDropArea)]

Вы чаще всего конкретизируете, что именно пользователи смогут перетаскивать. Это будут только определенные элементы, например картинки, а не вообще все в приложении. Настройка функции [**CanDrag**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.CanDrag) с помощью XAML осуществляется следующим образом.

[!code-xml[Основной блок](./code/drag_drop/cs/MainPage.xaml#SnippetDragArea)]

Чтобы разрешить перетаскивание, больше ничего делать не нужно, если только вы не собираетесь менять пользовательский интерфейс (об этом рассказывается дальше в статье). Для настройки завершения перетаскивания придется выполнить некоторые действия.

## <a name="handle-the-dragover-event"></a>Обработка события DragOver

Событие [**DragOver**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.DragOver) возникает, когда пользователь перетаскивает элемент в приложении, но еще не завершил этот процесс. В этом обработчике необходимо с помощью свойства [**AcceptedOperation**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.AcceptedOperation) выбрать, какой вид операции будет поддерживать ваше приложение. Наиболее распространено копирование.

[!code-cs[Основной блок](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_DragOver)]

## <a name="process-the-drop-event"></a>Обработка завершения перетаскивания

Событие [**Drop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.Drop) возникает, когда пользователь отпускает элементы в допустимой области приложения. Обработайте их с помощью свойства [**DataView**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.DataView).

Для простоты в примере ниже предположим, что пользователь перетащил одну фотографию. На самом деле пользователи могут перетаскивать несколько элементов разных форматов одновременно. Приложение должно обрабатывать эту возможность, проверяя, какие типы файлов перемещены перетаскиванием, и обрабатывая их соответственно. Если пользователь пытается сделать что-то, не поддерживаемое приложением, ему необходимо показать соответствующее уведомление.

[!code-cs[Основной блок](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_Drop)]

## <a name="customize-the-ui"></a>Настройка пользовательского интерфейса

Система предоставляет пользовательский интерфейс по умолчанию для перетаскивания. Также можно настроить различные области пользовательского интерфейса, например пользовательские заголовки и глифы, или вообще отключить отображение пользовательского интерфейса. Чтобы настроить пользовательский интерфейс, используйте свойство [**DragEventArgs.DragUIOverride**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.DragUIOverride).

[!code-cs[Основной блок](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_DragOverCustom)]

## <a name="open-a-context-menu-on-an-item-you-can-drag-with-touch"></a>Открытие контекстного меню на элементе, который можно перетаскивать с помощью сенсорного управления

При использовании сенсорного управления перетаскивание [**UIElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement) и открытие его контекстного меню выполняются аналогичными сенсорными жестами, каждый из которых начинается с нажатия и удерживания. Вот как система различает эти два действия над элементами в вашем приложении, поддерживающими оба действия: 

* Если пользователь нажмет и будет удерживать элемент и начнет перетаскивать его в пределах 500 миллисекунд, элемент перетаскивается, а контекстное меню не отображается. 
* Если пользователь нажмет и будет удерживать элемент, но не начнет перетаскивать его в течение 500 миллисекунд, открывается контекстное меню. 
* Если пользователь попытается перетащить элемент (не отрывая палец) после открытия контекстного меню, меню закрывается, и начинается перетаскивание.

## <a name="designate-an-item-in-a-listview-or-gridview-as-a-folder"></a>Обозначение элемента в ListView или GridView в качестве папки

Вы можете указать [**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ListViewItem) или [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.GridViewItem) как папку. Это особенно удобно в сценариях TreeView и проводника. Для этого явно задайте свойству [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.AllowDrop) значение **True** для этого элемента. 

Система автоматически отобразит соответствующие анимации перетаскивания в папку в противоположность элементу, не являющемся папкой. Код вашего приложения должен продолжать обрабатывать событие [**Drop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.Drop) элемента папки (а также элемента, не являющегося папкой), чтобы обновить источник данных и добавить перетаскиваемый элемент в целевую папку.

## <a name="see-also"></a>См. также

* [Связь между приложениями](index.md)
* [AllowDrop](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.allowdrop.aspx)
* [CanDrag](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.candrag.aspx)
* [DragOver](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.dragover.aspx)
* [AcceptedOperation](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.acceptedoperation.aspx)
* [DataView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.dataview.aspx)
* [DragUIOverride](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.draguioverride.aspx)
* [Drop](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.drop.aspx)
* [IsDragSource](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.isdragsource.aspx)
