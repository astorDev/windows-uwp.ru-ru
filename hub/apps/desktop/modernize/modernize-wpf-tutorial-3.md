---
description: В этом руководстве показано, как добавлять пользовательские интерфейсы UWP XAML, создавать пакеты MSIX и включать другие актуальные компоненты в приложения WPF.
title: Добавление элемента управления CalendarView универсальной платформы Windows с помощью XAML Islands
ms.topic: article
ms.date: 06/27/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, UWP, Windows Forms, WPF, объекты XAML Island
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 830c1cdf2e24e716d51642bc65b5b6783d0d784a
ms.sourcegitcommit: 6bb794c6e309ba543de6583d96627fbf1c177bef
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69643375"
---
# <a name="part-3-add-a-uwp-calendarview-control-using-xaml-islands"></a>Часть 3. Добавление элемента управления CalendarView универсальной платформы Windows с помощью XAML Islands

Это третья часть руководства, в которой рассматривается модернизация примера классического приложения WPF Contoso Expenses. Общие сведения об этом руководстве, предварительные требования и инструкции по скачиванию примера приложения см. в разделе [Руководство. Модернизация приложения WPF](modernize-wpf-tutorial.md). В этой статье предполагается, что вы уже выполнили инструкции из [части 2](modernize-wpf-tutorial-2.md).

В вымышленном сценарии, рассматриваемом в рамках этого руководства, группа разработки Contoso хочет упростить выбор даты для отчета о расходах на устройстве с сенсорным вводом. В этой части руководства вы добавите в приложение элемент управления UWP [CalendarView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/calendar-view). Это элемент управления, который используется в функциях даты и времени Windows 10 на панели задач.

![Изображение элемента управления CalendarView](images/wpf-modernize-tutorial/CalendarViewControl.png)

В отличие от элемента управления **InkCanvas**, добавленного в [части 2](modernize-wpf-tutorial-2.md), набор средств сообщества Windows Community не предоставляет упакованную версию UWP **CalendarView**, которую можно использовать в приложениях WPF. В качестве альтернативы можно разместить **InkCanvas** в универсальном элементе управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost). Этот элемент управления можно использовать для размещения любого основного элемента управления UWP из Windows SDK или библиотеки WinUI либо любого пользовательского элемента управления UWP, созданного сторонним производителем. Элемент управления **WindowsXamlHost** предоставляется в пакете NuGet `Microsoft.Toolkit.Wpf.UI.XamlHost`. Этот пакет входит в состав пакета NuGet `Microsoft.Toolkit.Wpf.UI.Controls`, установленного в [части 2](modernize-wpf-tutorial-2.md).

