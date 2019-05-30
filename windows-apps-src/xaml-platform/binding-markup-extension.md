---
description: Расширение разметки Binding преобразовывается во время загрузки XAML в экземпляр класса Binding.
title: Расширение разметки Binding'
ms.assetid: 3BAFE7B5-AF33-487F-9AD5-BEAFD65D04C3
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: c2d54e9077429c6526168492c31a9d24a43a1be8
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66366536"
---
# <a name="binding-markup-extension"></a>Расширение разметки {Binding}


**Примечание**  новый механизм привязки доступна для Windows 10, которая оптимизирована по производительности и производительностью разработчика. См. раздел [Расширение разметки: {x:Bind}](x-bind-markup-extension.md).

**Примечание**  Общие сведения об использовании привязки данных в приложении с помощью **{Binding}** (и для сравнения комплексная между **{x: Bind}** и **{Binding}** ), см. в разделе [привязки данных в глубину](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth).

**{Binding}** расширение разметки используется для свойств привязки данных в элементах управления для значений, поступающих из источника данных, например, код. Расширение разметки **{Binding}** преобразовывается во время загрузки XAML в экземпляр класса [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding). Этот объект привязки получает значение из свойства в источнике данных и передает его в свойство в элементе управления. Объект привязки можно дополнительно настроить таким образом, чтобы он регистрировал изменения значений свойства источника данных и сам обновлялся на основании этих данных. Кроме того, его можно настроить, чтобы он отправлял изменения значения элемента управления назад к свойству источника. Свойство, являющееся целью привязки данных, должно быть свойством зависимостей. Подробнее см. в разделе [Общие сведения о свойствах зависимостей](dependency-properties-overview.md).

**{Binding}** имеет одинаковый с локальным значением приоритет свойства зависимостей, и настройка локального значения в императивном коде удаляет эффект любой привязки **{Binding}** , настроенной в разметке.

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML


``` syntax
<object property="{Binding}" .../>
-or-
<object property="{Binding propertyPath}" .../>
-or-
<object property="{Binding bindingProperties}" .../>
-or-
<object property="{Binding propertyPath, bindingProperties}" .../>
```

