---
Description: TwoPaneView — это элемент управления макетом, позволяющий управлять отображением приложений с 2 различными областями содержимого.
title: Представление с двумя панелями
template: detail.hbs
ms.date: 01/22/2020
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: b3d12f2aad1d5dffbbad0790e5940699536daf0b
ms.sourcegitcommit: e6a435716799c7bb192b3d5c4d3b8295ec3911d4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549709"
---
# <a name="two-pane-view"></a>Представление с двумя панелями

[TwoPaneView](/uwp/api/microsoft.ui.xaml.controls.twopaneview) — это элемент управления макетом, позволяющий управлять отображением приложений с 2 различными областями содержимого, например с основным и подробным представлением.

> [!IMPORTANT]
> В этой статье описаны функциональные возможности предварительной версии, а также инструкции по ее использованию, которые могут быть существенно изменены, прежде чем версия станет общедоступной. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.

Несмотря на то что элемент управления TwoPaneView работает на всех устройствах с Windows, его предназначение — позволить вам в полной мере автоматически использовать преимущества двухэкранных устройств без специального кодирования. На двухэкранном устройстве представление с двумя панелями обеспечивает четкое разделение пользовательского интерфейса, при котором ваше содержимое размещается с обеих сторон промежутка между экранами.

> **ПРИМЕЧАНИЕ.** _Двухэкранное устройство_ — это особый тип устройств с уникальными возможностями. Он не эквивалентен компьютеру с несколькими мониторами. Дополнительные сведения о двухэкранных устройствах см. в [этой статье](/dual-screen/introduction).
>
>В данной статье мы используем термины _одноэкранное устройство_ или _одноэкранный дисплей_ для обозначения любого устройства, не являющегося двухэкранным, будь то один монитор или часть многомониторной установки. Элемент управления TwoPaneView функционирует на одноэкранном устройстве так же, как и другие элементы управления XAML. Дополнительные сведения об оптимизации своего приложения для нескольких мониторов см. в [этой статье](/windows/uwp/design/layout/show-multiple-views).

| **Получение библиотеки пользовательского интерфейса Windows** |
| - |
| Этот элемент управления является частью библиотеки пользовательского интерфейса Windows, пакета NuGet, который содержит новые элементы управления и компоненты пользовательского интерфейса для приложений UWP. Дополнительные сведения, включая инструкции по установке, см. в [обзорной статье о библиотеке пользовательского интерфейса Windows](/uwp/toolkits/winui/). |

| **API платформы** | **API библиотеки пользовательского интерфейса Windows** |
| - | - |
| [Класс TwoPaneView](/uwp/api/windows.ui.xaml.controls.twopaneview) | [Класс TwoPaneView](/uwp/api/microsoft.ui.xaml.controls.twopaneview) |

В этом документе мы будем использовать в XAML псевдоним **muxc** для всех API библиотеки пользовательского интерфейса Windows, которую мы добавили в проект. Мы добавили его для нашего элемента [Page](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page):

```xaml
xmlns:muxc="using:Microsoft.UI.Xaml.Controls"
```

В коде C# мы также будем использовать псевдоним **muxc** для всех API библиотеки пользовательского интерфейса Windows, которую мы добавили в проект. Мы добавили оператор **using** в верхней части файла:

```csharp
using muxc = Microsoft.UI.Xaml.Controls;
```

## <a name="is-this-the-right-control"></a>Выбор правильного элемента управления

Представление с двумя панелями следует использовать при наличии 2 отдельных областей содержимого и:

- если необходимо, чтобы содержимое автоматически перегруппировывалось и изменяло размер по ширине окна;
- если необходимо, чтобы вторичная область содержимого отображалась/скрывалась в зависимости от доступного пространства;
- если необходимо четко распределить содержимое между двумя экранами двухэкранного устройства.

## <a name="examples"></a>Примеры

На приведенных ниже изображениях показано приложение, работающее на одноэкранном и двухэкранном устройстве. Представление с двумя панелями адаптирует пользовательский интерфейс приложения к различным конфигурациям панели на каждом устройстве.

![tpv-single.png](images/two-pane-view/tpv-single.png)

_Приложение на одноэкранном устройстве._

![tpv-dual-wide.png](images/two-pane-view/tpv-dual-wide.png)

_Приложение, открытое на двухэкранном устройстве в горизонтальном режиме._

![tpv-dual-tall.png](images/two-pane-view/tpv-dual-tall.png)

_Приложение, открытое на двухэкранном устройстве в вертикальном режиме._

## <a name="how-it-works"></a>Принцип работы

