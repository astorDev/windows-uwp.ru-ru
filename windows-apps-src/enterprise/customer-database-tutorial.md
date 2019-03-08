---
title: Создание приложения базы данных клиента
description: Создание приложения базы данных клиента и узнайте, как реализовать базовые функции приложения.
keywords: Корпоративный, руководства, клиент, данных, создание проверкой подлинности delete, REST, чтение, обновление
ms.date: 05/07/2018
ms.topic: article
ms.localizationpriority: med
ms.openlocfilehash: 9c09e0fb73e42fd8a3d0c70bbb5396be32624387
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57623249"
---
# <a name="tutorial-create-a-customer-database-application"></a>Учебник. Создание приложения базы данных клиента

В этом руководстве создается простое приложение для управления список клиентов. При этом в нем представлена подборка основные понятия для корпоративных приложений на универсальной платформы Windows. Вы научитесь выполнять следующие действия:

* Реализуйте создания, чтения, обновления и удаления, выполняемых над локальной базы данных SQL.
* Добавьте таблицу данных для отображения и изменения данных клиента в пользовательский Интерфейс.
* Упорядочивание элементов пользовательского интерфейса в простой макет формы.

Начальной точкой для этого учебника является одностраничного приложения с минимальными пользовательского интерфейса и функций, в зависимости от упрощенную версию [примера приложения базы данных заказов клиента](https://github.com/Microsoft/Windows-appsample-customers-orders-database). Оно написано на C# и XAML и мы ожидаете, у вас есть базовые знания обоих этих языков.

![На главную страницу работающее приложение](images/customer-database-tutorial/customer-list.png)

### <a name="prerequisites"></a>Предварительные условия

* [Убедитесь, что у вас есть последнюю версию Visual Studio и пакета SDK для Windows 10](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* [Клонируйте или скачайте пример руководство по базам данных клиента](https://aka.ms/customer-database-tutorial)

После клонирования/загрузки репозитория, можно изменить проект, открыв **CustomerDatabaseTutorial.sln** с помощью Visual Studio.

> [!NOTE]
> Ознакомьтесь с [полный образец базы данных заказов клиента](https://github.com/Microsoft/Windows-appsample-customers-orders-database) чтобы увидеть этот учебник был основан на приложение.

## <a name="part-1-code-of-interest"></a>Часть 1. Интересующего кода

Если запустить приложение сразу после его открытия, вы увидите несколько кнопок в верхней части пустой экран. Если он не видны вам, приложение уже включает в себя локальной базы данных SQLite, предоставлены некоторые пользователи теста. На этой странице вы начнем путем реализации элемента управления пользовательского интерфейса для отображения этих клиентов и затем перейдем к добавлению операций в базе данных. Прежде чем начать, Вот где вы будете работать.

### <a name="views"></a>Представления

**CustomerListPage.xaml** просмотра приложения, который определяет пользовательский Интерфейс для одной страницы в этом руководстве. Каждый раз, когда вам нужно добавить или изменить визуальный элемент в пользовательском Интерфейсе, мы сделаем это в этом файле. Этот учебник поможет добавить эти элементы:

* Объект **RadDataGrid** для отображения и редактирования клиентов. 
* Объект **StackPanel** задать начальные значения для нового клиента.

### <a name="viewmodels"></a>ViewModels

**ViewModels\CustomerListPageViewModel.cs** является, где находится основной логики приложения. Каждое действие пользователя, выполняемое в представлении будут переданы в этот файл для обработки. В этом руководстве будет добавим новый код и реализовывать следующие методы:

* **CreateNewCustomerAsync**, который инициализирует новый объект CustomerViewModel.
* **DeleteNewCustomerAsync**, которая удаляет нового клиента до его отображения в пользовательском Интерфейсе.
* **DeleteAndUpdateAsync**, обрабатывающая логики «удалить».
* **GetCustomerListAsync**, который получает список клиентов из базы данных.
* **SaveInitialChangesAsync**, который добавляет сведения нового клиента в базу данных.
* **UpdateCustomersAsync**, которая обновляет пользовательский Интерфейс в соответствии с клиентов добавлены или удалены.

**CustomerViewModel** является оболочкой для информации клиента, который отслеживает ли он является был недавно изменен. Вам не нужно ничего добавлять к этому классу, но некоторые из кода, который вы добавите в другом месте будет ссылаться на нее.

Дополнительные сведения о том, как создается образец извлечь [Общие сведения о структуре приложения](../enterprise/customer-database-app-structure.md).

## <a name="part-2-add-the-datagrid"></a>Часть 2. Добавьте элемент управления DataGrid

Прежде чем выполнять операции с данными клиента, необходимо добавить элемент управления пользовательского интерфейса для отображения этих клиентов. Чтобы сделать это, мы будем использовать готовый сторонних **RadDataGrid** элемента управления. **Telerik.UI.for.UniversalWindowsPlatform** пакет NuGet уже был включен в этот проект. Давайте добавим сетки в свой проект.

1. Откройте **Views\CustomerListPage.xaml** из обозревателя решений. Добавьте следующую строку кода в **страницы** тег для объявления сопоставление Telerik пространство имен, содержащее сетке данных.

    ```xaml
        xmlns:telerikGrid="using:Telerik.UI.Xaml.Controls.Grid"
    ```

2. Ниже **CommandBar** в главном **RelativePanel** представления, добавьте **RadDataGrid** управление с помощью некоторые основные параметры конфигурации:

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

3. Вы добавили в сетке данных, но требуется его данные для отображения. К нему, добавьте следующие строки кода:

    ```xaml
    ItemsSource="{x:Bind ViewModel.Customers}"
    UserEditMode="Inline"
    ```
    Теперь, после определения источника данных для отображения, **RadDataGrid** будет обрабатывать большую часть логики пользовательского интерфейса для вас. Тем не менее если при запуске проекта, вы по-прежнему данные отображаться не на дисплее. Том, что ViewModel не загружать их еще.

![Пустое приложение, с помощью нет клиентов](images/customer-database-tutorial/blank-customer-list.png)

## <a name="part-3-read-customers"></a>Часть 3. Чтение клиентов

При инициализации, **ViewModels\CustomerListPageViewModel.cs** вызовы **GetCustomerListAsync** метод. Метод необходимо извлечь данные клиента теста из SQLite базы данных, включается в этом руководстве.

1. В **ViewModels\CustomerListPageViewModel.cs**, обновите ваш **GetCustomerListAsync** метод следующим кодом:

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
    **GetCustomerListAsync** метод вызывается при загрузке ViewModel, но перед этим шагом, ничего не произошло. Здесь мы добавили вызов **GetAsync** метод в **репозитория/SqlCustomerRepository**. Это позволит связаться для получения перечисляемую коллекцию объектов Customer в репозиторий. Затем анализирует их в отдельные объекты, прежде чем добавлять их в своих внутренних **ObservableCollection** , отображаются и редактируются.

2. Запустите свое приложение — теперь отображается сетка данных, отображение списка клиентов.

![Начальный список клиентов](images/customer-database-tutorial/initial-customers.png)

## <a name="part-4-edit-customers"></a>Часть 4. Изменение пользователей

Можно изменить, дважды щелкнув их записи в сетке данных, но необходимо убедиться, что любые изменения, внесенные в пользовательском Интерфейсе также выполняются в коллекцию клиентов в код программной части. Это означает, что вам придется реализовать двухстороннюю привязку данных. Если вам нужны дополнительные сведения об этом, ознакомьтесь с нашей [Общие сведения о привязке данных](../get-started/display-customers-in-list-learning-track.md).

1. Во-первых, объявления, **ViewModels\CustomerListPageViewModel.cs** реализует **INotifyPropertyChanged** интерфейса:

    ```csharp
    public class CustomerListPageViewModel : INotifyPropertyChanged
    ```

2. В основной части класса, добавьте следующие события и метод:

    ```csharp
    public event PropertyChangedEventHandler PropertyChanged;

    public void OnPropertyChanged([CallerMemberName] string propertyName = null) =>
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    ```

    **OnPropertyChanged** метод упрощает для вашего задания для вызова **PropertyChanged** событий, который необходим для двусторонней привязки данных.

3. Обновите метод задания значения **SelectedCustomer** с помощью этого вызова функции:

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

4. В **Views\CustomerListPage.xaml**, добавьте **SelectedCustomer** свойства сетки данных.

    ```xaml
    SelectedItem="{x:Bind ViewModel.SelectedCustomer, Mode=TwoWay}"
    ```

    Это связывает выбора пользователя в сетке данных с соответствующего объекта клиента в код программной части. Режим привязки TwoWay позволяет изменения, внесенные в пользовательском Интерфейсе отобразятся на этот объект.

5. Запустите приложение. Теперь можно узнать, какие клиенты, отображаемое в сетке и внести изменения в базовые данные через пользовательский Интерфейс.

![Изменение клиента в сетке данных](images/customer-database-tutorial/edit-customer-inline.png)

## <a name="part-5-update-customers"></a>Часть 5. Обновление клиентов

Теперь, когда вы могли просматривать и редактировать клиентов, необходимо иметь возможность отправить изменения в базе данных и извлекать все обновления, внесенные другими пользователями.

1. Вернитесь к **ViewModels\CustomerListPageViewModel.cs**и перейдите к **UpdateCustomersAsync** метод. Измените его, указав этот код, чтобы передать изменения в базу данных и получить никакой новой информации:

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
    Этот код использует **IsModified** свойство **ViewModels\CustomerViewModel.cs**, автоматически обновляется при каждом изменении клиента. Это позволяет избежать лишних обращений и только отправка изменений из обновленных клиентов к базе данных.

## <a name="part-6-create-a-new-customer"></a>Часть 6. Создание клиента

Представляет сложной задачей, добавления нового клиента, как клиент будет отображаться как пустая строка, при добавлении его к пользовательскому Интерфейсу перед заданием значения его свойств. Это не проблема, но здесь мы упростит для задания начальных значений для клиента. В этом руководстве мы добавим простой Сворачиваемая панель, но если у вас есть Дополнительные сведения, чтобы добавить для этой цели можно создать отдельную страницу.

### <a name="update-the-code-behind"></a>Обновление кода

1. Добавление нового поля закрытого и открытого свойства **ViewModels\CustomerListPageViewModel.cs**. Это будет использоваться для управления ли панель отображается.

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

2. Добавить новый открытое свойство модели представления Инверсия значение **AddingNewCustomer**. Это будет использоваться для отключения кнопки на панели команд регулярных когда панель отображается.

    ```csharp
    public bool EnableCommandBar => !AddingNewCustomer;
    ```
    Теперь необходимо возможности для отображения Сворачиваемая панель, а также для создания клиента для редактирования в ней. 

3. Добавьте новый fiend частного и общедоступного свойства ViewModel, для хранения созданного клиента.

    ```csharp
    private CustomerViewModel _newCustomer;

    public CustomerViewModel NewCustomer
    {
        get => _newCustomer;
        set
        {
            if {_newCustomer != value}
            {
                _newCustomer = value;
                OnPropertyChanged();
            }
        }
    }
    ```

2.  Обновление вашей **CreateNewCustomerAsync** метод для создания нового клиента, добавьте его в репозиторий и установите ее в качестве выбранного клиента:

    ```csharp
    public async Task CreateNewCustomerAsync()
    {
        CustomerViewModel newCustomer = new CustomerViewModel(new Models.Customer());
        NewCustomer = newCustomer;
        await App.Repository.Customers.UpsertAsync(NewCustomer.Model);
        AddingNewCustomer = true;
    }
    ```

3. Обновить **SaveInitialChangesAsync** метод для добавления клиента, созданного в репозитории, обновление пользовательского интерфейса и закрыть панель.

    ```csharp
    public async Task SaveInitialChangesAsync()
    {
        await App.Repository.Customers.UpsertAsync(NewCustomer.Model);
        await UpdateCustomersAsync();
        AddingNewCustomer = false;
    }
    ```
4. Добавьте следующую строку кода в качестве последней строки в метод задания значения **AddingNewCustomer**:

    ```csharp
    OnPropertyChanged(nameof(EnableCommandBar));
    ```

    Это гарантирует, что **EnableCommandBar** автоматически обновляется каждый раз, когда **AddingNewCustomer** изменяется.

### <a name="update-the-ui"></a>Обновление пользовательского интерфейса

1. Вернитесь к **Views\CustomerListPage.xaml**и добавьте **StackPanel** со следующими свойствами между вашей **CommandBar** и сетки данных:

    ```xaml
    <StackPanel
        x:Name="newCustomerPanel"
        Orientation="Horizontal"
        x:Load="{x:Bind ViewModel.AddingNewCustomer, Mode=OneWay}"
        RelativePanel.Below="mainCommandBar">
    </StackPanel>
    ```
    **X: Load** атрибут гарантирует, что в этой области отображается только при добавлении нового клиента.

2. Внесите следующие изменения в позицию сетки данных, чтобы убедиться, что перемещается вниз при появлении новой панели:

    ```xaml
    RelativePanel.Below="newCustomerPanel"
    ```

3. Обновить панель стека с четырьмя **TextBox** элементов управления. Они привязки к отдельным свойствам нового клиента и можно будет изменять его значения перед их добавлением в сетке данных.

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

4. Добавьте простая кнопка вашей новой панели стека, чтобы сохранить только что созданный клиент:

    ```xaml
    <StackPanel>
        <!--Text boxes from step 3-->
        <AppBarButton
            x:Name="SaveNewCustomer"
            Click="{x:Bind ViewModel.SaveInitialChangesAsync}"
            Icon="Save"/>
    </StackPanel>
    ```

5. Обновить **CommandBar**, поэтому регулярного создания, удаления и обновления кнопки будут отключены панели отображается, когда:

    ```xaml
    <CommandBar
        x:Name="mainCommandBar"
        HorizontalAlignment="Stretch"
        IsEnabled="{x:Bind ViewModel.EnableCommandBar, Mode=OneWay}"
        Background="AliceBlue">
        <!--App bar buttons-->
    </CommandBar>
    ```

6. Запустите приложение. Теперь можно создать клиента и ввода данных в панели стека.

![Создание нового клиента](images/customer-database-tutorial/add-new-customer.png)

## <a name="part-7-delete-a-customer"></a>Часть 7. Удаление клиента

Удаление клиента является окончательной основные операции, необходимо реализовать. При удалении клиента был выбран в сетке данных, необходимо немедленно вызвать **UpdateCustomersAsync** для обновления пользовательского интерфейса. Тем не менее не нужно вызывать этот метод, если необходимо удалить клиента, для которого вы только что создали.

1. Перейдите к **ViewModels\CustomerListPageViewModel.cs**и обновите **DeleteAndUpdateAsync** метод:

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

2. В **Views\CustomerListPage.xaml**, обновления панели для добавления нового клиента, поэтому он содержит вторую кнопку:

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

3. В **ViewModels\CustomerListPageViewModel.cs**, обновить **DeleteNewCustomerAsync** метод для удаления нового клиента:

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

4. Запустите приложение. Теперь можно удалить пользователей, либо в сетке данных, либо в панели.

![Удаление нового клиента](images/customer-database-tutorial/delete-new-customer.png)

## <a name="conclusion"></a>Заключение

Поздравляем! Все это Готово ваше приложение теперь имеет обширный набор операций с локальной базой данных. Можно создать, чтение, обновление и удаление клиентов внутри пользовательского интерфейса, и эти изменения сохраняются в базе данных и будут сохраняться между разных запусках приложения.

Теперь, когда вы закончите, учитывайте следующее:
* Если это еще не сделано, см. статью [Общие сведения о структуре приложения](../enterprise/customer-database-app-structure.md) Дополнительные сведения о том, почему сборке приложения, как это.
* Изучите [полный образец базы данных заказов клиента](https://github.com/Microsoft/Windows-appsample-customers-orders-database) чтобы увидеть этот учебник был основан на приложение.

Или если вы для сложной задачей, можно продолжить и более поздних версий...

## <a name="going-further-connect-to-a-remote-database"></a>Продвигаясь дальше: Подключение к удаленной базе данных

Мы предоставляем Пошаговое руководство по реализации этих вызовов к локальной базе данных SQLite. Но что делать, если вы хотите вместо этого используйте удаленной базы данных?

Если вы хотите попробовать это, нужно будет собственную учетную запись Azure Active Directory (AAD) и возможность размещения источника данных.

Необходимо добавить проверку подлинности, функции, чтобы обрабатывать вызовы REST, а затем создать для взаимодействия с удаленной базой данных. Нет кода в полном объеме [образец базы данных заказов клиента](https://github.com/Microsoft/Windows-appsample-customers-orders-database) , указываемый для каждой необходимые операции.

### <a name="settings-and-configuration"></a>Параметры и конфигурация

Необходимые действия для подключения к удаленной базе данных выраженная в [файле readme](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/README.md). Необходимо сделать следующее:

* Указание учетной записи Azure идентификатор для [Constants.cs](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoRepository/Constants.cs).
* Укажите URL-адрес удаленной базы данных для [Constants.cs](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoRepository/Constants.cs).
* Строка подключения для базы данных для [Constants.cs](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoRepository/Constants.cs).
* Свяжите приложение с Microsoft Store.
* Скопировать [проект службы](https://github.com/Microsoft/Windows-appsample-customers-orders-database/tree/master/ContosoService) в свое приложение и развернуть его в Azure.

### <a name="authentication"></a>Authentication

Вам необходимо будет создать кнопку, чтобы запустить последовательность проверки подлинности и на отдельной странице для сбора сведений о пользователе или контекстного меню. После создания, необходимо предоставить код, который запрашивает учетные данные и использует его для получения маркера доступа. В образце базы данных заказов клиента создает оболочку для вызовов Microsoft Graph с **WebAccountManager** библиотеки для получения токена и проверку подлинности в учетную запись AAD.

* Логика аутентификации реализована в [ **AuthenticationViewModel.cs**](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoApp/ViewModels/AuthenticationViewModel.cs).
* Процесс проверки подлинности отображается в настраиваемом [ **AuthenticationControl.xaml** ](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoApp/UserControls/AuthenticationControl.xaml) элемента управления.

### <a name="rest-calls"></a>Вызовы REST

Вам не нужно изменять какие-либо кода мы добавили в этом руководстве для реализации вызовов REST. Вместо этого необходимо сделать следующее:

* Создать новые реализации **ICustomerRepository** и **ITutorialRepository** интерфейсы, реализация тот же набор функций через REST вместо SQLite. Потребуется выполнять сериализацию и десериализацию JSON и может переноситься вызовов REST в отдельном **HttpHelper** класса, если вам нужно. Ссылаться на [полный пример](https://github.com/Microsoft/Windows-appsample-customers-orders-database/tree/master/ContosoRepository/Rest) подробные сведения.
* В **App.xaml.cs**, создайте новую функцию для инициализации репозитория REST и вызвать его вместо **SqliteDatabase** при инициализации приложения. Снова следует обратиться к [полный пример](https://github.com/Microsoft/Windows-appsample-customers-orders-database/blob/master/ContosoApp/App.xaml.cs).

После завершения все три эти действия можно будет проходить проверку подлинности в учетной записи AAD через приложение. Вызовы REST к удаленной базе данных приведет к замене локальной вызовы SQLite, но взаимодействие с пользователем должны быть одинаковыми. Если у вас даже более требовательными, можно добавить параметры страницы, чтобы разрешить пользователю динамически переключаться между ними.