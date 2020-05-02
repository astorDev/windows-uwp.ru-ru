---
description: В этом руководстве показано, как добавлять пользовательские интерфейсы UWP XAML, создавать пакеты MSIX и включать другие актуальные компоненты в приложения WPF.
title: Добавление действий пользователей и уведомлений для Windows 10
ms.topic: article
ms.date: 06/27/2019
ms.author: mcleans
author: mcleanbyron
keywords: Windows 10, UWP, Windows Forms, WPF, объекты XAML Island
ms.localizationpriority: medium
ms.custom: RS5, 19H1
ms.openlocfilehash: 8443ac25ba678986046b967a90a8899eaffb76aa
ms.sourcegitcommit: 76e8b4fb3f76cc162aab80982a441bfc18507fb4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67420118"
---
# <a name="part-4-add-windows-10-user-activities-and-notifications"></a>Часть 4. Добавление действий пользователей и уведомлений для Windows 10

Это четвертая часть руководства, в которой рассматривается модернизация примера классического приложения WPF, которое называется Contoso Expenses. Общие сведения об этом руководстве, предварительные требования и инструкции по скачиванию примера приложения см. в разделе [Руководство. Модернизация приложения WPF](modernize-wpf-tutorial.md). В этой статье предполагается, что вы уже выполнили [часть 3](modernize-wpf-tutorial-3.md).

В предыдущих частях этого руководства вы добавили в приложение элементы управления UWP XAML, используя области XAML. В результате этого вы также обеспечили возможность вызова любого API WinRT в приложении. Это позволяет приложению использовать множество других функций, предлагаемых Windows 10, а не только элементы управления UWP XAML.

В вымышленном сценарии, рассматриваемом в этом руководстве, группа разработки Contoso решила добавить в приложение две новые функции: действия и уведомления. В этой части руководства показано, как реализовать данные функции.

## <a name="add-a-user-activity"></a>Добавление действия пользователя

В Windows 10 приложения могут отслеживать действия, выполняемые пользователем, например открытие файла или просмотр определенной страницы. Затем эти действия добавляются на временную шкалу — компонент, появившийся в Windows 10 версии 1803. Временная шкала позволяет пользователю быстро вернуться к начатому ранее действию и возобновить его выполнение.

![Изображение временной шкалы Windows](images/wpf-modernize-tutorial/WindowsTimeline.png)

