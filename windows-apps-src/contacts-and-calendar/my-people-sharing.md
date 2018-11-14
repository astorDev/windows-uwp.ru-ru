---
title: Публикации близких людей
description: Описание добавления поддержки публикаций для близких людей
author: muhsinking
ms.author: mukin
ms.date: 06/28/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 7084c4dde7bdf2d59842a04fe9fd52bc029c264a
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6199403"
---
# <a name="my-people-sharing"></a>Публикации близких людей

Функция "Близкие люди" позволяет пользователям закреплять контакты на панели задач, чтобы легко взаимодействовать с ними в любом компоненте Windows, независимо от того, с какими приложения они связаны. Теперь пользователи могут делиться содержимым с закрепленными контактами, перетаскивая файлы из проводника к нужному контакту. Они также могут делиться содержимым с любыми контактами в хранилище контактов Windows с помощью стандартной чудо-кнопки общего доступа. Читайте дальше, чтобы узнать, как сделать ваше приложение доступной целью для публикации с помощью функции "Близкие люди".

![Панель общего доступа "Близкие люди"](images/my-people-sharing.png)

## <a name="requirements"></a>Требования

+ Windows10 и Microsoft Visual Studio2017. Сведения об установке см. в разделе [Настройка Visual Studio](https://docs.microsoft.com/en-us/windows/uwp/get-started/get-set-up).
+ Знание основ C# или похожих объектно-ориентированных языков программирования. Сведения о начале работы с C# см. в разделе [Создание приложения "Привет, мир"](https://docs.microsoft.com/en-us/windows/uwp/get-started/create-a-hello-world-app-xaml-universal).

## <a name="overview"></a>Обзор

Вам необходимо выполнить три действия, что сделать ваше приложением допустимой целью для публикации с помощью функции "Близкие люди".

1. [Объявить поддержку контракта активации shareTarget в манифесте приложения.](https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/my-people-sharing#declaring-support-for-the-share-contract)
2. [Указать контакты, с которыми пользователи могут делиться данными с помощью вашего приложения.](https://docs.microsoft.com/en-us/windows/uwp/contacts-and-calendar/my-people-sharing#annotating-contacts)
3. Реализовать поддержку нескольких экземпляров приложения, запущенных одновременно.  Пользователям необходимо взаимодействовать с полной версией приложения, сохраняя возможность делиться содержимым с другими. Они могут использовать его в нескольких окнах общего доступа одновременно. Для этого приложение должно поддерживать возможность одновременного запуска нескольких представлений. Сведения о том, как это сделать, см. в статье [Отображение нескольких представлений для приложения](https://docs.microsoft.com/en-us/windows/uwp/layout/show-multiple-views).

После этого приложение станет целью общего доступа в окне "Близкие люди", которое можно запустить двумя способами:
1. Контакт выбирается с помощью чудо-кнопки общего доступа.
2. Пользователь перетаскивает файлы к контакту, закрепленному на панели задач.

## <a name="declaring-support-for-the-share-contract"></a>Объявление поддержки контракта отправки данных

Чтобы объявить поддержку приложением получения данных, откройте приложение в Visual Studio. В **обозревателе решений** щелкните правой кнопкой мыши файл **Package.appxmanifest** и выберите команду **Открыть с помощью**. В меню выберите пункт **Редактор (текстовый) XML** и нажмите кнопку **ОК**. Затем внесите следующие изменения в манифест:


**До**
```xml
<Applications>
    <Application Id="MyApp"
      Executable="$targetnametoken$.exe"
      EntryPoint="My.App">
    </Application>
</Applications>
```

**После**

```xml
<Applications>
    <Application Id="MyApp"
      Executable="$targetnametoken$.exe"
      EntryPoint="My.App">
        <Extensions>
            <uap:Extension Category="windows.shareTarget">
                <uap:ShareTarget Description="Share with MyApp">
                    <uap:SupportedFileTypes>
                        <uap:SupportsAnyFileType/>
                    </uap:SupportedFileTypes>
                    <uap:DataFormat>Text</uap:DataFormat>
                    <uap:DataFormat>Bitmap</uap:DataFormat>
                    <uap:DataFormat>Html</uap:DataFormat>
                    <uap:DataFormat>StorageItems</uap:DataFormat>
                    <uap:DataFormat>URI</uap:DataFormat>
                </uap:ShareTarget>
            </uap:Extension>
         </Extensions>
    </Application>
</Applications>
```

Этот код добавляет поддержку всех файлов и форматов данных, но вы можете указать поддерживаемые типы файлов и форматы данных (дополнительные сведения см. в разделе [Документация по классу ShareTarget](https://docs.microsoft.com/en-us/uwp/schemas/appxpackage/appxmanifestschema/element-sharetarget)).

## <a name="annotating-contacts"></a>Аннотация контактов

Чтобы ваше приложение отображалось как цель в окне общего доступа "Близкие люди" для ваших контактов, необходимо записать их в хранилище контактов Windows. Сведения о записи контактов см. в разделе [Пример интеграции карточки контакта](https://github.com/Microsoft/Windows-universal-samples/tree/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/ContactCardIntegration). 

Чтобы приложение отображалось как цель передачи данных функции "Близкие люди", оно должно записать аннотацию для этого контакта. Аннотации— это компоненты данных из приложения, связанные с контактом. Аннотация должна содержать активируемый класс, соответствующий требуемому представлению, в элементе **ProviderProperties**, а также должна объявить поддержку операции **Share**.

Аннотировать контакты можно в любой момент во время работы приложения, но в общем случае необходимо делать это, как только они добавляются в хранилище контактов Windows.

```Csharp
if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 5))
{
    // Create a new contact annotation
    ContactAnnotation annotation = new ContactAnnotation();
    annotation.ContactId = myContact.Id;

    // Add appId and Share support to the annotation
    String appId = "MyApp_vqvv5s4y3scbg!App";
    annotation.ProviderProperties.Add("ContactShareAppID", appId);
    annotation.SupportedOperations = ContactAnnotationOperations::Share;

    // Save annotation to contact annotation list
    // Windows.ApplicationModel.Contacts.ContactAnnotationList 
    await contactAnnotationList.TrySaveAnnotationAsync(annotation);
}
```

"appId"— это имя семейства пакетов, за которым следует символ "!" и идентификатор активируемого класса. Чтобы найти имя семейства пакета, откройте **Package.appxmanifest** , с помощью редактора по умолчанию и перейдите на вкладку «Упаковка». Здесь «Приложение» является активируемый класс, соответствующий представлению цели.

## <a name="running-as-a-my-people-share-target"></a>Запуск приложения в качестве цели общего доступа функции "Близкие люди"

Наконец, чтобы запустить приложение, переопределите метод [OnShareTargetActivated](https://docs.microsoft.com/en-us/uwp/api/Windows.UI.Xaml.Application#Windows_UI_Xaml_Application_OnShareTargetActivated_Windows_ApplicationModel_Activation_ShareTargetActivatedEventArgs_) в основном классе приложения для обработки активации общего доступа. Свойство [ShareTargetActivatedEventArgs.ShareOperation.Contacts](https://docs.microsoft.com/en-us/uwp/api/windows.applicationmodel.datatransfer.sharetarget.shareoperation#Properties) будет содержать контакты, которым передаются данные, или будет пустым, если это стандартная операция общего доступа (не публикация близким людям).

```Csharp
protected override void OnShareTargetActivated(ShareTargetActivatedEventArgs args)
{
    bool isPeopleShare = false;
    if (ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 5))
    {
        // Make sure the current OS version includes the My People feature before
        // accessing the ShareOperation.Contacts property
        isPeopleShare = (args.ShareOperation.Contacts.Count > 0);
    }

    if (isPeopleShare)
    {
        // Show share UI for MyPeople contact(s)
    }
    else
    {
        // Show standard share UI for unpinned contacts
    }
}
```

## <a name="see-also"></a>См. также:
+ [Добавление поддержки функции "Близкие люди"](my-people-support.md)
+ [Класс ShareTarget](https://docs.microsoft.com/en-us/uwp/schemas/appxpackage/appxmanifestschema/element-sharetarget)
+ [Пример интеграции карточки контакта](https://github.com/Microsoft/Windows-universal-samples/tree/6370138b150ca8a34ff86de376ab6408c5587f5d/Samples/ContactCardIntegration)
