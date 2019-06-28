---
description: Этот учебник демонстрирует добавление пользовательских интерфейсах XAML UWP, создание MSIX пакеты и другие современные компоненты включить в приложении WPF.
title: Добавьте элемент управления представления календаря для универсальной платформы Windows, с помощью XAML о-ва
ms.topic: article
ms.date: 06/27/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, универсальной платформы Windows, windows forms, wpf, о-ва xaml
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: fce9135267461f61515c7dc04bbaf43b1e4c04ed
ms.sourcegitcommit: 1eec0e4fd8a5ba82803fdce6e23fcd01b9488523
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2019
ms.locfileid: "67420110"
---
# <a name="part-3-add-a-uwp-calendarview-control-using-xaml-islands"></a>Часть 3. Добавьте элемент управления представления календаря для универсальной платформы Windows, с помощью XAML о-ва

Это третья часть учебник, в котором показано, как модернизировать пример классического приложения WPF с именем Contoso расходы. Обзор учебника, предварительные требования и инструкции для загрузки примера приложения, см. в разделе [руководства: Модернизация приложения WPF](modernize-wpf-tutorial.md). В этой статье предполагается, что вы уже выполнили [часть 2](modernize-wpf-tutorial-2.md).

В вымышленной сценарии работы с этим руководством команда разработчиков компании Contoso хочет упростить выберите дату для отчета о расходах на устройстве с поддержкой сенсорного ввода. В этой части руководства вы добавите UWP [представления календаря](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/calendar-view) элемента управления в приложение. Это тот же элемент управления, который используется в Windows 10 функциях даты и времени на панели задач.

![Изображение CalendarViewControl](images/wpf-modernize-tutorial/CalendarViewControl.png)

В отличие от **InkCanvas** управления вы добавили в [часть 2](modernize-wpf-tutorial-2.md), Windows Community Toolkit не поддерживает оболочку версия UWP **представления календаря** , можно использовать в приложениях WPF . Кроме того, вы будете размещать **InkCanvas** в универсальном [WindowsXamlHost](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) элемента управления. Этот элемент управления можно использовать для размещения любого элемента управления универсальной платформы Windows, предоставляемое поставщиком платформы или сторонней сторона. **WindowsXamlHost** управления обеспечивается `Microsoft.Toolkit.Wpf.UI.XamlHost` пакет NuGet. Этот пакет входит в состав `Microsoft.Toolkit.Wpf.UI.Controls` пакет NuGet, установленного в [часть 2](modernize-wpf-tutorial-2.md).

Чтобы использовать **WindowsXamlHost** элемента управления, необходимо напрямую вызывать API-интерфейсы WinRT из кода в приложении WPF. `Microsoft.Windows.SDK.Contracts` Пакет NuGet содержит ссылки, необходимые для вызова API WinRT из приложения. Этот пакет также входит в `Microsoft.Toolkit.Wpf.UI.Controls` пакет NuGet, установленного в [часть 2](modernize-wpf-tutorial-2.md).

## <a name="add-the-windowsxamlhost-control"></a>Добавьте элемент управления WindowsXamlHost

1. В **обозревателе решений**, разверните **представления** папку в **ContosoExpenses.Core** проекта и дважды щелкните **AddNewExpense.xaml**файл. Это форма, используемая для добавления в список новый расход. Здесь показано, как он выглядит в текущей версии приложения.

    ![Добавить новый расход](images/wpf-modernize-tutorial/AddNewExpense.png)

    Элемента выбора даты, включенных в WPF предназначен для традиционных компьютеров с помощью мыши и клавиатуры. Выбор даты с сенсорным экраном не представляется возможным, из-за небольшого размера элемента управления и ограниченные пространства между каждый день в календаре.

