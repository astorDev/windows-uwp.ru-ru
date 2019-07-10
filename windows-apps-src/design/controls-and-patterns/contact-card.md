---
Description: Кнопка предоставляет пользователю возможность вызвать немедленное действие.
title: Карточка контакта
ms.date: 03/07/2018
ms.topic: article
keywords: windows 10, uwp
pm-contact: kele
design-contact: tbd
dev-contact: tbd
doc-status: not-published
ms.localizationpriority: medium
ms.openlocfilehash: 4c227629ace1f3fdbb2af8582401f9273cf11c2e
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "63799646"
---
# <a name="contact-card"></a>Карточка контакта

Карточка контакта отображает контактные данные, такие как имя, номер телефона и адрес [Контакта](/uwp/api/Windows.ApplicationModel.Contacts.Contact) (механизм, который платформа UWP использует для представления пользователей и компаний).  Карточка контакта также позволяет пользователю редактировать контактные данные. Можно выбрать, какую карточку следует отобразить: компактную карточку контакта или полную карточку контакта, которая содержит дополнительные сведения.

> **Важные API**: [метод ShowContactCard](/uwp/api/windows.applicationmodel.contacts.contactmanager.showcontactcard), [метод ShowFullContactCard](/uwp/api/windows.applicationmodel.contacts.contactmanager.showfullcontactcard), [метод IsShowContactCardSupported](/uwp/api/windows.applicationmodel.contacts.contactmanager.IsShowContactCardSupported), [класс Contact](/uwp/api/Windows.ApplicationModel.Contacts.Contact)  

Существуют два способа отображения карточки контакта:  
* В качестве стандартной карточки контакта, которая отображается во всплывающем элементе, который исчезает, то есть карточка контакта исчезает, когда пользователь щелкает вне ее области. 
* В качестве полной карточки контакта, которая занимает больше пространства и не исчезает, то есть пользователь должен нажать кнопку **Закрыть**, чтобы она закрылась. 


<figure>
    <img src="images/contact-card/contact-card-standard.png" alt="The full contact card">
    <figcaption>Стандартная карточка контакта</figcaption>
</figure>

<figure>
    <img src="images/contact-card/contact-card-full.png" alt="The full contact card">
    <figcaption>Полная карточка контакта</figcaption>
</figure>


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте карточку контакта, если требуется отобразить контактные данные для контакта. Если вам необходимо отобразить только имя контакта и изображение, используйте [элемент управления аватаром пользователя](person-picture.md). 


<!-- TODO: Add examples back when the contact card has been added. -->

<!-- ## Examples

