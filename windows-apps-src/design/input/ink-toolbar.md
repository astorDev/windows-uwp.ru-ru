---
Description: Добавьте InkToolbar по умолчанию в приложение универсальной платформы Windows (UWP) для рукописного ввода, добавьте кнопку специального пера в InkToolbar и привяжите кнопку специального пера к определению специального пера.
title: Добавление InkToolbar в приложение универсальной платформы Windows (UWP)
label: Add an InkToolbar to a Universal Windows Platform (UWP) app
template: detail.hbs
keywords: Windows Ink, рукописный ввод в Windows, DirectInk, InkPresenter, InkCanvas, InkToolbar, универсальная платформа Windows, UWP, взаимодействие с пользователем, ввод
ms.date: 02/08/2017
ms.topic: article
ms.assetid: d888f75f-c2a0-4134-81db-907b5e24fcc5
ms.localizationpriority: medium
ms.openlocfilehash: 8ae67e5d4d6da3cc9716c5f0efd276023bae9af0
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258373"
---
# <a name="add-an-inktoolbar-to-a-universal-windows-platform-uwp-app"></a>Добавление InkToolbar в приложение универсальной платформы Windows (UWP)



В приложениях универсальной платформы Windows (UWP) рукописный ввод контролируется с помощью двух разных элементов управления: [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) и [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar).

Элемент управления [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) предоставляет базовую функциональность Windows Ink. Используйте его для отрисовки ввода с помощью пера в качестве росчерка пера (с параметрами цвета и толщины по умолчанию) или росчерка стирания.

> Подробные сведения о реализации элемента управления InkCanvas см. в разделе [Взаимодействие с помощью пера в приложениях UWP](pen-and-stylus-interactions.md).

Элемент управления InkCanvas представляет собой полностью прозрачное наложение и не предоставляет никакого встроенного пользовательского интерфейса для настройки свойств росчерка пера. Изменить стандартные настройки рукописного ввода, предоставить пользователям возможность настраивать свойства росчерка пера и обеспечить поддержку других специальных функций рукописного ввода можно двумя способами.

- В коде программной части с помощью объекта [**InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter), привязанного к элементу управления InkCanvas.

  API InkPresenter предоставляют широкие возможности пользовательской настройки рукописного ввода. Подробные сведения см. в разделе [Взаимодействие с помощью пера в приложениях UWP](pen-and-stylus-interactions.md).

- Привяжите элемент управления [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) к элементу InkCanvas. По умолчанию InkToolbar предоставляет настраиваемую и расширяемую коллекцию кнопок для активации функций рукописного ввода и настройки параметров ввода, таких как размер росчерка, цвет чернил и форма кончика пера.

  В этом разделе мы рассмотрим элемент управления InkToolbar.

> **Важные API-интерфейсы**: [**класс InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas), [**класс InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar), [**класс InkPresenter**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkpresenter), [**Windows.UI.Input.Inking**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking)

## <a name="default-inktoolbar"></a>InkToolbar по умолчанию

По умолчанию элемент управления [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) содержит кнопки для рисования, стирания, выделения и отображения линейки или транспортира. В зависимости от функции во всплывающем элементе могут отображаться другие параметры и команды, например цвет чернил, толщина росчерка и стирание всего написанного.

![InkToolbar](./images/ink/ink-tools-invoked-toolbar-small.png)  
*Панель инструментов рукописного ввода Windows по умолчанию*

Чтобы добавить [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar) по умолчанию в приложение рукописного ввода, просто поместите его на одной странице с вашим [**InkCanvas**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inkcanvas) и свяжите эти два элемента управления.

1. В файле MainPage.xaml объявите объект-контейнер (в этом примере мы используем элемент управления «сетка») для поверхности рукописного ввода.
2. Объявите объект InkCanvas в качестве дочернего элемента контейнера. (Размер элемента управления InkCanvas наследуется от контейнера.)
3. Объявите элемент управления InkToolbar и используйте атрибут TargetInkCanvas, чтобы привязать этот элемент к InkCanvas.

> [!NOTE]
> Убедитесь, что элемент управления InkToolbar объявлен после InkCanvas. В противном случае наложение InkCanvas сделает InkToolbar недоступным.

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
        <TextBlock x:Name="Header"
                   Text="Basic ink sample"
                   Style="{ThemeResource HeaderTextBlockStyle}"
                   Margin="10,0,0,0" />
    </StackPanel>
    <Grid Grid.Row="1">
        <Image Source="Assets\StoreLogo.png" />
        <InkCanvas x:Name="inkCanvas" />
        <InkToolbar x:Name="inkToolbar"
          VerticalAlignment="Top"
          TargetInkCanvas="{x:Bind inkCanvas}" />
    </Grid>
</Grid>
```

## <a name="basic-customization"></a>Базовая настройка

В этом разделе мы рассмотрим некоторые базовые сценарии пользовательской настройки панели инструментов Windows Ink.

### <a name="specify-location-and-orientation"></a>Определение места и ориентации

При добавлении панели инструментов рукописного ввода в приложение можно принять расположение по умолчанию и ориентацию панели инструментов или задать их в соответствии с требованиями приложения или пользователя.

**XAML**

Явным образом задайте место и ориентацию панели инструментов, используя свойства [VerticalAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.VerticalAlignment), [HorizontalAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.HorizontalAlignment) и [Orientation](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar?branch=rs3.Orientation).

| По умолчанию | Задано явным образом |
| --- | --- |
| ![Расположение и ориентация панели инструментов рукописного ввода по умолчанию](./images/ink/location-default-small.png) | ![Расположение и ориентация панели инструментов рукописного ввода, заданные явно](./images/ink/location-explicit-small.png) |
| *Расположение и ориентация по умолчанию на панели инструментов рукописного ввода Windows* | *Панель инструментов рукописного ввода Windows — явное расположение и ориентация* |

Следующий код используется, чтобы явным образом задать расположение и ориентацию панели инструментов рукописного ввода в XAML.
```xaml
<InkToolbar x:Name="inkToolbar" 
    VerticalAlignment="Center" 
    HorizontalAlignment="Right" 
    Orientation="Vertical" 
    TargetInkCanvas="{x:Bind inkCanvas}" />
