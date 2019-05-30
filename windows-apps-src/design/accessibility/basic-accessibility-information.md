---
Description: Основные сведения о специальных возможностях часто классифицируются по названию, роли и значению. В этом разделе описывается код, благодаря которому ваше приложение сможет предоставлять основные сведения, необходимые вспомогательным технологиям.
ms.assetid: 9641C926-68C9-4842-8B55-C38C39A9E5C5
title: Предоставление основных сведений о специальных возможностях
label: Expose basic accessibility information
template: detail.hbs
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: c4b796c8af493508328730c2b958a0beb38bf60c
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66361960"
---
# <a name="expose-basic-accessibility-information"></a>Предоставление основных сведений о специальных возможностях  



Основные сведения о специальных возможностях часто классифицируются по названию, роли и значению. В этом разделе описывается код, благодаря которому ваше приложение сможет предоставлять основные сведения, необходимые вспомогательным технологиям.

<span id="accessible_name"/>
<span id="ACCESSIBLE_NAME"/>

## <a name="accessible-name"></a>Специальное имя  
Специальное имя — это краткая описательная текстовая строка, которую использует программа чтения с экрана, чтобы объявить элемент пользовательского интерфейса. Задайте специальное имя для элементов пользовательского интерфейса со значением, важным для понимания содержимого или для взаимодействия с интерфейсом. К таким элементам обычно относятся изображения, поля ввода, кнопки, элементы управления и области.

В этой таблице описано, как задать или получить специальное имя для различных типов элементов пользовательского интерфейса XAML.