| Термин | Описание |
|------|-------------|
| *propertyPath* | Строка, указывающая путь свойства для привязки. Подробную информацию см. в разделе [Путь свойства](#property-path) ниже. |
| *bindingProperties* | *propName*=*значение*\[, *propName*=*значение*\]*<br/>Одна или несколько привязок свойств, указанных с помощью синтаксиса пары "имя/значение". |
| *propName* | Имя строки свойства для установки в объекте [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding). Например, Converter. |
| *Значение* | Значение, которое следует задать для свойства. Синтаксис аргумента зависит от свойства раздела [Свойства класса Binding, которые можно задать в {Binding}](#properties-of-the-binding-class-that-can-be-set-with-binding) ниже |

## <a name="property-path"></a>Путь свойства

[**Путь** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path) описывает свойство, которое осуществляется привязка к (свойство источника). Path — это позиционный параметр, то есть вы можете использовать имя параметра явным образом (`{Binding Path=EmployeeID}`) или можете указать его в качестве первого параметра без имени (`{Binding EmployeeID}`).

Тип [**Path**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path) — это путь к свойству, являющийся строкой, которая оценивает свойство или подсвойство вашего пользовательского типа или типа инфраструктуры. Тип можно может быть [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject) (необязательно). Этапы в пути к свойству разделены точками (.), и вы можете добавить несколько разделителей для прохождения по иерархии. Используйте разделительные точки независимо от языка программирования, используемого для реализации объекта, к которому осуществляется привязка.

Например, чтобы привязать пользовательский интерфейс к свойству объекта имени сотрудника, ваш путь к свойству может быть "Employee.FirstName". Если бы элемент управления элементами привязывался к свойству, содержащему подчиненных сотрудников, то путем свойства мог бы быть Employee.Dependents, а шаблон элемента управления элементами, отобразил бы элементы в Dependents.

Если источником данных выступает коллекция, то в пути свойства можно указывать элементы коллекции по их позиции или индексу. Например «команд\[0\]. Игроки», где литерала "\[\]" содержит «0», задающий первый элемент в коллекции.

При использовании привязки [**ElementName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.elementname) к существующему [**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject) частью пути свойства могут быть присоединенные свойства. Для устранения синтаксической неоднозначности присоединенного свойства, когда точка внутри его имени интерпретируется как разделитель шагов пути свойства, заключите назначенное имя присоединенного свойства в скобки. Пример: `(AutomationProperties.Name)`.

Промежуточный объект пути свойства хранится в виде объекта [**PropertyPath**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyPath) в представлении времени выполнения, но в большинстве сценариев не нужно работать с объектом **PropertyPath** в коде. Обычно вы можете указать нужные сведения о привязке в коде XAML.

Дополнительную информацию о синтаксисе строки для пути свойства, о путях свойств в областях анимации и о построении объекта [**PropertyPath**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.PropertyPath) см. в разделе [Синтаксис Property-path](property-path-syntax.md).

## <a name="properties-of-the-binding-class-that-can-be-set-with-binding"></a>Свойства класса привязок, которые можно задать с помощью {Binding}


**{Binding}** демонстрируется с помощью замещающего синтаксиса *bindingProperties* поскольку есть несколько свойств, доступных для чтения и записи [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding), которые можно задавать в данном случае использования расширения разметки. Свойства можно задавать в любом порядке с парами *propName*=*value*, разделенными запятыми. Для некоторых свойств требуются типы, не предусматривающие преобразования; им необходимы их собственные расширения разметки, вложенные в **{Binding}** .

| Свойство | Описание |
|----------|-------------|
| [**путь**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path) | См. раздел [Путь к свойству](#property-path) выше. |
| [**Преобразователь**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter) | Указывает объект преобразователя, вызываемый механизмом привязки. Преобразователь можно задать в разметке с помощью расширения разметки [{StaticResource}](staticresource-markup-extension.md), чтобы ссылаться на этот объект из словаря ресурсов. |
| [**ConverterLanguage**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterlanguage) | Указывает язык и региональные параметры, используемые преобразователем. (Если он установлен [ **преобразователь**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter).) Для культуры устанавливается в качестве идентификатора на основе стандартов. Подробнее: [**ConverterLanguage**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterlanguage) |
| [**converterParameter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterparameter) | Указывает параметр преобразователя, который можно использовать в логике преобразователя. (Если он установлен [ **преобразователь**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter).) Большинство преобразователи использовать простой логики, получения всех сведений, они должны из переданного значения для преобразования, и нет необходимости **ConverterParameter** значение. Параметр **ConverterParameter** используется для более сложных преобразователей, у которых больше одной условной логики и которым недостаточно информации, переданной в **ConverterParameter**. Вы также можете написать преобразователь, который использует нестроковые значения, но это используется редко. Подробнее см. в разделе "Примечания" статьи **ConverterParameter**. |
| [**elementName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.elementname) | Задает источник данных, ссылаясь на другой элемент в той же конструкции XAML, имеющий свойство **Name** или атрибут [x:Name attribute](x-name-attribute.md). Это часто применяется для общего доступа к связанным значениям или использования подсвойств одного элемента пользовательского интерфейса, чтобы передать определенное значение для другого элемента, например в шаблоне элемента управления XAML. |
| [**fallbackValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.fallbackvalue) | Задает значение, которое отображается, когда не удается разрешить источник или путь. |
| [**Режим**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.mode) | Указывает режим привязки, как одно из следующих значений: «OneTime», «OneWay» или «TwoWay». Эти строки соответствуют именам постоянных перечисления [**BindingMode**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindingMode). Значение по умолчанию — OneWay. Обратите внимание, что это поведение отличается от шаблона по умолчанию для параметра **{x:Bind}** , значением которого является "OneTime". | 
| [**RelativeSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.relativesource) | Указывает источник данных, описывая положение источника привязки относительно целевого объекта привязки. Это чаще всего используется в привязках в шаблонах элементов управления XAML. Настройка [расширения разметки {RelativeSource}](relativesource-markup-extension.md). |
| [**Источник**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.source) | Указывает источник данных объекта. В расширении разметки **Binding** свойство [**Source**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.source) требует ссылки на объект, например на расширение разметки [{StaticResource}](staticresource-markup-extension.md). Если это свойство не указано, то источник задается действующим контекстом данных. Обычно значение Source не указывается в отдельных привязках, но используется общий контекст **DataContext** для нескольких привязок. Дополнительные сведения см. в статье [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) или [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth). |
| [**TargetNullValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.targetnullvalue) | Задает значение, которое отображается, когда значение источника разрешается, но оно явно равно **null**. |
| [**UpdateSourceTrigger**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.updatesourcetrigger) | Задает время обновления источника привязки. Если значение не задано, по умолчанию используется **Default**. |

**Примечание**  при преобразовании разметки из **{x: Bind}** для **{Binding}** , то необходимо учитывать различия в значения по умолчанию для **режим** свойство.

[**Преобразователь**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter), [ **ConverterLanguage** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterlanguage) и **ConverterLanguage** связаны в сценарий преобразования значения или тип из источник привязки в тип или значение, совместимое с целевого свойства привязки. Более подробную информацию и примеры см. в разделе "Преобразования данных" статьи [Подробно о привязке данных](https://docs.microsoft.com/windows/uwp/data-binding/data-binding-in-depth).

> [!NOTE]
> Начиная с Windows 10 версии 1607, платформа XAML предоставляет встроенный преобразователь Boolean в Visibility. Преобразователь сопоставляет значение **true** значению перечисления **Visible**, а значение **false** значению **Collapsed**, поэтому можно осуществить привязку свойства Visibility к Boolean без создания преобразователя. Для использования встроенного преобразователя минимальная версия целевого пакета SDK вашего приложения должна быть 14393 или более поздней. Вы не сможете использовать преобразователь, если ваше приложение предназначено для более ранних версий Windows 10. Дополнительные сведения о целевых версиях см. в статье [Адаптивный к версии код](https://docs.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

[**Источник**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.source), [ **RelativeSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.relativesource), и [ **ElementName** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.elementname) указать источник привязки, чтобы они были взаимно исключают друг друга.

**Совет**  Если вам нужно указать в одиночную фигурную скобку для значения, например в [ **путь** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path) или [ **ConverterParameter** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterparameter), затем укажите перед ним обратную косую черту: `\{`. Также можно включить всю строку, содержащую скобки, которые нужно преобразовать, в дополнительный набор кавычек, например: `ConverterParameter='{Mix}'`.

## <a name="examples"></a>Примеры

```XML
<!-- binding a UI element to a view model -->    
<Page ... >
    <Page.DataContext>
        <local:BookstoreViewModel/>
    </Page.DataContext>

    <GridView ItemsSource="{Binding BookSkus}" SelectedItem="{Binding SelectedBookSku, Mode=TwoWay}" ... />
</Page>
```

```XML
<!-- binding a UI element to another UI element -->
<Page ... >
    <Page.Resources>
        <local:S2Formatter x:Key="GradeConverter"/>
    </Page.Resources>

    <Slider x:Name="sliderValueConverter" ... />
    <TextBox Text="{Binding Path=Value, ElementName=sliderValueConverter,
        Mode=OneWay,
        Converter={StaticResource GradeConverter}}"/>
</Page>
```

Во втором примере задает четыре разных [ **привязки** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding) свойства: [**ElementName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.elementname), [ **путь**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path), [ **режим** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.mode) и [ **преобразователя** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter). В этом случае явно показан **Path**, названный как свойство **Binding**. **Path** определяется как источник привязки данных, являющийся другим объектом того же дерева объектов среды выполнения, [**Slider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Slider) с именем `sliderValueConverter`.

Обратите внимание, что значение свойства [**Converter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter) использует другое расширение разметки, [расширение разметки StaticResource](staticresource-markup-extension.md), поэтому здесь показано использование двух вложенных расширений разметки. Первой оценивается внутренняя разметка, поэтому как только ресурс получен, привязка может использовать действительный [**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter) (пользовательский класс, созданный элементом `local:S2Formatter` в ресурсах).

## <a name="tools-support"></a>Поддержка средств

Компонент Microsoft IntelliSense в Microsoft Visual Studio отображает свойства контекста данных при создании **{Binding}** в редакторе разметки XAML. Как только вы введете "{Binding", в раскрывающемся списке отобразятся свойства контекста данных, соответствующие [**Path**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path). IntelliSense также помогает с другими свойствами [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding). Для того, чтобы это работало, необходимо иметь или контекст данных, или контекст данных времени разработки, установленные на странице разметки. Команда **Перейти к определению** (клавиша F12) также работает с **{Binding}** . Кроме того, вы можете использовать диалоговое окно привязки данных.

 
