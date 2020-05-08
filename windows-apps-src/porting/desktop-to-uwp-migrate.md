---
Description: Совместное использование кода в классическом приложении и приложении UWP
title: Совместное использование кода в классическом приложении и приложении UWP
ms.date: 10/03/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 4c07a3bbff4b29d2b59ef7d6d8a5912ce3675a4e
ms.sourcegitcommit: ef723e3d6b1b67213c78da696838a920c66d5d30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2020
ms.locfileid: "82730353"
---
# <a name="move-from-a-desktop-application-to-uwp"></a>Переход от классического приложения к UWP

При наличии существующего классического приложения, созданного с помощью .NET Framework (включая WPF и Windows Forms) или API Win32 с + +, у вас есть несколько вариантов перехода на универсальная платформа Windows (UWP) и Windows 10.

## <a name="package-your-desktop-application-in-an-msix-package"></a>Упаковка классического приложения в пакет MSIX

Вы можете упаковать настольное приложение в пакет MSIX, чтобы получить доступ ко многим другим функциям Windows 10. MSIX — это современный универсальный формат пакетов для упаковки любых приложений для Windows, включая приложения UWP, WPF, Windows Forms и Win32. Упаковав классические приложения для Windows в MSIX-пакеты, вы получаете доступ к надежным возможностям установки и обновления, управляемой модели безопасности с гибкой системой возможностей, поддержке Microsoft Store, возможностям управления предприятием и многим пользовательским моделям распространения. Вы можете упаковать приложение независимо от наличия исходного кода или наличия только существующего файла установщика (например, MSI или App-V Installer). После упаковки приложения можно интегрировать такие компоненты UWP, как расширения пакета и другие компоненты UWP.

Дополнительные сведения см. в разделе [Упаковка классических приложений (Настольный мост)](/windows/msix/desktop/desktop-to-uwp-root) и [компоненты, для которых требуется удостоверение пакета](/windows/apps/desktop/modernize/modernize-packaged-apps).

## <a name="use-windows-runtime-apis"></a>Использование среда выполнения Windows API

Для интеграции современных возможностей, которые могут заинтересовать пользователей Windows 10, большинство API-интерфейсов среды выполнения Windows можно вызывать напрямую из классических приложений WPF, Windows Forms или C++ Win32. Например, вы можете вызывать API-интерфейсы среды выполнения Windows, чтобы добавлять всплывающие уведомления в приложение.

Дополнительные сведения см. в статье об [использовании API-интерфейсов среды выполнения Windows для классических приложений](/windows/apps/desktop/modernize/desktop-to-uwp-enhance).

## <a name="migrate-a-net-framework-app-to-a-uwp-app"></a>Перенос приложения .NET Framework в приложение UWP

Если приложение выполняется на .NET Framework, его можно перенести в приложение UWP, используя .NET Standard 2,0. Переместите как можно больше кода, как и в .NET Standard библиотеки классов 2,0, а затем создайте приложение UWP, которое ссылается на библиотеки .NET Standard 2,0. 

### <a name="share-code-in-a-net-standard-20-library"></a>Совместное использование кода в библиотеке .NET Standard 2.0

Если приложение выполняется на .NET Framework, разместите как можно больше кода, как и в .NET Standard 2,0 библиотеки классов. Если в коде используются API-интерфейсы, определенные в этом стандарте, код можно многократно использовать в приложениях UWP. В стандарт .NET Standard 2.0 входит гораздо больше API-интерфейсов, поэтому совместное использование кода в библиотеках .NET Standard стало еще удобнее.

Ниже приведен видеоролик с дополнительными сведениями о нем.

