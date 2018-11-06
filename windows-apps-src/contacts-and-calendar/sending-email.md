---
author: normesta
description: В данной статье рассказывается, как запустить диалоговое окно составления электронного письма, с помощью которого пользователь может отправить электронное письмо. Перед отображением диалогового окна вы можете заполнить поля сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.
title: Отправка сообщения электронной почты
ms.assetid: 74511E90-9438-430E-B2DE-24E196A111E5
keywords: контакты, электронное письмо, отправка
ms.author: normesta
ms.date: 10/11/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 0a28809210f71bf523e3cc5f9c8da1db9fbcc90c
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6046977"
---
# <a name="send-email"></a>Отправка сообщения электронной почты

В данной статье рассказывается, как запустить диалоговое окно составления электронного письма, с помощью которого пользователь может отправить электронное письмо. Перед отображением диалогового окна вы можете заполнить поля сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.

**В этой статье:**

-   [Запуск диалогового окна составления электронного письма](#launch-the-compose-email-dialog)
-   [Сводка и дальнейшие действия](#summary-and-next-steps)
-   [Еще по теме](#related-topics)

## <a name="launch-the-compose-email-dialog"></a>Запуск диалогового окна составления электронного письма

Создайте объект [**EmailMessage**](https://msdn.microsoft.com/library/windows/apps/Dn631270) и укажите данные, которые необходимо предварительно внести в поля диалогового окна составления электронного письма. Чтобы отобразить диалоговое окно, вызовите метод [**ShowComposeNewEmailAsync**](https://msdn.microsoft.com/library/windows/apps/Dn631269).

``` cs
private async Task ComposeEmail(Windows.ApplicationModel.Contacts.Contact recipient,
    string subject, string messageBody)
{
    var emailMessage = new Windows.ApplicationModel.Email.EmailMessage();
    emailMessage.Body = messageBody;

    var email = recipient.Emails.FirstOrDefault<Windows.ApplicationModel.Contacts.ContactEmail>();
    if (email != null)
    {
        var emailRecipient = new Windows.ApplicationModel.Email.EmailRecipient(email.Address);
        emailMessage.To.Add(emailRecipient);
        emailMessage.Subject = subject;
    }

    await Windows.ApplicationModel.Email.EmailManager.ShowComposeNewEmailAsync(emailMessage);
}
```

>[!NOTE]
> Вложения, добавляемых в сообщение электронной почты с помощью класса [EmailAttachment](https://docs.microsoft.com/uwp/api/windows.applicationmodel.email.emailattachment) будет отображаться только в приложение "Почта". Если пользователи имеют другие программы Почта, настроенный в качестве своей программы почты по умолчанию, то составления появится окно без вложения. Это известная проблема.

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия

В данной статье рассказано, как запустить диалоговое окно составления электронного письма. Сведения о том, как выбрать контакты-получатели электронного письма, см. в статье [Выбор контактов](selecting-contacts.md). См. [**PickSingleFileAsync**](https://msdn.microsoft.com/library/windows/apps/JJ635275) для выбора файла в качестве вложения в сообщение.

## <a name="related-topics"></a>Еще по теме

* [Выбор контактов](selecting-contacts.md)
* [Как продолжить работу приложения для Windows Phone после вызова средства выбора файлов](https://msdn.microsoft.com/library/windows/apps/xaml/Dn614994)
 

 
