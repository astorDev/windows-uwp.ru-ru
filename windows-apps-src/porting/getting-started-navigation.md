---
title: Приступая к работе - навигация
description: Приступая к работе - навигация
ms.assetid: F4DF5C5F-C886-4483-BBDA-498C4E2C1BAF
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: dcbc8f6737c2b7450e42ed01a752087d6e9034c1
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74259149"
---
# <a name="getting-started-navigation"></a>Начало работы: навигация


## <a name="adding-navigation"></a>Добавление навигации

iOS предоставляет класс **UINavigationController**, чтобы помочь в создании навигации в приложении: вы можете использовать представления push и pop для создания иерархии **UIViewControllers**, определяющей ваше приложение.

В отличие от этого, приложение Windows 10, содержащее несколько представлений, занимает больше возможностей, чем подход к переходу на веб-сайт. Вы можете представить своих пользователей, которые переходят между «страницами» приложения по мере нажатия элементов управления. Подробнее см. в разделе [Основы проектирования навигации](https://docs.microsoft.com/windows/uwp/layout/navigation-basics).

Одним из способов управления этой навигацией в приложении Windows 10 является использование класса [**Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame) . Эти возможности описаны в указанном ниже пошаговом руководстве.

Продолжим работу с решением, которую вы начали ранее. Откройте файл **MainPage.xaml** и добавьте кнопку в **представление конструирования**. Задайте для свойства кнопки **Content** значение "Go To Page" вместо "Button". Затем создайте обработчик для события кнопки **Click**, как показано на следующем рисунке. Если вы не помните, как это делать, обратитесь к пошаговому руководству в предыдущем разделе (совет: дважды щелкните кнопку в **представлении конструирования**).

![Добавление кнопки и события нажатия в Visual Studio](images/ios-to-uwp/vs-go-to-page.png)

Давайте добавим новую страницу. В представлении **Решение** коснитесь меню **Проект**, а затем коснитесь элемента **Добавление нового элемента**. Выберите **Пустая страница**, как показано на следующем рисунке, и затем нажмите кнопку **Добавить**.

![Добавление новой страницы в Visual Studio](images/ios-to-uwp/vs-add-new-page.png)

Затем добавьте кнопку в файл BlankPage.xaml. Мы будем использовать элемент управления AppBarButton с изображением стрелки, указывающей назад: в представлении **XAML** добавьте ` <AppBarButton Icon="Back"/>` между элементами `<Grid> </Grid>`.

Теперь добавим обработчик событий к кнопке: дважды щелкните элемент управления в режиме **конструктора** , после чего Microsoft Visual Studio добавит текст «AppBarButton\_**щелкните»,** как показано на следующем рисунке, а затем добавляет и отображает соответствующий обработчик событий в файле BlankPage.XAML.cs.

![Добавление кнопки “Назад” и события нажатия в Visual Studio](images/ios-to-uwp/vs-add-back-button.png)

Если вы вернетесь к представлению **XAML** файла BlankPage.xaml, код XAML элемента `<AppBarButton>` будет выглядеть так:

` <AppBarButton Icon="Back" Click="AppBarButton_Click"/>`

Вернитесь в файл BlankPage.xaml.cs и добавьте следующий код для перехода на предыдущую страницу при нажатии кнопки пользователем.

```csharp
private void AppBarButton_Click(object sender, RoutedEventArgs e)
{
    // Add the following line of code.    
    Frame.GoBack();
}
```

В завершение откройте файл MainPage.xaml.cs и добавьте следующий код. Он открывает страницу BlankPage после нажатия кнопки пользователем.

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    // Add the following line of code.
    Frame.Navigate(typeof(BlankPage1));
}
```

Теперь запустите программу. Нажмите кнопку "Go To Page", чтобы перейти на другую страницу, а затем нажмите кнопку со стрелкой назад, чтобы вернуться на предыдущую страницу.

Для управления навигацией по страницам используется класс [**Frame**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Frame). Так как класс **уинавигатионконтроллер** в iOS использует методы **пушвиевконтроллер** и **попвиевконтроллер** , класс **Frame** для приложений UWP предоставляет методы [**navigate**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.navigate) и [**GoBack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.goback) . Класс **Frame** также содержит метод под названием [**GoForward**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.goforward), который выполняет то, что вы ожидаете.

В этом пошаговом руководстве при каждом переходе на страницу создается новый экземпляр BlankPage. (Предыдущий экземпляр будет автоматически освобожден (*высвобожден*). Если вы не хотите, чтобы при каждом переходе на страницу создавался новый экземпляр, добавьте этот код в конструктор класса BlankPage в файле BlankPage.xaml.cs. Он включает режим [**NavigationCacheMode**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page.navigationcachemode).

```csharp
public BlankPage()
{
    this.InitializeComponent();
    // Add the following line of code.
    this.NavigationCacheMode = Windows.UI.Xaml.Navigation.NavigationCacheMode.Enabled;
}
```

Для указания числа кэшируемых страниц в журнале навигации вы также можете получить или задать свойствоCacheSize[**класса**Frame](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.cachesize).

Подробнее о навигации см. в разделах [Навигация](https://docs.microsoft.com/windows/uwp/layout/navigation-basics) и [Пример анимаций персонализации на XAML](https://code.msdn.microsoft.com/windowsapps/Personality-Animations-3f857919).

**Примечание** .  сведения об навигации по приложениям UWP с помощью JavaScript и HTML см. в разделе [Краткое руководство. использование одностраничной навигации](https://docs.microsoft.com/previous-versions/windows/apps/hh452768(v=win.10)).
 
### <a name="next-step"></a>Дальнейшие действия

[Приступая к работе: анимация](getting-started-animation.md)

