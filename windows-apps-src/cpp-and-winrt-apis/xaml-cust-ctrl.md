---
description: Этот раздел поможет выполнить этапы создания простого пользовательского элемента управления с помощью C + +/ WinRT. Вы можете создать на сведения для создания собственных многофункциональных и настраиваемых элементов управления пользовательского интерфейса.
title: Создание пользовательских (на основе шаблона) элементов управления XAML с помощью C++/WinRT
ms.date: 10/03/2018
ms.topic: article
keywords: Windows 10, uwp, standard, c ++, cpp, winrt, проекции, XAML, пользовательские, шаблон, элемент управления
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: ce4f7eea074233c625a2cc92ef773f0b06c2be9f
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57635149"
---
# <a name="xaml-custom-templated-controls-with-cwinrt"></a>Создание пользовательских (на основе шаблона) элементов управления XAML с помощью C++/WinRT

> [!IMPORTANT]
> Основные понятия и термины, которые поддерживают понимание того, как использовать и создавать классы среды выполнения с [C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), см. в разделе [использовать API-интерфейсы с использованием C + +/ WinRT](consume-apis.md) и [API-интерфейсы автора с использованием C + +/ WinRT](author-apis.md).

Одним из наиболее мощных функций из универсальной платформы Windows (UWP) является гибкость, обеспечивающая стека пользовательского интерфейса (UI) для создания пользовательских элементов управления в соответствии XAML [ **управления** ](/uwp/api/windows.ui.xaml.controls.control) типа. Инфраструктура пользовательского интерфейса XAML предоставляет функции, такие как [пользовательские свойства взаимозависимости](/windows/uwp/xaml-platform/custom-dependency-properties) и [присоединенных свойств](/windows/uwp/xaml-platform/custom-attached-properties), и [шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates), который упрощают создание многофункциональные и настраиваемых элементов управления. Этот раздел поможет выполнить этапы создания пользовательского элемента управления (шаблон) с использованием C + +/ WinRT.

## <a name="create-a-blank-app-bglabelcontrolapp"></a>Создайте пустое приложение (BgLabelControlApp)
Начните с создания нового проекта в Microsoft Visual Studio. Создание **Visual C++** > **универсальной Windows** > **пустое приложение (C + +/ WinRT)** проект и назовите его *BgLabelControlApp* . В следующем разделе этой статьи, вы будете перенаправлены для построения проекта (не создавать до тех пор).

Мы собираемся создавать новый класс для представления пользовательского элемента управления (шаблон). Класс создается и используется в рамках одной и той же единицы компиляции. Но мы хотим иметь возможность создать экземпляр этого класса из разметки XAML и на что-либо причине, это будет классом среды выполнения. Кроме того, мы применим C++/WinRT для его создания и использования.

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

Выше показан шаблон, которому необходимо следовать при объявлении свойства зависимостей (DP). Есть два вида для каждой точки Распространения. Сначала объявите статическое свойство только для чтения типа [ **DependencyProperty**](/uwp/api/windows.ui.xaml.dependencyproperty). Он имеет имя вашей DP плюс *свойство*. Это статическое свойство используется в реализации. Во-вторых тип и имя вашего DP объявлении свойства чтения и записи. Если вы хотите создавать *присоединенное свойство* (а не точки Распространения), затем см. в разделе примеров кода в [настраиваемые присоединенные свойства](/windows/uwp/xaml-platform/custom-attached-properties).

> [!NOTE]
> Точки Распространения с типом с плавающей запятой, затем сделать его `double` (`Double` в [MIDL 3.0](/uwp/midl-3/)). Объявление и реализация DP типа `float` (`Single` в MIDL), а затем задать значение для этой точки Распространения в разметке XAML, приводит к ошибке *не удалось создать «Windows.Foundation.Single» из текста "<NUMBER>"*.

Сохраните файл и выполните сборку проекта. Во время сборки запускается инструмент `midl.exe` для создания файла метаданных среды выполнения Windows (`\BgLabelControlApp\Debug\BgLabelControlApp\Unmerged\BgLabelControl.winmd`), описывающего класс среды выполнения. Затем запускается средство `cppwinrt.exe` для создания файлов исходного кода для поддержки создания и использования вашего класса среды выполнения. Эти файлы включают заглушки для начала работы реализации **BgLabelControl** класса среды выполнения, объявленного в вашей IDL. Это заглушки `\BgLabelControlApp\BgLabelControlApp\Generated Files\sources\BgLabelControl.h` и `BgLabelControl.cpp`.

