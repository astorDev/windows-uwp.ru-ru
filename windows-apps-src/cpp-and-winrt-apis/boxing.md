---
description: Скалярное значение необходимо поместить в объект ссылочного класса перед его передачей в функцию, которая ожидает **IInspectable**. Этот процесс называют *упаковкой* значения.
title: Упаковка-преобразование и распаковка-преобразование скалярных значений в IInspectable с помощью C++/WinRT
ms.date: 04/10/2018
ms.topic: article
keywords: Windows 10, uwp, стандартная, c++, cpp, winrt, проекция, XAML, управление, упаковка, скалярное, значение
ms.localizationpriority: medium
ms.openlocfilehash: 5c86d1ac8ce83ea092ce0e2730ea0e9d4a201b94
ms.sourcegitcommit: 8921a9cc0dd3e5665345ae8eca7ab7aeb83ccc6f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "8887034"
---
# <a name="boxing-and-unboxing-scalar-values-to-iinspectable-with-cwinrt"></a>Упаковка-преобразование и распаковка-преобразование скалярных значений в IInspectable с помощью C++/WinRT
 
[**Интерфейс IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable) — это корневой интерфейс любого класса среды выполнения Windows (WinRT). Эта идея аналогична тому, что в корне каждого COM-интерфейса и класса находится [**IUnknown**](https://msdn.microsoft.com/library/windows/desktop/ms680509), а **System.Object** находится в корне каждого класса [Common Type System](https://docs.microsoft.com/dotnet/standard/base-types/common-type-system).

Другими словами, функции, которая ожидает **IInspectable**, можно передать экземпляр любого класса среды выполнения. Однако такой функции нельзя напрямую передать скалярное значение, например числовое или текстовое. Вместо этого скалярное значение необходимо поместить в объект ссылочного класса. Этот процесс называют *упаковкой* значения.

[C + +/ WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) предоставляет функции [**winrt::box_value**](/uwp/cpp-ref-for-winrt/box-value) , которая принимает скалярное значение и возвращает значение, упакованное в **IInspectable**. Для распаковки **IInspectable** обратно в скалярное значение существуют функции [**winrt::unbox_value**](/uwp/cpp-ref-for-winrt/unbox-value) и [**winrt::unbox_value_or**](/uwp/cpp-ref-for-winrt/unbox-value-or).

## <a name="examples-of-boxing-a-value"></a>Примеры упаковки значения
Функция доступа [**LaunchActivatedEventArgs::Arguments**](/uwp/api/windows.applicationmodel.activation.launchactivatedeventargs.Arguments) возвращает[**winrt::hstring**](/uwp/cpp-ref-for-winrt/hstring), которое является скалярным значением. Мы можете упаковать это значение **hstring** и передать его функции, ожидающей **IInspectable**, следующим образом.

```cppwinrt
void App::OnLaunched(LaunchActivatedEventArgs const& e)
{
    ...
    rootFrame.Navigate(winrt::xaml_typename<BlankApp1::MainPage>(), winrt::box_value(e.Arguments()));
    ...
}
```

Чтобы задать свойство содержимого [**кнопки**](/uwp/api/windows.ui.xaml.controls.button) XAML следует вызвать функцию-мутатор [**Button::Content**](/uwp/api/windows.ui.xaml.controls.contentcontrol.content?) . Чтобы присвоить строковое значение свойству содержимого, можно использовать этот код.

```cppwinrt
Button().Content(winrt::box_value(L"Clicked"));
```

Сначала конструктор преобразования [**hstring**](/uwp/cpp-ref-for-winrt/hstring) преобразует строковый литерал в **hstring**. Затем вызывается перегрузка **winrt::box_value**, которая принимает значение **hstring**.

## <a name="examples-of-unboxing-an-iinspectable"></a>Примеры распаковки IInspectable
В ваших собственных функциях, которые ожидают **IInspectable**, можно использовать для распаковки [**winrt::unbox_value**](/uwp/cpp-ref-for-winrt/unbox-value) и [**winrt::unbox_value_or**](/uwp/cpp-ref-for-winrt/unbox-value-or) для распаковки со значением по умолчанию.

```cppwinrt
void Unbox(winrt::Windows::Foundation::IInspectable const& object)
{
    hstring hstringValue = unbox_value<hstring>(object); // Throws if object is not a boxed string.
    hstringValue = unbox_value_or<hstring>(object, L"Default"); // Returns L"Default" if object is not a boxed string.
    float floatValue = unbox_value_or<float>(object, 0.f); // Returns 0.0 if object is not a boxed float.
}
```

## <a name="determine-the-type-of-a-boxed-value"></a>Определение типа упакованного значения
Если вы получили упакованное значение и не знаете, какого типа его содержимое (необходимо знать его тип, чтобы распаковать его), можно запросить у упакованного значения его интерфейс [**IPropertyValue**](/uwp/api/windows.foundation.ipropertyvalue), а затем вызвать **Type** для него. Приведем пример кода.

```cppwinrt
float pi = 3.14f;
auto piInspectable = winrt::box_value(pi);
auto piPropertyValue = piInspectable.as<winrt::Windows::Foundation::IPropertyValue>();
WINRT_ASSERT(piPropertyValue.Type() == winrt::Windows::Foundation::PropertyType::Single);
```

## <a name="important-apis"></a>Важные API
* [Интерфейс IInspectable](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)
* [Шаблон функции winrt::box_value](/uwp/cpp-ref-for-winrt/box-value)
* [Структура winrt::hstring](/uwp/cpp-ref-for-winrt/hstring)
* [Шаблон функции winrt::unbox_value](/uwp/cpp-ref-for-winrt/unbox-value)
* [Шаблон функции winrt::unbox_value_or](/uwp/cpp-ref-for-winrt/unbox-value-or)
