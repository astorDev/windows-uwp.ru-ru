---
title: создание приложения базы данных клиентов
description: Создайте приложение базы данных клиента и Узнайте, как реализовать базовые функции корпоративного приложения.
keywords: предприятие, учебник, клиент, данные, создание для чтения, удаление, ОСТАВШАЯся, проверка подлинности
ms.date: 05/07/2018
ms.topic: article
ms.localizationpriority: med
ms.openlocfilehash: b8cf0554464b56337e3d57b58db543092682ffa3
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258619"
---
# <a name="tutorial-create-a-customer-database-application"></a>Учебник. Создание приложения базы данных для клиента

В этом руководстве создается простое приложение для управления списком клиентов. Это позволяет выбрать основные понятия для корпоративных приложений в UWP. Вы научитесь выполнять следующие действия:

* Реализуйте операции создания, чтения, обновления и удаления в локальной базе данных SQL.
* Добавление сетки данных для просмотра и редактирования данных клиента в пользовательском интерфейсе.
* Размещение элементов пользовательского интерфейса в базовой структуре формы.

Отправной точкой для этого руководства является одностраничное приложение с минимальным пользовательским интерфейсом и функциональностью на основе упрощенной версии примера приложения для работы с [базой данных Customer Orders](https://github.com/Microsoft/Windows-appsample-customers-orders-database). Он написан на C# языке и XAML, и мы ожидаем, что у вас есть базовое знакомство с обоими языками.

![Главная страница рабочего приложения](images/customer-database-tutorial/customer-list.png)

### <a name="prerequisites"></a>Предварительные требования

* [Убедитесь, что у вас установлена последняя версия Visual Studio и пакет SDK для Windows 10.](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* [Клонирование или скачивание примера учебника по базе данных клиента](https://github.com/microsoft/windows-tutorials-customer-database)

После клонирования или скачивания репозитория можно изменить проект, открыв **кустомердатабасетуториал. sln** в Visual Studio.

> [!NOTE]
> Ознакомьтесь с [примером базы данных "полный заказ для клиентов](https://github.com/Microsoft/Windows-appsample-customers-orders-database) ", чтобы увидеть приложение, на котором основано это руководство.

## <a name="part-1-code-of-interest"></a>Часть 1. интересующий вас код

Если приложение запускается сразу после открытия, вы увидите несколько кнопок в верхней части пустого экрана. Хотя это невидимо для вас, приложение уже содержит локальную базу данных SQLite, подготовленную с помощью нескольких тестовых клиентов. Здесь вы начинаете с реализации элемента управления пользовательского интерфейса для вывода этих клиентов, а затем переходите к добавлению операций в базе данных. Прежде чем начать, здесь вы будете работать.

### <a name="views"></a>Представления

**Кустомерлистпаже. XAML** — это представление приложения, которое определяет пользовательский интерфейс для отдельной страницы в этом учебнике. Каждый раз, когда необходимо добавить или изменить визуальный элемент в пользовательском интерфейсе, это можно сделать в этом файле. В этом руководстве описано, как добавить следующие элементы:

* **Раддатагрид** для отображения и редактирования клиентов. 
* **StackPanel** для установки начальных значений для нового клиента.

### <a name="viewmodels"></a>ViewModels

**Виевмоделс\кустомерлистпажевиевмодел.КС** — это место, где находится основная логика приложения. Каждое действие пользователя, выполненное в представлении, будет передано в этот файл для обработки. В этом руководстве вы добавите новый код и реализуем следующие методы:

* **Креатеневкустомерасинк**, который инициализирует новый объект CustomerViewModel.
* **Делетеневкустомерасинк**, который удаляет нового клиента перед его отображением в пользовательском интерфейсе.
* **Делетеандупдатеасинк**, который обрабатывает логику кнопки удаления.
* **Жеткустомерлистасинк**, который получает список клиентов из базы данных.
* **Савеинитиалчанжесасинк**, который добавляет сведения о новом клиенте в базу данных.
* **Упдатекустомерсасинк**, который обновляет пользовательский интерфейс для отражения добавленных или удаленных клиентов.

**CustomerViewModel** — это программа-оболочка для информации клиента, которая отслеживает, был ли он недавно изменен. В этот класс не нужно добавлять ничего, но в него будет ссылаться другой код, который вы добавите в другое место.

Дополнительные сведения о создании образца см. в [обзоре структуры приложения](../enterprise/customer-database-app-structure.md).

## <a name="part-2-add-the-datagrid"></a>Часть 2. Добавление элемента управления DataGrid

Прежде чем приступить к работе с данными клиента, необходимо добавить элемент управления пользовательского интерфейса для вывода этих клиентов. Для этого мы будем использовать готовый элемент управления **раддатагрид** стороннего производителя. Пакет NuGet **Telerik. UI. for. UniversalWindowsPlatform** уже включен в этот проект. Давайте добавим сетку в наш проект.

1. Откройте **виевс\кустомерлистпаже.ксамл** из Обозреватель решений. Добавьте следующую строку кода в тег **страницы** , чтобы объявить сопоставление с пространством имен Telerik, содержащим сетку данных.

    ```xaml
        xmlns:telerikGrid="using:Telerik.UI.Xaml.Controls.Grid"
    ```

2. Под панелью **команд** в главной **релативепанел** представления добавьте элемент управления **раддатагрид** с некоторыми базовыми параметрами конфигурации:

    ```xaml
    <Grid
        x:Name="CustomerListRoot"
        Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <RelativePanel>
            <CommandBar
                x:Name="mainCommandBar"
                HorizontalAlignment="Stretch"
                Background="AliceBlue">
                <!--CommandBar content-->
            </CommandBar>
            <telerikGrid:RadDataGrid
                x:Name="DataGrid"
                BorderThickness="0"
                ColumnDataOperationsMode="Flyout"
                GridLinesVisibility="None"
                GroupPanelPosition="Left"
                RelativePanel.AlignLeftWithPanel="True"
                RelativePanel.AlignRightWithPanel="True"
                RelativePanel.Below="mainCommandBar" />
        </RelativePanel>
    </Grid>
    ```

3. Вы добавили сетку данных, но для ее вывода нужны данные. Добавьте в него следующие строки кода:

    ```xaml
    ItemsSource="{x:Bind ViewModel.Customers}"
    UserEditMode="Inline"
    ```
    Теперь, когда вы определили источник данных для вывода, **раддатагрид** будет выполнять большую часть логики пользовательского интерфейса. Однако при запуске проекта данные на экране по-прежнему не отображаются. Это обусловлено тем, что ViewModel еще не загружает его.

![Пустое приложение без клиентов](images/customer-database-tutorial/blank-customer-list.png)

## <a name="part-3-read-customers"></a>Часть 3. чтение клиентов

При инициализации **виевмоделс\кустомерлистпажевиевмодел.КС** вызывает метод **жеткустомерлистасинк** . Этот метод должен получить тестовые данные клиента из базы данных SQLite, входящей в этот учебник.

1. В **виевмоделс\кустомерлистпажевиевмодел.КС**Обновите метод **жеткустомерлистасинк** следующим кодом:

    ```csharp
    public async Task GetCustomerListAsync()
    {
        var customers = await App.Repository.Customers.GetAsync(); 
        if (customers == null)
        {
            return;
        }
        await DispatcherHelper.ExecuteOnUIThreadAsync(() =>
        {
            Customers.Clear();
            foreach (var c in customers)
            {
                Customers.Add(new CustomerViewModel(c));
            }
        });
    }
    ```
    Метод **жеткустомерлистасинк** вызывается при загрузке ViewModel, но до этого шага он ничего не делал. Здесь мы добавили вызов метода с методом **Async** в **репозитории или склкустомеррепоситори**. Это позволяет ему обращаться к репозиторию для получения перечислимой коллекции объектов Customer. Затем он анализирует их до отдельных объектов, прежде чем добавлять их к внутреннему **ObservableCollection** , чтобы их можно было отобразить и изменить.

2. Запустите приложение. Теперь вы увидите сетку данных, в которой отображается список клиентов.

![Первоначальный список клиентов](images/customer-database-tutorial/initial-customers.png)

## <a name="part-4-edit-customers"></a>Часть 4. изменение клиентов

Можно изменить записи в сетке данных, дважды щелкнув их, но необходимо убедиться, что любые изменения, вносимые в пользовательском интерфейсе, также будут внесены в коллекцию клиентов в коде программной части. Это означает, что необходимо реализовать двустороннюю привязку данных. Если вы хотите получить дополнительные сведения об этом, ознакомьтесь [с нашим введением в привязку данных](../get-started/display-customers-in-list-learning-track.md).

1. Сначала объявите, что **виевмоделс\кустомерлистпажевиевмодел.КС** реализует интерфейс **INotifyPropertyChanged** :

    ```csharp
    public class CustomerListPageViewModel : INotifyPropertyChanged
    ```

2. Затем в основном тексте класса добавьте следующее событие и метод:

    ```csharp
    public event PropertyChangedEventHandler PropertyChanged;

    public void OnPropertyChanged([CallerMemberName] string propertyName = null) =>
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    ```

    Метод **OnPropertyChanged** упрощает для методов задания вызов события **PropertyChanged** , которое необходимо для двусторонней привязки данных.

3. Обновите метод задания для **селектедкустомер** с помощью этого вызова функции:

    ```csharp
    public CustomerViewModel SelectedCustomer
    {
        get => _selectedCustomer;
        set
        {
            if (_selectedCustomer != value)
            {
                _selectedCustomer = value;
                OnPropertyChanged();
            }
        }
    }
    ```

4. В **виевс\кустомерлистпаже.ксамл**добавьте свойство **селектедкустомер** в сетку данных.

    ```xaml
    SelectedItem="{x:Bind ViewModel.SelectedCustomer, Mode=TwoWay}"
    ```

    Это связывает выбор пользователя в сетке данных с соответствующим объектом Customer в коде программной части. Режим привязки TwoWay позволяет отражать изменения, внесенные в пользовательском интерфейсе, в этот объект.

5. Запустите приложение. Теперь вы можете видеть клиентов, отображаемых в сетке, и вносить изменения в базовые данные с помощью пользовательского интерфейса.

![Изменение клиента в сетке данных](images/customer-database-tutorial/edit-customer-inline.png)

## <a name="part-5-update-customers"></a>Часть 5. обновление клиентов

Теперь, когда вы можете просматривать и изменять клиентов, необходимо иметь возможность отправлять изменения в базу данных и получать обновления, внесенные другими пользователями.

1. Вернитесь в **виевмоделс\кустомерлистпажевиевмодел.КС**и перейдите к методу **упдатекустомерсасинк** . Обновите его с помощью этого кода, чтобы отправить изменения в базу данных и получить новые сведения:

    ```csharp
    public async Task UpdateCustomersAsync()
    {
        foreach (var modifiedCustomer in Customers
            .Where(x => x.IsModified).Select(x => x.Model))
        {
            await App.Repository.Customers.UpsertAsync(modifiedCustomer);
        }
        await GetCustomerListAsync();
    }
    ```
    В этом коде используется свойство **виевмоделс\кустомервиевмодел.КС** **, которое автоматически** обновляется при каждом изменении клиента. Это позволяет избежать ненужных вызовов и отправлять изменения только от обновленных клиентов в базу данных.

## <a name="part-6-create-a-new-customer"></a>Часть 6. Создание нового клиента

Добавление нового клиента представляет проблему, так как клиент будет отображаться в виде пустой строки, если добавить его в пользовательский интерфейс перед указанием значений для его свойств. Это не проблема, но здесь мы упростили Задание начальных значений клиента. В этом учебнике мы добавим простую свертываемая панель, но если у вас есть дополнительные сведения для добавления, можно создать отдельную страницу для этой цели.

### <a name="update-the-code-behind"></a>Обновление кода программной части

1. Добавьте новое закрытое поле и общедоступное свойство в **виевмоделс\кустомерлистпажевиевмодел.КС**. Он будет использоваться для управления отображением панели.

    ```csharp
    private bool _addingNewCustomer = false;

    public bool AddingNewCustomer
    {
        get => _addingNewCustomer;
        set
        {
            if (_addingNewCustomer != value)
            {
                _addingNewCustomer = value;
                OnPropertyChanged();
            }
        }
    }
    ```

2. Добавьте в ViewModel новое открытое свойство, обратное значение **аддингневкустомер**. Он будет использоваться для отключения обычных кнопок панели команд при отображении панели.

    ```csharp
    public bool EnableCommandBar => !AddingNewCustomer;
    ```
    Теперь вам потребуется способ отобразить свертываемая панель и создать клиента для редактирования в ней. 

3. Добавьте новое закрытое свойство фиенд и public в ViewModel для хранения только что созданного клиента.

    ```csharp
    private CustomerViewModel _newCustomer;

    public CustomerViewModel NewCustomer
    {
        get => _newCustomer;
        set
        {
            if (_newCustomer != value)
            {
                _newCustomer = value;
                OnPropertyChanged();
            }
        }
    }
    ```

2.  Обновите метод **креатеневкустомерасинк** , чтобы создать нового клиента, добавить его в репозиторий и задать его в качестве выбранного клиента:

    ```csharp
    public async Task CreateNewCustomerAsync()
    {
        CustomerViewModel newCustomer = new CustomerViewModel(new Models.Customer());
        NewCustomer = newCustomer;
        await App.Repository.Customers.UpsertAsync(NewCustomer.Model);
        AddingNewCustomer = true;
    }
    ```

3. Обновите метод **савеинитиалчанжесасинк** , чтобы добавить недавно созданного клиента в репозиторий, обновите пользовательский интерфейс и закройте панель.

    ```csharp
    public async Task SaveInitialChangesAsync()
    {
        await App.Repository.Customers.UpsertAsync(NewCustomer.Model);
        await UpdateCustomersAsync();
        AddingNewCustomer = false;
    }
    ```
4. Добавьте следующую строку кода в качестве конечной строки в методе задания для **аддингневкустомер**:

    ```csharp
    OnPropertyChanged(nameof(EnableCommandBar));
    ```

    Это обеспечит автоматическое обновление **енаблекоммандбар** при каждом изменении **аддингневкустомер** .

### <a name="update-the-ui"></a>Обновление пользовательского интерфейса

1. Вернитесь к **виевс\кустомерлистпаже.ксамл**и добавьте **StackPanel** со следующими свойствами между панелью **CommandBar** и сеткой данных:

    ```xaml
    <StackPanel
        x:Name="newCustomerPanel"
        Orientation="Horizontal"
        x:Load="{x:Bind ViewModel.AddingNewCustomer, Mode=OneWay}"
        RelativePanel.Below="mainCommandBar">
    </StackPanel>
    ```
    Атрибут **x:Load** гарантирует, что эта панель отображается только при добавлении нового клиента.

2. Внесите следующее изменение в расположение сетки данных, чтобы убедиться, что она перемещается вниз при появлении новой панели:

    ```xaml
    RelativePanel.Below="newCustomerPanel"
    ```

3. Обновите панель стека, используя четыре элемента управления **TextBox** . Они привязываются к отдельным свойствам нового клиента и позволяют изменять его значения перед добавлением в сетку данных.

    ```xaml
    <StackPanel
        x:Name="newCustomerPanel"
        Orientation="Horizontal"
        x:Load="{x:Bind ViewModel.AddingNewCustomer, Mode=OneWay}"
        RelativePanel.Below="mainCommandBar">
        <TextBox
            Header="First name"
            PlaceholderText="First"
            Margin="8,8,16,8"
            MinWidth="120"
            Text="{x:Bind ViewModel.NewCustomer.FirstName, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}"/>
        <TextBox
            Header="Last name"
            PlaceholderText="Last"
            Margin="0,8,16,8"
            MinWidth="120"
            Text="{x:Bind ViewModel.NewCustomer.LastName, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}"/>
        <TextBox
            Header="Address"
            PlaceholderText="1234 Address St, Redmond WA 00000"
            Margin="0,8,16,8"
            MinWidth="280"
            Text="{x:Bind ViewModel.NewCustomer.Address, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}"/>
        <TextBox
            Header="Company"
            PlaceholderText="Company"
            Margin="0,8,16,8"
            MinWidth="120"
            Text="{x:Bind ViewModel.NewCustomer.Company, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}"/>
    </StackPanel>
    ```

4. Добавьте простую кнопку на новую панель стека, чтобы сохранить только что созданного клиента:

    ```xaml
    <StackPanel>
        <!--Text boxes from step 3-->
        <AppBarButton
            x:Name="SaveNewCustomer"
            Click="{x:Bind ViewModel.SaveInitialChangesAsync}"
            Icon="Save"/>
    </StackPanel>
    ```

5. Обновите панель **команд**, чтобы при отображении панели стека были отключены стандартные кнопки Создать, удалить и обновить.

    ```xaml
    <CommandBar
        x:Name="mainCommandBar"
        HorizontalAlignment="Stretch"
        IsEnabled="{x:Bind ViewModel.EnableCommandBar, Mode=OneWay}"
        Background="AliceBlue">
        <!--App bar buttons-->
    </CommandBar>
    ```

6. Запустите приложение. Теперь можно создать клиента и ввести его данные на панели стека.

![Создание нового клиента](images/customer-database-tutorial/add-new-customer.png)

## <a name="part-7-delete-a-customer"></a>Часть 7. Удаление клиента

Удаление клиента — это окончательная базовая операция, которую необходимо реализовать. При удалении клиента, выбранного в сетке данных, необходимо немедленно вызвать **упдатекустомерсасинк** , чтобы обновить пользовательский интерфейс. Однако вам не нужно вызывать этот метод, если вы удаляете только что созданный клиент.

1. Перейдите по адресу **виевмоделс\кустомерлистпажевиевмодел.КС**и обновите метод **делетеандупдатеасинк** :

    ```csharp
    public async void DeleteAndUpdateAsync()
    {
        if (SelectedCustomer != null)
        {
            await App.Repository.Customers.DeleteAsync(_selectedCustomer.Model.Id);
        }
        await UpdateCustomersAsync();
    }
    ```

2. В **виевс\кустомерлистпаже.ксамл**обновите панель стека, чтобы добавить нового клиента, чтобы он содержал вторую кнопку:

    ```xaml
    <StackPanel>
        <!--Text boxes for adding a new customer-->
        <AppBarButton
            x:Name="DeleteNewCustomer"
            Click="{x:Bind ViewModel.DeleteNewCustomerAsync}"
            Icon="Cancel"/>
        <AppBarButton
            x:Name="SaveNewCustomer"
            Click="{x:Bind ViewModel.SaveInitialChangesAsync}"
            Icon="Save"/>
    </StackPanel>
    ```

3. В **виевмоделс\кустомерлистпажевиевмодел.КС**Обновите метод **делетеневкустомерасинк** , чтобы удалить нового клиента:

    ```csharp
    public async Task DeleteNewCustomerAsync()
    {
        if (NewCustomer != null)
        {
            await App.Repository.Customers.DeleteAsync(_newCustomer.Model.Id);
            AddingNewCustomer = false;
        }
    }
    ```

4. Запустите приложение. Теперь можно удалить клиентов либо в сетке данных, либо на панели стека.

![Удаление нового клиента](images/customer-database-tutorial/delete-new-customer.png)

## <a name="conclusion"></a>Заключение

Поздравляем! Теперь, когда все это сделано, ваше приложение будет иметь полный спектр операций локальной базы данных. Вы можете создавать, читать, обновлять и удалять клиентов в пользовательском интерфейсе, и эти изменения сохраняются в базе данных и сохраняются в разных запусках приложения.

Теперь, когда все готово, учитывайте следующее.
* Если вы еще этого не сделали, ознакомьтесь с [обзором структуры приложения](../enterprise/customer-database-app-structure.md) , чтобы получить дополнительные сведения о том, почему приложение строится.
* Изучите [пример полной базы данных Customer Orders](https://github.com/Microsoft/Windows-appsample-customers-orders-database) , чтобы увидеть приложение, на котором основано это руководство.

Если у вас возникла проблема, можно продолжить...

## <a name="going-further-connect-to-a-remote-database"></a>Дальнейшие действия: подключение к удаленной базе данных

Мы предоставили пошаговое руководство по реализации этих вызовов в локальной базе данных SQLite. Но что если вместо этого вы хотите использовать удаленную базу данных?

Если вы хотите сделать это, вам потребуется собственная учетная запись Azure Active Directory (AAD) и возможность размещения собственного источника данных.

Необходимо добавить проверку подлинности, функции для управления вызовами RESTFUL, а затем создать удаленную базу данных для взаимодействия. В [образце полной базы данных Customer Orders](https://github.com/Microsoft/Windows-appsample-customers-orders-database) имеется код, на который можно ссылаться для каждой необходимой операции.

### <a name="settings-and-configuration"></a>Параметры и конфигурация

Необходимые действия для подключения к собственной удаленной базе данных приведены в [файле сведений примера](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/README.md). Необходимо выполнить следующие действия.

* Укажите идентификатор клиента учетной записи Azure для [Constants.CS](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoRepository/Constants.cs).
* Укажите URL-адрес удаленной базы данных для [Constants.CS](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoRepository/Constants.cs).
* Укажите строку подключения для базы данных [Constants.CS](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoRepository/Constants.cs).
* Свяжите приложение с Microsoft Store.
* Скопируйте [проект службы](https://github.com/Microsoft/Windows-appsample-customers-orders-database/tree/master/ContosoService) в приложение и разверните его в Azure.

### <a name="authentication"></a>Authentication

Необходимо создать кнопку для запуска последовательности проверки подлинности, а также всплывающее окно или отдельную страницу для сбора сведений о пользователе. После создания этого кода необходимо предоставить код, который запрашивает сведения о пользователе и использует его для получения маркера доступа. Пример базы данных Customer Orders заключает Microsoft Graph вызовы в библиотеку **вебаккаунтманажер** для получения маркера и выполнения проверки подлинности для учетной записи AAD.

* Логика проверки подлинности реализована в [**AuthenticationViewModel.CS**](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoApp/ViewModels/AuthenticationViewModel.cs).
* Процесс проверки подлинности отображается в пользовательском элементе управления [**аусентикатионконтрол. XAML**](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoApp/UserControls/AuthenticationControl.xaml) .

### <a name="rest-calls"></a>Вызовы RESTFUL

Вам не потребуется изменять код, добавленный в этом руководстве, для реализации вызовов RESTFUL. Вместо этого необходимо выполнить следующие действия.

* Создайте новые реализации интерфейсов **икустомеррепоситори** и **итуториалрепоситори** , реализуя тот же набор функций с помощью функции RESTful вместо SQLite. Необходимо выполнить сериализацию и десериализовать JSON, а также при необходимости переключить вызовы остальных в отдельный класс **хттфелпер** . См. [полный пример](https://github.com/Microsoft/Windows-appsample-customers-orders-database/tree/master/ContosoRepository/Rest) для конкретных особенностей.
* В **app.XAML.CS**создайте новую функцию для инициализации репозитория RESTful и вызовите ее вместо **склитедатабасе** при инициализации приложения. См. [полный пример](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoApp/App.xaml.cs).

После выполнения всех трех этапов вы сможете пройти проверку подлинности в вашей учетной записи AAD с помощью приложения. Вызовы функции RESTFUL к удаленной базе данных будут заменять локальные вызовы SQLite, но взаимодействие с пользователем должно быть одинаковым. Если вы еще амбициозные, можно добавить страницу параметров, чтобы разрешить пользователю динамически переключаться между ними.