Представление с двумя панелями содержит две панели для размещения содержимого. Размер и расположение панелей регулируется в зависимости от пространства, доступного для окна. Возможные макеты панели определяются перечислением [TwoPaneViewMode](/uwp/api/microsoft.ui.xaml.controls.twopaneviewmode):

- **SinglePane** — отображается только одна панель, как указано в свойстве [PanePriority](/uwp/api/microsoft.ui.xaml.controls.twopaneview.panepriority).
- **Wide** — панели отображаются рядом или отображается одна панель, как указано в свойстве [WideModeConfiguration](/uwp/api/microsoft.ui.xaml.controls.twopaneview.widemodeconfiguration).
- **Tall** — панели отображаются сверху и снизу, либо отображается одна панель, как указано в свойстве [TallModeConfiguration](/uwp/api/microsoft.ui.xaml.controls.twopaneview.tallmodeconfiguration).

Для настройки представления с двумя панелями задайте [PanePriority](/uwp/api/microsoft.ui.xaml.controls.twopaneview.panepriority), указав, какая панель будет отображаться при наличии пространства лишь для одной панели. Затем укажите, где должна отображаться панель Pane1: сверху или снизу для вертикальных окон, слева или справа для горизонтальных окон.

Представление с двумя панелями управляет размером и расположением панелей, но вам все равно необходимо настроить адаптацию содержимого внутри панели к изменениям размера и ориентации. Дополнительные сведения о создании адаптивного пользовательского интерфейса см. в статьях [Responsive layouts with XAML](/windows/uwp/design/layout/layouts-with-xaml) (Гибкие макеты в XAML) и [Layout panels](/windows/uwp/design/layout/layout-panels) (Панели макета).

Представление с двумя панелями управляет отображением панелей в зависимости от устройства, на котором запущено приложение:

- На двухэкранном устройстве.

    Представление с двумя панелями упрощает оптимизацию пользовательского интерфейса для двухэкранных устройств. Размер окна будет изменяться для использования всего доступного пространства на экранах. Если ваше приложение открыто только на одном из экранов устройства, будет отображаться одна панель, как указано в свойстве [PanePriority](/uwp/api/microsoft.ui.xaml.controls.twopaneview.panepriority).

    Если ваше приложение открыто на двух экранах устройства, каждый экран будет отображать содержимое одной из панелей, правильно распределенное с учетом промежутка. При использовании представления с двумя панелями поддержка разделения встроена. Вам лишь нужно настроить высоту/ширину, указав, на каком из экранов должна отображаться каждая панель. Представление с двумя панелями настроит остальные параметры.


- На одноэкранных устройствах.

    При использовании одноэкранного устройства, такого как ноутбук или компьютер, представление с двумя панелями функционирует так же, как и любой элемент управления XAML. При изменении размера окна представление с двумя панелями соответственно настраивает размер и положение панелей. Оно содержит дополнительные свойства, которые устанавливаются для определения поведения элемента управления при отображении на одном экране в окне, поддерживающем изменение размера. Эти свойства более подробно описаны в следующем разделе.

## <a name="how-to-use-the-two-pane-view-control"></a>Использование элемента управления "Представление с двумя панелями"

[TwoPaneView](/uwp/api/microsoft.ui.xaml.controls.twopaneview) не обязательно должен быть корневым элементом макета вашей страницы. Вы будете часто использовать его внутри элемента управления [NavigationView](/uwp/api/microsoft.ui.xaml.controls.navigationview), который обеспечивает общую навигацию для вашего приложения. TwoPaneView адаптируется соответствующим образом независимо от того, где он находится в дереве XAML. Однако мы рекомендуем не вкладывать TwoPaneView в другой элемент TwoPaneView.

### <a name="add-content-to-the-panes"></a>Добавление содержимого на панель

Каждая панель представления с двумя панелями может содержать один элемент XAML UIElement. Чтобы добавить содержимое, как правило, необходимо разместить панель макета XAML на каждой панели, а затем добавить другие элементы управления и содержимое на панель. Панели могут изменять размер и переключаться между горизонтальным и вертикальным режимами, поэтому необходимо настроить адаптацию содержимого каждой панели к таким изменениям. Дополнительные сведения о создании адаптивного пользовательского интерфейса см. в статьях [Responsive layouts with XAML](/windows/uwp/design/layout/layouts-with-xaml) (Гибкие макеты в XAML) и [Layout panels](/windows/uwp/design/layout/layout-panels) (Панели макета).

