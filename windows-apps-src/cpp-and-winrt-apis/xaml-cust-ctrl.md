---
author: stevewhims
description: В этом разделе описываются этапы создания простого пользовательского элемента управления с помощью C + +/ WinRT. Можно создать информацию для создания собственных функциональных возможностей и настраиваемые элементы управления пользовательского интерфейса.
title: (XAML шаблонных) элементов управления с помощью C + +/ WinRT
ms.author: stwhi
ms.date: 08/01/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартный, c ++, cpp, winrt, проекции, XAML, настраиваемые, шаблонного, элемент управления
ms.localizationpriority: medium
ms.openlocfilehash: c108175c66d27b2cdbd910a0f7653ca1befb68e9
ms.sourcegitcommit: 9a17266f208ec415fc718e5254d5b4c08835150c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2018
ms.locfileid: "2885568"
---
# <a name="xaml-custom-templated-controls-with-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>(XAML шаблонных) элементов управления с [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)

Одним из наиболее эффективные функции платформы универсальные Windows (UWP) является гибкость, предоставляющий стек пользовательского интерфейса (UI) для создания пользовательских элементов управления на основе типа XAML [элемента управления](/uwp/api/windows.ui.xaml.controls.control) . Платформа пользовательского интерфейса XAML предоставляет функции, например [настраиваемых свойствах зависимости](/windows/uwp/xaml-platform/custom-dependency-properties) и вложенные свойства и [шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates), которые упрощают создание многофункциональных и настраиваемые элементы управления. В этом разделе описываются шаги по созданию пользовательского элемента управления (шаблон) с помощью C + +/ WinRT.

## <a name="create-a-blank-app-bglabelcontrolapp"></a>Создание пустого приложения (BgLabelControlApp)
Начните с создания нового проекта в Microsoft Visual Studio. Создание **пустой приложения Visual C++ (C + +/ WinRT)** project и присвойте ему имя *BgLabelControlApp*.

Мы будем создавать новый класс для представления пользовательского элемента управления (шаблон). Класс создается и используется в рамках одной и той же единицы компиляции. Но необходимо создать экземпляр этого класса из разметки XAML, а также для, причины, это будет классом среды выполнения. Кроме того, мы применим C++/WinRT для его создания и использования.

Первый шаг при создании нового класса среды выполнения — добавление в проект нового элемента **файла Midl (.idl)**. Назовите его `BgLabelControl.idl`. Удалите содержимое `BgLabelControl.idl` по умолчанию вставьте в это объявление класса среды выполнения.

```idl
// BgLabelControl.idl
namespace BgLabelControlApp
{
    runtimeclass BgLabelControl : Windows.UI.Xaml.Controls.Control
    {
        BgLabelControl();
        static Windows.UI.Xaml.DependencyProperty LabelProperty{ get; };
        String Label;
    }
}
```

Выше показан шаблон, который нужно следовать при объявлении свойства зависимостей (DP). Существует два элемента для каждого DP. Во-первых объявите только для чтения статическое свойство типа [DependencyProperty](/uwp/api/windows.ui.xaml.dependencyproperty). Она имеет имя DP и *Свойства*. Это статическое свойство используется в реализации. Во-вторых объявите свойство экземпляра чтения и записи с типом и именем вашего DP.

> [!NOTE]
> Если вы хотите DP с типом с плавающей запятой, создайте `double` (`Double` в [MIDL 3.0](/uwp/midl-3/)). Объявление и реализация DP типа `float` (`Single` в MIDL), и затем задать значение для этого DP в разметке XAML, приводит к ошибке *не удалось создать Windows.Foundation.Single из текста "<NUMBER>"*.

Сохраните файл и выполните сборку проекта. Во время сборки запускается инструмент `midl.exe` для создания файла метаданных среды выполнения Windows (`\BgLabelControlApp\Debug\BgLabelControlApp\Unmerged\BgLabelControl.winmd`), описывающего класс среды выполнения. Затем запускается средство `cppwinrt.exe` для создания файлов исходного кода для поддержки создания и использования вашего класса среды выполнения. Эти файлы содержат заглушки для начала реализации класса среды выполнения **BgLabelControl** , указанному в вашей IDL. Это заглушки `\BgLabelControlApp\BgLabelControlApp\Generated Files\sources\BgLabelControl.h` и `BgLabelControl.cpp`.

Скопируйте файлы заглушек `BgLabelControl.h` и `BgLabelControl.cpp` из `\BgLabelControlApp\BgLabelControlApp\Generated Files\sources\` в папку проекта `\BgLabelControlApp\BgLabelControlApp\`. Убедитесь, что в **Обозревателе решений** включена функция **Показать все файлы**. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**.

## <a name="implement-the-bglabelcontrol-custom-control-class"></a>Реализация класса пользовательского элемента управления **BgLabelControl**
Теперь давайте откроем `\BgLabelControlApp\BgLabelControlApp\BgLabelControl.h` и `BgLabelControl.cpp` и реализуем класс среды выполнения. В `BgLabelControl.h`, измените конструктор, чтобы задать ключ стиля по умолчанию, реализации **Label** и **LabelProperty**, добавление обработчика событий статического с именем **OnLabelChanged** для обработки изменения значения свойства зависимостей и добавьте закрытый элемент Чтобы сохранить резервного поля для **LabelProperty**.

В этом пошаговом руководстве мы не будет использовать **OnLabelChanged**. Однако есть ли, чтобы вы могли видеть регистрирует свойство зависимостей с помощью изменения свойства обратного вызова.

После добавления, вашей `BgLabelControl.h` выглядит следующим образом.

```cppwinrt
// BgLabelControl.h
...
struct BgLabelControl : BgLabelControlT<BgLabelControl>
{
    BgLabelControl() { DefaultStyleKey(winrt::box_value(L"BgLabelControlApp.BgLabelControl")); }

