---
author: mijacobs
description: Отображает аватар для пользователя, если он доступен; в противном случае отображаются инициалы лица или универсальный глиф.
title: Элемент управления "Аватар пользователя"
template: detail.hbs
label: Parallax View
ms.author: mijacobs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: trestar
design-contact: kimsea
dev-contact: kefodero
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: eadc0763e7f99930bee3d2a388a881c52e89f609
ms.sourcegitcommit: e814a13978f33654d8e995584f4b047cb53e0aef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2018
ms.locfileid: "6052269"
---
# <a name="person-picture-control"></a>Элемент управления "Аватар пользователя"

Элемент управления аватаром пользователя отображает аватар для пользователя, если он доступен; в противном случае отображаются инициалы лица или универсальный глиф. Элемент управления можно использовать для отображения [объекта Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact), который управляет контактными данными пользователя, или можно вручную указать контактные данные, такие как отображаемое имя и изображение профиля.  

> **Важные API-интерфейсы**: [класс PersonPicture](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.personpicture), [класс Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact), [класс ContactManager](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.ContactManager)

На этом рисунке показаны два элемента управления аватаром пользователя вместе с двумя элементами [блоков текста](text-block.md), в которых отображаются имена пользователей. 
![Элемент управления "Аватар пользователя"](images/person-picture/person-picture_hero.png)


## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Используйте аватар, когда необходимо представить пользователя и его контактные данные. Вот несколько примеров того, как вы можете использовать элемент управления:
* Для отображения текущего пользователя
* Для отображения контактов в адресной книге
* Для отображения отправителя сообщения 
* Для отображения контакта в социальных сетях

На рисунке показан элемент управления аватаром в списке контактов: ![средство управления аватарами](images/person-picture/person-picture-control.png)

## <a name="examples"></a>Примеры.

<table>
<th align="left">Галерея элементов управления XAML<th>
<tr>
<td><img src="images/xaml-controls-gallery-sm.png" alt="XAML controls gallery"></img></td>
<td>
    <p>Если у вас установлено приложение <strong style="font-weight: semi-bold">галереи элементов управления XAML</strong>, щелкните здесь, чтобы <a href="xamlcontrolsgallery:/item/PersonPicture">открыть приложение и увидеть PersonPicture в действии</a>.</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">Скачать приложение галереи элементов управления XAML (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics">Получить исходный код (GitHub)</a></li>
    </ul>
</td>
</tr>
</table>

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
> Чтобы упростить код, в этом примере создается новый объект Contact. В реальном приложении вы бы позволили пользователю выбрать контакт или использовали бы [ContactManager](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.ContactManager) для запроса списка контактов. Подробнее о получении и управлении контактами см. в [статьях о календаре и контактах](../../contacts-and-calendar/index.md). 

## <a name="determining-which-info-to-display"></a>Определение сведений для отображения

Когда вы предоставляете объект [Contact](https://docs.microsoft.com/en-us/uwp/api/Windows.ApplicationModel.Contacts.Contact), элемент управления аватарами оценивает его, чтобы определить, какие сведения могут отображаться. 

Если изображение доступно, элемент управления отображает первое изображение, которое находит, в следующем порядке:

1. LargeDisplayPicture
1. SmallDisplayPicture
1. Thumbnail

Вы можете изменить выбираемое изображение, установив для свойства PreferSmallImage значение true. Это придает SmallDisplayPicture более высокий приоритет, чем LargeDisplayPicture.

Если изображение не существует, элемент управления отображает имя или инициалы контакта. Если нет данных об имени, этот элемент управления выводит контактные данные, например, электронный адрес или номер телефона. 

## <a name="get-the-sample-code"></a>Получить пример кода

- [Образец галереи элементов управления XAML](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlUIBasics) — ознакомьтесь со всеми элементами управления XAML в интерактивном формате.

## <a name="related-articles"></a>Еще по теме

* [Контакты и календарь](../../contacts-and-calendar/index.md)
* [Пример карт контакта](http://go.microsoft.com/fwlink/p/?LinkId=624040)
