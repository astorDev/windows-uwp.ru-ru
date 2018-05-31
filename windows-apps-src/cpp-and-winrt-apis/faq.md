---
author: stevewhims
description: Ответы на часто возникающие вопросы о разработке и использовании API среды выполнения Windows с помощью C++/WinRT.
title: Ответы на часто задаваемые вопросы о C++/WinRT
ms.author: stwhi
ms.date: 04/10/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Windows 10, uwp, стандартная, c ++, cpp, winrt, проекция, вопросы и ответы, чаво
ms.localizationpriority: medium
ms.openlocfilehash: aad5c5ed2123af39ebb6aff0c9098586ce958196
ms.sourcegitcommit: ab92c3e0dd294a36e7f65cf82522ec621699db87
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/03/2018
ms.locfileid: "1832039"
---
# <a name="frequently-asked-questions-about-cwinrtwindowsuwpcpp-and-winrt-apisintro-to-using-cpp-with-winrt"></a>Ответы на часто задаваемые вопросы о [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)
> [!NOTE]
> **Некоторые сведения относятся к предварительным версиям продуктов, в которые перед коммерческим выпуском могут быть внесены существенные изменения. Майкрософт не дает никаких гарантий, явных или подразумеваемых, в отношении предоставленной здесь информации.**

Ответы на часто возникающие вопросы о разработке и использовании API среды выполнения Windows с помощью C++/WinRT.

## <a name="what-are-the-requirements-for-the-cwinrt-visual-studio-extension-vsixhttpsakamscppwinrtvsix"></a>Каковы требования для [расширения Visual Studio (VSIX)](https://aka.ms/cppwinrt/vsix) C++/WinRT?
[VSIX](https://aka.ms/cppwinrt/vsix) требует минимальную целевую версию пакета Windows SDK 10.0.17134.0 (Windows 10, версия 1803). Также потребуется версии Visual Studio 2017 версии 15.6 или более поздней. Проект, использующий VSIX, можно определить по наличию `<CppWinRTEnabled>true</CppWinRTEnabled>`в `<PropertyGroup Label="Globals">` в файле `.vcxproj`. Дополнительные сведения см. в разделе [поддержка Visual Studio для C++/WinRT и VSIX ](intro-to-using-cpp-with-winrt.md#visual-studio-support-for-cwinrt-and-the-vsix).

## <a name="whats-a-runtime-class"></a>Что такое *класс среды выполнения*?
Класс среды выполнения — это тип, который можно активировать и использовать через современные интерфейсы COM, обычно через границы исполняемого файла. Тем не менее, класс среды выполнения может также использоваться в единице компиляции, которая его реализует. Класс среды выполнения объявляется в языке описания интерфейса (IDL) и может реализоваться в стандартной версии C++ с использованием C++/WinRT.

## <a name="what-do-the-projected-type-and-the-implementation-type-mean"></a>Что означают понятия *тип проекции* и *тип реализации*?
Если вы лишь *используете* класс среды выполнения Windows (класс среды выполнения), то вы имеете дело с *типом проекции*. C++/WinRT представляет собой *языковую проекцию*, поэтому типы проекции являются частью среды выполнения Windows, которая *проецируется* в C++ с помощью C++/WinRT. Дополнительные сведения см. в разделе [Использование API-интерфейсов в C++/WinRT](consume-apis.md).

*Тип реализации* содержит реализацию класса среды выполнения, поэтому он доступен только в проекте, который реализует класс среды выполнения. При работе в проекте, который реализует классы среды выполнения (проекте компонента среды выполнения Windows или проекте, использующем пользовательский интерфейс XAML), очень важно понимать различия между вашим типом реализации класса среды выполнения и типом проекции, представляющим класс среды выполнения, проецируемый в C++/WinRT. Дополнительные сведения см. в разделе [Создание API-интерфейсов в C++/WinRT](author-apis.md).

## <a name="should-i-implement-windowsfoundationiclosableuwpapiwindowsfoundationiclosable-and-if-so-how"></a>Следует ли реализовывать [**Windows::Foundation::IClosable**](/uwp/api/windows.foundation.iclosable) и если да, та каким образом?
Если у вас есть класс среды выполнения, который освобождает ресурсы в своем деструкторе, и этот класс среды предназначен для использования извне его единицы компиляции (это компонент среды выполнения Windows, предназначенный для общего использования клиентскими приложениями среды выполнения Windows), рекомендуется также реализовать **IClosable** для поддержки использования вашего класса среды выполнения языками, в которых отсутствует детерминированная финализация. Убедитесь, что ресурсы освобождаются при вызове деструктора, [**IClosable::Close**](/uwp/api/windows.foundation.iclosable.Close) или и того, и другого сразу. **IClosable::Close** можно вызывать произвольное число раз.

## <a name="do-i-need-to-call-iclosablecloseuwpapiwindowsfoundationiclosablewindowsfoundationiclosableclose-on-runtime-classes-that-i-consume"></a>Нужно ли вызывать [**IClosable::Close**](/uwp/api/windows.foundation.iclosable#Windows_Foundation_IClosable_Close_) для классов среды выполнения, которые я использую?
**IClosable** существует для поддержки языков, в которых отсутствует детерминированная финализация. Таким образом, не следует вызывать метод **IClosable::Close** из C++/WinRT, за исключением очень редких случаев полувзаимной блокировки. К примеру, если вы используете типы **Windows.UI.Composition**, может возникнуть ситуация, в которой вам понадобится удалить объекты в установленной последовательности, а не оставлять задачу их уничтожения программе-оболочке C++/WinRT.

## <a name="do-i-need-to-declare-a-constructor-in-my-runtime-classs-idl"></a>Нужно ли объявлять конструктор в IDL моего класса среды выполнения?
Только в том случае, если класс среды выполнения предназначен для использования извне его единицы компиляции (это компонент среды выполнения Windows, предназначенный для общего использования клиентскими приложениями среды выполнения Windows). Дополнительные сведения о назначении и последствиях объявления конструкторов в IDL см. в разделе [Конструкторы классов среды выполнения](author-apis.md#runtime-class-constructors).