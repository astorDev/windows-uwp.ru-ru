---
description: Пространство имен Windows.ApplicationModel.Contacts позволяет выбирать контакты несколькими способами.
title: Выбрать контакты
ms.assetid: 35FEDEE6-2B0E-4391-84BA-5E9191D4E442
keywords: контакты, выбор
keywords: выбор одного контакта
keywords: выбор нескольких контактов
keywords: контакты, выбор нескольких
keywords: выбор определенных данных контактов
keywords: контакт, выбор определенных данных
keywords: контакт, выбор определенных полей
---

# Выбор контактов

\[ Обновлено для приложений UWP в Windows 10. Статьи о Windows 8.x см. в [архиве](http://go.microsoft.com/fwlink/p/?linkid=619132) \]


Пространство имен [**Windows.ApplicationModel.Contacts**](https://msdn.microsoft.com/library/windows/apps/BR225002) позволяет выбирать контакты несколькими способами. В этой статье описано, как выбрать один или несколько контактов, и показано, как настроить окно выбора контактов, чтобы получать только необходимые вашему приложению сведения о контактах.

## Настройка окна выбора контактов

Создайте экземпляр [**Windows.ApplicationModel.Contacts.ContactPicker**](https://msdn.microsoft.com/library/windows/apps/BR224913) и назначьте его переменной.

```cs
var contactPicker = new Windows.ApplicationModel.Contacts.ContactPicker();
```

## Настройка режима выбора (необязательно)

По умолчанию окно выбора контактов извлекает все доступные данные о контактах, выбранных пользователем. Свойство [**SelectionMode**](https://msdn.microsoft.com/library/windows/apps/BR224913-selectionmode) позволяет настроить окно выбора контактов так, чтобы получать только поля данных, необходимые вашему приложению. Этот способ использования окна выбора контактов более эффективен, если нужна только часть доступных сведений контакта.

Сначала задайте свойство [**SelectionMode**](https://msdn.microsoft.com/library/windows/apps/BR224913-selectionmode) для параметра **Поля**:

```cs
contactPicker.SelectionMode = Windows.ApplicationModel.Contacts.ContactSelectionMode.Fields;
```

Затем при помощи свойства [**desiredFieldsWithContactFieldType**](https://msdn.microsoft.com/library/windows/apps/BR224913-desiredfieldswithcontactfieldtype) укажите поля, которые должно извлекать окно выбора контактов. В примере ниже показано, как настроить окно выбора контактов для извлечения электронных адресов.

``` cs
contactPicker.DesiredFieldsWithContactFieldType.Add(Windows.ApplicationModel.Contacts.ContactFieldType.Email);
```

## Запуск окна выбора контактов

```cs
Contact contact = await contactPicker.PickContactAsync();
```

Если вы хотите, чтобы пользователь выбрал один или несколько контактов, используйте [**pickContactsAsync**](https://msdn.microsoft.com/library/windows/apps/BR224913-pickcontactsasync).

```cs
public IList&lt;Contact&gt; contacts;
contacts = await contactPicker.PickContactsAsync();
```

## Обработка контактов

Когда окно выбора возвращает данные, проверьте, выбрал ли пользователь какие-либо контакты. Если да, обработайте сведения о контактах.

В данном примере показано, как обработать один контакт. Здесь мы извлекаем имя контакта и копируем его в элемент управления [**TextBlock**](https://msdn.microsoft.com/library/windows/apps/BR209652) с именем *OutputName*.

```cs
if (contact != null)
{
    OutputName.Text = contact.DisplayName;
}
else
{
    rootPage.NotifyUser(&quot;No contact was selected.&quot;, NotifyType.ErrorMessage);
}
```

В этом примере показано, как обработать несколько контактов.

```cs
if (contacts != null &amp;&amp; contacts.Count &gt; 0)
{
    foreach (Contact contact in contacts)
    {
        // Do something with the contact information.
    }
}
```

## Полный пример (один контакт)

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

private void AppendContactFieldValues&lt;T&gt;(TextBlock content, IList&lt;T&gt; fields)
{
    if (fields.Count &gt; 0)
    {
        StringBuilder output = new StringBuilder();

        if (fields[0].GetType() == typeof(ContactEmail))
        {
            foreach (ContactEmail email in fields as IList&lt;ContactEmail&gt;)
            {
                output.AppendFormat(&quot;Email: {0} ({1})\n&quot;, email.Address, email.Kind);
            }
        }
        else if (fields[0].GetType() == typeof(ContactPhone))
        {
            foreach (ContactPhone phone in fields as IList&lt;ContactPhone&gt;)
            {
                output.AppendFormat(&quot;Phone: {0} ({1})\n&quot;, phone.Number, phone.Kind);
            }
        }
        else if (fields[0].GetType() == typeof(ContactAddress))
        {
            List&lt;String&gt; addressParts = null;
            string unstructuredAddress = &quot;&quot;;

            foreach (ContactAddress address in fields as IList&lt;ContactAddress&gt;)
            {
                addressParts = (new List&lt;string&gt; { address.StreetAddress, address.Locality, address.Region, address.PostalCode });
                unstructuredAddress = string.Join(&quot;, &quot;, addressParts.FindAll(s =&gt; !string.IsNullOrEmpty(s)));
                output.AppendFormat(&quot;Address: {0} ({1})\n&quot;, unstructuredAddress, address.Kind);
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

## Полный пример (несколько контактов)

В данном примере окно выбора контактов получает несколько контактов, а затем добавляет контакты в элемент управления [**ListView**](https://msdn.microsoft.com/library/windows/apps/BR242878) с именем `OutputContacts`.

```cs
MainPage rootPage = MainPage.Current;
public IList&lt;Contact&gt; contacts;

private async void PickContactsButton-Click(object sender, RoutedEventArgs e)
{
    var contactPicker = new Windows.ApplicationModel.Contacts.ContactPicker();
    contactPicker.CommitButtonText = &quot;Select&quot;;
    contacts = await contactPicker.PickContactsAsync();

    // Clear the ListView.
    OutputContacts.Items.Clear();

    if (contacts.Count &gt; 0)
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
        if (contact.Emails.Count &gt; 0)
        {
            SecondaryText = contact.Emails[0].Address;
        }
        else if (contact.Phones.Count &gt; 0)
        {
            SecondaryText = contact.Phones[0].Number;
        }
        else if (contact.Addresses.Count &gt; 0)
        {
            List&lt;string&gt; addressParts = (new List&lt;string&gt; { contact.Addresses[0].StreetAddress, 
              contact.Addresses[0].Locality, contact.Addresses[0].Region, contact.Addresses[0].PostalCode });
              string unstructuredAddress = string.Join(&quot;, &quot;, addressParts.FindAll(s =&gt; !string.IsNullOrEmpty(s)));
            SecondaryText = unstructuredAddress;
        }
    }
}
```

## Сводка и дальнейшие действия

Теперь вы имеете представление об использовании окна выбора контактов для получения контактных данных. Скачайте [Примеры универсальных приложений для Windows](http://go.microsoft.com/fwlink/p/?linkid=619979) из GitHub, чтобы ознакомиться с другими примерами использования контактов и окна выбора контактов.



<!--HONumber=Mar16_HO1-->