В этом примере создается простой пользовательский интерфейс приложения, отображающего изображения и информацию, который показан ниже. При наличии пространства для двух панелей изображение и информация отображаются в отдельных панелях. (При наличии пространства лишь для одной панели необходимо переместить содержимое панели Pane2 на панель Pane1 и настроить для пользователя прокрутку, позволяющую просмотреть любое скрытое содержимое. Код для этого приведен далее в разделе _Реакция на изменения режима_.)

![tpv-add-content.png](images/two-pane-view/tpv-add-content.png)

```xaml
<muxc:TwoPaneView
    Pane1Length="*"
    ModeChanged="TwoPaneView_ModeChanged">

    <muxc:TwoPaneView.Pane1>
        <Grid x:Name="Pane1Root">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" MinHeight="40"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <CommandBar x:Name="MyCommandBar" DefaultLabelPosition="Right">
                <AppBarButton x:Name="Share" Icon="Share" Label="Share"/>
                <AppBarButton x:Name="Print" Icon="Print" Label="Print"/>
            </CommandBar>

            <ScrollViewer Grid.Row="1">
                <StackPanel x:Name="Pane1StackPanel">
                    <Image x:Name="TheImage" Source="Assets\LandscapeImage8.jpg"
                           VerticalAlignment="Top" HorizontalAlignment="Center" 
                           Margin="16,0"/>
                </StackPanel>
            </ScrollViewer>

        </Grid>
    </muxc:TwoPaneView.Pane1>

    <muxc:TwoPaneView.Pane2>
        <Grid x:Name="Pane2Root">
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto" MinHeight="40"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <StackPanel x:Name="DetailsContent" Grid.Row="1"
                Orientation="Vertical" Padding="16">

                <TextBlock Text="Mountain.jpg" Margin="0,0,0,12"
                   FontWeight="SemiBold" FontSize="18"/>

                <TextBlock Text="Date Taken:" FontWeight="SemiBold"/>
                <TextBlock Text="8/29/2019 9:55am" Margin="0,0,0,12"/>

                <TextBlock Text="Dimensions:" FontWeight="SemiBold"/>
                <TextBlock Text="1000x750" Margin="0,0,0,12"/>

                <TextBlock Text="Resolution:" FontWeight="SemiBold"/>
                <TextBlock Text="96 dpi" Margin="0,0,0,12"/>

                <TextBlock Text="Description:" FontWeight="SemiBold"/>
                <TextBlock TextWrapping="Wrap" 
                           Text="Lorem ipsum dolor sit amet."/>
            </StackPanel>
        </Grid>
    </muxc:TwoPaneView.Pane2>
</muxc:TwoPaneView>
```

### <a name="specify-which-pane-to-display"></a>Определение панели для отображения

Если представление с двумя панелями может отображать лишь одну панель, оно использует свойство [PanePriority](/uwp/api/microsoft.ui.xaml.controls.twopaneview.panepriority), чтобы определить нужную панель. По умолчанию для PanePriority установлено значение **Pane1**. Вы можете установить это свойство в XAML или в коде, как описано ниже.

```xaml
<muxc:TwoPaneView x:Name="MyTwoPaneView" PanePriority="Pane2">
```

```csharp
MyTwoPaneView.PanePriority = Microsoft.UI.Xaml.Controls.TwoPaneViewPriority.Pane2;
```

### <a name="pane-sizing"></a>Изменение размера панелей

