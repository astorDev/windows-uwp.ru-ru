---
description: В этом разделе рассматриваются этапы создания простого пользовательского элемента управления с помощью C++/WinRT. С помощью приведенной информации можно создавать собственные многофункциональные и настраиваемые элементы пользовательского интерфейса.
title: Создание пользовательских (на основе шаблона) элементов управления XAML с помощью C++/WinRT
ms.date: 04/24/2019
ms.topic: article
keywords: windows 10, uwp, standard, c++, cpp, winrt, projection, XAML, custom, templated, control
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: 6acbd62a8fa75eefb39598dd5bbb6ec1270388c4
ms.sourcegitcommit: cc9f5a16386be78c12821a975e43497a0693abba
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72578174"
---
# <a name="xaml-custom-templated-controls-with-cwinrt"></a>Создание пользовательских (на основе шаблона) элементов управления XAML с помощью C++/WinRT

> [!IMPORTANT]
> Основные понятия и термины, которые помогают понять, как использовать и создавать классы среды выполнения с помощью [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt), см. в разделах [Использование API-интерфейсов в C++/WinRT](consume-apis.md)и [Создание API-интерфейсов в C++/WinRT](author-apis.md).

Одним из наиболее мощных компонентов универсальной платформы Windows (UWP) является гибкость, которую обеспечивает стек пользовательского интерфейса (UI), чтобы создать пользовательские элементы управления в соответствии с типом [ **элемента управления**](/uwp/api/windows.ui.xaml.controls.control) XAML. Платформа пользовательского интерфейса XAML предоставляет функции, такие как [пользовательские свойства зависимостей](/windows/uwp/xaml-platform/custom-dependency-properties), [присоединенные свойства](/windows/uwp/xaml-platform/custom-attached-properties) и [шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates), которые упрощают создание многофункциональных и настраиваемых элементов управления. В этом разделе рассматриваются этапы создания пользовательского (шаблонного) элемента управления с помощью C++/WinRT.

## <a name="create-a-blank-app-bglabelcontrolapp"></a>Создайте пустое приложение (BgLabelControlApp)
Начните с создания проекта в Microsoft Visual Studio. Создайте проект **Пустое приложение (C++/WinRT)** и назовите его *BgLabelControlApp*. В следующем разделе этой статьи, вы будете перенаправлены, чтобы создать проект (не создавайте до тех пор).

> [!NOTE]
> Сведения об установке Visual Studio для разработки с использованием C++/WinRT, включая установку и использование расширения C++/WinRT для Visual Studio (VSIX) и пакета NuGet (которые вместе обеспечивают поддержку шаблона проекта и сборки), приведены в разделе [Поддержка Visual Studio для C++/WinRT, XAML, расширения VSIX и пакета NuGet](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-xaml-the-vsix-extension-and-the-nuget-package).

Мы собираемся создать новый класс, чтобы представить пользовательский (шаблонный) элемент управления. Класс создается и используется в рамках одной и той же единицы компиляции. Однако мы хотим иметь возможность создать этот класс из разметки XAML, поэтому это будет класс среды выполнения. Кроме того, мы применим C++/WinRT для его создания и использования.

Первый шаг при создании нового класса среды выполнения — добавление в проект нового элемента **Файл Midl (.idl)** . Назовите его `BgLabelControl.idl`. Удалите содержимое `BgLabelControl.idl` по умолчанию вставьте в это объявление класса среды выполнения.

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

Выше показан шаблон, которому необходимо следовать при объявлении свойства зависимостей (DP). Для каждой точки распространения существует два элемента. Сначала объявите нередактируемое статическое свойство типа [**DependencyProperty**](/uwp/api/windows.ui.xaml.dependencyproperty). Он имеет имя вашей DP плюс *свойство*. Это статическое свойство будет использоваться в реализации. Во-вторых, объявите свойство экземпляра, доступного для чтения и записи, с типом и именем вашей DP. Если вы хотите создать *присоединенное свойство* (а не DP), см. примеры кода в разделе [Настраиваемые присоединенные свойства](/windows/uwp/xaml-platform/custom-attached-properties).

