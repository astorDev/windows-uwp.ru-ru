---
Description: Numberbox — это элемент управления, который можно использовать для отображения и редактирования чисел.
title: Поле ввода номера телефона
template: detail.hbs
ms.date: 11/27/2019
ms.topic: article
keywords: windows 10, uwp
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: a74d98298f2c51059d8293022817e91ba573cf45
ms.sourcegitcommit: 0dee502484df798a0595ac1fe7fb7d0f5a982821
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82970699"
---
# <a name="number-box"></a>Поле ввода номера телефона

Представляет элемент управления, который можно использовать для отображения и редактирования чисел. Он поддерживает верификацию, пошаговое добавление и последовательные вычисления основных уравнений, таких как умножение, деление, сложение и вычитание.

**Получение библиотеки пользовательского интерфейса Windows**

|  |  |
| - | - |
| ![Логотип WinUI](images/winui-logo-64x64.png) | Элемент управления **NumberBox** требует использовать библиотеку пользовательского интерфейса Windows — пакет NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений для Windows. Дополнительные сведения, включая инструкции по установке, см. в [обзорной статье о библиотеке пользовательского интерфейса Windows](https://docs.microsoft.com/uwp/toolkits/winui/). |

**API-интерфейсы библиотеки пользовательского интерфейса Windows:** [Класс NumberBox](https://docs.microsoft.com/uwp/api/microsoft.ui.xaml.controls.NumberBox)

> [!TIP]
> В рамках этого документа в XAML мы используем псевдоним **muxc** для всех API библиотеки пользовательского интерфейса Windows, которую мы добавили в проект. Мы добавили его для нашего элемента [Page](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page): `xmlns:muxc="using:Microsoft.UI.Xaml.Controls"`.
>
>В коде C# мы также используем псевдоним **muxc** для всех API библиотеки пользовательского интерфейса Windows, которую мы добавили в проект. Мы добавили оператор **using** в верхней части файла: `using muxc = Microsoft.UI.Xaml.Controls;`.

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Элемент управления NumberBox можно использовать, чтобы получать и отображать математические входные данные. Если вам нужно редактируемое текстовое поле, которое принимает больше цифр, используйте элемент управления [TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox). Если вам нужно редактируемое текстовое поле, которое принимает пароли или другие важные данные, см. [PasswordBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox). Если вам нужно текстовое поле для ввода поисковых запросов, см. [AutoSuggestBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.autosuggestbox). Если вам нужно ввести или отредактировать отформатированный текст, см. [RichEditBox](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.richeditbox).

## <a name="examples"></a>Примеры

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас есть установленное приложение <strong style="font-weight: semi-bold">XAML Controls Gallery</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/TextBox">открыть приложение и увидеть NumberBox в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

### <a name="create-a-simple-numberbox"></a>Создание простого NumberBox

Вот XAML для базового NumberBox, который демонстрирует внешний вид по умолчанию. Используйте [x:Bind](/windows/uwp/xaml-platform/x-bind-markup-extension#property-path), чтобы обеспечить синхронизацию отображаемых пользователю данных с данными, хранящимися в вашем приложении.

```xaml
<muxc:NumberBox Value="{x:Bind Path=ViewModel.NumberBoxValue, Mode=TwoWay}" />
```

![Фокусное поле ввода, показывающее 0.](images/numberbox-basic.png)

### <a name="labeling-numberbox"></a>Добавление меток NumberBox

Используйте `Header` или `PlaceholderText`, если цель NumberBox не ясна. `Header` отображается независимо от того, имеет ли NumberBox значение.

```xaml
<muxc:NumberBox Header="Enter a number:"
    Value="{x:Bind Path=ViewModel.NumberBoxValue, Mode=TwoWay}" />
```

![Заголовок "Ввести выражение:" над полем NumberBox.](images/numberbox-header.png)

`PlaceholderText` отображается в поле NumberBox и появляется, только когда `Value` установлен на "не число" или когда входные данные очищены пользователем.

```xaml
<muxc:NumberBox PlaceholderText="1+2^2"
    Value="{x:Bind Path=ViewModel.NumberBoxValue, Mode=TwoWay}" />
```

![Окно NumberBox, содержащее текст заполнителя, читающего "A + B".](images/numberbox-placeholder-text.png)

### <a name="enable-calculation-support"></a>Включение поддержки расчетов

Установка свойства `AcceptsExpression` на значение true позволяет NumberBox оценивать основные линейные выражения, такие как умножение, деление, сложение и вычитание, используя стандартный порядок операций. Вычисление срабатывает при потере фокуса или при нажатии клавиши "Ввод". После вычисления выражения его первоначальная форма не сохраняется.

```xaml
<muxc:NumberBox Value="{x:Bind Path=ViewModel.NumberBoxValue, Mode=TwoWay}"
    AcceptsExpression="True" />
```

### <a name="increment-and-decrement-stepping"></a>Шаги увеличения и уменьшения

Используйте свойство `SmallChange`, чтобы настроить степень изменения значения внутри NumberBox, когда NumberBox находится в фокусе и пользователь выполняет одно из следующих действий:

- прокручивает;
- нажимает клавишу со стрелкой вверх;
- нажимает клавишу со стрелкой вниз.

Используйте свойство `LargeChange`, чтобы настроить степень изменения значений внутри NumberBox, когда NumberBox находится в фокусе и пользователь нажимает клавишу PageUp или PageDown.

Используйте свойство `SpinButtonPlacementMode`, чтобы включить кнопки, которые можно нажать для увеличения или уменьшения значения в поле NumberBox на сумму, указанную в свойстве `SmallChange`. Эти кнопки будут отключены при превышении значений "Максимум" и "Минимум" другим шагом.

Задайте для параметра `SpinButtonPlacementMode` значение `Inline`, чтобы включить отображение кнопок рядом с элементом управления.

```xaml
<muxc:NumberBox Value="{x:Bind Path=ViewModel.NumberBoxValue, Mode=TwoWay}"
    SmallChange="10"
    LargeChange="100"
    SpinButtonPlacementMode="Inline" />
```

![NumberBox с кнопкой со стрелкой вниз и кнопкой со стрелкой вверх рядом с ней.](images/numberbox-spinbutton-inline.png)

Задайте для параметра `SpinButtonPlacementMode` значение `Compact`, чтобы кнопки отображались в виде всплывающего окна, только если NumberBox находится в фокусе.

```xaml
<muxc:NumberBox Value="{x:Bind Path=ViewModel.NumberBoxValue, Mode=TwoWay}"
    SmallChange="10"
    LargeChange="100"
    SpinButtonPlacementMode="Compact" />
```

![NumberBox с небольшим значком внутри, который показывает стрелку, указывающую вверх, и стрелку, указывающую вниз.](images/numberbox-spinbutton-compact-non-visible.png)

![NumberBox с кнопкой со стрелкой вниз и кнопкой со стрелкой вверх, уплывающей в сторону на верхнем слое.](images/numberbox-spinbutton-compact-visible.png)

### <a name="enabling-input-validation"></a>Включение проверки входных данных

Настройка `ValidationMode` на `InvalidInputOverwritten` позволит NumberBox перезаписать недействительный ввод, который не является ни числовой, ни формальной формулой с последним действительным значением, когда при потере фокуса или нажатии клавиши "Ввод" активируется функция вычисления.

```xaml
<muxc:NumberBox Header="Quantity"
    Value="{x:Bind Path=ViewModel.NumberBoxValue, Mode=TwoWay}"
    ValidationMode="InvalidInputOverwritten" />
```

Установив `ValidationMode` на `Disabled`, можно настроить пользовательскую проверку входных данных.

В отношении десятичных точек и запятых используемое пользователем форматирование будет заменено форматированием, настроенным для NumberBox. Ошибка проверки ввода не будет активирована.

### <a name="formatting-input"></a>Форматирование входных данных

[Форматирование чисел](/uwp/api/windows.globalization.numberformatting) можно использовать для форматирования значения Numberbox, настроив экземпляр класса форматирования и назначив его свойству `NumberFormatter`. Десятичные, валютные, процентные и значащие разряды — лишь немногие из доступных классов форматирования чисел. Обратите внимание, что свойствами форматирования чисел также определяется округление.

Ниже приведен пример использования DecimalFormatter для форматирования значения NumberBox с одним целым числом, двумя числами дробной части и округлением до ближайшего 0,25:

```xaml
<muxc:NumberBox  x:Name="FormattedNumberBox"
    Value="{x:Bind Path=ViewModel.NumberBoxValue, Mode=TwoWay}" />
```

```csharp
private void SetNumberBoxNumberFormatter()
{
    IncrementNumberRounder rounder = new IncrementNumberRounder();
    rounder.Increment = 0.25;
    rounder.RoundingAlgorithm = RoundingAlgorithm.RoundUp;

    DecimalFormatter formatter = new DecimalFormatter();
    formatter.IntegerDigits = 1;
    formatter.FractionDigits = 2;
    formatter.NumberRounder = rounder;
    FormattedNumberBox.NumberFormatter = formatter;
}
```

![NumberBox отображает значение 0,00.](images/numberbox-formatted.png)

В отношении десятичных точек и запятых используемое пользователем форматирование будет заменено форматированием, настроенным для NumberBox. Ошибка проверки ввода не будет активирована.

## <a name="remarks"></a>Замечания

### <a name="input-scope"></a>Область ввода

`Number` будет использоваться для [области ввода](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue). Эта область ввода предназначена для работы с числами 0–9. Это можно изменить, однако альтернативные типы InputScope при этом не будут поддерживаться.

### <a name="not-a-number"></a>Не число

Если поле NumberBox очищено от входных данных, `Value` будет установлено на `NaN`, что означает отсутствие цифрового значения.

### <a name="expression-evaluation"></a>Вычисление выражения

Для вычисления выражений NumberBox использует инфиксную запись. В порядке приоритета допустимыми операторами являются:

* ^
* */
* +-

Обратите внимание, что для переопределения правил приоритета можно использовать круглые скобки.

## <a name="recommendations"></a>Рекомендации

* `Text` и `Value` упрощают захват значения NumberBox в виде строки или двойной, исключая необходимость конвертирования значений между типами. Программное изменение значения NumberBox рекомендуется делать через свойство `Value`. `Value` перезапишет `Text` в начальной настройке. После первоначальной настройки изменения, внесенные в одно значение, будут перенесены на другое, но согласованное выполнение программных изменений с помощью `Value` поможет избежать концептуальной двусмысленности, когда NumberBox будет принимать нечисловые символы с помощью `Text`.
* Используйте `Header` или `PlaceholderText`, чтобы информировать пользователей о том, что NumberBox принимает в качестве входных данных только числовые символы. Представление чисел по буквам, например "один", не будет допустимым значением.