2. В верхней части **AddNewExpense.xaml** добавьте следующий атрибут к **окно** элемент.

    ```xml
    xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

    После добавления этого атрибута **окно** элемент теперь должен выглядеть следующим образом.

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

3. Изменение **высота** атрибут **окно** элемента из 450 800. Это необходимо, поскольку UWP **представления календаря** управления занимает больше места, чем этот элемент управления WPF.

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

4. Найдите `DatePicker` элемент ближе к концу файла и замените этот элемент в следующем XAML.

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp"  />
    ```

    Добавляет этот XAML **WindowsXamlHost** элемента управления. **InitialTypeName** свойство указывает полное имя элемента управления универсальной платформы Windows, необходимая для размещения (в этом случае **Windows.UI.Xaml.Controls.CalendarView**).

5. Нажмите клавишу F5 для сборки и запуска приложения в отладчике. Выберите сотрудник из списка и нажмите клавишу **добавить новый расход** кнопки. Убедитесь, что следующая страница размещает новый универсальной платформы Windows **представления календаря** элемента управления.

    ![Оболочка представления календаря](images/wpf-modernize-tutorial/CalendarViewWrapper.png)

6. Закройте приложение.

## <a name="interact-with-the-windowsxamlhost-control"></a>Взаимодействовать с элементом управления WindowsXamlHost

Далее, будет показано обновление приложения для обработки выбранной даты, отобразить ее на экране и заполнить **расходов** объекта для сохранения в базе данных.

UWP [представления календаря](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) содержит два члена, которые необходимы для этого сценария:

- **SelectedDates** свойство содержит дату, выбранного пользователем.
- **SelectedDatesChanged** событие возникает, когда пользователь выбирает дату.

Тем не менее **WindowsXamlHost** элемент управления является универсальным узлом для *любой* вида элемента управления универсальной платформы Windows. Таким образом, он не предоставляет свойство с именем **SelectedDates** или вызывать событие **SelectedDatesChanged**, поскольку они зависят от **представления календаря** элемента управления. Для использования этих элементов, необходимо написать код, к которому приводятся **WindowsXamlHost** для **представления календаря** типа. Лучший способ это сделать: в ответ на **ChildChanged** событие **WindowsXamlHost** элемент управления, который будет выводиться, когда размещенный элемент управления визуализирован.

