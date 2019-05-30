---
description: В данной статье рассказывается, как запустить диалоговое окно составления электронного письма, с помощью которого пользователь может отправить электронное письмо. Перед отображением диалогового окна вы можете заполнить поля сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.
title: Отправка сообщения электронной почты
ms.assetid: 74511E90-9438-430E-B2DE-24E196A111E5
keywords: контакты, электронное письмо, отправка
ms.date: 10/11/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 524e1f12c3da0d9d06e73d84e08e2d54efde9a7e
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66361221"
---
# <a name="send-email"></a>Отправка сообщения электронной почты

В данной статье рассказывается, как запустить диалоговое окно составления электронного письма, с помощью которого пользователь может отправить электронное письмо. Перед отображением диалогового окна вы можете заполнить поля сообщения значениями по умолчанию. Сообщение не будет отправлено до тех пор, пока пользователь не нажмет кнопку отправки.

**В этой статье**

-   [Открыть диалоговое окно создания сообщения электронной почты](#launch-the-compose-email-dialog)
-   [Сводка и дальнейшие действия](#summary-and-next-steps)
-   [Связанные разделы](#related-topics)

## <a name="launch-the-compose-email-dialog"></a>Запуск диалогового окна составления электронного письма

Создайте объект [**EmailMessage**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Email.EmailMessage) и укажите данные, которые необходимо предварительно внести в поля диалогового окна составления электронного письма. Чтобы отобразить диалоговое окно, вызовите метод [**ShowComposeNewEmailAsync**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.email.emailmanager.showcomposenewemailasync).

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
> Вложения, добавленные в сообщение электронной почты с помощью [EmailAttachment](https://docs.microsoft.com/uwp/api/windows.applicationmodel.email.emailattachment) класс будет отображаться только в приложение "Почта". Если пользователи используют другие программы электронной почты настроен в качестве этих программах по умолчанию, то compose появится окно без вложений. Это известная проблема.

## <a name="summary-and-next-steps"></a>Краткая сводка и дальнейшие действия

В данной статье рассказано, как запустить диалоговое окно составления электронного письма. Сведения о том, как выбрать контакты-получатели электронного письма, см. в статье [Выбор контактов](selecting-contacts.md). См. [**PickSingleFileAsync**](https://docs.microsoft.com/uwp/api/windows.storage.pickers.fileopenpicker.picksinglefileasync) для выбора файла в качестве вложения в сообщение.

## <a name="related-topics"></a>См. также

* [Выбор контактов](selecting-contacts.md)
* [Как для продолжения после вызова средства выбора файлов для приложения Windows Phone](https://docs.microsoft.com/previous-versions/windows/apps/dn614994(v=win.10))
 

 