> [!NOTE]
> Если вы хотите создать DP с типом с плавающей запятой, создайте ее `double` (`Double` в [MIDL 3.0](/uwp/midl-3/)). Объявление и реализация DP типа `float` (`Single` в MIDL), а затем установка значения для этой DP в разметке XAML, приведет к ошибке *Не удалось создать "Windows.Foundation.Single" из текста "<NUMBER>"* .

Сохраните файл и выполните сборку проекта. Во время сборки запускается инструмент `midl.exe` для создания файла метаданных среды выполнения Windows (`\BgLabelControlApp\Debug\BgLabelControlApp\Unmerged\BgLabelControl.winmd`), описывающего класс среды выполнения. Затем запускается средство `cppwinrt.exe` для создания файлов исходного кода для поддержки создания и использования вашего класса среды выполнения. Эти файлы включают заглушки для начала реализации класса среды выполнения **BgLabelControl**, объявленного в вашем IDL. Это заглушки `\BgLabelControlApp\BgLabelControlApp\Generated Files\sources\BgLabelControl.h` и `BgLabelControl.cpp`.

Скопируйте файлы заглушек `BgLabelControl.h` и `BgLabelControl.cpp` из `\BgLabelControlApp\BgLabelControlApp\Generated Files\sources\` в папку проекта `\BgLabelControlApp\BgLabelControlApp\`. В **Обозревателе решений**убедитесь, что функция **Показать все файлы** включена. Щелкните правой кнопкой мыши скопированные файлы заглушек и выберите **Включить в проект**.

## <a name="implement-the-bglabelcontrol-custom-control-class"></a>Реализуйте пользовательский класс управления **BgLabelControl**
Теперь давайте откроем `\BgLabelControlApp\BgLabelControlApp\BgLabelControl.h` и `BgLabelControl.cpp` и реализуем класс среды выполнения. В `BgLabelControl.h`, измените конструктор, чтобы задать стандартный ключ стилей, реализуйте **метку** и **LabelProperty**, добавьте обработчик статического события с именем **OnLabelChanged**, чтобы выполнить изменения в значении свойства зависимостей, и добавить закрытый элемент для хранения резервного поля для **LabelProperty**.

После их добавления ваш файл `BgLabelControl.h` будет выглядеть следующим образом.

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

В `BgLabelControl.cpp`, определите такие статические элементы.

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

В этом пошаговом руководстве мы не будем использовать **OnLabelChanged**. Но здесь все происходит таким образом, что вы можете увидеть, как зарегистрировать свойство зависимости с помощью обратного вызова с измененным свойством. Реализация **OnLabelChanged** также демонстрирует, как получить производный тип проекции от базового (в данном случае базовый тип проекции **DependencyObject**). В ней также показано, как потом получить указатель на тип, реализующий тип проекции. Вторая операция естественно может быть доступной только в проекте, который реализует тип проекции (то есть проект, реализующий класс среды выполнения).

> [!NOTE]
> Если вы еще не установили пакет Windows SDK версии 10.0.17763.0 (Windows 10, версия 1809) или более поздней версии, затем необходимо вызвать [**winrt::from_abi**](/uwp/cpp-ref-for-winrt/from-abi) в свойстве зависимости измененного обработчика события выше, вместо [ **winrt::get_ SELF**](/uwp/cpp-ref-for-winrt/get-self).

## <a name="design-the-default-style-for-bglabelcontrol"></a>Спроектируйте для **BgLabelControl** стиль по умолчанию

В своем конструкторе **BgLabelControl** задает собственный стандартный ключ стиля. Но что *является* стилем по умолчанию? Пользовательский (шаблонный) элемент управления должен иметь стиль по умолчанию&mdash;содержащий стандартный шаблон элемента управления&mdash;, который можно использовать для визуализации в случае, если потребитель элемента управления не устанавливает стиль и/или шаблон. В этом разделе мы добавим файл разметки в проект, содержащий наш стиль по умолчанию.

В узле проекта создайте папку (не фильтр, а папку) и назовите ее Themes. В разделе `Themes` добавьте новый элемент типа **Visual C++**  > **XAML** > **Представление XAML** и назовите его Generic.xaml. Имена папок и файлов должны быть такими, чтобы платформа XAML могла найти стиль по умолчанию для настраиваемого элемента управления. Удалите содержимое по умолчанию `Generic.xaml` и вставьте в разметке ниже.

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

В этом случае единственным свойством, которое задает стиль по умолчанию является шаблон элемента управления. Шаблон состоит из квадрата (фон которого привязан к свойству **Фон**, который имеет все экземпляры типа [ **элементов управления**](/uwp/api/windows.ui.xaml.controls.control) XAML) и текстового элемента (текст которого привязан к свойству зависимости **BgLabelControl::Label**).

## <a name="add-an-instance-of-bglabelcontrol-to-the-main-ui-page"></a>Добавьте экземпляр **BgLabelControl** на главную страницу пользовательского интерфейса

Откройте файл `MainPage.xaml`, который содержит разметку XAML для главной страницы пользовательского интерфейса. Сразу после элемента **Кнопка** (внутри **StackPanel**), добавьте следующую разметку.

```xaml
<local:BgLabelControl Background="Red" Label="Hello, World!"/>
```

Кроме того, добавьте следующую включенную директиву к `MainPage.h`, чтобы тип **MainPage** (комбинация компиляции разметки XAML и императивного кода) был проинформирован о типе пользовательского элемента управления **BgLabelControl**. Если вы хотите использовать **BgLabelControl** с другой страницы XAML, тогда также добавьте эту включенную директиву в файл заголовка для этой страницы. Или просто поместите одну включенную директиву в файл предкомпилированного заголовка.

```cppwinrt
// MainPage.h
...
#include "BgLabelControl.h"
...
```

Теперь выполните сборку и запустите проект. Вы увидите, что шаблон элемента управления по умолчанию привязан к кисти фона, а также метке экземпляра **BgLabelControl** в разметке.

В этом пошаговом руководстве показан простой пример настраиваемого (шаблонного) элемента управления в C++/WinRT. Вы можете сделать свои собственные элементы управления полнофункциональными и произвольно снабдить их широкими возможностями. Например пользовательский элемент управления может принимать форму чего-то сложного, как, скажем сетки редактируемых данных, проигрывателя видео или визуализатора трехмерной геометрии.

## <a name="implementing-overridable-functions-such-as-measureoverride-and-onapplytemplate"></a>Реализуя *переопределяемые* функции, такие как **MeasureOverride** и **OnApplyTemplate**

Вы получаете пользовательский элемент управления из класса среды выполнения [**элемента управления**](/uwp/api/windows.ui.xaml.controls.control), который в дальнейшем сам является производным от классов базовой среды выполнения. Здесь также есть переопределяемые методы **элемента управления**, [ **FrameworkElement**](/uwp/api/windows.ui.xaml.frameworkelement), и [ **UIElement** ](/uwp/api/windows.ui.xaml.uielement), которые можно переопределить в производном классе. Этот пример кода демонстрирует, как это сделать.

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

*Переопределяемые* функции в разных языковых проекциях представляются по-разному. В C#, например, переопределяемые функции обычно отображаются как защищенные виртуальные функции. В C++/WinRT они не являются ни виртуальными, ни защищенными, но их по-прежнему можно переопределить и предоставить собственную реализацию, как показано выше.

## <a name="important-apis"></a>Важные API
* [Класс элемента управления](/uwp/api/windows.ui.xaml.controls.control)
* [Класс DependencyProperty](/uwp/api/windows.ui.xaml.dependencyproperty)
* [Класс FrameworkElement](/uwp/api/windows.ui.xaml.frameworkelement)
* [Класс UIElement](/uwp/api/windows.ui.xaml.uielement)

## <a name="related-topics"></a>Статьи по теме
* [Шаблоны элементов управления](/windows/uwp/design/controls-and-patterns/control-templates)
* [Пользовательские свойства зависимостей](/windows/uwp/xaml-platform/custom-dependency-properties)
