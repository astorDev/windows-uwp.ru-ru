---
description: "В этой статье рассказывается, как добавить возможность перетаскивания в приложение универсальной платформы Windows (UWP)."
title: "Перетаскивание"
ms.assetid: A15ED2F5-1649-4601-A761-0F6C707A8B7E
author: awkoren
ms.sourcegitcommit: 03f3f86ed1310e6e3ac5f53cc5e81ebef708a1a2
ms.openlocfilehash: ffa2f0f368a61ef4f3003c1fa03e143b26c6859b

---
# Перетаскивание

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В этой статье рассказывается, как добавить возможность перетаскивания в приложение универсальной платформы Windows (UWP). Перетаскивание — это классический естественный способ взаимодействовать с таким содержимым, как изображения и файлы. После реализации перетаскивание работает без проблем во всех направлениях, в том числе из приложения UWP в приложение UWP, из приложения UWP в классическое приложение и из классического приложения в приложение UWP.

## Определение допустимых областей

С помощью свойств [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.AllowDrop) и [**CanDrag**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.CanDrag) можно назначить области приложения, в которых будет работать перетаскивание.

Разметка ниже демонстрирует, как сделать перетаскивание доступным для конкретной области приложения, используя [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.AllowDrop) в XAML. Если пользователь попытается отпустить перетаскиваемое содержимое в другом месте, система не позволит сделать это. Если вы хотите, чтобы пользователи могли использовать перетаскивание в любом месте вашего приложения, установите весь фон в качестве места переноса.

[!code-xml[Основной блок](./code/drag_drop/cs/MainPage.xaml#SnippetDropArea)]

Вы чаще всего конкретизируете, что именно пользователи смогут перетаскивать. Это будут только определенные элементы, например картинки, а не вообще все в приложении. Настройка функции [**CanDrag**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.CanDrag) с помощью XAML осуществляется следующим образом.

[!code-xml[Основной блок](./code/drag_drop/cs/MainPage.xaml#SnippetDragArea)]

Чтобы разрешить перетаскивание, больше ничего делать не нужно, если только вы не собираетесь менять пользовательский интерфейс (об этом рассказывается дальше в статье). Для настройки завершения перетаскивания придется выполнить некоторые действия.

## Обработка события DragOver

Событие [**DragOver**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.DragOver) возникает, когда пользователь перетаскивает элемент в приложении, но еще не завершил этот процесс. В этом обработчике необходимо с помощью свойства [**AcceptedOperation**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.AcceptedOperation) выбрать, какой вид операции будет поддерживать ваше приложение. Наиболее распространено копирование.

[!code-cs[Основной блок](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_DragOver)]

## Обработка завершения перетаскивания

Событие [**Drop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.Drop) возникает, когда пользователь отпускает элементы в допустимой области приложения. Обработайте их с помощью свойства [**DataView**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.DataView).

Для простоты в примере ниже предположим, что пользователь перетащил одну фотографию. На самом деле пользователи могут перетаскивать несколько элементов разных форматов одновременно. Приложение должно обрабатывать эту возможность, проверяя, какие типы файлов перемещены перетаскиванием, и обрабатывая их соответственно. Если пользователь пытается сделать что-то, не поддерживаемое приложением, ему необходимо показать соответствующее уведомление.

[!code-cs[Основной блок](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_Drop)]

## Настройка пользовательского интерфейса

Система предоставляет пользовательский интерфейс по умолчанию для перетаскивания. Также можно настроить различные области пользовательского интерфейса, например пользовательские заголовки и глифы, или вообще отключить отображение пользовательского интерфейса. Чтобы настроить пользовательский интерфейс, используйте свойство [**DragEventArgs.DragUIOverride**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.DragUIOverride).

[!code-cs[Основной блок](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_DragOverCustom)]

## См. также

* [AllowDrop](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.allowdrop.aspx)
* [CanDrag](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.candrag.aspx)
* [DragOver](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.dragover.aspx)
* [AcceptedOperation](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.acceptedoperation.aspx)
* [DataView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.dataview.aspx)
* [DragUiOverride](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.draguioverride.aspx)
* [Drop](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.drop.aspx)


<!--HONumber=Jun16_HO4-->


