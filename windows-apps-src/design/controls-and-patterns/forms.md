---
Description: Макет, касающиеся форм в приложениях универсальной платформы Windows.
title: Формы
template: detail.hbs
ms.date: 11/07/2017
ms.topic: article
keywords: windows 10, uwp, fluent
ms.openlocfilehash: 8a57f13e168a248569bca1beeceed7b4f6c89f69
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57658159"
---
# <a name="forms"></a>Формы
Формы — это группа элементов управления, сбора и передачи данных от пользователей. Для страниц параметров обычно используются формы опросов, создании учетных записей и многое другое. 

В этой статье рассматриваются рекомендации по разработке для создания разметки XAML для форм.

![Пример формы](images/PivotHeader.png)

## <a name="when-should-you-use-a-form"></a>Когда следует использовать форму?
Форма является выделенную страницу для сбора входных данных, которые явно связаны друг с другом. Формы следует использовать, когда необходимо явным образом собирать данные от пользователя. Можно создать форму для пользователя:
- Войдите в учетную запись
- Зарегистрируйтесь для учетной записи
- Изменить параметры приложения, такие как конфиденциальность или параметры отображения
- Принять участие в опросе
- Покупки
- Оставить отзыв

## <a name="types-of-forms"></a>Типы форм

Рассматривая способы отправки и отображается ввод данных пользователем, существует два типа форм:

### <a name="1-instantly-updating"></a>1. Мгновенное обновление
![Странице "Параметры"](images/control-examples/toggle-switch-news.png)

Использование мгновенно обновления формы, при необходимости пользователям сразу же видеть результаты при изменении значений в форме. Например в страницы параметров, выбранных отображаются и любые изменения, внесенные в выбранные параметры вступают в силу немедленно. Чтобы подтвердить изменения в приложении, необходимо будет [добавьте обработчик событий](controls-and-events-intro.md) для каждого входного элемента управления. Если пользователь изменяет элемент управления вводом, затем приложение может реагировать соответствующим образом.

### <a name="2-submitting-with-button"></a>2. Отправка с помощью кнопки
Тип формы позволяет пользователю выбирать время для отправки данных с одним нажатием кнопки.

![Добавить новую страницу событий календарь](images/calendar-form.png)

Такого рода форма позволяет пользователю в отвечать на запросы. Как правило этим типом формы содержит дополнительные поля для ввода произвольной формы и таким образом получает разнообразных ответов. Чтобы обеспечить входные данные пользователя и правильно отформатированные данные после отправки, учитывайте следующие рекомендации:

- Это делает невозможным отправки недопустимых данных с помощью правильного управления (т. е. Используйте CalendarDatePicker, а не текстового поля для дат календаря). Узнайте больше о выборе соответствующие элементы управления для ввода в форме в разделе элементы управления для ввода в более поздней версии.
- При использовании элемента управления TextBox, предоставляют пользователям указание нужный формат ввода с [PlaceholderText](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox.PlaceholderText) свойство.
- Предоставить пользователям с соответствующими экранную клавиатуру, указав ожидаемые входные данные элемента управления с [InputScope](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.inputscope) свойство.
- Пометьте обязательные входные данные, отмеченные звездочкой * метки.
- Отключение кнопки отправки, пока не будет заполнено все сведения, необходимые.
- Если есть недопустимые данные после отправки элементов с недопустимые входные данные с выделенными полями или границы и пользователь должен повторно отправить форму.
- Для других ошибок, например ошибкой сетевого соединения убедитесь, что для отображения сообщения об соответствующее сообщение об ошибке для пользователя. 


## <a name="layout"></a>Макет

Чтобы упростить взаимодействие с пользователем и убедитесь, что пользователи могут ввести правильные входные данные, учитывайте следующие рекомендации по проектированию макеты для форм. 

### <a name="labels"></a>Метки
[Метки](labels.md) следует по левому краю и поместить над элемент управления для ввода. Многие элементы управления обладают встроенное свойство заголовка для отображения метки. Для элементов управления, не имеющих свойства заголовка, а также для того, чтобы присвоить метку группе элементов управления, можно использовать [TextBlock](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Controls.TextBlock).

Чтобы [конструктора для объекта специальных возможностей](../accessibility/accessibility.md), пометьте все лица и группы элементов управления для ясности для обоих человека и средства чтения с экрана. 

Для стилей шрифта, используйте значение по умолчанию [UWP тип увеличения](../style/typography.md). Используйте `TitleTextBlockStyle` для заголовков страниц, `SubtitleTextBlockStyle` для заголовков групп, и `BodyTextBlockStyle` для метки элемента управления.