    winrt::hstring Label()
    {
        return winrt::unbox_value<winrt::hstring>(GetValue(m_labelProperty));
    }

    void Label(winrt::hstring const& value)
    {
        SetValue(m_labelProperty, winrt::box_value(value));
    }

    static Windows::UI::Xaml::DependencyProperty LabelProperty() { return m_labelProperty; }

    static void OnLabelChanged(Windows::UI::Xaml::DependencyObject const& d, Windows::UI::Xaml::DependencyPropertyChangedEventArgs const& e);

private:
    static Windows::UI::Xaml::DependencyProperty m_labelProperty;
};
...
```

В `BgLabelControl.cpp`, определение статические члены следующим образом.

```cppwinrt
// BgLabelControl.cpp
...
Windows::UI::Xaml::DependencyProperty BgLabelControl::m_labelProperty =
    Windows::UI::Xaml::DependencyProperty::Register(
        L"Label",
        winrt::xaml_typename<winrt::hstring>(),
        winrt::xaml_typename<BgLabelControlApp::BgLabelControl>(),
        Windows::UI::Xaml::PropertyMetadata{ winrt::box_value(L"default label"), Windows::UI::Xaml::PropertyChangedCallback{ &BgLabelControl::OnLabelChanged } }
);

void BgLabelControl::OnLabelChanged(Windows::UI::Xaml::DependencyObject const& d, Windows::UI::Xaml::DependencyPropertyChangedEventArgs const& e) {}
...
```

## <a name="design-the-default-style-for-bglabelcontrol"></a>Проектирование стиль по умолчанию для **BgLabelControl**

В конструкторе **BgLabelControl** задает ключ стиля по умолчанию для себя. Но какие *— это* стиль по умолчанию? Необходимо иметь стиль по умолчанию (шаблонного) пользовательского элемента управления&mdash;себя шаблон управления по умолчанию&mdash;который мог бы использоваться для отображения самого с в случае, если потребитель элемента управления не устанавливает стиль и/или шаблона. В этом разделе мы добавим файл разметки в проект, содержащий наших стиль по умолчанию.

В разделе узел проекта создать новую папку с именем «Темы». В разделе `Themes`, добавьте новый элемент типа **Visual C++** > **XAML** > **XAML-представление**и присвойте ему имя «Generic.xaml». Имена папок и файлов должны быть следующим образом, чтобы framework XAML найти стиль по умолчанию для пользовательского элемента управления. Удалите содержимое по умолчанию `Generic.xaml`и вставьте в следующей разметке.

```xaml
<!-- \Themes\Generic.xaml -->
<ResourceDictionary
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:local="using:BgLabelControlApp">

    <Style TargetType="local:BgLabelControl" >
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="local:BgLabelControl">
                    <Grid Width="100" Height="100" Background="{TemplateBinding Background}">
                        <TextBlock HorizontalAlignment="Center" VerticalAlignment="Center" Text="{TemplateBinding Label}"/>
                    </Grid>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>
</ResourceDictionary>
```

В этом случае единственный параметр, который задает стиль по умолчанию — это шаблон элемента управления. Шаблон состоит из квадрат (, фон привязан к свойству **фона** , для которого все экземпляры типа XAML [элемента управления](/uwp/api/windows.ui.xaml.controls.control) ) и элемент текст (текст которого привязано к свойству зависимостей **BgLabelControl::Label** ).

## <a name="add-an-instance-of-bglabelcontrol-to-the-main-ui-page"></a>Добавить экземпляр **BgLabelControl** на главную страницу пользовательского интерфейса

Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Сразу после элемента **Button** (внутри **StackPanel**) добавьте следующую разметку.

```xaml
<local:BgLabelControl Background="Red" Label="Hello, World!"/>
```

Кроме того, добавьте следующую директиву для `MainPage.h` , чтобы принять во внимание тип настраиваемого элемента управления **BgLabelControl** **MainPage** типа (сочетание компиляция разметки и принудительной кода XAML).

```cppwinrt
// MainPage.h
...
#include "BgLabelControl.h"
...
```

Выполните сборку и запуск проекта. Вы увидите, что шаблон элемента управления по умолчанию — это привязка кисть фона и метки экземпляра **BgLabelControl** в разметке.

В этом пошаговом руководстве показано простой пример настраиваемого элемента управления (шаблон) в C + +/ WinRT. Пользовательские элементы управления можно сделать произвольно более полные и полнофункциональных. К примеру пользовательского элемента управления может принимать вид из какой-либо элемент сложным, как сетке данных, проигрыватель видео или средства визуализации трехмерной геометрии.

## <a name="important-apis"></a>Важные API
* [Элемент управления](/uwp/api/windows.ui.xaml.controls.control)
* [DependencyProperty](/uwp/api/windows.ui.xaml.dependencyproperty)

## <a name="related-topics"></a>Еще по теме
* [Шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates)
* [Пользовательские свойства зависимостей](/windows/uwp/xaml-platform/custom-dependency-properties)
