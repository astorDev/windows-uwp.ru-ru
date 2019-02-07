---
description: Расширение разметки xBind позволяет функции для использования в разметке.
title: Функции в x:Bind
ms.date: 02/06/2019
ms.topic: article
keywords: Windows 10, uwp, xBind
ms.localizationpriority: medium
ms.openlocfilehash: b85777c254c36cc7bf5b156569c7cef267a6c567
ms.sourcegitcommit: b79cc7e0eac414ac2275517a7f56d1f9a817d112
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "9060038"
---
# <a name="functions-in-xbind"></a>Функции в x:Bind

> [!NOTE]
> Общие сведения об использовании привязки данных в приложении с помощью **{x: Bind}** (и комплексное сравнение между **{x: Bind}** и **{Binding}**) см. в разделе [подробно о привязке данных](data-binding-in-depth.md).

Начиная с Windows 10 версии 1607 **{x: Bind}** поддерживает использование функции на конечном этапе шаге пути привязки. Это позволяет:

- Более простой способ преобразования значения
- Привязки могут зависеть от нескольких параметров

> [!NOTE]
> Чтобы использовать функции с **{x: Bind}**, минимальная версия целевого пакета SDK вашего приложения должна быть 14393 или более поздней. Если ваше приложение предназначено для более ранних версий Windows 10, вы не сможете использовать функции. Дополнительные сведения о целевых версиях см. в статье [Адаптивный к версии код](https://msdn.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code).

В следующем примере фон и передний план элемента привязаны к функциям для выполнения преобразования на основе параметра цвета

```xaml
<DataTemplate x:DataType="local:ColorEntry">
    <Grid Background="{x:Bind local:ColorEntry.Brushify(Color), Mode=OneWay}" Width="240">
        <TextBlock Text="{x:Bind ColorName}" Foreground="{x:Bind TextColor(Color)}" Margin="10,5" />
    </Grid>
</DataTemplate>
```

```csharp
class ColorEntry
{
    public string ColorName { get; set; }
    public Color Color { get; set; }

    public static SolidColorBrush Brushify(Color c)
    {
        return new SolidColorBrush(c);
    }

    public SolidColorBrush TextColor(Color c)
    {
        return new SolidColorBrush(((c.R * 0.299 + c.G * 0.587 + c.B * 0.114) > 150) ? Colors.Black : Colors.White);
    }
}
```

## <a name="xaml-attribute-usage"></a>Использование атрибутов XAML

```xaml
<object property="{x:Bind pathToFunction.FunctionName(functionParameter1, functionParameter2, ...), bindingProperties}" ... />
```

## <a name="path-to-the-function"></a>Путь к функции

Путь к функции определяется аналогично другим путям свойств и может содержать точки (.), индексаторы или приведения.

Статические функции можно указывать с помощью синтаксиса XMLNamespace:ClassName.MethodName. Например, используйте ниже синтаксис для привязки к статические функции в коде.

```xaml
<Page 
     xmlns:local="using:MyNamespace">
     ...
    <StackPanel>
        <TextBlock x:Name="BigTextBlock" FontSize="20" Text="Big text" />
        <TextBlock FontSize="{x:Bind local:MyHelpers.Half(BigTextBlock.FontSize)}" 
                   Text="Small text" />
    </StackPanel>
</Page>
```

```csharp
namespace MyNamespace
{
    static public class MyHelpers
    {
        public static double Half(double value) => value / 2.0;
    }
}
```

Можно также использовать системные функции непосредственно в разметке для выполнения простых сценариев, таких как форматирование дат форматирование текста, сцепление текста, и пр., например:

```xaml
<Page 
     xmlns:sys="using:System"
     xmlns:local="using:MyNamespace">
     ...
     <CalendarDatePicker Date="{x:Bind sys:DateTime.Parse(TextBlock1.Text)}" />
     <TextBlock Text="{x:Bind sys:String.Format('{0} is now available in {1}', local:MyPage.personName, local:MyPage.location)}" />
</Page>
```

Если выбран режим привязки OneWay или TwoWay, будет произведено отслеживание изменений пути функции, и привязка будет обработана заново при наличии изменений в данных объектах.

Требования к функции, к которой осуществляется привязка:

- Доступность для кода и метаданных. В С# будут работать внутренние и частные методы, однако в C++/CX необходимо использовать открытые методы WinRT
- Перегрузка основана на количестве, а не типе аргументов и будет пытаться попытается соответствовать первой перегрузке с указанным количеством аргументов
- Типы аргументов должны соответствовать передаваемым данным — сужающие преобразования не производятся
- Возвращаемый тип функции должен соответствовать типу свойства, использующего привязку

Модуль привязки реагирует на изменение свойства уведомления активируется с помощью имени функции и повторно оценивать привязки при необходимости. Пример.

```xaml
<DataTemplate x:DataType="local:Person">
   <StackPanel>
      <TextBlock Text="{x:Bind FullName}" />
      <Image Source="{x:Bind IconToBitmap(Icon, CancellationToken), Mode=OneWay}" />
   </StackPanel>
</DataTemplate>
```

```csharp
public class Person:INotifyPropertyChanged
{
    //Implementation for an Icon property and a CancellationToken property with PropertyChanged notifications
    ...

    //IconToBitmap function is essentially a multi binding converter between several options.
    public Uri IconToBitmap (Uri icon, Uri cancellationToken)
    {
        Uri foo = new Uri(...);        
        if (isCancelled)
        {
            foo = cancellationToken;
        }
        else 
        {
            if (this.fullName.Contains("Sr"))
            {
               //pass a different Uri back
               foo = new Uri(...);
            }
            else
            {
                foo = icon;
            }
        }
        return foo;
    }

    //Ensure FullName property handles change notification on itself as well as IconToBitmap since the function uses it
    public string FullName
    {
        get { return this.fullName; }
        set
        {
            this.fullName = value;
            this.OnPropertyChanged ();
            this.OnPropertyChanged ("IconToBitmap"); 
            //this ensures Image.Source binding re-evaluates when FullName changes in addition to Icon and CancellationToken
        }
    }
}
```

> [!TIP]
> Можно использовать функции в расширении разметки x: Bind для достижения те же сценарии, как что поддерживался через конвертеры и связывание с несколькими источниками в WPF.

## <a name="function-arguments"></a>Аргументы функции

Можно указать несколько аргументов функции, разделенных запятой (,)

- Синтаксис пути привязки аналогичен синтаксису привязки непосредственно к объекту.
  - Если выбран режим привязки OneWay или TwoWay, будет произведено отслеживание изменений, и привязка будет обработана заново при изменении объектов
- Строковые константы заключаются в кавычки. Они необходимы для обозначения строки. Для экранирования кавычек в строках можно использовать символ карет (^)
- Числовые константы, например 123.456
- Логические константы — обозначаются как "x:True" или "x:False"

### <a name="two-way-function-bindings"></a>Двухсторонняя привязка функции

При двусторонней привязке следует определить вторую функцию для обратного направления привязки. Это делается с помощью свойства **BindBack** привязки. В следующем примере функция должна принимать один аргумент, представляющий собой значение, которое должно быть возвращено модели.

```xaml
<TextBlock Text="{x:Bind a.MyFunc(b), BindBack=a.MyFunc2, Mode=TwoWay}" />
```
