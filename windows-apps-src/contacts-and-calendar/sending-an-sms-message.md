---
author: Xansky
description: "В данной статье рассказывается, как запустить диалоговое окно составления SMS, с помощью которого пользователь может отправить SMS-сообщение. Перед отображением диалогового окна вы можете заполнить поля SMS-сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки."
title: "Отправка SMS-сообщения"
ms.assetid: 4D7B509B-1CF0-4852-9691-E96D8352A4D6
keywords: "контакты, SMS, отправка"
ms.author: mhopkins
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.openlocfilehash: 3d2edd2e26633abacce07a2a6fe9f8c9d14b0afe
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="send-an-sms-message"></a>Отправка SMS-сообщения

\[ Обновлено для приложений UWP в Windows10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В данной статье рассказывается, как запустить диалоговое окно составления SMS, с помощью которого пользователь может отправить SMS-сообщение. Перед отображением диалогового окна вы можете заполнить поля SMS-сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.

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

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия

В этой статье рассказано, как запустить диалоговое окно составления SMS. Сведения о том, как выбрать контакты-получатели SMS-сообщения, см. в статье [Выбор контактов](selecting-contacts.md). Скачайте [Примеры универсальных приложений для Windows](http://go.microsoft.com/fwlink/p/?linkid=619979) из GitHub, чтобы просмотреть дополнительные примеры отправки и получения SMS с помощью фоновой задачи.

## <a name="related-topics"></a>Еще по теме

* [Выбор контактов](selecting-contacts.md)
