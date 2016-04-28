---
description: Статья рассказывает, как запустить диалоговое окно составления электронного письма, с помощью которого пользователь может отправить электронное письмо. Перед отображением диалогового окна вы можете заполнить поля сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.
title: Отправка сообщения электронной почты
ms.assetid: 74511E90-9438-430E-B2DE-24E196A111E5
keywords: контакты, электронное письмо, отправка
---

# Отправка сообщения электронной почты

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


В данной статье рассказывается, как запустить диалоговое окно составления электронного письма, с помощью которого пользователь может отправить электронное письмо. Перед отображением диалогового окна вы можете заполнить поля сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.

**В этой статье:**

-   [Запуск диалогового окна составления электронного письма](#launch-the-compose-email-dialog)
-   [Сводка и дальнейшие действия](#summary-and-next-steps)
-   [Связанные разделы](#related-topics)

## Запуск диалогового окна составления электронного письма

Создайте объект [**EmailMessage**](https://msdn.microsoft.com/library/windows/apps/Dn631270) и укажите данные, которые необходимо предварительно внести в поля диалогового окна составления электронного письма. Чтобы отобразить диалоговое окно, вызовите метод [**ShowComposeNewEmailAsync**](https://msdn.microsoft.com/library/windows/apps/Dn631269).

``` cs
private async void ComposeEmail(Windows.ApplicationModel.Contacts.Contact recipient, 
    string messageBody, 
    StorageFile attachmentFile)
{
    var emailMessage = new Windows.ApplicationModel.Email.EmailMessage();
    emailMessage.Body = messageBody;

    if (attachmentFile != null)
    {
        var stream = Windows.Storage.Streams.RandomAccessStreamReference.CreateFromFile(attachmentFile);

        var attachment = new Windows.ApplicationModel.Email.EmailAttachment(
            attachmentFile.Name,
            stream);

        emailMessage.Attachments.Add(attachment);
    }

    var email = recipient.Emails.FirstOrDefault&lt;Windows.ApplicationModel.Contacts.ContactEmail&gt;();
    if (email != null)
    {
        var emailRecipient = new Windows.ApplicationModel.Email.EmailRecipient(email.Address);
        emailMessage.To.Add(emailRecipient);
    }

    await Windows.ApplicationModel.Email.EmailManager.ShowComposeNewEmailAsync(emailMessage);
        
}
```

## Сводка и дальнейшие действия

В данной статье рассказано, как запустить диалоговое окно составления электронного письма. Сведения о том, как выбрать контакты-получатели электронного письма, см. в статье [Выбор контактов](selecting-contacts.md). См. [**PickSingleFileAsync**](https://msdn.microsoft.com/library/windows/apps/JJ635275) для выбора файла в качестве вложения в сообщение.

## Связанные разделы

* [Выбор контактов](selecting-contacts.md)
* [Как продолжить работу приложения для Windows Phone после вызова средства выбора файлов](https://msdn.microsoft.com/library/windows/apps/xaml/Dn614994)
 

 






<!--HONumber=Mar16_HO1-->


