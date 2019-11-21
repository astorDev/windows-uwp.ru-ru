---
Description: Вы можете включить голосовые функции в ваши приложения с помощью средств работы с голосовыми командами, распознавания и синтеза речи в Кортане.
title: Взаимодействие с Surface Dial
label: Surface Dial interactions
template: detail.hbs
keywords: Surface Dial, колесо Windows, RadialController, радиальный контроллер, взаимодействие с пользователем, ввод
ms.date: 02/08/2017
ms.topic: article
ms.assetid: e7deb1d6-feeb-471e-9a83-26386d1aaf37
ms.localizationpriority: medium
ms.openlocfilehash: 9d6647b25f6f9f5015ca31ae75a869731ccc1fe3
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258311"
---
# <a name="surface-dial-interactions"></a>Взаимодействие с Surface Dial

![изображение Surface номеронабирателя с помощью Surface Studio](images/windows-wheel/dial-pen-studio-600px.png)  
*Surface Dial с Surface Studio и пером* (доступно для покупки в [Microsoft Store](https://www.microsoft.com/store/d/Surface-Dial/925R551SKTGN?icid=Surface_Accessories_ModB_Surface_Dial_103116)).

## <a name="overview"></a>Обзор

Такие устройства Windows Wheel, как Surface Dial, — это новые категории устройств ввода, предоставляющие удобные и уникальные возможности взаимодействия с Windows и приложениями для Windows. 

> [!IMPORTANT]
> В этом разделе мы рассмотрим использование Surface Dial, но эта информация применима ко всем устройствам Windows Wheel. 

| Видео |   |
| --- | --- |
| <iframe src="https://www.youtube-nocookie.com/embed/WMklcdzcNcU" width="300" height="200" allowFullScreen="true" frameBorder="0"></iframe> | <iframe src="https://channel9.msdn.com/Blogs/One-Dev-Minute/Programming-the-Microsoft-Surface-Dial/player" width="300" height="200" allowFullScreen="true" frameBorder="0"></iframe> |
| *Партнеры по приложениям для набора абонентов* | *Поверхность набора для разработчики* |

Surface Dial служит вспомогательным многомодальным устройством ввода, дополняющим основное устройство, с помощью *вращательных* действий (или жестов). В большинстве случаев пользователь вращает устройство недоминантной рукой, выполняя задачу доминантной рукой (например, используя перо). Оно не предназначено для точного ввода, как сенсорный экран, перо или мышь. 

Surface Dial также поддерживает действие *нажатия и удерживания* и *щелчок*. Нажатие и удерживание служит одной цели — отображение меню команд. Если меню активно, поворот и щелчок обрабатывается в меню. В противном случае данные ввода передаются в приложение для обработки. 

**Как и для всех устройств ввода Windows, вы можете настроить и адаптировать взаимодействие с абонентской настройкой в соответствии с функциональными возможностями в приложениях.**

> [!TIP]
> При совместном использовании Surface Dial и новое устройство Studio Surface могут расширить возможности взаимодействия с пользователем.  
>
>В дополнению к действию нажатия и удерживания по умолчанию, описанному выше, Surface Dial можно также разместить прямо на экране Surface Studio. Это позволяет реализовать специальное меню "на экране". 
>
>Определяя расположение контакта и границы Surface Dial, система использует эти сведения для обработки перекрытия устройством и отображает более крупную версию меню вокруг Surface Dial. Эти же сведения также могут использоваться приложением для адаптации пользовательского интерфейса, если устройство присутствует или предполагается, что оно будет использоваться, например из-за положения руки.

| Меню Surface Dial вне экрана | | Экранное меню Surface Dial |
| --- | --- | --- |
| ![Меню Surface Dial вне экрана](images/windows-wheel/surface-dial-menu-offscreen.png) | | ![Экранное меню Surface Dial](images/windows-wheel/surface-dial-menu-onscreen.png) |

## <a name="system-integration"></a>Системная интеграция

Устройство Surface Dial тесно интегрировано с Windows и поддерживает набор встроенных средств в меню: системную громкость, прокрутку, масштабирование, отмена и повтор.

Этот набор встроенных средств адаптируется к текущему контексту системы, добавляя следующие инструменты:
- элемент управления яркостью системы, когда пользователь находится на рабочем столе Windows;
- средство перехода к предыдущей или следующей дорожке при воспроизведении мультимедиа.

Помимо общей поддержки платформы, устройство Surface Dial также тесно интегрировано с элементами управления платформы Windows Ink ([**InkCanvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkCanvas) и [**InkToolbar**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.InkToolbar)).

Набор ![Surface с помощью пера Surface](images/windows-wheel/dial-and-pen-400px.png)  
*Поверхность набора с помощью пера Surface*

При использовании с Surface Dial эти элементы управления предоставляют дополнительные функции для изменения атрибутов рукописного ввода и управления трафаретом линейки на панели рукописного ввода.

Если открыть меню Dial Surface в приложении, использующем панель рукописного ввода, меню будет содержать инструменты для управления типом пера и толщиной кисти. Если линейка включена, соответствующее средство добавляется в меню, чтобы управлять положением и углом линейки.

![меню набора поверхностей с инструментом выбора пера для панели инструментов рукописного ввода Windows](images/windows-wheel/surface-dial-menu-inktoolbar-pen.png)  
*Меню "поверхность набора" с инструментом "Выбор пера" для панели инструментов рукописного ввода Windows*

![меню набора средств с размером штриха для панели инструментов рукописного ввода Windows](images/windows-wheel/surface-dial-menu-inktoolbar-strokesize.png)  
*Меню набора поверхностей с инструментом «размер штриха» для панели инструментов Windows Ink*

![меню набора поверхностей с помощью инструмента "линейка" для панели инструментов рукописного ввода Windows](images/windows-wheel/surface-dial-menu-inktoolbar-ruler.png)  
*Меню "поверхность набора" с инструментом "линейка" для панели инструментов рукописного ввода Windows*

## <a name="user-customization"></a>Пользовательская настройка

Пользователи могут настраивать некоторые взаимодействия с Dial на странице **Параметры Windows -> Устройство -> Колесико**, в том числе средства по умолчанию, вибрацию (или тактильную отдачу) и доминантную руку. 

При настройке Surface Dial следует убедиться, что выбранные функции и возможности доступны и включены пользователем.

## <a name="custom-tools"></a>Специальные инструменты

Здесь мы рассмотрим рекомендации для проектирования взаимодействия с пользователем и советы разработчикам по настройке инструментов в меню Surface Dial.

### <a name="ux-guidance-for-custom-tools"></a>Руководство по UX для пользовательских инструментов

**Убедитесь, что ваши инструменты соответствуют текущему контексту.** Если сразу понятно, для чего инструмент нужен и как использовать его с помощью используется, пользователи смогут быстро освоить его и сосредоточатся на своих задачах.

**Максимально Сократите количество инструментов приложения**  
В меню Surface Dial есть место для семи элементов. Если элементов восемь или больше, пользователь должен включить Dial, чтобы узнать, какие средства доступны во всплывающем элементе, что усложняет навигацию, обнаружение и выбор инструментов.

Мы рекомендуем предоставит один специальный инструмент для приложения или контекста. Таким образом вы сможете задать инструмент в зависимости от действий пользователя и без активации меню Surface Dial и выбора инструмента. 

**Динамическое обновление коллекции средств**  
Поскольку элементы меню Surface Dial не поддерживают отключенное состояние, вам следует динамически добавлять и удалять инструменты (включая встроенные средства по умолчанию) на основе контекста пользователя (текущего представления или окна с фокусом). Если инструмент не связан с текущим действием или не нужен, удалите его.

> [!IMPORTANT]
> При добавлении элемента в меню убедитесь, что элемент не существует.

**Не удалять встроенное средство настройки системного тома**  
Обычно элемент управления громкостью всегда нужен пользователям. Они могут слушать музыку при работе с вашим приложением, поэтому средства управления громкостью и воспроизведением всегда должны быть доступны в меню Surface Dial. (Средство включения следующей дорожки автоматически добавляется в меню при воспроизведении мультимедиа.)

**Соответствует организации меню**  
Это помогает пользователям узнать, какие средства доступны в вашем приложении, а также повышает их эффективность при переключении инструментов.

**Обеспечение высокого качества значков, совместимых со встроенными значками**  
Значки могут передать ваш профессионализм и стремление к совершенству, а также внушить пользователям доверие.
- Предоставьте высококачественные изображения в формате PNG размером 64 x 64 пикселя (минимальный поддерживаемый размер — 44 x 44)
- Убедитесь, что фон прозрачный
- Значок должны заполнять большую часть изображения
- У белого значка должен быть черный контур, чтобы он был виден в режиме высокой контрастности

|   |   |   |
| --- | --- | --- |
| ![Значок с альфа-фоном](images/windows-wheel/surface-dial-menu-icon1.png) | ![Значок, показанный в меню колесика со значком темы по умолчанию](images/windows-wheel/surface-dial-menu-icon2.png) | ![Экранное меню Surface Dial](images/windows-wheel/surface-dial-menu-icon3.png) |
| *Значок с фоном Alpha* | *Значок, отображаемый в меню "колесо" со схемой по умолчанию* | *Значок, отображаемый в меню "колесо" с высокая контрастность белой темой* |

**Использовать краткие и описательные имена**  
Имя инструмента отображается в меню вместе со значком и также используется программами чтения с экрана. 
- Имена должны быть короткими, чтобы помещаться в центральном круге меню колесика
- Имена должно четко обозначать основное действие (вспомогательного действия может подразумеваться):
  - прокрутка означает движение в обоих направлениях поворота;
  - отмена указывает основное действие, но повтор (вспомогательное действие) может подразумеваться и легко может быть обнаружен пользователем.

### <a name="developer-guidance"></a>Руководство для разработчиков

Вы можете настроить Surface Dial, чтобы дополнить функции ваших приложениях с помощью набора [API среды выполнения Windows](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialController). 

Как было сказано ранее, меню Surface Dial по умолчанию заполняется набором встроенных средств для широкого спектра базовых системных функций (громкость и яркость системы, прокрутка, масштабирование, отмена и управления мультимедиа, когда система обнаруживает воспроизведение аудио или видео). Однако эти средства по умолчанию могут не предоставлять возможности, необходимые для вашего приложения. 

В следующих разделах описано, как добавить специальный инструмент в меню Surface Dial и указать, какие встроенные средства доступны.

Скачайте более надежную версию этого примера из [настройки радиалконтроллер](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-radialcontroller-customization.zip).

**Добавление пользовательского инструмента**

В этом примере мы добавляем базовый специальный инструмент, который передает данные ввода (события поворота и щелчка) в некоторые элементы управления пользовательского интерфейса XAML.

1. Сначала мы объявим наш пользовательский интерфейс (ползунок и переключатель) в XAML.

   ![изображение пользовательского интерфейса примера приложения](images/windows-wheel/surface-dial-snippet-customtool1.png)  
   *Пользовательский интерфейс примера приложения*

    ```Xaml
    <Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
      <Grid.RowDefinitions>
        <RowDefinition Height="Auto"/>
        <RowDefinition Height="*"/>
      </Grid.RowDefinitions>
      <StackPanel x:Name="HeaderPanel" 
        Orientation="Horizontal" 
        Grid.Row="0">
          <TextBlock x:Name="Header"
            Text="RadialController customization sample"
            VerticalAlignment="Center"
            Style="{ThemeResource HeaderTextBlockStyle}"
            Margin="10,0,0,0" />
      </StackPanel>
      <StackPanel Orientation="Vertical" 
        VerticalAlignment="Center" 
        HorizontalAlignment="Center"
        Grid.Row="1">
          <!-- Slider for rotation input -->
          <Slider x:Name="RotationSlider"
            Width="300"
            HorizontalAlignment="Left"/>
          <!-- Switch for click input -->
          <ToggleSwitch x:Name="ButtonToggle"
            HorizontalAlignment="Left"/>
      </StackPanel>
    </Grid>
    ```

2. Затем в коде программной части мы добавим специальный инструмент в меню Surface Dial и объявим обработчики ввода [**RadialController**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialController). 

   Мы получим ссылку на объект [**RadialController**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialController) для Surface Dial (myController), вызвав метод [**CreateForCurrentView**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.createforcurrentview).

   Затем мы создадим экземпляр [**RadialControllerMenuItem**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerMenuItem) (myItem), вызвав метод [**RadialControllerMenuItem.CreateFromIcon**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollermenuitem.createfromicon). 

   Потом мы добавим этот элемент в коллекции элементов меню.

   Мы объявляем обработчики событий ввода ([**ButtonClicked**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.buttonclicked) и [**RotationChanged**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.rotationchanged)) для объекта [**RadialController**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialController).

   Наконец, мы определяем обработчики событий.

    ```csharp
    public sealed partial class MainPage : Page
    {
        RadialController myController;

        public MainPage()
        {
            this.InitializeComponent();
            // Create a reference to the RadialController.
            myController = RadialController.CreateForCurrentView();

            // Create an icon for the custom tool.
            RandomAccessStreamReference icon =
              RandomAccessStreamReference.CreateFromUri(
                new Uri("ms-appx:///Assets/StoreLogo.png"));

            // Create a menu item for the custom tool.
            RadialControllerMenuItem myItem =
              RadialControllerMenuItem.CreateFromIcon("Sample", icon);

            // Add the custom tool to the RadialController menu.
            myController.Menu.Items.Add(myItem);

            // Declare input handlers for the RadialController.
            myController.ButtonClicked += MyController_ButtonClicked;
            myController.RotationChanged += MyController_RotationChanged;
        }

        // Handler for rotation input from the RadialController.
        private void MyController_RotationChanged(RadialController sender,
          RadialControllerRotationChangedEventArgs args)
        {
            if (RotationSlider.Value + args.RotationDeltaInDegrees > 100)
            {
                RotationSlider.Value = 100;
                return;
            }
            else if (RotationSlider.Value + args.RotationDeltaInDegrees < 0)
            {
                RotationSlider.Value = 0;
                return;
            }
            RotationSlider.Value += args.RotationDeltaInDegrees;
        }

        // Handler for click input from the RadialController.
        private void MyController_ButtonClicked(RadialController sender,
          RadialControllerButtonClickedEventArgs args)
        {
            ButtonToggle.IsOn = !ButtonToggle.IsOn;
        }
    }
    ```

При запуске приложения мы используем Surface Dial для взаимодействия с ним. Сначала мы нажимаем и удерживаем его, чтобы открыть меню и выбрать специальный инструмент. После активации специального инструмента ползунок можно настроить, вращая Dial, а переключатель можно активировать, щелкнув Dial.

![изображение пользовательского интерфейса примера приложения, активированного с помощью настраиваемого инструмента для набора средств,](images/windows-wheel/surface-dial-snippet-customtool2.png)  
*Пользовательский интерфейс примера приложения, активированный с помощью настраиваемого инструмента набора для Surface*

**Указание встроенных средств**

Вы можете использовать класс [**RadialControllerConfiguration**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerConfiguration), чтобы настроить коллекцию встроенных элементов меню для вашего приложения.

Например, если в приложении нет областей прокрутки или масштабирования, а функции отмены и повтора не нужны, эти средства можно удалить из меню. Это даст место для собственных инструментов. 

> [!IMPORTANT] 
> В меню Surface Dial должен быть по крайней мере один элемент. Если перед добавлением одного из специальных инструментов удалить все инструменты по умолчанию, они будут восстановлены, а ваш инструмент будет добавлен в коллекцию по умолчанию.

Согласно рекомендациям по проектированию не следует удалять элементы управления мультимедиа (громкость и предыдущая или следующая дорожка), так как пользователи часто слушают музыку в фоновом режиме во время выполнения других задач.

Здесь мы покажем, как настроить меню Surface Dial, добавив в него элементы управления громкостью и перехода к следующей или предыдущей дорожке.

```csharp
public MainPage()
{
  ...
  //Remove a subset of the default system tools
  RadialControllerConfiguration myConfiguration = 
  RadialControllerConfiguration.GetForCurrentView();
  myConfiguration.SetDefaultMenuItems(new[] 
  {
    RadialControllerSystemMenuItemKind.Volume,
      RadialControllerSystemMenuItemKind.NextPreviousTrack
  });
}
```

## <a name="custom-interactions"></a>Пользовательские режимы взаимодействия

Как уже упоминалось, Surface Dial поддерживает три жеста (нажатие и удерживание, поворот, щелчок) с помощью соответствующих функций взаимодействия по умолчанию. 

Убедитесь, что все специальные процедуры взаимодействия, основанные на этих жестах, имеют смысл для выбранного действия или инструмента. 

> [!NOTE]
> Взаимодействие зависит от состояния меню Surface Dial. Если меню активно, оно обрабатывает данные ввода, в противном случае это делает приложение.

### <a name="press-and-hold"></a>Нажатие и удерживание

Этот жест активирует и отображает меню Surface Dial, с ним не связаны функции приложения. 

По умолчанию меню отображается в центре экрана пользователя. Однако пользователь может переместить его в любое место.

> [!NOTE]
> Если Surface Dial размещается на экране Surface Studio, меню выравнивается по центру экрана Surface Dial.

### <a name="rotate"></a>Поворот

Surface Dial используется, в основном, для поворота и плавного постепенного изменения аналоговых значений или элементов управления.

Устройство можно вращать по часовой стрелке и против часовой стрелки, а также давать тактильную обратную связь для указания расстояния.

> [!NOTE]
> Пользователь может отключить тактильную обратную связь на странице **Параметры Windows -> Устройства -> Колесико**.

#### <a name="ux-guidance-for-custom-interactions"></a>Руководство по UX для пользовательских взаимодействий

**Средства с непрерывным или высоким уровнем секрета должны отключать обратную связь хаптик**

Тактильная обратная связь соответствует вращательной чувствительности активного инструмента. Мы рекомендуем отключить тактильную обратную связь для инструментов с непрерывной или высокой вращательной чувствительностью, так как иначе пользователям можно быть неудобно. 

**Основная рука не должна влиять на взаимодействие на основе вращения**

Surface Dial не может определить, какая рука применяется, но пользователь может выбрать доминантную руку в разделе **Параметры Windows -> Устройства -> Перо и Windows Ink**.

**Языковой стандарт следует учитывать для всех взаимодействий вращения**

Порадуйте пользователей, адаптируя возможности взаимодействия к региональным стандартам, в том числе учитывая направление написания текста.

Встроенные средства и в меню Dial следуют этим рекомендациям для взаимодействия с помощью поворота:

|   |   |   |
| --- | --- | --- |
| Влево<br/>Вверх<br/>Из | ![Изображение Surface Dial](images/windows-wheel/surface-dial-rotate.png) | Вправо<br/>Вниз<br/>В |
|   |   |   |

| Концептуальное направление | Сопоставление с Surface Dial | Поворот по часовой стрелке | Поворот против часовой стрелки |
| --- | --- | --- | --- |
| По горизонтали | Сопоставление "влево" и "вправе" на основе верхней части Surface Dial | Вправо | Влево |
| По вертикали | Сопоставление "вверх" и "вниз" на основе левой части Surface Dial | Вниз | Вверх |
| Ось Z | Сопоставление направления "в" (или ближайшего) с направлением "вверх" и "вправо"<br/>Сопоставление направления "из" (или дальнего) с направлением "вниз" и "влево" | В | Из |

#### <a name="developer-guidance"></a>Руководство для разработчиков

Когда пользователь поворачивает устройство, события [**RadialController.RotationChanged**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.rotationchanged) инициируются в зависимости от разности ([**RadialControllerRotationChangedEventArgs.RotationDeltaInDegrees**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollerrotationchangedeventargs.rotationdeltaindegrees)) относительно оси вращения. Чувствительность (или разрешение) данных можно задать с помощью свойства [**RadialController.RotationResolutionInDegrees**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.rotationresolutionindegrees).

> [!NOTE]
> По умолчанию вращательное событие ввода предоставляется объекту [**RadialController**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialController), только если устройство повернуто как минимум на 10 градусов. Каждое событие ввода вызывает вибрацию устройства.

В целом мы рекомендуем отключить тактильную обратную связь, если разрешение поворота меньше 5 градусов. Это обеспечивает более плавное взаимодействие с пользователем. 

Вы можете включить и отключить тактильную отдачу для специальных инструментов с помощью свойства [**RadialController.UseAutomaticHapticFeedback**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.useautomatichapticfeedback).

> [!NOTE]
> Невозможно переопределить поведение тактильной отдачи системных средств, таких как элемент управления громкостью. Для них тактильную отдачу может отключить только пользователь на странице параметров колесика.

Вот пример настройки разрешения данных поворота, а также включения и выключения тактильной отдачи.

```csharp
private void MyController_ButtonClicked(RadialController sender, 
  RadialControllerButtonClickedEventArgs args)
{
  ButtonToggle.IsOn = !ButtonToggle.IsOn;

  if(ButtonToggle.IsOn)
  {
    //high resolution mode
    RotationSlider.LargeChange = 1;
    myController.UseAutomaticHapticFeedback = false;
    myController.RotationResolutionInDegrees = 1;
  }
  else
  {
    //low resolution mode
    RotationSlider.LargeChange = 10;
    myController.UseAutomaticHapticFeedback = true;
    myController.RotationResolutionInDegrees = 10;
  }
}
```

### <a name="click"></a>Нажмите

Щелчок Surface Dial аналогичен нажатию левой кнопки мыши (состояние поворота устройства не влияет на это действие).

#### <a name="ux-guidance"></a>Руководство по проектированию взаимодействия с пользователем

**Не сопоставлять действие или команду с этим жестом, если пользователь не может легко восстановиться из результата**

Любое действие, выполняемое приложением после того, как пользователь щелкнут Surface Dial, должно быть обратимым. Всегда позволяйте пользователю без труда перемещаться по стеку возврата приложения и восстанавливать предыдущее состояние приложения.

Бинарные операции, например включение и отключение звука или отображение и скрытие — вот хорошие кандидаты для жеста нажатия.

**Не следует включать или отключать модальные средства, щелкнув поверхность набора**

Некоторые режимы приложения или средства могут конфликтовать с взаимодействиями, основанными на повороте, или отключать их. Такие инструменты, как линейка на панели инструментов Windows Ink, следует включать и выключать с помощью других возможностей пользовательского интерфейса (на панели инструментов есть встроенный элемент управления [**ToggleButton**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Primitives.ToggleButton)).

Для модальных инструментов сопоставьте активный элемент меню Surface Dial с целевым средством или ранее выбранным элементом меню.

#### <a name="developer-guidance"></a>Руководство для разработчиков

При нажатии Surface Dial инициируется событие [**RadialController.ButtonClicked**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.buttonclicked). В [**RadialControllerButtonClickedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerButtonClickedEventArgs) есть свойство [**Contact**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollerbuttonclickedeventargs.contact), содержащее расположение и контактную область Surface Dial на экране Surface Studio. Если Surface Dial не контактирует с экраном, это свойство имеет значение null. 

### <a name="on-screen"></a>На экране

Как описано выше, Surface Dial можно использовать в сочетании с Surface Studio для отображения меню Surface Dial в специальном экранном режиме. 

В нем вы можете интегрировать и настраивать взаимодействие с помощью Dial в ваших приложениях. Примеры уникальных возможностей, доступных только при совместном использовании Surface Dial и Surface Studio:

- отображение контекстно-зависимых инструментов (например, цветовой палитры) на основе положения Surface Dial, что облегчает их поиск и использование;
- выбор активного средства в зависимости от пользовательского интерфейса, в котором размещено устройство Surface Dial;
- увеличение области экрана в зависимости от расположения Surface Dial;
- уникальное игровое взаимодействие на основе расположения экрана.

#### <a name="ux-guidance-for-on-screen-interactions"></a>Руководство по UX для взаимодействия на экране

**Приложения должны отвечать, когда на экране обнаруживается набираемый абонент**

Визуальная обратная связь позволяет показать пользователям, что приложение обнаружило устройство на экране Surface Studio.

**Настройка пользовательского интерфейса, связанного с набором, на основе расположения устройства**

Устройство (и тело пользователя) может загородить важные элементы интерфейса в зависимости от того, где он размещен.

**Настройка пользовательского интерфейса, связанного с набором поверхностей, на основе взаимодействия с пользователем**

Помимо загораживания оборудования рука пользователя может загородить часть экрана при работе с устройством.

Загороженная область зависит от того, какой рукой осуществляется работа с устройством. Поскольку устройство предназначено для использования главным образом недоминантной рукой, пользовательский интерфейс, связанный с Surface Dial, необходимо настроить для противоположной руки, заданной пользователем (**Параметры Windows > Устройства > Перо и Windows Ink > Выберите, какой рукой вы пишете**).

**Взаимодействия должны реагировать на расположение абонентской области, а не на перемещение**

Ножка устройства должна быть закреплена на экране, а не скользить по нему, так как это не точное позиционное устройство. Поэтому мы ожидаем, что пользователи будут часто поднимать и ставить Surface Dial, а не перетаскивать его по экрану.

**Использование расположения экрана для определения намерений пользователя**

Выбор активного инструмента на основе контекста пользовательского интерфейса, например близости к элементу управления, холсту или окну, может улучшить взаимодействие с пользователем, упрощая выполнение задачи.

#### <a name="developer-guidance"></a>Руководство для разработчиков

Если устройство Surface Dial размещено на поверхности дигитайзера Surface Studio, инициируется событие [**RadialController.ScreenContactStarted**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.screencontactstarted), а данные о контакте ([**RadialControllerScreenContactStartedEventArgs.Contact**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollerscreencontactstartedeventargs.contact)) передаются вашему приложению.

Если пользователь нажимает Surface Dial при контакте с дигитайзером Studio Surface, вызывается событие [**RadialController.ButtonClicked**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontroller.buttonclicked), а данные о контакте ([**RadialControllerButtonClickedEventArgs.Contact**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollerbuttonclickedeventargs.contact)) передаются вашему приложению. 

Контактные данные ([**RadialControllerScreenContact**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerScreenContact)) содержат координаты X и Y центра Surface Dial в координатном пространстве приложения ([**RadialControllerScreenContact.Position**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollerscreencontact.position)), а также ограничивающий прямоугольник ([**RadialControllerScreenContact.Bounds**](https://docs.microsoft.com/uwp/api/windows.ui.input.radialcontrollerscreencontact.bounds)) в аппаратно-независимые пикселях (DIP). Эти сведения очень полезны для предоставления контекста активному инструменту и визуальной обратной связи пользователю.

В следующем примере мы создали простое приложение с четырьмя разделами, каждый из которых содержит один ползунок и один переключатель. Затем мы используем положение Surface Dial на экране для выбора набора ползунков и переключателей, которыми управляет Surface Dial.

1. Сначала мы объявляем наш интерфейс (четыре раздела, каждый из которых содержит ползунок и переключатель) в XAML.

   ![изображение пользовательского интерфейса примера приложения](images/windows-wheel/surface-dial-snippet-customtool3.png)  
   *Пользовательский интерфейс примера приложения*

   ```xaml 
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
  <Grid.RowDefinitions>
    <RowDefinition Height="Auto"/>
    <RowDefinition Height="*"/>
  </Grid.RowDefinitions>
  <StackPanel x:Name="HeaderPanel" 
        Orientation="Horizontal" 
        Grid.Row="0">
    <TextBlock x:Name="Header"
      Text="RadialController customization sample"
      VerticalAlignment="Center"
      Style="{ThemeResource HeaderTextBlockStyle}"
      Margin="10,0,0,0" />
  </StackPanel>
  <Grid Grid.Row="1" x:Name="RootGrid">
    <Grid.RowDefinitions>
      <RowDefinition Height="*"/>
      <RowDefinition Height="*"/>
    </Grid.RowDefinitions>
    <Grid.ColumnDefinitions>
      <ColumnDefinition Width="*"/>
      <ColumnDefinition Width="*"/>
    </Grid.ColumnDefinitions>
    <Grid x:Name="Grid0"
      Grid.Row="0"
      Grid.Column="0">
      <StackPanel Orientation="Vertical" 
        VerticalAlignment="Center" 
        HorizontalAlignment="Center">
        <!-- Slider for rotational input -->
        <Slider x:Name="RotationSlider0"
          Width="300"
          HorizontalAlignment="Left"/>
        <!-- Switch for button input -->
        <ToggleSwitch x:Name="ButtonToggle0"
            HorizontalAlignment="Left"/>
      </StackPanel>
    </Grid>
    <Grid x:Name="Grid1"
      Grid.Row="0"
      Grid.Column="1">
      <StackPanel Orientation="Vertical" 
        VerticalAlignment="Center" 
        HorizontalAlignment="Center">
        <!-- Slider for rotational input -->
        <Slider x:Name="RotationSlider1"
          Width="300"
          HorizontalAlignment="Left"/>
        <!-- Switch for button input -->
        <ToggleSwitch x:Name="ButtonToggle1"
            HorizontalAlignment="Left"/>
      </StackPanel>
    </Grid>
    <Grid x:Name="Grid2"
      Grid.Row="1"
      Grid.Column="0">
      <StackPanel Orientation="Vertical" 
        VerticalAlignment="Center" 
        HorizontalAlignment="Center">
        <!-- Slider for rotational input -->
        <Slider x:Name="RotationSlider2"
          Width="300"
          HorizontalAlignment="Left"/>
        <!-- Switch for button input -->
        <ToggleSwitch x:Name="ButtonToggle2"
            HorizontalAlignment="Left"/>
      </StackPanel>
    </Grid>
    <Grid x:Name="Grid3"
      Grid.Row="1"
      Grid.Column="1">
      <StackPanel Orientation="Vertical" 
        VerticalAlignment="Center" 
        HorizontalAlignment="Center">
        <!-- Slider for rotational input -->
        <Slider x:Name="RotationSlider3"
          Width="300"
          HorizontalAlignment="Left"/>
        <!-- Switch for button input -->
        <ToggleSwitch x:Name="ButtonToggle3"
            HorizontalAlignment="Left"/>
      </StackPanel>
    </Grid>
  </Grid>
</Grid>
   ```

2. Вот код программной части с обработчиками для положения Surface Dial на экране.

```csharp
Slider ActiveSlider;
ToggleSwitch ActiveSwitch;
Grid ActiveGrid;

public MainPage()
{
  ...

  myController.ScreenContactStarted += 
    MyController_ScreenContactStarted;
  myController.ScreenContactContinued += 
    MyController_ScreenContactContinued;
  myController.ScreenContactEnded += 
    MyController_ScreenContactEnded;
  myController.ControlLost += MyController_ControlLost;

  //Set initial grid for Surface Dial input.
  ActiveGrid = Grid0;
  ActiveSlider = RotationSlider0;
  ActiveSwitch = ButtonToggle0;
}

private void MyController_ScreenContactStarted(RadialController sender, 
  RadialControllerScreenContactStartedEventArgs args)
{
  //find grid at contact location, update visuals, selection
  ActivateGridAtLocation(args.Contact.Position);
}

private void MyController_ScreenContactContinued(RadialController sender, 
  RadialControllerScreenContactContinuedEventArgs args)
{
  //if a new grid is under contact location, update visuals, selection
  if (!VisualTreeHelper.FindElementsInHostCoordinates(
    args.Contact.Position, RootGrid).Contains(ActiveGrid))
  {
    ActiveGrid.Background = new 
      SolidColorBrush(Windows.UI.Colors.White);
    ActivateGridAtLocation(args.Contact.Position);
  }
}

private void MyController_ScreenContactEnded(RadialController sender, object args)
{
  //return grid color to normal when contact leaves screen
  ActiveGrid.Background = new 
  SolidColorBrush(Windows.UI.Colors.White);
}

private void MyController_ControlLost(RadialController sender, object args)
{
  //return grid color to normal when focus lost
  ActiveGrid.Background = new 
    SolidColorBrush(Windows.UI.Colors.White);
}

private void ActivateGridAtLocation(Point Location)
{
  var elementsAtContactLocation = 
    VisualTreeHelper.FindElementsInHostCoordinates(Location, 
      RootGrid);

  foreach (UIElement element in elementsAtContactLocation)
  {
    if (element as Grid == Grid0)
    {
      ActiveSlider = RotationSlider0;
      ActiveSwitch = ButtonToggle0;
      ActiveGrid = Grid0;
      ActiveGrid.Background = new SolidColorBrush( 
        Windows.UI.Colors.LightGoldenrodYellow);
      return;
    }
    else if (element as Grid == Grid1)
    {
      ActiveSlider = RotationSlider1;
      ActiveSwitch = ButtonToggle1;
      ActiveGrid = Grid1;
      ActiveGrid.Background = new SolidColorBrush( 
        Windows.UI.Colors.LightGoldenrodYellow);
      return;
    }
    else if (element as Grid == Grid2)
    {
      ActiveSlider = RotationSlider2;
      ActiveSwitch = ButtonToggle2;
      ActiveGrid = Grid2;
      ActiveGrid.Background = new SolidColorBrush( 
        Windows.UI.Colors.LightGoldenrodYellow);
      return;
    }
    else if (element as Grid == Grid3)
    {
      ActiveSlider = RotationSlider3;
      ActiveSwitch = ButtonToggle3;
      ActiveGrid = Grid3;
      ActiveGrid.Background = new SolidColorBrush( 
        Windows.UI.Colors.LightGoldenrodYellow);
      return;
    }
  }
}
```

При запуске приложения мы используем Surface Dial для взаимодействия с ним. Сначала мы размещаем устройство на экране Surface Studio, что приложение обнаруживает и связывает с нижним правым разделом (см. изображение). Затем мы нажимаем и удерживаем Surface Dial, чтобы открыть меню и выбрать специальный инструмент. После активации специального инструмента ползунок можно настроить, вращая Surface Dial, а переключатель можно активировать, нажав Surface Dial.

![изображение пользовательского интерфейса примера приложения, активированного с помощью настраиваемого инструмента для набора средств,](images/windows-wheel/surface-dial-snippet-customtool4.png)  
*Пользовательский интерфейс примера приложения, активированный с помощью настраиваемого инструмента набора для Surface*

## <a name="summary"></a>Сводка

В этом разделе представлен обзор устройства ввода Surface Dial, рекомендации по проектированию взаимодействия с пользователем и руководство для разработчиков, помогающие настроить работу устройства вне экрана и на экране при использовании вместе с Surface Studio.

## <a name="feedback"></a>Отзыв

Отправляйте свои вопросы, предложения и отзывы на [radialcontroller@microsoft.com](mailto:radialcontroller@microsoft.com).

## <a name="related-articles"></a>Связанные статьи

[Учебник. Поддержка набора поверхностей (и других устройств с колесиком) в приложении UWP](radialcontroller-walkthrough.md)

### <a name="api-reference"></a>Справочник по API

- [Класс **радиалконтроллер**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialController)
- [Класс **радиалконтроллербуттонкликкедевентаргс**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerButtonClickedEventArgs)
- [Класс **радиалконтроллерконфигуратион**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerConfiguration) 
- [Класс **радиалконтроллерконтролаккуиредевентаргс**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerControlAcquiredEventArgs) 
- [Класс **радиалконтроллермену**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerMenu) 
- [Класс **радиалконтроллерменуитем**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerMenuItem) 
- [Класс **радиалконтроллерротатиончанжедевентаргс**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerRotationChangedEventArgs) 
- [Класс **радиалконтроллерскринконтакт**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerScreenContact) 
- [Класс **радиалконтроллерскринконтактконтинуедевентаргс**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerScreenContactContinuedEventArgs) 
- [Класс **радиалконтроллерскринконтактстартедевентаргс**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerScreenContactStartedEventArgs)
- [Перечисление **радиалконтроллерменукновникон**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerMenuKnownIcon) 
- [Перечисление **радиалконтроллерсистемменуитемкинд**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.RadialControllerSystemMenuItemKind) 

### <a name="samples"></a>Примеры

#### <a name="topic-samples"></a>Примеры в разделе

[Настройка Радиалконтроллер](https://github.com/MicrosoftDocs/windows-topic-specific-samples/archive/uwp-radialcontroller-customization.zip)

#### <a name="other-samples"></a>Другие примеры

[Образец цветовой книги](https://github.com/Microsoft/Windows-appsample-coloringbook)

[Руководство по началу работы: Поддержка набора поверхностей (и других устройств с колесиком) в приложении UWP](https://github.com/Microsoft/Windows-tutorials-inputs-and-devices/tree/master/GettingStarted-RadialController)

[Примеры для универсальной платформы Windows (C# и C++)](https://github.com/Microsoft/Windows-universal-samples/tree/b78d95134ce2d57c848e0a8dc339fc362748fb9c/Samples/RadialController)

[Пример классического рабочего стола Windows](https://github.com/Microsoft/Windows-classic-samples/tree/master/Samples/RadialController)