Для отслеживания действий пользователя используется [Microsoft Graph](https://developer.microsoft.com/graph/). Однако при создании приложения Windows 10 вам не нужно напрямую взаимодействовать с конечными точками REST, предоставленными Microsoft Graph. Вместо этого можно использовать удобный набор API WinRT. Мы будем использовать эти API WinRT в приложении Contoso Expenses, чтобы отслеживать, когда пользователь открывает страницу расходов в приложении, и использовать адаптивные карточки, чтобы пользователи могли создавать действия.

### <a name="introduction-to-adaptive-cards"></a>Введение в адаптивные карточки

В этом разделе кратко описываются [адаптивные карточки](https://docs.microsoft.com/adaptive-cards/). Если эти сведения вам не нужны, можно пропустить данный раздел и сразу перейти к инструкциям по [добавлению адаптивной карточки](#add-an-adaptive-card).

Адаптивные карточки дают разработчикам возможность обмениваться содержимым карточек обычным и стандартным способом. Адаптивная карточка описывается полезными данными JSON, определяющими ее содержимое: текст, изображения, действия и многое другое.

Адаптивная карточка определяет только содержимое, а не его внешний вид. Платформа, в которой получена адаптивная карточка, может визуализировать ее содержимое с помощью наиболее подходящего стиля. В основе адаптивных карточек лежит [отрисовщик](https://docs.microsoft.com/adaptive-cards/rendering-cards/getting-started), который может принять полезные данные JSON и преобразовать их в собственный пользовательский интерфейс. Например, это может быть пользовательский интерфейс XAML для приложения WPF или UWP, пользовательский интерфейс AXML для приложения Android или пользовательский интерфейс HTML для веб-сайта или чат-бота.

Ниже приведен пример простых полезных данных адаптивной карточки.

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

На рисунке ниже показано, как этот код JSON визуализируется различными способами — в канале Teams, Кортане и уведомлении Windows.

![Изображение визуализации адаптивной карточки](images/wpf-modernize-tutorial/AdaptiveCards.png)

Адаптивные карточки играют важную роль на временной шкале, так как определяют способ отображения действий в Windows. Каждый эскиз, отображаемый во временной шкале, на самом деле является адаптивной карточкой. Таким образом, когда вы соберетесь создать действие пользователя в приложении, вам будет предложено предоставить адаптивную карточку для его визуализации.

> [!NOTE]
> Для мозгового штурма оформления адаптивной карточки удобно использовать [конструктор в Интернете](https://adaptivecards.io/designer/). Вы сможете спроектировать карточку с помощью стандартных блоков (изображений, блоков текста, столбцов и т. д.) и получить соответствующий код JSON. Окончательно выбрав оформление, вы сможете использовать библиотеку [Adaptive Cards](https://www.nuget.org/packages/AdaptiveCards/), чтобы упростить создание адаптивной карточки с помощью классов C# вместо простого кода JSON, отладка и сборка которого сопряжена со сложностями.

### <a name="add-an-adaptive-card"></a>Добавление адаптивной карточки

1. Щелкните правой кнопкой мыши проект **ContosoExpenses.Core** в Обозревателе решений и выберите **Управление пакетами NuGet**.

2. В окне **диспетчера пакетов NuGet** щелкните **Обзор**. Найдите пакет `Newtonsoft.Json` и установите последнюю доступную версию. Это популярная библиотека для работы с JSON, с помощью которой вы будете обрабатывать строки JSON, необходимые для адаптивных карточек.

    ![Пакет NuGet NewtonSoft.json](images/wpf-modernize-tutorial/JsonNetNuGet.png)

    > [!NOTE]
    > Если не установить пакет `Newtonsoft.Json` отдельно, библиотека Adaptive Cards будет ссылаться на более раннюю версию пакета `Newtonsoft.Json`, который не поддерживает платформу .NET Core 3.0.

2. В окне **диспетчера пакетов NuGet** щелкните **Обзор**. Найдите пакет `AdaptiveCards` и установите последнюю доступную версию.

    ![Пакет NuGet для адаптивных карточек](images/wpf-modernize-tutorial/AdaptiveCardsNuGet.png)

3. В **обозревателе решений** щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Добавить > Класс**. Присвойте классу имя **TimelineService.cs** и нажмите кнопку **ОК**.

4. В начало файла **TimelineService.cs** добавьте следующие операторы.

    ```csharp
    using AdaptiveCards;
    using ContosoExpenses.Data.Models;
    ```

5. Измените пространство имен `ContosoExpenses.Core`, объявленное в файле, на `ContosoExpenses`.

5. Добавьте следующий метод в класс `TimelineService`.

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

#### <a name="about-the-code"></a>О коде

Этот метод получает объект **Expense** со всеми сведениями о затратах для визуализации и создает объект **AdaptiveCard**. Этот метод добавляет в карточку:

- заголовок, содержащий описание расходов;
- изображение — логотип Contoso;
- сумму расходов;
- дату расходов.

Последние 3 элемента разбиты на два отдельных столбца, чтобы логотип Contoso и подробные сведения о расходах можно было разместить параллельно. После создания объекта метод возвращает соответствующую строку JSON с помощью метода **ToJson**.

### <a name="define-the-user-activity"></a>Определение действия пользователя

Теперь, когда вы определили адаптивную карточку, на ее основе можно создать действие пользователя.

1. В начало файла **TimelineService.cs** добавьте следующие операторы.

    ```csharp
    using Windows.ApplicationModel.UserActivities;
    using System.Threading.Tasks;
    using Windows.UI.Shell;
    ```

    > [!NOTE]
    > Это пространства имен UWP. Они разрешаются, так как пакет NuGet `Microsoft.Toolkit.Wpf.UI.Controls`, установленный на шаге 2, содержит ссылку на пакет `Microsoft.Windows.SDK.Contracts`. Это позволяет проекту **ContosoExpenses.Core** ссылаться на интерфейсы API WinRT, даже если это проект на основе .NET Core 3.

2. Добавьте в класс `TimelineService` приведенные ниже объявления полей.

    ```csharp
    private UserActivityChannel _userActivityChannel;
    private UserActivity _userActivity;
    private UserActivitySession _userActivitySession;
    ```

3. Добавьте следующий метод в класс `TimelineService`.

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

4. Сохраните изменения в файле **TimelineService.cs**.

#### <a name="about-the-code"></a>О коде

Метод `AddToTimeline` сначала получает объект **UserActivityChannel**, необходимый для хранения действий пользователя. Затем он создает действие пользователя с помощью метода **GetOrCreateUserActivityAsync**, для чего требуется уникальный идентификатор. В этом случае, если действие уже существует, приложение сможет его обновить. В противном случае будет создано новое действие. Передаваемый идентификатор зависит от типа приложения, которое вы создаете.

* Если вы хотите постоянно обновлять одно и то же действие, чтобы на временной шкале отображалось только самое последнее действие, можно использовать фиксированный идентификатор (например, **Expenses**).
* Если вы хотите отслеживать каждое действие по отдельности, чтобы они все отображались на временной шкале, можно использовать динамический идентификатор.

В этом случае приложение будет отслеживать каждую открытую статью расходов как отдельное действие пользователя, поэтому код создает каждый идентификатор с помощью ключевого слова **Expense-** , за которым следует уникальный идентификатор расходов.

После того как метод создает объект **UserActivity** и заполняет его следующими сведениями.

* Идентификатор **ActivationUri**, который вызывается, когда пользователь щелкает действие на временной шкале. В коде используется настраиваемый протокол **contosoexpenses**, который приложение обработает позже.
* Объект **VisualElements**, который содержит набор свойств, определяющих внешний вид действия. Этот код задает **DisplayText** (заголовок, отображаемый в верхней части записи на временной шкале) и **Content**. 

Именно здесь в действие вступает адаптивная карточка, определенная ранее. Приложение передает в метод адаптивную карточку, которая была разработана ранее, в качестве содержимого. Однако в Windows 10 для представления карточки используется объект, отличный от аналогичного объекта в пакете NuGet `AdaptiveCards`. Таким образом, метод повторно создает карточку с помощью метода **CreateAdaptiveCardFromJson**, предоставляемого классом **AdaptiveCardBuilder**. После того как метод создает действие пользователя, он сохраняет действие и создает новый сеанс.

Когда пользователь щелкает действие на временной шкале, активируется протокол **contosoexpenses://** , а URL-адрес будет содержать сведения, необходимые приложению для получения выбранной статьи расходов. В качестве дополнительной задачи можно реализовать активацию протокола, чтобы приложение правильно реагировало, когда пользователь использует временную шкалу.

### <a name="integrate-the-application-with-timeline"></a>Интеграция приложения с временной шкалой

Теперь, когда вы создали класс, взаимодействующий с временной шкалой, мы можем использовать его для улучшения работы приложения. Лучше всего использовать метод **AddToTimeline**, предоставляемый классом **TimelineService**, когда пользователь открывает страницу сведений о расходах.

1. В проекте **ContosoExpenses.Core** разверните папку **ViewModels** и откройте файл **ExpenseDetailViewModel.cs**. Это объект ViewModel, который поддерживает окно сведений о расходах.

2. Найдите общедоступный конструктор класса **ExpenseDetailViewModel** и добавьте в его конец приведенный ниже код. При открытии окна расходов этот метод вызывает метод **AddToTimeline** и передает текущие данные расходов. Класс **TimelineService** использует эти данные для создания действия пользователя на основе сведений о расходах.

    ```csharp
    TimelineService timeline = new TimelineService();
    timeline.AddToTimeline(expense);
    ```

    По завершении конструктор должен выглядеть следующим образом.

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

3. Нажмите клавишу F5, чтобы выполнить сборку приложения и открыть его в отладчике. Выберите сотрудника из списка, а затем выберите одну из статей расходов. На странице сведений обратите внимание на описание, дату и сумму расходов.

4. Нажмите **Пуск + TAB**, чтобы открыть временную шкалу.

5. Прокрутите вниз список открытых приложений, пока не увидите раздел **Ранее сегодня**. В этом разделе показаны некоторые из последних действий пользователя. Щелкните ссылку **Просмотреть все действия** рядом с заголовком **Ранее сегодня**.

6. Убедитесь, что вы видите новую карточку со сведениями о расходах, которые вы только что выбрали в приложении.

    ![Временная шкала Contoso Expenses](images/wpf-modernize-tutorial/ContosoExpensesTimeline.png)

7. Если теперь открыть другую статью расходов, вы увидите, как добавятся новые карты в качестве действий пользователя. Помните, что код использует разные идентификаторы для каждого действия, поэтому он создает карточку для каждой статьи расходов, открываемой в приложении.

8. Закройте приложение.

## <a name="add-a-notification"></a>Добавление уведомления

Второй компонент, который группа разработки Contoso хочет добавить, — это уведомление, отображаемое пользователю при каждом сохранении новой статьи расходов в базе данных. Для этого можно использовать встроенную систему уведомлений Windows 10, которая предоставляется разработчикам через интерфейсы API WinRT. Эта система уведомлений имеет множество преимуществ:

- уведомления согласованы с остальной частью операционной системы;
- они позволяют действовать;
- они сохраняются в центре уведомлений, поэтому их можно просмотреть позже.

Чтобы добавить уведомление в приложение:

1. В **обозревателе решений** щелкните правой кнопкой мыши проект **ContosoExpenses.Core** и выберите **Добавить > Класс**. Присвойте классу имя **NotificationService.cs** и нажмите кнопку **ОК**.

2. В начало файла **NotificationService.cs** добавьте следующие операторы.

    ```csharp
    using Windows.Data.Xml.Dom;
    using Windows.UI.Notifications;
    ```

3. Измените пространство имен `ContosoExpenses.Core`, объявленное в файле, на `ContosoExpenses`.

4. Добавьте следующий метод в класс `NotificationService`.

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

    Всплывающие уведомления представлены в виде полезных данных XML, которые могут содержать текст, изображения, действия и многое другое. Все поддерживаемые элементы описаны [здесь](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/toast-schema). Этот код использует очень простую схему с двумя строками текста: заголовком и основным текстом. После того как код определит полезные данные XML и загрузит их в объект **XmlDocument**, он поместит эти данные XML в объект **ToastNotification** и отобразит его с помощью класса **ToastNotificationManager**.

5. В проекте **ContosoExpenses.Core** разверните папку **ViewModels** и откройте файл **AddNewExpenseViewModel.cs**. 

6. Выберите метод `SaveExpenseCommand`, который активируется при нажатии пользователем кнопки для сохранения новой статьи расходов. Добавьте в этом метод приведенный ниже код, сразу после вызова метода `SaveExpense`.

    ```csharp
    NotificationService notificationService = new NotificationService();
    notificationService.ShowNotification(expense.Description, expense.Cost);
    ```

    По завершении метод `SaveExpenseCommand` должен выглядеть следующим образом.

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

7. Нажмите клавишу F5, чтобы выполнить сборку приложения и открыть его в отладчике. Выберите из списка сотрудника и нажмите кнопку **Add new expense** (Добавить новую статью расходов). Заполните все поля в этой форме и щелкните **Save** (Сохранить).

8. Будет порождено исключение, как показано ниже.

    ![Ошибка всплывающего уведомления](images/wpf-modernize-tutorial/ToastNotificationError.png)

Это исключение вызвано тем, что у приложения Contoso Expenses еще нет идентификатора пакета. Для использования в приложении некоторых API WinRT, включая API уведомлений, требуется идентификатор пакета. Приложения UWP получают идентификатор пакета по умолчанию, так как они могут распространяться только через пакеты MSIX. Другие типы приложений для Windows, включая приложения WPF, также можно разворачивать с помощью пакетов MSIX, чтобы получить идентификатор пакета. В следующей части этого руководства будет описано, как это сделать.

## <a name="next-steps"></a>Дальнейшие действия

На этом этапе работы с руководством вы успешно добавили действие пользователя в приложение, которое интегрируется с временной шкалой Windows, а также добавили в это приложение уведомление, которое активируется при создании новой статьи расходов пользователями. Однако это уведомление еще не работает, так как приложению требуется идентификатор пакета для использования API уведомлений. Сведения о том, как создать пакет MSIX для приложения, чтобы получить идентификатор пакета и воспользоваться другими преимуществами развертывания, приведены в разделе [Часть 5. Упаковка и развертывание с помощью MSIX](modernize-wpf-tutorial-5.md)
