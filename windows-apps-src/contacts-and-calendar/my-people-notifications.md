---
title: Уведомления близких людей
description: В этой статье описывается, как создавать и использовать уведомления близких людей — новый вид всплывающих уведомлений.
ms.date: 10/25/2017
ms.topic: article
keywords: Windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1c106df0efc7952895f882ec5c05cc1af52bcfac
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75683502"
---
# <a name="my-people-notifications"></a>Уведомления близких людей

Уведомления близких людей предоставляют новый способ общаться с близкими людьми простыми и выразительными жестами. В этой статье описывается процесс разработки и внедрения уведомлений близких людей в ваше приложение. Полные примеры реализации см. в разделе [Пример уведомлений близких людей.](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/MyPeopleNotifications)

![уведомление со смайликом в виде сердца](images/heart-emoji-notification-small.gif)

## <a name="requirements"></a>Требования

+ Windows 10 и Microsoft Visual Studio 2019. Сведения об установке см. в разделе [Настройка Visual Studio](https://docs.microsoft.com/windows/uwp/get-started/get-set-up).
+ Знание основ C# или схожих объектно-ориентированных языков программирования. Сведения о начале работы с C# см. в разделе [Создание приложения "Привет, мир"](https://docs.microsoft.com/windows/uwp/get-started/create-a-hello-world-app-xaml-universal).

## <a name="how-it-works"></a>Как это работает

В качестве альтернативы универсальным всплывающим уведомлениям можно отправлять уведомления с помощью функции "Близкие люди" для создания персонализированных процессов взаимодействия с пользователями. Это новый вид всплывающих уведомлений, которые отправляются от контакта, закрепленного на панели задач пользователя с функцией "Близкие люди". При получении уведомления запускается анимация аватара отправителя на панели задач и воспроизводится звук, что сообщает о запуске уведомления. Анимация или изображение, указанные в полезных данных, отображаются 5 секунд (если это анимация длительностью менее 5 секунд, она будет зациклена для увеличения длительности до 5 секунд).

## <a name="supported-image-types"></a>Поддерживаемые типы изображений

+ GIF
+ Статическое изображение (JPEG, PNG)
+ Spritesheet (только по вертикали)

> [!NOTE]
> Spritesheet — это анимация, полученная на основе статического изображения (JPEG или PNG). Отдельные кадры упорядочиваются по вертикали, чтобы первый из них находится сверху (при этом в полезных данных всплывающего уведомления можно указать другой начальный кадр). Все кадры должны быть одной высоты, при этом программа отображает их по порядку, чтобы создать анимацию (представьте книжку с рисунками, в которой страницы расположены вертикально). Ниже представлен пример Spritesheet.

![Spritesheet с радугой](images/shoulder-tap-rainbow-spritesheet.png)

## <a name="notification-parameters"></a>Параметры уведомлений
Уведомления "Близкие люди" используют платформу [всплывающих уведомлений](../design/shell/tiles-and-notifications/adaptive-interactive-toasts.md), но требуют дополнительного узла привязки в полезных данных всплывающего уведомления. Вторая привязка должна содержать следующий параметр:

```xml
experienceType="shoulderTap"
```

Это указывает, что всплывающее уведомление следует рассматривать как уведомление функции "Близкие люди".

Узел изображения внутри привязки должен содержать следующие параметры:

+ **src**
    + URI ресурса. Это может веб-URI протокола HTTP и HTTPS, URI msappx или путь к локальному файлу.
+ **спритешит-src**
    + URI ресурса. Это может веб-URI протокола HTTP и HTTPS, URI msappx или путь к локальному файлу. Требуются только для анимаций Spritesheet.
+ **спритешит-высота**
    + Высота кадра в пикселях. Требуются только для анимаций Spritesheet.
+ **спритешит — кадров/с**
    + Кадров в секунду (FPS). Требуются только для анимаций Spritesheet. Поддерживаются только значения от 1 до 120.
+ **спритешит — Стартингфраме**
    + Номер начального кадра анимации. Используется только для анимаций Spritesheet. Если параметр отсутствует, используется значение по умолчанию (0).
+ **alt**
    + Текстовая строка, используемая для экранного диктора.

> [!NOTE]
> При создании анимированного уведомления по-прежнему следует указывать статическое изображение в параметре "src". Оно будет использоваться как резервное, если произойдет сбой отображения анимации.

Кроме того, узел всплывающего уведомления верхнего уровня должен содержать параметр **hint-people** для указания контакта-отправителя. Этот параметр может принимать значения:

+ **Адрес электронной почты** 
    + Например: ` mailto:johndoe@mydomain.com `
+ **Номер телефона** 
    + Например: tel:888-888-8888
+ **Удаленный идентификатор** 
    + Например: remoteid:1234

> [!NOTE]
> Если ваше приложение использует [API ContactStore](https://docs.microsoft.com/uwp/api/windows.applicationmodel.contacts.contactstore) и свойство [StoredContact.RemoteId](https://docs.microsoft.com/uwp/api/Windows.Phone.PersonalInformation.StoredContact.RemoteId) для связи контрактов, сохраненных на компьютере, с контрактами, сохраненными удаленно, важно, чтобы значение свойства RemoteId было и стабильным, и уникальным. Это значит, что удаленный идентификатор должен неизменно обозначать одну учетную запись пользователя и содержать уникальный тег, чтобы гарантировать отсутствие конфликтов с удаленными идентификаторами других контактов на компьютере, включая контакты, которыми владеют другие приложения.
> Если уникальность и стабильность удаленных идентификаторов, используемых вашим приложением, нельзя гарантировать, то можно использовать класс [RemoteIdHelper](https://docs.microsoft.com/previous-versions/windows/apps/jj207024(v=vs.105)#BKMK_UsingtheRemoteIdHelperclass), показанный ниже в этом разделе, чтобы добавить ко всем вашим удаленным идентификаторам уникальный тег перед добавлением их к системе. Или можно вовсе отказаться от использования свойства RemoteId и создать собственное расширенное свойство, в котором будут храниться удаленные идентификаторы для ваших контактов.

Помимо второй привязки и полезных данных необходимо включить другие полезные данные в первую привязку для резервного всплывающего уведомления. Оно будет использоваться, если потребуется вернуться к обычному уведомлению (описание приведено в [конце данной статьи](/windows/uwp/contacts-and-calendar/my-people-notifications#falling-back-to-toast)).

## <a name="creating-the-notification"></a>Создание уведомления
Вы можете создать шаблон уведомления "Близкие люди" так же, как для [всплывающего уведомления](../design/shell/tiles-and-notifications/adaptive-interactive-toasts.md).

Вот пример создания уведомления "Близкие люди" с помощью полезных данных статического изображения:

```xml
<toast hint-people="mailto:johndoe@mydomain.com">
    <visual lang="en-US">
        <binding template="ToastGeneric">
            <text hint-style="body">Toast fallback</text>
            <text>Add your fallback toast content here</text>
        </binding>
        <binding template="ToastGeneric" experienceType="shoulderTap">
            <image src="https://docs.microsoft.com/windows/uwp/contacts-and-calendar/images/shoulder-tap-static-payload.png"/>
        </binding>
    </visual>
</toast>
```

При запуске уведомления оно должно выглядеть следующим образом:

![уведомление со статическим изображением](images/static-image-notification-small.gif)

Вот пример создания уведомления "Близкие люди" с помощью полезных данных анимации Spritesheet. Высота кадра Spritesheet составляет 80 пикселей, частота анимации — 25 кадров в секунду. Сделаем 15 кадр начальным и предоставим резервное статическое изображение в параметре "src". Резервное изображение используется, если анимация Spritesheet не отображается.

```xml
<toast hint-people="mailto:johndoe@mydomain.com">
    <visual lang="en-US">
        <binding template="ToastGeneric">
            <text hint-style="body">Toast fallback</text>
            <text>Add your fallback toast content here</text>
        </binding>
        <binding template="ToastGeneric" experienceType="shoulderTap">
            <image src="https://docs.microsoft.com/windows/uwp/contacts-and-calendar/images/shoulder-tap-pizza-static.png"
                spritesheet-src="https://docs.microsoft.com/windows/uwp/contacts-and-calendar/images/shoulder-tap-pizza-spritesheet.png"
                spritesheet-height='80' spritesheet-fps='25' spritesheet-startingFrame='15'/>
        </binding>
    </visual>
</toast>
```

При запуске уведомления оно должно выглядеть следующим образом:

![уведомление Spritesheet](images/pizza-notification-small.gif)

## <a name="starting-the-notification"></a>Запуск уведомления
Для запуска уведомления "Близкие люди" необходимо преобразовать шаблон всплывающего уведомления в объект [XmlDocument](https://docs.microsoft.com/uwp/api/windows.data.xml.dom.xmldocument). Когда вы определили всплывающее уведомление в XML-файле (здесь это content.xml), используйте следующий код на C#, чтобы запустить его:

```CSharp
string xmlText = File.ReadAllText("content.xml");
XmlDocument xmlContent = new XmlDocument();
xmlContent.LoadXml(xmlText);
```

Затем можно использовать этот код для создания и отправки всплывающего уведомления:

```CSharp
ToastNotification notification = new ToastNotification(xmlContent);
ToastNotificationManager.CreateToastNotifier().Show(notification);
```

## <a name="falling-back-to-toast"></a>Возврат к всплывающему уведомлению
В некоторых случаях уведомление функции "Близкие люди" отображается как обычное всплывающее уведомление. Уведомление функции "Близкие люди" заменяется на обычное всплывающее уведомление в следующих случаях:

+ не удается отобразить уведомление;
+ уведомления функции "Близкие люди" не включены получателем;
+ контакт отправителя не закреплен на панели задач получателя.

Если уведомление функции "Близкие люди" возвращается к всплывающему уведомлению, вторая привязка игнорируется, а для отображения всплывающего уведомления используется только первая привязка. Именно поэтому крайне важно предоставить резервные полезные данные в первой привязке всплывающего уведомления.

## <a name="see-also"></a>См. также статью
+ [Пример уведомлений "Мои люди"](https://github.com/Microsoft/Windows-universal-samples/tree/dev/Samples/MyPeopleNotifications)
+ [Добавление поддержки пользователей](my-people-support.md)
+ [Адаптивные всплывающие уведомления](../design/shell/tiles-and-notifications/adaptive-interactive-toasts.md)
+ [Класс Тоастнотификатион](https://docs.microsoft.com/uwp/api/windows.ui.notifications.toastnotification)
