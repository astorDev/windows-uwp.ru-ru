---
author: Xansky
Description: Lists the Microsoft UI Automation control patterns, the classes that clients use to access them, and the interfaces providers use to implement them.
ms.assetid: 2091883C-5D0C-44ED-936A-709022926A42
title: Шаблоны и интерфейсы элементов управления
label: Control patterns and interfaces
template: detail.hbs
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: e48045e27e8ee7796f5dda2afb691a9f6e5371b2
ms.sourcegitcommit: cbe7cf620622a5e4df7414f9e38dfecec1cfca99
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7443188"
---
# <a name="control-patterns-and-interfaces"></a>Шаблоны и интерфейсы элементов управления  



Здесь перечислены шаблоны элементов управления модели автоматизации пользовательского интерфейса Майкрософт, классы, которыми пользуются клиенты для доступа к ним, и интерфейсы, которыми пользуются поставщики для их реализации.

В таблице в данном разделе описаны элементы управления модели автоматизации пользовательского интерфейса Microsoft. В ней также перечислены классы, которые используют клиенты UI Automation для доступа к шаблонам элементов управления, и интерфейсы, которые используют поставщики автоматизации пользовательского интерфейса для их реализации. В столбце **Шаблон элемента управления** указывается имя шаблона с точки зрения клиента UI Automation как постоянное значение, указанное в свойстве [**Control Pattern Availability Property Identifiers**](https://msdn.microsoft.com/library/windows/desktop/Ee671199). С точки зрения поставщика UI Automation каждый из этих шаблонов является постоянным именем [**PatternInterface**](https://msdn.microsoft.com/library/windows/apps/BR242496). В столбце **Интерфейс поставщика класса** указывается имя интерфейса, который реализуют поставщики, чтобы предоставить данный шаблон для пользовательского элемента управления XAML.

Дополнительные сведения о методе реализации настраиваемых одноранговых элементов автоматизации, которые предоставляют шаблоны элементов управления и реализуют интерфейсы, см. в разделе [Настраиваемые одноранговые классы автоматизации](custom-automation-peers.md).

При реализации шаблона элемента управления также следует ознакомиться с документацией поставщика автоматизации пользовательского интерфейса, в которой объясняются некоторые функции, которые клиенты ожидают увидеть в шаблоне элемента управления независимо от того, какая инфраструктура пользовательского интерфейса используется для его реализации. Некоторые сведения, указанные в документации поставщика автоматизации пользовательского интерфейса, повлияют на способ реализации одноранговых элементов и правильную поддержку данного шаблона. Откройте раздел, посвященный [реализации шаблонов элементов управления UI Automation](https://msdn.microsoft.com/library/windows/desktop/Ee671292), и найдите страницу с описанием шаблона, который нужно реализовать.

| Шаблон элемента управления | Интерфейс поставщика класса | Описание |
|-----------------|--------------------------|-------------|
| **Annotation** | [**IAnnotationProvider**](https://msdn.microsoft.com/library/windows/apps/Hh738493) | Используется для предоставления свойств аннотации в документе. |
| **Dock** | [**IDockProvider**](https://msdn.microsoft.com/library/windows/apps/BR242565) | Используется для элементов управления, которые можно пристыковать к контейнеру. Например, разные виды панелей инструментов. |
| **Перетаскивание** | [**IDragProvider**](https://msdn.microsoft.com/library/windows/apps/Hh750322) | Используется для поддержки элементов управления с возможностью перетаскивания или элементов управления с перетаскиваемыми элементами. |
| **DropTarget** | [**IDropTargetProvider**](https://msdn.microsoft.com/library/windows/apps/Hh750327) | Используется для поддержки элементов управления, которые могут быть целевыми объектами в операции перетаскивания. |
| **ExpandCollapse** | [**IExpandCollapseProvider**](https://msdn.microsoft.com/library/windows/apps/BR242568) | Используется для поддержки элементов управления, которые можно визуально развернуть, чтобы отобразить дополнительное содержимое, и свернуть, чтобы скрыть содержимое. |
| **Grid** | [**IGridProvider**](https://msdn.microsoft.com/library/windows/apps/BR242578) | Используется для элементов управления, которые поддерживают функции сетки, такие как установка размеров и перемещение в указанную ячейку. Обратите внимание, что сама сетка не реализует этот шаблон, так как обеспечивает макет, но не является элементом управления. |
| **GridItem** | [**IGridItemProvider**](https://msdn.microsoft.com/library/windows/apps/BR242572) | Используется для элементов управления, имеющих ячейки в рамках сетки. |
| **Invoke** | [**IInvokeProvider**](https://msdn.microsoft.com/library/windows/apps/BR242582) | Используется для элементов управления, которые можно вызывать, например  [**Button**](https://msdn.microsoft.com/library/windows/apps/BR209265). |
| **ItemContainer** | [**IItemContainerProvider**](https://msdn.microsoft.com/library/windows/apps/BR242583) | Позволяет приложениям найти элемент в контейнере, например виртуализированный список. |
| **MultipleView** | [**IMultipleViewProvider**](https://msdn.microsoft.com/library/windows/apps/BR242585) | Используется для элементов управления, которые могут переключаться между несколькими представлениями одного набора сведений, данных или дочерних элементов. |
| **ObjectModel** | [**IObjectModelProvider**](https://msdn.microsoft.com/library/windows/apps/Dn251815) | Используется для предоставления указателя на модель базового объекта документа. |
| **RangeValue** | [**IRangeValueProvider**](https://msdn.microsoft.com/library/windows/apps/BR242590) | Используется для элементов управления, у которых есть диапазон значений, применяемый к элементу управления. Например, вертушка с годами может иметь диапазон с 1900 по нынешний год, а другая вертушка, представляющая месяцы, будет иметь диапазон от 1 до 12. |
| **Scroll** | [**IScrollProvider**](https://msdn.microsoft.com/library/windows/apps/BR242601) | Используется для элементов управления, которые можно прокручивать. Например, элементы управления с полосой прокрутки, активируемые, когда появляется больше информации, чем можно отобразить в видимой области элемента управления. |
| **ScrollItem** | [**IScrollItemProvider**](https://msdn.microsoft.com/library/windows/apps/BR242599) | Используется для элементов управления с отдельными элементами в прокручиваемом списке. Например, элемент управления "Список" с отдельными элементами в прокручиваемом списке, такими как элемент управления "Поле со списком". |
| **Selection** | [**ISelectionProvider**](https://msdn.microsoft.com/library/windows/apps/BR242616) | Используется для контейнерных элементов управления выбором. Например, [**ListBox**](https://msdn.microsoft.com/library/windows/apps/BR242868) и [**ComboBox**](https://msdn.microsoft.com/library/windows/apps/BR209348). |
| **SelectionItem** | [**ISelectionItemProvider**](https://msdn.microsoft.com/library/windows/apps/BR242610) | Используется для отдельных элементов в контейнерных элементах управления выбором, таких как поле со списком и список. |
| **Spreadsheet** | [**ISpreadsheetProvider**](https://msdn.microsoft.com/library/windows/apps/Dn251821) | Используется для предоставления содержимого электронной таблицы или другого документа сеточного типа. |
| **SpreadsheetItem** | [**ISpreadsheetItemProvider**](https://msdn.microsoft.com/library/windows/apps/Dn251817) | Используется для предоставления свойств ячейки электронной таблицы или другого документа сеточного типа. |
| **Styles** | [**IStylesProvider**](https://msdn.microsoft.com/library/windows/apps/Dn251823) | Используется для описания элемента пользовательского интерфейса с определенным стилем, заливкой, узором или формой. |
| **SynchronizedInput** | [**ISynchronizedInputProvider**](https://msdn.microsoft.com/library/windows/apps/Dn279198) | Позволяет клиентским приложениям UI Automation направлять данные, вводимые с помощью мыши или клавиатуры, в определенный элемент пользовательского интерфейса. |
| **Table** | [**ITableProvider**](https://msdn.microsoft.com/library/windows/apps/BR242623) | Используется для элементов управления, у которых есть сетка и заголовок. Например, табличный элемент управления "Календарь". |
| **TableItem** | [**ITableItemProvider**](https://msdn.microsoft.com/library/windows/apps/BR242620) | Используется для элементов в таблице. |
| **Text** | [**ITextProvider**](https://msdn.microsoft.com/library/windows/apps/BR242627) | Используется для полей ввода и документов, которые предоставляют текстовую информацию. Также см. разделы [**ITextRangeProvider**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.provider.itextrangeprovider) и [**ITextProvider2**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.provider.itextprovider2). |
| **TextChild** | [**ITextChildProvider**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.provider.itextchildprovider) | Используется для доступа к ближайшему предку элемента, поддерживающему шаблон элемента управления **Text**. |
| **TextEdit** | Управляемые классы недоступны | Предоставляет доступ к элементу управления, изменяющему текст, например к элементу управления, который выполняет автозамену или позволяет вводить данные с помощью редактора метода ввода (IME). |
| **TextRange** | [**ITextRangeProvider**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.provider.itextrangeprovider) | Предоставляет доступ к диапазону непрерывного текста в текстовом контейнере, который реализует [**ITextProvider**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.provider.itextprovider). Также см. раздел [**ITextRangeProvider2**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.automation.provider.itextrangeprovider2). |
| **Toggle** | [**IToggleProvider**](https://msdn.microsoft.com/library/windows/apps/BR242653) | Используется для элементов управления с переключаемым состоянием. Например, для [**CheckBox**](https://msdn.microsoft.com/library/windows/apps/BR209316) и элементов меню, которые можно выбрать. |
| **Transform** | [**ITransformProvider**](https://msdn.microsoft.com/library/windows/apps/BR242656) | Используется для элементов управления, которые можно переместить, повернуть или размер которых можно изменить. Обычно используется для шаблона элемента управления "Преобразование" в конструкторах, формах, графических редакторах и приложениях для рисования. |
| **Value** | [**IValueProvider**](https://msdn.microsoft.com/library/windows/apps/BR242663) | Позволяет клиенту получить или установить значение элемента управления, который не поддерживает диапазон значений. |
| **VirtualizedItem** | [**IVirtualizedItemProvider**](https://msdn.microsoft.com/library/windows/apps/BR242668) | Предоставляет элементы внутри контейнеров, которые были виртуализированы и должны быть полностью доступны в качестве элементов UI Automation. |
| **Window** | [**IWindowProvider**](https://msdn.microsoft.com/library/windows/apps/BR242670) | Предоставляет сведения, относящиеся к windows, основные концепции MicrosoftWindows операционной системы. В качестве примеров элементов управления, которые являются окнами, можно привести дочерние окна и диалоговые окна. |

> [!NOTE]
> Не факт, что вы найдете реализации всех этих шаблонов в существующих элементах управления XAML. Некоторые шаблоны имеют интерфейсы только для поддержки соответствия общему определению шаблонов в инфраструктуре автоматизации пользовательского интерфейса и для поддержки сценариев одноранговых классов автоматизации, в которых для поддержки этого шаблона требуется исключительно пользовательская реализация.

> [!NOTE]
> Приложения Магазина Windows Phone поддерживают не все перечисленные здесь шаблоны элементов управления модели автоматизации пользовательского интерфейса. Примеры неподдерживаемых шаблонов: **Annotation**, **Dock**, **Drag**, **DropTarget**, **ObjectModel**.

<span id="related_topics"/>

## <a name="related-topics"></a>Связанные темы  
* [Настраиваемые одноранговые элементы автоматизации](custom-automation-peers.md)
* [Специальные возможности](accessibility.md) 
