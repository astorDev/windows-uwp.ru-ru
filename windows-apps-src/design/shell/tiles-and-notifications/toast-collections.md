---
author: manoskow
Description: Learn how to group notifications in Action Center using collections.
title: Коллекции всплывающих уведомлений
label: Toast Collections
template: detail.hbs
ms.author: mijacobs
ms.date: 05/16/2018
ms.topic: article
keywords: Windows 10, uwp, уведомления, коллекций, коллекция, группы уведомлений, группировка уведомлений, группа, упорядочивание, центр уведомлений, всплывающее уведомление
ms.localizationpriority: medium
ms.openlocfilehash: be7c4ec2e9a47eeeb00663ae94f89e44c6751352
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2018
ms.locfileid: "7579403"
---
# <a name="grouping-toast-notifications-with-collections"></a>Группирование всплывающих уведомлений с помощью коллекций
Коллекции можно использовать для упорядочивания всплывающих уведомлений вашего приложения в центре уведомлений. Коллекции помогают пользователям проще находить сведения в центре уведомлений, а разработчикам — лучше управлять своими уведомлениями.  API-интерфейсы ниже позволяют удалять, создавать и обновлять коллекции уведомлений.

> [!IMPORTANT]
> **Требуется обновление Creators Update**. Обратите внимание, что для использования коллекций всплывающих уведомлений необходим пакет SDK 15063 и сборка 15063 или более поздней версии. Связанные с API: [Windows.UI.Notifications.ToastCollection](https://docs.microsoft.com/en-us/uwp/api/windows.ui.notifications.toastcollection) и [Windows.UI.Notifications.ToastCollectionManager](https://docs.microsoft.com/en-us/uwp/api/windows.ui.notifications.toastcollectionmanager)

В примере ниже представлено приложение для обмена сообщениями, разделяющее уведомления на основании группы чатов. Каждый чат (чат Comp Sci 160A Project, чат Direct Messages, чат Lacrosse Team) является отдельной коллекцией.  Обратите внимание, на четкую группировку уведомлений, как если бы все они принадлежали отдельному приложению, несмотря на то, что все они являются уведомлениями из одного и того же приложения.  Если вам нужен более тонкий способ упорядочивания уведомлений, см. раздел [Всплывающие заголовки](toast-headers.md).  
![Пример коллекции с двумя разными группами уведомлений](images/toast-collection-example.png)

## <a name="creating-collections"></a>Создание коллекций
При создании каждой коллекции необходимо указать отображаемое имя и значок, которые будут отображаться в центре уведомлений в рамках названия коллекции, как показано на рисунке выше. Для коллекций также необходим аргумент запуска, который будет помогать приложению переходить в правильное положение в рамках приложения, когда пользователь будет щелкать название коллекции.  

### <a name="create-a-collection"></a>Создание коллекции

``` csharp 
public const string toastCollectionId = "ToastCollection";

// Create a toast collection
public async void CreateToastCollection()
{
    string displayName = "Work Email"; 
    string launchArg = "NavigateToWorkEmailInbox"; 
    Uri icon = new Windows.Foundation.Uri("ms-appx:///Assets/workEmail.png");

    // Constructor
    ToastCollection workEmailToastCollection = new ToastCollection(MainPage.toastCollectionId, 
        displayName,
        launchArg, 
        icon);

    // Calls the platform to create the collection
    await ToastNotificationManager.GetDefault().GetToastCollectionManager().SaveToastCollectionAsync(workEmailToastCollection);                                 
}
```

## <a name="sending-notifications-to-a-collection"></a>Отправка уведомлений в коллекцию
Рассмотрим процесс отправки уведомлений из трех разных каналов всплывающих уведомлений: локальные, запланированные и push-уведомления.  Для каждого из этих примеров мы создадим пример кода всплывающего уведомления для отправки, а затем расскажем о том, как добавить всплывающее уведомление в коллекцию через каждый канал.

Создание полезных данных уведомления.

``` csharp
public const string toastCollectionId = "MyToastCollection";

public async void SendToastToToastCollection()
{
    // Construct the notification Content
    string toastXmlString = 
        $@"<toast launch=’’>
            <visual>
                <binding template=’ToastGeneric’>
                    <text>Hello,</text>
                    <text>it’s me</text>
                </binding>
            </visual>
        </toast>";
    // Convert to XML
    XmlDocument toastXml = new XmlDocment();
    toastXml.LoadXml(toastXmlString);
    ToastNotification toast = new ToastNotification(toastXml);
```

### <a name="send-a-toast-to-a-collection"></a>Отправка всплывающего уведомления в коллекцию

```csharp
// Get the collection notifier
var notifier = await ToastNotificationManager.GetDefault().GetToastNotifierForToastCollectionIdAsync(MainPage.toastCollectionId);

// And show the toast
notifier.Show(toast);
```

### <a name="add-a-scheduled-toast-to-a-collection"></a>Добавление запланированного всплывающего уведомления в коллекцию

``` csharp
// Create scheduled toast from XML above
ScheduledToastNotification scheduledToast = new ScheduledToastNotification(toastXml, DateTimeOffset.Now.AddSeconds(10));

// Get notifier
var notifier = await ToastNotificationManager.GetDefault().GetToastNotifierForToastCollectionIdAsync(MainPage.toastCollectionId);
    
// Add to schedule
notifier.AddToSchedule(scheduledToast);
```

### <a name="send-a-push-toast-to-a-collection"></a>Отправка всплывающего push-уведомления в коллекцию
Для всплывающих push-уведомлений необходимо добавить заголовок X-WNS-CollectionId в сообщение POST.
```csharp
// Add header to HTTP request
request.Headers.Add("X-WNS-CollectionId", collectionId); 

```

## <a name="managing-collections"></a>Управление коллекциями
#### <a name="create-the-toast-collection-manager"></a>Создание диспетчера коллекций всплывающих уведомлений
Для остальных фрагментов кода в разделе "Управление коллекциями" мы используем диспетчер collectionManager, представленный ниже.
```csharp
ToastCollectionManger collectionManager = ToastNotificationManager.GetDefault().GetToastCollectionManager();
```

#### <a name="get-all-collections"></a>Получение всех коллекций

``` csharp
IReadOnlyList<ToastCollection> collections = await collectionManager.FindAllToastCollectionsAsync();
``` 

#### <a name="get-the-number-of-collections-created"></a>Получение числа созданных коллекций

``` csharp
int toastCollectionCount = (await collectionManager.FindAllToastCollectionsAsync()).Count;
```

#### <a name="remove-a-collection"></a>Удаление коллекции

``` csharp
await collectionManager.RemoveToastCollectionAsync(MainPage.toastCollectionId);
```

#### <a name="update-a-collection"></a>Обновление коллекции
Вы можете обновлять коллекции, создавая новую коллекцию с таким же идентификатором и сохраняя новый экземпляр коллекции.
``` csharp
string displayName = "Updated Display Name"; 
string launchArg = "UpdatedLaunchArgs"; 
Uri icon = new Windows.Foundation.Uri("ms-appx:///Assets/updatedPicture.png");

// Construct a new toast collection with the same collection id
ToastCollection updatedToastCollection = new ToastCollection(MainPage.toastCollectionId, 
            displayName,
            launchArg, 
            icon);

// Calls the platform to update the collection by saving the new instance
await collectionManager.SaveToastCollectionAsync(updatedToastCollection);                               
```
## <a name="managing-toasts-within-a-collection"></a>Управление всплывающими уведомлениями в коллекции
#### <a name="group-and-tag-properties"></a>Свойства групп и тегов
Свойства групп и тегов вместе взятые уникальным образом определяют уведомление в коллекции.  Группа (и тег) выступают в качестве составного первичного ключа (более одного идентификатора) для определения уведомления. Например, если вы хотите удалить или заменить уведомление, необходимо иметь возможность указать, *какое уведомление* вы хотите удалить или заменить; это делается путем указания тега и группы. Пример — приложение для обмена сообщениями  Разработчики могут использовать идентификатор беседы в качестве группы и идентификатор сообщения в качестве тега.

#### <a name="remove-a-toast-from-a-collection"></a>Удаление всплывающего уведомления из коллекции
Можно удалить отдельные всплывающие уведомления с помощью идентификаторов тега и группы или очистить все всплывающие уведомления в коллекции.
``` csharp
// Get the history
var collectionHistory = await ToastNotificationManager.GetDefault().GetHistoryForToastCollectionAsync(MainPage.toastCollectionId);

// Remove toast
collectionHistory.Remove(tag, group); 
```

#### <a name="clear-all-toasts-within-a-collection"></a>Очистка всех всплывающих уведомлений в коллекции
``` csharp
// Get the history
var collectionHistory = await ToastNotificationManager.GetDefault().GetHistoryForToastCollectionAsync(MainPage.toastCollectionId);

// Remove toast
collectionHistory.Clear();
```


## <a name="collections-in-notifications-visualizer"></a>Коллекции в визуализаторе уведомлений
Для проектирования коллекций можно использовать средство [Визуализатор уведомлений](notifications-visualizer.md). Выполните инструкции ниже.

* Щелкните значок в виде шестерни в правом нижнем углу. 
* Выберите "Коллекции всплывающих уведомлений".
* Над предпросмотром всплывающего уведомления находится раскрывающееся меню "Коллекция всплывающих уведомлений". Выберите "Управление коллекциями".
* Нажмите кнопку "Добавить коллекцию", заполните сведения о коллекции и нажмите "Сохранить".
* Можно добавить дополнительные коллекции или щелкнуть вне окна управления коллекциями, чтобы вернуться на главный экран.
* Выберите коллекцию, в которую вы хотите добавить всплывающее уведомление, в раскрывающемся меню "Коллекция всплывающего уведомления".
* При запуске всплывающего уведомления оно будет добавлено в соответствующую коллекцию в центре уведомлений.


## <a name="other-details"></a>Дополнительные сведения
Создаваемые вами коллекции всплывающих уведомлений также отражаются в параметрах уведомлений пользователя.  Пользователи могут переключать параметры каждой отдельной коллекции, чтобы включать или отключать эти подгруппы.  При отключении уведомлений на верхнем уровне приложения также отключаются все уведомления коллекций.  Кроме того, каждая коллекция по умолчанию будет отображать 3 уведомления в центре уведомлений, и пользователь сможет развернуть его, чтобы отобразить до 20 уведомлений.

## <a name="related-topics"></a>Смежные разделы

* [Содержимое всплывающего уведомления](adaptive-interactive-toasts.md)
* [Заголовки всплывающих уведомлений](toast-headers.md)
* [Библиотека уведомлений в GitHub (входит в набор средств сообщества Windows)](https://github.com/Microsoft/UWPCommunityToolkit/tree/master/Microsoft.Toolkit.Uwp.Notifications)