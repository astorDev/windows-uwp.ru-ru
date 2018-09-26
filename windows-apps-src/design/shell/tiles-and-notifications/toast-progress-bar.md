---
author: andrewleader
Description: Learn how to use a progress bar within your toast notification.
title: Индикатор выполнения всплывающего уведомления и привязка данных
label: Toast progress bar and data binding
template: detail.hbs
ms.author: mijacobs
ms.date: 12/7/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, всплывающее уведомление, индикатор выполнения, индикатор выполнения всплывающего уведомления, привязка данных всплывающего уведомления
ms.localizationpriority: medium
ms.openlocfilehash: b99c2479bef3c10ecc82707e475f49fd2b9014ec
ms.sourcegitcommit: 232543fba1fb30bb1489b053310ed6bd4b8f15d5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2018
ms.locfileid: "4179287"
---
# <a name="toast-progress-bar-and-data-binding"></a>Индикатор выполнения всплывающего уведомления и привязка данных

Индикатор хода выполнения внутри всплывающего уведомления позволяет указать пользователю состояние продолжительных операций, таких как скачивание, обработка видео, цели упражнения и многое другое.

> [!IMPORTANT]
> **Требует обновления Creators Update и библиотека уведомлений версии 1.4.0**: вам необходимо выбрать целевой пакет SDK 15063 и использовать сборку 15063 или более поздней версии для применения индикаторов выполнения во всплывающих уведомлениях. Для создания индикатора выполнения в содержимом всплывающего уведомления необходимо использовать [библиотеку уведомлений набора средств сообщества UWP из NuGet](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) версии 1.4.0 или более поздней.

Индикатор хода выполнения внутри всплывающего уведомления может быть "не определенным"» (нет конкретного значения, анимированные точки, обозначающие выполнение операции) или "определенным" (определенная доля заполнения индикатора, например 60%).

