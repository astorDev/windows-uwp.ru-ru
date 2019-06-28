---
description: Этот учебник демонстрирует добавление пользовательских интерфейсах XAML UWP, создание MSIX пакеты и другие современные компоненты включить в приложении WPF.
title: Добавление действия пользователей Windows 10 и уведомления
ms.topic: article
ms.date: 06/27/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, универсальной платформы Windows, windows forms, wpf, о-ва xaml
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 8443ac25ba678986046b967a90a8899eaffb76aa
ms.sourcegitcommit: 1eec0e4fd8a5ba82803fdce6e23fcd01b9488523
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2019
ms.locfileid: "67420118"
---
# <a name="part-4-add-windows-10-user-activities-and-notifications"></a>Часть 4. Добавление действия пользователей Windows 10 и уведомления

Это четвертая часть учебник, в котором показано, как модернизировать пример классического приложения WPF с именем Contoso расходы. Обзор учебника, предварительные требования и инструкции для загрузки примера приложения, см. в разделе [руководства: Модернизация приложения WPF](modernize-wpf-tutorial.md). В этой статье предполагается, что вы уже выполнили [часть 3](modernize-wpf-tutorial-3.md).

В предыдущей части этого руководства вы добавили элементы управления XAML UWP в приложение с помощью острова XAML. Как по продукту этого вы включили приложение, чтобы вызвать любой API WinRT. Это открывает возможность для приложения, чтобы использовать множество других функций, предлагаемых Windows 10, не только элементы управления XAML UWP.

В вымышленной сценарии работы с этим руководством, Contoso команда разработчиков решила добавить две новые функции в приложение: действия и уведомления. Этой части руководства показано, как реализовать эти функции.

## <a name="add-a-user-activity"></a>Добавить действие пользователя

В Windows 10 приложения можно отслеживать действия, выполняемые пользователем, такие как открытие файла или отображение определенной страницы. Эти действия затем становятся доступными через временной шкалы, функция, представленная в Windows 10 версии 1803, которое позволяет пользователю быстро вернуться в прошлом и возобновить действие, в котором они запущены ранее.

![Изображение временной шкалы Windows](images/wpf-modernize-tutorial/WindowsTimeline.png)