| Тип элемента | Описание |
|--------------|-------------|
| Статический текст | Для элементов [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) и [**RichTextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock) специальное имя определяется автоматически на основе видимого (внутреннего) текста. Весь текст в этом элементе используется как имя. См. [Имя на основе внутреннего текста](#name_from_inner_text). |
| Изображений | Элемент XAML [**Image**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image) не имеет прямого аналога для атрибута HTML **alt** в **img** и аналогичных элементах. Используйте [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name?view=netframework-4.8), чтобы задать имя, или примените метод титров. Подробнее: [Специальные имена изображений](#images). |
| Элементы формы | Специальное имя для элемента формы должно совпадать с меткой, отображаемой для этого элемента. См. [Метки и LabeledBy](#labels). |
| Кнопки и ссылки | По умолчанию специальное имя кнопки или ссылки задается на основе видимого текста с помощью правил, аналогичных указанным в статье [Имя на основе внутреннего текста](#name_from_inner_text). Если кнопка содержит только изображение, используйте [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name?view=netframework-4.8) для предоставления текстового эквивалента предполагаемого действия кнопки. |

Большинство элементов-контейнеров, например панели, не предлагают использовать свое содержимое в качестве специального имени. Это связано с тем, что именно содержимое элемента (а не его контейнер) должно сообщить имя и соответствующую роль. Элемент-контейнер может сообщить о том, что у него есть дочерние элементы в представлении модели автоматизации пользовательского интерфейса Майкрософт и что он доступен для просмотра логикой специальных возможностей. Но пользователям специальных возможностей обычно не нужно знать о контейнерах, поэтому большинство контейнеров не имеют имен.

<span id="role_value"/>
<span id="ROLE_VALUE"/>

## <a name="role-and-value"></a>Роль и значение  
Элементы управления и другие элементы пользовательского интерфейса, которые входят в словарь XAML, реализуют поддержку модели автоматизации пользовательского интерфейса для сообщения роли и значения в рамках своих определений. С помощью инструментов модели автоматизации пользовательского интерфейса можно проверить сведения о роли и значении элементов управления. Вы также можете прочитать документацию по реализации [**AutomationPeer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationPeer) каждого элемента управления. Доступные роли в рамках инфраструктуры автоматизации пользовательского интерфейса определены в перечислении [**AutomationControlType**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.AutomationControlType). Клиенты автоматизации пользовательского интерфейса, в том числе специальные возможности, могут получать данные о роли, вызывая методы, предоставляемые инфраструктурой автоматизации пользовательского интерфейса, с помощью объекта **AutomationPeer** для элемента управления.

Не всем элементам управления присвоено значение. Элементы управления со значением передают эти сведения модели автоматизации пользовательского интерфейса через кэширующие узлы и шаблоны, которые поддерживаются этим элементом управления. Например, у элемента формы [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) есть значение. Специальные возможности иногда относятся к клиентам автоматизации пользовательского интерфейса и могут определить, существует ли значение и чему оно равно. В данном случае **TextBox** поддерживает шаблон [**IValueProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Provider.IValueProvider) посредством определений [**TextBoxAutomationPeer**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.Peers.TextBoxAutomationPeer).

> [!NOTE]
> Если вы используете [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name?view=netframework-4.8) или другие методы явного указания специального имени, не включайте в него текст, который используется в данных о роли или типе элемента управления. Например, не используйте в имени такие строки, как «button» или «list». Сведения о роли и типе поступают от другого свойства модели автоматизации пользовательского интерфейса (**LocalizedControlType**), которое предоставляется поддержкой элемента управления по умолчанию для модели автоматизации пользовательского интерфейса. Многие специальные возможности добавляют **LocalizedControlType** к специальному имени, поэтому дублирование роли в специальном имени может привести к повторению слов. Например, если вы зададите для элемента управления [**Button**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Button) специальное имя «button» или укажете строку «button» в качестве последней части имени, то средства чтения с экрана могут прочитать это имя как «button button». Эту особенность специальных возможностей нужно проверить с помощью экранного диктора.

<span id="Influencing_the_UI_Automation_tree_views"/>
<span id="influencing_the_ui_automation_tree_views"/>
<span id="INFLUENCING_THE_UI_AUTOMATION_TREE_VIEWS"/>

## <a name="influencing-the-ui-automation-tree-views"></a>Воздействие на представления дерева автоматизации пользовательского интерфейса  
Инфраструктура автоматизации пользовательского интерфейса Microsoft Windows включает иерархические представления, из которых клиенты модели могут получать отношения между элементами пользовательского интерфейса с помощью трех возможных представлений: базового представления, представления элементов управления или представления содержимого. Клиенты модели часто используют представление элементов управления, поскольку оно отражает представление и организацию интерактивных элементов интерфейса. Инструменты тестирования обычно позволяют выбрать иерархическое представление для отображения организации элементов.

Когда инфраструктура автоматизации пользовательского интерфейса представляет пользовательский интерфейс приложения универсальной платформы Windows (UWP), в представлении элементов управления по умолчанию будут отображаться элементы любого класса, производного от [**Control**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Control), и некоторые другие элементы. Однако в ряде случаев отображение элемента в представлении элементов управления нежелательно из-за состава пользовательского интерфейса, в котором этот элемент дублирует сведения или представляет информацию, лишнюю для сценариев специальных возможностей. Чтобы изменить способ предоставления элементов для иерархических представлений, используйте присоединенное свойство [**AutomationProperties.AccessibilityView**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.automation.automationproperties.accessibilityviewproperty). Если вы добавите элемент в дерево **Raw**, то большинство специальных возможностей не будут сообщать о том, что этот элемент входит в их представление. Чтобы увидеть, как это работает в существующих элементах управления, откройте эталонный XAML-файл generic.xaml в текстовом редакторе и найдите в шаблонах строку **AutomationProperties.AccessibilityView**.

<span id="name_from_inner_text"/>
<span id="NAME_FROM_INNER_TEXT"/>

## <a name="name-from-inner-text"></a>Имя на основе внутреннего текста  
Чтобы было проще использовать строки, уже существующие в видимом пользовательском интерфейсе, для значений специальных имен, многие элементы управления и другие элементы пользовательского интерфейса автоматически определяют специальное имя по умолчанию на основе внутреннего текста элемента или строкового значения свойств содержимого.

* [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock), [ **RichTextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichTextBlock), [ **TextBox** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) и **RichTextBlock** каждого распространять значения **текст** свойство как доступное имя по умолчанию.
* Любой подкласс [**ContentControl**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.contentcontrol.content) использует последовательные вызовы метода «ToString» для поиска строк в своем значении [**Content**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.contentcontrol.content) и предлагает эти строки в качестве специального имени по умолчанию.

> [!NOTE]
> В модели автоматизации пользовательского интерфейса требуется, чтобы длина специального имени не превышала 2048 символов. Если строка, используемая для автоматического определения специального имени, превышает этот предел, специальное имя усекается в этой точке.

<span id="images"/>
<span id="IMAGES"/>

## <a name="accessible-names-for-images"></a>Специальные имена изображений
Для реализации поддержки программ чтения с экрана и предоставления основных идентифицирующих сведений для каждого элемента пользовательского интерфейса иногда необходимо добавить текстовые альтернативы нетекстовой информации, например изображениям и диаграммам (за исключением любых чисто декоративных или структурных элементов). Такие элементы не имеют внутреннего текста, и поэтому специальное имя не будет иметь вычисляемого значения. Вы можете непосредственно задать специальное имя, настроив присоединенное свойство [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name?view=netframework-4.8), как показано в следующем примере.

XAML
```xml
<!-- Comment -->
<Image Source="product.png"
  AutomationProperties.Name="An image of a customer using the product."/>
```

Как вариант, вы можете добавить текстовый заголовок, который появляется в видимом пользовательском интерфейсе и будет выступать в качестве связанной с меткой информации о специальных возможностях для содержимого изображения. Ниже приведен пример:

XAML
```xml
<Image HorizontalAlignment="Left" Width="480" x:Name="img_MyPix"
  Source="snoqualmie-NF.jpg"
  AutomationProperties.LabeledBy="{Binding ElementName=caption_MyPix}"/>
<TextBlock x:Name="caption_MyPix">Mount Snoqualmie Skiing</TextBlock>
```

<span id="labels"/>
<span id="LABELS"/>

## <a name="labels-and-labeledby"></a>Метки и LabeledBy  
Предпочтительный способ связать метку с элементом формы — использовать [**TextBlock**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock) с **x:Name** в качестве текста метки, а затем задать присоединенное свойство [**AutomationProperties.LabeledBy**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/ms591292(v%3Dvs.95)) элемента формы для обращения к меткам **TextBlock** по имени XAML. Когда при использовании этого шаблона пользователь щелкает метку, фокус перемещается на связанный элемент управления, и специальные возможности могут использовать текст метки в качестве специального имени для поля формы. Вот пример, демонстрирующий этот метод.

XAML
```xml
<StackPanel x:Name="LayoutRoot" Background="White">
   <StackPanel Orientation="Horizontal">
     <TextBlock Name="lbl_FirstName">First name</TextBlock>
     <TextBox
      AutomationProperties.LabeledBy="{Binding ElementName=lbl_FirstName}"
      Name="tbFirstName" Width="100"/>
   </StackPanel>
   <StackPanel Orientation="Horizontal">
     <TextBlock Name="lbl_LastName">Last name</TextBlock>
     <TextBox
      AutomationProperties.LabeledBy="{Binding ElementName=lbl_LastName}"
      Name="tbLastName" Width="100"/>
   </StackPanel>
 </StackPanel>
```

<span id="accessible_description"/>
<span id="ACCESSIBLE_DESCRIPTION"/>

## <a name="accessible-description-optional"></a>Специальное описание (необязательно)  
Специальное описание предоставляет дополнительные сведения о специальных возможностях конкретного элемента пользовательского интерфейса. Обычно специальное описание используется тогда, когда специальное имя само по себе недостаточно точно передает назначение элемента.

Экранный диктор для чтения с экрана озвучивает специальное описание элемента только тогда, когда пользователь запрашивает дополнительные сведения об элементе, нажав клавиши CAPSLOCK+F.

Специальное имя предназначено для идентификации элемента управления, а не для подробного описания его поведения. Если для пояснения элемента управления краткого описания недостаточно, вы можете задать присоединенное свойство [**AutomationProperties.HelpText**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.helptext?view=netframework-4.8) в дополнение к [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name?view=netframework-4.8).

<span id="Testing_accessibility_early_and_often"/>
<span id="testing_accessibility_early_and_often"/>
<span id="TESTING_ACCESSIBILITY_EARLY_AND_OFTEN"/>

## <a name="testing-accessibility-early-and-often"></a>Раннее и частое тестирование специальных возможностей  
Наилучший способ проверить поддержку программы чтения с экрана — самостоятельно протестировать свое приложение, используя эту программу. В результате вы узнаете, как ведет себя программа чтения с экрана и какие основные сведения о специальных возможностях отсутствуют в приложении. После этого вы сможете соответствующим образом настроить значения свойств пользовательского интерфейса или модели его автоматизации. Дополнительную информацию см. в [статье, посвященной проверке специальных возможностей](accessibility-testing.md).

Один из инструментов проверки называется **AccScope**. Инструмент **AccScope** очень полезен, поскольку позволяет увидеть визуальное представление пользовательского интерфейса. По нему можно судить о том, как может выглядеть приложение с точки зрения специальных возможностей в качестве дерева автоматизации. В частности, в экранном дикторе есть режим, в котором можно увидеть, как диктор получает текст из приложения и упорядочивает элементы пользовательского интерфейса. AccScope можно с успехом использовать на протяжении всего цикла разработки приложения, даже на стадии предварительной разработки. Подробнее: [AccScope](https://docs.microsoft.com/windows/desktop/WinAuto/accscope).

<span id="Accessible_names_from_dynamic_data"/>
<span id="accessible_names_from_dynamic_data"/>
<span id="ACCESSIBLE_NAMES_FROM_DYNAMIC_DATA"/>

## <a name="accessible-names-from-dynamic-data"></a>Специальные имена на основе динамических данных  
Windows поддерживает множество элементов управления, которые можно использовать для отображения значений, поступающих из связанного источника данных, с помощью функции под названием *привязка данных*. При заполнении списков элементами данных вам, возможно, потребуется использовать метод, который задает специальные имена для элементов списка с привязкой к данным после заполнения первоначального списка. Подробнее см. сценарий 4 в [образце специальных возможностей XAML](https://go.microsoft.com/fwlink/p/?linkid=238570).

<span id="Accessible_names_and_localization"/>
<span id="accessible_names_and_localization"/>
<span id="ACCESSIBLE_NAMES_AND_LOCALIZATION"/>

## <a name="accessible-names-and-localization"></a>Специальные имена и локализация  
Чтобы гарантировать, что специальное имя также будет локализовано, используйте правильные методы для хранения локализуемых строк в качестве ресурсов, а также для ссылок на подключения ресурсов со значениями [x:Uid directive](https://docs.microsoft.com/windows/uwp/xaml-platform/x-uid-directive). Если специальное имя происходит от явно заданного использования [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name?view=netframework-4.8), убедитесь, что его строка также может быть локализована.

Помните, что вложенные свойства, такие как [**AutomationProperties**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Automation.AutomationProperties), используют особый синтаксис соответствия имени ресурса, поэтому ресурс ссылается на вложенное свойство применительно к отдельному элементу. Например, имя ресурса [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name?view=netframework-4.8) применительно к элементу пользовательского интерфейса с именем `MediumButton` — `MediumButton.[using:Windows.UI.Xaml.Automation]AutomationProperties.Name`.

<span id="related_topics"/>

## <a name="related-topics"></a>См. также  
* [Специальные возможности](accessibility.md)
* [**AutomationProperties.Name**](https://docs.microsoft.com/dotnet/api/system.windows.automation.automationproperties.name?view=netframework-4.8)
* [XAML примера специальных возможностей](https://go.microsoft.com/fwlink/p/?linkid=238570)
* [Проверка специальных возможностей](accessibility-testing.md)
