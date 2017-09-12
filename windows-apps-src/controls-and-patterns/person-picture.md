---
author: mijacobs
description: 
title: "Элемент управления &quot;Аватар пользователя&quot;"
template: detail.hbs
label: Parallax View
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp
pm-contact: kevjey
design-contact: kimsea
dev-contact: kefodero
doc-status: Published
ms.openlocfilehash: c71fdf3d19c8c8e43666620df53258825c4a8eb1
ms.sourcegitcommit: 10d6736a0827fe813c3c6e8d26d67b20ff110f6c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2017
---
# <a name="person-picture-control"></a>Элемент управления "Аватар пользователя"

> [!IMPORTANT]
> В этой статье описана еще не выпущенная функция, которая может быть существенно изменена до коммерческого выпуска. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.

Элемент управления аватаром пользователя отображает аватар для пользователя, если он доступен; в противном случае отображаются инициалы лица или универсальный глиф. Элемент управления можно использовать для отображения [объекта Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact), который управляет контактными данными пользователя, или можно вручную указать контактные данные, такие как отображаемое имя и изображение профиля.  

> **Важные API-интерфейсы**: [класс PersonPicture](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.personpicture), [класс Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact), [класс ContactManager](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.ContactManager)

![Элемент управления "Аватар пользователя"](images/person-picture/person-picture_hero.png)

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте аватар, когда необходимо представить пользователя и его контактные данные. Вот несколько примеров того, как вы можете использовать элемент управления:
* Для отображения текущего пользователя
* Для отображения контактов в адресной книге
* Для отображения отправителя сообщения 
* Для отображения контакта в социальных сетях

На рисунке показан элемент управления аватаром в списке контактов: ![средство управления аватарами](images/person-picture/person-picture-control.png)

## <a name="how-to-use-the-person-picture-control"></a>Использование элемента управления аватаром пользователя

Чтобы создать аватар, используется класс PersonPicture. В этом примере создается элемент управления PersonPicture и вручную предоставляется имя пользователя, аватар и инициалы:

```xaml
<Page
    x:Class="App2.ExamplePage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:App2"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <StackPanel Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
        <PersonPicture
            DisplayName="Betsy Sherman"
            ProfilePicture="Assets\BetsyShermanProfile.png"
            Initials="BS" />
    </StackPanel>
</Page>
```

## <a name="using-the-person-picture-control-to-display-a-contact-object"></a>Использование элемента управления аватаром пользователя для отображения объекта Contact

Элемент управления выбором пользователя можно использовать для отображения объекта [Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact): 

```xaml
<Page
    x:Class="SampleApp.PersonPictureContactExample"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:SampleApp"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d">

    <StackPanel Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

        <PersonPicture
            Contact="{x:Bind CurrentContact, Mode=OneWay}" />
            
        <Button Click="LoadContactButton_Click">Load contact</Button>
    </StackPanel>
</Page>
```

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Runtime.InteropServices.WindowsRuntime;
using Windows.Foundation;
using Windows.Foundation.Collections;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Controls.Primitives;
using Windows.UI.Xaml.Data;
using Windows.UI.Xaml.Input;
using Windows.UI.Xaml.Media;
using Windows.UI.Xaml.Navigation;
using Windows.ApplicationModel.Contacts;

namespace SampleApp
{
    public sealed partial class PersonPictureContactExample : Page, System.ComponentModel.INotifyPropertyChanged
    {
        public PersonPictureContactExample()
        {
            this.InitializeComponent();
        }

        private Windows.ApplicationModel.Contacts.Contact _currentContact; 
        public Windows.ApplicationModel.Contacts.Contact CurrentContact
        {
            get => _currentContact;
            set
            {
                _currentContact = value;
                PropertyChanged?.Invoke(this,
                    new System.ComponentModel.PropertyChangedEventArgs(nameof(CurrentContact)));
            }

        }
        public event System.ComponentModel.PropertyChangedEventHandler PropertyChanged;

        public static async System.Threading.Tasks.Task<Windows.ApplicationModel.Contacts.Contact> CreateContact()
        {

            var contact = new Windows.ApplicationModel.Contacts.Contact();
            contact.FirstName = "Betsy";
            contact.LastName = "Sherman";

            // Get the app folder where the images are stored.
            var appInstalledFolder = 
                Windows.ApplicationModel.Package.Current.InstalledLocation;
            var assets = await appInstalledFolder.GetFolderAsync("Assets");
            var imageFile = await assets.GetFileAsync("betsy.png");
            contact.SourceDisplayPicture = imageFile;

            return contact;
        }

        private async void LoadContactButton_Click(object sender, RoutedEventArgs e)
        {
            CurrentContact = await CreateContact();
        }
    }
}
```

> [!NOTE]
> Чтобы упростить код, в этом примере создается новый объект Contact. В реальном приложении вы бы позволили пользователю выбрать контакт или использовали бы [ContactManager](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.ContactManager) для запроса списка контактов. Подробнее о получении и управлении контактами см. в [статьях о календаре и контактах](../contacts-and-calendar/index.md). 

## <a name="determining-which-info-to-display"></a>Определение сведений для отображения

Когда вы предоставляете объект [Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact), элемент управления аватарами оценивает его, чтобы определить, какие сведения могут отображаться. 

Если изображение доступно, элемент управления отображает первое изображение, которое находит, в следующем порядке:

1.    LargeDisplayPicture
2.    SmallDisplayPicture
3.    Thumbnail

Вы можете изменить выбираемое изображение, установив для свойства PreferSmallImage значение true. Это придает SmallDisplayPicture более высокий приоритет, чем LargeDisplayPicture.

Если изображение не существует, элемент управления отображает имя или инициалы контакта. Если нет данных об имени, этот элемент управления выводит контактные данные, например, электронный адрес или номер телефона. 

## <a name="related-articles"></a>Еще по теме

* [Контакты и календарь](../contacts-and-calendar/index.md)
* [Пример карт контакта](http://go.microsoft.com/fwlink/p/?LinkId=624040)