<table>
<th align="left">XAML Controls Gallery<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>If you have the <strong style="font-weight: semi-bold">XAML Controls Gallery</strong> app installed, click here to <a href="xamlcontrolsgallery:/item/Button">open the app and see the Button in action</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Get the XAML Controls Gallery app (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">Get the source code (GitHub)</a></li>
    </ul>
</td>
</tr>
</table> -->

## <a name="show-a-standard-contact-card"></a>Отображение стандартной карточки контакта

1. Как правило, карточка контакта отображается, когда пользователь щелкает какой-то элемент: кнопку или, возможно, [элемент управления аватаром пользователя](person-picture.md). Нам не нужно скрывать этот элемент. Чтобы он не скрывался, необходимо создать [Rect](/uwp/api/windows.foundation.rect), описывающий расположение и размер элемента. 

    Давайте создадим служебную функцию для этого. Мы используем ее позже.
    ```csharp
    // Gets the rectangle of the element 
    public static Rect GetElementRectHelper(FrameworkElement element) 
    { 
        // Passing "null" means set to root element. 
        GeneralTransform elementTransform = element.TransformToVisual(null); 
        Rect rect = elementTransform.TransformBounds(new Rect(0, 0, element.ActualWidth, element.ActualHeight)); 
        return rect; 
    } 

    ```

2. Определите, отображается ли карточка контакта путем вызова метода [ContactManager.IsShowContactCardSupported](/uwp/api/windows.applicationmodel.contacts.contactmanager.IsShowContactCardSupported). Если этот способ не поддерживается, отобразите сообщение об ошибке. (В этом примере предполагается, что карточка контакта будет отображаться в ответ на событие щелчка.)
    ```csharp
    // Contact and Contact Managers are existing classes 
    private void OnUserClickShowContactCard(object sender, RoutedEventArgs e) 
    { 
        if (ContactManager.IsShowContactCardSupported()) 
        { 

    ```

3. Используйте служебную функцию, которую вы создали на шаге 1, чтобы получить границы элемента управления, который активировал событие (чтобы не заслонить его карточкой контакта).

    ```csharp
            Rect selectionRect = GetElementRect((FrameworkElement)sender); 
    ```

4. Получите объект [Contact](//docs.microsoft.com/uwp/api/Windows.ApplicationModel.Contacts.Contact) для отображения. В этом примере создается простой контакт, но код должен извлекать фактический контакт. 

    ```csharp
                // Retrieve the contact to display
                var contact = new Contact(); 
                var email = new ContactEmail(); 
                email.Address = "jsmith@contoso.com"; 
                contact.Emails.Add(email); 
    ```
5. Отобразите карточку контакта, вызвав метод [ShowContactCard](/uwp/api/windows.applicationmodel.contacts.contactmanager.showcontactcard). 

    ```csharp
            ContactManager.ShowFullContactCard(
                contact, selectionRect, Placement.Default); 
        } 
    } 
    ```

Далее представлен полный код примера.

```csharp
// Gets the rectangle of the element 
public static Rect GetElementRect(FrameworkElement element) 
{ 
    // Passing "null" means set to root element. 
    GeneralTransform elementTransform = element.TransformToVisual(null); 
    Rect rect = elementTransform.TransformBounds(new Rect(0, 0, element.ActualWidth, element.ActualHeight)); 
    return rect; 
} 
 
// Display a contact in response to an event
private void OnUserClickShowContactCard(object sender, RoutedEventArgs e) 
{ 
    if (ContactManager.IsShowContactCardSupported()) 
    { 
        Rect selectionRect = GetElementRect((FrameworkElement)sender);

        // Retrieve the contact to display
        var contact = new Contact(); 
        var email = new ContactEmail(); 
        email.Address = "jsmith@contoso.com"; 
        contact.Emails.Add(email); 
    
        ContactManager.ShowContactCard(
            contact, selectionRect, Placement.Default); 
    } 
} 

```

## <a name="show-a-full-contact-card"></a>Отображение полной карточки контакта

Чтобы отобразить полную карточку контакта, вызовите метод [ShowFullContactCard](/uwp/api/windows.applicationmodel.contacts.contactmanager.showfullcontactcard) вместо метода [ShowContactCard](/uwp/api/windows.applicationmodel.contacts.contactmanager.showcontactcard).

```csharp
private void onUserClickShowContactCard() 
{ 
   
    Contact contact = new Contact(); 
    ContactEmail email = new ContactEmail(); 
    email.Address = "jsmith@hotmail.com"; 
    contact.Emails.Add(email); 
 
 
    // Setting up contact options.     
    FullContactCardOptions fullContactCardOptions = new FullContactCardOptions(); 
 
    // Display full contact card on mouse click.   
    // Launch the People’s App with full contact card  
    fullContactCardOptions.DesiredRemainingView = ViewSizePreference.UseLess; 
     
 
    // Shows the full contact card by launching the People App. 
    ContactManager.ShowFullContactCard(contact, fullContactCardOptions); 
} 

```

## <a name="retrieving-real-contacts"></a>Извлечение "настоящих" контактов

В примерах в этой статье выполняется создание простого контакта. В настоящем приложении вам, вероятно, потребуется извлечь существующий контакт. Инструкции и приведены в статье [Контакты и календарь](/windows/uwp/contacts-and-calendar/).




## <a name="related-articles"></a>Связанные статьи
- [Контакты и календарь](/windows/uwp/contacts-and-calendar/)
- [Пример с карточками контактов](https://go.microsoft.com/fwlink/p/?LinkId=624040)
- [Элемент управления "Аватар пользователя"](/windows/uwp/controls-and-patterns/person-picture/)
