---
title: Использование базы данных SQLite в приложении UWP
description: Использование базы данных SQLite в приложении UWP.
ms.date: 11/30/2018
ms.topic: article
keywords: windows 10, uwp, SQLite, базы данных
ms.localizationpriority: medium
ms.openlocfilehash: 1cc8cfe696d35872469d97dba24f5388ff6833b5
ms.sourcegitcommit: a20457776064c95a74804f519993f36b87df911e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71339785"
---
# <a name="use-a-sqlite-database-in-a-uwp-app"></a>Использование базы данных SQLite в приложении UWP
SQLite можно использовать для хранения и извлечения данных из облегченной базы данных на устройстве пользователя. Это руководство содержит соответствующие инструкции.

## <a name="some-benefits-of-using-sqlite-for-local-storage"></a>Некоторые преимущества использования SQLite для локального хранения данных

:heavy_check_mark: SQLite легкая и автономная. Это библиотека кода без других зависимых компонентов. Ее не требуется настраивать.

:heavy_check_mark: Тут нет сервера базы данных. Клиент и сервер работают в одном процессе.

:heavy_check_mark: База данных SQLite находится в общедоступном домене, поэтому вы можете свободно использовать и распространять ее в вашем приложении.

:heavy_check_mark: SQLite работает на разных платформах и архитектурах.