> **Важные API-интерфейсы**: [класс NotificationData](https://docs.microsoft.com/uwp/api/windows.ui.notifications.notificationdata), [метод ToastNotifier.Update](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotifier.Update), [класс ToastNotification](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotification)

> [!NOTE]
> Только настольные компьютеры поддерживают индикаторы выполнения во всплывающих уведомлениях. На других устройствах индикатор выполнения удаляется из уведомления.

На рисунке ниже показано определенный индикатор хода выполнения со всеми свойствами.

<img alt="Toast with progress bar properties labeled" src="images/toast-progressbar-annotated.png" width="626"/>

| Свойство | Тип | Обязательный | Описание |
|---|---|---|---|
| **Title** | string или [BindableString](toast-schema.md#bindablestring) | false | Получает или задает необязательную строку заголовка. Поддерживает привязку данных. |
| **Value** | double, [AdaptiveProgressBarValue](toast-schema.md#adaptiveprogressbarvalue) или [BindableProgressBarValue](toast-schema.md#bindableprogressbarvalue) | false | Получает или задает значение индикатора хода выполнения. Поддерживает привязку данных. По умолчанию — 0. Значение double в диапазоне от 0,0 до 1,0, `AdaptiveProgressBarValue.Indeterminate` или `new BindableProgressBarValue("myProgressValue")`. |
| **ValueStringOverride** | string или [BindableString](toast-schema.md#bindablestring) | false | Получает или задает необязательную строку для отображения вместо строки процентов по умолчанию. Если строка не указана, отображается текст, например "70%". |
| **Status** | string или [BindableString](toast-schema.md#bindablestring) | true | Получает или задает строку состояния (обязательную), которая отображается под индикатором хода выполнения слева. Эта строка должна отражать состояние операции, например, "Загрузка..." или "Установка...". |


Вот,как создается уведомление, представленное выше...

```csharp
ToastContent content = new ToastContent()
{
    Visual = new ToastVisual()
    {
        BindingGeneric = new ToastBindingGeneric()
        {
            Children =
            {
                new AdaptiveText()
                {
                    Text = "Downloading your weekly playlist..."
                },
 
                new AdaptiveProgressBar()
                {
                    Title = "Weekly playlist",
                    Value = 0.6,
                    ValueStringOverride = "15/26 songs",
                    Status = "Downloading..."
                }
            }
        }
    }
};
```

```xml
<toast>
    <visual>
        <binding template="ToastGeneric">
            <text>Downloading your weekly playlist...</text>
            <progress
                title="Weekly playlist"
                value="0.6"
                valueStringOverride="15/26 songs"
                status="Downloading..."/>
        </binding>
    </visual>
</toast>
```

Однако вам потребуется динамически обновлять значения индикатора хода выполнения, чтобы он был "динамическим". Для этого можно использовать привязку данных для обновления всплывающего уведомления.


## <a name="using-data-binding-to-update-a-toast"></a>Использование привязки данных для обновления всплывающего уведомления

Использование привязки данных состоит из следующих действий...

1. Создайте содержимое всплывающего уведомления, которое использует поля с привязкой к данным
2. Назначьте **Tag** (и при необходимости **Group**) объекту **ToastNotification**
3. Определите начальные значения **Data** в **ToastNotification**
4. Отправьте всплывающее уведомление
5. Используйте **Tag** b **Group** для обновления значений **Data**

В следующем фрагменте кода показаны шаги 1–4. В следующем фрагменте показано, как обновить значения **Data** всплывающего уведомления.

```csharp
using Windows.UI.Notifications;
using Microsoft.Toolkit.Uwp.Notifications;
 
public void SendUpdatableToastWithProgress()
{
    // Define a tag (and optionally a group) to uniquely identify the notification, in order update the notification data later;
    string tag = "weekly-playlist";
    string group = "downloads";
 
    // Construct the toast content with data bound fields
    var content = new ToastContent()
    {
        Visual = new ToastVisual()
        {
            BindingGeneric = new ToastBindingGeneric()
            {
                Children =
                {
                    new AdaptiveText()
                    {
                        Text = "Downloading your weekly playlist..."
                    },
    
                    new AdaptiveProgressBar()
                    {
                        Title = "Weekly playlist",
                        Value = new BindableProgressBarValue("progressValue"),
                        ValueStringOverride = new BindableString("progressValueString"),
                        Status = new BindableString("progressStatus")
                    }
                }
            }
        }
    };
 
    // Generate the toast notification
    var toast = new ToastNotification(content.GetXml());
 
    // Assign the tag and group
    toast.Tag = tag;
    toast.Group = group;
 
    // Assign initial NotificationData values
    // Values must be of type string
    toast.Data = new NotificationData();
    toast.Data.Values["progressValue"] = "0.6";
    toast.Data.Values["progressValueString"] = "15/26 songs";
    toast.Data.Values["progressStatus"] = "Downloading...";
 
    // Provide sequence number to prevent out-of-order updates, or assign 0 to indicate "always update"
    toast.Data.SequenceNumber = 1;
 
    // Show the toast notification to the user
    ToastNotificationManager.CreateToastNotifier().Show(toast);
}
```

Затем, если вы хотите изменить значения **Data**, используйте метод [**Update**](https://docs.microsoft.com/uwp/api/Windows.UI.Notifications.ToastNotifier.Update), чтобы предоставить новые данные без повторного создания всех полезных данных всплывающего уведомления.

```csharp
using Windows.UI.Notifications;
 
public void UpdateProgress()
{
    // Construct a NotificationData object;
    string tag = "weekly-playlist";
    string group = "downloads";
 
    // Create NotificationData and make sure the sequence number is incremented
    // since last update, or assign 0 for updating regardless of order
    var data = new NotificationData
    {
        SequenceNumber = 2
    };

    // Assign new values
    // Note that you only need to assign values that changed. In this example
    // we don't assign progressStatus since we don't need to change it
    data.Values["progressValue"] = "0.7";
    data.Values["progressValueString"] = "18/26 songs";

    // Update the existing notification's data by using tag/group
    ToastNotificationManager.CreateToastNotifier().Update(data, tag, group);
}
```

Если вы используете метод **Update**, а не заменяете всплывающее уведомление целиком, уведомление остается в той же позиции в центре уведомлений и не перемещается вверх или вниз. Пользователи были бы очень удивлены, если бы всплывающее уведомление каждый раз оказывалось в верхней части центра уведомлений, с заполненным индикатором хода выполнения.

Метод **Update** возвращает перечисление [**NotificationUpdateResult**](https://docs.microsoft.com/uwp/api/windows.ui.notifications.notificationupdateresult), которое позволяет узнать, выполнено ли обновление успешно или уведомление не удалось найти (это означает, что пользователь, вероятно, удалил уведомление и вам следует остановить отправку обновлений). Не рекомендуется показывать другое всплывающее уведомление до завершения текущий операции (например, завершения загрузки).


## <a name="elements-that-support-data-binding"></a>Элементы, которые поддерживают привязку данных
Следующие элементы во всплывающих уведомлениях поддерживают привязку данных

- Все свойства **AdaptiveProgress**
- Свойство **Текст** элементов **AdaptiveText** верхнего уровня


## <a name="update-or-replace-a-notification"></a>Обновление или замена уведомления

Начиная с Windows 10 вы всегда можете **заменить** уведомление, отправив новое всплывающее уведомление с теми же значениями **Tag** и **Group**. Так в чем же разница между **заменой** всплывающего уведомления и **обновлением** данных уведомления?

| | Замена | Обновление |
| -- | -- | --
| **Позиция в центре уведомлений** | Перемещение уведомления в верхнюю часть центра уведомлений. | Уведомления остается на прежнем месте в центре уведомлений. |
| **Изменение содержимого** | Можно полностью изменить все содержимое и макет всплывающего уведомления | Можно изменить только свойства, которые поддерживают привязку данных (индикатор хода выполнения и текст верхнего уровня) |
| **Повторное отображение в качестве всплывающего меню** | Может снова отображаться как всплывающее окно, если оставить свойству **SuppressPopup** задано значение `false` (или значение true, чтобы автоматически отправить его в центр уведомлений) | Не отображается как всплывающее окно; данные всплывающего уведомления автоматически обновляются в центре уведомлений |
| **Закрыто пользователем** | Независимо от того, закрыл ли пользователь предыдущее уведомление, замена всплывающего уведомления отправляется всегда | Если пользователь закрыл всплывающее уведомление, оно не будет обновлено |

Как правило, **обновление полезно в следующих ситуациях...**

- Сведения, которые часто изменения за короткий период времени и не должны отображаться на переднем плане
- Незначительные изменения содержимого всплывающего уведомления, например изменение 50% на 65%

Во многих случаях после завершения последовательности обновлений (например, после скачивания файла) мы рекомендуем использовать конечный шаг, так как...

- Последнее уведомление, скорее всего, содержит значительные изменения макета, такие как удаление индикатора хода выполнения, добавление новых кнопок и т. д
- Пользователи могли закрыть ожидающие уведомление о ходе выполнения, так как им не интересны сведения о загрузке, но они по-прежнему хотят получить всплывающее уведомление после завершения операции


## <a name="related-topics"></a>Статьи по теме

- [Полный пример кода на GitHub](https://github.com/WindowsNotifications/quickstart-toast-progress-bar)
- [Документация по содержимому всплывающего уведомления](adaptive-interactive-toasts.md)