> [!VIDEO https://www.youtube-nocookie.com/embed/YI4MurjfMn8?list=PLRAdsfhKI4OWx321A_pr-7HhRNk7wOLLY&amp;ecver=1]

#### <a name="add-net-standard-libraries"></a>Добавление библиотек .NET Standard

Прежде всего, добавьте в вашей решение одну или несколько библиотек классов .NET Standard.  

![Добавление стандартного проекта dotnet](images/desktop-to-uwp/dot-net-standard-project-template.png)

Количество добавляемых в решение библиотек зависит от того, как вы будете организовывать свой код.

Убедитесь, что целевым объектом каждой библиотеки классов является **.NET Standard 2.0**.

![Целевой объект — .NET Standard 2.0](images/desktop-to-uwp/target-standard-20.png)

Этот параметр находится на страницах свойств проекта библиотеки классов.

В проекте классического приложения добавьте ссылку на проект библиотеки классов.

![Ссылка на библиотеку классов](images/desktop-to-uwp/class-library-reference.png)

Далее используйте специальные инструменты, чтобы определить, насколько ваш код соответствует стандарту. Таким образом, перед перемещением кода в библиотеку вы решите, какие его части можно использовать повторно, какие части требуют минимальных изменений, а какие будут предназначаться для конкретных приложений.

#### <a name="check-library-and-code-compatibility"></a>Проверка совместимости библиотеки и кода

Начнем с пакетов NuGet и других DLL-файлов, полученных от стороннего поставщика.

Если в вашем приложении используются такие файлы, определите, совместимы ли они со стандартом .NET Standard 2.0. Для этого можно использовать расширение Visual Studio или служебную программу командной строки.

Используйте эти же инструменты для анализа кода. Скачайте инструменты по этой ссылке ([dotnet-apiport](https://github.com/Microsoft/dotnet-apiport/releases)) и просмотрите это видео, чтобы научиться ими пользоваться.
&nbsp;
> [!VIDEO https://www.youtube-nocookie.com/embed/rzs_FGPyAlY?list=PLRAdsfhKI4OWx321A_pr-7HhRNk7wOLLY&amp;ecver=2]

Если ваш код не соответствует стандарту, рассмотрите другие варианты использования этого кода. В первую очередь, откройте [обозреватель API-интерфейсов .NET](https://docs.microsoft.com/dotnet/api/?view=netstandard-2.0). С его помощью можно просматривать API-интерфейсы, доступные в стандарте .NET Standard 2.0. В качестве области списка выберите .NET Standard 2.0.

![вариант dot net](images/desktop-to-uwp/dot-net-option.png)

Часть кода будет предназначена для определенной платформы и останется в проекте классического приложения.

#### <a name="example-migrating-data-access-code-to-a-net-standard-20-library"></a>Пример. Перенос кода доступа к данным в библиотеку .NET Standard 2.0

Предположим, что у нас есть очень простое приложение Windows Forms, которое показывает клиентов из образца базы данных Northwind.

![Приложение Windows Forms](images/desktop-to-uwp/win-forms-app.png)

Проект содержит библиотеку классов .NET Standard 2.0 со статическим классом с именем **Northwind**. Если переместить этот код в класс **Northwind** , он не будет компилироваться, поскольку в нем ``SQLConnection``используются ``SqlCommand``классы, ``SqlDataReader`` и, а также эти классы, недоступные в .NET Standard 2,0.

```csharp
public static ArrayList GetCustomerNames()
{
    ArrayList customers = new ArrayList();

    using (SqlConnection conn = new SqlConnection())
    {
        conn.ConnectionString =
            @"Data Source=" +
            @"<Your Server Name>\SQLEXPRESS;Initial Catalog=NORTHWIND;Integrated Security=SSPI";

        conn.Open();

        SqlCommand command = new SqlCommand("select ContactName from customers order by ContactName asc", conn);

        using (SqlDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                customers.Add(reader[0]);
            }
        }
    }

    return customers;
}

```
Мы можем использовать [обозреватель API-интерфейсов .NET](https://docs.microsoft.com/dotnet/api/?view=netstandard-2.0) для получения альтернативного варианта. Классы ``DbConnection``, ``DbCommand`` и ``DbDataReader`` они доступны в .NET Standard 2.0, поэтому можно использовать их.  

В данной исправленной версии эти классы используются для получения списка пользователей, но для создания класса ``DbConnection`` потребуется передать объект фабрики, созданный в клиентском приложении.

```csharp
public static ArrayList GetCustomerNames(DbProviderFactory factory)
{
    ArrayList customers = new ArrayList();

    using (DbConnection conn = factory.CreateConnection())
    {
        conn.ConnectionString = @"Data Source=" +
                        @"<Your Server Name>\SQLEXPRESS;Initial Catalog=NORTHWIND;Integrated Security=SSPI";

        conn.Open();

        DbCommand command = factory.CreateCommand();
        command.Connection = conn;
        command.CommandText = "select ContactName from customers order by ContactName asc";

        using (DbDataReader reader = command.ExecuteReader())
        {
            while (reader.Read())
            {
                customers.Add(reader[0]);
            }
        }
    }

    return customers;
}
```  

На странице с кодом программной части Windows Form можно создать экземпляр фабрики и передать его нашему методу.

```csharp
public partial class Customers : Form
{
    public Customers()
    {
        InitializeComponent();

        dataGridView1.Rows.Clear();

        SqlClientFactory factory = SqlClientFactory.Instance;

        foreach (string customer in Northwind.GetCustomerNames(factory))
        {
            dataGridView1.Rows.Add(customer);
        }
    }
}
```

### <a name="create-a-uwp-app"></a>Создание приложения UWP

Теперь все готово для добавления приложения UWP в решение.

![изображение моста переноса классических приложений на UWP](images/desktop-to-uwp/adaptive-ui.png)

Все равно потребуется спроектировать страницы пользовательского интерфейса в XAML и написать код для конкретного устройства или платформы, но после этого вы получите доступ к полному спектру устройств Windows 10, а страницы вашего приложения буду выполнены в современном стиле с эффективной адаптацией под различные размеры и разрешение экрана.

Приложение будет реагировать на механизмы ввода, отличные от клавиатуры с мышью, а пользоваться его возможностями и параметрами будет удобно на любых устройствах. Это означает, что пользователям нужно научиться пользоваться приложением только один раз, так как оно работает практически одинаково независимо от устройства.

Это лишь несколько преимуществ UWP. Дополнительные сведения см. в разделе [Создавайте великолепные решения с помощью Windows](https://developer.microsoft.com/windows/why-build-for-uwp).

#### <a name="add-a-uwp-project"></a>Добавление проекта UWP

Прежде всего, добавьте проект UWP в свое решение.

![Проект UWP](images/desktop-to-uwp/new-uwp-app.png)

Затем в проекте UWP добавьте ссылку на проект библиотеки .NET Standard 2.0.

![Ссылка на библиотеку классов](images/desktop-to-uwp/class-library-reference2.png)

#### <a name="build-your-pages"></a>Создание собственных страниц

Добавьте страницы XAML и вызовите код в библиотеке .NET Standard 2.0.

![Приложение UWP](images/desktop-to-uwp/uwp-app.png)

```xml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel x:Name="customerStackPanel">
        <ListView x:Name="customerList"/>
    </StackPanel>
</Grid>
```

```csharp
public sealed partial class MainPage : Page
{
    public MainPage()
    {
        this.InitializeComponent();

        SqlClientFactory factory = SqlClientFactory.Instance;

        customerList.ItemsSource = Northwind.GetCustomerNames(factory);
    }
}
```

Чтобы начать работать с UWP, обратитесь к разделу [Что такое приложение UWP](https://docs.microsoft.com/windows/uwp/get-started/universal-application-platform-guide).

### <a name="reach-ios-and-android-devices"></a>Доступ к устройствам iOS и Android

Получить доступ к устройствам Android и iOS можно путем добавления проектов Xamarin.  

![Приложения Xamarin](images/desktop-to-uwp/xamarin-apps.png)

С помощью этих проектов можно использовать C# для создания приложений Android и iOS с полным доступом к API-интерфейсам для конкретных платформ и устройств. Эти приложения используют средства аппаратного ускорения с учетом конкретной платформы и компилируются для достижения естественной производительности.

У них есть доступ ко всем функциям, предоставленным базовыми платформой и устройством, включая возможности конкретных платформ, такие как маяки iBeacon и фрагменты Android. Кроме того, вы сможете использовать стандартные собственные элементы управления для создания пользовательского интерфейса, оформление и возможности которого будут соответствовать ожиданиям пользователей.

Как и в случае с приложениями UWP, стоимость добавления приложения Android или iOS ниже благодаря возможности многократно использовать бизнес-логику в библиотеке классов .NET Standard 2.0. Необходимо спроектировать страницы пользовательского интерфейса в XAML и написать код для конкретного устройства или платформы.

#### <a name="add-a-xamarin-project"></a>Добавление проекта Xamarin

Прежде всего, добавьте в решение проект **Android**, **iOS** или **Межплатформенный проект**.

Эти шаблоны находятся в диалоговом окне **Добавление нового проекта** в группе **Visual C#**.

![Приложения Xamarin](images/desktop-to-uwp/xamarin-projects.png)

>[!NOTE]
>Межплатформенные проекты подходят для приложений с минимальным набором возможностей для конкретных платформ. Эти проекты можно использовать для создания единого собственного пользовательского интерфейса на основе XAML, который будет работать в iOS, Android и Windows. Дополнительные сведения см. [здесь](https://docs.microsoft.com/xamarin/xamarin-forms/).

Затем добавьте ссылку на проект библиотеки классов через проект Android, iOS или межплатформенный проект.

![Ссылка на библиотеку классов](images/desktop-to-uwp/class-library-reference3.png)

#### <a name="build-your-pages"></a>Создание собственных страниц

В нашем примере показан список пользователей в приложении Android.

![Приложение Android](images/desktop-to-uwp/android-app.png)

```xml
<?xml version="1.0" encoding="utf-8"?>
<TextView xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent"
    android:padding="10dp" android:textSize="16sp"
    android:id="@android:id/list">
</TextView>
```

```csharp
[Activity(Label = "MyAndroidApp", MainLauncher = true)]
public class MainActivity : ListActivity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        SqlClientFactory factory = SqlClientFactory.Instance;

        var customers = (string[])Northwind.GetCustomerNames(factory).ToArray(typeof(string));

        ListAdapter = new ArrayAdapter<string>(this, Resource.Layout.list_item, customers);
    }
}
```

Инструкции по началу работы с проектами Android, iOS и межплатформенными проектами см. на [портале Xamarin для разработчиков](https://docs.microsoft.com/xamarin).

## <a name="next-steps"></a>Дальнейшие действия

**Поиск ответов на вопросы**

Есть вопросы? Задайте их на Stack Overflow. Наша команда следит за этими [тегами](https://stackoverflow.com/questions/tagged/project-centennial+or+desktop-bridge). Вы также можете задать нам вопросы [здесь](https://social.msdn.microsoft.com/Forums/en-US/home?filter=alltypes&sort=relevancedesc&searchTerm=%5BDesktop%20Converter%5D).

**Оставьте отзыв или предложите новые возможности для реализации**

См. раздел [UserVoice](https://wpdev.uservoice.com/forums/110705-universal-windows-platform/category/161895-desktop-bridge-centennial).
