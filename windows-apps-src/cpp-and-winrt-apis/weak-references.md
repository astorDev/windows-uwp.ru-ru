---
author: stevewhims
description: Поддержка слабых ссылок в C++/WinRT не расходует ресурсов до тех пор, пока у вашего объекта не запрашивается IWeakReferenceSource.
title: Слабые ссылки в C++/WinRT
ms.author: stwhi
ms.date: 04/19/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, UWP, стандартные, c++, cpp, winrt, проекция, слабая, ссылка
ms.localizationpriority: medium
ms.openlocfilehash: 63ffad19c0ae8a52737ae13a54e5657df875d0b5
ms.sourcegitcommit: ab92c3e0dd294a36e7f65cf82522ec621699db87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "1832608"
---
# <a name="weak-references-in-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Слабые ссылки в [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
Проектирование API-интерфейсов C++/WinRT чаще всего можно произвести таким образом, чтобы избежать необходимости циклических и слабых ссылок. Тем не менее, когда речь идет о собственной реализации инфраструктуры пользовательского интерфейса на основе XAML &mdash; ввиду исторических особенностей проектирования инфраструктуры &mdash; механизма слабых ссылок в C++/WinRT необходим для обработки циклических ссылок. За пределами XAML вам, скорее всего, не потребуется использовать слабые ссылки (хотя в теории в них нет ничего, относящегося непосредственно к XAML).

Для конкретного декларируемого вами типа C++/WinRT на первый взгляд не очевидно, требуются ли слабые ссылки, и если да, то когда. Поэтому C++/ WinRT автоматически обеспечивает поддержку слабых ссылок в шаблоне структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements), от которого напрямую или косвенно наследуются ваши собственные типы C++/WinRT. Поддержка слабых ссылок не расходует ресурсов до тех пор, пока у вашего объекта не запрашивается [**IWeakReferenceSource**](https://msdn.microsoft.com/library/br224609). Кроме того, вы можете явным образом [отказаться от этой поддержки](#opting-out-of-weak-reference-support).

## <a name="code-examples"></a>Примеры кода
Шаблон структуры [**winrt::weak_ref**](/uwp/cpp-ref-for-winrt/weak-ref) является одним из вариантов получения слабой ссылки на экземпляр класса.

```cppwinrt
Class c;
winrt::weak_ref<Class> weak{ c };
```
Кроме того, можно использовать вспомогательную функцию [**winrt::make_weak**](/uwp/cpp-ref-for-winrt/make-weak).

```cppwinrt
Class c;
auto weak = winrt::make_weak(c);
```

Создание слабой ссылки не влияет на количество ссылок на сам объект, а просто приводит к выделению блока управления. Этот блок управления отвечает за реализацию семантики слабой ссылки. Затем вы можете попытаться превратить слабую ссылку в строгую и в случае успеха ее использовать.

```cppwinrt
if (Class strong = weak.get())
{
    // use strong, for example strong.DoWork();
}
```

При условии, что существуют некоторые другие строгие ссылки, вызов [**weak_ref::get**](/uwp/cpp-ref-for-winrt/weak-ref#weakrefget-function) увеличивает количество ссылок и возвращает строгую ссылку в вызывающий код.

## <a name="a-weak-reference-to-the-this-pointer"></a>Слабая ссылка на указатель *this*
Объект C++/WinRT прямо или косвенно является производным от шаблона структуры [**winrt::implements**](/uwp/cpp-ref-for-winrt/implements). Защищенная функция-член [**Implements::get_weak**](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function) возвращает слабую ссылку на указатель *this* объекта C++/WinRT. [**Implements.get_strong**](/uwp/cpp-ref-for-winrt/implements#implementsgetstrong-function) получает строгую ссылку.

## <a name="opting-out-of-weak-reference-support"></a>Отказ от поддержки слабых ссылок.
Поддержка слабых ссылок осуществляется автоматически. Однако вы можете явным образом отказаться от этой поддержки, передав структуру маркера [**winrt::no_weak_ref**](/uwp/cpp-ref-for-winrt/no-weak-ref) в качестве аргумента шаблона базовому классу.

При непосредственном наследовании из **winrt::implements**.

```cppwinrt
struct MyImplementation: implements<MyImplementation, IStringable, no_weak_ref>
{
    ...
}
```

При создании класса среды выполнения.

```cppwinrt
struct MyRuntimeClass: MyRuntimeClassT<MyRuntimeClass, no_weak_ref>
{
    ...
}
```

Не имеет значения, где в пакете параметров располагается структура маркера. При запросе слабой ссылки для типа с отключенной поддержкой компилятор отобразит сообщение "*This is only for weak ref support*" ("Только для поддержки слабых ссылок").

## <a name="important-apis"></a>Важные API
* [implements::get_weak function](/uwp/cpp-ref-for-winrt/implements#implementsgetweak-function)
* [Шаблон функции winrt::make_weak](/uwp/cpp-ref-for-winrt/make-weak)
* [Структура маркера winrt::no_weak_ref](/uwp/cpp-ref-for-winrt/no-weak-ref)
* [Шаблон структуры winrt::weak_ref](/uwp/cpp-ref-for-winrt/weak-ref)