Скопируйте файлы заглушек `BgLabelControl.h` и `BgLabelControl.cpp` из `\BgLabelControlApp\BgLabelControlApp\Generated Files\sources\` в папку проекта `\BgLabelControlApp\BgLabelControlApp\`. В **Обозревателе решений**убедитесь, что функция **Показать все файлы** включена. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**.

## <a name="implement-the-bglabelcontrol-custom-control-class"></a>Реализуйте **BgLabelControl** настраиваемого класса элемента управления
Теперь давайте откроем `\BgLabelControlApp\BgLabelControlApp\BgLabelControl.h` и `BgLabelControl.cpp` и реализуем класс среды выполнения. В `BgLabelControl.h`, измените конструктор для задания ключа, реализуйте стиля по умолчанию **метка** и **LabelProperty**, добавьте обработчик статическое событие с именем **OnLabelChanged** для обрабатывать изменения значения свойства зависимостей, и добавьте закрытый элемент для хранения резервное поле для **LabelProperty**.

После добавления, ваш `BgLabelControl.h` выглядит следующим образом.

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

В `BgLabelControl.cpp`, определять такие статические члены.

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

В этом пошаговом руководстве мы не будем использовать **OnLabelChanged**. Но существует таким образом, чтобы понять, как для регистрации свойства зависимостей с обратным вызовом для изменения свойств. Реализация **OnLabelChanged** также показано, как получить проецируемых производный тип от базового типа проецируемых (базовый тип проецируемых **DependencyObject**, в данном случае). И показано, как получить указатель на тип, реализующий проецированном типе. Что вторая операция естественным образом только можно будет в проекте, который реализует проецированный тип (то есть проект, реализующий класс среды выполнения).

> [!NOTE]
> Если вы еще не установили пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии, затем необходимо вызвать [ **winrt::from_abi** ](/uwp/cpp-ref-for-winrt/from-abi) в свойство измененные обработчика события зависимости выше, вместо [ **winrt::get_self**](/uwp/cpp-ref-for-winrt/get-self).

## <a name="design-the-default-style-for-bglabelcontrol"></a>Проектирование стиль по умолчанию для **BgLabelControl**

В своем конструкторе **BgLabelControl** задает ключ стиля по умолчанию для себя. Но как насчет *является* стиль по умолчанию? Пользовательский элемент управления (шаблон) должен иметь стиль по умолчанию&mdash;с шаблоном элемента управления по умолчанию&mdash;которого его можно использовать для подготовки к просмотру с регистром, потребитель элемента управления не задать стиль и шаблон. В этом разделе мы добавим файл разметки в проект, содержащий наши стиль по умолчанию.

В разделе узел проекта создайте новую папку и назовите его «Темы». В разделе `Themes`, добавьте новый элемент типа **Visual C++** > **XAML** > **представление XAML**и назовите его «Generic.xaml». Имена файлов и папок обязательно должны иметь следующий вид в порядке для платформы XAML в поиске стиля по умолчанию для пользовательского элемента управления. Удалите содержимое по умолчанию `Generic.xaml`и вставьте в разметке ниже.

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

В этом случае единственное свойство, которое задает стиль по умолчанию — это шаблон элемента управления. Шаблон состоит из квадрата (фон которого привязано к **фона** свойство, все экземпляры XAML [ **управления** ](/uwp/api/windows.ui.xaml.controls.control) тип иметь) и текстовый элемент (которой текст привязан к **BgLabelControl::Label** свойство зависимостей).

## <a name="add-an-instance-of-bglabelcontrol-to-the-main-ui-page"></a>Добавьте экземпляр **BgLabelControl** на главную страницу пользовательского интерфейса

Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Сразу после **кнопку** элемент (внутри **StackPanel**), добавьте следующую разметку.

```xaml
<local:BgLabelControl Background="Red" Label="Hello, World!"/>
```

Кроме того, добавьте директиву, чтобы включить следующие `MainPage.h` таким образом, чтобы **MainPage** тип (сочетание компиляции разметки XAML и императивного кода) известно о **BgLabelControl** тип настраиваемого элемента управления. Если вы хотите использовать **BgLabelControl** с другой страницы XAML, тогда добавьте этот же include-директива в файл заголовка для этой страницы, слишком. Или, в качестве альтернативы просто поместите одной директивы #include в файл предкомпилированного заголовка.

```cppwinrt
// MainPage.h
...
#include "BgLabelControl.h"
...
```

Выполните сборку и запуск проекта. Вы увидите, что шаблон элемента управления по умолчанию привязка кисть фона, а также метки, **BgLabelControl** экземпляра в разметке.

В этом пошаговом руководстве показали простой пример пользовательского элемента управления (шаблон) в C + +/ WinRT. Собственные пользовательские элементы управления можно сделать произвольно широкие возможности и полнофункциональным. Например пользовательский элемент управления может принимать форму из что-то непростого, скажем сетку редактируемых данных, проигрыватель видео или визуализатора трехмерной геометрии.

## <a name="implementing-overridable-functions-such-as-measureoverride-and-onapplytemplate"></a>Реализация *переопределяемый* функции, такие как **MeasureOverride** и **OnApplyTemplate**

Наследовать из пользовательского элемента управления [ **управления** ](/uwp/api/windows.ui.xaml.controls.control) класса среды выполнения, который сам дальнейшей является производным от класса базовой среды выполнения. И переопределяемые методы **управления**, [ **FrameworkElement**](/uwp/api/windows.ui.xaml.frameworkelement), и [ **UIElement** ](/uwp/api/windows.ui.xaml.uielement) что можно переопределить в производном классе. Ниже приведен пример кода, показывающий, как это сделать.

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

*Переопределяемый* функций представляют по-разному в разных языковых проекций. В C#, например, переопределяемые функции обычно отображаются как защищенные виртуальные функции. В C + +/ WinRT, они являются виртуального ни защищенных, но по-прежнему можно переопределить их и предоставить собственную реализацию, как показано выше.

## <a name="important-apis"></a>Важные API
* [Класс элемента управления](/uwp/api/windows.ui.xaml.controls.control)
* [Класс DependencyProperty](/uwp/api/windows.ui.xaml.dependencyproperty)
* [FrameworkElement-класс](/uwp/api/windows.ui.xaml.frameworkelement)
* [UIElement-класс](/uwp/api/windows.ui.xaml.uielement)

## <a name="related-topics"></a>Статьи по теме
* [Шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates)
* [Пользовательские свойства взаимозависимости](/windows/uwp/xaml-platform/custom-dependency-properties)
