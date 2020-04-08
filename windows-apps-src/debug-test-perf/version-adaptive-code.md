---
title: Адаптивный к версии код
description: С помощью класса ApiInformation можно воспользоваться новыми API, при этом сохраняя совместимость с предыдущими версиями.
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 3293e91e-6888-4cc3-bad3-61e5a7a7ab4e
ms.localizationpriority: medium
ms.openlocfilehash: 2c03475c0c4007508a18c17645dbe99eeb7d6cb0
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681985"
---
# <a name="version-adaptive-code"></a>Адаптивный к версии код

Написание адаптивного кода аналогично созданию [адаптивного пользовательского интерфейса](https://docs.microsoft.com/windows/uwp/layout/layouts-with-xaml). Вы можете спроектировать базовый интерфейс для самого небольшого экрана, а затем перемещать и добавлять элементы, если вы обнаружите, что приложение работает на устройстве с большим экраном. Для создания адаптивного кода вы пишите базовый код для самой ранней версии ОС и добавляете дополнительные функции, если обнаруживаете, что приложение работает в более поздней версии ОС, где эти функции доступны.

Важные справочные сведения об ApiInformation, контрактах API и настройке Visual Studio доступны в статье [Version adaptive apps: Use new APIs while maintaining compatibility with previous versions](version-adaptive-apps.md) (Приложения с адаптивным к версии кодом: используйте новые API, сохраняя совместимость с предыдущими версиями).

### <a name="runtime-api-checks"></a>Проверки API во время выполнения

Используйте класс [Windows.Foundation.Metadata.ApiInformation](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation) в своем коде в условном выражении, чтобы проверить наличие API-интерфейса, который вы хотите вызвать. Это условие оценивается при каждом запуске приложения, но оно будет **истинным** только на устройствах, где этот API-интерфейс присутствует и, следовательно, доступен. Это позволяет писать адаптивный к версии код, чтобы создавать приложения, использующие API, которые доступны только в определенных версиях ОС.

Рассмотрим конкретные примеры использования новых функций в Windows Insider Preview. Общий обзор использования класса **ApiInformation** см. в статье [Device families overview](https://docs.microsoft.com/uwp/extension-sdks/device-families-overview#writing-code) (Общие сведения о семействах устройств) и записи блога [Dynamically detecting features with API contracts](https://blogs.windows.com/buildingapps/2015/09/15/dynamically-detecting-features-with-api-contracts-10-by-10/) (Динамическое определение компонентов с контрактами API).

> [!TIP]
> Многочисленные проверки API во время выполнения могут повлиять на производительность приложения. В этих примерах мы покажем проверки встроенными. В рабочем коде необходимо выполнить проверку только один раз и кэшировать результат, а затем использовать его во всем приложении. 

### <a name="unsupported-scenarios"></a>Неподдерживаемые сценарии

В большинстве случаев вы можете сохранить для приложения минимальную версию SDK 10240 и использовать проверки во время выполнения, чтобы включить новые API, если приложение работает в более поздней версии системы. Однако в ряде случаев необходимо увеличить минимальную версию приложения, чтобы использовать новые функции.

Это нужно сделать, если вы используете:
- новый API, которому требуется возможность, недоступна в более ранней версии. Вам необходимо увеличить минимальную версию до той, которая поддерживает эту возможность. Подробнее см. в разделе [Объявления характеристик приложения](../packaging/app-capability-declarations.md);
- любые новые ключи ресурсов, добавленные в generic.xaml и недоступные в предыдущей версии. Версия файла generic.xaml, используемого во время выполнения, определяется по версии ОС устройства. Невозможно использовать проверки API во время выполнения, чтобы определить наличие ресурсов XAML. Поэтому необходимо использовать только ключи ресурсов, которые доступны в минимальной версии, поддерживаемой приложением, иначе в нем возникнет сбой из-за исключения [XAMLParseException](https://docs.microsoft.com/uwp/api/windows.ui.xaml.markup.xamlparseexception) во время выполнения.

### <a name="adaptive-code-options"></a>Варианты адаптивного кода

Создать адаптивный код можно двумя способами. В большинстве случаев вы пишете разметку приложения для минимальной версии, а затем используете код приложения, чтобы добавить более новые функции, если ОС их поддерживает. Однако если вам нужно обновить свойство в визуальном состоянии, а между версиями ОС изменилось только свойство или значения перечисления, вы можете создать расширяемый триггер состояния, который активируется в зависимости от наличия API.

Здесь мы сравним эти варианты.

**Код приложения**

Когда следует использовать:
- рекомендуется для всех сценариев адаптивного кода, за исключением конкретных случаев, указанных ниже для расширяемых триггеров.

Преимущества:
- упрощает обработку отличий API в разметке.

Недостатки:
- Нет поддержки конструктора.

**Триггеры состояния**

Когда следует использовать:
- только если между версиями ОС изменилось свойство или перечисление, связанное с визуальным состоянием, для чего не требуется изменение логики.

Преимущества:
- позволяет создавать специальные визуальные состояния, которые активируются в зависимости от наличия API.
- Доступна частичная поддержка конструктора.

Недостатки:
- использование настраиваемых триггеров ограничено визуальным состояниям, что не сводится к сложным адаптивным макетам;
- Следует использовать методы установки, чтобы задавать измененные значения, поэтому возможны только простые изменения;
- пользовательские триггеры состояния достаточно подробны для настройки и применения.

## <a name="adaptive-code-examples"></a>Примеры адаптивного кода

В этом разделе мы приведем несколько примеров адаптивного кода, использующего новые API Windows 10 версии 1607 (Windows Insider Preview).

### <a name="example-1-new-enum-value"></a>Пример 1: новое значение перечисления

В Windows 10 версии 1607 добавлено новое значение перечисления [InputScopeNameValue](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.inputscopenamevalue): **ChatWithoutEmoji**. Поведение этого типа вводимых данных совпадает с типом **Chat** (проверка правописания, автозаполнение, автоматическое применение заглавных букв), но он сопоставляет с сенсорной клавиатурой без кнопки смайлика. Это полезно, если вы создаете собственное средство выбора смайликов и хотите отключить встроенную кнопку смайлика на сенсорной клавиатуре. 

В этом примере показано, как проверить, доступно ли значение перечисления **ChatWithoutEmoji**, и задать свойство [InputScope](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.inputscope) элемента **TextBox**, если это так. Если значение отсутствует в системе, где запущено приложение, для **InputScope** задается значение **Chat**. Показанный код можно разместить в конструкторе Page или обработчике событий Page.Loaded.

> [!TIP]
> При проверке API используйте статические строки, а не полагайтесь на возможности языка .NET, иначе приложение может попытаться обратиться к неопределенному типу, что приведет к сбою во время выполнения.

**C#**
```csharp
// Create a TextBox control for sending messages 
// and initialize an InputScope object.
TextBox messageBox = new TextBox();
messageBox.AcceptsReturn = true;
messageBox.TextWrapping = TextWrapping.Wrap;
InputScope scope = new InputScope();
InputScopeName scopeName = new InputScopeName();

// Check that the ChatWithEmoji value is present.
// (It's present starting with Windows 10, version 1607,
//  the Target version for the app. This check returns false on earlier versions.)         
if (ApiInformation.IsEnumNamedValuePresent("Windows.UI.Xaml.Input.InputScopeNameValue", "ChatWithoutEmoji"))
{
    // Set new ChatWithoutEmoji InputScope if present.
    scopeName.NameValue = InputScopeNameValue.ChatWithoutEmoji;
}
else
{
    // Fall back to Chat InputScope.
    scopeName.NameValue = InputScopeNameValue.Chat;
}

// Set InputScope on messaging TextBox.
scope.Names.Add(scopeName);
messageBox.InputScope = scope;

// For this example, set the TextBox text to show the selected InputScope.
messageBox.Text = messageBox.InputScope.Names[0].NameValue.ToString();

// Add the TextBox to the XAML visual tree (rootGrid is defined in XAML).
rootGrid.Children.Add(messageBox);
```

В предыдущем примере элемент создается TextBox, и все его свойства задаются в коде. Однако если у вас есть существующий код XAML и необходимо просто изменить свойство InputScope в системах, где новое значение поддерживается, это можно сделать, не изменяя XAML, как показано ниже. Вы задаете значение по умолчанию **Chat** в XAML, но переопределяете его в коде, если значение **ChatWithoutEmoji** присутствует.

**XAML**
```xaml
<TextBox x:Name="messageBox"
         AcceptsReturn="True" TextWrapping="Wrap"
         InputScope="Chat"
         Loaded="messageBox_Loaded"/>
```

**C#**
```csharp
private void messageBox_Loaded(object sender, RoutedEventArgs e)
{
    if (ApiInformation.IsEnumNamedValuePresent("Windows.UI.Xaml.Input.InputScopeNameValue", "ChatWithoutEmoji"))
    {
        // Check that the ChatWithEmoji value is present.
        // (It's present starting with Windows 10, version 1607,
        //  the Target version for the app. This code is skipped on earlier versions.)
        InputScope scope = new InputScope();
        InputScopeName scopeName = new InputScopeName();
        scopeName.NameValue = InputScopeNameValue.ChatWithoutEmoji;
        // Set InputScope on messaging TextBox.
        scope.Names.Add(scopeName);
        messageBox.InputScope = scope;
    }

    // For this example, set the TextBox text to show the selected InputScope.
    // This is outside of the API check, so it will happen on all OS versions.
    messageBox.Text = messageBox.InputScope.Names[0].NameValue.ToString();
}
```

Теперь рассмотрим, как параметры целевой и минимальной версии применяются к нашему примеру.

В этих примерах можно использовать значение перечисления Chat в XAML или в коде без проверки, поскольку оно присутствует в минимальной поддерживаемой версии ОС. 

При использовании значения ChatWithoutEmoji в XAML или в коде без проверки компиляция пройдет без ошибок, поскольку это значение есть в целевой версии ОС. Приложение также запустится без ошибок в системе с целевой версией ОС. Однако при запуске приложения на компьютере с минимальной версией ОС произойдет сбой во время выполнения, поскольку значение перечисления ChatWithoutEmoji отсутствует. Поэтому это значение следует использовать только в коде и создать для него оболочку в виде проверки API, чтобы вызывать его, только если значение поддерживается в текущей системе.

### <a name="example-2-new-control"></a>Пример 2: новый элемент управления

В новой версии Windows обычно предоставляются новые элементы управления на поверхности API платформы UWP, которые реализуют новые возможности. Чтобы использовать новый элемент управления используйте метод [ApiInformation.IsTypePresent](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.istypepresent).

В Windows 10 версии 1607 появился новый элемент управления мультимедиа [**MediaPlayerElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaplayerelement). Он основан на классе [MediaPlayer](https://docs.microsoft.com/uwp/api/windows.media.playback.mediaplayer), поэтому предоставляет такие функции, как привязка к фоновому звуку, а также использует архитектурные улучшения стека функций мультимедиа.

Однако если приложение выполняется на устройстве с версией Windows 10 более ранней, чем 1607, необходимо использовать элемент [**MediaElement**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.mediaelement) вместо нового элемента управления **MediaPlayerElement**. Вы можете использовать метод [**ApiInformation.IsTypePresent**](https://docs.microsoft.com/uwp/api/windows.foundation.metadata.apiinformation.istypepresent), чтобы проверить наличие элемента управления MediaPlayerElement во время выполнения, и загрузить подходящий элемент.

В этом примере показано, как создать приложение, которое использует новый элемент MediaPlayerElement или старый элемент MediaElement в зависимости от того, имеется доступен ли тип MediaPlayerElement. В коде используйте класс [UserControl](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.usercontrol), чтобы разбить элементы управления, их интерфейс и код на компоненты и переключаться между ними зависимости от версии ОС. Кроме того, можно использовать настраиваемый элемент управления, который предоставляет дополнительные функции, но это выходит за рамки данного простого примера.
 
**MediaPlayerUserControl** 

`MediaPlayerUserControl` инкапсулирует элемент **MediaPlayerElement** и несколько кнопок, которые используются для перехода по мультимедиа кадр за кадром. UserControl позволяет обрабатывать эти элементы управления как единый объект и упрощает переключение на MediaElement в старых системах. Этот пользовательский элемент управления следует использовать только в системах, где MediaPlayerElement поддерживается, поэтому в коде внутри этого элемента управления проверки ApiInformation не выполняются.

> [!NOTE]
> Для упрощения этого примера кнопки перехода по кадрам размещаются вне проигрывателя мультимедиа. Для улучшения взаимодействия с пользователем необходимо добавить в MediaTransportControls собственные кнопки. Подробнее см. в разделе [Пользовательские элементы управления транспортировкой](https://docs.microsoft.com/windows/uwp/controls-and-patterns/custom-transport-controls). 

**XAML**
```xaml
<UserControl
    x:Class="MediaApp.MediaPlayerUserControl"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:MediaApp"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    d:DesignHeight="300"
    d:DesignWidth="400">

    <Grid x:Name="MPE_grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition Height="Auto"/>
        </Grid.RowDefinitions>
        <StackPanel Orientation="Horizontal" 
                    HorizontalAlignment="Center" Grid.Row="1">
            <RepeatButton Click="StepBack_Click" Content="Step Back"/>
            <RepeatButton Click="StepForward_Click" Content="Step Forward"/>
        </StackPanel>
    </Grid>
</UserControl>
```

**C#**
```csharp
using System;
using Windows.Media.Core;
using Windows.UI.Xaml;
using Windows.UI.Xaml.Controls;

namespace MediaApp
{
    public sealed partial class MediaPlayerUserControl : UserControl
    {
        public MediaPlayerUserControl()
        {
            this.InitializeComponent();
            
            // The markup code compiler runs against the Minimum OS version so MediaPlayerElement must be created in app code
            MPE = new MediaPlayerElement();
            Uri videoSource = new Uri("ms-appx:///Assets/UWPDesign.mp4");
            MPE.Source = MediaSource.CreateFromUri(videoSource);
            MPE.AreTransportControlsEnabled = true;
            MPE.MediaPlayer.AutoPlay = true;

            // Add MediaPlayerElement to the Grid
            MPE_grid.Children.Add(MPE);

        }

        private void StepForward_Click(object sender, RoutedEventArgs e)
        {
            // Step forward one frame, only available using MediaPlayerElement.
            MPE.MediaPlayer.StepForwardOneFrame();
        }

        private void StepBack_Click(object sender, RoutedEventArgs e)
        {
            // Step forward one frame, only available using MediaPlayerElement.
            MPE.MediaPlayer.StepForwardOneFrame();
        }
    }
}
```

**MediaElementUserControl**
 
`MediaElementUserControl` инкапсулирует элемент управления **MediaElement**.

**XAML**
```xaml
<UserControl
    x:Class="MediaApp.MediaElementUserControl"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:MediaApp"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    mc:Ignorable="d"
    d:DesignHeight="300"
    d:DesignWidth="400">

    <Grid>
        <MediaElement AreTransportControlsEnabled="True" 
                      Source="Assets/UWPDesign.mp4"/>
    </Grid>
</UserControl>
```

> [!NOTE]
> Кодовая страница `MediaElementUserControl` содержит только сформированный код, поэтому она не показана.

**Инициализация элемента управления на основе IsTypePresent**

Во время выполнения вы вызываете **ApiInformation.IsTypePresent**, чтобы проверить наличие MediaPlayerElement. Если элемент есть, вы загружаете `MediaPlayerUserControl`, если нет — загружается `MediaElementUserControl`.

**C#**
```csharp
public MainPage()
{
    this.InitializeComponent();

    UserControl mediaControl;

    // Check for presence of type MediaPlayerElement.
    if (ApiInformation.IsTypePresent("Windows.UI.Xaml.Controls.MediaPlayerElement"))
    {
        mediaControl = new MediaPlayerUserControl();
    }
    else
    {
        mediaControl = new MediaElementUserControl();
    }

    // Add mediaControl to XAML visual tree (rootGrid is defined in XAML).
    rootGrid.Children.Add(mediaControl);
}
```

> [!IMPORTANT]
> Помните, что эта проверка только устанавливает для объекта `mediaControl` значение `MediaPlayerUserControl` или `MediaElementUserControl`. Эти условные проверки следует выполнять в любом месте кода, где требуется определить, нужно ли использовать API MediaPlayerElement или MediaElement. Проверку необходимо выполнить только один раз и кэшировать результат, а затем использовать его во всем приложении.

## <a name="state-trigger-examples"></a>Примеры триггера состояния

Расширяемые триггеры состояния позволяют использовать разметку и код совместно для активации изменения визуального состояния на основе условия, которое вы проверяете в коде — в нашем случае это наличие определенного API. Мы не рекомендуем использовать триггеры состояния для общих сценариев адаптивного кода, так как они потребляют много ресурсов и применяются только к визуальному состоянию. 

Триггеры состояния необходимо использовать для адаптивного кода, только если пользовательский интерфейс в разных версиях ОС претерпевает незначительные изменения, которые не повлияют на оставшийся пользовательский интерфейс — это может быть изменение свойства или значения перечисления элемента управления.

### <a name="example-1-new-property"></a>Пример 1: новое свойство

Первый шаг настройки расширяемого триггера состояния — создание подкласса для класса [StateTriggerBase](https://docs.microsoft.com/uwp/api/windows.ui.xaml.statetriggerbase), чтобы создать пользовательский триггер, который будет активироваться в зависимости от присутствия API. В этом примере показан триггер, который активируется, если наличие свойства соответствует переменной `_isPresent`, заданной в XAML.

**C#**
```csharp
class IsPropertyPresentTrigger : StateTriggerBase
{
    public string TypeName { get; set; }
    public string PropertyName { get; set; }

    private Boolean _isPresent;
    private bool? _isPropertyPresent = null;

    public Boolean IsPresent
    {
        get { return _isPresent; }
        set
        {
            _isPresent = value;
            if (_isPropertyPresent == null)
            {
                // Call into ApiInformation method to determine if property is present.
                _isPropertyPresent =
                ApiInformation.IsPropertyPresent(TypeName, PropertyName);
            }

            // If the property presence matches _isPresent then the trigger will be activated;
            SetActive(_isPresent == _isPropertyPresent);
        }
    }
}
```

Следующий шаг — настройка триггера визуального состояния в XAML, чтобы получить два различных визуальных состояния в зависимости от присутствия API. 

В Windows 10 версии 1607 добавлено новое свойство для класса [FrameworkElement](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement), [AllowFocusOnInteraction](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.allowfocusoninteraction), которое определяет, получает ли элемент управления фокус, когда пользователь взаимодействует с ним. Это удобно, если вам нужно удерживать фокус на текстовом поле для ввода данных (и продолжать показывать сенсорную клавиатуру), когда пользователь нажимает кнопку.

Триггер в этом примере проверяет, доступно ли свойство. Если свойство присутствует, для свойства **AllowFocusOnInteraction** кнопки задается значение **false**; если свойство отсутствует, то кнопка сохраняет первоначальное состояние. В пример включен элемент TextBox, чтобы упростить просмотр действия этого свойства при запуске кода.

**XAML**
```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">
    <StackPanel>
        <TextBox Width="300" Height="36"/>
        <!-- Button to set the new property on. -->
        <Button x:Name="testButton" Content="Test" Margin="12"/>
    </StackPanel>

    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="propertyPresentStateGroup">
            <VisualState>
                <VisualState.StateTriggers>
                    <!--Trigger will activate if the AllowFocusOnInteraction property is present-->
                    <local:IsPropertyPresentTrigger 
                        TypeName="Windows.UI.Xaml.FrameworkElement" 
                        PropertyName="AllowFocusOnInteraction" IsPresent="True"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="testButton.AllowFocusOnInteraction" 
                            Value="False"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>
```

### <a name="example-2-new-enum-value"></a>Пример 2: новое значение перечисления

В этом примере показано, как задать различные значения перечисления в зависимости от того, доступно ли то или иное значение. Здесь настраиваемый триггер состояния используется, чтобы достичь того же результата, что и предыдущем примере чата. В данном примере вы используете новый тип вводимых данных ChatWithoutEmoji, если устройство работает под управлением Windows 10 версии 1607, в противном случае используется тип вводимых данных **Chat**. Визуальные состояния, которые используют этот триггер, настраиваются в стиле *если-иначе*, где тип вводимых данных выбирается в зависимости от наличия нового значения перечисления.

**C#**
```csharp
class IsEnumPresentTrigger : StateTriggerBase
{
    public string EnumTypeName { get; set; }
    public string EnumValueName { get; set; }

    private Boolean _isPresent;
    private bool? _isEnumValuePresent = null;

    public Boolean IsPresent
    {
        get { return _isPresent; }
        set
        {
            _isPresent = value;

            if (_isEnumValuePresent == null)
            {
                // Call into ApiInformation method to determine if value is present.
                _isEnumValuePresent =
                ApiInformation.IsEnumNamedValuePresent(EnumTypeName, EnumValueName);
            }

            // If the property presence matches _isPresent then the trigger will be activated;
            SetActive(_isPresent == _isEnumValuePresent);
        }
    }
}
```

**XAML**
```xaml
<Grid Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

    <TextBox x:Name="messageBox"
     AcceptsReturn="True" TextWrapping="Wrap"/>


    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="EnumPresentStates">
            <!--if-->
            <VisualState x:Name="isPresent">
                <VisualState.StateTriggers>
                    <local:IsEnumPresentTrigger 
                        EnumTypeName="Windows.UI.Xaml.Input.InputScopeNameValue" 
                        EnumValueName="ChatWithoutEmoji" IsPresent="True"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="messageBox.InputScope" Value="ChatWithoutEmoji"/>
                </VisualState.Setters>
            </VisualState>
            <!--else-->
            <VisualState x:Name="isNotPresent">
                <VisualState.StateTriggers>
                    <local:IsEnumPresentTrigger 
                        EnumTypeName="Windows.UI.Xaml.Input.InputScopeNameValue" 
                        EnumValueName="ChatWithoutEmoji" IsPresent="False"/>
                </VisualState.StateTriggers>
                <VisualState.Setters>
                    <Setter Target="messageBox.InputScope" Value="Chat"/>
                </VisualState.Setters>
            </VisualState>
        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</Grid>
```

## <a name="related-articles"></a>Похожие статьи

- [Общие сведения о семействах устройств](https://docs.microsoft.com/uwp/extension-sdks/device-families-overview)
- [Динамическое обнаружение компонентов с контрактами API](https://blogs.windows.com/buildingapps/2015/09/15/dynamically-detecting-features-with-api-contracts-10-by-10/)
