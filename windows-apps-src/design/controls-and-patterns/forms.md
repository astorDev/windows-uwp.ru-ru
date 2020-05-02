---
Description: Рекомендации по макетам форм в приложениях UWP.
title: Формы
template: detail.hbs
ms.date: 11/07/2017
ms.topic: article
keywords: windows 10, uwp, fluent
ms.openlocfilehash: b6533864748b4245b16ec7bcea9d2a831ff1c88a
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76520449"
---
# <a name="forms"></a>Формы
Форма — это группа элементов управления, которые собирают данные пользователей и отправляют их. Формы обычно используются для страниц параметров, опросов, создания учетных записей и многого другого. 

В этой статье обсуждаются рекомендации по созданию макетов XAML для форм.

![Пример формы](images/PivotHeader.png)

## <a name="when-should-you-use-a-form"></a>Когда следует использовать форму?
Форма — это отдельная страница для входных данных, которые четко связаны друг с другом. Форму необходимо использовать , когда нужно явно собирать данные пользователя. Вы можете создать форму для пользователя, чтобы:
- войти в учетную запись;
- зарегистрироваться в учетной записи;
- изменить параметры приложения, такие как конфиденциальность или отображение;
- принять участье в опросе;
- в номенклатуре покупки;
- Оставить отзыв

## <a name="types-of-forms"></a>Типы форм

Существует два типа форм отправления и отображения пользовательского ввода:

### <a name="1-instantly-updating"></a>1. Мгновенное обновление
![страница параметров](images/control-examples/toggle-switch-news.png)

Используйте мгновенно обновляемую форму, когда хотите, чтобы пользователи сразу увидели результаты изменения значений в форме. Например, на страницах параметров отображаются текущие выбранные параметры. Любые изменения, внесенные в выбранные параметры, применяются немедленно. Чтобы подтвердить изменения в вашем приложении, необходимо [добавить обработчик событий](controls-and-events-intro.md) к каждому элементу управления для ввода. Если пользователь изменяет элемент управления для ввода, то приложение может реагировать соответствующим образом.

### <a name="2-submitting-with-button"></a>2. Отправка с помощью кнопки
Другой тип формы позволяет пользователю выбирать, когда отправлять данные с помощью одного нажатия кнопки.

![добавить новую страницу событий календаря](images/calendar-form.png)

Этот тип формы дает пользователю возможность гибко отвечать на запросы. Как правило, этот тип формы содержит больше полей ввода свободной формы и, таким образом, получает больше разнообразных ответов. Чтобы обеспечить корректный пользовательский ввод и правильно отформатированные данные при отправке, учтите следующие рекомендации:

- Сделайте невозможной отправку недопустимых данных с помощью правильного элемента управления (то есть используйте CalendarDatePicker вместо TextBox для календарных дат). Подробнее о выборе соответствующих элементов управления для ввода в форме см. в разделе "Элементы управления для ввода в более поздней версии".
- При использовании элементов управления TextBox предоставьте пользователям подсказку о желаемом формате ввода с помощью свойства [PlaceholderText](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox.PlaceholderText).
- Предоставьте пользователям соответствующую экранную клавиатуру, указав ожидаемый ввод элемента управления с помощью свойства [InputScope](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.inputscope).
- Пометьте обязательное значение звездочкой * на метке.
- Отключите кнопку отправки, пока все необходимые данные не будут заполнены.
- Если после отправки есть недопустимые данные, пометьте элементы управления недопустимым вводом с выделенными полями или границами и попросите пользователя повторно отправить форму.
- Для других ошибок, таких как сбой сетевого подключения, убедитесь, что для пользователя отображено соответствующее сообщение об ошибке. 


## <a name="layout"></a>Макет

Чтобы упростить взаимодействие с пользователем и убедиться, что пользователи могут вводить правильные данные, рассмотрите следующие рекомендации по разработке макетов для форм. 