<div class="mx-responsive-img">
<table>
<tr><th>Рекомендуется</th><th>Не рекомендуется</th></tr>
<tr>
<td><img src="../controls-and-patterns/images/forms-shortform1col.png" alt="form with top labels"></td>
<td><img src="../controls-and-patterns/images/forms-leftlabel-donot1.png" alt="form with left labels don't"></td>
</tr>
</table>
</div>

### <a name="spacing"></a>Интервал
Чтобы визуально разделить групп элементов управления друг от друга, используйте [выравнивание, поля и заполнение](../layout/alignment-margin-padding.md). Отдельные элементы управления для ввода являются 80px высоты и должны быть расстоянии друг от друга 24 точки друг от друга. Группы элементов управления вводом должно быть расстоянии друг от друга 48 пикс друг от друга.

![группы форм](images/forms-groups.png)

### <a name="columns"></a>Столбцы
Создание столбцов можно уменьшить лишних пробелов в формах, особенно в больших размеров экрана. Тем не менее если вы хотите создать форму несколькими столбцами, число столбцов должно влиять на количество входных элементов управления страницы и размера экрана окна приложения. А не заполнить экран с множество элементов управления вводом, рассмотрите возможность создания нескольких страниц для формы.  

<div class="mx-responsive-img">
<table>
<tr><th>Рекомендуется</th><th>Не рекомендуется</th></tr>
<tr>
<td><img src="../controls-and-patterns/images/forms-2cols.png" alt="form with 2 columns"></td>
<td><img src="../controls-and-patterns/images/forms-2cols-bad.png" alt="form with 2 bad columns"></td>
</tr>
<tr><td><img src="../controls-and-patterns/images/forms-3cols.png" alt="form with 3 columns"></td></tr>
</table>

</div>

### <a name="responsive-layout"></a>Гибкий макет
Формы должны изменяться размеры при изменении размера экрана или окна, чтобы пользователям не следует недооценивать все поля ввода. Дополнительные сведения см. в разделе [методы гибкий Дизайн](../layout/responsive-design.md). Например вы хотите всегда хранить отдельных областей формы в представлении, независимо от размера экрана.

![фокус форм](images/forms-focus2.png)