Действия пользователей отслеживаются с помощью [Microsoft Graph](https://developer.microsoft.com/graph/). Тем не менее при создании приложения Windows 10, не нужно напрямую взаимодействовать с конечными точками REST, предоставляемых Microsoft Graph. Вместо этого можно использовать удобный набор API-интерфейсы WinRT. Мы будем использовать эти API-интерфейсы WinRT в приложении Contoso расходы для отслеживания каждый раз при открытии расходы в приложении и использовать Adaptive карты, чтобы пользователи могли создавать действия.

### <a name="introduction-to-adaptive-cards"></a>Общие сведения об адаптивной карты

Этот раздел содержит краткий обзор [адаптивной карты](https://docs.microsoft.com/adaptive-cards/). Если эта информация не нужна, можно пропустить этот шаг и перейти прямо к [Добавить инструмент Adaptive Cards](#add-an-adaptive-card) инструкции.

Адаптивная карт позволяют разработчикам для обмена содержимым карты в общим и согласованным образом. Инструмент Adaptive Cards описывается полезные данные JSON, определяющий его содержимое, которое может включать текст, изображения, действия и многое другое.

Инструмент Adaptive Cards определяет только то содержимое и не внешний вид содержимого. Платформы, где принимается инструмент Adaptive Cards может отображать содержимое с использованием наиболее подходящего стилей. Предназначены адаптивной карты можно с помощью [там](https://docs.microsoft.com/adaptive-cards/rendering-cards/getting-started), который является возможность использовать полезные данные JSON и преобразовать его в собственный пользовательский Интерфейс. Например пользовательский Интерфейс может быть XAML для WPF или UWP приложения, AXML приложения для Android или HTML для веб-сайт или в беседах программ-роботов.

Вот пример простой инструмент Adaptive Cards полезных данных.

```json
{
    "type": "AdaptiveCard",
    "body": [
        {
            "type": "Container",
            "items": [
                {
                    "type": "TextBlock",
                    "size": "Medium",
                    "weight": "Bolder",
                    "text": "Publish Adaptive Card schema"
                },
                {
                    "type": "ColumnSet",
                    "columns": [
                        {
                            "type": "Column",
                            "items": [
                                {
                                    "type": "Image",
                                    "style": "Person",
                                    "url": "https://pbs.twimg.com/profile_images/3647943215/d7f12830b3c17a5a9e4afcc370e3a37e_400x400.jpeg",
                                    "size": "Small"
                                }
                            ],
                            "width": "auto"
                        },
                        {
                            "type": "Column",
                            "items": [
                                {
                                    "type": "TextBlock",
                                    "weight": "Bolder",
                                    "text": "Matt Hidinger",
                                    "wrap": true
                                },
                                {
                                    "type": "TextBlock",
                                    "spacing": "None",
                                    "text": "Created {{DATE(2017-02-14T06:08:39Z,SHORT)}}",
                                    "isSubtle": true,
                                    "wrap": true
                                }
                            ],
                            "width": "stretch"
                        }
                    ]
                }
            ]
        }
    ],
    "actions": [
        {
            "type": "Action.ShowCard",
            "title": "Set due date",
            "card": {
                "type": "AdaptiveCard",
                "style": "emphasis",
                "body": [
                    {
                        "type": "Input.Date",
                        "id": "dueDate"
                    },
                    {
                        "type": "Input.Text",
                        "id": "comment",
                        "placeholder": "Add a comment",
                        "isMultiline": true
                    }
                ],
                "actions": [
                    {
                        "type": "Action.OpenUrl",
                        "title": "OK",
                        "url": "http://adaptivecards.io"
                    }
                ],
                "$schema": "http://adaptivecards.io/schemas/adaptive-card.json"
            }
        },
        {
            "type": "Action.OpenUrl",
            "title": "View",
            "url": "http://adaptivecards.io"
        }
    ],
    "$schema": "http://adaptivecards.io/schemas/adaptive-card.json",
    "version": "1.0"
}
```

На следующем рисунке показано, как этот код JSON отображается по-разному, ta канала команд, Cortana и уведомление Windows.

![Адаптивная образа визуализации карты](images/wpf-modernize-tutorial/AdaptiveCards.png)

На временной шкале адаптивной карты играть важную роль, так как это способ Windows отображает действия. Каждый эскиз, отображаются внутри временная шкала является фактически инструмент Adaptive cards. Таким образом Если вы собираетесь создать действие пользователя внутри своего приложения, будет предложено предоставить инструмент Adaptive Cards их вывода.

> [!NOTE]
> Используя это отличный способ мозговой штурм виды инструмент Adaptive Cards [online конструктор](https://adaptivecards.io/designer/). У вас будет возможность проектировать карты с помощью стандартных блоков (изображения, тексты, столбцы, и т.д.) и получить соответствующие JSON. После можно представить себе окончательной архитектуры, можно использовать библиотеку с именем [адаптивной карты](https://www.nuget.org/packages/AdaptiveCards/) для упрощения сборки с помощью инструмент Adaptive Cards C# классы вместо простой JSON, который может быть сложно отлаживать и сборки.

### <a name="add-an-adaptive-card"></a>Добавить инструмент Adaptive Cards

1. Щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта в обозревателе решений и выберите **управление пакетами NuGet**.

2. В **диспетчер пакетов NuGet** окно, нажмите кнопку **Обзор**. Поиск `Newtonsoft.Json` упаковке и установке последней доступной версии. Это популярных библиотека манипуляции JSON, который будет использоваться для mainipulate строки JSON, которые требуется адаптивной карты.

    ![Пакет NewtonSoft.Json NuGet](images/wpf-modernize-tutorial/JsonNetNuGet.png)

    > [!NOTE]
    > Если вы не установите `Newtonsoft.Json` отдельных пакетов, библиотека адаптивной карты будет ссылаться на более старую версию `Newtonsoft.Json` пакет, который не поддерживает .NET Core 3.0.

2. В **диспетчер пакетов NuGet** окно, нажмите кнопку **Обзор**. Поиск `AdaptiveCards` упаковке и установке последней доступной версии.

    ![Адаптивная пакет NuGet карты](images/wpf-modernize-tutorial/AdaptiveCardsNuGet.png)

3. В **обозревателе решений**, щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта, выберите **Добавить -> класс**. Назовите класс **TimelineService.cs** и нажмите кнопку **ОК**.

4. В **TimelineService.cs** добавьте следующие инструкции в начало файла.

    ```csharp
    using AdaptiveCards;
    using ContosoExpenses.Data.Models;
    ```

5. Изменение пространства имен объявляются в файле из `ContosoExpenses.Core` для `ContosoExpenses`.

5. Добавьте следующий метод в `TimelineService` класса.

   ```csharp
    private string BuildAdaptiveCard(Expense expense)
    {
        AdaptiveCard card = new AdaptiveCard("1.0");

        AdaptiveTextBlock title = new AdaptiveTextBlock
        {
            Text = expense.Description,
            Size = AdaptiveTextSize.Medium,
            Wrap = true
        };

        AdaptiveColumnSet columnSet = new AdaptiveColumnSet();
        AdaptiveColumn photoColumn = new AdaptiveColumn
        {
            Width = "auto"
        };

        AdaptiveImage image = new AdaptiveImage
        {
            Url = new Uri("https://appmodernizationworkshop.blob.core.windows.net/contosoexpenses/Contoso192x192.png"),
            Size = AdaptiveImageSize.Small,
            Style = AdaptiveImageStyle.Default
        };
        photoColumn.Items.Add(image);

        AdaptiveTextBlock amount = new AdaptiveTextBlock
        {
            Text = expense.Cost.ToString(),
            Weight = AdaptiveTextWeight.Bolder,
            Wrap = true
        };

        AdaptiveTextBlock date = new AdaptiveTextBlock
        {
            Text = expense.Date.Date.ToShortDateString(),
            IsSubtle = true,
            Spacing = AdaptiveSpacing.None,
            Wrap = true
        };

        AdaptiveColumn expenseColumn = new AdaptiveColumn
        {
            Width = "stretch"
        };
        expenseColumn.Items.Add(amount);
        expenseColumn.Items.Add(date);

        columnSet.Columns.Add(photoColumn);
        columnSet.Columns.Add(expenseColumn);

        card.Body.Add(title);
        card.Body.Add(columnSet);

        string json = card.ToJson();
        return json;
    }
    ```

#### <a name="about-the-code"></a>Сведения о коде

Этот метод получает **расходов** создает новый объект со всеми сведениями о расходов для подготовки к просмотру и его **AdaptiveCard** объекта. Этот метод добавляет следующие карты:

- Заголовок, который использует Описание расхода.
- Изображение, которое является логотип компании Contoso.
- Сумма расходов.
- Дата расходов.

3 последних элементов разбиваются на двух разных столбцов, чтобы логотип компании Contoso и сведений об о расходах можно поместить рядом друг с другом. После построения объекта, метод возвращает соответствующую строку JSON с помощью **ToJson** метод.

### <a name="define-the-user-activity"></a>Определение активности пользователей

Теперь, когда вы определили инструмент Adaptive Cards, можно создать на его основе действии пользователя.

1. Добавьте следующие операторы в верхнюю часть **TimelineService.cs** файла:

    ```csharp
    using Windows.ApplicationModel.UserActivities;
    using System.Threading.Tasks;
    using Windows.UI.Shell;
    ```

    > [!NOTE]
    > Ниже приведены пространства имен универсальной платформы Windows. Эти разрешения, так как `Microsoft.Toolkit.Wpf.UI.Controls` пакет NuGet, который вы установили на шаге 2 включает ссылку на `Microsoft.Windows.SDK.Contracts` пакет, позволяющий **ContosoExpenses.Core** проекта Справочник по API-интерфейсы WinRT, несмотря на то, что это .NET Проект Core 3.

2. Добавьте следующие объявления поля для `TimelineService` класса.

    ```csharp
    private UserActivityChannel _userActivityChannel;
    private UserActivity _userActivity;
    private UserActivitySession _userActivitySession;
    ```

3. Добавьте следующий метод в `TimelineService` класса.

    ```csharp
    public async Task AddToTimeline(Expense expense)
    {
        _userActivityChannel = UserActivityChannel.GetDefault();
        _userActivity = await _userActivityChannel.GetOrCreateUserActivityAsync($"Expense-{expense.ExpenseId}");

        _userActivity.ActivationUri = new Uri($"contosoexpenses://expense/{expense.ExpenseId}");
        _userActivity.VisualElements.DisplayText = "Contoso Expenses";

        string json = BuildAdaptiveCard(expense);

        _userActivity.VisualElements.Content = AdaptiveCardBuilder.CreateAdaptiveCardFromJson(json);

        await _userActivity.SaveAsync();
        _userActivitySession?.Dispose();
        _userActivitySession = _userActivity.CreateSession();
    }
    ```

4. Сохраните изменения в **TimelineService.cs**.

#### <a name="about-the-code"></a>Сведения о коде

`AddToTimeline` Метод сначала получает **UserActivityChannel** объектов, необходимых для хранения действий пользователей. Затем он создает новое действие пользователя с помощью **GetOrCreateUserActivityAsync** метод, который требуется уникальный идентификатор. Таким образом, если действие уже существует, его можно обновить; в противном случае он создаст новый. Идентификатор для передачи зависит от того, вид приложения, которые вы создаете:

* Если вы хотите всегда обновлять того же действия, чтобы временная шкала будут отображаться только самую последнюю из них, можно использовать фиксированный идентификатор (например **расходы**).
* Если вы хотите отслеживать каждое действие, как его, чтобы временной шкалы будут показаны все из них, можно использовать динамический идентификатор.

В этом случае приложение будет отслеживать каждый открытый расходов как действие другого пользователя, поэтому код создает каждый идентификатор с помощью ключевого слова **расходов -** следуют расходов уникальный идентификатор.

После того как метод создаст **UserActivity** объекта, он заполняет объект следующие сведения:

* **ActivationUri** , вызываемой при нажатии на действии на временной шкале. Код использует пользовательский протокол под названием **contosoexpenses** , приложение будет обрабатывать более поздней версии.
* **VisualElements** объект, содержащий набор свойств, которые определяют внешний вид действия. Этот код задает **DisplayText** (то есть заголовок, отображаемый поверх запись на временной шкале) и **содержимого**. 

Это происходит, когда инструмент Adaptive Cards, определенную ранее играет роль. Приложение передает инструмент Adaptive Cards, созданной ранее как содержимое в метод. Тем не менее, Windows 10 используется объект для представления карты, по сравнению с, используемая `AdaptiveCards` пакет NuGet. Таким образом, метод повторно создает карту с помощью **CreateAdaptiveCardFromJson** метода, предоставляемого **AdaptiveCardBuilder** класса. После того как метод создаст активности пользователей, он сохраняет действия и создает новый сеанс.

Когда пользователь щелкает действия на временной шкале, **contosoexpenses: / /** будет активирован протокол и URL-адрес будет включать сведения, нужные приложению для получения выбранного расходов. Как необязательная задача можно реализовать протокол активации, чтобы приложение реагирует должным образом, когда пользователь использует временную шкалу.

### <a name="integrate-the-application-with-timeline"></a>Интеграция приложения с временной шкалы

Теперь, когда вы создали класс, который взаимодействует с временной шкалой, мы сможем использовать ее для улучшения взаимодействия с приложением. Лучше всего использовать **AddToTimeline** метода, предоставляемого **TimelineService** класса — когда пользователь открывает странице сведений о расходах.

1. В **ContosoExpenses.Core** проекта, разверните **ViewModels** и откройте **ExpenseDetailViewModel.cs** файла. Это ViewModel, которая поддерживает подробно о расходах.

2. Найдите открытый конструктор **ExpenseDetailViewModel** класса и добавьте следующий код в конце конструктора. Каждый раз, когда открывается окно расходов, метод вызывает **AddToTimeline** метод и передает текущее расходов. **TimelineService** класс использует эти сведения для создания действий пользователей, используя сведения о расходах.

    ```csharp
    TimelineService timeline = new TimelineService();
    timeline.AddToTimeline(expense);
    ```

    Когда вы закончите, конструктор должен выглядеть следующим образом.

    ```csharp
    public ExpensesDetailViewModel(IDatabaseService databaseService, IStorageService storageService)
    {
        var expense = databaseService.GetExpense(storageService.SelectedExpense);

        ExpenseType = expense.Type;
        Description = expense.Description;
        Location = expense.Address;
        Amount = expense.Cost;

        TimelineService timeline = new TimelineService();
        timeline.AddToTimeline(expense);
    }
    ```

3. Нажмите клавишу F5 для сборки и запуска приложения в отладчике. Выберите сотрудник из списка, а затем выберите расходы. На странице сведений ознакомьтесь с описанием расходов "," Дата "и" сумму.

4. Нажмите клавишу **Пуск + ВКЛАДКЕ** Открытие временной шкалы.

5. Прокрутите вниз список открытое приложений, пока не появится в разделе **сегодня**. В этом разделе показаны некоторые из последних действиях пользователей. Нажмите кнопку **см. в разделе о действиях** ссылку рядом с полем **сегодня** заголовок.

6. Убедитесь, что вы видите новую карту с информацией о расходе, выбранную в приложении.

    ![Временная шкала расходы Contoso](images/wpf-modernize-tutorial/ContosoExpensesTimeline.png)

7. Если теперь открыть другие расходы, вы увидите новые карточки, добавляемого в качестве действий пользователей. Помните, что в коде используется другой идентификатор для каждого действия, поэтому он создает карточку для каждого расходов открыть в приложении.

8. Закройте приложение.

## <a name="add-a-notification"></a>Добавить уведомление

Вторая функция, команда разработчиков компании Contoso хочет добавить — это уведомление, который отображается для пользователя при каждом сохранении нового расходов в базу данных. Чтобы сделать это, вы можете использовать встроенные уведомления системы в Windows 10, который предоставляется разработчикам через API-интерфейсы WinRT. Эта система уведомлений имеет множество преимуществ:

- Уведомления являются согласованными с остальной частью операционной системы.
- Они являются практические.
- Они сохраняются в центре поддержки, их можно просмотреть позже.

Чтобы добавить уведомление в приложение:

1. В **обозревателе решений**, щелкните правой кнопкой мыши **ContosoExpenses.Core** проекта, выберите **Добавить -> класс**. Назовите класс **NotificationService.cs** и нажмите кнопку **ОК**.

2. В **NotificationService.cs** добавьте следующие инструкции в начало файла.

    ```csharp
    using Windows.Data.Xml.Dom;
    using Windows.UI.Notifications;
    ```

3. Изменение пространства имен объявляются в файле из `ContosoExpenses.Core` для `ContosoExpenses`.

4. Добавьте следующий метод в `NotificationService` класса.

    ```csharp
    public void ShowNotification(string description, double amount)
    {
        string xml = $@"<toast>
                          <visual>
                            <binding template='ToastGeneric'>
                              <text>Expense added</text>
                              <text>Description: {description} - Amount: {amount} </text>
                            </binding>
                          </visual>
                        </toast>";

        XmlDocument doc = new XmlDocument();
        doc.LoadXml(xml);

        ToastNotification toast = new ToastNotification(doc);
        ToastNotificationManager.CreateToastNotifier().Show(toast);
    }
    ```

    Всплывающие уведомления, представлены полезные данные XML, который может включать текст, изображения, действия и многое другое. Можно найти все поддерживаемые элементы [здесь](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/toast-schema). Этот код использует очень простую схему с помощью двух строк текста: заголовок и текст. После того как код определяет полезные данные XML и загружает их в **XmlDocument** объекта, он создает оболочку для XML-данные в **ToastNotification** объекта и отображает их с помощью **ToastNotificationManager** класса.

5. В **ContosoExpenses.Core** проекта, разверните **ViewModels** и откройте **AddNewExpenseViewModel.cs** файла. 

6. Найдите `SaveExpenseCommand` метод, который активируется, когда пользователь нажимает кнопку, чтобы сохранить новый расход. Добавьте следующий код в этот метод, сразу после вызова `SaveExpense` метод.

    ```csharp
    NotificationService notificationService = new NotificationService();
    notificationService.ShowNotification(expense.Description, expense.Cost);
    ```

    Когда вы закончите, `SaveExpenseCommand` метод должен выглядеть следующим образом.

    ```csharp
    private RelayCommand _saveExpenseCommand;
    public RelayCommand SaveExpenseCommand
    {
        get
        {
            if (_saveExpenseCommand == null)
            {
                _saveExpenseCommand = new RelayCommand(() =>
                {
                    Expense expense = new Expense
                    {
                        Address = Address,
                        City = City,
                        Cost = Cost,
                        Date = Date,
                        Description = Description,
                        EmployeeId = storageService.SelectedEmployeeId,
                        Type = ExpenseType
                    };

                    databaseService.SaveExpense(expense);

                    NotificationService notificationService = new NotificationService();
                    notificationService.ShowNotification(expense.Description, expense.Cost);

                    Messenger.Default.Send<UpdateExpensesListMessage>(new UpdateExpensesListMessage());
                    Messenger.Default.Send<CloseWindowMessage>(new CloseWindowMessage());
                }, () => IsFormFilled
                );
            }

            return _saveExpenseCommand;
        }
    }
    ```

7. Нажмите клавишу F5 для сборки и запуска приложения в отладчике. Выберите сотрудник из списка и нажмите кнопку **добавить новый расход** кнопки. Заполните все поля в форме и нажмите клавишу **Сохранить**.

8. Вы получите следующее исключение.

    ![Ошибка всплывающие уведомления](images/wpf-modernize-tutorial/ToastNotificationError.png)

Это исключение связано с тем, что удостоверение пакета еще нет приложения Contoso. Некоторые интерфейсы API WinRT, включая уведомления, API, требуется идентификатор пакета, прежде чем они могут использоваться в приложении. Удостоверение пакета по умолчанию получают приложений универсальной платформы Windows, так как они могут размещаться только посредством MSIX пакетов. Другие виды приложений Windows, включая приложения WPF, можно также развертывать посредством MSIX пакетов, чтобы получить удостоверение пакета. В следующей части этого учебника будет рассказано, как это сделать.

## <a name="next-steps"></a>Следующие шаги

На этом этапе в учебнике добавлен действием пользователя к приложению, которое интегрируется с временной шкалой Windows, а также добавлении уведомление в приложение, которое активируется, когда пользователи создают новый расход. Тем не менее уведомления еще не работает, так как для приложения требуется идентификатор пакета, чтобы использовать уведомления о API. Чтобы узнать, как выполнить сборку пакета MSIX для приложения, чтобы получить удостоверение пакета и получить другие преимущества развертывания, см. в разделе [часть 5: Упаковка и развертывание с помощью MSIX](modernize-wpf-tutorial-5.md).
