---
author: stevewhims
description: В этом разделе описана этапы создания простого пользовательского элемента управления с помощью C + +/ WinRT. Вы можете создать на информации для создания собственных функциональных возможностей и настраиваемых элементов управления пользовательского интерфейса.
title: Создание пользовательских (на основе шаблона) элементов управления XAML с помощью C++/WinRT
ms.author: stwhi
ms.date: 10/03/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c ++, cpp, winrt, проекция, XAML, пользовательский, шаблон, элемент управления
ms.localizationpriority: medium
ms.openlocfilehash: 539876113ce2aba563cfa65b13571cbf3998cc2d
ms.sourcegitcommit: 1c6325aa572868b789fcdd2efc9203f67a83872a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "4749895"
---
# <a name="xaml-custom-templated-controls-with-cwinrt"></a>Создание пользовательских (на основе шаблона) элементов управления XAML с помощью C++/WinRT

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), см. в разделе [Использование API-интерфейсов в C + +/ WinRT](consume-apis.md) и [Создание API-интерфейсов в C + +/ WinRT](author-apis.md).

Одним из наиболее важных возможностей универсальной платформы Windows (UWP) является гибкость, что в стек пользовательского интерфейса (UI) для создания пользовательских элементов управления, в зависимости от типа XAML [**элемента управления**](/uwp/api/windows.ui.xaml.controls.control) . Платформа пользовательского интерфейса XAML предоставляет функции, такие как [пользовательские свойства зависимостей](/windows/uwp/xaml-platform/custom-dependency-properties) и присоединенных свойств и [шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates), которые упрощают создание многофункциональных и настраиваемых элементов управления. В этом разделе описана процедура инструкции по созданию пользовательского элемента управления (на основе шаблона) с помощью C + +/ WinRT.

## <a name="create-a-blank-app-bglabelcontrolapp"></a>Создайте пустое приложение (BgLabelControlApp)
Начните с создания нового проекта в Microsoft Visual Studio. Создание **Visual C++** > **Универсальные** > **пустое приложение (C + +/ WinRT)** проект и назовите его *BgLabelControlApp*.

Мы создадим новый класс, представляющий пользовательского элемента управления (на основе шаблона). Класс создается и используется в рамках одной и той же единицы компиляции. Но мы хотим иметь возможность создавать экземпляры этот класс из разметки XAML, поэтому, это будет представлять собой класс среды выполнения. Кроме того, мы применим C++/WinRT для его создания и использования.

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

Выше показан шаблон, которому необходимо следовать при объявлении свойства зависимостей (DP). Существует два значения для каждого DP. Во-первых объявите статическое свойство только для чтения типа [**DependencyProperty**](/uwp/api/windows.ui.xaml.dependencyproperty). Он имеет имя вашего DP, а также *Свойства*. Это статическое свойство используем в вашей реализации. Во-вторых вы объявляете свойство экземпляра для чтения и записи с помощью типа и имя вашего DP.

> [!NOTE]
> Если вы хотите DP с типом с плавающей запятой, сделать ее `double` (`Double` в [MIDL 3.0](/uwp/midl-3/)). Объявления и реализации DP типа `float` (`Single` в MIDL), а затем задать значение для этого DP в разметке XAML, приводит к ошибке *не удалось создать «Windows.Foundation.Single» из текста "<NUMBER>"*.

Сохраните файл и выполните сборку проекта. Во время сборки запускается инструмент `midl.exe` для создания файла метаданных среды выполнения Windows (`\BgLabelControlApp\Debug\BgLabelControlApp\Unmerged\BgLabelControl.winmd`), описывающего класс среды выполнения. Затем запускается средство `cppwinrt.exe` для создания файлов исходного кода для поддержки создания и использования вашего класса среды выполнения. Эти файлы включают заглушки для начала реализации класса среды выполнения **BgLabelControl** , объявленного в вашем IDL. Это заглушки `\BgLabelControlApp\BgLabelControlApp\Generated Files\sources\BgLabelControl.h` и `BgLabelControl.cpp`.

