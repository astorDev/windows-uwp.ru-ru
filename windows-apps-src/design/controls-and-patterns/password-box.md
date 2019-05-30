---
Description: Поле ввода пароля — это текстовое поле, в котором вводимые символы скрыты в целях безопасности.
title: Руководство по полям паролей
ms.assetid: 332B04D6-4FFE-42A4-8B3D-ABE8266C7C18
dev.assetid: 4BFDECC6-9BC5-4FF5-8C63-BB36F6DDF2EF
label: Password box
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: miguelrb
design-contact: ksulliv
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: d41590914fb66cb8061298f77f24e5efcdd03bf5
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66364629"
---
# <a name="password-box"></a>Поле ввода пароля

 

Поле ввода пароля — это текстовое поле, в котором вводимые символы скрыты в целях безопасности. Поле ввода пароля внешне похоже на текстовое поле с той разницей, что в нем вместо вводимого текста отображаются подстановочные символы. Вы можете выбрать необходимый знак заполнителя.

> **Важные API**: [Класс PasswordBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox), [свойство пароля](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.password), [PasswordChar-свойство](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.passwordchar), [свойство PasswordRevealMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.passwordrevealmode), [PasswordChanged событий](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.passwordchanged)

По умолчанию поле ввода пароля предоставляет пользователю возможность просмотреть свой пароль, удерживая кнопку показа. Можно отключить кнопку показа либо предоставить альтернативный механизм отображения пароля, например флажок.

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Элемент управления **PasswordBox** может использоваться для сбора паролей или других конфиденциальных данных, например номеров карт социального страхования.

Дополнительные сведения о выборе подходящего элемента управления текстом можно найти в статье [Элементы управления текстом](text-controls.md).

## <a name="examples"></a>Примеры

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/PasswordBox">открыть приложение и увидеть PasswordBox в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Получить приложение XAML Controls Gallery (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

Поле ввода пароля может иметь несколько состояний, включая следующие состояния, которые следует отметить.

Поле ввода пароля в состоянии покоя может отображать текст подсказки, чтобы пользователь знал о предназначении этого поля:

![Поле ввода пароля в состоянии покоя с текстом подсказки](images/passwordbox-rest-hinttext.png)

Когда пользователь печатает в поле ввода пароля, поведением по умолчанию является отображение маркеров, за которыми скрывается вводимый текст:

![Состояние фокуса поля ввода пароля при вводе текста](images/passwordbox-focus-typing.png)

Нажатие кнопки «Показать» справа позволяет посмотреть вводимый текст пароля:

![Отображается текст, вводимый в поле пароля](images/passwordbox-text-reveal.png)

## <a name="create-a-password-box"></a>Создание поля ввода пароля

Используйте свойство [Password](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.password), чтобы получить или задать содержимое элемента PasswordBox. Это можно сделать в обработчике для события [PasswordChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.passwordchanged), чтобы выполнять проверку, пока пользователь вводит пароль. Или можно использовать другое событие, например кнопку [Щелкните](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click), чтобы выполнять проверку после того, как пользователь завершит ввод текста.

Ниже приведен фрагмент кода XAML для поля ввода пароля, демонстрирующий внешний вид элемента PasswordBox по умолчанию. При вводе пароля программа проверяет, не вводит ли пользователь буквально слово Password (Пароль). Если это так, то на экране будет отображено соответствующее сообщение.

```xaml
<StackPanel>  
  <PasswordBox x:Name="passwordBox" Width="200" MaxLength="16"
             PasswordChanged="passwordBox_PasswordChanged"/>

  <TextBlock x:Name="statusText" Margin="10" HorizontalAlignment="Center" />
</StackPanel>   
```

```csharp
private void passwordBox_PasswordChanged(object sender, RoutedEventArgs e)
{
    if (passwordBox.Password == "Password")
    {
        statusText.Text = "'Password' is not allowed as a password.";
    }
    else
    {
        statusText.Text = string.Empty;
    }
}
```
Ниже показан результат выполнения этого кода, если в качестве пароля пользователь вводит слово Password (Пароль).

![Поле ввода пароля с сообщением проверки](images/passwordbox-revealed-validation.png)

### <a name="password-character"></a>Знак пароля

Знак, используемый для маскировки пароля, можно изменить, задав свойство [PasswordChar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.passwordchar). Здесь маркер по умолчанию заменяется звездочкой.

```xaml
<PasswordBox x:Name="passwordBox" Width="200" PasswordChar="*"/>
```

Результат выглядит так:

![Поле ввода пароля с настраиваемым знаком](images/passwordbox-custom-char.png)

### <a name="headers-and-placeholder-text"></a>Заголовки и подстановочный текст

Свойства [Header](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.header) и [PlaceholderText](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.placeholdertext) можно использовать, чтобы предоставить контекст для элемента PasswordBox. Это особенно полезно при наличии нескольких полей, например в форме для изменения пароля.

```xaml
<PasswordBox x:Name="passwordBox" Width="200" Header="Password" PlaceholderText="Enter your password"/>
```

![Поле ввода пароля в состоянии покоя с текстом подсказки](images/passwordbox-rest-hinttext.png)

### <a name="maximum-length"></a>Максимальная длина

Укажите максимальное количество вводимых символов с помощью свойства [MaxLength](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.maxlength). Свойства для указания минимальной длины не существует, но вы можете проверить длину пароля и выполнить любую другую проверку в коде приложения.

## <a name="password-reveal-mode"></a>Режим показа пароля

Элемент управления PasswordBox имеет встроенную кнопку, нажав которую можно отобразить введенный пароль. Так выглядит результат действия пользователя. Если отпустить кнопку, пароль будет автоматически скрыт.

