---
Description: Чтобы упростить пользователям ввод данных с помощью сенсорной клавиатуры или панели функционального ввода, можно настроить тип вводимых данных элемента управления для ввода текста, чтобы он соответствовал типу данных, которые должен вводить пользователь.
MS-HAID: dev\_ctrl\_layout\_txt.use\_input\_scope\_to\_change\_the\_touch\_keyboard
MSHAttr: PreferredLib:/library/windows/apps
Search.Product: eADQiWindows 10XVcnh
title: Использование типа вводимых данных для изменения сенсорной клавиатуры
ms.assetid: 6E5F55D7-24D6-47CC-B457-B6231EDE2A71
template: detail.hbs
keywords: клавиатура, специальные возможности, навигация, фокус, текст, ввод, взаимодействие с пользователем
ms.date: 02/08/2017
ms.topic: article
ms.openlocfilehash: c522e21c45a3edd08a14b081cc227a83f19a3ea0
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66365357"
---
# <a name="use-input-scope-to-change-the-touch-keyboard"></a>Использование типа вводимых данных для изменения сенсорной клавиатуры

Чтобы упростить пользователям ввод данных с помощью сенсорной клавиатуры или панели функционального ввода, можно настроить тип вводимых данных элемента управления для ввода текста, чтобы он соответствовал типу данных, которые должен вводить пользователь.

### <a name="important-apis"></a>Важные API
- [InputScope](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.inputscope)
- [inputScopeNameValue](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue)


Сенсорная клавиатура позволяет вводить текст, если приложение выполняется на устройстве с сенсорным экраном. Сенсорная клавиатура появляется, когда пользователь касается редактируемого поля ввода, например, **[TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)** или **[RichEditBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox)** . Пользователи могут вводить данные в вашем приложении гораздо быстрее и проще, если настроить *тип вводимых данных* элемента управления текстом на соответствие типу данных, которые пользователь должен вводить. Тип вводимых данных предоставляет системе подсказку о типе текстового ввода, ожидаемого элементом управления. Благодаря этому система может отобразить специальную раскладку сенсорной клавиатуры в соответствии с используемым типом ввода.

Например, если текстовое поле используется только для ввода 4-значного ПИН-кода, установите для свойства [**InputScope**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.inputscope) значение **Number**. Это сообщает системе, что нужно отобразить раскладку цифровой клавиатуры, благодаря чему пользователю проще вводить PIN-код.

> [!IMPORTANT]
> - Эти сведения применимы только к панели функционального ввода. Они неприменимы к аппаратным клавиатурам или экранной клавиатуре, доступной в параметрах специальных возможностей Windows.
> - Тип вводимых данных не вызывает выполнение проверки ввода, а также не препятствует пользователю вводить любые данные через аппаратную клавиатуру или другое устройство ввода. При необходимости вы по-прежнему несете ответственность за проверку вводимых данных в коде.

## <a name="changing-the-input-scope-of-a-text-control"></a>Изменение типа вводимых данных для элемента управления текстом

Типы вводимых данных, доступные вашему приложению, представляют собой члены перечисления **[InputScopeNameValue](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue)** . Вы можете задать для свойства **InputScope** объекта **[TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)** или **[RichEditBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox)** одно из указанных ниже значений.

> [!IMPORTANT]
> **[InputScope](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.inputscope)** свойство **[PasswordBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox)** поддерживает только **пароль** и  **NumericPin** значения. Любое другое значение будет проигнорировано.

В этой процедуре мы изменим тип вводимых данных для нескольких текстовых полей в соответствии с данными, которые предполагается вводить.

**Чтобы изменить область ввода в XAML**