Скопируйте файлы заглушек `BgLabelControl.h` и `BgLabelControl.cpp` из `\BgLabelControlApp\BgLabelControlApp\Generated Files\sources\` в папку проекта `\BgLabelControlApp\BgLabelControlApp\`. Убедитесь, что в **Обозревателе решений** включена функция **Показать все файлы**. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**.

## <a name="implement-the-bglabelcontrol-custom-control-class"></a>Реализуйте класс пользовательского элемента управления **BgLabelControl**
Теперь давайте откроем `\BgLabelControlApp\BgLabelControlApp\BgLabelControl.h` и `BgLabelControl.cpp` и реализуем класс среды выполнения. В `BgLabelControl.h`, измените конструктор, чтобы задать ключ стиля по умолчанию, реализовать **метку** и **LabelProperty**, добавить обработчик статических событий с именем **OnLabelChanged** для обработки изменений значения свойства зависимостей и добавьте частный член для хранения резервное поле для **LabelProperty**.

После их добавления ваш `BgLabelControl.h` выглядит следующим образом.

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

    static void OnLabelChanged(Windows::UI::Xaml::DependencyObject const&, Windows::UI::Xaml::DependencyPropertyChangedEventArgs const&);

private:
    static Windows::UI::Xaml::DependencyProperty m_labelProperty;
};
...
```

В `BgLabelControl.cpp`, определите статические члены следующим образом.

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