### <a name="labels"></a>Метки
[Метки](labels.md) должны быть выровнены по левому краю и размещены над элементом управления для ввода. Встроенное свойство заголовка, с помощью которого задаются метки, доступно во многих элементах управления. Для элементов управления, не имеющих свойства заголовка, а также для того, чтобы присвоить метку группе элементов управления, можно использовать [TextBlock](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBlock).

Чтобы [рекомендовать специальные возможности](../accessibility/accessibility.md), пометьте все отдельные элементы и группы элементов управления для наглядности как для людей, так и для программ чтения с экрана. 

Для стилей шрифта используйте по умолчанию [пандус типа UWP](../style/typography.md). Используйте `TitleTextBlockStyle` для заголовков страниц, `SubtitleTextBlockStyle` для заголовков групп и `BodyTextBlockStyle` для меток элементов управления.

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
Чтобы визуально отделить группы элементов управления друг от друга, используйте [выравнивание , поля и отступы](../layout/alignment-margin-padding.md). Отдельные элементы управления для ввода имеют высоту 80 пикс и должны быть заменены на 24 пикс. Группы элементов управления для ввода должны быть заменены на 48 пикс.

![группы форм](images/forms-groups.png)

### <a name="columns"></a>Столбцы
Создание столбцов может уменьшить ненужные пробелы в формах, особенно на экранах большего размера. Однако если нужно создать форму с несколькими столбцами, количество столбцов должно зависеть от количества элементов управления для ввода на странице и размера экрана окна приложения. Вместо того, чтобы переполнять экран многочисленными элементами управления для ввода, рассмотрите возможность создания нескольких страниц для формы.  

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
Размер форм должен изменяться при изменении размера экрана или окна, чтобы пользователи не игнорировали поля ввода. Для получения дополнительных сведений см. [Методы адаптивного дизайна](../layout/responsive-design.md). Например, если необходимо всегда держать в поле зрения определенные области формы, независимо от размера экрана.

![фокус форм](images/forms-focus2.png)

