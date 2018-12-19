---
title: Использование базы данных SQL Server в приложении UWP
description: Использование базы данных SQL Server в приложении UWP.
ms.date: 11/13/2017
ms.topic: article
keywords: windows 10, uwp, SQL Server, база данных
ms.localizationpriority: medium
ms.openlocfilehash: 4fe215a593293ff91afb7f71a830512ac365093f
ms.sourcegitcommit: 8ac3818db796a144b44f848b6211bc46a62ab544
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/19/2018
ms.locfileid: "8976931"
---
# <a name="use-a-sql-server-database-in-a-uwp-app"></a>Использование базы данных SQL Server в приложении UWP
Ваше приложение может подключаться напрямую к базе данных SQL Server и затем хранить и извлекать данные с помощью классов в пространстве имен [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient.aspx).

В этом руководстве мы расскажем вам об одном способе выполнения этой задачи. Если вы установите пример базы данных [Northwind](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/linq/downloading-sample-databases) поверх экземпляра SQL Server, а затем используете эти фрагменты кода, у вас получится базовый пользовательский интерфейс, демонстрирующий продукты из примера базы данных Northwind.

![Продукты Northwind](images/products-northwind.png)

В основе фрагментов кода, представленных в этом руководстве, лежит более [полный пример кода](https://github.com/StefanWickDev/IgniteDemos/tree/master/NorthwindDemo).

## <a name="first-set-up-your-solution"></a>Сначала настройте свое решение.

Чтобы подключить свое приложение напрямую к базе данных SQL Server, убедитесь, что минимальная версия вашего проекта поддерживает Fall Creators Update.  Эти сведения можно найти на странице свойств проекта UWP.

![Минимальная версия Windows SDK](images/min-version-fall-creators.png)

Откройте файл **Package.appxmanifest** проекта UWP в конструкторе манифестов.

На вкладке " **возможности** " установите флажок **Корпоративная проверка подлинности** при использовании проверки подлинности Windows для проверки подлинности SQL Server.

![Корпоративная проверка подлинности](images/enterprise-authentication.png)

<a id="use-data" />

## <a name="add-and-retrieve-data-in-a-sql-server-database"></a>Добавление данных в базу данных SQL Server и их извлечение

В этом разделе мы выполним следующие действия.

1. Добавление строки подключения.

2. Создание класса для хранения данных продукта.

3. Получение продуктов из базы данных SQL Server.

4. Добавление базового интерфейса пользователя.

5. Заполнение пользовательского интерфейса продуктами.

>[!NOTE]
> В этом разделе показан один из способов организации кода доступа к данным. Его задачей является показать пример того, как можно использовать [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient.aspx) для хранения данных в базе данных SQL Server и их извлечения. Код можно организовывать любым образом, который лучше всего подходит для проекта вашего приложения.

### <a name="add-a-connection-string"></a>Добавление строки подключения

В файле **App.xaml.cs** добавьте свойство в класс ``App``, который предоставляет другим классам в вашем решении доступ к строке подключения.

Строка подключения указывает на базу данных Northwind в экземпляре SQL Server Express.

```csharp
sealed partial class App : Application
{
    // Connection string for using Windows Authentication.
    private string connectionString =
        @"Data Source=YourServerName\SQLEXPRESS;Initial Catalog=NORTHWIND;Integrated Security=SSPI";

    // This is an example connection string for using SQL Server Authentication.
    // private string connectionString =
    //     @"Data Source=YourServerName\YourInstanceName;Initial Catalog=DatabaseName; User Id=XXXXX; Password=XXXXX";

    public string ConnectionString { get => connectionString; set => connectionString = value; }

    ...
}
```

### <a name="create-a-class-to-hold-product-data"></a>Создание класса для хранения данных продукта

Мы создадим класс, реализующий событие [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), чтобы можно было привязать атрибуты в пользовательском интерфейсе XAML к свойствам этого класса.

```csharp
public class Product : INotifyPropertyChanged
{
    public int ProductID { get; set; }
    public string ProductCode { get { return ProductID.ToString(); } }
    public string ProductName { get; set; }
    public string QuantityPerUnit { get; set; }
    public decimal UnitPrice { get; set; }
    public string UnitPriceString { get { return UnitPrice.ToString("######.00"); } }
    public int UnitsInStock { get; set; }
    public string UnitsInStockString { get { return UnitsInStock.ToString("#####0"); } }
    public int CategoryId { get; set; }

    public event PropertyChangedEventHandler PropertyChanged;
    private void NotifyPropertyChanged(string propertyName)
    {
        if (PropertyChanged != null)
        {
            PropertyChanged(this, new PropertyChangedEventArgs(propertyName));
        }
    }

}
```

### <a name="retrieve-products-from-the-sql-server-database"></a>Получение продуктов из базы данных SQL Server

Создайте метод, который будет получать продукты из примера базы данных Northwind и возвращать их в качестве коллекции [ObservableCollection](https://msdn.microsoft.com/library/windows/apps/ms668604.aspx) экземпляров ``Product``.

```csharp
public ObservableCollection<Product> GetProducts(string connectionString)
{
    const string GetProductsQuery = "select ProductID, ProductName, QuantityPerUnit," +
       " UnitPrice, UnitsInStock, Products.CategoryID " +
       " from Products inner join Categories on Products.CategoryID = Categories.CategoryID " +
       " where Discontinued = 0";

    var products = new ObservableCollection<Product>();
    try
    {
        using (SqlConnection conn = new SqlConnection(connectionString))
        {
            conn.Open();
            if (conn.State == System.Data.ConnectionState.Open)
            {
                using (SqlCommand cmd = conn.CreateCommand())
                {
                    cmd.CommandText = GetProductsQuery;
                    using (SqlDataReader reader = cmd.ExecuteReader())
                    {
                        while (reader.Read())
                        {
                            var product = new Product();
                            product.ProductID = reader.GetInt32(0);
                            product.ProductName = reader.GetString(1);
                            product.QuantityPerUnit = reader.GetString(2);
                            product.UnitPrice = reader.GetDecimal(3);
                            product.UnitsInStock = reader.GetInt16(4);
                            product.CategoryId = reader.GetInt32(5);
                            products.Add(product);
                        }
                    }
                }
            }
        }
        return products;
    }
    catch (Exception eSql)
    {
        Debug.WriteLine("Exception: " + eSql.Message);
    }
    return null;
}
```

### <a name="add-a-basic-user-interface"></a>Добавление базового пользовательского интерфейса

 Добавьте следующий XAML-код в файл **MainPage.xaml** проекта UWP.

 Этот XAML-код создает [ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview) для отображения каждого продукта, возвращенного в предыдущем фрагменте кода, и привязывает атрибуты каждой строки в [ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview) к свойствам, определенным в классе ``Product``.

```xml
<Grid Background="{ThemeResource SystemControlAcrylicWindowBrush}">
    <RelativePanel>
        <ListView Name="InventoryList"
                  SelectionMode="Single"
                  ScrollViewer.VerticalScrollBarVisibility="Auto"
                  ScrollViewer.IsVerticalRailEnabled="True"
                  ScrollViewer.VerticalScrollMode="Enabled"
                  ScrollViewer.HorizontalScrollMode="Enabled"
                  ScrollViewer.HorizontalScrollBarVisibility="Auto"
                  ScrollViewer.IsHorizontalRailEnabled="True"
                  Margin="20">
            <ListView.HeaderTemplate>
                <DataTemplate>
                    <StackPanel Orientation="Horizontal"  >
                        <TextBlock Text="ID" Margin="8,0" Width="50" Foreground="DarkRed" />
                        <TextBlock Text="Product description" Width="300" Foreground="DarkRed" />
                        <TextBlock Text="Packaging" Width="200" Foreground="DarkRed" />
                        <TextBlock Text="Price" Width="80" Foreground="DarkRed" />
                        <TextBlock Text="In stock" Width="80" Foreground="DarkRed" />
                    </StackPanel>
                </DataTemplate>
            </ListView.HeaderTemplate>
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="local:Product">
                    <StackPanel Orientation="Horizontal" >
                        <TextBlock Name="ItemId"
                                    Text="{x:Bind ProductCode}"
                                    Width="50" />
                        <TextBlock Name="ItemName"
                                    Text="{x:Bind ProductName}"
                                    Width="300" />
                        <TextBlock Text="{x:Bind QuantityPerUnit}"
                                   Width="200" />
                        <TextBlock Text="{x:Bind UnitPriceString}"
                                   Width="80" />
                        <TextBlock Text="{x:Bind UnitsInStockString}"
                                   Width="80" />
                    </StackPanel>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </RelativePanel>
</Grid>
```

### <a name="show-products-in-the-listview"></a>Отображение продуктов в ListView

Откройте файл **MainPage.xaml.cs** и добавьте код в конструктор класса ``MainPage``, который задает свойство **ItemSource** в [ListView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview) для [ObservableCollection](https://msdn.microsoft.com/library/windows/apps/ms668604.aspx) экземпляров ``Product``.

```csharp
public MainPage()
{
    this.InitializeComponent();
    InventoryList.ItemsSource = GetProducts((App.Current as App).ConnectionString);
}
```

Запустите проект и посмотрите, как продукты из примера базы данных Northwind отображаются в пользовательском интерфейсе.

![Продукты Northwind](images/products-northwind.png)

Изучите пространство имен [System.Data.SqlClient](https://msdn.microsoft.com/library/system.data.sqlclient.aspx), чтобы узнать, что еще можно сделать с данными в базе данных SQL Server.

## <a name="trouble-connecting-to-your-database"></a>Проблемы с подключением к базе данных?

В большинстве случаев необходимо изменить некоторые аспекты конфигурации SQL Server. Если вы можете подключиться к базе данных из другого типа классического приложения, например приложения Windows Forms или WPF, убедитесь, что вы включили TCP/IP для SQL Server. Это можно сделать в консоли **Управление компьютером**.

![Управление компьютерами](images/computer-management.png)

Затем убедитесь в том, что запущена служба обозревателя SQL Server.

![Служба обозревателя SQL Server](images/sql-browser-service.png)

## <a name="next-steps"></a>Дальнейшие действия

**Использование облегченной базы данных для хранения данных на устройстве пользователя**

См. раздел [Использование базы данных SQLite в приложении UWP](sqlite-databases.md).

**Совместное использование кода между разными приложениями на различных платформах**

См. раздел [Совместное использование кода в классическом приложении и приложении UWP](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-migrate).

**Добавление страниц с основными и подробными данными с помощью серверов Azure SQL**

См. раздел [Пример базы данных заказов клиентов](https://github.com/Microsoft/Windows-appsample-customers-orders-database).
