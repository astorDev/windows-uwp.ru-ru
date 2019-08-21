---
description: В этом руководстве показано, как добавлять пользовательские интерфейсы XAML UWP, создавать пакеты MSIX и внедрять в приложение WPF другие современные компоненты.
title: Добавление элемента управления CalendarView универсальной платформы Windows с помощью XAML Islands
ms.topic: article
ms.date: 06/27/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, UWP, Windows Forms, WPF, о-ва XAML
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 830c1cdf2e24e716d51642bc65b5b6783d0d784a
ms.sourcegitcommit: 6bb794c6e309ba543de6583d96627fbf1c177bef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69643375"
---
# <a name="part-3-add-a-uwp-calendarview-control-using-xaml-islands"></a>Часть 3. Добавление элемента управления CalendarView универсальной платформы Windows с помощью XAML Islands

Это третья часть учебника, в котором показано, как модернизировать пример классического приложения WPF с именем Contoso расходы. Общие сведения о руководстве, предварительных требованиях и инструкциях по скачиванию примера приложения см [. в разделе Учебник. Модернизировать приложение](modernize-wpf-tutorial.md)WPF. В этой статье предполагается, что вы уже выполнили [часть 2](modernize-wpf-tutorial-2.md).

В вымышленном сценарии в рамках этого руководства группа разработки Contoso хочет упростить выбор даты для отчета о расходах на устройстве с поддержкой сенсорного ввода. В этой части руководства вы добавите в приложение элемент управления UWP [календарвиев](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/calendar-view) . Это тот же элемент управления, который используется в функциях даты и времени Windows 10 на панели задач.

![Изображение Календарвиевконтрол](images/wpf-modernize-tutorial/CalendarViewControl.png)

В отличие от элемента управления **InkCanvas** , добавленного во [второй части](modernize-wpf-tutorial-2.md), набор средств Windows Community Toolkit не предоставляет упакованную версию **календарвиев** UWP, которую можно использовать в приложениях WPF. В качестве альтернативы можно разместить **InkCanvas** в универсальном элементе управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) . Этот элемент управления можно использовать для размещения любого первого элемента управления UWP, предоставляемого Windows SDK или библиотекой Винуи или любого пользовательского элемента управления UWP, созданного третьим лицом. Элемент управления **виндовсксамлхост** предоставляется `Microsoft.Toolkit.Wpf.UI.XamlHost` пакетом NuGet пакета. Этот пакет входит `Microsoft.Toolkit.Wpf.UI.Controls` в пакет NuGet, установленный в [части 2](modernize-wpf-tutorial-2.md).

> [!NOTE]
> В этом учебнике показано, как использовать [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) для размещения первого элемента управления **календарвиев** , предоставляемого Windows SDK. Пошаговое руководство, в котором показано, как разместить пользовательский элемент управления, см. [в разделе Размещение пользовательского элемента управления UWP в приложении WPF с помощью XAML-ва](host-custom-control-with-xaml-islands.md).

Чтобы использовать элемент управления **виндовсксамлхост** , необходимо напрямую вызывать API-интерфейсы WinRT из кода в приложении WPF. Пакет `Microsoft.Windows.SDK.Contracts` NuGet содержит ссылки, необходимые для вызова API-интерфейсов WinRT из приложения. Этот пакет также входит в `Microsoft.Toolkit.Wpf.UI.Controls` пакет NuGet, установленный в [части 2](modernize-wpf-tutorial-2.md).

## <a name="add-the-windowsxamlhost-control"></a>Добавление элемента управления Виндовсксамлхост

1. В **Обозреватель решений**разверните папку **представления** в проекте **контосоекспенсес. Core** и дважды щелкните файл **аддневекспенсе. XAML** . Это форма, используемая для добавления нового расхода в список. Вот как оно отображается в текущей версии приложения.

    ![Добавить новый расход](images/wpf-modernize-tutorial/AddNewExpense.png)

    Элемент управления "Выбор даты", включенный в WPF, предназначен для традиционных компьютеров с мышью и клавиатурой. Выбор даты с сенсорным экраном не является реальностью из-за небольшого размера элемента управления и ограниченного пространства между каждым днем в календаре.

