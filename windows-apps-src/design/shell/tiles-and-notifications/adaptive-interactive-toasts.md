---
author: andrewleader
Description: Adaptive and interactive toast notifications let you create flexible pop-up notifications with more content, optional inline images, and optional user interaction.
title: Содержимое всплывающего уведомления
ms.assetid: 1FCE66AF-34B4-436A-9FC9-D0CF4BDA5A01
label: Toast content
template: detail.hbs
ms.author: mijacobs
ms.date: 11/20/2017
ms.topic: article
keywords: windows 10, uwp, всплывающие уведомления, интерактивные всплывающие уведомления, адаптивные всплывающие уведомления, содержимое всплывающего уведомления, полезные данных во всплывающем уведомлении
ms.localizationpriority: medium
ms.openlocfilehash: 791b1dcede799de4ecf8480d994a5c0f1ddb58af
ms.sourcegitcommit: 93c0a60cf531c7d9fe7b00e7cf78df86906f9d6e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "7577449"
---
# <a name="toast-content"></a>Содержимое всплывающего уведомления

Создавайте гибкие уведомления с текстом, изображениями и элементами управления с помощью адаптивных и интерактивных всплывающих уведомлений.

> **Важные API-интерфейсы**: [пакет NuGet уведомлений набора средств сообщества UWP](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/)

