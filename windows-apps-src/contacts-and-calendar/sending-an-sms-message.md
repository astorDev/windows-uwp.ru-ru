---
description: В данной статье рассказывается, как запустить диалоговое окно составления SMS, с помощью которого пользователь может отправить SMS-сообщение. Перед отображением диалогового окна вы можете заполнить поля SMS-сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.
title: Отправка SMS-сообщения
ms.assetid: 4D7B509B-1CF0-4852-9691-E96D8352A4D6
keywords: контакты, SMS, отправка
---

# Отправка SMS-сообщения

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В данной статье рассказывается, как запустить диалоговое окно составления SMS, с помощью которого пользователь может отправить SMS-сообщение. Перед отображением диалогового окна вы можете заполнить поля SMS-сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.

## Запуск диалогового окна составления SMS

Создайте объект [**ChatMessage**](https://msdn.microsoft.com/library/windows/apps/Dn642160) и укажите данные, которые необходимо предварительно внести в поля диалогового окна составления электронного письма. Чтобы отобразить диалоговое окно, вызовите метод [**ShowComposeSmsMessageAsync**](https://msdn.microsoft.com/library/windows/apps/Dn642160manager-showcomposesmsmessageasync).

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

    var phone = recipient.Phones.FirstOrDefault&lt;Windows.ApplicationModel.Contacts.ContactPhone&gt;();
    if (phone != null)
    {
        chatMessage.Recipients.Add(phone.Number);
    }
    await Windows.ApplicationModel.Chat.ChatMessageManager.ShowComposeSmsMessageAsync(chatMessage);
}
```

## Сводка и дальнейшие действия

В этой статье рассказано, как запустить диалоговое окно составления SMS. Сведения о том, как выбрать контакты-получатели SMS-сообщения, см. в статье [Выбор контактов](selecting-contacts.md). Скачайте [Примеры универсальных приложений для Windows](http://go.microsoft.com/fwlink/p/?linkid=619979) из GitHub, чтобы просмотреть дополнительные примеры отправки и получения SMS с помощью фоновой задачи.

## Связанные разделы

* [Выбор контактов](selecting-contacts.md)




<!--HONumber=Mar16_HO1-->