2. В верхней части файла **аддневекспенсе. XAML** добавьте следующий атрибут в элемент **Window** .

    ```xml
    xmlns:xamlhost="clr-namespace:Microsoft.Toolkit.Wpf.UI.XamlHost;assembly=Microsoft.Toolkit.Wpf.UI.XamlHost"
    ```

    После добавления этого атрибута элемент **Window** теперь должен выглядеть следующим образом.

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

3. Измените атрибут **Height** элемента **Window** с 450 на 800. Это необходимо, поскольку элемент управления **КАЛЕНДАРВИЕВ** UWP занимает больше пространства, чем средство выбора даты WPF.

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

4. Найдите элемент `DatePicker` в нижней части файла и замените этот элемент следующим кодом XAML.

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp"  />
    ```

    Этот XAML добавляет элемент управления **виндовсксамлхост** . Свойство **инитиалтипенаме** указывает полное имя элемента управления UWP, который требуется разместить (в данном случае **Windows. UI. XAML. Controls. календарвиев**).

5. Нажмите клавишу F5, чтобы создать и запустить приложение в отладчике. Выберите сотрудника из списка и нажмите кнопку **Добавить новый расход** . Убедитесь, что на следующей странице размещается новый элемент управления UWP **календарвиев** .

    ![Оболочка Календарвиев](images/wpf-modernize-tutorial/CalendarViewWrapper.png)

6. Закройте приложение.

## <a name="interact-with-the-windowsxamlhost-control"></a>Взаимодействие с элементом управления Виндовсксамлхост

Затем вы обновите приложение, чтобы обработать выбранную дату, отобразите его на экране и заполните объект **расходов** для сохранения в базе данных.

[КАЛЕНДАРВИЕВ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.CalendarView) UWP содержит два элемента, которые относятся к этому сценарию:

- Свойство **SelectedDates** содержит дату, выбранную пользователем.
- Событие **селектеддатесчанжед** возникает, когда пользователь выбирает дату.

Однако элемент управления **виндовсксамлхост** является универсальным элементом управления ведущего приложения для *любого* типа элемента управления UWP. Таким образом, он не предоставляет свойство с именем **SelectedDates** или событие с именем **селектеддатесчанжед**, поскольку они являются особыми для элемента управления **календарвиев** . Чтобы получить доступ к этим членам, необходимо написать код, который приводит **виндовсксамлхост** к типу **календарвиев** . Лучше это сделать в ответ на событие **чилдчанжед** элемента управления **виндовсксамлхост** , которое возникает при подготовке к просмотру размещенного элемента управления.

1. В файле **аддневекспенсе. XAML** добавьте обработчик событий для события **Чилдчанжед** элемента управления **виндовсксамлхост** , добавленного ранее. По завершении элемент **виндовсксамлхост** должен выглядеть следующим образом.

    ```xml
    <xamlhost:WindowsXamlHost InitialTypeName="Windows.UI.Xaml.Controls.CalendarView" Grid.Column="1" Grid.Row="6" Margin="5, 0, 0, 0" x:Name="CalendarUwp"  ChildChanged="CalendarUwp_ChildChanged" />
    ```

2. В том же файле выберите элемент **Grid. RowDefinitions** основной **сетки**. Добавьте еще один элемент **RowDefinition** с **высотой** , равным **Auto** , в конце списка дочерних элементов. По завершении элемент **Grid. RowDefinitions** должен выглядеть следующим образом (теперь должен быть 9 **RowDefinition** элементов).

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

4. Добавьте следующий код XAML после элемента **виндовсксамлхост** и перед элементом **Button** рядом с концом файла.

    ```xml
    <TextBlock Text="Selected date:" FontSize="16" FontWeight="Bold" Grid.Row="7" Grid.Column="0" />
    <TextBlock Text="{Binding Path=Date}" FontSize="16" Grid.Row="7" Grid.Column="1" />
    ```

5. Найдите элемент **Button** рядом с концом файла и измените значение свойства **Grid. Row** с **7** на **8**. Это сдвигает кнопку на одну строку в сетке, так как вы добавили новую строку.

    ```xml
    <Button Content="Save" Grid.Row="8" Grid.Column="0" Command="{Binding Path=SaveExpenseCommand}" Margin="5, 12, 0, 0" HorizontalAlignment="Left" Width="180" />
    ```

6. Откройте файл кода **AddNewExpense.XAML.CS** .

7. Добавьте следующий оператор в начало файла.

    ```csharp
    using Microsoft.Toolkit.Wpf.UI.XamlHost;
    ```

8. Добавьте в `AddNewExpense` класс следующий обработчик событий. Этот код реализует событие **чилдчанжед** элемента управления **виндовсксамлхост** , объявленного ранее в файле XAML.

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

    Этот код использует свойство **Child** элемента управления **виндовсксамлхост** для доступа к элементу управления **календарвиев** UWP. Затем код подписывается на событие **селектеддатесчанжед** , которое активируется, когда пользователь выбирает дату из календаря. Этот обработчик событий передает выбранную дату в ViewModel, где новый объект **расходов** создается и сохраняется в базе данных. Для этого в коде используется инфраструктура обмена сообщениями, предоставляемая пакетом NuGet MVVM Light. Код отправляет сообщение с именем **селектеддатемессаже** в ViewModel, который получает его и устанавливает свойство **Date** с выбранным значением. В этом сценарии элемент управления **календарвиев** настраивается для режима одиночного выбора, поэтому коллекция будет содержать только один элемент.

    На этом этапе проект по-прежнему не компилируется, так как в нем отсутствует реализация для класса **селектеддатемессаже** . Следующие шаги реализуют этот класс.

9. В **Обозреватель решений**щелкните правой кнопкой мыши папку **сообщения** и выберите **Добавить-> класс**. Назовите новый класс **селектеддатемессаже** и нажмите кнопку **Добавить**.

10. Замените содержимое файла кода **SelectedDateMessage.CS** следующим кодом. Этот код добавляет инструкцию using для `GalaSoft.MvvmLight.Messaging` пространства имен (из пакета MVVM Light NuGet), наследует от класса **мессажебасе** и добавляет свойство **DateTime** , которое инициализируется с помощью открытого конструктора. По завершении файл должен выглядеть следующим образом.

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

11. Затем обновите ViewModel, чтобы получить это сообщение и заполнить свойство **Date** объекта ViewModel. В **Обозреватель решений**разверните папку **ViewModels** и откройте файл **AddNewExpenseViewModel.CS** .

12. Нахождение открытого конструктора для `AddNewExpenseViewModel` класса и добавьте следующий код в конец конструктора. Этот код регистрируется для получения **селектеддатемессаже**, извлечения выбранной даты из него через свойство **SelectedDate** , и мы используем его для задания свойства **Date** , предоставленного ViewModel. Так как это свойство привязано к элементу управления **TextBlock** , добавленному ранее, можно увидеть дату, выбранную пользователем.

    ```csharp
    Messenger.Default.Register<SelectedDateMessage>(this, message =>
    {
        Date = message.SelectedDate;
    });
    ```

    По завершении `AddNewExpenseViewModel` конструктор должен выглядеть следующим образом. 

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
    > `SaveExpenseCommand` Метод в том же файле кода выполняет сохранение расходов в базе данных. Этот метод использует свойство **Date** для создания нового объекта **расходов** . Теперь это свойство заполняется элементом управления **календарвиев** через `SelectedDateMessage` только что созданное сообщение.

13. Нажмите клавишу F5, чтобы создать и запустить приложение в отладчике. Выберите одного из доступных сотрудников, а затем нажмите кнопку **Добавить новый расход** . Заполните все поля в форме и выберите дату из нового элемента управления **календарвиев** . Подтвердите, что выбранная дата отображается в виде строки под элементом управления.

14. Нажмите кнопку **сохранить** . Форма будет закрыта, а новые расходы будут добавлены в конец списка расходов. Первым столбцом с датой расходов должна быть Дата, выбранная в элементе управления **календарвиев** .

## <a name="next-steps"></a>Следующие шаги

На этом этапе работы с руководством вы успешно заменили элемент управления "Дата и время" WPF с помощью элемента управления UWP **календарвиев** , который поддерживает сенсорные и Цифровые перья в дополнение к вводу с помощью мыши и клавиатуры. Несмотря на то, что набор средств Windows Community Toolkit не предоставляет упакованную версию элемента управления UWP **календарвиев** , который можно использовать непосредственно в приложении WPF, вы смогли разместить элемент управления с помощью универсального элемента управления [виндовсксамлхост](https://docs.microsoft.com/windows/communitytoolkit/controls/wpf-winforms/windowsxamlhost) .

Теперь вы готовы [к части 4: Добавление действий и уведомлений](modernize-wpf-tutorial-4.md)пользователей Windows 10.