> [!NOTE]
> Традиционные шаблоны для Windows8.1 и Windows Phone 8.1 см. в разделе [дополнена каталога шаблонов](https://msdn.microsoft.com/library/windows/apps/hh761494).


## <a name="getting-started"></a>Начало работы

**Установите библиотеку уведомлений.** Если вы хотите использовать C# вместо XML для создания уведомлений, установите пакет NuGet с именем [Microsoft.Toolkit.Uwp.Notifications](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) (выполните поиск по критерию "notifications uwp"). В примерах с C# в этой статье используется версия 1.0.0 пакета NuGet.

**Установите Визуализатор уведомлений.** Это бесплатное приложение UWP помогает проектировать интерактивные всплывающие уведомления, мгновенно отображая всплывающее уведомление при изменении, как в представлении редактора или конструирования XAML в Visual Studio. Смотрите в разделе [Визуализатор уведомлений](notifications-visualizer.md) дополнительные сведения или [загрузите Notifications Visualizer из Store](https://www.microsoft.com/store/apps/notifications-visualizer/9nblggh5xsl1).


## <a name="sending-a-toast-notification"></a>Отправка всплывающего уведомления

Чтобы узнать, как отправлять уведомления, в разделе [отправить локального всплывающих](send-local-toast.md). В этой документации относится только к созданию содержимого всплывающего уведомления.


## <a name="toast-notification-structure"></a>Структура всплывающего уведомления

Всплывающие уведомления представляют собой сочетание некоторые свойства данных, например тегов или группа (который позволяют определить уведомления) и *содержимого всплывающих*.

Основные компоненты содержимого всплывающего уведомления — это...
* **Запуск**: это определяет, какие аргументы будут передаваться в ваше приложение, когда пользователь щелкает всплывающего уведомления, позволяя прямая ссылка в правильный содержимое, которое отображение всплывающего уведомления. Дополнительные сведения см. в разделе [отправить локального всплывающих](send-local-toast.md).
* **графические элементы**: визуальная часть всплывающего уведомления, включая общую привязку с текстом и изображениями.
* **действия**: интерактивный часть всплывающего уведомления, в том элементы управления и ввода.
* **аудио**: звук воспроизводится, когда всплывающее уведомление отображается пользователю элементы управления.

В необработанных XML определен содержимого всплывающего уведомления, но вы можете использовать наши [NuGet библиотеки](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) получить объектной модели C# (или C++) для создания содержимого всплывающего уведомления. В этой статье описываются все, что происходит в пределах содержимого всплывающего уведомления.

```csharp
ToastContent content = new ToastContent()
{
    Launch = "app-defined-string",
 
    Visual = new ToastVisual()
    {
        BindingGeneric = new ToastBindingGeneric() { ... }
    },
 
    Actions = new ToastActionsCustom() { ... },
 
    Audio = new ToastAudio() { ... }
};
```

```xml
<toast launch="app-defined-string">

  <visual>
    <binding template="ToastGeneric">
      ...
    </binding>
  </visual>

  <actions>
    ...
  </actions>

  <audio src="ms-winsoundevent:Notification.Reminder"/>

</toast>
```

Вот визуальное представление содержимого всплывающего уведомления:

![структура всплывающего уведомления](images/adaptivetoasts-structure.jpg)


## <a name="visual"></a>Объект класса Visual

Для каждого всплывающего уведомления следует задать графическую часть — указать общую привязку, в которой может быть текст, изображения и другие элементы. Эти элементы будут отображаться на различных устройствах с Windows, включая настольные компьютеры, телефоны, планшеты и Xbox.

Информацию обо всех атрибутах, поддерживаемых в разделе визуального представления, и их дочерних элементах [см. документацию схемы](toast-schema.md#toastvisual).

Удостоверение приложения для всплывающего уведомления передается через значок вашего приложения. Однако если используется переопределение логотипа приложения, имя приложения будет отображаться под строками текста.

| Удостоверение приложения для обычного всплывающего уведомления | Удостоверение приложения с appLogoOverride |
| -- | -- |
| <img src="images/adaptivetoasts-withoutapplogooverride.jpg" alt="notification without appLogoOverride" width="364"/> | <img alt="notification with appLogoOverride" src="images/adaptivetoasts-withapplogooverride.jpg" width="364"/> |


## <a name="text-elements"></a>Текстовые элементы

У всплывающего уведомления должен быть по крайней мере один текстовый элемент, оно может также содержать два дополнительных текстовых элемента с типом [**AdaptiveText**](toast-schema.md#adaptivetext).

<img alt="Toast with title and description" src="images/toast-title-and-description.jpg" width="364"/>

С момента выпуска юбилейного обновления Windows 10 вы можете контролировать число отображаемых строк с помощью свойства **HintMaxLines** текста. Значение по умолчанию (и максимальное) — 2 строки текста для заголовка, и до 4 строк (объединенных) для двух дополнительных элементов описания (второй и третий с типом **AdaptiveText**).

```csharp
new ToastBindingGeneric()
{
    Children =
    {
        new AdaptiveText()
        {
            Text = "Adaptive Tiles Meeting",
            HintMaxLines = 1
        },

        new AdaptiveText()
        {
            Text = "Conf Room 2001 / Building 135"
        },

        new AdaptiveText()
        {
            Text = "10:00 AM - 10:30 AM"
        }
    }
}
```

```xml
<binding template="ToastGeneric">
    <text hint-maxLines="1">Adaptive Tiles Meeting</text>
    <text>Conf Room 2001 / Building 135</text>
    <text>10:00 AM - 10:30 AM</text>
</binding>
```


## <a name="app-logo-override"></a>Переопределение логотипа приложения

По умолчанию во всплывающем уведомлении будет отображаться логотип вашего приложения. Но вы можете показывать другое логотип, указав нужное изображение в [**ToastGenericAppLogo**](toast-schema.md#toastgenericapplogo). Например если это уведомление от какого-либо человека, мы рекомендуем показывать вместо логотипа приложения изображение этого человека.

<img alt="Toast with app logo override" src="images/toast-applogooverride.jpg" width="364"/>

Используйте свойство **HintCrop**, чтобы кадрировать изображение. Например, при значении **Circle** выводится изображение, кадрированное по кругу. В противном случае изображением будет квадратным. Размер изображения — 48x48 пикселей при 100% масштабе.

```csharp
new ToastBindingGeneric()
{
    ...

    AppLogoOverride = new ToastGenericAppLogo()
    {
        Source = "https://picsum.photos/48?image=883",
        HintCrop = ToastGenericAppLogoCrop.Circle
    }
}
```

```xml
<binding template="ToastGeneric">
    ...
    <image placement="appLogoOverride" hint-crop="circle" src="https://picsum.photos/48?image=883"/>
</binding>
```


## <a name="hero-image"></a>Главное изображение

**Новые возможности в юбилейном обновлении**. Во всплывающих уведомлениях может быть главное изображение. Это специальное изображение [**ToastGenericHeroImage**](toast-schema.md#toastgenericheroimage), которое показывается на видном месте в окне уведомления и в центра уведомлений. Размер изображения — 364x180 пикселей при 100% масштабе.

<img alt="Toast with hero image" src="images/toast-heroimage.jpg" width="364"/>

```csharp
new ToastBindingGeneric()
{
    ...

    HeroImage = new ToastGenericHeroImage()
    {
        Source = "https://picsum.photos/364/180?image=1043"
    }
}
```

```xml
<binding template="ToastGeneric">
    ...
    <image placement="hero" src="https://picsum.photos/364/180?image=1043"/>
</binding>
```


## <a name="inline-image"></a>Встроенное изображение

Вы можете предоставить полноразмерное встроенное изображение, которое отображается при развертывании всплывающего уведомления.

<img alt="Toast with additional image" src="images/toast-additionalimage.jpg" width="364"/>

```csharp
new ToastBindingGeneric()
{
    Children =
    {
        ...

        new AdaptiveImage()
        {
            Source = "https://picsum.photos/360/202?image=1043"
        }
    }
}
```

```xml
<binding template="ToastGeneric">
    ...
    <image src="https://picsum.photos/360/202?image=1043" />
</binding>
```


## <a name="image-size-restrictions"></a>Ограничения на размер изображения

Изображения во всплывающем уведомления могут изображений, могут извлекаться из..

 - http://
 - ms-appx:///
 - ms-appdata:///

Для удаленных веб-изображений по адресам http и https есть ограничения на размер файла каждого отдельного изображения. В обновлении Fall Creators Update (16299) мы увеличили ограничение до 3 МБ для обычных подключений и до 1 МБ при лимитном подключении. До этого размер изображений всегда был ограничен до 200 КБ.

| Обычное подключение | Лимитное подключение | До обновления Fall Creators Update |
| - | - | - |
| 3 MБ | 1 MБ | 200 КБ |

Если размер файла изображения превышает ограничение, его не удается загрузить, или истекает время ожидания, то изображение выводиться не будет, но остальная часть уведомления будет показана.


## <a name="attribution-text"></a>Текст атрибута

**Новые возможности Юбилейное обновление**: Если возникает необходимость создать ссылку источник содержимого, вы можете использовать авторство текста. Этот текст всегда отображается в нижней части уведомления, удостоверение приложения или уведомления метку времени.

В старых версиях Windows, которые не поддерживают текст авторство текст просто отображается как другой текстовый элемент (при условии, что у вас еще нет максимально три текстовых элементов).

<img alt="Toast with attribution text" src="images/toast-attributiontext.jpg" width="364"/>

```csharp
new ToastBindingGeneric()
{
    ...

    Attribution = new ToastGenericAttributionText()
    {
        Text = "Via SMS"
    }
}
```

```xml
<binding template="ToastGeneric">
    ...
    <text placement="attribution">Via SMS</text>
</binding>
```


## <a name="custom-timestamp"></a>Настраиваемая отметка времени

**Новые возможности центра обновления создателям**: теперь можно переопределить предоставляется системой и метку времени с помощью собственного метка времени, точно представляет, когда был создан сообщения или сведения и содержимое. Эта метка времени отображается в центре уведомлений.

<img alt="Toast with custom timestamp" src="images/toast-customtimestamp.jpg" width="396"/>

Подробнее про использование настраиваемых меток времени, см. в разделе [пользовательские отметки времени во всплывающих уведомлениях](custom-timestamps-on-toasts.md).

```csharp
ToastContent toastContent = new ToastContent()
{
    DisplayTimestamp = new DateTime(2017, 04, 15, 19, 45, 00, DateTimeKind.Utc),
    ...
};
```

```xml
<toast displayTimestamp="2017-04-15T19:45:00Z">
  ...
</toast>
```


## <a name="progress-bar"></a>Индикатор выполнения

**Новые возможности в обновлении Creators Update**. Вы можете показывать индикатор выполнения во всплывающем уведомлении, чтобы информировать пользователя о ходе выполнения операций, например, при загрузке файлов.

<img alt="Toast with progress bar" src="images/toast-progressbar.png" width="364"/>

Подробнее о применении индикатора выполнения см. на странице [Индикатор выполнения во всплывающем уведомлении](toast-progress-bar.md).


## <a name="headers"></a>Заголовки

**Новые возможности в обновлении Creators Update**. Вы можете группировать уведомления под заголовками для структурирования информации в центре уведомлений. Например, можно озаглавить сообщения из группового чата, сгруппировать уведомления на общую тему и так далее.

<img alt="Toasts with header" src="images/toast-headers-action-center.png" width="396"/>

Подробнее о применении заголовков см. на странице [Заголовки для всплывающих уведомлений](toast-headers.md).


## <a name="adaptive-content"></a>Адаптивное содержимое

**Новые возможности Юбилейное обновление**: в дополнение к содержимому, указанном выше, вы можете отображать дополнительное адаптивных содержимое, которое отображается, если развернута всплывающего уведомления.

Это дополнительное содержимое задается с помощью адаптивных, которой вы можете больше узнать о чтения [документации адаптивных плиток](create-adaptive-tiles.md).

Помните о том, что все адаптивное содержимое следует размещать внутри [**AdaptiveGroup**](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/toast-schema#adaptivegroup). В противном случае оно не будет обработан с помощью адаптивных.


### <a name="columns-and-text-elements"></a>Текстовые элементы и столбцы

Вот пример, где используются столбцов, а также некоторые дополнительные адаптивных текстовых элементов. Так как текстовые элементы находятся в **AdaptiveGroup**, то в них поддерживаются все свойства богатого адаптивного форматирования.

<img alt="Toast with additional text" src="images/toast-additionaltext.jpg" width="364"/>

```csharp
new ToastBindingGeneric()
{
    Children =
    {
        ...

        new AdaptiveGroup()
        {
            Children =
            {
                new AdaptiveSubgroup()
                {
                    Children =
                    {
                        new AdaptiveText()
                        {
                            Text = "52 attendees",
                            HintStyle = AdaptiveTextStyle.Base
                        },
                        new AdaptiveText()
                        {
                            Text = "23 minute drive",
                            HintStyle = AdaptiveTextStyle.CaptionSubtle
                        }
                    }
                },
                new AdaptiveSubgroup()
                {
                    Children =
                    {
                        new AdaptiveText()
                        {
                            Text = "1 Microsoft Way",
                            HintStyle = AdaptiveTextStyle.CaptionSubtle,
                            HintAlign = AdaptiveTextAlign.Right
                        },
                        new AdaptiveText()
                        {
                            Text = "Bellevue, WA 98008",
                            HintStyle = AdaptiveTextStyle.CaptionSubtle,
                            HintAlign = AdaptiveTextAlign.Right
                        }
                    }
                }
            }
        }
    }
}
```

```xml
<binding template="ToastGeneric">
    ...
    <group>
        <subgroup>
            <text hint-style="base">52 attendees</text>
            <text hint-style="captionSubtle">23 minute drive</text>
        </subgroup>
        <subgroup>
            <text hint-style="captionSubtle" hint-align="right">1 Microsoft Way</text>
            <text hint-style="captionSubtle" hint-align="right">Bellevue, WA 98008</text>
        </subgroup>
    </group>
</binding>
```


## <a name="buttons"></a>Кнопки

Кнопки сделать всплывающего уведомления интерактивной, позволяя пользователю выполнять быстрые действия на всплывающее уведомление, не прерывая их текущего рабочего процесса. Пользователи, например, могут ответить на сообщение непосредственно из всплывающего уведомления или удалить письмо, даже не открывая приложение электронной почты. Кнопки отображаются в развернутой части уведомления.

Подробнее о полноценной реализации кнопок см. на странице [Отправка локального всплывающего уведомления](send-local-toast.md).

После нажатия кнопки могут выполняться следующие различные действия...

-   Активация приложения на переднем плане с аргументом, который можно использовать для перехода к указанной странице или контексту.
-   Активация фоновой задачи приложения, для быстрого ответа или других сценариев.
-   Активация другого приложения через запуск протокола.
-   Выполнение системного действия, например, откладывание или закрытие уведомления.

> [!NOTE]
> В уведомлении размещается до пяти кнопок (включая элементы контекстного меню, которое мы рассмотрим ниже).

<img alt="notification with actions, example 1" src="images/adaptivetoasts-xmlsample02.jpg" width="364"/>

```csharp
ToastContent content = new ToastContent()
{
    ...
 
    Actions = new ToastActionsCustom()
    {
        Buttons =
        {
            new ToastButton("See more details", "action=viewdetails&contentId=351")
            {
                ActivationType = ToastActivationType.Foreground
            },

            new ToastButton("Remind me later", "action=remindlater&contentId=351")
            {
                ActivationType = ToastActivationType.Background
            }
        }
    }
};
```

```xml
<toast launch="app-defined-string">

    ...

    <actions>

        <action
            content="See more details"
            arguments="action=viewdetails&amp;contentId=351"
            activationType="foreground"/>

        <action
            content="Remind me later"
            arguments="action=remindlater&amp;contentId=351"
            activationType="background"/>

    </actions>

</toast>
```


### <a name="buttons-with-icons"></a>Кнопки со значками

На кнопки можно добавить значки. Эти значки — белые прозрачные изображения 16x16 пикселей при 100% масштабе, в самом изображении не должно быть внутренних полей. Если вы решили показывать значки на всплывающем уведомлении, то следует указать значки для ВСЕХ кнопок уведомления, так как в таком случае стиль кнопок меняется на «кнопки со значками».

> [!NOTE]
> Для поддержки специальных возможностей обязательно добавьте контрастную белую версию значка (черный силуэт на белом фоне), чтобы пользователи с включенным режимом высокой контрастности могли разглядеть значок. Дополнительные сведения см. на странице [поддержка специальных возможностей на всплывающих уведомлениях](tile-toast-language-scale-contrast.md).

<img src="images\adaptivetoasts-buttonswithicons.png" width="364" alt="Toast that has buttons with icons"/>

```csharp
new ToastButton("Dismiss", "dismiss")
{
    ActivationType = ToastActivationType.Background,
    ImageUri = "Assets/ToastButtonIcons/Dismiss.png"
}
```


```xml
<action
    content="Dismiss"
    imageUri="Assets/ToastButtonIcons/Dismiss.png"
    arguments="dismiss"
    activationType="background"/>
```


### <a name="buttons-with-pending-update-activation"></a>Кнопки, активирующие ожидание обновления

**Новые возможности в обновлении Fall Creators Update**. На фоновых кнопках активации, можно использовать поведение после активации **PendingUpdate** для создания многоэтапных взаимодействий со всплывающими уведомлениями. Когда пользователь нажимает кнопку, активируется фоновая задача и всплывающее уведомление переводится в состояние «ожидается обновление» и остается на экране, пока ваша фоновая задача не заменит его на новое всплывающее уведомление.

Подробнее о реализации такого механизма см. в разделе [Ожидание обновления во всплывающих уведомлениях](toast-pending-update.md).

![Всплывающее уведомление с ожидающим обновлением](images/toast-pendingupdate.gif)


### <a name="context-menu-actions"></a>Действия из контекстного меню

**Новые возможности в юбилейном обновлении**. Вы можете добавить дополнительные действия к существующему контекстному меню, которое появляется, когда пользователь щелкает правой кнопкой мыши всплывающее уведомление в центре уведомлений. Обратите внимание, что это меню появляется только после щелчка правой кнопкой мыши в центре уведомлений. Оно не появляется при щелчке правой кнопкой мыши в окне всплывающего уведомления.

> [!NOTE]
> На старых устройствах дополнительные действия контекстного меню будут отображаться как обычные кнопки на всплывающем уведомлении.

Добавленные дополнительные действия контекстного меню (например, «Сменить местоположение») размещаются поверх двух стандартных системных записей.

<img alt="Toast with context menu" src="images/toast-contextmenu.png" width="444"/>

```csharp
ToastContent content = new ToastContent()
{
    ...
 
    Actions = new ToastActionsCustom()
    {
        ContextMenuItems =
        {
            new ToastContextMenuItem("Change location", "action=changeLocation")
        }
    }
};
```

```xml
<toast>

    ...

    <actions>

        <action
            placement="contextMenu"
            content="Change location"
            arguments="action=changeLocation"/>

    </actions>

</toast>
```

> [!NOTE]
> Дополнительные элементы контекстного меню входят в общий лимит на пять кнопок во всплывающем уведомлении.

Обработка активации дополнительных элементов контекстного меню выполняется точно так же, как в случае с кнопками.


## <a name="inputs"></a>Элементы ввода данных

Элементы ввода указываются в разделе действий области toast всплывающего уведомления, это значит, что они отображаются только в развернутом окне уведомления.


### <a name="quick-reply-text-box"></a>Чтобы быстро ответить текстовое поле

Чтобы быстро ответить текстовое поле, для обмена сообщениями сценарии, такие как, добавьте ввод текста и кнопку и ссылки идентификатор ввода текста, чтобы кнопки отображается рядом с входных данных.

<img alt="notification with text input and actions" src="images/adaptivetoasts-xmlsample05.jpg" width="364"/>

```csharp
ToastContent content = new ToastContent()
{
    ...
 
    Actions = new ToastActionsCustom()
    {
        Inputs =
        {
            new ToastTextBox("tbReply")
            {
                PlaceholderContent = "Type a reply"
            }
        },

        Buttons =
        {
            new ToastButton("Reply", "action=reply&convId=9318")
            {
                ActivationType = ToastActivationType.Background,

                // To place the button next to the text box,
                // reference the text box's Id and provide an image
                TextBoxId = "tbReply",
                ImageUri = "Assets/Reply.png"
            }
        }
    }
};
```

```xml
<toast launch="app-defined-string">

    ...

    <actions>

        <input id="textBox" type="text" placeHolderContent="Type a reply"/>

        <action
            content="Send"
            arguments="action=reply&amp;convId=9318"
            activationType="background"
            hint-inputId="textBox"
            imageUri="Assets/Reply.png"/>

    </actions>

</toast>
```


### <a name="inputs-with-buttons-bar"></a>Панель элементов ввода с кнопками

Вы можете сделать один (или несколько) элементов ввода с обычными кнопками, которые отображаются ниже элементов ввода.

<img alt="notification with text and input actions" src="images/adaptivetoasts-xmlsample04.jpg" width="364"/>

```csharp
ToastContent content = new ToastContent()
{
    ...
 
    Actions = new ToastActionsCustom()
    {
        Inputs =
        {
            new ToastTextBox("tbReply")
            {
                PlaceholderContent = "Type a reply"
            }
        },

        Buttons =
        {
            new ToastButton("Reply", "action=reply&threadId=9218")
            {
                ActivationType = ToastActivationType.Background
            },

            new ToastButton("Video call", "action=videocall&threadId=9218")
            {
                ActivationType = ToastActivationType.Foreground
            }
        }
    }
};
```

```xml
<toast launch="app-defined-string">

    ...

    <actions>

        <input id="textBox" type="text" placeHolderContent="Type a reply"/>

        <action
            content="Reply"
            arguments="action=reply&amp;threadId=9218"
            activationType="background"/>

        <action
            content="Video call"
            arguments="action=videocall&amp;threadId=9218"
            activationType="foreground"/>

    </actions>

</toast>
```


### <a name="selection-input"></a>Меню выбора

Помимо текстовых полей также можно использовать меню выбора.

<img alt="notification with selection input and actions" src="images/adaptivetoasts-xmlsample06.jpg" width="364"/>

```csharp
ToastContent content = new ToastContent()
{
    ...
 
    Actions = new ToastActionsCustom()
    {
        Inputs =
        {
            new ToastSelectionBox("time")
            {
                DefaultSelectionBoxItemId = "lunch",
                Items =
                {
                    new ToastSelectionBoxItem("breakfast", "Breakfast"),
                    new ToastSelectionBoxItem("lunch", "Lunch"),
                    new ToastSelectionBoxItem("dinner", "Dinner")
                }
            }
        },

        Buttons = { ... }
};
```

```xml
<toast launch="app-defined-string">

    ...

    <actions>

        <input id="time" type="selection" defaultInput="lunch">
            <selection id="breakfast" content="Breakfast" />
            <selection id="lunch" content="Lunch" />
            <selection id="dinner" content="Dinner" />
        </input>

        ...

    </actions>

</toast>
```


### <a name="snoozedismiss"></a>Отложить/закрыть

С помощью меню выбора и двух кнопок, можно создать напоминание с системными действиями для откладывания или закрытия уведомления. Убедитесь, что выбрали напоминание в качестве сценария для уведомления, чтобы оно действовало соответствующим образом.

<img alt="reminder notification" src="images/adaptivetoasts-xmlsample07.jpg" width="364"/>

Мы привяжем кнопку «Отложить» к действию меню выбора с помощью свойства **SelectionBoxId** на кнопке всплывающего уведомления.

```csharp
ToastContent content = new ToastContent()
{
    Scenario = ToastScenario.Reminder,

    ...
 
    Actions = new ToastActionsCustom()
    {
        Inputs =
        {
            new ToastSelectionBox("snoozeTime")
            {
                DefaultSelectionBoxItemId = "15",
                Items =
                {
                    new ToastSelectionBoxItem("5", "5 minutes"),
                    new ToastSelectionBoxItem("15", "15 minutes"),
                    new ToastSelectionBoxItem("60", "1 hour"),
                    new ToastSelectionBoxItem("240", "4 hours"),
                    new ToastSelectionBoxItem("1440", "1 day")
                }
            }
        },

        Buttons =
        {
            new ToastButtonSnooze()
            {
                SelectionBoxId = "snoozeTime"
            },
 
            new ToastButtonDismiss()
        }
    }
};
```

```xml
<toast scenario="reminder" launch="action=viewEvent&amp;eventId=1983">
   
  ...
 
  <actions>
     
    <input id="snoozeTime" type="selection" defaultInput="15">
      <selection id="1" content="1 minute"/>
      <selection id="15" content="15 minutes"/>
      <selection id="60" content="1 hour"/>
      <selection id="240" content="4 hours"/>
      <selection id="1440" content="1 day"/>
    </input>
 
    <action activationType="system" arguments="snooze" hint-inputId="snoozeTime" content="" />
 
    <action activationType="system" arguments="dismiss" content=""/>
     
  </actions>
   
</toast>
```

Чтобы использовать системные действия для откладывания и закрытия уведомления:

-   Укажите **ToastButtonSnooze** или **ToastButtonDismiss**
-   При необходимости задайте строку пользовательского содержимого:
    -   Если не указать строку, мы используем автоматически локализованные строки «Snooze» и «Закрыть».
-   При необходимости задать **SelectionBoxId**:
    -   Если вы не хотите, чтобы пользователь выбирал интервал откладывания, а просто хотите, чтобы уведомление было отложено только один раз на определенный системой период (одинаковый во всей операционной системе), не создавайте &lt;input&gt; совсем.
    -   Если вы хотите предоставить выбор интервала откладывания, выполните следующие действия.
        -   Укажите **SelectionBoxId** в действии откладывания
        -   Сопоставьте идентификатор ввода с **SelectionBoxId** действия откладывания
        -   Укажите **ToastSelectionBoxItem**от значения для nonNegativeInteger, которое представляет snooze интервал в минутах.



## <a name="audio"></a>Звук

Пользовательские звуковые сигналы всегда поддерживались в мобильной версии и поддерживаются в настольной версии 1511 (сборка 10586) и более поздних. На пользовательские звуковые сигналы можно ссылаться через следующие пути:

-   ms-appx:///
-   ms-appdata:///

Кроме того, можно выбирать звуковые сигналы из [списка ms-winsoundevents](/uwp/schemas/tiles/toastschema/element-audio#attributes-and-elements), который всегда поддерживался на обеих платформах.

```csharp
ToastContent content = new ToastContent()
{
    ...

    Audio = new ToastAudio()
    {
        Src = new Uri("ms-appx:///Assets/NewMessage.mp3")
    }
}
```

```xml
<toast launch="app-defined-string">

    ...

    <audio src="ms-appx:///Assets/NewMessage.mp3"/>

</toast>
```

Дополнительные сведения о работе со звуками во всплывающих уведомлениях см. на странице [звуковая схема](/uwp/schemas/tiles/toastschema/element-audio). Подробнее про отправку всплывающего уведомления с нестандартными звуками см. в разделе [пользовательский звук во всплывающих уведомлениях](custom-audio-on-toasts.md).


## <a name="alarms-reminders-and-incoming-calls"></a>Будильники, напоминания и входящие вызовы

Для создания будильники, напоминания и уведомления о входящих вызовах, вы просто используете со значением сценарий, присвоенный ей в обычный всплывающее уведомление. Сценарий меняет некоторую функциональность, чтобы обеспечить согласованную и унифицированную работу элемента графического интерфейса.

> [!IMPORTANT]
> При использовании сценария «Напоминание» или «Будильник» для всплывающего уведомления требуется задать по крайней мере одну кнопку. В противном случае это всплывающее уведомление будет рассматриваться как обычное всплывающее уведомление.

* **Напоминание**. Всплывающее уведомление с напоминанием остается на экране, пока пользователь его не закроет или не выполнит действие. В Windows Mobile всплывающие уведомления также отображаются сразу в развернутом виде. Звук будет воспроизводиться.
* **Будильник**: помимо поведения напоминания, оповещения Кроме того цикл звук с помощью звуковой сигнал по умолчанию.
* **IncomingCall**: уведомления о входящих вызовах на устройствах Windows Mobile открываются во весь экран. В противном случае — они ведут себя как будильники, за исключением того, что используют аудио мелодии звонка и их кнопки выглядят по-другому.

```csharp
ToastContent content = new ToastContent()
{
    Scenario = ToastScenario.Reminder,

    ...
}
```

```xml
<toast scenario="reminder" launch="app-defined-string">

    ...

</toast>
```


## <a name="localization-and-accessibility"></a>Локализация и специальные возможности

Вы можете загружать в ваши плитки и всплывающие уведомления строки и изображения, соответствующие языку интерфейса, коэффициенту экранного увеличения, режиму контрастности и другим факторам времени исполнения. Дополнительные сведения см. в разделе [Поддержка различных языков, масштабирования и высокой контрастности в плитках и всплывающих уведомлениях](tile-toast-language-scale-contrast.md).


## <a name="handling-activation"></a>Обработка активации
Чтобы узнать, как для обработки активации всплывающих уведомлений (нажатия всплывающего уведомления или кнопок на всплывающего уведомления), в разделе [отправить локального всплывающих](send-local-toast.md).
 
## <a name="related-topics"></a>Еще по теме:

* [Отправка локального всплывающего уведомления и обработка активации](send-local-toast.md)
* [Библиотека уведомлений в GitHub (входит в UWP Community Toolkit)](https://github.com/Microsoft/UWPCommunityToolkit/tree/master/Microsoft.Toolkit.Uwp.Notifications)
* [Поддержка различных языков, масштабирования и высокой контрастности в плитках и всплывающих уведомлениях](tile-toast-language-scale-contrast.md)
