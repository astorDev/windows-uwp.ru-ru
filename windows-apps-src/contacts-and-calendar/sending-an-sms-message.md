---
author: normesta
description: В данной статье рассказывается, как запустить диалоговое окно составления SMS, с помощью которого пользователь может отправить SMS-сообщение. Перед отображением диалогового окна вы можете заполнить поля SMS-сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.
title: Отправка SMS-сообщения
ms.assetid: 4D7B509B-1CF0-4852-9691-E96D8352A4D6
keywords: контакты, SMS, отправка
ms.author: normesta
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 06d84646685c6944ab0e816b42cf6fb2125f8a57
ms.sourcegitcommit: ed0304b8a214c03b8aab74b8ef12c9f82b8e3c5f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/19/2018
ms.locfileid: "7291578"
---
# <a name="send-an-sms-message"></a>Отправка SMS-сообщения

В данной статье рассказывается, как запустить диалоговое окно составления SMS, с помощью которого пользователь может отправить SMS-сообщение. Перед отображением диалогового окна вы можете заполнить поля SMS-сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.

Чтобы вызвать этот код, объявления **чата**, **smsSend**и **chatSystem** возможностей в манифесте пакета. Это [ограниченные возможности](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations#special-and-restricted-capabilities) , но их можно использовать в вашем приложении. Утверждение требуется только в том случае, если вы планируете опубликовать приложение в магазин. См. в разделе [типы учетных записей, расположений и сборы](https://docs.microsoft.com/windows/uwp/publish/account-types-locations-and-fees).

## <a name="launch-the-compose-sms-dialog"></a>Запуск диалогового окна составления SMS

Создайте объект [**ChatMessage**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.chat.chatmessage) и укажите данные, которые необходимо предварительно внести в поля диалогового окна составления электронного письма. Чтобы отобразить диалоговое окно, вызовите метод [**ShowComposeSmsMessageAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.chat.chatmessagemanager.showcomposesmsmessageasync).

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

Можно использовать следующий код, чтобы определить, является ли устройство, на котором выполняется ваше приложение сможет отправлять SMS-сообщений.

```csharp
if (Windows.Foundation.Metadata.ApiInformation.IsTypePresent("Windows.ApplicationModel.Chat"))
{
   // Call code here.
}
```

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия

В этой статье рассказано, как запустить диалоговое окно составления SMS. Сведения о том, как выбрать контакты-получатели SMS-сообщения, см. в статье [Выбор контактов](selecting-contacts.md). Скачайте [Примеры универсальных приложений для Windows](http://go.microsoft.com/fwlink/p/?linkid=619979) из GitHub, чтобы просмотреть дополнительные примеры отправки и получения SMS с помощью фоновой задачи.

## <a name="related-topics"></a>Еще по теме

* [Выбор контактов](selecting-contacts.md)
