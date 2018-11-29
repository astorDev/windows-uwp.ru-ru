---
title: Добавление поддержки функции "Близкие люди" в приложение
description: В этой статье описывается, как добавить поддержку функции "Близкие люди" в приложение, как закрепить и открепить контакты
ms.date: 06/28/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 9a486f27d390a651cec0dcad82246a858bab2f33
ms.sourcegitcommit: 89ff8ff88ef58f4fe6d3b1368fe94f62e59118ad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/29/2018
ms.locfileid: "8193093"
---
# <a name="adding-my-people-support-to-an-application"></a>Добавление поддержки функции "Близкие люди" в приложение

Функция "Близкие люди" позволяет пользователям закреплять контакты из приложения непосредственно на панели задач. При этом создается новый объект контакта, с который они могут взаимодействовать несколькими способами. В этой статье показано, как добавить поддержку этой функции, чтобы пользователи могли закреплять контакты непосредственно из приложения. Когда контакты закреплены, становятся доступны новые типы взаимодействия с пользователем, такие как [публикация для близких людей](my-people-sharing.md) и [уведомления](my-people-notifications.md).

![Чат "Близкие люди"](images/my-people-chat.png)

## <a name="requirements"></a>Требования

+ Windows10 и Microsoft Visual Studio2017. Сведения об установке см. в разделе [Настройка Visual Studio](https://docs.microsoft.com/en-us/windows/uwp/get-started/get-set-up).
+ Знание основ C# или похожих объектно-ориентированных языков программирования. Сведения о начале работы с C# см. в разделе [Создание приложения "Привет, мир"](https://docs.microsoft.com/en-us/windows/uwp/get-started/create-a-hello-world-app-xaml-universal).

## <a name="overview"></a>Обзор

Чтобы реализовать поддержку функции "Близкие люди" в приложении, необходимо выполнить три действия:

1. [Объявить поддержку контракта активации shareTarget в манифесте приложения.](https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/my-people-sharing#declaring-support-for-the-share-contract)
2. [Указать контакты, с которыми пользователи могут делиться данными с помощью вашего приложения.](https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/my-people-sharing#annotating-contacts)
3.  Реализовать поддержку нескольких экземпляров приложения, запущенных одновременно. Пользователям необходимо взаимодействовать с полной версией приложения при работе с ним на панели контактов.  Они могут даже использовать его на нескольких панелях контактов одновременно.  Для этого приложение должно поддерживать возможность одновременного запуска нескольких представлений. Сведения о том, как это сделать, см. в статье [Отображение нескольких представлений для приложения](https://docs.microsoft.com/en-us/windows/uwp/layout/show-multiple-views).

После реализации этой возможности ваше приложение будет отображаться на панели контактов для указанных контактов.

## <a name="declaring-support-for-the-contract"></a>Объявление поддержки контракта

Чтобы объявить поддержку контракта "Близкие люди", откройте приложение в Visual Studio. В **обозревателе решений** щелкните правой кнопкой мыши файл **Package.appxmanifest** и выберите команду **Открыть с помощью**. В меню выберите пункт **Редактор (текстовый) XML** и нажмите кнопку **ОК**. Внесите следующие изменения в манифест:

**До**

```xml
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10">

    <Applications>
        <Application Id="MyApp"
          Executable="$targetnametoken$.exe"
          EntryPoint="My.App">
        </Application>
    </Applications>

```

**После**

```xml
<Package
  xmlns="http://schemas.microsoft.com/appx/manifest/foundation/windows10"
  xmlns:uap="http://schemas.microsoft.com/appx/manifest/uap/windows10"
  xmlns:uap4="http://schemas.microsoft.com/appx/manifest/uap/windows10/4">

    <Applications>
        <Application Id="MyApp"
          Executable="$targetnametoken$.exe"
          EntryPoint="My.App">
          <Extensions>
            <uap4:Extension Category="windows.contactPanel" />
          </Extensions>
        </Application>
    </Applications>

```

После этого ваше приложение можно будет запускать через контракт **windows.ContactPanel**, который позволяет взаимодействовать с панелями контакта.

## <a name="annotating-contacts"></a>Аннотация контактов

Чтобы включить отображение контактов из вашего приложения на панели задач с использованием области "Близкие люди", необходимо записать их в хранилище контактов Windows.  Сведения о записи контактов см. в разделе [Пример карточки контакта](https://github.com/Microsoft/Windows-universal-samples/tree/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/ContactCardIntegration).

Ваше приложение также должно записать аннотацию для каждого контакта. Аннотации— это компоненты данных из приложения, связанные с контактом. Аннотация должна содержать активируемый класс, соответствующий требуемому представлению, в элементе **ProviderProperties**, а также должна объявить поддержку операции **ContactProfile**.

Аннотировать контакты можно в любой момент во время работы приложения, но в общем случае необходимо делать это, как только они добавляются в хранилище контактов Windows.

```Csharp
if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 5))
{
    // Create a new contact annotation
    ContactAnnotation annotation = new ContactAnnotation();
    annotation.ContactId = myContact.Id;

    // Add appId and contact panel support to the annotation
    String appId = "MyApp_vqvv5s4y3scbg!App";
    annotation.ProviderProperties.Add("ContactPanelAppID", appId);
    annotation.SupportedOperations = ContactAnnotationOperations.ContactProfile;

    // Save annotation to contact annotation list
    // Windows.ApplicationModel.Contacts.ContactAnnotationList 
    await contactAnnotationList.TrySaveAnnotationAsync(annotation));
}
```

"appId"— это имя семейства пакетов, за которым следует символ "!" и идентификатор активируемого класса. Чтобы найти имя семейства пакета, откройте **Package.appxmanifest** с помощью редактора по умолчанию и найдите вкладку «Упаковка». Здесь элемент «Приложение» является активируемым классом, соответствующим представлению запуска приложения.

## <a name="allow-contacts-to-invite-new-potential-users"></a>Предоставление контактам возможности приглашать новых потенциальных пользователей

По умолчанию приложение отображается на панели контактов только для указанных вами контактов.  Это позволяет избежать путаницы с контактами, с которыми невозможно взаимодействовать с помощью вашего приложения.  Чтобы приложение отображалось для контактов, которые неизвестны вашему приложению (например, чтобы пригласить пользователей добавить эти контакты в свою учетную запись), можно добавить следующий код в манифест:

**До**

```Csharp
<Applications>
    <Application Id="MyApp"
      Executable="$targetnametoken$.exe"
      EntryPoint="My.App">
      <Extensions>
        <uap4:Extension Category="windows.contactPanel" />
      </Extensions>
    </Application>
</Applications>
```

**После**

```Csharp
<Applications>
    <Application Id="MyApp"
      Executable="$targetnametoken$.exe"
      EntryPoint="My.App">
      <Extensions>
        <uap4:Extension Category="windows.contactPanel">
            <uap4:ContactPanel SupportsUnknownContacts="true" />
        </uap4:Extension>
      </Extensions>
    </Application>
</Applications>
```

После этого изменения приложение будет отображаться как доступный параметр на панели контактов для всех закрепленных пользователем контактов.  Когда приложение активируются с помощью контракта панели контактов, необходимо проверить, известен ли этот контакт вашему приложению.  Если это не так, следует отобразить интерфейс для нового пользователя.

![Панель контактов "Близкие люди"](images/my-people.png)

## <a name="support-for-email-apps"></a>Поддержка почтовых приложений

Если вы создаете почтовое приложение, вам не нужно аннотировать каждый контакт вручную.  Если объявить поддержку панели контактов и протокола mailto:, приложение автоматически будет отображаться для пользователей с электронным адресом.

## <a name="running-in-the-contact-panel"></a>Запуск на панели контактов

Когда приложение появится на панели контактов для некоторых или всех пользователей, необходимо обрабатывать активацию с помощью контракта панели контактов.

```Csharp
override protected void OnActivated(IActivatedEventArgs e)
{
    if (e.Kind == ActivationKind.ContactPanel)
    {
        // Create a Frame to act as the navigation context and navigate to the first page
        var rootFrame = new Frame();

        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        // Navigate to the page that shows the Contact UI.
        rootFrame.Navigate(typeof(ContactPage), e);

        // Ensure the current window is active
        Window.Current.Activate();
    }
}
```

Если приложение активируется с этим контрактом, оно получает [объект ContactPanelActivatedEventArgs](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.activation.contactpanelactivatedeventargs).  Он содержит идентификатор контакта, с которым ваше приложение пытается взаимодействовать при запуске, и объект [ContactPanel](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.contacts.contactpanel). Вам следует сохранить ссылку на этот объект ContactPanel, что позволит взаимодействовать с панелью.

Объект ContactPanel предоставляет два события, которые ваше приложение должно прослушивать.
+ Событие **LaunchFullAppRequested** отправляется, когда пользователь вызывает элемент интерфейса, который запрашивает запуск полного приложения в отдельном окне.  Ваше приложение отвечает за собственный запуск и передачу необходимого контекста.  Вы можете сделать это любым способом (например, за счет запуска протокола).
+ **Событие Closing** отправляется, когда приложение закрывается, что позволяет сохранить любой контекст.

С помощью объекта ContactPanel также можно задать цвет фона заголовка панели контактов (если он не задан, используется цвет системной темы) и программно закрыть панель контактов.

## <a name="supporting-notification-badging"></a>Поддержка индикаторов для уведомлений

Если вы хотите, чтобы на контактах, закрепленных на панели задач, появлялись индикаторы при получении новых уведомлений, связанных с этим контактом, из вашего приложения, необходимо включить параметр **hint-people** во [всплывающих уведомлениях](https://docs.microsoft.com/en-us/windows/uwp/shell/tiles-and-notifications/adaptive-interactive-toasts) и явные [Уведомления близких людей](https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/my-people-notifications).

![Индикаторы для уведомлений людей](images/my-people-badging.png)

Для добавления индикатора контакту узел всплывающего уведомления верхнего уровня должен содержать параметр hint-people для указания контакта-отправителя или связанного контакта. Этот параметр может принимать любое из перечисленных ниже значений.
+ **Адрес электронной почты** 
    + Например: mailto:johndoe@mydomain.com
+ **Номер телефона** 
    + Например: tel:888-888-8888
+ **Удаленный идентификатор** 
    + Например: remoteid:1234

Ниже приведен пример определения всплывающего уведомления, связанного с определенным человеком.
```XML
<toast hint-people="mailto:johndoe@mydomain.com">
    <visual lang="en-US">
        <binding template="ToastText01">
            <text>John Doe posted a comment.</text>
        </binding>
    </visual>
</toast>
```

> [!NOTE]
> Если ваше приложение использует [API ContactStore](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.contacts.contactstore) и свойство [StoredContact.RemoteId](https://docs.microsoft.com/en-us/uwp/api/Windows.Phone.PersonalInformation.StoredContact.RemoteId) для связи контрактов, сохраненных на компьютере, с контрактами, сохраненными удаленно, важно, чтобы значение свойства RemoteId было и стабильным, и уникальным. Это значит, что удаленный идентификатор должен неизменно обозначать одну учетную запись пользователя и содержать уникальный тег, чтобы гарантировать отсутствие конфликтов с удаленными идентификаторами других контактов на компьютере, включая контакты, которыми владеют другие приложения.
> Если уникальность и стабильность удаленных идентификаторов, используемых вашим приложением, нельзя гарантировать, то можно использовать класс RemoteIdHelper, показанный ниже в этом разделе, чтобы добавить ко всем вашим удаленным идентификаторам уникальный тег перед добавлением их к системе. Или можно вовсе отказаться от использования свойства RemoteId и создать собственное расширенное свойство, в котором будут храниться удаленные идентификаторы для ваших контактов.

## <a name="the-pinnedcontactmanager-class"></a>Класс The PinnedContactManager

Класс [PinnedContactManager](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.contacts.pinnedcontactmanager) используется для управления контактами, которые закреплены на панели задач. Этот класс позволяет закрепить и открепить контакты, определить, закреплен ли контакт и поддерживается ли закрепление на той или иной поверхности системой, в которой сейчас выполняется приложение.

Вы можете получить объект PinnedContactManager с помощью метода **GetDefault**:

```Csharp
PinnedContactManager pinnedContactManager = PinnedContactManager.GetDefault();
```

## <a name="pinning-and-unpinning-contacts"></a>Закрепление и открепление контактов
Теперь вы можете закреплять и откреплять контакты с помощью созданного объекта PinnedContactManager. Методы **RequestPinContactAsync** и **RequestUnpinContactAsync** предоставляют пользователям диалоговые окна подтверждения, поэтому их следует вызывать из вашего потока ASTA или пользовательского интерфейса.

```Csharp
async void PinContact (Contact contact)
{
    await pinnedContactManager.RequestPinContactAsync(contact,
                                                      PinnedContactSurface.Taskbar);
}

async void UnpinContact (Contact contact)
{
    await pinnedContactManager.RequestUnpinContactAsync(contact,
                                                        PinnedContactSurface.Taskbar);
}
```

Можно также закрепить несколько контактов одновременно:

```Csharp
async Task PinMultipleContacts(Contact[] contacts)
{
    await pinnedContactManager.RequestPinContactsAsync(
        contacts, PinnedContactSurface.Taskbar);
}
```

> [!Note]
> В данный момент пакетная операция для закрепления контактов отсутствует.

**Примечание.** 

## <a name="see-also"></a>См. также:
+ [Публикации близких людей](my-people-sharing.md)
+ [Уведомления близких людей](my-people-notifications.md)
+ [Видео Channel 9 о добавлении поддержки функции "Близкие люди" в приложение](https://channel9.msdn.com/Events/Build/2017/P4056)
+ [Пример интеграции функции "Близкие люди"](http://aka.ms/mypeoplebuild2017)
+ [Пример карточки контакта](https://github.com/Microsoft/Windows-universal-samples/tree/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/ContactCardIntegration)
+ [Документация по классу PinnedContactManager](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.contacts.pinnedcontactmanager)
+ [Подключение приложения к действиям в карточках контактов](https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/integrating-with-contacts)