Размер панелей на одноэкранном устройстве определяется свойствами [Pane1Length](/uwp/api/microsoft.ui.xaml.controls.twopaneview.pane1length) и [Pane2Length](/uwp/api/microsoft.ui.xaml.controls.twopaneview.pane2length). Они используют значения [GridLength](/uwp/api/windows.ui.xaml.gridlength), которые поддерживают _автоматическое_ определение размера и определение размера _по типу "звезды"_ (\*). Описание этих типов определения размера см. в разделе _Layout properties_ (Свойства макета) в статье [Responsive layouts with XAML](/windows/uwp/design/layout/layouts-with-xaml#layout-properties) (Гибкие макеты в XAML).

По умолчанию для Pane1Length установлено значение **Auto**, и размер панели масштабируется в соответствии с размером содержимого. Для Pane2Length установлена *, и эта панель использует все оставшееся пространство.

![tpv-size-default.png](images/two-pane-view/tpv-size-default.png)

_Панели с размером по умолчанию_

Значения по умолчанию удобно использовать для типичного макета основного/подробного представления, где список элементов отображается на панели Pane1, а множество подробных сведений — на панели Pane2. Однако, в зависимости от содержимого, вы можете разделить пространство по-разному. Здесь для Pane1Length установлено значение 2*, поэтому для этой панели выделено вдвое больше пространства, чем для панели Pane2.

```xaml
<muxc:TwoPaneView x:Name="MyTwoPaneView" Pane1Length="2*">
```

![tpv-size-2.png](images/two-pane-view/tpv-size-2.png)

_Панели с размером 2* и *_

> **ПРИМЕЧАНИЕ.** Как упоминалось ранее, на двухэкранном устройстве эти свойства игнорируются, а размеры панелей настраиваются автоматически в зависимости от _состояния_ устройства.

Если для панели задано автоматическое изменение размера, вы можете контролировать размер, задав высоту и ширину панели, в которой хранится содержимое. В этом случае вам может потребоваться обработать событие ModeChanged и установить ограничения высоты и ширины содержимого в соответствии с текущим режимом.

### <a name="display-in-wide-or-tall-mode"></a>Отображение в горизонтальном или вертикальном режиме

На дисплее компьютера [режим](/uwp/api/microsoft.ui.xaml.controls.twopaneview.mode) отображения представления с двумя панелями определяется свойствами [MinWideModeWidth](/uwp/api/microsoft.ui.xaml.controls.twopaneview.minwidemodewidth) и [MinTallModeHeight](/uwp/api/microsoft.ui.xaml.controls.twopaneview.mintallmodeheight). Для двух свойств задано значение по умолчанию 641px, такое же, как и для свойства [NavigationView.CompactThresholdWidth](/uwp/api/windows.ui.xaml.controls.navigationview.compactmodethresholdwidth).

Если окно:

- шире, чем значение, заданное для MinWideModeWidth, используется режим **Wide** (Горизонтальный).
- уже, чем значение, заданное для MinWideModeWidth, и выше, чем значение, заданное для MinTallModeHeight, используется режим **Tall** (Вертикальный).
- уже, чем значение, заданное для MinWideModeWidth, и короче, чем значение, заданное для MinTallModeHeight, используется режим **SinglePane**.

> **ПРИМЕЧАНИЕ.** Как упоминалось ранее, на двухэкранном устройстве эти свойства игнорируются, а размеры панелей настраиваются автоматически в зависимости от _состояния_ устройства.

#### <a name="wide-configuration-options"></a>Параметры конфигурации режима широкого окна

Представление с двумя панелями переходит в горизонтальный режим, если один дисплей шире, чем свойство MinWideModeWidth. MinWideModeWidth контролирует переход представления с двумя панелями в горизонтальный режим. Значение по умолчанию — 641px, но вы можете изменить его на любое другое. В общем, вы должны установить это свойство равным минимальной ширине вашей панели.

Если представление с двумя панелями находится в горизонтальном режиме, свойство WideModeConfiguration определяет, что будет отображено:

- **SinglePane** — одна панель (как определено свойством PanePriority). Панель занимает весь размер TwoPaneView (т. е. размер по типу "звезда" в обоих направлениях).
- **LeftRight** — панель Pane1 слева и панель Pane2 справа. Обе панели имеют размер по типу "звезда" по вертикали, ширина панели Pane1 изменяется автоматически, а панели Pane2 — равна размеру по типу "звезда".
- **RightLeft** — панель Pane1 справа и панель Pane2 слева. Обе панели имеют размер по типу "звезда" по вертикали, ширина панели Pane2 изменяется автоматически, а панели Pane1 — равна размеру по типу "звезда".

Значением по умолчанию является **LeftRight**.

| LeftRight | RightLeft |
| - | - |
| ![tpv-left-right.png](images/two-pane-view/tpv-left-right.png)  | ![tpv-right-left.png](images/two-pane-view/tpv-right-left.png)  |

> **СОВЕТ.** Если на устройстве используется язык с порядком чтения справа налево (RTL), представление с двумя панелями автоматически меняет порядок: RightLeft преобразуется в LeftRight, а LeftRight — в RightLeft.

#### <a name="tall-configuration-options"></a>Параметры конфигурации вертикального режима

Представление с двумя панелями переходит в вертикальный режим, если размер одного дисплея уже значения MinWideModeWidth и выше значения MinTallModeHeight. Значение по умолчанию — 641px, но вы можете изменить его на любое другое. В общем, вы должны установить это свойство равным минимальной высоте вашей панели.

Если представление с двумя панелями находится в горизонтальном режиме, свойство TallLayout определяет, что будет отображено:

- **SinglePane** — одна панель (как определено свойством PanePriority). Панель занимает весь размер TwoPaneView (т. е. размер по типу "звезда" в обоих направлениях).
- **TopBottom** — панель Pane1 сверху и панель Pane2 справа. Обе панели имеют размер по типу "звезда" по горизонтали, высота панели Pane1 изменяется автоматически, а панели Pane2 — равна размеру по типу "звезда".
- **BottomTop** — панель Pane1 справа и панель Pane2 слева. Обе панели имеют размер по типу "звезда" по горизонтали, высота панели Pane2 изменяется автоматически, а панели Pane1 — равна размеру по типу "звезда".

По умолчанию используется **TopBottom**.

| TopBottom | BottomTop |
| - | - |
| ![tpv-top-bottom.png](images/two-pane-view/tpv-top-bottom.png)  | ![tpv-bottom-top.png](images/two-pane-view/tpv-bottom-top.png)  |

#### <a name="special-values-for-minwidemodewidth-and-mintallmodeheight"></a>Специальные значения для MinWideModeWidth и MinTallModeHeight

С помощью свойства MinWideModeWidth можно предотвратить переход представления с двумя панелями в горизонтальный режим — просто установите для него значение [Double.PositiveInfinity](/dotnet/api/system.double.positiveinfinity?view=dotnet-uwp-10.0).

Если для свойства MinTallModeHeight задано значение [Double.PositiveInfinity](/dotnet/api/system.double.positiveinfinity?view=dotnet-uwp-10.0), оно не позволит представлению с двумя панелями переходить в вертикальный режим.

Если для MinTallModeHeight задано значение 0, представление с двумя панелями не будет переходить в режим SinglePane.

#### <a name="responding-to-mode-changes"></a>Реакция на изменения режима

С помощью свойства [Mode](/uwp/api/microsoft.ui.xaml.controls.twopaneview.mode) только для чтения можно получить текущий режим отображения. Всякий раз, когда представление с двумя панелями меняет отображаемую панель или панели, событие [ModeChanged](/uwp/api/microsoft.ui.xaml.controls.twopaneview.modechanged) возникает до отображения обновленного содержимого. Вы можете обработать событие, чтобы оно реагировало на изменения режима отображения.

Один из способов использования этого события — обновить пользовательский интерфейс приложения, позволив пользователям просматривать все содержимое в режиме SinglePane. Например, образец приложения содержит основную панель (изображение) и информационную панель.

![tpv-add-content.png](images/two-pane-view/tpv-add-content.png)

_Горизонтальный режим_

Если для отображения одной панели достаточно пространства, переместите содержимое панели Pane2 на панель Pane1, позволяя пользователю прокручивать страницу для просмотра всего содержимого. Это выглядит следующим образом:

![tpv-mode-change.png](images/two-pane-view/tpv-mode-change.png)

_Режим SinglePane_

```csharp
 private void TwoPaneView_ModeChanged(Microsoft.UI.Xaml.Controls.TwoPaneView sender, object args)
 {
     ((Panel)DetailsContent.Parent).Children.Remove(DetailsContent);
     ((Panel)MyCommandBar.Parent).Children.Remove(MyCommandBar);

     // Single pane
     if (sender.Mode == Microsoft.UI.Xaml.Controls.TwoPaneViewMode.SinglePane)
     {
         // Add the command bar and details content to Pane1.
         Pane1StackPanel.Children.Add(DetailsContent);
         Pane1Root.Children.Add(MyCommandBar);
     }
     // Dual pane.
     else
     {
         // Wide mode.
         if (sender.Mode == Microsoft.UI.Xaml.Controls.TwoPaneViewMode.Wide)
         {
             // Put the command bar in Pane2.
             Pane2Root.Children.Add(MyCommandBar);
         }
         // Tall mode.
         else if (sender.Mode == Microsoft.UI.Xaml.Controls.TwoPaneViewMode.Tall)
         {
             // Put the command bar in Pane1
             Pane1Root.Children.Add(MyCommandBar);
         }

         // Put details content in Pane2.
         Pane2Root.Children.Add(DetailsContent);
     }
 }
```

## <a name="dos-and-donts"></a>Что рекомендуется и что не рекомендуется делать

- Всегда используйте представление с двумя панелями, позволяя приложению использовать преимущества двух дисплеев и больших экранов.
- Не помещайте представление с двумя панелями в другое такое же представление.

## <a name="related-articles"></a>Похожие статьи

- [Макет](../layout/index.md)
- [Create apps for dual-screen devices](/dual-screen) (Создание приложений для двухэкранных устройств)
- [Introduction to dual-screen devices](/dual-screen/introduction) (Общие сведения о двухэкранных устройствах)
