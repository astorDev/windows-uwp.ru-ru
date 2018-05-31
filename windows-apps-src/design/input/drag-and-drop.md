---
description: В этой статье рассказывается, как добавить возможность перетаскивания в приложение универсальной платформы Windows (UWP).
title: Перетаскивание
ms.assetid: A15ED2F5-1649-4601-A761-0F6C707A8B7E
author: msatranjr
ms.author: misatran
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 48462623d17c612d716300acc3a64057738e72c9
ms.sourcegitcommit: 346b5c9298a6e9e78acf05944bfe13624ea7062e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/05/2018
ms.locfileid: "1708049"
---
# <a name="drag-and-drop"></a>Перетаскивание

Перетаскивание является интуитивно понятным способом передачи данных в приложении или между приложениями на компьютере с Windows. Перетаскивание дает пользователю возможность перемещать данные между приложениями или внутри приложения с помощью стандартного жеста ("нажатие-удержание-сдвиг" с помощью пальца или "нажатие-сдвиг" с помощью мыши или пера).

> **Важные API-интерфейсы**: [свойство CanDrag](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.CanDrag), [свойство AllowDrop](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.AllowDrop) 

Источник перетаскивания, который является приложением или областью, в которой запускается жест перетаскивания, предоставляет данные для передачи путем заполнения объекта пакета данных, который может содержать стандартные форматы данных, включая текст, RTF, HTML, точечные рисунки, элементы хранилища или пользовательские форматы данных. Источник также обозначает тип операций, которые он поддерживает: копировать, переместить или ссылка. При освобождении указателя выполняется перетаскивание. Место переноса, которое является приложением или областью под указателем, обрабатывает пакет данных и возвращает тип выполненной операции.

Во время перетаскивания пользовательский интерфейс перетаскивания предоставляет визуальную индикацию типа выполняемой операции перетаскивания. Эта визуальная обратная связь изначально предоставляется источником, но может быть изменена местом переноса при наведении на них указателя.

Современная функция перетаскивания доступна на всех устройствах, поддерживающих UWP. Эта функция позволяет передавать данные между приложениями любого типа либо внутри самого приложения, включая классические Windows-приложения. Тем не менее в этой статье основное внимание уделяется API-интерфейсу XAML для современной операции перетаскивания. После реализации функции перетаскивание корректно работает во всех направлениях, в том числе из приложения UWP в приложение UWP, из приложения UWP в классическое приложение и из классического приложения в приложение UWP.

Здесь приведен обзор действий по реализации функции перетаскивания в вашем приложении.

1. Включите возможность перетаскивания элемента, установив для его свойства **CanDrag** значение true.  
2. Выполнять построение пакета данных. Система обрабатывает изображения и текст автоматически, но для другого содержимого необходимо обрабатывать события **DragStarted** и **DragCompleted** и использовать их для создания собственного пакета данных. 
3. Включите перетаскивание, задав свойству **AllowDrop** значение **true** для всех элементов, которые могут принимать перетаскиваемое содержимое. 
4. Обработайте событие **DragOver**, чтобы сообщить системе, какие типы операций перетаскивания может принимать элемент. 
5. Обработайте событие **Drop** для получения перетаскиваемого содержимого. 



## <a name="enable-dragging"></a>Включение перетаскивания

Чтобы включить возможность перетаскивания элемента, задайте его свойству [**CanDrag**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.CanDrag) значение **true**. Это сделает элемент — и элементы, которые он содержит в случае использования коллекций, например, ListView — перетаскиваемым.

Определите, какие элементы будут поддерживать перетаскивание. Пользователям не требуется перетаскивать все содержимое в вашем приложении; только определенные элементы, такие как изображения и текст. 

Вот как задать свойство [**CanDrag**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.CanDrag).

