---
author: normesta
description: Пространство имен Windows.ApplicationModel.Contacts позволяет выбирать контакты несколькими способами.
title: Выбор контактов
ms.assetid: 35FEDEE6-2B0E-4391-84BA-5E9191D4E442
keywords: контакты, выбор, выбор одного контакта, выбор нескольких контактов, контакты, выбор нескольких, выбор определенных, контактные данные, контакт, выбор определенных контактных данных, выбор определенных полей
ms.author: normesta
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: a721e618864155e4eec66d222e8eeafa2e0ca038
ms.sourcegitcommit: 38f06f1714334273d865935d9afb80efffe97a17
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2018
ms.locfileid: "6194810"
---
# <a name="select-contacts"></a>Выбор контактов



Пространство имен [**Windows.ApplicationModel.Contacts**](https://msdn.microsoft.com/library/windows/apps/BR225002) позволяет выбирать контакты несколькими способами. В этой статье описано, как выбрать один или несколько контактов, и показано, как настроить окно выбора контактов, чтобы получать только необходимые вашему приложению сведения о контактах.

## <a name="set-up-the-contact-picker"></a>Настройка окна выбора контактов

Создайте экземпляр [**Windows.ApplicationModel.Contacts.ContactPicker**](https://msdn.microsoft.com/library/windows/apps/BR224913) и назначьте его переменной.

```cs
var contactPicker = new Windows.ApplicationModel.Contacts.ContactPicker();
```

## <a name="set-the-selection-mode-optional"></a>Настройка режима выбора (необязательно)

По умолчанию окно выбора контактов извлекает все доступные данные о контактах, выбранных пользователем. Свойство [**SelectionMode**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.contacts.contactpicker.selectionmode) позволяет настроить окно выбора контактов так, чтобы получать только поля данных, необходимые вашему приложению. Этот способ использования окна выбора контактов более эффективен, если нужна только часть доступных сведений контакта.

Сначала задайте свойство [**SelectionMode**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.contacts.contactpicker.selectionmode) для параметра **Поля**:

```cs
contactPicker.SelectionMode = Windows.ApplicationModel.Contacts.ContactSelectionMode.Fields;
```

Затем при помощи свойства [**DesiredFieldsWithContactFieldType**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.contacts.contactpicker.desiredfieldswithcontactfieldtype) укажите поля, которые должно извлекать окно выбора контактов. В примере ниже показано, как настроить окно выбора контактов для извлечения адресов электронной почты.

``` cs
contactPicker.DesiredFieldsWithContactFieldType.Add(Windows.ApplicationModel.Contacts.ContactFieldType.Email);
```

## <a name="launch-the-picker"></a>Запуск окна выбора контактов

```cs
Contact contact = await contactPicker.PickContactAsync();
```

Если вы хотите, чтобы пользователь выбрал один или несколько контактов, используйте [**PickContactsAsync**](https://msdn.microsoft.com/library/windows/apps/windows.applicationmodel.contacts.contactpicker.pickcontactsasync).

```cs
public IList<Contact> contacts;
contacts = await contactPicker.PickContactsAsync();
```

## <a name="process-the-contacts"></a>Обработка контактов

Когда окно выбора возвращает данные, проверьте, выбрал ли пользователь какие-либо контакты. Если да, обработайте сведения о контактах.

В данном примере показано, как обработать один контакт. Здесь мы извлекаем имя контакта и копируем его в элемент управления [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/BR209652) с именем *OutputName*.

```cs
if (contact != null)
{
    OutputName.Text = contact.DisplayName;
}
else
{
    rootPage.NotifyUser("No contact was selected.", NotifyType.ErrorMessage);
}
```

В этом примере показано, как обработать несколько контактов.

```cs
if (contacts != null && contacts.Count > 0)
{
    foreach (Contact contact in contacts)
    {
        // Do something with the contact information.
    }
}
```

## <a name="complete-example-single-contact"></a>Полный пример (один контакт)

В данном примере окно выбора контактов извлекает имя, а также адрес электронной почты, расположение или номер телефона одного контакта.

```cs
// ...
using Windows.ApplicationModel.Contacts;
// ...

private async void PickAContactButton-Click(object sender, RoutedEventArgs e)
{
    ContactPicker contactPicker = new ContactPicker();

    contactPicker.DesiredFieldsWithContactFieldType.Add(ContactFieldType.Email);
    contactPicker.DesiredFieldsWithContactFieldType.Add(ContactFieldType.Address);
    contactPicker.DesiredFieldsWithContactFieldType.Add(ContactFieldType.PhoneNumber);

    Contact contact = await contactPicker.PickContactAsync();

    if (contact != null)
    {
        OutputFields.Visibility = Visibility.Visible;
        OutputEmpty.Visibility = Visibility.Collapsed;

        OutputName.Text = contact.DisplayName;

        AppendContactFieldValues(OutputEmails, contact.Emails);
        AppendContactFieldValues(OutputPhoneNumbers, contact.Phones);
        AppendContactFieldValues(OutputAddresses, contact.Addresses);
    }
    else
    {
        OutputEmpty.Visibility = Visibility.Visible;
        OutputFields.Visibility = Visibility.Collapsed;
    }
}

private void AppendContactFieldValues<T>(TextBlock content, IList<T> fields)
{
    if (fields.Count > 0)
    {
        StringBuilder output = new StringBuilder();

        if (fields[0].GetType() == typeof(ContactEmail))
        {
            foreach (ContactEmail email in fields as IList<ContactEmail>)
            {
                output.AppendFormat("Email: {0} ({1})\n", email.Address, email.Kind);
            }
        }
        else if (fields[0].GetType() == typeof(ContactPhone))
        {
            foreach (ContactPhone phone in fields as IList<ContactPhone>)
            {
                output.AppendFormat("Phone: {0} ({1})\n", phone.Number, phone.Kind);
            }
        }
        else if (fields[0].GetType() == typeof(ContactAddress))
        {
            List<String> addressParts = null;
            string unstructuredAddress = "";

            foreach (ContactAddress address in fields as IList<ContactAddress>)
            {
                addressParts = (new List<string> { address.StreetAddress, address.Locality, address.Region, address.PostalCode });
                unstructuredAddress = string.Join(", ", addressParts.FindAll(s => !string.IsNullOrEmpty(s)));
                output.AppendFormat("Address: {0} ({1})\n", unstructuredAddress, address.Kind);
            }
        }

        content.Visibility = Visibility.Visible;
        content.Text = output.ToString();
    }
    else
    {
        content.Visibility = Visibility.Collapsed;
    }
}
```

## <a name="complete-example-multiple-contacts"></a>Полный пример (несколько контактов)

В данном примере окно выбора контактов получает несколько контактов, а затем добавляет контакты в элемент управления [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) с именем `OutputContacts`.

```cs
MainPage rootPage = MainPage.Current;
public IList<Contact> contacts;

private async void PickContactsButton-Click(object sender, RoutedEventArgs e)
{
    var contactPicker = new Windows.ApplicationModel.Contacts.ContactPicker();
    contactPicker.CommitButtonText = "Select";
    contacts = await contactPicker.PickContactsAsync();

    // Clear the ListView.
    OutputContacts.Items.Clear();

    if (contacts != null && contacts.Count > 0)
    {
        OutputContacts.Visibility = Windows.UI.Xaml.Visibility.Visible;
        OutputEmpty.Visibility = Visibility.Collapsed;

        foreach (Contact contact in contacts)
        {
            // Add the contacts to the ListView.
            OutputContacts.Items.Add(new ContactItemAdapter(contact));
        }
    }
    else
    {
        OutputEmpty.Visibility = Visibility.Visible;
    }         
}
```

``` cs
public class ContactItemAdapter
{
    public string Name { get; private set; }
    public string SecondaryText { get; private set; }

    public ContactItemAdapter(Contact contact)
    {
        Name = contact.DisplayName;
        if (contact.Emails.Count > 0)
        {
            SecondaryText = contact.Emails[0].Address;
        }
        else if (contact.Phones.Count > 0)
        {
            SecondaryText = contact.Phones[0].Number;
        }
        else if (contact.Addresses.Count > 0)
        {
            List<string> addressParts = (new List<string> { contact.Addresses[0].StreetAddress,
              contact.Addresses[0].Locality, contact.Addresses[0].Region, contact.Addresses[0].PostalCode });
              string unstructuredAddress = string.Join(", ", addressParts.FindAll(s => !string.IsNullOrEmpty(s)));
            SecondaryText = unstructuredAddress;
        }
    }
}
```

## <a name="summary-and-next-steps"></a>Сводка и дальнейшие действия

Теперь вы имеете представление об использовании окна выбора контактов для получения контактных данных. Скачайте [Примеры универсальных приложений для Windows](http://go.microsoft.com/fwlink/p/?linkid=619979) из GitHub, чтобы ознакомиться с другими примерами использования контактов и окна выбора контактов.
