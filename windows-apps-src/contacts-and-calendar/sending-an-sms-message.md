---
description: В данной статье рассказывается, как запустить диалоговое окно составления SMS, с помощью которого пользователь может отправить SMS-сообщение. Перед отображением диалогового окна вы можете заполнить поля SMS-сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.
title: Отправка SMS-сообщения
ms.assetid: 4D7B509B-1CF0-4852-9691-E96D8352A4D6
keywords: контакты, SMS, отправка
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: ea262e026f31e1d690673f9b1d88e882d88ee4aa
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74255006"
---
# <a name="send-an-sms-message"></a>Отправка SMS-сообщения

В данной статье рассказывается, как запустить диалоговое окно составления SMS, с помощью которого пользователь может отправить SMS-сообщение. Перед отображением диалогового окна вы можете заполнить поля SMS-сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.

Чтобы вызвать этот код, объявите возможности **разговора**, **смссенд**и **чатсистем** в манифесте пакета. Это [ограниченные возможности](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations#special-and-restricted-capabilities) , но их можно использовать в приложении. Утверждение необходимо только в том случае, если вы планируете публиковать приложение в магазине. См. раздел [типы учетных записей, местоположения и сборы](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees).

## <a name="launch-the-compose-sms-dialog"></a>Запуск диалогового окна составления SMS

Создайте объект [**ChatMessage**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.chat.chatmessage) и укажите данные, которые необходимо предварительно внести в поля диалогового окна составления электронного письма. Чтобы отобразить диалоговое окно, вызовите метод [**ShowComposeSmsMessageAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.chat.chatmessagemanager.showcomposesmsmessageasync).

```cs
private async void ComposeSms(Windows.ApplicationModel.Contacts.Contact recipient,
    string messageBody,
    StorageFile attachmentFile,
    string mimeType)
{
    var chatMessage = new Windows.ApplicationModel.Chat.ChatMessage();
    chatMessage.Body = messageBody;

    if (attachmentFile != null)
    {
        var stream = Windows.Storage.Streams.RandomAccessStreamReference.CreateFromFile(attachmentFile);

        var attachment = new Windows.ApplicationModel.Chat.ChatMessageAttachment(
            mimeType,
            stream);

        chatMessage.Attachments.Add(attachment);
    }

    var phone = recipient.Phones.FirstOrDefault<Windows.ApplicationModel.Contacts.ContactPhone>();
    if (phone != null)
    {
        chatMessage.Recipients.Add(phone.Number);
    }
    await Windows.ApplicationModel.Chat.ChatMessageManager.ShowComposeSmsMessageAsync(chatMessage);
}
```

Чтобы определить, может ли устройство, выполняющее приложение, передавать SMS-сообщения, можно использовать следующий код.

```csharp
if (Windows.Foundation.Metadata.ApiInformation.IsTypePresent("Windows.ApplicationModel.Chat"))
{
   // Call code here.
}
```

## <a name="summary-and-next-steps"></a>Краткая сводка и дальнейшие действия

В этой статье рассказано, как запустить диалоговое окно составления SMS. Сведения о том, как выбрать контакты-получатели SMS-сообщения, см. в статье [Выбор контактов](selecting-contacts.md). Скачайте [Примеры универсальных приложений для Windows](https://github.com/Microsoft/Windows-universal-samples) из GitHub, чтобы просмотреть дополнительные примеры отправки и получения SMS с помощью фоновой задачи.

## <a name="related-topics"></a>Статьи по теме

* [Выбор контактов](selecting-contacts.md)