[!code-xml[Main](./code/drag_drop/cs/MainPage.xaml#SnippetDragArea)]

Чтобы разрешить перетаскивание, больше ничего делать не нужно, если только вы не собираетесь менять пользовательский интерфейс (об этом рассказывается дальше в статье). Для настройки завершения перетаскивания придется выполнить некоторые действия.

## <a name="construct-a-data-package"></a>Создание пакета данных 

В большинстве случаев система будет создавать пакет данных за вас. Система автоматически обрабатывает:
* Изображения
* Текст 

При работе с другим содержимым необходимо обработать события **DragStarted** и **DragCompleted** и использовать их для создания собственного пакета данных [DataPackage](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackage).

## <a name="enable-dropping"></a>Включение отпускания

Следующая разметка демонстрирует, как сделать отпускание доступным для конкретной области приложения, используя [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.AllowDrop) в XAML. Если пользователь попытается отпустить перетаскиваемое содержимое в другом месте, система не позволит сделать это. Если вы хотите, чтобы пользователи могли использовать отпускание в любом месте вашего приложения, установите весь фон в качестве места переноса.

[!code-xml[Main](./code/drag_drop/cs/MainPage.xaml#SnippetDropArea)]


## <a name="handle-the-dragover-event"></a>Обработка события DragOver

Событие [**DragOver**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.DragOver) возникает, когда пользователь перетаскивает элемент в приложении, но еще не завершил этот процесс. В этом обработчике необходимо с помощью свойства [**AcceptedOperation**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.AcceptedOperation) выбрать, какой вид операции будет поддерживать ваше приложение. Наиболее распространено копирование.

[!code-cs[Main](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_DragOver)]

## <a name="process-the-drop-event"></a>Обработка события Drop

Событие [**Drop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.Drop) возникает, когда пользователь отпускает элементы в допустимой области приложения. Обработайте их с помощью свойства [**DataView**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.DataView).

Для простоты в примере ниже предположим, что пользователь перетащил одну фотографию напрямую. На самом деле пользователи могут перетаскивать несколько элементов разных форматов одновременно. Ваше приложение должно обрабатывать эту возможность, проверяя, какие типы файлов были перемещены перетаскиванием и сколько файлов существует, и обрабатывать каждый из них соответствующим образом. Вам также необходимо решить, следует ли уведомить пользователей, если они пытаются сделать что-то, что ваше приложение не поддерживает.

[!code-cs[Main](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_Drop)]

## <a name="customize-the-ui"></a>Настройка пользовательского интерфейса

Система предоставляет пользовательский интерфейс по умолчанию для перетаскивания. Также можно настроить различные области пользовательского интерфейса, например пользовательские заголовки и глифы, или вообще отключить отображение пользовательского интерфейса. Чтобы настроить пользовательский интерфейс, используйте свойство [**DragEventArgs.DragUIOverride**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.DragEventArgs.DragUIOverride).

[!code-cs[Main](./code/drag_drop/cs/MainPage.xaml.cs#SnippetGrid_DragOverCustom)]

## <a name="open-a-context-menu-on-an-item-you-can-drag-with-touch"></a>Открытие контекстного меню на элементе, который можно перетаскивать с помощью сенсорного управления

При использовании сенсорного управления перетаскивание [**UIElement**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement) и открытие его контекстного меню выполняются аналогичными сенсорными жестами, каждый из которых начинается с нажатия и удерживания. Вот как система различает эти два действия над элементами в вашем приложении, поддерживающими оба действия: 

* Если пользователь нажмет и будет удерживать элемент и начнет перетаскивать его в пределах 500 миллисекунд, элемент перетаскивается, а контекстное меню не отображается. 
* Если пользователь нажмет и будет удерживать элемент, но не начнет перетаскивать его в течение 500 миллисекунд, открывается контекстное меню. 
* Если пользователь попытается перетащить элемент (не отрывая палец) после открытия контекстного меню, меню закрывается, и начинается перетаскивание.

## <a name="designate-an-item-in-a-listview-or-gridview-as-a-folder"></a>Обозначение элемента в ListView или GridView в качестве папки

Вы можете указать [**ListViewItem**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.ListViewItem) или [**GridViewItem**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.Controls.GridViewItem) как папку. Это особенно удобно в сценариях TreeView и проводника. Для этого явно задайте свойству [**AllowDrop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.AllowDrop) значение **True** для этого элемента. 

Система автоматически отобразит соответствующие анимации перетаскивания в папку в противоположность элементу, не являющемся папкой. Код вашего приложения должен продолжать обрабатывать событие [**Drop**](https://msdn.microsoft.com/library/windows/apps/Windows.UI.Xaml.UIElement.Drop) элемента папки (а также элемента, не являющегося папкой), чтобы обновить источник данных и добавить перетаскиваемый элемент в целевую папку.

## <a name="implementing-custom-drag-and-drop"></a>Реализация пользовательской функции перетаскивания

Класс [UIElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement) выполняет большую часть работы по реализации функции перетаскивания за вас. Но если вы хотите, можно реализовать собственную версию функции с помощью API-интерфейсов в [пространстве имен Windows.ApplicationModel.DataTransfer.DragDrop.Core](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.datatransfer.dragdrop.core).

| Функции | API-интерфейс WinRT |
| --- | --- |
|  Включение перетаскивания | [CoreDragOperation](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.dragdrop.core.coredragoperation)  |
|  Создание пакета данных | [DataPackage](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.datapackage)  |
| Передача перетаскивания оболочке  | [CoreDragOperation.StartAsync](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.dragdrop.core.coredragoperation)  |
| Получение перетаскивания из оболочки  | [CoreDragDropManager](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.dragdrop.core.coredragdropmanager)<br/>[ICoreDropOperationTarget](https://docs.microsoft.com/uwp/api/windows.applicationmodel.datatransfer.dragdrop.core.icoredropoperationtarget)    |



## <a name="see-also"></a>Смежные разделы

* [Связь между приложениями](index.md)
* [AllowDrop](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.allowdrop.aspx)
* [CanDrag](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.candrag.aspx)
* [DragOver](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.dragover.aspx)
* [AcceptedOperation](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.acceptedoperation.aspx)
* [DataView](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.dataview.aspx)
* [DragUIOverride](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.drageventargs.draguioverride.aspx)
* [Drop](https://msdn.microsoft.com/library/windows/apps/xaml/windows.ui.xaml.uielement.drop.aspx)
* [IsDragSource](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.listviewbase.isdragsource.aspx)