### <a name="tab-stops"></a>Остановки перехода
Пользователи могут использовать клавиатуры для перемещения элементов управления с помощью [позиции табуляции](../input/keyboard-interactions.md#tab-stops). По умолчанию последовательности табуляции элементов управления отражает порядок, в котором они были созданы в XAML. Чтобы переопределить поведение по умолчанию, измените **IsTabStop** или **TabIndex** свойства элемента управления. 

![клавиши табуляции для фокусировки на элементе управления в форме](images/forms-focus1.png)

## <a name="input-controls"></a>Элементы управления для ввода
Элементы управления для ввода являются элементы пользовательского интерфейса, которые позволяют пользователям вводить сведения в формы. Некоторые стандартные элементы управления, которые могут быть добавлены к формам, перечислены ниже, а также сведения об их использовании.

### <a name="text-input"></a>Текстовый ввод
Элемент управления | Используйте | Пример
 - | - | -
[TextBox](text-box.md) | Запись одной или нескольких строк текста | Имена, ответов в свободной форме или отзыв
[PasswordBox](password-box.md) | Сбор личных данных, чтобы скрывать символы | Пароли, номера социального страхования (SSN), ПИН-кодов, сведения о кредитной карте 
[AutoSuggestBox](auto-suggest-box.md) | Показать список предложения из соответствующего набора данных пользователей, как они тип | Базы данных поиска, выслать: строка, предыдущие запросы
[RichEditBox](rich-edit-box.md) | Изменение текстовых файлов с помощью форматированного текста, гиперссылок и изображения | Отправка файла, предварительного просмотра и редактирования в приложении

### <a name="selection"></a>Выбор
Элемент управления | Используйте | Пример
- | - | - 
| [Флажок](checkbox.md) | Установите или снимите один или несколько элементов действия | Примите условия, добавьте дополнительные элементы, выберите все подходящие
[Переключатель "](radio-button.md) | Выберите один из вариантов из двух или более вариантов | Выберите тип доставки, метод и т. д.
[ToggleSwitch](toggles.md) | Выберите один из двух взаимоисключающих вариантов. | Включить или выключить

> **Примечание**. При наличии пяти или более элементов выбора, используйте элемент управления списка.

### <a name="lists"></a>Списки
Элемент управления | Используйте | Пример
- | - | -
[ComboBox](https://docs.microsoft.com/windows/uwp/controls-and-patterns/lists.md#drop-down-lists) | Запуск в compact состоянии и разверните, чтобы увидеть список выбираемых элементов | Выберите из длинный список элементов, таких как Штатах Америки или в странах
[ListView](https://docs.microsoft.com/windows/uwp/controls-and-patterns/lists#list-views) | Классификация элементов и назначить заголовки групп, перетаскивание элементов, следите за содержимое и изменение порядка элементов | Параметры рейтинга
[GridView](https://docs.microsoft.com/windows/uwp/controls-and-patterns/lists#grid-views) | Упорядочить и Обзор коллекций на основе образа | Выбрать фотографию, цвет и отобразить темы

### <a name="numeric-input"></a>Числового ввода
Элемент управления | Используйте | Пример
- | - | -
[Slider](slider.md) | Выберите число из диапазона непрерывных числовых значений | Процентные показатели, тома, скорость воспроизведения
[Оценка](rating.md) | Оцените с звезд | Отзывы пользователей

### <a name="date-and-time"></a>Date and Time (Дата и время)

Элемент управления | Используйте 
- | - 
[Представления календаря](calendar-view.md) | Выберите дату или диапазон дат из всегда видны календаря 
[CalendarDatePicker](calendar-date-picker.md) | Выберите дату из календаря контекстные 
[Выбор даты](date-picker.md) | Выберите один локализованную дату, когда контекстные сведения не имеет значения
[TimePicker](time-picker.md) | Выберите значение один раз

### <a name="additional-controls"></a>Дополнительные элементы управления 
Полный список элементов управления универсальной платформы Windows, см. в разделе [индекс Функциональная классификация элементов управления](controls-by-function.md).

Более сложные и пользовательские элементы управления пользовательского интерфейса, см. на UWP ресурсы, доступные из компаний-например [Telerik](https://www.telerik.com/), [SyncFusion](https://www.syncfusion.com/products/uwp), [DevExpress](https://www.devexpress.com/Products/NET/Controls/Win10Apps/), [ Infragistics](https://www.infragistics.com/products/universal-windows-platform), [ComponentOne](https://www.componentone.com/Studio/Platform/UWP), и [ActiPro](https://www.actiprosoftware.com/products/controls/universal).

## <a name="one-column-form-example"></a>Пример элемента form один столбец
В этом примере используется Acrylic ["основной/подробности"](master-details.md) [представление списка](lists.md) и [NavigationView](navigationview.md) элемента управления.
![Снимок экрана еще один пример формы](images/FormExample2.png)
```xaml
<StackPanel>
    <TextBlock Text="New Customer" Style="{StaticResource TitleTextBlockStyle}"/>
    <Button Height="100" Width="100" Background="LightGray" Content="Add photo" Margin="0,24" Click="AddPhotoButton_Click"/>
    <TextBox x:Name="Name" Header= "Name" Margin="0,24,0,0" MaxLength="32" Width="400" HorizontalAlignment="Left" InputScope="PersonalFullName"/>
    <TextBox x:Name="PhoneNumber" Header="Phone Number" Margin="0,24,0,0" MaxLength="15" Width="400" HorizontalAlignment="Left" InputScope="TelephoneNumber" />
    <TextBox x:Name="Email" Header="Email" Margin="0,24,0,0" MaxLength="50" Width="400" HorizontalAlignment="Left" InputScope="EmailNameOrAddress" />
    <TextBox x:Name="Address" Header="Address" PlaceholderText="Address" Margin="0,24,0,0" MaxLength="50" Width="400" HorizontalAlignment="Left" InputScope="EmailNameOrAddress" />
    <TextBox x:Name="Address2" Margin="0,24,0,0" PlaceholderText="Address 2" MaxLength="50" Width="400" HorizontalAlignment="Left" InputScope="EmailNameOrAddress" />
    <RelativePanel>
        <TextBox x:Name="City" PlaceholderText="City" Margin="0,24,0,0" MaxLength="50" Width="200" HorizontalAlignment="Left" InputScope="EmailNameOrAddress" />
        <ComboBox x:Name="State" PlaceholderText="State" Margin="24,24,0,0"  Width="100" RelativePanel.RightOf="City">
             <x:String>WA</x:String>
        </ComboBox>
    </RelativePanel>
    <TextBox x:Name="ZipCode" PlaceholderText="Zip Code" Margin="0,24,0,0" MaxLength="6" Width="100" HorizontalAlignment="Left" InputScope="Number" />
    <StackPanel Orientation="Horizontal">
        <Button Content="Save" Margin="0,24" Click="SaveButton_Click"/>
        <Button Content="Cancel" Margin="24" Click="CancelButton_Click"/>
    </StackPanel>  
</StackPanel>
```

## <a name="two-column-form-example"></a>Пример элемента form двух столбцов
В этом примере используется [Pivot](pivot.md) управления [Acrylic](../style/acrylic.md) фон, и [CommandBar](app-bars.md) помимо элементов управления вводом.
![Снимок экрана: пример элемента form](images/FormExample.png)
```xaml
<Grid>
    <Pivot Background="{ThemeResource SystemControlAccentAcrylicWindowAccentMediumHighBrush}" >
        <Pivot.TitleTemplate>
            <DataTemplate>
                <Grid>
                    <TextBlock Text="Company Name" Style="{ThemeResource HeaderTextBlockStyle}"/>
                </Grid>
            </DataTemplate>
        </Pivot.TitleTemplate>
        <PivotItem Header="Orders" Margin="0"/>    
        <PivotItem Header="Customers" Margin="0">
            <!--Form Example-->
            <Grid Background="White">
                <RelativePanel>
                    <StackPanel x:Name="Customer" Margin="20">
                        <TextBox x:Name="CustomerName" Header= "Customer Name" Margin="0,24,0,0" MaxLength="320" Width="400" HorizontalAlignment="Left" InputScope="PersonalFullName"/>
                        <TextBox x:Name="CustomerPhoneNumber" Header="Phone Number" Margin="0,24,0,0" MaxLength="50" Width="400" HorizontalAlignment="Left" InputScope="TelephoneNumber" />
                        <TextBox x:Name="Address" Header="Address" PlaceholderText="Address" Margin="0,24,0,0" MaxLength="50" Width="400" HorizontalAlignment="Left" InputScope="AlphanumericFullWidth" />
                        <TextBox x:Name="Address2" Margin="0,24,0,0" PlaceholderText="Address 2" MaxLength="50" Width="400" HorizontalAlignment="Left" InputScope="AlphanumericFullWidth" />
                        <RelativePanel>
                            <TextBox x:Name="City" PlaceholderText="City" Margin="0,24,0,0" MaxLength="50" Width="200" HorizontalAlignment="Left" InputScope="Text" />
                            <ComboBox x:Name="State" PlaceholderText="State" Margin="24,24,0,0"  Width="100" RelativePanel.RightOf="City">
                                <x:String>WA</x:String>
                            </ComboBox>
                        </RelativePanel>
                        <TextBox x:Name="ZipCode" PlaceholderText="Zip Code" Margin="0,24,0,0" MaxLength="6" Width="100" HorizontalAlignment="Left" InputScope="Number" />
                    </StackPanel>
                    <StackPanel x:Name="Associate" Margin="20" RelativePanel.RightOf="Customer">
                        <TextBox x:Name="AssociateName" Header= "Associate" Margin="0,24,0,0" MaxLength="320" Width="400" HorizontalAlignment="Left" InputScope="PersonalFullName"/>
                        <TextBox x:Name="AssociatePhoneNumber" Header="Phone Number" Margin="0,24,0,0" MaxLength="50" Width="400" HorizontalAlignment="Left" InputScope="TelephoneNumber" />
                        <DatePicker x:Name="TargetInstallDate" Header="Target install Date" HorizontalAlignment="Left" Margin="0,24,0,0"></DatePicker>
                        <TimePicker x:Name="InstallTime" Header="Install Time" HorizontalAlignment="Left" Margin="0,24,0,0"></TimePicker>
                    </StackPanel>
                </RelativePanel>
            </Grid>
        </PivotItem>
        <PivotItem Header="Invoices"/>
        <PivotItem Header="Stock"/>
        <Pivot.RightHeader>
            <CommandBar OverflowButtonVisibility="Collapsed" Background="Transparent">
                <AppBarButton Icon="Add"/>
                <AppBarSeparator/>
                <AppBarButton Icon="Edit" />
                <AppBarButton Icon="Delete"/>
                <AppBarSeparator/>
                <AppBarButton Icon="Save"/>
            </CommandBar>
        </Pivot.RightHeader>
    </Pivot>
</Grid>
```

## <a name="customer-orders-database-sample"></a>Образец базы данных заказов клиента
![клиент заказывает базы данных экрана](images/customerorderform.png) вы научитесь подключите к вводу формы **Azure** и базы данных см. в разделе полностью реализованного формы, см. в разделе [базу данных заказов клиентов](https://github.com/Microsoft/Windows-appsample-customers-orders-database) пример приложения.

## <a name="related-topics"></a>Статьи по теме
- [Элементы управления для ввода](controls-and-events-intro.md)
- [Оформления](../style/typography.md)