```

**Инициализация на основе настроек пользователя или состояния устройства**

В некоторых случаях может потребоваться настроить расположение и ориентацию панели инструментов рукописного ввода в зависимости от предпочтений пользователя или состояния устройства. В следующем примере показано, как настроить расположение и ориентацию панели инструментов рукописного ввода на основе параметров письма левой или правой рукой. Для этого перейдите в раздел **Параметры > Устройства > Перо и Windows Ink > Перо > Выберите, какой рукой вы пишете**.

![главными настройками](./images/ink/location-handedness-setting.png)  
*Параметр доминирующего руки*

Вы можете отправить запрос этому параметру через свойство HandPreference пространства имен Windows.UI.ViewManagement и задать [HorizontalAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.HorizontalAlignment) в соответствии с возвращенным значением. В этом примере мы размещаем панель инструментов в левой части приложения для пользователя-левши и справа — для пользователя-правши.

**Загрузить этот пример на [панели инструментов рукописного ввода пример расположения и ориентации (базовый)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-toolbar-handedness.zip)**

```csharp
public MainPage()
{
    this.InitializeComponent();

    Windows.UI.ViewManagement.UISettings settings = 
        new Windows.UI.ViewManagement.UISettings();
    HorizontalAlignment alignment = 
        (settings.HandPreference == 
            Windows.UI.ViewManagement.HandPreference.LeftHanded) ? 
            HorizontalAlignment.Left : HorizontalAlignment.Right;
    inkToolbar.HorizontalAlignment = alignment;
}
```

**Динамически корректировать состояние пользователя или устройства**

Также можно использовать привязку для отслеживания обновлений пользовательского интерфейса на основе изменений, вносимых в предпочтения пользователя, параметры устройства или состояния устройства. В следующем примере мы подробнее рассмотрим предыдущий пример и покажем, как динамически расположить панель инструментов рукописного ввода в зависимости от ориентации устройства с помощью привязки, объекта ViewMOdel и интерфейса [INotifyPropertyChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.inotifypropertychanged). 

**Загрузить этот пример на [панели инструментов рукописного ввода пример расположения и ориентации (динамический)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-toolbar-handedness-dynamic.zip)**

1. Сначала давайте добавим объект ViewModel.
    1. Добавьте новую папку в проект и назовите ее **ViewModels**.
    1. Добавьте новый класс в папку ViewModels (в этом примере мы называли его **InkToolbarSnippetHostViewModel.cs**).
        > [!NOTE] 
        > Мы использовали [шаблон Singleton](https://docs.microsoft.com/previous-versions/msp-n-p/ff650849(v=pandp.10)), так как в течение жизненного цикла приложения нам потребуется только один объект этого типа.

    1. Добавьте в файл пространство имен `using System.ComponentModel`.
    1. Добавьте статическую переменную-член с именем **instance** и статическое нередактируемое свойство с именем **Instance**. Сделайте конструктор закрытым, чтобы обеспечить возможность доступа к этому классу только через свойство Instance.   
        > [!NOTE] 
        > Этот класс наследует из интерфейса [INotifyPropertyChanged](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.inotifypropertychanged), который используется для уведомления клиентов (как правило, связывающих клиентов) о том, что значение свойства изменилось. Мы будем использовать его для обработки изменений ориентации устройства (мы подробнее рассмотрим этот код далее).  

        ```csharp
        using System.ComponentModel;

        namespace locationandorientation.ViewModels
        {
            public class InkToolbarSnippetHostViewModel : INotifyPropertyChanged
            {
                private static InkToolbarSnippetHostViewModel instance;
                
                public static InkToolbarSnippetHostViewModel Instance
                {
                    get
                    {
                        if (null == instance)
                        {
                            instance = new InkToolbarSnippetHostViewModel();
                        }
                        return instance;
                    }
                }
            }

            private InkToolbarSnippetHostViewModel() { }
        }
        ```

    1. Добавьте два логических свойства в класс InkToolbarSnippetHostViewModel: свойство **LeftHandedLayout** (функционал аналогичен предыдущему примеру только на основе XAML) и свойство **PortraitLayout** (ориентация устройства).
        >[!NOTE] 
        > Свойство PortraitLayout является настраиваемым и содержит определение события [PropertyChanged](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.data.inotifypropertychanged.PropertyChanged).

        ```csharp
        public bool LeftHandedLayout
        {
            get
            {
                bool leftHandedLayout = false;
                Windows.UI.ViewManagement.UISettings settings =
                    new Windows.UI.ViewManagement.UISettings();
                leftHandedLayout = (settings.HandPreference ==
                    Windows.UI.ViewManagement.HandPreference.LeftHanded);
                return leftHandedLayout;
            }
        }

        public bool portraitLayout = false;
        public bool PortraitLayout
        {
            get
            {
                Windows.UI.ViewManagement.ApplicationViewOrientation winOrientation = 
                    Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
                portraitLayout = 
                    (winOrientation == 
                        Windows.UI.ViewManagement.ApplicationViewOrientation.Portrait);
                return portraitLayout;
            }
            set
            {
                if (value.Equals(portraitLayout)) return;
                portraitLayout = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("PortraitLayout"));
            }
        }
        ```

1. Теперь добавим несколько классов преобразователя в наш проект. Каждый класс содержит объект Convert, который возвращает значение выравнивания ([HorizontalAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.horizontalalignment) или [VerticalAlignment](https://docs.microsoft.com/uwp/api/windows.ui.xaml.verticalalignment)).
    1. Добавьте новую папку в проект и назовите ее **Converters**.
    1. Добавьте два новых класса в папку Converters (в этом примере мы назвали их **HorizontalAlignmentFromHandednessConverter.cs** и **VerticalAlignmentFromAppViewConverter.cs**).
    1. Добавьте пространства имен `using Windows.UI.Xaml` и `using Windows.UI.Xaml.Data` в каждый файл.
    1. Задайте для каждого класса значение `public` и укажите, что он реализует интерфейс [IValueConverter](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.ivalueconverter).
    1. Добавьте методы [Convert](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.data.ivalueconverter.convert) и [ConvertBack](https://docs.microsoft.com/en-us/uwp/api/windows.ui.xaml.data.ivalueconverter.convertback) в каждый файл, как показано здесь (метод ConvertBack оставлен нереализованным).
        - Класс HorizontalAlignmentFromHandednessConverter размещает панель инструментов рукописного ввода в правой части приложения для пользователей-правшей и в левой части приложения — для пользователей-левшей.
        ```csharp
        using System;

        using Windows.UI.Xaml;
        using Windows.UI.Xaml.Data;

        namespace locationandorientation.Converters
        {
            public class HorizontalAlignmentFromHandednessConverter : IValueConverter
            {
                public object Convert(object value, Type targetType,
                    object parameter, string language)
                {
                    bool leftHanded = (bool)value;
                    HorizontalAlignment alignment = HorizontalAlignment.Right;
                    if (leftHanded)
                    {
                        alignment = HorizontalAlignment.Left;
                    }
                    return alignment;
                }

                public object ConvertBack(object value, Type targetType,
                    object parameter, string language)
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

        - Класс VerticalAlignmentFromAppViewConverter размещает панель инструментов рукописного ввода в центре приложения в книжной ориентации и в верхней части приложения в альбомной ориентации (несмотря на то, что в первую очередь нужно учитывать удобство использования, в этом примере представлен произвольный выбор в целях демонстрации).
        ```csharp
        using System;

        using Windows.UI.Xaml;
        using Windows.UI.Xaml.Data;

        namespace locationandorientation.Converters
        {
            public class VerticalAlignmentFromAppViewConverter : IValueConverter
            {
                public object Convert(object value, Type targetType,
                    object parameter, string language)
                {
                    bool portraitOrientation = (bool)value;
                    VerticalAlignment alignment = VerticalAlignment.Top;
                    if (portraitOrientation)
                    {
                        alignment = VerticalAlignment.Center;
                    }
                    return alignment;
                }

                public object ConvertBack(object value, Type targetType,
                    object parameter, string language)
                {
                    throw new NotImplementedException();
                }
            }
        }
        ```

1. Теперь откройте файл MainPage.xaml.cs.
    1. Добавьте `using using locationandorientation.ViewModels` в список пространств имен, чтобы связать ViewModel.
    1. Добавьте `using Windows.UI.ViewManagement` в список пространств имен, чтобы включить прослушивание изменений ориентации устройства.
    1. Добавьте код [виндовсизечанжедевенсандлер](https://docs.microsoft.com/uwp/api/windows.ui.xaml.windowsizechangedeventhandler) .
    1. Установите [DataContext](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement.DataContext) для представления в одноэлементный экземпляр класса инктулбарсниппесоствиевмодел. 
    ```csharp
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Controls;

    using locationandorientation.ViewModels;
    using Windows.UI.ViewManagement;

    namespace locationandorientation
    {
        public sealed partial class MainPage : Page
        {
            public MainPage()
            {
                this.InitializeComponent();

                Window.Current.SizeChanged += (sender, args) =>
                {
                    ApplicationView currentView = ApplicationView.GetForCurrentView();

                    if (currentView.Orientation == ApplicationViewOrientation.Landscape)
                    {
                        InkToolbarSnippetHostViewModel.Instance.PortraitLayout = false;
                    }
                    else if (currentView.Orientation == ApplicationViewOrientation.Portrait)
                    {
                        InkToolbarSnippetHostViewModel.Instance.PortraitLayout = true;
                    }
                };

                DataContext = InkToolbarSnippetHostViewModel.Instance;
            }
        }
    }
    ```

1. Затем откройте файл MainPage. XAML.
    1. Добавьте `xmlns:converters="using:locationandorientation.Converters"` в элемент `Page` для привязки к нашим преобразователям.
        ```xaml
        <Page
        x:Class="locationandorientation.MainPage"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:local="using:locationandorientation"
        xmlns:converters="using:locationandorientation.Converters"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        mc:Ignorable="d">
        ```

    1. Добавьте элемент `PageResources` и укажите ссылки на наши преобразователи.
        ```xaml
        <Page.Resources>
            <converters:HorizontalAlignmentFromHandednessConverter x:Key="HorizontalAlignmentConverter"/>
            <converters:VerticalAlignmentFromAppViewConverter x:Key="VerticalAlignmentConverter"/>
        </Page.Resources>
        ```

    1. Добавьте элементы InkCanvas и Инктулбар и привяжите свойства VerticalAlignment и HorizontalAlignment к Инктулбар.
        ```xaml
        <InkCanvas x:Name="inkCanvas" />
        <InkToolbar x:Name="inkToolbar" 
                    VerticalAlignment="{Binding PortraitLayout, Converter={StaticResource VerticalAlignmentConverter} }" 
                    HorizontalAlignment="{Binding LeftHandedLayout, Converter={StaticResource HorizontalAlignmentConverter} }" 
                    Orientation="Vertical" 
                    TargetInkCanvas="{x:Bind inkCanvas}" />
        ```

1. Вернитесь к файлу InkToolbarSnippetHostViewModel.cs, чтобы добавить свойства `PortraitLayout` и `LeftHandedLayout` bool в класс `InkToolbarSnippetHostViewModel`, а также поддержку повторной привязки `PortraitLayout` при изменении значения свойства. 
    ```csharp
    public bool LeftHandedLayout
    {
        get
        {
            bool leftHandedLayout = false;
            Windows.UI.ViewManagement.UISettings settings =
                new Windows.UI.ViewManagement.UISettings();
            leftHandedLayout = (settings.HandPreference ==
                Windows.UI.ViewManagement.HandPreference.LeftHanded);
            return leftHandedLayout;
        }
    }

    public bool portraitLayout = false;
    public bool PortraitLayout
    {
        get
        {
            Windows.UI.ViewManagement.ApplicationViewOrientation winOrientation = 
                Windows.UI.ViewManagement.ApplicationView.GetForCurrentView().Orientation;
            portraitLayout = 
                (winOrientation == 
                    Windows.UI.ViewManagement.ApplicationViewOrientation.Portrait);
            return portraitLayout;
        }
        set
        {
            if (value.Equals(portraitLayout)) return;
            portraitLayout = value;
            PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("PortraitLayout"));
        }
    }

    #region INotifyPropertyChanged Members

    public event PropertyChangedEventHandler PropertyChanged;

    protected void OnPropertyChanged(string property)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(property));
    }

    #endregion
    ```

Теперь у вас должно быть приложение для рукописного ввода, которое адаптируется как к основному пользователю и динамически реагирует на ориентацию устройства пользователя.

### <a name="specify-the-selected-button"></a>Укажите выделенную кнопку  
![кнопка карандаша, выбранная при инициализации](./images/ink/ink-tools-default-toolbar.png)  
*Панель инструментов рукописного ввода Windows с кнопкой карандаша, выбранная при инициализации*

По умолчанию первая (или крайняя левая) кнопка выбрана, когда запускается приложение и инициируется панель инструментов. На панели инструментов Windows Ink по умолчанию это кнопка шариковой ручки.

Поскольку порядок встроенных кнопок определяется платформой, первая кнопка не обязательно связана с пером или инструментом, который требуется активировать по умолчанию.

Можно переопределить это поведение по умолчанию и указать выбранную кнопку на панели инструментов.

В этом примере мы инициализируем панель инструментов по умолчанию с выбранной кнопкой карандаша и активированным карандашом (вместо шариковой ручки).

1. Используйте объявление XAML для элементов управления InkToolbar и InkCanvas из предыдущего примера.
2. В коде программной части настройте обработчик для события [Loaded](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.loaded) объекта [InkToolbar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar).

  ```csharp
  /// <summary>
  /// An empty page that can be used on its own or navigated to within a Frame.
  /// Here, we set up InkToolbar event listeners.
  /// </summary>
  public MainPage_CodeBehind()
  {
      this.InitializeComponent();
      // Add handlers for InkToolbar events.
      inkToolbar.Loaded += inkToolbar_Loaded;
  }
  ```

3. В обработчике события [Loaded](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.loaded) выполните следующие действия:
    1. Получите ссылку на встроенную кнопку [InkToolbarPencilButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpencilbutton).

    При передаче объекта [InkToolbarTool.Pencil](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbartool) в метод [GetToolButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar.gettoolbutton) возвращается объект [InkToolbarToolButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbartoolbutton) для элемента [InkToolbarPencilButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpencilbutton).

    2. Настройте [ActiveTool](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar.activetool) для объекта, возвращенного в предыдущем шаге.

```CSharp
/// <summary>
/// Handle the Loaded event of the InkToolbar.
/// By default, the active tool is set to the first tool on the toolbar.
/// Here, we set the active tool to the pencil button.
/// </summary>
/// <param name="sender"></param>
/// <param name="e"></param>
private void inkToolbar_Loaded(object sender, RoutedEventArgs e)
{
    InkToolbarToolButton pencilButton = inkToolbar.GetToolButton(InkToolbarTool.Pencil);
    inkToolbar.ActiveTool = pencilButton;
}
```

### <a name="specify-the-built-in-buttons"></a>Определение встроенных кнопок

![конкретные кнопки, включаемые при инициализации](./images/ink/ink-tools-specific.png)  
*Конкретные кнопки, включаемые при инициализации*

Как уже упоминалось, панель инструментов Windows Ink содержит коллекцию встроенных кнопок по умолчанию. Эти кнопки отображаются в следующем порядке (слева направо):

- [инктулбарбаллпоинтпенбуттон](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarballpointpenbutton)
- [инктулбарпенЦилбуттон](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpencilbutton)
- [инктулбархигхлигхтербуттон](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarhighlighterbutton)
- [инктулбарерасербуттон](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbareraserbutton)
- [инктулбаррулербуттон](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarrulerbutton)

В этом примере мы инициализируем панель инструментов только с встроенными кнопками шариковой ручки, карандаша и ластика.

Это можно сделать с помощью XAML или кода программной части.

**XAML**

Измените объявление XAML для элементов управления InkCanvas и InkToolbar из первого примера.
- Добавьте атрибут [InitialControls](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar.initialcontrols) и сделайте его значение равным [None](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarinitialcontrols). Коллекция встроенных кнопок по умолчанию будет очищена.
- Добавьте кнопки InkToolbar, необходимые в вашем приложении. Здесь мы добавим только кнопки [InkToolbarBallpointPenButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarballpointpenbutton), [InkToolbarPencilButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpencilbutton) и [InkToolbarEraserButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbareraserbutton).
> [!NOTE]
> Кнопки добавляются на панель инструментов в порядке, определяемом платформой, а не в указанном здесь порядке.

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
        <TextBlock x:Name="Header"
                   Text="Basic ink sample"
                   Style="{ThemeResource HeaderTextBlockStyle}"
                   Margin="10,0,0,0" />
    </StackPanel>
    <Grid Grid.Row="1">
        <Image Source="Assets\StoreLogo.png" />
        <!-- Clear the default InkToolbar buttons by setting InitialControls to None. -->
        <!-- Set the active tool to the pencil button. -->
        <InkCanvas x:Name="inkCanvas" />
        <InkToolbar x:Name="inkToolbar"
                    VerticalAlignment="Top"
                    TargetInkCanvas="{x:Bind inkCanvas}"
                    InitialControls="None">
            <!--
             Add only the ballpoint pen, pencil, and eraser.
             Note that the buttons are added to the toolbar in the order
             defined by the framework, not the order we specify here.
            -->
            <InkToolbarEraserButton />
            <InkToolbarBallpointPenButton />
            <InkToolbarPencilButton/>
        </InkToolbar>
    </Grid>
</Grid>
```

**Поддерживающий код**
1. Используйте объявление XAML для элементов управления InkCanvas и InkToolbar из первого примера.

  ```xaml
  <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
      <Grid.RowDefinitions>
          <RowDefinition Height="Auto"/>
          <RowDefinition Height="*"/>
      </Grid.RowDefinitions>
      <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
          <TextBlock x:Name="Header"
                     Text="Basic ink sample"
                     Style="{ThemeResource HeaderTextBlockStyle}"
                     Margin="10,0,0,0" />
      </StackPanel>
      <Grid Grid.Row="1">
          <Image Source="Assets\StoreLogo.png" />
          <InkCanvas x:Name="inkCanvas" />
          <InkToolbar x:Name="inkToolbar"
          VerticalAlignment="Top"
          TargetInkCanvas="{x:Bind inkCanvas}" />
      </Grid>
  </Grid>
  ```

2. В коде программной части настройте обработчик для события [Loading](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.loading) объекта [InkToolbar](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar).

  ```csharp
  /// <summary>
  /// An empty page that can be used on its own or navigated to within a Frame.
  /// Here, we set up InkToolbar event listeners.
  /// </summary>
  public MainPage_CodeBehind()
  {
      this.InitializeComponent();
      // Add handlers for InkToolbar events.
      inkToolbar.Loading += inkToolbar_Loading;
  }
  ```

3. Задайте для параметра [InitialControls](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar.initialcontrols) значение [None](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarinitialcontrols).
4. Создайте ссылки на объекты для кнопок, которые требуются вашему приложению. Здесь мы добавим только кнопки [InkToolbarBallpointPenButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarballpointpenbutton), [InkToolbarPencilButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpencilbutton) и [InkToolbarEraserButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbareraserbutton).
  > [!NOTE]
  > Кнопки добавляются на панель инструментов в порядке, определяемом платформой, а не в указанном здесь порядке.

5. [Добавьте](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobjectcollection.add) кнопки в элемент управления InkToolbar.

  ```csharp
  /// <summary>
  /// Handles the Loading event of the InkToolbar.
  /// Here, we identify the buttons to include on the InkToolbar.
  /// </summary>
  /// <param name="sender">The InkToolbar</param>
  /// <param name="args">The InkToolbar event data.
  /// If there is no event data, this parameter is null</param>
  private void inkToolbar_Loading(FrameworkElement sender, object args)
  {
      // Clear all built-in buttons from the InkToolbar.
      inkToolbar.InitialControls = InkToolbarInitialControls.None;

      // Add only the ballpoint pen, pencil, and eraser.
      // Note that the buttons are added to the toolbar in the order
      // defined by the framework, not the order we specify here.
      InkToolbarBallpointPenButton ballpoint = new InkToolbarBallpointPenButton();
      InkToolbarPencilButton pencil = new InkToolbarPencilButton();
      InkToolbarEraserButton eraser = new InkToolbarEraserButton();
      inkToolbar.Children.Add(eraser);
      inkToolbar.Children.Add(ballpoint);
      inkToolbar.Children.Add(pencil);
  }
  ```

<!--
### Support touch input
By default, the InkToolbar supports both pen and mouse input, you have to enable support for touch input.
-->

## <a name="custom-buttons-and-inking-features"></a>Специальные кнопки и функции рукописного ввода

Можно настроить и расширить коллекцию кнопок (и связанных с ними функций рукописного ввода), предоставляемую элементом управления InkToolbar.

В элементе управления InkToolbar представлены две группы кнопок разных типов.

1. Группа кнопок-инструментов, которая содержит встроенные кнопки для рисования, стирания и выделения. Сюда добавляются специальные перья и инструменты.
> **Обратите внимание** ,&nbsp;&nbsp;выбор компонентов является взаимоисключающим.

2. Группа кнопок-переключателей, которая содержит встроенную кнопку линейки. Сюда добавляются специальные переключатели.
> **Обратите внимание** ,&nbsp;&nbsp;функции не являются взаимоисключающими и могут использоваться одновременно с другими активными инструментами.

В зависимости от используемого приложения и требуемой функции рукописного ввода в элемент управления InkToolbar можно добавить любые нижеперечисленные кнопки (связанные со специальными функциями пера).

- Специальное перо — перо, для которого соответствующее приложение определяет цветовую палитру и свойства кончика пера, такие как форма, поворот и размер.
- Специальный инструмент — отличный от пера инструмент, который определяется соответствующим приложением.
- Специальный переключатель — включает или выключает определенную приложением функцию. Если функция включена, она действует вместе с активным инструментом.

> **Обратите внимание** ,&nbsp;&nbsp;нельзя изменить порядок показа встроенных кнопок. Они отображаются в следующем порядке: шариковая ручка, карандаш, маркер, ластик и линейка. Специальные перья добавляются после последнего пера по умолчанию, кнопки специальных инструментов располагаются между кнопкой последнего пера и кнопкой ластика, а кнопки специальных переключателей — после кнопки линейки (специальные кнопки добавляются в порядке настройки).

### <a name="custom-pen"></a>Специальное перо

Вы можете создать специальное перо (активируется с помощью кнопки специального пера), для которого вы определите цветовую палитру чернил и свойства кончика пера, такие как форму, поворот и размер.

![пользовательской каллиграфической кнопки пера](./images/ink/ink-tools-custompen.png)  
*Пользовательская кнопка пера*

В этом примере мы определяем специальное перо с широким кончиком, с помощью которого можно делать основные каллиграфические росчерки пера. Кроме того, мы настроим коллекцию кистей в палитре, которая отображается во всплывающем элементе кнопки.

**Поддерживающий код**

Сначала мы определим специальное перо и зададим атрибуты рисования в коде программной части. Впоследствии мы будем ссылаться на это специальное перо из XAML.

1. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите команду «Добавить» > «Новый элемент».
2. В разделе Visual C# -> «Код» добавьте новый файл класса и назовите его CalligraphicPen.cs.
3. В этом файле Calligraphic.cs замените блок использования по умолчанию следующим кодом:
```csharp
using System.Numerics;
using Windows.UI;
using Windows.UI.Input.Inking;
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Media;
```

4. Укажите, что класс CalligraphicPen наследуется от [InkToolbarCustomPen](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarcustompen).
```csharp
class CalligraphicPen : InkToolbarCustomPen
{
}
```

5. Переопределите [CreateInkDrawingAttributesCore](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarcustompen.createinkdrawingattributescore), чтобы задать собственную кисть и нужный размер росчерка.
```csharp
class CalligraphicPen : InkToolbarCustomPen
{
    protected override InkDrawingAttributes
      CreateInkDrawingAttributesCore(Brush brush, double strokeWidth)
    {
    }
}
```

6. Создайте объект [InkDrawingAttributes](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkdrawingattributes) и настройте [форму наконечника пера](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkdrawingattributes.pentip), [поворот кончика](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkdrawingattributes.pentiptransform), [размер росчерка](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkdrawingattributes.size) и [цвет чернил](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkdrawingattributes.color).
```csharp
class CalligraphicPen : InkToolbarCustomPen
{
    protected override InkDrawingAttributes
      CreateInkDrawingAttributesCore(Brush brush, double strokeWidth)
    {
        InkDrawingAttributes inkDrawingAttributes =
          new InkDrawingAttributes();
        inkDrawingAttributes.PenTip = PenTipShape.Circle;
        inkDrawingAttributes.Size =
          new Windows.Foundation.Size(strokeWidth, strokeWidth * 20);
        SolidColorBrush solidColorBrush = brush as SolidColorBrush;
        if (solidColorBrush != null)
        {
            inkDrawingAttributes.Color = solidColorBrush.Color;
        }
        else
        {
            inkDrawingAttributes.Color = Colors.Black;
        }

        Matrix3x2 matrix = Matrix3x2.CreateRotation(45);
        inkDrawingAttributes.PenTipTransform = matrix;

        return inkDrawingAttributes;
    }
}
```

**XAML**

Затем добавим необходимые ссылки на специальное перо в файле MainPage.xaml.

1. Мы объявим словарь ресурсов локальной страницы, который создает ссылку на специальное перо (`CalligraphicPen`), определенное в файле CalligraphicPen.cs, и [коллекцию кистей](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.BrushCollection), поддерживаемых специальным пером (`CalligraphicPenPalette`).
```xaml
<Page.Resources>
    <!-- Add the custom CalligraphicPen to the page resources. -->
    <local:CalligraphicPen x:Key="CalligraphicPen" />
    <!-- Specify the colors for the palette of the custom pen. -->
    <BrushCollection x:Key="CalligraphicPenPalette">
        <SolidColorBrush Color="Blue" />
        <SolidColorBrush Color="Red" />
    </BrushCollection>
</Page.Resources>
```

2. Затем добавим элемент управления InkToolbar с дочерним элементом управления [InkToolbarCustomPenButton](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarcustompenbutton).

  Кнопка специального пера содержит две статические ссылки на ресурсы, объявленные в ресурсах страницы: `CalligraphicPen` и `CalligraphicPenPalette`.

  Кроме того, зададим диапазон для ползунка, регулирующего размер штриха ([MinStrokeWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpenbutton.minstrokewidth), [MaxStrokeWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpenbutton.maxstrokewidth) и [SelectedStrokeWidth](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpenbutton.selectedstrokewidthproperty)), выбранной кисти ([SelectedBrushIndex](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbarpenbutton.selectedbrushindex)) и значка для кнопки специального пера ([SymbolIcon](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.symbolicon)).
```xaml
<Grid Grid.Row="1">
    <InkCanvas x:Name="inkCanvas" />
    <InkToolbar x:Name="inkToolbar"
                VerticalAlignment="Top"
                TargetInkCanvas="{x:Bind inkCanvas}">
        <InkToolbarCustomPenButton
            CustomPen="{StaticResource CalligraphicPen}"
            Palette="{StaticResource CalligraphicPenPalette}"
            MinStrokeWidth="1" MaxStrokeWidth="3" SelectedStrokeWidth="2"
            SelectedBrushIndex ="1">
            <SymbolIcon Symbol="Favorite" />
            <InkToolbarCustomPenButton.ConfigurationContent>
                <InkToolbarPenConfigurationControl />
            </InkToolbarCustomPenButton.ConfigurationContent>
        </InkToolbarCustomPenButton>
    </InkToolbar>
</Grid>
```

### <a name="custom-toggle"></a>Специальный переключатель

Вы можете создать специальный переключатель (активируется с помощью кнопки специального переключателя), чтобы включать или отключать определяемую приложением функцию. Если функция включена, она действует вместе с активным инструментом.

В этом примере мы определяем специальный переключатель, включающий рукописный ввод с помощью сенсорного ввода (по умолчанию рукописный сенсорный ввод не включен).

> [!NOTE]  
> Если вам нужно поддерживать рукописный сенсорный ввод, рекомендуется включить его, используя CustomToggleButton со значком и подсказкой, указанными в этом примере.

Как правило, сенсорный ввод используется для непосредственного взаимодействия с неким объектом или пользовательским интерфейсом приложения. Чтобы продемонстрировать различия в поведении, когда рукописный сенсорный ввод включен, мы разместим InkCanvas в контейнере ScrollViewer и зададим такой размер ScrollViewer, чтобы он был меньше InkCanvas. 

При запуске приложения поддерживается только рукописный ввод с помощью пера, а сенсорный ввод используется для сдвига или масштабирования поверхности рукописного ввода. Когда рукописный сенсорный ввод включен, поверхность рукописного ввода невозможно сдвигать или масштабировать посредством сенсорного ввода.

> [!NOTE]
> См. раздел [Элементы управления рукописным вводом](../controls-and-patterns/inking-controls.md) для ознакомления с рекомендациями по взаимодействию с пользователем и для [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas), и для [**InkToolbar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkToolbar) . Для этого примера подойдут следующие рекомендации.
> - Элемент управления [**InkToolbar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkToolbar) (и рукописный ввод в целом) удобнее и эффективнее всего использовать с активным пером, однако если этого требует ваше приложение, можно включить поддержку рукописного ввода с помощью мыши и касания. 
> - Если реализуется поддержка рукописного ввода с помощью касаний, рекомендуется использовать значок "ED5F" шрифта Segoe MLD2 Assets для кнопки переключения с подсказкой «Письмо касанием». 

**XAML**

1. Сначала мы объявляем элемент [**InkToolbarCustomToggleButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkToolbarCustomToggleButton) (toggleButton) с прослушивателем событий Click, который определяет обработчик событий (Toggle_Custom).

```xaml 
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>

    <StackPanel Grid.Row="0" 
                x:Name="HeaderPanel" 
                Orientation="Horizontal">
        <TextBlock x:Name="Header" 
                   Text="Basic ink sample" 
                   Style="{ThemeResource HeaderTextBlockStyle}" 
                   Margin="10" />
    </StackPanel>

    <ScrollViewer Grid.Row="1" 
                  HorizontalScrollBarVisibility="Auto" 
                  VerticalScrollBarVisibility="Auto">
        
        <Grid HorizontalAlignment="Left" VerticalAlignment="Top">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            
            <InkToolbar Grid.Row="0" 
                        Margin="10"
                        x:Name="inkToolbar" 
                        VerticalAlignment="Top"
                        TargetInkCanvas="{x:Bind inkCanvas}">
                <InkToolbarCustomToggleButton 
                x:Name="toggleButton" 
                Click="CustomToggle_Click" 
                ToolTipService.ToolTip="Touch Writing">
                    <SymbolIcon Symbol="{x:Bind TouchWritingIcon}"/>
                </InkToolbarCustomToggleButton>
            </InkToolbar>
            
            <ScrollViewer Grid.Row="1" 
                          Height="500"
                          Width="500"
                          x:Name="scrollViewer" 
                          ZoomMode="Enabled" 
                          MinZoomFactor=".1" 
                          VerticalScrollMode="Enabled" 
                          VerticalScrollBarVisibility="Auto" 
                          HorizontalScrollMode="Enabled" 
                          HorizontalScrollBarVisibility="Auto">
                
                <Grid x:Name="outputGrid" 
                      Height="1000"
                      Width="1000"
                      Background="{ThemeResource SystemControlBackgroundChromeWhiteBrush}">
                    <InkCanvas x:Name="inkCanvas"/>
                </Grid>
                
            </ScrollViewer>
        </Grid>
    </ScrollViewer>
</Grid>
```

**Поддерживающий код**

2. В предыдущем фрагменте кода мы объявили прослушиватель событий Click и обработчик (Toggle_Custom) для специальной кнопки переключения для сенсорного рукописного ввода (toggleButton). Этот обработчик просто переключает поддержку CoreInputDeviceTypes.Touch с помощью свойства InputDeviceTypes объекта InkPresenter.

   Мы также задаем значок для кнопки с помощью элемента SymbolIcon и расширения разметки {x: Bind}, которая привязывает его к полю, заданному в файле кода программной части (TouchWritingIcon).

   Следующий фрагмент кода включает в себя как обработчик событий Click, так и определение TouchWritingIcon.

```csharp 
namespace Ink_Basic_InkToolbar
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage_AddCustomToggle : Page
    {
        Symbol TouchWritingIcon = (Symbol)0xED5F;

        public MainPage_AddCustomToggle()
        {
            this.InitializeComponent();
        }

        // Handler for the custom toggle button that enables touch inking.
        private void CustomToggle_Click(object sender, RoutedEventArgs e)
        {
            if (toggleButton.IsChecked == true)
            {
                inkCanvas.InkPresenter.InputDeviceTypes |= CoreInputDeviceTypes.Touch;
            }
            else
            {
                inkCanvas.InkPresenter.InputDeviceTypes &= ~CoreInputDeviceTypes.Touch;
            }
        }
    }
}
```

### <a name="custom-tool"></a>Специальный инструмент

Вы можете создать кнопку специального инструмента, чтобы вызывать отличный от пера инструмент, определяемый вашим приложением.

По умолчанию [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) обрабатывает все входные данные как росчерк пера или росчерк стирания. Это относится и к входным данным, которые модифицированы дополнительной возможностью оборудования, например кнопкой пера, правой кнопкой мыши или чем-то подобным. Тем не менее, [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) можно настроить на оставление определенных входных данных без обработки, чтобы затем их можно было передать в ваше приложение для специальной обработки.

В этом примере мы определяем кнопку специального инструмента, при активации которой последующие росчерки обрабатываются и отображаются как лассо выбора (пунктирная линия), а не как рукописный ввод. Всем росчеркам пера в пределах области выделения задается свойство [**Selected**](https://docs.microsoft.com/uwp/api/windows.ui.input.inking.inkstroke.selected).

> [!NOTE]
> См. раздел «Элементы управления рукописным вводом» для ознакомления с рекомендациями по взаимодействию с пользователем и для InkCanvas, и для InkToolbar. Для этого примера подойдет следующая рекомендация.
> - Если вы предоставляете возможность выделения жестом, рекомендуется использовать значок "EF20" из шрифта Segoe MLD2 Assets для кнопки этого средства и подсказку «Инструмент выделения». 
 
**XAML**

1. Во-первых, мы объявляем элемент [**InkToolbarCustomToolButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkToolbarCustomToolButton) (customToolButton) с прослушивателем событий Click, который определяет обработчик событий (customToolButton_Click), где настроено выделение росчерком. (Мы также добавили набор кнопок для копирования, вырезания и вставки выделения росчерком.)

2. Также мы добавляем элемент Canvas для рисования нашего росчерка выделения. Использование отдельного слоя для рисования росчерка выделения гарантирует, что [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) и его содержимое останется без изменений. 

```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <StackPanel x:Name="HeaderPanel" Orientation="Horizontal" Grid.Row="0">
        <TextBlock x:Name="Header" 
                   Text="Basic ink sample" 
                   Style="{ThemeResource HeaderTextBlockStyle}" 
                   Margin="10,0,0,0" />
    </StackPanel>
    <StackPanel x:Name="ToolPanel" Orientation="Horizontal" Grid.Row="1">
        <InkToolbar x:Name="inkToolbar" 
                    VerticalAlignment="Top" 
                    TargetInkCanvas="{x:Bind inkCanvas}">
            <InkToolbarCustomToolButton 
                x:Name="customToolButton" 
                Click="customToolButton_Click" 
                ToolTipService.ToolTip="Selection tool">
                <SymbolIcon Symbol="{x:Bind SelectIcon}"/>
            </InkToolbarCustomToolButton>
        </InkToolbar>
        <Button x:Name="cutButton" 
                Content="Cut" 
                Click="cutButton_Click"
                Width="100"
                Margin="5,0,0,0"/>
        <Button x:Name="copyButton" 
                Content="Copy"  
                Click="copyButton_Click"
                Width="100"
                Margin="5,0,0,0"/>
        <Button x:Name="pasteButton" 
                Content="Paste"  
                Click="pasteButton_Click"
                Width="100"
                Margin="5,0,0,0"/>
    </StackPanel>
    <Grid Grid.Row="2" x:Name="outputGrid" 
              Background="{ThemeResource SystemControlBackgroundChromeWhiteBrush}" 
              Height="Auto">
        <!-- Canvas for displaying selection UI. -->
        <Canvas x:Name="selectionCanvas"/>
        <!-- Canvas for displaying ink. -->
        <InkCanvas x:Name="inkCanvas" />
    </Grid>
</Grid>
```

**Поддерживающий код**

2. Затем мы обрабатываем событие Click для [**InkToolbarCustomToolButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkToolbarCustomToolButton) в файле кода программной части MainPage.xaml.cs.

   Этот обработчик настраивает [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) на передачу необработанных входных данных приложению. 

   Более подробное описание этого кода см. в подразделе «Передача входных данных для расширенной обработки» раздела [Взаимодействие с пером и Windows Ink в приложениях UWP](pen-and-stylus-interactions.md).

   Мы также задаем значок для кнопки с помощью элемента SymbolIcon и расширения разметки {x: Bind}, которая привязывает его к полю, заданному в файле кода программной части (SelectIcon).

   Следующий фрагмент кода включает в себя как обработчик событий Click, так и определение SelectIcon.

```csharp
namespace Ink_Basic_InkToolbar
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage_AddCustomTool : Page
    {
        // Icon for custom selection tool button.
        Symbol SelectIcon = (Symbol)0xEF20;

        // Stroke selection tool.
        private Polyline lasso;
        // Stroke selection area.
        private Rect boundingRect;

        public MainPage_AddCustomTool()
        {
            this.InitializeComponent();

            // Listen for new ink or erase strokes to clean up selection UI.
            inkCanvas.InkPresenter.StrokeInput.StrokeStarted +=
                StrokeInput_StrokeStarted;
            inkCanvas.InkPresenter.StrokesErased +=
                InkPresenter_StrokesErased;
        }

        private void customToolButton_Click(object sender, RoutedEventArgs e)
        {
            // By default, the InkPresenter processes input modified by 
            // a secondary affordance (pen barrel button, right mouse 
            // button, or similar) as ink.
            // To pass through modified input to the app for custom processing 
            // on the app UI thread instead of the background ink thread, set 
            // InputProcessingConfiguration.RightDragAction to LeaveUnprocessed.
            inkCanvas.InkPresenter.InputProcessingConfiguration.RightDragAction =
                InkInputRightDragAction.LeaveUnprocessed;

            // Listen for unprocessed pointer events from modified input.
            // The input is used to provide selection functionality.
            inkCanvas.InkPresenter.UnprocessedInput.PointerPressed +=
                UnprocessedInput_PointerPressed;
            inkCanvas.InkPresenter.UnprocessedInput.PointerMoved +=
                UnprocessedInput_PointerMoved;
            inkCanvas.InkPresenter.UnprocessedInput.PointerReleased +=
                UnprocessedInput_PointerReleased;
        }

        // Handle new ink or erase strokes to clean up selection UI.
        private void StrokeInput_StrokeStarted(
            InkStrokeInput sender, Windows.UI.Core.PointerEventArgs args)
        {
            ClearSelection();
        }

        private void InkPresenter_StrokesErased(
            InkPresenter sender, InkStrokesErasedEventArgs args)
        {
            ClearSelection();
        }

        private void cutButton_Click(object sender, RoutedEventArgs e)
        {
            inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
            inkCanvas.InkPresenter.StrokeContainer.DeleteSelected();
            ClearSelection();
        }

        private void copyButton_Click(object sender, RoutedEventArgs e)
        {
            inkCanvas.InkPresenter.StrokeContainer.CopySelectedToClipboard();
        }

        private void pasteButton_Click(object sender, RoutedEventArgs e)
        {
            if (inkCanvas.InkPresenter.StrokeContainer.CanPasteFromClipboard())
            {
                inkCanvas.InkPresenter.StrokeContainer.PasteFromClipboard(
                    new Point(0, 0));
            }
            else
            {
                // Cannot paste from clipboard.
            }
        }

        // Clean up selection UI.
        private void ClearSelection()
        {
            var strokes = inkCanvas.InkPresenter.StrokeContainer.GetStrokes();
            foreach (var stroke in strokes)
            {
                stroke.Selected = false;
            }
            ClearBoundingRect();
        }

        private void ClearBoundingRect()
        {
            if (selectionCanvas.Children.Any())
            {
                selectionCanvas.Children.Clear();
                boundingRect = Rect.Empty;
            }
        }

        // Handle unprocessed pointer events from modifed input.
        // The input is used to provide selection functionality.
        // Selection UI is drawn on a canvas under the InkCanvas.
        private void UnprocessedInput_PointerPressed(
            InkUnprocessedInput sender, PointerEventArgs args)
        {
            // Initialize a selection lasso.
            lasso = new Polyline()
            {
                Stroke = new SolidColorBrush(Windows.UI.Colors.Blue),
                StrokeThickness = 1,
                StrokeDashArray = new DoubleCollection() { 5, 2 },
            };

            lasso.Points.Add(args.CurrentPoint.RawPosition);

            selectionCanvas.Children.Add(lasso);
        }

        private void UnprocessedInput_PointerMoved(
            InkUnprocessedInput sender, PointerEventArgs args)
        {
            // Add a point to the lasso Polyline object.
            lasso.Points.Add(args.CurrentPoint.RawPosition);
        }

        private void UnprocessedInput_PointerReleased(
            InkUnprocessedInput sender, PointerEventArgs args)
        {
            // Add the final point to the Polyline object and 
            // select strokes within the lasso area.
            // Draw a bounding box on the selection canvas 
            // around the selected ink strokes.
            lasso.Points.Add(args.CurrentPoint.RawPosition);

            boundingRect =
                inkCanvas.InkPresenter.StrokeContainer.SelectWithPolyLine(
                    lasso.Points);

            DrawBoundingRect();
        }

        // Draw a bounding rectangle, on the selection canvas, encompassing 
        // all ink strokes within the lasso area.
        private void DrawBoundingRect()
        {
            // Clear all existing content from the selection canvas.
            selectionCanvas.Children.Clear();

            // Draw a bounding rectangle only if there are ink strokes 
            // within the lasso area.
            if (!((boundingRect.Width == 0) ||
                (boundingRect.Height == 0) ||
                boundingRect.IsEmpty))
            {
                var rectangle = new Rectangle()
                {
                    Stroke = new SolidColorBrush(Windows.UI.Colors.Blue),
                    StrokeThickness = 1,
                    StrokeDashArray = new DoubleCollection() { 5, 2 },
                    Width = boundingRect.Width,
                    Height = boundingRect.Height
                };

                Canvas.SetLeft(rectangle, boundingRect.X);
                Canvas.SetTop(rectangle, boundingRect.Y);

                selectionCanvas.Children.Add(rectangle);
            }
        }
    }
}
```



### <a name="custom-ink-rendering"></a>Пользовательская передача рукописного ввода

По умолчанию рукописные данные обрабатываются в фоновом потоке с низкой задержкой и отображаются как «влажные» в процессе ввода. После завершения росчерка (когда пользователь поднял перо или палец либо отпустил кнопку мыши) росчерк обрабатывается в потоке пользовательского интерфейса и отображается «сухим» на слое [**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) (над содержимым приложения, вместо влажных рукописных данных).

Платформа рукописного ввода позволяет переопределить это поведение и полностью настроить рукописный ввод за счет пользовательской сушки рукописных данных.

Дополнительные сведения о пользовательской сушке см. в разделе [Взаимодействие с пером и Windows Ink в приложениях UWP](https://docs.microsoft.com/windows/uwp/design/input/pen-and-stylus-interactions#custom-ink-rendering).

> [!NOTE]
> Пользовательская сушка и [**InkToolbar**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.inktoolbar)  
> Если ваше приложение переопределяет поведение по умолчанию для отрисовки рукописных данных объекта [**InkPresenter**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.Inking.InkPresenter) пользовательской реализацией сушки, отрисованные росчерки пера более недоступны для InkToolbar, а встроенные команды стирания InkToolbar не работают должным образом. Для реализации функции стирания необходимо обрабатывать все события указателя, выполнять проверку нажатия для каждого штриха и переопределить встроенную команду «Удалить все рукописные данные».

## <a name="related-articles"></a>Связанные статьи

- [Взаимодействие с помощью пера](pen-and-stylus-interactions.md)

### <a name="topic-samples"></a>Примеры в разделе

- [Пример расположения и ориентации панели инструментов для рукописного ввода (базовый)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-toolbar-handedness.zip)
- [Пример расположения и ориентации панели инструментов для рукописного ввода (динамический)](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-ink-toolbar-handedness-dynamic.zip)

### <a name="other-samples"></a>Другие примеры

- [Пример простого рукописногоC#вводаC++(/)](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/SimpleInk)
- [Образец сложных рукописныхC++данных ()](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/ComplexInk)
- [Образец рукописного ввода (JavaScript)](https://go.microsoft.com/fwlink/p/?LinkID=620308)
- [Руководство по началу работы. Поддержка рукописного ввода в приложении UWP](https://github.com/Microsoft/Windows-tutorials-inputs-and-devices/tree/master/GettingStarted-Ink)
- [Образец цветовой книги](https://github.com/Microsoft/Windows-appsample-coloringbook)
- [Образец заметок для семьи](https://github.com/Microsoft/Windows-appsample-familynotes)