![Отображается текст, вводимый в поле пароля](images/passwordbox-text-reveal.png)

### <a name="peek-mode"></a>Режим временного просмотра

По умолчанию кнопка показа пароля (или кнопка «Подсмотреть») отображается. Пользователь должен удерживать эту кнопку для просмотра пароля, чтобы поддерживать высокий уровень безопасности.

Значение свойства [PasswordRevealMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.passwordbox.passwordrevealmode) — не единственный фактор, который определяет видимость кнопки показа пароля. Среди других факторов: отображается ли элемент управления над минимальной шириной, имеет ли PasswordBox фокус и содержит ли поле ввода текста хотя бы один символ. Кнопка показа пароля отображается, только когда поле PasswordBox получает фокус в первый раз и в него вводится символ. Если PasswordBox теряет фокус, а затем вновь получает его, кнопка показа не отображается, пока пароль не очистят и не начнут вводить заново.

> **Внимание!** &nbsp;&nbsp;До Windows 10 кнопка показа пароля не отображалась по умолчанию. Если в вашем приложении необходимо всегда скрывать пароль, установите для параметра PasswordRevealMode значение Hidden.

### <a name="hidden-and-visible-modes"></a>Режимы Hidden и Visible

Другие значения перечисления [PasswordRevealMode](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordRevealMode) — **Hidden** и **Visible** — скрывают кнопку показа пароля и позволяют программно управлять видимостью пароля.

Чтобы пароль был всегда скрыт, установите для параметра PasswordRevealMode значение Hidden. Если вам не нужно, чтобы пароль был всегда скрыт, можно предоставить настраиваемый пользовательский интерфейс, чтобы пользователь мог переключать PasswordRevealMode между Hidden и Visible.

В предыдущих версиях Windows Phone элемент PasswordBox использовал флажок для переключения видимости пароля. Можно создать аналогичный пользовательский интерфейс для своего приложения, как показано в следующем примере. Чтобы позволить пользователю переключать режимы, можно использовать и другие элементы управления, например [ToggleButton](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.ToggleButton).

В этом примере показано, как использовать [CheckBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CheckBox), чтобы позволить пользователю переключать режим показа элемента PasswordBox.

```xaml
<StackPanel Width="200">
    <PasswordBox Name="passwordBox1"
                 PasswordRevealMode="Hidden"/>
    <CheckBox Name="revealModeCheckBox" Content="Show password"
              IsChecked="False"
              Checked="CheckBox_Changed" Unchecked="CheckBox_Changed"/>
</StackPanel>
```

```csharp
private void CheckBox_Changed(object sender, RoutedEventArgs e)
{
    if (revealModeCheckBox.IsChecked == true)
    {
        passwordBox1.PasswordRevealMode = PasswordRevealMode.Visible;
    }
    else
    {
        passwordBox1.PasswordRevealMode = PasswordRevealMode.Hidden;
    }
}
```

Этот элемент управления PasswordBox выглядит следующим образом.

![Поле ввода пароля с настраиваемой кнопкой показа](images/passwordbox-custom-reveal.png)

## <a name="choose-the-right-keyboard-for-your-text-control"></a>Выбор подходящей клавиатуры для элемента управления текстом

Чтобы упростить пользователям ввод данных с помощью сенсорной клавиатуры или панели функционального ввода, можно настроить тип вводимых данных элемента управления для ввода текста, чтобы он соответствовал типу данных, которые должен вводить пользователь. PasswordBox поддерживает только значения типа вводимых данных **Password** и **NumericPin**. Любое другое значение будет проигнорировано.

Дополнительные сведения об использовании типов вводимых данных см. в разделе [Использование типа вводимых данных для изменения сенсорной клавиатуры](https://docs.microsoft.com/windows/uwp/design/input/use-input-scope-to-change-the-touch-keyboard).

## <a name="recommendations"></a>Рекомендации

-   Используйте метку или подстановочный текст, если назначение поля ввода пароля не очевидно. Метка отображается независимо от того, указано ли значение в поле ввода. Подстановочный текст отображается внутри поля ввода текста и пропадает после ввода значения.
-   Задайте для поля ввода пароля достаточную ширину, чтобы можно было ввести ряд значений. Длина слов различается в зависимости от языков, поэтому если вы собираетесь выводить свое приложение на международный уровень, следует учитывать особенности локализации.
-   Не размещайте дополнительные элементы управления рядом с полем для ввода пароля. Поле ввода пароля имеет кнопку показа пароля, с помощью которой можно проверить введенный пароль. Поэтому размещение справа другого элемента управления может привести к тому, что пользователь случайно раскроет свой пароль, пытаясь нажать на другой элемент управления. Во избежание такой ситуации оставьте между полем и другим элементом управления достаточно места или расположите этот элемент на следующей строке.
-   Рассмотрите возможность предоставления двух полей ввода пароля для создания учетной записи: одного поля для нового пароля и второго — для подтверждения пароля.
-   Для выполнения входа отображайте только одно поле ввода пароля.
-   Когда поле ввода пароля используется для PIN-кода, рассмотрите возможность предоставления незамедлительного ответа сразу после ввода последней цифры без использования кнопки подтверждения.

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Xaml-Controls-Gallery) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Связанные статьи

[Текстовые элементы управления](text-controls.md)

- [Рекомендации для проверки орфографии](text-controls.md)
- [Добавление поиска](https://docs.microsoft.com/previous-versions/windows/apps/hh465231(v=win.10))
- [Рекомендации для ввода текста](text-controls.md)
- [Класс TextBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)
- [Класс Windows.UI.Xaml.Controls PasswordBox](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.PasswordBox)
- [Свойство String.Length](https://msdn.microsoft.com/library/system.string.length(v=vs.110).aspx)