### <a name="tab-stops"></a>Позиции табуляции
Пользователи могут использовать клавиатуру для навигации по элементам управления с [позиции табуляции](../input/keyboard-interactions.md#tab-stops). По умолчанию последовательность переходов для элементов управления отражает порядок их создания в XAML. Чтобы переопределить поведение по умолчанию, измените свойства элемента управления **IsTabStop** или **TabIndex** . 

![Фокус клавиши TAB — на элементе управления в форме](images/forms-focus1.png)

## <a name="input-controls"></a>Элементы управления для ввода
Элементы управления для ввода — это элементы пользовательского интерфейса, которые позволяют пользователям вводить данные в формы. Ниже перечислены некоторые общие элементы управления, которые можно добавить в формы, а также сведения о том, когда их использовать.

### <a name="text-input"></a>Ввод текста
Элемент | Использование | Пример
 - | - | -
[TextBox](text-box.md) | Позволяет захватить одну или несколько строк текста | Имена, свободные формы ответов или отзывы
[PasswordBox](password-box.md) | Позволяет собрать личные данные, скрывая персонажей | Пароли, номера социального страхования (SSN), PIN-коды, сведения о кредитной карте 
[AutoSuggestBox](auto-suggest-box.md) | Позволяет показывать пользователям список предложений из соответствующего набора данных при вводе | Поиск в базе данных, получатель: строка, предыдущие запросы
[RichEditBox](rich-edit-box.md) | Позволяет редактировать текстовые файлы с отформатированным текстом, гиперссылками и изображениями | Помогает загрузить файл, предварительно просмотреть его и отредактировать в приложении

### <a name="selection"></a>Выбор
Элемент | Использование | Пример
- | - | - 
| [CheckBox](checkbox.md) | Выбор или отмена выбора элементов одного или большего количества действий | Принимает условия, добавляет дополнительные элементы, выбирает все подходящие
[RadioButton](radio-button.md) | Позволяют пользователю выбрать один параметр из двух или более предлагаемых вариантов | Тип выбора, способ доставки и т. д.
[ToggleSwitch](toggles.md) | Выбирает один из двух взаимоисключающих вариантов | Включить или выключить

> **Примечание**. Выберите один из двух взаимоисключающих параметров. Если имеется пять или более элементов выбора, используйте элемент управления "Список".

### <a name="lists"></a>Списки
Элемент | Использование | Пример
- | - | -
[ComboBox](combo-box.md) | Начните с состояния сжатия и разверните, чтобы отобразить список выбираемых элементов | Выберите из длинного списка элементов, таких как штаты или страны
[ListView](https://docs.microsoft.com/windows/uwp/controls-and-patterns/lists#list-views) | Позволяет упорядочивать элементы и назначать заголовки групп, а также перетаскивать элементы, организовывать содержимое и изменять порядок элементов | Параметры ранга
[GridView](https://docs.microsoft.com/windows/uwp/controls-and-patterns/lists#grid-views) | Позволяет упорядочивать и просматривать коллекции на основе изображений | Выберите фотографию, цвет, тему отображения

### <a name="numeric-input"></a>Числовой ввод
Элемент | Использование | Пример
- | - | -
[Ползунок](slider.md) | Позволяет выбрать число из диапазона непрерывных числовых значений | Проценты, том, скорость воспроизведения
[Оценка](rating.md) | Позволяет оценить с помощью звезд | Отзывы клиентов

### <a name="date-and-time"></a>Date and Time (Дата и время)

Элемент | Использование 
- | - 
[CalendarView](calendar-view.md) | Выбор одной даты или диапазона дат в календаре, который всегда виден 
[CalendarDatePicker](calendar-date-picker.md) | Выбор одной даты из контекстного календаря 
[DatePicker](date-picker.md) | Выбор одной даты, когда контекстно-зависимая информация не важна
[TimePicker](time-picker.md) | Выбор одного значения времени

### <a name="additional-controls"></a>Дополнительные элементы управления 
Полный список элементов управления UWP см. в [индексе элементов управления по функции](controls-by-function.md).

Более сложные и настраиваемые элементы управления пользовательским интерфейсом можно найти в ресурсах UWP, которые предлагаются такими компаниями, как [Telerik](https://www.telerik.com/), [SyncFusion](https://www.syncfusion.com/uwp-ui-controls), [DevExpress](https://www.devexpress.com/Products/NET/Controls/Win10Apps/), [Infragistics](https://www.infragistics.com/products/universal-windows-platform), [ComponentOne](https://www.componentone.com/Studio/Platform/UWP) и [ActiPro](https://www.actiprosoftware.com/products/controls/universal).

## <a name="one-column-form-example"></a>Пример формы с одним столбцом
В этом примере используются [представление списка](lists.md) Acrylic с [основными и подробными сведениями](master-details.md) и элемент управления[NavigationView](navigationview.md).
![Снимок экрана: другой пример формы](images/FormExample2.png)
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

## <a name="two-column-form-example"></a>Пример формы с двумя столбцами
В этом примере используются элементы управления [Pivot](pivot.md), фон [Acrylic](../style/acrylic.md) и [CommandBar](app-bars.md) в дополнение к элементам управления для ввода.
![Снимок экрана: пример формы](images/FormExample.png)
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

## <a name="customer-orders-database-sample"></a>Пример базы данных Customer Orders
![Снимок экрана: база данных заказов пользователей](images/customerorderform.png) Чтобы узнать, как подключить входные значения формы к базе данных **Azure** и увидеть полностью реализованную форму, см. пример приложения [База данных Customer Orders](https://github.com/Microsoft/Windows-appsample-customers-orders-database).

## <a name="related-topics"></a>Связанные темы
- [Элементы управления для ввода](controls-and-events-intro.md)
- [Оформление текста](../style/typography.md)
