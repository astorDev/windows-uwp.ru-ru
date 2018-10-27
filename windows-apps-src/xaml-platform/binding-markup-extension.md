---
author: jwmsft
description: Расширение разметки Binding преобразовывается во время загрузки XAML в экземпляр класса Binding.
title: Расширение разметки Binding'
ms.assetid: 3BAFE7B5-AF33-487F-9AD5-BEAFD65D04C3
ms.author: jimwalk
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 69d316ad48645d8995e602b270a5615322c8b43f
ms.sourcegitcommit: 086001cffaf436e6e4324761d59bcc5e598c15ea
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "5685391"
---
# <a name="binding-markup-extension"></a>Расширение разметки {Binding}


**Примечание**новый механизм привязки доступен для Windows10, который оптимизирован для повышения быстродействия и производительности и разработчика. См. раздел [Расширение разметки: {x:Bind}](x-bind-markup-extension.md).

**Примечание**Общие сведения об использовании данных привязки в вашем приложении с **{Binding}** (и комплексное сравнение между **{x: Bind}** и **{Binding}**) см. в разделе [подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946).

Расширение разметки **{Binding}** используются для привязки свойств в элементах управления к значениям от источника данных, например кода. Расширение разметки **{Binding}** преобразовывается во время загрузки XAML в экземпляр класса [**Binding**](https://msdn.microsoft.com/library/windows/apps/br209820). Этот объект привязки получает значение из свойства в источнике данных и передает его в свойство в элементе управления. Объект привязки можно дополнительно настроить таким образом, чтобы он регистрировал изменения значений свойства источника данных и сам обновлялся на основании этих данных. Кроме того, его можно настроить, чтобы он отправлял изменения значения элемента управления назад к свойству источника. Свойство, являющееся целью привязки данных, должно быть свойством зависимостей. Подробнее см. в разделе [Общие сведения о свойствах зависимостей](dependency-properties-overview.md).

**{Binding}** имеет одинаковый с локальным значением приоритет свойства зависимостей, и настройка локального значения в императивном коде удаляет эффект любой привязки **{Binding}**, настроенной в разметке.

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
| *bindingProperties* | *propName*=*value*\[, *propName*=*value*\]*<br/>Одна или несколько привязок свойств, указанных с помощью синтаксиса пары "имя/значение". |
| *propName* | Имя строки свойства для установки в объекте [**Binding**](https://msdn.microsoft.com/library/windows/apps/br209820). Например, "Converter". |
| *value* | Значение, которое следует задать для свойства. Синтаксис аргумента зависит от свойства раздела [Свойства класса Binding, которые можно задать в {Binding}](#properties-of-the-binding-class-that-can-be-set-with-binding) ниже |

## <a name="property-path"></a>Путь свойства

[**Path**](https://msdn.microsoft.com/library/windows/apps/br209830) описывает свойство, к которому осуществляется привязка (исходное свойство). Path — это позиционный параметр, то есть вы можете использовать имя параметра явным образом (`{Binding Path=EmployeeID}`) или можете указать его в качестве первого параметра без имени (`{Binding EmployeeID}`).

Тип [**Path**](https://msdn.microsoft.com/library/windows/apps/br209830) — это путь к свойству, являющийся строкой, которая оценивает свойство или подсвойство вашего пользовательского типа или типа инфраструктуры. Тип можно может быть [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/br242356) (необязательно). Этапы в пути к свойству разделены точками (.), и вы можете добавить несколько разделителей для прохождения по иерархии. Используйте разделительные точки независимо от языка программирования, используемого для реализации объекта, к которому осуществляется привязка.

Например, чтобы привязать пользовательский интерфейс к свойству объекта имени сотрудника, ваш путь к свойству может быть "Employee.FirstName". Если бы элемент управления элементами привязывался к свойству, содержащему подчиненных сотрудников, то путем свойства мог бы быть "Employee.Dependents", а шаблон элемента управления элементами, отобразил бы элементы в "Dependents".

Если источником данных выступает коллекция, то в пути свойства можно указывать элементы коллекции по их позиции или индексу. Пример: "Teams\[0\].Players". Здесь литерал "\[\]" заключает "0", указывающий первый элемент в коллекции.

При использовании привязки [**ElementName**](https://msdn.microsoft.com/library/windows/apps/br209828) к существующему [**DependencyObject**](https://msdn.microsoft.com/library/windows/apps/br242356) частью пути свойства могут быть присоединенные свойства. Для устранения синтаксической неоднозначности присоединенного свойства, когда точка внутри его имени интерпретируется как разделитель шагов пути свойства, заключите назначенное имя присоединенного свойства в скобки. Пример: `(AutomationProperties.Name)`.

Промежуточный объект пути свойства хранится в виде объекта [**PropertyPath**](https://msdn.microsoft.com/library/windows/apps/br244259) в представлении времени выполнения, но в большинстве сценариев не нужно работать с объектом **PropertyPath** в коде. Обычно вы можете указать нужные сведения о привязке в коде XAML.

Дополнительную информацию о синтаксисе строки для пути свойства, о путях свойств в областях анимации и о построении объекта [**PropertyPath**](https://msdn.microsoft.com/library/windows/apps/br244259) см. в разделе [Синтаксис Property-path](property-path-syntax.md).

## <a name="properties-of-the-binding-class-that-can-be-set-with-binding"></a>Свойства класса привязок, которые можно задать с помощью {Binding}


**{Binding}** демонстрируется с помощью замещающего синтаксиса *bindingProperties* поскольку есть несколько свойств, доступных для чтения и записи [**Binding**](https://msdn.microsoft.com/library/windows/apps/br209820), которые можно задавать в данном случае использования расширения разметки. Свойства можно задавать в любом порядке с парами *propName*=*value*, разделенными запятыми. Для некоторых свойств требуются типы, не предусматривающие преобразования; им необходимы их собственные расширения разметки, вложенные в **{Binding}**.

| Свойство | Описание |
|----------|-------------|
| [**Путь**](https://msdn.microsoft.com/library/windows/apps/br209830) | См. раздел [Путь к свойству](#property-path) выше. |
| [**Преобразователь**](https://msdn.microsoft.com/library/windows/apps/br209826) | Указывает объект преобразователя, вызываемый механизмом привязки. Преобразователь можно задать в разметке с помощью расширения разметки [{StaticResource}](staticresource-markup-extension.md), чтобы ссылаться на этот объект из словаря ресурсов. |
| [**ConverterLanguage**](https://msdn.microsoft.com/library/windows/apps/hh701880) | Указывает язык и региональные параметры, используемые преобразователем. (Если задается [**Converter**](https://msdn.microsoft.com/library/windows/apps/br209826).) Язык и региональные параметры задаются как стандартный идентификатор. Подробнее: [**ConverterLanguage**](https://msdn.microsoft.com/library/windows/apps/hh701880) |
| [**ConverterParameter**](https://msdn.microsoft.com/library/windows/apps/br209827) | Указывает параметр преобразователя, который можно использовать в логике преобразователя. (Если задается [**Converter**](https://msdn.microsoft.com/library/windows/apps/br209826).) Большая часть преобразователей использует простую логику для получения всей необходимой информации из переданного значения, и им не нужно значение **ConverterParameter**. Параметр **ConverterParameter** используется для более сложных преобразователей, у которых больше одной условной логики и которым недостаточно информации, переданной в **ConverterParameter**. Вы также можете написать преобразователь, который использует нестроковые значения, но это используется редко. Подробнее см. в разделе "Примечания" статьи **ConverterParameter**. |
| [**ElementName**](https://msdn.microsoft.com/library/windows/apps/br209828) | Задает источник данных, ссылаясь на другой элемент в той же конструкции XAML, имеющий свойство **Name** или атрибут [x:Name attribute](x-name-attribute.md). Это часто применяется для общего доступа к связанным значениям или использования подсвойств одного элемента пользовательского интерфейса, чтобы передать определенное значение для другого элемента, например в шаблоне элемента управления XAML. |
| [**FallbackValue**](https://msdn.microsoft.com/library/windows/apps/dn279345) | Задает значение, которое отображается, когда не удается разрешить источник или путь. |
| [**Режим**](https://msdn.microsoft.com/library/windows/apps/br209829) | Указывает режим привязки как одну из этих строк: OneTime, OneWay или TwoWay. Эти строки соответствуют именам постоянных перечисления [**BindingMode**](https://msdn.microsoft.com/library/windows/apps/br209822). Значение по умолчанию—OneWay. Обратите внимание, что это поведение отличается от шаблона по умолчанию для параметра **{x:Bind}**, значением которого является "OneTime". | 
| [**RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209831) | Указывает источник данных, описывая положение источника привязки относительно целевого объекта привязки. Это чаще всего используется в привязках в шаблонах элементов управления XAML. Настройка [расширения разметки {RelativeSource}](relativesource-markup-extension.md). |
| [**Источник**](https://msdn.microsoft.com/library/windows/apps/br209832) | Указывает источник данных объекта. В расширении разметки **Binding** свойство [**Source**](https://msdn.microsoft.com/library/windows/apps/br209832) требует ссылки на объект, например на расширение разметки [{StaticResource}](staticresource-markup-extension.md). Если это свойство не указано, то источник задается действующим контекстом данных. Обычно значение Source не указывается в отдельных привязках, но используется общий контекст **DataContext** для нескольких привязок. Дополнительные сведения см. в статье [**DataContext**](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.frameworkelement.datacontext.aspx) или [Подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946). |
| [**TargetNullValue**](https://msdn.microsoft.com/library/windows/apps/dn279347) | Задает значение, которое отображается, когда значение источника разрешается, но оно явно равно **null**. |
| [**UpdateSourceTrigger**](https://msdn.microsoft.com/library/windows/apps/dn279350) | Задает время обновления источника привязки. Если значение не задано, по умолчанию используется **Default**. |

**Примечание**при преобразовании разметки **{x: Bind}** для **{Binding}**, то следует учитывать различия в основной значения для свойства **режим** .

[**Converter**](https://msdn.microsoft.com/library/windows/apps/br209826), [**ConverterLanguage**](https://msdn.microsoft.com/library/windows/apps/hh701880) и **ConverterLanguage** связаны сценарием преобразования значения или типа из источника привязки в тип или значение, совместимые со свойством целевого объекта привязки. Более подробную информацию и примеры см. в разделе "Преобразования данных" статьи [Подробно о привязке данных](https://msdn.microsoft.com/library/windows/apps/mt210946).

> [!NOTE]
> Начиная с Windows 10 версии 1607, платформа XAML предоставляет встроенный преобразователь Boolean в Visibility. Преобразователь сопоставляет значение **true** значению перечисления **Visible**, а значение **false** значению **Collapsed**, поэтому можно осуществить привязку свойства Visibility к Boolean без создания преобразователя. Для использования встроенного преобразователя минимальная версия целевого пакета SDK вашего приложения должна быть 14393 или более поздней. Вы не сможете использовать преобразователь, если ваше приложение предназначено для более ранних версий Windows 10. Дополнительные сведения о целевых версиях см. в статье [Адаптивный к версии код](https://msdn.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

[**Source**](https://msdn.microsoft.com/library/windows/apps/br209832), [**RelativeSource**](https://msdn.microsoft.com/library/windows/apps/br209831) и [**ElementName**](https://msdn.microsoft.com/library/windows/apps/br209828) указывают источник привязки, поэтому они являются взаимоисключающими.

**Совет**Если вам необходимо указать одну фигурную скобку значения, например [**путь**](https://msdn.microsoft.com/library/windows/apps/br209830) или [**ConverterParameter**](https://msdn.microsoft.com/library/windows/apps/br209827), затем поставьте обратную косую черту: `\{`. Также можно включить всю строку, содержащую скобки, которые нужно преобразовать, в дополнительный набор кавычек, например: `ConverterParameter='{Mix}'`.

## <a name="examples"></a>Примеры.

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

Во втором примере устанавливается четыре разных свойства [**Binding**](https://msdn.microsoft.com/library/windows/apps/br209820): [**ElementName**](https://msdn.microsoft.com/library/windows/apps/br209828), [**Path**](https://msdn.microsoft.com/library/windows/apps/br209830), [**Mode**](https://msdn.microsoft.com/library/windows/apps/br209829) и [**Converter**](https://msdn.microsoft.com/library/windows/apps/br209826). В этом случае явно показан **Path**, названный как свойство **Binding**. **Path** определяется как источник привязки данных, являющийся другим объектом того же дерева объектов среды выполнения, [**Slider**](https://msdn.microsoft.com/library/windows/apps/br209614) с именем `sliderValueConverter`.

Обратите внимание, что значение свойства [**Converter**](https://msdn.microsoft.com/library/windows/apps/br209826) использует другое расширение разметки, [расширение разметки StaticResource](staticresource-markup-extension.md), поэтому здесь показано использование двух вложенных расширений разметки. Первой оценивается внутренняя разметка, поэтому как только ресурс получен, привязка может использовать действительный [**IValueConverter**](https://msdn.microsoft.com/library/windows/apps/br209903) (пользовательский класс, созданный элементом `local:S2Formatter` в ресурсах).

## <a name="tools-support"></a>Поддержка средств

Компонент Microsoft IntelliSense в Microsoft Visual Studio отображает свойства контекста данных при создании **{Binding}** в редакторе разметки XAML. Как только вы введете "{Binding", в раскрывающемся списке отобразятся свойства контекста данных, соответствующие [**Path**](https://msdn.microsoft.com/library/windows/apps/br209830). IntelliSense также помогает с другими свойствами [**Binding**](https://msdn.microsoft.com/library/windows/apps/br209820). Для того, чтобы это работало, необходимо иметь или контекст данных, или контекст данных времени разработки, установленные на странице разметки. Команда **Перейти к определению** (клавиша F12) также работает с **{Binding}**. Кроме того, вы можете использовать диалоговое окно привязки данных.

 