void BgLabelControl::OnLabelChanged(Windows::UI::Xaml::DependencyObject const& d, Windows::UI::Xaml::DependencyPropertyChangedEventArgs const& /* e */)
{
    if (BgLabelControlApp::BgLabelControl theControl{ d.try_as<BgLabelControlApp::BgLabelControl>() })
    {
        // Call members of the projected type via theControl.

        BgLabelControlApp::implementation::BgLabelControl* ptr{ winrt::get_self<BgLabelControlApp::implementation::BgLabelControl>(theControl) };
        // Call members of the implementation type via ptr.
    }
}
...
```

В этом пошаговом руководстве мы не используем **OnLabelChanged**. Но есть таким образом, можно узнать, как зарегистрировать свойство зависимостей с помощью обратного вызова при изменении свойства. Реализация **OnLabelChanged** также показано, как получить производный тип проекции от базового типа проекции (базовый тип проекции — это **DependencyObject**, в данном случае). И показано, как затем получить указатель на тип, реализующий тип проекции. Эта операция второй естественным образом будет только возможно в проекте, который реализует проецируемого типа (то есть проект, который реализует класс среды выполнения).

> [!NOTE]
> Если вы еще не установили пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии, необходимо вызвать [**winrt::from_abi**](/uwp/cpp-ref-for-winrt/from-abi) в обработчик события изменения свойств зависимостей выше, вместо [**winrt::get_self**](/uwp/cpp-ref-for-winrt/get-self).

## <a name="design-the-default-style-for-bglabelcontrol"></a>Проектирование стиль по умолчанию **BgLabelControl**

В его конструктор **BgLabelControl** устанавливает ключ стиля по умолчанию для себя. Но какие *— это* стиль по умолчанию? Пользовательский элемент управления (на основе шаблона) должен иметь стиль по умолчанию&mdash;с шаблоном элемента управления по умолчанию&mdash;которой его можно использовать для отрисовки сам с на случай, если потребитель элемента управления не задать стиль и шаблон. В этом разделе мы добавим файла разметки в проект, содержащий наших стиль по умолчанию.

В узле проекта создайте новую папку и назовите его «Темы». В разделе `Themes`, добавить новый элемент типа **Visual C++** > **XAML** > **Представление XAML**и назовите его «Generic.xaml». Имена папок и файлов должны быть следующим образом в порядке для платформы XAML найти стиль по умолчанию для пользовательского элемента управления. Удалите содержимое по умолчанию `Generic.xaml`и вставьте в разметке ниже.

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

В этом случае единственным свойством, которое задает стиль по умолчанию — это шаблон элемента управления. Шаблон состоит из квадрат (которого фона привязано к свойству **фона** , которой все экземпляры типа XAML [**элемента управления**](/uwp/api/windows.ui.xaml.controls.control) ) и текстовый элемент (текст которого привязано к свойству зависимостей **BgLabelControl::Label** ).

## <a name="add-an-instance-of-bglabelcontrol-to-the-main-ui-page"></a>Добавьте экземпляр объекта **BgLabelControl** на главную страницу пользовательского интерфейса

Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Сразу после элемента **Button** (внутри **StackPanel**) добавьте следующую разметку.

```xaml
<local:BgLabelControl Background="Red" Label="Hello, World!"/>
```

Кроме того, добавьте следующий директиву для `MainPage.h` таким образом, учитывать тип пользовательского элемента управления **BgLabelControl** типа **MainPage** (сочетание компиляции разметки XAML и императивном коде).

```cppwinrt
// MainPage.h
...
#include "BgLabelControl.h"
...
```

Выполните сборку и запуск проекта. Вы увидите, что шаблон элемента управления по умолчанию — это привязка кисть фона и метку, экземпляра **BgLabelControl** в разметке.

В этом пошаговом руководстве показали простой пример пользовательского элемента управления (на основе шаблона) в C + +/ WinRT. Вы можете сделать собственные пользовательские элементы управления произвольно полнофункциональной и полнофункциональным. Пример пользовательского элемента управления может занять форме что-то же сложная, как редактируемых данных сетки, видеопроигрыватель или визуализатор трехмерной геометрии.

## <a name="implementing-overridable-functions-such-as-measureoverride-and-onapplytemplate"></a>Реализация *переопределяемые* функции, такие как **MeasureOverride** и **OnApplyTemplate**

Можно создать пользовательский элемент управления, производный от класса среды выполнения [**элемента управления**](/uwp/api/windows.ui.xaml.controls.control) , который в свою очередь Дополнительно является производным от классов базовой среды выполнения. Также существуют переопределяемые методы элементов **управления**, [**FrameworkElement**](/uwp/api/windows.ui.xaml.frameworkelement)и вы можете переопределить в производном классе [**UIElement**](/uwp/api/windows.ui.xaml.uielement) . Ниже приведен пример кода, показывающий, как это сделать.

```cppwinrt
struct BgLabelControl : BgLabelControlT<BgLabelControl>
{
...
    // Control overrides.
    void OnPointerPressed(Windows::UI::Xaml::Input::PointerRoutedEventArgs const& /* e */) const { ... };

    // FrameworkElement overrides.
    Windows::Foundation::Size MeasureOverride(Windows::Foundation::Size const& /* availableSize */) const { ... };
    void OnApplyTemplate() const { ... };

    // UIElement overrides.
    Windows::UI::Xaml::Automation::Peers::AutomationPeer OnCreateAutomationPeer() const { ... };
...
};
```

*Переопределяемые* функции имитируют по-разному в различных языковых проекции. В C#, например, переопределяемые функции обычно отображаются в виде защищенные виртуальные функции. В C + +/ WinRT, они не защищенные ни виртуальный, но по-прежнему можно переопределить их и предоставлять реализации, как показано выше.

## <a name="important-apis"></a>Важные API
* [Класс элементов управления](/uwp/api/windows.ui.xaml.controls.control)
* [Класс DependencyProperty](/uwp/api/windows.ui.xaml.dependencyproperty)
* [Класс FrameworkElement](/uwp/api/windows.ui.xaml.frameworkelement)
* [Класс UIElement](/uwp/api/windows.ui.xaml.uielement)

## <a name="related-topics"></a>Еще по теме
* [Шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates)
* [Пользовательские свойства зависимостей](/windows/uwp/xaml-platform/custom-dependency-properties)