Подробнее о SQLite см. [здесь](https://sqlite.org/about.html).

## <a name="choose-an-abstraction-layer"></a>Выбор слоя абстрагирования

Мы рекомендуем использовать Entity Framework Core или [библиотеку SQLite](https://github.com/aspnet/Microsoft.Data.Sqlite/) с открытым исходным кодом, встроенные корпорацией Майкрософт.

### <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) — это объектно-реляционный модуль сопоставления, позволяющий разработчикам работать с реляционными данными с помощью объектов, специализированных для доменов. Если вы уже использовали эту платформу для работы с данными в других приложениях .NET, можно перенести этот код в приложение UWP и он будет работать с соответствующими изменениями в строке подключения.

Чтобы опробовать эту платформу, см. раздел [Начало работы с EF Core на универсальной платформе Windows (UWP) с новой базой данных](https://docs.microsoft.com/ef/core/get-started/uwp/getting-started).

### <a name="sqlite-library"></a>Библиотеке SQLite

Библиотека [Microsoft.Data.Sqlite](https://docs.microsoft.com/dotnet/api/microsoft.data.sqlite?view=msdata-sqlite-2.0.0) реализует интерфейсы в пространстве имен [System.Data.Common](https://docs.microsoft.com/dotnet/api/system.data.common). Майкрософт активно поддерживает эти реализации и предоставляет интуитивно понятную оболочку для низкоуровневых собственных API-интерфейсов SQLite.

В остальных разделах этого руководства приведены инструкции по работе с этой библиотекой.

## <a name="set-up-your-solution-to-use-the-microsoftdatasqlite-library"></a>Настройка решения для использования библиотеки Microsoft.Data.SQlite

Мы начнем с базового проекта UWP, добавим библиотеку классов, а затем установим соответствующие пакеты NuGet.

Тип библиотеки классов, добавляемой в решение, и специальные пакеты, которые вы устанавливаете, зависят от минимальной версии Windows SDK, для которого предназначено ваше приложение. Эти сведения можно найти на странице свойств проекта UWP.

![Минимальная версия Windows SDK](images/min-version.png)

Используйте один из следующих разделов в зависимости от минимальной версии Windows SDK, для которого предназначен вам проект UWP.

### <a name="the-minimum-version-of-your-project-does-not-target-the-fall-creators-update"></a>Минимальная версия проекта не предназначена для обновления Fall Creators Update

Если вы используете Visual Studio 2015, щелкните **Справка**->**Сведения о Microsoft Visual Studio**. Затем в списке установленных программ убедитесь, что версия диспетчера пакетов NuGet — **3.5** или более поздняя версия. Если номер версии ниже, установите более позднюю версию NuGet. Скачать ее можно [здесь](https://www.nuget.org/downloads). На этой странице вы найдете все версии Nuget, перечисленные под заголовком **Visual Studio 2015**.

Затем добавьте библиотеку классов в свое решение. Использовать библиотеку классов для размещения вашего кода доступа к данным не требуется, но мы используем ее в нашем примере. Мы назовем библиотеку **DataAccessLibrary**, а класс в библиотеке — **DataAccess**.

![Библиотека классов](images/class-library.png)

Щелкните правой кнопкой мыши решение, а затем нажмите кнопку **Управление пакетами NuGet для решения**.

![Управление пакетами NuGet](images/manage-nuget.png)

Если вы используете Visual Studio 2015, выберите вкладку **Установленные** и убедитесь, что версия пакета **Microsoft.NETCore.UniversalWindowsPlatform** — **5.2.2** или более поздняя версия.

![Версия .NETCore](images/package-version.png)

Если это не так, обновите пакет до более новой версии.

Выберите вкладку **Обзор** и выполните поиск пакета **Microsoft.Data.SQLite**. Установите версию пакета **1.1.1** (или ниже).

![Пакет SQLite](images/sqlite-package.png)

Перейдите к разделу [Добавление данных в базу данных SQLite и их извлечение](#use-data) данного руководства.

### <a name="the-minimum-version-of-your-project-targets-the-fall-creators-update"></a>Минимальная версия проекта предназначена для обновления Fall Creators Update

Существует ряд преимуществ повышения минимальной версии проекта UWP для обновления Fall Creators Update.

В первую очередь можно использовать библиотеки .NET Standard 2.0 вместо стандартных библиотек классов. Это означает, что вы можете передать свой код доступа к данным любому другому приложению на основе .NET. Например, приложениям для WPF, Windows Forms, Android, iOS или ASP.NET.

Во-вторых, в вашем приложении нет библиотек пакетов SQLite. Вместо этого ваше приложение может использовать версию SQLite, которая поставляется вместе с Windows. Это обеспечивает следующие преимущества.

:heavy_check_mark: Уменьшает размер приложения, поскольку не требуется скачивать двоичную библиотеку SQLite, а затем упаковывать ее в качестве части приложения.

:heavy_check_mark: Отсутствие необходимости в принудительной передаче новой версии вашего приложения пользователям, если SQLite опубликует важные исправления ошибок и уязвимостей безопасности в SQLite. Версия Windows SQLite обслуживается корпорацией Майкрософт совместно с SQLite.org.

:heavy_check_mark: Загрузка приложения может быть быстрее, поскольку, скорее всего, версия SDK-пакета SQLite будет уже загружена в память.

Давайте начнем с добавления библиотеки классов .NET Standard 2.0 в ваше решение. Использовать библиотеку классов для размещения вашего кода доступа к данным необязательно, но мы используем ее в нашем примере. Мы назовем библиотеку **DataAccessLibrary**, а класс в библиотеке — **DataAccess**.

![Библиотека классов](images/dot-net-standard.png)

Щелкните правой кнопкой мыши решение, а затем нажмите кнопку **Управление пакетами NuGet для решения**.

![Управление пакетами NuGet](images/manage-nuget-2.png)

На этом этапе у вас есть выбор. Можно использовать версию SQLite, которая входит в состав Windows, или если по какой-либо причине вы хотите использовать конкретную версию SQLite, можно добавить библиотеку SQLite в пакет.

Давайте начнем с использования версии SQLite, которая входит в состав Windows.

#### <a name="to-use-the-version-of-sqlite-that-is-installed-with-windows"></a>Чтобы использовать версию SQLite, которая устанавливается вместе с Windows, выполните следующие действия.

Выберите вкладку **Обзор** и выполните поиск пакета **Microsoft.Data.SQLite.core**, а затем установите его.

![Пакет SQLite Core](images/sqlite-core-package.png)

Выполните поиск пакета **SQLitePCLRaw.bundle_winsqlite3**, а затем установите его только в проект UWP в вашем решении.

![Пакет SQLite PCL Raw](images/sqlite-raw-package.png)

#### <a name="to-include-sqlite-with-your-app"></a>Добавление SQLite в приложение

Этого делать не требуется. Но если вы хотите добавить конкретную версию SQLite в свое приложение, выберите вкладку **Обзор** и выполните поиск пакета **Microsoft.Data.SQLite**. Установите версию пакета **2.0** (или ниже).

![Пакет SQLite](images/sqlite-package-v2.png)

<a id="use-data" />

## <a name="add-and-retrieve-data-in-a-sqlite-database"></a>Добавление данных в базу данных SQLite и их извлечение

Мы выполним следующие действия.

:one: Подготовка класса доступа к данным.

:two: Инициализация базы данных SQLite.

:three: Вставка данных в базу данных SQLite.

:four: Извлечение данных из базы данных SQLite.

:five: Добавление базового пользовательского интерфейса.

### <a name="prepare-the-data-access-class"></a>Подготовка класса доступа к данным

В проекте UWP добавьте ссылку на проект **DataAccessLibrary** в своем решении.

![Библиотека классов для доступа к данным](images/ref-class-library.png)

Добавьте следующий оператор ``using`` в файлы **App.xaml.cs** и **MainPage.xaml.cs** в проекте UWP.

```csharp
using DataAccessLibrary;
```

Откройте класс **DataAccess** в вашем решении **DataAccessLibrary** и сделайте этот класс статическим.

>[!NOTE]
>В нашем примере мы размещаем код доступа к данным в статическом классе, однако это лишь проектное решение, которое не является обязательным.

```csharp
namespace DataAccessLibrary
{
    public static class DataAccess
    {

    }
}

```

Добавьте следующие операторы использования в начало этого файла.

```csharp
using Microsoft.Data.Sqlite;
using System.Collections.Generic;
```

<a id="initialize" />

### <a name="initialize-the-sqlite-database"></a>Инициализация базы данных SQLite

Добавьте метод в класс **DataAccess**, который инициализирует базу данных SQLite.

```csharp
public static void InitializeDatabase()
{
    using (SqliteConnection db =
        new SqliteConnection("Filename=sqliteSample.db"))
    {
        db.Open();

        String tableCommand = "CREATE TABLE IF NOT " +
            "EXISTS MyTable (Primary_Key INTEGER PRIMARY KEY, " +
            "Text_Entry NVARCHAR(2048) NULL)";

        SqliteCommand createTable = new SqliteCommand(tableCommand, db);

        createTable.ExecuteReader();
    }
}
```

Этот код создает базу данных SQLite и сохраняет ее в локальном хранилище данных приложения.

В этом примере мы используем имя базы данных ``sqlliteSample.db``, но вы можете использовать любое имя при условии, что оно будет использоваться во всех объектах [SqliteConnection](https://docs.microsoft.com/dotnet/api/microsoft.data.sqlite.sqliteconnection?view=msdata-sqlite-2.0.0), экземпляры которых необходимо создать.

В конструкторе файла **App.xaml.cs** проекта UWP вызовите метод ``InitializeDatabase`` класса **DataAccess**.

```csharp
public App()
{
    this.InitializeComponent();
    this.Suspending += OnSuspending;

    DataAccess.InitializeDatabase();

}
```

<a id="insert" />

### <a name="insert-data-into-the-sqlite-database"></a>Вставка данных в базу данных SQLite

Добавьте метод в класс **DataAccess**, который вставляет данные в базу данных SQLite. Этот код использует параметры в запросе для предотвращения атак путем внедрения кода SQL.

```csharp
public static void AddData(string inputText)
{
    using (SqliteConnection db =
        new SqliteConnection("Filename=sqliteSample.db"))
    {
        db.Open();

        SqliteCommand insertCommand = new SqliteCommand();
        insertCommand.Connection = db;

        // Use parameterized query to prevent SQL injection attacks
        insertCommand.CommandText = "INSERT INTO MyTable VALUES (NULL, @Entry);";
        insertCommand.Parameters.AddWithValue("@Entry", inputText);

        insertCommand.ExecuteReader();

        db.Close();
    }

}
```

<a id="retrieve" />

### <a name="retrieve-data-from-the-sqlite-database"></a>Извлечение данных из базы данных SQLite

Добавьте метод, который возвращает строки данных из базы данных SQLite.

```csharp
public static List<String> GetData()
{
    List<String> entries = new List<string>();

    using (SqliteConnection db =
        new SqliteConnection("Filename=sqliteSample.db"))
    {
        db.Open();

        SqliteCommand selectCommand = new SqliteCommand
            ("SELECT Text_Entry from MyTable", db);

        SqliteDataReader query = selectCommand.ExecuteReader();

        while (query.Read())
        {
            entries.Add(query.GetString(0));
        }

        db.Close();
    }

    return entries;
}
```

Метод [Read](https://docs.microsoft.com/dotnet/api/microsoft.data.sqlite.sqlitedatareader.read?view=msdata-sqlite-2.0.0#Microsoft_Data_Sqlite_SqliteDataReader_Read) считывает строки возвращаемых данных. Он возвращает значение **true**, если остались строки. В противном случае он возвращает значение **false** .

Метод [GetString](https://docs.microsoft.com/dotnet/api/microsoft.data.sqlite.sqlitedatareader.getstring?view=msdata-sqlite-2.0.0#Microsoft_Data_Sqlite_SqliteDataReader_GetString_System_Int32_) возвращает значение конкретного столбца в виде строки. Он принимает целое число, представляющее собой отсчитываемый от нуля порядковый номер столбца необходимых данных. Можно использовать похожие методы, такие как [GetDataTime](https://docs.microsoft.com/dotnet/api/microsoft.data.sqlite.sqlitedatareader.getdatetime?view=msdata-sqlite-2.0.0#Microsoft_Data_Sqlite_SqliteDataReader_GetDateTime_System_Int32_) и [GetBoolean ](https://docs.microsoft.com/dotnet/api/microsoft.data.sqlite.sqlitedatareader.getboolean?view=msdata-sqlite-2.0.0#Microsoft_Data_Sqlite_SqliteDataReader_GetBoolean_System_Int32_). Выберите метод в зависимости от типа данных в столбце.

Порядковый номер параметра не так важен в этом примере, так как мы выбираем все записи в одном столбце. Тем не менее, если несколько столбцов являются частью запроса, используйте порядковое значение для получения столбца, из которого требуется извлечь данные.

## <a name="add-a-basic-user-interface"></a>Добавление базового пользовательского интерфейса

В файл **MainPage.xaml** проекта UWP добавьте следующий XAML-код.

```xml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox Name="Input_Box"></TextBox>
        <Button Click="AddData">Add</Button>
        <ListView Name="Output">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextBlock Text="{Binding}"/>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackPanel>
</Grid>
```

Этот базовый пользовательский интерфейс предоставляет пользователю поле ``TextBox``, которое можно использовать для ввода строки, которую мы добавим в базу данных SQLite. Мы подключимся ``Button``в этом пользовательском интерфейсе к обработчику событий, который будет получать данные из базы данных SQLite и затем отображать их в ``ListView``.

В файл **MainPage.xaml.cs** добавьте следующий обработчик. Это метод, который мы связали с кнопкой ``Click`` событие ``Button`` в пользовательском интерфейсе.

```csharp
private void AddData(object sender, RoutedEventArgs e)
{
    DataAccess.AddData(Input_Box.Text);

    Output.ItemsSource = DataAccess.GetData();
}
```

Вот и все. Изучите [Microsoft.Data.Sqlite](https://docs.microsoft.com/dotnet/api/microsoft.data.sqlite?view=msdata-sqlite-2.0.0) чтобы узнать, что еще можно сделать с вашей базой данных SQLite. Перейдите по ссылкам ниже, чтобы узнать о других способах использования данных в приложении UWP.

## <a name="next-steps"></a>Дальнейшие действия

**Подключение приложения непосредственно к базе данных SQL Server**

Ознакомьтесь с разделом [Использование базы данных SQL Server в приложении UWP](sql-server-databases.md).

**Совместное использование кода между разными приложениями на различных платформах**

См. статью о [совместном использовании кода в классическом приложении и приложении UWP](https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-migrate).

**Добавление страниц с основными и подробными данными с помощью серверов Azure SQL**

См. статью [Customer Orders Database sample](https://github.com/Microsoft/Windows-appsample-customers-orders-database) (Пример базы данных заказов клиентов).