> [!NOTE]
> В этом руководстве показано, как использовать [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения основного элемента управления **CalendarView** из Windows SDK. Пошаговое руководство, в котором показано, как разместить пользовательский элемент управления, приведено в разделе [Размещение настраиваемого элемента управления UWP в приложении WPF, использующем XAML Islands](host-custom-control-with-xaml-islands.md).

Чтобы использовать элемент управления **WindowsXamlHost**, потребуется напрямую вызывать интерфейсы API WinRT из кода в приложении WPF. Пакет NuGet `Microsoft.Windows.SDK.Contracts` содержит ссылки, необходимые для вызова интерфейсов API WinRT из приложения. Этот пакет также входит в состав пакета NuGet `Microsoft.Toolkit.Wpf.UI.Controls`, установленного в [части 2](modernize-wpf-tutorial-2.md).

## <a name="add-the-windowsxamlhost-control"></a>Добавление элемента управления WindowsXamlHost

1. В **обозревателе решений** разверните папку **Представления** для проекта **ContosoExpenses.Core** и дважды щелкните файл **AddNewExpense.xaml**. Это форма, используемая для добавления новой статьи расходов в список. Ниже показано, как она выглядит в текущей версии приложения.

    ![Добавление новой статьи расходов](images/wpf-modernize-tutorial/AddNewExpense.png)

    Элемент управления "Выбор даты", включенный в WPF, предназначен для традиционных компьютеров с мышью и клавиатурой. Выбор даты на сенсорном экране не слишком удобен из-за маленького размера элемента управления и ограниченного расстояния между днями в календаре.

2. В начало файла **AddNewExpense.xaml** добавьте в элемент **Window** следующий атрибут.

    ```xml
    xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

    Когда вы добавите этот атрибут, элемент **Window** будет выглядеть следующим образом.

    ```xml
    <Window x:Class="ContosoExpenses.Views.AddNewExpense"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
            DataContext="{Binding Source={StaticResource ViewModelLocator},Path=AddNewExpenseViewModel}"
            xmlns:local="clr-namespace:ContosoExpenses"
            mc:Ignorable="d"
            Title="Add new expense" Height="450" Width="800"
            Background="{StaticResource AddNewExpenseBackground}">
    ```

3. Измените значение атрибута **Height** элемента **Window** с 450 на 800. Это необходимо, так как элемент управления UWP **CalendarView** занимает больше пространства, чем элемент управления "Выбор даты" WPF.

    ```xml
    <Window x:Class="ContosoExpenses.Views.AddNewExpense"
            xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
            xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
            xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
            xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
            xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
            DataContext="{Binding Source={StaticResource ViewModelLocator},Path=AddNewExpenseViewModel}"
            xmlns:local="clr-namespace:ContosoExpenses"
            mc:Ignorable="d"
            Title="Add new expense" Height="800" Width="800"
            Background="{StaticResource AddNewExpenseBackground}">
    ```

4. Найдите элемент `DatePicker` в нижней части файла и замените этот элемент приведенным ниже кодом XAML.

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp"  />
    ```

    Этот код XAML добавляет элемент управления **WindowsXamlHost**. Свойство **InitialTypeName** указывает полное имя элемента управления UWP, который требуется разместить (в данном случае это **Windows.UI.XAML.Controls.CalendarView**).

5. Нажмите клавишу F5, чтобы выполнить сборку приложения и открыть его в отладчике. Выберите из списка сотрудника и нажмите кнопку **Add new expense** (Добавить новую статью расходов). Убедитесь, что на следующей странице размещен новый элемент управления UWP **CalendarView**.

    ![Оболочка CalendarView](images/wpf-modernize-tutorial/CalendarViewWrapper.png)

6. Закройте приложение.

## <a name="interact-with-the-windowsxamlhost-control"></a>Взаимодействие с элементом управления WindowsXamlHost

Далее следует обновить приложение для обработки выбранной даты, отобразить ее на экране и заполнить объект **Expense**, чтобы сохранить его в базе данных.

Элемент управления UWP [CalendarView](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) содержит два элемента, которые относятся к этому сценарию:

- свойство **SelectedDates**, содержащее дату, которую выбрал пользователь;
- событие **SelectedDatesChanged**, возникающее, когда пользователь выбирает дату.

Однако **WindowsXamlHost** является универсальным элементом управления ведущего приложения для *любого* типа элемента управления UWP. Таким образом, он не предоставляет свойство **SelectedDates** или событие **SelectedDatesChanged**, так как они относятся к **CalendarView**. Чтобы получить доступ к этим элементам, необходимо написать код, который приводит **WindowsXamlHost** к типу **CalendarView**. Лучше это сделать в ответ на событие **ChildChanged** элемента управления **WindowsXamlHost**, которое порождается при отображении размещенного элемента управления.

1. В файл **ddNewExpense.xaml** добавьте обработчик событий для события **ChildChanged** элемента управления **WindowsXamlHost**, добавленного ранее. По завершении элемент **WindowsXamlHost** должен выглядеть следующим образом.

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp"  ChildChanged="CalendarUwp_ChildChanged" />
    ```

2. В том же файле найдите элемент **Grid.RowDefini** основного элемента **Grid**. Добавьте еще один элемент **RowDefinition** со свойством **Height**, имеющим значение **Auto**, в конец списка дочерних элементов. По завершении элемент **Grid.RowDefinitions** должен выглядеть следующим образом (теперь должно быть 9 элементов **RowDefinition**).

    ```xml
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
    </Grid.RowDefinitions>
    ```

4. Добавьте приведенный ниже код XAML после элемента **WindowsXamlHost** и перед элементом **Button**, указанным ближе к концу файла.

    ```xml
    <TextBlock Text="Selected date:" FontSize="16" FontWeight="Bold" Grid.Row="7" Grid.Column="0" />
    <TextBlock Text="{Binding Path=Date}" FontSize="16" Grid.Row="7" Grid.Column="1" />
    ```

5. Найдите элемент **Button** в конце файла и измените значение свойства **Grid.Row** с **7** на **8**. Это сдвинет кнопку на одну строку в сетке, так как вы добавили новую строку.

    ```xml
    <Button Content="Save" Grid.Row="8" Grid.Column="0" Command="{Binding Path=SaveExpenseCommand}" Margin="5, 12, 0, 0" HorizontalAlignment="Left" Width="180" />
    ```

6. Откройте файл кода **AddNewExpense.xaml.cs**.

7. Добавьте следующий оператор в начало файла.

    ```csharp
    using Microsoft.Toolkit.Wpf.UI.XamlHost;
    ```

8. Добавьте следующий обработчик событий в класс `AddNewExpense`. Этот код реализует событие **ChildChanged** элемента управления **WindowsXamlHost**, объявленного ранее в XAML-файле.

    ```csharp
    private void CalendarUwp_ChildChanged(object sender, System.EventArgs e)
    {
        WindowsXamlHost windowsXamlHost = (WindowsXamlHost)sender;

        Windows.UI.Xaml.Controls.CalendarView calendarView =
            (Windows.UI.Xaml.Controls.CalendarView)windowsXamlHost.Child;

        if (calendarView != null)
        {
            calendarView.SelectedDatesChanged += (obj, args) =>
            {
                if (args.AddedDates.Count > 0)
                {
                    Messenger.Default.Send<SelectedDateMessage>(new SelectedDateMessage(args.AddedDates[0].DateTime));
                }
            };
        }
    }
    ```

    Этот код использует свойство **Child** элемента управления **WindowsXamlHost** для доступа к элементу управления UWP **CalendarView**. Затем код подписывается на событие **SelectedDatesChanged**, которое активируется, когда пользователь выбирает дату из календаря. Этот обработчик событий передает выбранную дату в ViewModel, где новый объект **Expense** создается и сохраняется в базе данных. Для этого в коде используется инфраструктура обмена сообщениями, предоставляемая пакетом NuGet MVVM Light. Код отправляет сообщение **SelectedDateMessage** в объект ViewModel, который получает его и присваивает свойству **Date** выбранное значение. В этом сценарии элемент управления **CalendarView** настроен для режима одиночного выбора, поэтому коллекция будет содержать только один элемент.

    На этом этапе проект по-прежнему не компилируется, так как в нем отсутствует реализация класса **SelectedDateMessage**. Следующие шаги реализуют этот класс.

9. В **обозревателе решений** щелкните правой кнопкой мыши папку **Сообщения** и выберите **Добавить > Класс**. Присвойте новому классу имя **SelectedDateMessage** и нажмите кнопку **Добавить**.

10. Замените все содержимое файла кода **SelectedDateMessage.cs** приведенным ниже кодом. Этот код добавляет оператор using для пространства имен `GalaSoft.MvvmLight.Messaging` (из пакета NuGet MVVM Light), наследует от класса **MessageBase** и добавляет свойство **DateTime**, которое инициализируется с помощью общедоступного конструктора. По завершении файл должен выглядеть следующим образом.

    ```csharp
    using GalaSoft.MvvmLight.Messaging;
    using System;
    
    namespace ContosoExpenses.Messages
    {
        public class SelectedDateMessage: MessageBase
        {
            public DateTime SelectedDate { get; set; }
    
            public SelectedDateMessage(DateTime selectedDate)
            {
                this.SelectedDate = selectedDate;
            }
        }
    }
    ```

11. Далее следует обновить ViewModel для получения этого сообщения и заполнить свойство **Date** ViewModel. В **обозревателе решений** разверните папку **ViewModels** и откройте файл **AddNewExpenseViewModel.cs**.

12. Найдите общедоступный конструктор класса `AddNewExpenseViewModel` и добавьте в его конец приведенный ниже код. Этот код регистрирует приложение для получения сообщения **SelectedDateMessage** и получает выбранную дату из его свойства **SelectedDate**, а затем мы используем ее для задания свойства **Date**, предоставленного ViewModel. Так как это свойство привязано к элементу управления **TextBlock**, который вы добавили ранее, можно увидеть дату, выбранную пользователем.

    ```csharp
    Messenger.Default.Register<SelectedDateMessage>(this, message =>
    {
        Date = message.SelectedDate;
    });
    ```

    По завершении конструктор `AddNewExpenseViewModel` должен выглядеть следующим образом. 

    ```csharp
    public AddNewExpenseViewModel(IDatabaseService databaseService, IStorageService storageService)
    {
        this.databaseService = databaseService;
        this.storageService = storageService;

        Date = DateTime.Today;

        Messenger.Default.Register<SelectedDateMessage>(this, message =>
        {
            Date = message.SelectedDate;
        });
    }
    ```

    > [!NOTE]
    > Метод `SaveExpenseCommand` в том же файле кода выполняет сохранение статей расходов в базе данных. Этот метод использует свойство **Date** для создания нового объекта **Expense**. Теперь это свойство заполняется элементом управления **CalendarView** посредством только что созданного сообщения `SelectedDateMessage`.

13. Нажмите клавишу F5, чтобы выполнить сборку приложения и открыть его в отладчике. Выберите одного из доступных сотрудников и нажмите кнопку **Add new expense** (Добавить новую статью расходов). Заполните все поля в форме и выберите дату в новом элементе управления **CalendarView**. Убедитесь, что выбранная дата отображается в виде строки под элементом управления.

14. Нажмите кнопку **Save** (Сохранить). Форма будет закрыта, а новая статья расходов добавлена в конец списка расходов. Первым столбцом с датой расходов должна быть дата, выбранная в элементе управления **CalendarView**.

## <a name="next-steps"></a>Дальнейшие действия

На этом этапе работы с руководством вы успешно заменили управляющий элемент даты и времени WPF с помощью элемента управления UWP **CalendarView**, который в дополнение к вводу с помощью мыши и клавиатуры поддерживает сенсорный ввод и ввод с помощью цифрового пера. Несмотря на то, что набор средств сообщества Windows не предоставляет упакованную версию элемента управления UWP **CalendarView**, которую можно использовать непосредственно в приложении WPF, вы смогли разместить этот элемент управления с помощью универсального элемента управления [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost).

Теперь вы готовы перейти к статье [Часть 4. Добавление действий пользователей и уведомлений для Windows 10](modernize-wpf-tutorial-4.md)