1. В **AddNewExpense.xaml** файл, добавьте обработчик событий для **ChildChanged** событие **WindowsXamlHost** добавленного ранее элемента управления. Когда вы закончите, **WindowsXamlHost** элемент должен выглядеть следующим образом.

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp"  ChildChanged="CalendarUwp_ChildChanged" />
    ```

2. В этом же файле найдите **Grid.RowDefinitions** элемент основной **сетки**. Добавьте еще одно **элемент узла RowDefinition** элемент с **высота** равным **автоматически** в конце списка дочерних элементов. Когда вы закончите, **Grid.RowDefinitions** элемент должен выглядеть следующим образом (должно появиться 9 **элемент узла RowDefinition** элементов).

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

4. Добавьте следующий XAML после **WindowsXamlHost** элемента и до **кнопку** элемент в конце файла.

    ```xml
    <TextBlock Text="Selected date:" FontSize="16" FontWeight="Bold" Grid.Row="7" Grid.Column="0" />
    <TextBlock Text="{Binding Path=Date}" FontSize="16" Grid.Row="7" Grid.Column="1" />
    ```

5. Найдите **кнопку** элемент в конце файла и измените **Grid.Row** свойства из **7** для **8**. Это переносит кнопки вниз на одну строку в сетке, из-за добавления новой строки.

    ```xml
    <Button Content="Save" Grid.Row="8" Grid.Column="0" Command="{Binding Path=SaveExpenseCommand}" Margin="5, 12, 0, 0" HorizontalAlignment="Left" Width="180" />
    ```

6. Откройте **AddNewExpense.xaml.cs** файл кода.

7. Добавьте следующую инструкцию в начало файла.

    ```csharp
    using Microsoft.Toolkit.Wpf.UI.XamlHost;
    ```

8. Добавьте следующий обработчик событий к `AddNewExpense` класса. Этот код реализует **ChildChanged** событие **WindowsXamlHost** управления, объявленный ранее в файле XAML.

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

    Этот код использует **дочерних** свойство **WindowsXamlHost** управления для доступа к UWP **представления календаря** элемента управления. Код, после чего подписывается на **SelectedDatesChanged** событие, которое активируется, когда пользователь выбирает дату из календаря. Этот обработчик событий передает выбранной даты ViewModel где нового **расходов** объект создается и сохранены в базе данных. Чтобы сделать это, код использует инфраструктуру обмена сообщениями, предоставляемые пакетом MVVM Light NuGet. Код отправляет сообщение вызывается **SelectedDateMessage** в ViewModel, который будет его получают и задайте **даты** свойство с выбранным значением. В этом сценарии **представления календаря** управления настроен в режиме одиночного выбора, коллекция будет содержать только один элемент.

    На этом этапе проект по-прежнему не будет компилироваться, так как в нем отсутствует реализация **SelectedDateMessage** класса. Ниже приведены инструкции по реализации этого класса.

9. В **обозревателе решений**, щелкните правой кнопкой мыши **сообщений** папку и выберите **Добавить -> класс**. Назовите новый класс **SelectedDateMessage** и нажмите кнопку **добавить**.

10. Замените содержимое файла **SelectedDateMessage.cs** файла кода следующим кодом. Этот код добавляет using инструкции для `GalaSoft.MvvmLight.Messaging` пространства имен (с использованием пакета MVVM Light NuGet) наследует от **MessageBase** и добавляет **DateTime** свойство, которое инициализируется с помощью открытый конструктор. Когда вы закончите, файл должен выглядеть следующим образом.

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

11. Далее вы обновите ViewModel для этого сообщения и заполнения **даты** свойство ViewModel. В **обозревателе решений**, разверните **ViewModels** и откройте **AddNewExpenseViewModel.cs** файла.

12. Найдите открытый конструктор для `AddNewExpenseViewModel` класса и добавьте следующий код в конец конструктора. Этот код регистрируется для получения **SelectedDateMessage**, извлечения выбранной даты из его с помощью **SelectedDate** свойство и использовать его для задания **даты** свойство предоставляемые ViewModel. Так как это свойство связано с **TextBlock** элемента управления, добавленный ранее, вы увидите даты, выбранного пользователем.

    ```csharp
    Messenger.Default.Register<SelectedDateMessage>(this, message =>
    {
        Date = message.SelectedDate;
    });
    ```

    Когда вы закончите, `AddNewExpenseViewModel` конструктор должен выглядеть следующим образом. 

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
    > `SaveExpenseCommand` Метод в одном файле кода выполняет работу по сохранении расходы в базу данных. Этот метод использует **даты** свойства для создания нового **расходов** объекта. Это свойство заполняется теперь по **представления календаря** управлять через `SelectedDateMessage` сообщение, только что создан.

13. Нажмите клавишу F5 для сборки и запуска приложения в отладчике. Выберите один из доступных сотрудников и нажмите кнопку **добавить новый расход** кнопки. Заполните все поля в форме, а затем выберите дату из новой **представления календаря** элемента управления. Убедитесь, что выбранная дата отображается в виде строки под элементом управления.

14. Нажмите клавишу **Сохранить** кнопки. Форма будет закрыта и новый расход добавляется в конец списка расходов. Первый столбец с датой расходов должен быть даты, выбранной на **представления календаря** элемента управления.

## <a name="next-steps"></a>Следующие шаги

На этом этапе в этом руководстве, вы успешно заменили элемента WPF даты и времени с помощью UWP **представления календаря** элемент управления, который поддерживает сенсорное управление и цифровых перья, в дополнение к мыши и клавиатуры. Несмотря на то, что в набор инструментов сообщества Windows не предоставляет оболочку версию универсальной платформы Windows **представления календаря** элемента управления, который может использоваться непосредственно в приложении WPF, можно было разместить элемент управления с помощью универсального [WindowsXamlHost ](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) элемента управления.

Теперь вы готовы для [часть 4: Добавление действия пользователей Windows 10 и уведомления](modernize-wpf-tutorial-4.md).
