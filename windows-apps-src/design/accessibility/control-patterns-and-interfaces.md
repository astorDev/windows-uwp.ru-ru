---
Description: Здесь перечислены шаблоны элементов управления модели автоматизации пользовательского интерфейса Майкрософт, классы, которыми пользуются клиенты для доступа к ним, и интерфейсы, которыми пользуются поставщики для их реализации.
ms.assetid: 2091883C-5D0C-44ED-936A-709022926A42
title: Шаблоны и интерфейсы элементов управления
label: Control patterns and interfaces
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: fc8018a4ccf53c106a1e10410593e214d9dcead7
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66359569"
---
# <a name="control-patterns-and-interfaces"></a>Шаблоны и интерфейсы элементов управления  



Здесь перечислены шаблоны элементов управления модели автоматизации пользовательского интерфейса Майкрософт, классы, которыми пользуются клиенты для доступа к ним, и интерфейсы, которыми пользуются поставщики для их реализации.

В таблице в данном разделе описаны элементы управления модели автоматизации пользовательского интерфейса Microsoft. В ней также перечислены классы, которые используют клиенты UI Automation для доступа к шаблонам элементов управления, и интерфейсы, которые используют поставщики автоматизации пользовательского интерфейса для их реализации. В столбце **Шаблон элемента управления** указывается имя шаблона с точки зрения клиента UI Automation как постоянное значение, указанное в свойстве [**Control Pattern Availability Property Identifiers**](https://docs.microsoft.com/windows/desktop/WinAuto/uiauto-control-pattern-availability-propids). С точки зрения поставщика UI Automation каждый из этих шаблонов является постоянным именем [**PatternInterface**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.PatternInterface). В столбце **Интерфейс поставщика класса** указывается имя интерфейса, который реализуют поставщики, чтобы предоставить данный шаблон для пользовательского элемента управления XAML.

Дополнительные сведения о методе реализации настраиваемых одноранговых элементов автоматизации, которые предоставляют шаблоны элементов управления и реализуют интерфейсы, см. в разделе [Настраиваемые одноранговые классы автоматизации](custom-automation-peers.md).

При реализации шаблона элемента управления также следует ознакомиться с документацией поставщика автоматизации пользовательского интерфейса, в которой объясняются некоторые функции, которые клиенты ожидают увидеть в шаблоне элемента управления независимо от того, какая инфраструктура пользовательского интерфейса используется для его реализации. Некоторые сведения, указанные в документации поставщика автоматизации пользовательского интерфейса, повлияют на способ реализации одноранговых элементов и правильную поддержку данного шаблона. Откройте раздел, посвященный [реализации шаблонов элементов управления UI Automation](https://docs.microsoft.com/windows/desktop/WinAuto/uiauto-implementinguiautocontrolpatterns), и найдите страницу с описанием шаблона, который нужно реализовать.

| Шаблон элемента управления | Интерфейс поставщика класса | Описание |
|-----------------|--------------------------|-------------|
| **Заметки** | [**IAnnotationProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IAnnotationProvider) | Используется для предоставления свойств аннотации в документе. |
| **Dock** | [**IDockProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IDockProvider) | Используется для элементов управления, которые можно пристыковать к контейнеру. Например, разные виды панелей инструментов. |
| **Перетащите** | [**IDragProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IDragProvider) | Используется для поддержки элементов управления с возможностью перетаскивания или элементов управления с перетаскиваемыми элементами. |
| **Конечный** | [**IDropTargetProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IDropTargetProvider) | Используется для поддержки элементов управления, которые могут быть целевыми объектами в операции перетаскивания. |
| **ExpandCollapse** | [**IExpandCollapseProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IExpandCollapseProvider) | Используется для поддержки элементов управления, которые можно визуально развернуть, чтобы отобразить дополнительное содержимое, и свернуть, чтобы скрыть содержимое. |
| **Сетка** | [**IGridProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IGridProvider) | Используется для элементов управления, которые поддерживают функции сетки, такие как установка размеров и перемещение в указанную ячейку. Обратите внимание, что сама сетка не реализует этот шаблон, так как обеспечивает макет, но не является элементом управления. |
| **GridItem** | [**IGridItemProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IGridItemProvider) | Используется для элементов управления, имеющих ячейки в рамках сетки. |
| **Вызвать** | [**IInvokeProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IInvokeProvider) | Используется для элементов управления, которые можно вызывать, например  [**Button**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button). |
| **ItemContainer** | [**IItemContainerProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IItemContainerProvider) | Позволяет приложениям найти элемент в контейнере, например виртуализированный список. |
| **MultipleView модели** | [**IMultipleViewProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IMultipleViewProvider) | Используется для элементов управления, которые могут переключаться между несколькими представлениями одного набора сведений, данных или дочерних элементов. |
| **ObjectModel** | [**IObjectModelProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IObjectModelProvider) | Используется для предоставления указателя на модель базового объекта документа. |
| **RangeValue** | [**IRangeValueProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IRangeValueProvider) | Используется для элементов управления, у которых есть диапазон значений, применяемый к элементу управления. Например, вертушка с годами может иметь диапазон с 1900 по нынешний год, а другая вертушка, представляющая месяцы, будет иметь диапазон от 1 до 12. |
| **Прокрутки** | [**IScrollProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IScrollProvider) | Используется для элементов управления, которые можно прокручивать. Например, элементы управления с полосой прокрутки, активируемые, когда появляется больше информации, чем можно отобразить в видимой области элемента управления. |
| **ScrollItem модели** | [**IScrollItemProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IScrollItemProvider) | Используется для элементов управления с отдельными элементами в прокручиваемом списке. Например, элемент управления "Список" с отдельными элементами в прокручиваемом списке, такими как элемент управления "Поле со списком". |
| **Выбор** | [**ISelectionProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ISelectionProvider) | Используется для контейнерных элементов управления выбором. Например, [**ListBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListBox) и [**ComboBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ComboBox). |
| **SelectionItem** | [**ISelectionItemProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ISelectionItemProvider) | Используется для отдельных элементов в контейнерных элементах управления выбором, таких как поле со списком и список. |
| **Электронную таблицу** | [**ISpreadsheetProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ISpreadsheetProvider) | Используется для предоставления содержимого электронной таблицы или другого документа сеточного типа. |
| **SpreadsheetItem** | [**ISpreadsheetItemProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ISpreadsheetItemProvider) | Используется для предоставления свойств ячейки электронной таблицы или другого документа сеточного типа. |
| **Стили** | [**IStylesProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IStylesProvider) | Используется для описания элемента пользовательского интерфейса с определенным стилем, заливкой, узором или формой. |
| **SynchronizedInput** | [**ISynchronizedInputProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ISynchronizedInputProvider) | Позволяет клиентским приложениям UI Automation направлять данные, вводимые с помощью мыши или клавиатуры, в определенный элемент пользовательского интерфейса. |
| **Таблицы** | [**ITableProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ITableProvider) | Используется для элементов управления, у которых есть сетка и заголовок. Например, табличный элемент управления "Календарь". |
| **TableItem** | [**ITableItemProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ITableItemProvider) | Используется для элементов в таблице. |
| **Text** | [**ITextProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ITextProvider) | Используется для полей ввода и документов, которые предоставляют текстовую информацию. Также см. разделы [**ITextRangeProvider**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.provider.itextrangeprovider) и [**ITextProvider2**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.provider.itextprovider2). |
| **TextChild** | [**ITextChildProvider**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.provider.itextchildprovider) | Используется для доступа к ближайшему предку элемента, поддерживающему шаблон элемента управления **Text**. |
| **TextEdit** | Управляемые классы недоступны | Предоставляет доступ к элементу управления, изменяющему текст, например к элементу управления, который выполняет автозамену или позволяет вводить данные с помощью редактора метода ввода (IME). |
| **TextRange** | [**ITextRangeProvider**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.provider.itextrangeprovider) | Предоставляет доступ к диапазону непрерывного текста в текстовом контейнере, который реализует [**ITextProvider**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.provider.itextprovider). Также см. раздел [**ITextRangeProvider2**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.provider.itextrangeprovider2). |
| **Переключение** | [**IToggleProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IToggleProvider) | Используется для элементов управления с переключаемым состоянием. Например, для [**CheckBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox) и элементов меню, которые можно выбрать. |
| **Преобразование** | [**ITransformProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.ITransformProvider) | Используется для элементов управления, которые можно переместить, повернуть или размер которых можно изменить. Обычно используется для шаблона элемента управления "Преобразование" в конструкторах, формах, графических редакторах и приложениях для рисования. |
| **Значение** | [**IValueProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IValueProvider) | Позволяет клиенту получить или установить значение элемента управления, который не поддерживает диапазон значений. |
| **VirtualizedItem** | [**IVirtualizedItemProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IVirtualizedItemProvider) | Предоставляет элементы внутри контейнеров, которые были виртуализированы и должны быть полностью доступны в качестве элементов UI Automation. |
| **Окно** | [**IWindowProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IWindowProvider) | Предоставляет сведения, относящиеся к windows, основные концепции ОС Microsoft Windows. В качестве примеров элементов управления, которые являются окнами, можно привести дочерние окна и диалоговые окна. |

> [!NOTE]
> Не факт, что вы найдете реализации всех этих шаблонов в существующих элементах управления XAML. Некоторые шаблоны имеют интерфейсы только для поддержки соответствия общему определению шаблонов в инфраструктуре автоматизации пользовательского интерфейса и для поддержки сценариев одноранговых классов автоматизации, в которых для поддержки этого шаблона требуется исключительно пользовательская реализация.

> [!NOTE]
> Приложения Магазина Windows Phone поддерживают не все перечисленные здесь шаблоны элементов управления модели автоматизации пользовательского интерфейса. Примеры неподдерживаемых шаблонов: **Annotation**, **Dock**, **Drag**, **DropTarget**, **ObjectModel**.

<span id="related_topics"/>

## <a name="related-topics"></a>См. также  
* [Настраиваемые одноранговые классы автоматизации](custom-automation-peers.md)
* [Специальные возможности](accessibility.md) 
