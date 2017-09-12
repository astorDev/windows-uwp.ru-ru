---
title: "Уведомления близких людей"
description: "В этой статье описывается, как создавать и использовать уведомления близких людей— новый вид всплывающих уведомлений."
author: mukin
ms.author: mukin
ms.date: 06/28/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
ms.openlocfilehash: b1fbba8b8cea3edd51dc9b60cae1ea3853f39dd1
ms.sourcegitcommit: ec18e10f750f3f59fbca2f6a41bf1892072c3692
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2017
---
# <a name="my-people-notifications"></a>Уведомления близких людей

> [!IMPORTANT]
> **ПРЕДВАРИТЕЛЬНАЯ ВЕРСИЯ | Требуется осеннее обновление Creators Update**: для поддержки API-интерфейсов "Близкие люди" необходимо использовать [пакет SDK для участников программы предварительной оценки версии 16225](https://www.microsoft.com/en-us/software-download/windowsinsiderpreviewSDK) и [сборку для участников программы предварительной оценки 16226](https://blogs.windows.com/windowsexperience/2017/06/21/announcing-windows-10-insider-preview-build-16226-pc/) или более поздней версии.

Уведомления близких людей— новый вид жеста, который в первую очередь ориентирован на пользователей. Они предоставляют новый способ общаться с близкими людьми простыми и выразительными жестами.

![уведомление со смайликом в виде сердца](images/heart-emoji-notification-small.gif)

## <a name="requirements"></a>Требования

+ Windows10 и Microsoft Visual Studio2017. Сведения об установке см. в разделе [Настройка Visual Studio](https://docs.microsoft.com/en-us/windows/uwp/get-started/get-set-up).
+ Знание основ C# или похожих объектно-ориентированных языков программирования. Сведения о начале работы с C# см. в разделе [Создание приложения "Привет, мир"](https://docs.microsoft.com/en-us/windows/uwp/get-started/create-a-hello-world-app-xaml-universal).

## <a name="how-it-works"></a>Принцип работы

В качестве альтернативы универсальным всплывающим уведомлениям разработчики приложений могут отправлять уведомления с помощью функции "Близкие люди" для создания персонализированных процессов взаимодействия с пользователями. Это новый вид всплывающих уведомлений, которые отправляются от контакта, закрепленного на панели задач пользователя. При получении уведомления запускается анимация аватара отправителя на панели задач и воспроизводится звук, что сообщает о запуске уведомления "Близкие люди". Затем анимация или изображение, указанные в полезных данных, отображаются 5секунд (если это анимация длительностью менее 5секунд, она будет зациклена для увеличения длительности до 5секунд).

## <a name="supported-image-types"></a>Поддерживаемые типы изображений

+ GIF
+ Статическое изображение (JPEG, PNG)
+ Spritesheet (только по вертикали)

> [!NOTE]
> Spritesheet— это анимация, полученная на основе статического изображения (JPEG или PNG). Отдельные кадры упорядочиваются по вертикали, чтобы первый из них находится сверху (другой начальный кадр можно указать в полезных данных всплывающего уведомления). Все кадры должны быть одной высоты, при этом программа отображает их по порядку, чтобы создать анимацию (представьте книжку с рисунками, в которой страницы расположены вертикально). Ниже представлен пример Spritesheet.

![Spritesheet с радугой](images/shoulder-tap-rainbow-spritesheet.png)

## <a name="notification-parameters"></a>Параметры уведомлений
Уведомления "Близкие люди" используют платформу [всплывающих уведомлений](../controls-and-patterns/tiles-and-notifications-adaptive-interactive-toasts.md) Windows 10 и требуют дополнительного узла привязки в полезных данных всплывающего уведомления. Это означает, что уведомления функции "Близкие люди" должны содержать две привязки, а не одну. Вторая привязка должна содержать следующий параметр:

```xml
experienceType=”shoulderTap”
```

Это указывает, что всплывающее уведомление следует рассматривать как уведомление функции "Близкие люди".

Узел изображения внутри привязки должен содержать следующие параметры:

+ **src**
    + URI ресурса. Это может веб-URI протокола HTTP и HTTPS, URI msappx или путь к локальному файлу.
+ **spritesheet-src**
    + URI ресурса. Это может веб-URI протокола HTTP и HTTPS, URI msappx или путь к локальному файлу. Требуются только для анимаций Spritesheet.
+ **spritesheet-height**
    + Высота кадра в пикселях. Требуются только для анимаций Spritesheet.
+ **spritesheet-fps**
    + Кадров в секунду. Требуются только для анимаций Spritesheet.
+ **spritesheet-startingFrame**
    + Номер начального кадра анимации. Используется только для анимаций Spritesheet. Если параметр отсутствует, используется значение по умолчанию (0).
+ **alt**
    + Текстовая строка, используемая для экранного диктора.

> [!NOTE]
> Даже если вы используете Spritesheet, вам по-прежнему следует указать статическое изображение в параметре "src" на случай, если анимация не будет отображаться.

Кроме того, узел всплывающего уведомления верхнего уровня должен содержать параметр **hint-people** для указания контакта-отправителя. Этот параметр может принимать значения:

+ **Электронный адрес** 
    + Например: mailto:johndoe@mydomain.com
+ **Номер телефона** 
    + Например: tel:888-888-8888
+ **Удаленный идентификатор** 
    + Например: remoteid:1234

> [!NOTE]
> Если ваше приложение использует [API ContactStore](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.contacts.contactstore) и свойство [StoredContact.RemoteId](https://docs.microsoft.com/en-us/uwp/api/Windows.Phone.PersonalInformation.StoredContact#Windows_Phone_PersonalInformation_StoredContact_RemoteId) для связи контрактов, сохраненных на компьютере, с контрактами, сохраненными удаленно, важно, чтобы значение свойства RemoteId было и стабильным, и уникальным. Это значит, что удаленный идентификатор должен неизменно обозначать одну учетную запись пользователя и содержать уникальный тег, чтобы гарантировать отсутствие конфликтов с удаленными идентификаторами других контактов на компьютере, включая контакты, которыми владеют другие приложения.
> Если уникальность и стабильность удаленных идентификаторов, используемых вашим приложением, нельзя гарантировать, то можно использовать класс RemoteIdHelper, показанный ниже в этом разделе, чтобы добавить ко всем вашим удаленным идентификаторам уникальный тег перед добавлением их к системе. Или можно вовсе отказаться от использования свойства RemoteId и создать собственное расширенное свойство, в котором будут храниться удаленные идентификаторы для ваших контактов.

Помимо второй привязки и полезных данных, НЕОБХОДИМО включить другие полезные данные в первую привязку для резервного всплывающего уведомления (которое будет использоваться, если потребуется вернуться к обычному уведомлению). Это описано более подробно в последнем разделе.

## <a name="creating-the-notification"></a>Создание уведомления
Вы можете создать шаблон уведомления "Близкие люди" так же, как для [всплывающего уведомления](../controls-and-patterns/tiles-and-notifications-adaptive-interactive-toasts.md).

Вот пример создания уведомления "Близкие люди" с помощью статического изображения:

```xml
<toast hint-people="mailto:johndoe@mydomain.com">
    <visual lang="en-US">
        <binding template="ToastGeneric">
            <text hint-style="body">Toast fallback</text>
            <text>Add your fallback toast content here</text>
        </binding>
        <binding template="ToastGeneric" experienceType="shoulderTap">
            <image src="https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/images/shoulder-tap-static-payload.png"/>
        </binding>
    </visual>
</toast>
```

При запуске уведомления оно должно выглядеть следующим образом:

![уведомление со статическим изображением](images/static-image-notification-small.gif)

Далее мы покажем, как создать уведомление с помощью анимации Spritesheet. Высота кадра Spritesheet составляет 80пикселей, частота анимации— 25кадров в секунду. Сделаем 15 кадр начальным и предоставим резервное статическое изображение в параметре "src". Резервное изображение используется, если анимация Spritesheet не отображается.

```xml
<toast hint-people="mailto:johndoe@mydomain.com">
    <visual lang="en-US">
        <binding template="ToastGeneric">
            <text hint-style="body">Toast fallback</text>
            <text>Add your fallback toast content here</text>
        </binding>
        <binding template="ToastGeneric" experienceType="shoulderTap">
            <image src="https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/images/shoulder-tap-pizza-static.png"
                spritesheet-src="https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/images/shoulder-tap-pizza-spritesheet.png"
                spritesheet-height='80' spritesheet-fps='25' spritesheet-startingFrame='15'/>
        </binding>
    </visual>
</toast>
```

При запуске уведомления оно должно выглядеть следующим образом:

![уведомление Spritesheet](images/pizza-notification-small.gif)

## <a name="starting-the-notification"></a>Запуск уведомления
Для запуска уведомления "Близкие люди" необходимо преобразовать шаблон всплывающего уведомления в объект [XmlDocument](https://msdn.microsoft.com/en-us/library/windows/apps/windows.data.xml.dom.xmldocument.aspx). Предположим, что вы определили всплывающее уведомление в XML-файле (здесь это content.xml), тогда используйте следующий код на C#, чтобы запустить его:

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
В некоторых случаях уведомление, обозначенное как уведомление функции "Близкие люди", отображается как обычное всплывающее уведомление. Уведомление функции "Близкие люди" заменяется на обычное всплывающее уведомление в следующих случаях:

+ не удается отобразить уведомление;
+ уведомления функции "Близкие люди" не включены получателем;
+ контакт отправителя не закреплен на панели задач получателя.

Если уведомление функции "Близкие люди" возвращается к всплывающему уведомлению, вторая привязка игнорируется, а для отображения всплывающего уведомления используется только первая привязка. Это означает, что, как отмечалось ранее, необходимо указать резервные полезные данные в первой привязке всплывающего уведомления.

## <a name="see-also"></a>См. также:
+ [Добавление поддержки функции "Близкие люди"](my-people-support.md)
+ [Адаптивные всплывающие уведомления](../controls-and-patterns/tiles-and-notifications-adaptive-interactive-toasts.md)
+ [Класс ToastNotification](https://docs.microsoft.com/en-us/uwp/api/windows.ui.notifications.toastnotification)