1.  В файле XAML для страницы найдите тег для элемента управления с текстом, который необходимо изменить.
2.  Добавьте атрибут [**InputScope**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.inputscope) к тегу и укажите значение [**InputScopeNameValue**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue), которое соответствует ожидаемым вводимым данным.

    Вот некоторые текстовые поля, которые могут отображаться в общей форме контакта с клиентом. При заданном [**InputScope**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.inputscope) для каждого текстового поля отображается сенсорная клавиатура с подходящей раскладкой для данных.

    ```xaml
    <StackPanel Width="300">
        <TextBox Header="Name" InputScope="Default"/>
        <TextBox Header="Email Address" InputScope="EmailSmtpAddress"/>
        <TextBox Header="Telephone Number" InputScope="TelephoneNumber"/>
        <TextBox Header="Web site" InputScope="Url"/>
    </StackPanel>
    ```

**Чтобы изменить область ввода в коде**

1.  В файле XAML для страницы найдите тег для элемента управления с текстом, который необходимо изменить. Если он не установлен, установите [x:Name attribute](https://docs.microsoft.com/windows/uwp/xaml-platform/x-name-attribute), таким образом вы сможете ссылаться на элемент управления в коде.

    ```csharp
    <TextBox Header="Telephone Number" x:Name="phoneNumberTextBox"/>
    ```

2.  Создайте экземпляр объекта [**InputScope**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScope).

    ```csharp
    InputScope scope = new InputScope();
    ```

3.  Создайте экземпляр объекта [**InputScopeName**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScopeName).
    
    ```csharp
    InputScopeName scopeName = new InputScopeName();
    ```

4.  Установите для свойства [**NameValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.inputscopename.namevalue) объекта [**InputScopeName**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScopeName) значение перечисления [**InputScopeNameValue**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScopeNameValue).

    ```csharp
    scopeName.NameValue = InputScopeNameValue.TelephoneNumber;
    ```

5.  Добавьте объект [**InputScopeName**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScopeName) в коллекцию [**Names**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.inputscope.names) объекта [**InputScope**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScope).

    ```csharp
    scope.Names.Add(scopeName);
    ```

6.  Задайте объект [**InputScope**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Input.InputScope) в качестве значения свойства элемента управления текстом [**InputScope**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.inputscope).

    ```csharp
    phoneNumberTextBox.InputScope = scope;
    ```

Ниже приведен код полностью.

```CSharp
InputScope scope = new InputScope();
InputScopeName scopeName = new InputScopeName();
scopeName.NameValue = InputScopeNameValue.TelephoneNumber;
scope.Names.Add(scopeName);
phoneNumberTextBox.InputScope = scope;
```

Эти же шаги можно сжать до следующего сокращенного кода.

```CSharp
phoneNumberTextBox.InputScope = new InputScope() 
{
    Names = {new InputScopeName(InputScopeNameValue.TelephoneNumber)}
};
```

## <a name="text-prediction-spell-checking-and-auto-correction"></a>Прогнозирование текста, проверка правописания и автозамена

Элементы управления [**TextBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox) и [**RichEditBox**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.RichEditBox) имеют несколько свойств, которые влияют на поведение панели функционального ввода. Чтобы обеспечить наилучшее взаимодействие с пользователями, важно понимать, как эти свойства влияют на ввод текста с помощью сенсорного ввода.

-   [**IsSpellCheckEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.isspellcheckenabled), если проверка орфографии включена для текстового элемента управления, элемент управления взаимодействует с ядро системы проверки орфографии для выделения слов, которые не распознаются. Можно коснуться слова, чтобы просмотреть список рекомендуемых исправлений. По умолчанию функция проверки правописания включена.

    Для типа вводимых данных **Default** это свойство также автоматически включает функции написания первого слова предложения с прописной буквы и автозамены слов по мере ввода. Для других типов вводимых данных эти функции автозамены можно выключить. Подробнее см. в таблицах далее в этом разделе.

-   [**IsTextPredictionEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.istextpredictionenabled)— при включении прогнозирование текста для текстового элемента управления, система выдает список слов, которые могут начинает введите. Вы можете выбрать слово из списка, чтобы не вводить слово целиком. По умолчанию функция прогнозирования текста включена.

    Прогнозирование текста можно отключить, если тип вводимых данных отличается от **Default**, даже если свойство [**IsTextPredictionEnabled**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.istextpredictionenabled) имеет значение **true**. Подробнее см. в таблицах далее в этом разделе.

-   [**PreventKeyboardDisplayOnProgrammaticFocus**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.preventkeyboarddisplayonprogrammaticfocus)— Если этому свойству присвоено **true**, она предотвращает отображение SIP, когда фокус установлен программно на текстового элемента управления. Вместо этого клавиатура отображается только тогда, когда пользователь взаимодействует с элементом управления.

## <a name="touch-keyboard-index-for-windows"></a>Индекс сенсорной клавиатуры для Windows

Эти таблицы Показать макеты Windows обратимо входной Panel (SIP) для часто встречающихся значений области ввода. Влияние типа вводимых данных на функции, включаемые свойствами **IsSpellCheckEnabled** и **IsTextPredictionEnabled**, указаны для каждого типа вводимых данных. Это неполный перечень доступных типов вводимых данных.

> [!Tip] 
> Большинство сенсорной клавиатуры между макетом буквы и цифры и символы макет можно переключать, нажав клавишу **& 123** ключ для изменения макета цифры и символы, и нажмите клавишу **abcd** ключа Измените макет является буквой алфавита.

### <a name="default"></a>Значение по умолчанию

`<TextBox InputScope="Default"/>`

Windows по умолчанию сенсорной клавиатуры.

![Сенсорная клавиатура Windows по умолчанию](images/input-scopes/default.png)
- Проверка правописания: включена при **IsSpellCheckEnabled** = **true**, отключена при **IsSpellCheckEnabled** = **false**
- Автозамена: включена при **IsSpellCheckEnabled** = **true**, отключена при **IsSpellCheckEnabled** = **false**
- Автоматическое применение заглавных букв: включено при **IsSpellCheckEnabled** = **true**, отключено при **IsSpellCheckEnabled** = **false**
- Прогнозирование текста: включено при **IsTextPredictionEnabled** = **true**, отключено при **IsTextPredictionEnabled** = **false**

### <a name="currencyamountandsymbol"></a>CurrencyAmountAndSymbol

`<TextBox InputScope="CurrencyAmountAndSymbol"/>`

Раскладка клавиатуры с цифрами и символами по умолчанию.

![Сенсорная клавиатура Windows для валют](images/input-scopes/currencyamountandsymbol.png)

- Включает в себя ключи страницы влево и вправо, чтобы показать дополнительные символы
- Проверка правописания: по умолчанию включена, может быть отключена
- Автозамена: по умолчанию включена, может быть отключена
- Автоматическое применение заглавных букв: всегда отключено
- Прогнозирование текста: по умолчанию включено, может быть отключено
 
### <a name="url"></a>url

`<TextBox InputScope="Url"/>`

![Сенсорная клавиатура Windows для URL-адресов](images/input-scopes/url.png)

- Содержит клавиши **.com** и ![go](images/input-scopes/kbdgokey.png) (Перейти). Нажмите и удерживайте **.com** ключ, чтобы отобразить дополнительные параметры ( **.org**, **.net**, суффикс региона и)
- Включает в себя **:** , **-** , и **/** ключи
- Проверка правописания: по умолчанию отключена, может быть включена
- Автозамена: по умолчанию отключена, может быть включена
- Автоматическое применение заглавных букв: по умолчанию отключено, может быть включено
- Прогнозирование текста: по умолчанию отключено, может быть включено


### <a name="emailsmtpaddress"></a>EmailSmtpAddress

`<TextBox InputScope="EmailSmtpAddress"/>`

![Сенсорная клавиатура Windows для ввода адресов электронной почты](images/input-scopes/emailsmtpaddress.png)
- Содержит клавиши **@** и **.com**. Нажмите и удерживайте **.com** ключ, чтобы отобразить дополнительные параметры ( **.org**, **.net**, суффикс региона и)
- Включает в себя **_** и **-** ключи
- Проверка правописания: по умолчанию отключена, может быть включена
- Автозамена: по умолчанию отключена, может быть включена
- Автоматическое применение заглавных букв: по умолчанию отключено, может быть включено
- Прогнозирование текста: по умолчанию отключено, может быть включено


### <a name="number"></a>Number

`<TextBox InputScope="Number"/>`

![Сенсорная клавиатура Windows для ввода чисел](images/input-scopes/number.png)
- Проверка правописания: по умолчанию включена, может быть отключена
- Автозамена: по умолчанию включена, может быть отключена
- Автоматическое применение заглавных букв: всегда отключено
- Прогнозирование текста: по умолчанию включено, может быть отключено

### <a name="telephonenumber"></a>TelephoneNumber (номер телефона)

`<TextBox InputScope="TelephoneNumber"/>`

![Сенсорная клавиатура Windows для ввода номеров телефонов](images/input-scopes/telephonenumber.png)
- Проверка правописания: по умолчанию включена, может быть отключена
- Автозамена: по умолчанию включена, может быть отключена
- Автоматическое применение заглавных букв: всегда отключено
- Прогнозирование текста: по умолчанию включено, может быть отключено

### <a name="search"></a>Поиск

`<TextBox InputScope="Search"/>`

![Сенсорная клавиатура Windows для поиска](images/input-scopes/search.png)
- Включает в себя **поиска** ключей вместо **ввод** ключ
- Проверка правописания: по умолчанию включена, может быть отключена
- Автозамена: по умолчанию включена, может быть отключена
- Автоматическое применение заглавных букв: всегда отключено
- Прогнозирование текста: по умолчанию включено, может быть отключено

### <a name="searchincremental"></a>SearchIncremental

`<TextBox InputScope="SearchIncremental"/>`

![Windows сенсорной клавиатуры для последовательного поиска](images/input-scopes/searchincremental.png)
- Же макет, что **по умолчанию**
- Проверка правописания: по умолчанию отключена, может быть включена
- Автозамена: всегда отключена
- Автоматическое применение заглавных букв: всегда отключено
- Прогнозирование текста: всегда отключено

### <a name="formula"></a>Формулы

`<TextBox InputScope="Formula"/>`

![Windows сенсорной клавиатуры для формулы](images/input-scopes/formula.png)
- Включает в себя **=** ключ
- Также включает в себя **%** , **$** , и **+** ключи
- Проверка правописания: по умолчанию включена, может быть отключена
- Автозамена: по умолчанию включена, может быть отключена
- Автоматическое применение заглавных букв: всегда отключено
- Прогнозирование текста: по умолчанию включено, может быть отключено

### <a name="chat"></a>Chat (чат)

`<TextBox InputScope="Chat"/>`

![Сенсорная клавиатура Windows по умолчанию](images/input-scopes/default.png)
- Же макет, что **по умолчанию**
- Проверка правописания: по умолчанию включена, может быть отключена
- Автозамена: по умолчанию включена, может быть отключена
- Автоматическое применение заглавных букв: по умолчанию включено, может быть отключено
- Прогнозирование текста: по умолчанию включено, может быть отключено

### <a name="nameorphonenumber"></a>NameOrPhoneNumber

`<TextBox InputScope="NameOrPhoneNumber"/>`

![Сенсорная клавиатура Windows по умолчанию](images/input-scopes/default.png)
- Же макет, что **по умолчанию**
- Проверка правописания: по умолчанию отключена, может быть включена
- Автозамена: по умолчанию отключена, может быть включена
- Автоматически: off по умолчанию, можно включить (первая буква каждого слова прописными буквами)
- Прогнозирование текста: по умолчанию отключено, может быть включено
